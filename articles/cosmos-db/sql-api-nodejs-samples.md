---
title: Node.js-voorbeelden voor het beheren van gegevens in Azure Cosmos-database
description: Zoek Node.js-voorbeelden op GitHub voor veelvoorkomende taken in Azure Cosmos DB, zoals CRUD-bewerkingen.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 08/23/2019
ms.author: dech
ms.custom: devx-track-js
ms.openlocfilehash: e2d8316e0b44593e220a0c9a9358b465ad39c9eb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102216833"
---
# <a name="nodejs-examples-to-manage-data-in-azure-cosmos-db"></a>Node.js-voorbeelden voor het beheren van gegevens in Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET V2 SDK-voorbeelden](sql-api-dotnet-samples.md)
> * [.NET V3 SDK-voorbeelden](sql-api-dotnet-v3sdk-samples.md)
> * [Java V4 SDK-voorbeelden](sql-api-java-sdk-samples.md)
> * [Spring Data V3 SDK-voorbeelden](sql-api-spring-data-sdk-samples.md)
> * [Node.js-voorbeelden](sql-api-nodejs-samples.md)
> * [Python-voorbeelden](sql-api-python-samples.md)
> * [Galerie met codevoorbeelden voor Azure](https://azure.microsoft.com/resources/samples/?sort=0&service=cosmos-db)
> 
> 

Voorbeeldoplossingen waarmee CRUD-bewerkingen en andere veelvoorkomende bewerkingen worden uitgevoerd in Azure Cosmos DB-resources worden uitgevoerd, zijn opgenomen in de GitHub-opslagplaats [azure-cosmos-js](https://github.com/Azure/azure-cosmos-js/tree/master/samples). Dit artikel bevat:

* Koppelingen naar de taken in elk van de Node.js-voorbeeldprojectbestanden.
* Koppelingen naar het bijbehorende API-referentiemateriaal.

**Vereisten**

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

- U kunt [voordelen als Visual Studio-abonnee activeren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio): Via uw Visual Studio-abonnement ontvangt u elke maand een tegoed dat u voor betaalde Azure-services kunt gebruiken.

[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

U hebt ook de [JavaScript SDK](sql-api-sdk-node.md) nodig.
   
   > [!NOTE]
   > Elk voorbeeld staat op zichzelf. Het stelt zichzelf in en aan het einde worden de gegevens automatisch opgeschoond. Als zodanig wordt in de voorbeelden [Containers.create](/javascript/api/%40azure/cosmos/containers) meerdere keren aangeroepen. Telkens wanneer dit wordt gedaan, wordt uw abonnement gefactureerd voor één uur gebruik per prestatielaag van de container die wordt gemaakt.
   > 
   > 

## <a name="database-examples"></a>Voorbeelden voor databases

In het bestand [DatabaseManagement](https://github.com/Azure/azure-cosmos-js/blob/master/samples/DatabaseManagement.ts) ziet u hoe u de CRUD-bewerkingen op de database uitvoert. Zie het conceptuele artikel [Werken met databases, containers en items](account-databases-containers-items.md) voor meer informatie over de Azure Cosmos-databases voordat u de volgende voorbeelden uitvoert. 

| Taak | API-verwijzing |
| --- | --- |
| [Database maken indien deze niet bestaat](https://github.com/Azure/azure-cosmos-js/blob/master/samples/DatabaseManagement.ts#L12-L14) |[Databases.createIfNotExists](/javascript/api/@azure/cosmos/databases#createifnotexists-databaserequest--requestoptions-) |
| [Databases voor een account weergeven](https://github.com/Azure/azure-cosmos-js/blob/master/samples/DatabaseManagement.ts#L16-L18) |[Databases.readAll](/javascript/api/@azure/cosmos/databases#readall-feedoptions-) |
| [Een database lezen op id](https://github.com/Azure/azure-cosmos-js/blob/master/samples/DatabaseManagement.ts#L20-L29) |[Database.read](/javascript/api/@azure/cosmos/database#read-requestoptions-) |
| [Een database verwijderen](https://github.com/Azure/azure-cosmos-js/blob/master/samples/DatabaseManagement.ts#L31-L32) |[Database.delete](/javascript/api/@azure/cosmos/database#delete-requestoptions-) |

## <a name="container-examples"></a>Voorbeelden van containers

In het bestand [ContainerManagement](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ContainerManagement.ts) ziet u hoe u de CRUD-bewerkingen op de database uitvoert. Zie het conceptuele artikel [Werken met databases, containers en items](account-databases-containers-items.md) voor meer informatie over de Azure Cosmos-verzamelingen voordat u de volgende voorbeelden uitvoert. 

| Taak | API-verwijzing |
| --- | --- |
| [Container maken indien deze niet bestaat](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ContainerManagement.ts#L14-L15) |[Containers.createIfNotExists](/javascript/api/@azure/cosmos/containers#createifnotexists-containerrequest--requestoptions-) |
| [Containers voor een account weergeven](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ContainerManagement.ts#L17-L21) |[Containers.readAll](/javascript/api/@azure/cosmos/containers#readall-feedoptions-) |
| [Een containerdefinitie lezen](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ContainerManagement.ts#L23-L26) |[Container.read](/javascript/api/@azure/cosmos/container#read-requestoptions-) |
| [Container verwijderen](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ContainerManagement.ts#L28-L30) |[Container.delete](/javascript/api/@azure/cosmos/container#delete-requestoptions-) |

## <a name="item-examples"></a>Voorbeelden van items

In het bestand [ItemManagement](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts) ziet u hoe u de CRUD-bewerkingen op het item uitvoert. Zie het conceptuele artikel [Werken met databases, containers en items](account-databases-containers-items.md) voor meer informatie over de Azure Cosmos-documenten voordat u de volgende voorbeelden uitvoert. 

| Taak | API-verwijzing |
| --- | --- |
| [Items maken](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L18-L21) |[Items.create](/javascript/api/@azure/cosmos/items#create-t--requestoptions-) |
| [Alle items in een container lezen](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L23-L28) |[Items.readAll](/javascript/api/@azure/cosmos/items#readall-feedoptions-) |
| [Een item op id lezen](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L30-L33) |[Item.read](/javascript/api/@azure/cosmos/item#read-requestoptions-) |
| [Item alleen lezen als het is gewijzigd](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L45-L56) |[Item.read](/javascript/api/%40azure/cosmos/item)<br/>[RequestOptions.accessCondition](/javascript/api/%40azure/cosmos/requestoptions#accesscondition) |
| [Een query uitvoeren voor documenten](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L58-L79) |[Items.query](/javascript/api/%40azure/cosmos/items) |
| [Item vervangen](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L81-L96) |[Item.replace](/javascript/api/%40azure/cosmos/item) |
| [Item vervangen door voorwaardelijke ETag-controle](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L98-L135) |[Item.replace](/javascript/api/%40azure/cosmos/item)<br/>[RequestOptions.accessCondition](/javascript/api/%40azure/cosmos/requestoptions#accesscondition) |
| [Item verwijderen](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L137-L140) |[Item.delete](/javascript/api/%40azure/cosmos/item) |

## <a name="indexing-examples"></a>Voorbeelden van indexen

In het [IndexManagement](https://github.com/Azure/azure-cosmos-js/blob/master/samples/IndexManagement.ts)-bestand ziet u hoe u de indexering beheert. Zie de conceptuele artikelen [Indexeringsbeleid](index-policy.md), [Indexeringstypen](index-overview.md#index-types) en [Indexeringspaden](index-policy.md#include-exclude-paths) voor meer informatie over het indexeren in Azure Cosmos DB voordat u de volgende voorbeelden uitvoert. 

| Taak | API-verwijzing |
| --- | --- |
| [Handmatig een specifiek item indexeren](https://github.com/Azure/azure-cosmos-js/blob/master/samples/IndexManagement.ts#L52-L75) |[RequestOptions.indexingDirective: 'include'](/javascript/api/%40azure/cosmos/requestoptions#indexingdirective) |
| [Handmatig een bepaald item uitsluiten van de index](https://github.com/Azure/azure-cosmos-js/blob/master/samples/IndexManagement.ts#L17-L29) |[RequestOptions.indexingDirective: 'exclude'](/javascript/api/%40azure/cosmos/requestoptions#indexingdirective) |
| [Een pad uitsluiten van de index](https://github.com/Azure/azure-cosmos-js/blob/master/samples/IndexManagement.ts#L142-L167) |[IndexingPolicy.ExcludedPath](/javascript/api/%40azure/cosmos/indexingpolicy#excludedpaths) |
| [Een bereikindex maken op een tekenreekspad](https://github.com/Azure/azure-cosmos-js/blob/master/samples/IndexManagement.ts#L87-L112) |[IndexKind.Range](/javascript/api/%40azure/cosmos/indexkind), [IndexingPolicy](/javascript/api/%40azure/cosmos/indexingpolicy), [Items.query](/javascript/api/%40azure/cosmos/items) |
| [Een container maken met standaard indexPolicy en vervolgens online bijwerken](https://github.com/Azure/azure-cosmos-js/blob/master/samples/IndexManagement.ts#L13-L15) |[Containers.create](/javascript/api/%40azure/cosmos/containers)

## <a name="server-side-programming-examples"></a>Voorbeelden van programmering op de server

In het bestand [index.ts](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ServerSideScripts/index.ts) van het project [ServerSideScripts](https://github.com/Azure/azure-cosmos-js/tree/master/samples/ServerSideScripts) ziet u hoe de volgende taken worden uitgevoerd. Zie het conceptuele artikel [Opgeslagen procedures, triggers en door de gebruiker gedefinieerde functies](stored-procedures-triggers-udfs.md)voor meer informatie over het programmeren op de server in Azure Cosmos DB voordat u de volgende voorbeelden uitvoert. 

| Taak | API-verwijzing |
| --- | --- |
| [Een opgeslagen procedure maken](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ServerSideScripts/upsert.js) |[StoredProcedures.create](/javascript/api/%40azure/cosmos/storedprocedures) |
| [Een opgeslagen procedure uitvoeren](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ServerSideScripts/index.ts) |[StoredProcedure.execute](/javascript/api/%40azure/cosmos/storedprocedure) |

Voor meer informatie over programmeren aan de serverzijde ziet u [Programmeren aan de serverzijde van Azure Cosmos DB: opgeslagen procedures, databasetriggers en UDF's](stored-procedures-triggers-udfs.md).
