---
title: Overzicht van media spelers voor Media Services
description: Welke media spelers kan ik gebruiken met Azure Media Services? Azure Media Player, Schuda en Video.js tot nu toe.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 3/08/2021
ms.openlocfilehash: 43d0a8ee82a84a57be7c8ded9e7f5afc172bd9d6
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2021
ms.locfileid: "106282402"
---
# <a name="media-players-for-media-services"></a>Media spelers voor Media Services

U kunt verschillende media spelers gebruiken met Media Services.

## <a name="azure-media-player"></a>Azure Media Player

Azure Media Player is een video speler voor een groot aantal browsers en apparaten. Azure Media Player maakt gebruik van industriestandaarden zoals HTML5, MSE (Media Source Extensions) en EME (Encrypted Media Extensions) om een geavanceerde adaptieve streamingervaring te bieden. Wanneer deze standaarden niet beschikbaar zijn op een apparaat of in een browser, Azure Media Player gebruikt Flash en Silverlight als terugval technologie. Ongeacht de afspeel technologie die wordt gebruikt, hebben ontwikkel aars een uniforme java script-interface voor toegang tot Api's. Inhoud die wordt geleverd door Azure Media Services kan worden gespeeld over een breed scala aan apparaten en browsers zonder extra inspanning.

Raadpleeg de [Azure Media Player-documentatie](../azure-media-player/azure-media-player-overview.md).

## <a name="shaka"></a>Schud

Shake-speler is een open-source java script-bibliotheek voor adaptieve media. Hiermee worden adaptieve media-indelingen (zoals streepje en HLS) afgespeeld in een browser, zonder gebruik te maken van plugins of Flash. In plaats daarvan gebruikt de Shake-speler de open-webstandaarden media bron extensies en versleutelde Media-extensies.

Zie [de Shake-speler gebruiken met Azure Media Services](player-shaka-player-how-to.md).

## <a name="videojs"></a>Video.js

Video.js is een video speler waarmee adaptieve media-indelingen (zoals DASH en HLS) in een browser worden afgespeeld. Video.js maakt gebruik van de open Web Standards MediaSource Extensions en versleutelde Media-extensies. Het biedt ondersteuning voor het afspelen van video op Desk tops en mobiele apparaten. De officiële documentatie vindt u op https://docs.videojs.com/ .

Zie [de Video.js Player gebruiken met Azure Media Services](player-how-to-video-js-player.md).


## <a name="next-steps"></a>Volgende stappen ##

- [Quickstart voor Azure Media Player](../azure-media-player/azure-media-player-quickstart.md)