---
title: Een Defender IoT micro Agent-module maken (preview)
titleSuffix: Azure Defender for IoT
description: Meer informatie over het maken van een afzonderlijke DefenderIotMicroAgent-module apparaatdubbels voor nieuwe apparaten.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/20/2021
ms.topic: quickstart
ms.service: azure
ms.openlocfilehash: e1c2f4e79b9f0f0ba1ec0da0c5a2a29cdc8bc6d7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103489876"
---
# <a name="create-a-defender-iot-micro-agent-module-twin-preview"></a>Een Defender IoT micro Agent-module maken (preview)

U kunt afzonderlijke **DefenderIotMicroAgent** -module apparaatdubbels maken voor nieuwe apparaten. U kunt ook een batch maken voor de module apparaatdubbels voor alle apparaten in een IoT Hub. 

## <a name="device-twins"></a>Apparaat apparaatdubbels 

Apparaatdubbels spelen een belangrijke rol voor zowel apparaatbeheer als procesautomatisering voor IoT-oplossingen die in Azure zijn ingebouwd. 

Defender voor IoT kan volledig worden geïntegreerd met uw bestaande IoT Device Management-platform. Volledige integratie, stelt u in staat om de beveiligings status van uw apparaat te beheren, zodat u alle bestaande mogelijkheden voor apparaatbesturing kunt gebruiken. Integratie wordt bereikt door gebruik te maken van de IoT Hub dubbele methode. 

Meer informatie over het concept van [device apparaatdubbels](../iot-hub/iot-hub-devguide-device-twins.md)   in azure IOT hub. 

## <a name="defender-iot-micro-agent-twins"></a>Defender-IoT-micro-agent apparaatdubbels 

Defender voor IoT maakt gebruik van een Defender-IoT-micro agent voor elk apparaat. De Defender-IoT-micro agent is van toepassing op alle informatie die relevant is voor de beveiliging van apparaten, voor elk specifiek apparaat in uw oplossing. De beveiligings eigenschappen van het apparaat worden geconfigureerd via een speciale Defender-IoT-micro agent-twee voor veiliger communicatie, om updates in te scha kelen en onderhoud waarvoor minder resources zijn vereist. 

## <a name="understanding-defenderiotmicroagent-module-twins"></a>Informatie over DefenderIotMicroAgent-module apparaatdubbels 

Apparaatdubbels speelt een belang rijke rol in Apparaatbeheer en proces automatisering voor IoT-oplossingen die zijn ingebouwd in Azure.

Met Defender voor IoT beschikt u over de mogelijkheid om uw bestaande IoT Device Management-platform volledig te integreren, zodat u de beveiligings status van uw apparaat kunt beheren en de bestaande mogelijkheden van het besturings element voor apparaten moet gebruiken. U kunt uw Defender voor IoT integreren met behulp van het dubbele mechanisme IoT Hub.  

Zie [IOT hub module apparaatdubbels](../iot-hub/iot-hub-devguide-module-twins.md)voor meer informatie over het algemene concept van module Apparaatdubbels in azure IOT hub.

Defender voor IoT maakt gebruik van het module dubbele mechanisme en houdt een Defender-IoT-micro agent dubbele naam `DefenderIotMicroAgent` voor elk van uw apparaten bij. 

Als u optimaal wilt profiteren van alle functies van Defender voor IoT, moet u de apparaatdubbels van Defender-IoT-micro agent maken, configureren en gebruiken voor elk apparaat in de service. 

## <a name="create-defenderiotmicroagent-module-twin"></a>DefenderIotMicroAgent-module maken dubbele 

**DefenderIotMicroAgent** module apparaatdubbels kan worden gemaakt door elke module hand matig te bewerken, met inbegrip van specifieke configuraties voor elk apparaat. 

Hand matig een nieuwe **DefenderIotMicroAgent** -module maken, twee maal voor een apparaat: 

1. Zoek en selecteer in uw IoT Hub het apparaat waarop u een Defender-IoT-micro-agent dubbele wilt maken. 

1. Selecteer **module-identiteit toevoegen**. 

1. In het veld **id-naam van module**   , en voer in  `DefenderIotMicroAgent` . 

1. Selecteer **Opslaan**. 

## <a name="verify-the-creation-of-a-module-twin"></a>Het maken van een module verifiëren 

Controleren of er een Defender-IoT-micro agent-dubbele computer is voor een specifiek apparaat: 

1. Selecteer in uw Azure IoT Hub **IOT-apparaten** in   het menu **Explorers**   . 

1. Voer de apparaat-ID in of selecteer een optie in het veld **query apparaat** en selecteer **query apparaten**.  

    :::image type="content" source="media/quickstart-create-micro-agent-module-twin/iot-devices.png" alt-text="Selecteer query apparaten om een lijst met uw apparaten op te halen.":::

1. Selecteer het apparaat en open de pagina met details van het **apparaat** . 

1. Selecteer het menu **module-identiteiten**   en bevestig dat de **DefenderIotMicroAgent** -module bestaat in de lijst met module-identiteiten die zijn gekoppeld aan het apparaat.  

    :::image type="content" source="media/quickstart-create-micro-agent-module-twin/device-details-module.png" alt-text="Selecteer module-identiteiten op het tabblad.":::

## <a name="next-steps"></a>Volgende stappen 

Ga naar het volgende artikel voor meer informatie over het [onderzoeken van beveiligings aanbevelingen](quickstart-investigate-security-recommendations.md).
