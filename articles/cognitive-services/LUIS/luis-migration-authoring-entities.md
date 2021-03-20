---
title: Migreren naar v3 machine-learning-entiteit
description: De V3-ontwerp functie biedt een nieuw entiteits type, de machine learning-entiteit, en de mogelijkheid om relaties toe te voegen aan de entiteit machine learning en andere entiteiten of functies van de toepassing.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 05/08/2020
ms.openlocfilehash: 667226770d25ef1687420b1c13bc71863f987e33
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91324685"
---
# <a name="migrate-to-v3-authoring-entity"></a>Migreren naar v3-ontwerp entiteit

De V3-ontwerp functie biedt een nieuw entiteits type, de machine learning-entiteit, en de mogelijkheid om relaties toe te voegen aan de entiteit machine learning en andere entiteiten of functies van de toepassing.

## <a name="entities-are-decomposable-in-v3"></a>Entiteiten kunnen worden ontstelbaar in v3

Entiteiten die zijn gemaakt met de V3-ontwerp-Api's, hetzij met behulp van de [api's](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview) of met de portal, bieden u de mogelijkheid om een gelaagd entiteits model te bouwen met een bovenliggend item en onderliggende items. Het bovenliggende item is bekend als de **machine learning-entiteit** en de onderliggende entiteiten worden aangeduid als **subentiteiten** van de door de machine geleerde entiteit.

Elke subentiteit is ook een machine learning-entiteit, maar met de toegevoegde configuratie opties van functies.

* De **vereiste onderdelen** zijn regels die garanderen dat een entiteit wordt geëxtraheerd wanneer deze overeenkomt met een functie. De regel wordt gedefinieerd door de vereiste functie voor het model:
    * [Vooraf gebouwde entiteit](luis-reference-prebuilt-entities.md)
    * [Een entiteit in de vorm van een reguliere expressie](reference-entity-regular-expression.md)
    * [Entiteit weer geven](reference-entity-list.md).

## <a name="how-do-these-new-relationships-compare-to-v2-authoring"></a>Hoe kunnen deze nieuwe relaties worden vergeleken met v2-ontwerpen

V2-ontwerping heeft hiërarchische en samengestelde entiteiten, samen met rollen en functies, voorzien om deze zelfde taak uit te voeren. Omdat de entiteiten, functies en rollen niet expliciet zijn gerelateerd aan elkaar, is het moeilijk om te begrijpen hoe LUIS de relaties tijdens de voor spelling heeft geïmpliceerd.

Met v3 is de relatie expliciet en ontworpen door de auteur van de app. Zo kunt u, als auteur van de app,:

* Bekijk hoe LUIS deze relaties voordicteert, in het voor beeld uitingen
* Testen op deze relaties met het [interactieve test venster](luis-interactive-test.md) of op het eind punt
* Gebruik deze relaties in de client toepassing via een goed gestructureerd, benoemd, genest [. json-object](reference-entity-machine-learned-entity.md)

## <a name="planning"></a>Planning

Wanneer u migreert, moet u rekening houden met het volgende in uw migratie plan:

* Maak een back-up van uw LUIS-app en voer de migratie uit op een afzonderlijke app. Wanneer u een v2-en V3-app tegelijkertijd beschikbaar hebt, kunt u de vereiste wijzigingen en de invloed op de Voorspellings resultaten valideren.
* Huidige metrische voor spelling slagen vastleggen
* Huidige dashboard gegevens vastleggen als moment opname van de app-status
* Bestaande intenties, entiteiten, woordgroepen lijsten, patronen en batch tests controleren
* De volgende elementen kunnen **zonder wijziging** worden gemigreerd:
    * Intents
    * Entiteiten
        * Een entiteit in de vorm van een reguliere expressie
        * Lijstentiteit
    * Functies
        * Woordgroepen lijst
* De volgende elementen moeten worden gemigreerd **met wijzigingen**:
    * Entiteiten
        * Hiërarchische entiteit
        * Samengestelde entiteit
    * Rollen: rollen kunnen alleen worden toegepast op een bovenliggende entiteit (machine learning). Rollen kunnen niet worden toegepast op subentiteiten
    * Batch tests en patronen die gebruikmaken van de hiërarchische en samengestelde entiteiten

Wanneer u uw migratie plan ontwerpt, laat u de laatste instanties van de machine learning controleren, nadat alle hiërarchische en samengestelde entiteiten zijn gemigreerd. Terwijl u een rechte migratie kunt uitvoeren, kunt u, nadat u de wijziging hebt aangebracht en de resultaten van de batch test en voor spellingen hebt gecontroleerd, de meer Unified JSON ertoe leiden dat u wijzigingen aanbrengt zodat de laatste gegevens die aan de client zijde worden geleverd, anders zijn ingedeeld. Dit is vergelijkbaar met het herstructureren van code en moet worden behandeld met hetzelfde controle proces als uw organisatie.

Als u geen batch tests hebt voor uw v2-model en u de batch tests naar het v3-model wilt migreren als onderdeel van de migratie, kunt u niet valideren hoe de migratie van invloed is op de Voorspellings resultaten van het eind punt.

## <a name="migrating-from-v2-entities"></a>Migreren van v2-entiteiten

Wanneer u begint met het maken van een v3-ontwerp model, moet u rekening houden met het verplaatsen van de machine learning-entiteit en de bijbehorende subentiteiten en onderdelen.

In de volgende tabel ziet u welke entiteiten moeten worden gemigreerd van een v2 naar een v3-entiteits ontwerp.

|V2-entiteits type voor ontwerpen|Type v3-ontwerp entiteit|Voorbeeld|
|--|--|--|
|Samengestelde entiteit|Door machine geleerde entiteit|[meer informatie](#migrate-v2-composite-entity)|
|Hiërarchische entiteit|rol van machine leer entiteit|[meer informatie](#migrate-v2-hierarchical-entity)|

## <a name="migrate-v2-composite-entity"></a>V2 samengestelde entiteit migreren

Elk onderliggend element van de v2-samengestelde waarde moet worden weer gegeven met een subentiteit van de V3 machine-learning-entiteit. Als het samengestelde onderliggende element een vooraf samengestelde, reguliere expressie of een lijst entiteit is, moet dit worden toegepast als een vereiste functie voor de subentiteit.

Overwegingen bij het plannen van het migreren van een samengestelde entiteit naar een machine learning-entiteit:
* Onderliggende entiteiten kunnen niet worden gebruikt in patronen
* Onderliggende entiteiten worden niet langer gedeeld
* Onderliggende entiteiten moeten worden gelabeld als ze worden gebruikt als niet-machine-geleerd

### <a name="existing-features"></a>Bestaande functies

Een woordgroepen lijst die wordt gebruikt voor het boosten van woorden in de samengestelde entiteit moet worden toegepast als een functie voor de machine learning (bovenliggende entiteit), de entiteit van de subentiteit (kind) of het doel (als de lijst met zinsdelen alleen van toepassing is op één intentie). Plan om de functie toe te voegen aan de entiteit, waar deze het meest ingrijpend moet worden verhoogd. Voeg de functie niet algemeen toe aan de entiteit machine learning (bovenliggend object) als de voor spelling van een subentiteit (onderliggend item) het belangrijkst wordt verhoogd.

### <a name="new-features"></a>Nieuwe functies

Voeg in v3 ontwerpen een plannings stap toe om entiteiten te evalueren als mogelijke functies voor alle entiteiten en intenties.

### <a name="example-entity"></a>Voorbeeld entiteit

Deze entiteit is alleen een voor beeld. De migratie van uw eigen entiteit vereist mogelijk andere overwegingen.

Overweeg een v2-samengestelde versie voor het wijzigen van een pizza `order` die gebruikmaakt van:
* vooraf gebouwde datetimeV2 voor leverings tijd
* woordgroepen lijst om bepaalde woorden, zoals pizza, cirkel, crust en topping, te stimuleren
* lijst entiteit voor het detecteren van toppings zoals paddestoelen, olijven, pepperoni.

Een voor beeld van een utterance voor deze entiteit is:

`Change the toppings on my pie to mushrooms and delivery it 30 minutes later`

In de volgende tabel ziet u de migratie:

|V2-modellen|V3-modellen|
|--|--|
|Entiteit van bovenliggend onderdeel met de naam `Order`|Bovenliggende-machine-learning entiteit met de naam `Order`|
|Kinder datetimeV2|* Vooraf gebouwde entiteit migreren naar een nieuwe app.<br>* Voeg de vereiste functie op Parent toe voor vooraf gebouwde datetimeV2.|
|Onderliggende lijst entiteit voor toppings|* Migreer de lijst entiteit naar een nieuwe app.<br>* Voeg vervolgens een vereiste functie toe aan het bovenliggende item voor de lijst entiteit.|


## <a name="migrate-v2-hierarchical-entity"></a>Hiërarchische v2-entiteit migreren

Bij het ontwerpen van v2 werd een hiërarchische entiteit gemaakt vóór de functies bestaande in LUIS. Beide hebben hetzelfde doel om entiteiten te extra heren op basis van context gebruik. Als u hiërarchische entiteiten hebt, kunt u deze als eenvoudige entiteiten met rollen beschouwen.

In v3 ontwerpen:
* Een rol kan worden toegepast op de entiteit machine learning (bovenliggend object).
* Een rol kan niet worden toegepast op een of meer subentiteiten.

Deze entiteit is alleen een voor beeld. De migratie van uw eigen entiteit vereist mogelijk andere overwegingen.

Overweeg een hiërarchische v2-entiteit voor het wijzigen van een pizza `order` :
* waarbij elk onderliggend element een oorspronkelijke topping of de uiteindelijke topping bepaalt

Een voor beeld van een utterance voor deze entiteit is:

`Change the topping from mushrooms to olives`

In de volgende tabel ziet u de migratie:

|V2-modellen|V3-modellen|
|--|--|
|Entiteit van bovenliggend onderdeel met de naam `Order`|Bovenliggende-machine-learning entiteit met de naam `Order`|
|Onderliggende hiërarchische entiteit met oorspronkelijke en laatste Pizza-Topping|* Voeg een rol toe aan `Order` voor elke topping.|

## <a name="api-change-constraint-replaced-with-required-feature"></a>De API-wijzigings beperking is vervangen door de vereiste functie

Deze wijziging is aangebracht in mei 2020 bij de build-conferentie en is alleen van toepassing op de V3-ontwerp-Api's waarbij een app een beperkte functie gebruikt. Als u migreert van v2 naar v3 ontwerpen of als u geen gebruik hebt gemaakt van v3 beperkte functies, kunt u deze sectie overs Laan.

**Functionaliteit** : de mogelijkheid om een bestaande entiteit te vereisen als onderdeel van een ander model en die model alleen te extra heren als de entiteit wordt gedetecteerd. De functionaliteit is niet gewijzigd, maar de API en de terminologie zijn gewijzigd.

|Vorige terminologie|Nieuwe terminologie|
|--|--|
|`constrained feature`<br>`constraint`<br>`instanceOf`|`required feature`<br>`isRequired`|

#### <a name="automatic-migration"></a>Automatische migratie

Vanaf **19 2020 juni** kunt u geen beperkingen maken met behulp van de vorige ontwerp-API die deze functionaliteit beschikbaar maakt.

Alle bestaande beperkings functies worden automatisch gemigreerd naar de vereiste functie vlag. Er zijn geen programmatische wijzigingen vereist voor uw Voorspellings-API en er is geen gevolg van een wijziging in de kwaliteit van de nauw keurigheid van de voor spelling.

#### <a name="luis-portal-changes"></a>Wijzigingen in de LUIS-Portal

De preview-versie van de LUIS-Portal verwijst naar deze functie als een **beperking**. De huidige LUIS-portal wijst deze functionaliteit aan als een **vereiste functie**.

#### <a name="previous-authoring-api"></a>Vorige ontwerp-API

Deze functionaliteit werd toegepast in de ontwerp functie voor het ontwerpen van **[entiteiten maken onderliggende API](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5d86cf3c6a25a45529767d77)** als onderdeel van de definitie van een entiteit, met behulp `instanceOf` van de eigenschap van de onderliggende entiteit:

```json
{
    "name" : "dayOfWeek",
    "instanceOf": "datetimeV2",
    "children": [
        {
           "name": "dayNumber",
           "instanceOf": "number",
           "children": []
        }
    ]
}
```

#### <a name="new-authoring-api"></a>Nieuwe ontwerp-API

Deze functionaliteit wordt nu toegepast met de **[functie-API voor het toevoegen van entiteits](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5d9dc1781e38aaec1c375f26)** `featureName` `isRequired` Eigenschappen. De waarde van de `featureName` eigenschap is de naam van het model.

```json
{
    "featureName": "YOUR-MODEL-NAME-HERE",
    "isRequired" : true
}
```


## <a name="next-steps"></a>Volgende stappen

* [Bronnen voor ontwikkelaars](developer-reference-resource.md)
