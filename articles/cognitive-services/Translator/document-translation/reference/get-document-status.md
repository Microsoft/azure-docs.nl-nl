---
title: Methode documentstatus op halen
titleSuffix: Azure Cognitive Services
description: De methode documentstatus get retourneert de status voor een specifiek document.
services: cognitive-services
author: jann-skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 04/21/2021
ms.author: v-jansk
ms.openlocfilehash: 4c6e82af46a012ad53dfa1cc1db1252ef2c0443e
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107864933"
---
# <a name="get-document-status"></a>Documentstatus op halen

De methode Documentstatus op halen retourneert de status voor een specifiek document. De methode retourneert de vertaalstatus voor een specifiek document op basis van de aanvraag-id en document-id.

## <a name="request-url"></a>Aanvraag-URL

Verzend een `GET` aanvraag naar:
```HTTP
GET https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1/batches/{id}/documents/{documentId}
```

Meer informatie over het vinden [van uw aangepaste domeinnaam](../get-started-with-document-translation.md#find-your-custom-domain-name).

> [!IMPORTANT]
>
> * **Voor alle API-aanvragen voor de documentvertalingsservice is een aangepast domein-eindpunt vereist.**
> * U kunt het eindpunt op de pagina sleutels  en eindpunt van uw Azure Portal-resource, noch het globale translator-eindpunt, , gebruiken om HTTP-aanvragen voor documentvertaling `api.cognitive.microsofttranslator.com` te maken.

## <a name="request-parameters"></a>Aanvraagparameters

Aanvraagparameters die worden doorgegeven aan de queryreeks zijn:

|Queryparameter|Vereist|Beschrijving|
|--- |--- |--- |
|documentId|Waar|De document-id.|
|id|Waar|De batch-id.|
## <a name="request-headers"></a>Aanvraagheaders

Aanvraagheaders zijn:

|Kopteksten|Beschrijving|
|--- |--- |
|Ocp-Apim-Subscription-Key|Vereiste aanvraagheader|

## <a name="response-status-codes"></a>Antwoordstatuscodes

Hier volgen de mogelijke HTTP-statuscodes die een aanvraag retourneert.

|Statuscode|Description|
|--- |--- |
|200|OK. De aanvraag is geslaagd en wordt geaccepteerd door de service. De bewerkingsdetails worden geretourneerd. HeadersRetry-After: integerETag: string|
|401|Onbevoegde. Controleer uw referenties.|
|404|Niet gevonden. De resource is niet gevonden.|
|500|Interne serverfout.|
|Andere statuscodes|<ul><li>Te veel aanvragen</li><li>Server tijdelijk niet beschikbaar</li></ul>|

## <a name="get-document-status-response"></a>Antwoord op documentstatus krijgen

### <a name="successful-get-document-status-response"></a>Antwoord op documentstatus is geslaagd

|Naam|Type|Description|
|--- |--- |--- |
|leertraject|tekenreeks|Locatie van het document of de map.|
|createdDateTimeUtc|tekenreeks|Datum/tijd van bewerking gemaakt.|
|lastActionDateTimeUtc|tekenreeks|De datum waarop de status van de bewerking is bijgewerkt.|
|status|Tekenreeks|Lijst met mogelijke statussen voor een taak of document: <ul><li>Geannuleerd</li><li>Annuleren</li><li>Mislukt</li><li>Niet gestart</li><li>Wordt uitgevoerd</li><li>Geslaagd</li><li>ValidationFailed</li></ul>|
|tot|tekenreeks|Tweeletterig taalcode van Naar taal. Bekijk de lijst met talen.|
|progress|getal|Voortgang van de vertaling, indien beschikbaar|
|id|tekenreeks|Document-id.|
|teken In rekening gebracht|geheel getal|Tekens die in rekening worden gebracht door de API.|

### <a name="error-response"></a>Foutbericht

|Naam|Type|Description|
|--- |--- |--- |
|code|tekenreeks|Enums met foutcodes op hoog niveau. Mogelijke waarden:<br/><ul><li>InternalServerError</li><li>InvalidArgument</li><li>InvalidRequest</li><li>RequestRateTooHigh</li><li>ResourceNotFound</li><li>ServiceUnavailable</li><li>Niet geautoriseerd</li></ul>|
|message|tekenreeks|Haalt een foutbericht op hoog niveau op.|
|innerError|InnerErrorV2|Nieuwe interne foutindeling, die voldoet aan Cognitive Services API-richtlijnen. Het bevat de vereiste eigenschappen ErrorCode, message en optionele eigenschappen target, details(sleutel-waardepaar), interne fout (kan worden genest).|
|innerError.code|tekenreeks|Haalt een codefoutreeks op.|
|innerError.message|tekenreeks|Haalt een foutbericht op hoog niveau op.|

## <a name="examples"></a>Voorbeelden

### <a name="example-successful-response"></a>Voorbeeld van een geslaagd antwoord
Het volgende JSON-object is een voorbeeld van een geslaagd antwoord.

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

### <a name="example-error-response"></a>Voorbeeld van een foutbericht

Het volgende JSON-object is een voorbeeld van een foutbericht. Het schema voor andere foutcodes is hetzelfde.

Statuscode: 401

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

Volg onze quickstart voor meer informatie over het gebruik van Documentvertaling en de clientbibliotheek.

> [!div class="nextstepaction"]
> [Aan de slag met documentvertaling](../get-started-with-document-translation.md)
