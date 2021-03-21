---
title: Hand leiding voor het instellen van een Windows-sjabloon machine | Microsoft Docs
description: Algemene stappen voor het voorbereiden van een Windows-sjabloon machine in Lab-Services.  Deze stappen omvatten het instellen van Windows Update planning, het installeren van OneDrive en het installeren van Office.
author: EMaher
ms.topic: article
ms.date: 06/26/2020
ms.author: enewman
ms.openlocfilehash: 216dc843b31eac355e1d818014f3d70b2ef83132
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94647899"
---
# <a name="guide-to-setting-up-a-windows-template-machine-in-azure-lab-services"></a>Hand leiding voor het instellen van een Windows-sjabloon machine in Azure Lab Services

Als u een Windows 10-sjabloon computer instelt voor Azure Lab Services, zijn hier enkele aanbevolen procedures en tips die u kunt overwegen. De onderstaande configuratie stappen zijn allemaal optioneel.  Deze voorbereidende stappen kunnen u helpen om uw studenten productiever te maken, de duur van het klassetijd te beperken en ervoor te zorgen dat ze de nieuwste technologieën gebruiken.

>[!IMPORTANT]
>Dit artikel bevat Power shell-fragmenten om het wijzigings proces van de machine sjabloon te stroom lijnen.  Voor alle Power shell-scripts die worden weer gegeven, moet u deze uitvoeren in Windows Power shell met beheerders bevoegdheden. In Windows 10 kunt u een snelle manier om dit te doen met de rechter muisknop op het menu Start klikken en de ' Windows Power shell (beheerder) ' kiezen.

## <a name="install-and-configure-onedrive"></a>OneDrive installeren en configureren

Als u wilt voor komen dat student gegevens verloren gaan als een virtuele machine opnieuw is ingesteld, raden wij studenten aan om hun gegevens terug te zetten naar de Cloud.  Micro soft OneDrive kan leerlingen/studenten helpen bij het beveiligen van hun gegevens.  

### <a name="install-onedrive"></a>OneDrive installeren

Zie de Download pagina's van [onedrive](https://onedrive.live.com/about/download/) of [onedrive voor bedrijven](https://onedrive.live.com/about/business/) om onedrive hand matig te downloaden en te installeren.

U kunt ook het volgende Power shell-script gebruiken.  De meest recente versie van OneDrive wordt automatisch gedownload en geïnstalleerd.  Zodra de OneDrive-client is geïnstalleerd, voert u het installatie programma uit.  In ons voor beeld gebruiken we de `/allUsers` switch om OneDrive te installeren voor alle gebruikers op de computer. We gebruiken ook de `/silent` Schakel optie om OneDrive op de achtergrond te installeren.

```powershell
Write-Host "Downloading OneDrive Client..."
$DownloadPath = "$env:USERPROFILE/Downloads/OneDriveSetup.exe"
if((Test-Path $DownloadPath) -eq $False )
{
    Write-Host "Downloading OneDrive..."
    $web = new-object System.Net.WebClient
    $web.DownloadFile("https://go.microsoft.com/fwlink/p/?LinkId=248256",$DownloadPath)
} else {
    Write-Host "OneDrive installer already exists at " $DownloadPath
}

Write-Host "Installing OneDrive..."
& $env:USERPROFILE/Downloads/OneDriveSetup.exe /allUsers /silent
```

### <a name="onedrive-customizations"></a>Aanpassingen in OneDrive

Er zijn veel [aanpassingen die u kunt uitvoeren in OneDrive](/onedrive/use-group-policy). Laten we een aantal van de meest voorkomende aanpassingen behandelen.

#### <a name="silently-move-windows-known-folders-to-onedrive"></a>Windows bekende mappen op de achtergrond verplaatsen naar OneDrive

Mappen als documenten, down loads en afbeeldingen worden vaak gebruikt om student bestanden op te slaan. Om ervoor te zorgen dat deze mappen in OneDrive worden opgeslagen, raden we u aan deze mappen naar OneDrive te verplaatsen.

Als u zich op een computer bevindt die niet gebruikmaakt van Active Directory, kunnen gebruikers deze mappen hand matig verplaatsen naar OneDrive zodra ze zijn geverifieerd in OneDrive.

1. Verkenner openen
2. Klik met de rechter muisknop op de map documenten, down loads of afbeeldingen.
3. Ga naar eigenschappen > locatie.  Verplaats de map naar een nieuwe map in de OneDrive-map.

Als uw virtuele machine is verbonden met Active Directory, kunt u instellen dat de sjabloon machine automatisch uw studenten vraagt om de bekende mappen naar OneDrive te verplaatsen.  

U moet eerst uw organisatie-ID ophalen.  Zie [uw Microsoft 365 organisatie-ID zoeken](/onedrive/find-your-office-365-tenant-id)voor meer instructies.  U kunt de organisatie-ID ook ophalen met behulp van de volgende Power shell.

```powershell
Install-Module MSOnline -Confirm
Connect-MsolService
$officeTenantID = Get-MSOLCompanyInformation |
    Select-Object -expand objectID |
    Select-Object -expand Guid
```

Zodra u uw organisatie-ID hebt, stelt u OneDrive in om bekende mappen naar OneDrive te verplaatsen met behulp van de volgende Power shell.

```powershell
if ($officeTenantID -eq $null)
{
        Write-Error "Variable `$officeTenantId must be set to your Office Tenant Id before continuing."
}
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
    -Name "KFMSilentOptIn" -Value $officeTenantID -PropertyType STRING
```

### <a name="use-onedrive-files-on-demand"></a>OneDrive-bestanden op aanvraag gebruiken

Studenten hebben mogelijk veel bestanden binnen hun OneDrive-accounts. Om ruimte te besparen op de computer en de download tijd te beperken, wordt u aangeraden alle bestanden die zijn opgeslagen in het OneDrive-account van studenten op aanvraag in te stellen.  Bestanden op aanvraag worden alleen gedownload zodra een gebruiker toegang heeft tot het bestand.

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive" -Force
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
    -Name "FilesOnDemandEnabled" -Value "00000001" -PropertyType DWORD
```

### <a name="silently-sign-in-users-to-onedrive"></a>Gebruikers op de achtergrond aanmelden bij OneDrive

OneDrive kan worden ingesteld om automatisch aan te melden met de Windows-referenties van de aangemelde gebruiker.  Automatische aanmelding is handig voor klassen waarbij de student zich aanmeldt met hun school referenties.

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
    -Name "SilentAccountConfig" -Value "00000001" -PropertyType DWORD
```

### <a name="disable-the-tutorial-that-appears-at-the-end-of-onedrive-setup"></a>De zelf studie uitschakelen die wordt weer gegeven aan het einde van de installatie van OneDrive

Met deze instelling kunt u voor komen dat de zelf studie wordt gestart in een webbrowser aan het einde van de installatie van OneDrive.

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive" -Force
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
    -Name "DisableTutorial" -Value "00000001" -PropertyType DWORD -Force
```

### <a name="set-the-maximum-size-of-a-file-that-to-be-download-automatically"></a>De maximale grootte instellen van een bestand dat automatisch moet worden gedownload

Deze instelling wordt gebruikt in combi natie met gebruikers die zich op de achtergrond aanmelden bij de OneDrive-Sync-Client met hun Windows-referenties op apparaten waarvoor geen OneDrive-bestanden op aanvraag zijn ingeschakeld. Gebruikers met een OneDrive die groter is dan de opgegeven drempel waarde (in MB), wordt gevraagd de mappen te kiezen die ze willen synchroniseren voordat de OneDrive Sync-Client (OneDrive.exe) de bestanden downloadt.  In ons voor beeld is ' 1111-2222-3333-4444 ' de organisatie-ID en 0005000 stelt een drempel van 5 GB in.

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive\DiskSpaceCheckThresholdMB"
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive\DiskSpaceCheckThresholdMB"
    -Name "1111-2222-3333-4444" -Value "0005000" -PropertyType DWORD
```

## <a name="install-and-configure-microsoft-365"></a>Microsoft 365 installeren en configureren

### <a name="install-microsoft-365"></a>Microsoft 365 installeren

Als uw sjabloon machine Office nodig heeft, raden wij u aan Office te installeren via het [Office Deployment Tool (ODT)](https://www.microsoft.com/download/details.aspx?id=49117). U moet een herbruikbaar configuratie bestand maken met behulp van het [beheer centrum](https://config.office.com/) van de Microsoft 365-apps om te kiezen welke architectuur, welke functies u nodig hebt vanuit Office en hoe vaak het wordt bijgewerkt.

1. Ga naar het [beheer centrum van Microsoft 365-apps](https://config.office.com/) en down load uw eigen configuratie bestand.
2. Down load [Office Deployment Tool](https://www.microsoft.com/download/details.aspx?id=49117).  Het gedownloade bestand is `setup.exe` .
3. Voer uit `setup.exe /download configuration.xml` om Office-onderdelen te downloaden.
4. Voer uit `setup.exe /configure configuration.xml` om Office-onderdelen te installeren.

### <a name="change-the-microsoft-365-update-channel"></a>Het Microsoft 365-update kanaal wijzigen

Met het Office-configuratie programma kunt u instellen hoe vaak Office updates ontvangt. Als u echter wilt wijzigen hoe vaak Office updates ontvangt na de installatie, kunt u de URL van het update kanaal wijzigen. URL-adressen van het update kanaal kunt u vinden op [het update kanaal voor de Microsoft 365 apps voor apparaten in uw organisatie](/deployoffice/change-update-channels). In het onderstaande voor beeld ziet u hoe u Microsoft 365 instelt om het maandelijkse update kanaal te gebruiken.

```powershell
# Update to the Microsoft 365 Monthly Channel
Set-ItemProperty
    -Path "HKLM:\SOFTWARE\Microsoft\Office\ClickToRun\Configuration\CDNBaseUrl"
    -Name "CDNBaseUrl"
    -Value "http://officecdn.microsoft.com/pr/492350f6-3a01-4f97-b9c0-c7c6ddf67d60"
```

## <a name="install-and-configure-updates"></a>Updates installeren en configureren

### <a name="install-the-latest-windows-updates"></a>Installeer de meest recente Windows-updates

U wordt aangeraden de meest recente micro soft-updates op de sjabloon computer te installeren voor beveiligings doeleinden voordat u de sjabloon-VM publiceert.  Het is ook mogelijk om te voor komen dat studenten in hun werk worden onderbroken wanneer updates op onverwachte tijden worden uitgevoerd.

1. **Instellingen** starten vanuit het menu Start
2. Klik op **Update** & beveiliging
3. Klik op **controleren op updates**
4. Updates worden gedownload en geïnstalleerd.

U kunt Power shell ook gebruiken om de sjabloon machine bij te werken.

```powershell
Set-ExecutionPolicy Bypass -Scope Process -Force
Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Confirm
Install-Module PSWindowsUpdate -Confirm
Install-WindowsUpdate -MicrosoftUpdate
Set-ExecutionPolicy default -Force
```

>[!NOTE]
>Voor sommige updates moet de computer mogelijk opnieuw worden opgestart.  U wordt gevraagd of opnieuw opstarten is vereist.

### <a name="install-the-latest-updates-for-microsoft-store-apps"></a>De nieuwste updates voor Microsoft Store-apps installeren

Het is raadzaam dat alle Microsoft Store-apps worden bijgewerkt naar de nieuwste versie.  Hier volgen instructies voor het hand matig bijwerken van toepassingen van de Microsoft Store.  

1. Start **Microsoft Store** -toepassing.
2. Klik op het weglatings teken (...) naast uw gebruikers foto in de bovenste hoek van de toepassing.
3. Selecteer **downloaden** en bijwerken in de vervolg keuzelijst.
4. Klik op de knop **update ophalen** .

U kunt ook Power shell gebruiken voor het bijwerken van Microsoft Store toepassingen die al zijn geïnstalleerd.

```powershell
(Get-WmiObject -Namespace "root\cimv2\mdm\dmmap" -Class "MDM_EnterpriseModernAppManagement_AppManagement01").UpdateScanMethod()
```

### <a name="stop-automatic-windows-updates"></a>Automatische Windows-updates stoppen

Nadat u Windows hebt bijgewerkt naar de nieuwste versie, kunt u overwegen Windows-updates te stoppen.  Automatische updates kunnen problemen veroorzaken met de geplande tijd van de klasse.  Als uw cursus een langere keer wordt uitgevoerd, kunt u de cursisten vragen om hand matig te controleren op updates of automatische updates in te stellen voor een tijd buiten de geplande uren.  Zie voor meer informatie over aanpassings opties voor Windows Update de [instellingen voor het beheren van aanvullende Windows Update](/windows/deployment/update/waas-wu-settings).

Automatische Windows-updates worden mogelijk gestopt met het volgende Power shell-script.

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\Windows\AU"
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\Windows\AU"
    -Name "NoAutoUpdate" -Value "1" -PropertyType DWORD
```

## <a name="install-foreign-language-packs"></a>Vreemde taal pakketten installeren

Als u extra talen hebt geïnstalleerd op de virtuele machine, kunt u deze toevoegen via de Microsoft Store.

1. Microsoft Store starten
2. Zoeken naar taal pakket
3. Selecteer de taal die u wilt installeren

Als u al bent aangemeld bij de sjabloon-VM, gebruikt u de snelkoppeling installeren taal pakket ( `ms-settings:regionlanguage?activationSource=SMC-IA-4027670` ) om rechtstreeks naar de juiste instellingen pagina te gaan.

## <a name="remove-unneeded-built-in-apps"></a>Overbodige ingebouwde apps verwijderen

Windows 10 wordt geleverd met veel ingebouwde toepassingen die mogelijk niet nodig zijn voor uw specifieke klasse. Als u de machine installatie kopie voor studenten wilt vereenvoudigen, kunt u sommige toepassingen van de sjabloon computer verwijderen.  Als u een lijst met geïnstalleerde toepassingen wilt weer geven, gebruikt u de Power shell- `Get-AppxPackage` cmdlet.  In het onderstaande voor beeld ziet u alle geïnstalleerde toepassingen die kunnen worden verwijderd.

```powershell
Get-AppxPackage | Where {$_.NonRemovable -eq $false} | select Name
```

Als u een toepassing wilt verwijderen, gebruikt u de cmdlet Remove-Appx.  In het onderstaande voor beeld ziet u hoe u alle XBox-gerelateerde kunt verwijderen.

```powershell
Get-AppxPackage -Name *xbox* | foreach { if (-not $_.NonRemovable) { Remove-AppxPackage $_} }
```

## <a name="install-common-teaching-related-applications"></a>Algemene onderwijs toepassingen installeren

Andere apps installeren die vaak worden gebruikt voor het onderwijs via de Windows Store-app. Suggesties zijn onder andere toepassingen zoals [micro soft White Board app](https://www.microsoft.com/store/productId/9MSPC6MP8FM4), [micro soft teams](https://www.microsoft.com/store/productId/9MSPC6MP8FM4)en [Minecraft Education Edition](https://education.minecraft.net/). Deze toepassingen moeten hand matig worden geïnstalleerd via de Windows Store of via hun respectieve websites op de sjabloon-VM.

## <a name="conclusion"></a>Conclusie

In dit artikel vindt u optionele stappen voor het voorbereiden van uw Windows-sjabloon-VM voor een doel treffende klasse.  Stappen zijn het installeren van OneDrive en het installeren van Microsoft 365, het installeren van updates voor Windows en het installeren van updates voor Microsoft Store-apps.  Ook wordt uitgelegd hoe u updates instelt voor een planning die het beste werkt voor uw klasse.  

## <a name="next-steps"></a>Volgende stappen
Raadpleeg het artikel over het beheren van het afsluit gedrag van Windows voor hulp bij het beheren van kosten: [hand leiding voor het beheren van het afsluit gedrag van Windows](how-to-windows-shutdown.md)