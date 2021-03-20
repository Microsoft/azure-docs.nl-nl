---
title: Overzicht van de typen Azure IoT-apparaten
description: Meer informatie over de verschillende apparaattypen die worden ondersteund door Azure IoT en de hulpprogram ma's die beschikbaar zijn.
author: ryanwinter
ms.author: rywinter
ms.service: iot-develop
ms.topic: conceptual
ms.date: 01/11/2021
ms.openlocfilehash: aa99594fe3de98635e37d15beebf015f15dc4f64
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100654157"
---
# <a name="overview-of-azure-iot-device-types"></a>Overzicht van de typen Azure IoT-apparaten
IoT-apparaten bestaan in een brede selectie van hardwareplatforms. Er zijn kleine 8-bits Sphere voor de meest recente x86-Cpu's, zoals gevonden op een desktop computer. Veel variabelen factor in de beslissing waarvoor u een IoT-apparaat wilt kiezen en dit artikel heeft een aantal belang rijke verschillen beschreven.

## <a name="key-hardware-differentiators"></a>Verschillen voor Key hardware
Enkele belang rijke factoren bij het kiezen van uw hardware zijn kosten, energie verbruik, netwerken en beschik bare invoer en uitvoer.

* **Kosten:** Kleinere goedkopere apparaten worden doorgaans gebruikt bij het massaal produceren van het uiteindelijke product. De trans actie kan echter nog duurder zijn, gezien het zeer beperkte apparaat. De ontwikkelings kosten kunnen worden verdeeld over alle geproduceerde apparaten, zodat de ontwikkelings kosten per eenheid laag worden.

* **Energie:** Hoeveel energie een apparaat verbruikt, is belang rijk als het apparaat gebruikmaakt van accu's en niet is verbonden met het energie raster. Sphere zijn vaak ontworpen voor lagere energiebeheer scenario's en kunnen een betere keuze zijn voor het verlengen van de levens duur van de accu.

* **Netwerk toegang:** Er zijn veel manieren om een apparaat te verbinden met een Cloud service. Ethernet, Wi-Fi en mobiel en enkele van de beschik bare opties. Welk verbindings type u kiest, is afhankelijk van waar het apparaat wordt geïmplementeerd en hoe het wordt gebruikt. Zo kan mobiel bijvoorbeeld een aantrekkelijke optie zijn, op basis van de hoge dekking, maar voor apparaten met een hoog verkeer kan het erg kostbaar zijn. Hardwired Ethernet biedt goedkopere gegevens kosten, maar met het nadeel van minder draagbaar.

* **Invoer en uitvoer:** De invoer en uitvoer die op het apparaat beschikbaar zijn, zijn rechtstreeks van invloed op de besturings mogelijkheden van apparaten. Een micro controller heeft doorgaans veel I/O-functies die rechtstreeks in de chip zijn gebouwd en biedt een breed scala aan Sens oren om rechtstreeks verbinding te maken.

## <a name="microcontrollers-vs-microprocessors"></a>Micro controllers VS micro processors
IoT-apparaten kunnen worden onderverdeeld in twee algemene categorieën: micro controllers (Sphere) en micro processors (MPUs).

**Sphere** zijn goed koop en eenvoudiger te werk dan MPUs. Een MCU bevat veel van de functies, zoals geheugen, interfaces en I/O binnen de chip zelf. Met een MPU wordt deze functionaliteit van onderdelen in de ondersteuning van chips getekend. Een MCU maakt vaak gebruik van een realtime-besturings systeem (RTO'S) of het uitvoeren van Bare Metal (geen OS) en biedt realtime responsie en zeer deterministische reacties op externe gebeurtenissen.

**MPUs** voert doorgaans een besturings systeem voor algemene doel einden uit, zoals Windows, Linux of MacOSX, dat een niet-deterministisch real-time antwoord biedt. Er is doorgaans geen garantie wanneer een taak wordt voltooid. 

:::image type="content" border="false" source="media/concepts-iot-device-types/mcu-vs-mpu.png" alt-text="MCU VS MPU":::

Hieronder ziet u een tabel met een aantal van de verschillen tussen een MCU en een op MPU gebaseerd systeem:

||Micro controller (MCU)|Micro processor (MPU)|
|-|-|-|
|**CPU**| Less | Meer |
|**RAM**| Less | Meer |
|**Flits**| Less | Meer |
|**Besturingssysteem**| Nee of RTO'S | Algemeen gebruik |
|**Moeilijk te ontwikkelen**| Harde |  Simpele |
|**Energie verbruik**| Lager | Hoger |
|**Kosten**| Lager | Hoger |
|**Deterministische**| Ja | Nee-met uitzonde ringen|
|**Grootte van apparaat**| Formaat | Neemt |
