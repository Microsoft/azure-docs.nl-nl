---
title: Conceptuele uitleg van de basis principes van de beveiligings module voor Azure RTO'S
description: Meer informatie over de basis beginselen van de beveiligings module voor Azure RTO'S-concepten en-werk stromen.
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
ms.date: 09/09/2020
ms.author: mlottner
ms.openlocfilehash: 8f521bd593369509fd520831f90ce9c601227f09
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96340047"
---
# <a name="security-module-for-azure-rtos-preview"></a>Beveiligings module voor Azure RTO'S (preview-versie)

Gebruik dit artikel om een beter inzicht te krijgen in de beveiligings module voor Azure RTO'S, met inbegrip van functies en voor delen, evenals koppelingen naar relevante configuratie-en referentie bronnen. 

## <a name="azure-rtos-iot-security-module"></a>Azure RTO'S IoT-beveiligings module

De beveiligings module voor Azure RTO'S biedt een uitgebreide beveiligings oplossing voor Azure RTO'S-apparaten als onderdeel van de NetX Duo-aanbieding. Binnen de NetX Duo-aanbieding wordt Azure RTO'S geleverd met de ingebouwde Azure IoT-beveiligings module, en biedt dit een dekking van algemene bedreigingen op uw real-time besturingssysteem apparaten nadat deze is geactiveerd. 

De beveiligings module voor Azure RTO'S wordt op de achtergrond uitgevoerd en biedt een naadloze gebruikers ervaring tijdens het verzenden van beveiligings berichten met de unieke verbindingen van elke klant naar hun IoT Hub. De beveiligings module voor Azure RTO'S is standaard ingeschakeld.  

## <a name="azure-rtos-netx-duo"></a>Azure RTOS NetX Duo

Azure RTO'S NetX Duo is een geavanceerde TCP/IP-netwerk stack met hoge kwaliteit die speciaal is ontworpen voor diep Inge sloten real-time-en IoT-toepassingen. Azure RTO'S NetX Duo is een Dual IPv4-en IPv6-netwerk stack met een uitgebreide set protocollen, waaronder beveiliging en Cloud. Meer informatie over [Azure Rto's netx Duo](/azure/rtos/netx-duo/) -oplossingen.

De module biedt de volgende functies:

- **Schadelijke netwerk activiteiten detecteren**
- **Gedrags basislijnen voor apparaten op basis van aangepaste waarschuwingen**
- **Beveiligings hygiëne van het apparaat verbeteren**

## <a name="security-module-for-azure-rtos-architecture"></a>Beveiligings module voor Azure RTO'S-architectuur

De beveiligings module voor Azure RTO'S wordt geïnitialiseerd door het Azure IoT-middleware-platform en gebruikt IoT Hub-clients om beveiligings-telemetrie naar de hub te verzenden.

:::image type="content" source="media/architecture/security-module-state-diagram.png" alt-text="Status diagram en informatie stroom van Azure IoT Security module":::

De beveiligings module voor Azure RTO'S bewaakt de volgende activiteit van het apparaat en informatie met behulp van drie Verzamel modules:
- Netwerk activiteit van apparaat **TCP**, **UDP** en **ICM**
- Systeem gegevens als **threadx** en **netx Duo** -versies
- Heartbeat-gebeurtenissen

Elke Collector is gekoppeld aan een prioriteits groep en elke prioriteits groep heeft een eigen interval met mogelijke waarden van **laag**, **gemiddeld** en **hoog**. De intervallen beïnvloeden het tijds interval waarin de gegevens worden verzameld en verzonden.

Elke tijds interval kan worden geconfigureerd en de IoT-Connectors kunnen worden ingeschakeld en uitgeschakeld om [uw oplossing](how-to-azure-rtos-security-module.md)verder aan te passen. 

## <a name="supported-security-alerts-and-recommendations"></a>Ondersteunde beveiligings waarschuwingen en aanbevelingen

De beveiligings module voor Azure RTO'S ondersteunt specifieke beveiligings waarschuwingen en aanbevelingen. Zorg ervoor dat u [de relevante waarschuwings-en aanbevelings waarden](concept-rtos-security-alerts-recommendations.md) voor uw service controleert en wijzigt nadat u de eerste configuratie hebt voltooid.

## <a name="ready-to-begin"></a>Klaar om te beginnen?

De beveiligings module voor Azure RTO'S is beschikbaar als gratis down load voor uw IoT-apparaten. De Defender voor IoT-Cloud service is beschikbaar met een proef versie van 30 dagen per Azure-abonnement. [Down load de Security module nu](https://github.com/azure-rtos/azure-iot-preview/releases) en we gaan aan de slag. 

## <a name="next-steps"></a>Volgende stappen

- Aan de slag met de beveiligings module voor Azure RTO'S- [vereisten en-Setup](quickstart-azure-rtos-security-module.md).
- Meer informatie over de beveiligings module voor Azure RTO'S- [beveiligings waarschuwingen en aanbevelingen](concept-rtos-security-alerts-recommendations.md). 
- Gebruik de beveiligings module voor Azure RTO'S [Reference-API](azure-rtos-security-module-api.md).