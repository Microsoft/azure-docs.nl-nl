---
title: De status van documenten op halen
titleSuffix: Azure Cognitive Services
description: De get documents status methode retourneert de status voor alle documenten in een batch document vertaalaanvraag.
services: cognitive-services
author: jann-skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 04/21/2021
ms.author: v-jansk
ms.openlocfilehash: 8476c4891cef9d9055b16c7ac574e569ecf5b3f2
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107864860"
---
# <a name="get-documents-status"></a>De status van documenten op halen

De get documents status methode retourneert de status voor alle documenten in een batch document vertaalaanvraag.

De documenten die in het antwoord zijn opgenomen, worden in aflopende volgorde gesorteerd op document-id. Als het aantal documenten in het antwoord onze pagineringslimiet overschrijdt, wordt paginering aan de serverzijde gebruikt. Ge pagineerde antwoorden geven een gedeeltelijk resultaat aan en bevatten een vervolg-token in het antwoord. Het ontbreken van een vervolg-token betekent dat er geen extra pagina's beschikbaar zijn.

$top en $skip queryparameters kunnen worden gebruikt om een aantal te retourneren resultaten en een offset voor de verzameling op te geven. De server respecteert de waarden die zijn opgegeven door de client. Clients moeten echter worden voorbereid op het verwerken van antwoorden die een ander paginaformaat of een vervolg-token bevatten.

Wanneer zowel $top als $skip zijn opgenomen, moet de server eerst $skip toepassen en vervolgens $top op de verzameling.

> [!NOTE]
> Als de server geen rekening kan houden met $top en/of $skip, moet de server een fout retourneren aan de client die erover informeert in plaats van alleen de queryopties te negeren. Dit vermindert het risico dat de client veronderstellingen maakt over de geretourneerde gegevens.

## <a name="request-url"></a>Aanvraag-URL

Verzend een `GET` aanvraag naar:
```HTTP
GET https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1/batches/{id}/documents
```

Meer informatie over het vinden van [uw aangepaste domeinnaam](../get-started-with-document-translation.md#find-your-custom-domain-name).

> [!IMPORTANT]
>
> * **Voor alle API-aanvragen voor de documentvertalingsservice is een aangepast domein-eindpunt vereist.**
> * U kunt het eindpunt op de pagina sleutels  en eindpunten van uw Azure Portal-resource, noch het globale translator-eindpunt, , gebruiken om HTTP-aanvragen te maken voor `api.cognitive.microsofttranslator.com` documentvertaling.

## <a name="request-parameters"></a>Aanvraagparameters

Aanvraagparameters die worden doorgegeven aan de queryreeks zijn:

|Queryparameter|Vereist|Beschrijving|
|--- |--- |--- |
|id|Waar|De bewerkings-id.|
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
|200|OK. Geslaagde aanvraag en retourneert de status van de documenten. HeadersRetry-After: integerETag: string|
|400|Ongeldige aanvraag. Controleer de invoerparameters.|
|401|Onbevoegde. Controleer uw referenties.|
|404|De resource is niet gevonden.|
|500|Interne serverfout.|
|Andere statuscodes|<ul><li>Te veel aanvragen</li><li>Server tijdelijk niet beschikbaar</li></ul>|


## <a name="get-documents-status-response"></a>Statusreactie van documenten op halen

### <a name="successful-get-documents-status-response"></a>Statusreactie van documenten is geslaagd

De volgende informatie wordt geretourneerd als een geslaagd antwoord.

|Naam|Type|Description|
|--- |--- |--- |
|@nextLink|tekenreeks|URL voor de volgende pagina. Null als er geen pagina's meer beschikbaar zijn.|
|waarde|DocumentStatusDetail []|De detailstatus van afzonderlijke documenten die hieronder worden vermeld.|
|value.path|tekenreeks|Locatie van het document of de map.|
|value.createdDateTimeUtc|tekenreeks|Datum/tijd van bewerking gemaakt.|
|value.lastActionDateTimeUt|tekenreeks|De datum waarop de status van de bewerking is bijgewerkt.|
|value.status|status|Lijst met mogelijke statussen voor een taak of document.<ul><li>Geannuleerd</li><li>Annuleren</li><li>Mislukt</li><li>Niet gestart</li><li>Wordt uitgevoerd</li><li>Geslaagd</li><li>ValidationFailed</li></ul>|
|value.to|tekenreeks|Naar taal.|
|value.progress|tekenreeks|Voortgang van de vertaling, indien beschikbaar.|
|value.id|tekenreeks|Document-id.|
|value.characterCharged|geheel getal|Tekens die in rekening worden gebracht door de API.|

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
      "path": "https://myblob.blob.core.windows.net/destinationContainer/fr/mydoc.txt",
      "createdDateTimeUtc": "2020-03-26T00:00:00Z",
      "lastActionDateTimeUtc": "2020-03-26T01:00:00Z",
      "status": "Running",
      "to": "fr",
      "progress": 0.1,
      "id": "273622bd-835c-4946-9798-fd8f19f6bbf2",
      "characterCharged": 0
    }
  ],
  "@nextLink": "https://westus.cognitiveservices.azure.com/translator/text/batch/v1.0.preview.1/operation/0FA2822F-4C2A-4317-9C20-658C801E0E55/documents?$top=5&$skip=15"
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
