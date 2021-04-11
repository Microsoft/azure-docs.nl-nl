---
title: Miniaturen genereren met behulp van Media Encoder Standard .NET
description: In dit artikel wordt beschreven hoe u .NET kunt gebruiken om een Asset te coderen en tegelijkertijd miniaturen te genereren met behulp van Media Encoder Standard.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 12/01/2020
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 135c163c555bc30bea57ccbdf73bf0414a834da2
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2021
ms.locfileid: "106109642"
---
# <a name="how-to-generate-thumbnails-using-encoder-standard-with-net"></a>Miniaturen genereren met behulp van encoder Standard met .NET

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

U kunt Media Encoder Standard gebruiken om een of meer miniaturen te genereren op basis van uw invoer video in [JPEG](https://en.wikipedia.org/wiki/JPEG) -of [PNG](https://en.wikipedia.org/wiki/Portable_Network_Graphics) -afbeeldings bestands indelingen.

## <a name="recommended-reading-and-practice"></a>Aanbevolen Lees-en praktijk

Het is raadzaam dat u bekend raakt met aangepaste trans formaties door [te lezen hoe u codeert met een aangepaste transform-.net](encode-custom-presets-how-to.md).

## <a name="transform-code-example"></a>Code voorbeeld van trans formatie

In het onderstaande code voorbeeld wordt alleen een miniatuur gemaakt.  U moet de volgende para meters instellen:

- **Start** : de positie in de invoer video van waaruit u miniaturen wilt gaan genereren. De waarde kan de ISO 8601-indeling hebben (bijvoorbeeld PT05S om 5 seconden te beginnen) of een aantal frames (bijvoorbeeld 10 om te beginnen bij het tiende frame) of een relatieve waarde voor de stroom duur (bijvoorbeeld 10% om te beginnen bij 10% van de duur van de stroom). Biedt ook ondersteuning voor een macro {best}, waarmee wordt aangegeven dat het coderings programma de beste miniatuur van de eerste paar seconden van de video selecteert en slechts één miniatuur produceert, ongeacht welke andere instellingen voor stap en bereik zijn. De standaard waarde is macro {best}.
- **stap** -de intervallen waarop miniaturen worden gegenereerd. De waarde kan de ISO 8601-indeling hebben (bijvoorbeeld elke vijf seconden voor één afbeelding) of een aantal frames (bijvoorbeeld 30 voor de ene afbeelding elke 30 frames) of een relatieve waarde voor de stroom duur (bijvoorbeeld 10% voor één afbeelding elke 10% van de duur van de stroom). De stap waarde is van invloed op de eerst gegenereerde miniatuur, die mogelijk niet exact hetzelfde is als de begin tijd van de voor instelling voor transformeren. Dit wordt veroorzaakt door de encoder, waarmee wordt geprobeerd de beste miniatuur te selecteren tussen de begin tijd en de positie van de stap vanaf de begin tijd als de eerste uitvoer. Omdat de standaard waarde 10% is, betekent dit dat als de stroom een lange duur heeft, de eerste gegenereerde miniatuur mogelijk ver weg is van de tijd die is opgegeven tijdens het starten. Probeer een redelijke waarde voor stap te selecteren als de eerste miniatuur naar verwachting dicht bij de begin tijd is of stel de bereik waarde in op 1 als er tijdens de begin tijd slechts één miniatuur nodig is.
- **bereik** : de positie ten opzichte van de vooraf ingestelde begin tijd van de trans formatie in de invoer video waarbij het genereren van miniaturen moet worden gestopt. De waarde kan de ISO 8601-indeling hebben (bijvoorbeeld PT5M30S om 5 minuten en 30 seconden vanaf de begin tijd te stoppen), of een aantal frames (bijvoorbeeld 300 om tijdens het starten op het frame 300th te stoppen. Als deze waarde 1 is, betekent dit dat er slechts één miniatuur tijdens de start tijd wordt geproduceerd) of een relatieve waarde aan de stroom duur (bijvoorbeeld 50% om de helft van de stroom duur vanaf de begin tijd te stoppen). De standaard waarde is 100%, wat betekent dat de gegevens aan het einde van de stroom moeten worden gestopt.
- **lagen** : een verzameling uitvoer afbeeldings lagen die moeten worden geproduceerd door de encoder.

```csharp

private static Transform EnsureTransformExists(IAzureMediaServicesClient client, string resourceGroupName, string accountName, string transformName)
{
    // Does a Transform already exist with the desired name? Assume that an existing Transform with the desired name
    // also uses the same recipe or Preset for processing content.
    Transform transform = client.Transforms.Get(resourceGroupName, accountName, transformName);

    if (transform == null)
    {
        // Create a new Transform Outputs array - this defines the set of outputs for the Transform
        TransformOutput[] outputs = new TransformOutput[]
        {
            // Create a new TransformOutput with a custom Standard Encoder Preset
            // This demonstrates how to create custom codec and layer output settings

          new TransformOutput(
                new StandardEncoderPreset(
                    codecs: new Codec[]
                    {
                        // Generate a set of PNG thumbnails
                        new PngImage(
                            start: "25%",
                            step: "25%",
                            range: "80%",
                            layers: new PngLayer[]{
                                new PngLayer(
                                    width: "50%",
                                    height: "50%"
                                )
                            }
                        )
                    },
                    // Specify the format for the output files for the thumbnails
                    formats: new Format[]
                    {
                        new PngFormat(
                            filenamePattern:"Thumbnail-{Basename}-{Index}{Extension}"
                        )
                    }
                ),
                onError: OnErrorType.StopProcessingJob,
                relativePriority: Priority.Normal
            )
        };

        string description = "A transform that includes thumbnails.";
        // Create the custom Transform with the outputs defined above
        transform = client.Transforms.CreateOrUpdate(resourceGroupName, accountName, transformName, outputs, description);
    }

    return transform;
}
```

## <a name="next-steps"></a>Volgende stappen

[Miniaturen genereren met behulp van REST](media-services-generate-thumbnails-rest.md)
