---
title: DTU-resource beperkt elastische Pools
description: Op deze pagina worden enkele algemene DTU-resource limieten voor elastische Pools in Azure SQL Database beschreven.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: seo-lt-2019 sqldbrb=1 references_regions
ms.devlang: ''
ms.topic: reference
author: sachinpMSFT
ms.author: sachinp
ms.reviewer: sstein
ms.date: 04/09/2021
ms.openlocfilehash: 906d34a91736d28f0da4a5ba34b7114fbe410adb
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107306633"
---
# <a name="resources-limits-for-elastic-pools-using-the-dtu-purchasing-model"></a>Bronnen limieten voor elastische Pools met behulp van het DTU-aankoop model
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

In dit artikel vindt u gedetailleerde resource limieten voor data bases in Azure SQL Database die zich binnen een elastische pool bevinden met behulp van het DTU-aankoop model.

* Zie [overzicht van resource limieten op een server](resource-limits-logical-server.md)voor de limieten voor DTU-aankoop modellen voor afzonderlijke data bases op een server.
* Zie voor de resource limieten van het type DTU-aankoop model voor Azure SQL Database de limieten voor [DTU-bronnen enkele data bases](resource-limits-dtu-single-databases.md) en [DTU-bronnen beperken elastische Pools](resource-limits-dtu-elastic-pools.md).
* Zie [vCore resource limieten-Azure SQL database](resource-limits-vcore-single-databases.md) en vCore resource limieten [-elastische Pools](resource-limits-vcore-elastic-pools.md)voor vCore-resource limieten.
* Zie [Inkoop modellen en service lagen](purchasing-models.md)voor meer informatie over de verschillende aankoop modellen.

Elke alleen-lezen replica heeft zijn eigen resources, zoals Dtu's, werk rollen en sessies. Elke alleen-lezen replica is onderhevig aan de resource limieten die verderop in dit artikel worden beschreven.

## <a name="elastic-pool-storage-sizes-and-compute-sizes"></a>Elastische pool: opslag grootten en reken grootten

Voor Azure SQL Database elastische Pools tonen de volgende tabellen de resources die beschikbaar zijn in elke servicelaag en de berekenings grootte. U kunt de servicelaag, de reken grootte en de opslag hoeveelheid instellen met behulp van:

* [Transact-SQL](elastic-pool-scale.md) via [ALTER data base](/sql/t-sql/statements/alter-database-transact-sql#overview-sql-database)
* [Azure-portal](elastic-pool-manage.md#azure-portal)
* [PowerShell](elastic-pool-manage.md#powershell)
* [Azure-CLI](elastic-pool-manage.md#azure-cli)
* [REST API](elastic-pool-manage.md#rest-api)


> [!IMPORTANT]
> Zie [een elastische pool schalen](elastic-pool-scale.md) voor schaal baarheid en overwegingen

De resource limieten van afzonderlijke data bases in elastische Pools zijn in het algemeen hetzelfde als voor afzonderlijke data bases buiten Pools op basis van Dtu's en de servicelaag. Zo is het maximum aantal gelijktijdige werk rollen voor een S2-data base 120 werk nemers. Het maximum aantal gelijktijdige werk rollen voor een data base in een Standard-pool is dus ook 120 werk nemers als het maximale DTU per data base in de pool 50 Dtu's is (wat gelijk is aan S2).
 
Voor hetzelfde aantal Dtu's kunnen resources die aan een elastische pool worden gegeven de bronnen die aan één Data Base zijn gegeven, buiten een elastische pool overschrijden. Dit betekent dat het eDTU-gebruik van een elastische pool kleiner moet zijn dan de som van het DTU-gebruik voor alle data bases in de pool, afhankelijk van de werkbelasting patronen. In een extreem geval met slechts één data base in een elastische pool waarvoor het gebruik van de data base-DTU 100% is, is het mogelijk dat het gebruik van de pool-eDTU 50% is voor bepaalde werkbelasting patronen. Dit kan gebeuren zelfs als Max DTU per data base de Maxi maal ondersteunde waarde voor de opgegeven pool grootte blijft.

> [!NOTE]
> De resource limiet voor opslag per groep in elk van de volgende tabellen bevat geen tempdb-en logboek opslag.

### <a name="basic-elastic-pool-limits"></a>Basisbeperkingen voor elastische groepen

| eDTU's per groep | **50** | **100** | **200** | **300** | **400** | **800** | **1200** | **1600** |
|:---|---:|---:|---:| ---: | ---: | ---: | ---: | ---: |
| Inbegrepen opslag per pool (GB) | 5 | 10 | 20 | 29 | 39 | 78 | 117 | 156 |
| Maximale opslag per groep (GB) | 5 | 10 | 20 | 29 | 39 | 78 | 117 | 156 |
| Maxi maal In-Memory OLTP-opslag per pool (GB) | N.v.t. | N.v.t. | N.v.t. | N.v.t. | N.v.t. | N.v.t. | N.v.t. | N.v.t. |
| Maximum aantal Db's per pool <sup>1</sup> | 100 | 200 | 500 | 500 | 500 | 500 | 500 | 500 |
| Maxi maal aantal gelijktijdige werk nemers (aanvragen) per pool <sup>2</sup> | 100 | 200 | 400 | 600 | 800 | 1600 | 2400 | 3200 |
| Maximum aantal gelijktijdige sessies per pool <sup>2</sup> | 30.000 | 30.000 | 30.000 | 30.000 |30.000 | 30.000 | 30.000 | 30.000 |
| Min-DTU per database keuze | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 |
| Max. DTU per database opties | 5 | 5 | 5 | 5 | 5 | 5 | 5 | 5 |
| Maximale opslag per database (GB) | 2 | 2 | 2 | 2 | 2 | 2 | 2 | 2 |
||||||||

<sup>1</sup> Zie [resource beheer in compacte elastische Pools](elastic-pool-resource-management.md) voor aanvullende overwegingen.

<sup>2</sup> Zie [enkele database resource limieten](resource-limits-vcore-single-databases.md)voor het maximum aantal gelijktijdige werk rollen (aanvragen) voor elke afzonderlijke data base. Als de elastische pool bijvoorbeeld gebruikmaakt van GEN5 en het maximum aantal vCore per data base is ingesteld op 2, is de waarde voor maximum aantal gelijktijdige werk nemers 200.  Als het maximum aantal vCore per data base is ingesteld op 0,5, is de waarde voor het maximum aantal gelijktijdige werkers 50 sinds op GEN5 een maximum van 100 gelijktijdige werk nemers per vCore. Voor andere maximale vCore-instellingen per data base die minder dan 1 vCore of minder zijn, is het aantal gelijktijdige werk nemers op dezelfde manier opnieuw geschaald.

### <a name="standard-elastic-pool-limits"></a>Standaardbeperkingen voor elastische pools

| eDTU's per groep | **50** | **100** | **200** | **300** | **400** | **800**|
|:---|---:|---:|---:| ---: | ---: | ---: |
| Inbegrepen opslag per pool (GB) <sup>1</sup> | 50 | 100 | 200 | 300 | 400 | 800 |
| Maximale opslag per groep (GB) | 500 | 750 | 1024 | 1280 | 1536 | 2048 |
| Maxi maal In-Memory OLTP-opslag per pool (GB) | N.v.t. | N.v.t. | N.v.t. | N.v.t. | N.v.t. | N.v.t. |
| Maximum aantal Db's per pool <sup>2</sup> | 100 | 200 | 500 | 500 | 500 | 500 |
| Maxi maal aantal gelijktijdige werk nemers (aanvragen) per pool <sup>3</sup> | 100 | 200 | 400 | 600 | 800 | 1600 |
| Maximum aantal gelijktijdige sessies per pool <sup>3</sup> | 30.000 | 30.000 | 30.000 | 30.000 | 30.000 | 30.000 |
| Min-DTU per database keuze | 0, 10, 20, 50 | 0, 10, 20, 50, 100 | 0, 10, 20, 50, 100, 200 | 0, 10, 20, 50, 100, 200, 300 | 0, 10, 20, 50, 100, 200, 300, 400 | 0, 10, 20, 50, 100, 200, 300, 400, 800 |
| Max. DTU per database opties | 10, 20, 50 | 10, 20, 50, 100 | 10, 20, 50, 100, 200 | 10, 20, 50, 100, 200, 300 | 10, 20, 50, 100, 200, 300, 400 | 10, 20, 50, 100, 200, 300, 400, 800 |
| Maximale opslag per database (GB) | 1024 | 1024 | 1024 | 1024 | 1024 | 1024 |
||||||||

<sup>1</sup> Zie [SQL database prijs opties](https://azure.microsoft.com/pricing/details/sql-database/elastic/) voor meer informatie over de extra kosten die zijn ontstaan door extra opslag ruimte.

<sup>2</sup> Zie [resource beheer in compacte elastische Pools](elastic-pool-resource-management.md) voor aanvullende overwegingen.

<sup>3</sup> Zie [enkele database resource limieten](resource-limits-vcore-single-databases.md)voor het maximum aantal gelijktijdige werk rollen (aanvragen) voor elke afzonderlijke data base. Als de elastische pool bijvoorbeeld gebruikmaakt van GEN5 en het maximum aantal vCore per data base is ingesteld op 2, is de waarde voor maximum aantal gelijktijdige werk nemers 200.  Als het maximum aantal vCore per data base is ingesteld op 0,5, is de waarde voor het maximum aantal gelijktijdige werkers 50 sinds op GEN5 een maximum van 100 gelijktijdige werk nemers per vCore. Voor andere maximale vCore-instellingen per data base die minder dan 1 vCore of minder zijn, is het aantal gelijktijdige werk nemers op dezelfde manier opnieuw geschaald.

### <a name="standard-elastic-pool-limits-continued"></a>Limieten voor Standard elastische pools (vervolg)

| eDTU's per groep | **1200** | **1600** | **2000** | **2500** | **3000** |
|:---|---:|---:|---:| ---: | ---: |
| Inbegrepen opslag per pool (GB) <sup>1</sup> | 1200 | 1600 | 2000 | 2500 | 3000 |
| Maximale opslag per groep (GB) | 2560 | 3072 | 3584 | 4096 | 4096 |
| Maxi maal In-Memory OLTP-opslag per pool (GB) | N.v.t. | N.v.t. | N.v.t. | N.v.t. | N.v.t. |
| Maximum aantal Db's per pool <sup>2</sup> | 500 | 500 | 500 | 500 | 500 |
| Maxi maal aantal gelijktijdige werk nemers (aanvragen) per pool <sup>3</sup> | 2400 | 3200 | 4000 | 5000 | 6000 |
| Maximum aantal gelijktijdige sessies per pool <sup>3</sup> | 30.000 | 30.000 | 30.000 | 30.000 | 30.000 |
| Min-DTU per database keuze | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500, 3000 |
| Max. DTU per database opties | 10, 20, 50, 100, 200, 300, 400, 800, 1200 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500, 3000 |
| Maximale opslag per database (GB) | 1024 | 1536 | 1792 | 2304 | 2816 |
|||||||

<sup>1</sup> Zie [SQL database prijs opties](https://azure.microsoft.com/pricing/details/sql-database/elastic/) voor meer informatie over de extra kosten die zijn ontstaan door extra opslag ruimte.

<sup>2</sup> Zie [resource beheer in compacte elastische Pools](elastic-pool-resource-management.md) voor aanvullende overwegingen.

<sup>3</sup> Zie [enkele database resource limieten](resource-limits-vcore-single-databases.md)voor het maximum aantal gelijktijdige werk rollen (aanvragen) voor elke afzonderlijke data base. Als de elastische pool bijvoorbeeld gebruikmaakt van GEN5 en het maximum aantal vCore per data base is ingesteld op 2, is de waarde voor maximum aantal gelijktijdige werk nemers 200.  Als het maximum aantal vCore per data base is ingesteld op 0,5, is de waarde voor het maximum aantal gelijktijdige werkers 50 sinds op GEN5 een maximum van 100 gelijktijdige werk nemers per vCore. Voor andere maximale vCore-instellingen per data base die minder dan 1 vCore of minder zijn, is het aantal gelijktijdige werk nemers op dezelfde manier opnieuw geschaald.

### <a name="premium-elastic-pool-limits"></a>Limieten voor Premium elastische pools

| eDTU's per groep | **125** | **250** | **500** | **1000** | **1500**|
|:---|---:|---:|---:| ---: | ---: |
| Inbegrepen opslag per pool (GB) <sup>1</sup> | 250 | 500 | 750 | 1024 | 1536 |
| Maximale opslag per groep (GB) | 1024 | 1024 | 1024 | 1024 | 1536 |
| Maxi maal In-Memory OLTP-opslag per pool (GB) | 1 | 2 | 4 | 10 | 12 |
| Maximum aantal Db's per pool <sup>2</sup> | 50 | 100 | 100 | 100 | 100 |
| Maxi maal aantal gelijktijdige werk nemers per pool (aanvragen) <sup>3</sup> | 200 | 400 | 800 | 1600 | 2400 |
| Maximum aantal gelijktijdige sessies per pool <sup>3</sup> | 30.000 | 30.000 | 30.000 | 30.000 | 30.000 |
| Minimaal aantal eDTU’s per database | 0, 25, 50, 75, 125 | 0, 25, 50, 75, 125, 250 | 0, 25, 50, 75, 125, 250, 500 | 0, 25, 50, 75, 125, 250, 500, 1000 | 0, 25, 50, 75, 125, 250, 500, 1000|
| Maximaal aantal eDTU’s per database | 25, 50, 75, 125 | 25, 50, 75, 125, 250 | 25, 50, 75, 125, 250, 500 | 25, 50, 75, 125, 250, 500, 1000 | 25, 50, 75, 125, 250, 500, 1000|
| Maximale opslag per database (GB) | 1024 | 1024 | 1024 | 1024 | 1536 |
|||||||

<sup>1</sup> Zie [SQL database prijs opties](https://azure.microsoft.com/pricing/details/sql-database/elastic/) voor meer informatie over de extra kosten die zijn ontstaan door extra opslag ruimte.

<sup>2</sup> Zie [resource beheer in compacte elastische Pools](elastic-pool-resource-management.md) voor aanvullende overwegingen.

<sup>3</sup> Zie [enkele database resource limieten](resource-limits-vcore-single-databases.md)voor het maximum aantal gelijktijdige werk rollen (aanvragen) voor elke afzonderlijke data base. Als de elastische pool bijvoorbeeld gebruikmaakt van GEN5 en het maximum aantal vCore per data base is ingesteld op 2, is de waarde voor maximum aantal gelijktijdige werk nemers 200.  Als het maximum aantal vCore per data base is ingesteld op 0,5, is de waarde voor het maximum aantal gelijktijdige werkers 50 sinds op GEN5 een maximum van 100 gelijktijdige werk nemers per vCore. Voor andere maximale vCore-instellingen per data base die minder dan 1 vCore of minder zijn, is het aantal gelijktijdige werk nemers op dezelfde manier opnieuw geschaald.

### <a name="premium-elastic-pool-limits-continued"></a>Limieten voor Premium elastische pools (vervolg)

| eDTU's per groep | **2000** | **2500** | **3000** | **3500** | **4000**|
|:---|---:|---:|---:| ---: | ---: |
| Inbegrepen opslag per pool (GB) <sup>1</sup> | 2048 | 2560 | 3072 | 3548 | 4096 |
| Maximale opslag per groep (GB) | 2048 | 2560 | 3072 | 3548 | 4096|
| Maxi maal In-Memory OLTP-opslag per pool (GB) | 16 | 20 | 24 | 28 | 32 |
| Maximum aantal Db's per pool <sup>2</sup> | 100 | 100 | 100 | 100 | 100 |
| Maxi maal aantal gelijktijdige werk nemers (aanvragen) per pool <sup>3</sup> | 3200 | 4000 | 4800 | 5600 | 6400 |
| Maximum aantal gelijktijdige sessies per pool <sup>3</sup> | 30.000 | 30.000 | 30.000 | 30.000 | 30.000 |
| Min-DTU per database keuze | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750, 4000 |
| Max. DTU per database opties | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750, 4000 |
| Maximale opslag per database (GB) | 2048 | 2560 | 3072 | 3584 | 4096 |
|||||||

<sup>1</sup> Zie [SQL database prijs opties](https://azure.microsoft.com/pricing/details/sql-database/elastic/) voor meer informatie over de extra kosten die zijn ontstaan door extra opslag ruimte.

<sup>2</sup> Zie [resource beheer in compacte elastische Pools](elastic-pool-resource-management.md) voor aanvullende overwegingen.

<sup>3</sup> Zie [enkele database resource limieten](resource-limits-vcore-single-databases.md)voor het maximum aantal gelijktijdige werk rollen (aanvragen) voor elke afzonderlijke data base. Als de elastische pool bijvoorbeeld gebruikmaakt van GEN5 en het maximum aantal vCore per data base is ingesteld op 2, is de waarde voor maximum aantal gelijktijdige werk nemers 200.  Als het maximum aantal vCore per data base is ingesteld op 0,5, is de waarde voor het maximum aantal gelijktijdige werkers 50 sinds op GEN5 een maximum van 100 gelijktijdige werk nemers per vCore. Voor andere maximale vCore-instellingen per data base die minder dan 1 vCore of minder zijn, is het aantal gelijktijdige werk nemers op dezelfde manier opnieuw geschaald.

> [!IMPORTANT]
> Meer dan 1 TB aan opslag ruimte in de Premium-laag is momenteel beschikbaar in alle regio's behalve: China-oost, China-noord, Duitsland-centraal en Duitsland-noordoost. In deze regio’s is de maximale opslagruimte in de Premium-laag beperkt tot 1 TB.  Raadpleeg [P11-P15 huidige beperkingen](single-database-scale.md#p11-and-p15-constraints-when-max-size-greater-than-1-tb) voor meer informatie.

Als alle DTU's van een elastische groep zijn gebruikt, ontvangt elke database in de groep een gelijke hoeveelheid resources om query's te verwerken. De service SQL Database verdeelt resources eerlijk over databases door gelijke hoeveelheden rekentijd te garanderen. Gelijke verdeling van resources in een elastische groep vindt plaats naast een bepaalde hoeveelheid resources die sowieso voor elke database wordt gegarandeerd wanneer het aantal DTU's per database is ingesteld op een andere waarde dan nul.

> [!NOTE]
> `tempdb`Zie voor limieten [tempdb-limieten](/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database).

### <a name="database-properties-for-pooled-databases"></a>Data base-eigenschappen voor gegroepeerde Data bases

De volgende tabel beschrijft de eigenschappen voor gepoolde data bases.

| Eigenschap | Beschrijving |
|:--- |:--- |
| Maximaal aantal eDTU’s per database |Het maximum aantal eDTU's dat elke database in de groep mag gebruiken, is afhankelijk van het gebruik door andere databases in de groep. Het maximum aantal eDTU's per database is geen resourcegarantie voor een database. Het is een algemene instelling voor alle databases in de groep. Stel het maximum aantal eDTU's per database hoog genoeg zijn om pieken in databasegebruik te kunnen afhandelen. Enige mate van overtoewijzing is normaal, omdat de groep in het algemeen uitgaat van pieken en dalen in gebruikspatronen voor databases, waarbij alle databases niet tegelijkertijd pieken. Stel, het piekgebruik per database is twintig eDTU's en slechts 20% van de 100 databases in de groep pieken op hetzelfde moment. Als het maximum aantal eDTU's per database is ingesteld op twintig eDTU's, kan de groep redelijkerwijs vijf keer worden overtoegewezen en kan het aantal eDTU's per groep worden ingesteld op 400. |
| Minimaal aantal eDTU’s per database |Het minimum aantal eDTU's dat voor elke database in de groep wordt gegarandeerd. Het is een algemene instelling voor alle databases in de groep. Het minimum aantal eDTU’s per database kan worden ingesteld op 0. Dat is ook de standaardwaarde. Deze eigenschap wordt ingesteld op een waarde tussen 0 en het gemiddelde eDTU-gebruik per database. Het product van het aantal databases in de groep en het minimum aantal eDTU's per database mag niet groter zijn dan het aantal eDTU's per groep. Als een groep bijvoorbeeld twintig databases heeft en het minimum aantal eDTU's per database is ingesteld op tien eDTU's, dan moet het aantal eDTU's per groep minimaal 200 eDTU's zijn. |
| Maximale opslag per data base |De maximale database grootte die is ingesteld door de gebruiker voor een data base in een pool. Gegroepeerde Data bases delen echter toegewezen pool opslag. Zelfs als de totale maximum opslag *per data base* is ingesteld op groter dan de totale beschik bare opslag *ruimte van de groep*, kan de totale ruimte die daad werkelijk door alle data bases wordt gebruikt niet groter zijn dan de beschik bare groeps limiet. De maximale database grootte verwijst naar de maximale grootte van de gegevens bestanden en bevat niet de ruimte die wordt gebruikt door de logboek bestanden. |
|||

## <a name="next-steps"></a>Volgende stappen

* Zie [resource limieten voor afzonderlijke data bases met behulp van het vCore-aankoop model](resource-limits-vcore-single-databases.md) voor vCore resource limieten voor één data base
* Zie [resource limieten voor afzonderlijke data bases met behulp van het DTU-aankoop model](resource-limits-dtu-single-databases.md) voor DTU-resource limieten voor één data base
* Zie [resource limieten voor elastische Pools met behulp van het vCore-aankoop model](resource-limits-vcore-elastic-pools.md) voor vCore resource limieten voor elastische Pools
* Zie [SQL Managed instance resource limits](../managed-instance/resource-limits.md)voor resource limieten voor beheerde exemplaren in een Azure SQL Managed instance.
* Zie [Azure-abonnement en service limieten, quota's en beperkingen](../../azure-resource-manager/management/azure-subscription-service-limits.md)voor meer informatie over algemene Azure-limieten.
* Zie [overzicht van resource limieten op een logische SQL-Server](resource-limits-logical-server.md) voor informatie over limieten op het niveau van de server en het abonnement voor meer informatie over de limieten voor bronnen op een logische SQL-Server.