---
title: Beveiligings vragen instellen als uw verificatie methode-Azure AD
description: Hoe u de pagina beveiligings gegevens (preview) instelt om uw identiteit te verifiëren met vooraf gedefinieerde beveiligings vragen als verificatie methode.
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
ms.openlocfilehash: 0c55f15d12ddc51573f59f56c742a8cac525af0b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "83744436"
---
# <a name="set-up-security-questions-as-your-verification-method"></a>Beveiligings vragen instellen als verificatie methode

U kunt deze stappen volgen om de methode voor het opnieuw instellen van een wacht woord toe te voegen. Nadat u dit de eerste keer hebt ingesteld, kunt u terugkeren naar de pagina met **beveiligings** gegevens om uw beveiligings gegevens toe te voegen, bij te werken of te verwijderen.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

>[!Note]
>Als u de optie beveiligings vragen niet ziet, is het mogelijk dat uw organisatie deze optie niet kan gebruiken voor de methode voor het opnieuw instellen van uw wacht woord. In dit geval moet u een andere methode kiezen of contact opnemen met de Help Desk van uw organisatie voor meer informatie.

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

## <a name="set-up-your-security-questions-from-the-security-info-page"></a>Uw beveiligings vragen instellen op de pagina met beveiligings gegevens

Afhankelijk van de instellingen van uw organisatie kunt u mogelijk een aantal beveiligings vragen kiezen en beantwoorden als een van de methoden voor beveiligings gegevens. Uw beheerder stelt het aantal beveiligings vragen in dat u nodig hebt om te kiezen en te beantwoorden.

Als u beveiligings vragen gebruikt, raden wij u aan deze te gebruiken in combi natie met een andere methode. Beveiligings vragen kunnen minder veilig zijn dan andere methoden omdat sommige mensen de antwoorden op uw vragen kunnen kennen.

> [!Note]
> Beveiligings vragen worden privé opgeslagen en beveiligd op een gebruikers object in de Directory en kunnen tijdens de registratie alleen door u worden beantwoord. De beheerder kan uw vragen of antwoorden op geen enkele manier lezen of wijzigen.
>
> Als u de optie beveiligings vragen niet ziet, is het mogelijk dat uw organisatie geen beveiligings vragen mag gebruiken voor verificatie. Als dit het geval is, moet u een andere methode kiezen of contact opnemen met de beheerder voor meer informatie.
>
> Beheerders accounts mogen geen beveiligings vragen gebruiken als een methode voor het opnieuw instellen van een wacht woord. Als u bent aangemeld als account voor het beheer niveau, worden deze opties niet weer geven.

### <a name="to-set-up-your-security-questions"></a>Uw beveiligings vragen instellen

1. Meld u aan bij uw werk-of school account en ga vervolgens naar de https://myaccount.microsoft.com/ pagina.

    ![Mijn profiel pagina, met gemarkeerde koppelingen voor beveiligings gegevens](media/security-info/securityinfo-myprofile-page.png)

2. Selecteer **beveiligings gegevens** in het navigatie deel venster links of op de koppeling in het blok met **beveiligings gegevens** en selecteer vervolgens **methode toevoegen** op de pagina **beveiligings gegevens** .

    ![Pagina met beveiligings gegevens met de gemarkeerde optie methode toevoegen](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. Selecteer op de pagina **een methode toevoegen** de optie **beveiligings vragen** in de vervolg keuzelijst en selecteer vervolgens **toevoegen**.

    ![Het vak methode toevoegen met geselecteerde beveiligings vragen](media/security-info/securityinfo-myprofile-addquestions.png)

4. Op de pagina **beveiligings vragen** klikt u op en beantwoordt u de beveiligings vragen en selecteert u vervolgens **Opslaan**.

    ![Telefoon nummer toevoegen en telefoon gesprekken kiezen](media/security-info/securityinfo-myprofile-securityquestions.png)

    Je beveiligings gegevens worden bijgewerkt en je kunt je beveiligings vragen gebruiken om je identiteit te verifiëren bij het gebruik van wacht woord opnieuw instellen.

## <a name="delete-security-questions-from-your-security-info-methods"></a>Beveiligings vragen verwijderen uit uw beveiligings info-methoden

Als u uw beveiligings vragen niet meer wilt gebruiken als een beveiligings gegevens methode, kunt u deze verwijderen van de pagina met **beveiligings gegevens** .

>[!Important]
>Als u de beveiligings vragen per ongeluk verwijdert, is het niet mogelijk om deze ongedaan te maken. U moet de methode opnieuw toevoegen door de stappen in de sectie [uw beveiligings vragen instellen](#set-up-your-security-questions-from-the-security-info-page) van dit artikel te volgen.

### <a name="to-delete-your-security-questions"></a>Uw beveiligings vragen verwijderen

1. Selecteer op de pagina **beveiligings gegevens** de koppeling **verwijderen** naast de optie **beveiligings vragen** .

    ![Koppeling voor het verwijderen van de telefoon methode vanuit beveiligings gegevens](media/security-info/securityinfo-myprofile-questionsdelete.png)

2. Selecteer **Ja** in het bevestigings venster om uw **beveiligings vragen** te verwijderen. Nadat uw beveiligings vragen zijn verwijderd, wordt de methode verwijderd uit de beveiligings gegevens en verdwijnt deze van de pagina met **beveiligings gegevens** .

## <a name="additional-security-info-methods"></a>Aanvullende beveiligings gegevens methoden

U hebt extra opties voor het controleren van uw identiteit door uw organisatie, op basis van wat you're u probeert te doen. De opties zijn:

- **Verificator-app.** Down load en gebruik een verificator-app om een goedkeurings melding of een wille keurig gegenereerde goedkeurings code te verkrijgen voor verificatie in twee stappen of het opnieuw instellen van wacht woorden. Zie [beveiligings informatie instellen voor het gebruik van een verificator-app](security-info-setup-auth-app.md)voor stapsgewijze instructies voor het instellen en gebruiken van de app Microsoft Authenticator.

- **Tekst van mobiel apparaat.** Voer het nummer van uw mobiele apparaat in en ontvang een tekst die u gebruikt voor verificatie in twee stappen of het opnieuw instellen van wacht woorden. Voor stapsgewijze instructies over het verifiëren van uw identiteit met een SMS-bericht, Zie [beveiligings informatie instellen voor het gebruik van tekst berichten (SMS)](security-info-setup-text-msg.md).

- **Mobiel apparaat of telefoon nummer van werk.** Voer het nummer van uw mobiele apparaat in en ontvang een telefoon oproep voor verificatie in twee stappen of het opnieuw instellen van wacht woorden. Zie [beveiligings informatie instellen voor het gebruik van telefoon gesprekken](security-info-setup-phone-number.md)voor stapsgewijze instructies over het verifiëren van uw identiteit met een telefoon nummer.

- **Beveiligings sleutel.** Registreer uw door micro soft compatibele beveiligings sleutel en gebruik deze samen met een pincode voor verificatie in twee stappen of het opnieuw instellen van wacht woorden. Zie [beveiligings informatie instellen voor het gebruik van een beveiligings sleutel](security-info-setup-security-key.md)voor stapsgewijze instructies over het verifiëren van uw identiteit met een beveiligings sleutel.

- **E-mail adres.** Voer het e-mail adres van uw werk of school in om een e-mail te ontvangen voor het opnieuw instellen van wacht woorden. Deze optie is niet beschikbaar voor verificatie in twee stappen. Zie [beveiligings informatie instellen voor het gebruik van e-mail](security-info-setup-email.md)voor stapsgewijze instructies voor het instellen van uw e-mail adres.

    >[!Note]
    >Als sommige van deze opties ontbreken, is het waarschijnlijk dat uw organisatie deze methoden niet toestaat. Als dit het geval is, moet u een beschik bare methode kiezen of contact opnemen met de beheerder voor meer hulp.

## <a name="next-steps"></a>Volgende stappen

- Stel uw wachtwoord opnieuw in als u dit hebt verloren of bent vergeten, uit het [Portal voor wachtwoordherstel](https://passwordreset.microsoftonline.com/) of volg de stappen in het artikel [Uw werk- of schoolaccount wachtwoord opnieuw instellen](active-directory-passwords-update-your-own-password.md).

- Krijg tips voor het oplossen van problemen en hulp met problemen met aanmelden in het artikel [U kunt niet aanmelden bij uw Microsoft-account](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant).
