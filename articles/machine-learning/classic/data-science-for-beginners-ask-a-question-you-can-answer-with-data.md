---
title: Een vraag stellen gegevens kunnen antwoorden ML Studio (klassiek)-Azure
description: Meer informatie over het formuleren van een scherpe vraag voor gegevens wetenschap in data Science voor beginners Video 3. Bevat een vergelijking van classificatie-en regressie vragen.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: conceptual
author: sdgilley
ms.author: sgilley
ms.date: 03/22/2019
ms.openlocfilehash: 2ff440801db7ba8c5bfc1785fc7a399a1d773b73
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100517022"
---
# <a name="ask-a-question-you-can-answer-with-data"></a>Stel een vraag die u met gegevens kunt beantwoorden
## <a name="video-3-data-science-for-beginners-series"></a>Video 3: gegevens wetenschap voor beginners-serie
Meer informatie over het formuleren van een probleem met een gegevens wetenschap in een vraag in data Science voor beginners Video 3. Deze video bevat een vergelijking van vragen over classificatie-en regressie-algoritmen.

Bekijk alles om alles uit de serie te halen. [Ga naar de lijst met Video's](#other-videos-in-this-series)
<br>

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Data-science-for-beginners-Ask-a-question-you-can-answer-with-data/player]
>
>

## <a name="other-videos-in-this-series"></a>Andere Video's in deze serie
*Data wetenschappen voor beginners* is een snelle inleiding tot data Science in vijf korte Video's.

* Video 1: [de 5 vragen van data Science-antwoorden](data-science-for-beginners-the-5-questions-data-science-answers.md) *(5 minuten 14 seconden)*
* Video 2: [zijn uw gegevens gereed voor gegevens wetenschap?](data-science-for-beginners-is-your-data-ready-for-data-science.md) *(4 min. 56 sec.)*
* Video 3: Stel een vraag die u kunt beantwoorden met gegevens
* Video 4: [een antwoord voors pellen met een eenvoudig model](data-science-for-beginners-predict-an-answer-with-a-simple-model.md) *(7 min 42 seconden)*
* Video 5: [werk van anderen naar gegevens wetenschap kopiëren](data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3 min 18 sec.)*

## <a name="transcript-ask-a-question-you-can-answer-with-data"></a>Transcriptie: Stel een vraag die u kunt beantwoorden met gegevens
Welkom bij de derde video in de reeks "data Science for beginners".  

In deze versie krijgt u enkele tips voor het formuleren van een vraag die u kunt beantwoorden met gegevens.

U kunt meer uit deze video halen als u eerst de twee oudere Video's in deze serie bekijkt: "de gegevens wetenschap van 5 vragen kan beantwoorden" en "zijn uw gegevens klaar voor data wetenschappen?"

## <a name="ask-a-sharp-question"></a>Een scherpe vraag stellen
We hebben uitgelegd hoe gegevens wetenschap het proces is van het gebruik van namen (ook wel categorieën of labels genoemd) en cijfers om een antwoord op een vraag te voors pellen. Maar dit kan slechts een wille keurige vraag zijn. het moet een *scherpe vraag zijn.*

Een vague-vraag hoeft niet te worden beantwoord met een naam of nummer. Een duidelijke vraag moet zijn.

Stel dat u een Magic-lampje hebt gevonden met een genie die uw vraag naar waarheid beantwoordt. Maar het is een mischievous-genie, die probeert hun antwoord te maken als vague en verwarrend als ze kunnen worden verwijderd. U wilt ze vastmaken aan de hand van een vraag, zodat u niet meer weet wat u wilt weten.

Als u een vague-vraag wilt stellen, zoals ' wat gebeurt er met mijn aandelen? ', kan de genie worden beantwoord ', wordt de prijs gewijzigd '. Dat is een truthful-antwoord, maar dit is niet heel nuttig.

Maar als u een scherpe vraag wilt stellen, bijvoorbeeld ' wat doet de verkoop prijs van mijn aandelen in de volgende week? ', kan de genie u niet helpen, maar een specifiek antwoord geven en een verkoop prijs voors pellen.

## <a name="examples-of-your-answer-target-data"></a>Voor beelden van uw antwoord: doel gegevens
Nadat u uw vraag hebt geformuleerd, controleert u of u voor beelden hebt van het antwoord in uw gegevens.

Als onze vraag ' wat is de verkoop prijs van mijn aandelen volgende week? ' vervolgens moeten we ervoor zorgen dat onze gegevens de koers geschiedenis bevatten.

Als de vraag "welke auto in mijn vloot het eerst zal mislukken?" vervolgens moeten we ervoor zorgen dat de gegevens informatie over eerdere fouten bevat.

![Doel gegevens: voor beelden van uw antwoord. Een Data Science-vraag formuleren.](./media/data-science-for-beginners-ask-a-question-you-can-answer-with-data/target-data.png)

Deze voor beelden van antwoorden worden een doel genoemd. Een doel is wat we proberen te voors pellen over toekomstige gegevens punten, of het nu een categorie of een nummer is.

Als u geen doel gegevens hebt, moet u een aantal ophalen. U kunt geen antwoord geven op uw vraag.

## <a name="reformulate-your-question"></a>Uw vraag opnieuw formuleren
Het kan zijn dat u uw vraag opnieuw kunt weer geven om een nuttig antwoord te krijgen.

De vraag is dit gegevens punt A of B? de categorie (of de naam of het label) van iets voors pellen. We gebruiken een *classificatie algoritme* om het te beantwoorden.

De vraag wat? of ' hoeveel? ' voor spelt een bedrag. Voor het beantwoorden van het gebruik van een *regressie algoritme*.

Als u wilt zien hoe we deze kunnen omzetten, gaan we naar de vraag ' welke nieuws tekst is het interessantst voor deze lezer? ' U wordt gevraagd om een voor spelling van één keuze uit een groot aantal mogelijkheden: ' is dit A-of B-of C-of D? ' -en zou een classificatie algoritme gebruiken.

Maar dit kan handig zijn als u het woord opnieuw als "hoe interessant is elk artikel in deze lijst aan deze lezer?" Nu kunt u elk artikel een numerieke Score geven en dan is het eenvoudig om het artikel met de hoogste score te identificeren. Dit is een reformuleering van de classificatie vraag in een regressie vraag of hoeveel?

![Formuleer uw vraag opnieuw. Classificatie vraag versus regressie vraag.](./media/data-science-for-beginners-ask-a-question-you-can-answer-with-data/classification-question-vs-regression-question.png)

Hoe u een vraag stelt, is een aanwijzing waarmee u een antwoord kunt geven.

U zult merken dat bepaalde families van algoritmen, zoals die in het voor beeld van het nieuws artikel, nauw verwant zijn. U kunt uw vraag opnieuw formuleren om het algoritme te gebruiken waarmee u het nuttigst antwoord krijgt.

Maar de belangrijkste vraag is dat u de vraag die u kunt beantwoorden met de gegevens, te stellen. En zorg ervoor dat u de juiste gegevens hebt om deze te beantwoorden.

We hebben een aantal basis principes besproken voor het stellen van een vraag die u kunt beantwoorden met gegevens.

Bekijk de andere Video's in "data Science for beginners" van Microsoft Azure Machine Learning Studio (klassiek).

## <a name="next-steps"></a>Volgende stappen
* [Probeer een eerste experiment van data Science met Machine Learning Studio (klassiek)](create-experiment.md)
* [Krijg een inleiding tot Machine Learning op Microsoft Azure](../overview-what-is-azure-ml.md)