---
title: Azure Machine Learning als Event Grid bron
description: Hierin worden de eigenschappen beschreven die worden gegeven voor Machine Learning-werkruimte gebeurtenissen met Azure Event Grid
ms.topic: conceptual
ms.date: 02/11/2021
ms.openlocfilehash: e47c2137840e21eab73906d42b1e1536422f872d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100363386"
---
# <a name="azure-machine-learning-as-an-event-grid-source"></a>Azure Machine Learning als Event Grid bron

In dit artikel vindt u de eigenschappen en het schema voor machine learning werkruimte gebeurtenissen. Zie [Azure Event grid-gebeurtenis schema](event-schema.md)voor een inleiding tot gebeurtenis schema's.


## <a name="available-event-types"></a>Beschik bare gebeurtenis typen

Azure Machine Learning worden de volgende gebeurtenis typen meeverzonden:

| Gebeurtenistype | Beschrijving |
| ---------- | ----------- |
| Micro soft. MachineLearningServices. ModelRegistered | Deze gebeurtenis treedt op wanneer een nieuw model of model versie is geregistreerd. |
| Micro soft. MachineLearningServices. ModelDeployed | Deze gebeurtenis treedt op wanneer model (sen) met succes is geïmplementeerd op een eind punt. |
| Micro soft. MachineLearningServices. RunCompleted | Deze gebeurtenis treedt op wanneer een uitvoering is voltooid. |
| Micro soft. MachineLearningServices. DatasetDriftDetected | Deze gebeurtenis treedt op wanneer drift door een DataSet-drijf monitor wordt gedetecteerd. |
| Micro soft. MachineLearningServices. RunStatusChanged | Deze gebeurtenis treedt op wanneer de status van een uitvoering wordt gewijzigd. |

## <a name="example-events"></a>Voorbeeld gebeurtenissen

Wanneer een gebeurtenis wordt geactiveerd, verzendt de Event Grid-Service gegevens over die gebeurtenis om het eind punt te abonneren. Deze sectie bevat een voorbeeld van hoe de gegevens voor elke gebeurtenis eruitzien.

# <a name="event-grid-event-schema"></a>[Event Grid-gebeurtenisschema](#tab/event-grid-event-schema)

### <a name="microsoftmachinelearningservicesmodelregistered-event"></a>Micro soft. MachineLearningServices. ModelRegistered-gebeurtenis

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearningServices/workspaces/{workspace-name}",
  "subject": "models/sklearn_regression_model:20",
  "eventType": "Microsoft.MachineLearningServices.ModelRegistered",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "ModelName": "sklearn_regression_model",
    "ModelVersion": 20,
    "ModelTags": {
        "area": "diabetes",
        "type": "regression"
    },
    "ModelProperties": {
        "type": "test"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="microsoftmachinelearningservicesmodeldeployed-event"></a>Micro soft. MachineLearningServices. ModelDeployed-gebeurtenis

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearningServices/workspaces/{workspace-name}",
  "subject": "endpoints/my-sklearn-service",
  "eventType": "Microsoft.MachineLearningServices.ModelDeployed",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "ServiceName": "my-sklearn-service",
    "ServiceComputeType": "ACI",
    "ModelIds": "sklearn_regression_model:1,sklearn_regression_model:2",
    "ServiceTags": {
        "area": "diabetes",
        "type": "regression"
    },
    "ServiceProperties": {
        "type": "test"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="microsoftmachinelearningservicesruncompleted-event"></a>Micro soft. MachineLearningServices. RunCompleted-gebeurtenis

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearningServices/workspaces/{workspace-name}",
  "subject": "experiments/0fa9dfaa-cba3-4fa7-b590-23e48548f5c1/runs/AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5",
  "eventType": "Microsoft.MachineLearningServices.RunCompleted",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "experimentId": "0fa9dfaa-cba3-4fa7-b590-23e48548f5c1",
    "experimentName": "automl-local-regression",
    "runId": "AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5",
    "runType": null,
    "runTags": {},
    "runProperties": {
        "runTemplate": "automl_child",
        "pipeline_id": "5adc0a4fe02504a586f09a4fcbb241f9a4012062",
        "pipeline_spec": "{\"objects\": [{\"class_name\": \"StandardScaler\", \"module\": \"sklearn.preprocessing\", \"param_args\": [], \"param_kwargs\": {\"with_mean\": true, \"with_std\": false}, \"prepared_kwargs\": {}, \"spec_class\": \"preproc\"}, {\"class_name\": \"LassoLars\", \"module\": \"sklearn.linear_model\", \"param_args\": [], \"param_kwargs\": {\"alpha\": 0.001, \"normalize\": true}, \"prepared_kwargs\": {}, \"spec_class\": \"sklearn\"}], \"pipeline_id\": \"5adc0a4fe02504a586f09a4fcbb241f9a4012062\"}",
        "training_percent": "100",
        "predicted_cost": "0.062226144097381045",
        "iteration": "5",
        "run_template": "automl_child",
        "run_preprocessor": "StandardScalerWrapper",
        "run_algorithm": "LassoLars",
        "conda_env_data_location": "aml://artifact/ExperimentRun/dcid.AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5/outputs/conda_env_v_1_0_0.yml",
        "model_name": "AutoMLad912b2d65",
        "scoring_data_location": "aml://artifact/ExperimentRun/dcid.AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5/outputs/scoring_file_v_1_0_0.py",
        "model_data_location": "aml://artifact/ExperimentRun/dcid.AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5/outputs/model.pkl"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="microsoftmachinelearningservicesdatasetdriftdetected-event"></a>Micro soft. MachineLearningServices. DatasetDriftDetected-gebeurtenis

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearningServices/workspaces/{workspace-name}",
  "subject": "datadrifts/{}/runs/{}",
  "eventType": "Microsoft.MachineLearningServices.DatasetDriftDetected",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "DataDriftId": "01d29aa4-e6a4-470a-9ef3-66660d21f8ef",
    "DataDriftName": "myDriftMonitor",
    "RunId": "01d29aa4-e6a4-470a-9ef3-66660d21f8ef_1571590300380",
    "BaseDatasetId": "3c56d136-0f64-4657-a0e8-5162089a88a3",
    "TargetDatasetId": "d7e74d2e-c972-4266-b5fb-6c9c182d2a74",
    "DriftCoefficient": 0.83503490684792081,
    "StartTime": "2019-07-04T00:00:00+00:00",
    "EndTime": "2019-07-05T00:00:00+00:00"
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="microsoftmachinelearningservicesrunstatuschanged-event"></a>Micro soft. MachineLearningServices. RunStatusChanged-gebeurtenis

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearningServices/workspaces/{workspace-name}",
  "subject": "experiments/0fa9dfaa-cba3-4fa7-b590-23e48548f5c1/runs/AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5",
  "eventType": "Microsoft.MachineLearningServices.RunStatusChanged",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "experimentId": "0fa9dfaa-cba3-4fa7-b590-23e48548f5c1",
    "experimentName": "automl-local-regression",
    "runId": "AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5",
    "runType": null,
    "runTags": {},
    "runProperties": {
        "runTemplate": "automl_child",
        "pipeline_id": "5adc0a4fe02504a586f09a4fcbb241f9a4012062",
        "pipeline_spec": "{\"objects\": [{\"class_name\": \"StandardScaler\", \"module\": \"sklearn.preprocessing\", \"param_args\": [], \"param_kwargs\": {\"with_mean\": true, \"with_std\": false}, \"prepared_kwargs\": {}, \"spec_class\": \"preproc\"}, {\"class_name\": \"LassoLars\", \"module\": \"sklearn.linear_model\", \"param_args\": [], \"param_kwargs\": {\"alpha\": 0.001, \"normalize\": true}, \"prepared_kwargs\": {}, \"spec_class\": \"sklearn\"}], \"pipeline_id\": \"5adc0a4fe02504a586f09a4fcbb241f9a4012062\"}",
        "training_percent": "100",
        "predicted_cost": "0.062226144097381045",
        "iteration": "5",
        "run_template": "automl_child",
        "run_preprocessor": "StandardScalerWrapper",
        "run_algorithm": "LassoLars",
        "conda_env_data_location": "aml://artifact/ExperimentRun/dcid.AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5/outputs/conda_env_v_1_0_0.yml",
        "model_name": "AutoMLad912b2d65",
        "scoring_data_location": "aml://artifact/ExperimentRun/dcid.AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5/outputs/scoring_file_v_1_0_0.py",
        "model_data_location": "aml://artifact/ExperimentRun/dcid.AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5/outputs/model.pkl"
    },
   "runStatus": "failed"
   },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

# <a name="cloud-event-schema"></a>[Cloudgebeurtenisschema](#tab/cloud-event-schema)

### <a name="microsoftmachinelearningservicesmodelregistered-event"></a>Micro soft. MachineLearningServices. ModelRegistered-gebeurtenis

```json
[{
  "source": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearningServices/workspaces/{workspace-name}",
  "subject": "models/sklearn_regression_model:20",
  "type": "Microsoft.MachineLearningServices.ModelRegistered",
  "time": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "ModelName": "sklearn_regression_model",
    "ModelVersion": 20,
    "ModelTags": {
        "area": "diabetes",
        "type": "regression"
    },
    "ModelProperties": {
        "type": "test"
    }
  },
  "specversion": "1.0"
}]
```

### <a name="microsoftmachinelearningservicesmodeldeployed-event"></a>Micro soft. MachineLearningServices. ModelDeployed-gebeurtenis

```json
[{
  "source": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearningServices/workspaces/{workspace-name}",
  "subject": "endpoints/my-sklearn-service",
  "type": "Microsoft.MachineLearningServices.ModelDeployed",
  "time": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "ServiceName": "my-sklearn-service",
    "ServiceComputeType": "ACI",
    "ModelIds": "sklearn_regression_model:1,sklearn_regression_model:2",
    "ServiceTags": {
        "area": "diabetes",
        "type": "regression"
    },
    "ServiceProperties": {
        "type": "test"
    }
  },
  "specversion": "1.0"
}]
```

### <a name="microsoftmachinelearningservicesruncompleted-event"></a>Micro soft. MachineLearningServices. RunCompleted-gebeurtenis

```json
[{
  "source": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearningServices/workspaces/{workspace-name}",
  "subject": "experiments/0fa9dfaa-cba3-4fa7-b590-23e48548f5c1/runs/AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5",
  "type": "Microsoft.MachineLearningServices.RunCompleted",
  "time": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "experimentId": "0fa9dfaa-cba3-4fa7-b590-23e48548f5c1",
    "experimentName": "automl-local-regression",
    "runId": "AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5",
    "runType": null,
    "runTags": {},
    "runProperties": {
        "runTemplate": "automl_child",
        "pipeline_id": "5adc0a4fe02504a586f09a4fcbb241f9a4012062",
        "pipeline_spec": "{\"objects\": [{\"class_name\": \"StandardScaler\", \"module\": \"sklearn.preprocessing\", \"param_args\": [], \"param_kwargs\": {\"with_mean\": true, \"with_std\": false}, \"prepared_kwargs\": {}, \"spec_class\": \"preproc\"}, {\"class_name\": \"LassoLars\", \"module\": \"sklearn.linear_model\", \"param_args\": [], \"param_kwargs\": {\"alpha\": 0.001, \"normalize\": true}, \"prepared_kwargs\": {}, \"spec_class\": \"sklearn\"}], \"pipeline_id\": \"5adc0a4fe02504a586f09a4fcbb241f9a4012062\"}",
        "training_percent": "100",
        "predicted_cost": "0.062226144097381045",
        "iteration": "5",
        "run_template": "automl_child",
        "run_preprocessor": "StandardScalerWrapper",
        "run_algorithm": "LassoLars",
        "conda_env_data_location": "aml://artifact/ExperimentRun/dcid.AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5/outputs/conda_env_v_1_0_0.yml",
        "model_name": "AutoMLad912b2d65",
        "scoring_data_location": "aml://artifact/ExperimentRun/dcid.AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5/outputs/scoring_file_v_1_0_0.py",
        "model_data_location": "aml://artifact/ExperimentRun/dcid.AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5/outputs/model.pkl"
    }
  },
  "specversion": "1.0"
}]
```

### <a name="microsoftmachinelearningservicesdatasetdriftdetected-event"></a>Micro soft. MachineLearningServices. DatasetDriftDetected-gebeurtenis

```json
[{
  "source": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearningServices/workspaces/{workspace-name}",
  "subject": "datadrifts/{}/runs/{}",
  "type": "Microsoft.MachineLearningServices.DatasetDriftDetected",
  "time": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "DataDriftId": "01d29aa4-e6a4-470a-9ef3-66660d21f8ef",
    "DataDriftName": "myDriftMonitor",
    "RunId": "01d29aa4-e6a4-470a-9ef3-66660d21f8ef_1571590300380",
    "BaseDatasetId": "3c56d136-0f64-4657-a0e8-5162089a88a3",
    "TargetDatasetId": "d7e74d2e-c972-4266-b5fb-6c9c182d2a74",
    "DriftCoefficient": 0.83503490684792081,
    "StartTime": "2019-07-04T00:00:00+00:00",
    "EndTime": "2019-07-05T00:00:00+00:00"
  },
  "specversion": "1.0"
}]
```

### <a name="microsoftmachinelearningservicesrunstatuschanged-event"></a>Micro soft. MachineLearningServices. RunStatusChanged-gebeurtenis

```json
[{
  "source": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearningServices/workspaces/{workspace-name}",
  "subject": "experiments/0fa9dfaa-cba3-4fa7-b590-23e48548f5c1/runs/AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5",
  "type": "Microsoft.MachineLearningServices.RunStatusChanged",
  "time": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "experimentId": "0fa9dfaa-cba3-4fa7-b590-23e48548f5c1",
    "experimentName": "automl-local-regression",
    "runId": "AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5",
    "runType": null,
    "runTags": {},
    "runProperties": {
        "runTemplate": "automl_child",
        "pipeline_id": "5adc0a4fe02504a586f09a4fcbb241f9a4012062",
        "pipeline_spec": "{\"objects\": [{\"class_name\": \"StandardScaler\", \"module\": \"sklearn.preprocessing\", \"param_args\": [], \"param_kwargs\": {\"with_mean\": true, \"with_std\": false}, \"prepared_kwargs\": {}, \"spec_class\": \"preproc\"}, {\"class_name\": \"LassoLars\", \"module\": \"sklearn.linear_model\", \"param_args\": [], \"param_kwargs\": {\"alpha\": 0.001, \"normalize\": true}, \"prepared_kwargs\": {}, \"spec_class\": \"sklearn\"}], \"pipeline_id\": \"5adc0a4fe02504a586f09a4fcbb241f9a4012062\"}",
        "training_percent": "100",
        "predicted_cost": "0.062226144097381045",
        "iteration": "5",
        "run_template": "automl_child",
        "run_preprocessor": "StandardScalerWrapper",
        "run_algorithm": "LassoLars",
        "conda_env_data_location": "aml://artifact/ExperimentRun/dcid.AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5/outputs/conda_env_v_1_0_0.yml",
        "model_name": "AutoMLad912b2d65",
        "scoring_data_location": "aml://artifact/ExperimentRun/dcid.AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5/outputs/scoring_file_v_1_0_0.py",
        "model_data_location": "aml://artifact/ExperimentRun/dcid.AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5/outputs/model.pkl"
    },
   "runStatus": "failed"
   },
  "specversion": "1.0"
}]
```

---

### <a name="event-properties"></a>Gebeurtenis eigenschappen

# <a name="event-grid-event-schema"></a>[Event Grid-gebeurtenisschema](#tab/event-grid-event-schema)

Een gebeurtenis heeft de volgende gegevens op het hoogste niveau:

| Eigenschap | Type | Description |
| -------- | ---- | ----------- |
| `topic` | tekenreeks | Volledige bronpad naar de bron van de gebeurtenis. Dit veld is niet beschrijfbaar. Event Grid biedt deze waarde. |
| `subject` | tekenreeks | Het door de uitgever gedefinieerde pad naar het gebeurtenisonderwerp. |
| `eventType` | tekenreeks | Een van de geregistreerde gebeurtenistypen voor deze gebeurtenisbron. |
| `eventTime` | tekenreeks | Het tijdstip waarop de gebeurtenis is gegenereerd op basis van de UTC-tijd van de provider. |
| `id` | tekenreeks | De unieke id voor de gebeurtenis. |
| `data` | object | Gebeurtenis gegevens van Blob-opslag. |
| `dataVersion` | tekenreeks | De schemaversie van het gegevensobject. De uitgever definieert de schemaversie. |
| `metadataVersion` | tekenreeks | De schemaversie van de metagegevens van de gebeurtenis. Event Grid definieert het schema voor de eigenschappen op het hoogste niveau. Event Grid biedt deze waarde. |

# <a name="cloud-event-schema"></a>[Cloudgebeurtenisschema](#tab/cloud-event-schema)

Een gebeurtenis heeft de volgende gegevens op het hoogste niveau:

| Eigenschap | Type | Description |
| -------- | ---- | ----------- |
| `source` | tekenreeks | Volledige bronpad naar de bron van de gebeurtenis. Dit veld is niet beschrijfbaar. Event Grid biedt deze waarde. |
| `subject` | tekenreeks | Het door de uitgever gedefinieerde pad naar het gebeurtenisonderwerp. |
| `type` | tekenreeks | Een van de geregistreerde gebeurtenistypen voor deze gebeurtenisbron. |
| `time` | tekenreeks | Het tijdstip waarop de gebeurtenis is gegenereerd op basis van de UTC-tijd van de provider. |
| `id` | tekenreeks | De unieke id voor de gebeurtenis. |
| `data` | object | Gebeurtenis gegevens van Blob-opslag. |
| `specversion` | tekenreeks | CloudEvents-schema specificatie versie. |

---


Het gegevens object heeft de volgende eigenschappen voor elk gebeurtenis type:

### <a name="microsoftmachinelearningservicesmodelregistered"></a>Micro soft. MachineLearningServices. ModelRegistered

| Eigenschap | Type | Description |
| -------- | ---- | ----------- |
| `ModelName` | tekenreeks | De naam van het model dat is geregistreerd. |
| `ModelVersion` | tekenreeks | De versie van het model dat is geregistreerd. |
| `ModelTags` | object | De tags van het model dat is geregistreerd. |
| `ModelProperties` | object | De eigenschappen van het model dat is geregistreerd. |

### <a name="microsoftmachinelearningservicesmodeldeployed"></a>Micro soft. MachineLearningServices. ModelDeployed

| Eigenschap | Type | Description |
| -------- | ---- | ----------- |
| `ServiceName` | tekenreeks | De naam van de geïmplementeerde service. |
| `ServiceComputeType` | tekenreeks | Het reken type (bijvoorbeeld ACI, AKS) van de geïmplementeerde service. |
  | `ModelIds` | tekenreeks | Een door komma's gescheiden lijst met model-Id's. De Id's van de modellen die in de service zijn geïmplementeerd. |
| `ServiceTags` | object | De tags van de geïmplementeerde service. |
| `ServiceProperties` | object | De eigenschappen van de geïmplementeerde service. |

### <a name="microsoftmachinelearningservicesruncompleted"></a>Micro soft. MachineLearningServices. RunCompleted

| Eigenschap | Type | Description |
| -------- | ---- | ----------- |
| `experimentId` | tekenreeks | De ID van het experiment waarvan de uitvoering deel uitmaakt. |
| `experimentName` | tekenreeks | De naam van het experiment waarvan de uitvoering deel uitmaakt. |
| `runId` | tekenreeks | De ID van de uitvoering die is voltooid. |
| `runType` | tekenreeks | Het type uitvoering van de voltooide uitvoering. |
| `runTags` | object | De labels van de voltooide uitvoering. |
| `runProperties` | object | De eigenschappen van de voltooide uitvoering. |

### <a name="microsoftmachinelearningservicesdatasetdriftdetected"></a>Micro soft. MachineLearningServices. DatasetDriftDetected

| Eigenschap | Type | Description |
| -------- | ---- | ----------- |
| `DataDriftId` | tekenreeks | De ID van de gegevensdrijf monitor die de gebeurtenis heeft geactiveerd. |
| `DataDriftName` | tekenreeks | De naam van de gegevensdrijf monitor die de gebeurtenis heeft geactiveerd. |
| `RunId` | tekenreeks | De ID van de uitvoering die gegevens drift heeft gedetecteerd. |
| `BaseDatasetId` | tekenreeks | De ID van de basis gegevensset die wordt gebruikt voor het detecteren van drift. |
| `TargetDatasetId` | tekenreeks | De ID van de doel gegevensset die wordt gebruikt voor het detecteren van drift. |
| `DriftCoefficient` | double | Het coëfficiënt resultaat dat de gebeurtenis heeft geactiveerd. |
| `StartTime` | datum/tijd | De begin tijd van de tijd reeks voor de doel gegevensset die heeft geleid tot de drift-detectie.  |
| `EndTime` | datum/tijd | De eind tijd van de tijd reeks van de doel gegevensset die heeft geleid tot de drift-detectie. |

### <a name="microsoftmachinelearningservicesrunstatuschanged"></a>Micro soft. MachineLearningServices. RunStatusChanged

| Eigenschap | Type | Description |
| -------- | ---- | ----------- |
| `experimentId` | tekenreeks | De ID van het experiment waarvan de uitvoering deel uitmaakt. |
| `experimentName` | tekenreeks | De naam van het experiment waarvan de uitvoering deel uitmaakt. |
| `runId` | tekenreeks | De ID van de uitvoering die is voltooid. |
| `runType` | tekenreeks | Het type uitvoering van de voltooide uitvoering. |
| `runTags` | object | De labels van de voltooide uitvoering. |
| `runProperties` | object | De eigenschappen van de voltooide uitvoering. |
| `runStatus` | tekenreeks | De status van de uitvoering. |

## <a name="tutorials-and-how-tos"></a>Zelfstudies en handleidingen
| Titel | Beschrijving |
| ----- | ----- |
| [Azure Machine Learning gebeurtenissen gebruiken](../machine-learning/how-to-use-event-grid.md) | Overzicht van het integreren van Azure Machine Learning met Event Grid. |

## <a name="next-steps"></a>Volgende stappen

* Zie [Wat is Event Grid?](overview.md) voor een inleiding tot Azure Event Grid.
* Zie [Event grid-abonnements schema](subscription-creation-schema.md) voor meer informatie over het maken van een Azure Event grid-abonnement
* Zie voor een inleiding tot het gebruik van Azure Event Grid met Azure Machine Learning [Azure machine learning gebeurtenissen gebruiken](../machine-learning/how-to-use-event-grid.md)
* Zie [gebeurtenis gerichte machine learning werk stromen maken](../machine-learning/how-to-use-event-grid.md) voor een voor beeld van het gebruik van Azure Event Grid met Azure machine learning.
