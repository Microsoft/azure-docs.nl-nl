---
title: Beveiligingsgegevens instellen vanaf een aanmeldingsprompt - Azure AD
description: Informatie over het instellen van beveiligingsgegevens voor uw werk- of schoolaccount, als hierom wordt gevraagd op de aanmeldingspagina van uw organisatie.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: rhicock
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 04/07/2020
ms.author: curtand
ms.openlocfilehash: 016e0de26bf3a9513940be161cfecf6d1c2b9d02
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88798496"
---
# <a name="set-up-your-security-info-from-a-sign-in-prompt"></a>Beveiligingsgegevens instellen vanaf een aanmeldingsprompt

U kunt deze stappen volgen als u wordt gevraagd uw beveiligingsgegevens in te stellen, onmiddellijk nadat u zich aanmeldt bij uw werk- of schoolaccount.

U ziet deze prompt alleen als u niet de beveiligingsgegevens hebt ingesteld die zijn vereist door uw organisatie. Als u eerder de beveiligingsgegevens hebt ingesteld, maar u wijzigingen wilt aanbrengen, kunt u de stappen volgen in de verschillende artikelen met uitleg per methode. Zie [Uw overzicht met beveiligingsinformatie toevoegen of bijwerken](./security-info-setup-auth-app.md) voor meer informatie.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

## <a name="security-verification-versus-password-reset-authentication"></a>Beveiligingsverificatie versus verificatie voor het opnieuw instellen van het wachtwoord

Methoden voor beveiligingsgegevens worden zowel gebruikt voor tweeledige beveiligingsverificatie als voor het opnieuw instellen van het wachtwoord. Niet elke methode kan echter voor beide worden gebruikt.

| Methode | Gebruikt voor |
| ------ | -------- |
| Authenticator-app | Tweeledige verificatie en verificatie voor het opnieuw instellen van het wachtwoord. |
| Sms-berichten | Tweeledige verificatie en verificatie voor het opnieuw instellen van het wachtwoord. |
| Telefoonoproep | Tweeledige verificatie en verificatie voor het opnieuw instellen van het wachtwoord. |
| Beveiligingssleutel | Tweeledige verificatie en verificatie voor het opnieuw instellen van het wachtwoord. |
| E-mailaccount | Alleen voor verificatie voor het opnieuw instellen van het wachtwoord. Voor tweeledige verificatie moet u een andere methode kiezen. |
| Beveiligingsvragen | Alleen voor verificatie voor het opnieuw instellen van het wachtwoord. Voor tweeledige verificatie moet u een andere methode kiezen. |

## <a name="sign-in-to-your-work-or-school-account"></a>Meld u aan bij uw werk- of schoolaccount

Nadat u zich bij uw account voor werk of school aanmeldt, ziet u een prompt waarin u meer informatie moet opgeven voordat u toegang tot uw account wordt verleend.

![Vraag om meer informatie](media/security-info/securityinfo-prompt.png)

## <a name="set-up-your-security-info-using-the-wizard"></a>Instellen van uw beveiligingsgegevens met de wizard

Volg deze stappen voor het instellen van uw beveiligingsgegevens voor uw werk- of schoolaccount vanaf de opdrachtprompt.

>[!Important]
>Dit is slechts een voorbeeld van het proces. Afhankelijk van de vereisten van uw organisatie, kan de beheerder andere verificatiemethoden hebben ingesteld die u moet gebruiken tijdens dit proces. In dit voorbeeld hebben we twee methoden verplicht gesteld, de Microsoft Authenticator-app en een mobiel telefoonnummer voor verificatie oproepen of sms-berichten.

1. Nadat u **Volgende** hebt geselecteerd in de opdrachtprompt, wordt de wizard **Houd uw account veilig** weergegeven, de eerste methode die u van uw beheerder en de organisatie moet instellen. Voor dit voorbeeld is dit de Microsoft Authenticator-app.

   > [!Note]
   > Als u een andere verificator-app dan de Microsoft Authenticator-app gebruiken wilt, selecteert u de koppeling **Ik wil een andere verificator-app gebruiken**.
   >
   > Als u van uw organisatie u een andere methode dan de authenticator-app kiezen mag, kunt u de **Ik wil andere methode instellen-koppeling** selecteren.

    ![Wizard Houd uw account veilig met de downloadpagina van de verificator](media/security-info/securityinfo-prompt-get-auth-app.png)

2. Selecteer **Nu downloaden** om de Microsoft Authenticator-app op uw mobiele apparaat te downloaden en installeren en selecteer vervolgens **Volgende**. Zie voor meer informatie over het downloaden en installeren van de app, [De Microsoft Authenticator-app downloaden en installeren](user-help-auth-app-download-install.md).

    ![Wizard Houd uw account veilig met de pagina Uw account instellen van de verificator](media/security-info/securityinfo-prompt-auth-app-setup-acct.png)

3. Blijven op de **Uw account instellen**-pagina tijdens het instellen van de Microsoft Authenticator-app op uw mobiele apparaat.

4. Open de Microsoft Authenticator-app, selecteer dat u meldingen toestaat (als u hierom wordt gevraagd), selecteer **Account toevoegen** uit het pictogram **Aanpassen en controle** in de rechterbovenhoek en selecteer vervolgens **Werk- of schoolaccount**.

    >[!Note]
    >Als dit de eerste keer is dat u de Microsoft Authenticator-app instelt, wordt u mogelijk gevraagd of u de app toegang wilt geven tot uw camera (iOS) of de wilt toestaan foto's te maken en video op te nemen (Android). U moet **Toestaan** selecteren, zodat de Authenticator-app toegang heeft tot uw camera om in de volgende stap een foto te maken van de QR-code. Als u de app geen cameratoegang geeft, kunt u de Authenticator-app wel installeren, maar moet u de codegegevens handmatig toevoegen. Zie [Handmatig een account toevoegen aan de app](user-help-auth-app-add-account-manual.md) voor informatie over het handmatig toevoegen van de code.

5. Ga terug naar de pagina **Uw account instellen** op uw computer en selecteer vervolgens **Volgende**.

    De pagina **QR-code scannen** wordt weergegeven.

    ![De QR-code scannen met behulp van de Authenticator-app](media/security-info/securityinfo-prompt-auth-app-qrcode.png)

6. Scan de weergegeven code met de QR-codelezer van de Microsoft Authenticator-app die op uw mobiele apparaat wordt weergegeven nadat u uw werk of school-account hebt gemaakt in stap 5.

    De verificator-app moet nu uw werk of schoolaccount toevoegen zonder aanvullende informatie van u. Als de QR-codelezer de code echter niet kan lezen, kunt u **Kan de QR-afbeelding niet scannen** selecteren en de code en URL handmatig invoeren in de Microsoft Authenticator-app. Zie voor meer informatie over het handmatig toevoegen van een code [Handmatig een account toevoegen aan de app](user-help-auth-app-add-account-manual.md).

7. Selecteer **Volgende** op de pagina **QR-code scannen** op uw computer.

    Een melding wordt verzonden naar de Microsoft Authenticator-app op uw mobiele apparaat voor het testen van uw account.

    ![Uw account met de verificator-app testen](media/security-info/securityinfo-prompt-test-app.png)

8. Keur de melding in de Microsoft Authenticator-app goed en selecteer vervolgens **Volgende**.

    ![Melding van geslaagd, met verbinding van de app en uw account](media/security-info/securityinfo-prompt-auth-app-success.png)

    Uw beveiligingsgegevens zijn bijgewerkt voor het standaard gebruik van de Microsoft Authenticator-app om uw identiteit te verifiëren bij het gebruik van verificatie in twee stappen of opnieuw instellen van het wachtwoord.

9. Op de pagina **Telefoon** instellen, kiest u of u een sms-bericht of een telefonische oproep wilt ontvangen, en selecteert vervolgens **Volgende**. Voor de doeleinden van dit voorbeeld gebruiken we sms-berichten, zodat u een telefoonnummer moet gebruiken voor een apparaat dat sms-berichten kan accepteren.

    ![Begin met het instellen van uw telefoonnummer voor de sms-berichten verzenden](media/security-info/securityinfo-prompt-text-msg.png)

    Een sms-bericht is verzonden naar uw telefoonnummer. Als liever een telefoongesprek krijgt, is het proces hetzelfde. U zult echter een telefonische oproep met instructies in plaats van een sms-bericht ontvangen.

10. Voer de code die is geleverd door het sms-bericht aan uw mobiele apparaat en selecteer vervolgens **Volgende**.

    ![Test uw account met het sms-bericht](media/security-info/securityinfo-prompt-text-msg-enter-code.png)

11. Bekijk de Geslaagd-melding en selecteer vervolgens **Gereed**.

    ![Geslaagd-melding](media/security-info/securityinfo-prompt-call-answered-success.png)

    Uw beveiligingsgegevens zijn bijgewerkt voor het gebruik van een sms-bericht als back-up om uw identiteit te verifiëren bij het gebruik van verificatie in twee stappen of opnieuw instellen van het wachtwoord.

12. Bekijk de pagina **Succes** om te controleren dat u zowel de Microsoft Authenticator-app als een telefoonmethode (sms-bericht of telefonische oproep) heeft ingesteld voor uw beveiligingsgegevens en selecteer vervolgens **Gereed**.

    ![Pagina Wizard is voltooid](media/security-info/securityinfo-prompt-setup-success.png)

    >[!Note]
    >Als uw organisatie vereist dat u app-wachtwoorden gebruikt, ziet u mogelijk een extra sectie in deze wizard waar u deze kunt instellen. Als er een derde sectie met de naam **App-wachtwoorden** wordt weergegeven, moet u deze invullen voordat u de wizard kunt voltooien. Voor stappen over het toevoegen van een app-wachtwoord raadpleegt u de sectie [Uw app-wachtwoorden beheren](#manage-your-app-passwords) in dit artikel.

### <a name="manage-your-app-passwords"></a>Uw app-wachtwoorden beheren

Bepaalde apps, zoals Outlook 2010, bieden geen ondersteuning voor verificatie in twee stappen. Dit gebrek aan ondersteuning betekent dat als u in uw organisatie verificatie in twee stappen gebruikt, de app niet werkt. Om dit probleem op te lossen, kunt u een automatisch gegenereerd wachtwoord maken voor gebruik met elke niet-browser-app, gescheiden van uw normale wachtwoord.

>[!Note]
>Als deze optie niet wordt weergegeven in de wizard, betekent dit dat uw beheerder deze niet heeft ingesteld. Als dit niet is ingesteld, maar u weet dat u app-wachtwoorden moet gebruiken, kunt u de stappen op de pagina [App-wachtwoorden instellen op de pagina Beveiligingsgegevens](security-info-app-passwords.md) volgen.

Wanneer u app-wachtwoorden gebruikt, is het belangrijk om het volgende te onthouden:

- App-wachtwoorden worden automatisch gegenereerd en eenmaal per app ingevoerd.

- Er is een limiet van 40 wachtwoorden per gebruiker. Als u meer wachtwoorden wilt maken dan dat, wordt u gevraagd een bestaand wachtwoord te verwijderen voordat u het nieuwe wachtwoord kunt maken.

- Gebruik één app-wachtwoord per apparaat, niet per app. Maak bijvoorbeeld één wachtwoord voor alle apps op uw laptop, en één ander wachtwoord voor alle apps op uw bureaubladcomputer.

#### <a name="to-add-app-passwords-in-the-sign-in-wizard"></a>App-wachtwoorden toevoegen in de wizard Aanmelden

1. Nadat u de vorige secties van de wizard hebt voltooid, selecteert u **Volgende** en voltooit u de sectie **App-wachtwoord**.

2. Typ de naam van de app waarvoor het wachtwoord is vereist, bijvoorbeeld `Outlook 2010`, en selecteer **Volgende**.

    ![De naam van het app-wachtwoord toevoegen in de wizard](media/security-info/app-password-app-password.png)

3. Kopieer de wachtwoordcode uit het scherm **App-wachtwoord** en plak deze in het gebied **Wachtwoord** van de app (in dit voorbeeld Outlook 2010).

    ![De pagina App-wachtwoord, met een wachtwoord om te kopiëren](media/security-info/app-password-copy-password.png)

4. Nadat u het wachtwoord hebt gekopieerd en in de app hebt geplakt, keert u terug naar deze wizard om ervoor te zorgen dat alle gegevens van uw aanmeldingsmethode juist zijn en selecteert u **Gereed**.

    ![App-wachtwoordpagina met voltooiingskennisgeving](media/security-info/app-password-complete.png)

## <a name="next-steps"></a>Volgende stappen

- Als u standaard-info beveiligingsmethoden wilt wijzigen, wissen of bijwerken, zie:

    - [Beveiligingsgegevens instellen voor een Authenticator-app](security-info-setup-auth-app.md).

    - [Beveiligingsgegevens instellen voor sms-berichten](security-info-setup-text-msg.md).

    - [Beveiligingsgegevens instellen om telefonische boodschappen te gebruiken](security-info-setup-phone-number.md).

    - [Beveiligingsgegevens instellen om e-mail te gebruiken](security-info-setup-email.md).

    - [Beveiligingsgegevens instellen om vooraf gedefinieerde beveiligingsvragen te gebruiken](security-info-setup-questions.md).

- Zie voor meer informatie over hoe u zich aanmelden met de opgegeven methode [Hoe u zich aanmeldt](user-help-sign-in.md).

- Stel uw wachtwoord opnieuw in als u dit hebt verloren of bent vergeten, uit het [Portal voor wachtwoordherstel](https://passwordreset.microsoftonline.com/) of volg de stappen in het artikel [Uw werk- of schoolaccount wachtwoord opnieuw instellen](active-directory-passwords-update-your-own-password.md).

- Krijg tips voor het oplossen van problemen en hulp met problemen met aanmelden in het artikel [U kunt niet aanmelden bij uw Microsoft-account](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant).