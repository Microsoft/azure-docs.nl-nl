---
title: Probleem met de ParallelRunStep oplossen
titleSuffix: Azure Machine Learning
description: Tips voor het oplossen van problemen bij het ophalen van fouten met behulp van de ParallelRunStep in machine learning pijp lijnen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: troubleshooting
ms.custom: troubleshooting
ms.reviewer: larryfr, vaidyas, laobri, tracych
ms.author: trmccorm
author: tmccrmck
ms.date: 09/23/2020
ms.openlocfilehash: a0f813253520d76731a9b49a89b0bcace7c2ef34
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/09/2021
ms.locfileid: "99979161"
---
# <a name="troubleshooting-the-parallelrunstep"></a>Probleem met de ParallelRunStep oplossen

In dit artikel leert u hoe u problemen kunt oplossen wanneer u fouten krijgt met behulp van de [ParallelRunStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.parallel_run_step.parallelrunstep?preserve-view=true&view=azure-ml-py) -klasse van de [Azure machine learning SDK](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py).

Zie [problemen met machine learning pijp lijnen oplossen](how-to-debug-pipelines.md)voor algemene tips voor het oplossen van problemen met een pijp lijn.

## <a name="testing-scripts-locally"></a>Scripts lokaal testen

 Uw ParallelRunStep wordt uitgevoerd als een stap in ML-pijp lijnen. Mogelijk wilt u [uw scripts lokaal testen](how-to-debug-visual-studio-code.md#debug-and-troubleshoot-machine-learning-pipelines) als een eerste stap.

##  <a name="script-requirements"></a>Script vereisten

Het script voor een `ParallelRunStep` *moet* twee functies bevatten:
- `init()`: Gebruik deze functie voor een kostbare of algemene voorbereiding voor latere deductie. Gebruik het bijvoorbeeld om het model in een algemeen object te laden. Deze functie wordt slecht één keer aangeroepen, aan het begin van het proces.
-  `run(mini_batch)`: De functie wordt uitgevoerd voor elk exemplaar van `mini_batch`.
    -  `mini_batch`: `ParallelRunStep` roept de uitvoermethode aan en geeft een lijst of pandas-`DataFrame` op als argument aan de methode. Elke vermelding in mini_batch is een bestandspad als de invoer een `FileDataset` is, of een pandas-`DataFrame` als de invoer een `TabularDataset` is.
    -  `response`: run()-methode moet resulteren in een pandas-`DataFrame` of een matrix. Voor append_row output_action worden deze geretourneerde elementen toegevoegd aan het algemene uitvoerbestand. Voor summary_only wordt de inhoud van de elementen genegeerd. Voor alle uitvoeracties geeft elk geretourneerde uitvoerelement een geslaagde uitvoering van een invoerelement in de mini-batch aan. Zorg dat er voldoende gegevens worden opgenomen in het resultaat van de uitvoering om invoer toe te wijzen aan de uitvoerresultaten. Uitvoer wordt naar het uitvoerbestand geschreven en bevindt zich niet altijd in de juiste volgorde. Gebruik een sleutel in de uitvoer om deze toe te wijzen aan de invoer.

```python
%%writefile digit_identification.py
# Snippets from a sample script.
# Refer to the accompanying digit_identification.py
# (https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines/parallel-run)
# for the implementation script.

import os
import numpy as np
import tensorflow as tf
from PIL import Image
from azureml.core import Model


def init():
    global g_tf_sess

    # Pull down the model from the workspace
    model_path = Model.get_model_path("mnist")

    # Construct a graph to execute
    tf.reset_default_graph()
    saver = tf.train.import_meta_graph(os.path.join(model_path, 'mnist-tf.model.meta'))
    g_tf_sess = tf.Session()
    saver.restore(g_tf_sess, os.path.join(model_path, 'mnist-tf.model'))


def run(mini_batch):
    print(f'run method start: {__file__}, run({mini_batch})')
    resultList = []
    in_tensor = g_tf_sess.graph.get_tensor_by_name("network/X:0")
    output = g_tf_sess.graph.get_tensor_by_name("network/output/MatMul:0")

    for image in mini_batch:
        # Prepare each image
        data = Image.open(image)
        np_im = np.array(data).reshape((1, 784))
        # Perform inference
        inference_result = output.eval(feed_dict={in_tensor: np_im}, session=g_tf_sess)
        # Find the best probability, and add it to the result list
        best_result = np.argmax(inference_result)
        resultList.append("{}: {}".format(os.path.basename(image), best_result))

    return resultList
```

Als u een ander bestand of een andere map hebt in dezelfde map als uw deductiescript, dan kunt u ernaar verwijzen door de huidige werkmap te zoeken.

```python
script_dir = os.path.realpath(os.path.join(__file__, '..',))
file_path = os.path.join(script_dir, "<file_name>")
```

### <a name="parameters-for-parallelrunconfig"></a>Para meters voor ParallelRunConfig

`ParallelRunConfig` is de voornaamste configuratie voor `ParallelRunStep`-exemplaar in de Azure Machine Learning-pijplijn. U gebruikt dit om uw script te verpakken en de nodige parameters te configureren, waaronder alle volgende vermeldingen:
- `entry_script`: Een gebruikersscript als een lokaal bestandspad dat parallel wordt uitgevoerd op meerdere knooppunten. Als `source_directory` aanwezig is, gebruikt dan een relatief pad. Zoniet, gebruik dan een pad dat toegankelijk is op de machine.
- `mini_batch_size`: De grootte van de mini-batch die is doorgegeven aan een enkele `run()`-aanroep. (optioneel; de standaardwaarde is `10`-bestanden voor `FileDataset` en `1MB` voor `TabularDataset`.)
    - Voor `FileDataset` is dit het aantal bestanden met een minimumwaarde `1`. U kunt meerdere bestanden combineren in één mini-batch.
    - Voor `TabularDataset` is dit de grootte van de gegevens. Voorbeeldwaarden zijn `1024`, `1024KB`, `10MB` en `1GB`. De aanbevolen waarde is `1MB`. De mini-batch van `TabularDataset` zal nooit bestandsgrenzen overschrijden. Als u bijvoorbeeld een .csv-bestand heeft met verschillende groottes, dan is het kleinste bestan 100 KB en het grootste 10 MB. Als u `mini_batch_size = 1MB` instelt, worden bestanden met een grootte van minder dan 1 MB beschouwd als één mini-batch. Bestanden met een grootte van meer dan 1 MB, worden in verschillende mini-batches opgedeeld.
- `error_threshold`: Het aantal recordfouten voor `TabularDataset` en bestandsfouten voor `FileDataset` die tijdens de verwerking genegeerd mogen worden. Als het aantal fouten voor de volledige invoer boven deze waarde komt, wordt de taak afgebroken. De drempelwaarde voor fouten geldt voor de volledige invoer, niet voor afzonderlijke mini-batches die naar de `run()`-methode verzonden worden. Het bereik is `[-1, int.max]`. Het deel `-1` geeft aan dat alle fouten tijdens de verwerking genegeerd worden.
- `output_action`: Een van de volgende waarden geeft aan wat er met de uitvoer gebeurt:
    - `summary_only`: Het gebruikersscript slaat de uitvoer op. `ParallelRunStep` gebruikt de uitvoer enkel voor de berekening van de drempelwaarde voor fouten.
    - `append_row`: Er wordt slechts één bestand gemaakt in de uitvoermap voor alle invoerwaarden, waarbij elke uitvoerwaarde een aparte regel krijgt.
- `append_row_file_name`: Om de naam van het uitvoerbestand voor append_row output_action aan te passen (optioneel; de standaardwaarde is `parallel_run_step.txt`).
- `source_directory`: Paden naar mappen met alle bestanden die moeten worden uitgevoerd op het rekendoel (optioneel).
- `compute_target`: Alleen `AmlCompute` wordt ondersteund.
- `node_count`: Het aantal rekenknooppunten dat moet worden gebruikt voor de uitvoering van het gebruikersscript.
- `process_count_per_node`: Het aantal processen per knooppunt. Het is aanbevolen om het aantal GPU of CPU voor één knooppunt in te stellen (optioneel; standaardwaarde is `1`).
- `environment`: De definitie van de Python-omgeving. U kunt deze configureren om een bestaande Python-omgeving te gebruiken of om een tijdelijke omgeving in te stellen. De definitie zorgt er ook voor dat de vereiste toepassingsafhankelijkheden worden ingesteld (optioneel).
- `logging_level`: Uitgebreidheid van het logboek. De waarden om uitgebreidheid te verhogen zijn: `WARNING`, `INFO` en `DEBUG`. (optioneel; de standaardwaarde is `INFO`)
- `run_invocation_timeout`: De aanroeptime-out voor de `run()`-methode in seconden. (optioneel; standaardwaarde is `60`)
- `run_max_try`: Maximum aantal pogingen van `run()` voor een mini-batch. Een `run()` is mislukt als er een uitzondering wordt gegenereerd of er niets wordt geretourneerd wanneer `run_invocation_timeout` is bereikt (optioneel; de standaardwaarde is `3`). 

U kunt `mini_batch_size`, `node_count`, `process_count_per_node`, `logging_level`, `run_invocation_timeout` en `run_max_try` als `PipelineParameter` opgeven, zodat u de parameterwaarden kunt aanpassen wanneer u een pijplijnuitvoering opnieuw verzendt. In dit voorbeeld gebruikt u `PipelineParameter` voor `mini_batch_size` en `Process_count_per_node`. Wanneer u later een uitvoering opnieuw verzendt, verandert u deze waarden. 

### <a name="parameters-for-creating-the-parallelrunstep"></a>Para meters voor het maken van de ParallelRunStep

Maak de ParallelRunStep met het script, de omgevingsconfiguratie en de parameters. Geef het rekendoel op dat u al aan uw werkruimte hebt gekoppeld als het doel van de uitvoering voor uw deductiescript. Gebruik `ParallelRunStep` om de stap voor de batchdeductiepijplijn te maken, met de volgende parameters:
- `name`: De naam van de stap, met de volgende beperkingen voor de naamgeving: uniek, 3-32 tekens en regex ^\[a-z\]([-a-z0-9]*[a-z0-9])?$.
- `parallel_run_config`: Een `ParallelRunConfig`-object, zoals eerder gedefinieerd.
- `inputs`: Een of meer Azure Machine Learning-gegevenssets van één type die moeten worden gepartitioneerd voor parallelle verwerking.
- `side_inputs`: Een of meer referentiegegevens of gegevenssets die worden gebruikt als aanvullende invoerwaarden en niet gepartitioneerd moeten worden.
- `output`: Een `PipelineData`-object dat overeenkomt met de uitvoermap.
- `arguments`: Een lijst van argumenten die worden doorgegeven aan het gebruikersscript. Gebruik unknown_args om deze op te halen in uw invoerscript (optioneel).
- `allow_reuse`: Of de stap vorige resultaten moet hergebruiken wanneer dezelfde instellingen/invoerwaarden worden uitgevoerd. Als deze parameter `False` is, dan wordt er altijd een nieuwe uitvoering gegenereerd voor deze stap tijdens pijplijnuitvoering. (optioneel; de standaardwaarde is `True`.)

```python
from azureml.pipeline.steps import ParallelRunStep

parallelrun_step = ParallelRunStep(
    name="predict-digits-mnist",
    parallel_run_config=parallel_run_config,
    inputs=[input_mnist_ds_consumption],
    output=output_dir,
    allow_reuse=True
)
```

## <a name="debugging-scripts-from-remote-context"></a>Fouten opsporen in scripts in externe context

De overgang van het opsporen van fouten in een score script voor het opsporen van fouten in een score script in een pijp lijn kan lastig zijn. Zie voor informatie over het vinden van uw logboeken in de portal  [machine learning sectie pijp lijnen over het opsporen van fouten in scripts van een externe context](how-to-debug-pipelines.md). De informatie in deze sectie is ook van toepassing op een ParallelRunStep.

Het logboek bestand `70_driver_log.txt` bevat bijvoorbeeld informatie van de controller die de ParallelRunStep-code start.

Vanwege de gedistribueerde aard van ParallelRunStep-taken zijn er logboeken van verschillende bronnen. Er worden echter twee geconsolideerde bestanden gemaakt die gegevens op hoog niveau bieden:

- `~/logs/job_progress_overview.txt`: Dit bestand bevat informatie op hoog niveau over het aantal Mini-batches (ook wel taken genoemd) dat tot nu toe is gemaakt en het aantal verwerkte mini batches tot nu toe. Nu wordt het resultaat van de taak weer gegeven. Als de taak is mislukt, wordt het fout bericht weer gegeven en wordt aangegeven waar de probleem oplossing moet worden gestart.

- `~/logs/sys/master_role.txt`: Dit bestand bevat het hoofd knooppunt (ook wel bekend als Orchestrator) voor het uitvoeren van de taak. Omvat het maken van taken, voortgangs bewaking, het resultaat van de uitvoering.

Logboeken die zijn gegenereerd op basis van een invoer script met behulp van EntryScript-helper-en-afdruk instructies, worden in de volgende bestanden

- `~/logs/user/entry_script_log/<ip_address>/<process_name>.log.txt`: Deze bestanden zijn de logboeken geschreven van entry_script met behulp van de EntryScript-helper.

- `~/logs/user/stdout/<ip_address>/<process_name>.stdout.txt`: Deze bestanden zijn de logboeken van stdout (bijvoorbeeld Afdruk instructie) van entry_script.

- `~/logs/user/stderr/<ip_address>/<process_name>.stderr.txt`: Deze bestanden zijn de logboeken van stderr van entry_script.

Er is sprake van een beknopt overzicht van fouten in uw script:

- `~/logs/user/error.txt`: In dit bestand wordt geprobeerd de fouten in uw script samen te vatten.

Voor meer informatie over fouten in uw script, geldt het volgende:

- `~/logs/user/error/`: Bevat volledige stack traceringen van uitzonde ringen die zijn opgetreden tijdens het laden en uitvoeren van het invoer script.

Als u wilt weten hoe elk knoop punt het Score script heeft uitgevoerd, bekijkt u de afzonderlijke proces logboeken voor elk knoop punt. De proces logboeken kunnen worden gevonden in de `sys/node` map, gegroepeerd op worker-knoop punten:

- `~/logs/sys/node/<ip_address>/<process_name>.txt`: Dit bestand bevat gedetailleerde informatie over elke mini-batch tijdens het ophalen of volt ooien van een werk nemer. Voor elke mini-batch bestaat dit bestand uit:

    - Het IP-adres en de PID van het werk proces. 
    - Het totale aantal items, het aantal items dat is verwerkt en het aantal mislukte items.
    - De tijd van begin tijd, duur, proces tijd en uitvoerings methode.

U kunt ook de resultaten van periodieke controles van het resource gebruik voor elk knoop punt weer geven. De logboek bestanden en installatie bestanden bevinden zich in deze map:

- `~/logs/perf`: Stel `--resource_monitor_interval` in dat het controle-interval in seconden moet worden gewijzigd. Het standaard interval is `600` ongeveer 10 minuten. Als u de bewaking wilt stoppen, stelt u de waarde in op `0` . Elke `<ip_address>` map bevat:

    - `os/`: Informatie over alle actieve processen in het knoop punt. Met één controle wordt de opdracht van een besturings systeem uitgevoerd en wordt het resultaat opgeslagen in een bestand. In Linux is dit de opdracht `ps` . Gebruik in Windows `tasklist` .
        - `%Y%m%d%H`: De naam van de submap is de tijd tot uur.
            - `processes_%M`: Het bestand eindigt op de minuut van de controle tijd.
    - `node_disk_usage.csv`: Gedetailleerd schijf gebruik van het knoop punt.
    - `node_resource_usage.csv`: Overzicht van resource gebruik van het knoop punt.
    - `processes_resource_usage.csv`: Overzicht van resource gebruik van elk proces.

### <a name="how-do-i-log-from-my-user-script-from-a-remote-context"></a>Hoe kan ik logboek van mijn gebruikers script vanuit een externe context?

ParallelRunStep kan meerdere processen uitvoeren op één knoop punt op basis van process_count_per_node. Als u logboeken van elk proces wilt ordenen op basis van het knoop punt en afdruk-en logboek instructie combi neren, raden we u aan om ParallelRunStep logger te gebruiken zoals hieronder wordt weer gegeven. U ontvangt een logboek van EntryScript en zorgt ervoor dat de logboeken worden weer gegeven in **Logboeken/gebruikers** mappen in de portal.

**Een voor beeld van een invoer script met behulp van het logboek:**
```python
from azureml_user.parallel_run import EntryScript

def init():
    """ Initialize the node."""
    entry_script = EntryScript()
    logger = entry_script.logger
    logger.debug("This will show up in files under logs/user on the Azure portal.")


def run(mini_batch):
    """ Accept and return the list back."""
    # This class is in singleton pattern and will return same instance as the one in init()
    entry_script = EntryScript()
    logger = entry_script.logger
    logger.debug(f"{__file__}: {mini_batch}.")
    ...

    return mini_batch
```

### <a name="how-could-i-pass-a-side-input-such-as-a-file-or-files-containing-a-lookup-table-to-all-my-workers"></a>Hoe kan ik een invoer van een zijde, zoals een bestand of bestand (en) met een opzoek tabel, aan al mijn werk rollen door geven?

De gebruiker kan referentie gegevens door geven aan een script met behulp van side_inputs para meter ParalleRunStep. Alle gegevens sets die als side_inputs worden gegeven, worden op elk worker-knoop punt gekoppeld. De gebruiker kan de locatie van de koppeling verkrijgen door het argument door te geven.

Maak een [gegevensset](/python/api/azureml-core/azureml.core.dataset.dataset?preserve-view=true&view=azure-ml-py) met de referentie gegevens en Registreer deze bij uw werk ruimte. Geef deze door aan de `side_inputs` para meter van uw `ParallelRunStep` . Daarnaast kunt u het pad in de sectie toevoegen `arguments` om eenvoudig toegang te krijgen tot het gekoppelde pad:

```python
label_config = label_ds.as_named_input("labels_input")
batch_score_step = ParallelRunStep(
    name=parallel_step_name,
    inputs=[input_images.as_named_input("input_images")],
    output=output_dir,
    arguments=["--labels_dir", label_config],
    side_inputs=[label_config],
    parallel_run_config=parallel_run_config,
)
```

Nadat u dit hebt geopend in uw Afleidings script (bijvoorbeeld in de methode init ()), als volgt:

```python
parser = argparse.ArgumentParser()
parser.add_argument('--labels_dir', dest="labels_dir", required=True)
args, _ = parser.parse_known_args()

labels_path = args.labels_dir
```

### <a name="how-to-use-input-datasets-with-service-principal-authentication"></a>Hoe gebruik ik invoer gegevens sets met Service-Principal-verificatie?

Gebruiker kan invoer gegevens sets door geven met Service-Principal-verificatie die in de werk ruimte wordt gebruikt. Als u een dergelijke gegevensset in ParallelRunStep wilt gebruiken, moet u de gegevensset registreren om de ParallelRunStep-configuratie te maken.

```python
service_principal = ServicePrincipalAuthentication(
    tenant_id="***",
    service_principal_id="***",
    service_principal_password="***")
 
ws = Workspace(
    subscription_id="***",
    resource_group="***",
    workspace_name="***",
    auth=service_principal
    )
 
default_blob_store = ws.get_default_datastore() # or Datastore(ws, '***datastore-name***') 
ds = Dataset.File.from_files(default_blob_store, '**path***')
registered_ds = ds.register(ws, '***dataset-name***', create_new_version=True)
```

## <a name="next-steps"></a>Volgende stappen

* Bekijk deze [Jupyter-notebooks die Azure machine learning pijp lijnen demonstreren](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines)

* Raadpleeg de SDK-Naslag informatie voor hulp met het pakket met de stappen voor de [azureml-pipelines](/python/api/azureml-pipeline-steps/azureml.pipeline.steps?preserve-view=true&view=azure-ml-py) . Referentie [documentatie](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.parallelrunstep?preserve-view=true&view=azure-ml-py) voor de klasse ParallelRunStep weer geven.

* Volg de [Geavanceerde zelf studie](tutorial-pipeline-batch-scoring-classification.md) over het gebruik van pijp lijnen met ParallelRunStep. De zelf studie laat zien hoe u een ander bestand als een kantlijn invoer kunt door geven.
