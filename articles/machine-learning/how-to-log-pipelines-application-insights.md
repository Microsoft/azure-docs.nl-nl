---
title: Pijplijnlogboekbestanden & bewaken
titleSuffix: Azure Machine Learning
description: Voeg logboekregistratie toe aan uw trainings- en batchscorepijplijnen en bekijk de vastgelegde resultaten in Application Insights.
services: machine-learning
author: NilsPohlmann
ms.author: nilsp
ms.service: machine-learning
ms.subservice: core
ms.date: 08/11/2020
ms.topic: how-to
ms.custom: devx-track-python
ms.openlocfilehash: cb455eb5eaf91377ca8dedc827ff89113f51b71a
ms.sourcegitcommit: 5ce88326f2b02fda54dad05df94cf0b440da284b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107884890"
---
# <a name="collect-machine-learning-pipeline-log-files-in-application-insights-for-alerts-and-debugging"></a>Logboekbestanden machine learning pijplijn verzamelen in Application Insights voor waarschuwingen en debuggen


De [OpenCensus](https://opencensus.io/quickstart/python/) Python-bibliotheek kan worden gebruikt om logboeken om te Application Insights vanuit uw scripts. Door logboeken van pijplijn-runs op één plek te aggregeren, kunt u query's maken en problemen diagnosticeren. Met Application Insights kunt u logboeken in de loop van de tijd bijhouden en pijplijnlogboeken vergelijken voor verschillende runs.

Als uw logboeken eenmaal zijn op hun plaats, krijgt u een geschiedenis van uitzonderingen en foutberichten. Omdat Application Insights integreert met Azure-waarschuwingen, kunt u ook waarschuwingen maken op basis van Application Insights query's.

## <a name="prerequisites"></a>Vereisten

* Volg de stappen om een werkruimte [Azure Machine Learning](./how-to-manage-workspace.md) maken en uw [eerste pijplijn te maken](./how-to-create-machine-learning-pipelines.md)
* [Configureer uw ontwikkelomgeving](./how-to-configure-environment.md) om de Azure Machine Learning SDK te installeren.
* Installeer het [Pakket voor export van OpenCensus Azure Monitor-export:](https://pypi.org/project/opencensus-ext-azure/)
  ```python
  pip install opencensus-ext-azure
  ```
* Een Application Insights [maken](../azure-monitor/app/opencensus-python.md) (dit document bevat ook informatie over het verkrijgen van de connection string voor de resource)

## <a name="getting-started"></a>Aan de slag

Deze sectie is een inleiding die specifiek is voor het gebruik van OpenCensus vanuit een Azure Machine Learning pijplijn. Zie [OpenCensus](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-azure) Azure Monitor Export voor een gedetailleerde zelfstudie

Voeg een PythonScriptStep toe aan uw Azure ML-pijplijn. Configureer [uw RunConfiguration](/python/api/azureml-core/azureml.core.runconfiguration) met de afhankelijkheid opencensus-ext-azure. Configureer de `APPLICATIONINSIGHTS_CONNECTION_STRING` omgevingsvariabele.

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

Maak een bestand met de naam `sample_step.py`. Importeer de klasse AzureLogHandler om logboeken naar uw Application Insights. U moet ook de Python-bibliotheek voor logboekregistratie importeren.

```python
from opencensus.ext.azure.log_exporter import AzureLogHandler
import logging
```

Voeg vervolgens de AzureLogHandler toe aan de Python-logboeken.

```python
logger = logging.getLogger(__name__)
logger.setLevel(logging.DEBUG)
logger.addHandler(logging.StreamHandler())

# Assumes the environment variable APPLICATIONINSIGHTS_CONNECTION_STRING is already set
logger.addHandler(AzureLogHandler())
logger.warning("I will be sent to Application Insights")
```

## <a name="logging-with-custom-dimensions"></a>Logboekregistratie met aangepaste dimensies
 
Standaard hebben logboeken die worden doorgestuurd Application Insights onvoldoende context om terug te gaan naar de run of het experiment. Er zijn extra velden nodig om de logboeken bruikbaar te maken voor het diagnosticeren van problemen. 

Als u deze velden wilt toevoegen, kunt u Aangepaste dimensies toevoegen om context te bieden aan een logboekbericht. Een voorbeeld is wanneer iemand logboeken in meerdere stappen in dezelfde pijplijn-run wil weergeven.

Aangepaste dimensies zijn een woordenlijst met sleutel-waardeparen (opgeslagen als tekenreeks, tekenreeks). De woordenlijst wordt vervolgens verzonden naar Application Insights en weergegeven als een kolom in de queryresultaten. De afzonderlijke dimensies kunnen worden gebruikt als [queryparameters.](#additional-helpful-queries)

### <a name="helpful-context-to-include"></a>Nuttige context om op te nemen

| Veld                          | Redenering/voorbeeld                                                                                                                                                                       |
|--------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| parent_run_id                  | Kan logboeken opvragen voor logboeken met dezelfde parent_run_id logboeken gedurende een bepaalde periode voor alle stappen te bekijken, in plaats van elke afzonderlijke stap te moeten bekijken                                        |
| step_id                        | Kan logboeken opvragen voor logboeken met dezelfde step_id om te zien waar een probleem is opgetreden met een beperkt bereik tot alleen de afzonderlijke stap                                                        |
| step_name                      | Kan query's uitvoeren op logboeken om de prestaties van stappen in de tijd te bekijken. Helpt ook bij het vinden van een step_id recente runs zonder in de gebruikersinterface van de portal te duiken                                          |
| experiment_name                | Kan query's uitvoeren op logboeken om de prestaties van het experiment in de tijd te bekijken. Helpt ook bij het vinden van parent_run_id of step_id recente runs zonder in de gebruikersinterface van de portal te duiken                   |
| run_url                 | Kan een koppeling rechtstreeks terug naar de run geven voor onderzoek. |

**Andere nuttige velden**

Deze velden vereisen mogelijk extra code-instrumentatie en worden niet geleverd door de context van de run.

| Veld                   | Redenering/voorbeeld                                                                                                                                                                                                           |
|-------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| build_url/build_version | Als u CI/CD gebruikt om te implementeren, kan dit veld logboeken correleren met de codeversie die de stap- en pijplijnlogica heeft opgegeven. Deze koppeling kan u verder helpen bij het diagnosticeren van problemen of het identificeren van modellen met specifieke eigenschappen (logboek-/metrische waarden) |
| run_type                       | Kan onderscheid maken tussen verschillende modeltypen of trainings- en scoreuit runs |

### <a name="creating-a-custom-dimensions-dictionary"></a>Een woordenlijst voor aangepaste dimensies maken

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

## <a name="opencensus-python-logging-considerations"></a>Overwegingen bij logboekregistratie van OpenCensus Python

De OpenCensus AzureLogHandler wordt gebruikt om Python-logboeken naar de Application Insights. Als gevolg hiervan moeten logboekregistratienuances van Python in aanmerking worden genomen. Wanneer een logboek wordt gemaakt, heeft deze een standaardlogboekniveau en worden logboeken weergegeven die groter zijn dan of gelijk zijn aan dat niveau. Een goede referentie voor het gebruik van Python-logboekregistratiefuncties is [het Logboekregistratiegids.](https://docs.python.org/3/howto/logging-cookbook.html)

De `APPLICATIONINSIGHTS_CONNECTION_STRING` omgevingsvariabele is nodig voor de OpenCensus-bibliotheek. We raden u aan deze omgevingsvariabele in te stellen in plaats van deze door te geven als pijplijnparameter om te voorkomen dat verbindingsreeksen met platte tekst worden doorgegeven.

## <a name="querying-logs-in-application-insights"></a>Query's uitvoeren op logboeken in Application Insights

De logboeken die naar Application Insights, worden onder 'traces' of 'exceptions' (uitzonderingen) te zien. Zorg ervoor dat u het tijdvenster aanpast om de pijplijnuit voeren op te nemen.

![Application Insights queryresultaat](./media/how-to-debug-pipelines-application-insights/traces-application-insights-query.png)

Het resultaat in Application Insights het logboekbericht en -niveau, het bestandspad en het coderegelnummer. Er worden ook aangepaste dimensies in opgenomen. In deze afbeelding toont de woordenlijst customDimensions de sleutel-waardeparen uit het vorige [codevoorbeeld](#creating-a-custom-dimensions-dictionary).

### <a name="additional-helpful-queries"></a>Aanvullende nuttige query's

Sommige van de onderstaande query's gebruiken 'customDimensions.Level'. Deze ernstniveaus komen overeen met het niveau waar het Python-logboek oorspronkelijk mee is verzonden. Zie logboekquery's voor [Azure Monitor meer informatie over query's.](/azure/data-explorer/kusto/query/)

| Gebruiksvoorbeeld                                                               | Query                                                                                              |
|------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------|
| Logboekresultaten voor specifieke aangepaste dimensies, bijvoorbeeld 'parent_run_id' | <pre>traces \| <br>where customDimensions.parent_run_id == '931024c2-3720-11ea-b247-c49deda841c1</pre> |
| Logboekresultaten voor alle trainingsuit runs in de afgelopen 7 dagen                     | <pre>traces \| <br>where timestamp > ago(7d) <br>and customDimensions.run_type == 'training'</pre>           |
| Logboekresultaten met ernstNiveaufout van de afgelopen 7 dagen              | <pre>traces \| <br>where timestamp > ago(7d) <br>and customDimensions.Level == 'ERROR'                     |
| Aantal logboekresultaten met ernstNiveaufout in de afgelopen 7 dagen     | <pre>traces \| <br>where timestamp > ago(7d) <br>and customDimensions.Level == 'ERROR' \| <br>summarize count()</pre> |

## <a name="next-steps"></a>Volgende stappen

Zodra u logboeken in uw Application Insights hebt, kunnen ze worden gebruikt om Azure Monitor [op](../azure-monitor/alerts/alerts-overview.md#what-you-can-alert-on) basis van queryresultaten in te stellen.

U kunt ook resultaten van query's toevoegen aan een [Azure-dashboard](../azure-monitor/app/tutorial-app-dashboards.md#add-logs-query) voor meer inzichten.