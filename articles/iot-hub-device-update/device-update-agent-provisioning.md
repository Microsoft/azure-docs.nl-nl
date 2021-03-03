---
title: Update van het apparaat voor Azure IoT Hub agent inrichten | Microsoft Docs
description: Update van het apparaat voor Azure IoT Hub agent inrichten
author: ValOlson
ms.author: valls
ms.date: 2/16/2021
ms.topic: how-to
ms.service: iot-hub-device-update
ms.openlocfilehash: 79bac3f057412f3973121f48cd735f72d0a97d04
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101679230"
---
# <a name="device-update-agent"></a>Update-Agent van apparaat

Update van het apparaat voor IoT Hub ondersteunt twee soorten updates: op installatie kopie gebaseerd en op basis van een pakket. 

* Installatie kopieën bieden een hoger vertrouwens niveau in de eind toestand van het apparaat. Het is doorgaans eenvoudiger om de resultaten van een installatie kopie te repliceren: update tussen een pre-productie omgeving en een productie omgeving, omdat deze niet dezelfde uitdagingen als pakketten en hun afhankelijkheden vormt. Als gevolg van hun atoom karakter kan een model van een/B-failover ook eenvoudig worden aangenomen. 
* Updates op basis van pakketten zijn gerichte updates waarmee alleen een specifiek onderdeel of een specifieke toepassing op het apparaat wordt gewijzigd. Zo kunt u de band breedte verlagen en de update sneller downloaden en installeren. Pakket updates bieden doorgaans minder downtime van apparaten bij het Toep assen van een update en vermijden de overhead van het maken van installatie kopieën. 

Volg de onderstaande koppelingen om de Update-Agent van het apparaat te bouwen, uit te voeren en te wijzigen.

## <a name="build-the-device-update-agent"></a>De Update-Agent voor het apparaat maken

Volg de instructies om de Update Agent van het apparaat te [bouwen](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-build-agent-code.md) vanuit de bron.

## <a name="run-the-device-update-agent"></a>De Update-Agent voor het apparaat uitvoeren

Zodra de agent is gebouwd, wordt de agent [uitgevoerd](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-run-agent.md) .

## <a name="modifying-the-device-update-agent"></a>De Update Agent van het apparaat wijzigen

Breng nu de wijzigingen aan die nodig zijn om de agent in uw installatie kopie op te nemen.  Lees hoe u de Update Agent voor het apparaat kunt [wijzigen](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-modify-the-agent-code.m) voor hulp.

### <a name="troubleshooting-guide"></a>Handleiding voor het oplossen van problemen

Als u problemen ondervindt, raadpleegt u de update voor het oplossen van problemen met IoT Hub voor [probleem oplossing](troubleshoot-device-update.md) om mogelijke problemen op te lossen en benodigde informatie te verzamelen die aan micro soft kan worden verstrekt.

## <a name="next-steps"></a>Volgende stappen

Gebruik onder vooraf gemaakte installatie kopieën en binaire bestanden voor een eenvoudige demonstratie van het bijwerken van het apparaat voor IoT Hub.  

[Update van de installatie kopie: aan de slag met Raspberry Pi 3 B + Reference yocto image](device-update-raspberry-pi.md)

[Update van de installatie kopie: aan de slag met Ubuntu (18,04 x64) Simulator referentie agent](device-update-simulator.md)

[Pakket update: aan de slag met de Ubuntu Server 18,04 x64-pakket agent](device-update-ubuntu-agent.md)

