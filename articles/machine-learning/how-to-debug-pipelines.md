---
title: Problemen oplossen met ML-pijp lijnen
titleSuffix: Azure Machine Learning
description: Problemen oplossen wanneer u fouten krijgt met een machine learning pijp lijn. Veelvoorkomende Valk uilen en tips voor het oplossen van fouten in scripts voor en tijdens externe uitvoering.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: lobrien
ms.author: laobri
ms.date: 10/22/2020
ms.topic: troubleshooting
ms.custom: troubleshooting, devx-track-python, contperf-fy21q2
ms.openlocfilehash: 195942d1787cdef51ee480fa5c5595db99bc7c78
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102522084"
---
# <a name="troubleshooting-machine-learning-pipelines"></a>Problemen met machine learning pijp lijnen oplossen

In dit artikel leert u hoe u problemen kunt oplossen wanneer er fouten optreden tijdens het uitvoeren van een [machine learning pijp lijn](concept-ml-pipelines.md) in de [Azure Machine Learning SDK](/python/api/overview/azure/ml/intro) en [Azure machine learning Designer](./concept-designer.md). 

## <a name="troubleshooting-tips"></a>Tips voor probleemoplossing

De volgende tabel bevat veelvoorkomende problemen bij het ontwikkelen van pijp lijnen met mogelijke oplossingen.

| Probleem | Mogelijke oplossing |
|--|--|
| Kan geen gegevens door geven aan de `PipelineData` map | Zorg ervoor dat u een map hebt gemaakt in het script dat overeenkomt met de locatie van de gegevens van de stap uitvoer. In de meeste gevallen definieert een invoer argument de uitvoermap en maakt u de map expliciet. Gebruiken `os.makedirs(args.output_dir, exist_ok=True)` om de uitvoermap te maken. Raadpleeg de [zelf studie](tutorial-pipeline-batch-scoring-classification.md#write-a-scoring-script) voor een score voorbeeld script waarin dit ontwerp patroon wordt weer gegeven. |
| Afhankelijkheids fouten | Als er afhankelijkheids fouten in uw externe pijp lijn worden weer gegeven die niet zijn uitgevoerd tijdens het lokaal testen, moet u controleren of de afhankelijkheden en versies van de externe omgeving overeenkomen met die in uw test omgeving. (Zie [omgeving bouwen, in cache plaatsen en opnieuw gebruiken](./concept-environments.md#environment-building-caching-and-reuse)|
| Dubbel zinnige fouten met Compute-doelen | Probeer de reken doelen te verwijderen en opnieuw te maken. Het opnieuw maken van Compute-doelen is snel en kan tijdelijke problemen oplossen. |
| Pijp lijn waarbij de stappen niet opnieuw worden gebruikt | Het opnieuw gebruiken van de stap is standaard ingeschakeld, maar zorg ervoor dat u deze niet hebt uitgeschakeld in een pijplijn stap. Als opnieuw gebruiken is uitgeschakeld, `allow_reuse` wordt de para meter in de stap ingesteld op `False` . |
| De pijp lijn wordt onnodig opnieuw uitgevoerd | Om ervoor te zorgen dat de stappen alleen worden uitgevoerd wanneer de onderliggende gegevens of scripts worden gewijzigd, moet u de bron code mappen voor elke stap loskoppelen. Als u dezelfde bron directory voor meerdere stappen gebruikt, kan het nodig zijn om niet-uitgevoerde opnieuw uit te voeren. Gebruik de `source_directory` para meter voor een pijplijn stap object om naar uw geïsoleerde map voor die stap te verwijzen en zorg ervoor dat u niet hetzelfde `source_directory` pad gebruikt voor meerdere stappen. |
| Stap langzamer over trainings-epochen of andere herhalings gedrag | Schakel eventuele bestands schrijfopdrachten, inclusief logboek registratie, van `as_mount()` naar in `as_upload()` . De **koppel** modus maakt gebruik van een extern gevirtualiseerde bestands systeem en uploadt het hele bestand telkens wanneer het wordt toegevoegd aan. |
| Het starten van het reken doel duurt lang | Docker-installatie kopieën voor reken doelen worden geladen van Azure Container Registry (ACR). Azure Machine Learning maakt standaard een ACR die gebruikmaakt van de *Basic* -servicelaag. Het wijzigen van de ACR voor uw werk ruimte in de standaard-of Premium-laag kan de tijd verminderen die nodig is om installatie kopieën te bouwen en te laden. Zie [Azure container Registry service lagen](../container-registry/container-registry-skus.md)voor meer informatie. |

### <a name="authentication-errors"></a>Verificatiefouten

Als u een beheer bewerking uitvoert op een reken doel van een externe taak, wordt een van de volgende fouten weer gegeven: 

```json
{"code":"Unauthorized","statusCode":401,"message":"Unauthorized","details":[{"code":"InvalidOrExpiredToken","message":"The request token was either invalid or expired. Please try again with a valid token."}]}
```

```json
{"error":{"code":"AuthenticationFailed","message":"Authentication failed."}}
```

U ontvangt bijvoorbeeld een fout melding als u een reken doel probeert te maken of koppelen van een ML-pijp lijn die wordt ingediend voor externe uitvoering.
## <a name="troubleshooting-parallelrunstep"></a>Meer `ParallelRunStep` 

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
- `output`: Een `OutputFileDatasetConfig` object dat overeenkomt met de uitvoermap.
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

## <a name="debugging-techniques"></a>Technieken voor fout opsporing

Er zijn drie belang rijke technieken voor het opsporen van fouten in pijp lijnen: 

* Fout opsporing voor afzonderlijke pijplijn stappen op uw lokale computer
* Logboek registratie en Application Insights gebruiken om de oorzaak van het probleem te isoleren en vast te stellen
* Een externe debugger koppelen aan een pijp lijn die wordt uitgevoerd in azure

### <a name="debug-scripts-locally"></a>Scripts lokaal fouten opsporen

Een van de meest voorkomende storingen in een pijp lijn is dat het domein script niet wordt uitgevoerd zoals bedoeld, of dat er runtime-fouten in de externe Compute-context staan die lastig zijn om fouten op te sporen.

Pijp lijnen zelf kunnen niet lokaal worden uitgevoerd, maar als u de scripts in isolatie uitvoert op uw lokale computer, kunt u sneller fouten opsporen omdat u niet hoeft te wachten op het proces voor het bouwen van de berekening en de omgeving. Er is een aantal ontwikkel werkzaamheden vereist om dit te doen:

* Als uw gegevens zich in een Cloud-Data Store bevinden, moet u gegevens downloaden en deze beschikbaar maken voor uw script. Het gebruik van een klein voor beeld van uw gegevens is een goede manier om in runtime te verminderen en snel feedback te krijgen over het gedrag van scripts
* Als u probeert een tussenliggende pijplijn stap te simuleren, moet u mogelijk hand matig de object typen bouwen waarvan het specifieke script in de vorige stap verwacht.
* U moet ook uw eigen omgeving definiëren en de afhankelijkheden repliceren die zijn gedefinieerd in uw externe Compute-omgeving

Zodra u een script hebt ingesteld om uit te voeren op uw lokale omgeving, is het veel eenvoudiger om fouten op te sporen in taken zoals:

* Een aangepaste configuratie voor fout opsporing koppelen
* Uitvoering onderbreken en object status controleren
* Het opvangen van type of logische fouten die pas worden weer gegeven na runtime

> [!TIP] 
> Zodra u kunt controleren of uw script wordt uitgevoerd zoals verwacht, voert u een goede volgende stap uit om het script uit te voeren in een pijp lijn met één stap voordat u deze in een pijp lijn met meerdere stappen probeert uit te voeren.

## <a name="configure-write-to-and-review-pipeline-logs"></a>Pijplijn logboeken configureren, schrijven naar en bekijken

Het lokaal testen van scripts is een uitstekende manier om fouten op te sporen in primaire code fragmenten en complexe logica voordat u begint met het bouwen van een pijp lijn, maar op een bepaald moment moet u waarschijnlijk fouten opsporen in scripts tijdens het uitvoeren van de werkelijke pijp lijn zelf, met name bij het vaststellen van het gedrag dat optreedt tijdens de interactie tussen de stappen van de pijp lijn. We raden u aan om `print()` de instructies in uw stap scripts te gebruiken, zodat u de object status en verwachte waarden tijdens externe uitvoering kunt zien, vergelijkbaar met hoe u fouten opspoort in Java script-code.

### <a name="logging-options-and-behavior"></a>Opties voor logboek registratie en gedrag

De onderstaande tabel bevat informatie over verschillende opties voor fout opsporing voor pijp lijnen. Het is geen limitatieve lijst, omdat er andere opties zijn dan alleen de Azure Machine Learning, python en opentellingen die hier worden weer gegeven.

| Bibliotheek                    | Type   | Voorbeeld                                                          | Doel                                  | Resources                                                                                                                                                                                                                                                                                                                    |
|----------------------------|--------|------------------------------------------------------------------|----------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Azure Machine Learning-SDK | Metrisch | `run.log(name, val)`                                             | Gebruikers interface van Azure Machine Learning Portal             | [Experimenten bijhouden](how-to-track-experiments.md)<br>[de klasse azureml. core. run](/python/api/azureml-core/azureml.core.run%28class%29)                                                                                                                                                 |
| Python afdrukken/logboek registratie    | Logboek    | `print(val)`<br>`logging.info(message)`                          | Logboeken van Stuur Programma's, Azure Machine Learning Designer | [Experimenten bijhouden](how-to-track-experiments.md)<br><br>[Python-logboek registratie](https://docs.python.org/2/library/logging.html)                                                                                                                                                                       |
| OpenCensus Python          | Logboek    | `logger.addHandler(AzureLogHandler())`<br>`logging.log(message)` | Application Insights-traceringen                | [Fouten met pijplijnen opsporen in Application Insights](./how-to-log-pipelines-application-insights.md)<br><br>[OpenCensus Azure Monitor Exporters](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-azure)<br>[Python-logboek registratie Cookbook](https://docs.python.org/3/howto/logging-cookbook.html) |

#### <a name="logging-options-example"></a>Voor beeld van logboek registratie opties

```python
import logging

from azureml.core.run import Run
from opencensus.ext.azure.log_exporter import AzureLogHandler

run = Run.get_context()

# Azure ML Scalar value logging
run.log("scalar_value", 0.95)

# Python print statement
print("I am a python print statement, I will be sent to the driver logs.")

# Initialize python logger
logger = logging.getLogger(__name__)
logger.setLevel(args.log_level)

# Plain python logging statements
logger.debug("I am a plain debug statement, I will be sent to the driver logs.")
logger.info("I am a plain info statement, I will be sent to the driver logs.")

handler = AzureLogHandler(connection_string='<connection string>')
logger.addHandler(handler)

# Python logging with OpenCensus AzureLogHandler
logger.warning("I am an OpenCensus warning statement, find me in Application Insights!")
logger.error("I am an OpenCensus error statement with custom dimensions", {'step_id': run.id})
``` 

## <a name="azure-machine-learning-designer"></a>Azure Machine Learning-ontwerpprogramma

Voor pijp lijnen die in de ontwerp functie zijn gemaakt, kunt u het **70_driver_log** bestand vinden op de pagina ontwerpen of op de detail pagina van de pijplijn uitvoering.

### <a name="enable-logging-for-real-time-endpoints"></a>Logboek registratie inschakelen voor realtime-eind punten

Voor het oplossen van problemen met realtime-eind punten in de ontwerp functie moet u logboek registratie van toepassingen inschakelen met behulp van de SDK. Met logboek registratie kunt u problemen met model implementatie en-gebruik oplossen. Zie [logboek registratie voor geïmplementeerde modellen](./how-to-enable-app-insights.md)voor meer informatie. 

### <a name="get-logs-from-the-authoring-page"></a>Logboeken ophalen via de pagina ontwerpen

Wanneer u een pijplijn uitvoering verzendt en op de bewerkings pagina blijft, kunt u de logboek bestanden vinden die voor elke module worden gegenereerd, wanneer elke module is voltooid.

1. Selecteer een module die op het ontwerp canvas is uitgevoerd.
1. Ga in het rechterdeel venster van de module naar het tabblad  **uitvoer en logboeken** .
1. Vouw het rechterdeel venster uit en selecteer het **70_driver_log.txt** om het bestand in de browser weer te geven. U kunt Logboeken ook lokaal downloaden.

    ![Uitgevouwen uitvoer deel venster in de ontwerp functie](./media/how-to-debug-pipelines/designer-logs.png)

### <a name="get-logs-from-pipeline-runs"></a>Logboeken ophalen van pijplijn uitvoeringen

U kunt ook de logboek bestanden voor specifieke uitvoeringen vinden op de detail pagina pijp lijn uitvoeren, die u kunt vinden in de sectie **pijp lijnen** of **experimenten** van de Studio.

1. Selecteer een pijplijn uitvoering die in de ontwerp functie is gemaakt.

    ![Pagina pijplijn uitvoering](./media/how-to-debug-pipelines/designer-pipelines.png)

1. Selecteer een module in het voorbeeld venster.
1. Ga in het rechterdeel venster van de module naar het tabblad  **uitvoer en logboeken** .
1. Vouw het rechterdeel venster uit om het **70_driver_log.txt** -bestand in de browser weer te geven of selecteer het bestand om de logboeken lokaal te downloaden.

> [!IMPORTANT]
> Als u een pijp lijn wilt bijwerken op de pagina Details van de pijplijn uitvoering, moet u de pijplijn uitvoering **klonen** naar een nieuwe pijp lijn concept. Een pijplijn uitvoering is een moment opname van de pijp lijn. Het is vergelijkbaar met een logboek bestand en kan niet worden gewijzigd. 

## <a name="application-insights"></a>Application Insights
Voor meer informatie over het gebruik van de bibliotheek opentellingen python op deze manier raadpleegt u deze hand leiding: [fouten opsporen en problemen oplossen met machine learning pijp lijnen in Application Insights](./how-to-log-pipelines-application-insights.md)

## <a name="interactive-debugging-with-visual-studio-code"></a>Interactieve fout opsporing met Visual Studio code

In sommige gevallen moet u mogelijk interactief fouten opsporen in de python-code die wordt gebruikt in uw ML-pijp lijn. Door Visual Studio code (VS code) en debugpy te gebruiken, kunt u deze koppelen aan de code zoals deze wordt uitgevoerd in de trainings omgeving. Ga voor meer informatie naar de [richt lijnen voor interactieve fout opsporing in VS code](how-to-debug-visual-studio-code.md#debug-and-troubleshoot-machine-learning-pipelines).

## <a name="next-steps"></a>Volgende stappen

* Voor een volledige zelf studie `ParallelRunStep` raadpleegt u [zelf studie: een Azure machine learning pijp lijn bouwen voor batch scores](tutorial-pipeline-batch-scoring-classification.md).

* Zie [Automated ml gebruiken in een Azure machine learning-pijp lijn in python](how-to-use-automlstep-in-pipelines.md)voor een volledig voor beeld van automatische machine learning in ml-pijp lijnen.

* Raadpleeg de SDK-Naslag informatie voor hulp met het kern pakket voor [azureml-pipelines-core](/python/api/azureml-pipeline-core/) en het pakket met [azureml-pijp lijnen-stappen](/python/api/azureml-pipeline-steps/) .

* Bekijk de lijst met [uitzonde ringen en fout codes voor Designer](algorithm-module-reference/designer-error-codes.md).