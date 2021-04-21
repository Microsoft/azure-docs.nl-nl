---
title: Berichtsessies Azure Service Bus inschakelen | Microsoft Docs
description: In dit artikel wordt uitgelegd hoe u berichtsessies kunt inschakelen met Azure Portal, PowerShell, CLI en programmeertalen (C#, Java, Python en JavaScript)
ms.topic: how-to
ms.date: 04/19/2021
ms.openlocfilehash: dc2667b746a57c7f2e4ac5faec88c4540bed1180
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107755209"
---
# <a name="enable-message-sessions-for-an-azure-service-bus-queue-or-a-subscription"></a>Berichtsessies inschakelen voor een Azure Service Bus wachtrij of een abonnement
Azure Service Bus maken gezamenlijke en geordende verwerking van niet-gebonden reeksen gerelateerde berichten mogelijk. Sessies kunnen worden gebruikt in **de patronen first in, first out (FIFO)** en **request-response.** Zie Berichtsessies [voor meer informatie.](message-sessions.md) In dit artikel worden verschillende manieren beschreven om sessies in te Service Bus een wachtrij of abonnement. 

> [!IMPORTANT]
> - De basislaag van Service Bus biedt geen ondersteuning voor sessies. De lagen Standard en Premium ondersteunen sessies. Zie Prijzen voor Service Bus verschillen [tussen deze lagen.](https://azure.microsoft.com/pricing/details/service-bus/)
> - U kunt berichtsessies niet in- of uitschakelen nadat de wachtrij of het abonnement is gemaakt. U kunt dit alleen doen op het moment dat u de wachtrij of het abonnement maakt. 

## <a name="using-azure-portal"></a>Azure Portal gebruiken
Wanneer u **een** wachtrij in de Azure Portal, selecteert u **Sessies inschakelen,** zoals wordt weergegeven in de volgende afbeelding. 

:::image type="content" source="./media/message-sessions/queue-sessions.png" alt-text="Sessie inschakelen op het moment dat de wachtrij wordt gemaakt":::

Wanneer u een abonnement voor een onderwerp in de Azure Portal, selecteert u **Sessies inschakelen,** zoals wordt weergegeven in de volgende afbeelding. 

:::image type="content" source="./media/message-sessions/subscription-sessions.png" alt-text="Sessie inschakelen op het moment dat het abonnement wordt gemaakt":::

## <a name="using-azure-cli"></a>Azure CLI gebruiken
Als **u een wachtrij wilt maken met berichtsessies ingeschakeld,** gebruikt u de opdracht met ingesteld op [`az servicebus queue create`](/cli/azure/servicebus/queue#az_servicebus_queue_create) `--enable-session` `true` .

```azurecli-interactive
az servicebus queue create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name myqueue \
    --enable-session true
```

Als **u een abonnement wilt maken voor een onderwerp met berichtsessies ingeschakeld,** gebruikt u [`az servicebus topic subscription create`](/cli/azure/servicebus/topic/subscription#az_servicebus_topic_subscription_create) de opdracht met ingesteld op `--enable-session` `true` .

```azurecli-interactive
az servicebus topic subscription create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --topic-name mytopic \
    --name mysubscription \
    --enable-session true
```

## <a name="using-azure-powershell"></a>Azure PowerShell gebruiken
Als **u een wachtrij wilt maken met berichtsessies ingeschakeld,** gebruikt u de opdracht met ingesteld op [`New-AzServiceBusQueue`](/powershell/module/az.servicebus/new-azservicebusqueue) `-RequiresSession` `$True` . 

```azurepowershell-interactive
New-AzServiceBusQueue -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -QueueName myqueue `
    -RequiresSession $True
```

Als **u een abonnement wilt maken voor een onderwerp met berichtsessies ingeschakeld,** gebruikt u [`New-AzServiceBusSubscription`](/powershell/module/az.servicebus/new-azservicebussubscription) de opdracht met ingesteld op `-RequiresSession` `true` . 

```azurepowershell-interactive
New-AzServiceBusSubscription -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -TopicName mytopic `
    -SubscriptionName mysubscription `
    -RequiresSession $True
```

## <a name="using-azure-resource-manager-template"></a>Azure Resource Manager-sjabloon gebruiken
Als **u een wachtrij wilt maken met ingeschakelde berichtsessies,** stelt u in op in de sectie `requiresSession` `true` Wachtrijeigenschappen. Zie [Microsoft.ServiceBus namespaces/queues template reference (Sjabloonverwijzing voor Microsoft.ServiceBus-naamruimten/wachtrijen) voor meer informatie.](/azure/templates/microsoft.servicebus/namespaces/queues?tabs=json) 

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
            "requiresSession": true
          }
        }
      ]
    }
  ]
}

```

Als **u een abonnement wilt maken voor een onderwerp met ingeschakelde berichtsessies,** stelt u `requiresSession` in op in de sectie `true` Abonnementseigenschappen. Zie [Microsoft.ServiceBus namespaces/topics/subscriptions template reference (Sjabloonverwijzing voor Microsoft.ServiceBus-naamruimten/onderwerpen/abonnementen) voor meer informatie.](/azure/templates/microsoft.servicebus/namespaces/topics/subscriptions?tabs=json) 

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
                "requiresSession": true
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