---
title: Categorisatie van afbeeldingen - Computer Vision
titleSuffix: Azure Cognitive Services
description: Leer concepten met betrekking tot de functie voor het categoriseren van afbeeldingen van de Computer Vision API.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 97a6a08730ae0c87df3b65185d48297e2338e69b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107768103"
---
# <a name="categorize-images-by-subject-matter"></a>Afbeeldingen categoriseren op onderwerp

Naast tags en een beschrijving retourneert Computer Vision op taxonomie gebaseerde categorieën die in een afbeelding zijn gedetecteerd. In tegenstelling tot tags worden categorieën ingedeeld in een bovenliggende/onderliggende hiërarchie en zijn er minder van (86 in tegenstelling tot duizenden tags). Alle categorienamen zijn in het Engels. Categorisatie kan zelf of naast het nieuwere tagsmodel worden uitgevoerd.

## <a name="the-86-category-concept"></a>Het concept van de 86 categorieën

Computer Vision kan een afbeelding breed of specifiek categoriseren met behulp van de lijst met 86 categorieën in het volgende diagram. Zie [Categorietaxonomie](category-taxonomy.md) voor de volledige taxonomie in tekstindeling.

![Gegroepeerde lijsten met alle categorieën in de categorie taxonomie](./Images/analyze_categories-v2.png)

## <a name="image-categorization-examples"></a>Voorbeelden van categorisatie van afbeeldingen

Het volgende JSON-antwoord illustreert wat Computer Vision retourneert bij het categoriseren van de voorbeeldafbeelding op basis van de visuele kenmerken.

![Een vrouw op het dak van een gebouw](./Images/woman_roof.png)

```json
{
    "categories": [
        {
            "name": "people_",
            "score": 0.81640625
        }
    ],
    "requestId": "bae7f76a-1cc7-4479-8d29-48a694974705",
    "metadata": {
        "height": 200,
        "width": 300,
        "format": "Jpeg"
    }
}
```

In de volgende tabel ziet u een typische afbeeldingsset en de categorie die door Computer Vision voor elke afbeelding wordt geretourneerd.

| Installatiekopie | Categorie |
|-------|----------|
| ![Vier personen die samen zijn gesteld als een familie](./Images/family_photo.png) | people_group |
| ![Een man die in een veld zit](./Images/cute_dog.png) | animal_dog |
| ![Een persoon die op een bergsteen staat bij een vervalsen](./Images/mountain_vista.png) | outdoor_mountain |
| ![Een stapel broodrollen in een tabel](./Images/bread.png) | food_bread |

## <a name="use-the-api"></a>De API gebruiken

De categorisatiefunctie maakt deel uit van de [Analyze Image](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-ga/operations/56f91f2e778daf14a499f21b) API. U kunt deze API aanroepen via een native SDK of via REST-aanroepen. Neem `Categories` op in de **queryparameter visualFeatures.** Wanneer u vervolgens het volledige JSON-antwoord krijgt, parseert u de tekenreeks voor de inhoud van de `"categories"` sectie.

* [Snelstart: De Computer Vision-clientbibliotheek gebruiken](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de gerelateerde concepten van het [taggen van afbeeldingen](concept-tagging-images.md) [en het beschrijven van afbeeldingen.](concept-describing-images.md)
