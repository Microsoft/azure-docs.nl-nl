---
author: baanders
description: Neem het bestand op voor de resources die nodig zijn voor het maken van Azure Digital Apparaatdubbels-eind punten
ms.service: digital-twins
ms.topic: include
ms.date: 1/26/2021
ms.author: baanders
ms.openlocfilehash: 58c90bae3dea0f3a47489ea7d8de6a79f823dcab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "99054501"
---
### <a name="prerequisite-create-endpoint-resources"></a>Voor waarde: eindpunt resources maken

Als u een eind punt aan Azure Digital Apparaatdubbels wilt koppelen, moet het gebeurtenis grid-onderwerp, het Event Hub-of Service Bus-onderwerp dat u voor het eind punt gebruikt, al bestaan.

Gebruik de volgende tabel om te zien welke resources moeten worden ingesteld voordat u het eind punt maakt.

| Eindpunttype | Vereiste bronnen (gekoppeld aan instructies voor maken) |
| --- | --- |
| Event Grid-eind punt | [Event grid-onderwerp](../articles/event-grid/custom-event-quickstart-portal.md#create-a-custom-topic) |
| Event Hubs-eind punt | [Event &nbsp; hubs- &nbsp; naam ruimte](../articles/event-hubs/event-hubs-create.md)<br/><br/>[Event Hub](../articles/event-hubs/event-hubs-create.md)<br/><br/>Beschrijving [autorisatie regel](../articles/event-hubs/authorize-access-shared-access-signature.md) voor verificatie op basis van een sleutel | 
| Service Bus-eind punt | [Service Bus naam ruimte](../articles/service-bus-messaging/service-bus-quickstart-topics-subscriptions-portal.md)<br/><br/>[Service Bus onderwerp](../articles/service-bus-messaging/service-bus-quickstart-topics-subscriptions-portal.md)<br/><br/> Beschrijving [autorisatie regel](../articles/service-bus-messaging/service-bus-authentication-and-authorization.md#shared-access-signature) voor verificatie op basis van een sleutel|
