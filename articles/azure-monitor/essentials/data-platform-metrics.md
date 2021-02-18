---
title: Metrische gegevens in Azure Monitor | Microsoft Docs
description: Hierin worden metrische gegevens in Azure Monitor beschreven die lichter worden bewaakt en die vrijwel in realtime scenario's kunnen ondersteunen.
documentationcenter: ''
author: bwren
manager: carmonm
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2019
ms.author: bwren
ms.openlocfilehash: bc2dc3fdb74819a923a3fc4dac89262c1f43ac98
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100609059"
---
# <a name="azure-monitor-metrics-overview"></a>Overzicht van Azure Monitor metrische gegevens
Azure Monitor metrieken is een functie van Azure Monitor die numerieke gegevens uit [bewaakte bronnen](../monitor-reference.md) in een time series-data base verzamelt. Metrische gegevens zijn numerieke waarden die met regel matige tussen pozen worden verzameld en een aspect van een systeem op een bepaald moment beschrijven. Metrische gegevens in Azure Monitor zijn licht gewicht en kunnen bijna realtime-scenario's ondersteunen, waardoor ze vooral nuttig zijn voor waarschuwingen en snelle detectie van problemen. U kunt ze interactief met metrische gegevens Verkenner analyseren, proactief een melding ontvangen wanneer een waarde een drempel overschrijdt of deze visualiseren in een werkmap of dash board.


> [!NOTE]
> Azure Monitor metrieken is een helft van het gegevens platform dat Azure Monitor ondersteunt. De andere is [Azure monitor logboeken](../platform/data-platform-logs.md) waarmee logboek-en prestatie gegevens worden verzameld en ingedeeld en kunnen worden geanalyseerd met een uitgebreide query taal. Metrieken zijn meer licht dan gegevens in Azure Monitor logboeken en kunnen bijna realtime-scenario's ondersteunen, waardoor ze bijzonder nuttig zijn voor waarschuwingen en snelle detectie van problemen. Metrieken kunnen alleen numerieke gegevens in een bepaalde structuur opslaan, terwijl Logboeken verschillende verschillende gegevens typen met hun eigen structuur kunnen opslaan. U kunt ook complexe analyses uitvoeren op logboek gegevens met behulp van logboek query's die niet kunnen worden gebruikt voor de analyse van metrische gegevens.


## <a name="what-can-you-do-with-azure-monitor-metrics"></a>Wat kunt u doen met Azure Monitor metrische gegevens?
De volgende tabel geeft een lijst van de verschillende manieren waarop u metrische gegevens kunt gebruiken in Azure Monitor.

|  |  |
|:---|:---|
| **Analyseren** | Met [metrische gegevens Verkenner](metrics-charts.md) kunt u de verzamelde metrische gegevens in een grafiek analyseren en de metrische gegevens van verschillende resources vergelijken. |
| **Waarschuwing** | Configureer een [waarschuwings regel voor metrische gegevens](../alerts/alerts-metric.md) die een melding verzendt of [geautomatiseerd actie](../alerts/action-groups.md) onderneemt wanneer de metrische waarde een drempel overschrijdt. |
| **Visualiseren** | Een grafiek vastmaken aan een [Azure-dash board](../learn/tutorial-app-dashboards.md)vanuit de metrics Explorer.<br>Een [werkmap](../visualize/workbooks-overview.md) maken om te combi neren met meerdere gegevens sets in een interactief rapport. De resultaten van een query exporteren naar [Grafana](../platform/grafana-plugin.md) om gebruik te maken van Dash boards en combi neren met andere gegevens bronnen. |
| **Automatiseren** |  Gebruik [automatisch schalen](../autoscale/autoscale-overview.md) om resources te verg Roten of te verkleinen op basis van een metrische waarde die een drempel overschrijdt. |
| **Ophalen** | Toegang krijgen tot metrische waarden van een opdracht regel met behulp van  [Power shell-cmdlets](/powershell/module/az.applicationinsights)<br>Toegang krijgen tot meet waarden van aangepaste toepassing met behulp van [rest API](../platform/rest-api-walkthrough.md).<br>Toegang krijgen tot meet waarden van een opdracht regel met behulp van  [cli](/cli/azure/monitor/metrics). |
| **Exporteren** | U [kunt metrische gegevens naar Logboeken routeren](../platform/resource-logs.md#send-to-azure-storage) voor het analyseren van informatie in azure monitor metrieken samen met gegevens in azure monitor-logboeken en om meet waarden langer dan 93 dagen op te slaan.<br>Meet gegevens streamen naar een [Event hub](../platform/stream-monitoring-data-event-hubs.md) om ze naar externe systemen te routeren. |
| **Archiveren** | [Archiveer](../platform/platform-logs-overview.md) de prestaties of de status geschiedenis van uw resource voor naleving, controle of offline rapportage. |

![Overzicht van metrische gegevens](media/data-platform-metrics/metrics-overview.png)


## <a name="data-collection"></a>Gegevens verzamelen
Er zijn drie basis bronnen voor metrische gegevens die worden verzameld door Azure Monitor. Zodra deze metrische gegevens worden verzameld in de data base van de Azure Monitor metriek, kunnen ze samen worden geëvalueerd, ongeacht de bron.

**Azure-resources**. Platform metrieken worden gemaakt door Azure-resources en geven u inzicht in hun status en prestaties. Elk type resource maakt een [afzonderlijke set metrische gegevens](../platform/metrics-supported.md) zonder dat er configuratie is vereist. Metrische platform gegevens worden verzameld van Azure-resources met een frequentie van één minuut, tenzij anders aangegeven in de definitie van de metrische gegevens. 

**Toepassingen**. Metrische gegevens worden gemaakt door Application Insights voor uw bewaakte toepassingen en helpen u bij het detecteren van prestatie problemen en het bijhouden van trends in de manier waarop uw toepassing wordt gebruikt. Dit geldt ook voor de _reactie tijd_ van de server en _browser uitzonderingen_.

**Virtuele-machine agenten**. Metrische gegevens worden verzameld uit het gast besturingssysteem van een virtuele machine. Schakel de metrische gegevens voor het gast besturingssysteem in voor virtuele Windows-machines met [Windows diagnostische extensie (WAD)](../agents/diagnostics-extension-overview.md) en voor virtuele Linux-machines met [InfluxData-telegrafe-agent](https://www.influxdata.com/time-series-platform/telegraf/).

**Aangepaste metrische gegevens**. U kunt metrische gegevens opgeven naast de standaard metrische gegevens die automatisch beschikbaar zijn. U kunt [aangepaste metrische gegevens definiëren in uw toepassing](../app/api-custom-events-metrics.md) die worden bewaakt door Application Insights of aangepaste metrische gegevens voor een Azure-service maken met behulp van de [API voor aangepaste metrische gegevens](../platform/metrics-store-custom-rest-api.md).

- Zie [wat wordt bewaakt door Azure monitor?](../monitor-reference.md) voor een volledige lijst met gegevens bronnen waarmee gegevens kunnen worden verzonden naar Azure monitor metrieken.

## <a name="metrics-explorer"></a>Metrics-explorer
Gebruik [Metrics Explorer](metrics-charts.md) om de gegevens in uw metrische data base interactief te analyseren en de waarden van meerdere metrieken in de loop van de tijd te bepalen. U kunt de grafieken vastmaken aan een dash board om ze te bekijken met andere visualisaties. U kunt ook metrische gegevens ophalen met behulp van de [Azure monitoring rest API](../platform/rest-api-walkthrough.md).

![Metrics Explorer](media/data-platform-metrics/metrics-explorer.png)

- Zie aan de slag [met Azure monitor Metrics Explorer](../platform/metrics-getting-started.md) om aan de slag te gaan met metrische gegevens Verkenner.

## <a name="data-structure"></a>Gegevensstructuur
Gegevens die worden verzameld door Azure Monitor metrieken worden opgeslagen in een Data Base met een tijd reeks die is geoptimaliseerd voor het analyseren van gegevens met tijds tempel. Elke set metrische waarden is een tijd reeks met de volgende eigenschappen:

* Het tijdstip waarop de waarde is verzameld
* De resource waaraan de waarde is gekoppeld
* Een naam ruimte die fungeert als een categorie voor de metriek
* De naam van een metrische waarde
* De waarde zelf
* Sommige metrische gegevens kunnen meerdere dimensies hebben, zoals wordt beschreven in [multi-dimensionale metrische gegevens](#multi-dimensional-metrics). Aangepaste metrische gegevens kunnen Maxi maal 10 dimensies hebben.

## <a name="multi-dimensional-metrics"></a>Multi-dimensionale metrische gegevens
Een van de uitdagingen van metrische gegevens is dat deze regel matig beperkte informatie bevat om context te bieden voor verzamelde waarden. Azure Monitor verhelpt deze uitdaging met multidimensionale metrische gegevens. Afmetingen van een metriek zijn naam/waarde-paren die aanvullende gegevens bevatten om de waarde van de metriek te beschrijven. Een metrische _beschik bare schijf ruimte_ kan bijvoorbeeld een dimensie hebben met de naam _station_ met de waarden _C:_, _D:_, waardoor het weer geven van de beschik bare schijf ruimte op alle stations of elk afzonderlijk station kan worden toegestaan.

In het onderstaande voor beeld ziet u twee gegevens sets voor een hypothetische metriek met de naam _netwerk doorvoer_. De eerste gegevensset heeft geen dimensies. De tweede gegevensset toont de waarden met twee dimensies, het _IP-adres_ en de _richting_:

### <a name="network-throughput"></a>Netwerk doorvoer

| Tijdstempel     | Metrische waarde |
| ------------- |:-------------|
| 8/9/2017 8:14 | 1.331,8 kbps |
| 8/9/2017 8:15 | 1.141,4 kbps |
| 8/9/2017 8:16 | 1.110,2 kbps |

Deze niet-dimensionale metriek kan alleen een basis vraag beantwoorden zoals ' wat is de door Voer van mijn netwerk op een bepaald moment? '

### <a name="network-throughput--two-dimensions-ip-and-direction"></a>Netwerk doorvoer + twee dimensies ("IP" en "richting")

| Tijdstempel     | Dimensie ' IP '   | Dimensie "richting" | Metrische waarde|
| ------------- |:-----------------|:------------------- |:-----------|
| 8/9/2017 8:14 | IP = "192.168.5.2" | Direction = "verzenden"    | 646,5 kbps |
| 8/9/2017 8:14 | IP = "192.168.5.2" | Direction = "ontvangen" | 420,1 kbps |
| 8/9/2017 8:14 | IP = "10.24.2.15"  | Direction = "verzenden"    | 150,0 kbps |
| 8/9/2017 8:14 | IP = "10.24.2.15"  | Direction = "ontvangen" | 115,2 kbps |
| 8/9/2017 8:15 | IP = "192.168.5.2" | Direction = "verzenden"    | 515,2 kbps |
| 8/9/2017 8:15 | IP = "192.168.5.2" | Direction = "ontvangen" | 371,1 kbps |
| 8/9/2017 8:15 | IP = "10.24.2.15"  | Direction = "verzenden"    | 155,0 kbps |
| 8/9/2017 8:15 | IP = "10.24.2.15"  | Direction = "ontvangen" | 100,1 kbps |

Met deze metriek kunt u vragen beantwoorden zoals ' wat is de netwerk doorvoer voor elk IP-adres? ' en ' hoeveel gegevens zijn verzonden versus ontvangen? '. Multidimensionale meet waarden bevatten extra analytische en diagnostische waarde vergeleken met niet-dimensionale metrische gegevens.

## <a name="retention-of-metrics"></a>Metrische gegevens bewaren
Voor de meeste resources in Azure worden metrische gegevens 93 dagen bewaard. Er zijn enkele uitzonde ringen:

**Metrische gegevens van het gast besturingssysteem**
-   **Klassieke waarden voor het besturings systeem** van de gast. Dit zijn prestatie meter items die worden verzameld door de [Windows diagnostische extensie (WAD)](../agents/diagnostics-extension-overview.md) of de [Linux Diagnostic extension (LAD)](../../virtual-machines/extensions/diagnostics-linux.md) en worden doorgestuurd naar een Azure-opslag account. Bewaar termijn voor deze metrische gegevens is 14 dagen.
-   De **metrische gegevens van het gast besturingssysteem worden verzonden naar Azure monitor metrische gegevens**. Dit zijn prestatie meter items die worden verzameld door de [Windows diagnostische extensie (WAD)](../agents/diagnostics-extension-overview.md) en worden verzonden naar de [gegevens Sink van Azure monitor](../agents/diagnostics-extension-overview.md#data-destinations), of via de [InfluxData-telegrafie-agent](https://www.influxdata.com/time-series-platform/telegraf/) op Linux-machines. De Bewaar periode voor deze metrische gegevens is 93 dagen.
-   De **metrische gegevens van het gast besturingssysteem die door log Analytics agent zijn verzameld**. Dit zijn prestatie meter items die worden verzameld door de Log Analytics-agent en worden verzonden naar een Log Analytics-werk ruimte. Het bewaren van deze metrische gegevens is 31 dagen en kan Maxi maal twee jaar worden verlengd.

**Metrische gegevens op basis van het logboek Application Insights**. 
- Achter de scène worden [metrische gegevens op basis van een logboek](../app/pre-aggregated-metrics-log-metrics.md) omgezet in logboek query's. De bijbehorende retentie komt overeen met de retentie van gebeurtenissen in onderliggende logboeken. Voor Application Insights bronnen worden logboeken gedurende 90 dagen bewaard.


> [!NOTE]
> U kunt [de metrische gegevens van het platform voor Azure monitor resources naar een log Analytics-werk ruimte verzenden voor een](../platform/resource-logs.md#send-to-azure-storage) lange termijn trending.





## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [Azure monitor-gegevens platform](../platform/data-platform.md).
- Meer informatie over [logboek gegevens in azure monitor](../platform/data-platform-logs.md).
- Meer informatie over de [beschik bare bewakings gegevens](../agents/data-sources.md) voor verschillende bronnen in Azure.

