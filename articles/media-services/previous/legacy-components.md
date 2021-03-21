---
title: Azure Media Services verouderde onderdelen | Microsoft Docs
description: In dit onderwerp worden Azure Media Services oudere onderdelen beschreven.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 3487e735e48cb5067515762d6276429ca81e43fe
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103008736"
---
# <a name="azure-media-services-legacy-components"></a>Azure Media Services verouderde onderdelen

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

In de loop van de tijd worden media service onderdelen verbeterd en verouderde onderdelen buiten gebruik gesteld. Dit artikel helpt u bij het migreren van uw toepassing vanuit een verouderd onderdeel naar een actueel onderdeel.
 
## <a name="retirement-plans-of-legacy-components-and-migration-guidance"></a>Pensioen plannen van verouderde onderdelen en migratie richtlijnen

De media processors van *Windows Azure Media Encoder* (WAME) en *Azure Media Encoder* (AAM) zijn afgeschaft.

* [Migreren van Windows Azure Media Encoder naar Media Encoder Standard](migrate-windows-azure-media-encoder.md)
* [Migreren van Azure Media Encoder naar Media Encoder Standard](migrate-azure-media-encoder.md)

De volgende Media Analytics media-processors zijn afgeschaft of binnenkort moeten worden afgeschaft:

  
 
| **Naam van mediaprocessor** | **Buitengebruikstellings datum** | **Aanvullende opmerkingen** |
| --- | --- | ---|
| Azure Media Indexer 2 | 1 januari, 2020 | Deze media processor wordt vervangen door de [Media Services v3 AudioAnalyzerPreset Basic-modus](../latest/analyzing-video-audio-files-concept.md). Zie [Migrate from Azure media indexer 2 to Azure Media Services video indexer](migrate-indexer-v1-v2.md)voor meer informatie. |
| Azure Media Indexer | 1 maart 2023 | Deze media processor wordt vervangen door de [Media Services v3 AudioAnalyzerPreset Basic-modus](../latest/analyzing-video-audio-files-concept.md). Zie [Migrate from Azure media indexer 2 to Azure Media Services video indexer](migrate-indexer-v1-v2.md)voor meer informatie. |
| Bewegings detectie | 1 juni, 2020|Er zijn op dit moment geen vervangings plannen. |
| Video samenvatting |1 juni, 2020|Er zijn op dit moment geen vervangings plannen.|
| Video optische teken herkenning | 1 juni, 2020 |Deze media processor is vervangen door [Azure Media Services video indexer](../video-indexer/index.yml). U kunt ook [Azure Media Services v3 API](../latest/analyzing-video-audio-files-concept.md)gebruiken. <br/>Zie [Azure Media Services v3-voor instellingen en video indexer vergelijken](../video-indexer/compare-video-indexer-with-media-services-presets.md). |
| Gezichtsherkenning | 1 juni, 2020 | Deze media processor is vervangen door [Azure Media Services video indexer](../video-indexer/index.yml). U kunt ook [Azure Media Services v3 API](../latest/analyzing-video-audio-files-concept.md)gebruiken. <br/>Zie [Azure Media Services v3-voor instellingen en video indexer vergelijken](../video-indexer/compare-video-indexer-with-media-services-presets.md). |
| Content Moderator | 1 juni, 2020 |Deze media processor is vervangen door [Azure Media Services video indexer](../video-indexer/index.yml). U kunt ook [Azure Media Services v3 API](../latest/analyzing-video-audio-files-concept.md)gebruiken. <br/>Zie [Azure Media Services v3-voor instellingen en video indexer vergelijken](../video-indexer/compare-video-indexer-with-media-services-presets.md). |

## <a name="next-steps"></a>Volgende stappen

[Migratie richtlijnen voor het overstappen van Media Services versie 2 naar v3](../latest/migrate-v-2-v-3-migration-introduction.md)
