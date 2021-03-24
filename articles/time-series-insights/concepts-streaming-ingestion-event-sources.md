---
title: Gebeurtenis bronnen voor streaming-opname-Azure Time Series Insights Gen2 | Microsoft Docs
description: Meer informatie over het streamen van gegevens naar Azure Time Series Insights Gen2.
author: lyrana
ms.author: lyhughes
manager: deepakpalled
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 03/18/2021
ms.openlocfilehash: ec41f7503ec179cb1fa6172e94e613933f719c93
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2021
ms.locfileid: "104953608"
---
# <a name="azure-time-series-insights-gen2-event-sources"></a>Azure Time Series Insights Gen2-gebeurtenis bronnen

Uw Azure Time Series Insights Gen2-omgeving kan Maxi maal twee streaming-gebeurtenis bronnen bevatten. Twee typen Azure-resources worden ondersteund als invoer:

- [Azure IoT Hub](../iot-hub/about-iot-hub.md)
- [Azure Event Hubs](../event-hubs/event-hubs-about.md)

Gebeurtenissen moeten worden verzonden als JSON met UTF-8-code ring.

## <a name="create-or-edit-event-sources"></a>Gebeurtenis bronnen maken of bewerken

De gebeurtenis bron is de koppeling tussen uw hub en uw Azure Time Series Insights Gen2-omgeving, en er wordt een afzonderlijke bron van het type `Time Series Insights event source` gemaakt in de resource groep. De IoT Hub of event hub-resources kunnen in hetzelfde Azure-abonnement wonen als uw Azure Time Series Insights Gen2-omgeving of een ander abonnement. Het is echter een best practice om uw Azure Time Series Insights omgeving en de IoT Hub of event hub binnen dezelfde Azure-regio te huis te nemen.

U kunt de [Azure Portal](./tutorials-set-up-tsi-environment.md#create-an-azure-time-series-insights-gen2-environment), [Azure cli](https://docs.microsoft.com/cli/azure/ext/timeseriesinsights/tsi/event-source), [Azure Resource Manager sjablonen](time-series-insights-manage-resources-using-azure-resource-manager-template.md)en de [rest API](/rest/api/time-series-insights/management(gen1/gen2)/eventsources) gebruiken om de gebeurtenis bronnen van uw omgeving te maken, bewerken of verwijderen.

## <a name="start-options"></a>Start opties

Wanneer u een gebeurtenis bron maakt, hebt u de optie om op te geven welke vooraf bestaande gegevens moeten worden verzameld. Deze instelling is optioneel. De volgende opties zijn beschikbaar:

| Naam   |  Beschrijving  |  Voor beeld van Azure Resource Manager sjabloon |
|----------|-------------|------|
| EarliestAvailable | Alle vooraf bestaande gegevens die zijn opgeslagen in de IoT of event hub opnemen | `"ingressStartAt": {"type": "EarliestAvailable"}` |
| EventSourceCreationTime |  Begin met het opnemen van gegevens die binnenkomen nadat de gebeurtenis bron is gemaakt. Bestaande gegevens die zijn gestreamd vóór het maken van de gebeurtenis bron, worden genegeerd. Dit is de standaard instelling in de Azure Portal   |   `"ingressStartAt": {"type": "EventSourceCreationTime"}` |
| CustomEnqueuedTime | In uw omgeving worden gegevens van uw aangepaste tijdgebonden tijd (UTC) door gegeven. Alle gebeurtenissen die in de IoT-of event hub zijn opgenomen op of na de aangepaste tijd in de wachtrij, worden opgenomen en opgeslagen. Alle gebeurtenissen die zijn aangekomen vóór uw aangepaste tijd in de wachtrij, worden genegeerd. Houd er rekening mee dat ' tijd in wachtrij ' verwijst naar de tijd (in UTC) die de gebeurtenis heeft ontvangen in IoT of event hub. Dit wijkt af van een aangepaste [tijds tempel eigenschap](./concepts-streaming-ingestion-event-sources.md#event-source-timestamp) in de hoofd tekst van uw gebeurtenis. |     `"ingressStartAt": {"type": "CustomEnqueuedTime", "time": "2021-03-01T17:00:00.20Z"}` |

> [!IMPORTANT]
>
> - Als u EarliestAvailable selecteert en een groot aantal bestaande gegevens hebt, kunt u een hoge initiële latentie ervaren als uw Azure Time Series Insights Gen2-omgeving al uw gegevens verwerkt.
> - Deze hoge latentie moet uiteindelijk deel uitmaakt naarmate de gegevens worden geïndexeerd. Verzend een ondersteunings ticket via de Azure Portal als u een voortdurende hoge latentie ondervindt.

* EarliestAvailable

![EarliestAvailable diagram](media/concepts-streaming-event-sources/event-source-earliest-available.png)

* EventSourceCreationTime

![EventSourceCreationTime diagram](media/concepts-streaming-event-sources/event-source-creation-time.png)

* CustomEnqueuedTime

![CustomEnqueuedTime diagram](media/concepts-streaming-event-sources/event-source-custom-enqueued-time.png)


## <a name="streaming-ingestion-best-practices"></a>Best practices voor streaming-opname

- Maak altijd een unieke consumenten groep voor uw Azure Time Series Insights Gen2-omgeving om gegevens van uw gebeurtenis bron te gebruiken. Het opnieuw gebruiken van consumenten groepen kan wille keurige verbreken veroorzaken en kan leiden tot verlies van gegevens.

- Configureer uw Azure Time Series Insights Gen2-omgeving en uw IoT Hub en/of Event Hubs in dezelfde Azure-regio. Hoewel het mogelijk is om een gebeurtenis bron in een afzonderlijke regio te configureren, wordt dit scenario niet ondersteund en kunnen we geen hoge Beschik baarheid garanderen.

- Ga niet verder dan de limiet van de [doorvoer snelheid](./concepts-streaming-ingress-throughput-limits.md) van uw omgeving of per partitie limiet.

- Configureer een [waarschuwing](./time-series-insights-environment-mitigate-latency.md#monitor-latency-and-throttling-with-alerts) over vertraging als uw omgeving problemen ondervindt bij het verwerken van gegevens. Zie [productie werkbelastingen](./concepts-streaming-ingestion-event-sources.md#production-workloads) hieronder voor voorgestelde waarschuwings voorwaarden.

- Gebruik Streaming-opname alleen voor bijna realtime-en recente gegevens, en het streamen van historische gegevens wordt niet ondersteund.

- Begrijpen hoe eigenschappen worden ontsnapd en JSON- [gegevens worden afgevlakt en opgeslagen.](./concepts-json-flattening-escaping-rules.md)

- Volg het principe van minimale bevoegdheden bij het leveren van verbindings reeksen voor gebeurtenis bronnen. Configureer voor Event Hubs een gedeeld toegangs beleid met alleen de claim *verzenden* en voor IOT hub de *service Connect* -machtiging alleen te gebruiken.

> [!CAUTION] 
> Als u uw IoT Hub of event hub verwijdert en een nieuwe resource met dezelfde naam opnieuw maakt, moet u een nieuwe gebeurtenis bron maken en de nieuwe IoT Hub of event hub koppelen. Er worden pas gegevens opgenomen wanneer u deze stap hebt voltooid.

## <a name="production-workloads"></a>Productieworkloads

Naast de aanbevolen procedures, raden we u aan het volgende te implementeren voor bedrijfskritische workloads.

- Verg root uw IoT Hub-of event hub-Bewaar tijd tot Maxi maal zeven dagen.

- Maak omgevings waarschuwingen in de Azure Portal. Met waarschuwingen op basis van platform [metrische gegevens](./how-to-monitor-tsi-reference.md#metrics) kunt u end-to-end pijplijn gedrag valideren. De instructies voor het maken en beheren van waarschuwingen vindt u [hier](./time-series-insights-environment-mitigate-latency.md#monitor-latency-and-throttling-with-alerts). Voorgestelde waarschuwings voorwaarden:

  - IngressReceivedMessagesTimeLag is langer dan vijf minuten
  - IngressReceivedBytes is 0
- Houd de opname taak evenwichtig tussen uw IoT Hub-of event hub-partities.

### <a name="historical-data-ingestion"></a>Historische gegevens opname

Het gebruik van de streaming-pijp lijn voor het importeren van historische gegevens wordt momenteel niet ondersteund in Azure Time Series Insights Gen2. Als u gegevens uit het verleden in uw omgeving wilt importeren, volgt u de onderstaande richt lijnen:

- Streamt geen Live en historische gegevens parallel. Het opnemen van gegevens uit de volg orde resulteert in gedegradeerde query prestaties.
- Neem historische gegevens op in een tijdgebonden manier om de beste prestaties te leveren.
- Blijf binnen de limieten voor de doorvoer snelheid van de opname hieronder.
- Schakel warme Store uit als de gegevens ouder zijn dan de Bewaar periode voor uw warme winkel.

## <a name="event-source-timestamp"></a>Tijds tempel gebeurtenis bron

Bij het configureren van een gebeurtenis bron, wordt u gevraagd om een time stamp-ID-eigenschap op te geven. De eigenschap time stamp wordt gebruikt om gebeurtenissen gedurende een periode bij te houden. Dit is de tijd die wordt gebruikt als de tijds tempel `$ts` in de [query-api's](/rest/api/time-series-insights/dataaccessgen2/query/execute) en voor het uitzetten van reeksen in de Azure time series Insights Explorer. Als er geen eigenschap wordt gegeven tijdens de aanmaak tijd, of als de tijds tempel eigenschap ontbreekt in een gebeurtenis, worden de IoT Hub van de gebeurtenis of de gebeurtenissen hubs in de wachtrij gezet als standaard waarde gebruikt. Eigenschaps waarden van tijds tempels worden opgeslagen in UTC.

Over het algemeen kunnen gebruikers ervoor kiezen de tijds tempel eigenschap aan te passen en de tijd te gebruiken waarop de sensor of tag de Lees bewerking heeft gegenereerd, in plaats van de standaard hub in de wachtrij te gebruiken. Dit is met name nodig wanneer apparaten een onherstelbaar connectiviteits verlies hebben en een batch van vertraagde berichten wordt doorgestuurd naar Azure Time Series Insights-Gen2.

Als uw aangepaste tijds tempel binnen een genest JSON-object of een matrix valt, moet u de juiste eigenschaps naam opgeven volgens onze [afvlakkings-en Escape-naamgevings regels](concepts-json-flattening-escaping-rules.md). De tijds tempel van de gebeurtenis bron voor de JSON-nettolading die [hier](concepts-json-flattening-escaping-rules.md#example-a) wordt weer gegeven, moet bijvoorbeeld worden ingevoerd als `"values.time"` .

### <a name="time-zone-offsets"></a>Tijd zone-offsets

Tijds tempels moeten worden verzonden in de ISO 8601-indeling en worden opgeslagen in UTC. Als er een tijd zone-offset is opgegeven, wordt de offset toegepast en vervolgens de tijd die wordt opgeslagen en geretourneerd in UTC-notatie. Als de verschuiving onjuist is opgemaakt, wordt deze genegeerd. In situaties waarin uw oplossing mogelijk geen context van de oorspronkelijke offset heeft, kunt u de offset gegevens in een aanvullende gebeurtenis eigenschap verzenden om ervoor te zorgen dat deze behouden blijven en dat uw toepassing kan verwijzen naar een query-antwoord.

De tijd zone-offset moet worden opgemaakt als een van de volgende:

± HHMMZ</br>
± UU: MM</br>
± UU: MMZ</br>

## <a name="next-steps"></a>Volgende stappen

- Lees de [regels voor json-afvlakking en-Escapes](./concepts-json-flattening-escaping-rules.md) om te begrijpen hoe gebeurtenissen worden opgeslagen.

- Inzicht in de [doorvoer beperkingen](./concepts-streaming-ingress-throughput-limits.md) van uw omgeving
