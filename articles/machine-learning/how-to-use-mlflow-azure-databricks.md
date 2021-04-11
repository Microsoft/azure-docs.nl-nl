---
title: MLflow tracking voor Azure Databricks ML experimenten
titleSuffix: Azure Machine Learning
description: Stel MLflow in met Azure Machine Learning om metrische gegevens en artefacten van Azure Databricks ml-experimenten te registreren.
services: machine-learning
author: nibaccam
ms.author: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.reviewer: nibaccam
ms.date: 09/22/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: a894a46ce7c78b65dde80c52f9516fcbe4d27bcb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102520996"
---
# <a name="track-azure-databricks-ml-experiments-with-mlflow-and-azure-machine-learning-preview"></a>Azure Databricks ML experimenten bijhouden met MLflow en Azure Machine Learning (preview)

In dit artikel leert u hoe u de tracking-URI en logboek registratie-API van MLflow kunt inschakelen, die gezamenlijk bekend staat als [MLflow-tracking](https://mlflow.org/docs/latest/quickstart.html#using-the-tracking-api), om verbinding te maken met uw Azure Databricks-experimenten (ADB), MLflow en Azure machine learning.

[MLflow](https://www.mlflow.org) is een open-source bibliotheek voor het beheren van de levens cyclus van uw machine learning experimenten. MLFlow tracking is een onderdeel van MLflow dat de metrische gegevens van uw training en model artefacten registreert en registreert. Meer informatie over [Azure Databricks en MLflow](/azure/databricks/applications/mlflow/). 

Zie [experimenten volgen met MLflow en Azure machine learning](how-to-use-mlflow.md) voor aanvullende MLflow en Azure machine learning functionaliteits integraties.

>[!NOTE]
> MLflow is een open-source bibliotheek die regel matig wordt gewijzigd. Als zodanig moeten de functies die beschikbaar zijn via de Azure Machine Learning-en MLflow-integratie, worden beschouwd als een preview en niet volledig worden ondersteund door micro soft.

> [!TIP]
> De informatie in dit document is voornamelijk bedoeld voor gegevens wetenschappers en ontwikkel aars die het model trainings proces willen bewaken. Zie [Azure machine learning bewaken](monitor-azure-machine-learning.md)als u een beheerder bent die geïnteresseerd is in het bewaken van het resource gebruik en gebeurtenissen van Azure machine learning, zoals quota's, voltooide trainings uitvoeringen of voltooide implementaties van modellen.

## <a name="prerequisites"></a>Vereisten

* Installeer het `azureml-mlflow`-pakket. 
    * Dit pakket maakt automatisch deel uit `azureml-core` van de [Azure machine learning PYTHON-SDK](/python/api/overview/azure/ml/install), waarmee de connectiviteit voor MLflow wordt geboden om toegang te krijgen tot uw werk ruimte.
* Een [Azure Databricks-werk ruimte en-cluster](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal).
* [Maak een Azure machine learning-werkruimte](how-to-manage-workspace.md).
    * Bekijk welke [toegangs machtigingen u nodig hebt om uw MLflow-bewerkingen uit te voeren met uw werk ruimte](how-to-assign-roles.md#mlflow-operations).

## <a name="track-azure-databricks-runs"></a>Azure Databricks uitvoeringen bijhouden

Als u MLflow tracking met Azure Machine Learning, kunt u de vastgelegde metrische gegevens en artefacten opslaan vanuit uw Azure Databricks worden uitgevoerd in uw: 

* Azure Databricks werk ruimte.
* Azure Machine Learning-werkruimte

Nadat u uw Azure Databricks-werk ruimte en-cluster hebt gemaakt, 

1. Installeer de *azureml-mlflow-* bibliotheek van PyPi om ervoor te zorgen dat uw cluster toegang heeft tot de benodigde functies en klassen.

1. Stel uw proef notitieblok in.

1. Verbind uw Azure Databricks-werk ruimte en Azure Machine Learning-werk ruimte.

Meer informatie over deze stappen vindt u in de volgende secties, zodat u uw MLflow-experimenten kunt uitvoeren met Azure Databricks. 

## <a name="install-libraries"></a>Bibliotheken installeren

Als u bibliotheken in uw cluster wilt installeren, gaat u naar het tabblad **tape wisselaars** en selecteert u **nieuwe installeren**

 ![mlflow met Azure databricks](./media/how-to-use-mlflow-azure-databricks/azure-databricks-cluster-libraries.png)

Typ in het veld **pakket** de tekst azureml-mlflow en selecteer vervolgens installeren. Herhaal deze stap als dat nodig is om andere extra pakketten te installeren op uw cluster voor uw experiment.

 ![Mlflow-bibliotheek voor Azure DB installeren](./media/how-to-use-mlflow-azure-databricks/install-libraries.png)

## <a name="set-up-your-notebook"></a>Uw notitie blok instellen 

Wanneer uw ADB-cluster is ingesteld, 
1. Selecteer **werk ruimten** in het navigatie deel venster aan de linkerkant. 
1. Vouw de vervolg keuzelijst werk ruimten uit en selecteer **importeren**
1. Slepen en neerzetten of bladeren naar uw proef notitieblok om uw ADB-werk ruimte te importeren.
1. Selecteer **Importeren**. Uw experiment notebook wordt automatisch geopend.
1. Selecteer onder de titel van het notitie blok links bovenaan het cluster dat u wilt koppelen aan uw experiment notebook. 

## <a name="connect-your-azure-databricks-and-azure-machine-learning-workspaces"></a>Uw Azure Databricks-en Azure Machine Learning-werk ruimten verbinden

Als u uw ADB-werk ruimte koppelt aan uw Azure Machine Learning-werk ruimte, kunt u de gegevens van uw experiment volgen in de werk ruimte Azure Machine Learning.

Als u uw ADB-werk ruimte wilt koppelen aan een nieuwe of bestaande Azure Machine Learning-werk ruimte, 
1. Meld u aan bij de [Azure-portal](https://ms.portal.azure.com).
1. Ga naar de **overzichts** pagina van de ADB-werk ruimte.
1. Selecteer de knop **koppeling Azure machine learning werk ruimte** aan de rechter kant. 

 ![Azure DB-en Azure Machine Learning-werk ruimten koppelen](./media/how-to-use-mlflow-azure-databricks/link-workspaces.png)

## <a name="mlflow-tracking-in-your-workspaces"></a>MLflow bijhouden in uw werk ruimten

Nadat u uw werk ruimte hebt gemaakt, wordt het bijhouden van MLflow automatisch ingesteld op de volgende locaties:

* De gekoppelde Azure Machine Learning-werk ruimte.
* De oorspronkelijke ADB-werk ruimte. 

Al uw experimenten worden behandeld in de service Managed Azure Machine Learning tracking.

De volgende code moet zich in uw proef notitieblok bevinden om de gekoppelde Azure Machine Learning-werk ruimte te krijgen. 

Deze code, 

*  Hiermee haalt u de details op van uw Azure-abonnement om uw Azure Machine Learning-werk ruimte te instantiëren. 

* Er wordt van uitgegaan dat u een bestaande resource groep en Azure Machine Learning werk ruimte hebt, anders kunt u [deze maken](how-to-manage-workspace.md). 

* Hiermee stelt u de naam van het experiment. Hier volgt een `user_name` consistent met de `user_name` gekoppelde Azure Databricks-werk ruimte.

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

### <a name="set-mlflow-tracking-to-only-track-in-your-azure-machine-learning-workspace"></a>Stel MLflow-tracering in op alleen bijhouden in uw Azure Machine Learning-werk ruimte

Als u uw bijgehouden experimenten liever op een centrale locatie wilt beheren, kunt u het bijhouden van MLflow instellen op **alleen** volgen in uw Azure machine learning-werk ruimte. 

Neem de volgende code op in uw script:

```python
uri = ws.get_mlflow_tracking_uri()
mlflow.set_tracking_uri(uri)
```

Importeer in uw trainings script `mlflow` om de MLflow-logboek registratie-api's te gebruiken en begin met het vastleggen van de metrische gegevens voor de uitvoering. In het volgende voor beeld wordt de epoche-verlies metriek vastgelegd. 

```python
import mlflow 
mlflow.log_metric('epoch_loss', loss.item()) 
```

## <a name="register-models-with-mlflow"></a>Modellen registreren met MLflow

Nadat uw model is getraind, kunt u uw modellen registreren en registreren bij de back-upserver met de- `mlflow.<model_flavor>.log_model()` methode. `<model_flavor>`verwijst naar het Framework dat is gekoppeld aan het model. [Meer informatie over de model soorten die worden ondersteund](https://mlflow.org/docs/latest/models.html#model-api). 

De back-upserver is standaard de Azure Databricks-werk ruimte. tenzij u ervoor hebt gekozen om [MLflow tracking zo in te stellen dat deze alleen in uw Azure machine learning-werk ruimte worden bijgehouden](#set-mlflow-tracking-to-only-track-in-your-azure-machine-learning-workspace), dan is de back-upserver Azure machine learning de werk ruimte.   

* **Als een geregistreerd model met de naam niet bestaat**, wordt een nieuw model door de methode geregistreerd, wordt versie 1 gemaakt en wordt een ModelVersion MLflow-object geretourneerd. 

* **Als er al een geregistreerd model met de naam bestaat**, maakt de methode een nieuwe model versie en retourneert het versie object. 

```python
mlflow.spark.log_model(model, artifact_path = "model", 
                       registered_model_name = 'model_name')  

mlflow.sklearn.log_model(model, artifact_path = "model", 
                         registered_model_name = 'model_name') 
```

## <a name="create-endpoints-for-mlflow-models"></a>Eind punten voor MLflow-modellen maken

Wanneer u klaar bent voor het maken van een eind punt voor uw ML-modellen. U kunt implementeren als, 

* Een Azure Machine Learning Request-Response webservice voor interactieve scores. Met deze implementatie kunt u het Azure Machine Learning model beheer en de mogelijkheden voor het detecteren van gegevens drift Toep assen op uw productie modellen. 

* MLFlow-model objecten, die kunnen worden gebruikt in streams of batch pijplijnen als Python-functies of Panda-UDFs in Azure Databricks werk ruimte.

### <a name="deploy-models-to-azure-machine-learning-endpoints"></a>Modellen implementeren voor Azure Machine Learning-eind punten 
U kunt gebruikmaken van de [mlflow. azureml. Implementeer](https://www.mlflow.org/docs/latest/python_api/mlflow.azureml.html#mlflow.azureml.deploy) API om een model te implementeren in uw Azure machine learning-werk ruimte. Als u het model alleen hebt geregistreerd voor de Azure Databricks-werk ruimte, zoals beschreven in de sectie [modellen registreren bij MLflow](#register-models-with-mlflow) , geeft u de `model_name` para meter op om het model in azure machine learning werk ruimte te registreren. 

Azure Databricks-uitvoeringen kunnen worden geïmplementeerd naar de volgende eind punten, 
* [Azure-container exemplaar](how-to-deploy-mlflow-models.md#deploy-to-azure-container-instance-aci)
* [Azure Kubernetes Service](how-to-deploy-mlflow-models.md#deploy-to-azure-kubernetes-service-aks)

### <a name="deploy-models-to-adb-endpoints-for-batch-scoring"></a>Modellen implementeren voor ADB-eind punten voor batch Score 

U kunt kiezen Azure Databricks clusters voor batch scores. Het MLFlow-model wordt geladen en gebruikt als Spark-Panda UDF om nieuwe gegevens te scoren. 

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

Als u niet van plan bent om de vastgelegde metrische gegevens en artefacten in uw werk ruimte te gebruiken, is de mogelijkheid om ze afzonderlijk te verwijderen, momenteel niet beschikbaar. Verwijder in plaats daarvan de resource groep die het opslag account en de werk ruimte bevat, zodat u geen kosten in rekening brengt:

1. Selecteer **Resourcegroepen** links in Azure Portal.

   ![Verwijderen in Azure Portal](./media/how-to-use-mlflow-azure-databricks/delete-resources.png)

1. Selecteer de resourcegroep die u eerder hebt gemaakt uit de lijst.

1. Selecteer **Resourcegroep verwijderen**.

1. Voer de naam van de resourcegroup in. Selecteer vervolgens **Verwijderen**.

## <a name="example-notebooks"></a>Voorbeeldnotebooks

De [MLflow met Azure machine learning-notebooks](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/track-and-monitor-experiments/using-mlflow) tonen en uitvouwen op concepten die in dit artikel worden gepresenteerd.

## <a name="next-steps"></a>Volgende stappen
* [Implementeer MLflow-modellen als een Azure-webservice](how-to-deploy-mlflow-models.md). 
* [Uw modellen beheren](concept-model-management-and-deployment.md).
* [Spoor experiment wordt uitgevoerd met MLflow en Azure machine learning](how-to-use-mlflow.md). 
* Meer informatie over [Azure Databricks en MLflow](/azure/databricks/applications/mlflow/).