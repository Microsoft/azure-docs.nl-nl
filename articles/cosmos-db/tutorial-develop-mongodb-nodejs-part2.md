---
title: Een Node.js Express-app maken met de API van Azure Cosmos DB voor MongoDB (deel 2)
description: Deel 2 van de serie zelfstudies voor het maken van een MongoDB-app met Angular en Node op Azure Cosmos DB, waarbij gebruik wordt gemaakt van dezelfde API's als voor MongoDB.
author: johnpapa
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 12/26/2018
ms.author: jopapa
ms.custom: seodec18
ms.reviewer: sngun
ms.openlocfilehash: e3af713d6e120e66a0cde5116f4556bb8caedf55
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93097842"
---
# <a name="create-an-angular-app-with-azure-cosmos-dbs-api-for-mongodb---create-a-nodejs-express-app"></a>Een Angular-app maken met de API van Azure Cosmos DB voor MongoDB - een Node.js Express-app maken
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

In deze meerdelige zelfstudie leert u hoe u een app maakt die is geschreven in Node.js met Express en Angular, en hoe u deze app verbindt met uw [Cosmos-account dat is geconfigureerd met de API van Cosmos DB voor MongoDB](mongodb-introduction.md).

Deel 2 van de zelfstudie bouwt voort op [de inleiding](tutorial-develop-mongodb-nodejs.md) en beschrijft de volgende taken:

> [!div class="checklist"]
> * De Angular CLI en TypeScript installeren
> * Een nieuw project maken met Angular
> * De app uitbouwen met het Express-framework
> * De app testen in Postman

## <a name="video-walkthrough"></a>Video-overzicht

> [!VIDEO https://www.youtube.com/embed/lIwJIYcGSUg]

## <a name="prerequisites"></a>Vereisten

Bekijk de [introductievideo](tutorial-develop-mongodb-nodejs.md) voordat u begint aan dit deel van de zelfstudie.

Voor deze zelfstudie hebt u verder nodig: 
* [Node.js](https://nodejs.org/) versie 8.4.0 of hoger.
* [Postman](https://www.getpostman.com/)
* [Visual Studio Code](https://code.visualstudio.com/) of uw favoriete code-editor.

> [!TIP]
> Aan de hand van deze zelfstudie wordt u stapsgewijs begeleid bij het maken van de toepassing. Als u het voltooide project wilt downloaden, kunt u de voltooide app vinden in de [angular-cosmosdb-opslagplaats](https://github.com/Azure-Samples/angular-cosmosdb) op GitHub.

## <a name="install-the-angular-cli-and-typescript"></a>De Angular CLI en TypeScript installeren

1. Open een Windows-opdrachtprompt of Mac-terminalvenster en installeer de Angular CLI.

    ```bash
    npm install -g @angular/cli
    ```

2. Installeer TypeScript door in de opdrachtprompt de volgende opdracht te typen. 

    ```bash
    npm install -g typescript
    ```

## <a name="use-the-angular-cli-to-create-a-new-project"></a>De Angular CLI gebruiken om een nieuw project te maken

1. Navigeer in de opdrachtprompt naar de map waar u het nieuwe project wilt opslaan en voer de volgende opdracht uit. Met deze opdracht worden een nieuwe map en een nieuw project gemaakt met de naam angular-cosmosdb. Ook worden de Angular-componenten geïnstalleerd die voor een nieuwe app zijn vereist. Tevens worden de minimale instellingen gebruikt (--minimal) en wordt opgegeven dat het project gebruikmaakt van Sass (een CSS-achtige syntaxis met de vlag --style scss).

    ```bash
    ng new angular-cosmosdb --minimal --style scss
    ```

2. Als de opdracht is voltooid, wijzigt u de mappen in de src-/clientmap.

    ```bash
    cd angular-cosmosdb
    ```

3. Open vervolgens de map in Visual Studio Code.

    ```bash
    code .
    ```

## <a name="build-the-app-using-the-express-framework"></a>De app bouwen met het Express-framework

1. In Visual Studio Code klikt u in het deelvenster **Explorer** met de rechtermuisknop op de map **src** en klikt u op **New Folder**. Geef de nieuwe map de naam *server*.

2. Klik in het deelvenster **Explorer** met de rechtermuisknop op de map **server**, klik op **New File** en geef het nieuwe bestand de naam *index.js*.

3. Ga terug naar de opdrachtprompt en gebruik de volgende opdracht om de body-parser te installeren. Hiermee kan de app de JSON-gegevens parseren die worden doorgegeven via de API's.

    ```bash
    npm i express body-parser --save
    ```

4. Kopieer in Visual Studio Code de volgende code in het bestand index.js. Deze code:
    * Verwijst naar Express
    * Haalt de body-parser op voor het lezen van de JSON-gegevens in de hoofdtekst (body) van de aanvragen
    * Gebruikt een ingebouwde functie met de naam path
    * Stelt hoofdvariabelen in om het zoeken naar de locatie van de code te vereenvoudigen
    * Stelt een poort in
    * Start Express
    * Vertelt de app hoe gebruik moet worden gemaakt van de middleware die wordt gebruikt voor het ophalen van de server
    * Behandelt alles dat zich in de map dist bevindt, dat wil zeggen de statische inhoud
    * Haalt de app op en behandelt index.html voor alle GET-aanvragen die niet op de server worden gevonden (voor dieptekoppelingen)
    * Start de server met app.listen
    * Gebruikt een pijlfunctie om te registreren dat de poort actief is
    
   ```node
   const express = require('express');
   const bodyParser = require('body-parser');
   const path = require('path');
   const routes = require('./routes');

   const root = './';
   const port = process.env.PORT || '3000';
   const app = express();

   app.use(bodyParser.json());
   app.use(bodyParser.urlencoded({ extended: false }));
   app.use(express.static(path.join(root, 'dist/angular-cosmosdb')));
   app.use('/api', routes);
   app.get('*', (req, res) => {
     res.sendFile('dist/angular-cosmosdb/index.html', {root});
   });

   app.listen(port, () => console.log(`API running on localhost:${port}`));
   ```

5. In Visual Studio Code klikt u in het deelvenster **Explorer** met de rechtermuisknop op de map **server** en klikt u vervolgens op **New file**. Geef het nieuwe bestand de naam *routes.js*. 

6. Kopieer de volgende code in **routes.js**. Deze code:
   * Verwijst naar de Express-router
   * Haalt de hero's op
   * Stuurt de JSON terug voor een gedefinieerde hero

   ```node
   const express = require('express');
   const router = express.Router();

   router.get('/heroes', (req, res) => {
    res.send(200, [
       {"id": 10, "name": "Starlord", "saying": "oh yeah"}
    ])
   });

   module.exports=router;
   ```

7. Sla al uw gewijzigde bestanden op. 

8. Klik in Visual Studio Code op de knop **Fouten opsporen** :::image type="icon" source="./media/tutorial-develop-mongodb-nodejs-part2/debug-button.png":::, en klik op de knop Tandwiel :::image type="icon" source="./media/tutorial-develop-mongodb-nodejs-part2/gear-button.png":::. Het nieuwe bestand launch.json wordt geopend in Visual Studio Code. 

8. Wijzig in regel 11 van het bestand launch.json `"${workspaceFolder}\\server"` in `"program": "${workspaceRoot}/src/server/index.js"` en sla het bestand op.

9. Klik op de knop **Foutopsporing starten** :::image type="icon" source="./media/tutorial-develop-mongodb-nodejs-part2/start-debugging-button.png"::: om de app uit te voeren.

    De app moet nu foutloos worden uitgevoerd.

## <a name="use-postman-to-test-the-app"></a>Postman gebruiken om de app te testen

1. Open nu Postman en voeg `http://localhost:3000/api/heroes` aan het vak GET toe. 

2. Klik op de knop **Send** en haal de json-respons uit de app op. 

    De respons laat zien dat de app actief is en lokaal wordt uitgevoerd. 

    :::image type="content" source="./media/tutorial-develop-mongodb-nodejs-part2/azure-cosmos-db-postman.png" alt-text="Postman, waarin de aanvraag en de respons worden weergegeven":::


## <a name="next-steps"></a>Volgende stappen

In dit deel van de zelfstudie hebt u het volgende gedaan:

> [!div class="checklist"]
> * Een Node.js-project gemaakt met de Angular CLI
> * De app getest in Postman

U kunt doorgaan met het volgende deel van de zelfstudie voor het bouwen van de gebruikersinterface.

> [!div class="nextstepaction"]
> [De gebruikersinterface bouwen met Angular](tutorial-develop-mongodb-nodejs-part3.md)
