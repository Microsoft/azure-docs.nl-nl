---
title: Een Vision AI-model implementeren in uw Azure percept DK
description: Meer informatie over het implementeren van een Vision AI-model naar uw Azure percept DK vanuit Azure percept Studio
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/12/2021
ms.custom: template-how-to
ms.openlocfilehash: 6e1ed39edfd3c395fbc3e4d26a4aa358d48a1d5b
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101662421"
---
# <a name="deploy-a-vision-ai-model-to-your-azure-percept-dk"></a>Een Vision AI-model implementeren in uw Azure percept DK

Volg deze hand leiding voor het implementeren van een Vision AI-model naar uw Azure percept DK vanuit Azure percept Studio.

## <a name="prerequisites"></a>Vereisten

- Azure percept DK (Devkit)
- [Azure-abonnement](https://azure.microsoft.com/free/)
- [Setup van Azure PERCEPT DK](./quickstart-percept-dk-set-up.md): u hebt uw Devkit met een Wi-Fi-netwerk verbonden, een IOT hub gemaakt en uw Devkit verbonden met de IOT hub

## <a name="model-deployment"></a>Modelimplementatie

1. Schakel uw Devkit uit.

1. Ga naar [Azure percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819).

1. Klik aan de linkerkant van de pagina overzicht op **apparaten**.

    :::image type="content" source="./media/how-to-deploy-model/overview-devices-inline.png" alt-text="Overzichts scherm van Azure percept Studio." lightbox="./media/how-to-deploy-model/overview-devices.png":::

1. Selecteer uw Devkit in de lijst.

    :::image type="content" source="./media/how-to-deploy-model/select-device.png" alt-text="Lijst met percept-apparaten.":::

1. Klik op de volgende pagina op **een voorbeeld model implementeren** als u een van de vooraf getrainde voor beelden van Vision-modellen wilt implementeren. Als u een bestaande [aangepaste oplossing voor No-code Vision](./tutorial-nocode-vision.md)wilt implementeren, klikt u op **een Custom Vision project implementeren**.

    :::image type="content" source="./media/how-to-deploy-model/deploy-model.png" alt-text="Lijst met percept-apparaten.":::

1. Als u hebt gekozen voor het implementeren van een no-code Vision-oplossing, selecteert u uw project en uw voorkeurs model herhaling en klikt u op **implementeren**.

1. Als u hebt gekozen voor het implementeren van een voorbeeld model, selecteert u het model en klikt u op **implementeren op apparaat**.

    :::image type="content" source="./media/how-to-deploy-model/select-sample-model.png" alt-text="Lijst met percept-apparaten.":::

1. Wanneer de implementatie van uw model is geslaagd, wordt in de rechter bovenhoek van het scherm een status bericht weer gegeven. Als u wilt dat uw model niet meer in actie wordt weer gegeven, klikt u op de koppeling **stroom weer geven** in het status bericht om de RTSP-video stroom van het zicht bare SoM van uw Devkit weer te geven.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het weer geven van uw [Azure PERCEPT DK-telemetrie](how-to-view-telemetry.md).