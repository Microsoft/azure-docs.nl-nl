---
title: Architectuur van digitaal distributiecentrum voor IoT Central | Microsoft Docs
description: Een architectuur van een toepassingssjabloon voor een digitaal distributiecentrum voor IoT Central
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: conceptual
ms.date: 10/20/2019
ms.openlocfilehash: f5f1a9a51c4aa68c11f1843424186a727be94621
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/08/2021
ms.locfileid: "99834303"
---
# <a name="architecture-of-iot-central-digital-distribution-center-application-template"></a>Architectuur van toepassingssjabloon voor digitaal distributiecentrum voor IoT Central



Partners en klanten kunnen de app-sjabloon en de volgende richtlijnen gebruiken om end-to-end-oplossingen voor een **digitaal distributiecentrum** te ontwikkelen.

> [!div class="mx-imgBorder"]
> ![digitaal distributiecentrum](./media/concept-ddc-architecture/digital-distribution-center-architecture.png)

1. Set IoT-sensoren die telemetriegegevens naar een gatewayapparaat verzenden
2. Gatewayapparaten die telemetrie en geaggregeerde inzichten naar IoT Central verzenden
3. Gegevens worden gerouteerd naar de gewenste Azure-service voor bewerking
4. Azure-services zoals ASA of Azure Functions kunnen worden gebruikt om gegevensstromen te transformeren en te verzenden naar de gewenste opslagaccounts
5. Verwerkte gegevens worden opgeslagen in dynamische opslag voor bijna realtime acties of koude opslag voor aanvullende inzichtverbeteringen die zijn gebaseerd op ML of batchanalyse. 
6. Logic Apps kan worden gebruikt om verschillende zakelijke werkstromen in zakelijke toepassingen voor eindgebruikers uit te schakelen

## <a name="details"></a>Details
De volgende sectie bevat een overzicht van elk onderdeel van de conceptuele architectuur

## <a name="video-cameras"></a>Videocamera's 
Videocamera's zijn de primaire sensoren in dit digitaal verbonden ecosysteem op ondernemingsschaal. Door de vooruitgang in machine learning en kunstmatige intelligentie kunnen videobeelden worden getransformeerd tot gestructureerde gegevens en aan de rand worden verwerkt voordat ze worden verzonden naar de cloud. We kunnen IP-camera's gebruiken om beelden vast te leggen, ze op de camera te comprimeren en vervolgens de gecomprimeerde gegevens via edge-computing naar een video-analysepijplijn te verzenden, of GigE Vision-camera's gebruiken om beelden op de sensor vast te leggen en deze vervolgens rechtstreeks naar Azure IoT Edge te verzenden, waar ze vervolgens worden gecomprimeerd voordat ze worden verwerkt in de video-analysepijplijn. 

## <a name="azure-iot-edge-gateway"></a>Azure IoT Edge-gateway
De workloads van camera's-als-sensoren en edge worden lokaal beheerd door Azure IoT Edge en de camerastroom wordt verwerkt door de analysepijplijn. De pijplijn voor de verwerking van videoanalyses op Azure IoT Edge biedt veel voordelen, waaronder snellere reactietijden en een laag bandbreedteverbruik, wat resulteert in lage latentie voor snelle gegevensverwerking. Alleen de meest essentiële metagegevens, inzichten of acties worden naar de cloud verzonden voor verdere actie of onderzoek. 

## <a name="device-management-with-iot-central"></a>Apparaatbeheer met IoT Central 
Azure IoT Central is een platform voor het ontwikkelen van oplossingen waarmee de connectiviteit, configuratie en het beheer van IoT-apparaten en Azure IoT Edge-gateways worden vereenvoudigd. Het platform vermindert de belasting en de kosten van het beheer en bewerkingen en gerelateerde ontwikkelingen van IoT-apparaten aanzienlijk. Klanten en partners kunnen end-to-end-bedrijfsoplossingen bouwen om digitale feedbacklussen in distributiecentrums te realiseren.

## <a name="business-insights-and-actions-using-data-egress"></a>Zakelijke inzichten en acties met behulp van uitgaande gegevens 
Het IoT Central-platform biedt rijke uitbreidingsopties door middel van continue gegevensexport (CDE) en API's. Zakelijke inzichten die zijn gebaseerd op telemetriegegevensverwerking of onbewerkte telemetrie, worden doorgaans geëxporteerd naar een LOB-toepassing van uw voorkeur. Dit kan worden bereikt via webhook, Service Bus, Event Hub of Blob Storage om Machine Learning-modellen te bouwen, te trainen en te implementeren en inzichten verder te verrijken.

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over het implementeren van een [digitale distributiecentrumsjabloon](./tutorial-iot-central-digital-distribution-center.md)
* Meer informatie over [IoT Central-retailsjablonen](./overview-iot-central-retail.md)
* Zie [Overzicht van IoT Central](../core/overview-iot-central.md) voor meer informatie over IoT Central
