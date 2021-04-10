---
title: Een detectie model opgeven-face
titleSuffix: Azure Cognitive Services
description: In dit artikel wordt uitgelegd hoe u kunt kiezen welk gezichts detectie model u wilt gebruiken met uw Azure face-toepassing.
services: cognitive-services
author: yluiu
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 03/05/2021
ms.author: yluiu
ms.custom: devx-track-csharp
ms.openlocfilehash: 72fd005ce44d116f86d9a0b4c0d1932e2e4facfb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102425767"
---
# <a name="specify-a-face-detection-model"></a>Een model voor gezichtsdetectie opgeven

In deze hand leiding wordt uitgelegd hoe u een gezichts detectie model voor de Azure face-service kunt opgeven.

De face-service maakt gebruik van machine learning modellen voor het uitvoeren van bewerkingen op menselijke gezichten in installatie kopieën. We blijven de nauw keurigheid van onze modellen verbeteren op basis van feedback van klanten en de voor uitgang van onderzoek en wij leveren deze verbeteringen als model updates. Ontwikkel aars hebben de optie om op te geven welke versie van het gezichts detectie model ze willen gebruiken. ze kunnen het model kiezen dat het beste past bij hun gebruik.

Lees in voor meer informatie over het opgeven van het gezichts detectie model in bepaalde gezichts bewerkingen. De face-service maakt gebruik van gezichts detectie wanneer een afbeelding van een gezicht wordt omgezet in een andere vorm van gegevens.

Als u niet zeker weet of u het meest recente model moet gebruiken, gaat u naar de sectie [verschillende modellen evalueren](#evaluate-different-models) om het nieuwe model te evalueren en de resultaten te vergelijken met de huidige gegevensset.

## <a name="prerequisites"></a>Vereisten

U moet bekend zijn met het concept van AI-gezichts detectie. Als dat niet het geval is, raadpleegt u de conceptuele hand leiding voor gezichts detectie of instructies:

* [Concepten van gezichts detectie](../concepts/face-detection.md)
* [Gezichten detecteren in een installatie kopie](HowtoDetectFacesinImage.md)

## <a name="detect-faces-with-specified-model"></a>Gezichten met het opgegeven model detecteren

Met gezichts herkenning vindt u de locatie van het begrenzingsvak van menselijke gezichten en identificeert u hun visuele bezienswaardigheden. De functies van het gezicht worden geëxtraheerd en opgeslagen voor toekomstig gebruik in [herkennings](../concepts/face-recognition.md) bewerkingen.

Wanneer u de [Face-detect-] API gebruikt, kunt u de model versie toewijzen met de `detectionModel` para meter. De beschikbare waarden zijn:

* `detection_01`
* `detection_02`
* `detection_03`

Een aanvraag-URL voor het [gezichts detectie] rest API ziet er als volgt uit:

`https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes][&recognitionModel][&returnRecognitionModel][&detectionModel]&subscription-key=<Subscription key>`

Als u de-client bibliotheek gebruikt, kunt u de waarde voor toewijzen `detectionModel` door door te geven in een geschikte teken reeks. Als u deze niet toegewezen geeft, gebruikt de API de standaard model versie ( `detection_01` ). Zie het volgende code voorbeeld voor de .NET-client bibliotheek.

```csharp
string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
var faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, false, false, recognitionModel: "recognition_04", detectionModel: "detection_03");
```

## <a name="add-face-to-person-with-specified-model"></a>Een gezicht toevoegen aan een persoon met een opgegeven model

De face-service kan gezichts gegevens uit een afbeelding extra heren en deze koppelen aan een **persoons** object via de [PersonGroup persoon-add face-](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) API. In deze API-aanroep kunt u het detectie model op dezelfde manier als in het [gezichts detectie]opgeven.

Zie het volgende code voorbeeld voor de .NET-client bibliotheek.

```csharp
// Create a PersonGroup and add a person with face detected by "detection_03" model
string personGroupId = "mypersongroupid";
await faceClient.PersonGroup.CreateAsync(personGroupId, "My Person Group Name", recognitionModel: "recognition_04");

string personId = (await faceClient.PersonGroupPerson.CreateAsync(personGroupId, "My Person Name")).PersonId;

string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
await client.PersonGroupPerson.AddFaceFromUrlAsync(personGroupId, personId, imageUrl, detectionModel: "detection_03");
```

Met deze code wordt een **PersonGroup** met id gemaakt `mypersongroupid` en wordt hieraan een **persoon** toegevoegd. Vervolgens wordt er een gezicht aan deze **persoon** toegevoegd met behulp van het `detection_03` model. Als u de para meter *detectionModel* niet opgeeft, wordt het standaard model gebruikt door de API `detection_01` .

> [!NOTE]
> U hoeft niet hetzelfde detectie model te gebruiken voor alle gezichten in een **persoons** object en u hoeft niet hetzelfde detectie model te gebruiken bij het detecteren van nieuwe gezichten om te vergelijken met een **persoons** object (bijvoorbeeld in de API voor het identificeren van het [gezichts] punt).

## <a name="add-face-to-facelist-with-specified-model"></a>Gezicht toevoegen aan FaceList met het opgegeven model

U kunt ook een detectie model opgeven wanneer u een gezicht toevoegt aan een bestaand **FaceList** -object. Zie het volgende code voorbeeld voor de .NET-client bibliotheek.

```csharp
await faceClient.FaceList.CreateAsync(faceListId, "My face collection", recognitionModel: "recognition_04");

string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
await client.FaceList.AddFaceFromUrlAsync(faceListId, imageUrl, detectionModel: "detection_03");
```

Deze code maakt een **FaceList** `My face collection` met de naam en voegt een gezicht toe aan het `detection_03` model. Als u de para meter *detectionModel* niet opgeeft, wordt het standaard model gebruikt door de API `detection_01` .

> [!NOTE]
> U hoeft niet hetzelfde detectie model te gebruiken voor alle gezichten in een **FaceList** -object en u hoeft niet hetzelfde detectie model te gebruiken bij het detecteren van nieuwe gezichten om te vergelijken met een **FaceList** -object.

## <a name="evaluate-different-models"></a>Verschillende modellen evalueren

De verschillende gezichts detectie modellen zijn geoptimaliseerd voor verschillende taken. Raadpleeg de volgende tabel voor een overzicht van de verschillen.

|**detection_01**  |**detection_02**  |**detection_03** 
|---------|---------|---|
|Standaard keuze voor alle gezichts detectie bewerkingen. | Uitgebracht in mei 2019 en optioneel beschikbaar in alle gezichts detectie bewerkingen. |  Uitgebracht in februari 2021 en optioneel beschikbaar in alle gezichts detectie bewerkingen.
|Niet geoptimaliseerd voor kleine, weer gave-of wazige gezichten.  | Verbeterde nauw keurigheid van kleine, weer gave-en wazige gezichten. | Grotere nauw keurigheid, inclusief op kleinere gezichten (64x64 pixels) en geroteerde gezichts standen.
|Hiermee worden de belangrijkste kenmerken van het gezicht (Head pose, Age, Emotion, enzovoort) geretourneerd als deze zijn opgegeven in de aanroep detectie. |  Retourneert geen face-kenmerken.     | Retourneert de kenmerken ' faceMask ' en ' noseAndMouthCovered ' als deze zijn opgegeven in de aanroep van de detectie.
|Retourneert gezichts bezienswaardigheden als deze zijn opgegeven in de detectie aanroep.   | Retourneert geen gezichts bezienswaardigheden.  | Retourneert geen gezichts bezienswaardigheden.

De beste manier om de prestaties van de detectie modellen te vergelijken, is door deze te gebruiken in een voorbeeld gegevensset. We raden u aan de API voor het detectie van het [gezichts] aanbod op diverse installatie kopieën aan te roepen, met name afbeeldingen van veel gezichten of gezichten die moeilijk te zien zijn, met behulp van elk detectie model. Let op het aantal gezichten dat elke model retourneert.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u het detectie model kunt opgeven voor gebruik met verschillende gezichts-Api's. Volg vervolgens een Snelstartgids om aan de slag te gaan met gezichts detectie.

* [Gezichts-.NET-SDK](../quickstarts/client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp)
* [Gezichts python-SDK](../quickstarts/client-libraries.md?pivots=programming-language-python%253fpivots%253dprogramming-language-python)
* [Gezichts go-SDK](../quickstarts/client-libraries.md?pivots=programming-language-go%253fpivots%253dprogramming-language-go)

[Face - Detecteren]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d
[Face - Find Similar]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237
[Face - Identificeren]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239
[Face - Verify]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a
[PersonGroup - Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244
[PersonGroup - Get]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246
[PersonGroup Person - Add Face]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b
[PersonGroup - Train]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249
[LargePersonGroup - Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d
[FaceList - Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b
[FaceList - Get]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c
[FaceList - Add Face]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250
[LargeFaceList - Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc