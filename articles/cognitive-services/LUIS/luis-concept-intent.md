---
title: Intents en entiteiten-LUIS
titleSuffix: Azure Cognitive Services
description: Eén intentie vertegenwoordigt een taak of actie die de gebruiker wil uitvoeren. Het is een doel, uitgedrukt in van de uiting van een gebruiker. Definieer een set intenties die overeenkomt met acties die gebruikers in uw toepassing willen uitvoeren.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/10/2019
ms.openlocfilehash: bf5ab7361c84fb787366c7c361829e52362fe427
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "95025905"
---
# <a name="intents-in-your-luis-app"></a>Intenties in uw LUIS-app

Een intentie vertegenwoordigt een taak of actie die de gebruiker wil uitvoeren. Het is een doel of doel dat wordt weer gegeven in de [utterance](luis-concept-utterance.md)van een gebruiker.

Definieer een set intenties die overeenkomt met acties die gebruikers in uw toepassing willen uitvoeren. Een reis-app definieert bijvoorbeeld verschillende intenties:

App-intenties voor reizen   |   Voorbeelden van utterances   |
------|------|
 BookFlight     |   "Boek me een vlucht naar Rio volgende week" <br/> "Vlucht naar Rio op het 24" <br/> "Ik heb een vlak ticket nodig volgende zondag tot Rio de Janeiro"    |
 Begroeting     |   Hoi <br/>Hallo <br/>Goedemorgen  |
 Weersverwachting | ' Wat is de weers verwachting zoals in Boston? ' <br/> De prognose voor dit weekend weer geven |
 Geen         | Een recept voor een cookie ontvangen<br>"Heeft de Lakeers gewonnen?" |

Alle toepassingen worden geleverd met de vooraf gedefinieerde intentie, '[geen](#none-intent)', de terugval opzet.

## <a name="prebuilt-domains-provide-intents"></a>Vooraf ontwikkelde domeinen bieden intenties
Naast de intenties die u definieert, kunt u vooraf ontwikkelde intenties gebruiken uit een van de [vooraf ontwikkelde domeinen](./howto-add-prebuilt-models.md).

## <a name="return-all-intents-scores"></a>Alle uitstaande scores van intenties retour neren
U wijst een utterance toe aan één intentie. Wanneer LUIS een utterance op het eind punt ontvangt, wordt standaard het hoogste doel voor die utterance geretourneerd.

Als u de scores voor alle intenties voor de utterance wilt, kunt u een vlag geven op de query reeks van de prediction-API.

|API-versie voor voor spelling|Vlag|
|--|--|
|V2|`verbose=true`|
|V3|`show-all-intents=true`|

## <a name="intent-compared-to-entity"></a>Intentie vergeleken met entiteit
De intentie vertegenwoordigt een actie die de toepassing moet uitvoeren voor de gebruiker en is gebaseerd op de hele utterance. Een utterance kan slechts één bovenste Score intentie hebben, maar kan een groot aantal entiteiten hebben.

<a name="how-do-intents-relate-to-entities"></a>

Maak een intentie wanneer de _bedoeling_ van de gebruiker een actie in uw client toepassing zou activeren, zoals een aanroep van de functie checkweather (). Maak vervolgens entiteiten om de vereiste para meters voor het uitvoeren van de actie weer te geven.

|Intentie   | Entiteit | Voorbeeld van een utterance   |
|------------------|------------------------------|------------------------------|
| Weersverwachting | {"type": "locatie", "entiteit": "Seattle"}<br>{"type": "Builtin. datetimeV2. date", "entiteit": "morgen", "oplossing": "2018-05-23"} | Wat is het weer `Seattle` `tomorrow` ? |
| Weersverwachting | {"type": "date_range", "entiteit": "dit weekend"} | De prognose voor weer geven `this weekend` |
||||

## <a name="prebuilt-domain-intents"></a>Vooraf ontwikkelde domein intentie

[Vooraf ontwikkelde domeinen](./howto-add-prebuilt-models.md) bieden intenties met uitingen.

## <a name="none-intent"></a>None- intent

**Geen** intentie wordt gemaakt, maar is op het doel leeg gelaten. **Geen** intentie is een vereiste intentie en kan niet worden verwijderd of de naam ervan kan niet worden gewijzigd. Vul deze met uitingen die buiten uw domein vallen.

De **geen** intentie is de terugval intentie, die belang rijk is voor elke app, en moet 10% van de totale uitingen hebben. Dit wordt gebruikt om LUIS uitingen te leren die niet belang rijk zijn in het app-domein (onderwerpgebied). Als u geen uitingen toevoegt voor de **geen** intentie, dwingt Luis een utterance af die zich buiten het domein bevindt in een van de domein intentie. Hierdoor worden de Voorspellings scores schuingetrokken door LUIS de verkeerde intentie te geven voor de utterance.

Wanneer een utterance wordt voor speld als geen intentie, kan de client toepassing meer vragen stellen of een menu opgeven om de gebruiker naar geldige keuzes te sturen.

## <a name="negative-intentions"></a>Negatieve voornemens
Als u negatieve en positieve doel stellingen wilt bepalen, zoals "ik **wil** een auto" en "Ik wil een auto **niet** willen", kunt u twee intenten (één positieve en één negatieve) maken en voor elk het juiste uitingen toevoegen. U kunt ook één intentie maken en de twee verschillende positieve en negatieve voor waarden markeren als een entiteit.

## <a name="intents-and-patterns"></a>Doel stellingen en patronen

Als u bijvoorbeeld uitingen hebt, die in een deel of geheel kunnen worden gedefinieerd als een reguliere expressie, kunt u overwegen de [reguliere expressie-entiteit](luis-concept-entity-types.md#regular-expression-entity) te gebruiken die is gekoppeld aan een [patroon](luis-concept-patterns.md).

Het gebruik van een reguliere expressie-entiteit waarborgt de extractie van gegevens zodat het patroon wordt vergeleken. Het patroon dat overeenkomt met een exacte intentie, wordt geretourneerd.

## <a name="intent-balance"></a>Saldo van intentie
De intenties van het app-domein moeten een evenwicht hebben tussen uitingen in elke intentie. Hebben niet één intentie met 10 uitingen en een andere intentie met 500 uitingen. Dit is niet in balans. Als dit het geval is, raadpleegt u de intentie met 500 uitingen om te zien of veel van de intenties in een [patroon](luis-concept-patterns.md)kunnen worden ingedeeld.

**Geen** intentie is in het saldo opgenomen. Dat intentie moet 10% van het totale aantal uitingen in de app bevatten.

## <a name="intent-limits"></a>Limieten voor intenties
Bekijk de [limieten](luis-limits.md#model-boundaries) om inzicht te krijgen in het aantal intenties dat u aan een model kunt toevoegen.

### <a name="if-you-need-more-than-the-maximum-number-of-intents"></a>Als u meer dan het maximum aantal intenties nodig hebt
Houd eerst rekening met het feit of uw systeem te veel intenties gebruikt.

### <a name="can-multiple-intents-be-combined-into-single-intent-with-entities"></a>Kan meerdere intenties worden gecombineerd tot één intentie met entiteiten
Intenties die te vergelijkbaar zijn, kunnen het moeilijker maken om de LUIS te onderscheiden. Intenties moeten voldoende worden aangepast om de belangrijkste taken vast te leggen waarvoor de gebruiker wordt gevraagd, maar ze hoeven niet elk pad op te nemen dat door de code wordt gebruikt. BookFlight en FlightCustomerService kunnen bijvoorbeeld afzonderlijke intenties zijn in een reis-app, maar BookInternationalFlight en BookDomesticFlight zijn te vergelijkbaar. Als uw systeem ze moet onderscheiden, gebruikt u entiteiten of andere logica in plaats van intents.

### <a name="dispatcher-model"></a>Model voor dispatcher
Meer informatie over het combi neren van LUIS-en QnA Maker-apps met het [Verzend model](luis-concept-enterprise.md#when-you-need-to-combine-several-luis-and-qna-maker-apps).

### <a name="request-help-for-apps-with-significant-number-of-intents"></a>Hulp vragen voor apps met een groot aantal intenties
Neem contact op met de ondersteuning als het aantal intenties of het delen van uw intenties in meerdere apps niet geschikt is voor u. Als uw Azure-abonnement ondersteunings services bevat, neemt u contact op met de [technische ondersteuning van Azure](https://azure.microsoft.com/support/options/).

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [entiteiten](luis-concept-entity-types.md), die belang rijke woorden zijn die relevant zijn voor intenties
* Meer informatie over het [toevoegen en beheren van intenties](luis-how-to-add-intents.md) in uw Luis-app.
* [Aanbevolen procedures](luis-concept-best-practices.md) voor het controleren van de intentie