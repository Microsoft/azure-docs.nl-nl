---
title: Afbeeldings categorisatie-Computer Vision
titleSuffix: Azure Cognitive Services
description: Leer concepten met betrekking tot de functie voor het categoriseren van afbeeldingen van de Computer Vision-API.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 9721ffa807c9adbeb50839113bc64fd23d8eb13f
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96533720"
---
# <a name="categorize-images-by-subject-matter"></a>Afbeeldingen op onderwerp categoriseren

Naast Tags en een beschrijving Computer Vision retourneert de op taxonomie gebaseerde categorieën die zijn gedetecteerd in een installatie kopie. In tegens telling tot labels worden categorieën in een bovenliggende/onderliggende Hereditary-hiërarchie ingedeeld en zijn er minder van de tags (86, in tegens telling tot duizenden Tags). Alle categorie namen zijn in het Engels. Categorisatie kan zelf of naast het nieuwere label model worden uitgevoerd.

## <a name="the-86-category-concept"></a>Het concept van de 86 categorieën

Computer vision kan een afbeelding breed of specifiek categoriseren met behulp van de lijst met 86-categorieën in het volgende diagram. Zie [Categorietaxonomie](category-taxonomy.md) voor de volledige taxonomie in tekstindeling.

![Gegroepeerde lijsten van alle categorieën in de categorie taxonomie](./Images/analyze_categories-v2.png)

## <a name="image-categorization-examples"></a>Voor beelden van afbeeldings categorisatie

In het volgende JSON-antwoord ziet u wat Computer Vision retourneert wanneer de voorbeeld afbeelding wordt gecategoriseerd op basis van de visuele functies.

![Een vrouw aan het dak van een Apartment-gebouw](./Images/woman_roof.png)

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

In de volgende tabel ziet u een typische installatie kopie en de categorie die wordt geretourneerd door Computer Vision voor elke afbeelding.

| Installatiekopie | Categorie |
|-------|----------|
| ![Vier mensen die als familie samen werken](./Images/family_photo.png) | people_group |
| ![Een puppy die in een gras veld zit](./Images/cute_dog.png) | animal_dog |
| ![Een persoon in een Zons ondergang van een Mountain Rock](./Images/mountain_vista.png) | outdoor_mountain |
| ![Een stapel van brood rollen in een tabel](./Images/bread.png) | food_bread |

## <a name="use-the-api"></a>De API gebruiken

De categorisatie functie maakt deel uit van de API voor het [analyseren van afbeeldingen](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b) . U kunt deze API aanroepen via een systeem eigen SDK of via REST-aanroepen. Neem `Categories` in de query parameter **visualFeatures** op. Wanneer u vervolgens het volledige JSON-antwoord krijgt, parseert u de teken reeks voor de inhoud van de `"categories"` sectie.

* [Snelstartgids: Computer Vision REST API of client bibliotheken](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de verwante concepten voor het [labelen van afbeeldingen](concept-tagging-images.md) en het [beschrijven van installatie kopieën](concept-describing-images.md).
