---
title: Opslag bron methode document vertaling ophalen
titleSuffix: Azure Cognitive Services
description: Met de methode document opslag ophalen wordt een lijst met ondersteunde opslag bronnen geretourneerd.
services: cognitive-services
author: jann-skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 03/25/2021
ms.author: v-jansk
ms.openlocfilehash: d6d8b1d08bbef1c37cbf0583022428037808580d
ms.sourcegitcommit: c94e282a08fcaa36c4e498771b6004f0bfe8fb70
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105613058"
---
# <a name="document-translation-get-document-storage-source"></a>Document vertaling: opslag bron document ophalen

De methode document opslag ophalen bron retourneert een lijst met opslag bronnen/-opties die worden ondersteund door de service voor document vertalingen.

## <a name="request-url"></a>Aanvraag-URL

Een `GET` aanvraag verzenden naar:
```HTTP
GET https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1/storagesources
```

Meer informatie over hoe u uw [aangepaste domein naam](../get-started-with-document-translation.md#find-your-custom-domain-name)kunt vinden.

> [!IMPORTANT]
>
> * **Voor alle API-aanvragen voor de document Vertaal service is een aangepast domein eindpunt vereist**.
> * U kunt het eind punt dat u hebt gevonden op uw Azure Portal bron _sleutels en de eindpunt_ pagina of het globale Translator-eind punt niet gebruiken `api.cognitive.microsofttranslator.com` om HTTP-aanvragen voor document omzetting te maken.

## <a name="request-headers"></a>Aanvraagheaders

Aanvraag headers zijn:

|Kopteksten|Beschrijving|
|--- |--- |
|Ocp-Apim-Subscription-Key|Header vereiste aanvraag|

## <a name="response-status-codes"></a>Antwoord status codes

Hier volgen de mogelijke HTTP-status codes die een aanvraag retourneert.

|Statuscode|Description|
|--- |--- |
|200|OK. De aanvraag is voltooid en er wordt een lijst met opslag bronnen geretourneerd.|
|500|Interne server fout.|
|Andere status codes|<ul><li>Te veel aanvragen</li><li>Tijdelijke server niet beschikbaar</li></ul>|

## <a name="get-document-storage-source-response"></a>Antwoord op document opslag bron ophalen

### <a name="successful-get-document-storage-source-response"></a>Het bron antwoord voor document opslag is opgehaald
Basis type voor het retour neren van een lijst in de bron-API voor document opslag ophalen.

|Naam|Type|Description|
|--- |--- |--- |
|waarde|teken reeks []|Lijst met objecten.|


### <a name="error-response"></a>Fout bericht

|Naam|Type|Description|
|--- |--- |--- |
|code|tekenreeks|Opsommingen met fout codes op hoog niveau. Mogelijke waarden:<br/><ul><li>InternalServerError</li><li>InvalidArgument</li><li>InvalidRequest</li><li>RequestRateTooHigh</li><li>ResourceNotFound</li><li>ServiceUnavailable</li><li>Niet geautoriseerd</li></ul>|
|message|tekenreeks|Hiermee wordt het fout bericht op hoog niveau opgehaald.|
|innerError|InnerErrorV2|De nieuwe interne fout indeling, die voldoet aan Cognitive Services API-richt lijnen. Het bevat de vereiste eigenschappen Error code, Message en optionele eigenschappen target, Details (sleutel waarde-paar), interne fout (dit kan worden genest).|
|innerError. code|tekenreeks|Hiermee wordt de fout teken reeks van code opgehaald.|
|innerError. Message|tekenreeks|Hiermee wordt het fout bericht op hoog niveau opgehaald.|

## <a name="examples"></a>Voorbeelden

### <a name="example-successful-response"></a>Voor beeld geslaagde reactie

Hier volgt een voor beeld van een geslaagde reactie.

```JSON
{
  "value": [
    "AzureBlob"
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