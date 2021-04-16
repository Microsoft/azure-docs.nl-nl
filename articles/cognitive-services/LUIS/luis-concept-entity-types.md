---
title: Entiteitstypen - LUIS
description: Een entiteit extraheert gegevens uit een uiting van een gebruiker tijdens de voorspellingsruntime. Een _optioneel_, secundair doel is om de voorspelling van de intentie of andere entiteiten te verbeteren door de entiteit als een functie te gebruiken.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/13/2021
ms.openlocfilehash: 44cffecd653ec2ec748e73d01dc86a87cfcd7de9
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107500325"
---
# <a name="entities-in-luis"></a>Entiteiten in LUIS

Een entiteit is een item of een element dat relevant is voor de intentie van de gebruiker. Entiteiten definiëren gegevens die kunnen worden geëxtraheerd uit de utterance en zijn essentieel voor het voltooien van de vereiste actie van een gebruiker. Bijvoorbeeld:

|Uiting|Voorspelde intentie|Geëxtraheerde entiteiten|Uitleg|
|--|--|--|--|
|Hallo hoe gaat het?|Begroeting|-|Er is niets om uit te extraheren.|
|Ik wil een kleine pizza bestellen|orderPizza| 'klein' | De entiteit 'Grootte' wordt geëxtraheerd als 'klein'.|
|Het slaaplicht uitschakelen|Afslag| 'slaapkamers' | De entiteit 'Ruimte' wordt geëxtraheerd als 'slaapkamers'.|
|Saldo controleren op mijn spaarrekening die eindigt op 4406|checkBalance| "besparingen", "4406" | De entiteit accountType wordt geëxtraheerd als 'savings' en de entiteit accountNumber wordt geëxtraheerd als '4406'.|
|3 tickets naar New York kopen|buyTickets| "3", "New York" | De entiteit ticketsCount wordt geëxtraheerd als '3' en de entiteit 'Destination' wordt geëxtraheerd als 'New York'.|

Entiteiten zijn optioneel, maar worden aanbevolen. U hoeft geen entiteiten te maken voor elk concept in uw app, alleen voor de volgende dingen:

* De clienttoepassing heeft de gegevens nodig, of 
* De entiteit fungeert als hint of signaal naar een andere entiteit of intentie. Ga naar Entiteiten als functies voor meer informatie [over entiteiten als Functies.](#entities-as-features)

## <a name="entity-types"></a>Entiteitstypen

Als u een entiteit wilt maken, moet u deze een naam en een type geven. Er zijn verschillende typen entiteiten in LUIS. 

### <a name="list-entity"></a>Entiteit list

Een entiteit List vertegenwoordigt een vaste, gesloten set gerelateerde woorden samen met hun synoniemen. U kunt lijstentiteiten gebruiken om meerdere synoniemen of variaties te herkennen en een genormaliseerde uitvoer voor deze entiteiten te extraheren. Gebruik de *aanbevelingsoptie* om suggesties te zien voor nieuwe woorden op basis van de huidige lijst. 

Een entiteit list is niet machine-learned, wat betekent dat LUIS geen aanvullende waarden voor lijstentiteiten detecteert. LUIS markeert een overeenkomst met een item in een lijst als een entiteit in het antwoord.

Overeenkomst in lijstentiteiten is zowel casegevoelig als moet een exacte overeenkomst zijn om te worden geëxtraheerd. Genormaliseerde waarden worden ook gebruikt bij het overeenkomen met de lijstentiteit. Bijvoorbeeld:

|Genormaliseerde waarde|Synoniemen|
|--|--|
|Klein|sm, sml, zeer klein, kleinste|
|Middelgroot|md, mdm, normaal, gemiddeld, midden|
|Groot|lg, lrg, groot|

Zie het [naslagartikel over entiteiten in de lijst](reference-entity-list.md) voor meer informatie.

### <a name="regex-entity"></a>Regex-entiteit

Een entiteit in de reguliere expressie extraheert een entiteit op basis van een patroon voor reguliere expressies dat u op geeft. Het negeert het geval en negeert culturele variant. Reguliere expressie is het beste voor gestructureerde tekst of een vooraf gedefinieerde reeks alfanumerieke waarden die in een bepaalde indeling worden verwacht. Bijvoorbeeld:

|Entiteit|Reguliere expressie|Voorbeeld|
|--|--|--|
|Vluchtnummer|flight [A-Z] {2} [0-9]{4}| flight AS 1234|
|Creditcardnummer|[0-9]{16}|5478789865437632|

Zie het [naslagartikel over regex-entiteiten](reference-entity-regular-expression.md) voor meer informatie.

### <a name="prebuilt-entity"></a>Vooraf gemaakte entiteit

LUIS biedt een set vooraf gebouwde entiteiten voor het herkennen van algemene typen gegevens, zoals naam, datum, nummer en valuta.  Het gedrag van vooraf gebouwde entiteiten is vast. De ondersteuning voor vooraf gebouwde entiteiten is afhankelijk van de cultuur van de LUIS-app. Bijvoorbeeld:

|Vooraf gebouwde entiteit|Voorbeeldwaarde|
|--|--|
|PersonName|James, Bill, Tom|
|DatetimeV2|02-05-2019 2 mei 2019 om 8:00 uur op 2 mei 2019|

Zie het [naslagartikel over vooraf gebouwde entiteiten](./luis-reference-prebuilt-entities.md) voor meer informatie.

### <a name="patternany-entity"></a>Pattern.Any-entiteit

Een patroon. Elke entiteit is een tijdelijke aanduiding met variabele lengte die alleen wordt gebruikt in de sjabloonuiting van een patroon om te markeren waar de entiteit begint en eindigt. Het volgt een specifieke regel of een specifiek patroon en wordt het beste gebruikt voor zinnen met een vaste lexicale structuur. Bijvoorbeeld:

|Voorbeeld van een utterance|Patroon|Entiteit|
|--|--|--|
|Kan ik een burger a.u.u.a. hebben?|Kan ik een {100} [please][?] hebben| burger
|Kan ik een pizza hebben?|Kan ik een {100} [please][?] hebben| Pizza
|Waar vind ik The Great Gatsby?|Waar vind ik {bookName}?| The Great Gatsby|

Zie het [referentieartikel over Pattern.Any-entiteiten](./reference-entity-pattern-any.md) voor meer informatie.

### <a name="machine-learned-ml-entity"></a>Machine learned -entiteit (ML)

De entiteit Machine Learned maakt gebruik van context om entiteiten te extraheren op basis van gelabelde voorbeelden. Het is de voorkeursentiteit voor het bouwen van LUIS-toepassingen. Het is afhankelijk van machine learning algoritmen en vereist dat labels met succes worden aangepast aan uw toepassing. Gebruik een ML-entiteit om gegevens te identificeren die niet altijd goed zijn opgemaakt, maar die dezelfde betekenis hebben. 

|Voorbeeld van een utterance|*Geëxtraheerde productentiteit*|
|--|--|
|Ik wil een boek kopen.|"book"|
|Kan ik deze schoenen kopen?|"schoenen"|
|Voeg deze shorts toe aan mijn winkelwagen.|"shorts"|

Meer informatie over machine learned-entiteiten vindt u [hier.](./reference-entity-machine-learned-entity.md)

Zie het [naslagartikel over door machine geleerde entiteiten](./reference-entity-pattern-any.md) voor meer informatie.

#### <a name="ml-entity-with-structure"></a>ML-entiteit met structuur

Een ML-entiteit kan bestaan uit kleinere subentiteiten, die elk hun eigen eigenschappen kunnen hebben. Adres kan *bijvoorbeeld* de volgende structuur hebben:

* Adres: 4567 Main Street, NY, 98052, USA
    * Gebouwnummer: 4567
    * Straatnaam: Hoofdweg
    * Staat: NY
    * Postcode: 98052
    * Land: Verenigde Staten


### <a name="building-effective-ml-entities"></a>Effectieve ML-entiteiten bouwen

Volg deze best practices om effectief door machine geleerde entiteiten te bouwen:

* Als u een machine learned-entiteit met subentiteiten hebt, moet u ervoor zorgen dat de verschillende orders en varianten van de entiteit en subentiteiten worden weergegeven in de gelabelde utterances. Gelabelde voorbeelduitingen moeten alle geldige formulieren bevatten en entiteiten bevatten die worden weergegeven en die niet aanwezig zijn en die ook opnieuw worden rangschikken binnen de utterance.

* Vermijd overfitting van de entiteiten naar een zeer vaste set. Overfitting vindt plaats wanneer het model niet goed generaliseert en een veelvoorkomende probleem is in machine learning modellen. Dit impliceert dat de app niet op de juiste manier zou werken met nieuwe typen voorbeelden. Op zijn beurt moet u de gelabelde voorbeeld-utterances variëren, zodat de app verder kan generaliseren dan de beperkte voorbeelden die u op biedt.

* Uw labels moeten consistent zijn voor alle intenties. Dit omvat zelfs utterances die u op geeft in de *intentie None* die deze entiteit bevat. Anders kan het model de reeksen niet effectief bepalen.

## <a name="entities-as-features"></a>Entiteiten als functies

Een andere belangrijke functie van entiteiten is om ze te gebruiken als kenmerken of eigenschappen voor andere intenties of entiteiten te onderscheiden, zodat uw systeem deze geobserveert en leert.

### <a name="entities-as-features-for-intents"></a>Entiteiten als functies voor intenties

U kunt entiteiten gebruiken als signaal voor een intentie. De aanwezigheid van een bepaalde entiteit in de utterance kan bijvoorbeeld onderscheiden van welke intentie deze valt.

|Voorbeeld van een utterance|Entiteit|Intentie|
|--|--|--|
|Boek me een *strijd met New York.*|Plaats|Vlucht boeken|
|Boek de belangrijkste *vergaderruimte voor mij.*|Room|Ruimte reserveren|

### <a name="entities-as-feature-for-entities"></a>Entiteiten als functie voor entiteiten

U kunt ook entiteiten gebruiken als indicator van de aanwezigheid van andere entiteiten. Een veelvoorkomende voorbeeld hiervan is het gebruik van een vooraf gebouwde entiteit als een functie voor een andere ML-entiteit.
Als u een vluchtboekingssysteem bouwt en uw uiting lijkt op 'Book me a flight from Seattle', dan hebt u *Origin City* en *Destination City* als ML-entiteiten. Het is een goed idee om de vooraf gebouwde entiteit als `GeographyV2` een functie voor beide entiteiten te gebruiken.

Zie het [referentieartikel GeographyV2-entiteiten](./luis-reference-prebuilt-geographyv2.md) voor meer informatie.

U kunt entiteiten ook gebruiken als vereiste functies voor andere entiteiten. Dit helpt bij het oplossen van geëxtraheerde entiteiten. Als u bijvoorbeeld een toepassing voor het bestellen van pizza's maakt en u een ML-entiteit hebt, kunt u een entiteit list maken en deze gebruiken als een vereiste functie `Size` `SizeList` voor de `Size` entiteit. Uw toepassing retourneert de genormaliseerde waarde als de geëxtraheerde entiteit uit de utterance. 

Zie [functies](luis-concept-feature.md) voor meer informatie en vooraf gebouwde [entiteiten](./luis-reference-prebuilt-entities.md) voor meer informatie over de oplossing van vooraf gebouwde entiteiten die beschikbaar zijn in uw cultuur. 


## <a name="entity-prediction-status-and-errors"></a>Voorspellingsstatus en -fouten voor entiteiten

In de LUIS-portal ziet u het volgende wanneer de entiteit een andere entiteitsvoorspelling heeft dan de entiteit die u hebt gelabeld voor een voorbeelduiting. Deze andere score is gebaseerd op het huidige getrainde model. 

:::image type="content" source="./media/luis-concept-entities/portal-entity-prediction-error.png" alt-text="In de LUIS-portal ziet u wanneer de entiteit een andere entiteitsvoorspelling heeft dan de entiteit die u hebt geselecteerd voor een voorbeelduiting":::

De tekst die de fout veroorzaakt, wordt gemarkeerd in de voorbeelduiting en de voorbeelduitingsregel heeft een foutindicator aan de rechterkant, weergegeven als een rode driehoek. 

Probeer een of meer van de volgende opties om entiteitsfouten op te lossen:

* De gemarkeerde tekst is verkeerd gelabeld. Als u dit wilt oplossen, controleert u het label, corrigeert u het en gaat u de app opnieuw trainen. 
* Maak een [functie](luis-concept-feature.md) voor de entiteit om het concept van de entiteit te identificeren.
* Voeg meer [voorbeeld-utterances toe](luis-concept-utterance.md) en label met de entiteit .
* [Bekijk actieve leersuggesties](luis-concept-review-endpoint-utterances.md) voor alle uitingen die worden ontvangen op het voorspellings-eindpunt, zodat u het concept van de entiteit kunt identificeren.


## <a name="next-steps"></a>Volgende stappen

* Meer informatie over goede [voorbeeld-utterances.](luis-concept-utterance.md)
* Zie [Entiteiten toevoegen voor](luis-how-to-add-entities.md) meer informatie over het toevoegen van entiteiten aan uw LUIS-app.
* Meer informatie over [LUIS-toepassingslimieten.](./luis-limits.md) 
* Gebruik een [zelfstudie](tutorial-machine-learned-entity.md) om te leren hoe u gestructureerde gegevens kunt extraheren uit een utterance met behulp van de machine learning-entiteit.
