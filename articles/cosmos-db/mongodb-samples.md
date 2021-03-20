---
title: De API voor MongoDB van Azure Cosmos DB gebruiken om een Node.js-app te bouwen
description: Een zelfstudie waarmee u een onlinedatabase maakt met de API voor MongoDB van Azure Cosmos DB.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: sample
ms.date: 12/26/2018
author: sivethe
ms.author: sivethe
ms.custom: devx-track-js
ms.openlocfilehash: c2117c535cca679d3a7a3b75491bf60ab9567ceb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93098209"
---
# <a name="build-an-app-using-nodejs-and-azure-cosmos-dbs-api-for-mongodb"></a>Een app bouwen met behulp van Node.js en de API voor MongoDB van Azure Cosmos DB 
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [.NET Core](./sql-api-get-started.md)
> * [Java](./create-sql-api-java.md)
> * [Node.js voor MongoDB](mongodb-samples.md)
> * [Node.js](sql-api-nodejs-get-started.md)
>

In dit voorbeeld ziet u hoe u een console-app bouwt met behulp van Node.js en de API voor MongoDB van Azure Cosmos DB.

Als u dit voorbeeld wilt gebruiken, moet u het volgende doen:

* [Maak](create-mongodb-dotnet.md#create-account) een Cosmos-account dat is geconfigureerd voor gebruik van de API voor MongoDB van Azure Cosmos DB.
* Haal de gegevens voor uw [verbindingsreeks](connect-mongodb-account.md) op.

## <a name="create-the-app"></a>De app maken

1. Maak een *app.js*-bestand en kopieer en plak de onderstaande code.

    ```javascript
    var MongoClient = require('mongodb').MongoClient;
    var assert = require('assert');
    var ObjectId = require('mongodb').ObjectID;
    var url = 'mongodb://<username>:<password>@<endpoint>.documents.azure.com:10255/?ssl=true';

    var insertDocument = function(db, callback) {
    db.collection('families').insertOne( {
            "id": "AndersenFamily",
            "lastName": "Andersen",
            "parents": [
                { "firstName": "Thomas" },
                { "firstName": "Mary Kay" }
            ],
            "children": [
                { "firstName": "John", "gender": "male", "grade": 7 }
            ],
            "pets": [
                { "givenName": "Fluffy" }
            ],
            "address": { "country": "USA", "state": "WA", "city": "Seattle" }
        }, function(err, result) {
        assert.equal(err, null);
        console.log("Inserted a document into the families collection.");
        callback();
    });
    };
    
    var findFamilies = function(db, callback) {
    var cursor =db.collection('families').find( );
    cursor.each(function(err, doc) {
        assert.equal(err, null);
        if (doc != null) {
            console.dir(doc);
        } else {
            callback();
        }
    });
    };
    
    var updateFamilies = function(db, callback) {
    db.collection('families').updateOne(
        { "lastName" : "Andersen" },
        {
            $set: { "pets": [
                { "givenName": "Fluffy" },
                { "givenName": "Rocky"}
            ] },
            $currentDate: { "lastModified": true }
        }, function(err, results) {
        console.log(results);
        callback();
    });
    };
    
    var removeFamilies = function(db, callback) {
    db.collection('families').deleteMany(
        { "lastName": "Andersen" },
        function(err, results) {
            console.log(results);
            callback();
        }
    );
    };
    
    MongoClient.connect(url, function(err, client) {
    assert.equal(null, err);
    var db = client.db('familiesdb');
    insertDocument(db, function() {
        findFamilies(db, function() {
        updateFamilies(db, function() {
            removeFamilies(db, function() {
                client.close();
            });
        });
        });
    });
    });
    ```
    
    **Optioneel**: Als u het **MongoDB Node.js 2.2-stuurprogramma** gebruikt, vervangt u het volgende codefragment:

    Oorspronkelijk:

    ```javascript
    MongoClient.connect(url, function(err, client) {
    assert.equal(null, err);
    var db = client.db('familiesdb');
    insertDocument(db, function() {
        findFamilies(db, function() {
        updateFamilies(db, function() {
            removeFamilies(db, function() {
                client.close();
            });
        });
        });
    });
    });
    ```
    
    Moet worden vervangen door:

    ```javascript
    MongoClient.connect(url, function(err, db) {
    assert.equal(null, err);
    insertDocument(db, function() {
        findFamilies(db, function() {
        updateFamilies(db, function() {
            removeFamilies(db, function() {
                db.close();
            });
        });
        });
    });
    });
    ```
    
2. Wijzig de volgende variabelen in het bestand *app.js* volgens uw accountinstellingen (meer informatie over het zoeken naar uw [verbindingsreeks](connect-mongodb-account.md)):

    > [!IMPORTANT]
    > Voor het **MongoDB Node.js 3.0-stuurprogramma** is codering vereist van speciale tekens in het Cosmos DB-wachtwoord. Zorg ervoor dat u de tekens '=' codeert als %3D
    >
    > Voorbeeld: Het wachtwoord *jm1HbNdLg5zxEuyD86ajvINRFrFCUX0bIWP15ATK3BvSv==* wordt gecodeerd naar *jm1HbNdLg5zxEuyD86ajvINRFrFCUX0bIWP15ATK3BvSv%3D%3D*
    >
    > Voor het **MongoDB Node.js 2.2-stuurprogramma** is geen codering vereist van speciale tekens in het Cosmos DB-wachtwoord.
    >
    >
   
    ```javascript
    var url = 'mongodb://<endpoint>:<password>@<endpoint>.documents.azure.com:10255/?ssl=true';
    ```
     
3. Open uw favoriete terminal, voer **npm install mongodb --save** uit en voer uw app uit met **node app.js**

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [gebruik van Studio 3T](mongodb-mongochef.md) met de API voor MongoDB van Azure Cosmos DB.
- Meer informatie over het [gebruik van Robo 3T](mongodb-robomongo.md) met de API voor MongoDB van Azure Cosmos DB.
- Verken [voorbeelden](mongodb-samples.md) van MongoDB met de API van Azure Cosmos DB voor MongoDB.