---
title: Resultaten van AutoML-experiment evalueren
titleSuffix: Azure Machine Learning
description: Meer informatie over het weer geven en evalueren van grafieken en metrische gegevens voor elk van uw geautomatiseerde machine learning experimenten.
services: machine-learning
author: gregorybchris
ms.author: chgrego
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.date: 11/30/2020
ms.topic: conceptual
ms.custom: how-to, contperfq2, automl
ms.openlocfilehash: 43ce1c4865b3458ccd9c0ac17589f8ca5d77d92f
ms.sourcegitcommit: fec60094b829270387c104cc6c21257826fccc54
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/09/2020
ms.locfileid: "96922075"
---
# <a name="evaluate-automated-machine-learning-experiment-results"></a>Resultaten van automatische machine learning experimenten evalueren

In dit artikel vindt u informatie over het evalueren en vergelijken van modellen die zijn getraind door uw geautomatiseerde machine learning-experiment (geautomatiseerd ML). Tijdens de uitvoering van een geautomatiseerd ML experiment worden veel uitgevoerd en wordt er een model gemaakt. Voor elk model genereert automatische ML evaluatie-metrische gegevens en grafieken die u helpen de prestaties van het model te meten. 

Zo genereren automatische ML de volgende diagrammen op basis van het type experiment.

| Classificatie| Regressie/prognose |
| ----------------------------------------------------------- | ---------------------------------------- |
| [Verwarringsmatrix](#confusion-matrix)                       | [Residuenhistogram](#residuals)        |
| [De curve van de ontvanger van het besturings kenmerk (ROC)](#roc-curve) | [Voorspeld versus waar](#predicted-vs-true) |
| [Precisie-intrekken (PR)-curve](#precision-recall-curve)      |                                          |
| [Bocht](#lift-curve)                                   |                                          |
| [Cumulatieve toename-curve](#cumulative-gains-curve)           |                                          |
| [Kalibratie curve](#calibration-curve)                     |                     


## <a name="prerequisites"></a>Vereisten

- Een Azure-abonnement. (Als u nog geen abonnement op Azure hebt, [Maak dan een gratis account](https://aka.ms/AMLFree) aan voordat u begint)
- Een Azure Machine Learning experiment gemaakt met een van de volgende opties:
  - De [Azure machine learning Studio](how-to-use-automated-ml-for-ml-models.md) (geen code vereist)
  - De [Azure machine learning PYTHON SDK](how-to-configure-auto-train.md)

## <a name="view-run-results"></a>Uitvoerings resultaten weer geven

Nadat het experiment voor automatische ML is voltooid, kunt u een overzicht van de uitvoeringen vinden via:
  - Een browser met [Azure machine learning Studio](overview-what-is-machine-learning-studio.md)
  - Een Jupyter-notebook met behulp van de [RunDetails Jupyter-widget](/python/api/azureml-widgets/azureml.widgets.rundetails?view=azure-ml-py&preserve-view=true)

In de volgende stappen en video kunt u zien hoe u de uitvoerings geschiedenis en de metrische gegevens voor de model evaluatie weergeeft in de studio:

1. Meld u aan bij [de Studio](https://ml.azure.com/) en navigeer naar uw werk ruimte.
1. Selecteer in het menu links **experimenten**.
1. Selecteer uw experiment in de lijst met experimenten.
1. Selecteer in de tabel onder aan de pagina een automatische ML-uitvoering.
1. Selecteer op het tabblad **modellen** de naam van het **algoritme** voor het model dat u wilt evalueren.
1. Gebruik op het tabblad **metrieken** de selectie vakjes aan de linkerkant om metrische gegevens en grafieken weer te geven.

![Stappen voor het weer geven van metrische gegevens in Studio](./media/how-to-understand-automated-ml/how-to-studio-metrics.gif)

## <a name="classification-metrics"></a>Metrische classificaties

Automatische ML berekent metrische prestatie gegevens voor elk classificatie model dat is gegenereerd voor uw experiment. Deze metrische gegevens zijn gebaseerd op de scikit-leer implementatie. 

Er zijn veel classificatie gegevens gedefinieerd voor binaire classificaties op twee klassen, en voor het genereren van gemiddelden over klassen is een score voor classificatie met meerdere klassen vereist. Scikit-Learn biedt verschillende methoden voor het berekenen, drie van de geautomatiseerde ML: **macro**, **micro** en **gewogen**.

- **Macro** -de metrische gegevens voor elke klasse berekenen en het niet-gewogen gemiddelde nemen
- **Micro** -de metrische gegevens globaal berekenen door het totale aantal positieve, onwaare negatieven en foutieve positieven (onafhankelijk van klassen) te tellen.
- **Gewogen** : berekent de metriek voor elke klasse en nemen het gewogen gemiddelde op basis van het aantal steek proeven per klasse.

Hoewel elke methode voor het berekenen van de voor delen een gemeen schappelijke overweging heeft wanneer de juiste methode wordt geselecteerd, is de klasse onevenwichtig. Als klassen een verschillend aantal voor beelden hebben, is het wellicht meer informatie over het gebruik van een macro gemiddeld waarbij minderheids klassen gelijk zijn aan de meerderheid van de klassen. Meer informatie over [metrische gegevens van het binaire element en de multiklassewaarde in automatische milliliters](#binary-vs-multiclass-classification-metrics). 

De volgende tabel bevat een overzicht van de prestatie gegevens van het model die geautomatiseerd ML berekent voor elk classificatie model dat voor uw experiment wordt gegenereerd. Zie de documentatie voor scikit-Learn die is gekoppeld in het veld **berekening** van elke metriek voor meer informatie. 

|Gegevens|Beschrijving|Berekening|
|--|--|---|
|AUC | AUC is het gebied onder de [ontvanger van het besturings systeem](#roc-curve).<br><br> **Doel stelling:** Dichter bij 1 hoe beter <br> **Bereik:** [0, 1]<br> <br>Ondersteunde metrische namen zijn onder andere, <li>`AUC_macro`, het reken kundige gemiddelde van de AUC voor elke klasse.<li> `AUC_micro`, berekend door het combi neren van de werkelijke positieven en de fout-positieven van elke klasse. <li> `AUC_weighted`, reken kundig gemiddelde van de score voor elke klasse, gewogen op basis van het aantal werkelijke instanties in elke klasse.   |[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | 
|accuracy| Nauw keurigheid is de verhouding van voor spellingen die exact overeenkomen met de echte klassen labels. <br> <br>**Doel stelling:** Dichter bij 1 hoe beter <br> **Bereik:** [0, 1]|[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html)|
|average_precision|De gemiddelde nauw keurigheid van een curve is een gebogen lijn die het gewogen gemiddelde van de nauw keurigheid van elke drempel waarde bereikt, waarbij de toename van de vorige drempel waarde die als gewicht wordt gebruikt, wordt ingetrokken. <br><br> **Doel stelling:** Dichter bij 1 hoe beter <br> **Bereik:** [0, 1]<br> <br>Ondersteunde metrische namen zijn onder andere,<li>`average_precision_score_macro`, het reken kundige gemiddelde van de gemiddelde precisie Score van elke klasse.<li> `average_precision_score_micro`, berekend door het combi neren van de werkelijke positieven en de fout-positieven bij elke afsluit proces.<li>`average_precision_score_weighted`, het reken kundige gemiddelde van de gemiddelde precisie score voor elke klasse, gewogen op basis van het aantal werkelijke instanties in elke klasse.|[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|
balanced_accuracy|Nauw keurigheid in evenwicht is het reken kundige gemiddelde van intrekken voor elke klasse.<br> <br>**Doel stelling:** Dichter bij 1 hoe beter <br> **Bereik:** [0, 1]|[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|
f1_score|F1-Score is het harmonische gemiddelde van Precision en intrekken. Het is een goede evenwichtige maat eenheid voor zowel fout-positieve als onjuiste negatieven. Er wordt echter geen rekening gehouden met de waarde voor het account. <br> <br>**Doel stelling:** Dichter bij 1 hoe beter <br> **Bereik:** [0, 1]<br> <br>Ondersteunde metrische namen zijn onder andere,<li>  `f1_score_macro`: het reken kundige gemiddelde van de F1-score voor elke klasse. <li> `f1_score_micro`: berekend door het totale aantal positieve, onwaare negatieven en fout-positieven te tellen. <li> `f1_score_weighted`: gewogen gemiddelde per klasse frequentie van de F1-score voor elke klasse.|[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|
log_loss|Dit is de functie verlies die wordt gebruikt in (MultiNomial) logistiek regressie en uitbrei dingen van IT, zoals Neural-netwerken, gedefinieerd als de negatieve kans op Logboeken van de werkelijke labels op basis van de voor spellingen van een Probabilistic-classificatie. <br><br> **Doel stelling:** Dichter bij 0 hoe beter <br> **Bereik:** [0, inf)|[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.log_loss.html)|
norm_macro_recall| Genormaliseerde macro intrekken is een terugroep macro: gemiddeld en genormaliseerd, zodat wille keurige prestaties een Score van 0 hebben en de perfecte prestaties een Score van 1 hebben. <br> <br>**Doel stelling:** Dichter bij 1 hoe beter <br> **Bereik:** [0, 1] |`(recall_score_macro - R)`&nbsp;/&nbsp;`(1 - R)` <br><br>waar, `R` de verwachte waarde van `recall_score_macro` voor wille keurige voor spellingen.<br><br>`R = 0.5`&nbsp;voor &nbsp; binaire &nbsp; classificatie. <br>`R = (1 / C)` voor problemen met de classificatie van C-klasse.|
Matthews-correlatie coëfficiënt | Matthews-correlatie coëfficiënt is een evenwichtige meet nauwkeurigheid, die zelfs kan worden gebruikt als één klasse veel meer steek proeven heeft dan de andere. Een coëfficiënt van 1 geeft perfecte voor spelling, 0 wille keurige voor spelling en-1 inverse voor spelling.<br><br> **Doel stelling:** Dichter bij 1 hoe beter <br> **Bereik:** [-1, 1]|[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.matthews_corrcoef.html)|
precisie|Precision is de mogelijkheid van een model om te voor komen dat negatieve steek proeven als positief worden gelabeld. <br><br> **Doel stelling:** Dichter bij 1 hoe beter <br> **Bereik:** [0, 1]<br> <br>Ondersteunde metrische namen zijn onder andere, <li> `precision_score_macro`, het reken kundige gemiddelde van de precisie voor elke klasse. <li> `precision_score_micro`, globaal berekend door het totale aantal positieve en foutieve positieven te tellen. <li> `precision_score_weighted`, het reken kundige gemiddelde van de precisie voor elke klasse, gewogen op basis van het aantal werkelijke instanties in elke klasse.|[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|
relevante overeenkomsten| Intrekken is de mogelijkheid van een model voor het detecteren van alle positieve voor beelden. <br><br> **Doel stelling:** Dichter bij 1 hoe beter <br> **Bereik:** [0, 1]<br> <br>Ondersteunde metrische namen zijn onder andere, <li>`recall_score_macro`: het reken kundige gemiddelde van het intrekken van elke klasse. <li> `recall_score_micro`: globaal berekend door het totale aantal positieve, onwaare negatieven en foutieve positieven te tellen.<li> `recall_score_weighted`: het reken kundige gemiddelde van het intrekken van elke klasse, gewogen op het aantal werkelijke instanties in elke klasse.|[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|
weighted_accuracy|Gewogen nauw keurigheid is nauw keurig wanneer elk voor beeld wordt gewogen door het totale aantal steek proeven dat tot dezelfde klasse behoort. <br><br>**Doel stelling:** Dichter bij 1 hoe beter <br>**Bereik:** [0, 1]|[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html)|

### <a name="binary-vs-multiclass-classification-metrics"></a>Binaire gegevens en classificaties voor klassen met meer dan een klasse

Automatische ML maakt geen onderscheid tussen binaire en multimetrische metrische gegevens. Dezelfde validatie gegevens worden gerapporteerd, of een gegevensset twee klassen of meer dan twee klassen heeft. Sommige metrische gegevens zijn echter bedoeld voor de classificatie van verschillende klassen. Wanneer dit wordt toegepast op een binaire gegevensset, worden deze metrische gegevens niet als `true` klasse beschouwd, zoals u mogelijk verwacht. Metrische gegevens die duidelijk bedoeld zijn voor multi class, worden met `micro` ,, of geachtervoegseld `macro` `weighted` . Voor beelden zijn `average_precision_score` , `f1_score` ,, en `precision_score` `recall_score` `AUC` .

In plaats van intrekken te berekenen als `tp / (tp + fn)` , wordt het gemiddelde van de multiklasse-inhaal ( `micro` , `macro` of `weighted` ) gemiddeld boven beide klassen van een gegevensset voor binaire classificatie. Dit komt overeen met het berekenen van het terughalen voor de `true` klasse en de `false` klasse afzonderlijk, en vervolgens het gemiddelde van de twee.

## <a name="confusion-matrix"></a>Verwarringsmatrix

Verwar ring matrices bieden een visuele manier om te bepalen hoe een machine learning model systematische fouten in de voor spellingen van classificatie modellen maakt. Het woord ' Verwar ring ' in de naam is afkomstig uit een model met ' verwarren ' of verkeerd gelabelde voor beelden. Een cel in rij `i` en kolom `j` in een Verwar ring-matrix bevat het aantal steek proeven in de evaluatie-gegevensset die bij een klasse horen `C_i` en die door het model als klasse zijn geclassificeerd `C_j` .

In de Studio duidt een donkerdere cel op een hoger aantal steek proeven. Als u de **genormaliseerde** weer gave in de vervolg keuzelijst selecteert, wordt er voor elke matrix regel genormaliseerd dat het percentage van de klasse `C_i` wordt voorspeld als klasse `C_j` . Het voor deel van de standaard weergave van **RAW** is dat u kunt zien of onevenwichtigheid in de verdeling van werkelijke klassen het model heeft veroorzaakt om voor beelden te classificeren van de minderheids klasse, een gemeen schappelijk probleem in gegevens sets die niet in balans zijn.

De Verwar ring matrix van een goed model bevat de meeste steek proeven langs de diagonale richting.

### <a name="confusion-matrix-for-a-good-model"></a>Verwar ring matrix voor een goed model 
![Verwar ring matrix voor een goed model ](./media/how-to-understand-automated-ml/chart-confusion-matrix-good.png)

### <a name="confusion-matrix-for-a-bad-model"></a>Verwar ring matrix voor een onjuist model
![Verwar ring matrix voor een onjuist model](./media/how-to-understand-automated-ml/chart-confusion-matrix-bad.png)

## <a name="roc-curve"></a>ROC-curve

De curve van de ontvanger heeft de relatie tussen de werkelijke positieve frequentie (TPR) en de fout positief (voor) als de drempel waarde voor de beslissing verandert. De ROC-curve kan minder informatieve zijn bij het trainen van modellen op gegevens sets met een hoge onevenwichtige klasse, aangezien de meerderheids klasse bijdragen kan verdrinken van minderheids klassen.

Het gebied onder de curve (AUC) kan worden geïnterpreteerd als het aandeel van de juiste geclassificeerde steek proeven. Nauw keuriger is de AUC de kans dat de classificatie een wille keurig positief voor beeld krijgt dat groter is dan een wille keurig gekozen negatief voor beeld. De vorm van de curve geeft een Intuition voor de relatie tussen TPR en voor als functie van de classificatie drempel of beslissings grens.

Een curve die de linkerbovenhoek van de grafiek nadert, is een 100% TPR en 0% voor, het best mogelijke model. Een wille keurig model produceert een ROC curve langs de `y = x` lijn van de linkerbenedenhoek naar de rechter bovenhoek. Een erger dan een wille keurig model zou een ROC-curve hebben die spannings dips onder de `y = x` regel.
> [!TIP]
> Voor classificatie experimenten kan elk van de lijn diagrammen die worden geproduceerd voor geautomatiseerde ML modellen worden gebruikt om het model per klasse of gemiddeld in alle klassen te evalueren. U kunt scha kelen tussen deze verschillende weer gaven door te klikken op klassen labels in de legenda rechts van de grafiek.
### <a name="roc-curve-for-a-good-model"></a>ROC-curve voor een goed model
![ROC-curve voor een goed model](./media/how-to-understand-automated-ml/chart-roc-curve-good.png)

### <a name="roc-curve-for-a-bad-model"></a>ROC curve voor een onjuist model
![ROC curve voor een onjuist model](./media/how-to-understand-automated-ml/chart-roc-curve-bad.png)

## <a name="precision-recall-curve"></a>Nauw keurigheid-intrekken curve

Met de curve voor het terugroepen van precisie wordt de relatie tussen de precisie en de intrekking van de beslissings drempel geplot. Intrekken is de mogelijkheid van een model om alle positieve voor beelden te detecteren en precisie is de mogelijkheid van een model om te voor komen dat negatieve steek proeven als positief worden gelabeld. Voor sommige zakelijke problemen is het intrekken en wat een grotere nauw keurigheid nodig, afhankelijk van het relatieve belang van het voor komen van onjuiste negatieven versus valse positieven.
> [!TIP]
> Voor classificatie experimenten kan elk van de lijn diagrammen die worden geproduceerd voor geautomatiseerde ML modellen worden gebruikt om het model per klasse of gemiddeld in alle klassen te evalueren. U kunt scha kelen tussen deze verschillende weer gaven door te klikken op klassen labels in de legenda rechts van de grafiek.
### <a name="precision-recall-curve-for-a-good-model"></a>Curve voor het intrekken van precisie voor een goed model
![Curve voor het intrekken van precisie voor een goed model](./media/how-to-understand-automated-ml/chart-precision-recall-curve-good.png)

### <a name="precision-recall-curve-for-a-bad-model"></a>Curve voor het intrekken van een precisie voor een onjuist model
![Curve voor het intrekken van een precisie voor een onjuist model](./media/how-to-understand-automated-ml/chart-precision-recall-curve-bad.png)

## <a name="cumulative-gains-curve"></a>Cumulatieve toename-curve

Met de cumulatieve winst curve wordt het percentage positieve steek proeven correct geclassificeerd als een functie van het percentage steek proeven dat wordt overwogen, waarbij de voor spellingen van voor speld resultaat worden onderzocht.

Als u de winst wilt berekenen, moet u eerst alle voor beelden van de hoogste naar de laagste waarschijnlijkheid van het model sorteren. Ga vervolgens naar `x%` de hoogste betrouw baarheid. Deel het aantal positieve steek proeven dat is gedetecteerd op `x%` basis van het totale aantal positieve steek proeven om de winst te krijgen. Cumulatieve toename is het percentage positieve steek proeven dat wordt gedetecteerd bij het overwegen van een percentage van de gegevens die waarschijnlijk tot de positieve klasse behoren.

Bij een perfecte model worden alle positieve steek proeven genoteerd boven alle negatieve steek proeven, met een cumulatieve toename kromme die bestaat uit twee rechte segmenten. De eerste is een lijn met helling `1 / x` van `(0, 0)` tot `(x, 1)` waar `x` is de Fractie van steek proeven die bij de positieve klasse horen ( `1 / num_classes` als de klassen evenwichtig zijn). De tweede is een horizontale lijn van `(x, 1)` tot `(1, 1)` . In het eerste segment worden alle positieve steek proeven correct geclassificeerd en wordt cumulatief gewonnen `100%` binnen de eerste `x%` van de overwogen steek proeven.

Het model voor wille keurige basis lijn heeft een gepaarde verdeling van de cumulatieve winst, volgend `y = x` op waar voor `x%` steek proeven die alleen over `x%` de totale positieve steek proeven worden beschouwd. Een perfecte model heeft een micro gemiddelde kromme die de linkerbovenhoek en een regel voor het gemiddelde van een macro die de helling heeft `1 / num_classes` tot de cumulatieve toename 100% en vervolgens horizon taal tot het percentage van de gegevens 100.
> [!TIP]
> Voor classificatie experimenten kan elk van de lijn diagrammen die worden geproduceerd voor geautomatiseerde ML modellen worden gebruikt om het model per klasse of gemiddeld in alle klassen te evalueren. U kunt scha kelen tussen deze verschillende weer gaven door te klikken op klassen labels in de legenda rechts van de grafiek.
### <a name="cumulative-gains-curve-for-a-good-model"></a>Cumulatieve toename van de curve voor een goed model
![Cumulatieve toename van de curve voor een goed model](./media/how-to-understand-automated-ml/chart-cumulative-gains-curve-good.png)

### <a name="cumulative-gains-curve-for-a-bad-model"></a>Cumulatieve toename van de kromme voor een onjuist model
![Cumulatieve toename van de kromme voor een onjuist model](./media/how-to-understand-automated-ml/chart-cumulative-gains-curve-bad.png)

## <a name="lift-curve"></a>Bocht

In de lift kromme ziet u hoe vaak een model beter presteert ten opzichte van een wille keurig model. Lift is gedefinieerd als de verhouding van de cumulatieve toename van de cumulatieve toename van een wille keurig model.

Bij deze relatieve prestaties wordt rekening gehouden met het feit dat de classificatie moeilijker wordt naarmate u het aantal klassen verhoogt. (Een wille keurig model wordt onjuist voor speld op basis van een hogere Fractie van voor beelden van een gegevensset met 10 klassen vergeleken met een gegevensset met twee klassen)

De kromme voor de basis lijn is de `y = 1` lijn waar de prestaties van het model consistent zijn met die van een wille keurig model. Over het algemeen is de lift-curve voor een goed model groter dan die grafiek en verder van de x-as, wat aangeeft dat wanneer het model het meest betrouwbaar is in de voor spellingen die het veel beter is dan een wille keurige schatting.

> [!TIP]
> Voor classificatie experimenten kan elk van de lijn diagrammen die worden geproduceerd voor geautomatiseerde ML modellen worden gebruikt om het model per klasse of gemiddeld in alle klassen te evalueren. U kunt scha kelen tussen deze verschillende weer gaven door te klikken op klassen labels in de legenda rechts van de grafiek.
### <a name="lift-curve-for-a-good-model"></a>Bocht voor een goed model
![Bocht voor een goed model](./media/how-to-understand-automated-ml/chart-lift-curve-good.png)
 
### <a name="lift-curve-for-a-bad-model"></a>Lift curve voor een onjuist model
![Lift curve voor een onjuist model](./media/how-to-understand-automated-ml/chart-lift-curve-bad.png)

## <a name="calibration-curve"></a>Kalibratie curve

Met de kalibratie curve wordt de vertrouwens verhouding van een model in de voor spellingen van het aantal positieve steek proeven op elk betrouwbaarheids niveau getekend. Een goed gekalibreerd model classificeert 100% van de voor spellingen waaraan het 100% betrouw baarheid, 50% van de voor spellingen toewijst 50% betrouw baarheid, 20% van de voor spellingen die een 20% betrouw baarheid toewijst, enzovoort. Een volledig gekalibreerd model heeft een kalibratie kromme `y = x` die volgt op de regel waarin het model perfect voor spelt op de kans dat er steek proeven bij elke klasse horen.

Een over-vertrouwbaar model heeft meer voor speld dat de waarschijnlijkheid gelijk is aan nul en één, wat zelden duidelijk is over de klasse van elk voor beeld en de kalibratie curve ziet er ongeveer uit als achterwaarts. Een onder-vertrouwend model wijst een lagere kans op gemiddelde toe aan de klasse die het voor spelt, en de bijbehorende kalibratie curve ziet er ongeveer uit als een ' S '. In de kalibratie curve wordt niet de mogelijkheid van een model op de juiste wijze geclassificeerd, maar in plaats daarvan wordt het vertrouwen aan de voor spellingen toegewezen. Een onjuist model kan nog steeds een goede kalibratie curve hebben als het model goed vertrouwen en hoge onzekerheid toewijst.

> [!NOTE]
> De kalibratie curve is gevoelig voor het aantal steek proeven, dus een kleine validatieset kan ruis resultaten opleveren die moeilijk te interpreteren kunnen zijn. Dit betekent niet noodzakelijkerwijs dat het model niet goed is gekalibreerd.

### <a name="calibration-curve-for-a-good-model"></a>Kalibratie curve voor een goed model
![Kalibratie curve voor een goed model](./media/how-to-understand-automated-ml/chart-calibration-curve-good.png)

### <a name="calibration-curve-for-a-bad-model"></a>Kalibratie curve voor een onjuist model
![Kalibratie curve voor een onjuist model](./media/how-to-understand-automated-ml/chart-calibration-curve-bad.png)

## <a name="regressionforecasting-metrics"></a>Metrische gegevens voor regressie/prognose

Automatische ML berekent dezelfde metrische gegevens over prestaties voor elk gegenereerd model, ongeacht of het gaat om een regressie-of prognose-experiment. Deze metrische gegevens worden ook Norma Lise ring voor het maken van een vergelijking tussen modellen die zijn getraind voor gegevens met verschillende bereiken. Zie [metrische gegevens normalisatie](#metric-normalization) voor meer informatie  

De volgende tabel bevat een overzicht van de prestatie gegevens voor modellen die zijn gegenereerd voor regressie-en prognose experimenten. Net als bij classificatie-metrische gegevens zijn deze metrische gegevens ook gebaseerd op de scikit leer implementaties. De relevante scikit-documentatie is dienovereenkomstig gekoppeld in het veld **berekening** .

|Gegevens|Beschrijving|Berekening|
--|--|--|
explained_variance|De uitleg afwijking meet de mate waarin een model accounts voor de variatie in de doel variabele. Het is het percentage afname van de oorspronkelijke gegevens tot de variantie van de fouten. Wanneer het gemiddelde van de fouten 0 is, is het gelijk aan het kwadraat van de berekening (Zie r2_score hieronder). <br> <br> **Doel stelling:** Dichter bij 1 hoe beter <br> **Bereik:** (-inf, 1]|[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.explained_variance_score.html)|
mean_absolute_error|De gemiddelde absolute fout is de verwachte waarde van de absolute waarde van het verschil tussen het doel en de voor spelling.<br><br> **Doel stelling:** Dichter bij 0 hoe beter <br> **Bereik:** [0, inf) <br><br> Dergelijke <br>`mean_absolute_error` <br>  `normalized_mean_absolute_error`, de mean_absolute_error gedeeld door het bereik van de gegevens. | [Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|
median_absolute_error|Mediaan absolute fout is de mediaan van alle absolute verschillen tussen het doel en de voor spelling. Dit verlies is robuust voor uitbijters.<br><br> **Doel stelling:** Dichter bij 0 hoe beter <br> **Bereik:** [0, inf)<br><br>Dergelijke <br> `median_absolute_error`<br> `normalized_median_absolute_error`: de median_absolute_error gedeeld door het bereik van de gegevens. |[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|
r2_score|R ^ 2 is de determinatie coëfficiënt of het percentage verlaging in kwadratische fouten ten opzichte van een basislijn model dat het gemiddelde uitvoert. <br> <br> **Doel stelling:** Dichter bij 1 hoe beter <br> **Bereik:** (-inf, 1]|[Berekening](https://scikit-learn.org/0.16/modules/generated/sklearn.metrics.r2_score.html)|
root_mean_squared_error |Root mean error (RMSE) is de vierkantswortel van het verwachte verschil in kwadraat tussen het doel en de voor spelling. Voor een onzuivere Estimator is RMSE gelijk aan de standaard deviatie.<br> <br> **Doel stelling:** Dichter bij 0 hoe beter <br> **Bereik:** [0, inf)<br><br>Dergelijke<br> `root_mean_squared_error` <br> `normalized_root_mean_squared_error`: de root_mean_squared_error gedeeld door het bereik van de gegevens. |[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|
root_mean_squared_log_error|Het wortel gemiddelde van het logaritmische fout is de vierkantswortel van de verwachte kwadratische fout.<br><br>**Doel stelling:** Dichter bij 0 hoe beter <br> **Bereik:** [0, inf) <br> <br>Dergelijke <br>`root_mean_squared_log_error` <br> `normalized_root_mean_squared_log_error`: de root_mean_squared_log_error gedeeld door het bereik van de gegevens.  |[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|
spearman_correlation| ' Spearman correlatie ' is een niet-parametrische meting van de monotonicity van de relatie tussen twee gegevens sets. In tegens telling tot de correlatie van Pearson, neemt de ' Spearman-correlatie niet in dat beide gegevens sets normaal gesp roken worden gedistribueerd. Net als andere correlatie coëfficiënten varieert ' Spearman ' tussen-1 en 1 met 0 voor geen correlatie. De correlaties van-1 of 1 impliceren een nauw keurige monotone relatie. <br><br> ' Spearman ' is een rang orde correlatie-metriek, wat betekent dat wijzigingen in voor spelling of werkelijke waarden het resultaat van de taak ' Spearman ' niet wijzigen als de rang orde van voor spelling of werkelijke waarden niet wordt gewijzigd.<br> <br> **Doel stelling:** Dichter bij 1 hoe beter <br> **Bereik:** [-1, 1]|[Berekening](https://docs.scipy.org/doc/scipy-0.16.1/reference/generated/scipy.stats.spearmanr.html)|

### <a name="metric-normalization"></a>Metrische gegevens normalisatie

Automated ML maakt regressies en prognoses van metrische gegevens die kunnen worden vergeleken tussen modellen die zijn getraind met verschillende bereiken. Een model dat is getraind voor een gegevens met een groter bereik heeft een hogere fout dan hetzelfde model dat is getraind voor gegevens met een kleiner bereik, tenzij deze fout wordt genormaliseerd.

Hoewel er geen standaard methode is voor het normaliseren van metrische fout gegevens, neemt geautomatiseerd ML de gemeen schappelijke aanpak voor het delen van de fout op uit het bereik van de gegevens: `normalized_error = error / (y_max - y_min)`

Bij het evalueren van een prognose model voor tijdreeks gegevens neemt geautomatiseerde ML extra stappen om ervoor te zorgen dat normalisatie plaatsvindt per time series-ID (korrel), omdat elke tijd reeks waarschijnlijk een andere distributie van doel waarden heeft.
## <a name="residuals"></a>Residuen

De grafiek verschilt is een histogram met de Voorspellings fouten (verschillen) die zijn gegenereerd voor regressie-en prognose experimenten. Verschillen worden berekend als `y_predicted - y_true` voor alle voor beelden en vervolgens weer gegeven als een histogram om de model afwijking weer te geven.

In dit voor beeld ziet u dat beide modellen enigszins worden verlicht om lager te voors pellen dan de werkelijke waarde. Dit is niet ongebruikelijk voor een gegevensset met een scheefe verdeling van werkelijke doelen, maar geeft slechtere model prestaties. Een goed model heeft een verdeelde verdeling van de grootste waarde bij nul met een paar resten in de meeste jaren. Een erger model heeft een verdeling van de verdeelde verschillen met minder steek proeven rond nul.

### <a name="residuals-chart-for-a-good-model"></a>Diagram van de rest van een goed model
![Diagram van de rest van een goed model](./media/how-to-understand-automated-ml/chart-residuals-good.png)

### <a name="residuals-chart-for-a-bad-model"></a>Diagram van verschillen voor een onjuist model
![Diagram van verschillen voor een onjuist model](./media/how-to-understand-automated-ml/chart-residuals-bad.png)

## <a name="predicted-vs-true"></a>Voorspeld versus waar

Voor regressie-en Voorspellings experimenten wordt de relatie tussen de doel functie (True/actual values) en de voor spellingen van het model in de grafiek getekend. De werkelijke waarden zijn binning langs de x-as en voor elke bin wordt de gemiddelde voorspelde waarde getekend met fout balken. Zo kunt u zien of een model is gericht op het voors pellen van bepaalde waarden. De lijn geeft de gemiddelde voor spelling en het gearceerde gebied de variantie van voor spellingen rond dat gemiddelde aan.

Vaak heeft de meest voorkomende waarde de meest nauw keurige voor spelling met de laagste variantie. De afstand van de trend lijn van de ideale `y = x` lijn waar enkele echte waarden zijn, is een goede meting van de model prestaties op uitschieters. U kunt het histogram onder aan de grafiek gebruiken om de werkelijke gegevens distributie te omraden. Met meer voor beelden van gegevens, waarbij de distributie verspreid is, kunnen de prestaties van het model worden verbeterd op ongeziene gegevens.

In dit voor beeld ziet u dat het betere model een voorspelde en ware lijn heeft die dichter bij de ideale `y = x` lijn staat.

### <a name="predicted-vs-true-chart-for-a-good-model"></a>Voor speld versus waar grafiek voor een goed model
![Voor speld versus waar grafiek voor een goed model](./media/how-to-understand-automated-ml/chart-predicted-true-good.png)

### <a name="predicted-vs-true-chart-for-a-bad-model"></a>Voor speld versus waar-diagram voor een onjuist model
![Voor speld versus waar-diagram voor een onjuist model](./media/how-to-understand-automated-ml/chart-predicted-true-bad.png)

## <a name="model-explanations-and-feature-importances"></a>Uitleg bij het model en belang rijke functies

Hoewel de metrische gegevens voor de evaluatie van modellen en grafieken geschikt zijn voor het meten van de algemene kwaliteit van een model, kunt u controleren welke gegevensset functies een model gebruikt om de voor spellingen te maken. Daarom biedt geautomatiseerd ML een dash board voor het interpreteren van modellen om de relatieve bijdragen van gegevensset-functies te meten en te rapporteren.

![Functie urgentie](./media/how-to-understand-automated-ml/how-to-feature-importance.gif)

Als u het dash board voor interpretaties wilt weer geven in de studio:

1. Meld u aan bij [de Studio](https://ml.azure.com/) en navigeer naar uw werk ruimte
2. Selecteer in het menu links **experimenten**
3. Selecteer uw experiment in de lijst met experimenten
4. Selecteer in de tabel aan de onderkant van de pagina een AutoML-uitvoering
5. Selecteer op het tabblad **modellen** de **algoritme naam** voor het model dat u wilt uitleggen
6. Op het tabblad **uitleg** ziet u mogelijk een uitleg die al is gemaakt als het model het beste is
7. Als u een nieuwe uitleg wilt maken, selecteert u **model uitleggen** en selecteert u de externe Compute waarmee uitleg moet worden berekend.

> [!NOTE]
> Het ForecastTCN-model wordt momenteel niet ondersteund door automatische ML-uitleg en andere prognose modellen hebben mogelijk beperkte toegang tot hulpprogram ma's voor interpretatie.

## <a name="next-steps"></a>Volgende stappen
* Probeer de [voorbeeld notitieblokken voor automatische machine learning-modellen](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model).
* Meer informatie over de [verantwoordelijke AI-aanbiedingen in automatische milliliters](how-to-machine-learning-interpretability-automl.md).
* Neem voor automatische ML specifieke vragen contact op met askautomatedml@microsoft.com .
