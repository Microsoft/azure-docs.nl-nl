---
title: Integratie met andere services
titleSuffix: Azure Digital Twins
description: Meer informatie over de ingangs-en uitgangs vereisten bij het implementeren van Azure Digital Apparaatdubbels.
author: baanders
ms.author: baanders
ms.date: 3/16/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 118b02ab694d27dbe4e13cbfa1a617a56b052772
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92043065"
---
# <a name="integrate-azure-digital-twins-with-other-services"></a>Azure Digital Apparaatdubbels integreren met andere services

Azure Digital Apparaatdubbels wordt doorgaans samen met andere services gebruikt om flexibele, verbonden oplossingen te maken die uw gegevens op verschillende manieren gebruiken.

Met behulp van [**gebeurtenis routes**](concepts-route-events.md)kan Azure Digital apparaatdubbels gegevens ontvangen van upstream-services zoals [IOT hub](../iot-hub/about-iot-hub.md) of [Logic apps](../logic-apps/logic-apps-overview.md), die worden gebruikt voor het leveren van telemetriegegevens en meldingen. 

Azure Digital Apparaatdubbels kan ook gegevens routeren naar downstream-Services, zoals [Azure Maps](../azure-maps/about-azure-maps.md) en [Time Series Insights](../time-series-insights/overview-what-is-tsi.md), voor opslag, werk stroom integratie, analyses en meer. 

## <a name="data-ingress"></a>Gegevens binnenkomend

Azure Digital Apparaatdubbels kan worden aangedreven met gegevens en gebeurtenissen van elke service:[IOT hub](../iot-hub/about-iot-hub.md), [Logic apps](../logic-apps/logic-apps-overview.md), uw eigen aangepaste service, enzovoort. Zo kunt u telemetrie van fysieke apparaten in uw omgeving verzamelen en deze gegevens verwerken met behulp van de Azure Digital Apparaatdubbels-grafiek in de Cloud.

In plaats van een ingebouwde IoT Hub achter de schermen te hebben, kunt u met Azure Digital Apparaatdubbels uw eigen IoT Hub gebruiken voor de service. U kunt een bestaand IoT Hub gebruiken dat momenteel in productie is, of een nieuw item implementeren dat voor dit doel moet worden gebruikt. Hiermee hebt u volledige toegang tot alle functies van het beheer van het apparaat van IoT Hub.

Gebruik een [**Azure-functie**](../azure-functions/functions-overview.md)om gegevens uit een wille keurige bron op te nemen in azure Digital apparaatdubbels. Meer informatie over dit patroon vindt [*u in procedures: opname telemetrie van IOT hub*](how-to-ingest-iot-hub-data.md)of probeer het zelf in de Azure Digital Apparaatdubbels [*zelf studie: verbinding maken met een end-to-end oplossing*](tutorial-end-to-end.md). 

U kunt ook leren hoe u Azure Digital Apparaatdubbels verbindt met een Logic Apps-trigger in [*How-to: integreert met Logic apps*](how-to-integrate-logic-apps.md).

## <a name="data-egress-services"></a>Services voor gegevens uitgaand verkeer

Azure Digital Apparaatdubbels kan gegevens verzenden naar verbonden **eind punten**. Ondersteunde eind punten kunnen zijn:
* [Event Hub](../event-hubs/event-hubs-about.md)
* [Event Grid](../event-grid/overview.md)
* [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)

Eindpunten worden aan Azure Digital Twins gekoppeld met behulp van beheer-API's of Azure Portal. Meer informatie over het koppelen van een eind punt aan Azure Digital Apparaatdubbels in [*procedures: eind punten en routes beheren*](how-to-manage-routes-apis-cli.md).

Er zijn veel andere services waarheen u uiteindelijk misschien uw gegevens wilt verzenden, zoals [Azure Storage](../storage/common/storage-introduction.md), [Azure Maps](../azure-maps/about-azure-maps.md)of [Time Series Insights](../time-series-insights/overview-what-is-tsi.md). Als u uw gegevens naar Services wilt verzenden, koppelt u de doel service aan een eind punt.

Als u bijvoorbeeld ook Azure Maps gebruikt en de locatie wilt correleren met uw Apparaatdubbels [dubbele grafiek](concepts-twins-graph.md)van Azure, kunt u Azure Functions met Event grid gebruiken om communicatie tussen alle services in uw implementatie tot stand te brengen. Meer informatie hierover vindt u in [ *How to: Azure Digital apparaatdubbels gebruiken om een Azure Maps binnenste kaart bij te werken*](how-to-integrate-maps.md)

U kunt ook leren hoe u gegevens op een vergelijk bare manier omleiden naar Time Series Insights, in [*procedures: integreren met Time Series Insights*](how-to-integrate-time-series-insights.md).

## <a name="next-steps"></a>Volgende stappen

Meer informatie over eind punten en routerings gebeurtenissen naar externe services:
* [*Concepten: Azure Digital Apparaatdubbels-gebeurtenissen routeren*](concepts-route-events.md)

Zie Azure Digital Apparaatdubbels instellen om gegevens op te nemen uit IoT Hub:
* [*Instructies: telemetrie opnemen van IoT Hub*](how-to-ingest-iot-hub-data.md)