---
title: Aanbevolen procedures voor het bouwen van uw LUIS-app
description: Meer informatie over de aanbevolen procedures voor het verkrijgen van de beste resultaten van het model van uw LUIS-app.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/17/2020
ms.openlocfilehash: 2f6ed85416cc5d7c3c2baba2b2cfe489e301d7e5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "98788482"
---
# <a name="best-practices-for-building-a-language-understanding-luis-app"></a>Aanbevolen procedures voor het bouwen van een LUIS-app (Language memorandum)
Gebruik het ontwerp proces voor apps om uw LUIS-app te bouwen:

* Bouw taal modellen (intents en entiteiten)
* Voeg een paar trainings voorbeeld uitingen (15-30 per intentie) toe
* Publiceren naar eind punt
* Testen vanaf eind punt

Nadat de app is [gepubliceerd](luis-how-to-publish-app.md), gebruikt u de ontwikkelings levenscyclus om functies toe te voegen, te publiceren en te testen vanaf het eind punt. Begin niet de volgende ontwerp cyclus door meer voorbeeld uitingen toe te voegen, omdat LUIS uw model niet kan leren kennen met de gebruikers uitingen van de echte wereld.

Vouw de uitingen niet uit totdat de huidige set van zowel het voor beeld als het eind punt uitingen vertrouwt, hoge Voorspellings scores. Verbeter scores met behulp van [actief leren](luis-concept-review-endpoint-utterances.md).




## <a name="do-and-dont"></a>Do en niet
De volgende lijst bevat aanbevolen procedures voor LUIS-apps:

|Wel doen|Niet doen|
|--|--|
|[Uw schema plannen](#do-plan-your-schema)|[Bouwen en publiceren zonder een abonnement](#dont-publish-too-quickly)|
|[Afzonderlijke intenties definiëren](#do-define-distinct-intents)<br>[Functies toevoegen aan intenties](#do-add-features-to-intents)<br>
[Door de machine geleerde entiteiten gebruiken](#do-use-machine-learned-entities) |[Een groot aantal voor beelden uitingen toevoegen aan intenties](#dont-add-many-example-utterances-to-intents)<br>[Gebruik enkele of eenvoudige entiteiten](#dont-use-few-or-simple-entities) |
|[Vind een zoete vlek tussen te algemeen en te specifiek voor elke intentie](#do-find-sweet-spot-for-intents)|[LUIS als trainings platform gebruiken](#dont-use-luis-as-a-training-platform)|
|[Uw app iteratief bouwen met versies](#do-build-your-app-iteratively-with-versions)<br>[Entiteiten bouwen voor model ontleding](#do-build-for-model-decomposition)|[Een groot aantal voor beeld-uitingen met dezelfde indeling toevoegen en andere indelingen negeren](#dont-add-many-example-utterances-of-the-same-format-ignoring-other-formats)|
|[Patronen in latere iteraties toevoegen](#do-add-patterns-in-later-iterations)|[De definitie van intenties en entiteiten combi neren](#dont-mix-the-definition-of-intents-and-entities)|
|[U hoeft uw uitingen te verdelen over alle intenties](#balance-your-utterances-across-all-intents) , met uitzonde ring van de geen intentie.<br>[Voor beeld uitingen toevoegen aan geen intentie](#do-add-example-utterances-to-none-intent)|[Woordgroepen lijsten met alle mogelijke waarden maken](#dont-create-phrase-lists-with-all-the-possible-values)|
|[Gebruik de functie Voorst Ellen voor actief leren](#do-leverage-the-suggest-feature-for-active-learning)|[Te veel patronen toevoegen](#dont-add-many-patterns)|
|[De prestaties van uw app bewaken met batch tests](#do-monitor-the-performance-of-your-app)|[Train en publiceer met elk enkel voor beeld utterance toegevoegd](#dont-train-and-publish-with-every-single-example-utterance)|

## <a name="do-plan-your-schema"></a>Plan uw schema

Voordat u begint met het bouwen van het schema van de app, moet u bepalen wat en waar u deze app gaat gebruiken. Hoe dieper en specifiek uw planning, hoe beter uw app wordt.

* Gerichte gebruikers onderzoeken
* End-to-end-personen definiëren voor uw app-Voice, avatar, afhandeling van problemen (proactieve, reactief)
* Gebruikers interacties (tekst, spraak) identificeren met kanalen, afleveren naar bestaande oplossingen of een nieuwe oplossing maken voor deze app
* End-to-end gebruikers traject
    * Wat moet u verwachten dat deze app niet kan worden uitgevoerd? * Wat zijn de prioriteiten van wat het moet doen?
    * Wat zijn de belangrijkste use cases?
* Gegevens verzamelen- [meer informatie](data-collection.md) over het verzamelen en voorbereiden van gegevens

## <a name="do-define-distinct-intents"></a>Afzonderlijke intenties definiëren
Zorg ervoor dat de woorden lijst voor elke intentie alleen voor dat doel is en niet overlapt met een ander doel. Als u bijvoorbeeld een app wilt hebben die reis regelingen afhandelt, zoals vlieg vluchten en hotels, kunt u ervoor kiezen om deze onderwerpgebieden als afzonderlijke intenties of hetzelfde doel te hebben met entiteiten voor specifieke gegevens binnen de utterance.

Als de vocabulaire tussen twee intenties hetzelfde is, combi neer u de intentie en gebruikt u entiteiten.

Bekijk het volgende voor beeld uitingen:

|Voorbeelden van utterances|
|--|
|Een vlucht boeken|
|Een hotel boeken|

`Book a flight` en `Book a hotel` gebruiken dezelfde woorden lijst van `book a ` . Deze indeling is hetzelfde, zodat deze hetzelfde doel moet zijn met de verschillende woorden van `flight` en `hotel` als geëxtraheerde entiteiten.

## <a name="do-add-features-to-intents"></a>Functies toevoegen aan intenties

Functies beschrijven concepten voor een intentie. Een functie kan een woordgroepen lijst zijn met woorden die belang rijk zijn voor dat doel of een entiteit die belang rijk is voor dat doel.

## <a name="do-find-sweet-spot-for-intents"></a>Vind een zoete plaats voor intenties
Gebruik Voorspellings gegevens uit LUIS om te bepalen of uw intentie elkaar overlappen. Overlappende intentiesen verwarren LUIS. Het resultaat is dat de bovenste Score intentie te dicht bij een andere intentie is. Omdat LUIS niet exact hetzelfde pad door de gegevens voor de training gebruikt, heeft een overlappende intentie een kans op het eerste of tweede in de training. U wilt dat de Score van de utterance voor elke intentie verder uit elkaar komt, zodat deze flip/flop niet plaatsvindt. Een goed onderscheiding voor intenties moet elke keer op de verwachte beste intentie worden uitgevoerd.

## <a name="do-use-machine-learned-entities"></a>Door de machine geleerde entiteiten gebruiken

Door de machine geleerde entiteiten worden aangepast aan uw app en vereisen een label. Als u geen door de machine geleerde entiteiten gebruikt, gebruikt u mogelijk het verkeerde hulp programma.

Door de machine geleerde entiteiten kunnen andere entiteiten als functies gebruiken. Deze andere entiteiten kunnen aangepaste entiteiten zijn, zoals reguliere expressie-entiteiten of lijst entiteiten, of u kunt vooraf gemaakte entiteiten gebruiken als-onderdelen.

Meer informatie over de [daad werkelijke door de machine geleerde entiteiten](luis-concept-entity-types.md#effective-machine-learned-entities).

<a name="#do-build-the-app-iteratively"></a>

## <a name="do-build-your-app-iteratively-with-versions"></a>Bouw uw app iteratief op met versies

Elke ontwerp cyclus moet zich in een nieuwe [versie](./luis-concept-app-iteration.md)bevinden, die is gekloond van een bestaande versie.

## <a name="do-build-for-model-decomposition"></a>Bouwen voor model ontleding

Model ontleding heeft een typisch proces van:

* **opzet** maken op basis van de bedoelingen van de gebruiker van de client-app
* 15-30-voor beeld uitingen toevoegen op basis van de werkelijke gebruikers invoer
* Label gegevens concept op het hoogste niveau in voor beeld utterance
* gegevens concept in subentiteiten opsplitsen
* onderdelen toevoegen aan subentiteiten
* functies toevoegen aan intenties

Wanneer u de intentie hebt gemaakt en voor beeld-uitingen hebt toegevoegd, wordt in het volgende voor beeld de entiteit ontleding beschreven.

Begin met het identificeren van de volledige gegevens concepten die u wilt uitpakken in een utterance. Dit is uw machine learning-entiteit. Splits de woord groep vervolgens in de bijbehorende onderdelen. Dit omvat het identificeren van subentiteiten en onderdelen.

Als u bijvoorbeeld een adres wilt extra heren, kan de bovenste machine learning-entiteit worden aangeroepen `Address` . Identificeer tijdens het maken van het adres een aantal subentiteiten, zoals het adres, de plaats, de provincie en de post code.

Ga door met het ontsamen stellen van deze elementen door:
* Het toevoegen van een vereiste functie van de post code als een reguliere expressie-entiteit.
* De samen stellen van het adres in delen:
    * Een **straat nummer** met een vereiste functie van een vooraf samengestelde entiteit van het nummer.
    * Een **straat naam**.
    * Een **straat type** met een vereiste functie van een lijst-entiteit, met inbegrip van woorden zoals verdenken, cirkel, weg en Lane.

Met de V3 authoring API kunt u model ontleden.

## <a name="do-add-patterns-in-later-iterations"></a>Voeg patronen toe in latere iteraties

U moet weten hoe de app zich gedraagt voordat [patronen](luis-concept-patterns.md) worden toegevoegd, omdat patronen meer sterk worden gewogen dan bijvoorbeeld uitingen en het vertrouwen wordt schuingetrokken.

Wanneer u begrijpt hoe uw app zich gedraagt, voegt u patronen toe die van toepassing zijn op uw app. U hoeft deze niet bij elke [herhaling](luis-concept-app-iteration.md)toe te voegen.

Er is geen kwaadief toevoegen aan het begin van uw model ontwerp, maar het is eenvoudiger om te zien hoe elk patroon het model wijzigt nadat het model is getest met uitingen.

<a name="balance-your-utterances-across-all-intents"></a>

## <a name="do-balance-your-utterances-across-all-intents"></a>Laat uw uitingen in balans voor alle intenties

Om ervoor te zorgen dat LUIS-voor spellingen nauw keurig zijn, moet de hoeveelheid voor beeld uitingen in elke intentie (met uitzonde ring van het geen doel) relatief gelijk zijn.

Als u een intentie hebt met 100-voor beeld-uitingen en een intentie met 20 voor beeld-uitingen, heeft de utterance-intentie van 100 een hoger tempo van voor spelling.

## <a name="do-add-example-utterances-to-none-intent"></a>Voeg voor beeld uitingen toe aan geen intentie

Dit is de terugval intentie, wat inhoudt dat u buiten uw toepassing kunt vinden. Voeg één voor beeld utterance toe aan de geen intentie voor elk 10 voor beeld uitingen in de rest van uw LUIS-app.

## <a name="do-leverage-the-suggest-feature-for-active-learning"></a>Gebruik de functie Voorst Ellen voor actief leren

Gebruik het **controle punt uitingen** van het [actieve leer proces](luis-how-to-review-endpoint-utterances.md)regel matig, in plaats van meer voor beeld-uitingen aan intenties toe te voegen. Omdat de app voortdurend eind punt uitingen ontvangt, wordt deze lijst steeds groter en gewijzigd.

## <a name="do-monitor-the-performance-of-your-app"></a>Bewaak de prestaties van uw app.

Bewaak de nauw keurigheid van de voor spelling met behulp van een [batch-testset](./luis-how-to-batch-test.md) .

Bewaar een afzonderlijke set uitingen die niet worden gebruikt als [voor beeld uitingen](luis-concept-utterance.md) of endpoint uitingen. Blijf de app verbeteren voor uw testset. Pas de testset aan om echte gebruikers uitingen weer te geven. Gebruik deze testset om elke iteratie of versie van de app te evalueren.

## <a name="dont-publish-too-quickly"></a>Niet te snel publiceren

Het publiceren van uw app is te snel, zonder [goed te plannen](#do-plan-your-schema), kan leiden tot diverse problemen, zoals:

* Uw app werkt niet in uw eigen scenario met een acceptabel prestatie niveau.
* Het schema (intents en entiteiten) zou niet geschikt zijn en als u client-app-logica hebt ontwikkeld volgens het schema, moet u deze mogelijk zelf herschrijven. Dit kan leiden tot onverwachte vertragingen en extra kosten voor het project waaraan u werkt.
* Uitingen die u aan het model toevoegt, kan leiden tot een afwijking van het voor beeld van utterance dat moeilijk te detecteren en identificeren. Ook wordt het verwijderen van ambiguïteit moeilijk nadat u een bepaald schema hebt doorgevoerd.

## <a name="dont-add-many-example-utterances-to-intents"></a>Voeg geen veel voor beeld-uitingen toe aan intents

Nadat de app is gepubliceerd, voegt u alleen uitingen toe van actief leren in het ontwikkelings levenscyclus proces. Als uitingen te vergelijkbaar zijn, voegt u een patroon toe.

## <a name="dont-use-few-or-simple-entities"></a>Gebruik geen enkele of eenvoudige entiteiten

Entiteiten zijn gebouwd voor het uitpakken en voors pellen van gegevens. Het is belang rijk dat elke intentie machine learning-entiteiten heeft die de gegevens in het doel beschrijven. Dit helpt LUIS de intentie te voors pellen, zelfs als uw client toepassing de geëxtraheerde entiteit niet hoeft te gebruiken.

## <a name="dont-use-luis-as-a-training-platform"></a>Gebruik LUIS niet als trainings platform

LUIS is specifiek voor het domein van een taal model. Het is niet bedoeld om te werken als een algemeen platform voor het trainen van natuurlijke taal.

## <a name="dont-add-many-example-utterances-of-the-same-format-ignoring-other-formats"></a>Voeg geen veel voor beeld-uitingen met dezelfde indeling toe, waarbij andere indelingen worden genegeerd

LUIS verwacht variaties in een uitingen van een intentie. De uitingen kan variëren en heeft dezelfde algemene betekenis. Variaties kunnen de utterance lengte, woorden keuze en woord plaatsing bevatten.

|Gebruik niet dezelfde indeling|Gebruik verschillende notaties|
|--|--|
|Een ticket kopen bij Seattle<br>Een ticket kopen bij Parijs<br>Een ticket kopen bij Orlando|1 ticket kopen bij Seattle<br>Reserveer twee stoelen op het rode oog tot Parijs volgende maandag<br>Ik wil 3 tickets boeken naar Orlando voor een lente-afbreek punt|

In de tweede kolom worden verschillende werk woorden (kopen, reserve ringen, boeken), verschillende hoeveel heden (1, 2, 3) en verschillende regelingen van woorden gebruikt, maar allemaal hetzelfde als het kopen van vlieg tickets voor reizen.

## <a name="dont-mix-the-definition-of-intents-and-entities"></a>De definitie van intents en entiteiten niet combi neren

Maak een intentie voor elke actie die uw bot gaat ondernemen. Gebruik entiteiten als para meters die deze actie mogelijk maken.

Voor een bot die vlieg vluchten gaat boeken, maakt u een **BookFlight** intentie. Maak geen intentie voor elke luchtvaart maatschappij of voor elke bestemming. Gebruik deze gegevens als [entiteiten](luis-concept-entity-types.md) en markeer deze in het voor beeld uitingen.

## <a name="dont-create-phrase-lists-with-all-the-possible-values"></a>Geen woordgroepen lijsten met alle mogelijke waarden maken

Geef enkele voor beelden in de [woordgroepen lijsten](luis-concept-feature.md) , maar niet alle woorden of woord groepen. LUIS generaliseert en houdt rekening met de context.

## <a name="dont-add-many-patterns"></a>Geen veel patronen toevoegen

Voeg niet te veel [patronen](luis-concept-patterns.md)toe. LUIS is bedoeld om snel te leren werken met minder voor beelden. U hoeft het systeem niet onnodig te overbelasten.

## <a name="dont-train-and-publish-with-every-single-example-utterance"></a>Train en publiceer niet met elk enkel voor beeld utterance

10 of 15 uitingen toevoegen vóór training en publicatie. Zo kunt u de gevolgen voor de nauw keurigheid van de voor spellingen weer geven. Het toevoegen van één utterance heeft mogelijk geen zicht bare invloed op de score.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [plannen van uw app](luis-how-plan-your-app.md) in uw Luis-app.