---
title: Een gegevensfactory in Azure maken met behulp van .NET SDK
description: Maak een Azure-gegevensfactory en pijplijn met .NET SDK om gegevens te kopiëren van de ene locatie in Azure Blob Storage naar de andere.
author: linda33wj
ms.service: data-factory
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 03/27/2021
ms.author: jingwang
ms.openlocfilehash: 3c667fe20b392bfb52b8300ce4b8b59d15a13b9a
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/05/2021
ms.locfileid: "106385425"
---
# <a name="quickstart-create-a-data-factory-and-pipeline-using-net-sdk"></a>Quickstart: Een data factory en pijplijn maken met behulp van .NET SDK

> [!div class="op_single_selector" title1="Selecteer de versie van de Data Factory-service die u gebruikt:"]
> * [Versie 1:](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Huidige versie](quickstart-create-data-factory-dot-net.md)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In deze quickstart wordt beschreven hoe u .NET SDK kunt gebruiken om een Azure-gegevensfactory te maken. Met de pijplijn die u in deze data factory maakt, worden gegevens **gekopieerd** van één map naar een andere map in een Azure Blob Storage. Meer informatie over het **transformeren** van gegevens met behulp van Azure Data Factory vindt u in [Zelfstudie: Gegevens transformeren met Spark](tutorial-transform-data-spark-portal.md).

> [!NOTE]
> Dit artikel is geen gedetailleerde introductie tot de Data Factory-service. Zie [Inleiding tot Azure Data Factory](introduction.md) voor een inleiding tot Azure Data Factory-service.

[!INCLUDE [data-factory-quickstart-prerequisites](../../includes/data-factory-quickstart-prerequisites.md)] 

### <a name="visual-studio"></a>Visual Studio

De procedures in dit artikel zijn gebaseerd op Visual Studio 2019. De procedures voor Visual Studio 2013, 2015 en 2017 verschillen enigszins.

## <a name="create-an-application-in-azure-active-directory"></a>Een toepassing maken in Azure Active Directory

Volg in de secties in *Procedure: de portal gebruiken voor het maken van een Azure AD-toepassing en service-principal die toegang hebben tot resources* de instructies om de volgende taken uit te voeren:

1. Maak in [Een Azure Active Directory-toepassing maken](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal) een toepassing in Azure Active Directory die staat voor de .NET-toepassing die u in deze zelfstudie maakt. Voor de aanmeldings-URL kunt u een dummy-URL opgeven, zoals wordt getoond in het artikel (`https://contoso.org/exampleapp`).
2. Haal in [Waarden ophalen voor aanmelden](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in) de **toepassings-id** en **tenant-id** op, en noteer deze waarden zodat u ze later in deze zelfstudie kunt gebruiken. 
3. Haal in [Certificaten en geheimen](../active-directory/develop/howto-create-service-principal-portal.md#authentication-two-options) de **verificatiesleutel** op, en noteer deze waarde zodat u deze later in deze zelfstudie kunt gebruiken.
4. Wijs in [De toepassing toewijzen aan een rol](../active-directory/develop/howto-create-service-principal-portal.md#assign-a-role-to-the-application) de toepassing toe aan de rol **Inzender** op het niveau van het abonnement, zodat met de toepassing gegevensfactory's in het abonnement kunnen worden gemaakt.

## <a name="create-a-visual-studio-project"></a>Een Visual Studio-project maken

Maak vervolgens een C# .NET-consoletoepassing in Visual Studio:

1. Start **Visual Studio**.
2. Selecteer in het Startvenster de optie **Een nieuw project maken** > **Console-app (.NET Framework)** . .NET versie 4.5.2 of hoger is vereist.
3. Voer **ADFv2QuickStart** in bij **Projectnaam**.
4. Selecteer **Maken** om het project te maken.

## <a name="install-nuget-packages"></a>NuGet-pakketten installeren

1. Selecteer **Hulpprogramma's** > **NuGet Package Manager** > **Package Manager-console**.
2. Voer in het deelvenster **Package Manager Console** de volgende opdrachten uit om pakketten te installeren. Zie voor meer informatie het [NuGet-pakket Microsoft.Azure.Management.DataFactory](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactory/).

    ```powershell
    Install-Package Microsoft.Azure.Management.DataFactory
    Install-Package Microsoft.Azure.Management.ResourceManager -IncludePrerelease
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
    ```

## <a name="create-a-data-factory-client"></a>Een data factory-client maken

1. Open **Program.cs** en neem de volgende instructies op om verwijzingen naar naamruimten toe te voegen.

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

2. Voeg de volgende code toe aan de methode **Main** waarmee de variabelen worden ingesteld. Vervang de plaatsaanduidingen door uw eigen waarden. Voor een lijst met Azure-regio's waarin Data Factory momenteel beschikbaar is, selecteert u op de volgende pagina de regio's waarin u geïnteresseerd bent, vouwt u vervolgens **Analytics** uit en gaat u naar **Data Factory**: [Beschikbare producten per regio](https://azure.microsoft.com/global-infrastructure/services/). De gegevensopslagplaatsen (Azure Storage, Azure SQL Database, enzovoort) en berekeningen (HDInsight, enzovoort) die worden gebruikt in Data Factory, kunnen zich in andere regio's bevinden.

   ```csharp
   // Set variables
   string tenantID = "<your tenant ID>";
   string applicationId = "<your application ID>";
   string authenticationKey = "<your authentication key for the application>";
   string subscriptionId = "<your subscription ID where the data factory resides>";
   string resourceGroup = "<your resource group where the data factory resides>";
   string region = "<the location of your resource group>";
   string dataFactoryName = 
       "<specify the name of data factory to create. It must be globally unique.>";
   string storageAccount = "<your storage account name to copy data>";
   string storageKey = "<your storage account key>";
   // specify the container and input folder from which all files 
   // need to be copied to the output folder. 
   string inputBlobPath =
       "<path to existing blob(s) to copy data from, e.g. containername/inputdir>";
   //specify the contains and output folder where the files are copied
   string outputBlobPath =
       "<the blob path to copy data to, e.g. containername/outputdir>";

   // name of the Azure Storage linked service, blob dataset, and the pipeline
   string storageLinkedServiceName = "AzureStorageLinkedService";
   string blobDatasetName = "BlobDataset";
   string pipelineName = "Adfv2QuickStartPipeline";
   ```
> [!NOTE]
> Voor onafhankelijke Clouds moet u de juiste Cloud-specifieke eind punten voor ActiveDirectoryAuthority en ResourceManagerUrl (BaseUri) gebruiken. In azure gov gebruikt u bijvoorbeeld de instantie van in https://login.microsoftonline.us plaats van https://login.microsoftonline.com , en gebruikt u in https://management.usgovcloudapi.net plaats van https://management.azure.com/ , en maakt u vervolgens de Data Factory-beheer-client. U kunt Power shell gebruiken om de eind punt-Url's voor verschillende Clouds eenvoudig op te halen door ' Get-AzEnvironment | Format-List, waarmee een lijst met eind punten voor elke cloud omgeving wordt geretourneerd.

3. Voeg de volgende code toe aan de methode **Main** om een instantie van de klasse **DataFactoryManagementClient** te maken. U gebruikt dit object om een data factory, een gekoppelde service, gegevenssets en een pijplijn te maken. U kunt dit object ook gebruiken om de details van de pijplijnuitvoering te controleren.

   ```csharp
   // Authenticate and create a data factory management client
   var context = new AuthenticationContext("https://login.microsoftonline.com/" + tenantID);
   ClientCredential cc = new ClientCredential(applicationId, authenticationKey);
   AuthenticationResult result = context.AcquireTokenAsync(
       "https://management.azure.com/", cc).Result;
   ServiceClientCredentials cred = new TokenCredentials(result.AccessToken);
   var client = new DataFactoryManagementClient(cred) {
       SubscriptionId = subscriptionId };
   ```


## <a name="create-a-data-factory"></a>Een gegevensfactory maken

Voeg de volgende code toe aan de methode **Main** om een **data factory** te maken. 

```csharp
// Create a data factory
Console.WriteLine("Creating data factory " + dataFactoryName + "...");
Factory dataFactory = new Factory
{
    Location = region,
    Identity = new FactoryIdentity()
};
client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, dataFactory);
Console.WriteLine(
    SafeJsonConvert.SerializeObject(dataFactory, client.SerializationSettings));

while (client.Factories.Get(resourceGroup, dataFactoryName).ProvisioningState ==
       "PendingCreation")
{
    System.Threading.Thread.Sleep(1000);
}
```

## <a name="create-a-linked-service"></a>Een gekoppelde service maken

Voeg de volgende code toe aan de methode **Main** om een **gekoppelde Azure Storage-service** te maken.

U maakt gekoppelde services in een gegevensfactory om uw gegevensarchieven en compute-services aan de gegevensfactory te koppelen. In deze quickstart hoeft u maar één gekoppelde Azure Storage-service te maken, die wordt gebruikt als de bron voor het kopiëren en als de sinkopslag; deze heet in het voorbeeld 'AzureStorageLinkedService'.

```csharp
// Create an Azure Storage linked service
Console.WriteLine("Creating linked service " + storageLinkedServiceName + "...");

LinkedServiceResource storageLinkedService = new LinkedServiceResource(
    new AzureStorageLinkedService
    {
        ConnectionString = new SecureString(
            "DefaultEndpointsProtocol=https;AccountName=" + storageAccount +
            ";AccountKey=" + storageKey)
    }
);
client.LinkedServices.CreateOrUpdate(
    resourceGroup, dataFactoryName, storageLinkedServiceName, storageLinkedService);
Console.WriteLine(SafeJsonConvert.SerializeObject(
    storageLinkedService, client.SerializationSettings));
```

## <a name="create-a-dataset"></a>Een gegevensset maken

Voeg de volgende code toe aan de methode **Main** om een **Azure Blob Storage-gegevensset** te maken.

U definieert een gegevensset die de gegevens vertegenwoordigt die van een bron naar een sink moeten worden gekopieerd. In dit voorbeeld verwijst deze Blob-gegevensset naar de gekoppelde Azure Storage-service die u in de vorige stap gemaakt hebt. De gegevensset heeft een parameter nodig waarvan de waarde wordt ingesteld in een activiteit die de gegevensset verbruikt. De parameter wordt gebruikt om het 'folderPath' te maken dat verwijst naar de plaats waar de gegevens zich bevinden/zijn opgeslagen.

```csharp
// Create an Azure Blob dataset
Console.WriteLine("Creating dataset " + blobDatasetName + "...");
DatasetResource blobDataset = new DatasetResource(
    new AzureBlobDataset
    {
        LinkedServiceName = new LinkedServiceReference
        {
            ReferenceName = storageLinkedServiceName
        },
        FolderPath = new Expression { Value = "@{dataset().path}" },
        Parameters = new Dictionary<string, ParameterSpecification>
        {
            { "path", new ParameterSpecification { Type = ParameterType.String } }
        }
    }
);
client.Datasets.CreateOrUpdate(
    resourceGroup, dataFactoryName, blobDatasetName, blobDataset);
Console.WriteLine(
    SafeJsonConvert.SerializeObject(blobDataset, client.SerializationSettings));
```

## <a name="create-a-pipeline"></a>Een pijplijn maken

Voeg de volgende code toe aan de methode **Main** om **een pijplijn met een kopieeractiviteit te maken**.

In dit voorbeeld bevat deze pijplijn één activiteit en heeft deze twee parameters: het pad van de invoerblob en het pad van de uitvoerblob. De waarden voor deze parameters worden ingesteld wanneer de pijplijn wordt geactiveerd of uitgevoerd. De kopieeractiviteit verwijst naar dezelfde blobgegevensset die u in de vorige stap heb gemaakt als invoer en uitvoer. Wanneer de dataset wordt gebruikt als invoergegevensset, wordt het invoerpad opgegeven. En wanneer de dataset wordt gebruikt als uitvoergegevensset, wordt het uitvoerpad opgegeven. 

```csharp
// Create a pipeline with a copy activity
Console.WriteLine("Creating pipeline " + pipelineName + "...");
PipelineResource pipeline = new PipelineResource
{
    Parameters = new Dictionary<string, ParameterSpecification>
    {
        { "inputPath", new ParameterSpecification { Type = ParameterType.String } },
        { "outputPath", new ParameterSpecification { Type = ParameterType.String } }
    },
    Activities = new List<Activity>
    {
        new CopyActivity
        {
            Name = "CopyFromBlobToBlob",
            Inputs = new List<DatasetReference>
            {
                new DatasetReference()
                {
                    ReferenceName = blobDatasetName,
                    Parameters = new Dictionary<string, object>
                    {
                        { "path", "@pipeline().parameters.inputPath" }
                    }
                }
            },
            Outputs = new List<DatasetReference>
            {
                new DatasetReference
                {
                    ReferenceName = blobDatasetName,
                    Parameters = new Dictionary<string, object>
                    {
                        { "path", "@pipeline().parameters.outputPath" }
                    }
                }
            },
            Source = new BlobSource { },
            Sink = new BlobSink { }
        }
    }
};
client.Pipelines.CreateOrUpdate(resourceGroup, dataFactoryName, pipelineName, pipeline);
Console.WriteLine(SafeJsonConvert.SerializeObject(pipeline, client.SerializationSettings));
```

## <a name="create-a-pipeline-run"></a>Een pijplijnuitvoering maken

Voeg de volgende code toe aan de methode **Main** om een **pijplijnuitvoering te activeren**.

Met deze code worden ook de waarden van de parameters **inputPath** en **outputPath** die in de pijplijn worden opgegeven, ingesteld op de werkelijke waarden van de bron- en sinkblobpaden.

```csharp
// Create a pipeline run
Console.WriteLine("Creating pipeline run...");
Dictionary<string, object> parameters = new Dictionary<string, object>
{
    { "inputPath", inputBlobPath },
    { "outputPath", outputBlobPath }
};
CreateRunResponse runResponse = client.Pipelines.CreateRunWithHttpMessagesAsync(
    resourceGroup, dataFactoryName, pipelineName, parameters: parameters
).Result.Body;
Console.WriteLine("Pipeline run ID: " + runResponse.RunId);
```

## <a name="monitor-a-pipeline-run"></a>Een pijplijnuitvoering controleren

1. Voeg de volgende code toe aan de methode **Main** om continu de status te controleren totdat het kopiëren van de gegevens is voltooid.

   ```csharp
   // Monitor the pipeline run
   Console.WriteLine("Checking pipeline run status...");
   PipelineRun pipelineRun;
   while (true)
   {
       pipelineRun = client.PipelineRuns.Get(
           resourceGroup, dataFactoryName, runResponse.RunId);
       Console.WriteLine("Status: " + pipelineRun.Status);
       if (pipelineRun.Status == "InProgress" || pipelineRun.Status == "Queued")
           System.Threading.Thread.Sleep(15000);
       else
           break;
   }
   ```

2. Voeg de volgende code toe aan de methode **Main** om details van de kopieeractiviteit weer te geven, zoals de omvang van de gelezen of weggeschreven gegevens.

   ```csharp
   // Check the copy activity run details
   Console.WriteLine("Checking copy activity run details...");

   RunFilterParameters filterParams = new RunFilterParameters(
       DateTime.UtcNow.AddMinutes(-10), DateTime.UtcNow.AddMinutes(10));
   ActivityRunsQueryResponse queryResponse = client.ActivityRuns.QueryByPipelineRun(
       resourceGroup, dataFactoryName, runResponse.RunId, filterParams);
   if (pipelineRun.Status == "Succeeded")
       Console.WriteLine(queryResponse.Value.First().Output);
   else
       Console.WriteLine(queryResponse.Value.First().Error);
   Console.WriteLine("\nPress any key to exit...");
   Console.ReadKey();
   ```

## <a name="run-the-code"></a>De code uitvoeren

Bouw en start de toepassing en controleer vervolgens de uitvoering van de pijplijn.

In de console wordt de voortgang weergegeven van het maken van een data factory, een gekoppelde service, gegevenssets, pijplijn en pijplijnuitvoering. Vervolgens wordt de uitvoeringsstatus van de pijplijn gecontroleerd. Wacht totdat u de details van de uitvoering van de kopieeractiviteit ziet met de omvang van de gelezen/weggeschreven gegevens. Gebruik vervolgens hulpprogramma's zoals [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) om te controleren of de blob(s) is/zijn gekopieerd van het 'inputBlobPath' naar het 'outputBlobPath' zoals u hebt opgegeven in de variabelen.

### <a name="sample-output"></a>Voorbeelduitvoer

```json
Creating data factory SPv2Factory0907...
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
        "value": "DefaultEndpointsProtocol=https;AccountName=<storageAccountName>;AccountKey=<storageAccountKey>",
        "type": "SecureString"
      }
    }
  }
}
Creating dataset BlobDataset...
{
  "properties": {
    "type": "AzureBlob",
    "typeProperties": {
      "folderPath": {
        "value": "@{dataset().path}",
        "type": "Expression"
      }
    },
    "linkedServiceName": {
      "referenceName": "AzureStorageLinkedService",
      "type": "LinkedServiceReference"
    },
    "parameters": {
      "path": {
        "type": "String"
      }
    }
  }
}
Creating pipeline Adfv2QuickStartPipeline...
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
            "type": "BlobSink"
          }
        },
        "inputs": [
          {
            "referenceName": "BlobDataset",
            "parameters": {
              "path": "@pipeline().parameters.inputPath"
            },
            "type": "DatasetReference"
          }
        ],
        "outputs": [
          {
            "referenceName": "BlobDataset",
            "parameters": {
              "path": "@pipeline().parameters.outputPath"
            },
            "type": "DatasetReference"
          }
        ],
        "name": "CopyFromBlobToBlob"
      }
    ],
    "parameters": {
      "inputPath": {
        "type": "String"
      },
      "outputPath": {
        "type": "String"
      }
    }
  }
}
Creating pipeline run...
Pipeline run ID: 308d222d-3858-48b1-9e66-acd921feaa09
Checking pipeline run status...
Status: InProgress
Status: InProgress
Checking copy activity run details...
{
    "dataRead": 331452208,
    "dataWritten": 331452208,
    "copyDuration": 23,
    "throughput": 14073.209,
    "errors": [],
    "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (West US)",
    "usedDataIntegrationUnits": 2,
    "billedDuration": 23
}

Press any key to exit...
```

## <a name="verify-the-output"></a>De uitvoer controleren

De uitvoermap wordt automatisch door de pijplijn gemaakt in de blobcontainer **adftutorial**. Vervolgens wordt het bestand **emp.txt** gekopieerd van de invoermap naar de uitvoermap. 

1. Selecteer in de Azure Portal op de containerpagina **adftutorial** waarbij u gestopt bent in de sectie [Een invoermap en een bestand toevoegen voor de blobcontainer](#add-an-input-folder-and-file-for-the-blob-container) hierboven **Vernieuwen** om de uitvoermap weer te geven. 
2. Selecteer **Uitvoer** in de lijst met mappen.
3. Controleer of het bestand **emp.txt** naar de uitvoermap is gekopieerd. 

## <a name="clean-up-resources"></a>Resources opschonen

Als u de data factory programmatisch wilt verwijderen, voegt u de volgende regels code toe aan het programma: 

```csharp
Console.WriteLine("Deleting the data factory");
client.Factories.Delete(resourceGroup, dataFactoryName);
```

## <a name="next-steps"></a>Volgende stappen

Met de pijplijn in dit voorbeeld worden gegevens gekopieerd van de ene locatie naar een andere locatie in een Azure Blob-opslag. Doorloop de [zelfstudies](tutorial-copy-data-dot-net.md) voor meer informatie over het gebruiken van Data Factory in andere scenario's. 
