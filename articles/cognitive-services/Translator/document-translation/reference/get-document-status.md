---
title: Methode document vertaling document status ophalen
titleSuffix: Azure Cognitive Services
description: Met de methode document ophalen wordt de status van een specifiek document geretourneerd.
services: cognitive-services
author: jann-skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 03/25/2021
ms.author: v-jansk
ms.openlocfilehash: 79bc3d076c1a7e164cab9c3231b29be84370e04a
ms.sourcegitcommit: c94e282a08fcaa36c4e498771b6004f0bfe8fb70
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105613061"
---
# <a name="document-translation-get-document-status"></a>Document vertaling: document status ophalen

Met de methode document ophalen wordt de status van een specifiek document geretourneerd. De methode retourneert de Vertaal status voor een specifiek document op basis van de aanvraag-ID en document-ID.

## <a name="request-url"></a>Aanvraag-URL

Een `GET` aanvraag verzenden naar:
```HTTP
GET https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1/batches/{id}/documents/{documentId}
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
|documentId|Waar|De document-ID.|
|id|Waar|De batch-ID.|
## <a name="request-headers"></a>Aanvraagheaders

Aanvraag headers zijn:

|Kopteksten|Beschrijving|
|--- |--- |
|Ocp-Apim-Subscription-Key|Header vereiste aanvraag|

## <a name="response-status-codes"></a>Antwoord status codes

Hier volgen de mogelijke HTTP-status codes die een aanvraag retourneert.

|Statuscode|Description|
|--- |--- |
|200|OK. De aanvraag is voltooid en deze wordt geaccepteerd door de service. De bewerkings Details worden geretourneerd. HeadersRetry-After: integerETag: String|
|401|Gasten. Controleer uw referenties.|
|404|Niet gevonden. De resource is niet gevonden.|
|500|Interne server fout.|
|Andere status codes|<ul><li>Te veel aanvragen</li><li>Tijdelijke server niet beschikbaar</li></ul>|

## <a name="get-document-status-response"></a>Reactie van document status ophalen

### <a name="successful-get-document-status-response"></a>Ophalen van document status is geslaagd

|Naam|Type|Description|
|--- |--- |--- |
|leertraject|tekenreeks|De locatie van het document of de map.|
|createdDateTimeUtc|tekenreeks|Datum/tijd waarop bewerking is gemaakt.|
|lastActionDateTimeUtc|tekenreeks|Datum en tijd waarop de status van de bewerking is bijgewerkt.|
|status|Tekenreeks|Lijst met mogelijke statussen voor taak of document: <ul><li>Geannuleerd</li><li>Annuleren</li><li>Mislukt</li><li>NotStarted</li><li>Wordt uitgevoerd</li><li>Geslaagd</li><li>ValidationFailed</li></ul>|
|tot|tekenreeks|Taal code voor twee letters van op taal. Bekijk de lijst met talen.|
|progress|getal|Voortgang van de vertaling, indien beschikbaar|
|id|tekenreeks|Document-ID.|
|characterCharged|geheel getal|Tekens die door de API in rekening worden gebracht.|

### <a name="error-response"></a>Fout bericht

|Naam|Type|Description|
|--- |--- |--- |
|code|tekenreeks|Opsommingen met fout codes op hoog niveau. Mogelijke waarden:<br/><ul><li>InternalServerError</li><li>InvalidArgument</li><li>InvalidRequest</li><li>RequestRateTooHigh</li><li>ResourceNotFound</li><li>ServiceUnavailable</li><li>Niet geautoriseerd</li></ul>|
|message|tekenreeks|Hiermee wordt het fout bericht op hoog niveau opgehaald.|
|innerError|InnerErrorV2|De nieuwe interne fout indeling, die voldoet aan Cognitive Services API-richt lijnen. Het bevat de vereiste eigenschappen Error code, Message en optionele eigenschappen target, Details (sleutel waarde-paar), interne fout (kan worden genest).|
|innerError. code|tekenreeks|Hiermee wordt de fout teken reeks van code opgehaald.|
|innerError. Message|tekenreeks|Hiermee wordt het fout bericht op hoog niveau opgehaald.|

## <a name="examples"></a>Voorbeelden

### <a name="example-successful-response"></a>Voor beeld geslaagde reactie
Het volgende JSON-object is een voor beeld van een geslaagd antwoord.

```JSON
{
  "path": "https://myblob.blob.core.windows.net/destinationContainer/fr/mydoc.txt",
  "createdDateTimeUtc": "2020-03-26T00:00:00Z",
  "lastActionDateTimeUtc": "2020-03-26T01:00:00Z",
  "status": "Running",
  "to": "fr",
  "progress": 0.1,
  "id": "273622bd-835c-4946-9798-fd8f19f6bbf2",
  "characterCharged": 0
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
