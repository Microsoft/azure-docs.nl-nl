---
title: Parametrisatie met geautomatiseerde machine learning
titleSuffix: Azure Machine Learning
description: Meer informatie over de instellingen voor gegevens parametrisatie in Azure Machine Learning en hoe u deze functies kunt aanpassen voor uw geautomatiseerde ML experimenten.
author: nibaccam
ms.author: nibaccam
ms.reviewer: nibaccam
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to,automl,contperf-fy21q2
ms.date: 12/18/2020
ms.openlocfilehash: 5fcb57d1ef909d7c15e21b34c3f584c6615a6a44
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/12/2021
ms.locfileid: "98134412"
---
# <a name="data-featurization-in-automated-machine-learning"></a>Gegevens parametrisatie in geautomatiseerde machine learning



Meer informatie over de instellingen voor gegevens parametrisatie in Azure Machine Learning en hoe u deze functies kunt aanpassen voor [automatische ml experimenten](concept-automated-ml.md).

## <a name="feature-engineering-and-featurization"></a>Feature engineering en parametrisatie

*Functie techniek* is het proces van het gebruik van domein kennis van de gegevens voor het maken van functies die machine learning (ml) algoritmen voor meer informatie. In Azure Machine Learning worden technieken voor het schalen van gegevens en normalisatie toegepast om functie techniek gemakkelijker te maken. Deze technieken en deze functie techniek worden gezamenlijk *parametrisatie* genoemd in geautomatiseerde machine learning, of *autoML*, experimenten.

## <a name="prerequisites"></a>Vereisten

In dit artikel wordt ervan uitgegaan dat u al weet hoe u een AutoML-experiment kunt configureren. Raadpleeg de volgende artikelen voor meer informatie over configuratie:

- Voor een code-eerste ervaring: [Configureer geautomatiseerde ml experimenten met behulp van de Azure machine learning SDK voor python](how-to-configure-auto-train.md).
- Voor een code ring met weinig code of zonder codes: [u kunt geautomatiseerde machine learning modellen maken, controleren en implementeren met behulp van de Azure machine learning Studio](how-to-use-automated-ml-for-ml-models.md).

## <a name="configure-featurization"></a>Parametrisatie configureren

In elk automatisch machine learning experiment worden standaard [technieken voor automatisch schalen en normaliseren](#featurization) toegepast op uw gegevens. Deze technieken zijn soorten parametrisatie die *bepaalde* algoritmen helpen die gevoelig zijn voor functies op verschillende schalen. U kunt meer parametrisatie inschakelen, zoals *gegevens over ontbrekende waarden*, *code ring* en *trans formaties*.

> [!NOTE]
> Stappen voor automatische machine learning parametrisatie (zoals functie normalisatie, het verwerken van ontbrekende gegevens of het converteren van tekst naar numerieke) worden onderdeel van het onderliggende model. Wanneer u het model gebruikt voor voor spellingen, worden dezelfde parametrisatie-stappen die tijdens de training worden toegepast, automatisch toegepast op de invoer gegevens.

Voor experimenten die u configureert met de python-SDK, kunt u de instelling parametrisatie in-of uitschakelen en de parametrisatie-stappen die u voor uw experiment wilt gebruiken verder opgeven. Als u de Azure Machine Learning Studio gebruikt, raadpleegt u de [stappen om parametrisatie in te scha kelen](how-to-use-automated-ml-for-ml-models.md#customize-featurization).

De volgende tabel bevat de geaccepteerde instellingen voor `featurization` in de [AutoMLConfig-klasse](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig):

|Parametrisatie-configuratie | Beschrijving|
------------- | ------------- |
|`"featurization": 'auto'`| Hiermee geeft u op dat, als onderdeel van preverwerking, de [stappen](#featurization) voor [gegevens Guardrails](#data-guardrails) en parametrisatie automatisch moeten worden uitgevoerd. Dit is de standaardinstelling.|
|`"featurization": 'off'`| Hiermee geeft u op dat parametrisatie stappen niet automatisch moeten worden uitgevoerd.|
|`"featurization":`&nbsp;`'FeaturizationConfig'`| Hiermee geeft u op dat aangepaste parametrisatie-stappen moeten worden gebruikt. [Meer informatie over het aanpassen van parametrisatie](#customize-featurization).|

<a name="featurization"></a>

## <a name="automatic-featurization"></a>Automatische featurization

De volgende tabel bevat een overzicht van de technieken die automatisch worden toegepast op uw gegevens. Deze technieken worden toegepast op experimenten die zijn geconfigureerd met behulp van de SDK of Studio. Als u dit gedrag wilt uitschakelen, stelt u `"featurization": 'off'` in uw `AutoMLConfig` object in.

> [!NOTE]
> Als u van plan bent om uw door AutoML gemaakte modellen te exporteren naar een [ONNX-model](concept-onnx.md), worden alleen de parametrisatie-opties aangegeven met een asterisk (*) ondersteund in de ONNX-indeling. Meer informatie over [het converteren van modellen naar ONNX](concept-automated-ml.md#use-with-onnx).

|Parametrisatie- &nbsp; stappen| Beschrijving |
| ------------- | ------------- |
|**Hoge kardinaliteit of geen variantie-functies verwijderen** _ |Verwijder deze functies uit de trainings-en validatie sets. Is van toepassing op functies waarbij alle waarden ontbreken, met dezelfde waarde in alle rijen of met een hoge kardinaliteit (bijvoorbeeld hashes, Id's of GUID'S).|
|_*Ontbrekende waarden toegerekend**_ |Voor numerieke functies toegerekend met het gemiddelde van de waarden in de kolom.<br/><br/>Voor categorische-functies toegerekend met de meest frequente waarde.|
|_*Meer functies genereren**_ |Voor DateTime-functies: jaar, maand, dag, dag van de week, dag van jaar, kwar taal, week van het jaar, uur, minuut, seconde.<br><br> _For prognose taken, * deze extra DateTime-functies worden gemaakt: ISO-jaar, half-halfjaar, kalender maand als teken reeks, week, dag van de week als teken reeks, dag van kwar taal, dag van jaar, AM/PM (0 als het uur vóór 12:00 uur (12 uur), 1 anders), AM/PM als teken reeks, uur van dag<br/><br/>Voor tekst functies: term frequentie op basis van unigrams, bigrams en trigrams. Meer informatie over [hoe dit wordt gedaan met Bert.](#bert-integration)|
|**Transformeren en versleutelen** _|Numerieke functies met weinig unieke waarden transformeren in categorische-functies.<br/><br/>Code ring met één Hot-categorische wordt gebruikt voor functies met weinig kardinaliteit. Een hot-hash-code ring wordt gebruikt voor categorische-functies met een hoge kardinaliteit.|
|_ *Woord insluitingen**|Met een tekst-featurizer worden vectoren van tekst tokens geconverteerd naar zinnen vectoren met behulp van een vooraf getraind model. De insluitings vector van elk woord in een document wordt samengevoegd met de rest om een document functie Vector te maken.|
|**Cluster afstand**|Treinen a k: cluster model op alle numerieke kolommen. Produceert *k* nieuwe functies (één nieuwe numerieke functie per cluster) die de afstand van elk voor beeld naar de massa middelpunt van elk cluster bevatten.|

## <a name="data-guardrails"></a>Gegevens Guardrails

*Data Guardrails* helpt u bij het identificeren van mogelijke problemen met uw gegevens (bijvoorbeeld ontbrekende waarden of [onevenwicht in klasse](concept-manage-ml-pitfalls.md#identify-models-with-imbalanced-data)). Ze helpen u ook om corrigerende maat regelen te nemen voor betere resultaten.

De gegevens Guardrails worden toegepast:

- **Voor SDK-experimenten**: wanneer de para meters `"featurization": 'auto'` of `validation=auto` in uw object zijn opgegeven `AutoMLConfig` .
- **Voor Studio experimenten**: wanneer automatische parametrisatie is ingeschakeld.

U kunt de gegevens Guardrails voor uw experiment bekijken:

- Door in te stellen `show_output=True` Wanneer u een experiment verzendt met behulp van de SDK.

- In de Studio, op het tabblad **gegevens Guardrails** van uw automatische ml run.

### <a name="data-guardrail-states"></a>Status gegevens guardrail

Gegevens Guardrails worden weer gegeven in een van de volgende drie statussen:

|Staat| Beschrijving |
|----|---- |
|**Buffer**| Er zijn geen gegevens problemen gedetecteerd en er is geen actie vereist voor u. |
|**Gereed**| Er zijn wijzigingen toegepast op uw gegevens. We raden u aan de corrigerende maat regelen te controleren die AutoML hebben genomen, om ervoor te zorgen dat de wijzigingen worden uitgelijnd met de verwachte resultaten. |
|**Gewaarschuwd**| Er is een gegevens probleem gedetecteerd, maar dit kan niet worden opgelost. We raden u aan het probleem te herzien en op te lossen.|

### <a name="supported-data-guardrails"></a>Guardrails voor ondersteunde gegevens

In de volgende tabel worden de gegevens Guardrails beschreven die momenteel worden ondersteund en de bijbehorende statussen die u kunt zien wanneer u uw experiment verzendt:

Guardrail|Status|Voor waarde &nbsp; voor &nbsp; trigger
---|---|---
**Ontbrekende functie waarden toerekening** |Buffer <br><br><br> Gereed| Er zijn geen ontbrekende onderdeel waarden gedetecteerd in uw trainings gegevens. Meer informatie over de toerekening van [ontbrekende waarden.](./how-to-use-automated-ml-for-ml-models.md#customize-featurization) <br><br> Er zijn ontbrekende functie waarden gedetecteerd in uw trainings gegevens en deze zijn toegerekend.
**Functie verwerking met hoge kardinaliteit** |Buffer <br><br><br> Gereed| Uw invoer is geanalyseerd en er zijn geen functies met een hoge kardinaliteit gedetecteerd. <br><br> Er zijn functies met een hoge kardinaliteit gedetecteerd in uw invoer en zijn afgehandeld.
**Verwerking van splitsing van validatie** |Gereed| De validatie configuratie is ingesteld op `'auto'` en de trainings gegevens bevatten *minder dan 20.000 rijen*. <br> Elke iteratie van het getrainde model is gevalideerd door Kruis validatie te gebruiken. Meer informatie over [validatie gegevens](./how-to-configure-auto-train.md#training-validation-and-test-data). <br><br> De validatie configuratie is ingesteld op `'auto'` en de trainings gegevens bevatten *meer dan 20.000 rijen*. <br> De invoer gegevens zijn gesplitst in een trainings gegevensset en een validatie gegevensset voor validatie van het model.
**Detectie van klasse-verdeling** |Buffer <br><br><br><br>Gewaarschuwd <br><br><br>Gereed | Uw invoer is geanalyseerd en alle klassen zijn evenwichtig in uw trainings gegevens. Een gegevensset wordt beschouwd als evenwichtig als elke klasse een goede representatie heeft in de gegevensset, gemeten op basis van het aantal en de verhouding van steek proeven. <br><br> Er zijn niet-sluitende klassen gedetecteerd in uw invoer. Los het probleem met de oplossing op om model afwijking te herstellen. Meer informatie over [gegevens](./concept-manage-ml-pitfalls.md#identify-models-with-imbalanced-data)die niet in balans zijn.<br><br> Er zijn niet-sluitende klassen gedetecteerd in uw invoer en de logica voor het opruimen van de gegevens is bepaald voor het Toep assen van Balancing.
**Detectie van geheugen problemen** |Buffer <br><br><br><br> Gereed |<br> De geselecteerde waarden (horizon, vertraging, doorlopend venster) zijn geanalyseerd en er zijn geen mogelijke problemen met de geheugen detectie gedetecteerd. Meer informatie over [prognose configuraties](./how-to-auto-train-forecast.md#configuration-settings)voor time series. <br><br><br>De geselecteerde waarden (horizon, vertraging, doorlopend venster) zijn geanalyseerd en kunnen ervoor zorgen dat het experiment te weinig geheugen heeft. De vertragings-of venster configuraties zijn uitgeschakeld.
**Frequentie detectie** |Buffer <br><br><br><br> Gereed |<br> De tijd reeks is geanalyseerd en alle gegevens punten zijn afgestemd op de gedetecteerde frequentie. <br> <br> De tijd reeks is geanalyseerd en gegevens punten die niet zijn uitgelijnd met de gedetecteerde frequentie, zijn gedetecteerd. Deze gegevens punten zijn verwijderd uit de gegevensset. Meer informatie over het [voorbereiden van gegevens voor time series-prognoses](./how-to-auto-train-forecast.md#preparing-data).

## <a name="customize-featurization"></a>Parametrisatie aanpassen

U kunt uw parametrisatie-instellingen aanpassen om ervoor te zorgen dat de gegevens en functies die worden gebruikt om uw ML-model te trainen, in relevante voor spellingen resulteren.

Geef in uw object op om featurizations aan te passen `"featurization": FeaturizationConfig` `AutoMLConfig` . Als u de Azure Machine Learning Studio gebruikt voor uw experiment, raadpleegt u het [artikel](how-to-use-automated-ml-for-ml-models.md#customize-featurization). Als u parametrisatie voor prognoses taak typen wilt aanpassen, raadpleegt u de voor [spellingen](how-to-auto-train-forecast.md#customize-featurization).

Ondersteunde aanpassingen zijn onder andere:

|Aanpassing|Definitie|
|--|--|
|**Update van het kolom doel**|Overschrijf het automatisch gedetecteerde functie type voor de opgegeven kolom.|
|**Para meter bijwerken van trans formatie** |De para meters voor de opgegeven transformator bijwerken. *Biedt momenteel* ondersteuning voor toerekening (gemiddelde, meest frequente en gemiddelde) en *HashOneHotEncoder*.|
|**Kolommen verwijderen** |Hiermee geeft u kolommen op die moeten worden featurized.|
|**Trans formaties blok keren**| Hiermee geeft u de Block-trans formaties op die moeten worden gebruikt in het parametrisatie-proces.|

>[!NOTE]
> De functionaliteit **Drop columns** is afgeschaft vanaf SDK-versie 1,19. Verwijder kolommen uit uw gegevensset als onderdeel van het opschonen van gegevens, voordat u deze in uw geautomatiseerde ML experiment gebruikt. 

Het object maken met `FeaturizationConfig` API-aanroepen:

```python
featurization_config = FeaturizationConfig()
featurization_config.blocked_transformers = ['LabelEncoder']
featurization_config.drop_columns = ['aspiration', 'stroke']
featurization_config.add_column_purpose('engine-size', 'Numeric')
featurization_config.add_column_purpose('body-style', 'CategoricalHash')
#default strategy mean, add transformer param for for 3 columns
featurization_config.add_transformer_params('Imputer', ['engine-size'], {"strategy": "median"})
featurization_config.add_transformer_params('Imputer', ['city-mpg'], {"strategy": "median"})
featurization_config.add_transformer_params('Imputer', ['bore'], {"strategy": "most_frequent"})
featurization_config.add_transformer_params('HashOneHotEncoder', [], {"number_of_bits": 3})
```

## <a name="featurization-transparency"></a>Parametrisatie transparantie

Voor elk AutoML-model is parametrisatie automatisch toegepast.  Parametrisatie omvat geautomatiseerde functie techniek (wanneer `"featurization": 'auto'` ) en schalen en normalisatie, die vervolgens van invloed zijn op het geselecteerde algoritme en de bijbehorende afstemming-waarden. AutoML ondersteunt verschillende methoden om ervoor te zorgen dat u inzicht hebt in wat is toegepast op uw model.

Bekijk dit voor beeld van een prognose:

+ Er zijn vier invoer functies: A (numeriek), B (numeriek), C (numeriek), D (DateTime).
+ De numerieke functie C wordt verwijderd omdat deze een ID-kolom met alle unieke waarden is.
+ De numerieke functies A en B bevatten ontbrekende waarden en worden daarom toegerekend aan het gemiddelde.
+ De datum/tijd-functie D is featurized in 11 verschillende ontworpen functies.

Als u deze informatie wilt ophalen, gebruikt u de `fitted_model` uitvoer van uw automatische ml-experiment run.

```python
automl_config = AutoMLConfig(…)
automl_run = experiment.submit(automl_config …)
best_run, fitted_model = automl_run.get_output()
```
### <a name="automated-feature-engineering"></a>Geautomatiseerde functie techniek 
Het `get_engineered_feature_names()` retourneert een lijst met de namen van de functies van de functie.

  >[!Note]
  >Gebruik ' timeseriestransformer ' voor taak = ' prognose ', Else gebruik ' datatransformer ' voor de taak ' regressie ' of ' classificatie '.

  ```python
  fitted_model.named_steps['timeseriestransformer']. get_engineered_feature_names ()
  ```

Deze lijst bevat alle functie namen van technici. 

  ```
  ['A', 'B', 'A_WASNULL', 'B_WASNULL', 'year', 'half', 'quarter', 'month', 'day', 'hour', 'am_pm', 'hour12', 'wday', 'qday', 'week']
  ```

`get_featurization_summary()`Hiermee wordt een parametrisatie-samen vatting van alle invoer functies opgehaald.

  ```python
  fitted_model.named_steps['timeseriestransformer'].get_featurization_summary()
  ```

Uitvoer

  ```
  [{'RawFeatureName': 'A',
    'TypeDetected': 'Numeric',
    'Dropped': 'No',
    'EngineeredFeatureCount': 2,
    'Tranformations': ['MeanImputer', 'ImputationMarker']},
   {'RawFeatureName': 'B',
    'TypeDetected': 'Numeric',
    'Dropped': 'No',
    'EngineeredFeatureCount': 2,
    'Tranformations': ['MeanImputer', 'ImputationMarker']},
   {'RawFeatureName': 'C',
    'TypeDetected': 'Numeric',
    'Dropped': 'Yes',
    'EngineeredFeatureCount': 0,
    'Tranformations': []},
   {'RawFeatureName': 'D',
    'TypeDetected': 'DateTime',
    'Dropped': 'No',
    'EngineeredFeatureCount': 11,
    'Tranformations': ['DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime']}]
  ```

   |Uitvoer|Definitie|
   |----|--------|
   |RawFeatureName|Invoer functie/kolom naam van de opgegeven gegevensset.|
   |TypeDetected|Het gegevens type van de invoer functie is gedetecteerd.|
   |Minder|Hiermee wordt aangegeven of de invoer functie is verwijderd of gebruikt.|
   |EngineeringFeatureCount|Aantal functies dat wordt gegenereerd via geautomatiseerde functie technische trans formaties.|
   |Transformaties|Lijst met trans formaties die zijn toegepast op de invoer functies voor het genereren van ontworpen functies.|

### <a name="scaling-and-normalization"></a>Schalen en normalisatie

Gebruik om inzicht te krijgen in de schaal/normalisatie en het geselecteerde algoritme met de afstemming-waarden `fitted_model.steps` . 

De volgende voorbeeld uitvoer wordt uitgevoerd `fitted_model.steps` voor een gekozen uitvoering:

```
[('RobustScaler', 
  RobustScaler(copy=True, 
  quantile_range=[10, 90], 
  with_centering=True, 
  with_scaling=True)), 

  ('LogisticRegression', 
  LogisticRegression(C=0.18420699693267145, class_weight='balanced', 
  dual=False, 
  fit_intercept=True, 
  intercept_scaling=1, 
  max_iter=100, 
  multi_class='multinomial', 
  n_jobs=1, penalty='l2', 
  random_state=None, 
  solver='newton-cg', 
  tol=0.0001, 
  verbose=0, 
  warm_start=False))
```

Voor meer informatie gebruikt u deze hulp functie: 

```python
from pprint import pprint

def print_model(model, prefix=""):
    for step in model.steps:
        print(prefix + step[0])
        if hasattr(step[1], 'estimators') and hasattr(step[1], 'weights'):
            pprint({'estimators': list(
                e[0] for e in step[1].estimators), 'weights': step[1].weights})
            print()
            for estimator in step[1].estimators:
                print_model(estimator[1], estimator[0] + ' - ')
        else:
            pprint(step[1].get_params())
            print()

print_model(model)
```

Deze helper-functie retourneert de volgende uitvoer voor een bepaalde uitvoering met `LogisticRegression with RobustScalar` als het specifieke algoritme.

```
RobustScaler
{'copy': True,
'quantile_range': [10, 90],
'with_centering': True,
'with_scaling': True}

LogisticRegression
{'C': 0.18420699693267145,
'class_weight': 'balanced',
'dual': False,
'fit_intercept': True,
'intercept_scaling': 1,
'max_iter': 100,
'multi_class': 'multinomial',
'n_jobs': 1,
'penalty': 'l2',
'random_state': None,
'solver': 'newton-cg',
'tol': 0.0001,
'verbose': 0,
'warm_start': False}
```

### <a name="predict-class-probability"></a>Klasse waarschijnlijkheid voors pellen

Modellen die zijn gemaakt met behulp van automatische ML, bevatten alle wrapper-objecten die de functionaliteit van de open-source klasse Origin spie gelen. De meeste wrapper-objecten die worden geretourneerd door automatische MILLILITERs implementeren de `predict_proba()` functie, die een matrix-achtige of Sparse matrix gegevens voorbeeld van uw functies (X-waarden) accepteert en een n-dimensionale matrix van elk voor beeld en de bijbehorende klasse-kans retourneert.

Als u het beste uitvoeren en het model hebt opgehaald met behulp van de bovenstaande oproepen, kunt u `predict_proba()` rechtstreeks vanuit het ingebouwde model aanroepen, waarbij u een `X_test` voor beeld in de juiste indeling levert, afhankelijk van het model type.

```python
best_run, fitted_model = automl_run.get_output()
class_prob = fitted_model.predict_proba(X_test)
```

Als het onderliggende model de functie niet ondersteunt `predict_proba()` of als de indeling onjuist is, wordt een model klasse-specifieke uitzonde ring gegenereerd. Zie de documentatie voor [RandomForestClassifier](https://scikit-learn.org/stable/modules/generated/sklearn.ensemble.RandomForestClassifier.html#sklearn.ensemble.RandomForestClassifier.predict_proba) en [XGBoost](https://xgboost.readthedocs.io/en/latest/python/python_api.html) voor voor beelden van hoe deze functie voor verschillende model typen wordt geïmplementeerd.

<a name="bert-integration"></a>

## <a name="bert-integration-in-automated-ml"></a>BERT-integratie in automatische ML

[Bert](https://techcommunity.microsoft.com/t5/azure-ai/how-bert-is-integrated-into-azure-automated-machine-learning/ba-p/1194657) wordt gebruikt in de parametrisatie-laag van AutoML. Als een kolom in deze laag vrije tekst of andere typen gegevens, zoals tijds tempels of eenvoudige getallen bevat, wordt parametrisatie dienovereenkomstig toegepast.

Voor BERT is het model goed afgestemd en getraind waarbij gebruik wordt gemaakt van de door de gebruiker geleverde labels. Hier worden document insluitingen uitgevoerd als functies naast andere, zoals op Time Stamp gebaseerde functies, dag van de week. 


### <a name="steps-to-invoke-bert"></a>Stappen voor het aanroepen van BERT

Om BERT aan te roepen, moet u  `enable_dnn: True` in uw automl_settings instellen en een GPU-Compute ( `vm_size = "STANDARD_NC6"` of een hogere GPU) gebruiken. Als er een CPU-berekening wordt gebruikt, wordt in plaats van BERT, AutoML de BiLSTM DNN-featurizer ingeschakeld.

AutoML voert de volgende stappen uit voor BERT. 

1. **Voor verwerking en tokening van alle tekst kolommen**. De transformator ' StringCast ' kan bijvoorbeeld worden gevonden in de parametrisatie-samen vatting van het uiteindelijke model. Een voor beeld van het maken van een samen vatting van het model parametrisatie vindt u in [Dit notitie blok](https://towardsdatascience.com/automated-text-classification-using-machine-learning-3df4f4f9570b).

2. **Alle tekst kolommen samen voegen tot één tekst kolom**, dus de `StringConcatTransformer` in het laatste model. 

    Onze implementatie van BERT beperkt de totale tekst lengte van een trainings voorbeeld tot 128-tokens. Dit betekent dat alle tekst kolommen bij het samen voegen de meeste 128-tokens lang moeten zijn. Als er meerdere kolommen aanwezig zijn, moet elke kolom worden verwijderd zodat aan deze voor waarde wordt voldaan. Als dat niet het geval is, wordt voor de Tokenizer laag van BERT-tokens deze invoer met 128-tokens afgekapt voor samengevoegde kolommen met een lengte >128.

3. **Als onderdeel van het opruimen van functies vergelijkt AutoML BERT met de basis lijn (verzameling woorden) met een voor beeld van de gegevens.** Met deze vergelijking wordt bepaald of BERT nauw keurigere verbeteringen opleveren. Als BERT beter presteert dan de basis lijn, gebruikt AutoML vervolgens BERT voor Text parametrisatie voor de hele gegevens. In dat geval wordt de `PretrainedTextDNNTransformer` in het laatste model weer geven.

BERT wordt doorgaans langer uitgevoerd dan andere featurizers. Voor betere prestaties raden we u aan om ' STANDARD_NC24r ' of ' STANDARD_NC24rs_V3 ' te gebruiken voor hun RDMA-mogelijkheden. 

AutoML distribueert BERT-training over meerdere knoop punten als deze beschikbaar zijn (Maxi maal acht knoop punten). Dit kan in uw object worden uitgevoerd `AutoMLConfig` door de `max_concurrent_iterations` para meter in te stellen op hoger dan 1. 

## <a name="supported-languages-for-bert-in-automl"></a>Ondersteunde talen voor BERT in autoML 

AutoML ondersteunt momenteel ongeveer 100 talen en afhankelijk van de taal van de gegevensset, kiest autoML het juiste BERT-model. Voor Duitse gegevens gebruiken we het Duitse BERT-model. Voor het Engels gebruiken we het Engelse BERT-model. Voor alle andere talen gebruiken we het Multilingual BERT-model.

In de volgende code wordt het Duitse BERT-model geactiveerd, omdat de taal van de gegevensset is opgegeven in `deu` , de taal code van drie letters voor Duits volgens de [ISO-classificatie](https://iso639-3.sil.org/code/deu):

```python
from azureml.automl.core.featurization import FeaturizationConfig

featurization_config = FeaturizationConfig(dataset_language='deu')

automl_settings = {
    "experiment_timeout_minutes": 120,
    "primary_metric": 'accuracy', 
# All other settings you want to use 
    "featurization": featurization_config,
    
  "enable_dnn": True, # This enables BERT DNN featurizer
    "enable_voting_ensemble": False,
    "enable_stack_ensemble": False
}
```

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het instellen van uw geautomatiseerde ML experimenten:

    * Voor een code-eerste ervaring: [Configureer geautomatiseerde ml experimenten met behulp van de Azure machine learning SDK](how-to-configure-auto-train.md).
    * Voor een code ring met weinig code of voor [uw eigen ervaring: Maak uw automatische ml-experimenten in de Azure machine learning Studio](how-to-use-automated-ml-for-ml-models.md).

* Meer informatie over [hoe en waar een model moet worden geïmplementeerd](how-to-deploy-and-where.md).

* Meer informatie over [het trainen van een regressie model met behulp van geautomatiseerde machine learning](tutorial-auto-train-models.md) of [hoe u kunt trainen met behulp van geautomatiseerde machine learning op een externe bron](how-to-auto-train-remote.md).
