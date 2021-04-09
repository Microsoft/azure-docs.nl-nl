---
title: Zoeken naar entiteiten met de Bing Entiteiten zoeken-API
titleSuffix: Azure Cognitive Services
description: Gebruik de Bing Entiteiten zoeken-API om te zoeken naar entiteiten en locaties en om deze te extraheren uit zoekquery's.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: aahi
ms.openlocfilehash: 9dabceda17defb24f2a916cd641f625feb551c6a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "96353286"
---
# <a name="searching-for-entities-with-the-bing-entity-api"></a>Zoeken naar entiteiten met de Bing Entiteiten-API

> [!WARNING]
> Bing Search-API's worden van Cognitive Services naar Bing Search Services verplaatst. Vanaf **30 oktober 2020** moeten nieuwe instanties van Bing Search worden ingericht overeenkomstig het proces dat [hier](/bing/search-apis/bing-web-search/create-bing-search-service-resource) is beschreven.
> Bing Search-API's die zijn ingericht met Cognitive Services, worden voor de komende drie jaar of tot het einde van uw Enterprise Agreement ondersteund, afhankelijk van wat het eerst afloopt.
> Raadpleeg [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource) voor migratie-instructies.

## <a name="suggest-search-terms-with-the-bing-autosuggest-api"></a>Zoektermen voorstellen met de Bing Automatische suggestie-API

Als u een zoekvak aanbiedt waarin de gebruiker een zoekterm invoert, kunt u de [Automatische suggestie-API voor Bing](../../bing-autosuggest/get-suggested-search-terms.md) gebruiken om de ervaring te verbeteren. De API retourneert voorgestelde queryreeksen op basis van gedeeltelijke zoektermen terwijl de gebruiker typt.

Nadat de gebruiker de zoekterm heeft ingevoerd, past u URL-codering toe op de term voordat u de queryparameter [q](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#query) instelt. Als de gebruiker bijvoorbeeld *Marcus Appel* invoert, stelt u `q` in op *Marcus+Appel* of *Marcus%20Appel*.

Als de zoekterm een spelfout bevat, bevat het zoekresultaat het object [QueryContext](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#querycontext). In het object worden de oorspronkelijke spelling weergegeven en de gecorrigeerde spelling die Bing heeft gebruikt voor de zoekopdracht.

```json
"queryContext": {
    "originalQuery": "hollo wrld",
    "alteredQuery": "hello world",
    "alterationOverrideQuery": "+hollo wrld",
    "adultIntent": false
}
```

## <a name="the-bing-entity-search-api-response"></a>Het antwoord van de Bing Entiteiten zoeken-API

Het API-antwoord bevat het object [SearchResponse](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#searchresponse). Als Bing een entiteit of plaats vindt die relevant is, bevat het object het veld `entities`, het veld `places` of beide. Als dat niet het geval is, bevat het antwoordobject geen van beide velden.
> [!NOTE]
> Entiteitantwoorden bieden ondersteuning voor meerdere markten, maar plaatsantwoorden bieden alleen ondersteuning voor Amerikaanse bedrijfslocaties. 

Het veld `entities` is een [EntityAnswer](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference)-object dat een lijst met [entiteit](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#entity)objecten bevat (zie het veld `value`). De lijst bevat mogelijk één dominante entiteit, meerdere entiteiten met vergelijkbare opties of beide. 

Er wordt een dominante entiteit geretourneerd waarvan Bing vindt dat deze als enige aan de aanvraag voldoet (er is geen twijfel over mogelijk). Als meerdere entiteiten het antwoord zouden kunnen bieden op de aanvraag, bevat de lijst meer dan één entiteit. Als in de aanvraag bijvoorbeeld de algemene naam van een reeks films bevat, bevat de lijst waarschijnlijk meerdere vergelijkbare entiteiten. Als de aanvraag echter een specifieke franchisenaam bevat, bevat de lijst waarschijnlijk één dominante entiteit.

Entiteiten omvatten bekende personen (zoals zangers, acteurs, atleten, modellen, etc.), plaatsen en bezienswaardigheden (zoals Mount Rainier of Lincoln Memorial) en specifieke zaken zoals een banaan, een hond, een boek of een filmtitel. Het veld [entityPresentationInfo](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#entitypresentationinfo) bevat hints waarmee het type van de entiteit kan worden geïdentificeerd. U ziet hier bijvoorbeeld of het om een persoon, film, dier of bezienswaardigheid gaat. Voor een lijst met mogelijke typen gaat u naar [Entiteitstypen](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#entity-types)

```json
"entityPresentationInfo": {
    "entityScenario": "DominantEntity",
    "entityTypeHints": ["Attraction"],
    "entityTypeDisplayHint": "Mountain"
}, ...
```

Hieronder ziet u een antwoord met een dominante entiteit en een vergelijkbare entiteit.

```json
{
    "_type": "SearchResponse",
    "queryContext": {
        "originalQuery": "Mount Rainier"
    },
    "entities": {
        "value": [{
            "contractualRules": [{
                "_type": "ContractualRules/LicenseAttribution",
                "targetPropertyName": "description",
                "mustBeCloseToContent": true,
                "license": {
                    "name": "CC-BY-SA",
                    "url": "https://creativecommons.org/licenses/by-sa/3.0/"
                },
                "licenseNotice": "Text under CC-BY-SA license"
            },
            {
                "_type": "ContractualRules/LinkAttribution",
                "targetPropertyName": "description",
                "mustBeCloseToContent": true,
                "text": "contoso.com",
                "url": "http://contoso.com/mount_rainier"
            },
            {
                "_type": "ContractualRules/MediaAttribution",
                "targetPropertyName": "image",
                "mustBeCloseToContent": true,
                "url": "http://contoso.com/mount-rainier"
            }],
            "webSearchUrl": "https://www.bing.com/search?q=Mount%20Rainier...",
            "name": "Mount Rainier",
            "url": "http://www.northwindtraders.com/",
            "image": {
                "name": "Mount Rainier",
                "thumbnailUrl": "https://www.bing.com/th?id=A4ae343983daa4...",
                "provider": [{
                    "_type": "Organization",
                    "url": "http://contoso.com/mount_rainier"
                }],
                "hostPageUrl": "http://contoso.com/commons/7/72/mount_rain...",
                "width": 110,
                "height": 110
            },
            "description": "Mount Rainier is 14,411 ft tall and the highest mountain...",
            "entityPresentationInfo": {
                "entityScenario": "DominantEntity",
                "entityTypeHints": ["Attraction"]
            },
            "bingId": "38b9431e-cf91-93be-0584-c42a3ecbfdc7"
        },
        {
            "contractualRules": [{
                "_type": "ContractualRules/MediaAttribution",
                "targetPropertyName": "image",
                "mustBeCloseToContent": true,
                "url": "http://contoso.com/mount_rainier_national_park"
            }],
            "webSearchUrl": "https://www.bing.com/search?q=Mount%20Rainier%20National...",
            "name": "Mount Rainier National Park",
            "url": "http://worldwideimporters.com/",
            "image": {
                "name": "Mount Rainier National Park",
                "thumbnailUrl": "https://www.bing.com/th?id=A91bdc5a1b648a695a39...",
                "provider": [{
                    "_type": "Organization",
                    "url": "http://contoso.com/mount_rainier_national_park"
                }],
                "hostPageUrl": "http://contoso.com/en/7/7a...",
                "width": 50,
                "height": 50
            },
            "description": "Mount Rainier National Park is a United States National Park...",
            "entityPresentationInfo": {
                "entityScenario": "DisambiguationItem",
                "entityTypeHints": ["Organization"]
            },
            "bingId": "29d4b681-227a-3924-7bb1-8a54e8666b8c"
        }]
    }
}
```

De entiteit bevat de velden `name`, `description` en `image`. Als u deze velden weergeeft in uw gebruikerservaring, moet u er een kenmerk aan toewijzen. Het veld `contractualRules` bevat een lijst kenmerken die u moet toepassen. Op basis van de contractregel wordt geïdentificeerd op welk veld het kenmerk van toepassing is. Zie [Kenmerk](#data-attribution) voor meer informatie over het toepassen van kenmerken.

```json
"contractualRules": [{
    "_type": "ContractualRules/LicenseAttribution",
    "targetPropertyName": "description",
    "mustBeCloseToContent": true,
    "license": {
        "name": "CC-BY-SA",
        "url": "https://creativecommons.org/licenses/by-sa/3.0/"
    },
    "licenseNotice": "Text under CC-BY-SA license"
},
{
    "_type": "ContractualRules/LinkAttribution",
    "targetPropertyName": "description",
    "mustBeCloseToContent": true,
    "text": "contoso.com",
    "url": "http://contoso.com/wiki/Mount_Rainier"
},
{
    "_type": "ContractualRules/MediaAttribution",
    "targetPropertyName": "image",
    "mustBeCloseToContent": true,
    "url": "http://contoso.com/wiki/Mount_Rainier"
}], ...
```

Wanneer u de entiteitgegevens (naam, beschrijving en afbeelding) weergeeft, moet u de URL in het veld `webSearchUrl` gebruiken om te voorzien in een koppeling naar de Bing-zoekresultatenpagina die de entiteit bevat.

## <a name="find-places"></a>Locaties zoeken

Het veld `places` is een [LocalEntityAnswer](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference)-object dat een lijst met [plaats](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#place)objecten bevat (zie [Entiteitstypen](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#entity-types) voor meer informatie). De lijst bevat één of meer lokale entiteiten die antwoord bieden op de aanvraag.

Plaatsen kunnen bijvoorbeeld restaurants, hotels of lokale bedrijven zijn. Het veld [entityPresentationInfo](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#entitypresentationinfo) bevat hints waarmee het type van de lokale entiteit kan worden geïdentificeerd. De lijst bevat een lijst met hints, zoals Place, LocalBusiness of Restaurant. Met elke volgende hint in de matrix wordt het entiteitstype verder benaderd. Voor een lijst met mogelijke typen gaat u naar [Entiteitstypen](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#entity-types)

```json
"entityPresentationInfo": {
    "entityScenario": "ListItem",
    "entityTypeHints": ["Place",
    "LocalBusiness",
    "Restaurant"]
}, ...
```
> [!NOTE]
> Entiteitantwoorden bieden ondersteuning voor meerdere markten, maar plaatsantwoorden bieden alleen ondersteuning voor Amerikaanse bedrijfslocaties. 

Met lokale-entiteitsquery's zoals *restaurant in de buurt* moet de locatie van de gebruiker bekend zijn om nauwkeurige resultaten te kunnen bieden. Uw aanvragen moeten altijd de headers X-Search-Location en X-MSEdge-ClientIP bevatten om de locatie van de gebruiker op te geven. Als Bing van mening is dat het handig zou zijn de locatie van de gebruiker te weten, wordt het veld `askUserForLocation` van [QueryContext](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#querycontext) ingesteld op **true**. 

```json
{
    "_type": "SearchResponse",
    "queryContext": {
        "originalQuery": "Sinful Bakery and Cafe",
        "askUserForLocation": true
    },
    ...
}
```

Een plaatsresultaat bevat de naam van de plaats, het adres, het telefoonnummer en de URL naar de website van de entiteit. Wanneer u de entiteitgegevens weergeeft, moet u de URL in het veld `webSearchUrl` gebruiken om te voorzien in een koppeling naar de Bing-zoekresultatenpagina die de entiteit bevat.

```json
"places": {
    "value": [{
        "_type": "Restaurant",
        "webSearchUrl": "https://www.bing.com/search?q=Sinful%20Bakery...",
        "name": "Liberty's Delightful Sinful Bakery & Cafe",
        "url": "http://libertysdelightfulsinfulbakeryandcafe.com/",
        "entityPresentationInfo": {
            "entityScenario": "ListItem",
            "entityTypeHints": ["Place",
            "LocalBusiness",
            "Restaurant"]
        },
        "address": {
            "addressLocality": "Seattle",
            "addressRegion": "WA",
            "postalCode": "98112",
            "addressCountry": "US",
            "neighborhood": "Madison Park"
        },
        "telephone": "(800) 555-1212"
    }]
}
```

> [!NOTE]
> U, of een derde partij die namens u handelt, mag geen gegevens van de Entities-API gebruiken, behouden, opslaan, in een cache plaatsen, delen of distribueren voor test-, ontwikkelings-, trainings- of distributiedoeleinden, noch voor het beschikbaar maken van een niet-Microsoft-service of -functie.  

## <a name="data-attribution"></a>Toeschrijving van gegevens

Antwoorden van de Bing Entity-API bevatten informatie die het eigendom is van derde partijen. Het is uw verantwoordelijkheid om ervoor te zorgen dat u op de juiste manier gebruikmaakt van de gegevens, bijvoorbeeld door te voldoen aan een Creative Commons-licentie die is afgegeven om uw gebruikerservaring mogelijk te maken.

Als een antwoord of resultaat het veld `contractualRules`, `attributions` of `provider` bevat, moet u de gegevens toeschrijven. Als het antwoord geen van deze velden bevat, is dit niet vereist. Als het antwoord het veld `contractualRules` en het veld `attributions` en/of de velden `provider` bevat, moet u de contractuele regels volgen om de gegevens toe te schrijven.

In het volgende voorbeeld ziet u een entiteit met de contractuele regel MediaAttribution en een Image-element met een veld `provider`. De regel MediaAttribution identificeert de afbeelding als het doel van de regel, zodat u het veld `provider` van de afbeelding kunt negeren en in plaats daarvan de regel MediaAttribution moet volgen om de gegevens toe te schrijven.  

```json
"value": [{
    "contractualRules": [
        ...
        {
            "_type": "ContractualRules/MediaAttribution",
            "targetPropertyName": "image",
            "mustBeCloseToContent": true,
            "url": "http://contoso.com/mount_rainier"
        }
    ],
    ...
    "image": {
        "name": "Mount Rainier",
        "thumbnailUrl": "https://www.bing.com/th?id=A46378861201...",
        "provider": [{
            "_type": "Organization",
            "url": "http://contoso.com/mount_rainier"
        }],
        "hostPageUrl": "http://www.graphicdesigninstitute.com/Uploaded...",
        "width": 110,
        "height": 110
    },
    ...
}]
```

Als een contractuele regel het veld `targetPropertyName` bevat, geldt de regel alleen voor het betreffende veld. Anders is de regel van toepassing op het bovenliggende object dat het veld `contractualRules` bevat.

In het volgende voorbeeld bevat de regel `LinkAttribution` het veld `targetPropertyName`, wat betekent dat de regel geldt voor het veld `description`. Voor regels die betrekking hebben op specifieke velden moet u direct na de betreffende gegevens een regel invoegen met een hyperlink naar de website van de provider. Als u bijvoorbeeld de beschrijving wilt toeschrijven, voegt u direct na de beschrijvende tekst een regel in met een hyperlink naar de gegevens op de website van de provider, in dit voorbeeld een koppeling naar contoso.com.

```json
"entities": {
    "value": [{
            ...
            "description": "Marcus Appel is a former American....",
            ...
            "contractualRules": [{
                    "_type": "ContractualRules/LinkAttribution",
                    "targetPropertyName": "description",
                    "mustBeCloseToContent": true,
                    "text": "contoso.com",
                    "url": "http://contoso.com/cr?IG=B8AD73..."
                 },
            ...
  
```

### <a name="license-attribution"></a>Toeschrijving van licentie

Als de lijst met contractuele regels een regel [LicenseAttribution](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#licenseattribution) bevat, moet u de mededeling opnemen op de regel direct na de inhoud waarop de licentie van toepassing is. De regel `LicenseAttribution` maakt gebruik van het veld `targetPropertyName` om de eigenschap te identificeren waarop de licentie van toepassing is.

Hieronder ziet u een voorbeeld met een regel `LicenseAttribution`.

![Toeschrijving van licentie](../media/cognitive-services-bing-entities-api/licenseattribution.png)

De licentiemededeling die u opneemt, moet een hyperlink bevatten naar de website met informatie over de licentie. Meestal maakt u een hyperlink van de naam van de licentie. Als de mededeling bijvoorbeeld **Tekst valt onder CC-BY-SA-licentie** is en CC-BY-SA de naam is van de licentie, maakt u van CC-BY-SA een hyperlink.

### <a name="link-and-text-attribution"></a>Toeschrijving van koppeling en tekst

De regels [LinkAttribution](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#linkattribution) en [TextAttribution](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#textattribution) worden meestal gebruikt voor identificatie van de provider van de gegevens. Het veld `targetPropertyName` geeft het veld aan waarop de regel van toepassing is.

Als u de providers wilt toeschrijven, neemt u direct na de inhoud waarom het gaat een regel op met de toeschrijving (bijvoorbeeld na het target-veld). De regel moet een label hebben dat duidelijk aangeeft dat de providers de bron zijn van de gegevens. Voorbeeld: 'Gegevens van: contoso.com'. Voor `LinkAttribution`-regels moet u een hyperlink toevoegen naar de website van de provider.

Hieronder ziet u een voorbeeld met de regels `LinkAttribution` en `TextAttribution`.

![Toeschrijving van koppelingstekst](../media/cognitive-services-bing-entities-api/linktextattribution.png)

### <a name="media-attribution"></a>Toeschrijving van media

Als de entiteit een afbeelding bevat en u deze wilt weergeven, moet u een klikbare koppeling naar de website van de provider opgeven. Als de entiteit een regel [MediaAttribution](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#mediaattribution) bevat, gebruikt u de URL van de regel om de klikbare koppeling te maken. Gebruik anders de URL die is opgenomen in het veld `provider` van de afbeelding om de klikbare koppeling te maken.

Hieronder ziet u een voorbeeld met het veld `provider` van een afbeelding en contractuele regels. Omdat het voorbeeld een contractuele regel bevat, kunt u het veld `provider` van de afbeelding negeren en de regel `MediaAttribution` toepassen.

![Toeschrijving van media](../media/cognitive-services-bing-entities-api/mediaattribution.png)

### <a name="search-or-search-like-experience"></a>Zoek- of zoekachtige ervaring

Net als bij de Bing Webzoekopdrachten-API, mag de Bing Entiteiten zoeken-API alleen worden gebruikt bij rechtstreekse query's of zoekopdrachten van gebruikers, of voor acties in een app of ervaring die logischerwijs kunnen worden geïnterpreteerd als een zoekopdracht van een gebruiker. Ter illustratie ziet u verderop enkele voorbeelden van acceptabele zoek- of zoekachtige ervaringen.

- De gebruiker voert in een app een query rechtstreeks in in een zoekvak
- De gebruiker selecteert specifieke tekst of een specifieke afbeelding en vraagt om meer of aanvullende informatie
- De gebruiker vraagt een zoekbot naar een bepaald onderwerp
- De gebruiker blijft bij een bepaald object of en bepaalde entiteit hangen in een scenario met visueel zoeken

Als u niet zeker of uw ervaring kan worden gezien als een zoekachtige ervaring, wordt het aanbevolen contact op te nemen met Microsoft.

## <a name="throttling-requests"></a>Aanvraagbeperkingen

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>Volgende stappen

* Raadpleeg een [Quickstart](../quickstarts/csharp.md) om te beginnen met zoeken naar entiteiten met de Bing Entiteiten zoeken-API.