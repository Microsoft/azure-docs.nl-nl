---
title: Lees voorkeur gebruiken met de API van het Azure Cosmos DB voor MongoDB
description: Meer informatie over het gebruik van MongoDB read voor keur met de API van Azure Cosmos DB voor MongoDB
author: sivethe
ms.author: sivethe
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: how-to
ms.date: 02/26/2019
ms.custom: devx-track-js
ms.openlocfilehash: 3c78ad6605e927015d35df12cadf0347dd0337cf
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96349041"
---
# <a name="how-to-globally-distribute-reads-using-azure-cosmos-dbs-api-for-mongodb"></a>Lees bewerkingen globaal distribueren met behulp van de API van Azure Cosmos DB voor MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

In dit artikel wordt beschreven hoe u met beAzure Cosmos DB hulp van de MongoDb-API voor MongoDB wereld wijd Lees bewerkingen kunt distribueren met de [voor keuren](https://docs.mongodb.com/manual/core/read-preference/) voor het lezen van instellingen

## <a name="prerequisites"></a>Vereisten 
Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint. 
[!INCLUDE [cosmos-db-emulator-mongodb](../../includes/cosmos-db-emulator-mongodb.md)]

Raadpleeg dit [Quick](tutorial-global-distribution-mongodb.md) start-artikel voor instructies over het gebruik van de Azure Portal om een Cosmos-account in te stellen met globale distributie en er vervolgens verbinding mee te maken.

## <a name="clone-the-sample-application"></a>De voorbeeldtoepassing klonen

Open een git-terminalvenster zoals git bash en `cd` naar een werkmap.  

Voer de volgende opdrachten uit om de voorbeeldopslagplaats te klonen. Gebruik een van de volgende voor beelden van opslag plaatsen op basis van uw platform van belang:

1. [.NET-voorbeeld toepassing](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-geo-readpreference)
2. [Voorbeeld toepassing NodeJS]( https://github.com/Azure-Samples/azure-cosmos-db-mongodb-node-geo-readpreference)
3. [Voorbeeld toepassing Mongoose](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-mongoose-geo-readpreference)
4. [Java-voorbeeld toepassing](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-geo-readpreference)
5. [Voorbeeld toepassing SpringBoot](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-spring)


```bash
git clone <sample repo url>
```

## <a name="run-the-application"></a>De toepassing uitvoeren

Afhankelijk van het platform dat wordt gebruikt, installeert u de vereiste pakketten en start u de toepassing. Als u afhankelijkheden wilt installeren, volgt u het Leesmij-bestand dat is opgenomen in de voor beeld-toepassings opslagplaats. Gebruik bijvoorbeeld in de voorbeeld toepassing NodeJS de volgende opdrachten om de vereiste pakketten te installeren en de toepassing te starten.

```bash
cd mean
npm install
node index.js
```
De toepassing probeert verbinding te maken met een MongoDB-bron en mislukt omdat de connection string ongeldig is. Volg de stappen in het Leesmij-bestand om de connection string bij te werken `url` . Werk ook de `readFromRegion` naar een lees regio in uw Cosmos-account bij. De volgende instructies zijn afkomstig uit het NodeJS-voor beeld:

```
* Next, substitute the `url`, `readFromRegion` in App.Config with your Cosmos account's values. 
```

Na het volgen van deze stappen wordt de voorbeeld toepassing uitgevoerd en wordt de volgende uitvoer gegenereerd:

```
connected!
readDefaultfunc query completed!
readFromNearestfunc query completed!
readFromRegionfunc query completed!
readDefaultfunc query completed!
readFromNearestfunc query completed!
readFromRegionfunc query completed!
readDefaultfunc query completed!
readFromSecondaryfunc query completed!
```

## <a name="read-using-read-preference-mode"></a>Lezen met de voorkeurs modus lezen

Het MongoDB-protocol biedt de volgende Lees voorkeurs modi die clients kunnen gebruiken:

1. BASIS
2. PRIMARY_PREFERRED
3. PRIMAIRE
4. SECONDARY_PREFERRED
5. AFGEROND

Raadpleeg de documentatie over het gedrag van gedetailleerde [MongoDb Lees voorkeuren](https://docs.mongodb.com/manual/core/read-preference-mechanics/#replica-set-read-preference-behavior) voor meer informatie over het gedrag van elk van deze Lees voorkeuren. In Cosmos DB is primair toegewezen aan het schrijf gebied en de secundaire kaarten voor het lezen van regio's.

Op basis van algemene scenario's wordt u aangeraden de volgende instellingen te gebruiken:

1. Als **Lees bewerkingen met een lage latentie** vereist zijn, gebruikt u de **dichtstbijzijnde** modus voor lees voorkeuren. Met deze instelling wordt de Lees bewerking naar de dichtstbijzijnde beschik bare regio doorgestuurd. Houd er rekening mee dat als de dichtstbijzijnde regio de schrijf regio is, deze bewerkingen worden omgeleid naar deze regio.
2. Als **hoge Beschik baarheid en geo-distributie van Lees bewerkingen** vereist zijn (latentie is geen beperking), gebruikt u de **primaire** voorkeurs modus van de voor keur of **secundaire** voorkeurs instelling voor lezen. Met deze instelling worden Lees bewerkingen naar respectievelijk een beschik bare schrijf-of lees regio doorgestuurd. Als de regio niet beschikbaar is, worden aanvragen omgeleid naar de volgende beschik bare regio volgens het gedrag van de Lees voorkeur.

In het volgende code fragment van de voorbeeld toepassing ziet u hoe u de dichtstbijzijnde Lees voorkeur configureert in NodeJS:

```javascript
  var query = {};
  var readcoll = client.db('regionDB').collection('regionTest', {readPreference: ReadPreference.NEAREST});
  readcoll.find(query).toArray(function(err, data) {
    assert.equal(null, err);
    console.log("readFromNearestfunc query completed!");
  });
```

Op dezelfde manier ziet u in het volgende code fragment hoe u de SECONDARY_PREFERRED Lees voorkeur configureert in NodeJS:

```javascript
  var query = {};
  var readcoll = client.db('regionDB').collection('regionTest', {readPreference: ReadPreference.SECONDARY_PREFERRED});
  readcoll.find(query).toArray(function(err, data) {
    assert.equal(null, err);
    console.log("readFromSecondaryPreferredfunc query completed!");
  });
```

De Lees voorkeur kan ook worden ingesteld door `readPreference` als een para meter door te geven in de Connection String URI-opties:

```javascript
const MongoClient = require('mongodb').MongoClient;
const assert = require('assert');

// Connection URL
const url = 'mongodb://localhost:27017?ssl=true&replicaSet=globaldb&readPreference=nearest';

// Database Name
const dbName = 'myproject';

// Use connect method to connect to the Server
MongoClient.connect(url, function(err, client) {
  console.log("Connected correctly to server");

  const db = client.db(dbName);

  client.close();
});
```

Raadpleeg de bijbehorende voorbeeld toepassing opslag plaatsen voor andere platformen, zoals [.net](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-geo-readpreference) en [Java](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-geo-readpreference).

## <a name="read-using-tags"></a>Lezen met behulp van Tags

Naast de Lees modus voor keuren, kunt u met het MongoDB-protocol Tags gebruiken om Lees bewerkingen uit te kunnen sturen. In de API van Cosmos DB voor MongoDB, `region` wordt de tag standaard opgenomen als onderdeel van het `isMaster` antwoord:

```json
"tags": {
         "region": "West US"
      }
```

Daarom kan MongoClient de `region` tag samen met de regio naam gebruiken om Lees bewerkingen naar specifieke regio's te sturen. Voor Cosmos-accounts kunt u de namen van regio's vinden in Azure Portal aan de linkerkant onder **instellingen->replica gegevens wereld wijd**. Deze instelling is handig voor het bereiken van **Lees isolatie** -gevallen waarin client toepassing alleen lees bewerkingen naar een specifieke regio wil door sturen. Deze instelling is ideaal voor scenario's van het type niet-productie/analyse, die op de achtergrond worden uitgevoerd en geen essentiële services produceren.

In het volgende code fragment van de voorbeeld toepassing ziet u hoe u de voor keur lezen configureert met tags in NodeJS:

```javascript
 var query = {};
  var readcoll = client.db('regionDB').collection('regionTest',{readPreference: new ReadPreference(ReadPreference.SECONDARY_PREFERRED, {"region": "West US"})});
  readcoll.find(query).toArray(function(err, data) {
    assert.equal(null, err);
    console.log("readFromRegionfunc query completed!");
  });
```

Raadpleeg de bijbehorende voorbeeld toepassing opslag plaatsen voor andere platformen, zoals [.net](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-geo-readpreference) en [Java](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-geo-readpreference).

In dit artikel hebt u geleerd hoe u wereld wijd Lees bewerkingen kunt distribueren met behulp van Lees voorkeur met de API voor MongoDB van Azure Cosmos DB.

## <a name="clean-up-resources"></a>Resources opschonen

Als u deze app niet verder wilt gebruiken, verwijdert u alle resources die in dit artikel zijn gemaakt in de Azure Portal door de volgende stappen uit te voeren:

1. Klik in het menu aan de linkerkant in Azure Portal op **Resourcegroepen** en klik vervolgens op de resource die u hebt gemaakt. 
2. Klik op de pagina van uw resourcegroep op **Verwijderen**, typ de naam van de resource die u wilt verwijderen in het tekstvak en klik vervolgens op **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

* [MongoDB-gegevens importeren in Azure Cosmos DB](../dms/tutorial-mongodb-cosmos-db.md?toc=%2fazure%2fcosmos-db%2ftoc.json%253ftoc%253d%2fazure%2fcosmos-db%2ftoc.json)
* [Een wereld wijd gedistribueerde data base instellen met de API van Azure Cosmos DB voor MongoDB](tutorial-global-distribution-mongodb.md)
* [Lokaal ontwikkelen met de Azure Cosmos DB-emulator](local-emulator.md)