---
title: "Zelfstudie: Query's uitvoeren met SQL in Azure Cosmos DB?"
description: "Zelfstudie: Meer informatie over het uitvoeren van query's met SQL-query's in Azure Cosmos DB met behulp van de query Playground"
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.custom: tutorial-develop, mvc
ms.topic: tutorial
ms.date: 11/05/2019
ms.reviewer: sngun
ms.openlocfilehash: c206b68495b09988e49f37433ccd5616acd29cb7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93337775"
---
# <a name="tutorial-query-azure-cosmos-db-by-using-the-sql-api"></a>Zelfstudie: Query's uitvoeren in Azure Cosmos DB met behulp van de SQL-API
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

De [SQL-API](./introduction.md) van Azure Cosmos DB ondersteunt het opvragen van documenten met behulp van SQL. Dit artikel bevat een voorbeelddocument en twee voorbeelden van SQL-query's en de resultaten daarvan.

Dit artikel behandelt de volgende taken: 

> [!div class="checklist"]
> * Gegevens opvragen met behulp van SQL

## <a name="sample-document"></a>Voorbeelddocument

In de SQL-query's in dit artikel wordt het volgende voorbeelddocument gebruikt.

```json
{
  "id": "WakefieldFamily",
  "parents": [
      { "familyName": "Wakefield", "givenName": "Robin" },
      { "familyName": "Miller", "givenName": "Ben" }
  ],
  "children": [
      {
        "familyName": "Merriam", 
        "givenName": "Jesse", 
        "gender": "female", "grade": 1,
        "pets": [
            { "givenName": "Goofy" },
            { "givenName": "Shadow" }
        ]
      },
      { 
        "familyName": "Miller", 
         "givenName": "Lisa", 
         "gender": "female", 
         "grade": 8 }
  ],
  "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
  "creationDate": 1431620462,
  "isRegistered": false
}
```

## <a name="where-can-i-run-sql-queries"></a>Waar kan ik SQL-query's uitvoeren?

U kunt query's uitvoeren met de Data Explorer in de Azure-portal, via de [REST API en SDK's](sql-api-sdk-dotnet.md) en zelfs de [query-speelplaats](https://www.documentdb.com/sql/demo), waar query's worden uitgevoerd voor een bestaande reeks voorbeeldgegevens.

Meer informatie over SQL-query's vindt u in:
* [SQL-query en SQL-syntaxis](sql-query-getting-started.md)

## <a name="prerequisites"></a>Vereisten

In deze zelfstudie wordt ervan uitgegaan dat u een Azure Cosmos DB-account en een verzameling hebt. Hebt u geen van deze resources? Voltooi de [snelstart van 5 minuten](create-cosmosdb-resources-portal.md).

## <a name="example-query-1"></a>Voorbeeldquery 1

Op basis van het bovenstaand voorbeelddocument van een familie retourneert de volgende SQL-query de documenten waarin het id-veld gelijk is aan `WakefieldFamily`. Omdat het een `SELECT *`-instructie is, is de uitvoer van de query het volledige JSON-document:

**Query**

```sql
    SELECT * 
    FROM Families f 
    WHERE f.id = "WakefieldFamily"
```

**Results**

```json
{
  "id": "WakefieldFamily",
  "parents": [
      { "familyName": "Wakefield", "givenName": "Robin" },
      { "familyName": "Miller", "givenName": "Ben" }
  ],
  "children": [
      {
        "familyName": "Merriam", 
        "givenName": "Jesse", 
        "gender": "female", "grade": 1,
        "pets": [
            { "givenName": "Goofy" },
            { "givenName": "Shadow" }
        ]
      },
      { 
        "familyName": "Miller", 
         "givenName": "Lisa", 
         "gender": "female", 
         "grade": 8 }
  ],
  "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
  "creationDate": 1431620462,
  "isRegistered": false
}
```

## <a name="example-query-2"></a>Voorbeeldquery 2

In de volgende query worden alle opgegeven namen geretourneerd van kinderen in de familie van wie de id overeenkomt met `WakefieldFamily`.

**Query**

```sql
    SELECT c.givenName 
    FROM Families f 
    JOIN c IN f.children 
    WHERE f.id = 'WakefieldFamily'
```

**Results**

```
[
    {
        "givenName": "Jesse"
    },
    {
        "givenName": "Lisa"
    }
]
```


## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende gedaan:

> [!div class="checklist"]
> * U hebt geleerd hoe u een query uitvoert met behulp van SQL  

U kunt nu doorgaan met de volgende zelfstudie, waarin u leert hoe u uw gegevens globaal distribueert.

> [!div class="nextstepaction"]
> [Uw gegevens globaal distribueren](tutorial-global-distribution-sql-api.md)