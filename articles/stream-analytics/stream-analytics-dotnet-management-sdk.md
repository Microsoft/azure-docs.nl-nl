---
title: Management .NET SDK voor Azure Stream Analytics
description: Aan de slag met Stream Analytics management .NET SDK. Meer informatie over het instellen en uitvoeren van analyse taken. Een project, invoer, uitvoer en trans formaties maken.
author: jseb225
ms.author: jeanb
ms.service: stream-analytics
ms.topic: how-to
ms.date: 12/06/2018
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 633885bb1062edac8226c073768ffdeba84fcb55
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98012628"
---
# <a name="management-net-sdk-set-up-and-run-analytics-jobs-using-the-azure-stream-analytics-api-for-net"></a>Management .NET SDK: analyse taken instellen en uitvoeren met behulp van de Azure Stream Analytics-API voor .NET
Meer informatie over het instellen en uitvoeren van analyse taken met behulp van de Stream Analytics-API voor .NET met behulp van de Management .NET SDK. Stel een project in en maak invoer-en uitvoer bronnen, trans formaties en taken voor starten en stoppen. Voor uw analyse taken kunt u gegevens streamen vanuit Blob Storage of van een Event Hub.

Zie de [documentatie van management Reference voor de stream Analytics-API voor .net](/previous-versions/azure/dn889315(v=azure.100)).

Azure Stream Analytics is een volledig beheerde service met lage latentie, Maxi maal beschik bare, schaal bare, complexe gebeurtenis verwerking via streaming-gegevens in de Cloud. Met Stream Analytics kunnen klanten streaming-taken instellen om gegevens stromen te analyseren en ze in staat stellen om in realtime analyses te maken.  

> [!NOTE]
> We hebben de voorbeeld code in dit artikel bijgewerkt met Azure Stream Analytics management .NET SDK v2. x-versie. Zie [de Management .NET SDK v1. x gebruiken voor stream Analytics]()voor voorbeeld code met de SDK-versie van lagecy (1. x).

## <a name="prerequisites"></a>Vereisten
Voordat u aan dit artikel begint, moet u aan de volgende vereisten voldoen:

* Installeer Visual Studio 2019 of 2015.
* Down load en Installeer [Azure .NET SDK](https://azure.microsoft.com/downloads/).
* Maak een Azure-resource groep in uw abonnement. Het volgende voor beeld is een voor beeld-Azure PowerShell script. Zie [Azure PowerShell installeren en configureren](/powershell/azure/)voor Azure PowerShell informatie.  

   ```powershell
   # Log in to your Azure account
   Add-AzureAccount
   
   # Select the Azure subscription you want to use to create the resource group
   Select-AzureSubscription -SubscriptionName <subscription name>
   
   # If Stream Analytics has not been registered to the subscription, remove the remark    symbol (#) to run the Register-AzProvider cmdlet to register the provider namespace
   #Register-AzProvider -Force -ProviderNamespace 'Microsoft.StreamAnalytics'
   
   # Create an Azure resource group
   New-AzureResourceGroup -Name <YOUR RESOURCE GROUP NAME> -Location <LOCATION>
   ```

* Stel een invoer bron en een uitvoer doel in voor de taak waarmee verbinding moet worden gemaakt.

## <a name="set-up-a-project"></a>Een project instellen
Als u een analyse taak wilt maken, gebruikt u de Stream Analytics-API voor .NET, moet u uw project eerst instellen.

1. Maak een Visual Studio C# .NET-console toepassing.
2. Voer in de Package Manager-console de volgende opdrachten uit om de NuGet-pakketten te installeren. De eerste is de Azure Stream Analytics management .NET SDK. De tweede is voor Azure-client verificatie.

   ```powershell   
   Install-Package Microsoft.Azure.Management.StreamAnalytics -Version 2.0.0
   Install-Package Microsoft.Rest.ClientRuntime.Azure.Authentication -Version 2.3.1
   ```

3. Voeg de sectie **appSettings** toe aan het App.config-bestand:
   
   ```powershell
   <appSettings>
       <add key="ClientId" value="1950a258-227b-4e31-a9cf-717495945fc2" />
       <add key="RedirectUri" value="urn:ietf:wg:oauth:2.0:oob" />
       <add key="SubscriptionId" value="YOUR SUBSCRIPTION ID" />
       <add key="ActiveDirectoryTenantId" value="YOUR TENANT ID" />
   </appSettings>
   ```

    Vervang waarden voor **SubscriptionId** en **ActiveDirectoryTenantId** met uw Azure-abonnement en Tenant-id's. U kunt deze waarden verkrijgen door de volgende Azure PowerShell-cmdlet uit te voeren:

   ```powershell
      Get-AzureAccount
   ```

4. Voeg de volgende verwijzing toe aan het. csproj-bestand:

   ```csharp
   <Reference Include="System.Configuration" />
   ```

5. Voeg de volgende **using** -instructies toe aan het bron bestand (Program.cs) in het project:
   
   ```csharp
   using System;
   using System.Collections.Generic;
   using System.Configuration;
   using System.Threading;
   using System.Threading.Tasks;
   
   using Microsoft.Azure.Management.StreamAnalytics;
   using Microsoft.Azure.Management.StreamAnalytics.Models;
   using Microsoft.Rest.Azure.Authentication;
   using Microsoft.Rest;
   ```

6. Een authenticatie methode toevoegen:

   ```csharp
   private static async Task<ServiceClientCredentials> GetCredentials()
   {
       var activeDirectoryClientSettings = ActiveDirectoryClientSettings.UsePromptOnly(ConfigurationManager.AppSettings["ClientId"], new Uri("urn:ietf:wg:oauth:2.0:oob"));
       ServiceClientCredentials credentials = await UserTokenProvider.LoginWithPromptAsync(ConfigurationManager.AppSettings["ActiveDirectoryTenantId"], activeDirectoryClientSettings);
       
       return credentials;
    }
   ```

## <a name="create-a-stream-analytics-management-client"></a>Een Stream Analytics-beheer-client maken
Met een **StreamAnalyticsManagementClient** -object kunt u de taak en de taak onderdelen, zoals invoer, uitvoer en trans formatie, beheren.

Voeg de volgende code toe aan het begin van de methode **Main** :

   ```csharp
    string resourceGroupName = "<YOUR AZURE RESOURCE GROUP NAME>";
    string streamingJobName = "<YOUR STREAMING JOB NAME>";
    string inputName = "<YOUR JOB INPUT NAME>";
    string transformationName = "<YOUR JOB TRANSFORMATION NAME>";
    string outputName = "<YOUR JOB OUTPUT NAME>";
    
    SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
    
    // Get credentials
    ServiceClientCredentials credentials = GetCredentials().Result;
    
    // Create Stream Analytics management client
    StreamAnalyticsManagementClient streamAnalyticsManagementClient = new StreamAnalyticsManagementClient(credentials)
    {
        SubscriptionId = ConfigurationManager.AppSettings["SubscriptionId"]
    };
   ```

De waarde van de variabele **resourceGroupName** moet gelijk zijn aan de naam van de resource groep die u in de vereiste stappen hebt gemaakt of opgenomen.

Als u het aspect van de referentie presentatie van het maken van taken wilt automatiseren, raadpleegt u [een Service-Principal verifiëren met Azure Resource Manager](../active-directory/develop/howto-authenticate-service-principal-powershell.md).

In de overige gedeelten van dit artikel wordt ervan uitgegaan dat deze code aan het begin van de methode **Main** is.

## <a name="create-a-stream-analytics-job"></a>Een Stream Analytics-taak maken
Met de volgende code wordt een Stream Analytics taak gemaakt onder de resource groep die u hebt gedefinieerd. U gaat later een invoer, uitvoer en trans formatie toevoegen aan de taak.

   ```csharp
   // Create a streaming job
   StreamingJob streamingJob = new StreamingJob()
   {
       Tags = new Dictionary<string, string>()
       {
           { "Origin", ".NET SDK" },
           { "ReasonCreated", "Getting started tutorial" }
       },
       Location = "West US",
       EventsOutOfOrderPolicy = EventsOutOfOrderPolicy.Drop,
       EventsOutOfOrderMaxDelayInSeconds = 5,
       EventsLateArrivalMaxDelayInSeconds = 16,
       OutputErrorPolicy = OutputErrorPolicy.Drop,
       DataLocale = "en-US",
       CompatibilityLevel = CompatibilityLevel.OneFullStopZero,
       Sku = new Sku()
       {
           Name = SkuName.Standard
       }
   };
   StreamingJob createStreamingJobResult = streamAnalyticsManagementClient.StreamingJobs.CreateOrReplace(streamingJob, resourceGroupName, streamingJobName);
   ```

## <a name="create-a-stream-analytics-input-source"></a>Een Stream Analytics-invoer bron maken
Met de volgende code wordt een Stream Analytics invoer bron gemaakt met het BLOB-invoer bron type en CSV-serialisatie. Gebruik **EventHubStreamInputDataSource** in plaats van **BlobStreamInputDataSource** om een event hub invoer bron te maken. Op dezelfde manier kunt u het type serialisatie van de invoer bron aanpassen.

   ```csharp
   // Create an input
   StorageAccount storageAccount = new StorageAccount()
   {
       AccountName = "<YOUR STORAGE ACCOUNT NAME>",
       AccountKey = "<YOUR STORAGE ACCOUNT KEY>"
   };
   Input input = new Input()
   {
       Properties = new StreamInputProperties()
       {
           Serialization = new CsvSerialization()
           {
               FieldDelimiter = ",",
               Encoding = Encoding.UTF8
           },
           Datasource = new BlobStreamInputDataSource()
           {
               StorageAccounts = new[] { storageAccount },
               Container = "<YOUR STORAGE BLOB CONTAINER>",
               PathPattern = "{date}/{time}",
               DateFormat = "yyyy/MM/dd",
               TimeFormat = "HH",
               SourcePartitionCount = 16
           }
       }
   };
   Input createInputResult = streamAnalyticsManagementClient.Inputs.CreateOrReplace(input, resourceGroupName, streamingJobName, inputName);
   ```

Invoer bronnen, ongeacht of deze afkomstig zijn van Blob-opslag of een Event Hub, zijn gekoppeld aan een specifieke taak. Als u dezelfde invoer bron voor verschillende taken wilt gebruiken, moet u de methode opnieuw aanroepen en een andere taak naam opgeven.

## <a name="test-a-stream-analytics-input-source"></a>Een Stream Analytics invoer bron testen
De methode **TestConnection** test of de stream Analytics taak verbinding kan maken met de invoer bron en andere aspecten die specifiek zijn voor het invoer bron type. In de BLOB-invoer bron die u in een eerdere stap hebt gemaakt, controleert de methode bijvoorbeeld of de naam en sleutel paar van het opslag account kunnen worden gebruikt om verbinding te maken met het opslag account en om te controleren of de opgegeven container bestaat.

   ```csharp
   // Test the connection to the input
   ResourceTestStatus testInputResult = streamAnalyticsManagementClient.Inputs.Test(resourceGroupName, streamingJobName, inputName);
   ```

## <a name="create-a-stream-analytics-output-target"></a>Een Stream Analytics-uitvoer doel maken
Het maken van een uitvoer doel is vergelijkbaar met het maken van een Stream Analytics invoer bron. Uitvoer doelen zijn als invoer bronnen gekoppeld aan een specifieke taak. Als u hetzelfde uitvoer doel voor verschillende taken wilt gebruiken, moet u de methode opnieuw aanroepen en een andere taak naam opgeven.

Met de volgende code wordt een uitvoer doel (Azure SQL Database) gemaakt. U kunt het gegevens type van het uitvoer doel en/of het type serialisatie aanpassen.

   ```csharp
   // Create an output
   Output output = new Output()
   {
       Datasource = new AzureSqlDatabaseOutputDataSource()
       {
           Server = "<YOUR DATABASE SERVER NAME>",
           Database = "<YOUR DATABASE NAME>",
           User = "<YOUR DATABASE LOGIN>",
           Password = "<YOUR DATABASE LOGIN PASSWORD>",
           Table = "<YOUR DATABASE TABLE NAME>"
       }
   };
   Output createOutputResult = streamAnalyticsManagementClient.Outputs.CreateOrReplace(output, resourceGroupName, streamingJobName, outputName);
   ```

## <a name="test-a-stream-analytics-output-target"></a>Een Stream Analytics-uitvoer doel testen
Een Stream Analytics uitvoer doel heeft ook de **TestConnection** -methode voor het testen van verbindingen.

   ```csharp
   // Test the connection to the output
   ResourceTestStatus testOutputResult = streamAnalyticsManagementClient.Outputs.Test(resourceGroupName, streamingJobName, outputName);
   ```

## <a name="create-a-stream-analytics-transformation"></a>Een Stream Analytics transformatie maken
Met de volgende code wordt een Stream Analytics transformatie gemaakt met de query ' Select * from input ' en wordt opgegeven dat er één streaming-eenheid voor de Stream Analytics taak moet worden toegewezen. Zie [Azure stream Analytics taken schalen](stream-analytics-scale-jobs.md)voor meer informatie over het aanpassen van streaming-eenheden.

   ```csharp
   // Create a transformation
   Transformation transformation = new Transformation()
   {
       Query = "Select Id, Name from <your input name>", // '<your input name>' should be replaced with the value you put for the 'inputName' variable above or in a previous step
       StreamingUnits = 1
   };
   Transformation createTransformationResult = streamAnalyticsManagementClient.Transformations.CreateOrReplace(transformation, resourceGroupName, streamingJobName, transformationName);
   ```

Net als bij invoer en uitvoer is een trans formatie ook gekoppeld aan de specifieke Stream Analytics taak die is gemaakt onder.

## <a name="start-a-stream-analytics-job"></a>Een Stream Analytics-taak starten
Nadat u een Stream Analytics taak en de invoer (s), uitvoer (en) en trans formatie hebt gemaakt, kunt u de taak starten door de **Start** -methode aan te roepen.

Met de volgende voorbeeld code wordt een Stream Analytics taak gestart waarbij een aangepaste start tijd voor uitvoer is ingesteld op 12 december 2012, 12:12:12 UTC:

   ```csharp
   // Start a streaming job
   StartStreamingJobParameters startStreamingJobParameters = new StartStreamingJobParameters()
   {
       OutputStartMode = OutputStartMode.CustomTime,
       OutputStartTime = new DateTime(2012, 12, 12, 12, 12, 12, DateTimeKind.Utc)
   };
   streamAnalyticsManagementClient.StreamingJobs.Start(resourceGroupName, streamingJobName, startStreamingJobParameters);
   ```

## <a name="stop-a-stream-analytics-job"></a>Een Stream Analytics taak stoppen
U kunt een actieve Stream Analytics-taak stoppen door de **Stop** -methode aan te roepen.

   ```csharp
   // Stop a streaming job
   streamAnalyticsManagementClient.StreamingJobs.Stop(resourceGroupName, streamingJobName);
   ```

## <a name="delete-a-stream-analytics-job"></a>Een Stream Analytics taak verwijderen
Met de methode **verwijderen** worden de taak en de onderliggende subbronnen, inclusief invoer (len), uitvoer (en) en trans formatie van de taak verwijderd.

   ```csharp
   // Delete a streaming job
   streamAnalyticsManagementClient.StreamingJobs.Delete(resourceGroupName, streamingJobName);
   ```

## <a name="get-support"></a>Ondersteuning krijgen
Probeer voor meer hulp onze [micro soft Q&een vraag pagina voor Azure stream Analytics](/answers/topics/azure-stream-analytics.html).

## <a name="next-steps"></a>Volgende stappen
U hebt de basis beginselen van het gebruik van een .NET SDK geleerd om analyse taken te maken en uit te voeren. Lees de volgende artikelen voor meer informatie:

* [Inleiding tot Azure Stream Analytics](stream-analytics-introduction.md)
* [Aan de slag met Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Azure Stream Analytics-taken schalen](stream-analytics-scale-jobs.md)
* [Azure stream Analytics management .NET SDK](/previous-versions/azure/dn889315(v=azure.100)).
* [Naslaggids voor Azure Stream Analytics Query](/stream-analytics-query/stream-analytics-query-language-reference)
* [REST API-naslaggids voor Azure Stream Analytics Management](/rest/api/streamanalytics/)

<!--Image references-->
[5]: ./media/markdown-template-for-new-articles/octocats.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png


<!--Link references-->
[azure.blob.storage]: https://azure.microsoft.com/documentation/services/storage/
[azure.blob.storage.use]: ../storage/blobs/storage-quickstart-blobs-dotnet.md

[azure.event.hubs]: https://azure.microsoft.com/services/event-hubs/
[azure.event.hubs.developer.guide]: /previous-versions/azure/dn789972(v=azure.100)

[stream.analytics.query.language.reference]: /stream-analytics-query/stream-analytics-query-language-reference
[stream.analytics.forum]: https://go.microsoft.com/fwlink/?LinkId=512151

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.developer.guide]: stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.query.language.reference]: /stream-analytics-query/stream-analytics-query-language-reference
[stream.analytics.rest.api.reference]: /rest/api/streamanalytics/