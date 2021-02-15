---
title: 'ML Studio (klassiek): voor spel-antwoorden met regressie modellen-Azure'
description: Het maken van een eenvoudig regressie model voor het voors pellen van een prijs in data Science voor beginners Video 4. Bevat een lineaire regressie met doel gegevens.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: conceptual
author: sdgilley
ms.author: sgilley
ms.custom: seodec18
ms.date: 03/22/2019
ms.openlocfilehash: c6d78b10b98abe33744673459b3d31d8941aac89
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100520405"
---
# <a name="predict-an-answer-with-a-simple-model"></a>Met een eenvoudig model een antwoord voorspellen


## <a name="video-4-data-science-for-beginners-series"></a>Video 4: data wetenschappen voor beginners-serie
Meer informatie over het maken van een eenvoudig regressie model voor het voors pellen van de prijs van een ruit in data wetenschappen voor beginners Video 4. Er wordt een regressie model met doel gegevens getekend.

Bekijk alles om alles uit de serie te halen. [Ga naar de lijst met Video's](#other-videos-in-this-series)
<br>

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/data-science-for-beginners-series-predict-an-answer-with-a-simple-model/player]
>
>

## <a name="other-videos-in-this-series"></a>Andere Video's in deze serie
*Data wetenschappen voor beginners* is een snelle inleiding tot data Science in vijf korte Video's.

* Video 1: [de 5 vragen van data Science-antwoorden](data-science-for-beginners-the-5-questions-data-science-answers.md) *(5 minuten 14 seconden)*
* Video 2: [zijn uw gegevens gereed voor gegevens wetenschap?](data-science-for-beginners-is-your-data-ready-for-data-science.md) *(4 min. 56 sec.)*
* Video 3: [Stel een vraag die u kunt beantwoorden met gegevens](data-science-for-beginners-ask-a-question-you-can-answer-with-data.md) *(4 min 17 sec.)*
* Video 4: een antwoord voors pellen met een eenvoudig model
* Video 5: [werk van anderen naar gegevens wetenschap kopiëren](data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3 min 18 sec.)*

## <a name="transcript-predict-an-answer-with-a-simple-model"></a>Transcriptie: een antwoord voors pellen met een eenvoudig model
Welkom bij de vierde video in de serie "data Science for beginners". In deze versie bouwt u een eenvoudig model en maakt u een voor spelling.

Een *model* is een vereenvoudigd verhaal over onze gegevens. Ik laat u zien wat ik betekenen.

## <a name="collect-relevant-accurate-connected-enough-data"></a>Relevante, nauw keurige, geverbindingeerde, voldoende gegevens verzamelen
Stel dat ik een diamant wil kopen. Ik heb een ring die deel uitmaakte van mijn achternaam met een instelling voor een dakje-Diamond van 1,35 en ik wil een idee krijgen van de hoeveelheid kosten. Ik maak een Klad blok en een pen in de juwelen Store en noteer de prijs van alle ruiten in het geval en hoeveel ze in CARATS afwegen. Begin met de eerste diamant: it 1,01 CARATS en $7.366.

Ik ga nu door en doe dit voor alle andere diamanten in de Store.

![Kolommen met Diamond-gegevens](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/diamond-data.png)

U ziet dat de lijst twee kolommen bevat. Elke kolom heeft een verschillend kenmerk-gewicht in CARATS en Price, en elke rij is één gegevens punt dat één ruit voor stelt.

We hebben een kleine gegevensset hier gemaakt: een tabel. Merk op dat het voldoet aan onze criteria voor kwaliteit:

* De gegevens zijn **relevant** : het gewicht is zeker van de prijs
* Het is **nauw keurig** dat we de prijzen die we hebben genoteerd, hebben gecontroleerd
* De **verbinding is gemaakt** : er zijn geen lege spaties in een van deze kolommen
* En als we zien, is het **voldoende** gegevens om onze vraag te beantwoorden

## <a name="ask-a-sharp-question"></a>Een scherpe vraag stellen
We zullen onze vraag nu op een scherpe manier doen: "Hoeveel kost het dan voor de aanschaf van een 1,35 dakje Diamond?"

De lijst bevat geen 1,35 dakje-diamant. Daarom moeten we de rest van onze gegevens gebruiken om een antwoord op de vraag te krijgen.

## <a name="plot-the-existing-data"></a>De bestaande gegevens uitzetten
Het eerste wat we doen, is het tekenen van een horizontale nummer lijn, een as genoemd, om de gewichten in een grafiek te plaatsen. Het bereik van de gewichten is 0 tot 2, dus we tekenen een lijn die het bereik bedekt en plaatst maat streepjes voor elke halve dakje.

Vervolgens tekent u een verticale as om de prijs vast te leggen en verbindt u deze met de horizontale lijn voor gewichten. Dit wordt uitgedrukt in guldens. Nu hebben we een set coördinaten assen.

![Gewicht en prijs assen](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/weight-and-price-axes.png)

We gaan deze gegevens nu volgen en omzetten in een *spreidings tekening*. Dit is een uitstekende manier om numerieke gegevens sets te visualiseren.

Voor het eerste gegevens punt Eyeball een verticale lijn op 1,01 CARATS. Vervolgens Eyeball een horizontale lijn op $7.366. Waar ze aan voldoen, tekenen we een punt. Dit staat voor onze eerste ruit.

Nu gaan we elke ruit in deze lijst door lopen en hetzelfde doen. Als we dit doen, is dit een aantal punten, één voor elke ruit.

![Spreidings plot](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/scatter-plot.png)

## <a name="draw-the-model-through-the-data-points"></a>Het model door middel van de gegevens punten tekenen
Als u nu de punten en squint bekijkt, ziet de verzameling eruit als een FAT-, fuzzy-regel. We kunnen onze markering door lopen en een rechte lijn door ons laten tekenen.

Door een regel te tekenen, hebben we een *model* gemaakt. U kunt dit zien als de echte wereld en een vereenvoudigde-strip-versie. De strip is nu onjuist. de lijn gaat niet alle gegevens punten door. Maar het is een nuttige vereenvoudiging.

![Lineaire regressie lijn](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/linear-regression-line.png)

Het feit dat alle punten niet precies op de regel staan, is OK. Gegevens wetenschappers verklaren dit door te zeggen dat het model de lijn is en dat er voor elke stip een *ruis* of *afwijking* is gekoppeld. Er is de onderliggende perfecte relatie, en daar is het Gritty, de echte wereld waarmee ruis en onzekerheid worden toegevoegd.

Omdat we proberen *de vraag te* beantwoorden, is dit een *regressie*. Omdat we een rechte lijn gebruiken, is dit een *lineaire regressie*.

## <a name="use-the-model-to-find-the-answer"></a>Het model gebruiken om het antwoord te vinden
We hebben nu een model en we stellen ons hiervan op de prijs: wat is de kosten van 1,35 dakje Diamond?

Om onze vraag te beantwoorden, Eyeball 1,35 CARATS en tekent u een verticale lijn. Wanneer het de model regel kruist, Eyeball een horizontale lijn naar de dollar-as. Het komt direct op 10.000. Perk! Dat is het antwoord: een dakje Diamond-kosten van 1,35 over $10.000.

![Het antwoord op het model zoeken](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/find-the-answer.png)

## <a name="create-a-confidence-interval"></a>Een betrouwbaarheids interval maken
Het is natuurlijk dat u afvraagt hoe nauw keurig deze voor spelling is. Het is handig om te weten of de dakje-Diamond van 1,35 erg dicht bij $10.000 of veel hoger of lager is. Om dit uit te zetten, tekenen we een envelop rond de regressie lijn die de meeste punten bevat. Deze envelop wordt ons *betrouwbaarheids interval* genoemd: we zijn redelijk dat de prijzen binnen deze envelop vallen omdat ze in het verleden het meest hebben. We kunnen twee meer horizontale lijnen tekenen vanaf het punt waarop de 1,35 dakje lijn de bovenkant en de onderkant van de envelop overschrijdt.

![Betrouwbaarheidsinterval](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/confidence-interval.png)

Nu kunnen we iets vertellen over ons betrouwbaarheids interval: we kunnen er zeker van zijn dat de prijs van een dakje Diamond van 1,35 ongeveer $10.000 is, maar het kan even zijn als $8.000 en kan zo hoog zijn als $12.000.

## <a name="were-done-with-no-math-or-computers"></a>U bent klaar, zonder wiskunde of computers
We hebben bepaald wat gegevens wetenschappers aan de slag krijgen, en we hebben het net gedaan door te tekenen:

* We hebben een vraag gesteld die we kunnen beantwoorden met gegevens
* We hebben een *model* gemaakt met behulp van *lineaire regressie*
* We hebben een voor *Spelling* gemaakt, compleet met een *betrouwbaarheids interval*

En we hebben geen wiskunde of computers gebruikt om dit uit te voeren.

Nu is er meer informatie, zoals...

* de cut van de diamant
* Kleur variaties (hoe dicht het Diamond moet worden wit)
* het aantal insluitingen in de ruit

... Daarna zouden we meer kolommen hebben. In dat geval wordt math nuttig. Als u meer dan twee kolommen hebt, is het moeilijk om punten op papier te tekenen. Met de wiskunde kunt u die lijn of dat vlak op uw gegevens aansluiten.

Als er in plaats van slechts een aantal diamanten wordt gewerkt, hebben we 2000 of 2.000.000. u kunt dit veel sneller doen met een computer.

Nu hebben we meer informatie over het uitvoeren van lineaire regressie en hebben we een voor spelling gemaakt met behulp van gegevens.

Bekijk de andere Video's in "data Science for beginners" van Microsoft Azure Machine Learning Studio (klassiek).

## <a name="next-steps"></a>Volgende stappen
* [Probeer een eerste experiment van data Science met Machine Learning Studio (klassiek)](create-experiment.md)
* [Krijg een inleiding tot Machine Learning op Microsoft Azure](../overview-what-is-azure-ml.md)