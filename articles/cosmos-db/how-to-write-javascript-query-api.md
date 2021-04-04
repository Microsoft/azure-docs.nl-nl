---
title: Opgeslagen procedures en triggers schrijven met behulp van de Java script-query-API in Azure Cosmos DB
description: Leer opgeslagen procedures en triggers schrijven met de JavaScript-query-API in Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 05/07/2020
ms.author: tisande
ms.custom: devx-track-js
ms.openlocfilehash: 449af63ef3409b8c8173edef7a5d77074cca02b9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93335837"
---
# <a name="how-to-write-stored-procedures-and-triggers-in-azure-cosmos-db-by-using-the-javascript-query-api"></a>Opgeslagen procedures en triggers schrijven in Azure Cosmos DB met de JavaScript-query-API
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

In Azure Cosmos DB kunt u geoptimaliseerde query's uitvoeren door gebruik te maken van een gestroomlijnde JavaScript-interface zonder dat u SQL hoeft te kennen om opgeslagen procedures of triggers te kunnen schrijven. Zie het artikel [Werken met de geïntegreerde query-API voor JavaScript in Azure Cosmos DB](javascript-query-api.md) voor meer informatie over ondersteuning van de JavaScript-query-API in Azure Cosmos DB.

## <a name="stored-procedure-using-the-javascript-query-api"></a><a id="stored-procedures"></a>Opgeslagen procedure met behulp van de JavaScript-query-API

Het volgende codevoorbeeld geeft aan hoe de JavaScript-query-API wordt gebruikt in de context van een opgeslagen procedure. Met de opgeslagen procedure wordt een Azure Cosmos-item ingevoegd dat is opgegeven door een invoer parameter en wordt een meta gegevens document bijgewerkt met behulp van de `__.filter()` methode, met minSize, maxSize en totalSize op basis van de eigenschap Size van het invoer item.

> [!NOTE]
> `__` (dubbele onderstreping) is een alias naar `getContext().getCollection()` bij gebruik van de JavaScript-query-API.

```javascript
/**
 * Insert an item and update metadata doc: minSize, maxSize, totalSize based on item.size.
 */
function insertDocumentAndUpdateMetadata(item) {
  // HTTP error codes sent to our callback function by CosmosDB server.
  var ErrorCode = {
    RETRY_WITH: 449,
  }

  var isAccepted = __.createDocument(__.getSelfLink(), item, {}, function(err, item, options) {
    if (err) throw err;

    // Check the item (ignore items with invalid/zero size and metadata itself) and call updateMetadata.
    if (!item.isMetadata && item.size > 0) {
      // Get the metadata. We keep it in the same container. it's the only item that has .isMetadata = true.
      var result = __.filter(function(x) {
        return x.isMetadata === true
      }, function(err, feed, options) {
        if (err) throw err;

        // We assume that metadata item was pre-created and must exist when this script is called.
        if (!feed || !feed.length) throw new Error("Failed to find the metadata item.");

        // The metadata item.
        var metaItem = feed[0];

        // Update metaDoc.minSize:
        // for 1st document use doc.Size, for all the rest see if it's less than last min.
        if (metaItem.minSize == 0) metaItem.minSize = item.size;
        else metaItem.minSize = Math.min(metaItem.minSize, item.size);

        // Update metaItem.maxSize.
        metaItem.maxSize = Math.max(metaItem.maxSize, item.size);

        // Update metaItem.totalSize.
        metaItem.totalSize += item.size;

        // Update/replace the metadata item in the store.
        var isAccepted = __.replaceDocument(metaItem._self, metaItem, function(err) {
          if (err) throw err;
          // Note: in case concurrent updates causes conflict with ErrorCode.RETRY_WITH, we can't read the meta again
          //       and update again because due to Snapshot isolation we will read same exact version (we are in same transaction).
          //       We have to take care of that on the client side.
        });
        if (!isAccepted) throw new Error("replaceDocument(metaItem) returned false.");
      });
      if (!result.isAccepted) throw new Error("filter for metaItem returned false.");
    }
  });
  if (!isAccepted) throw new Error("createDocument(actual item) returned false.");
}
```

## <a name="next-steps"></a>Volgende stappen

Zie de volgende artikelen voor informatie over opgeslagen procedures, triggers en door de gebruiker gedefinieerde functies in Azure Cosmos DB:

* [How to work with stored procedures, triggers, user-defined functions in Azure Cosmos DB](how-to-use-stored-procedures-triggers-udfs.md) (Werken met opgeslagen procedures, triggers en door de gebruiker gedefinieerde functies in Azure Cosmos DB)

* [How to register and use stored procedures in Azure Cosmos DB](how-to-use-stored-procedures-triggers-udfs.md#stored-procedures) (Opgeslagen procedures registreren en gebruiken in Azure Cosmos DB)

* How to register and use [pre-triggers](how-to-use-stored-procedures-triggers-udfs.md#pre-triggers) and [post-triggers](how-to-use-stored-procedures-triggers-udfs.md#post-triggers) in Azure Cosmos DB (Voorafgaande triggers en navolgende triggers registreren en gebruiken in Azure Cosmos DB)

* [How to register and use user-defined functions in Azure Cosmos DB](how-to-use-stored-procedures-triggers-udfs.md#udfs) (Door de gebruiker gedefinieerde functies registreren in Azure Cosmos DB)

* [Synthetic partition keys in Azure Cosmos DB](synthetic-partition-keys.md) (Synthetische partitiesleutels in Azure Cosmos DB)
