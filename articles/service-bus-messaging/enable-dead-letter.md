---
title: Inschakelen van dead lettering Azure Service Bus wachtrijen en abonnementen
description: In dit artikel wordt uitgelegd hoe u inschakelen van wachtrijen en abonnementen in wachtrijen en abonnementen met behulp van Azure Portal, PowerShell, CLI en programmeertalen (C#, Java, Python en JavaScript)
ms.topic: how-to
ms.date: 04/20/2021
ms.openlocfilehash: 4d4a232d9129cf110a33ece56330ee708f9341b6
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107819368"
---
# <a name="enable-dead-lettering-on-message-expiration-for-azure-service-bus-queues-and-subscriptions"></a>Inschakelen van in wachtrijen en abonnementen Azure Service Bus verlopen berichten
Azure Service Bus wachtrijen en abonnementen voor onderwerpen bieden een secundaire subqueue, ook wel een wachtrij voor inlopende letters (DLQ) genoemd. De wachtrij voor in wachtrijen geplaatste berichten hoeft niet expliciet te worden gemaakt en kan niet worden verwijderd of beheerd, onafhankelijk van de hoofdentiteit. Het doel van de wachtrij voor niet-bezorgde berichten is om berichten te houden die niet kunnen worden bezorgd bij een ontvanger of berichten die niet kunnen worden verwerkt. Zie Overview [of Service Bus dead-letter queues voor meer informatie.](service-bus-dead-letter-queues.md) In dit artikel worden verschillende manieren beschreven om inschakelen van Service Bus wachtrijen en abonnementen. 

## <a name="using-azure-portal"></a>Azure Portal gebruiken
Wanneer u een  wachtrij **of** een abonnement voor een  onderwerp in de Azure Portal maakt, selecteert u Inschakelen van inlopende berichten bij verlopen berichten, zoals wordt weergegeven in de volgende voorbeelden. 

### <a name="create-a-queue-with-dead-lettering-enabled"></a>Een wachtrij maken met 'dead lettering' ingeschakeld
:::image type="content" source="./media/enable-dead-letter/create-queue.png" alt-text="Inschakelen van dead lettering op het moment dat de wachtrij wordt gemaakt":::

### <a name="create-a-subscription-with-dead-lettering-enabled"></a>Een abonnement maken met 'dead lettering' ingeschakeld
:::image type="content" source="./media/enable-dead-letter/create-subscription.png" alt-text="Inschakelen van inlopende letters op het moment dat het abonnement wordt gemaakt":::

### <a name="update-the-dead-lettering-on-message-expiration-setting-for-an-existing-queue"></a>De instelling voor verlopen berichten voor een bestaande wachtrij bijwerken
Selecteer op **de** pagina Overzicht voor Service Bus wachtrij de huidige waarde voor de instelling Dead **lettering on** message expiration (Verlopen berichten bij het verlopen van berichten). In het volgende voorbeeld is de huidige waarde **Uitgeschakeld.** U kunt in- of uitschakelen wanneer berichten verlopen in het pop-upvenster. 

:::image type="content" source="./media/enable-dead-letter/queue-configuration.png" alt-text="Inschakelen van dead-lettering bij verlopen van berichten voor een bestaande wachtrij":::

### <a name="update-the-dead-lettering-on-message-expiration-setting-for-an-existing-subscription"></a>De instelling voor verlopen berichten voor een bestaand abonnement bijwerken
Selecteer op **de** pagina Overzicht voor Service Bus abonnement de huidige waarde voor de instelling Dead **lettering on** message expiration (Verlopen berichten bij het verlopen van berichten). In het volgende voorbeeld is de huidige waarde **Uitgeschakeld.** U kunt in- of uitschakelen wanneer berichten verlopen in het pop-upvenster. 

:::image type="content" source="./media/enable-dead-letter/subscription-configuration.png" alt-text="Inschakelen van dead-lettering bij het verlopen van berichten voor een bestaand abonnement":::

## <a name="using-azure-cli"></a>Azure CLI gebruiken
Als **u een wachtrij wilt maken met dead lettering** bij het verlopen van berichten ingeschakeld, gebruikt u [`az servicebus queue create`](/cli/azure/servicebus/queue#az_servicebus_queue_create) de opdracht met ingesteld op `--enable-dead-lettering-on-message-expiration` `true` . 

```azurecli-interactive
az servicebus queue create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name myqueue \
    --enable-dead-lettering-on-message-expiration true
```

Als **u de instelling voor het verlopen van berichten voor** een bestaande wachtrij wilt inschakelen, gebruikt u de opdracht met ingesteld op [`az servicebus queue update`](/cli/azure/servicebus/queue#az_servicebus_queue_update) `--enable-dead-lettering-on-message-expiration` `true` . 

```azurecli-interactive
az servicebus queue update \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name myqueue \
    --enable-dead-lettering-on-message-expiration true
```


Als **u een abonnement wilt maken op een onderwerp met 'dead lettering'** ingeschakeld bij het verlopen van berichten, gebruikt u [`az servicebus topic subscription create`](/cli/azure/servicebus/topic/subscription#az_servicebus_topic_subscription_create) de opdracht met ingesteld op `--enable-dead-lettering-on-message-expiration` `true` .

```azurecli-interactive
az servicebus topic subscription create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --topic-name mytopic \
    --name mysubscription \
    --enable-dead-lettering-on-message-expiration true
```

Als **u de instelling voor het verlopen van berichten voor** een abonnement op een onderwerp wilt inschakelen, gebruikt u de opdracht met set [`az servicebus topic subscription update`](/cli/azure/servicebus/topic/subscription#az_servicebus_topic_subscription_update) `--enable-dead-lettering-on-message-expiration` `true` .

```azurecli-interactive
az servicebus topic subscription create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --topic-name mytopic \
    --name mysubscription \
    --enable-dead-lettering-on-message-expiration true
```

## <a name="using-azure-powershell"></a>Azure PowerShell gebruiken
Als **u een wachtrij wilt maken met dead lettering** bij het verlopen van berichten ingeschakeld, gebruikt u [`New-AzServiceBusQueue`](/powershell/module/az.servicebus/new-azservicebusqueue) de opdracht met ingesteld op `-DeadLetteringOnMessageExpiration` `$True` . 

```azurepowershell-interactive
New-AzServiceBusQueue -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -QueueName myqueue `
    -DeadLetteringOnMessageExpiration $True
```

Als **u de instelling voor het verlopen van berichten voor** een bestaande wachtrij wilt inschakelen, gebruikt u de opdracht zoals wordt weergegeven in het volgende [`Set-AzServiceBusQueue`](/powershell/module/az.servicebus/set-azservicebusqueue) voorbeeld.

```azurepowershell-interactive
$queue=Get-AzServiceBusQueue -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -QueueName myqueue 

$queue.DeadLetteringOnMessageExpiration=$True

Set-AzServiceBusQueue -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -QueueName myqueue `
    -QueueObj $queue
``` 

Als **u een abonnement wilt maken voor een onderwerp met 'dead lettering'** ingeschakeld bij het verlopen van berichten, gebruikt u [`New-AzServiceBusSubscription`](/powershell/module/az.servicebus/new-azservicebussubscription) de opdracht met ingesteld op `-DeadLetteringOnMessageExpiration` `$True` . 

```azurepowershell-interactive
New-AzServiceBusSubscription -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -TopicName mytopic `
    -SubscriptionName mysubscription `
    -DeadLetteringOnMessageExpiration $True
```

Zie het volgende voorbeeld als u de instelling voor het **verlopen van** berichten voor een bestaand abonnement wilt inschakelen.

```azurepowershell-interactive
$subscription=Get-AzServiceBusSubscription -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -TopicName mytopic `
    -SubscriptionName mysub

$subscription.DeadLetteringOnMessageExpiration=$True

Set-AzServiceBusSubscription -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -Name mytopic `
    -SubscriptionName mysub `
    -SubscriptionObj $subscription 
```

## <a name="using-azure-resource-manager-template"></a>Azure Resource Manager-sjabloon gebruiken
Als **u een wachtrij wilt maken met 'dead lettering' ingeschakeld bij het** verlopen van berichten, stelt u in de sectie `deadLetteringOnMessageExpiration` wachtrijeigenschappen in op `true` . Zie [Microsoft.ServiceBus namespaces/queues template reference (Sjabloonverwijzing voor Microsoft.ServiceBus-naamruimten/wachtrijen) voor meer informatie.](/azure/templates/microsoft.servicebus/namespaces/queues?tabs=json) 

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
            "deadLetteringOnMessageExpiration": true
          }
        }
      ]
    }
  ]
}

```

Als **u een abonnement wilt maken voor een onderwerp met 'dead lettering'** ingeschakeld bij het verlopen van berichten, stelt u in de sectie `deadLetteringOnMessageExpiration` wachtrijeigenschappen in op `true` . Zie [Microsoft.ServiceBus namespaces/topics/subscriptions template reference (Sjabloonverwijzing voor Microsoft.ServiceBus-naamruimten/onderwerpen/abonnementen) voor meer informatie.](/azure/templates/microsoft.servicebus/namespaces/topics/subscriptions?tabs=json) 

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
                "deadLetteringOnMessageExpiration": true
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