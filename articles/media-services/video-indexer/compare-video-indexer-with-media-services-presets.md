---
title: Vergelijking van Video Indexer en Azure Media Services v3-voor waarden
description: In dit artikel worden Video Indexer mogelijkheden en Azure Media Services v3-voor waarden vergeleken.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.subservice: video-indexer
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/24/2020
ms.author: juliako
ms.openlocfilehash: 0961aa3e573e511a6941bc59ddc335f64799abb4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87047342"
---
# <a name="compare-azure-media-services-v3-presets-and-video-indexer"></a>Azure Media Services voorinstellingen voor v3 en Video Indexer vergelijken 

In dit artikel worden de mogelijkheden van **video indexer api's** en **Media Services v3 api's** vergeleken. 

Er is momenteel een overlap ping tussen de functies die worden geboden door de [video indexer api's](https://api-portal.videoindexer.ai/) en de [Media Services v3-api's](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/Encoding.json). De volgende tabel bevat de huidige richt lijnen voor het leren van de verschillen en overeenkomsten. 

## <a name="compare"></a>Vergelijken

|Functie|Video Indexer-Api's |Video-en audio analyse-voor instellingen<br/>in Media Services v3-Api's|
|---|---|---|
|Media Insights|[Verbeterd](video-indexer-output-json-v2.md) |[Basisprincipes](../latest/analyzing-video-audio-files-concept.md)|
|Bewerkingen|Bekijk de volledige lijst met ondersteunde functies: <br/> [Overzicht](video-indexer-overview.md)|Retourneert alleen video inzichten|
|Billing|[Media Services prijzen](https://azure.microsoft.com/pricing/details/media-services/#analytics)|[Media Services prijzen](https://azure.microsoft.com/pricing/details/media-services/#analytics)|
|Naleving|Ga voor de meest recente nalevings updates naar [Azure compliance Offerings.pdf](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942/file/178110/23/Microsoft%20Azure%20Compliance%20Offerings.pdf) en zoek naar "video indexer" om te controleren of het voldoet aan een certificaat van belang.|Ga voor de meest recente nalevings updates naar [Azure compliance Offerings.pdf](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942/file/178110/23/Microsoft%20Azure%20Compliance%20Offerings.pdf) en zoek naar "Media Services" om te controleren of het voldoet aan een certificaat van belang.|
|Gratis proefversie|VS - oost|Niet beschikbaar|
|Beschikbaarheid in regio’s|[Beschik baarheid van Cognitive services per regio](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services) weer geven|Zie [Media Services Beschik baarheid per regio](https://azure.microsoft.com/global-infrastructure/services/?products=media-services).|

## <a name="next-steps"></a>Volgende stappen

[Overzicht van Video Indexer](video-indexer-overview.md)

[Overzicht van Media Services v3](../latest/media-services-overview.md)
