---
title: Het kenmerk HeadPose gebruiken
titleSuffix: Azure Cognitive Services
description: Ontdek hoe u het HeadPose-kenmerk kunt gebruiken om de gezichtsrechthoek automatisch te draaien of hoofdbewegingen in een videofeed te detecteren.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 02/23/2021
ms.author: pafarley
ms.custom: devx-track-csharp
ms.openlocfilehash: cec466fc420e435159e2882d7b39ef6d03eeea7c
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101733479"
---
# <a name="use-the-headpose-attribute"></a>Het kenmerk HeadPose gebruiken

In deze handleiding ziet u hoe u het kenmerk HeadPose van een gedetecteerd gezicht kunt gebruiken om enkele belangrijke scenario's mogelijk te maken.

## <a name="rotate-the-face-rectangle"></a>De gezichtsrechthoek draaien

De gezichtsrechthoek, geretourneerd met elk gedetecteerd gezicht, markeert de locatie en grootte van het gezicht in de afbeelding. De rechthoek wordt standaard altijd uitgelijnd met de afbeelding (de zijden zijn verticaal en horizontaal); dit kan inefficiënt zijn voor het inkaderen van gezichten in een hoek. In situaties waar u op een programmatische manier gezichten wilt bijsnijden in een afbeelding, is het beter om de rechthoek te kunnen draaien om bij te snijden.

De voorbeeld-app [Cognitive Services Face WPF](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/Cognitive-Services-Face-WPF) gebruikt het kenmerk HeadPose om de gedetecteerde gezichtsrechthoeken te draaien.

### <a name="explore-the-sample-code"></a>De voorbeeldcode verkennen

U kunt de gezichtsrechthoek programmatisch draaien met het kenmerk HeadPose. Als u dit kenmerk opgeeft tijdens het detecteren van gezichten (zie [Gezichten detecteren](HowtoDetectFacesinImage.md)), dan kunt u er later alsnog een query op uitvoeren. Met de volgende methode van de [Cognitive Services Face WPF](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/Cognitive-Services-Face-WPF)-app wordt een lijst met **DetectedFace**-objecten gebruikt en wordt een lijst met **[Face](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/app-samples/Cognitive-Services-Face-WPF/Sample-WPF/Controls/Face.cs)** -objecten weergegeven. **Face** hier is een aangepaste klasse waarmee gezichtsgegevens worden opgeslagen, met inbegrip van de bijgewerkte rechthoekcoördinaten. Nieuwe waarden worden berekend voor **bovenkant**, **links**, **breedte** en **hoogte**, en een nieuw veld **FaceAngle** geeft de rotatie op.

```csharp
/// <summary>
/// Calculate the rendering face rectangle
/// </summary>
/// <param name="faces">Detected face from service</param>
/// <param name="maxSize">Image rendering size</param>
/// <param name="imageInfo">Image width and height</param>
/// <returns>Face structure for rendering</returns>
public static IEnumerable<Face> CalculateFaceRectangleForRendering(IList<DetectedFace> faces, int maxSize, Tuple<int, int> imageInfo)
{
    var imageWidth = imageInfo.Item1;
    var imageHeight = imageInfo.Item2;
    var ratio = (float)imageWidth / imageHeight;
    int uiWidth = 0;
    int uiHeight = 0;
    if (ratio > 1.0)
    {
        uiWidth = maxSize;
        uiHeight = (int)(maxSize / ratio);
    }
    else
    {
        uiHeight = maxSize;
        uiWidth = (int)(ratio * uiHeight);
    }

    var uiXOffset = (maxSize - uiWidth) / 2;
    var uiYOffset = (maxSize - uiHeight) / 2;
    var scale = (float)uiWidth / imageWidth;

    foreach (var face in faces)
    {
        var left = (int)(face.FaceRectangle.Left * scale + uiXOffset);
        var top = (int)(face.FaceRectangle.Top * scale + uiYOffset);

        // Angle of face rectangles, default value is 0 (not rotated).
        double faceAngle = 0;

        // If head pose attributes have been obtained, re-calculate the left & top (X & Y) positions.
        if (face.FaceAttributes?.HeadPose != null)
        {
            // Head pose's roll value acts directly as the face angle.
            faceAngle = face.FaceAttributes.HeadPose.Roll;
            var angleToPi = Math.Abs((faceAngle / 180) * Math.PI);

            // _____       | / \ |
            // |____|  =>  |/   /|
            //             | \ / |
            // Re-calculate the face rectangle's left & top (X & Y) positions.
            var newLeft = face.FaceRectangle.Left +
                face.FaceRectangle.Width / 2 -
                (face.FaceRectangle.Width * Math.Sin(angleToPi) + face.FaceRectangle.Height * Math.Cos(angleToPi)) / 2;

            var newTop = face.FaceRectangle.Top +
                face.FaceRectangle.Height / 2 -
                (face.FaceRectangle.Height * Math.Sin(angleToPi) + face.FaceRectangle.Width * Math.Cos(angleToPi)) / 2;

            left = (int)(newLeft * scale + uiXOffset);
            top = (int)(newTop * scale + uiYOffset);
        }

        yield return new Face()
        {
            FaceId = face.FaceId?.ToString(),
            Left = left,
            Top = top,
            OriginalLeft = (int)(face.FaceRectangle.Left * scale + uiXOffset),
            OriginalTop = (int)(face.FaceRectangle.Top * scale + uiYOffset),
            Height = (int)(face.FaceRectangle.Height * scale),
            Width = (int)(face.FaceRectangle.Width * scale),
            FaceAngle = faceAngle,
        };
    }
}
```

### <a name="display-the-updated-rectangle"></a>De bijgewerkte rechthoek weergeven

Hier kunt u de geretourneerde **Face**-objecten gebruiken in uw weergave. De volgende regels van [FaceDetectionPage.xaml](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/app-samples/Cognitive-Services-Face-WPF/Sample-WPF/Controls/FaceDetectionPage.xaml) laten zien hoe de nieuwe rechthoek wordt weergegeven op basis van deze gegevens:

```xaml
 <DataTemplate>
    <Rectangle Width="{Binding Width}" Height="{Binding Height}" Stroke="#FF26B8F4" StrokeThickness="1">
        <Rectangle.LayoutTransform>
            <RotateTransform Angle="{Binding FaceAngle}"/>
        </Rectangle.LayoutTransform>
    </Rectangle>
</DataTemplate>
```

## <a name="detect-head-gestures"></a>Hoofdbewegingen detecteren

U kunt hoofdbewegingen, zoals knikken en hoofd schudden, detecteren door HeadPose-wijzigingen in realtime bij te houden. U kunt deze functie als een aangepaste aanwezigheidsdetector gebruiken.

Detectie op basis van de aanwezigheid is de taak om te bepalen of een onderwerp een echte persoon is en geen afbeelding of videoweergave. Een detector voor hoofdbewegingen kan worden gebruikt als een manier om de aanwezigheid te controleren, met name in plaats van een afbeeldingsweergave van een persoon.

> [!CAUTION]
> Als u hoofdbewegingen in realtime wilt detecteren, moet u de Face-API op hoge snelheid aanroepen (meer dan één keer per seconde). Dit is niet mogelijk als u een gratis abonnement (f0) hebt. Als u een betaald abonnement hebt, moet u ervoor zorgen dat u de kosten hebt berekend voor het uitvoeren van snelle API-aanroepen voor de detectie van hoofdbewegingen.

Zie het [Face HeadPose-voorbeeld](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceAPIHeadPoseSample) op GitHub voor een werkend voorbeeld van detectie van hoofdbewegingen.

## <a name="next-steps"></a>Volgende stappen

Zie de [Cognitive Services Face WPF](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/Cognitive-Services-Face-WPF)-app op GitHub voor een werkend voorbeeld van gedraaide gezichtsrechthoeken. U kunt ook de [Face HeadPose-voorbeeld](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples)app bekijken, die het kenmerk HeadPose in realtime volgt om hoofdbewegingen te detecteren.