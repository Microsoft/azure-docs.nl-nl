---
title: Gegevensdrift in gegevenssets detecteren (preview)
titleSuffix: Azure Machine Learning
description: Meer informatie over het instellen van detectie van gegevensdrift in Azure Learning. Maak gegevenssetmonitors (preview), controleer op gegevensdrift en stel waarschuwingen in.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: sgilley
ms.author: copeters
author: lostmygithubaccount
ms.date: 06/25/2020
ms.topic: how-to
ms.custom: data4ml, contperf-fy21q2
ms.openlocfilehash: e73b14e24fffacde11e355ae5a4caf0cb76f07ba
ms.sourcegitcommit: 5ce88326f2b02fda54dad05df94cf0b440da284b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107884872"
---
# <a name="detect-data-drift-preview-on-datasets"></a>Gegevensdrift (preview) detecteren in gegevenssets

Meer informatie over het bewaken van gegevensdrift en het instellen van waarschuwingen wanneer de drift hoog is.  

Met Azure Machine Learning monitors voor gegevenssets (preview) kunt u het volgende doen:
* **Analyseer drift in uw gegevens om** te begrijpen hoe deze na een periode veranderen.
* **Modelgegevens controleren op** verschillen tussen het trainen en verwerken van gegevenssets.  Begin met het [verzamelen van modelgegevens van geïmplementeerde modellen.](how-to-enable-data-collection.md)
* **Controleer nieuwe gegevens op** verschillen tussen een basislijn en doelgegevensset.
* **Profielfuncties in gegevens om bij** te houden hoe statistische eigenschappen na een bepaalde periode veranderen.
* **Stel waarschuwingen in voor gegevensdrift** voor vroege waarschuwingen voor potentiële problemen. 
* **[Maak een nieuwe gegevenssetversie](how-to-version-track-datasets.md)** wanneer u bepaalt dat de gegevens te veel zijn gedrift.

Er [wordt een Azure Machine Learning-gegevensset](how-to-create-register-datasets.md) gebruikt om de monitor te maken. De gegevensset moet een tijdstempelkolom bevatten.

U kunt metrische gegevens over gegevensdrift weergeven met de Python-SDK of in Azure Machine Learning-studio.  Andere metrische gegevens en inzichten zijn beschikbaar via [de Azure-toepassing Insights-resource](../azure-monitor/app/app-insights-overview.md) die is gekoppeld aan Azure Machine Learning werkruimte.

> [!IMPORTANT]
> Detectie van gegevensdrift voor gegevenssets is momenteel in openbare preview.
> De preview-versie wordt aangeboden zonder Service Level Agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

## <a name="prerequisites"></a>Vereisten

Als u gegevenssetmonitors wilt maken en hiermee wilt werken, hebt u het volgende nodig:
* Een Azure-abonnement. Als u geen Azure-abonnement hebt, maakt u een gratis account voordat u begint. Probeer vandaag nog de [gratis of betaalde versie van Azure Machine Learning](https://aka.ms/AMLFree).
* Een [Azure Machine Learning werkruimte](how-to-manage-workspace.md).
* De [Azure Machine Learning SDK voor Python geïnstalleerd,](/python/api/overview/azure/ml/install)die het pakket azureml-datasets bevat.
* Gestructureerde (tabellaire) gegevens met een tijdstempel die is opgegeven in het bestandspad, de bestandsnaam of de kolom in de gegevens.

## <a name="what-is-data-drift"></a>Wat is gegevensdrift?

Gegevensdrift is een van de belangrijkste redenen waarom de nauwkeurigheid van het model na verloop van tijd afgenomen.  Voor machine learning modellen is gegevensdrift de wijziging in modelinvoergegevens die leiden tot een verslechtering van de modelprestaties.  Het bewaken van gegevensdrift helpt bij het detecteren van deze prestatieproblemen van het model.

Oorzaken van gegevensdrift zijn onder andere:

- Upstream-proceswijzigingen, zoals een sensor die wordt vervangen, die de meeteenheden wijzigt van inches in centimeters. 
- Problemen met de gegevenskwaliteit, zoals een defecte sensor die altijd 0 leest.
- Natuurlijke afwijking in de gegevens, zoals gemiddelde temperatuurswisselingen met de jaargetijde.
- Wijziging in relatie tussen functies of covariantieverschuiving. 

Azure Machine Learning vereenvoudigt de detectie van driften door één metrische gegevens te berekenen die de complexiteit abstraheert van gegevenssets die worden vergeleken.  Deze gegevenssets kunnen honderden functies en tienduizenden rijen hebben. Zodra er een afwijking is gedetecteerd, zoomt u in op welke functies de afwijking veroorzaken.  Vervolgens inspecteert u metrische gegevens op functieniveau om fouten op te sporen en de hoofdoorzaak van de afwijking te isoleren.

Met deze top-down aanpak kunt u eenvoudig gegevens bewaken in plaats van traditionele technieken op basis van regels. Technieken op basis van regels, zoals het toegestane gegevensbereik of toegestane unieke waarden, kunnen tijdrovend en foutgevoelig zijn.

In Azure Machine Learning gebruikt u gegevenssetmonitors om gegevensdrift te detecteren en te waarschuwen.
  
### <a name="dataset-monitors"></a>Gegevenssetmonitors 

Met een gegevenssetmonitor kunt u het volgende doen:

* Detecteren en waarschuwen voor gegevensdrift op nieuwe gegevens in een gegevensset.
* Historische gegevens analyseren op drift.
* Profileer nieuwe gegevens gedurende een periode.

Het gegevensdriftalgoritme biedt een algemene meting van de wijziging in gegevens en een indicatie van welke functies verantwoordelijk zijn voor verder onderzoek. Gegevenssetmonitors produceren een aantal andere metrische gegevens door nieuwe gegevens in de gegevensset te `timeseries` profileren. 

Aangepaste waarschuwingen kunnen worden ingesteld voor alle metrische gegevens die door de monitor worden gegenereerd [via Azure-toepassing Insights](../azure-monitor/app/app-insights-overview.md). Gegevenssetmonitors kunnen worden gebruikt om snel gegevensproblemen te ondervangen en de tijd te verminderen om het probleem op te sporen door waarschijnlijke oorzaken te identificeren.  

Conceptueel gezien zijn er drie primaire scenario's voor het instellen van gegevenssetmonitors in Azure Machine Learning.

Scenario | Beschrijving
---|---
De gegevens van een model controleren op afwijking van de trainingsgegevens | De resultaten van dit scenario kunnen worden geïnterpreteerd als het controleren van de nauwkeurigheid van een proxy op de nauwkeurigheid van het model, omdat de nauwkeurigheid van het model af neemt wanneer de afdrift van de trainingsgegevens.
Een gegevensset voor tijdreeksen controleren op afwijking van een vorige periode. | Dit scenario is algemener en kan worden gebruikt voor het bewaken van gegevenssets die upstream of downstream van het bouwen van modellen betrokken zijn.  De doel-gegevensset moet een tijdstempelkolom hebben. De basislijn-gegevensset kan elke tabellaire gegevensset zijn die functies heeft die gemeenschappelijk zijn met de doel gegevensset.
Analyses uitvoeren op eerdere gegevens. | Dit scenario kan worden gebruikt om historische gegevens te begrijpen en beslissingen te nemen in instellingen voor gegevenssetmonitors.

Gegevenssetmonitors zijn afhankelijk van de volgende Azure-services.

|Azure-service  |Description  |
|---------|---------|
| *Gegevensset* | Drift maakt gebruik Machine Learning gegevenssets om trainingsgegevens op te halen en gegevens voor modeltraining te vergelijken.  Het genereren van een profiel van gegevens wordt gebruikt voor het genereren van enkele van de gerapporteerde metrische gegevens, zoals min, max, afzonderlijke waarden, aantal afzonderlijke waarden. |
| *Azureml-pijplijn en -rekenkracht* | De driftberekenings job wordt gehost in azureml pipeline.  De taak wordt op aanvraag of volgens een schema geactiveerd om te worden uitgevoerd op een rekenproces dat is geconfigureerd tijdens het maken van de driftmonitor.
| *Application Insights*| Drift stuurt metrische gegevens naar Application Insights behorend bij de machine learning werkruimte.
| *Azure Blob Storage*| Drift stuurt metrische gegevens in json-indeling naar Azure Blob Storage.

### <a name="baseline-and-target-datasets"></a>Basislijn- en doelsets 

U [controleert Azure machine learning gegevenssets op](how-to-create-register-datasets.md) gegevensdrift. Wanneer u een gegevenssetmonitor maakt, verwijst u naar uw:
* Basislijnset: meestal de trainingsset voor een model.
* De doelgegevensset , meestal modelinvoergegevens, wordt in de tijd vergeleken met uw basislijngegevensset. Deze vergelijking betekent dat uw doel gegevensset een tijdstempelkolom moet hebben opgegeven.

De monitor vergelijkt de basislijn- en doelsets.

## <a name="create-target-dataset"></a>Doel gegevensset maken

De doelgegevensset moet de eigenschap instellen door de tijdstempelkolom op te geven vanuit een kolom in de gegevens of een virtuele kolom die is afgeleid van het padpatroon van `timeseries` de bestanden. Maak de gegevensset met een tijdstempel via de [Python SDK](#sdk-dataset) of [Azure Machine Learning-studio](#studio-dataset). Een kolom die een 'tijdstempel' vertegenwoordigt, moet worden opgegeven om eigenschappen aan de `timeseries` gegevensset toe te voegen. Als uw gegevens zijn gepartliceerd in mapstructuur met tijdgegevens, zoals {yyyy/MM/dd}, maakt u een virtuele kolom via de padpatrooninstelling en stelt u deze in als de 'partitietijdstempel' om het belang van tijdreeksfunctionaliteit te verbeteren.

# <a name="python"></a>[Python](#tab/python)
<a name="sdk-dataset"></a>

De [`Dataset`](/python/api/azureml-core/azureml.data.tabulardataset#with-timestamp-columns-timestamp-none--partition-timestamp-none--validate-false----kwargs-) klassemethode [`with_timestamp_columns()`](/python/api/azureml-core/azureml.data.tabulardataset#with-timestamp-columns-timestamp-none--partition-timestamp-none--validate-false----kwargs-)  definieert de tijdstempelkolom voor de gegevensset.

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
> Zie het voorbeeldnoteboek of de SDK-documentatie voor gegevenssets voor een volledig voorbeeld van het gebruik van het kenmerk `timeseries` [van gegevenssets.](/python/api/azureml-core/azureml.data.tabulardataset#with-timestamp-columns-timestamp-none--partition-timestamp-none--validate-false----kwargs-) [](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/timeseries-datasets/tabular-timeseries-dataset-filtering.ipynb)

# <a name="studio"></a>[Studio](#tab/azure-studio)

<a name="studio-dataset"></a>

Als u uw gegevensset maakt met behulp van Azure Machine Learning-studio, moet u ervoor zorgen dat het pad naar uw gegevens tijdstempelgegevens bevat, alle submappen met gegevens bevat en de partitieindeling in stelt.

In het volgende voorbeeld worden alle gegevens onder de submap *NoaaIsdFlorida/2019* genomen en geeft de partitieindeling het jaar, de maand en de dag van de tijdstempel op.

[![Partitie-indeling](./media/how-to-monitor-datasets/partition-format.png)](media/how-to-monitor-datasets/partition-format-expand.png)

Geef in **de schema-instellingen** de tijdstempelkolom op uit een virtuele of echte kolom in de opgegeven gegevensset:

:::image type="content" source="media/how-to-monitor-datasets/timestamp.png" alt-text="De tijdstempel instellen":::

Als uw gegevens zijn gepart partitioneerd op datum, zoals hier het geval is, kunt u ook de partition_timestamp.  Hierdoor kunnen datums efficiënter worden verwerkt.

:::image type="content" source="media/how-to-monitor-datasets/timeseries-partitiontimestamp.png" alt-text="Tijdstempel van partitie":::

---

## <a name="create-dataset-monitor"></a>Gegevenssetmonitor maken

Maak een gegevenssetmonitor om gegevensdrift in een nieuwe gegevensset te detecteren en te waarschuwen voor gegevensdrift.  Gebruik de [Python SDK of](#sdk-monitor) [Azure Machine Learning-studio](#studio-monitor).

# <a name="python"></a>[Python](#tab/python)
<a name="sdk-monitor"></a> Zie de [Python SDK-referentiedocumentatie over gegevensdrift](/python/api/azureml-datadrift/azureml.datadrift) voor meer informatie. 

In het volgende voorbeeld ziet u hoe u een gegevenssetmonitor maakt met behulp van de Python SDK

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
> Zie ons voorbeeldnoteboek voor een volledig voorbeeld van het instellen van een gegevensset en `timeseries` [gegevensdriftdetector.](https://aka.ms/datadrift-notebook)


# <a name="studio"></a>[Studio](#tab/azure-studio)
<a name="studio-monitor"></a>

1. Navigeer naar [de startpagina van de studio.](https://ml.azure.com)
1. Selecteer het **tabblad Gegevenssets** aan de linkerkant. 
1. Selecteer **Gegevenssetmonitors.**
   ![Monitorlijst](./media/how-to-monitor-datasets/monitor-list.png)

1. Klik op de **knop +Monitor maken** en ga door met de wizard door te klikken op **Volgende.**  

:::image type="content" source="media/how-to-monitor-datasets/wizard.png" alt-text="Een monitorwizard maken":::

* **Selecteer doel gegevensset**.  De doelgegevensset is een gegevensset in tabelvorm met een opgegeven tijdstempelkolom die wordt geanalyseerd op gegevensdrift. De doelgegevensset moet functies hebben die gemeenschappelijk zijn met de basislijngegevensset en moet een gegevensset zijn `timeseries` waaraan nieuwe gegevens worden toegevoegd. Historische gegevens in de doelgegevensset kunnen worden geanalyseerd of nieuwe gegevens kunnen worden bewaakt.

* **Selecteer basislijnset.**  Selecteer de tabellaire gegevensset die moet worden gebruikt als basislijn voor vergelijking van de doelset gedurende een bepaalde periode.  De basislijnset moet functies hebben die gemeenschappelijk zijn met de doelset.  Selecteer een tijdsbereik om een segment van de doelset te gebruiken of geef een afzonderlijke gegevensset op die moet worden gebruikt als basislijn.

* **Controleer de instellingen.**  Deze instellingen zijn voor de pijplijn voor geplande gegevenssetmonitor, die wordt gemaakt. 

    | Instelling | Beschrijving | Tips | Veranderlijk | 
    | ------- | ----------- | ---- | ------- |
    | Name | Naam van de gegevenssetmonitor. | | No |
    | Functies | Lijst met functies die worden geanalyseerd op gegevensdrift in de tijd. | Ingesteld op de uitvoerfunctie(s) van een model om conceptdrift te meten. Neem geen functies op die op natuurlijke wijze in de tijd (maand, jaar, index, enzovoort) zijn gedrift. U kunt backfillen en bestaande gegevensdriftmonitor na het aanpassen van de lijst met functies. | Yes | 
    | Rekendoel | Azure Machine Learning rekendoel om de gegevenssetmonitortaken uit te voeren. | | Yes | 
    | Inschakelen | De planning voor de pijplijn voor gegevenssetmonitor in- of uitschakelen | Schakel de planning voor het analyseren van historische gegevens uit met de instelling backfill. Deze kan worden ingeschakeld nadat de gegevenssetmonitor is gemaakt. | Yes | 
    | Frequentie | De frequentie die wordt gebruikt voor het plannen van de pijplijn-taak en het analyseren van historische gegevens bij het uitvoeren van een backfill. Opties zijn onder andere dagelijks, wekelijks of maandelijks. | Bij elke run worden gegevens in de doelgegevensset vergeleken op basis van de frequentie: <li>Dagelijks: De meest recente volledige dag in de doelset vergelijken met de basislijn <li>Wekelijks: De meest recente volledige week (maandag - zondag) vergelijken in de doelset met de basislijn <li>Maandelijks: De meest recente volledige maand in de doelset vergelijken met de basislijn | No | 
    | Latentie | Het duurt uren voordat gegevens in de gegevensset binnenkomen. Als het bijvoorbeeld drie dagen duurt voordat gegevens in de SQL-database binnenkomen die de gegevensset bevat, stelt u de latentie in op 72. | Kan niet worden gewijzigd nadat de gegevenssetmonitor is gemaakt | No | 
    | E-mailadressen | E-mailadressen voor waarschuwingen op basis van schending van de drempelwaarde voor gegevensdriftpercentage. | E-mailberichten worden verzonden via Azure Monitor. | Yes | 
    | Drempelwaarde | Drempelwaarde voor gegevensdriftpercentage voor e-mailwaarschuwingen. | Verdere waarschuwingen en gebeurtenissen kunnen worden ingesteld voor veel andere metrische gegevens in de gekoppelde resource van Application Insights werkruimte. | Yes |

Nadat de wizard is voltooien, wordt de resulterende gegevenssetmonitor weergegeven in de lijst. Selecteer deze optie om naar de detailpagina van die monitor te gaan.

---

## <a name="understand-data-drift-results"></a>Gegevensdriftresultaten begrijpen

In deze sectie ziet u de resultaten van het bewaken van een gegevensset, te vinden op de pagina **Gegevenssets-gegevenssetmonitors**  /   in Azure Studio.  U kunt de instellingen bijwerken en bestaande gegevens analyseren voor een specifieke periode op deze pagina.  

Begin met de inzichten op het hoogste niveau in de omvang van gegevensdrift en een markering van functies die verder moeten worden onderzocht.

:::image type="content" source="media/how-to-monitor-datasets/drift-overview.png" alt-text="Drift-overzicht":::


| Metrisch | Beschrijving | 
| ------ | ----------- | 
| Magnitude van gegevensdrift | Een afwijkingspercentage tussen de basislijn en de doelset gedurende een bepaalde periode. Tussen 0 en 100 geeft 0 identieke gegevenssets aan en 100 geeft aan dat het Azure Machine Learning gegevensdriftmodel de twee gegevenssets volledig kan onderscheiden. Ruis in het precieze percentage dat wordt gemeten, wordt verwacht vanwege machine learning technieken die worden gebruikt om deze magnitude te genereren. | 
| Belangrijkste driftfuncties | Toont de functies van de gegevensset die het meest zijn gedrift en daarom het meeste bijdragen aan de metrische gegevens van Drift Magnitude. Als gevolg van covariantieverschuiving hoeft de onderliggende distributie van een functie niet noodzakelijkerwijs te worden gewijzigd om een relatief hoog functie-belang te hebben. |
| Drempelwaarde | Als de grootte van de gegevensdrift de drempelwaarde overschrijdt, worden er waarschuwingen getriggerd. Dit kan worden geconfigureerd in de monitorinstellingen. | 

### <a name="drift-magnitude-trend"></a>Trend in magnitude van drift

Bekijk hoe de gegevensset verschilt van de doelset in de opgegeven periode.  Hoe dichter bij 100%, hoe meer de twee gegevenssets verschillen.

:::image type="content" source="media/how-to-monitor-datasets/drift-magnitude.png" alt-text="Trend in magnitude van drift":::

### <a name="drift-magnitude-by-features"></a>Afwijkings magnitude op kenmerken

Deze sectie bevat inzichten op functieniveau in de wijziging in de distributie van de geselecteerde functie, evenals andere statistieken, in de tijd.

De doelgegevensset wordt ook geprofileerd in de tijd. De statistische afstand tussen de basislijndistributie van elke functie wordt vergeleken met die van de doelset in de afgelopen periode.  Conceptueel gezien is dit vergelijkbaar met de magnitude van de gegevensdrift.  Deze statistische afstand is echter voor een afzonderlijke functie in plaats van alle functies. Min, max en mean zijn ook beschikbaar.

Klik in Azure Machine Learning-studio op een balk in de grafiek om de details op functieniveau voor die datum weer te geven. Standaard ziet u de distributie van de basislijnset en de distributie van de meest recente run van dezelfde functie.

:::image type="content" source="media/how-to-monitor-datasets/drift-by-feature.gif" alt-text="Afwijkings magnitude op functies":::

Deze metrische gegevens kunnen ook worden opgehaald in de Python-SDK via de `get_metrics()` methode voor een `DataDriftDetector` -object.

### <a name="feature-details"></a>Functiedetails

Schuif tot slot omlaag om details voor elke afzonderlijke functie weer te geven.  Gebruik de vervolgkeuzelijsten boven de grafiek om de functie te selecteren en selecteer daarnaast de metrische gegevens die u wilt weergeven.

:::image type="content" source="media/how-to-monitor-datasets/numeric-feature.gif" alt-text="Numerieke functiegrafiek en vergelijking":::

Metrische gegevens in de grafiek zijn afhankelijk van het type functie.

* Numerieke functies

    | Metrisch | Beschrijving |  
    | ------ | ----------- |  
    | Wasserstein-afstand | Minimale hoeveelheid werk voor het transformeren van basislijndistributie naar de doeldistributie. |
    | Gemiddelde waarde | Gemiddelde waarde van de functie. |
    | Minimumwaarde | Minimale waarde van de functie. |
    | Maximumwaarde | Maximale waarde van de functie. |

* Categorische functies
    
    | Metrisch | Beschrijving |  
    | ------ | ----------- |  
    | Euclidische afstand     |  Berekend voor categorische kolommen. Euclidische afstand wordt berekend op twee vectoren, gegenereerd op basis van empirische verdeling van dezelfde categorische kolom uit twee gegevenssets. 0 geeft aan dat er geen verschil is in de empirische verdelingen.  Hoe meer deze afwijkt van 0, des te meer deze kolom is afgedrift. Trends kunnen worden waargenomen op grond van een tijdreeksplot van deze metrische gegevens en kunnen nuttig zijn bij het blootleggen van een drifting-functie.  |
    | Unieke waarden | Het aantal unieke waarden (kardinaliteit) van de functie. |

Selecteer in deze grafiek één datum om de functiedistributie tussen het doel en deze datum voor de weergegeven functie te vergelijken. Voor numerieke kenmerken toont dit twee waarschijnlijkheidsdistributies.  Als de functie numeriek is, wordt er een staafdiagram weergegeven.

:::image type="content" source="media/how-to-monitor-datasets/select-date-to-compare.gif" alt-text="Selecteer een datum om te vergelijken met het doel":::

## <a name="metrics-alerts-and-events"></a>Metrische gegevens, waarschuwingen en gebeurtenissen

Metrische gegevens kunnen worden opgevraagd in [de Azure-toepassing Insights-resource](../azure-monitor/app/app-insights-overview.md) die is gekoppeld aan uw machine learning werkruimte. U hebt toegang tot alle functies van Application Insights instellen voor aangepaste waarschuwingsregels en actiegroepen om een actie te activeren, zoals een e-mail/sms/push/spraak of Azure-functie. Raadpleeg de volledige documentatie Application Insights voor meer informatie. 

Als u wilt beginnen, gaat u naar [Azure Portal](https://portal.azure.com) en selecteert u de **pagina Overzicht van uw** werkruimte.  De gekoppelde Application Insights resource staat aan de rechterkant:

[![Overzicht van de Azure Portal](./media/how-to-monitor-datasets/ap-overview.png)](media/how-to-monitor-datasets/ap-overview-expanded.png)

Selecteer Logboeken (Analyse) onder Bewaking in het linkerdeelvenster:

![Overzicht van Application Insights](./media/how-to-monitor-datasets/ai-overview.png)

De metrische gegevens van de gegevenssetmonitor worden opgeslagen als `customMetrics` . U kunt een query schrijven en uitvoeren na het instellen van een gegevenssetmonitor om deze weer te geven:

[![Log Analytics-query](./media/how-to-monitor-datasets/simple-query.png)](media/how-to-monitor-datasets/simple-query-expanded.png)

Nadat u metrische gegevens voor het instellen van waarschuwingsregels heeft vastgesteld, maakt u een nieuwe waarschuwingsregel:

![Nieuwe waarschuwingsregel](./media/how-to-monitor-datasets/alert-rule.png)

U kunt een bestaande actiegroep gebruiken of een nieuwe maken om de actie te definiëren die moet worden ondernomen wanneer aan de voorwaarden van de set wordt voldaan:

![Nieuwe actiegroep](./media/how-to-monitor-datasets/action-group.png)


## <a name="troubleshooting"></a>Problemen oplossen

Beperkingen en bekende problemen voor monitors voor gegevensdrift:

* Het tijdsbereik bij het analyseren van historische gegevens is beperkt tot 31 intervallen van de frequentie-instelling van de monitor. 
* Beperking van 200 functies, tenzij er geen functielijst is opgegeven (alle functies die worden gebruikt).
* De rekenkracht moet groot genoeg zijn om de gegevens te verwerken.
* Zorg ervoor dat uw gegevensset gegevens bevat binnen de begin- en einddatum voor een bepaalde monitoruitdaging.
* Gegevenssetmonitors werken alleen voor gegevenssets met 50 rijen of meer.
* Kolommen of functies in de gegevensset worden geclassificeerd als categorisch of numeriek op basis van de voorwaarden in de volgende tabel. Als de functie niet aan deze voorwaarden voldoet, bijvoorbeeld een kolom van het type tekenreeks met >100 unieke waarden, wordt de functie uit ons gegevensdriftalgoritme verdwenen, maar nog steeds geprofileerd. 

    | Functietype | Gegevenstype | Voorwaarde | Beperkingen | 
    | ------------ | --------- | --------- | ----------- |
    | Categorische gegevens | string, bool, int, float | Het aantal unieke waarden in de functie is minder dan 100 en minder dan 5% van het aantal rijen. | Null wordt beschouwd als een eigen categorie. | 
    | Numerieke | int, float | De waarden in de functie zijn van een numeriek gegevenstype en voldoen niet aan de voorwaarde voor een categorische functie. | De functie is >15% van de waarden null is. | 

* Wanneer u een gegevensdriftmonitor hebt  gemaakt, maar geen gegevens kunt zien op de pagina Gegevenssetmonitors in Azure Machine Learning-studio, probeert u het volgende.

    1. Controleer of u het juiste datumbereik bovenaan de pagina hebt geselecteerd.  
    1. Selecteer op **het tabblad Gegevenssetmonitors** de koppeling experiment om de status van de run te controleren.  Deze koppeling staat aan de rechterkant van de tabel.
    1. Als de run is voltooid, controleert u de logboeken van het stuurprogramma om te zien hoeveel metrische gegevens zijn gegenereerd of of er waarschuwingsberichten zijn.  Zoek stuurprogrammalogboeken op **het tabblad Uitvoer en logboeken** nadat u op een experiment hebt geklikt.

* Als de `backfill()` SDK-functie niet de verwachte uitvoer genereert, kan dit worden veroorzaakt door een verificatieprobleem.  Wanneer u de berekening maakt om door te geven aan deze functie, gebruikt u niet `Run.get_context().experiment.workspace.compute_targets` .  Gebruik in plaats [daarvan ServicePrincipalAuthentication,](/python/api/azureml-core/azureml.core.authentication.serviceprincipalauthentication) zoals het volgende, om de rekenkracht te maken die u aan die functie `backfill()` doorwerkt: 

  ```python
   auth = ServicePrincipalAuthentication(
          tenant_id=tenant_id,
          service_principal_id=app_id,
          service_principal_password=client_secret
          )
   ws = Workspace.get("xxx", auth=auth, subscription_id="xxx", resource_group="xxx")
   compute = ws.compute_targets.get("xxx")
   ```

* Vanuit de modelgegevensverzamelaar kan het tot (maar meestal minder dan) 10 minuten duren voordat gegevens in uw Blob Storage-account binnenkomen. Wacht in een script of notebook tien minuten om ervoor te zorgen dat de onderstaande cellen worden uitgevoerd.

    ```python
    import time
    time.sleep(600)
    ```

## <a name="next-steps"></a>Volgende stappen

* Ga naar de [Azure Machine Learning-studio](https://ml.azure.com) of het [Python-notebook om](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datadrift-tutorial/datadrift-tutorial.ipynb) een gegevenssetmonitor in te stellen.
* Zie gegevensdrift instellen voor [modellen die zijn geïmplementeerd in Azure Kubernetes Service](./how-to-enable-data-collection.md).
* Stel gegevenssetdriftmonitors in [met event grid](how-to-use-event-grid.md).
