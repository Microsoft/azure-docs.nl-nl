---
title: Wacht woorden importeren in de Microsoft Authenticator-app-Azure AD
description: Wacht woorden importeren in de micro soft-verificatie-app van populaire wachtwoord beheerders.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 01/28/2021
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: ecc6580148dfba92077336a26ff9160fbe88eb2c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99806152"
---
# <a name="import-passwords-into-the-microsoft-authenticator-app"></a>Wacht woorden importeren in de app Microsoft Authenticator

Microsoft Authenticator ondersteunt het importeren van wacht woorden van Google Chrome, Firefox, LastPass, Bitwarden en RoboForm. Als micro soft uw bestaande wachtwoord beheer momenteel niet ondersteunt, kunt u [hand matig aanmeldings referenties invoeren in het CSV](https://go.microsoft.com/fwlink/?linkid=2134938)-bestand van de sjabloon. Als u uw bestaande wacht woorden wilt importeren en deze wilt beheren in de verificator-app, exporteert u uw wacht woorden vanuit uw bestaande wachtwoord beheerder naar onze CSV-indeling (door lijst scheidings tekens gescheiden waarden). Importeer vervolgens het geëxporteerde CSV-bestand naar de verificator in de Chrome-browser uitbreiding of rechtstreeks naar de verificator-app (Android en iOS).

## <a name="import-from-google-chrome-or-android-smart-lock"></a>Importeren uit Google Chrome-of Android-Smart Lock

U kunt uw wacht woorden van Google Chrome of Android Smart Lock naar verificator importeren op uw smartphone of op uw desktop computer. U kunt:

- [Importeren vanuit Chrome op Android en iOS](#import-from-chrome-on-android-and-ios)
- [Importeren vanuit Chrome desktop browser](#import-from-chrome-desktop-browser)

### <a name="import-from-chrome-on-android-and-ios"></a>Importeren vanuit Chrome op Android en iOS

Google Chrome-gebruikers op Android-en Apple-telefoons kunnen hun wacht woord rechtstreeks vanuit hun telefoon importeren met een paar eenvoudige stappen.

1. Installeer de verificator-app op uw telefoon en open het tabblad **wacht woorden** .

1. Meld u aan bij Google Chrome op uw telefoon.

1. Tik ![ op het menu Google Chrome ellips rechtsboven ](./media/user-help-authenticator-app-import-passwords/ellipsis-chrome.png) voor Android-telefoons of rechtsonder op Ios-apparaten en tik vervolgens op **instellingen.**

   Platform | Koppeling
   ---------- | --------
   Android | ![Menu locatie voor Google Chrome-instellingen](./media/user-help-authenticator-app-import-passwords/android-settings-menu.png)
   iOS | ![Menu pictogram Google Chrome-instellingen](./media/user-help-authenticator-app-import-passwords/apple-settings-menu.png)

1. Open **wacht woorden** in **instellingen**.

   Platform | Koppeling
   ---------- | --------
   Android | ![Locatie van de opdracht Andoid Chrome-wacht woorden](./media/user-help-authenticator-app-import-passwords/android-passwords-location.png)
   iOS | ![Opdracht locatie voor Apple Chrome-wacht woorden](./media/user-help-authenticator-app-import-passwords/apple-passwords-location.png)

1. Op Android-apparaten tikt u op het ![ menu Google Chrome ellips rechtsboven ](./media/user-help-authenticator-app-import-passwords/ellipsis-chrome.png) voor Android-telefoons of rechtsonder op Ios-apparaten en tikt u vervolgens op **wacht woorden exporteren**.

   Platform | Koppeling
   ---------- | --------
   Android | ![Locatie voor exporteren van Android Chrome-wacht woorden](./media/user-help-authenticator-app-import-passwords/android-export-passwords-location.png)
   iOS | ![Locatie van Apple Chrome-export wachtwoorden](./media/user-help-authenticator-app-import-passwords/apple-export-passwords-location.png)

   U moet een pincode, vinger afdruk of gezichts herkenning opgeven. Bevestig uw identiteit en tik nogmaals op **wacht woorden exporteren** om te exporteren.

1. Nadat de wacht woorden zijn geëxporteerd, wordt u door Chrome gevraagd om te kiezen in welke app u wilt importeren. Selecteer **verificator** om te beginnen met het importeren van wacht woorden. U wordt op de hoogte gesteld van de import status wanneer deze is voltooid.

   Platform | Koppeling
   ---------- | --------
   Android | ![Locatie van wacht woorden voor Android Chrome importeren](./media/user-help-authenticator-app-import-passwords/android-chrome-import.png)
   iOS | ![Locatie van Apple Chrome import-wacht woorden](./media/user-help-authenticator-app-import-passwords/apple-chrome-import.png)

### <a name="import-from-chrome-desktop-browser"></a>Importeren vanuit Chrome desktop browser

Voordat u begint, moet u de [micro soft-uitbrei ding voor automatisch door voeren](https://chrome.google.com/webstore/detail/microsoft-autofill/fiedbfgcleddlbcmgdigjgdfcggjcion) installeren en u aanmelden in uw Chrome-browser.

1. Open [Google-wachtwoord beheer](https://passwords.google.com) in een browser. Als u dat nog niet hebt gedaan, meldt u zich aan bij uw Google-account.

1. Het tandwiel pictogram selecteren ![Tandwiel pictogram voor desktop wachtwoord beheer](./media/user-help-authenticator-app-import-passwords/desktop-password-manager-gear.png) om te openen op de pagina wachtwoord instellingen.

1. Selecteer **exporteren** en selecteer vervolgens op de volgende pagina de optie **exporteren** opnieuw om uw wacht woord te exporteren. Geef uw Google-wacht woord op wanneer u wordt gevraagd uw identiteit te bevestigen. U wordt op de hoogte gesteld van de import status wanneer deze is voltooid.

   ![De opdracht locatie voor exporteren van Desktop Chrome browser-wacht woorden](./media/user-help-authenticator-app-import-passwords/desktop-chrome-export-passwords-location.png)

1. Open de uitbrei ding Chrome voor automatisch aanvullen en selecteer **instellingen**.

   ![Locatie van uitbrei ding van uitbreidings instellingen voor desktop Chrome browser](./media/user-help-authenticator-app-import-passwords/desktop-chrome-autofill-settings.png)

1. Selecteer **gegevens importeren** om een dialoog venster te openen. Selecteer vervolgens **bestand kiezen** om het CSV-bestand te zoeken en te importeren.

   ![CSV-locatie voor het importeren van gegevens in de Blade Desktop Chrome](./media/user-help-authenticator-app-import-passwords/desktop-chrome-import-csv.png)

## <a name="import-from-firefox"></a>Importeren vanuit Firefox

In Firefox kunnen alleen wacht woorden van de bureaublad browser worden geëxporteerd. Zorg er dus voor dat u toegang hebt tot de Firefox-desktop browser voordat u wacht woorden importeert uit Firefox.

1. Meld u aan bij de nieuwste versie van Firefox op uw bureau blad en selecteer de ![Het menu Hamburger van Firefox](./media/user-help-authenticator-app-import-passwords/desktop-firefox-ellipsis-icon.png) menu vanaf de rechter bovenhoek van het scherm.

1. Selecteer **aanmeldingen en wacht woorden**.

   ![Aanmeld gegevens van bureau blad Firefox-browser en locatie van wacht woorden](./media/user-help-authenticator-app-import-passwords/desktop-firefox-passwords-location.png)

1. Selecteer op de pagina Firefox-Lockwise het ![ menu menu van Firefox-beletsel tekens ](./media/user-help-authenticator-app-import-passwords/desktop-firefox-ellipsis-icon.png) , selecteer **aanmeldingen exporteren** en bevestig uw intentie door **exporteren** te selecteren. U wordt gevraagd uzelf te identificeren door uw pincode, apparaatwachtwoord of wacht woord in te voeren of door uw vinger afdrukken te scannen. Zodra de detectie is gelukt, exporteert Firefox uw wacht woorden in CSV-indeling naar de geselecteerde locatie.

   ![Locatie van Desktop Firefox-browser voor exporteren van wacht woorden](./media/user-help-authenticator-app-import-passwords/desktop-firefox-export-passwords-location.png)

1. U kunt uw wacht woorden importeren in verificator vanuit een desktop browser of op iOS-of Android-telefoons. Importeren naar de verificator-app op uw telefoon:

      1. U kunt het geëxporteerde CSV-bestand op uw Android-of iOS-telefoon overdragen met behulp van de voor keur en de veilige manier en het vervolgens downloaden. Vervolgens deelt u het CSV-bestand met de verificator-app om het importeren te starten.

         Platform | Koppeling
         ---------- | --------
         Android | ![Locatie van wacht woorden voor Android Chrome importeren](./media/user-help-authenticator-app-import-passwords/android-chrome-import.png)
         iOS | ![Locatie van Apple Chrome import-wacht woorden](./media/user-help-authenticator-app-import-passwords/apple-chrome-import.png)

      1. Nadat u uw wacht woord hebt geïmporteerd in verificator, verwijdert u het CSV-bestand van uw bureau blad of mobiele telefoon.

## <a name="import-from-lastpass"></a>Importeren uit LastPass

LastPass biedt alleen ondersteuning voor het exporteren van wacht woorden vanuit een bureaublad browser. Zorg er dus voor dat u toegang hebt tot een bureaublad browser voordat u begint met het importeren van wacht woorden.

1. Meld u aan bij [de LastPass-website](https://lastpass.com) en selecteer **Geavanceerde opties** en selecteer vervolgens **exporteren**.

   ![Locatie van LastPass voor exporteren van Desktop-wacht woorden](./media/user-help-authenticator-app-import-passwords/desktop-lastpass-export-passwords-location.png)

1. Identificeer uzelf wanneer u hierom wordt gevraagd door uw hoofd wachtwoord op te geven. Daarna ziet u de geëxporteerde wacht woorden op de webpagina.

1. Kopieer de inhoud van de webpagina.

1. Open Klad blok (of uw favoriete tekst editor) en plak de gekopieerde inhoud.

1. Sla dit Klad blok-bestand op door **bestand** &gt; **Opslaan als** te selecteren. Geef een naam op die eindigt op '. csv ' (zoals LastPass.csv) op een veilige locatie in het bureau blad.

   ![LastPass op Desktop opslaan CSV-bestand](./media/user-help-authenticator-app-import-passwords/desktop-lastpass-save-import-file.png)

1. U kunt uw wacht woorden importeren in verificator in een bureaublad browser of op iOS-of Android-telefoons. Importeren naar de verificator-app op uw telefoon:

      1. Zet het geëxporteerde CSV-bestand op uw smartphone door met een voor keur en een veilige manier en down load het. Deel vervolgens het CSV-bestand met de verificator-app om het importeren te starten.

         Platform | Koppeling
         ---------- | --------
         Android | ![Locatie van import wachtwoorden voor Android LastPass](./media/user-help-authenticator-app-import-passwords/android-chrome-import.png)
         iOS | ![Locatie van Apple LastPass-import wachtwoorden](./media/user-help-authenticator-app-import-passwords/apple-chrome-import.png)

      1. Nadat u uw wacht woord hebt geïmporteerd in verificator, verwijdert u het CSV-bestand van uw bureau blad of mobiele telefoon.

## <a name="import-from-bitwarden"></a>Importeren uit Bitwarden

Bitwarden biedt alleen ondersteuning voor het exporteren van wacht woorden vanuit een bureaublad browser. Zorg er dus voor dat u toegang hebt tot een bureaublad browser voordat u begint met het importeren van wacht woorden.

1. Meld u aan bij https://vault.bitwarden.com/ en selecteer **hulpprogram ma's** &gt; **exporteren kluis**. Kies de bestands indeling CSV, geef uw hoofd wachtwoord op en selecteer vervolgens **kluis exporteren** om te beginnen met exporteren.

   ![Locatie van Bitwarden-export kluis](./media/user-help-authenticator-app-import-passwords/desktop-bitwarden-export-command-location.png)

1. U kunt uw wacht woorden importeren in verificator in een bureaublad browser of op iOS-of Android-telefoons. Importeren naar de verificator-app op uw telefoon:

      1. Zet het geëxporteerde CSV-bestand op uw smartphone door met een voor keur en een veilige manier en down load het. Deel vervolgens het CSV-bestand met de verificator-app om het importeren te starten.

         Platform | Koppeling
         ---------- | --------
         Android | ![Locatie van import wachtwoorden voor Android Bitwarden](./media/user-help-authenticator-app-import-passwords/android-chrome-import.png)
         iOS | ![Locatie van Apple Bitwarden-import wachtwoorden](./media/user-help-authenticator-app-import-passwords/apple-chrome-import.png)

      1. Nadat u uw wacht woord hebt geïmporteerd in verificator, verwijdert u het CSV-bestand van uw bureau blad of mobiele telefoon.

## <a name="import-from-roboform"></a>Importeren uit RoboForm

RoboForm staat alleen het exporteren van wacht woorden uit de desktop-app toe, dus zorg ervoor dat u toegang hebt tot de RoboForm-app op een bureau blad voordat u begint met het importeren.

1. Start RoboForm vanaf uw desktop-client en meld u aan bij uw account.

1. Selecteer **Opties** in het menu **RoboForm** .

   ![Menu opties bureau blad RoboForm](./media/user-help-authenticator-app-import-passwords/desktop-roboform-options.png)

1. Selecteer **account & gegevens** &gt; **export**.

   ![Locatie van de RoboForm-export opdracht voor desktop](./media/user-help-authenticator-app-import-passwords/desktop-roboform-accounts-data.png)

1. Kies een veilige locatie om het geëxporteerde bestand op te slaan. Selecteer **aanmeldingen** als het **gegevens** type en selecteer het CSV-bestand als indeling en selecteer vervolgens **exporteren**.

   ![Het dialoog venster RoboForm exporteren](./media/user-help-authenticator-app-import-passwords/desktop-roboform-export-dialog.png)

1. Controleer of uw intentie en het CSV-bestand worden geëxporteerd naar de geselecteerde locatie.

   ![Bevestigings dialoogvenster voor desktop RoboForm-export](./media/user-help-authenticator-app-import-passwords/desktop-roboform-confirmation.png)

1. U kunt uw wacht woorden importeren in verificator in een bureaublad browser of op iOS-of Android-telefoons. Importeren naar de verificator-app op uw telefoon:

      1. Zet het geëxporteerde CSV-bestand op uw smartphone door met een voor keur en een veilige manier en down load het. Deel vervolgens het CSV-bestand met de verificator-app om het importeren te starten.

         Platform | Koppeling
         ---------- | --------
         Android | ![Locatie van import wachtwoorden voor Android RoboForm](./media/user-help-authenticator-app-import-passwords/android-chrome-import.png)
         iOS | ![Locatie van Apple RoboForm-import wachtwoorden](./media/user-help-authenticator-app-import-passwords/apple-chrome-import.png)

      1. Nadat u uw wacht woord hebt geïmporteerd in verificator, verwijdert u het CSV-bestand van uw bureau blad of mobiele telefoon.

## <a name="import-by-creating-a-csv"></a>Importeren door een CSV te maken

Als de stappen voor het importeren van wacht woorden uit uw wachtwoord beheerder niet in dit artikel worden vermeld, kunt u een CSV maken die u kunt gebruiken om uw wacht woorden in verificator te importeren. U wordt aangeraden deze stappen op een bureau blad uit te voeren, zodat u ze eenvoudig kunt Format teren.

1. [Down load en open de import sjabloon](https://go.microsoft.com/fwlink/?linkid=2134938)op uw bureau blad. Als u een Apple iPhone, Safari en sleutel keten gebruiker bent, kunt u nu door gaan naar stap 4.

1. Uw wacht woorden exporteren uit uw bestaande wachtwoord beheer in een niet-versleuteld CSV-bestand.

1. Kopieer de relevante kolommen uit het geëxporteerde CSV-bestand naar de sjabloon CSV en sla het bestand op.

1. Als u geen geëxporteerd CSV-bestand hebt, kunt u elke aanmelding vanuit uw bestaande wachtwoord beheerder kopiëren naar het CSV-bestand van de sjabloon. Verwijder of wijzig de veldnamenrij niet. Wanneer u klaar bent, controleert u de integriteit van uw gegevens voordat u begint met de volgende stap.

1. U kunt uw wacht woorden importeren in verificator in een bureaublad browser of op iOS-of Android-telefoons. Importeren naar de verificator-app op uw telefoon:

      1. Zet het geëxporteerde CSV-bestand op uw smartphone door met een voor keur en een veilige manier en down load het. Deel vervolgens het CSV-bestand met de verificator-app om het importeren te starten.

         Platform | Koppeling
         ---------- | --------
         Android | ![Locatie van wacht woorden voor Android CSV-Import](./media/user-help-authenticator-app-import-passwords/android-chrome-import.png)
         iOS | ![Locatie van Apple CSV-Import wachtwoorden](./media/user-help-authenticator-app-import-passwords/apple-chrome-import.png)

      1. Nadat u uw wacht woord hebt geïmporteerd in verificator, verwijdert u het CSV-bestand van uw bureau blad of mobiele telefoon.

## <a name="troubleshooting-steps"></a>Stappen voor probleemoplossing

De meest voorkomende oorzaak van mislukte import bewerkingen is onjuiste opmaak in het CSV-bestand. U kunt de volgende stappen uitvoeren om het probleem op te lossen.

- Raadpleeg dit artikel om te controleren of het importeren van wacht woorden van uw huidige wachtwoord beheerder al wordt ondersteund. Als dit het geval is, kunt u het importeren opnieuw proberen door de stappen te volgen die worden vermeld voor uw provider.

- Als het importeren van de indeling van uw wachtwoord beheer momenteel niet wordt ondersteund, kunt u het opnieuw proberen door [uw CSV-bestand hand matig te maken](#import-by-creating-a-csv).

- U kunt de integriteit van CSV-gegevens controleren met de volgende suggesties:

  - De eerste rij moet een koptekst bevatten met drie kolommen: **URL**, **gebruikers naam** en **wacht woord**.

  - Elke rij moet een waarde bevatten onder de kolommen **URL** en **wacht woord** .

- U kunt de CSV opnieuw maken door uw inhoud in het [CSV-sjabloon bestand](https://go.microsoft.com/fwlink/?linkid=2134938)te plakken.

- Als niets anders werkt, meldt u het probleem met de koppeling **Feedback verzenden** van de verificator-app-instellingen.
