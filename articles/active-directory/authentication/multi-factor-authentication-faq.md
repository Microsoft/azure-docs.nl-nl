---
title: Veelgestelde vragen over Azure AD Multi-Factor Authentication-Azure Active Directory
description: Veelgestelde vragen en antwoorden met betrekking tot Azure AD Multi-Factor Authentication.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/14/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1dfe99a2ed57ecc953c69d1bad89e2b2c8816ca3
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/06/2020
ms.locfileid: "96741442"
---
# <a name="frequently-asked-questions-about-azure-ad-multi-factor-authentication"></a>Veelgestelde vragen over Azure AD Multi-Factor Authentication

In deze veelgestelde vragen vindt u antwoorden op veelgestelde vragen over Azure AD Multi-Factor Authentication en het gebruik van de Multi-Factor Authentication-service. Het is onderverdeeld in vragen over de service in het algemeen, facturerings modellen, gebruikers ervaringen en probleem oplossing.

> [!IMPORTANT]
> Met ingang van 1 juli 2019 biedt micro soft geen MFA-server meer voor nieuwe implementaties. Nieuwe klanten die multi-factor Authentication van hun gebruikers willen vereisen, moeten gebruikmaken van Azure AD-Multi-Factor Authentication in de Cloud. Bestaande klanten die MFA-server voorafgaand aan 1 juli hebben geactiveerd, kunnen de nieuwste versie downloaden, toekomstige updates en activerings referenties genereren.
>
> De gegevens die hieronder worden gedeeld met betrekking tot Azure Multi-Factor Authentication-server, zijn alleen van toepassing op gebruikers die al de MFA-server uitvoeren.
>
> Licentie verlening op basis van verbruik is niet langer beschikbaar voor nieuwe klanten vanaf 1 september 2018.
> Met ingang van 1 september 2018 kunnen nieuwe verificatie providers niet meer worden gemaakt. Bestaande auth-providers kunnen blijven gebruiken en worden bijgewerkt. Multi-factor Authentication blijft een beschik bare functie in Azure AD Premium-licenties.

## <a name="general"></a>Algemeen

* [Hoe worden gebruikers gegevens door Azure Multi-Factor Authentication-server verwerkt?](#how-does-azure-multi-factor-authentication-server-handle-user-data)
* [Welke SMS-korte codes worden gebruikt voor het verzenden van SMS-berichten naar mijn gebruikers?](#what-sms-short-codes-are-used-for-sending-sms-messages-to-my-users)

### <a name="how-does-azure-multi-factor-authentication-server-handle-user-data"></a>Hoe worden gebruikers gegevens door Azure Multi-Factor Authentication-server verwerkt?

Met Multi-Factor Authentication-server worden gebruikers gegevens alleen opgeslagen op de on-premises servers. In de cloud worden geen permanente gebruikersgegevens opgeslagen. Wanneer de gebruiker verificatie in twee stappen uitvoert, verzendt Multi-Factor Authentication-server gegevens naar de Azure AD Multi-Factor Authentication-Cloud service voor verificatie. De communicatie tussen Multi-Factor Authentication-server en de Multi-Factor Authentication-Cloud service maakt gebruik van Secure Sockets Layer (SSL) of Transport Layer Security (TLS) via poort 443 uitgaand.

Wanneer verificatie aanvragen worden verzonden naar de Cloud service, worden gegevens verzameld voor verificatie-en gebruiks rapporten. De volgende gegevens velden zijn opgenomen in verificatie Logboeken in twee stappen:

* **Unieke id** (gebruikers naam of on-premises multi-factor Authentication-Server-id)
* **Voor-en achternaam** (optioneel)
* **E-mail adres** (optioneel)
* **Telefoon nummer** (wanneer u een spraak oproep of SMS-verificatie gebruikt)
* **Apparaat-token** (bij gebruik van verificatie van mobiele apps)
* **Verificatie modus**
* **Verificatieresultaat**
* **Multi-Factor Authentication-server naam**
* **Multi-Factor Authentication-server IP-adres**
* **Client-IP** (indien beschikbaar)

De optionele velden kunnen worden geconfigureerd in Multi-Factor Authentication-server.

Het verificatie resultaat (geslaagd of geweigerd) en de reden als het is geweigerd, wordt opgeslagen met de verificatie gegevens. Deze gegevens zijn beschikbaar in verificatie-en gebruiks rapporten.

Zie [gegevens locatie en klant gegevens voor Azure AD-multi-factor Authentication](concept-mfa-data-residency.md)voor meer informatie.

### <a name="what-sms-short-codes-are-used-for-sending-sms-messages-to-my-users"></a>Welke SMS-korte codes worden gebruikt voor het verzenden van SMS-berichten naar mijn gebruikers?

In het Verenigde Staten gebruiken we de volgende korte SMS-codes:

* *97671*
* *69829*
* *51789*
* *99399*

In Canada gebruiken we de volgende korte SMS-codes:

* *759731*
* *673801*

Er is geen gegarandeerde consistentie op SMS-of voice-Multi-Factor Authentication prompt met hetzelfde nummer. In het belang van onze gebruikers kunnen we op elk gewenst moment korte codes toevoegen of verwijderen, omdat we route aanpassingen aanbrengen om de bezorgings mogelijkheden van SMS te verbeteren.

We bieden geen ondersteuning voor korte codes voor landen of regio's, naast de Verenigde Staten en Canada.

## <a name="billing"></a>Billing

U kunt de meeste facturerings vragen beantwoorden door te verwijzen naar de [pagina met multi-factor Authentication prijzen](https://azure.microsoft.com/pricing/details/multi-factor-authentication/) of de documentatie voor [Azure AD-multi-factor Authentication versies en verbruiks abonnementen](concept-mfa-licensing.md).

* [Zijn mijn organisatie in rekening gebracht voor het verzenden van telefoon gesprekken en SMS-berichten die worden gebruikt voor verificatie?](#is-my-organization-charged-for-sending-the-phone-calls-and-text-messages-that-are-used-for-authentication)
* [Factureert het facturerings model per gebruiker voor alle ingeschakelde gebruikers, of alleen voor de gebruikers die een verificatie in twee stappen hebben uitgevoerd?](#does-the-per-user-billing-model-charge-me-for-all-enabled-users-or-just-the-ones-that-performed-two-step-verification)
* [Hoe werkt Multi-Factor Authentication facturering?](#how-does-multi-factor-authentication-billing-work)
* [Is er een gratis versie van Azure AD Multi-Factor Authentication?](#is-there-a-free-version-of-azure-ad-multi-factor-authentication)
* [Kan mijn organisatie op elk gewenst moment scha kelen tussen een facturerings model per gebruiker en per verificatie?](#can-my-organization-switch-between-per-user-and-per-authentication-consumption-billing-models-at-any-time)
* [Kan mijn organisatie op elk gewenst moment scha kelen tussen op verbruik gebaseerde facturering en abonnementen (een model op basis van een licentie)?](#can-my-organization-switch-between-consumption-based-billing-and-subscriptions-a-license-based-model-at-any-time)
* [Moet mijn organisatie identiteiten gebruiken en synchroniseren voor het gebruik van Azure AD Multi-Factor Authentication?](#does-my-organization-have-to-use-and-synchronize-identities-to-use-azure-ad-multi-factor-authentication)

### <a name="is-my-organization-charged-for-sending-the-phone-calls-and-text-messages-that-are-used-for-authentication"></a>Zijn mijn organisatie in rekening gebracht voor het verzenden van telefoon gesprekken en SMS-berichten die worden gebruikt voor verificatie?

Nee, er worden geen kosten in rekening gebracht voor afzonderlijke telefoon gesprekken of SMS-berichten die naar gebruikers worden verzonden via Azure AD Multi-Factor Authentication. Als u een MFA-provider per verificatie gebruikt, wordt u gefactureerd voor elke authenticatie, maar niet voor de gebruikte methode.

Uw gebruikers worden mogelijk in rekening gebracht voor de telefoon gesprekken of SMS-berichten die ze ontvangen, op basis van hun persoonlijke telefoon service.

### <a name="does-the-per-user-billing-model-charge-me-for-all-enabled-users-or-just-the-ones-that-performed-two-step-verification"></a>Factureert het facturerings model per gebruiker voor alle ingeschakelde gebruikers, of alleen voor de gebruikers die een verificatie in twee stappen hebben uitgevoerd?

De facturering is gebaseerd op het aantal gebruikers dat is geconfigureerd voor het gebruik van Multi-Factor Authentication, ongeacht of ze een maand verificatie in twee stappen hebben uitgevoerd.

### <a name="how-does-multi-factor-authentication-billing-work"></a>Hoe werkt Multi-Factor Authentication facturering?

Wanneer u een MFA-provider per gebruiker of per verificatie maakt, wordt het Azure-abonnement van uw organisatie maandelijks gefactureerd op basis van het gebruik. Dit facturerings model is vergelijkbaar met hoe Azure factureert voor gebruik van virtuele machines en Web Apps.

Wanneer u een abonnement voor Azure AD Multi-Factor Authentication koopt, betaalt uw organisatie alleen de jaarlijkse licentie kosten voor elke gebruiker. MFA-licenties en Microsoft 365, Azure AD Premium of Enterprise Mobility + Security bundels worden op deze manier gefactureerd.

Zie [Azure AD-multi-factor Authentication ophalen](concept-mfa-licensing.md)voor meer informatie.

### <a name="is-there-a-free-version-of-azure-ad-multi-factor-authentication"></a>Is er een gratis versie van Azure AD Multi-Factor Authentication?

Standaard instellingen voor beveiliging kunnen worden ingeschakeld in de laag Azure AD Free. Met de standaard instellingen voor beveiliging worden alle gebruikers ingeschakeld voor multi-factor Authentication met behulp van de app Microsoft Authenticator. U kunt geen tekst bericht of telefoon verificatie gebruiken met standaard instellingen voor de beveiliging, alleen de Microsoft Authenticator-app.

Zie [Wat zijn beveiligings standaards?](../fundamentals/concept-fundamentals-security-defaults.md) voor meer informatie.

### <a name="can-my-organization-switch-between-per-user-and-per-authentication-consumption-billing-models-at-any-time"></a>Kan mijn organisatie op elk gewenst moment scha kelen tussen een facturerings model per gebruiker en per verificatie?

Als uw organisatie MFA aanschaft als zelfstandige service met facturering op basis van verbruik, kiest u een facturerings model wanneer u een MFA-provider maakt. U kunt het facturerings model niet wijzigen nadat een MFA-provider is gemaakt. 

Als uw MFA-provider *niet* is gekoppeld aan een Azure AD-Tenant of als u de nieuwe MFA-provider koppelt aan een andere Azure AD-Tenant, worden de gebruikers instellingen en configuratie opties niet overgedragen. Bestaande Azure MFA-servers moeten ook opnieuw worden geactiveerd met behulp van de activeringsreferenties die worden gegenereerd door de nieuwe MFA-provider. Als u de MFA-servers opnieuw activeert om ze te koppelen aan de nieuwe MFA-provider, is dit niet van invloed op telefonische verificatie en verificatie via een sms-bericht. Mobiele app-meldingen werken echter niet meer voor gebruikers totdat ze de mobiele app opnieuw activeren.

Meer informatie over MFA-providers vindt u in [aan de slag met een Azure multi-factor Authentication-provider](concept-mfa-authprovider.md).

### <a name="can-my-organization-switch-between-consumption-based-billing-and-subscriptions-a-license-based-model-at-any-time"></a>Kan mijn organisatie op elk gewenst moment scha kelen tussen op verbruik gebaseerde facturering en abonnementen (een model op basis van een licentie)?

In sommige gevallen Ja.

Als uw Directory een Azure Multi-Factor Authentication provider *per gebruiker* heeft, kunt u MFA-licenties toevoegen. Gebruikers met licenties worden niet meegeteld in de facturering op basis van verbruik per gebruiker. Gebruikers zonder licenties kunnen nog steeds worden ingeschakeld voor MFA via de MFA-provider. Als u licenties aanschaft en toewijst voor alle gebruikers die zijn geconfigureerd voor het gebruik van Multi-Factor Authentication, kunt u de Azure Multi-Factor Authentication-provider verwijderen. U kunt altijd een andere MFA-provider per gebruiker maken als u in de toekomst meer gebruikers dan licenties hebt.

Als uw Directory een Azure Multi-Factor Authentication provider *per verificatie* heeft, wordt u altijd gefactureerd voor elke verificatie, zolang de MFA-provider is gekoppeld aan uw abonnement. U kunt MFA-licenties toewijzen aan gebruikers, maar u wordt nog steeds gefactureerd voor elke verificatie aanvraag in twee stappen, ongeacht of deze afkomstig is van iemand met een MFA-licentie die is toegewezen of niet.

### <a name="does-my-organization-have-to-use-and-synchronize-identities-to-use-azure-ad-multi-factor-authentication"></a>Moet mijn organisatie identiteiten gebruiken en synchroniseren voor het gebruik van Azure AD Multi-Factor Authentication?

Als uw organisatie gebruikmaakt van een facturerings model op basis van verbruik, is Azure Active Directory optioneel, maar niet vereist. Als uw MFA-provider niet is gekoppeld aan een Azure AD-Tenant, kunt u Azure Multi-Factor Authentication-server alleen on-premises implementeren.

Azure Active Directory is vereist voor het licentie model, omdat licenties worden toegevoegd aan de Azure AD-Tenant wanneer u deze aanschaft en toewijst aan gebruikers in de Directory.

## <a name="manage-and-support-user-accounts"></a>Gebruikersaccounts beheren en ondersteunen

* [Wat moet ik vertellen dat mijn gebruikers doen als ze geen antwoord op hun telefoon ontvangen?](#what-should-i-tell-my-users-to-do-if-they-dont-receive-a-response-on-their-phone)
* [Wat moet ik doen als een van mijn gebruikers geen toegang kan krijgen tot hun account?](#what-should-i-do-if-one-of-my-users-cant-get-in-to-their-account)
* [Wat moet ik doen als een van mijn gebruikers een telefoon kwijtraakt die app-wacht woorden gebruikt?](#what-should-i-do-if-one-of-my-users-loses-a-phone-that-is-using-app-passwords)
* [Wat gebeurt er als een gebruiker zich niet kan aanmelden bij niet-browser-apps?](#what-if-a-user-cant-sign-in-to-non-browser-apps)
* [Mijn gebruikers zeggen dat ze het tekst bericht soms niet ontvangen of dat er een time-out optreedt voor de verificatie.](#my-users-say-that-sometimes-they-dont-receive-the-text-message-or-the-verification-times-out)
* [Kan ik de hoeveelheid tijd wijzigen die gebruikers nodig hebben om de verificatie code van een SMS-bericht in te voeren voordat het systeem time-out heeft?](#can-i-change-the-amount-of-time-my-users-have-to-enter-the-verification-code-from-a-text-message-before-the-system-times-out)
* [Kan ik hardware-tokens gebruiken met Azure Multi-Factor Authentication-server?](#can-i-use-hardware-tokens-with-azure-multi-factor-authentication-server)
* [Kan ik Azure Multi-Factor Authentication-server gebruiken om Terminal Services te beveiligen?](#can-i-use-azure-multi-factor-authentication-server-to-secure-terminal-services)
* [Ik heb een beller-ID geconfigureerd in de MFA-server, maar mijn gebruikers ontvangen nog steeds Multi-Factor Authentication aanroepen van een anonieme aanroeper.](#i-configured-caller-id-in-mfa-server-but-my-users-still-receive-multi-factor-authentication-calls-from-an-anonymous-caller)
* [Waarom worden mijn gebruikers gevraagd om hun beveiligings gegevens te registreren?](#why-are-my-users-being-prompted-to-register-their-security-information)

### <a name="what-should-i-tell-my-users-to-do-if-they-dont-receive-a-response-on-their-phone"></a>Wat moet ik vertellen dat mijn gebruikers doen als ze geen antwoord op hun telefoon ontvangen?

Laat uw gebruikers vijf keer in vijf minuten proberen om een telefoon gesprek of SMS voor verificatie te ontvangen. Micro soft gebruikt meerdere providers voor het leveren van aanroepen en SMS-berichten. Als deze aanpak niet werkt, opent u een ondersteunings aanvraag om verder te gaan met het oplossen van problemen.

Beveiligings-apps van derden kunnen ook het tekst bericht of telefoon gesprek met de verificatie code blok keren. Als u een beveiligings-app van derden gebruikt, schakelt u de beveiliging uit en vraagt u een andere MFA-verificatie code te verzenden.

Als de bovenstaande stappen niet werken, controleert u of gebruikers zijn geconfigureerd voor meer dan één verificatie methode. Probeer opnieuw aan te melden, maar selecteer een andere verificatie methode op de aanmeldings pagina.

Zie voor meer informatie de [hand leiding voor probleem oplossing voor eind gebruikers](../user-help/multi-factor-authentication-end-user-troubleshoot.md).

### <a name="what-should-i-do-if-one-of-my-users-cant-get-in-to-their-account"></a>Wat moet ik doen als een van mijn gebruikers geen toegang kan krijgen tot hun account?

U kunt het account van de gebruiker opnieuw instellen door het registratie proces opnieuw door te voeren. Meer informatie over [het beheren van gebruikers-en apparaatinstellingen met Azure AD-multi-factor Authentication in de Cloud](howto-mfa-userdevicesettings.md).

### <a name="what-should-i-do-if-one-of-my-users-loses-a-phone-that-is-using-app-passwords"></a>Wat moet ik doen als een van mijn gebruikers een telefoon kwijtraakt die app-wacht woorden gebruikt?

Verwijder alle app-wacht woorden van de gebruiker om onbevoegde toegang te voor komen. Nadat de gebruiker een vervangend apparaat heeft, kunnen de wacht woorden opnieuw worden gemaakt. Meer informatie over [het beheren van gebruikers-en apparaatinstellingen met Azure AD-multi-factor Authentication in de Cloud](howto-mfa-userdevicesettings.md).

### <a name="what-if-a-user-cant-sign-in-to-non-browser-apps"></a>Wat gebeurt er als een gebruiker zich niet kan aanmelden bij niet-browser-apps?

Als uw organisatie nog steeds verouderde clients gebruikt en u [het gebruik van app-wacht woorden hebt toegestaan](howto-mfa-app-passwords.md), kunnen uw gebruikers zich niet aanmelden bij deze verouderde clients met hun gebruikers naam en wacht woord. In plaats daarvan moeten ze [app-wacht woorden instellen](../user-help/multi-factor-authentication-end-user-app-passwords.md). Uw gebruikers moeten hun aanmeldings gegevens wissen (verwijderen), de app opnieuw starten en u vervolgens aanmelden met de gebruikers naam en het *app-wacht woord* in plaats van het normale wacht woord.

Als uw organisatie geen verouderde clients heeft, mag u uw gebruikers niet toestaan om app-wacht woorden te maken.

> [!NOTE]
> **Moderne authenticatie voor Office 2013-clients**
>
> App-wacht woorden zijn alleen nodig voor apps die moderne authenticatie niet ondersteunen. Office 2013-clients ondersteunen moderne verificatie protocollen, maar moeten worden geconfigureerd. Moderne verificatie is beschikbaar voor elke klant die de update van 2015 maart of hoger voor Office 2013 uitvoert. Zie het blog bericht [Updated Office 365 moderne authenticatie](https://www.microsoft.com/microsoft-365/blog/2015/11/19/updated-office-365-modern-authentication-public-preview/)voor meer informatie.

### <a name="my-users-say-that-sometimes-they-dont-receive-the-text-message-or-the-verification-times-out"></a>Mijn gebruikers zeggen dat ze het tekst bericht soms niet ontvangen of dat er een time-out optreedt voor de verificatie.

Het leveren van SMS-berichten is niet gegarandeerd omdat er niet-instel bare factoren zijn die van invloed kunnen zijn op de betrouw baarheid van de service. Deze factoren omvatten het land of de regio van bestemming, de mobiele telefoon provider en de signaal sterkte.

Beveiligings-apps van derden kunnen ook het tekst bericht of telefoon gesprek met de verificatie code blok keren. Als u een beveiligings-app van derden gebruikt, schakelt u de beveiliging uit en vraagt u een andere MFA-verificatie code te verzenden.

Als uw gebruikers vaak problemen ondervinden met het betrouw bare ontvangen van SMS-berichten, kunt u ze in plaats daarvan de Microsoft Authenticator app of de methode telefoon oproep gebruiken. Het Microsoft Authenticator kan meldingen ontvangen via mobiele en Wi-Fi verbindingen. Daarnaast kan de mobiele app verificatie codes genereren, zelfs wanneer het apparaat helemaal geen signaal heeft. De Microsoft Authenticator-app is beschikbaar voor [Android](https://go.microsoft.com/fwlink/?Linkid=825072), [iOS](https://go.microsoft.com/fwlink/?Linkid=825073) en [Windows Phone](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6).

### <a name="can-i-change-the-amount-of-time-my-users-have-to-enter-the-verification-code-from-a-text-message-before-the-system-times-out"></a>Kan ik de hoeveelheid tijd wijzigen die gebruikers nodig hebben om de verificatie code van een SMS-bericht in te voeren voordat het systeem time-out heeft?

In sommige gevallen moet u Ja.

Voor eenrichtings-SMS met Azure MFA server v 7.0 of hoger kunt u de time-outinstelling configureren door een register sleutel in te stellen. Nadat de MFA-Cloud service het tekst bericht heeft verzonden, wordt de verificatie code (of een eenmalige wachtwoord code) naar de MFA-server geretourneerd. De MFA-server slaat de code standaard 300 seconden op in het geheugen. Als de gebruiker de code niet invoert voordat de 300 seconden zijn verstreken, wordt de verificatie geweigerd. Gebruik de volgende stappen om de standaard time-outinstelling te wijzigen:

1. Ga naar `HKLM\Software\Wow6432Node\Positive Networks\PhoneFactor`.
2. Maak een **DWORD** -register sleutel met de naam *pfsvc_pendingSmsTimeoutSeconds* en stel de tijd in seconden in die u wilt dat de Azure MFA-server eenmalige wachtwoord codes opslaat.

>[!TIP]
>
> Als u meerdere MFA-servers hebt, kent alleen het account dat de oorspronkelijke verificatie aanvraag heeft verwerkt, de verificatie code die naar de gebruiker is verzonden. Wanneer de gebruiker de code invoert, moet de verificatie aanvraag om deze te valideren naar dezelfde server worden verzonden. Als de code validatie naar een andere server wordt verzonden, wordt de verificatie geweigerd.

Als gebruikers niet binnen de gedefinieerde time-outperiode reageren op het SMS, wordt de verificatie geweigerd.

U kunt de time-outinstelling niet configureren voor SMS-eenrichtings verificatie met Azure AD MFA in de Cloud (inclusief de AD FS adapter of de uitbrei ding Network Policy Server). Azure AD slaat de verificatie code voor 180 seconden op.

### <a name="can-i-use-hardware-tokens-with-azure-multi-factor-authentication-server"></a>Kan ik hardware-tokens gebruiken met Azure Multi-Factor Authentication-server?

Als u Azure Multi-Factor Authentication-server gebruikt, kunt u open-time-mobiele TOTP (OATH, one-time password) van derden importeren en deze vervolgens gebruiken voor verificatie in twee stappen.

U kunt ActiveIdentity-tokens gebruiken die OATH mobiele TOTP-tokens zijn als u de geheime sleutel in een CSV-bestand plaatst en naar Azure Multi-Factor Authentication-server importeert. U kunt OATH-tokens gebruiken met Active Directory Federation Services (ADFS), op formulieren gebaseerde verificatie van Internet Information Server (IIS) en Remote Authentication Dial-In User Service (RADIUS) zolang het client systeem de invoer van de gebruiker kan accepteren.

U kunt OATH mobiele TOTP-tokens van derden importeren met de volgende indelingen:  

- Portable symmetrische-sleutel container (PSKC)
- CSV als het bestand een serie nummer, een geheime sleutel in de basis-32-indeling en een tijds interval bevat

### <a name="can-i-use-azure-multi-factor-authentication-server-to-secure-terminal-services"></a>Kan ik Azure Multi-Factor Authentication-server gebruiken om Terminal Services te beveiligen?

Ja, maar als u Windows Server 2012 R2 of hoger gebruikt, kunt u Terminal Services alleen beveiligen met behulp van Extern bureaublad-gateway (RD-gateway).

Wijzigingen in de beveiliging in Windows Server 2012 R2 gewijzigd hoe Azure Multi-Factor Authentication-server verbinding maakt met het LSA-beveiligings pakket (lokale beveiligings autoriteit) in Windows Server 2012 en eerdere versies. Voor versies van Terminal Services in Windows Server 2012 of eerder kunt u [een toepassing beveiligen met Windows-verificatie](howto-mfaserver-windows.md#to-secure-an-application-with-windows-authentication-use-the-following-procedure). Als u Windows Server 2012 R2 gebruikt, hebt u RD-gateway nodig.

### <a name="i-configured-caller-id-in-mfa-server-but-my-users-still-receive-multi-factor-authentication-calls-from-an-anonymous-caller"></a>Ik heb een beller-ID geconfigureerd in de MFA-server, maar mijn gebruikers ontvangen nog steeds Multi-Factor Authentication aanroepen van een anonieme aanroeper.

Wanneer Multi-Factor Authentication aanroepen via het open bare telefoonnet werk worden geplaatst, worden ze soms doorgestuurd via een transporteur die geen beller-ID ondersteunt. Vanwege het verwerkings gedrag van de provider is de beller-ID niet gegarandeerd, zelfs al wordt deze door het Multi-Factor Authentication-systeem altijd verzonden.

### <a name="why-are-my-users-being-prompted-to-register-their-security-information"></a>Waarom worden mijn gebruikers gevraagd om hun beveiligings gegevens te registreren?

Er zijn verschillende redenen waarom gebruikers kunnen worden gevraagd om hun beveiligings gegevens te registreren:

- De gebruiker is ingeschakeld voor MFA door hun beheerder in azure AD, maar er zijn nog geen beveiligings gegevens voor hun account geregistreerd.
- De gebruiker is ingeschakeld voor Self-service voor wachtwoord herstel in azure AD. De beveiligings informatie helpt ze hun wacht woord in de toekomst opnieuw in te stellen als ze het ooit verg eten.
- De gebruiker heeft een toepassing geopend die een beleid voor voorwaardelijke toegang heeft om MFA te vereisen en nog niet eerder is geregistreerd voor MFA.
- De gebruiker registreert een apparaat met Azure AD (met inbegrip van Azure AD-deelname) en voor uw organisatie is MFA vereist voor apparaatregistratie, maar de gebruiker is niet eerder geregistreerd voor MFA.
- De gebruiker genereert Windows hello voor bedrijven in Windows 10 (waarvoor MFA is vereist) en is niet eerder geregistreerd voor MFA.
- De organisatie heeft een MFA-registratie beleid gemaakt en ingeschakeld dat is toegepast op de gebruiker.
- De gebruiker is eerder geregistreerd voor MFA, maar er is een verificatie methode gekozen die een beheerder heeft uitgeschakeld. De gebruiker moet daarom MFA-registratie opnieuw door lopen om een nieuwe standaard verificatie methode te selecteren.

## <a name="errors"></a>Fouten

* [Wat moeten gebruikers doen als het fout bericht ' verificatie aanvraag is niet voor een geactiveerd account ' wordt weer gegeven bij het gebruik van meldingen voor mobiele apps?](#what-should-users-do-if-they-see-an-authentication-request-is-not-for-an-activated-account-error-message-when-using-mobile-app-notifications)
* [Wat moeten gebruikers doen als ze een 0x800434D4L-fout bericht zien wanneer ze zich aanmelden bij een niet-browser toepassing?](#what-should-users-do-if-they-see-a-0x800434d4l-error-message-when-signing-in-to-a-non-browser-application)

### <a name="what-should-users-do-if-they-see-an-authentication-request-is-not-for-an-activated-account-error-message-when-using-mobile-app-notifications"></a>Wat moeten gebruikers doen als het fout bericht ' verificatie aanvraag is niet voor een geactiveerd account ' wordt weer gegeven bij het gebruik van meldingen voor mobiele apps?

Vraag de gebruiker om de volgende procedure uit te voeren om het account uit de Microsoft Authenticator te verwijderen en voeg het vervolgens opnieuw toe:

1. Ga naar [het Azure Portal profiel](https://account.activedirectory.windowsazure.com/profile/) en meld u aan met een organisatie account.
2. Selecteer **aanvullende beveiligings verificatie**.
3. Verwijder het bestaande account uit de app Microsoft Authenticator.
4. Klik op **configureren** en volg de instructies om de Microsoft Authenticator opnieuw te configureren.

### <a name="what-should-users-do-if-they-see-a-0x800434d4l-error-message-when-signing-in-to-a-non-browser-application"></a>Wat moeten gebruikers doen als ze een 0x800434D4L-fout bericht zien wanneer ze zich aanmelden bij een niet-browser toepassing?

De *0x800434D4L* -fout treedt op wanneer u zich probeert aan te melden bij een niet-browser toepassing, die is geïnstalleerd op een lokale computer en niet werkt met accounts waarvoor verificatie in twee stappen is vereist.

Een tijdelijke oplossing voor deze fout is het gebruik van afzonderlijke gebruikers accounts voor bewerkingen die betrekking hebben op beheerders en niet voor beheerders. Later kunt u post vakken koppelen tussen uw beheerders account en een niet-beheerders account, zodat u zich kunt aanmelden bij Outlook met behulp van uw niet-beheerders account. Voor meer informatie over deze oplossing leert u hoe u [een beheerder de mogelijkheid biedt om de inhoud van het postvak van een gebruiker te openen en weer te geven](https://help.outlook.com/141/gg709759.aspx?sl=1).

## <a name="next-steps"></a>Volgende stappen

Als uw vraag hier niet wordt beantwoord, zijn de volgende ondersteunings opties beschikbaar:

* Zoek in de [Microsoft ondersteuning Knowledge Base](https://support.microsoft.com) naar oplossingen voor veelvoorkomende technische problemen.
* Zoek en blader door technische vragen en antwoorden van de Community of vraag uw eigen vraag in het [Azure Active Directory Q&A](/answers/topics/azure-active-directory.html).
* Neem contact op met micro soft Professional via [ondersteuning voor Azure multi-factor Authentication-Server](https://support.microsoft.com/oas/default.aspx?prid=14947). Wanneer u contact met ons opneemt, is het handig als u zoveel mogelijk informatie over uw probleem kunt opnemen. Informatie die u kunt opgeven, omvat de pagina waar u de fout hebt gezien, de specifieke fout code, de specifieke sessie-ID en de ID van de gebruiker die de fout heeft gezien.
* Als u een verouderde Phone factor-klant bent en u vragen hebt of hulp nodig hebt bij het opnieuw instellen van een wacht woord, gebruikt u het [phonefactorsupport@microsoft.com](mailto:phonefactorsupport@microsoft.com) e-mail adres om een ondersteunings aanvraag te openen.