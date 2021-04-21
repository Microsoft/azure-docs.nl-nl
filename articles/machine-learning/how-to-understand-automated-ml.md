---
title: Resultaten van AutoML-experiment evalueren
titleSuffix: Azure Machine Learning
description: Meer informatie over het weergeven en evalueren van grafieken en metrische gegevens voor elk van uw geautomatiseerde machine learning experiment runs.
services: machine-learning
author: gregorybchris
ms.author: chgrego
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.date: 12/09/2020
ms.topic: conceptual
ms.custom: how-to, contperf-fy21q2, automl
ms.openlocfilehash: 2bed95385823a167c7a31eed11d752894984ea38
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107791873"
---
# <a name="evaluate-automated-machine-learning-experiment-results"></a>Resultaten van geautomatiseerde machine learning evalueren

In dit artikel leert u hoe u modellen evalueert en vergelijkt die zijn getraind met uw geautomatiseerde machine learning (geautomatiseerde ML). In de loop van een geautomatiseerd ML-experiment worden veel uitvoeringen gemaakt en wordt met elke uitvoering een model gemaakt. Voor elk model genereert geautomatiseerde ML metrische evaluatiegegevens en grafieken die u helpen de prestaties van het model te meten. 

Geautomatiseerde ML genereert bijvoorbeeld de volgende grafieken op basis van het type experiment.

| Classificatie| Regressie/prognose |
| ----------------------------------------------------------- | ---------------------------------------- |
| [Verwarringsmatrix](#confusion-matrix)                       | [Residuenhistogram](#residuals)        |
| [ROC-curve (Receiver Operating Characteristic)](#roc-curve) | [Voorspeld versus waar](#predicted-vs-true) |
| [Curve voor precisie-terughalen (PR)](#precision-recall-curve)      |                                          |
| [Lift-curve](#lift-curve)                                   |                                          |
| [Cumulatieve winstcurve](#cumulative-gains-curve)           |                                          |
| [Kalibratiekromme](#calibration-curve)                     |                     


## <a name="prerequisites"></a>Vereisten

- Een Azure-abonnement. (Als u nog geen abonnement op Azure hebt, maak [dan een gratis account aan](https://aka.ms/AMLFree) voordat u begint)
- Een Azure Machine Learning experiment gemaakt met een van de volgende:
  - De [Azure Machine Learning-studio](how-to-use-automated-ml-for-ml-models.md) (geen code vereist)
  - De [Azure Machine Learning Python SDK](how-to-configure-auto-train.md)

## <a name="view-run-results"></a>Resultaten van de run weergeven

Nadat uw geautomatiseerde ML-experiment is voltooid, kunt u een geschiedenis van de runs vinden via:
  - Een browser met [Azure Machine Learning-studio](overview-what-is-machine-learning-studio.md)
  - Een Jupyter-notebook met behulp [van de RunDetails Jupyter-widget](/python/api/azureml-widgets/azureml.widgets.rundetails)

In de volgende stappen en video ziet u hoe u de metrische gegevens en grafieken van de uitvoeringsgeschiedenis en modelevaluatie in de studio kunt bekijken:

1. [Meld u aan bij de studio](https://ml.azure.com/) en navigeer naar uw werkruimte.
1. Selecteer Experimenten in het menu **links.**
1. Selecteer uw experiment in de lijst met experimenten.
1. Selecteer in de tabel onder aan de pagina een geautomatiseerde ML-run.
1. Selecteer op **het** tabblad Modellen de **algoritmenaam** voor het model dat u wilt evalueren.
1. Gebruik op **het** tabblad Metrische gegevens de selectievakjes aan de linkerkant om metrische gegevens en grafieken weer te geven.

![Stappen voor het weergeven van metrische gegevens in Studio](./media/how-to-understand-automated-ml/how-to-studio-metrics.gif)

## <a name="classification-metrics"></a>Metrische classificatiegegevens

Geautomatiseerde ML berekent prestatiemetrieken voor elk classificatiemodel dat voor uw experiment wordt gegenereerd. Deze metrische gegevens zijn gebaseerd op de scikit learn-implementatie. 

Veel metrische classificatiegegevens worden gedefinieerd voor binaire classificatie voor twee klassen en vereisen gemiddelde van klassen om één score te produceren voor classificatie met meerdere klassen. Scikit-learn biedt verschillende gemiddelden, waarvan drie geautomatiseerde ML wordt gebruikt: **macro,** **micro** en **gewogen**.

- **Macro:** de metrische gegevens voor elke klasse berekenen en het ongewicht gemiddelde nemen
- **Micro:** bereken de metrische gegevens globaal door het totale aantal werkelijke positieven, fout-negatieven en fout-positieven (onafhankelijk van klassen) te tellen.
- **Gewogen:** bereken de metrische gegevens voor elke klasse en neem het gewogen gemiddelde op basis van het aantal steekproeven per klasse.

Hoewel elke gemiddeldemethode voordelen heeft, is klasse-onevenwichtigheid een veelvoorkomende overweging bij het selecteren van de juiste methode. Als klassen verschillende aantallen steekproeven hebben, kan het meer informatief zijn om een macrogemiddelde te gebruiken waarbij klassen van klassen die klassen zijn die gelijk zijn aan de meerderheidsklassen krijgen. Meer informatie over binaire versus [multiklasse metrische gegevens in geautomatiseerde ML](#binary-vs-multiclass-classification-metrics). 

De volgende tabel bevat een overzicht van de metrische gegevens over modelprestaties die door geautomatiseerde ML worden berekend voor elk classificatiemodel dat voor uw experiment wordt gegenereerd. Zie de scikit-learn-documentatie die is gekoppeld in het **veld Berekening** van elke metrische gegevens voor meer informatie. 

|Metrisch|Beschrijving|Berekening|
|--|--|---|
|AUC | AUC is het gebied onder de [Receiver Operating Characteristic Curve](#roc-curve).<br><br> **Doel:** Hoe dichter bij 1, hoe beter <br> **Bereik:** [0, 1]<br> <br>Ondersteunde namen van metrische gegevens zijn onder andere: <li>`AUC_macro`, het rekenkundige gemiddelde van de AUC voor elke klasse.<li> `AUC_micro`, berekend door de echte positieven en fout-positieven uit elke klasse te combineren. <li> `AUC_weighted`, rekenkundig gemiddelde van de score voor elke klasse, gewogen op het aantal werkelijke exemplaren in elke klasse.<br><br>Opmerking: De AUC-waarden die door geautomatiseerde ML worden gerapporteerd, komen mogelijk niet overeen met de ROC-grafiek als er slechts twee klassen zijn. Voor binaire classificatie wordt de onderliggende scikit-learn-implementatie van AUC niet daadwerkelijk toegepast op macro/micro/gewogen gemiddelde. In plaats daarvan wordt de AUC van de meest waarschijnlijke positieve klasse geretourneerd. De ROC-grafiek blijft het gemiddelde voor klassen toepassen op binaire classificatie, net zoals bij meerdere klassen.  |[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | 
|accuracy| Nauwkeurigheid is de verhouding van voorspellingen die exact overeenkomen met de werkelijke klasselabels. <br> <br>**Doel:** Hoe dichter bij 1, hoe beter <br> **Bereik:** [0, 1]|[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html)|
|average_precision|De gemiddelde precisie geeft een overzicht van een curve voor precisie-terughalen als het gewogen gemiddelde van precisies dat bij elke drempelwaarde wordt bereikt, met de toename van het terughalen van de vorige drempelwaarde als het gewicht. <br><br> **Doel:** Hoe dichter bij 1, hoe beter <br> **Bereik:** [0, 1]<br> <br>Ondersteunde namen van metrische gegevens zijn:<li>`average_precision_score_macro`, het rekenkundige gemiddelde van de gemiddelde precisiescore van elke klasse.<li> `average_precision_score_micro`, berekend door de werkelijke positieven en fout-positieven bij elke cutoff te combineren.<li>`average_precision_score_weighted`, het rekenkundige gemiddelde van de gemiddelde precisiescore voor elke klasse, gewogen op het aantal werkelijke exemplaren in elke klasse.|[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|
balanced_accuracy|Evenwichtige nauwkeurigheid is het rekenkundige gemiddelde van terughalen voor elke klasse.<br> <br>**Doelstelling:** Hoe dichter bij 1, hoe beter <br> **Bereik:** [0, 1]|[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|
f1_score|F1-score is het gemiddelde van precisie en terughalen. Het is een goede evenwichtige meting van zowel fout-positieven als fout-negatieven. Er wordt echter geen rekening gehouden met echte negatieven. <br> <br>**Doelstelling:** Hoe dichter bij 1, hoe beter <br> **Bereik:** [0, 1]<br> <br>Ondersteunde namen van metrische gegevens zijn onder andere:<li>  `f1_score_macro`: het rekenkundige gemiddelde van de F1-score voor elke klasse. <li> `f1_score_micro`: berekend door het totale aantal echte positieven, fout-negatieven en fout-positieven te tellen. <li> `f1_score_weighted`: gewogen gemiddelde met de klassefrequentie van de F1-score voor elke klasse.|[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|
log_loss|Dit is de verliesfunctie die wordt gebruikt in (multinomiale) logistieke regressie en uitbreidingen ervan, zoals neurale netwerken, gedefinieerd als de negatieve log-waarschijnlijkheid van de werkelijke labels op basis van de voorspellingen van een probabilistische classificatie. <br><br> **Doelstelling:** Hoe dichter bij 0, hoe beter <br> **Bereik:** [0, inf)|[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.log_loss.html)|
norm_macro_recall| Genormaliseerde macro-terugroepen zijn macrogemiddelden en genormaliseerd, zodat willekeurige prestaties een score van 0 hebben en perfecte prestaties een score van 1 hebben. <br> <br>**Doel:** Hoe dichter bij 1, hoe beter <br> **Bereik:** [0, 1] |`(recall_score_macro - R)`&nbsp;/&nbsp;`(1 - R)` <br><br>waarbij `R` de verwachte waarde van is `recall_score_macro` voor willekeurige voorspellingen.<br><br>`R = 0.5`&nbsp;voor &nbsp; binaire &nbsp; classificatie. <br>`R = (1 / C)` voor classificatieproblemen met C-klassen.|
matthews_correlation | De correlatiecoëfficiënt van Matthews is een evenwichtige meting van nauwkeurigheid, die zelfs kan worden gebruikt als de ene klasse veel meer steekproeven heeft dan de andere. Een coëfficiënt van 1 geeft perfecte voorspelling, 0 willekeurige voorspelling en -1 inverse voorspelling aan.<br><br> **Doel:** Hoe dichter bij 1, hoe beter <br> **Bereik:** [-1, 1]|[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.matthews_corrcoef.html)|
precisie|Precisie is het vermogen van een model om te voorkomen dat negatieve steekproeven als positief worden gelabeld. <br><br> **Doel:** Hoe dichter bij 1, hoe beter <br> **Bereik:** [0, 1]<br> <br>Ondersteunde namen van metrische gegevens zijn onder andere: <li> `precision_score_macro`, het rekenkundige gemiddelde van precisie voor elke klasse. <li> `precision_score_micro`, wereldwijd berekend door het totale aantal echte positieven en fout-positieven te tellen. <li> `precision_score_weighted`, het rekenkundige gemiddelde van precisie voor elke klasse, gewogen op het aantal werkelijke exemplaren in elke klasse.|[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|
relevante overeenkomsten| Zoals u zich herinnert, is het vermogen van een model om alle positieve steekproeven te detecteren. <br><br> **Doel:** Hoe dichter bij 1, hoe beter <br> **Bereik:** [0, 1]<br> <br>Ondersteunde namen van metrische gegevens zijn onder andere: <li>`recall_score_macro`: het rekenkundige gemiddelde van de terugroep voor elke klasse. <li> `recall_score_micro`: wereldwijd berekend door het totale aantal echte positieven, fout-negatieven en fout-positieven te tellen.<li> `recall_score_weighted`: het rekenkundige gemiddelde van de terugroep voor elke klasse, gewogen op het aantal werkelijke exemplaren in elke klasse.|[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|
weighted_accuracy|Gewogen nauwkeurigheid is nauwkeurigheid waarbij elke steekproef wordt gewogen op het totale aantal steekproeven dat tot dezelfde klasse behoort. <br><br>**Doel:** Hoe dichter bij 1, hoe beter <br>**Bereik:** [0, 1]|[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html)|

### <a name="binary-vs-multiclass-classification-metrics"></a>Binaire versus metrische classificatiemetrieken voor meerdere klassen

Geautomatiseerde ML maakt geen onderscheid tussen binaire en metrische gegevens van meerdere klasses. Dezelfde metrische validatiegegevens worden gerapporteerd, ongeacht of een gegevensset twee klassen of meer dan twee klassen heeft. Sommige metrische gegevens zijn echter bedoeld voor classificatie met meerdere klassen. Wanneer deze metrische gegevens worden toegepast op een binaire gegevensset, worden deze klassen niet behandeld als de `true` klasse, zoals u zou verwachten. Metrische gegevens die duidelijk zijn bedoeld voor meerdere klasses, krijgen het achtervoegsel `micro` `macro` , of `weighted` . Voorbeelden zijn `average_precision_score` , , , en `f1_score` `precision_score` `recall_score` `AUC` .

In plaats van recall te berekenen als , wordt bijvoorbeeld het gemiddelde van de meervoudige klasse recall ( , of ) berekend voor beide klassen van een binaire `tp / (tp + fn)` `micro` `macro` `weighted` classificatie-gegevensset. Dit komt overeen met het berekenen van de terugroep voor de klasse en de klasse afzonderlijk en vervolgens het gemiddelde van `true` `false` de twee nemen.

Geautomatiseerde ML berekent geen binaire metrische gegevens, dat wil zeggen metrische gegevens voor gegevenssets met binaire classificatie. Deze metrische gegevens kunnen echter handmatig worden berekend met behulp van de [verwarringsmatrix](#confusion-matrix) die Geautomatiseerde ML heeft gegenereerd voor die specifieke run. U kunt bijvoorbeeld de precisie, , berekenen met de werkelijke positieve en fout-positieve waarden die worden weergegeven in een `tp / (tp + fp)` 2x2-verwarringsmatrixdiagram.

## <a name="confusion-matrix"></a>Verwarringsmatrix

Verwarring matrices bieden een visual voor hoe een machine learning model systematische fouten maakt in de voorspellingen voor classificatiemodellen. Het woord 'verwarring' in de naam is afkomstig van een model dat 'verwarrend' of 'verkeerd labelen' is. Een cel op rij en kolom in een verwarringsmatrix bevat het aantal voorbeelden in de evaluatie-gegevensset die deel uitmaken van klasse en die door het model zijn geclassificeerd `i` `j` als klasse `C_i` `C_j` .

In de studio geeft een donkere cel een hoger aantal steekproeven aan. Als **u genormaliseerde** weergave selecteert in de vervolgkeuzerij, wordt deze genormaliseerd voor elke matrixrij om het percentage weer te geven van de klasse die wordt voorspeld `C_i` als klasse `C_j` . Het voordeel van  de standaardweergave Onbewerkt is dat u kunt zien of onevenwichtigheid in de distributie van werkelijke klassen ervoor heeft gezorgd dat het model steekproeven uit de klasse class niet goed classificeert, een veelvoorkomende probleem bij onevenwichtige gegevenssets.

De verwarringsmatrix van een goed model heeft de meeste steekproeven langs de diagonaal.

### <a name="confusion-matrix-for-a-good-model"></a>Verwarringsmatrix voor een goed model 
![Verwarringsmatrix voor een goed model ](./media/how-to-understand-automated-ml/chart-confusion-matrix-good.png)

### <a name="confusion-matrix-for-a-bad-model"></a>Verwarringsmatrix voor een slecht model
![Verwarringsmatrix voor een slecht model](./media/how-to-understand-automated-ml/chart-confusion-matrix-bad.png)

## <a name="roc-curve"></a>ROC-curve

De ROC-curve (Receiver Operating Characteristic) plot de relatie tussen het werkelijke positieve percentage (TPR) en het fout-positieve percentage (FPR) wanneer de beslissingsdrempel wordt gewijzigd. De ROC-curve kan minder informatief zijn bij het trainen van modellen op gegevenssets met een hoge klasse-onevenwichtigheid, omdat de meerderheidsklasse bijdragen van de klassen kan uitsommen.

Het gebied onder de curve (AUC) kan worden geïnterpreteerd als het aandeel correct geclassificeerde steekproeven. Om precies te zijn, is de AUC de waarschijnlijkheid dat de classificatie een willekeurig gekozen positieve steekproef hoger rangschikt dan een willekeurig gekozen negatieve steekproef. De vorm van de curve geeft een idee van de relatie tussen TPR en FPR als een functie van de classificatiedrempel of beslissingsgrens.

Een curve die de linkerbovenhoek van het diagram nadert, benadert een 100% TPR en 0% FPR, het best mogelijke model. Een willekeurig model produceert een ROC-curve langs de `y = x` lijn van de linkeronderhoek naar de rechterbovenhoek. Een slechter dan willekeurig model zou een ROC-curve hebben die onder de lijn `y = x` dipt.
> [!TIP]
> Voor classificatieexperimenten kan elk van de lijndiagrammen die worden geproduceerd voor geautomatiseerde ML-modellen worden gebruikt om het model per klasse te evalueren of een gemiddelde te geven over alle klassen. U kunt schakelen tussen deze verschillende weergaven door te klikken op klasselabels in de legenda rechts van de grafiek.

### <a name="roc-curve-for-a-good-model"></a>ROC-curve voor een goed model
![ROC-curve voor een goed model](./media/how-to-understand-automated-ml/chart-roc-curve-good.png)

### <a name="roc-curve-for-a-bad-model"></a>ROC-curve voor een slecht model
![ROC-curve voor een slecht model](./media/how-to-understand-automated-ml/chart-roc-curve-bad.png)

## <a name="precision-recall-curve"></a>Curve voor precisie-terughalen

De curve voor precisie-terughalen plot de relatie tussen precisie en terughalen wanneer de beslissingsdrempel verandert. Terughalen is het vermogen van een model om alle positieve steekproeven te detecteren en precisie is het vermogen van een model om te voorkomen dat negatieve steekproeven als positief worden gelabeld. Voor sommige zakelijke problemen is mogelijk een hogere terugroep en een hogere precisie vereist, afhankelijk van het relatieve belang van het vermijden van fout-negatieven versus fout-positieven.
> [!TIP]
> Voor classificatieexperimenten kan elk van de lijndiagrammen die worden geproduceerd voor geautomatiseerde ML-modellen worden gebruikt om het model per klasse te evalueren of om het gemiddelde te krijgen over alle klassen. U kunt schakelen tussen deze verschillende weergaven door te klikken op klasselabels in de legenda rechts van de grafiek.
### <a name="precision-recall-curve-for-a-good-model"></a>Curve voor precisie-terughalen voor een goed model
![Curve voor precisie-terughalen voor een goed model](./media/how-to-understand-automated-ml/chart-precision-recall-curve-good.png)

### <a name="precision-recall-curve-for-a-bad-model"></a>Curve voor precisie-terughalen voor een slecht model
![Curve voor precisie-terughalen voor een slecht model](./media/how-to-understand-automated-ml/chart-precision-recall-curve-bad.png)

## <a name="cumulative-gains-curve"></a>Curve voor cumulatieve winst

De cumulatieve winstcurve geeft het percentage positieve steekproeven aan dat correct is geclassificeerd als een functie van het percentage steekproeven dat wordt beschouwd als steekproeven in de volgorde van de voorspelde waarschijnlijkheid.

Als u de winst wilt berekenen, moet u eerst alle steekproeven sorteren van de hoogste naar de laagste waarschijnlijkheid die door het model wordt voorspeld. Neem vervolgens `x%` de hoogste betrouwbaarheidsvoorspellingen. Deel het aantal positieve steekproeven dat in dat deel wordt gedetecteerd door het totale aantal `x%` positieve steekproeven om de winst te verkrijgen. Cumulatieve winst is het percentage positieve steekproeven dat we detecteren bij het overwegen van een deel van de gegevens die waarschijnlijk tot de positieve klasse behoren.

Een perfect model rangschikt alle positieve steekproeven boven alle negatieve steekproeven en geeft een cumulatieve winstcurve die bestaat uit twee rechte segmenten. De eerste is een lijn met helling van naar , waarbij het gedeelte is van de steekproeven die deel uitmaken van de `1 / x` positieve klasse ( als klassen evenwichtig `(0, 0)` `(x, 1)` `x` `1 / num_classes` zijn). De tweede is een horizontale lijn van `(x, 1)` tot `(1, 1)` . In het eerste segment worden alle positieve steekproeven correct geclassificeerd en gaat de cumulatieve winst naar `100%` binnen de eerste van de genomen `x%` steekproeven.

Het willekeurige basismodel heeft een cumulatieve winstcurve na, waarbij voor steekproeven alleen ongeveer van het totale aantal `y = x` `x%` positieve `x%` steekproeven is gedetecteerd. Een perfect model heeft een microgemiddelde curve die de linkerbovenhoek raakt en een macrogemiddelde lijn met een helling totdat de cumulatieve winst 100% is en vervolgens horizontaal totdat het gegevensspercentage `1 / num_classes` 100 is.
> [!TIP]
> Voor classificatieexperimenten kan elk van de lijndiagrammen die worden geproduceerd voor geautomatiseerde ML-modellen worden gebruikt om het model per klasse te evalueren of het gemiddelde te nemen over alle klassen. U kunt schakelen tussen deze verschillende weergaven door te klikken op klasselabels in de legenda rechts van de grafiek.
### <a name="cumulative-gains-curve-for-a-good-model"></a>Cumulatieve winstcurve voor een goed model
![Cumulatieve winstcurve voor een goed model](./media/how-to-understand-automated-ml/chart-cumulative-gains-curve-good.png)

### <a name="cumulative-gains-curve-for-a-bad-model"></a>Cumulatieve winstcurve voor een slecht model
![Cumulatieve winstcurve voor een slecht model](./media/how-to-understand-automated-ml/chart-cumulative-gains-curve-bad.png)

## <a name="lift-curve"></a>Lift-curve

De lift-curve laat zien hoe vaak beter een model presteert in vergelijking met een willekeurig model. Lift wordt gedefinieerd als de verhouding tussen cumulatieve winst en de cumulatieve winst van een willekeurig model.

Bij deze relatieve prestaties wordt rekening gehouden met het feit dat classificatie moeilijker wordt naarmate u het aantal klassen verhoogt. (Een willekeurig model voorspelt ten onrechte een hoger gedeelte van de steekproeven uit een gegevensset met 10 klassen vergeleken met een gegevensset met twee klassen)

De liftcurve van de basislijn is `y = 1` de lijn waar de modelprestaties consistent zijn met die van een willekeurig model. Over het algemeen zal de lift-curve voor een goed model hoger zijn in dat diagram en verder van de x-as, wat laat zien dat wanneer het model het meest vertrouwen heeft in de voorspellingen, het vele keren beter presteert dan willekeurig raden.

> [!TIP]
> Voor classificatieexperimenten kan elk van de lijndiagrammen die worden geproduceerd voor geautomatiseerde ML-modellen worden gebruikt om het model per klasse te evalueren of het gemiddelde te nemen over alle klassen. U kunt schakelen tussen deze verschillende weergaven door te klikken op klasselabels in de legenda rechts van de grafiek.
### <a name="lift-curve-for-a-good-model"></a>Lift-curve voor een goed model
![Lift-curve voor een goed model](./media/how-to-understand-automated-ml/chart-lift-curve-good.png)
 
### <a name="lift-curve-for-a-bad-model"></a>Lift-curve voor een slecht model
![Lift-curve voor een slecht model](./media/how-to-understand-automated-ml/chart-lift-curve-bad.png)

## <a name="calibration-curve"></a>Kalibratiekromme

De kalibratiecurve geeft het vertrouwen van een model weer in de voorspellingen ten opzichte van het aandeel positieve steekproeven op elk betrouwbaarheidsniveau. Een goed ge kalibreerd model classificeert correct 100% van de voorspellingen waaraan het 100% betrouwbaarheid toewijst, 50% van de voorspellingen waaraan het 50% vertrouwen toewijst, 20% van de voorspellingen waaraan het een betrouwbaarheid van 20% toewijst, en meer. Een perfect ge kalibreerd model heeft een kalibratiecurve na de lijn waar het model perfect de waarschijnlijkheid voorspelt dat `y = x` steekproeven tot elke klasse behoren.

Een te vol vertrouwend model voorspelt waarschijnlijkheden dicht bij nul en één, en is zelden niet zeker van de klasse van elke steekproef en de kalibratiecurve ziet er ongeveer uit als achterwaartse 'S'. Een model dat minder vertrouwen heeft, wijst gemiddeld een lagere waarschijnlijkheid toe aan de klasse die het voorspelt en de bijbehorende kalibratiecurve ziet er ongeveer uit als een 'S'. De curve van de kalibratie geeft niet aan dat een model correct kan classificeren, maar in plaats daarvan de mogelijkheid om betrouwbaarheid correct toe te wijzen aan de voorspellingen. Een slecht model kan nog steeds een goede curve hebben als het model op de juiste wijze lage betrouwbaarheid en hoge onzekerheid toewijst.

> [!NOTE]
> De curve van de kalibratie is gevoelig voor het aantal steekproeven, zodat een kleine validatieset ruisresultaten kan produceren die moeilijk te interpreteren zijn. Dit betekent niet noodzakelijkerwijs dat het model niet goed is ge kalibreerd.

### <a name="calibration-curve-for-a-good-model"></a>Curve van de kalibratie voor een goed model
![Curve van de kalibratie voor een goed model](./media/how-to-understand-automated-ml/chart-calibration-curve-good.png)

### <a name="calibration-curve-for-a-bad-model"></a>Curve van de kalibratie voor een slecht model
![Curve van de kalibratie voor een slecht model](./media/how-to-understand-automated-ml/chart-calibration-curve-bad.png)

## <a name="regressionforecasting-metrics"></a>Metrische gegevens voor regressie/prognose

Geautomatiseerde ML berekent dezelfde prestatiemetrieken voor elk gegenereerd model, ongeacht of het een regressie- of prognoseexperiment is. Deze metrische gegevens worden ook genormaliseerd om een vergelijking mogelijk te maken tussen modellen die zijn getraind op gegevens met verschillende reeksen. Zie Metrische [normalisatie voor meer informatie.](#metric-normalization)  

De volgende tabel bevat een overzicht van de metrische gegevens voor modelprestaties die worden gegenereerd voor regressie- en prognoseexperimenten. Net als metrische classificatiegegevens zijn deze metrische gegevens ook gebaseerd op de scikit learn-implementaties. De juiste scikit learn-documentatie is dienovereenkomstig gekoppeld in het **veld** Berekening.

|Metrisch|Beschrijving|Berekening|
--|--|--|
explained_variance|Uitleg van variantie meet de mate waarin een model rekening moet houden met de variatie in de doelvariabele. Dit is de procent afname in afwijking van de oorspronkelijke gegevens ten opzichte van de variantie van de fouten. Wanneer het gemiddelde van de fouten 0 is, is dit gelijk aan de decoëfficiënt van de bepaling (zie r2_score hieronder). <br> <br> **Doelstelling:** Hoe dichter bij 1, hoe beter <br> **Bereik:** (-inf, 1]|[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.explained_variance_score.html)|
mean_absolute_error|Gemiddelde absolute fout is de verwachte waarde van de absolute waarde van het verschil tussen het doel en de voorspelling.<br><br> **Doel:** Hoe dichter bij 0, hoe beter <br> **Bereik:** [0, inf) <br><br> Typen: <br>`mean_absolute_error` <br>  `normalized_mean_absolute_error`, de mean_absolute_error gedeeld door het gegevensbereik. | [Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|
mean_absolute_percentage_error|MapE (Mean absolute percentage error) is een meting van het gemiddelde verschil tussen een voorspelde waarde en de werkelijke waarde.<br><br> **Doel:** Hoe dichter bij 0, hoe beter <br> **Bereik:** [0, inf) ||
median_absolute_error|Mediaan absolute fout is de mediaan van alle absolute verschillen tussen het doel en de voorspelling. Dit verlies is robuust voor uitbijten.<br><br> **Doel:** Hoe dichter bij 0, hoe beter <br> **Bereik:** [0, inf)<br><br>Typen: <br> `median_absolute_error`<br> `normalized_median_absolute_error`: de median_absolute_error gedeeld door het gegevensbereik. |[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|
r2_score|R<sup>2</sup> (de bepalingscoëfficiënt) meet de proportionele vermindering van gemiddelde kwadraatfout (MSE) ten opzichte van de totale afwijking van de waargenomen gegevens. <br> <br> **Doel:** Hoe dichter bij 1, hoe beter <br> **Bereik:** [-1, 1]<br><br>Opmerking: R<sup>2</sup> heeft vaak het bereik (-inf, 1]. De MSE kan groter zijn dan de waargenomen afwijking, zodat R<sup>2</sup> willekeurig grote negatieve waarden kan hebben, afhankelijk van de gegevens en de modelvoorspellingen. Geautomatiseerde ML-fragmenten hebben R<sup>2-scores</sup> gerapporteerd op -1, dus een waarde van -1 voor R<sup>2</sup> betekent waarschijnlijk dat de werkelijke R<sup>2-score</sup> lager is dan -1. Houd rekening met de andere metrische waarden en de eigenschappen van de gegevens bij het interpreteren van een negatieve R<sup>2-score.</sup>|[Berekening](https://scikit-learn.org/0.16/modules/generated/sklearn.metrics.r2_score.html)|
root_mean_squared_error |De wortel van de gemiddelde kwadraatfout (RMSE) is de vierkantswortel van het verwachte kwadraatverschil tussen het doel en de voorspelling. Voor een onbevooroordeelde estimator is RMSE gelijk aan de standaarddeviatie.<br> <br> **Doelstelling:** Hoe dichter bij 0, hoe beter <br> **Bereik:** [0, inf)<br><br>Typen:<br> `root_mean_squared_error` <br> `normalized_root_mean_squared_error`: de root_mean_squared_error gedeeld door het gegevensbereik. |[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|
root_mean_squared_log_error|De wortel van de gemiddelde kwadraatlogboekfout is de vierkantswortel van de verwachte kwadraatfout in logaritme.<br><br>**Doelstelling:** Hoe dichter bij 0, hoe beter <br> **Bereik:** [0, inf) <br> <br>Typen: <br>`root_mean_squared_log_error` <br> `normalized_root_mean_squared_log_error`: de root_mean_squared_log_error gedeeld door het gegevensbereik.  |[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|
spearman_correlation| Spearman-correlatie is een niet-parametrische meting van de monotone relatie tussen twee gegevenssets. In tegenstelling tot de Pearson-correlatie gaat de Spearman-correlatie er niet van uit dat beide gegevenssets normaal worden gedistribueerd. Net als andere correlatiecoëfficiënten varieert Spearman tussen -1 en 1, met 0 wat geen correlatie impliceert. Correlaties van -1 of 1 impliceren een exacte monotone relatie. <br><br> Spearman is een metrische waarde voor rank-ordercorrelatie, wat betekent dat wijzigingen in voorspelde of werkelijke waarden het Spearman-resultaat niet wijzigen als ze de rangschikkingsorde van voorspelde of werkelijke waarden niet wijzigen.<br> <br> **Doelstelling:** Hoe dichter bij 1, hoe beter <br> **Bereik:** [-1, 1]|[Berekening](https://docs.scipy.org/doc/scipy-0.16.1/reference/generated/scipy.stats.spearmanr.html)|

### <a name="metric-normalization"></a>Normalisering van metrische gegevens

Geautomatiseerde ML normaliseert metrische gegevens voor regressie en prognoses, waardoor modellen die zijn getraind op gegevens met verschillende bereik kunnen worden vergeleken. Een model dat is getraind op gegevens met een groter bereik heeft een hogere fout dan hetzelfde model dat is getraind op gegevens met een kleiner bereik, tenzij deze fout wordt genormaliseerd.

Hoewel er geen standaardmethode is voor het normaliseren van metrische foutgegevens, gebruikt geautomatiseerde ML de algemene benadering om de fout te delen door het gegevensbereik: `normalized_error = error / (y_max - y_min)`

Bij het evalueren van een prognosemodel op tijdreeksgegevens, neemt geautomatiseerde ML extra stappen om ervoor te zorgen dat normalisering plaatsvindt per tijdreeks-id (grain), omdat elke tijdreeks waarschijnlijk een andere distributie van doelwaarden heeft.
## <a name="residuals"></a>Residuen

Het residuendiagram is een histogram van de voorspellingsfouten (residuen) die zijn gegenereerd voor regressie- en prognoseexperimenten. Residuen worden berekend als `y_predicted - y_true` voor alle voorbeelden en vervolgens weergegeven als een histogram om modelvooroordelen weer te geven.

In dit voorbeeld zijn beide modellen enigszins bevooroordeeld om lager te voorspellen dan de werkelijke waarde. Dit is niet ongebruikelijk voor een gegevensset met een verscheefde verdeling van werkelijke doelen, maar duidt op slechtere modelprestaties. Een goed model heeft een residuendistributie die piekt op nul met weinig residuen aan de uitersten. Een slechter model heeft een verspreide residuendistributie met minder steekproeven rond nul.

### <a name="residuals-chart-for-a-good-model"></a>Residuendiagram voor een goed model
![Residuendiagram voor een goed model](./media/how-to-understand-automated-ml/chart-residuals-good.png)

### <a name="residuals-chart-for-a-bad-model"></a>Diagram met residuen voor een slecht model
![Diagram met residuen voor een slecht model](./media/how-to-understand-automated-ml/chart-residuals-bad.png)

## <a name="predicted-vs-true"></a>Voorspeld versus waar

Voor regressie en prognoses wordt in het voorspelde versus het werkelijke diagram de relatie tussen de doelfunctie (waar/werkelijke waarden) en de voorspellingen van het model weergegeven. De werkelijke waarden worden op de x-as in een bin geplaatst en voor elke bin wordt de gemiddelde voorspelde waarde uitgezet met foutbalken. Hiermee kunt u zien of een model is gericht op het voorspellen van bepaalde waarden. De lijn geeft de gemiddelde voorspelling weer en het gearceerde gebied geeft de afwijking van voorspellingen rond dat gemiddelde aan.

Vaak heeft de meest voorkomende werkelijke waarde de meest nauwkeurige voorspellingen met de laagste afwijking. De afstand van de trendlijn van de ideale lijn waar enkele werkelijke waarden zijn, is een goede meting van `y = x` de modelprestaties bij uitbijten. U kunt het histogram onderaan het diagram gebruiken om te redeneren over de werkelijke gegevensdistributie. Door meer gegevensvoorbeelden op te nemen waarbij de distributie verspreid is, kunnen de modelprestaties voor niet-beveiligde gegevens worden verbeterd.

In dit voorbeeld heeft het betere model een voorspelde versus werkelijke lijn die dichter bij de ideale lijn `y = x` ligt.

### <a name="predicted-vs-true-chart-for-a-good-model"></a>Voorspelde versus werkelijke grafiek voor een goed model
![Voorspelde versus werkelijke grafiek voor een goed model](./media/how-to-understand-automated-ml/chart-predicted-true-good.png)

### <a name="predicted-vs-true-chart-for-a-bad-model"></a>Voorspelde versus werkelijke grafiek voor een slecht model
![Voorspelde versus werkelijke grafiek voor een slecht model](./media/how-to-understand-automated-ml/chart-predicted-true-bad.png)

## <a name="model-explanations-and-feature-importances"></a>Modelverklaringen en functie-belangrijkheid

Hoewel metrische gegevens en grafieken voor modelevaluatie goed zijn voor het meten van de algemene kwaliteit van een model, is het essentieel om te controleren welke gegevenssetfuncties een model gebruikt om de voorspellingen te doen bij het gebruik van verantwoorde AI. Daarom biedt geautomatiseerde ML een dashboard met modelverklaringen om de relatieve bijdragen van gegevenssetfuncties te meten en te rapporteren. Bekijk het [uitlegdashboard in de](how-to-use-automated-ml-for-ml-models.md#model-explanations-preview)Azure Machine Learning-studio .

Zie Modelverklaringen instellen voor geautomatiseerde ML-experimenten met de Python SDK voor een eerste [code Azure Machine Learning ervaring.](how-to-machine-learning-interpretability-automl.md)

> [!NOTE]
> Het ForecastTCN-model wordt momenteel niet ondersteund door geautomatiseerde ML-uitleg en andere prognosemodellen hebben mogelijk beperkte toegang tot interpreteerbaarheidshulpprogramma's.

## <a name="next-steps"></a>Volgende stappen
* Probeer de [voorbeeld-notebooks machine learning uitleg van het geautomatiseerde model.](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model)
* Voor specifieke vragen over geautomatiseerde ML kunt u contact op met askautomatedml@microsoft.com .
