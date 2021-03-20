---
title: Windows virtueel bureau blad-MSIX-app voor het koppelen van Power shell-scripts configureren-Azure
description: Power shell-scripts maken voor MSIX app attach voor Windows virtueel bureau blad.
author: Heidilohr
ms.topic: how-to
ms.date: 12/14/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 5e45c51735e0b7ab4b263d3f3047b5848c82439d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98185764"
---
# <a name="create-powershell-scripts-for-msix-app-attach-preview"></a>Power shell-scripts voor MSIX app attach maken (preview)

> [!IMPORTANT]
> MSIX app attach is momenteel beschikbaar als open bare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

In dit onderwerp vindt u instructies voor het instellen van Power shell-scripts voor het toevoegen van MSIX-apps.

>[!IMPORTANT]
>Voordat u aan de slag gaat, moet u ervoor zorgen dat [dit formulier](https://aka.ms/enablemsixappattach) wordt ingevuld en verzonden om de MSIX-app-koppeling in uw abonnement in te scha kelen. Als u geen goedgekeurde aanvraag hebt, werkt MSIX app attach niet. De goed keuring van aanvragen kan tot 24 uur duren tijdens kantoor dagen. U ontvangt een e-mail wanneer uw aanvraag is geaccepteerd en voltooid.

## <a name="install-certificates"></a>Certificaten installeren

U moet certificaten installeren op alle sessie-hosts in de hostgroep die de APS host van uw MSIX-app voor het koppelen van pakketten.

Als uw app gebruikmaakt van een certificaat dat niet openbaar of zelfondertekend is, kunt u dit als volgt installeren:

1. Klik met de rechter muisknop op het pakket en selecteer **Eigenschappen**.
2. In het venster dat wordt weer gegeven, selecteert u het tabblad **digitale hand tekeningen** . Er mag slechts één item in de lijst staan op het tabblad, zoals wordt weer gegeven in de volgende afbeelding. Selecteer het item dat u wilt markeren en selecteer vervolgens **Details**.
3. Wanneer het venster Details van digitale hand tekening wordt weer gegeven, selecteert u het tabblad **Algemeen** en selecteert u **certificaat weer geven** en selecteert u **certificaat installeren**.
4. Wanneer het installatie programma wordt geopend, selecteert u **lokale computer** als uw opslag locatie en selecteert u vervolgens **volgende**.
5. Als het installatie programma u vraagt of u wilt toestaan dat de app wijzigingen in uw apparaat aanbrengt, selecteert u **Ja**.
6. Selecteer **alle certificaten in het onderstaande archief opslaan** en selecteer vervolgens **Bladeren**.
7. Wanneer het venster certificaat archief selecteren wordt weer gegeven, selecteert u **vertrouwde personen** en selecteert u **OK**.
8. Selecteer **volgende** en **volt ooien**.

## <a name="enable-microsoft-hyper-v"></a>Microsoft Hyper-V inschakelen

Microsoft Hyper-V moet zijn ingeschakeld omdat de `Mount-VHD` opdracht voor het stadium is vereist en moet worden `Dismount-VHD` gestage.

```powershell
Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Hyper-V -All
```

>[!NOTE]
>Bij deze wijziging moet u de virtuele machine opnieuw opstarten.

## <a name="prepare-powershell-scripts-for-msix-app-attach"></a>Power shell-scripts voorbereiden voor MSIX-app attach

Het koppelen van de MSIX-app heeft vier afzonderlijke fasen die in de volgende volg orde moeten worden uitgevoerd:

1. Fase
2. Registreren
3. Registratie
4. Destage

Elke fase maakt een Power shell-script. Voorbeeld scripts voor elke fase zijn [hier](https://github.com/Azure/RDS-Templates/tree/master/msix-app-attach)beschikbaar.

### <a name="stage-powershell-script"></a>Fase Power shell-script

Voordat u de Power shell-scripts bijwerkt, moet u ervoor zorgen dat u de volume-GUID van het volume in de VHD hebt. De volume-GUID ophalen:

1.  Open de netwerk share waar de VHD zich bevindt in de virtuele machine waarop u het script wilt uitvoeren.

2.  Klik met de rechter muisknop op de VHD en selecteer **koppelen**. Hiermee wordt de VHD gekoppeld aan een stationsletter.

3.  Nadat u de VHD hebt gekoppeld, wordt het venster **bestanden Verkenner** geopend. De bovenliggende map vastleggen en de **$parentFolder** variabele bijwerken

    >[!NOTE]
    >Als u geen bovenliggende map ziet, betekent dit dat de MSIX niet goed is uitgevouwen. Voer de vorige sectie opnieuw uit en probeer het opnieuw.

4.  Open de bovenliggende map. Als het pakket correct is uitgevouwen, ziet u een map met dezelfde naam als het Pack. Werk de **$packageName** variabele bij zodat deze overeenkomt met de naam van deze map.

    Bijvoorbeeld `VSCodeUserSetup-x64-1.38.1_1.38.1.0_x64__8wekyb3d8bbwe`.

5.  Open een opdracht prompt en voer **mountvol** in. Met deze opdracht wordt een lijst met volumes en de bijbehorende GUID'S weer gegeven. Kopieer de GUID van het volume waar de stationsletter overeenkomt met het station waarnaar u uw VHD hebt gekoppeld in stap 2.

    Als u bijvoorbeeld in dit voor beeld uitvoer voor de opdracht mountvol hebt gekoppeld, kunt u de bovenstaande waarde alleen kopiëren als u de VHD op station C hebt geplaatst `C:\` :

    ```cmd
    Possible values for VolumeName along with current mount points are:

    \\?\Volume{a12b3456-0000-0000-0000-10000000000}\
    *** NO MOUNT POINTS ***

    \\?\Volume{c78d9012-0000-0000-0000-20000000000}\
        E:\

    \\?\Volume{d34e5678-0000-0000-0000-30000000000}\
        C:\

    ```


6.  Werk de **$volumeGuid** variabele bij met de volume-GUID die u zojuist hebt gekopieerd.

7. Open een Power shell-prompt voor beheerders en werk het volgende Power shell-script bij met de variabelen die van toepassing zijn op uw omgeving.

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

### <a name="register-powershell-script"></a>Power shell-script registreren

Als u het REGI ster wilt uitvoeren, voert u de volgende Power shell-cmdlets uit met de waarden voor de tijdelijke aanduiding die zijn vervangen door instellingen die van toepassing zijn op uw omgeving.

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

### <a name="deregister-powershell-script"></a>Het Power shell-script deregistreren

Voor dit script vervangt u de tijdelijke aanduiding voor **$packageName** door de naam van het pakket dat u wilt testen.

```powershell
#MSIX app attach deregistration sample

#region variables
$packageName = "<package name>"
#endregion

#region deregister
Remove-AppxPackage -PreserveRoamableApplicationData $packageName
#endregion
```

### <a name="destage-powershell-script"></a>Power shell-script destage

Voor dit script vervangt u de tijdelijke aanduiding voor **$packageName** door de naam van het pakket dat u wilt testen. In een productie-implementatie kunt u dit het beste uitvoeren bij het afsluiten.

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

## <a name="set-up-simulation-scripts-for-the-msix-app-attach-agent"></a>Simulatie scripts instellen voor de MSIX-agent voor het koppelen van apps

Nadat u de scripts hebt gemaakt, kunnen gebruikers deze hand matig uitvoeren of instellen dat ze automatisch worden uitgevoerd als opstart-, aanmeldings-, afmeldings-en afsluit scripts. Zie [opstart-, afsluit-, aanmeldings-en afmeldings scripts gebruiken in Groepsbeleid](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn789196(v=ws.11)/)voor meer informatie over deze soorten scripts.

Met elk van deze automatische scripts wordt één fase van de app gekoppelde scripts uitgevoerd:

- Het opstart script voert het fase script uit.
- Het aanmeldings script voert het registratie script uit.
- Het afmeldings script voert het registratie script uit.
- Het afsluit script voert het destage-script uit.

## <a name="use-packages-offline"></a>Pakketten offline gebruiken

Als u pakketten gebruikt van de [Microsoft Store voor bedrijven](https://businessstore.microsoft.com/) of de [Microsoft Store voor onderwijs](https://educationstore.microsoft.com/) binnen uw netwerk of op apparaten die niet zijn verbonden met internet, moet u de pakket licenties van de Microsoft Store ophalen en op uw apparaat installeren om de app te kunnen uitvoeren. Als uw apparaat online is en verbinding kan maken met de Microsoft Store voor bedrijven, moeten de vereiste licenties automatisch worden gedownload, maar als u offline bent, moet u de licenties hand matig instellen.

Als u de licentie bestanden wilt installeren, moet u een Power shell-script gebruiken dat de MDM_EnterpriseModernAppManagement_StoreLicenses02_01-klasse aanroept in de WMI Bridge-provider.

U kunt als volgt de licenties instellen voor offline gebruik:

1. Down load het app-pakket, de licenties en de vereiste Frameworks van de Microsoft Store voor bedrijven. U hebt zowel de versleutelde als niet-versleutelde licentie bestanden nodig. Gedetailleerde Download instructies vindt u [hier](/microsoft-store/distribute-offline-apps#download-an-offline-licensed-app).
2. Werk de volgende variabelen bij in het script voor stap 3:
      1. `$contentID` is de ContentID-waarde van het niet-versleutelde licentie bestand (. XML). U kunt het licentie bestand openen in een tekst editor naar keuze.
      2. `$licenseBlob` is de volledige teken reeks voor de licentie-Blob in het gecodeerde licentie bestand (. bin). U kunt het versleutelde licentie bestand openen in een tekst editor naar keuze.
3. Voer het volgende script uit vanuit een Power shell-prompt voor beheerders. Er is een goede plaats om de licentie-installatie uit te voeren aan het einde van het [faserings script](#stage-powershell-script) dat ook moet worden uitgevoerd vanaf een beheerders prompt.

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

Deze functie wordt momenteel niet ondersteund, maar u kunt vragen stellen aan de community op het [virtuele bureau blad van Windows TechCommunity](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop).

U kunt ook feedback geven voor Windows virtueel bureau blad op de [Windows Virtual Desktop feedback hub](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app).
