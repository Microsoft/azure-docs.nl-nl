---
title: Een punt-naar-site-VPN (P2S) configureren in Linux voor gebruik met Azure Files | Microsoft Docs
description: Een punt-naar-site-VPN (P2S) configureren in Linux voor gebruik met Azure Files
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 10/19/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 9608e3bdaab033d58796a3841e8cd92d7a8a81ef
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107777973"
---
# <a name="configure-a-point-to-site-p2s-vpn-on-linux-for-use-with-azure-files"></a>Een punt-naar-site-VPN (P2S) configureren in Linux voor gebruik met Azure Files
U kunt een punt-naar-site-VPN-verbinding (P2S) gebruiken om uw Azure-bestands shares te verbinden via SMB van buiten Azure, zonder poort 445 te openen. Een punt-naar-site-VPN-verbinding is een VPN-verbinding tussen Azure en een afzonderlijke client. Als u een punt-naar-site-VPN-verbinding met Azure Files wilt gebruiken, moet er een punt-naar-site-VPN-verbinding worden geconfigureerd voor elke client die verbinding wil maken. Als u veel clients hebt die vanuit uw on-premises netwerk verbinding moeten maken met uw Azure-bestands shares, kunt u een site-naar-site-VPN-verbinding (S2S) gebruiken in plaats van een punt-naar-site-verbinding voor elke client. Zie Een [site-naar-site-VPN configureren](storage-files-configure-s2s-vpn.md)voor gebruik met Azure Files.

We raden u [](storage-files-networking-overview.md) ten zeerste aan Azure Files netwerkoverzicht te lezen voordat u verdergaat met dit artikel voor een volledige bespreking van de netwerkopties die beschikbaar zijn voor Azure Files.

In het artikel worden de stappen beschreven voor het configureren van een punt-naar-site-VPN in Linux om Azure-bestands shares rechtstreeks on-premises te kunnen mounten. Als u verkeer via een VPN wilt Azure File Sync, raadpleegt u [De proxy Azure File Sync en firewallinstellingen configureren.](../file-sync/file-sync-firewall-and-proxy.md)

## <a name="prerequisites"></a>Vereisten
- De meest recente versie van de Azure CLI. Zie Install the Azure PowerShell CLI (De cli installeren en uw besturingssysteem selecteren) voor meer informatie over het installeren van de Azure [CLI.](/cli/azure/install-azure-cli) Als u liever de module Azure PowerShell Linux gebruikt, kunt u dat wel doen, maar de onderstaande instructies worden weergegeven voor Azure CLI.

- Een Azure-bestands share die u on-premises wilt toevoegen. Azure-bestands shares worden geïmplementeerd in opslagaccounts. Dit zijn beheer constructies die een gedeelde opslaggroep vertegenwoordigen waarin u meerdere bestands shares kunt implementeren, evenals andere opslagbronnen, zoals blobcontainers of wachtrijen. Meer informatie over het implementeren van Azure-bestands shares en opslagaccounts vindt u in [Een Azure-bestands share maken.](storage-how-to-create-file-share.md)

- Een privé-eindpunt voor het opslagaccount met de Azure-bestands share die u on-premises wilt toevoegen. Zie Configuring Azure Files network endpoints (Netwerk Azure Files eindpunten configureren) voor meer informatie over het maken [van een privé-eindpunt.](storage-files-networking-endpoints.md?tabs=azure-cli) 

## <a name="install-required-software"></a>Vereiste software installeren
De gateway van het virtuele Azure-netwerk kan VPN-verbindingen bieden met behulp van verschillende VPN-protocollen, waaronder IPsec en OpenVPN. Deze handleiding laat zien hoe u IPsec gebruikt en het strongSwan-pakket gebruikt om ondersteuning te bieden in Linux. 

> Geverifieerd met Ubuntu 18.10.

```bash
sudo apt install strongswan strongswan-pki libstrongswan-extra-plugins curl libxml2-utils cifs-utils

installDir="/etc/"
```

### <a name="deploy-a-virtual-network"></a>Een virtueel netwerk implementeren 
Als u on-premises toegang wilt krijgen tot uw Azure-bestands share en andere Azure-resources via een punt-naar-site-VPN, moet u een virtueel netwerk of VNet maken. De P2S VPN-verbinding die u automatisch maakt, is een brug tussen uw on-premises Linux-machine en dit virtuele Azure-netwerk.

Met het volgende script maakt u een virtueel Azure-netwerk met drie subnetten: één voor het service-eindpunt van uw opslagaccount, één voor het privé-eindpunt van uw opslagaccount, dat vereist is voor toegang tot het on-premises opslagaccount zonder aangepaste routering te maken voor het openbare IP-adres van het opslagaccount dat kan worden gewijzigd, en een voor uw virtuele netwerkgateway die de VPN-service levert. 

Vergeet niet om `<region>` , en te vervangen door de juiste waarden voor uw `<resource-group>` `<desired-vnet-name>` omgeving.

```bash
region="<region>"
resourceGroupName="<resource-group>"
virtualNetworkName="<desired-vnet-name>"

virtualNetwork=$(az network vnet create \
    --resource-group $resourceGroupName \
    --name $virtualNetworkName \
    --location $region \
    --address-prefixes "192.168.0.0/16" \
    --query "newVNet.id" | tr -d '"')

serviceEndpointSubnet=$(az network vnet subnet create \
    --resource-group $resourceGroupName \
    --vnet-name $virtualNetworkName \
    --name "ServiceEndpointSubnet" \
    --address-prefixes "192.168.0.0/24" \
    --service-endpoints "Microsoft.Storage" \
    --query "id" | tr -d '"')

privateEndpointSubnet=$(az network vnet subnet create \
    --resource-group $resourceGroupName \
    --vnet-name $virtualNetworkName \
    --name "PrivateEndpointSubnet" \
    --address-prefixes "192.168.1.0/24" \
    --query "id" | tr -d '"')

gatewaySubnet=$(az network vnet subnet create \
    --resource-group $resourceGroupName \
    --vnet-name $virtualNetworkName \
    --name "GatewaySubnet" \
    --address-prefixes "192.168.2.0/24" \
    --query "id" | tr -d '"')
```

## <a name="create-certificates-for-vpn-authentication"></a>Certificaten voor VPN-verificatie maken
Als u wilt dat VPN-verbindingen van uw on-premises Linux-machines worden geverifieerd voor toegang tot uw virtuele netwerk, moet u twee certificaten maken: een basiscertificaat dat wordt verstrekt aan de gateway van de virtuele machine en een clientcertificaat dat wordt ondertekend met het basiscertificaat. Met het volgende script worden de vereiste certificaten gemaakt.

```bash
rootCertName="P2SRootCert"
username="client"
password="1234"

mkdir temp
cd temp

sudo ipsec pki --gen --outform pem > rootKey.pem
sudo ipsec pki --self --in rootKey.pem --dn "CN=$rootCertName" --ca --outform pem > rootCert.pem

rootCertificate=$(openssl x509 -in rootCert.pem -outform der | base64 -w0 ; echo)

sudo ipsec pki --gen --size 4096 --outform pem > "clientKey.pem"
sudo ipsec pki --pub --in "clientKey.pem" | \
    sudo ipsec pki \
        --issue \
        --cacert rootCert.pem \
        --cakey rootKey.pem \
        --dn "CN=$username" \
        --san $username \
        --flag clientAuth \
        --outform pem > "clientCert.pem"

openssl pkcs12 -in "clientCert.pem" -inkey "clientKey.pem" -certfile rootCert.pem -export -out "client.p12" -password "pass:$password"
```

## <a name="deploy-virtual-network-gateway"></a>Virtuele netwerkgateway implementeren
De gateway van het virtuele Azure-netwerk is de service waar uw on-premises Linux-machines verbinding mee maken. Voor het implementeren van deze service zijn twee basisonderdelen vereist: een openbaar IP-adres waarmee de gateway naar uw clients wordt identificeert waar ter wereld ze zich ook voordeden en een basiscertificaat dat u eerder hebt gemaakt en dat wordt gebruikt om uw clients te verifiëren.

Vergeet niet om te `<desired-vpn-name-here>` vervangen door de naam die u voor deze resources wilt gebruiken.

> [!Note]  
> Het implementeren van de virtuele Azure-netwerkgateway kan tot 45 minuten duren. Terwijl deze resource wordt geïmplementeerd, wordt dit bash-script geblokkeerd om de implementatie te kunnen uitvoeren.
>
> P2S IKEv2/OpenVPN-verbindingen worden niet  ondersteund met de Basic-SKU. Dit script maakt dienovereenkomstig **gebruik van de VpnGw1-SKU** voor de gateway van het virtuele netwerk.

```bash
vpnName="<desired-vpn-name-here>"
publicIpAddressName="$vpnName-PublicIP"

publicIpAddress=$(az network public-ip create \
    --resource-group $resourceGroupName \
    --name $publicIpAddressName \
    --location $region \
    --sku "Basic" \
    --allocation-method "Dynamic" \
    --query "publicIp.id" | tr -d '"')

az network vnet-gateway create \
    --resource-group $resourceGroupName \
    --name $vpnName \
    --vnet $virtualNetworkName \
    --public-ip-addresses $publicIpAddress \
    --location $region \
    --sku "VpnGw1" \
    --gateway-typ "Vpn" \
    --vpn-type "RouteBased" \
    --address-prefixes "172.16.201.0/24" \
    --client-protocol "IkeV2" > /dev/null

az network vnet-gateway root-cert create \
    --resource-group $resourceGroupName \
    --gateway-name $vpnName \
    --name $rootCertName \
    --public-cert-data $rootCertificate \
    --output none
```

## <a name="configure-the-vpn-client"></a>De VPN-client configureren
De gateway van het virtuele Azure-netwerk maakt een downloadbaar pakket met configuratiebestanden die vereist zijn voor het initialiseren van de VPN-verbinding op uw on-premises Linux-machine. Met het volgende script worden de certificaten die u hebt gemaakt, op de juiste locatie opgeslagen en wordt het bestand geconfigureerd met de juiste waarden uit het `ipsec.conf` configuratiebestand in het downloadbare pakket.

```bash
vpnClient=$(az network vnet-gateway vpn-client generate \
    --resource-group $resourceGroupName \
    --name $vpnName \
    --authentication-method EAPTLS | tr -d '"')

curl $vpnClient --output vpnClient.zip
unzip vpnClient.zip

vpnServer=$(xmllint --xpath "string(/VpnProfile/VpnServer)" Generic/VpnSettings.xml)
vpnType=$(xmllint --xpath "string(/VpnProfile/VpnType)" Generic/VpnSettings.xml | tr '[:upper:]' '[:lower:]')
routes=$(xmllint --xpath "string(/VpnProfile/Routes)" Generic/VpnSettings.xml)

sudo cp "${installDir}ipsec.conf" "${installDir}ipsec.conf.backup"
sudo cp "Generic/VpnServerRoot.cer" "${installDir}ipsec.d/cacerts"
sudo cp "${username}.p12" "${installDir}ipsec.d/private" 

echo -e "\nconn $virtualNetworkName" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tkeyexchange=$vpnType" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\ttype=tunnel" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tleftfirewall=yes" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tleft=%any" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tleftauth=eap-tls" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tleftid=%client" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tright=$vpnServer" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\trightid=%$vpnServer" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\trightsubnet=$routes" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tleftsourceip=%config" | sudo tee -a "${installDir}ipsec.conf" > /dev/null 
echo -e "\tauto=add" | sudo tee -a "${installDir}ipsec.conf" > /dev/null

echo ": P12 client.p12 '$password'" | sudo tee -a "${installDir}ipsec.secrets" > /dev/null

sudo ipsec restart
sudo ipsec up $virtualNetworkName 
```

## <a name="mount-azure-file-share"></a>Een Azure-bestands share toevoegen
Nu u uw punt-naar-site-VPN hebt ingesteld, kunt u uw Azure-bestands delen. In het volgende voorbeeld wordt de share niet permanent bevestigd. Zie Een Azure-bestands share gebruiken met Linux als u deze permanent [wilt toevoegen.](storage-how-to-use-files-linux.md) 

```bash
fileShareName="myshare"

mntPath="/mnt/$storageAccountName/$fileShareName"
sudo mkdir -p $mntPath

storageAccountKey=$(az storage account keys list \
    --resource-group $resourceGroupName \
    --account-name $storageAccountName \
    --query "[0].value" | tr -d '"')

smbPath="//$storageAccountPrivateIP/$fileShareName"
sudo mount -t cifs $smbPath $mntPath -o vers=3.0,username=$storageAccountName,password=$storageAccountKey,serverino
```

## <a name="see-also"></a>Zie ook
- [Azure Files netwerkoverzicht](storage-files-networking-overview.md)
- [Een punt-naar-site-VPN (P2S) configureren in Windows voor gebruik met Azure Files](storage-files-configure-p2s-vpn-windows.md)
- [Een site-naar-site-VPN (S2S) configureren voor gebruik met Azure Files](storage-files-configure-s2s-vpn.md)