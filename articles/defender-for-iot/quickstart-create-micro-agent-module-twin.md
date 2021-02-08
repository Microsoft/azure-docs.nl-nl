---
title: Een Defender IOT micro Agent-module maken (preview)
titleSuffix: Azure Defender for IoT
description: Meer informatie over het maken van een afzonderlijke DefenderIotMicroAgent-module apparaatdubbels voor nieuwe apparaten.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/20/2021
ms.topic: quickstart
ms.service: azure
ms.openlocfilehash: 7935db0a78db59abaf2e9983c5ebb56bb4365a7e
ms.sourcegitcommit: 4784fbba18bab59b203734b6e3a4d62d1dadf031
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/08/2021
ms.locfileid: "99810132"
---
# <a name="create-a-defender-iot-micro-agent-module-twin-preview"></a>Een Defender IOT micro Agent-module maken (preview)

U kunt afzonderlijke **DefenderIotMicroAgent** -module apparaatdubbels maken voor nieuwe apparaten. U kunt ook een batch maken voor de module apparaatdubbels voor alle apparaten in een IoT Hub. 

## <a name="device-twins"></a>Apparaat apparaatdubbels 

Apparaatdubbels spelen een belangrijke rol voor zowel apparaatbeheer als procesautomatisering voor IoT-oplossingen die in Azure zijn ingebouwd. 

Defender voor IoT kan volledig worden geïntegreerd met uw bestaande IoT Device Management-platform. Volledige integratie, stelt u in staat om de beveiligings status van uw apparaat te beheren, zodat u alle bestaande mogelijkheden voor apparaatbesturing kunt gebruiken. Integratie wordt bereikt door gebruik te maken van de IoT Hub dubbele methode. 

Meer informatie over het concept van [device apparaatdubbels](../iot-hub/iot-hub-devguide-device-twins.md)   in azure IOT hub. 

## <a name="security-module-twins"></a>Beveiligings module apparaatdubbels 

Defender voor IoT gebruikt een beveiligings module voor elk apparaat. De beveiligings module bevat alle informatie die relevant is voor de beveiliging van apparaten, voor elk specifiek apparaat in uw oplossing. Beveiligings eigenschappen van apparaten worden geconfigureerd via een speciale beveiligings module, met een eigen communicatie, om updates en onderhoud in te scha kelen waarvoor minder resources nodig zijn. 

## <a name="understanding-defenderiotmicroagent-module-twins"></a>Informatie over DefenderIotMicroAgent-module apparaatdubbels 

Apparaatdubbels speelt een belang rijke rol in Apparaatbeheer en proces automatisering voor IoT-oplossingen die zijn ingebouwd in Azure.

Met Defender voor IoT beschikt u over de mogelijkheid om uw bestaande IoT Device Management-platform volledig te integreren, zodat u de beveiligings status van uw apparaat kunt beheren en de bestaande mogelijkheden van het besturings element voor apparaten moet gebruiken. U kunt uw Defender voor IoT integreren met behulp van het dubbele mechanisme IoT Hub.  

Zie [IOT hub module apparaatdubbels](../iot-hub/iot-hub-devguide-module-twins.md)voor meer informatie over het algemene concept van module Apparaatdubbels in azure IOT hub.

Defender voor IoT maakt gebruik van het module-dubbel mechanisme en houdt een beveiligings module toe met een dubbele naam `DefenderIotMicroAgent` voor elk van uw apparaten. 

Als u optimaal wilt profiteren van alle functies van Defender voor IoT, moet u de beveiligings module apparaatdubbels maken, configureren en gebruiken voor elk apparaat in de service. 

## <a name="create-defenderiotmicroagent-module-twin"></a>DefenderIotMicroAgent-module maken dubbele 

**DefenderIotMicroAgent** module apparaatdubbels kan worden gemaakt door elke module hand matig te bewerken, met inbegrip van specifieke configuraties voor elk apparaat. 

Hand matig een nieuwe **DefenderIotMicroAgent** -module maken, twee maal voor een apparaat: 

1. Zoek in uw IoT Hub het apparaat waarop u een beveiligings module wilt maken en selecteer deze. 

1. Selecteer **module-identiteit toevoegen**. 

1. In het veld **id-naam van module**   , en voer in  `DefenderIotMicroAgent` . 

1. Selecteer **Opslaan**. 

## <a name="verify-the-creation-of-a-module-twin"></a>Het maken van een module verifiëren 

Ga als volgt te werk om te controleren of er een moduledubbel bestaat voor een specifiek apparaat: 

1. Selecteer in uw Azure IoT Hub **IOT-apparaten** in   het menu **Explorers**   . 

1. Voer de apparaat-ID in of selecteer een optie in het veld **query apparaat** en selecteer **query apparaten**.  

    :::image type="content" source="media/quickstart-create-micro-agent-module-twin/iot-devices.png" alt-text="Selecteer query apparaten om een lijst met uw apparaten op te halen.":::

1. Selecteer het apparaat en open de pagina met details van het **apparaat** . 

1. Selecteer het menu **module-identiteiten**   en bevestig dat de **DefenderIotMicroAgent** -module bestaat in de lijst met module-identiteiten die zijn gekoppeld aan het apparaat.  

    :::image type="content" source="media/quickstart-create-micro-agent-module-twin/device-details-module.png" alt-text="Selecteer module-identiteiten op het tabblad.":::

## <a name="next-steps"></a>Volgende stappen 

Ga naar het volgende artikel voor meer informatie over het [onderzoeken van beveiligings aanbevelingen](quickstart-investigate-security-recommendations.md).
