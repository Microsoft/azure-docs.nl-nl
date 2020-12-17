---
title: Azure Time Series Insights gegevens referentie bewaken | Microsoft Docs
description: Referentie documentatie voor het bewaken van Azure Time Series Insights.
author: deepakpalled
ms.author: lyhughes
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/10/2020
ms.custom: lyrana
ms.openlocfilehash: 0b564ddfdea2cf24b7f9b1bc608d47fa4cfe541b
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/17/2020
ms.locfileid: "97632669"
---
# <a name="monitoring-azure-time-series-insights-data-reference"></a>Azure Time Series Insights gegevens referentie bewaken

Meer informatie over de gegevens en resources die worden verzameld door Azure Monitor van uw Azure Time Series Insights omgeving. Zie [bewaking time series Insights]( ./how-to-monitor-tsi.md) voor meer informatie over het verzamelen en analyseren van bewakings gegevens.

## <a name="metrics"></a>Metrische gegevens

In deze sectie vindt u alle automatisch verzamelde platform gegevens die zijn verzameld voor Azure Time Series Insights. Zie [Azure monitor ondersteunde metrische gegevens](../azure-monitor/platform/metrics-supported.md)voor een lijst met alle Azure monitor metrische gegevens voor ondersteuning (inclusief Azure time series Insights). De resource provider voor deze metrische gegevens is [micro soft. TimeSeriesInsights/environments/eventsources](../azure-monitor/platform/metrics-supported.md#microsofttimeseriesinsightsenvironmentseventsources) en [micro soft. TimeSeriesInsights/environments](../azure-monitor/platform/metrics-supported.md#microsofttimeseriesinsightsenvironments).


### <a name="ingress"></a>Inkomend verkeer
 
|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Beschrijving|
|---|---|---|---|---|
|IngressReceivedBytes|Ontvangen bytes van binnenkomend verkeer|Bytes|Totaal|Aantal gelezen bytes van de gebeurtenis bron|
|IngressReceivedInvalidMessages|Ongeldige berichten ontvangen|Aantal|Totaal|Aantal ongeldige berichten gelezen uit de gebeurtenis bron|
|IngressReceivedMessages|Ontvangen berichten met ingang|Aantal|Totaal|Aantal berichten dat uit de gebeurtenis bron is gelezen|
|IngressReceivedMessagesCountLag|Vertraging ontvangen inkomende berichten van ingang|Count|Gemiddeld|Verschil tussen het Volg nummer van de laatste bericht in de bron partitie en het Volg nummer van de berichten die worden verwerkt in ingangs gebeurtenissen|
|IngressReceivedMessagesTimeLag|Tijds vertraging ontvangen inkomende berichten|Seconden|Maximum|Verschil tussen het tijdstip waarop het bericht in de bron van de gebeurtenis in de wachtrij staat en de tijd die wordt verwerkt in ingangs|
|IngressStoredBytes|In ingangs opgeslagen bytes|Bytes|Totaal|De totale grootte van de gebeurtenissen die zijn verwerkt en beschikbaar voor de query|
|IngressStoredEvents|Opgeslagen gebeurtenissen in ingangs|Aantal|Totaal|Aantal samengevoegde gebeurtenissen dat is verwerkt en beschikbaar is voor query|

### <a name="storage"></a>Storage

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Beschrijving|
|---|---|---|---|---|
|WarmStorageMaxProperties|Maximale eigenschappen van warme opslag|Count|Maximum|Maximum aantal eigenschappen dat is toegestaan door de omgeving voor de SKU van S1/S2 en het maximum aantal eigenschappen dat is toegestaan door de warme Store voor PAYG SKU|
|WarmStorageUsedProperties|Eigenschappen voor warme opslag gebruikt |Count|Maximum|Aantal eigenschappen dat wordt gebruikt door de omgeving voor de SKU van S1/S2 en het aantal eigenschappen dat door warme Store voor PAYG SKU wordt gebruikt|

## <a name="resource-logs"></a>Resourcelogboeken

In deze sectie vindt u de typen bron logboeken die u kunt verzamelen voor uw Azure Time Series Insights-omgeving.

| Categorie | Weergavenaam | Beschrijving |
|----- |----- |----- |
| Inkomend verkeer | TSIIngress | In de categorie ingang worden fouten bijgehouden die optreden in de ingangs pijplijn. Deze categorie bevat fouten die optreden bij het ontvangen van gebeurtenissen (zoals fouten bij het maken van verbinding met een gebeurtenis bron) en het verwerken van gebeurtenissen (zoals fouten bij het parseren van een gebeurtenis lading). |

## <a name="schemas"></a>Schema 's
De volgende schema's zijn in gebruik door Azure Time Series Insights

### <a name="tsiingress-table"></a>TSIIngress-tabel

| Eigenschap | Beschrijving |
|----- |----- |
| TimeGenerated | Tijd (UTC) waarop deze gebeurtenis wordt gegenereerd. |
| Locatie | De locatie van de resource. |
| Categorie | De categorie van de logboek gebeurtenis. |
| OperationName | De naam van de bewerking van de gebeurtenis. |
| CorrelationId | De correlatie-ID van de aanvraag. |
| Niveau | De ernst van de gebeurtenis. |
| ResultDescription | Beschrijving van het resultaat van de bewerking, zoals ' ontvangen verboden fout '. |
| Bericht | Het bericht dat is gekoppeld aan de fout. Bevat informatie over wat er mis ging en hoe u de fout kunt oplossen. |
| ErrorCode | De code die is gekoppeld aan de fout |
| EventSourceType | Het type van de gebeurtenis bron. Het kan een event hub of IoT hub zijn. |
| EventSourceProperties | Een verzameling eigenschappen die specifiek zijn voor uw gebeurtenis bron. Bevat details zoals de Consumer groep en de naam van de toegangs sleutel. |
