---
title: Cognitieve vaardigheid van document extractie
titleSuffix: Azure Cognitive Search
description: Extraheert inhoud uit een bestand binnen de verrijkings pijplijn. Deze vaardigheid is momenteel in openbare preview.
manager: nitinme
author: careyjmac
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/17/2020
ms.author: chalton
ms.openlocfilehash: 144e8058e640f98dc6b0ef60534405525532b00e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102547863"
---
# <a name="document-extraction-cognitive-skill"></a>Cognitieve vaardigheid van document extractie

De vaardigheid van **document extractie** extraheert inhoud uit een bestand binnen de verrijkings pijplijn. Zo kunt u gebruikmaken van de stap voor document extractie die normaal gesp roken plaatsvindt voordat de vaardig heden-uitvoering wordt uitgevoerd op bestanden die door andere vaardig heden kunnen worden gegenereerd.

> [!NOTE]
> Als u het bereik uitbreidt door de verwerkings frequentie te verhogen, meer documenten toe te voegen of meer AI-algoritmen toe te voegen, moet u [een factureer bare Cognitive Services resource koppelen](cognitive-search-attach-cognitive-services.md). Er worden kosten in rekening gebracht bij het aanroepen van Api's in Cognitive Services en voor het ophalen van afbeeldingen als onderdeel van de fase voor het kraken van documenten in indexering. Er worden geen kosten in rekening gebracht voor het ophalen van tekst uit documenten.
>
> De uitvoering van ingebouwde vaardig heden wordt in rekening gebracht op basis van de bestaande [Cognitive Services betalen naar](https://azure.microsoft.com/pricing/details/cognitive-services/)gebruik-prijs. Prijzen voor Image extractie worden beschreven op de [pagina met prijzen](https://azure.microsoft.com/pricing/details/search/).

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.DocumentExtractionSkill

## <a name="skill-parameters"></a>Vaardigheids parameters

Parameters zijn hoofdlettergevoelig.

| Invoerwaarden | Toegestane waarden | Description |
|-----------------|----------------|-------------|
| `parsingMode`   | `default` <br/> `text` <br/> `json`  | Is ingesteld op `default` voor document extractie van bestanden die geen zuivere tekst of JSON zijn. Ingesteld op `text` om de prestaties van tekst bestanden te verbeteren. Stel in om `json` gestructureerde inhoud op te halen uit json-bestanden. Als `parsingMode` niet expliciet is gedefinieerd, wordt deze ingesteld op `default` . |
| `dataToExtract` | `contentAndMetadata` <br/> `allMetadata` | Stel in op `contentAndMetadata` om alle meta gegevens en tekst inhoud van elk bestand uit te pakken. Stel deze in op `allMetadata` alleen de [meta gegevens eigenschappen voor het inhouds type](search-blob-metadata-properties.md) (bijvoorbeeld meta gegevens die uniek zijn voor PNG-bestanden) te extra heren. Als `dataToExtract` niet expliciet is gedefinieerd, wordt deze ingesteld op `contentAndMetadata` . |
| `configuration` | Zie hieronder. | Een woorden lijst met optionele para meters die aanpassen hoe de document extractie wordt uitgevoerd. Zie de onderstaande tabel voor beschrijvingen van ondersteunde configuratie-eigenschappen. |

| Configuratie parameter   | Toegestane waarden | Description |
|-------------------------|----------------|-------------|
| `imageAction`           | `none`<br/> `generateNormalizedImages`<br/> `generateNormalizedImagePerPage` | Instellen op `none` om Inge sloten afbeeldingen of afbeeldings bestanden in de gegevensset te negeren. Dit is de standaardinstelling. <br/>Stel voor een [installatie kopie analyse met behulp van cognitieve vaardig heden](cognitive-search-concept-image-scenarios.md)in `generateNormalizedImages` om ervoor te hebben dat de kwalificatie een matrix van genormaliseerde installatie kopieën maakt als onderdeel van het kraken van documenten. Voor deze actie is vereist dat `parsingMode` is ingesteld op `default` en `dataToExtract` is ingesteld op `contentAndMetadata` . Een genormaliseerde installatie kopie verwijst naar extra verwerking, wat resulteert in een uniforme afbeeldings uitvoer, formaat en roteert om een consistente rendering te bevorderen wanneer u afbeeldingen opneemt in visuele Zoek resultaten (bijvoorbeeld Foto's met dezelfde grootte in een besturings element van de [JFK](https://github.com/Microsoft/AzureSearch_JFK_Files)). Deze informatie wordt voor elke installatie kopie gegenereerd wanneer u deze optie gebruikt.  <br/>Als u deze instelt op `generateNormalizedImagePerPage` , worden PDF-bestanden anders behandeld in plaats van Inge sloten afbeeldingen te extra heren, elke pagina wordt weer gegeven als een afbeelding en genormaliseerd.  Niet-PDF-bestands typen worden op dezelfde manier behandeld als wanneer ze `generateNormalizedImages` zijn ingesteld.
| `normalizedImageMaxWidth` | Wille keurig geheel getal tussen 50-10000 | De maximum breedte (in pixels) voor genormaliseerde afbeeldingen die worden gegenereerd. De standaardwaarde is 2000. | 
| `normalizedImageMaxHeight` | Wille keurig geheel getal tussen 50-10000 | De maximum hoogte (in pixels) voor genormaliseerde afbeeldingen die worden gegenereerd. De standaardwaarde is 2000. |

> [!NOTE]
> De standaard waarde van 2000 pixels voor de genormaliseerde afbeeldingen maximale breedte en hoogte is gebaseerd op de maximale grootte die wordt ondersteund door de [OCR-vaardigheid](cognitive-search-skill-ocr.md) en de vaardigheid van de [afbeeldings analyse](cognitive-search-skill-image-analysis.md). De [OCR-vaardigheid](cognitive-search-skill-ocr.md) ondersteunt een maximale breedte en hoogte van 4200 voor niet-Engelse talen en 10000 voor Engels.  Als u de maximum limiet verhoogt, kan de verwerking op grotere afbeeldingen mislukken, afhankelijk van de definitie van uw vaardig heden en de taal van de documenten. 
## <a name="skill-inputs"></a>Vaardigheids invoer

| Invoer naam     | Description |
|--------------------|-------------|
| `file_data` | Het bestand waaruit de inhoud moet worden opgehaald. |

De invoer file_data moet een object zijn dat als volgt is gedefinieerd:

```json
{
  "$type": "file",
  "data": "BASE64 encoded string of the file"
}
```

Dit bestands referentie object kan op een van de volgende drie manieren worden gegenereerd:

 - De `allowSkillsetToReadFileData` para meter in de definitie van de Indexeer functie wordt ingesteld op ' True '.  Hiermee maakt u een pad `/document/file_data` dat een object vertegenwoordigt met de oorspronkelijke bestands gegevens die zijn gedownload uit de BLOB-gegevens bron. Deze para meter is alleen van toepassing op gegevens in Blob Storage.

 - Het instellen `imageAction` van de para meter in de definitie van de Indexeer functie op een andere waarde dan `none` .  Hiermee maakt u een matrix met installatie kopieën die de vereiste Conventie voor invoer naar deze vaardigheid volgen, indien deze afzonderlijk worden door gegeven (dat wil zeggen `/document/normalized_images/*` ).

 - Als u een aangepaste vaardigheid hebt, retourneert u een JSON-object dat precies zo is gedefinieerd als hierboven.  De `$type` para meter moet exact worden ingesteld `file` en de `data` para meter moet de basis 64 gecodeerde byte matrix gegevens van de bestands inhoud zijn.

## <a name="skill-outputs"></a>Vaardigheids uitvoer

| Uitvoer naam    | Description |
|--------------|-------------|
| `content` | De tekst inhoud van het document. |
| `normalized_images`   | Als de `imageAction` is ingesteld op een andere waarde dan `none` , bevat het veld nieuwe *normalized_images* een matrix met installatie kopieën. Raadpleeg [de documentatie voor de extractie van afbeeldingen](cognitive-search-concept-image-scenarios.md) voor meer informatie over de uitvoer indeling van elke afbeelding. |

##  <a name="sample-definition"></a>Voorbeeld definitie

```json
 {
    "@odata.type": "#Microsoft.Skills.Util.DocumentExtractionSkill",
    "parsingMode": "default",
    "dataToExtract": "contentAndMetadata",
    "configuration": {
        "imageAction": "generateNormalizedImages",
        "normalizedImageMaxWidth": 2000,
        "normalizedImageMaxHeight": 2000
    },
    "context": "/document",
    "inputs": [
      {
        "name": "file_data",
        "source": "/document/file_data"
      }
    ],
    "outputs": [
      {
        "name": "content",
        "targetName": "extracted_content"
      },
      {
        "name": "normalized_images",
        "targetName": "extracted_normalized_images"
      }
    ]
  }
```

##  <a name="sample-input"></a>Voorbeeldinvoer

```json
{
  "values": [
    {
      "recordId": "1",
      "data":
      {
        "file_data": {
          "$type": "file",
          "data": "aGVsbG8="
        }
      }
    }
  ]
}
```


##  <a name="sample-output"></a>Voorbeelduitvoer

```json
{
  "values": [
    {
      "recordId": "1",
      "data": {
        "content": "hello",
        "normalized_images": []
      }
    }
  ]
}
```

## <a name="see-also"></a>Zie ook

+ [Ingebouwde vaardigheden](cognitive-search-predefined-skills.md)
+ [Een vaardig heden definiëren](cognitive-search-defining-skillset.md)
+ [Informatie over het verwerken en extra heren van afbeeldingen in cognitieve Zoek scenario's](cognitive-search-concept-image-scenarios.md)