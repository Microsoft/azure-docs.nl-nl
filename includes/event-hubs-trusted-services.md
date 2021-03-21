---
title: bestand opnemen
description: bestand opnemen
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 03/08/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 0e487b3ab3663c765c052f2064201865508ef57f
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102510703"
---
## <a name="trusted-microsoft-services"></a>Vertrouwde micro soft-Services
Wanneer u de instelling **vertrouwde micro soft-Services toestaan deze firewall wilt overs Laan** inschakelt, krijgen de volgende services toegang tot uw event hubs-resources.

| Vertrouwde service | Ondersteunde gebruiks scenario's | 
| --------------- | ------------------------- | 
| Azure Event Grid | Hiermee kan Azure Event Grid gebeurtenissen verzenden naar Event hubs in uw Event Hubs naam ruimte. U moet ook de volgende stappen uitvoeren: <ul><li>Door het systeem toegewezen identiteit voor een onderwerp of een domein inschakelen</li><li>De identiteit toevoegen aan de rol Azure Event Hubs data Sender op de Event Hubs naam ruimte</li><li>Configureer vervolgens het gebeurtenis abonnement dat gebruikmaakt van een Event Hub als een eind punt om de door het systeem toegewezen identiteit te gebruiken.</li></ul> <p>Zie [Event Delivery with a Managed Identity](../articles/event-grid/managed-service-identity.md) (Engelstalig) voor meer informatie.</p>|
| Azure Monitor (Diagnostische instellingen) | Hiermee kan Azure Monitor diagnostische gegevens verzenden naar Event hubs in uw Event Hubs naam ruimte. Azure Monitor kan de Event Hub lezen en ook gegevens naar de Event Hub schrijven. |
| Azure Stream Analytics | Hiermee kan een Azure Stream Analytics taak gegevens lezen van ([invoer](../articles/stream-analytics/stream-analytics-add-inputs.md)) of gegevens schrijven naar ([uitvoer](../articles/stream-analytics/event-hubs-output.md)) event hubs in uw event hubs naam ruimte. <p>**Belang rijk**: de stream Analytics taak moet worden geconfigureerd voor het gebruik van een **beheerde identiteit** voor toegang tot de Event hub. Zie [beheerde identiteiten gebruiken voor toegang tot Event hub vanuit een Azure stream Analytics-taak (preview)](../articles/stream-analytics/event-hubs-managed-identity.md)voor meer informatie. </p>|
| Azure IoT Hub | Hiermee kan IoT Hub berichten verzenden naar Event hubs in uw event hub-naam ruimte. U moet ook de volgende stappen uitvoeren: <ul><li>Door het systeem toegewezen identiteit inschakelen voor uw IoT-hub</li><li>Voeg de identiteit toe aan de rol Azure Event Hubs data Sender op de Event Hubs naam ruimte.</li><li>Configureer vervolgens de IoT Hub die gebruikmaakt van een Event Hub als een aangepast eind punt voor het gebruik van de verificatie op basis van identiteit.</li></ul>
| Azure API Management | <p>Met de API Management-service kunt u gebeurtenissen verzenden naar een Event Hub in uw Event Hubs naam ruimte.</p> <ul><li>U kunt aangepaste werk stromen activeren door gebeurtenissen naar uw Event Hub te verzenden wanneer een API wordt aangeroepen met behulp van het [beleid voor verzenden en aanvragen](../articles/api-management/api-management-sample-send-request.md).</li><li>U kunt ook een Event Hub als back-end behandelen in een API. Zie [verifiëren met een beheerde identiteit om toegang te krijgen tot een event hub](https://github.com/Azure/api-management-policy-snippets/blob/master/examples/Authenticate%20using%20Managed%20Identity%20to%20access%20Event%20Hub.xml)voor een voor beeld van een beleid. U moet ook de volgende stappen uitvoeren:<ol><li>Schakel de door het systeem toegewezen identiteit in op het API Management-exemplaar. Zie [beheerde identiteiten gebruiken in Azure API Management](../articles/api-management/api-management-howto-use-managed-service-identity.md)voor instructies.</li><li>De identiteit toevoegen aan de rol **Azure Event hubs data Sender** op de Event hubs naam ruimte</li></ol></li></ul> | 
