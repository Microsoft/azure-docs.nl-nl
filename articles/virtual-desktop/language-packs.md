---
title: Taalpakketten installeren op Windows 10-VM's in Windows Virtual Desktop - Azure
description: Taalpakketten installeren voor virtuele Windows 10 met meerdere sessies in Windows Virtual Desktop.
author: Heidilohr
ms.topic: how-to
ms.date: 12/03/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: db1ac3f5de507a5cfdbfec7216afea9a0f4ac541
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107515036"
---
# <a name="add-language-packs-to-a-windows-10-multi-session-image"></a>Taalpakketten toevoegen aan een Windows 10 voor meerdere sessies

Windows Virtual Desktop is een service die uw gebruikers altijd en overal kunnen implementeren. Daarom is het belangrijk dat uw gebruikers in staat zijn om aan te passen welke taal hun Windows 10 Enterprise afbeelding voor meerdere sessies wordt weergegeven.

Er zijn twee manieren waarop u kunt voldoen aan de taalbehoeften van uw gebruikers:

- Bouw toegewezen hostgroepen met een aangepaste afbeelding voor elke taal.
- Gebruikers met verschillende taal- en lokalisatievereisten in dezelfde hostgroep hebben, maar hun afbeeldingen aanpassen om ervoor te zorgen dat ze kunnen selecteren welke taal ze nodig hebben.

De laatste methode is veel efficiënter en rendabeler. Het is echter aan u om te bepalen welke methode het beste bij uw behoeften past. In dit artikel wordt beschreven hoe u talen voor uw afbeeldingen kunt aanpassen.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om uw aangepaste Windows 10 Enterprise meerdere sessies aan te passen om meerdere talen toe te voegen:

- Een virtuele Azure-machine (VM) Windows 10 Enterprise meerdere sessies, versie 1903 of hoger

- De Language ISO, Feature on Demand (FOD) Disk 1 en Inbox Apps ISO van de versie van het besturingssysteem die de afbeelding gebruikt. U kunt ze hier downloaden:
     
     - Taal ISO:
        - [Windows 10, versie 1903 of 1909 Language Pack ISO](https://software-download.microsoft.com/download/pr/18362.1.190318-1202.19h1_release_CLIENTLANGPACKDVD_OEM_MULTI.iso)
        - [Windows 10, versie 2004 of 20H2 Language Pack ISO](https://software-download.microsoft.com/download/pr/19041.1.191206-1406.vb_release_CLIENTLANGPACKDVD_OEM_MULTI.iso)

     - FOD Disk 1 ISO:
        - [Windows 10, versie 1903 of 1909 FOD Disk 1 ISO](https://software-download.microsoft.com/download/pr/18362.1.190318-1202.19h1_release_amd64fre_FOD-PACKAGES_OEM_PT1_amd64fre_MULTI.iso)
        - [Windows 10 versie 2004 of 20H2 FOD Disk 1 ISO](https://software-download.microsoft.com/download/pr/19041.1.191206-1406.vb_release_amd64fre_FOD-PACKAGES_OEM_PT1_amd64fre_MULTI.iso)
        
     - Iso voor Postvak IN-apps:
        - [Windows 10, versie 1903 of 1909 Inbox Apps ISO](https://software-download.microsoft.com/download/pr/18362.1.190318-1202.19h1_release_amd64fre_InboxApps.iso)
        - [Windows 10 versie 2004 ISO voor Postvak IN-apps](https://software-download.microsoft.com/download/pr/19041.1.191206-1406.vb_release_amd64fre_InboxApps.iso)
        - [Windows 10, versie 20H2 Inbox Apps ISO](https://software-download.microsoft.com/download/pr/19041.508.200905-1327.vb_release_svc_prod1_amd64fre_InboxApps.iso)
     
     - Als u Local Experience-pakket ISO-bestanden (LXP) gebruikt om uw afbeeldingen te lokaliseren, moet u ook de juiste LXP ISO downloaden voor de beste taalervaring
        - Als u een Windows 10, versie 1903 of 1909:
          - [Windows 10, versie 1903 of 1909 LXP ISO](https://software-download.microsoft.com/download/pr/Win_10_1903_32_64_ARM64_MultiLng_LngPkAll_LXP_ONLY.iso)
        - Als u Windows 10, versie 2004 of 20H2 gebruikt, gebruikt u de informatie in Talen [toevoegen in Windows 10:](/windows-hardware/manufacture/desktop/language-packs-known-issue) Bekende problemen om erachter te komen welke van de volgende LXP ISO's voor u het meest van toepassing zijn:
          - [Windows 10, versie 2004 of 20H2 **9B** LXP ISO](https://software-download.microsoft.com/download/pr/Win_10_2004_64_ARM64_MultiLang_LangPckAll_LIP_LXP_ONLY)
          - [Windows 10, versie 2004 of 20H2 **9C** LXP ISO](https://software-download.microsoft.com/download/pr/Win_10_2004_32_64_ARM64_MultiLng_LngPkAll_LIP_9C_LXP_ONLY)
          - [Windows 10, versie 2004 of 20H2 **10C** LXP ISO](https://software-download.microsoft.com/download/pr/LanguageExperiencePack.2010C.iso)
          - [Windows 10, versie 2004 of 20H2 **11C** LXP ISO](https://software-download.microsoft.com/download/pr/LanguageExperiencePack.2011C.iso)
          - [Windows 10, versie 2004 of 20H2 **1C** LXP ISO](https://software-download.microsoft.com/download/pr/LanguageExperiencePack.2101C.iso)
          - [Windows 10, versie 2004 of 20H2 **2C** LXP ISO](https://software-download.microsoft.com/download/pr/LanguageExperiencePack.2102C.iso)
          - [Windows 10, versie 2004 of 20H2 **3C** LXP ISO](https://software-download.microsoft.com/download/pr/LanguageExperiencePack.2103C.iso)

- Een Azure Files share of een bestands share op een virtuele Windows-bestandsservermachine

>[!NOTE]
>De bestands share (opslagplaats) moet toegankelijk zijn vanaf de Azure-VM die u wilt gebruiken om de aangepaste afbeelding te maken.

## <a name="create-a-content-repository-for-language-packages-and-features-on-demand"></a>Een opslagplaats voor inhoud maken voor taalpakketten en -functies op aanvraag

De inhoudsopslagplaats voor taalpakketten en FOD's en een opslagplaats voor de Inbox Apps-pakketten maken:

1. Download op een Azure-VM de Windows 10 ISO-, FOD's en Postvak IN-apps voor Windows 10 Enterprise voor meerdere sessies, versie 1903/1909 en 2004 van de koppelingen in [Vereisten](#prerequisites).

2. Open en bevestig de ISO-bestanden op de VM.

3. Ga naar het taalpakket ISO en kopieer de inhoud uit de mappen **LocalExperiencePacks** en **x64 \\ langpacks** en plak de inhoud in de bestands share.

4. Ga naar het **FOD ISO-bestand,** kopieer alle inhoud en plak deze in de bestands share.
5. Ga naar de **map amd64fre** in de ISO van Inbox Apps en kopieer de inhoud in de opslagplaats voor de Postvak IN-apps die u hebt voorbereid.

     >[!NOTE]
     > Als u met beperkte opslag werkt, kopieert u alleen de bestanden voor de talen die uw gebruikers nodig hebben. U kunt de bestanden onderscheiden door te kijken naar de taalcodes in hun bestandsnamen. Het Franse bestand bevat bijvoorbeeld de code fr-FR in de naam. Zie Beschikbare taalpakketten voor Windows voor een volledige lijst met taalcodes [voor alle beschikbare talen.](/windows-hardware/manufacture/desktop/available-language-packs-for-windows)

     >[!IMPORTANT]
     > Voor sommige talen zijn aanvullende lettertypen vereist die zijn opgenomen in satellietpakketten die verschillende naamconventen volgen. Japanse lettertypebestandsnamen bevatten bijvoorbeeld 'Jpan'.
     >
     > [!div class="mx-imgBorder"]
     > ![Een voorbeeld van de Japanse taalpakketten met de taaltag 'Jpan' in de bestandsnamen.](media/language-pack-example.png)

6. Stel de machtigingen in voor de inhoudsopslagplaats voor taal, zodat u leestoegang hebt vanaf de VM die u gaat gebruiken om de aangepaste afbeelding te bouwen.

## <a name="create-a-custom-windows-10-enterprise-multi-session-image-manually"></a>Handmatig een aangepaste Windows 10 Enterprise voor meerdere sessies maken

Handmatig een aangepaste Windows 10 Enterprise voor meerdere sessies maken:

1. Implementeer een azure-VM, ga naar de Azure Gallery en selecteer de huidige versie van Windows 10 Enterprise sessie die u gebruikt.
2. Nadat u de VM hebt geïmplementeerd, maakt u er verbinding mee met behulp van RDP als lokale beheerder.
3. Zorg ervoor dat uw VM alle nieuwste Windows-updates heeft. Download de updates en start de VM opnieuw, indien nodig.
4. Maak verbinding met het taalpakket, de FOD en de bestands shareopslagplaats van Inbox Apps en koppel deze aan een letterstation (bijvoorbeeld station E).

## <a name="create-a-custom-windows-10-enterprise-multi-session-image-automatically"></a>Automatisch een aangepaste Windows 10 Enterprise voor meerdere sessies maken

Als u liever talen installeert via een geautomatiseerd proces, kunt u een script instellen in PowerShell. U kunt het volgende voorbeeldscript gebruiken om de taalpakketten Spaans (Spanje), Frans (Frankrijk) en Chinees (2004) en satellietpakketten te installeren voor Windows 10 Enterprise meerdere sessies, versie 2004. Het script integreert het taalinterfacepakket en alle benodigde satellietpakketten in de afbeelding. U kunt dit script echter ook wijzigen om andere talen te installeren. Zorg ervoor dat u het script vanuit een PowerShell-sessie met verhoogde bevoegdheid kunt uitvoeren, anders werkt het niet.

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

Het script kan even duren, afhankelijk van het aantal talen dat u moet installeren.

Nadat het script is uitgevoerd, controleert u of de taalpakketten correct zijn geïnstalleerd door naar **Start**  >  **Settings**  >  **Time & Language** Language te  >  **gaan.** Als de taalbestanden er zijn, bent u klaar.

Nadat u extra talen aan de Windows-afbeelding hebt toegevoegd, moeten de Postvak IN-apps ook worden bijgewerkt om de toegevoegde talen te ondersteunen. U kunt dit doen door de vooraf geïnstalleerde apps te vernieuwen met de inhoud van de ISO van de Postvak IN-apps. Als u deze vernieuwing wilt uitvoeren in een niet-verbonden omgeving (geen toegang tot internet vanaf de VM mogelijk), kunt u het volgende PowerShell-voorbeeldscript gebruiken om het proces te automatiseren.

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
>De Postvak IN-apps die zijn opgenomen in de ISO zijn niet de nieuwste versies van de vooraf geïnstalleerde Windows-apps. Als u de nieuwste versie van alle apps wilt downloaden, moet u de apps bijwerken met de Windows Store-app en handmatig zoeken naar updates nadat u de extra talen hebt geïnstalleerd.

Wanneer u klaar bent, moet u ervoor zorgen dat de verbinding met de share wordt verbroken.

## <a name="finish-customizing-your-image"></a>Het aanpassen van uw afbeelding voltooien

Nadat u de taalpakketten hebt geïnstalleerd, kunt u alle andere software installeren die u wilt toevoegen aan uw aangepaste installatie afbeelding.

Wanneer u klaar bent met het aanpassen van uw installatie afbeelding, moet u het hulpprogramma voor systeemvoorbereiding (sysprep) uitvoeren.

Sysprep uitvoeren:

1. Open een opdrachtprompt met verhoogde opdracht en voer de volgende opdracht uit om de afbeelding te generaliseren:  
   
     ```cmd
     C:\Windows\System32\Sysprep\sysprep.exe /oobe /generalize /shutdown
     ```

2. Stop de VM en leg deze vast in een beheerde afbeelding door de instructies te volgen in [Create a managed image of a generalized VM in Azure](../virtual-machines/windows/capture-image-resource.md)(Een beheerde VM maken in Azure).

3. U kunt nu de aangepaste afbeelding gebruiken om een Windows Virtual Desktop te implementeren. Zie Zelfstudie: Een hostgroep maken met de Azure Portal voor meer informatie [over het implementeren Azure Portal.](create-host-pools-azure-marketplace.md)

## <a name="enable-languages-in-windows-settings-app"></a>Talen inschakelen in de app Windows-instellingen

Nadat u ten slotte de hostgroep hebt geïmplementeerd, moet u de taal toevoegen aan de taallijst van elke gebruiker, zodat deze de voorkeurstaal kan selecteren in het menu Instellingen.

Om ervoor te zorgen dat uw gebruikers de talen kunnen selecteren die u hebt geïnstalleerd, moet u zich aanmelden als de gebruiker en vervolgens de volgende PowerShell-cmdlet uitvoeren om de geïnstalleerde taalpakketten toe te voegen aan het menu Talen. U kunt dit script ook instellen als een geautomatiseerde taak of aanmeldingsscript dat wordt geactiveerd wanneer de gebruiker zich bij de sessie meldt.

```powershell
$LanguageList = Get-WinUserLanguageList
$LanguageList.Add("es-es")
$LanguageList.Add("fr-fr")
$LanguageList.Add("zh-cn")
Set-WinUserLanguageList $LanguageList -force
```

Nadat een gebruiker de taalinstellingen heeft gewijzigd, moet deze zich bij de Windows Virtual Desktop-sessie aanmelden en zich opnieuw aanmelden om de wijzigingen door te voeren. 

## <a name="next-steps"></a>Volgende stappen

Zie Adding [language packs in Windows 10, version 1803 and later versions: Known issues](/windows-hardware/manufacture/desktop/language-packs-known-issue)(Taalpakketten toevoegen in Windows 10, versie 1803 en latere versies: Bekende problemen) als u meer wilt weten over bekende problemen met taalpakketten.

Als u nog andere vragen hebt over een Windows 10 Enterprise sessie, raadpleegt u onze [veelgestelde vragen.](windows-10-multisession-faq.yml)
