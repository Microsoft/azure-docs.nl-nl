---
title: Een Xamarin-app ontwikkelen met .NET en Azure Cosmos DB-API voor MongoDB
description: Deze snelstart bevat een voorbeeld van Xamarin-code dat u kunt gebruiken om verbinding te maken met de Azure Cosmos DB-API voor MongoDB en er query's op uit te voeren
author: codemillmatt
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 10/09/2020
ms.author: masoucou
ms.custom: devx-track-csharp
ms.openlocfilehash: 75db62b4f8a5c6512ca5fc84d149513fe81f6019
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "101658228"
---
# <a name="quickstart-build-a-xamarinforms-app-with-net-sdk-and-azure-cosmos-dbs-api-for-mongodb"></a>Snelstartgids: Een Xamarin.Forms-app ontwikkelen met de .NET SDK en Azure Cosmos DB-API voor MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Python](./mongodb-introduction.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-go.md)
>  

Azure Cosmos DB is de wereldwijd gedistribueerde multimodel-databaseservice van Microsoft. U kunt snel databases maken van documenten, sleutel/waarde-paren en grafieken en hier query’s op uitvoeren. Deze databases genieten allemaal het voordeel van de globale distributie en horizontale schaalmogelijkheden die ten grondslag liggen aan Azure Cosmos DB.

In deze snelstart laten we u zien hoe u een [Cosmos-account maakt dat is geconfigureerd met Azure Cosmos DB-API voor MongoDB](mongodb-introduction.md) en een documentdatabase en verzameling kunt maken met behulp van Azure Portal. U bouwt vervolgens een todo-app van de app Xamarin.Forms met behulp van het [MongoDB .NET-stuurprogramma](https://docs.mongodb.com/ecosystem/drivers/csharp/).

## <a name="prerequisites-to-run-the-sample-app"></a>Vereisten voor het uitvoeren van de voorbeeld-app

Als u het voorbeeld wilt uitvoeren, hebt u [Visual Studio](https://www.visualstudio.com/downloads/) of [Visual Studio voor Mac](https://visualstudio.microsoft.com/vs/mac/) en een geldig Azure Cosmos DB-account nodig.

Als u Visual Studio nog niet hebt, downloadt u [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/) waarbij de workload **Mobile development with .NET** tijdens het instellen wordt geïnstalleerd.

Als u liever op een Mac werkt, download dan [Visual Studio voor Mac](https://visualstudio.microsoft.com/vs/mac/) en voer de installatie uit.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

<a id="create-account"></a>

## <a name="create-a-database-account"></a>Een databaseaccount maken

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

Het voorbeeld dat in dit artikel wordt beschreven, is compatibel met MongoDB.Driver versie 2.6.1.

## <a name="clone-the-sample-app"></a>De voorbeeld-app klonen

Download eerst de voorbeeld-app uit GitHub. Deze implementeert een takenlijst-app met het documentopslagmodel van MongoDB.



# <a name="windows"></a>[Windows](#tab/windows)

1. Open in Windows een opdrachtprompt of op Mac de terminal, maak een nieuwe map met de naam git-samples en sluit het venster.

    ```batch
    md "C:\git-samples"
    ```

    ```bash
    mkdir '$home\git-samples\
    ```

2. Open een git-terminalvenster, bijvoorbeeld git bash, en gebruik de `cd`-opdracht om naar de nieuwe map te gaan voor het installeren van de voorbeeld-app.

    ```bash
    cd "C:\git-samples"
    ```

3. Voer de volgende opdracht uit om de voorbeeldopslagplaats te klonen. Deze opdracht maakt een kopie van de voorbeeld-app op uw computer.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-mongodb-xamarin-getting-started.git
    ```

Als u niet git wilt gebruiken, kunt u ook [het project als een ZIP-bestand downloaden](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-xamarin-getting-started/archive/master.zip)

## <a name="review-the-code"></a>De code bekijken

Deze stap is optioneel. Als u wilt weten hoe de databaseresources in de code worden gemaakt, kunt u de volgende codefragmenten bekijken. Als u deze stap wilt overslaan, kunt u verdergaan naar [Uw verbindingsreeks bijwerken](#update-your-connection-string).

De volgende codefragmenten zijn allemaal afkomstig uit de klasse `MongoService`, te vinden in het volgende pad: src/TaskList.Core/Services/MongoService.cs.

* Initialiseer de Mongo-client.
    ```cs
    MongoClientSettings settings = MongoClientSettings.FromUrl(new MongoUrl(APIKeys.ConnectionString));

    settings.SslSettings = new SslSettings() { EnabledSslProtocols = SslProtocols.Tls12 };

    settings.RetryWrites = false;

    MongoClient mongoClient = new MongoClient(settings);
    ```

* Haal een verwijzing naar de database en verzameling op. De MongoDB .NET-SDK maakt automatisch zowel de database als de verzameling, als deze nog niet bestaan.
    ```cs
    string dbName = "MyTasks";
    string collectionName = "TaskList";

    var db = mongoClient.GetDatabase(dbName);

    var collectionSettings = new MongoCollectionSettings 
    {
        ReadPreference = ReadPreference.Nearest
    };

    tasksCollection = db.GetCollection<MyTask>(collectionName, collectionSettings);
    ```
* Haal alle documenten op als een lijst.
    ```cs
    var allTasks = await TasksCollection
                    .Find(new BsonDocument())
                    .ToListAsync();
    ```

* Query voor specifieke documenten.
    ```cs
    public async Task<List<MyTask>> GetIncompleteTasksDueBefore(DateTime date)
    {
        var tasks = await TasksCollection
                        .AsQueryable()
                        .Where(t => t.Complete == false)
                        .Where(t => t.DueDate < date)
                        .ToListAsync();

        return tasks;
    }
    ```

* Maak een taak en voeg deze aan de verzameling toe.
    ```cs
    public async Task CreateTask(MyTask task)
    {
        await TasksCollection.InsertOneAsync(task);
    }
    ```

* Werk een taak bij in een verzameling.
    ```cs
    public async Task UpdateTask(MyTask task)
    {
        await TasksCollection.ReplaceOneAsync(t => t.Id.Equals(task.Id), task);
    }
    ```

* Verwijder een taak uit een verzameling.
    ```cs
    public async Task DeleteTask(MyTask task)
    {
        await TasksCollection.DeleteOneAsync(t => t.Id.Equals(task.Id));
    }
    ```

<a id="update-your-connection-string"></a>

## <a name="update-your-connection-string"></a>Uw verbindingsreeks bijwerken

Ga nu terug naar Azure Portal om de verbindingsreeksinformatie op te halen en kopieer deze in de app.

1. Klik in [Azure Portal](https://portal.azure.com/), in uw Azure Cosmos DB-account, in het linker navigatiegedeelte op **Verbindingsreeks** en klik vervolgens op **Sleutels voor lezen/schrijven**. In de volgende stappen gebruikt u de kopieerknoppen aan de rechterkant van het scherm om de Primaire verbindingsreeks te kopiëren.

2. Open het bestand **APIKeys.cs** in de directory **Helpers** van het project **TaskList.Core**.

3. Kopieer de waarde van uw **primaire verbindingsreeks** uit de portal (met behulp van de knop Kopiëren) en maak deze de waarde van het veld **ConnectionString** in uw **APIKeys.cs**-bestand.

4. Verwijder `&replicaSet=globaldb` uit de verbindingsreeks. U krijgt een runtime-fout als u die waarde niet uit de querytekenreeks verwijdert.

> [!IMPORTANT]
> U moet het sleutel/waarde-paar `&replicaSet=globaldb` uit de querytekenreeks van de verbindingsreeks verwijderen om een runtime-fout te voorkomen.

U hebt uw app nu bijgewerkt met alle informatie die nodig is voor de communicatie met Azure Cosmos DB.

## <a name="run-the-app"></a>De app uitvoeren

### <a name="visual-studio-2019"></a>Visual Studio 2019

1. Klik in Visual Studio met de rechtermuisknop op elk project in **Solution Explorer** en klik vervolgens op **NuGet-pakketten beheren**.
2. Klik op **Alle NuGet-pakketten herstellen**.
3. Klik met de rechtermuisknop op **TaskList.Android** en selecteer **Instellen als opstartproject**.
4. Druk op F5 om te beginnen met de foutopsporing van de toepassing.
5. Als u op iOS wilt werken, wordt uw machine eerst verbonden met een Mac (hier zijn [instructies](/xamarin/ios/get-started/installation/windows/introduction-to-xamarin-ios-for-visual-studio) over hoe u dat kunt doen).
6. Klik met de rechtermuisknop op **TaskList.iOS**-project en selecteer **Instellen als opstartproject**.
7. Klik op F5 om te beginnen met de foutopsporing van de toepassing.

### <a name="visual-studio-for-mac"></a>Visual Studio voor Mac

1. Selecteer in de platformvervolgkeuzelijst TaskList.iOS of TaskList.Android, afhankelijk van op welk platform u wilt werken.
2. Druk op cmd+Enter om te beginnen met de foutopsporing van de toepassing.

## <a name="review-slas-in-the-azure-portal"></a>SLA’s bekijken in Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u geleerd hoe een Azure Cosmos DB-account kunt maken en een Xamarin.Forms-app kunt uitvoeren met de API voor MongoDB. Nu kunt u aanvullende gegevens in uw Cosmos DB-account importeren.

> [!div class="nextstepaction"]
> [Gegevens importeren in Azure Cosmos DB die zijn geconfigureerd met Azure Cosmos DB-API voor MongoDB](../dms/tutorial-mongodb-cosmos-db.md?toc=%2fazure%2fcosmos-db%2ftoc.json%253ftoc%253d%2fazure%2fcosmos-db%2ftoc.json)