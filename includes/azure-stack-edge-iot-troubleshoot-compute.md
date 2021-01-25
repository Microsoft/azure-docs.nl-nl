---
author: v-dalc
ms.service: databox
ms.author: alkohli
ms.topic: include
ms.date: 01/21/2021
ms.openlocfilehash: 3defa62c55bb5ab042ade816f611ea45b39a0117
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/25/2021
ms.locfileid: "98761549"
---
Gebruik de IoT Edge runtime-antwoorden van agent om problemen met betrekking tot berekeningen op te lossen. Hier volgt een lijst met mogelijke reacties:

* 200-OK
* 400-de implementatie configuratie is onjuist of ongeldig.
* 417-er is geen implementatie configuratie ingesteld op het apparaat.
* 412-de schema versie in de implementatie configuratie is ongeldig.
* 406-het IoT Edge apparaat is offline of stuurt geen status rapporten.
* 500-er is een fout opgetreden in de IoT Edge-runtime.

Zie [IOT Edge-agent](/azure/iot-edge/iot-edge-runtime?view=iotedge-2018-06&preserve-view=true#iot-edge-agent)voor meer informatie.