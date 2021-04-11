---
title: De afbeeldings analyse-API aanroepen
titleSuffix: Azure Cognitive Services
description: Meer informatie over het aanroepen van de API voor beeld analyse en het configureren van het gedrag.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: sample
ms.date: 09/09/2019
ms.author: kefre
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 3f9a6afe3202df40e26332c3a8c91b8c3eca8a32
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2021
ms.locfileid: "107012265"
---
# <a name="call-the-image-analysis-api"></a>De afbeeldings analyse-API aanroepen

In dit artikel wordt beschreven hoe u de API voor afbeeldings analyse aanroept om informatie te retour neren over de visuele functies van een afbeelding.

In deze hand leiding wordt ervan uitgegaan dat u al <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title=" een computer vision resource hebt gemaakt om "  target="_blank"> een computer vision resource </a> te maken en om een abonnements sleutel en eind punt-URL te verkrijgen. Als u dat nog niet hebt gedaan, volgt u een [Snelstartgids](../quickstarts-sdk/image-analysis-client-library.md) om aan de slag te gaan.
  
## <a name="submit-data-to-the-service"></a>Gegevens verzenden naar de service

U verzendt een lokale installatie kopie of een externe installatie kopie naar de analyse-API. Voor lokaal plaatst u de gegevens van de binaire installatie kopie in de hoofd tekst van de HTTP-aanvraag. Voor extern kunt u de URL van de installatie kopie opgeven door de hoofd tekst van de aanvraag te Format teren, zoals in het volgende: `{"url":"http://example.com/images/test.jpg"}` .

## <a name="determine-how-to-process-the-data"></a>Bepalen hoe de gegevens moeten worden verwerkt

###  <a name="select-visual-features"></a>Visuele onderdelen selecteren

De [analyse-API](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-3/operations/56f91f2e778daf14a499f21b) geeft u toegang tot alle functies van de installatie kopie analyse van de service. U moet opgeven welke functies u wilt gebruiken door de URL-query parameters in te stellen. Een para meter kan meerdere waarden bevatten, gescheiden door komma's. Voor elke functie die u opgeeft, moet u een extra reken tijd opgeven, zodat u alleen opgeeft wat u nodig hebt.

|URL-parameter | Waarde | Beschrijving|
|---|---|--|
|`visualFeatures`|`Adult` | detecteert of de afbeelding porno grafie verduidelijkt (komt voor een naaktheid of een geslachte handeling), of is benchmarks (voor beelden van extreem geweld of bloed). Er wordt ook een expliciete suggestie voor inhoud (ook wel ongepaste-inhoud) gedetecteerd.|
||`Brands` | detecteert verschillende merken in een installatie kopie, met inbegrip van de geschatte locatie. Het argument Brands is alleen beschikbaar in het Engels.|
||`Categories` | de inhoud van de afbeelding wordt gecategoriseerd op basis van een taxonomie die is gedefinieerd in de documentatie. Dit is de standaard waarde van `visualFeatures` .|
||`Color` | bepaalt de accent kleur, dominante kleur en of een afbeelding zwart&wit is.|
||`Description` | Hierin wordt de afbeeldings inhoud met een volledige zin in ondersteunde talen beschreven.|
||`Faces` | detecteert of gezichten aanwezig zijn. Als dit het geval is, kunt u coördinaten, geslacht en leeftijd genereren.|
||`ImageType` | detecteert of de afbeelding illustraties of een lijn tekening is.|
||`Objects` | detecteert verschillende objecten in een installatie kopie, met inbegrip van de geschatte locatie. Het argument Objects is alleen beschikbaar in het Engels.|
||`Tags` | Tags de afbeelding met een gedetailleerde lijst met woorden die betrekking hebben op de inhoud van de installatie kopie.|
|`details`| `Celebrities` | Hiermee wordt beroemdheden geïdentificeerd als deze wordt gedetecteerd in de installatie kopie.|
||`Landmarks` |identificeert bezienswaardigheden als deze worden gedetecteerd in de installatie kopie.|

Een gevulde URL kan er als volgt uitzien:

`https://{endpoint}/vision/v2.1/analyze?visualFeatures=Description,Tags&details=Celebrities`

### <a name="specify-languages"></a>Talen opgeven

U kunt ook de taal van de geretourneerde gegevens opgeven. De volgende URL-query parameter specificeert de taal. De standaardwaarde is `en`.

|URL-parameter | Waarde | Beschrijving|
|---|---|--|
|`language`|`en` | Engels|
||`es` | Spaans|
||`ja` | Japans|
||`pt` | Portugees|
||`zh` | Vereenvoudigd Chinees|

Een gevulde URL kan er als volgt uitzien:

`https://{endpoint}/vision/v2.1/analyze?visualFeatures=Description,Tags&details=Celebrities&language=en`

> [!NOTE]
> **Scoped API-aanroepen**
>
> Sommige van de functies in de afbeeldings analyse kunnen rechtstreeks worden aangeroepen en via de API-aanroep voor analyse. U kunt bijvoorbeeld een bereik analyse van alleen afbeeldings Tags uitvoeren door een aanvraag in te stellen `https://{endpoint}/vision/v3.2-preview.3/tag` . Zie de [referentie documentatie](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-3/operations/56f91f2e778daf14a499f21b) voor andere functies die afzonderlijk kunnen worden aangeroepen.

## <a name="get-results-from-the-service"></a>Resultaten van de service ophalen

De service retourneert een `200` http-antwoord en de hoofd tekst bevat de geretourneerde gegevens in de vorm van een JSON-teken reeks. Hier volgt een voor beeld van een JSON-antwoord.

```json
{  
  "tags":[  
    {  
      "name":"outdoor",
      "score":0.976
    },
    {  
      "name":"bird",
      "score":0.95
    }
  ],
  "description":{  
    "tags":[  
      "outdoor",
      "bird"
    ],
    "captions":[  
      {  
        "text":"partridge in a pear tree",
        "confidence":0.96
      }
    ]
  }
}
```

Zie de volgende tabel voor uitleg van de velden in dit voor beeld:

Veld | Type | Inhoud
------|------|------|
Tags  | `object` | Het object op het hoogste niveau voor een matrix van tags.
tags[].Name | `string`    | Het trefwoord uit de classificatie van tags.
tags[].Score    | `number`    | De betrouwbaarheidsscore, tussen 0 en 1.
description     | `object`    | Het object op het hoogste niveau voor een beschrijving van een afbeelding.
description.tags[] |    `string`    | De lijst met tags. Als de mogelijkheid om een bijschrift te produceren niet betrouwbaar genoeg is, zijn de tags mogelijk de enige informatiebron die voor de aanroepende functie beschikbaar is.
description.captions[].text    | `string`    | Een zin die de afbeelding beschrijft.
description.captions[].confidence    | `number`    | De betrouwbaarheidsscore voor de zin.

### <a name="error-codes"></a>Foutcodes

Raadpleeg de volgende lijst met mogelijke fouten en de oorzaken ervan:

* 400
    * InvalidImageUrl: de URL van de afbeelding heeft een ongeldige indeling of is niet toegankelijk.
    * InvalidImageFormat: invoer gegevens zijn geen geldige installatie kopie.
    * InvalidImageSize-invoer afbeelding is te groot.
    * Het NotSupportedVisualFeature-opgegeven functie type is niet geldig.
    * NotSupportedImage: niet-ondersteunde installatie kopie, bijvoorbeeld onderliggende porno grafie.
    * InvalidDetails: de parameter waarde wordt niet ondersteund `detail` .
    * NotSupportedLanguage: de aangevraagde bewerking wordt niet ondersteund in de opgegeven taal.
    * BadArgument: aanvullende informatie vindt u in het fout bericht.
* 415-fout met niet-ondersteund media type. Het inhouds type bevindt zich niet in de toegestane typen:
    * Voor een afbeeldings-URL: het inhouds type moet application/json zijn
    * Voor de gegevens van een binaire afbeelding: het inhouds type moet application/octet-stream of meerdelige/form-data zijn
* 500
    * FailedToProcess
    * Time-out tijdens verwerken van installatie kopie.
    * InternalServerError

## <a name="next-steps"></a>Volgende stappen

Als u de REST API wilt uitproberen, gaat u naar de [Image ANALYSIS API-referentie](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-3/operations/56f91f2e778daf14a499f21b).
