---
title: Automatisch doorsturen inschakelen voor Azure Service Bus en abonnementen
description: In dit artikel wordt uitgelegd hoe u automatisch doorsturen inschakelen voor wachtrijen en abonnementen met behulp van Azure Portal, PowerShell, CLI en programmeertalen (C#, Java, Python en JavaScript)
ms.topic: how-to
ms.date: 04/19/2021
ms.openlocfilehash: e5f69a82aac96deaf96f0ae6aaa1a26d0ee3aaf3
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107814662"
---
# <a name="enable-auto-forwarding-for-azure-service-bus-queues-and-subscriptions"></a>Automatisch doorsturen inschakelen voor Azure Service Bus en abonnementen
Met Service Bus functie voor automatisch doorsturen kunt u een wachtrij of abonnement aan een andere wachtrij of een ander onderwerp dat deel uitmaakt van dezelfde naamruimte, aan elkaar vastketenen. Wanneer automatisch doorsturen is ingeschakeld, Service Bus berichten die in de eerste wachtrij of het eerste abonnement (bron) worden geplaatst, automatisch verwijderd en in de tweede wachtrij of het tweede onderwerp (doel) geplaatst. Het is nog steeds mogelijk om een bericht rechtstreeks naar de doelentiteit te verzenden. Zie [Chaining Service Bus tities with auto forwarding (Entiteiten Service Bus automatisch doorsturen) voor meer informatie.](service-bus-auto-forwarding.md) In dit artikel worden verschillende manieren beschreven om automatisch doorsturen in te Service Bus wachtrijen en abonnementen. 

> [!IMPORTANT]
> De basislaag van Service Bus biedt geen ondersteuning voor de functie voor automatisch doorsturen. De standard- en premium-lagen ondersteunen de functie. Zie Prijzen voor Service Bus [verschillen tussen deze lagen.](https://azure.microsoft.com/pricing/details/service-bus/)

## <a name="using-azure-portal"></a>Azure Portal gebruiken
Wanneer u een wachtrij **of** **een** abonnement voor een onderwerp in de Azure Portal maakt, selecteert u Berichten doorsturen naar **wachtrij/onderwerp,** zoals wordt weergegeven in de volgende voorbeelden. Geef vervolgens op of u berichten wilt doorsturen naar een wachtrij of een onderwerp. In dit voorbeeld wordt de **optie Wachtrij** geselecteerd en wordt een wachtrij uit dezelfde naamruimte geselecteerd.

### <a name="create-a-queue-with-auto-forwarding-enabled"></a>Een wachtrij maken met automatisch doorsturen ingeschakeld
:::image type="content" source="./media/enable-auto-forward/create-queue.png" alt-text="Automatisch doorsturen inschakelen op het moment dat de wachtrij wordt gemaakt":::

### <a name="create-a-subscription-for-a-topic-with-auto-forwarding-enabled"></a>Een abonnement maken voor een onderwerp met automatisch doorsturen ingeschakeld
:::image type="content" source="./media/enable-auto-forward/create-subscription.png" alt-text="Automatisch doorsturen inschakelen op het moment dat het abonnement wordt gemaakt":::

### <a name="update-the-auto-forward-setting-for-an-existing-queue"></a>De instelling voor automatisch doorsturen voor een bestaande wachtrij bijwerken
Selecteer op **de** pagina Overzicht voor Service Bus wachtrij de huidige waarde voor de instelling **Berichten doorsturen naar.** In het volgende voorbeeld is de huidige waarde **Uitgeschakeld.** In het **venster Berichten doorsturen naar wachtrij/onderwerp** kunt u de wachtrij of het onderwerp selecteren waarin u wilt dat de berichten worden doorgestuurd. 

:::image type="content" source="./media/enable-auto-forward/queue-auto-forward.png" alt-text="Automatisch doorsturen inschakelen voor een bestaande wachtrij":::

### <a name="update-the-auto-forward-setting-for-an-existing-subscription"></a>De instelling voor automatisch doorsturen bijwerken voor een bestaand abonnement
Selecteer op **de** pagina Overzicht voor Service Bus abonnement de huidige waarde voor de instelling **Berichten doorsturen naar.** In het volgende voorbeeld is de huidige waarde **Uitgeschakeld.** In het **venster Berichten doorsturen naar wachtrij/onderwerp** kunt u de wachtrij of het onderwerp selecteren waarin u wilt dat de berichten worden doorgestuurd. 

:::image type="content" source="./media/enable-auto-forward/subscription-auto-forward.png" alt-text="Automatisch doorsturen inschakelen voor een bestaand abonnement":::

## <a name="using-azure-cli"></a>Azure CLI gebruiken
Als **u een wachtrij** wilt maken waarvoor automatisch doorsturen is ingeschakeld, gebruikt u de opdracht met die is ingesteld op de naam van de wachtrij of het onderwerp waarvoor u de berichten wilt [`az servicebus queue create`](/cli/azure/servicebus/queue#az_servicebus_queue_create) `--forward-to` doorsturen. 

```azurecli-interactive
az servicebus queue create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name myqueue \
    --forward-to myqueue2
```

Als **u de** instelling automatisch doorsturen voor een bestaande wachtrij wilt bijwerken, gebruikt u de opdracht met ingesteld op de naam van de wachtrij of het onderwerp waarvoor u de berichten wilt [`az servicebus queue update`](/cli/azure/servicebus/queue#az_servicebus_queue_update) `--forward-to` doorsturen. 

```azurecli-interactive
az servicebus queue update \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name myqueue \
    --forward-to myqueue2
```


Als u een **abonnement** op een onderwerp wilt maken waarvoor automatisch doorsturen is ingeschakeld, gebruikt u de opdracht met en stelt u in op de naam van de wachtrij of het onderwerp waarvoor u de berichten [`az servicebus topic subscription create`](/cli/azure/servicebus/topic/subscription#az_servicebus_topic_subscription_create) wilt `--forward-to` doorsturen.

```azurecli-interactive
az servicebus topic subscription create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --topic-name mytopic \
    --name mysubscription \
    --forward-to myqueue2
```

Als u de instelling **voor automatisch** doorsturen voor een abonnement op een onderwerp wilt bijwerken, gebruikt u de opdracht met en stelt u in op de naam van de wachtrij of het onderwerp waarvoor u de berichten wilt [`az servicebus topic subscription update`](/cli/azure/servicebus/topic/subscription#az_servicebus_topic_subscription_update) `--forward-to` doorsturen.

```azurecli-interactive
az servicebus topic subscription create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --topic-name mytopic \
    --name mysubscription \
    --forward-to myqueue2
```

## <a name="using-azure-powershell"></a>Azure PowerShell gebruiken
Als **u een wachtrij** wilt maken waarvoor automatisch doorsturen is ingeschakeld, gebruikt u de opdracht met die is ingesteld op de naam van de wachtrij of het onderwerp waarvoor u de berichten wilt [`New-AzServiceBusQueue`](/powershell/module/az.servicebus/new-azservicebusqueue) `-ForwardTo` doorsturen. 

```azurepowershell-interactive
New-AzServiceBusQueue -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -QueueName myqueue `
    -ForwardTo myqueue2
```

Als **u de instelling automatisch doorsturen voor een bestaande wachtrij wilt** bijwerken, gebruikt u de opdracht zoals wordt weergegeven in het volgende [`Set-AzServiceBusQueue`](/powershell/module/az.servicebus/set-azservicebusqueue) voorbeeld.

```azurepowershell-interactive
$queue=Get-AzServiceBusQueue -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -QueueName myqueue 

$queue.ForwardTo='myqueue2'

Set-AzServiceBusQueue -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -QueueName myqueue `
    -QueueObj $queue
``` 

Als u een **abonnement** wilt maken voor een onderwerp waarvoor automatisch doorsturen is ingeschakeld, gebruikt u de opdracht met die is ingesteld op de naam van de wachtrij of het onderwerp waarvoor u de berichten [`New-AzServiceBusSubscription`](/powershell/module/az.servicebus/new-azservicebussubscription) wilt `-ForwardTo` doorsturen.

```azurepowershell-interactive
New-AzServiceBusSubscription -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -TopicName mytopic `
    -SubscriptionName mysubscription `
    -ForwardTo myqueue2
```

Zie het volgende voorbeeld als u de instelling voor **automatisch doorsturen voor een** bestaand abonnement wilt bijwerken.

```azurepowershell-interactive
$subscription=Get-AzServiceBusSubscription -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -TopicName mytopic `
    -SubscriptionName mysub

$subscription.ForwardTo='mytopic2'

Set-AzServiceBusSubscription -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -Name mytopic `
    -SubscriptionName mysub `
    -SubscriptionObj $subscription 
```

## <a name="using-azure-resource-manager-template"></a>Azure Resource Manager-sjabloon gebruiken
Als **u een wachtrij** wilt maken waarvoor automatisch doorsturen is ingeschakeld, stelt u in de sectie wachtrijeigenschappen in op de naam van de wachtrij of het onderwerp waarvoor u de berichten wilt `forwardTo` doorsturen. Zie [Microsoft.ServiceBus namespaces/queues template reference (Sjabloonverwijzing voor Microsoft.ServiceBus-naamruimten/wachtrijen) voor meer informatie.](/azure/templates/microsoft.servicebus/namespaces/queues?tabs=json) 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "serviceBusNamespaceName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Service Bus namespace"
      }
    },
    "serviceBusQueueName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Queue"
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for all resources."
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.ServiceBus/namespaces",
      "apiVersion": "2018-01-01-preview",
      "name": "[parameters('serviceBusNamespaceName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "Standard"
      },
      "properties": {},
      "resources": [
        {
          "type": "Queues",
          "apiVersion": "2017-04-01",
          "name": "[parameters('serviceBusQueueName')]",
          "dependsOn": [
            "[resourceId('Microsoft.ServiceBus/namespaces', parameters('serviceBusNamespaceName'))]"
          ],
          "properties": {
            "forwardTo": "myqueue2"
          }
        }
      ]
    }
  ]
}

```

Als u een **abonnement** wilt maken voor een onderwerp waarvoor automatisch doorsturen is ingeschakeld, stelt u in de sectie wachtrijeigenschappen in op de naam van de wachtrij of het onderwerp waarvoor u de berichten `forwardTo` wilt doorsturen. Zie [Microsoft.ServiceBus namespaces/topics/subscriptions template reference (Sjabloonverwijzing voor Microsoft.ServiceBus-naamruimten/onderwerpen/abonnementen) voor meer informatie.](/azure/templates/microsoft.servicebus/namespaces/topics/subscriptions?tabs=json) 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "service_BusNamespace_Name": {
      "type": "string",
      "metadata": {
        "description": "Name of the Service Bus namespace"
      }
    },
    "serviceBusTopicName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Topic"
      }
    },
    "serviceBusSubscriptionName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Subscription"
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for all resources."
      }
    }
  },
  "resources": [
    {
      "apiVersion": "2018-01-01-preview",
      "name": "[parameters('service_BusNamespace_Name')]",
      "type": "Microsoft.ServiceBus/namespaces",
      "location": "[parameters('location')]",
      "sku": {
        "name": "Standard"
      },
      "properties": {},
      "resources": [
        {
          "apiVersion": "2017-04-01",
          "name": "[parameters('serviceBusTopicName')]",
          "type": "topics",
          "dependsOn": [
            "[resourceId('Microsoft.ServiceBus/namespaces/', parameters('service_BusNamespace_Name'))]"
          ],
          "properties": {
            "maxSizeInMegabytes": 1024
          },
          "resources": [
            {
              "apiVersion": "2017-04-01",
              "name": "[parameters('serviceBusSubscriptionName')]",
              "type": "Subscriptions",
              "dependsOn": [
                "[parameters('serviceBusTopicName')]"
              ],
              "properties": {
                "forwardTo": "myqueue2"
              }
            }
          ]
        }
      ]
    }
  ]
}
```


## <a name="next-steps"></a>Volgende stappen
Probeer de voorbeelden in de taal van uw keuze om de Azure Service Bus verkennen. 

- [Azure Service Bus-clientbibliotheekvoorbeelden voor Java](/samples/azure/azure-sdk-for-java/servicebus-samples/)
- [Azure Service Bus-clientbibliotheekvoorbeelden voor Python](/samples/azure/azure-sdk-for-python/servicebus-samples/)
- [Azure Service Bus-clientbibliotheekvoorbeelden voor JavaScript](/samples/azure/azure-sdk-for-js/service-bus-javascript/)
- [Azure Service Bus clientbibliotheekvoorbeelden voor TypeScript](/samples/azure/azure-sdk-for-js/service-bus-typescript/)
- [Voorbeelden van Azure.Messaging.ServiceBus voor .NET](/samples/azure/azure-sdk-for-net/azuremessagingservicebus-samples/)

Voorbeelden voor de oudere .NET- en Java-clientbibliotheken vindt u hieronder:
- [Microsoft.Azure.ServiceBus-voorbeelden voor .NET](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/)
- [azure-servicebus-voorbeelden voor Java](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/azure-servicebus/MessageBrowse)