---
title: VS Cloud Explorer gebruiken voor het beheren van Azure IoT Hub van apparaten
description: Meer informatie over het gebruik van Cloud Explorer voor Visual Studio het bewaken van apparaat-naar-cloud-berichten en het verzenden van cloud-naar-apparaatberichten in Azure IoT Hub.
author: shizn
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: xshi
ms.openlocfilehash: 8461a77d06a63c2ac319323a91b5577ca4dce1cf
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567027"
---
# <a name="use-cloud-explorer-for-visual-studio-to-send-and-receive-messages-between-your-device-and-iot-hub"></a>Cloud Explorer gebruiken om Visual Studio berichten te verzenden en te ontvangen tussen uw apparaat en IoT Hub

![End-to-end-diagram](./media/iot-hub-visual-studio-cloud-device-messaging/e-to-e-diagram.png)

In dit artikel leert u hoe u Cloud Explorer voor Visual Studio kunt gebruiken om apparaat-naar-cloud-berichten te bewaken en cloud-naar-apparaat-berichten te verzenden. Apparaat-naar-cloud-berichten kunnen sensorgegevens zijn die uw apparaat verzamelt en vervolgens naar uw IoT Hub. Cloud-naar-apparaat-berichten kunnen opdrachten zijn die uw IoT Hub naar uw apparaat verzendt. U kunt bijvoorbeeld een LED laten knipperen die is verbonden met uw apparaat.

[Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS) is een handige Visual Studio-extensie waarmee u uw Azure-resources kunt weergeven, hun eigenschappen kunt inspecteren en belangrijke acties voor ontwikkelaars kunt uitvoeren vanuit Visual Studio. Dit artikel is gericht op het gebruik van Cloud Explorer om berichten te verzenden en te ontvangen tussen uw apparaat en uw hub.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="prerequisites"></a>Vereisten

- Een actief Azure-abonnement.

- Een Azure IoT Hub onder uw abonnement.

- Microsoft Visual Studio 2017 Update 9 of hoger. In dit artikel wordt [Visual Studio 2019 gebruikt.](https://www.visualstudio.com/vs/)

- Het onderdeel Cloud Explorer van Visual Studio Installer, dat standaard is geselecteerd met Azure Workload.

## <a name="update-cloud-explorer-to-latest-version"></a>Cloud Explorer bijwerken naar de nieuwste versie

Het onderdeel Cloud Explorer van Visual Studio Installer voor Visual Studio 2017 ondersteunt alleen het bewaken van apparaat-naar-cloud- en cloud-naar-apparaat-berichten. Als u Visual Studio 2017 wilt gebruiken, downloadt en installeert u de meest recente [Cloud Explorer.](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS)

## <a name="sign-in-to-access-your-hub"></a>Aanmelden voor toegang tot uw hub

Volg deze stappen om toegang te krijgen tot uw hub:

1. Selecteer Visual Studio Cloud Explorer **weergeven**  >  **om** Cloud Explorer te openen.

1. Selecteer het pictogram Accountbeheer om uw abonnementen weer te geven.

    ![Pictogram Accountbeheer](media/iot-hub-visual-studio-cloud-device-messaging/account-management-icon.png)

1. Als u bent aangemeld bij Azure, worden uw accounts weergegeven. Als u zich voor het eerst wilt aanmelden bij Azure, kiest **u Een account toevoegen.**

1. Selecteer de Azure-abonnementen die u wilt gebruiken en kies **Toepassen.**

1. Vouw uw abonnement uit en vouw **vervolgens IoT Hubs uit.**  Onder elke hub ziet u uw apparaten voor die hub.

    ![Apparatenlijst](media/iot-hub-visual-studio-cloud-device-messaging/hub-device-list.png)

## <a name="monitor-device-to-cloud-messages"></a>Apparaat-naar-cloud-berichten bewaken

Volg deze stappen om berichten te bewaken die vanaf uw apparaat naar IoT Hub apparaat worden verzonden:

1. Klik met de rechtermuisknop op IoT Hub apparaat en **selecteer Controle D2C-bericht starten.**

    ![Controle D2C-bericht starten](media/iot-hub-visual-studio-cloud-device-messaging/start-monitoring-d2c-message-vs2019.png)

1. De bewaakte berichten worden weergegeven onder **Uitvoer**.

    ![Resultaat van controle van D2C-bericht](media/iot-hub-visual-studio-cloud-device-messaging/monitor-d2c-message-result-vs2019.png)

1. Als u de bewaking wilt stoppen, klikt u met de rechtermuisknop op IoT Hub apparaat en selecteert **u Controle D2C-bericht stoppen.**

## <a name="send-cloud-to-device-messages"></a>Cloud-naar-apparaat-berichten verzenden

Volg deze stappen om een bericht IoT Hub uw apparaat te verzenden:

1. Klik met de rechtermuisknop op uw apparaat en **selecteer C2D-bericht verzenden.**

1. Voer het bericht in het invoervak in.

    ![C2D-bericht verzenden](media/iot-hub-visual-studio-cloud-device-messaging/send-c2d-message-test.png)

    Resultaten worden weergegeven onder **Uitvoer**.

    ![Resultaat van C2D-bericht verzenden](media/iot-hub-visual-studio-cloud-device-messaging/send-c2d-message-result-vs2019.png)

## <a name="next-steps"></a>Volgende stappen

U hebt geleerd hoe u apparaat-naar-cloud-berichten bewaakt en cloud-naar-apparaat-berichten verzendt tussen uw IoT-apparaat en Azure IoT Hub.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]