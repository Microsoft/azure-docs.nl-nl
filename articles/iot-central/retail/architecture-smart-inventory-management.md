---
title: Architectuur van IoT Smart Inventory Management | Microsoft Docs
description: Een architectuur van een IoT Smart Inventory Management-sjabloon voor IoT Central
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: conceptual
ms.date: 10/20/2019
ms.custom: mqtt
ms.openlocfilehash: d7939193b0dad1ed74dccd0ace0b0f0323b17124
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99832637"
---
# <a name="architecture-of-iot-central-smart-inventory-management-application-template"></a>Architectuur van een IoT Smart Inventory Management-toepassingssjabloon voor IoT Central

Partners en klanten kunnen de app-sjabloon en de volgende richtlijnen gebruiken om end-to-end-oplossingen voor **slim voorraadbeheer** te ontwikkelen.

> [!div class="mx-imgBorder"]
> ![Slim voorraadbeheer](./media/concept-smart-inventory-mgmt-architecture/smart-inventory-management-architecture.png)

1. Set IoT-sensoren die telemetriegegevens naar een gatewayapparaat verzenden
2. Gatewayapparaten die telemetrie en geaggregeerde inzichten naar IoT Central verzenden
3. Gegevens worden gerouteerd naar de gewenste Azure-service voor bewerking
4. Azure-services zoals ASA of Azure Functions kunnen worden gebruikt om gegevensstromen te transformeren en te verzenden naar de gewenste opslagaccounts 
5. Verwerkte gegevens worden opgeslagen in dynamische opslag voor bijna realtime acties of koude opslag voor aanvullende inzichtverbeteringen die zijn gebaseerd op ML of batchanalyse. 
6. Logic Apps kan worden gebruikt om verschillende zakelijke werkstromen in zakelijke toepassingen voor eindgebruikers uit te schakelen

## <a name="details"></a>Details
De volgende sectie biedt een overzicht van elk deel van de conceptuele architectuur van telemetrieopname van radiofrequentie-identificatie (RFID), Bluetooth Low Energy (BLE)-tags

## <a name="rfid-tags"></a>RFID-tags
RFID-tags verzenden gegevens over een item via radiogolven. RFID-tags hebben doorgaans geen batterij, tenzij dit nadrukkelijk wordt vermeld. De tags worden gevoed door de radiogolven die worden gegenereerd door de lezer en sturen een signaal naar de RFID-lezer.

## <a name="ble-tags"></a>BLE-tags
De Bluetooth-baken verzendt regelmatig pakketten met gegevens. De gegevens van de Bluetooth-baken worden gedetecteerd door de BLE-lezers of op smartphones geïnstalleerde services en worden vervolgens naar de Cloud verzonden.

## <a name="rfid--ble-readers"></a>RFID- en BLE-lezers
Een RFID-lezer converteert radiogolven naar een bruikbaarder gegevensformaat. De gegevens die worden verzameld uit de tags worden vervolgens opgeslagen in de lokale Edge-server of naar de Cloud verzonden met behulp van JSON-RPC 2.0 via MQTT.
Een BLE-lezer, ook wel Access points (AP), is vergelijkbaar met een RFID-lezer. Het wordt gebruikt om Bluetooth-signalen in de buurt te detecteren en het bericht door te sturen naar een lokale Azure IoT Edge- of Cloud-server met behulp van JSON-RPC 2.0 via MQTT.
De meeste lezers kunnen RFID- en Bluetooth-baken-signalen ontvangen en bieden vaak de mogelijkheid voor het gebruik van extra sensors voor temperatuur, vochtigheid, snelheid en gyroscoop.

## <a name="azure-iot-edge-gateway"></a>Azure IoT Edge-gateway
De Azure IoT Edge-server biedt een plek om die gegevens vooraf lokaal te verwerken voordat deze naar de Cloud worden verzonden. We kunnen ook standaardcontainers gebruiken om kunstmatige intelligentie te implementeren voor Cloud-workloads, Azure en services van derden, en bedrijfslogica.

## <a name="device-management-with-iot-central"></a>Apparaatbeheer met IoT Central 
Azure IoT Central is een platform voor het ontwikkelen van oplossingen waarmee de connectiviteit, configuratie en het beheer van IoT-apparaten wordt vereenvoudigd. Het platform vermindert de belasting en de kosten van het beheer en bewerkingen en gerelateerde ontwikkelingen van IoT-apparaten aanzienlijk. Klanten en partners kunnen end-to-end-bedrijfsoplossingen bouwen om digitale feedbacklussen voor voorraadbeheer te realiseren.

## <a name="business-insights--actions-using-data-egress"></a>Zakelijke inzichten en acties met behulp van uitgaande gegevens 
Het IoT Central-platform biedt rijke uitbreidingsopties door middel van continue gegevensexport (CDE) en API's. Zakelijke inzichten gebaseerd op telemetriegegevensverwerking of onbewerkte telemetrie, worden doorgaans geëxporteerd naar een LOB-toepassing van uw voorkeur. Dit kan worden bereikt met webhook, Service Bus, Event Hub of Blob Storage om Machine Learning-modellen te bouwen, te trainen en te implementeren en inzichten verder te verrijken.

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over het implementeren van een [sjabloon voor slim voorraadbeheer](./tutorial-iot-central-smart-inventory-management.md)
* Meer informatie over [IoT Central-retailsjablonen](./overview-iot-central-retail.md)
* Zie [Overzicht van IoT Central](../core/overview-iot-central.md) voor meer informatie over IoT Central
