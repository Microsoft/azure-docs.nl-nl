---
title: Geheugen-en gelijktijdigheids limieten
description: Bekijk de geheugen-en gelijktijdigheids limieten die zijn toegewezen aan de verschillende prestatie niveaus en resource klassen voor exclusieve SQL-groep in azure Synapse Analytics.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: fb0ad93fb4a1269b4cca02b114c0427f0c44a31b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96455355"
---
# <a name="memory-and-concurrency-limits-for-dedicated-sql-pool-in-azure-synapse-analytics"></a>Geheugen-en gelijktijdigheids limieten voor exclusieve SQL-groep in azure Synapse Analytics

Bekijk de geheugen-en gelijktijdigheids limieten die zijn toegewezen aan de verschillende prestatie niveaus en resource klassen in azure Synapse Analytics.  

## <a name="data-warehouse-capacity-settings"></a>Capaciteits instellingen van Data Warehouse

In de volgende tabellen ziet u de maximale capaciteit voor het Data Warehouse op verschillende prestatie niveaus. Zie [Scale Compute-Portal](quickstart-scale-compute-portal.md)voor meer informatie over het wijzigen van het prestatie niveau.

### <a name="service-levels"></a>Service niveaus

De service niveaus variëren van DW100c tot DW30000c.

| Prestatieniveau | Rekenknooppunten | Distributies per reken knooppunt | Geheugen per Data Warehouse (GB) |
|:-----------------:|:-------------:|:------------------------------:|:------------------------------:|
| DW100c            | 1             | 60                             |    60                          |
| DW200c            | 1             | 60                             |   120                          |
| DW300c            | 1             | 60                             |   180                          |
| DW400c            | 1             | 60                             |   240                          |
| DW500c            | 1             | 60                             |   300                          |
| DW1000c           | 2             | 30                             |   600                          |
| DW1500c           | 3             | 20                             |   900                          |
| DW2000c           | 4             | 15                             |  1200                          |
| DW2500c           | 5             | 12                             |  1500                          |
| DW3000c           | 6             | 10                             |  1800                          |
| DW5000c           | 10            | 6                              |  3000                          |
| DW6000c           | 12            | 5                              |  3600                          |
| DW7500c           | 15            | 4                              |  4500                          |
| DW10000c          | 20            | 3                              |  6000                          |
| DW15000c          | 30            | 2                              |  9000                          |
| DW30000c          | 60            | 1                              | 18000                          |

Het maximale service niveau is DW30000c, met 60 Compute-knoop punten en één distributie per Compute-knoop punt. Een voor beeld: een Data Warehouse van 600 TB op DW30000c verwerkt ongeveer 10 TB per reken knooppunt.

## <a name="concurrency-maximums-for-workload-groups"></a>Gelijktijdige maximum waarden voor werkbelasting groepen

Met de introductie van [werkbelasting groepen](sql-data-warehouse-workload-isolation.md)is het concept van gelijktijdigheids sleuven niet langer van toepassing.  Resources per aanvraag worden toegewezen op basis van een percentage en opgegeven in de definitie van de werkbelasting groep.  Zelfs met het verwijderen van gelijktijdigheids sleuven, zijn er echter mini maal vereiste resources per query op basis van het service niveau.  In de onderstaande tabel is de minimale hoeveelheid resources gedefinieerd die is vereist per query op service niveaus en de bijbehorende gelijktijdigheid die kan worden behaald.

|Serviceniveau|Maximum aantal gelijktijdige query's|Min% ondersteund voor REQUEST_MIN_RESOURCE_GRANT_PERCENT|
|---|---|---|
|DW100c|4|25%|
|DW200c|8|12,5%|
|DW300c|12|8%|
|DW400c|16|6,25%|
|DW500c|20|5%|
|DW1000c|32|3%|
|DW1500c|32|3%|
|DW2000c|48|2%|
|DW2500c|48|2%|
|DW3000c|64|1,5%|
|DW5000c|64|1,5%|
|DW6000c|128|0,75%|
|DW7500c|128|0,75%|
|DW10000c|128|0,75%|
|DW15000c|128|0,75%|
|DW30000c|128|0,75%|
||||

## <a name="concurrency-maximums-for-resource-classes"></a>Gelijktijdige maximum waarden voor resource klassen

Om ervoor te zorgen dat elke query voldoende bronnen heeft om efficiënt uit te voeren, Synapse SQL traceert resource gebruik door gelijktijdigheids sleuven aan elke query toe te wijzen. Het systeem plaatst query's in een wachtrij op basis van urgentie en gelijktijdigheids sleuven. Query's wachten in de wachtrij totdat er voldoende gelijktijdigheids sleuven beschikbaar zijn. [Urgentie](sql-data-warehouse-workload-importance.md) en gelijktijdigheids sleuven bepalen de prioriteit van de CPU. Zie [uw workload analyseren](analyze-your-workload.md) voor meer informatie

**Statische resource klassen**

In de volgende tabel ziet u het maximum aantal gelijktijdige query's en gelijktijdigheids sleuven voor elke [statische resource klasse](resource-classes-for-workload-management.md).  

| Serviceniveau | Maximum aantal gelijktijdige query's | Beschik bare gelijktijdigheids sleuven | Sleuven die worden gebruikt door staticrc10 | Sleuven die worden gebruikt door staticrc20 | Sleuven die worden gebruikt door staticrc30 | Sleuven die worden gebruikt door staticrc40 | Sleuven die worden gebruikt door staticrc50 | Sleuven die worden gebruikt door bron staticrc60 | Sleuven die worden gebruikt door staticrc70 | Sleuven die worden gebruikt door staticrc80 |
|:-------------:|:--------------------------:|:---------------------------:|:---------:|:----------:|:----------:|:----------:|:----------:|:----------:|:----------:|:----------:|
| DW100c        |  4                         |    4                        | 1         | 2          | 4          | 4          | 4         |  4         |  4         |  4         |
| DW200c        |  8                         |    8                        | 1         | 2          | 4          | 8          |  8         |  8         |  8         |  8        |
| DW300c        | 12                         |   12                        | 1         | 2          | 4          | 8          |  8         |  8         |  8         |   8        |
| DW400c        | 16                         |   16                        | 1         | 2          | 4          | 8          | 16         | 16         | 16         |  16        |
| DW500c        | 20                         |   20                        | 1         | 2          | 4          | 8          | 16         | 16         | 16         |  16        |
| DW1000c       | 32                         |   40                        | 1         | 2          | 4          | 8          | 16         | 32         | 32         |  32        |
| DW1500c       | 32                         |   60                        | 1         | 2          | 4          | 8          | 16         | 32         | 32         |  32        |
| DW2000c       | 48                         |   80                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW2500c       | 48                         |  100                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW3000c       | 64                         |  120                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW5000c       | 64                         |  200                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW6000c       | 128                        |  240                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW7500c       | 128                        |  300                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW10000c      | 128                        |  400                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW15000c      | 128                        |  600                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW30000c      | 128                        | 1200                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |

**Dynamische resource klassen**

In de volgende tabel ziet u het maximum aantal gelijktijdige query's en gelijktijdigheids sleuven voor elke [dynamische resource klasse](resource-classes-for-workload-management.md). Dynamische resource klassen gebruiken een geheugen toewijzing van 3-10-22-70 voor kleine, middel grote-xlarge resource klassen in alle service niveaus.

| Serviceniveau | Maximum aantal gelijktijdige query's | Beschik bare gelijktijdigheids sleuven | Sleuven die worden gebruikt door smallrc | Sleuven die worden gebruikt door mediumrc | Sleuven die worden gebruikt door largerc | Sleuven die worden gebruikt door xlargerc |
|:-------------:|:--------------------------:|:---------------------------:|:---------------------:|:----------------------:|:---------------------:|:----------------------:|
| DW100c        |  4                         |    4                        | 1                     |  1                     |  1                    |   2                    |
| DW200c        |  8                         |    8                        | 1                     |  1                     |  1                    |   5                    |
| DW300c        | 12                         |   12                        | 1                     |  1                     |  2                    |   8                    |
| DW400c        | 16                         |   16                        | 1                     |  1                     |  3                    |  11                    |
| DW500c        | 20                         |   20                        | 1                     |  2                     |  4                    |  14                    |
| DW1000c       | 32                         |   40                        | 1                     |  4                     |  8                    |  28                    |
| DW1500c       | 32                         |   60                        | 1                     |  6                     |  13                   |  42                    |
| DW2000c       | 32                         |   80                        | 2                     |  8                     |  17                   |  56                    |
| DW2500c       | 32                         |  100                        | 3                     | 10                     |  22                   |  70                    |
| DW3000c       | 32                         |  120                        | 3                     | 12                     |  26                   |  84                    |
| DW5000c       | 32                         |  200                        | 6                     | 20                     |  44                   | 140                    |
| DW6000c       | 32                         |  240                        | 7                     | 24                     |  52                   | 168                    |
| DW7500c       | 32                         |  300                        | 9                     | 30                     |  66                   | 210                    |
| DW10000c      | 32                         |  400                        | 12                    | 40                     |  88                   | 280                    |
| DW15000c      | 32                         |  600                        | 18                    | 60                     | 132                   | 420                    |
| DW30000c      | 32                         | 1200                        | 36                    | 120                    | 264                   | 840                    |

Als er niet voldoende gelijktijdigheids sleuven beschikbaar zijn om de uitvoering van query's te starten, worden query's in de wachtrij geplaatst en uitgevoerd op basis van belang.  Als er sprake is van een gelijkwaardige prioriteit, worden query's uitgevoerd op basis van de eerste in.  Wanneer een query is voltooid en het aantal query's en sleuven onder de limiet valt, worden in azure Synapse Analytics query's in de wachtrij geplaatst.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor meer informatie over het gebruik van resource klassen voor het optimaliseren van uw werk belasting:

* [Resource klassen voor workload Management](resource-classes-for-workload-management.md)
* [De werk belasting analyseren](analyze-your-workload.md)
