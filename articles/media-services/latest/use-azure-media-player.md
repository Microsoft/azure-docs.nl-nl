---
title: Afspelen met Azure Media Player-Azure | Microsoft Docs
description: Azure Media Player is een web-videospeler voor het afspelen van media-inhoud van Microsoft Azure Media Services op een groot aantal browsers en apparaten.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 07/17/2019
ms.author: inhenkel
ms.openlocfilehash: a3b32110a7236d47946c3a1f901aaedf51b47775
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96022214"
---
# <a name="playback-with-azure-media-player"></a>Afspelen met Azure Media Player

Azure Media Player is een web-videospeler voor het afspelen van media-inhoud van Microsoft Azure Media Services op een groot aantal browsers en apparaten. Azure Media Player maakt gebruik van industriestandaarden zoals HTML5, Media Source Extensions (MSE) en Encrypted Media Extensions (EME) om een geavanceerde adaptieve streaming-ervaring te bieden. Wanneer deze standaarden niet beschikbaar zijn op een apparaat of in een browser, valt Azure Media Player terug op Flash- en Silverlight-technologie. Ontwikkelaars hebben altijd toegang tot API's via een geïntegreerde JavaScript-interface, ongeacht de afspeeltechnologie die wordt gebruikt. Dit maakt het mogelijk dat inhoud die wordt aangeboden door Azure Media Services over een breed scala aan apparaten en browsers zonder extra inspanning kan worden afgespeeld.

Met Microsoft Azure Media Services kan inhoud worden geleverd met HLS, DASH, Smooth Streaming streaming-indelingen voor het afspelen van inhoud. Azure Media Player houdt rekening met deze verschillende indelingen en speelt automatisch de beste koppeling af op basis van de mogelijkheden van het platform of de browser. Media Services biedt ook dynamische versleuteling van assets met PlayReady-versleuteling of AES-128 bits-envelop versleuteling. Azure Media Player kan met PlayReady en AES-128-bits versleutelde inhoud ontsleutelen wanneer het op de juiste wijze is geconfigureerd.

> [!NOTE]
> HTTPS Play is vereist voor Widevine versleutelde inhoud.

## <a name="use-azure-media-player-demo-page"></a>Azure Media Player demo pagina gebruiken

### <a name="start-using"></a>Beginnen met gebruiken

U kunt de [Azure Media Player-demo pagina](https://aka.ms/azuremediaplayer) gebruiken om Azure Media Services-voor beelden of uw eigen stream af te spelen.  

Plak een andere URL en druk op **bijwerken** om een nieuwe video af te spelen.

Als u verschillende afspeel opties wilt configureren (bijvoorbeeld Tech, taal of versleuteling), drukt u op **Geavanceerde opties**.

![Azure Media Player](./media/azure-media-player/home-page.png)

### <a name="monitor-diagnostics-of-a-video-stream"></a>Diagnostische gegevens van een video stroom bewaken

U kunt de [Azure Media Player-demo pagina](https://aka.ms/azuremediaplayer) gebruiken om de diagnose van een video stroom te bewaken.

![Diagnostische gegevens Azure Media Player](./media/azure-media-player/diagnostics.png)

## <a name="set-up-azure-media-player-in-your-html"></a>Azure Media Player in uw HTML instellen

Azure Media Player is eenvoudig in te stellen. Het kan even duren voordat de media-inhoud wordt afgespeeld vanuit uw Media Services-account. Raadpleeg de [Azure Media Player-documentatie](../azure-media-player/azure-media-player-overview.md) voor meer informatie over het instellen en configureren van Azure Media Player.

## <a name="additional-notes"></a>Aanvullende opmerkingen

* Widevine is een service van Google Inc. en is onderworpen aan de servicevoorwaarden en het privacybeleid van Google Inc.

## <a name="next-steps"></a>Volgende stappen

* [Documentatie voor Azure Media Player](../azure-media-player/azure-media-player-overview.md)
* [Azure Media Player-voor beelden](https://github.com/Azure-Samples/azure-media-player-samples)