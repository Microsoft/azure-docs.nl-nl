---
title: Onboarden naar Defender voor IoT-oplossing op basis van een agent
description: Ontdek hoe u de Defender for IoT-beveiligingsservice in uw Azure IoT-hub inschakelt.
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
ms.openlocfilehash: 127e439a7740cb97cbe126071aaaa5245cd85782
ms.sourcegitcommit: 4784fbba18bab59b203734b6e3a4d62d1dadf031
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/08/2021
ms.locfileid: "99809125"
---
# <a name="onboard-to-defender-for-iot-agent-based-solution"></a>Onboarden naar Defender voor IoT-oplossing op basis van een agent

In dit artikel wordt uitgelegd hoe u de Defender voor IoT-service op uw bestaande IoT Hub inschakelt. Als u momenteel niet over een Azure IoT-hub beschikt, raadpleegt u [Een Azure IoT-hub maken met Azure Portal](../iot-hub/iot-hub-create-through-portal.md) om aan de slag te gaan.

U kunt uw IoT-beveiliging beheren via de IoT Hub in Defender voor IoT. Met de beheer Portal in de IoT Hub kunt u het volgende doen: 

- IoT Hub beveiliging beheren.

- Basis beheer van de beveiliging van een IoT-apparaat zonder een agent te installeren op basis van de IoT Hub telemetrie. 

- Geavanceerd beheer van de beveiliging van een IoT-apparaat op basis van de micro agent.

> [!NOTE]
> Defender for IoT biedt momenteel alleen ondersteuning voor IoT-hubs van de Standard-laag.

## <a name="onboard-to-defender-for-iot-in-iot-hub"></a>Onboarden naar Defender voor IoT in IoT Hub

Voor alle nieuwe IoT-hubs is Defender voor IoT **ingesteld op standaard** . U kunt controleren of Defender voor IoT wordt in-of uitgeschakeld tijdens het proces voor het maken **van** de IOT hub.

Als u wilt controleren of de wissel knop is ingesteld **op op**:

1. Navigeer naar de Azure Portal.

1. Selecteer **IOT hub** in de lijst met Azure-Services.

1. Selecteer **Maken**.

    :::image type="content" source="media/quickstart-onboard-iot-hub/create-iot-hub.png" alt-text="Selecteer de knop maken op de bovenste werk balk." lightbox="media/quickstart-onboard-iot-hub/create-iot-hub-expanded.png":::

1. Selecteer het tabblad **beheer** en controleer of **Defender voor IOT** Toggle is ingesteld op **on**.

    :::image type="content" source="media/quickstart-onboard-iot-hub/management-tab.png" alt-text="Zorg ervoor dat de schakel optie Defender voor IoT is ingesteld op aan.":::

## <a name="onboard-defender-for-iot-to-an-existing-iot-hub"></a>Onboarding Defender voor IoT naar een bestaande IoT Hub

U kunt uw apparaat-id-beheer, apparaat-naar-Cloud-en Cloud-naar-apparaat communicatie patronen controleren. Ga als volgt te werk om de service te starten: 

1. Navigeer naar IoT Hub. 

1. Selecteer het menu **beveiligings overzicht**   . 

1. Klik op uw IoT-oplossing beveiligen en het voorbereidings formulier volt ooien. 


## <a name="next-steps"></a>Volgende stappen

Ga naar het volgende artikel om uw oplossing te configureren...

> [!div class="nextstepaction"]
> [Een Defender IOT micro Agent-module maken (preview)](quickstart-create-micro-agent-module-twin.md)
