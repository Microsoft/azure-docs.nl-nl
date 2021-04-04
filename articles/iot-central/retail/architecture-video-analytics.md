---
title: Azure IoT Central video Analytics-object en bewegings detectie | Microsoft Docs
description: Meer informatie over het bouwen van een IoT Central-toepassing met behulp van de toepassings sjabloon video Analytics-object-en bewegings detectie in IoT Central. Deze sjabloon maakt gebruik van live video analyses en verbonden camera's.
author: KishorIoT
ms.author: nandab
ms.date: 07/27/2020
ms.topic: conceptual
ms.service: iot-central
ms.subservice: iot-central-retail
services: iot-central
ms.openlocfilehash: 48808f762536390287bae40e8af3849da20b81c2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91874285"
---
# <a name="video-analytics---object-and-motion-detection-application-architecture"></a>Video analyse-toepassings architectuur voor object-en bewegings detectie

Met de sjabloon **video Analytics-toepassing voor detectie van objecten en bewegingen** kunt u IOT-oplossingen bouwen, zoals live video analyse mogelijkheden.

:::image type="content" source="media/architecture-video-analytics/architecture.png" alt-text="Diagram van het overzicht van video Analytics-objecten en Motion-detectie onderdelen.":::

De belangrijkste onderdelen van de video analytics-oplossing zijn:

## <a name="live-video-analytics-lva"></a>Live video Analytics (LVA)

LVA biedt een platform waarmee u intelligente video toepassingen kunt bouwen die de rand en de Cloud omvatten. Met het platform kunt u intelligente video toepassingen bouwen die de rand en de Cloud omvatten. Het platform biedt de mogelijkheid om live video vast te leggen, op te nemen en te analyseren. Bovendien kunnen de resultaten, zoals video en/of videoanalyses, worden gepubliceerd naar Azure-services. De Azure-services kunnen in de cloud of aan de rand worden uitgevoerd. Het platform kan worden gebruikt om IoT-oplossingen te verbeteren met videoanalyse.

Zie [Live video Analytics](https://github.com/Azure/live-video-analytics) op github voor meer informatie.

## <a name="iot-edge-lva-gateway-module"></a>LVA-gateway module IoT Edge

In de module IoT Edge LVA-gateway worden camera's als nieuwe apparaten geïnstantieerd en rechtstreeks verbonden met IoT Central met behulp van de client-SDK voor IoT-apparaten.

Bij deze referentie-implementatie maken apparaten verbinding met de oplossing met behulp van symmetrische sleutels van de rand. Zie [Get connected to Azure IOT Central](../core/concepts-get-connected.md) voor meer informatie over de connectiviteit van apparaten

## <a name="media-graph"></a>Mediagrafiek

Met media Graph kunt u bepalen waar de media moeten worden vastgelegd, hoe deze moeten worden verwerkt en waar de resultaten moeten worden geleverd. U configureert media Graph door onderdelen of knoop punten op de gewenste manier te verbinden. Zie [Media Graph](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph) op github voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

De voorgestelde volgende stap is om te leren hoe u [een IOT Central-toepassing kunt implementeren met behulp van de toepassing video Analytics-object-en bewegings detectie](tutorial-video-analytics-deploy.md).
