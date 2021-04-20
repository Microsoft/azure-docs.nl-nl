---
title: Conceptuele uitleg van de basisbeginselen van de Defender-IoT-micro-agent voor Azure RTOS
description: Leer de basisbeginselen van de Defender-IoT-micro-agent voor Azure RTOS concepten en werkstroom.
ms.topic: conceptual
ms.date: 09/09/2020
ms.openlocfilehash: 04b86d401bcb9fc919c36b28cf4f80ea3bfd7030
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107750478"
---
# <a name="defender-iot-micro-agent-for-azure-rtos-preview"></a>Defender-IoT-micro-agent voor Azure RTOS (preview)

Gebruik dit artikel voor een beter begrip van de Defender-IoT-micro-agent voor Azure RTOS, inclusief functies en voordelen, evenals koppelingen naar relevante configuratie- en referentieresources. 

## <a name="azure-rtos-iot-defender-iot-micro-agent"></a>Azure RTOS IoT Defender-IoT-micro-agent

Defender-IoT-micro-agent voor Azure RTOS biedt een uitgebreide beveiligingsoplossing voor Azure RTOS-apparaten als onderdeel van de NetX Duo-aanbieding. Binnen het NetX Duo-aanbod wordt Azure RTOS geleverd met de ingebouwde Azure IoT Defender-IoT-micro-agent en biedt dekking voor veelvoorkomende bedreigingen op uw realtime besturingssysteemapparaten zodra deze zijn geactiveerd.

De Defender-IoT-micro-agent voor Azure RTOS wordt op de achtergrond uitgevoerd en biedt een naadloze gebruikerservaring, terwijl beveiligingsberichten worden verzonden met behulp van de unieke verbindingen van elke klant met hun IoT Hub. De Defender-IoT-micro-agent voor Azure RTOS is standaard ingeschakeld.  

## <a name="azure-rtos-netx-duo"></a>Azure RTOS NetX Duo

Azure RTOS NetX Duo is een geavanceerde TCP/IP-netwerkstack van industriële kwaliteit die speciaal is ontworpen voor diep ingesloten realtime- en IoT-toepassingen. Azure RTOS NetX Duo is een dubbele IPv4- en IPv6-netwerkstack die een uitgebreide set protocollen biedt, waaronder beveiliging en cloud. Meer informatie over [Azure RTOS NetX Duo-oplossingen.](/azure/rtos/netx-duo/)

De module biedt de volgende functies:

- **Schadelijke netwerkactiviteiten detecteren**
- **Basislijnen voor apparaatgedrag op basis van aangepaste waarschuwingen**
- **Beveiligingshygiëne van apparaten verbeteren**

## <a name="defender-iot-micro-agent-for-azure-rtos-architecture"></a>Defender-IoT-micro-agent voor Azure RTOS architectuur

De Defender-IoT-micro-agent voor Azure RTOS wordt door het Azure IoT-middlewareplatform initialiseren en gebruikt IoT Hub-clients om beveiligingstelemetrie te verzenden naar de hub.

:::image type="content" source="media/architecture/security-module-state-diagram.png" alt-text="Statusdiagram en informatiestroom van Azure IoT Defender-IoT-micro-agent":::

De Defender-IoT-micro-agent voor Azure RTOS de volgende apparaatactiviteit en -informatie met behulp van drie collectoren:
- Apparaatnetwerkactiviteit **TCP,** **UDP** en **ICM**
- Systeeminformatie als **Threadx-** **en NetX Duo-versies**
- Heartbeat-gebeurtenissen

Elke collector is gekoppeld aan een prioriteitsgroep en elke prioriteitsgroep heeft een eigen interval met mogelijke waarden **van Laag,** **Gemiddeld** en **Hoog.** De intervallen zijn van invloed op het tijdsinterval waarin de gegevens worden verzameld en verzonden.

Elk tijdsinterval kan worden geconfigureerd en de IoT-connectors kunnen worden ingeschakeld en uitgeschakeld om uw [oplossing verder aan te passen.](how-to-azure-rtos-security-module.md) 

## <a name="supported-security-alerts-and-recommendations"></a>Ondersteunde beveiligingswaarschuwingen en aanbevelingen

De Defender-IoT-micro-agent voor Azure RTOS ondersteunt specifieke beveiligingswaarschuwingen en aanbevelingen. Controleer en pas [de relevante waarschuwings-](concept-rtos-security-alerts-recommendations.md) en aanbevelingswaarden voor uw service aan na het voltooien van de eerste configuratie.

## <a name="ready-to-begin"></a>Klaar om te beginnen?

Defender-IoT-micro-agent voor Azure RTOS wordt geleverd als een gratis download voor uw IoT-apparaten. De Defender for IoT-cloudservice is beschikbaar met een proefversie van 30 dagen per Azure-abonnement. [Download nu de Defender-IoT-micro-agent](https://github.com/azure-rtos/azure-iot-preview/releases) en ga aan de slag. 

## <a name="next-steps"></a>Volgende stappen

- Ga aan de slag met Defender-IoT-micro-agent voor Azure RTOS [vereisten en het instellen van](quickstart-azure-rtos-security-module.md).
- Meer informatie over Defender-IoT-micro-agent voor Azure RTOS [beveiligingswaarschuwingen en aanbevelingsondersteuning.](concept-rtos-security-alerts-recommendations.md) 
- Gebruik de Defender-IoT-micro-agent voor Azure RTOS [referentie-API.](azure-rtos-security-module-api.md)