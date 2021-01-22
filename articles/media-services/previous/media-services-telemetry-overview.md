---
title: Azure Media Services telemetrie | Microsoft Docs
description: Dit artikel bevat een overzicht van Microsoft Azure Media Services telemetrie.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 95c20ec4-c782-4063-8042-b79f95741d28
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/01/2019
ms.author: juliako
ms.openlocfilehash: 4bf9a96d7ffc3b939abe8cfb889c5bd49fee09cc
ms.sourcegitcommit: 77afc94755db65a3ec107640069067172f55da67
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98694585"
---
# <a name="azure-media-services-telemetry"></a>Azure Media Services telemetrie  

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Er worden geen nieuwe functies of functionaliteit meer aan Media Services v2. toegevoegd. <br/>Bekijk de nieuwste versie [Media Services v3](../latest/index.yml). Zie ook [migratie richtlijnen van v2 naar v3](../latest/migrate-v-2-v-3-migration-introduction.md)

Met Azure Media Services (AMS) kunt u toegang krijgen tot gegevens over telemetrische/metrieken voor de services. Met de huidige versie van AMS kunt u telemetrie-gegevens verzamelen voor live **Channel**-, **StreamingEndpoint**-en live- **Archief** entiteiten. 

Telemetrie wordt naar een opslag tabel geschreven in een Azure Storage-account dat u opgeeft (meestal gebruikt u het opslag account dat is gekoppeld aan uw AMS-account). 

Het telemetrie-systeem beheert geen gegevens retentie. U kunt de oude telemetriegegevens verwijderen door de opslag tabellen te verwijderen.

In dit onderwerp wordt beschreven hoe u de AMS-telemetrie configureert en gebruikt.

## <a name="configuring-telemetry"></a>Telemetrie configureren

U kunt telemetrie configureren op een granulatie op onderdeel niveau. Er zijn twee detail niveaus: "normaal" en "uitgebreid". Op dit moment retour neren beide niveaus dezelfde informatie. U kunt het beste "normaal. 

In de volgende onderwerpen ziet u hoe u telemetrie inschakelt:

[Telemetrie inschakelen met .NET](media-services-dotnet-telemetry.md) 

[Telemetrie inschakelen met REST](media-services-rest-telemetry.md)

## <a name="consuming-telemetry-information"></a>Informatie over telemetrie gebruiken

Telemetrie wordt geschreven naar een Azure Storage tabel in het opslag account dat u hebt opgegeven toen u telemetrie voor het Media Services account hebt geconfigureerd. In deze sectie worden de opslag tabellen voor de metrische gegevens beschreven.

U kunt telemetrie-gegevens op een van de volgende manieren gebruiken:

- Lees gegevens rechtstreeks vanuit Azure Table Storage (bijvoorbeeld met de opslag-SDK). Zie de **informatie** over het gebruik van telemetriegegevens in [Dit](/previous-versions/azure/mt742089(v=azure.100)) onderwerp voor de beschrijving van de telemetrie-opslag tabellen.

of

- Gebruik de ondersteuning in de Media Services .NET SDK voor het lezen van opslag gegevens, zoals beschreven in [Dit](media-services-dotnet-telemetry.md) onderwerp. 


Het hieronder beschreven telemetrie-schema is ontworpen om goede prestaties te bieden binnen de limieten van Azure Table Storage:

- Gegevens worden gepartitioneerd op account-ID en service-ID zodat telemetrie van elke service onafhankelijk van elkaar kan worden opgevraagd.
- Partities bevatten de datum om een redelijke bovengrens voor de partitie grootte te geven.
- De rijwaarden zijn in omgekeerde volg orde zodat de meest recente telemetriegegevens voor een bepaalde service kunnen worden opgevraagd.

Zo kunnen veel van de algemene query's efficiënt zijn:

- Parallel, onafhankelijk downloaden van gegevens voor afzonderlijke services.
- Ophalen van alle gegevens voor een bepaalde service in een datum bereik.
- De meest recente gegevens voor een service worden opgehaald.

### <a name="telemetry-table-storage-output-schema"></a>Uitvoer schema voor telemetrie-tabel opslag

Telemetriegegevens worden opgeslagen in een statistische functie in een tabel, ' TelemetryMetrics20160321 ', waarbij ' 20160321 ' de datum van de gemaakte tabel is. Het telemetrie-systeem maakt een afzonderlijke tabel voor elke nieuwe dag op basis van 00:00 UTC. De tabel wordt gebruikt voor het opslaan van terugkerende waarden, zoals opname bitrate binnen een bepaald tijd venster, verzonden bytes, enzovoort. 

Eigenschap|Waarde|Voor beelden/opmerkingen
---|---|---
PartitionKey|{account-ID} _ {entiteit-ID}|e49bef329c29495f9b9570989682069d_64435281c50a4dd8ab7011cb0f4cdf66<br/<br/>De account-ID is opgenomen in de partitie sleutel om werk stromen te vereenvoudigen waarbij meerdere Media Services-accounts naar hetzelfde opslag account worden geschreven.
RowKey|{seconden tot middernacht} _ {wille keurige waarde}|01688_00199<br/><br/>De rij begint met het aantal seconden tot middernacht om de bovenste n-stijl query's in een partitie toe te staan. Zie [Dit](../../cosmos-db/table-storage-design-guide.md#log-tail-pattern) artikel voor meer informatie. 
Tijdstempel|Datum/tijd|Automatische tijds tempel van de Azure-tabel 2016-09-09T22:43:42.241 Z
Type|Het type entiteit dat telemetrie-gegevens levert|Kanaal-StreamingEndpoint/archief<br/><br/>Het gebeurtenis type is een teken reeks waarde.
Name|De naam van de telemetrie-gebeurtenis|ChannelHeartbeat/StreamingEndpointRequestLog
ObservedTime|Het tijdstip waarop de telemetrie-gebeurtenis is opgetreden (UTC)|2016-09-09T22:42:36.924 Z<br/><br/>De waargenomen tijd wordt gegeven door de entiteit die de telemetrie verzendt (bijvoorbeeld een kanaal). Er kunnen tijd synchronisatie problemen zijn tussen onderdelen, dus deze waarde is ongeveer gelijk aan
ServiceID|{Service-ID}|f70bd731-691d-41c6-8f2d-671d0bdc9c7e
Entiteit-specifieke eigenschappen|Zoals gedefinieerd door de gebeurtenis|Streamnaam: stream1, bitrate 10123,...<br/><br/>De overige eigenschappen worden gedefinieerd voor het opgegeven gebeurtenis type. Azure-tabel inhoud is sleutel waardeparen.  (dat wil zeggen dat verschillende rijen in de tabel verschillende sets eigenschappen hebben).

### <a name="entity-specific-schema"></a>Entiteit-specifiek schema

Er zijn drie soorten metrische gegevens items met een entiteit die elk zijn gepusht met de volgende frequentie:

- Streaming-eind punten: elke 30 seconden
- Live kanalen: elke minuut
- Live-Archief: elke minuut

**Streaming-eindpunt**

Eigenschap|Waarde|Voorbeelden
---|---|---
PartitionKey|PartitionKey|e49bef329c29495f9b9570989682069d_64435281c50a4dd8ab7011cb0f4cdf66
RowKey|RowKey|01688_00199
Tijdstempel|Tijdstempel|Automatische tijds tempel van Azure Table 2016-09-09T22:43:42.241 Z
Type|Type|StreamingEndpoint
Name|Name|StreamingEndpointRequestLog
ObservedTime|ObservedTime|2016-09-09T22:42:36.924 Z
ServiceID|Service-id|f70bd731-691d-41c6-8f2d-671d0bdc9c7e
HostName|Hostnaam van het eind punt|builddemoserver.origin.mediaservices.windows.net
Status code|Registreert HTTP-status|200
ResultCode|Details van resultaat code|S_OK
RequestCount|Totaal aantal aanvragen in de aggregatie|3
Bytes sent|Totaal aantal verzonden bytes|2987358
ServerLatency|Gemiddelde server latentie (inclusief opslag)|129
E2ELatency|Gemiddelde end-to-end-latentie|250

**Live-kanaal**

Eigenschap|Waarde|Voor beelden/opmerkingen
---|---|---
PartitionKey|PartitionKey|e49bef329c29495f9b9570989682069d_64435281c50a4dd8ab7011cb0f4cdf66
RowKey|RowKey|01688_00199
Tijdstempel|Tijdstempel|Automatische tijds tempel van de Azure-tabel 2016-09-09T22:43:42.241 Z
Type|Type|Kanaal
Name|Name|ChannelHeartbeat
ObservedTime|ObservedTime|2016-09-09T22:42:36.924 Z
ServiceID|Service-id|f70bd731-691d-41c6-8f2d-671d0bdc9c7e
TrackType|Type track video/audio/tekst|video/audio
Nummer bijhouden|Naam van het spoor|Video/audio_1
Bitsnelheid|Bitsnelheid bijhouden|785000
CustomAttributes||   
IncomingBitrate|Werkelijke binnenkomende bitrate|784548
OverlapCount|Overlap ping in de opname|0
DiscontinuityCount|Oncontinuïteit voor spoor|0
LastTimestamp|Laatste opgenomen tijds tempel van gegevens|1800488800
NonincreasingCount|Aantal verwijderde fragmenten vanwege niet-toenemende tijds tempel|2
UnalignedKeyFrames|Of er fragment (en) (op verschillende kwaliteits niveaus) zijn ontvangen waar de keyframes niet zijn uitgelijnd |Waar
UnalignedPresentationTime|Of we fragment (en) (op verschillende kwaliteits niveaus/tracks) ontvangen waarbij de presentatie tijd niet is uitgelijnd|Waar
UnexpectedBitrate|Waar, als de berekende/werkelijke bitrate voor audio/video track > 40.000 bps en IncomingBitrate = = 0 of IncomingBitrate en actualBitrate verschillen per 50% |Waar
In orde|Waar, als <br/>overlapCount, <br/>DiscontinuityCount, <br/>NonIncreasingCount, <br/>UnalignedKeyFrames, <br/>UnalignedPresentationTime, <br/>UnexpectedBitrate<br/> zijn allemaal 0|Waar<br/><br/>In orde is een samengestelde functie die onwaar retourneert wanneer een van de volgende voor waarden van toepassing is:<br/><br/>-OverlapCount > 0<br/>-DiscontinuityCount > 0<br/>-NonincreasingCount > 0<br/>-UnalignedKeyFrames = = True<br/>-UnalignedPresentationTime = = True<br/>-UnexpectedBitrate = = True

**Live-Archief**

Eigenschap|Waarde|Voor beelden/opmerkingen
---|---|---
PartitionKey|PartitionKey|e49bef329c29495f9b9570989682069d_64435281c50a4dd8ab7011cb0f4cdf66
RowKey|RowKey|01688_00199
Tijdstempel|Tijdstempel|Automatische tijds tempel van de Azure-tabel 2016-09-09T22:43:42.241 Z
Type|Type|Archiveren
Name|Name|ArchiveHeartbeat
ObservedTime|ObservedTime|2016-09-09T22:42:36.924 Z
ServiceID|Service-id|f70bd731-691d-41c6-8f2d-671d0bdc9c7e
Manifestnaam|Programma-URL|Asset-eb149703-ed0a-483c-91c4-e4066e72cce3/a0a5cfbf-71ec-4bd2-8c01-a92a2b38c9ba. ISM
Nummer bijhouden|Naam van het spoor|audio_1
TrackType|Type spoor|Audio/video
CustomAttribute|Hex-teken reeks die onderscheidt tussen een ander spoor met dezelfde naam en bitsnelheid (hoek van meerdere camera's)|
Bitsnelheid|Bitsnelheid bijhouden|785000
In orde|Waar, als FragmentDiscardedCount = = 0 && ArchiveAcquisitionError = = False|Waar (deze twee waarden zijn niet aanwezig in de metriek, maar zijn aanwezig in de bron gebeurtenis)<br/><br/>In orde is een samengestelde functie die onwaar retourneert wanneer een van de volgende voor waarden van toepassing is:<br/><br/>-FragmentDiscardedCount > 0<br/>-ArchiveAcquisitionError = = True

## <a name="general-qa"></a>Algemeen Q&A

### <a name="how-to-consume-metrics-data"></a>Hoe worden metrische gegevens verbruikt?

Metrische gegevens worden opgeslagen als een reeks Azure-tabellen in het opslag account van de klant. Deze gegevens kunnen worden gebruikt met behulp van de volgende hulpprogram ma's:

- AMS-SDK
- Microsoft Azure Storage Explorer (ondersteunt exporteren naar een indeling met door komma's gescheiden waarden en verwerkt in Excel)
- REST-API

### <a name="how-to-find-average-bandwidth-consumption"></a>Hoe kunt u het gemiddelde bandbreedte verbruik vinden?

Het gemiddelde bandbreedte verbruik is het gemiddelde van bytes sent gedurende een periode.

### <a name="how-to-define-streaming-unit-count"></a>Hoe kan ik het aantal streaming-eenheden definiëren?

Het aantal streaming-eenheden kan worden gedefinieerd als de piek doorvoer van de streaming-eind punten van de service gedeeld door de piek doorvoer van één streaming-eind punt. De maximale gebruiks doorvoer van één streaming-eind punt is 160 Mbps.
Stel bijvoorbeeld dat de piek doorvoer van de service van een klant 40 MBps is (de maximale waarde van bytes sent gedurende een periode). Vervolgens is het aantal streaming-eenheden gelijk aan (40 MBps) * (8 bits/byte)/(160 Mbps) = 2 streaming-eenheden.

### <a name="how-to-find-average-requestssecond"></a>Gemiddeld aantal aanvragen per seconde zoeken

Als u het gemiddelde aantal aanvragen per seconde wilt vinden, berekent u het gemiddelde aantal aanvragen (RequestCount) gedurende een periode.

### <a name="how-to-define-channel-health"></a>Kanaal status definiëren?

De kanaal status kan worden gedefinieerd als een samengestelde Boole-functie, zodat deze ONWAAR is wanneer een van de volgende voor waarden van toepassing is:

- OverlapCount > 0
- DiscontinuityCount > 0
- NonincreasingCount > 0
- UnalignedKeyFrames = = True 
- UnalignedPresentationTime = = True 
- UnexpectedBitrate = = True


### <a name="how-to-detect-discontinuities"></a>Hoe kunnen er problemen worden opgespoord?

Als u wilt detecteren, kunt u alle kanaal gegevens items zoeken waarbij DiscontinuityCount > 0. De bijbehorende ObservedTime-tijds tempel geeft de tijden aan waarop de zich heeft voorgedaan.

### <a name="how-to-detect-timestamp-overlaps"></a>Hoe kan ik tijds tempel overlappingen detecteren?

Als u de tijds tempel overlap pingen wilt detecteren, zoekt u alle kanaal gegevens invoer waarbij OverlapCount > 0. De bijbehorende ObservedTime-tijds tempel geeft de tijden aan waarop de tijds tempel overlapt.

### <a name="how-to-find-streaming-request-failures-and-reasons"></a>Hoe kan ik problemen met streaming-aanvragen en redenen vinden?

Als u fouten en redenen voor streaming-aanvragen wilt vinden, zoekt u alle gegevens items van streaming-eind punten waarbij ResultCode niet gelijk is aan S_OK. Het bijbehorende veld status code geeft de reden voor het mislukken van de aanvraag aan.

### <a name="how-to-consume-data-with-external-tools"></a>Gegevens gebruiken met externe hulpprogram ma's

U kunt gegevens van de telemetrische verwerking en visualiseren met de volgende hulpprogram ma's:

- Power BI
- Application Insights
- Azure Monitor (voorheen Shoebox)
- AMS Live-dash board
- Azure Portal (in afwachting van vrijgave)

### <a name="how-to-manage-data-retention"></a>Hoe kan ik gegevens retentie beheren?

Het telemetrie-systeem voorziet niet in het beheer van gegevens retentie of het automatisch verwijderen van oude records. Daarom moet u oude records hand matig beheren en verwijderen uit de opslag tabel. Raadpleeg de opslag-SDK voor informatie over hoe u dit kunt doen.

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
