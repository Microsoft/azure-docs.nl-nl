---
title: 'Azure Cosmos DB: bulk-upload Java API, SDK & resources'
description: Meer informatie over de bulk-lanceren Java API en SDK, inclusief release datums, pensioen datums en wijzigingen die zijn aangebracht tussen elke versie van de Azure Cosmos DB bulk-uitvoerder Java SDK.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 08/12/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 5ef75719686f9299fee56cad247ca49167171813
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104577169"
---
# <a name="java-bulk-executor-library-download-information"></a>Java-bibliotheek voor bulksgewijs laden: Download informatie
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

| | Koppeling/notities |
|---|---|
|**Beschrijving**|Met de bibliotheek bulk-uitvoerder kunnen client toepassingen bulk bewerkingen uitvoeren in Azure Cosmos DB accounts. de bibliotheek voor bulk-uitvoerder biedt BulkImport-en BulkUpdate-naam ruimten. De BulkImport-module kan documenten bulksgewijs opnemen in een geoptimaliseerde manier, zodat de door Voer ingericht voor een verzameling voor het maximale aantal wordt verbruikt. De BulkUpdate-module kan bestaande gegevens in azure Cosmos-containers bulksgewijs bijwerken als patches.|
|**SDK downloaden**|[Maven](https://search.maven.org/#search%7Cga%7C1%7Cdocumentdb-bulkexecutor)|
|**Bibliotheek voor bulk-uitvoerder in GitHub**|[GitHub](https://github.com/Azure/azure-cosmosdb-bulkexecutor-java-getting-started)|
| **API-documentatie**| [Naslag documentatie voor Java API](/java/api/com.microsoft.azure.documentdb.bulkexecutor)|
|**Aan de slag**|[Aan de slag met de bibliotheek met Java-SDK voor bulksgewijs laden](bulk-executor-java.md)|
|**Minimale ondersteunde runtime**|[Java Development Kit (JDK) 7 +](/java/azure/jdk/)|

## <a name="release-notes"></a>Releaseopmerkingen

### <a name="2100"></a><a name="2.10.0"></a>2.10.0

* Correctie voor DocumentAnalyzer. Java voor het uitpakken van geneste partitie sleutel waarden van JSON.

### <a name="294"></a><a name="2.9.4"></a>2.9.4

* Voeg functionaliteit toe aan BulkDelete-bewerkingen om een nieuwe poging te doen voor specifieke fouten en om ook een lijst met fouten te retour neren aan de gebruiker die opnieuw zou kunnen worden uitgevoerd.

### <a name="293"></a><a name="2.9.3"></a>2.9.3

* Update voor de Cosmos SDK-versie 2.4.7.

### <a name="292"></a><a name="2.9.2"></a>2.9.2

* Correctie voor ' mergeAll ' om door te gaan met ' id ' en de partitie sleutel waarde zodat alle patched document eigenschappen die zijn geplaatst na ' id ' en partitie sleutel waarde worden toegevoegd aan de lijst met bijgewerkte items.

### <a name="291"></a><a name="2.9.1"></a>2.9.1

* Hiermee wordt de begin graad van gelijktijdigheid bijgewerkt naar 1 en worden logboeken voor fout opsporing voor minibatch toegevoegd.