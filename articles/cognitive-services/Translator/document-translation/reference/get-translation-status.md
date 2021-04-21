---
title: Vertaalstatus op halen
titleSuffix: Azure Cognitive Services
description: De methode get translation status retourneert de status voor een aanvraag voor documentvertaling.
services: cognitive-services
author: jann-skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 03/25/2021
ms.author: v-jansk
ms.openlocfilehash: 8b129974396e420948737c9bdf47a5707decab6b
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107836165"
---
# <a name="get-translation-status"></a>Vertaalstatus op halen

De methode Get translation status retourneert de status voor een documentvertalingsaanvraag. De status bevat de algehele aanvraagstatus en de status voor documenten die worden vertaald als onderdeel van die aanvraag.

## <a name="request-url"></a>Aanvraag-URL

Verzend een `GET` aanvraag naar:
```HTTP
GET https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1/batches/{id}
```

Meer informatie over het vinden van [uw aangepaste domeinnaam](../get-started-with-document-translation.md#find-your-custom-domain-name).

> [!IMPORTANT]
>
> * **Voor alle API-aanvragen voor de documentvertalingsservice is een aangepast domein-eindpunt vereist.**
> * U kunt het eindpunt dat u op uw  Azure Portal resourcesleutels en eindpuntpagina hebt gevonden, noch het globale translator-eindpunt gebruiken om HTTP-aanvragen te maken voor `api.cognitive.microsofttranslator.com` documentvertaling.


## <a name="request-parameters"></a>Aanvraagparameters

Aanvraagparameters die worden doorgegeven aan de queryreeks zijn:

|Queryparameter|Vereist|Beschrijving|
|--- |--- |--- |
|id|Waar|De bewerkings-id.|

## <a name="request-headers"></a>Aanvraagheaders

Aanvraagheaders zijn:

|Kopteksten|Beschrijving|
|--- |--- |
|Ocp-Apim-Subscription-Key|Vereiste aanvraagheader|

## <a name="response-status-codes"></a>Antwoordstatuscodes

Hier volgen de mogelijke HTTP-statuscodes die een aanvraag retourneert.

|Statuscode|Description|
|--- |--- |
|200|OK. Geslaagde aanvraag en retourneert de status van de batchvertalingsbewerking. HeadersRetry-After: integerETag: string|
|401|Onbevoegde. Controleer uw referenties.|
|404|De resource is niet gevonden.|
|500|Interne serverfout.|
|Andere statuscodes|<ul><li>Te veel aanvragen</li><li>Server tijdelijk niet beschikbaar</li></ul>|

## <a name="get-translation-status-response"></a>Antwoord van vertaalstatus op halen

### <a name="successful-get-translation-status-response"></a>Geslaagd antwoord op vertaalstatus

De volgende informatie wordt geretourneerd als een geslaagd antwoord.

|Naam|Type|Beschrijving|
|--- |--- |--- |
|id|tekenreeks|Id van de bewerking.|
|createdDateTimeUtc|tekenreeks|Bewerking datum/tijd gemaakt.|
|lastActionDateTimeUtc|tekenreeks|De datum waarop de status van de bewerking is bijgewerkt.|
|status|Tekenreeks|Lijst met mogelijke statussen voor een taak of document: <ul><li>Geannuleerd</li><li>Annuleren</li><li>Mislukt</li><li>Niet gestart</li><li>Wordt uitgevoerd</li><li>Geslaagd</li><li>ValidationFailed</li></ul>|
|samenvatting|StatusSummary|Samenvatting met de details die hieronder worden vermeld.|
|summary.total|geheel getal|Totaalaantal.|
|summary.failed|geheel getal|Aantal mislukte pogingen.|
|summary.success|geheel getal|Aantal geslaagde.|
|summary.inProgress|geheel getal|Het aantal wordt uitgevoerd.|
|summary.notYetStarted|geheel getal|Aantal van nog niet gestart.|
|summary.cancelled|geheel getal|Aantal geannuleerd.|
|summary.totalCharacterCharged|geheel getal|Totaal aantal tekens dat in rekening wordt gebracht door de API.|

###<a name="error-response"></a>Foutbericht

|Naam|Type|Description|
|--- |--- |--- |
|code|tekenreeks|Enums met foutcodes op hoog niveau. Mogelijke waarden:<br/><ul><li>InternalServerError</li><li>InvalidArgument</li><li>InvalidRequest</li><li>RequestRateTooHigh</li><li>ResourceNotFound</li><li>ServiceUnavailable</li><li>Niet geautoriseerd</li></ul>|
|message|tekenreeks|Haalt een foutbericht op hoog niveau op.|
|Doel|tekenreeks|Haalt de bron van de fout op. Dit zijn bijvoorbeeld 'documenten' of 'document-id' voor een ongeldig document.|
|innerError|InnerErrorV2|Nieuwe interne foutindeling, die voldoet aan Cognitive Services API-richtlijnen. Het bevat de vereiste eigenschappen ErrorCode, message en optionele eigenschappen target, details(sleutel-waardepaar), interne fout (kan worden genest).|
|innerError.code|tekenreeks|Haalt een codefoutreeks op.|
|innerError.message|tekenreeks|Haalt een foutbericht op hoog niveau op.|

## <a name="examples"></a>Voorbeelden

### <a name="example-successful-response"></a>Voorbeeld van een geslaagd antwoord

Het volgende JSON-object is een voorbeeld van een geslaagd antwoord.

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
