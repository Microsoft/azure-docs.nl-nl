---
title: Een punt-naar-site-VPN (P2S) op Linux configureren voor gebruik met Azure Files | Microsoft Docs
description: Een punt-naar-site-VPN (P2S) op Linux configureren voor gebruik met Azure Files
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 10/19/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 74422318718e318a00d7bd7ebaf8e4093ef75aa6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94629271"
---
# <a name="configure-a-point-to-site-p2s-vpn-on-linux-for-use-with-azure-files"></a>Een punt-naar-site-VPN (P2S) op Linux configureren voor gebruik met Azure Files
U kunt een punt-naar-site-VPN-verbinding (P2S) gebruiken om uw Azure-bestands shares te koppelen via SMB van buiten Azure, zonder dat u poort 445 hoeft te openen. Een punt-naar-site-VPN-verbinding is een VPN-verbinding tussen Azure en een afzonderlijke client. Als u een punt-naar-site-VPN-verbinding met Azure Files wilt gebruiken, moet er een punt-naar-site-VPN-verbinding worden geconfigureerd voor elke client die verbinding wil maken. Als u veel clients hebt die verbinding moeten maken met uw Azure-bestands shares van uw on-premises netwerk, kunt u een S2S-VPN-verbinding (site-naar-site) gebruiken in plaats van een punt-naar-site-verbinding voor elke client. Zie [een site-naar-site-VPN configureren voor gebruik met Azure files voor](storage-files-configure-s2s-vpn.md)meer informatie.

We raden u ten zeerste aan [Azure files netwerk overzicht](storage-files-networking-overview.md) te lezen voordat u verdergaat met dit artikel voor een volledige bespreking van de beschik bare netwerk opties voor Azure files.

In het artikel worden de stappen beschreven voor het configureren van een punt-naar-site-VPN op Linux om Azure-bestands shares rechtstreeks on-premises te koppelen. Als u Azure File Sync verkeer via een VPN wilt omleiden, raadpleegt u [Azure file sync proxy-en Firewall instellingen configureren](storage-sync-files-firewall-and-proxy.md).

## <a name="prerequisites"></a>Vereisten
- De meest recente versie van de Azure CLI. Zie voor meer informatie over het installeren van de Azure CLI [de Azure POWERSHELL cli installeren](/cli/azure/install-azure-cli) en selecteer uw besturings systeem. Als u liever de module Azure PowerShell gebruikt in Linux, kunt u de onderstaande instructies echter voor Azure CLI weer geven.

- Een Azure-bestands share die u on-premises wilt koppelen. Azure-bestands shares worden geïmplementeerd in opslag accounts. Dit zijn beheer constructies die een gedeelde opslag groep vertegenwoordigen, waarbij u meerdere bestands shares en andere opslag resources, zoals BLOB-containers of wacht rijen, kunt implementeren. Meer informatie over het implementeren van Azure-bestands shares en opslag accounts vindt u in [een Azure-bestands share maken](storage-how-to-create-file-share.md).

- Een persoonlijk eind punt voor het opslag account dat de Azure-bestands share bevat die u on-premises wilt koppelen. Zie [Azure files Network-eind punten configureren](storage-files-networking-endpoints.md?tabs=azure-cli)voor meer informatie over het maken van een persoonlijk eind punt. 

## <a name="install-required-software"></a>Vereiste software installeren
De virtuele Azure-netwerk gateway kan VPN-verbindingen bieden met behulp van verschillende VPN-protocollen, waaronder IPsec en OpenVPN. In deze hand leiding wordt uitgelegd hoe u IPsec gebruikt en hoe u het strongSwan-pakket gebruikt om ondersteuning te bieden voor Linux. 

> Geverifieerd met Ubuntu 18,10.

```bash
sudo apt install strongswan strongswan-pki libstrongswan-extra-plugins curl libxml2-utils cifs-utils

installDir="/etc/"
```

### <a name="deploy-a-virtual-network"></a>Een virtueel netwerk implementeren 
Als u toegang wilt krijgen tot uw Azure-bestands share en andere Azure-resources via een punt-naar-site-VPN, moet u een virtueel netwerk of VNet maken. De P2S VPN-verbinding die u automatisch maakt, is een brug tussen uw on-premises Linux-machine en dit virtuele Azure-netwerk.

Met het volgende script maakt u een virtueel Azure-netwerk met drie subnetten: één voor het service-eind punt van uw opslag account, een voor het privé-eind punt van uw opslag account, dat is vereist voor toegang tot het opslag account op locatie zonder aangepaste route ring te maken voor het open bare IP-adres van het opslag account dat kan worden gewijzigd, en één voor de virtuele netwerk gateway 

Vergeet niet om `<region>` , `<resource-group>` en `<desired-vnet-name>` met de juiste waarden voor uw omgeving te vervangen.

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
Als u wilt dat VPN-verbindingen van uw on-premises Linux-machines worden geverifieerd om toegang te krijgen tot uw virtuele netwerk, moet u twee certificaten maken: een basis certificaat dat wordt verschaft aan de gateway van de virtuele machine en een client certificaat, dat wordt ondertekend met het basis certificaat. Met het volgende script worden de vereiste certificaten gemaakt.

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

## <a name="deploy-virtual-network-gateway"></a>Virtuele netwerk gateway implementeren
De gateway van het virtuele Azure-netwerk is de service waarmee uw on-premises Linux-machines verbinding maken. Voor het implementeren van deze service zijn twee basis onderdelen vereist: een openbaar IP-adres waarmee de gateway naar uw clients wordt geïdentificeerd, waar deze zich ook in de wereld bevinden en een basis certificaat dat u eerder hebt gemaakt en dat wordt gebruikt om uw clients te verifiëren.

Vergeet niet door `<desired-vpn-name-here>` de gewenste naam voor deze resources te vervangen.

> [!Note]  
> Het implementeren van de virtuele Azure-netwerk gateway kan Maxi maal 45 minuten duren. Tijdens de implementatie van deze resource wordt door dit script script voor bash geblokkeerd, zodat deze kan worden voltooid.
>
> P2S IKEv2/OpenVPN-verbindingen worden niet ondersteund met de **basis** -SKU. Dit script maakt gebruik van de **VpnGw1** SKU voor de virtuele netwerk gateway.

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
De gateway van het virtuele netwerk van Azure maakt een downloadbaar pakket met configuratie bestanden die nodig zijn om de VPN-verbinding op uw on-premises Linux-computer te initialiseren. Het volgende script plaatst de certificaten die u in de juiste plaats hebt gemaakt en configureert het `ipsec.conf` bestand met de juiste waarden uit het configuratie bestand in het Download bare pakket.

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

## <a name="mount-azure-file-share"></a>Azure-bestands share koppelen
Nu u uw punt-naar-site-VPN hebt ingesteld, kunt u uw Azure-bestands share koppelen. In het volgende voor beeld wordt de share niet permanent gekoppeld. Zie [een Azure-bestands share gebruiken met Linux](storage-how-to-use-files-linux.md)voor meer informatie over het vastmaken van een permanente koppeling. 

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
- [Overzicht van Azure Files netwerken](storage-files-networking-overview.md)
- [Een punt-naar-site-VPN (P2S) in Windows configureren voor gebruik met Azure Files](storage-files-configure-p2s-vpn-windows.md)
- [Een site-naar-site-VPN (S2S) configureren voor gebruik met Azure Files](storage-files-configure-s2s-vpn.md)