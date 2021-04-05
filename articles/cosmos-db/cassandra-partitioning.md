---
title: Partitioneren in Azure Cosmos DB Cassandra-API
description: Meer informatie over partitioneren in Azure Cosmos DB Cassandra-API
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 05/20/2020
ms.openlocfilehash: ba615d3e41393afe007238a0fe1e694732ad123e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93087635"
---
# <a name="partitioning-in-azure-cosmos-db-cassandra-api"></a>Partitioneren in Azure Cosmos DB Cassandra-API
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

In dit artikel wordt beschreven hoe partitioneren werkt in Azure Cosmos DB Cassandra-API. 

Cassandra-API maakt gebruik van partitionering om de afzonderlijke tabellen in een spatie te schalen om te voldoen aan de prestatie behoeften van uw toepassing. Partities worden gevormd op basis van de waarde van een partitie sleutel die is gekoppeld aan elke record in een tabel. Alle records in een partitie hebben dezelfde partitie sleutel waarde. Azure Cosmos DB transparant en beheert automatisch de plaatsing van partities over de fysieke resources om efficiënt te voldoen aan de schaal baarheid en prestatie behoeften van de tabel. Naarmate de door Voer en opslag vereisten van een toepassing toenemen, Azure Cosmos DB verplaatst en verdeelt u de gegevens over een groter aantal fysieke machines.

Vanuit het perspectief van de ontwikkelaar gedraagt de partitie zich op dezelfde manier als voor Azure Cosmos DB Cassandra-API als in native [Apache Cassandra](https://cassandra.apache.org/). Er zijn echter enkele verschillen achter de schermen. 


## <a name="differences-between-apache-cassandra-and-azure-cosmos-db"></a>Verschillen tussen Apache Cassandra en Azure Cosmos DB

In Azure Cosmos DB wordt elke computer waarop partities worden opgeslagen, zelf aangeduid als [fysieke partitie](partitioning-overview.md#physical-partitions). De fysieke partitie is Akin naar een virtuele machine. een toegewezen reken eenheid of een set fysieke resources. Elke partitie die op deze reken eenheid is opgeslagen, wordt een [logische partitie](partitioning-overview.md#logical-partitions) in azure Cosmos DB genoemd. Als u al bekend bent met Apache Cassandra, kunt u logische partities op dezelfde manier beschouwen als gewone partities in Cassandra. 

Apache Cassandra raadt een limiet van 100 MB aan voor de grootte van een gegevens die kunnen worden opgeslagen in een partitie. Met de Cassandra-API voor Azure Cosmos DB kunt u Maxi maal 20 GB per logische partitie en Maxi maal 30 gigabyte aan gegevens per fysieke partitie. In Azure Cosmos DB, in tegens telling tot Apache Cassandra, wordt de beschik bare reken capaciteit in de fysieke partitie uitgedrukt met behulp van één metrische waarde voor [aanvraag eenheden](request-units.md), waarmee u uw werk belasting kunt zien in termen van aanvragen (lees-of schrijf bewerkingen) per seconde, in plaats van kernen, geheugen of IOPS. Zo kunt u de capaciteits planning op een meer recht naar voren brengen, zodra u de kosten van elke aanvraag begrijpt. Elke fysieke partitie kan Maxi maal 10000 RUs beschik bare reken capaciteit hebben. Meer informatie over de opties voor schaal baarheid vindt u in het artikel over [Elastic Scale](manage-scale-cassandra.md) in Cassandra-API. 

In Azure Cosmos DB bestaat elke fysieke partitie uit een set replica's, ook wel bekend als replica sets, met ten minste 4 replica's per partitie. Dit is in tegens telling tot Apache Cassandra, waarbij het instellen van een replicatie factor 1 mogelijk is. Dit leidt echter tot lage Beschik baarheid als het enige knoop punt met de gegevens uitvalt. In Cassandra-API is er altijd een replicatie factor van 4 (quorum van 3). Azure Cosmos DB beheert automatisch replica sets, terwijl deze moeten worden onderhouden met behulp van verschillende hulpprogram ma's in Apache Cassandra. 

Apache Cassandra bevat een concept van tokens, die hashes zijn van partitie sleutels. De tokens zijn gebaseerd op een murmur3 64 byte-hash, met waarden variërend van-2 ^ 63 tot-2 ^ 63-1. Dit bereik wordt meestal de ' token ring ' genoemd in Apache Cassandra. De token ring wordt gedistribueerd naar tokens en deze bereiken zijn verdeeld over de knoop punten die aanwezig zijn in een systeem eigen Apache Cassandra-cluster. Partitioneren voor Azure Cosmos DB wordt op een vergelijk bare manier geïmplementeerd, behalve dat er een ander hash-algoritme wordt gebruikt en een grotere interne token ring heeft. Extern hebben we echter hetzelfde token bereik beschikbaar als Apache Cassandra, d.w.z.-2 ^ 63 tot-2 ^ 63-1.


## <a name="primary-key"></a>Primaire sleutel

Alle tabellen in Cassandra-API moeten een `primary key` gedefinieerd. De syntaxis voor een primaire sleutel wordt hieronder weer gegeven:

```shell
column_name cql_type_definition PRIMARY KEY
```

Stel dat we een gebruikers tabel willen maken waarin berichten voor verschillende gebruikers worden opgeslagen:

```shell
CREATE TABLE uprofile.user ( 
   id UUID PRIMARY KEY, 
   user text,  
   message text);
```

In dit ontwerp hebben we het `id` veld als primaire sleutel gedefinieerd. De primaire sleutel fungeert als de id voor de record in de tabel en wordt ook gebruikt als partitie sleutel in Azure Cosmos DB. Als de primaire sleutel is gedefinieerd in de eerder beschreven manier, is er slechts één record in elke partitie. Dit leidt tot een perfecte horizontale en schaal bare distributie bij het schrijven van gegevens naar de data base en is ideaal voor het gebruik van aanvragen voor sleutel waarden. De toepassing moet de primaire sleutel geven bij het lezen van gegevens uit de tabel om de Lees prestaties te maximaliseren. 

:::image type="content" source="./media/cassandra-partitioning/cassandra-partitioning.png" alt-text="partities" border="false":::


## <a name="compound-primary-key"></a>Samengestelde primaire sleutel

Apache Cassandra heeft ook een concept van  `compound keys` . Een samengestelde `primary key` bestaat uit meer dan een kolom; de eerste kolom is de en `partition key` eventuele aanvullende kolommen zijn `clustering keys` . De syntaxis voor a `compound primary key` wordt hieronder weer gegeven:

```shell
PRIMARY KEY (partition_key_column_name, clustering_column_name [, ...])
```

Stel dat we het bovenstaande ontwerp willen wijzigen en het mogelijk maken om op efficiënte wijze berichten voor een bepaalde gebruiker op te halen:

```shell
CREATE TABLE uprofile.user (
   user text,  
   id int, 
   message text, 
   PRIMARY KEY (user, id));
```

In dit ontwerp worden nu `user` de partitie sleutel en `id` de cluster sleutel gedefinieerd. U kunt zoveel cluster sleutels definiëren als u wilt, maar elke waarde (of een combi natie van waarden) voor de cluster sleutel moet uniek zijn om te voor komen dat er meerdere records worden toegevoegd aan dezelfde partitie, bijvoorbeeld:

```shell
insert into uprofile.user (user, id, message) values ('theo', 1, 'hello');
insert into uprofile.user (user, id, message) values ('theo', 2, 'hello again');
```

Wanneer gegevens worden geretourneerd, wordt deze door de cluster sleutel gesorteerd zoals verwacht in Apache Cassandra:

:::image type="content" source="./media/cassandra-partitioning/select-from-pk.png" alt-text="Scherm opname van de geretourneerde gegevens die door de cluster sleutel worden gesorteerd.":::

Met gegevens die op deze manier zijn gemodelleerd, kunnen meerdere records worden toegewezen aan elke partitie, gegroepeerd op gebruiker. We kunnen een query die op een efficiënte manier wordt gerouteerd door de `partition key` (in dit geval `user` ), om alle berichten van een bepaalde gebruiker te verkrijgen. 

:::image type="content" source="./media/cassandra-partitioning/cassandra-partitioning2.png" alt-text="Diagram dat laat zien hoe meerdere records kunnen worden toegewezen aan elke partitie, gegroepeerd op gebruiker." border="false":::


## <a name="composite-partition-key"></a>Samengestelde partitie sleutel

Samengestelde-partitie sleutels werken op dezelfde manier als samengestelde sleutels, behalve dat u meerdere kolommen kunt opgeven als een samengestelde partitie sleutel. De syntaxis van samengestelde partitie sleutels wordt hieronder weer gegeven:

```shell
PRIMARY KEY (
   (partition_key_column_name[, ...]), 
    clustering_column_name [, ...]);
```
U kunt bijvoorbeeld het volgende hebben, waarbij de unieke combi natie van `firstname` en de `lastname` partitie sleutel vormt en `id` de cluster sleutel is:

```shell
CREATE TABLE uprofile.user ( 
   firstname text, 
   lastname text,
   id int,  
   message text, 
   PRIMARY KEY ((firstname, lastname), id) );
```

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [partitioneren en horizon taal schalen in azure Cosmos DB](partitioning-overview.md).
* Meer informatie over [ingerichte door Voer in azure Cosmos DB](request-units.md).
* Meer informatie over [globale distributie in azure Cosmos DB](distribute-data-globally.md).
