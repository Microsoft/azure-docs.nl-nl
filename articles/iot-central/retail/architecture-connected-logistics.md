---
title: Architectuur van verbonden IoT-logistiek | Microsoft Docs
description: Een architectuur van een toepassingssjabloon voor verbonden IoT-logistiek voor IoT Central
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: conceptual
ms.date: 10/20/2019
ms.custom: mqtt
ms.openlocfilehash: d1e17dce80c313bf726451c36ec06dd393549600
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99832416"
---
# <a name="architecture-of-iot-central-connected-logistics-application-template"></a>Architectuur van een toepassingssjabloon voor verbonden logistiek voor IoT Central



Partners en klanten kunnen de toepassingssjabloon en de volgende richtlijnen gebruiken om end-to-end-oplossingen voor **verbonden logistiek** te ontwikkelen.

> [!div class="mx-imgBorder"]
> ![dashboard voor verbonden logistiek](./media/concept-connected-logistics-architecture/connected-logistics-architecture.png)

1. Set IoT-tags die telemetriegegevens naar een gatewayapparaat verzenden
2. Gatewayapparaten die telemetrie en geaggregeerde inzichten naar IoT Central verzenden
3. Gegevens worden gerouteerd naar de gewenste Azure-service voor bewerking
4. Azure-services zoals ASA of Azure Functions kunnen worden gebruikt om gegevensstromen te transformeren en te verzenden naar de gewenste opslagaccounts
5. Zakelijke toepassingen voor eindgebruikers kunnen worden gebruikt in verschillende zakelijke werkstromen

## <a name="details"></a>Details
In het volgende gedeelte worden de verschillende onderdelen van de conceptuele architectuur voor opname van telemetriegegevens van IoT-tags en gateways beschreven.

## <a name="iot-tags"></a>IoT-Tags
IoT-tags bieden sensormogelijkheden voor fysieke, lucht- en omgevingsomstandigheden zoals temperatuur, luchtvochtigheid, trillingen, kantelhoek en licht. IoT-tags maken doorgaans via Zigbee (802.15.4) verbinding met een gateway-apparaat. Tags zijn een goedkoper alternatief voor sensoren. U kunt ze aan het eind van een logistiektraject wegdoen om uitdagingen op het gebied van omgekeerde logistiek te vermijden.

## <a name="gateway"></a>Gateway
Gateways kunnen ook fungeren als IoT-tags omdat ze over mogelijkheden voor omgevingsdetectie beschikken. De gateway maakt upstream connectiviteit met de Azure IoT-cloud (MQTT) mogelijk via mobiele en wifi-kanalen.  De modi voor Bluetooth, NFC en draadloze sensornetwerken op basis van 802.15.4 worden gebruikt voor downstream communicatie met IoT-tags. Gateways bieden volledig beveiligde cloudconnectiviteit, koppeling van IoT-tags, aggregatie van sensorgegevens, gegevensretentie en de mogelijkheid om waarschuwingsdrempels te configureren.

## <a name="device-management-with-iot-central"></a>Apparaatbeheer met IoT Central 
Azure IoT Central is een platform voor het ontwikkelen van oplossingen waarmee de connectiviteit, configuratie en het beheer van IoT-apparaten wordt vereenvoudigd. Het platform vermindert de lasten en de kosten van het beheren en werken met IoT-apparaten en daaraan gerelateerde ontwikkelingen aanzienlijk. Klanten en partners kunnen end-to-end-bedrijfsoplossingen ontwikkelen om digitale feedbacklussen voor logistiek te realiseren.

## <a name="business-insights-and-actions-using-data-egress"></a>Zakelijke inzichten en acties met behulp van uitgaande gegevens 
Het IoT Central-platform biedt veel uitbreidingsopties door middel van continue gegevensexport en API's. Zakelijke inzichten gebaseerd op telemetriegegevensverwerking of onbewerkte telemetrie, worden doorgaans geëxporteerd naar een LOB-toepassing van uw voorkeur. Dit kan worden bereikt met webhook, Service Bus, Event Hub of Blob Storage om Machine Learning-modellen te bouwen, te trainen en te implementeren en inzichten verder te verrijken.

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over hoe u een [template voor verbonden logistiek](./tutorial-iot-central-connected-logistics.md) kunt implementeren
* Meer informatie over [IoT Central-retailsjablonen](./overview-iot-central-retail.md)
* Zie [Overzicht van IoT Central](../core/overview-iot-central.md) voor meer informatie over IoT Central
