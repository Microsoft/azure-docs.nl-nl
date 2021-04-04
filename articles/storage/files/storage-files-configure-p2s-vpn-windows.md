---
title: Een punt-naar-site-VPN (P2S) in Windows configureren voor gebruik met Azure Files | Microsoft Docs
description: Een punt-naar-site-VPN (P2S) in Windows configureren voor gebruik met Azure Files
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 10/19/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 6253deb53229172cd499a6aa14b8d8f19bc07b63
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94629254"
---
# <a name="configure-a-point-to-site-p2s-vpn-on-windows-for-use-with-azure-files"></a>Een punt-naar-site-VPN (P2S) in Windows configureren voor gebruik met Azure Files
U kunt een punt-naar-site-VPN-verbinding (P2S) gebruiken om uw Azure-bestands shares te koppelen via SMB van buiten Azure, zonder dat u poort 445 hoeft te openen. Een punt-naar-site-VPN-verbinding is een VPN-verbinding tussen Azure en een afzonderlijke client. Als u een punt-naar-site-VPN-verbinding met Azure Files wilt gebruiken, moet er een punt-naar-site-VPN-verbinding worden geconfigureerd voor elke client die verbinding wil maken. Als u veel clients hebt die verbinding moeten maken met uw Azure-bestands shares van uw on-premises netwerk, kunt u een S2S-VPN-verbinding (site-naar-site) gebruiken in plaats van een punt-naar-site-verbinding voor elke client. Zie [een site-naar-site-VPN configureren voor gebruik met Azure files voor](storage-files-configure-s2s-vpn.md)meer informatie.

We raden u ten zeerste aan om [netwerk overwegingen te lezen voor directe toegang tot de Azure-bestands share](storage-files-networking-overview.md) voordat u doorgaat met deze procedure voor een volledige bespreking van de beschik bare netwerk opties voor Azure files.

In het artikel worden de stappen beschreven voor het configureren van een punt-naar-site-VPN in Windows (Windows-client en Windows Server) om Azure-bestands shares rechtstreeks on-premises te koppelen. Als u Azure File Sync verkeer via een VPN wilt omleiden, raadpleegt u [Azure file sync proxy-en Firewall instellingen configureren](storage-sync-files-firewall-and-proxy.md).

## <a name="prerequisites"></a>Vereisten
- De meest recente versie van de module Azure PowerShell. Voor meer informatie over het installeren van de Azure PowerShell, raadpleegt u [de module Azure PowerShell installeren](/powershell/azure/install-az-ps) en selecteert u uw besturings systeem. Als u liever de Azure CLI in Windows gebruikt, kunt u de onderstaande instructies echter weer geven voor Azure PowerShell.

- Een Azure-bestands share die u on-premises wilt koppelen. Azure-bestands shares worden geïmplementeerd in opslag accounts. Dit zijn beheer constructies die een gedeelde opslag groep vertegenwoordigen, waarbij u meerdere bestands shares en andere opslag resources, zoals BLOB-containers of wacht rijen, kunt implementeren. Meer informatie over het implementeren van Azure-bestands shares en opslag accounts vindt u in [een Azure-bestands share maken](storage-how-to-create-file-share.md).

- Een persoonlijk eind punt voor het opslag account dat de Azure-bestands share bevat die u on-premises wilt koppelen. Zie [Azure files Network-eind punten configureren](storage-files-networking-endpoints.md?tabs=azure-powershell)voor meer informatie over het maken van een persoonlijk eind punt. 

## <a name="deploy-a-virtual-network"></a>Een virtueel netwerk implementeren
Als u toegang wilt krijgen tot uw Azure-bestands share en andere Azure-resources via een punt-naar-site-VPN, moet u een virtueel netwerk of VNet maken. De P2S VPN-verbinding die u automatisch maakt, is een brug tussen uw on-premises Windows-computer en dit virtuele Azure-netwerk.

Met de volgende Power Shell maakt u een virtueel Azure-netwerk met drie subnetten: één voor het service-eind punt van uw opslag account, een voor het privé-eind punt van uw opslag account, dat is vereist voor toegang tot het opslag account op locatie zonder aangepaste route ring te maken voor het open bare IP-adres van het opslag account dat kan worden gewijzigd, en één voor de virtuele netwerk 

Vergeet niet om `<region>` , `<resource-group>` en `<desired-vnet-name>` met de juiste waarden voor uw omgeving te vervangen.

```PowerShell
$region = "<region>"
$resourceGroupName = "<resource-group>" 
$virtualNetworkName = "<desired-vnet-name>"

$virtualNetwork = New-AzVirtualNetwork `
    -ResourceGroupName $resourceGroupName `
    -Name $virtualNetworkName `
    -Location $region `
    -AddressPrefix "192.168.0.0/16"

Add-AzVirtualNetworkSubnetConfig `
    -Name "ServiceEndpointSubnet" `
    -AddressPrefix "192.168.0.0/24" `
    -VirtualNetwork $virtualNetwork `
    -ServiceEndpoint "Microsoft.Storage" `
    -WarningAction SilentlyContinue | Out-Null

Add-AzVirtualNetworkSubnetConfig `
    -Name "PrivateEndpointSubnet" `
    -AddressPrefix "192.168.1.0/24" `
    -VirtualNetwork $virtualNetwork `
    -WarningAction SilentlyContinue | Out-Null

Add-AzVirtualNetworkSubnetConfig `
    -Name "GatewaySubnet" `
    -AddressPrefix "192.168.2.0/24" `
    -VirtualNetwork $virtualNetwork `
    -WarningAction SilentlyContinue | Out-Null

$virtualNetwork | Set-AzVirtualNetwork | Out-Null
$virtualNetwork = Get-AzVirtualNetwork `
    -ResourceGroupName $resourceGroupName `
    -Name $virtualNetworkName

$serviceEndpointSubnet = $virtualNetwork.Subnets | `
    Where-Object { $_.Name -eq "ServiceEndpointSubnet" }
$privateEndpointSubnet = $virtualNetwork.Subnets | `
    Where-Object { $_.Name -eq "PrivateEndpointSubnet" }
$gatewaySubnet = $virtualNetwork.Subnets | ` 
    Where-Object { $_.Name -eq "GatewaySubnet" }
```

## <a name="create-root-certificate-for-vpn-authentication"></a>Basis certificaat voor VPN-verificatie maken
Als u wilt dat VPN-verbindingen van uw on-premises Windows-computers worden geverifieerd voor toegang tot uw virtuele netwerk, moet u twee certificaten maken: een basis certificaat, dat wordt verschaft aan de gateway van de virtuele machine en een client certificaat, dat wordt ondertekend met het basis certificaat. Met de volgende Power shell wordt het basis certificaat gemaakt. het client certificaat wordt gemaakt nadat de gateway van het virtuele Azure-netwerk is gemaakt met informatie van de gateway. 

```PowerShell
$rootcertname = "CN=P2SRootCert"
$certLocation = "Cert:\CurrentUser\My"
$vpnTemp = "C:\vpn-temp\"
$exportedencodedrootcertpath = $vpnTemp + "P2SRootCertencoded.cer"
$exportedrootcertpath = $vpnTemp + "P2SRootCert.cer"

if (-Not (Test-Path $vpnTemp)) {
    New-Item -ItemType Directory -Force -Path $vpnTemp | Out-Null
}

if ($PSVersionTable.PSVersion -ge [System.Version]::new(6, 0)) {
    Install-Module WindowsCompatibility
    Import-WinModule PKI
}

$rootcert = New-SelfSignedCertificate `
    -Type Custom `
    -KeySpec Signature `
    -Subject $rootcertname `
    -KeyExportPolicy Exportable `
    -HashAlgorithm sha256 `
    -KeyLength 2048 `
    -CertStoreLocation $certLocation `
    -KeyUsageProperty Sign `
    -KeyUsage CertSign

Export-Certificate `
    -Cert $rootcert `
    -FilePath $exportedencodedrootcertpath `
    -NoClobber | Out-Null

certutil -encode $exportedencodedrootcertpath $exportedrootcertpath | Out-Null

$rawRootCertificate = Get-Content -Path $exportedrootcertpath

[System.String]$rootCertificate = ""
foreach($line in $rawRootCertificate) { 
    if ($line -notlike "*Certificate*") { 
        $rootCertificate += $line 
    } 
}
```

## <a name="deploy-virtual-network-gateway"></a>Virtuele netwerk gateway implementeren
De gateway van het virtuele Azure-netwerk is de service die door uw on-premises Windows-computers wordt verbonden. Voor het implementeren van deze service zijn twee basis onderdelen vereist: een openbaar IP-adres waarmee de gateway naar uw clients wordt geïdentificeerd, waar deze zich ook in de wereld bevinden en een basis certificaat dat u eerder hebt gemaakt, dat wordt gebruikt om uw clients te verifiëren.

Vergeet niet door `<desired-vpn-name-here>` de gewenste naam voor deze resources te vervangen.

> [!Note]  
> Het implementeren van de virtuele Azure-netwerk gateway kan Maxi maal 45 minuten duren. Terwijl deze resource wordt geïmplementeerd, wordt door dit Power shell-script geblokkeerd om de implementatie te volt ooien. Dit is normaal.

```PowerShell
$vpnName = "<desired-vpn-name-here>" 
$publicIpAddressName = "$vpnName-PublicIP"

$publicIPAddress = New-AzPublicIpAddress `
    -ResourceGroupName $resourceGroupName `
    -Name $publicIpAddressName `
    -Location $region `
    -Sku Basic `
    -AllocationMethod Dynamic

$gatewayIpConfig = New-AzVirtualNetworkGatewayIpConfig `
    -Name "vnetGatewayConfig" `
    -SubnetId $gatewaySubnet.Id `
    -PublicIpAddressId $publicIPAddress.Id

$azRootCertificate = New-AzVpnClientRootCertificate `
    -Name "P2SRootCert" `
    -PublicCertData $rootCertificate

$vpn = New-AzVirtualNetworkGateway `
    -ResourceGroupName $resourceGroupName `
    -Name $vpnName `
    -Location $region `
    -GatewaySku VpnGw1 `
    -GatewayType Vpn `
    -VpnType RouteBased `
    -IpConfigurations $gatewayIpConfig `
    -VpnClientAddressPool "172.16.201.0/24" `
    -VpnClientProtocol IkeV2 `
    -VpnClientRootCertificates $azRootCertificate
```

## <a name="create-client-certificate"></a>Client certificaat maken
Het client certificaat wordt gemaakt met de URI van de virtuele netwerk gateway. Dit certificaat is ondertekend met het basis certificaat dat u eerder hebt gemaakt.

```PowerShell
$clientcertpassword = "1234"

$vpnClientConfiguration = New-AzVpnClientConfiguration `
    -ResourceGroupName $resourceGroupName `
    -Name $vpnName `
    -AuthenticationMethod EAPTLS

Invoke-WebRequest `
    -Uri $vpnClientConfiguration.VpnProfileSASUrl `
    -OutFile "$vpnTemp\vpnclientconfiguration.zip"

Expand-Archive `
    -Path "$vpnTemp\vpnclientconfiguration.zip" `
    -DestinationPath "$vpnTemp\vpnclientconfiguration"

$vpnGeneric = "$vpnTemp\vpnclientconfiguration\Generic"
$vpnProfile = ([xml](Get-Content -Path "$vpnGeneric\VpnSettings.xml")).VpnProfile

$exportedclientcertpath = $vpnTemp + "P2SClientCert.pfx"
$clientcertname = "CN=" + $vpnProfile.VpnServer

$clientcert = New-SelfSignedCertificate `
    -Type Custom `
    -DnsName $vpnProfile.VpnServer `
    -KeySpec Signature `
    -Subject $clientcertname `
    -KeyExportPolicy Exportable `
    -HashAlgorithm sha256 `
    -KeyLength 2048 `
    -CertStoreLocation $certLocation `
    -Signer $rootcert `
    -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2")

$mypwd = ConvertTo-SecureString -String $clientcertpassword -Force -AsPlainText

Export-PfxCertificate `
    -FilePath $exportedclientcertpath `
    -Password $mypwd `
    -Cert $clientcert | Out-Null
```

## <a name="configure-the-vpn-client"></a>De VPN-client configureren
De gateway van het virtuele netwerk van Azure maakt een downloadbaar pakket met configuratie bestanden die nodig zijn om de VPN-verbinding op uw lokale Windows-computer te initialiseren. De VPN-verbinding wordt geconfigureerd met de functie [altijd op VPN](/windows-server/remote/remote-access/vpn/always-on-vpn/) van Windows 10/Windows Server 2016 +. Dit pakket bevat ook uitvoer bare pakketten die de verouderde Windows VPN-client configureren, als dat nodig is. In deze hand leiding wordt altijd op VPN gebruikt in plaats van de verouderde Windows VPN-client als de always on VPN-client toestaan dat eind gebruikers verbinding maken met of de verbinding verbreken met de Azure VPN zonder beheerders machtigingen voor hun computer. 

Met het volgende script wordt het client certificaat geïnstalleerd dat vereist is voor verificatie van de virtuele netwerk gateway, het downloaden en installeren van het VPN-pakket. Vergeet niet om `<computer1>` `<computer2>` de gewenste computers te vervangen. U kunt dit script uitvoeren op net zoveel computers als gewenst door meer Power shell-sessies toe te voegen aan de `$sessions` matrix. Het account dat u gebruikt, moet een beheerder zijn op elk van deze machines. Als een van deze computers de lokale computer is waarop u het script uitvoert, moet u het script uitvoeren vanuit een Power shell-sessie met verhoogde bevoegdheden. 

```PowerShell
$sessions = [System.Management.Automation.Runspaces.PSSession[]]@()
$sessions += New-PSSession -ComputerName "<computer1>"
$sessions += New-PSSession -ComputerName "<computer2>"

foreach ($session in $sessions) {
    Invoke-Command -Session $session -ArgumentList $vpnTemp -ScriptBlock { 
        $vpnTemp = $args[0]
        if (-Not (Test-Path $vpnTemp)) {
            New-Item `
                -ItemType Directory `
                -Force `
                -Path "C:\vpn-temp" | Out-Null
        }
    }

    Copy-Item `
        -Path $exportedclientcertpath, $exportedrootcertpath, "$vpnTemp\vpnclientconfiguration.zip" `
        -Destination $vpnTemp `
        -ToSession $session

    Invoke-Command `
        -Session $session `
        -ArgumentList `
            $mypwd, `
            $vpnTemp, `
            $virtualNetworkName `
        -ScriptBlock { 
            $mypwd = $args[0] 
            $vpnTemp = $args[1]
            $virtualNetworkName = $args[2]

            Import-PfxCertificate `
                -Exportable `
                -Password $mypwd `
                -CertStoreLocation "Cert:\LocalMachine\My" `
                -FilePath "$vpnTemp\P2SClientCert.pfx" | Out-Null

            Import-Certificate `
                -FilePath "$vpnTemp\P2SRootCert.cer" `
                -CertStoreLocation "Cert:\LocalMachine\Root" | Out-Null

            Expand-Archive `
                -Path "$vpnTemp\vpnclientconfiguration.zip" `
                -DestinationPath "$vpnTemp\vpnclientconfiguration"
            $vpnGeneric = "$vpnTemp\vpnclientconfiguration\Generic"

            $vpnProfile = ([xml](Get-Content -Path "$vpnGeneric\VpnSettings.xml")).VpnProfile

            Add-VpnConnection `
                -Name $virtualNetworkName `
                -ServerAddress $vpnProfile.VpnServer `
                -TunnelType Ikev2 `
                -EncryptionLevel Required `
                -AuthenticationMethod MachineCertificate `
                -SplitTunneling `
                -AllUserConnection

            Add-VpnConnectionRoute `
                -Name $virtualNetworkName `
                -DestinationPrefix $vpnProfile.Routes `
                -AllUserConnection

            Add-VpnConnectionRoute `
                -Name $virtualNetworkName `
                -DestinationPrefix $vpnProfile.VpnClientAddressPool `
                -AllUserConnection

            rasdial $virtualNetworkName
        }
}

Remove-Item -Path $vpnTemp -Recurse
```

## <a name="mount-azure-file-share"></a>Azure-bestands share koppelen
Nu u uw punt-naar-site-VPN hebt ingesteld, kunt u het gebruiken om de Azure-bestands share te koppelen op de computers die u installeert via Power shell. In het volgende voor beeld wordt de share gekoppeld, wordt de hoofdmap van de share vermeld om te bewijzen dat de share daad werkelijk is gekoppeld en wordt de share ontkoppeld. Helaas is het niet mogelijk om de share permanent te koppelen via externe communicatie met Power shell. Zie [een Azure-bestands share gebruiken met Windows](storage-how-to-use-files-windows.md)om permanent te koppelen. 

```PowerShell
$myShareToMount = "<file-share>"

$storageAccountKeys = Get-AzStorageAccountKey `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName
$storageAccountKey = ConvertTo-SecureString `
    -String $storageAccountKeys[0].Value `
    -AsPlainText `
    -Force

$nic = Get-AzNetworkInterface -ResourceId $privateEndpoint.NetworkInterfaces[0].Id
$storageAccountPrivateIP = $nic.IpConfigurations[0].PrivateIpAddress

Invoke-Command `
    -Session $sessions `
    -ArgumentList  `
        $storageAccountName, `
        $storageAccountKey, `
        $storageAccountPrivateIP, `
        $myShareToMount `
    -ScriptBlock {
        $storageAccountName = $args[0]
        $storageAccountKey = $args[1]
        $storageAccountPrivateIP = $args[2]
        $myShareToMount = $args[3]

        $credential = [System.Management.Automation.PSCredential]::new(
            "AZURE\$storageAccountName", 
            $storageAccountKey)

        New-PSDrive `
            -Name Z `
            -PSProvider FileSystem `
            -Root "\\$storageAccountPrivateIP\$myShareToMount" `
            -Credential $credential `
            -Persist | Out-Null
        Get-ChildItem -Path Z:\
        Remove-PSDrive -Name Z
    }
```

## <a name="see-also"></a>Zie ook
- [Netwerk overwegingen voor directe toegang tot Azure-bestands shares](storage-files-networking-overview.md)
- [Een punt-naar-site-VPN (P2S) op Linux configureren voor gebruik met Azure Files](storage-files-configure-p2s-vpn-linux.md)
- [Een site-naar-site-VPN (S2S) configureren voor gebruik met Azure Files](storage-files-configure-s2s-vpn.md)