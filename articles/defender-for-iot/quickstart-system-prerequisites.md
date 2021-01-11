---
title: Systeemvereisten
description: Verkrijg de systeemvereisten die nodig zijn om Azure Defender voor IoT uit te voeren.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 11/30/2020
ms.topic: quickstart
ms.service: azure
ms.openlocfilehash: 8ee3afcae69ca6c082452e590eb8370bcc122af4
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/31/2020
ms.locfileid: "97844457"
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

## <a name="see-also"></a>Zie tevens

- [Vereiste apparaten identificeren](how-to-identify-required-appliances.md)
- [Over de installatie van het Azure Defender for IoT-netwerk](how-to-set-up-your-network.md)
