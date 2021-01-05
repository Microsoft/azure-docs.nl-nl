---
title: Ontwikkelen met AutoML & Azure Databricks
titleSuffix: Azure Machine Learning
description: Meer informatie over het instellen van een ontwikkel omgeving in Azure Machine Learning en Azure Databricks. Gebruik de Azure ML-Sdk's voor Databricks en Databricks met AutoML.
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.date: 10/21/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 878e6f11645a6478c0d536e9d6d6dac4518c5349
ms.sourcegitcommit: 44844a49afe8ed824a6812346f5bad8bc5455030
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/23/2020
ms.locfileid: "97740960"
---
# <a name="set-up-a-development-environment-with-azure-databricks-and-automl-in-azure-machine-learning"></a>Stel een ontwikkel omgeving in met Azure Databricks en AutoML in Azure Machine Learning 

Meer informatie over het configureren van een ontwikkel omgeving in Azure Machine Learning die gebruikmaakt van Azure Databricks en geautomatiseerd ML.

Azure Databricks is ideaal voor het uitvoeren van grootschalige intensieve machine learning werk stromen op het schaal bare Apache Spark platform in de Azure-Cloud. Het biedt een samenwerkings omgeving op basis van een laptop met een CPU of een reken cluster op basis van GPU.

Zie [een python-ontwikkel omgeving instellen](how-to-configure-environment.md)voor meer informatie over andere machine learning ontwikkel omgevingen.


## <a name="prerequisite"></a>Vereiste

Azure Machine Learning werk ruimte. Als u er geen hebt, kunt u een Azure Machine Learning-werk ruimte maken met behulp van de [Azure Portal](how-to-manage-workspace.md)-, [Azure CLI](how-to-manage-workspace-cli.md#create-a-workspace)-en [Azure Resource Manager-sjablonen](how-to-create-workspace-template.md).


## <a name="azure-databricks-with-azure-machine-learning-and-automl"></a>Azure Databricks met Azure Machine Learning en AutoML

Azure Databricks integreert met Azure Machine Learning en de AutoML-mogelijkheden. 

U kunt Azure Databricks gebruiken:

+ Een model trainen met Spark MLlib en het model implementeren in ACI/AKS.
+ Met [geautomatiseerde machine learning](concept-automated-ml.md) mogelijkheden met behulp van een Azure ml-SDK.
+ Als reken doel van een [Azure machine learning-pijp lijn](concept-ml-pipelines.md).

## <a name="set-up-a-databricks-cluster"></a>Een Databricks-cluster instellen

Maak een [Databricks-cluster](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal). Sommige instellingen zijn alleen van toepassing als u de SDK installeert voor automatische machine learning op Databricks.

**Het duurt enkele minuten om het cluster te maken.**

Gebruik deze instellingen:

| Instelling |Van toepassing op| Waarde |
|----|---|---|
| Clusternaam |altijd| yourclustername |
| Databricks Runtime versie |altijd| Runtime 7,1 (scala 2,21, Spark 3.0.0)-niet ML|
| Python-versie |altijd| 3 |
| Type werk nemer <br>(bepaalt het maximum aantal gelijktijdige iteraties) |Geautomatiseerde machine learning<br>alleen| Voorkeurs-VM geoptimaliseerd voor geheugen |
| IT |altijd| 2 of hoger |
| Automatisch schalen inschakelen |Geautomatiseerde machine learning<br>alleen| Uitschakelen |

Wacht totdat het cluster wordt uitgevoerd voordat u doorgaat.

## <a name="add-the-azure-ml-sdk-to-databricks"></a>De Azure ML SDK toevoegen aan Databricks

Zodra het cluster wordt uitgevoerd, [maakt u een bibliotheek](https://docs.databricks.com/user-guide/libraries.html#create-a-library) om het juiste Azure machine learning SDK-pakket aan uw cluster toe te voegen. 

Als u automatische ML wilt gebruiken, gaat u verder met [het toevoegen van de Azure ml SDK met AutoML](#add-the-azure-ml-sdk-with-automl-to-databricks).


1. Klik met de rechter muisknop op de huidige werkruimte map waar u de bibliotheek wilt opslaan. Selecteer   >  **bibliotheek** maken.
    
    > [!TIP]
    > Als u een oude SDK-versie hebt, schakelt u deze uit in de geïnstalleerde bibliotheken van het cluster en gaat u naar de Prullenbak. Installeer de nieuwe SDK-versie en start het cluster opnieuw op. Als er een probleem is nadat de computer opnieuw is opgestart, ontkoppelt u het cluster en koppelt u het opnieuw.

1. Kies de volgende optie (er worden geen andere SDK-installaties ondersteund)

   |SDK- &nbsp; pakket &nbsp; extra's|Bron|PyPi- &nbsp; naam&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|
   |----|---|---|
   |Voor Databricks| Python-ei of PyPI uploaden | azureml-SDK [databricks]|

   > [!WARNING]
   > Er kunnen geen andere SDK-extra's worden geïnstalleerd. Kies alleen de `databricks` optie [].

   * Selecteer niet **automatisch koppelen aan alle clusters**.
   * Selecteer  **koppelen** naast de naam van uw cluster.

1. Controleer op fouten totdat de status is gewijzigd in **bijgevoegd**. Dit kan enkele minuten duren.  Als deze stap mislukt:

   Probeer het cluster opnieuw te starten door:
   1. Selecteer **clusters** in het linkerdeel venster.
   1. Selecteer de naam van uw cluster in de tabel.
   1. Selecteer **opnieuw opstarten** op het tabblad **tape wisselaars** .

   Een geslaagde installatie ziet er ongeveer als volgt uit: 

  ![Azure Machine Learning SDK voor Databricks](./media/how-to-configure-environment/amlsdk-withoutautoml.jpg) 

## <a name="add-the-azure-ml-sdk-with-automl-to-databricks"></a>Voeg de Azure ML SDK met AutoML toe aan Databricks
Als het cluster is gemaakt met Databricks Runtime 7,1 of hoger (*niet* ml), voert u de volgende opdracht uit in de eerste cel van uw notitie blok om de AML-SDK te installeren.

```
%pip install --upgrade --force-reinstall -r https://aka.ms/automl_linux_requirements.txt
```
Voor Databricks Runtime 7,0 en lager installeert u de Azure Machine Learning SDK met het [init-script](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks/automl/README.md).

### <a name="automl-config-settings"></a>AutoML-configuratie-instellingen

Bij het gebruik van Azure Databricks de volgende para meters toevoegen in AutoML config:

- ```max_concurrent_iterations``` is gebaseerd op het aantal worker-knoop punten in uw cluster.
- ```spark_context=sc``` is gebaseerd op de standaard Spark-context.

## <a name="ml-notebooks-that-work-with-azure-databricks"></a>ML-notebooks die samen werken met Azure Databricks

Uitproberen:
+ Er zijn veel voorbeeld notitieblokken beschikbaar, **maar alleen [deze voorbeeld notitieblokken](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks) werken met Azure Databricks.**

+ Importeer deze voor beelden rechtstreeks vanuit uw werk ruimte. Zie hieronder: ![ Selecteer import ](./media/how-to-configure-environment/azure-db-screenshot.png)
 ![ paneel importeren](./media/how-to-configure-environment/azure-db-import.png)

+ Meer informatie over het [maken van een pijp lijn met Databricks als de trainings Compute](how-to-create-your-first-pipeline.md).

## <a name="troubleshooting"></a>Problemen oplossen

* **Fout bij het installeren van pakketten**

    Azure Machine Learning SDK-installatie mislukt op Azure Databricks wanneer er meer pakketten zijn geïnstalleerd. Sommige pakketten, zoals `psutil` , kunnen conflicten veroorzaken. Installeer pakketten door de bibliotheek versie te blok keren om installatie fouten te voor komen. Dit probleem heeft betrekking op Databricks en niet op de Azure Machine Learning SDK. Dit probleem kan ook optreden met andere bibliotheken. Voorbeeld:
    
    ```python
    psutil cryptography==1.5 pyopenssl==16.0.0 ipython==2.2.0
    ```

    U kunt ook init-scripts gebruiken als u problemen ondervindt bij de installatie van python-bibliotheken. Deze aanpak wordt niet officieel ondersteund. Zie voor meer informatie [cluster-scoped init-scripts](https://docs.azuredatabricks.net/user-guide/clusters/init-scripts.html#cluster-scoped-init-scripts).

* **Fout bij importeren: kan naam niet importeren `Timedelta` van `pandas._libs.tslibs`**: als u deze fout ziet wanneer u gebruikmaakt van automatische machine learning, voert u de twee volgende regels uit in uw notitie blok:
    ```
    %sh rm -rf /databricks/python/lib/python3.7/site-packages/pandas-0.23.4.dist-info /databricks/python/lib/python3.7/site-packages/pandas
    %sh /databricks/python/bin/pip install pandas==0.23.4
    ```

* **Import fout: geen module met de naam ' Pandas. core. indices '**: als u deze fout ziet wanneer u automatische machine learning gebruikt:

    1. Voer deze opdracht uit om twee pakketten te installeren in uw Azure Databricks cluster:
    
       ```bash
       scikit-learn==0.19.1
       pandas==0.22.0
       ```
    
    1. Ontkoppel het cluster en koppel het vervolgens opnieuw aan uw notitie blok.
    
    Als u met deze stappen het probleem niet kunt oplossen, probeert u het cluster opnieuw op te starten.

* **FailToSendFeather**: als er een `FailToSendFeather` fout wordt weer gegeven bij het lezen van gegevens op Azure Databricks cluster, raadpleegt u de volgende oplossingen:
    
    * Upgrade `azureml-sdk[automl]` pakket naar de nieuwste versie.
    * Voeg `azureml-dataprep` versie 1.1.8 of hoger toe.
    * Voeg `pyarrow` versie 0,11 of hoger toe.
  

## <a name="next-steps"></a>Volgende stappen

- [Train een model](tutorial-train-models-with-aml.md) op Azure machine learning met de MNIST-gegevensset.
- Zie de [Naslag informatie voor de Azure machine learning SDK voor python](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py).
