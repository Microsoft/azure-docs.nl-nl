---
title: Wat is risico? Azure AD-identiteitsbeveiliging
description: Risico's in Azure AD Identity Protection
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 04/13/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 87516ddcce32ab205b13139c057a2ab999146b74
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107376355"
---
# <a name="what-is-risk"></a>Wat is risico?

Risicodetecties in Azure AD Identity Protection alle geïdentificeerde verdachte acties met betrekking tot gebruikersaccounts in de directory.

Identity Protection biedt organisaties toegang tot krachtige resources om deze verdachte acties snel te bekijken en te reageren. 

![Beveiligingsoverzicht met riskante gebruikers en aanmeldingen](./media/concept-identity-protection-risks/identity-protection-security-overview.png)

> [!NOTE]
> Identity Protection genereert alleen risicodetecties wanneer de juiste referenties worden gebruikt. Als er onjuiste referenties worden gebruikt bij een aanmelding, bestaat er geen risico op referentierisico's.

## <a name="risk-types-and-detection"></a>Risicotypen en detectie

Er zijn twee soorten risico **Gebruiker** en **Aanmelden en** twee typen detectie of berekening **Realtime** en **Offline.**

Realtime-detecties worden mogelijk vijf tot tien minuten niet in rapportages aan het werk. Offlinedetecties worden mogelijk twee tot 24 uur niet in rapportages aan het werk gezet.

### <a name="user-risk"></a>Gebruikersrisico

Een gebruikersrisico vertegenwoordigt de waarschijnlijkheid dat een bepaalde identiteit of account is aangetast. 

Deze risico's worden offline berekend met behulp van de interne en externe bedreigingsinformatiebronnen van Microsoft, waaronder beveiligingsonderzoekers, advocaten, beveiligingsteams bij Microsoft en andere vertrouwde bronnen.

| Risicodetectie | Description |
| --- | --- |
| Gelekte referenties | Dit type risicodetectie geeft aan dat de geldige referenties van de gebruiker zijn gelekt. Wanneer cybercriminals geldige wachtwoorden van legitieme gebruikers in gevaar brengen, delen ze deze referenties vaak. Dit delen wordt doorgaans gedaan door openbaar te plaatsen op het donkere web, sites te plakken of door de referenties op de zwarte markt te verkopen en te verkopen. Wanneer de service met gelekte referenties van Microsoft gebruikersreferenties verkrijgt van het donkere web, sites voor plakken of andere bronnen, worden deze gecontroleerd op basis van de huidige geldige referenties van Azure AD-gebruikers om geldige overeenkomsten te vinden. Zie Veelvoorkomende vragen voor meer informatie over [gelekte referenties.](#common-questions) |
| Azure AD-bedreigingsinformatie | Dit type risicodetectie geeft aan dat gebruikersactiviteit ongebruikelijk is voor de opgegeven gebruiker of consistent is met bekende aanvalspatronen op basis van de interne en externe bedreigingsinformatiebronnen van Microsoft. |

### <a name="sign-in-risk"></a>Aanmeldingsrisico

Een aanmeldingsrisico vertegenwoordigt de waarschijnlijkheid dat een bepaalde verificatieaanvraag niet is geautoriseerd door de identiteitseigenaar. 

Deze risico's kunnen in realtime worden berekend of offline worden berekend met behulp van de interne en externe bedreigingsinformatiebronnen van Microsoft, waaronder beveiligingsonderzoekers, professionals op het gebied van wetshandhaving, beveiligingsteams bij Microsoft en andere vertrouwde bronnen.

| Risicodetectie | Detectietype | Beschrijving |
| --- | --- | --- |
| Anoniem IP-adres | Real-time | Dit type risicodetectie duidt op aanmeldingen vanaf een anoniem IP-adres (bijvoorbeeld Tor-browser of anonieme VPN). Deze IP-adressen worden doorgaans gebruikt door actors die hun aanmeldings-telemetrie (IP-adres, locatie, apparaat, enzovoort) willen verbergen voor mogelijk schadelijke intenties. |
| Ongewoon traject | Offline | Dit type risicodetectie identificeert twee aanmeldingen die afkomstig zijn van geografisch verafgelegen locaties, waarbij ten minste één van de locaties, gezien het gedrag in het verleden, ook atypisch kan zijn voor de gebruiker. Naast verschillende andere factoren houdt dit machine learning-algoritme rekening met de tijd tussen de twee aanmeldingen en de tijd die het zou hebben gehad om de gebruiker van de eerste locatie naar de tweede locatie te laten gaan, wat aangeeft dat een andere gebruiker dezelfde referenties gebruikt. <br><br> Het algoritme negeert duidelijke 'fout-positieven' die bijdragen aan de onmogelijke reisomstandigheden, zoals VPN's en locaties die regelmatig worden gebruikt door andere gebruikers in de organisatie. Het systeem heeft een eerste leerperiode van de vroegste van 14 dagen of tien aanmeldingen, waarin het aanmeldingsgedrag van een nieuwe gebruiker wordt geleerd. |
| Aan malware gekoppeld IP-adres | Offline | Dit type risicodetectie duidt op aanmeldingen van IP-adressen die zijn geïnfecteerd met malware die actief communiceren met een botserver. Deze detectie wordt bepaald door IP-adressen van het apparaat van de gebruiker te correuleren met IP-adressen die in contact waren met een botserver terwijl de botserver actief was. |
| Onbekende aanmeldingseigenschappen | Real-time | Dit type risicodetectie kijkt naar eerdere aanmeldingsgeschiedenis (IP, breedtegraad/lengtegraad en ASN) om te zoeken naar afwijkende aanmeldingen. Het systeem slaat informatie op over eerdere locaties die door een gebruiker worden gebruikt en overweegt deze 'vertrouwde' locaties. De risicodetectie wordt geactiveerd wanneer de aanmelding plaatsvindt vanaf een locatie die nog niet in de lijst met vertrouwde locaties staat. Nieuwe gebruikers worden voor een bepaalde periode in de 'leermodus' gebracht, waarin onbekende detecties van aanmeldingseigenschappen worden uitgeschakeld terwijl onze algoritmen het gedrag van de gebruiker leren kennen. De duur van de leermodus is dynamisch en is afhankelijk van de tijd die het algoritme nodig heeft om voldoende informatie over de aanmeldingspatronen van de gebruiker te verzamelen. De minimale duur is vijf dagen. Een gebruiker kan na een lange periode van inactiviteit teruggaan naar de leermodus. Het systeem negeert ook aanmeldingen van vertrouwde apparaten en locaties die zich geografisch dicht bij een vertrouwde locatie bevinden. <br><br> We voeren deze detectie ook uit voor basisverificatie (of verouderde protocollen). Omdat deze protocollen geen moderne eigenschappen hebben, zoals client-id, is er beperkte telemetrie om fout-positieven te verminderen. We raden onze klanten aan om over te schakelen naar moderne verificatie. |
| Door beheerder bevestigd misbruik van gebruiker | Offline | Deze detectie geeft aan dat een beheerder 'Gebruiker gecompromitteerd bevestigen' heeft geselecteerd in de gebruikersinterface van riskante gebruikers of met behulp van de API riskyUsers. Als u wilt zien welke beheerder heeft bevestigd dat deze gebruiker is gecompromitteerd, controleert u de risicogeschiedenis van de gebruiker (via de gebruikersinterface of API). |
| Schadelijk IP-adres | Offline | Deze detectie duidt op aanmelding vanaf een schadelijk IP-adres. Een IP-adres wordt beschouwd als kwaadwillend op basis van hoge foutpercentages vanwege ongeldige referenties die zijn ontvangen van het IP-adres of andere IP-reputatiebronnen. |
| Verdachte bewerkingsregels voor Postvak IN | Offline | Deze detectie wordt gedetecteerd door [Microsoft Cloud App Security (MCAS)](/cloud-app-security/anomaly-detection-policy#suspicious-inbox-manipulation-rules). Deze detectie profileert uw omgeving en activeert waarschuwingen wanneer verdachte regels voor het verwijderen of verplaatsen van berichten of mappen zijn ingesteld in het Postvak IN van een gebruiker. Deze detectie kan erop wijzen dat het account van de gebruiker is aangetast, dat berichten opzettelijk worden verborgen en dat het postvak wordt gebruikt om spam of malware in uw organisatie te distribueren. |
| Wachtwoordspray | Offline | Bij een wachtwoordsprayaanval worden meerdere gebruikersnamen aangevallen met behulp van veelvoorkomende wachtwoorden op een uniforme brute force-manier om onbevoegde toegang te krijgen. Deze risicodetectie wordt geactiveerd wanneer er een wachtwoordsprayaanval is uitgevoerd. |
| Onmogelijk traject | Offline | Deze detectie wordt gedetecteerd door [Microsoft Cloud App Security (MCAS)](/cloud-app-security/anomaly-detection-policy#impossible-travel). Deze detectie identificeert twee gebruikersactiviteiten (één of meerdere sessies) die afkomstig zijn van geografisch verafgelegen locaties binnen een tijdsperiode die korter is dan de tijd die de gebruiker nodig zou hebben gehad om van de eerste locatie naar de tweede locatie te gaan, wat aangeeft dat een andere gebruiker dezelfde referenties gebruikt. |
| Nieuw land | Offline | Deze detectie wordt gedetecteerd door [Microsoft Cloud App Security (MCAS)](/cloud-app-security/anomaly-detection-policy#activity-from-infrequent-country). Deze detectie bekijkt eerdere activiteitlocaties om nieuwe en niet-frequente locaties vast te stellen. De engine voor de detectie van afwijkingen slaat informatie op over eerdere locaties die worden gebruikt door gebruikers in de organisatie. |
| Activiteit vanaf anoniem IP-adres | Offline | Deze detectie wordt gedetecteerd door [Microsoft Cloud App Security (MCAS)](/cloud-app-security/anomaly-detection-policy#activity-from-anonymous-ip-addresses). Deze detectie identificeert dat gebruikers actief waren vanaf een IP-adres dat is geïdentificeerd als een anoniem proxy-IP-adres. |
| Verdachte doorstuuractiviteit voor Postvak IN | Offline | Deze detectie wordt gedetecteerd door [Microsoft Cloud App Security (MCAS)](/cloud-app-security/anomaly-detection-policy#suspicious-inbox-forwarding). Deze detectie zoekt naar verdachte regels voor het doorsturen van e-mail, bijvoorbeeld als een gebruiker een regel voor postvak IN heeft gemaakt die een kopie van alle e-mailberichten doorst sturen naar een extern adres. |

### <a name="other-risk-detections"></a>Andere risicodetecties

| Risicodetectie | Detectietype | Description |
| --- | --- | --- |
| Extra risico gedetecteerd | Realtime of offline | Deze detectie geeft aan dat een van de bovenstaande Premium-detecties is gedetecteerd. Omdat de Premium-detecties alleen zichtbaar zijn voor Azure AD Premium P2-klanten, krijgen ze de titel 'extra risico gedetecteerd' voor klanten zonder Azure AD Premium P2-licenties. |

## <a name="common-questions"></a>Veelgestelde vragen

### <a name="risk-levels"></a>Risiconiveaus

Met Identity Protection wordt het risico gecategoriseerd in drie niveaus: laag, gemiddeld en hoog. Wanneer u aangepast [beleid voor identiteitsbeveiliging configureert,](./concept-identity-protection-policies.md#custom-conditional-access-policy)kunt u dit ook configureren om te activeren bij **Geen risiconiveau.** Geen risico betekent dat er geen actieve indicatie is dat de identiteit van de gebruiker is aangetast.

Microsoft biedt geen specifieke details over de manier waarop het risico wordt berekend, maar hoe hoger het niveau is, hoe betrouwbaarder het is dat de gebruiker of het aanmelden is gecompromitteerd. Een voorbeeld: één geval van onbekende aanmeldingseigenschappen voor een gebruiker kan minder bedreigend zijn dan gelekte referenties voor een andere gebruiker.

### <a name="password-hash-synchronization"></a>Synchronisatie van wachtwoord-hashes

Risicodetecties zoals gelekte referenties vereisen de aanwezigheid van wachtwoordhashes om detectie uit te voeren. Zie voor meer informatie over wachtwoord-hashsynchronisatie het artikel [Wachtwoordhashsynchronisatie implementeren met Azure AD Connect synchronisatie.](../hybrid/how-to-connect-password-hash-synchronization.md)

### <a name="leaked-credentials"></a>Gelekte referenties

#### <a name="where-does-microsoft-find-leaked-credentials"></a>Waar vindt Microsoft gelekte referenties?

Microsoft vindt gelekte referenties op verschillende plaatsen, waaronder:

- Openbare plaksites, zoals pastebin.com en paste.ca waar slechte actoren dergelijk materiaal doorgaans plaatsen. Deze locatie is de eerste keer dat de meeste actoren stoppen bij hun zoek naar gestolen referenties.
- Wetshandhavingsinstanties.
- Andere groepen bij Microsoft die donker webonderzoek doen.

#### <a name="why-arent-i-seeing-any-leaked-credentials"></a>Waarom zie ik geen gelekte referenties?

Gelekte referenties worden verwerkt wanneer Microsoft een nieuwe, openbaar beschikbare batch vindt. Vanwege de gevoelige aard worden de gelekte referenties kort na de verwerking verwijderd. Alleen nieuwe gelekte referenties die zijn gevonden nadat u wachtwoord-hashsynchronisatie (PHS) hebt ingeschakeld, worden verwerkt voor uw tenant. Er wordt geen verificatie uitgevoerd van eerder gevonden referentieparen. 

#### <a name="i-havent-seen-any-leaked-credential-risk-events-for-quite-some-time"></a>Ik heb al een hele tijd geen gelekte referentierisicogebeurtenissen gezien?

Als u geen gelekte referentierisicogebeurtenissen hebt gezien, heeft dit de volgende oorzaken:

- PhS is niet ingeschakeld voor uw tenant.
- Microsoft heeft geen gelekte referentieparen gevonden die overeenkomen met uw gebruikers.

#### <a name="how-often-does-microsoft-process-new-credentials"></a>Hoe vaak verwerkt Microsoft nieuwe referenties?

Referenties worden onmiddellijk verwerkt nadat ze zijn gevonden, normaal gesproken in meerdere batches per dag.

## <a name="next-steps"></a>Volgende stappen

- [Policies available to mitigate risks](concept-identity-protection-policies.md) (Beschikbare beleidsregels om risico's te beperken)
- [Beveiligingsoverzicht](concept-identity-protection-security-overview.md)