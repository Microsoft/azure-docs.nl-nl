---
title: Vertakking in Azure Data Factory-pijplijn
description: Leer de stroom van gegevens in Azure Data Factory beheren door activiteiten te vertakken en te koppelen.
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: tutorial
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 9/27/2019
ms.openlocfilehash: 111fff6e35bed9c2af9c638549362039f25def12
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100386013"
---
# <a name="branching-and-chaining-activities-in-a-data-factory-pipeline"></a>Activiteiten vertakken en koppelen in een Data Factory-pijplijn

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In deze zelfstudie maakt u een Data Factory-pijplijn die enkele stroombeheerfuncties demonstreert. Deze pijplijn kopieert vanuit een container in Azure Blob Storage naar een andere container in hetzelfde opslagaccount. Als de kopieerbewerking is geslaagd, worden de details over de geslaagde kopieerbewerking via de pijplijn verzonden in een e-mail. Deze informatie kan de hoeveelheid geschreven gegevens omvatten. Als de kopieerbewerking is mislukt, worden de details over de mislukte kopieerbewerking (zoals de foutmelding) verzonden in een e-mail. In de zelfstudie ziet u hoe u parameters kunt doorgeven.

Deze afbeelding biedt een overzicht van het scenario:

![In het diagram wordt Azure Blob Storage weergegeven. Dit is het doel van een kopie, die bij voltooiing een e-mail met details, of bij een fout een e-mail met foutgegevens verzendt.](media/tutorial-control-flow/overview.png)

In deze zelfstudie leert u hoe u de volgende taken uitvoert:

> [!div class="checklist"]
> * Een gegevensfactory maken
> * Een gekoppelde Azure Storage-service maken
> * Een Azure Blob-gegevensset maken
> * Een pijplijn met een kopieeractiviteit en een webactiviteit maken
> * Verzenden van uitvoer van activiteiten naar latere activiteiten
> * Parameters doorgeven en systeemvariabelen gebruiken
> * Een pijplijnuitvoering starten
> * De uitvoering van de pijplijn en van de activiteit controleren

In deze zelfstudie wordt .NET SDK gebruikt. U kunt andere methoden gebruiken voor interactie met Azure Data Factory. Zie [Quickstarts van 5 minuten](./quickstart-create-data-factory-portal.md) voor Data Factory-quickstarts.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

* Azure-opslagaccount. U gebruikt de blobopslag als bron-gegevensopslag. Raadpleeg [Een opslagaccount maken](../storage/common/storage-account-create.md) als u geen account Azure-opslagaccount hebt.
* Azure Storage Explorer. Zie [Azure Storage Explorer](https://storageexplorer.com/) als u dit hulpprogramma wilt installeren.
* Azure SQL Database. U gebruikt de database als sink-gegevensopslag. Als u geen database in Azure SQL Database hebt, raadpleegt u het artikel [Een database in Azure SQL Database maken](../azure-sql/database/single-database-create-quickstart.md).
* Visual Studio. Dit artikel gebruikt Visual Studio 2019.
* Azure .NET SDK. Download en installeer de [Azure .NET SDK](https://azure.microsoft.com/downloads/).

Zie [Beschikbare producten per regio](https://azure.microsoft.com/global-infrastructure/services/) voor een lijst met Azure-regio’s waarin Data Factory momenteel beschikbaar is. De gegevensopslag en berekeningen kunnen zich in andere regio’s bevinden. De opslag bevat Azure Storage en Azure SQL Database. De berekeningen bevatten HDInsight, die Data Factory gebruikt.

Maak een toepassing zoals beschreven in [Een Azure Active Directory-toepassing maken](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal). Wijs de toepassing toe aan de rol **Inzender** door de instructies in hetzelfde artikel te volgen. U hebt verschillende waarden nodig voor latere onderdelen van deze zelfstudie, zoals **Toepassings-id (client)** en **Directory-id (tenant)** .

### <a name="create-a-blob-table"></a>Een blobtabel maken

1. Open een teksteditor. Kopieer de volgende tekst en sla deze lokaal op als *input.txt*.

   ```
   Ethel|Berg
   Tamika|Walsh
   ```

1. Open Azure Storage Explorer. Breid uw opslagaccount uit. Klik met de rechtermuisknop op **Blob-containers** en selecteer **Blob-container maken**.
1. Geef de nieuwe container de naam *adfv2branch* en selecteer **Uploaden** om het bestand *input.txt* toe te voegen aan de container.

## <a name="create-visual-studio-project"></a>Een Visual Studio-project maken<a name="create-visual-studio-project"></a>

Een C# .NET-consoletoepassing maken:

1. Start Visual Studio en selecteer **Een nieuw project maken**.
1. Kies in het venster **Een nieuw project maken** **Console-app (.NET Framework)** voor C# en selecteer **Volgende**.
1. Geef het project de naam *ADFv2BranchTutorial*.
1. Selecteer **.NET-versie 4.5.2** of hoger en selecteer vervolgens **Maken**.

### <a name="install-nuget-packages"></a>NuGet-pakketten installeren

1. Selecteer **Hulpprogramma's** > **NuGet Package Manager** > **Package Manager-console**.
1. Voer in **Package Manager Console** de volgende opdrachten uit om pakketten te installeren. Raadpleeg voor meer informatie het [NuGet-pakket Microsoft.Azure.Management.DataFactory](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactory/).

   ```powershell
   Install-Package Microsoft.Azure.Management.DataFactory
   Install-Package Microsoft.Azure.Management.ResourceManager -IncludePrerelease
   Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
   ```

### <a name="create-a-data-factory-client"></a>Een data factory-client maken

1. Open *Program.cs* en voeg de volgende instructies toe:

   ```csharp
   using System;
   using System.Collections.Generic;
   using System.Linq;
   using Microsoft.Rest;
   using Microsoft.Azure.Management.ResourceManager;
   using Microsoft.Azure.Management.DataFactory;
   using Microsoft.Azure.Management.DataFactory.Models;
   using Microsoft.IdentityModel.Clients.ActiveDirectory;
   ```

1. Voeg deze statische variabelen toe aan de klasse `Program`. Vervang de plaatsaanduidingen door uw eigen waarden.

   ```csharp
   // Set variables
   static string tenantID = "<tenant ID>";
   static string applicationId = "<application ID>";
   static string authenticationKey = "<Authentication key for your application>";
   static string subscriptionId = "<Azure subscription ID>";
   static string resourceGroup = "<Azure resource group name>";

   static string region = "East US";
   static string dataFactoryName = "<Data factory name>";

   // Specify the source Azure Blob information
   static string storageAccount = "<Azure Storage account name>";
   static string storageKey = "<Azure Storage account key>";
   // confirm that you have the input.txt file placed in th input folder of the adfv2branch container.
   static string inputBlobPath = "adfv2branch/input";
   static string inputBlobName = "input.txt";
   static string outputBlobPath = "adfv2branch/output";
   static string emailReceiver = "<specify email address of the receiver>";

   static string storageLinkedServiceName = "AzureStorageLinkedService";
   static string blobSourceDatasetName = "SourceStorageDataset";
   static string blobSinkDatasetName = "SinkStorageDataset";
   static string pipelineName = "Adfv2TutorialBranchCopy";

   static string copyBlobActivity = "CopyBlobtoBlob";
   static string sendFailEmailActivity = "SendFailEmailActivity";
   static string sendSuccessEmailActivity = "SendSuccessEmailActivity";
   ```

1. Voeg de volgende code toe aan de methode `Main`. Deze code maakt een instantie van klasse `DataFactoryManagementClient`. U gebruikt dit object vervolgens voor het maken van een data factory, een gekoppelde service, gegevenssets en een pijplijn. U kunt dit object ook gebruiken om de details van de pijplijnuitvoering te controleren.

   ```csharp
   // Authenticate and create a data factory management client
   var context = new AuthenticationContext("https://login.windows.net/" + tenantID);
   ClientCredential cc = new ClientCredential(applicationId, authenticationKey);
   AuthenticationResult result = context.AcquireTokenAsync("https://management.azure.com/", cc).Result;
   ServiceClientCredentials cred = new TokenCredentials(result.AccessToken);
   var client = new DataFactoryManagementClient(cred) { SubscriptionId = subscriptionId };
   ```

### <a name="create-a-data-factory"></a>Een gegevensfactory maken

1. Een methode `CreateOrUpdateDataFactory` toevoegen aan uw bestand *Program.cs*:

   ```csharp
   static Factory CreateOrUpdateDataFactory(DataFactoryManagementClient client)
   {
       Console.WriteLine("Creating data factory " + dataFactoryName + "...");
       Factory resource = new Factory
       {
           Location = region
       };
       Console.WriteLine(SafeJsonConvert.SerializeObject(resource, client.SerializationSettings));

       Factory response;
       {
           response = client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, resource);
       }

       while (client.Factories.Get(resourceGroup, dataFactoryName).ProvisioningState == "PendingCreation")
       {
           System.Threading.Thread.Sleep(1000);
       }
       return response;
   }
   ```

1. Voeg de volgende regel toe aan de methode `Main` waarmee een data factory wordt gemaakt:

   ```csharp
   Factory df = CreateOrUpdateDataFactory(client);
   ```

## <a name="create-an-azure-storage-linked-service"></a>Een gekoppelde Azure Storage-service maken

1. Een methode `StorageLinkedServiceDefinition` toevoegen aan uw bestand *Program.cs*:

   ```csharp
   static LinkedServiceResource StorageLinkedServiceDefinition(DataFactoryManagementClient client)
   {
      Console.WriteLine("Creating linked service " + storageLinkedServiceName + "...");
      AzureStorageLinkedService storageLinkedService = new AzureStorageLinkedService
      {
          ConnectionString = new SecureString("DefaultEndpointsProtocol=https;AccountName=" + storageAccount + ";AccountKey=" + storageKey)
      };
      Console.WriteLine(SafeJsonConvert.SerializeObject(storageLinkedService, client.SerializationSettings));
      LinkedServiceResource linkedService = new LinkedServiceResource(storageLinkedService, name:storageLinkedServiceName);
      return linkedService;
   }
   ```

1. Voeg de volgende regel toe aan de methode `Main` waarmee een gekoppelde Azure Storage-service wordt gemaakt:

   ```csharp
   client.LinkedServices.CreateOrUpdate(resourceGroup, dataFactoryName, storageLinkedServiceName, StorageLinkedServiceDefinition(client));
   ```

Zie [Eigenschappen van een gekoppelde service](connector-azure-blob-storage.md#linked-service-properties) voor meer informatie over ondersteunde eigenschappen en details.

## <a name="create-datasets"></a>Gegevenssets maken

In dit gedeelte maakt u twee gegevenssets: één voor de bron en de andere voor de sink.

### <a name="create-a-dataset-for-a-source-azure-blob"></a>Een gegevensset maken voor de brongegevens in Azure Blob

Voeg een methode toe waarmee een *Azure Blob-gegevensset* wordt gemaakt. Zie [Eigenschappen van een Azure Blob-gegevensset](connector-azure-blob-storage.md#dataset-properties) voor meer informatie over ondersteunde eigenschappen en details.

Een methode `SourceBlobDatasetDefinition` toevoegen aan uw bestand *Program.cs*:

```csharp
static DatasetResource SourceBlobDatasetDefinition(DataFactoryManagementClient client)
{
    Console.WriteLine("Creating dataset " + blobSourceDatasetName + "...");
    AzureBlobDataset blobDataset = new AzureBlobDataset
    {
        FolderPath = new Expression { Value = "@pipeline().parameters.sourceBlobContainer" },
        FileName = inputBlobName,
        LinkedServiceName = new LinkedServiceReference
        {
            ReferenceName = storageLinkedServiceName
        }
    };
    Console.WriteLine(SafeJsonConvert.SerializeObject(blobDataset, client.SerializationSettings));
    DatasetResource dataset = new DatasetResource(blobDataset, name:blobSourceDatasetName);
    return dataset;
}
```

U definieert een gegevensset die de brongegevens in Azure Blob vertegenwoordigt. Deze Blob-gegevensset verwijst naar de gekoppelde Azure Storage-service die wordt ondersteund in de vorige stap. De Blob-gegevensset beschrijft de locatie van de blob waaruit moet worden gekopieerd: *FolderPath* en *FileName*.

Let op het gebruik van parameters voor *FolderPath*. `sourceBlobContainer` is de naam van de parameter en de expressie wordt vervangen door de waarden die worden doorgegeven in de pijplijnuitvoering. De syntaxis voor het definiëren van parameters is`@pipeline().parameters.<parameterName>`

### <a name="create-a-dataset-for-a-sink-azure-blob"></a>Een gegevensset maken voor de sinkgegevens in Azure Blob

1. Een methode `SourceBlobDatasetDefinition` toevoegen aan uw bestand *Program.cs*:

   ```csharp
   static DatasetResource SinkBlobDatasetDefinition(DataFactoryManagementClient client)
   {
       Console.WriteLine("Creating dataset " + blobSinkDatasetName + "...");
       AzureBlobDataset blobDataset = new AzureBlobDataset
       {
           FolderPath = new Expression { Value = "@pipeline().parameters.sinkBlobContainer" },
           LinkedServiceName = new LinkedServiceReference
           {
               ReferenceName = storageLinkedServiceName
           }
       };
       Console.WriteLine(SafeJsonConvert.SerializeObject(blobDataset, client.SerializationSettings));
       DatasetResource dataset = new DatasetResource(blobDataset, name: blobSinkDatasetName);
       return dataset;
   }
   ```

1. Voeg de volgende code toe aan de methode `Main` om een Azure Blob Storage-bron- en -sinkgegevensset te maken.

   ```csharp
   client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobSourceDatasetName, SourceBlobDatasetDefinition(client));

   client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobSinkDatasetName, SinkBlobDatasetDefinition(client));
   ```

## <a name="create-a-c-class-emailrequest"></a>Een C#-klasse maken: EmailRequest

Maak in uw C#-project een klasse met de naam `EmailRequest`. Deze klasse definieert welke eigenschappen de pijplijn in de hoofdtekstaanvraag verzendt bij het verzenden van een e-mailbericht. In deze zelfstudie verzendt de pijplijn vier eigenschappen van de pijplijn naar het e-mailbericht:

* Bericht. Hoofdtekst van het e-mailbericht. Voor een geslaagde kopie bevat deze eigenschap de hoeveelheid gegevens die is geschreven. Voor een mislukte kopie bevat deze eigenschap details over de fout.
* Naam data factory. Naam van de data factory.
* Naam pijplijn. Naam van de pijplijn.
* Ontvanger. Parameter die wordt doorgegeven. Met deze eigenschap wordt de ontvanger van het e-mailbericht opgegeven.

```csharp
    class EmailRequest
    {
        [Newtonsoft.Json.JsonProperty(PropertyName = "message")]
        public string message;

        [Newtonsoft.Json.JsonProperty(PropertyName = "dataFactoryName")]
        public string dataFactoryName;

        [Newtonsoft.Json.JsonProperty(PropertyName = "pipelineName")]
        public string pipelineName;

        [Newtonsoft.Json.JsonProperty(PropertyName = "receiver")]
        public string receiver;

        public EmailRequest(string input, string df, string pipeline, string receiverName)
        {
            message = input;
            dataFactoryName = df;
            pipelineName = pipeline;
            receiver = receiverName;
        }
    }
```

## <a name="create-email-workflow-endpoints"></a>Eindpunten voor de e-mailwerkstroom maken

Voor het activeren van het verzenden van een e-mail gebruikt u [Logic Apps](../logic-apps/logic-apps-overview.md) om de werkstroom te definiëren. Zie voor meer informatie over het maken van een Logic App-werkstroom [Een logische app maken](../logic-apps/quickstart-create-first-logic-app-workflow.md).

### <a name="success-email-workflow"></a>Werkstroom voor e-mail met succesbericht

Maak in de [Azure-portal](https://portal.azure.com) een Logic Apps-werk stroom met de naam *CopySuccessEmail*. Hiermee wordt de werkstroomtrigger gedefinieerd als `When an HTTP request is received`. Als aanvraagtrigger vult u het `Request Body JSON Schema` met de volgende JSON:

```json
{
    "properties": {
        "dataFactoryName": {
            "type": "string"
        },
        "message": {
            "type": "string"
        },
        "pipelineName": {
            "type": "string"
        },
        "receiver": {
            "type": "string"
        }
    },
    "type": "object"
}
```

Uw werkstroom ziet er ongeveer uit als het volgende voorbeeld:

![Werkstroom voor e-mail met succesbericht](media/tutorial-control-flow/success-email-workflow-trigger.png)

Deze JSON-inhoud correspondeert met de `EmailRequest`-klasse die u in het vorige gedeelte hebt gemaakt.

Voeg een actie van `Office 365 Outlook – Send an email` toe. Pas voor de actie **Een e-mail verzenden** de opmaak van de e-mail aan met behulp van de eigenschappen die in het JSON-schema voor de **hoofdtekst** van de aanvraag worden doorgegeven. Hier volgt een voorbeeld:

![Logic App Designer - actie e-mail verzenden](media/tutorial-control-flow/customize-send-email-action.png)

Nadat u de werkstroom hebt opgeslagen, moet u de waarde **HTTP POST URL** van de trigger kopiëren en opslaan.

## <a name="fail-email-workflow"></a>Werkstroom voor e-mail met foutbericht

Kloon **CopySuccessEmail** als een andere Logic Apps-werkstroom met de naam *CopyFailEmail*. In de aanvraagtrigger is het `Request Body JSON schema` hetzelfde. Wijzig de indeling van uw e-mailbericht net als het `Subject` om er een e-mail met de foutmelding van te maken. Hier volgt een voorbeeld:

![Logic App designer - werkstroom voor e-mailbericht met foutmelding](media/tutorial-control-flow/fail-email-workflow.png)

Nadat u de werkstroom hebt opgeslagen, moet u de waarde **HTTP POST URL** van de trigger kopiëren en opslaan.

U zou nu twee werkstroom-URL’s moeten hebben, zoals in de volgende voorbeelden:

```csharp
//Success Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000

//Fail Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000
```

## <a name="create-a-pipeline"></a>Een pijplijn maken

Ga terug naar uw project in Visual Studio. We voegen nu de code waarmee een pijplijn wordt gemaakt met een kopieeractiviteit en eigenschap `DependsOn`. In deze zelfstudie bevat deze pijplijn maar één activiteit: een kopieeractiviteit waarbij de Blob-gegevensset als bron wordt gebruikt en een andere Blob-gegevensset als sink. Als de kopieeractiviteit slaagt of mislukt, worden verschillende e-mailtaken aangeroepen.

In deze pijpelijn gebruikt u de volgende functies:

* Parameters
* Webactiviteit
* Afhankelijkheid van activiteiten
* De uitvoer van een activiteit gebruiken als invoer voor een andere activiteit

1. Voeg deze methode toe aan uw project. De volgende gedeelten bevatten meer details.

    ```csharp
    static PipelineResource PipelineDefinition(DataFactoryManagementClient client)
            {
                Console.WriteLine("Creating pipeline " + pipelineName + "...");
                PipelineResource resource = new PipelineResource
                {
                    Parameters = new Dictionary<string, ParameterSpecification>
                    {
                        { "sourceBlobContainer", new ParameterSpecification { Type = ParameterType.String } },
                        { "sinkBlobContainer", new ParameterSpecification { Type = ParameterType.String } },
                        { "receiver", new ParameterSpecification { Type = ParameterType.String } }

                    },
                    Activities = new List<Activity>
                    {
                        new CopyActivity
                        {
                            Name = copyBlobActivity,
                            Inputs = new List<DatasetReference>
                            {
                                new DatasetReference
                                {
                                    ReferenceName = blobSourceDatasetName
                                }
                            },
                            Outputs = new List<DatasetReference>
                            {
                                new DatasetReference
                                {
                                    ReferenceName = blobSinkDatasetName
                                }
                            },
                            Source = new BlobSource { },
                            Sink = new BlobSink { }
                        },
                        new WebActivity
                        {
                            Name = sendSuccessEmailActivity,
                            Method = WebActivityMethod.POST,
                            Url = "https://prodxxx.eastus.logic.azure.com:443/workflows/00000000000000000000000000000000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=0000000000000000000000000000000000000000000000",
                            Body = new EmailRequest("@{activity('CopyBlobtoBlob').output.dataWritten}", "@{pipeline().DataFactory}", "@{pipeline().Pipeline}", "@pipeline().parameters.receiver"),
                            DependsOn = new List<ActivityDependency>
                            {
                                new ActivityDependency
                                {
                                    Activity = copyBlobActivity,
                                    DependencyConditions = new List<String> { "Succeeded" }
                                }
                            }
                        },
                        new WebActivity
                        {
                            Name = sendFailEmailActivity,
                            Method =WebActivityMethod.POST,
                            Url = "https://prodxxx.eastus.logic.azure.com:443/workflows/000000000000000000000000000000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=0000000000000000000000000000000000000000000",
                            Body = new EmailRequest("@{activity('CopyBlobtoBlob').error.message}", "@{pipeline().DataFactory}", "@{pipeline().Pipeline}", "@pipeline().parameters.receiver"),
                            DependsOn = new List<ActivityDependency>
                            {
                                new ActivityDependency
                                {
                                    Activity = copyBlobActivity,
                                    DependencyConditions = new List<String> { "Failed" }
                                }
                            }
                        }
                    }
                };
                Console.WriteLine(SafeJsonConvert.SerializeObject(resource, client.SerializationSettings));
                return resource;
            }
    ```

1. Voeg de volgende regel toe aan de methode `Main` waarmee de pijplijn wordt gemaakt:

   ```csharp
   client.Pipelines.CreateOrUpdate(resourceGroup, dataFactoryName, pipelineName, PipelineDefinition(client));
   ```

### <a name="parameters"></a>Parameters

Het eerste gedeelte van onze pijplijncode definieert parameters.

* `sourceBlobContainer`. De bron-blob-gegevensset gebruikt deze parameter in de pijplijn.
* `sinkBlobContainer`. De sink-blob-gegevensset gebruikt deze parameter in de pijplijn.
* `receiver`. De twee webactiviteiten in de pijplijn die e-mailberichten met het bericht geslaagd of mislukt verzenden naar de ontvanger, gebruiken deze parameter.

```csharp
Parameters = new Dictionary<string, ParameterSpecification>
    {
        { "sourceBlobContainer", new ParameterSpecification { Type = ParameterType.String } },
        { "sinkBlobContainer", new ParameterSpecification { Type = ParameterType.String } },
        { "receiver", new ParameterSpecification { Type = ParameterType.String } }
    },
```

### <a name="web-activity"></a>Webactiviteit

De webactiviteit staat een aanroep toe naar elk REST-eindpunt. Zie voor meer informatie over de activiteit [Webactiviteit in Azure Data Factory](control-flow-web-activity.md). Deze pijplijn gebruikt een webactiviteit voor het aanroepen van de Logic Apps-e-mailwerkstroom. U maakt twee webactiviteiten: een die de werkstroom `CopySuccessEmail` aanroept, en één die `CopyFailWorkFlow` aanroept.

```csharp
        new WebActivity
        {
            Name = sendCopyEmailActivity,
            Method = WebActivityMethod.POST,
            Url = "https://prodxxx.eastus.logic.azure.com:443/workflows/12345",
            Body = new EmailRequest("@{activity('CopyBlobtoBlob').output.dataWritten}", "@{pipeline().DataFactory}", "@{pipeline().Pipeline}", "@pipeline().parameters.receiver"),
            DependsOn = new List<ActivityDependency>
            {
                new ActivityDependency
                {
                    Activity = copyBlobActivity,
                    DependencyConditions = new List<String> { "Succeeded" }
                }
            }
        }
```

Plak de eindpunten van de **HTTP POST URL** uit uw Logic Apps-werkstroom in de eigenschap `Url`. Geef in de eigenschap `Body` een exemplaar van de klasse `EmailRequest` door. De e-mailaanvraag bevat de volgende eigenschappen:

* Bericht. Hiermee wordt de waarde van `@{activity('CopyBlobtoBlob').output.dataWritten` doorgegeven. Leest een eigenschap van de vorige kopieeractiviteit en geeft de waarde van `dataWritten` door. In het geval waarin het kopiëren mislukt, wordt de uitvoer van de fout doorgegeven in plaats van `@{activity('CopyBlobtoBlob').error.message`.
* Naam data factory. Geeft de waarde van `@{pipeline().DataFactory}` door. Met deze systeemvariabele hebt u toegang tot de bijbehorende data factory-naam. Voor een lijst van systeemvariabelen raadpleegt u [Systeemvariabelen](control-flow-system-variables.md).
* Naam pijplijn. Hiermee wordt de waarde van `@{pipeline().Pipeline}` doorgegeven. Met deze systeemvariabele hebt u toegang tot de bijbehorende pijplijnnaam.
* Ontvanger. Hiermee wordt de waarde van `"@pipeline().parameters.receiver"` doorgegeven. Biedt toegang tot de pijplijnparameters.

Deze code maakt een nieuwe activiteitafhankelijkheid, die afhankelijk is van de vorige kopieeractiviteit.

## <a name="create-a-pipeline-run"></a>Een pijplijnuitvoering maken

Voeg de volgende code toe aan de methode `Main`waarmee een pijplijnuitvoering wordt geactiveerd.

```csharp
// Create a pipeline run
Console.WriteLine("Creating pipeline run...");
Dictionary<string, object> arguments = new Dictionary<string, object>
{
    { "sourceBlobContainer", inputBlobPath },
    { "sinkBlobContainer", outputBlobPath },
    { "receiver", emailReceiver }
};

CreateRunResponse runResponse = client.Pipelines.CreateRunWithHttpMessagesAsync(resourceGroup, dataFactoryName, pipelineName, arguments).Result.Body;
Console.WriteLine("Pipeline run ID: " + runResponse.RunId);
```

## <a name="main-class"></a>Main-klasse

Uw laatste `Main`-methode moet er zo uitzien.

```csharp
// Authenticate and create a data factory management client
var context = new AuthenticationContext("https://login.windows.net/" + tenantID);
ClientCredential cc = new ClientCredential(applicationId, authenticationKey);
AuthenticationResult result = context.AcquireTokenAsync("https://management.azure.com/", cc).Result;
ServiceClientCredentials cred = new TokenCredentials(result.AccessToken);
var client = new DataFactoryManagementClient(cred) { SubscriptionId = subscriptionId };

Factory df = CreateOrUpdateDataFactory(client);

client.LinkedServices.CreateOrUpdate(resourceGroup, dataFactoryName, storageLinkedServiceName, StorageLinkedServiceDefinition(client));
client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobSourceDatasetName, SourceBlobDatasetDefinition(client));
client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobSinkDatasetName, SinkBlobDatasetDefinition(client));

client.Pipelines.CreateOrUpdate(resourceGroup, dataFactoryName, pipelineName, PipelineDefinition(client));

Console.WriteLine("Creating pipeline run...");
Dictionary<string, object> arguments = new Dictionary<string, object>
{
    { "sourceBlobContainer", inputBlobPath },
    { "sinkBlobContainer", outputBlobPath },
    { "receiver", emailReceiver }
};

CreateRunResponse runResponse = client.Pipelines.CreateRunWithHttpMessagesAsync(resourceGroup, dataFactoryName, pipelineName, arguments).Result.Body;
Console.WriteLine("Pipeline run ID: " + runResponse.RunId);
```

Bouw het programma en voer het uit om een pijplijnuitvoering te activeren.

## <a name="monitor-a-pipeline-run"></a>Een pijplijnuitvoering controleren

1. Voeg de volgende code aan de `Main` methode toe:

    ```csharp
    // Monitor the pipeline run
    Console.WriteLine("Checking pipeline run status...");
    PipelineRun pipelineRun;
    while (true)
    {
        pipelineRun = client.PipelineRuns.Get(resourceGroup, dataFactoryName, runResponse.RunId);
        Console.WriteLine("Status: " + pipelineRun.Status);
        if (pipelineRun.Status == "InProgress")
            System.Threading.Thread.Sleep(15000);
        else
            break;
    }
    ```

    Met deze code wordt doorlopend de status van de uitvoering gecontroleerd totdat deze klaar is met het kopiëren van de gegevens.

1. Voeg de volgende code toe aan de methode `Main` om details van de kopieeractiviteit weer te geven, zoals de omvang van de gelezen of weggeschreven gegevens:

    ```csharp
    // Check the copy activity run details
    Console.WriteLine("Checking copy activity run details...");

    List<ActivityRun> activityRuns = client.ActivityRuns.ListByPipelineRun(
    resourceGroup, dataFactoryName, runResponse.RunId, DateTime.UtcNow.AddMinutes(-10), DateTime.UtcNow.AddMinutes(10)).ToList();

    if (pipelineRun.Status == "Succeeded")
    {
        Console.WriteLine(activityRuns.First().Output);
        //SaveToJson(SafeJsonConvert.SerializeObject(activityRuns.First().Output, client.SerializationSettings), "ActivityRunResult.json", folderForJsons);
    }
    else
        Console.WriteLine(activityRuns.First().Error);

    Console.WriteLine("\nPress any key to exit...");
    Console.ReadKey();
    ```

## <a name="run-the-code"></a>De code uitvoeren

Bouw en start de toepassing en controleer vervolgens de uitvoering van de pijplijn.

In de toepassing wordt de voortgang weergegeven van het maken van een data factory, een gekoppelde service, gegevenssets, pijplijn en pijplijnuitvoering. Vervolgens wordt de uitvoeringsstatus van de pijplijn gecontroleerd. Wacht totdat u details ziet van de uitvoering van de kopieeractiviteit, waaronder de omvang van de gelezen/weggeschreven gegevens. Gebruik vervolgens hulpprogramma's als Azure Storage Explorer om te controleren of de blob(s) is/zijn gekopieerd van het *inputBlobPath* naar het *outputBlobPath* zoals u hebt opgegeven in de variabelen.

Uw uitvoer moet lijken op het volgende voorbeeld:

```json
Creating data factory DFTutorialTest...
{
  "location": "East US"
}
Creating linked service AzureStorageLinkedService...
{
  "type": "AzureStorage",
  "typeProperties": {
    "connectionString": "DefaultEndpointsProtocol=https;AccountName=***;AccountKey=***"
  }
}
Creating dataset SourceStorageDataset...
{
  "type": "AzureBlob",
  "typeProperties": {
    "folderPath": {
      "type": "Expression",
      "value": "@pipeline().parameters.sourceBlobContainer"
    },
    "fileName": "input.txt"
  },
  "linkedServiceName": {
    "type": "LinkedServiceReference",
    "referenceName": "AzureStorageLinkedService"
  }
}
Creating dataset SinkStorageDataset...
{
  "type": "AzureBlob",
  "typeProperties": {
    "folderPath": {
      "type": "Expression",
      "value": "@pipeline().parameters.sinkBlobContainer"
    }
  },
  "linkedServiceName": {
    "type": "LinkedServiceReference",
    "referenceName": "AzureStorageLinkedService"
  }
}
Creating pipeline Adfv2TutorialBranchCopy...
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
            "type": "DatasetReference",
            "referenceName": "SourceStorageDataset"
          }
        ],
        "outputs": [
          {
            "type": "DatasetReference",
            "referenceName": "SinkStorageDataset"
          }
        ],
        "name": "CopyBlobtoBlob"
      },
      {
        "type": "WebActivity",
        "typeProperties": {
          "method": "POST",
          "url": "https://xxxx.eastus.logic.azure.com:443/workflows/... ",
          "body": {
            "message": "@{activity('CopyBlobtoBlob').output.dataWritten}",
            "dataFactoryName": "@{pipeline().DataFactory}",
            "pipelineName": "@{pipeline().Pipeline}",
            "receiver": "@pipeline().parameters.receiver"
          }
        },
        "name": "SendSuccessEmailActivity",
        "dependsOn": [
          {
            "activity": "CopyBlobtoBlob",
            "dependencyConditions": [
              "Succeeded"
            ]
          }
        ]
      },
      {
        "type": "WebActivity",
        "typeProperties": {
          "method": "POST",
          "url": "https://xxx.eastus.logic.azure.com:443/workflows/... ",
          "body": {
            "message": "@{activity('CopyBlobtoBlob').error.message}",
            "dataFactoryName": "@{pipeline().DataFactory}",
            "pipelineName": "@{pipeline().Pipeline}",
            "receiver": "@pipeline().parameters.receiver"
          }
        },
        "name": "SendFailEmailActivity",
        "dependsOn": [
          {
            "activity": "CopyBlobtoBlob",
            "dependencyConditions": [
              "Failed"
            ]
          }
        ]
      }
    ],
    "parameters": {
      "sourceBlobContainer": {
        "type": "String"
      },
      "sinkBlobContainer": {
        "type": "String"
      },
      "receiver": {
        "type": "String"
      }
    }
  }
}
Creating pipeline run...
Pipeline run ID: 00000000-0000-0000-0000-0000000000000
Checking pipeline run status...
Status: InProgress
Status: InProgress
Status: Succeeded
Checking copy activity run details...
{
  "dataRead": 20,
  "dataWritten": 20,
  "copyDuration": 4,
  "throughput": 0.01,
  "errors": [],
  "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)"
}
{}

Press any key to exit...
```

## <a name="next-steps"></a>Volgende stappen

U hebt de volgende taken uitgevoerd in deze zelfstudie:

> [!div class="checklist"]
> * Een gegevensfactory maken
> * Een gekoppelde Azure Storage-service maken
> * Een Azure Blob-gegevensset maken
> * Een pijplijn met een kopieeractiviteit en een webactiviteit maken
> * Verzenden van uitvoer van activiteiten naar latere activiteiten
> * Parameters doorgeven en systeemvariabelen gebruiken
> * Een pijplijnuitvoering starten
> * De uitvoering van de pijplijn en van de activiteit controleren

U kunt nu doorgaan naar het gedeelte Concepten voor meer informatie over Azure Data Factory.
> [!div class="nextstepaction"]
>[Pijplijnen en activiteiten](concepts-pipelines-activities.md)