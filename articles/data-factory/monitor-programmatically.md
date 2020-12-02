---
title: Een Azure-data factory programmatisch bewaken
description: Meer informatie over het bewaken van een pijp lijn in een data factory met behulp van verschillende Sdk's (Software Development Kits).
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/16/2018
author: dcstwh
ms.author: weetok
manager: anandsub
ms.custom: devx-track-python
ms.openlocfilehash: e7b435080fda48e1df0bb31d16dafed30ac3d3db
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96497857"
---
# <a name="programmatically-monitor-an-azure-data-factory"></a>Een Azure-data factory programmatisch bewaken

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In dit artikel wordt beschreven hoe u een pijp lijn in een data factory bewaakt met behulp van verschillende Sdk's (Software Development Kits). 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="data-range"></a>Gegevens bereik

Data Factory slaat alleen pijplijn run data op voor 45 dagen. Wanneer u een query uitvoert op programmeer niveau voor gegevens over Data Factory pijplijn uitvoeringen, bijvoorbeeld met de Power shell-opdracht, `Get-AzDataFactoryV2PipelineRun` zijn er geen maximum datums voor de optionele `LastUpdatedAfter` `LastUpdatedBefore` para meters. Als u bijvoorbeeld een query uitvoert voor gegevens in het afgelopen jaar, retourneert de query geen fout, maar retourneert alleen pijplijn gegevens uit de afgelopen 45 dagen.

Als u de gegevens van de pijplijn periode langer dan 45 dagen wilt behouden, stelt u uw eigen diagnostische logboek registratie in met [Azure monitor](monitor-using-azure-monitor.md).

## <a name="net"></a>.NET
Zie [een Data Factory en pijp lijn maken met behulp van .net](quickstart-create-data-factory-dot-net.md)voor een volledig overzicht van het maken en bewaken van een pijp lijn met behulp van .NET SDK.

1. Voeg de volgende code toe om continu de status van de pijplijn uitvoering te controleren totdat deze klaar is met het kopiëren van de gegevens.

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

2. Voeg de volgende code toe om details van de uitvoer van de Kopieer activiteit op te halen, bijvoorbeeld de grootte van de gegevens die zijn gelezen/geschreven.

    ```csharp
    // Check the copy activity run details
    Console.WriteLine("Checking copy activity run details...");
   
    List<ActivityRun> activityRuns = client.ActivityRuns.ListByPipelineRun(
    resourceGroup, dataFactoryName, runResponse.RunId, DateTime.UtcNow.AddMinutes(-10), DateTime.UtcNow.AddMinutes(10)).ToList(); 
    if (pipelineRun.Status == "Succeeded")
        Console.WriteLine(activityRuns.First().Output);
    else
        Console.WriteLine(activityRuns.First().Error);
    Console.WriteLine("\nPress any key to exit...");
    Console.ReadKey();
    ```

Zie [Data Factory Naslag informatie over .NET SDK](/dotnet/api/microsoft.azure.management.datafactory?view=azure-dotnet)voor volledige documentatie over .NET SDK.

## <a name="python"></a>Python
Zie [een Data Factory en pijp lijn maken met behulp](quickstart-create-data-factory-python.md)van python voor een volledig overzicht van het maken en bewaken van een pijp lijn met behulp van python SDK.

Als u de uitvoering van de pijp lijn wilt controleren, voegt u de volgende code toe:

```python
# Monitor the pipeline run
time.sleep(30)
pipeline_run = adf_client.pipeline_runs.get(
    rg_name, df_name, run_response.run_id)
print("\n\tPipeline run status: {}".format(pipeline_run.status))
activity_runs_paged = list(adf_client.activity_runs.list_by_pipeline_run(
    rg_name, df_name, pipeline_run.run_id, datetime.now() - timedelta(1),  datetime.now() + timedelta(1)))
print_activity_run_details(activity_runs_paged[0])
```

Zie [Naslag informatie over Data Factory PYTHON SDK](/python/api/overview/azure/datafactory?view=azure-python)voor volledige documentatie over python SDK.

## <a name="rest-api"></a>REST-API
Zie [een Data Factory en pijp lijn maken met behulp van rest API](quickstart-create-data-factory-rest-api.md)voor een volledig overzicht van het maken en bewaken van een pijp lijn met behulp van rest API.
 
1. Voer het volgende script uit om continu de status van de pijplijnuitvoering te controleren totdat het kopiëren van de gegevens is voltooid.

    ```powershell
    $request = "https://management.azure.com/subscriptions/${subsId}/resourceGroups/${resourceGroup}/providers/Microsoft.DataFactory/factories/${dataFactoryName}/pipelineruns/${runId}?api-version=${apiVersion}"
    while ($True) {
        $response = Invoke-RestMethod -Method GET -Uri $request -Header $authHeader
        Write-Host  "Pipeline run status: " $response.Status -foregroundcolor "Yellow"

        if ($response.Status -eq "InProgress") {
            Start-Sleep -Seconds 15
        }
        else {
            $response | ConvertTo-Json
            break
        }
    }
    ```
2. Voer het volgende script uit om uitvoeringsdetails van de kopieeractiviteit op te halen, zoals de omvang van de gelezen of weggeschreven gegevens.

    ```powershell
    $request = "https://management.azure.com/subscriptions/${subsId}/resourceGroups/${resourceGroup}/providers/Microsoft.DataFactory/factories/${dataFactoryName}/pipelineruns/${runId}/activityruns?api-version=${apiVersion}&startTime="+(Get-Date).ToString('yyyy-MM-dd')+"&endTime="+(Get-Date).AddDays(1).ToString('yyyy-MM-dd')+"&pipelineName=Adfv2QuickStartPipeline"
    $response = Invoke-RestMethod -Method GET -Uri $request -Header $authHeader
    $response | ConvertTo-Json
    ```

Zie [Data Factory rest API Reference](/rest/api/datafactory/)(Engelstalig) voor volledige documentatie over rest API.

## <a name="powershell"></a>PowerShell
Zie [een Data Factory en pijp lijn maken met behulp van Power shell](quickstart-create-data-factory-powershell.md)voor een volledig overzicht van het maken en bewaken van een pijp lijn met behulp van Power shell.

1. Voer het volgende script uit om continu de status van de pijplijnuitvoering te controleren totdat het kopiëren van de gegevens is voltooid.

    ```powershell
    while ($True) {
        $run = Get-AzDataFactoryV2PipelineRun -ResourceGroupName $resourceGroupName -DataFactoryName $DataFactoryName -PipelineRunId $runId

        if ($run) {
            if ($run.Status -ne 'InProgress') {
                Write-Host "Pipeline run finished. The status is: " $run.Status -foregroundcolor "Yellow"
                $run
                break
            }
            Write-Host  "Pipeline is running...status: InProgress" -foregroundcolor "Yellow"
        }

        Start-Sleep -Seconds 30
    }
    ```
2. Voer het volgende script uit om uitvoeringsdetails van de kopieeractiviteit op te halen, zoals de omvang van de gelezen of weggeschreven gegevens.

    ```powershell
    Write-Host "Activity run details:" -foregroundcolor "Yellow"
    $result = Get-AzDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)
    $result
    
    Write-Host "Activity 'Output' section:" -foregroundcolor "Yellow"
    $result.Output -join "`r`n"
    
    Write-Host "\nActivity 'Error' section:" -foregroundcolor "Yellow"
    $result.Error -join "`r`n"
    ```

Zie [Data Factory Power shell-cmdlet-Naslag informatie](/powershell/module/az.datafactory)voor volledige documentatie over Power shell-cmdlets.

## <a name="next-steps"></a>Volgende stappen
Zie [pijp lijnen bewaken met behulp van Azure monitor](monitor-using-azure-monitor.md) artikel voor meer informatie over het gebruik van Azure Monitor om Data Factory pijp lijnen te bewaken. 

