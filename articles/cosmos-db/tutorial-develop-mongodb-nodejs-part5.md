---
title: De Angular-app verbinden met de API van Azure Cosmos DB voor MongoDB met Mongoose
description: In deze zelfstudie wordt beschreven hoe u een Node.js-toepassing maakt door met Angular en Express de gegevens te beheren die zijn opgeslagen in Cosmos DB. In dit gedeelte gebruikt u Mongoose om verbinding te maken met Azure Cosmos DB.
author: johnpapa
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 12/26/2018
ms.author: jopapa
ms.custom: seodec18, devx-track-js
ms.reviewer: sngun
ms.openlocfilehash: a3097fa539f460ef5e8ffe73598fa5d55516717e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93097801"
---
# <a name="create-an-angular-app-with-azure-cosmos-dbs-api-for-mongodb---use-mongoose-to-connect-to-cosmos-db"></a>Een Angular-app maken met de API voor MongoDB van Azure Cosmos DB - Mongoose gebruiken om verbinding te maken met Cosmos DB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

In deze meerdelige zelfstudie leert u hoe u een Node.js-app maakt met Express en Angular, en hoe u deze app verbindt met uw [Cosmos-account dat is geconfigureerd met de API voor MongoDB van Cosmos DB](mongodb-introduction.md). In dit artikel wordt deel 5 van de zelfstudie beschreven. Dit is een voortzetting van [deel 4](tutorial-develop-mongodb-nodejs-part4.md).

In dit deel van de zelfstudie komen deze onderwerpen aan bod:

> [!div class="checklist"]
> * Mongoose gebruiken om verbinding te maken met Cosmos DB
> * Verbindingsreeks van Cosmos DB ophalen
> * Het Hero-model maken
> * De Hero-service maken voor het ophalen van Hero-gegevens
> * Voer de app lokaal uit.

Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

* Voer de stappen in [deel 4](tutorial-develop-mongodb-nodejs-part4.md) uit voordat u met deze zelfstudie begint.

* Voor deze zelfstudie moet u de Azure CLI lokaal uitvoeren. Azure CLI versie 2.0 of hoger moet geïnstalleerd zijn. Voer `az --version` uit om de versie te bekijken. Als u de Azure CLI moet installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren](/cli/azure/install-azure-cli).

* In deze zelfstudie wordt u stapsgewijs begeleid bij het maken van de toepassing. Als u het voltooide project wilt downloaden, kunt u de voltooide app vinden in de [angular-cosmosdb-opslagplaats](https://github.com/Azure-Samples/angular-cosmosdb) op GitHub.

## <a name="use-mongoose-to-connect"></a>Mongoose gebruiken om verbinding te maken

Mongoose is een ODM-bibliotheek (Object Data Modeling) voor MongoDB en Node.js. U kunt Mongoose gebruiken om verbinding te maken met uw Azure Cosmos DB-account. Gebruik de volgende stappen om Mongoose te installeren en verbinding te maken met Azure Cosmos DB:

1. Installeer de module mongoose npm, een API die wordt gebruikt voor de communicatie met MongoDB.

    ```bash
    npm i mongoose --save
    ```

1. Maak in de map **server** een bestand met de naam **mongo.js**. U gaat de verbindingsgegevens van uw Azure Cosmos DB-account toevoegen aan dit bestand.

1. Kopieer de volgende code naar het bestand **mongo.js**. De code biedt de volgende functionaliteit:

   * Vereist Mongoose.
   * Overschrijft de Mongo-promise om de basis-promise te gebruiken die is ingebouwd in ES6/ES2015 en latere versies.
   * Roept een ENV-bestand aan waarmee u bepaalde dingen kunt instellen op basis van de fase waarin u zich bevindt: fasering, productie of ontwikkeling. U maakt dit bestand in het volgende gedeelte.
   * Haalt de MongoDB-verbindingsreeks op, die is ingesteld in het ENV-bestand.
   * Maakt een verbindingsfunctie die Mongoose aanroept.

     ```javascript
     const mongoose = require('mongoose');
     /**
     * Set to Node.js native promises
     * Per https://mongoosejs.com/docs/promises.html
     */
     mongoose.Promise = global.Promise;

     const env = require('./env/environment');

     // eslint-disable-next-line max-len
     const mongoUri = `mongodb://${env.accountName}:${env.key}@${env.accountName}.documents.azure.com:${env.port}/${env.databaseName}?ssl=true`;

     function connect() {
     mongoose.set('debug', true);
     return mongoose.connect(mongoUri, { useMongoClient: true });
     }

     module.exports = {
     connect,
     mongoose
     };
     ```
    
1. Maak in het deelvenster Explorer, onder **server**, een map met de naam **environment**. Maak in de map **environment** een bestand met de naam **environment.js**.

1. Uit het bestand mongo.js moeten we waarden overnemen voor de parameters `dbName`, `key` en `cosmosPort`. Kopieer de volgende code naar het bestand **environment.js**:

    ```javascript
    // TODO: replace if yours are different
    module.exports = {
      accountName: 'your-cosmosdb-account-name-goes-here',
      databaseName: 'admin', 
      key: 'your-key-goes-here',
      port: 10255
    };
    ```

## <a name="get-the-connection-string"></a>De verbindingsreeks ophalen

Om uw toepassing te verbinden met Azure Cosmos DB, moet u de configuratie-instellingen voor de toepassing bijwerken. Voer de volgende stappen uit om de instellingen bij te werken: 

1. Ga naar de Azure-portal en zoek deze gegevens op voor uw Azure Cosmos DB-account: het poortnummer, de Azure Cosmos DB-accountnaam en de primaire sleutel.

1. Wijzig in het bestand **environment.js** de waarde van `port` in 10255. 

    ```javascript
    const port = 10255;
    ```

1. Wijzig in het bestand **environment.js** de waarde van `accountName` in de naam van het Azure Cosmos DB-account dat u in [deel 4](tutorial-develop-mongodb-nodejs-part4.md) van de reeks zelfstudies hebt gemaakt. 

1. Haal de primaire sleutel voor het Azure Cosmos DB-account op via de volgende CLI-opdracht in het terminalvenster: 

    ```azurecli-interactive
    az cosmosdb list-keys --name <cosmosdb-name> -g myResourceGroup
    ```    
    
    \<cosmosdb-name> is de naam van het Azure Cosmos DB-account dat u hebt gemaakt in [deel 4](tutorial-develop-mongodb-nodejs-part4.md) van de reeks zelfstudies.

1. Kopieer de primaire sleutel naar het bestand **environment.js** als de waarde voor `key`.

De toepassing beschikt nu over alle gegevens om verbinding te maken met Azure Cosmos DB. 

## <a name="create-a-hero-model"></a>Een Hero-model maken

Vervolgens moet u het schema definiëren van de gegevens die moeten worden opgeslagen in Azure Cosmos DB. Dit doet u door een modelbestand te definiëren. Gebruik de volgende stappen om een _Hero-model_ te maken waarmee het schema van de gegevens wordt gedefinieerd:

1. Maak in het deelvenster Explorer het bestand **hero.model.js** onder de map **server**.

1. Kopieer de volgende code naar het bestand **hero.model.js**. De code biedt de volgende functionaliteit:

   * Vereist Mongoose.
   * Maakt een nieuw schema met een id, een naam en een uitspraak.
   * Maakt een model aan de hand van het schema.
   * Exporteert het model. 
   * Geeft de verzameling de naam **Heroes** (in plaats van **Heros**, wat de standaardnaam van de verzameling is op basis van de regels voor namen in meervoud in Mongoose).

   ```javascript
   const mongoose = require('mongoose');

   const Schema = mongoose.Schema;

   const heroSchema = new Schema(
     {
       id: { type: Number, required: true, unique: true },
       name: String,
       saying: String
     },
     {
       collection: 'Heroes'
     }
   );

   const Hero = mongoose.model('Hero', heroSchema);

   module.exports = Hero;
   ```

## <a name="create-a-hero-service"></a>Een Hero-service maken

Als u het Hero-model hebt gemaakt, moet u een service definiëren voor het lezen van de gegevens, en voor het uitvoeren van bewerkingen voor opvragen, verwijderen en bijwerken. Gebruik de volgende stappen om een _Hero-service_ te maken die een query uitvoert op de gegevens vanuit Azure Cosmos DB:

1. Maak in het deelvenster Explorer het bestand **hero.service.js** onder de map **server**.

1. Kopieer de volgende code naar het bestand **hero.service.js**. De code biedt de volgende functionaliteit:

   * Haalt het model op dat u hebt gemaakt.
   * Maakt verbinding met de database.
   * Maakt een variabele `docquery` die de methode `hero.find` gebruikt voor het definiëren van een query waarmee alle hero's worden geretourneerd.
   * Voert een query uit met de functie `docquery.exec` met een promise om een lijst op te vragen met alle hero's waarvan de responsstatus 200 is. 
   * Retourneert het foutbericht als de status 500 is.
   * Haalt de hero's op omdat we modules gebruiken. 

   ```javascript
   const Hero = require('./hero.model');

   require('./mongo').connect();

   function getHeroes() {
     const docquery = Hero.find({});
     docquery
       .exec()
       .then(heroes => {
         res.status(200).json(heroes);
       })
       .catch(error => {
         res.status(500).send(error);
         return;
       });
   }

   module.exports = {
     getHeroes
   };
   ```

## <a name="configure-routes"></a>Routes configureren

Vervolgens moet u routes instellen voor het afhandelen van de URL's voor get-, create-, read- en delete-aanvragen. De routeringsmethoden bevatten callback-functies (ook wel _handlerfuncties_ genoemd). Deze functies worden aangeroepen wanneer de toepassing een aanvraag voor het opgegeven eindpunt en de HTTP-methode ontvangt. Gebruik de volgende stappen om de Hero-service toe te voegen en uw routes te definiëren:

1. Open in Visual Studio Code het bestand **routes.js** en maak een commentaar van de functie `res.send` waarmee de hero-voorbeeldgegevens worden verzonden. Voeg in plaats daarvan een regel toe om de functie `heroService.getHeroes` aan te roepen.

    ```javascript
    router.get('/heroes', (req, res) => {
      heroService.getHeroes(req, res);
    //  res.send(200, [
    //      {"id": 10, "name": "Starlord", "saying": "oh yeah"}
    //  ])
    });
    ```

1. `require` de hero-service in het bestand **routes.js**:

    ```javascript
    const heroService = require('./hero.service'); 
    ```

1. Werk in het bestand **hero.service.js** de functie `getHeroes` bij voor gebruik van de parameters `req` en `res` (zie hieronder):

    ```javascript
    function getHeroes(req, res) {
    ```

Laten we de bovenstaande code nog eens even bekijken. We hebben eerst het bestand index.js, waarmee de node-server wordt ingesteld. In het bestand worden uw routes ingesteld en gedefinieerd. Vervolgens communiceert het bestand routes.js met de hero-service. De service krijgt de opdracht functies op te halen, zoals **getHeroes**, en de aanvraag en respons door te geven. Het bestand hero.service.js haalt het model op en maakt verbinding met Mongo. Vervolgens wordt **getHeroes** uitgevoerd wanneer we deze functie aanroepen en wordt de respons 200 geretourneerd. 

## <a name="run-the-app"></a>De app uitvoeren

Voer nu de app uit door de volgende stappen te volgen:

1. Sla alle wijzigingen op in Visual Studio Code. Selecteer de knop **Fouten opsporen** aan de linkerkant :::image type="icon" source="./media/tutorial-develop-mongodb-nodejs-part5/debug-button.png":::, en selecteer vervolgens de knop **Foutopsporing starten** :::image type="icon" source="./media/tutorial-develop-mongodb-nodejs-part5/start-debugging-button.png":::.

1. Schakel nu over naar de browser. Open de **hulpprogramma's voor ontwikkelaars** en ga naar het tabblad **Network**. Ga naar `http://localhost:3000` en daar ziet u de toepassing.

    :::image type="content" source="./media/tutorial-develop-mongodb-nodejs-part5/azure-cosmos-db-heroes-app.png" alt-text="Nieuw Azure Cosmos DB-account in de Azure-portal":::

Er zijn nog geen hero's opgeslagen in de app. In het volgende deel van deze reeks zelfstudies gaan we put-, push- en delete-functionaliteit toevoegen. Daarna kunnen we vanuit de gebruikersinterface hero's toevoegen, bijwerken en verwijderen met behulp van Mongoose-verbindingen met onze Azure Cosmos-database. 

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u de resources niet meer nodig hebt, kunt u de resourcegroep, het Azure Cosmos DB-account en alle bijbehorende resources verwijderen. Gebruik de volgende stappen om de resourcegroep te verwijderen:

 1. Ga naar de resourcegroep waarin u het Azure Cosmos DB-account hebt gemaakt.
 1. Selecteer **Resourcegroep verwijderen**.
 1. Bevestig de naam van de resourcegroep die u wilt verwijderen en selecteer **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

Ga verder met deel 6 van de reeks zelfstudies om de functies Post, Put en Delete toe te voegen aan de app:

> [!div class="nextstepaction"]
> [Deel 6: De functies Post, Put en Delete aan de app toevoegen](tutorial-develop-mongodb-nodejs-part6.md)