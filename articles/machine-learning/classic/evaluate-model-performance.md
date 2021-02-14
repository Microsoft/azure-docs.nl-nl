---
title: 'ML Studio (klassiek): Evalueer & Kruis validatie modellen-Azure'
description: Meer informatie over de metrische gegevens die u kunt gebruiken om de model prestaties in Azure Machine Learning Studio (klassiek) te bewaken.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18, previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/20/2017
ms.openlocfilehash: b2ca78d30659fce6e4246c81216cae94b404955e
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100520014"
---
# <a name="evaluate-model-performance-in-azure-machine-learning-studio-classic"></a>Prestaties van model in Azure Machine Learning Studio evalueren (klassiek)

**VAN TOEPASSING OP:**  ![Van toepassing op.](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio (klassiek) ![Niet van toepassing op.](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)


In dit artikel vindt u meer informatie over de metrische gegevens die u kunt gebruiken om de model prestaties in Azure Machine Learning Studio (klassiek) te bewaken.  Het evalueren van de prestaties van een model is een van de belangrijkste fasen in het data Science-proces. Hiermee wordt aangegeven hoe de Score (voor spellingen) van een gegevensset is geslaagd door een getraind model. Azure Machine Learning Studio (klassiek) ondersteunt model evaluatie via twee van de belangrijkste machine learning modules: 
+ [Model evalueren][evaluate-model] 
+ [Model kruislings valideren][cross-validate-model]

Met deze modules kunt u zien hoe uw model presteert volgens een aantal metrische gegevens die vaak worden gebruikt in machine learning en statistieken.

Het evalueren van modellen moet worden overwogen in combi natie met:
+ [Para meters optimaliseren voor algoritmen](algorithm-parameters-optimize.md)
+ [Interpreteerbaarheid van modellen](interpret-model-results.md)

Er worden drie veelvoorkomende leer scenario's weer gegeven: 
* regressie
* binaire classificatie 
* classificatie met meer klassen


## <a name="evaluation-vs-cross-validation"></a>Evaluatie versus Kruis validatie
Evaluatie en kruis validatie zijn standaard manieren om de prestaties van uw model te meten. Ze genereren zowel evaluatie gegevens die u kunt controleren of vergelijken met die van andere modellen.

Bij het evalueren van het [model][evaluate-model] wordt een gescoorde gegevensset verwacht als invoer (of twee voor het geval u de prestaties van twee verschillende modellen wilt vergelijken). Daarom moet u uw model trainen met behulp van de [Train model][train-model] module en voor spellingen maken voor een aantal gegevensset met behulp van de module [score model][score-model] voordat u de resultaten kunt evalueren. De evaluatie is gebaseerd op de gescoorde labels/waarschijnlijkheid samen met de werkelijke labels, die allemaal worden uitgevoerd door de module [score model][score-model] .

U kunt ook Kruis validatie gebruiken om een aantal verwerkings bewerkingen (10 vouwen) automatisch uit te voeren op verschillende subsets van de invoer gegevens. De invoer gegevens worden opgesplitst in tien delen, waarbij één is gereserveerd voor testen en de andere 9 voor training. Dit proces wordt 10 keer herhaald en de metrische gegevens van de evaluatie worden gemiddeld. Zo kunt u bepalen hoe goed een model generaliseert voor nieuwe gegevens sets. De module voor het [valideren van meerdere validaties][cross-validate-model] maakt deel uit van een niet-traind model en een gelabelde gegevensset en voert de resultaten van de evaluatie van elk van de 10 vouwen, naast de gemiddelde resultaten.

In de volgende secties bouwen we eenvoudige regressie-en classificatie modellen en evalueren ze hun prestaties met behulp van zowel het [beoordelings model][evaluate-model] als de [Kruis validatie model][cross-validate-model] modules.

## <a name="evaluating-a-regression-model"></a>Een regressie model evalueren
Stel dat we de prijs van een auto willen voors pellen met behulp van functies zoals afmetingen, paarden kracht, Engine specificaties, enzovoort. Dit is een typisch regressie probleem, waarbij de doel variabele (*prijs*) een doorlopende numerieke waarde is. We kunnen een lineair regressie model passend maken dat, op basis van de functie waarden van een bepaalde auto, de prijs van die auto kan voors pellen. Dit regressie model kan worden gebruikt om te scoren op dezelfde gegevensset die we hebben opgeleid. Zodra we de gedicteerde auto prijzen hebben, kunnen we de prestaties van het model evalueren door te kijken hoeveel de voor spellingen afwijken van de werkelijke prijzen op gemiddeld. Om dit te illustreren, gebruiken we de beschik bare *gegevens over auto Mobile price data (RAW)* in het gedeelte **opgeslagen gegevens sets** in machine learning Studio (klassiek).

### <a name="creating-the-experiment"></a>Het experiment maken
Voeg de volgende modules toe aan uw werk ruimte in Azure Machine Learning Studio (klassiek):

* Autoprijsgegevens (onbewerkt)
* [Lineaire regressie][linear-regression]
* [Trainingsmodel][train-model]
* [Score Model][score-model]
* [Model evalueren][evaluate-model]

Verbind de poorten zoals hieronder wordt weer gegeven in afbeelding 1 en stel de kolom Label van de module [Train model][train-model] in op *prijs*.

![Een regressie model evalueren](./media/evaluate-model-performance/1.png)

Afbeelding 1. Een regressie model evalueren.

### <a name="inspecting-the-evaluation-results"></a>De evaluatie resultaten controleren
Nadat u het experiment hebt uitgevoerd, kunt u op de uitvoer poort van de module [Evaluate model][evaluate-model] klikken en *visualiseren* selecteren om de resultaten van de evaluatie te bekijken. De metrische gegevens voor de evaluatie die beschikbaar zijn voor regressie modellen zijn: de *absolute fout*, het *hoofd knooppunt absolute fout*, de *relatieve absolute fout*, de *relatieve kwadratische fout* en de *coëfficiënt van de bepaling*.

De term ' error ' vertegenwoordigt het verschil tussen de voorspelde waarde en de waarde True. De absolute waarde of het kwadraat van dit verschil wordt doorgaans berekend om de totale omvang van de fout voor alle instanties vast te leggen, omdat het verschil tussen de voorspelde en de werkelijke waarde in sommige gevallen negatief kan zijn. De metrische fout gegevens meten de voorspellende prestaties van een regressie model in termen van de gemiddelde afwijking van de voor spellingen van de werkelijke waarden. Lagere fout waarden betekent dat het model nauw keuriger is bij het maken van voor spellingen. De totale fout waarde nul betekent dat het model perfect past bij de gegevens.

De determinatie coëfficiënt, ook wel R-kwadraat genoemd, is een standaard manier om te meten hoe goed het model past bij de gegevens. Het kan worden geïnterpreteerd als het deel van de variatie dat door het model wordt uitgelegd. In dit geval is een hogere verhouding beter, waarbij 1 wijst op een perfecte plaats.

![Metrische regressie-evaluatie gegevens](./media/evaluate-model-performance/2.png)

Afbeelding 2. Metrische regressie grootheden.

### <a name="using-cross-validation"></a>Kruis validatie gebruiken
Zoals eerder vermeld, kunt u automatisch herhaalde training, waardering en evaluaties uitvoeren met behulp van de [model module kruislings valideren][cross-validate-model] . Alles wat u nodig hebt in dit geval is een gegevensset, een niet-traind model en een [model module met kruis validatie][cross-validate-model] (zie afbeelding hieronder). U moet de kolom Label instellen op *prijs* in de eigenschappen van de [model module kruislings valideren][cross-validate-model] .

![Een regressie model Kruis valideren](./media/evaluate-model-performance/3.png)

Afbeelding 3. Een regressie model kruislings valideren.

Nadat u het experiment hebt uitgevoerd, kunt u de resultaten van de evaluatie controleren door te klikken op de juiste uitvoer poort van de module voor het [kruislings valideren][cross-validate-model] van het model. Hiermee krijgt u een gedetailleerd overzicht van de metrische gegevens voor elke iteratie (vouw) en de gemiddelde resultaten van elk van de metrische gegevens (afbeelding 4).

![Kruis validatie resultaten van een regressie model](./media/evaluate-model-performance/4.png)

Afbeelding 4: Kruis validatie resultaten van een regressie model.

## <a name="evaluating-a-binary-classification-model"></a>Een binair classificatie model evalueren
In een scenario met een binaire indeling heeft de doel variabele slechts twee mogelijke resultaten, bijvoorbeeld: {0, 1} of {False, True}, {Negative, positief}. Stel dat u een gegevensset hebt gekregen van volwassen mede werkers met enkele demografische en werkgelegenheids variabelen en dat u wordt gevraagd om het inkomens niveau te voors pellen, een binaire variabele met de waarden {"<= 50 K", ">50 K"}. Met andere woorden, de klasse Negative vertegenwoordigt de werk nemers die kleiner zijn dan of gelijk zijn aan 50 K per jaar, en de positieve klasse vertegenwoordigt alle andere werk nemers. Net als bij het regressie scenario zullen we een model trainen, bepaalde gegevens beoordelen en de resultaten evalueren. Het belangrijkste verschil is hier de keuze van metrische gegevens Azure Machine Learning Studio (klassiek) berekeningen en uitvoer. Om het scenario voor Voorspellings niveau te illustreren, gebruiken we de [volwassene](https://archive.ics.uci.edu/ml/datasets/Adult) gegevensset voor het maken van een studio-experiment (klassiek) en evalueren we de prestaties van een logistiek regressie model met twee klassen, een veelgebruikte binaire classificatie.

### <a name="creating-the-experiment"></a>Het experiment maken
Voeg de volgende modules toe aan uw werk ruimte in Azure Machine Learning Studio (klassiek):

* Gegevensset Binaire classificatie voor volwassen Census-inkomen
* [Logistieke regressie met twee klassen][two-class-logistic-regression]
* [Trainingsmodel][train-model]
* [Score Model][score-model]
* [Model evalueren][evaluate-model]

Verbind de poorten zoals hieronder wordt weer gegeven in afbeelding 5 en stel de kolom Label van de module [Train model][train-model] in op *inkomen*.

![Een binair classificatie model evalueren](./media/evaluate-model-performance/5.png)

Afbeelding 5. Een binair classificatie model evalueren.

### <a name="inspecting-the-evaluation-results"></a>De evaluatie resultaten controleren
Nadat u het experiment hebt uitgevoerd, kunt u op de uitvoer poort van de module [Evaluate model][evaluate-model] klikken en *visualiseren* selecteren om de resultaten van de evaluatie te bekijken (afbeelding 7). De metrische gegevens van de evaluatie versie die beschikbaar zijn voor binaire classificatie modellen zijn: *nauw keurigheid*, *precisie*, *intrekken*, *F1 Score* en *AUC*. Daarnaast wordt in de module een Verwar ring-matrix uitgevoerd met het aantal True-positieven, fout-negatieven, fout-positieven en echte negatieven, maar ook voor *Roc*, *precisie/terughalen* en *Lift* curven.

Nauw keurigheid is gewoon het aandeel van de juiste geclassificeerde instanties. Doorgaans is dit de eerste metriek die u bekijkt bij het evalueren van een classificatie. Wanneer de test gegevens echter niet in balans zijn (waarbij de meeste instanties tot een van de klassen behoren), of als u geïnteresseerd bent in de prestaties van een van de klassen, legt de nauw keurigheid niet echt de effectiviteit van een classificatie vast. In het scenario voor de classificatie van het inkomens niveau wordt ervan uitgegaan dat u op sommige gegevens test, waarbij 99% van de instanties de mensen vertegenwoordigen die minder dan of gelijk zijn aan 50.000 per jaar. Het is mogelijk om een 0,99 nauw keurigheid te krijgen door de klasse ' <= 50.000 ' voor alle exemplaren te voors pellen. De classificatie in dit geval lijkt een goede taak te zijn, maar in werkelijkheid is het niet mogelijk om een van de hoge inkomsten van personen (de 1%) te classificeren. correct.

Daarom is het handig om extra metrische gegevens te berekenen waarmee meer specifieke aspecten van de evaluatie worden vastgelegd. Voordat u de details van deze metrische gegevens overgaat, is het belang rijk dat u de Verwar ring-matrix van een binaire classificatie-evaluatie begrijpt. De klassen labels in de Trainingsset kunnen alleen worden uitgevoerd op twee mogelijke waarden, die meestal positief of negatief zijn. De positieve en negatieve instanties die een classificatie voorspeld op de juiste wijze worden gedicteerd, worden respectievelijk True-positieven (TP) en True Negative (TN) genoemd. Op dezelfde manier worden de onjuist geclassificeerde instanties ' fout-positieven (FP) en ' onwaar ' negatieven (FN) genoemd. De Verwar ring matrix is een tabel waarin het aantal exemplaren wordt weer gegeven dat onder elk van deze vier categorieën valt. Azure Machine Learning Studio (klassiek) beslist automatisch welke van de twee klassen in de gegevensset de positieve klasse zijn. Als de klassen labels Booleaans of geheel getal zijn, krijgen de labels ' True ' en ' 1 ' instanties de positieve klasse toegewezen. Als de labels teken reeksen zijn, zoals met de gegevensset baten, worden de labels alfabetisch gesorteerd en wordt het eerste niveau als een negatieve klasse gekozen, terwijl het tweede niveau de positieve klasse is.

![Verwar ring matrix voor binaire classificatie](./media/evaluate-model-performance/6a.png)

Afbeelding 6. Verwar ring matrix voor binaire classificatie.

Als u terugkeert naar het probleem met de winst classificatie, willen we verschillende evaluatie vragen stellen die ons helpen de prestaties van de gebruikte classificatie te begrijpen. Een natuurlijke vraag is: ' buiten de personen voor wie het model is voorspeld voor het verdienen van >50 K (TP + FP), hoeveel goed zijn geclassificeerd (TP)? ' Deze vraag kan worden beantwoord door de **nauw keurigheid** van het model te bekijken. Dit is het aandeel van de positieve waarde die juist is geclassificeerd: TP/(TP + FP). Een andere veelgestelde vraag is ' van alle hoge voor delen van werk nemers met inkomsten >50.000 (TP + FN), hoeveel had de classificatie correct geclassificeerd (TP) '. Dit is in feite het **intrekken**, of de werkelijke positieve factor: TP/(TP + FN) van de classificatie. U zult merken dat er sprake is van een duidelijke afweging tussen Precision en intrekken. Als er bijvoorbeeld sprake is van een relatief gebalanceerde gegevensset, zou een classificatie die voornamelijk positieve instanties voorspeld, een hoge terugroep zou hebben, maar een lage nauw keurigheid als veel van de negatieve instanties verkeerd worden geclassificeerd, wat resulteert in een groot aantal fout-positieven. Als u een overzicht wilt zien van de verschillen tussen deze twee gegevens, klikt u op **de curve voor** de resultaten van de evaluatie pagina (linksboven in afbeelding 7).

![Resultaten van evaluatie van binaire classificatie](./media/evaluate-model-performance/7.png)

Afbeelding 7. Evaluatie resultaten van binaire classificatie.

Een andere gerelateerde metrische waarde die vaak wordt gebruikt, is de **F1-Score**, die zowel nauw keurig als in aanmerking komt. Het harmonische gemiddelde van deze twee metrische gegevens en wordt als volgt berekend: F1 = 2 (Precision x intrekken)/(Precision + intrekken). De F1-Score is een goede manier om de evaluatie in één getal samen te vatten, maar het is altijd een goed idee om nauw keurig te kijken en samen te halen om beter inzicht te krijgen in hoe een classificatie zich gedraagt.

Daarnaast kan een de werkelijke positieve verhouding controleren, vergeleken met het onjuiste positieve rente nummer in de curve van de **ontvanger** en het bijbehorende **gebied onder de waarde curve (AUC)** . Hoe dichter deze curve zich bevindt in de linkerbovenhoek, des te beter is de prestaties van de classificatie (waardoor de werkelijke positieve snelheid wordt gemaximaliseerd terwijl het onjuiste positieve tempo wordt geminimaliseerd). Curves die zich dicht bij de diagonaal van het waarnemings punt bevinden, zijn het resultaat van classificaties die doorgaans voor spellingen doen die dicht bij wille keurig raden zijn.

### <a name="using-cross-validation"></a>Kruis validatie gebruiken
Net als in het regressie voorbeeld kunnen we Kruis validatie uitvoeren om herhaaldelijk verschillende subsets van de gegevens automatisch te trainen, te beoordelen en te evalueren. Op dezelfde manier kunnen we de [model module kruislings valideren][cross-validate-model] , een niet-uitgetraind logistiek regressie model en een gegevensset gebruiken. De label kolom moet worden ingesteld op *inkomsten* in de eigenschappen van de [model module kruislings valideren][cross-validate-model] . Na het uitvoeren van het experiment en het klikken op de juiste uitvoer poort van de module voor het [kruislings valideren][cross-validate-model] van het model, kunnen we naast het gemiddelde en de standaard afwijking van elke vouw de metrische waarden voor de binaire classificatie zien. 

![Een binair classificatie model Kruis valideren](./media/evaluate-model-performance/8.png)

Afbeelding 8. Een binair classificatie model valideren.

![Kruis validatie resultaten van een binaire classificatie](./media/evaluate-model-performance/9.png)

Afbeelding 9. Kruis validatie resultaten van een binaire classificatie.

## <a name="evaluating-a-multiclass-classification-model"></a>Een classificatie model voor multi klassen evalueren
In dit experiment gebruiken we de populaire [Iris](https://archive.ics.uci.edu/ml/datasets/Iris "Iris") -gegevensset, die exemplaren van drie verschillende typen (klassen) van de Iris plant bevat. Er zijn vier onderdeel waarden (SEPA-lengte/breedte en grootte/breedte van het bloem) voor elk exemplaar. In de vorige experimenten hebben we de modellen getraind en getest met dezelfde gegevens sets. Hier gebruiken we de module voor het [splitsen van gegevens][split] om twee subsets van de gegevens te maken, te trainen op het eerste en de score en evalueren voor de tweede. De Iris-gegevensset is openbaar beschikbaar op de [icb machine learning-opslag plaats](https://archive.ics.uci.edu/ml/index.html)en kan worden gedownload met behulp van een module voor [gegevens import][import-data] .

### <a name="creating-the-experiment"></a>Het experiment maken
Voeg de volgende modules toe aan uw werk ruimte in Azure Machine Learning Studio (klassiek):

* [Gegevens importeren][import-data]
* [Beslissingsforest met meerdere klassen][multiclass-decision-forest]
* [Gegevens splitsen][split]
* [Trainingsmodel][train-model]
* [Score Model][score-model]
* [Model evalueren][evaluate-model]

Verbind de poorten zoals hieronder wordt weer gegeven in afbeelding 10.

Stel de index van de label kolom van de module [Train model][train-model] in op 5. De gegevensset heeft geen koprij, maar we weten dat de klassen labels zich in de vijfde kolom bevinden.

Klik op de module [gegevens importeren][import-data] en stel de eigenschap *gegevens bron* in op *Web-URL via http* en de *URL* naar http://archive.ics.uci.edu/ml/machine-learning-databases/iris/iris.data .

Stel het gedeelte van de instanties in dat moet worden gebruikt voor de training in de module voor het [splitsen van gegevens][split] (bijvoorbeeld 0,7).

![Een classificatie met meer klassen evalueren](./media/evaluate-model-performance/10.png)

Afbeelding 10. Een classificatie met meer klassen evalueren

### <a name="inspecting-the-evaluation-results"></a>De evaluatie resultaten controleren
Voer het experiment uit en klik op de uitvoer poort van het [Evalueer model][evaluate-model]. De resultaten van de evaluatie worden in dit geval weer gegeven in de vorm van een Verwar ring matrix. In de matrix worden de werkelijke versus voorspelde instanties voor alle drie de klassen weer gegeven.

![Resultaten van evaluatie van classificatie van multi klassen](./media/evaluate-model-performance/11.png)

Afbeelding 11. Resultaten van classificatie van multi klasse-evaluatie.

### <a name="using-cross-validation"></a>Kruis validatie gebruiken
Zoals eerder vermeld, kunt u automatisch herhaalde training, waardering en evaluaties uitvoeren met behulp van de [model module kruislings valideren][cross-validate-model] . U hebt een gegevensset, een niet-traind model en een [model module met kruis validatie][cross-validate-model] nodig (zie afbeelding hieronder). Opnieuw moet u de kolom Label van de module [model voor kruis validatie][cross-validate-model] (kolom index 5 in dit geval) instellen. Nadat u het experiment hebt uitgevoerd en op de juiste uitvoer poort van het [Kruis validatie model][cross-validate-model]hebt geklikt, kunt u de metrische waarden voor elke vouwen en de gemiddelde en standaard afwijking controleren. De metrische gegevens die hier worden weer gegeven, zijn vergelijkbaar met de waarden die in de binaire classificatie case worden besproken. Bij een classificatie met meervoudige klassen worden de werkelijke positieven/negatieven en de fout-positieven/negatieven echter uitgevoerd door te tellen op basis van per klasse, omdat er geen algemene positieve of negatieve klasse is. Bij het berekenen van de precisie of het terughalen van de klasse Iris-setosa wordt ervan uitgegaan dat dit de positieve klasse en alle andere als negatief is.

![Een classificatie model met meerdere klassen valideren](./media/evaluate-model-performance/12.png)

Afbeelding 12. Een classificatie model met meerdere klassen valideren.

![Kruis validatie resultaten van een classificatie model met meerdere klassen](./media/evaluate-model-performance/13.png)

Afbeelding 13. Kruis validatie resultaten van een classificatie model met meerdere klassen.

<!-- Module References -->
[cross-validate-model]: /azure/machine-learning/studio-module-reference/cross-validate-model
[evaluate-model]: /azure/machine-learning/studio-module-reference/evaluate-model
[linear-regression]: /azure/machine-learning/studio-module-reference/linear-regression
[multiclass-decision-forest]: /azure/machine-learning/studio-module-reference/multiclass-decision-forest
[import-data]: /azure/machine-learning/studio-module-reference/import-data
[score-model]: /azure/machine-learning/studio-module-reference/score-model
[split]: /azure/machine-learning/studio-module-reference/split-data
[train-model]: /azure/machine-learning/studio-module-reference/train-model
[two-class-logistic-regression]: /azure/machine-learning/studio-module-reference/two-class-logistic-regression