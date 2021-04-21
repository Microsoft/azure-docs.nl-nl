---
title: Metrische & logboekbestanden weergeven
titleSuffix: Azure Machine Learning
description: Schakel logboekregistratie in voor uw ML-trainingsruns om realtime metrische gegevens van de run te bewaken en om fouten en waarschuwingen te diagnosticeren.
services: machine-learning
author: swinner95
ms.author: shwinne
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.date: 04/19/2021
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 5bc7564a11f570833bd6cb10c14267e08c3b5753
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107820437"
---
# <a name="log--view-metrics-and-log-files"></a>Metrische & logboekbestanden weergeven

Registreer realtime-informatie met behulp van zowel het standaard python-logboekregistratiepakket als Azure Machine Learning python SDK-specifieke functionaliteit. U kunt logboeken lokaal opslaan en logboeken verzenden naar uw werkruimte in de portal.

Logboeken kunnen u helpen bij het diagnosticeren van fouten en waarschuwingen, of bij het bijhouden van metrische gegevens, zoals parameters en modelprestaties. In dit artikel leert u hoe u logboekregistratie inschakelt in de volgende scenario's:

> [!div class="checklist"]
> * Metrische gegevens voor het uitvoeren van logboeken
> * Interactieve trainingssessies
> * Trainingstaken verzenden met behulp van ScriptRunConfig
> * Systeemeigen Python-instellingen voor `logging`
> * Logboekregistratie van aanvullende bronnen


> [!TIP]
> In dit artikel leest u hoe u het modeltrainingsproces kunt bewaken. Zie [Azure Machine Learning bewaken](monitor-azure-machine-learning.md) als u geïnteresseerd bent in het bewaken van het resourcegebruik en gebeurtenissen van Azure Machine Learning, zoals quota's, voltooide trainingsuitvoeringen of voltooide implementaties van modellen.

## <a name="data-types"></a>Gegevenstypen

U kunt meerdere gegevenstypen vastleggen, waaronder scalaire waarden, lijsten, tabellen, afbeeldingen, directory's en meer. Zie de [referentiepagina voor Run-klassen](/python/api/azureml-core/azureml.core.run%28class%29) voor meer informatie en Python-codevoorbeelden voor verschillende gegevenstypen.

### <a name="logging-run-metrics"></a>Metrische gegevens voor het uitvoeren van logboekregistratie 

Gebruik de volgende methoden in de API's voor logboekregistratie om de visualisaties met metrische gegevens te beïnvloeden. Noteer de [servicelimieten](./resource-limits-quotas-capacity.md#metrics) voor deze vastgelegde metrische gegevens. 

|Vastgelegde waarde|Voorbeeldcode| Indeling in portal|
|----|----|----|
|Een matrix met numerieke waarden in een logboek maken| `run.log_list(name='Fibonacci', value=[0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89])`|lijndiagram met één variabele|
|Registreer één numerieke waarde met dezelfde metrische naam die herhaaldelijk wordt gebruikt (bijvoorbeeld vanuit een for-lus)| `for i in tqdm(range(-10, 10)):    run.log(name='Sigmoid', value=1 / (1 + np.exp(-i))) angle = i / 2.0`| Lijndiagram met één variabele|
|Een rij met 2 numerieke kolommen herhaaldelijk aanmelden|`run.log_row(name='Cosine Wave', angle=angle, cos=np.cos(angle))   sines['angle'].append(angle)      sines['sine'].append(np.sin(angle))`|Lijndiagram met twee variabelen|
|Logboektabel met 2 numerieke kolommen|`run.log_table(name='Sine Wave', value=sines)`|Lijndiagram met twee variabelen|
|Logboekafbeelding|`run.log_image(name='food', path='./breadpudding.jpg', plot=None, description='desert')`|Gebruik deze methode om een afbeeldingsbestand of een matplotlib-plot bij de run te leggen. Deze afbeeldingen zijn zichtbaar en vergelijkbaar in de runrecord|

### <a name="logging-with-mlflow"></a>Logboekregistratie met MLflow
Gebruik MLFlowLogger om metrische gegevens te logboeken.

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

## <a name="view-run-metrics"></a>Metrische gegevens voor uitvoeren weergeven

## <a name="via-the-sdk"></a>Via de SDK
U kunt de metrische gegevens van een getraind model weergeven met behulp van ```run.get_metrics()``` . Zie het voorbeeld hieronder. 

```python
from azureml.core import Run
run = Run.get_context()
run.log('metric-name', metric_value)

metrics = run.get_metrics()
# metrics is of type Dict[str, List[float]] mapping mertic names
# to a list of the values for that metric in the given run.

metrics.get('metric-name')
# list of metrics in the order they were recorded
```

<a name="view-the-experiment-in-the-web-portal"></a>

## <a name="view-run-metrics-in-aml-studio-ui"></a>Metrische gegevens over uitvoeren weergeven in de AML Studio-gebruikersinterface

U kunt bladeren door voltooide records, inclusief vastgelegde metrische gegevens, in [de Azure Machine Learning-studio](https://ml.azure.com).

Navigeer naar **het tabblad Experimenten.** Selecteer het tabblad Alle runs om al uw runs in uw werkruimte voor experimenten **weer te** bieden. U kunt inzoomen op de runs voor specifieke experimenten door het filter Experiment toe te passen in de bovenste menubalk.

Selecteer voor de afzonderlijke experimentweergave het **tabblad Alle experimenten.** Op het dashboard van de experimentrun ziet u metrische gegevens en logboeken voor elke run. 

U kunt ook de lijsttabel van de run bewerken om meerdere runs te selecteren en de laatste, minimale of maximale vastgelegde waarde voor uw runs weer te geven. Pas uw grafieken aan om de vastgelegde metrische waarden en statistische gegevens voor meerdere runs te vergelijken. U kunt meerdere metrische gegevens op de y-as van uw grafiek plotten en uw x-as aanpassen om uw vastgelegde metrische gegevens te plotten. 


### <a name="view-and-download-log-files-for-a-run"></a>Logboekbestanden voor een run weergeven en downloaden 

Logboekbestanden zijn een essentiële resource voor het debuggen van de Azure ML-workloads. Nadat u een trainings job hebt verzenden, zoomt u in op een specifieke run om de logboeken en uitvoer ervan weer te geven:  

1. Navigeer naar **het tabblad Experimenten.**
1. Selecteer de runID voor een specifieke run.
1. Selecteer **Uitvoer en logboeken** bovenaan de pagina.
2. Selecteer **Alles downloaden om** al uw logboeken te downloaden naar een zip-map.
3. U kunt ook afzonderlijke logboekbestanden downloaden door het logboekbestand te kiezen en **Downloaden te selecteren**

:::image type="content" source="media/how-to-log-view-metrics/download-logs.png" alt-text="Schermopname van de sectie Uitvoer en logboeken van een run.":::

In de onderstaande tabellen ziet u de inhoud van de logboekbestanden in de mappen die u in deze sectie ziet.

> [!NOTE]
> U ziet niet noodzakelijkerwijs elk bestand voor elke run. De 20_image_build_log*.txt wordt bijvoorbeeld alleen weergegeven wanneer er een nieuwe afbeelding wordt gemaakt (bijvoorbeeld wanneer u uw omgeving wijzigt).

#### <a name="azureml-logs-folder"></a>`azureml-logs` Map

|File  |Beschrijving  |
|---------|---------|
|20_image_build_log.txt     | Bouwlogboek van Docker-afbeelding voor de trainingsomgeving, optioneel, één per run. Alleen van toepassing bij het bijwerken van uw omgeving. Anders hergebruikt AML de in cache opgeslagen afbeelding. Als dit lukt, bevat de registergegevens van de afbeelding voor de bijbehorende afbeelding.         |
|55_azureml-execution-<node_id>.txt     | stdout/stderr-logboek van hosthulpprogramma, één per knooppunt. Haalt een afbeelding op naar het rekendoel. Opmerking: dit logboek wordt alleen weergegeven wanneer u rekenbronnen hebt beveiligd.         |
|65_job_prep-<node_id>.txt     |   stdout/stderr-logboek van jobvoorbereidingsscript, één per knooppunt. Download uw code naar rekendoel en gegevensstores (indien aangevraagd).       |
|70_driver_log(_x).txt      |  stdout/stderr log from AML control script and customer training script, one per process. **Standaarduitvoer van uw script. Dit bestand is de plek waar de logboeken van uw code (bijvoorbeeld afdruk instructies) worden weer geven.** In de meeste gevallen bewaakt u hier de logboeken.       |
|70_mpi_log.txt     |   MPI-frameworklogboek, optioneel, één per run. Alleen voor MPI-run.   |
|75_job_post-<node_id>.txt     |  stdout/stderr log of job release script, één per knooppunt. Verzend logboeken en laat de rekenbronnen terug naar Azure.        |
|process_info.jsop      |   laten zien welk proces op welk knooppunt wordt uitgevoerd.  |
|process_status.jsop      | processtatus weergeven, bijvoorbeeld als een proces niet is gestart, uitgevoerd of voltooid.         |

#### <a name="logs--azureml-folder"></a>`logs > azureml` Map

|File  |Beschrijving  |
|---------|---------|
|110_azureml.log      |         |
|job_prep_azureml.log     |   systeemlogboek voor jobvoorbereiding        |
|job_release_azureml.log     | systeemlogboek voor het vrijgeven van een taak        |

#### <a name="logs--azureml--sidecar--node_id-folder"></a>`logs > azureml > sidecar > node_id` Map

Wanneer sidecar is ingeschakeld, worden scripts voor taakvoorbereiding en jobre release uitgevoerd binnen de sidecar-container.  Er is één map voor elk knooppunt. 

|File  |Beschrijving  |
|---------|---------|
|start_cms.txt     |  Logboek van het proces dat begint wanneer Sidecar Container wordt gestart       |
|prep_cmd.txt      |   Logboek voor ContextManagers ingevoerd wanneer wordt uitgevoerd (sommige van `job_prep.py` deze inhoud wordt gestreamd naar `azureml-logs/65-job_prep` )       |
|release_cmd.txt     |  Logboek voor ComtextManagers afgesloten wanneer `job_release.py` wordt uitgevoerd        |

#### <a name="other-folders"></a>Andere mappen

Voor de training van taken op clusters met meerdere berekeningen zijn logboeken aanwezig voor elk knooppunt-IP-adres. De structuur voor elk knooppunt is hetzelfde als taken met één knooppunt. Er is nog een map met logboeken voor algemene uitvoering, stderr- en stdout-logboeken.

Azure Machine Learning registreert informatie uit verschillende bronnen tijdens de training, zoals AutoML of de Docker-container die de trainings job wordt uitgevoerd. Veel van deze logboeken worden niet gedocumenteerd. Als u problemen ondervindt en contact op kunt nemen met Microsoft Ondersteuning, kunnen deze logboeken mogelijk worden gebruikt tijdens het oplossen van problemen.


## <a name="interactive-logging-session"></a>Interactieve logboekregistratiesessie

Interactieve logboekregistratiesessies worden doorgaans gebruikt in notebookomgevingen. Met de methode [Experiment.start_logging()](/python/api/azureml-core/azureml.core.experiment%28class%29#start-logging--args----kwargs-) wordt een interactieve logboekregistratiesessie gestart. Alle metrische gegevens die tijdens de sessie zijn geregistreerd, worden toegevoegd aan het uitvoeringsrecord in het experiment. Met de methode [run.complete()](/python/api/azureml-core/azureml.core.run%28class%29#complete--set-status-true-) worden de sessies beëindigd en de uitvoering als voltooid gemarkeerd.

## <a name="scriptrun-logs"></a>ScriptRun-logboeken

In dit gedeelte leert u hoe u logboekcode in uitvoeringen toevoegt die zijn gemaakt toen de ScriptRunConfig werd geconfigureerd. Met de klasse [**ScriptRunConfig**](/python/api/azureml-core/azureml.core.scriptrunconfig)-kunt u scripts en omgevingen inkapselen voor herhaalbare uitvoeringen. U kunt deze optie ook gebruiken om een visuele Jupyter Notebook-widget weer te geven voor bewakingsdoeleinden.

In dit voorbeeld wordt een parameteropruiming uitgevoerd op alfawaarden en worden de resultaten vastgelegd met de methode [run.log()](/python/api/azureml-core/azureml.core.run%28class%29#log-name--value--description----).

1. Maak een trainingsscript dat de logica voor logboekregistratie bevat, `train.py`.

   [!code-python[](~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train.py)]


1. Verzend het script ```train.py``` voor uitvoering in een door de gebruiker beheerde omgeving. De volledige scriptmap wordt verzonden voor training.

   [!notebook-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb?name=src)]


   [!notebook-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb?name=run)]

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

## <a name="other-logging-sources"></a>Andere bronnen voor logboekregistratie

Met Azure Machine Learning kunnen tijdens de training ook gegevens van andere bronnen worden geregistreerd, zoals geautomatiseerde machine learning-uitvoeringen, of Docker-containers die de taken uitvoeren. Deze logboeken worden niet gedocumenteerd, maar als u problemen ondervindt en contact opneemt met Microsoft-ondersteuning, kunnen ze deze logboeken gebruiken tijdens het oplossen van problemen.

Raadpleeg [Hoe kan ik metrische gegevens registreren in de ontwerpfunctie](how-to-track-designer-experiments.md) voor informatie over het vastleggen van metrische gegevens in de Azure Machine Learning-ontwerpfunctie

## <a name="example-notebooks"></a>Voorbeeldnotebooks

In de volgende notebooks worden concepten in dit artikel gedemonstreerd:
* [how-to-use-azureml/training/train-on-local](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-on-local)
* [how-to-use-azureml/track-and-monitor-experiments/logging-api](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/logging-api)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Volgende stappen

Raadpleeg deze artikelen voor meer informatie over het gebruik van Azure Machine Learning:

* Bekijk een voorbeeld van hoe u het beste model registreert en implementeert in de zelfstudie [Een afbeeldingsclassificatiemodel trainen met Azure Machine Learning](tutorial-train-models-with-aml.md).