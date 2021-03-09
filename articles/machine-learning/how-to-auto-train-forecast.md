---
title: Automatisch een time-series-prognose model trainen
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van Azure Machine Learning voor het trainen van een regressie model voor een time-series met geautomatiseerde machine learning.
services: machine-learning
author: nibaccam
ms.author: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, contperf-fy21q1, automl
ms.date: 08/20/2020
ms.openlocfilehash: 14837391f7bf907acbbe1d573f3171acef4db658
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102503501"
---
# <a name="auto-train-a-time-series-forecast-model"></a>Automatisch een time-series-prognose model trainen


In dit artikel vindt u informatie over het configureren en trainen van een regressie model voor het maken van een time-reeks met behulp van geautomatiseerde machine learning, AutoML, in de [Azure machine learning python-SDK](/python/api/overview/azure/ml/). 

Dit doet u als volgt: 

> [!div class="checklist"]
> * Gegevens voorbereiden voor time series-model lering.
> * Specifieke time series-para meters in een [`AutoMLConfig`](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) object configureren.
> * Voor spellingen uitvoeren met gegevens van de tijd reeks.

Voor een lage code kunt u de [zelf studie: prognose vraag met geautomatiseerde machine learning](tutorial-automated-ml-forecast.md) voor een time-series-prognose voor beeld met behulp van geautomatiseerde machine learning in [Azure machine learning Studio](https://ml.azure.com/).

In tegens telling tot de methoden van de klassieke tijd reeks, in automatische MILLILITERs, worden waarden in het verleden ' gedraaid ' om extra dimensies voor de regressor hierop samen met andere voor spellingen. Deze aanpak omvat meerdere contextuele variabelen en hun relatie met elkaar tijdens de training. Omdat meerdere factoren van invloed kunnen zijn op een prognose, wordt deze methode goed afgestemd op de scenario's met de praktijk. Wanneer u bijvoorbeeld de verkoop, interacties van historische trends, wissel koersen en prijs verlangt, wordt het resultaat van de verkoop gezamenlijk gestimuleerd. 

## <a name="prerequisites"></a>Vereisten

Voor dit artikel hebt u het volgende nodig: 

* Een Azure Machine Learning-werkruimte. Zie [een Azure machine learning-werk ruimte maken](how-to-manage-workspace.md)voor het maken van de werk ruimte.

* In dit artikel wordt ervan uitgegaan dat u bekend bent met het instellen van een geautomatiseerd machine learning experiment. Volg de [zelf studie](tutorial-auto-train-models.md) of Lees [hoe](how-to-configure-auto-train.md) u de belangrijkste ontwerp patronen voor automatische machine learning experimenten kunt zien.

## <a name="preparing-data"></a> Gegevens voorbereiden

Het belangrijkste verschil tussen het taak type van een prognose van een voor spelling en een regressie taak in AutoML is het opnemen van een functie in uw gegevens die een geldige tijd reeks vertegenwoordigt. Een reguliere tijd reeks heeft een duidelijk gedefinieerde en consistente frequentie en heeft op elk steekproef punt een waarde in een doorlopende periode. 

Bekijk de volgende moment opname van een bestand `sample.csv` .
Deze gegevensset is van dagelijkse verkoop gegevens voor een bedrijf met twee verschillende winkels, A en B. 

Daarnaast zijn er functies voor

 *  `week_of_year`: Hiermee staat u toe dat het model de wekelijkse seizoensgebondenheid detecteert.
* `day_datetime`: Hiermee wordt een schone time-reeks met een dagelijkse frequentie aangeduid.
* `sales_quantity`: de doel kolom voor het uitvoeren van voor spellingen. 

```output
day_datetime,store,sales_quantity,week_of_year
9/3/2018,A,2000,36
9/3/2018,B,600,36
9/4/2018,A,2300,36
9/4/2018,B,550,36
9/5/2018,A,2100,36
9/5/2018,B,650,36
9/6/2018,A,2400,36
9/6/2018,B,700,36
9/7/2018,A,2450,36
9/7/2018,B,650,36
```


Lees de gegevens in een Panda data frame en gebruik vervolgens de `to_datetime` functie om ervoor te zorgen dat de tijd reeks een `datetime` type is.

```python
import pandas as pd
data = pd.read_csv("sample.csv")
data["day_datetime"] = pd.to_datetime(data["day_datetime"])
```

In dit geval worden de gegevens al oplopend gesorteerd op basis van het veld tijd `day_datetime` . Wanneer u echter een experiment instelt, moet u ervoor zorgen dat de gewenste tijd kolom in oplopende volg orde wordt gesorteerd om een geldige tijd reeks te maken. 

De volgende code, 
* Gaat ervan uit dat de gegevens 1.000 records bevatten, en maakt een deterministische splitsing in de gegevens voor het maken van trainings-en test gegevens sets. 
* Hiermee wordt de kolom Label aangeduid als `sales_quantity` .
* Scheidt het label veld van `test_data` om de set te vormen `test_target` .

```python
train_data = data.iloc[:950]
test_data = data.iloc[-50:]

label =  "sales_quantity"
 
test_labels = test_data.pop(label).values
```

> [!IMPORTANT]
> Wanneer u een model wilt trainen voor het voors pellen van toekomstige waarden, kunt u ervoor zorgen dat alle functies die in de training worden gebruikt, kunnen worden gebruikt bij het uitvoeren van voor spellingen voor uw beoogde horizon. <br> <br>Wanneer u bijvoorbeeld een vraag prognose maakt, met inbegrip van een functie voor de huidige aandelen koers, kan de nauw keurigheid van de training enorm toenemen. Als u echter van plan bent om te forecasten met een lange horizon, is het wellicht niet mogelijk om toekomstige voorraad waarden te voors pellen die overeenkomen met toekomstige periode punten en de nauw keurigheid van het model kan lijden.

<a name="config"></a>

## <a name="training-and-validation-data"></a>Trainings-en validatie gegevens

U kunt afzonderlijke trein-en validatie sets rechtstreeks in het `AutoMLConfig` object opgeven.   Meer informatie over de [AutoMLConfig](#configure-experiment).

Voor time series-prognoses wordt alleen **ROCV (Rolling Origin Kruis validatie)** standaard voor validatie gebruikt. Geef de trainings-en validatie gegevens samen en stel het aantal Kruis validatie vouwen in met de `n_cross_validations` para meter in uw `AutoMLConfig` . ROCV verdeelt de reeks in trainings-en validatie gegevens met behulp van een tijd punt op oorsprong. Als u de oorspronkelijke tijd verschuift, worden de vouwen voor kruis validatie gegenereerd. Deze strategie behoudt de gegevens integriteit van de tijd reeks en elimineert het risico op lekkage van gegevens

![Kruis validatie tussen komst](./media/how-to-auto-train-forecast/ROCV.svg)

U kunt ook uw eigen validatie gegevens meenemen, meer informatie over het [configureren van gegevens splitsingen en kruis validatie in AutoML](how-to-configure-cross-validation-data-splits.md#provide-validation-data).


```python
automl_config = AutoMLConfig(task='forecasting',
                             n_cross_validations=3,
                             ...
                             **time_series_settings)
```

Meer informatie over hoe AutoML van toepassing is op Kruis validatie om te [voor komen dat modellen worden verouderd](concept-manage-ml-pitfalls.md#prevent-over-fitting).

## <a name="configure-experiment"></a>Experiment configureren

Het [`AutoMLConfig`](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) object definieert de instellingen en gegevens die nodig zijn voor een geautomatiseerde machine learning taak. De configuratie van een prognose model is vergelijkbaar met de instelling van een standaard regressie model, maar bepaalde modellen, configuratie opties en parametrisatie-stappen zijn specifiek voor tijdreeks gegevens. 

### <a name="supported-models"></a>Ondersteunde modellen
Automatische machine learning probeert automatisch verschillende modellen en algoritmen als onderdeel van het maken en afstemmen van het model. Als gebruiker hoeft u het algoritme niet op te geven. Voor het voors pellen van experimenten zijn zowel systeem eigen time-series als diepe leer modellen onderdeel van het aanbevelings systeem. De volgende tabel geeft een overzicht van deze subset van modellen. 

>[!Tip]
> Traditionele regressie modellen worden ook getest als onderdeel van het aanbevelings systeem voor het voors pellen van experimenten. Zie de [tabel ondersteunde](how-to-configure-auto-train.md#supported-models) modellen voor de volledige lijst met modellen. 

Modellen| Beschrijving | Voordelen
----|----|---
Prophet (preview-versie)|Prophet werkt het beste met een tijd reeks met krachtige seizoensgebonden effecten en verschillende seizoenen historische gegevens. Als u gebruik wilt maken van dit model, installeert u het lokaal met `pip install fbprophet` . | Nauw keurige & snelle, robuuste uitbijters, ontbrekende gegevens en dramatische wijzigingen in uw tijd reeks.
Automatische ARIMA (preview-versie)|Automatisch herlopend, geïntegreerd zwevend gemiddelde (ARIMA) wordt het beste uitgevoerd wanneer de gegevens stationair zijn. Dit betekent dat de statistische eigenschappen, zoals het gemiddelde en de variantie, constant zijn in de hele set. Als u bijvoorbeeld een munten spiegelt, is de kans dat u koppen krijgt, 50%, ongeacht of u vandaag, morgen of volgend jaar spiegelt.| Ideaal voor univariate-Series, aangezien de vorige waarden worden gebruikt om de toekomstige waarden te voors pellen.
ForecastTCN (preview-versie)| ForecastTCN is een Neural-netwerk model dat is ontworpen om de meest veeleisende prognose taken uit te voeren, waarbij niet-lineaire lokale en wereld wijde trends in uw gegevens worden vastgelegd, evenals relaties tussen tijd reeksen.|Kan gebruikmaken van complexe trends in uw gegevens en kan eenvoudig worden geschaald naar het grootste aantal gegevens sets.

### <a name="configuration-settings"></a>Configuratie-instellingen

Net als bij een regressie probleem definieert u de standaard opleidings parameters, zoals het taak type, het aantal iteraties, de trainings gegevens en het aantal Kruis validaties. Voor prognose taken zijn er aanvullende para meters die moeten worden ingesteld die van invloed zijn op het experiment. 

De volgende tabel bevat een overzicht van deze aanvullende para meters. Zie de [documentatie](/python/api/azureml-automl-core/azureml.automl.core.forecasting_parameters.forecastingparameters) van de ForecastingParameter-klasse voor syntaxis ontwerp patronen.

| Parameter &nbsp; naam | Beschrijving | Vereist |
|-------|-------|-------|
|`time_column_name`|Wordt gebruikt om de kolom datetime op te geven in de invoer gegevens die worden gebruikt voor het bouwen van de tijd reeks en het uitstellen van de frequentie.|✓|
|`forecast_horizon`|Hiermee definieert u hoeveel Peri Oden voorwaarts u wilt ramen. De horizon ligt in eenheden van de tijd reeks frequentie. Eenheden zijn gebaseerd op het tijds interval van uw trainings gegevens, bijvoorbeeld maandelijks, wekelijks dat de Forecaster moet voors pellen.|✓|
|`enable_dnn`|[DNNs voor het maken van prognoses]().||
|`time_series_id_column_names`|De kolom namen die worden gebruikt voor het uniek identificeren van de tijd reeks in gegevens die meerdere rijen met dezelfde tijds tempel hebben. Als er geen tijd reeks-id's zijn gedefinieerd, wordt ervan uitgegaan dat de gegevensset één keer wordt gebruikt. Zie de [energy_demand_notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand)voor meer informatie over één time-serie.||
|`freq`| De frequentie van de time series-gegevensset. Deze para meter vertegenwoordigt de periode waarmee gebeurtenissen worden verwacht, zoals dagelijks, wekelijks, jaarlijks, enzovoort. De frequentie moet een alias met een [Panda-offset](https://pandas.pydata.org/pandas-docs/stable/user_guide/timeseries.html#dateoffset-objects)zijn.||
|`target_lags`|Het aantal rijen dat de doel waarden moeten worden vertraagd op basis van de frequentie van de gegevens. De vertraging wordt weer gegeven als een lijst of één geheel getal. Er moet een vertraging worden gebruikt wanneer de relatie tussen de onafhankelijke variabelen en de afhankelijke variabele standaard niet overeenkomt met of correleert. ||
|`feature_lags`| De functies voor vertraging worden automatisch bepaald door automatische MILLILITERs wanneer `target_lags` ze zijn ingesteld en `feature_lags` ingesteld op `auto` . Het inschakelen van functie lags kan helpen de nauw keurigheid te verbeteren. Functie lags zijn standaard uitgeschakeld. ||
|`target_rolling_window_size`|*n* historische Peri Oden die moeten worden gebruikt voor het genereren van prognose waarden, <= grootte van de Trainingsset. Als u dit weglaat, is *n* de volledige grootte van de Trainingsset. Geef deze para meter op als u alleen een bepaalde hoeveelheid geschiedenis wilt beschouwen bij het trainen van het model. Meer informatie over het [samen voegen van target-rollen](#target-rolling-window-aggregation).||
|`short_series_handling_config`| Hiermee wordt het verwerken van korte tijd reeksen voor komen dat fouten tijdens de training mislukken vanwege onvoldoende gegevens. De verwerking van de korte serie is standaard ingesteld op `auto` . Meer informatie over [verwerking van de korte serie](#short-series-handling).|


De volgende code, 
* Maakt gebruik van de- [`ForecastingParameters`](/python/api/azureml-automl-core/azureml.automl.core.forecasting_parameters.forecastingparameters) klasse om de para meters voor de voor spellingen van uw experiment training te definiëren
* Hiermee stelt `time_column_name` u de `day_datetime` waarde in op het veld in de gegevensset. 
* Hiermee definieert `time_series_id_column_names` u de para meter voor `"store"` . Dit zorgt ervoor dat er **twee afzonderlijke time-series groepen** worden gemaakt voor de gegevens. een voor Store A en B.
* Hiermee stelt `forecast_horizon` u de 50 in op om te voors pellen voor de hele testset. 
* Hiermee stelt u een prognose venster in op 10 Peri Oden met `target_rolling_window_size`
* Hiermee wordt één vertraging van de doel waarden voor twee Peri Oden met de `target_lags` para meter opgegeven. 
* Wordt ingesteld `target_lags` op de aanbevolen instelling ' automatisch ', waardoor deze waarde automatisch voor u wordt gedetecteerd.

```python
from azureml.automl.core.forecasting_parameters import ForecastingParameters

forecasting_parameters = ForecastingParameters(time_column_name='day_datetime', 
                                               forecast_horizon=50,
                                               time_series_id_column_names=["store"],
                                               freq='W',
                                               target_lags='auto',
                                               target_rolling_window_size=10)
                                              
```

Deze `forecasting_parameters` worden vervolgens door gegeven aan uw standaard `AutoMLConfig` object, samen met het `forecasting` taak type, de primaire metrische gegevens, afsluit criteria en trainings gegevens. 

```python
from azureml.core.workspace import Workspace
from azureml.core.experiment import Experiment
from azureml.train.automl import AutoMLConfig
import logging

automl_config = AutoMLConfig(task='forecasting',
                             primary_metric='normalized_root_mean_squared_error',
                             experiment_timeout_minutes=15,
                             enable_early_stopping=True,
                             training_data=train_data,
                             label_column_name=label,
                             n_cross_validations=5,
                             enable_ensembling=False,
                             verbosity=logging.INFO,
                             **forecasting_parameters)
```

De hoeveelheid gegevens die nodig zijn voor het trainen van een prognose model met automatische MILLILITERs wordt beïnvloed door de `forecast_horizon` `n_cross_validations` waarden,, en `target_lags` of `target_rolling_window_size` die zijn opgegeven bij het configureren van uw `AutoMLConfig` . 

De volgende formule berekent de hoeveelheid historische gegevens die nodig zijn voor het bouwen van de functies van de tijd reeks.

Mini maal vereiste historische gegevens: (2x `forecast_horizon` ) + # `n_cross_validations` + Max (Max ( `target_lags` ), `target_rolling_window_size` )

Er wordt een fout uitzondering gegenereerd voor alle reeksen in de gegevensset die niet voldoen aan de vereiste hoeveelheid historische gegevens voor de opgegeven instellingen. 

### <a name="featurization-steps"></a>Parametrisatie-stappen

In elk automatisch machine learning experiment worden standaard technieken voor automatisch schalen en normaliseren toegepast op uw gegevens. Deze technieken zijn soorten **parametrisatie** die *bepaalde* algoritmen helpen die gevoelig zijn voor functies op verschillende schalen. Meer informatie over de standaard parametrisatie-stappen in [parametrisatie in AutoML](how-to-configure-auto-features.md#automatic-featurization)

De volgende stappen worden echter alleen uitgevoerd voor `forecasting` taak typen:

* Detectie frequentie van tijds reeksen detecteren (bijvoorbeeld elk uur, dagelijks, wekelijks) en nieuwe records maken voor ontbrekende tijd punten om de reeks continu te maken.
* Ontbrekende waarden in het doel (via voorwaartse opvulling) en functie kolommen toegerekend (met behulp van mediaan kolom waarden)
* Maak functies op basis van Time Series-id's om vaste effecten in verschillende reeksen in te scha kelen
* Op tijd gebaseerde functies maken voor het leren van seizoensgebonden patronen
* Categorische variabelen coderen naar numerieke aantallen

Zie [parametrisatie transparantie](how-to-configure-auto-features.md#featurization-transparency) voor een overzicht van de functies die worden gemaakt als gevolg van deze stappen.

> [!NOTE]
> Automatische machine learning parametrisatie stappen (functie normalisatie, het verwerken van ontbrekende gegevens, het converteren van tekst naar numerieke waarde, enzovoort) worden onderdeel van het onderliggende model. Wanneer u het model gebruikt voor voor spellingen, worden dezelfde parametrisatie-stappen die tijdens de training worden toegepast, automatisch toegepast op de invoer gegevens.

#### <a name="customize-featurization"></a>Parametrisatie aanpassen

U hebt ook de optie om uw parametrisatie-instellingen aan te passen, zodat u zeker weet dat de gegevens en functies die worden gebruikt om uw ML-model te trainen, in relevante voor spellingen resulteren. 

Ondersteunde aanpassingen voor `forecasting` taken zijn onder andere:

|Aanpassing|Definitie|
|--|--|
|**Update van het kolom doel**|Overschrijf het automatisch gedetecteerde functie type voor de opgegeven kolom.|
|**Para meter bijwerken van trans formatie** |De para meters voor de opgegeven transformator bijwerken. *Ondersteunt momenteel* toerekenings functie (fill_value en mediaan).|
|**Kolommen verwijderen** |Hiermee geeft u kolommen op die moeten worden featurized.|

Geef in uw object op om featurizations aan te passen met de SDK `"featurization": FeaturizationConfig` `AutoMLConfig` . Meer informatie over [aangepaste featurizations](how-to-configure-auto-features.md#customize-featurization).

>[!NOTE]
> De functionaliteit **Drop columns** is afgeschaft vanaf SDK-versie 1,19. Verwijder kolommen uit uw gegevensset als onderdeel van het opschonen van gegevens, voordat u deze in uw geautomatiseerde ML experiment gebruikt. 

```python
featurization_config = FeaturizationConfig()

# `logQuantity` is a leaky feature, so we remove it.
featurization_config.drop_columns = ['logQuantitity']

# Force the CPWVOL5 feature to be of numeric type.
featurization_config.add_column_purpose('CPWVOL5', 'Numeric')

# Fill missing values in the target column, Quantity, with zeroes.
featurization_config.add_transformer_params('Imputer', ['Quantity'], {"strategy": "constant", "fill_value": 0})

# Fill mising values in the `INCOME` column with median value.
featurization_config.add_transformer_params('Imputer', ['INCOME'], {"strategy": "median"})
```

Zie [How to Customize parametrisatie in the Studio](how-to-use-automated-ml-for-ml-models.md#customize-featurization)(Engelstalig) als u de Azure machine learning Studio gebruikt voor uw experiment.

## <a name="optional-configurations"></a>Optionele configuraties

Er zijn aanvullende optionele configuraties beschikbaar voor prognoses van taken, zoals het inschakelen van diep gaande lessen en het opgeven van een doel aggregatie voor een rolling venster. 

### <a name="enable-deep-learning"></a>Grondige training inschakelen

> [!NOTE]
> DNN-ondersteuning voor prognoses in geautomatiseerde Machine Learning is **een preview-versie** en wordt niet ondersteund voor lokale uitvoeringen.

U kunt ook gebruikmaken van diep gaande lessen met diepe Neural netwerken, DNNs, om de scores van uw model te verbeteren. Met de grondige training van automatische ML kunt u Univariate-en multidimensionale tijdreeks gegevens voors pellen.

Uitgebreide leer modellen hebben drie ingebouwde mogelijkheden:
1. Ze kunnen leren van wille keurige toewijzingen van invoer naar uitvoer
1. Ze ondersteunen meerdere invoer en uitvoer
1. Ze kunnen automatisch patronen extra heren in invoer gegevens die meer dan lange reeksen omspannen. 

Als u uitgebreide training wilt inschakelen, stelt u de `enable_dnn=True` in het- `AutoMLConfig` object in.

```python
automl_config = AutoMLConfig(task='forecasting',
                             enable_dnn=True,
                             ...
                             **forecasting_parameters)
```
> [!Warning]
> Wanneer u DNN inschakelt voor experimenten die zijn gemaakt met de SDK, worden de [Aanbevolen model verklaringen](how-to-machine-learning-interpretability-automl.md) uitgeschakeld.

Als u DNN wilt inschakelen voor een AutoML-experiment dat is gemaakt in de Azure Machine Learning Studio, raadpleegt u de instellingen voor het [taak type in de Studio How-to](how-to-use-automated-ml-for-ml-models.md#create-and-run-experiment).

Bekijk het voor beeld van de [drank productie prognose](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-beer-remote/auto-ml-forecasting-beer-remote.ipynb) voor een gedetailleerd code voorbeeld met DNNs.

### <a name="target-rolling-window-aggregation"></a>Aggregatie van het doel venster
De beste informatie die een Forecaster kan hebben, is vaak de recente waarde van het doel.  Met aggregaties voor het doel venster kunt u een roulerende aggregatie van gegevens waarden toevoegen als onderdelen. Het genereren en gebruiken van deze aanvullende functies als extra contextuele gegevens helpt de nauw keurigheid van het trein model.

Stel bijvoorbeeld dat u energie vraag wilt voors pellen. Mogelijk wilt u een functie van een rolling venster van drie dagen toevoegen om rekening te laten gaan met thermische wijzigingen van verwarmde ruimten. In dit voor beeld maakt u dit venster op basis van `target_rolling_window_size= 3` de instelling in de `AutoMLConfig` constructor. 

In de tabel ziet u de resulterende functie techniek die zich voordoet wanneer de venster aggregatie wordt toegepast. Kolommen voor **minimum, maximum** en **som** worden gegenereerd op een sliding window van drie op basis van de gedefinieerde instellingen. Elke rij heeft een nieuwe berekende functie, in het geval van de tijds tempel voor 8 september 2017 4: am de waarden voor maximum, minimum en som worden berekend met behulp van de **vraag waarden** voor 8 september 2017 1: am-3: am. In dit venster van drie ploegen worden de gegevens voor de resterende rijen ingevuld.

![doorlopend venster voor doel](./media/how-to-auto-train-forecast/target-roll.svg)

Bekijk een voor beeld van een python-code met behulp van de [samenvoeg functie van het doel venster](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb).

### <a name="short-series-handling"></a>Verwerking van de korte serie

Voor automatische ML wordt een tijd reeks in rekening gehouden met een **korte serie** als er onvoldoende gegevens punten zijn voor het uitvoeren van de trein-en validerings fasen van model ontwikkeling. Het aantal gegevens punten varieert voor elk experiment en is afhankelijk van de max_horizon, het aantal Kruistabel validaties en de lengte van het model lookback. Dit is het maximale aantal geschiedenis dat nodig is voor het bouwen van de functies van de tijd reeks. Raadpleeg de [documentatie over short_series_handling_configuration](/python/api/azureml-automl-core/azureml.automl.core.forecasting_parameters.forecastingparameters#short-series-handling-configuration)voor de exacte berekening.

Automatische ML biedt de verwerking van korte reeksen standaard met de `short_series_handling_configuration` para meter in het `ForecastingParameters` object. 

Als u de verwerking van de korte serie wilt inschakelen, `freq` moet u ook de para meter definiëren. Als u een frequentie wilt definiëren, worden deze ingesteld `freq='H'` . Bekijk [hier](https://pandas.pydata.org/pandas-docs/stable/user_guide/timeseries.html#dateoffset-objects)de opties voor de frequentie reeks. Als u het standaard gedrag wilt wijzigen, `short_series_handling_configuration = 'auto'` werkt u de `short_series_handling_configuration` para meter in uw `ForecastingParameter` object bij.  

```python
from azureml.automl.core.forecasting_parameters import ForecastingParameters

forecast_parameters = ForecastingParameters(time_column_name='day_datetime', 
                                            forecast_horizon=50,
                                            short_series_handling_configuration='auto',
                                            freq = 'H',
                                            target_lags='auto')
```
De volgende tabel bevat een overzicht van de beschik bare instellingen voor `short_series_handling_config` .
 
|Instelling|Beschrijving
|---|---
|`auto`| Het volgende is het standaard gedrag voor verwerking van de korte serie <li> *Als alle reeksen kort zijn*, moet u de gegevens aanvullen. <br> <li> *Als niet alle reeksen kort zijn*, verwijdert u de korte reeks. 
|`pad`| Als `short_series_handling_config = pad` , vervolgens worden door automatische ml wille keurige waarden toegevoegd aan elke korte reeks die wordt gevonden. Hieronder vindt u een lijst met de kolom typen en hoe deze worden aangevuld met: <li>Object kolommen met NaNs <li> Numerieke kolommen met 0 <li> Boole/Logic-kolommen met de waarde ONWAAR <li> De doel kolom wordt aangevuld met wille keurige waarden met een gemiddelde van nul en een standaard deviatie van 1. 
|`drop`| Als `short_series_handling_config = drop` , wordt de korte serie door automatische ml gezakt en wordt deze niet gebruikt voor training of voor spellingen. Voor spellingen voor deze reeksen worden NaN geretourneerd.
|`None`| Er wordt geen reeks opgevuld of verwijderd

>[!WARNING]
>Opvulling kan van invloed zijn op de nauw keurigheid van het resulterende model, omdat we kunst matige gegevens hebben geïntroduceerd om zonder storingen te kunnen profiteren van een eerdere training. <br> <br> Als veel van de reeksen kort zijn, ziet u mogelijk ook wat invloed heeft op de resultaten van de uitleg

## <a name="run-the-experiment"></a>Het experiment uitvoeren 

Wanneer u uw `AutoMLConfig` object klaar hebt, kunt u het experiment verzenden. Wanneer het model is voltooid, haalt u de best mogelijke run-iteratie op.

```python
ws = Workspace.from_config()
experiment = Experiment(ws, "Tutorial-automl-forecasting")
local_run = experiment.submit(automl_config, show_output=True)
best_run, fitted_model = local_run.get_output()
```
 
## <a name="forecasting-with-best-model"></a>Prognose met het beste model

Gebruik de beste model iteratie om waarden voor de test gegevensset te prognoseen.

De `forecast()` functie staat specificaties toe voor het starten van voor spellingen, in tegens telling tot de `predict()` , die doorgaans wordt gebruikt voor classificatie-en regressie taken.

In het volgende voor beeld vervangt u eerst alle waarden in `y_pred` met `NaN` . In dit geval wordt de oorsprong van de prognose aan het einde van de trainings gegevens weer gegeven. Als u echter alleen de tweede helft van met hebt `y_pred` vervangen `NaN` , zou de functie de numerieke waarden in de eerste helft ongewijzigd laten, maar de `NaN` waarden in de tweede helft worden geforecastd. De functie retourneert zowel de voorspelde waarden als de uitgelijnde functies.

U kunt ook de `forecast_destination` para meter in de `forecast()` functie gebruiken om waarden te ramen tot een opgegeven datum.

```python
label_query = test_labels.copy().astype(np.float)
label_query.fill(np.nan)
label_fcst, data_trans = fitted_pipeline.forecast(
    test_data, label_query, forecast_destination=pd.Timestamp(2019, 1, 8))
```

Bereken het basis gemiddelde in kwadraat (RMSE) tussen de `actual_labels` werkelijke waarden en de voorspelde waarden in `predict_labels` .

```python
from sklearn.metrics import mean_squared_error
from math import sqrt

rmse = sqrt(mean_squared_error(actual_labels, predict_labels))
rmse
```

Nu de algehele model nauwkeurigheid is vastgesteld, is de volgende stap het model te gebruiken voor het voors pellen van onbekende toekomstige waarden. 

Geef een gegevensset op in dezelfde indeling als de testset `test_data` , maar met toekomstige datetimes en de resulterende Voorspellings is de voorspelde waarden voor elke stap van de tijds reeks. Stel dat de laatste tijdreeks records in de gegevensset 12/31/2018 zijn. Voor een prognose van de vraag voor de volgende dag (of zoveel Peri Oden als u nodig hebt om een prognose te maken, <= `forecast_horizon` ), maakt u een eenmalige reeks record voor elke Store voor 01/01/2019.

```output
day_datetime,store,week_of_year
01/01/2019,A,1
01/01/2019,A,1
```

Herhaal de stappen die nodig zijn om deze toekomstige gegevens naar een data frame te laden en voer vervolgens uit `best_run.predict(test_data)` om toekomstige waarden te voors pellen.

> [!NOTE]
> Voor spellingen in het voor beeld worden niet ondersteund voor prognoses met automatische ML wanneer `target_lags` en/of `target_rolling_window_size` zijn ingeschakeld.


## <a name="example-notebooks"></a>Voorbeeldnotebooks
Zie de voor beelden van voor beeld van de [voorbeeld notitieblokken](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning) voor gedetailleerde code voorbeelden van een geavanceerde prognose configuratie, waaronder:

* [Kerst detectie en parametrisatie](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-bike-share/auto-ml-forecasting-bike-share.ipynb)
* [Cross-Origin-Kruis validatie](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)
* [Configureer bare lags](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-bike-share/auto-ml-forecasting-bike-share.ipynb)
* [statistische functies van het Rolling venster](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)
* [DNN](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-beer-remote/auto-ml-forecasting-beer-remote.ipynb)

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [hoe en waar een model moet worden geïmplementeerd](how-to-deploy-and-where.md).
* Meer informatie over [Interpretiteit: model uitleg bij automatische machine learning (preview)](how-to-machine-learning-interpretability-automl.md). 
* Meer informatie over het trainen van meerdere modellen met AutoML in de [vele modellen oplossings versneller](https://aka.ms/many-models).
* Volg de [zelf studie](tutorial-auto-train-models.md) voor een end-to-end-voor beeld voor het maken van experimenten met automatische machine learning.
