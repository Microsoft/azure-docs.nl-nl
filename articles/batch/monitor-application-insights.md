---
title: Bewaak batch met Azure-toepassing Insights
description: Meer informatie over het instrumenteren van een Azure Batch .NET-toepassing met behulp van de Azure-toepassing Insights-bibliotheek.
ms.topic: how-to
ms.custom: devx-track-csharp
ms.date: 04/05/2018
ms.openlocfilehash: 9decb99c3de798df43dedc2441208066d18e3a13
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104605780"
---
# <a name="monitor-and-debug-an-azure-batch-net-application-with-application-insights"></a>Een Azure Batch .NET-toepassing bewaken en fouten opsporen met Application Insights

[Application Insights](../azure-monitor/app/app-insights-overview.md) biedt ontwikkel aars een elegante en krachtige manier om toepassingen te controleren en te debuggen die zijn geïmplementeerd in Azure-Services. Gebruik Application Insights om prestatie meter items en uitzonde ringen te bewaken en uw code te instrumenteren met aangepaste metrische gegevens en tracering. Als u Application Insights integreert met uw Azure Batch-toepassing, kunt u in bijna realtime nauw keurig inzicht krijgen in gedrag en problemen onderzoeken.

In dit artikel wordt uitgelegd hoe u de Application Insights-bibliotheek kunt toevoegen en configureren in uw Azure Batch .NET-oplossing en hoe u uw toepassings code kunt instellen. U ziet ook hoe u uw toepassing kunt bewaken via de Azure Portal en aangepaste Dash boards bouwt. Raadpleeg de [documentatie over talen, platforms en integraties](../azure-monitor/app/platforms.md)voor Application Insights ondersteuning in andere talen.

In [github](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ApplicationInsights)vindt u een voor beeld van een C#-oplossing met code die moet worden meegeleverd met dit artikel. In dit voor beeld wordt Application Insights instrumentatie code toegevoegd aan het [TopNWords](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/TopNWords) -voor beeld. Als u niet bekend bent met dit voor beeld, probeer dan eerst TopNWords te bouwen en uit te voeren. Dit helpt u inzicht te krijgen in een eenvoudige batch-werk stroom van het verwerken van een set invoer-blobs parallel op meerdere reken knooppunten. 

> [!TIP]
> Als alternatief kunt u uw batch-oplossing configureren om Application Insights gegevens weer te geven, zoals prestatie meter items voor virtuele machines in Batch Explorer. [Batch Explorer](https://github.com/Azure/BatchExplorer) is een gratis, uitgebreid, zelfstandig client hulpprogramma waarmee Azure batch toepassingen kunnen worden gemaakt, opgespoord en gecontroleerd. Download een [installatiepakket](https://azure.github.io/BatchExplorer/) voor Mac, Linux of Windows. Raadpleeg de [batch-Insights-opslag plaats](https://github.com/Azure/batch-insights) voor snelle stappen om Application Insights gegevens in batch Explorer in te scha kelen. 
>

## <a name="prerequisites"></a>Vereisten
* [Visual Studio 2017 of hoger](https://www.visualstudio.com/vs)

* [Batch-account en gekoppeld opslag account](batch-account-create-portal.md)

* [Application Insights-resource](../azure-monitor/app/create-new-resource.md )
  
   * Gebruik de Azure Portal om een Application Insights *resource* te maken. Selecteer het type *algemene* **toepassing**.

   * Kopieer de [instrumentatie sleutel](../azure-monitor/app/create-new-resource.md#copy-the-instrumentation-key) vanuit de portal. Verderop in dit artikel is dit vereist.
  
  > [!NOTE]
  > Mogelijk worden er [kosten in rekening gebracht](https://azure.microsoft.com/pricing/details/application-insights/) voor de gegevens die zijn opgeslagen in Application Insights. Dit omvat de diagnostische en bewakings gegevens die in dit artikel worden besproken.
  > 

## <a name="add-application-insights-to-your-project"></a>Application Insights toevoegen aan uw project

Het **micro soft. ApplicationInsights. Windowsserver** NuGet-pakket en de bijbehorende afhankelijkheden zijn vereist voor uw project. Deze toevoegen aan of herstellen in het project van uw toepassing. Gebruik de `Install-Package` opdracht of NuGet Package Manager om het pakket te installeren.

```powershell
Install-Package Microsoft.ApplicationInsights.WindowsServer
```
Referentie Application Insights van uw .NET-toepassing met behulp van de **micro soft. ApplicationInsights** -naam ruimte.

## <a name="instrument-your-code"></a>Uw code instrumenteren

Uw oplossing moet een Application Insights [TelemetryClient](/dotnet/api/microsoft.applicationinsights.telemetryclient)maken om uw code te instrumenteren. In het voor beeld wordt de configuratie van de TelemetryClient geladen uit het [ApplicationInsights.config](../azure-monitor/app/configuration-with-applicationinsights-config.md) -bestand. Zorg ervoor dat u ApplicationInsights.config in de volgende projecten bijwerkt met uw Application Insights instrumentatie sleutel: Microsoft.Azure.Batch. Samples. TelemetryStartTask en TopNWordsSample.

```xml
<InstrumentationKey>YOUR-IKEY-GOES-HERE</InstrumentationKey>
```
Voeg ook de instrumentatie sleutel toe in het bestand TopNWords. cs.

In het voor beeld in TopNWords. cs worden de volgende [instrumentatie aanroepen](../azure-monitor/app/api-custom-events-metrics.md) van de Application INSIGHTS-API gebruikt:
* `TrackMetric()` -Houdt in hoe lang, het gemiddelde van een reken knooppunt het vereiste tekst bestand downloadt.
* `TrackTrace()` -Voegt fout opsporingsgegevens toe aan uw code.
* `TrackEvent()` -Hiermee worden interessante gebeurtenissen bijgehouden die moeten worden vastgelegd.

In dit voor beeld wordt uitzonde ring verwerkt. In plaats daarvan Application Insights automatisch onverwerkte uitzonde ringen, waardoor de fout opsporing aanzienlijk wordt verbeterd. 

Het volgende code fragment laat zien hoe u deze methoden gebruikt.

```csharp
public void CountWords(string blobName, int numTopN, string storageAccountName, string storageAccountKey)
{
    // simulate exception for some set of tasks
    Random rand = new Random();
    if (rand.Next(0, 10) % 10 == 0)
    {
        blobName += ".badUrl";
    }

    // log the url we are downloading the file from
    insightsClient.TrackTrace(new TraceTelemetry(string.Format("Task {0}: Download file from: {1}", this.taskId, blobName), SeverityLevel.Verbose));

    // open the cloud blob that contains the book
    var storageCred = new StorageCredentials(storageAccountName, storageAccountKey);
    CloudBlockBlob blob = new CloudBlockBlob(new Uri(blobName), storageCred);
    using (Stream memoryStream = new MemoryStream())
    {
        // calculate blob download time
        DateTime start = DateTime.Now;
        blob.DownloadToStream(memoryStream);
        TimeSpan downloadTime = DateTime.Now.Subtract(start);

        // track how long the blob takes to download on this node
        // this will help debug timing issues or identify poorly performing nodes
        insightsClient.TrackMetric("Blob download in seconds", downloadTime.TotalSeconds, this.CommonProperties);

        memoryStream.Position = 0; //Reset the stream
        var sr = new StreamReader(memoryStream);
        var myStr = sr.ReadToEnd();
        string[] words = myStr.Split(' ');

        // log how many words were found in the text file
        insightsClient.TrackTrace(new TraceTelemetry(string.Format("Task {0}: Found {1} words", this.taskId, words.Length), SeverityLevel.Verbose));
        var topNWords =
            words.
                Where(word => word.Length > 0).
                GroupBy(word => word, (key, group) => new KeyValuePair<String, long>(key, group.LongCount())).
                OrderByDescending(x => x.Value).
                Take(numTopN).
                ToList();
        foreach (var pair in topNWords)
        {
            Console.WriteLine("{0} {1}", pair.Key, pair.Value);
        }

        // emit an event to track the completion of the task
        insightsClient.TrackEvent("Done counting words");
    }
}
```

### <a name="azure-batch-telemetry-initializer-helper"></a>Hulp bij het initialiseren van Azure Batch telemetrie
Wanneer u telemetrie rapporteert voor een bepaalde server en exemplaar, Application Insights gebruikt de Azure VM-rol en de VM-naam voor de standaard waarden. In de context van Azure Batch ziet u in het voor beeld hoe u de naam van de pool en de naam van het reken knooppunt kunt gebruiken. Gebruik een [telemetrie-initialisatie functie](../azure-monitor/app/api-filtering-sampling.md#add-properties) voor het overschrijven van de standaard waarden. 

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.Extensibility;
using System;
using System.Threading;

namespace Microsoft.Azure.Batch.Samples.TelemetryInitializer
{
    public class AzureBatchNodeTelemetryInitializer : ITelemetryInitializer
    {
        // Azure Batch environment variables
        private const string PoolIdEnvironmentVariable = "AZ_BATCH_POOL_ID";
        private const string NodeIdEnvironmentVariable = "AZ_BATCH_NODE_ID";

        private string roleInstanceName;
        private string roleName;

        public void Initialize(ITelemetry telemetry)
        {
            if (string.IsNullOrEmpty(telemetry.Context.Cloud.RoleName))
            {
                // override the role name with the Azure Batch Pool name
                string name = LazyInitializer.EnsureInitialized(ref this.roleName, this.GetPoolName);
                telemetry.Context.Cloud.RoleName = name;
            }

            if (string.IsNullOrEmpty(telemetry.Context.Cloud.RoleInstance))
            {
                // override the role instance with the Azure Batch Compute Node name
                string name = LazyInitializer.EnsureInitialized(ref this.roleInstanceName, this.GetNodeName);
                telemetry.Context.Cloud.RoleInstance = name;
            }
        }

        private string GetPoolName()
        {
            return Environment.GetEnvironmentVariable(PoolIdEnvironmentVariable) ?? string.Empty;
        }

        private string GetNodeName()
        {
            return Environment.GetEnvironmentVariable(NodeIdEnvironmentVariable) ?? string.Empty;
        }
    }
}
```

Het ApplicationInsights.config-bestand in het TopNWordsSample-project bevat de volgende opties om de initialisatie functie voor telemetrie in te scha kelen:

```xml
<TelemetryInitializers>
    <Add Type="Microsoft.Azure.Batch.Samples.TelemetryInitializer.AzureBatchNodeTelemetryInitializer, Microsoft.Azure.Batch.Samples.TelemetryInitializer"/>
</TelemetryInitializers>
``` 

## <a name="update-the-job-and-tasks-to-include-application-insights-binaries"></a>De taak en taken voor het toevoegen van Application Insights binaire bestanden bijwerken

Controleer of de binaire bestanden correct zijn geplaatst om Application Insights correct op uw reken knooppunten uit te voeren. Voeg de vereiste binaire bestanden toe aan de verzameling resource bestanden van uw taak, zodat ze worden gedownload op het moment dat de taak wordt uitgevoerd. De volgende fragmenten zijn vergelijkbaar met de code in job. cs.

Maak eerst een statische lijst met Application Insights bestanden die u wilt uploaden.

```csharp
private static readonly List<string> AIFilesToUpload = new List<string>()
{
    // Application Insights config and assemblies
    "ApplicationInsights.config",
    "Microsoft.ApplicationInsights.dll",
    "Microsoft.AI.Agent.Intercept.dll",
    "Microsoft.AI.DependencyCollector.dll",
    "Microsoft.AI.PerfCounterCollector.dll",
    "Microsoft.AI.ServerTelemetryChannel.dll",
    "Microsoft.AI.WindowsServer.dll",
    
    // custom telemetry initializer assemblies
    "Microsoft.Azure.Batch.Samples.TelemetryInitializer.dll",
 };
...
```

Maak vervolgens de tijdelijke bestanden die worden gebruikt door de taak.
```csharp
...
// create file staging objects that represent the executable and its dependent assembly to run as the task.
// These files are copied to every node before the corresponding task is scheduled to run on that node.
FileToStage topNWordExe = new FileToStage(TopNWordsExeName, stagingStorageAccount);
FileToStage storageDll = new FileToStage(StorageClientDllName, stagingStorageAccount);

// Upload Application Insights assemblies
List<FileToStage> aiStagedFiles = new List<FileToStage>();
foreach (string aiFile in AIFilesToUpload)
{
    aiStagedFiles.Add(new FileToStage(aiFile, stagingStorageAccount));
}
...
```

De `FileToStage` methode is een Help-functie in het code voorbeeld waarmee u eenvoudig een bestand van een lokale schijf naar een Azure Storage-BLOB kunt uploaden. Elk bestand wordt later gedownload naar een reken knooppunt en ernaar wordt verwezen door een taak.

Voeg ten slotte de taken toe aan de taak en neem de benodigde Application Insights binaire bestanden op.
```csharp
...
// initialize a collection to hold the tasks that will be submitted in their entirety
List<CloudTask> tasksToRun = new List<CloudTask>(topNWordsConfiguration.NumberOfTasks);
for (int i = 1; i <= topNWordsConfiguration.NumberOfTasks; i++)
{
    CloudTask task = new CloudTask("task_no_" + i, String.Format("{0} --Task {1} {2} {3} {4}",
        TopNWordsExeName,
        string.Format("https://{0}.blob.core.windows.net/{1}",
            accountSettings.StorageAccountName,
            documents[i]),
        topNWordsConfiguration.TopWordCount,
        accountSettings.StorageAccountName,
        accountSettings.StorageAccountKey));

    //This is the list of files to stage to a container -- for each job, one container is created and 
    //files all resolve to Azure Blobs by their name (so two tasks with the same named file will create just 1 blob in
    //the container).
    task.FilesToStage = new List<IFileStagingProvider>
                        {
                            // required application binaries
                            topNWordExe,
                            storageDll,
                        };
    foreach (FileToStage stagedFile in aiStagedFiles)
   {
        task.FilesToStage.Add(stagedFile);
   }    
    task.RunElevated = false;
    tasksToRun.Add(task);
}
```

## <a name="view-data-in-the-azure-portal"></a>Gegevens weer geven in de Azure Portal

Nu u de taak en taken hebt geconfigureerd voor het gebruik van Application Insights, voert u de voorbeeld taak uit in uw pool. Ga naar het Azure Portal en open de Application Insights resource die u hebt ingericht. Nadat de groep is ingericht, moet u de gegevens stroom en het vastgelegde logboek weer geven. De rest van dit artikel raakt slechts op enkele Application Insights functies, maar u kunt de volledige functieset bekijken.

### <a name="view-live-stream-data"></a>Live Stream-gegevens weer geven

Als u traceer logboeken wilt weer geven in uw toepassingen Insights-resource, klikt u op **Live Stream**. In de volgende scherm afbeelding ziet u hoe u Live-gegevens kunt weer geven die afkomstig zijn van de reken knooppunten in de groep, bijvoorbeeld het CPU-gebruik per reken knooppunt.

![Gegevens van reken knooppunten van Live Stream](./media/monitor-application-insights/applicationinsightslivestream.png)

### <a name="view-trace-logs"></a>Traceer logboeken weer geven

Als u traceer logboeken wilt weer geven in uw Applications Insights-resource, klikt u op **zoeken**. In deze weer gave ziet u een lijst met diagnostische gegevens die zijn vastgelegd door Application Insights, met inbegrip van traceringen, gebeurtenissen en uitzonde ringen. 

In de volgende scherm afbeelding ziet u hoe een enkele tracering voor een taak wordt geregistreerd en later wordt gevraagd om fouten op te sporen.

![Afbeelding van traceer logboeken](./media/monitor-application-insights/tracelogsfortask.png)

### <a name="view-unhandled-exceptions"></a>Niet-verwerkte uitzonde ringen weer geven

In de volgende scherm afbeeldingen ziet u hoe Application Insights logboeken uitzonde ringen veroorzaakt vanuit uw toepassing. In dit geval kunt u binnen enkele seconden van de toepassing die de uitzonde ring opvoert, een specifieke uitzonde ring bekijken en het probleem vaststellen.

![Onverwerkte uitzonderingen](./media/monitor-application-insights/exception.png)

### <a name="measure-blob-download-time"></a>Download tijd van BLOB meten

Aangepaste metrische gegevens zijn ook een waardevol hulp middel in de portal. U kunt bijvoorbeeld de gemiddelde tijd die nodig is voor elk reken knooppunt weer geven om het vereiste tekst bestand dat het verwerkt is te downloaden.

Een voorbeeld grafiek maken:
1. Klik in de Application Insights resource op **Metrics Explorer**  >  **grafiek toevoegen**.
2. Klik op **bewerken** in de grafiek die is toegevoegd.
2. Werk de grafiek Details als volgt bij:
   * Stel het **grafiek type** in op **raster**.
   * Stel **aggregatie** in op **gemiddelde**.
   * Stel **Group by** in op **NodeId**.
   * Selecteer in **metrische gegevens** de optie **aangepaste**  >  **BLOB-down load in seconden**.
   * Pas de weer gave **kleuren palet** aan uw keuze aan. 

![Download tijd voor BLOB per knoop punt](./media/monitor-application-insights/blobdownloadtime.png)


## <a name="monitor-compute-nodes-continuously"></a>Reken knooppunten continu bewaken

U hebt wellicht gezien dat alle metrische gegevens, met inbegrip van prestatie meter items, alleen worden geregistreerd wanneer de taken worden uitgevoerd. Dit gedrag is handig omdat hiermee de hoeveelheid gegevens die Application Insights Logboeken wordt beperkt. Er zijn echter gevallen waarin u de reken knooppunten altijd wilt bewaken. Ze kunnen bijvoorbeeld achtergrond taken uitvoeren die niet zijn gepland via de batch-service. In dit geval moet u een bewakings proces instellen om te worden uitgevoerd gedurende de levens duur van het reken knooppunt. 

Een van de manieren om dit gedrag te verzorgen is het starten van een proces waarmee de Application Insights-bibliotheek wordt geladen en op de achtergrond wordt uitgevoerd. In het voor beeld worden met de begin taak de binaire bestanden op de computer geladen en wordt een proces voor onbepaalde tijd uitgevoerd. Configureer het Application Insights configuratie bestand voor dit proces voor het verzenden van extra gegevens die u wilt, zoals prestatie meter items.

```csharp
...
 // Batch start task telemetry runner
private const string BatchStartTaskFolderName = "StartTask";
private const string BatchStartTaskTelemetryRunnerName = "Microsoft.Azure.Batch.Samples.TelemetryStartTask.exe";
private const string BatchStartTaskTelemetryRunnerAIConfig = "ApplicationInsights.config";
...
CloudPool pool = client.PoolOperations.CreatePool(
    topNWordsConfiguration.PoolId,
    targetDedicated: topNWordsConfiguration.PoolNodeCount,
    virtualMachineSize: "standard_d1_v2",
    cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5"));
...

// Create a start task which will run a dummy exe in background that simply emits performance
// counter data as defined in the relevant ApplicationInsights.config.
// Note that the waitForSuccess on the start task was not set so the Compute Node will be
// available immediately after this command is run.
pool.StartTask = new StartTask()
{
    CommandLine = string.Format("cmd /c {0}", BatchStartTaskTelemetryRunnerName),
    ResourceFiles = resourceFiles
};
...
```

> [!TIP]
> Als u de beheer baarheid van uw oplossing wilt verg Roten, kunt u de assembly bundelen in een [toepassings pakket](./batch-application-packages.md). U kunt het toepassings pakket vervolgens automatisch implementeren in uw Pools door een toepassings pakket verwijzing toe te voegen aan de groeps configuratie.
>

## <a name="throttle-and-sample-data"></a>Gashendel en voorbeeld gegevens 

Als gevolg van de grootschalige aard van Azure Batch toepassingen die in productie worden uitgevoerd, wilt u mogelijk de hoeveelheid gegevens die door Application Insights wordt verzameld, beperken om de kosten te beheren. Zie [sampling in Application Insights](../azure-monitor/app/sampling.md) voor een aantal mechanismen om dit te doen.


## <a name="next-steps"></a>Volgende stappen
* Meer informatie over [Application Insights](../azure-monitor/app/app-insights-overview.md).

* Raadpleeg de [documentatie over talen, platforms en integraties](../azure-monitor/app/platforms.md)voor Application Insights ondersteuning in andere talen.


