---
title: Overzicht van Azure Industrial IoT
description: Dit artikel bevat een overzicht van industriële IoT. In dit onderwerp worden de connectiviteits-en beveiligings onderdelen voor de werk vloer in IIoT uitgelegd.
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: overview
ms.date: 3/22/2021
ms.openlocfilehash: 940391d26b5a8455fef01c8094cd08e05ab51290
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/22/2021
ms.locfileid: "104787926"
---
# <a name="what-is-industrial-iot-iiot"></a>Wat is industrieel IoT (IIoT)?

IIoT (industrieel Internet of Things) verbetert de industriële efficiency door de toepassing van IoT in de productie-industrie.

![Industriële IOT](media/overview-what-is-Industrial-IoT/icon-255-px.png)

## <a name="improve-industrial-efficiencies"></a>Industriële efficiëntie verbeteren
Verbeter uw operationele productiviteit en winstgevendheid met Azure Industrial IoT. Verminder het tijdrovende proces van de toegang tot de assets op locatie. Verbind en bewaak uw industriële apparaten en apparaten in de Cloud, inclusief uw machines die al op de fabriek werkzaam zijn. Analyseer uw IoT-gegevens voor inzichten die u helpen de prestaties van de hele site te verbeteren.

## <a name="industrial-iot-components"></a>Industriële IoT-onderdelen

**Apparaten IOT Edge** Een IoT Edge apparaat bestaat uit Edge runtime-en Edge-modules. 
- *Edge-modules* zijn docker-containers, die de kleinste reken eenheid zijn, zoals OPC-Uitgever en OPC-twee. 
- *Edge-apparaat* wordt gebruikt voor het implementeren van dergelijke modules, die fungeren als een bemiddelaar tussen de OPC UA-server en IOT hub in de Cloud. [Hier](https://azure.microsoft.com/services/iot-edge/)vindt u meer informatie over IOT Edge.

**IOT hub** De IoT Hub fungeert als een centrale Message hub voor bidirectionele communicatie tussen IoT-toepassing en de apparaten die worden beheerd. Het is een open en flexibel Cloud platform als een service die ondersteuning biedt voor open-source Sdk's en meerdere protocollen. Lees [hier](https://azure.microsoft.com/services/iot-hub/)meer over IOT hub.

**Industriële rand modules**
- *OPC-Publisher*: de OPC-Publisher wordt uitgevoerd binnen IOT Edge. Het maakt verbinding met OPC UA-servers en publiceert JSON-gecodeerde telemetriegegevens van deze servers in OPC UA ' pub/sub ' naar Azure IoT Hub. Alle transport protocollen die door de Azure IoT Hub client-SDK worden ondersteund, kunnen worden gebruikt, d.w.z. HTTPS, AMQP en MQTT.
- *OPC*: de OPC, twee bestaat uit micro services die gebruikmaken van Azure IoT Edge en IOT hub om verbinding te maken met de Cloud en het Factory-netwerk. OPC Twin biedt detectie, registratie en extern beheer van industriële apparaten via REST-API's. OPC dubbele vereist geen OPC UA-SDK (gecombineerd architectuur). De programmeer taal neutraal en kan worden opgenomen in een serverloze werk stroom.
- *Detectie*: de detectie module, die wordt vertegenwoordigd door de identiteit van een detectie service, biedt Discovery-Services aan de rand, waaronder OPC UA-server detectie. Als detectie is geconfigureerd en ingeschakeld, stuurt de module de resultaten van een scan test via de IoT Edge en IoT Hub telemetrie-pad naar de onboarding-service. De service verwerkt de resultaten en werkt alle gerelateerde identiteiten bij in het REGI ster.


**Uw industriële assets detecteren, registreren en beheren met Azure** Met Azure Industrial IoT kunnen exploitanten voor OPC UA ingeschakelde servers in een fabrieks netwerk detecteren en registreren in azure IoT Hub. Operationele mede werkers kunnen zich abonneren op gebeurtenissen in de fabriek en overal ter wereld. De REST Api's van micro Services spie gelen de rand van de OPC UA-Services. Ze zijn beveiligd met OAUTH-verificatie en autorisatie van Azure Active Directory (AAD). Hierdoor kunnen uw Cloud toepassingen door de server adres ruimten bladeren of variabelen lezen/schrijven en methoden uitvoeren met behulp van HTTPS en eenvoudige OPC UA JSON payloads.

## <a name="next-steps"></a>Volgende stappen
Nu u hebt geleerd wat industrieel IoT is, kunt u lezen over het industrieel IoT-platform en de OPC-Uitgever:

> [!div class="nextstepaction"]
> [Wat is het industrieel IoT-platform?](overview-what-is-industrial-iot-platform.md)

> [!div class="nextstepaction"]
> [Wat is de OPC-Uitgever?](overview-what-is-opc-publisher.md)