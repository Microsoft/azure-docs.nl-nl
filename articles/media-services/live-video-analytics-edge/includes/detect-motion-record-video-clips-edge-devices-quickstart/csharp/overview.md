---
ms.openlocfilehash: 9c1b521a0f10da77295fd2457793566d787cb2cd
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89570163"
---
> [!div class="mx-imgBorder"]
> :::image type="content" source="../../../media/quickstarts/overview-qs4.svg" alt-text="Signaalflow":::

Het voorgaande diagram laat zien hoe de signalen in deze quickstart stromen. [Een Edge-module](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) simuleert een IP-camera die als host fungeert voor een RTSP-server (Real-Time Streaming Protocol). Een [RTSP-bron](../../../media-graph-concept.md#rtsp-source)knooppunt haalt de video-feed van deze server, en verstuurt videoframes naar het knooppunt van de [bewegingsdetectieprocessor](../../../media-graph-concept.md#motion-detection-processor). De RTSP-bron verzendt diezelfde videoframes naar een [signaalpoortprocessor](../../../media-graph-concept.md#signal-gate-processor)knoop punt, dat gesloten blijft totdat het wordt geactiveerd door een gebeurtenis.

Wanneer de bewegingsdetectieprocessor beweging detecteert in de video, verzendt deze een gebeurtenis naar het signaalpoortprocessorknooppunt waardoor dit wordt geactiveerd. De poort wordt geopend gedurende de geconfigureerde tijd, waarin videoframes worden verzonden naar het [bestandssink](../../../media-graph-concept.md#file-sink)knooppunt. Dit sinkknooppunt registreert de video als een MP4-bestand in het lokale bestandssysteem van uw edge-apparaat. Het bestand wordt opgeslagen op de geconfigureerde locatie.

In deze quickstart gaat u het volgende doen:

1. De mediagrafiek maken en implementeren.
1. De resultaten interpreteren.
1. Resources opschonen.
