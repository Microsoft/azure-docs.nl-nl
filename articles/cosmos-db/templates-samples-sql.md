---
title: Azure Resource Manager sjablonen voor Azure Cosmos DB core (SQL-API)
description: Gebruik Azure Resource Manager sjablonen om Azure Cosmos DB te maken en te configureren.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 10/14/2020
ms.author: mjbrown
ms.openlocfilehash: 52fa5957cdbc3ae73ebc4b36e04582741199414a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93340597"
---
# <a name="azure-resource-manager-templates-for-azure-cosmos-db"></a>Azure Resource Manager-sjablonen voor Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

In dit artikel worden alleen Azure Resource Manager sjabloon voorbeelden weer gegeven voor core-(SQL) API-accounts. U kunt ook voor beelden van sjablonen vinden voor [Cassandra](templates-samples-cassandra.md)-, [Gremlin](templates-samples-gremlin.md)-, [MongoDb](templates-samples-mongodb.md)-en [Table](templates-samples-table.md) -api's.

## <a name="core-sql-api"></a>Core (SQL) API

|**Sjabloon**|**Beschrijving**|
|---|---|
|[Een Azure Cosmos-account,-Data Base,-container maken met de door Voer van automatisch schalen](manage-with-templates.md#create-autoscale) | Met deze sjabloon maakt u een core (SQL) API-account in twee regio's, een Data Base en container met de door Voer van automatisch schalen. |
|[Een Azure Cosmos-account, Data Base, container met een analytische opslag maken](manage-with-templates.md#create-analytical-store) | Met deze sjabloon maakt u een core-API-account (SQL) in één regio met een container die is geconfigureerd met de optie voor analyse-TTL ingeschakeld en selecteert u hand matig of automatisch schalen door voer. |
|[Een Azure Cosmos-account, Data Base, container met de standaard doorvoer (hand matig) maken](manage-with-templates.md#create-manual) | Met deze sjabloon maakt u een core (SQL) API-account in twee regio's, een Data Base en container met een standaard doorvoer. |
|[Een Azure Cosmos-account,-data base en-container maken met een opgeslagen procedure, trigger en UDF](manage-with-templates.md#create-sproc) | Met deze sjabloon maakt u een core-API-account (SQL) in twee regio's met een opgeslagen procedure, trigger en UDF voor een container. |
|[Een persoonlijk eind punt maken voor een bestaand Azure Cosmos-account](how-to-configure-private-endpoints.md#create-a-private-endpoint-by-using-a-resource-manager-template) |  Met deze sjabloon maakt u een persoonlijk eind punt voor een bestaand Azure Cosmos core-API-account in een bestaand virtueel netwerk. |
|[Een Azure Cosmos-account met een gratis laag maken](manage-with-templates.md#free-tier) |  Met deze sjabloon wordt een Azure Cosmos DB core-API-account gemaakt op de laag gratis. |

Zie [Azure Resource Manager referentie voor Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions) pagina voor de referentie documentatie.
