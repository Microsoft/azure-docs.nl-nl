---
title: Batch bewaken met Azure-toepassing Insights
description: Informatie over het instrumenteren van een Azure Batch .NET-toepassing met behulp van de Azure-toepassing Insights-bibliotheek.
ms.topic: how-to
ms.custom: devx-track-csharp
ms.date: 04/13/2021
ms.openlocfilehash: 8bc8ff0a04996d988a642062f118e9e6792abbf0
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389346"
---
# <a name="monitor-and-debug-an-azure-batch-net-application-with-application-insights"></a>Een .NET-toepassing met Azure Batch bewaken en fouten opsporen met Application Insights

[Application Insights](../azure-monitor/app/app-insights-overview.md) biedt een elegante en krachtige manier voor ontwikkelaars om toepassingen te bewaken en fouten op te sporen die zijn geïmplementeerd in Azure-services. Gebruik Application Insights prestatiemeters en -uitzonderingen te bewaken en uw code te instrumenteren met aangepaste metrische gegevens en tracering. Door Application Insights te integreren met uw Azure Batch-toepassing, kunt u meer inzicht krijgen in gedrag en problemen bijna in realtime onderzoeken.

In dit artikel wordt beschreven hoe u de Application Insights-bibliotheek toevoegt en configureert in Azure Batch .NET-oplossing en uw toepassingscode instrumenteert. U ziet ook manieren om uw toepassing te bewaken via de Azure Portal en aangepaste dashboards te bouwen. Zie Application Insights, platformen en integratiedocumentatie voor meer informatie over de ondersteuning [in andere talen.](../azure-monitor/app/platforms.md)

Een C#-voorbeeldoplossing met code bij dit artikel is beschikbaar op [GitHub.](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ApplicationInsights) In dit voorbeeld wordt Application Insights instrumentatiecode toegevoegd aan het [TopNWords-voorbeeld.](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/TopNWords) Als u niet bekend bent met dat voorbeeld, kunt u eerst TopNWords bouwen en uitvoeren. Als u dit doet, krijgt u inzicht in een eenvoudige Batch-werkstroom voor het parallel verwerken van een set invoer-blobs op meerdere rekenknooppunten.

> [!TIP]
> Als alternatief kunt u uw Batch-oplossing zo configureren dat er Application Insights worden weergegeven, zoals prestatiemeters voor virtuele Batch Explorer. [Batch Explorer](https://github.com/Azure/BatchExplorer) is een gratis, uitgebreid, zelfstandig clienthulpprogramma voor het maken, opsporen van fouten en het bewaken van Azure Batch toepassingen. Download een [installatiepakket](https://azure.github.io/BatchExplorer/) voor Mac, Linux of Windows. Zie de [batch-insights-repo](https://github.com/Azure/batch-insights) voor snelle stappen voor het inschakelen Application Insights gegevens in Batch Explorer.

## <a name="prerequisites"></a>Vereisten

- [Visual Studio 2017 of hoger](https://www.visualstudio.com/vs)
- [Batch-account en gekoppeld opslagaccount](batch-account-create-portal.md)
- [Application Insights resource .](../azure-monitor/app/create-new-resource.md) Gebruik de Azure Portal om een nieuwe Application Insights *maken.* Selecteer het *type Algemene* **toepassing.**
- Kopieer de [instrumentatiesleutel](../azure-monitor/app/create-new-resource.md#copy-the-instrumentation-key) uit de Azure Portal. U hebt deze waarde later nog nodig.
  
  > [!NOTE]
  > Er worden mogelijk [kosten in rekening gebracht](https://azure.microsoft.com/pricing/details/application-insights/) voor gegevens die zijn opgeslagen in Application Insights. Dit omvat de diagnostische en bewakingsgegevens die in dit artikel worden besproken.

## <a name="add-application-insights-to-your-project"></a>Een Application Insights aan uw project toevoegen

Het **NuGet-pakket Microsoft.ApplicationInsights.WindowsServer** en de afhankelijkheden zijn vereist voor uw project. Voeg ze toe aan of herstel ze in het project van uw toepassing. Als u het pakket wilt installeren, gebruikt u `Install-Package` de opdracht of NuGet Pakketbeheer.

```powershell
Install-Package Microsoft.ApplicationInsights.WindowsServer
```

Verwijs Application Insights naar uw .NET-toepassing met behulp van de **Naamruimte Microsoft.ApplicationInsights.**

## <a name="instrument-your-code"></a>Uw code instrumentatie

Om uw code te instrumenteert, moet uw oplossing een Application Insights [TelemetryClient](/dotnet/api/microsoft.applicationinsights.telemetryclient)maken. In het voorbeeld wordt de configuratie van de TelemetryClient uit hetApplicationInsights.config[geladen.](../azure-monitor/app/configuration-with-applicationinsights-config.md) Zorg ervoor dat u ApplicationInsights.config in de volgende projecten bij te werken met uw Application Insights instrumentatiesleutel: Microsoft.Azure.Batch. Samples.TelemetryStartTask en TopNWordsSample.

```xml
<InstrumentationKey>YOUR-IKEY-GOES-HERE</InstrumentationKey>
```

Voeg ook de instrumentatiesleutel toe aan het bestand TopNWords.cs.

In het voorbeeld in TopNWords.cs worden de volgende [instrumentatie-aanroepen](../azure-monitor/app/api-custom-events-metrics.md) van de api Application Insights gebruikt:

- `TrackMetric()` - Houdt bij hoe lang het gemiddeld duurt voordat een reken knooppunt het vereiste tekstbestand downloadt.
- `TrackTrace()` - Hiermee voegt u aanroepen voor debuggen toe aan uw code.
- `TrackEvent()` - Houdt interessante gebeurtenissen bij om vast te leggen.

In dit voorbeeld wordt de afhandeling van uitzonderingen met opzet weg laten. In plaats Application Insights automatisch niet-verhandelde uitzonderingen rapporteert, waardoor de ervaring voor het debuggen aanzienlijk wordt verbeterd.

In het volgende fragment ziet u hoe u deze methoden gebruikt.

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

### <a name="azure-batch-telemetry-initializer-helper"></a>Azure Batch helper voor initialisatie van telemetrie

Bij het rapporteren van telemetrie voor een bepaalde server en instantie gebruikt Application Insights azure-VM-rol en VM-naam voor de standaardwaarden. In de context van Azure Batch voorbeeld laten we zien hoe u in plaats daarvan de poolnaam en de naam van het reken knooppunt gebruikt. Gebruik een [initialisatie voor telemetrie](../azure-monitor/app/api-filtering-sampling.md#add-properties) om de standaardwaarden te overschrijven.

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

Als u de initialisatie van de telemetrie wilt inschakelen, bevat ApplicationInsights.config bestand in het project TopNWordsSample het volgende:

```xml
<TelemetryInitializers>
    <Add Type="Microsoft.Azure.Batch.Samples.TelemetryInitializer.AzureBatchNodeTelemetryInitializer, Microsoft.Azure.Batch.Samples.TelemetryInitializer"/>
</TelemetryInitializers>
```

## <a name="update-the-job-and-tasks-to-include-application-insights-binaries"></a>Werk de job en taken bij om binaire Application Insights op te nemen

Zorg ervoor dat Application Insights op de juiste wijze worden uitgevoerd op uw rekenknooppunten, zodat de binaire bestanden correct zijn geplaatst. Voeg de vereiste binaire bestanden toe aan de verzameling resourcebestanden van uw taak, zodat ze worden gedownload op het moment dat uw taak wordt uitgevoerd. De volgende codefragmenten zijn vergelijkbaar met code in Job.cs.

Maak eerst een statische lijst met de Application Insights die u wilt uploaden.

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

Maak vervolgens de faseringsbestanden die door de taak worden gebruikt.

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

De `FileToStage` methode is een helperfunctie in het codevoorbeeld waarmee u eenvoudig een bestand van de lokale schijf kunt uploaden naar Azure Storage blob. Elk bestand wordt later gedownload naar een reken knooppunt en er wordt naar verwezen door een taak.

Voeg ten slotte de taken toe aan de job en neem de benodigde binaire Application Insights op.

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

## <a name="view-data-in-the-azure-portal"></a>Gegevens weergeven in de Azure Portal

Nu u de job en taken hebt geconfigureerd voor het gebruik van Application Insights, kunt u de voorbeeldtaken uitvoeren in uw pool. Navigeer naar Azure Portal en open de Application Insights resource die u hebt ingericht. Nadat de pool is ingericht, ziet u dat de gegevens stromen en worden geregistreerd. In de rest van dit artikel worden slechts enkele Application Insights beschreven, maar u kunt gerust de volledige functieset verkennen.

### <a name="view-live-stream-data"></a>Livestreamgegevens weergeven

Als u traceerlogboeken in uw Applications Insights-resource wilt weergeven, klikt u **Live Stream**. In de volgende schermopname ziet u hoe u livegegevens kunt bekijken die afkomstig zijn van de rekenknooppunten in de pool, bijvoorbeeld het CPU-gebruik per rekenknooppunt.

![Schermopname van reken knooppuntgegevens van de livestream.](./media/monitor-application-insights/applicationinsightslivestream.png)

### <a name="view-trace-logs"></a>Traceerlogboeken weergeven

Als u traceerlogboeken in uw Applications Insights-resource wilt weergeven, klikt u op **Zoeken.** Deze weergave toont een lijst met diagnostische gegevens die zijn vastgelegd door Application Insights traceringen, gebeurtenissen en uitzonderingen. 

In de volgende schermopname ziet u hoe één trace voor een taak wordt vastgelegd en later wordt opgevraagd voor het opsporen van gegevens.

![Schermopname van logboeken voor één trace.](./media/monitor-application-insights/tracelogsfortask.png)

### <a name="view-unhandled-exceptions"></a>Onverhandelde uitzonderingen weergeven

Application Insights logboeken die zijn opgeworpen vanuit uw toepassing. In dit geval kunt u binnen enkele seconden na de uitzondering inzoomen op een specifieke uitzondering en het probleem vaststellen.

![Schermopname met onverhandelde uitzonderingen.](./media/monitor-application-insights/exception.png)

### <a name="measure-blob-download-time"></a>Downloadtijd blob meten

Aangepaste metrische gegevens zijn ook een waardevol hulpmiddel in de portal. U kunt bijvoorbeeld de gemiddelde tijd weergeven die elk reken knooppunt nodig had om het vereiste tekstbestand te downloaden dat werd verwerkt.

Een voorbeeldgrafiek maken:

1. Klik in Application Insights resource op **Metrics Explorer**  >  **Grafiek toevoegen.**
1. Klik **op Bewerken** in de grafiek die is toegevoegd.
1. Werk de grafiekdetails als volgt bij:
   - Stel **Grafiektype in** op **Raster.**
   - Stel **Aggregatie in** op **Gemiddelde.**
   - Stel **Groep op in** op **NodeId.**
   - Selecteer **in Metrische** gegevens **de optie Aangepaste** blob downloaden in  >  **seconden**.
   - Pas het **kleurenpalet van het weergavepalet** aan uw keuze aan.

![Schermopname van een grafiek met de downloadtijd van blobs per knooppunt.](./media/monitor-application-insights/blobdownloadtime.png)

## <a name="monitor-compute-nodes-continuously&quot;></a>Rekenknooppunten continu bewaken

Mogelijk hebt u opgemerkt dat alle metrische gegevens, inclusief prestatiemeters, alleen worden geregistreerd wanneer de taken worden uitgevoerd. Dit gedrag is handig omdat hiermee de hoeveelheid gegevens wordt beperkt die Application Insights logboeken. Er zijn echter gevallen waarin u altijd de rekenknooppunten wilt bewaken. Ze kunnen bijvoorbeeld achtergrondwerk uitvoeren dat niet is gepland via de Batch-service. In dit geval stelt u een bewakingsproces in dat wordt uitgevoerd voor de levensduur van het reken knooppunt. 

Een manier om dit gedrag te bereiken, is door een proces te starten dat de Application Insights-bibliotheek laadt en op de achtergrond wordt uitgevoerd. In het voorbeeld laadt de begintaak de binaire bestanden op de computer en blijft een proces voor onbepaalde tijd actief. Configureer Application Insights configuratiebestand voor dit proces om aanvullende gegevens te zenden waarin u bent geïnteresseerd, zoals prestatiemeters.

```csharp
...
 // Batch start task telemetry runner
private const string BatchStartTaskFolderName = &quot;StartTask&quot;;
private const string BatchStartTaskTelemetryRunnerName = &quot;Microsoft.Azure.Batch.Samples.TelemetryStartTask.exe&quot;;
private const string BatchStartTaskTelemetryRunnerAIConfig = &quot;ApplicationInsights.config&quot;;
...
CloudPool pool = client.PoolOperations.CreatePool(
    topNWordsConfiguration.PoolId,
    targetDedicated: topNWordsConfiguration.PoolNodeCount,
    virtualMachineSize: &quot;standard_d1_v2&quot;,
    VirtualMachineConfiguration: new VirtualMachineConfiguration(
    imageReference: new ImageReference(
                        publisher: &quot;MicrosoftWindowsServer&quot;,
                        offer: &quot;WindowsServer&quot;,
                        sku: &quot;2019-datacenter-core&quot;,
                        version: &quot;latest"),
    nodeAgentSkuId: "batch.node.windows amd64");
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
> Als u de beheerbaarheid van uw oplossing wilt vergroten, kunt u de assembly bundelen in een [toepassingspakket](./batch-application-packages.md). Als u het toepassingspakket vervolgens automatisch wilt implementeren in uw pools, voegt u een toepassingspakketverwijzing toe naar de poolconfiguratie.

## <a name="throttle-and-sample-data"></a>Gegevens van vertragingen en voorbeelden

Vanwege de grootschalige aard van Azure Batch toepassingen die in productie worden uitgevoerd, wilt u mogelijk de hoeveelheid gegevens beperken die wordt verzameld door Application Insights kosten te beheren. Zie [Sampling in Application Insights](../azure-monitor/app/sampling.md) voor een aantal mechanismen om dit te bereiken.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [Application Insights](../azure-monitor/app/app-insights-overview.md).
- Zie Application Insights, platformen en integratiedocumentatie voor meer informatie over de ondersteuning [in andere talen.](../azure-monitor/app/platforms.md)
