---
title: Gebeurtenislevering verifiëren voor gebeurtenis-handlers (Azure Event Grid)
description: In dit artikel worden verschillende manieren beschreven voor het authenticeren van levering aan gebeurtenis-handlers in Azure Event Grid.
ms.topic: conceptual
ms.date: 01/07/2021
ms.openlocfilehash: 7db258ee152e4b1c46362e74e0246b80513ca9f2
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107777253"
---
# <a name="authenticate-event-delivery-to-event-handlers-azure-event-grid"></a>Gebeurtenislevering verifiëren voor gebeurtenis-handlers (Azure Event Grid)
Dit artikel bevat informatie over het authenticeren van de levering van gebeurtenissen aan gebeurtenis-handlers. U ziet ook hoe u de webhook-eindpunten beveiligt die worden gebruikt voor het ontvangen van gebeurtenissen van Event Grid met behulp van Azure Active Directory (Azure AD) of een gedeeld geheim.

## <a name="use-system-assigned-identities-for-event-delivery"></a>Door het systeem toegewezen identiteiten gebruiken voor levering van gebeurtenissen
U kunt een door het systeem toegewezen beheerde identiteit inschakelen voor een onderwerp of domein en de identiteit gebruiken om gebeurtenissen door testuren naar ondersteunde bestemmingen, zoals Service Bus wachtrijen en onderwerpen, Event Hubs en opslagaccounts.

Dit zijn de stappen: 

1. Maak een onderwerp of domein met een door het systeem toegewezen identiteit, of werk een bestaand onderwerp of domein bij om identiteit in te stellen. 
1. Voeg de identiteit toe aan een geschikte rol (bijvoorbeeld Service Bus Data Sender) op de bestemming (bijvoorbeeld een Service Bus wachtrij).
1. Wanneer u gebeurtenisabonnementen maakt, moet u het gebruik van de identiteit inschakelen om gebeurtenissen aan de bestemming te leveren. 

Zie Levering van gebeurtenissen met een beheerde identiteit voor gedetailleerde [stapsgewijse instructies.](managed-service-identity.md)


## <a name="authenticate-event-delivery-to-webhook-endpoints"></a>Gebeurtenislevering aan webhook-eindpunten verifiëren
In de volgende secties wordt beschreven hoe u gebeurtenislevering bij webhook-eindpunten verifieert. U moet een validatiehandhakemechanisme gebruiken, ongeacht de methode die u gebruikt. Zie [Levering van webhookgebeurtenissen](webhook-event-delivery.md) voor meer informatie. 


### <a name="using-azure-active-directory-azure-ad"></a>Met Azure Active Directory (Azure AD)
U kunt het webhook-eindpunt dat wordt gebruikt voor het ontvangen van gebeurtenissen van Event Grid met behulp van Azure AD beveiligen. U moet een Azure AD-toepassing maken, een rol en service-principal maken in uw toepassing die Event Grid autoriseert, en het gebeurtenisabonnement configureren voor het gebruik van de Azure AD-toepassing. Meer informatie over [het configureren Azure Active Directory met Event Grid](secure-webhook-delivery.md).

### <a name="using-client-secret-as-a-query-parameter"></a>Clientgeheim gebruiken als queryparameter
U kunt uw webhook-eindpunt ook beveiligen door queryparameters toe te voegen aan de webhookdoel-URL die is opgegeven als onderdeel van het maken van een gebeurtenisabonnement. Stel een van de queryparameters in als een clientgeheim, zoals een [toegangs token](https://en.wikipedia.org/wiki/Access_token) of een gedeeld geheim. Event Grid-service bevat alle queryparameters in elke gebeurtenisleveringsaanvraag voor de webhook. De webhookservice kan het geheim ophalen en valideren. Als het clientgeheim wordt bijgewerkt, moet ook het gebeurtenisabonnement worden bijgewerkt. Om leveringsfouten tijdens deze geheimrotatie te voorkomen, moet u ervoor zorgen dat de webhook zowel oude als nieuwe geheimen voor een beperkte duur accepteert voordat u het gebeurtenisabonnement bij het nieuwe geheim bij te werken. 

Omdat queryparameters clientgeheimen kunnen bevatten, worden ze met extra zorgvuldigheid afgehandeld. Ze worden opgeslagen als versleuteld en zijn niet toegankelijk voor service-operators. Ze worden niet geregistreerd als onderdeel van de servicelogboeken/traceringen. Bij het ophalen van de eigenschappen van het gebeurtenisabonnement worden doelqueryparameters niet standaard geretourneerd. Bijvoorbeeld: [--include-full-endpoint-url](/cli/azure/eventgrid/event-subscription#az_eventgrid_event_subscription_show) parameter moet worden gebruikt in Azure [CLI.](/cli/azure)

Zie Levering van webhookgebeurtenissen voor meer informatie over het leveren van gebeurtenissen [aan webhooks](webhook-event-delivery.md)

> [!IMPORTANT]
> Azure Event Grid ondersteunt alleen  HTTPS-webhook-eindpunten. 

## <a name="endpoint-validation-with-cloudevents-v10"></a>Eindpuntvalidatie met CloudEvents v1.0
Als u al bekend bent met Event Grid, bent u mogelijk op de hoogte van de eindpuntvalidatiehandhake voor het voorkomen van misbruik. CloudEvents v1.0 implementeert een eigen semantiek voor beveiliging tegen misbruik met [behulp](webhook-event-delivery.md) van de **HTTP OPTIONS-methode.** Zie HTTP [1.1 Web Hooks for event delivery - Version 1.0 (HTTP 1.1-web hooks](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#4-abuse-protection)voor levering van gebeurtenissen - versie 1.0) voor meer informatie. Wanneer u het CloudEvents-schema voor uitvoer gebruikt, gebruikt Event Grid de cloudgebeurtenisbeveiliging CloudEvents v1.0 in plaats van het mechanisme voor Event Grid validatiegebeurtenis. Zie [CloudEvents v1.0-schema](cloudevents-schema.md)gebruiken met Event Grid voor meer Event Grid. 


## <a name="next-steps"></a>Volgende stappen
Zie [Publicatie-clients verifiëren](security-authenticate-publishing-clients.md) voor meer informatie over het verifiëren van clients die gebeurtenissen publiceren naar onderwerpen of domeinen. 
