---
title: Overzicht integratie Azure Service Bus met Azure Event Grid | Microsoft Docs
description: Dit artikel bevat een beschrijving van de manier waarop Azure Service Bus berichten integreert met Azure Event Grid.
documentationcenter: .net
author: spelluru
ms.topic: conceptual
ms.date: 02/11/2021
ms.author: spelluru
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 658107bb74396891c8e6e05a9e8074a9416a5f6f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100369659"
---
# <a name="azure-service-bus-to-event-grid-integration-overview"></a>Overzicht integratie Azure Service Bus met Azure Event Grid
Service Bus kan nu gebeurtenissen naar Event Grid verzenden als er zich berichten in een wachtrij of abonnement bevinden zonder dat er ontvangers zijn. U kunt Event Grid-abonnementen maken voor uw Service Bus-naamruimten, naar deze gebeurtenissen luisteren en erop reageren door een ontvanger te starten. Met deze functie kunt u Service Bus gebruiken in reactieve programmeermodellen. Het belangrijkste scenario van deze functie is dat Service Bus-wachtrijen of -abonnementen met een laag volume aan berichten geen ontvanger nodig hebben die continu berichten opvraagt. 

Als u deze functie wilt inschakelen, hebt u het volgende nodig:

* Een Service Bus Premium-naamruimte met ten minste één Service Bus-wachtrij of een Service Bus-onderwerp met ten minste één abonnement.
* Inzender-toegang tot de Service Bus-naamruimte. Navigeer naar uw Service Bus-naam ruimte in de Azure Portal en selecteer vervolgens **toegangs beheer (IAM)** en selecteer het tabblad **roltoewijzingen** . Controleer of u de Inzender toegang hebt tot de naam ruimte. 
* Bovendien moet u een abonnement op Event Grid voor de Service Bus-naamruimte hebben. Het abonnement ontvangt de melding van Event Grid dat er berichten klaarstaan. Typische abonnees zijn mogelijk de functie Logic Apps van Azure App Service, Azure Functions of een webhook die contact opneemt met een web-app. De abonnee verwerkt vervolgens de berichten. 

![19][]

[!INCLUDE [event-grid-service-bus.md](../../includes/event-grid-service-bus.md)]

## <a name="event-grid-subscriptions-for-service-bus-namespaces"></a>Event Grid abonnementen voor Service Bus naam ruimten
U kunt op drie verschillende manieren Event Grid-abonnementen voor Service Bus-naamruimten maken:

- Azure Portal. Raadpleeg de volgende zelf studies voor meer informatie over het gebruik van Azure Portal om Event Grid-abonnementen te maken voor Service Bus gebeurtenissen met Azure Logic Apps en Azure Functions als handlers. 
    - [Azure Logic Apps](service-bus-to-event-grid-integration-example.md#receive-messages-by-using-logic-apps)
    - [Azure Functions](service-bus-to-event-grid-integration-function.md#connect-the-function-and-namespace-via-event-grid)
* Azure CLI. In het volgende CLI-voor beeld ziet u hoe u een Azure Functions-abonnement maakt voor een [systeem onderwerp](../event-grid/system-topics.md) dat door een service bus naam ruimte wordt gemaakt.

     ```azurecli-interactive
    namespaceid=$(az resource show --namespace Microsoft.ServiceBus --resource-type namespaces --name "<service bus namespace>" --resource-group "<resource group that contains the service bus namespace>" --query id --output tsv
    
    az eventgrid event-subscription create --resource-id $namespaceid --name "<YOUR EVENT GRID SUBSCRIPTION NAME>" --endpoint "<your_endpoint_url>" --subject-ends-with "<YOUR SERVICE BUS SUBSCRIPTION NAME>"
    ```
- Zo. Hier volgt een voorbeeld:
    ```powershell-interactive
    $namespaceID = (Get-AzServiceBusNamespace -ResourceGroupName "<YOUR RESOURCE GROUP NAME>" -NamespaceName "<YOUR NAMESPACE NAME>").Id
    
    New-AzEVentGridSubscription -EventSubscriptionName "<YOUR EVENT GRID SUBSCRIPTION NAME>" -ResourceId $namespaceID -Endpoint "<YOUR ENDPOINT URL>” -SubjectEndsWith "<YOUR SERVICE BUS SUBSCRIPTION NAME>"
    ```
### <a name="how-many-events-are-emitted-and-how-often"></a>Hoeveel gebeurtenissen worden er verzonden en hoe vaak?

Als u meerdere wachtrijen en onderwerpen of abonnementen in de naamruimte hebt, krijgt u minimaal één gebeurtenis per wachtrij en één per abonnement. De gebeurtenissen worden onmiddellijk uitgezonden als de Service Bus-entiteit geen berichten bevat en er een nieuw bericht arriveert. Of de gebeurtenissen worden elke twee minuten uitgezonden, tenzij Service Bus een actieve ontvanger detecteert. De gebeurtenissen worden niet onderbroken als u door berichten bladert.

Standaard verzendt Service Bus gebeurtenissen voor alle entiteiten in de naamruimte. Als u alleen voor specifieke entiteiten gebeurtenissen wilt ophalen, raadpleegt u de volgende sectie.

### <a name="use-filters-to-limit-where-you-get-events-from"></a>Filters gebruiken om te beperken waarvandaan u gebeurtenissen ophaalt

Als u gebeurtenissen bijvoorbeeld alleen van één wachtrij of één abonnement binnen uw naamruimte wilt ontvangen, kunt u de filters *Begint met* of *Eindigt op* van Event Grid gebruiken. In sommige interfaces worden deze filters *voorvoegselfilter* en *achtervoegselfilter* genoemd. Als u gebeurtenissen voor meerdere maar niet alle wachtrijen en abonnementen wilt ontvangen, kunt u meerdere verschillende Event Grid-abonnementen maken en voor elk ervan een filter opgeven.

## <a name="next-steps"></a>Volgende stappen
Zie de volgende zelfstudies: 
- [Azure Logic Apps om Service Bus berichten te verwerken die via Event Grid worden ontvangen](service-bus-to-event-grid-integration-example.md#receive-messages-by-using-logic-apps)
- [Azure Functions om Service Bus berichten te verwerken die via Event Grid worden ontvangen](service-bus-to-event-grid-integration-function.md#connect-the-function-and-namespace-via-event-grid)

[1]: ./media/service-bus-to-event-grid-integration-concept/sbtoeventgrid1.png
[19]: ./media/service-bus-to-event-grid-integration-concept/sbtoeventgriddiagram.png
[8]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid8.png
[9]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid9.png
[20]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal.png
[21]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal2.png
