---
title: Methode ondersteunde documentindelingen krijgen
titleSuffix: Azure Cognitive Services
description: Met de methode ondersteunde documentindelingen opmaken wordt een lijst met ondersteunde documentindelingen weergegeven.
services: cognitive-services
author: jann-skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 04/21/2021
ms.author: v-jansk
ms.openlocfilehash: e47f3363a9e09a3e371c751e0bdd1143cfc72314
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107864897"
---
# <a name="get-supported-document-formats"></a>Ondersteunde documentindelingen krijgen

De methode Ondersteunde documentindelingen krijgen retourneert een lijst met documentindelingen die worden ondersteund door de documentvertalingsservice. De lijst bevat de algemene bestandsextensie en het inhoudstype als u de upload-API gebruikt.

## <a name="request-url"></a>Aanvraag-URL

Verzend een `GET` aanvraag naar:
```HTTP
GET https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1/documents/formats
```

Meer informatie over het vinden van [uw aangepaste domeinnaam](../get-started-with-document-translation.md#find-your-custom-domain-name).

> [!IMPORTANT]
>
> * **Voor alle API-aanvragen voor de documentvertalingsservice is een aangepast domein-eindpunt vereist.**
> * U kunt het eindpunt op de pagina sleutels  en eindpunten van uw Azure Portal-resource, noch het globale translator-eindpunt, , gebruiken om HTTP-aanvragen te maken voor `api.cognitive.microsofttranslator.com` documentvertaling.

## <a name="request-headers"></a>Aanvraagheaders

Aanvraagheaders zijn:

|Kopteksten|Beschrijving|
|-----|-----|
|Ocp-Apim-Subscription-Key|Vereiste aanvraagheader|

## <a name="response-status-codes"></a>Antwoordstatuscodes

Hier volgen de mogelijke HTTP-statuscodes die een aanvraag retourneert.

|Statuscode|Description|
|-----|-----|
|200|OK. Retourneert de lijst met ondersteunde bestandsindelingen voor documenten.|
|500|Interne serverfout.|
|Andere statuscodes|<ul><li>Te veel aanvragen</li><li>Server tijdelijk niet beschikbaar</li></ul>|

## <a name="file-format-response"></a>Antwoord bestandsindeling

### <a name="successful-fileformatlistresult-response"></a>Geslaagd fileFormatListResult-antwoord

De volgende informatie wordt geretourneerd als een geslaagd antwoord.

|Naam|Type|Description|
|--- |--- |--- |
|waarde|FileFormat []|FileFormat[] bevat de hieronder vermelde details.|
|value.format|tekenreeks[]|Ondersteunde inhoudstypen voor deze indeling.|
|value.fileExtensions|tekenreeks[]|Ondersteunde bestandsextensie voor deze indeling.|
|value.contentTypes|tekenreeks[]|Naam van de indeling.|
|value.versions|Tekenreeks[]|Ondersteunde versie.|

### <a name="error-response"></a>Foutbericht

|Naam|Type|Description|
|--- |--- |--- |
 |code|tekenreeks|Enums met foutcodes op hoog niveau. Mogelijke waarden:<ul><li>InternalServerError</li><li>InvalidArgument</li><li>InvalidRequest</li><li>RequestRateTooHigh</li><li>ResourceNotFound</li><li>ServiceUnavailable</li><li>Niet geautoriseerd</li></ul>|
|message|tekenreeks|Haalt een foutbericht op hoog niveau op.|
|innerError|InnerErrorV2|Nieuwe interne foutindeling, die voldoet aan Cognitive Services API-richtlijnen. Het bevat vereiste eigenschappen: ErrorCode, message en optional properties target, details(sleutelwaardepaar), interne fout (dit kan worden genest).|
|innerError.code|tekenreeks|Haalt een codefoutreeks op.|
|innerError.message|tekenreeks|Haalt een foutbericht op hoog niveau op.|

## <a name="examples"></a>Voorbeelden

### <a name="example-successful-response"></a>Voorbeeld van een geslaagd antwoord
Hier volgt een voorbeeld van een geslaagd antwoord.

Statuscode: 200

```JSON
{
  "value": [
    {
      "format": "PlainText",
      "fileExtensions": [
        ".txt"
      ],
      "contentTypes": [
        "text/plain"
      ],
      "versions": []
    },
    {
      "format": "PortableDocumentFormat",
      "fileExtensions": [
        ".pdf"
      ],
      "contentTypes": [
        "application/pdf"
      ],
      "versions": []
    },
    {
      "format": "OpenXmlPresentation",
      "fileExtensions": [
        ".pptx"
      ],
      "contentTypes": [
        "application/vnd.openxmlformats-officedocument.presentationml.presentation"
      ],
      "versions": []
    },
    {
      "format": "OpenXmlSpreadsheet",
      "fileExtensions": [
        ".xlsx"
      ],
      "contentTypes": [
        "application/vnd.openxmlformats-officedocument.spreadsheetml.sheet"
      ],
      "versions": []
    },
    {
      "format": "OutlookMailMessage",
      "fileExtensions": [
        ".msg"
      ],
      "contentTypes": [
        "application/vnd.ms-outlook"
      ],
      "versions": []
    },
    {
      "format": "HtmlFile",
      "fileExtensions": [
        ".html"
      ],
      "contentTypes": [
        "text/html"
      ],
      "versions": []
    },
    {
      "format": "OpenXmlWord",
      "fileExtensions": [
        ".docx"
      ],
      "contentTypes": [
        "application/vnd.openxmlformats-officedocument.wordprocessingml.document"
      ],
      "versions": []
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