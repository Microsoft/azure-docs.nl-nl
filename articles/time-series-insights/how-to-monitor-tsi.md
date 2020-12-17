---
title: Bewakings Time Series Insights | Microsoft Docs
description: Bewaak Time Series Insights voor Beschik baarheid, prestaties en werking.
author: deepakpalled
ms.author: lyhughes
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/10/2020
ms.custom: lyrana
ms.openlocfilehash: cff0c54cf5aa8854273be9502f5cf6df4e0a055b
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/17/2020
ms.locfileid: "97632737"
---
# <a name="monitoring-time-series-insights"></a>Bewakings Time Series Insights

Wanneer u belang rijke toepassingen en bedrijfs processen hebt die afhankelijk zijn van Azure-resources, wilt u deze resources controleren op hun Beschik baarheid, prestaties en werking. In dit artikel worden de bewakings gegevens beschreven die worden gegenereerd door Time Series Insights en hoe u de functies van Azure Monitor kunt gebruiken om deze gegevens te analyseren en te waarschuwen.

## <a name="monitor-overview"></a>Overzicht van monitor

De pagina **overzicht** in de Azure portal voor elke time series Insights omgeving bevat een korte weer gave van het resource gebruik, zoals het aantal ontvangen berichten en het aantal opgeslagen bytes. Deze informatie is nuttig, maar slechts een klein aantal bewakings gegevens is beschikbaar in dit deel venster. Een deel van deze gegevens wordt automatisch verzameld en is beschikbaar voor analyse zodra u de resource maakt. U kunt extra typen gegevens verzameling inschakelen met een bepaalde configuratie.

## <a name="what-is-azure-monitor"></a>Wat is Azure Monitor

Time Series Insights maakt bewakings gegevens met behulp van [Azure monitor](https://docs.microsoft.com/azure/azure-monitor/overview). Dit is een volledige stack bewakings service in azure met een volledige set functies voor het bewaken van uw Azure-resources naast bronnen in andere Clouds en on-premises.

Begin met het artikel [bewaking van Azure-resources met Azure monitor](https://docs.microsoft.com/azure/azure-monitor/insights/monitor-azure-resource), waarin de volgende concepten worden beschreven:

- Wat is Azure Monitor?
- Kosten die zijn gekoppeld aan bewaking
- Bewaken van gegevens die zijn verzameld in azure
- Gegevens verzameling configureren
- Standaard Programma's in azure voor het analyseren en waarschuwen van bewakings gegevens

In de volgende secties vindt u een beschrijving van de specifieke gegevens die zijn verzameld voor Azure Time Series Insights. In deze secties vindt u ook voor beelden voor het configureren van gegevens verzameling en het analyseren van deze gegevens met Azure-hulpprogram ma's.

> [!TIP]
> Zie [verbruik en geschatte kosten](../azure-monitor/platform/usage-estimated-costs.md)voor meer informatie over de kosten die zijn gekoppeld aan Azure monitor. Zie [gegevens opname tijd vastleggen](../azure-monitor/platform/data-ingestion-time.md)voor meer informatie over de tijd die nodig is om uw gegevens weer te geven in azure monitor.

## <a name="monitoring-data-from-azure-time-series-insights"></a>Gegevens van Azure Time Series Insights bewaken

Azure Time Series Insights worden dezelfde soorten bewakings gegevens verzameld als andere Azure-resources die worden beschreven in [gegevens van Azure-resources bewaken](../azure-monitor/insights/monitor-azure-resource.md#monitoring-data). 

Zie [Azure time series Insights bewakings gegevens Naslag informatie](how-to-monitor-tsi-reference.md) voor een gedetailleerde Naslag informatie over de logboeken en metrieken die u kunt verzamelen.

## <a name="collection-and-routing"></a>Verzameling en route ring

Metrische platform gegevens worden automatisch verzameld en opgeslagen, maar kunnen worden doorgestuurd naar andere locaties met behulp van een diagnostische instelling.

Bron logboeken worden pas verzameld en opgeslagen als u een diagnostische instelling hebt gemaakt en deze naar een of meer locaties wilt door sturen.
Zie [Diagnostische instelling maken voor het verzamelen van platform logboeken en metrische gegevens in azure](../azure-monitor/platform/diagnostic-settings.md) voor het gedetailleerde proces voor het maken van een diagnostische instelling met behulp van de Azure Portal, CLI of Power shell. Wanneer u een diagnostische instelling maakt, geeft u op welke categorieën logboeken u wilt verzamelen.

U kunt Logboeken verzamelen uit de volgende categorieën voor Azure Time Series Insights:

   | Categorie | Beschrijving |
   |---|---|
   | Inkomend verkeer  | In de categorie ingang worden fouten bijgehouden die optreden in de ingangs pijplijn. Deze categorie bevat fouten die optreden bij het ontvangen van gebeurtenissen (zoals fouten bij het maken van verbinding met een gebeurtenis bron) en het verwerken van gebeurtenissen (zoals fouten bij het parseren van een gebeurtenis lading). |

## <a name="analyzing-metrics"></a>Metrische gegevens analyseren

U kunt metrische gegevens voor Azure Time Series Insights analyseren, samen met metrische gegevens uit andere Azure-Services, door metrische gegevens te openen in het menu Azure Monitor. Zie [aan de slag met Azure Metrics Explorer](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-getting-started) voor meer informatie over het gebruik van dit hulp programma.

Zie [Monitoring Azure time series Insights data Reference](how-to-monitor-tsi-reference.md#metrics) (Engelstalig) voor een lijst met de metrische gegevens van het platform.

Dit voor beeld toont het aantal bytes dat van alle gebeurtenis bronnen is ontvangen in uw Azure Time Series Insights omgeving.

**Ontvangen bytes** [ ![ Azure time series ingress ontvangen bytes](media/how-to-monitor-tsi/ingress-received-bytes.png)](media/how-to-monitor-tsi/ingress-received-bytes.png#lightbox)

In het voor beeld ziet u het aantal bytes dat is verwerkt en beschikbaar is voor query's in uw Azure Time Series Insights-omgeving.

In **ingangs opgeslagen bytes** [ ![ Azure time series](media/how-to-monitor-tsi/ingress-stored-bytes.png)](media/how-to-monitor-tsi/ingress-stored-bytes.png#lightbox) inkomend bytes

## <a name="analyzing-logs"></a>Logboeken analyseren
U kunt bron Logboeken openen als een BLOB in een opslag account, als gebeurtenis gegevens of via logboek analyse query's.

Gegevens in Azure Monitor logboeken worden opgeslagen in tabellen waarvan elke tabel een eigen set unieke eigenschappen heeft.

Alle resource Logboeken in Azure Monitor hebben dezelfde velden die worden gevolgd door servicespecifieke velden. Het algemene schema wordt beschreven in [Azure monitor resource-logboek schema](../azure-monitor/platform/resource-logs-schema.md#top-level-common-schema). Zie [Azure time series Insights monitoring data Reference](how-to-monitor-tsi-reference.md#resource-logs)(Engelstalig) voor een lijst met de typen bron logboeken die worden verzameld voor Azure time series Insights.

Azure Time Series Insights slaat gegevens op in de volgende tabellen.

| Tabel | Beschrijving |
|:---|:---|
| TSIIngress | De tabel waarin gegevens worden opgeslagen vanuit de categorie binnenkomend. In de categorie ingang worden fouten bijgehouden die optreden in de ingangs pijplijn. Deze categorie bevat fouten die optreden bij het ontvangen van gebeurtenissen (zoals fouten bij het maken van verbinding met een gebeurtenis bron) en het verwerken van gebeurtenissen (zoals fouten bij het parseren van een gebeurtenis lading).

Als u gegevens wilt omleiden naar Azure Monitor logboeken, moet u een diagnostische instelling maken om bron Logboeken of platform metrieken te verzenden naar een Log Analytics-werk ruimte. Zie [verzameling en route ring](https://docs.microsoft.com/azure/iot-hub/monitor-iot-hub#collection-and-routing)voor meer informatie.

## <a name="sample-queries"></a>Voorbeeld Query's

Hieronder vindt u query's die u kunt gebruiken om uw Azure Time Series Insights-omgeving te bewaken:

+ Meer informatie over de verbindings fouten van gebeurtenis bronnen in de afgelopen vijf dagen:

    ```Kusto
   TSIIngress
   | where OperationName == "Microsoft.TimeSeriesInsights/environments/eventsources/ingress/connect"
   | where _ResourceId contains "<your environment name, event source name, or the full event source resource URL>"
   | where TimeGenerated > ago(5d)

    ```
+ Details ophalen over ongeldige berichten die in de afgelopen vijf dagen zijn ontvangen:

    ```Kusto
   TSIIngress
   | where OperationName == "Microsoft.TimeSeriesInsights/environments/eventsources/ingress/deserialize"
   | where _ResourceId contains "<your environment name, event source name, or the full event source resource URL>"
   | where TimeGenerated > ago(5d)

    ```

## <a name="alerts"></a>Waarschuwingen

Azure Monitor waarschuwingen geven u proactief op de hoogte wanneer er belang rijke voor waarden worden gevonden in uw bewakings gegevens. Hiermee kunt u problemen in uw systeem identificeren en oplossen voordat uw klanten ze opmerken. U kunt waarschuwingen instellen voor [metrische gegevens](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-overview), [Logboeken](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log)en het [activiteiten logboek](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-alerts). Verschillende soorten waarschuwingen hebben voor delen en nadelen.

Wanneer u een waarschuwings regel maakt op basis van de metrische gegevens van het platform, moet u er rekening mee houden dat voor Time Series Insights-platform metrische gegevens die worden verzameld in aantal eenheden, sommige aggregaties mogelijk niet beschikbaar of bruikbaar zijn.

## <a name="next-steps"></a>Volgende stappen

* Zie [Azure time series Insights Naslag informatie over bewakings gegevens](how-to-monitor-tsi-reference.md) voor een verwijzing naar de logboeken en metrieken die zijn gemaakt door Azure time series Insights.
* Zie [Azure-resources bewaken met Azure monitor](../azure-monitor/insights/monitor-azure-resource.md) voor meer informatie over het bewaken van Azure-resources.
