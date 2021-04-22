---
title: De status van vertalingen op halen
titleSuffix: Azure Cognitive Services
description: De methode get translations status retourneert een lijst met verzonden batchaanvragen en de status voor elke aanvraag.
services: cognitive-services
author: jann-skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 04/21/2021
ms.author: v-jansk
ms.openlocfilehash: c3301283f0a7334a7c207ff7c80b4f71a13de465
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107864825"
---
# <a name="get-translations-status"></a>De status van vertalingen op halen

De methode Get translations status retourneert een lijst met verzonden batchaanvragen en de status voor elke aanvraag. Deze lijst bevat alleen batchaanvragen die door de gebruiker zijn ingediend (op basis van het abonnement). De status voor elke aanvraag wordt gesorteerd op id.

Als het aantal aanvragen de pagineringslimiet overschrijdt, wordt paginering aan de serverzijde gebruikt. Ge pagineerde antwoorden geven een gedeeltelijk resultaat aan en bevatten een vervolg-token in het antwoord. Het ontbreken van een vervolg-token betekent dat er geen extra pagina's beschikbaar zijn.

$top en $skip queryparameters kunnen worden gebruikt om een aantal te retourneren resultaten en een offset voor de verzameling op te geven.

De server respecteert de waarden die zijn opgegeven door de client. Clients moeten echter worden voorbereid op het verwerken van antwoorden die een ander paginaformaat of een vervolg-token bevatten.

Wanneer zowel $top als $skip zijn opgenomen, moet de server eerst $skip toepassen en vervolgens $top op de verzameling. 

> [!NOTE]
> Als de server geen rekening kan houden met $top en/of $skip, moet de server een fout retourneren aan de client die erover informeert in plaats van alleen de queryopties te negeren. Dit vermindert het risico dat de client veronderstellingen maakt over de geretourneerde gegevens.

## <a name="request-url"></a>Aanvraag-URL

Verzend een `GET` aanvraag naar:
```HTTP
GET https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1/batches
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
|$skip|Niet waar|Sla de $skip in de verzameling over. Wanneer zowel $top als $skip worden opgegeven, $skip eerst toegepast.|
|$top|Niet waar|Neem de $top in de verzameling. Wanneer zowel $top als $skip worden opgegeven, $skip eerst toegepast.|

## <a name="request-headers"></a>Aanvraagheaders

Aanvraagheaders zijn:

|Kopteksten|Beschrijving|
|--- |--- |
|Ocp-Apim-Subscription-Key|Vereiste aanvraagheader|

## <a name="response-status-codes"></a>Antwoordstatuscodes

Hier volgen de mogelijke HTTP-statuscodes die een aanvraag retourneert.

|Statuscode|Description|
|--- |--- |
|200|OK. Geslaagde aanvraag en retourneert de status van alle bewerkingen. HeadersRetry-After: integerETag: string|
|400|Slechte aanvraag. Ongeldige aanvraag. Controleer de invoerparameters.|
|401|Onbevoegde. Controleer uw referenties.|
|500|Interne serverfout.|
|Andere statuscodes|<ul><li>Te veel aanvragen</li><li>Server tijdelijk niet beschikbaar</li></ul>|

## <a name="get-translations-status-response"></a>Antwoord van de status van vertalingen op halen

### <a name="successful-get-translations-status-response"></a>Geslaagd antwoord op de status van de vertaling

De volgende informatie wordt geretourneerd als een geslaagd antwoord.

|Naam|Type|Beschrijving|
|--- |--- |--- |
|id|tekenreeks|Id van de bewerking.|
|createdDateTimeUtc|tekenreeks|Bewerking datum/tijd gemaakt.|
|lastActionDateTimeUtc|tekenreeks|De datum waarop de status van de bewerking is bijgewerkt.|
|status|Tekenreeks|Lijst met mogelijke statussen voor een taak of document: <ul><li>Geannuleerd</li><li>Annuleren</li><li>Mislukt</li><li>Niet gestart</li><li>Wordt uitgevoerd</li><li>Geslaagd</li><li>ValidationFailed</li></ul>|
|samenvatting|StatusSummary[]|Samenvatting met de details die hieronder worden vermeld.|
|summary.total|geheel getal|Telling van het totale aantal documenten.|
|summary.failed|geheel getal|Het aantal documenten is mislukt.|
|summary.success|geheel getal|Aantal documenten dat is vertaald.|
|summary.inProgress|geheel getal|Aantal documenten dat wordt uitgevoerd.|
|summary.notYetStarted|geheel getal|Aantal documenten dat nog niet is verwerkt.|
|summary.cancelled|geheel getal|Aantal geannuleerde documenten.|
|summary.totalCharacterCharged|geheel getal|Totaal aantal tekens in rekening gebracht.|

### <a name="error-response"></a>Foutbericht

|Naam|Type|Description|
|--- |--- |--- |
|code|tekenreeks|Enums met foutcodes op hoog niveau. Mogelijke waarden:<br/><ul><li>InternalServerError</li><li>InvalidArgument</li><li>InvalidRequest</li><li>RequestRateTooHigh</li><li>ResourceNotFound</li><li>ServiceUnavailable</li><li>Niet geautoriseerd</li></ul>|
|message|tekenreeks|Haalt een foutbericht op hoog niveau op.|
|Doel|tekenreeks|Haalt de bron van de fout op. Dit zijn bijvoorbeeld 'documenten' of 'document-id' in het geval van een ongeldig document.|
|innerError|InnerErrorV2|Nieuwe interne foutindeling, die voldoet aan Cognitive Services API-richtlijnen. Het bevat vereiste eigenschappen: ErrorCode, message en optional properties target, details(sleutelwaardepaar), interne fout (dit kan worden genest).|
|innerError.code|tekenreeks|Haalt een codefoutreeks op.|
|innerError.message|tekenreeks|Haalt een foutbericht op hoog niveau op.|

## <a name="examples"></a>Voorbeelden

### <a name="example-successful-response"></a>Voorbeeld van een geslaagd antwoord

Hier volgt een voorbeeld van een geslaagd antwoord.

```JSON
{
  "value": [
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
  ]
}
```

### <a name="example-error-response"></a>Voorbeeld van een foutbericht

Hier volgt een voorbeeld van een foutbericht. Het schema voor andere foutcodes is hetzelfde.

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
