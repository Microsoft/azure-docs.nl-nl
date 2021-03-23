---
title: Azure-industrieel IoT-platform
description: Dit artikel bevat een overzicht van het industriële IoT-platform en de bijbehorende onderdelen.
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: conceptual
ms.date: 3/22/2021
ms.openlocfilehash: 24932b17c630190cb3121d5310a865758fa6a920
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104801550"
---
# <a name="what-is-the-industrial-iot-iiot-platform"></a>Wat is het industrieel IoT-platform (IIoT)?

Het Azure Industrial IoT-platform is een micro soft-Suite met modules en services die zijn geïmplementeerd in Azure. Deze modules en services hebben volledige openheid. We passen met name de PaaS-aanbieding (Managed platform as a Service) van Azure toe, open-source software waarvoor een licentie is verleend via een MIT-licentie, open International Standards for Communication (OPC UA, AMQP, MQTT, HTTP) en interfaces (open API) en open industriële gegevens modellen (OPC UA) op de rand en in de Cloud.

## <a name="enabling-shopfloor-connectivity"></a>Shopfloor-connectiviteit inschakelen 

Het Azure Industrial IoT-platform gaat over industriële connectiviteit van shopfloor-assets (inclusief de detectie van voor OPC UA-ingeschakelde activa), normaliseert hun gegevens in OPC UA-indeling en verzendt Asset-telemetriegegevens naar Azure in OPC UA PubSub-indeling. Daar worden de telemetriegegevens in een Cloud database opgeslagen. Daarnaast biedt het platform beveiligde toegang tot de shopfloor-assets via OPC UA vanuit de Cloud. Beheer mogelijkheden voor apparaten (inclusief de beveiligings configuratie) zijn ook ingebouwd. De OPC UA-functionaliteit is gemaakt met behulp van docker-container technologie voor eenvoudige implementatie en beheer. Voor niet-OPC UA-ingeschakelde assets hebben we een partnerschap aangegaan met de toonaangevende industrie-connectiviteits providers en hebben ze hun stuur programma voor de OPC UA-adapter op de Azure IoT Edge poort. Deze adapters zijn beschikbaar op de Azure Marketplace.

## <a name="industrial-iot-components-iot-edge-modules-and-cloud-microservices"></a>Industriële IoT-onderdelen: IoT Edge modules en Cloud micro Services

De Edge-services worden geïmplementeerd als Azure IoT Edge modules en on-premises worden uitgevoerd. De Cloud micro services worden geïmplementeerd als ASP.NET Micro Services met een REST-interface en worden uitgevoerd op beheerde Azure Kubernetes-Services of zelfstandig op Azure App Service. Voor zowel Edge-als Cloud Services hebben we vooraf ontwikkelde docker-containers in de micro soft Container Registry (MCR). deze stap wordt verwijderd voor de klant. De Edge-en Cloud Services maken gebruik van elkaar en moeten samen worden gebruikt. We hebben ook gebruiks vriendelijke implementatie scripts verschaft waarmee het hele platform kan worden geïmplementeerd met één opdracht.

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd wat het Azure-industriële IoT-platform is, kunt u meer te weten komen over de OPC-Uitgever:

> [!div class="nextstepaction"]
> [Wat is de OPC-Uitgever?](overview-what-is-opc-publisher.md)