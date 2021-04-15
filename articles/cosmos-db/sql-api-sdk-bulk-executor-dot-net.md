---
title: 'Azure Cosmos DB: Bulkuitvoerder van .NET API, SDK & resources'
description: Meer informatie over de bulkuitvoerings-API en SDK van de bulkuitvoering, inclusief releasedatums, pensioendatums en wijzigingen die zijn aangebracht tussen elke versie van de Azure Cosmos DB .NET SDK voor bulkuitvoering.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 04/06/2021
ms.author: anfeldma
ms.openlocfilehash: 99b6e06b817f98e64968615b5e652b707268fe78
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107364495"
---
# <a name="net-bulk-executor-library-download-information"></a>Bibliotheek voor .NET-bulkuitvoerders: informatie downloaden 
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET SDK v3](sql-api-sdk-dotnet-standard.md)
> * [.NET SDK v2](sql-api-sdk-dotnet.md)
> * [.NET Core-SDK v2](sql-api-sdk-dotnet-core.md)
> * [.NET wijzigingenfeed-SDK v2](sql-api-sdk-dotnet-changefeed.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Java SDK v4](sql-api-sdk-java-v4.md)
> * [Async Java-SDK v2](sql-api-sdk-async-java.md)
> * [Sync Java-SDK v2](sql-api-sdk-java.md)
> * [Spring Data v2](sql-api-sdk-java-spring-v2.md)
> * [Spring Data v3](sql-api-sdk-java-spring-v3.md)
> * [Spark 3 OLTP-connector](sql-api-sdk-java-spark-v3.md)
> * [Spark 2 OLTP-connector](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api/cosmos-db/)
> * [REST-resourceprovider](/rest/api/cosmos-db-resource-provider/)
> * [SQL](./sql-query-getting-started.md)
> * [Bulkuitvoerprogramma - .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Bulkuitvoerprogramma - Java](sql-api-sdk-bulk-executor-java.md)

| | Koppeling/opmerkingen |
|---|---|
| **Beschrijving**| Met de .NET-bibliotheek voor bulkuitvoer kunnen clienttoepassingen bulkbewerkingen uitvoeren op Azure Cosmos DB accounts. Deze bibliotheek biedt naamruimten BulkImport, BulkUpdate en BulkDelete. De BulkImport-module kan documenten bulksgewijs opnemen op een geoptimaliseerde manier, zodat de doorvoer die is ingericht voor een verzameling maximaal wordt verbruikt. De BulkUpdate-module kan bestaande gegevens in Azure Cosmos-containers bulksgewijs bijwerken als patches. De BulkDelete-module kan documenten bulksgewijs verwijderen op een geoptimaliseerde manier, zodat de doorvoer die is ingericht voor een verzameling maximaal wordt verbruikt.|
|**SDK downloaden**| [NuGet](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.BulkExecutor/) |
| **Bulkuitvoerbibliotheek in GitHub**| [GitHub](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started)|
|**API-documentatie**|[.NET API-referentiedocumentatie](/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor)|
|**Aan de slag**|[Aan de slag met de bulkuitvoerbibliotheek .NET SDK](bulk-executor-dot-net.md)|
| **Huidig ondersteund framework**| Microsoft .NET Framework 4.5.2, 4.6.1 en .NET Standard 2.0 |

> [!NOTE]
> Als u bulkuitvoering gebruikt, raadpleegt u de meest recente versie 3.x van [de .NET SDK,](tutorial-sql-api-dotnet-bulk-import.md)waarin bulkuitvoering is ingebouwd in de SDK. 

## <a name="release-notes"></a>Opmerkingen bij de release

### <a name="241-preview"></a><a name="2.4.1-preview"></a>2.4.1-preview

* TotalElapsedTime is opgelost in het antwoord van BulkDelete om de totale tijd correct te meten, inclusief eventuele nieuwe proberen.

### <a name="240-preview"></a><a name="2.4.0-preview"></a>2.4.0-preview

* SDK-afhankelijkheid gewijzigd in >= 2.5.1

### <a name="230-preview2"></a><a name="2.3.0-preview2"></a>2.3.0-preview2

* Ondersteuning toegevoegd voor graaf bulkuitvoerder om TTL te accepteren op hoek punten en randen

### <a name="220-preview2"></a><a name="2.2.0-preview2"></a>2.2.0-preview2

* Er is een probleem opgelost dat uitzonderingen heeft veroorzaakt tijdens elastisch schalen van Azure Cosmos DB uitgevoerd in de gatewaymodus. Deze oplossing zorgt ervoor dat deze functioneel equivalent is aan versie 1.4.1.

### <a name="210-preview2"></a><a name="2.1.0-preview2"></a>2.1.0-preview2

* BulkDelete-ondersteuning toegevoegd voor SQL API-accounts om partitiesleutels en document-id-tuples te accepteren die moeten worden verwijderd. Deze wijziging zorgt ervoor dat het functioneel equivalent is aan versie 1.4.0.

### <a name="200-preview2"></a><a name="2.0.0-preview2"></a>2.0.0-preview2

* Inclusief MongoBulkExecutor ter ondersteuning van .NET Standard 2.0. Dankzij deze functie is het functioneel equivalent aan versie 1.3.0, met ondersteuning voor .NET Standard 2.0 als doel framework.

### <a name="200-preview"></a><a name="2.0.0-preview"></a>2.0.0-preview

* .NET Standard 2.0 is toegevoegd als een van de ondersteunde doel-frameworks om de bulkuitvoerbibliotheek te laten werken met .NET Core-toepassingen.

### <a name="189"></a><a name="1.8.9"></a>1.8.9

* Er is een probleem met BulkDeleteAsync opgelost toen waarden met aanhalingstekens met escape-waarden werden doorgegeven als invoerparameters.

### <a name="188"></a><a name="1.8.8"></a>1.8.8

* Er is een probleem opgelost in MongoBulkExecutor waardoor de documentgrootte onverwacht werd toegenomen door opvulling toe te voegen en in sommige gevallen de maximale documentgroottelimiet te overschrijden.

### <a name="187"></a><a name="1.8.7"></a>1.8.7

* Er is een probleem opgelost met BulkDeleteAsync wanneer de verzameling geneste partitiesleutelpaden heeft.

### <a name="186"></a><a name="1.8.6"></a>1.8.6

* MongoBulkExecutor implementeert nu IDisposable en wordt naar verwachting verwijderd na gebruik.

### <a name="185"></a><a name="1.8.5"></a>1.8.5

* Vergrendeling van SDK-versie verwijderd. Pakket is nu afhankelijk van SDK >= 2.5.1.

### <a name="184"></a><a name="1.8.4"></a>1.8.4

* De verwerking van id's bij het aanroepen van BulkImport met een lijst met POCO-objecten met numerieke waarden is opgelost.

### <a name="183"></a><a name="1.8.3"></a>1.8.3

* TotalElapsedTime is opgelost in het antwoord van BulkDelete om de totale tijd correct te meten, inclusief eventuele nieuwe proberen.

### <a name="182"></a><a name="1.8.2"></a>1.8.2

* Er is een hoog CPU-verbruik in bepaalde scenario's opgelost.
* Tracering maakt nu gebruik van TraceSource. Gebruikers kunnen listeners definiëren voor de `BulkExecutorTrace` bron.
* Er is een zeldzaam scenario opgelost dat een vergrendeling zou kunnen veroorzaken bij het verzenden van documenten in de buurt van 2 MB.

### <a name="160"></a><a name="1.6.0"></a>1.6.0

* De bulkuitvoering is bijgewerkt om nu de nieuwste versie van de Azure Cosmos DB .NET SDK (2.4.0) te gebruiken

### <a name="150"></a><a name="1.5.0"></a>1.5.0

* Ondersteuning toegevoegd voor graaf bulkuitvoerder om TTL te accepteren op hoek punten en randen

### <a name="141"></a><a name="1.4.1"></a>1.4.1

* Er is een probleem opgelost dat uitzonderingen heeft veroorzaakt tijdens elastisch schalen van Azure Cosmos DB uitgevoerd in de gatewaymodus.

### <a name="140"></a><a name="1.4.0"></a>1.4.0

* Ondersteuning voor BulkDelete toegevoegd voor SQL API-accounts om partitiesleutel en document-id-tuples te accepteren die moeten worden verwijderd.

### <a name="130"></a><a name="1.3.0"></a>1.3.0

* Er is een probleem opgelost dat een opmaakprobleem heeft veroorzaakt in de gebruikersagent die wordt gebruikt door de bulkuitvoerder.

### <a name="120"></a><a name="1.2.0"></a>1.2.0

* Er is een verbetering aangebracht in het importeren en bijwerken van API's voor bulksgewijs uitvoeren om zich transparant aan te passen aan elastisch schalen van Cosmos-containers wanneer de opslag de huidige capaciteit overschrijdt zonder uitzonderingen te geven.

### <a name="112"></a><a name="1.1.2"></a>1.1.2

* De DocumentDB .NET SDK-afhankelijkheid is omhoog gedrempeld naar versie 2.1.3.

### <a name="111"></a><a name="1.1.1"></a>1.1.1

* Er is een probleem opgelost, waardoor een JSRT-fout werd veroorzaakt tijdens het importeren naar vaste verzamelingen.

### <a name="110"></a><a name="1.1.0"></a>1.1.0

* Ondersteuning toegevoegd voor BulkDelete-bewerking voor Azure Cosmos DB SQL API-accounts.
* Ondersteuning toegevoegd voor bulkimportbewerking voor accounts met Azure Cosmos DB API voor MongoDB.
* De DocumentDB .NET SDK-afhankelijkheid is hoger dan versie 2.0.0. 

### <a name="102"></a><a name="1.0.2"></a>1.0.2

* Ondersteuning toegevoegd voor bulkimportbewerking voor Azure Cosmos DB Gremlin API-accounts.

### <a name="101"></a><a name="1.0.1"></a>1.0.1

* Kleine fout opgelost in de BulkImport-bewerking voor Azure Cosmos DB SQL API-accounts.

### <a name="100"></a><a name="1.0.0"></a>1.0.0

* Ondersteuning toegevoegd voor BulkImport- en BulkUpdate-bewerkingen voor Azure Cosmos DB SQL API-accounts.

## <a name="next-steps"></a>Volgende stappen

Zie het volgende artikel voor meer informatie over de Java-bibliotheek voor bulksgewijs uitvoeren:

[SDK voor Java-bulkuitvoerbibliotheek en release-informatie](sql-api-sdk-bulk-executor-java.md)