---
title: Wijzigingen in het Voorspellings eindpunt in de V3 API
description: De V3 Api's voor de query Voorspellings-endpoint zijn gewijzigd. Gebruik deze hand leiding voor informatie over het migreren naar versie 3-eindpunt-Api's.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 06/30/2020
ms.openlocfilehash: 59cf250a9db5a1f6759495c1b5a3c48cb07cde15
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95018783"
---
# <a name="prediction-endpoint-changes-for-v3"></a>Wijzigingen in het Voorspellings eindpunt voor v3

De V3 Api's voor de query Voorspellings-endpoint zijn gewijzigd. Gebruik deze hand leiding voor informatie over het migreren naar versie 3-eindpunt-Api's.

**Algemeen beschik bare status** : deze v3 API bevat belang rijke wijzigingen in de JSON-aanvraag en het antwoord van v2 API.

De V3 API biedt de volgende nieuwe functies:

* [Externe entiteiten](schema-change-prediction-runtime.md#external-entities-passed-in-at-prediction-time)
* [Dynamische lijsten](schema-change-prediction-runtime.md#dynamic-lists-passed-in-at-prediction-time)
* [JSON-wijzigingen van entiteit vooraf gebouwd](#prebuilt-entity-changes)

De Voorspellings eindpunt [aanvraag](#request-changes) en het [antwoord](#response-changes) bevatten belang rijke wijzigingen ter ondersteuning van de nieuwe functies die hierboven worden vermeld, waaronder de volgende:

* [Wijzigingen in het reactie object](#top-level-json-changes)
* [Namen van entiteits rollen in plaats van naam van entiteit](#entity-role-name-instead-of-entity-name)
* [Eigenschappen voor het markeren van entiteiten in uitingen](#marking-placement-of-entities-in-utterances)

[Referentie documentatie](https://aka.ms/luis-api-v3) is beschikbaar voor v3.

## <a name="v3-changes-from-preview-to-ga"></a>V3 wijzigingen van preview to GA

V3 heeft de volgende wijzigingen aangebracht als onderdeel van de move to GA:

* De volgende vooraf gemaakte entiteiten hebben verschillende JSON-Reacties:
    * [OrdinalV1](luis-reference-prebuilt-ordinal.md)
    * [GeographyV2](luis-reference-prebuilt-geographyv2.md)
    * [DatetimeV2](luis-reference-prebuilt-datetimev2.md)
    * De naam van de meet bare eenheids sleutel van `units` naar `unit`

* Wijziging van JSON van aanvraag tekst:
    * van `preferExternalEntities` tot `preferExternalEntities`
    * optionele `score` para meter voor externe entiteiten

* Wijzigingen van de JSON van de antwoord tekst:
    * `normalizedQuery` Voer

## <a name="suggested-adoption-strategy"></a>Voorgestelde acceptatie strategie

Als u gebruik wilt maken van bot Framework, Bing Spellingcontrole V7 of als u alleen uw LUIS app-ontwerp wilt migreren, gaat u verder met het v2-eind punt.

Als u geen van uw client toepassing of-integratie (bot-Framework en Bing Spellingcontrole V7) kent, worden deze beïnvloed en kunt u gemakkelijk uw LUIS-app-ontwerp en het predictation-eind punt gebruiken. Het v2-Voorspellings eindpunt is nog steeds beschikbaar en is een goede back-upstrategie.


## <a name="not-supported"></a>Niet ondersteund

### <a name="bing-spell-check"></a>Bing Spellingcontrole

Deze API wordt niet ondersteund in het v3-Voorspellings eindpunt-door gebruik te gaan van het v2 API-Voorspellings eindpunt voor spelling correcties. Als u een spelling correctie nodig hebt terwijl u v3 API gebruikt, moet de client toepassing de [Bing spellingcontrole](../bing-spell-check/overview.md) -API aanroepen en de tekst wijzigen in de juiste spelling voordat de tekst naar de Luis-API wordt verzonden.

## <a name="bot-framework-and-azure-bot-service-client-applications"></a>Bot Framework en Azure Bot Service-client toepassingen

Ga verder met het gebruik van het v2 API-Voorspellings eindpunt totdat de V 4.7 van het bot-Framework wordt uitgebracht.

## <a name="v2-api-deprecation"></a>V2-API-afschaffing

De v2-Voorspellings-API wordt niet afgeschaft gedurende ten minste negen maanden na de preview-versie van v3 juni 2020.

## <a name="endpoint-url-changes"></a>Eind punt-URL wordt gewijzigd

### <a name="changes-by-slot-name-and-version-name"></a>Wijzigingen per sleuf naam en versie naam

De [indeling van de HTTP-aanroep van het v3-eind punt](developer-reference-resource.md#rest-endpoints) is gewijzigd.

Als u een query wilt uitvoeren op versie, moet u eerst [via API publiceren](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c3b) met `"directVersionPublish":true` . Zoek het eind punt naar de versie-ID in plaats van de naam van de sleuf.

|Geldige waarden voor `SLOT-NAME`|
|--|
|`production`|
|`staging`|

## <a name="request-changes"></a>Wijzigingen aanvragen

### <a name="query-string-changes"></a>Query reeks wijzigingen

[!INCLUDE [V3 query params](./includes/v3-prediction-query-params.md)]

### <a name="v3-post-body"></a>V3 POST-hoofd tekst

```JSON
{
    "query":"your utterance here",
    "options":{
        "datetimeReference": "2019-05-05T12:00:00",
        "preferExternalEntities": true
    },
    "externalEntities":[],
    "dynamicLists":[]
}
```

|Eigenschap|Type|Versie|Standaard|Doel|
|--|--|--|--|--|
|`dynamicLists`|matrix|Alleen v3|Niet vereist.|Met [dynamische lijsten](schema-change-prediction-runtime.md#dynamic-lists-passed-in-at-prediction-time) kunt u een bestaande getrainde en gepubliceerde lijst entiteit uitbreiden, al in de Luis-app.|
|`externalEntities`|matrix|Alleen v3|Niet vereist.|[Externe entiteiten](schema-change-prediction-runtime.md#external-entities-passed-in-at-prediction-time) bieden uw Luis-app de mogelijkheid om entiteiten tijdens runtime te identificeren en te labelen, die kunnen worden gebruikt als functies voor bestaande entiteiten. |
|`options.datetimeReference`|tekenreeks|Alleen v3|Geen standaard waarde|Wordt gebruikt om de [datetimeV2-offset](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity)te bepalen. De indeling voor de datetimeReference is [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601).|
|`options.preferExternalEntities`|booleaans|Alleen v3|onjuist|Hiermee wordt aangegeven of de externe entiteit van de gebruiker [(met dezelfde naam als bestaande entiteit)](schema-change-prediction-runtime.md#override-existing-model-predictions) wordt gebruikt of dat de bestaande entiteit in het model wordt gebruikt voor de voor spelling. |
|`query`|tekenreeks|Alleen v3|Vereist.|**In v2** bevindt de utterance die moet worden voor speld in de `q` para meter. <br><br>**In v3** wordt de functionaliteit door gegeven in de `query` para meter.|

## <a name="response-changes"></a>Reactie wijzigingen

De JSON van het query-antwoord is gewijzigd zodat meer programmatische toegang mogelijk is tot de gegevens die het vaakst worden gebruikt.

### <a name="top-level-json-changes"></a>JSON-wijzigingen op het hoogste niveau



De bovenste JSON-eigenschappen voor v2 zijn, wanneer `verbose` is ingesteld op True, waarmee alle intents en hun scores in de eigenschap worden geretourneerd `intents` :

```JSON
{
    "query":"this is your utterance you want predicted",
    "topScoringIntent":{},
    "intents":[],
    "entities":[],
    "compositeEntities":[]
}
```

De bovenste JSON-eigenschappen voor v3 zijn:

```JSON
{
    "query": "this is your utterance you want predicted",
    "prediction":{
        "topIntent": "intent-name-1",
        "intents": {},
        "entities":{}
    }
}
```

Het `intents` object is een niet-geordende lijst. Ga er niet van uit dat het eerste onderliggende item in de `intents` overeenkomt met de `topIntent` . Gebruik in plaats daarvan de `topIntent` waarde om de score te vinden:

```nodejs
const topIntentName = response.prediction.topIntent;
const score = intents[topIntentName];
```

De wijzigingen in het JSON-schema van het antwoord zijn toegestaan voor:

* Maak onderscheid tussen de oorspronkelijke utterance,, en het resultaat van de voor `query` Spelling, niet `prediction` .
* Eenvoudiger programmatische toegang tot voorspelde gegevens. In plaats van te inventariseren via een matrix in v2, hebt u toegang tot waarden op **naam** voor zowel de intenties als de entiteiten. Voor voorspelde entiteits rollen wordt de rolnaam geretourneerd, omdat deze uniek is voor de hele app.
* Gegevens typen, indien bepaald, worden in acht genomen. Numerieke tekens worden niet langer geretourneerd als teken reeksen.
* Onderscheid tussen informatie over de voor spellingen van de eerste prioriteit en aanvullende meta gegevens, geretourneerd in het `$instance` object.

### <a name="entity-response-changes"></a>Reactie wijzigingen entiteit

#### <a name="marking-placement-of-entities-in-utterances"></a>Plaatsing van entiteiten in uitingen markeren

**In v2** werd een entiteit in een utterance gemarkeerd met de `startIndex` and `endIndex` .

**In v3** wordt de entiteit gemarkeerd met `startIndex` en `entityLength` .

#### <a name="access-instance-for-entity-metadata"></a>Toegang `$instance` voor entiteits-meta gegevens

Als u meta gegevens van de entiteit nodig hebt, moet de query reeks de `verbose=true` vlag gebruiken en het antwoord bevat de meta gegevens in het `$instance` object. Voor beelden worden weer gegeven in de JSON-antwoorden in de volgende secties.

#### <a name="each-predicted-entity-is-represented-as-an-array"></a>Elke voorspelde entiteit wordt weer gegeven als een matrix

Het `prediction.entities.<entity-name>` object bevat een matrix omdat elke entiteit meermaals kan worden voorspeld in de utterance.

<a name="prebuilt-entities-with-new-json"></a>

#### <a name="prebuilt-entity-changes"></a>Vooraf gemaakte entiteit wijzigingen

Het v3-antwoord object bevat wijzigingen in vooraf gemaakte entiteiten. Bekijk [specifieke vooraf gedefinieerde entiteiten](luis-reference-prebuilt-entities.md) voor meer informatie.

#### <a name="list-entity-prediction-changes"></a>Wijzigingen in de voor spelling van een lijst entiteit

De JSON voor de voor spelling van een lijst entiteit is gewijzigd in een matrix met matrices:

```JSON
"entities":{
    "my_list_entity":[
        ["canonical-form-1","canonical-form-2"],
        ["canonical-form-2"]
    ]
}
```
Elke Interior-matrix correspondeert met tekst in de utterance. Het object Interior is een matrix omdat dezelfde tekst in meer dan één sublijst van een lijst entiteit kan worden weer gegeven.

Bij het toewijzen `entities` van een object aan het `$instance` object, wordt de volg orde van de objecten behouden voor de voor spellingen van de lijst entiteit.

```nodejs
const item = 0; // order preserved, use same enumeration for both
const predictedCanonicalForm = entities.my_list_entity[item];
const associatedMetadata = entities.$instance.my_list_entity[item];
```

#### <a name="entity-role-name-instead-of-entity-name"></a>De rolnaam van de entiteit in plaats van de naam van de entiteit

In v2 heeft de `entities` matrix alle voorspelde entiteiten geretourneerd met de naam van de entiteit als de unieke id. In v3 geldt dat als de entiteit rollen gebruikt en de voor spelling voor een entiteits functie is, de primaire id de rolnaam is. Dit is mogelijk omdat de namen van entiteits rollen uniek moeten zijn voor de hele app, met inbegrip van andere model namen (intentie, entiteit).

In het volgende voor beeld: overweeg een utterance die de tekst bevat, `Yellow Bird Lane` . Deze tekst wordt voor speld als aangepaste `Location` entiteits rollen van `Destination` .

|Utterance tekst|Entiteitsnaam|Rolnaam|
|--|--|--|
|`Yellow Bird Lane`|`Location`|`Destination`|

In v2 wordt de entiteit geïdentificeerd door de naam van de _entiteit_ met de rol als eigenschap van het object:

```JSON
"entities":[
    {
        "entity": "Yellow Bird Lane",
        "type": "Location",
        "startIndex": 13,
        "endIndex": 20,
        "score": 0.786378264,
        "role": "Destination"
    }
]
```

In v3 wordt naar de entiteit verwezen door de rol van de _entiteit_, als de voor spelling voor de rol is:

```JSON
"entities":{
    "Destination":[
        "Yellow Bird Lane"
    ]
}
```

In v3 heeft hetzelfde resultaat `verbose` als de markering voor het retour neren van meta gegevens van entiteit:

```JSON
"entities":{
    "Destination":[
        "Yellow Bird Lane"
    ],
    "$instance":{
        "Destination": [
            {
                "role": "Destination",
                "type": "Location",
                "text": "Yellow Bird Lane",
                "startIndex": 25,
                "length":16,
                "score": 0.9837309,
                "modelTypeId": 1,
                "modelType": "Entity Extractor"
            }
        ]
    }
}
```

<a name="external-entities-passed-in-at-prediction-time"></a>
<a name="override-existing-model-predictions"></a>

## <a name="extend-the-app-at-prediction-time"></a>De app op de Voorspellings tijd uitbreiden

Meer informatie over het [uitbreiden van de](schema-change-prediction-runtime.md) app tijdens de voor spelling-runtime.

## <a name="deprecation"></a>Afschaffing

De v2 API wordt niet afgeschaft gedurende ten minste negen maanden na de preview-versie van v3.

## <a name="next-steps"></a>Volgende stappen

Gebruik de V3 API-documentatie om bestaande REST-aanroepen bij te werken naar LUIS [endpoint](https://westcentralus.dev.cognitive.microsoft.com/docs/services/luis-endpoint-api-v3-0/operations/5cb0a9459a1fe8fa44c28dd8) -api's.