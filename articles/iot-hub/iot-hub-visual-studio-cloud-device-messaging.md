---
title: VS Cloud Explorer gebruiken voor het beheren van berichten van Azure IoT Hub-apparaten
description: Meer informatie over het gebruik van Cloud Explorer voor Visual Studio voor het bewaken van apparaten in Cloud berichten en het verzenden van Cloud naar apparaat-berichten in azure IoT Hub.
author: shizn
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: xshi
ms.openlocfilehash: c56bb7030b2ebc12e3afc24e2d8cb29ce2dda0bf
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "74079479"
---
# <a name="use-cloud-explorer-for-visual-studio-to-send-and-receive-messages-between-your-device-and-iot-hub"></a>Gebruik Cloud Explorer voor Visual Studio om berichten tussen uw apparaat en IoT Hub te verzenden en te ontvangen.

![End-to-end-diagram](./media/iot-hub-visual-studio-cloud-device-messaging/e-to-e-diagram.png)

[Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS) is een handige Visual Studio-uitbrei ding waarmee u uw Azure-resources kunt weer geven, de eigenschappen van hun eigen en belang rijke ontwikkel acties vanuit Visual Studio. In dit artikel wordt uitgelegd hoe u met Cloud Explorer berichten tussen uw apparaat en uw hub kunt verzenden en ontvangen.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="what-you-learn"></a>Wat u leert

In dit artikel leert u hoe u Cloud Explorer voor Visual Studio kunt gebruiken om apparaat-naar-Cloud-berichten te bewaken en Cloud-naar-apparaat-berichten te verzenden. Apparaat-naar-Cloud-berichten kunnen sensor gegevens zijn die door het apparaat worden verzameld en vervolgens naar uw IoT Hub worden verzonden. Cloud-naar-apparaat-berichten kunnen opdrachten zijn die uw IoT Hub naar uw apparaat verzendt. Knip bijvoorbeeld een LED die is verbonden met uw apparaat.

## <a name="what-you-do"></a>Wat u doet

In dit artikel voert u de volgende taken uit:

- Gebruik Cloud Explorer voor Visual Studio om apparaat-naar-Cloud-berichten te bewaken.

- Gebruik Cloud Explorer voor Visual Studio om Cloud-naar-apparaat-berichten te verzenden.

## <a name="what-you-need"></a>Wat u nodig hebt

De volgende vereisten zijn nodig:

- Een actief Azure-abonnement.

- Een Azure-IoT Hub onder uw abonnement.

- Micro soft Visual Studio 2017 Update 9 of hoger. In dit artikel wordt gebruikgemaakt van [Visual Studio 2019](https://www.visualstudio.com/vs/).

- Het onderdeel Cloud Explorer van Visual Studio Installer, dat standaard is geselecteerd met de Azure-workload.

## <a name="update-cloud-explorer-to-latest-version"></a>Cloud Explorer bijwerken naar de nieuwste versie

Het onderdeel Cloud Explorer van Visual Studio Installer voor Visual Studio 2017 biedt alleen ondersteuning voor het bewaken van apparaat-naar-Cloud-en Cloud-naar-apparaat-berichten. Als u Visual Studio 2017 wilt gebruiken, downloadt en installeert u de nieuwste [Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS).

## <a name="sign-in-to-access-your-hub"></a>Meld u aan om toegang te krijgen tot uw hub

Voer de volgende stappen uit om toegang te krijgen tot uw hub:

1. Selecteer in Visual Studio   >  **Cloud Explorer** weer geven om Cloud Explorer te openen.

1. Selecteer het pictogram account beheer om uw abonnementen weer te geven.

    ![Pictogram account beheer](media/iot-hub-visual-studio-cloud-device-messaging/account-management-icon.png)

1. Als u bent aangemeld bij Azure, worden uw accounts weer gegeven. Als u zich voor de eerste keer wilt aanmelden bij Azure, kiest u **een account toevoegen**.

1. Selecteer de Azure-abonnementen die u wilt gebruiken en klik **op Toep assen**.

1. Breid uw abonnement uit en vouw **IOT-hubs** uit.  Onder elke hub kunt u uw apparaten weer geven voor die hub.

    ![Apparaten lijst](media/iot-hub-visual-studio-cloud-device-messaging/hub-device-list.png)

## <a name="monitor-device-to-cloud-messages"></a>Apparaat-naar-Cloud-berichten bewaken

Ga als volgt te werk om berichten te bewaken die worden verzonden vanaf uw apparaat naar uw IoT Hub:

1. Klik met de rechter muisknop op uw IoT Hub of apparaat en selecteer **D2C-bericht bewaking starten**.

    ![Het D2C-bericht van de bewaking starten](media/iot-hub-visual-studio-cloud-device-messaging/start-monitoring-d2c-message-vs2019.png)

1. De bewaakte berichten worden weer gegeven onder **uitvoer**.

    ![Resultaten van D2C-bericht bewaken](media/iot-hub-visual-studio-cloud-device-messaging/monitor-d2c-message-result-vs2019.png)

1. Als u de bewaking wilt stoppen, klikt u met de rechter muisknop op een IoT Hub of apparaat en selecteert u **bewaking D2C-bericht stoppen**.

## <a name="send-cloud-to-device-messages"></a>Cloud-naar-apparaat-berichten verzenden

Voer de volgende stappen uit om een bericht te verzenden van uw IoT Hub naar uw apparaat:

1. Klik met de rechter muisknop op het apparaat en selecteer **C2D-bericht verzenden**.

1. Voer het bericht in het invoervak in.

    ![C2D-bericht verzenden](media/iot-hub-visual-studio-cloud-device-messaging/send-c2d-message-test.png)

    Resultaten worden weer gegeven onder **uitvoer**.

    ![Resultaat van C2D-bericht verzenden](media/iot-hub-visual-studio-cloud-device-messaging/send-c2d-message-result-vs2019.png)

## <a name="next-steps"></a>Volgende stappen

U hebt geleerd hoe u apparaat-naar-Cloud-berichten kunt bewaken en Cloud-naar-apparaat-berichten kunt verzenden tussen uw IoT-apparaat en Azure IoT Hub.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]