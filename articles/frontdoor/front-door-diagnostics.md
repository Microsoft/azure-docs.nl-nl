---
title: Metrische gegevens en logboeken bewaken in azure front-deur | Microsoft Docs
description: In dit artikel worden de verschillende metrische gegevens en toegangs logboeken beschreven die door Azure front-deur worden ondersteund
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/23/2020
ms.author: yuajia
ms.openlocfilehash: a972123604cafbfc4c6753c1fe3ad21147b53b2d
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2021
ms.locfileid: "106550656"
---
# <a name="monitoring-metrics-and-logs-in-azure-front-door"></a>Metrische gegevens en logboeken bewaken in azure front deur

Met behulp van de voor deur van Azure kunt u resources op de volgende manieren controleren:

- **Metrische gegevens**. Azure front-deur heeft momenteel acht metrische gegevens om prestatie meter items weer te geven.
- **Logboeken**. Met activiteiten en Diagnostische logboeken kunnen prestaties, toegang en andere gegevens worden opgeslagen of verbruikt vanuit een resource voor bewakings doeleinden.

##  <a name="metrics"></a><a name="metrics"></a>Metrische gegevens

Metrische gegevens zijn een functie voor bepaalde Azure-resources waarmee u prestatie meter items in de portal kunt weer geven. Hieronder vindt u de beschik bare metrische gegevens voor de voor deur:

| Metrisch | Weergave naam voor metrische gegevens | Eenheid | Dimensies | Beschrijving |
| --- | --- | --- | --- | --- |
| RequestCount | Aantal aanvragen | Count | Http status</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Het aantal client aanvragen dat door de voor deur wordt geleverd.  |
| RequestSize | Aanvraag grootte | Bytes | Http status</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Het aantal bytes dat is verzonden als aanvragen van clients naar de voor deur. |
| ResponseSize | Grootte van antwoord | Bytes | Http status</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Het aantal bytes dat is verzonden als reacties van de voor deur naar clients. |
| TotalLatency | Totale latentie | Milliseconden | Http status</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | De totale tijd van de client aanvraag die wordt ontvangen door de voor deur tot de laatste reactie byte die van AFD naar de client is verzonden. |
| BackendRequestCount | Aantal back-aanvragen | Count | Http status</br>HttpStatusGroup</br>Back-end | Het aantal aanvragen dat van de voor deur naar de back-end wordt verzonden. |
| BackendRequestLatency | Latentie van back-upaanvraag | Milliseconden | Back-end | De tijd die wordt berekend vanaf het moment dat de aanvraag door de voor deur naar de back-end is verzonden tot de voor deur de laatste reactie byte van de back-end heeft ontvangen. |
| BackendHealthPercentage | Back-status percentage | Percentage | Back-end</br>Hosts | Het percentage geslaagde status tests van front-deur naar back-end. |
| WebApplicationFirewallRequestCount | Aantal aanvragen voor Web Application firewall | Count | PolicyName</br>RuleName</br>Bewerking | Het aantal client aanvragen dat is verwerkt door de beveiligingslaag van de toepassingslaag. |

## <a name="activity-logs"></a><a name="activity-log"></a>Activiteiten logboeken

Activiteiten logboeken bevatten informatie over de bewerkingen die op de voor deur worden uitgevoerd. Ze bepalen ook de What, wie en wanneer er schrijf bewerkingen (put, post of Delete) op de voor deur worden uitgevoerd.

>[!NOTE]
>Activiteiten logboeken bevatten geen lees bewerkingen (Get). Ze bevatten ook geen bewerkingen die u uitvoert met behulp van de Azure Portal of de oorspronkelijke beheer-API.

U krijgt toegang tot activiteiten Logboeken in uw voor deur of in alle logboeken van uw Azure-resources in Azure Monitor. Activiteitenlogboeken weergeven:

1. Selecteer uw voor deur-exemplaar.
2. Selecteer **Activiteitenlogboek**.

    :::image type="content" source="./media/front-door-diagnostics/activity-log.png" alt-text="Activiteitenlogboek":::

3. Kies een filter bereik en selecteer vervolgens **Toep assen**.

## <a name="diagnostic-logs"></a><a name="diagnostic-logging"></a>Diagnostische logboeken
Diagnostische logboeken bieden uitgebreide informatie over bewerkingen en fouten die belang rijk zijn voor controle en probleem oplossing. Diagnostische logboeken verschillen van activiteiten Logboeken.

Activiteiten logboeken bieden inzicht in de bewerkingen die worden uitgevoerd op Azure-resources. Diagnostische logboeken bieden inzicht in de bewerkingen die uw resource heeft voltooid. Zie [Azure monitor Diagnostische logboeken](../azure-monitor/essentials/platform-logs-overview.md)voor meer informatie.

:::image type="content" source="./media/front-door-diagnostics/diagnostic-log.png" alt-text="Diagnostische logboeken":::

Diagnostische logboeken voor uw front-deur configureren:

1. Selecteer uw Azure-voor deur.

2. Kies **Diagnostische instellingen**.

3. Selecteer **Diagnostische gegevens inschakelen**. Archiveer Diagnostische logboeken Samen met metrische gegevens naar een opslag account, stream ze naar een Event Hub of stuur ze naar Azure Monitor-Logboeken.

De voor deur bevat momenteel Diagnostische logboeken. Diagnostische logboeken bieden afzonderlijke API-aanvragen voor elke vermelding met het volgende schema:

| Eigenschap  | Beschrijving |
| ------------- | ------------- |
| BackendHostname | Als de aanvraag wordt doorgestuurd naar een back-end, vertegenwoordigt dit veld de hostnaam van de back-end. Dit veld is leeg als de aanvraag wordt omgeleid of doorgestuurd naar een regionale cache (wanneer caching wordt ingeschakeld voor de routerings regel). |
| CacheStatus | In het geval van cache scenario's definieert dit veld de cache treffer/Missing in de POP |
| ClientIp | Het IP-adres van de client die de aanvraag heeft ingediend. Als er een X-doorgestuurd is voor de header in de aanvraag, wordt het client-IP-adres uit hetzelfde gekozen. |
| ClientPort | De IP-poort van de client die de aanvraag heeft ingediend. |
| HttpMethod | De HTTP-methode die door de aanvraag wordt gebruikt. |
| Http status code | De HTTP-status code die is geretourneerd door de proxy. |
| HttpStatusDetails | De resulterende status van de aanvraag. De betekenis van deze teken reeks waarde is te vinden in een status verwijzings tabel. |
| HttpVersion | Het type van de aanvraag of verbinding. |
| POP | De korte naam van de rand waar de aanvraag is gelandd. |
| RequestBytes | De grootte van het HTTP-aanvraag bericht in bytes, met inbegrip van de aanvraag headers en de hoofd tekst van de aanvraag. |
| RequestUri | De URI van de ontvangen aanvraag. |
| ResponseBytes | Bytes dat als reactie door de back-endserver wordt verzonden.  |
| RoutingRuleName | De naam van de routerings regel die de aanvraag heeft gevonden. |
| RulesEngineMatchNames | De namen van de regels die de aanvraag heeft gevonden. |
| Exemplaar | De TLS/SSL-protocol versie die wordt gebruikt door de aanvraag of null als er geen versleuteling is. |
| SentToOriginShield </br> (afgeschaft) * **Zie opmerkingen bij afschaffing in de volgende sectie.**| Als dit het geval is, betekent dit dat de aanvraag is beantwoord van de oorspronkelijke schild in de cache in plaats van de Edge-pop. Schild van oorsprong is een bovenliggende cache die wordt gebruikt om de verhouding van de cache treffers te verbeteren. |
| isReceivedFromClient | Als deze waarde True is, betekent dit dat de aanvraag afkomstig is van de client. Als de waarde False is, is de aanvraag een Missing in de rand (Child POP) en wordt gereageerd vanaf het afschermen van de oorsprong (bovenliggende POP). |
| TimeTaken | De tijds duur van de eerste byte van de aanvraag naar de laatste byte van de reactie, in seconden. |
| TrackingReference | De unieke verwijzings reeks die een aanvraag identificeert die wordt geleverd door de voor deur, ook verzonden als X-Azure-ref-header naar de client. Vereist voor het zoeken naar details in de logboeken van de toegang voor een specifieke aanvraag. |
| User agent | Het browser type dat door de client wordt gebruikt. |
| Info | Dit veld bevat het specifieke type fout voor verdere probleem oplossing. </br> Mogelijke waarden zijn: </br> **Fout**: geeft aan dat er geen fout is gevonden. </br> **CertificateError**: algemene SSL-certificaat fout.</br> **CertificateNameCheckFailed**: de hostnaam in het SSL-certificaat is ongeldig of komt niet overeen. </br> **ClientDisconnected**: de aanvraag is mislukt vanwege een client netwerk verbinding. </br> **UnspecifiedClientError**: algemene client fout. </br> **InvalidRequest**: ongeldige aanvraag. Dit kan gebeuren vanwege een ongeldige header, hoofd tekst en URL. </br> **DNSFailure**: DNS-fout. </br> **DNSNameNotResolved**: de server naam of het adres kan niet worden omgezet. </br> **OriginConnectionAborted**: de verbinding met de oorsprong is abrupt gestopt. </br> **OriginConnectionError**: algemene verbindings fout van oorsprong. </br> **OriginConnectionRefused**: de verbinding met de oorsprong kan niet tot stand worden gebracht. </br> **OriginError**: fout met algemene oorsprong. </br> **OriginInvalidResponse**: oorsprong heeft een ongeldige of niet-herkende reactie geretourneerd. </br> **OriginTimeout**: de time-outperiode voor de oorspronkelijke aanvraag is verlopen. </br> **ResponseHeaderTooBig**: de oorsprong heeft te groot van een reactie header geretourneerd. </br> **RestrictedIP**: de aanvraag is geblokkeerd vanwege een beperkt IP-adres. </br> **SSLHandshakeError**: kan geen verbinding maken met de oorsprong vanwege een SSL-hand Shake fout. </br> **UnspecifiedError**: er is een fout opgetreden die niet overeenkomt met de fouten in de tabel. |

### <a name="sent-to-origin-shield-deprecation"></a>Verzonden naar afkomst van oorsprong afschermen
De onbewerkte logboek eigenschap **isSentToOriginShield** is afgeschaft en vervangen door een nieuw veld **isReceivedFromClient**. Gebruik het nieuwe veld als u het afgeschafte veld al gebruikt. 

Onbewerkte logboeken bevatten logboeken die zijn gegenereerd op basis van CDN Edge (onderliggende POP) en Afscherm van oorsprong. Schild van oorsprong verwijst naar bovenliggende knoop punten die zich wereld wijd bevinden. Deze knoop punten communiceren met de oorspronkelijke servers en verminderen de belasting van het verkeer op de oorsprong. 

Voor elke aanvraag die naar de oorsprong van het schild gaat, zijn er twee logboek vermeldingen:

* Eén voor Edge-knoop punten
* Eén voor het afschermen van oorsprong. 

U kunt het veld **isReceivedFromClient** gebruiken om de juiste gegevens op te halen om de uitkomsten of reacties te onderscheiden van de Edge-knoop punten versus het oorsprongs schild. 

Als de waarde ONWAAR is, betekent dit dat de aanvraag wordt gereageerd van het afschermen van de oorsprong van het Schild-knoop punt. Deze aanpak is van kracht om onbewerkte logboeken te vergelijken met facturerings gegevens. Er worden geen kosten in rekening gebracht voor het afbreken van de oorsprong van het schild naar de Edge-knoop punten. Er worden kosten in rekening gebracht voor het uitschrijven van de Edge-knoop punten naar clients. 

**Kusto-query voorbeeld voor het uitsluiten van logboeken die zijn gegenereerd op basis van een schild in Log Analytics.**

`AzureDiagnostics 
| where Category == "FrontdoorAccessLog" and isReceivedFromClient_b == true`

> [!NOTE]
> Voor verschillende routerings configuraties en verkeers gedrag kunnen sommige velden, zoals backendHostname, cacheStatus, isReceivedFromClient en POP, worden gereageerd met verschillende waarden. In de onderstaande tabel worden de verschillende waarden beschreven. deze velden hebben verschillende scenario's:

| Scenario's | Aantal logboek vermeldingen | POP | BackendHostname | isReceivedFromClient | CacheStatus |
| ------------- | ------------- | ------------- | ------------- | ------------- | ------------- |
| Routerings regel zonder caching ingeschakeld | 1 | POP-code van rand | Back-end waar de aanvraag is doorgestuurd | Waar | CONFIG_NOCACHE |
| Routerings regel met caching ingeschakeld. Cache treffer aan de rand POP | 1 | POP-code van rand | Leeg | Waar | BEZOCHT |
| Routerings regel met caching ingeschakeld. Cache missers bij Edge POP, maar cache treffers in bovenliggende cache POP | 2 | 1. POP-code van rand</br>2. POP-code van bovenliggende cache | 1. POP-hostnaam van bovenliggende cache</br>2. empty | 1. True</br>2. onwaar | 1. MIS</br>2. DRUK |
| Routerings regel met caching ingeschakeld. Caches missen bij Edge POP, maar gedeeltelijke cache treffers in bovenliggende cache POP | 2 | 1. POP-code van rand</br>2. POP-code van bovenliggende cache | 1. POP-hostnaam van bovenliggende cache</br>2. back-end waarmee cache kan worden gevuld | 1. True</br>2. onwaar | 1. MIS</br>2. PARTIAL_HIT |
| Routerings regel met caching ingeschakeld. Cache PARTIAL_HIT bij Edge-POP, maar cache is actief op POP van bovenliggende cache | 2 | 1. POP-code van rand</br>2. POP-code van bovenliggende cache | 1. POP-code van rand</br>2. POP-code van bovenliggende cache | 1. True</br>2. onwaar | 1. PARTIAL_HIT</br>2. DRUK |
| Routerings regel met caching ingeschakeld. Cache missers bij zowel de rand als de bovenliggende cache | 2 | 1. POP-code van rand</br>2. POP-code van bovenliggende cache | 1. POP-code van rand</br>2. POP-code van bovenliggende cache | 1. True</br>2. onwaar | 1. MIS</br>2. MIS |

> [!NOTE]
> In het geval van cache scenario's wordt de waarde voor de cache status partial_hit wanneer een aantal bytes voor een aanvraag wordt opgehaald van de front-deur rand of de oorspronkelijke schild cache, terwijl sommige van de bytes van de oorsprong voor grote objecten worden opgehaald.

De voor deur maakt gebruik van een techniek die object Chunking wordt genoemd. Wanneer een groot bestand wordt aangevraagd, haalt de voor deur kleinere delen van het bestand op van de oorsprong. Nadat de voor deur van de POP-server een volledige of byte bereik van het aangevraagde bestand ontvangt, vraagt de front-deur van de server het bestand aan bij de oorsprong in segmenten van 8 MB.

Nadat het segment aan de voor deur is aangekomen, wordt het in de cache geplaatst en direct aan de gebruiker geleverd. De voor deur haalt vervolgens het volgende segment parallel op. Deze prefetch zorgt ervoor dat de inhoud één segment vóór de gebruiker blijft, waardoor de latentie wordt verminderd. Dit proces wordt voortgezet totdat het hele bestand wordt gedownload (indien aangevraagd), alle byte bereiken beschikbaar zijn (indien aangevraagd) of de client sluit de verbinding. Zie RFC 7233 voor meer informatie over de aanvraag voor byte bereik. Bij de voor deur worden eventuele segmenten in de cache opgeslagen wanneer ze worden ontvangen. Het hele bestand hoeft niet in de cache te worden opgeslagen in de front-deur cache. Het door lopen van aanvragen voor het bestand of de byte bereiken wordt vanuit de front-deur cache bediend. Als niet alle segmenten in de cache op de voor deur worden opgeslagen, wordt prefetch gebruikt om segmenten van de oorsprong aan te vragen. Deze optimalisatie is afhankelijk van de mogelijkheid van de oorspronkelijke server om aanvragen voor byte bereik te ondersteunen. Als de oorspronkelijke server geen aanvragen voor byte bereik ondersteunt, is deze optimalisatie niet effectief.

## <a name="next-steps"></a>Volgende stappen

- [Een Front Door-profiel maken](quickstart-create-front-door.md)
- [De werking van de voor deur](front-door-routing-architecture.md)
