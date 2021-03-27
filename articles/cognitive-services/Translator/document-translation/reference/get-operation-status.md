---
title: Status van Get-bewerking document vertaling
titleSuffix: Azure Cognitive Services
description: De methode Get Operation status retourneert de status voor een aanvraag voor document vertaling.
services: cognitive-services
author: jann-skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 03/25/2021
ms.author: v-jansk
ms.openlocfilehash: e8aa9b93dc089cc05dd5157a7ac84cceb5f6fcd5
ms.sourcegitcommit: c94e282a08fcaa36c4e498771b6004f0bfe8fb70
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105612989"
---
# <a name="document-translation-get-operation-status"></a>Document vertaling: bewerkings status ophalen

De methode voor het ophalen van bewerkings document status retourneert de status voor een aanvraag voor document vertaling. De status bevat de algemene aanvraag status en de status van documenten die worden vertaald als onderdeel van deze aanvraag.

## <a name="request-url"></a>Aanvraag-URL

Een `GET` aanvraag verzenden naar:
```HTTP
GET https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1/batches/{id}
```

Meer informatie over hoe u uw [aangepaste domein naam](../get-started-with-document-translation.md#find-your-custom-domain-name)kunt vinden.

> [!IMPORTANT]
>
> * **Voor alle API-aanvragen voor de document Vertaal service is een aangepast domein eindpunt vereist**.
> * U kunt het eind punt dat u hebt gevonden op uw Azure Portal bron _sleutels en de eindpunt_ pagina of het globale Translator-eind punt niet gebruiken `api.cognitive.microsofttranslator.com` om HTTP-aanvragen voor document omzetting te maken.


## <a name="request-parameters"></a>Aanvraag parameters

Aanvraag parameters die zijn door gegeven voor de query reeks zijn:

|Queryparameter|Vereist|Beschrijving|
|--- |--- |--- |
|id|Waar|De bewerkings-ID.|

## <a name="request-headers"></a>Aanvraagheaders

Aanvraag headers zijn:

|Kopteksten|Beschrijving|
|--- |--- |
|Ocp-Apim-Subscription-Key|Header vereiste aanvraag|

## <a name="response-status-codes"></a>Antwoord status codes

Hier volgen de mogelijke HTTP-status codes die een aanvraag retourneert.

|Statuscode|Description|
|--- |--- |
|200|OK. De aanvraag is voltooid en retourneert de status van de batch omzettings bewerking. HeadersRetry-After: integerETag: String|
|401|Gasten. Controleer uw referenties.|
|404|De resource is niet gevonden.|
|500|Interne server fout.|
|Andere status codes|<ul><li>Te veel aanvragen</li><li>Tijdelijke server niet beschikbaar</li></ul>|

## <a name="get-operation-status-response"></a>Reactie van bewerkings status ophalen

### <a name="successful-get-operation-status-response"></a>Het antwoord van de bewerkings status is opgehaald

De volgende informatie wordt geretourneerd in een geslaagde reactie.

|Naam|Type|Beschrijving|
|--- |--- |--- |
|id|tekenreeks|De ID van de bewerking.|
|createdDateTimeUtc|tekenreeks|Datum/tijd waarop bewerking is gemaakt.|
|lastActionDateTimeUtc|tekenreeks|Datum en tijd waarop de status van de bewerking is bijgewerkt.|
|status|Tekenreeks|Lijst met mogelijke statussen voor taak of document: <ul><li>Geannuleerd</li><li>Annuleren</li><li>Mislukt</li><li>NotStarted</li><li>Wordt uitgevoerd</li><li>Geslaagd</li><li>ValidationFailed</li></ul>|
|samenvatting|StatusSummary|Samen vatting met de hieronder vermelde Details.|
|samen vatting. totaal|geheel getal|Totaalaantal.|
|samen vatting. mislukt|geheel getal|Aantal mislukte pogingen.|
|samen vatting. geslaagd|geheel getal|Aantal geslaagde.|
|samen vatting. InProgress|geheel getal|Het aantal wordt uitgevoerd.|
|Summary. notYetStarted|geheel getal|Het aantal is nog niet gestart.|
|samen vatting. geannuleerd|geheel getal|Het aantal geannuleerde.|
|Summary. totalCharacterCharged|geheel getal|Het totale aantal tekens dat door de API in rekening wordt gebracht.|

###<a name="error-response"></a>Fout bericht

|Naam|Type|Description|
|--- |--- |--- |
|code|tekenreeks|Opsommingen met fout codes op hoog niveau. Mogelijke waarden:<br/><ul><li>InternalServerError</li><li>InvalidArgument</li><li>InvalidRequest</li><li>RequestRateTooHigh</li><li>ResourceNotFound</li><li>ServiceUnavailable</li><li>Niet geautoriseerd</li></ul>|
|message|tekenreeks|Hiermee wordt het fout bericht op hoog niveau opgehaald.|
|stemming|tekenreeks|Hiermee wordt de bron van de fout opgehaald. Het is bijvoorbeeld ' Documents ' of ' document-id ' voor een ongeldig document.|
|innerError|InnerErrorV2|De nieuwe interne fout indeling, die voldoet aan Cognitive Services API-richt lijnen. Het bevat de vereiste eigenschappen Error code, Message en optionele eigenschappen target, Details (sleutel waarde-paar), interne fout (kan worden genest).|
|innerError. code|tekenreeks|Hiermee wordt de fout teken reeks van code opgehaald.|
|innerError. Message|tekenreeks|Hiermee wordt het fout bericht op hoog niveau opgehaald.|

## <a name="examples"></a>Voorbeelden

### <a name="example-successful-response"></a>Voor beeld geslaagde reactie

Het volgende JSON-object is een voor beeld van een geslaagd antwoord.

```JSON
{
  "id": "727bf148-f327-47a0-9481-abae6362f11e",
  "createdDateTimeUtc": "2020-03-26T00:00:00Z",
  "lastActionDateTimeUtc": "2020-03-26T01:00:00Z",
  "status": "Succeeded",
  "summary": {
    "total": 10,
    "failed": 1,
    "success": 9,
    "inProgress": 0,
    "notYetStarted": 0,
    "cancelled": 0,
    "totalCharacterCharged": 0
  }
}
```

### <a name="example-error-response"></a>Voorbeeld fout bericht

Het volgende JSON-object is een voor beeld van een fout bericht. Het schema voor andere fout codes is hetzelfde.

Status code: 401

```JSON
{
  "error": {
    "code": "Unauthorized",
    "message": "User is not authorized",
    "target": "Document",
    "innerError": {
      "code": "Unauthorized",
      "message": "Operation is not authorized"
    }
  }
}
```

## <a name="next-steps"></a>Volgende stappen

Volg onze Snelstartgids voor meer informatie over het gebruik van document vertalingen en de client bibliotheek.

> [!div class="nextstepaction"]
> [Aan de slag met document vertalingen](../get-started-with-document-translation.md)
