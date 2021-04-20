---
title: Gebeurtenisbronnen voor streaming-opname - Azure Time Series Insights Gen2-| Microsoft Docs
description: Meer informatie over het streamen van gegevens Azure Time Series Insights Gen2.
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 03/18/2021
ms.openlocfilehash: 499cb3c978a67f9ef71e6ad9dd03be9f05b45729
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107726966"
---
# <a name="azure-time-series-insights-gen2-event-sources"></a>Azure Time Series Insights Gen2-gebeurtenisbronnen

Uw Azure Time Series Insights Gen2-omgeving kan maximaal twee bronnen voor streaminggebeurtenissen hebben. Er worden twee typen Azure-resources ondersteund als invoer:

- [Azure IoT Hub](../iot-hub/about-iot-hub.md)
- [Azure Event Hubs](../event-hubs/event-hubs-about.md)

Gebeurtenissen moeten worden verzonden als JSON met UTF-8-codering.

## <a name="create-or-edit-event-sources"></a>Gebeurtenisbronnen maken of bewerken

De gebeurtenisbron is de koppeling tussen uw hub en uw Azure Time Series Insights Gen2-omgeving en er wordt een afzonderlijke resource van het type `Time Series Insights event source` gemaakt in uw resourcegroep. De IoT Hub of Event Hub-resource(s) kunnen zich in hetzelfde Azure-abonnement als uw Azure Time Series Insights Gen2-omgeving of een ander abonnement. Het is echter een best practice uw Azure Time Series Insights-omgeving en de IoT Hub of Event Hub binnen dezelfde Azure-regio te kunnen onder brengen.

U kunt de [Azure Portal,](./tutorials-set-up-tsi-environment.md#create-an-azure-time-series-insights-gen2-environment) [Azure CLI,](https://docs.microsoft.com/cli/azure/ext/timeseriesinsights/tsi/event-source) [Azure Resource Manager-sjablonen](time-series-insights-manage-resources-using-azure-resource-manager-template.md)en de [REST API](/rest/api/time-series-insights/management(gen1/gen2)/eventsources) gebruiken om gebeurtenisbronnen van uw omgeving te maken, bewerken of verwijderen.

> [!WARNING]
> Beperk de openbare internettoegang niet tot een hub of gebeurtenisbron die wordt gebruikt door Time Series Insights anders wordt de benodigde verbinding verbroken.

## <a name="start-options"></a>Startopties

Wanneer u een gebeurtenisbron maakt, kunt u opgeven welke bestaande gegevens moeten worden verzameld. Deze instelling is optioneel. De volgende opties zijn beschikbaar:

| Naam   |  Beschrijving  |  Azure Resource Manager voorbeeld van een sjabloon |
|----------|-------------|------|
| Vroegste beschikbaar | Alle bestaande gegevens opnemen die zijn opgeslagen in de IoT of Event Hub | `"ingressStartAt": {"type": "EarliestAvailable"}` |
| EventSourceCreationTime |  Begin met het opnemen van gegevens die binnenkomen nadat de gebeurtenisbron is gemaakt. Alle bestaande gegevens die zijn gestreamd voordat de gebeurtenisbron werd gemaakt, worden genegeerd. Dit is de standaardinstelling in de Azure Portal   |   `"ingressStartAt": {"type": "EventSourceCreationTime"}` |
| CustomEnqueuedTime | Uw omgeving neemt gegevens van uw aangepaste enqueued time (UTC) door. Alle gebeurtenissen die op of na uw aangepaste enqueued-tijd in uw IoT of Event Hub zijn opgeslagen, worden opgenomen en opgeslagen. Alle gebeurtenissen die vóór uw aangepaste enqueuedtijd zijn aangekomen, worden genegeerd. Houd er rekening mee dat 'enqueued time' verwijst naar de tijd (in UTC) waarin de gebeurtenis is aangekomen in uw IoT of Event Hub. Dit wijkt af van een aangepaste [tijdstempel-eigenschap](./concepts-streaming-ingestion-event-sources.md#event-source-timestamp) die zich in de hoofd body van uw gebeurtenis. |     `"ingressStartAt": {"type": "CustomEnqueuedTime", "time": "2021-03-01T17:00:00.20Z"}` |

> [!IMPORTANT]
>
> - Als u VroegstBeschikbaar selecteert en veel vooraf bestaande gegevens hebt, kan er een hoge initiële latentie zijn omdat uw Azure Time Series Insights Gen2-omgeving al uw gegevens verwerkt.
> - Deze hoge latentie zou uiteindelijk af moeten gaan naarmate gegevens worden geïndexeerd. Dien een ondersteuningsticket in via Azure Portal als u een continue hoge latentie ervaart.

- Vroegste beschikbaar

![Diagram Vroegstavailable](media/concepts-streaming-event-sources/event-source-earliest-available.png)

- EventSourceCreationTime

![EventSourceCreationTime Diagram](media/concepts-streaming-event-sources/event-source-creation-time.png)

- CustomEnqueuedTime

![CustomEnqueuedTime-diagram](media/concepts-streaming-event-sources/event-source-custom-enqueued-time.png)

## <a name="streaming-ingestion-best-practices"></a>Best practices voor streaming-opname

- Maak altijd een unieke consumentengroep voor uw Azure Time Series Insights Gen2-omgeving om gegevens uit uw gebeurtenisbron te gebruiken. Hergebruik van consumentengroepen kan willekeurige verbroken verbinding veroorzaken en kan leiden tot gegevensverlies.

- Configureer Azure Time Series Insights Gen2-omgeving en uw IoT Hub en/of Event Hubs in dezelfde Azure-regio. Hoewel het mogelijk is om een gebeurtenisbron in een afzonderlijke regio te configureren, wordt dit scenario niet ondersteund en kunnen we hoge beschikbaarheid niet garanderen.

- Ga niet verder dan [](./concepts-streaming-ingress-throughput-limits.md) de doorvoersnelheidslimiet van uw omgeving of per partitielimiet.

- Configureer een [vertragingswaarschuwing om](./time-series-insights-environment-mitigate-latency.md#monitor-latency-and-throttling-with-alerts) een melding te ontvangen als uw omgeving problemen ondervindt met het verwerken van gegevens. Zie [Productieworkloads](./concepts-streaming-ingestion-event-sources.md#production-workloads) hieronder voor voorgestelde waarschuwingsvoorwaarden.

- Gebruik streaming-opname alleen voor bijna realtime en recente gegevens. Het streamen van historische gegevens wordt niet ondersteund.

- Meer informatie over hoe eigenschappen worden afgevlakt en JSON-gegevens [plat worden gemaakt en opgeslagen.](./concepts-json-flattening-escaping-rules.md)

- Volg het principe van de minste bevoegdheden bij het verstrekken van verbindingsreeksen voor gebeurtenisbron. Voor Event Hubs configureert u een beleid voor gedeelde toegang met alleen de *claim* verzenden en gebruikt IoT Hub alleen de *service-verbindingsmachtiging.*

> [!CAUTION]
> Als u uw IoT Hub of Event Hub verwijdert en opnieuw een nieuwe resource met dezelfde naam maakt, moet u een nieuwe gebeurtenisbron maken en de nieuwe IoT Hub of Event Hub koppelen. Gegevens worden pas opgenomen wanneer u deze stap hebt voltooid.

## <a name="production-workloads"></a>Productieworkloads

Naast de bovenstaande best practices raden we u aan het volgende te implementeren voor bedrijfskritieke workloads.

- Verhoog de IoT Hub van uw gegevens of Event Hub naar het maximum van zeven dagen.

- Maak omgevingswaarschuwingen in de Azure Portal. Met waarschuwingen op basis van [metrische](./how-to-monitor-tsi-reference.md#metrics) platformgegevens kunt u end-to-end-pijplijngedrag valideren. De instructies voor het maken en beheren van waarschuwingen staan [hier.](./time-series-insights-environment-mitigate-latency.md#monitor-latency-and-throttling-with-alerts) Voorgestelde waarschuwingsvoorwaarden:

  - IngressReceivedMessagesTimeLag is groter dan 5 minuten
  - IngressReceivedBytes is 0
- Houd de belasting van uw opname in balans tussen uw IoT Hub of Event Hub-partities.

### <a name="historical-data-ingestion"></a>Historische gegevensingestie

Het gebruik van de streaming-pijplijn voor het importeren van historische gegevens wordt momenteel niet ondersteund in Azure Time Series Insights Gen2. Als u eerdere gegevens in uw omgeving wilt importeren, volgt u de onderstaande richtlijnen:

- Stream geen live en historische gegevens parallel. Het opnemen van gegevens in een andere volgorde leidt tot slechtere queryprestaties.
- Historische gegevens opnemen op tijd geordend voor de beste prestaties.
- Blijf binnen de onderstaande limieten voor opnamedoorvoersnelheid.
- Schakel Warme opslag uit als de gegevens ouder zijn dan de bewaarperiode voor warme opslag.

## <a name="event-source-timestamp"></a>Tijdstempel van gebeurtenisbron

Wanneer u een gebeurtenisbron configureert, wordt u gevraagd een tijdstempel-id-eigenschap op te geven. De tijdstempel-eigenschap wordt gebruikt om gebeurtenissen in de tijd bij te houden. Dit is de tijd die wordt gebruikt als tijdstempel in de Query-API's en voor het plotten van reeksen `$ts` in de Azure Time Series Insights Explorer. [](/rest/api/time-series-insights/dataaccessgen2/query/execute) Als er tijdens het maken geen eigenschap wordt opgegeven of als de tijdstempel-eigenschap ontbreekt in een gebeurtenis, wordt de IoT Hub- of Events Hubs-enqueu-tijd van de gebeurtenis gebruikt als de standaardwaarde. Waarden van tijdstempel-eigenschappen worden opgeslagen in UTC.

Over het algemeen kiezen gebruikers ervoor om de tijdstempel-eigenschap aan te passen en de tijd te gebruiken waarop de sensor of tag de leeswaarde heeft gegenereerd in plaats van de standaard hub-enqueued time te gebruiken. Dit is met name nodig wanneer apparaten af en toe verbindingsverlies hebben en een batch vertraagde berichten wordt doorgestuurd naar Azure Time Series Insights Gen2.

Als uw aangepaste tijdstempel binnen een genest JSON-object of een matrix valt, moet u de juiste eigenschapsnaam geven volgens onze naamconventiviteit voor plat maken en [escapen.](concepts-json-flattening-escaping-rules.md) Het tijdstempel van de gebeurtenisbron voor de hier weergegeven JSON-nettolading [moet](concepts-json-flattening-escaping-rules.md#example-a) bijvoorbeeld worden ingevoerd als `"values.time"` .

### <a name="time-zone-offsets"></a>Tijdzone-offsets

Tijdstempels moeten worden verzonden in ISO 8601-indeling en worden opgeslagen in UTC. Als er een tijdzone-offset wordt opgegeven, wordt de offset toegepast en wordt de tijd opgeslagen en geretourneerd in UTC-indeling. Als de offset niet goed is opgemaakt, wordt deze genegeerd. In situaties waarin uw oplossing mogelijk geen context van de oorspronkelijke offset heeft, kunt u de offsetgegevens in een extra afzonderlijke gebeurtenis-eigenschap verzenden om ervoor te zorgen dat deze behouden blijven en dat uw toepassing kan verwijzen in een query-antwoord.

De tijdzone offset moet worden opgemaakt als een van de volgende:

±HHMMZ<br />
±HH:MM<br />
±HH:MMZ

## <a name="next-steps"></a>Volgende stappen

- Lees de [JSON-regels voor plat maken en escapen](./concepts-json-flattening-escaping-rules.md) om te begrijpen hoe gebeurtenissen worden opgeslagen.

- Inzicht in de doorvoerbeperkingen van [uw omgeving](./concepts-streaming-ingress-throughput-limits.md)
