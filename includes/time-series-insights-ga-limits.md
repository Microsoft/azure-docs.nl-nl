---
title: bestand opnemen
description: bestand opnemen
services: digital-twins
ms.service: digital-twins
ms.topic: include
ms.date: 07/09/2020
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.custom: include file
ms.openlocfilehash: e584b6eff16636f0657c586f6c630dbf8bbb99b2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96027101"
---
Hieronder vindt u een overzicht van de belangrijkste limieten in Azure Time Series Insights gen1.

### <a name="sku-ingress-rates-and-capacities"></a>Tarieven en capaciteit van de SKU

De snelheid en capaciteit van de SKU van de sku's van de S2 bieden flexibiliteit bij het configureren van een nieuwe Azure Time Series Insights-omgeving. Uw SKU-capaciteit geeft uw dagelijkse ingangs rente aan op basis van het aantal gebeurtenissen of opgeslagen bytes, afhankelijk van wat het eerste komt. Houd er rekening mee dat ingangen *per minuut* wordt gemeten en dat **beperking** wordt toegepast met behulp van het token bucket algoritme. Binnenkomend verkeer wordt gemeten in blokken van 1 KB. Zo wordt een werkelijke gebeurtenis van 0,8 KB gemeten als één gebeurtenis, en wordt een gebeurtenis van 2,6 KB geteld als drie gebeurtenissen.

| Capaciteit van S1-SKU | Ingangs frequentie | Maximale opslag capaciteit
| --- | --- | --- |
| 1 | 1 GB (1.000.000 gebeurtenissen) per dag | 30 GB (30.000.000 gebeurtenissen) per maand |
| 10 | 10 GB (10.000.000 gebeurtenissen) per dag | 300 GB (300.000.000 gebeurtenissen) per maand |

| Capaciteit van S2-SKU | Ingangs frequentie | Maximale opslag capaciteit
| --- | --- | --- |
| 1 | 10 GB (10.000.000 gebeurtenissen) per dag | 300 GB (300.000.000 gebeurtenissen) per maand |
| 10 | 100 GB (100.000.000 gebeurtenissen) per dag | 3 TB (3.000.000.000 gebeurtenissen) per maand |

> [!NOTE]
> Capaciteit schaalt lineair, dus een S1-SKU met capaciteit 2 ondersteunt 2 GB (2.000.000) gebeurtenissen per dag ingangs snelheden en 60 GB (60.000.000 gebeurtenissen) per maand.

S2-SKU-omgevingen ondersteunen aanzienlijk meer gebeurtenissen per maand en hebben een aanzienlijk hogere ingangs capaciteit.

| SKU  | Aantal gebeurtenissen per maand  | Aantal gebeurtenissen per minuut | Gebeurtenis grootte per minuut  |
|---------|---------|---------|---------|---------|
| S1     |   30.000.000   |  720    |  720 KB   |
 |S2     |   300.000.000   | 7.200   | 7.200 KB  |

### <a name="property-limits"></a>Eigenschaps limieten

Limieten voor gen1-eigenschappen zijn afhankelijk van de geselecteerde SKU-omgeving. De opgegeven gebeurtenis eigenschappen hebben bijbehorende JSON-, CSV-en grafiek kolommen die kunnen worden weer gegeven in de [Azure time series Insights Explorer](../articles/time-series-insights/time-series-quickstart.md).

| SKU | Maximum aantal eigenschappen |
| --- | --- |
| S1 | 600-eigenschappen (kolommen) |
| S2 | 800-eigenschappen (kolommen) |

### <a name="event-sources"></a>Gebeurtenisbronnen

Er worden Maxi maal twee gebeurtenis bronnen per instantie ondersteund.

* Meer informatie over het [toevoegen van een event hub bron](../articles/time-series-insights/how-to-ingest-data-event-hub.md).
* [Een IOT hub-bron](../articles/time-series-insights/how-to-ingest-data-iot-hub.md)configureren.

### <a name="api-limits"></a>API-limieten

REST API limieten voor Azure Time Series Insights gen1 zijn opgegeven in de [referentie documentatie van rest API](/rest/api/time-series-insights/dataaccess(preview)/query/getavailability).