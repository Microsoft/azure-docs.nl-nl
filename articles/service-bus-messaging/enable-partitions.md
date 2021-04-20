---
title: Partitionering in Azure Service Bus wachtrijen en onderwerpen inschakelen
description: In dit artikel wordt uitgelegd hoe u partitionering in Azure Service Bus-wachtrijen en -onderwerpen kunt inschakelen met behulp van Azure Portal, PowerShell, CLI en programmeertalen (C#, Java, Python en JavaScript)
ms.topic: how-to
ms.date: 04/19/2021
ms.openlocfilehash: fb704f784d490cb73c14fc73b1a6c4368d16acbc
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107755205"
---
# <a name="enable-partitioning-for-an-azure-service-bus-queue-or-a-topic"></a>Partitionering inschakelen voor een Azure Service Bus wachtrij of een onderwerp
Service Bus kunnen wachtrijen en onderwerpen, of berichtentiteiten, worden gepartitief over meerdere berichtenbrokers en berichtenopslag. Partitioneren betekent dat de totale doorvoer van een gepartitiesteerde entiteit niet langer wordt beperkt door de prestaties van één berichtenbroker of berichtenopslag. Als een berichtenopslag tijdelijk uitstort, wordt er bovendien geen gepartitiede wachtrij of onderwerp niet beschikbaar gemaakt. Gepartitiede wachtrijen en onderwerpen kunnen alle geavanceerde Service Bus bevatten, zoals ondersteuning voor transacties en sessies. Zie Gepartitiede [wachtrijen en onderwerpen voor meer informatie.](service-bus-partitioning.md) In dit artikel worden verschillende manieren beschreven om detectie van dubbele berichten in te Service Bus een wachtrij of onderwerp. 

> [!IMPORTANT]
> - Partitionering is beschikbaar bij het maken van entiteiten voor alle wachtrijen en onderwerpen in Basic- of Standard-SKU's. Deze is niet beschikbaar voor de Premium Messaging-SKU, maar alle eerder gepartities in Premium-naamruimten blijven werken zoals verwacht.
> - Het is niet mogelijk om de partitioneringsoptie voor een bestaande wachtrij of onderwerp te wijzigen. U kunt de optie alleen instellen wanneer u een wachtrij of een onderwerp maakt. 

## <a name="using-azure-portal"></a>Azure Portal gebruiken
Bij het maken **van een** wachtrij in Azure Portal selecteert u **Partitionering inschakelen,** zoals wordt weergegeven in de volgende afbeelding. 

:::image type="content" source="./media/enable-partitions/create-queue.png" alt-text="Partitionering inschakelen op het moment dat de wachtrij wordt gemaakt":::

Bij het maken van een onderwerp in Azure Portal selecteert u **Partitionering inschakelen,** zoals wordt weergegeven in de volgende afbeelding. 

:::image type="content" source="./media/enable-partitions/create-topic.png" alt-text="Partitionering inschakelen op het moment dat het onderwerp wordt gemaakt":::

## <a name="using-azure-cli"></a>Azure CLI gebruiken
Als **u een wachtrij wilt maken met partitionering ingeschakeld,** gebruikt u de opdracht met ingesteld op [`az servicebus queue create`](/cli/azure/servicebus/queue#az_servicebus_queue_create) `--enable-partitioning` `true` .

```azurecli-interactive
az servicebus queue create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name myqueue \
    --enable-partitioning true
```

Als **u een onderwerp wilt maken met partitionering ingeschakeld,** gebruikt u de opdracht met ingesteld op [`az servicebus topic create`](/cli/azure/servicebus/topic#az_servicebus_topic_create) `--enable-partitioning` `true` .

```azurecli-interactive
az servicebus topic create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name mytopic \
    --enable-partitioning true
```

## <a name="using-azure-powershell"></a>Azure PowerShell gebruiken
Als **u een wachtrij wilt maken met partitionering ingeschakeld,** gebruikt u de opdracht met ingesteld op [`New-AzServiceBusQueue`](/powershell/module/az.servicebus/new-azservicebusqueue) `-EnablePartitioning` `$True` . 

```azurepowershell-interactive
New-AzServiceBusQueue -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -QueueName myqueue `
    -EnablePartitioning $True
```

Als **u een onderwerp wilt maken met partitionering ingeschakeld,** gebruikt u de opdracht met ingesteld op [`New-AzServiceBusTopic`](/powershell/module/az.servicebus/new-azservicebustopic) `-EnablePartitioning` `true` . 

```azurepowershell-interactive
New-AzServiceBusTopic -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -Name mytopic `
    -EnablePartitioning $True
```

## <a name="using-azure-resource-manager-template"></a>Azure Resource Manager-sjabloon gebruiken
Als **u een wachtrij wilt maken met partitionering ingeschakeld,** stelt u in op in de sectie `enablePartitioning` `true` wachtrij-eigenschappen. Zie [Microsoft.ServiceBus namespaces/queues template reference (Sjabloonverwijzing voor Microsoft.ServiceBus-naamruimten/wachtrijen) voor meer informatie.](/azure/templates/microsoft.servicebus/namespaces/queues?tabs=json) 

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
            "enablePartitioning": true
          }
        }
      ]
    }
  ]
}

```

Als **u een onderwerp wilt maken met detectie van duplicaten ingeschakeld,** stelt u `enablePartitioning` in op in de sectie `true` onderwerpeigenschappen. Zie [Microsoft.ServiceBus namespaces/topics template reference (Sjabloonverwijzing voor Microsoft.ServiceBus-naamruimten/onderwerpen) voor meer informatie.](/azure/templates/microsoft.servicebus/namespaces/topics?tabs=json) 

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
            "enablePartitioning": true
          }
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
- [Azure.Messaging.ServiceBus-voorbeelden voor .NET](/samples/azure/azure-sdk-for-net/azuremessagingservicebus-samples/)

Zoek voorbeelden voor de oudere .NET- en Java-clientbibliotheken hieronder:
- [Voorbeelden van Microsoft.Azure.ServiceBus voor .NET](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/)
- [azure-servicebus-voorbeelden voor Java](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/azure-servicebus/MessageBrowse)