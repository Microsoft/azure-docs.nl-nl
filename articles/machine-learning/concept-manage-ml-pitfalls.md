---
title: Vermijd het overdoen van & niet-sluitende gegevens met AutoML
titleSuffix: Azure Machine Learning
description: Identificeer en beheer algemene Valk uilen van ML-modellen met geautomatiseerde machine learning oplossingen van Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: nibaccam
author: nibaccam
ms.author: nibaccam
ms.date: 04/09/2020
ms.openlocfilehash: ba85bda1e322d3efd467527b48bd4cd90eb7ce8c
ms.sourcegitcommit: fec60094b829270387c104cc6c21257826fccc54
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/09/2020
ms.locfileid: "96922622"
---
# <a name="prevent-overfitting-and-imbalanced-data-with-automated-machine-learning"></a>Voor komen dat gegevens met automatische machine learning worden verdeeld en gebalanceerd.

Gegevens die groter zijn dan en niet in balans staan, zijn veelvoorkomende Valk uilen wanneer u machine learning modellen bouwt. Azure Machine Learning geautomatiseerde machine learning biedt standaard grafieken en metrische gegevens waarmee u deze Risico's kunt herkennen en de best practices kunt implementeren om ze te helpen oplossen. 

## <a name="identify-over-fitting"></a>Meerwaarde van installatie identificeren

Over het machine learning vindt plaats wanneer een model te groot is voor de opleidings gegevens en daardoor niet nauw keurig kan worden voor speld op onduidelijke test gegevens. Met andere woorden, het model heeft simpelweg specifieke patronen en ruis in de trainings gegevens, maar is niet flexibel genoeg om voor spellingen te doen op echte gegevens.

Bekijk de volgende getrainde modellen en de bijbehorende trein-en test keurigheden.

| Modelleren | Nauw keurigheid trainen | Nauw keurigheid testen |
|-------|----------------|---------------|
| A | 99,9% | 95% |
| B | 87% | 87% |
| C | 99,9% | 45% |

Als model **a** wordt overwogen, is er sprake van een veelvoorkomende, niet-gemeen schappelijke gegevens. De nauw keurigheid van de test moet echter altijd kleiner zijn dan de nauw keurigheid van de training, en het onderscheid voor meer informatie over de juiste aanpassing is afhankelijk van *wat* nauw keuriger is. 

Bij het vergelijken van modellen **a** en **B** is model **a** een beter model omdat het een hogere nauw keurigheid voor de test heeft en hoewel de nauw keurigheid van de test op 95% iets lager is. U hebt model **B** niet gewoon gekozen, omdat de keurigheden van de trein en test dichter bij elkaar komen.

Model **C** staat voor een duidelijk beeld van over-montage; de nauw keurigheid van de training is zeer hoog, maar de nauw keurigheid van de test is nergens bijna zo hoog. Dit onderscheid is subjectief, maar komt van kennis van uw probleem en gegevens, en van de omvang van de fout zijn acceptabel.

## <a name="prevent-over-fitting"></a>Voor komen dat de installatie meer wordt uitgevoerd

In de meeste gevallen van egregious wordt ervan uitgegaan dat de combi Naties van functie waarden die tijdens de training worden weer gegeven, altijd resulteren in exact dezelfde uitvoer voor het doel.

De beste manier om te voor komen dat u aan de slag gaat, is het volgen van de best practices van ML, waaronder:

* Meer trainings gegevens gebruiken en statistische afwijking elimineren
* Doel lekkage voor komen
* Minder functies gebruiken
* **Regularisatie en afstemming optimaliseren**
* **Complexiteits beperkingen voor modellen**
* **Kruisvalidatie**

In de context van automatische ML zijn de eerste drie bovenstaande items de **Best practices die u implementeert**. De laatste drie vetgedrukte items zijn de **Best practices waarmee automatische milliliters worden geïmplementeerd** . In andere instellingen dan automatische MILLILITERs zijn alle zes aanbevolen procedures te volgen om te voor komen dat modellen worden overbelast.

### <a name="best-practices-you-implement"></a>Aanbevolen procedures voor het implementeren van

Het gebruik van **meer gegevens** is de eenvoudigste en best mogelijke manier om te voor komen dat u aan de slag gaat, en naarmate een extra bonus doorgaans nauw keuriger wordt. Wanneer u meer gegevens gebruikt, is het moeilijker voor het model om exacte patronen te onthouden. het is ook mogelijk om oplossingen te bereiken die flexibeler zijn om meer voor waarden te bieden. Het is ook belang rijk om **statistische afwijking** te herkennen, om ervoor te zorgen dat uw trainings gegevens geen geïsoleerde patronen bevatten die niet voor komen in Live-Voorspellings gegevens. Dit scenario kan moeilijk worden opgelost, omdat er mogelijk geen verdere aanpassing is tussen uw Train-en test sets, maar er mogelijk sprake is van een overmatige installatie van gegevens in vergelijking met live test.

Het **doel lekkage** is een soortgelijk probleem, waar u mogelijk geen overmatige installatie van de sets voor Train/Test ziet, maar deze wordt weer gegeven bij voor spellingen. Lekkage van het doel treedt op wanneer uw model ' vals speelt ' tijdens de training wordt uitgevoerd door toegang te hebben tot gegevens die niet normaal gesp roken bij voor spelling moeten zijn. Als het probleem zich bijvoorbeeld voordoet om op maandag te voors pellen wat een basisproduct prijs op vrijdag is, maar een van uw functies per ongeluk opgenomen gegevens uit donderdag, worden de gegevens in het model niet in de toekomst weer gegeven. Lekkage van het doel is een eenvoudige fout om te missen, maar wordt vaak gekenmerkt door een abnormaal hoge nauw keurigheid van uw probleem. Als u probeert aandelen koers te voors pellen en een model met een nauw keurigheid van 95% hebt getraind, is er waarschijnlijk ergens in uw functies lekkage opgetreden.

Het **verwijderen van functies** kan ook helpen om te voor komen dat het model te veel velden bevat om specifieke patronen te onthouden, waardoor het flexibeler is. Het kan lastig zijn om kwantitatief te meten, maar als u functies kunt verwijderen en dezelfde nauw keurigheid wilt behouden, hebt u waarschijnlijk het model flexibeler en hebt u het risico van meerwaarde verbeterd.

### <a name="best-practices-automated-ml-implements"></a>Aanbevolen procedures voor de implementatie van automatische MILLILITERs

**Regularisatie** is het proces van het minimaliseren van een kosten functie voor het bestraffen van complexe en meer bewaarde modellen. Er zijn verschillende soorten regularisatie-functies, maar in het algemeen zijn ze allemaal in het gepaste formaat, variantie en complexiteit van het model. Automatische MILLILITERs maakt gebruik van L1 (lasso), L2 (tanden) en ElasticNet (L1 en L2) in verschillende combi Naties met verschillende model afstemming-instellingen die de voor-en hand leiding regelen. In eenvoudige termen variëren automatische MILLILITERs hoeveel een model wordt gereguleerd en wordt het beste resultaat gekozen.

Automatische ML implementeert ook de **complexiteits beperkingen** van expliciete modellen om te voor komen dat ze niet meer worden toegepast. In de meeste gevallen is deze implementatie specifiek voor beslissings structuur of forest-algoritmen, waarbij de maximale diepte van afzonderlijke structuren beperkt is en het totale aantal gebruikte bomen in forest-of ensemble-technieken beperkt is.

**Kruis validatie (AVK)** is het proces van het nemen van veel subsets van uw volledige trainings gegevens en het trainen van een model op elke subset. Het is een goed idee dat een model ' blij ' kan krijgen en een uitstekende nauw keurigheid heeft met één subset, maar door veel subsets te gebruiken, wordt deze hoge nauw keurigheid niet elke keer door het model gerealiseerd. Bij het uitvoeren van AVK geeft u een evaluatie-gegevensset voor validatie op, geeft u uw CV-vouwen (aantal subsets) op en geautomatiseerd ML traint uw model en verlaagt u Hyper parameters om de fout in uw valideringsset te minimaliseren. Een CV-vouw is mogelijk te groot, maar door veel ervan te gebruiken, wordt de kans kleiner dat uw uiteindelijke model over de juiste plaats komt. De balans is dat de AVK een langere opleidings tijd en dus meer kosten in beslag neemt, omdat u deze voor elke *n* -CV-subsets eenmaal hoeft te trainen. 

> [!NOTE]
> Kruis validatie is standaard niet ingeschakeld. Deze moet worden geconfigureerd in de instellingen voor automatische ML. Nadat Kruis validatie is geconfigureerd en er een validatie gegevensset is opgegeven, wordt het proces voor u geautomatiseerd. Meer informatie over de [configuratie voor meerdere validaties in automatische ml](how-to-configure-cross-validation-data-splits.md)

<a name="imbalance"></a>

## <a name="identify-models-with-imbalanced-data"></a>Modellen met niet-sluitende gegevens identificeren

Gegevens die in het algemeen worden gebalanceerd, worden doorgaans gevonden in gegevens voor machine learning classificatie scenario's en verwijzen naar gegevens die een onevenredige verhouding van waarnemingen in elke klasse bevatten. Dit onevenwichtige kan leiden tot een onverantwoord waargenomen positief effect van de nauw keurigheid van een model, omdat de invoer gegevens van invloed zijn op één klasse, wat resulteert in het getrainde model om die afwijking te simuleren. 

Daarnaast worden automatisch de volgende grafieken gegenereerd, die u kunnen helpen inzicht te krijgen in de juistheid van de classificaties van uw model en modellen te identificeren die mogelijk van invloed zijn op gegevens die niet in evenwicht zijn.

Grafiek| Description
---|---
[Verwar ring matrix](how-to-understand-automated-ml.md#confusion-matrix)| Hiermee worden de juist geclassificeerde labels geëvalueerd op basis van de daad werkelijke labels van de gegevens. 
[Precisie-intrekken](how-to-understand-automated-ml.md#precision-recall-curve)| Evalueert de verhouding van de juiste labels op basis van de verhouding van gevonden label exemplaren van de gegevens 
[ROC curven](how-to-understand-automated-ml.md#roc-curve)| Evalueert de verhouding van de juiste labels op basis van de verhouding van ONWAAR-positieve labels.

## <a name="handle-imbalanced-data"></a>Niet-sluitende gegevens verwerken 

Als onderdeel van het vereenvoudigen van de machine learning-werk stroom, **heeft geautomatiseerd ml ingebouwde mogelijkheden** voor het oplossen van niet-sluitende gegevens zoals, 

- Een **kolom** met het gewicht: automatische ml biedt ondersteuning voor een kolom met gewichten als invoer, waardoor rijen in de gegevens kunnen worden gewogen of waarmee een klasse meer of minder ' belang rijk ' kan worden gemaakt.

- De algoritmen die door automatische MILLILITERs worden gebruikt, kunnen niet worden geevenwichtd wanneer het aantal steek proeven in de klasse minderheid gelijk is aan of minder is dan 20% van het aantal steek proeven in de meerderheids klasse, waarbij de minderheids klasse naar de enige steek proef verwijst. Vervolgens voert AutoML een experiment uit met subvoorbeeld gegevens om te controleren of het gebruik van klasse-gewichten dit probleem verhelpt en de prestaties te verbeteren. Als er tijdens dit experiment een betere prestaties worden vastgesteld, wordt deze oplossing toegepast.

- Gebruik prestatie metrieken die beter werken met gegevens die niet in balans zijn. De AUC_weighted is bijvoorbeeld een primaire metriek waarmee de bijdrage van elke klasse wordt berekend op basis van het relatieve aantal steek proeven dat deze klasse vertegenwoordigt, en daarom robuuster is tegen onevenwichtigheid.

De volgende technieken zijn aanvullende opties voor het afhandelen van niet-sluitende gegevens **buiten de automatische milliliters**. 

- Hersampling naar zelfs het onevenwicht van de klasse, hetzij door de kleinere klassen te bemonsteren of de grotere klassen te verlagen. Deze methoden vereisen expertise om te verwerken en te analyseren.

- Controleer de metrische gegevens over de prestaties van niet-sluitende informatie. De F1-Score is bijvoorbeeld het harmonische gemiddelde van Precision en intrekken. Nauw keurigheid meet de nauw keurigheid van een classificatie, waarbij een grotere nauw keurigheid minder fout-positieven aangeeft, terwijl intrekken meet de volledigheid van een classificatie, waarbij een hogere inhaal bewerking minder valse negatieven aanduidt.

## <a name="next-steps"></a>Volgende stappen

Bekijk voor beelden en leer hoe u modellen bouwt met geautomatiseerde machine learning:

+ Volg de [zelf studie: automatisch een regressie model trainen met Azure machine learning](tutorial-auto-train-models.md)

+ De instellingen voor automatische training-experiment configureren:
  + In Azure Machine Learning Studio [gaat u](how-to-use-automated-ml-for-ml-models.md)als volgt te werk.
  + Gebruik de python-SDK om de [volgende stappen uit te voeren](how-to-configure-auto-train.md).


