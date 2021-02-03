---
title: Livevideo analyseren met uw eigen HTTP-model - Azure
description: In deze snelstart gebruikt u computer vision om de live videofeed van een (gesimuleerde) IP-camera te analyseren door uw eigen HTTP-model te gebruiken.
ms.topic: quickstart
ms.date: 04/27/2020
zone_pivot_groups: ams-lva-edge-programming-languages
ms.openlocfilehash: d3ba937abcc7bbfd9bb2afe7b15aec28ebb57446
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/03/2021
ms.locfileid: "99508483"
---
# <a name="quickstart-analyze-live-video-by-using-your-own-http-model"></a>Quickstart: Livevideo analyseren met uw eigen HTTP-model

In deze quickstart leert u hoe u Live Video Analytics in IoT Edge kunt gebruiken om de live-videofeed van een (gesimuleerde) IP-camera te analyseren. U ziet hoe u een computer vision-model kunt toepassen om objecten te detecteren. Een subset van de frames in de livevideofeed wordt verzonden naar een deductieservice. De resultaten worden verzonden naar IoT Edge-hub. 

::: zone pivot="programming-language-csharp"
[!INCLUDE [header](includes/analyze-live-video-your-http-model-quickstart/csharp/header.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [header](includes/analyze-live-video-your-http-model-quickstart/python/header.md)]
::: zone-end

## <a name="prerequisites"></a>Vereisten

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/analyze-live-video-your-http-model-quickstart/csharp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prerequisites](includes/analyze-live-video-your-http-model-quickstart/python/prerequisites.md)]
::: zone-end

## <a name="review-the-sample-video"></a>De voorbeeldvideo bekijken

::: zone pivot="programming-language-csharp"
[!INCLUDE [review-sample-video](includes/analyze-live-video-your-http-model-quickstart/csharp/review-sample-video.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [review-sample-video](includes/analyze-live-video-your-http-model-quickstart/python/review-sample-video.md)]
::: zone-end

## <a name="overview"></a>Overzicht

::: zone pivot="programming-language-csharp"
[!INCLUDE [overview](includes/analyze-live-video-your-http-model-quickstart/csharp/overview.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [overview](includes/analyze-live-video-your-http-model-quickstart/python/overview.md)]
::: zone-end

## <a name="create-and-deploy-the-media-graph"></a>De mediagrafiek maken en implementeren

::: zone pivot="programming-language-csharp"
[!INCLUDE [create-deploy-media-graph](includes/analyze-live-video-your-http-model-quickstart/csharp/create-deploy-media-graph.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [create-deploy-media-graph](includes/analyze-live-video-your-http-model-quickstart/python/create-deploy-media-graph.md)]
::: zone-end

## <a name="interpret-results"></a>Resultaten interpreteren

::: zone pivot="programming-language-csharp"
[!INCLUDE [interpret-results](includes/analyze-live-video-your-http-model-quickstart/csharp/interpret-results.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [interpret-results](includes/analyze-live-video-your-http-model-quickstart/python/interpret-results.md)]
::: zone-end

## <a name="clean-up-resources"></a>Resources opschonen

Als u andere quickstarts wilt proberen, moet u de resources die u hebt gemaakt, bewaren. Anders gaat u in de Azure-portal naar de resourcegroepen, selecteert u de resourcegroep waar u deze quickstart hebt uitgevoerd en verwijdert u vervolgens alle resources.

## <a name="next-steps"></a>Volgende stappen

* Probeer een [beveiligde versie van het YoloV3-model](https://github.com/Azure/live-video-analytics/blob/master/utilities/video-analysis/tls-yolov3-onnx/readme.md) en implementeer deze naar het IoT-edge-apparaat. 

Bekijk extra uitdagingen voor gevorderde gebruikers:

* Gebruik een [IP-camera](https://en.wikipedia.org/wiki/IP_camera) met ondersteuning voor RTSP in plaats van de RTSP-simulator. U kunt zoeken naar IP-camera's die RTSP ondersteunen op de pagina met [ONVIF-compatibele](https://www.onvif.org/conformant-products/) producten. Zoek naar apparaten die voldoen aan de profielen G, S of T.
* Gebruik een AMD64-of x64-Linux-apparaat (in plaats van een Azure Linux-VM). Dit apparaat moet zich in hetzelfde netwerk als de IP-camera bevinden. Volg de instructies in [Azure IoT Edge-runtime installeren op Linux](../../iot-edge/how-to-install-iot-edge.md). Registreer vervolgens het apparaat met IoT Hub door de instructies in [Uw eerste IoT Edge-module implementeren op een virtueel Linux-apparaat](../../iot-edge/quickstart-linux.md) te volgen.