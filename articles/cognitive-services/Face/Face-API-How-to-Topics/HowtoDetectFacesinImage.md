---
title: Gezichten detecteren in een afbeeldings gezicht
titleSuffix: Azure Cognitive Services
description: In deze hand leiding wordt gedemonstreerd hoe u gezichts detectie kunt gebruiken om kenmerken te extra heren, zoals geslacht, leeftijd of pose van een bepaalde afbeelding.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: sbowles
ms.custom: devx-track-csharp
ms.openlocfilehash: 81a5f771e141639b2dcf33afe603fe53428bf88a
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98943606"
---
# <a name="get-face-detection-data"></a>Detectie gegevens van het gezicht ophalen

In deze hand leiding wordt gedemonstreerd hoe u gezichts detectie kunt gebruiken om kenmerken te extra heren, zoals geslacht, leeftijd of pose van een bepaalde afbeelding. De code fragmenten in deze hand leiding zijn geschreven in C# met behulp van de Azure Cognitive Services Face client-bibliotheek. Dezelfde functionaliteit is beschikbaar via de [rest API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

In deze hand leiding wordt uitgelegd hoe u:

- Haal de locaties en dimensies van gezichten op in een afbeelding.
- Haal de locaties van verschillende gezichts bezienswaardigheden, zoals leerlingen, neus en mond, op in een afbeelding.
- Het geslacht, de leeftijd, de Emotion en andere kenmerken van een gedetecteerd gezicht schatten.

## <a name="setup"></a>Instellen

In deze hand leiding wordt ervan uitgegaan dat u al een [FaceClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient) -object met de naam hebt gemaakt `faceClient` met een face-abonnements sleutel en eind punt-URL. Hier kunt u de functie voor gezichts herkenning gebruiken door het aanroepen van [DetectWithUrlAsync](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithurlasync), dat wordt gebruikt in deze hand leiding of [DetectWithStreamAsync](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithstreamasync). Volg een van de Quick starts voor instructies over het instellen van deze functie.

Deze hand leiding is gericht op de details van de detectie aanroep, zoals de argumenten die u kunt door geven en wat u met de geretourneerde gegevens kunt doen. U wordt aangeraden alleen een query uit te voeren voor de functies die u nodig hebt. Elke bewerking heeft meer tijd nodig om te volt ooien.

## <a name="get-basic-face-data"></a>Basis gegevens over het gezicht ophalen

Roep de [DetectWithUrlAsync](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithurlasync) -of [DetectWithStreamAsync](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithstreamasync) -methode aan waarbij de para meter _returnFaceId_ is ingesteld op **True** om gezichten te vinden en hun locaties op te halen in een installatie kopie. Dit is de standaardinstelling.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="basic1":::

U kunt de geretourneerde [DetectedFace](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface) -objecten opvragen voor hun unieke id's en een rechthoek die de pixel coördinaten van het gezicht geeft.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="basic2":::

Zie [FaceRectangle](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.facerectangle)voor informatie over het parseren van de locatie en afmetingen van het gezicht. Normaal gesp roken bevat deze rechthoek de ogen, Eyebrows, neus en mond. De bovenkant van Head, oren en Chin is niet noodzakelijkerwijs opgenomen. Als u de gezichts rechthoek wilt gebruiken om een volledige kop te bijsnijden of een mid-shot portret te krijgen, mogelijk voor een foto-ID-type afbeelding, kunt u de rechthoek in elke richting uitvouwen.

## <a name="get-face-landmarks"></a>Gezichts bezienswaardigheden ophalen

[Gezichts bezienswaardigheden](../concepts/face-detection.md#face-landmarks) zijn een verzameling gemakkelijk te vinden punten op een gezicht, zoals de pupilsen of de tip van de neus. Als u gegevens over het gezichts punt wilt ophalen, stelt u de para meter _detectionModel_ in op **detectionModel. Detection01** en de para meter _returnFaceLandmarks_ op **True**.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="landmarks1":::

De volgende code laat zien hoe u de locaties van de neus en de pupillen kunt ophalen:

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="landmarks2":::

U kunt ook gegevens over gezichts bezienswaardigheden gebruiken om de richting van het gezicht nauw keurig te berekenen. U kunt bijvoorbeeld de draai hoek van het gezicht definiëren als een vector van het midden van de mond naar het midden van de ogen. Met de volgende code wordt deze vector berekend:

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="direction":::

Wanneer u de richting van het gezicht kent, kunt u het rechthoekige gezichts kader draaien om het op de juiste manier af te stemmen. Als u gezichten wilt bijsnijden in een afbeelding, kunt u de afbeelding op een programmatische manier draaien, zodat de gezichten altijd rechtop worden weer gegeven.

## <a name="get-face-attributes"></a>Gezichtskenmerken ophalen

Naast gezichts rechthoeken en bezienswaardigheden, kunnen met de gezichts detectie-API verschillende conceptuele kenmerken van een gezicht worden geanalyseerd. Zie de sectie met de conceptuele [kenmerken](../concepts/face-detection.md#attributes) voor een volledige lijst.

Als u face-kenmerken wilt analyseren, stelt u de para meter _detectionModel_ in op **detectionModel. Detection01** en de para meter _ReturnFaceAttributes_ in een lijst met [FaceAttributeType Enum](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype) -waarden.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="attributes1":::

Vervolgens krijgt u verwijzingen naar de geretourneerde gegevens en voert u meer bewerkingen uit op basis van uw behoeften.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="attributes2":::

Zie de conceptuele hand leiding voor [gezichts detectie en kenmerken](../concepts/face-detection.md) voor meer informatie over elk van de kenmerken.

## <a name="next-steps"></a>Volgende stappen

In deze hand leiding hebt u geleerd hoe u de verschillende functies van gezicht detectie kunt gebruiken. Vervolgens integreert u deze functies in uw app door een diep gaande zelf studie te volgen.

- [Zelfstudie: een WPF-app maken om gegevens van gezichten in een afbeelding weer te geven](../Tutorials/FaceAPIinCSharpTutorial.md)

## <a name="related-topics"></a>Verwante onderwerpen

- [Referentie documentatie (REST)](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
- [Referentie documentatie (.NET SDK)](/dotnet/api/overview/azure/cognitiveservices/client/faceapi)
