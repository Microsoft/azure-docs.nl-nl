---
title: MILLILITERs logboeken & metrische gegevens controleren en weer geven
titleSuffix: Azure Machine Learning
description: Bewaak uw ML experimenten en Bekijk metrische uitvoerings gegevens met Jupyter widgets en de Azure Machine Learning Studio.
services: machine-learning
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.service: machine-learning
ms.subservice: core
ms.date: 07/30/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: e86ea0d90ea267b1c9ceecc8fed6c3d7e5102eaf
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/07/2021
ms.locfileid: "102443570"
---
# <a name="monitor-and-view-ml-run-logs-and-metrics"></a>MILLILITERs logboeken en-metrische gegevens bewaken en weer geven

Meer informatie over het bewaken van Azure Machine Learning uitvoeringen en het weer geven van de logboeken. 

Wanneer u een experiment uitvoert, worden logboeken en metrische gegevens gestreamd voor u.  Daarnaast kunt u uw eigen toevoegen.  Zie [logboek registratie inschakelen in azure ml-trainings uitvoeringen](how-to-track-experiments.md)voor meer informatie.

De logboeken kunnen u helpen bij het vaststellen van fouten en waarschuwingen voor de uitvoering. Prestatie gegevens zoals para meters en model nauwkeurigheid helpen u bij het bijhouden en bewaken van uw uitvoeringen.

In dit artikel leert u hoe u Logboeken kunt weer geven met behulp van de volgende methoden:

> [!div class="checklist"]
> * Monitors worden uitgevoerd in de Studio
> * Monitors worden uitgevoerd met behulp van de Jupyter Notebook-widget
> * Automatische machine learning uitvoeringen bewaken
> * Uitvoer logboeken na voltooiing weer geven
> * Uitvoer logboeken weer geven in de Studio

Zie [trainings uitvoeringen starten, controleren en annuleren](how-to-manage-runs.md)voor algemene informatie over het beheren van uw experimenten.

## <a name="monitor-runs-using-the-jupyter-notebook-widget"></a>Monitor uitvoeringen met behulp van de Jupyter notebook-widget

Wanneer u de methode **ScriptRunConfig** gebruikt om uitvoeringen te verzenden, kunt u de voortgang van de uitvoering bekijken met behulp van de [Jupyter-widget](/python/api/azureml-widgets/azureml.widgets?preserve-view=true&view=azure-ml-py). Net als het indienen van de run, is de widget asynchroon en biedt deze elke 10-15 seconden live updates totdat de taak is voltooid.

Bekijk de widget Jupyter tijdens het wachten tot de uitvoering is voltooid.
    
```python
from azureml.widgets import RunDetails
RunDetails(run).show()
```

![Scherm opname van Jupyter notebook-widget](./media/how-to-track-experiments/run-details-widget.png)

U kunt ook een koppeling naar dezelfde weer gave in uw werk ruimte ophalen.

```python
print(run.get_portal_url())
```

## <a name="monitor-automated-machine-learning-runs"></a>Automatische machine learning uitvoeringen bewaken

Voor automatische machine learning uitvoeringen, voor het openen van de grafieken van een vorige uitvoering, vervangt u door `<<experiment_name>>` de juiste naam van het experiment:

```python
from azureml.widgets import RunDetails
from azureml.core.run import Run

experiment = Experiment (workspace, <<experiment_name>>)
run_id = 'autoML_my_runID' #replace with run_ID
run = Run(experiment, run_id)
RunDetails(run).show()
```

![Jupyter notebook-widget voor automatische Machine Learning](./media/how-to-track-experiments/azure-machine-learning-auto-ml-widget.png)

## <a name="show-output-upon-completion"></a>Uitvoer weer geven na voltooiing

Wanneer u **ScriptRunConfig** gebruikt, kunt u gebruiken ```run.wait_for_completion(show_output = True)``` om weer te geven wanneer de model training is voltooid. De ```show_output``` vlag geeft u uitgebreide uitvoer. Zie de sectie ScriptRunConfig voor meer informatie over het [inschakelen van logboek registratie](how-to-track-experiments.md#scriptrun-logs).

<a id="queryrunmetrics"></a>

## <a name="view-run-metrics"></a>Metrische uitvoerings gegevens weer geven

## <a name="via-the-sdk"></a>Via de SDK
U kunt de metrische gegevens van een getraind model weer geven met behulp van ```run.get_metrics()``` . Zie het voorbeeld hieronder. 

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

## <a name="view-run-records-in-the-studio"></a>Uitvoerings records weer geven in de Studio

In de [Azure machine learning Studio](https://ml.azure.com)kunt u voltooide records voor uitvoeren bekijken, inclusief vastgelegde metrische gegevens.

Navigeer naar het tabblad **experimenten** . Als u al uw uitvoeringen in uw werk ruimte in verschillende experimenten wilt weer geven, selecteert u het tabblad **alle uitvoeringen** . U kunt inzoomen op uitvoeringen voor specifieke experimenten door het experiment filter toe te passen op de bovenste menu balk.

Selecteer in de weer gave individueel experiment het tabblad **alle experimenten** . In het dash board voor experimenteel uitvoeren kunt u bijgehouden metrische gegevens en logboeken voor elke uitvoering bekijken. 

U kunt ook de tabel uitvoerings lijst bewerken om meerdere uitvoeringen te selecteren en de laatste, minimale of maximale vastgelegde waarde voor uw uitvoeringen weer te geven. Pas uw grafieken aan om de vastgelegde metrische waarden en aggregaties over meerdere uitvoeringen te vergelijken. 

![Details uitvoeren in de Azure Machine Learning Studio](media/how-to-track-experiments/experimentation-tab.gif)

### <a name="view-log-files-for-a-run"></a>Logboek bestanden weer geven voor een uitvoering 

Logboek bestanden zijn een essentiële bron voor het opsporen van fouten in de Azure ML-workloads. Inzoomen op een specifieke uitvoering om de logboeken en uitvoer weer te geven:  

1. Navigeer naar het tabblad **experimenten** .
1. Selecteer de runID voor een specifieke uitvoering.
1. Selecteer **uitvoer en logboeken** boven aan de pagina.

:::image type="content" source="media/how-to-monitor-view-training-logs/view-logs.png" alt-text="Scherm afbeelding van de sectie uitvoer en logboeken van een uitvoering":::

In de volgende tabellen ziet u de inhoud van de logboek bestanden in de mappen die in deze sectie worden weer gegeven.

> [!NOTE]
> U hoeft niet per se elk bestand te zien voor elke uitvoering. Bijvoorbeeld: de 20_image_build_log *. txt wordt alleen weer gegeven wanneer een nieuwe installatie kopie wordt gemaakt (bijvoorbeeld wanneer u de omgeving wijzigt).

#### <a name="azureml-logs-folder"></a>`azureml-logs` map

|File  |Beschrijving  |
|---------|---------|
|20_image_build_log.txt     | Docker-installatie kopie voor het maken van een logboek voor de trainings omgeving, optioneel, één per run. Alleen van toepassing bij het bijwerken van uw omgeving. Anders wordt de cache afbeelding door andere AML opnieuw gebruikt. Als dit lukt, bevat de details van de installatie kopie van het REGI ster voor de bijbehorende installatie kopie.         |
|55_azureml-Execution-<node_id # C1.txt     | stdout/stderr-logboek van het hulp programma host, één per knoop punt. Hiermee wordt de afbeelding opgehaald naar het reken doel. Opmerking: dit logboek wordt alleen weer gegeven wanneer u de reken resources hebt beveiligd.         |
|65_job_prep-<node_id # C1.txt     |   stdout/stderr-logboek van taak voorbereidings script, één per knoop punt. Down load uw code naar berekenings doel en gegevens opslag (indien gewenst).       |
|70_driver_log (_x). txt      |  stdout/stderr-logboek van het AML-besturings script en het trainings script van de klant, één per proces. **Dit is de standaard uitvoer van uw script. Hier worden de logboeken van uw code (zoals afdruk instructies) weer gegeven.** In de meeste gevallen zult u de logboeken hier bewaken.       |
|70_mpi_log.txt     |   MPI Framework-logboek, optioneel, één per run. Alleen voor MPI-uitvoering.   |
|75_job_post-<node_id # C1.txt     |  stdout/stderr-logboek van taak release script, één per knoop punt. Logboeken verzenden, de reken bronnen weer vrijgeven naar Azure.        |
|process_info.jsop      |   weer geven welk proces wordt uitgevoerd op welk knoop punt.  |
|process_status.jsop      | proces status weer geven, d.w.z. als een proces niet is gestart, wordt uitgevoerd of voltooid.         |

#### <a name="logs--azureml-folder"></a>`logs > azureml` map

|File  |Beschrijving  |
|---------|---------|
|110_azureml. log      |         |
|job_prep_azureml. log     |   systeem logboek voor taak voorbereiding        |
|job_release_azureml. log     | systeem logboek voor taak release        |

#### <a name="logs--azureml--sidecar--node_id-folder"></a>`logs > azureml > sidecar > node_id` map

Als zijspan wagen is ingeschakeld, worden taak voorbereiding en taak release scripts uitgevoerd in de container voor de zijspan wagen.  Er is één map voor elk knoop punt. 

|File  |Beschrijving  |
|---------|---------|
|start_cms.txt     |  Logboek van het proces dat wordt gestart wanneer de container voor de zijspan wagen wordt gestart       |
|prep_cmd.txt      |   Het logboek voor ContextManagers wordt ingevoerd wanneer `job_prep.py` wordt uitgevoerd (sommige van deze worden gestreamd naar `azureml-logs/65-job_prep` )       |
|release_cmd.txt     |  Logboek voor ComtextManagers afgesloten wanneer `job_release.py` wordt uitgevoerd        |

#### <a name="other-folders"></a>Andere mappen

Voor taken training op meerdere compute-clusters zijn logboeken aanwezig voor elk IP-adres van knoop punten. De structuur voor elk knoop punt is hetzelfde als de taken van een enkel knoop punt. Er is één extra map Logboeken voor algemene uitvoer-, stderr-en stdout-Logboeken.

Azure Machine Learning registreert gegevens uit verschillende bronnen tijdens de training, zoals AutoML of de docker-container die de trainings taak uitvoert. Veel van deze logboeken zijn niet gedocumenteerd. Als u problemen ondervindt en contact opneemt met micro soft ondersteuning, kunnen ze deze logboeken gebruiken tijdens het oplossen van problemen.

## <a name="monitor-a-compute-cluster"></a>Een berekenings cluster bewaken

Voer de volgende stappen uit om de uitvoering van een specifiek reken doel vanuit uw browser te controleren:

1. Selecteer in de [Azure machine learning Studio](https://ml.azure.com/)uw werk ruimte en selecteer vervolgens __Compute__ in de linkerkant van de pagina.

1. Selecteer __trainings clusters__ om een lijst weer te geven met reken doelen die worden gebruikt voor de training. Selecteer vervolgens het cluster.

    ![Het trainings cluster selecteren](./media/how-to-track-experiments/select-training-compute.png)

1. Selecteer __uitvoeren__. De lijst met uitvoeringen die gebruikmaken van dit cluster wordt weer gegeven. Als u Details voor een specifieke uitvoering wilt weer geven, gebruikt u de koppeling in de kolom __uitvoeren__ . Als u Details voor het experiment wilt weer geven, gebruikt u de koppeling in de kolom __experiment__ .

    ![Uitvoeringen voor trainings cluster selecteren](./media/how-to-track-experiments/show-runs-for-compute.png)
    
    > [!TIP]
    > Omdat de doelen van de trainings Compute een gedeelde resource zijn, kunnen ze op een bepaald moment meerdere uitvoeringen in de wachtrij plaatsen of actief zijn.
    > 
    > Een run kan onderliggende uitvoeringen bevatten, waardoor één trainings taak kan leiden tot meerdere vermeldingen.

Zodra een uitvoering is voltooid, wordt deze niet meer weer gegeven op deze pagina. Als u informatie over voltooide uitvoeringen wilt bekijken, gaat u naar de sectie __experimenten__ van Studio en selecteert u het experiment en voert u uit. Zie metrische gegevens van de sectie [weergave voor voltooide uitvoeringen](#view-the-experiment-in-the-web-portal)voor meer informatie.


## <a name="next-steps"></a>Volgende stappen

Voer de volgende stappen uit om te leren hoe u Azure Machine Learning gebruikt:

* Meer informatie over het [bijhouden van experimenten en het inschakelen van Logboeken in de Azure machine learning Designer](how-to-track-designer-experiments.md).

* Bekijk een voorbeeld van hoe u het beste model registreert en implementeert in de zelfstudie [Een afbeeldingsclassificatiemodel trainen met Azure Machine Learning](tutorial-train-models-with-aml.md).
