---
title: 'Zelfstudie: Een Node.js-web-app bouwen met Azure Cosmos DB voor het beheren van SQL API-gegevens'
description: In deze zelfstudie over Node.js wordt uitgelegd hoe u Microsoft Azure Cosmos DB gebruikt voor het opslaan van en de toegang tot gegevens van een Node.js Express-webtoepassing die wordt gehost op de Web Apps-functie van Microsoft Azure App Service.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 11/05/2019
ms.author: sngun
ms.custom: devx-track-js
ms.openlocfilehash: 49cf54bda985f7d97b2db6a3ada7859aee829cff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97359537"
---
# <a name="tutorial-build-a-nodejs-web-app-using-the-javascript-sdk-to-manage-a-sql-api-account-in-azure-cosmos-db"></a>Zelfstudie: Een Node.js-web-app bouwen met behulp van de JavaScript SDK voor het beheren van een SQL API-account in Azure Cosmos DB 
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET](sql-api-dotnet-application.md)
> * [Java](sql-api-java-application.md)
> * [Node.js](sql-api-nodejs-application.md)
> * [Python](./create-sql-api-python.md)
> * [Xamarin](mobile-apps-with-xamarin.md)
> 

Als ontwikkelaar hebt u mogelijk toepassingen die gebruikmaken van NoSQL-documentgegevens. U kunt een SQL-API-account in Azure Cosmos DB gebruiken om deze documentgegevens op te slaan en te openen. In deze zelfstudie over Node.js wordt uitgelegd hoe u gegevens uit een SQL API-account in Azure Cosmos DB kunt opslaan en openen met behulp van een Node.js Express-toepassing die wordt gehost in de Web Apps-functie van Microsoft Azure App Service. In deze zelfstudie bouwt u een webtoepassing (Todo-app) waarmee u taken kunt maken, ophalen en voltooien. De taken worden opgeslagen als JSON-documenten in Azure Cosmos DB. 

Deze zelfstudie laat zien hoe u een SQL API-account in Azure Cosmos DB kunt maken met behulp van Azure Portal. Vervolgens bouwt u een web-app die op de Node.js SDK is gebouwd om een database en container te maken en items aan de container toe te voegen. Daarna kunt u de web-app uitvoeren. In deze zelfstudie wordt gebruikgemaakt van JavaScript SDK-versie 3.0.

Deze zelfstudie bestaat uit de volgende taken:

> [!div class="checklist"]
> * Maak een Azure Cosmos DB-account
> * Een nieuwe Node.js-toepassing maken
> * De toepassing verbinden met Azure Cosmos DB
> * De toepassing uitvoeren en implementeren in Azure

## <a name="prerequisites"></a><a name="_Toc395783176"></a>Vereisten

Voordat u de instructies in dit artikel uitvoert, moet u beschikken over de volgende resources:

* Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint. 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [Node.js][Node.js], versie 6.10 of hoger.
* [Express generator](https://www.expressjs.com/starter/generator.html) (u kunt Express installeren via `npm install express-generator -g`)
* Installeer [Git][Git] op uw lokale werkstation.

## <a name="create-an-azure-cosmos-db-account"></a><a name="_Toc395637761"></a>Een Azure Cosmos DB-account maken
Begin met het maken van een Azure Cosmos DB-account. Als u al een account hebt of de Azure Cosmos DB-emulator gebruikt voor deze zelfstudie, kunt u direct doorgaan naar [Stap 2: Een nieuwe Node.js-toepassing maken](#_Toc395783178).

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

[!INCLUDE [cosmos-db-keys](../../includes/cosmos-db-keys.md)]

## <a name="create-a-new-nodejs-application"></a><a name="_Toc395783178"></a>Een nieuwe Node.js-toepassing maken
Laten we eens kijken hoe u het Express-framework gebruikt om een eenvoudig Hallo wereld Node.js-project te maken.

1. Open een terminal waarmee u graag werkt, zoals de Node.js-opdrachtprompt.

1. Navigeer naar de map waarin u de nieuwe toepassing wilt opslaan.

1. Gebruik de express-generator om een toepassing met de naam **todo** te maken.

   ```bash
   express todo
   ```

1. Open de nieuwe map **todo** en installeer de afhankelijkheden.

   ```bash
   cd todo
   npm install
   ```

1. Voer de nieuwe toepassing uit.

   ```bash
   npm start
   ```

1. U kunt uw nieuwe toepassing bekijken door in uw browser naar `http://localhost:3000` te gaan.
   
   :::image type="content" source="./media/sql-api-nodejs-application/cosmos-db-node-js-express.png" alt-text="Node.js leren - Schermopname van de toepassing Hello World in een browservenster":::

   Stop de toepassing door middel van Ctrl+C in het terminalvenster en selecteer **y** om de batchtaak te beëindigen.

## <a name="install-the-required-modules"></a><a name="_Toc395783179"></a>De vereiste modules installeren

Het bestand **package.json** is een van de bestanden die zijn gemaakt in de hoofdmap van het project. Dit bestand bevat een lijst met aanvullende modules die u nodig hebt voor uw Node.js-toepassing. Wanneer u deze toepassing implementeert in Azure, wordt dit bestand gebruikt om te bepalen welke modules ter ondersteuning van de toepassing in Azure moeten worden geïnstalleerd. Installeer nog twee pakketten voor deze zelfstudie.

1. Installeer de module **\@azure/cosmos** via NPM. 

   ```bash
   npm install @azure/cosmos
   ```

## <a name="connect-the-nodejs-application-to-azure-cosmos-db"></a><a name="_Toc395783180"></a>De Node.js-toepassing verbinden met Azure Cosmos DB
U hebt nu de initiële setup en configuratie voltooid. U gaat nu code schrijven die voor de Todo-toepassing is vereist om met Azure Cosmos DB te kunnen communiceren.

### <a name="create-the-model"></a>Het model maken
1. Maak in de hoofdmap van uw projectmap een nieuwe map met de naam **models**.  

2. Maak in de map **models** een nieuw bestand met de naam **taskDao.js**. Dit bestand bevat code die vereist is voor het maken van de database en de container. Het definieert ook methoden om taken te lezen, bij te werken, te maken en te zoeken in Azure Cosmos DB. 

3. Kopieer de volgende code naar het bestand **taskDao.js**:

   ```javascript
    // @ts-check
    const CosmosClient = require('@azure/cosmos').CosmosClient
    const debug = require('debug')('todo:taskDao')

    // For simplicity we'll set a constant partition key
    const partitionKey = undefined
    class TaskDao {
      /**
       * Manages reading, adding, and updating Tasks in Cosmos DB
       * @param {CosmosClient} cosmosClient
       * @param {string} databaseId
       * @param {string} containerId
       */
      constructor(cosmosClient, databaseId, containerId) {
        this.client = cosmosClient
        this.databaseId = databaseId
        this.collectionId = containerId

        this.database = null
        this.container = null
      }

      async init() {
        debug('Setting up the database...')
        const dbResponse = await this.client.databases.createIfNotExists({
          id: this.databaseId
        })
        this.database = dbResponse.database
        debug('Setting up the database...done!')
        debug('Setting up the container...')
        const coResponse = await this.database.containers.createIfNotExists({
          id: this.collectionId
        })
        this.container = coResponse.container
        debug('Setting up the container...done!')
      }

      async find(querySpec) {
        debug('Querying for items from the database')
        if (!this.container) {
          throw new Error('Collection is not initialized.')
        }
        const { resources } = await this.container.items.query(querySpec).fetchAll()
        return resources
      }

      async addItem(item) {
        debug('Adding an item to the database')
        item.date = Date.now()
        item.completed = false
        const { resource: doc } = await this.container.items.create(item)
        return doc
      }

      async updateItem(itemId) {
        debug('Update an item in the database')
        const doc = await this.getItem(itemId)
        doc.completed = true

        const { resource: replaced } = await this.container
          .item(itemId, partitionKey)
          .replace(doc)
        return replaced
      }

      async getItem(itemId) {
        debug('Getting an item from the database')
        const { resource } = await this.container.item(itemId, partitionKey).read()
        return resource
      }
    }

    module.exports = TaskDao
   ```
4. Sla het bestand **taskDao.js** op en sluit het bestand.  

### <a name="create-the-controller"></a>De controller maken

1. Maak in de map **routes** van uw project een nieuw bestand met de naam **tasklist.js**.  

2. Voeg de volgende code toe aan het bestand **tasklist.js**. Met deze code worden de CosmosClient- en async-modules geladen die worden gebruikt voor **tasklist.js**. Met deze code wordt ook de klasse **TaskList** gedefinieerd, die wordt doorgegeven als een exemplaar van het eerder gedefinieerde object **TaskDao**:
   
   ```javascript
    const TaskDao = require("../models/TaskDao");
    
    class TaskList {
      /**
       * Handles the various APIs for displaying and managing tasks
       * @param {TaskDao} taskDao
       */
      constructor(taskDao) {
        this.taskDao = taskDao;
      }
      async showTasks(req, res) {
        const querySpec = {
          query: "SELECT * FROM root r WHERE r.completed=@completed",
          parameters: [
            {
              name: "@completed",
              value: false
            }
          ]
        };

        const items = await this.taskDao.find(querySpec);
        res.render("index", {
          title: "My ToDo List ",
          tasks: items
        });
      }

      async addTask(req, res) {
        const item = req.body;

        await this.taskDao.addItem(item);
        res.redirect("/");
      }

      async completeTask(req, res) {
        const completedTasks = Object.keys(req.body);
        const tasks = [];

        completedTasks.forEach(task => {
          tasks.push(this.taskDao.updateItem(task));
        });

        await Promise.all(tasks);

        res.redirect("/");
      }
    }

    module.exports = TaskList;
   ```

3. Sla het bestand **tasklist.js** op en sluit het bestand.

### <a name="add-configjs"></a>Config.js toevoegen

1. Maak in de hoofdmap van uw projectmap een nieuw bestand met de naam **config.js**. 

2. Voeg de volgende code toe aan het bestand **config.js**. Met deze code definieert u configuratie-instellingen en waarden die voor de toepassing zijn vereist.
   
   ```javascript
   const config = {};

   config.host = process.env.HOST || "[the endpoint URI of your Azure Cosmos DB account]";
   config.authKey =
     process.env.AUTH_KEY || "[the PRIMARY KEY value of your Azure Cosmos DB account";
   config.databaseId = "ToDoList";
   config.containerId = "Items";

   if (config.host.includes("https://localhost:")) {
     console.log("Local environment detected");
     console.log("WARNING: Disabled checking of self-signed certs. Do not have this code in production.");
     process.env.NODE_TLS_REJECT_UNAUTHORIZED = "0";
     console.log(`Go to http://localhost:${process.env.PORT || '3000'} to try the sample.`);
   }

   module.exports = config;
   ```

3. Werk in het bestand **config.js** de waarden voor HOST en AUTH_KEY bij met de waarden op de pagina Sleutels van uw Azure Cosmos DB-account in [Azure Portal](https://portal.azure.com). 

4. Sla het bestand **config.js** op en sluit het bestand.

### <a name="modify-appjs"></a>App.js wijzigen

1. Ga naar de projectmap en open het bestand **app.js**. Dit bestand is gemaakt toen de Express-webtoepassing werd gemaakt.  

2. Voeg de volgende code toe aan het bestand **app.js**. Met deze code wordt gedefinieerd welk configuratiebestand moet worden gebruikt, en worden de waarden in een aantal variabelen geladen die u gaat gebruiken in de volgende secties. 
   
   ```javascript
    const CosmosClient = require('@azure/cosmos').CosmosClient
    const config = require('./config')
    const TaskList = require('./routes/tasklist')
    const TaskDao = require('./models/taskDao')

    const express = require('express')
    const path = require('path')
    const logger = require('morgan')
    const cookieParser = require('cookie-parser')
    const bodyParser = require('body-parser')

    const app = express()

    // view engine setup
    app.set('views', path.join(__dirname, 'views'))
    app.set('view engine', 'jade')

    // uncomment after placing your favicon in /public
    //app.use(favicon(path.join(__dirname, 'public', 'favicon.ico')));
    app.use(logger('dev'))
    app.use(bodyParser.json())
    app.use(bodyParser.urlencoded({ extended: false }))
    app.use(cookieParser())
    app.use(express.static(path.join(__dirname, 'public')))

    //Todo App:
    const cosmosClient = new CosmosClient({
      endpoint: config.host,
      key: config.authKey
    })
    const taskDao = new TaskDao(cosmosClient, config.databaseId, config.containerId)
    const taskList = new TaskList(taskDao)
    taskDao
      .init(err => {
        console.error(err)
      })
      .catch(err => {
        console.error(err)
        console.error(
          'Shutting down because there was an error settinig up the database.'
        )
        process.exit(1)
      })

    app.get('/', (req, res, next) => taskList.showTasks(req, res).catch(next))
    app.post('/addtask', (req, res, next) => taskList.addTask(req, res).catch(next))
    app.post('/completetask', (req, res, next) =>
      taskList.completeTask(req, res).catch(next)
    )
    app.set('view engine', 'jade')

    // catch 404 and forward to error handler
    app.use(function(req, res, next) {
      const err = new Error('Not Found')
      err.status = 404
      next(err)
    })

    // error handler
    app.use(function(err, req, res, next) {
      // set locals, only providing error in development
      res.locals.message = err.message
      res.locals.error = req.app.get('env') === 'development' ? err : {}

      // render the error page
      res.status(err.status || 500)
      res.render('error')
    })

    module.exports = app
   ```

3. Sla ten slotte het bestand **app.js** op en sluit het bestand.

## <a name="build-a-user-interface"></a><a name="_Toc395783181"></a>Een gebruikersinterface maken

Nu gaan we de gebruikersinterface bouwen zodat een gebruiker kan werken met de toepassing. De Express-toepassing die is gemaakt in de vorige secties, gebruikt **Jade** als de weergave-engine.

1. Het bestand **layout.jade** in de map **views** wordt gebruikt als een algemeen sjabloon voor andere **.jade**-bestanden. In deze stap wordt het bestand aangepast voor het gebruik van Twitter Bootstrap. Dit is een werkset waarmee u een website kunt ontwerpen.  

2. Open het bestand **layout.jade** in de map **views** en vervang de inhoud door de volgende code:

   ```html
   doctype html
   html
     head
       title= title
       link(rel='stylesheet', href='//ajax.aspnetcdn.com/ajax/bootstrap/3.3.2/css/bootstrap.min.css')
       link(rel='stylesheet', href='/stylesheets/style.css')
     body
       nav.navbar.navbar-inverse.navbar-fixed-top
         div.navbar-header
           a.navbar-brand(href='#') My Tasks
       block content
       script(src='//ajax.aspnetcdn.com/ajax/jQuery/jquery-1.11.2.min.js')
       script(src='//ajax.aspnetcdn.com/ajax/bootstrap/3.3.2/bootstrap.min.js')
   ```

    Door deze code weet de **Jade**-engine dat er bepaalde HTML-code voor onze toepassing moet worden weergegeven en wordt er een **blok** met de naam **content** gemaakt waar u lay-out kunt opgeven voor de inhoudspagina's. Sla het bestand **layout.jade** op en sluit het bestand.

3. Open nu het bestand **index.jade**, de weergave die door de toepassing wordt gebruikt, en vervang de inhoud van het bestand door de volgende code:

   ```html
   extends layout
   block content
        h1 #{title}
        br
        
        form(action="/completetask", method="post")
         table.table.table-striped.table-bordered
            tr
              td Name
              td Category
              td Date
              td Complete
            if (typeof tasks === "undefined")
              tr
                td
            else
              each task in tasks
                tr
                  td #{task.name}
                  td #{task.category}
                  - var date  = new Date(task.date);
                  - var day   = date.getDate();
                  - var month = date.getMonth() + 1;
                  - var year  = date.getFullYear();
                  td #{month + "/" + day + "/" + year}
                  td
                   if(task.completed) 
                    input(type="checkbox", name="#{task.id}", value="#{!task.completed}", checked=task.completed)
                   else
                    input(type="checkbox", name="#{task.id}", value="#{!task.completed}", checked=task.completed)
          button.btn.btn-primary(type="submit") Update tasks
        hr
        form.well(action="/addtask", method="post")
          label Item Name:
          input(name="name", type="textbox")
          label Item Category:
          input(name="category", type="textbox")
          br
          button.btn(type="submit") Add item
   ```

Met deze code breidt u de lay-out uit en levert u inhoud voor de tijdelijke aanduiding **content** die we eerder in het bestand **layout.jade** zijn tegengekomen. In deze lay-out hebt u twee HTML-formulieren gemaakt.

Het eerste formulier bevat een tabel voor onze gegevens en een knop waarmee items kunnen worden bijgewerkt door ze naar de methode **/completeTask** van de controller te verzenden.
    
Het tweede formulier bevat twee invoervelden en een knop waarmee een nieuw item kan worden gemaakt door ze naar de methode **/addtask** van de controller te verzenden. Meer is er niet nodig om de toepassing te laten werken.

## <a name="run-your-application-locally"></a><a name="_Toc395783181"></a>De toepassing lokaal uitvoeren

Nu u de toepassing hebt gebouwd, kunt u deze lokaal uitvoeren met behulp van de volgende stappen:  

1. Als u de toepassing wilt testen op de lokale machine, voert u `npm start` uit in de terminal om de toepassing te starten. Vernieuw vervolgens de browserpagina `http://localhost:3000`. De pagina ziet er nu uit zoals de volgende schermafbeelding:
   
    :::image type="content" source="./media/sql-api-nodejs-application/cosmos-db-node-js-localhost.png" alt-text="Schermopname van de toepassing MyTodo List in een browservenster":::

    > [!TIP]
    > Als u een fout ontvangt over de inspringing in het layout.jade- of index.jade-bestand, moet u ervoor zorgen dat de eerste twee regels in beide bestanden links worden uitgelijnd, zonder spaties. Als er vóór de eerste twee regels spaties staan, verwijdert u deze, slaat u beide bestanden op en vernieuwt u het browservenster. 

2. Gebruik de velden Item, Itemnaam en Categorie om een nieuwe taak in te voeren en selecteer vervolgens **Item toevoegen**. Hiermee maakt u in Azure Cosmos DB een document met deze eigenschappen. 

3. De pagina moet worden bijgewerkt met het nieuwe item in de takenlijst.
   
    :::image type="content" source="./media/sql-api-nodejs-application/cosmos-db-node-js-added-task.png" alt-text="Schermopname van de toepassing met een nieuw item in de takenlijst":::

4. Als u een taak wilt voltooien, schakelt u het selectievakje in de kolom Voltooid in en selecteert u vervolgens **Taken bijwerken**. Hiermee wordt het door u gemaakte document bijgewerkt en uit de weergave verwijderd.

5. Klik om de toepassing te stoppen op CTRL+C in het terminalvenster en selecteer vervolgens **y** om de batchtaak te beëindigen.

## <a name="deploy-your-application-to-web-apps"></a><a name="_Toc395783182"></a>Uw toepassing implementeren in Web Apps

Als uw toepassing lokaal goed werkt, kunt u deze implementeren naar Azure met behulp van de volgende stappen:

1. Als u dit nog niet hebt gedaan, schakelt u een git-opslagplaats in voor uw Web Apps-toepassing.

2. Voeg uw Web-Apps-toepassing toe als een externe git.
   
   ```bash
   git remote add azure https://username@your-azure-website.scm.azurewebsites.net:443/your-azure-website.git
   ```

3. Implementeer de toepassing door deze naar de externe locatie te pushen.
   
   ```bash
   git push azure main
   ```

4. Uw webtoepassing wordt binnen enkele seconden gepubliceerd en gestart in een browser.

## <a name="clean-up-resources"></a>Resources opschonen

U kunt de resourcegroep, het Azure Cosmos DB-account en alle gerelateerde resources verwijderen wanneer u de resources niet meer nodig hebt. Hiertoe selecteert u de resourcegroep die u hebt gebruikt voor het Azure Cosmos DB-account en selecteert u **Verwijderen**. Vervolgens bevestigt u de naam van de resourcegroep die u wilt verwijderen.

## <a name="next-steps"></a><a name="_Toc395637775"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Mobiele toepassingen maken met Xamarin en Azure Cosmos DB](mobile-apps-with-xamarin.md)


[Node.js]: https://nodejs.org/
[Git]: https://git-scm.com/
[GitHub]: https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-todo-app