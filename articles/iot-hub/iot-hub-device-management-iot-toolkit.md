---
title: Azure IoT-Apparaatbeheer met Azure IoT-Hulpprogram Ma's voor VSCode
description: Gebruik de Azure IoT-Hulpprogram Ma's voor Visual Studio code voor Azure IoT Hub Apparaatbeheer, met de directe methoden en de gewenste opties voor eigenschappen beheer.
author: formulahendry
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/04/2019
ms.author: junhan
ms.openlocfilehash: d85e0e967dd802a77ccbc11b884d7a9f2891524d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "81688103"
---
# <a name="use-azure-iot-tools-for-visual-studio-code-for-azure-iot-hub-device-management"></a>Gebruik Azure IoT-Hulpprogram Ma's voor Visual Studio code voor Azure IoT Hub Apparaatbeheer

![End-to-end-diagram](media/iot-hub-get-started-e2e-diagram/2.png)

[Azure IOT-Hulpprogram ma's](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) is een handige Visual Studio code-extensie die het IOT hub beheer en IOT-toepassings ontwikkeling eenvoudiger maakt. Het wordt geleverd met beheer opties die u kunt gebruiken om verschillende taken uit te voeren.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

| Beheer optie          | Taak                    |
|----------------------------|--------------------------------|
| Directe methoden             | Zorg ervoor dat een apparaat functioneert, zoals het starten of stoppen van het verzenden van berichten of het opnieuw opstarten van het apparaat.                                        |
| Dubbele apparaten lezen           | De gerapporteerde status van een apparaat ophalen. Zo rapporteert het apparaat dat de LED nu knippert.                                    |
| Dubbele update van apparaat         | Plaats een apparaat in bepaalde staten, zoals het instellen van een LED op groen of het instellen van het verzend interval van de telemetrie naar 30 minuten.         |
| Cloud-naar-apparaat-berichten   | Meldingen verzenden naar een apparaat. Bijvoorbeeld: ' het is nu zeer waarschijnlijk de regen. Vergeet niet om een paraplu te brengen. "              |

Zie voor meer gedetailleerde informatie over de verschillen en richt lijnen over het gebruik van deze opties [apparaat-naar-Cloud communicatie richtlijnen](iot-hub-devguide-d2c-guidance.md) en [Cloud-naar-apparaat-communicatie richtlijnen](iot-hub-devguide-c2d-guidance.md).

Apparaatdubbels zijn JSON-documenten waarin statusinformatie van een apparaat (metagegevens, configuraties en voorwaarden) zijn opgeslagen. IoT Hub persistent voor elk apparaat dat verbinding maakt met het apparaat. Zie [aan de slag met apparaat apparaatdubbels](iot-hub-node-node-twin-getstarted.md)voor meer informatie over apparaatdubbels.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="what-you-learn"></a>Wat u leert

U leert hoe u Azure IoT-Hulpprogram Ma's voor Visual Studio code gebruikt met verschillende beheer opties op uw ontwikkel computer.

## <a name="what-you-do"></a>Wat u doet

Voer Azure IoT-Hulpprogram Ma's voor Visual Studio code uit met verschillende beheer opties.

## <a name="what-you-need"></a>Wat u nodig hebt

* Een actief Azure-abonnement.
* Een Azure IoT hub onder uw abonnement.
* [Visual Studio Code](https://code.visualstudio.com/)
* [Azure IOT-Hulpprogram ma's voor VS code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) of kopieer deze URL en plak deze in een browser venster: `vscode:extension/vsciot-vscode.azure-iot-tools` .

## <a name="sign-in-to-access-your-iot-hub"></a>Meld u aan voor toegang tot uw IoT-hub

1. Vouw in de **Verkenner** -weer gave van VS code het gedeelte **Azure IOT Hub-apparaten** uit in de linkerbenedenhoek.

2. Klik op **IOT hub selecteren** in het context menu.

3. Er wordt een pop-upvenster weer gegeven in de rechter benedenhoek om u voor de eerste keer aan te melden bij Azure.

4. Nadat u zich hebt aangemeld, wordt de lijst met Azure-abonnementen weer gegeven en selecteert u vervolgens Azure-abonnement en IoT Hub.

5. De lijst met apparaten wordt weer gegeven in een paar seconden op het tabblad **apparaten van Azure IOT hub** .

   > [!Note]
   > U kunt de installatie ook voltooien door **IoT Hub-verbindingsreeks instellen** te kiezen. Voer de **iothubowner** -beleids Connection String in voor de IOT-hub waarmee uw IOT-apparaat verbinding maakt in het pop-upvenster.

## <a name="direct-methods"></a>Directe methoden

1. Klik met de rechter muisknop op het apparaat en selecteer **rechtstreekse methode aanroepen**. 

2. Voer de methode naam en payload in het invoervak in.

3. De resultaten worden weer gegeven in de weer gave **uitvoer** van  >  **Azure IOT hub** .

## <a name="read-device-twin"></a>Dubbele apparaten lezen

1. Klik met de rechter muisknop op het apparaat en selecteer **apparaat twee bewerken**. 

2. Een **azure-iot-device-twin.jsin** het bestand wordt geopend met de inhoud van het apparaat dubbele.

## <a name="update-device-twin"></a>Dubbele update van apparaat

1. Maak enkele bewerkingen van **Tags** of **Eigenschappen. gewenst** veld.

2. Klik met de rechter muisknop op het bestand **azure-iot-device-twin.js** .

3. Selecteer **apparaat dubbele bijwerken** om het apparaat te updaten.

## <a name="send-cloud-to-device-messages"></a>Cloud-naar-apparaat-berichten verzenden

Voer de volgende stappen uit om een bericht van uw IoT-hub naar uw apparaat te verzenden:
 
1. Klik met de rechter muisknop op het apparaat en selecteer **C2D-bericht naar apparaat verzenden**. 

2. Voer het bericht in het invoervak in.

3. De resultaten worden weer gegeven in de weer gave **uitvoer** van  >  **Azure IOT hub** .

## <a name="next-steps"></a>Volgende stappen

U hebt geleerd hoe u de Azure IoT tools-extensie voor Visual Studio code gebruikt met verschillende beheer opties.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
