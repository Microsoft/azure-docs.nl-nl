---
title: Stel de Microsoft Authenticator-app in als uw verificatie methode-Azure AD
description: Hoe u uw beveiligings informatie pagina (preview) instelt om uw identiteit te verifiëren met behulp van de Microsoft Authenticator-app als verificatie methode.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 02/13/2019
ms.author: curtand
ms.openlocfilehash: c96be79a349478db7d0452cae1ff07ee1c48aaa3
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94700083"
---
# <a name="set-up-the-microsoft-authenticator-app-as-your-verification-method"></a>De app Microsoft Authenticator instellen als uw verificatiemethode

U kunt deze stappen volgen om uw twee ledige verificatie en de methoden voor het opnieuw instellen van het wacht woord toe te voegen. Nadat u dit de eerste keer hebt ingesteld, kunt u terugkeren naar de pagina met **beveiligings** gegevens om uw beveiligings gegevens toe te voegen, bij te werken of te verwijderen.

Als u wordt gevraagd om dit onmiddellijk in te stellen nadat u zich hebt aangemeld bij uw werk-of school account, raadpleegt u de gedetailleerde stappen in het artikel [uw beveiligings informatie instellen vanaf het aanmeldings](security-info-setup-signin.md) venster.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

>[!Note]
> Als u de optie verificator-app niet ziet, is het mogelijk dat uw organisatie deze optie niet mag gebruiken voor verificatie. In dit geval moet u een andere methode kiezen of contact opnemen met de Help Desk van uw organisatie voor meer hulp.

## <a name="security-vs-password-reset-verification"></a>Verificatie versus wachtwoord herstel voor beveiliging

Methoden voor beveiligingsgegevens worden zowel gebruikt voor tweeledige beveiligingsverificatie als voor het opnieuw instellen van het wachtwoord. Niet elke methode kan echter voor beide worden gebruikt.

| Methode | Gebruikt voor |
| ------ | -------- |
| Authenticator-app | Tweeledige verificatie en verificatie voor het opnieuw instellen van het wachtwoord. |
| Sms-berichten | Tweeledige verificatie en verificatie voor het opnieuw instellen van het wachtwoord. |
| Telefoonoproep | Tweeledige verificatie en verificatie voor het opnieuw instellen van het wachtwoord. |
| Beveiligingssleutel | Tweeledige verificatie en verificatie voor het opnieuw instellen van het wachtwoord. |
| E-mailaccount | Alleen voor verificatie voor het opnieuw instellen van het wachtwoord. Voor tweeledige verificatie moet u een andere methode kiezen. |
| Beveiligingsvragen | Alleen voor verificatie voor het opnieuw instellen van het wachtwoord. Voor tweeledige verificatie moet u een andere methode kiezen. |

## <a name="set-up-the-microsoft-authenticator-app-from-the-security-info-page"></a>De Microsoft Authenticator-app instellen op de pagina met beveiligings gegevens

Afhankelijk van de instellingen van uw organisatie kunt u mogelijk een verificatie-app gebruiken als een van uw methoden voor beveiligings gegevens. U hoeft de Microsoft Authenticator-app niet te gebruiken en u kunt een andere app kiezen tijdens het instellen van het proces. In dit artikel wordt echter uitgegaan van de app Microsoft Authenticator.

>[!Important]
> Als u Microsoft Authenticator-app hebt ingesteld op vijf verschillende apparaten of 5 hardware-tokens, kunt u een zesde versie niet instellen en ziet u mogelijk het volgende fout bericht.
> 
> **U kunt Microsoft Authenticator niet instellen omdat u al vijf verificator-apps of-hardware-tokens hebt. Neem contact op met uw beheerder om een van uw verificator-apps of-hardware-tokens te verwijderen.**

### <a name="to-set-up-the-microsoft-authenticator-app"></a>De app Microsoft Authenticator instellen

1. Meld u aan bij uw werk-of school account en ga vervolgens naar de https://myaccount.microsoft.com/ pagina.

    ![Mijn profiel pagina, met gemarkeerde koppelingen voor beveiligings gegevens](media/security-info/securityinfo-myprofile-page.png)

2. Selecteer **beveiligings gegevens** in het navigatie deel venster links of op de koppeling in het blok met **beveiligings gegevens** en selecteer vervolgens **methode toevoegen** op de pagina **beveiligings gegevens** .

    ![Pagina met beveiligings gegevens met de gemarkeerde optie methode toevoegen](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. Selecteer op de pagina **een methode toevoegen** de optie **verificator-app** in de vervolg keuzelijst en selecteer vervolgens **toevoegen**.

    ![Het vak methode toevoegen met de verificator-app geselecteerd](media/security-info/securityinfo-myprofile-addauthapp.png)

4. Selecteer op de pagina **beginnen met het ophalen van de app de** optie **nu downloaden** om de app Microsoft Authenticator op uw mobiele apparaat te downloaden en te installeren en selecteer vervolgens **volgende**.

    Zie voor meer informatie over het downloaden en installeren van de app, [De Microsoft Authenticator-app downloaden en installeren](user-help-auth-app-download-install.md).

    ![Begin met het ophalen van de app-pagina](media/security-info/securityinfo-myprofile-getauthapp.png)

   > [!Note]
   > Als u een andere verificator-app dan de Microsoft Authenticator-app gebruiken wilt, selecteert u de koppeling **Ik wil een andere verificator-app gebruiken**.
   >
   > Als u van uw organisatie u een andere methode dan de authenticator-app kiezen mag, kunt u de **Ik wil andere methode instellen-koppeling** selecteren.

5. Blijven op de **Uw account instellen**-pagina tijdens het instellen van de Microsoft Authenticator-app op uw mobiele apparaat.

    ![De verificator-app-pagina instellen](media/security-info/securityinfo-myprofile-setupauthapp.png)

6. Open de Microsoft Authenticator-app, selecteer dat u meldingen toestaat (als u hierom wordt gevraagd), selecteer **Account toevoegen** uit het pictogram **Aanpassen en controle** in de rechterbovenhoek en selecteer vervolgens **Werk- of schoolaccount**.

    >[!Note]
    >Als dit de eerste keer is dat u de Microsoft Authenticator-app instelt, wordt u mogelijk gevraagd of u de app toegang wilt geven tot uw camera (iOS) of de wilt toestaan foto's te maken en video op te nemen (Android). U moet **Toestaan** selecteren, zodat de Authenticator-app toegang heeft tot uw camera om in de volgende stap een foto te maken van de QR-code. Als u de app geen cameratoegang geeft, kunt u de Authenticator-app wel installeren, maar moet u de codegegevens handmatig toevoegen. Zie [Handmatig een account toevoegen aan de app](user-help-auth-app-add-account-manual.md) voor informatie over het handmatig toevoegen van de code.

7. Ga terug naar de pagina **Uw account instellen** op uw computer en selecteer vervolgens **Volgende**.

    De pagina **QR-code scannen** wordt weergegeven.

    ![De QR-code scannen met behulp van de Authenticator-app](media/security-info/securityinfo-myprofile-qrcodeauthapp.png)

8. Scan de meegeleverde code met de Microsoft Authenticator QR-code lezer van de app, die op uw mobiele apparaat werd weer gegeven nadat u uw werk-of school account hebt gemaakt in stap 6.

    De verificator-app moet nu uw werk of schoolaccount toevoegen zonder aanvullende informatie van u. Als de QR-codelezer de code echter niet lezen kan, kunt u de **Koppeling kan QR-code niet scannen** selecteren en handmatig de code en URL in de Microsoft Authenticator-app invoeren. Zie voor meer informatie over het handmatig toevoegen van een code [Handmatig een account toevoegen aan de app](user-help-auth-app-add-account-manual.md).

9. Selecteer **Volgende** op de pagina **QR-code scannen** op uw computer.

    Een melding wordt verzonden naar de Microsoft Authenticator-app op uw mobiele apparaat voor het testen van uw account.

    ![Uw account met de verificator-app testen](media/security-info/securityinfo-myprofile-tryitauthapp.png)

10. Keur de melding in de Microsoft Authenticator-app goed en selecteer vervolgens **Volgende**.

     ![Melding van geslaagd, met verbinding van de app en uw account](media/security-info/securityinfo-myprofile-successauthapp.png)

     Uw beveiligingsgegevens zijn bijgewerkt voor het standaard gebruik van de Microsoft Authenticator-app om uw identiteit te verifiëren bij het gebruik van verificatie in twee stappen of opnieuw instellen van het wachtwoord.

## <a name="delete-your-authenticator-app-from-your-security-info-methods"></a>Je verificator-app uit je beveiligings gegevens methoden verwijderen

Als u uw verificator-app niet meer wilt gebruiken als een beveiligings gegevens methode, kunt u deze verwijderen van de pagina met **beveiligings gegevens** . Dit werkt voor alle verificator-apps, niet alleen de Microsoft Authenticator-app. Nadat u de app hebt verwijderd, moet u naar de verificator-app op uw mobiele apparaat gaan en het account verwijderen.

>[!Important]
>Als u de verificator-app per ongeluk verwijdert, is het niet mogelijk om deze ongedaan te maken. U moet de verificator-app opnieuw toevoegen door de stappen in de sectie [de verificator-app instellen](#set-up-the-microsoft-authenticator-app-from-the-security-info-page) in dit artikel te volgen.

### <a name="to-delete-the-authenticator-app"></a>De verificator-app verwijderen

1. Selecteer op de pagina **beveiligings gegevens** de koppeling **verwijderen** naast de verificator-app.

    ![Koppeling om de verificator-app uit de beveiligings gegevens te verwijderen](media/security-info/securityinfo-myprofile-deleteauthapp.png)

2. Selecteer **Ja** in het bevestigings venster om de verificator-app te verwijderen. Nadat de verificator-app is verwijderd, wordt deze verwijderd uit de beveiligings gegevens en verdwijnt deze van de pagina met **beveiligings gegevens** . Als de verificator-app de standaard methode is, wordt de standaard waarde gewijzigd in een andere beschik bare methode.

3. Open de verificator-app op uw mobiele apparaat, selecteer **accounts bewerken** en verwijder vervolgens uw werk-of school account uit de verificator-app.

    Uw account is volledig verwijderd uit de verificator-app voor twee ledige verificatie en aanvragen voor het opnieuw instellen van wacht woorden.

## <a name="change-your-default-security-info-method"></a>De standaard methode voor beveiligings gegevens wijzigen

Als u wilt dat de verificator-app de standaard methode wordt gebruikt wanneer u zich aanmeldt bij uw werk-of school account met behulp van twee ledige verificatie of voor aanvragen voor het opnieuw instellen van wacht woorden, kunt u deze instellen op de pagina met beveiligings **gegevens** .

### <a name="to-change-your-default-security-info-method"></a>De standaard methode voor beveiligings gegevens wijzigen

1. Selecteer op de pagina **beveiligings gegevens** de **wijzigings** koppeling naast de standaard gegevens van de **aanmeldings methode** .

    ![Koppeling wijzigen voor standaard aanmeldings methode](media/security-info/securityinfo-myprofile-changedefaultauthapp.png)

2. Kies **Microsoft Authenticator-melding** in de vervolg keuzelijst met beschik bare methoden. Als u de app Microsoft Authenticator niet gebruikt, selecteert u de optie **verificator-app of hardware-token** .

    ![Methode kiezen voor standaard aanmelding](media/security-info/securityinfo-myprofile-defaultauthapp.png)

3. Selecteer **bevestigen**.

    De standaard methode voor het aanmelden wordt gewijzigd in de app Microsoft Authenticator.

## <a name="additional-security-info-methods"></a>Aanvullende beveiligings gegevens methoden

U hebt extra opties voor het controleren van uw identiteit door uw organisatie, op basis van wat you're u probeert te doen. De opties zijn:

- **Tekst van mobiel apparaat.** Voer het nummer van uw mobiele apparaat in en ontvang een tekst die u gebruikt voor verificatie in twee stappen of het opnieuw instellen van wacht woorden. Voor stapsgewijze instructies over het verifiëren van uw identiteit met een SMS-bericht, Zie [beveiligings informatie instellen voor het gebruik van tekst berichten (SMS)](security-info-setup-text-msg.md).

- **Mobiel apparaat of telefoon nummer van werk.** Voer het nummer van uw mobiele apparaat in en ontvang een telefoon oproep voor verificatie in twee stappen of het opnieuw instellen van wacht woorden. Zie [beveiligings informatie instellen voor het gebruik van telefoon gesprekken](security-info-setup-phone-number.md)voor stapsgewijze instructies over het verifiëren van uw identiteit met een telefoon nummer.

- **Beveiligings sleutel.** Registreer uw door micro soft compatibele beveiligings sleutel en gebruik deze samen met een pincode voor verificatie in twee stappen of het opnieuw instellen van wacht woorden. Zie [beveiligings informatie instellen voor het gebruik van een beveiligings sleutel](security-info-setup-security-key.md)voor stapsgewijze instructies over het verifiëren van uw identiteit met een beveiligings sleutel.

- **E-mail adres.** Voer het e-mail adres van uw werk of school in om een e-mail te ontvangen voor het opnieuw instellen van wacht woorden. Deze optie is niet beschikbaar voor verificatie in twee stappen. Zie [beveiligings informatie instellen voor het gebruik van e-mail](security-info-setup-email.md)voor stapsgewijze instructies voor het instellen van uw e-mail adres.

- **Beveiligings vragen.** Beantwoord enkele beveiligings vragen die door uw beheerder voor uw organisatie zijn gemaakt. Deze optie is alleen beschikbaar voor het opnieuw instellen van wacht woorden en niet voor verificatie in twee stappen. Voor stapsgewijze instructies over het instellen van uw beveiligings vragen raadpleegt u het artikel [beveiligings vragen instellen voor het gebruik](security-info-setup-questions.md) van beveiligings problemen.

    >[!Note]
    >Als sommige van deze opties ontbreken, is het waarschijnlijk dat uw organisatie deze methoden niet toestaat. Als dit het geval is, moet u een beschik bare methode kiezen of contact opnemen met de beheerder voor meer hulp.

## <a name="next-steps"></a>Volgende stappen

- Meld u aan met behulp van de Microsoft Authenticator-app en volg de stappen in het artikel [Aanmelden met verificatie in twee stappen of beveiligings informatie](security-info-setup-signin.md) .

- Stel uw wachtwoord opnieuw in als u dit hebt verloren of bent vergeten, uit het [Portal voor wachtwoordherstel](https://passwordreset.microsoftonline.com/) of volg de stappen in het artikel [Uw werk- of schoolaccount wachtwoord opnieuw instellen](active-directory-passwords-update-your-own-password.md).

- Krijg tips voor het oplossen van problemen en hulp met problemen met aanmelden in het artikel [U kunt niet aanmelden bij uw Microsoft-account](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant).
