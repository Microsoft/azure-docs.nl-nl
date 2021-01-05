---
title: Een groot aantal taken verzenden naar een batch-taak
description: Informatie over het efficiënt verzenden van een zeer groot aantal taken in een enkele Azure Batch-taak.
ms.topic: how-to
ms.date: 12/30/2020
ms.custom: devx-track-python, devx-track-csharp
ms.openlocfilehash: 08cf92507a4556afbf56c9cb7e2c9c1b3a6c9479
ms.sourcegitcommit: beacda0b2b4b3a415b16ac2f58ddfb03dd1a04cf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/31/2020
ms.locfileid: "97831513"
---
# <a name="submit-a-large-number-of-tasks-to-a-batch-job"></a>Een groot aantal taken verzenden naar een batch-taak

Wanneer u grootschalige Azure Batch werk belastingen uitvoert, wilt u mogelijk tien tallen duizenden, honderd duizenden of zelfs meer taken verzenden naar één taak.

In dit artikel wordt beschreven hoe u een groot aantal taken kunt verzenden met een aanzienlijk verhoogde door Voer voor één batch-taak. Nadat de taken zijn verzonden, voeren ze de batch-wachtrij in voor verwerking op de groep die u voor de taak opgeeft.

## <a name="use-task-collections"></a>Taak verzamelingen gebruiken

Bij het toevoegen van een groot aantal taken gebruikt u de juiste methoden of overbelasting van de batch-Api's om taken als een *verzameling* toe te voegen in plaats van een voor een. Over het algemeen maakt u een taak verzameling door taken te definiëren tijdens het herhalen van een set invoer bestanden of para meters voor uw taak.

De maximale grootte van de taak verzameling die u in één aanroep kunt toevoegen, is afhankelijk van de batch-API die u gebruikt.

### <a name="apis-allowing-collections-of-up-to-100-tasks"></a>Api's die verzamelingen van Maxi maal 100 taken toestaan

Deze batch-Api's beperken de verzameling tot 100 taken. De limiet kan kleiner zijn, afhankelijk van de grootte van de taken (bijvoorbeeld als de taken een groot aantal bron bestanden of omgevings variabelen bevatten).

- [REST API](/rest/api/batchservice/task/addcollection)
- [Python-API](/python/api/azure-batch/azure.batch.operations.TaskOperations)
- [Node.js-API](/javascript/api/@azure/batch/task)

Wanneer u deze Api's gebruikt, moet u logica opgeven om het aantal taken te verdelen om te voldoen aan de verzamelings limiet, en om fouten af te handelen en opnieuw te proberen in geval van fouten bij het toevoegen van taken. Als een taak verzameling te groot is om toe te voegen, genereert de aanvraag een fout en moet deze opnieuw worden uitgevoerd met minder taken.

### <a name="apis-allowing-collections-of-larger-numbers-of-tasks"></a>Api's die verzamelingen van grotere aantallen taken toestaan

Andere batch-Api's ondersteunen veel grotere taak verzamelingen, alleen beperkt door de RAM-Beschik baarheid van de verzendende client. Deze Api's behandelen op transparante wijze de verzameling van de taken in ' chunks ' voor de Api's op lagere niveaus en nieuwe pogingen voor het toevoegen van mislukte taken.

- [.NET API](/dotnet/api/microsoft.azure.batch.cloudjob.addtaskasync)
- [Java-API](/java/api/com.microsoft.azure.batch.protocol.tasks.addcollectionasync)
- [Azure batch cli-extensie](batch-cli-templates.md) met batch-cli-sjablonen
- [Python SDK-extensie](https://pypi.org/project/azure-batch-extensions/)

## <a name="increase-throughput-of-task-submission"></a>De door Voer van het verzenden van taken verhogen

Het kan enige tijd duren om een grote verzameling taken toe te voegen aan een taak. Het toevoegen van 20.000-taken via de .NET API kan bijvoorbeeld een minuut duren. Afhankelijk van de batch-API en uw workload, kunt u de door Voer van de taak verbeteren door een of meer van de volgende wijzigingen aan te brengen.

### <a name="task-size"></a>Taak grootte

Het toevoegen van grote taken duurt langer dan kleinere items. Als u de grootte van elke taak in een verzameling wilt verkleinen, kunt u de taak opdracht regel vereenvoudigen, het aantal omgevings variabelen verminderen of de vereisten voor het uitvoeren van taken efficiënter afhandelen.

In plaats van een groot aantal bron bestanden te gebruiken, moet u bijvoorbeeld taak afhankelijkheden installeren met behulp van een [begin taak](jobs-and-tasks.md#start-task) in de groep of een [toepassings pakket](batch-application-packages.md) of [docker-container](batch-docker-container-workloads.md)gebruiken.

### <a name="number-of-parallel-operations"></a>Aantal parallelle bewerkingen

Afhankelijk van de batch-API kunt u de door Voer verhogen door het maximum aantal gelijktijdige bewerkingen door de batch-client te verhogen. Configureer deze instelling met behulp van de eigenschap [BatchClientParallelOptions. MaxDegreeOfParallelism](/dotnet/api/microsoft.azure.batch.batchclientparalleloptions.maxdegreeofparallelism) in de .net API of de `threads` para meter van methoden als [TaskOperations.add_collection](/python/api/azure-batch/azure.batch.operations.TaskOperations) in de batch python SDK-extensie. (Deze eigenschap is niet beschikbaar in de systeem eigen batch python SDK.)

Deze eigenschap is standaard ingesteld op 1, maar u kunt deze hoger instellen om de door Voer van bewerkingen te verbeteren. U kunt een verhoogde door Voer afhandelen door gebruik te maken van de netwerk bandbreedte en enkele CPU-prestaties. De door Voer van de taak wordt verhoogd met Maxi maal 100 keer de `MaxDegreeOfParallelism` of `threads` . In de praktijk moet u het aantal gelijktijdige bewerkingen instellen op onder 100.

 Met de Azure Batch CLI-uitbrei ding met batch sjablonen wordt het aantal gelijktijdige bewerkingen automatisch verhoogd op basis van het aantal beschik bare kernen, maar deze eigenschap kan niet worden geconfigureerd in de CLI.

### <a name="http-connection-limits"></a>Limieten voor HTTP-verbindingen

Het uitvoeren van veel gelijktijdige HTTP-verbindingen kan de prestaties van de batch-client beperken wanneer er grote aantallen taken worden toegevoegd. Sommige Api's beperken het aantal HTTP-verbindingen. Bij het ontwikkelen met de .NET API, bijvoorbeeld de eigenschap [ServicePointManager. DefaultConnectionLimit](/dotnet/api/system.net.servicepointmanager.defaultconnectionlimit) is standaard ingesteld op 2. U wordt aangeraden de waarde te verhogen naar een getal dat dicht bij of gelijk is aan het aantal parallelle bewerkingen.

## <a name="example-batch-net"></a>Voor beeld: batch .NET

In de volgende C#-fragmenten worden instellingen weer gegeven voor configuratie bij het toevoegen van een groot aantal taken met behulp van de batch .NET-API.

Verhoog de waarde van de eigenschap [MaxDegreeOfParallelism](/dotnet/api/microsoft.azure.batch.batchclientparalleloptions.maxdegreeofparallelism) van de [BatchClient](/dotnet/api/microsoft.azure.batch.batchclient)om de door Voer van de taak te verg Roten. Bijvoorbeeld:

```csharp
BatchClientParallelOptions parallelOptions = new BatchClientParallelOptions()
  {
    MaxDegreeOfParallelism = 15
  };
...
```

Voeg een taak verzameling toe aan de taak met behulp van de juiste overbelasting van de methode [AddTaskAsync](/dotnet/api/microsoft.azure.batch.cloudjob.addtaskasync) of [AddTask](/dotnet/api/microsoft.azure.batch.cloudjob.addtask
) . Bijvoorbeeld:

```csharp
// Add a list of tasks as a collection
List<CloudTask> tasksToAdd = new List<CloudTask>(); // Populate with your tasks
...
await batchClient.JobOperations.AddTaskAsync(jobId, tasksToAdd, parallelOptions);
```

## <a name="example-batch-cli-extension"></a>Voor beeld: batch CLI-extensie

Maak met behulp van de Azure Batch CLI-extensies met [batch-cli-sjablonen](batch-cli-templates.md)een taak sjabloon-JSON-bestand dat een [taak fabriek](https://github.com/Azure/azure-batch-cli-extensions/blob/master/doc/taskFactories.md)bevat. De taak-Factory configureert een verzameling van gerelateerde taken voor een taak uit een enkele taak definitie.  

Hier volgt een voor beeld van een taak sjabloon voor een one-dimensionale parametrische sweep-taak met een groot aantal taken (in dit geval 250.000). De opdracht regel van de taak is een eenvoudige `echo` opdracht.

```json
{
    "job": {
        "type": "Microsoft.Batch/batchAccounts/jobs",
        "apiVersion": "2016-12-01",
        "properties": {
            "id": "myjob",
            "constraints": {
                "maxWallClockTime": "PT5H",
                "maxTaskRetryCount": 1
            },
            "poolInfo": {
                "poolId": "mypool"
            },
            "taskFactory": {
                "type": "parametricSweep",
                "parameterSets": [
                    {
                        "start": 1,
                        "end": 250000,
                        "step": 1
                    }
                ],
                "repeatTask": {
                    "commandLine": "/bin/bash -c 'echo Hello world from task {0}'",
                    "constraints": {
                        "retentionTime":"PT1H"
                    }
                }
            },
            "onAllTasksComplete": "terminatejob"
        }
    }
}
```

Zie [Azure batch cli-sjablonen en-bestands overdracht gebruiken](batch-cli-templates.md)als u een taak wilt uitvoeren met de sjabloon.

## <a name="example-batch-python-sdk-extension"></a>Voor beeld: batch python SDK-extensie

Als u de Azure Batch python SDK-extensie wilt gebruiken, installeert u eerst de python-SDK en de uitbrei ding:

```
pip install azure-batch
pip install azure-batch-extensions
```

Stel een waarde in `BatchExtensionsClient` die gebruikmaakt van de SDK-extensie:

```python

client = batch.BatchExtensionsClient(
    base_url=BATCH_ACCOUNT_URL, resource_group=RESOURCE_GROUP_NAME, batch_account=BATCH_ACCOUNT_NAME)
...
```

Een verzameling taken maken om aan een taak toe te voegen. Bijvoorbeeld:

```python
tasks = list()
# Populate the list with your tasks
...
```

Voeg de taak verzameling toe met behulp van [Task.add_collection](/python/api/azure-batch/azure.batch.operations.TaskOperations). Stel de `threads` para meter in om het aantal gelijktijdige bewerkingen te verhogen:

```python
try:
    client.task.add_collection(job_id, threads=100)
except Exception as e:
    raise e
```

De batch python SDK-extensie biedt ook ondersteuning voor het toevoegen van taak parameters aan een taak met behulp van een JSON-specificatie voor een taak fabriek. Zo kunt u bijvoorbeeld taak parameters configureren voor een parametrische sweep die vergelijkbaar is met die in de voor gaande batch CLI-sjabloon:

```python
parameter_sweep = {
    "job": {
        "type": "Microsoft.Batch/batchAccounts/jobs",
        "apiVersion": "2016-12-01",
        "properties": {
            "id": "myjob",
            "poolInfo": {
                "poolId": "mypool"
            },
            "taskFactory": {
                "type": "parametricSweep",
                "parameterSets": [
                    {
                        "start": 1,
                        "end": 250000,
                        "step": 1
                    }
                ],
                "repeatTask": {
                    "commandLine": "/bin/bash -c 'echo Hello world from task {0}'",
                    "constraints": {
                        "retentionTime": "PT1H"
                    }
                }
            },
            "onAllTasksComplete": "terminatejob"
        }
    }
}
...
job_json = client.job.expand_template(parameter_sweep)
job_parameter = client.job.jobparameter_from_json(job_json)
```

Voeg de taak parameters toe aan de taak. Stel de `threads` para meter in om het aantal gelijktijdige bewerkingen te verhogen:

```python
try:
    client.job.add(job_parameter, threads=50)
except Exception as e:
    raise e
```

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het gebruik van de Azure Batch CLI-uitbrei ding met [batch-cli-sjablonen](batch-cli-templates.md).
- Meer informatie over de [batch PYTHON SDK-extensie](https://pypi.org/project/azure-batch-extensions/).
- Meer informatie over [Best practices voor Azure batch](best-practices.md).
