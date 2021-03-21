---
title: Problemen oplossen met Azure percept Vision-en Vision-modules
description: Tips voor het oplossen van problemen met een aantal van de meest voorkomende problemen in de Vision AI-prototypen
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/18/2021
ms.custom: template-how-to
ms.openlocfilehash: e7351079e7325aa7750dc0d10f0923bc847ccc3c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "101662603"
---
# <a name="vision-solution-troubleshooting"></a>Problemen met Vision-oplossingen oplossen

Raadpleeg de volgende richt lijnen voor meer informatie over het oplossen van oplossingen zonder code Vision in azure percept Studio.

## <a name="delete-a-vision-project"></a>Een Vision-project verwijderen

1. Ga naar https://www.customvision.ai/projects.

1. Beweeg de muis aanwijzer over het project dat u wilt verwijderen en klik op het prullenbak pictogram om het project te verwijderen.

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-delete-project.png" alt-text="De pagina projecten in Custom Vision met het pictogram verwijderen gemarkeerd.":::

## <a name="check-which-modules-are-on-a-device"></a>Controleren welke modules zich op een apparaat bevinden

1. Ga naar de [Azure Portal](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_Azure_Iothub=aduprod&microsoft_azure_marketplace_ItemHideKey=Microsoft_Azure_ADUHidden#home).

1. Klik op het pictogram van de **IOT hub** .

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-iot-hub-2-inline.png" alt-text="Azure Portal start pagina met het pictogram IOT hub is gemarkeerd." lightbox= "./media/vision-solution-troubleshooting/vision-iot-hub-2.png":::

1. Selecteer de IoT Hub waarmee het doel apparaat is verbonden.

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-iot-hub.png" alt-text="Lijst met IoT-hubs.":::

1. Selecteer **IOT Edge** en klik op uw apparaat op het tabblad **apparaat-id** .

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-iot-edge.png" alt-text="Start pagina IoT Edge.":::

1. Uw apparaat modules worden vermeld op het tabblad **modules** .

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-device-modules-inline.png" alt-text="IOT Edge-pagina voor het geselecteerde apparaat waarin de inhoud van het tabblad modules wordt weer gegeven." lightbox= "./media/vision-solution-troubleshooting/vision-device-modules.png":::

## <a name="delete-a-device"></a>Een apparaat verwijderen

1. Ga naar de [Azure Portal](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_Azure_Iothub=aduprod&microsoft_azure_marketplace_ItemHideKey=Microsoft_Azure_ADUHidden#home).

1. Klik op het pictogram van de **IOT hub** .

1. Selecteer de IoT Hub waarmee het doel apparaat is verbonden.

1. Selecteer **IOT Edge** en schakel het selectie vakje in naast uw doel apparaat-id. Klik op het prullenbak pictogram om uw apparaat te verwijderen.

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-delete-device.png" alt-text="Het pictogram verwijderen is gemarkeerd in de start pagina van IOT Edge.":::

## <a name="eye-module-troubleshooting-tips"></a>Tips voor probleem oplossing voor ogen module

Als er een probleem is met **WebStreamModule**, moet u ervoor zorgen dat **azureeyemodule**, die het visuele model dezicht, wordt uitgevoerd. Als u de runtime status wilt controleren, gaat u naar de [Azure Portal](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_Azure_Iothub=aduprod&microsoft_azure_marketplace_ItemHideKey=Microsoft_Azure_ADUHidden#home) en navigeert u naar **alle resources**  ->  **\<your IoT hub>**  ->  **IOT Edge**  ->  **\<your device ID>** . Klik op het tabblad **modules** om de runtime status van alle geïnstalleerde modules weer te geven.

:::image type="content" source="./media/vision-solution-troubleshooting/over-the-air-iot-edge-device-page-inline.png" alt-text="Scherm runtime status van de apparaat-module." lightbox= "./media/vision-solution-troubleshooting/over-the-air-iot-edge-device-page.png":::

Als de runtime status van **azureeyemodule** niet wordt weer gegeven als **actief**, klikt u op **set modules**  ->  **azureeyemodule**. Stel op de pagina **module-instellingen** de **gewenste status** in op **actief** en klik op **bijwerken**.

 :::image type="content" source="./media/vision-solution-troubleshooting/firmware-desired-status-stopped.png" alt-text="Configuratie scherm van module-instelling.":::

## <a name="view-device-rtsp-video-stream"></a>RTSP-video stroom van apparaat weer geven

Bekijk de RTSP-video stroom van uw apparaat in [Azure percept Studio](./how-to-view-video-stream.md) of [VLC Media Player](https://www.videolan.org/vlc/index.html).

Als u de RTSP-stroom in VLC Media Player wilt openen, gaat u naar **Media**  ->  **Open Network Stream**  ->  **RTSP://[IP-adres van apparaat]/result**.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de [algemene hand leiding voor probleem oplossing](./troubleshoot-dev-kit.md) voor meer informatie over het oplossen van problemen met Azure percept DK.