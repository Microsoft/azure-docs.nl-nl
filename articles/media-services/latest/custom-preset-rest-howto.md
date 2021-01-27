---
title: Aangepaste trans formatie coderen met behulp van Media Services v3 REST-Azure
description: In dit onderwerp wordt uitgelegd hoe u Azure Media Services V3 gebruikt voor het coderen van een aangepaste trans formatie met behulp van REST.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: how-to
ms.custom: ''
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: 7a2cfc0db2f95b21c872dd2ed0cf4fbf675a8d6f
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98897608"
---
# <a name="how-to-encode-with-a-custom-transform---rest"></a>Coderen met een aangepaste transform-REST

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Wanneer u code ring met Azure Media Services, kunt u snel aan de slag met een van de aanbevolen ingebouwde voor instellingen, op basis van best practices in de branche, zoals wordt getoond in de zelf studie voor [streaming-bestanden](stream-files-tutorial-with-rest.md#create-a-transform) . U kunt ook een aangepaste voor instelling maken om uw specifieke scenario-of apparaat vereisten te bereiken.

## <a name="considerations"></a>Overwegingen

Bij het maken van aangepaste voor instellingen gelden de volgende overwegingen:

* Alle waarden voor de hoogte en breedte van AVC-inhoud moeten een meervoud van 4 zijn.
* In Azure Media Services v3 bevinden alle coderings bitsnelheden zich in bits per seconde. Dit wijkt af van de voor instellingen met onze v2 Api's, die kilobits/seconde als eenheid hebben gebruikt. Als de bitrate in v2 bijvoorbeeld is opgegeven als 128 (kilobits/seconde), wordt deze in v3 ingesteld op 128000 (bits/seconde).

## <a name="prerequisites"></a>Vereisten 

- [Een Azure Media Services-account maken](./create-account-howto.md). <br/>Zorg ervoor dat u de naam van de resource groep en de naam van het Media Services account vergeet. 
- [Postman configureren voor Azure Media Services rest API-aanroepen](media-rest-apis-with-postman.md).<br/>Zorg ervoor dat u de laatste stap in het onderwerp [Azure AD-Token ophalen](media-rest-apis-with-postman.md#get-azure-ad-token)volgt. 

## <a name="define-a-custom-preset"></a>Een aangepaste voor instelling definiëren

In het volgende voor beeld wordt de hoofd tekst van de aanvraag van een nieuwe trans formatie gedefinieerd. We definiëren een set uitvoer die we willen genereren wanneer deze trans formatie wordt gebruikt. 

In dit voor beeld voegen we eerst een AacAudio-laag toe voor de audio codering en twee H264Video-lagen voor de video codering. In de video lagen worden labels toegewezen zodat ze kunnen worden gebruikt in de naam van het uitvoer bestand. Vervolgens willen we dat de uitvoer ook miniaturen bevat. In het onderstaande voor beeld geven we installatie kopieën op in PNG-indeling, gegenereerd om 50% van de oplossing van de video-invoer en met drie time Stamps-{25%, 50%, 75} van de lengte van de invoer video. Ten slotte geven we de indeling voor de uitvoer bestanden op: één voor video en audio, en een andere voor de miniaturen. Omdat we meerdere H264Layers hebben, moeten we macro's gebruiken waarmee unieke namen per laag worden geproduceerd. We kunnen een `{Label}` or `{Bitrate}` -macro gebruiken, maar in het voor beeld wordt het voormalige weer gegeven.

```json
{
    "properties": {
        "description": "Basic Transform using a custom encoding preset",
        "outputs": [
            {
                "onError": "StopProcessingJob",
                "relativePriority": "Normal",
                "preset": {
                    "@odata.type": "#Microsoft.Media.StandardEncoderPreset",
                    "codecs": [
                        {
                            "@odata.type": "#Microsoft.Media.AacAudio",
                            "channels": 2,
                            "samplingRate": 48000,
                            "bitrate": 128000,
                            "profile": "AacLc"
                        },
                        {
                            "@odata.type": "#Microsoft.Media.H264Video",
                            "keyFrameInterval": "PT2S",
                            "stretchMode": "AutoSize",
                            "sceneChangeDetection": false,
                            "complexity": "Balanced",
                            "layers": [
                                {
                                    "width": "1280",
                                    "height": "720",
                                    "label": "HD",
                                    "bitrate": 3400000,
                                    "maxBitrate": 3400000,
                                    "bFrames": 3,
                                    "slices": 0,
                                    "adaptiveBFrame": true,
                                    "profile": "Auto",
                                    "level": "auto",
                                    "bufferWindow": "PT5S",
                                    "referenceFrames": 3,
                                    "entropyMode": "Cabac"
                                },
                                {
                                    "width": "640",
                                    "height": "360",
                                    "label": "SD",
                                    "bitrate": 1000000,
                                    "maxBitrate": 1000000,
                                    "bFrames": 3,
                                    "slices": 0,
                                    "adaptiveBFrame": true,
                                    "profile": "Auto",
                                    "level": "auto",
                                    "bufferWindow": "PT5S",
                                    "referenceFrames": 3,
                                    "entropyMode": "Cabac"
                                }
                            ]
                        },
                        {
                            "@odata.type": "#Microsoft.Media.PngImage",
                            "stretchMode": "AutoSize",
                            "start": "25%",
                            "step": "25%",
                            "range": "80%",
                            "layers": [
                                {
                                    "width": "50%",
                                    "height": "50%"
                                }
                            ]
                        }
                    ],
                    "formats": [
                        {
                            "@odata.type": "#Microsoft.Media.Mp4Format",
                            "filenamePattern": "Video-{Basename}-{Label}-{Bitrate}{Extension}",
                            "outputFiles": []
                        },
                        {
                            "@odata.type": "#Microsoft.Media.PngFormat",
                            "filenamePattern": "Thumbnail-{Basename}-{Index}{Extension}"
                        }
                    ]
                }
            }
        ]
    }
}

```

## <a name="create-a-new-transform"></a>Een nieuwe trans formatie maken  

In dit voor beeld maken we een **trans formatie** die is gebaseerd op de aangepaste voor instelling die we eerder hebben gedefinieerd. Wanneer u een trans formatie maakt, moet u eerst [Get](/rest/api/media/transforms/get) gebruiken om te controleren of er al een bestaat. Als de trans formatie bestaat, kunt u deze opnieuw gebruiken. 

Selecteer in de verzameling die u hebt gedownload, trans **formaties en taken** -> **transform maken of bijwerken**.

De methode voor het **opnemen** van HTTP-aanvragen is vergelijkbaar met:

```
PUT https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/transforms/:transformName?api-version={{api-version}}
```

Selecteer het tabblad **hoofd tekst** en vervang de hoofd tekst door de JSON-code die u [eerder hebt gedefinieerd](#define-a-custom-preset). Als Media Services de trans formatie op de opgegeven video of audio wilt Toep assen, moet u een taak onder die trans formatie indienen.

Selecteer **Verzenden**. 

Als Media Services de trans formatie op de opgegeven video of audio wilt Toep assen, moet u een taak onder die trans formatie indienen. Zie [zelf studie: video bestanden streamen-rest](stream-files-tutorial-with-rest.md)voor een volledig voor beeld waarin wordt getoond hoe u een taak onder een trans formatie verzendt.

## <a name="next-steps"></a>Volgende stappen

[Andere rest-bewerkingen](/rest/api/media/) weer geven
