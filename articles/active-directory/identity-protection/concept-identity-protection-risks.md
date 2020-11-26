---
title: Wat is risico? Azure AD-identiteitsbeveiliging
description: Risico's in Azure AD Identity Protection uitleggen
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 11/09/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: f10d8a94be53780f732112c012600a7fb840642b
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96180639"
---
# <a name="what-is-risk"></a>Wat is risico?

Risico detecties in Azure AD Identity Protection omvatten alle geïdentificeerde verdachte acties die betrekking hebben op gebruikers accounts in de Directory.

Identiteits beveiliging biedt organisaties toegang tot krachtige bronnen om snel deze verdachte acties te bekijken en te beantwoorden. 

![Beveiligings overzicht met Risk ante gebruikers en aanmeldingen](./media/concept-identity-protection-risks/identity-protection-security-overview.png)

> [!NOTE]
> Identiteits beveiliging genereert alleen risico detecties wanneer de juiste referenties worden gebruikt. Als er onjuiste referenties worden gebruikt voor een aanmelding, vertegenwoordigt het geen risico van inbreuk op referenties.

## <a name="risk-types-and-detection"></a>Risico typen en detectie

Er zijn twee soorten risico **gebruiker** en **aanmelding** , en twee typen detectie of berekeningen in **realtime** en **offline**.

Realtime detecties kunnen vijf tot tien minuten niet worden weer gegeven in rapportage. Offline detecties kunnen niet twee tot twintig uur worden weer gegeven in rapportage.

### <a name="user-risk"></a>Gebruikersrisico

Een gebruikers risico vertegenwoordigt de waarschijnlijkheid dat een bepaalde identiteit of een bepaald account is aangetast. 

Deze Risico's worden offline berekend met de interne en externe Threat Intelligence-bronnen van micro soft, waaronder beveiligings onderzoekers, politie professionals, beveiligings teams bij micro soft en andere vertrouwde bronnen.

| Risico detectie | Description |
| --- | --- |
| Gelekte referenties | Dit type risico detectie geeft aan dat de geldige referenties van de gebruiker zijn gelekt. Wanneer Cybercriminals geldige wacht woorden van legitieme gebruikers beveiligt, delen ze deze referenties vaak. Dit delen geschiedt doorgaans door op het donkere web te plaatsen, sites te plakken of door de referenties op de zwarte markt te verhandelen en te verkopen. Wanneer de micro soft lekkende referentie Service gebruikers referenties ophaalt van de donkere websites, plak sites of andere bronnen, worden ze gecontroleerd op basis van de huidige geldige referenties van de Azure AD-gebruikers om geldige overeenkomsten te vinden. Zie [Veelgestelde vragen](#common-questions)voor meer informatie over gelekte referenties. |
| Azure AD-bedreigingsinformatie | Dit type risico detectie duidt op de activiteit van de gebruiker die ongebruikelijk is voor de gegeven gebruiker of is consistent met bekende aanvals patronen op basis van de interne en externe informatie bronnen van micro soft. |

### <a name="sign-in-risk"></a>Aanmeldingsrisico

Een aanmeldings risico duidt op de kans dat een bepaalde verificatie aanvraag niet is geautoriseerd door de eigenaar van de identiteit. 

Deze Risico's kunnen in realtime worden berekend of offline worden berekend met behulp van de interne en externe Threat Intelligence-bronnen van micro soft, waaronder beveiligings onderzoekers, politie professionals, beveiligings teams bij micro soft en andere vertrouwde bronnen.

| Risico detectie | Detectie type | Description |
| --- | --- | --- |
| Anoniem IP-adres | Real-time | Dit type risico detectie duidt op aanmeldingen vanaf een anoniem IP-adres (bijvoorbeeld Tor browser of anoniem VPN). Deze IP-adressen worden meestal gebruikt door Actors die hun aanmeld telemetrie (IP-adres, locatie, apparaat enzovoort) willen verbergen voor mogelijk schadelijk doel. |
| Ongewoon traject | Offline | Dit type risico detectie identificeert twee aanmeldingen die afkomstig zijn van geografische locaties, waarbij ten minste één van de locaties mogelijk ook ongewoon kan zijn voor de gebruiker, gezien het gedrag van het verleden. Deze machine learning-algoritme houdt onder andere rekening met de tijd tussen de twee aanmeldingen en de tijd die nodig zou zijn voor de gebruiker om van de eerste locatie naar de tweede te gaan, wat aangeeft dat een andere gebruiker dezelfde referenties gebruikt. <br><br> Het algoritme negeert duidelijke ' fout-positieven ' die bijdragen aan de niet-bewaarde reis omstandigheden, zoals Vpn's en locaties die regel matig door andere gebruikers in de organisatie worden gebruikt. Het systeem heeft een initiële leer periode van het eerste aantal van 14 dagen of 10 aanmeldingen, waarbij het een nieuwe gebruiker aanmeldt. |
| Aan malware gekoppeld IP-adres | Offline | Dit type risico detectie duidt op aanmeldingen van IP-adressen die zijn geïnfecteerd met malware waarvan bekend is dat deze actief communiceert met een bot-server. Deze detectie wordt bepaald door de IP-adressen van het apparaat van de gebruiker te correleren met IP-adressen die in contact komen met een bot-server terwijl de bot-server actief was. |
| Onbekende aanmeldingseigenschappen | Real-time | Dit type risico detectie beschouwt eerdere aanmeldings geschiedenis (IP, breedte graad/lengte graad en ASN) om te zoeken naar afwijkende aanmeldingen. Het systeem slaat informatie op over eerdere locaties die door een gebruiker worden gebruikt en beschouwt deze bekende locaties. De risico detectie wordt geactiveerd wanneer de aanmelding wordt uitgevoerd vanaf een locatie die zich nog niet in de lijst met bekende locaties bevindt. Nieuwe gebruikers worden in de ' leer modus ' opgenomen voor een periode waarin niet-bekende aanmeldings eigenschappen risico detecties worden uitgeschakeld terwijl de algoritmen het gedrag van de gebruiker leren kennen. De duur van de leer modus is dynamisch en is afhankelijk van hoe lang het duurt om voldoende informatie over de aanmeld patronen van de gebruiker te verzamelen. De minimale duur is vijf dagen. Een gebruiker kan na een lange periode van inactiviteit terugkeren naar de leer modus. Het systeem negeert ook aanmeldingen van bekende apparaten en locaties die geografisch dicht bij een bekende locatie vallen. <br><br> We voeren deze detectie ook uit voor basis verificatie (of verouderde protocollen). Omdat deze protocollen geen moderne eigenschappen hebben, zoals client-ID, is er beperkte telemetrie om fout-positieven te verminderen. We raden onze klanten aan om over te stappen op moderne authenticatie. |
| Door beheerder bevestigd misbruik van gebruiker | Offline | Deze detectie geeft aan dat een beheerder in de gebruikers interface van Risk ante gebruikers de gebruiker heeft geknoeid, of met behulp van de riskyUsers-API. Als u wilt zien welke beheerder deze gebruiker heeft bevestigd, controleert u de risico geschiedenis van de gebruiker (via de gebruikers interface of API). |
| Schadelijk IP-adres | Offline | Met deze detectie wordt de aanmelding aangegeven vanaf een schadelijk IP-adres. Een IP-adres wordt beschouwd als schadelijk op basis van hoge fout frequenties vanwege ongeldige referenties die zijn ontvangen van het IP-adres of andere IP-reputatie bronnen. |
| Verdachte regels voor het bewerken van postvak in | Offline | Deze detectie wordt gedetecteerd door [Microsoft Cloud app Security (MCAS)](/cloud-app-security/anomaly-detection-policy#suspicious-inbox-manipulation-rules). Deze detectie profileert uw omgeving en activeert waarschuwingen wanneer verdachte regels die berichten of mappen verwijderen of verplaatsen worden ingesteld op het postvak in van een gebruiker. Deze detectie kan erop wijzen dat het account van de gebruiker is aangetast, dat berichten opzettelijk worden verborgen en dat het postvak wordt gebruikt voor het distribueren van spam of malware in uw organisatie. |
| Wachtwoordspray | Offline | Een aanval met een wacht woord is het geval wanneer meerdere gebruikers namen worden aangevallen met veelvoorkomende wacht woorden in een geïntegreerde brute kracht om onbevoegde toegang te krijgen. Deze risico detectie wordt geactiveerd wanneer een wachtwoord sproei-aanval is uitgevoerd. |
| Onmogelijk traject | Offline | Deze detectie wordt gedetecteerd door [Microsoft Cloud app Security (MCAS)](/cloud-app-security/anomaly-detection-policy#impossible-travel). Deze detectie identificeert twee gebruikers activiteiten (is een of meerdere sessies) die afkomstig zijn van geografisch gelegen locaties binnen een tijds periode die korter is dan de tijd die de gebruiker nodig heeft om te reizen van de eerste locatie naar de tweede, wat aangeeft dat een andere gebruiker dezelfde referenties gebruikt. |

### <a name="other-risk-detections"></a>Andere risico detecties

| Risico detectie | Detectie type | Description |
| --- | --- | --- |
| Er is extra risico gedetecteerd | Realtime of offline | Deze detectie geeft aan dat een van de bovenstaande Premium-detecties is gedetecteerd. Aangezien de Premium-detecties alleen zichtbaar zijn voor Azure AD Premium P2-klanten, hebben ze de titel ' extra risico gedetecteerd ' voor klanten zonder Azure AD Premium P2-licenties. |

## <a name="common-questions"></a>Veelgestelde vragen

### <a name="risk-levels"></a>Risiconiveaus

Met Identity Protection wordt het risico gecategoriseerd in drie niveaus: laag, gemiddeld en hoog. Wanneer u [aangepaste beleids regels voor identiteits beveiliging](./concept-identity-protection-policies.md#custom-conditional-access-policy)configureert, kunt u deze ook configureren om te activeren op een **risico** niveau. Geen risico betekent dat er geen actieve indicatie is dat de identiteit van de gebruiker is aangetast.

Microsoft biedt geen specifieke details over de manier waarop het risico wordt berekend, maar hoe hoger het niveau is, hoe betrouwbaarder het is dat de gebruiker of het aanmelden is gecompromitteerd. Een voorbeeld: één geval van onbekende aanmeldingseigenschappen voor een gebruiker kan minder bedreigend zijn dan gelekte referenties voor een andere gebruiker.

### <a name="password-hash-synchronization"></a>Wachtwoord-hashsynchronisatie

Risico detecties zoals gelekte referenties en wachtwoord spray vereisen de aanwezigheid van wacht woord-hashes voor detectie. Zie het artikel [wachtwoord hash-synchronisatie implementeren met Azure AD Connect Sync](../hybrid/how-to-connect-password-hash-synchronization.md)voor meer informatie over het synchroniseren van wacht woord-hashes.

### <a name="leaked-credentials"></a>Gelekte referenties

#### <a name="where-does-microsoft-find-leaked-credentials"></a>Waar vindt micro soft gelekte referenties?

Micro soft vindt gelekte referenties op verschillende plaatsen, waaronder:

- Open bare plak sites, zoals pastebin.com en paste.ca, waarbij dergelijke materialen doorgaans worden geboekt door ongeldige actors. Deze locatie is de meeste onjuiste actoren van de zoek opdracht om gestolen referenties te vinden.
- Politie instanties.
- Andere groepen op micro soft voeren een donker Webonderzoek uit.

#### <a name="why-arent-i-seeing-any-leaked-credentials"></a>Waarom zie ik geen gelekte referenties?

Gelekte referenties worden verwerkt wanneer micro soft een nieuwe, openbaar beschik bare batch vindt. Vanwege de gevoelige aard worden de gelekte referenties kort na de verwerking verwijderd. Alleen nieuwe gelekte referenties die zijn gevonden nadat u wachtwoord hash synchronisatie (PHS) inschakelt, worden verwerkt voor uw Tenant. Er wordt geen verificatie uitgevoerd van eerder gevonden referentieparen. 

#### <a name="i-havent-seen-any-leaked-credential-risk-events-for-quite-some-time"></a>Ik heb enige tijd geen gelekte referentie risico gebeurtenissen gedetecteerd?

Als u geen gelekte referentie risico gebeurtenissen hebt gezien, is dit om de volgende redenen:

- PHS is niet ingeschakeld voor uw Tenant.
- Micro soft heeft geen lekkende referentie paren gevonden die overeenkomen met uw gebruikers.

#### <a name="how-often-does-microsoft-process-new-credentials"></a>Hoe vaak worden nieuwe referenties door micro soft verwerkt?

Referenties worden onmiddellijk verwerkt nadat ze zijn gevonden, normaal gesp roken in meerdere batches per dag.

## <a name="next-steps"></a>Volgende stappen

- [Policies available to mitigate risks](concept-identity-protection-policies.md) (Beschikbare beleidsregels om risico's te beperken)
- [Beveiligingsoverzicht](concept-identity-protection-security-overview.md)