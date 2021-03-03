---
title: Gegevens kopiëren vanuit Azure Blob Storage naar Azure SQL Database
description: Deze zelfstudie bevat stapsgewijze instructies voor het kopiëren van gegevens uit Azure Blob Storage naar Azure SQL Database.
author: linda33wj
ms.service: data-factory
ms.topic: tutorial
ms.date: 02/18/2021
ms.author: jingwang
ms.openlocfilehash: 2a9e19f24c4889a194c85e33317860db1d3a68d2
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101736556"
---
# <a name="copy-data-from-azure-blob-to-azure-sql-database-using-azure-data-factory"></a>Gegevens met Azure Data Factory kopiëren van Azure Blob Storage naar SQL Database

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In deze zelfstudie maakt u een data factory met een pijplijn die gegevens kopieert van Azure Blob Storage naar Azure SQL Database. Het configuratiepatroon in deze zelfstudie geldt voor het kopiëren van een gegevensarchief op basis van bestanden naar een relationeel gegevensarchief. Zie [Ondersteunde gegevensarchieven en -indelingen](copy-activity-overview.md#supported-data-stores-and-formats) voor een lijst met gegevensarchieven die worden ondersteund als bron en als sink.

In deze zelfstudie voert u de volgende stappen uit:

> [!div class="checklist"]
> * Een data factory maken.
> * Gekoppelde Azure Storage- en Azure SQL Database-services maken.
> * Gegevenssets voor Azure Blob Storage en Azure SQL Database maken.
> * Een pijplijn met een kopieeractiviteit maken
> * Een pijplijnuitvoering starten
> * De uitvoering van de pijplijn en van de activiteit controleren

In deze zelfstudie wordt .NET SDK gebruikt. U kunt andere methoden gebruiken voor interactie met Azure Data Factory. Kijk voor voorbeelden onder **Quickstarts**.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis Azure-account](https://azure.microsoft.com/free/) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

* *Een Azure Storage-account*. U gebruikt de blob-opslag als *bron*-gegevensopslag. Zie [Een opslagaccount voor algemene doeleinden maken](../storage/common/storage-account-create.md) als u geen Azure Storage-account hebt.
* *Azure SQL-database*. U gebruikt de database als *sink*-gegevensopslag. Als u geen database in Azure SQL Database hebt, raadpleegt u het artikel [Een database in Azure SQL Database maken](../azure-sql/database/single-database-create-quickstart.md).
* *Visual Studio*. De procedures in dit artikel zijn gebaseerd op Visual Studio 2019.
* *[Azure-SDK voor .NET](/dotnet/azure/dotnet-tools)* .
* *Azure Active Directory-toepassing*. Als u geen Azure Active Directory-toepassing hebt, raadpleegt u de sectie [Een Azure Active Directory-toepassing maken](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal) van [Procedure: De portal gebruiken om een Azure AD-toepassing te maken](../active-directory/develop/howto-create-service-principal-portal.md). Kopieer de volgende waarden voor gebruik in latere stappen: **Toepassings-id (client-id)** , **Verificatiesleutel** en **Map-id (tenant-id)** . Wijs de toepassing toe aan de rol **Inzender** door de instructies in hetzelfde artikel te volgen.

### <a name="create-a-blob-and-a-sql-table"></a>Een blob en een SQL-tabel maken

Maak nu een bron-blob en een SQL-sink-tabel om Azure Blob Storage en Azure SQL Database voor te bereiden voor gebruik in deze zelfstudie:

#### <a name="create-a-source-blob"></a>Een bron-blob maken

Maak eerst een bron-blob door een container te maken en er een invoertekstbestand naar te uploaden:

1. Open Kladblok. Kopieer de volgende tekst en sla deze lokaal op in een bestand met de naam *inputEmp.txt*.

    ```inputEmp.txt
    John|Doe
    Jane|Doe
    ```

2. Gebruik een hulpprogramma zoals [Azure Opslagverkenner](https://azure.microsoft.com/features/storage-explorer/) om de container *adfv2tutorial* te maken en om het bestand *inputEmp.txt* naar de container te uploaden.

#### <a name="create-a-sink-sql-table"></a>Een SQL-sink-tabel maken

Maak vervolgens een SQL-sink-tabel:

1. Gebruik het volgende SQL-script om de tabel *dbo.emp* te maken in de Azure SQL-database.

    ```sql
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50)
    )
    GO

    CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID);
    ```

2. Geef Azure-services toegang tot SQL Database. Zorg ervoor dat u toegang tot Azure-services geeft in uw server, zodat de Data Factory-service gegevens kan schrijven naar SQL Database. Voer de volgende stappen uit om dit te controleren en de instelling in te schakelen:

    1. Ga naar de [Azure-portal](https://portal.azure.com) om uw SQL-server te beheren. Zoek en selecteer **SQL-servers**.

    2. Selecteer uw server.

    3. Selecteer onder de koptekst **Beveiliging** van het SQL-servermenu de optie **Firewalls en virtuele netwerken**.

    4. Selecteer op de pagina **Firewalls en virtuele netwerken** onder **Toestaan dat Azure-services en -resources toegang tot deze server krijgen** de optie **AAN**.

## <a name="create-a-visual-studio-project"></a>Een Visual Studio-project maken

Maak met behulp van Visual Studio een C# .NET-consoletoepassing.

1. Open Visual Studio.
2. Selecteer in het **Startvenster** de optie **Een nieuw project maken**.
3. Kies in het venster **Een nieuw project maken** de C#-versie van **Consoletoepassing (.NET Framework)** uit de lijst met projecttypen. Selecteer vervolgens **Volgende**.
4. Voer in het venster **Uw nieuwe project configureren** bij **Projectnaam** de naam *ADFv2Tutorial* in. Bij **Locatie** bladert u naar en/of maakt u de map waarin u het project wilt opslaan. Selecteer vervolgens **Maken**. Het nieuwe project verschijnt in de Visual Studio IDE.

## <a name="install-nuget-packages"></a>NuGet-pakketten installeren

Vervolgens installeert u de vereiste bibliotheekpakketten met behulp van de NuGet Package Manager.

1. Op de menubalk kiest u **Tools** > **NuGet Package Manager** > **Package Manager Console**.
2. Voer in het deelvenster **Package Manager Console** de volgende opdrachten uit om pakketten te installeren. Zie [Microsoft.Azure.Management.DataFactory](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactory/) voor informatie over het Azure Data Factory NuGet-pakket.

    ```package manager console
    Install-Package Microsoft.Azure.Management.DataFactory
    Install-Package Microsoft.Azure.Management.ResourceManager -PreRelease
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
    ```

## <a name="create-a-data-factory-client"></a>Een data factory-client maken

Volg deze stappen om een data factory-client te maken.

1. Open *Program.cs* en overschrijf de bestaande `using`-instructies met de volgende code om verwijzingen naar naamruimten toe te voegen.

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using Microsoft.Rest;
    using Microsoft.Rest.Serialization;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.DataFactory;
    using Microsoft.Azure.Management.DataFactory.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    ```

2. Voeg de volgende code toe aan de methode `Main` waarmee variabelen worden ingesteld. Vervang de 14 tijdelijke aanduidingen door uw eigen waarden.

    Zie [Beschikbare producten per regio](https://azure.microsoft.com/global-infrastructure/services/) om de lijst met Azure-regio’s te bekijken waarin Data Factory momenteel beschikbaar is. Kies in de vervolgkeuzelijst **Producten** de optie **Bladeren** > **Analytics** > **Data Factory**. Kies vervolgens in de vervolgkeuzelijst **Regio’s** de regio’s waarin u interesse hebt. Er verschijnt een raster met de beschikbaarheidsstatus van Data Factory-producten voor uw geselecteerde regio’s.

    > [!NOTE]
    > Gegevensarchieven (zoals Azure Storage en Azure SQL Database) en berekeningen (zoals HDInsight) die Data Factory gebruikt, kunnen zich in andere regio's bevinden dan wat u kiest voor Data Factory.

    ```csharp
    // Set variables
    string tenantID = "<your tenant ID>";
    string applicationId = "<your application ID>";
    string authenticationKey = "<your authentication key for the application>";
    string subscriptionId = "<your subscription ID to create the factory>";
    string resourceGroup = "<your resource group to create the factory>";

    string region = "<location to create the data factory in, such as East US>";
    string dataFactoryName = "<name of data factory to create (must be globally unique)>";

    // Specify the source Azure Blob information
    string storageAccount = "<your storage account name to copy data>";
    string storageKey = "<your storage account key>";
    string inputBlobPath = "adfv2tutorial/";
    string inputBlobName = "inputEmp.txt";

    // Specify the sink Azure SQL Database information
    string azureSqlConnString =
        "Server=tcp:<your server name>.database.windows.net,1433;" +
        "Database=<your database name>;" +
        "User ID=<your username>@<your server name>;" +
        "Password=<your password>;" +
        "Trusted_Connection=False;Encrypt=True;Connection Timeout=30";
    string azureSqlTableName = "dbo.emp";

    string storageLinkedServiceName = "AzureStorageLinkedService";
    string sqlDbLinkedServiceName = "AzureSqlDbLinkedService";
    string blobDatasetName = "BlobDataset";
    string sqlDatasetName = "SqlDataset";
    string pipelineName = "Adfv2TutorialBlobToSqlCopy";
    ```

3. Voeg de volgende code toe aan de methode `Main` waarmee een exemplaar van de klasse `DataFactoryManagementClient` wordt gemaakt. U gebruikt dit object om een data factory, een gekoppelde service, gegevenssets en een pijplijn te maken. U kunt dit object ook gebruiken om de details van de pijplijnuitvoering te controleren.

    ```csharp
    // Authenticate and create a data factory management client
    var context = new AuthenticationContext("https://login.windows.net/" + tenantID);
    ClientCredential cc = new ClientCredential(applicationId, authenticationKey);
    AuthenticationResult result = context.AcquireTokenAsync(
        "https://management.azure.com/", cc
    ).Result;
    ServiceClientCredentials cred = new TokenCredentials(result.AccessToken);
    var client = new DataFactoryManagementClient(cred) { SubscriptionId = subscriptionId };
    ```

## <a name="create-a-data-factory"></a>Een gegevensfactory maken

Voeg de volgende code toe aan de methode `Main` waarmee een *data factory* wordt gemaakt.

```csharp
// Create a data factory
Console.WriteLine("Creating a data factory " + dataFactoryName + "...");
Factory dataFactory = new Factory
{
    Location = region,
    Identity = new FactoryIdentity()
};

client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, dataFactory);
Console.WriteLine(
    SafeJsonConvert.SerializeObject(dataFactory, client.SerializationSettings)
);

while (
    client.Factories.Get(
        resourceGroup, dataFactoryName
    ).ProvisioningState == "PendingCreation"
)
{
    System.Threading.Thread.Sleep(1000);
}
```

## <a name="create-linked-services"></a>Gekoppelde services maken

In deze zelfstudie maakt u twee gekoppelde services voor respectievelijk de bron en de sink.

### <a name="create-an-azure-storage-linked-service"></a>Een gekoppelde Azure Storage-service maken

Voeg de volgende code toe aan de methode `Main` waarmee een *gekoppelde Azure Storage-service* wordt gemaakt. Zie [Eigenschappen van een gekoppelde Azure Storage-service](connector-azure-blob-storage.md#linked-service-properties) voor informatie over ondersteunde eigenschappen en details.

```csharp
// Create an Azure Storage linked service
Console.WriteLine("Creating linked service " + storageLinkedServiceName + "...");

LinkedServiceResource storageLinkedService = new LinkedServiceResource(
    new AzureStorageLinkedService
    {
        ConnectionString = new SecureString(
            "DefaultEndpointsProtocol=https;AccountName=" + storageAccount +
            ";AccountKey=" + storageKey
        )
    }
);

client.LinkedServices.CreateOrUpdate(
    resourceGroup, dataFactoryName, storageLinkedServiceName, storageLinkedService
);
Console.WriteLine(
    SafeJsonConvert.SerializeObject(storageLinkedService, client.SerializationSettings)
);
```

### <a name="create-an-azure-sql-database-linked-service"></a>Een gekoppelde Azure SQL Database-service maken

Voeg de volgende code toe aan de methode `Main` waarmee een *gekoppelde Azure SQL Database-service* wordt gemaakt. Zie [Eigenschappen van een gekoppelde Azure SQL Database-service](connector-azure-sql-database.md#linked-service-properties) voor informatie over ondersteunde eigenschappen en details.

```csharp
// Create an Azure SQL Database linked service
Console.WriteLine("Creating linked service " + sqlDbLinkedServiceName + "...");

LinkedServiceResource sqlDbLinkedService = new LinkedServiceResource(
    new AzureSqlDatabaseLinkedService
    {
        ConnectionString = new SecureString(azureSqlConnString)
    }
);

client.LinkedServices.CreateOrUpdate(
    resourceGroup, dataFactoryName, sqlDbLinkedServiceName, sqlDbLinkedService
);
Console.WriteLine(
    SafeJsonConvert.SerializeObject(sqlDbLinkedService, client.SerializationSettings)
);
```

## <a name="create-datasets"></a>Gegevenssets maken

In deze sectie maakt u twee gegevenssets: één voor de bron en de andere voor de sink.

### <a name="create-a-dataset-for-source-azure-blob"></a>Een gegevensset maken voor de brongegevens in Azure Blob

Voeg de volgende code toe aan de methode `Main` waarmee een *Azure Blob-gegevensset* wordt gemaakt. Zie [Eigenschappen van een Azure Blob-gegevensset](connector-azure-blob-storage.md#dataset-properties) voor informatie over ondersteunde eigenschappen en details.

U definieert een gegevensset die de brongegevens in Azure Blob vertegenwoordigt. Deze Blob-gegevensset verwijst naar de gekoppelde Azure Storage-service die u in de vorige stap hebt gemaakt en beschrijft het volgende:

- De locatie van de blob waaruit moet worden gekopieerd: `FolderPath` en `FileName`
- De blob-indeling die aangeeft hoe de inhoud moet worden geparseerd: `TextFormat` en de bijbehorende instellingen, zoals het kolomscheidingsteken
- De gegevensstructuur, inclusief kolomnamen en gegevenstypen, die in dit voorbeeld worden gekoppeld aan de SQL-sink-tabel

```csharp
// Create an Azure Blob dataset
Console.WriteLine("Creating dataset " + blobDatasetName + "...");
DatasetResource blobDataset = new DatasetResource(
    new AzureBlobDataset
    {
        LinkedServiceName = new LinkedServiceReference {
            ReferenceName = storageLinkedServiceName
        },
        FolderPath = inputBlobPath,
        FileName = inputBlobName,
        Format = new TextFormat { ColumnDelimiter = "|" },
        Structure = new List<DatasetDataElement>
        {
            new DatasetDataElement { Name = "FirstName", Type = "String" },
            new DatasetDataElement { Name = "LastName", Type = "String" }
        }
    }
);

client.Datasets.CreateOrUpdate(
    resourceGroup, dataFactoryName, blobDatasetName, blobDataset
);
Console.WriteLine(
    SafeJsonConvert.SerializeObject(blobDataset, client.SerializationSettings)
);
```

### <a name="create-a-dataset-for-sink-azure-sql-database"></a>Een gegevensset maken voor de sink-gegevens in Azure SQL Database

Voeg de volgende code toe aan de methode `Main` waarmee een *Azure SQL Database-gegevensset* wordt gemaakt. Zie [Eigenschappen van een Azure SQL Database-gegevensset](connector-azure-sql-database.md#dataset-properties) voor informatie over ondersteunde eigenschappen en details.

U definieert een gegevensset die de sink-gegevens in Azure SQL Database vertegenwoordigt. Deze gegevensset verwijst naar de gekoppelde Azure SQL Database-service die u in de vorige stap hebt gemaakt. De gegevensset geeft ook aan in welke SQL-tabel de gekopieerde gegevens worden opgeslagen.

```csharp
// Create an Azure SQL Database dataset
Console.WriteLine("Creating dataset " + sqlDatasetName + "...");
DatasetResource sqlDataset = new DatasetResource(
    new AzureSqlTableDataset
    {
        LinkedServiceName = new LinkedServiceReference
        {
            ReferenceName = sqlDbLinkedServiceName
        },
        TableName = azureSqlTableName
    }
);

client.Datasets.CreateOrUpdate(
    resourceGroup, dataFactoryName, sqlDatasetName, sqlDataset
);
Console.WriteLine(
    SafeJsonConvert.SerializeObject(sqlDataset, client.SerializationSettings)
);
```

## <a name="create-a-pipeline"></a>Een pijplijn maken

Voeg de volgende code toe aan de methode `Main` waarmee een *pijplijn met een kopieeractiviteit* wordt gemaakt. In deze zelfstudie bevat deze pijplijn maar één activiteit: `CopyActivity`, waarbij de blob-gegevensset als bron wordt gebruikt en de SQL-gegevensset als sink. Zie [Kopieeractiviteit in Azure Data Factory](copy-activity-overview.md) voor informatie over kopieeractiviteitsdetails.

```csharp
// Create a pipeline with copy activity
Console.WriteLine("Creating pipeline " + pipelineName + "...");
PipelineResource pipeline = new PipelineResource
{
    Activities = new List<Activity>
    {
        new CopyActivity
        {
            Name = "CopyFromBlobToSQL",
            Inputs = new List<DatasetReference>
            {
                new DatasetReference() { ReferenceName = blobDatasetName }
            },
            Outputs = new List<DatasetReference>
            {
                new DatasetReference { ReferenceName = sqlDatasetName }
            },
            Source = new BlobSource { },
            Sink = new SqlSink { }
        }
    }
};

client.Pipelines.CreateOrUpdate(resourceGroup, dataFactoryName, pipelineName, pipeline);
Console.WriteLine(
    SafeJsonConvert.SerializeObject(pipeline, client.SerializationSettings)
);
```

## <a name="create-a-pipeline-run"></a>Een pijplijnuitvoering maken

Voeg de volgende code toe aan de methode `Main`waarmee een *pijplijnuitvoering wordt geactiveerd*.

```csharp
// Create a pipeline run
Console.WriteLine("Creating pipeline run...");
CreateRunResponse runResponse = client.Pipelines.CreateRunWithHttpMessagesAsync(
    resourceGroup, dataFactoryName, pipelineName
).Result.Body;
Console.WriteLine("Pipeline run ID: " + runResponse.RunId);
```

## <a name="monitor-a-pipeline-run"></a>Een pijplijnuitvoering controleren

Voeg nu de code in om statussen van de pijplijnuitvoering te controleren en details van de uitvoering van de kopieeractiviteit te verkrijgen.

1. Voeg de volgende code toe aan de methode `Main` om continu de statussen van de pijplijnuitvoering te controleren totdat deze klaar is met het kopiëren van de gegevens.

    ```csharp
    // Monitor the pipeline run
    Console.WriteLine("Checking pipeline run status...");
    PipelineRun pipelineRun;
    while (true)
    {
        pipelineRun = client.PipelineRuns.Get(
            resourceGroup, dataFactoryName, runResponse.RunId
        );
        Console.WriteLine("Status: " + pipelineRun.Status);
        if (pipelineRun.Status == "InProgress")
            System.Threading.Thread.Sleep(15000);
        else
            break;
    }
    ```

2. Voeg de volgende code toe aan de methode `Main` om details van de kopieeractiviteit weer te geven, zoals de omvang van de gelezen of weggeschreven gegevens.

    ```csharp
    // Check the copy activity run details
    Console.WriteLine("Checking copy activity run details...");

    RunFilterParameters filterParams = new RunFilterParameters(
        DateTime.UtcNow.AddMinutes(-10), DateTime.UtcNow.AddMinutes(10)
    );

    ActivityRunsQueryResponse queryResponse = client.ActivityRuns.QueryByPipelineRun(
        resourceGroup, dataFactoryName, runResponse.RunId, filterParams
    );

    if (pipelineRun.Status == "Succeeded")
    {
        Console.WriteLine(queryResponse.Value.First().Output);
    }
    else
        Console.WriteLine(queryResponse.Value.First().Error);

    Console.WriteLine("\nPress any key to exit...");
    Console.ReadKey();
    ```

## <a name="run-the-code"></a>De code uitvoeren

Bouw de toepassing door **Bouwen** > **Oplossing bouwen** te kiezen. Start de toepassing vervolgens door **Fouten opsporen** > **Foutopsporing starten** te kiezen, en verifieer de pijplijnuitvoering.

In de console ziet u de voortgang van het maken van een data factory, een gekoppelde service, gegevenssets, pijplijn en pijplijnuitvoering. Vervolgens wordt de uitvoeringsstatus van de pijplijn gecontroleerd. Wacht totdat u details ziet van de uitvoering van de kopieeractiviteit, waaronder de omvang van de gelezen/weggeschreven gegevens. Vervolgens kunt u met hulpprogramma's zoals SQL Server Management Studio (SSMS) of Visual Studio verbinding maken met de database van Azure SQL Database en controleren of de opgegeven doeltabel de gekopieerde gegevens bevat.

### <a name="sample-output"></a>Voorbeelduitvoer

```json
Creating a data factory AdfV2Tutorial...
{
  "identity": {
    "type": "SystemAssigned"
  },
  "location": "East US"
}
Creating linked service AzureStorageLinkedService...
{
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": {
        "type": "SecureString",
        "value": "DefaultEndpointsProtocol=https;AccountName=<accountName>;AccountKey=<accountKey>"
      }
    }
  }
}
Creating linked service AzureSqlDbLinkedService...
{
  "properties": {
    "type": "AzureSqlDatabase",
    "typeProperties": {
      "connectionString": {
        "type": "SecureString",
        "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
      }
    }
  }
}
Creating dataset BlobDataset...
{
  "properties": {
    "type": "AzureBlob",
    "typeProperties": {
      "folderPath": "adfv2tutorial/",
      "fileName": "inputEmp.txt",
      "format": {
        "type": "TextFormat",
        "columnDelimiter": "|"
      }
    },
    "structure": [
      {
        "name": "FirstName",
        "type": "String"
      },
      {
        "name": "LastName",
        "type": "String"
      }
    ],
    "linkedServiceName": {
      "type": "LinkedServiceReference",
      "referenceName": "AzureStorageLinkedService"
    }
  }
}
Creating dataset SqlDataset...
{
  "properties": {
    "type": "AzureSqlTable",
    "typeProperties": {
      "tableName": "dbo.emp"
    },
    "linkedServiceName": {
      "type": "LinkedServiceReference",
      "referenceName": "AzureSqlDbLinkedService"
    }
  }
}
Creating pipeline Adfv2TutorialBlobToSqlCopy...
{
  "properties": {
    "activities": [
      {
        "type": "Copy",
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "SqlSink"
          }
        },
        "inputs": [
          {
            "type": "DatasetReference",
            "referenceName": "BlobDataset"
          }
        ],
        "outputs": [
          {
            "type": "DatasetReference",
            "referenceName": "SqlDataset"
          }
        ],
        "name": "CopyFromBlobToSQL"
      }
    ]
  }
}
Creating pipeline run...
Pipeline run ID: 1cd03653-88a0-4c90-aabc-ae12d843e252
Checking pipeline run status...
Status: InProgress
Status: InProgress
Status: Succeeded
Checking copy activity run details...
{
  "dataRead": 18,
  "dataWritten": 28,
  "rowsCopied": 2,
  "copyDuration": 2,
  "throughput": 0.01,
  "errors": [],
  "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)",
  "usedDataIntegrationUnits": 2,
  "billedDuration": 2
}

Press any key to exit...
```

## <a name="next-steps"></a>Volgende stappen

Met de pijplijn in dit voorbeeld worden gegevens gekopieerd van de ene locatie naar een andere locatie in een Azure Blob-opslag. U hebt geleerd hoe u:

> [!div class="checklist"]
> * Een data factory maken.
> * Gekoppelde Azure Storage- en Azure SQL Database-services maken.
> * Gegevenssets voor Azure Blob Storage en Azure SQL Database maken.
> * Een pijplijn met een kopieeractiviteit maken.
> * Een pijplijnuitvoering starten.
> * De uitvoering van de pijplijn en van de activiteit controleren.

Ga verder met de volgende zelfstudie als u wilt weten hoe u on-premises gegevens kopieert naar de cloud:

> [!div class="nextstepaction"]
>[On-premises gegevens kopiëren naar de cloud](tutorial-hybrid-copy-powershell.md)
