---
title: Multi-document transacties gebruiken in Azure Cosmos DB-API voor MongoDB
description: Meer informatie over het maken van een voor beeld van een Mongo-shell-app die een multi-document transactie (alle-of-Nothing-semantiek) kan uitvoeren op een vaste verzameling in Azure Cosmos DB-API voor MongoDB 4,0.
author: gahl-levy
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 03/02/2021
ms.author: gahllevy
ms.openlocfilehash: 4d7dcc829f25b7f1b7c6cb6b1d13a664d301bfe6
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101662672"
---
# <a name="use-multi-document-transactions-in-azure-cosmos-db-api-for-mongodb"></a>Multi-document transacties gebruiken in Azure Cosmos DB-API voor MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

In dit artikel maakt u een Mongo-shell-app die een multi-document transactie uitvoert op een vaste verzameling in Azure Cosmos DB-API voor MongoDB met Server versie 4,0.

## <a name="what-are-multi-document-transactions"></a>Wat zijn trans acties voor meerdere documenten?

In Azure Cosmos DB-API voor MongoDB zijn bewerkingen op één document Atomic. Met multi-document transacties kunnen toepassingen atomische bewerkingen uitvoeren op meerdere documenten. Het biedt de semantiek ' alles-of-Nothing ' voor de bewerkingen. Bij het door voeren worden de wijzigingen die zijn aangebracht in de trans acties persistent gemaakt en als de trans actie mislukt, worden alle wijzigingen in de trans actie genegeerd.

Multi-document transacties volgen **zure** semantiek:

* Atomiciteit: alle bewerkingen die als één worden behandeld
* Consistentie: gegevens doorgevoerd is geldig
* Isolatie: geïsoleerd van andere bewerkingen
* Duurzaamheid: transactie gegevens worden bewaard wanneer de client dit doet

## <a name="requirements"></a>Vereisten

Multi-document transacties worden ondersteund in een unsharded-verzameling in versie 4,0. Multi-document transacties worden niet ondersteund in verzamelingen of in Shard-verzamelingen.

Alle Stuur Programma's die ondersteuning bieden voor wire-protocol versie 4,0 of hoger, ondersteunen Azure Cosmos DB-API voor MongoDB multi-document transacties.

## <a name="run-multi-document-transactions-in-mongodb-shell"></a>Multi-document transacties uitvoeren in de MongoDB-shell

1. Open een opdracht prompt en ga naar de map waarin Mongo shell versie 4,0 en hoger is geïnstalleerd:

   ```powershell
   cd <path_to_mongo_shell_>
   ```

2. Maak een Mongo-shell script *connect_friends.js* en voeg de volgende inhoud toe

   ```javascript
   // insert data into friends collection
   db.getMongo().getDB("users").friends.insert({name:"Tom"})
   db.getMongo().getDB("users").friends.insert({name:"Mike"})
   // start transaction
   var session = db.getMongo().startSession();
   var friendsCollection = session.getDatabase("users").friends;
   session.startTransaction();
   // operations in transaction
   try {
       friendsCollection.updateOne({ name: "Tom" }, { $set: { friendOf: "Mike" } } );
       friendsCollection.updateOne({ name: "Mike" }, { $set: { friendOf: "Tom" } } );
   } catch (error) {
       // abort transaction on error
       session.abortTransaction();
       throw error;
   }

    // commit transaction
    session.commitTransaction();

    ```

3. Voer de volgende opdracht uit om de trans actie met meerdere documenten uit te voeren. De host, poort, gebruiker en sleutel vindt u in de Azure Portal.

   ```powershell
   mongo "<HOST>:<PORT>" -u "<USER>" -p "KEY" --ssl connect_friends.js
   ```

## <a name="next-steps"></a>Volgende stappen

Ontdek wat er nieuw is in [Azure Cosmos DB-API voor MongoDB 4,0](mongodb-feature-support-40.md)
