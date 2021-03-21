---
title: Uw app plannen-LUIS
description: Betrek relevante app-intenties en entiteiten en maak vervolgens uw toepassings abonnementen in Language Understanding intelligent Services (LUIS).
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 05/14/2020
ms.openlocfilehash: 66df23466694fe8b9caea4e56565cde6d8fd7416
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "95018953"
---
# <a name="plan-your-luis-app-schema-with-subject-domain-and-data-extraction"></a>Uw LUIS-app-schema plannen met onderwerp domein en gegevens extractie

Een LUIS-app-schema bevat [intents](luis-glossary.md#intent) en [entiteiten](luis-glossary.md#entity) die relevant zijn voor uw onderwerps [domein](luis-glossary.md#domain). De intenties classificeren de gebruiker [uitingen](luis-glossary.md#utterance)en de entiteiten halen gegevens op uit de uitingen van de gebruiker.

## <a name="identify-your-domain"></a>Uw domein identificeren

Een LUIS-app wordt gecentreerd rond een onderwerps domein. U kunt bijvoorbeeld een reis-app hebben die de boeking van tickets, vluchten, hotels en huur auto's afhandelt. Een andere app kan inhoud bevatten die betrekking heeft op de uitoefening, het bijhouden van de geschiktheid en het instellen van doelen. Het identificeren van het domein helpt u bij het vinden van woorden of zinsdelen die relevant zijn voor uw domein.

> [!TIP]
> LUIS biedt [vooraf gemaakte domeinen](./howto-add-prebuilt-models.md) voor veel algemene scenario's. Controleer of u een vooraf gebouwd domein kunt gebruiken als uitgangs punt voor uw app.

## <a name="identify-your-intents"></a>Uw intenties identificeren

Denk na over de [intenties](luis-concept-intent.md) die belang rijk zijn voor de taak van uw toepassing.

Laten we het voor beeld van een reis-app bekijken, met functies voor het boeken van een vlucht en het weer geven van de gebruikers bestemming. U kunt de `BookFlight` en- `GetWeather` intentie definiëren voor deze acties.

In een complexere app met meer functies hebt u meer intenties en moet u ze zorgvuldig definiëren, zodat de intenties niet te specifiek zijn. Het kan bijvoorbeeld `BookFlight` `BookHotel` nodig zijn om afzonderlijke intenties te maken, maar `BookInternationalFlight` dit `BookDomesticFlight` kan te vergelijkbaar zijn.

> [!NOTE]
> Het is een best practice voor het gebruik van slechts zoveel intenties als u nodig hebt om de functies van uw app uit te voeren. Als u te veel intenties definieert, wordt het moeilijker voor LUIS om uitingen correct te classificeren. Als u te weinig definieert, zijn ze mogelijk zo algemeen dat ze elkaar overlappen.

Als u geen algemene gebruikers intentie wilt identificeren, voegt u alle voor beeld-gebruikers uitingen toe aan de `None` bedoeling. Als uw app groeit tot meer intenties, kunt u deze later maken.

## <a name="create-example-utterances-for-each-intent"></a>Voor beeld uitingen maken voor elke intentie

Voorkom dat u begint met het maken van te veel uitingen voor elke intentie. Nadat u de intenties hebt bepaald, maakt u 15 tot 30 voorbeeld uitingen per intentie. Elke utterance moet afwijken van de eerder door gegeven uitingen. Een goed voor deel in uitingen omvat het totale aantal woorden, woorden, werkwoordsvormen en [Lees tekens](luis-reference-application-settings.md#punctuation-normalization).

Zie goed te weten [uitingen voor Luis-apps](luis-concept-utterance.md)voor meer informatie.

## <a name="identify-your-entities"></a>Uw entiteiten identificeren

In het voor beeld uitingen identificeert u de entiteiten die u wilt extra heren. Als u een vlucht wilt boeken, hebt u informatie nodig zoals de doel-, datum-, vlieg tickets-, ticket categorie-en reis klasse. Maak entiteiten voor deze gegevens typen en markeer vervolgens de [entiteiten](luis-concept-entity-types.md) in het voor beeld uitingen. Entiteiten zijn belang rijk voor het uitvoeren van een intentie.

Wanneer u bepaalt welke entiteiten in uw app moeten worden gebruikt, moet u er ook voor zorgen dat er verschillende typen entiteiten zijn voor het vastleggen van relaties tussen object typen. [Entiteiten in Luis](luis-concept-entity-types.md) bieden meer details over de verschillende typen.

> [!TIP]
> LUIS biedt [vooraf gemaakte entiteiten](./howto-add-prebuilt-models.md) voor veelvoorkomende, gespreks gebruikers scenario's. Overweeg het gebruik van vooraf gemaakte entiteiten als uitgangs punt voor de ontwikkeling van uw toepassing.

## <a name="resolution-with-intent-or-entity"></a>Oplossing met intentie of entiteit?

In veel gevallen, met name bij het werken met natuurlijk gesprek, bieden gebruikers een utterance die meer dan een functie of intentie kan bevatten. Om dit op te lossen, moet u weten dat de weer gave van de uitvoer kan worden uitgevoerd in zowel de intenties als de entiteiten. Deze representatie moet worden toewijst aan de acties van uw client toepassing en hoeft niet te worden beperkt tot de intenties.

**Int-vorderingen-ties** is het concept dat acties (meestal begrepen als intenties) ook als entiteiten kunnen worden vastgelegd en in dit formulier in de uitvoer-JSON kunnen worden opgenomen, waar u het kunt toewijzen aan een specifieke actie. _Negatie_ is een gemeen schappelijk gebruik om deze afhankelijkheid te benutten voor zowel de intentie als de entiteit voor volledige extractie.

Houd rekening met de volgende twee uitingen die de keuze van woorden zeer dicht in overweging nemen, maar die verschillende resultaten hebben:

|Uiting|
|--|
|`Please schedule my flight from Cairo to Seattle`|
|`Cancel my flight from Cairo to Seattle`|

In plaats van twee afzonderlijke intenties te maken, maakt u één intentie met een `FlightAction` machine learning-entiteit. De machine learning entiteit moet de details van de actie extra heren voor zowel een planning als een annulerings aanvraag, evenals een oorsprong of bestemmings locatie.

De `FlightAction` entiteit zou worden gestructureerd in het volgende pseudo-schema van machine learning entiteit en subentiteiten:

* FlightAction
    * Bewerking
    * Oorsprong
    * Doel

Om het uitpakken van functies toe te voegen aan de subentiteiten. U kiest uw functies op basis van de woorden lijst die u verwacht te zien in de uitingen van de gebruiker en de waarden die u in het Voorspellings antwoord wilt ontvangen.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Learning the LUIS Development lifecylce](luis-concept-app-iteration.md)