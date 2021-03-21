---
title: 'Model voor kruis validatie: module verwijzing'
titleSuffix: Azure Machine Learning
description: Gebruik de module voor het valideren van een model in Azure Machine Learning Designer om de schatting van de para meters voor classificatie-of regressie modellen te valideren.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/11/2020
ms.openlocfilehash: d4099ecf6e6bcc6654391e54292878393fb22914
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93421342"
---
# <a name="cross-validate-model"></a>Kruisvalidatie van model valideren

In dit artikel wordt beschreven hoe u de module voor kruis validatie kunt gebruiken in Azure Machine Learning Designer. *Kruis validatie* is een techniek die vaak wordt gebruikt in machine learning om zowel de variabiliteit van een gegevensset als de betrouw baarheid te beoordelen van een model dat via deze gegevens is getraind.  

De module voor kruis validatie maakt als invoer een gegevensset met een label, samen met een niet-uitgetrainde classificatie of regressie model. De gegevensset wordt onderverdeeld in een aantal subsets (*vouwen*), bouwt een model op elke vouw en retourneert vervolgens een set nauw keurige statistieken voor elke vouw. Door de nauw keurige statistieken voor alle vouwen te vergelijken, kunt u de kwaliteit van de gegevensset interpreteren. U kunt vervolgens begrijpen of het model gevoelig is voor variaties in de gegevens.  

Kruis valideren model retourneert ook voorspelde resultaten en waarschijnlijkheid voor de gegevensset, zodat u de betrouw baarheid van de voor spellingen kunt beoordelen.  

### <a name="how-cross-validation-works"></a>Hoe Kruis validatie werkt

1. Met kruis validatie worden trainings gegevens wille keurig verdeeld over vouwen. 

   Het algoritme wordt standaard ingesteld op 10 vouwen als u de gegevensset niet eerder hebt gepartitioneerd. Als u de gegevensset wilt splitsen in een ander aantal vouwen, kunt u de module [Partition en sample](partition-and-sample.md) gebruiken en aangeven hoeveel vouwen u wilt gebruiken.  

2.  In de module worden de gegevens in vouw 1 gereserveerd om te worden gebruikt voor validatie. (Dit wordt ook wel de *evaluatie-vouw* genoemd.) De module gebruikt de resterende vouwen om een model te trainen. 

    Als u bijvoorbeeld vijf vouwen maakt, genereert de module vijf modellen tijdens Kruis validatie. De module treinen elk model met behulp van vier vijfde van de gegevens. Elk model wordt getest op het resterende aantal van een vijfde.  

3.  Tijdens het testen van het model voor elke vouw, evalueert de module meerdere nauwkeurigheids statistieken. Welke statistieken de module gebruikt, is afhankelijk van het type model dat u wilt evalueren. Verschillende statistieken worden gebruikt om classificatie modellen en regressie modellen te evalueren.  

4.  Wanneer het proces voor het maken en evalueren van alle vouwen is voltooid, genereert het Kruis validatie model een set prestatie gegevens en gescoorde resultaten voor alle gegevens. Bekijk deze metrische gegevens om te zien of een enkele Vouw een hoge of lage nauw keurigheid heeft. 

### <a name="advantages-of-cross-validation"></a>Voor delen van kruis validatie

Een voor beeld van een gemeen schappelijke manier om een model te evalueren, is door de gegevens in een training te verdelen en te testen met behulp van [gesplitste gegevens](split-data.md)en vervolgens het model op de trainings gegevens te valideren. Kruis validatie biedt echter een aantal voor delen:  

-   Kruis validatie maakt gebruik van meer test gegevens.

    Kruis validatie meet de prestaties van het model met de opgegeven para meters in een grotere gegevens ruimte. Dat wil zeggen dat kruis validatie gebruikmaakt van de volledige trainings gegevensset voor zowel trainingen als evaluatie, in plaats van een deel. Als u daarentegen een model valideert met behulp van gegevens die zijn gegenereerd op basis van een wille keurige splitsing, wordt het model doorgaans alleen op 30 procent of minder van de beschik bare gegevens geëvalueerd.  

    Omdat intervalidatie treinen echter het model meerdere keren in een grotere gegevensset valideert, is het veel meer reken kracht. Het duurt veel langer dan het valideren van een wille keurige splitsing.  

-   Bij Kruis validatie worden zowel de gegevensset als het model geëvalueerd.

    Bij Kruis validatie wordt niet alleen de nauw keurigheid van een model gemeten. Het biedt u ook een idee van hoe representatief de gegevensset is en hoe gevoelig het model kan zijn voor variaties in de gegevens.  

## <a name="how-to-use-cross-validate-model"></a>Model voor meerdere validatie gebruiken

Kruis validatie kan veel tijd in beslag nemen als uw gegevensset groot is.  U kunt het model met meerdere validaties gebruiken in de eerste fase van het bouwen en testen van uw model. In die fase kunt u de goed keurigheid van de model parameters evalueren (ervan uitgaande dat de reken tijd Maxi maal is). U kunt vervolgens uw model trainen en evalueren met behulp van de ingestelde para meters met het [Train model](train-model.md) en de [model modules evalueren](evaluate-model.md) .

In dit scenario moet u het model trainen en testen met behulp van het model kruislings valideren.

1. Voeg de module model voor kruis validatie toe aan uw pijp lijn. U kunt het vinden in Azure Machine Learning designer in de categorie **beoordeling & evaluatie** van het model. 

2. Verbind de uitvoer van een classificatie of regressie model. 

    Als u bijvoorbeeld **twee klassen hebt gestimuleerd beslissings structuur** voor classificatie, configureert u het model met de gewenste para meters. Sleep vervolgens een connector van de niet- **getrainde model** poort van de classificatie naar de overeenkomende poort van kruislings validate model. 

    > [!TIP] 
    > U hoeft het model niet te trainen omdat Kruis validatie het model automatisch als onderdeel van de evaluatie ondertraint.  
3.  Verbind op de **gegevensset** -poort van kruislings valideren model een trainings gegevensset met een label.  

4.  Klik in het rechter paneel van het model Kruis validatie op **kolom bewerken**. Selecteer de enkelvoudige kolom die het klassen label of de voorspel bare waarde bevat. 

5. Stel een waarde in voor de **wille keurige Seed** -para meter als u de resultaten van kruis validatie wilt herhalen in opeenvolgende uitvoeringen van dezelfde gegevens.  

6. Verzend de pijp lijn.

7. Zie de sectie met [resultaten](#results) voor een beschrijving van de rapporten.

## <a name="results"></a>Resultaten

Nadat alle iteraties zijn voltooid, maakt het Kruis validatie model scores voor de volledige gegevensset. Er worden ook prestatie gegevens gemaakt die u kunt gebruiken om de kwaliteit van het model te beoordelen.

### <a name="scored-results"></a>Gescoorde resultaten

De eerste uitvoer van de module levert de bron gegevens voor elke rij, samen met enkele voorspelde waarden en gerelateerde kansen. 

Als u de resultaten wilt weer geven, klikt u in de pijp lijn met de rechter muisknop op de model module Kruis valideren. Selecteer **gescoorde resultaten visualiseren**.

| Nieuwe kolomnaam      | Beschrijving                              |
| -------------------- | ---------------------------------------- |
| Labels met een score        | Deze kolom wordt toegevoegd aan het einde van de gegevensset. Het bevat de voorspelde waarde voor elke rij. |
| Gescoorde kansen | Deze kolom wordt toegevoegd aan het einde van de gegevensset. Deze geeft de geschatte waarschijnlijkheid van de waarde in de **gescoorde labels** aan. |
| Vouw nummer          | Hiermee wordt de op nul gebaseerde index van de vouw aangegeven waarmee elke rij met gegevens tijdens Kruis validatie werd toegewezen. |

 ### <a name="evaluation-results"></a>Evaluatieresultaten

Het tweede rapport is gegroepeerd op vouwen. Houd er rekening mee dat tijdens de uitvoering door Kruis valideren model de opleidings gegevens wille keurig splitst in *n* vouwen (standaard 10). In elke iteratie over de gegevensset gebruikt het Kruis validatie model één vouw als een validatie gegevensset. De overige *n-1* vouwen worden gebruikt voor het trainen van een model. Elk van de *n* -modellen wordt getest op basis van de gegevens in alle andere vouwen.

In dit rapport worden de vouwen weer gegeven op index waarde, in oplopende volg orde.  Als u wilt sorteren op een andere kolom, kunt u de resultaten opslaan als een gegevensset.

Als u de resultaten wilt weer geven, klikt u in de pijp lijn met de rechter muisknop op de model module Kruis valideren. Selecteer **evaluatie resultaten visualiseren door te vouwen**.


|Kolomnaam| Beschrijving|
|----|----|
|Vouw nummer| Een id voor elke vouw. Als u vijf vouwen hebt gemaakt, zijn er vijf subsets met gegevens, genummerd van 0 tot 4.
|Aantal voor beelden in vouwen|Het aantal rijen dat aan elke vouw is toegewezen. Ze moeten ongeveer gelijk zijn. |


De module bevat ook de volgende metrische gegevens voor elke vouw, afhankelijk van het type model dat u wilt evalueren: 

+ **Classificatie modellen**: precisie, intrekken, F-Score, AUC, nauw keurigheid  

+ **Regressie modellen**: absolute fout, wortel gemiddelde fout, relatieve absolute fout, relatieve kwadraat fout en coëfficiënt van bepaling


## <a name="technical-notes"></a>Technische opmerkingen  

+ Het is een best practice om gegevens sets te normaliseren voordat u ze gebruikt voor kruis validatie. 

+ Kruis validatie model is veel meer reken kracht en duurt langer dan als u het model hebt gevalideerd met behulp van een wille keurig opgesplitste gegevensset. De reden hiervoor is dat model treinen door kruisen en het model meerdere keren wordt gevalideerd.

+ Het is niet nodig om de gegevensset te splitsen in trainings-en test sets wanneer u kruis validatie gebruikt om de nauw keurigheid van het model te meten. 


## <a name="next-steps"></a>Volgende stappen

Bekijk de [set met modules die beschikbaar zijn](module-reference.md) voor Azure machine learning. 

