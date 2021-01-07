---
title: bestand opnemen
description: bestand opnemen
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 01/05/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 5c0585182fcd6899bdd123607f3b7d46a97253e9
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/06/2021
ms.locfileid: "97956462"
---
## <a name="trusted-microsoft-services"></a>Vertrouwde micro soft-Services
Wanneer u de instelling **vertrouwde micro soft-Services toestaan deze firewall wilt overs Laan** inschakelt, krijgen de volgende services toegang tot uw event hubs-resources.

| Vertrouwde service | Ondersteunde gebruiks scenario's | 
| --------------- | ------------------------- | 
| Azure Event Grid | Hiermee kan Azure Event Grid gebeurtenissen verzenden naar Event hubs in uw Event Hubs naam ruimte. U moet ook de volgende stappen uitvoeren: <ul><li>Door het systeem toegewezen identiteit voor een onderwerp of een domein inschakelen</li><li>De identiteit toevoegen aan de rol Azure Event Hubs data Sender op de Event Hubs naam ruimte</li><li>Configureer vervolgens het gebeurtenis abonnement dat gebruikmaakt van een Event Hub als een eind punt om de door het systeem toegewezen identiteit te gebruiken.</li></ul> <p>Zie [Event Delivery with a Managed Identity](../articles/event-grid/managed-service-identity.md) (Engelstalig) voor meer informatie.</p>|
| Azure Monitor (Diagnostische instellingen) | Hiermee kan Azure Monitor diagnostische gegevens verzenden naar Event hubs in uw Event Hubs naam ruimte. |
| Azure Stream Analytics | Hiermee kan een Azure Stream Analytics taak gegevens lezen van ([invoer](../articles/stream-analytics/stream-analytics-add-inputs.md)) of gegevens schrijven naar ([uitvoer](../articles/stream-analytics/event-hubs-output.md)) event hubs in uw event hubs naam ruimte. <p>**Belang rijk**: de stream Analytics taak moet worden geconfigureerd voor het gebruik van een **beheerde identiteit** voor toegang tot de Event hub. Zie [beheerde identiteiten gebruiken voor toegang tot Event hub vanuit een Azure stream Analytics-taak (preview)](../articles/stream-analytics/event-hubs-managed-identity.md)voor meer informatie. </p>|
| Azure IoT Hub | Hiermee kan IoT Hub berichten verzenden naar Event hubs in uw event hub-naam ruimte. U moet ook de volgende stappen uitvoeren: <ul><li>Door het systeem toegewezen identiteit inschakelen voor uw IoT-hub</li><li>Voeg de identiteit toe aan de rol Azure Event Hubs data Sender op de Event Hubs naam ruimte.</li><li>Configureer vervolgens de IoT Hub die gebruikmaakt van een Event Hub als een aangepast eind punt voor het gebruik van de verificatie op basis van identiteit.</li></ul>
