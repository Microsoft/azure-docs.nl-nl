---
title: Resource Manager-sjablonen voor Azure Cosmos DB Table-API
description: Gebruik Azure Resource Manager sjablonen om Azure Cosmos DB Table-API te maken en te configureren.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: how-to
ms.date: 05/19/2020
ms.author: mjbrown
ms.openlocfilehash: e8fb47cc54d520d44085c8d007a4dfe92972d1d4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93340580"
---
# <a name="manage-azure-cosmos-db-table-api-resources-using-azure-resource-manager-templates"></a>Azure Cosmos DB Table-API resources beheren met behulp van Azure Resource Manager sjablonen
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

In dit artikel leert u hoe u Azure Resource Manager sjablonen kunt gebruiken om uw Azure Cosmos DB accounts, data bases en containers te implementeren en beheren.

In dit artikel vindt u voor beelden voor Table-API accounts, om voor beelden te vinden voor andere typen API-accounts raadpleegt u: Azure Resource Manager sjablonen gebruiken met de API van Azure Cosmos DB voor [Cassandra](./templates-samples-cassandra.md), [Gremlin](./templates-samples-gremlin.md), [MongoDb](./templates-samples-mongodb.md)en [SQL](./manage-with-templates.md) -artikelen.

> [!IMPORTANT]
>
> * Account namen zijn beperkt tot 44 tekens, alle kleine letters.
> * Als u de doorvoer waarden wilt wijzigen, implementeert u de sjabloon opnieuw met de bijgewerkte RU/s.
> * Wanneer u locaties toevoegt aan of verwijdert uit een Azure Cosmos-account, kunt u niet tegelijkertijd andere eigenschappen wijzigen. Deze bewerkingen moeten afzonderlijk worden uitgevoerd.

Als u een van de onderstaande Azure Cosmos DB resources wilt maken, kopieert u de volgende voorbeeld sjabloon naar een nieuw JSON-bestand. U kunt optioneel een JSON-bestand voor para meters maken dat moet worden gebruikt bij het implementeren van meerdere exemplaren van dezelfde resource met verschillende namen en waarden. Er zijn veel manieren om Azure Resource Manager sjablonen te implementeren, waaronder, [Azure Portal](../azure-resource-manager/templates/deploy-portal.md), [Azure cli](../azure-resource-manager/templates/deploy-cli.md), [Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md) en [github](../azure-resource-manager/templates/deploy-to-azure-button.md).

> [!TIP]
> Als u gedeelde door voer wilt inschakelen wanneer u Table-API gebruikt, schakelt u door Voer op account niveau in het Azure Portal.

<a id="create-autoscale"></a>

## <a name="azure-cosmos-account-for-table-with-autoscale-throughput"></a>Azure Cosmos-account voor tabel met door Voer automatisch schalen

Met deze sjabloon wordt een Azure Cosmos-account gemaakt voor Table-API met één tabel met de door Voer van automatisch schalen. Deze sjabloon is ook beschikbaar voor één klik op implementeren vanuit de Azure Quick Start-sjablonen galerie.

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Implementeren in Azure":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-table-autoscale%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-table-autoscale/azuredeploy.json":::

<a id="create-manual"></a>

## <a name="azure-cosmos-account-for-table-with-standard-provisioned-throughput"></a>Azure Cosmos-account voor tabel met standaard ingerichte door Voer

Met deze sjabloon wordt een Azure Cosmos-account gemaakt voor Table-API met een tabel met een standaard doorvoer snelheid. Deze sjabloon is ook beschikbaar voor één klik op implementeren vanuit de Azure Quick Start-sjablonen galerie.

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Implementeren in Azure":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-table%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-table/azuredeploy.json":::

## <a name="next-steps"></a>Volgende stappen

Hier volgen enkele aanvullende bronnen:

* [Documentatie voor Azure Resource Manager](../azure-resource-manager/index.yml)
* [Resource provider-schema Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)
* [Quick Start-sjablonen Azure Cosmos DB](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
* [Veelvoorkomende fouten bij Azure Resource Manager implementatie oplossen](../azure-resource-manager/templates/common-deployment-errors.md)