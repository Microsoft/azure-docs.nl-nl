---
title: Problemen met ML-pijplijnen oplossen
titleSuffix: Azure Machine Learning
description: Problemen oplossen wanneer er fouten optreden bij het uitvoeren van een machine learning pijplijn. Veelvoorkomende valkuilen en tips voor het opsporen van fouten in uw scripts vóór en tijdens uitvoering op afstand.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: lobrien
ms.author: laobri
ms.date: 10/22/2020
ms.topic: troubleshooting
ms.custom: troubleshooting, devx-track-python, contperf-fy21q2
ms.openlocfilehash: 3a85566f395e97bbe88d52a8b306c7e0aa15669d
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107817337"
---
# <a name="troubleshooting-machine-learning-pipelines"></a>Problemen met pijplijnen machine learning oplossen

In dit artikel leert u hoe u problemen kunt oplossen wanneer er fouten optreden bij het uitvoeren van een [machine learning-pijplijn](concept-ml-pipelines.md) in de Azure Machine Learning [SDK](/python/api/overview/azure/ml/intro) en Azure Machine Learning [designer](./concept-designer.md). 

## <a name="troubleshooting-tips"></a>Tips voor probleemoplossing

De volgende tabel bevat veelvoorkomende problemen tijdens het ontwikkelen van pijplijnen, met mogelijke oplossingen.

| Probleem | Mogelijke oplossing |
|--|--|
| Kan geen gegevens doorgeven aan `PipelineData` map | Zorg ervoor dat u een map hebt gemaakt in het script dat overeenkomt met de locatie van de gegevens van de stapuitvoer. In de meeste gevallen definieert een invoerargument de uitvoermap en maakt u vervolgens de map expliciet. Gebruik `os.makedirs(args.output_dir, exist_ok=True)` om de uitvoermap te maken. Zie de [zelfstudie](tutorial-pipeline-batch-scoring-classification.md#write-a-scoring-script) voor een voorbeeld van een scorescript dat dit ontwerppatroon laat zien. |
| Afhankelijkheidsfouten | Als er afhankelijkheidsfouten in uw externe pijplijn worden weergegeven die niet optraden tijdens het lokaal testen, controleert u of de afhankelijkheden en versies van de externe omgeving overeenkomen met die in uw testomgeving. (Zie [Omgeving bouwen, opslaan in de caching en hergebruik](./concept-environments.md#environment-building-caching-and-reuse)|
| Dubbelzinnige fouten met rekendoelen | Probeer rekendoelen te verwijderen en opnieuw te maken. Het opnieuw maken van rekendoelen gaat snel en kan een aantal tijdelijke problemen oplossen. |
| Stappen voor het niet opnieuw gebruiken van pijplijnen | Hergebruik van stappen is standaard ingeschakeld, maar zorg ervoor dat u deze niet hebt uitgeschakeld in een pijplijnstap. Als hergebruik is uitgeschakeld, wordt de parameter in de `allow_reuse` stap ingesteld op `False` . |
| Pijplijn wordt onnodig opnieuw gebruikt | Om ervoor te zorgen dat de stappen alleen opnieuw worden doorlopen wanneer de onderliggende gegevens of scripts worden gewijzigd, moet u de broncodedirecties voor elke stap loskoppelen. Als u dezelfde bronmap voor meerdere stappen gebruikt, kunnen er onnodige nieuwe runs worden ervaren. Gebruik de parameter in een pijplijnstapobject om naar uw geïsoleerde map voor die stap te wijzen en zorg ervoor dat u niet hetzelfde pad gebruikt `source_directory` `source_directory` voor meerdere stappen. |
| Stap vertraagt tijdens trainings epochs of ander loopinggedrag | Probeer schrijf- en logboekregistratie van naar over `as_mount()` te `as_upload()` schakelen. De **modus** voor het monteren maakt gebruik van een extern gevirtualiseerd bestandssysteem en uploadt het hele bestand telkens als het wordt toegevoegd. |
| Het duurt lang voordat het rekendoel is begonnen | Docker-afbeeldingen voor rekendoelen worden geladen vanuit Azure Container Registry (ACR). Standaard maakt Azure Machine Learning een ACR die gebruikmaakt van de basic-servicelaag.  Het wijzigen van de ACR voor uw werkruimte in de Standard- of Premium-laag kan de tijd verminderen die nodig is om afbeeldingen te bouwen en te laden. Zie servicelagen voor [Azure Container Registry meer informatie.](../container-registry/container-registry-skus.md) |

### <a name="authentication-errors"></a>Verificatiefouten

Als u vanaf een externe taak een beheerbewerking op een rekendoel hebt uitgevoerd, ontvangt u een van de volgende fouten: 

```json
{"code":"Unauthorized","statusCode":401,"message":"Unauthorized","details":[{"code":"InvalidOrExpiredToken","message":"The request token was either invalid or expired. Please try again with a valid token."}]}
```

```json
{"error":{"code":"AuthenticationFailed","message":"Authentication failed."}}
```

U ontvangt bijvoorbeeld een foutmelding als u een rekendoel probeert te maken of koppelen vanuit een ML-pijplijn die wordt verzonden voor externe uitvoering.
## <a name="troubleshooting-parallelrunstep"></a>Probleemoplossing `ParallelRunStep` 

Het script voor een `ParallelRunStep` *moet twee* functies bevatten:
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

### <a name="parameters-for-parallelrunconfig"></a>Parameters voor ParallelRunConfig

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

### <a name="parameters-for-creating-the-parallelrunstep"></a>Parameters voor het maken van de ParallelRunStep

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

## <a name="debugging-techniques"></a>Technieken voor het debuggen

Er zijn drie belangrijke technieken voor het debuggen van pijplijnen: 

* Fouten opsporen in afzonderlijke pijplijnstappen op uw lokale computer
* Logboekregistratie en Application Insights om de oorzaak van het probleem te isoleren en te diagnosticeren
* Een extern debugger koppelen aan een pijplijn die wordt uitgevoerd in Azure

### <a name="debug-scripts-locally"></a>Lokaal fouten opsporen in scripts

Een van de meest voorkomende fouten in een pijplijn is dat het domeinscript niet wordt uitgevoerd zoals bedoeld of runtime-fouten bevat in de context van externe berekeningen die moeilijk te debuggen zijn.

Pijplijnen zelf kunnen niet lokaal worden uitgevoerd, maar door de scripts geïsoleerd op uw lokale computer uit te voeren, kunt u sneller fouten opsporen omdat u niet hoeft te wachten op het bouwproces van de berekening en de omgeving. Er is een aantal ontwikkelwerkzaamheden nodig om dit te doen:

* Als uw gegevens zich in een gegevensopslag in de cloud, moet u gegevens downloaden en beschikbaar maken voor uw script. Het gebruik van een klein voorbeeld van uw gegevens is een goede manier om runtime te bekorten en snel feedback te krijgen over het gedrag van scripts
* Als u probeert een tussenliggende pijplijnstap te simuleren, moet u mogelijk handmatig de objecttypen bouwen die het specifieke script verwacht van de vorige stap
* U moet ook uw eigen omgeving definiëren en de afhankelijkheden repliceren die zijn gedefinieerd in uw externe rekenomgeving

Zodra u een script hebt ingesteld om te worden uitgevoerd in uw lokale omgeving, is het veel eenvoudiger om debugtaken uit te voeren, zoals:

* Een aangepaste foutopsporingsconfiguratie koppelen
* Uitvoering onderbreken en object-status inspecteren
* Type- of logische fouten die pas worden blootgesteld tijdens runtime

> [!TIP] 
> Zodra u kunt controleren of uw script wordt uitgevoerd zoals verwacht, is een goede volgende stap het uitvoeren van het script in een pijplijn met één stap voordat u het in een pijplijn met meerdere stappen probeert uit te voeren.

## <a name="configure-write-to-and-review-pipeline-logs"></a>Pijplijnlogboeken configureren, schrijven naar en controleren

Het lokaal testen van scripts is een uitstekende manier om fouten op te sporen in belangrijke codefragmenten en complexe logica voordat u begint met het bouwen van een pijplijn, maar op een bepaald moment moet u waarschijnlijk fouten opsporen in scripts tijdens de daadwerkelijke pijplijnrun zelf, met name bij het diagnosticeren van gedrag dat optreedt tijdens de interactie tussen pijplijnstappen. We raden u aan om instructies in uw stapscripts te gebruiken, zodat u de objecttoestand en verwachte waarden kunt zien tijdens het uitvoeren op afstand, vergelijkbaar met hoe u fouten in `print()` JavaScript-code opsport.

### <a name="logging-options-and-behavior"></a>Opties en gedrag voor logboekregistratie

De onderstaande tabel bevat informatie over verschillende opties voor foutopsporing voor pijplijnen. Het is geen volledige lijst, omdat er naast alleen de Azure Machine Learning, Python en OpenCensus die hier worden weergegeven, andere opties bestaan.

| Bibliotheek                    | Type   | Voorbeeld                                                          | Doel                                  | Resources                                                                                                                                                                                                                                                                                                                    |
|----------------------------|--------|------------------------------------------------------------------|----------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Azure Machine Learning-SDK | Metrisch | `run.log(name, val)`                                             | Azure Machine Learning Portal-gebruikersinterface             | [Experimenten bijhouden](how-to-log-view-metrics.md)<br>[azureml.core.Run-klasse](/python/api/azureml-core/azureml.core.run%28class%29)                                                                                                                                                 |
| Afdrukken/logboekregistratie met Python    | Logboek    | `print(val)`<br>`logging.info(message)`                          | Stuurprogrammalogboeken, Azure Machine Learning designer | [Experimenten bijhouden](how-to-log-view-metrics.md)<br><br>[Python-logboekregistratie](https://docs.python.org/2/library/logging.html)                                                                                                                                                                       |
| OpenCensus Python          | Logboek    | `logger.addHandler(AzureLogHandler())`<br>`logging.log(message)` | Application Insights - traceringen                | [Fouten met pijplijnen opsporen in Application Insights](./how-to-log-pipelines-application-insights.md)<br><br>[OpenCensus Azure Monitor Exporters](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-azure)<br>[Python-handleiding voor logboekregistratie](https://docs.python.org/3/howto/logging-cookbook.html) |

#### <a name="logging-options-example"></a>Voorbeeld van opties voor logboekregistratie

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

Voor pijplijnen die in de ontwerpfunctie zijn gemaakt, kunt u het 70_driver_log vinden op de ontwerppagina of op de detailpagina van de pijplijnrun. 

### <a name="enable-logging-for-real-time-endpoints"></a>Logboekregistratie inschakelen voor realtime-eindpunten

Als u realtime-eindpunten in de ontwerpfunctie wilt oplossen en er fouten in wilt opsporen, moet u Application Insight-logboekregistratie inschakelen met behulp van de SDK. Met logboekregistratie kunt u problemen met de implementatie en het gebruik van modellen oplossen en er fouten in opsporen. Zie Logboekregistratie voor [geïmplementeerde modellen voor meer informatie.](./how-to-enable-app-insights.md) 

### <a name="get-logs-from-the-authoring-page"></a>Logboeken van de ontwerppagina op halen

Wanneer u een pijplijn-run indient en op de ontwerppagina blijft, kunt u de logboekbestanden vinden die voor elke module worden gegenereerd wanneer elke module is uitgevoerd.

1. Selecteer een module die is uitgevoerd in het ontwerpvas.
1. Ga in het rechterdeelvenster van de module naar het **tabblad Uitvoer en logboeken.**
1. Vouw het rechterdeelvenster uit en selecteer **de** 70_driver_log.txtom het bestand in de browser weer te geven. U kunt logboeken ook lokaal downloaden.

    ![Uitgebreid uitvoervenster in de ontwerpfunctie](./media/how-to-debug-pipelines/designer-logs.png)

### <a name="get-logs-from-pipeline-runs"></a>Logboeken van pijplijn-runs halen

U kunt de logboekbestanden voor specifieke runs ook vinden op de detailpagina van  de pijplijnuitleiding. U vindt deze in de sectie **Pijplijnen** of Experimenten van de studio.

1. Selecteer een pijplijn die is gemaakt in de ontwerpfunctie.

    ![De pagina Pijplijn uitvoeren](./media/how-to-debug-pipelines/designer-pipelines.png)

1. Selecteer een module in het voorbeeldvenster.
1. Ga in het rechterdeelvenster van de module naar het **tabblad Uitvoer en logboeken.**
1. Vouw het rechterdeelvenster uit om het **70_driver_log.txt** in de browser weer te geven of selecteer het bestand om de logboeken lokaal te downloaden.

> [!IMPORTANT]
> Als u een pijplijn wilt bijwerken vanaf de pagina details van de pijplijnrun, **moet** u de pijplijn-run klonen naar een nieuw pijplijnontwerp. Een pijplijn-run is een momentopname van de pijplijn. Het is vergelijkbaar met een logboekbestand en kan niet worden gewijzigd. 

## <a name="application-insights"></a>Application Insights
Voor meer informatie over het gebruik van de OpenCensus Python-bibliotheek op deze manier, zie deze handleiding: Fouten opsporen en machine learning pijplijnen [in Application Insights](./how-to-log-pipelines-application-insights.md)

## <a name="interactive-debugging-with-visual-studio-code"></a>Interactieve debugging met Visual Studio Code

In sommige gevallen moet u mogelijk interactief fouten opsporen in de Python-code die wordt gebruikt in uw ML-pijplijn. Door Visual Studio Code (VS Code) en debugpy te gebruiken, kunt u deze koppelen aan de code terwijl deze wordt uitgevoerd in de trainingsomgeving. Ga naar de handleiding interactieve [debugging in VS Code voor meer informatie.](how-to-debug-visual-studio-code.md#debug-and-troubleshoot-machine-learning-pipelines)

## <a name="next-steps"></a>Volgende stappen

* Voor een volledige zelfstudie met `ParallelRunStep` , zie [Zelfstudie: Een Azure Machine Learning maken voor batchscores.](tutorial-pipeline-batch-scoring-classification.md)

* Zie Geautomatiseerde ML gebruiken in een Azure Machine Learning-pijplijn in Python voor een volledig voorbeeld van geautomatiseerde machine learning [in ML-pijplijnen.](how-to-use-automlstep-in-pipelines.md)

* Zie de SDK-referentie voor hulp bij het [pakket azureml-pipelines-core](/python/api/azureml-pipeline-core/) en het pakket [azureml-pipelines-steps.](/python/api/azureml-pipeline-steps/)

* Bekijk de lijst met [designer-uitzonderingen en -foutcodes.](algorithm-module-reference/designer-error-codes.md)