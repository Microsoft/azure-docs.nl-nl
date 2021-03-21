---
title: Diep gaande informatie versus machine learning
titleSuffix: Azure Machine Learning
description: Meer informatie over hoe diep leren is gekoppeld aan machine learning en AI. Gebruik in Azure Machine Learning diepe leer modellen voor fraude detectie, object detectie en meer.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: lazzeri
author: FrancescaLazzeri
ms.date: 01/14/2020
ms.custom: contperf-fy21q1,contperfq1
ms.openlocfilehash: 48de06d28442b4d05cd3a7ab287732c0999e434c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101659695"
---
# <a name="deep-learning-vs-machine-learning-in-azure-machine-learning"></a>Diep gaande informatie versus machine learning in Azure Machine Learning

In dit artikel worden uitgebreide lessen vergeleken met machine learning en wordt uitgelegd hoe ze in de bredere categorie van kunst matige intelligentie passen. Meer informatie over oplossingen voor uitgebreide trainingen die u kunt bouwen op Azure Machine Learning, zoals fraude detectie, spraak-en gezichts herkenning, sentiment analyse en tijd reeks prognoses.

Zie het overzicht van [machine learning Algorithm Cheat](./algorithm-cheat-sheet.md?WT.mc_id=docs-article-lazzeri)voor meer informatie over het kiezen van algoritmen voor uw oplossingen.

## <a name="deep-learning-machine-learning-and-ai"></a>Diep leren, machine learning en AI

![Relatie diagram: AI versus machine learning versus diep gaande lessen](./media/concept-deep-learning-vs-machine-learning/ai-vs-machine-learning-vs-deep-learning.png)

Houd rekening met de volgende definities voor een beter begrip van diep leren en machine learning versus AI:

- **Diep leren** is een subset van machine learning die is gebaseerd op kunst matige Neural netwerken. Het _leer proces_ is _dieper_ omdat de structuur van kunst matige Neural netwerken uit meerdere invoer-, uitvoer-en verborgen lagen bestaat. Elke laag bevat eenheden waarmee de invoergegevens worden getransformeerd tot informatie die door de volgende laag voor een bepaalde voorspellingstaak kan worden gebruikt. Dankzij deze structuur kan een machine een eigen gegevens verwerking leren.

- **Machine learning** is een subset van kunst matige intelligentie die gebruikmaakt van technieken (zoals diepe learning) waarmee computers ervaring kunnen gebruiken om taken te verbeteren. Het _leer proces_ is gebaseerd op de volgende stappen:

   1. Gegevens in een algoritme invoeren. (In deze stap kunt u aanvullende informatie voor het model opgeven, bijvoorbeeld door het uitpakken van functies uit te voeren.)
   1. Gebruik deze gegevens om een model te trainen.
   1. Het model testen en implementeren.
   1. Gebruik het geïmplementeerde model om een geautomatiseerde, voorspellende taak uit te voeren. (Met andere woorden, roep en gebruik het geïmplementeerde model om de voor spellingen te ontvangen die door het model worden geretourneerd.)

- **Kunst matige intelligentie (AI)** is een techniek waarmee computers Human Intelligence kunnen nabootsen. Het bevat machine learning. 
 
U kunt met behulp van machine learning en diepe Leer technieken computer systemen en-toepassingen bouwen die vaak aan Human Intelligence zijn gekoppeld. Deze taken omvatten afbeeldings herkenning, spraak herkenning en taal omzetting.

## <a name="techniques-of-deep-learning-vs-machine-learning"></a>Technieken van diep leren versus machine learning 

Nu u een overzicht hebt van machine learning versus diepere lessen, gaan we de twee technieken vergelijken. In machine learning moet het algoritme worden aangegeven hoe u een nauw keurige voor spelling maakt door meer informatie te gebruiken (bijvoorbeeld door functie extractie uit te voeren). In het uitgebreid leren is het algoritme meer informatie over het maken van een nauw keurige voor spelling door de eigen gegevens verwerking, dankzij de kunst matige Neural-netwerk structuur.

De volgende tabel vergelijkt de twee technieken in meer detail:

| |Alle machine learning |Alleen diep gaande lessen|
|---|---|---|
|  **Aantal gegevens punten** | Kan kleine hoeveel heden gegevens gebruiken om voor spellingen te maken. | Er moet gebruik worden gemaakt van grote hoeveel heden trainings gegevens om voor spellingen te maken. |
|  **Hardware-afhankelijkheden** | Kan werken op computers met een laag eind punt. Er is geen grote hoeveelheid reken kracht nodig. | Is afhankelijk van high-end-machines. Er wordt een groot aantal bewerkingen voor matrix vermenigvuldiging uitgevoerd. Met een GPU kunnen deze bewerkingen efficiënt worden geoptimaliseerd. |
|  **Parametrisatie-proces** | Vereist onderdelen om nauw keurig te worden geïdentificeerd en gemaakt door gebruikers. | Meer informatie over functies op hoog niveau van gegevens en maakt zelf nieuwe functies. |
|  **Leer benadering** | Verdeelt het leer proces in kleinere stappen. Vervolgens worden de resultaten van elke Step Into één uitvoer gecombineerd. | Gaat door het leer proces door het probleem te verhelpen. |
|  **Uitvoeringstijd** | Neemt relatief weinig tijd in beslag om te trainen, variërend van een paar seconden tot enkele uren. | Het duurt doorgaans lang om te trainen omdat een diepe leer algoritme veel lagen omvat. |
|  **Uitvoer** | De uitvoer is doorgaans een numerieke waarde, zoals een score of classificatie. | De uitvoer kan meerdere indelingen hebben, zoals een tekst, een score of een geluid. |

## <a name="what-is-transfer-learning"></a>Wat is overboeking Learning

Voor het trainen van diepe leer modellen zijn vaak grote hoeveel heden trainings gegevens, high-end Compute-resources (GPU, TPU) en een langere training tijd vereist. Wanneer u geen van deze voor u beschikbaar hebt, kunt u een snelkoppeling maken naar het trainings proces met behulp van een techniek die bekend staat als *overboeking learning.*

Overboeking learning is een techniek waarbij kennis wordt toegepast op basis van het oplossen van een probleem naar een ander, maar verwant probleem.

Vanwege de structuur van Neural-netwerken bevatten de eerste set lagen meestal functies van een lager niveau, terwijl de laatste set lagen de functie op een hoger niveau bevat die dichter bij het betrokken domein ligt. Door de laatste lagen te nemen voor gebruik in een nieuw domein of probleem, kunt u de hoeveelheid tijd, gegevens en reken resources die nodig zijn om het nieuwe model te trainen aanzienlijk reduceren. Als u bijvoorbeeld al een model hebt dat auto's herkent, kunt u dit model met behulp van overboeking Learning ook herkennen aan vracht wagens, motor rijwielen en andere soorten voer tuigen.

Meer informatie over het Toep assen van overboeking learning voor installatie kopie classificatie met behulp van een open source-framework in Azure Machine Learning: [Train een diep Learning PyTorch-model met behulp van overboeking Learning](./how-to-train-pytorch.md?WT.mc_id=docs-article-lazzeri).

## <a name="deep-learning-use-cases"></a>Uitgebreide leer cases

Vanwege de kunst matige Neural-netwerk structuur worden er diep gaande kennissen van Excel bij het identificeren van patronen in ongestructureerde gegevens, zoals afbeeldingen, geluid, video en tekst. Om die reden is het snel om veel branches te transformeren, met inbegrip van gezondheids zorg, energie, financiën en Trans Port. Deze branches zijn nu bezig met het herzien van traditionele bedrijfs processen. 

Enkele van de meest voorkomende toepassingen voor diep gaande lessen worden beschreven in de volgende alinea's. In Azure Machine Learning kunt u een model gebruiken van uw build van een open-source-framework of het model bouwen met behulp van de beschik bare hulpprogram ma's.

### <a name="named-entity-recognition"></a>Herkenning benoemde entiteit

Benoemde entiteits herkenning is een diepe leer methode die een stuk tekst als invoer gebruikt en transformeert in een vooraf opgegeven klasse. Deze nieuwe informatie kan een post code, een datum, een product-ID zijn. De informatie kan vervolgens worden opgeslagen in een Structured schema om een lijst met adressen te maken of om te fungeren als een bench Mark voor een identiteits validatie-engine.

### <a name="object-detection"></a>Objectdetectie

Grondige kennis is toegepast in veel object detectie-use cases. Object detectie bestaat uit twee onderdelen: afbeeldings classificatie en vervolgens afbeeldings lokalisatie. Met afbeeldings _classificatie_ worden de objecten van de afbeelding, zoals auto's of personen, geïdentificeerd. _Lokalisatie_ van de installatie kopie biedt de specifieke locatie van deze objecten. 

Object detectie wordt al gebruikt in branches zoals gaming, handels verkeer, toer isme en zelf gerichte auto's.

### <a name="image-caption-generation"></a>Bijschrift bij afbeelding genereren

Net als bij afbeeldings herkenning moet in afbeeldings bijschriften voor een bepaalde afbeelding een bijschrift worden gegenereerd waarin de inhoud van de installatie kopie wordt beschreven. Wanneer u objecten op Foto's kunt detecteren en labelen, is de volgende stap het omzetten van deze labels in beschrijvende zinnen. 

Doorgaans gebruiken installatie kopieën voor bijschriften van toepassingen convolutional Neural-netwerken om objecten in een installatie kopie te identificeren en vervolgens een recurrent Neural-netwerk te gebruiken om de labels te omzetten in consistente zinnen.

### <a name="machine-translation"></a>Automatische vertaling

Met machine vertalingen worden woorden of zinnen uit de ene taal gebruikt en worden ze automatisch omgezet in een andere taal. De automatische vertaling van de machine is al lange tijd in de buurt, maar met een diep gaande leer proces worden indrukwekkende resultaten in twee specifieke gebieden gecentraliseerd: automatisch omzetten van tekst (en vertaling van spraak naar tekst) en automatische vertaling van afbeeldingen.

Met de juiste gegevens transformatie kan een Neural-netwerk tekst, audio en visuele signalen begrijpen. Machine vertalingen kunnen worden gebruikt om geluids fragmenten in grotere audio bestanden te identificeren en het gesp roken woord of de afbeelding als tekst te transcriberen.

### <a name="text-analytics"></a>Tekstanalyse

Voor tekst analyse op basis van diepe leer methoden moet u grote hoeveel heden tekst gegevens (bijvoorbeeld medische documenten of onkosten ontvangsten) analyseren, patronen herkennen en gestructureerde en beknopte informatie maken.

Bedrijven gebruiken dieper leren om tekst analyse uit te voeren om de handel en naleving van de overheid te detecteren. Een ander algemeen voor beeld is een verzekerings fraude: tekst analyse is vaak gebruikt voor het analyseren van grote hoeveel heden documenten om de kans op fraude van een verzekerings claim te herkennen. 

## <a name="artificial-neural-networks"></a>Kunst matige Neural netwerken

Kunst matige Neural netwerken worden gevormd door lagen van verbonden knoop punten. Uitgebreide leer modellen gebruiken Neural-netwerken met een groot aantal lagen. 

In de volgende secties worden de meest populaire kunst matige Neural-netwerk topologieën besproken.

### <a name="feedforward-neural-network"></a>Feedforward Neural-netwerk

Het Feedforward Neural-netwerk is het meest eenvoudige type kunst matig Neural netwerk. In een Feedforward-netwerk worden de gegevens in slechts één richting verplaatst van de invoer laag naar de uitvoer laag. Met Feedforward Neural-netwerken wordt een invoer getransformeerd door een reeks verborgen lagen te plaatsen. Elke laag bestaat uit een set neurons en elke laag is volledig verbonden met alle neurons in de laag. De laatste volledig verbonden laag (de uitvoer laag) vertegenwoordigt de gegenereerde voor spellingen.

### <a name="recurrent-neural-network"></a>Recurrent Neural-netwerk

Recurrente Neural Networks is een veelgebruikt Neural netwerk. In deze netwerken wordt de uitvoer van een laag opgeslagen en weer gegeven in de invoer laag om te helpen bij het voors pellen van het resultaat van de laag. Rehuidige Neural-netwerken hebben fantastische leer mogelijkheden. Ze worden veel gebruikt voor complexe taken, zoals het maken van een time series-prognose, het leren van hand schrift en het herkennen van de taal.

### <a name="convolutional-neural-network"></a>Convolutional Neural-netwerk

Een convolutional Neural-netwerk is een bijzonder effectief Neural netwerk en biedt een unieke architectuur. Lagen zijn ingedeeld in drie dimensies: breedte, hoogte en diepte. De neurons in de ene laag worden niet verbonden met alle neurons in de volgende laag, maar alleen voor een kleine regio van de neurons van de laag. De uiteindelijke uitvoer wordt gereduceerd tot één vector met waarschijnlijkheids scores, geordend op de diepte dimensie. 

Convolutional Neural Networks zijn gebruikt in gebieden zoals video herkenning, afbeeldings herkenning en aanbevolen systemen.

## <a name="next-steps"></a>Volgende stappen

In de volgende artikelen ziet u meer opties voor het gebruik van open-source uitgebreide leer modellen in [Azure machine learning](./index.yml?WT.mc_id=docs-article-lazzeri):


- [Hand matige cijfers classificeren met behulp van een tensor flow-model](./how-to-train-tensorflow.md?WT.mc_id=docs-article-lazzeri) 

- [Geclassificeerde handgeschreven cijfers door gebruik te maken van een tensor flow Estimator en Keras](./how-to-train-keras.md?WT.mc_id=docs-article-lazzeri)

- [Hand matige cijfers classificeren met behulp van een ketting model](./how-to-set-up-training-targets.md?WT.mc_id=docs-article-lazzeri)
