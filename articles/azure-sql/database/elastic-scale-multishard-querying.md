---
title: Query's uitvoeren op Shard-data bases
description: Query's uitvoeren op Shards met behulp van de client bibliotheek voor Elastic data base.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.topic: how-to
ms.custom: sqldbrb=1
author: stevestein
ms.author: sstein
ms.date: 01/25/2019
ms.openlocfilehash: 5a0dd12efb9d94bda264b3bd04b05cdc3df917e5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92786629"
---
# <a name="multi-shard-querying-using-elastic-database-tools"></a>Multi-Shard query's uitvoeren met behulp van Elastic data base-hulpprogram ma's
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

## <a name="overview"></a>Overzicht

Met de [Elastic database-hulpprogram ma's](elastic-scale-introduction.md)kunt u Shard-database oplossingen maken. **Query's voor meerdere Shard** worden gebruikt voor taken zoals het verzamelen/rapporteren van gegevens waarvoor een query moet worden uitgevoerd die over meerdere Shards wordt uitgerekt. (Dit is in tegens telling tot [gegevens afhankelijke route ring](elastic-scale-data-dependent-routing.md), waardoor alle werk wordt uitgevoerd op één Shard.)

1. U kunt een **RangeShardMap** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap), [.net](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)) of **ListShardMap** (Java [, .net) downloaden](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.listshardmap-1)met [](/java/api/com.microsoft.azure.elasticdb.shard.map.listshardmap)behulp van de **TryGetRangeShardMap** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.trygetrangeshardmap), [.net](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetrangeshardmap)), de **TryGetListShardMap** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.trygetlistshardmap), [.net](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetlistshardmap)) of de **GetShardMap** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.getshardmap), [.net](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.getshardmap))-methode. Zie [een ShardMapManager maken](elastic-scale-shard-map-management.md#constructing-a-shardmapmanager) en [een RangeShardMap of ListShardMap ophalen](elastic-scale-shard-map-management.md#get-a-rangeshardmap-or-listshardmap).
2. Maak een **MultiShardConnection** -object ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard.multishardconnection), [.net](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.query.multishardconnection)).
3. Maak een **MultiShardStatement of MultiShardCommand** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard.multishardstatement), [.net](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand)).
4. Stel de **eigenschap CommandText** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard.multishardstatement), [.net](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand)) in op een T-SQL-opdracht.
5. Voer de opdracht uit door de methode **ExecuteQueryAsync of ExecuteReader** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard.multishardstatement.executeQueryAsync), [.net](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand)) aan te roepen.
6. Bekijk de resultaten met behulp van de klasse **MultiShardResultSet of MultiShardDataReader** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard.multishardresultset), [.net](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.query.multisharddatareader)).

## <a name="example"></a>Voorbeeld

De volgende code illustreert het gebruik van meerdere Shard query's met behulp van een bepaalde **ShardMap** met de naam *myShardMap*.

```csharp
using (MultiShardConnection conn = new MultiShardConnection(myShardMap.GetShards(), myShardConnectionString))
{
    using (MultiShardCommand cmd = conn.CreateCommand())
    {
        cmd.CommandText = "SELECT c1, c2, c3 FROM ShardedTable";
        cmd.CommandType = CommandType.Text;
        cmd.ExecutionOptions = MultiShardExecutionOptions.IncludeShardNameColumn;
        cmd.ExecutionPolicy = MultiShardExecutionPolicy.PartialResults;

        using (MultiShardDataReader sdr = cmd.ExecuteReader())
        {
            while (sdr.Read())
            {
                var c1Field = sdr.GetString(0);
                var c2Field = sdr.GetFieldValue<int>(1);
                var c3Field = sdr.GetFieldValue<Int64>(2);
            }
        }
    }
}
```

Een belang rijk verschil is het bouwen van multi-Shard-verbindingen. Waar **SqlConnection** op een afzonderlijke data base werkt, neemt de **MultiShardConnection** een **_verzameling van Shards_*_ als invoer. Vul de verzameling Shards van een Shard-kaart in. De query wordt vervolgens uitgevoerd op de verzameling van Shards met behulp van _* Union alle** semantiek voor het samen stellen van één algemeen resultaat. Optioneel, de naam van de Shard waarvan de rij afkomstig is, kan worden toegevoegd aan de uitvoer met behulp van de eigenschap **ExecutionOptions** op de opdracht.

Let op de aanroep van **myShardMap. GetShards ()**. Deze methode haalt alle Shards op uit de Shard-kaart en biedt een eenvoudige manier om een query uit te voeren op alle relevante data bases. De verzameling Shards voor een query met meerdere Shard kan worden verfijnd door een LINQ-query uit te voeren op de verzameling die wordt geretourneerd door de aanroep van **myShardMap. GetShards ()**. In combi natie met het gedeeltelijke resultaten beleid is de huidige mogelijkheid in query's met meerdere Shard ontworpen om goed te werken voor tien tallen tot honderden Shards.

Een beperking met meerdere Shard query's is momenteel het ontbreken van validatie voor Shards en shardlets die worden opgevraagd. Hoewel gegevens afhankelijke route ring verifieert dat een bepaalde Shard deel uitmaakt van de Shard-kaart op het moment van query's, voert multi-Shard-query's deze controle niet uit. Dit kan leiden tot query's voor meerdere Shard die worden uitgevoerd op data bases die zijn verwijderd uit de Shard-toewijzing.

## <a name="multi-shard-queries-and-split-merge-operations"></a>Multi-Shard query's en Split-Merge-bewerkingen

Query's met meerdere Shard controleren niet of shardlets op de opgevraagde data base deel nemen aan actieve Split-Merge-bewerkingen. (Zie [schalen met behulp van het Elastic database hulp programma voor splitsen en samen voegen](elastic-scale-overview-split-and-merge.md).) Dit kan leiden tot inconsistenties waarbij rijen uit dezelfde shardlet worden weer gegeven voor meerdere data bases in dezelfde multi-Shard-query. Houd rekening met deze beperkingen en overweeg om doorlopende Split-Merge-bewerkingen en wijzigingen aan de Shard-kaart uit te voeren tijdens het uitvoeren van query's voor meerdere Shard.

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]