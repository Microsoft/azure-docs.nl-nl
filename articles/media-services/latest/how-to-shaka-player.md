---
title: De Shake-speler gebruiken met Azure Media Services
description: In dit artikel wordt uitgelegd hoe u de Shake-speler kunt gebruiken met Azure Media Services
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-js
ms.openlocfilehash: 7b4bda5a439b1e0b35dc6d3af99e0273e4987568
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101091347"
---
# <a name="how-to-use-the-shaka-player-with-azure-media-services"></a>De Shake-speler gebruiken met Azure Media Services

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

## <a name="overview"></a>Overzicht

Shake-speler is een open-source java script-bibliotheek voor adaptieve media. Hiermee worden adaptieve media-indelingen (zoals streepje en HLS) afgespeeld in een browser, zonder gebruik te maken van plugins of Flash. In plaats daarvan gebruikt de Shake-speler de open-webstandaarden media bron extensies en versleutelde Media-extensies.

Het wordt aangeraden om [Mux.js](https://github.com/videojs/mux.js/) als zonder deze te gebruiken, de Shake-speler ondersteunt HLS CMAF-indeling, maar niet HLS TS.

De officiële documentatie vindt u in de [documentatie van Shake-spelers](https://shaka-player-demo.appspot.com/docs/api/tutorial-welcome.html).

## <a name="sample-code"></a>Voorbeeldcode

Voorbeeld code voor dit artikel is beschikbaar op [Azure-samples/Media-Services-3rdParty-Player-samples](https://github.com/Azure-Samples/media-services-3rdparty-player-samples).

## <a name="implementing-the-player"></a>De speler implementeren

Volg deze instructies als u uw eigen exemplaar van de speler wilt implementeren:

1. Maak een `index.html` bestand waarin u de speler gaat hosten. Voeg de volgende regels code toe (u kunt de versies voor nieuwer vervangen indien van toepassing):

    ```html
    <html>
      <head>
        <script src="//cdn.jsdelivr.net/npm/shaka-player@3.0.1/dist/shaka-player.compiled.js"></script>
        <script src="//cdn.jsdelivr.net/npm/mux.js@5.6.3/dist/mux.js"></script>
        <script type="module" src="index.js"></script>
      </head>
      <body>
        <video id="video" controls></video>
      </body>
    </html>
    ```

1. Voeg een Java script-bestand met de volgende code toe:

    ```javascript
    // myScript.js
    shaka.polyfill.installAll();

    var video = document.getElementById('video');
    var player = new shaka.Player(video);
    window.player = player;

    var manifestUrl = 'https://amsplayeraccount-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/sample-vod.ism/manifest(format=m3u8-aapl)';
    player.load(manifestUrl);
    ```

1. Vervang door `manifestUrl` de HLS of de streepjes-URL uit de streaming-Locator van uw asset, die u kunt vinden op de pagina met de streaming-Locator in het Azure Portal.
    ![Url's van streaming-Locators](media/how-to-shaka-player/streaming-urls.png)

1. Voer een server uit (bijvoorbeeld met `npm http-server` ) en uw speler moet werken...

## <a name="set-up-captions"></a>Bijschriften instellen

### <a name="set-up-vod-captions"></a>Bijschriften voor VOD instellen

Voer de volgende regels code uit en vervang door de `captionUrl` Directory. VTT (VTT-bestand moet zich op dezelfde host bevindt om CORS-fout te voor komen), `lang` met de code van twee letters voor taal en `type` met ofwel `caption` `subtitle` :

```javascript
player.configure('streaming.alwaysStreamText', true)
player.load(manifestUrl).then(function(){
        player.addTextTrack(captionUrl, lang, type, 'text/vtt');
        var tracks = player.getTextTracks();
        player.selectTextTrack(tracks[0]);
});
```

### <a name="set-up-live-stream-captions"></a>Ondertiteling van Live Stream instellen

Het inschakelen van bijschriften in Live Stream is geconfigureerd voor het toevoegen van de volgende regel code:

```javascript
player.setTextTrackVisibility(true)
```

## <a name="set-up-token-authentication"></a>Token verificatie instellen

Voer de volgende regels code uit en vervang door `token` de token teken reeks:

```javascript
player.getNetworkingEngine().registerRequestFilter(function (type, request) {
  if (type === shaka.net.NetworkingEngine.RequestType.LICENSE) {
    request.headers['Authorization'] = 'Bearer ' + token;
  }
});
```

## <a name="set-up-aes-128-encryption"></a>AES-128-versleuteling instellen

Shake-speler ondersteunt momenteel geen AES-128-versleuteling.

Een koppeling naar een GitHub- [probleem](https://github.com/google/shaka-player/issues/850) om de status van deze functie te volgen.

## <a name="set-up-drm-protection"></a>DRM-beveiliging instellen

De Shake-speler gebruikt versleutelde Media-extensies (EME), waarvoor een beveiligde URL moet worden gebruikt. Voor het testen van inhoud die is beveiligd met DRM, is het nodig om HTTPS te gebruiken. Als de site gebruikmaakt van HTTPS, moet het manifest en elk segment ook https gebruiken. Dit komt door de vereisten voor gemengde inhoud.

De volg orde van de voor keur voor het Schudda-beheer van de URL ('s) van de licentie server (s):

1. ClearKey config, dat wordt gebruikt voor fout opsporing, moet alle andere gegevens overschrijven. (De toepassing kan nog steeds een ClearKey-licentie server opgeven.)
2. Door de toepassing geconfigureerde servers, indien aanwezig, moeten iets in het manifest overschrijven.
3. Licentie servers die door het manifest worden opgegeven, worden alleen gebruikt als er niets anders is opgegeven.

We kunnen de volgende code gebruiken om de URL van de licentie server voor Widevine of PlayReady op te geven:

```javascript
player.configure({
  drm: {
    servers: {
      "com.widevine.alpha": "YOUR WIDEVINE LICENSE URL",
      "com.microsoft.playready": "YOUR PLAYREADY LICENSE URL"
    }
  }
});

```

Voor alle FairPlay-inhoud moet een server certificaat worden ingesteld. Deze wordt ingesteld in de configuratie van de speler:

```javascript
const req = await fetch("YOUR FAIRPLAY CERTIFICATE URL");
const cert = await req.arrayBuffer();
player.configure('drm.advanced.com\\.apple\\.fps\\.1_0.serverCertificate', new Uint8Array(cert));
```

Zie de [documentatie van Shake-Player DRM-beveiliging](https://shaka-player-demo.appspot.com/docs/api/tutorial-drm-config.html)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

* [De Azure Media Player gebruiken](../azure-media-player/azure-media-player-overview.md)
* [Snelstart: Inhoud versleutelen](encrypt-content-quickstart.md)
