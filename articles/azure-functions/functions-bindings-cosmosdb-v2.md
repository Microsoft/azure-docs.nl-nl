---
title: Azure Cosmos DB bindingen voor de functies 2. xD en hoger
description: Meer informatie over het gebruik van Azure Cosmos DB triggers en bindingen in Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/24/2017
ms.author: cshoe
ms.openlocfilehash: dde3b95c7997a7f742a9d48a964f4275169f6e9c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92106923"
---
# <a name="azure-cosmos-db-trigger-and-bindings-for-azure-functions-2x-and-higher-overview"></a>Azure Cosmos DB trigger en bindingen voor Azure Functions 2. x-en hoger-overzicht

> [!div class="op_single_selector" title1="Selecteer de versie van de Azure Functions runtime die u gebruikt: "]
> * [Versie 1:](functions-bindings-cosmosdb.md)
> * [Versie 2 en hoger](functions-bindings-cosmosdb-v2.md)

In deze reeks artikelen wordt uitgelegd hoe u [Azure Cosmos DB](../cosmos-db/serverless-computing-database.md) bindingen in azure functions 2. x en hoger kunt gebruiken. Azure Functions ondersteunt trigger-, invoer-en uitvoer bindingen voor Azure Cosmos DB.

| Bewerking | Type |
|---------|---------|
| Een functie uitvoeren wanneer een Azure Cosmos DB-document wordt gemaakt of gewijzigd | [Trigger](./functions-bindings-cosmosdb-v2-trigger.md) |
| Een Azure Cosmos DB document lezen | [Invoer binding](./functions-bindings-cosmosdb-v2-input.md) |
| Wijzigingen in een Azure Cosmos DB document opslaan  |[Uitvoer binding](./functions-bindings-cosmosdb-v2-output.md) |

> [!NOTE]
> Deze verwijzing is voor [Azure functions versie 2. x of hoger](functions-versions.md).  Zie [Azure Cosmos DB bindingen voor Azure functions 1. x](functions-bindings-cosmosdb.md)voor informatie over het gebruik van deze bindingen in functions 1. x.
>
> Deze binding heeft oorspronkelijk de naam DocumentDB. In functies versie 2. x en hoger zijn de trigger, bindingen en pakket de naam Cosmos DB.

## <a name="supported-apis"></a>Ondersteunde API's

[!INCLUDE [SQL API support only](../../includes/functions-cosmosdb-sqlapi-note.md)]

## <a name="add-to-your-functions-app"></a>Toevoegen aan uw functions-app

### <a name="functions-2x-and-higher"></a>Functions 2.x en hoger

Voor het werken met de trigger en bindingen moet u verwijzen naar het juiste pakket. Het NuGet-pakket wordt gebruikt voor .NET-klassen bibliotheken terwijl de uitbreidings bundel wordt gebruikt voor alle andere toepassings typen.

| Taal                                        | Toevoegen door...                                   | Opmerkingen 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | Het [NuGet-pakket]installeren, versie 3. x | |
| C#-script, Java, java script, Python, Power shell | De [uitbreidings bundel] registreren          | De [extensie voor Azure-Hulpprogram ma's] wordt aanbevolen voor gebruik met Visual Studio code. |
| C#-script (alleen online in Azure Portal)         | Een binding toevoegen                            | Zie [uw extensies bijwerken]om bestaande bindings extensies bij te werken zonder uw functie-app opnieuw te publiceren. |

[NuGet-pakket]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB
[core tools]: ./functions-run-local.md
[uitbreidings bundel]: ./functions-bindings-register.md#extension-bundles
[Uw extensies bijwerken]: ./functions-bindings-register.md
[Extensie van Azure-Hulpprogram Ma's]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions 1.x

Functions 1. x apps hebben automatisch een verwijzing naar het pakket [micro soft. Azure. webjobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) , versie 2. x.

## <a name="next-steps"></a>Volgende stappen

- [Een functie uitvoeren wanneer een Azure Cosmos DB-document wordt gemaakt of gewijzigd (trigger)](./functions-bindings-cosmosdb-v2-trigger.md)
- [Een Azure Cosmos DB document lezen (invoer binding)](./functions-bindings-cosmosdb-v2-input.md)
- [Wijzigingen opslaan in een Azure Cosmos DB-document (uitvoer binding)](./functions-bindings-cosmosdb-v2-output.md)