---
title: Hyperparameter-afstemming van een model
titleSuffix: Azure Machine Learning
description: Automatiseer hyperparameter-afstemming voor deep learning en machine learning modellen met behulp Azure Machine Learning.
ms.author: anumamah
author: Aniththa
ms.reviewer: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.date: 02/26/2021
ms.topic: how-to
ms.custom: devx-track-python, contperf-fy21q1
ms.openlocfilehash: 77d5a5bda3e108b0366e27cb2c8c16986633f71d
ms.sourcegitcommit: 5ce88326f2b02fda54dad05df94cf0b440da284b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107888382"
---
# <a name="hyperparameter-tuning-a-model-with-azure-machine-learning"></a>Hyperparameter die een model afstemmt met Azure Machine Learning

Automatiseer efficiënte afstemming van hyperparameters met behulp Azure Machine Learning [HyperDrive-pakket](/python/api/azureml-train-core/azureml.train.hyperdrive). Meer informatie over het voltooien van de stappen die nodig zijn om hyperparameters af te stemmen met [de Azure Machine Learning SDK](/python/api/overview/azure/ml/):

1. De zoekruimte van de parameter definiëren
1. Een primaire metrische gegevens opgeven om te optimaliseren  
1. Beleid voor vroegtijdige beëindiging opgeven voor slecht presterende uitvoeringen
1. Resources maken en toewijzen
1. Een experiment starten met de gedefinieerde configuratie
1. De trainings runs visualiseren
1. Selecteer de beste configuratie voor uw model

## <a name="what-is-hyperparameter-tuning"></a>Wat is hyperparameter-afstemming?

**Hyperparameters zijn** aanpasbare parameters die u in staat stellen het modeltrainingsproces te bepalen. Met neurale netwerken bepaalt u bijvoorbeeld het aantal verborgen lagen en het aantal knooppunten in elke laag. De modelprestaties zijn sterk afhankelijk van hyperparameters.

 **Hyperparameter-afstemming,** ook wel **hyperparameteroptimalisatie** genoemd, is het proces van het vinden van de configuratie van hyperparameters die de beste prestaties opleveren. Het proces is doorgaans kostbaar en handmatig.

Azure Machine Learning kunt u hyperparameter-afstemming automatiseren en experimenten parallel uitvoeren om hyperparameters efficiënt te optimaliseren.


## <a name="define-the-search-space"></a>De zoekruimte definiëren

U kunt hyperparameters afstemmen door het bereik van waarden te verkennen dat voor elke hyperparameter is gedefinieerd.

Hyperparameters kunnen discrete of continue zijn en hebben een distributie van waarden die worden beschreven door een [parameterexpressie](/python/api/azureml-train-core/azureml.train.hyperdrive.parameter_expressions).

### <a name="discrete-hyperparameters"></a>Discrete hyperparameters

Discrete hyperparameters worden opgegeven als een tussen `choice` discrete waarden. `choice` kan het volgende zijn:

* een of meer door komma's gescheiden waarden
* een `range` -object
* willekeurig `list` object


```Python
    {
        "batch_size": choice(16, 32, 64, 128)
        "number_of_hidden_layers": choice(range(1,5))
    }
```

In dit geval neemt een van de `batch_size` waarden [16, 32, 64, 128] en een van de `number_of_hidden_layers` waarden [1, 2, 3, 4].

De volgende geavanceerde discrete hyperparameters kunnen ook worden opgegeven met behulp van een distributie:

* `quniform(low, high, q)` - Retourneert een waarde zoals round(uniform(low, high) /q) * q
* `qloguniform(low, high, q)` - Retourneert een waarde zoals round(exp(uniform(low, high)) / q) * q
* `qnormal(mu, sigma, q)` - Retourneert een waarde zoals round(normal(mu, sigma) / q) * q
* `qlognormal(mu, sigma, q)` - Retourneert een waarde zoals round(exp(normal(mu, sigma)) / q) * q

### <a name="continuous-hyperparameters"></a>Doorlopende hyperparameters 

De continue hyperparameters worden opgegeven als een distributie over een doorlopend bereik van waarden:

* `uniform(low, high)` - Retourneert een waarde die gelijkmatig is verdeeld tussen laag en hoog
* `loguniform(low, high)` - Retourneert een waarde die is getekend op basis van exp(uniform(low, high)) zodat de logaritme van de retourwaarde gelijkmatig wordt verdeeld
* `normal(mu, sigma)` - Retourneert een echte waarde die normaal gesproken wordt gedistribueerd met gemiddelde mu en standaarddeviatie sigma
* `lognormal(mu, sigma)` - Retourneert een waarde die is getekend op basis van exp(normal(mu, sigma)) zodat de logaritme van de retourwaarde normaal wordt gedistribueerd

Een voorbeeld van een parameterruimtedefinitie:

```Python
    {    
        "learning_rate": normal(10, 3),
        "keep_probability": uniform(0.05, 0.1)
    }
```

Deze code definieert een zoekruimte met twee parameters: `learning_rate` en `keep_probability` . `learning_rate` heeft een normale verdeling met gemiddelde waarde 10 en een standaardafwijking van 3. `keep_probability` heeft een uniforme verdeling met een minimumwaarde van 0,05 en een maximumwaarde van 0,1.

### <a name="sampling-the-hyperparameter-space"></a>Steekproeven nemen van de hyperparameterruimte

Geef de steekproefmethode voor parameters op die moet worden gebruikt voor de hyperparameterruimte. Azure Machine Learning ondersteunt de volgende methoden:

* Willekeurige steekproeven
* Rastersampling
* Bayesiaanse steekproeven

#### <a name="random-sampling"></a>Willekeurige steekproeven

[Willekeurige steekproeven](/python/api/azureml-train-core/azureml.train.hyperdrive.randomparametersampling) ondersteunen discrete en continue hyperparameters. Het ondersteunt vroege beëindiging van uitvoeringen met lage prestaties. Sommige gebruikers doen een eerste zoekopdracht met willekeurige steekproeven en verfijnen vervolgens de zoekruimte om de resultaten te verbeteren.

Bij willekeurige steekproeven worden hyperparameterwaarden willekeurig geselecteerd uit de gedefinieerde zoekruimte. 

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

#### <a name="grid-sampling"></a>Rastersampling

[Rastersampling](/python/api/azureml-train-core/azureml.train.hyperdrive.gridparametersampling) ondersteunt discrete hyperparameters. Gebruik rastersampling als u kunt budgetten hebt om de zoekruimte volledig te doorzoeken. Ondersteunt vroege beëindiging van uitvoeringen met lage prestaties.

Met rastersampling wordt een eenvoudige rasterzoekactie uitgevoerd op alle mogelijke waarden. Rastersampling kan alleen worden gebruikt met `choice` hyperparameters. De volgende ruimte heeft bijvoorbeeld zes voorbeelden:

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

[Bayesiaanse steekproeven](/python/api/azureml-train-core/azureml.train.hyperdrive.bayesianparametersampling) zijn gebaseerd op het Optimalisatiealgoritme van Bayesisch. Het kiest voorbeelden op basis van de vorige voorbeelden, zodat nieuwe voorbeelden de primaire metrische gegevens verbeteren.

Bayesiaanse steekproeven worden aanbevolen als u voldoende budget hebt om de hyperparameterruimte te verkennen. Voor de beste resultaten raden we een maximum aantal runs aan dat groter is dan of gelijk is aan 20 keer het aantal hyperparameters dat wordt afgestemd. 

Het aantal gelijktijdige runs heeft invloed op de effectiviteit van het afstemmingsproces. Een kleiner aantal gelijktijdige runs kan leiden tot betere convergentie van steekproeven, omdat de kleinere mate van parallelle parallellelisme het aantal runs verhoogt dat voordeel heeft van eerder voltooide runs.

Bayesiaanse steekproeven bieden alleen ondersteuning `choice` voor `uniform` , en `quniform` distributies over de zoekruimte.

```Python
from azureml.train.hyperdrive import BayesianParameterSampling
from azureml.train.hyperdrive import uniform, choice
param_sampling = BayesianParameterSampling( {
        "learning_rate": uniform(0.05, 0.1),
        "batch_size": choice(16, 32, 64, 128)
    }
)
```



## <a name="specify-primary-metric"></a><a name="specify-primary-metric-to-optimize"></a> Primaire metrische gegevens opgeven

Geef de [primaire metrische gegevens op](/python/api/azureml-train-core/azureml.train.hyperdrive.primarymetricgoal) die u wilt afstemmen op hyperparameters om te optimaliseren. Elke trainingsrun wordt geëvalueerd voor de primaire metrische gegevens. Het beleid voor vroegtijdige beëindiging maakt gebruik van de primaire metrische gegevens om uitvoeringen met lage prestaties te identificeren.

Geef de volgende kenmerken op voor uw primaire metrische gegevens:

* `primary_metric_name`: De naam van de primaire metrische gegevens moet exact overeenkomen met de naam van de metrische gegevens die zijn geregistreerd door het trainingsscript
* `primary_metric_goal`: Dit kan of zijn en bepaalt of de primaire metrische gegevens worden gemaximaliseerd of geminimaliseerd bij het `PrimaryMetricGoal.MAXIMIZE` `PrimaryMetricGoal.MINIMIZE` evalueren van de runs. 

```Python
primary_metric_name="accuracy",
primary_metric_goal=PrimaryMetricGoal.MAXIMIZE
```

In dit voorbeeld wordt de 'nauwkeurigheid' gemaximaliseerd.

### <a name="log-metrics-for-hyperparameter-tuning"></a><a name="log-metrics-for-hyperparameter-tuning"></a>Metrische logboekgegevens voor het afstemmen van hyperparameters

Het trainingsscript voor uw model **moet** de primaire metrische gegevens tijdens het trainen van het model in een logboek houden, zodat HyperDrive er toegang toe heeft voor het afstemmen van hyperparameters.

Registreer de primaire metrische gegevens in uw trainingsscript met het volgende voorbeeldfragment:

```Python
from azureml.core.run import Run
run_logger = Run.get_context()
run_logger.log("accuracy", float(val_accuracy))
```

Het trainingsscript berekent de `val_accuracy` en registreert deze als de primaire metrische 'nauwkeurigheid'. Telkens als de metrische gegevens worden geregistreerd, wordt deze ontvangen door de afstemmingsservice voor hyperparameters. Het is aan u om de frequentie van de rapportage te bepalen.

Zie Logboekregistratie inschakelen in Azure [ML-trainings](how-to-log-view-metrics.md)runs voor meer informatie over het vastleggen van waarden in modeltrainingen.

## <a name="specify-early-termination-policy"></a><a name="early-termination"></a> Beleid voor vroegtijdige beëindiging opgeven

Slecht presterende uitvoeringen automatisch beëindigen met een beleid voor vroegtijdige beëindiging. Vroege beëindiging verbetert de rekenefficiëntie.

U kunt de volgende parameters configureren die bepalen wanneer een beleid wordt toegepast:

* `evaluation_interval`: de frequentie waarmee het beleid wordt toegepast. Telkens wanneer het trainingsscript logboeken registreert, telt de primaire metrische gegevens als één interval. Met `evaluation_interval` een van 1 wordt het beleid steeds toegepast wanneer het trainingsscript de primaire metrische gegevens rapporteert. Met `evaluation_interval` een van 2 wordt het beleid elke andere keer toegepast. Indien niet opgegeven, `evaluation_interval` is standaard ingesteld op 1.
* `delay_evaluation`: vertraagt de eerste beleidsevaluatie voor een opgegeven aantal intervallen. Dit is een optionele parameter die voortijdige beëindiging van trainingsruns voorkomt door alle configuraties voor een minimum aantal intervallen uit te voeren. Indien opgegeven, past het beleid elk veelvoud van evaluation_interval die groter is dan of gelijk is aan delay_evaluation.

Azure Machine Learning ondersteunt de volgende beleidsregels voor vroegtijdige beëindiging:
* [Banditbeleid](#bandit-policy)
* [Beleid voor mediaan stoppen](#median-stopping-policy)
* [Selectiebeleid voor afroepen](#truncation-selection-policy)
* [Geen beëindigingsbeleid](#no-termination-policy-default)


### <a name="bandit-policy"></a>Banditbeleid

[Het banditbeleid](/python/api/azureml-train-core/azureml.train.hyperdrive.banditpolicy#definition) is gebaseerd op slack-factor/slack-hoeveelheid en evaluatie-interval. Bandit wordt uitgevoerd wanneer de primaire metrische gegevens zich niet binnen de opgegeven slack-factor/slack-hoeveelheid van de meest geslaagde run.

> [!NOTE]
> Bayesiaanse steekproeven bieden geen ondersteuning voor vroegtijdige beëindiging. Wanneer u Bayesiaanse steekproeven gebruikt, stelt u `early_termination_policy = None` in.

Geef de volgende configuratieparameters op:

* `slack_factor` of `slack_amount` : de slack die is toegestaan met betrekking tot de best presterende trainingsrun. `slack_factor` geeft de toegestane slack aan als een verhouding. `slack_amount` geeft de toegestane slack aan als een absoluut bedrag in plaats van een verhouding.

    Denk bijvoorbeeld aan een Bandit-beleid dat wordt toegepast op interval 10. Stel dat de best presterende uitvoering op interval 10 een primaire metrische waarde heeft gerapporteerd, 0,8 is met het doel om het primaire metrische gegevens te maximaliseren. Als in het beleid een van 0,2 wordt opgegeven, wordt elke training beëindigd waarvan de beste metrische gegevens bij interval 10 kleiner zijn dan `slack_factor` 0,66 (0,8/(1+)). `slack_factor`
* `evaluation_interval`: (optioneel) de frequentie voor het toepassen van het beleid
* `delay_evaluation`: (optioneel) vertraagt de eerste beleidsevaluatie voor een opgegeven aantal intervallen


```Python
from azureml.train.hyperdrive import BanditPolicy
early_termination_policy = BanditPolicy(slack_factor = 0.1, evaluation_interval=1, delay_evaluation=5)
```

In dit voorbeeld wordt het beleid voor vroegtijdige beëindiging toegepast op elk interval wanneer metrische gegevens worden gerapporteerd, te beginnen bij evaluatie-interval 5. Elke uitvoering waarvan de beste metrische gegevens kleiner zijn dan (1/(1+0.1) of 91% van de best presterende uitvoering, wordt beëindigd.

### <a name="median-stopping-policy"></a>Mediaan stopbeleid

[Mediaan stoppen](/python/api/azureml-train-core/azureml.train.hyperdrive.medianstoppingpolicy) is een beleid voor vroegtijdige beëindiging op basis van de lopende gemiddelden van primaire metrische gegevens die door de runs worden gerapporteerd. Met dit beleid worden de gemiddelden voor het uitvoeren van alle trainings runs berekend en worden runs gestopt waarvan de primaire metrische waarde slechter is dan de mediaan van de gemiddelden.

Voor dit beleid worden de volgende configuratieparameters gebruikt:
* `evaluation_interval`: de frequentie voor het toepassen van het beleid (optionele parameter).
* `delay_evaluation`: vertraagt de eerste beleidsevaluatie voor een opgegeven aantal intervallen (optionele parameter).


```Python
from azureml.train.hyperdrive import MedianStoppingPolicy
early_termination_policy = MedianStoppingPolicy(evaluation_interval=1, delay_evaluation=5)
```

In dit voorbeeld wordt het vroege beëindigingsbeleid toegepast op elk interval, beginnend bij evaluatie-interval 5. Een run wordt gestopt met interval 5 als de beste primaire metriek slechter is dan de mediaan van de gemiddelde van de lopende gedurende intervallen van 1:5 voor alle trainingsruns.

### <a name="truncation-selection-policy"></a>Selectiebeleid voor afroepen

[Selectie van afzetting annuleert](/python/api/azureml-train-core/azureml.train.hyperdrive.truncationselectionpolicy) een percentage van de laagst presterende uitvoeringen op elk evaluatie-interval. Runs worden vergeleken met behulp van de primaire metrische gegevens. 

Dit beleid heeft de volgende configuratieparameters:

* `truncation_percentage`: het percentage van de laagst presterende uitvoeringen dat moet worden beëindigd bij elk evaluatie-interval. Een geheel getal tussen 1 en 99.
* `evaluation_interval`: (optioneel) de frequentie voor het toepassen van het beleid
* `delay_evaluation`: (optioneel) vertraagt de eerste beleidsevaluatie voor een opgegeven aantal intervallen


```Python
from azureml.train.hyperdrive import TruncationSelectionPolicy
early_termination_policy = TruncationSelectionPolicy(evaluation_interval=1, truncation_percentage=20, delay_evaluation=5)
```

In dit voorbeeld wordt het vroege beëindigingsbeleid toegepast op elk interval, beginnend bij evaluatie-interval 5. Een uitvoering wordt beëindigd met interval 5 als de prestaties op interval 5 de laagste 20% van de prestaties van alle uitvoeringen met interval 5 hebben.

### <a name="no-termination-policy-default"></a>Geen beëindigingsbeleid (standaard)

Als er geen beleid is opgegeven, kan de afstemmingsservice voor hyperparameters alle trainings uitgevoerd tot voltooiing.

```Python
policy=None
```

### <a name="picking-an-early-termination-policy"></a>Een beleid voor vroegtijdige beëindiging kiezen

* Voor een voorzichtig beleid dat besparingen biedt zonder het beëindigen van beloften, kunt u een mediaan stopbeleid overwegen met `evaluation_interval` 1 en `delay_evaluation` 5. Dit zijn voorzichtige instellingen, die een besparing van ongeveer 25%-35% kunnen opleveren zonder verlies van primaire metrische gegevens (op basis van onze evaluatiegegevens).
* Gebruik voor agressievere besparingen Bandit-beleid met een kleinere toegestane slack of afdronk selectiebeleid met een groter aflopend percentage.

## <a name="create-and-assign-resources"></a>Resources maken en toewijzen

Beheer uw resourcebudget door het maximum aantal trainings runs op te geven.

* `max_total_runs`: Maximum aantal trainings runs. Moet een geheel getal tussen 1 en 1000 zijn.
* `max_duration_minutes`: (optioneel) Maximale duur, in minuten, van het afstemmingsexperiment voor hyperparameters. Wordt uitgevoerd nadat deze duur is geannuleerd.

>[!NOTE] 
>Als zowel `max_total_runs` als `max_duration_minutes` zijn opgegeven, wordt het afstemmingsexperiment voor hyperparameters beëindigd wanneer de eerste van deze twee drempelwaarden wordt bereikt.

Geef daarnaast het maximum aantal trainingsruns op dat gelijktijdig moet worden uitgevoerd tijdens het afstemmen van de hyperparameter.

* `max_concurrent_runs`: (optioneel) Maximum aantal runs dat gelijktijdig kan worden uitgevoerd. Als dit niet wordt opgegeven, worden alle runs parallel uitgevoerd. Indien opgegeven, moet een geheel getal tussen 1 en 100 zijn.

>[!NOTE] 
>Het aantal gelijktijdige runs wordt beperkt tot de resources die beschikbaar zijn in het opgegeven rekendoel. Zorg ervoor dat het rekendoel de beschikbare resources voor de gewenste gelijktijdigheid heeft.

```Python
max_total_runs=20,
max_concurrent_runs=4
```

Met deze code wordt het afstemmingsexperiment voor hyperparameters geconfigureerd voor het gebruik van maximaal 20 totaal aantal uitvoeringen, met vier configuraties tegelijk.

## <a name="configure-hyperparameter-tuning-experiment"></a>Hyperparameter-afstemmingsexperiment configureren

Geef [het volgende op om uw experiment voor het afstemmen van hyperparameters](/python/api/azureml-train-core/azureml.train.hyperdrive.hyperdriverunconfig) te configureren:
* De gedefinieerde zoekruimte voor hyperparameters
* Uw beleid voor vroegtijdige beëindiging
* De primaire metrische gegevens
* Instellingen voor resourcetoewijzing
* ScriptRunConfig `script_run_config`

ScriptRunConfig is het trainingsscript dat wordt uitgevoerd met de voorbeelden van hyperparameters. Het definieert de resources per taak (één of meerdere knooppunt) en het rekendoel dat moet worden gebruikt.

> [!NOTE]
>Het rekendoel dat in `script_run_config` wordt gebruikt, moet voldoende resources hebben om te voldoen aan uw gelijktijdigheidsniveau. Zie Trainingsuit runs configureren voor meer informatie [over](how-to-set-up-training-targets.md)ScriptRunConfig.

Configureer uw experiment voor het afstemmen van hyperparameters:

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

De `HyperDriveConfig` stelt de parameters in die worden doorgegeven aan de `ScriptRunConfig script_run_config` . De `script_run_config` geeft op zijn beurt parameters door aan het trainingsscript. Het bovenstaande codefragment is afkomstig uit het voorbeeldnoteboek Trainen, hyperparameter afstemmen en implementeren [met PyTorch](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/pytorch/train-hyperparameter-tune-deploy-with-pytorch). In dit voorbeeld worden de `learning_rate` `momentum` parameters en afgestemd. Het vroegtijdig stoppen van runs wordt bepaald door een , waarmee een run wordt gestopt waarvan de primaire metrische gegevens buiten de vallen `BanditPolicy` `slack_factor` (zie [De klasseverwijzing BanditPolicy).](/python/api/azureml-train-core/azureml.train.hyperdrive.banditpolicy) 

De volgende code uit het voorbeeld laat zien hoe de afgestemde waarden worden ontvangen, geparseerd en doorgegeven aan de functie van het `fine_tune_model` trainingsscript:

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
> Bij elke hyperparameter wordt de training opnieuw gestart, inclusief het herbouwen van het model en alle _gegevensbelastingen._ U kunt deze kosten minimaliseren door een Azure Machine Learning pijplijn of handmatig proces te gebruiken om zoveel mogelijk gegevensvoorbereiding uit te laten gaan voordat u de training hebt uitgevoerd. 

## <a name="submit-hyperparameter-tuning-experiment"></a>Hyperparameter-afstemmingsexperiment verzenden

Nadat u de configuratie voor het afstemmen van de hyperparameter hebt bepaald, [dient u het experiment in:](/python/api/azureml-core/azureml.core.experiment%28class%29#submit-config--tags-none----kwargs-)

```Python
from azureml.core.experiment import Experiment
experiment = Experiment(workspace, experiment_name)
hyperdrive_run = experiment.submit(hd_config)
```

## <a name="warm-start-hyperparameter-tuning-optional"></a>Hyperparameter voor warm starten afstemmen (optioneel)

Het vinden van de beste hyperparameterwaarden voor uw model kan een iteratief proces zijn. U kunt de kennis uit de vijf vorige runs opnieuw gebruiken om de afstemming van hyperparameters te versnellen.

Warm starten wordt anders verwerkt, afhankelijk van de steekproefmethode:
- **Bayesiaanse steekproeven:** Steekproeven uit de vorige run worden gebruikt als voorkennis om nieuwe steekproeven te kiezen en de primaire metrische gegevens te verbeteren.
- **Willekeurige steekproeven** of **rastersampling:** Bij vroege beëindiging wordt gebruikgemaakt van kennis van eerdere uitvoeringen om slecht presterende uitvoeringen te bepalen. 

Geef de lijst op met bovenliggende runs waar u de start wilt warmen.

```Python
from azureml.train.hyperdrive import HyperDriveRun

warmstart_parent_1 = HyperDriveRun(experiment, "warmstart_parent_run_ID_1")
warmstart_parent_2 = HyperDriveRun(experiment, "warmstart_parent_run_ID_2")
warmstart_parents_to_resume_from = [warmstart_parent_1, warmstart_parent_2]
```

Als een experiment voor het afstemmen van hyperparameters wordt geannuleerd, kunt u trainings runs hervatten vanaf het laatste controlepunt. Uw trainingsscript moet echter controlepuntlogica verwerken.

De trainingsuitvoer moet dezelfde hyperparameterconfiguratie gebruiken en de uitvoermappen hebben bevestigd. Het trainingsscript moet het argument accepteren, dat het controlepunt of modelbestanden bevat van waaruit de training `resume-from` wordt hervat. U kunt afzonderlijke trainings runs hervatten met behulp van het volgende codefragment:

```Python
from azureml.core.run import Run

resume_child_run_1 = Run(experiment, "resume_child_run_ID_1")
resume_child_run_2 = Run(experiment, "resume_child_run_ID_2")
child_runs_to_resume = [resume_child_run_1, resume_child_run_2]
```

U kunt uw experiment voor het afstemmen van hyperparameters configureren om de start van een eerder experiment op te warmen of afzonderlijke trainings runs te hervatten met behulp van de optionele parameters `resume_from` `resume_child_runs` en in de configuratie:

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

## <a name="visualize-hyperparameter-tuning-runs"></a>Afstemmings runs van hyperparameters visualiseren

U kunt de afstemmings runs van uw hyperparameters visualiseren in Azure Machine Learning-studio of u kunt een notebookwidget gebruiken.

### <a name="studio"></a>Studio

U kunt al uw hyperparameterafstemmings runs visualiseren in [de Azure Machine Learning-studio](https://ml.azure.com). Zie Records uitvoeren weergeven in de studio voor meer informatie over het weergeven van [een experiment in de portal.](how-to-log-view-metrics.md#view-the-experiment-in-the-web-portal)

- **Grafiek met metrische** gegevens: met deze visualisatie worden de metrische gegevens bijgehouden die zijn vastgelegd voor elke onderliggende hyperdrive-run gedurende de afstemming van hyperparameters. Elke regel vertegenwoordigt een onderliggende run en elk punt meet de primaire metrische waarde bij die iteratie van runtime.  

    :::image type="content" source="media/how-to-tune-hyperparameters/hyperparameter-tuning-metrics.png" alt-text="Grafiek met metrische gegevens over het afstemmen van hyperparameters":::

- **Diagram met parallelle coördinaten:** deze visualisatie toont de correlatie tussen de prestaties van de primaire metrische gegevens en afzonderlijke hyperparameterwaarden. De grafiek is interactief via de verplaatsing van assen (klik en sleep door het aslabel) en door waarden op één as te markeren (klik en sleep verticaal langs één as om een bereik van gewenste waarden te markeren).

    :::image type="content" source="media/how-to-tune-hyperparameters/hyperparameter-tuning-parallel-coordinates.png" alt-text="Diagram met afstemming van parallelle coördinaten van hyperparameter":::

- **2dimensionale spreidingsdiagram:** deze visualisatie toont de correlatie tussen twee afzonderlijke hyperparameters, samen met de bijbehorende primaire metrische waarde.

    :::image type="content" source="media/how-to-tune-hyperparameters/hyperparameter-tuning-2-dimensional-scatter.png" alt-text="Hyparameterafstemming 2-dimensionale spreidingsdiagram":::

- **3-dimensionale** spreidingsdiagram: deze visualisatie is hetzelfde als 2D, maar maakt drie hyperparameterdimensaties van correlatie met de primaire metrische waarde mogelijk. U kunt ook klikken en slepen om de grafiek te heroriënteren om verschillende correlaties in 3D-ruimte weer te geven.

    :::image type="content" source="media/how-to-tune-hyperparameters/hyperparameter-tuning-3-dimensional-scatter.png" alt-text="Hyparameterafstemming 3-dimensionale spreidingsdiagram":::

### <a name="notebook-widget"></a>Notebook-widget

Gebruik de [widget Notebook om](/python/api/azureml-widgets/azureml.widgets.rundetails) de voortgang van uw trainings runs te visualiseren. In het volgende codefragment worden al uw hyperparameterafstemmingsuit runs op één plek in een Jupyter-notebook gevisualiseerd:

```Python
from azureml.widgets import RunDetails
RunDetails(hyperdrive_run).show()
```

Met deze code wordt een tabel weergegeven met details over de trainings runs voor elk van de hyperparameterconfiguraties.

:::image type="content" source="media/how-to-tune-hyperparameters/hyperparameter-tuning-table.png" alt-text="Hyperparameter-afstemmingstabel":::

U kunt ook de prestaties van elk van de uitvoeringen visualiseren terwijl de training wordt uitgevoerd.

## <a name="find-the-best-model"></a>Het beste model zoeken

Nadat alle uitvoeringen van hyperparameter-afstemming zijn voltooid, identificeert u de best presterende configuratie- en hyperparameterwaarden:

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

Raadpleeg train-hyperparameter-*-notebooks in deze map:
* [how-to-use-azureml/ml-frameworks](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Volgende stappen
* [Een experiment bijhouden](how-to-log-view-metrics.md)
* [Een getraind model implementeren](how-to-deploy-and-where.md)
