---
title: Woorden lijst voor Elastic Database-hulpprogram ma's
description: Uitleg van de termen die worden gebruikt voor Elastic data base-hulpprogram ma's
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/04/2018
ms.openlocfilehash: 166e365a4ead8ad6d0f7e543c081161ebff5f027
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92330458"
---
# <a name="elastic-database-tools-glossary"></a>Woorden lijst voor Elastic Database-hulpprogram ma's
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

De volgende termen worden gedefinieerd voor de [Elastic database-hulpprogram ma's](elastic-scale-introduction.md). De hulpprogram ma's worden gebruikt voor het beheren van [Shard Maps](elastic-scale-shard-map-management.md), en het toevoegen van de [client bibliotheek](elastic-database-client-library.md), het [hulp programma voor splitsen en samen voegen](elastic-scale-overview-split-and-merge.md), [elastische Pools](elastic-pool-overview.md)en [query's](elastic-query-overview.md). 

Deze voor waarden worden gebruikt in het [toevoegen van een Shard met Elastic database-hulpprogram ma's](elastic-scale-add-a-shard.md) en [het gebruik van de klasse toewijzingen oplossen om problemen met de Shard-kaart op te lossen](elastic-database-recovery-manager.md)

![Termen voor Elastic Scale][1]

**Data Base**: een data base in Azure SQL database. 

**Gegevens afhankelijke route ring**: de functionaliteit waarmee een toepassing verbinding kan maken met een Shard op basis van een specifieke sharding-sleutel. Zie [gegevens afhankelijke route ring](elastic-scale-data-dependent-routing.md). Vergelijk met een **[multi-Shard-query](elastic-scale-multishard-querying.md)**.

**Overzicht van globale Shard**: de kaart tussen sharding sleutels en hun respectieve Shards binnen een **Shard-set**. De globale Shard-toewijzing wordt opgeslagen in **Shard-toewijzings beheer**. Vergelijken met de **lokale Shard-toewijzing**.

**Lijst met Shard-toewijzing**: een Shard-toewijzing waarbij sharding sleutels afzonderlijk worden toegewezen. Vergelijkt met de **Shard-kaart**.   

**Lokale Shard-toewijzing**: opgeslagen op een Shard, de lokale Shard-toewijzing bevat toewijzingen voor de shardlets die zich op de Shard bevinden.

**Multi-Shard query**: de mogelijkheid om een query uit te geven op meerdere Shards; resultaten sets worden geretourneerd met behulp van UNION alle semantiek (ook wel ' ventilator-out-query ' genoemd). Vergelijkt met **gegevens afhankelijke route ring**.

**Multi tenant** en **single-tenant**: hier ziet u een Data Base met één Tenant en een multi tenant-Data Base:

![Scherm opname van een Data Base met één Tenant en een Data Base met meerdere tenants.](./media/elastic-scale-glossary/multi-single-simple.png)

Hier volgt een representatie van **Shard** single-en multi tenant-data bases. 

![Single-en multi tenant-data bases](./media/elastic-scale-glossary/shards-single-multi.png)

**Toewijzing van bereik Shard**: een Shard-toewijzing waarbij de Shard-distributie strategie is gebaseerd op meerdere bereiken aaneengesloten waarden. 

**Naslag tabellen**: tabellen die niet worden Shard, maar die worden gerepliceerd tussen Shards. Post codes kunnen bijvoorbeeld worden opgeslagen in een verwijzings tabel. 

**Shard**: een data base in Azure SQL database die gegevens uit een Shard gegevensset opslaat. 

**Shard elasticiteit**: de mogelijkheid om **horizon taal schalen** en **verticaal schalen** uit te voeren.

**Shard-tabellen**: tabellen die Shard zijn, dat wil zeggen, waarvan de gegevens worden verdeeld over Shards op basis van hun sharding sleutel waarden. 

**Sharding-sleutel**: een kolom waarde die bepaalt hoe gegevens worden gedistribueerd over Shards. Het waardetype kan een van de volgende waarden hebben: **int**, **bigint**, **varbinary** of **uniqueidentifier**. 

**Shard ingesteld**: de verzameling van Shards die worden toegeschreven aan dezelfde Shard-toewijzing in de Shard-kaart Manager.  

**Shardlet**: alle gegevens die zijn gekoppeld aan één waarde van een sharding-sleutel op een Shard. Een shardlet is de kleinste eenheid van gegevens verplaatsing tijdens het opnieuw distribueren van Shard-tabellen. 

**Shard-toewijzing**: de set toewijzingen tussen sharding-sleutels en hun respectieve Shards.

**Shard-toewijzings beheer**: een beheer object en gegevens opslag dat de Shard-kaart (en), Shard locaties en toewijzingen bevat voor een of meer Shard sets.

![Diagram toont een Shard-toewijzings beheer dat is gekoppeld aan shardmaps_global, shards_global en shard_mappings_global.][2]

## <a name="verbs"></a>Termen
**Horizon taal schalen**: de handeling van het uitschalen (of in) een verzameling Shards door Shards toe te voegen aan of te verwijderen uit een Shard-kaart, zoals hieronder wordt weer gegeven.

![Horizon taal en verticaal schalen][3]

**Samen voegen**: de handeling van het verplaatsen van shardlets van twee Shards naar een Shard en het bijwerken van de Shard-kaart dienovereenkomstig.

**Shardlet verplaatsen**: de handeling waarbij één Shardlet naar een andere Shard wordt verplaatst. 

**Shard**: de handel van het Horizon taal partitioneren van identieke gestructureerde gegevens over meerdere data bases op basis van een sharding-sleutel.

**Splitsen**: de handeling van het verplaatsen van verschillende shardlets van een Shard naar een andere (doorgaans nieuwe) Shard. Een sharding-sleutel wordt door de gebruiker als het Splits punt verschaft.

**Verticaal schalen**: de kracht van het schalen van de reken grootte van een afzonderlijke Shard. U kunt bijvoorbeeld een Shard wijzigen van Standard in Premium (wat resulteert in meer computer bronnen). 

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/elastic-scale-glossary/glossary.png
[2]: ./media/elastic-scale-glossary/mappings.png
[3]: ./media/elastic-scale-glossary/h_versus_vert.png

