---
title: Logboekregistratie van ML-experimenten en metrische gegevens
titleSuffix: Azure Machine Learning
description: Schakel logboek registratie in voor uw ML-trainings uitvoeringen om realtime uitvoerings metrieken te controleren en om fouten en waarschuwingen te onderzoeken.
services: machine-learning
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.service: machine-learning
ms.subservice: core
ms.date: 07/30/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 9576730d9c4f8d4d237dce9ce8f207ea14b04f45
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103471595"
---
# <a name="enable-logging-in-ml-training-runs"></a>Logboek registratie inschakelen in ML-trainings uitvoeringen


Met de Azure Machine Learning Python-SDK kunt u in realtime informatie vastleggen met behulp van zowel het standaardpakket voor logboekregistratie van Python als de specifieke functionaliteit van de SDK. U kunt logboeken lokaal opslaan en logboeken verzenden naar uw werkruimte in de portal.

Logboeken kunnen u helpen bij het diagnosticeren van fouten en waarschuwingen, of bij het bijhouden van metrische gegevens, zoals parameters en modelprestaties. In dit artikel leert u hoe u logboekregistratie inschakelt in de volgende scenario's:

> [!div class="checklist"]
> * Interactieve trainingssessies
> * Trainingstaken verzenden met behulp van ScriptRunConfig
> * Systeemeigen Python-instellingen voor `logging`
> * Logboekregistratie van aanvullende bronnen


> [!TIP]
> In dit artikel leest u hoe u het modeltrainingsproces kunt bewaken. Zie [Azure Machine Learning bewaken](monitor-azure-machine-learning.md) als u geïnteresseerd bent in het bewaken van het resourcegebruik en gebeurtenissen van Azure Machine Learning, zoals quota's, voltooide trainingsuitvoeringen of voltooide implementaties van modellen.

## <a name="data-types"></a>Gegevenstypen

U kunt meerdere gegevenstypen vastleggen, waaronder scalaire waarden, lijsten, tabellen, afbeeldingen, directory's en meer. Zie de [referentiepagina voor Run-klassen](/python/api/azureml-core/azureml.core.run%28class%29) voor meer informatie en Python-codevoorbeelden voor verschillende gegevenstypen.

### <a name="logging-run-metrics"></a>Metrische gegevens over logboek registratie uitvoeren 

Gebruik de volgende methoden in de logboek registratie-Api's om de metrische visualisaties te beïnvloeden. Noteer de [service limieten](https://docs.microsoft.com/azure/machine-learning/resource-limits-quotas-capacity#metrics) voor deze vastgelegde metrische gegevens. 

|Geregistreerde waarde|Voorbeeldcode| Indeling in Portal|
|----|----|----|
|Een matrix met numerieke waarden vastleggen in een logboek| `run.log_list(name='Fibonacci', value=[0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89])`|lijn diagram met één variabele|
|Een enkele numerieke waarde met dezelfde metrische naam in het logboek vastleggen, herhaaldelijk gebruikt (bijvoorbeeld van binnen een for-lus)| `for i in tqdm(range(-10, 10)):    run.log(name='Sigmoid', value=1 / (1 + np.exp(-i))) angle = i / 2.0`| Lijn diagram met één variabele|
|Een rij met 2 numerieke kolommen herhaaldelijk vastleggen|`run.log_row(name='Cosine Wave', angle=angle, cos=np.cos(angle))   sines['angle'].append(angle)      sines['sine'].append(np.sin(angle))`|Lijn diagram met twee variabelen|
|Logboek tabel met 2 numerieke kolommen|`run.log_table(name='Sine Wave', value=sines)`|Lijn diagram met twee variabelen|
|Logboek afbeelding|`run.log_image(name='food', path='./breadpudding.jpg', plot=None, description='desert')`|Gebruik deze methode om een afbeeldings bestand of een matplotlib-grafiek te registreren bij de uitvoering. Deze installatie kopieën worden weer gegeven en vergeleken in het rapport uitvoeren|

### <a name="logging-with-mlflow"></a>Loggen met MLflow
Gebruik MLFlowLogger voor het vastleggen van metrische gegevens.

```python
from azureml.core import Run
# connect to the workspace from within your running code
run = Run.get_context()
ws = run.experiment.workspace

# workspace has associated ml-flow-tracking-uri
mlflow_url = ws.get_mlflow_tracking_uri()

#Example: PyTorch Lightning
from pytorch_lightning.loggers import MLFlowLogger

mlf_logger = MLFlowLogger(experiment_name=run.experiment.name, tracking_uri=mlflow_url)
mlf_logger._run_id = run.id
```

## <a name="interactive-logging-session"></a>Interactieve logboekregistratiesessie

Interactieve logboekregistratiesessies worden doorgaans gebruikt in notebookomgevingen. Met de methode [Experiment.start_logging()](/python/api/azureml-core/azureml.core.experiment%28class%29#start-logging--args----kwargs-) wordt een interactieve logboekregistratiesessie gestart. Alle metrische gegevens die tijdens de sessie zijn geregistreerd, worden toegevoegd aan het uitvoeringsrecord in het experiment. Met de methode [run.complete()](/python/api/azureml-core/azureml.core.run%28class%29#complete--set-status-true-) worden de sessies beëindigd en de uitvoering als voltooid gemarkeerd.

## <a name="scriptrun-logs"></a>ScriptRun-logboeken

In dit gedeelte leert u hoe u logboekcode in uitvoeringen toevoegt die zijn gemaakt toen de ScriptRunConfig werd geconfigureerd. Met de klasse [**ScriptRunConfig**](/python/api/azureml-core/azureml.core.scriptrunconfig)-kunt u scripts en omgevingen inkapselen voor herhaalbare uitvoeringen. U kunt deze optie ook gebruiken om een visuele Jupyter Notebook-widget weer te geven voor bewakingsdoeleinden.

In dit voorbeeld wordt een parameteropruiming uitgevoerd op alfawaarden en worden de resultaten vastgelegd met de methode [run.log()](/python/api/azureml-core/azureml.core.run%28class%29#log-name--value--description----).

1. Maak een trainingsscript dat de logica voor logboekregistratie bevat, `train.py`.

   [!code-python[](~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train.py)]


1. Verzend het script ```train.py``` voor uitvoering in een door de gebruiker beheerde omgeving. De volledige scriptmap wordt verzonden voor training.

   [! notebook-python [] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb? name = src)]


   [! notebook-python [] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb? name = run)]

    Met de parameter `show_output` wordt uitgebreide logboekregistratie ingeschakeld, zodat u zowel details van het trainingsproces als informatie over externe resources of rekendoelen kunt bekijken. Gebruik de volgende code om uitgebreide logboekregistratie in te schakelen wanneer u het experiment verzendt.

```python
run = exp.submit(src, show_output=True)
```

U kunt ook dezelfde parameter gebruiken in de functie `wait_for_completion` van de resulterende uitvoering.

```python
run.wait_for_completion(show_output=True)
```

## <a name="native-python-logging"></a>Systeemeigen logboekregistratie van Python

Sommige logboeken in de SDK bevatten mogelijk een foutmelding dat u het logboekregistratieniveau moet instellen op DEBUG (fouten opsporen). Als u het logboekregistratieniveau wilt instellen, voegt u de volgende code toe aan het script.

```python
import logging
logging.basicConfig(level=logging.DEBUG)
```

## <a name="additional-logging-sources"></a>Aanvullende bronnen voor logboekregistratie

Met Azure Machine Learning kunnen tijdens de training ook gegevens van andere bronnen worden geregistreerd, zoals geautomatiseerde machine learning-uitvoeringen, of Docker-containers die de taken uitvoeren. Deze logboeken worden niet gedocumenteerd, maar als u problemen ondervindt en contact opneemt met Microsoft-ondersteuning, kunnen ze deze logboeken gebruiken tijdens het oplossen van problemen.

Raadpleeg [Hoe kan ik metrische gegevens registreren in de ontwerpfunctie](how-to-track-designer-experiments.md) voor informatie over het vastleggen van metrische gegevens in de Azure Machine Learning-ontwerpfunctie

## <a name="example-notebooks"></a>Voorbeeldnotebooks

In de volgende notebooks worden concepten in dit artikel gedemonstreerd:
* [how-to-use-azureml/training/train-on-local](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-on-local)
* [how-to-use-azureml/track-and-monitor-experiments/logging-api](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/logging-api)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Volgende stappen

Raadpleeg deze artikelen voor meer informatie over het gebruik van Azure Machine Learning:

* Meer informatie over het [vastleggen van metrische gegevens in de Azure Machine Learning-ontwerpfunctie](how-to-track-designer-experiments.md).

* Bekijk een voorbeeld van hoe u het beste model registreert en implementeert in de zelfstudie [Een afbeeldingsclassificatiemodel trainen met Azure Machine Learning](tutorial-train-models-with-aml.md).
