---
title: Met de API van Azure Cosmos DB voor MongoDB en de .NET-SDK een web-app maken| Microsoft Docs
description: Biedt een voorbeeld van .NET-code dat u kunt gebruiken om verbinding te maken met de Azure Cosmos DB-API's voor MongoDB en er query's op uit te voeren.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 10/15/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 5d3c73bdc107ce1971e2958073ad5c100c6bf6bd
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96349194"
---
# <a name="quickstart-build-a-net-web-app-using-azure-cosmos-dbs-api-for-mongodb"></a>Quickstart: Met de API van Azure Cosmos DB voor MongoDB een .NET-web-app maken 
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Python](create-mongodb-flask.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-go.md)
>  

Azure Cosmos DB is de snelle NoSQL-database van Microsoft, met open API's voor elke schaal. U kunt snel databases maken van documenten, sleutel/waarde-paren en grafieken en hier query's op uitvoeren. Deze databases genieten allemaal het voordeel van de globale distributie en horizontale schaalmogelijkheden die ten grondslag liggen aan Cosmos DB. 

In deze snelstart ziet u hoe u een Cosmos-account kunt maken met de [API van Azure Cosmos DB voor MongoDB](mongodb-introduction.md). Vervolgens ontwikkelt en implementeert u een web-app voor taaklijsten die met behulp van het [MongoDB .NET-stuurprogramma](https://docs.mongodb.com/ecosystem/drivers/csharp/) is gemaakt.

## <a name="prerequisites-to-run-the-sample-app"></a>Vereisten voor het uitvoeren van de voorbeeld-app

Als u het voorbeeld wilt uitvoeren, hebt u [Visual Studio](https://www.visualstudio.com/downloads/) en een geldig Azure Cosmos DB-account nodig.

Als u Visual Studio nog niet hebt, downloadt u [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/) waarbij de workload **ASP.NET-ontwikkeling en webontwikkeling** tijdens het instellen wordt geïnstalleerd.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 

<a id="create-account"></a>
## <a name="create-a-database-account"></a>Een databaseaccount maken

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

Het voorbeeld dat in dit artikel wordt beschreven, is compatibel met MongoDB.Driver versie 2.6.1.

## <a name="clone-the-sample-app"></a>De voorbeeld-app klonen

Download eerst de voorbeeld-app uit GitHub. 

1. Open een opdrachtprompt, maak een nieuwe map met de naam git-samples en sluit vervolgens de opdrachtprompt.

    ```bash
    mkdir "C:\git-samples"
    ```

2. Open een git-terminalvenster, bijvoorbeeld git bash, en gebruik de `cd`-opdracht om naar de nieuwe map te gaan voor het installeren van de voorbeeld-app.

    ```bash
    cd "C:\git-samples"
    ```

3. Voer de volgende opdracht uit om de voorbeeldopslagplaats te klonen. Deze opdracht maakt een kopie van de voorbeeld-app op uw computer. 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-getting-started.git
    ```

Als u niet git wilt gebruiken, kunt u ook [het project als een ZIP-bestand downloaden](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-getting-started/archive/master.zip).

## <a name="review-the-code"></a>De code bekijken

Deze stap is optioneel. Als u wilt weten hoe de databaseresources in de code worden gemaakt, kunt u de volgende codefragmenten bekijken. Als u deze stap wilt overslaan, kunt u verdergaan naar [Uw verbindingsreeks bijwerken](#update-your-connection-string). 

De volgende codefragmenten zijn alle overgenomen uit het bestand Dal.cs in de map DAL.

* Initialiseer de client.

    ```cs
        MongoClientSettings settings = new MongoClientSettings();
        settings.Server = new MongoServerAddress(host, 10255);
        settings.UseSsl = true;
        settings.SslSettings = new SslSettings();
        settings.SslSettings.EnabledSslProtocols = SslProtocols.Tls12;

        MongoIdentity identity = new MongoInternalIdentity(dbName, userName);
        MongoIdentityEvidence evidence = new PasswordEvidence(password);

        settings.Credential = new MongoCredential("SCRAM-SHA-1", identity, evidence);

        MongoClient client = new MongoClient(settings);
    ```

* Haal de database en de verzameling op.

    ```cs
    private string dbName = "Tasks";
    private string collectionName = "TasksList";

    var database = client.GetDatabase(dbName);
    var todoTaskCollection = database.GetCollection<MyTask>(collectionName);
    ```

* Haal alle documenten op.

    ```cs
    collection.Find(new BsonDocument()).ToList();
    ```

Een taak maken en deze aan de verzameling toevoegen

   ```csharp
    public void CreateTask(MyTask task)
    {
        var collection = GetTasksCollectionForEdit();
        try
        {
            collection.InsertOne(task);
        }
        catch (MongoCommandException ex)
        {
            string msg = ex.Message;
        }
    }
   ```
   Op dezelfde manier kunt u documenten bijwerken en verwijderen met behulp van de methoden [collection.UpdateOne()](https://docs.mongodb.com/stitch/mongodb/actions/collection.updateOne/index.html) en [collection.DeleteOne()](https://docs.mongodb.com/stitch/mongodb/actions/collection.deleteOne/index.html). 

## <a name="update-your-connection-string"></a>Uw verbindingsreeks bijwerken

Ga nu terug naar Azure Portal om de verbindingsreeksinformatie op te halen en kopieer deze in de app.

1. Klik in [Azure Portal](https://portal.azure.com/) in uw Azure Cosmos DB-account in het linker navigatiegedeelte op **Verbindingsreeks** en klik vervolgens op **Sleutels voor lezen/schrijven**. U gebruikt de kopieerknoppen aan de rechterkant van het scherm om de Gebruikersnaam, het Wachtwoord, en de Host naar het bestand Dal.cs te kopiëren. Dit doet u in de volgende stap.

2. Open het bestand **Dal.cs** in de map **DAL**. 

3. Kopieer uw **gebruikersnaam** van de portal (gebruik de kopieerknop) en geef deze waarde aan de **gebruikersnaam** in uw **Dal.cs**-bestand. 

4. Kopieer vervolgens de waarde van **host** van de portal en geef deze waarde aan de **host** in uw **Dal.cs**-bestand. 

5. Kopieer ten slotte de waarde van **wachtwoord** van de portal en geef deze waarde aan het **wachtwoord** in uw **Dal.cs**-bestand. 

U hebt uw app nu bijgewerkt met alle informatie die nodig is voor de communicatie met Cosmos DB. 
    
## <a name="run-the-web-app"></a>De web-app uitvoeren

1. Klik in Visual Studio met de rechtermuisknop op het project in **Solution Explorer** en klik vervolgens op **NuGet-pakketten beheren**. 

2. Typ in het vak **Bladeren** in NuGet *MongoDB.Driver*.

3. Installeer de bibliotheek **MongoDB.Driver** uit de lijst met resultaten. Hiermee wordt het MongoDB.Driver-pakket geïnstalleerd, met alle vereiste onderdelen.

4. Klik op Ctrl+F5 om de toepassing uit te voeren. Uw app wordt in uw browser weergegeven. 

5. Klik op **Maken** in de browser en maak een paar nieuwe taken in uw taaklijst-app.

## <a name="review-slas-in-the-azure-portal"></a>SLA’s bekijken in Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Volgende stappen

In deze snelstart hebt u geleerd hoe u een Cosmos DB-account kunt maken, hoe u een verzameling kunt maken en hoe u een console-app kunt uitvoeren. Nu kunt u aanvullende gegevens in uw Cosmos database importeren. 

> [!div class="nextstepaction"]
> [MongoDB-gegevens importeren in Azure Cosmos DB](../dms/tutorial-mongodb-cosmos-db.md?toc=%2fazure%2fcosmos-db%2ftoc.json%253ftoc%253d%2fazure%2fcosmos-db%2ftoc.json)