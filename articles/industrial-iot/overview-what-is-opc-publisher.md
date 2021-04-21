---
title: Microsoft OPC Publisher
description: In dit artikel vindt u een overzicht van de OPC Publisher Edge-module.
author: v-condav
ms.author: jemorina
ms.service: industrial-iot
ms.topic: conceptual
ms.date: 3/22/2021
ms.openlocfilehash: 6df39c93e9bcfca522ac61a863c87269216cc592
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107816166"
---
# <a name="what-is-the-opc-publisher"></a>Wat is de OPC Publisher?

OPC Publisher is een volledig ondersteund Microsoft-product dat de hiaat tussen industriële assets en de Microsoft Azure cloud overbrugt. Dit gebeurt door opC UA ingeschakelde assets of software voor industriële connectiviteit te verbinden met uw Microsoft Azure cloud. Het publiceert de telemetriegegevens die worden verzameld voor Azure IoT Hub in verschillende indelingen, waaronder IEC62541 OPC UA PubSub-standaardindeling (vanaf versie 2.6). OPC Publisher wordt op Azure IoT Edge uitgevoerd als een module of op gewone Docker als een container. Omdat het gebruik maakt van de platformoverschrijdende .NET-runtime, wordt deze systeemeigen uitgevoerd op Zowel Linux als Windows 10.

OPC Publisher is een referentie-implementatie die u het volgende laat zien:

- Verbinding maken met bestaande OPC UA-servers.
- Publiceer met JSON gecodeerde telemetriegegevens van OPC UA-servers in OPC UA Pub/Sub-indeling met behulp van een JSON-nettolading naar een Azure IoT Hub.

U kunt elk van de transportprotocollen gebruiken die de Azure IoT Hub client-SDK ondersteunt, zoals HTTPS, AMQP en MQTT.

De referentie-implementatie omvat het volgende.

- Een OPC UA-*client* om verbinding te maken met bestaande OPC UA-servers die u in uw netwerk hebt.
- Een OPC UA-*server* op poort 62222 die u kunt gebruiken voor het beheren van wat er wordt gepubliceerd en IoT Hub directe methoden biedt om hetzelfde te doen.

U kunt de [referentie-implementatie van OPC Publisher](https://github.com/Azure/iot-edge-opc-publisher) downloaden van GitHub.

De toepassing wordt geïmplementeerd met behulp van .NET Core-technologie en kan worden uitgevoerd op elk platform dat door .NET core wordt ondersteund.

## <a name="what-does-the-opc-publisher-do"></a>Wat doet de OPC Publisher?

OPC Publisher implementeert logica voor nieuwe pogingen om verbinding tot stand te brengen met eindpunten die niet reageren op een bepaald aantal Keep Alive-aanvragen. Bijvoorbeeld als een OPC UA-server niet meer reageert vanwege een stroomstoring.

Voor elke afzonderlijk publicatie-interval naar een OPC UA-server maakt de toepassing een afzonderlijk abonnement via welk alle knooppunten met dit publicatie-interval worden bijgewerkt.

OPC Publisher ondersteunt batchverwerking van de gegevens die worden verzonden naar IoT Hub om de netwerkbelasting te verminderen. Deze batchverwerking verzendt alleen een pakket naar IoT Hub als de geconfigureerde pakketgrootte is bereikt.

Deze toepassing maakt gebruik van de OPC Foundation OPC UA-referentiestack als NuGet-pakketten. Zie [https://opcfoundation.org/license/redistributables/1.3/](https://opcfoundation.org/license/redistributables/1.3/) voor de licentievoorwaarden.

## <a name="next-steps"></a>Volgende stappen
Nu u hebt geleerd wat de OPC Publisher is, kunt u aan de slag gaan door deze te implementeren:

> [!div class="nextstepaction"]
> [OPC Publisher implementeren in zelfstandige modus](tutorial-publisher-deploy-opc-publisher-standalone.md)
