---
title: MLflow-tracering voor Azure Databricks ML-experimenten
titleSuffix: Azure Machine Learning
description: Stel MLflow in met Azure Machine Learning om metrische gegevens en artefacten van Azure Databricks ML-experimenten te loggen.
services: machine-learning
author: nibaccam
ms.author: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.reviewer: nibaccam
ms.date: 09/22/2020
ms.topic: how-to
ms.custom: devx-track-python
ms.openlocfilehash: c1bef16d89a22e7df43e1f473697b4577d080d4c
ms.sourcegitcommit: 5ce88326f2b02fda54dad05df94cf0b440da284b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107888166"
---
# <a name="track-azure-databricks-ml-experiments-with-mlflow-and-azure-machine-learning-preview"></a>ML Azure Databricks experimenten bijhouden met MLflow en Azure Machine Learning (preview)

In dit artikel leert u hoe u de tracerings-URI en logboekregistratie-API van MLflow, gezamenlijk bekend als [MLflow Tracking,](https://mlflow.org/docs/latest/quickstart.html#using-the-tracking-api)kunt inschakelen om uw Azure Databricks(ADB)-experimenten, MLflow en Azure Machine Learning.

[MLflow](https://www.mlflow.org) is een opensource-bibliotheek voor het beheren van de levenscyclus van uw machine learning experimenten. MLFlow Tracking is een onderdeel van MLflow dat de metrische gegevens en modelartefacten van uw trainingsrun registreert en bij houdt. Meer informatie over [Azure Databricks en MLflow.](/azure/databricks/applications/mlflow/) 

Zie [Experimentuit runs bijhouden met MLflow en Azure Machine Learning](how-to-use-mlflow.md) voor aanvullende integraties van MLflow en Azure Machine Learning-functionaliteit.

>[!NOTE]
> Als open source wordt MLflow regelmatig gewijzigd. Als zodanig moet de functionaliteit die beschikbaar wordt gesteld via de Azure Machine Learning en MLflow-integratie worden beschouwd als een preview-versie en niet volledig worden ondersteund door Microsoft.

> [!TIP]
> De informatie in dit document is voornamelijk bedoeld voor gegevenswetenschappers en ontwikkelaars die het modeltrainingsproces willen bewaken. Als u een beheerder bent die geïnteresseerd bent in het bewaken van resourcegebruik en gebeurtenissen van Azure Machine Learning, zoals quota, voltooide trainingsuitvoer of voltooide modelimplementaties, zie Bewaking van [Azure Machine Learning](monitor-azure-machine-learning.md).

## <a name="prerequisites"></a>Vereisten

* Installeer het `azureml-mlflow`-pakket. 
    * Met dit pakket wordt automatisch de `azureml-core` [Python-SDK Azure Machine Learning,](/python/api/overview/azure/ml/install)waarmee MLflow toegang kan krijgen tot uw werkruimte.
* Een [Azure Databricks werkruimte en cluster](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal).
* [Maak een Azure Machine Learning-werkruimte](how-to-manage-workspace.md).
    * Kijk welke [toegangsmachtigingen u nodig hebt om uw MLflow-bewerkingen uit te voeren met uw werkruimte](how-to-assign-roles.md#mlflow-operations).

## <a name="track-azure-databricks-runs"></a>De Azure Databricks bijhouden

Met MLflow Tracking met Azure Machine Learning kunt u de vastgelegde metrische gegevens en artefacten van uw Azure Databricks worden uitgevoerd in uw: 

* Azure Databricks werkruimte.
* Azure Machine Learning-werkruimte

Nadat u uw werkruimte en cluster Azure Databricks, 

1. Installeer de *bibliotheek azureml-mlflow* vanuit PyPi om ervoor te zorgen dat uw cluster toegang heeft tot de benodigde functies en klassen.

1. Stel uw experimentnotenotenote in.

1. Verbind uw Azure Databricks werkruimte en Azure Machine Learning werkruimte.

Aanvullende details voor deze stappen vindt u in de volgende secties, zodat u uw MLflow-experimenten kunt uitvoeren met Azure Databricks. 

## <a name="install-libraries"></a>Bibliotheken installeren

Als u bibliotheken op uw cluster wilt installeren, gaat u naar **het tabblad Bibliotheken** en **selecteert u Nieuwe installeren.**

 ![mlflow met azure databricks](./media/how-to-use-mlflow-azure-databricks/azure-databricks-cluster-libraries.png)

Typ  azureml-mlflow in het veld Pakket en selecteer installeren. Herhaal deze stap indien nodig om andere extra pakketten te installeren in uw cluster voor uw experiment.

 ![Mlflow-bibliotheek installeren in Azure DB](./media/how-to-use-mlflow-azure-databricks/install-libraries.png)

## <a name="set-up-your-notebook"></a>Uw notebook instellen 

Zodra uw ADB-cluster is ingesteld, 
1. Selecteer **Werkruimten** in het linkernavigatievenster. 
1. Vouw de vervolgkeuzelijst Werkruimten uit en selecteer **Importeren**
1. Sleep uw experimentnote om uw ADB-werkruimte te importeren, of blader naar zoeken.
1. Selecteer **Importeren**. Uw experimentnotenoteboek wordt automatisch geopend.
1. Selecteer linksboven in de notebooktitel het cluster dat u wilt koppelen aan uw experimentnotenote. 

## <a name="connect-your-azure-databricks-and-azure-machine-learning-workspaces"></a>Uw werkruimten Azure Databricks en Azure Machine Learning verbinden

Door uw ADB-werkruimte aan uw Azure Machine Learning te koppelen, kunt u uw experimentgegevens bijhouden in Azure Machine Learning werkruimte.

Als u uw ADB-werkruimte wilt koppelen aan een nieuwe of bestaande Azure Machine Learning werkruimte, 
1. Meld u aan bij de [Azure-portal](https://ms.portal.azure.com).
1. Navigeer naar de pagina Overzicht van **uw ADB-werkruimte.**
1. Selecteer **rechtsonder Azure Machine Learning knop** Koppeling naar werkruimte. 

 ![Azure DB en werkruimten Azure Machine Learning koppelen](./media/how-to-use-mlflow-azure-databricks/link-workspaces.png)

## <a name="mlflow-tracking-in-your-workspaces"></a>MLflow-tracering in uw werkruimten

Nadat u uw werkruimte hebt ingesteld, wordt MLflow Tracking automatisch ingesteld om op alle volgende plaatsen te worden bij te houden:

* De gekoppelde Azure Machine Learning werkruimte.
* Uw oorspronkelijke ADB-werkruimte. 

Al uw experimenten komen terecht in de beheerde Azure Machine Learning-traceringsservice.

De volgende code moet in uw experimentnoteboek staan om uw gekoppelde Azure Machine Learning op te halen. 

Deze code, 

*  Haalt de details van uw Azure-abonnement op om uw werkruimte Azure Machine Learning maken. 

* Stel dat u een bestaande resourcegroep hebt en Azure Machine Learning werkruimte, anders kunt u [deze maken.](how-to-manage-workspace.md) 

* Hiermee stelt u de naam van het experiment. De `user_name` hier is consistent met de die is gekoppeld aan de Azure Databricks `user_name` werkruimte.

```python
import mlflow
import mlflow.azureml
import azureml.mlflow
import azureml.core

from azureml.core import Workspace

subscription_id = 'subscription_id'

# Azure Machine Learning resource group NOT the managed resource group
resource_group = 'resource_group_name' 

#Azure Machine Learning workspace name, NOT Azure Databricks workspace
workspace_name = 'workspace_name'  

# Instantiate Azure Machine Learning workspace
ws = Workspace.get(name=workspace_name,
                   subscription_id=subscription_id,
                   resource_group=resource_group)

#Set MLflow experiment. 
experimentName = "/Users/{user_name}/{experiment_folder}/{experiment_name}" 
mlflow.set_experiment(experimentName) 

```

### <a name="set-mlflow-tracking-to-only-track-in-your-azure-machine-learning-workspace"></a>MLflow-tracering instellen op alleen bijhouden in Azure Machine Learning werkruimte

Als u uw bij te houden experimenten liever op een centrale locatie  beheert, kunt u MLflow-tracering zo instellen dat deze alleen wordt bij te houden in Azure Machine Learning werkruimte. 

Neem de volgende code op in uw script:

```python
uri = ws.get_mlflow_tracking_uri()
mlflow.set_tracking_uri(uri)
```

Importeer in uw trainingsscript om de API's voor MLflow-logboekregistratie te gebruiken `mlflow` en begin met het vastleggen van de metrische gegevens van uw run. In het volgende voorbeeld wordt de metrische gegevens voor epoche-verlies in een logboek bijgekomen. 

```python
import mlflow 
mlflow.log_metric('epoch_loss', loss.item()) 
```

## <a name="register-models-with-mlflow"></a>Modellen registreren bij MLflow

Nadat uw model is getraind, kunt u uw modellen registreren en registreren bij de back-endtrackingserver met de `mlflow.<model_flavor>.log_model()` methode . `<model_flavor>`, verwijst naar het framework dat is gekoppeld aan het model. [Ontdek welke modelsmaak wordt ondersteund.](https://mlflow.org/docs/latest/models.html#model-api) 

De back-endtrackingserver is standaard Azure Databricks werkruimte; Tenzij u ervoor hebt gekozen [om MLflow-tracering](#set-mlflow-tracking-to-only-track-in-your-azure-machine-learning-workspace)alleen bij te houden in uw Azure Machine Learning-werkruimte, is de back-Azure Machine Learning-werkruimte.   

* **Als er geen geregistreerd model met** de naam bestaat, registreert de methode een nieuw model, maakt versie 1 en retourneert een ModelVersion MLflow-object. 

* **Als er al een geregistreerd model met de naam bestaat,** maakt de methode een nieuwe modelversie en retourneert deze het versieobject. 

```python
mlflow.spark.log_model(model, artifact_path = "model", 
                       registered_model_name = 'model_name')  

mlflow.sklearn.log_model(model, artifact_path = "model", 
                         registered_model_name = 'model_name') 
```

## <a name="create-endpoints-for-mlflow-models"></a>Eindpunten maken voor MLflow-modellen

Wanneer u klaar bent om een eindpunt voor uw ML-modellen te maken. U kunt implementeren als: 

* Een Azure Machine Learning Request-Response webservice voor interactief scoren. Met deze implementatie kunt u gebruikmaken van de mogelijkheden Azure Machine Learning modelbeheer en detectie van gegevensdrift toepassen op uw productiemodellen. 

* MLFlow-modelobjecten, die kunnen worden gebruikt in streaming- of batchpijplijnen als Python-functies of Pandas-UF's in Azure Databricks werkruimte.

### <a name="deploy-models-to-azure-machine-learning-endpoints"></a>Modellen implementeren Azure Machine Learning eindpunten 
U kunt de [API mlflow.azureml.deploy](https://www.mlflow.org/docs/latest/python_api/mlflow.azureml.html#mlflow.azureml.deploy) gebruiken om een model te implementeren in Azure Machine Learning werkruimte. Als u het model alleen hebt geregistreerd bij de Azure Databricks-werkruimte, zoals beschreven in de sectie Modellen registreren met [MLflow,](#register-models-with-mlflow) geeft u de parameter op om het model te registreren `model_name` bij Azure Machine Learning werkruimte. 

Azure Databricks-runs kunnen worden geïmplementeerd op de volgende eindpunten, 
* [Azure Container Instance](how-to-deploy-mlflow-models.md#deploy-to-azure-container-instance-aci)
* [Azure Kubernetes Service](how-to-deploy-mlflow-models.md#deploy-to-azure-kubernetes-service-aks)

### <a name="deploy-models-to-adb-endpoints-for-batch-scoring"></a>Modellen implementeren op ADB-eindpunten voor batch scoren 

U kunt kiezen Azure Databricks voor batchscores. Het MLFlow-model wordt geladen en gebruikt als een Spark Pandas UDF om nieuwe gegevens te scoren. 

```python
from pyspark.sql.types import ArrayType, FloatType 

model_uri = "runs:/"+last_run_id+ {model_path} 

#Create a Spark UDF for the MLFlow model 

pyfunc_udf = mlflow.pyfunc.spark_udf(spark, model_uri) 

#Load Scoring Data into Spark Dataframe 

scoreDf = spark.table({table_name}).where({required_conditions}) 


#Make Prediction 

preds = (scoreDf 

           .withColumn('target_column_name', pyfunc_udf('Input_column1', 'Input_column2', ' Input_column3', …)) 

        ) 

display(preds) 
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u niet van plan bent om de vastgelegde metrische gegevens en artefacten in uw werkruimte te gebruiken, is de mogelijkheid om ze afzonderlijk te verwijderen momenteel niet beschikbaar. Verwijder in plaats daarvan de resourcegroep die het opslagaccount en de werkruimte bevat, zodat er geen kosten in rekening worden gebracht:

1. Selecteer **Resourcegroepen** links in Azure Portal.

   ![Verwijderen in Azure Portal](./media/how-to-use-mlflow-azure-databricks/delete-resources.png)

1. Selecteer de resourcegroep die u eerder hebt gemaakt uit de lijst.

1. Selecteer **Resourcegroep verwijderen**.

1. Voer de naam van de resourcegroup in. Selecteer vervolgens **Verwijderen**.

## <a name="example-notebooks"></a>Voorbeeldnotebooks

De [MLflow met Azure Machine Learning notebooks](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/track-and-monitor-experiments/using-mlflow) laten concepten zien en uitbreiden die in dit artikel worden beschreven.

## <a name="next-steps"></a>Volgende stappen
* [Implementeer MLflow-modellen als een Azure-webservice.](how-to-deploy-mlflow-models.md) 
* [Uw modellen beheren.](concept-model-management-and-deployment.md)
* [Experimentuit runs bijhouden met MLflow en Azure Machine Learning](how-to-use-mlflow.md). 
* Meer informatie over [Azure Databricks en MLflow.](/azure/databricks/applications/mlflow/)