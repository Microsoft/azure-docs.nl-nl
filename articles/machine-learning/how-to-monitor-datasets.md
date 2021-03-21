---
title: Gegevens drift detecteren in gegevens sets (preview-versie)
titleSuffix: Azure Machine Learning
description: Meer informatie over het instellen van de detectie van gegevens drift in azure learning. Maak de monitors voor gegevens sets (preview), Controleer of er gegevensdrift zijn en stel waarschuwingen in.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: sgilley
ms.author: copeters
author: lostmygithubaccount
ms.date: 06/25/2020
ms.topic: conceptual
ms.custom: how-to, data4ml, contperf-fy21q2
ms.openlocfilehash: 5a3d16445c5a4276f07f4ed502b9830a10c4ff72
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102518905"
---
# <a name="detect-data-drift-preview-on-datasets"></a>Gegevens drift (preview) detecteren in gegevens sets

Meer informatie over het bewaken van gegevens drift en het instellen van waarschuwingen wanneer de drift hoog is.  

Met Azure Machine Learning gegevensset monitors (preview) kunt u het volgende doen:
* **Analyseer de drift in uw gegevens** om inzicht te krijgen in de manier waarop deze in de loop van de tijd verandert.
* **Model gegevens bewaken** voor verschillen tussen trainingen en gegevens sets.  Verzamel eerst [model gegevens uit geïmplementeerde modellen](how-to-enable-data-collection.md).
* **Bewaak nieuwe gegevens** voor verschillen tussen de basis lijn en de doel-DataSet.
* **Profiel functies in gegevens** om bij te houden hoe statistische eigenschappen na verloop van tijd veranderen.
* **Stel waarschuwingen in voor de gegevens drift** voor vroegtijdige waarschuwingen voor potentiële problemen. 
* **[Maak een nieuwe versie van de gegevensset] (hoe-naar-versie-track-gegevens sets** wanneer u hebt vastgesteld dat er te veel records zijn.

Er wordt een [Azure machine learning-gegevensset](how-to-create-register-datasets.md) gebruikt om de monitor te maken. De gegevensset moet een time stamp-kolom bevatten.

U kunt metrische gegevens over data drift weer geven met de python-SDK of in Azure Machine Learning Studio.  Andere metrische gegevens en inzichten zijn beschikbaar via de [Azure-toepassing Insights](../azure-monitor/app/app-insights-overview.md) -resource die aan de Azure machine learning-werk ruimte is gekoppeld.

> [!IMPORTANT]
> De gegevensdrijf detectie voor gegevens sets is momenteel beschikbaar als open bare preview.
> De preview-versie wordt aangeboden zonder Service Level Agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om de monitors voor de gegevensset te maken en te gebruiken:
* Een Azure-abonnement. Als u geen Azure-abonnement hebt, maakt u een gratis account voordat u begint. Probeer vandaag nog de [gratis of betaalde versie van Azure Machine Learning](https://aka.ms/AMLFree).
* Een [Azure machine learning-werk ruimte](how-to-manage-workspace.md).
* De [Azure machine learning SDK voor python is geïnstalleerd](/python/api/overview/azure/ml/install), waaronder het pakket met de azureml-gegevens sets.
* Gestructureerde (tabellaire) gegevens met een tijds tempel die is opgegeven in het bestandspad, de bestands naam of de kolom in de gegevens.

## <a name="what-is-data-drift"></a>Wat is gegevens drift?

Gegevens drift is een van de belangrijkste redenen voor de nauw keurigheid van het model in de loop van de tijd.  Voor machine learning modellen is gegevens drift de wijziging in model invoer gegevens die leidt tot het model leren van prestaties.  Bewaken van gegevens drift helpt bij het detecteren van deze prestatie problemen voor modellen.

Oorzaken van gegevens drift zijn onder andere:

- Proces wijzigingen in de upstream, zoals een sensor die wordt vervangen, waarbij de maat eenheden worden gewijzigd van inches in centimeters. 
- Problemen met gegevens kwaliteit, zoals een gebroken sensor, worden altijd 0 gelezen.
- Natuurlijke drift in de gegevens, zoals gemiddelde temperatuur wijziging, met de seizoenen.
- Wijziging ten opzichte van functies of covariate SHIFT. 

Azure Machine Learning vereenvoudigt de detectie van de drift door één metrische waarde te berekenen die de complexiteit van gegevens sets vergeleken wordt.  Deze gegevens sets kunnen honderden functies en tien duizenden rijen bevatten. Zodra de drift is gedetecteerd, kunt u inzoomen op de functies die de drift veroorzaken.  Vervolgens inspecteert u de metrische gegevens van het functie niveau om fouten op te sporen en de hoofd oorzaak voor de drift te isoleren.

Met deze neerwaartse benadering kunt u eenvoudig gegevens bewaken in plaats van traditionele op regels gebaseerde technieken. Op regels gebaseerde technieken zoals toegestaan gegevens bereik of toegestane unieke waarden kunnen tijdrovend en fout gevoelig zijn.

In Azure Machine Learning gebruikt u de monitors voor gegevensset om gegevens drift te detecteren en te waarschuwen.
  
### <a name="dataset-monitors"></a>Monitor voor gegevensset 

Met een gegevensset-monitor kunt u het volgende doen:

* Gegevens drift detecteren en waarschuwen voor nieuwe gegevens in een gegevensset.
* Analyseer historische gegevens voor drift.
* Nieuwe gegevens in de loop van de tijd van het profiel.

Het algoritme voor gegevens drift biedt een algemene meting van de gegevens wijziging en geeft aan welke functies verantwoordelijk zijn voor nader onderzoek. De monitors van de gegevensset geven een aantal andere metrische gegevens aan door het profileren van de nieuwe informatie in de `timeseries` gegevensset. 

Aangepaste waarschuwingen kunnen worden ingesteld op alle metrische gegevens die door de monitor worden gegenereerd via [Azure-toepassing Insights](../azure-monitor/app/app-insights-overview.md). U kunt de monitors van de gegevensset gebruiken om snel gegevens problemen te ondervangen en de tijd te beperken om het probleem op te lossen door waarschijnlijke oorzaken te identificeren.  

In het algemeen zijn er drie primaire scenario's voor het instellen van gegevensset-monitors in Azure Machine Learning.

Scenario | Beschrijving
---|---
De gegevens van een model bewaken voor drift van de trainings gegevens | De resultaten van dit scenario kunnen worden geïnterpreteerd als bewaking van een proxy voor de nauw keurigheid van het model, omdat de model nauwkeurigheid afnemen wanneer het gaat om gegevens drift van de trainings gegevens.
Een time series-gegevensset controleren voor drift vanaf een vorige tijds periode. | Dit scenario is algemener en kan worden gebruikt voor het bewaken van gegevens sets die betrekking hebben op de upstream of het maken van een model.  De doel gegevensset moet een time stamp-kolom bevatten. De gegevensset voor de basis lijn kan elke tabellaire gegevensset zijn met functies die gemeen schappelijk zijn met de doel-DataSet.
Analyse uitvoeren op gegevens in het verleden. | Dit scenario kan worden gebruikt om historische gegevens te begrijpen en beslissingen op te nemen in instellingen voor de monitors van de gegevensset.

Gegevensset monitors zijn afhankelijk van de volgende Azure-Services.

|Azure-service  |Beschrijving  |
|---------|---------|
| *Gegevensset* | Drift maakt gebruik van Machine Learning gegevens sets om trainings gegevens op te halen en gegevens voor model training te vergelijken.  Het genereren van een gegevens profiel wordt gebruikt voor het genereren van een aantal van de gerapporteerde metrieken, zoals min, Max, DISTINCT-waarden en aantal afzonderlijke waarden. |
| *Azureml-pijp lijn en-reken kracht* | De taak voor het berekenen van de drift wordt gehost in de azureml-pijp lijn.  De taak wordt op aanvraag geactiveerd of op schema om te worden uitgevoerd op een reken tijd die is geconfigureerd tijdens het maken van de drijf monitor.
| *Application Insights*| Met drift worden metrische gegevens verzonden naar Application Insights die horen bij de machine learning-werk ruimte.
| *Azure Blob Storage*| Met drift worden metrische gegevens in JSON-indeling verzonden naar Azure Blob-opslag.

### <a name="baseline-and-target-datasets"></a>Gegevens sets basis lijn en doel 

U bewaakt [Azure machine learning gegevens sets](how-to-create-register-datasets.md) voor data drift. Wanneer u een gegevensset-monitor maakt, gaat u naar het volgende:
* Basislijn gegevensset: doorgaans de gegevensset van de training voor een model.
* Doel gegevensset-doorgaans model invoer gegevens-wordt in de loop van de tijd vergeleken met de gegevensset van de basis lijn. Deze vergelijking betekent dat er een time stamp-kolom moet worden opgegeven voor de doel-DataSet.

De monitor vergelijkt de basislijn-en doel gegevens sets.

## <a name="create-target-dataset"></a>Doel gegevensset maken

De eigenschappen van de doel-dataset moeten worden `timeseries` ingesteld op de gewenste eigenschap door de time stamp-kolom op te geven van een kolom in de gegevens of een virtuele kolom die is afgeleid van het pad patroon van de bestanden. Maak de gegevensset met een tijds tempel via de [python-SDK](#sdk-dataset) of [Azure machine learning Studio](#studio-dataset). Een kolom met een tijds tempel moet worden opgegeven om eigenschappen toe `timeseries` te voegen aan de gegevensset. Als uw gegevens zijn gepartitioneerd in mapstructuur met tijd gegevens, zoals {JJJJ/MM/DD}, maakt u een virtuele kolom met de instelling voor het pad patroon en stelt u deze in als de tijds tempel van de partitie om het belang van de tijd reeks functionaliteit te verbeteren.

# <a name="python"></a>[Python](#tab/python)
<a name="sdk-dataset"></a>

De [`Dataset`](/python/api/azureml-core/azureml.data.tabulardataset#with-timestamp-columns-timestamp-none--partition-timestamp-none--validate-false----kwargs-) [`with_timestamp_columns()`](/python/api/azureml-core/azureml.data.tabulardataset#with-timestamp-columns-timestamp-none--partition-timestamp-none--validate-false----kwargs-)  methode class definieert de time stamp-kolom voor de gegevensset.

```python 
from azureml.core import Workspace, Dataset, Datastore

# get workspace object
ws = Workspace.from_config()

# get datastore object 
dstore = Datastore.get(ws, 'your datastore name')

# specify datastore paths
dstore_paths = [(dstore, 'weather/*/*/*/*/data.parquet')]

# specify partition format
partition_format = 'weather/{state}/{date:yyyy/MM/dd}/data.parquet'

# create the Tabular dataset with 'state' and 'date' as virtual columns 
dset = Dataset.Tabular.from_parquet_files(path=dstore_paths, partition_format=partition_format)

# assign the timestamp attribute to a real or virtual column in the dataset
dset = dset.with_timestamp_columns('date')

# register the dataset as the target dataset
dset = dset.register(ws, 'target')
```

> [!TIP]
> Voor een volledig voor beeld van het gebruik `timeseries` van de eigenschappen van gegevens sets raadpleegt u het [voor beeld-notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/timeseries-datasets/tabular-timeseries-dataset-filtering.ipynb) of de SDK-documentatie voor [gegevens sets](/python/api/azureml-core/azureml.data.tabulardataset#with-timestamp-columns-timestamp-none--partition-timestamp-none--validate-false----kwargs-).

# <a name="studio"></a>[Studio](#tab/azure-studio)

<a name="studio-dataset"></a>

Als u uw gegevensset maakt met behulp van Azure Machine Learning Studio, zorgt u ervoor dat het pad naar uw gegevens tijds tempel informatie bevat, alle submappen met gegevens bevatten en de partitie-indeling instellen.

In het volgende voor beeld worden alle gegevens in de submap *NoaaIsdFlorida/2019* gemaakt en de partitie-indeling geeft het jaar, de maand en de dag van het tijds tempel.

[![Partitie-indeling](./media/how-to-monitor-datasets/partition-format.png)](media/how-to-monitor-datasets/partition-format-expand.png)

Geef in de **schema** -instellingen de time stamp-kolom op uit een virtuele of Real-kolom in de opgegeven gegevensset:

:::image type="content" source="media/how-to-monitor-datasets/timestamp.png" alt-text="De tijds tempel instellen":::

Als uw gegevens op datum zijn gepartitioneerd, zoals hier het geval is, kunt u ook de partition_timestamp opgeven.  Hierdoor kan de verwerking van datums efficiënter worden uitgevoerd.

:::image type="content" source="media/how-to-monitor-datasets/timeseries-partitiontimestamp.png" alt-text="Tijds tempel van partitie":::

---

## <a name="create-dataset-monitor"></a>Controle programma voor gegevensset maken

Maak een gegevensset-monitor om gegevens drift te detecteren en te waarschuwen voor een nieuwe gegevensset.  Gebruik de [python-SDK](#sdk-monitor) of [Azure machine learning Studio](#studio-monitor).

# <a name="python"></a>[Python](#tab/python)
<a name="sdk-monitor"></a> Raadpleeg de [documentatie van PYTHON SDK voor meer informatie over gegevens drift](/python/api/azureml-datadrift/azureml.datadrift) . 

In het volgende voor beeld ziet u hoe u een DataSet-monitor maakt met behulp van de python-SDK

```python
from azureml.core import Workspace, Dataset
from azureml.datadrift import DataDriftDetector
from datetime import datetime

# get the workspace object
ws = Workspace.from_config()

# get the target dataset
dset = Dataset.get_by_name(ws, 'target')

# set the baseline dataset
baseline = target.time_before(datetime(2019, 2, 1))

# set up feature list
features = ['latitude', 'longitude', 'elevation', 'windAngle', 'windSpeed', 'temperature', 'snowDepth', 'stationName', 'countryOrRegion']

# set up data drift detector
monitor = DataDriftDetector.create_from_datasets(ws, 'drift-monitor', baseline, target, 
                                                      compute_target='cpu-cluster', 
                                                      frequency='Week', 
                                                      feature_list=None, 
                                                      drift_threshold=.6, 
                                                      latency=24)

# get data drift detector by name
monitor = DataDriftDetector.get_by_name(ws, 'drift-monitor')

# update data drift detector
monitor = monitor.update(feature_list=features)

# run a backfill for January through May
backfill1 = monitor.backfill(datetime(2019, 1, 1), datetime(2019, 5, 1))

# run a backfill for May through today
backfill1 = monitor.backfill(datetime(2019, 5, 1), datetime.today())

# disable the pipeline schedule for the data drift detector
monitor = monitor.disable_schedule()

# enable the pipeline schedule for the data drift detector
monitor = monitor.enable_schedule()
```

> [!TIP]
> Zie voor een volledig voor beeld van het instellen van een `timeseries` gegevensset en gegevens drift een [voor beeld](https://aka.ms/datadrift-notebook)van een notebook.


# <a name="studio"></a>[Studio](#tab/azure-studio)
<a name="studio-monitor"></a>

1. Ga naar de [Start pagina van de Studio](https://ml.azure.com).
1. Selecteer het tabblad **gegevens sets** aan de linkerkant. 
1. Selecteer de **monitors** voor de gegevensset.
   ![Monitor lijst](./media/how-to-monitor-datasets/monitor-list.png)

1. Klik op de knop **monitor maken** en ga door met de wizard door op **volgende** te klikken.  

:::image type="content" source="media/how-to-monitor-datasets/wizard.png" alt-text="Een wizard voor het maken van een monitor":::

* **Selecteer doel gegevensset**.  De doel-dataset is een gegevensset in tabel vorm waarvoor een time stamp-kolom is opgegeven die wordt geanalyseerd voor gegevens drift. De doel gegevensset moet algemene kenmerken hebben met de gegevensset van de basis lijn en moet een `timeseries` gegevensset zijn waaraan nieuwe gegevens worden toegevoegd. Historische gegevens in de doel-gegevensset kunnen worden geanalyseerd of nieuwe gegevens kunnen worden bewaakt.

* **Selecteer de basislijn gegevensset.**  Selecteer de tabellaire gegevensset die moet worden gebruikt als de basis lijn voor de vergelijking van de doel gegevensset gedurende een bepaalde periode.  De gegevensset van de basis lijn moet algemene kenmerken hebben met de doel-DataSet.  Selecteer een tijds bereik voor het gebruik van een segment van de doel gegevensset of geef een afzonderlijke gegevensset op die moet worden gebruikt als basis lijn.

* **Instellingen controleren**.  Deze instellingen zijn voor de bewaakte pijp lijn van de geplande gegevensset, die wordt gemaakt. 

    | Instelling | Beschrijving | Tips | Veranderlijk | 
    | ------- | ----------- | ---- | ------- |
    | Name | De naam van de monitor van de gegevensset. | | Nee |
    | Functies | Lijst met functies die gedurende een bepaalde periode worden geanalyseerd voor gegevens opslag. | Ingesteld op de uitvoer functie (s) van een model om concept drift te meten. Neem geen functies op die in de loop van de tijd (maand, jaar, index enz.) natuurlijk zijn. U kunt backfill en een bestaande gegevensdrijf monitor na het aanpassen van de lijst met functies. | Ja | 
    | Rekendoel | Azure Machine Learning Compute-doel voor het uitvoeren van de controle taken voor de gegevensset. | | Ja | 
    | Inschakelen | Het schema in-of uitschakelen voor de monitor pijplijn van de gegevensset | Schakel het schema uit om historische gegevens te analyseren met de instelling backfill. De functie kan worden ingeschakeld nadat de monitor voor de gegevensset is gemaakt. | Ja | 
    | Frequentie | De frequentie die wordt gebruikt om de pijplijn taak te plannen en historische gegevens te analyseren als er een backfill wordt uitgevoerd. Opties zijn dagelijks, wekelijks of maandelijks. | Bij elke uitvoering worden de gegevens in de doel gegevensset vergeleken op basis van de frequentie: <li>Dagelijks: de meest recente volledige dag in de doel gegevensset vergelijken met de basis lijn <li>Wekelijks: de meest recente volledige week (maandag-zondag) in de doel gegevensset vergelijken met de basis lijn <li>Maandelijks: de meest recente volledige maand in de doel gegevensset vergelijken met de basis lijn | Nee | 
    | Latentie | Tijd, in uren, duurt het om gegevens in de gegevensset te ontvangen. Als het bijvoorbeeld drie dagen duurt voordat de gegevens in de SQL-Data Base worden inge kapseld, stelt u de gegevensset in op 72. | Kan niet worden gewijzigd nadat de monitor voor de gegevensset is gemaakt | Nee | 
    | E-mailadressen | E-mail adressen voor waarschuwingen op basis van schending van de drempel waarde voor het percentage gegevens drift. | E-mail berichten worden verzonden via Azure Monitor. | Ja | 
    | Drempelwaarde | Drempel waarde voor het percentage gegevens drift voor e-mail waarschuwingen. | Verdere waarschuwingen en gebeurtenissen kunnen worden ingesteld op veel andere metrische gegevens in de gekoppelde Application Insights resource van de werk ruimte. | Ja |

Nadat de wizard is voltooid, wordt de resulterende gegevensset-monitor weer gegeven in de lijst. Selecteer deze optie om naar de pagina met details van de monitor te gaan.

---

## <a name="understand-data-drift-results"></a>Resultaten van gegevens drift begrijpen

In deze sectie ziet u de resultaten van het bewaken van een gegevensset die is gevonden op de pagina **gegevens sets**  /  **gegevensset monitors** in azure Studio.  U kunt de instellingen bijwerken en bestaande gegevens voor een bepaalde periode op deze pagina analyseren.  

Begin met inzichten op het hoogste niveau in de omvang van gegevens drift en een hooglicht van functies die verder moeten worden onderzocht.

:::image type="content" source="media/how-to-monitor-datasets/drift-overview.png" alt-text="Overzicht van drift":::


| Metrisch | Beschrijving | 
| ------ | ----------- | 
| Grootte van gegevens drift | Een percentage van de verschuiving tussen de basis lijn en de doel gegevensset gedurende een bepaalde periode. Variërend van 0 tot 100 duidt 0 op identieke gegevens sets en 100 geeft aan dat het gegevensdrijf model van Azure Machine Learning de twee gegevens sets volledig kan onderscheiden. Er wordt een nauw keuriger percentage gemeten als gevolg van machine learning technieken die worden gebruikt voor het genereren van deze omvang. | 
| Belangrijkste drijf functies | Hier worden de functies weer gegeven van de gegevensset die het meest is gedrijfd en zijn daarom het grootste deel van de metrische meet waarde. Vanwege covariate-verschuiving hoeft de onderliggende verdeling van een functie niet noodzakelijkerwijs te worden aangepast aan een relatief hoog belang rijk onderdeel. |
| Drempelwaarde | Er worden waarschuwingen geactiveerd door de grootte van gegevens drift boven de drempel waarde instellen. Dit kan worden geconfigureerd in de instellingen van de monitor. | 

### <a name="drift-magnitude-trend"></a>Trend van grootte van drift

Bekijk hoe de gegevensset verschilt van de doel gegevensset in de opgegeven tijds periode.  Dichter bij 100%, des te meer gegevens sets verschillen.

:::image type="content" source="media/how-to-monitor-datasets/drift-magnitude.png" alt-text="Trend van grootte van drift":::

### <a name="drift-magnitude-by-features"></a>Grootte van de drift per functie

Deze sectie bevat inzichten op functie niveau in de wijziging in de distributie van de geselecteerde functie, evenals andere statistieken, na verloop van tijd.

De doel gegevensset is ook in de loop van de tijd profileren. De statistische afstand tussen de basislijn distributie van elke functie wordt vergeleken met de tijds duur van de doel gegevensset.  In het algemeen is dit vergelijkbaar met de grootte van de gegevens drift.  Deze statistische afstand geldt echter voor een afzonderlijke functie in plaats van alle functies. Min, Max en gemiddelde zijn ook beschikbaar.

Klik in de Azure Machine Learning Studio op een staaf in de grafiek om de details van het functie niveau voor die datum te bekijken. Standaard ziet u de distributie van de basislijn gegevensset en de distributie van de meest recente uitvoering van hetzelfde onderdeel.

:::image type="content" source="media/how-to-monitor-datasets/drift-by-feature.gif" alt-text="Grootte van de drift per functie":::

Deze metrische gegevens kunnen ook worden opgehaald in de python-SDK via de `get_metrics()` methode voor een `DataDriftDetector` object.

### <a name="feature-details"></a>Details van functie

Schuif ten slotte omlaag om de Details voor elke afzonderlijke functie weer te geven.  Gebruik de vervolg keuzelijsten boven de grafiek om de functie te selecteren en selecteer vervolgens de metrische gegevens die u wilt weer geven.

:::image type="content" source="media/how-to-monitor-datasets/numeric-feature.gif" alt-text="Grafiek en vergelijking van numerieke functies":::

De metrische gegevens in de grafiek zijn afhankelijk van het type functie.

* Numerieke functies

    | Metrisch | Beschrijving |  
    | ------ | ----------- |  
    | Wasserstein afstand | Minimale hoeveelheid werk voor het transformeren van basislijn distributie in de doel verdeling. |
    | Gemiddelde waarde | De gemiddelde waarde van de functie. |
    | Minimumwaarde | De minimum waarde van de functie. |
    | Maximumwaarde | De maximum waarde van de functie. |

* Categorische-functies
    
    | Metrisch | Beschrijving |  
    | ------ | ----------- |  
    | Euclidian afstand     |  Berekend voor categorische-kolommen. De Euclidean-afstand wordt berekend op twee vectoren die zijn gegenereerd op basis van een empirische verdeling van dezelfde categorische-kolom uit twee gegevens sets. 0 geeft aan dat er geen verschil is in de empirische verdelingen.  Hoe meer het is dan 0, hoe meer deze kolom is gedrijfd. Trends kunnen worden waargenomen vanuit een tijd reeks diagram van deze metrische gegevens en kunnen nuttig zijn bij het opsporen van een drijf functie.  |
    | Unieke waarden | Het aantal unieke waarden (kardinaliteit) van de functie. |

Selecteer in dit diagram één datum om de functie distributie tussen het doel en deze datum voor de weer gegeven functie te vergelijken. Voor numerieke functies worden er twee kans-distributies weer gegeven.  Als de functie numeriek is, wordt er een staaf diagram weer gegeven.

:::image type="content" source="media/how-to-monitor-datasets/select-date-to-compare.gif" alt-text="Selecteer een datum om te vergelijken met het doel":::

## <a name="metrics-alerts-and-events"></a>Metrische gegevens, waarschuwingen en gebeurtenissen

U kunt de metrische gegevens opvragen in de [Azure-toepassing Insights](../azure-monitor/app/app-insights-overview.md) -resource die aan uw machine learning-werk ruimte is gekoppeld. U hebt toegang tot alle functies van Application Insights, zoals instellen voor aangepaste waarschuwings regels en actie groepen om een actie te activeren, zoals een E-mail/SMS/push/Voice-of Azure-functie. Raadpleeg de volledige Application Insights-documentatie voor meer informatie. 

Om aan de slag te gaan, gaat u naar de [Azure Portal](https://portal.azure.com) en selecteert u de **overzichts** pagina van uw werk ruimte.  De gekoppelde Application Insights resource bevindt zich uiterst rechts:

[![Overzicht van de Azure Portal](./media/how-to-monitor-datasets/ap-overview.png)](media/how-to-monitor-datasets/ap-overview-expanded.png)

Selecteer Logboeken (analyse) onder bewaking in het linkerdeel venster:

![Overzicht van Application Insights](./media/how-to-monitor-datasets/ai-overview.png)

De metrische gegevens van de gegevensset-monitor worden opgeslagen als `customMetrics` . U kunt een query schrijven en uitvoeren nadat u een controle programma voor gegevensset hebt ingesteld om ze te bekijken:

[![Log Analytics-query](./media/how-to-monitor-datasets/simple-query.png)](media/how-to-monitor-datasets/simple-query-expanded.png)

Nadat u metrische gegevens hebt geïdentificeerd om waarschuwings regels in te stellen, maakt u een nieuwe waarschuwings regel:

![Nieuwe waarschuwingsregel](./media/how-to-monitor-datasets/alert-rule.png)

U kunt een bestaande actie groep gebruiken of een nieuwe maken om de actie te definiëren die moet worden uitgevoerd wanneer aan de voor waarden van de set wordt voldaan:

![Nieuwe actie groep](./media/how-to-monitor-datasets/action-group.png)


## <a name="troubleshooting"></a>Problemen oplossen

Beperkingen en bekende problemen voor gegevens drift-monitors:

* Het tijds bereik voor het analyseren van historische gegevens is beperkt tot 31 intervallen van de frequentie-instelling van de monitor. 
* Beperking van 200-functies, tenzij er geen functie lijst is opgegeven (alle gebruikte functies).
* De reken grootte moet groot genoeg zijn om de gegevens te kunnen verwerken.
* Zorg ervoor dat uw gegevensset gegevens bevat binnen de begin-en eind datum voor een bepaalde monitor uitvoering.
* De monitors van de gegevensset werken alleen voor gegevens sets die 50 rijen of meer bevatten.
* Kolommen, of functies, worden in de gegevensset geclassificeerd als categorische of numeriek op basis van de voor waarden in de volgende tabel. Als de functie niet aan deze voor waarden voldoet, bijvoorbeeld een kolom van het type teken reeks met de unieke waarden van >100, wordt de functie verwijderd uit onze data drift-algoritme, maar is nog steeds profileeerd. 

    | Onderdeel type | Gegevenstype | Voorwaarde | Beperkingen | 
    | ------------ | --------- | --------- | ----------- |
    | Categorische gegevens | teken reeks, BOOL, int, float | Het aantal unieke waarden in de functie is kleiner dan 100 en minder dan 5% van het aantal rijen. | NULL wordt beschouwd als een eigen categorie. | 
    | Cijfer | int, float | De waarden in de functie zijn van een numeriek gegevens type en voldoen niet aan de voor waarde voor een categorische-functie. | De functie is verwijderd als >15% van de waarden null zijn. | 

* Wanneer u een gegevensdrijf monitor hebt gemaakt, maar de gegevens op de pagina **gegevensset** controleren in azure machine learning Studio niet kunt zien, kunt u het volgende proberen.

    1. Controleer of u het juiste datum bereik hebt geselecteerd boven aan de pagina.  
    1. Op het tabblad **gegevensset monitors** selecteert u de koppeling experiment om de uitvoerings status te controleren.  Deze koppeling bevindt zich helemaal rechts in de tabel.
    1. Als de uitvoering is voltooid, controleert u de logboeken van het stuur programma om te zien hoeveel metrische gegevens er zijn gegenereerd of of er waarschuwingen zijn.  Zoek het stuur programma-Logboeken op het tabblad **uitvoer en logboeken** nadat u op een experiment hebt geklikt.

* Als de SDK `backfill()` -functie de verwachte uitvoer niet genereert, kan dit worden veroorzaakt door een verificatie probleem.  Wanneer u de compute maakt om deze functie door te geven, moet u niet gebruiken `Run.get_context().experiment.workspace.compute_targets` .  Gebruik in plaats daarvan [ServicePrincipalAuthentication](/python/api/azureml-core/azureml.core.authentication.serviceprincipalauthentication) zoals de volgende om de compute te maken die u doorgeeft aan die `backfill()` functie: 

  ```python
   auth = ServicePrincipalAuthentication(
          tenant_id=tenant_id,
          service_principal_id=app_id,
          service_principal_password=client_secret
          )
   ws = Workspace.get("xxx", auth=auth, subscription_id="xxx", resource_group="xxx")
   compute = ws.compute_targets.get("xxx")
   ```

* Vanuit de model gegevens verzamelaar kan het tot (maar meestal minder dan) 10 minuten duren voordat gegevens in uw Blob Storage-account arriveren. Wacht in een script of notebook 10 minuten om ervoor te zorgen dat de onderstaande cellen worden uitgevoerd.

    ```python
    import time
    time.sleep(600)
    ```

## <a name="next-steps"></a>Volgende stappen

* Kop naar de [Azure machine learning Studio](https://ml.azure.com) of de [python-notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datadrift-tutorial/datadrift-tutorial.ipynb) voor het instellen van een monitor voor de gegevensset.
* Zie gegevens drift instellen voor [modellen die zijn geïmplementeerd in azure Kubernetes service](./how-to-enable-data-collection.md).
* Stel de gegevensset-drijf monitors in met [Event grid](how-to-use-event-grid.md).