---
title: Taken gelijktijdig uitvoeren om het gebruik van Batch-rekenknooppunten te maximaliseren
description: De efficiëntie verhogen en de kosten verlagen door minder rekenknooppunten te gebruiken en taken parallel uit te voeren op elk knooppunt in een Azure Batch pool
ms.topic: how-to
ms.date: 04/13/2021
ms.custom: H1Hack27Feb2017, devx-track-csharp
ms.openlocfilehash: 81648f30a7a02f702dcb189aa7df27e5a82e2b07
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389295"
---
# <a name="run-tasks-concurrently-to-maximize-usage-of-batch-compute-nodes"></a>Taken gelijktijdig uitvoeren om het gebruik van Batch-rekenknooppunten te maximaliseren

U kunt het resourcegebruik op een kleiner aantal rekenknooppunten in uw pool maximaliseren door meer dan één taak tegelijkertijd op elk knooppunt uit te voeren.

Hoewel sommige scenario's het beste werken met alle resources van een knooppunt die zijn toegewezen aan één taak, kunnen bepaalde workloads kortere taaktijden en lagere kosten ervaren wanneer meerdere taken deze resources delen. Denk eens na over de volgende scenario's:

- **Minimaliseer de gegevensoverdracht** voor taken die gegevens kunnen delen. U kunt de kosten voor gegevensoverdracht aanzienlijk verlagen door gedeelde gegevens naar een kleiner aantal knooppunten te kopiëren en vervolgens taken parallel op elk knooppunt uit te voeren. Dit geldt met name als de gegevens die naar elk knooppunt moeten worden gekopieerd, moeten worden overgedragen tussen geografische regio's.
- **Maximaliseer het** geheugengebruik voor taken waarvoor een grote hoeveelheid geheugen nodig is, maar alleen tijdens korte perioden en op variabele tijden tijdens de uitvoering. U kunt minder, maar grotere rekenknooppunten met meer geheugen gebruiken om dergelijke pieken efficiënt te verwerken. Op deze knooppunten worden meerdere taken parallel uitgevoerd op elk knooppunt, maar elke taak kan op verschillende tijdstippen profiteren van het geheugen van de knooppunten.
- **Verminder de limieten voor knooppuntaantal** wanneer communicatie tussen knooppunts in een pool is vereist. Momenteel zijn pools die zijn geconfigureerd voor communicatie tussen knooppunten beperkt tot 50 rekenknooppunten. Als elk knooppunt in een dergelijke pool taken parallel kan uitvoeren, kan een groter aantal taken tegelijkertijd worden uitgevoerd.
- **Repliceer een on-premises rekencluster,** bijvoorbeeld wanneer u een rekenomgeving voor het eerst naar Azure verplaatst. Als uw huidige on-premises oplossing meerdere taken per rekenpunt uitvoert, kunt u het maximum aantal knooppunttaken verhogen om die configuratie beter te spiegelen.

## <a name="example-scenario"></a>Voorbeeldscenario

Stel u bijvoorbeeld een taaktoepassing voor met CPU- en geheugenvereisten, zodat [Standard \_ D1-knooppunten](../cloud-services/cloud-services-sizes-specs.md#d-series) voldoende zijn. Als u de taak echter binnen de vereiste tijd wilt voltooien, zijn er 1000 van deze knooppunten nodig.

In plaats van Standard \_ D1-knooppunten met 1 CPU-kern te gebruiken, kunt u [Standard \_ D14-knooppunten](../cloud-services/cloud-services-sizes-specs.md#d-series) met elk 16 kernen gebruiken en parallelle taakuitvoering inschakelen. Dit betekent dat er 16 keer minder knooppunten kunnen worden gebruikt: in plaats van 1000 knooppunten zijn er slechts 63 vereist. Als er grote toepassingsbestanden of referentiegegevens vereist zijn voor elk knooppunt, worden de duur en efficiëntie van de taak verbeterd, omdat de gegevens naar slechts 63 knooppunten worden gekopieerd.

## <a name="enable-parallel-task-execution"></a>Parallelle taakuitvoering inschakelen

U configureert rekenknooppunten voor parallelle taakuitvoering op poolniveau. Stel met de Batch .NET-bibliotheek de eigenschap [CloudPool.TaskSlotsPerNode](/dotnet/api/microsoft.azure.batch.cloudpool.taskslotspernode) in wanneer u een pool maakt. Als u de Batch-REST API, stelt u het element [taskSlotsPerNode](/rest/api/batchservice/pool/add) in de aanvraag body in tijdens het maken van de pool.

> [!NOTE]
> U kunt het `taskSlotsPerNode` element en de eigenschap [TaskSlotsPerNode](/dotnet/api/microsoft.azure.batch.cloudpool) alleen instellen op het moment dat de pool wordt gemaakt. Ze kunnen niet worden gewijzigd nadat een pool al is gemaakt.

Azure Batch kunt u taaksleuven per knooppunt instellen tot (4x) het aantal knooppuntkernen. Als de pool bijvoorbeeld is geconfigureerd met knooppunten van grootte 'Groot' (vier kernen), kan `taskSlotsPerNode` worden ingesteld op 16. Ongeacht het aantal kernen dat het knooppunt heeft, kunt u echter niet meer dan 256 taaksleuven per knooppunt hebben. Zie Grootten voor Cloud Services voor meer informatie over het aantal kernen voor elk van de [knooppuntgrootten.](../cloud-services/cloud-services-sizes-specs.md) Zie Quota en limieten voor de Azure Batch [service voor meer informatie over servicelimieten.](batch-quota-limit.md)

> [!TIP]
> Zorg ervoor dat u rekening houdt met de `taskSlotsPerNode` waarde wanneer u een formule voor automatisch [schalen voor](/rest/api/batchservice/pool/enableautoscale) uw pool maakt. Een formule die evalueert, kan bijvoorbeeld aanzienlijk worden beïnvloed door een `$RunningTasks` toename van het aantal taken per knooppunt. Zie Rekenknooppunten automatisch schalen in een [Azure Batch-pool voor meer informatie.](batch-automatic-scaling.md)

## <a name="specify-task-distribution"></a>Taakdistributie opgeven

Bij het inschakelen van gelijktijdige taken is het belangrijk om op te geven hoe de taken moeten worden verdeeld over de knooppunten in de pool.

Met behulp van [de eigenschap CloudPool.TaskSchedulingPolicy](/dotnet/api/microsoft.azure.batch.cloudpool.taskschedulingpolicy) kunt u opgeven dat taken gelijkmatig moeten worden toegewezen aan alle knooppunten in de pool ('verspreid'). U kunt ook opgeven dat zoveel mogelijk taken aan elk knooppunt moeten worden toegewezen voordat taken worden toegewezen aan een ander knooppunt in de pool ('verpakking').

Neem bijvoorbeeld de pool met [Standard \_ D14-knooppunten](../cloud-services/cloud-services-sizes-specs.md#d-series) (in het bovenstaande voorbeeld) die is geconfigureerd met de waarde [CloudPool.TaskSlotsPerNode](/dotnet/api/microsoft.azure.batch.cloudpool.taskslotspernode) van 16. Als [CloudPool.TaskSchedulingPolicy](/dotnet/api/microsoft.azure.batch.cloudpool.taskschedulingpolicy) is geconfigureerd met een [ComputeNodeFillType](/dotnet/api/microsoft.azure.batch.common.computenodefilltype) van *Pack,* wordt het gebruik van alle 16 kernen van elk knooppunt gemaximaliseerd en kan een pool voor automatisch schalen ongebruikte knooppunten (knooppunten zonder toegewezen taken) uit de pool verwijderen. [](batch-automatic-scaling.md) Dit minimaliseert het resourcegebruik en bespaart geld.

## <a name="define-variable-slots-per-task"></a>Variabele sleuven per taak definiëren

Een taak kan worden gedefinieerd met de eigenschap [CloudTask.RequiredSlots,](/dotnet/api/microsoft.azure.batch.cloudtask.requiredslots) waarin wordt aangegeven hoeveel sleuven er moeten worden uitgevoerd op een reken knooppunt. De standaardwaarde is 1. U kunt variabele taaksleuven instellen als uw taken verschillende gewichten hebben met betrekking tot resourcegebruik op het reken knooppunt. Hierdoor kan elk reken knooppunt een redelijk aantal gelijktijdige taken uitvoeren zonder dat de systeembronnen, zoals CPU of geheugen, worden overstelpen.

Voor een pool met eigenschap kunt u bijvoorbeeld vereiste CPU-intensieve taken met meerdere kernen verzenden, terwijl andere taken `taskSlotsPerNode = 8` kunnen worden ingesteld op `requiredSlots = 8` `requiredSlots = 1` . Wanneer deze gemengde workload is gepland, worden de CPU-intensieve taken uitsluitend uitgevoerd op hun rekenknooppunten, terwijl andere taken gelijktijdig (maximaal acht taken tegelijk) op andere knooppunten kunnen worden uitgevoerd. Dit helpt u om uw workload te verdelen over rekenknooppunten en de efficiëntie van resourcegebruik te verbeteren.

Zorg ervoor dat u niet opgeeft dat een taak groter is `requiredSlots` dan die van de `taskSlotsPerNode` pool. Hierdoor kan de taak nooit worden uitgevoerd. De Batch-service valideert dit conflict momenteel niet wanneer u taken indient, omdat een job mogelijk geen pool heeft die is gebonden tijdens het indienen, of kan worden gewijzigd in een andere pool door het uitschakelen/opnieuw inschakelen.

> [!TIP]
> Wanneer u variabele taaksleuven gebruikt, is het mogelijk dat grote taken met meer vereiste sleuven tijdelijk niet kunnen worden gepland omdat er onvoldoende sleuven beschikbaar zijn op elk rekenknooppunt, zelfs wanneer er op sommige knooppunten nog steeds niet-actieve sleuven zijn. U kunt de jobprioriteit voor deze taken verhogen om de kans te vergroten om te concurreren om beschikbare sleuven op knooppunten.
>
> De Batch-service stuurt [de TaskScheduleFailEvent](batch-task-schedule-fail-event.md) wanneer het niet lukt om een taak uit te voeren en blijft de planning opnieuw proberen totdat de vereiste sleuven beschikbaar zijn. U kunt naar die gebeurtenis luisteren om potentiële problemen met de taakplanning te detecteren en dienovereenkomstig te verhelpen.

## <a name="batch-net-example"></a>Batch .NET-voorbeeld

De volgende [Batch .NET](/dotnet/api/microsoft.azure.batch) API-codefragmenten laten zien hoe u een pool met meerdere taaksleuven per knooppunt maakt en hoe u een taak met de vereiste sleuven kunt verzenden.

### <a name="create-a-pool-with-multiple-task-slots-per-node"></a>Een pool met meerdere taaksleuven per knooppunt maken

Dit codefragment toont een aanvraag voor het maken van een pool die vier knooppunten bevat, met vier taaksleuven toegestaan per knooppunt. Er wordt een taakplanningsbeleid opgegeven dat elk knooppunt vult met taken voordat taken worden toegewezen aan een ander knooppunt in de pool.

Zie [BatchClient.PoolOperations.CreatePool](/dotnet/api/microsoft.azure.batch.pooloperations.createpool)voor meer informatie over het toevoegen van pools met behulp van de Batch .NET-API.

```csharp
CloudPool pool =
    batchClient.PoolOperations.CreatePool(
        poolId: "mypool",
        targetDedicatedComputeNodes: 4
        virtualMachineSize: "standard_d1_v2",
        VirtualMachineConfiguration: new VirtualMachineConfiguration(
            imageReference: new ImageReference(
                                publisher: "MicrosoftWindowsServer",
                                offer: "WindowsServer",
                                sku: "2019-datacenter-core",
                                version: "latest"),
            nodeAgentSkuId: "batch.node.windows amd64");

pool.TaskSlotsPerNode = 4;
pool.TaskSchedulingPolicy = new TaskSchedulingPolicy(ComputeNodeFillType.Pack);
pool.Commit();
```

### <a name="create-a-task-with-required-slots"></a>Een taak maken met de vereiste sleuven

Met dit codefragment maakt u een taak met niet-standaard `requiredSlots` . Deze taak wordt alleen uitgevoerd wanneer er voldoende vrije sleuven beschikbaar zijn op een reken knooppunt.

```csharp
CloudTask task = new CloudTask(taskId, taskCommandLine)
{
    RequiredSlots = 2
};
```

### <a name="list-compute-nodes-with-counts-for-running-tasks-and-slots"></a>Rekenknooppunten met tellingen voor het uitvoeren van taken en sleuven

In dit codefragment worden alle rekenknooppunten in de pool vermeld en worden de tellingen voor het uitvoeren van taken en taaksleuven per knooppunt afgedrukt.

```csharp
ODATADetailLevel nodeDetail = new ODATADetailLevel(selectClause: "id,runningTasksCount,runningTaskSlotsCount");
IPagedEnumerable<ComputeNode> nodes = batchClient.PoolOperations.ListComputeNodes(poolId, nodeDetail);

await nodes.ForEachAsync(node =>
{
    Console.WriteLine(node.Id + " :");
    Console.WriteLine($"RunningTasks = {node.RunningTasksCount}, RunningTaskSlots = {node.RunningTaskSlotsCount}");

}).ConfigureAwait(continueOnCapturedContext: false);
```

### <a name="list-task-counts-for-the-job"></a>Lijst met taaktellingen voor de taak

Met dit codefragment worden taaktellingen voor de job, die zowel taken als taaksleuven per taaktoestand omvatten.

```csharp
TaskCountsResult result = await batchClient.JobOperations.GetJobTaskCountsAsync(jobId);

Console.WriteLine("\t\tActive\tRunning\tCompleted");
Console.WriteLine($"TaskCounts:\t{result.TaskCounts.Active}\t{result.TaskCounts.Running}\t{result.TaskCounts.Completed}");
Console.WriteLine($"TaskSlotCounts:\t{result.TaskSlotCounts.Active}\t{result.TaskSlotCounts.Running}\t{result.TaskSlotCounts.Completed}");
```

## <a name="batch-rest-example"></a>Batch REST-voorbeeld

De volgende [Batch REST](/rest/api/batchservice/) API-codefragmenten laten zien hoe u een pool met meerdere taaksleuven per knooppunt maakt en hoe u een taak met de vereiste sleuven kunt verzenden.

### <a name="create-a-pool-with-multiple-task-slots-per-node"></a>Een pool met meerdere taaksleuven per knooppunt maken

Dit fragment toont een aanvraag voor het maken van een pool die twee grote knooppunten bevat met maximaal vier taken per knooppunt.

Zie Een pool toevoegen aan een account voor meer informatie REST API het toevoegen van [pools met behulp van de REST API.](/rest/api/batchservice/pool/add)

```json
{
  "odata.metadata":"https://myaccount.myregion.batch.azure.com/$metadata#pools/@Element",
  "id":"mypool",
  "vmSize":"large",
  "virtualMachineConfiguration": {
    "imageReference": {
      "publisher": "canonical",
      "offer": "ubuntuserver",
      "sku": "18.04-lts"
    },
    "nodeAgentSKUId": "batch.node.ubuntu 16.04"
  },
  "targetDedicatedComputeNodes":2,
  "taskSlotsPerNode":4,
  "enableInterNodeCommunication":true,
}
```

### <a name="create-a-task-with-required-slots"></a>Een taak maken met de vereiste sleuven

Dit fragment toont een aanvraag voor het toevoegen van een taak met niet-standaard `requiredSlots` . Deze taak wordt alleen uitgevoerd wanneer er voldoende vrije sleuven beschikbaar zijn op het reken-knooppunt.

```json
{
  "id": "taskId",
  "commandLine": "bash -c 'echo hello'",
  "userIdentity": {
    "autoUser": {
      "scope": "task",
      "elevationLevel": "nonadmin"
    }
  },
  "requiredSLots": 2
}
```

## <a name="code-sample-on-github"></a>Codevoorbeeld op GitHub

Het [project ParallelTasks](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ParallelTasks) op GitHub illustreert het gebruik van de eigenschap [CloudPool.TaskSlotsPerNode.](/dotnet/api/microsoft.azure.batch.cloudpool.taskslotspernode)

Deze C#-consoletoepassing maakt gebruik van de [Batch .NET-bibliotheek](/dotnet/api/microsoft.azure.batch) om een pool met een of meer rekenknooppunten te maken. Er wordt een configureerbaar aantal taken op deze knooppunten uitgevoerd om een variabele belasting te simuleren. Uitvoer van de toepassing toont welke knooppunten elke taak hebben uitgevoerd. De toepassing biedt ook een overzicht van de taakparameters en de duur.

Hieronder vindt u bijvoorbeeld het overzichtsgedeelte van de uitvoer van twee verschillende uitvoeringen van de voorbeeldtoepassing ParallelTasks. De duur van de taak die hier wordt weergegeven, omvat geen tijd voor het  maken van een pool, omdat elke taak is verzonden naar een eerder gemaakte pool waarvan de rekenknooppunten tijdens het indienen de status Niet-actief hadden.

Bij de eerste uitvoering van de voorbeeldtoepassing ziet u dat met één knooppunt in de pool en de standaardinstelling van één taak per knooppunt, de duur van de taak meer dan 30 minuten is.

```
Nodes: 1
Node size: large
Task slots per node: 1
Max slots per task: 1
Tasks: 32
Duration: 00:30:01.4638023
```

De tweede run van het voorbeeld toont een aanzienlijke afname in de duur van de taak. Dit komt doordat de pool is geconfigureerd met vier taken per knooppunt, waardoor parallelle taakuitvoering de job in bijna een kwart van de tijd kan voltooien.

```
Nodes: 1
Node size: large
Task slots per node: 4
Max slots per task: 1
Tasks: 32
Duration: 00:08:48.2423500
```

## <a name="next-steps"></a>Volgende stappen

- Probeer de [Batch Explorer](https://azure.github.io/BatchExplorer/) HeatMap. Batch Explorer is een gratis, uitgebreid, zelfstandig clienthulpprogramma voor het maken, opsporen en controleren van Azure Batch toepassingen. Wanneer u de voorbeeldtoepassing [ParallelTasks](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ParallelTasks) uitvoert, kunt u met de functie Batch Explorer HeatMap eenvoudig de uitvoering van parallelle taken op elk knooppunt visualiseren.
- Bekijk [Azure Batch voorbeelden op GitHub.](https://github.com/Azure/azure-batch-samples)
- Meer informatie over [Batch-taakafhankelijkheden.](batch-task-dependencies.md)
