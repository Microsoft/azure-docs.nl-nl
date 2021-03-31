---
title: Stel uw telefoon nummer in als uw verificatie methode-Azure AD
description: Uw beveiligings gegevens (preview)-pagina instellen om uw identiteit te verifiëren met behulp van uw telefoon nummer en mobiel apparaat als verificatie methode.
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
ms.openlocfilehash: 7500b6b146a627dcdebacd9d2e2c7c61bc43c105
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "83744450"
---
# <a name="set-up-a-phone-number-as-your-verification-method"></a>Stel een telefoon nummer in als uw verificatie methode

U kunt deze stappen volgen om uw twee ledige verificatie en de methoden voor het opnieuw instellen van het wacht woord toe te voegen. Nadat u dit de eerste keer hebt ingesteld, kunt u terugkeren naar de pagina met **beveiligings** gegevens om uw beveiligings gegevens toe te voegen, bij te werken of te verwijderen.

Als u wordt gevraagd om dit onmiddellijk in te stellen nadat u zich hebt aangemeld bij uw werk-of school account, raadpleegt u de gedetailleerde stappen in het artikel [uw beveiligings informatie instellen vanaf het aanmeldings](security-info-setup-signin.md) venster.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

> [!Note]
> Beveiligings gegevens worden niet ondersteund met behulp van telefoon extensies. Zelfs als u de juiste indeling, + 1 4255551234X12345 toevoegt, worden de uitbrei dingen verwijderd voordat de aanroep wordt geplaatst.
>
> Als u een telefoon optie niet ziet, is het mogelijk dat uw organisatie deze optie niet mag gebruiken voor verificatie. In dit geval moet u een andere methode kiezen of contact opnemen met de Help Desk van uw organisatie voor meer hulp.

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

## <a name="set-up-phone-calls-from-the-security-info-page"></a>Telefoon gesprekken instellen vanaf de pagina met beveiligings gegevens

Afhankelijk van de instellingen van uw organisatie kunt u telefoon gesprekken mogelijk gebruiken als een van de methoden voor beveiligings gegevens.

>[!Note]
>Als u een tekst bericht wilt ontvangen in plaats van een telefoon gesprek, volgt u de stappen in het artikel [beveiligings gegevens instellen om tekst berichten te gebruiken](security-info-setup-text-msg.md) .

### <a name="to-set-up-phone-calls"></a>Telefoon gesprekken instellen

1. Meld u aan bij uw werk-of school account en ga vervolgens naar de https://myaccount.microsoft.com/ pagina.

    ![Mijn profiel pagina, met gemarkeerde koppelingen voor beveiligings gegevens](media/security-info/securityinfo-myprofile-page.png)

2. Selecteer **beveiligings gegevens** in het navigatie deel venster links of op de koppeling in het blok met **beveiligings gegevens** en selecteer vervolgens **methode toevoegen** op de pagina **beveiligings gegevens** .

    ![Pagina met beveiligings gegevens met de gemarkeerde optie methode toevoegen](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. Selecteer op de pagina **een methode toevoegen** de optie **telefoon** in de vervolg keuzelijst en selecteer vervolgens **toevoegen**.

    ![Vak methode toevoegen, met telefoon geselecteerd](media/security-info/securityinfo-myprofile-addphonetext.png)

4. Typ op de pagina **telefoon** het telefoon nummer voor uw mobiele apparaat, kies **mij bellen** en selecteer **volgende**.

    ![Telefoon nummer toevoegen en telefoon gesprekken kiezen](media/security-info/securityinfo-myprofile-phonecall-addnumber.png)

5. Beantwoord de telefoon oproep voor verificatie, die wordt verzonden naar het telefoon nummer dat u hebt ingevoerd en volg de instructies.

    De pagina wordt gewijzigd om uw succes weer te geven.

    ![Geslaagde melding, verbinding maken met het telefoon nummer, de keuze voor het ontvangen van telefoon gesprekken en uw account](media/security-info/securityinfo-myprofile-phonetext-success.png)

    Je beveiligings gegevens worden bijgewerkt en je kunt telefoon gesprekken gebruiken om je identiteit te verifiëren wanneer je verificatie in twee stappen gebruikt of wacht woord opnieuw instellen. Zie de sectie [uw standaard beveiligings informatie wijzigen](#change-your-default-security-info-method) in dit artikel als u telefonisch contact wilt maken met de standaard methode.

## <a name="delete-phone-calls-from-your-security-info-methods"></a>Telefoon gesprekken verwijderen uit uw beveiligings info-methoden

Als u telefoon gesprekken niet meer wilt gebruiken als een beveiligings gegevens methode, kunt u deze verwijderen van de pagina met **beveiligings gegevens** .

>[!Important]
>Als u telefoon gesprekken per ongeluk verwijdert, is het niet mogelijk om deze ongedaan te maken. U moet de-methode opnieuw toevoegen door de stappen in de sectie [telefoon gesprekken instellen](#set-up-phone-calls-from-the-security-info-page) van dit artikel te volgen.

### <a name="to-delete-phone-calls"></a>Telefoon gesprekken verwijderen

1. Selecteer op de pagina **beveiligings gegevens** de koppeling **verwijderen** naast de optie **telefoon** .

    ![Koppeling voor het verwijderen van de telefoon methode vanuit beveiligings gegevens](media/security-info/securityinfo-myprofile-phonetext-delete.png)

2. Selecteer **Ja** in het bevestigings venster om het **telefoon** nummer te verwijderen. Nadat uw telefoon nummer is verwijderd, wordt het verwijderd uit uw beveiligings gegevens en verdwijnt dit op de pagina met **beveiligings gegevens** . Als **telefoon** de standaard methode is, wordt de standaard instelling gewijzigd in een andere beschik bare methode.

## <a name="change-your-default-security-info-method"></a>De standaard methode voor beveiligings gegevens wijzigen

Als u wilt dat telefoon gesprekken de standaard methode zijn die wordt gebruikt wanneer u zich aanmeldt bij uw werk-of school account met behulp van twee ledige verificatie of voor aanvragen voor het opnieuw instellen van wacht woorden, kunt u deze instellen op de pagina met **beveiligings gegevens** .

### <a name="to-change-your-default-security-info-method"></a>De standaard methode voor beveiligings gegevens wijzigen

1. Selecteer op de pagina **beveiligings gegevens** de **wijzigings** koppeling naast de standaard gegevens van de **aanmeldings methode** .

    ![Koppeling wijzigen voor standaard aanmeldings methode](media/security-info/securityinfo-myprofile-phonetext-defaultchange.png)

2. Selecteer **telefoon oproep (*_your_phone_number_*)** in de vervolg keuzelijst met beschik bare methoden en selecteer vervolgens **bevestigen**.

    ![Methode kiezen voor standaard aanmelding](media/security-info/securityinfo-myprofile-phonecall-changeddefault.png)

    De standaard methode voor het aanmelden bij de **telefoon oproep (*_your_phone_number_*)** wordt gebruikt.

## <a name="additional-security-info-methods"></a>Aanvullende beveiligings gegevens methoden

U hebt extra opties voor het controleren van uw identiteit door uw organisatie, op basis van wat you're u probeert te doen. De opties zijn:

- **Verificator-app.** Down load en gebruik een verificator-app om een goedkeurings melding of een wille keurig gegenereerde goedkeurings code te verkrijgen voor verificatie in twee stappen of het opnieuw instellen van wacht woorden. Zie [beveiligings informatie instellen voor het gebruik van een verificator-app](security-info-setup-auth-app.md)voor stapsgewijze instructies voor het instellen en gebruiken van de app Microsoft Authenticator.

- **Tekst van mobiel apparaat.** Voer het nummer van uw mobiele apparaat in en ontvang een tekst die u gebruikt voor verificatie in twee stappen of het opnieuw instellen van wacht woorden. Voor stapsgewijze instructies over het verifiëren van uw identiteit met een SMS-bericht, Zie [beveiligings informatie instellen voor het gebruik van tekst berichten (SMS)](security-info-setup-text-msg.md).

- **Beveiligings sleutel.** Registreer uw door micro soft compatibele beveiligings sleutel en gebruik deze samen met een pincode voor verificatie in twee stappen of het opnieuw instellen van wacht woorden. Zie [beveiligings informatie instellen voor het gebruik van een beveiligings sleutel](security-info-setup-security-key.md)voor stapsgewijze instructies over het verifiëren van uw identiteit met een beveiligings sleutel.

- **E-mail adres.** Voer het e-mail adres van uw werk of school in om een e-mail te ontvangen voor het opnieuw instellen van wacht woorden. Deze optie is niet beschikbaar voor verificatie in twee stappen. Zie [beveiligings informatie instellen voor het gebruik van e-mail](security-info-setup-email.md)voor stapsgewijze instructies voor het instellen van uw e-mail adres.

- **Beveiligings vragen.** Beantwoord enkele beveiligings vragen die door uw beheerder voor uw organisatie zijn gemaakt. Deze optie is alleen beschikbaar voor het opnieuw instellen van wacht woorden en niet voor verificatie in twee stappen. Voor stapsgewijze instructies over het instellen van uw beveiligings vragen raadpleegt u het artikel [beveiligings vragen instellen voor het gebruik](security-info-setup-questions.md) van beveiligings problemen.

    >[!Note]
    >Als sommige van deze opties ontbreken, is het waarschijnlijk dat uw organisatie deze methoden niet toestaat. Als dit het geval is, moet u een beschik bare methode kiezen of contact opnemen met de beheerder voor meer hulp.

## <a name="next-steps"></a>Volgende stappen

- Stel uw wachtwoord opnieuw in als u dit hebt verloren of bent vergeten, uit het [Portal voor wachtwoordherstel](https://passwordreset.microsoftonline.com/) of volg de stappen in het artikel [Uw werk- of schoolaccount wachtwoord opnieuw instellen](active-directory-passwords-update-your-own-password.md).

- Krijg tips voor het oplossen van problemen en hulp met problemen met aanmelden in het artikel [U kunt niet aanmelden bij uw Microsoft-account](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant).
