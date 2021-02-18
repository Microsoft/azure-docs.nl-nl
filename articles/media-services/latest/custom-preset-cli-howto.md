---
title: Een aangepaste transform-CLI coderen
description: In dit onderwerp wordt beschreven hoe u Azure Media Services V3 gebruikt om een aangepaste trans formatie te coderen met behulp van Azure CLI.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: how-to
ms.custom: devx-track-azurecli
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: a91123b22eaef4cbaf2e96c93200777c8a85abab
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/18/2021
ms.locfileid: "101092392"
---
# <a name="how-to-encode-with-a-custom-transform---azure-cli"></a>Coderen met een aangepaste trans formatie-Azure CLI

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Wanneer u code ring met Azure Media Services, kunt u snel aan de slag met een van de aanbevolen ingebouwde voor instellingen, op basis van best practices in de branche, zoals wordt getoond in de Snelstartgids voor [streaming-bestanden](stream-files-cli-quickstart.md#create-a-transform-for-adaptive-bitrate-encoding) . U kunt ook een aangepaste voor instelling maken om uw specifieke scenario-of apparaat vereisten te bereiken.

## <a name="considerations"></a>Overwegingen

Bij het maken van aangepaste voor instellingen gelden de volgende overwegingen:

* Alle waarden voor de hoogte en breedte van AVC-inhoud moeten een meervoud van 4 zijn.
* In Azure Media Services v3 bevinden alle coderings bitsnelheden zich in bits per seconde. Dit wijkt af van de voor instellingen met onze v2 Api's, die kilobits/seconde als eenheid hebben gebruikt. Als de bitrate in v2 bijvoorbeeld is opgegeven als 128 (kilobits/seconde), wordt deze in v3 ingesteld op 128000 (bits/seconde).

## <a name="prerequisites"></a>Vereisten

[Een Azure Media Services-account maken](./create-account-howto.md).

Zorg ervoor dat u de naam van de resource groep en de naam van het Media Services account vergeet.

## <a name="define-a-custom-preset"></a>Een aangepaste voor instelling definiëren

In het volgende voor beeld wordt de hoofd tekst van de aanvraag van een nieuwe trans formatie gedefinieerd. We definiëren een set uitvoer die we willen genereren wanneer deze trans formatie wordt gebruikt.

In dit voor beeld voegen we eerst een AacAudio-laag toe voor de audio codering en twee H264Video-lagen voor de video codering. In de video lagen worden labels toegewezen zodat ze kunnen worden gebruikt in de naam van het uitvoer bestand. Vervolgens willen we dat de uitvoer ook miniaturen bevat. In het onderstaande voor beeld geven we installatie kopieën op in PNG-indeling, gegenereerd om 50% van de oplossing van de video-invoer en met drie time Stamps-{25%, 50%, 75} van de lengte van de invoer video. Ten slotte geven we de indeling voor de uitvoer bestanden op: één voor video en audio, en een andere voor de miniaturen. Omdat we meerdere H264Layers hebben, moeten we macro's gebruiken waarmee unieke namen per laag worden geproduceerd. We kunnen een `{Label}` or `{Bitrate}` -macro gebruiken, maar in het voor beeld wordt het voormalige weer gegeven.

We gaan deze trans formatie opslaan in een bestand. In dit voor beeld noemen we het bestand `customPreset.json` .

```json
{
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
```

## <a name="create-a-new-transform"></a>Een nieuwe trans formatie maken  

In dit voor beeld maken we een **trans formatie** die is gebaseerd op de aangepaste voor instelling die we eerder hebben gedefinieerd. Wanneer u een trans formatie maakt, moet u eerst controleren of er al een bestaat. Als de trans formatie bestaat, kunt u deze opnieuw gebruiken. Met de volgende `show` opdracht wordt de `customTransformName` trans formatie geretourneerd als deze bestaat:

```azurecli-interactive
az ams transform show -a amsaccount -g amsResourceGroup -n customTransformName
```

Met de volgende Azure CLI-opdracht wordt de trans formatie gemaakt op basis van de aangepaste voor instelling (eerder gedefinieerd).

```azurecli-interactive
az ams transform create -a amsaccount -g amsResourceGroup -n customTransformName --description "Basic Transform using a custom encoding preset" --preset customPreset.json
```

Als Media Services de trans formatie op de opgegeven video of audio wilt Toep assen, moet u een taak onder die trans formatie indienen. Voor een volledig voor beeld waarin wordt getoond hoe u een taak onder een trans formatie verzendt, raadpleegt u [Quick Start: video bestanden streamen-Azure cli](stream-files-cli-quickstart.md).

## <a name="see-also"></a>Zie ook

[Azure-CLI](/cli/azure/ams)
