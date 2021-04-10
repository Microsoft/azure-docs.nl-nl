---
title: 'Snelstartgids: systeem vereisten'
description: In deze Snelstartgids kunt u de systeem vereisten ophalen die nodig zijn om Azure Defender voor IoT uit te voeren.
ms.date: 11/30/2020
ms.topic: quickstart
ms.openlocfilehash: 2aae849b6ee772c2aa29c680f3b107af3ed600b0
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/05/2021
ms.locfileid: "106382943"
---
# <a name="quickstart-system-prerequisites"></a>Snelstartgids: systeem vereisten

In dit artikel worden de systeemvereisten vermeld voor het uitvoeren van Azure Defender voor IoT.

## <a name="prerequisites"></a>Vereisten

- Geen

## <a name="minimum-requirements"></a>Minimale vereisten

- Netwerkswitches die ondersteuning bieden voor verkeersbewaking via de SPAN-poort.
- Hardware-apparaten voor NTA-sensoren.
- De rol Inzender voor het Azure-abonnement. Dit is alleen vereist tijdens onboarding, om doorgevoerde apparaten en verbinding met Azure Sentinel te definiëren.
- De rol **Inzender** voor Azure IoT Hub (Gratis laag Standard-laag), voor cloudverbonden beheer. Zorg ervoor dat de functie **Azure Defender for IoT** is ingeschakeld.
- Voor Defender-IoT-ondersteuning op apparaatniveau ondersteunt Defender voor IoT-agents een groeiende lijst met apparaten en platformen. Bekijk de [lijst met ondersteunde platforms](how-to-deploy-agent.md).

## <a name="supported-service-regions"></a>Ondersteunde serviceregio's

Defender for IoT routeert al het verkeer uit alle Europese regio's naar het regionale datacentrum West-Europa. Het verkeer uit alle resterende regio's wordt naar het regionale datacentrum VS - centraal gerouteerd.

Zie [Ondersteunde regio's voor IoT Hub](https://azure.microsoft.com/global-infrastructure/services/?products=iot-hub) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Vereiste apparaten identificeren](how-to-identify-required-appliances.md)
