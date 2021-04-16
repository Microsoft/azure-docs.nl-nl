---
title: Machine Learning-pijplijn YAML
titleSuffix: Azure Machine Learning
description: Informatie over het definiëren van een machine learning pijplijn met behulp van een YAML-bestand. YAML-pijplijndefinities worden gebruikt met machine learning-extensie voor de Azure CLI.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.reviewer: larryfr
ms.author: nilsp
author: NilsPohlmann
ms.date: 07/31/2020
ms.custom: devx-track-python, devx-track-azurecli
ms.openlocfilehash: 1c44f018d558b9426ba6271c0cbb1c2356a2a9b2
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107478281"
---
# <a name="define-machine-learning-pipelines-in-yaml"></a>Definieer machine learning pijplijnen in YAML

Meer informatie over het definiëren van machine learning pijplijnen in [YAML](https://yaml.org/). Wanneer u de machine learning voor de Azure CLI gebruikt, verwachten veel van de pijplijnopdrachten een YAML-bestand dat de pijplijn definieert.

In de volgende tabel wordt vermeld wat wel en niet wordt ondersteund bij het definiëren van een pijplijn in YAML:

| Staptype | Ondersteund? |
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

## <a name="pipeline-definition"></a>Pijplijndefinitie

Een pijplijndefinitie maakt gebruik van de volgende sleutels, die overeenkomen met de [klasse Pipelines:](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline.pipeline)

| YAML-sleutel | Beschrijving |
| ----- | ----- |
| `name` | De beschrijving van de pijplijn. |
| `parameters` | Parameter(s) voor de pijplijn. |
| `data_reference` | Definieert hoe en waar gegevens beschikbaar moeten worden gesteld in een run. |
| `default_compute` | Standaardrekendoel waar alle stappen in de pijplijn worden uitgevoerd. |
| `steps` | De stappen die in de pijplijn worden gebruikt. |

## <a name="parameters"></a>Parameters

In `parameters` de sectie worden de volgende sleutels gebruikt, die overeenkomen met de klasse [PipelineParameter:](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelineparameter)

| YAML-sleutel | Beschrijving |
| ---- | ---- |
| `type` | Het waardetype van de parameter. Geldige typen zijn `string` , , , of `int` `float` `bool` `datapath` . |
| `default` | De standaardwaarde. |

Elke parameter heeft de naam . Het volgende YAML-fragment definieert bijvoorbeeld drie parameters `NumIterationsParameter` met de naam , en `DataPathParameter` `NodeCountParameter` :

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

In `data_references` de sectie worden de volgende sleutels gebruikt, die overeenkomen met de [DataReference](/python/api/azureml-core/azureml.data.data_reference.datareference):

| YAML-sleutel | Beschrijving |
| ----- | ----- |
| `datastore` | Het gegevensstore waarnaar moet worden verwezen. |
| `path_on_datastore` | Het relatieve pad in de back-opslag voor de gegevensverwijzing. |

Elke gegevensverwijzing is opgenomen in een sleutel. Het volgende YAML-fragment definieert bijvoorbeeld een gegevensverwijzing die is opgeslagen in de sleutel met de naam `employee_data` :

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

Stappen definiëren een rekenomgeving, samen met de bestanden die in de omgeving moeten worden uitgevoerd. Gebruik de sleutel om het type stap te `type` definiëren:

| Staptype | Beschrijving |
| ----- | ----- |
| `AdlaStep` | Voert een U-SQL-script uit met Azure Data Lake Analytics. Komt overeen met de [AdlaStep-klasse.](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.adlastep) |
| `AzureBatchStep` | Taken worden uitgevoerd met Azure Batch. Komt overeen met de [klasse AzureBatchStep.](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.azurebatchstep) |
| `DatabricsStep` | Voegt een Databricks-notebook, Python-script of JAR toe. Komt overeen met de [DatabricksStep-klasse.](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricksstep) |
| `DataTransferStep` | Brengt gegevens over tussen opslagopties. Komt overeen met de [klasse DataTransferStep.](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep) |
| `PythonScriptStep` | Voert een Python-script uit. Komt overeen met de [klasse PythonScriptStep.](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep) |
| `ParallelRunStep` | Hiermee wordt een Python-script uitgevoerd om grote hoeveelheden gegevens asynchroon en parallel te verwerken. Komt overeen met de [klasse ParallelRunStep.](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.parallel_run_step.parallelrunstep) |

### <a name="adla-step"></a>ADLA-stap

| YAML-sleutel | Beschrijving |
| ----- | ----- |
| `script_name` | De naam van het U-SQL-script (ten opzichte van de `source_directory` ). |
| `compute` | Het Azure Data Lake-rekendoel dat voor deze stap moet worden gebruikt. |
| `parameters` | [Parameters](#parameters) voor de pijplijn. |
| `inputs` | Invoer kan [InputPortBinding,](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding) [DataReference,](#data-reference) [PortDataReference,](/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference) [PipelineData,](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata) [Dataset,](/python/api/azureml-core/azureml.core.dataset%28class%29) [DatasetDefinition](/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition)of [PipelineDataset zijn.](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset) |
| `outputs` | Uitvoer kan [PipelineData of](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata) [OutputPortBinding zijn.](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding) |
| `source_directory` | Map die het script, de assemblies, enzovoort bevat. |
| `priority` | De prioriteitswaarde die moet worden gebruikt voor de huidige taak. |
| `params` | Woordenlijst met naam-waardeparen. |
| `degree_of_parallelism` | De mate van parallellelisme die moet worden gebruikt voor deze taak. |
| `runtime_version` | De runtimeversie van de Data Lake Analytics engine. |
| `allow_reuse` | Hiermee bepaalt u of de stap eerdere resultaten opnieuw moet gebruiken wanneer deze opnieuw worden uitgevoerd met dezelfde instellingen. |

Het volgende voorbeeld bevat de definitie van een ADLA-stap:

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
| `compute` | Het Azure Batch rekendoel dat voor deze stap moet worden gebruikt. |
| `inputs` | Invoer kan [InputPortBinding,](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding) [DataReference,](#data-reference) [PortDataReference,](/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference) [PipelineData,](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata) [Dataset,](/python/api/azureml-core/azureml.core.dataset%28class%29) [DatasetDefinition](/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition)of [PipelineDataset zijn.](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset) |
| `outputs` | Uitvoer kan [PipelineData of](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata) [OutputPortBinding zijn.](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding) |
| `source_directory` | Map die de binaire bestanden van de module, het uitvoerbare bestand, de assemblies, enzovoort bevat. |
| `executable` | Naam van de opdracht/het uitvoerbare bestand dat wordt uitgevoerd als onderdeel van deze taak. |
| `create_pool` | Booleaanse vlag om aan te geven of de pool moet worden aan te maken voordat de taak wordt uitgevoerd. |
| `delete_batch_job_after_finish` | Booleaanse vlag om aan te geven of de taak moet worden verwijderd uit het Batch-account nadat deze is voltooid. |
| `delete_batch_pool_after_finish` | Booleaanse vlag om aan te geven of de pool moet worden verwijderd nadat de taak is voltooien. |
| `is_positive_exit_code_failure` | Booleaanse vlag om aan te geven of de taak mislukt als de taak wordt afgesloten met een positieve code. |
| `vm_image_urn` | Als `create_pool` is `True` en VM `VirtualMachineConfiguration` gebruikt. |
| `pool_id` | De id van de pool waarin de taak wordt uitgevoerd. |
| `allow_reuse` | Hiermee bepaalt u of de stap eerdere resultaten opnieuw moet gebruiken wanneer deze opnieuw worden uitgevoerd met dezelfde instellingen. |

Het volgende voorbeeld bevat een definitie Azure Batch stap:

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

### <a name="databricks-step"></a>Databricks-stap

| YAML-sleutel | Beschrijving |
| ----- | ----- |
| `compute` | Het Azure Databricks rekendoel dat voor deze stap moet worden gebruikt. |
| `inputs` | Invoer kan [InputPortBinding,](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding) [DataReference,](#data-reference) [PortDataReference,](/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference) [PipelineData,](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata) [Dataset,](/python/api/azureml-core/azureml.core.dataset%28class%29) [DatasetDefinition](/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition)of [PipelineDataset zijn.](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset) |
| `outputs` | Uitvoer kan [PipelineData](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata) of [OutputPortBinding zijn.](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding) |
| `run_name` | De naam in Databricks voor deze run. |
| `source_directory` | Map die het script en andere bestanden bevat. |
| `num_workers` | Het statische aantal werksters voor het Databricks-runcluster. |
| `runconfig` | Het pad naar een `.runconfig` bestand. Dit bestand is een YAML-weergave van de [RunConfiguration-klasse.](/python/api/azureml-core/azureml.core.runconfiguration) Zie voor meer informatie over de structuur van [ dit bestandrunconfigschema.jsop](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json). |
| `allow_reuse` | Hiermee bepaalt u of de stap eerdere resultaten opnieuw moet gebruiken wanneer deze opnieuw worden uitgevoerd met dezelfde instellingen. |

Het volgende voorbeeld bevat een Databricks-stap:

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

### <a name="data-transfer-step"></a>Stap voor gegevensoverdracht

| YAML-sleutel | Beschrijving |
| ----- | ----- |
| `compute` | Het Azure Data Factory rekendoel dat voor deze stap moet worden gebruikt. |
| `source_data_reference` | Invoerverbinding die fungeert als de bron van gegevensoverdrachtsbewerkingen. Ondersteunde waarden zijn [InputPortBinding,](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding) [DataReference,](#data-reference) [PortDataReference,](/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference) [PipelineData,](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata) [Dataset,](/python/api/azureml-core/azureml.core.dataset%28class%29) [DatasetDefinition](/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition)of [PipelineDataset.](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset) |
| `destination_data_reference` | Invoerverbinding die fungeert als het doel van gegevensoverdrachtsbewerkingen. Ondersteunde waarden [zijn PipelineData](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata) en [OutputPortBinding.](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding) |
| `allow_reuse` | Hiermee bepaalt u of de stap eerdere resultaten opnieuw moet gebruiken wanneer deze opnieuw worden uitgevoerd met dezelfde instellingen. |

Het volgende voorbeeld bevat een gegevensoverdrachtstap:

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

### <a name="python-script-step"></a>Stap voor Python-script

| YAML-sleutel | Beschrijving |
| ----- | ----- |
| `inputs` | Invoer kan [InputPortBinding,](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding) [DataReference,](#data-reference) [PortDataReference,](/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference) [PipelineData,](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata) [Dataset,](/python/api/azureml-core/azureml.core.dataset%28class%29) [DatasetDefinition](/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition)of [PipelineDataset zijn.](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset) |
| `outputs` | Uitvoer kan [PipelineData of](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata) [OutputPortBinding zijn.](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding) |
| `script_name` | De naam van het Python-script (ten opzichte van `source_directory` ). |
| `source_directory` | Map met het script, de Conda-omgeving, enzovoort. |
| `runconfig` | Het pad naar een `.runconfig` bestand. Dit bestand is een YAML-weergave van de [RunConfiguration-klasse.](/python/api/azureml-core/azureml.core.runconfiguration) Zie voor meer informatie over de structuur van [ dit bestandrunconfig.jsop](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json). |
| `allow_reuse` | Hiermee bepaalt u of de stap eerdere resultaten opnieuw moet gebruiken wanneer deze opnieuw worden uitgevoerd met dezelfde instellingen. |

Het volgende voorbeeld bevat een Python-scriptstap:

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

### <a name="parallel-run-step"></a>Parallelle run-stap

| YAML-sleutel | Beschrijving |
| ----- | ----- |
| `inputs` | Invoer kan [Gegevensset,](/python/api/azureml-core/azureml.core.dataset%28class%29) [GegevenssetDefinition](/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition)of [PipelineDataset zijn.](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset) |
| `outputs` | Uitvoer kan [PipelineData](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata) of [OutputPortBinding zijn.](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding) |
| `script_name` | De naam van het Python-script (ten opzichte van `source_directory` ). |
| `source_directory` | Map die het script, de Conda-omgeving, enzovoort bevat. |
| `parallel_run_config` | Het pad naar een `parallel_run_config.yml` bestand. Dit bestand is een YAML-weergave van de [klasse ParallelRunConfig.](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.parallelrunconfig) |
| `allow_reuse` | Hiermee bepaalt u of de stap eerdere resultaten opnieuw moet gebruiken wanneer deze opnieuw worden uitgevoerd met dezelfde instellingen. |

Het volgende voorbeeld bevat een parallelle run-stap:

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

### <a name="pipeline-with-multiple-steps"></a>Pijplijn met meerdere stappen 

| YAML-sleutel | Beschrijving |
| ----- | ----- |
| `steps` | Volgorde van een of meer PipelineStep-definities. Houd er rekening `destination` mee dat de sleutels van de ene stap de sleutels worden voor de van de volgende `outputs` `source` `inputs` stap.| 

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

Bij het definiëren van het schema voor een pijplijn kan deze worden geactiveerd door een gegevensstore of worden terugkerende op basis van een tijdsinterval. Hier volgen de sleutels die worden gebruikt om een planning te definiëren:

| YAML-sleutel | Beschrijving |
| ----- | ----- |
| `description` | Een beschrijving van de planning. |
| `recurrence` | Bevat terugkeerpatrooninstellingen als de planning terugkerend is. |
| `pipeline_parameters` | Parameters die vereist zijn voor de pijplijn. |
| `wait_for_provisioning` | Of moet worden gewacht tot het inrichten van de planning is voltooid. |
| `wait_timeout` | Het aantal seconden dat moet worden gewacht voordat er een time-out wordt uitgevoerd. |
| `datastore_name` | Het gegevensopslag dat moet worden gecontroleerd op gewijzigde/toegevoegde blobs. |
| `polling_interval` | Hoe lang, in minuten, tussen polling voor gewijzigde/toegevoegde blobs. Standaardwaarde: 5 minuten. Alleen ondersteund voor gegevensstoreschema's. |
| `data_path_parameter_name` | De naam van de pijplijnparameter voor het gegevenspad die moet worden ingesteld met het gewijzigde blobpad. Alleen ondersteund voor gegevensstoreschema's. |
| `continue_on_step_failure` | Of de uitvoering van andere stappen in de ingediende PipelineRun moet worden voortgezet als een stap mislukt. Indien opgegeven, overschrijven de instelling `continue_on_step_failure` van de pijplijn.
| `path_on_datastore` | Optioneel. Het pad naar het gegevensopslag dat moet worden gecontroleerd op gewijzigde/toegevoegde blobs. Het pad is onder de container voor de gegevensstore, dus het werkelijke pad dat door de planning wordt bewaakt, is container/ `path_on_datastore` . Als er geen is, wordt de gegevensstore-container bewaakt. Toevoegingen/wijzigingen die zijn aangebracht in een submap van de `path_on_datastore` worden niet bewaakt. Alleen ondersteund voor gegevensstoreschema's. |

Het volgende voorbeeld bevat de definitie voor een schema dat door een gegevensstore wordt geactiveerd:

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

Gebruik bij het definiëren van **een terugkerend schema** de volgende sleutels onder `recurrence` :

| YAML-sleutel | Beschrijving |
| ----- | ----- |
| `frequency` | Hoe vaak het schema wordt recurseert. Geldige waarden zijn `"Minute"` , , , of `"Hour"` `"Day"` `"Week"` `"Month"` . |
| `interval` | Hoe vaak het schema wordt gebruikt. De waarde voor een geheel getal is het aantal tijdseenheden dat moet worden gewacht totdat het schema opnieuw wordt in gebruik. |
| `start_time` | De begintijd voor de planning. De tekenreeksindeling van de waarde is `YYYY-MM-DDThh:mm:ss` . Als er geen begintijd is opgegeven, wordt de eerste workload onmiddellijk uitgevoerd en worden toekomstige workloads uitgevoerd op basis van de planning. Als de begintijd in het verleden ligt, wordt de eerste workload uitgevoerd op de volgende berekende run time. |
| `time_zone` | De tijdzone voor de begintijd. Als er geen tijdzone is opgegeven, wordt UTC gebruikt. |
| `hours` | Als of is, kunt u een of meer gehele getallen van 0 tot 23 opgeven, gescheiden door komma's, als de uren van de dag waarop de `frequency` `"Day"` `"Week"` pijplijn moet worden uitgevoerd. Alleen `time_of_day` of en kunnen worden `hours` `minutes` gebruikt. |
| `minutes` | Als of is, kunt u een of meer gehele getallen van 0 tot 59 opgeven, gescheiden door komma's, als de minuten van het uur waarop de `frequency` `"Day"` `"Week"` pijplijn moet worden uitgevoerd. Alleen `time_of_day` of en kunnen worden `hours` `minutes` gebruikt. |
| `time_of_day` | Als `frequency` of `"Day"` `"Week"` is, kunt u een tijdstip van de dag opgeven voor de planning die moet worden uitgevoerd. De tekenreeksindeling van de waarde is `hh:mm` . Alleen `time_of_day` of en kunnen worden `hours` `minutes` gebruikt. |
| `week_days` | Als `frequency` `"Week"` is, kunt u een of meer dagen opgeven, gescheiden door komma's, wanneer de planning moet worden uitgevoerd. Geldige waarden zijn `"Monday"` , , , , , en `"Tuesday"` `"Wednesday"` `"Thursday"` `"Friday"` `"Saturday"` `"Sunday"` . |

Het volgende voorbeeld bevat de definitie voor een terugkerend schema:

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

Meer informatie over [het gebruik van de CLI-extensie voor Azure Machine Learning](reference-azure-machine-learning-cli.md).
