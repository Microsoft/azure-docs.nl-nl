---
title: De Video.js Player gebruiken met Azure Media Services
description: In dit artikel wordt uitgelegd hoe u het HTML-video object en Java script gebruikt met Azure Media Services
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
ms.openlocfilehash: a0b357705de04ed4c2be3223f9dd07f61f75d970
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98954577"
---
# <a name="how-to-use-the-videojs-player-with-azure-media-services"></a>De Video.js Player gebruiken met Azure Media Services

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

## <a name="overview"></a>Overzicht

Video.js is een webvideo speler die is gebouwd voor een HTML5-wereld. Hiermee worden adaptieve media-indelingen (zoals streepje en HLS) afgespeeld in een browser, zonder gebruik te maken van plugins of Flash. Video.js gebruikt in plaats daarvan de MediaSource-extensies voor open Web Standards en versleutelde Media-extensies. Bovendien ondersteunt het afspelen van video op Desk tops en mobiele apparaten.

De officiële documentatie vindt u op [https://docs.videojs.com/](https://docs.videojs.com/) .

## <a name="sample-code"></a>Voorbeeldcode
Voorbeeld code voor dit artikel is beschikbaar op [Azure-samples/Media-Services-3rdParty-Player-samples](https://github.com/Azure-Samples/media-services-3rdparty-player-samples).

## <a name="implement-the-player"></a>De speler implementeren

1. Maak een `index.html` bestand waarin u de speler gaat hosten. Voeg de volgende regels code toe (u kunt de versies voor nieuwer vervangen indien van toepassing):

    ```html
    <html>
      <head>
        <link href="https://vjs.zencdn.net/7.8.2/video-js.css" rel="stylesheet" />
      </head>
      <body>
        <video id="video" class="video-js vjs-default-skin vjs-16-9" controls data-setup="{}">
        </video>

        <script src="https://vjs.zencdn.net/7.8.2/video.js"></script>
        <script src="https://cdn.jsdelivr.net/npm/videojs-contrib-eme@3.7.0/dist/videojs-contrib-eme.min.js"></script>
        <script type="module" src="index.js"></script>
      </body>
    <html>
    ```

2. Voeg een `index.js` bestand toe met de volgende code:

    ```javascript
    var videoJS = videojs("video");
    videoJS.src({
      src: "manifestUrl",
      type: "protocolType",
    });
    ```

3. Vervang door `manifestUrl` de HLS of de streepjes-URL van de streaming-Locator van uw asset die u kunt vinden op de pagina streaming Locator in het Azure Portal.
    ![Url's van streaming-Locators](media/how-to-shaka-player/streaming-urls.png)

4. Vervang door `protocolType` de volgende opties:

    - ' Application/x-mpegURL ' voor HLS-protocollen
    - "toepassing/streepje + XML" voor de STREEPJES protocollen

### <a name="set-up-captions"></a>Bijschriften instellen

Voer de `addRemoteTextTrack` -methode uit en vervang:

- `subtitleKind` met ofwel,, `"captions"` `"subtitles"` `"descriptions"` of `"metadata"`  
- `caption` met het bestandspad. VTT (VTT-bestand moet zich op dezelfde host bevinden om CORS-fout te voor komen)
- `subtitleLang` met de BCP 47-code voor taal, bijvoorbeeld `"eng"` voor Engels of `"es"` Spaans
- `subtitleLabel` met de gewenste weergave naam van het bijschrift

```javascript
videojs.players.video.addRemoteTextTrack({
  kind: subtitleKind,
  src: caption,
  srclang: subtitleLang,
  label: subtitleLabel
});
```

### <a name="set-up-token-authentication"></a>Token verificatie instellen

Het token moet worden ingesteld in het autorisatie veld van de header van de aanvraag. Om problemen met CORS te voor komen, moet dit token alleen worden ingesteld in die aanvragen met `'keydeliver'` in de bijbehorende URL. De volgende code regels moeten het werk doen:

```javascript
setupTokenForDecrypt (options) {
  if (options.uri.includes('keydeliver')) {
    options.headers = options.headers || {}
    options.headers.Authorization = 'Bearer=' + this.getInputToken()
  }

  return options
}
```

De bovenstaande functie moet vervolgens worden gekoppeld aan de `videojs.Hls.xhr.beforeRequest` gebeurtenis.

```javascript
videojs.Hls.xhr.beforeRequest = setupTokenForDecrypt;
```

### <a name="set-up-aes-128-encryption"></a>AES-128-versleuteling instellen

Video.js ondersteunt AES-128-versleuteling zonder extra configuratie. 

> [!NOTE] 
> Er is momenteel een [probleem](https://github.com/videojs/video.js/issues/6717) met de inhoud VERSLEUTELING en HLS/Dash CMAF, die niet kan worden afgespeeld.

### <a name="set-up-drm-protection"></a>DRM-beveiliging instellen

Ter ondersteuning van DRM-beveiliging moet u de [videojs-contrib-EME](https://github.com/videojs/videojs-contrib-eme) officiële extensie toevoegen. Een CDN-versie werkt ook.

1. In het `index.js` bestand dat hierboven wordt vermeld, moet u de uitbrei ding EME initialiseren door toe te voegen `videoJS.eme();` *voordat* u de bron van de video toevoegt:

   ```javascript
    videoJS.eme();
   ```

2. Nu kunt u de Url's van de DRM-services en de Url's van de bijbehorende licenties als volgt definiëren:

   ```javascript
   videoJS.src({
       keySystems: {
           "com.microsoft.playready": "YOUR PLAYREADY LICENSE URL",
           "com.widevine.alpha": "YOUR WIDEVINE LICENSE URL",
           "com.apple.fps.1_0": {
            certificateUri: "YOUR FAIRPLAY CERTIFICATE URL",
            licenseUri: "YOUR FAIRPLAY LICENSE URL"
           }
         }
       })

   ```

#### <a name="acquiring-the-license-url"></a>De licentie-URL ophalen

Als u de licentie-URL wilt ophalen, kunt u het volgende doen:

- Raadpleeg de configuratie van uw DRM-provider
- of, als u het voor beeld gebruikt, raadpleegt u het `output.json` document dat wordt gegenereerd wanneer u het Power shell-script van *setup-vod.ps1* voor VODs of het *start-live.ps1* script voor live streams hebt uitgevoerd. U vindt ook de kinderen in dit bestand.

#### <a name="using-tokenized-drm"></a>Met tokenve DRM gebruiken

U moet de volgende regel toevoegen aan de `src` eigenschap van de speler om de DRM-beveiliging met tokens te ondersteunen:

```javascript
videoJS.src({
src: ...,
emeHeaders: {'Authorization': "Bearer=" + "YOUR TOKEN"},
keySystems: {...
```

## <a name="next-steps"></a>Volgende stappen

- [De Azure Media Player gebruiken](../azure-media-player/azure-media-player-overview.md)  
- [Snelstart: Inhoud versleutelen](encrypt-content-quickstart.md)
