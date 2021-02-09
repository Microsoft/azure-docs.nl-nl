---
title: Analysearchitectuur voor winkels
description: Een analysetoepassing voor een winkel maken met de toepassingssjabloon voor betaling in IoT Central
author: avneets
ms.author: avneets
ms.date: 10/13/2019
ms.topic: conceptual
ms.service: iot-central
ms.subservice: iot-central-retail
services: iot-central
manager: eliotgra
ms.openlocfilehash: 972ab8eaaf7c0b9fe8beb446d74bc8d2e89bbc55
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/08/2021
ms.locfileid: "99832195"
---
# <a name="in-store-analytics-architecture"></a>Analysearchitectuur voor winkels


Met analyseoplossingen voor winkels kunt u verschillende omstandigheden binnen de retailomgeving monitoren. Deze oplossingen kunnen worden gemaakt met behulp van een van de toepassingssjablonen in IoT Central en de onderstaande architectuur als leidraad.


![Azure IoT Central-winkelanalyses](./media/architecture/store-analytics-architecture-frame.png)

- Set IoT-sensoren die telemetriegegevens naar een gatewayapparaat verzenden
- Gatewayapparaten die telemetrie en geaggregeerde inzichten naar IoT Central verzenden
- Continue gegevensexport naar de gewenste Azure-service voor bewerking
- Gegevens kunnen worden gestructureerd in de gewenste indeling en naar een opslagservice worden verzonden
- Zakelijke toepassingen kunnen gegevens opvragen en inzichten genereren voor het aansturen van retailbewerkingen
 
Laten we eens kijken naar de belangrijkste onderdelen die in het algemeen een rol spelen in een oplossing voor winkelanalyses.

## <a name="condition-monitoring-sensors"></a>Sensoren voor het bewaken van omstandigheden

Een IoT-oplossing begint met een set sensoren die zinvolle signalen vanuit uw retailomgeving vastleggen. Dit wordt weergegeven door verschillende sensoren helemaal links in het bovenstaande architectuurdiagram.

## <a name="gateway-devices"></a>Gatewayapparaten

Veel IoT-sensoren kunnen onbewerkte signalen rechtstreeks verzenden naar de cloud of naar een gatewayapparaat dat zich in de buurt bevindt. Met het gatewayapparaat wordt gegevensaggregatie aan de rand uitgevoerd voordat samenvattingsinzichten naar een IoT Central-toepassing worden verzonden. De gatewayapparaten zijn ook verantwoordelijk voor het doorsturen van opdracht- en controlebewerkingen naar de sensorapparaten, indien van toepassing. 

## <a name="iot-central-application"></a>IoT Central-toepassing

De Azure IoT Central-toepassing neemt gegevens op van verschillende soorten IoT-sensoren en gatewayapparaten in de retailomgeving en genereert een set zinvolle inzichten.

Azure IoT Central biedt ook een aangepaste ervaring voor winkeloperators, zodat zij de infrastructuurapparaten op afstand kunnen bewaken en beheren.

## <a name="data-transform"></a>Gegevenstransformatie
De Azure IoT Central-toepassing in een oplossing kan worden geconfigureerd voor het exporteren van onbewerkte of geaggregeerde inzichten naar een set Azure PaaS-services (Platform-as-a-Service) waarmee gegevens kunnen worden bewerkt. Deze inzichten kunnen worden verrijkt voordat ze in een zakelijke toepassing worden opgenomen. 

## <a name="business-application"></a>Zakelijke toepassing
De IoT-gegevens kunnen worden gebruikt om verschillende soorten zakelijke toepassingen binnen een retailomgeving aan te sturen. Een manager of werknemer van een winkel kan deze toepassingen gebruiken om zakelijke inzichten te visualiseren en in real time zinvolle acties uit te voeren. Als u wilt weten hoe u een realtime Power BI-dashboard voor uw retailteam kunt maken, volgt u de [zelfstudie](./tutorial-in-store-analytics-create-app.md).

## <a name="next-steps"></a>Volgende stappen
* Ga aan de slag met de toepassingssjablonen voor [winkelanalyse voor betaling](https://aka.ms/checkouttemplate) en [winkelanalyse voor monitoring van omstandigheden](https://aka.ms/conditiontemplate). 
* Bekijk de [complete zelfstudie](https://aka.ms/storeanalytics-tutorial) waarin u een oplossing gaat maken met een van de toepassingssjablonen voor winkelanalyse.
