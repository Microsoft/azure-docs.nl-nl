---
title: Taal pakketten installeren op Windows 10-Vm's in virtueel bureau blad van Windows-Azure
description: Taal pakketten installeren voor Vm's met meerdere sessies van Windows 10 in Windows virtueel bureau blad.
author: Heidilohr
ms.topic: how-to
ms.date: 12/03/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: fc7892db2ca11ab7970835f8979360961ee01104
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103463337"
---
# <a name="add-language-packs-to-a-windows-10-multi-session-image"></a>Taal pakketten toevoegen aan een Windows 10-installatie kopie met meerdere sessies

Windows Virtual Desktop is een service die uw gebruikers overal en overal kunnen implementeren. Daarom is het belang rijk dat uw gebruikers kunnen aanpassen in welke taal hun Windows 10 Enter prise-afbeelding voor meerdere sessies wordt weer gegeven.

Er zijn twee manieren waarop u de taal behoeften van uw gebruikers kunt aanpassen:

- Bouw specifieke hostgroepen met een aangepaste installatie kopie voor elke taal.
- Hebben gebruikers met verschillende taal-en lokalisatie vereisten in dezelfde hostgroep, maar aanpassen hun installatie kopieën om ervoor te zorgen dat ze de gewenste taal kunnen selecteren.

Deze laatste methode is veel efficiënter en rendabeler. U kunt echter zelf bepalen welke methode het beste aansluit bij uw behoeften. In dit artikel wordt uitgelegd hoe u talen voor uw installatie kopieën kunt aanpassen.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende zaken nodig om uw installatie kopieën met meerdere sessies van Windows 10 Enter prise aan te passen om meerdere talen toe te voegen:

- Een Azure-virtuele machine (VM) met Windows 10 Enter prise meerdere sessies, versie 1903 of hoger

- De taal ISO, functie on demand (DOM) schijf 1 en Postvak in-apps ISO van de versie van het besturings systeem die wordt gebruikt door de installatie kopie. U kunt ze hier downloaden:
     
     - Taal ISO:
        - [Windows 10, versie 1903 of 1909 taal pakket ISO](https://software-download.microsoft.com/download/pr/18362.1.190318-1202.19h1_release_CLIENTLANGPACKDVD_OEM_MULTI.iso)
        - [Windows 10, versie 2004 of 20H2 language pack ISO](https://software-download.microsoft.com/download/pr/19041.1.191206-1406.vb_release_CLIENTLANGPACKDVD_OEM_MULTI.iso)

     - Dom-schijf 1 ISO:
        - [Windows 10, versie 1903 of 1909 dom schijf 1 ISO](https://software-download.microsoft.com/download/pr/18362.1.190318-1202.19h1_release_amd64fre_FOD-PACKAGES_OEM_PT1_amd64fre_MULTI.iso)
        - [Windows 10, versie 2004 of 20H2 dom schijf 1 ISO](https://software-download.microsoft.com/download/pr/19041.1.191206-1406.vb_release_amd64fre_FOD-PACKAGES_OEM_PT1_amd64fre_MULTI.iso)
        
     - ISO-apps voor Postvak in:
        - [Windows 10, versie 1903 of 1909 postvak in-apps ISO](https://software-download.microsoft.com/download/pr/18362.1.190318-1202.19h1_release_amd64fre_InboxApps.iso)
        - [Windows 10, versie 2004 postvak in-apps ISO](https://software-download.microsoft.com/download/pr/19041.1.191206-1406.vb_release_amd64fre_InboxApps.iso)
        - [Windows 10, versie 20H2 postvak in-apps ISO](https://software-download.microsoft.com/download/pr/19041.508.200905-1327.vb_release_svc_prod1_amd64fre_InboxApps.iso)
     
     - Als u ISO-bestanden van het lokale Experience Pack (LXP) gebruikt om uw installatie kopieën te lokaliseren, moet u ook de juiste LXP ISO voor de beste taal ervaring downloaden
        - Als u werkt met Windows 10, versie 1903 of 1909:
          - [Windows 10, versie 1903 of 1909 LXP ISO](https://software-download.microsoft.com/download/pr/Win_10_1903_32_64_ARM64_MultiLng_LngPkAll_LXP_ONLY.iso)
        - Als u gebruikmaakt van Windows 10, versie 2004 of 20H2, gebruikt u de informatie in [talen toevoegen in Windows 10: bekende problemen](/windows-hardware/manufacture/desktop/language-packs-known-issue) om te achterhalen welke van de volgende LXP-iso's het meest geschikt is voor u:
          - [Windows 10, versie 2004 of 20H2 **9b** LXP ISO](https://software-download.microsoft.com/download/pr/Win_10_2004_64_ARM64_MultiLang_LangPckAll_LIP_LXP_ONLY)
          - [Windows 10, versie 2004 of 20H2 **9C** LXP ISO](https://software-download.microsoft.com/download/pr/Win_10_2004_32_64_ARM64_MultiLng_LngPkAll_LIP_9C_LXP_ONLY)
          - [Windows 10, versie 2004 of 20H2 **10C** LXP ISO](https://software-download.microsoft.com/download/pr/LanguageExperiencePack.2010C.iso)
          - [Windows 10, versie 2004 of 20H2 **11C** LXP ISO](https://software-download.microsoft.com/download/pr/LanguageExperiencePack.2011C.iso)
          - [Windows 10, versie 2004 of 20H2 **1c** LXP ISO](https://software-download.microsoft.com/download/pr/LanguageExperiencePack.2101C.iso)
          - [Windows 10, versie 2004 of 20H2 **2c** LXP ISO](https://software-download.microsoft.com/download/pr/LanguageExperiencePack.2102C.iso)

- Een Azure Files share of bestands share op een virtuele machine met Windows-Bestands server

>[!NOTE]
>De bestands share (opslag plaats) moet toegankelijk zijn vanaf de Azure-VM die u wilt gebruiken om de aangepaste installatie kopie te maken.

## <a name="create-a-content-repository-for-language-packages-and-features-on-demand"></a>Een inhouds opslagplaats maken voor taal pakketten en onderdelen op aanvraag

Voor het maken van de inhouds opslagplaats voor taal pakketten en FODs en een opslag plaats voor de app-pakketten voor het postvak in:

1. Down load op een virtuele machine van Azure de Windows 10-apps voor meerdere talen ISO, FODs en Postvak in voor Windows 10 Enter prise multi-session, versie 1903/1909 en 2004 installatie kopieën van de koppelingen in [vereisten](#prerequisites).

2. Open en koppel de ISO-bestanden op de VM.

3. Ga naar het taal pakket ISO en kopieer de inhoud van de **LocalExperiencePacks** -en x64-bestanden van het Pack **\\ lang** en plak de inhoud vervolgens in de bestands share.

4. Ga naar het **bestand in de Dom ISO**, kopieer alle inhoud en plak het in de bestands share.
5. Ga naar de map **amd64fre** in de ISO-apps van het postvak in en kopieer de inhoud in de opslag plaats voor de apps in het postvak in die u hebt voor bereid.

     >[!NOTE]
     > Als u met beperkte opslag werkt, kopieert u alleen de bestanden voor de talen die uw gebruikers nodig hebben. U kunt de bestanden uit elkaar laten zien door de taal codes in hun bestands namen te bekijken. Het Franse bestand heeft bijvoorbeeld de code ' fr-FR ' in de naam. Zie [beschik bare taal pakketten voor Windows](/windows-hardware/manufacture/desktop/available-language-packs-for-windows)voor een volledige lijst met taal codes voor alle beschik bare talen.

     >[!IMPORTANT]
     > Voor sommige talen zijn extra letter typen in satelliet pakketten vereist die voldoen aan verschillende naam conventies. Bijvoorbeeld: Japanse lettertype bestandsnamen bevatten ' Jpan '.
     >
     > [!div class="mx-imgBorder"]
     > ![Een voor beeld van de Japanse taal pakketten met de taal code ' Jpan ' in hun bestands namen.](media/language-pack-example.png)

6. Stel de machtigingen in op de taal van de opslag plaats voor inhoud, zodat u over lees toegang hebt vanaf de VM die u gebruikt om de aangepaste installatie kopie te maken.

## <a name="create-a-custom-windows-10-enterprise-multi-session-image-manually"></a>Een aangepaste Windows 10 Enter prise-installatie kopie voor meerdere sessies hand matig maken

Hand matig een aangepaste Windows 10 Enter prise-installatie kopie maken:

1. Implementeer een Azure VM en ga naar de Azure-galerie en selecteer de huidige versie van Windows 10 Enter prise multi-session die u gebruikt.
2. Nadat u de virtuele machine hebt geïmplementeerd, maakt u deze met behulp van RDP als een lokale beheerder.
3. Zorg ervoor dat uw VM over alle meest recente Windows-updates beschikt. Down load de updates en start de VM indien nodig opnieuw op.
4. Maak verbinding met het taal pakket, de dom en de bestands share opslagplaats voor het postvak in en koppel deze aan een stationsletter (bijvoorbeeld station E).

## <a name="create-a-custom-windows-10-enterprise-multi-session-image-automatically"></a>Automatisch een aangepaste installatie kopie van Windows 10 Enter prise maken

Als u liever talen installeert via een geautomatiseerd proces, kunt u een script instellen in Power shell. U kunt het volgende script voorbeeld gebruiken om de taal pakketten Spaans (Spanje), Frans (Frank rijk) en Chinees (volks Republiek China) en satelliet pakketten voor Windows 10 Enter prise multi-session versie 2004 te installeren. Het script integreert het taal interface pakket en alle benodigde satelliet pakketten in de installatie kopie. U kunt dit script echter ook aanpassen om andere talen te installeren. Zorg ervoor dat u het script uitvoert vanuit een Power shell-sessie met verhoogde bevoegdheid, anders werkt het niet.

```powershell
########################################################
## Add Languages to running Windows Image for Capture##
########################################################

##Disable Language Pack Cleanup##
Disable-ScheduledTask -TaskPath "\Microsoft\Windows\AppxDeploymentClient\" -TaskName "Pre-staged app cleanup"

##Set Language Pack Content Stores##
[string]$LIPContent = "E:"

##Spanish##
Add-AppProvisionedPackage -Online -PackagePath $LIPContent\es-es\LanguageExperiencePack.es-es.Neutral.appx -LicensePath $LIPContent\es-es\License.xml
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Client-Language-Pack_x64_es-es.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Basic-es-es-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Handwriting-es-es-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-OCR-es-es-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Speech-es-es-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-TextToSpeech-es-es-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-NetFx3-OnDemand-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-InternetExplorer-Optional-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-MSPaint-FoD-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Notepad-FoD-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-PowerShell-ISE-FOD-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Printing-WFS-FoD-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-StepsRecorder-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-WordPad-FoD-Package~31bf3856ad364e35~amd64~es-es~.cab
$LanguageList = Get-WinUserLanguageList
$LanguageList.Add("es-es")
Set-WinUserLanguageList $LanguageList -force

##French##
Add-AppProvisionedPackage -Online -PackagePath $LIPContent\fr-fr\LanguageExperiencePack.fr-fr.Neutral.appx -LicensePath $LIPContent\fr-fr\License.xml
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Client-Language-Pack_x64_fr-fr.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Basic-fr-fr-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Handwriting-fr-fr-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-OCR-fr-fr-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Speech-fr-fr-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-TextToSpeech-fr-fr-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-NetFx3-OnDemand-Package~31bf3856ad364e35~amd64~fr-fr~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-InternetExplorer-Optional-Package~31bf3856ad364e35~amd64~fr-FR~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-MSPaint-FoD-Package~31bf3856ad364e35~amd64~fr-FR~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Notepad-FoD-Package~31bf3856ad364e35~amd64~fr-FR~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-PowerShell-ISE-FOD-Package~31bf3856ad364e35~amd64~fr-FR~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Printing-WFS-FoD-Package~31bf3856ad364e35~amd64~fr-FR~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-StepsRecorder-Package~31bf3856ad364e35~amd64~fr-FR~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-WordPad-FoD-Package~31bf3856ad364e35~amd64~fr-FR~.cab
$LanguageList = Get-WinUserLanguageList
$LanguageList.Add("fr-fr")
Set-WinUserLanguageList $LanguageList -force

##Chinese(PRC)##
Add-AppProvisionedPackage -Online -PackagePath $LIPContent\zh-cn\LanguageExperiencePack.zh-cn.Neutral.appx -LicensePath $LIPContent\zh-cn\License.xml
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Client-Language-Pack_x64_zh-cn.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Basic-zh-cn-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Fonts-Hans-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Handwriting-zh-cn-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-OCR-zh-cn-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Speech-zh-cn-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-TextToSpeech-zh-cn-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-NetFx3-OnDemand-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-InternetExplorer-Optional-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-MSPaint-FoD-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Notepad-FoD-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-PowerShell-ISE-FOD-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Printing-WFS-FoD-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-StepsRecorder-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-WordPad-FoD-Package~31bf3856ad364e35~amd64~zh-cn~.cab
$LanguageList = Get-WinUserLanguageList
$LanguageList.Add("zh-cn")
Set-WinUserLanguageList $LanguageList -force
```

Het script kan enige tijd duren, afhankelijk van het aantal talen dat u moet installeren.

Nadat het script is uitgevoerd, controleert u of de taal pakketten correct zijn geïnstalleerd door naar **Start**  >  **Settings**  >  **time & language**  >  **Language** te gaan. Als de taal bestanden zich daar bevinden, bent u klaar.

Nadat u extra talen aan de Windows-installatie kopie hebt toegevoegd, moeten de apps in het postvak in ook worden bijgewerkt ter ondersteuning van de toegevoegde talen. U kunt dit doen door de vooraf geïnstalleerde apps te vernieuwen met de inhoud van de ISO van het postvak in-apps. Als u deze vernieuwing wilt uitvoeren in een omgeving zonder verbinding (geen Internet toegang vanaf de VM mogelijk), kunt u het volgende Power shell-voorbeeld script gebruiken om het proces te automatiseren.

```powershell
#########################################
## Update Inbox Apps for Multi Language##
#########################################
##Set Inbox App Package Content Stores##
[string]$InboxApps = "F:\"
##Update Inbox Store Apps##
$AllAppx = Get-Item $inboxapps\*.appx | Select-Object name
$AllAppxBundles = Get-Item $inboxapps\*.appxbundle | Select-Object name
$allAppxXML = Get-Item $inboxapps\*.xml | Select-Object name
foreach ($Appx in $AllAppx) {
    $appname = $appx.name.substring(0,$Appx.name.length-5)
    $appnamexml = $appname + ".xml"
    $pathappx = $InboxApps + "\" + $appx.Name
    $pathxml = $InboxApps + "\" + $appnamexml
    
    if($allAppxXML.name.Contains($appnamexml)){
    
    Write-Host "Handeling with xml $appname"  
  
    Add-AppxProvisionedPackage -Online -PackagePath $pathappx -LicensePath $pathxml
    } else {
      
      Write-Host "Handeling without xml $appname"
      
      Add-AppxProvisionedPackage -Online -PackagePath $pathappx -skiplicense
    }
}
foreach ($Appx in $AllAppxBundles) {
    $appname = $appx.name.substring(0,$Appx.name.length-11)
    $appnamexml = $appname + ".xml"
    $pathappx = $InboxApps + "\" + $appx.Name
    $pathxml = $InboxApps + "\" + $appnamexml
    
    if($allAppxXML.name.Contains($appnamexml)){
    Write-Host "Handeling with xml $appname"
    
    Add-AppxProvisionedPackage -Online -PackagePath $pathappx -LicensePath $pathxml
    } else {
       Write-Host "Handeling without xml $appname"
      Add-AppxProvisionedPackage -Online -PackagePath $pathappx -skiplicense
    }
}
```

>[!IMPORTANT]
>De in de ISO opgenomen postvak in-apps zijn niet de nieuwste versies van de vooraf geïnstalleerde Windows-apps. Als u de nieuwste versie van alle apps wilt downloaden, moet u de apps bijwerken met behulp van de Windows Store-app en een hand matige zoek actie uitvoeren op updates nadat u de extra talen hebt geïnstalleerd.

Wanneer u klaar bent, moet u de verbinding met de share verbreken.

## <a name="finish-customizing-your-image"></a>Het aanpassen van uw installatie kopie volt ooien

Nadat u de taal pakketten hebt geïnstalleerd, kunt u andere software installeren die u wilt toevoegen aan uw aangepaste installatie kopie.

Wanneer u klaar bent met het aanpassen van uw installatie kopie, moet u het hulp programma voor systeem voorbereiding (Sysprep) uitvoeren.

Sysprep uitvoeren:

1. Open een opdracht prompt met verhoogde bevoegdheid en voer de volgende opdracht uit om de installatie kopie te generaliseren:  
   
     ```cmd
     C:\Windows\System32\Sysprep\sysprep.exe /oobe /generalize /shutdown
     ```

2. Stop de virtuele machine en leg deze vast in een beheerde installatie kopie door de instructies in [een beheerde installatie kopie maken van een gegeneraliseerde vm in azure](../virtual-machines/windows/capture-image-resource.md)te volgen.

3. U kunt nu de aangepaste installatie kopie gebruiken voor het implementeren van een Windows-hostgroep voor virtueel bureau blad. Zie [zelf studie: een hostgroep maken met de Azure Portal](create-host-pools-azure-marketplace.md)voor meer informatie over het implementeren van een hostgroep.

## <a name="enable-languages-in-windows-settings-app"></a>Talen inschakelen in de Windows-instellingen-app

Ten slotte moet u nadat u de hostgroep hebt geïmplementeerd, de taal toevoegen aan de taal lijst van elke gebruiker zodat deze de gewenste taal kan selecteren in het menu instellingen.

Om ervoor te zorgen dat uw gebruikers de talen kunnen selecteren die u hebt geïnstalleerd, meldt u zich aan als de gebruiker en voert u de volgende Power shell-cmdlet uit om de geïnstalleerde taal pakketten toe te voegen aan het menu talen. U kunt dit script ook instellen als een geautomatiseerde taak of een aanmeldings script dat wordt geactiveerd wanneer de gebruiker zich aanmeldt bij de sessie.

```powershell
$LanguageList = Get-WinUserLanguageList
$LanguageList.Add("es-es")
$LanguageList.Add("fr-fr")
$LanguageList.Add("zh-cn")
Set-WinUserLanguageList $LanguageList -force
```

Nadat een gebruiker de taal instellingen heeft gewijzigd, moeten ze zich afmelden bij hun Windows Virtual Desktop-sessie en zich opnieuw aanmelden om de wijzigingen van kracht te laten worden. 

## <a name="next-steps"></a>Volgende stappen

Zie [taal pakketten toevoegen in Windows 10, versie 1803 en latere versies](/windows-hardware/manufacture/desktop/language-packs-known-issue)voor meer informatie over bekende problemen voor taal pakketten: bekende problemen.

Als u andere vragen over Windows 10 Enter prise multi-session hebt, lees dan onze [Veelgestelde vragen](windows-10-multisession-faq.md).
