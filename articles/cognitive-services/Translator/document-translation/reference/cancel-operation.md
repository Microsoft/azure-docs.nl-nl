---
title: Methode voor annuleren van document vertaling
titleSuffix: Azure Cognitive Services
description: De methode Cancel Operations annuleert een huidige verwerking of bewerking in de wachtrij.
services: cognitive-services
author: jann-skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 03/25/2021
ms.author: v-jansk
ms.openlocfilehash: 39730f118dd93a972f238f85ef890f4dc54ca91a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105613067"
---
# <a name="document-translation-cancel-operations"></a>Document vertaling: annulerings bewerkingen

Een momenteel verwerkte of in de wachtrij geplaatste bewerking annuleren. Een bewerking wordt niet geannuleerd als deze al is voltooid of is mislukt of wordt geannuleerd. Er wordt een ongeldige aanvraag geretourneerd. Alle documenten waarvan de vertaling is voltooid, worden niet geannuleerd en worden in rekening gebracht. Indien mogelijk worden alle documenten die in behandeling zijn, geannuleerd.

## <a name="request-url"></a>Aanvraag-URL

Een `DELETE` aanvraag verzenden naar:
```DELETE HTTP
https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1/batches/{id}
```

Meer informatie over hoe u uw [aangepaste domein naam](../get-started-with-document-translation.md#find-your-custom-domain-name)kunt vinden.

> [!IMPORTANT]
>
> * **Voor alle API-aanvragen voor de document Vertaal service is een aangepast domein eindpunt vereist**.
> * U kunt het eind punt dat u hebt gevonden op uw Azure Portal bron _sleutels en de eindpunt_ pagina of het globale Translator-eind punt niet gebruiken `api.cognitive.microsofttranslator.com` om HTTP-aanvragen voor document omzetting te maken.

## <a name="request-parameters"></a>Aanvraag parameters

Aanvraag parameters die zijn door gegeven voor de query reeks zijn:

|Queryparameter|Vereist|Beschrijving|
|-----|-----|-----|
|id|Waar|De bewerking-id.|

## <a name="request-headers"></a>Aanvraagheaders

Aanvraag headers zijn:

|Kopteksten|Beschrijving|
|-----|-----|
|Ocp-Apim-Subscription-Key|Header vereiste aanvraag|

## <a name="response-status-codes"></a>Antwoord status codes

Hier volgen de mogelijke HTTP-status codes die een aanvraag retourneert.

| Statuscode| Description|
|-----|-----|
|200|OK. Annulerings aanvraag is verzonden|
|401|Gasten. Controleer uw referenties.|
|404|Niet gevonden. De resource is niet gevonden. 
|500|Interne server fout.
|Andere status codes|<ul><li>Te veel aanvragen</li><li>Tijdelijke server niet beschikbaar</li></ul>|

## <a name="cancel-operations-response"></a>Reactie op bewerkingen annuleren

### <a name="successful-response"></a>Geslaagde reactie

De volgende informatie wordt geretourneerd in een geslaagde reactie.

|Naam|Type|Beschrijving|
|--- |--- |--- |
|id|tekenreeks|De ID van de bewerking.|
|createdDateTimeUtc|tekenreeks|Datum/tijd waarop bewerking is gemaakt.|
|lastActionDateTimeUtc|tekenreeks|Datum en tijd waarop de status van de bewerking is bijgewerkt.|
|status|Tekenreeks|Lijst met mogelijke statussen voor taak of document: <ul><li>Geannuleerd</li><li>Annuleren</li><li>Mislukt</li><li>NotStarted</li><li>Wordt uitgevoerd</li><li>Geslaagd</li><li>ValidationFailed</li></ul>|
|samenvatting|StatusSummary|Samen vatting met de hieronder vermelde Details.|
|samen vatting. totaal|geheel getal|Aantal van het totale aantal documenten.|
|samen vatting. mislukt|geheel getal|Het aantal mislukte documenten.|
|samen vatting. geslaagd|geheel getal|Het aantal documenten dat is vertaald.|
|samen vatting. InProgress|geheel getal|Aantal documenten dat wordt uitgevoerd.|
|Summary. notYetStarted|geheel getal|Het aantal documenten dat nog niet is begonnen met verwerken.|
|samen vatting. geannuleerd|geheel getal|Het aantal geannuleerde.|
|Summary. totalCharacterCharged|geheel getal|Het totale aantal tekens dat door de API in rekening wordt gebracht.|

### <a name="error-response"></a>Fout bericht

|Naam|Type|Description|
|--- |--- |--- |
|code|tekenreeks|Opsommingen met fout codes op hoog niveau. Mogelijke waarden:<br/><ul><li>InternalServerError</li><li>InvalidArgument</li><li>InvalidRequest</li><li>RequestRateTooHigh</li><li>ResourceNotFound</li><li>ServiceUnavailable</li><li>Niet geautoriseerd</li></ul>|
|message|tekenreeks|Hiermee wordt het fout bericht op hoog niveau opgehaald.|
|stemming|tekenreeks|Hiermee wordt de bron van de fout opgehaald. Het is bijvoorbeeld ' Documents ' of ' document-id ' voor een ongeldig document.|
|innerError|InnerErrorV2|De nieuwe interne fout indeling, die voldoet aan Cognitive Services API-richt lijnen. Het bevat de vereiste eigenschappen Error code, Message en optionele eigenschappen target, Details (sleutel waarde-paar), interne fout (kan worden genest).|
|innerError. code|tekenreeks|Hiermee wordt de fout teken reeks van code opgehaald.|
|wend. Fout. Message|tekenreeks|Hiermee wordt het fout bericht op hoog niveau opgehaald.|

## <a name="examples"></a>Voorbeelden

### <a name="example-successful-response"></a>Voor beeld geslaagde reactie

Het volgende JSON-object is een voor beeld van een geslaagd antwoord.

Status code: 200

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

Status code: 500

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

Volg onze Snelstartgids voor meer informatie over het gebruik van document vertalingen en de client bibliotheek.

> [!div class="nextstepaction"]
> [Aan de slag met document vertalingen](../get-started-with-document-translation.md)
