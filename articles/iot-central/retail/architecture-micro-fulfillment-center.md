---
title: Micro-uitvoeringscentrum van Azure IoT Central | Microsoft Docs
description: Meer informatie over het bouwen van een toepassing voor het micro-uitvoeringscentrum met behulp van onze toepassingssjabloon voor het micro-uitvoeringscentrum in IoT Central
author: avneet723
ms.author: avneets
ms.date: 10/13/2019
ms.topic: overview
ms.service: iot-central
ms.subservice: iot-central-retail
services: iot-central
manager: eliotgra
ms.openlocfilehash: 9a0d91677770e89f3e249fc699057332cccaa323
ms.sourcegitcommit: fa807e40d729bf066b9b81c76a0e8c5b1c03b536
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/11/2020
ms.locfileid: "97347469"
---
# <a name="micro-fulfillment-center-architecture"></a>Architectuur voor het micro-uitvoeringscentrum

Met de oplossingen van het micro-uitvoeringscentrum kunt u digitaal verbinding maken en alle aspecten van een volledige geautomatiseerd uitvoeringscentrum bewaken en beheren om door middel van minder uitval kosten te besparen, en tegelijkertijd de beveiliging en algemene efficiëntie te verbeteren. Deze oplossingen kunnen worden gemaakt met behulp van een van de toepassingssjablonen in IoT Central en de onderstaande architectuur als leidraad.

![Azure IoT Central-winkelanalyses](./media/architecture/micro-fulfillment-center-architecture-frame.png)

1. Set IoT-sensoren die telemetriegegevens naar een gatewayapparaat verzenden
2. Gatewayapparaten die telemetrie en geaggregeerde inzichten naar IoT Central verzenden
3. Continue gegevensexport naar de gewenste Azure-service voor bewerking
4. Gegevens kunnen worden gestructureerd in de gewenste indeling en naar een opslagservice worden verzonden
5. Bedrijfstoepassingen kunnen gegevens opvragen en inzichten genereren voor het aansturen van retailbewerkingen
 
Laten we eens kijken naar de belangrijkste onderdelen die in het algemeen een rol spelen in een oplossing voor het micro-uitvoeringscentrum.

## <a name="robotic-carriers"></a>Geautomatiseerde orderpickers

Een oplossing voor het micro-uitvoeringscentrum heeft waarschijnlijk een grote set automatische orderpickers die verschillende soorten telemetriesignalen genereren. Deze signalen kunnen worden opgenomen door een gatewayapparaat, worden geaggregeerd en vervolgens naar IoT Central worden verzonden, zoals wordt weergegeven aan de linkerkant van het architectuurdiagram.  

## <a name="condition-monitoring-sensors"></a>Sensoren voor het bewaken van omstandigheden

Een IoT-oplossing begint met een set sensoren die zinvolle signalen vanuit uw uitvoeringscentrum vastleggen. Dit wordt weergegeven door verschillende sensoren helemaal links in het bovenstaande architectuurdiagram.

## <a name="gateway-devices"></a>Gatewayapparaten

Veel IoT-sensoren kunnen onbewerkte signalen rechtstreeks verzenden naar de cloud of naar een gatewayapparaat dat zich in de buurt bevindt. Met het gatewayapparaat wordt gegevensaggregatie aan de rand uitgevoerd voordat samenvattingsinzichten naar een IoT Central-toepassing worden verzonden. De gatewayapparaten zijn ook verantwoordelijk voor het doorsturen van opdracht- en controlebewerkingen naar de sensorapparaten, indien van toepassing. 

## <a name="iot-central-application"></a>IoT Central-toepassing

De Azure IoT Central-toepassing neemt gegevens op van verschillende soorten IoT-sensoren, robots en gatewayapparaten in de uitvoeringscentrumomgeving en genereert een set zinvolle inzichten.

Azure IoT Central biedt ook een aangepaste ervaring voor winkeloperators, zodat zij de infrastructuurapparaten op afstand kunnen bewaken en beheren.

## <a name="data-transform"></a>Gegevenstransformatie
De Azure IoT Central-toepassing in een oplossing kan worden geconfigureerd voor het exporteren van onbewerkte of geaggregeerde inzichten naar een set Azure PaaS-services (Platform-as-a-Service) waarmee gegevens kunnen worden bewerkt en deze inzichten kunnen worden verrijkt voordat ze in een bedrijfstoepassing worden opgenomen. 

## <a name="business-application"></a>Zakelijke toepassing
De IoT-gegevens kunnen worden gebruikt om verschillende soorten zakelijke toepassingen binnen een retailomgeving aan te sturen. Een manager of werknemer van een uitvoeringscentrum kan deze toepassingen gebruiken om zakelijke inzichten te visualiseren en in realtime zinvolle acties uit te voeren. Als u wilt weten hoe u een real-time Power BI-dashboard voor uw retailteam kunt maken, volgt u de [zelfstudie](./tutorial-in-store-analytics-create-app.md).

## <a name="next-steps"></a>Volgende stappen
* Aan de slag met de toepassingssjabloon [Micro-uitvoeringscentrum](https://aka.ms/checkouttemplate). 
* Bekijk de [zelfstudie](https://aka.ms/mfc-tutorial) die u begeleidt bij het bouwen van een oplossing met behulp van de app-sjabloon Micro-uitvoeringscentrum.
