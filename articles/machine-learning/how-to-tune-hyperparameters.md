---
title: Hyper parameters voor uw model afstemmen
titleSuffix: Azure Machine Learning
description: Hyper parameters efficiënt afstemmen op diep gaande lessen en machine learning modellen met behulp van Azure Machine Learning.
ms.author: swatig
author: swatig007
ms.reviewer: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.date: 03/30/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, contperf-fy21q1
ms.openlocfilehash: a1a0b89dee5a6d57e6f317c5f6c8b61ffeda3c33
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/10/2020
ms.locfileid: "97029070"
---
# <a name="tune-hyperparameters-for-your-model-with-azure-machine-learning"></a>Hyper parameters voor uw model afstemmen met Azure Machine Learning


Automatiseer efficiënt afstemmen van afstemming met behulp van Azure Machine Learning [HyperDrive-pakket](/python/api/azureml-train-core/azureml.train.hyperdrive?preserve-view=true&view=azure-ml-py). Meer informatie over het uitvoeren van de stappen die nodig zijn om Hyper parameters af te stemmen met de [Azure machine learning SDK](/python/api/overview/azure/ml/?preserve-view=true&view=azure-ml-py):

1. De zoek ruimte voor de para meter definiëren
1. Geef een primaire metriek op om te optimaliseren  
1. Beleid voor vroegtijdige beëindiging opgeven voor uitvoeringen die weinig worden uitgevoerd
1. Resources toewijzen
1. Een experiment met de gedefinieerde configuratie starten
1. De trainings uitvoeringen visualiseren
1. De beste configuratie voor uw model selecteren

## <a name="what-are-hyperparameters"></a>Wat zijn Hyper parameters?

**Hyper parameters** zijn aanpas bare para meters waarmee u het model trainings proces kunt beheren. Met Neural-netwerken bepaalt u bijvoorbeeld het aantal verborgen lagen en het aantal knoop punten in elke laag. Model prestaties zijn sterk afhankelijk van Hyper parameters.

 **Afstemming tuning** is het proces van het vinden van de configuratie van Hyper parameters die de beste prestaties oplevert. Het proces is doorgaans kostbaar en hand matig.

Met Azure Machine Learning kunt u afstemming-tuning automatiseren en experimenten parallel gebruiken om Hyper parameters efficiënt te optimaliseren.


## <a name="define-the-search-space"></a>De zoek ruimte definiëren

Hyper parameters afstemmen door het bereik van waarden te verkennen dat voor elke afstemming is gedefinieerd.

Hyper parameters kan afzonderlijk of doorlopend zijn en heeft een distributie van waarden die worden beschreven door een [parameter expressie](/python/api/azureml-train-core/azureml.train.hyperdrive.parameter_expressions?preserve-view=true&view=azure-ml-py).

### <a name="discrete-hyperparameters"></a>Discrete Hyper parameters 

Discrete Hyper parameters zijn opgegeven als een `choice` onder discrete waarden. `choice` kan zijn:

* een of meer door komma's gescheiden waarden
* een `range` object
* wille keurig wille keurig `list` object


```Python
    {
        "batch_size": choice(16, 32, 64, 128)
        "number_of_hidden_layers": choice(range(1,5))
    }
```

In dit geval wordt `batch_size` een van de waarden [16, 32, 64, 128] en wordt `number_of_hidden_layers` een van de waarden [1, 2, 3, 4] gebruikt.

De volgende geavanceerde discrete Hyper parameters kunnen ook worden opgegeven met behulp van een distributie:

* `quniform(low, high, q)` -Retourneert een waarde zoals Round (Uniform (laag, hoog)/q) * q
* `qloguniform(low, high, q)` -Retourneert een waarde zoals Round (exp (Uniform (laag, hoog)/q) * q
* `qnormal(mu, sigma, q)` -Retourneert een waarde zoals Round (normaal (mu, Sigma)/q) * q
* `qlognormal(mu, sigma, q)` -Retourneert een waarde zoals Round (exp (normaal (mu, Sigma)/q) * q

### <a name="continuous-hyperparameters"></a>Doorlopend Hyper parameters 

De doorlopende Hyper parameters worden opgegeven als een distributie over een doorlopend bereik van waarden:

* `uniform(low, high)` -Retourneert een waarde die gelijkmatig wordt verdeeld tussen laag en hoog
* `loguniform(low, high)` -Retourneert een waarde die is getekend op basis van exp (Uniform (Low, High)), zodat de logaritme van de geretourneerde waarde gelijkmatig wordt gedistribueerd
* `normal(mu, sigma)` -Retourneert een echte waarde die normaal gesp roken wordt gedistribueerd met gemiddelde MU en standaard deviatie Sigma
* `lognormal(mu, sigma)` -Retourneert een waarde die is getekend op basis van exp (normaal (mu, Sigma)), zodat de logaritme van de geretourneerde waarde normaal gesp roken wordt gedistribueerd

Een voor beeld van een definitie van een parameter ruimte:

```Python
    {    
        "learning_rate": normal(10, 3),
        "keep_probability": uniform(0.05, 0.1)
    }
```

Deze code definieert een zoek ruimte met twee para meters: `learning_rate` en `keep_probability` . `learning_rate` heeft een normale verdeling met gemiddelde waarde 10 en een standaard afwijking van 3. `keep_probability` heeft een uniforme distributie met een minimum waarde van 0,05 en een maximum waarde van 0,1.

### <a name="sampling-the-hyperparameter-space"></a>De afstemming ruimte bemonsteren

Geef de parameter bemonsterings methode op die u wilt gebruiken voor de afstemming ruimte. Azure Machine Learning ondersteunt de volgende methoden:

* Wille keurige steek proef
* Raster sampling
* Bayesiaanse steekproeven

#### <a name="random-sampling"></a>Wille keurige steek proef

[Wille keurige steek proeven](/python/api/azureml-train-core/azureml.train.hyperdrive.randomparametersampling?preserve-view=true&view=azure-ml-py) ondersteunen discrete en doorlopende Hyper parameters. Het biedt ondersteuning voor vroege beëindiging van uitvoeringen met lage prestaties. Sommige gebruikers voeren een eerste zoek opdracht uit met wille keurige steek proeven en verfijnen de zoek ruimte om de resultaten te verbeteren.

In wille keurige steek proeven worden afstemming waarden wille keurig geselecteerd uit de gedefinieerde zoek ruimte. 

```Python
from azureml.train.hyperdrive import RandomParameterSampling
from azureml.train.hyperdrive import normal, uniform, choice
param_sampling = RandomParameterSampling( {
        "learning_rate": normal(10, 3),
        "keep_probability": uniform(0.05, 0.1),
        "batch_size": choice(16, 32, 64, 128)
    }
)
```

#### <a name="grid-sampling"></a>Raster sampling

[Raster sampling](/python/api/azureml-train-core/azureml.train.hyperdrive.gridparametersampling?preserve-view=true&view=azure-ml-py) ondersteunt discrete Hyper parameters. Gebruik raster sampling als u een budget kunt gebruiken om de zoek ruimte uitgebreid te doorzoeken. Biedt ondersteuning voor vroegtijdige beëindiging van uitvoeringen met lage prestaties.

Hiermee wordt een eenvoudige raster zoekactie uitgevoerd voor alle mogelijke waarden. Raster sampling kan alleen worden gebruikt met `choice` Hyper parameters. De volgende ruimte heeft bijvoorbeeld zes voor beelden:

```Python
from azureml.train.hyperdrive import GridParameterSampling
from azureml.train.hyperdrive import choice
param_sampling = GridParameterSampling( {
        "num_hidden_layers": choice(1, 2, 3),
        "batch_size": choice(16, 32)
    }
)
```

#### <a name="bayesian-sampling"></a>Bayesiaanse steekproeven

[Bayesiaanse-steek proeven](/python/api/azureml-train-core/azureml.train.hyperdrive.bayesianparametersampling?preserve-view=true&view=azure-ml-py) zijn gebaseerd op het Bayesiaanse-optimalisatie algoritme. Er worden voor beelden gekozen op basis van de manier waarop eerdere steek proeven worden uitgevoerd, zodat nieuwe voor beelden de primaire metriek verbeteren.

Bayesiaanse-steek proeven worden aanbevolen als u voldoende budget hebt om de afstemming ruimte te verkennen. Voor de beste resultaten raden wij u aan een maximum aantal uitvoeringen te hebben dat groter is dan of gelijk is aan 20 maal het aantal Hyper parameters dat wordt afgestemd. 

Het aantal gelijktijdige uitvoeringen heeft invloed op de effectiviteit van het afstemmings proces. Een kleiner aantal gelijktijdige uitvoeringen kan leiden tot een betere steek proef van de convergentie, omdat de kleinere mate van parallellisme het aantal uitvoeringen verhoogt dat van eerder voltooide uitvoeringen van het voor deel is.

Bayesiaanse-steek proeven bieden alleen ondersteuning voor `choice` , `uniform` en `quniform` distributies in de zoek ruimte.

```Python
from azureml.train.hyperdrive import BayesianParameterSampling
from azureml.train.hyperdrive import uniform, choice
param_sampling = BayesianParameterSampling( {
        "learning_rate": uniform(0.05, 0.1),
        "batch_size": choice(16, 32, 64, 128)
    }
)
```



## <a name="specify-primary-metric"></a><a name="specify-primary-metric-to-optimize"></a> Primaire metriek opgeven

Geef de [primaire metriek](/python/api/azureml-train-core/azureml.train.hyperdrive.primarymetricgoal?preserve-view=true&view=azure-ml-py) op die u wilt afstemming optimaliseren. Elke trainings uitvoering wordt geëvalueerd voor de primaire metriek. Voor het beleid voor vroegtijdige beëindiging wordt gebruikgemaakt van de primaire metriek om uitvoeringen met een lage prestaties te identificeren.

Geef de volgende kenmerken op voor uw primaire metriek:

* `primary_metric_name`: De naam van de primaire metriek moet exact overeenkomen met de naam van de metrische gegevens die door het trainings script worden vastgelegd
* `primary_metric_goal`: Het kan ofwel `PrimaryMetricGoal.MAXIMIZE` of `PrimaryMetricGoal.MINIMIZE` en bepalen of de primaire metriek wordt gemaximaliseerd of geminimaliseerd bij het evalueren van de uitvoeringen. 

```Python
primary_metric_name="accuracy",
primary_metric_goal=PrimaryMetricGoal.MAXIMIZE
```

Dit voor beeld maximaliseert ' nauw keurig '.

### <a name="log-metrics-for-hyperparameter-tuning"></a><a name="log-metrics-for-hyperparameter-tuning"></a>Metrische logboek gegevens voor afstemming-afstemming

Het trainings script voor uw model **moet** de primaire metriek registreren tijdens de model training, zodat HyperDrive toegang kan krijgen tot de gegevens voor afstemming-afstemming.

Registreer de primaire metrische gegevens in uw trainings script met het volgende voorbeeld fragment:

```Python
from azureml.core.run import Run
run_logger = Run.get_context()
run_logger.log("accuracy", float(val_accuracy))
```

Het trainings script berekent de `val_accuracy` en registreert dit als de primaire metriek "nauw keurig". Telkens wanneer de metriek wordt geregistreerd, wordt deze ontvangen door de afstemming tuning service. Het is aan te raden om de frequentie van rapportage te bepalen.

Zie [logboek registratie inschakelen in azure ml-trainings uitvoeringen](how-to-track-experiments.md)voor meer informatie over het uitvoeren van registratie waarden in model training.

## <a name="specify-early-termination-policy"></a><a name="early-termination"></a> Beleid voor vroegtijdige beëindiging opgeven

Automatisch aflopende uitvoeringen uitvoeren met een beleid voor vroegtijdige beëindiging. Vroege beëindiging verbetert de verwerkings efficiëntie.

U kunt de volgende para meters configureren die bepalen wanneer een beleid wordt toegepast:

* `evaluation_interval`: de frequentie waarmee het beleid wordt toegepast. Telkens wanneer het trainings script de primaire metriek registreert als één interval. `evaluation_interval`Als 1 wordt het beleid toegepast wanneer het trainings script de primaire metriek rapporteert. `evaluation_interval`Met 2 wordt het beleid elke keer toegepast. Als u niets opgeeft, `evaluation_interval` wordt standaard ingesteld op 1.
* `delay_evaluation`: de eerste beleids evaluatie voor een opgegeven aantal intervallen wordt uitgesteld. Dit is een optionele para meter waarmee het voor tijdig beëindigen van de trainings uitvoeringen wordt voor komen door ervoor te zorgen dat alle configuraties voor een minimum aantal intervallen worden uitgevoerd. Indien opgegeven, wordt het beleid toegepast op elk veelvoud van evaluation_interval dat groter is dan of gelijk is aan delay_evaluation.

Azure Machine Learning ondersteunt de volgende beleids regels voor vroegtijdige beëindiging:
* [Bandit-beleid](#bandit-policy)
* [Beleid voor mediaan stoppen](#median-stopping-policy)
* [Selectie beleid voor afkap ping](#truncation-selection-policy)
* [Geen beleid voor beëindiging](#no-termination-policy-default)


### <a name="bandit-policy"></a>Bandit-beleid

Het [Bandit-beleid](/python/api/azureml-train-core/azureml.train.hyperdrive.banditpolicy?preserve-view=true&view=azure-ml-py#&preserve-view=truedefinition) is gebaseerd op de toegestane factor en het toegestane aantal en de evaluatie-interval. Bandit wordt beëindigd wanneer de primaire metriek zich niet binnen het opgegeven aantal toegestane vertragings factoren bevindt vergeleken met de best presterende uitvoering.

> [!NOTE]
> Bayesiaanse-steek proeven bieden geen ondersteuning voor vroege beëindiging. Wanneer u Bayesiaanse-steek proeven gebruikt, stelt u in `early_termination_policy = None` .

Geef de volgende configuratie parameters op:

* `slack_factor` of `slack_amount` : de toegestane vertraging met betrekking tot de best presterende trainings uitvoering. `slack_factor` Hiermee geeft u de toegestane vertraging als een ratio op. `slack_amount` Hiermee geeft u de toegestane vertraging als een absoluut bedrag op in plaats van een ratio.

    Denk bijvoorbeeld aan een Bandit-beleid dat wordt toegepast tijdens interval 10. Stel dat de best presterende run bij interval 10 een primaire metriek heeft gerapporteerd, 0,8 is met een doel om de primaire meet waarde te maximaliseren. Als in het beleid een van de 0,2 wordt opgegeven, wordt een `slack_factor` training uitgevoerd waarvan de beste metrische waarde bij interval 10 kleiner is dan 0,66 (0,8/(1 + `slack_factor` )).
* `evaluation_interval`: (optioneel) de frequentie voor het Toep assen van het beleid
* `delay_evaluation`: (optioneel) de eerste beleids evaluatie voor een opgegeven aantal intervallen wordt vertraagd


```Python
from azureml.train.hyperdrive import BanditPolicy
early_termination_policy = BanditPolicy(slack_factor = 0.1, evaluation_interval=1, delay_evaluation=5)
```

In dit voor beeld wordt het beleid voor vroegtijdige beëindiging toegepast op elk interval wanneer metrische gegevens worden gerapporteerd, beginnend bij de evaluatie-interval 5. Een uitvoering waarvan de beste metriek kleiner is dan (1/(1 + 0,1) of 91% van de best presterende uitvoering wordt beëindigd.

### <a name="median-stopping-policy"></a>Beleid voor mediaan stoppen

[Mediaan stoppen](/python/api/azureml-train-core/azureml.train.hyperdrive.medianstoppingpolicy?preserve-view=true&view=azure-ml-py) is een beleid voor vroegtijdige beëindiging op basis van het actieve gemiddelde van primaire metrische gegevens die door de uitvoeringen worden gerapporteerd. Dit beleid berekent de lopende gemiddelden voor alle trainings uitvoeringen en beëindigt uitvoeringen met primaire meet waarden, erger dan de mediaan van gemiddelden.

Voor dit beleid worden de volgende configuratie parameters gebruikt:
* `evaluation_interval`: de frequentie voor het Toep assen van het beleid (optionele para meter).
* `delay_evaluation`: de eerste beleids evaluatie wordt uitgesteld voor een opgegeven aantal intervallen (optionele para meter).


```Python
from azureml.train.hyperdrive import MedianStoppingPolicy
early_termination_policy = MedianStoppingPolicy(evaluation_interval=1, delay_evaluation=5)
```

In dit voor beeld wordt het beleid voor vroegtijdige beëindiging toegepast op elk interval, te beginnen bij de evaluatie-interval 5. Een run wordt beëindigd bij interval 5 als de beste primaire gegevens slechter zijn dan de mediaan van de lopende gemiddelden over intervallen 1:5 voor alle trainings uitvoeringen.

### <a name="truncation-selection-policy"></a>Selectie beleid voor afkap ping

Met de selectie van de [Afkap ping](/python/api/azureml-train-core/azureml.train.hyperdrive.truncationselectionpolicy?preserve-view=true&view=azure-ml-py) wordt een percentage van de laagste uitgevoerde uitvoeringen bij elk evaluatie-interval geannuleerd. Uitvoeringen worden vergeleken met de primaire metriek. 

Voor dit beleid worden de volgende configuratie parameters gebruikt:

* `truncation_percentage`: het percentage van de laagste uitvoeringen dat bij elk evaluatie-interval wordt beëindigd. Een integer-waarde tussen 1 en 99.
* `evaluation_interval`: (optioneel) de frequentie voor het Toep assen van het beleid
* `delay_evaluation`: (optioneel) de eerste beleids evaluatie voor een opgegeven aantal intervallen wordt vertraagd


```Python
from azureml.train.hyperdrive import TruncationSelectionPolicy
early_termination_policy = TruncationSelectionPolicy(evaluation_interval=1, truncation_percentage=20, delay_evaluation=5)
```

In dit voor beeld wordt het beleid voor vroegtijdige beëindiging toegepast op elk interval, te beginnen bij de evaluatie-interval 5. Een uitvoering eindigt bij interval 5 als de prestaties bij interval 5 in de laagste 20% van de prestaties van alle uitvoeringen in interval 5 liggen.

### <a name="no-termination-policy-default"></a>Geen beëindigings beleid (standaard)

Als er geen beleid is opgegeven, kan de afstemming-afstemmings service alle trainings uitvoeringen uitvoeren tot voltooiing.

```Python
policy=None
```

### <a name="picking-an-early-termination-policy"></a>Een beleid voor vroegtijdige beëindiging kiezen

* Voor een conservatief beleid dat besparingen oplevert zonder toezeggings taken te beëindigen, moet u een mediaan beleid voor stoppen met `evaluation_interval` 1 en `delay_evaluation` 5 overwegen. Dit zijn voorzichtige instellingen, die ongeveer 25%-35% besparingen kunnen bieden zonder verlies van primaire metriek (op basis van de evaluatie gegevens).
* Voor meer agressieve besparingen gebruikt u Bandit-beleid met een kleinere toegestane vertraging of selectie beleid voor Afkap ping met een grotere Afbrekings percentage.

## <a name="allocate-resources"></a>Resources toewijzen

Beheer uw resource budget door het maximum aantal trainings uitvoeringen op te geven.

* `max_total_runs`: Het maximum aantal trainings uitvoeringen. Moet een geheel getal tussen 1 en 1000 zijn.
* `max_duration_minutes`: (optioneel) maximum duur, in minuten, van het afstemming tuning-experiment. Wordt uitgevoerd nadat deze duur is geannuleerd.

>[!NOTE] 
>Als beide `max_total_runs` en `max_duration_minutes` zijn opgegeven, wordt het afstemming-afstemmings experiment beëindigd wanneer de eerste van deze twee drempel waarden wordt bereikt.

Daarnaast geeft u het maximum aantal trainings uitvoeringen op dat gelijktijdig moet worden uitgevoerd tijdens uw afstemming-afstemmings zoek opdracht.

* `max_concurrent_runs`: (optioneel) maximum aantal uitvoeringen dat gelijktijdig kan worden uitgevoerd. Als niet wordt opgegeven, worden alle uitgevoerd parallel gestart. Indien opgegeven, moet een geheel getal tussen 1 en 100 zijn.

>[!NOTE] 
>Het aantal gelijktijdige uitvoeringen wordt gegatedd op de resources die beschikbaar zijn in het opgegeven Compute-doel. Zorg ervoor dat het Compute-doel de beschik bare resources voor de gewenste gelijktijdigheid heeft.

```Python
max_total_runs=20,
max_concurrent_runs=4
```

Met deze code wordt het afstemming-afstemmings experiment geconfigureerd voor gebruik van Maxi maal 20 volledige uitvoeringen, waarbij vier configuraties tegelijk worden uitgevoerd.

## <a name="configure-experiment"></a>Experiment configureren

Als u [uw afstemming tuning](/python/api/azureml-train-core/azureml.train.hyperdrive.hyperdriverunconfig?preserve-view=true&view=azure-ml-py) -experiment wilt configureren, geeft u het volgende op:
* De gedefinieerde afstemming-Zoek ruimte
* Uw beleid voor vroegtijdige beëindiging
* De primaire metriek
* Toewijzings instellingen voor resources
* ScriptRunConfig `src`

De ScriptRunConfig is het trainings script dat wordt uitgevoerd met de Hyper parameters van de steek proef. Het definieert de resources per taak (één of meerdere knoop punten) en het reken doel dat moet worden gebruikt.

> [!NOTE]
>Het Compute-doel dat is opgegeven in, `src` moet voldoende resources hebben om te voldoen aan het gelijktijdigheids niveau. Zie [Configure training runs](how-to-set-up-training-targets.md)(Engelstalig) voor meer informatie over ScriptRunConfig.

Uw afstemming-afstemmings experiment configureren:

```Python
from azureml.train.hyperdrive import HyperDriveConfig
hd_config = HyperDriveConfig(run_config=src,
                             hyperparameter_sampling=param_sampling,
                             policy=early_termination_policy,
                             primary_metric_name="accuracy",
                             primary_metric_goal=PrimaryMetricGoal.MAXIMIZE,
                             max_total_runs=100,
                             max_concurrent_runs=4)
```

## <a name="submit-experiment"></a>Experiment verzenden

Nadat u de afstemming-afstemmings configuratie hebt gedefinieerd, moet u [het experiment verzenden](/python/api/azureml-core/azureml.core.experiment%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truesubmit-config--tags-none----kwargs-):

```Python
from azureml.core.experiment import Experiment
experiment = Experiment(workspace, experiment_name)
hyperdrive_run = experiment.submit(hd_config)
```

## <a name="warm-start-your-hyperparameter-tuning-experiment-optional"></a>Start uw afstemming tuning-experiment warme (optioneel)

Het zoeken naar de beste afstemming-waarden voor uw model kan een iteratief proces zijn. U kunt kennis van de vijf vorige uitvoeringen hergebruiken om afstemming-afstemming te versnellen.


Warme start wordt op verschillende manieren afgehandeld, afhankelijk van de bemonsterings methode:
- **Bayesiaanse sampling**: proef versies van de vorige uitvoering worden gebruikt als eerdere kennis om nieuwe voor beelden te kiezen en om de primaire meet waarde te verbeteren.
- **Steekproefsgewijze steek proef of** **raster sampling**: vroege beëindiging maakt gebruik van kennis van eerdere uitvoeringen om te bepalen of er slecht uitgevoerde uitvoeringen zijn. 

Geef de lijst van bovenliggende uitvoeringen op waaruit u wilt warmen.

```Python
from azureml.train.hyperdrive import HyperDriveRun

warmstart_parent_1 = HyperDriveRun(experiment, "warmstart_parent_run_ID_1")
warmstart_parent_2 = HyperDriveRun(experiment, "warmstart_parent_run_ID_2")
warmstart_parents_to_resume_from = [warmstart_parent_1, warmstart_parent_2]
```

Als een afstemming-afstemmings experiment wordt geannuleerd, kunt u de trainings uitvoeringen van het laatste controle punt hervatten. Uw trainings script moet echter de controlepunt logica verwerken.

De uitvoering van de training moet dezelfde afstemming-configuratie gebruiken en de uitvoer mappen hebben gekoppeld. Het trainings script moet het `resume-from` argument accepteren dat het controle punt of de model bestanden bevat waaruit de trainings uitvoering moet worden hervat. U kunt afzonderlijke trainings uitvoeringen hervatten met het volgende code fragment:

```Python
from azureml.core.run import Run

resume_child_run_1 = Run(experiment, "resume_child_run_ID_1")
resume_child_run_2 = Run(experiment, "resume_child_run_ID_2")
child_runs_to_resume = [resume_child_run_1, resume_child_run_2]
```

U kunt uw afstemming-afstemmings experiment configureren om te beginnen met een vorig experiment of door de uitvoering van afzonderlijke trainingen te hervatten met behulp van de optionele para meters `resume_from` en `resume_child_runs` in de configuratie:

```Python
from azureml.train.hyperdrive import HyperDriveConfig

hd_config = HyperDriveConfig(run_config=src,
                             hyperparameter_sampling=param_sampling,
                             policy=early_termination_policy,
                             resume_from=warmstart_parents_to_resume_from,
                             resume_child_runs=child_runs_to_resume,
                             primary_metric_name="accuracy",
                             primary_metric_goal=PrimaryMetricGoal.MAXIMIZE,
                             max_total_runs=100,
                             max_concurrent_runs=4)
```

## <a name="visualize-experiment"></a>Experimenteren met visualiseren

Gebruik de [widget notitie blok](/python/api/azureml-widgets/azureml.widgets.rundetails?preserve-view=true&view=azure-ml-py) om de voortgang van uw trainings uitvoeringen te visualiseren. In het volgende code fragment worden alle afstemming-afstemmings uitvoeringen op één plek in een Jupyter-notebook gevisualiseerd:

```Python
from azureml.widgets import RunDetails
RunDetails(hyperdrive_run).show()
```

Met deze code wordt een tabel weer gegeven met informatie over de trainings uitvoeringen voor elk van de afstemming-configuraties.

![afstemming tuning Table](./media/how-to-tune-hyperparameters/HyperparameterTuningTable.png)

U kunt ook de prestaties van elk van de uitvoeringen visualiseren als de voortgang van de training. 

![afstemming-afstemmings plot](./media/how-to-tune-hyperparameters/HyperparameterTuningPlot.png)

U kunt de correlatie tussen de prestaties en waarden van afzonderlijke Hyper parameters visueel identificeren met behulp van een parallelle coördinaten tekening. 

[![parallelle coördinaten voor afstemming afstemmen](./media/how-to-tune-hyperparameters/HyperparameterTuningParallelCoordinates.png)](media/how-to-tune-hyperparameters/hyperparameter-tuning-parallel-coordinates-expanded.png)

U kunt ook al uw afstemming-afstemmings uitvoeringen visualiseren in de Azure-webportal. Zie [experimenten volgen](how-to-monitor-view-training-logs.md#view-the-experiment-in-the-web-portal)voor meer informatie over het weer geven van een experiment in de portal.

## <a name="find-the-best-model"></a>Het beste model zoeken

Wanneer alle afstemmings uitvoeringen van afstemming zijn voltooid, identificeert u de best presterende configuratie-en afstemming-waarden:

```Python
best_run = hyperdrive_run.get_best_run_by_primary_metric()
best_run_metrics = best_run.get_metrics()
parameter_values = best_run.get_details()['runDefinition']['Arguments']

print('Best Run Id: ', best_run.id)
print('\n Accuracy:', best_run_metrics['accuracy'])
print('\n learning rate:',parameter_values[3])
print('\n keep probability:',parameter_values[5])
print('\n batch size:',parameter_values[7])
```

## <a name="sample-notebook"></a>Voorbeeldnotebook
Raadpleeg Train-afstemming-*-notebooks in deze map:
* [procedures voor het gebruik van azureml/ml-Frameworks](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Volgende stappen
* [Een experiment volgen](how-to-track-experiments.md)
* [Een getraind model implementeren](how-to-deploy-and-where.md)