---
title: Zoek methode voor Translator Dictionary
titleSuffix: Azure Cognitive Services
description: De Zoek methode voor de woorden lijst bevat alternatieve vertalingen voor een woord en een klein aantal idiomatisch-zinnen.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 01/21/2020
ms.author: swmachan
ms.openlocfilehash: 6ada9c06f0befbea4b9aa85f95651e96dec595b7
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95021283"
---
# <a name="translator-30-dictionary-lookup"></a>Translator 3,0: opzoeken in woorden lijst

Biedt alternatieve vertalingen voor een woord en een klein aantal idiomatisch-zinnen. Elke vertaling heeft een deel van spraak en een lijst met back-vertalingen. Met de back-vertalingen kunnen gebruikers de vertaling in context begrijpen. Met de [voorbeeld bewerking voor de woorden lijst](./v3-0-dictionary-examples.md) kunt u verder inzoomen om het voor beeld van elk Vertaal paar te bekijken.

## <a name="request-url"></a>Aanvraag-URL

Een `POST` aanvraag verzenden naar:

```HTTP
https://api.cognitive.microsofttranslator.com/dictionary/lookup?api-version=3.0
```

## <a name="request-parameters"></a>Aanvraag parameters

Aanvraag parameters die zijn door gegeven voor de query reeks zijn:

| Query parameter  | Description |
| ------ | ----------- |
| api-versie <img width=200/>   | **Vereiste para meter**.<br/>De versie van de API die door de client is aangevraagd. Waarde moet `3.0` |
| from | **Vereiste para meter**.<br/>Geeft de taal van de invoer tekst aan. De bron taal moet een van de [ondersteunde talen](./v3-0-languages.md) zijn die in het `dictionary` bereik zijn opgenomen. |
| tot   | **Vereiste para meter**.<br/>Hiermee geeft u de taal van de uitvoer tekst op. De doel taal moet een van de [ondersteunde talen](v3-0-languages.md) zijn die in het `dictionary` bereik zijn opgenomen. |


Aanvraag headers zijn onder andere:

| Kopteksten  | Beschrijving |
| ------ | ----------- |
| Verificatieheader(s) <img width=200/>  | **Vereiste aanvraagheader**.<br/>Zie <a href="/azure/cognitive-services/translator/reference/v3-0-reference#authentication">Beschikbare opties voor verificatie</a>. |
| Content-Type | **Vereiste aanvraagheader**.<br/>Hiermee geeft u het inhoudstype van de payload op. Mogelijke waarden zijn: `application/json` . |
| Content-Length   | **Vereiste aanvraagheader**.<br/>De lengte van de aanvraagtekst. |
| X-ClientTraceId   | **Optioneel**.<br/>Een door de client gegenereerde GUID om de aanvraag op unieke wijze te identificeren. U kunt deze header weglaten als u de tracerings-id in de queryreeks opneemt middels een queryparameter met de naam `ClientTraceId`. |

## <a name="request-body"></a>Aanvraagbody

De hoofd tekst van de aanvraag is een JSON-matrix. Elk matrix element is een JSON-object met een teken reeks eigenschap met `Text` de naam, die de term vertegenwoordigt die moet worden gezocht.

```json
[
    {"Text":"fly"}
]
```

De volgende beperkingen zijn van toepassing:

* De matrix kan Maxi maal 10 elementen bevatten.
* De tekst waarde van een matrix element mag niet langer zijn dan 100 tekens, inclusief spaties.

## <a name="response-body"></a>Hoofdtekst van de reactie

Een geslaagde reactie is een JSON-matrix met één resultaat voor elke teken reeks in de invoer matrix. Een resultaat object bevat de volgende eigenschappen:

  * `normalizedSource`: Een teken reeks met de genormaliseerde vorm van de bron term. Als de aanvraag bijvoorbeeld "JOHN" is, is de genormaliseerde vorm "John". De inhoud van dit veld wordt de invoer voor [Zoek voorbeelden](./v3-0-dictionary-examples.md).
    
  * `displaySource`: Een teken reeks waarmee de bron term in een formulier het meest geschikt is voor weer gave van eind gebruikers. Als de invoer bijvoorbeeld ' JOHN ' is, wordt in het weergave formulier de gebruikelijke spelling van de naam weer gegeven: "John". 

  * `translations`: Een lijst met vertalingen voor de bron term. Elk element van de lijst is een object met de volgende eigenschappen:

    * `normalizedTarget`: Een teken reeks met de genormaliseerde vorm van deze term in de doel taal. Deze waarde moet worden gebruikt als invoer voor [Zoek voorbeelden](./v3-0-dictionary-examples.md).

    * `displayTarget`: Een teken reeks waarmee de term in de doel taal en in een formulier wordt weer gegeven die het meest geschikt is voor weergave van eind gebruikers. Over het algemeen geldt dit alleen voor het `normalizedTarget` hoofdletter gebruik. Een voor beeld: een eigen zelfstandig naam woord zoals ' Juan ' heeft `normalizedTarget = "juan"` en `displayTarget = "Juan"` .

    * `posTag`: Een teken reeks waarmee deze term wordt gekoppeld aan een deel van een spraak label.

        | Label naam | Description  |
        |----------|--------------|
        | Corr      | Bijvoeglijke naamwoorden   |
        | ADV      | Beparameters      |
        | CONJ     | Voegwoorden |
        | DET      | Zoekers  |
        | MODAAL    | Termen        |
        | ZELFSTANDIG     | Zelfstandige naamwoorden        |
        | PREP     | Preposities |
        | PRON     | Voornaamwoorden     |
        | TERM     | Termen        |
        | DAARENTEG    | Anders        |

        Als implementatie opmerking worden deze Tags bepaald door een deel van spraak Tags aan de Engelse kant en vervolgens de meest frequente tag te nemen voor elk bron/doel-paar. Als mensen vaak een Spaans woord vertalen naar een ander deel van een spraak code in het Engels, kunnen Tags uiteindelijk worden beëindigd (ten opzichte van het Spaanse woord).

    * `confidence`: Een waarde tussen 0,0 en 1,0 voor het ' vertrouwen ' (of mogelijk nauw keuriger ', ' waarschijnlijker ', ' kans in de opleidings gegevens ') van die Vertaal paar. De som van betrouwbaarheids scores voor één bron woord kan niet oplopen tot 1,0. 

    * `prefixWord`: Een teken reeks waarmee het woord wordt weer gegeven als een voor voegsel van de vertaling. Dit is momenteel de geslachte naam van zelfstandige naam woorden, in talen met geslachte determiners. Het voor voegsel van het Spaanse woord "Mosca" is bijvoorbeeld "La", omdat "Mosca" een Feminine-zelfstandig naam woord in het Spaans is. Dit is alleen afhankelijk van de vertaling en niet voor de bron. Als er geen voor voegsel is, is dit de lege teken reeks.
    
    * `backTranslations`: Een lijst met ' back-vertalingen ' van het doel. Bijvoorbeeld bron woorden waarnaar het doel kan worden vertaald. De lijst moet het aangevraagde bron woord bevatten (bijvoorbeeld, als het gezochte bron woord ' vliegen ' is, dan is het gegarandeerd dat ' vliegen ' in de `backTranslations` lijst wordt weer geven). Het is echter niet zeker dat deze zich op de eerste positie bevindt. Dit is vaak niet het geval. Elk element van de `backTranslations` lijst is een object dat wordt beschreven door de volgende eigenschappen:

        * `normalizedText`: Een teken reeks met de genormaliseerde vorm van de bron term die een back-translatie van het doel is. Deze waarde moet worden gebruikt als invoer voor [Zoek voorbeelden](./v3-0-dictionary-examples.md).        

        * `displayText`: Een teken reeks die de bron term bevat die een back-upconversie van het doel is in een formulier dat het meest geschikt is voor weer gave op de eind gebruiker.

        * `numExamples`: Een geheel getal dat het aantal voor beelden voor deze Vertaal paar voor stelt. De werkelijke voor beelden moeten worden opgehaald met een afzonderlijke aanroep naar [lookup-voor beelden](./v3-0-dictionary-examples.md). Het nummer is voornamelijk bedoeld om weer gave in een UX te vergemakkelijken. Zo kan een gebruikers interface een Hyper Link toevoegen aan de back-translatie als het aantal voor beelden groter is dan nul en de back-vertalingen als tekst zonder opmaak weer geven als er geen voor beelden zijn. Houd er rekening mee dat het werkelijke aantal voor beelden dat wordt geretourneerd door een aanroep van de [voor beelden van de zoek opdracht](./v3-0-dictionary-examples.md) , kan kleiner zijn dan `numExamples` , omdat extra filters op de vlucht kunnen worden toegepast om "onjuiste" voor beelden te verwijderen.
        
        * `frequencyCount`: Een geheel getal dat de frequentie van dit vertaal paar in de gegevens vertegenwoordigt. Het belangrijkste doel van dit veld is een gebruikers interface te bieden met een manier om back-vertalingen te sorteren, zodat de meest voorkomende voor waarden het eerst zijn.

    > [!NOTE]
    > Als de term die wordt gezocht, niet in de woorden lijst voor komt, is de reactie 200 (OK), maar `translations` is de lijst een lege lijst.

## <a name="examples"></a>Voorbeelden

In dit voor beeld ziet u hoe u alternatieve vertalingen in het Spaans van de Engelse periode kunt opzoeken `fly` .

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/dictionary/lookup?api-version=3.0&from=en&to=es" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'fly'}]"
```

De antwoord tekst (afgekort voor de duidelijkheid) is:

```
[
    {
        "normalizedSource":"fly",
        "displaySource":"fly",
        "translations":[
            {
                "normalizedTarget":"volar",
                "displayTarget":"volar",
                "posTag":"VERB",
                "confidence":0.4081,
                "prefixWord":"",
                "backTranslations":[
                    {"normalizedText":"fly","displayText":"fly","numExamples":15,"frequencyCount":4637},
                    {"normalizedText":"flying","displayText":"flying","numExamples":15,"frequencyCount":1365},
                    {"normalizedText":"blow","displayText":"blow","numExamples":15,"frequencyCount":503},
                    {"normalizedText":"flight","displayText":"flight","numExamples":15,"frequencyCount":135}
                ]
            },
            {
                "normalizedTarget":"mosca",
                "displayTarget":"mosca",
                "posTag":"NOUN",
                "confidence":0.2668,
                "prefixWord":"",
                "backTranslations":[
                    {"normalizedText":"fly","displayText":"fly","numExamples":15,"frequencyCount":1697},
                    {"normalizedText":"flyweight","displayText":"flyweight","numExamples":0,"frequencyCount":48},
                    {"normalizedText":"flies","displayText":"flies","numExamples":9,"frequencyCount":34}
                ]
            },
            //
            // ...list abbreviated for documentation clarity
            //
        ]
    }
]
```

In dit voor beeld ziet u wat er gebeurt wanneer de term die wordt gezocht, niet bestaat voor het geldige woordenlijst paar.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/dictionary/lookup?api-version=3.0&from=en&to=es" -H "X-ClientTraceId: 875030C7-5380-40B8-8A03-63DACCF69C11" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'fly123456'}]"
```

Omdat de term niet in de woorden lijst voor komt, bevat de antwoord tekst een lege `translations` lijst.

```
[
    {
        "normalizedSource":"fly123456",
        "displaySource":"fly123456",
        "translations":[]
    }
]
```