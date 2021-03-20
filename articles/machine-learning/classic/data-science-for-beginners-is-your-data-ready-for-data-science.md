---
title: 'ML Studio (klassiek): gegevens evaluatie-Azure'
description: Vier criteria waaraan uw gegevens moeten voldoen om de data technologie gereed te maken voor gegevens wetenschap. Deze video bevat concrete voor beelden die u helpen bij de evaluatie van de basis gegevens.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: conceptual
author: sdgilley
ms.author: sgilley
ms.custom: seodec18
ms.date: 03/22/2019
ms.openlocfilehash: 07db1f1c8021759fbd5702ad9353f74fcd1524c9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100516971"
---
# <a name="is-your-data-ready-for-data-science"></a>Zijn gegevens gereed voor gegevenswetenschap?
## <a name="video-2-data-science-for-beginners-series"></a>Video 2: gegevens wetenschap voor beginners-serie
Meer informatie over het evalueren van uw gegevens om te controleren of deze voldoet aan de basis criteria die voor data technologie kunnen worden gemaakt.

Bekijk alles om alles uit de serie te halen. [Ga naar de lijst met Video's](#other-videos-in-this-series)
<br>

> [!VIDEO https://channel9.msdn.com/Shows/SupervisionNotRequired/9/player]
>
>

## <a name="other-videos-in-this-series"></a>Andere Video's in deze serie
*Data wetenschappen voor beginners* is een snelle inleiding tot data Science in vijf korte Video's.

* Video 1: [de 5 vragen van data Science-antwoorden](data-science-for-beginners-the-5-questions-data-science-answers.md) *(5 minuten 14 seconden)*
* Video 2: zijn uw gegevens gereed voor gegevens wetenschap?
* Video 3: [Stel een vraag die u kunt beantwoorden met gegevens](data-science-for-beginners-ask-a-question-you-can-answer-with-data.md) *(4 min 17 sec.)*
* Video 4: [een antwoord voors pellen met een eenvoudig model](data-science-for-beginners-predict-an-answer-with-a-simple-model.md) *(7 min 42 seconden)*
* Video 5: [werk van anderen naar gegevens wetenschap kopiëren](data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3 min 18 sec.)*

## <a name="transcript-is-your-data-ready-for-data-science"></a>Transcriptie: is uw gegevens gereed voor gegevens wetenschap?
Welkom bij "zijn uw gegevens gereed voor gegevens wetenschap?" de tweede video in de reeks *gegevens wetenschap voor beginners*.  

Voordat de gegevens wetenschap u de gewenste antwoorden kan geven, moet u ervoor zorgen dat er een aantal grond stoffen van hoge kwaliteit is om mee te werken. Net als bij het maken van een pizza, hoe beter de ingrediënten waarmee u begint, het uiteindelijke product. 

## <a name="criteria-for-data"></a>Criteria voor gegevens
In data Wetenschappen zijn er bepaalde ingrediënten die samen moeten worden getrokken, waaronder:

* Dienen
* Verbonden
* Onjuiste
* Voldoende voor het gebruik van

## <a name="is-your-data-relevant"></a>Zijn uw gegevens relevant?
Dus het eerste ingrediënt: u hebt gegevens nodig die relevant zijn.

![Relevante gegevens vergeleken met irrelevante gegevens-gegevens evalueren](./media/data-science-for-beginners-is-your-data-ready-for-data-science/relevant-and-irrelevant-data.png)

Aan de linkerkant ziet u het bloed alcohol niveau van zeven mensen die buiten een Boston-balk zijn getest, het Rode Sox batting gemiddeld in het laatste spel en de prijs van melk in de dichtstbijzijnde winkel.

Dit zijn alle rechtmatige gegevens. De enige fout is dat deze niet relevant is. Er is geen duidelijke relatie tussen deze getallen. Als iemand u de huidige prijs van melk en het Rode Sox batting-gemiddelde heeft gegeven, is er geen manier om de inhoud van de bloed alcohol te raden.

Bekijk nu de tabel aan de rechter kant. Deze keer wordt de hoofd massa van elke persoon gemeten, evenals het aantal drank dat ze hebben gehad.  De getallen in elke rij zijn nu relevant voor elkaar. Als ik mijn lichaams massa en het aantal Margaritas dat ik heb ontvangen, zou je een schatting kunnen maken bij mijn bloed alcohol-inhoud.

## <a name="do-you-have-connected-data"></a>Hebt u gegevens verbonden?
Het volgende ingrediënt is verbonden met gegevens.

![Verbonden gegevens versus gegevens gegevens die niet zijn verbonden, gegevens die klaar zijn](./media/data-science-for-beginners-is-your-data-ready-for-data-science/connected-vs-disconnected-data.png)

Hier volgt een aantal relevante gegevens over de kwaliteit van hamburgers: rooster temperatuur, Patty-gewicht en classificatie in het lokale voedsel-Magazine. U ziet echter wel de hiaten in de tabel aan de linkerkant.

Voor de meeste gegevens sets ontbreken enkele waarden. Het is gebruikelijk om deze te laten werken en er zijn manieren om deze te omzeilen. Maar als er te veel ontbreekt, worden uw gegevens weer gegeven als Zwitserse kaas.

Als u de tabel aan de linkerkant bekijkt, is er zoveel ontbrekende gegevens, is het moeilijk om een soort relatie te hebben tussen de rooster temperatuur en het Patty-gewicht. In dit voor beeld ziet u gegevens die niet zijn verbonden.

De tabel aan de rechter kant, maar is vol en volledig, een voor beeld van gekoppelde gegevens.

## <a name="is-your-data-accurate"></a>Zijn uw gegevens nauw keurig?
Het volgende ingrediënt is nauw keurig. Hier vindt u vier doelen die u kunt aanraken.

![Nauw keurige gegevens versus onjuiste gegevens criteria](./media/data-science-for-beginners-is-your-data-ready-for-data-science/inaccurate-vs-accurate-data.png)

Bekijk het doel in de rechter bovenhoek. Er is een nauw keurig deel van de groep over het stieren-oog. Dat is natuurlijk nauw keurig. Oddly, in de taal van de gegevens wetenschap, wordt de prestaties van het doel recht hieronder ook als nauw keurig beschouwd.

Als u het midden van deze pijlen hebt toegewezen, ziet u dat deze zeer dicht bij het oog op de stieren wordt weer gegeven. De pijlen zijn alle rond het doel verdeeld, waardoor ze niet nauw keurig worden beschouwd, maar ze rond het blote oog worden gecentreerd, zodat ze nauw keurig worden beschouwd.

Bekijk nu het doel linksboven. Hier worden de pijlen zeer dicht bij elkaar geraken, een nauw keurig groeperen. Ze zijn nauw keurig, maar ze zijn niet nauw keurig, omdat het midden het blote-oog is. De pijlen in het rechtsonder-doel zijn zowel onjuist als onnauwkeurig. Deze Archer vereist meer prak tijken.

## <a name="do-you-have-enough-data-to-work-with"></a>Hebt u voldoende gegevens om mee te werken?
Ten slotte is ingrediënt #4 voldoende gegevens.

![Hebt u voldoende gegevens voor analyse? Gegevens evaluatie](./media/data-science-for-beginners-is-your-data-ready-for-data-science/barely-enough-data.png)

Denk aan elk gegevens punt in de tabel als een penseel streek in een tekening. Als u slechts een paar van deze hebt, kan het zijn dat u precies weet wat het is.

Als u nog meer penseel streken toevoegt, begint uw tekenen met een beetje scherper.

Wanneer u net genoeg streken hebt, ziet u alleen genoeg voor het maken van een groot aantal beslissingen. Kan ik nog een bezoek brengen? Het lijkt alsof het goed is, dat ziet eruit als schoon water – Ja, dat is waar ik op vakantie ga.

Wanneer u meer gegevens toevoegt, wordt de afbeelding duidelijker en kunt u gedetailleerde beslissingen nemen. Nu kunt u de drie hotels bekijken aan de linkerkant. U kunt de architectuur functies van de functie op de voor grond opmerken. U kunt zelfs kiezen om op de derde verdieping te blijven vanwege de weer gave.

Met gegevens die relevant, verbonden, nauw keurig en voldoende zijn, hebt u alle benodigde ingrediënten nodig voor een hoogwaardige gegevens wetenschap.

Zorg ervoor dat u de andere vier Video's in *Data Science voor beginners* van Microsoft Azure machine learning Studio (klassiek) bekijkt.

## <a name="next-steps"></a>Volgende stappen
* [Probeer een eerste experiment van data Science met Machine Learning Studio (klassiek)](create-experiment.md)
* [Krijg een inleiding tot Machine Learning op Microsoft Azure](../overview-what-is-azure-ml.md)