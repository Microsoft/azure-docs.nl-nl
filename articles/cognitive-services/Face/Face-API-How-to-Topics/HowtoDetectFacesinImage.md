---
title: Gezichten in een afbeelding detecteren - Face
titleSuffix: Azure Cognitive Services
description: In deze handleiding wordt gedemonstreerd hoe u gezichtsdetectie gebruikt om kenmerken zoals geslacht, leeftijd of houding uit een bepaalde afbeelding te extraheren.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 02/23/2021
ms.author: sbowles
ms.custom: devx-track-csharp
ms.openlocfilehash: 71e98b735b4aa4631d73f8730a48c56a8c7585ab
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107497639"
---
# <a name="get-face-detection-data"></a>Gezichtsdetectiegegevens op halen

In deze handleiding wordt gedemonstreerd hoe u gezichtsdetectie gebruikt om kenmerken zoals geslacht, leeftijd of houding uit een bepaalde afbeelding te extraheren. De codefragmenten in deze handleiding zijn geschreven in C# met behulp van de Azure Cognitive Services Face-clientbibliotheek. Dezelfde functionaliteit is beschikbaar via de [REST API.](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)

In deze handleiding ziet u hoe u het volgende kunt doen:

- Haal de locaties en dimensies van gezichten in een afbeelding op.
- Haal de locaties van verschillende gezichtsherkenningspunten, zoals pupillen, neus en mond, op in een afbeelding.
- Denk aan het geslacht, de leeftijd, de emotie en andere kenmerken van een gedetecteerd gezicht.

## <a name="setup"></a>Instellen

In deze handleiding wordt ervan uitgenomen dat u al een [FaceClient-object](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient) met de naam `faceClient` hebt gemaakt met een Face-abonnementssleutel en eindpunt-URL. Hier kunt u de functie gezichtsdetectie gebruiken door [DetectWithUrlAsync](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithurlasync)aan te roepen, dat in deze handleiding wordt gebruikt, of [DetectWithStreamAsync.](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithstreamasync) Volg een van de quickstarts voor instructies over het instellen van deze functie.

Deze handleiding is gericht op de details van de detectie-aanroep, zoals welke argumenten u kunt doorgeven en wat u kunt doen met de geretourneerde gegevens. U wordt aangeraden alleen een query uit te voeren voor de functies die u nodig hebt. Het voltooien van elke bewerking kost extra tijd.

## <a name="get-basic-face-data"></a>Basisgegevens van gezichtsherkenning op halen

Als u gezichten wilt zoeken en hun locaties in een afbeelding wilt op halen, roept u de methode [DetectWithUrlAsync](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithurlasync) of [DetectWithStreamAsync](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithstreamasync) aan met de parameter _returnFaceId_ ingesteld op **true**. Dit is de standaardinstelling.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="basic1":::

U kunt de geretourneerde [DetectedFace-objecten](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface) opvragen voor hun unieke ID's en een rechthoek die de pixelcoördinaten van het gezicht geeft.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="basic2":::

Zie [FaceRectangle](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.facerectangle)voor meer informatie over het parseren van de locatie en dimensies van het gezicht. Normaal gesproken bevat deze rechthoek de ogen, neus, neus en mond. De bovenkant van het hoofd, de oren en de kin zijn niet noodzakelijkerwijs opgenomen. Als u de gezichtrechthoek wilt gebruiken om een volledige kop bij te snijden of een staande foto te krijgen, bijvoorbeeld voor een afbeelding van het type foto-id, kunt u de rechthoek in elke richting uitbreiden.

## <a name="get-face-landmarks"></a>Gezichtsherkenningspunten krijgen

[Gezichtsherkenningspunten](../concepts/face-detection.md#face-landmarks) zijn een set eenvoudig te vinden punten op een gezicht, zoals de pupillen of de punt van de neus. Als u oriëntatiepuntgegevens voor gezichten wilt op halen, stelt u de parameter _detectionModel_ in op **DetectionModel.Detection01** en de parameter _returnFaceLandmarks_ op **true**.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="landmarks1":::

De volgende code laat zien hoe u de locaties van de neus en pupillen kunt ophalen:

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="landmarks2":::

U kunt ook gegevens over gezichtsherkenningspunten gebruiken om de richting van het gezicht nauwkeurig te berekenen. U kunt bijvoorbeeld de rotatie van het gezicht definiëren als een vector van het midden van de mond naar het midden van de ogen. Met de volgende code wordt deze vector berekend:

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="direction":::

Wanneer u de richting van het gezicht weet, kunt u het rechthoekige gezichtsframe draaien om het beter uit te lijnen. Als u gezichten in een afbeelding wilt bijsnijden, kunt u de afbeelding programmatisch roteren, zodat de gezichten altijd worden weergegeven.

## <a name="get-face-attributes"></a>Gezichtskenmerken ophalen

Naast gezichtrechthoeken en oriëntatiepunten kan de API voor gezichtsdetectie verschillende conceptuele kenmerken van een gezicht analyseren. Zie de conceptuele sectie [Face-kenmerken](../concepts/face-detection.md#attributes) voor een volledige lijst.

Als u gezichtskenmerken wilt analyseren, stelt u de parameter _detectionModel_ in op **DetectionModel.Detection01** en de parameter _returnFaceAttributes_ op een lijst met [FaceAttributeType Enum-waarden.](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype)

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="attributes1":::

Haal vervolgens verwijzingen naar de geretourneerde gegevens op en doe meer bewerkingen op basis van uw behoeften.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="attributes2":::

Zie de conceptuele handleiding Gezichtsdetectie en -kenmerken voor meer informatie over [elk](../concepts/face-detection.md) van de kenmerken.

## <a name="next-steps"></a>Volgende stappen

In deze handleiding hebt u geleerd hoe u de verschillende functies van gezichtsdetectie gebruikt. Integreer vervolgens deze functies in een app om gezichtsgegevens van gebruikers toe te voegen.

- [Zelfstudie: Gebruikers toevoegen aan een Face-service](../enrollment-overview.md)

## <a name="related-topics"></a>Verwante onderwerpen

- [Referentiedocumentatie (REST)](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
- [Referentiedocumentatie (.NET SDK)](/dotnet/api/overview/azure/cognitiveservices/client/faceapi)
