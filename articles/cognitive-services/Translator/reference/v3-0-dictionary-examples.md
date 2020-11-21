---
title: Voor beelden van Translator Dictionary-methode
titleSuffix: Azure Cognitive Services
description: De voor beelden van de vertaler-woorden lijst biedt voor beelden die laten zien hoe termen in de woorden lijst in de context worden gebruikt.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 01/21/2020
ms.author: swmachan
ms.openlocfilehash: 1fe5a5669173733e3d0cf1b56b9997d15a3ff138
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95021300"
---
# <a name="translator-30-dictionary-examples"></a>Vertaler 3,0: Dictionary-voor beelden

Biedt voor beelden die laten zien hoe termen in de woorden lijst in de context worden gebruikt. Deze bewerking wordt samen gebruikt bij het [opzoeken van woorden lijsten](./v3-0-dictionary-lookup.md).

## <a name="request-url"></a>Aanvraag-URL

Een `POST` aanvraag verzenden naar:

```HTTP
https://api.cognitive.microsofttranslator.com/dictionary/examples?api-version=3.0
```

## <a name="request-parameters"></a>Aanvraag parameters

Aanvraag parameters die zijn door gegeven voor de query reeks zijn:

| Query parameter | Description |
| --------- | ----------- |
| api-versie <img width=200/> | **Vereiste para meter**.<br/>De versie van de API die door de client is aangevraagd. Waarde moet zijn `3.0` . |
| from | **Vereiste para meter**.<br/>Geeft de taal van de invoer tekst aan. De bron taal moet een van de [ondersteunde talen](./v3-0-languages.md) zijn die in het `dictionary` bereik zijn opgenomen. |
| tot | **Vereiste para meter**.<br/>Hiermee geeft u de taal van de uitvoer tekst op. De doel taal moet een van de [ondersteunde talen](./v3-0-languages.md) zijn die in het `dictionary` bereik zijn opgenomen.  | 

Aanvraag headers zijn onder andere:

| Kopteksten  | Beschrijving |
| ------ | ----------- |
| Verificatieheader(s) <img width=200/>  | **Vereiste aanvraagheader**.<br/>Zie <a href="/azure/cognitive-services/translator/reference/v3-0-reference#authentication">Beschikbare opties voor verificatie</a>. |
| Content-Type | **Vereiste aanvraagheader**.<br/>Hiermee geeft u het inhoudstype van de payload op. Mogelijke waarden zijn: `application/json` . |
| Content-Length   | **Vereiste aanvraagheader**.<br/>De lengte van de aanvraagtekst. |
| X-ClientTraceId   | **Optioneel**.<br/>Een door de client gegenereerde GUID om de aanvraag op unieke wijze te identificeren. U kunt deze header weglaten als u de tracerings-id in de queryreeks opneemt middels een queryparameter met de naam `ClientTraceId`. |

## <a name="request-body"></a>Aanvraagbody

De hoofd tekst van de aanvraag is een JSON-matrix. Elk matrix element is een JSON-object met de volgende eigenschappen:

  * `Text`: Een teken reeks die de term aangeeft die moet worden gezocht. Dit moet de waarde zijn van een `normalizedText` veld uit de back-vertalingen van een eerdere lookup-aanvraag voor een [woorden lijst](./v3-0-dictionary-lookup.md) . Dit kan ook de waarde van het `normalizedSource` veld zijn.

  * `Translation`: Een teken reeks waarmee de vertaalde tekst wordt opgegeven die eerder door de zoek bewerking van de [woorden lijst](./v3-0-dictionary-lookup.md) is geretourneerd. Dit moet de waarde zijn uit het `normalizedTarget` veld in de `translations` lijst van het antwoord op de [woorden lijst opzoeken](./v3-0-dictionary-lookup.md) . Met de service worden voor beelden geretourneerd voor het specifieke woord paar van de doel groep.

Een voor beeld is:

```json
[
    {"Text":"fly", "Translation":"volar"}
]
```

De volgende beperkingen zijn van toepassing:

* De matrix kan Maxi maal 10 elementen bevatten.
* De tekst waarde van een matrix element mag niet langer zijn dan 100 tekens, inclusief spaties.

## <a name="response-body"></a>Hoofdtekst van de reactie

Een geslaagde reactie is een JSON-matrix met één resultaat voor elke teken reeks in de invoer matrix. Een resultaat object bevat de volgende eigenschappen:

  * `normalizedSource`: Een teken reeks met de genormaliseerde vorm van de bron term. Over het algemeen moet dit gelijk zijn aan de waarde van het `Text` veld in de hoofd tekst van de aanvraag.
    
  * `normalizedTarget`: Een teken reeks met de genormaliseerde vorm van de doel term. Over het algemeen moet dit gelijk zijn aan de waarde van het `Translation` veld in de hoofd tekst van de aanvraag.
  
  * `examples`: Een lijst met voor beelden voor het paar (bron term, doel term). Elk element van de lijst is een object met de volgende eigenschappen:

    * `sourcePrefix`: De teken reeks die moet worden samengevoegd _vóór_ de waarde van `sourceTerm` om een volledig voor beeld te vormen. Voeg geen spatie toe, omdat deze al aanwezig is wanneer dit moet zijn. Deze waarde kan een lege teken reeks zijn.

    * `sourceTerm`: Een teken reeks die gelijk is aan de werkelijke term die wordt opgezocht. De teken reeks wordt toegevoegd aan `sourcePrefix` en `sourceSuffix` voor het volledige voor beeld. De waarde ervan wordt gescheiden, zodat deze kan worden gemarkeerd in een gebruikers interface, bijvoorbeeld door het vet te maken.

    * `sourceSuffix`: De teken reeks die moet worden samengevoegd _na_ de waarde van `sourceTerm` om een volledig voor beeld te vormen. Voeg geen spatie toe, omdat deze al aanwezig is wanneer dit moet zijn. Deze waarde kan een lege teken reeks zijn.

    * `targetPrefix`: Een teken reeks die vergelijkbaar is met `sourcePrefix` maar voor het doel.

    * `targetTerm`: Een teken reeks die vergelijkbaar is met `sourceTerm` maar voor het doel.

    * `targetSuffix`: Een teken reeks die vergelijkbaar is met `sourceSuffix` maar voor het doel.

    > [!NOTE]
    > Als er geen voor beelden in de woorden lijst staan, is de reactie 200 (OK), maar `examples` is de lijst een lege lijst.

## <a name="examples"></a>Voorbeelden

In dit voor beeld ziet u hoe u voor beelden kunt opzoeken voor het paar dat is gemaakt met de Engelse term `fly` en de Spaanse vertaling `volar` .

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/dictionary/examples?api-version=3.0&from=en&to=es" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'fly', 'Translation':'volar'}]"
```

De antwoord tekst (afgekort voor de duidelijkheid) is:

```
[
    {
        "normalizedSource":"fly",
        "normalizedTarget":"volar",
        "examples":[
            {
                "sourcePrefix":"They need machines to ",
                "sourceTerm":"fly",
                "sourceSuffix":".",
                "targetPrefix":"Necesitan máquinas para ",
                "targetTerm":"volar",
                "targetSuffix":"."
            },      
            {
                "sourcePrefix":"That should really ",
                "sourceTerm":"fly",
                "sourceSuffix":".",
                "targetPrefix":"Eso realmente debe ",
                "targetTerm":"volar",
                "targetSuffix":"."
            },
            //
            // ...list abbreviated for documentation clarity
            //
        ]
    }
]
```