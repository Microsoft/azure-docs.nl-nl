---
title: 'Azure Service Bus : aantal berichten'
description: Haal het aantal berichten op dat in wachtrijen en abonnementen wordt opgevraagd met behulp van Azure Resource Manager en de Azure Service Bus NamespaceManager-API's.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 5fc7211673badfde664d77128f9d79523926ccc9
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107814590"
---
# <a name="get-message-counters"></a>Berichttellers ontvangen
In dit artikel worden verschillende manieren beschreven om de volgende berichttellingen voor een wachtrij of abonnement op te halen. Kennis van het aantal actieve berichten is handig om te bepalen of een wachtrij een achterstand opbouwt waarvoor meer resources moeten worden verwerkt dan wat er momenteel is geïmplementeerd. 

| Prestatiemeteritem | Description |
| ----- | ---------- | 
| ActiveMessageCount | Het aantal berichten in de wachtrij of het abonnement dat actief is en klaar is voor levering. |
| ScheduledMessageCount | Het aantal berichten met de geplande status. |
| DeadLetterMessageCount | Het aantal berichten in de wachtrij voor in wachtrij voor niet-geplaatste berichten. |
| TransferMessageCount | Het aantal berichten dat in afwachting is van overdracht naar een andere wachtrij of een ander onderwerp. |
| TransferDeadLetterMessageCount | Het aantal berichten dat niet kan worden overgebracht naar een andere wachtrij of een ander onderwerp en die zijn verplaatst naar de wachtrij voor niet-geplaatste overdrachtsberichten. |

Als een toepassing resources wil schalen op basis van de lengte van de wachtrij, moet dit in een gemeten tempo worden geregeld. Het verkrijgen van de berichtentellers is een dure bewerking binnen de berichtenbroker en het uitvoeren ervan is vaak rechtstreeks en nadelig van invloed op de prestaties van de entiteit.

> [!NOTE]
> De berichten die worden verzonden naar een Service Bus worden doorgestuurd naar abonnementen voor dat onderwerp. Het aantal actieve berichten voor het onderwerp zelf is dus 0, omdat deze berichten zijn doorgestuurd naar het abonnement. Haal het aantal berichten op bij het abonnement en controleer of het groter is dan 0. Hoewel u berichten in het abonnement ziet, worden ze daadwerkelijk opgeslagen in een opslag die eigendom is van het onderwerp. Als u naar de abonnementen kijkt, hebben deze berichten niet-nul (wat maximaal 323 MB aan ruimte voor deze hele entiteit betekent).


## <a name="using-azure-portal"></a>Azure Portal gebruiken
Navigeer naar uw naamruimte en selecteer de wachtrij. U ziet berichttellers op de **pagina Overzicht** voor de wachtrij.

:::image type="content" source="./media/message-counters/queue-overview.png" alt-text="Berichttellers op de overzichtspagina van de wachtrij":::

Navigeer naar uw naamruimte, selecteer het onderwerp en selecteer vervolgens het abonnement voor het onderwerp. U ziet berichttellers op de **pagina Overzicht** voor de wachtrij.

:::image type="content" source="./media/message-counters/subscription-overview.png" alt-text="Berichttellers op de overzichtspagina van het abonnement":::

## <a name="using-azure-cli"></a>Azure CLI gebruiken
Gebruik de [`az servicebus queue show`](/cli/azure/servicebus/queue#az_servicebus_queue_show) opdracht om de details van het aantal berichten voor een wachtrij op te halen, zoals wordt weergegeven in het volgende voorbeeld. 

```azurecli-interactive
az servicebus queue show --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name myqueue \
    --query countDetails
```

Hier is een voorbeeld van uitvoer:

```bash
ActiveMessageCount    DeadLetterMessageCount    ScheduledMessageCount    TransferMessageCount    TransferDeadLetterMessageCount
--------------------  ------------------------  -----------------------  ----------------------  --------------------------------
0                     0                         0                        0                       0
```

Gebruik de [`az servicebus topic subscription show`](/cli/azure/servicebus/topic/subscription#az_servicebus_topic_subscription_show) opdracht om de details van het aantal berichten voor een abonnement op te halen, zoals wordt weergegeven in het volgende voorbeeld. 

```azurecli-interactive
az servicebus topic subscription show --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --topic-name mytopic \
    --name mysub \
    --query countDetails
```

## <a name="using-azure-powershell"></a>Azure PowerShell gebruiken
Met PowerShell kunt u de details van het aantal berichten voor een wachtrij als volgt verkrijgen:

```azurepowershell-interactive
$queueObj=Get-AzServiceBusQueue -ResourceGroup myresourcegroup `
                    -NamespaceName mynamespace `
                    -QueueName myqueue 

$queueObj.CountDetails
```

Dit is de voorbeelduitvoer:

```bash
ActiveMessageCount             : 7
DeadLetterMessageCount         : 1
ScheduledMessageCount          : 3
TransferMessageCount           : 0
TransferDeadLetterMessageCount : 0
```

U kunt de details van het aantal berichten voor een abonnement als volgt verkrijgen:

```azurepowershell-interactive
$topicObj= Get-AzServiceBusSubscription -ResourceGroup myresourcegroup `
                -NamespaceName mynamespace `
                -TopicName mytopic `
                -SubscriptionName mysub

$topicObj.CountDetails
```

Het `MessageCountDetails` geretourneerde object heeft de volgende eigenschappen: `ActiveMessageCount` , , , , `DeadLetterMessageCount` `ScheduledMessageCount` `TransferDeadLetterMessageCount` `TransferMessageCount` . 

## <a name="next-steps"></a>Volgende stappen

Probeer de voorbeelden in de taal van uw keuze om de Azure Service Bus verkennen. 

- [Azure Service Bus-clientbibliotheekvoorbeelden voor Java](/samples/azure/azure-sdk-for-java/servicebus-samples/)
- [Azure Service Bus-clientbibliotheekvoorbeelden voor Python](/samples/azure/azure-sdk-for-python/servicebus-samples/)
- [Azure Service Bus-clientbibliotheekvoorbeelden voor JavaScript](/samples/azure/azure-sdk-for-js/service-bus-javascript/)
- [Azure Service Bus clientbibliotheekvoorbeelden voor TypeScript](/samples/azure/azure-sdk-for-js/service-bus-typescript/)
- [Azure.Messaging.ServiceBus-voorbeelden voor .NET](/samples/azure/azure-sdk-for-net/azuremessagingservicebus-samples/)

Zoek voorbeelden voor de oudere .NET- en Java-clientbibliotheken hieronder:
- [Voorbeelden van Microsoft.Azure.ServiceBus voor .NET](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/)
- [azure-servicebus-voorbeelden voor Java](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/azure-servicebus/MessageBrowse)
