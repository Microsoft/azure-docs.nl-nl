---
title: 'Snelstartgids: onboarding voor IoT naar een oplossing op basis van een agent'
description: In deze Quick Start leert u hoe u de Defender voor IoT-beveiligings service in uw Azure-IoT Hub kunt voorbereiden en inschakelen.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: shhazam-ms
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/20/2021
ms.author: shhazam
ms.openlocfilehash: d30a03aa7b7715a8792e7b70a0571270c6ad7b37
ms.sourcegitcommit: f6193c2c6ce3b4db379c3f474fdbb40c6585553b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/08/2021
ms.locfileid: "102449676"
---
# <a name="quickstart-onboard-defender-for-iot-to-an-agent-based-solution"></a>Snelstartgids: onboarding voor IoT naar een oplossing op basis van een agent

In dit artikel wordt uitgelegd hoe u de Defender voor IoT-service op uw bestaande IoT Hub inschakelt. Als u momenteel niet over een Azure IoT-hub beschikt, raadpleegt u [Een Azure IoT-hub maken met Azure Portal](../iot-hub/iot-hub-create-through-portal.md) om aan de slag te gaan.

U kunt uw IoT-beveiliging beheren via de IoT Hub in Defender voor IoT. Met de beheer Portal in de IoT Hub kunt u het volgende doen: 

- IoT Hub beveiliging beheren.

- Basis beheer van de beveiliging van een IoT-apparaat zonder een agent te installeren op basis van de IoT Hub telemetrie. 

- Geavanceerd beheer van de beveiliging van een IoT-apparaat op basis van de micro agent.

> [!NOTE]
> Defender for IoT biedt momenteel alleen ondersteuning voor IoT-hubs van de Standard-laag.

## <a name="prerequisites"></a>Vereisten

Geen

## <a name="onboard-defender-for-iot-to-an-iot-hub"></a>Onboarding Defender voor IoT naar een IoT Hub

Voor alle nieuwe IoT-hubs is Defender voor IoT **ingesteld op standaard** . U kunt controleren of Defender voor IoT wordt in-of uitgeschakeld tijdens het proces voor het maken **van** de IOT hub.

Als u wilt controleren of de wissel knop is ingesteld **op op**:

1. Navigeer naar de Azure Portal.

1. Selecteer **IOT hub** in de lijst met Azure-Services.

1. Selecteer **Maken**.

    :::image type="content" source="media/quickstart-onboard-iot-hub/create-iot-hub.png" alt-text="Selecteer de knop maken op de bovenste werk balk." lightbox="media/quickstart-onboard-iot-hub/create-iot-hub-expanded.png":::

1. Selecteer het tabblad **beheer** en controleer of **Defender voor IOT** Toggle is ingesteld op **on**.

    :::image type="content" source="media/quickstart-onboard-iot-hub/management-tab.png" alt-text="Zorg ervoor dat de schakel optie Defender voor IoT is ingesteld op aan.":::

## <a name="onboard-defender-for-iot-to-an-existing-iot-hub"></a>Onboarding Defender voor IoT naar een bestaande IoT Hub

U kunt Defender voor IoT uitvoeren op een bestaande IoT Hub, waar u vervolgens het apparaat Identity Management, het apparaat naar de Cloud en de communicatie patronen van de apparaten kunt controleren.

Voor een onboarding van Defender voor IoT naar een bestaande IoT Hub:

1. Navigeer naar het IoT Hub. 

1. Selecteer de IoT Hub voor de onboarding.

1. Selecteer een optie onder het gedeelte **beveiliging** .

1. Klik op **uw IOT-oplossing beveiligen**   en het voorbereidings formulier volt ooien. 

    :::image type="content" source="media/quickstart-onboard-iot-hub/secure-your-iot-solution.png" alt-text="Selecteer de knop uw IoT-oplossing beveiligen om uw oplossing te beveiligen.":::

De knop **beveiligd uw IOT-oplossing** wordt alleen weer gegeven als de IOT hub nog niet is uitgevoerd, of als u de Defender voor IOT in-/uitschakelen hebt **uitgeschakeld**.

:::image type="content" source="media/quickstart-onboard-iot-hub/toggle-is-off.png" alt-text="Als uw wissel knop is ingesteld op uitgeschakeld tijdens het voorbereiden.":::

## <a name="next-steps"></a>Volgende stappen

Ga naar het volgende artikel om uw oplossing te configureren...

> [!div class="nextstepaction"]
> [Een Defender IOT micro Agent-module maken (preview)](quickstart-create-micro-agent-module-twin.md)
