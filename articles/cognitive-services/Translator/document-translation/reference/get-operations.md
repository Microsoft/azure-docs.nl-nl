---
title: Get-bewerkingen voor document vertalingen
titleSuffix: Azure Cognitive Services
description: De methode Get Operations retourneert een lijst met verzonden batch-aanvragen en de status voor elke aanvraag.
services: cognitive-services
author: jann-skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 03/25/2021
ms.author: v-jansk
ms.openlocfilehash: c42f3081a831c267c7bc605267b99e2a916ea3d8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105612990"
---
# <a name="document-translation-get-operations"></a>Document vertaling: Get-bewerkingen

De methode Get Operations retourneert een lijst met verzonden batch-aanvragen en de status voor elke aanvraag. Deze lijst bevat alleen batch-aanvragen die zijn ingediend door de gebruiker (op basis van het abonnement). De status voor elke aanvraag wordt gesorteerd op id.

Als het aantal aanvragen de paginerings limiet overschrijdt, wordt paginering aan de server zijde gebruikt. Gepagineerde reacties duiden op een gedeeltelijk resultaat en bevatten een vervolg token in het antwoord. Het ontbreken van een vervolg token betekent dat er geen extra pagina's beschikbaar zijn.

$top-en $skip query parameters kunnen worden gebruikt om een aantal resultaten op te geven dat moet worden geretourneerd en een offset voor de verzameling.

De server voldoet aan de waarden die zijn opgegeven door de client. Clients moeten echter worden voor bereid op het verwerken van antwoorden die een andere pagina grootte bevatten of een vervolg token bevatten.

Als zowel $top als $skip zijn opgenomen, moet de server eerst $skip Toep assen en vervolgens $top in de verzameling. 

> [!NOTE]
> Als de server $top en/of $skip niet kan controleren, moet de server een fout retour neren naar de client die hierover informeert in plaats van de query opties te negeren. Dit vermindert het risico dat de client hypo theses maakt over de gegevens die worden geretourneerd.

## <a name="request-url"></a>Aanvraag-URL

Een `GET` aanvraag verzenden naar:
```HTTP
GET https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1/batches
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
|$skip|Niet waar|De $skip vermeldingen in de verzameling overs Laan. Wanneer zowel $top als $skip worden opgegeven, wordt $skip eerst toegepast.|
|$top|Niet waar|Neem de $top items in de verzameling. Wanneer zowel $top als $skip worden opgegeven, wordt $skip eerst toegepast.|

## <a name="request-headers"></a>Aanvraagheaders

Aanvraag headers zijn:

|Kopteksten|Beschrijving|
|--- |--- |
|Ocp-Apim-Subscription-Key|Header vereiste aanvraag|

## <a name="response-status-codes"></a>Antwoord status codes

Hier volgen de mogelijke HTTP-status codes die een aanvraag retourneert.

|Statuscode|Description|
|--- |--- |
|200|OK. De aanvraag is voltooid en de status van de bewerkingen wordt geretourneerd. HeadersRetry-After: integerETag: String|
|400|Ongeldige aanvraag. Ongeldige aanvraag. Controleer de invoer parameters.|
|401|Gasten. Controleer uw referenties.|
|500|Interne server fout.|
|Andere status codes|<ul><li>Te veel aanvragen</li><li>Tijdelijke server niet beschikbaar</li></ul>|

## <a name="get-operations-response"></a>Respons voor bewerkingen ophalen

### <a name="successful-get-operations-response"></a>Het antwoord van de bewerking is voltooid

De volgende informatie wordt geretourneerd in een geslaagde reactie.

|Naam|Type|Beschrijving|
|--- |--- |--- |
|id|tekenreeks|De ID van de bewerking.|
|createdDateTimeUtc|tekenreeks|Datum/tijd waarop bewerking is gemaakt.|
|lastActionDateTimeUtc|tekenreeks|Datum en tijd waarop de status van de bewerking is bijgewerkt.|
|status|Tekenreeks|Lijst met mogelijke statussen voor taak of document: <ul><li>Geannuleerd</li><li>Annuleren</li><li>Mislukt</li><li>NotStarted</li><li>Wordt uitgevoerd</li><li>Geslaagd</li><li>ValidationFailed</li></ul>|
|samenvatting|StatusSummary[]|Samen vatting met de hieronder vermelde Details.|
|samen vatting. totaal|geheel getal|Aantal van het totale aantal documenten.|
|samen vatting. mislukt|geheel getal|Het aantal mislukte documenten.|
|samen vatting. geslaagd|geheel getal|Het aantal documenten dat is vertaald.|
|samen vatting. InProgress|geheel getal|Aantal documenten dat wordt uitgevoerd.|
|Summary. notYetStarted|geheel getal|Het aantal documenten dat nog niet is begonnen met verwerken.|
|samen vatting. geannuleerd|geheel getal|Het aantal geannuleerde documenten.|
|Summary. totalCharacterCharged|geheel getal|Het totale aantal gefactureerde tekens.|

###<a name="error-response"></a>Fout bericht

|Naam|Type|Description|
|--- |--- |--- |
|code|tekenreeks|Opsommingen met fout codes op hoog niveau. Mogelijke waarden:<br/><ul><li>InternalServerError</li><li>InvalidArgument</li><li>InvalidRequest</li><li>RequestRateTooHigh</li><li>ResourceNotFound</li><li>ServiceUnavailable</li><li>Niet geautoriseerd</li></ul>|
|message|tekenreeks|Hiermee wordt het fout bericht op hoog niveau opgehaald.|
|stemming|tekenreeks|Hiermee wordt de bron van de fout opgehaald. Het is bijvoorbeeld ' documenten ' of ' document-id ' in het geval van een ongeldig document.|
|innerError|InnerErrorV2|De nieuwe interne fout indeling, die voldoet aan Cognitive Services API-richt lijnen. Het bevat de vereiste eigenschappen Error code, Message en optionele eigenschappen target, Details (sleutel waarde-paar), interne fout (dit kan worden genest).|
|innerError. code|tekenreeks|Hiermee wordt de fout teken reeks van code opgehaald.|
|innerError. Message|tekenreeks|Hiermee wordt het fout bericht op hoog niveau opgehaald.|

## <a name="examples"></a>Voorbeelden

### <a name="example-successful-response"></a>Voor beeld geslaagde reactie

Hier volgt een voor beeld van een geslaagde reactie.

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

### <a name="example-error-response"></a>Voorbeeld fout bericht

Hier volgt een voor beeld van een fout bericht. Het schema voor andere fout codes is hetzelfde.

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
