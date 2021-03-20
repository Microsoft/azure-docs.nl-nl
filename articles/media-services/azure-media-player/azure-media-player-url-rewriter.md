---
title: URL-herschrijffunctie van Azure Media Player
description: In Azure Media Player wordt een opgegeven URL van Azure Media Services herschreven om stromen te bieden voor SMOOTH, DASH, HLS v3 en HLS v4.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/20/2020
ms.custom: devx-track-js
ms.openlocfilehash: b037eec13fda0b5ec16a4f2f53ad2a64fb5f8da1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91275554"
---
# <a name="url-rewriter"></a>URL-herschrijver #

Standaard wordt in Azure Media Player een opgegeven URL van Azure Media Services herschreven om stromen te bieden voor SMOOTH, DASH, HLS v3 en HLS v4. Als de bron bijvoorbeeld zoals in het volgende voorbeeld is, wordt in Azure Media Player geprobeerd alle bovenstaande protocollen af te spelen:

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin">
        <source src="//example/path/to/myVideo.ism/manifest" type="application/vnd.ms-sstr+xml" />
    </video>
```

Als u de URL-herschrijffunctie echter niet wilt gebruiken, kunt u hiervoor de `disableUrlRewriter`-eigenschap aan de parameter toevoegen. Dit betekent dat alle informatie die aan de bronnen wordt doorgegeven, rechtstreeks aan de speler worden doorgegeven, zonder te worden aangepast.  Hier ziet u een voorbeeld waarin twee bronnen aan de speler worden toegevoegd, één DASH en één SMOOTH Streaming.

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin">
        <source src="//example/path/to/myVideo.ism/manifest(format=mpd-time-csf)" type="application/dash+xml" data-setup='{"disableUrlRewriter": true}'/>
        <source src="//example/path/to/myVideo.ism/manifest" type="application/vnd.ms-sstr+xml" data-setup='{"disableUrlRewriter": true}'/>
    </video>
```

of

```javascript
    myPlayer.src([
        { src: "//example/path/to/myVideo.ism/manifest(format=mpd-time-csf)", type: "application/dash+xml", disableUrlRewriter: true },
        { src: "//example/path/to/myVideo.ism/manifest", type: "application/vnd.ms-sstr+xml", disableUrlRewriter: true }
    ]);
```

U kunt desgewenst ook de specifieke streamingindelingen opgeven die u door Azure Media Player wilt laten herschrijven met behulp van de `streamingFormats`-parameter. Opties zijn onder andere `DASH`, `SMOOTH`, `HLSv3`, `HLSv4`, `HLS`. Het verschil tussen HLS enerzijds en HLSv3 en v4 anderzijds is dat de HLS-indeling het afspelen van FairPlay-inhoud ondersteunt. v3 en v4 bieden geen ondersteuning voor FairPlay. Dit is handig als u geen leveringsbeleid voor een specifiek protocol tot uw beschikking hebt.  Hier ziet u een voorbeeld waar er geen DASH-protocol is ingeschakeld voor uw asset.

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin">
        <source src="//example/path/to/myVideo.ism/manifest" type="application/vnd.ms-sstr+xml" data-setup='{"streamingFormats": ["SMOOTH", "HLS","HLS-V3", "HLS-V4"] }'/>
    </video>
```

of

```javascript
    myPlayer.src([
        { src: "//example/path/to/myVideo.ism/manifest", type: "application/vnd.ms-sstr+xml", streamingFormats: ["SMOOTH", "HLS","HLS-V3", "HLS-V4"]},
    ]);
```

De bovenstaande twee kunnen gezamenlijk worden gebruikt in meerdere situaties, op basis van uw specifieke asset.

> [!NOTE]
> Widevine-beveiligingsinformatie blijft alleen behouden bij het DASH-protocol.

## <a name="next-steps"></a>Volgende stappen ##

- [Quickstart voor Azure Media Player](azure-media-player-quickstart.md)