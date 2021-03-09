---
title: Een model afstemming afstemmen
titleSuffix: Azure Machine Learning
description: Automatiseer afstemming tuning voor diep leren en machine learning modellen met behulp van Azure Machine Learning.
ms.author: anumamah
author: Aniththa
ms.reviewer: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.date: 02/26/2021
ms.topic: conceptual
ms.custom: how-to, devx-track-python, contperf-fy21q1
ms.openlocfilehash: 34adcf2218e29572ec9a86583addc7c021313085
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102519636"
---
# <a name="hyperparameter-tuning-a-model-with-azure-machine-learning"></a>Afstemming afstemmen op een model met Azure Machine Learning

Automatiseer efficiënt afstemmen van afstemming met behulp van Azure Machine Learning [HyperDrive-pakket](/python/api/azureml-train-core/azureml.train.hyperdrive). Meer informatie over het uitvoeren van de stappen die nodig zijn om Hyper parameters af te stemmen met de [Azure machine learning SDK](/python/api/overview/azure/ml/):

1. De zoek ruimte voor de para meter definiëren
1. Geef een primaire metriek op om te optimaliseren  
1. Beleid voor vroegtijdige beëindiging opgeven voor uitvoeringen die weinig worden uitgevoerd
1. Resources maken en toewijzen
1. Een experiment met de gedefinieerde configuratie starten
1. De trainings uitvoeringen visualiseren
1. De beste configuratie voor uw model selecteren

## <a name="what-is-hyperparameter-tuning"></a>Wat is afstemming tuning?

**Hyper parameters** zijn aanpas bare para meters waarmee u het model trainings proces kunt beheren. Met Neural-netwerken bepaalt u bijvoorbeeld het aantal verborgen lagen en het aantal knoop punten in elke laag. Model prestaties zijn sterk afhankelijk van Hyper parameters.

 **Afstemming tuning**, ook wel **afstemming Optimization** genoemd, is het proces van het vinden van de configuratie van Hyper parameters dat resulteert in de beste prestaties. Het proces is doorgaans kostbaar en hand matig.

Met Azure Machine Learning kunt u afstemming-tuning automatiseren en experimenten parallel gebruiken om Hyper parameters efficiënt te optimaliseren.


## <a name="define-the-search-space"></a>De zoek ruimte definiëren

Hyper parameters afstemmen door het bereik van waarden te verkennen dat voor elke afstemming is gedefinieerd.

Hyper parameters kan afzonderlijk of doorlopend zijn en heeft een distributie van waarden die worden beschreven door een [parameter expressie](/python/api/azureml-train-core/azureml.train.hyperdrive.parameter_expressions).

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

[Wille keurige steek proeven](/python/api/azureml-train-core/azureml.train.hyperdrive.randomparametersampling) ondersteunen discrete en doorlopende Hyper parameters. Het biedt ondersteuning voor vroege beëindiging van uitvoeringen met lage prestaties. Sommige gebruikers voeren een eerste zoek opdracht uit met wille keurige steek proeven en verfijnen de zoek ruimte om de resultaten te verbeteren.

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

[Raster sampling](/python/api/azureml-train-core/azureml.train.hyperdrive.gridparametersampling) ondersteunt discrete Hyper parameters. Gebruik raster sampling als u een budget kunt gebruiken om de zoek ruimte uitgebreid te doorzoeken. Biedt ondersteuning voor vroegtijdige beëindiging van uitvoeringen met lage prestaties.

Raster sampling voert een eenvoudige raster zoekactie uit op alle mogelijke waarden. Raster sampling kan alleen worden gebruikt met `choice` Hyper parameters. De volgende ruimte heeft bijvoorbeeld zes voor beelden:

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

[Bayesiaanse-steek proeven](/python/api/azureml-train-core/azureml.train.hyperdrive.bayesianparametersampling) zijn gebaseerd op het Bayesiaanse-optimalisatie algoritme. Er worden voor beelden gekozen op basis van de manier waarop eerdere steek proeven zijn uitgevoerd, zodat nieuwe voor beelden de primaire metriek verbeteren.

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

Geef de [primaire metriek](/python/api/azureml-train-core/azureml.train.hyperdrive.primarymetricgoal) op die u wilt afstemming optimaliseren. Elke trainings uitvoering wordt geëvalueerd voor de primaire metriek. Voor het beleid voor vroegtijdige beëindiging wordt gebruikgemaakt van de primaire metriek om uitvoeringen met een lage prestaties te identificeren.

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

Laat de uitvoering van een vroegtijdig afsluitings beleid automatisch beëindigen. Vroege beëindiging verbetert de verwerkings efficiëntie.

U kunt de volgende para meters configureren die bepalen wanneer een beleid wordt toegepast:

* `evaluation_interval`: de frequentie waarmee het beleid wordt toegepast. Telkens wanneer het trainings script de primaire metriek registreert als één interval. `evaluation_interval`Als 1 wordt het beleid toegepast wanneer het trainings script de primaire metriek rapporteert. `evaluation_interval`Met 2 wordt het beleid elke keer toegepast. Als u niets opgeeft, `evaluation_interval` wordt standaard ingesteld op 1.
* `delay_evaluation`: de eerste beleids evaluatie voor een opgegeven aantal intervallen wordt uitgesteld. Dit is een optionele para meter waarmee het voor tijdig beëindigen van de trainings uitvoeringen wordt voor komen door ervoor te zorgen dat alle configuraties voor een minimum aantal intervallen worden uitgevoerd. Indien opgegeven, wordt het beleid toegepast op elk veelvoud van evaluation_interval dat groter is dan of gelijk is aan delay_evaluation.

Azure Machine Learning ondersteunt de volgende beleids regels voor vroegtijdige beëindiging:
* [Bandit-beleid](#bandit-policy)
* [Beleid voor mediaan stoppen](#median-stopping-policy)
* [Selectie beleid voor afkap ping](#truncation-selection-policy)
* [Geen beleid voor beëindiging](#no-termination-policy-default)


### <a name="bandit-policy"></a>Bandit-beleid

Het [Bandit-beleid](/python/api/azureml-train-core/azureml.train.hyperdrive.banditpolicy#definition) is gebaseerd op de toegestane factor en het toegestane aantal en de evaluatie-interval. Bandit-uiteinden worden uitgevoerd wanneer de primaire metriek zich niet binnen de opgegeven toegestane vertragings factor/toegestane vertraging bevindt.

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

[Mediaan stoppen](/python/api/azureml-train-core/azureml.train.hyperdrive.medianstoppingpolicy) is een beleid voor vroegtijdige beëindiging op basis van het actieve gemiddelde van primaire metrische gegevens die door de uitvoeringen worden gerapporteerd. Dit beleid berekent de lopende gemiddelden voor alle trainings uitvoeringen en stopt met uitvoeringen waarvan de primaire meet waarde erger is dan de mediaan van de gemiddelden.

Voor dit beleid worden de volgende configuratie parameters gebruikt:
* `evaluation_interval`: de frequentie voor het Toep assen van het beleid (optionele para meter).
* `delay_evaluation`: de eerste beleids evaluatie wordt uitgesteld voor een opgegeven aantal intervallen (optionele para meter).


```Python
from azureml.train.hyperdrive import MedianStoppingPolicy
early_termination_policy = MedianStoppingPolicy(evaluation_interval=1, delay_evaluation=5)
```

In dit voor beeld wordt het beleid voor vroegtijdige beëindiging toegepast op elk interval, te beginnen bij de evaluatie-interval 5. Een uitvoering wordt gestopt bij interval 5 als de beste primaire gegevens slechter zijn dan de mediaan van de lopende gemiddelden over intervallen 1:5 voor alle trainings uitvoeringen.

### <a name="truncation-selection-policy"></a>Selectie beleid voor afkap ping

Met de selectie van de [Afkap ping](/python/api/azureml-train-core/azureml.train.hyperdrive.truncationselectionpolicy) wordt een percentage van de laagste uitgevoerde uitvoeringen bij elk evaluatie-interval geannuleerd. Uitvoeringen worden vergeleken met de primaire metriek. 

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

## <a name="create-and-assign-resources"></a>Resources maken en toewijzen

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

## <a name="configure-hyperparameter-tuning-experiment"></a>Afstemming tuning-experiment configureren

Als u [uw afstemming tuning](/python/api/azureml-train-core/azureml.train.hyperdrive.hyperdriverunconfig) -experiment wilt configureren, geeft u het volgende op:
* De gedefinieerde afstemming-Zoek ruimte
* Uw beleid voor vroegtijdige beëindiging
* De primaire metriek
* Toewijzings instellingen voor resources
* ScriptRunConfig `script_run_config`

De ScriptRunConfig is het trainings script dat wordt uitgevoerd met de Hyper parameters van de steek proef. Het definieert de resources per taak (één of meerdere knoop punten) en het reken doel dat moet worden gebruikt.

> [!NOTE]
>Het reken doel dat wordt gebruikt in `script_run_config` moet voldoende resources hebben om te voldoen aan uw gelijktijdigheids niveau. Zie [Configure training runs](how-to-set-up-training-targets.md)(Engelstalig) voor meer informatie over ScriptRunConfig.

Uw afstemming-afstemmings experiment configureren:

```Python
from azureml.train.hyperdrive import HyperDriveConfig
from azureml.train.hyperdrive import RandomParameterSampling, BanditPolicy, uniform, PrimaryMetricGoal

param_sampling = RandomParameterSampling( {
        'learning_rate': uniform(0.0005, 0.005),
        'momentum': uniform(0.9, 0.99)
    }
)

early_termination_policy = BanditPolicy(slack_factor=0.15, evaluation_interval=1, delay_evaluation=10)

hd_config = HyperDriveConfig(run_config=script_run_config,
                             hyperparameter_sampling=param_sampling,
                             policy=early_termination_policy,
                             primary_metric_name="accuracy",
                             primary_metric_goal=PrimaryMetricGoal.MAXIMIZE,
                             max_total_runs=100,
                             max_concurrent_runs=4)
```

`HyperDriveConfig`Hiermee stelt u de para meters die worden door gegeven aan de `ScriptRunConfig script_run_config` . De `script_run_config` , op zijn beurt geeft para meters door aan het trainings script. Het bovenstaande code fragment is afkomstig uit de voor beeld [-notebook Train, afstemming Tune en Deploy with PyTorch](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/pytorch/train-hyperparameter-tune-deploy-with-pytorch). In dit voor beeld `learning_rate` worden de `momentum` para meters en ingesteld op afgestemd. Eerdere stops van uitvoeringen worden bepaald door een `BanditPolicy` , waardoor een uitvoering wordt gestopt waarvan de primaire meet waarde buiten de ligt `slack_factor` (Zie [BanditPolicy-klassen verwijzing](/python/api/azureml-train-core/azureml.train.hyperdrive.banditpolicy)). 

De volgende code uit het voor beeld laat zien hoe de afgestemde waarden worden ontvangen, geparseerd en worden door gegeven aan de functie van het trainings script `fine_tune_model` :

```python
# from pytorch_train.py
def main():
    print("Torch version:", torch.__version__)

    # get command-line arguments
    parser = argparse.ArgumentParser()
    parser.add_argument('--num_epochs', type=int, default=25,
                        help='number of epochs to train')
    parser.add_argument('--output_dir', type=str, help='output directory')
    parser.add_argument('--learning_rate', type=float,
                        default=0.001, help='learning rate')
    parser.add_argument('--momentum', type=float, default=0.9, help='momentum')
    args = parser.parse_args()

    data_dir = download_data()
    print("data directory is: " + data_dir)
    model = fine_tune_model(args.num_epochs, data_dir,
                            args.learning_rate, args.momentum)
    os.makedirs(args.output_dir, exist_ok=True)
    torch.save(model, os.path.join(args.output_dir, 'model.pt'))
```

> [!Important]
> Bij elke afstemming wordt de training opnieuw gestart, met inbegrip van het opnieuw samen stellen van het model en _alle gegevens laden_. U kunt deze kosten tot een minimum beperken door een Azure Machine Learning pijp lijn of hand matig proces te gebruiken om zoveel mogelijk gegevens voorbereiding te doen voordat uw training wordt uitgevoerd. 

## <a name="submit-hyperparameter-tuning-experiment"></a>Afstemming-afstemmings experiment verzenden

Nadat u de afstemming-afstemmings configuratie hebt gedefinieerd, moet u [het experiment verzenden](/python/api/azureml-core/azureml.core.experiment%28class%29#submit-config--tags-none----kwargs-):

```Python
from azureml.core.experiment import Experiment
experiment = Experiment(workspace, experiment_name)
hyperdrive_run = experiment.submit(hd_config)
```

## <a name="warm-start-hyperparameter-tuning-optional"></a>Warme start afstemming tuning (optioneel)

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

hd_config = HyperDriveConfig(run_config=script_run_config,
                             hyperparameter_sampling=param_sampling,
                             policy=early_termination_policy,
                             resume_from=warmstart_parents_to_resume_from,
                             resume_child_runs=child_runs_to_resume,
                             primary_metric_name="accuracy",
                             primary_metric_goal=PrimaryMetricGoal.MAXIMIZE,
                             max_total_runs=100,
                             max_concurrent_runs=4)
```

## <a name="visualize-hyperparameter-tuning-runs"></a>Afstemming tuning-uitvoeringen visualiseren

U kunt uw afstemming-afstemmings uitvoeringen visualiseren in de Azure Machine Learning Studio of u kunt een notebook-widget gebruiken.

### <a name="studio"></a>Studio

U kunt al uw afstemming-afstemmings uitvoeringen visualiseren in de [Azure machine learning Studio](https://ml.azure.com). Zie [Run records weer geven in de Studio](how-to-monitor-view-training-logs.md#view-the-experiment-in-the-web-portal)voor meer informatie over het weer geven van een experiment in de portal.

- **Grafiek met metrische gegevens**: deze visualisatie houdt de metrische gegevens bij die zijn geregistreerd voor elke onderliggende Hyperdrive-ondergeschiktheid tijdens de duur van het afstemmen van afstemming. Elke regel vertegenwoordigt een onderliggende uitvoering en elk punt meet de primaire metrische waarde bij die herhaling van runtime.  

    :::image type="content" source="media/how-to-tune-hyperparameters/hyperparameter-tuning-metrics.png" alt-text="Grafiek met metrische gegevens voor afstemming afstemmen":::

- **Parallelle coördinaten grafiek**: deze visualisatie toont de correlatie tussen primaire metrische prestatie-en individuele afstemming-waarden. De grafiek is interactief via verplaatsing van assen (klikken en slepen op het aslabel) en door waarden te markeren in één as (klik en sleep verticaal langs één as om een bereik van gewenste waarden te markeren).

    :::image type="content" source="media/how-to-tune-hyperparameters/hyperparameter-tuning-parallel-coordinates.png" alt-text="Grafiek met parallelle coördinaten voor afstemming tuning":::

- **2-dimensionale spreidings diagram**: deze visualisatie toont de correlatie tussen twee afzonderlijke Hyper parameters samen met de bijbehorende primaire meet waarde.

    :::image type="content" source="media/how-to-tune-hyperparameters/hyperparameter-tuning-2-dimensional-scatter.png" alt-text="Hyparameter tuning 2-dimensionale spreidings diagram":::

- **3D-spreidings diagram**: deze visualisatie is hetzelfde als 2D, maar biedt drie afstemming dimensies van correlatie met de primaire meet waarde. U kunt ook klikken en slepen om de grafiek te verplaatsen om verschillende correlaties in de 3D-ruimte weer te geven.

    :::image type="content" source="media/how-to-tune-hyperparameters/hyperparameter-tuning-3-dimensional-scatter.png" alt-text="Hyparameter tuning 3D-spreidings diagram":::

### <a name="notebook-widget"></a>Notebook-widget

Gebruik de [widget notitie blok](/python/api/azureml-widgets/azureml.widgets.rundetails) om de voortgang van uw trainings uitvoeringen te visualiseren. In het volgende code fragment worden alle afstemming-afstemmings uitvoeringen op één plek in een Jupyter-notebook gevisualiseerd:

```Python
from azureml.widgets import RunDetails
RunDetails(hyperdrive_run).show()
```

Met deze code wordt een tabel weer gegeven met informatie over de trainings uitvoeringen voor elk van de afstemming-configuraties.

:::image type="content" source="media/how-to-tune-hyperparameters/hyperparameter-tuning-table.png" alt-text="Afstemming tuning Table":::

U kunt ook de prestaties van elk van de uitvoeringen visualiseren als de voortgang van de training.

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
