---
title: Systeemvereisten
description: Verkrijg de systeemvereisten die nodig zijn om Azure Defender voor IoT uit te voeren.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 11/30/2020
ms.topic: quickstart
ms.service: azure
ms.openlocfilehash: 7b609fec2a47dc0685b30dac12f43263127f70ef
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/15/2021
ms.locfileid: "100523206"
---
# <a name="system-prerequisites"></a>Systeemvereisten
In dit artikel worden de systeemvereisten vermeld voor het uitvoeren van Azure Defender voor IoT.

## <a name="minimum-requirements"></a>Minimale vereisten

- Netwerkswitches die ondersteuning bieden voor verkeersbewaking via de SPAN-poort.
- Hardware-apparaten voor NTA-sensoren.
- De rol Inzender voor het Azure-abonnement. Dit is alleen vereist tijdens onboarding, om doorgevoerde apparaten en verbinding met Azure Sentinel te definiëren.
- De rol **Inzender** voor Azure IoT Hub (Gratis laag Standard-laag), voor cloudverbonden beheer. Zorg ervoor dat de functie **Azure Defender for IoT** is ingeschakeld.
- Defender for IoT-agents bieden een groeiende lijst met apparaten en platforms voor ondersteuning van de beveiligingsmodule op apparaatniveau. Bekijk de [lijst met ondersteunde platforms](how-to-deploy-agent.md).

## <a name="supported-service-regions"></a>Ondersteunde serviceregio's

Defender for IoT routeert al het verkeer uit alle Europese regio's naar het regionale datacentrum West-Europa. Het verkeer uit alle resterende regio's wordt naar het regionale datacentrum VS - centraal gerouteerd.

Zie [Ondersteunde regio's voor IoT Hub](https://azure.microsoft.com/global-infrastructure/services/?products=iot-hub) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

[Vereiste apparaten identificeren](how-to-identify-required-appliances.md) 
 [Over Azure Defender voor IOT-netwerk installatie](how-to-set-up-your-network.md)
