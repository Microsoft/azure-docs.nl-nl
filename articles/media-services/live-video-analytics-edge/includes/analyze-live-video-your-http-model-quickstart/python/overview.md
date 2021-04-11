---
ms.openlocfilehash: 8124f31213de1bbb794968c616e6efb940ac36b2
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105582389"
---
> [!div class="mx-imgBorder"]
> :::image type="content" source="../../../media/quickstarts/overview-qs5.svg" alt-text="Signaalflow":::

Dit diagram laat zien hoe de signalen in deze quickstart stromen. Een [Edge-module](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) simuleert een IP-camera die als host fungeert voor een RTSP-server (Real-Time Streaming Protocol). Een [RTSP-bron](../../../media-graph-concept.md#rtsp-source)-knooppunt haalt de videofeed van deze server en verstuurt videoframes naar het [HTTP-extensieprocessor](../../../media-graph-concept.md#http-extension-processor)-knooppunt. 

Het knooppunt HTTP-extensie speelt de rol van een proxy. Het knooppunt samplet de binnenkomende videoframes die u hebt ingesteld met het veld `samplingOptions`, en converteert de videoframes bovendien naar het gespecificeerde afbeeldingstype. Vervolgens wordt de afbeelding via REST doorgestuurd naar een andere Edge-module die een AI-model achter een HTTP-eindpunt uitvoert. In dit voorbeeld wordt die Edge-module gebouwd met behulp van het [YOLOv3](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/yolov3-onnx)-model, dat vele soorten objecten kan detecteren. Het HTTP-extensieprocessor-knooppunt verzamelt de detectieresultaten en publiceert de gebeurtenissen naar het [IoT Hub Sink](../../../media-graph-concept.md#iot-hub-message-sink)-knooppunt. Het knooppunt verzendt die gebeurtenissen vervolgens naar [IoT Edge-hub](../../../../../iot-fundamentals/iot-glossary.md#iot-edge-hub).

In deze quickstart gaat u het volgende doen:

1. De mediagrafiek maken en implementeren.
1. De resultaten interpreteren.
1. Resources opschonen.