---
title: Vertaalmethode annuleren
titleSuffix: Azure Cognitive Services
description: De vertaalmethode annuleren annuleert een bewerking die momenteel wordt verwerkt of in de wachtrij staat.
services: cognitive-services
author: jann-skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 04/21/2021
ms.author: v-jansk
ms.openlocfilehash: e3b7da30f54b9d9468b46a2cd0972a3397e5cdce
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107865102"
---
# <a name="cancel-translation"></a>Vertaling annuleren

Annuleer een bewerking die momenteel wordt verwerkt of in de wachtrij staat. Een bewerking wordt niet geannuleerd als deze al is voltooid, mislukt of geannuleerd. Er wordt een slechte aanvraag geretourneerd. Alle documenten met voltooide vertaling worden niet geannuleerd en worden in rekening gebracht. Alle in behandeling zijnde documenten worden, indien mogelijk, geannuleerd.

## <a name="request-url"></a>Aanvraag-URL

Verzend een `DELETE` aanvraag naar:

```DELETE HTTP
https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1/batches/{id}
```

Meer informatie over het vinden [van uw aangepaste domeinnaam](../get-started-with-document-translation.md#find-your-custom-domain-name).

> [!IMPORTANT]
>
> * **Voor alle API-aanvragen voor de documentvertalingsservice is een aangepast domein-eindpunt vereist.**
> * U kunt het eindpunt op de pagina sleutels  en eindpunt van uw Azure Portal-resource, noch het globale translator-eindpunt, , gebruiken om HTTP-aanvragen voor documentvertaling `api.cognitive.microsofttranslator.com` te maken.

## <a name="request-parameters"></a>Aanvraagparameters

Aanvraagparameters die worden doorgegeven aan de queryreeks zijn:

|Queryparameter|Vereist|Beschrijving|
|-----|-----|-----|
|id|Waar|De operation-id.|

## <a name="request-headers"></a>Aanvraagheaders

Aanvraagheaders zijn:

|Kopteksten|Beschrijving|
|-----|-----|
|Ocp-Apim-Subscription-Key|Vereiste aanvraagheader|

## <a name="response-status-codes"></a>Antwoordstatuscodes

Hier volgen de mogelijke HTTP-statuscodes die een aanvraag retourneert.

| Statuscode| Description|
|-----|-----|
|200|OK. Aanvraag annuleren is verzonden|
|401|Onbevoegde. Controleer uw referenties.|
|404|Niet gevonden. De resource is niet gevonden. 
|500|Interne serverfout.
|Andere statuscodes|<ul><li>Te veel aanvragen</li><li>Server tijdelijk niet beschikbaar</li></ul>|

## <a name="cancel-translation-response"></a>Vertaalreactie annuleren

### <a name="successful-response"></a>Geslaagd antwoord

De volgende informatie wordt geretourneerd als een geslaagd antwoord.

|Naam|Type|Beschrijving|
|--- |--- |--- |
|id|tekenreeks|Id van de bewerking.|
|createdDateTimeUtc|tekenreeks|Datum/tijd van bewerking gemaakt.|
|lastActionDateTimeUtc|tekenreeks|De datum waarop de status van de bewerking is bijgewerkt.|
|status|Tekenreeks|Lijst met mogelijke statussen voor een taak of document: <ul><li>Geannuleerd</li><li>Annuleren</li><li>Mislukt</li><li>Niet gestart</li><li>Wordt uitgevoerd</li><li>Geslaagd</li><li>ValidationFailed</li></ul>|
|samenvatting|StatusSummary|Samenvatting met de details die hieronder worden vermeld.|
|summary.total|geheel getal|Telling van het totale aantal documenten.|
|summary.failed|geheel getal|Het aantal documenten is mislukt.|
|summary.success|geheel getal|Aantal documenten dat is vertaald.|
|summary.inProgress|geheel getal|Aantal documenten dat wordt uitgevoerd.|
|summary.notYetStarted|geheel getal|Aantal documenten dat nog niet is verwerkt.|
|summary.cancelled|geheel getal|Aantal geannuleerd.|
|summary.totalCharacterCharged|geheel getal|Totaal aantal tekens dat in rekening wordt gebracht door de API.|

### <a name="error-response"></a>Foutbericht

|Naam|Type|Description|
|--- |--- |--- |
|code|tekenreeks|Enums met foutcodes op hoog niveau. Mogelijke waarden:<br/><ul><li>InternalServerError</li><li>InvalidArgument</li><li>InvalidRequest</li><li>RequestRateTooHigh</li><li>ResourceNotFound</li><li>ServiceUnavailable</li><li>Niet geautoriseerd</li></ul>|
|message|tekenreeks|Haalt een foutbericht op hoog niveau op.|
|Doel|tekenreeks|Haalt de bron van de fout op. Dit zijn bijvoorbeeld 'documenten' of 'document-id' voor een ongeldig document.|
|innerError|InnerErrorV2|Nieuwe interne foutindeling, die voldoet aan Cognitive Services API-richtlijnen. Het bevat de vereiste eigenschappen ErrorCode, message en optionele eigenschappen target, details(sleutel-waardepaar), interne fout (kan worden genest).|
|innerError.code|tekenreeks|Haalt de codefoutreeks op.|
|Innerlijke. Eroor.message|tekenreeks|Haalt een foutbericht op hoog niveau op.|

## <a name="examples"></a>Voorbeelden

### <a name="example-successful-response"></a>Voorbeeld van een geslaagd antwoord

Het volgende JSON-object is een voorbeeld van een geslaagd antwoord.

Statuscode: 200

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

Statuscode: 500

```JSON
{
  "error": {
    "code": "InternalServerError",
    "message": "Internal Server Error",
    "target": "Operation",
    "innerError": {
      "code": "InternalServerError",
      "message": "Unexpected internal server error has occurred"
    }
  }
}
```

## <a name="next-steps"></a>Volgende stappen

Volg onze quickstart voor meer informatie over het gebruik van Documentvertaling en de clientbibliotheek.

> [!div class="nextstepaction"]
> [Aan de slag met documentvertaling](../get-started-with-document-translation.md)
