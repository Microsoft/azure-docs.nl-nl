---
title: Configureer bare levens duur van tokens
titleSuffix: Microsoft identity platform
description: Meer informatie over het instellen van de levens duur voor toegangs-, SAML-en ID-tokens die zijn uitgegeven door het micro soft Identity-platform.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 01/04/2021
ms.author: ryanwi
ms.custom: aaddev, identityplatformtop40, content-perf, FY21Q1, contperf-fy21q1
ms.reviewer: hirsin, jlu, annaba
ms.openlocfilehash: ecd7c3b034a65fa239248bf0e286181475376da2
ms.sourcegitcommit: f6f928180504444470af713c32e7df667c17ac20
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/07/2021
ms.locfileid: "97963469"
---
# <a name="configurable-token-lifetimes-in-microsoft-identity-platform-preview"></a>Configureer bare levens duur van tokens in micro soft Identity platform (preview-versie)

U kunt de levens duur opgeven van een toegangs-ID of SAML-token dat is uitgegeven door het micro soft Identity-platform. U kunt de levensduur van een token instellen voor alle apps in uw organisatie, voor een multitenanttoepassing (voor meerdere organisaties) of voor een specifieke service-principal in uw organisatie. We bieden momenteel geen ondersteuning voor het configureren van de levens duur van tokens voor [beheerde ID service-principals](../managed-identities-azure-resources/overview.md).

In azure AD vertegenwoordigt een beleids object een set regels die worden afgedwongen voor afzonderlijke toepassingen of voor alle toepassingen in een organisatie. Elk beleids type heeft een unieke structuur, met een reeks eigenschappen die worden toegepast op objecten waaraan ze zijn toegewezen.

U kunt een beleid instellen als het standaard beleid voor uw organisatie. Het beleid wordt toegepast op alle toepassingen in de organisatie, zolang deze niet worden overschreven door een beleid met een hogere prioriteit. U kunt ook een beleid toewijzen aan specifieke toepassingen. De volg orde van prioriteit is afhankelijk van het beleids type.

Voor voor beelden leest u voor [beelden van het configureren van de levens duur van tokens](configure-token-lifetimes.md).

> [!NOTE]
> Configureerbaar token levensduur beleid is alleen van toepassing op mobiele en desktop-clients die toegang hebben tot share point online en OneDrive voor bedrijven-resources, en is niet van toepassing op webbrowser sessies.
> Als u de levens duur van webbrowser sessies voor share point online en OneDrive voor bedrijven wilt beheren, gebruikt u de functie voor de [levens duur van de voorwaardelijke toegang](../conditional-access/howto-conditional-access-session-lifetime.md) . Raadpleeg de [share point online-blog](https://techcommunity.microsoft.com/t5/SharePoint-Blog/Introducing-Idle-Session-Timeout-in-SharePoint-and-OneDrive/ba-p/119208) voor meer informatie over het configureren van time-outs voor inactieve sessies.

## <a name="license-requirements"></a>Licentievereisten

Voor deze functie hebt u een Azure AD Premium P1-licentie nodig. Zie [Algemeen beschik bare functies van de gratis en Premium-edities vergelijken](https://azure.microsoft.com/pricing/details/active-directory/)om de juiste licentie voor uw vereisten te vinden.

Klanten met een [Microsoft 365 Business-licentie](/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description) hebben ook toegang tot de functies voor voorwaardelijke toegang.

## <a name="token-lifetime-policies-for-access-saml-and-id-tokens"></a>Token levensduur beleid voor toegangs-, SAML-en ID-tokens

U kunt beleid voor levens duur van tokens instellen voor toegangs tokens, SAML-tokens en ID-tokens. 

### <a name="access-tokens"></a>Toegangstokens

Clients gebruiken toegangs tokens om toegang te krijgen tot een beveiligde bron. Een toegangs token kan alleen worden gebruikt voor een specifieke combi natie van gebruiker, client en resource. Toegangs tokens kunnen niet worden ingetrokken en zijn geldig tot de verval datum. Een kwaadwillende actor die een toegangs token heeft verkregen, kan deze gebruiken voor een omvang van de levens duur. Het aanpassen van de levens duur van een toegangs token is een afweging tussen het verbeteren van de systeem prestaties en het verg Roten van de hoeveelheid tijd die de client toegang behoudt nadat het gebruikers account is uitgeschakeld. De systeem prestaties zijn verbeterd door het aantal keren dat een client een nieuw toegangs token moet verkrijgen te verminderen.  De standaard waarde is 1 uur-na 1 uur moet de client het vernieuwings token gebruiken om (meestal op de achtergrond) een nieuw vernieuwings token en toegangs token te verkrijgen.

### <a name="saml-tokens"></a>SAML-tokens

SAML-tokens worden gebruikt door veel web-gebaseerde SAAS-toepassingen en worden verkregen met behulp van het SAML2-protocol eindpunt van Azure Active Directory. Ze worden ook gebruikt door toepassingen die gebruikmaken van WS-Federation. De standaard levensduur van het token is 1 uur. Vanuit het oogpunt van een toepassing wordt de geldigheids periode van het token opgegeven door de NotOnOrAfter-waarde van het `<conditions …>` element in het token. Nadat de geldigheids periode van het token is beëindigd, moet de client een nieuwe verificatie aanvraag initiëren, die vaak wordt vervuld zonder interactief aanmelden als gevolg van de sessie token voor eenmalige aanmelding (SSO).

De waarde van NotOnOrAfter kan worden gewijzigd met behulp `AccessTokenLifetime` van de para meter in een `TokenLifetimePolicy` . Deze wordt ingesteld op de levens duur die in het beleid is geconfigureerd, plus een klok scheefheid factor van vijf minuten.

De bevestigings NotOnOrAfter die in het `<SubjectConfirmationData>` element is opgegeven, wordt niet beïnvloed door de configuratie van de levens duur van het token. 

### <a name="id-tokens"></a>Id-tokens

ID-tokens worden door gegeven aan websites en native clients. ID-tokens bevatten profiel informatie over een gebruiker. Een ID-token is gebonden aan een specifieke combi natie van gebruiker en client. ID-tokens worden beschouwd als geldig tot de verval datum. Normaal gesp roken komt een webtoepassing overeen met de levens duur van de sessie van een gebruiker in de toepassing tot de levens duur van het ID-token dat voor de gebruiker is uitgegeven. U kunt de levens duur van een ID-token aanpassen om te bepalen hoe vaak de webtoepassing de toepassings sessie verloopt en hoe vaak de gebruiker opnieuw moet worden geverifieerd met het micro soft Identity-platform (op de achtergrond of interactief).

### <a name="token-lifetime-policy-properties"></a>Beleids eigenschappen levens duur token

Beleid voor levens duur van tokens is een type beleids object dat de levens duur van tokens bevat. Dit beleid bepaalt hoe lang de toegangs-, SAML-en ID-tokens voor deze bron worden beschouwd als geldig. Als er geen beleid is ingesteld, wordt de standaard levensduur waarde afgedwongen. 

Het verminderen van de eigenschap levens duur van het toegangs token vermindert het risico dat een toegangs token of ID-token gedurende lange tijd wordt gebruikt door een schadelijke actor. (Deze tokens kunnen niet worden ingetrokken.) De afweging is dat de prestaties nadelig worden beïnvloed, omdat de tokens vaker moeten worden vervangen.

Zie [een beleid voor webaanmelding maken](configure-token-lifetimes.md#create-a-policy-for-web-sign-in)voor een voor beeld.

| Eigenschap | Teken reeks eigenschap van beleid | Alleen | Standaard | Minimum | Maximum |
| --- | --- | --- | --- | --- | --- |
| Levens duur van toegangs token |AccessTokenLifetime |Toegangs tokens, ID-tokens, SAML2-tokens |1 uur |10 minuten |1 dag |

> [!NOTE]
> Om ervoor te zorgen dat de webclient van micro soft teams werkt, wordt aanbevolen om AccessTokenLifetime meer dan 15 minuten te houden voor micro soft-teams.

## <a name="token-lifetime-policies-for-refresh-tokens-and-session-tokens"></a>Token levensduur beleid voor het vernieuwen van tokens en sessie tokens

U kunt beleid voor levens duur van tokens instellen voor vernieuwings tokens en sessie tokens.

> [!IMPORTANT]
> Vanaf mei 2020 kunnen nieuwe tenants geen vernieuwings-en sessie token levensduur configureren.  Tenants met een bestaande configuratie kunnen vernieuwings-en sessie token beleid aanpassen tot 30 januari 2021.   Azure Active Directory zal na 30 januari 2021 niet langer de configuratie van bestaande vernieuwings-en sessie tokens in het beleid naleven. U kunt de levens duur van de toegangs-, SAML-en ID-tokens na de buiten gebruiks telling nog steeds configureren.
>
> Als u moet door gaan met het definiëren van de tijds periode voordat een gebruiker wordt gevraagd zich opnieuw aan te melden, configureert u de aanmeldings frequentie in voorwaardelijke toegang. Lees voor meer informatie over voorwaardelijke toegang [verificatie sessie beheer configureren met voorwaardelijke toegang](/azure/active-directory/conditional-access/howto-conditional-access-session-lifetime).
>
> Als u na de pensionering geen gebruik wilt maken van voorwaardelijke toegang, worden uw vernieuwings-en sessie tokens op die datum ingesteld op de [standaard configuratie](#configurable-token-lifetime-properties-after-the-retirement) en kunt u de levens duur ervan niet meer wijzigen.

:::image type="content" source="./media/active-directory-configurable-token-lifetimes/roadmap.svg" alt-text="Buitengebruikstellings gegevens":::

### <a name="refresh-tokens"></a>Tokens vernieuwen

Wanneer een client een toegangs token verkrijgt om toegang te krijgen tot een beveiligde bron, ontvangt de client ook een vernieuwings token. Het vernieuwings token wordt gebruikt om nieuwe token paren voor toegang/vernieuwing op te halen wanneer het huidige toegangs token verloopt. Een vernieuwings token is gebonden aan een combi natie van gebruiker en client. Een vernieuwings token kan [op elk gewenst moment worden ingetrokken](access-tokens.md#token-revocation)en de geldigheid van het token wordt gecontroleerd elke keer dat het token wordt gebruikt.  Vernieuwings tokens worden niet ingetrokken wanneer deze worden gebruikt om nieuwe toegangs tokens op te halen. het is best practice echter om het oude token veilig te verwijderen wanneer er een nieuwe wordt opgehaald.

Het is belang rijk om onderscheid te maken tussen vertrouwelijke clients en open bare clients, omdat dit van invloed is op hoe lang vernieuwings tokens kunnen worden gebruikt. Zie [RFC 6749](https://tools.ietf.org/html/rfc6749#section-2.1)voor meer informatie over verschillende typen clients.

#### <a name="token-lifetimes-with-confidential-client-refresh-tokens"></a>Levens duur van tokens met vertrouwelijke client vernieuwings tokens
Vertrouwelijke clients zijn toepassingen die een client wachtwoord (geheim) veilig kunnen opslaan. Ze kunnen bewijzen dat aanvragen afkomstig zijn van de beveiligde client toepassing en niet van een schadelijke actor. Een web-app is bijvoorbeeld een vertrouwelijke client omdat hiermee een client geheim kan worden opgeslagen op de webserver. Het wordt niet weer gegeven. Omdat deze stromen veiliger zijn, kunnen de standaard levensduur van vernieuwings tokens die worden uitgegeven aan deze stromen `until-revoked` , niet worden gewijzigd met behulp van beleid en worden ze niet ingetrokken voor het opnieuw instellen van het wacht woord.

#### <a name="token-lifetimes-with-public-client-refresh-tokens"></a>Levens duur van tokens met open bare client vernieuwings tokens

Open bare clients kunnen een client wachtwoord (geheim) niet veilig opslaan. Een iOS/Android-app kan bijvoorbeeld geen geheim van de resource-eigenaar maken, dus wordt het beschouwd als een open bare client. U kunt beleids regels instellen op resources om te voor komen dat de vernieuwings tokens van open bare clients ouder dan een opgegeven periode een nieuw token paar voor toegang/vernieuwing verkrijgen. U doet dit door de eigenschap voor het [vernieuwings token (max. inactieve tijd](#refresh-token-max-inactive-time) ) te gebruiken `MaxInactiveTime` . U kunt ook beleid gebruiken om een periode in te stellen waarboven de vernieuwings tokens niet langer worden geaccepteerd. Als u dit wilt doen, gebruikt u de eigenschap maximum leeftijd van het token voor het vernieuwen van de [enkelvoudige factor](#single-factor-session-token-max-age) of het [token met meerdere factoren](#multi-factor-refresh-token-max-age) . U kunt de levens duur van een vernieuwings token aanpassen om te bepalen wanneer en hoe vaak de gebruiker de referenties opnieuw moet invoeren, in plaats van dat deze wordt geauthenticeerd wanneer een open bare client toepassing wordt gebruikt.

> [!NOTE]
> De maximale leeftijds eigenschap is de tijds duur dat één token kan worden gebruikt. 

### <a name="single-sign-on-session-tokens"></a>Sessie tokens voor eenmalige aanmelding
Wanneer een gebruiker zich verifieert met het micro soft Identity-platform, wordt één aanmeldings sessie (SSO) tot stand gebracht met de browser van de gebruiker en het micro soft Identity-platform. De SSO-token, in de vorm van een cookie, vertegenwoordigt deze sessie. Het SSO-sessie token is niet gebonden aan een specifieke bron/client toepassing. SSO-sessie tokens kunnen worden ingetrokken en de geldigheid ervan wordt gecontroleerd elke keer dat ze worden gebruikt.

Micro soft Identity platform gebruikt twee soorten SSO-sessie tokens: permanent en niet-persistent. Permanente sessie tokens worden opgeslagen als permanente cookies door de browser. Niet-permanente sessie tokens worden opgeslagen als sessie cookies. (Sessie cookies worden vernietigd wanneer de browser wordt gesloten.) Normaal gesp roken wordt een niet-persistent sessie token opgeslagen. Maar wanneer de gebruiker het selectie vakje **aangemeld blijven** tijdens de verificatie inschakelt, wordt een persistent sessie token opgeslagen.

Niet-permanente sessie tokens hebben een levens duur van 24 uur. Permanente tokens hebben een levens duur van 90 dagen. Wanneer een SSO-sessie token wordt gebruikt binnen de geldigheids periode, wordt de geldigheids periode nog eens 24 uur of 90 dagen verlengd, afhankelijk van het type token. Als een SSO-sessie token niet binnen de geldigheids periode wordt gebruikt, wordt dit beschouwd als verlopen en wordt het niet langer geaccepteerd.

U kunt een beleid gebruiken om de tijd in te stellen nadat het eerste sessie token is uitgegeven, waarboven het sessie token niet meer wordt geaccepteerd. (Hiervoor gebruikt u de eigenschap maximum leeftijd van sessie token.) U kunt de levens duur van een sessie token aanpassen om te bepalen wanneer en hoe vaak een gebruiker verplicht is om referenties opnieuw in te voeren, in plaats van op de achtergrond te worden geverifieerd wanneer een webtoepassing wordt gebruikt.

### <a name="refresh-and-session-token-lifetime-policy-properties"></a>Eigenschappen van het beleid voor vernieuwen en de levens duur van sessie tokens
Beleid voor levens duur van tokens is een type beleids object dat de levens duur van tokens bevat. Gebruik de eigenschappen van het beleid om de opgegeven levens duur van het token te beheren. Als er geen beleid is ingesteld, wordt de standaard levensduur waarde afgedwongen.

#### <a name="configurable-token-lifetime-properties"></a>Eigenschappen van Configureer bare token levensduur
| Eigenschap | Teken reeks eigenschap van beleid | Alleen | Standaard | Minimum | Maximum |
| --- | --- | --- | --- | --- | --- |
| Maximum aantal inactieve tijd voor het vernieuwen van token |MaxInactiveTime |Tokens vernieuwen |90 dagen |10 minuten |90 dagen |
| Maximum leeftijd van Single-Factor vernieuwings token |MaxAgeSingleFactor |Tokens vernieuwen (voor alle gebruikers) |Until-ingetrokken |10 minuten |Until-ingetrokken<sup>1</sup> |
| Maximum leeftijd van multi-factor Refresh-token |MaxAgeMultiFactor |Tokens vernieuwen (voor alle gebruikers) | 180 dagen |10 minuten |180 dagen<sup>1</sup> |
| Maximum leeftijd van Single-Factor sessie token |MaxAgeSessionSingleFactor |Sessie tokens (permanent en niet permanent) |Until-ingetrokken |10 minuten |Until-ingetrokken<sup>1</sup> |
| Maximale leeftijds duur multi-factor Session-token |MaxAgeSessionMultiFactor |Sessie tokens (permanent en niet permanent) | 180 dagen |10 minuten | 180 dagen<sup>1</sup> |

* <sup>1</sup>365 dagen is de maximale expliciete lengte die voor deze kenmerken kan worden ingesteld.

#### <a name="exceptions"></a>Uitzonderingen
| Eigenschap | Alleen | Standaard |
| --- | --- | --- |
| De maximale leeftijd van het vernieuwings token (uitgegeven voor federatieve gebruikers met onvoldoende intrekkings gegevens<sup>1</sup>) |Tokens vernieuwen (uitgegeven voor federatieve gebruikers met onvoldoende intrekkings gegevens<sup>1</sup>) |12 uur |
| De maximale inactieve tijd voor het vernieuwen van het token (uitgegeven voor vertrouwelijke clients) |Tokens vernieuwen (uitgegeven voor vertrouwelijke clients) |90 dagen |
| Maximale leeftijd van het vernieuwings token (uitgegeven voor vertrouwelijke clients) |Tokens vernieuwen (uitgegeven voor vertrouwelijke clients) |Until-ingetrokken |

* <sup>1</sup> federatieve gebruikers die onvoldoende intrekkings gegevens hebben, zijn alle gebruikers die het kenmerk ' LastPasswordChangeTimestamp ' niet hebben gesynchroniseerd. Deze gebruikers krijgen deze korte maximale leeftijd omdat Azure Active Directory niet kan controleren wanneer tokens worden ingetrokken die zijn gekoppeld aan een oude referentie (zoals een wacht woord dat is gewijzigd) en om ervoor te zorgen dat de gebruiker en de bijbehorende tokens nog steeds in goede staat zijn. Om deze ervaring te verbeteren, moeten Tenant beheerders ervoor zorgen dat ze het kenmerk ' LastPasswordChangeTimestamp ' synchroniseren (dit kan worden ingesteld voor het gebruikers object met behulp van Power shell of via AADSync).

### <a name="configurable-policy-property-details"></a>Details van Configureer bare beleids eigenschap

#### <a name="refresh-token-max-inactive-time"></a>Maximum aantal inactieve tijd voor het vernieuwen van token
**Teken reeks:** MaxInactiveTime

**Van invloed op:** Tokens vernieuwen

**Samen vatting:** Dit beleid bepaalt hoe oud een vernieuwings token kan zijn voordat een client deze niet meer kan gebruiken om een nieuw toegangs-en vernieuwings token paar op te halen wanneer er wordt geprobeerd toegang te krijgen tot deze bron. Omdat een nieuw vernieuwings token meestal wordt geretourneerd wanneer een vernieuwings token wordt gebruikt, voor komt dit beleid dat toegang wordt verkregen als de client toegang tot een bron probeert te krijgen met behulp van het huidige vernieuwings token tijdens de opgegeven periode.

Dit beleid dwingt gebruikers die niet actief zijn geweest op hun client om opnieuw te verifiëren om een nieuw vernieuwings token op te halen.

De eigenschap voor het vernieuwings token voor de maximale inactieve tijd moet worden ingesteld op een lagere waarde dan het Single-Factor maximum leeftijd van het token en de eigenschappen van de maximale leeftijds token voor multi-factor refresh.

Zie voor een voor beeld [een beleid maken voor een systeem eigen app die een web-API aanroept](configure-token-lifetimes.md#create-a-policy-for-a-native-app-that-calls-a-web-api).

#### <a name="single-factor-refresh-token-max-age"></a>Maximum leeftijd van Single-Factor vernieuwings token
**Teken reeks:** MaxAgeSingleFactor

**Van invloed op:** Tokens vernieuwen

**Samen vatting:** Dit beleid bepaalt hoe lang een gebruiker een vernieuwings token kan gebruiken om een nieuw toegangs-en vernieuwings token paar te verkrijgen nadat het voor het laatst is geverifieerd met behulp van slechts één factor. Nadat een gebruiker een nieuw vernieuwings token heeft geverifieerd en ontvangen, kan de gebruiker de stroom voor het vernieuwen van tokens gebruiken voor de opgegeven periode. (Dit geldt zo lang het huidige vernieuwings token niet is ingetrokken en niet langer dan de inactieve tijd niet wordt gebruikt.) Op dat moment wordt de gebruiker gedwongen opnieuw te verifiëren om een nieuw vernieuwings token te ontvangen.

Het verminderen van de maximale leeftijd dwingt gebruikers vaker te verifiëren. Omdat verificatie met één factor minder veilig wordt beschouwd dan multi-factor Authentication, raden we u aan deze eigenschap in te stellen op een waarde die gelijk is aan of kleiner is dan de maximale leeftijds eigenschap van de multi-factor Refresh-token.

Zie voor een voor beeld [een beleid maken voor een systeem eigen app die een web-API aanroept](configure-token-lifetimes.md#create-a-policy-for-a-native-app-that-calls-a-web-api).

#### <a name="multi-factor-refresh-token-max-age"></a>Maximum leeftijd van multi-factor Refresh-token
**Teken reeks:** MaxAgeMultiFactor

**Van invloed op:** Tokens vernieuwen

**Samen vatting:** Dit beleid bepaalt hoe lang een gebruiker een vernieuwings token kan gebruiken om een nieuw toegangs-en vernieuwings token paar te verkrijgen nadat het voor het laatst is geverifieerd met behulp van meerdere factoren. Nadat een gebruiker een nieuw vernieuwings token heeft geverifieerd en ontvangen, kan de gebruiker de stroom voor het vernieuwen van tokens gebruiken voor de opgegeven periode. (Dit geldt zo lang het huidige vernieuwings token niet is ingetrokken en niet langer dan de inactieve tijd niet wordt gebruikt.) Op dat moment worden gebruikers gedwongen opnieuw te verifiëren om een nieuw vernieuwings token te ontvangen.

Het verminderen van de maximale leeftijd dwingt gebruikers vaker te verifiëren. Omdat verificatie met één factor minder veilig wordt beschouwd dan multi-factor Authentication, raden we u aan deze eigenschap in te stellen op een waarde die gelijk is aan of groter is dan de Single-Factor eigenschap voor het vernieuwen van het token maximum leeftijd.

Zie voor een voor beeld [een beleid maken voor een systeem eigen app die een web-API aanroept](configure-token-lifetimes.md#create-a-policy-for-a-native-app-that-calls-a-web-api).

#### <a name="single-factor-session-token-max-age"></a>Maximum leeftijd van Single-Factor sessie token
**Teken reeks:** MaxAgeSessionSingleFactor

**Van invloed op:** Sessie tokens (permanent en niet permanent)

**Samen vatting:** Dit beleid bepaalt hoe lang een gebruiker een sessie token kan gebruiken om een nieuw ID-en sessie token te verkrijgen nadat deze de laatste keer is geverifieerd met behulp van slechts één factor. Nadat een gebruiker een nieuw sessie token heeft geverifieerd en ontvangen, kan de gebruiker de sessie token stroom voor de opgegeven periode gebruiken. (Dit geldt zo lang het token van de huidige sessie niet is ingetrokken en niet is verlopen.) Na de opgegeven periode moet de gebruiker opnieuw worden geverifieerd om een nieuw sessie token te ontvangen.

Het verminderen van de maximale leeftijd dwingt gebruikers vaker te verifiëren. Omdat verificatie met één factor minder veilig wordt beschouwd dan multi-factor Authentication, raden we u aan deze eigenschap in te stellen op een waarde die gelijk is aan of kleiner is dan de maximale leeftijds eigenschap voor het multi-factor-sessie token.

Zie [een beleid voor webaanmelding maken](configure-token-lifetimes.md#create-a-policy-for-web-sign-in)voor een voor beeld.

#### <a name="multi-factor-session-token-max-age"></a>Maximale leeftijds duur multi-factor Session-token
**Teken reeks:** MaxAgeSessionMultiFactor

**Van invloed op:** Sessie tokens (permanent en niet permanent)

**Samen vatting:** Dit beleid bepaalt hoe lang een gebruiker met behulp van meerdere factoren een sessie token kan gebruiken om een nieuw ID-en sessie token te verkrijgen. Nadat een gebruiker een nieuw sessie token heeft geverifieerd en ontvangen, kan de gebruiker de sessie token stroom voor de opgegeven periode gebruiken. (Dit geldt zo lang het token van de huidige sessie niet is ingetrokken en niet is verlopen.) Na de opgegeven periode moet de gebruiker opnieuw worden geverifieerd om een nieuw sessie token te ontvangen.

Het verminderen van de maximale leeftijd dwingt gebruikers vaker te verifiëren. Omdat verificatie met één factor minder veilig wordt beschouwd dan multi-factor Authentication, raden we u aan deze eigenschap in te stellen op een waarde die gelijk is aan of groter is dan de eigenschap Max Age van het Single-Factor sessie token.

## <a name="configurable-token-lifetime-properties-after-the-retirement"></a>Eigenschappen van Configureer bare token levensduur na het buiten gebruik stellen
De configuratie van de vernieuwings-en sessie token wordt beïnvloed door de volgende eigenschappen en waarden die respectievelijk zijn ingesteld. Na de buiten gebruiks telling van de vernieuwings-en sessie token configuratie op 30 januari 2021, voldoet Azure AD alleen voor de standaard waarden die hieronder worden beschreven. Als u besluit om geen gebruik te maken van voorwaardelijke toegang voor het beheren van de aanmeldings frequentie, worden uw vernieuwings-en sessie tokens op die datum ingesteld op de standaard configuratie en kunt u de levens duur ervan niet meer wijzigen.  

|Eigenschap   |Teken reeks eigenschap van beleid    |Alleen |Standaard |
|----------|-----------|------------|------------|
|Levens duur van toegangs token |AccessTokenLifetime |Toegangs tokens, ID-tokens, SAML2-tokens |1 uur |
|Maximum aantal inactieve tijd voor het vernieuwen van token |MaxInactiveTime  |Tokens vernieuwen |90 dagen  |
|Maximum leeftijd van Single-Factor vernieuwings token  |MaxAgeSingleFactor  |Tokens vernieuwen (voor alle gebruikers)  |Until-ingetrokken  |
|Maximum leeftijd van multi-factor Refresh-token  |MaxAgeMultiFactor  |Tokens vernieuwen (voor alle gebruikers) |Until-ingetrokken  |
|Maximum leeftijd van Single-Factor sessie token  |MaxAgeSessionSingleFactor |Sessie tokens (permanent en niet permanent)  |Until-ingetrokken |
|Maximale leeftijds duur multi-factor Session-token  |MaxAgeSessionMultiFactor  |Sessie tokens (permanent en niet permanent)  |Until-ingetrokken |

U kunt Power shell gebruiken om het beleid te vinden dat wordt beïnvloed door de buiten gebruiks telling.  Gebruik de [Power shell-cmdlets](configure-token-lifetimes.md#get-started) om het beleid te bekijken dat is gemaakt in uw organisatie, of om te vinden welke apps en service-principals zijn gekoppeld aan een specifiek beleid.

## <a name="policy-evaluation-and-prioritization"></a>Beleids evaluatie en prioriteits aanduiding
U kunt een token levensduur beleid maken en vervolgens toewijzen aan een specifieke toepassing, aan uw organisatie en aan service-principals. Meerdere beleids regels zijn mogelijk van toepassing op een specifieke toepassing. De levens duur van het token beleid dat van kracht is volgt deze regels:

* Als een beleid expliciet is toegewezen aan de Service-Principal, wordt dit afgedwongen.
* Als er geen beleid expliciet is toegewezen aan de Service-Principal, wordt een beleid afgedwongen dat expliciet is toegewezen aan de bovenliggende organisatie van de Service-Principal.
* Als er geen beleid expliciet is toegewezen aan de service-principal of aan de organisatie, wordt het beleid dat aan de toepassing is toegewezen, afgedwongen.
* Als er geen beleid is toegewezen aan de Service-Principal, de organisatie of het toepassings object, worden de standaard waarden afgedwongen. (Zie de tabel in [configuratie van levens duur van Configureer bare tokens](#configurable-token-lifetime-properties-after-the-retirement).)

Zie [Application and Service Principal Objects in azure Active Directory](app-objects-and-service-principals.md)voor meer informatie over de relatie tussen toepassings objecten en Service-Principal-objecten.

De geldigheid van een token wordt geëvalueerd op het moment dat het token wordt gebruikt. Het beleid met de hoogste prioriteit voor de toepassing die wordt geopend, treedt in werking.

Alle TimeSpans die hier worden gebruikt, zijn ingedeeld volgens het C# [time](/dotnet/api/system.timespan) -object-D. uu: mm: SS.  80 dagen en 30 minuten zouden zijn `80.00:30:00` .  De voorloop D kan worden verwijderd als de waarde nul is, dus 90 minuten `00:90:00` .  

### <a name="example-scenario"></a>Voorbeeldscenario

Een gebruiker wil toegang tot twee webtoepassingen: Web Application A en Web Application B.

Elementen
* Beide webtoepassingen bevinden zich in dezelfde bovenliggende organisatie.
* Het token levensduur beleid 1 met een sessie token van Maxi maal acht uur is ingesteld als de standaard waarde van de bovenliggende organisatie.
* Webtoepassing A is een reguliere webtoepassing en is niet gekoppeld aan een beleid.
* Web Application B wordt gebruikt voor zeer gevoelige processen. De Service-Principal is gekoppeld aan het token levensduur beleid 2, dat een sessie token heeft van een maximum leeftijd van 30 minuten.

Om 12:00 uur wordt een nieuwe browser sessie gestart en wordt geprobeerd toegang te krijgen tot webtoepassing A. De gebruiker wordt omgeleid naar het micro soft-identiteits platform en wordt gevraagd zich aan te melden. Hiermee maakt u een cookie met een sessie token in de browser. De gebruiker wordt teruggeleid naar de webtoepassing A met een ID-token waarmee de gebruiker toegang kan krijgen tot de toepassing.

Om 12:15 uur, probeert de gebruiker toegang tot Web Application B te krijgen. De browser wordt omgeleid naar het micro soft Identity-platform, dat de sessie cookie detecteert. De service-principal van Web Application B is gekoppeld aan het token levensduur beleid 2, maar maakt ook deel uit van de bovenliggende organisatie, met het standaard token levensduur beleid 1. Het token levensduur beleid 2 treedt in werking omdat het beleid dat is gekoppeld aan service-principals een hogere prioriteit heeft dan het standaard beleid van de organisatie. Het sessie token is oorspronkelijk in de afgelopen 30 minuten uitgegeven. dit wordt dus als geldig beschouwd. De gebruiker wordt teruggeleid naar Web Application B met een ID-token waarmee ze toegang krijgen.

Bij 1:00 uur probeert de gebruiker webtoepassing A te openen. De gebruiker wordt omgeleid naar het micro soft Identity-platform. Webtoepassing A is niet gekoppeld aan een beleid, maar omdat het zich in een organisatie met een standaard token levensduur beleid 1 bevindt, wordt dat beleid van kracht. De sessie cookie die oorspronkelijk in de afgelopen acht uur is uitgegeven, wordt gedetecteerd. De gebruiker wordt op de achtergrond teruggeleid naar de webtoepassing A met een nieuw ID-token. De gebruiker is niet verplicht om te verifiëren.

Vervolgens probeert de gebruiker toegang tot Web Application B te krijgen. De gebruiker wordt omgeleid naar het micro soft Identity-platform. Net als voorheen heeft het token levensduur beleid 2 van kracht. Omdat het token meer dan 30 minuten geleden is uitgegeven, wordt de gebruiker gevraagd om de aanmeldings referenties opnieuw in te voeren. Er worden een merk-nieuwe sessie token en ID-token uitgegeven. De gebruiker kan vervolgens toegang tot Web Application B krijgen.

## <a name="cmdlet-reference"></a>Cmdlet-naslaginformatie

Dit zijn de cmdlets in de [module Azure Active Directory Power shell voor Graph preview](/powershell/module/azuread/?view=azureadps-2.0-preview#service-principals&preserve-view=true&preserve-view=true).

### <a name="manage-policies"></a>Beleid beheren

U kunt de volgende cmdlets gebruiken voor het beheren van beleids regels.

| Cmdlet | Beschrijving | 
| --- | --- |
| [New-AzureADPolicy](/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) | Hiermee maakt u een nieuw beleid. |
| [Get-AzureADPolicy](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) | Hiermee haalt u alle Azure AD-beleids regels of een opgegeven beleid op. |
| [Get-AzureADPolicyAppliedObject](/powershell/module/azuread/get-azureadpolicyappliedobject?view=azureadps-2.0-preview&preserve-view=true) | Hiermee worden alle apps en service-principals opgehaald die zijn gekoppeld aan een beleid. |
| [Set-AzureADPolicy](/powershell/module/azuread/set-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) | Hiermee wordt een bestaand beleid bijgewerkt. |
| [Remove-AzureADPolicy](/powershell/module/azuread/remove-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) | Hiermee verwijdert u het opgegeven beleid. |

### <a name="application-policies"></a>Toepassingsbeleid
U kunt de volgende cmdlets voor toepassings beleid gebruiken.</br></br>

| Cmdlet | Beschrijving | 
| --- | --- |
| [Add-AzureADApplicationPolicy](/powershell/module/azuread/add-azureadapplicationpolicy?view=azureadps-2.0-preview&preserve-view=true) | Hiermee wordt het opgegeven beleid gekoppeld aan een toepassing. |
| [Get-AzureADApplicationPolicy](/powershell/module/azuread/get-azureadapplicationpolicy?view=azureadps-2.0-preview&preserve-view=true) | Hiermee wordt het beleid opgehaald dat is toegewezen aan een toepassing. |
| [Remove-AzureADApplicationPolicy](/powershell/module/azuread/remove-azureadapplicationpolicy?view=azureadps-2.0-preview&preserve-view=true) | Hiermee verwijdert u een beleid van een toepassing. |

### <a name="service-principal-policies"></a>Service-Principal-beleid
U kunt de volgende cmdlets gebruiken voor Service Principal-beleids regels.

| Cmdlet | Beschrijving | 
| --- | --- |
| [Add-AzureADServicePrincipalPolicy](/powershell/module/azuread/add-azureadserviceprincipalpolicy?view=azureadps-2.0-preview&preserve-view=true) | Hiermee wordt het opgegeven beleid gekoppeld aan een service-principal. |
| [Get-AzureADServicePrincipalPolicy](/powershell/module/azuread/get-azureadserviceprincipalpolicy?view=azureadps-2.0-preview&preserve-view=true) | Hiermee wordt een beleid opgehaald dat is gekoppeld aan de opgegeven service-principal.|
| [Remove-AzureADServicePrincipalPolicy](/powershell/module/azuread/remove-azureadserviceprincipalpolicy?view=azureadps-2.0-preview&preserve-view=true) | Hiermee verwijdert u het beleid van de opgegeven service-principal.|

## <a name="next-steps"></a>Volgende stappen

Meer informatie vindt u in [voor beelden van het configureren van de levens duur van tokens](configure-token-lifetimes.md).