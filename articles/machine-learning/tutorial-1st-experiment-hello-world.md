---
title: 'Zelfstudie: Een "Hallo wereld" uitvoeren Python-script'
titleSuffix: Azure Machine Learning
description: In deel 2 van de aan de slag-reeks van Azure Machine Learning ziet u hoe u een alledaags Python-script voor 'Hallo wereld' verzendt naar de cloud.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: aminsaied
ms.author: amsaied
ms.reviewer: sgilley
ms.date: 02/11/2021
ms.custom: devx-track-python
ms.openlocfilehash: 18f76480d1327d6ab41c475395a689f8024d7b25
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100369018"
---
# <a name="tutorial-run-a-hello-world-python-script-part-2-of-4"></a>Zelfstudie: Een Python-script voor 'Hallo wereld' uitvoeren (deel 2 van 4)

In deze zelfstudie leert u hoe u de Azure Machine Learning SDK voor Python kunt gebruiken om met een Python-script een 'Hallo wereld' te verzenden en uit te voeren.

Deze zelfstudie is *deel 2 van een vierdelige zelfstudiereeks* waarin u de grondbeginselen van Azure Machine Learning kunt zien en machine learning-taken op basis van taken uitvoert in Azure. Deze zelfstudie borduurt voort op het werk dat u hebt voltooid in [Deel 1: Uw lokale machine instellen voor de Azure Machine Learning](tutorial-1st-experiment-sdk-setup-local.md)

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een lokaal 'Hallo wereld'-script maken en uitvoeren.
> * Een Python-besturingsscript maken om 'Hallo wereld' te verzenden naar Azure Machine Learning.
> * Inzicht krijgen in de Azure Machine Learning-concepten in het besturingsscript
> * Het 'Hallo wereld'-script verzenden en uitvoeren.
> * De code-uitvoer weergeven in de cloud.

## <a name="prerequisites"></a>Vereisten

- Voltooien van [Deel 1](tutorial-1st-experiment-sdk-setup-local.md) als u nog geen Azure Machine Learning-werkruimte hebt.

## <a name="create-and-run-a-python-script-locally"></a>Een Python-script lokaal maken en uitvoeren

Maak een nieuwe submap met de naam `src` onder de `tutorial`-map om de code op te slaan die u op een Azure Machine Learning Compute-cluster wilt uitvoeren. Maak in de submap `src` het `hello.py` Python-script:

```python
# src/hello.py
print("Hello world!")
```

De structuur van uw projectmap ziet er nu als volgt uit:

:::image type="content" source="media/tutorial-1st-experiment-hello-world/directory-structure.png" alt-text="Mappen structuur toont hello.py in de submap src":::


### <a name="test-your-script-locally"></a><a name="test"></a>Uw script lokaal testen

U kunt uw code lokaal uitvoeren met behulp van uw favoriete IDE of een terminal. Code lokaal uitvoeren biedt het voordeel van interactieve foutopsporing.  Voer in het venster met de geactiveerde Conda-omgeving van *zelfstudie1* het Python-bestand uit:

```bash
cd <path/to/tutorial>
python ./src/hello.py
```

> [!div class="nextstepaction"]
> [Ik heb het script lokaal uitgevoerd](?success=run-local#control-script) [Er is een probleem opgetreden](https://www.research.net/r/7C2NTH7?issue=run-local)

## <a name="create-a-control-script"></a><a name="control-script"></a> Een besturingsscript maken

Met een *besturingsscript* kunt u uw `hello.py`-script uitvoeren in de cloud. Met het besturingsscript kunt u bepalen hoe en waar uw machine learning-code wordt uitgevoerd.  

Maak in uw zelfstudiemap een nieuw Python-bestand met de naam `03-run-hello.py` en kopieer/plak de volgende code in dat bestand:

```python
# tutorial/03-run-hello.py
from azureml.core import Workspace, Experiment, Environment, ScriptRunConfig

ws = Workspace.from_config()
experiment = Experiment(workspace=ws, name='day1-experiment-hello')

config = ScriptRunConfig(source_directory='./src', script='hello.py', compute_target='cpu-cluster')

run = experiment.submit(config)
aml_url = run.get_portal_url()
print(aml_url)
```

### <a name="understand-the-code"></a>De code begrijpen

Hier volgt een beschrijving van hoe het besturingsscript werkt:

:::row:::
   :::column span="":::
      `ws = Workspace.from_config()`
   :::column-end:::
   :::column span="2":::
      [Werkruimte](/python/api/azureml-core/azureml.core.workspace.workspace?preserve-view=true&view=azure-ml-py) maakt verbinding met uw Azure Machine Learning-werkruimte, zodat u kunt communiceren met uw Azure Machine Learning-resources.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `experiment =  Experiment( ... )`
   :::column-end:::
   :::column span="2":::
      [Experiment](/python/api/azureml-core/azureml.core.experiment.experiment?preserve-view=true&view=azure-ml-py) biedt een eenvoudige manier om meerdere uitvoeringen onder één naam te organiseren. Later kunt u zien hoe experimenten het eenvoudig maken om metrische gegevens te vergelijken tussen tientallen uitvoeringen.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `config = ScriptRunConfig( ... )` 
   :::column-end:::
   :::column span="2":::
      Met [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig?preserve-view=true&view=azure-ml-py) wordt uw `hello.py`-code ingepakt en doorgegeven aan uw werkruimte. Zoals de naam suggereert, kunt u deze klasse gebruiken om te _configureren_ hoe u wilt dat uw _script_ wordt _uitgevoerd_ in Azure Machine Learning. Het specificeert ook op welk rekendoel het script wordt uitgevoerd. In deze code is het doel het rekencluster dat u hebt gemaakt in de [installatiezelfstudie](tutorial-1st-experiment-sdk-setup-local.md).
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `run = experiment.submit(config)`
   :::column-end:::
   :::column span="2":::
       Hiermee wordt het script verzonden. Deze inzending wordt een [uitvoering](/python/api/azureml-core/azureml.core.run%28class%29?preserve-view=true&view=azure-ml-py) genoemd. Een uitvoering bevat één uitvoering van uw code. Gebruik een uitvoering om de voortgang van het script te bewaken, de uitvoer vast te leggen, de resultaten te analyseren, metrische gegevens te visualiseren en meer.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `aml_url = run.get_portal_url()` 
   :::column-end:::
   :::column span="2":::
        Het `run`-object biedt een ingang voor de uitvoering van uw code. Bewaak de voortgang vanuit de Azure Machine Learning Studio met de URL die wordt afgedrukt vanuit het Python-script.  
   :::column-end:::
:::row-end:::

> [!div class="nextstepaction"]
> [Ik heb het besturingsscript gemaakt](?success=create-control-script#submit) [Er is een probleem opgetreden](https://www.research.net/r/7C2NTH7?issue=create-control-script)

## <a name="submit-and-run-your-code-in-the-cloud"></a><a name="submit"></a> Uw code verzenden en uitvoeren in de cloud

Voer uw besturingsscript uit, dat op zijn beurt `hello.py` uitvoert op het rekencluster dat u hebt gemaakt in de [installatiezelfstudie](tutorial-1st-experiment-sdk-setup-local.md).


```bash
python 03-run-hello.py
```

> [!TIP]
> Als er bij het uitvoeren van deze code de foutmelding optreedt dat u geen toegang hebt tot het abonnement, gaat u naar [Verbinding maken met een werkruimte](how-to-manage-workspace.md?tab=python#connect-multi-tenant) voor informatie over verificatieopties.

> [!div class="nextstepaction"]
> [Ik heb code verzonden in de cloud](?success=submit-to-cloud#monitor) [Er is een probleem opgetreden](https://www.research.net/r/7C2NTH7?issue=submit-to-cloud)

## <a name="monitor-your-code-in-the-cloud-by-using-the-studio"></a><a name="monitor"></a>Uw code in de cloud bewaken met de studio

De uitvoer uit het script bevat een koppeling naar de studio die er ongeveer als volgt uitziet: `https://ml.azure.com/experiments/hello-world/runs/<run-id>?wsid=/subscriptions/<subscription-id>/resourcegroups/<resource-group>/workspaces/<workspace-name>`.

Volg de koppeling.  Eerst ziet u de status **Voorbereiden**.  De allereerste uitvoering duurt 5-10 minuten. Dit komt doordat het volgende gebeurt:

* Er wordt een Docker-installatiekopie gemaakt in de cloud
* De grootte van het berekeningscluster wordt gewijzigd van 0 naar 1 knooppunt
* De Docker-installatiekopie wordt gedownload naar de berekening. 

Volgende uitvoeringen verlopen veel sneller (~15 seconden), omdat de Docker-installatiekopie wordt opgeslagen in de cache van het rekenknooppunt. U kunt dit testen door de onderstaande code opnieuw te verzenden nadat de eerste uitvoering is voltooid.

Als de taak is voltooid, gaat u naar het tabblad **Uitvoer en logboeken**. Hier ziet u een bestand `70_driver_log.txt`, dat er als volgt uitziet:

```txt
 1: [2020-08-04T22:15:44.407305] Entering context manager injector.
 2: [context_manager_injector.py] Command line Options: Namespace(inject=['ProjectPythonPath:context_managers.ProjectPythonPath', 'RunHistory:context_managers.RunHistory', 'TrackUserError:context_managers.TrackUserError', 'UserExceptions:context_managers.UserExceptions'], invocation=['hello.py'])
 3: Starting the daemon thread to refresh tokens in background for process with pid = 31263
 4: Entering Run History Context Manager.
 5: Preparing to call script [ hello.py ] with arguments: []
 6: After variable expansion, calling script [ hello.py ] with arguments: []
 7:
 8: Hello world!
 9: Starting the daemon thread to refresh tokens in background for process with pid = 31263
10:
11:
12: The experiment completed successfully. Finalizing run...
13: Logging experiment finalizing status in history service.
14: [2020-08-04T22:15:46.541334] TimeoutHandler __init__
15: [2020-08-04T22:15:46.541396] TimeoutHandler __enter__
16: Cleaning up all outstanding Run operations, waiting 300.0 seconds
17: 1 items cleaning up...
18: Cleanup took 0.1812913417816162 seconds
19: [2020-08-04T22:15:47.040203] TimeoutHandler __exit__
```

Op regel 8 ziet u de 'Hallo wereld!' uitvoer.

Het `70_driver_log.txt`-bestand bevat de standaarduitvoer van een uitvoering. Dit bestand kan handig zijn bij het opsporen van fouten op externe uitvoeringen in de cloud.

> [!div class="nextstepaction"]
> [Ik hebt het logboek gezien in Studio](?success=monitor-in-studio#next-steps) [Er is een probleem opgetreden](https://www.research.net/r/7C2NTH7?issue=monitor-in-studio)

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een eenvoudig 'Hallo wereld'-script gemaakt en dit uitgevoerd in Azure. U hebt gezien hoe u verbinding maakt met uw Azure Machine Learning-werkruimte, een experiment maakt en uw `hello.py`-code naar de cloud verzendt.

In de volgende zelfstudie maakt u gebruik van de opgedane kennis en voert u iets interessanters uit dan `print("Hello world!")`.

> [!div class="nextstepaction"]
> [Zelfstudie: een model trainen](tutorial-1st-experiment-sdk-train.md)

>[!NOTE] 
> Als u de reeks zelfstudies nu wilt voltooien en niet wilt doorgaan met de volgende stap, moet u [uw resources opschonen](tutorial-1st-experiment-bring-data.md#clean-up-resources)
