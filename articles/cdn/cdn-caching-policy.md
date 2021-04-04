---
title: Azure CDN cache beleid in Azure Media Services beheren | Microsoft Docs
description: In dit artikel wordt uitgelegd hoe u Azure CDN beleid voor caching beheert in Azure Media Services.
services: media-services,cdn
documentationcenter: .NET
author: juliako
manager: erikre
editor: ''
ms.assetid: be33aecc-6dbe-43d7-a056-10ba911e0e94
ms.service: media-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 02/04/2017
ms.author: juliako
ms.openlocfilehash: 5807d38e22d8cecf40b5ad4262f9e4662b77ec4c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92779132"
---
# <a name="manage-azure-cdn-caching-policy-in-azure-media-services"></a>Azure CDN cache beleid in Azure Media Services beheren
Azure Media Services biedt op HTTP gebaseerde adaptieve streaming en progressief downloaden. Streaming op basis van HTTP is zeer schaalbaar en biedt voor delen van caching in proxy-en CDN-lagen, en caching aan client zijde. Streaming-eind punten bieden algemene streaming-mogelijkheden en ook configuratie voor HTTP-cache headers. Met streaming-eind punten wordt HTTP-cache-Control ingesteld: Max-Age en Expires-headers. U kunt meer informatie krijgen over HTTP-cache headers van [w3.org](https://www.w3.org/Protocols/rfc2616/rfc2616-sec13.html).

## <a name="default-caching-headers"></a>Standaard headers in cache geheugen
Streaming-eind punten passen standaard drie dagen cache headers toe voor streaminggegevens op aanvraag (werkelijke media fragmenten/segmenten) en manifest (afspeel lijst). Bij het gebruik van live streamen worden in streaming-eind punten drie dagen cache headers voor gegevens (werkelijke media fragmenten/segmenten) en 2 seconden cache header voor manifest-aanvragen (afspeel lijst) toegepast. Als Live-programma wordt ingeschakeld op aanvraag (Live-Archief), zijn er kopteksten van de on-demand streaming-cache van toepassing.

## <a name="azure-cdn-integration"></a>Integratie van Azure CDN
Azure Media Services biedt [geïntegreerde CDN](https://azure.microsoft.com/updates/azure-media-services-now-fully-integrated-with-azure-cdn/) voor streaming-eind punten. Cache-control-headers zijn op dezelfde manier van toepassing als streaming-eind punten voor streaming-eind punten die zijn ingeschakeld voor CDN. Azure CDN gebruikt streaming-eindpunt geconfigureerde cache waarden om de levens duur van de intern in de cache geplaatste objecten te definiëren en deze waarde ook gebruiken om de kopteksten van de leverings cache in te stellen. Wanneer u CDN-streaming-eind punten gebruikt, wordt het niet aanbevolen om kleine cache waarden in te stellen. Als u kleine waarden instelt, nemen de prestaties af en vermindert u het voor deel van CDN. Het is niet toegestaan cache headers in te stellen die kleiner zijn dan 600 seconden voor streaming-eind punten die geschikt zijn voor CDN.

> [!IMPORTANT]
>Azure Media Services heeft volledige integratie met Azure CDN. Met één klik kunt u alle beschik bare Azure CDN providers integreren in uw streaming-eind punt, met inbegrip van standaard-en Premium-producten. Zie deze [aankondiging](https://azure.microsoft.com/blog/standardstreamingendpoint/)voor meer informatie.
> 
> Gegevens kosten van streaming-eind punt naar CDN worden alleen uitgeschakeld als het CDN is ingeschakeld voor streaming-eindpunt-Api's of met behulp van de sectie streaming-eind punt van Azure Portal. Hand matige integratie of het rechtstreeks maken van een CDN-eind punt met behulp van CDN-Api's of de portal sectie worden de gegevens kosten niet uitgeschakeld.

## <a name="configuring-cache-headers-with-azure-media-services"></a>Cache headers met Azure Media Services configureren
U kunt Azure Portal-of Azure Media Services-Api's gebruiken voor het configureren van cache header waarden.

1. Zie de sectie [streaming-eind punten beheren](../media-services/previous/media-services-portal-manage-streaming-endpoints.md) in het streaming-eind punt configureren om cache headers te configureren met behulp van Azure Portal.
2. Azure Media Services REST API, [StreamingEndpoint](/rest/api/media/operations/streamingendpoint#StreamingEndpointCacheControl).
3. Azure Media Services .NET-SDK, [StreamingEndpointCacheControl eigenschappen](/dotnet/api/microsoft.windowsazure.mediaservices.client.streamingendpointcachecontrol).

## <a name="cache-configuration-precedence-order"></a>Volg orde van prioriteit van cache configuratie
1. Standaard waarde voor onderdrukking van Azure Media Services geconfigureerde cache waarde.
2. Als er geen hand matige configuratie is, zijn de standaard waarden van toepassing.
3. Standaard zijn 2 seconden cache headers van toepassing op het live streaming-manifest (afspeel lijst), ongeacht de Azure-media-of Azure Storage configuratie en het overschrijven van deze waarde is niet beschikbaar.