---
title: Zelfstudie over het instellen van wereldwijde distributie met de API van Azure Cosmos DB voor MongoDB
description: Lees hoe u wereldwijde distributie kunt instellen met behulp van de API van Azure Cosmos DB voor MongoDB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: tutorial
ms.date: 12/26/2018
ms.reviewer: sngun
ms.custom: devx-track-csharp
ms.openlocfilehash: 89826eab7b1686ae695a2716a03b2f5d03da277f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93099247"
---
# <a name="set-up-global-distributed-database-using-azure-cosmos-dbs-api-for-mongodb"></a>Wereldwijd gedistribueerde database instellen met behulp van de API van Azure Cosmos DB voor MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

In dit artikel laten we zien hoe u de Azure-portal kunt gebruiken om een wereldwijd gedistribueerde database in te stellen en er verbinding mee te maken met behulp van de API van Azure Cosmos DB voor MongoDB.

Dit artikel behandelt de volgende taken: 

> [!div class="checklist"]
> * Wereldwijde distributie configureren met behulp van Azure Portal
> * Globale distributie configureren met behulp van de [API van Azure Cosmos DB voor MongoDB](mongodb-introduction.md)

[!INCLUDE [cosmos-db-tutorial-global-distribution-portal](../../includes/cosmos-db-tutorial-global-distribution-portal.md)]

## <a name="verifying-your-regional-setup"></a>Regionale instellingen verifiëren 
Een eenvoudige manier om uw globale configuratie te controleren met de API van Cosmos DB voor MongoDB is het uitvoeren van de opdracht *isMaster()* vanuit de Mongo-shell.

Vanuit de Mongo-shell:

   ```
      db.isMaster()
   ```
   
Voorbeeldresultaten:

   ```JSON
      {
         "_t": "IsMasterResponse",
         "ok": 1,
         "ismaster": true,
         "maxMessageSizeBytes": 4194304,
         "maxWriteBatchSize": 1000,
         "minWireVersion": 0,
         "maxWireVersion": 2,
         "tags": {
            "region": "South India"
         },
         "hosts": [
            "vishi-api-for-mongodb-southcentralus.documents.azure.com:10255",
            "vishi-api-for-mongodb-westeurope.documents.azure.com:10255",
            "vishi-api-for-mongodb-southindia.documents.azure.com:10255"
         ],
         "setName": "globaldb",
         "setVersion": 1,
         "primary": "vishi-api-for-mongodb-southindia.documents.azure.com:10255",
         "me": "vishi-api-for-mongodb-southindia.documents.azure.com:10255"
      }
   ```

## <a name="connecting-to-a-preferred-region"></a>Verbinding maken met een voorkeursregio 

Met de API van Azure Cosmos DB voor MongoDB kunt u de leesvoorkeur van uw verzameling opgeven voor een globaal gedistribueerde database. Voor zowel leesbewerkingen met lage latentie als globale hoge beschikbaarheid raden wij u aan de leesvoorkeur van uw verzameling in te stellen op *dichtstbijzijnde*. De leesvoorkeur *dichtstbijzijnde* is geconfigureerd voor het lezen van de dichtstbijzijnde regio.

```csharp
var collection = database.GetCollection<BsonDocument>(collectionName);
collection = collection.WithReadPreference(new ReadPreference(ReadPreferenceMode.Nearest));
```

Voor toepassingen met een primaire lees-/schrijfregio en een secundaire regio voor noodherstel (DR) scenario's, wordt aangeraden de leesvoorkeur van uw verzameling in te stellen op *secundaire voorkeur*. De leesvoorkeur *secundaire voorkeur* is geconfigureerd om uit de secundaire regio te lezen wanneer de primaire regio niet beschikbaar is.

```csharp
var collection = database.GetCollection<BsonDocument>(collectionName);
collection = collection.WithReadPreference(new ReadPreference(ReadPreferenceMode.SecondaryPreferred));
```

Tot slot wilt u mogelijk uw leesregio’s handmatig opgeven. U kunt de regiotag instellen in uw leesvoorkeur.

```csharp
var collection = database.GetCollection<BsonDocument>(collectionName);
var tag = new Tag("region", "Southeast Asia");
collection = collection.WithReadPreference(new ReadPreference(ReadPreferenceMode.Secondary, new[] { new TagSet(new[] { tag }) }));
```

En daarmee is deze zelfstudie voltooid. Informatie over het beheren van de consistentie van uw wereldwijd gerepliceerde account kunt u vinden in [Consistentieniveaus in Azure Cosmos DB](consistency-levels.md). En voor meer informatie over hoe wereldwijde databasereplicatie werkt in Azure Cosmos DB, gaat u naar [Gegevens wereldwijd distribueren met Azure Cosmos DB](distribute-data-globally.md).

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende gedaan:

> [!div class="checklist"]
> * Wereldwijde distributie configureren met behulp van Azure Portal
> * Globale distributie configureren met behulp van de API van Cosmos DB voor MongoDB

U kunt nu doorgaan met de volgende zelfstudie voor informatie over lokaal ontwikkelen met behulp van de lokale Azure Cosmos DB-emulator.

> [!div class="nextstepaction"]
> [Lokaal ontwikkelen met de Azure Cosmos DB-emulator](local-emulator.md)
