---
title: Query's uitvoeren met de API van Azure Cosmos DB voor MongoDB
description: Meer informatie over het opvragen van gegevens uit de API van Azure Cosmos DB voor MongoDB met behulp van MongoDB-shellopdrachten
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: tutorial
ms.date: 12/03/2019
ms.reviewer: sngun
ms.openlocfilehash: f93ec39e7a2e3b5829c0d6205404c6c5c4af6189
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93074315"
---
# <a name="query-data-by-using-azure-cosmos-dbs-api-for-mongodb"></a>Query's uitvoeren met behulp van de API van Azure Cosmos DB voor MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

De [Azure Cosmos DB-API voor MongoDB](mongodb-introduction.md) ondersteunt [MongoDB-query’s](https://docs.mongodb.com/manual/tutorial/query-documents/). 

Dit artikel behandelt de volgende taken: 

> [!div class="checklist"]
> * Query's uitvoeren op gegevens die zijn opgeslagen in uw Cosmos-database met behulp van MongoDB-shell

U kunt aan de slag gaan met behulp van de voorbeelden in dit document en de video [Query’s uitvoeren in Azure Cosmos DB met MongoDB-shell](https://azure.microsoft.com/resources/videos/query-azure-cosmos-db-data-by-using-the-mongodb-shell/) bekijken.

## <a name="sample-document"></a>Voorbeelddocument

In de query's in dit artikel wordt het volgende voorbeelddocument gebruikt.

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
## <a name="example-query-1"></a><a id="examplequery1"></a>Voorbeeldquery 1 

Op basis van het bovenstaand voorbeelddocument van een familie retourneert de volgende query de documenten waarin het id-veld gelijk is aan `WakefieldFamily`.

**Query**

```bash
db.families.find({ id: "WakefieldFamily"})
```

**Results**

```json
{
    "_id": "ObjectId(\"58f65e1198f3a12c7090e68c\")",
    "id": "WakefieldFamily",
    "parents": [
      {
        "familyName": "Wakefield",
        "givenName": "Robin"
      },
      {
        "familyName": "Miller",
        "givenName": "Ben"
      }
    ],
    "children": [
      {
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1,
        "pets": [
          { "givenName": "Goofy" },
          { "givenName": "Shadow" }
        ]
      },
      {
        "familyName": "Miller",
        "givenName": "Lisa",
        "gender": "female",
        "grade": 8
      }
    ],
    "address": {
      "state": "NY",
      "county": "Manhattan",
      "city": "NY"
    },
    "creationDate": 1431620462,
    "isRegistered": false
}
```

## <a name="example-query-2"></a><a id="examplequery2"></a>Voorbeeldquery 2 

De volgende query retourneert alle kinderen in het gezin. 

**Query**

```bash 
db.families.find( { id: "WakefieldFamily" }, { children: true } )
``` 

**Results**

```json
{
    "_id": "ObjectId("58f65e1198f3a12c7090e68c")",
    "children": [
      {
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1,
        "pets": [
          { "givenName": "Goofy" },
          { "givenName": "Shadow" }
        ]
      },
      {
        "familyName": "Miller",
        "givenName": "Lisa",
        "gender": "female",
        "grade": 8
      }
    ]
}
```

## <a name="example-query-3"></a><a id="examplequery3"></a>Voorbeeldquery 3 

De volgende query retourneert alle geregistreerde gezinnen. 

**Query**

```bash
db.families.find( { "isRegistered" : true })
``` 

**Results**

Er wordt geen document geretourneerd. 

## <a name="example-query-4"></a><a id="examplequery4"></a>Voorbeeldquery 4

De volgende query retourneert alle niet-geregistreerde gezinnen. 

**Query**

```bash
db.families.find( { "isRegistered" : false })
``` 

**Results**

```json
{
    "_id": ObjectId("58f65e1198f3a12c7090e68c"),
    "id": "WakefieldFamily",
    "parents": [{
        "familyName": "Wakefield",
        "givenName": "Robin"
    }, {
        "familyName": "Miller",
        "givenName": "Ben"
    }],
    "children": [{
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1,
        "pets": [{
            "givenName": "Goofy"
        }, {
            "givenName": "Shadow"
        }]
    }, {
        "familyName": "Miller",
        "givenName": "Lisa",
        "gender": "female",
        "grade": 8
    }],
    "address": {
        "state": "NY",
        "county": "Manhattan",
        "city": "NY"
    },
    "creationDate": 1431620462,
    "isRegistered": false
}
```

## <a name="example-query-5"></a><a id="examplequery5"></a>Voorbeeldquery 5

De volgende query retourneert alle niet-geregistreerde gezinnen in de staat New York (NY). 

**Query**

```bash
db.families.find( { "isRegistered" : false, "address.state" : "NY" })
``` 

**Results**

```json
{
    "_id": ObjectId("58f65e1198f3a12c7090e68c"),
    "id": "WakefieldFamily",
    "parents": [{
        "familyName": "Wakefield",
        "givenName": "Robin"
    }, {
        "familyName": "Miller",
        "givenName": "Ben"
    }],
    "children": [{
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1,
        "pets": [{
            "givenName": "Goofy"
        }, {
            "givenName": "Shadow"
        }]
    }, {
        "familyName": "Miller",
        "givenName": "Lisa",
        "gender": "female",
        "grade": 8
    }],
    "address": {
        "state": "NY",
        "county": "Manhattan",
        "city": "NY"
    },
    "creationDate": 1431620462,
    "isRegistered": false
}
```

## <a name="example-query-6"></a><a id="examplequery6"></a>Voorbeeldquery 6

De volgende query retourneert alle gezinnen met kinderen in grade 8 (2e klas van de middelbare school).

**Query**

```bash
db.families.find( { children : { $elemMatch: { grade : 8 }} } )
```

**Results**

```json
{
    "_id": ObjectId("58f65e1198f3a12c7090e68c"),
    "id": "WakefieldFamily",
    "parents": [{
        "familyName": "Wakefield",
        "givenName": "Robin"
    }, {
        "familyName": "Miller",
        "givenName": "Ben"
    }],
    "children": [{
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1,
        "pets": [{
            "givenName": "Goofy"
        }, {
            "givenName": "Shadow"
        }]
    }, {
        "familyName": "Miller",
        "givenName": "Lisa",
        "gender": "female",
        "grade": 8
    }],
    "address": {
        "state": "NY",
        "county": "Manhattan",
        "city": "NY"
    },
    "creationDate": 1431620462,
    "isRegistered": false
}
```

## <a name="example-query-7"></a><a id="examplequery7"></a>Voorbeeldquery 7

De volgende query retourneert alle gezinnen waar de grootte van de array children (kinderen) 3 is.

**Query**

```bash
db.Family.find( {children: { $size:3} } )
```

**Results**

Er worden geen resultaten geretourneerd als er geen gezinnen zijn met meer dan twee kinderen. Alleen wanneer de parameter 2 is slaagt deze query en wordt het volledige document geretourneerd.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende gedaan:

> [!div class="checklist"]
> * U hebt geleerd hoe u query's kunt uitvoeren met behulp van de Cosmos DB-API voor MongoDB

U kunt nu doorgaan met de volgende zelfstudie, waarin u leert hoe u uw gegevens globaal distribueert.

> [!div class="nextstepaction"]
> [Uw gegevens globaal distribueren](tutorial-global-distribution-sql-api.md)

