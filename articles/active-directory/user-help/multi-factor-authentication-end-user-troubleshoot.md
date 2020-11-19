---
title: Veelvoorkomende problemen met account twee ledige verificatie-Azure AD
description: Oplossingen voor een aantal veelvoorkomende problemen met twee ledige verificaties en uw werk-of school account.
services: active-directory
author: curtand
manager: daveba
ms.assetid: 8f3aef42-7f66-4656-a7cd-d25a971cb9eb
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 09/01/2020
ms.author: curtand
ms.reviewer: kexia
metadata ms.custom: contperfq1
ms.openlocfilehash: 095ea62ee992fa12fc80f715f9e63725e9930595
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/19/2020
ms.locfileid: "94886206"
---
# <a name="common-problems-with-two-factor-verification-and-your-work-or-school-account"></a>Veelvoorkomende problemen met verificatie in twee stappen en uw werk- of schoolaccount

Er zijn enkele veelvoorkomende problemen met twee ledige verificatie die vaak vaker voor komen dan wat ons zou willen. We hebben dit artikel gecombineerd voor het beschrijven van oplossingen voor de meest voorkomende problemen.

Uw Azure Active Directory-organisatie (Azure AD) kan twee ledige verificatie inschakelen voor uw account. Wanneer verificatie met twee factoren is ingeschakeld, is voor uw account aanmelding een combi natie van de volgende gegevens vereist:

- Uw gebruikers naam
- Uw wachtwoord
- Een mobiel apparaat of telefoon nummer

Verificatie met twee factoren is veiliger dan alleen een wacht woord, omdat voor twee ledige verificatie iets is vereist dat u al _kent_ en wat u _hebt_. Geen hackers hebben uw fysieke telefoon.

>[!Important]
>Als u een beheerder bent, kunt u meer informatie vinden over het instellen en beheren van uw Azure AD-omgeving in de [Azure AD-documentatie](../index.yml).

Deze inhoud kan u helpen bij uw werk-of school account. Dit is het account dat u hebt ontvangen van uw organisatie (bijvoorbeeld dritan@contoso.com ). Als u problemen ondervindt met twee ledige verificatie voor een persoonlijk Microsoft-account, een account dat u zelf hebt ingesteld (bijvoorbeeld danielle@outlook.com ), raadpleegt u [twee ledige verificatie in-of uitschakelen voor uw Microsoft-account](https://support.microsoft.com/help/4028586/microsoft-account-turning-two-step-verification-on-or-off).

## <a name="i-dont-have-my-mobile-device-with-me"></a>Ik heb mijn mobiele apparaat niet met mij

Dit gebeurt. Je hebt je mobiele apparaat thuis verlaten en je kunt je telefoon nu niet gebruiken om te controleren wie je bent. Misschien hebt u eerder een alternatieve methode toegevoegd om u aan te melden bij uw account, bijvoorbeeld via uw zakelijke telefoon. Als dit het geval is, kunt u deze alternatieve methode nu gebruiken. Als u nooit een alternatieve verificatie methode hebt toegevoegd, kunt u contact opnemen met de Help Desk van uw organisatie voor hulp.

### <a name="to-sign-in-to-your-work-or-school-account-using-another-verification-method"></a>Aanmelden bij uw werk-of school account met een andere verificatie methode

1. Meld u aan bij uw account, maar selecteer de koppeling **aanmelden op een andere manier** op de pagina **twee ledige verificatie** .

    ![Verificatie methode voor aanmelding wijzigen](./media/multi-factor-authentication-end-user-troubleshoot/two-factor-auth-signin-another-way.png)

    Als u de koppeling **aanmelden op een andere manier** niet ziet, betekent dit dat u geen andere verificatie methoden hebt ingesteld. Neem contact op met uw beheerder voor hulp bij het aanmelden bij uw account.

2. Kies uw alternatieve verificatie methode en ga door met het verificatie proces met twee factoren.

## <a name="i-cant-turn-two-factor-verification-off"></a>Ik kan niet twee ledige verificatie uitschakelen

- Als u twee ledige verificatie gebruikt met een persoonlijk account voor een micro soft-service, alain@outlook.com kunt u [de functie in-en uitschakelen](https://account.live.com/proofs/Manage).

- Als u twee ledige verificatie met uw werk-of school account gebruikt, betekent dit waarschijnlijk dat uw organisatie heeft besloten deze extra beveiligings functie te gebruiken. Het is niet mogelijk om deze afzonderlijk in te scha kelen.

Als u twee ledige verificatie niet kunt uitschakelen, kan dit ook worden veroorzaakt door de standaard beveiligings instellingen die zijn toegepast op het niveau van de organisatie. Zie [Wat zijn de standaard beveiligings instellingen?](../fundamentals/concept-fundamentals-security-defaults.md) voor meer informatie over de standaard instellingen van de beveiliging.

## <a name="my-device-was-lost-or-stolen"></a>Mijn apparaat is verloren of gestolen

Als u bent kwijt geraakt of als uw mobiele apparaat is gestolen, kunt u een van de volgende acties uitvoeren:

- Meld u aan met een andere methode.
- Vraag de Help Desk van uw organisatie om uw instellingen te wissen.

We raden u ten zeerste aan de Help Desk van uw organisatie te laten weten of uw telefoon is kwijt geraakt of gestolen. De Help Desk kan de juiste updates voor uw account maken. Nadat uw instellingen zijn gewist, wordt u gevraagd om u te [registreren voor twee ledige verificatie](multi-factor-authentication-end-user-first-time.md) bij de volgende keer dat u zich aanmeldt.

## <a name="im-not-receiving-the-verification-code-sent-to-my-mobile-device"></a>Ik ontvang geen verificatie code die wordt verzonden naar mijn mobiele apparaat

Het ontvangen van uw verificatie code is een veelvoorkomend probleem. Het probleem heeft doorgaans te maken met uw mobiele apparaat en de instellingen ervan. Hier volgen enkele acties die u kunt proberen.

Probeer dit eens | Informatie over begeleiding
--------- | ------------
De micro soft Authenticator-app of verificatie codes gebruiken | U krijgt onze limiet voor de verificatie aanroepen, of u hebt de limiet bereikt voor de tekst verificatie codes, fout berichten tijdens het aanmelden. <br/><br/>Micro soft kan in korte tijd herhaalde verificatie pogingen beperken die door dezelfde gebruiker worden uitgevoerd. Deze beperking is niet van toepassing op de Microsoft Authenticator of de verificatie code. Als u deze limieten hebt bereikt, kunt u de verificator-app gebruiken, verificatie code of het aanmelden over een paar minuten opnieuw proberen.
Uw mobiele apparaat opnieuw opstarten | Soms moet uw apparaat gewoon worden vernieuwd. Wanneer u uw apparaat opnieuw opstart, worden alle achtergrond processen en-services beëindigd. Bij het opnieuw opstarten worden ook de kern onderdelen van het apparaat afgesloten. Elke service of elk onderdeel wordt vernieuwd wanneer u het apparaat opnieuw opstart.
Controleer of uw beveiligings gegevens juist zijn | Zorg ervoor dat de gegevens van de beveiligings verificatie methode nauw keurig zijn, met name uw telefoon nummers. Als u het verkeerde telefoon nummer plaatst, wordt al uw waarschuwingen naar een onjuist nummer gegaan. Gelukkig kan deze gebruiker niets doen met de waarschuwingen, maar ook niet om u aan te melden bij uw account. Zie de instructies in het artikel [uw twee ledige verificatie methode-instellingen beheren](multi-factor-authentication-end-user-manage-settings.md) om ervoor te zorgen dat uw gegevens correct zijn.
Controleren of uw meldingen zijn ingeschakeld | Controleer of er meldingen zijn ingeschakeld op het mobiele apparaat. Zorg ervoor dat de volgende meldings modi zijn toegestaan: <br/><br/> &bull; Telefoon gesprekken <br/> &bull; Uw verificatie-app <br/> &bull; Uw app voor tekst berichten <br/><br/> Zorg ervoor dat deze modi een waarschuwing maken die _zichtbaar_ is op het apparaat.
Zorg ervoor dat u een signaal voor een apparaat en een Internet verbinding hebt | Zorg ervoor dat uw telefoon gesprekken en SMS-berichten op uw mobiele apparaat terechtkomen. Laat een vriend u bellen en u een SMS-bericht sturen om er zeker van te zijn dat u beide ontvangt. Als u de oproep of tekst niet ontvangt, controleert u eerst of het mobiele apparaat is ingeschakeld. Als uw apparaat is ingeschakeld, maar u nog steeds geen gesprek of tekst ontvangt, is er waarschijnlijk een probleem met uw netwerk. U moet contact opnemen met uw provider. Als u vaak signaal problemen ondervindt, raden we u aan om de [Microsoft Authenticator-app](user-help-auth-app-download-install.md) op uw mobiele apparaat te installeren en te gebruiken. De verificator-app kan wille keurige beveiligings codes genereren voor aanmelden, zonder dat er een celpictogram of Internet verbinding vereist is.
Niet storen uitschakelen | Zorg ervoor dat u de functie **niet Stores** voor uw mobiele apparaat niet hebt ingeschakeld. Als deze functie is ingeschakeld, kunnen meldingen niet worden gewaarschuwd op uw mobiele apparaat. Raadpleeg de hand leiding van uw mobiele apparaat voor instructies over het uitschakelen van deze functie.
Telefoon nummers blok keren | In de Verenigde Staten zijn telefoon aanroepen van micro soft afkomstig van de volgende nummers: + 1 (866) 539 4191, + 1 (855) 330 8653 en + 1 (877) 668 6536.
Controleer de accu-gerelateerde instellingen | Als u de batterij optimalisatie zo instelt dat minder vaak gebruikte apps niet meer actief zijn op de achtergrond, heeft dit waarschijnlijk invloed op uw meldings systeem. Probeer batterij optimalisatie voor zowel uw verificatie-app als uw Messa ging-app uit te scha kelen. Probeer u vervolgens opnieuw aan te melden bij uw account.
Beveiligings-apps van derden uitschakelen | Sommige telefoon beveiligings-apps blok keren tekst berichten en telefoon gesprekken van irritante onbekende bellers. Een beveiligings-app kan ertoe leiden dat uw telefoon de verificatie code niet kan ontvangen. Probeer alle beveiligings-apps van derden uit te scha kelen op uw telefoon en vraag vervolgens een andere verificatie code te verzenden.

## <a name="im-not-being-prompted-for-my-second-verification-information"></a>Ik wordt niet gevraagd om mijn tweede verificatie gegevens

U meldt zich aan bij uw werk-of school account met behulp van uw gebruikers naam en wacht woord. Vervolgens wordt u gevraagd om uw aanvullende informatie over de beveiligings verificatie. Als u niet wordt gevraagd, is het mogelijk dat u uw apparaat nog niet hebt ingesteld. Uw mobiele apparaat moet worden ingesteld om te werken met uw specifieke aanvullende beveiligings verificatie methode.

Misschien hebt u nog geen apparaat ingesteld. Uw mobiele apparaat moet worden ingesteld om te werken met uw specifieke aanvullende beveiligings verificatie methode. Zie [uw instellingen voor twee ledige verificatie methode beheren](multi-factor-authentication-end-user-manage-settings.md)voor de stappen om uw mobiele apparaat beschikbaar te maken voor gebruik met uw verificatie methode. Als u weet dat u uw apparaat of uw account nog niet hebt ingesteld, kunt u de stappen volgen in het artikel [Mijn account instellen voor verificatie in twee stappen](multi-factor-authentication-end-user-first-time.md) .

## <a name="i-have-a-new-phone-number-and-i-want-to-add-it"></a>Ik heb een nieuw telefoon nummer dat ik wil toevoegen

Als u een nieuw telefoon nummer hebt, moet u de details van de beveiligings verificatie methode bijwerken. Hierdoor kunnen uw verificaties vragen naar de juiste locatie. Volg de stappen in het gedeelte **uw telefoon nummer toevoegen of wijzigen** van het artikel uw [instellingen voor twee ledige verificatie methode beheren](multi-factor-authentication-end-user-manage-settings.md#add-or-change-your-phone-number) om uw verificatie methode bij te werken.

## <a name="i-have-a-new-mobile-device-and-i-want-to-add-it"></a>Ik heb een nieuw mobiel apparaat dat ik wil toevoegen

Als u een nieuw mobiel apparaat hebt, moet u dit instellen op het gebruik van twee ledige verificatie. Dit is een oplossing met meerdere stappen:

1. Stel uw apparaat in op samen werking met uw account door de stappen in het artikel [Mijn account instellen voor verificatie in twee stappen](multi-factor-authentication-end-user-first-time.md) te volgen.

1. Werk uw account en apparaatgegevens bij op de pagina **aanvullende beveiligings verificatie** . Voer de update uit door uw oude apparaat te verwijderen en uw nieuwe toe te voegen. Zie het artikel [uw instellingen voor de verificatie methode van twee factoren beheren](multi-factor-authentication-end-user-manage-settings.md) voor meer informatie.

Optionele stappen:

- Installeer de app Microsoft Authenticator op uw mobiele apparaat door de stappen te volgen in het artikel [Microsoft Authenticator app downloaden en installeren](user-help-auth-app-download-install.md) .

- Schakel twee ledige verificatie in voor uw vertrouwde apparaten door de stappen in de sectie met **twee ledige verificatie vragen op een vertrouwd apparaat** in te scha kelen in het artikel [uw instellingen voor twee ledige verificatie methode beheren](multi-factor-authentication-end-user-manage-settings.md#turn-on-two-factor-verification-prompts-on-a-trusted-device) .

## <a name="im-having-problems-signing-in-on-my-mobile-device-while-traveling"></a>Ik ondervind problemen bij het aanmelden op mijn mobiele apparaat tijdens het reizen

Mogelijk vindt u het moeilijker om een verificatie methode met een mobiel apparaat te gebruiken, zoals een SMS-bericht, terwijl u zich op een internationale locatie bevindt. Het is ook mogelijk dat uw mobiele apparaat u de kosten voor roaming kan veroorzaken. Voor deze situatie raden we u aan om de Microsoft Authenticator-app te gebruiken, met de optie om verbinding te maken met een Wi-Fi hot spot. Zie het artikel [Microsoft Authenticator app downloaden en installeren](user-help-auth-app-download-install.md) voor meer informatie over het instellen van de Microsoft Authenticator-app op uw mobiele apparaat.

## <a name="i-cant-get-my-app-passwords-to-work"></a>Ik kan mijn app-wacht woorden niet gebruiken

App-wacht woorden vervangen uw normale wacht woord voor oudere desktop toepassingen die geen ondersteuning bieden voor twee ledige verificatie. Controleer eerst of u het wacht woord juist hebt getypt. Als dat niet het geval is, kunt u een nieuw app-wacht woord maken voor de app. Volg hiervoor de stappen in de sectie **app-wacht woorden maken en verwijderen met behulp van de portal mijn apps** in het artikel [app-wacht woorden beheren voor verificatie in twee stappen](multi-factor-authentication-end-user-app-passwords.md#create-and-delete-app-passwords-from-the-additional-security-verification-page) .

## <a name="i-cant-turn-off-two-factor-verification"></a>Ik kan niet twee ledige verificatie uitschakelen

Als u twee ledige verificatie met uw werk-of school account gebruikt (bijvoorbeeld alain@contoso.com ), betekent dit waarschijnlijk dat uw organisatie heeft besloten deze extra beveiligings functie te gebruiken. Omdat uw organisatie u heeft besloten dat u deze functie moet gebruiken, is het niet mogelijk deze afzonderlijk uit te schakelen. Als u echter twee ledige verificatie met een persoonlijk account gebruikt, alain@outlook.com hebt u de mogelijkheid om de functie in of uit te scha kelen. Zie voor meer informatie over het beheren van twee ledige verificatie voor uw persoonlijke accounts, [het inschakelen van twee ledige verificatie in-of uitschakelen voor uw Microsoft-account](https://support.microsoft.com/help/4028586/microsoft-account-turning-two-step-verification-on-or-off).

Als u twee ledige verificatie niet kunt uitschakelen, kan dit ook worden veroorzaakt door de standaard beveiligings instellingen die zijn toegepast op het niveau van de organisatie. Zie [Wat zijn de standaard beveiligings instellingen?](../fundamentals/concept-fundamentals-security-defaults.md) voor meer informatie over de standaard instellingen van de beveiliging.

## <a name="i-didnt-find-an-answer-to-my-problem"></a>Ik heb geen antwoord op mijn probleem gevonden

Als u deze stappen hebt uitgevoerd, maar nog steeds problemen ondervindt, neemt u contact op met de Help Desk van uw organisatie voor hulp.

## <a name="related-articles"></a>Verwante artikelen:

- [Instellingen voor tweeledige verificatiemethode beheren](multi-factor-authentication-end-user-manage-settings.md)

- [Mijn account instellen voor verificatie in twee stappen](multi-factor-authentication-end-user-first-time.md)

- [Veelgestelde vragen over Microsoft Authenticator-app](user-help-auth-app-faq.md)