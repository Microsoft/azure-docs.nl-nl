---
title: Overzicht van Azure Media Player
description: Meer informatie over de Azure Media Player.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/20/2020
ms.openlocfilehash: 9b99e24e47da69aa0f081e056230f4b0c1aa6bb1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93321735"
---
# <a name="azure-media-player-overview"></a>Overzicht van Azure Media Player #

Azure Media Player is een webvideospeler waarmee media-inhoud uit [Microsoft Azure Media Services](https://azure.microsoft.com/services/media-services/) kan worden afgespeeld op een groot aantal browsers en apparaten. Azure Media Player maakt gebruik van industriestandaarden zoals HTML5, MSE (Media Source Extensions) en EME (Encrypted Media Extensions) om een geavanceerde adaptieve streamingervaring te bieden.  Wanneer deze standaarden niet beschikbaar zijn op een apparaat of in een browser, valt Azure Media Player terug op Flash- en Silverlight-technologie. Ontwikkelaars hebben, ongeacht welke technologie voor afspelen wordt gebruikt, een uniforme JavaScript-interface voor toegang tot API's. Hiermee kan inhoud uit Azure Media Services worden afgespeeld op een grote verscheidenheid aan apparaten en browsers, zonder extra moeite.

Met Microsoft Azure Media Services kan inhoud worden geleverd met DASH, Smooth Streaming en HLS-streaming-indelingen voor het afspelen van inhoud. Azure Media Player houdt rekening met deze verschillende indelingen en speelt automatisch de beste koppeling af op basis van de mogelijkheden van het platform of de browser. Microsoft Azure Media Services biedt ook dynamische versleuteling van activa met Common Encryption (PlayReady of Widevine) of AES-128-bits-envelopversleuteling. Azure Media Player kan met PlayReady en AES-128-bits versleutelde inhoud ontsleutelen wanneer het op de juiste wijze is geconfigureerd.  Zie de sectie [Beveiligde inhoud](azure-media-player-protected-content.md) voor uitleg over het configureren van de speler.

Dien aanvragen voor nieuwe functies, ideeën of feedback in bij [UserVoice voor Azure Media Player](https://aka.ms/ampuservoice). Als u specifieke problemen of vragen hebt of bugs vindt, stuur dan een e-mail naar ampinfo@microsoft.com.

> [!NOTE]
> Houd er rekening mee dat Azure Media Player alleen mediastromen van Azure Media Services ondersteunt.

## <a name="license"></a>Licentie ##

Azure Media Player is gelicentieerd en onderhevig aan de voorwaarden van de Gebruiksrechtovereenkomst voor Microsoft-software voor Azure Media Player. Zie het [licentiebestand](/legal/azure-media-player/azure-media-player-license) voor alle voorwaarden. Raadpleeg de [Privacyverklaring](https://www.microsoft.com/en-us/privacystatement/default.aspx) voor meer informatie.

Copyright 2015 Microsoft Corporation.

## <a name="next-steps"></a>Volgende stappen ##

- [Quickstart voor Azure Media Player](azure-media-player-quickstart.md)
