---
title: Overzicht van de beveiligings agent
description: Ga aan de slag met het leren, configureren, implementeren en gebruiken van Azure Defender voor IoT Security Service-agents op uw IoT-apparaten.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: e3fe3e910134080f6620023f539913454670d093
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96339911"
---
# <a name="get-started-with-azure-defender-for-iot-device-security-agents"></a>Aan de slag met Azure Defender voor IoT-beveiligings agenten voor apparaten

Defender voor IoT-beveiligings agenten bieden verbeterde beveiligings mogelijkheden, zoals het bewaken van externe verbindingen, actieve toepassingen, aanmeldings gebeurtenissen en aanbevolen procedures voor het configureren van besturings systemen. Neem de controle over uw apparaat veld beveiliging tegen bedreigingen en beveiligings postuur met één service.

In C# en C worden referentie architectuur voor Linux-en Windows-beveiligings agenten vermeld.

De Defender voor IoT-beveiligings agenten verwerken de onbewerkte gebeurtenis verzameling van het besturings systeem van het apparaat, het samen voegen van gebeurtenissen om de kosten te verlagen en configuratie via een apparaat-module. Beveiligings berichten worden via uw IoT Hub naar Defender voor IoT Analytics Services verzonden.

Gebruik de volgende werk stroom voor het implementeren en testen van uw Defender voor IoT-beveiligings agenten:

1. [Schakel Defender voor IoT-service in op uw IoT Hub](quickstart-onboard-iot-hub.md)
1. Als uw IoT Hub geen geregistreerde apparaten heeft, [registreert u een nieuw apparaat](../iot-accelerators/quickstart-device-simulation-deploy.md).
1. [Maak een azureiotsecurity-beveiligings module](quickstart-create-security-twin.md) voor uw apparaten.
1. Als u de agent wilt installeren op een gesimuleerd Azure-apparaat in plaats van op een echt apparaat te installeren, kunt u [een nieuwe virtuele Azure-machine (VM)](../virtual-machines/linux/quick-create-portal.md) in een beschik bare zone zetten.
1. [Implementeer een Defender voor IOT-beveiligings agent](how-to-deploy-linux-cs.md) op uw IOT-apparaat of op een nieuwe virtuele machine.
1. Volg de instructies voor [trigger_events](https://aka.ms/iot-security-github-trigger-events) om een onschadelijke simulatie van een aanval uit te voeren.
1. Verifieer Defender voor IoT-waarschuwingen als reactie op de gesimuleerde aanval in de vorige stap. Begin met de verificatie 5 minuten na het uitvoeren van het script.
1. Bekijk [waarschuwingen](concept-security-alerts.md), [aanbevelingen](concept-recommendations.md)en [diep gaande informatie met behulp van Log Analytics](how-to-security-data-access.md) met behulp van IOT hub.

## <a name="next-steps"></a>Volgende stappen

- Uw [oplossing](quickstart-configure-your-solution.md) configureren
- [Beveiligingsmodules maken](quickstart-create-security-twin.md)
- [Aangepaste waarschuwingen](quickstart-create-custom-alerts.md) configureren
- [Een beveiligingsagent implementeren](how-to-deploy-agent.md)