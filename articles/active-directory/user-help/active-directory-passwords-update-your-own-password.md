---
title: Uw wacht woord opnieuw instellen met behulp van beveiligings gegevens-Azure Active Directory | Microsoft Docs
description: Uw eigen wacht woord opnieuw instellen als u dit vergeet met uw beveiligings gegevens en verificatie in twee stappen.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 09/10/2020
ms.author: curtand
ms.openlocfilehash: c47d573dab3e72a956f6f0b9d09a1dafadf0225c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91827762"
---
# <a name="reset-your-work-or-school-password-using-security-info"></a>Uw werk-of school wachtwoord opnieuw instellen met behulp van beveiligings gegevens

Als u uw werk-of school Wachtwoord verg eten bent, nooit een wacht woord hebt ontvangen van uw organisatie of uw account hebt vergrendeld, kunt u uw beveiligings gegevens en uw mobiele apparaat gebruiken om uw werk-of school wachtwoord opnieuw in te stellen. Uw beheerder moet deze functie inschakelen zodat u uw gegevens kunt instellen en [uw eigen wacht woord opnieuw instelt](./active-directory-passwords-reset-register.md).

Als u uw wacht woord kent en u dit wilt wijzigen, raadpleegt u de secties [uw wachtwoord stappen wijzigen](#how-to-change-your-password) in dit artikel.

>[!Important]
>Dit artikel is bedoeld voor gebruikers die proberen een verg eten of onbekend werk-of school account-wacht woord opnieuw in te stellen. Als u een beheerder bent die op zoek is naar informatie over het inschakelen van de selfservice voor wachtwoord herstel voor uw werk nemers of andere gebruikers, raadpleegt u de [implementatie van Azure AD self-service voor wachtwoord herstel en andere artikelen](../authentication/howto-sspr-deployment.md).

## <a name="how-to-reset-or-unlock-your-password-for-a-work-or-school-account"></a>Uw wacht woord opnieuw instellen of ontgrendelen voor een werk-of school account

Als u geen toegang hebt tot uw Azure Active Directory-account (Azure AD), kan dit de volgende zijn:

- Uw wacht woord werkt niet en u wilt het opnieuw instellen, of

- U weet uw wacht woord, maar uw account is vergrendeld en u moet het ontgrendelen.

### <a name="to-reset-your-password-and-get-back-into-your-account"></a>Uw wacht woord opnieuw instellen en teruggaan naar uw account

1. Selecteer **mijn wacht woord verg eten** in het scherm **wacht woord invoeren** .

2. Typ in het scherm **terug naar uw account** uw werk-of school **gebruikers-id** (bijvoorbeeld uw e-mail adres), bewijs dat u geen robot bent door de tekens die u op het scherm ziet, in te voeren en vervolgens **volgende** te selecteren.

   ![Ga terug naar uw account scherm](media/security-info/security-info-back-into-acct.png)

   >[!NOTE]
   >Als uw beheerder de mogelijkheid niet heeft ingeschakeld om uw eigen wacht woord opnieuw in te stellen, wordt de koppeling **contact opnemen met de beheerder** weer gegeven in plaats van het scherm **terug naar uw account** . Met deze koppeling kunt u contact opnemen met uw beheerder over het opnieuw instellen van uw wacht woord via een e-mail adres of een webportal.

3. Kies een van de volgende methoden om uw identiteit te verifiëren en uw wacht woord te wijzigen. Afhankelijk van hoe uw beheerder uw organisatie heeft ingesteld, moet u dit proces mogelijk een tweede keer door lopen, waarbij u informatie voor een tweede verificatie stap toevoegt.

    ![Ga terug naar uw account, verificatie stap #1](media/security-info/security-info-back-into-acct2.png)

    >[!NOTE]
    >Afhankelijk van hoe uw beheerder uw organisatie heeft ingesteld, zijn sommige van deze verificatie opties mogelijk niet beschikbaar. U moet uw mobiele apparaat eerder hebben ingesteld voor verificatie met ten minste één van deze methoden.<br><br>Daarnaast moet uw nieuwe wacht woord mogelijk voldoen aan bepaalde sterkte vereisten. Sterke wacht woorden hebben doorgaans 8 tot 16 tekens, inclusief hoofd-en kleine letters, ten minste één cijfer en ten minste één speciaal teken.

- **Stel uw wacht woord opnieuw in met behulp van een e-mail adres.** Hiermee wordt een e-mail bericht verzonden naar het e-mail adres dat u eerder hebt ingesteld in verificatie-of beveiligings gegevens in twee stappen. Als uw beheerder de beveiligings informatie heeft ingeschakeld, kunt u meer informatie over het instellen van een e-mail adres vinden in het artikel [beveiligings gegevens instellen voor het gebruik van e-mail (preview-versie)](security-info-setup-email.md) . Als u nog geen beveiligings gegevens gebruikt, kunt u meer informatie vinden over het instellen van een e-mail adres in het artikel [Stel mijn account in voor verificatie in twee stappen](multi-factor-authentication-end-user-first-time.md) . 

    1. Selecteer **e-mail naar mijn alternatieve e-mail** adres en selecteer vervolgens **e-mail**.

    2. Typ de verificatie code van het e-mail adres in het vak en selecteer **volgende**.

    3. Typ en bevestig uw nieuwe wacht woord en selecteer vervolgens **volt ooien**.

- **Stel uw wacht woord opnieuw in met een tekst bericht.** Hiermee verzendt u een SMS-bericht naar het telefoon nummer dat u eerder hebt ingesteld in Security info. Als uw beheerder de beveiligings informatie heeft ingeschakeld, kunt u meer informatie over het instellen van SMS-berichten vinden in het artikel [beveiligings gegevens instellen voor het gebruik van tekst berichten (preview-versie)](security-info-setup-text-msg.md) . Als u nog geen beveiligings gegevens gebruikt, kunt u meer informatie over het instellen van SMS-berichten vinden in het artikel [Mijn account instellen voor verificatie in twee stappen](multi-factor-authentication-end-user-first-time.md) .

    1. Selecteer **tekst mijn mobiele telefoon**, typ uw telefoon nummer en selecteer vervolgens **tekst**.

    2. Typ de verificatie code van het SMS-bericht in het vak en selecteer vervolgens **volgende**.

    3. Typ en bevestig uw nieuwe wacht woord en selecteer vervolgens **volt ooien**.

- **Stel uw wacht woord opnieuw in met behulp van een telefoon nummer.** Hiermee wordt een geautomatiseerd telefoon gesprek met het nummer dat u eerder hebt ingesteld in Security info. Als uw beheerder de beveiligings informatie heeft ingeschakeld, kunt u meer informatie vinden over het instellen van een telefoon nummer in het artikel [beveiligings gegevens instellen voor het gebruik van een telefoon gesprek (preview-versie)](security-info-setup-phone-number.md) . Als u nog geen beveiligings gegevens gebruikt, kunt u meer informatie vinden over het instellen van een telefoon nummer in het artikel [Mijn account instellen voor verificatie in twee stappen](multi-factor-authentication-end-user-first-time.md) .

    1. Selecteer **mijn mobiele telefoon bellen**, typ uw telefoon nummer en selecteer vervolgens **bellen**.

    2. Beantwoord de telefoon oproep en volg de instructies om uw identiteit te verifiëren en selecteer vervolgens **volgende**.

    3. Typ en bevestig uw nieuwe wacht woord en selecteer vervolgens **volt ooien**.

- **Stel uw wacht woord opnieuw in met beveiligings vragen.** Hier ziet u de lijst met beveiligings vragen die u hebt ingesteld in beveiligings gegevens. Als uw beheerder de beveiligings informatie heeft ingeschakeld, kunt u meer informatie vinden over het instellen van uw beveiligings vragen in het artikel [beveiligings vragen instellen voor het gebruik van vooraf gedefinieerde beveiligings problemen (preview-versie)](security-info-setup-questions.md) . Als u nog geen beveiligings gegevens gebruikt, kunt u meer informatie vinden over het instellen van beveiligings vragen in het artikel [Mijn account instellen voor verificatie in twee stappen](multi-factor-authentication-end-user-first-time.md) .

    1. Selecteer **mijn beveiligings vragen beantwoorden**, beantwoord de vragen en selecteer **volgende**.

    2. Typ en bevestig uw nieuwe wacht woord en selecteer vervolgens **volt ooien**.

- **Stel uw wacht woord opnieuw in met een melding van uw verificator-app.** Hiermee verzendt u een goedkeurings bericht naar de verificator-app. Als uw beheerder de beveiligings informatie heeft ingeschakeld, kunt u meer informatie vinden over het instellen van een verificator-app voor het verzenden van een melding in het artikel [beveiligings gegevens instellen voor het gebruik van een verificatie-app (preview-versie)](security-info-setup-auth-app.md) . Als u nog geen beveiligings gegevens gebruikt, kunt u meer informatie vinden over het instellen van een verificator-app om een melding te verzenden in het artikel [Stel mijn account in voor verificatie in twee stappen](multi-factor-authentication-end-user-first-time.md) .

    1. Selecteer **een melding goed keuren in mijn verificator-app** en selecteer vervolgens **melding verzenden**.

    2. De aanmelding goed keuren vanuit uw verificator-app.

    3. Typ en bevestig uw nieuwe wacht woord en selecteer vervolgens **volt ooien**.

- **Stel uw wacht woord opnieuw in met een code uit uw verificator-app.** Hiermee accepteert u een wille keurige code die door uw verificatie-app wordt verschaft. Als uw beheerder de beveiligings informatie heeft ingeschakeld, kunt u meer informatie vinden over het instellen van een verificator-app voor het opgeven van een code in het artikel [beveiligings gegevens instellen voor het gebruik van een verificatie-app (preview-versie)](security-info-setup-auth-app.md) . Als u nog geen beveiligings gegevens gebruikt, vindt u meer informatie over het instellen van een verificator-app voor het opgeven van een code in het artikel [Mijn account instellen voor verificatie in twee stappen](multi-factor-authentication-end-user-first-time.md) .

  1. Selecteer **een code invoeren uit mijn verificator-app** en selecteer vervolgens **melding verzenden**.

  2. Open uw verificator-app, typ de verificatie code voor uw account in het vak en selecteer vervolgens **volgende**.

  3. Typ en bevestig uw nieuwe wacht woord en selecteer vervolgens **volt ooien**.

  4. Nadat u het bericht hebt ontvangen dat uw wacht woord opnieuw is ingesteld, kunt u zich aanmelden bij uw account met uw nieuwe wacht woord.

     Als u nog steeds geen toegang hebt tot uw account, neemt u contact op met de beheerder van uw organisatie voor meer hulp.

Nadat u uw wacht woord opnieuw hebt ingesteld, kunt u een bevestigings-e-mail ontvangen die afkomstig is van een account zoals ' micro soft namens \<*your_organization*> '. Als u een soortgelijk e-mail bericht krijgt, maar u het wacht woord niet onlangs opnieuw hebt ingesteld, moet u onmiddellijk contact opnemen met de beheerder van uw organisatie.

## <a name="how-to-change-your-password"></a>Uw wachtwoord wijzigen

Als u uw wacht woord alleen wilt wijzigen, kunt u dit doen via de Office 365-Portal, de portal mijn apps of de aanmeldings pagina van Windows 10.

### <a name="to-change-your-password-using-the-office-365-portal"></a>Uw wacht woord wijzigen met behulp van de Office 365-Portal

Gebruik deze methode als u normaal gesp roken toegang hebt tot uw apps via de Office-portal:

1. Meld u aan bij uw [Office 365-account](https://portal.office.com)met uw bestaande wacht woord.

2. Selecteer uw profiel aan de rechter kant en selecteer vervolgens **account weer geven**.

3. Selecteer **beveiligings & privacy**-  >  **wacht woord**.

4. Typ uw oude wacht woord, maak en bevestig uw nieuwe wacht woord en selecteer vervolgens **verzenden**.

### <a name="to-change-your-password-from-the-my-apps-portal"></a>Uw wacht woord wijzigen vanuit de portal mijn apps

Gebruik deze methode als u normaal gesp roken toegang hebt tot uw apps via de portal mijn apps:

1. Meld u aan bij de [Portal mijn apps](https://myapps.microsoft.com/)met behulp van uw bestaande wacht woord.

2. Selecteer uw profiel aan de rechter kant en selecteer vervolgens **profiel**.

3. Selecteer **wacht woord wijzigen**.

4. Typ uw oude wacht woord, maak en bevestig uw nieuwe wacht woord en selecteer vervolgens **verzenden**.

### <a name="to-change-your-password-at-windows-sign-in"></a>Uw wacht woord wijzigen bij het aanmelden bij Windows

Als uw beheerder de functionaliteit heeft ingeschakeld, kunt u een koppeling zien om het **wacht woord opnieuw** in te stellen op het aanmeld scherm van Windows 7, Windows 8, Windows 8,1 of Windows 10.

1. Selecteer de koppeling **wacht woord opnieuw instellen** om het proces voor het opnieuw instellen van wacht woorden te starten zonder gebruik te hoeven maken van de normale webervaring.

2. Bevestig uw gebruikers-ID en selecteer **volgende**.

3. Selecteer en bevestig een contact methode voor verificatie. Kies indien nodig een tweede verificatie optie die afwijkt van uw vorige, waarbij u de benodigde gegevens kunt invullen.

4. Typ op de pagina **een nieuw wacht woord maken** en bevestig uw nieuwe wacht woord en selecteer vervolgens **volgende**.

    Sterke wacht woorden hebben doorgaans 8 tot 16 tekens, inclusief hoofd-en kleine letters, ten minste één cijfer en ten minste één speciaal teken.

5. Nadat u het bericht hebt ontvangen dat uw wacht woord opnieuw is ingesteld, kunt u **volt ooien** selecteren.

    Als u nog steeds geen toegang hebt tot uw account, neemt u contact op met de beheerder van uw organisatie voor meer hulp.

## <a name="common-problems-and-their-solutions"></a>Veelvoorkomende problemen en oplossingen

Hier volgen enkele veelvoorkomende fout gevallen en hun oplossingen:

|Probleem|Beschrijving|Oplossing|
| --- | --- | --- |
|Wanneer ik mijn wacht woord probeer te wijzigen, krijg ik een fout melding. |Uw wacht woord heeft een woord, woord groep of patroon waarmee uw wacht woord gemakkelijk kan worden geraden.| Probeer het opnieuw met een sterker wacht woord.|
|Nadat ik mijn gebruikers-ID heb ingevoerd, ga ik naar een pagina met de tekst ' Neem contact op met de beheerder '.|Micro soft heeft vastgesteld dat het wacht woord van uw gebruikers account wordt beheerd door uw beheerder in een on-premises omgeving. Als gevolg hiervan kunt u uw wacht woord niet opnieuw instellen via de koppeling geen toegang tot uw account. |Neem contact op met de beheerder voor meer informatie.|
|Nadat ik mijn gebruikers-ID heb ingevoerd, krijg ik de fout melding ' uw account is niet ingeschakeld voor het opnieuw instellen van het wacht woord '.|Uw beheerder heeft uw account niet ingesteld zodat u uw eigen wacht woord opnieuw kunt instellen.|Uw beheerder heeft geen wacht woord opnieuw instellen ingesteld voor uw organisatie via de koppeling geen toegang tot uw account of u hebt geen licenties meer voor het gebruik van de functie.<br><br> Als u uw wacht woord opnieuw wilt instellen, moet u de koppeling ' contact opnemen met een beheerder ' selecteren om een e-mail bericht te verzenden naar de beheerder van uw bedrijf. laat u weten dat u uw wacht woord opnieuw wilt instellen.|
|Wanneer ik mijn gebruikers-ID heb ingevoerd, krijg ik een fout melding dat het account niet kan worden geverifieerd.|De account gegevens kunnen niet worden geverifieerd met het aanmeldings proces.|Er zijn twee redenen waarom u dit bericht ziet.<br><br>1. de beheerder heeft het opnieuw instellen van het wacht woord voor uw organisatie ingeschakeld, maar u hebt zich niet geregistreerd voor het gebruik van de service. Registreren voor het opnieuw instellen van een wacht woord Zie een van de volgende artikelen, op basis van uw verificatie methode: [beveiligings gegevens instellen voor het gebruik van een verificator-app (](security-info-setup-auth-app.md)preview), [beveiligings gegevens instellen voor het gebruik van een telefoon gesprek (](security-info-setup-phone-number.md)preview), beveiligings gegevens instellen voor het gebruik van [SMS-berichten (](security-info-setup-text-msg.md)preview), beveiligings gegevens instellen voor het gebruik van [E-mail](security-info-setup-email.md)(preview) of [beveiligings informatie instellen voor het gebruik van beveiligings vragen](security-info-setup-questions.md)<br><br>2. de beheerder heeft het opnieuw instellen van het wacht woord voor uw organisatie niet ingeschakeld. In dit geval moet u de koppeling ' contact opnemen met een beheerder ' selecteren om een e-mail naar uw beheerder te verzenden, waarbij u wordt gevraagd uw wacht woord opnieuw in te stellen.|

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over beveiligings informatie vindt u in het [overzichts artikel Security info (preview)](./security-info-setup-signin.md) .

- Als u probeert terug te gaan naar een persoonlijk account, zoals Xbox, hotmail.com of outlook.com, kunt u de suggesties in de weer gegeven [Wanneer u zich niet kunt aanmelden bij uw Microsoft-account-artikel](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant).
