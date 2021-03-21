---
title: Micro soft Connected cache in een Azure IoT Edge voor de industriële IoT-configuratie | Microsoft Docs
description: Micro soft Connected cache in een Azure IoT Edge voor de zelf studie over industriële IoT-configuratie
author: andyriv
ms.author: andyriv
ms.date: 2/16/2021
ms.topic: tutorial
ms.service: iot-hub-device-update
ms.openlocfilehash: 0d70ed8b906c171c001c5bda81a79ca9b65febac
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "101664631"
---
# <a name="microsoft-connected-cache-preview-deployment-scenario-sample-microsoft-connected-cache-within-an-azure-iot-edge-for-industrial-iot-configuration"></a>Voor beeld van micro soft Connected cache preview Deployment scenario: micro soft Connected cache in een Azure IoT Edge voor de industriële IoT-configuratie

Productie netwerken zijn vaak ingedeeld in hiërarchische lagen volgens het [Purdue-netwerk model](https://en.wikipedia.org/wiki/Purdue_Enterprise_Reference_Architecture) (opgenomen in de [ISA 95](https://en.wikipedia.org/wiki/ANSI/ISA-95) -en [ISA 99](https://www.isa.org/standards-and-publications/isa-standards/isa-standards-committees/isa99) -standaarden). In deze netwerken is alleen de toplaag verbonden met de Cloud en kunnen de lagere lagen in de hiërarchie alleen communiceren met aangrenzende Noord-en Zuid-lagen.

Dit GitHub-voor beeld, [Azure IOT Edge voor industriële IOT](https://github.com/Azure-Samples/iot-edge-for-iiot), implementeert het volgende:

* Gesimuleerd Purdue-netwerk in azure
* Industriële activa 
* Hiërarchie van Azure IoT Edge gateways
  
Deze onderdelen worden gebruikt om industriële gegevens te verkrijgen en deze veilig te uploaden naar de Cloud zonder de beveiliging van het netwerk in gevaar te brengen. Micro soft Connected cache kan worden geïmplementeerd ter ondersteuning van het downloaden van inhoud op alle niveaus binnen het ISA 95-compatibele netwerk.

De sleutel voor het configureren van implementaties van met micro soft verbonden caches in een ISA 95-compatibel netwerk is het configureren van zowel de proxy *als* de upstream-host op de L3-IOT Edge gateway.

1. Configureer implementaties van met micro soft verbonden caches op de N5-en N4-niveaus, zoals beschreven in het Two-Level geneste IoT Edge gateway-voor beeld 
2. De implementatie op de L3-IoT Edge gateway moet de volgende opgeven:
   
   * UPSTREAM_HOST-de IP/FQDN van de N4-IoT Edge gateway, die door de L3 micro soft-verbonden cache wordt gevraagd om inhoud.
   * UPSTREAM_PROXY-de IP/FQDN: poort van de proxy server.

3. De niet-geotde proxy moet de N4 MCC FQDN/IP-adres toevoegen aan de acceptatie lijst.

Als u wilt controleren of micro soft Connected cache correct werkt, voert u de volgende opdracht uit in de terminal van het IoT Edge apparaat, de module te hosten of een apparaat in het netwerk.

```bash
    wget "http://<L3 IoT Edge Gateway IP>/mscomtest/wuidt.gif?cacheHostOrigin=au.download.windowsupdate.com
```