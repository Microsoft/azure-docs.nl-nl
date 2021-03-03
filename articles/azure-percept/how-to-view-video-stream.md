---
title: De RTSP-video stroom van Azure percept DK weer geven
description: Meer informatie over het weer geven van de RTSP-video stroom van het Vision-SoM van Azure percept DK
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/12/2021
ms.custom: template-how-to
ms.openlocfilehash: 20fb8495e17d4294351a50c3bc97436de9f03450
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101662364"
---
# <a name="view-your-azure-percept-dks-rtsp-video-stream"></a>De RTSP-video stroom van Azure percept DK weer geven

Volg deze hand leiding voor het weer geven van de RTSP-video stroom van het Vision-SoM van Azure percept DK in azure percept Studio. Het is niet meer mogelijk om te dezichpen van Vision AI-modellen die op uw apparaat zijn geïmplementeerd, zodat de webstream kan worden weer gegeven.

## <a name="prerequisites"></a>Vereisten

- Azure percept DK (Devkit)
- [Azure-abonnement](https://azure.microsoft.com/free/)
- [Setup van Azure PERCEPT DK](./quickstart-percept-dk-set-up.md): u hebt uw Devkit met een Wi-Fi-netwerk verbonden, een IOT hub gemaakt en uw Devkit verbonden met de IOT hub

## <a name="view-the-rtsp-video-stream"></a>De RTSP-video stroom weer geven

1. Schakel uw Devkit uit.

1. Ga naar [Azure percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819).

1. Klik aan de linkerkant van de pagina overzicht op **apparaten**.

    :::image type="content" source="./media/how-to-view-video-stream/overview-devices-inline.png" alt-text="Overzichts scherm van Azure percept Studio." lightbox="./media/how-to-view-video-stream/overview-devices.png":::

1. Selecteer uw Devkit in de lijst.

    :::image type="content" source="./media/how-to-view-video-stream/select-device.png" alt-text="Overzichts scherm van Azure percept Studio.":::

1. Klik op **de stream van uw apparaat weer geven**.

    :::image type="content" source="./media/how-to-view-video-stream/view-device-stream.png" alt-text="Overzichts scherm van Azure percept Studio.":::

    Hiermee opent u een afzonderlijk tabblad met de Live webstream van het visioning-SoM van uw Azure percept DK.

    :::image type="content" source="./media/how-to-view-video-stream/webstream.png" alt-text="Overzichts scherm van Azure percept Studio.":::

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het weer geven van uw [Azure PERCEPT DK-telemetrie](./how-to-view-telemetry.md).