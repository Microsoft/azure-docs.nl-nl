---
title: Latentie beperken bij het gebruik van de Face-service
titleSuffix: Azure Cognitive Services
description: Meer informatie over het beperken van latentie bij het gebruik van de Face-service.
services: cognitive-services
manager: chrhoder
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 1/5/2021
ms.openlocfilehash: a306883573387a2a5c20a53c7015c6dbd3eddf65
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107878664"
---
# <a name="how-to-mitigate-latency-when-using-the-face-service"></a>Hoe: latentie beperken bij het gebruik van de Face-service

Er kan latentie zijn bij het gebruik van de Face-service. Latentie verwijst naar elk soort vertraging dat optreedt bij de communicatie via een netwerk. Over het algemeen zijn dit mogelijke oorzaken van latentie:
- De fysieke afstand die elk pakket moet afleggen van de bron naar de bestemming.
- Problemen met het overdrachtsmedium.
- Fouten in routers of switches langs het overdrachtspad.
- De tijd die antivirustoepassingen, firewalls en andere beveiligingsmechanismen nodig hebben om pakketten te inspecteren.
- Defecten in client- of servertoepassingen.

In dit onderwerp worden mogelijke oorzaken van latentie besproken die specifiek zijn voor het gebruik van Azure Cognitive Services en hoe u deze oorzaken kunt verhelpen.

> [!NOTE]
> Azure Cognitive Services bieden geen Service Level Agreement (SLA) met betrekking tot latentie.

## <a name="possible-causes-of-latency"></a>Mogelijke oorzaken van latentie

### <a name="slow-connection-between-the-cognitive-service-and-a-remote-url"></a>Trage verbinding tussen de Cognitive Service en een externe URL

Sommige Azure Cognitive Services bieden methoden voor het verkrijgen van gegevens van een externe URL die u op verstrekt. Wanneer u bijvoorbeeld de methode [DetectWithUrlAsync](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithurlasync#Microsoft_Azure_CognitiveServices_Vision_Face_FaceOperationsExtensions_DetectWithUrlAsync_Microsoft_Azure_CognitiveServices_Vision_Face_IFaceOperations_System_String_System_Nullable_System_Boolean__System_Nullable_System_Boolean__System_Collections_Generic_IList_System_Nullable_Microsoft_Azure_CognitiveServices_Vision_Face_Models_FaceAttributeType___System_String_System_Nullable_System_Boolean__System_String_System_Threading_CancellationToken_) van de Face-service aanroept, kunt u de URL opgeven van een afbeelding waarin de service gezichten probeert te detecteren.

```csharp
var faces = await client.Face.DetectWithUrlAsync("https://www.biography.com/.image/t_share/MTQ1MzAyNzYzOTgxNTE0NTEz/john-f-kennedy---mini-biography.jpg");
```

De Face-service moet de afbeelding vervolgens downloaden van de externe server. Als de verbinding van de Face-service naar de externe server traag is, heeft dit invloed op de reactietijd van de Detect-methode.

U kunt dit verhelpen door de [afbeelding op te slaan in Azure Premium Blob Storage](../../../storage/blobs/storage-upload-process-images.md?tabs=dotnet). Bijvoorbeeld:

``` csharp
var faces = await client.Face.DetectWithUrlAsync("https://csdx.blob.core.windows.net/resources/Face/Images/Family1-Daughter1.jpg");
```

### <a name="large-upload-size"></a>Grote uploadgrootte

Sommige Azure Cognitive Services bieden methoden voor het verkrijgen van gegevens uit een bestand dat u uploadt. Wanneer u bijvoorbeeld de methode [DetectWithStreamAsync](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithstreamasync#Microsoft_Azure_CognitiveServices_Vision_Face_FaceOperationsExtensions_DetectWithStreamAsync_Microsoft_Azure_CognitiveServices_Vision_Face_IFaceOperations_System_IO_Stream_System_Nullable_System_Boolean__System_Nullable_System_Boolean__System_Collections_Generic_IList_System_Nullable_Microsoft_Azure_CognitiveServices_Vision_Face_Models_FaceAttributeType___System_String_System_Nullable_System_Boolean__System_String_System_Threading_CancellationToken_) van de Face-service aanroept, kunt u een afbeelding uploaden waarin de service gezichten probeert te detecteren.

```csharp
using FileStream fs = File.OpenRead(@"C:\images\face.jpg");
System.Collections.Generic.IList<DetectedFace> faces = await client.Face.DetectWithStreamAsync(fs, detectionModel: DetectionModel.Detection02);
```

Als het bestand dat moet worden geüpload groot is, is dit om de volgende redenen van invloed op de reactietijd `DetectWithStreamAsync` van de methode:
- Het uploaden van het bestand duurt langer.
- Het duurt langer voor de service het bestand verwerkt, in verhouding tot de bestandsgrootte.

Oplossingen:
- U [kunt de afbeelding opslaan in Azure Premium Blob Storage](../../../storage/blobs/storage-upload-process-images.md?tabs=dotnet). Bijvoorbeeld:
``` csharp
var faces = await client.Face.DetectWithUrlAsync("https://csdx.blob.core.windows.net/resources/Face/Images/Family1-Daughter1.jpg");
```
- Overweeg een kleiner bestand te uploaden.
    - Zie de richtlijnen met betrekking [tot invoergegevens voor gezichtsdetectie](../concepts/face-detection.md#input-data) en [invoergegevens voor gezichtsherkenning.](../concepts/face-recognition.md#input-data)
    - Bij gezichtsdetectie verhoogt het verkleinen van de afbeeldingsbestandsgrootte bij het gebruik `DetectionModel.Detection01` van het detectiemodel de verwerkingssnelheid. Wanneer u het detectiemodel gebruikt, verhoogt het verkleinen van de bestandsgrootte van de afbeelding alleen de verwerkingssnelheid als het afbeeldingsbestand `DetectionModel.Detection02` kleiner is dan 1920x1080.
    - Voor gezichtsherkenning heeft het verkleinen van de gezichtsgrootte tot 200x200 pixels geen invloed op de nauwkeurigheid van het herkenningsmodel.
    - De prestaties van de `DetectWithUrlAsync` methoden en zijn ook afhankelijk van het aantal gezichten in een `DetectWithStreamAsync` afbeelding. De Face-service kan maximaal 100 gezichten retourneren voor een afbeelding. Gezichten worden gerangschikt op rechthoekgrootte van groot naar klein.
    - Als u meerdere servicemethoden wilt aanroepen, kunt u deze parallel aanroepen als uw toepassingsontwerp dit toestaat. Als u bijvoorbeeld gezichten in twee afbeeldingen moet detecteren om een gezichtsvergelijking uit te voeren:
```csharp
var faces_1 = client.Face.DetectWithUrlAsync("https://www.biography.com/.image/t_share/MTQ1MzAyNzYzOTgxNTE0NTEz/john-f-kennedy---mini-biography.jpg");
var faces_2 = client.Face.DetectWithUrlAsync("https://www.biography.com/.image/t_share/MTQ1NDY3OTIxMzExNzM3NjE3/john-f-kennedy---debating-richard-nixon.jpg");
Task.WaitAll (new Task<IList<DetectedFace>>[] { faces_1, faces_2 });
IEnumerable<DetectedFace> results = faces_1.Result.Concat (faces_2.Result);
```

### <a name="slow-connection-between-your-compute-resource-and-the-face-service"></a>Trage verbinding tussen uw rekenresource en de Face-service

Als uw computer een trage verbinding met de Face-service heeft, is dit van invloed op de reactietijd van servicemethoden.

Oplossingen:
- Wanneer u uw Face-abonnement maakt, moet u ervoor zorgen dat u de regio kiest die het dichtst bij uw toepassing ligt.
- Als u meerdere servicemethoden wilt aanroepen, kunt u deze parallel aanroepen als uw toepassingsontwerp dit toestaat. Zie de vorige sectie voor een voorbeeld.
- Als langere latentie van invloed is op de gebruikerservaring, kiest u een time-outdrempel (bijvoorbeeld maximaal 5s) voordat u de API-aanroep opnieuw gaat proberen.

## <a name="next-steps"></a>Volgende stappen

In deze handleiding hebt u geleerd hoe u de latentie kunt beperken bij het gebruik van de Face-service. Hierna leert u hoe u kunt opschalen van bestaande PersonGroup- en FaceList-objecten naar respectievelijk LargePersonGroup- en LargeFaceList-objecten.

> [!div class="nextstepaction"]
> [Voorbeeld: De functie Grootschalig gebruiken](how-to-use-large-scale.md)

## <a name="related-topics"></a>Verwante onderwerpen

- [Referentiedocumentatie (REST)](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
- [Referentiedocumentatie (.NET SDK)](/dotnet/api/overview/azure/cognitiveservices/client/faceapi)
