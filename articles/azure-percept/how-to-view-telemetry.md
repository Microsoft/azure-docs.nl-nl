---
title: De telemetrie van het model van Azure percept DK weer geven
description: Meer informatie over het weer geven van het Vision model van uw Azure percept DK-telemetrie in azure IoT Explorer
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/17/2021
ms.custom: template-how-to
ms.openlocfilehash: b03cd8bc71f87e3ce7984a55d330bc116614f928
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101662491"
---
# <a name="view-your-azure-percept-dks-model-inference-telemetry"></a>De telemetrie van het model van Azure percept DK weer geven

Volg deze hand leiding om het Vision model van uw Azure percept DK-telemetrie in [Azure IOT Explorer](https://github.com/Azure/azure-iot-explorer/releases)te bekijken.

## <a name="prerequisites"></a>Vereisten

- Azure percept DK (Devkit)
- [Azure-abonnement](https://azure.microsoft.com/free/)
- [Setup van Azure PERCEPT DK](./quickstart-percept-dk-set-up.md): u hebt uw Devkit met een Wi-Fi-netwerk verbonden, een IOT hub gemaakt en uw Devkit verbonden met de IOT hub
- [Het Vision AI-model is geïmplementeerd in uw Azure percept DK](./how-to-deploy-model.md)

## <a name="view-telemetry"></a>Telemetrie bekijken

1. Schakel uw Devkit uit.

1. Down load en Installeer [Azure IOT Explorer](https://github.com/Azure/azure-iot-explorer/releases). Als u een Windows-gebruiker bent, selecteert u het. msi-bestand.

    :::image type="content" source="./media/how-to-view-telemetry/azure-iot-explorer-download.png" alt-text="Download scherm voor Azure IoT Explorer.":::

1. Verbind uw IoT Hub met Azure IoT Explorer:

    1. Ga naar de [Azure Portal](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_Azure_Iothub=aduprod&microsoft_azure_marketplace_ItemHideKey=Microsoft_Azure_ADUHidden#home).

    1. Selecteer **Alle resources**.

        :::image type="content" source="./media/how-to-view-telemetry/azure-portal.png" alt-text="Start pagina Azure Portal.":::

    1. Selecteer de IoT Hub waarmee uw Azure percept DK is verbonden.

        :::image type="content" source="./media/how-to-view-telemetry/iot-hub.png" alt-text="IoT Hub lijst in Azure Portal.":::

    1. Selecteer aan de linkerkant van de pagina IoT Hub **gedeelde toegangs beleid**.

        :::image type="content" source="./media/how-to-view-telemetry/shared-access-policies.png" alt-text="IoT Hub pagina met beleid voor gedeelde toegang.":::

    1. Klik op **iothubowner**.

        :::image type="content" source="./media/how-to-view-telemetry/iothubowner.png" alt-text="Scherm voor gedeeld toegangs beleid met iothubowner gemarkeerd.":::

    1. Klik op het pictogram voor de blauwe kopie naast **verbindings reeks: primaire sleutel**.

        :::image type="content" source="./media/how-to-view-telemetry/connection-string.png" alt-text="het venster iothubowner met connection string knop kopiëren gemarkeerd.":::

    1. Open Azure IoT Explorer en klik op **+ verbinding toevoegen**.

    1. Plak de connection string in het vak **verbindings reeks** in het venster **Connection String toevoegen** en klik op **Opslaan**.

        :::image type="content" source="./media/how-to-view-telemetry/add-connection-string.png" alt-text="Azure IOT Explorer-venster met vak voor het plakken van connection string naar.":::

    1. Verwijs het zicht op een object voor het dezicht van het model.

    1. Selecteer **telemetrie**.

    1. Klik op **starten** om telemetriegegevens van het apparaat weer te geven.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het weer geven van uw [Azure PERCEPT DK-video stroom](how-to-view-video-stream.md).