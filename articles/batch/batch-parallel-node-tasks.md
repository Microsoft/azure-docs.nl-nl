---
title: Gelijktijdig taken uitvoeren om het gebruik van batch Compute-knoop punten te maximaliseren
description: Verhoog de efficiëntie en verlaag de kosten met minder reken knooppunten en actieve taken parallel op elk knoop punt in een Azure Batch pool
ms.topic: how-to
ms.date: 10/08/2020
ms.custom: H1Hack27Feb2017, devx-track-csharp
ms.openlocfilehash: 8bc9f03f05d52df6e400be5c57033ab2a38fa8eb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92102962"
---
# <a name="run-tasks-concurrently-to-maximize-usage-of-batch-compute-nodes"></a>Gelijktijdig taken uitvoeren om het gebruik van batch Compute-knoop punten te maximaliseren

U kunt het resource gebruik maximaliseren op een kleiner aantal reken knooppunten in uw pool door meer dan één taak tegelijk op elk knoop punt uit te voeren.

Hoewel sommige scenario's het beste werken met alle resources van een knoop punt die zijn toegewezen aan één taak, kunnen bepaalde werk belastingen kortere taak tijden zien en kosten verlagen wanneer meerdere taken deze resources delen:

- **Minimaliseer de gegevens overdracht** voor taken die gegevens kunnen delen. U kunt de kosten voor gegevens overdracht aanzienlijk verminderen door gedeelde gegevens naar een kleiner aantal knoop punten te kopiëren en vervolgens taken parallel uit te voeren op elk knoop punt. Dit is met name van toepassing als de gegevens die naar elk knoop punt moeten worden gekopieerd, moeten worden overgedragen tussen geografische regio's.
- **Maximaliseer het geheugen gebruik** voor taken waarvoor een grote hoeveelheid geheugen nodig is, maar alleen tijdens korte Peri Oden, en op variabele tijdstippen tijdens de uitvoering. U kunt minder, maar grotere, reken knooppunten met meer geheugen gebruiken om dergelijke pieken efficiënt af te handelen. Op deze knoop punten worden meerdere taken parallel uitgevoerd op elk knoop punt, maar elke taak zou gebruikmaken van het geheugen van de knoop punten op verschillende tijdstippen.
- **Beperken van knooppunt aantal limieten** wanneer communicatie tussen knoop punten is vereist binnen een groep. Op dit moment zijn groepen die zijn geconfigureerd voor communicatie tussen knoop punten beperkt tot 50 Compute-knoop punten. Als elk knoop punt in een dergelijke groep parallelle taken kan uitvoeren, kan een groter aantal taken tegelijkertijd worden uitgevoerd.
- **Repliceer een on-premises Compute-Cluster**, bijvoorbeeld wanneer u een compute-omgeving naar Azure verplaatst. Als uw huidige on-premises oplossing meerdere taken per Compute-knoop punt uitvoert, kunt u het maximum aantal knooppunt taken verhogen om deze configuratie beter te spie gelen.

## <a name="example-scenario"></a>Voorbeeldscenario

Voor beeld: Stel dat een taak toepassing met CPU-en geheugen vereisten, zoals [standaard \_ D1](../cloud-services/cloud-services-sizes-specs.md) -knoop punten, voldoende is. Als u de taak echter in de vereiste tijd wilt volt ooien, zijn er 1.000 van deze knoop punten nodig.

In plaats van gebruik te maken van standaard \_ D1-knoop punten met 1 CPU-kern, kunt u [standaard \_ D14](../cloud-services/cloud-services-sizes-specs.md) -knoop punten gebruiken met elke 16 kernen en de uitvoering van parallelle taken inschakelen. Dit betekent dat er *16 keer minder knoop punten* kunnen worden gebruikt:--in plaats van 1.000-knoop punten. er is alleen 63 vereist. Als er voor elk knoop punt grote toepassings bestanden of referentie gegevens zijn vereist, zijn de duur en efficiëntie van de taak opnieuw verbeterd, omdat de gegevens slechts naar 63 knoop punten worden gekopieerd.

## <a name="enable-parallel-task-execution"></a>Uitvoering van parallelle taak inschakelen

U kunt reken knooppunten voor het uitvoeren van parallelle taken configureren op het groeps niveau. Stel in de batch .NET-bibliotheek de eigenschap [CloudPool. TaskSlotsPerNode](/dotnet/api/microsoft.azure.batch.cloudpool) in wanneer u een pool maakt. Als u de batch-REST API gebruikt, stelt u het element [taskSlotsPerNode](/rest/api/batchservice/pool/add) in de hoofd tekst van de aanvraag in tijdens het maken van de groep.

> [!NOTE]
> U kunt de `taskSlotsPerNode` eigenschap element en [TaskSlotsPerNode](/dotnet/api/microsoft.azure.batch.cloudpool) alleen instellen tijdens het maken van de groep. Ze kunnen niet worden gewijzigd nadat er al een pool is gemaakt.

Met Azure Batch kunt u taak sleuven per knoop punt instellen op Maxi maal (4x) het aantal knooppunt kernen. Als de pool bijvoorbeeld is geconfigureerd met knoop punten met de grootte ' groot ' (vier kernen), `taskSlotsPerNode` kan deze worden ingesteld op 16. Ongeacht het aantal kernen dat het knoop punt heeft, kunt u Maxi maal 256 taak sleuven per knoop punt hebben. Zie [grootten voor Cloud Services](../cloud-services/cloud-services-sizes-specs.md)voor meer informatie over het aantal kernen voor elk van de groottes van knoop punten. Zie [quota's en limieten voor de Azure batch-service](batch-quota-limit.md)voor meer informatie over service limieten.

> [!TIP]
> Zorg ervoor dat u rekening moet houden met de `taskSlotsPerNode` waarde wanneer u een [formule voor automatisch schalen](/rest/api/batchservice/pool/enableautoscale) voor uw groep bouwt. Een formule die evalueert, kan bijvoorbeeld `$RunningTasks` aanzienlijk worden beïnvloed door een toename van taken per knoop punt. Zie [reken knooppunten automatisch schalen in een Azure batch pool](batch-automatic-scaling.md)voor meer informatie.

## <a name="specify-task-distribution"></a>Taak distributie opgeven

Wanneer u gelijktijdige taken inschakelt, is het belang rijk om op te geven hoe u wilt dat de taken worden gedistribueerd over de knoop punten in de pool.

Met de eigenschap [CloudPool. TaskSchedulingPolicy](/dotnet/api/microsoft.azure.batch.cloudpool) kunt u opgeven dat taken gelijkmatig moeten worden toegewezen op alle knoop punten in de pool (' sprei ding '). U kunt ook opgeven dat er zoveel mogelijk taken moeten worden toegewezen aan elk knoop punt voordat taken worden toegewezen aan een ander knoop punt in de pool (' verpakking ').

Denk bijvoorbeeld aan de pool van [standaard \_ D14](../cloud-services/cloud-services-sizes-specs.md) -knoop punten (in het bovenstaande voor beeld) die is geconfigureerd met een [CloudPool. TaskSlotsPerNode](/dotnet/api/microsoft.azure.batch.cloudpool) -waarde van 16. Als [CloudPool. TaskSchedulingPolicy](/dotnet/api/microsoft.azure.batch.cloudpool) is geconfigureerd met een [ComputeNodeFillType](/dotnet/api/microsoft.azure.batch.common.computenodefilltype) *Pack*, wordt het gebruik van alle 16 kernen van elk knoop punt gemaximaliseerd en kan een groep voor automatisch [schalen](batch-automatic-scaling.md) ongebruikte knoop punten (knoop punten zonder toegewezen taken) verwijderen uit de groep. Hiermee wordt het resource gebruik geminimaliseerd en bespaart u geld.

## <a name="define-variable-slots-per-task"></a>Variabele sleuven per taak definiëren

Een taak kan worden gedefinieerd met de eigenschap [CloudTask. RequiredSlots](/dotnet/api/microsoft.azure.batch.cloudtask.requiredslots) , waarbij u kunt opgeven hoeveel sleuven er nodig zijn om uit te voeren op een reken knooppunt. De standaard waarde is 1. U kunt variabele taak sleuven instellen als uw taken verschillende weging hebben over het resource gebruik op het reken knooppunt. Hierdoor kan elk reken knooppunt een redelijk aantal gelijktijdige actieve taken hebben zonder veel systeem bronnen, zoals CPU of geheugen.

Voor een pool met een eigenschap `taskSlotsPerNode = 8` kunt u bijvoorbeeld multi-core vereist CPU-intensieve taken verzenden met `requiredSlots = 8` , terwijl andere taken kunnen worden ingesteld op `requiredSlots = 1` . Wanneer deze gemengde werk belasting is gepland, worden de CPU-intensieve taken uitsluitend uitgevoerd op hun reken knooppunten, terwijl andere taken gelijktijdig kunnen worden uitgevoerd (Maxi maal acht taken tegelijk) op andere knoop punten. Zo kunt u uw werk belasting verdelen over reken knooppunten en de efficiëntie van het resource gebruik verbeteren.

> [!TIP]
> Wanneer u variabele taak sleuven gebruikt, is het mogelijk dat grote taken met meer vereiste sleuven tijdelijk niet kunnen worden gepland omdat er onvoldoende sleuven beschikbaar zijn op een reken knooppunt, zelfs wanneer er nog steeds niet-actieve sleuven op sommige knoop punten zijn. U kunt de taak prioriteit voor deze taken verhogen om de kans te verg Roten om te concurreren voor beschik bare sleuven op knoop punten.
>
> De batch-service verzendt de [TaskScheduleFailEvent](batch-task-schedule-fail-event.md) wanneer het niet lukt om de uitvoering van een taak te plannen en blijft de planning opnieuw proberen totdat de vereiste sleuven beschikbaar zijn. U kunt naar die gebeurtenis Luis teren om mogelijke problemen met de planning van taken en de oplossing dienovereenkomstig te detecteren.

> [!NOTE]
> Geef geen taak `requiredSlots` op die groter is dan de pool `taskSlotsPerNode` . Dit heeft tot gevolg dat de taak nooit kan worden uitgevoerd. Dit conflict wordt momenteel niet door de batch-service gevalideerd wanneer u taken indient, omdat een taak mogelijk geen pool heeft die is gebonden aan de indienings tijd, of kan worden gewijzigd in een andere groep door deze uit te scha kelen of opnieuw in te scha kelen.

## <a name="batch-net-example"></a>Batch .NET-voor beeld

In de volgende batch code fragmenten van [.net](/dotnet/api/microsoft.azure.batch) API ziet u hoe u een groep met meerdere taak sleuven per knoop punt maakt en hoe u een taak met de vereiste sleuven verzendt.

### <a name="create-a-pool-with-multiple-task-slots-per-node"></a>Een groep maken met meerdere taak sleuven per knoop punt

Dit code fragment toont een aanvraag voor het maken van een groep die vier knoop punten bevat, waarbij vier taak sleuven per knoop punt zijn toegestaan. Hiermee wordt een beleid voor taak planning opgegeven waarmee elk knoop punt met taken wordt gevuld voordat taken worden toegewezen aan een ander knoop punt in de pool.

Zie [BatchClient. pool Operations. CreatePool](/dotnet/api/microsoft.azure.batch.pooloperations)voor meer informatie over het toevoegen van groepen met behulp van de batch .net-API.

```csharp
CloudPool pool =
    batchClient.PoolOperations.CreatePool(
        poolId: "mypool",
        targetDedicatedComputeNodes: 4
        virtualMachineSize: "standard_d1_v2",
        cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5"));

pool.TaskSlotsPerNode = 4;
pool.TaskSchedulingPolicy = new TaskSchedulingPolicy(ComputeNodeFillType.Pack);
pool.Commit();
```

### <a name="create-a-task-with-required-slots"></a>Een taak met vereiste sleuven maken

Met dit code fragment wordt een taak gemaakt met niet-standaard `requiredSlots` . Deze taak wordt alleen uitgevoerd als er voldoende vrije sleuven beschikbaar zijn op een reken knooppunt.

```csharp
CloudTask task = new CloudTask(taskId, taskCommandLine)
{
    RequiredSlots = 2
};
```

### <a name="list-compute-nodes-with-counts-for-running-tasks-and-slots"></a>Reken knooppunten weer geven met aantallen voor het uitvoeren van taken en sleuven

In dit code fragment worden alle reken knooppunten in de groep weer gegeven en worden de aantallen voor het uitvoeren van taken en taak sleuven per knoop punt afgedrukt.

```csharp
ODATADetailLevel nodeDetail = new ODATADetailLevel(selectClause: "id,runningTasksCount,runningTaskSlotsCount");
IPagedEnumerable<ComputeNode> nodes = batchClient.PoolOperations.ListComputeNodes(poolId, nodeDetail);

await nodes.ForEachAsync(node =>
{
    Console.WriteLine(node.Id + " :");
    Console.WriteLine($"RunningTasks = {node.RunningTasksCount}, RunningTaskSlots = {node.RunningTaskSlotsCount}");

}).ConfigureAwait(continueOnCapturedContext: false);
```

### <a name="list-task-counts-for-the-job"></a>Aantal taken voor de taak weer geven

Met dit code fragment worden taak aantallen voor de taak opgehaald, waaronder zowel taken als taak sleuven tellen per taak status.

```csharp
TaskCountsResult result = await batchClient.JobOperations.GetJobTaskCountsAsync(jobId);

Console.WriteLine("\t\tActive\tRunning\tCompleted");
Console.WriteLine($"TaskCounts:\t{result.TaskCounts.Active}\t{result.TaskCounts.Running}\t{result.TaskCounts.Completed}");
Console.WriteLine($"TaskSlotCounts:\t{result.TaskSlotCounts.Active}\t{result.TaskSlotCounts.Running}\t{result.TaskSlotCounts.Completed}");
```

## <a name="batch-rest-example"></a>Batch REST-voor beeld

In de volgende batch-code fragmenten voor [rest](/rest/api/batchservice/) API wordt getoond hoe u een groep met meerdere taak sleuven per knoop punt maakt en hoe u een taak met de vereiste sleuven verzendt.

### <a name="create-a-pool-with-multiple-task-slots-per-node"></a>Een groep maken met meerdere taak sleuven per knoop punt

Dit fragment bevat een aanvraag voor het maken van een groep die twee grote knoop punten bevat met een maximum van vier taken per knoop punt.

Zie [een pool toevoegen aan een account](/rest/api/batchservice/pool/add)voor meer informatie over het toevoegen van groepen met behulp van de rest API.

```json
{
  "odata.metadata":"https://myaccount.myregion.batch.azure.com/$metadata#pools/@Element",
  "id":"mypool",
  "vmSize":"large",
  "cloudServiceConfiguration": {
    "osFamily":"4",
    "targetOSVersion":"*",
  },
  "targetDedicatedComputeNodes":2,
  "taskSlotsPerNode":4,
  "enableInterNodeCommunication":true,
}
```

### <a name="create-a-task-with-required-slots"></a>Een taak met vereiste sleuven maken

Dit code fragment bevat een aanvraag om een taak toe te voegen die niet standaard is `requiredSlots` . Deze taak wordt alleen uitgevoerd als er voldoende vrije sleuven beschikbaar zijn op het reken knooppunt.

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

## <a name="code-sample-on-github"></a>Code voorbeeld op GitHub

Het [ParallelTasks](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ParallelTasks) -project op github illustreert het gebruik van de eigenschap [CloudPool. TaskSlotsPerNode](/dotnet/api/microsoft.azure.batch.cloudpool) .

Deze C#-console toepassing maakt gebruik van de [batch .net](/dotnet/api/microsoft.azure.batch) -bibliotheek om een groep met een of meer reken knooppunten te maken. Er wordt een configureerbaar aantal taken op deze knoop punten uitgevoerd om een variabele belasting te simuleren. Bij uitvoer van de toepassing ziet u op welke knoop punten elke taak wordt uitgevoerd. De toepassing biedt ook een samen vatting van de taak parameters en-duur.

Hieronder ziet u een voor beeld van het gedeelte samen vatting van de uitvoer van twee verschillende uitvoeringen van de voorbeeld toepassing ParallelTasks. De duur van de taak die hier wordt weer gegeven, bevat geen aanmaak tijd voor het maken van een groep, omdat elke taak is verzonden naar een eerder gemaakte groep waarvan de reken knooppunten tijdens het indienen de status *niet-actief* hadden.

De eerste uitvoering van de voorbeeld toepassing laat zien dat met één knoop punt in de pool en de standaard instelling van één taak per knoop punt, de taak duur meer dan 30 minuten is.

```
Nodes: 1
Node size: large
Task slots per node: 1
Max slots per task: 1
Tasks: 32
Duration: 00:30:01.4638023
```

Bij de tweede uitvoering van het voor beeld ziet u een aanzienlijke afname van de taak duur. Dit komt doordat de groep is geconfigureerd met vier taken per knoop punt, waardoor parallelle taken kunnen worden uitgevoerd om de taak in bijna een kwart van de tijd te volt ooien.

```
Nodes: 1
Node size: large
Task slots per node: 4
Max slots per task: 1
Tasks: 32
Duration: 00:08:48.2423500
```

## <a name="next-steps"></a>Volgende stappen

- Probeer de [batch Explorer](https://azure.github.io/BatchExplorer/) heatmap. Batch Explorer is een gratis, uitgebreid, zelfstandig client hulpprogramma waarmee Azure Batch toepassingen kunnen worden gemaakt, opgespoord en gecontroleerd. Wanneer u de voorbeeld toepassing [ParallelTasks](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ParallelTasks) uitvoert, kunt u met de functie voor het batch Explorer van Heatmap eenvoudig de uitvoering van parallelle taken op elk knoop punt visualiseren.
- Verken [Azure batch-voor beelden op github](https://github.com/Azure/azure-batch-samples).
- Meer informatie over [afhankelijkheden van batch-taken](batch-task-dependencies.md).
