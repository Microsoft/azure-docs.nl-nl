---
title: Video bestanden bijsnijden met Media Services-.NET | Microsoft Docs
description: Bijsnijden is het proces van het selecteren van een rechthoekig venster binnen het video frame en het coderen van alleen de pixels in het venster. In dit onderwerp wordt beschreven hoe u video bestanden bijsnijdt met Media Services met behulp van .NET.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: how-to
ms.date: 03/24/2021
ms.author: inhenkel
ms.openlocfilehash: 43d0e1e3b8c0eaa37a3b09557b333c3abafe8b63
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105572373"
---
# <a name="how-to-crop-video-files-with-media-services---net"></a>Video bestanden bijsnijden met Media Services-.NET

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

U kunt Media Services gebruiken om een invoer video te bijsnijden. Bijsnijden is het proces van het selecteren van een rechthoekig venster binnen het video frame en het coderen van alleen de pixels in het venster. Het volgende diagram illustreert het proces.

## <a name="pre-processing-stage"></a>Pre-verwerkings fase

Bijsnijden is een pre-verwerkings fase, waardoor de *bijsnijd parameters* in de voor instelling voor versleuteling van toepassing zijn op de *invoer* video. Encoding is een volgende fase en de instellingen voor breedte/hoogte zijn van toepassing op de *vooraf verwerkte* video en niet op de oorspronkelijke video. Ga als volgt te werk bij het ontwerpen van uw voor instelling:

1. De para meters voor bijsnijden selecteren op basis van de oorspronkelijke invoer video
1. Selecteer de versleutelings instellingen op basis van de bijgesneden video.

> [!WARNING]
> Als u de versleutelings instellingen niet overeenkomt met de bijgesneden video, is de uitvoer niet zoals verwacht.

Uw invoer video heeft bijvoorbeeld een resolutie van 1920 pixels (16:9 hoogte-breedte verhouding), maar heeft zwarte balken aan de linkerkant en aan de rechter kant, zodat alleen een 4:3-venster of 1440x1080 pixels actieve video bevatten. U kunt de zwarte balken bijsnijden en het gebied 1440x1080 coderen.

## <a name="transform-code"></a>Code transformeren

Het volgende code fragment laat zien hoe u een trans formatie in .NET schrijft om Video's te bijsnijden.  In de code wordt ervan uitgegaan dat u een lokaal bestand hebt waarmee u kunt werken.

- Links is de meest linkse locatie van de bijsnijd.
- Bovenaan is de meest voorkomende locatie van de bijsnijd.
- Width is de uiteindelijke breedte van de bijsnijd.
- Height is de uiteindelijke hoogte van de bijsnijd.

```dotnet
var preset = new StandardEncoderPreset

    {

        Filters = new Filters

        {                   

            Crop = new Rectangle

            {

                Left = "200",

                Top = "200",

                Width = "1280",

                Height = "720"

            }

        },

        Codecs =

        {

            new AacAudio(),

            new H264Video()

            {

                Layers =

                {                           

                    new H264Layer

                    {

                        Bitrate = 1000000,

                        Width = "1280",

                        Height = "720"

                    }

                }

            }

        },

        Formats =

        {

            new Mp4Format

            {

                FilenamePattern = "{Basename}_{Bitrate}{Extension}"

            }

        }

    }

```
