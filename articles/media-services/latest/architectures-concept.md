---
title: Media Services-architecturen
description: In dit artikel worden architecturen voor Media Services beschreven.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 11/20/2020
ms.author: inhenkel
ms.openlocfilehash: ad464eb1c0b6dec694c7c40868a0f95fcfeaf6e8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98891485"
---
# <a name="media-services-architectures"></a>Media Services-architecturen

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

## <a name="live-streaming-digital-media"></a>Digitale media voor live streamen

Met een oplossing voor live streamen kunt u video in realtime vastleggen en deze in realtime uitzenden aan consumenten, zoals interviews, conferenties en sport evenementen online. In deze oplossing wordt video vastgelegd door een video camera en verzonden naar een eind punt voor kanaal invoer. Het kanaal ontvangt de Live invoer stroom en maakt deze beschikbaar voor streaming via een streaming-eind punt naar een webbrowser of mobiele app. Het kanaal biedt ook een preview-eind punt voor bewaking om uw stroom te bekijken en te valideren vóór verdere verwerking en levering. Het kanaal kan ook de opgenomen inhoud opnemen en opslaan, zodat deze later kan worden gestreamd (video-on-demand).

Deze oplossing is gebouwd op basis van de door Azure beheerde services: Media Services en Content Delivery Network. Deze services worden uitgevoerd in een omgeving met hoge Beschik baarheid, patches en worden ondersteund, zodat u zich kunt concentreren op uw oplossing in plaats van de omgeving waarin ze worden uitgevoerd in.

Zie [digitale media live streamen](/azure/architecture/solution-ideas/articles/digital-media-live-stream) in het Azure Architecture Center.

## <a name="video-on-demand-digital-media"></a>Digitale video-on-demandmedia

Een eenvoudige video-on-demand-oplossing die u de mogelijkheid biedt om opgenomen video-inhoud, zoals films, Nieuws clips, sport segmenten, trainings Video's en zelf studies voor klant ondersteuning, te streamen naar elk eindpunt apparaat, mobiele toepassing of bureaublad browser die geschikt is voor video. Video bestanden worden geüpload naar Azure Blob-opslag, gecodeerd naar een standaard indeling met meerdere bitrates en vervolgens gedistribueerd via alle belangrijkste protocollen voor de HLS (MPEG-DASH, Smooth) naar de Azure Media Player-client.

Deze oplossing is gebouwd op basis van de door Azure beheerde services: Blob Storage, Content Delivery Network en Azure Media Player. Deze services worden uitgevoerd in een omgeving met hoge Beschik baarheid, patches en worden ondersteund, zodat u zich kunt concentreren op uw oplossing in plaats van de omgeving waarin ze worden uitgevoerd in.

Zie [video-on-demand digitale media](/azure/architecture/solution-ideas/articles/digital-media-video) in het Azure Architecture Center.

## <a name="gridwich-media-processing-system"></a>Gridwich-mediaverwerkingssysteem

Het Gridwich-systeem bestelt aanbevolen procedures voor het verwerken en leveren van media-assets op Azure. Hoewel het Gridwich-systeem een specifiek medium is, kunnen de bericht verwerking en het gebeurtenis kader van toepassing zijn op elke stateless werk stroom voor gebeurtenis verwerking.

Zie het [Gridwich-media verwerkings systeem](/azure/architecture/reference-architectures/media-services/gridwich-architecture) in het Azure Architecture Center.

## <a name="next-steps"></a>Volgende stappen

> [Overzicht van Azure Media Services](media-services-overview.md)