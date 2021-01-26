---
title: Gegevensopname met Azure Data Factory
titleSuffix: Azure Machine Learning
description: Meer informatie over de beschik bare opties voor het bouwen van een pijp lijn voor gegevens opname met Azure Data Factory en de voor delen van elk.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: iefedore
author: eedorenko
manager: davete
ms.reviewer: larryfr
ms.date: 01/26/2021
ms.topic: conceptual
ms.custom: how-to, devx-track-python, data4ml
ms.openlocfilehash: b842934ea6bb458a59a53ea7068aa9ece98aacf1
ms.sourcegitcommit: 95c2cbdd2582fa81d0bfe55edd32778ed31e0fe8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/26/2021
ms.locfileid: "98796145"
---
# <a name="data-ingestion-with-azure-data-factory"></a>Gegevensopname met Azure Data Factory

In dit artikel vindt u informatie over de beschik bare opties voor het bouwen van een pijp lijn voor gegevens opname met [Azure Data Factory](../data-factory/introduction.md). Deze Azure Data Factory pijp lijn wordt gebruikt om gegevens op te nemen voor gebruik met [Azure machine learning](overview-what-is-azure-ml.md). Met Data Factory kunt u gemakkelijk ETL-gegevens ophalen, transformeren en laden. Zodra de gegevens zijn getransformeerd en in opslag zijn geladen, kunt u deze gebruiken om uw machine learning modellen te trainen in Azure Machine Learning.

Eenvoudige gegevens transformatie kan worden afgehandeld met systeem eigen Data Factory activiteiten en instrumenten zoals de [gegevens stroom](../data-factory/control-flow-execute-data-flow-activity.md). Wanneer het gaat om complexere scenario's, kunnen de gegevens worden verwerkt met een aantal aangepaste code. Bijvoorbeeld python of R-code.

## <a name="compare-azure-data-factory-data-ingestion-pipelines"></a>Pijp lijnen voor het inslikken van Azure Data Factory gegevens vergelijken 
Er zijn verschillende algemene technieken van het gebruik van Data Factory voor het transformeren van gegevens tijdens opname. Elke techniek heeft voor-en nadelen waarmee u kunt bepalen of het geschikt is voor een specifieke use-case:

| Techniek | Voordelen | Nadelen |
| ----- | ----- | ----- |
| Data Factory + Azure Functions | <li> Lage latentie, serverloze compute<li>Stateful functies<li>Herbruikbare functies | Alleen geschikt voor korte actieve verwerking |
| Data Factory + aangepast onderdeel | <li>Grootschalige parallelle computing<li>Geschikt voor zware algoritmen | <li>Vereist terugloop code in een uitvoerbaar bestand<li>Complexiteit van de afhandelings afhankelijkheden en IO |
| Data Factory + Azure Databricks notebook |<li> Apache Spark<li>Systeem eigen python-omgeving |<li>Kan duur zijn<li>Het maken van clusters wordt in eerste instantie tijd en latentie toegevoegd

## <a name="azure-data-factory-with-azure-functions"></a>Azure Data Factory met Azure functions

Met Azure Functions kunt u kleine stukjes code (functies) uitvoeren zonder dat u zich zorgen hoeft te maken over de infra structuur van de toepassing. In deze optie worden de gegevens verwerkt met aangepaste python-code die is ingepakt in een Azure-functie. 

De functie wordt aangeroepen met de [Azure Data Factory Azure function-activiteit](../data-factory/control-flow-azure-function-activity.md). Deze methode is een goede optie voor licht gewicht gegevens transformaties. 

![Diagram toont een Azure Data Factory pijp lijn, met Azure function en run ML pijp lijn, en een Azure Machine Learning pijp lijn, met Train model, en hoe ze communiceren met onbewerkte gegevens en voor bereide gegevens.](media/how-to-data-ingest-adf/adf-function.png)



* Voordelen:
    * De gegevens worden verwerkt op een serverloze Compute met een relatief lage latentie
    * Data Factory pijp lijn kan een [duurzame Azure-functie](../azure-functions/durable/durable-functions-overview.md) aanroepen waarmee een geavanceerde gegevens transformatie stroom kan worden geïmplementeerd 
    * De details van de gegevens transformatie worden abstract gemaakt door de Azure-functie die opnieuw kan worden gebruikt en vanaf andere locaties kan worden aangeroepen.
* Nadelen
    * De Azure Functions moet worden gemaakt voor gebruik met ADF
    * Azure Functions is alleen geschikt voor het verwerken van langlopende gegevens

## <a name="azure-data-factory-with-custom-component-activity"></a>Azure Data Factory met aangepaste onderdeel activiteit

In deze optie worden de gegevens verwerkt met aangepaste python-code die is ingepakt in een uitvoerbaar bestand. Deze wordt aangeroepen met een [ Azure Data Factory aangepaste onderdeel activiteit](../data-factory/transform-data-using-dotnet-custom-activity.md). Deze aanpak is beter geschikt voor grote gegevens dan de vorige techniek.

![In het diagram ziet u een Azure Data Factory pijp lijn, met een aangepast onderdeel en een pijp lijn met M L, en een Azure Machine Learning pijp lijn, met Train model, en hoe ze communiceren met onbewerkte gegevens en voor bereide gegevens.](media/how-to-data-ingest-adf/adf-customcomponent.png)

* Voordelen:
    * De gegevens worden verwerkt op [Azure batch](../batch/batch-technical-overview.md) pool, waardoor grootschalige parallelle en High-Performance Computing worden geboden
    * Kan worden gebruikt om zware algoritmen uit te voeren en grote hoeveel heden gegevens te verwerken
* Nadelen:
    * Azure Batch groep moet worden gemaakt voor gebruik met Data Factory
    * Over technische technici met betrekking tot het inpakken van python-code naar een uitvoerbaar bestand Complexiteit van het verwerken van afhankelijkheden en para meters voor invoer/uitvoer

## <a name="azure-data-factory-with-azure-databricks-python-notebook"></a>Azure Data Factory met Azure Databricks python-notebook

[Azure Databricks](https://azure.microsoft.com/services/databricks/) is een op Apache Spark gebaseerd analyse platform in de micro soft Cloud.

In deze techniek wordt de gegevens transformatie uitgevoerd door een [python-notebook](../data-factory/transform-data-using-databricks-notebook.md), dat wordt uitgevoerd op een Azure Databricks cluster. Dit is waarschijnlijk de meest voorkomende benadering die gebruikmaakt van de volledige kracht van een Azure Databricks service. Het is ontworpen voor de verwerking van gedistribueerde gegevens op schaal.

![Diagram toont een Azure Data Factory pijp lijn, met Azure Databricks python, een pijp lijn met M L en een Azure Machine Learning pijp lijn, met Train model, en hoe ze communiceren met onbewerkte gegevens en voor bereide gegevens.](media/how-to-data-ingest-adf/adf-databricks.png)

* Voordelen:
    * De gegevens worden getransformeerd op de krach tigste Azure-service voor gegevens verwerking, waarvan een back-up wordt gemaakt door Apache Spark omgeving
    * Systeem eigen ondersteuning van python samen met data Science frameworks en Bibliotheken, waaronder tensor flow, PyTorch en scikit-Learn
    * Het is niet nodig om de python-code op te slaan in functions of uitvoer bare modules. De code werkt als volgt.
* Nadelen:
    * Azure Databricks-infra structuur moet worden gemaakt voor gebruik met Data Factory
    * Kan duur zijn afhankelijk van Azure Databricks configuratie
    * Het draaien van reken clusters van de modus koud duurt enige tijd die hoge latentie voor de oplossing biedt 
    

## <a name="consume-data-in-azure-machine-learning"></a>Gegevens in Azure Machine Learning gebruiken 

Met de Data Factory pijp lijn worden de voor bereide gegevens opgeslagen in uw opslag ruimte in de Cloud (zoals Azure Blob of Azure Datalake). <br>
Verbruik uw voor bereide gegevens in Azure Machine Learning door, 

* Het aanroepen van een Azure Machine Learning pijp lijn vanuit uw Data Factory-pijp lijn.<br>**OF**
* Een [Azure machine learning gegevens opslag](how-to-access-data.md#create-and-register-datastores) en [Azure machine learning gegevensset](how-to-create-register-datasets.md) maken voor gebruik op een later tijdstip.

### <a name="invoke-azure-machine-learning-pipeline-from-data-factory"></a>Azure Machine Learning pijp lijn aanroepen vanuit Data Factory

Deze methode wordt aanbevolen voor [MLOps-werk stromen (machine learning Operations)](concept-model-management-and-deployment.md#what-is-mlops). Als u geen Azure Machine Learning pijp lijn wilt instellen, raadpleegt u [gegevens rechtstreeks uit de opslag lezen](#read-data-directly-from-storage).

Telkens wanneer de Data Factory pijplijn wordt uitgevoerd, 

1. De gegevens worden opgeslagen op een andere locatie in de opslag. 
1. De Data Factory-pijp lijn roept een [Azure machine learning pijp lijn](concept-ml-pipelines.md)aan om de locatie door te geven aan Azure machine learning. Bij het aanroepen van de ML pijp lijn worden de gegevens locatie en run-ID verzonden als para meters. 
1. Met de ML-pijp lijn kunt u vervolgens een Azure Machine Learning gegevens opslag en gegevensset maken met de locatie van de data. Meer informatie vindt u in [Data Factory Azure machine learning-pijp lijnen uitvoeren](../data-factory/transform-data-machine-learning-service.md).

![Diagram toont een Azure Data Factory pijp lijn en een Azure Machine Learning pijp lijn en hoe ze communiceren met onbewerkte gegevens en voor bereide gegevens. Met de Data Factory pijp lijn worden gegevens naar de voor bereide data base gevoed, waarmee een gegevens archief wordt gevoed, wat gegevens sets in de werk ruimte Machine Learning.](media/how-to-data-ingest-adf/aml-dataset.png)

> [!TIP]
> Gegevens sets [ondersteunen versie beheer](./how-to-version-track-datasets.md), zodat de ml-pijp lijn een nieuwe versie van de gegevensset kan registreren die verwijst naar de meest recente gegevens van de ADF-pijp lijn.

Zodra de gegevens toegankelijk zijn via een gegevens opslag of gegevensset, kunt u deze gebruiken om een ML-model te trainen. Het trainings proces kan deel uitmaken van dezelfde ML-pijp lijn die wordt aangeroepen vanuit ADF. Het is ook mogelijk dat het een afzonderlijk proces is, zoals experimenten in een Jupyter-notebook.

Omdat gegevens sets ondersteuning bieden voor versie beheer en elke uitvoering van de pijp lijn een nieuwe versie maakt, is het eenvoudig om te begrijpen welke versie van de gegevens is gebruikt voor het trainen van een model.

### <a name="read-data-directly-from-storage"></a>Gegevens rechtstreeks uit de opslag lezen

Als u geen ML-pijp lijn wilt maken, kunt u rechtstreeks toegang krijgen tot de gegevens vanuit het opslag account waar uw voor bereide gegevens worden opgeslagen met een Azure Machine Learning gegevens opslag en gegevensset. 

De volgende python-code laat zien hoe u een gegevens opslag maakt die verbinding maakt met Azure DataLake Generation 2-opslag. Meer [informatie over gegevens opslag en waar u Service-Principal-machtigingen kunt vinden](how-to-access-data.md#create-and-register-datastores).

```python
ws = Workspace.from_config()
adlsgen2_datastore_name = '<ADLS gen2 storage account alias>'  #set ADLS Gen2 storage account alias in AML

subscription_id=os.getenv("ADL_SUBSCRIPTION", "<ADLS account subscription ID>") # subscription id of ADLS account
resource_group=os.getenv("ADL_RESOURCE_GROUP", "<ADLS account resource group>") # resource group of ADLS account

account_name=os.getenv("ADLSGEN2_ACCOUNTNAME", "<ADLS account name>") # ADLS Gen2 account name
tenant_id=os.getenv("ADLSGEN2_TENANT", "<tenant id of service principal>") # tenant id of service principal
client_id=os.getenv("ADLSGEN2_CLIENTID", "<client id of service principal>") # client id of service principal
client_secret=os.getenv("ADLSGEN2_CLIENT_SECRET", "<secret of service principal>") # the secret of service principal

adlsgen2_datastore = Datastore.register_azure_data_lake_gen2(
    workspace=ws,
    datastore_name=adlsgen2_datastore_name,
    account_name=account_name, # ADLS Gen2 account name
    filesystem='<filesystem name>', # ADLS Gen2 filesystem
    tenant_id=tenant_id, # tenant id of service principal
    client_id=client_id, # client id of service principal
```

Maak vervolgens een gegevensset om te verwijzen naar de bestanden die u wilt gebruiken in uw machine learning-taak. 

Met de volgende code wordt een TabularDataset gemaakt op basis van een CSV-bestand `prepared-data.csv` . Meer informatie over [typen gegevens sets en geaccepteerde bestands indelingen](how-to-create-register-datasets.md#dataset-types). 

```python
from azureml.core import Workspace, Datastore, Dataset
from azureml.core.experiment import Experiment
from azureml.train.automl import AutoMLConfig

# retrieve data via AML datastore
datastore = Datastore.get(ws, adlsgen2_datastore)
datastore_path = [(datastore, '/data/prepared-data.csv')]
        
prepared_dataset = Dataset.Tabular.from_delimited_files(path=datastore_path)
```

Hier kunt u gebruiken `prepared_dataset` om te verwijzen naar uw voor bereide gegevens, zoals in uw trainings scripts. Meer informatie over het [trainen van modellen met gegevens sets in azure machine learning](./how-to-train-with-datasets.md).

## <a name="next-steps"></a>Volgende stappen

* [Een Databricks-notebook uitvoeren in Azure Data Factory](../data-factory/transform-data-using-databricks-notebook.md)
* [Toegang tot gegevens in azure Storage-services](./how-to-access-data.md#create-and-register-datastores)
* [Train modellen met gegevens sets in azure machine learning](./how-to-train-with-datasets.md).
* [DevOps voor een gegevensopnamepijplijn](./how-to-cicd-data-ingestion.md)