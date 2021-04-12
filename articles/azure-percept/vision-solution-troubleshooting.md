---
title: Problemen oplossen met Azure percept Vision-en Vision-modules
description: Tips voor het oplossen van problemen met een aantal van de meest voorkomende problemen in de Vision AI-prototypen
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 03/29/2021
ms.custom: template-how-to
ms.openlocfilehash: 78de5ef0ef77a181d4a2da91e4b468db1b47f208
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106074689"
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

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-device-modules-inline.png" alt-text="IoT Edge pagina voor het geselecteerde apparaat waarin de inhoud van het tabblad modules wordt weer gegeven." lightbox= "./media/vision-solution-troubleshooting/vision-device-modules.png":::

## <a name="delete-a-device"></a>Een apparaat verwijderen

1. Ga naar de [Azure Portal](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_Azure_Iothub=aduprod&microsoft_azure_marketplace_ItemHideKey=Microsoft_Azure_ADUHidden#home).

1. Klik op het pictogram van de **IOT hub** .

1. Selecteer de IoT Hub waarmee het doel apparaat is verbonden.

1. Selecteer **IOT Edge** en schakel het selectie vakje in naast uw doel apparaat-id. Klik op het prullenbak pictogram om uw apparaat te verwijderen.

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-delete-device.png" alt-text="Pictogram verwijderen is gemarkeerd in IoT Edge Home page.":::

## <a name="eye-module-troubleshooting-tips"></a>Tips voor probleem oplossing voor ogen module

### <a name="check-the-runtime-status-of-azureeyemodule"></a>De runtime status van azureeyemodule controleren

Als er een probleem is met **WebStreamModule**, moet u ervoor zorgen dat de **azureeyemodule**, die het visuele model dezicht afhandelt, wordt uitgevoerd. Als u de runtime status wilt controleren, gaat u naar de [Azure Portal](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_Azure_Iothub=aduprod&microsoft_azure_marketplace_ItemHideKey=Microsoft_Azure_ADUHidden#home) en navigeert u naar **alle resources**  ->  **\<your IoT hub>**  ->  **IOT Edge**  ->  **\<your device ID>** . Klik op het tabblad **modules** om de runtime status van alle geïnstalleerde modules weer te geven.

:::image type="content" source="./media/vision-solution-troubleshooting/over-the-air-iot-edge-device-page-inline.png" alt-text="Scherm runtime status van de apparaat-module." lightbox= "./media/vision-solution-troubleshooting/over-the-air-iot-edge-device-page.png":::

Als de runtime status van **azureeyemodule** niet wordt weer gegeven als **actief**, klikt u op **set modules**  ->  **azureeyemodule**. Stel op de pagina **module-instellingen** de **gewenste status** in op **actief** en klik op **bijwerken**.

 :::image type="content" source="./media/vision-solution-troubleshooting/firmware-desired-status-stopped.png" alt-text="Configuratie scherm van module-instelling.":::

### <a name="update-telemetryintervalneuralnetworkms"></a>TelemetryIntervalNeuralNetworkMs bijwerken

Als u de volgende fout voor aantal beperkingen ondervindt, moet de TelemetryIntervalNeuralNetworkMs-waarde in de dubbele instellingen van de azureeyemodule-module worden bijgewerkt.

|Foutbericht|
|------|
|Het totale aantal berichten in IotHub ' xxxxxxxxx ' heeft het toegewezen quotum overschreden. Maximum aantal toegestane berichten: ' 8000 ', huidig aantal berichten: ' xxxx '. Verzend-en ontvangst bewerkingen worden tot de volgende UTC-datum geblokkeerd voor deze hub. Overweeg de eenheden voor deze hub te verhogen om het quotum te verhogen.|

TelemetryIntervalNeuralNetworkMs bepaalt hoe vaak berichten worden verzonden (in milliseconden) van het Neural-netwerk. Azure-abonnementen hebben een beperkt aantal berichten per dag, afhankelijk van de laag van uw abonnement. Als u weet dat u bent vergrendeld omdat u te veel berichten hebt verzonden, verhoogt u dit naar een hoger nummer. 12000 (dat wil zeggen elke 12 seconden) geeft u een goed afgeronde, verwerkte 7200 berichten per dag, die onder de limiet van 8000 berichten voor het gratis abonnement vallen.

Voer de volgende stappen uit om uw TelemetryIntervalNeuralNetworkMs-waarde bij te werken:

1. Meld u aan bij de [Azure Portal](https://ms.portal.azure.com/?feature.canmodifystamps=true&Microsoft_Azure_Iothub=aduprod#home) en open **alle resources**.

1. Klik op de pagina **alle resources** op de naam van de IOT hub die tijdens de installatie-ervaring aan uw Devkit is ingericht.

1. Klik aan de linkerkant van de pagina IoT Hub op **IOT Edge** onder **automatische Apparaatbeheer**. Zoek op de pagina apparaten IoT Edge de apparaat-ID van uw Devkit. Klik op de apparaat-ID van uw Devkit om de pagina met het IoT Edge apparaat te openen.

1. Selecteer **azureeyemodule** op het tabblad **modules** .

1. Open **module identiteit** op de pagina azureeyemodule.

    :::image type="content" source="./media/vision-solution-troubleshooting/module-page-inline.png" alt-text="Scherm afbeelding van module pagina." lightbox= "./media/vision-solution-troubleshooting/module-page.png":::

1. Schuif omlaag naar **Eigenschappen**. Houd er rekening mee dat de eigenschappen ' running ' en ' logging ' op dit moment niet actief zijn.

    :::image type="content" source="./media/vision-solution-troubleshooting/module-identity-twin-inline.png" alt-text="Scherm afbeelding van dubbele eigenschappen van module." lightbox= "./media/vision-solution-troubleshooting/module-identity-twin.png":::

1. Werk de waarde **TelemetryIntervalNeuralNetworkMs** naar wens bij en klik op het pictogram **Opslaan** .

## <a name="view-device-rtsp-video-stream"></a>RTSP-video stroom van apparaat weer geven

Bekijk de RTSP-video stroom van uw apparaat in [Azure percept Studio](./how-to-view-video-stream.md) of [VLC Media Player](https://www.videolan.org/vlc/index.html).

Als u de RTSP-stroom in VLC Media Player wilt openen, gaat u naar **Media**  ->  **Open Network Stream**  ->  **RTSP://[IP-adres van apparaat]/result**.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de [algemene hand leiding voor probleem oplossing](./troubleshoot-dev-kit.md) voor meer informatie over het oplossen van problemen met Azure percept DK.