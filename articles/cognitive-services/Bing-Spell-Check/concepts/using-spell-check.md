---
title: De Bing Spellingcontrole-API gebruiken
titleSuffix: Azure Cognitive Services
description: Meer informatie over de Bing Spellingcontrole modi, instellingen en andere informatie met betrekking tot de API.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: aahi
ms.openlocfilehash: 2031c31c6ea083452bbdbb95be74adf29be1f858
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "96349313"
---
# <a name="using-the-bing-spell-check-api"></a>De Bing Spellingcontrole-API gebruiken

> [!WARNING]
> Bing Search-API's worden van Cognitive Services naar Bing Search Services verplaatst. Vanaf **30 oktober 2020** moeten nieuwe instanties van Bing Search worden ingericht overeenkomstig het proces dat [hier](/bing/search-apis/bing-web-search/create-bing-search-service-resource) is beschreven.
> Bing Search-API's die zijn ingericht met Cognitive Services, worden voor de komende drie jaar of tot het einde van uw Enterprise Agreement ondersteund, afhankelijk van wat het eerst afloopt.
> Raadpleeg [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource) voor migratie-instructies.

Gebruik dit artikel voor meer informatie over het gebruik van de Bing Spellingcontrole-API voor het uitvoeren van contextuele grammatica- en spellingcontrole. De meeste spellingcontroles zijn afhankelijk van regelsets op basis van een woordenlijst, maar de Bing-spellingcontrole maakt gebruik van machine learning en statistische automatische vertaling voor nauwkeurige en contextuele correcties. 

## <a name="spell-check-modes"></a>Modi van spellingcontroles

De API ondersteunt twee controlemodi, `Proof` en `Spell`.  Probeer [hier](https://azure.microsoft.com/services/cognitive-services/spell-check/) voorbeelden uit.

### <a name="proof---for-documents"></a>Controleren - voor documenten 

De standaardmodus is `Proof`. De spellingmodus `Proof` bevat de meest uitgebreide controles, inclusief hoofdlettergebruik, basisinterpunctie en andere functies om te helpen bij het maken van het document. Deze is echter alleen beschikbaar in en-US (Engels-Verenigde Staten), es-ES (Spaans), pt-BR (Portugees). (Opmerking: alleen in de bètaversie voor Spaans en Portugees). Voor alle andere markten stelt u de queryparameter mode in op Spelling. 

> [!NOTE]
> Als de querytekst langer is dan 4096, wordt deze afgekapt tot 4096 tekens, waarna de tekst wordt verwerkt. 

### <a name="spell----for-web-searchesqueries"></a>Spelling - voor webzoekopdrachten/query's

`Spell` is een krachtigere functie waarmee betere zoekresultaten worden geretourneerd. In de modus `Spell` worden de meeste spelfouten gevonden, maar worden bepaalde grammaticafouten over het hoofd gezien die wel worden ontdekt door `Proof`, bijvoorbeeld hoofdlettergebruik en herhaalde woorden.

> [!NOTE]
> * Hieronder ziet u de maximale ondersteunde querylengte. Als de query de maximale lengte overschrijdt, worden de query en de bijbehorende resultaten niet gewijzigd.
>    * 130 tekens voor de volgende taalcodes: en, de, es, fr, pl, pt, sv, ru, nl, nb, tr-tr, it, zh, ko. 
>    * 65 tekens voor alle andere taalcodes.
> * De spellingmodus biedt geen ondersteuning voor vierkante haakjes (`[` en `]`) in query's, en kunnen inconsistente resultaten tot gevolg hebben. We raden u aan deze te verwijderen uit uw query's wanneer u de spellingmodus gebruikt.

## <a name="market-setting"></a>Marktinstelling

Er moet een [marktcode](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference#market-codes) bij de queryparameter `mkt` worden opgegeven in uw aanvraag. De API gebruikt anders een standaardmarkt op basis van het IP-adres van de aanvraag.


## <a name="http-post-and-get-support"></a>Ondersteuning voor HTTP POST en GET

De API biedt ondersteuning voor HTTP POST en HTTP GET. Welke u gebruikt, is afhankelijk van de lengte van de tekst die u wilt controleren. Als de tekenreeksen altijd korter zijn dan 1500 tekens, gebruikt u een GET. Gebruik POST ter ondersteuning van tekenreeksen van maximaal 10.000 tekens. De tekstreeks kan een ongeldig UTF-8-teken bevatten.

In het volgende voorbeeld ziet u een POST-aanvraag voor het controleren van de spelling en grammatica van een tekstreeks. Het voorbeeld bevat de queryparameter [mode](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference#mode) voor de volledigheid (deze kan worden weggelaten omdat `mode` standaard is ingesteld op Controleren). De queryparameter [tekst](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference#text) bevat de tekenreeks die moet worden gecontroleerd.
  
```  
POST https://api.cognitive.microsoft.com/bing/v7.0/spellcheck?mode=proof&mkt=en-us HTTP/1.1  
Content-Type: application/x-www-form-urlencoded  
Content-Length: 47  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
 
text=when+its+your+turn+turn,+john,+come+runing  
``` 

Als u HTTP GET gebruikt, neemt u de queryparameter `text` op in de queryreeks van de URL
  
Hieronder ziet u het antwoord op de vorige aanvraag. Het antwoord bevat een object [SpellCheck](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference#spellcheck). 
  
```json
{  
    "_type" : "SpellCheck",  
    "flaggedTokens" : [{  
        "offset" : 5,  
        "token" : "its",  
        "type" : "UnknownToken",  
        "suggestions" : [{  
            "suggestion" : "it's",  
            "score" : 1  
        }]  
    },  
    {  
        "offset" : 25,  
        "token" : "john",  
        "type" : "UnknownToken",  
        "suggestions" : [{  
            "suggestion" : "John",  
            "score" : 1  
        }]  
    },  
    {  
        "offset" : 19,  
        "token" : "turn",  
        "type" : "RepeatedToken",  
        "suggestions" : [{  
            "suggestion" : "",  
            "score" : 1  
        }]  
    },  
    {  
        "offset" : 35,  
        "token" : "runing",  
        "type" : "UnknownToken",  
        "suggestions" : [{  
            "suggestion" : "running",  
            "score" : 1  
        }]  
    }]  
}  
```  
  
Het veld [flaggedTokens](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference#flaggedtokens) bevat de spel- en grammaticafouten die door de API zijn gevonden in de [tekst](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference#text)reeks. Het veld `token` bevat het woord dat moet worden vervangen. Gebruik nu de op nul gebaseerde offset in het veld `offset` om de token te zoeken in de `text`-tekenreeks. Vervang vervolgens het woord op die locatie door het woord in het veld `suggestion`. 

Als het veld `type` RepeatedToken is, vervangt u het token nog steeds door `suggestion`, maar moet u waarschijnlijk ook de afsluitende spatie verwijderen.

## <a name="throttling-requests"></a>Aanvraagbeperkingen

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>Volgende stappen

- [Wat is de Bing Spellingcontrole-API?](../overview.md)
- [Naslag informatie over Bing Spellingcontrole-API V7](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference)