---
title: Systeemvereisten
description: Verkrijg de systeemvereisten die nodig zijn om Azure Defender voor IoT uit te voeren.
ms.date: 11/30/2020
ms.topic: quickstart
ms.openlocfilehash: 78d5948554ae531c4b2f77d67bb916d5290db943
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104780936"
---
# <a name="system-prerequisites"></a>Systeemvereisten
In dit artikel worden de systeemvereisten vermeld voor het uitvoeren van Azure Defender voor IoT.

## <a name="minimum-requirements"></a>Minimale vereisten

- Netwerkswitches die ondersteuning bieden voor verkeersbewaking via de SPAN-poort.
- Hardware-apparaten voor NTA-sensoren.
- De rol Inzender voor het Azure-abonnement. Dit is alleen vereist tijdens onboarding, om doorgevoerde apparaten en verbinding met Azure Sentinel te definiëren.
- De rol **Inzender** voor Azure IoT Hub (Gratis laag Standard-laag), voor cloudverbonden beheer. Zorg ervoor dat de functie **Azure Defender for IoT** is ingeschakeld.
- Voor Defender-IoT-ondersteuning op apparaatniveau ondersteunt Defender voor IoT-agents een groeiende lijst met apparaten en platformen. Bekijk de [lijst met ondersteunde platforms](how-to-deploy-agent.md).

## <a name="supported-service-regions"></a>Ondersteunde serviceregio's

Defender for IoT routeert al het verkeer uit alle Europese regio's naar het regionale datacentrum West-Europa. Het verkeer uit alle resterende regio's wordt naar het regionale datacentrum VS - centraal gerouteerd.

Zie [Ondersteunde regio's voor IoT Hub](https://azure.microsoft.com/global-infrastructure/services/?products=iot-hub) voor meer informatie.

## <a name="see-also"></a>Zie tevens

- [Vereiste apparaten identificeren](how-to-identify-required-appliances.md)
- [Over de installatie van het Azure Defender for IoT-netwerk](how-to-set-up-your-network.md)
