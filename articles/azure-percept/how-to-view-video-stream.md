---
title: De RTSP-video stroom van Azure percept DK weer geven
description: Meer informatie over het weer geven van de RTSP-video stroom vanuit Azure percept DK
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/12/2021
ms.custom: template-how-to
ms.openlocfilehash: 5f77e99dc5c34867fef2b0ac47c709824fa4477d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102096000"
---
# <a name="view-your-azure-percept-dks-rtsp-video-stream"></a>De RTSP-video stroom van Azure percept DK weer geven

Volg deze hand leiding om de RTSP-video stroom te bekijken vanuit Azure percept DK in azure percept Studio. Het is niet meer mogelijk om te voor komen dat op uw apparaat gedistribueerde AI-modellen worden weer gegeven in de webstream.

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

    :::image type="content" source="./media/how-to-view-video-stream/select-device.png" alt-text="Scherm opname van beschik bare apparaten in azure percept Studio.":::

1. Klik op **de stream van uw apparaat weer geven**.

    :::image type="content" source="./media/how-to-view-video-stream/view-device-stream.png" alt-text="Scherm afbeelding van de pagina apparaat met de acties beschik bare Vision-project.":::

    Hiermee opent u een afzonderlijk tabblad met de Live webstream van uw Azure percept DK.

    :::image type="content" source="./media/how-to-view-video-stream/webstream.png" alt-text="Scherm afbeelding van de webstream van het apparaat.":::

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het weer geven van uw [Azure PERCEPT DK-telemetrie](./how-to-view-telemetry.md).