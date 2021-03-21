---
title: Event Grid vergelijken, route ring voor IoT Hub | Microsoft Docs
description: IoT Hub biedt een eigen Message Routing-service, maar kan ook worden geïntegreerd met Event Grid voor het publiceren van gebeurtenissen. Vergelijk de twee functies.
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: kgremban
ms.custom: fasttrack-edit
ms.openlocfilehash: 37b411792ea1a3e21e0f26df0c7905eb8d46310e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97897702"
---
# <a name="compare-message-routing-and-event-grid-for-iot-hub"></a>Berichten Routering en-Event Grid vergelijken voor IoT Hub

Azure IoT Hub biedt de mogelijkheid om gegevens van uw verbonden apparaten te streamen en die gegevens te integreren in uw zakelijke toepassingen. IoT Hub biedt twee methoden voor het integreren van IoT-gebeurtenissen in andere Azure-Services of zakelijke toepassingen. In dit artikel worden de twee functies beschreven die deze mogelijkheid bieden, zodat u kunt kiezen welke optie het meest geschikt is voor uw scenario.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

**[IOT hub bericht routering](iot-hub-devguide-messages-d2c.md)**: met deze IOT hub functie kunnen gebruikers apparaat-naar-Cloud-berichten door sturen naar service-eind punten, zoals Azure Storage containers, Event Hubs, service bus wachtrijen en service bus onderwerpen. Route ring biedt ook een query mogelijkheid om de gegevens te filteren voordat deze naar de eind punten worden doorgestuurd. Naast de telemetriegegevens van apparaten kunt u ook [niet-telemetrie-gebeurtenissen](iot-hub-devguide-messages-d2c.md#non-telemetry-events) verzenden die kunnen worden gebruikt om acties te activeren. 

**IOT hub integratie met Event grid**: Azure Event grid is een volledig beheerde service voor gebeurtenis routering die gebruikmaakt van een model voor publiceren en abonneren. IoT Hub en Event Grid werken samen om IoT Hub gebeurtenissen in bijna realtime te [integreren in Azure-en niet-Azure-Services](iot-hub-event-grid.md). IoT Hub publiceert zowel gebeurtenis-en telemetrie [-gebeurtenissen.](iot-hub-event-grid.md#event-types)

## <a name="differences"></a>Verschillen

Hoewel zowel bericht routering als Event Grid waarschuwings configuratie inschakelen, zijn er enkele belang rijke verschillen tussen de twee. Raadpleeg de volgende tabel voor meer informatie:

| Functie | Berichten routering IoT Hub | Integratie met Event Grid IoT Hub |
| ------- | --------------- | ---------- |
| **Berichten en gebeurtenissen van apparaten** | Ja, bericht routering kan worden gebruikt voor telemetriegegevens, dubbele wijzigingen van apparaten, levens cyclus gebeurtenissen en digitale twee gebeurtenissen. | Ja, Event Grid kunnen worden gebruikt voor telemetriegegevens en faxgebeurtenissen zoals het apparaat gemaakt/verwijderd/verbonden/verbroken. Maar Event grid kan niet worden gebruikt voor dubbele wijzigings gebeurtenissen van het apparaat en gebeurtenissen met een digitale dubbele wijziging. |
| **Ordenen** | Ja, de volg orde van gebeurtenissen wordt gehandhaafd.  | Nee, volg orde van gebeurtenissen is niet gegarandeerd. | 
| **Filteren** | Uitgebreide filtering voor eigenschappen van de bericht toepassing, eigenschappen van het berichten systeem, bericht tekst, dubbele Tags voor apparaten en dubbele eigenschappen van het apparaat. Filteren wordt niet toegepast op digitale dubbele wijzigings gebeurtenissen. Zie [query syntaxis voor bericht routering](iot-hub-devguide-routing-query-syntax.md)voor voor beelden. | Filteren op basis van het gebeurtenis type, het type onderwerp en de kenmerken van elke gebeurtenis. Zie voor voor beelden [inzicht in het filteren van gebeurtenissen in Event grid abonnementen](../event-grid/event-filtering.md). Bij het abonneren op telemetrie-gebeurtenissen kunt u extra filters toep assen op de gegevens die u wilt filteren op bericht eigenschappen, bericht tekst en onderliggend apparaat in uw IoT Hub voordat u naar Event Grid publiceert. Zie [gebeurtenissen filteren](../iot-hub/iot-hub-event-grid.md#filter-events). |
| **Eindpunten** | <ul><li>Event Hubs</li> <li>Azure Blob Storage</li> <li>Service Bus wachtrij</li> <li>Service Bus-onderwerpen</li></ul><br>Betaalde IoT Hub Sku's (S1, S2 en S3) zijn beperkt tot 10 aangepaste eind punten. u kunt Maxi maal 100 routes maken per IoT Hub. | <ul><li>Azure Functions</li> <li>Azure Automation</li> <li>Event Hubs</li> <li>Logic Apps</li> <li>Storage Blob</li> <li>Aangepaste onderwerpen</li> <li>Queue Storage</li> <li>Power Automate</li> <li>Services van derden via webhooks</li></ul><br>500-eind punten per IoT Hub worden ondersteund. Zie [Event grid gebeurtenis-handlers](../event-grid/overview.md#event-handlers)voor de meest recente lijst met eind punten. |
| **Kosten** | Er worden geen afzonderlijke kosten in rekening gebracht voor bericht routering. Alleen binnenbrengen van telemetrie in IoT Hub wordt in rekening gebracht. Als u bijvoorbeeld een bericht naar drie verschillende eind punten hebt doorgestuurd, wordt slechts één bericht in rekening gebracht. | Er worden geen kosten in rekening gebracht van IoT Hub. Event Grid biedt de eerste 100.000 bewerkingen per maand gratis en vervolgens $0,60 per miljoen bewerkingen. |

## <a name="similarities"></a>Overeenkomsten

IoT Hub bericht Routering en Event Grid hebben er ook vergelijk bare, wat wordt beschreven in de volgende tabel:

| Functie | Berichten routering IoT Hub | Integratie met Event Grid IoT Hub |
| ------- | --------------- | ---------- |
| **Maximale bericht grootte** | 256 KB, apparaat-naar-Cloud | 256 KB, apparaat-naar-Cloud |
| **Betrouwbaarheid** | Hoog: elk bericht wordt voor elke route ten minste één keer op het eind punt bezorgd. Verloopt alle berichten die niet binnen één uur worden geleverd. | Hoog: Hiermee wordt elk bericht ten minste één keer op de webhook bezorgd voor elk abonnement. Verloopt alle gebeurtenissen die niet binnen 24 uur worden geleverd. | 
| **Schaalbaarheid** | Hoog: geoptimaliseerd voor de ondersteuning van miljoenen gelijktijdig verbonden apparaten die miljarden berichten verzenden. | Hoog: de route ring van 10.000.000 gebeurtenissen per seconde per regio is mogelijk. |
| **Latentie** | Laag: bijna realtime. | Laag: bijna realtime. |
| **Verzenden naar meerdere eind punten** | Ja, Eén bericht verzenden naar meerdere eind punten. | Ja, Eén bericht verzenden naar meerdere eind punten.  
| **Beveiliging** | IOT hub biedt identiteit per apparaat en ingetrokken Access Control. Zie de [IOT hub Access Control](iot-hub-devguide-security.md)voor meer informatie. | Event Grid biedt validatie op drie punten: gebeurtenis abonnementen, gebeurtenis publicatie en webhook gebeurtenis levering. Zie [Event grid beveiliging en verificatie](../event-grid/security-authentication.md)voor meer informatie. |

## <a name="how-to-choose"></a>Een keuze maken

IoT Hub bericht Routering en de IoT Hub-integratie met Event Grid verschillende acties uit om Vergelijk bare resultaten te krijgen. Ze nemen informatie uit uw IoT Hub-oplossing en geven deze aan zodat andere services kunnen reageren. Hoe kunt u bepalen welke u wilt gebruiken? Houd rekening met de volgende vragen om uw beslissing te helpen: 

* **Wat voor soort gegevens verstuurt u naar de eind punten?**

   Gebruik IoT Hub bericht routering wanneer u telemetriegegevens moet verzenden naar andere services. Met bericht routering kunt u ook een query uitvoeren op bericht-en systeem eigenschappen, bericht tekst, dubbele Tags voor apparaten en dubbele eigenschappen van het apparaat.

   De IoT Hub integratie met Event Grid werkt met gebeurtenissen die optreden in de IoT Hub-service. Deze IoT Hub gebeurtenissen bevatten telemetriegegevens, apparaten die zijn gemaakt, verwijderd, verbonden en verbroken. Bij het abonneren op telemetrie-gebeurtenissen kunt u extra filters toep assen op de gegevens die u wilt filteren op bericht eigenschappen, bericht tekst en onderliggend apparaat in uw IoT Hub voordat u naar Event Grid publiceert. Zie [gebeurtenissen filteren](../iot-hub/iot-hub-event-grid.md#filter-events).

* **Welke eind punten moet deze informatie ontvangen?**

   IoT Hub bericht routering ondersteunt een beperkt aantal unieke eind punten en eindpunt typen, maar u kunt Connect oren maken om de gegevens en gebeurtenissen om te leiden naar extra eind punten. Zie de tabel in de vorige sectie voor een volledige lijst met ondersteunde eind punten. 

   De IoT Hub integratie met Event Grid ondersteunt 500-eind punten per IoT Hub en een groter aantal verschillende typen eind punten. Het systeem eigen integreert met Azure Functions-, Logic Apps-, opslag-en Service Bus wachtrijen en werkt ook met webhooks om het verzenden van gegevens buiten het Azure-service-ecosysteem en in zakelijke toepassingen van derden uit te breiden.

* **Is het belang rijk dat uw gegevens in de juiste volg orde arriveren?**

   IoT Hub bericht routering onderhoudt de volg orde waarin berichten worden verzonden, zodat ze op dezelfde manier binnenkomen.

   Event Grid garandeert niet dat eind punten gebeurtenissen ontvangen in dezelfde volg orde als waarin ze zijn opgetreden. Voor de gevallen waarin de absolute volg orde van berichten belang rijk is en/of waarbij een consument een betrouw bare unieke id voor berichten nodig heeft, raden we u aan bericht routering te gebruiken. 

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [IOT hub bericht routering](iot-hub-devguide-messages-d2c.md) en de [IOT hub-eind punten](iot-hub-devguide-endpoints.md).
* Meer informatie over [Azure Event Grid](../event-grid/overview.md).
* Zie voor meer informatie over het maken van bericht routes de zelf studie [IOT hub apparaat-naar-Cloud-berichten verwerken met routes](../iot-hub/tutorial-routing.md) .
* Probeer de Event Grid-integratie uit door [e-mail meldingen over Azure IOT hub-gebeurtenissen te verzenden met behulp van Logic apps](../event-grid/publish-iot-hub-events-to-logic-apps.md).
