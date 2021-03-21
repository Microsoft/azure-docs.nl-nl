---
title: Taak afhankelijkheden maken om taken uit te voeren
description: Maak taken die afhankelijk zijn van de voltooiing van andere taken voor het verwerken van MapReduce stijl en soort gelijke big data werk belastingen in Azure Batch.
ms.topic: how-to
ms.date: 12/28/2020
ms.custom: H1Hack27Feb2017, devx-track-csharp
ms.openlocfilehash: ef05a98fffc3c0684ad0fa29f2f9f039b388f5ad
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97803932"
---
# <a name="create-task-dependencies-to-run-tasks-that-depend-on-other-tasks"></a>Taak afhankelijkheden maken om taken uit te voeren die afhankelijk zijn van andere taken

Met batch taak afhankelijkheden maakt u taken die zijn gepland voor uitvoering op Compute-knoop punten na het volt ooien van een of meer bovenliggende taken. U kunt bijvoorbeeld een taak maken waarmee elk frame van een 3D-film met afzonderlijke, parallelle taken wordt weer gegeven. De laatste taak--de taak samen voegen: Hiermee worden de gerenderde frames pas samengevoegd in de volledige film nadat alle frames zijn gerenderd.

Enkele scenario's waarbij taak afhankelijkheden handig zijn, zijn onder andere:

- MapReduce-werk belastingen in de Cloud.
- Taken waarvan de taken voor gegevens verwerking kunnen worden uitgedrukt als een Directed Acyclic Graph (DAG).
- Processen vóór rendering en na het renderen, waarbij elke taak moet worden voltooid voordat de volgende taak kan worden gestart.
- Een andere taak waarin downstream-taken afhankelijk zijn van de uitvoer van upstream-taken.

Standaard worden afhankelijke taken alleen gepland voor uitvoering nadat de bovenliggende taak is voltooid. U kunt eventueel een [afhankelijkheids actie](#dependency-actions)  opgeven om het standaard gedrag te overschrijven en taken uit te voeren wanneer de bovenliggende taak mislukt.

## <a name="task-dependencies-with-batch-net"></a>Taak afhankelijkheden met batch .NET

In dit artikel wordt beschreven hoe taak afhankelijkheden worden geconfigureerd met behulp van de [batch .net](/dotnet/api/microsoft.azure.batch) -bibliotheek. We laten u eerst zien hoe u de [taak afhankelijkheid kunt inschakelen](#enable-task-dependencies) voor uw taken en hoe u [een taak configureert met afhankelijkheden](#create-dependent-tasks). Daarnaast wordt beschreven hoe u een afhankelijkheids actie opgeeft voor het uitvoeren van afhankelijke taken als het bovenliggende item mislukt. Ten slotte bespreken we de [afhankelijkheids scenario's](#dependency-scenarios) die door batch worden ondersteund.

## <a name="enable-task-dependencies"></a>Taak afhankelijkheden inschakelen

Als u taak afhankelijkheden in uw batch-toepassing wilt gebruiken, moet u eerst de taak configureren voor het gebruik van taak afhankelijkheden. Schakel in batch .NET het in op uw [eigenschap cloudjob](/dotnet/api/microsoft.azure.batch.cloudjob) door de eigenschap [UsesTaskDependencies](/dotnet/api/microsoft.azure.batch.cloudjob.usestaskdependencies) in te stellen op `true` :

```csharp
CloudJob unboundJob = batchClient.JobOperations.CreateJob( "job001",
    new PoolInformation { PoolId = "pool001" });

// IMPORTANT: This is REQUIRED for using task dependencies.
unboundJob.UsesTaskDependencies = true;
```

In het voor gaande code fragment is ' batchClient ' een instantie van de klasse [batchClient](/dotnet/api/microsoft.azure.batch.batchclient) .

## <a name="create-dependent-tasks"></a>Afhankelijke taken maken

Als u een taak wilt maken die afhankelijk is van het volt ooien van een of meer bovenliggende taken, kunt u opgeven dat de taak afhankelijk is van de andere taken. Configureer in batch .NET de eigenschap [CloudTask. DependsOn](/dotnet/api/microsoft.azure.batch.cloudtask.dependson) met een exemplaar van de klasse [TaskDependencies](/dotnet/api/microsoft.azure.batch.taskdependencies) :

```csharp
// Task 'Flowers' depends on completion of both 'Rain' and 'Sun'
// before it is run.
new CloudTask("Flowers", "cmd.exe /c echo Flowers")
{
    DependsOn = TaskDependencies.OnIds("Rain", "Sun")
},
```

Met dit code fragment maakt u een afhankelijke taak met taak-ID "bloemen". De taak "bloemen" is afhankelijk van de taken "regen" en "Sun". Taak "bloemen" wordt alleen gepland om te worden uitgevoerd op een reken knooppunt nadat taken "regen" en "Sun" zijn voltooid.

> [!NOTE]
> Standaard wordt een taak als voltooid beschouwd als deze de status voltooid heeft en de afsluit code ervan `0` . In batch .NET betekent dit dat de waarde van de eigenschap [CloudTask. State](/dotnet/api/microsoft.azure.batch.cloudtask.state) is `Completed` en de waarde van de eigenschap [TaskExecutionInformation. exitCode](/dotnet/api/microsoft.azure.batch.taskexecutioninformation.exitcode) van CloudTask is `0` . Zie de sectie [afhankelijkheids acties](#dependency-actions) voor meer informatie over hoe u dit kunt wijzigen.

## <a name="dependency-scenarios"></a>Afhankelijkheids scenario's

Er zijn drie elementaire scenario's voor taak afhankelijkheden die u in Azure Batch kunt gebruiken: een-op-een-, een-op-veel-en taak-ID-bereik afhankelijkheid. Deze drie scenario's kunnen worden gecombineerd om een vierde scenario te bieden: veel-op-veel.

| Omstandigheden&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | Voorbeeld | Zien |
|:---:| --- | --- |
|  [Een-op-een](#one-to-one) |*taakb kan pas* is afhankelijk van *taaka* <p/> *taakb kan pas* wordt niet gepland voor uitvoering totdat *taaka* is voltooid |:::image type="content" source="media/batch-task-dependency/01_one_to_one.png" alt-text="Diagram waarin het scenario van een-op-een-taak afhankelijkheid wordt weer gegeven."::: |
|  [Een-op-veel](#one-to-many) |*taakc* is afhankelijk van zowel *Taaka* als *taakb kan pas* <p/> *taakc* wordt pas ingepland als de uitvoering van *Takena* en *taakb kan pas* is voltooid |:::image type="content" source="media/batch-task-dependency/02_one_to_many.png" alt-text="Diagram waarin het scenario van een-op-veel-taak afhankelijkheid wordt weer gegeven."::: |
|  [Bereik taak-ID](#task-id-range) |*taken* zijn afhankelijk van een reeks taken <p/> de *taak* wordt niet gepland voor uitvoering totdat de taken met de id *1* t/m *10* zijn voltooid |:::image type="content" source="media/batch-task-dependency/03_task_id_range.png" alt-text="Diagram waarin het taak-ID-scenario voor het afhankelijkheden van taken wordt weer gegeven."::: |

> [!TIP]
> U kunt **veel-op-veel** -relaties maken, zoals de taken C, D, E en F elk zijn afhankelijk van de taken A en B. Dit is bijvoorbeeld handig in scenario's met een geparalleld preproces waarbij uw downstream-taken afhankelijk zijn van de uitvoer van meerdere upstream-taken.
> 
> In de voor beelden in deze sectie wordt een afhankelijke taak alleen uitgevoerd nadat de bovenliggende taken zijn voltooid. Dit gedrag is het standaard gedrag voor een afhankelijke taak. U kunt een afhankelijke taak uitvoeren nadat een bovenliggende taak is mislukt door een [afhankelijkheids actie](#dependency-actions)  op te geven om het standaard gedrag te negeren.

### <a name="one-to-one"></a>Een-op-een

Bij een een-op-een-relatie is een taak afhankelijk van de geslaagde voltooiing van één bovenliggende taak. Als u de afhankelijkheid wilt maken, moet u één taak-ID opgeven voor de methode [TaskDependencies. OnId](/dotnet/api/microsoft.azure.batch.taskdependencies.onid) static wanneer u de eigenschap [CloudTask. DependsOn](/dotnet/api/microsoft.azure.batch.cloudtask.dependson) invult.

```csharp
// Task 'taskA' doesn't depend on any other tasks
new CloudTask("taskA", "cmd.exe /c echo taskA"),

// Task 'taskB' depends on completion of task 'taskA'
new CloudTask("taskB", "cmd.exe /c echo taskB")
{
    DependsOn = TaskDependencies.OnId("taskA")
},
```

### <a name="one-to-many"></a>Een-op-veel

Bij een een-op-veel-relatie is een taak afhankelijk van het volt ooien van meerdere bovenliggende taken. Als u de afhankelijkheid wilt maken, geeft u een verzameling taak-Id's op voor de methode [TaskDependencies. OnIds](/dotnet/api/microsoft.azure.batch.taskdependencies.onids) wanneer u de eigenschap [CloudTask. DependsOn](/dotnet/api/microsoft.azure.batch.cloudtask.dependson) invult.

```csharp
// 'Rain' and 'Sun' don't depend on any other tasks
new CloudTask("Rain", "cmd.exe /c echo Rain"),
new CloudTask("Sun", "cmd.exe /c echo Sun"),

// Task 'Flowers' depends on completion of both 'Rain' and 'Sun'
// before it is run.
new CloudTask("Flowers", "cmd.exe /c echo Flowers")
{
    DependsOn = TaskDependencies.OnIds("Rain", "Sun")
},
```

### <a name="task-id-range"></a>Bereik taak-ID

In een afhankelijkheid van een reeks bovenliggende taken is een taak afhankelijk van de voltooiing van taken waarvan de Id's binnen een bereik liggen.
Als u de afhankelijkheid wilt maken, geeft u de eerste en laatste taak-Id's in het bereik op wanneer [u de eigenschap](/dotnet/api/microsoft.azure.batch.taskdependencies.onidrange) [CloudTask. DependsOn](/dotnet/api/microsoft.azure.batch.cloudtask.dependson) invult.

> [!IMPORTANT]
> Wanneer u taak-ID-bereiken voor uw afhankelijkheden gebruikt, worden alleen de taken met Id's die gehele waarden vertegenwoordigen, geselecteerd door het bereik. Zo worden met het bereik `1..10` taken `3` en `7` , maar niet geselecteerd `5flamingoes` .
>
> Voorloop nullen zijn niet significant bij het evalueren van de bereik afhankelijkheden, dus taken met teken reeks `4` -id's, `04` en `004` zijn allemaal *binnen* het bereik en ze worden allemaal beschouwd als taak `4` , zodat de eerste die moet worden voltooid, aan de afhankelijkheid wordt voldaan.
>
> Elke taak in het bereik moet voldoen aan de afhankelijkheid, hetzij door het volt ooien of door te volt ooien met een fout die is toegewezen aan een [afhankelijkheids actie](#dependency-actions) ingesteld op **voldaan**.

```csharp
// Tasks 1, 2, and 3 don't depend on any other tasks. Because
// we will be using them for a task range dependency, we must
// specify string representations of integers as their ids.
new CloudTask("1", "cmd.exe /c echo 1"),
new CloudTask("2", "cmd.exe /c echo 2"),
new CloudTask("3", "cmd.exe /c echo 3"),

// Task 4 depends on a range of tasks, 1 through 3
new CloudTask("4", "cmd.exe /c echo 4")
{
    // To use a range of tasks, their ids must be integer values.
    // Note that we pass integers as parameters to TaskIdRange,
    // but their ids (above) are string representations of the ids.
    DependsOn = TaskDependencies.OnIdRange(1, 3)
},
```

## <a name="dependency-actions"></a>Afhankelijkheids acties

Een afhankelijke taak of set taken wordt standaard pas uitgevoerd nadat een bovenliggende taak is voltooid. In sommige gevallen wilt u wellicht afhankelijke taken uitvoeren, zelfs als de bovenliggende taak mislukt. U kunt het standaard gedrag negeren door een afhankelijkheids actie op te geven.

Een afhankelijkheids actie geeft aan of een afhankelijke taak in aanmerking komt voor uitvoering, op basis van het slagen of mislukken van de bovenliggende taak. Stel bijvoorbeeld dat een afhankelijke taak wacht op gegevens van het volt ooien van de upstream-taak. Als de upstream-taak mislukt, kan de afhankelijke taak mogelijk nog steeds worden uitgevoerd met oudere gegevens. In dit geval kan een afhankelijkheids actie opgeven dat de afhankelijke taak kan worden uitgevoerd ondanks het mislukken van de bovenliggende taak.

Een afhankelijkheids actie is gebaseerd op een afsluit voorwaarde voor de bovenliggende taak. U kunt een afhankelijkheids actie voor een van de volgende afsluit voorwaarden opgeven:

- Als er een fout optreedt die voorafgaat aan de verwerking.
- Wanneer er een fout optreedt bij het uploaden van het bestand. Als de taak wordt afgesloten met een afsluit code die is opgegeven via **exitCodes** of **exitCodeRanges**, en vervolgens een fout bij het uploaden van het bestand ondervindt, heeft de actie die is opgegeven door de afsluit code prioriteit.
- Wanneer de taak wordt afgesloten met een afsluit code die is gedefinieerd door de eigenschap **ExitCodes** .
- Wanneer de taak wordt afgesloten met een afsluit code die binnen een bereik valt dat is opgegeven met de eigenschap **ExitCodeRanges** .
- Als de taak wordt afgesloten met een afsluit code die niet is gedefinieerd door **ExitCodes** of **ExitCodeRanges**, of als de taak wordt afgesloten met een pre-verwerkings fout en de eigenschap **PreProcessingError** niet is ingesteld, of als de taak mislukt met een fout bij het uploaden van het bestand en de eigenschap **FileUploadError** niet is ingesteld. 

Voor .NET raadpleegt u de klasse [ExitConditions](/dotnet/api/microsoft.azure.batch.exitconditions) voor meer informatie over deze voor waarden.

Als u een afhankelijkheids actie in .NET wilt opgeven, stelt u de eigenschap [ExitOptions. DependencyAction](/dotnet/api/microsoft.azure.batch.exitoptions.dependencyaction) voor de voor waarde voor afsluiten in op een van de volgende:

- **Voldoen** aan: geeft aan dat afhankelijke taken in aanmerking komen voor uitvoering als de bovenliggende taak wordt afgesloten met een opgegeven fout.
- **Blok**: geeft aan dat afhankelijke taken niet in aanmerking komen voor uitvoering.

De standaard instelling voor de eigenschap **DependencyAction** is te **voldoen aan** de afsluit code 0 en **blok keren** voor alle andere afsluit voorwaarden.

Met het volgende code fragment wordt de eigenschap **DependencyAction** ingesteld voor een bovenliggende taak. Als de bovenliggende taak wordt afgesloten met een pre-verwerkings fout of met de opgegeven fout codes, wordt de afhankelijke taak geblokkeerd. Als de bovenliggende taak wordt afgesloten met een andere fout die niet gelijk is aan nul, komt de afhankelijke taak in aanmerking voor uitvoering.

```csharp
// Task A is the parent task.
new CloudTask("A", "cmd.exe /c echo A")
{
    // Specify exit conditions for task A and their dependency actions.
    ExitConditions = new ExitConditions
    {
        // If task A exits with a pre-processing error, block any downstream tasks (in this example, task B).
        PreProcessingError = new ExitOptions
        {
            DependencyAction = DependencyAction.Block
        },
        // If task A exits with the specified error codes, block any downstream tasks (in this example, task B).
        ExitCodes = new List<ExitCodeMapping>
        {
            new ExitCodeMapping(10, new ExitOptions() { DependencyAction = DependencyAction.Block }),
            new ExitCodeMapping(20, new ExitOptions() { DependencyAction = DependencyAction.Block })
        },
        // If task A succeeds or fails with any other error, any downstream tasks become eligible to run 
        // (in this example, task B).
        Default = new ExitOptions
        {
            DependencyAction = DependencyAction.Satisfy
        }
    }
},
// Task B depends on task A. Whether it becomes eligible to run depends on how task A exits.
new CloudTask("B", "cmd.exe /c echo B")
{
    DependsOn = TaskDependencies.OnId("A")
},
```

## <a name="code-sample"></a>Codevoorbeeld

Het voor beeld van het [TaskDependencies](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/TaskDependencies) -voorbeeld project op github demonstreert:

- Taak afhankelijkheid inschakelen voor een taak.
- Taken maken die afhankelijk zijn van andere taken.
- Taken uitvoeren op een pool van reken knooppunten.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de functie voor [toepassings pakketten](batch-application-packages.md) van batch, waarmee u een eenvoudige manier hebt om de toepassingen te implementeren en te installeren die uw taken uitvoeren op reken knooppunten.
- Meer informatie over [fout controle voor taken en taken](batch-job-task-error-checking.md).
