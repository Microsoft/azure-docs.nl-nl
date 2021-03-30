---
title: 'Quickstart: Cassandra-API met .NET Core - Azure Cosmos DB'
description: In deze snelstart ziet u hoe u de Cassandra-API in Azure Cosmos DB gebruikt om een profieltoepassing te maken met de Azure-portal en .NET Core
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
author: TheovanKraay
ms.author: thvankra
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 10/01/2020
ms.custom: devx-track-dotnet
ms.openlocfilehash: f228386d8cf0e708080b7f6c5f6cef7258b2eafb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93099977"
---
# <a name="quickstart-build-a-cassandra-app-with-net-core-and-azure-cosmos-db"></a>Quickstart: Een Cassandra-app bouwen met .NET Core en Azure Cosmos DB
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

> [!div class="op_single_selector"]
> * [.NET](create-cassandra-dotnet.md)
> * [.NET Core](create-cassandra-dotnet-core.md)
> * [Java v3](create-cassandra-java.md)
> * [Java v4](create-cassandra-java-v4.md)
> * [Node.js](create-cassandra-nodejs.md)
> * [Python](create-cassandra-python.md)
>  

Deze snelstart laat zien hoe u .NET Core en de [Cassandra-API](cassandra-introduction.md) van Azure Cosmos DB gebruikt om een profiel-app te maken door een voorbeeld van GitHub te klonen. In deze snelstart ziet u ook hoe u de webportal van Azure gebruikt om een Azure Cosmos DB-account te maken.

Azure Cosmos DB is de wereldwijd gedistribueerde multimodel-databaseservice van Microsoft. U kunt snel databases maken van documenten, sleutel/waarde-paren en grafieken en hier query's op uitvoeren. Deze databases genieten allemaal het voordeel van de wereldwijde distributie en horizontale schaalmogelijkheden die ten grondslag liggen aan Azure Cosmos DB. 

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)][Probeer Azure Cosmos DB gratis uit](https://azure.microsoft.com/try/cosmosdb/) zonder Azure-abonnement, zonder kosten en zonder verplichtingen.

U hebt verder nodig: 
* Als u Visual Studio 2019 nog niet hebt geïnstalleerd, kunt u het downloaden en de **gratis** [Community Edition van Visual Studio 2019](https://www.visualstudio.com/downloads/) gebruiken. Zorg ervoor dat u **Azure-ontwikkeling** inschakelt tijdens de installatie van Visual Studio.
* Installeer [Git](https://www.git-scm.com/) zodat u het voorbeeld kunt klonen.

<a id="create-account"></a>
## <a name="create-a-database-account"></a>Een databaseaccount maken

[!INCLUDE [cosmos-db-create-dbaccount-cassandra](../../includes/cosmos-db-create-dbaccount-cassandra.md)]


## <a name="clone-the-sample-application"></a>De voorbeeldtoepassing klonen

Nu gaan we werken met code. We gaan een Cassandra API-app klonen vanuit GitHub, de verbindingsreeks instellen en de app uitvoeren. U zult zien hoe gemakkelijk het is om op een programmatische manier met gegevens te werken. 

1. Open een opdrachtprompt. Maak een nieuwe map met de naam `git-samples`. Sluit vervolgens de opdrachtprompt.

    ```bash
    md "C:\git-samples"
    ```

2. Open een git-terminalvenster, bijvoorbeeld git bash, en gebruik de `cd`-opdracht om naar de nieuwe map te gaan voor het installeren van de voorbeeld-app.

    ```bash
    cd "C:\git-samples"
    ```

3. Voer de volgende opdracht uit om de voorbeeldopslagplaats te klonen. Deze opdracht maakt een kopie van de voorbeeld-app op uw computer.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-cassandra-dotnet-core-getting-started.git
    ```

4. Open vervolgens het oplossingsbestand CassandraQuickStartSample in Visual Studio. 

## <a name="review-the-code"></a>De code bekijken

Deze stap is optioneel. Als u wilt weten hoe de databaseresources met de code worden gemaakt, kunt u de volgende codefragmenten bekijken. De fragmenten zijn alle afkomstig uit het `Program.cs`-bestand binnen de methode `async Task ProcessAsync()`, dat is geïnstalleerd in de map `C:\git-samples\azure-cosmos-db-cassandra-dotnet-core-getting-started\CassandraQuickStart`. Als u deze stap wilt overslaan, kunt u verdergaan naar [Uw verbindingsreeks bijwerken](#update-your-connection-string).

* Initialiseer de sessie door verbinding te maken met een eindpunt van het Cassandra-cluster. De Cassandra-API voor Azure Cosmos DB biedt alleen ondersteuning voor TLSv1.2. 

  ```csharp
      var options = new Cassandra.SSLOptions(SslProtocols.Tls12, true, ValidateServerCertificate);
      options.SetHostNameResolver((ipAddress) => CASSANDRACONTACTPOINT);
      Cluster cluster = Cluster
          .Builder()
          .WithCredentials(USERNAME, PASSWORD)
          .WithPort(CASSANDRAPORT)
          .AddContactPoint(CASSANDRACONTACTPOINT)
          .WithSSL(options)
          .Build()
      ;
      ISession session = await cluster.ConnectAsync();
   ```

* Verwijder de bestaande keyspace, indien aanwezig.

    ```csharp
    await session.ExecuteAsync(new SimpleStatement("DROP KEYSPACE IF EXISTS uprofile")); 
    ```

* Maak een nieuwe keyspace.

    ```csharp
    await session.ExecuteAsync(new SimpleStatement("CREATE KEYSPACE uprofile WITH REPLICATION = { 'class' : 'NetworkTopologyStrategy', 'datacenter1' : 1 };"));
    ```

* Maak een nieuwe tabel.

   ```csharp
  await session.ExecuteAsync(new SimpleStatement("CREATE TABLE IF NOT EXISTS uprofile.user (user_id int PRIMARY KEY, user_name text, user_bcity text)"));
   ```

* Voeg gebruikersentiteiten toe met behulp van het IMapper-object in een nieuwe sessie die verbinding maakt met de uprofile-keyspace.

    ```csharp
    await mapper.InsertAsync<User>(new User(1, "LyubovK", "Dubai"));
    ```

* Voer een query uit voor het ophalen van de gebruikersgegevens van alle gebruikers.

    ```csharp
    foreach (User user in await mapper.FetchAsync<User>("Select * from user"))
    {
        Console.WriteLine(user);
    }
    ```

* Voer een query uit voor het ophalen van de gebruikersgegevens van één gebruiker.

    ```csharp
    mapper.FirstOrDefault<User>("Select * from user where user_id = ?", 3);
    ```

## <a name="update-your-connection-string"></a>Uw verbindingsreeks bijwerken

Ga nu terug naar Azure Portal om de verbindingsreeksinformatie op te halen en kopieer deze in de app. De verbindingsreeksinformatie stelt uw app in staat om te communiceren met de gehoste database.

1. Selecteer **Verbindingsreeks** in de [Azure-portal](https://portal.azure.com/).

1. Gebruik de knop :::image type="icon" source="./media/create-cassandra-dotnet/copy.png"::: aan de rechterkant van het scherm om de USERNAME-waarde te kopiëren.

   :::image type="content" source="./media/create-cassandra-dotnet/keys.png" alt-text="Een toegangssleutel in Azure Portal bekijken en kopiëren op de pagina Verbindingsreeks":::

1. Open in Visual Studio het bestand Program.cs. 

1. Plak de USERNAME-waarde uit de portal over `<PROVIDE>` op regel 13 heen.

    Regel 13 van USERNAME moet er nu als volgt uitzien 

    `private const string UserName = "cosmos-db-quickstart";`

    U kunt ook dezelfde waarde over `<PROVIDE>` op regel 15 plakken voor de CONTACT POINT-waarde:

    `private const string CassandraContactPoint = "cosmos-db-quickstarts.cassandra.cosmosdb.azure.com"; //  DnsName`

1. Ga terug naar de portal en kopieer de PASSWORD-waarde. Plak de PASSWORD-waarde uit de portal over `<PROVIDE>` op regel 14 heen.

    Regel 14 van Program.cs moet er nu als volgt uitzien 

    `private const string Password = "2Ggkr662ifxz2Mg...==";`

1. Ga terug naar de portal en kopieer de CONTACT POINT-waarde. Plak de CONTACT POINT-waarde uit de portal over `<PROVIDE>` op regel 16 heen.

    Regel 16 van Program.cs moet er nu als volgt uitzien 

    `private const string CASSANDRACONTACTPOINT = "quickstart-cassandra-api.cassandra.cosmos.azure.com";`

1. Sla het bestand Program.cs op.
    
## <a name="run-the-net-core-app"></a>De .NET Core-app uitvoeren

1. Selecteer in Visual Studio **Tools** > **NuGet Package Manager** > **Package Manager Console**.

2. Ga naar de opdrachtprompt en gebruik de volgende opdracht om het pakket NuGet van het .NET-stuurprogramma te installeren. 

    ```cmd
    Install-Package CassandraCSharpDriver
    ```
3. Druk op Ctrl+F5 om de toepassing uit te voeren. Uw app wordt in het consolevenster weergegeven. 

    :::image type="content" source="./media/create-cassandra-dotnet/output.png" alt-text="De uitvoer weergeven en controleren":::

    Druk op Ctrl+C om de uitvoering van het programma te stoppen en het consolevenster te sluiten. 
    
4. Open **Data Explorer** in de Azure-portal om deze nieuwe gegevens te bekijken, te wijzigen, een query erop uit te voeren of er iets anders mee te doen.

    :::image type="content" source="./media/create-cassandra-dotnet/data-explorer.png" alt-text="De gegevens weergeven in Data Explorer":::

## <a name="review-slas-in-the-azure-portal"></a>SLA’s bekijken in Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Volgende stappen

In deze snelstart hebt u geleerd hoe u een Azure Cosmos DB-account maakt, een container maakt met Data Explorer en een web-app uitvoert. Nu kunt u aanvullende gegevens in uw Cosmos DB-account importeren. 

> [!div class="nextstepaction"]
> [Cassandra-gegevens importeren in Azure Cosmos DB](cassandra-import-data.md)
