---
title: Een punt-naar-site-VPN (P2S) in Windows configureren voor gebruik met Azure Files | Microsoft Docs
description: Een punt-naar-site-VPN (P2S) configureren in Windows voor gebruik met Azure Files
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 10/19/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: de342267292c6a93c4a1ba2eae232403ccaf9514
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107785267"
---
# <a name="configure-a-point-to-site-p2s-vpn-on-windows-for-use-with-azure-files"></a>Een punt-naar-site-VPN (P2S) configureren in Windows voor gebruik met Azure Files
U kunt een punt-naar-site-VPN-verbinding (P2S) gebruiken om uw Azure-bestands shares via SMB van buiten Azure te verbinden, zonder poort 445 te openen. Een punt-naar-site-VPN-verbinding is een VPN-verbinding tussen Azure en een afzonderlijke client. Als u een punt-naar-site-VPN-verbinding met Azure Files wilt gebruiken, moet er een punt-naar-site-VPN-verbinding worden geconfigureerd voor elke client die verbinding wil maken. Als u veel clients hebt die verbinding moeten maken met uw Azure-bestands shares vanuit uw on-premises netwerk, kunt u een site-naar-site-VPN-verbinding (S2S) gebruiken in plaats van een punt-naar-site-verbinding voor elke client. Zie Een [site-naar-site-VPN configureren](storage-files-configure-s2s-vpn.md)voor gebruik met Azure Files voor meer Azure Files.

We raden u ten zeerste aan netwerkoverwegingen te lezen voor rechtstreekse toegang tot [Azure-bestands](storage-files-networking-overview.md) delen voordat u verdergaat met dit artikel voor een volledige bespreking van de netwerkopties die beschikbaar zijn voor Azure Files.

In dit artikel worden de stappen beschreven voor het configureren van een punt-naar-site-VPN in Windows (Windows-client en Windows Server) om Azure-bestands shares rechtstreeks on-premises te mounten. Zie Configuring Azure File Sync proxy and firewall settings (Proxy- en firewallinstellingen configureren) als u verkeer Azure File Sync via een VPN [wilt omgeleid.](../file-sync/file-sync-firewall-and-proxy.md)

## <a name="prerequisites"></a>Vereisten
- De meest recente versie van de Azure PowerShell module. Zie Install [the Azure PowerShell module](/powershell/azure/install-az-ps) (De module Azure PowerShell installeren) en selecteer uw besturingssysteem voor meer informatie over het installeren van de Azure PowerShell. Als u liever de Azure CLI in Windows gebruikt, kunt u dit doen, maar de onderstaande instructies worden weergegeven voor Azure PowerShell.

- Een Azure-bestands share die u on-premises wilt toevoegen. Azure-bestands shares worden geïmplementeerd in opslagaccounts. Dit zijn beheersin constructies die een gedeelde opslaggroep vertegenwoordigen waarin u meerdere bestands shares kunt implementeren, evenals andere opslagbronnen, zoals blobcontainers of wachtrijen. Meer informatie over het implementeren van Azure-bestands shares en opslagaccounts vindt u in [Een Azure-bestands share maken.](storage-how-to-create-file-share.md)

- Een privé-eindpunt voor het opslagaccount met de Azure-bestands share die u on-premises wilt toevoegen. Zie Configuring Azure Files network endpoints (Netwerk Azure Files eindpunten configureren) voor meer informatie over het maken van een [privé-eindpunt.](storage-files-networking-endpoints.md?tabs=azure-powershell) 

## <a name="deploy-a-virtual-network"></a>Een virtueel netwerk implementeren
Als u on-premises toegang wilt krijgen tot uw Azure-bestands share en andere Azure-resources via een punt-naar-site-VPN, moet u een virtueel netwerk of VNet maken. De P2S VPN-verbinding die u automatisch maakt, is een brug tussen uw on-premises Windows-computer en dit virtuele Azure-netwerk.

Met de volgende PowerShell maakt u een virtueel Azure-netwerk met drie subnetten: één voor het service-eindpunt van uw opslagaccount, één voor het privé-eindpunt van uw opslagaccount. Dit is vereist voor toegang tot het on-premises opslagaccount zonder aangepaste routering te maken voor het openbare IP-adres van het opslagaccount dat kan worden gewijzigd, en een voor uw virtuele netwerkgateway die de VPN-service levert. 

Vergeet niet om `<region>` , en te vervangen door de juiste waarden voor uw `<resource-group>` `<desired-vnet-name>` omgeving.

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

## <a name="create-root-certificate-for-vpn-authentication"></a>Basiscertificaat voor VPN-verificatie maken
Als u wilt dat VPN-verbindingen van uw on-premises Windows-machines worden geverifieerd voor toegang tot uw virtuele netwerk, moet u twee certificaten maken: een basiscertificaat dat wordt verstrekt aan de gateway van de virtuele machine en een clientcertificaat dat wordt ondertekend met het basiscertificaat. Met de volgende PowerShell wordt het basiscertificaat gemaakt; het clientcertificaat wordt gemaakt nadat de gateway van het virtuele Azure-netwerk is gemaakt met informatie van de gateway. 

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

## <a name="deploy-virtual-network-gateway"></a>Virtuele netwerkgateway implementeren
De gateway van het virtuele Azure-netwerk is de service die uw on-premises Windows-machines verbinden. Voor het implementeren van deze service zijn twee basisonderdelen vereist: een openbaar IP-adres waarmee de gateway voor uw clients wordt identificeert, waar ter wereld ze zich ook in de wereld, en een basiscertificaat dat u eerder hebt gemaakt, dat wordt gebruikt om uw clients te verifiëren.

Vergeet niet om te `<desired-vpn-name-here>` vervangen door de naam die u voor deze resources wilt gebruiken.

> [!Note]  
> Het implementeren van de virtuele Azure-netwerkgateway kan tot 45 minuten duren. Terwijl deze resource wordt geïmplementeerd, wordt dit PowerShell-script geblokkeerd om de implementatie te kunnen uitvoeren. Dit is normaal.

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

## <a name="create-client-certificate"></a>Clientcertificaat maken
Het clientcertificaat wordt gemaakt met de URI van de gateway van het virtuele netwerk. Dit certificaat is ondertekend met het basiscertificaat dat u eerder hebt gemaakt.

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
De gateway van het virtuele Azure-netwerk maakt een downloadbaar pakket met configuratiebestanden die vereist zijn voor het initialiseren van de VPN-verbinding op uw on-premises Windows-computer. We configureren de VPN-verbinding met behulp van de [functie Always On VPN](/windows-server/remote/remote-access/vpn/always-on-vpn/) van Windows 10/Windows Server 2016+. Dit pakket bevat ook uitvoerbare pakketten waarmee de verouderde Windows VPN-client wordt geconfigureerd, indien gewenst. In deze handleiding wordt Always On VPN gebruikt in plaats van de verouderde Windows VPN-client, omdat eindgebruikers met de Always On VPN-client verbinding kunnen maken met/de verbinding met Azure VPN kunnen verbreken zonder beheerdersmachtigingen voor hun computer. 

Met het volgende script installeert u het clientcertificaat dat is vereist voor verificatie bij de gateway van het virtuele netwerk, en downloadt en installeert u het VPN-pakket. Vergeet niet om `<computer1>` en te vervangen door de `<computer2>` gewenste computers. U kunt dit script op zoveel machines uitvoeren als u wilt door meer PowerShell-sessies aan de matrix toe te `$sessions` voegen. Uw gebruiksaccount moet een beheerder zijn op elk van deze computers. Als een van deze computers de lokale computer is waar u het script vandaan gebruikt, moet u het script uitvoeren vanuit een PowerShell-sessie met verhoogde bevoegdheid. 

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

## <a name="mount-azure-file-share"></a>Een Azure-bestands share toevoegen
Nu u uw punt-naar-site-VPN hebt ingesteld, kunt u deze gebruiken om de Azure-bestands share te installeren op de computers die u hebt ingesteld via PowerShell. In het volgende voorbeeld wordt de share aan de share toegevoegd, wordt de hoofdmap van de share weergegeven om te bewijzen dat de share daadwerkelijk is bevestigd en wordt de share ontkoppeld. Het is helaas niet mogelijk om de share permanent te mounten via remoting van PowerShell. Zie Een Azure-bestands share gebruiken met Windows als u deze permanent [wilt toevoegen.](storage-how-to-use-files-windows.md) 

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
- [Netwerkoverwegingen voor directe toegang tot Azure-bestands delen](storage-files-networking-overview.md)
- [Een punt-naar-site-VPN (P2S) configureren in Linux voor gebruik met Azure Files](storage-files-configure-p2s-vpn-linux.md)
- [Een site-naar-site-VPN (S2S) configureren voor gebruik met Azure Files](storage-files-configure-s2s-vpn.md)