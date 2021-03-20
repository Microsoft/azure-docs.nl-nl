---
title: Quickstart voor Azure Media Player
description: Leer de basisstappen om Azure Media Player in te stellen.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: quickstart
ms.date: 04/20/2020
ms.openlocfilehash: 1634731381be6ebbb8750746f88b8ff9e761025d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98879187"
---
# <a name="azure-media-player-quickstart"></a>Quickstart voor Azure Media Player
Azure Media Player is eenvoudig in te stellen. Het duurt slechts enkele minuten om media-inhoud via uw Azure Media Services-account te kunnen afspelen. In deze sectie worden de basisstappen beschreven zonder op de details in te gaan. In de volgende secties wordt dieper ingegaan op het instellen en configureren van Azure Media Player.  Voeg de volgende bijlagen toe aan de `<head>` van uw document:

```html
    <link href="//amp.azure.net/libs/amp/latest/skins/amp-default/azuremediaplayer.min.css" rel="stylesheet">
    <script src= "//amp.azure.net/libs/amp/latest/azuremediaplayer.min.js"></script>
```

> [!IMPORTANT]
> Gebruik **NIET** de `latest` versie in een productieomgeving, omdat deze op verzoek kan worden aangepast. Vervang `latest` door een versie van Azure Media Player, vervang bijvoorbeeld `latest` door `1.0.0`. Op Azure Media Player-versies kunt u [hier](azure-media-player-changelog.md) een query uitvoeren.

## <a name="use-the-video-element"></a>Het video-element gebruiken

Gebruik vervolgens het `<video>`-element zoals u dat normaal zou doen, maar met een extra `data-setup`-kenmerk dat opties bevat. Deze opties kunnen willekeurige Azure Media Services-opties bevatten in een geldig JSON-object.

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin" autoplay controls width="640" height="400" poster="poster.jpg" data-setup='{"nativeControlsForTouch": false}'>
        <source src="http://amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest" type="application/vnd.ms-sstr+xml" />
        <p class="amp-no-js">
            To view this video please enable JavaScript, and consider upgrading to a web browser that supports HTML5 video
        </p>
    </video>
```

Als u geen automatische installatie wilt gebruiken, kunt u het `data-setup`-kenmerk weglaten en handmatig een video-element initialiseren.

```html
    var myPlayer = amp('vid1', { /* Options */
            "nativeControlsForTouch": false,
            autoplay: false,
            controls: true,
            width: "640",
            height: "400",
            poster: ""
        }, function() {
              console.log('Good to go!');
               // add an event listener
              this.addEventListener('ended', function() {
                console.log('Finished!');
            }
          }
    );
    myPlayer.src([{
        src: "http://amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest",
        type: "application/vnd.ms-sstr+xml"
    }]);
```

## <a name="next-steps"></a>Volgende stappen ##

- [Volledige installatie van Azure Media Player](./azure-media-player-full-setup.md)