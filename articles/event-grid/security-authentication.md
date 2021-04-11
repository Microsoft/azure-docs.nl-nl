---
title: Gebeurtenis levering verifiëren naar gebeurtenis-handlers (Azure Event Grid)
description: In dit artikel worden verschillende methoden beschreven voor het verifiëren van de levering aan gebeurtenis-handlers in Azure Event Grid.
ms.topic: conceptual
ms.date: 01/07/2021
ms.openlocfilehash: f7a105c36b7c924e35c295edc43107353d738d5b
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105968132"
---
# <a name="authenticate-event-delivery-to-event-handlers-azure-event-grid"></a>Gebeurtenis levering verifiëren naar gebeurtenis-handlers (Azure Event Grid)
Dit artikel bevat informatie over het verifiëren van gebeurtenis levering aan gebeurtenis-handlers. Ook wordt uitgelegd hoe u de webhook-eind punten die worden gebruikt voor het ontvangen van gebeurtenissen van Event Grid met behulp van Azure Active Directory (Azure AD) of een gedeeld geheim kunt beveiligen.

## <a name="use-system-assigned-identities-for-event-delivery"></a>Door het systeem toegewezen identiteiten gebruiken voor gebeurtenis levering
U kunt een door het systeem toegewezen beheerde identiteit voor een onderwerp of domein inschakelen en de identiteit gebruiken om gebeurtenissen door te sturen naar ondersteunde doelen, zoals Service Bus-wacht rijen en-onderwerpen, Event hubs en opslag accounts.

Dit zijn de stappen: 

1. Maak een onderwerp of domein met een door het systeem toegewezen identiteit of werk een bestaand onderwerp of domein bij om identiteit in te scha kelen. 
1. Voeg de identiteit toe aan een geschikte rol (bijvoorbeeld Service Bus verzender van gegevens) op de bestemming (bijvoorbeeld een Service Bus wachtrij).
1. Wanneer u gebeurtenis abonnementen maakt, moet u het gebruik van de identiteit inschakelen voor het leveren van gebeurtenissen aan de bestemming. 

Zie [gebeurtenis levering met een beheerde identiteit](managed-service-identity.md)voor gedetailleerde stapsgewijze instructies.


## <a name="authenticate-event-delivery-to-webhook-endpoints"></a>Gebeurtenis levering verifiëren voor webhook-eind punten
In de volgende secties wordt beschreven hoe u de levering van gebeurtenissen verifieert aan webhook-eind punten. U moet een mechanisme voor validatie-Handshake gebruiken, onafhankelijk van de methode die u gebruikt. Zie [webhook Event Delivery](webhook-event-delivery.md) voor meer informatie. 


### <a name="using-azure-active-directory-azure-ad"></a>Azure Active Directory gebruiken (Azure AD)
U kunt het webhook-eind punt dat wordt gebruikt voor het ontvangen van gebeurtenissen van Event Grid, beveiligen met behulp van Azure AD. U moet een Azure AD-toepassing maken, een rol en service-principal maken in uw toepassing autoriseren Event Grid en het gebeurtenis abonnement configureren voor het gebruik van de Azure AD-toepassing. Meer informatie over het [configureren van Azure Active Directory met Event grid](secure-webhook-delivery.md).

### <a name="using-client-secret-as-a-query-parameter"></a>Client geheim gebruiken als query parameter
U kunt ook uw webhook-eind punt beveiligen door query parameters toe te voegen aan de webhook-doel-URL die is opgegeven als onderdeel van het maken van een gebeurtenis abonnement. Stel een van de query parameters in als een client geheim, zoals een [toegangs token](https://en.wikipedia.org/wiki/Access_token) of een gedeeld geheim. Event Grid-Service bevat alle query parameters in elke aanvraag voor het leveren van gebeurtenissen naar de webhook. De webhook-service kan het geheim ophalen en valideren. Als het client geheim is bijgewerkt, moet het gebeurtenis abonnement ook worden bijgewerkt. Zorg ervoor dat de webhook zowel oude als nieuwe geheimen voor een beperkte duur accepteert voordat u het gebeurtenis abonnement met het nieuwe geheim bijwerkt om afleverings fouten te voor komen tijdens deze geheime draaiing. 

Als query parameters kunnen client geheimen bevatten, worden ze behandeld met extra aandacht. Ze worden opgeslagen als versleuteld en zijn niet toegankelijk voor service operators. Ze worden niet geregistreerd als onderdeel van de service logboeken/traceringen. Bij het ophalen van de eigenschappen van het gebeurtenis abonnement worden de doel query parameters niet standaard geretourneerd. Bijvoorbeeld: [--include-Full-endpoint-URL](/cli/azure/eventgrid/event-subscription#az-eventgrid-event-subscription-show) -para meter moet worden gebruikt in azure [cli](/cli/azure).

Zie [webhook Event Delivery](webhook-event-delivery.md) (Engelstalig) voor meer informatie over het leveren van gebeurtenissen aan webhooks

> [!IMPORTANT]
> Azure Event Grid ondersteunt alleen **https** -webhook-eind punten. 

## <a name="endpoint-validation-with-cloudevents-v10"></a>Eindpunt validatie met CloudEvents v 1.0
Als u al bekend bent met Event Grid, is het mogelijk dat u op de hoogte bent van de validatie-Handshake van het eind punt om misbruik te voor komen. CloudEvents v 1.0 implementeert zijn eigen [beveiligings semantiek voor misbruik](webhook-event-delivery.md) door gebruik te maken van de **http-opties** methode. Zie [HTTP 1,1-Webhooks voor gebeurtenis levering-versie 1,0 voor](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#4-abuse-protection)meer informatie hierover. Wanneer u het CloudEvents-schema voor uitvoer gebruikt, maakt Event Grid gebruik van de CloudEvents v 1.0-misbruik beveiliging in plaats van het Event Grid validatie gebeurtenis mechanisme. Zie [CloudEvents v 1.0-schema gebruiken met Event grid](cloudevents-schema.md)voor meer informatie. 


## <a name="next-steps"></a>Volgende stappen
Zie [publicerend clients verifiëren](security-authenticate-publishing-clients.md) voor meer informatie over het verifiëren van clients die gebeurtenissen publiceren naar onderwerpen of domeinen. 
