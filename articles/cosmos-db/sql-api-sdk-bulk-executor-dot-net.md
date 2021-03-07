---
title: 'Azure Cosmos DB: bulk-uitvoerder .NET API, SDK & resources'
description: Meer informatie over de bulk-uitvoerder .NET API en SDK, inclusief release datums, pensioen datums en wijzigingen die zijn aangebracht tussen de verschillende versies van de Azure Cosmos DB bulk-vervoerder .NET SDK.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 08/12/2020
ms.author: anfeldma
ms.openlocfilehash: 91fcfc614fed95c40aa3fed4368d17399255e8b5
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/07/2021
ms.locfileid: "102423873"
---
# <a name="net-bulk-executor-library-download-information"></a>.NET-bibliotheek voor bulksgewijs laden: informatie downloaden 
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
> * [Spark-connector](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api/cosmos-db/)
> * [REST-resource provider](/rest/api/cosmos-db-resource-provider/)
> * [SQL](./sql-query-getting-started.md)
> * [Bulkuitvoerprogramma - .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Bulkuitvoerprogramma - Java](sql-api-sdk-bulk-executor-java.md)

| |  |
|---|---|
| **Beschrijving**| Met de bibliotheek .NET bulksgewijze uitvoerder kunnen client toepassingen bulk bewerkingen uitvoeren op Azure Cosmos DB accounts. Deze bibliotheek bevat BulkImport-, BulkUpdate-en BulkDelete-naam ruimten. De BulkImport-module kan documenten bulksgewijs opnemen in een geoptimaliseerde manier, zodat de door Voer ingericht voor een verzameling voor het maximale aantal wordt verbruikt. De BulkUpdate-module kan bestaande gegevens in azure Cosmos-containers bulksgewijs bijwerken als patches. De BulkDelete-module kan documenten bulksgewijs verwijderen op een geoptimaliseerde manier, zodat de door Voer ingericht voor een verzameling voor de maximale grootte wordt verbruikt.|
|**SDK downloaden**| [NuGet](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.BulkExecutor/) |
| **Bibliotheek voor bulk-uitvoerder in GitHub**| [GitHub](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started)|
|**API-documentatie**|[Naslag documentatie voor .NET API](/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor)|
|**Aan de slag**|[Aan de slag met de bibliotheek voor bulk-uitvoerder .NET SDK](bulk-executor-dot-net.md)|
| **Huidig ondersteund framework**| Microsoft .NET Framework 4.5.2, 4.6.1 en .NET Standard 2,0 |

> [!NOTE]
> Als u een bulk-uitvoerder gebruikt, raadpleegt u de meest recente versie 3. x van de [.NET SDK](tutorial-sql-api-dotnet-bulk-import.md), die in de SDK is ingebouwd. 

## <a name="release-notes"></a>Releaseopmerkingen

### <a name="241-preview"></a><a name="2.4.1-preview"></a>2.4.1-Preview

* Vaste TotalElapsedTime in het antwoord van BulkDelete om de totale tijd correct te meten, inclusief eventuele nieuwe pogingen.

### <a name="240-preview"></a><a name="2.4.0-preview"></a>2.4.0-Preview

* De SDK-afhankelijkheid is gewijzigd in >= 2.5.1

### <a name="230-preview2"></a><a name="2.3.0-preview2"></a>2.3.0-preview2

* Er is ondersteuning toegevoegd voor de bulk-uitvoerder van de grafiek om TTL op hoek punten en randen te accepteren

### <a name="220-preview2"></a><a name="2.2.0-preview2"></a>2.2.0-preview2

* Er is een probleem opgelost, waardoor uitzonde ringen worden veroorzaakt tijdens het gebruik van elastische schaling van Azure Cosmos DB wanneer de gateway modus wordt uitgevoerd. Deze oplossing maakt IT functioneel equivalent aan 1.4.1-release.

### <a name="210-preview2"></a><a name="2.1.0-preview2"></a>2.1.0-preview2

* Toegevoegde BulkDelete-ondersteuning voor SQL-API-accounts voor het accepteren van partitie sleutel, te verwijderen document-ID-Tuples. Deze wijziging is functioneel gelijk aan 1.4.0-release.

### <a name="200-preview2"></a><a name="2.0.0-preview2"></a>2.0.0-preview2

* Inclusief MongoBulkExecutor ter ondersteuning van .NET Standard 2,0. Deze functie is functioneel gelijk aan 1.3.0-release, met toevoeging van ondersteuning van .NET Standard 2,0 als het doel raamwerk.

### <a name="200-preview"></a><a name="2.0.0-preview"></a>2.0.0-Preview

* .NET Standard 2,0 is toegevoegd als een van de ondersteunde doel stellingen om de bulk-uitvoerder bibliotheek te laten werken met .NET core-toepassingen.

### <a name="189"></a><a name="1.8.9"></a>1.8.9

* Er is een probleem met BulkDeleteAsync opgelost wanneer waarden met een aanhalings teken als invoer parameters zijn door gegeven.

### <a name="188"></a><a name="1.8.8"></a>1.8.8

* Er is een probleem opgelost in MongoBulkExecutor die de document grootte onverwacht heeft verhoogd door opvulling toe te voegen en in sommige gevallen de maximale document grootte te overschrijden.

### <a name="187"></a><a name="1.8.7"></a>1.8.7

* Er is een probleem met BulkDeleteAsync opgelost wanneer de verzameling geneste partitie sleutel paden heeft.

### <a name="186"></a><a name="1.8.6"></a>1.8.6

* MongoBulkExecutor implementeert nu IDisposable en wordt naar verwachting buiten gebruik gesteld.

### <a name="185"></a><a name="1.8.5"></a>1.8.5

* De vergren deling op de SDK-versie is verwijderd. Pakket is nu afhankelijk van SDK >= 2.5.1.

### <a name="184"></a><a name="1.8.4"></a>1.8.4

* Vast verwerken van id's bij het aanroepen van BulkImport met een lijst met POCO-objecten met numerieke waarden.

### <a name="183"></a><a name="1.8.3"></a>1.8.3

* Vaste TotalElapsedTime in het antwoord van BulkDelete om de totale tijd correct te meten, inclusief eventuele nieuwe pogingen.

### <a name="182"></a><a name="1.8.2"></a>1.8.2

* Vast hoog CPU-verbruik voor bepaalde scenario's.
* Tracering maakt nu gebruik van TraceSource. Gebruikers kunnen listeners voor de bron definiëren `BulkExecutorTrace` .
* Er is een zeldzaam scenario opgelost dat kan leiden tot een vergren deling bij het verzenden van documenten bij een grootte van 2 MB.

### <a name="160"></a><a name="1.6.0"></a>1.6.0

* De bulk-uitvoerder is bijgewerkt naar nu de nieuwste versie van de Azure Cosmos DB .NET SDK (2.4.0)

### <a name="150"></a><a name="1.5.0"></a>1.5.0

* Er is ondersteuning toegevoegd voor de bulk-uitvoerder van de grafiek om TTL op hoek punten en randen te accepteren

### <a name="141"></a><a name="1.4.1"></a>1.4.1

* Er is een probleem opgelost, waardoor uitzonde ringen worden veroorzaakt tijdens het gebruik van elastische schaling van Azure Cosmos DB wanneer de gateway modus wordt uitgevoerd.

### <a name="140"></a><a name="1.4.0"></a>1.4.0

* Toegevoegde BulkDelete-ondersteuning voor SQL-API-accounts voor het accepteren van partitie sleutel, te verwijderen document-ID-Tuples.

### <a name="130"></a><a name="1.3.0"></a>1.3.0

* Er is een probleem opgelost, waardoor er een opmaak probleem is opgetreden in de gebruikers agent die wordt gebruikt door bulk-uitvoerder.

### <a name="120"></a><a name="1.2.0"></a>1.2.0

* Verbetering van de import-en update-Api's voor bulk-uitvoerder om op transparante wijze aan te passen aan elastisch schalen van de Cosmos-container wanneer opslag de huidige capaciteit overschrijdt zonder uitzonde ringen te genereren.

### <a name="112"></a><a name="1.1.2"></a>1.1.2

* Bewaak de DocumentDB .NET SDK-afhankelijkheid naar versie 2.1.3.

### <a name="111"></a><a name="1.1.1"></a>1.1.1

* Er is een probleem opgelost, waardoor een bulk-uitvoerder de JSRT-fout genereert tijdens het importeren in vaste verzamelingen.

### <a name="110"></a><a name="1.1.0"></a>1.1.0

* Er is ondersteuning toegevoegd voor de BulkDelete-bewerking voor Azure Cosmos DB SQL-API-accounts.
* Er is ondersteuning toegevoegd voor BulkImport-bewerking voor accounts met de API van Azure Cosmos DB voor MongoDB.
* Bewaak de DocumentDB .NET SDK-afhankelijkheid naar versie 2.0.0. 

### <a name="102"></a><a name="1.0.2"></a>1.0.2

* Er is ondersteuning toegevoegd voor de BulkImport-bewerking voor de Azure Cosmos DB Gremlin-API-accounts.

### <a name="101"></a><a name="1.0.1"></a>1.0.1

* Kleine fout oplossing voor de BulkImport-bewerking voor Azure Cosmos DB SQL-API-accounts.

### <a name="100"></a><a name="1.0.0"></a>1.0.0

* Er is ondersteuning toegevoegd voor BulkImport-en BulkUpdate-bewerkingen voor Azure Cosmos DB SQL-API-accounts.

## <a name="next-steps"></a>Volgende stappen

Zie het volgende artikel voor meer informatie over de Java-bibliotheek voor bulk-uitvoerder:

[SDK en release-informatie voor de Java bulk-uitvoerder](sql-api-sdk-bulk-executor-java.md)