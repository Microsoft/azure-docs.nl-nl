---
title: Inzicht Azure IoT Hub aangepaste eindpunten | Microsoft Docs
description: "Ontwikkelaarshandleiding: routeringsquery's gebruiken om apparaat-naar-cloud-berichten te routeren naar aangepaste eindpunten."
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/09/2018
ms.openlocfilehash: 4ad57473e0950f031fbeadee2302f85557ed526f
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107388258"
---
# <a name="use-message-routes-and-custom-endpoints-for-device-to-cloud-messages"></a>Berichtroutes en aangepaste eindpunten gebruiken voor apparaat-naar-cloud-berichten

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

IoT Hub [berichtroutering](iot-hub-devguide-routing-query-syntax.md) kunnen gebruikers apparaat-naar-cloud-berichten routeren naar service-gerichte eindpunten. Routering biedt ook de mogelijkheid om de gegevens te filteren voordat ze naar de eindpunten worden doorgeleid. Elke routeringsquery die u configureert, heeft de volgende eigenschappen:

| Eigenschap      | Beschrijving |
| ------------- | ----------- |
| **Naam**      | De unieke naam die de query identificeert. |
| **Bron**    | De oorsprong van de gegevensstroom waarop actie moet worden onder ondernemen. Bijvoorbeeld telemetrie van apparaten. |
| **Condition** | De queryexpressie voor de routeringsquery die wordt uitgevoerd op de eigenschappen van de berichttoepassing, systeemeigenschappen, berichtbesturingssysteem, tags van apparaat dubbels en eigenschappen van apparaattwee om te bepalen of deze overeenkomen met het eindpunt. Zie querysyntaxis voor berichtroutering voor meer informatie over het maken [van een query](iot-hub-devguide-routing-query-syntax.md) |
| **Eindpunt**  | De naam van het eindpunt waar IoT Hub berichten verzendt die overeenkomen met de query. U wordt aangeraden een eindpunt te kiezen in dezelfde regio als uw IoT-hub. |

Eén bericht kan overeenkomen met de voorwaarde voor meerdere routeringsquery's. In dat geval levert IoT Hub het bericht aan het eindpunt dat is gekoppeld aan elke overeenkomende query. IoT Hub ontdubbelt ook automatisch de levering van berichten, dus als een bericht overeenkomt met meerdere query's die dezelfde bestemming hebben, wordt het slechts één keer naar die bestemming geschreven.

## <a name="endpoints-and-routing"></a>Eindpunten en routering

Een IoT-hub heeft een standaard [ingebouwd eindpunt](iot-hub-devguide-messages-read-builtin.md). U kunt aangepaste eindpunten maken om berichten naar door te sturen door andere services in de abonnementen die u hebt te koppelen aan de hub. IoT Hub ondersteunt momenteel Azure Storage containers, Event Hubs, Service Bus wachtrijen en Service Bus onderwerpen als aangepaste eindpunten.

Wanneer u routering en aangepaste eindpunten gebruikt, worden berichten alleen aan het ingebouwde eindpunt geleverd als ze niet overeenkomen met een query. Als u berichten wilt leveren aan het ingebouwde eindpunt en aan een aangepast eindpunt, voegt u een route toe die berichten naar het **ingebouwde** gebeurtenis-eindpunt verzendt.

> [!NOTE]
> * IoT Hub ondersteunt alleen het schrijven van gegevens Azure Storage containers als blobs.
> * Service Bus wachtrijen en onderwerpen met **Sessies** of **Duplicaatdetectie** ingeschakeld worden niet ondersteund als aangepaste eindpunten.
> * In de Azure Portal kunt u alleen aangepaste routerings-eindpunten maken voor Azure-resources die zich in hetzelfde abonnement als uw hub hebben. U kunt aangepaste eindpunten maken voor resources in andere abonnementen die u bezit, maar aangepaste eindpunten moeten worden geconfigureerd met een andere methode dan de Azure Portal.

Zie Eindpunten voor meer informatie over het maken van aangepaste IoT Hub [in IoT Hub eindpunten.](iot-hub-devguide-endpoints.md)

Zie voor meer informatie over het lezen van aangepaste eindpunten:

* Lezen uit [Azure Storage containers](../storage/blobs/storage-blobs-introduction.md).

* Lezen vanuit [Event Hubs](../event-hubs/event-hubs-dotnet-standard-getstarted-send.md).

* Lezen uit [Service Bus wachtrijen](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md).

* Lees meer over [Service Bus onderwerpen](../service-bus-messaging/service-bus-dotnet-how-to-use-topics-subscriptions.md).

## <a name="next-steps"></a>Volgende stappen

* Zie eindpunten IoT Hub meer informatie over [IoT Hub eindpunten.](iot-hub-devguide-endpoints.md)

* Zie Querysyntaxis voor berichtroutering voor meer informatie over de querytaal die u gebruikt voor het definiëren van [routeringsquery's.](iot-hub-devguide-routing-query-syntax.md)

* De [zelfstudie IoT Hub van apparaat-naar-cloud-berichten](tutorial-routing.md) met behulp van routes laat zien hoe u routeringsquery's en aangepaste eindpunten gebruikt.
