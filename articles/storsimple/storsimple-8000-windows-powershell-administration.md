---
title: Power shell voor StorSimple-Apparaatbeheer
description: Meer informatie over het gebruik van Windows PowerShell voor StorSimple voor het beheren van uw StorSimple-apparaat.
author: alkohli
ms.service: storsimple
ms.topic: how-to
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: 65e9657c3948d8ce5883cd33ca8720f501352105
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95995421"
---
# <a name="use-windows-powershell-for-storsimple-to-administer-your-device"></a>Een apparaat beheren met Windows PowerShell voor StorSimple

## <a name="overview"></a>Overzicht

Windows PowerShell voor StorSimple biedt een opdracht regel interface die u kunt gebruiken om uw Microsoft Azure StorSimple-apparaat te beheren. Zoals de naam suggereert, is dit een op Windows Power shell gebaseerde opdracht regel interface die is ingebouwd in een beperkte runs Pace. Vanuit het perspectief van de gebruiker op de opdracht regel wordt een beperkte runs Pace weer gegeven als een niet-toegankelijke versie van Windows Power shell. Met het behoud van enkele van de basis mogelijkheden van Windows Power shell beschikt deze interface over extra specifieke cmdlets die zijn gericht op het beheer van uw Microsoft Azure StorSimple-apparaat.

In dit artikel worden de Windows PowerShell voor StorSimple functies beschreven, met inbegrip van de manier waarop u verbinding kunt maken met deze interface en koppelingen bevat naar stapsgewijze procedures of werk stromen die u kunt uitvoeren met deze interface. De werk stromen bevatten het registreren van uw apparaat, het configureren van de netwerk interface op het apparaat, het installeren van updates waarvoor het apparaat in de onderhouds modus moet zijn, de status van het apparaat wijzigen en eventuele problemen oplossen die zich kunnen voordoen.

Na het lezen van dit artikel kunt u het volgende doen:

* Maak verbinding met uw StorSimple-apparaat met behulp van Windows PowerShell voor StorSimple.
* Beheer uw StorSimple-apparaat met behulp van Windows PowerShell voor StorSimple.
* Hulp krijgen in Windows PowerShell voor StorSimple.

> [!NOTE]
> * Met Windows PowerShell voor StorSimple-cmdlets kunt u uw StorSimple-apparaat vanuit een seriële console of extern beheren via Windows Power shell Remoting. Voor meer informatie over elk van de afzonderlijke cmdlets die in deze interface kunnen worden gebruikt, gaat u naar de [cmdlet-naslag voor Windows PowerShell voor StorSimple](/powershell/module/hcs/?viewFallbackFrom=winserverr2-ps).
> * De Azure PowerShell StorSimple-cmdlets zijn een andere verzameling cmdlets waarmee u StorSimple service-en migratie taken kunt automatiseren vanaf de opdracht regel. Voor meer informatie over de Azure PowerShell-cmdlets voor StorSimple gaat u naar de naslag informatie voor de [Azure StorSimple-cmdlet](/powershell/module/servicemanagement/azure.service/?view=azuresmps-4.0.0&viewFallbackFrom=azuresmps-3.7.0#azure).


U kunt de Windows PowerShell voor StorSimple met een van de volgende methoden openen:

* [Verbinding maken met de seriële console van het StorSimple-apparaat](#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console)
* [Extern verbinding maken met StorSimple met behulp van Windows Power shell](#connect-remotely-to-storsimple-using-windows-powershell-for-storsimple)

## <a name="connect-to-windows-powershell-for-storsimple-via-the-device-serial-console"></a>Verbinding maken met Windows PowerShell voor StorSimple via de seriële console van het apparaat

U kunt [putty](https://www.putty.org/) -of soort gelijke terminal emulatie software downloaden om verbinding te maken met Windows PowerShell voor StorSimple. U moet PuTTy specifiek configureren voor toegang tot het Microsoft Azure StorSimple apparaat. De volgende onderwerpen bevatten gedetailleerde stappen voor het configureren van PuTTy en het maken van een verbinding met het apparaat. Diverse menu opties in de seriële console worden ook uitgelegd.

### <a name="putty-settings"></a>Instellingen voor PuTTY

Zorg ervoor dat u de volgende PuTTy-instellingen gebruikt om vanuit de seriële console verbinding te maken met de Windows Power shell-interface.

#### <a name="to-configure-putty"></a>PuTTy configureren

1. Selecteer in het dialoog venster PuTTy opnieuw **configureren** in het deel venster **categorie** de optie **toetsen bord**.
2. Zorg ervoor dat de volgende opties zijn geselecteerd (dit zijn de standaard instellingen wanneer u een nieuwe sessie start).
   
   | Toetsenbord item | Selecteer |
   | --- | --- |
   | Backspace-toets |Control-? (127) |
   | Home-en end-sleutels |Standard |
   | Functie toetsen en toetsen blok |ESC [n ~ |
   | Begin status van cursor sleutels |Normaal |
   | Begin status van het numerieke toetsen blok |Normaal |
   | Extra toetsenbord functies inschakelen |Control-Alt wijkt af van AltGr |
   
    ![Ondersteunde putty-instellingen](./media/storsimple-windows-powershell-administration/IC740877.png)
3. Klik op **Toepassen**.
4. Selecteer in het deel venster **categorie** de optie **vertaling**.
5. Selecteer **UTF-8** in het vak lijst met **externe teken sets** .
6. Selecteer bij **verwerking van regel teken tekens** **gebruik Unicode-lijn teken code punten**. Op de volgende scherm afbeelding ziet u de juiste selecties voor PuTTy.
   
    ![Instellingen voor UTF-putty](./media/storsimple-windows-powershell-administration/IC740878.png)
7. Klik op **Toepassen**.

U kunt nu PuTTy gebruiken om verbinding te maken met de seriële console van het apparaat door de volgende stappen uit te voeren.

[!INCLUDE [storsimple-use-putty](../../includes/storsimple-use-putty.md)]

### <a name="about-the-serial-console"></a>Over de seriële console

Wanneer u via de seriële console toegang hebt tot de Windows Power shell-interface van uw StorSimple-apparaat, wordt er een banner bericht weer gegeven, gevolgd door menu opties.

Het banner bericht bevat basis informatie over StorSimple, zoals het model, de naam, de geïnstalleerde software versie en de status van de controller die u wilt openen. In de volgende afbeelding ziet u een voor beeld van een banner bericht.

![Serie banner bericht](./media/storsimple-windows-powershell-administration/IC741098.png)

> [!IMPORTANT]
> U kunt het banner bericht gebruiken om te bepalen of de controller waarmee u verbonden bent, _actief_ of _passief_ is.

In de volgende afbeelding ziet u de verschillende runs Pace-opties die beschikbaar zijn in het menu van de seriële console.

![Uw apparaat registreren 2](./media/storsimple-windows-powershell-administration/IC740906.png)

U kunt kiezen uit de volgende instellingen:

1. **Aanmelden met volledige toegang** Met deze optie kunt u verbinding maken (met de juiste referenties) naar de **SSAdminConsole** runs Pace op de lokale controller. (De lokale controller is de controller waartoe u momenteel toegang hebt via de seriële console van uw StorSimple-apparaat.) Deze optie kan ook worden gebruikt om Microsoft Ondersteuning toegang te geven tot een onbeperkte runs Pace (een ondersteunings sessie) om mogelijke problemen met het apparaat op te lossen. Nadat u optie 1 hebt gebruikt om u aan te melden, kunt u de Microsoft Ondersteuning-Engineer toegang geven tot een onbeperkte runs Pace door een specifieke cmdlet uit te voeren. Zie [een ondersteunings sessie starten](storsimple-8000-contact-microsoft-support.md#start-a-support-session-in-windows-powershell-for-storsimple)voor meer informatie.
   
2. **Meld u aan bij peer controller met volledige toegang** Deze optie is hetzelfde als optie 1, behalve dat u verbinding kunt maken (met de juiste referenties) naar de **SSAdminConsole** runs Pace op de peer controller. Omdat het StorSimple-apparaat een apparaat met hoge Beschik baarheid is met twee controllers in een actief-passieve configuratie, verwijst peer naar de andere controller in het apparaat dat u via de seriële console hebt geopend.
   Net als bij optie 1 kan deze optie ook worden gebruikt om Microsoft Ondersteuning toegang te geven tot onbegrensde runs Pace op een peer controller.

3. **Verbinding maken met beperkte toegang** Deze optie wordt gebruikt om toegang te krijgen tot de Windows Power shell-interface in de beperkte modus. U wordt niet gevraagd om toegangs referenties. Deze optie maakt verbinding met een meer beperkte runs Pace vergeleken met de opties 1 en 2.  Enkele van de taken die beschikbaar zijn via optie 1 en **kunnen niet* worden uitgevoerd in deze runs Pace:
   
   * Herstellen naar de fabrieks instellingen
   * Het wacht woord wijzigen
   * Ondersteunings toegang in-of uitschakelen
   * Updates toepassen
   * Hotfixes installeren

     > [!NOTE]
     > Dit is de voorkeurs optie als u het beheerders wachtwoord van het apparaat verg eten bent en u geen verbinding kunt maken via optie 1 of 2.

4. **Taal wijzigen** Met deze optie kunt u de weergave taal van de Windows Power shell-interface wijzigen. De ondersteunde talen zijn Engels, Japans, Russisch, Frans, Zuid-Koreaans, Spaans, Italiaans, Duits, Chinees en Portugees.

## <a name="connect-remotely-to-storsimple-using-windows-powershell-for-storsimple"></a>Extern verbinding maken met StorSimple met behulp van Windows PowerShell voor StorSimple

U kunt Windows Power shell Remoting gebruiken om verbinding te maken met uw StorSimple-apparaat. Wanneer u op deze manier verbinding maakt, wordt er geen menu weer geven. (U ziet een menu alleen als u de seriële console op het apparaat gebruikt om verbinding te maken. Als u extern verbinding maakt, gaat u rechtstreeks naar het equivalent van ' optie 1 – volledige toegang ' op de seriële console.) Met Windows Power shell Remoting maakt u verbinding met een specifieke runs Pace. U kunt ook de weergave taal opgeven.

De weergave taal is onafhankelijk van de taal die u hebt ingesteld met behulp van de optie **taal wijzigen** in het menu van de seriële console. De land instelling van het apparaat waarmee u verbinding maakt, wordt automatisch opgehaald als er geen is opgegeven.

> [!NOTE]
> Als u werkt met Microsoft Azure virtuele hosts en StorSimple-Cloud apparaten, kunt u externe toegang tot Windows Power shell en de virtuele host gebruiken om verbinding te maken met het Cloud apparaat. Als u een share locatie hebt ingesteld op de host waarop u informatie van de Windows Power shell-sessie wilt opslaan, moet u er rekening mee houden dat de principal _iedereen_ alleen geverifieerde gebruikers bevat. Als u de share zo instelt dat toegang door _iedereen_ wordt toegestaan en u verbinding maakt zonder referenties op te geven, wordt de niet-geverifieerde anonieme Principal gebruikt en wordt er een fout melding weer geven. Als u dit probleem wilt oplossen, moet u op de share-host de gast account inschakelen en vervolgens de gast account volledige toegang geven tot de share of moet u geldige referenties opgeven, samen met de Windows Power shell-cmdlet.


U kunt HTTP of HTTPS gebruiken om verbinding te maken via externe communicatie met Windows Power shell. Volg de instructies in de volgende zelf studies:

* [Extern verbinding maken via HTTP](storsimple-8000-remote-connect.md#connect-through-http)
* [Extern verbinding maken met behulp van HTTPS](storsimple-8000-remote-connect.md#connect-through-https)

## <a name="connection-security-considerations"></a>Beveiligings overwegingen voor de verbinding

Houd rekening met het volgende wanneer u beslist hoe u verbinding maakt met Windows PowerShell voor StorSimple:

* Rechtstreeks verbinding maken met de seriële console van het apparaat is beveiligd, maar het maken van verbinding met de seriële console via netwerk switches is niet. Wees voorzichtig met het beveiligings risico wanneer u verbinding maakt met het apparaat via netwerk switches.
* Het maken van een verbinding via een HTTP-sessie kan meer beveiliging bieden dan via het netwerk verbinding maken via de seriële console. Hoewel dit niet de veiligste methode is, is het acceptabel voor vertrouwde netwerken.
* Het maken van een verbinding via een HTTPS-sessie is de veiligste en aanbevolen optie.

## <a name="administer-your-storsimple-device-using-windows-powershell-for-storsimple"></a>Uw StorSimple-apparaat beheren met Windows PowerShell voor StorSimple

De volgende tabel bevat een samen vatting van alle algemene beheer taken en complexe werk stromen die kunnen worden uitgevoerd in de Windows Power shell-interface van uw StorSimple-apparaat. Klik op de juiste vermelding in de tabel voor meer informatie over elke werk stroom.

#### <a name="windows-powershell-for-storsimple-workflows"></a>Windows PowerShell voor StorSimple werk stromen

| Als u dit wilt doen... | Gebruik deze procedure. |
| --- | --- |
| Uw apparaat registreren |[Het apparaat configureren en registreren met behulp van Windows PowerShell voor StorSimple](storsimple-8000-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple) |
| Webproxy configureren</br>Webproxy-instellingen weer geven |[Webproxy configureren voor uw StorSimple-apparaat](storsimple-8000-configure-web-proxy.md) |
| DATA 0-netwerk interface-instellingen op het apparaat wijzigen |[DATA 0-netwerk interface wijzigen voor uw StorSimple-apparaat](storsimple-8000-modify-data-0.md) |
| Een controller stoppen </br> Een controller opnieuw opstarten of afsluiten </br> Een apparaat afsluiten</br>Het apparaat terugzetten naar de fabrieksinstellingen |[Apparaatbeheer beheren](storsimple-8000-manage-device-controller.md) |
| Updates en hotfixes voor onderhouds modus installeren |[Uw apparaat bijwerken](storsimple-update-device.md) |
| Onderhouds modus activeren </br>Onderhouds modus afsluiten |[StorSimple](storsimple-8000-device-modes.md) |
| Een ondersteunings pakket maken</br>Een ondersteunings pakket ontsleutelen en bewerken |[Een ondersteunings pakket maken en beheren](storsimple-8000-create-manage-support-package.md) |
| Een ondersteunings sessie starten</br> |[Een ondersteunings sessie starten in Windows PowerShell voor StorSimple](storsimple-8000-create-manage-support-package.md#create-a-support-package) |

## <a name="get-help-in-windows-powershell-for-storsimple"></a>Hulp krijgen in Windows PowerShell voor StorSimple

In Windows PowerShell voor StorSimple is cmdlet Help beschikbaar. Er is ook een up-to-date versie van deze Help beschikbaar, die u kunt gebruiken om de Help op uw systeem bij te werken.

Hulp krijgen in deze interface is vergelijkbaar met die in Windows Power shell en de meeste Help-gerelateerde cmdlets werken. U vindt Help voor Windows Power shell online: [micro soft. Power shell. core](/powershell/module/Microsoft.PowerShell.Core/).

Hier volgt een korte beschrijving van de Help-informatie voor deze Windows Power shell-interface, inclusief informatie over het bijwerken van de Help.

### <a name="to-get-help-for-a-cmdlet"></a>Help voor een cmdlet verkrijgen

* Gebruik de volgende opdracht om hulp te krijgen voor cmdlets of functies: `Get-Help <cmdlet-name>`
* Als u online-Help voor een cmdlet wilt krijgen, gebruikt u de vorige cmdlet met de `-Online` para meter: `Get-Help <cmdlet-name> -Online`
* Voor volledige ondersteuning kunt u de `–Full` para meter gebruiken en voor beelden gebruiken de `–Examples` para meter.

### <a name="to-update-help"></a>Help bijwerken

U kunt de Help eenvoudig bijwerken in de Windows Power shell-interface. Voer de volgende stappen uit om de Help op uw systeem bij te werken.

#### <a name="to-update-cmdlet-help"></a>Help voor cmdlets bijwerken
1. Start Windows Power shell met de optie **als administrator uitvoeren** .
2. Typ het volgende achter de opdracht prompt:  `Update-Help`
3. De bijgewerkte Help-bestanden worden geïnstalleerd.
4. Nadat de Help-bestanden zijn geïnstalleerd, typt u: `Get-Help Get-Command` . Hiermee wordt een lijst met cmdlets weer gegeven waarvoor Help beschikbaar is.

> [!NOTE]
> Als u een lijst wilt weer geven met alle beschik bare cmdlets in een runs Pace, meldt u zich aan bij de bijbehorende menu optie en voert u de `Get-Command` cmdlet uit.


## <a name="next-steps"></a>Volgende stappen

Als u problemen ondervindt met uw StorSimple-apparaat tijdens het uitvoeren van een van de bovenstaande werk stromen, raadpleegt u de [Hulpprogram ma's voor het oplossen van problemen met StorSimple-implementaties](storsimple-8000-troubleshoot-deployment.md#tools-for-troubleshooting-storsimple-deployments).