---
title: bestand opnemen
description: bestand opnemen
services: digital-twins
ms.service: digital-twins
ms.topic: include
ms.date: 11/11/2020
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.custom: include file
ms.openlocfilehash: 016ad0e11f3378dba887e0a235f235fa91e3aa03
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98109346"
---
### <a name="property-limits"></a>Eigenschaps limieten

Azure Time Series Insights eigenschaps limieten zijn verhoogd tot 1.000 voor warme opslag en geen eigenschaps limiet voor koude opslag. De opgegeven gebeurtenis eigenschappen hebben bijbehorende JSON-, CSV-en grafiek kolommen die u kunt weer geven in de [Azure time series Insights Gen2 Explorer](../articles/time-series-insights/quickstart-explore-tsi.md).

| SKU | Maximum aantal eigenschappen |
| --- | --- |
| Gen2 (L1) | 1.000 eigenschappen (kolommen) voor warme opslag en onbeperkt voor koude opslag|
| Gen1 (S1) | 600-eigenschappen (kolommen) |
| Gen1 (S2) | 800-eigenschappen (kolommen) |

### <a name="streaming-ingestion"></a>Streamingopname

* Er zijn Maxi maal twee [gebeurtenis bronnen](../articles/time-series-insights/concepts-streaming-ingestion-event-sources.md) per omgeving.

* De aanbevolen procedures en algemene richt lijnen voor gebeurtenis bronnen vindt u [hier](../articles/time-series-insights/concepts-streaming-ingestion-event-sources.md#streaming-ingestion-best-practices)

* Standaard kan Azure Time Series Insights Gen2 binnenkomende gegevens opnemen met een snelheid van **Maxi maal 1 MB per seconde (Mbps) per Azure time series Insights Gen2-omgeving**. Er zijn extra beperkingen [per hub-partitie](../articles/time-series-insights/concepts-streaming-ingress-throughput-limits.md#hub-partitions-and-per-partition-limits). U kunt Maxi maal 2 MBps-tarieven instellen door een ondersteunings ticket in te dienen via de Azure Portal. Lees de [doorvoer limieten](../articles/time-series-insights/concepts-streaming-ingress-throughput-limits.md)voor het opnemen van gegevens stromen voor meer informatie.

### <a name="api-limits"></a>API-limieten

REST API limieten voor Azure Time Series Insights Gen2 zijn opgegeven in de [referentie documentatie van rest API](/rest/api/time-series-insights/preview#limits-1).