---
title: Domeinspecifieke inhoud - Computer Vision
titleSuffix: Azure Cognitive Services
description: Meer informatie over het opgeven van een afbeeldingscategorisatiedomein om gedetailleerdere informatie over een afbeelding te retourneren.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/08/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 530ca81cedad06c949323889cc02d2a233dd0c02
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107778819"
---
# <a name="detect-domain-specific-content"></a>Domeinspecifieke inhoud detecteren

Naast taggen en categorisatie op hoog niveau ondersteunt Computer Vision ook verdere domeinspecifieke analyse met behulp van modellen die zijn getraind op gespecialiseerde gegevens.

Er zijn twee manieren om de domeinspecifieke modellen te gebruiken: op zichzelf (scoped analyse) of als uitbreiding van de categorisatiefunctie.

### <a name="scoped-analysis"></a>Scoped analyse

U kunt een afbeelding analyseren met alleen het gekozen domeinspecifieke model door de [API Models/ \<model\> /Analyze aan te](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-ga/operations/56f91f2e778daf14a499f21b) roepen.

Hier volgt een voorbeeld van een JSON-antwoord dat wordt geretourneerd door de **API models/celebrities/analyze** voor de opgegeven afbeelding:

![Satya Nadella die glimlacht](./images/satya.jpeg)

```json
{
  "result": {
    "celebrities": [{
      "faceRectangle": {
        "top": 391,
        "left": 318,
        "width": 184,
        "height": 184
      },
      "name": "Satya Nadella",
      "confidence": 0.99999856948852539
    }]
  },
  "requestId": "8217262a-1a90-4498-a242-68376a4b956b",
  "metadata": {
    "width": 800,
    "height": 1200,
    "format": "Jpeg"
  }
}
```

### <a name="enhanced-categorization-analysis"></a>Verbeterde categorisatieanalyse

U kunt ook domeinspecifieke modellen gebruiken als aanvulling op de algemene afbeeldingsanalyse. U doet dit als onderdeel van categorisatie op hoog niveau door domeinspecifieke modellen op te geven in de *parameter details* van de [](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-ga/operations/56f91f2e778daf14a499f21b) [api-aanroep](concept-categorizing-images.md) Analyseren.

In dit geval wordt de classificatie van de 86-categorie-taxonomie eerst aangeroepen. Als een van de gedetecteerde categorieën een overeenkomend domeinsspecifieke model heeft, wordt de afbeelding ook via dat model doorgegeven en worden de resultaten toegevoegd.

Het volgende JSON-antwoord laat zien hoe domeinspecifieke analyse kan worden opgenomen als het knooppunt in een `detail` bredere categorisatieanalyse.

```json
"categories":[
  {
    "name":"abstract_",
    "score":0.00390625
  },
  {
    "name":"people_",
    "score":0.83984375,
    "detail":{
      "celebrities":[
        {
          "name":"Satya Nadella",
          "faceRectangle":{
            "left":597,
            "top":162,
            "width":248,
            "height":248
          },
          "confidence":0.999028444
        }
      ],
      "landmarks":[
        {
          "name":"Forbidden City",
          "confidence":0.9978346
        }
      ]
    }
  }
]
```

## <a name="list-the-domain-specific-models"></a>De domeinspecifieke modellen op een lijst zetten

Momenteel ondersteunt Computer Vision de volgende domeinspecifieke modellen:

| Naam | Beschrijving |
|------|-------------|
| Beroemdheden | Herkenning van beroemdheden, ondersteund voor afbeeldingen die zijn geclassificeerd in de `people_` categorie |
| Bezienswaardigheden | Herkenning van oriëntatiepunten, ondersteund voor afbeeldingen die zijn geclassificeerd in de `outdoor_` categorieën `building_` of |

Als u de [API Modellen](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-ga/operations/56f91f2e778daf14a499f20e) aanroept, wordt deze informatie samen met de categorieën waarop elk model van toepassing kan zijn, retourneren:

```json
{
  "models":[
    {
      "name":"celebrities",
      "categories":[
        "people_",
        "人_",
        "pessoas_",
        "gente_"
      ]
    },
    {
      "name":"landmarks",
      "categories":[
        "outdoor_",
        "户外_",
        "屋外_",
        "aoarlivre_",
        "alairelibre_",
        "building_",
        "建筑_",
        "建物_",
        "edifício_"
      ]
    }
  ]
}
```

## <a name="next-steps"></a>Volgende stappen

Meer informatie over concepten over [het categoriseren van afbeeldingen.](concept-categorizing-images.md)
