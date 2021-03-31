---
title: Azure IoT-protocol gateway | Microsoft Docs
description: Een Azure IoT-protocol gateway gebruiken om IoT Hub mogelijkheden en protocol ondersteuning uit te breiden om apparaten verbinding te laten maken met uw hub met behulp van protocollen die niet door IoT Hub systeem eigen worden ondersteund.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/11/2017
ms.custom:
- amqp
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
ms.openlocfilehash: 315d76715973b79a971f3ca15bcc5186d20135a2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87325029"
---
# <a name="support-additional-protocols-for-iot-hub"></a>Aanvullende protocollen voor IoT Hub ondersteunen

Azure IoT Hub biedt systeem eigen ondersteuning voor communicatie via het MQTT-, AMQP-en HTTPS-protocol. In sommige gevallen is het mogelijk dat apparaten of veld gateways een van deze standaard protocollen niet kunnen gebruiken en dat het protocol niet kan worden aangepast. In dergelijke gevallen kunt u een aangepaste gateway gebruiken. Met een aangepaste gateway kan het protocol worden aangepast voor IoT Hub eind punten door het verkeer van en naar IoT Hub te bridgen. U kunt de [Azure IOT-protocol gateway](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md) als een aangepaste gateway gebruiken om protocol aanpassing voor IOT hub in te scha kelen.

## <a name="azure-iot-protocol-gateway"></a>Azure IoT-protocol gateway

De Azure IoT-protocol gateway is een framework voor protocol aanpassing dat is ontworpen voor het communiceren van een hoge schaal, bidirectionele apparaten met IoT Hub. De protocol gateway is een Pass-Through-onderdeel dat verbindingen met een apparaat accepteert via een specifiek protocol. Het conbrugt het verkeer naar IoT Hub over AMQP 1,0.

U kunt de protocol gateway in azure op een zeer schaal bare manier implementeren met behulp van Azure Service Fabric, Azure Cloud Services worker-werk rollen of Windows Virtual Machines. Daarnaast kan de protocol gateway worden geïmplementeerd in on-premises omgevingen, zoals veld gateways.

De Azure IoT-protocol gateway bevat een MQTT-protocol adapter waarmee u, indien nodig, het MQTT-protocol gedrag kunt aanpassen. Omdat IoT Hub ingebouwde ondersteuning biedt voor het MQTT v 3.1.1-protocol, kunt u alleen overwegen de MQTT-protocol adapter te gebruiken als protocol aanpassingen of specifieke vereisten voor aanvullende functionaliteit zijn vereist.

De MQTT-adapter demonstreert ook het programmeer model voor het bouwen van protocol adapters voor andere protocollen. Daarnaast kunt u met het Azure IoT protocol-programmeer model aangepaste onderdelen voor speciale verwerking, zoals aangepaste verificatie, bericht transformaties, compressie/decompressie of versleuteling/ontsleuteling van verkeer tussen de apparaten en IoT Hub, toevoegen.

Voor flexibiliteit worden de Azure IoT-protocol gateway en MQTT-implementatie weer gegeven in een open-source software project. U kunt het open-source project gebruiken om ondersteuning toe te voegen voor diverse protocollen en protocol versies, of de implementatie van uw scenario aan te passen. 

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over de Azure IoT-protocol gateway en hoe u deze kunt gebruiken en implementeren als onderdeel van uw IoT-oplossing:

* [Opslag plaats voor Azure IoT-protocol gateway op GitHub](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md)

* [Ontwikkelaars handleiding voor Azure IoT-protocol gateway](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/docs/DeveloperGuide.md)

Zie voor meer informatie over het plannen van uw IoT Hub-implementatie:

* [Vergelijken met Event Hubs](iot-hub-compare-event-hubs.md)

* [Schalen, hoge Beschik baarheid en herstel na nood geval](iot-hub-scaling.md)

* [Ontwikkelaars handleiding IoT Hub](iot-hub-devguide.md)