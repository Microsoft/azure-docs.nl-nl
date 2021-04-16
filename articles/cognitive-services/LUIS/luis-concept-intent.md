---
title: Intenties en entiteiten - LUIS
titleSuffix: Azure Cognitive Services
description: Eén intentie vertegenwoordigt een taak of actie die de gebruiker wil uitvoeren. Het is een doel, uitgedrukt in van de uiting van een gebruiker. Definieer een set intenties die overeenkomt met acties die gebruikers in uw toepassing willen uitvoeren.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/13/2021
ms.openlocfilehash: 8e76e3e7683d43a7a39bc0c168a29016a988c705
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107499407"
---
# <a name="intents-in-your-luis-app"></a>Intenties in uw LUIS-app

Een intentie vertegenwoordigt een taak of actie die de gebruiker wil uitvoeren. Het is een doel dat wordt uitgedrukt in de uiting van [een gebruiker.](luis-concept-utterance.md)

Definieer een set intenties die overeenkomt met acties die gebruikers in uw toepassing willen uitvoeren. Een reis-app definieert bijvoorbeeld verschillende intenties:

Intenties van reis-app   |   Voorbeelden van utterances   |
------|------|
 BookFlight     |   'Book me a flight to Rio next week' (Boek een vlucht naar Rio volgende week) <br/> 'Fly me to Rio on the 24th' <br/> 'Ik heb een vliegtuigticket nodig volgende zondag naar Rio de Jaap'    |
 Begroeting     |   Hoi <br/>Hallo <br/>Goedemorgen  |
 Weersverwachting | "Hoe ziet het weer eruit in Boston?" <br/> 'Show me the forecast for this weekend' (Toon de prognose voor dit weekend) |
 Geen         | 'Haal een cookierecept voor me op'<br>"Heeft de Lakers gewonnen?" |

Alle toepassingen worden met de vooraf gedefinieerde intentie '[Geen'](#none-intent)gebruikt. Dit is de terugvalintentie.

## <a name="prebuilt-domains-provide-intents"></a>Vooraf gebouwde domeinen bieden intenties
Naast de intenties die u definieert, kunt u vooraf gebouwde intenties uit een van de [vooraf gebouwde domeinen gebruiken.](./howto-add-prebuilt-models.md)

## <a name="return-all-intents-scores"></a>Scores van alle intenties retourneren
U wijst een utterance toe aan één intentie. Wanneer LUIS een utterance op het eindpunt ontvangt, retourneert deze standaard de belangrijkste intentie voor die utterance.

Als u de scores voor alle intenties voor de utterance wilt, kunt u een vlag op de queryreeks van de voorspellings-API geven.

|Voorspellings-API-versie|Vlag|
|--|--|
|V2|`verbose=true`|
|V3|`show-all-intents=true`|

## <a name="intent-compared-to-entity"></a>Intentie vergeleken met entiteit
De intentie vertegenwoordigt de actie die de toepassing moet ondernemen voor de gebruiker en is gebaseerd op de hele utterance. Een uiting kan slechts één best scorende intentie hebben, maar kan veel entiteiten hebben.

<a name="how-do-intents-relate-to-entities"></a>

Maak een intentie wanneer  de intentie van de gebruiker een actie in uw clienttoepassing activeert, zoals een aanroep van de functie checkweather(). Maak vervolgens entiteiten die de parameters vertegenwoordigen die nodig zijn om de actie uit te voeren.

|Intentie   | Entiteit | Voorbeeld van een utterance   |
|------------------|------------------------------|------------------------------|
| Weersverwachting | { "type": "location", "entity": "Seattle" }<br>{ "type": "builtin.datetimeV2.date","entity": "tomorrow","resolution":"2018-05-23" } | Hoe ziet het weer eruit in `Seattle` `tomorrow` ? |
| Weersverwachting | { "type": "date_range", "entity": "this weekend" } | De prognose voor `this weekend` |
||||

## <a name="prebuilt-domain-intents"></a>Vooraf gebouwde domeinintentie

[Vooraf gebouwde domeinen](./howto-add-prebuilt-models.md) bieden intenties met utterances.

## <a name="none-intent"></a>None- intent

De **intentie Geen** wordt gemaakt, maar met opzet leeg gelaten. De **intentie** None is een vereiste intentie en kan niet worden verwijderd of hernoemd. Vul deze met uitingen die buiten uw domein vallen.

De **intentie** None is de terugvalintentie, die belangrijk is in elke app en 10% van het totale aantal utterances moet hebben. Het wordt gebruikt om LUIS-utterances te leren die niet belangrijk zijn in het app-domein (onderwerpgebied). Als u geen utterances toevoegt voor de intentie None, dwingt LUIS een uiting die zich buiten het domein in een van de domeinintenties voordeed.  Hierdoor worden de voorspellingsscores scheef scheef gemaakt door LUIS de verkeerde intentie voor de utterance te leren.

Wanneer een uiting wordt voorspeld als de intentie Geen, kan de clienttoepassing meer vragen stellen of een menu bieden om de gebruiker naar geldige keuzes te leiden.

## <a name="negative-intentions"></a>Negatieve bedoelingen
Als u negatieve en positieve bedoelingen wilt  bepalen, zoals 'Ik  wil een auto' en 'Ik wil geen auto', kunt u twee intenties maken (één positief en één negatief) en de juiste uitingen voor elk intentie toevoegen. U kunt ook één intentie maken en de twee verschillende positieve en negatieve termen markeren als een entiteit.

## <a name="intents-and-patterns"></a>Intenties en patronen

Als u voorbeelduitingen hebt, die kunnen worden gedefinieerd als een reguliere [](luis-concept-entity-types.md#regex-entity) expressie, kunt u overwegen om de entiteit met de reguliere expressie te gebruiken die is gekoppeld aan een [patroon](luis-concept-patterns.md).

Het gebruik van een entiteit in de reguliere expressie garandeert de gegevensextractie, zodat het patroon wordt gematcht. De patroonovereenkomst garandeert dat een exacte intentie wordt geretourneerd.

## <a name="intent-balance"></a>Intentiesaldo
De intenties van het app-domein moeten een balans hebben tussen utterances voor elke intentie. Niet één intentie hebben met 10 utterances en een andere intentie met 500 utterances. Dit is niet evenwichtig. Als u deze situatie hebt, bekijkt u de intentie met 500 utterances om te zien of veel van de intenties opnieuw kunnen worden ingedeeld in een [patroon](luis-concept-patterns.md).

De **intentie** None is niet opgenomen in het saldo. Deze intentie moet 10% van het totale aantal utterances in de app bevatten.

## <a name="intent-limits"></a>Limieten voor intenties
Bekijk [de](luis-limits.md#model-boundaries) limieten om te begrijpen hoeveel intenties u aan een model kunt toevoegen.

### <a name="if-you-need-more-than-the-maximum-number-of-intents"></a>Als u meer dan het maximum aantal intenties nodig hebt
Bedenk eerst of uw systeem te veel intenties gebruikt.

### <a name="can-multiple-intents-be-combined-into-single-intent-with-entities"></a>Kunnen meerdere intenties worden gecombineerd tot één intentie met entiteiten
Intenties die te vergelijkbaar zijn, kunnen het voor LUIS moeilijker maken om er onderscheid tussen te maken. Intenties moeten voldoende variëren om de belangrijkste taken vast te leggen die de gebruiker vraagt, maar ze hoeven niet elk pad vast te leggen dat door uw code wordt gebruikt. BookFlight en FlightCustomerService kunnen bijvoorbeeld afzonderlijke intenties zijn in een reis-app, maar BookInternationFlight en BookDomesticFlight zijn te vergelijkbaar. Als uw systeem deze moet onderscheiden, gebruikt u entiteiten of andere logica in plaats van intenties.

### <a name="dispatcher-model"></a>Dispatchermodel
Meer informatie over het combineren van LUIS- en QnA Maker-apps met het [verzendmodel](luis-concept-enterprise.md#when-you-need-to-combine-several-luis-and-qna-maker-apps).

### <a name="request-help-for-apps-with-significant-number-of-intents"></a>Hulp vragen voor apps met een groot aantal intenties
Als het verminderen van het aantal intenties of het onderverdelen van uw intenties in meerdere apps niet werkt, neemt u contact op met de ondersteuning. Als uw Azure-abonnement ondersteuningsservices bevat, neem dan contact op met [de technische ondersteuning van Azure.](https://azure.microsoft.com/support/options/)

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [entiteiten](luis-concept-entity-types.md), wat belangrijke woorden zijn die relevant zijn voor intenties
* Meer informatie over het [toevoegen en beheren van intenties](luis-how-to-add-intents.md) in uw LUIS-app.
* Best practices voor [intentie beoordelen](luis-concept-best-practices.md)