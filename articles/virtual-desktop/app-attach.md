---
title: PowerShell-scripts Windows Virtual Desktop MSIX-app koppelen - Azure
description: PowerShell-scripts voor MSIX-app koppelen voor Windows Virtual Desktop.
author: Heidilohr
ms.topic: how-to
ms.date: 04/13/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 43a8cb00804927784982999db13ee193c34f55ca
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107835376"
---
# <a name="create-powershell-scripts-for-msix-app-attach"></a>PowerShell-scripts voor MSIX-app koppelen

In dit onderwerp wordt u door het instellen van PowerShell-scripts voor MSIX-app-attach doorgenomen.

## <a name="install-certificates"></a>Certificaten installeren

U moet certificaten installeren op alle sessiehosts in de hostgroep die als host dienen voor de apps van uw MSIX-app-koppelpakketten.

Als uw app gebruikmaakt van een certificaat dat niet openbaar wordt vertrouwd of zelf is ondertekend, kunt u het als volgende installeren:

1. Klik met de rechtermuisknop op het pakket en selecteer **Eigenschappen.**
2. Selecteer in het venster dat wordt weergegeven het **tabblad Digitale** handtekeningen. Er mag slechts één item in de lijst op het tabblad staan. Selecteer dat item om het item te markeren en selecteer vervolgens **Details**.
3. Wanneer het venster met details van de digitale handtekening wordt weergegeven, selecteert u het tabblad **Algemeen** en selecteert u **vervolgens Certificaat weergeven** en **selecteert u vervolgens Certificaat installeren.**
4. Wanneer het installatieprogramma wordt geopend, **selecteert u lokale computer** als uw opslaglocatie en selecteert u **vervolgens Volgende.**
5. Als het installatieprogramma u vraagt of u wilt toestaan dat de app wijzigingen aan uw apparaat aan kan brengen, selecteert u **Ja.**
6. Selecteer **Alle certificaten in het volgende winkel plaatsen en** selecteer vervolgens **Bladeren.**
7. Wanneer het venster Certificaatopslag selecteren wordt weergegeven, selecteert u **Vertrouwde personen** en selecteert u **vervolgens OK.**
8. Selecteer **Volgende** en **Voltooien.**

## <a name="enable-microsoft-hyper-v"></a>Schakel Microsoft Hyper-V

Microsoft Hyper-V moet worden ingeschakeld omdat de opdracht nodig is om te worden gefaseerd `Mount-VHD` en nodig is om de fase te `Dismount-VHD` destagen.

```powershell
Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Hyper-V -All
```

>[!NOTE]
>Voor deze wijziging moet u de virtuele machine opnieuw opstarten.

## <a name="prepare-powershell-scripts-for-msix-app-attach"></a>PowerShell-scripts voorbereiden voor msix-app-attach

MsIX-app-attach bestaat uit vier verschillende fasen die in de volgende volgorde moeten worden uitgevoerd:

1. Fase
2. Registreren
3. Registratie ongedaan maken
4. Faseer de fase

In elke fase wordt een PowerShell-script gemaakt. Voorbeeldscripts voor elke fase zijn hier [beschikbaar.](https://github.com/Azure/RDS-Templates/tree/master/msix-app-attach)

### <a name="stage-powershell-script"></a>PowerShell-script faseer

Voordat u de PowerShell-scripts bij te werken, moet u ervoor zorgen dat u de volume-GUID van het volume in de VHD hebt. De volume-GUID op te halen:

1.  Open de netwerk share waar de VHD zich bevindt in de VM waar u het script gaat uitvoeren.

2.  Klik met de rechtermuisknop op de VHD en selecteer **Mount**. Hiermee wordt de VHD aan een stationletter bevestigd.

3.  Nadat u de VHD hebt bevestigd, wordt **het venster Bestandenverkenner** geopend. De bovenliggende map vastleggen en de variabele **$parentFolder** bijwerken

    >[!NOTE]
    >Als u geen bovenliggende map ziet, betekent dit dat de MSIX niet goed is uitvoed. Doe het vorige gedeelte opnieuw en probeer het opnieuw.

4.  Open de bovenliggende map. Als het pakket correct is uit vouwd, ziet u een map met dezelfde naam als het pakket. Werk de **variabele $packageName** bij om overeen te komen met de naam van deze map.

    Bijvoorbeeld `VSCodeUserSetup-x64-1.38.1_1.38.1.0_x64__8wekyb3d8bbwe`.

5.  Open een opdrachtprompt en voer **mountvol in.** Met deze opdracht wordt een lijst met volumes en hun GUID's weergegeven. Kopieer de GUID van het volume waar de stationletter overeenkomt met het station waarop u de VHD in stap 2 hebt bevestigd.

    In dit voorbeeld van de uitvoer voor de mountvol opdracht, als u uw VHD aan station C hebt bevestigd, moet u de bovenstaande waarde `C:\` kopiëren:

    ```cmd
    Possible values for VolumeName along with current mount points are:

    \\?\Volume{a12b3456-0000-0000-0000-10000000000}\
    *** NO MOUNT POINTS ***

    \\?\Volume{c78d9012-0000-0000-0000-20000000000}\
        E:\

    \\?\Volume{d34e5678-0000-0000-0000-30000000000}\
        C:\

    ```


6.  Werk de **$volumeGuid** bij met de volume-GUID die u zojuist hebt gekopieerd.

7. Open een PowerShell-prompt voor beheerders en werk het volgende PowerShell-script bij met de variabelen die van toepassing zijn op uw omgeving.

    ```powershell
    #MSIX app attach staging sample

    #region variables
    $vhdSrc="<path to vhd>"
    $packageName = "<package name>"
    $parentFolder = "<package parent folder>"
    $parentFolder = "\" + $parentFolder + "\"
    $volumeGuid = "<vol guid>"
    $msixJunction = "C:\temp\AppAttach\"
    #endregion

    #region mountvhd
    try
    {
          Mount-Diskimage -ImagePath $vhdSrc -NoDriveLetter -Access ReadOnly
          Write-Host ("Mounting of " + $vhdSrc + " was completed!") -BackgroundColor Green
    }
    catch
    {
          Write-Host ("Mounting of " + $vhdSrc + " has failed!") -BackgroundColor Red
    }
    #endregion

    #region makelink
    $msixDest = "\\?\Volume{" + $volumeGuid + "}\"
    if (!(Test-Path $msixJunction))
    {
         md $msixJunction
    }

    $msixJunction = $msixJunction + $packageName
    cmd.exe /c mklink /j $msixJunction $msixDest
    #endregion

    #region stage
    [Windows.Management.Deployment.PackageManager,Windows.Management.Deployment,ContentType=WindowsRuntime] | Out-Null
    Add-Type -AssemblyName System.Runtime.WindowsRuntime
    $asTask = ([System.WindowsRuntimeSystemExtensions].GetMethods() | Where { $_.ToString() -eq 'System.Threading.Tasks.Task`1[TResult] AsTask[TResult,TProgress](Windows.Foundation.IAsyncOperationWithProgress`2[TResult,TProgress])'})[0]
    $asTaskAsyncOperation = $asTask.MakeGenericMethod([Windows.Management.Deployment.DeploymentResult], [Windows.Management.Deployment.DeploymentProgress])
    $packageManager = [Windows.Management.Deployment.PackageManager]::new()
    $path = $msixJunction + $parentFolder + $packageName # needed if we do the pbisigned.vhd
    $path = ([System.Uri]$path).AbsoluteUri
    $asyncOperation = $packageManager.StagePackageAsync($path, $null, "StageInPlace")
    $task = $asTaskAsyncOperation.Invoke($null, @($asyncOperation))
    $task
    #endregion
    ```

### <a name="register-powershell-script"></a>PowerShell-script registreren

Als u het registerscript wilt uitvoeren, moet u de volgende PowerShell-cmdlets uitvoeren met de tijdelijke aanduidingen vervangen door waarden die van toepassing zijn op uw omgeving.

```powershell
#MSIX app attach registration sample

#region variables
$packageName = "<package name>"
$path = "C:\Program Files\WindowsApps\" + $packageName + "\AppxManifest.xml"
#endregion

#region register
Add-AppxPackage -Path $path -DisableDevelopmentMode -Register
#endregion
```

### <a name="deregister-powershell-script"></a>De registratie van PowerShell-script ongedaan maken

Vervang voor dit script de tijdelijke aanduiding voor **$packageName** door de naam van het pakket dat u test.

```powershell
#MSIX app attach deregistration sample

#region variables
$packageName = "<package name>"
#endregion

#region deregister
Remove-AppxPackage -PreserveRoamableApplicationData $packageName
#endregion
```

### <a name="destage-powershell-script"></a>PowerShell-script destagen

Vervang voor dit script de tijdelijke aanduiding voor **$packageName** door de naam van het pakket dat u test. In een productie-implementatie kunt u dit het beste uitvoeren bij afsluiten.

```powershell
#MSIX app attach de staging sample

$vhdSrc="<path to vhd>"

#region variables
$packageName = "<package name>"
$msixJunction = "C:\temp\AppAttach"
#endregion

#region deregister
Remove-AppxPackage -AllUsers -Package $packageName
Remove-Item "$msixJunction\$packageName" -Recurse -Force -Verbose
#endregion

#region Detach VHD
Dismount-DiskImage -ImagePath $vhdSrc -Confirm:$false
#endregion
```

## <a name="set-up-simulation-scripts-for-the-msix-app-attach-agent"></a>Simulatiescripts instellen voor de MSIX-app-attach-agent

Nadat u de scripts hebt maken, kunnen gebruikers ze handmatig uitvoeren of instellen dat ze automatisch worden uitgevoerd als opstart-, aanmeldings-, aanmeldings- en afsluitscripts. Zie Voor meer informatie over deze typen scripts opstart-, [afsluit-, aanmeldings-](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn789196(v=ws.11)/)en aanmeldingsscripts gebruiken in groepsbeleid.

Elk van deze automatische scripts voert één fase van de app-koppelingsscripts uit:

- Het opstartscript voert het fasescript uit.
- Met het aanmeldingsscript wordt het registerscript uitgevoerd.
- Met het afmeldingsscript wordt het script voor het ongedaan maken van de registratie uitgevoerd.
- Met het afsluitscript wordt het script gedefaseeerd uitgevoerd.

## <a name="use-packages-offline"></a>Pakketten offline gebruiken

Als u pakketten gebruikt van de [Microsoft Store voor Bedrijven of](https://businessstore.microsoft.com/) de [Microsoft Store voor Onderwijs](https://educationstore.microsoft.com/) in uw netwerk of op apparaten die niet zijn verbonden met internet, moet u de pakketlicenties van de Microsoft Store downloaden en installeren op uw apparaat om de app met succes uit te voeren. Als uw apparaat online is en verbinding kan maken met de Microsoft Store voor Bedrijven, moeten de vereiste licenties automatisch worden gedownload. Als u offline bent, moet u de licenties handmatig instellen.

Als u de licentiebestanden wilt installeren, moet u een PowerShell-script gebruiken dat de klasse MDM_EnterpriseModernAppManagement_StoreLicenses02_01 aanroept in de WMI Bridge-provider.

U kunt als volgende licenties instellen voor offlinegebruik:

1. Download het app-pakket, de licenties en de vereiste frameworks van de Microsoft Store voor Bedrijven. U hebt zowel de gecodeerde als niet-gecodeerde licentiebestanden nodig. Gedetailleerde downloadinstructies vindt u [hier.](/microsoft-store/distribute-offline-apps#download-an-offline-licensed-app)
2. Werk de volgende variabelen in het script bij voor stap 3:
      1. `$contentID` is de ContentID-waarde uit het niet-gecodeerde licentiebestand (.xml). U kunt het licentiebestand openen in een teksteditor van uw keuze.
      2. `$licenseBlob` is de volledige tekenreeks voor de licentieblob in het gecodeerde licentiebestand (.bin). U kunt het gecodeerde licentiebestand openen in een teksteditor van uw keuze.
3. Voer het volgende script uit vanaf een PowerShell-prompt voor beheerders. Een goede plaats voor het installeren van [](#stage-powershell-script) licenties is aan het einde van het faseringsscript dat ook moet worden uitgevoerd vanaf een beheerdersprompt.

```powershell
$namespaceName = "root\cimv2\mdm\dmmap"
$className = "MDM_EnterpriseModernAppManagement_StoreLicenses02_01"
$methodName = "AddLicenseMethod"
$parentID = "./Vendor/MSFT/EnterpriseModernAppManagement/AppLicenses/StoreLicenses"

#TODO - Update $contentID with the ContentID value from the unencoded license file (.xml)
$contentID = "{'ContentID'_in_unencoded_license_file}"

#TODO - Update $licenseBlob with the entire String in the encoded license file (.bin)
$licenseBlob = "{Entire_String_in_encoded_license_file}"

$session = New-CimSession

#The final string passed into the AddLicenseMethod should be of the form <License Content="encoded license blob" />
$licenseString = '<License Content='+ '"' + $licenseBlob +'"' + ' />'

$params = New-Object Microsoft.Management.Infrastructure.CimMethodParametersCollection
$param = [Microsoft.Management.Infrastructure.CimMethodParameter]::Create("param",$licenseString ,"String", "In")
$params.Add($param)


try
{
   $instance = New-CimInstance -Namespace $namespaceName -ClassName $className -Property @{ParentID=$parentID;InstanceID=$contentID}
   $session.InvokeMethod($namespaceName, $instance, $methodName, $params)

}
catch [Exception]
{
     write-host $_ | out-string
}
```

## <a name="next-steps"></a>Volgende stappen

Deze functie wordt momenteel niet ondersteund, maar u kunt vragen stellen aan de community op [de Windows Virtual Desktop TechCommunity.](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)

U kunt ook feedback geven voor Windows Virtual Desktop in Windows Virtual Desktop [feedbackhub.](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app)
