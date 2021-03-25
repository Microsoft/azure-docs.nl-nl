---
title: vCore-resourcelimieten voor individuele databases
description: Op deze pagina worden enkele veelvoorkomende limieten voor vCore bronnen voor één data base in Azure SQL Database beschreven.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: reference
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/23/2021
ms.openlocfilehash: d38dc7b2bf56ba91fcf77ebdb81836c8c398a52d
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2021
ms.locfileid: "105023844"
---
# <a name="resource-limits-for-single-databases-using-the-vcore-purchasing-model"></a>Resourcelimieten voor individuele databases met gebruikmaking van het vCore-aankoopmodel
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

In dit artikel vindt u gedetailleerde resource limieten voor afzonderlijke data bases in Azure SQL Database het inkoop model vCore gebruiken.

Zie [overzicht van resource limieten op een server](resource-limits-logical-server.md)voor de limieten voor DTU-aankoop modellen voor afzonderlijke data bases op een server.

U kunt de servicelaag, de reken grootte (Service doelstelling) en de opslag hoeveelheid voor één data base instellen met behulp van de [Azure Portal](single-database-manage.md#the-azure-portal), [Transact-SQL](single-database-manage.md#transact-sql-t-sql), [Power shell](single-database-manage.md#powershell), de [Azure cli](single-database-manage.md#the-azure-cli)of de [rest API](single-database-manage.md#rest-api).

> [!IMPORTANT]
> Zie [Eén data base schalen](single-database-scale.md)voor richt lijnen en overwegingen voor schalen.

## <a name="general-purpose---serverless-compute---gen5"></a>Algemeen gebruik-serverloze Compute-GEN5

De [Compute-laag zonder server](serverless-tier-overview.md) is momenteel alleen beschikbaar op GEN5-hardware.

### <a name="gen5-compute-generation-part-1"></a>GEN5 Compute Generation (deel 1)

|Berekenings grootte (Service doelstelling)|GP_S_Gen5_1|GP_S_Gen5_2|GP_S_Gen5_4|GP_S_Gen5_6|GP_S_Gen5_8|
|:--- | --: |--: |--: |--: |--: |
|Compute genereren|GEN5|GEN5|GEN5|GEN5|GEN5|
|Min-maximale vCores|0.5-1|0.5-2|0,5-4|0,75-6|1.0-8|
|Min-Maxi maal geheugen (GB)|2.02-3|2.05-6|2.10-12|2,25-18|3,00-24|
|Min-maximale vertraging voor automatisch onderbreken (minuten)|60-10080|60-10080|60-10080|60-10080|60-10080|
|Column Store-ondersteuning|Ja*|Ja|Ja|Ja|Ja|
|OLTP-opslag in het geheugen (GB)|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Maximale gegevens grootte (GB)|512|1024|1024|1024|1536|
|Maximale logboek grootte (GB)|154|307|307|307|461|
|Maximale gegevens grootte TempDB (GB)|32|64|128|192|256|
|Opslagtype|Externe SSD|Externe SSD|Externe SSD|Externe SSD|Externe SSD|
|I/o-latentie (bij benadering)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|
|Maximum aantal gegevens IOPS \*\*|320|640|1280|1920|2560|
|Maximale logboek frequentie (MBps)|4.5|9|18|27|36|
|Maxi maal aantal gelijktijdige werk nemers (aanvragen)|75|150|300|450|600|
|Maximaal aantal gelijktijdige sessies|30.000|30.000|30.000|30.000|30.000|
|Aantal replica's|1|1|1|1|1|
|Meerdere AZ|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Uitschalen voor leesbewerking|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Opgenomen back-upopslag|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|

\* Voor service doelstellingen met een kleinere maximale VCore-configuratie is mogelijk onvoldoende geheugen beschikbaar voor het maken en gebruiken van Column Store-indexen.  Als er problemen met de prestaties van het kolom archief optreden, verhoogt u de maximale VCore-configuratie om het Maxi maal beschik bare geheugen te verg Roten.  
\*\* De maximum waarde voor i/o-grootten tussen 8 KB en 64 KB. Werkelijke IOPS zijn werk belasting afhankelijk. Zie [Data io governance](resource-limits-logical-server.md#resource-governance)voor meer informatie.

### <a name="gen5-compute-generation-part-2"></a>GEN5 Compute Generation (deel 2)

|Berekenings grootte (Service doelstelling)|GP_S_Gen5_10|GP_S_Gen5_12|GP_S_Gen5_14|GP_S_Gen5_16|
|:--- | --: |--: |--: |--: |
|Compute genereren|GEN5|GEN5|GEN5|GEN5|
|Min-maximale vCores|1,25-10|1,50-12|1,75-14|2.00-16|
|Min-Maxi maal geheugen (GB)|3,75-30|4.50-36|5,25-42|6,00-48|
|Min-maximale vertraging voor automatisch onderbreken (minuten)|60-10080|60-10080|60-10080|60-10080|
|Column Store-ondersteuning|Ja|Ja|Ja|Ja|
|OLTP-opslag in het geheugen (GB)|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Maximale gegevens grootte (GB)|1536|3072|3072|3072|
|Maximale logboek grootte (GB)|461|461|461|922|
|Maximale gegevens grootte TempDB (GB)|320|384|448|512|
|Opslagtype|Externe SSD|Externe SSD|Externe SSD|Externe SSD|
|I/o-latentie (bij benadering)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|
|Max. gegevensiops *|3200|3840|4480|5120|
|Maximale logboek frequentie (MBps)|36|36|36|36|
|Maxi maal aantal gelijktijdige werk nemers (aanvragen)|750|900|1050|1200|
|Maximaal aantal gelijktijdige sessies|30.000|30.000|30.000|30.000|
|Aantal replica's|1|1|1|1|
|Meerdere AZ|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Uitschalen voor leesbewerking|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Opgenomen back-upopslag|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|

\* De maximum waarde voor i/o-grootten tussen 8 KB en 64 KB. Werkelijke IOPS zijn werk belasting afhankelijk. Zie [Data io governance](resource-limits-logical-server.md#resource-governance)voor meer informatie.

### <a name="gen5-compute-generation-part-3"></a>GEN5 Compute Generation (deel 3)

|Berekenings grootte (Service doelstelling)|GP_S_Gen5_18|GP_S_Gen5_20|GP_S_Gen5_24|GP_S_Gen5_32|GP_S_Gen5_40|
|:--- | --: |--: |--: |--: |--:|
|Compute genereren|GEN5|GEN5|GEN5|GEN5|GEN5|
|Min-maximale vCores|2,25-18|2,5-20|3-24|4-32|5-40|
|Min-Maxi maal geheugen (GB)|6.75-54|7.5-60|9-72|12-96|15-120|
|Min-maximale vertraging voor automatisch onderbreken (minuten)|60-10080|60-10080|60-10080|60-10080|60-10080|
|Column Store-ondersteuning|Ja|Ja|Ja|Ja|Ja|
|OLTP-opslag in het geheugen (GB)|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Maximale gegevens grootte (GB)|3072|3072|4096|4096|4096|
|Maximale logboek grootte (GB)|922|922|1024|1024|1024|
|Maximale gegevens grootte TempDB (GB)|576|640|768|1024|1280|
|Opslagtype|Externe SSD|Externe SSD|Externe SSD|Externe SSD|Externe SSD|
|I/o-latentie (bij benadering)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|
|Max. gegevensiops *|5760|6400|7680|10240|12800|
|Maximale logboek frequentie (MBps)|36|36|36|36|36|
|Maxi maal aantal gelijktijdige werk nemers (aanvragen)|1350|1500|1800|2400|3000|
|Maximaal aantal gelijktijdige sessies|30.000|30.000|30.000|30.000|30.000|
|Aantal replica's|1|1|1|1|1|
|Meerdere AZ|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Uitschalen voor leesbewerking|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Opgenomen back-upopslag|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|

\* De maximum waarde voor i/o-grootten tussen 8 KB en 64 KB. Werkelijke IOPS zijn werk belasting afhankelijk. Zie [Data io governance](resource-limits-logical-server.md#resource-governance)voor meer informatie.


## <a name="hyperscale---provisioned-compute---gen4"></a>Grootschalige-ingerichte Compute-Gen4

### <a name="gen4-compute-generation-part-1"></a>Gen4 Compute Generation (deel 1)

|Berekenings grootte (Service doelstelling)|HS_Gen4_1|HS_Gen4_2|HS_Gen4_3|HS_Gen4_4|HS_Gen4_5|HS_Gen4_6|
|:--- | --: |--: |--: |---: | --: |--: |
|Compute genereren|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|vCores|1|2|3|4|5|6|
|Geheugen (GB)|7|14|21|28|35|42|
|[RBPEX](service-tier-hyperscale.md#compute) Size|3X-geheugen|3X-geheugen|3X-geheugen|3X-geheugen|3X-geheugen|3X-geheugen|
|Column Store-ondersteuning|Ja|Ja|Ja|Ja|Ja|Ja|
|OLTP-opslag in het geheugen (GB)|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Maximale gegevens grootte (TB)|100 |100 |100 |100 |100 |100|
|Maximale logboek grootte (TB)|Onbeperkt |Onbeperkt |Onbeperkt |Onbeperkt |Onbeperkt |Onbeperkt |
|Maximale gegevens grootte TempDB (GB)|32|64|96|128|160|192|
|Opslagtype| [Opmerking 1](#notes) |[Opmerking 1](#notes)|[Opmerking 1](#notes) |[Opmerking 1](#notes) |[Opmerking 1](#notes) |[Opmerking 1](#notes) |
|Max. aantal lokale SSD-IOPS *|4000 |8000 |12000 |16000 |20.000 |24000 |
|Maximale logboek frequentie (MBps)|100 |100 |100 |100 |100 |100 |
|I/o-latentie (bij benadering)|[Opmerking 2](#notes)|[Opmerking 2](#notes)|[Opmerking 2](#notes)|[Opmerking 2](#notes)|[Opmerking 2](#notes)|[Opmerking 2](#notes)|
|Maxi maal aantal gelijktijdige werk nemers (aanvragen)|200|400|600|800|1000|1200|
|Maximaal aantal gelijktijdige sessies|30.000|30.000|30.000|30.000|30.000|30.000|
|Secundaire replica's|0-4|0-4|0-4|0-4|0-4|0-4|
|Meerdere AZ|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Uitschalen voor leesbewerking|Ja|Ja|Ja|Ja|Ja|Ja|
|Back-upopslag bewaren|7 dagen|7 dagen|7 dagen|7 dagen|7 dagen|7 dagen|
|||

\* Behalve lokale SSD IO gebruiken werk belastingen externe pagina- [Server](service-tier-hyperscale.md#page-server) -io. Effectief IOPS is afhankelijk van de werk belasting. Zie [Data io governance](resource-limits-logical-server.md#resource-governance)en [Data io in statistieken voor bron gebruik](hyperscale-performance-diagnostics.md#data-io-in-resource-utilization-statistics)voor meer informatie.

### <a name="gen4-compute-generation-part-2"></a>Gen4 Compute Generation (deel 2)

|Berekenings grootte (Service doelstelling)|HS_Gen4_7|HS_Gen4_8|HS_Gen4_9|HS_Gen4_10|HS_Gen4_16|HS_Gen4_24|
|:--- | ---: |--: |--: | --: |--: |--: |
|Compute genereren|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|vCores|7|8|9|10|16|24|
|Geheugen (GB)|49|56|63|70|112|159,5|
|[RBPEX](service-tier-hyperscale.md#compute) Size|3X-geheugen|3X-geheugen|3X-geheugen|3X-geheugen|3X-geheugen|3X-geheugen|
|Column Store-ondersteuning|Ja|Ja|Ja|Ja|Ja|Ja|
|OLTP-opslag in het geheugen (GB)|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Maximale gegevens grootte (TB)|100 |100 |100 |100 |100 |100 |
|Maximale logboek grootte (TB)|Onbeperkt |Onbeperkt |Onbeperkt |Onbeperkt |Onbeperkt |Onbeperkt |
|Maximale gegevens grootte TempDB (GB)|224|256|288|320|512|768|
|Opslagtype| [Opmerking 1](#notes) |[Opmerking 1](#notes) |[Opmerking 1](#notes) |[Opmerking 1](#notes) |[Opmerking 1](#notes) |[Opmerking 1](#notes) |
|Max. aantal lokale SSD-IOPS *|28000 |32000 |36000 |40000 |64000 |76800 |
|Maximale logboek frequentie (MBps)|100 |100 |100 |100 |100 |100 |
|I/o-latentie (bij benadering)|[Opmerking 2](#notes)|[Opmerking 2](#notes)|[Opmerking 2](#notes)|[Opmerking 2](#notes)|[Opmerking 2](#notes)|[Opmerking 2](#notes)|
|Maxi maal aantal gelijktijdige werk nemers (aanvragen)|1400|1600|1800|2000|3200|4800|
|Maximaal aantal gelijktijdige sessies|30.000|30.000|30.000|30.000|30.000|30.000|
|Secundaire replica's|0-4|0-4|0-4|0-4|0-4|0-4|
|Meerdere AZ|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Uitschalen voor leesbewerking|Ja|Ja|Ja|Ja|Ja|Ja|
|Back-upopslag bewaren|7 dagen|7 dagen|7 dagen|7 dagen|7 dagen|7 dagen|
|||

\* Behalve lokale SSD IO gebruiken werk belastingen externe pagina- [Server](service-tier-hyperscale.md#page-server) -io. Effectief IOPS is afhankelijk van de werk belasting. Zie [Data io governance](resource-limits-logical-server.md#resource-governance)en [Data io in statistieken voor bron gebruik](hyperscale-performance-diagnostics.md#data-io-in-resource-utilization-statistics)voor meer informatie.

## <a name="hyperscale---provisioned-compute---gen5"></a>Grootschalige-ingerichte Compute-GEN5

### <a name="gen5-compute-generation-part-1"></a>GEN5 Compute Generation (deel 1)

|Berekenings grootte (Service doelstelling)|HS_Gen5_2|HS_Gen5_4|HS_Gen5_6|HS_Gen_8|HS_Gen5_10|HS_Gen5_12|HS_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |--: |
|Compute genereren|GEN5|GEN5|GEN5|GEN5|GEN5|GEN5|GEN5|
|vCores|2|4|6|8|10|12|14|
|Geheugen (GB)|10.4|20,8|31,1|41,5|51,9|62,3|72,7|
|[RBPEX](service-tier-hyperscale.md#compute) Size|3X-geheugen|3X-geheugen|3X-geheugen|3X-geheugen|3X-geheugen|3X-geheugen|3X-geheugen|
|Column Store-ondersteuning|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|OLTP-opslag in het geheugen (GB)|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Maximale gegevens grootte (TB)|100 |100 |100 |100 |100 |100 |100|
|Maximale logboek grootte (TB)|Onbeperkt |Onbeperkt |Onbeperkt |Onbeperkt |Onbeperkt |Onbeperkt |Onbeperkt |
|Maximale gegevens grootte TempDB (GB)|64|128|192|256|320|384|448|
|Opslagtype| [Opmerking 1](#notes) |[Opmerking 1](#notes)|[Opmerking 1](#notes) |[Opmerking 1](#notes) |[Opmerking 1](#notes) |[Opmerking 1](#notes) |[Opmerking 1](#notes) |
|Max. aantal lokale SSD-IOPS *|8000 |16000 |24000 |32000 |40000 |48000 |56000 |
|Maximale logboek frequentie (MBps)|100 |100 |100 |100 |100 |100 |100 |
|I/o-latentie (bij benadering)|[Opmerking 2](#notes)|[Opmerking 2](#notes)|[Opmerking 2](#notes)|[Opmerking 2](#notes)|[Opmerking 2](#notes)|[Opmerking 2](#notes)|[Opmerking 2](#notes)|
|Maxi maal aantal gelijktijdige werk nemers (aanvragen)|200|400|600|800|1000|1200|1400|
|Maximaal aantal gelijktijdige sessies|30.000|30.000|30.000|30.000|30.000|30.000|30.000|
|Secundaire replica's|0-4|0-4|0-4|0-4|0-4|0-4|0-4|
|Meerdere AZ|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Uitschalen voor leesbewerking|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|Back-upopslag bewaren|7 dagen|7 dagen|7 dagen|7 dagen|7 dagen|7 dagen|7 dagen|
|||

\* Behalve lokale SSD IO gebruiken werk belastingen externe pagina- [Server](service-tier-hyperscale.md#page-server) -io. Effectief IOPS is afhankelijk van de werk belasting. Zie [Data io governance](resource-limits-logical-server.md#resource-governance)en [Data io in statistieken voor bron gebruik](hyperscale-performance-diagnostics.md#data-io-in-resource-utilization-statistics)voor meer informatie.

### <a name="gen5-compute-generation-part-2"></a>GEN5 Compute Generation (deel 2)

|Berekenings grootte (Service doelstelling)|HS_Gen5_16|HS_Gen5_18|HS_Gen5_20|HS_Gen_24|HS_Gen5_32|HS_Gen5_40|HS_Gen5_80|
|:--- | --: |--: |--: |--: |---: |--: |--: |
|Compute genereren|GEN5|GEN5|GEN5|GEN5|GEN5|GEN5|GEN5|
|vCores|16|18|20|24|32|40|80|
|Geheugen (GB)|83|93.4|103.8|124,6|166,1|207,6|415,2|
|[RBPEX](service-tier-hyperscale.md#compute) Size|3X-geheugen|3X-geheugen|3X-geheugen|3X-geheugen|3X-geheugen|3X-geheugen|3X-geheugen|
|Column Store-ondersteuning|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|OLTP-opslag in het geheugen (GB)|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Maximale gegevens grootte (TB)|100 |100 |100 |100 |100 |100 |100 |
|Maximale logboek grootte (TB)|Onbeperkt |Onbeperkt |Onbeperkt |Onbeperkt |Onbeperkt |Onbeperkt |Onbeperkt |
|Maximale gegevens grootte TempDB (GB)|512|576|640|768|1024|1280|2560|
|Opslagtype| [Opmerking 1](#notes) |[Opmerking 1](#notes)|[Opmerking 1](#notes)|[Opmerking 1](#notes) |[Opmerking 1](#notes) |[Opmerking 1](#notes) |[Opmerking 1](#notes) |
|Max. aantal lokale SSD-IOPS *|64000 |72000 |80000 |96000 |128000 |160000 |204800 |
|Maximale logboek frequentie (MBps)|100 |100 |100 |100 |100 |100 |100 |
|I/o-latentie (bij benadering)|[Opmerking 2](#notes)|[Opmerking 2](#notes)|[Opmerking 2](#notes)|[Opmerking 2](#notes)|[Opmerking 2](#notes)|[Opmerking 2](#notes)|[Opmerking 2](#notes)|
|Maxi maal aantal gelijktijdige werk nemers (aanvragen)|1600|1800|2000|2400|3200|4000|8000|
|Maximaal aantal gelijktijdige sessies|30.000|30.000|30.000|30.000|30.000|30.000|30.000|
|Secundaire replica's|0-4|0-4|0-4|0-4|0-4|0-4|0-4|
|Meerdere AZ|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Uitschalen voor leesbewerking|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|Back-upopslag bewaren|7 dagen|7 dagen|7 dagen|7 dagen|7 dagen|7 dagen|7 dagen|
|||

\* Behalve lokale SSD IO gebruiken werk belastingen externe pagina- [Server](service-tier-hyperscale.md#page-server) -io. Effectief IOPS is afhankelijk van de werk belasting. Zie [Data io governance](resource-limits-logical-server.md#resource-governance)en [Data io in statistieken voor bron gebruik](hyperscale-performance-diagnostics.md#data-io-in-resource-utilization-statistics)voor meer informatie.

#### <a name="notes"></a>Notities

**Opmerking 1**: grootschalige is een architectuur met meerdere lagen met afzonderlijke Compute-en opslag onderdelen: [grootschalige service tier-architectuur](service-tier-hyperscale.md#distributed-functions-architecture)

**Opmerking 2**: de latentie is 1-2 MS voor gegevens op de lokale SSD van de compute-replica, waarmee de meeste gebruikte gegevens pagina's in de cache worden opgeslagen. Hogere latentie voor gegevens die zijn opgehaald van pagina servers.

## <a name="hyperscale---provisioned-compute---dc-series"></a>Grootschalige-ingerichte Compute-DC-serie

|Berekenings grootte (Service doelstelling)|HS_DC_2|HS_DC_4|HS_DC_6|HS_DC_8|
|:--- | --: |--: |--: |--: |---: | 
|Compute genereren|DC-serie|DC-serie|DC-serie|DC-serie|
|vCores|2|4|6|8|
|Geheugen (GB)|9|18|27|36|
|[RBPEX](service-tier-hyperscale.md#compute) Size|3X-geheugen|3X-geheugen|3X-geheugen|3X-geheugen|
|Column Store-ondersteuning|Ja|Ja|Ja|Ja|
|OLTP-opslag in het geheugen (GB)|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Maximale gegevens grootte (TB)|100 |100 |100 |100 |
|Maximale logboek grootte (TB)|Onbeperkt |Onbeperkt |Onbeperkt |Onbeperkt |
|Maximale gegevens grootte TempDB (GB)|64|128|192|256|
|Opslagtype| [Opmerking 1](#notes) |[Opmerking 1](#notes)|[Opmerking 1](#notes) |[Opmerking 1](#notes) |
|Max. aantal lokale SSD-IOPS *|8000 |16000 |24000 |32000 |
|Maximale logboek frequentie (MBps)|100 |100 |100 |100 |
|I/o-latentie (bij benadering)|[Opmerking 2](#notes)|[Opmerking 2](#notes)|[Opmerking 2](#notes)|[Opmerking 2](#notes)|
|Maxi maal aantal gelijktijdige werk nemers (aanvragen)|160|320|480|640|
|Maximaal aantal gelijktijdige sessies|30.000|30.000|30.000|30.000|
|Secundaire replica's|0-4|0-4|0-4|0-4|
|Meerdere AZ|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Uitschalen voor leesbewerking|Ja|Ja|Ja|Ja|
|Back-upopslag bewaren|7 dagen|7 dagen|7 dagen|7 dagen|
|||

### <a name="notes"></a>Notities

**Opmerking 1**: grootschalige is een architectuur met meerdere lagen met afzonderlijke Compute-en opslag onderdelen: [grootschalige service tier-architectuur](service-tier-hyperscale.md#distributed-functions-architecture)

**Opmerking 2**: de latentie is 1-2 MS voor gegevens op de lokale SSD van de compute-replica, waarmee de meeste gebruikte gegevens pagina's in de cache worden opgeslagen. Hogere latentie voor gegevens die zijn opgehaald van pagina servers.

## <a name="general-purpose---provisioned-compute---gen4"></a>Algemeen beoogde, ingerichte Compute-Gen4

> [!IMPORTANT]
> Nieuwe Gen4-data bases worden niet meer ondersteund in de Australië-oost-of Brazilië-zuid regio's.

### <a name="gen4-compute-generation-part-1"></a>Gen4 Compute Generation (deel 1)

|Berekenings grootte (Service doelstelling)|GP_Gen4_1|GP_Gen4_2|GP_Gen4_3|GP_Gen4_4|GP_Gen4_5|GP_Gen4_6
|:--- | --: |--: |--: |--: |--: |--: |
|Compute genereren|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|vCores|1|2|3|4|5|6|
|Geheugen (GB)|7|14|21|28|35|42|
|Column Store-ondersteuning|Ja|Ja|Ja|Ja|Ja|Ja|
|OLTP-opslag in het geheugen (GB)|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Maximale gegevens grootte (GB)|1024|1024|1536|1536|1536|3072|
|Maximale logboek grootte (GB)|307|307|461|461|461|922|
|Maximale gegevens grootte TempDB (GB)|32|64|96|128|160|192|
|Opslagtype|Externe SSD|Externe SSD|Externe SSD|Externe SSD|Externe SSD|Externe SSD|
|I/o-latentie (bij benadering)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|
|Max. gegevensiops *|320|640|960|1280|1600|1920|
|Maximale logboek frequentie (MBps)|4.5|9|13.5|18|22.5|27|
|Maxi maal aantal gelijktijdige werk nemers (aanvragen)|200|400|600|800|1000|1200|
|Maximaal aantal gelijktijdige sessies|30.000|30.000|30.000|30.000|30.000|30.000|
|Aantal replica's|1|1|1|1|1|1|
|Meerdere AZ|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Uitschalen voor leesbewerking|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Opgenomen back-upopslag|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|

\* De maximum waarde voor i/o-grootten tussen 8 KB en 64 KB. Werkelijke IOPS zijn werk belasting afhankelijk. Zie [Data io governance](resource-limits-logical-server.md#resource-governance)voor meer informatie.

### <a name="gen4-compute-generation-part-2"></a>Gen4 Compute Generation (deel 2)

|Berekenings grootte (Service doelstelling)|GP_Gen4_7|GP_Gen4_8|GP_Gen4_9|GP_Gen4_10|GP_Gen4_16|GP_Gen4_24
|:--- | --: |--: |--: |--: |--: |--: |
|Compute genereren|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|vCores|7|8|9|10|16|24|
|Geheugen (GB)|49|56|63|70|112|159,5|
|Column Store-ondersteuning|Ja|Ja|Ja|Ja|Ja|Ja|
|OLTP-opslag in het geheugen (GB)|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Maximale gegevens grootte (GB)|3072|3072|3072|3072|4096|4096|
|Maximale logboek grootte (GB)|922|922|922|922|1229|1229|
|Maximale gegevens grootte TempDB (GB)|224|256|288|320|512|768|
|Opslagtype|Externe SSD|Externe SSD|Externe SSD|Externe SSD|Externe SSD|Externe SSD|
|I/o-latentie (bij benadering)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)
|Max. gegevensiops *|2240|2560|2880|3200|5120|7680|
|Maximale logboek frequentie (MBps)|31.5|36|36|36|36|36|
|Maxi maal aantal gelijktijdige werk nemers (aanvragen)|1400|1600|1800|2000|3200|4800|
|Maximaal aantal gelijktijdige sessies|30.000|30.000|30.000|30.000|30.000|30.000|
|Aantal replica's|1|1|1|1|1|1|
|Meerdere AZ|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Uitschalen voor leesbewerking|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Opgenomen back-upopslag|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|

\* De maximum waarde voor i/o-grootten tussen 8 KB en 64 KB. Werkelijke IOPS zijn werk belasting afhankelijk. Zie [Data io governance](resource-limits-logical-server.md#resource-governance)voor meer informatie.

## <a name="general-purpose---provisioned-compute---gen5"></a>Algemeen beoogde, ingerichte Compute-GEN5

### <a name="gen5-compute-generation-part-1"></a>GEN5 Compute Generation (deel 1)

|Berekenings grootte (Service doelstelling)|GP_Gen5_2|GP_Gen5_4|GP_Gen5_6|GP_Gen5_8|GP_Gen5_10|GP_Gen5_12|GP_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Compute genereren|GEN5|GEN5|GEN5|GEN5|GEN5|GEN5|GEN5|
|vCores|2|4|6|8|10|12|14|
|Geheugen (GB)|10.4|20,8|31,1|41,5|51,9|62,3|72,7|
|Column Store-ondersteuning|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|OLTP-opslag in het geheugen (GB)|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Maximale gegevens grootte (GB)|1024|1024|1536|1536|1536|3072|3072|
|Maximale logboek grootte (GB)|307|307|461|461|461|922|922|
|Maximale gegevens grootte TempDB (GB)|64|128|192|256|320|384|384|
|Opslagtype|Externe SSD|Externe SSD|Externe SSD|Externe SSD|Externe SSD|Externe SSD|Externe SSD|
|I/o-latentie (bij benadering)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|
|Max. gegevensiops *|640|1280|1920|2560|3200|3840|4480|
|Maximale logboek frequentie (MBps)|9|18|27|36|36|36|36|
|Maxi maal aantal gelijktijdige werk nemers (aanvragen)|200|400|600|800|1000|1200|1400|
|Maximaal aantal gelijktijdige sessies|30.000|30.000|30.000|30.000|30.000|30.000|30.000|
|Aantal replica's|1|1|1|1|1|1|1|
|Meerdere AZ|[Beschikbaar als preview-versie](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[Beschikbaar als preview-versie](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[Beschikbaar als preview-versie](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[Beschikbaar als preview-versie](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[Beschikbaar als preview-versie](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[Beschikbaar als preview-versie](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[Beschikbaar als preview-versie](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|
|Uitschalen voor leesbewerking|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Opgenomen back-upopslag|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|

\* De maximum waarde voor i/o-grootten tussen 8 KB en 64 KB. Werkelijke IOPS zijn werk belasting afhankelijk. Zie [Data io governance](resource-limits-logical-server.md#resource-governance)voor meer informatie.

### <a name="gen5-compute-generation-part-2"></a>GEN5 Compute Generation (deel 2)

|Berekenings grootte (Service doelstelling)|GP_Gen5_16|GP_Gen5_18|GP_Gen5_20|GP_Gen5_24|GP_Gen5_32|GP_Gen5_40|GP_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Compute genereren|GEN5|GEN5|GEN5|GEN5|GEN5|GEN5|GEN5|
|vCores|16|18|20|24|32|40|80|
|Geheugen (GB)|83|93.4|103.8|124,6|166,1|207,6|415,2|
|Column Store-ondersteuning|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|OLTP-opslag in het geheugen (GB)|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Maximale gegevens grootte (GB)|3072|3072|3072|4096|4096|4096|4096|
|Maximale logboek grootte (GB)|922|922|922|1024|1024|1024|1024|
|Maximale gegevens grootte TempDB (GB)|512|576|640|768|1024|1280|2560|
|Opslagtype|Externe SSD|Externe SSD|Externe SSD|Externe SSD|Externe SSD|Externe SSD|Externe SSD|
|I/o-latentie (bij benadering)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|
|Max. gegevensiops *|5120|5760|6400|7680|10240|12800|12800|
|Maximale logboek frequentie (MBps)|36|36|36|36|36|36|36|
|Maxi maal aantal gelijktijdige werk nemers (aanvragen)|1600|1800|2000|2400|3200|4000|8000|
|Maximaal aantal gelijktijdige sessies|30.000|30.000|30.000|30.000|30.000|30.000|30.000|
|Aantal replica's|1|1|1|1|1|1|1|
|Meerdere AZ|[Beschikbaar als preview-versie](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[Beschikbaar als preview-versie](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[Beschikbaar als preview-versie](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[Beschikbaar als preview-versie](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[Beschikbaar als preview-versie](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[Beschikbaar als preview-versie](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[Beschikbaar als preview-versie](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|
|Uitschalen voor leesbewerking|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Opgenomen back-upopslag|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|

\* De maximum waarde voor i/o-grootten tussen 8 KB en 64 KB. Werkelijke IOPS zijn werk belasting afhankelijk. Zie [Data io governance](resource-limits-logical-server.md#resource-governance)voor meer informatie.

## <a name="general-purpose---provisioned-compute---fsv2-series"></a>Algemeen beoogde, ingerichte Compute-Fsv2-serie

### <a name="fsv2-series-compute-generation-part-1"></a>Generatie van Fsv2-Series (deel 1)

|Berekenings grootte (Service doelstelling)|GP_Fsv2_8|GP_Fsv2_10|GP_Fsv2_12|GP_Fsv2_14| GP_Fsv2_16|
|:---| ---:|---:|---:|---:|---:|
|Compute genereren|Fsv2-serie|Fsv2-serie|Fsv2-serie|Fsv2-serie|Fsv2-serie|
|vCores|8|10|12|14|16|
|Geheugen (GB)|15,1|18.9|22,7|26.5|30,2|
|Column Store-ondersteuning|Ja|Ja|Ja|Ja|Ja|
|OLTP-opslag in het geheugen (GB)|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Maximale gegevens grootte (GB)|1024|1024|1024|1024|1536|
|Maximale logboek grootte (GB)|336|336|336|336|512|
|Maximale gegevens grootte TempDB (GB)|37|46|56|65|74|
|Opslagtype|Externe SSD|Externe SSD|Externe SSD|Externe SSD|Externe SSD|
|I/o-latentie (bij benadering)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|
|Max. gegevensiops *|2560|3200|3840|4480|5120|
|Maximale logboek frequentie (MBps)|36|36|36|36|36|
|Maxi maal aantal gelijktijdige werk nemers (aanvragen)|400|500|600|700|800|
|Maximaal aantal gelijktijdige aanmeldingen|800|1000|1200|1400|1600|
|Maximaal aantal gelijktijdige sessies|30.000|30.000|30.000|30.000|30.000|
|Aantal replica's|1|1|1|1|1|
|Meerdere AZ|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Uitschalen voor leesbewerking|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Opgenomen back-upopslag|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|

\* De maximum waarde voor i/o-grootten tussen 8 KB en 64 KB. Werkelijke IOPS zijn werk belasting afhankelijk. Zie [Data io governance](resource-limits-logical-server.md#resource-governance)voor meer informatie.

### <a name="fsv2-series-compute-generation-part-2"></a>Generatie van Fsv2-Series (deel 2)

|Berekenings grootte (Service doelstelling)|GP_Fsv2_18|GP_Fsv2_20|GP_Fsv2_24|GP_Fsv2_32| GP_Fsv2_36|GP_Fsv2_72|
|:---| ---:|---:|---:|---:|---:|---:|
|Compute genereren|Fsv2-serie|Fsv2-serie|Fsv2-serie|Fsv2-serie|Fsv2-serie|Fsv2-serie|
|vCores|18|20|24|32|36|72|
|Geheugen (GB)|34.0|37,8|45,4|60,5|68.0|136,0|
|Column Store-ondersteuning|Ja|Ja|Ja|Ja|Ja|Ja|
|OLTP-opslag in het geheugen (GB)|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Maximale gegevens grootte (GB)|1536|1536|1536|3072|3072|4096|
|Maximale logboek grootte (GB)|512|512|512|1024|1024|1024|
|Maximale gegevens grootte TempDB (GB)|83|93|111|148|167|333|
|Opslagtype|Externe SSD|Externe SSD|Externe SSD|Externe SSD|Externe SSD|Externe SSD|
|I/o-latentie (bij benadering)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|
|Max. gegevensiops *|5760|6400|7680|10240|11520|12800|
|Maximale logboek frequentie (MBps)|36|36|36|36|36|36|
|Maxi maal aantal gelijktijdige werk nemers (aanvragen)|900|1000|1200|1600|1800|3600|
|Maximaal aantal gelijktijdige aanmeldingen|1800|2000|2400|3200|3600|7200|
|Maximaal aantal gelijktijdige sessies|30.000|30.000|30.000|30.000|30.000|30.000|
|Aantal replica's|1|1|1|1|1|1|
|Meerdere AZ|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Uitschalen voor leesbewerking|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Opgenomen back-upopslag|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|

\* De maximum waarde voor i/o-grootten tussen 8 KB en 64 KB. Werkelijke IOPS zijn werk belasting afhankelijk. Zie [Data io governance](resource-limits-logical-server.md#resource-governance)voor meer informatie.

## <a name="general-purpose---provisioned-compute---dc-series"></a>Algemeen beoogde, ingerichte Compute-DC-serie

|Berekenings grootte (Service doelstelling)|GP_DC_2|GP_DC_4|GP_DC_6|GP_DC_8| 
|:---| ---:|---:|---:|---:|
|Compute genereren|DC-serie|DC-serie|DC-serie|DC-serie|
|vCores|2|4|6|8|
|Geheugen (GB)|9|18|27|36|
|Column Store-ondersteuning|Ja|Ja|Ja|Ja|
|OLTP-opslag in het geheugen (GB)|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Maximale gegevens grootte (GB)|1024|1536|3072|3072|
|Maximale logboek grootte (GB)|307|461|922|922|
|Maximale gegevens grootte TempDB (GB)|64|128|192|256|
|Opslagtype|Externe SSD|Externe SSD|Externe SSD|Externe SSD|
|I/o-latentie (bij benadering)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|
|Max. gegevensiops *|640|1280|1920|2560|
|Maximale logboek frequentie (MBps)|9|18|27|36|
|Maxi maal aantal gelijktijdige werk nemers (aanvragen)|160|320|480|640|
|Maximaal aantal gelijktijdige sessies|30.000|30.000|30.000|30.000|
|Aantal replica's|1|1|1|1|
|Meerdere AZ|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Uitschalen voor leesbewerking|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Opgenomen back-upopslag|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|


\* De maximum waarde voor i/o-grootten tussen 8 KB en 64 KB. Werkelijke IOPS zijn werk belasting afhankelijk. Zie [Data io governance](resource-limits-logical-server.md#resource-governance)voor meer informatie.

## <a name="business-critical---provisioned-compute---gen4"></a>Bedrijfs kritieke, ingerichte Compute-Gen4

> [!IMPORTANT]
> Nieuwe Gen4-data bases worden niet meer ondersteund in de Australië-oost-of Brazilië-zuid regio's.

### <a name="gen4-compute-generation-part-1"></a>Gen4 Compute Generation (deel 1)

|Berekenings grootte (Service doelstelling)|BC_Gen4_1|BC_Gen4_2|BC_Gen4_3|BC_Gen4_4|BC_Gen4_5|BC_Gen4_6|
|:--- | --: |--: |--: |--: |--: |--: |
|Compute genereren|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|vCores|1|2|3|4|5|6|
|Geheugen (GB)|7|14|21|28|35|42|
|Column Store-ondersteuning|Ja|Ja|Ja|Ja|Ja|Ja|
|OLTP-opslag in het geheugen (GB)|1|2|3|4|5|6|
|Opslagtype|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|
|Maximale gegevens grootte (GB)|1024|1024|1024|1024|1024|1024|
|Maximale logboek grootte (GB)|307|307|307|307|307|307|
|Maximale gegevens grootte TempDB (GB)|32|64|96|128|160|192|
|I/o-latentie (bij benadering)|1-2 ms (schrijven)<br>1-2 ms (lezen)|1-2 ms (schrijven)<br>1-2 ms (lezen)|1-2 ms (schrijven)<br>1-2 ms (lezen)|1-2 ms (schrijven)<br>1-2 ms (lezen)|1-2 ms (schrijven)<br>1-2 ms (lezen)|1-2 ms (schrijven)<br>1-2 ms (lezen)|
|Max. gegevensiops *|4000|8,000|12,000|16.000|20.000|24.000|
|Maximale logboek frequentie (MBps)|8|16|24|32|40|48|
|Maxi maal aantal gelijktijdige werk nemers (aanvragen)|200|400|600|800|1000|1200|
|Maximaal aantal gelijktijdige aanmeldingen|200|400|600|800|1000|1200|
|Maximaal aantal gelijktijdige sessies|30.000|30.000|30.000|30.000|30.000|30.000|
|Aantal replica's|4|4|4|4|4|4|
|Meerdere AZ|Ja|Ja|Ja|Ja|Ja|Ja|
|Uitschalen voor leesbewerking|Ja|Ja|Ja|Ja|Ja|Ja|
|Opgenomen back-upopslag|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|

\* De maximum waarde voor i/o-grootten tussen 8 KB en 64 KB. Werkelijke IOPS zijn werk belasting afhankelijk. Zie [Data io governance](resource-limits-logical-server.md#resource-governance)voor meer informatie.

### <a name="gen4-compute-generation-part-2"></a>Gen4 Compute Generation (deel 2)

|Berekenings grootte (Service doelstelling)|BC_Gen4_7|BC_Gen4_8|BC_Gen4_9|BC_Gen4_10|BC_Gen4_16|BC_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|Compute genereren|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|vCores|7|8|9|10|16|24|
|Geheugen (GB)|49|56|63|70|112|159,5|
|Column Store-ondersteuning|Ja|Ja|Ja|Ja|Ja|Ja|
|OLTP-opslag in het geheugen (GB)|7|8|9.5|11|20|36|
|Opslagtype|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|
|Maximale gegevens grootte (GB)|1024|1024|1024|1024|1024|1024|
|Maximale logboek grootte (GB)|307|307|307|307|307|307|
|Maximale gegevens grootte TempDB (GB)|224|256|288|320|512|768|
|I/o-latentie (bij benadering)|1-2 ms (schrijven)<br>1-2 ms (lezen)|1-2 ms (schrijven)<br>1-2 ms (lezen)|1-2 ms (schrijven)<br>1-2 ms (lezen)|1-2 ms (schrijven)<br>1-2 ms (lezen)|1-2 ms (schrijven)<br>1-2 ms (lezen)|1-2 ms (schrijven)<br>1-2 ms (lezen)|
|Maximum aantal gegevens IOPS |28.000|32.000|36.000|40,000|64.000|76.800|
|Maximale logboek frequentie (MBps)|56|64|64|64|64|64|
|Maxi maal aantal gelijktijdige werk nemers (aanvragen)|1400|1600|1800|2000|3200|4800|
|Maxi maal aantal gelijktijdige aanmeldingen (aanvragen)|1400|1600|1800|2000|3200|4800|
|Maximaal aantal gelijktijdige sessies|30.000|30.000|30.000|30.000|30.000|30.000|
|Aantal replica's|4|4|4|4|4|4|
|Meerdere AZ|Ja|Ja|Ja|Ja|Ja|Ja|
|Uitschalen voor leesbewerking|Ja|Ja|Ja|Ja|Ja|Ja|
|Opgenomen back-upopslag|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|

\* De maximum waarde voor i/o-grootten tussen 8 KB en 64 KB. Werkelijke IOPS zijn werk belasting afhankelijk. Zie [Data io governance](resource-limits-logical-server.md#resource-governance)voor meer informatie.

## <a name="business-critical---provisioned-compute---gen5"></a>Bedrijfs kritieke, ingerichte Compute-GEN5

### <a name="gen5-compute-generation-part-1"></a>GEN5 Compute Generation (deel 1)

|Berekenings grootte (Service doelstelling)|BC_Gen5_2|BC_Gen5_4|BC_Gen5_6|BC_Gen5_8|BC_Gen5_10|BC_Gen5_12|BC_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Compute genereren|GEN5|GEN5|GEN5|GEN5|GEN5|GEN5|GEN5|
|vCores|2|4|6|8|10|12|14|
|Geheugen (GB)|10.4|20,8|31,1|41,5|51,9|62,3|72,7|
|Column Store-ondersteuning|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|OLTP-opslag in het geheugen (GB)|1,57|3,14|4,71|6,28|8,65|11,02|13,39|
|Maximale gegevens grootte (GB)|1024|1024|1536|1536|1536|3072|3072|
|Maximale logboek grootte (GB)|307|307|461|461|461|922|922|
|Maximale gegevens grootte TempDB (GB)|64|128|192|256|320|384|448|
|Opslagtype|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|
|I/o-latentie (bij benadering)|1-2 ms (schrijven)<br>1-2 ms (lezen)|1-2 ms (schrijven)<br>1-2 ms (lezen)|1-2 ms (schrijven)<br>1-2 ms (lezen)|1-2 ms (schrijven)<br>1-2 ms (lezen)|1-2 ms (schrijven)<br>1-2 ms (lezen)|1-2 ms (schrijven)<br>1-2 ms (lezen)|1-2 ms (schrijven)<br>1-2 ms (lezen)|
|Max. gegevensiops *|8000|16.000|24.000|32.000|40,000|48.000|56.000|
|Maximale logboek frequentie (MBps)|24|48|72|96|96|96|96|
|Maxi maal aantal gelijktijdige werk nemers (aanvragen)|200|400|600|800|1000|1200|1400|
|Maximaal aantal gelijktijdige aanmeldingen|200|400|600|800|1000|1200|1400|
|Maximaal aantal gelijktijdige sessies|30.000|30.000|30.000|30.000|30.000|30.000|30.000|
|Aantal replica's|4|4|4|4|4|4|4|
|Meerdere AZ|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|Uitschalen voor leesbewerking|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|Opgenomen back-upopslag|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|

\* De maximum waarde voor i/o-grootten tussen 8 KB en 64 KB. Werkelijke IOPS zijn werk belasting afhankelijk. Zie [Data io governance](resource-limits-logical-server.md#resource-governance)voor meer informatie.

### <a name="gen5-compute-generation-part-2"></a>GEN5 Compute Generation (deel 2)

|Berekenings grootte (Service doelstelling)|BC_Gen5_16|BC_Gen5_18|BC_Gen5_20|BC_Gen5_24|BC_Gen5_32|BC_Gen5_40|BC_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Compute genereren|GEN5|GEN5|GEN5|GEN5|GEN5|GEN5|GEN5|
|vCores|16|18|20|24|32|40|80|
|Geheugen (GB)|83|93.4|103.8|124,6|166,1|207,6|415,2|
|Column Store-ondersteuning|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|OLTP-opslag in het geheugen (GB)|15,77|18,14|20,51|25,25|37,94|52,23|131,64|
|Maximale gegevens grootte (GB)|3072|3072|3072|4096|4096|4096|4096|
|Maximale logboek grootte (GB)|922|922|922|1024|1024|1024|1024|
|Maximale gegevens grootte TempDB (GB)|512|576|640|768|1024|1280|2560|
|Opslagtype|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|
|I/o-latentie (bij benadering)|1-2 ms (schrijven)<br>1-2 ms (lezen)|1-2 ms (schrijven)<br>1-2 ms (lezen)|1-2 ms (schrijven)<br>1-2 ms (lezen)|1-2 ms (schrijven)<br>1-2 ms (lezen)|1-2 ms (schrijven)<br>1-2 ms (lezen)|1-2 ms (schrijven)<br>1-2 ms (lezen)|1-2 ms (schrijven)<br>1-2 ms (lezen)|
|Max. gegevensiops *|64.000|72.000|80,000|96.000|128.000|160.000|204.800|
|Maximale logboek frequentie (MBps)|96|96|96|96|96|96|96|
|Maxi maal aantal gelijktijdige werk nemers (aanvragen)|1600|1800|2000|2400|3200|4000|8000|
|Maximaal aantal gelijktijdige aanmeldingen|1600|1800|2000|2400|3200|4000|8000|
|Maximaal aantal gelijktijdige sessies|30.000|30.000|30.000|30.000|30.000|30.000|30.000|
|Aantal replica's|4|4|4|4|4|4|4|
|Meerdere AZ|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|Uitschalen voor leesbewerking|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|Opgenomen back-upopslag|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|

\* De maximum waarde voor i/o-grootten tussen 8 KB en 64 KB. Werkelijke IOPS zijn werk belasting afhankelijk. Zie [Data io governance](resource-limits-logical-server.md#resource-governance)voor meer informatie.

## <a name="business-critical---provisioned-compute---m-series"></a>Bedrijfskritische-ingerichte Compute-M-serie

### <a name="m-series-compute-generation-part-1"></a>Generatie van d-Series Compute (deel 1)

|Berekenings grootte (Service doelstelling)|BC_M_8|BC_M_10|BC_M_12|BC_M_14|BC_M_16|BC_M_18|
|:---| ---:|---:|---:|---:|---:|---:|
|Compute genereren|M-serie|M-serie|M-serie|M-serie|M-serie|M-serie|
|vCores|8|10|12|14|16|18|
|Geheugen (GB)|235,4|294,3|353,2|412,0|470,9|529,7|
|Column Store-ondersteuning|Ja|Ja|Ja|Ja|Ja|Ja|
|OLTP-opslag in het geheugen (GB)|64|80|96|112|128|150|
|Maximale gegevens grootte (GB)|512|640|768|896|1024|1152|
|Maximale logboek grootte (GB)|171|213|256|299|341|384|
|Maximale gegevens grootte TempDB (GB)|256|320|384|448|512|576|
|Opslagtype|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|
|I/o-latentie (bij benadering)|1-2 ms (schrijven)<br>1-2 ms (lezen)|1-2 ms (schrijven)<br>1-2 ms (lezen)|1-2 ms (schrijven)<br>1-2 ms (lezen)|1-2 ms (schrijven)<br>1-2 ms (lezen)|1-2 ms (schrijven)<br>1-2 ms (lezen)|1-2 ms (schrijven)<br>1-2 ms (lezen)|
|Max. gegevensiops *|12.499|15.624|18.748|21.873|24.998|28.123|
|Maximale logboek frequentie (MBps)|48|60|72|84|96|108|
|Maxi maal aantal gelijktijdige werk nemers (aanvragen)|800|1000|1200|1400|1600|1800|
|Maximaal aantal gelijktijdige aanmeldingen|800|1000|1200|1400|1600|1800|
|Maximaal aantal gelijktijdige sessies|30.000|30.000|30.000|30.000|30.000|30.000|
|Aantal replica's|4|4|4|4|4|4|
|Meerdere AZ|Nee|Nee|Nee|Nee|Nee|Nee|
|Uitschalen voor leesbewerking|Ja|Ja|Ja|Ja|Ja|Ja|
|Opgenomen back-upopslag|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|

\* De maximum waarde voor i/o-grootten tussen 8 KB en 64 KB. Werkelijke IOPS zijn werk belasting afhankelijk. Zie [Data io governance](resource-limits-logical-server.md#resource-governance)voor meer informatie.

> [!IMPORTANT]
> In sommige gevallen moet u mogelijk een Data Base verkleinen om ongebruikte ruimte te claimen. Zie [Bestands ruimte beheren in Azure SQL database](file-space-manage.md)voor meer informatie.

### <a name="m-series-compute-generation-part-2"></a>Generatie van d-Series Compute (deel 2)

|Berekenings grootte (Service doelstelling)|BC_M_20|BC_M_24|BC_M_32|BC_M_64|BC_M_128|
|:---| ---:|---:|---:|---:|---:|
|Compute genereren|M-serie|M-serie|M-serie|M-serie|M-serie|
|vCores|20|24|32|64|128|
|Geheugen (GB)|588,6|706,3|941,8|1883,5|3767,0|
|Column Store-ondersteuning|Ja|Ja|Ja|Ja|Ja|
|OLTP-opslag in het geheugen (GB)|172|216|304|704|1768|
|Maximale gegevens grootte (GB)|1280|1536|2048|4096|4096|
|Maximale logboek grootte (GB)|427|512|683|1024|1024|
|Maximale gegevens grootte TempDB (GB)|4096|2048|1024|768|640|
|Opslagtype|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|
|I/o-latentie (bij benadering)|1-2 ms (schrijven)<br>1-2 ms (lezen)|1-2 ms (schrijven)<br>1-2 ms (lezen)|1-2 ms (schrijven)<br>1-2 ms (lezen)|1-2 ms (schrijven)<br>1-2 ms (lezen)|1-2 ms (schrijven)<br>1-2 ms (lezen)|
|Max. gegevensiops *|31.248|37.497|49.996|99.993|160.000|
|Maximale logboek frequentie (MBps)|120|144|192|264|264|
|Maxi maal aantal gelijktijdige werk nemers (aanvragen)|2.000|2.400|3.200|6.400|12.800|
|Maximaal aantal gelijktijdige aanmeldingen|2.000|2.400|3.200|6.400|12.800|
|Maximaal aantal gelijktijdige sessies|30.000|30.000|30.000|30.000|30.000|
|Aantal replica's|4|4|4|4|4|
|Meerdere AZ|Nee|Nee|Nee|Nee|Nee|
|Uitschalen voor leesbewerking|Ja|Ja|Ja|Ja|Ja|
|Opgenomen back-upopslag|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|

\* De maximum waarde voor i/o-grootten tussen 8 KB en 64 KB. Werkelijke IOPS zijn werk belasting afhankelijk. Zie [Data io governance](resource-limits-logical-server.md#resource-governance)voor meer informatie.

> [!IMPORTANT]
> In sommige gevallen moet u mogelijk een Data Base verkleinen om ongebruikte ruimte te claimen. Zie [Bestands ruimte beheren in Azure SQL database](file-space-manage.md)voor meer informatie.

## <a name="business-critical---provisioned-compute---dc-series"></a>Bedrijfs kritieke-ingerichte Compute-DC-serie

|Berekenings grootte (Service doelstelling)|BC_DC_2|BC_DC_4|BC_DC_6|BC_DC_8|
|:--- | --: |--: |--: |--: |
|Compute genereren|DC-serie|DC-serie|DC-serie|DC-serie|
|vCores|2|4|6|8|
|Geheugen (GB)|9|18|27|36|
|Column Store-ondersteuning|Ja|Ja|Ja|Ja|
|OLTP-opslag in het geheugen (GB)|1,7|3.7|5.9|8,2|
|Maximale gegevens grootte (GB)|768|768|768|768|
|Maximale logboek grootte (GB)|230|230|230|230|
|Maximale gegevens grootte TempDB (GB)|64|128|192|256|
|Opslagtype|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|
|I/o-latentie (bij benadering)|1-2 ms (schrijven)<br>1-2 ms (lezen)|1-2 ms (schrijven)<br>1-2 ms (lezen)|1-2 ms (schrijven)<br>1-2 ms (lezen)|1-2 ms (schrijven)<br>1-2 ms (lezen)|
|Max. gegevensiops *|14000|28000|42000|56000|
|Maximale logboek frequentie (MBps)|24|48|72|96|
|Maxi maal aantal gelijktijdige werk nemers (aanvragen)|200|400|600|800|
|Maximaal aantal gelijktijdige aanmeldingen|200|400|600|800|
|Maximaal aantal gelijktijdige sessies|30.000|30.000|30.000|30.000|
|Aantal replica's|4|4|4|4|
|Meerdere AZ|Nee|Nee|Nee|Nee|
|Uitschalen voor leesbewerking|Nee|Nee|Nee|Nee|
|Opgenomen back-upopslag|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|

\* De maximum waarde voor i/o-grootten tussen 8 KB en 64 KB. Werkelijke IOPS zijn werk belasting afhankelijk. Zie [Data io governance](resource-limits-logical-server.md#resource-governance)voor meer informatie.


## <a name="next-steps"></a>Volgende stappen

- Zie [resource limieten voor afzonderlijke data bases met behulp van het DTU-aankoop model](resource-limits-dtu-single-databases.md) voor DTU-resource limieten voor één data base
- Zie [resource limieten voor elastische Pools met behulp van het vCore-aankoop model](resource-limits-vcore-elastic-pools.md) voor vCore resource limieten voor elastische Pools
- Voor DTU-resource limieten voor elastische Pools raadpleegt [u resource limieten voor elastische Pools met behulp van het DTU-aankoop model](resource-limits-dtu-elastic-pools.md)
- Zie [SQL Managed instance resource limits](../managed-instance/resource-limits.md)voor resource limieten voor SQL Managed instance.
- Zie [Azure-abonnement en service limieten, quota's en beperkingen](../../azure-resource-manager/management/azure-subscription-service-limits.md)voor meer informatie over algemene Azure-limieten.
- Zie [overzicht van resource limieten op een server](resource-limits-logical-server.md) voor informatie over limieten op het niveau van de server en het abonnement voor meer informatie over de limieten van resources op een server.
