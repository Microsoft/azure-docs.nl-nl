---
title: Microsoft Verbonden cache binnen een Azure IoT Edge voor industriële IoT-configuratie | Microsoft Docs
description: Zelfstudie Verbonden cache Microsoft Azure IoT Edge voor industriële IoT-configuratie
author: andyriv
ms.author: andyriv
ms.date: 2/16/2021
ms.topic: tutorial
ms.service: iot-hub-device-update
ms.openlocfilehash: 083c7bf6edc7da1fd617487e91b0a3848fb401fe
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107811808"
---
# <a name="microsoft-connected-cache-preview-deployment-scenario-sample-microsoft-connected-cache-within-an-azure-iot-edge-for-industrial-iot-configuration"></a>Voorbeeld van Verbonden cache preview-implementatie van Microsoft: Microsoft Verbonden cache in een Azure IoT Edge voor industriële IoT-configuratie

Productienetwerken zijn vaak ingedeeld in hiërarchische lagen volgens het [Purdue-netwerkmodel](https://en.wikipedia.org/wiki/Purdue_Enterprise_Reference_Architecture) (opgenomen in de [ISA 95-](https://en.wikipedia.org/wiki/ANSI/ISA-95) en [ISA 99-standaarden).](https://www.isa.org/standards-and-publications/isa-standards/isa-standards-committees/isa99) In deze netwerken heeft alleen de bovenste laag verbinding met de cloud en kunnen de lagere lagen in de hiërarchie alleen communiceren met aangrenzende noord- en zuidlagen.

In dit [GitHub-voorbeeld, Azure IoT Edge voor Industrial IoT,](https://github.com/Azure-Samples/iot-edge-for-iiot)wordt het volgende geïmplementeerd:

* Gesimuleerd Purdue-netwerk in Azure
* Industriële activa 
* Hiërarchie Azure IoT Edge gateways
  
Deze onderdelen worden gebruikt om industriële gegevens te verkrijgen en veilig te uploaden naar de cloud zonder de beveiliging van het netwerk in gevaar te brengen. Microsoft Verbonden cache kunnen worden geïmplementeerd ter ondersteuning van het downloaden van inhoud op alle niveaus binnen het ISA 95-compatibele netwerk.

De sleutel voor het configureren van Microsoft Verbonden cache-implementaties binnen een ISA 95-compatibel netwerk is het configureren van zowel de *OT-proxy* als de upstream-host op de L3 IoT Edge-gateway.

1. Configureer Microsoft Verbonden cache-implementaties op het niveau L5 en L4, zoals beschreven in het voorbeeld Two-Level Geneste IoT Edge-gateway 
2. De implementatie op de IoT Edge L3-gateway moet het volgende opgeven:
   
   * UPSTREAM_HOST: de IP/FQDN van de L4 IoT Edge-gateway, die door de L3 Microsoft-Verbonden cache inhoud wordt opgevraagd.
   * UPSTREAM_PROXY: de IP/FQDN:POORT van de OT-proxyserver.

3. De OT-proxy moet het L4 MCC FQDN/IP-adres toevoegen aan de allowlist.

Als u wilt controleren of Microsoft Verbonden cache goed werkt, voert u de volgende opdracht uit in de terminal van het IoT Edge-apparaat, waar de module of een apparaat in het netwerk wordt host. Vervang \<Azure IoT Edge Gateway IP\> door het IP-adres of de hostnaam van uw IoT Edge gateway. (Zie details van de omgevingsvariabele voor informatie over de zichtbaarheid van dit rapport).

```bash
    wget http://<L3 IoT Edge Gateway IP>/mscomtest/wuidt.gif?cacheHostOrigin=au.download.windowsupdate.com
```