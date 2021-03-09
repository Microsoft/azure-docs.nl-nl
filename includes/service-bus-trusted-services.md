---
title: bestand opnemen
description: bestand opnemen
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 03/08/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: cd4d5de5d93e4aea862aaabd10fb5d3c6d45cb1c
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102511137"
---
## <a name="trusted-microsoft-services"></a>Vertrouwde micro soft-Services
Wanneer u de instelling **vertrouwde micro soft-Services toestaan deze firewall wilt overs Laan** inschakelt, krijgen de volgende services toegang tot uw service bus-resources.

| Vertrouwde service | Ondersteunde gebruiks scenario's | 
| --------------- | ------------------------- | 
| Azure Event Grid | Hiermee kan Azure Event Grid gebeurtenissen verzenden naar wacht rijen of onderwerpen in uw Service Bus naam ruimte. U moet ook de volgende stappen uitvoeren: <ul><li>Door het systeem toegewezen identiteit voor een onderwerp of een domein inschakelen</li><li>De identiteit toevoegen aan de Azure Service Bus gegevens afzender-rol op de Service Bus naam ruimte</li><li>Configureer vervolgens het gebeurtenis abonnement dat gebruikmaakt van een Service Bus wachtrij of onderwerp als een eind punt om de door het systeem toegewezen identiteit te gebruiken.</li></ul> <p>Zie [Event Delivery with a Managed Identity](../articles/event-grid/managed-service-identity.md) (Engelstalig) voor meer informatie.</p>|
| Azure API Management | <p>Met de API Management-service kunt u berichten verzenden naar een Service Bus wachtrij/onderwerp in uw Service Bus naam ruimte.</p><ul><li>U kunt aangepaste werk stromen activeren door berichten te verzenden naar uw Service Bus wachtrij/onderwerp wanneer een API wordt aangeroepen met behulp van het [beleid voor verzenden en aanvragen](../articles/api-management/api-management-sample-send-request.md).</li><li>U kunt ook een Service Bus wachtrij/onderwerp beschouwen als uw back-end in een API. Zie [verifiëren met een beheerde identiteit om toegang te krijgen tot een service bus wachtrij of onderwerp](https://github.com/Azure/api-management-policy-snippets/blob/master/examples/Authenticate%20using%20Managed%20Identity%20to%20access%20Service%20Bus.xml)voor een voor beeld van een beleid. U moet ook de volgende stappen uitvoeren:<ol><li>Schakel de door het systeem toegewezen identiteit in op het API Management-exemplaar. Zie [beheerde identiteiten gebruiken in Azure API Management](../articles/api-management/api-management-howto-use-managed-service-identity.md)voor instructies.</li><li>De identiteit toevoegen aan de **Azure Service Bus gegevens afzender** -rol op de service bus naam ruimte</li></ol></li></ul> | 