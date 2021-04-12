---
title: Overzicht van Azure Industrial IoT | Microsoft Docs
description: In dit artikel vindt u een overzicht van Industrial IoT. Hierin worden de verbonden fabriek, de fabrieksconnectiviteit en beveiligingsonderdelen in IIoT uitgelegd.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 3df10c9d7630e9db76994e8e508f30adb986e0d5
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105646876"
---
# <a name="what-is-industrial-iot-iiot"></a>Wat is Industrial IoT (IIoT)?

> [!IMPORTANT]
> Raadpleeg [Azure Industrial IoT](https://azure.github.io/Industrial-IoT/) voor de meest recente inhoud terwijl we dit artikel bijwerken.

IIoT staat voor Industrial Internet of Things. IIoT verbetert de industriële efficiency door de toepassing van IoT in de productiebranche. 

## <a name="improve-industrial-efficiencies"></a>Industriële efficiëntie verbeteren

Verbeter uw operationele productiviteit en winstgevendheid met een verbonden oplossingsverbetering voor fabrieken. Verbind en bewaak uw industriële apparaten en apparaten in de cloud (inclusief uw machines die al in de fabriek werkzaam zijn). Analyseer uw IoT-gegevens voor inzichten waarmee u de prestaties van de hele fabriek kunt verbeteren.

Verminder het tijdrovende proces waarmee u toegang krijgt tot de fabrieksmachines met OPC Twin en besteed uw tijd op het bouwen van IIoT-oplossingen. Stroomlijn het beheer van certificaten en de integratie van industriële activa met OPC Vault. U kunt erop vertrouwen dat de connectiviteit van uw assets wordt beveiligd. Deze microservices bieden een REST-achtige API bovenop [Azure Industrial IoT-onderdelen](https://github.com/Azure/Industrial-IoT). De service-API biedt u de mogelijkheid om de functionaliteit van de Edge-module te beheren. 

![Overzicht van industriële IoT](media/overview-iot-industrial/overview.png)

> [!NOTE]
> Voor meer informatie over de Industrial IoT-services van Azure raadpleegt u de [opslagplaats](https://github.com/Azure/Industrial-IoT) en [documentatie](https://azure.github.io/Industrial-IoT/) van GitHub.
Als u niet bekend bent met de werking van Azure IoT Edge-modules, begint u met de volgende artikelen:
- [Over Azure IoT Edge](../iot-edge/about-iot-edge.md)
- [Azure IoT Edge-modules](../iot-edge/iot-edge-modules.md)

## <a name="connected-factory"></a>Verbonden factory

[Connected Factory](../iot-accelerators/iot-accelerators-connected-factory-features.md) is een implementatie van de referentiearchitectuur van Microsoft Azure Industrial IoT die kan worden aangepast om te voldoen aan specifieke zakelijke vereisten. De volledige oplossingscode is open-source en beschikbaar in de GitHub-opslagplaats voor de Connected Factory-oplossingsverbetering. U kunt deze gebruiken als uitgangspunt voor een commercieel product en binnen enkele minuten een vooraf gemaakte oplossing implementeren in uw Azure-abonnement. 

## <a name="factory-floor-connectivity"></a>Connectiviteit in de fabriek

OPC Twin is een IIoT-onderdeel dat detectie en registratie van apparaten automatiseert. Daarnaast biedt OPC Twin externe controle over industriële apparaten via REST API's. OPC Twin maakt gebruik van Azure IoT Edge en IoT Hub om verbinding te maken met de cloud en het fabrieksnetwerk. OPC Twin helpt IIoT-ontwikkelaars om zich te richten op het bouwen van IIoT-toepassingen zonder dat u zich zorgen hoeft te maken over een veilige toegang tot de on-premises machines.

## <a name="security"></a>Beveiliging

OPC Vault is een implementatie van OPC UA Global Discovery Server (GDS) die de levenscyclus van certificaten voor OPC UA-server en clienttoepassingen in de cloud kan configureren, registreren en beheren. OPC Vault vereenvoudigt de implementatie en het onderhoud van de beveiligde connectiviteit van assets in de industriële ruimte. OPC Vault verlost fabrieksoperators van de handmatige en complexe processen die komen kijken bij connectiviteits- en certificaatbeheer. OPC Vault doet dit door certificaatbeheer te automatiseren.

## <a name="next-steps"></a>Volgende stappen

Nu u een inleiding hebt gehad op Industrial IoT en de bijbehorende onderdelen, is dit de voorgestelde volgende stap:

[Wat is OPC Twin?](overview-opc-twin.md)
