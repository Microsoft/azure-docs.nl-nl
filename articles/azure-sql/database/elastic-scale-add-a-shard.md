---
title: Een Shard toevoegen met Elastic data base-hulpprogram ma's
description: Hoe u Elastic Scale-Api's kunt gebruiken om nieuwe Shards toe te voegen aan een Shard-set.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/03/2019
ms.openlocfilehash: efab0234d428a8283845946289cdd1e8a17ded26
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92792052"
---
# <a name="adding-a-shard-using-elastic-database-tools"></a>Een Shard toevoegen met behulp van Elastic Database-hulpprogram ma's
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

## <a name="to-add-a-shard-for-a-new-range-or-key"></a>Een Shard toevoegen voor een nieuw bereik of nieuwe sleutel

Toepassingen moeten vaak nieuwe Shards toevoegen voor het afhandelen van gegevens die worden verwacht van nieuwe sleutels of belang rijke bereiken, voor een Shard-kaart die al bestaat. Het is bijvoorbeeld mogelijk dat een toepassings Shard op basis van de Tenant-ID een nieuwe Shard moet inrichten voor een nieuwe Tenant, of dat de gegevens Shard maandelijks een nieuwe Shard moeten inrichten voor het begin van elke nieuwe maand.

Als het nieuwe bereik met sleutel waarden niet al deel uitmaakt van een bestaande toewijzing, is het eenvoudig om het nieuwe Shard toe te voegen en de nieuwe sleutel of het bereik aan die Shard te koppelen.

### <a name="example--adding-a-shard-and-its-range-to-an-existing-shard-map"></a>Voor beeld: een Shard en het bijbehorende bereik toevoegen aan een bestaande Shard-toewijzing

In dit voor beeld wordt gebruikgemaakt van de TryGetShard ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.shardmap.trygetshard), [.net](/previous-versions/azure/dn823929(v=azure.100))), de[CreateShard (Java](/java/api/com.microsoft.azure.elasticdb.shard.map.shardmap.createshard), [.net](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.createshard)), CreateRangeMapping ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.createrangemapping), [.net](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1) -methoden en een exemplaar van de ShardLocation-klasse ([Java](/java/api/com.microsoft.azure.elasticdb.shard.base.shardlocation) [, .net](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardlocation)). In het onderstaande voor beeld is een Data Base met de naam **sample_shard_2** en alle benodigde schema objecten erin gemaakt voor het bewaren van het bereik [300, 400).  

```csharp
// sm is a RangeShardMap object.
// Add a new shard to hold the range being added.
Shard shard2 = null;

if (!sm.TryGetShard(new ShardLocation(shardServer, "sample_shard_2"),out shard2))
{
    shard2 = sm.CreateShard(new ShardLocation(shardServer, "sample_shard_2"));  
}

// Create the mapping and associate it with the new shard
sm.CreateRangeMapping(new RangeMappingCreationInfo<long>
                            (new Range<long>(300, 400), shard2, MappingStatus.Online));
```

Voor de .NET-versie kunt u ook Power shell gebruiken als alternatief voor het maken van een nieuw Shard-toewijzings beheer. [Hier](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db)vindt u een voor beeld.

## <a name="to-add-a-shard-for-an-empty-part-of-an-existing-range"></a>Een Shard toevoegen voor een leeg deel van een bestaand bereik

In sommige gevallen is het mogelijk dat u al een bereik hebt toegewezen aan een Shard en deze gedeeltelijk hebt gevuld met gegevens, maar dat toekomstige gegevens worden omgeleid naar een andere Shard. U kunt bijvoorbeeld op dag bereik Shard en al 50 dagen aan een Shard toegewezen, maar op dag 24 wilt u dat toekomstige gegevens in een andere Shard worden gegrond. Met het [hulp programma Split-Merge](elastic-scale-overview-split-and-merge.md) Elastic data base kan deze bewerking worden uitgevoerd, maar als gegevens verplaatsing niet nodig is (bijvoorbeeld gegevens voor het bereik van dagen [25, 50), dat wil zeggen, een dag 25 van 50 exclusief, nog niet bestaat) kunt u dit volledig doen met de Shard-toewijzings beheer-api's.

### <a name="example-splitting-a-range-and-assigning-the-empty-portion-to-a-newly-added-shard"></a>Voor beeld: een bereik splitsen en het lege gedeelte toewijzen aan een pas toegevoegde Shard

Een Data Base met de naam sample_shard_2 en alle benodigde schema objecten erin zijn gemaakt.  

```csharp
// sm is a RangeShardMap object.
// Add a new shard to hold the range we will move
Shard shard2 = null;

if (!sm.TryGetShard(new ShardLocation(shardServer, "sample_shard_2"),out shard2))
{
    shard2 = sm.CreateShard(new ShardLocation(shardServer,"sample_shard_2"));  
}

// Split the Range holding Key 25
sm.SplitMapping(sm.GetMappingForKey(25), 25);

// Map new range holding [25-50) to different shard:
// first take existing mapping offline
sm.MarkMappingOffline(sm.GetMappingForKey(25));

// now map while offline to a different shard and take online
RangeMappingUpdate upd = new RangeMappingUpdate();
upd.Shard = shard2;
sm.MarkMappingOnline(sm.UpdateMapping(sm.GetMappingForKey(25), upd));
```

**Belang rijk**: gebruik deze techniek alleen als u zeker weet dat het bereik voor de bijgewerkte toewijzing leeg is.  Met de voor gaande methoden worden geen gegevens gecontroleerd voor het bereik dat wordt verplaatst, zodat het het beste is om cheques in uw code op te neemt.  Als er rijen bestaan in het bereik dat wordt verplaatst, komt de werkelijke gegevens distributie niet overeen met de bijgewerkte Shard-toewijzing. Gebruik het [hulp programma voor splitsen en samen voegen](elastic-scale-overview-split-and-merge.md) om in plaats daarvan de bewerking uit te voeren.  

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]