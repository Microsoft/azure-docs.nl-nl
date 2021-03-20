---
title: Meer informatie over aangepaste Azure IoT Hub-eind punten | Microsoft Docs
description: "Ontwikkelaars handleiding: routerings query's gebruiken om apparaat-naar-Cloud-berichten naar aangepaste eind punten te routeren."
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/09/2018
ms.openlocfilehash: 8aebb5b6f6a3ac53bc49fd1d2f75de88667865fb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92147647"
---
# <a name="use-message-routes-and-custom-endpoints-for-device-to-cloud-messages"></a>Bericht routes en aangepaste eind punten voor apparaat-naar-Cloud-berichten gebruiken

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Met IoT Hub [bericht routering](iot-hub-devguide-routing-query-syntax.md) kunnen gebruikers apparaat-naar-Cloud-berichten naar service gerichte eind punten routeren. Route ring biedt ook een query mogelijkheid om de gegevens te filteren voordat deze naar de eind punten worden doorgestuurd. Elke routerings query die u configureert, heeft de volgende eigenschappen:

| Eigenschap      | Beschrijving |
| ------------- | ----------- |
| **Naam**      | De unieke naam waarmee de query wordt geïdentificeerd. |
| **Bron**    | De oorsprong van de gegevens stroom waarop moet worden gehandeld. Bijvoorbeeld telemetrie van apparaat. |
| **Condition** | De query-expressie voor de routerings query die wordt uitgevoerd op basis van de eigenschappen van de bericht toepassing, systeem eigenschappen, bericht tekst, dubbele labels van het apparaat en dubbele eigenschappen van het apparaat om te bepalen of het een overeenkomst voor het eind punt is. Voor meer informatie over het maken van een query raadpleegt u de [syntaxis voor bericht routering query's](iot-hub-devguide-routing-query-syntax.md) weer geven |
| **Eindpunt**  | De naam van het eind punt waar IoT Hub berichten verzendt die overeenkomen met de query. We raden u aan een eind punt in dezelfde regio te kiezen als uw IoT-hub. |

Eén bericht kan overeenkomen met de voor waarde voor meerdere routerings query's, in welk geval IoT Hub het bericht levert aan het eind punt dat is gekoppeld aan elke overeenkomende query. IoT Hub wordt ook de levering van berichten automatisch gedupliceerd, dus als een bericht overeenkomt met meerdere query's die dezelfde bestemming hebben, wordt deze slechts eenmaal naar die bestemming geschreven.

## <a name="endpoints-and-routing"></a>Eind punten en route ring

Een IoT-hub heeft een standaard [ingebouwd eind punt](iot-hub-devguide-messages-read-builtin.md). U kunt aangepaste eind punten maken om berichten naar te sturen door andere services in uw abonnement te koppelen aan de hub. IoT Hub ondersteunt momenteel Azure Storage containers, Event Hubs, Service Bus wachtrijen en Service Bus onderwerpen als aangepaste eind punten.

Wanneer u route ring en aangepaste eind punten gebruikt, worden berichten alleen bezorgd bij het ingebouwde eind punt als ze niet overeenkomen met een query. Als u berichten wilt leveren aan het ingebouwde eind punt en aan een aangepast eind punt, voegt u een route toe waarmee berichten worden verzonden naar het eind punt van de ingebouwde **gebeurtenissen** .

> [!NOTE]
> * IoT Hub ondersteunt alleen het schrijven van gegevens naar Azure Storage containers als blobs.
> * Service Bus-wacht rijen en-onderwerpen met **sessies** of **Duplicaten detectie** is ingeschakeld, worden niet ondersteund als aangepaste eind punten.

Zie [IOT hub-eind punten](iot-hub-devguide-endpoints.md)voor meer informatie over het maken van aangepaste eind punten in IOT hub.

Zie voor meer informatie over het lezen van aangepaste eind punten:

* Lezen van [Azure Storage-containers](../storage/blobs/storage-blobs-introduction.md).

* Lezen van [Event hubs](../event-hubs/event-hubs-dotnet-standard-getstarted-send.md).

* Lezen van [service bus-wacht rijen](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md).

* Lezen van [Service Bus onderwerpen](../service-bus-messaging/service-bus-dotnet-how-to-use-topics-subscriptions.md).

## <a name="next-steps"></a>Volgende stappen

* Zie [IOT hub-eind punten](iot-hub-devguide-endpoints.md)voor meer informatie over IOT hub-eind punten.

* Zie [bericht routering query syntaxis](iot-hub-devguide-routing-query-syntax.md)voor meer informatie over de query taal die u gebruikt om routerings query's te definiëren.

* Het [proces IOT hub apparaat-naar-Cloud-berichten met behulp van routes](tutorial-routing.md) zelf studie laat zien hoe u routerings query's en aangepaste eind punten kunt gebruiken.