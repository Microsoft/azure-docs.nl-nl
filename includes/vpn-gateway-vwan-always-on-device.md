---
title: bestand opnemen
description: bestand opnemen
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/12/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 34e841a5f17d589c4fbef54a4a8674a99ac6c640
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96027371"
---
Aan de volgende vereisten moet worden voldaan om een apparaat tunnel te kunnen instellen:

* Het apparaat moet een computer zijn die lid is van een domein en waarop Windows 10 Enter prise of onderwijs versie 1809 of hoger wordt uitgevoerd.
* De tunnel kan alleen worden geconfigureerd voor de Windows ingebouwde VPN-oplossing en wordt tot stand gebracht met behulp van IKEv2 met authenticatie van computer certificaten.
* Er kan slechts één tunnel van een apparaat per apparaat worden geconfigureerd.

1. Client certificaten installeren op de Windows 10-client met behulp van het artikel [punt-naar-site VPN-client](../articles/vpn-gateway/point-to-site-how-to-vpn-client-install-azure-cert.md) . Het certificaat moet zich in het archief van de lokale computer bestaan.
1. Maak een VPN-profiel en configureer de tunnel van het apparaat in de context van het lokale systeem account met behulp van [deze instructies](/windows-server/remote/remote-access/vpn/vpn-device-tunnel-config#vpn-device-tunnel-configuration).

### <a name="configuration-example-for-device-tunnel"></a>Configuratie voorbeeld voor de tunnel van apparaten

Nadat u de virtuele netwerk gateway hebt geconfigureerd en het client certificaat in het archief van de lokale computer op de Windows 10-client hebt geïnstalleerd, gebruikt u de volgende voor beelden om een tunnel voor client apparaten te configureren:

1. Kopieer de volgende tekst en sla deze op als ***devicecert.ps1***.

   ```
   Param(
   [string]$xmlFilePath,
   [string]$ProfileName
   )

   $a = Test-Path $xmlFilePath
   echo $a

   $ProfileXML = Get-Content $xmlFilePath

   echo $XML

   $ProfileNameEscaped = $ProfileName -replace ' ', '%20'

   $Version = 201606090004

   $ProfileXML = $ProfileXML -replace '<', '&lt;'
   $ProfileXML = $ProfileXML -replace '>', '&gt;'
   $ProfileXML = $ProfileXML -replace '"', '&quot;'

   $nodeCSPURI = './Vendor/MSFT/VPNv2'
   $namespaceName = "root\cimv2\mdm\dmmap"
   $className = "MDM_VPNv2_01"

   $session = New-CimSession

   try
   {
   $newInstance = New-Object Microsoft.Management.Infrastructure.CimInstance $className, $namespaceName
   $property = [Microsoft.Management.Infrastructure.CimProperty]::Create("ParentID", "$nodeCSPURI", 'String', 'Key')
   $newInstance.CimInstanceProperties.Add($property)
   $property = [Microsoft.Management.Infrastructure.CimProperty]::Create("InstanceID", "$ProfileNameEscaped", 'String', 'Key')
   $newInstance.CimInstanceProperties.Add($property)
   $property = [Microsoft.Management.Infrastructure.CimProperty]::Create("ProfileXML", "$ProfileXML", 'String', 'Property')
   $newInstance.CimInstanceProperties.Add($property)

   $session.CreateInstance($namespaceName, $newInstance)
   $Message = "Created $ProfileName profile."
   Write-Host "$Message"
   }
   catch [Exception]
   {
   $Message = "Unable to create $ProfileName profile: $_"
   Write-Host "$Message"
   exit
   }
   $Message = "Complete."
   Write-Host "$Message"
   ```
1. Kopieer de volgende tekst en sla deze op als ***VPNProfile.xml** _ in dezelfde map als _ * devicecert.ps1 * *. Bewerk de volgende tekst zodat deze overeenkomt met uw omgeving.

   * `<Servers>azuregateway-1234-56-78dc.cloudapp.net</Servers> <= Can be found in the VpnSettings.xml in the downloaded profile zip file`
   * `<Address>192.168.3.5</Address> <= IP of resource in the vnet or the vnet address space`
   * `<Address>192.168.3.4</Address> <= IP of resource in the vnet or the vnet address space`

   ```
   <VPNProfile>  
     <NativeProfile>  
   <Servers>azuregateway-1234-56-78dc.cloudapp.net</Servers>  
   <NativeProtocolType>IKEv2</NativeProtocolType>  
   <Authentication>  
     <MachineMethod>Certificate</MachineMethod>  
   </Authentication>  
   <RoutingPolicyType>SplitTunnel</RoutingPolicyType>  
    <!-- disable the addition of a class based route for the assigned IP address on the VPN interface -->
   <DisableClassBasedDefaultRoute>true</DisableClassBasedDefaultRoute>  
     </NativeProfile> 
     <!-- use host routes(/32) to prevent routing conflicts -->  
     <Route>  
   <Address>192.168.3.5</Address>  
   <PrefixSize>32</PrefixSize>  
     </Route>  
     <Route>  
   <Address>192.168.3.4</Address>  
   <PrefixSize>32</PrefixSize>  
     </Route>  
   <!-- need to specify always on = true --> 
     <AlwaysOn>true</AlwaysOn> 
   <!-- new node to specify that this is a device tunnel -->  
    <DeviceTunnel>true</DeviceTunnel>
   <!--new node to register client IP address in DNS to enable manage out -->
   <RegisterDNS>true</RegisterDNS>
   </VPNProfile>
   ```
1. Down load **PsExec** van [Sysinternals](/sysinternals/downloads/psexec) en pak de bestanden uit naar **C:\PSTools**.
1. Start Power shell op basis van een opdracht prompt van de beheerder door de volgende opdracht uit te voeren:

   ```
   PsExec.exe Powershell for 32-bit Windows
   PsExec64.exe Powershell for 64-bit Windows
   ```

   ![Scherm afbeelding toont een opdracht prompt venster met een opdracht om de 64-bits versie van Power shell te starten.](./media/vpn-gateway-vwan-always-on-device/powershell.png)
1. Ga in Power shell naar de map waarin **devicecert.ps1** en **VPNProfile.xml** zich bevinden en voer de volgende opdracht uit:

   ```powershell
   .\devicecert.ps1 .\VPNProfile.xml MachineCertTest
   ```
   
   ![Scherm afbeelding toont een Power shell-venster met MachineCertTest met behulp van het script devicesert.](./media/vpn-gateway-vwan-always-on-device/machinecerttest.png)
1. Voer **Rasphone** uit.

   ![Scherm afbeelding toont het dialoog venster uitvoeren met Rasphone geselecteerd.](./media/vpn-gateway-vwan-always-on-device/rasphone.png)
1. Zoek de vermelding **MachineCertTest** en klik op **verbinden**.

   ![Scherm afbeelding toont het dialoog venster netwerk verbindingen met MachineCertTest geselecteerd en een knop verbinding maken.](./media/vpn-gateway-vwan-always-on-device/connect.png)
1. Als de verbinding is geslaagd, start u de computer opnieuw op. De tunnel wordt automatisch verbonden.