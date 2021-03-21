---
title: Gebeurtenissen leveren met behulp van een persoonlijke koppelings service
description: In dit artikel wordt beschreven hoe u de beperking van het gebruik van een persoonlijke koppelings service kunt omzeilen.
ms.topic: how-to
ms.date: 02/12/2021
ms.openlocfilehash: 7ca15a76d56d9cdcdee741b661981b80c914d0e9
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "104722324"
---
# <a name="deliver-events-using-private-link-service"></a>Gebeurtenissen leveren met behulp van een persoonlijke koppelings service
Het is momenteel niet mogelijk om gebeurtenissen te leveren met behulp van [persoonlijke eind punten](../private-link/private-endpoint-overview.md). Dat wil zeggen dat er geen ondersteuning is als u strikte vereisten voor netwerk isolatie hebt waarbij het verkeer van de geleverde gebeurtenissen de privé-IP-ruimte niet mag verlaten. 

## <a name="use-managed-identity"></a>Een beheerde identiteit gebruiken
Als uw vereisten echter een veilige manier hebben om gebeurtenissen te verzenden met behulp van een versleuteld kanaal en een bekende identiteit van de afzender (in dit geval Event Grid) met behulp van open bare IP-ruimte, kunt u gebeurtenissen leveren aan Event Hubs-, Service Bus-of Azure Storage-service met behulp van een aangepast Azure Event grid-onderwerp of een domein met door het systeem beheerde identiteit. Zie [gebeurtenis bezorging met een beheerde identiteit](managed-service-identity.md)voor meer informatie over het leveren van gebeurtenissen met beheerde identiteit. 

Vervolgens kunt u een persoonlijke koppeling gebruiken die is geconfigureerd in Azure Functions of uw webhook die is geïmplementeerd in uw virtuele netwerk om gebeurtenissen op te halen. Zie het voor beeld: [verbinding maken met privé-eind punten met Azure functions](/samples/azure-samples/azure-functions-private-endpoints/connect-to-private-endpoints-with-azure-functions/).


:::image type="content" source="./media/consume-private-endpoints/deliver-private-link-service.svg" alt-text="Leveren via een privé koppelings service":::


Onder deze configuratie gaat het verkeer via het open bare IP/Internet van Event Grid naar Event Hubs, Service Bus of Azure Storage, maar kan het kanaal worden versleuteld en wordt een beheerde identiteit van Event Grid gebruikt. Als u uw Azure Functions of webhook die is geïmplementeerd in uw virtuele netwerk configureert om gebruik te maken van een Event Hubs, Service Bus of Azure Storage via een persoonlijke koppeling, blijft die sectie van het verkeer duidelijk binnen Azure.

## <a name="deliver-events-to-event-hubs-using-managed-identity"></a>Gebeurtenissen leveren aan Event Hubs met behulp van beheerde identiteit
Voer de volgende stappen uit om gebeurtenissen te leveren aan Event hubs in uw Event Hubs-naam ruimte met behulp van beheerde identiteit:

1. [Systeem toegewezen identiteit inschakelen voor een onderwerp of een domein](managed-service-identity.md#create-a-custom-topic-or-domain-with-an-identity). 
1. [Voeg de identiteit toe aan de rol **Azure Event hubs data Sender** op de Event hubs naam ruimte](../event-hubs/authenticate-managed-identity.md#to-assign-azure-roles-using-the-azure-portal).
1. [Schakel de optie **vertrouwde micro soft-services mogen deze firewall** instelling overs laan voor uw event hubs naam ruimte](../event-hubs/event-hubs-service-endpoints.md#trusted-microsoft-services)in. 
1. [Configureer het gebeurtenis abonnement](managed-service-identity.md#create-event-subscriptions-that-use-an-identity) dat gebruikmaakt van een event hub als een eind punt om de door het systeem toegewezen identiteit te gebruiken.

## <a name="deliver-events-to-service-bus-using-managed-identity"></a>Gebeurtenissen leveren aan Service Bus met behulp van beheerde identiteit
Ga als volgt te werk om gebeurtenissen te Service Bus die wacht rijen of onderwerpen in uw Service Bus naam ruimte gebruiken met behulp van beheerde identiteit:

1. [Systeem toegewezen identiteit inschakelen voor een onderwerp of een domein](managed-service-identity.md#create-a-custom-topic-or-domain-with-an-identity). 
1. De identiteit toevoegen aan de [Azure Service Bus gegevens afzender](/service-bus-messaging/service-bus-managed-service-identity#azure-built-in-roles-for-azure-service-bus) -rol op de service bus naam ruimte
1. [Schakel de optie **vertrouwde micro soft-services mogen deze firewall** instelling overs laan voor uw service bus naam ruimte](../service-bus-messaging/service-bus-service-endpoints.md#trusted-microsoft-services)in. 
1. [Configureer het gebeurtenis abonnement](managed-service-identity.md#create-event-subscriptions-that-use-an-identity) dat gebruikmaakt van een service bus wachtrij of onderwerp als een eind punt om de door het systeem toegewezen identiteit te gebruiken.

## <a name="deliver-events-to-storage"></a>Gebeurtenissen leveren aan opslag 
Voer de volgende stappen uit om gebeurtenissen te leveren aan opslag wachtrijen met behulp van beheerde identiteit:

1. [Systeem toegewezen identiteit inschakelen voor een onderwerp of een domein](managed-service-identity.md#create-a-custom-topic-or-domain-with-an-identity).
1. Voeg de identiteit toe aan de rol [afzender gegevens bericht van de opslag wachtrij](../storage/common/storage-auth-aad-rbac-portal.md) in azure Storage wachtrij.
1. [Configureer het gebeurtenis abonnement](managed-service-identity.md#create-event-subscriptions-that-use-an-identity) dat gebruikmaakt van een service bus wachtrij of onderwerp als een eind punt om de door het systeem toegewezen identiteit te gebruiken.


## <a name="next-steps"></a>Volgende stappen
Zie [gebeurtenis levering met een beheerde identiteit](managed-service-identity.md)voor meer informatie over het leveren van gebeurtenissen met behulp van een beheerde identiteit. 
