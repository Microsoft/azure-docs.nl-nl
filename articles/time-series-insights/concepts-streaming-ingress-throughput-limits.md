---
title: Doorvoer beperkingen voor streaming-opname-Azure Time Series Insights Gen2 | Microsoft Docs
description: Meer informatie over de doorvoer limieten voor inkomend verkeer in Azure Time Series Insights Gen2.
author: lyrana
ms.author: lyhughes
manager: dpalled
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 01/21/2021
ms.custom: seodec18
ms.openlocfilehash: 72b78f49ed8b7016b92262de7256492fa6d5d7d1
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98676798"
---
# <a name="streaming-ingestion-throughput-limits"></a>Doorvoer limieten voor stroom inslikken

Azure Time Series Insights beperkingen voor het inkomen van Gen2 streaming-gegevens worden hieronder beschreven.

> [!TIP]
> Lees [de Gen2-omgeving van uw Azure time series Insights](./how-to-plan-your-environment.md#review-azure-time-series-insights-gen2-limits) voor een uitgebreide lijst met alle limieten.

## <a name="per-environment-limitations"></a>Beperkingen per omgeving

Over het algemeen worden de ingangs snelheden weer gegeven als de factor van het aantal apparaten in uw organisatie, de gebeurtenis emissie frequentie en de grootte van elke gebeurtenis:

* **Aantal apparaten** × **gebeurtenis emissie frequentie** × **grootte van elke gebeurtenis**.

Standaard kan Azure Time Series Insights Gen2 binnenkomende gegevens opnemen met een snelheid van **Maxi maal 1 MB per seconde (Mbps) per Azure time series Insights Gen2-omgeving**. Er zijn extra beperkingen [per hub-partitie](./concepts-streaming-ingress-throughput-limits.md#hub-partitions-and-per-partition-limits).

> [!TIP]
>
> * Omgevings ondersteuning voor opname snelheden van Maxi maal 2 MBps kan worden geboden door een aanvraag.
> * Neem contact met ons op als u een hogere door voer nodig hebt door een ondersteunings ticket in te dienen via de Azure Portal.

* **Voor beeld 1:**

    Contoso-verzen ding heeft 100.000 apparaten die drie keer per minuut een gebeurtenis verzenden. De grootte van een gebeurtenis is 200 bytes. Ze gebruiken een IoT Hub met vier partities als de bron van de Azure Time Series Insights Gen2-gebeurtenis.

  * De opname frequentie voor hun Azure Time Series Insights Gen2-omgeving zou zijn: **100.000 apparaten * 200 bytes/gebeurtenis * (3/60 gebeurtenis/sec) = 1 Mbps**.
    * Uitgebalanceerde partities, het opname tempo per partitie is 0,25 MBps.
    * De opname frequentie van Contoso-verzen ding zou binnen de schaal beperkingen vallen.

* **Voor beeld 2:**

    Contoso vloot Analytics heeft 10.000 apparaten die elke seconde een gebeurtenis verzenden. Ze gebruiken een event hub met een aantal partities van 2 als de Azure Time Series Insights gebeurtenis bron Gen2. De grootte van een gebeurtenis is 200 bytes.

  * Het opname tempo van de omgeving zou zijn: **10.000-apparaten * 200 bytes/gebeurtenis * 1 gebeurtenis/SEC = 2 Mbps**.
    * Uitgebalanceerde partities, de frequentie per partitie is 1 MBps.
    * De opname frequentie van Contoso vloot Analytics ligt boven de omgeving en de partitie limieten. Ze kunnen een aanvraag indienen bij Azure Time Series Insights Gen2 via de Azure Portal om de opname frequentie voor hun omgeving te verhogen en een event hub te maken met meer partities die binnen de grenzen vallen.

## <a name="hub-partitions-and-per-partition-limits"></a>Hub-partities en limieten per partitie

Bij het plannen van uw Azure Time Series Insights Gen2-omgeving is het belang rijk dat u rekening houdt met de configuratie van de bron (nen) van de gebeurtenis die u wilt verbinden met Azure Time Series Insights Gen2. Zowel Azure IoT Hub als Event Hubs gebruiken partities om een horizontale schaal in te scha kelen voor gebeurtenis verwerking.

Een *partitie* is een geordende reeks gebeurtenissen die in een hub worden bewaard. Het aantal partities wordt ingesteld tijdens het maken van de hub en kan niet worden gewijzigd.

Bekijk voor Event Hubs aanbevolen procedures voor het partitioneren van [het aantal partities dat ik nodig heb?](../event-hubs/event-hubs-faq.md#how-many-partitions-do-i-need)

> [!NOTE]
> De meeste IoT-hubs die worden gebruikt met Azure Time Series Insights Gen2 hebben slechts vier partities nodig.

Of u nu een nieuwe hub voor uw Azure Time Series Insights Gen2-omgeving maakt of een bestaand gebruikt, u moet de opname frequentie per partitie berekenen om te bepalen of deze binnen de limieten valt.

Azure Time Series Insights Gen2 heeft momenteel een **limiet van 0,5 Mbps voor algemeen per partitie**.

### <a name="iot-hub-specific-considerations"></a>IoT Hub-specifieke overwegingen

Wanneer een apparaat wordt gemaakt in IoT Hub, wordt het permanent toegewezen aan een partitie. In dat geval kan IoT Hub de volg orde van gebeurtenissen garanderen (omdat de toewijzing nooit wordt gewijzigd).

Een vaste partitie toewijzing heeft ook invloed op Azure Time Series Insights Gen2-instanties die gegevens opnemen die vanuit IoT Hub downstream worden verzonden. Wanneer berichten van meerdere apparaten worden doorgestuurd naar de hub met behulp van dezelfde gateway apparaat-ID, kunnen ze in dezelfde partitie worden ontvangen op hetzelfde moment mogelijk de schaal limieten per partitie overschrijden.

**Impact**:

* Als een enkele partitie een aanhoudende belasting frequentie heeft voor de limiet, is het mogelijk dat Azure Time Series Insights Gen2 niet alle telemetrie van het apparaat synchroniseert voordat de IoT Hub gegevens Bewaar periode is overschreden. Als gevolg hiervan kunnen verzonden gegevens verloren gaan als de opname limieten consistent zijn overschreden.

Om dat te beperken, raden we u aan de volgende aanbevolen procedures uit te voeren:

* Bereken uw per omgeving en de opname snelheid per partitie voordat u de oplossing implementeert.
* Zorg ervoor dat uw IoT Hub-apparaten zoveel mogelijk gelijkmatig zijn verdeeld.

> [!IMPORTANT]
> Voor omgevingen die IoT Hub als gebeurtenis bron gebruiken, berekent u de opname snelheid met het aantal Hub-apparaten dat wordt gebruikt om ervoor te zorgen dat de snelheid lager is dan de limiet van 0,5 MBps per partitie.
>
> * Zelfs als er meerdere gebeurtenissen tegelijkertijd binnenkomen, wordt de limiet niet overschreden.

  ![IoT Hub partitie diagram](media/concepts-ingress-overview/iot-hub-partiton-diagram.png)

Raadpleeg de volgende bronnen voor meer informatie over het optimaliseren van hub-door Voer en-partities:

* [IoT Hub schaal](../iot-hub/iot-hub-scaling.md)
* [Event hub-schaal](../event-hubs/event-hubs-scalability.md#throughput-units)
* [Event hub-partities](../event-hubs/event-hubs-features.md#partitions)

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over gegevens [opslag](./concepts-storage.md)
