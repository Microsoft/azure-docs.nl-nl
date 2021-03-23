---
title: Micro soft OPC Publisher
description: Dit artikel bevat een overzicht van de OPC Publisher Edge-module.
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: conceptual
ms.date: 3/22/2021
ms.openlocfilehash: 3a44bdbadfe6ecd86a1b98fb7002f2d75c23bb6a
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104800530"
---
# <a name="what-is-the-opc-publisher"></a>Wat is de OPC-Uitgever?

OPC Publisher is een volledig ondersteund micro soft-product dat is ontwikkeld in de open, waardoor de kloof tussen industriële activa en de Microsoft Azure Cloud wordt overbrugd. Dit doet u door verbinding te maken met OPC UA-ingeschakelde assets of industriële connectiviteits software en om telemetriegegevens te publiceren naar Azure IoT Hub in verschillende indelingen, waaronder IEC62541 OPC UA PubSub Standard-indeling (vanaf versie 2,6 en hoger).

Het wordt uitgevoerd op Azure IoT Edge als een module of op een gewone docker als container. Omdat het gebruikmaakt van .NET platformoverschrijdende runtime, wordt het ook systeem eigen uitgevoerd op Linux en Windows 10.

OPC Publisher is een referentie-implementatie die u het volgende laat zien:

- Verbinding maken met bestaande OPC UA-servers.
- Met JSON gecodeerde telemetriegegevens van OPC UA-servers in OPC UA pub/sub-indeling met behulp van een JSON-payload naar Azure IoT Hub publiceren.

U kunt elk van de transportprotocollen gebruiken die de Azure IoT Hub client-SDK ondersteunt: HTTPS, AMQP en MQTT.

De referentie-implementatie omvat:

- Een OPC UA-*client* om verbinding te maken met bestaande OPC UA-servers die u in uw netwerk hebt.
- Een OPC UA-*server* op poort 62222 die u kunt gebruiken voor het beheren van wat er wordt gepubliceerd en IoT Hub directe methoden biedt om hetzelfde te doen.

U kunt de [referentie-implementatie van OPC Publisher](https://github.com/Azure/iot-edge-opc-publisher) downloaden van GitHub.

De toepassing wordt geïmplementeerd met behulp van .NET Core-technologie en kan worden uitgevoerd op elk platform dat door .NET core wordt ondersteund.

## <a name="what-does-the-opc-publisher-do"></a>Wat doet de OPC-Uitgever?

OPC Publisher implementeert logica voor nieuwe pogingen om verbinding tot stand te brengen met eindpunten die niet reageren op een bepaald aantal Keep Alive-aanvragen. Bijvoorbeeld als een OPC UA-server niet meer reageert vanwege een stroomstoring.

Voor elke afzonderlijk publicatie-interval naar een OPC UA-server maakt de toepassing een afzonderlijk abonnement via welk alle knooppunten met dit publicatie-interval worden bijgewerkt.

OPC Publisher ondersteunt batchverwerking van de gegevens die worden verzonden naar IoT Hub om de netwerkbelasting te verminderen. Deze batchverwerking verzendt alleen een pakket naar IoT Hub als de geconfigureerde pakketgrootte is bereikt.

Deze toepassing maakt gebruik van de OPC Foundation OPC UA-referentiestack als NuGet-pakketten. Zie [https://opcfoundation.org/license/redistributables/1.3/](https://opcfoundation.org/license/redistributables/1.3/) voor de licentievoorwaarden.

## <a name="next-steps"></a>Volgende stappen
Nu u weet wat de OPC-Publisher is, kunt u aan de slag gaan door deze te implementeren:

> [!div class="nextstepaction"]
> [OPC-Uitgever implementeren in zelfstandige modus](tutorial-publisher-deploy-opc-publisher-standalone.md)
