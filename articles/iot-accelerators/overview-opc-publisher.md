---
title: Wat is OPC Publisher - Azure | Microsoft Docs
description: In dit artikel vindt u een overzicht van de functies van OPC Publisher. Hiermee kunt u gecodeerde JSON-telemetriegegevens met behulp van een JSON-payload publiceren naar Azure IoT Hub.
author: dominicbetts
ms.author: dobett
ms.date: 06/10/2019
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 49ca12ed4f408e2a3fce1c8e59f541778f35311e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105646846"
---
# <a name="what-is-opc-publisher"></a>Wat is OPC Publisher?

> [!IMPORTANT]
> Raadpleeg [Azure Industrial IoT](https://azure.github.io/Industrial-IoT/) voor de meest recente inhoud terwijl we dit artikel bijwerken.

OPC Publisher is een referentie-implementatie die u het volgende laat zien:

- Verbinding maken met bestaande OPC UA-servers.
- Met JSON gecodeerde telemetriegegevens van OPC UA-servers in OPC UA pub/sub-indeling met behulp van een JSON-payload naar Azure IoT Hub publiceren.

U kunt elk van de transportprotocollen gebruiken die de Azure IoT Hub client-SDK ondersteunt: HTTPS, AMQP en MQTT.

De referentie-implementatie omvat:

- Een OPC UA-*client* om verbinding te maken met bestaande OPC UA-servers die u in uw netwerk hebt.
- Een OPC UA-*server* op poort 62222 die u kunt gebruiken voor het beheren van wat er wordt gepubliceerd en IoT Hub directe methoden biedt om hetzelfde te doen.

U kunt de [referentie-implementatie van OPC Publisher](https://github.com/Azure/iot-edge-opc-publisher) downloaden van GitHub.

De toepassing wordt geïmplementeerd met behulp van .NET Core-technologie en kan worden uitgevoerd op elk platform dat door .NET core wordt ondersteund.

OPC Publisher implementeert logica voor nieuwe pogingen om verbinding tot stand te brengen met eindpunten die niet reageren op een bepaald aantal Keep Alive-aanvragen. Bijvoorbeeld als een OPC UA-server niet meer reageert vanwege een stroomstoring.

Voor elke afzonderlijk publicatie-interval naar een OPC UA-server maakt de toepassing een afzonderlijk abonnement via welk alle knooppunten met dit publicatie-interval worden bijgewerkt.

OPC Publisher ondersteunt batchverwerking van de gegevens die worden verzonden naar IoT Hub om de netwerkbelasting te verminderen. Deze batchverwerking verzendt alleen een pakket naar IoT Hub als de geconfigureerde pakketgrootte is bereikt.

Deze toepassing maakt gebruik van de OPC Foundation OPC UA-referentiestack als NuGet-pakketten. Zie [https://opcfoundation.org/license/redistributables/1.3/](https://opcfoundation.org/license/redistributables/1.3/) voor de licentievoorwaarden.

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd wat OPC Publisher is, is de voorgestelde volgende stap om het volgende te leren:

[OPC Publisher configureren](howto-opc-publisher-configure.md)