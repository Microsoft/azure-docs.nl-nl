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
ms.openlocfilehash: d55a9ff4dc2a639fca67d19d9323b9397aa0f409
ms.sourcegitcommit: 3af12dc5b0b3833acb5d591d0d5a398c926919c8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/11/2021
ms.locfileid: "98070368"
---
# <a name="troubleshooting-machine-learning-pipelines"></a>Problemen met machine learning pijp lijnen oplossen

In dit artikel leert u hoe u problemen kunt oplossen wanneer er fouten optreden tijdens het uitvoeren van een [machine learning pijp lijn](concept-ml-pipelines.md) in de [Azure Machine Learning SDK](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py) en [Azure machine learning Designer](./concept-designer.md). 

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
| Azure Machine Learning-SDK | Metrisch | `run.log(name, val)`                                             | Gebruikers interface van Azure Machine Learning Portal             | [Experimenten bijhouden](how-to-track-experiments.md)<br>[de klasse azureml. core. run](/python/api/azureml-core/azureml.core.run%28class%29?preserve-view=true&view=azure-ml-py)                                                                                                                                                 |
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

* [Fouten in ParallelRunStep opsporen en problemen oplossen](how-to-debug-parallel-run-step.md)

* Voor een volledige zelf studie `ParallelRunStep` raadpleegt u [zelf studie: een Azure machine learning pijp lijn bouwen voor batch scores](tutorial-pipeline-batch-scoring-classification.md).

* Zie [Automated ml gebruiken in een Azure machine learning-pijp lijn in python](how-to-use-automlstep-in-pipelines.md)voor een volledig voor beeld van automatische machine learning in ml-pijp lijnen.

* Raadpleeg de SDK-Naslag informatie voor hulp met het kern pakket voor [azureml-pipelines-core](/python/api/azureml-pipeline-core/?preserve-view=true&view=azure-ml-py) en het pakket met [azureml-pijp lijnen-stappen](/python/api/azureml-pipeline-steps/?preserve-view=true&view=azure-ml-py) .

* Bekijk de lijst met [uitzonde ringen en fout codes voor Designer](algorithm-module-reference/designer-error-codes.md).