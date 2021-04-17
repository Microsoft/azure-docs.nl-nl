---
title: Best practices voor het bouwen van uw LUIS-app
description: Meer informatie over de best practices voor het krijgen van de beste resultaten van het model van uw LUIS-app.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/13/2021
ms.openlocfilehash: d5fa2a1e865a4f54de268e7ad756d1d4363f3b78
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107500206"
---
# <a name="best-practices-for-building-a-language-understanding-luis-app"></a>Best practices voor het bouwen van een LUIS-app (Language Understanding)
Gebruik het ontwerpproces voor apps om uw LUIS-app te bouwen:

* Taalmodellen bouwen (intenties en entiteiten)
* Enkele trainingsvoorbeeld-utterances toevoegen (15-30 per intentie)
* Publiceren naar eindpunt
* Testen vanaf eindpunt

Zodra uw app is [gepubliceerd, gebruikt](luis-how-to-publish-app.md)u de ontwikkelingslevenscyclus om functies toe te voegen, te publiceren en te testen vanaf het eindpunt. Begin niet met de volgende ontwerpcyclus door meer voorbeeld-utterances toe te voegen, omdat LUIS dan uw model niet kan leren met echte gebruikers-utterances.

Vouw de utterances pas uit als de huidige set met voorbeeld- en eindpuntuitingen vertrouwensscores met hoge voorspellingen retourneert. Scores verbeteren met [behulp van actief leren.](luis-concept-review-endpoint-utterances.md)




## <a name="do-and-dont"></a>Doen en niet doen
De volgende lijst bevat best practices voor LUIS-apps:

|Wel doen|Niet doen|
|--|--|
|[Uw schema plannen](#do-plan-your-schema)|[Bouwen en publiceren zonder plan](#dont-publish-too-quickly)|
|[Afzonderlijke intenties definiëren](#do-define-distinct-intents)<br>[Functies toevoegen aan intenties](#do-add-features-to-intents)<br>
[Door machine geleerde entiteiten gebruiken](#do-use-machine-learned-entities) |[Veel voorbeeld-utterances toevoegen aan intenties](#dont-add-many-example-utterances-to-intents)<br>[Enkele of eenvoudige entiteiten gebruiken](#dont-use-few-or-simple-entities) |
|[Een goede plaats vinden tussen te algemeen en te specifiek voor elke intentie](#do-find-sweet-spot-for-intents)|[LUIS gebruiken als trainingsplatform](#dont-use-luis-as-a-training-platform)|
|[Uw app iteratief bouwen met versies](#do-build-your-app-iteratively-with-versions)<br>[Entiteiten bouwen voor modeldecompositie](#do-build-for-model-decomposition)|[Veel voorbeeld-utterances van dezelfde indeling toevoegen, waarbij andere indelingen worden genegeerd](#dont-add-many-example-utterances-of-the-same-format-ignoring-other-formats)|
|[Patronen toevoegen in latere iteraties](#do-add-patterns-in-later-iterations)|[De definitie van intenties en entiteiten combineren](#dont-mix-the-definition-of-intents-and-entities)|
|[Balanceer uw utterances over alle intenties,](#balance-your-utterances-across-all-intents) met uitzondering van de intentie None.<br>[Voorbeeld-utterances toevoegen aan de intentie None](#do-add-example-utterances-to-none-intent)|[Woordgroepenlijsten met alle mogelijke waarden maken](#dont-create-phrase-lists-with-all-the-possible-values)|
|[De functie Voorstellen gebruiken voor actief leren](#do-leverage-the-suggest-feature-for-active-learning)|[Te veel patronen toevoegen](#dont-add-many-patterns)|
|[De prestaties van uw app bewaken met batchtests](#do-monitor-the-performance-of-your-app)|[Trainen en publiceren met elke toegevoegde voorbeeld-utterance](#dont-train-and-publish-with-every-single-example-utterance)|

## <a name="do-plan-your-schema"></a>Uw schema plannen

Voordat u begint met het bouwen van het schema van uw app, moet u bepalen wat en waar u van plan bent om deze app te gebruiken. Hoe grondiger en specifieker uw planning, hoe beter uw app wordt.

* Gerichte gebruikers onderzoeken
* End-to-end-persona's definiëren om uw app te vertegenwoordigen: stem, avatar, probleemafhandeling (proactief, reactief)
* Gebruikersinteracties (tekst, spraak) via welke kanalen identificeren, bestaande oplossingen leveren of een nieuwe oplossing voor deze app maken
* End-to-end gebruikerstraject
    * Wat u ervan kunt verwachten dat deze app wel en niet doet? * Wat zijn de prioriteiten van wat het moet doen?
    * Wat zijn de belangrijkste gebruiksgevallen?
* Gegevens verzamelen - [meer informatie over](data-collection.md) het verzamelen en voorbereiden van gegevens

## <a name="do-define-distinct-intents"></a>Definieer wel afzonderlijke intenties
Zorg ervoor dat de woordenlijst voor elke intentie alleen voor die intentie is en niet overlapt met een andere intentie. Als u bijvoorbeeld een app wilt hebben waarmee reisafspraken worden verwerkt, zoals vluchten en hotels, kunt u ervoor kiezen om deze onderwerpgebieden als afzonderlijke intenties te gebruiken of dezelfde intentie met entiteiten voor specifieke gegevens in de utterance.

Als de woordenlijst tussen twee intenties hetzelfde is, combineert u de intentie en gebruikt u entiteiten.

Kijk eens naar de volgende voorbeeld-utterances:

|Voorbeelden van utterances|
|--|
|Een vlucht boeken|
|Een hotel boeken|

`Book a flight` en `Book a hotel` gebruiken dezelfde woordenlijst als `book a ` . Deze indeling is hetzelfde en moet dus dezelfde intentie zijn met de verschillende woorden van `flight` en `hotel` als geëxtraheerde entiteiten.

## <a name="do-add-features-to-intents"></a>Functies toevoegen aan intenties

Functies beschrijven concepten voor een intentie. Een functie kan een woordgroepenlijst zijn met woorden die belangrijk zijn voor die intentie of een entiteit die belangrijk is voor die intentie.

## <a name="do-find-sweet-spot-for-intents"></a>Vind eens een goede plaats voor intenties
Gebruik voorspellingsgegevens van LUIS om te bepalen of uw intenties overlappen. Overlappende intenties zijn verwarrend voor LUIS. Het resultaat is dat de best scorende intentie te dicht bij een andere intentie ligt. Omdat LUIS niet steeds exact hetzelfde pad door de gegevens gebruikt voor training, heeft een overlappende intentie de kans om de eerste of tweede te zijn in de training. U wilt dat de score van de uiting voor elke intentie verder van elkaar is gescheiden, zodat deze flip/pper niet wordt gebruikt. Een goed onderscheid voor intenties moet elke keer resulteren in de verwachte topintentie.

## <a name="do-use-machine-learned-entities"></a>Machine learned-entiteiten gebruiken

Door machine geleerde entiteiten zijn afgestemd op uw app en vereisen labeling om succesvol te zijn. Als u geen machine learned-entiteiten gebruikt, gebruikt u mogelijk het verkeerde hulpprogramma.

Door machine geleerde entiteiten kunnen andere entiteiten als functies gebruiken. Deze andere entiteiten kunnen aangepaste entiteiten zijn, zoals entiteiten met reguliere expressies of lijstentiteiten, of u kunt vooraf gemaakte entiteiten gebruiken als functies.

Meer informatie [over effectieve machine learned-entiteiten.](luis-concept-entity-types.md#machine-learned-ml-entity)

<a name="#do-build-the-app-iteratively"></a>

## <a name="do-build-your-app-iteratively-with-versions"></a>Uw app iteratief bouwen met versies

Elke ontwerpcyclus moet binnen een nieuwe versie [zijn, gekloond](./luis-concept-app-iteration.md)van een bestaande versie.

## <a name="do-build-for-model-decomposition"></a>Bouwen voor modeldecompositie

Modeldecompositie heeft een typisch proces van:

* Intent **maken op** basis van de gebruikersintenties van de client-app
* 15-30 voorbeeld-utterances toevoegen op basis van gebruikersinvoer in de echte wereld
* het gegevensconcept op het hoogste niveau labelen in een voorbeeld-utterance
* gegevensconcept in subentiteiten op te breken
* functies toevoegen aan subentiteiten
* functies toevoegen aan intenties

Nadat u de intentie hebt gemaakt en voorbeeld-utterances hebt toegevoegd, wordt in het volgende voorbeeld de uitcompositie van entiteiten beschreven.

Begin met het identificeren van volledige gegevensconcepten die u wilt extraheren in een utterance. Dit is uw machine learning-entiteit. De woordgroep vervolgens opdelen in de onderdelen ervan. Dit omvat het identificeren van subentiteiten en functies.

Als u bijvoorbeeld een adres wilt extraheren, kan de belangrijkste machine learning-entiteit worden `Address` aangeroepen. Identificeer tijdens het maken van het adres enkele subentiteiten, zoals adres, plaats, staat en postcode.

Ga door met het opdelen van deze elementen door:
* Het toevoegen van een vereiste functie van de postcode als een reguliere expressie-entiteit.
* Het adres opdelen in delen:
    * Een **straatnummer met** een vereiste functie van een vooraf gebouwde entiteit van het nummer.
    * Een **straatnaam.**
    * Een **straattype** met een vereiste functie van een lijstentiteit, inclusief woorden zoals weg, cirkel, weg en weg.

Met de V3-ontwerp-API kan model worden ontleding mogelijk.

## <a name="do-add-patterns-in-later-iterations"></a>Voeg patronen toe in latere iteraties

U moet begrijpen hoe de [](luis-concept-patterns.md) app zich gedraagt voordat u patronen toevoegt, omdat patronen zwaarder worden gewogen dan voorbeeld-utterances en de betrouwbaarheid afscheeft.

Zodra u begrijpt hoe uw app zich gedraagt, voegt u patronen toe wanneer deze van toepassing zijn op uw app. U hoeft ze niet bij elke [iteratie toe te voegen.](luis-concept-app-iteration.md)

Het kan geen kwaad om ze toe te voegen aan het begin van uw modelontwerp, maar het is gemakkelijker om te zien hoe elk patroon het model verandert nadat het model is getest met utterances.

<a name="balance-your-utterances-across-all-intents"></a>

## <a name="do-balance-your-utterances-across-all-intents"></a>Uw uitingen over alle intenties in balans brengen

Als u wilt dat LUIS-voorspellingen nauwkeurig zijn, moet het aantal voorbeeld-utterances in elke intentie (met uitzondering van de intentie None) relatief gelijk zijn.

Als u een intentie hebt met 100 voorbeeld-utterances en een intentie met 20 voorbeeld-utterances, heeft de intentie van 100 utterances een hogere voorspellingsfrequentie.

## <a name="do-add-example-utterances-to-none-intent"></a>Voeg voorbeeld-utterances toe aan de intentie None

Deze intentie is de terugvalintentie die alles buiten uw toepassing aangeeft. Voeg één voorbeeld-utterance toe aan de intentie None voor elke 10 voorbeeld-utterances in de rest van uw LUIS-app.

## <a name="do-leverage-the-suggest-feature-for-active-learning"></a>Maak gebruik van de functie voorstellen voor actief leren

Gebruik [regelmatig de](luis-how-to-review-endpoint-utterances.md)Eindpunt-utterances controleren van actief leren in plaats van meer voorbeeld-utterances toe te voegen aan intenties.  Omdat de app voortdurend eindpunt-utterances ontvangt, groeit en verandert deze lijst.

## <a name="do-monitor-the-performance-of-your-app"></a>De prestaties van uw app bewaken

Controleer de nauwkeurigheid van de voorspelling met behulp van een [batchtestset.](./luis-how-to-batch-test.md)

Bewaar een afzonderlijke set utterances die niet worden gebruikt als [voorbeeld-utterances](luis-concept-utterance.md) of eindpunt-utterances. Blijf de app voor uw testset verbeteren. Pas de testset aan om echte uitingen van gebruikers weer te geven. Gebruik deze testset om elke iteratie of versie van de app te evalueren.

## <a name="dont-publish-too-quickly"></a>Niet te snel publiceren

Het te snel publiceren van uw app zonder [de juiste planning](#do-plan-your-schema)kan leiden tot verschillende problemen, zoals:

* Uw app werkt niet in uw werkelijke scenario op een acceptabel prestatieniveau.
* Het schema (intenties en entiteiten) is niet geschikt. Als u logica voor client-apps hebt ontwikkeld volgens het schema, moet u dat mogelijk helemaal opnieuw schrijven. Dit zou leiden tot onverwachte vertragingen en extra kosten voor het project waar u aan werkt.
* Uitingen die u aan het model toevoegt, kunnen leiden tot vooroordelen ten opzichte van de voorbeeld-utteranceset die moeilijk te vinden en te identificeren is. Dit maakt het ook moeilijk om dubbelzinnigheid te verwijderen nadat u zich hebt verplicht tot een bepaald schema.

## <a name="dont-add-many-example-utterances-to-intents"></a>Voeg niet veel voorbeeld-utterances toe aan intenties

Nadat de app is gepubliceerd, voegt u alleen utterances van actief leren toe in het ontwikkelingslevenscyclusproces. Als uitingen te vergelijkbaar zijn, voegt u een patroon toe.

## <a name="dont-use-few-or-simple-entities"></a>Gebruik geen enkele of eenvoudige entiteiten

Entiteiten zijn gebouwd voor gegevensextractie en voorspelling. Het is belangrijk dat elke intentie machine learning-entiteiten heeft die de gegevens in de intentie beschrijven. Dit helpt LUIS bij het voorspellen van de intentie, zelfs als uw clienttoepassing de geëxtraheerde entiteit niet hoeft te gebruiken.

## <a name="dont-use-luis-as-a-training-platform"></a>LUIS niet gebruiken als trainingsplatform

LUIS is specifiek voor het domein van een taalmodel. Het is niet bedoeld om te werken als een algemeen trainingsplatform voor natuurlijke taal.

## <a name="dont-add-many-example-utterances-of-the-same-format-ignoring-other-formats"></a>Voeg niet veel voorbeeld-utterances van dezelfde indeling toe, waarbij andere indelingen worden genegeerd

LUIS verwacht variaties in de utterances van een intentie. De utterances kunnen variëren terwijl ze dezelfde algemene betekenis hebben. Variaties kunnen bestaan uit lengte van utterances, woordkeuze en plaatsing van woorden.

|Gebruik niet dezelfde indeling|Gebruik een verschillende indeling|
|--|--|
|Een ticket kopen voor Seattle<br>Een ticket kopen naar Parijs<br>Een ticket kopen voor Orlando|1 ticket kopen voor Seattle<br>Reserveer volgende maandag twee plaatsen op het rode oog naar Parijs<br>Ik wil 3 tickets naar Orlando boeken voor de voorjaarsbreak|

In de tweede kolom worden verschillende werkwoorden gebruikt (kopen, reserveren, boeken), verschillende hoeveelheden (1, twee, 3) en verschillende woordafspraken, maar ze hebben allemaal dezelfde intentie om luchtvaarttickets voor reizen te kopen.

## <a name="dont-mix-the-definition-of-intents-and-entities"></a>Combineer de definitie van intenties en entiteiten niet

Maak een intentie voor elke actie die uw bot zal ondernemen. Gebruik entiteiten als parameters die die actie mogelijk maken.

Maak een **BookFlight-intentie** voor een bot die vluchten van luchtvaartmaatschappijen gaat boeken. Maak geen intentie voor elke luchtvaartmaatschappij of elke bestemming. Gebruik deze gegevens als entiteiten [en](luis-concept-entity-types.md) markeer ze in de voorbeeld-utterances.

## <a name="dont-create-phrase-lists-with-all-the-possible-values"></a>Maak geen woordgroepenlijsten met alle mogelijke waarden

Geef een paar voorbeelden op in de [woordgroepenlijsten,](luis-concept-feature.md) maar niet elk woord of woordgroep. LUIS generaliseert en houdt rekening met context.

## <a name="dont-add-many-patterns"></a>Voeg niet veel patronen toe

Voeg niet te veel patronen [toe.](luis-concept-patterns.md) LUIS is bedoeld om snel te leren met minder voorbeelden. Overbelast het systeem niet onnodig.

## <a name="dont-train-and-publish-with-every-single-example-utterance"></a>Train en publiceer niet met elke voorbeeld-utterance

Voeg 10 of 15 utterances toe voordat u gaat trainen en publiceren. Zo kunt u de impact op de nauwkeurigheid van de voorspelling zien. Het toevoegen van één utterance heeft mogelijk geen zichtbare invloed op de score.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [plannen van uw app](luis-how-plan-your-app.md) in uw LUIS-app.