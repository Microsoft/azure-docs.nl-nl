---
title: Machine Learning pijplijn YAML
titleSuffix: Azure Machine Learning
description: Meer informatie over het definiëren van een machine learning pijp lijn met behulp van een YAML-bestand. YAML pijplijn definities worden gebruikt met de extensie machine learning voor de Azure CLI.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.reviewer: larryfr
ms.author: nilsp
author: NilsPohlmann
ms.date: 07/31/2020
ms.custom: devx-track-python
ms.openlocfilehash: e2b5a3322f633ca8301357c2186d78d3ac437ae2
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102521965"
---
# <a name="define-machine-learning-pipelines-in-yaml"></a>machine learning pijp lijnen definiëren in YAML

Meer informatie over het definiëren van uw machine learning-pijp lijnen in [yaml](https://yaml.org/). Wanneer u de extensie machine learning gebruikt voor de Azure CLI, verwachten veel van de aan de pipeline gerelateerde opdrachten een YAML-bestand dat de pijp lijn definieert.

De volgende tabel bevat een overzicht van wat is en wordt momenteel niet ondersteund bij het definiëren van een pijp lijn in YAML:

| Type stap | Ondersteund? |
| ----- | :-----: |
| PythonScriptStep | Ja |
| ParallelRunStep | Ja |
| AdlaStep | Ja |
| AzureBatchStep | Ja |
| DatabricksStep | Ja |
| DataTransferStep | Ja |
| AutoMLStep | Nee |
| HyperDriveStep | Nee |
| ModuleStep | Ja |
| MPIStep | Nee |
| EstimatorStep | Nee |

## <a name="pipeline-definition"></a>Pijplijn definitie

Een pijplijn definitie maakt gebruik van de volgende sleutels, die overeenkomen met de klasse [pijp lijnen](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline.pipeline) :

| YAML-sleutel | Beschrijving |
| ----- | ----- |
| `name` | De beschrijving van de pijp lijn. |
| `parameters` | Para meter (s) naar de pijp lijn. |
| `data_reference` | Hiermee definieert u hoe en waar gegevens beschikbaar moeten worden gemaakt in een run. |
| `default_compute` | Standaard Compute target waarbij alle stappen in de pijplijn worden uitgevoerd. |
| `steps` | De stappen die worden gebruikt in de pijp lijn. |

## <a name="parameters"></a>Parameters

`parameters`In de sectie worden de volgende sleutels gebruikt, die overeenkomen met de klasse [PipelineParameter](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelineparameter) :

| YAML-sleutel | Beschrijving |
| ---- | ---- |
| `type` | Het waardetype van de para meter. Geldige typen zijn `string` , `int` , `float` , `bool` , of `datapath` . |
| `default` | De standaard waarde. |

Elke para meter heeft de naam. Het volgende YAML-code fragment definieert bijvoorbeeld drie para meters met de naam `NumIterationsParameter` , `DataPathParameter` en `NodeCountParameter` :

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        NumIterationsParameter:
            type: int
            default: 40
        DataPathParameter:
            type: datapath
            default:
                datastore: workspaceblobstore
                path_on_datastore: sample2.txt
        NodeCountParameter:
            type: int
            default: 4
```

## <a name="data-reference"></a>Verwijzing naar gegevens

`data_references`In het gedeelte worden de volgende sleutels gebruikt, die overeenkomen met de [DataReference](/python/api/azureml-core/azureml.data.data_reference.datareference):

| YAML-sleutel | Beschrijving |
| ----- | ----- |
| `datastore` | De gegevens opslag waarnaar wordt verwezen. |
| `path_on_datastore` | Het relatieve pad in de back-upopslag voor de gegevens verwijzing. |

Elke gegevens verwijzing bevindt zich in een sleutel. Met het volgende YAML-code fragment wordt bijvoorbeeld een gegevens referentie gedefinieerd die is opgeslagen in de sleutel met de naam `employee_data` :

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        PipelineParam1:
            type: int
            default: 3
    data_references:
        employee_data:
            datastore: adftestadla
            path_on_datastore: "adla_sample/sample_input.csv"
```

## <a name="steps"></a>Stappen

In stappen wordt een reken omgeving gedefinieerd, samen met de bestanden die in de omgeving moeten worden uitgevoerd. Als u het type van een stap wilt definiëren, gebruikt u de `type` sleutel:

| Type stap | Beschrijving |
| ----- | ----- |
| `AdlaStep` | Voert een U-SQL-script uit met Azure Data Lake Analytics. Komt overeen met de [AdlaStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.adlastep) -klasse. |
| `AzureBatchStep` | Voert taken uit met Azure Batch. Komt overeen met de [AzureBatchStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.azurebatchstep) -klasse. |
| `DatabricsStep` | Hiermee voegt u een Databricks-notebook, python-script of JAR toe. Komt overeen met de [DatabricksStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricksstep) -klasse. |
| `DataTransferStep` | Hiermee worden gegevens overgebracht tussen opslag opties. Komt overeen met de [DataTransferStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep) -klasse. |
| `PythonScriptStep` | Voert een python-script uit. Komt overeen met de [PythonScriptStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep) -klasse. |
| `ParallelRunStep` | Voert een python-script uit om grote hoeveel heden gegevens asynchroon en parallel te verwerken. Komt overeen met de [ParallelRunStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.parallel_run_step.parallelrunstep) -klasse. |

### <a name="adla-step"></a>ADLA stap

| YAML-sleutel | Beschrijving |
| ----- | ----- |
| `script_name` | De naam van het U-SQL-script (ten opzichte van de `source_directory` ). |
| `compute_target` | Het Azure Data Lake Compute-doel dat moet worden gebruikt voor deze stap. |
| `parameters` | [Para meters](#parameters) voor de pijp lijn. |
| `inputs` | Invoer kan [InputPortBinding](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding), [DataReference](#data-reference), [PortDataReference](/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference), [PipelineData](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata), [DataSet](/python/api/azureml-core/azureml.core.dataset%28class%29), [DatasetDefinition](/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition)of [PipelineDataset](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset)zijn. |
| `outputs` | De uitvoer kan [PipelineData](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata) of [OutputPortBinding](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding)zijn. |
| `source_directory` | Map die het script, assembly's, enzovoort bevat. |
| `priority` | De prioriteits waarde die voor de huidige taak moet worden gebruikt. |
| `params` | Woorden lijst met naam/waarde-paren. |
| `degree_of_parallelism` | De mate van parallelle uitvoering die moet worden gebruikt voor deze taak. |
| `runtime_version` | De runtime versie van de Data Lake Analytics-engine. |
| `allow_reuse` | Hiermee wordt bepaald of de stap eerdere resultaten opnieuw moet gebruiken wanneer deze opnieuw wordt uitgevoerd met dezelfde instellingen. |

Het volgende voor beeld bevat een ADLA-stap definitie:

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        PipelineParam1:
            type: int
            default: 3
    data_references:
        employee_data:
            datastore: adftestadla
            path_on_datastore: "adla_sample/sample_input.csv"
    default_compute: adlacomp
    steps:
        Step1:
            runconfig: "D:\\Yaml\\default_runconfig.yml"
            parameters:
                NUM_ITERATIONS_2:
                    source: PipelineParam1
                NUM_ITERATIONS_1: 7
            type: "AdlaStep"
            name: "MyAdlaStep"
            script_name: "sample_script.usql"
            source_directory: "D:\\scripts\\Adla"
            inputs:
                employee_data:
                    source: employee_data
            outputs:
                OutputData:
                    destination: Output4
                    datastore: adftestadla
                    bind_mode: mount
```

### <a name="azure-batch-step"></a>Azure Batch stap

| YAML-sleutel | Beschrijving |
| ----- | ----- |
| `compute_target` | Het Azure Batch Compute-doel dat moet worden gebruikt voor deze stap. |
| `inputs` | Invoer kan [InputPortBinding](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding), [DataReference](#data-reference), [PortDataReference](/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference), [PipelineData](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata), [DataSet](/python/api/azureml-core/azureml.core.dataset%28class%29), [DatasetDefinition](/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition)of [PipelineDataset](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset)zijn. |
| `outputs` | De uitvoer kan [PipelineData](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata) of [OutputPortBinding](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding)zijn. |
| `source_directory` | Map die de module binaire bestanden, uitvoer bare onderdelen, enzovoort bevat. |
| `executable` | De naam van de opdracht/het uitvoer bare bestand dat wordt uitgevoerd als onderdeel van deze taak. |
| `create_pool` | Een Booleaanse vlag die aangeeft of de groep moet worden gemaakt voordat de taak wordt uitgevoerd. |
| `delete_batch_job_after_finish` | Een Booleaanse vlag die aangeeft of de taak moet worden verwijderd uit het batch-account nadat deze is voltooid. |
| `delete_batch_pool_after_finish` | Een Booleaanse vlag die aangeeft of de groep moet worden verwijderd nadat de taak is voltooid. |
| `is_positive_exit_code_failure` | Een Booleaanse vlag die aangeeft of de taak mislukt als de taak wordt afgesloten met een positieve code. |
| `vm_image_urn` | Als dat het geval `create_pool` is `True` , en VM gebruikt `VirtualMachineConfiguration` . |
| `pool_id` | De ID van de pool waarin de taak wordt uitgevoerd. |
| `allow_reuse` | Hiermee wordt bepaald of de stap eerdere resultaten opnieuw moet gebruiken wanneer deze opnieuw wordt uitgevoerd met dezelfde instellingen. |

Het volgende voor beeld bevat een Azure Batch stap definitie:

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        PipelineParam1:
            type: int
            default: 3
    data_references:
        input:
            datastore: workspaceblobstore
            path_on_datastore: "input.txt"
    default_compute: testbatch
    steps:
        Step1:
            runconfig: "D:\\Yaml\\default_runconfig.yml"
            parameters:
                NUM_ITERATIONS_2:
                    source: PipelineParam1
                NUM_ITERATIONS_1: 7
            type: "AzureBatchStep"
            name: "MyAzureBatchStep"
            pool_id: "MyPoolName"
            create_pool: true
            executable: "azurebatch.cmd"
            source_directory: "D:\\scripts\\AureBatch"
            allow_reuse: false
            inputs:
                input:
                    source: input
            outputs:
                output:
                    destination: output
                    datastore: workspaceblobstore
```

### <a name="databricks-step"></a>Databricks stap

| YAML-sleutel | Beschrijving |
| ----- | ----- |
| `compute_target` | Het Azure Databricks Compute-doel dat moet worden gebruikt voor deze stap. |
| `inputs` | Invoer kan [InputPortBinding](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding), [DataReference](#data-reference), [PortDataReference](/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference), [PipelineData](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata), [DataSet](/python/api/azureml-core/azureml.core.dataset%28class%29), [DatasetDefinition](/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition)of [PipelineDataset](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset)zijn. |
| `outputs` | De uitvoer kan [PipelineData](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata) of [OutputPortBinding](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding)zijn. |
| `run_name` | De naam in Databricks voor deze uitvoering. |
| `source_directory` | Map die het script en andere bestanden bevat. |
| `num_workers` | Het statische aantal werk rollen voor het Databricks-uitvoerings cluster. |
| `runconfig` | Het pad naar een `.runconfig` bestand. Dit bestand is een YAMLe weer gave van de klasse [RunConfiguration](/python/api/azureml-core/azureml.core.runconfiguration) . Zie [runconfigschema.jsop](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json)voor meer informatie over de structuur van dit bestand. |
| `allow_reuse` | Hiermee wordt bepaald of de stap eerdere resultaten opnieuw moet gebruiken wanneer deze opnieuw wordt uitgevoerd met dezelfde instellingen. |

Het volgende voor beeld bevat een Databricks-stap:

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        PipelineParam1:
            type: int
            default: 3
    data_references:
        adls_test_data:
            datastore: adftestadla
            path_on_datastore: "testdata"
        blob_test_data:
            datastore: workspaceblobstore
            path_on_datastore: "dbtest"
    default_compute: mydatabricks
    steps:
        Step1:
            runconfig: "D:\\Yaml\\default_runconfig.yml"
            parameters:
                NUM_ITERATIONS_2:
                    source: PipelineParam1
                NUM_ITERATIONS_1: 7
            type: "DatabricksStep"
            name: "MyDatabrickStep"
            run_name: "DatabricksRun"
            python_script_name: "train-db-local.py"
            source_directory: "D:\\scripts\\Databricks"
            num_workers: 1
            allow_reuse: true
            inputs:
                blob_test_data:
                    source: blob_test_data
            outputs:
                OutputData:
                    destination: Output4
                    datastore: workspaceblobstore
                    bind_mode: mount
```

### <a name="data-transfer-step"></a>Stap voor gegevens overdracht

| YAML-sleutel | Beschrijving |
| ----- | ----- |
| `compute_target` | Het Azure Data Factory Compute-doel dat moet worden gebruikt voor deze stap. |
| `source_data_reference` | Invoer verbinding die fungeert als bron van bewerkingen voor gegevens overdracht. Ondersteunde waarden zijn [InputPortBinding](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding), [DataReference](#data-reference), [PortDataReference](/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference), [PipelineData](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata), [DataSet](/python/api/azureml-core/azureml.core.dataset%28class%29), [DatasetDefinition](/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition)of [PipelineDataset](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset). |
| `destination_data_reference` | Invoer verbinding die fungeert als bestemming voor de overdracht van gegevens. Ondersteunde waarden zijn [PipelineData](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata) en [OutputPortBinding](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding). |
| `allow_reuse` | Hiermee wordt bepaald of de stap eerdere resultaten opnieuw moet gebruiken wanneer deze opnieuw wordt uitgevoerd met dezelfde instellingen. |

Het volgende voor beeld bevat een stap voor gegevens overdracht:

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        PipelineParam1:
            type: int
            default: 3
    data_references:
        adls_test_data:
            datastore: adftestadla
            path_on_datastore: "testdata"
        blob_test_data:
            datastore: workspaceblobstore
            path_on_datastore: "testdata"
    default_compute: adftest
    steps:
        Step1:
            runconfig: "D:\\Yaml\\default_runconfig.yml"
            parameters:
                NUM_ITERATIONS_2:
                    source: PipelineParam1
                NUM_ITERATIONS_1: 7
            type: "DataTransferStep"
            name: "MyDataTransferStep"
            adla_compute_name: adftest
            source_data_reference:
                adls_test_data:
                    source: adls_test_data
            destination_data_reference:
                blob_test_data:
                    source: blob_test_data
```

### <a name="python-script-step"></a>Python-script stap

| YAML-sleutel | Beschrijving |
| ----- | ----- |
| `inputs` | Invoer kan [InputPortBinding](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding), [DataReference](#data-reference), [PortDataReference](/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference), [PipelineData](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata), [DataSet](/python/api/azureml-core/azureml.core.dataset%28class%29), [DatasetDefinition](/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition)of [PipelineDataset](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset)zijn. |
| `outputs` | De uitvoer kan [PipelineData](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata) of [OutputPortBinding](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding)zijn. |
| `script_name` | De naam van het python-script (relatief aan `source_directory` ). |
| `source_directory` | De map die het script, de Conda-omgeving bevat, enzovoort. |
| `runconfig` | Het pad naar een `.runconfig` bestand. Dit bestand is een YAMLe weer gave van de klasse [RunConfiguration](/python/api/azureml-core/azureml.core.runconfiguration) . Zie [runconfig.jsop](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json)voor meer informatie over de structuur van dit bestand. |
| `allow_reuse` | Hiermee wordt bepaald of de stap eerdere resultaten opnieuw moet gebruiken wanneer deze opnieuw wordt uitgevoerd met dezelfde instellingen. |

Het volgende voor beeld bevat een python-script stap:

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        PipelineParam1:
            type: int
            default: 3
    data_references:
        DataReference1:
            datastore: workspaceblobstore
            path_on_datastore: testfolder/sample.txt
    default_compute: cpu-cluster
    steps:
        Step1:
            runconfig: "D:\\Yaml\\default_runconfig.yml"
            parameters:
                NUM_ITERATIONS_2:
                    source: PipelineParam1
                NUM_ITERATIONS_1: 7
            type: "PythonScriptStep"
            name: "MyPythonScriptStep"
            script_name: "train.py"
            allow_reuse: True
            source_directory: "D:\\scripts\\PythonScript"
            inputs:
                InputData:
                    source: DataReference1
            outputs:
                OutputData:
                    destination: Output4
                    datastore: workspaceblobstore
                    bind_mode: mount
```

### <a name="parallel-run-step"></a>Parallelle uitvoerings stap

| YAML-sleutel | Beschrijving |
| ----- | ----- |
| `inputs` | Invoer kan [DataSet](/python/api/azureml-core/azureml.core.dataset%28class%29), [DatasetDefinition](/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition)of [PipelineDataset](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset)zijn. |
| `outputs` | De uitvoer kan [PipelineData](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata) of [OutputPortBinding](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding)zijn. |
| `script_name` | De naam van het python-script (relatief aan `source_directory` ). |
| `source_directory` | De map die het script, de Conda-omgeving bevat, enzovoort. |
| `parallel_run_config` | Het pad naar een `parallel_run_config.yml` bestand. Dit bestand is een YAMLe weer gave van de klasse [ParallelRunConfig](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.parallelrunconfig) . |
| `allow_reuse` | Hiermee wordt bepaald of de stap eerdere resultaten opnieuw moet gebruiken wanneer deze opnieuw wordt uitgevoerd met dezelfde instellingen. |

Het volgende voor beeld bevat een parallelle uitvoerings stap:

```yaml
pipeline:
    description: SamplePipelineFromYaml
    default_compute: cpu-cluster
    data_references:
        MyMinistInput:
            dataset_name: mnist_sample_data
    parameters:
        PipelineParamTimeout:
            type: int
            default: 600
    steps:        
        Step1:
            parallel_run_config: "yaml/parallel_run_config.yml"
            type: "ParallelRunStep"
            name: "parallel-run-step-1"
            allow_reuse: True
            arguments:
            - "--progress_update_timeout"
            - parameter:timeout_parameter
            - "--side_input"
            - side_input:SideInputData
            parameters:
                timeout_parameter:
                    source: PipelineParamTimeout
            inputs:
                InputData:
                    source: MyMinistInput
            side_inputs:
                SideInputData:
                    source: Output4
                    bind_mode: mount
            outputs:
                OutputDataStep2:
                    destination: Output5
                    datastore: workspaceblobstore
                    bind_mode: mount
```

### <a name="pipeline-with-multiple-steps"></a>Pijp lijn met meerdere stappen 

| YAML-sleutel | Beschrijving |
| ----- | ----- |
| `steps` | Reeks van een of meer PipelineStep definities. Houd er rekening mee dat de `destination` sleutels van de ene stap `outputs` de `source` sleutels worden naar de `inputs` volgende stap.| 

```yaml
pipeline:
    name: SamplePipelineFromYAML
    description: Sample multistep YAML pipeline
    data_references:
        TitanicDS:
            dataset_name: 'titanic_ds'
            bind_mode: download
    default_compute: cpu-cluster
    steps:
        Dataprep:
            type: "PythonScriptStep"
            name: "DataPrep Step"
            compute: cpu-cluster
            runconfig: ".\\default_runconfig.yml"
            script_name: "prep.py"
            arguments:
            - '--train_path'
            - output:train_path
            - '--test_path'
            - output:test_path
            allow_reuse: True
            inputs:
                titanic_ds:
                    source: TitanicDS
                    bind_mode: download
            outputs:
                train_path:
                    destination: train_csv
                    datastore: workspaceblobstore
                test_path:
                    destination: test_csv
        Training:
            type: "PythonScriptStep"
            name: "Training Step"
            compute: cpu-cluster
            runconfig: ".\\default_runconfig.yml"
            script_name: "train.py"
            arguments:
            - "--train_path"
            - input:train_path
            - "--test_path"
            - input:test_path
            inputs:
                train_path:
                    source: train_csv
                    bind_mode: download
                test_path:
                    source: test_csv
                    bind_mode: download

```

## <a name="schedules"></a>Schema's

Bij het definiëren van het schema voor een pijp lijn kan het gegevens archief worden geactiveerd of terugkerend op basis van een tijds interval. Hieronder vindt u de sleutels die worden gebruikt voor het definiëren van een planning:

| YAML-sleutel | Beschrijving |
| ----- | ----- |
| `description` | Een beschrijving van het schema. |
| `recurrence` | Bevat de instellingen van het terugkeer patroon als de planning terugkerend is. |
| `pipeline_parameters` | Alle para meters die vereist zijn voor de pijp lijn. |
| `wait_for_provisioning` | Hiermee wordt aangegeven of moet worden gewacht tot de inrichting van het schema is voltooid. |
| `wait_timeout` | Het aantal seconden dat moet worden gewacht voordat een time-out optreedt. |
| `datastore_name` | De gegevens opslag die moet worden bewaakt voor gewijzigde/toegevoegde blobs. |
| `polling_interval` | Hoe lang, in minuten, tussen polling voor gewijzigde/toegevoegde blobs. Standaard waarde: 5 minuten. Alleen ondersteund voor Data Store-schema's. |
| `data_path_parameter_name` | De naam van de pijplijn parameter van het gegevenspad dat moet worden ingesteld met het gewijzigde BLOB-pad. Alleen ondersteund voor Data Store-schema's. |
| `continue_on_step_failure` | Hiermee wordt aangegeven of de uitvoering van andere stappen in de verzonden PipelineRun moet blijven werken als een stap mislukt. Als deze optie wordt gegeven, wordt de `continue_on_step_failure` instelling van de pijp lijn overschreven.
| `path_on_datastore` | Optioneel. Het pad naar het gegevens archief dat moet worden bewaakt voor gewijzigde/toegevoegde blobs. Het pad bevindt zich onder de container voor de gegevens opslag, dus het werkelijke pad van de plannings monitors is container/ `path_on_datastore` . Als geen, wordt de Data Store-container gecontroleerd. Toevoegingen/wijzigingen die zijn aangebracht in een submap van de `path_on_datastore` worden niet bewaakt. Alleen ondersteund voor Data Store-schema's. |

Het volgende voor beeld bevat de definitie voor een planning die wordt geactiveerd door een Data Store:

```yaml
Schedule: 
      description: "Test create with datastore" 
      recurrence: ~ 
      pipeline_parameters: {} 
      wait_for_provisioning: True 
      wait_timeout: 3600 
      datastore_name: "workspaceblobstore" 
      polling_interval: 5 
      data_path_parameter_name: "input_data" 
      continue_on_step_failure: None 
      path_on_datastore: "file/path" 
```

Bij het definiëren van een **terugkerend schema** gebruikt u de volgende sleutels onder `recurrence` :

| YAML-sleutel | Beschrijving |
| ----- | ----- |
| `frequency` | Hoe vaak de planning wordt herhaald. Geldige waarden zijn,,, en `"Minute"` `"Hour"` `"Day"` `"Week"` `"Month"` . |
| `interval` | Hoe vaak de planning wordt geactiveerd. De waarde van het gehele getal is het aantal tijds eenheden dat moet worden gewacht tot de planning opnieuw wordt geactiveerd. |
| `start_time` | De begin tijd voor de planning. De teken reeks notatie van de waarde is `YYYY-MM-DDThh:mm:ss` . Als er geen start tijd wordt gegeven, wordt de eerste werk belasting onmiddellijk uitgevoerd en worden toekomstige werk belastingen uitgevoerd op basis van de planning. Als de begin tijd in het verleden ligt, wordt de eerste werk belasting uitgevoerd op de volgende berekende uitvoerings tijd. |
| `time_zone` | De tijd zone voor de begin tijd. Als er geen tijd zone wordt gegeven, wordt UTC gebruikt. |
| `hours` | Als `frequency` dat zo is `"Day"` `"Week"` , kunt u een of meer gehele getallen van 0 tot en met 23, gescheiden door komma's, opgeven als de uren van de dag waarop de pijp lijn moet worden uitgevoerd. Alleen `time_of_day` of `hours` en `minutes` kunnen worden gebruikt. |
| `minutes` | Als `frequency` dat zo is `"Day"` `"Week"` , kunt u een of meer gehele getallen van 0 tot en met 59 opgeven, gescheiden door komma's, als de minuten van het uur waarop de pijp lijn moet worden uitgevoerd. Alleen `time_of_day` of `hours` en `minutes` kunnen worden gebruikt. |
| `time_of_day` | Als dat zo `frequency` is `"Day"` `"Week"` , kunt u een tijdstip opgeven waarop het schema moet worden uitgevoerd. De teken reeks notatie van de waarde is `hh:mm` . Alleen `time_of_day` of `hours` en `minutes` kunnen worden gebruikt. |
| `week_days` | Als `frequency` `"Week"` dat het geval is, kunt u een of meer dagen opgeven, gescheiden door komma's, wanneer het schema moet worden uitgevoerd. Geldige waarden zijn,,,,, en `"Monday"` `"Tuesday"` `"Wednesday"` `"Thursday"` `"Friday"` `"Saturday"` `"Sunday"` . |

Het volgende voor beeld bevat de definitie van een terugkerend schema:

```yaml
Schedule: 
    description: "Test create with recurrence" 
    recurrence: 
        frequency: Week # Can be "Minute", "Hour", "Day", "Week", or "Month". 
        interval: 1 # how often fires 
        start_time: 2019-06-07T10:50:00 
        time_zone: UTC 
        hours: 
        - 1 
        minutes: 
        - 0 
        time_of_day: null 
        week_days: 
        - Friday 
    pipeline_parameters: 
        'a': 1 
    wait_for_provisioning: True 
    wait_timeout: 3600 
    datastore_name: ~ 
    polling_interval: ~ 
    data_path_parameter_name: ~ 
    continue_on_step_failure: None 
    path_on_datastore: ~ 
```

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [het gebruik van de CLI-extensie voor Azure machine learning](reference-azure-machine-learning-cli.md).