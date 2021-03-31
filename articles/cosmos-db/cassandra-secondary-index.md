---
title: Indexeren in Azure Cosmos DB Cassandra-API-account
description: Meer informatie over het werken met secundaire indexen in azure Azure Cosmos DB Cassandra-API-account.
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 04/04/2020
ms.author: thvankra
ms.reviewer: sngun
ms.openlocfilehash: 98e8f713ad2e4eef47e40d89a23dbf49a98ad67c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93339885"
---
# <a name="secondary-indexing-in-azure-cosmos-db-cassandra-api"></a>Secundaire indexering in Azure Cosmos DB Cassandra-API
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

De Cassandra-API in Azure Cosmos DB maakt gebruik van de onderliggende indexerings infrastructuur om de indexerings sterkte weer te geven die inherent is aan het platform. In tegens telling tot de core SQL-API, worden Cassandra-API in Azure Cosmos DB echter niet alle kenmerken standaard geïndexeerd. In plaats daarvan wordt secundaire indexering ondersteund om een index te maken voor bepaalde kenmerken, wat gedraagt zich op dezelfde manier als Apache Cassandra.  

Over het algemeen is het niet raadzaam om filter query's uit te voeren op de kolommen die niet zijn gepartitioneerd. U moet expliciete filter syntaxis toestaan gebruiken, wat resulteert in een bewerking die mogelijk niet goed wordt uitgevoerd. In Azure Cosmos DB kunt u dergelijke query's uitvoeren op kenmerken met weinig kardinaliteit, omdat ze uitwaaieren over partities om de resultaten op te halen.

Het is niet raadzaam om een index te maken op een regel matig bijgewerkte kolom. Het is verstandig om een index te maken wanneer u de tabel definieert. Dit zorgt ervoor dat gegevens en indexen zich in een consistente staat bevinden. Als u op dat moment een nieuwe index voor de bestaande gegevens maakt, kunt u de wijziging van de index voortgang voor de tabel niet bijhouden. Als u de voortgang van deze bewerking wilt volgen, moet u de voortgangs wijziging aanvragen via een [ondersteunings ticket]( https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request).


> [!NOTE]
> Secundaire index wordt niet ondersteund voor de volgende objecten:
> - gegevens typen zoals bevroren verzamelings typen, decimalen en variant typen.
> - Statische kolommen
> - Cluster sleutels

## <a name="indexing-example"></a>Voor beeld van indexeren

Maak eerst een voor beeld van een kern ruimte en tabel door de volgende opdrachten uit te voeren op de CQL shell-prompt:

```shell
CREATE KEYSPACE sampleks WITH REPLICATION = {'class' : 'SimpleStrategy'};
CREATE TABLE sampleks.t1(user_id int PRIMARY KEY, lastname text) WITH cosmosdb_provisioned_throughput=400;
``` 

Voeg vervolgens voorbeeld gebruikers gegevens in met de volgende opdrachten:

```shell
insert into sampleks.t1(user_id,lastname) values (1, 'nishu');
insert into sampleks.t1(user_id,lastname) values (2, 'vinod');
insert into sampleks.t1(user_id,lastname) values (3, 'bat');
insert into sampleks.t1(user_id,lastname) values (5, 'vivek');
insert into sampleks.t1(user_id,lastname) values (6, 'siddhesh');
insert into sampleks.t1(user_id,lastname) values (7, 'akash');
insert into sampleks.t1(user_id,lastname) values (8, 'Theo');
insert into sampleks.t1(user_id,lastname) values (9, 'jagan');
```

Als u probeert de volgende-instructie uit te voeren, wordt er een fout melding weer met de vraag `ALLOW FILTERING` : 

```shell
select user_id, lastname from sampleks.t1 where lastname='nishu';
``` 

Hoewel de Cassandra-API ondersteuning biedt voor het filteren van toestaan, wordt dit niet aanbevolen. In plaats daarvan moet u een index maken in de, zoals wordt weer gegeven in het volgende voor beeld:

```shell
CREATE INDEX ON sampleks.t1 (lastname);
```
Nadat u in het veld LastName een index hebt gemaakt, kunt u de vorige query nu uitvoeren. Met Cassandra-API in Azure Cosmos DB hoeft u geen index naam op te geven. Er wordt een standaard index met notatie `tablename_columnname_idx` gebruikt. Bijvoorbeeld, ` t1_lastname_idx` is de index naam voor de vorige tabel.

## <a name="dropping-the-index"></a>De index verwijderen 
U moet weten wat de index naam is om de index te verwijderen. Voer de `desc schema` opdracht uit om de beschrijving van de tabel op te halen. De uitvoer van deze opdracht bevat de index naam in de indeling `CREATE INDEX tablename_columnname_idx ON keyspacename.tablename(columnname)` . U kunt vervolgens de index naam gebruiken om de index te verwijderen, zoals wordt weer gegeven in het volgende voor beeld:

```shell
drop index sampleks.t1_lastname_idx;
```

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over hoe [automatische indexering](index-overview.md) werkt in azure Cosmos db
* [Door Azure Cosmos DB Cassandra API ondersteunde Apache Cassandra-functies](cassandra-support.md)
