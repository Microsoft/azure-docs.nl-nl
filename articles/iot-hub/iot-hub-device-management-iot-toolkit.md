---
title: Azure IoT-apparaatbeheer met Azure IoT Tools voor VSCode
description: Gebruik de Azure IoT Tools voor Visual Studio Code voor Azure IoT Hub-apparaatbeheer, met de directe methoden en de beheeropties voor de gewenste eigenschappen van de tweeling.
author: formulahendry
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/04/2019
ms.author: junhan
ms.openlocfilehash: b48def283ea27fdd0eaa3230a2eb9a8327461ff1
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567010"
---
# <a name="use-azure-iot-tools-for-visual-studio-code-for-azure-iot-hub-device-management"></a>Gebruik Azure IoT Tools voor Visual Studio Code voor Azure IoT Hub-apparaatbeheer

![End-to-end-diagram](media/iot-hub-get-started-e2e-diagram/2.png)

In dit artikel leert u hoe u Azure IoT Tools voor Visual Studio Code gebruikt met verschillende beheeropties op uw ontwikkelmachine. [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) is een handige Visual Studio Code-extensie die het IoT Hub en het ontwikkelen van IoT-toepassingen eenvoudiger maakt. Het wordt geleverd met beheeropties die u kunt gebruiken om verschillende taken uit te voeren.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

| Beheeroptie          | Taak                    |
|----------------------------|--------------------------------|
| Directe methoden             | Laat een apparaat fungeren als het starten of stoppen van het verzenden van berichten of het opnieuw opstarten van het apparaat.                                        |
| Apparaattweeling lezen           | Haal de gerapporteerde status van een apparaat op. Het apparaat meldt bijvoorbeeld dat de LED nu knippert.                                    |
| Apparaattweeling bijwerken         | Zet een apparaat in bepaalde staat, zoals het instellen van een LED op groen of het instellen van het telemetrie-verzendinterval op 30 minuten.         |
| Cloud-naar-apparaat-berichten   | Meldingen verzenden naar een apparaat. Bijvoorbeeld: "Het is zeer waarschijnlijk dat het vandaag gaat regenen. Vergeet niet om een umbrella mee te brengen.              |

Zie Richtlijnen voor [apparaat-naar-cloudcommunicatie](iot-hub-devguide-d2c-guidance.md) en Richtlijnen voor communicatie van cloud naar apparaat voor gedetailleerde uitleg over de verschillen en richtlijnen [voor het gebruik van deze opties.](iot-hub-devguide-c2d-guidance.md)

Apparaatdubbels zijn JSON-documenten waarin statusinformatie van een apparaat (metagegevens, configuraties en voorwaarden) zijn opgeslagen. IoT Hub houdt een apparaat dubbel aan voor elk apparaat dat er verbinding mee maakt. Zie Aan de slag met apparaattweeling voor meer informatie [over apparaattweeling.](iot-hub-node-node-twin-getstarted.md)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Vereisten

* Een actief Azure-abonnement.
* Een Azure IoT-hub onder uw abonnement.
* [Visual Studio Code](https://code.visualstudio.com/)
* [Azure IoT Tools voor VS Code of](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) kopieer deze URL en plak deze in een browservenster: `vscode:extension/vsciot-vscode.azure-iot-tools` .

## <a name="sign-in-to-access-your-iot-hub"></a>Aanmelden voor toegang tot uw IoT-hub

1. Vouw **in** de verkennerweergave van VS Code **Azure IoT Hub sectie Apparaten** in de linkeronderhoek uit.

2. Klik **op IoT Hub** selecteren in het contextmenu.

3. Er wordt een pop-up in de rechteronderhoek weer geven, waarin u zich voor de eerste keer kunt aanmelden bij Azure.

4. Nadat u zich hebt aanmelden, wordt de lijst met Azure-abonnementen weergegeven. Selecteer vervolgens Azure-abonnement en IoT Hub.

5. De lijst met apparaten wordt over **een paar seconden Azure IoT Hub het tabblad** Apparaten weergegeven.

   > [!Note]
   > U kunt de installatie ook voltooien door **IoT Hub-verbindingsreeks instellen** te kiezen. Voer in het pop-upvenster de connection string **iothubowner-beleid** in voor de IoT-hub waar uw IoT-apparaat verbinding mee maakt.

## <a name="direct-methods"></a>Directe methoden

1. Klik met de rechtermuisknop op uw apparaat en selecteer **Directe methode aanroepen.** 

2. Voer de naam van de methode en de nettolading in het invoervak in.

3. Resultaten worden weergegeven in **Azure IoT Hub**  >   uitvoerweergave.

## <a name="read-device-twin"></a>Apparaattweeling lezen

1. Klik met de rechtermuisknop op uw apparaat en **selecteer Apparaat dubbel bewerken.** 

2. Een **azure-iot-device-twin.jsbestand** wordt geopend met de inhoud van de apparaat dubbel.

## <a name="update-device-twin"></a>Apparaattweeling bijwerken

1. Maak enkele wijzigingen in het **veld tags** of **properties.desired.**

2. Klik met de rechtermuisknop op **azure-iot-device-twin.jsbestand.**

3. Selecteer **Apparaatt dubbel bijwerken om** de apparaattwee bij te werken.

## <a name="send-cloud-to-device-messages"></a>Cloud-naar-apparaat-berichten verzenden

Volg deze stappen om een bericht van uw IoT-hub naar uw apparaat te verzenden:
 
1. Klik met de rechtermuisknop op uw apparaat en **selecteer C2D-bericht verzenden naar apparaat.** 

2. Voer het bericht in het invoervak in.

3. Resultaten worden weergegeven in **Azure IoT Hub**  >   uitvoerweergave.

## <a name="next-steps"></a>Volgende stappen

U hebt geleerd hoe u de extensie Azure IoT Tools code Visual Studio met verschillende beheeropties.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
