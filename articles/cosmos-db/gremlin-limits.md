---
title: Limieten van Azure Cosmos DB Gremlin
description: Referentie documentatie voor runtime beperkingen van Graph engine
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: reference
ms.date: 10/04/2019
ms.author: sngun
ms.openlocfilehash: 4e638fdff67ad2d0bc6f191cdfd46867ab847923
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93080104"
---
# <a name="azure-cosmos-db-gremlin-limits"></a>Limieten van Azure Cosmos DB Gremlin
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

In dit artikel vindt u informatie over de limieten van Azure Cosmos DB Gremlin-engine en wordt uitgelegd hoe deze van invloed kunnen zijn op klant navigatie.

Cosmos DB Gremlin is gebaseerd op Cosmos DB-infra structuur. Als gevolg hiervan zijn alle limieten die worden uitgelegd in [Azure Cosmos DB Service limieten](./concepts-limits.md) nog steeds van toepassing.

## <a name="limits"></a>Limieten

Wanneer de limiet voor Gremlin is bereikt, wordt het door sturen geannuleerd met een **x-MS-status code** van 429 die een beperkings fout aangeeft. Zie [Gremlin server response headers](gremlin-limits.md) voor meer informatie.

**Resource**    | **Standaardlimiet** | **Uitleg**
--- | --- | ---
*Script lengte* | **64 kB** | Maximale lengte van een Gremlin-doorkruisingsscript per aanvraag.
*Diepte van operator* | **400** |  Totaal aantal unieke stappen in een doorkruising. ```g.V().out()```Heeft bijvoorbeeld een operator aantal van 2: v () en out (), heeft de ```g.V('label').repeat(out()).times(100)``` operator diepte van 3: v (), Repeat () en out () omdat dit ```.times(100)``` een para meter voor de ```.repeat()``` operator is.
*Mate van parallelle uitvoering* | **32** | Maximum aantal opslagpartities dat in één aanvraag naar de opslaglaag wordt bevraagd. Grafieken met honderden partities worden beïnvloed door deze limiet.
*Herhalings limiet* | **32** | Maximum aantal iteraties dat een ```.repeat()```-operator kan uitvoeren. Elke ```.repeat()``` stap in de meeste gevallen voert de breedte-eerste-passage uit, wat betekent dat alle navigatie is beperkt tot Maxi maal 32 hops tussen hoek punten.
*Time-out voor door lopen* | **30 seconden** | Door lopen wordt geannuleerd wanneer deze de tijd overschrijdt. Cosmos DB Graph is een OLTP-database waar de meeste doorkruisingen binnen enkele milliseconden worden uitgevoerd. Als u OLAP-query's wilt uitvoeren op Cosmos DB grafiek, gebruikt u [Apache Spark](https://azure.microsoft.com/services/cosmos-db/) met [grafiek gegevens frames](https://spark.apache.org/docs/latest/sql-programming-guide.html#datasets-and-dataframes) en [Cosmos DB Spark-connector](https://github.com/Azure/azure-cosmosdb-spark).
*Time-out van niet-actieve verbinding* | **1 uur** | De tijd dat de Gremlin-service niet-actieve WebSocket-verbindingen open blijft. TCP Keep-Alive-pakketten of HTTP Keep-Alive-aanvragen verlengen de duur van de verbinding niet meer dan deze limiet. Cosmos DB Graph-engine beschouwt WebSocket-verbindingen als inactief als er geen actieve Gremlin-aanvragen op worden uitgevoerd.
*Resourcetokens per uur* | **100** | Het aantal unieke resourcetokens dat door Gremlin-clients wordt gebruikt om verbinding te maken met het Gremlin-account in een regio. Wanneer de toepassing een unieke token limiet van elk uur overschrijdt, `"Exceeded allowed resource token limit of 100 that can be used concurrently"` wordt de volgende verificatie aanvraag geretourneerd.

## <a name="next-steps"></a>Volgende stappen
* [Gremlin-antwoord headers Azure Cosmos DB](gremlin-headers.md)
* [Azure Cosmos DB bron tokens met Gremlin](how-to-use-resource-tokens-gremlin.md)