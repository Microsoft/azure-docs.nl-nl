---
title: Beheer van Azure IoT-apparaten met Visual Studio Cloud Explorer
description: Gebruik de Cloud Explorer voor Visual Studio voor Azure IoT Hub Apparaatbeheer, met de juiste methoden en de gewenste beheer opties voor eigenschappen van de twee.
author: shizn
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: xshi
ms.openlocfilehash: 6fe5a45dda6632c56b3c6714827950e25e7d26af
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "73953186"
---
# <a name="use-cloud-explorer-for-visual-studio-for-azure-iot-hub-device-management"></a>Cloud Explorer gebruiken voor Visual Studio voor Azure IoT Hub Apparaatbeheer

![End-to-end-diagram](media/iot-hub-device-management-visual-studio/iot-e2e-simple.png)

[Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS) is een handige Visual Studio-uitbrei ding waarmee u uw Azure-resources kunt weer geven, de eigenschappen van hun eigen en belang rijke ontwikkel acties vanuit Visual Studio. Het wordt geleverd met beheer opties die u kunt gebruiken om verschillende taken uit te voeren.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

| Beheer optie          | Taak                    |
|----------------------------|--------------------------------|
| Directe methoden             | Zorg ervoor dat een apparaat functioneert, zoals het starten of stoppen van het verzenden van berichten of het opnieuw opstarten van het apparaat.                                        |
| Dubbele apparaten lezen           | De gerapporteerde status van een apparaat ophalen. Zo rapporteert het apparaat dat de LED nu knippert.                                    |
| Dubbele update van apparaat         | Plaats een apparaat in bepaalde staten, zoals het instellen van een LED op groen of het instellen van het verzend interval van de telemetrie naar 30 minuten.         |
| Cloud-naar-apparaat-berichten   | Meldingen verzenden naar een apparaat. Bijvoorbeeld: ' het is nu zeer waarschijnlijk de regen. Vergeet niet om een paraplu te brengen. "              |

Zie voor meer gedetailleerde informatie over de verschillen en richt lijnen over het gebruik van deze opties [apparaat-naar-Cloud communicatie richtlijnen](iot-hub-devguide-d2c-guidance.md) en [Cloud-naar-apparaat-communicatie richtlijnen](iot-hub-devguide-c2d-guidance.md).

Apparaatdubbels zijn JSON-documenten waarin statusinformatie van een apparaat zijn opgeslagen, zoals metagegevens, configuraties en voorwaarden. IoT Hub persistent voor elk apparaat dat verbinding maakt met het apparaat. Zie [aan de slag met apparaat apparaatdubbels](iot-hub-node-node-twin-getstarted.md)voor meer informatie over apparaatdubbels.

## <a name="what-you-learn"></a>Wat u leert

In dit artikel leert u hoe u Cloud Explorer voor Visual Studio kunt gebruiken met verschillende beheer opties op uw ontwikkel computer.

## <a name="what-you-do"></a>Wat u doet

In dit artikel voert u Cloud Explorer voor Visual Studio uit met verschillende beheer opties.

## <a name="what-you-need"></a>Wat u nodig hebt

De volgende vereisten zijn nodig:

- Een actief Azure-abonnement.

- Een Azure-IoT Hub onder uw abonnement.

- Micro soft Visual Studio 2017 Update 9 of hoger. In dit artikel wordt gebruikgemaakt van [Visual studio 2017 of Visual studio 2019](https://www.visualstudio.com/vs/).

- Onderdeel van de Cloud Explorer uit Visual Studio Installer, dat standaard is geselecteerd met de Azure-workload.

## <a name="update-cloud-explorer-to-latest-version"></a>Cloud Explorer bijwerken naar de nieuwste versie

Het onderdeel Cloud Explorer van Visual Studio Installer voor Visual Studio 2017 biedt alleen ondersteuning voor het bewaken van apparaat-naar-Cloud-en Cloud-naar-apparaat-berichten. Als u Visual Studio 2017 wilt gebruiken, downloadt en installeert u de nieuwste [Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS).

## <a name="sign-in-to-access-your-hub"></a>Meld u aan om toegang te krijgen tot uw hub

1. Selecteer in Visual Studio   >  **Cloud Explorer** weer geven om Cloud Explorer te openen.

1. Selecteer het pictogram account beheer om uw abonnementen weer te geven.

    ![Pictogram account beheer](media/iot-hub-visual-studio-cloud-device-messaging/account-management-icon.png)

1. Als u bent aangemeld bij Azure, worden uw accounts weer gegeven. Als u zich voor de eerste keer wilt aanmelden bij Azure, kiest u **een account toevoegen**.

1. Selecteer de Azure-abonnementen die u wilt gebruiken en klik **op Toep assen**.

1. Breid uw abonnement uit en vouw **IOT-hubs** uit.  Onder elke hub kunt u uw apparaten weer geven voor die hub. Klik met de rechter muisknop op een apparaat voor toegang tot de beheer opties.

    ![Beheer opties](media/iot-hub-device-management-visual-studio/management-options-vs2019.png)

## <a name="direct-methods"></a>Directe methoden

Als u directe methoden wilt gebruiken, voert u de volgende stappen uit:

1. Klik met de rechter muisknop op het apparaat en selecteer **directe methode van apparaat aanroepen**.

1. Voer de methode naam en payload in bij **directe methode Invoke** en selecteer vervolgens **OK**.

    Resultaten worden weer gegeven in de **uitvoer**.

## <a name="update-device-twin"></a>Dubbele update van apparaat

Voer de volgende stappen uit om een apparaat dubbele te bewerken:

1. Klik met de rechter muisknop op het apparaat en selecteer **apparaat twee bewerken**.

   Een **azure-iot-device-twin.jsin** het bestand wordt geopend met de inhoud van het apparaat dubbele.

1. Maak enkele bewerkingen van **Tags** of **Eigenschappen. gewenste** velden voor de **azure-iot-device-twin.jsin** het bestand.

1. Druk op **CTRL + S** om het dubbele apparaat bij te werken.

   Resultaten worden weer gegeven in de **uitvoer**.

## <a name="send-cloud-to-device-messages"></a>Cloud-naar-apparaat-berichten verzenden

Voer de volgende stappen uit om een bericht te verzenden van uw IoT Hub naar uw apparaat:

1. Klik met de rechter muisknop op het apparaat en selecteer **C2D-bericht verzenden**.

1. Voer het bericht in **C2D-bericht verzenden** in en selecteer **OK**.

   Resultaten worden weer gegeven in de **uitvoer**.

## <a name="next-steps"></a>Volgende stappen

U hebt geleerd hoe u Cloud Explorer voor Visual Studio gebruikt met verschillende beheer opties.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
