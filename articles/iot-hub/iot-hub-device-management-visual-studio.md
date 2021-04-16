---
title: Azure IoT-apparaatbeheer Visual Studio Cloud Explorer
description: Gebruik cloudverkenner voor Visual Studio voor Azure IoT Hub-apparaatbeheer, met de directe methoden en de gewenste beheeropties voor eigenschappen van de tweeling.
author: shizn
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: xshi
ms.openlocfilehash: 96f93325e0f17daaaf2bad91123fea81531ca152
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107566959"
---
# <a name="use-cloud-explorer-for-visual-studio-for-azure-iot-hub-device-management"></a>Cloud Explorer gebruiken voor Visual Studio voor Azure IoT Hub-apparaatbeheer

![End-to-end-diagram](media/iot-hub-device-management-visual-studio/iot-e2e-simple.png)

In dit artikel leert u hoe u Cloud Explorer kunt gebruiken voor Visual Studio verschillende beheeropties op uw ontwikkelcomputer. [Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS) is een handige Visual Studio waarmee u uw Azure-resources kunt weergeven, hun eigenschappen kunt inspecteren en belangrijke acties voor ontwikkelaars kunt uitvoeren vanuit Visual Studio. Het wordt geleverd met beheeropties die u kunt gebruiken om verschillende taken uit te voeren.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

| Beheeroptie          | Taak                    |
|----------------------------|--------------------------------|
| Directe methoden             | Laat een apparaat fungeren als het starten of stoppen van het verzenden van berichten of het opnieuw opstarten van het apparaat.                                        |
| Apparaattweeling lezen           | Haal de gerapporteerde status van een apparaat op. Het apparaat meldt bijvoorbeeld dat de LED nu knippert.                                    |
| Apparaattweeling bijwerken         | Zet een apparaat in bepaalde staat, zoals het instellen van een LED op groen of het instellen van het telemetrie-verzendinterval op 30 minuten.         |
| Cloud-naar-apparaat-berichten   | Meldingen verzenden naar een apparaat. Bijvoorbeeld: "Het is zeer waarschijnlijk dat het vandaag gaat regenen. Vergeet niet om een umbrella mee te brengen.              |

Zie Richtlijnen voor [apparaat-naar-cloudcommunicatie](iot-hub-devguide-d2c-guidance.md) en Richtlijnen voor communicatie van cloud naar apparaat voor gedetailleerde uitleg over de verschillen en richtlijnen [voor het gebruik van deze opties.](iot-hub-devguide-c2d-guidance.md)

Apparaatdubbels zijn JSON-documenten waarin statusinformatie van een apparaat zijn opgeslagen, zoals metagegevens, configuraties en voorwaarden. IoT Hub houdt een apparaat dubbel aan voor elk apparaat dat er verbinding mee maakt. Zie Aan de slag met apparaattweeling voor meer [informatie over apparaattweelingen.](iot-hub-node-node-twin-getstarted.md)

## <a name="prerequisites"></a>Vereisten

- Een actief Azure-abonnement.

- Een Azure IoT Hub onder uw abonnement.

- Microsoft Visual Studio 2017 Update 9 of hoger. In dit artikel [wordt Visual Studio 2017 of Visual Studio 2019 gebruikt.](https://www.visualstudio.com/vs/)

- Cloud Explorer-onderdeel van Visual Studio Installer, dat standaard is geselecteerd met Azure Workload.

## <a name="update-cloud-explorer-to-latest-version"></a>Cloud Explorer bijwerken naar de nieuwste versie

Het onderdeel Cloud Explorer van Visual Studio Installer voor Visual Studio 2017 ondersteunt alleen het bewaken van apparaat-naar-cloud- en cloud-naar-apparaat-berichten. Als u Visual Studio 2017 wilt gebruiken, downloadt en installeert u de meest recente [Cloud Explorer.](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS)

## <a name="sign-in-to-access-your-hub"></a>Aanmelden voor toegang tot uw hub

1. Selecteer Visual Studio Cloud Explorer **weergeven**  >  **om** Cloud Explorer te openen.

1. Selecteer het pictogram Accountbeheer om uw abonnementen weer te geven.

    ![Pictogram Accountbeheer](media/iot-hub-visual-studio-cloud-device-messaging/account-management-icon.png)

1. Als u bent aangemeld bij Azure, worden uw accounts weergegeven. Als u zich voor het eerst wilt aanmelden bij Azure, kiest **u Een account toevoegen.**

1. Selecteer de Azure-abonnementen die u wilt gebruiken en kies **Toepassen.**

1. Vouw uw abonnement uit en vouw **vervolgens IoT Hubs uit.**  Onder elke hub ziet u uw apparaten voor die hub. Klik met de rechtermuisknop op één apparaat om toegang te krijgen tot de beheeropties.

    ![Beheeropties](media/iot-hub-device-management-visual-studio/management-options-vs2019.png)

## <a name="direct-methods"></a>Directe methoden

Ga als volgt te werk om directe methoden te gebruiken:

1. Klik met de rechtermuisknop op uw apparaat en selecteer **Directe methode voor apparaat aanroepen.**

1. Voer de naam van de methode en de nettolading in **Directe methode aanroepen** in en selecteer **vervolgens OK.**

    Resultaten worden weergegeven in **Uitvoer**.

## <a name="update-device-twin"></a>Apparaattweeling bijwerken

Ga als volgt te werk om een apparaat dubbel te bewerken:

1. Klik met de rechtermuisknop op uw apparaat en **selecteer Apparaat dubbel bewerken.**

   Er **azure-iot-device-twin.jseen bestand** geopend met de inhoud van de apparaat dubbel.

1. Bewerk tags of **properties.desired-velden** in deazure-iot-device-twin.js **in het** bestand. 

1. Druk **op Ctrl+S om** de apparaattabel bij te werken.

   Resultaten worden weergegeven in **Uitvoer**.

## <a name="send-cloud-to-device-messages"></a>Cloud-naar-apparaat-berichten verzenden

Volg deze stappen om een bericht IoT Hub uw apparaat te verzenden:

1. Klik met de rechtermuisknop op uw apparaat en **selecteer C2D-bericht verzenden.**

1. Voer het bericht in **C2D-bericht verzenden in** en selecteer **OK.**

   Resultaten worden weergegeven in **Uitvoer**.

## <a name="next-steps"></a>Volgende stappen

U hebt geleerd hoe u Cloud Explorer gebruikt voor Visual Studio verschillende beheeropties.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
