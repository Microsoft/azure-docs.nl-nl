---
title: Machine learning-functies met LUIS
description: Voeg functies toe aan een taal model om tips te bieden over het herkennen van de invoer die u wilt labelen of classificeren.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: da85abdff3d1022659f2d4e83fd14c5ae6003fc9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100546054"
---
# <a name="machine-learning-features"></a>Machine learning-functies

In machine learning is een *functie*   een onderscheiding van eigenschappen of kenmerken van gegevens die uw systeem observeert en doorloopt.

Machine learning-functies geven LUIS belang rijke aanwijzingen voor het zoeken naar dingen die een concept onderscheiden. Ze zijn hints die LUIS kunnen gebruiken, maar ze zijn geen vaste regels. LUIS maakt gebruik van deze hints in combi natie met de labels om de gegevens te vinden.

Een functie kan worden beschreven als een functie, zoals f (x) = y. In het voor beeld utterance geeft de functie aan waar u moet zoeken naar de onderscheidende kenmerken. Gebruik deze informatie om uw schema te maken.

## <a name="types-of-features"></a>Typen functies

Functies zijn een vereist onderdeel van het schema ontwerp. LUIS ondersteunt beide woordgroepen lijsten en modellen als functies:

* Functie woordgroepen lijst
* Model (intentie of entiteit) als onderdeel

## <a name="find-features-in-your-example-utterances"></a>Zoek functies in uw voor beeld-uitingen

Omdat LUIS een op talen gebaseerde toepassing is, zijn de functies op basis van tekst. Kies tekst die aangeeft welke eigenschappen u wilt onderscheiden. Voor LUIS is de kleinste eenheid het *token*. Voor de Engelse taal is een token een aaneengesloten reeks letters en cijfers zonder spaties of lees tekens.

Omdat spaties en lees tekens geen tokens zijn, richt u zich op de tekst aanwijzingen die u als functies kunt gebruiken. Vergeet niet om ook variaties van woorden op te slaan, zoals:

* meervouds formulieren
* werkwoordsvormen
* afkortingen
* spelling en spel fouten

Bepaal of de tekst, omdat deze een eigenschappen onderscheidt, het volgende moet:

* Overeenkomen met een exact woord of een woord groep: overweeg een reguliere expressie-entiteit of een lijst entiteit toe te voegen als een functie voor de entiteit of het doel.
* Overeenkomen met een goed bekend concept zoals datums, tijden of namen van personen: gebruik een vooraf samengestelde entiteit als een functie voor de entiteit of het doel.
* Meer informatie over nieuwe voor beelden over een bepaalde periode: gebruik een woordgroepen lijst met enkele voor beelden van het concept als een functie voor de entiteit of het doel.

## <a name="create-a-phrase-list-for-a-concept"></a>Een woordgroepen lijst maken voor een concept

Een woordgroepen lijst is een lijst met woorden of zinsdelen waarmee een concept wordt beschreven. Een woordgroepen lijst wordt toegepast als een hoofdletter gevoelige overeenkomst op token niveau.

Wanneer u een woordgroepen lijst toevoegt, kunt u de functie als **[globaal](#global-features)** instellen. Een globale functie is van toepassing op de hele app.

### <a name="when-to-use-a-phrase-list"></a>Wanneer u een woordgroepen lijst gebruikt

Gebruik een woordgroepen lijst wanneer u uw LUIS-app nodig hebt om nieuwe items voor het concept te generaliseren en te identificeren. Woordgroepen lijsten zijn vergelijkbaar met een serverspecifieke woorden lijst. Ze verbeteren de kwaliteit van de inzichten voor intenties en entiteiten.

### <a name="how-to-use-a-phrase-list"></a>Een woordgroepen lijst gebruiken

Met een woordgroepen lijst, beschouwt LUIS context en generaliseert om items te identificeren die vergelijkbaar zijn met, maar die niet exact overeenkomen met de tekst. Volg deze stappen om een woordgroepen lijst te gebruiken:

1. Beginnen met een machine learning-entiteit:
    1. Een voor beeld van een uitingen toevoegen.
    1. Label met een machine learning-entiteit.
1. Een woordgroepen lijst toevoegen:
    1. Voeg woorden met een vergelijk bare betekenis toe. Voeg niet alle mogelijke woorden of woord groepen toe. Voeg in plaats daarvan enkele woorden of zinsdelen per keer toe. Vervolgens opnieuw trainen en publiceren.
    1. Voorgestelde woorden controleren en toevoegen.

### <a name="a-typical-scenario-for-a-phrase-list"></a>Een typisch scenario voor een woordgroepen lijst

Een typisch scenario voor een woordgroepen lijst is het verhogen van woorden die betrekking hebben op een specifiek idee.

Medische voor waarden zijn een goed voor beeld van woorden die mogelijk een woordgroepen lijst nodig hebben om hun betekenis te verhogen. Deze voor waarden kunnen specifieke fysische, chemische, therapeutische of abstracte betekenissen hebben. LUIS weet niet welke voor waarden belang rijk zijn voor uw onderwerps domein zonder een woordgroepen lijst.

De medische voor waarden extra heren:

1. Maak een voor beeld van een uitingen en een label met medische voor waarden in deze uitingen.
2. Maak een woordgroepen lijst met voor beelden van de voor waarden binnen het onderwerps domein. Deze woordgroepen lijst moet de werkelijke term bevatten die u hebt gelabeld en andere termen die hetzelfde concept beschrijven.
3. Voeg de woordgroepen lijst toe aan de entiteit of subentiteit die het concept extraheert dat in de woordgroepen lijst wordt gebruikt. Het meest voorkomende scenario is een onderdeel (onderliggend) van een machine learning-entiteit. Als de lijst met zinsdelen moet worden toegepast op alle intenties of entiteiten, markeert u de woordgroepen lijst als een lijst met globale zinsdelen. Met de vlag **enabledForAllModels** wordt dit model bereik in de API bepaald.

### <a name="token-matches-for-a-phrase-list"></a>Overeenkomende tokens voor een woordgroepen lijst

Een woordgroepen lijst is altijd van toepassing op token niveau. In de volgende tabel ziet u hoe een woordgroepen lijst met het woord **Anne** van toepassing is op variaties van dezelfde tekens in die volg orde.


| Token variatie van **Anne** | De woordgroepen lijst komt overeen wanneer het token wordt gevonden |
|--------------------------|---------------------------------------|
| **Anne**<br>**Anne**<br>           | Ja, token is **Anne**                  |
| **Anne**                    | Ja, token is **Anne**                  |
| **Anne**                     | Geen-token is **Anne**                  |

<a name="how-to-use-phrase-lists"></a>
<a name="how-to-use-a-phrase-lists"></a>
<a name="phrase-lists-help-identify-simple-exchangeable-entities"></a>

## <a name="a-model-as-a-feature-helps-another-model"></a>Een model als functie helpt een ander model

U kunt een model (intentie of entiteit) toevoegen als onderdeel van een ander model (intentie of entiteit). Door een bestaande intentie of entiteit als functie toe te voegen, voegt u een duidelijk gedefinieerd concept toe met een label voor beelden.

Wanneer u een model toevoegt als onderdeel, kunt u de functie als volgt instellen:
* **[Vereist](#required-features)**. Er moet een vereiste functie worden gevonden om het model te retour neren van het Voorspellings eindpunt.
* **[Global](#global-features)**. Een globale functie is van toepassing op de hele app.

### <a name="when-to-use-an-entity-as-a-feature-to-an-intent"></a>Wanneer een entiteit moet worden gebruikt als een functie voor een intentie

Voeg een entiteit als een functie toe aan een intentie wanneer de detectie van die entiteit significant is voor het doel.

Als de bedoeling bijvoorbeeld is voor het reserveren van een vlucht, zoals **BookFlight**, en de entiteit ticket gegevens bevat (zoals het aantal seats, Origin en Destination), moet de entiteit ticket-Information aanzienlijk gewicht toevoegen aan de voor spelling van de **BookFlight** intentie.

### <a name="when-to-use-an-entity-as-a-feature-to-another-entity"></a>Wanneer een entiteit moet worden gebruikt als een functie voor een andere entiteit

Een entiteit (A) moet worden toegevoegd als een functie aan een andere entiteit (B) wanneer de detectie van die entiteit (A) belang rijk is voor de voor spelling van een entiteit (B).

Als bijvoorbeeld een entiteit van het verzend adres zich in een straat subentiteit bevindt, wordt door de subentiteit straat, een aanzienlijk gewicht toegevoegd aan de voor spelling voor de entiteit van het verzend adres.

* Verzend adres (machine learning-entiteit):

    * Straat nummer (subentiteit)
    * Adres (subentiteit)
    * Plaats (subentiteit)
    * Staat of provincie (subentiteit)
    * Land/regio (subentiteit)
    * Post code (subentiteit)

## <a name="nested-subentities-with-features"></a>Geneste subentiteiten met functies

Een machine learning-subentiteit geeft aan dat er een concept voor de bovenliggende entiteit aanwezig is. Het bovenliggende element kan een andere subentiteit of de bovenste entiteit zijn. De waarde van de subentiteit fungeert als een functie voor het bovenliggende item.

Een subentiteit kan zowel een woordgroepen lijst als een model (een andere entiteit) als een functie hebben.

Wanneer de subentiteit een woordgroepen lijst heeft, wordt de vocabulaire van het concept verhoogd, maar wordt er geen informatie toegevoegd aan het JSON-antwoord van de voor spelling.

Wanneer de subentiteit een functie van een andere entiteit heeft, bevat het JSON-antwoord de geëxtraheerde gegevens van die andere entiteit.


## <a name="required-features"></a>Vereiste onderdelen

Er moet een vereiste functie worden gevonden om het model te retour neren van het Voorspellings eindpunt. Gebruik een vereiste functie wanneer u weet dat uw inkomende gegevens moeten overeenkomen met de functie.

Als de tekst van de utterance niet overeenkomt met de vereiste functie, wordt deze niet geëxtraheerd.

Een vereiste functie maakt gebruik van een niet-machine learning-entiteit:

* Reguliere-expressie-entiteit
* Lijstentiteit
* Vooraf gebouwde entiteit

Als u zeker weet dat uw model wordt gevonden in de gegevens, stelt u de functie in zoals vereist. Een vereiste functie retourneert niets als deze niet wordt gevonden.

U kunt door gaan met het voor beeld van het verzend adres:

Verzend adres (geleerde computer entiteit)

 * Straat nummer (subentiteit)
 * Adres (subentiteit)
 * Straat naam (subentiteit)
 * Plaats (subentiteit)
 * Staat of provincie (subentiteit)
 * Land/regio (subentiteit)
 * Post code (subentiteit)

### <a name="required-feature-using-prebuilt-entities"></a>Vereiste functie voor het gebruik van vooraf gemaakte entiteiten

Vooraf gemaakte entiteiten zoals plaats, provincie en land/regio zijn doorgaans een gesloten set lijsten, wat betekent dat ze niet veel in de loop van de tijd veranderen. Deze entiteiten kunnen de relevante aanbevolen functies hebben en deze functies kunnen worden gemarkeerd als vereist. De `isRequired` markering is echter alleen gerelateerd aan de entiteit waaraan deze is toegewezen en heeft geen invloed op de hiërarchie. Als de functie voor de vooraf samengestelde subentiteit niet wordt gevonden, is dit niet van invloed op de detectie en het retour neren van de bovenliggende entiteit.

Als voor beeld van een vereiste functie kunt u overwegen om adressen te detecteren. U kunt overwegen om een straat een vereiste te maken. Hiermee kan een gebruiker "1 micro soft Way" of "One micro soft Way" invoeren, en beide worden omgezet naar het cijfer "1" voor de subentiteit straat nummer. Zie het artikel over de [vooraf gedefinieerde entiteit ](luis-reference-prebuilt-entities.md) voor meer informatie.

### <a name="required-feature-using-list-entities"></a>Vereiste functie voor het gebruik van lijst entiteiten

Een [lijst entiteit](reference-entity-list.md) wordt gebruikt als een lijst met canonieke namen en de bijbehorende synoniemen. Als een vereiste functie als de utterance geen canonieke naam of synoniem bevat, wordt de entiteit niet geretourneerd als onderdeel van het Voorspellings eindpunt.

Stel dat uw bedrijf alleen naar een beperkt aantal landen/regio's wordt verzonden. U kunt een lijst entiteit maken met verschillende manieren waarop uw klant kan verwijzen naar het land/de regio. Als LUIS geen exacte overeenkomst in de tekst van de utterance vindt, wordt de entiteit (die de vereiste functie van de lijst entiteit bevat) niet geretourneerd in de voor spelling.

|Canonieke naam|Synoniemen|
|--|--|
|Verenigde Staten|U.S.<br>U. S. A<br>VS<br>VS<br>0|

Een client toepassing, zoals een chat-bot, kan een opvolgings vraag stellen om u te helpen. Zo kan de klant begrijpen dat de selectie van het land of de regio beperkt is en *vereist* is.

### <a name="required-feature-using-regular-expression-entities"></a>Vereiste functie voor het gebruik van reguliere expressie-entiteiten

Een [reguliere expressie-entiteit](reference-entity-regular-expression.md) die wordt gebruikt als een vereiste functie biedt uitgebreide tekst-overeenkomende mogelijkheden.

In het voor beeld van het verzend adres kunt u een reguliere expressie maken waarmee de syntaxis regels van de post codes van land/regio worden vastgelegd.

## <a name="global-features"></a>Algemene functies

Hoewel het meest wordt gebruikt om een functie toe te passen op een specifiek model, kunt u de functie configureren als een **globale functie** om deze toe te passen op uw hele toepassing.

Het meest voorkomende gebruik voor een globale functie is het toevoegen van een extra vocabulaire aan de app. Als uw klanten bijvoorbeeld een primaire taal gebruiken, maar wel een andere taal in dezelfde utterance kunnen gebruiken, kunt u een functie toevoegen die woorden uit de secundaire taal bevat.

Omdat de gebruiker de secundaire taal verwacht te gebruiken voor alle intentie of entiteit, voegt u woorden van de secundaire taal toe aan de lijst met zinsdelen. Configureer de woordgroepen lijst als een globale functie.

## <a name="combine-features-for-added-benefit"></a>Functies voor extra voor deel combi neren

U kunt meer dan één functie gebruiken om een trait of concept te beschrijven. Een algemene koppeling is het gebruik van:

* Een woordgroepen lijst functie: u kunt meerdere woordgroepen lijsten gebruiken als functies voor hetzelfde model.
* Een model als een functie: [prebuilde entiteit](luis-reference-prebuilt-entities.md), [reguliere expressie-entiteit](reference-entity-regular-expression.md), [lijst entiteit](reference-entity-list.md). 

### <a name="example-ticket-booking-entity-features-for-a-travel-app"></a>Voor beeld: entiteits functies voor ticket-reserve ring voor een reis-app  

Een voor beeld hiervan is een app voor het reserveren van een vlucht met een _vlucht reservering en_ een ticket-boekings _entiteit_. De ticket-boekings entiteit legt de informatie vast om een vliegtuig ticket in een reserverings systeem te boeken. 

De machine learning-entiteit voor ticket-Book heeft twee subeenheden voor het vastleggen van de oorsprong en bestemming. De functies moeten worden toegevoegd aan elke subentiteit, niet op de entiteit op het hoogste niveau.

:::image type="content" source="media/luis-concept-features/ticket-booking-entity.png" alt-text="Ticketbooking-entiteits schema":::

De ticket-boekings entiteit is een machine learning-entiteit met subentiteiten, waaronder _oorsprong_ en _bestemming_. Deze subentiteiten geven beide een geografische locatie aan. Voor het extra heren van de locaties en het onderscheiden van de _oorsprong_ en _bestemming_ moet elke subentiteit functies bevatten.

|Type|Subentiteit van oorsprong |Doel-subentiteit|
|--|--|--|
|Model als onderdeel|[geographyV2](luis-reference-prebuilt-geographyv2.md?tabs=V3) vooraf gebouwde entiteit|[geographyV2](luis-reference-prebuilt-geographyv2.md?tabs=V3) vooraf gebouwde entiteit|
|Woordgroepen lijst|**Oorspronkelijke woorden**: `start at` , `begin from` , `leave`|**Doel woorden**: `to` , `arrive` , `land at` , `go` , `going` , `stay` , `heading`|
|Woordgroepen lijst|Luchthaven codes-dezelfde lijst voor zowel de oorsprong als de bestemming|Luchthaven codes-dezelfde lijst voor zowel de oorsprong als de bestemming|
|Woordgroepen lijst|Namen van lucht havens-zelfde lijst voor zowel de oorsprong als de bestemming|Luchthaven codes-dezelfde lijst voor zowel de oorsprong als de bestemming|

Als u verwacht dat mensen luchthaven codes en namen van lucht havens gebruiken, dan moet LUIS woorden lijsten bevatten waarvoor beide typen zinsdelen worden gebruikt. Luchthaven codes kunnen vaker worden gebruikt met tekst die in een chatbot wordt ingevoerd, terwijl namen van lucht havens mogelijk vaker worden gebruikt in gesp roken conversatie, zoals een chatbot met spraak functionaliteit.

De overeenkomende details van de functies worden alleen geretourneerd voor modellen, niet voor woordgroepen lijsten, omdat alleen modellen worden geretourneerd in JSON voor voor spellingen.

#### <a name="ticket-booking-labeling-in-the-intent"></a>Ticket-reserve ring labelen in het doel

Nadat u de machine learning-entiteit hebt gemaakt, moet u voorbeeld uitingen toevoegen aan een intentie en de bovenliggende entiteit en alle subentiteiten labelen.

Voor het ticket reserverings voorbeeld labelt u het voor beeld uitingen in de intentie met de `TicketBooking` entiteit en eventuele subentiteiten in de tekst.

:::image type="content" source="media/luis-concept-features/intent-example-utterances-machine-learning-entity.png" alt-text="Voorbeelden van uitingen labelen":::

### <a name="example-pizza-ordering-app"></a>Voor beeld: Pizza best Ellen

Voor een tweede voor beeld moet u een app voor een pizza-restaurant ontvangen. deze ontvangt pizza-orders, met inbegrip van de details van het type pizza dat door iemand wordt best Ellen. Alle details van de pizza moeten, indien mogelijk, worden geëxtraheerd om de order verwerking te volt ooien.

De machine learning-entiteit in dit voor beeld is complexer met geneste subentiteiten, woordgroepen lijsten, vooraf gemaakte entiteiten en aangepaste entiteiten.

:::image type="content" source="media/luis-concept-features/pizza-order-entity.png" alt-text="Entiteits schema voor pizza order":::

In dit voor beeld worden functies op het niveau van de subentiteit en het onderliggende niveau van de subentiteit gebruikt. Welk niveau wordt opgehaald voor het soort woordgroepen lijst of model als onderdeel, is een belang rijk onderdeel van het ontwerp van de entiteit.

Hoewel subentiteiten een groot aantal woordgroepen lijsten kunnen hebben als functies waarmee de entiteit kan worden gedetecteerd, heeft elke subeenheid slechts één model als een functie. In deze [pizza-app](https://github.com/Azure/pizza_luis_bot/blob/master/CognitiveModels/MicrosoftPizza.json)zijn deze modellen voornamelijk lijsten.

:::image type="content" source="media/luis-concept-features/intent-example-utterances-machine-learning-entity-pizza.png" alt-text="Pizza order intentie met gelabeld voor beeld uitingen":::

Het correct gelabelde voorbeeld uitingen wordt weer gegeven op een manier om te laten zien hoe de entiteiten zijn genest. 


## <a name="best-practices"></a>Aanbevolen procedures

Lees de [Aanbevolen procedures](luis-concept-best-practices.md).

## <a name="next-steps"></a>Volgende stappen

* [Breid](schema-change-prediction-runtime.md) uw app-modellen uit tijdens de Voorspellings runtime.
* Zie [onderdelen toevoegen](luis-how-to-add-features.md) voor meer informatie over het toevoegen van functies aan uw Luis-app.
