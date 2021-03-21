---
title: Volledige installatie van Azure Media Player
description: Meer informatie over het instellen van de Azure Media Player.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: how-to
ms.date: 04/20/2020
ms.custom: devx-track-js
ms.openlocfilehash: b6b239f44311424db2e80c59e2aba639ae3c0000
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98797454"
---
# <a name="azure-media-player-full-setup"></a>Volledige installatie van Azure Media Player #

Azure Media Player is eenvoudig in te stellen. Het duurt slechts enkele ogen blikken voor het direct afspelen van media-inhoud vanaf uw Azure Media Services-account. Er worden ook voor [beelden](https://github.com/Azure-Samples/azure-media-player-samples) gegeven in de map met voor beelden van de release.

## <a name="step-1-include-the-javascript-and-css-files-in-the-head-of-your-page"></a>Stap 1: de Java script-en CSS-bestanden in de kop van uw pagina toevoegen ##

Met Azure Media Player kunt u toegang krijgen tot de scripts van de gehoste CDN-versie. Het wordt vaak aanbevolen om Java script te plaatsen vóór het label voor de eind code `<body>` in plaats van de `<head>` , maar Azure Meia Player bevat een ' HTML5 Shiv ', die in de kop voor oudere versies van Internet Explorer moet zijn om de video code als een geldig element te kunnen respecteren.

> [!NOTE]
> Als u al een HTML5-Shiv gebruikt zoals [modernisering](https://modernizr.com/) , kunt u de Azure Media Player java script overal toevoegen. Zorg echter dat uw versie van de Moderniseringr de Shiv voor video bevat.

### <a name="cdn-version"></a>CDN-versie ###

```html
    <link href="//amp.azure.net/libs/amp/latest/skins/amp-default/azuremediaplayer.min.css" rel="stylesheet">
    <script src= "//amp.azure.net/libs/amp/latest/azuremediaplayer.min.js"></script>
```

> [!IMPORTANT]
> Gebruik **NIET** de `latest` versie in een productieomgeving, omdat deze op verzoek kan worden aangepast. Vervang door `latest` een versie van Azure Media Player. Vervang bijvoorbeeld `latest` door `2.1.1`. Op Azure Media Player-versies kunt u [hier](azure-media-player-changelog.md) een query uitvoeren.

> [!NOTE]
> Sinds de `1.2.0` release is het niet meer nodig om de locatie op te nemen in de technische terugval (de locatie wordt automatisch opgehaald uit het relatieve pad van het azuremediaplayer.min.js-bestand). U kunt de locatie van de technische terugvals wijzigen door het volgende script toe te voegen aan de `<head>` bovenstaande scripts.

> [!NOTE]
> Vanwege de aard van de Flash-en Silverlight-invoeg toepassingen moeten de SWF-en XAP-bestanden worden gehost in een domein zonder gevoelige informatie of gegevens. dit wordt automatisch voor u in rekening gebracht met de Azure CDN gehoste versie.

```javascript
    <script>
      amp.options.flashSS.swf = "//amp.azure.net/libs/amp/latest/techs/StrobeMediaPlayback.2.0.swf"
      amp.options.silverlightSS.xap = "//amp.azure.net/libs/amp/latest/techs/SmoothStreamingPlayer.xap"
    </script>
```

## <a name="step-2-add-an-html5-video-tag-to-your-page"></a>Stap 2: een HTML5-Video code toevoegen aan uw pagina ##

Met Azure Media Player kunt u een HTML5-Video code gebruiken om een video in te sluiten. Azure Media Player leest vervolgens het label en maakt het werk in alle browsers, niet alleen voor de ondersteuning van HTML5-Video. Naast de basis opmaak heeft Azure Media Player een aantal extra onderdelen nodig.

1. Met het `<data-setup>` kenmerk op de `<video>` Azure Media Player vertelt u automatisch de video instellen wanneer de pagina gereed is en leest u de (in JSON-indeling) van het kenmerk.
1. Het `id` kenmerk: moet worden gebruikt en uniek zijn voor elke video op dezelfde pagina.
1. Het `class` kenmerk bevat twee klassen:
    - `azuremediaplayer` Hiermee worden stijlen toegepast die vereist zijn voor de functionaliteit van Azure Media Player-gebruikers interface
    - `amp-default-skin` de standaard weergave Toep assen op de HTML5-besturings elementen
1. De `<source>` bevat twee vereiste kenmerken
    - `src` het kenmerk kan een bestand met de extensie **. ISM/manifest* van Azure Media Services toevoegen, Azure Media Player automatisch de URL'S voor Dash, Smooth en HLS toevoegen aan de speler
    - `type` het kenmerk is het vereiste MIME-type van de stroom. Het MIME-type dat is gekoppeld aan *'. ISM/manifest '* is *' application/vnd. MS-sstr + XML '*
1. Het *optionele* `<data-setup>` kenmerk op de `<source>` vertelt Azure Media Player als er een uniek afleverings beleid voor de stream van Azure Media Services is, inclusief, maar niet beperkt tot, het versleutelings type (AES of PlayReady, Widevine of Fairplay) en het token.

Kenmerken, instellingen, bronnen en sporen op exact dezelfde manier opnemen/uitsluiten als voor HTML5-Video.

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin" autoplay controls width="640" height="400" poster="poster.jpg" data-setup='{"techOrder": ["azureHtml5JS", "flashSS", "html5FairPlayHLS","silverlightSS", "html5"], "nativeControlsForTouch": false}'>
        <source src="http://amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest" type="application/vnd.ms-sstr+xml" />
        <p class="amp-no-js">
            To view this video please enable JavaScript, and consider upgrading to a web browser that supports HTML5 video
        </p>
    </video>
```

De grote knop afspelen bevindt zich standaard in de linkerbovenhoek, zodat deze niet de interessante onderdelen van de poster bedekt. Als u liever de grote knop afspelen wilt centreren, kunt u een extra `amp-big-play-centered` `class` aan uw `<video>` element toevoegen.

### <a name="alternative-setup-for-dynamically-loaded-html"></a>Alternatieve installatie voor dynamisch geladen HTML ###

Als uw webpagina of toepassing de video code dynamisch laadt (Ajax, appendChild, enzovoort), zodat deze mogelijk niet bestaat wanneer de pagina wordt geladen, moet u de speler hand matig instellen in plaats van te vertrouwen op het kenmerk voor het instellen van de gegevens. Als u dit wilt doen, verwijdert u eerst het kenmerk data-Setup uit de tag, zodat er geen Verwar ring is als de speler is geïnitialiseerd. Voer vervolgens de volgende Java script uit nadat de Azure Media Player java script is geladen en nadat de video code in het DOM is geladen.

```javascript
    var myPlayer = amp('vid1', { /* Options */
            techOrder: ["azureHtml5JS", "flashSS", "html5FairPlayHLS","silverlightSS", "html5"],
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
        src: "http://samplescdn.origin.mediaservices.windows.net/e0e820ec-f6a2-4ea2-afe3-1eed4e06ab2c/AzureMediaServices_Overview.ism/manifest",
        type: "application/vnd.ms-sstr+xml"
    }]);
```

Het eerste argument in de `amp` functie is de id van uw video-tag. Vervang deze door uw eigen naam.

Het tweede argument is een Options-object. Hiermee kunt u aanvullende opties instellen, zoals u kunt doen met het kenmerk data-setup.

Het derde argument is een ' gereed ' call back. Als Azure Media Player is geïnitialiseerd, wordt deze functie aangeroepen. In het geval van een ' This '-object verwijst naar het Player-exemplaar.

In plaats van een element-ID te gebruiken, kunt u ook een verwijzing naar het element zelf door geven.

```javascript

    amp(document.getElementById('example_video_1'), {/*Options*/}, function() {
        //This is functionally the same as the previous example.
    });
    myPlayer.src([{ src: "//example/path/to/myVideo.ism/manifest", type: "application/vnd.ms-sstr+xml"]);
```

## <a name="next-steps"></a>Volgende stappen ##

- [Quickstart voor Azure Media Player](azure-media-player-quickstart.md)