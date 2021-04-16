---
title: Informatie Azure IoT Tools VSCode gebruiken voor het manageren van IT Hub-berichten
description: Informatie over het gebruik van Azure IoT Tools voor Visual Studio Code voor het bewaken van apparaat-naar-cloud-berichten en het verzenden van cloud-naar-apparaatberichten in Azure IoT Hub.
author: formulahendry
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 01/18/2019
ms.author: junhan
ms.custom:
- 'Role: Cloud Development'
ms.openlocfilehash: 1c4a840233e576c528e9c58d57eca0b3d524bf4d
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107566925"
---
# <a name="use-azure-iot-tools-for-visual-studio-code-to-send-and-receive-messages-between-your-device-and-iot-hub"></a>Gebruik Azure IoT Tools voor Visual Studio Code om berichten te verzenden en te ontvangen tussen uw apparaat en IoT Hub

![End-to-end-diagram](./media/iot-hub-vscode-iot-toolkit-cloud-device-messaging/e-to-e-diagram.png)

In dit artikel leert u hoe u Azure IoT Tools voor Visual Studio Code kunt gebruiken om apparaat-naar-cloud-berichten te bewaken en cloud-naar-apparaat-berichten te verzenden. Apparaat-naar-cloud-berichten kunnen sensorgegevens zijn die door uw apparaat worden verzameld en vervolgens naar uw IoT-hub worden verzonden. Cloud-naar-apparaat-berichten kunnen opdrachten zijn die uw IoT-hub naar uw apparaat verzendt om een LED te laten knipperen die is verbonden met uw apparaat.

[Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) is een handige Visual Studio Code-extensie die het IoT Hub en het ontwikkelen van IoT-toepassingen eenvoudiger maakt. Dit artikel is gericht op het gebruik Azure IoT Tools voor Visual Studio Code voor het verzenden en ontvangen van berichten tussen uw apparaat en uw IoT-hub.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="prerequisites"></a>Vereisten

* Een actief Azure-abonnement.

* Een Azure IoT-hub onder uw abonnement.

* [Visual Studio Code](https://code.visualstudio.com/)

* [Azure IoT Tools voor VS Code of](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) kopieer en plak deze URL in een browservenster: `vscode:extension/vsciot-vscode.azure-iot-tools`

## <a name="sign-in-to-access-your-iot-hub"></a>Aanmelden voor toegang tot uw IoT-hub

1. Vouw **in de** verkennerweergave van VS Code Azure IoT Hub sectie **Apparaten** in de linkeronderhoek uit.

2. Klik **op IoT Hub** selecteren in het contextmenu.

3. Er wordt een pop-uppop-up in de rechteronderhoek weer geven, waarin u zich voor de eerste keer kunt aanmelden bij Azure.

4. Nadat u zich hebt aanmelden, wordt de lijst met Azure-abonnementen weergegeven. Selecteer vervolgens Azure-abonnement en IoT Hub.

5. De lijst met apparaten wordt over **een paar seconden weergegeven Azure IoT Hub tabblad** Apparaten.

   > [!Note]
   > U kunt de installatie ook voltooien door **IoT Hub-verbindingsreeks instellen** te kiezen. Voer in het pop-upvenster het beleid **iothubowner** connection string voor de IoT-hub waar uw IoT-apparaat verbinding mee maakt.

## <a name="monitor-device-to-cloud-messages"></a>Apparaat-naar-cloud-berichten bewaken

Volg deze stappen om berichten te bewaken die vanaf uw apparaat naar uw IoT-hub worden verzonden:

1. Klik met de rechtermuisknop op uw apparaat en **selecteer Bewaking van ingebouwd gebeurtenis-eindpunt starten.**

2. De bewaakte berichten worden weergegeven in **Azure IoT Hub**  >   uitvoerweergave.

3. Als u de bewaking wilt stoppen, klikt u met de rechtermuisknop op **de weergave UITVOER** en selecteert u Bewaking van ingebouwd **gebeurtenis-eindpunt stoppen.**

## <a name="send-cloud-to-device-messages"></a>Cloud-naar-apparaat-berichten verzenden

Volg deze stappen om een bericht van uw IoT-hub naar uw apparaat te verzenden:

1. Klik met de rechtermuisknop op uw apparaat en **selecteer C2D-bericht verzenden naar apparaat.**

2. Voer het bericht in het invoervak in.

3. Resultaten worden weergegeven in **Azure IoT Hub**  >   uitvoerweergave.

## <a name="next-steps"></a>Volgende stappen

U hebt geleerd hoe u apparaat-naar-cloud-berichten bewaakt en cloud-naar-apparaat-berichten verzendt tussen uw IoT-apparaat en Azure IoT Hub.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]