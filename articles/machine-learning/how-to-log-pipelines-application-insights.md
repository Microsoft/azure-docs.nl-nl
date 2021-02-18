---
title: '& verzamelen van de logboek bestanden van de pijp lijn controleren'
titleSuffix: Azure Machine Learning
description: Voeg logboek registratie toe aan uw training en batch Score pijplijnen en Bekijk de geregistreerde resultaten in Application Insights.
services: machine-learning
author: NilsPohlmann
ms.author: nilsp
ms.service: machine-learning
ms.subservice: core
ms.date: 08/11/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 58e604eccaca4630a235f4ae83724df20d6b1e26
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100592523"
---
# <a name="collect-machine-learning-pipeline-log-files-in-application-insights-for-alerts-and-debugging"></a>machine learning pijplijn logboek bestanden verzamelen in Application Insights voor waarschuwingen en fout opsporing


De bibliotheek [Opentellingen](https://opencensus.io/quickstart/python/) python kan worden gebruikt om logboeken naar Application Insights te routeren vanuit uw scripts. Door Logboeken van pijplijn uitvoeringen op één plek samen te voegen, kunt u query's maken en problemen vaststellen. Met behulp van Application Insights kunt u Logboeken in de loop van de tijd bijhouden en de pijp lijn logboeken vergelijken met de verschillende uitvoeringen.

Als u uw logboeken eenmaal hebt uitgevoerd, wordt er een overzicht van uitzonde ringen en fout berichten weer geven. Omdat Application Insights integreert met Azure-waarschuwingen, kunt u ook waarschuwingen maken op basis van Application Insights query's.

## <a name="prerequisites"></a>Vereisten

* Volg de stappen om een [Azure machine learning](./how-to-manage-workspace.md) -werk ruimte te maken en [uw eerste pijp lijn te maken](./how-to-create-machine-learning-pipelines.md)
* [Configureer uw ontwikkel omgeving](./how-to-configure-environment.md) om de Azure machine learning SDK te installeren.
* Installeer het pakket [Opentellings Azure monitor-export](https://pypi.org/project/opencensus-ext-azure/) programma lokaal:
  ```python
  pip install opencensus-ext-azure
  ```
* Een [Application Insights-exemplaar](../azure-monitor/app/opencensus-python.md) maken (dit document bevat ook informatie over het ophalen van de Connection String voor de resource)

## <a name="getting-started"></a>Aan de slag

Deze sectie is een inleiding die specifiek is voor het gebruik van opentellingen vanuit een Azure Machine Learning pijp lijn. Zie voor een gedetailleerde zelf studie [Opentellingen Azure monitor-Exporters](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-azure)

Voeg een PythonScriptStep toe aan uw Azure ML-pijp lijn. Configureer uw [RunConfiguration](/python/api/azureml-core/azureml.core.runconfiguration?preserve-view=true&view=azure-ml-py) met de afhankelijkheid van opentellingen-ext-Azure. Configureer de `APPLICATIONINSIGHTS_CONNECTION_STRING` omgevings variabele.

```python
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core.runconfig import RunConfiguration
from azureml.pipeline.core import Pipeline
from azureml.pipeline.steps import PythonScriptStep

# Connecting to the workspace and compute target not shown

# Add pip dependency on OpenCensus
dependencies = CondaDependencies()
dependencies.add_pip_package("opencensus-ext-azure>=1.0.1")
run_config = RunConfiguration(conda_dependencies=dependencies)

# Add environment variable with Application Insights Connection String
# Replace the value with your own connection string
run_config.environment.environment_variables = {
    "APPLICATIONINSIGHTS_CONNECTION_STRING": 'InstrumentationKey=00000000-0000-0000-0000-000000000000'
}

# Configure step with runconfig
sample_step = PythonScriptStep(
        script_name="sample_step.py",
        compute_target=compute_target,
        runconfig=run_config
)

# Submit new pipeline run
pipeline = Pipeline(workspace=ws, steps=[sample_step])
pipeline.submit(experiment_name="Logging_Experiment")
```

Maak een bestand met de naam `sample_step.py`. Importeer de AzureLogHandler-klasse om logboeken naar Application Insights te routeren. U moet ook de python-logboek bibliotheek importeren.

```python
from opencensus.ext.azure.log_exporter import AzureLogHandler
import logging
```

Voeg vervolgens de AzureLogHandler toe aan de python-logboek registratie.

```python
logger = logging.getLogger(__name__)
logger.setLevel(logging.DEBUG)
logger.addHandler(logging.StreamHandler())

# Assumes the environment variable APPLICATIONINSIGHTS_CONNECTION_STRING is already set
logger.addHandler(AzureLogHandler())
logger.warning("I will be sent to Application Insights")
```

## <a name="logging-with-custom-dimensions"></a>Logboek registratie met aangepaste dimensies
 
Standaard hebben logboeken die zijn doorgestuurd naar Application Insights onvoldoende context om terug te gaan naar de uitvoering of het experiment. Om ervoor te zorgen dat de logboeken actie kunnen ondernemen voor het diagnosticeren van problemen, zijn er extra velden nodig. 

Om deze velden toe te voegen, kunnen aangepaste dimensies worden toegevoegd om context te bieden aan een logboek bericht. Een voor beeld is wanneer iemand logboeken wil weer geven over meerdere stappen in dezelfde pijplijn uitvoering.

Aangepaste dimensies vormen een woorden lijst met sleutel waarden (opgeslagen als teken reeks, teken reeks). De woorden lijst wordt vervolgens naar Application Insights verzonden en als kolom weer gegeven in de query resultaten. De afzonderlijke dimensies kunnen worden gebruikt als [query parameters](#additional-helpful-queries).

### <a name="helpful-context-to-include"></a>Nuttige context voor opname

| Veld                          | Reden/voor beeld                                                                                                                                                                       |
|--------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| parent_run_id                  | Kan Logboeken voor hen met dezelfde parent_run_id zoeken om logboeken gedurende een bepaalde periode voor alle stappen te bekijken, in plaats van elke afzonderlijke stap uit te voeren                                        |
| step_id                        | Kan Logboeken voor hen met dezelfde step_id zoeken om te zien waar een probleem is opgetreden met een smalle omvang tot alleen de afzonderlijke stap                                                        |
| step_name                      | Kan Logboeken opvragen om de stap prestaties in de loop van de tijd te bekijken. Helpt u ook een step_id voor recente uitvoeringen te vinden, zonder dat u in de portal-gebruikers interface                                          |
| experiment_name                | Kan query's uitvoeren op Logboeken om de prestaties van het experiment in de loop der tijd te bekijken. Helpt u ook een parent_run_id of step_id te vinden voor recente uitvoeringen, zonder dat u zich in de portal-gebruikers interface                   |
| run_url                 | Kan rechtstreeks een koppeling naar de uitvoering voor onderzoek opgeven. |

**Andere nuttige velden**

Deze velden kunnen extra code instrumentatie vereisen en worden niet door de uitvoerings context verschaft.

| Veld                   | Reden/voor beeld                                                                                                                                                                                                           |
|-------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| build_url/build_version | Als met CI/CD wordt geïmplementeerd, kan dit veld logboeken koppelen aan de code versie die de logica van de stap en pijp lijn heeft gegeven. Deze koppeling kan meer helpen bij het vaststellen van problemen of het identificeren van modellen met specifieke eigenschappen (logboek/metrieke waarden) |
| run_type                       | Kan onderscheid maken tussen verschillende model typen of training versus scores worden uitgevoerd |

### <a name="creating-a-custom-dimensions-dictionary"></a>Een woorden lijst voor aangepaste dimensies maken

```python
from azureml.core import Run

run = Run.get_context(allow_offline=False)

custom_dimensions = {
    "parent_run_id": run.parent.id,
    "step_id": run.id,
    "step_name": run.name,
    "experiment_name": run.experiment.name,
    "run_url": run.parent.get_portal_url(),
    "run_type": "training"
}

# Assumes AzureLogHandler was already registered above
logger.info("I will be sent to Application Insights with Custom Dimensions", extra= {"custom_dimensions":custom_dimensions})
```

## <a name="opencensus-python-logging-considerations"></a>Berekenings gebeurtenissen opentellingen python

De opentellings AzureLogHandler wordt gebruikt voor het routeren van python-logboeken naar Application Insights. Als gevolg hiervan moeten de nuances van python-logboeken worden overwogen. Wanneer een logboek wordt gemaakt, heeft het een standaard logboek niveau en worden logboeken weer gegeven die groter zijn dan of gelijk zijn aan dat niveau. Een goede referentie voor het gebruik van python-logboek registratie functies is de [logboek registratie Cookbook](https://docs.python.org/3/howto/logging-cookbook.html).

De `APPLICATIONINSIGHTS_CONNECTION_STRING` omgevings variabele is vereist voor de bibliotheek Opentellingen. We raden u aan deze omgevings variabele in te stellen in plaats van deze in te geven als een pijplijn parameter om te voor komen dat verbindings reeksen met ongecodeerde tekst worden door gegeven

## <a name="querying-logs-in-application-insights"></a>Query's uitvoeren op Logboeken in Application Insights

De logboeken die naar Application Insights worden gerouteerd, worden weer gegeven onder traceringen of uitzonde ringen. Zorg ervoor dat u uw tijd venster bijwerkt om de pijplijn uitvoering op te maken.

![Query resultaat Application Insights](./media/how-to-debug-pipelines-application-insights/traces-application-insights-query.png)

In het resultaat van Application Insights worden het logboek bericht en het niveau, het bestandspad en het code regel nummer weer gegeven. Hierin worden ook alle aangepaste dimensies weer gegeven die zijn opgenomen. In deze afbeelding toont de customDimensions-woorden lijst de sleutel/waardeparen van het vorige [code voorbeeld](#creating-a-custom-dimensions-dictionary).

### <a name="additional-helpful-queries"></a>Aanvullende nuttige query's

In enkele van de onderstaande query's wordt gebruikgemaakt van ' customDimensions. level '. Deze ernst niveaus komen overeen met het niveau waarmee het python-logboek oorspronkelijk is verzonden. Zie [Azure monitor-logboek query's](/azure/data-explorer/kusto/query/)voor meer informatie over query's.

| Gebruiksvoorbeeld                                                               | Query’s uitvoeren                                                                                              |
|------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------|
| De resultaten vastleggen voor een specifieke aangepaste dimensie, bijvoorbeeld ' parent_run_id ' | <pre>traces \| <br>where customDimensions.parent_run_id == '931024c2-3720-11ea-b247-c49deda841c1</pre> |
| De resultaten van de logboeken voor alle trainingen worden in de afgelopen 7 dagen uitgevoerd                     | <pre>traces \| <br>where timestamp > ago(7d) <br>and customDimensions.run_type == 'training'</pre>           |
| Resultaten vastleggen met severityLevel-fout in de afgelopen 7 dagen              | <pre>traces \| <br>where timestamp > ago(7d) <br>and customDimensions.Level == 'ERROR'                     |
| Aantal logboek resultaten met severityLevel-fout in de afgelopen 7 dagen     | <pre>traces \| <br>where timestamp > ago(7d) <br>and customDimensions.Level == 'ERROR' \| <br>summarize count()</pre> |

## <a name="next-steps"></a>Volgende stappen

Zodra u Logboeken in uw Application Insights-exemplaar hebt, kunnen ze worden gebruikt om [Azure monitor-waarschuwingen](../azure-monitor/alerts/alerts-overview.md#what-you-can-alert-on) in te stellen op basis van query resultaten.

U kunt ook resultaten van query's toevoegen aan een [Azure-dash board](../azure-monitor/app/tutorial-app-dashboards.md#add-logs-query) voor meer inzichten.