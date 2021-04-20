---
title: Detectie van dubbele berichten inschakelen - Azure Service Bus
description: In dit artikel wordt uitgelegd hoe u detectie van dubbele berichten kunt inschakelen met Azure Portal, PowerShell, CLI en programmeertalen (C#, Java, Python en JavaScript)
ms.topic: how-to
ms.date: 04/19/2021
ms.openlocfilehash: 708009fcf2479660316b38ac0b7d545d450de28c
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107755210"
---
# <a name="enable-duplicate-message-detection-for-an-azure-service-bus-queue-or-a-topic"></a>Detectie van dubbele berichten inschakelen voor Azure Service Bus wachtrij of een onderwerp
Wanneer u duplicaatdetectie voor een wachtrij of onderwerp inschakelen, Azure Service Bus een geschiedenis bij van alle berichten die naar de wachtrij of het onderwerp worden verzonden voor een geconfigureerde hoeveelheid tijd. Tijdens dat interval worden er geen dubbele berichten opgeslagen in uw wachtrij of onderwerp. Het inschakelen van deze eigenschap garandeert exact één levering gedurende een door de gebruiker gedefinieerde tijdspanne. Zie Detectie van duplicaten [voor meer informatie.](duplicate-detection.md) In dit artikel worden verschillende manieren beschreven om detectie van dubbele berichten in te Service Bus een wachtrij of onderwerp. 


> [!NOTE]
> - De basislaag van Service Bus biedt geen ondersteuning voor duplicaatdetectie. De lagen Standard en Premium bieden ondersteuning voor duplicaatdetectie. Zie Prijzen voor Service Bus verschillen [tussen deze lagen.](https://azure.microsoft.com/pricing/details/service-bus/)
> - U kunt duplicaatdetectie niet in- of uitschakelen nadat de wachtrij of het onderwerp is gemaakt. U kunt dit alleen doen op het moment dat u de wachtrij of het onderwerp maakt. 

## <a name="using-azure-portal"></a>Azure Portal gebruiken
Wanneer u **een** wachtrij in de Azure Portal, selecteert u **Duplicaatdetectie inschakelen,** zoals wordt weergegeven in de volgende afbeelding. U kunt de grootte van het duplicaatdetectievenster configureren bij het maken van een wachtrij of onderwerp. 

:::image type="content" source="./media/enable-duplicate-detection/create-queue.png" alt-text="Duplicaatdetectie inschakelen op het moment dat de wachtrij wordt gemaakt":::

Bij het maken van een onderwerp in Azure Portal selecteert u **Duplicaatdetectie inschakelen,** zoals wordt weergegeven in de volgende afbeelding. 

:::image type="content" source="./media/enable-duplicate-detection/create-topic.png" alt-text="Duplicaatdetectie inschakelen op het moment dat het onderwerp wordt gemaakt":::

U kunt deze instelling ook configureren voor een bestaande wachtrij of een bestaand onderwerp, als u duplicaatdetectie hebt ingeschakeld op het moment van maken. 

### <a name="update-duplicate-detection-window-size-for-an-existing-queue-or-a-topic"></a>De grootte van het duplicaatdetectievenster bijwerken voor een bestaande wachtrij of een onderwerp
Als u de grootte van het duplicaatdetectievenster  voor een bestaande wachtrij of een onderwerp wilt wijzigen, selecteert u op de pagina Overzicht de optie **Wijzigen** voor **het venster Duplicaatdetectie.**  

#### <a name="queue"></a>Wachtrij
:::image type="content" source="./media/enable-duplicate-detection/window-size.png" alt-text="De grootte van het duplicaatdetectievenster voor een wachtrij instellen":::

#### <a name="topic"></a>Onderwerp
:::image type="content" source="./media/enable-duplicate-detection/window-size-topic.png" alt-text="De grootte van het duplicaatdetectievenster instellen voor een onderwerp":::


## <a name="using-azure-cli"></a>Azure CLI gebruiken
Als **u een wachtrij wilt maken met duplicaatdetectie ingeschakeld,** gebruikt u de opdracht met ingesteld op [`az servicebus queue create`](/cli/azure/servicebus/queue#az_servicebus_queue_create) `--enable-duplicate-detection` `true` . 

```azurecli-interactive
az servicebus queue create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name myqueue \
    --enable-duplicate-detection true \
    --duplicate-detection-history-time-window P1D
```

Als **u een onderwerp wilt maken met detectie van duplicaten ingeschakeld,** gebruikt u de opdracht met [`az servicebus topic create`](/cli/azure/servicebus/topic#az_servicebus_topic_create) ingesteld op `--enable-duplicate-detection` `true` .

```azurecli-interactive
az servicebus topic create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name mytopic \
    --enable-duplicate-detection true \
    --duplicate-detection-history-time-window P1D
```

In de bovenstaande voorbeelden wordt ook de grootte van het duplicaatdetectievenster ingesteld met behulp van de `--duplicate-detection-history-time-window` parameter . De grootte van het venster is ingesteld op één dag. De standaardwaarde is 10 minuten en de maximaal toegestane waarde is zeven dagen.

Als **u een wachtrij met een nieuwe detectievenstergrootte wilt bijwerken,** gebruikt u de opdracht met de parameter [`az servicebus queue update`](/cli/azure/servicebus/queue#az_servicebus_queue_update) `--duplicate-detection-history-time-window` . In dit voorbeeld wordt de grootte van het venster bijgewerkt naar zeven dagen. 

```azurecli-interactive
az servicebus queue update \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name myqueue \
    --duplicate-detection-history-time-window P7D
```

Op dezelfde manier gebruikt u **de opdracht met de** parameter om een onderwerp bij te werken met een nieuwe detectievenstergrootte. [`az servicebus topic update`](/cli/azure/servicebus/topic#az_servicebus_topic_update) `--duplicate-detection-history-time-window` In dit voorbeeld wordt de grootte van het venster bijgewerkt naar zeven dagen.

```azurecli-interactive
az servicebus topic update \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name myqueue \
    --duplicate-detection-history-time-window P7D
```
 
## <a name="using-azure-powershell"></a>Azure PowerShell gebruiken
Als **u een wachtrij wilt maken met duplicaatdetectie ingeschakeld,** gebruikt u de opdracht met ingesteld op [`New-AzServiceBusQueue`](/powershell/module/az.servicebus/new-azservicebusqueue) `-RequiresDuplicateDetection` `$True` . 

```azurepowershell-interactive
New-AzServiceBusQueue -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -QueueName myqueue `
    -RequiresDuplicateDetection $True `
    -DuplicateDetectionHistoryTimeWindow P1D
```

Als **u een onderwerp wilt maken met duplicaatdetectie ingeschakeld,** gebruikt u de opdracht met ingesteld op [`New-AzServiceBusTopic`](/powershell/module/az.servicebus/new-azservicebustopic) `-RequiresDuplicateDetection` `true` . 

```azurepowershell-interactive
New-AzServiceBusTopic -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -Name mytopic `
    -RequiresDuplicateDetection $True
    -DuplicateDetectionHistoryTimeWindow P1D
```

In de bovenstaande voorbeelden wordt ook de grootte van het duplicaatdetectievenster ingesteld met behulp van de `-DuplicateDetectionHistoryTimeWindow` parameter . De grootte van het venster is ingesteld op één dag. De standaardwaarde is 10 minuten en de maximaal toegestane waarde is zeven dagen.

Zie **het volgende voorbeeld als u** een wachtrij wilt bijwerken met een nieuwe detectievenstergrootte.  In dit voorbeeld wordt de grootte van het venster bijgewerkt naar zeven dagen.

```azurepowershell-interactive
$queue=Get-AzServiceBusQueue -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -QueueName myqueue 

$queue.DuplicateDetectionHistoryTimeWindow='P7D'

Set-AzServiceBusQueue -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -QueueName myqueue `
    -QueueObj $queue
```

Zie **het volgende voorbeeld als u** een onderwerp wilt bijwerken met een nieuwe detectievenstergrootte. In dit voorbeeld wordt de grootte van het venster bijgewerkt naar zeven dagen.

```azurepowershell-interactive
$topic=Get-AzServiceBusTopic -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -Name mytopic

$topic.DuplicateDetectionHistoryTimeWindow='P7D'

Set-AzServiceBusTopic -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -Name mytopic `
    -TopicObj $topic
```

## <a name="using-azure-resource-manager-template"></a>Azure Resource Manager-sjabloon gebruiken
Als **u een wachtrij wilt maken met duplicaatdetectie ingeschakeld,** stelt u in op in de sectie `requiresDuplicateDetection` `true` Wachtrijeigenschappen. Zie [Microsoft.ServiceBus namespaces/queues template reference (Sjabloonverwijzing voor Microsoft.ServiceBus-naamruimten/wachtrijen) voor meer informatie.](/azure/templates/microsoft.servicebus/namespaces/queues?tabs=json) Geef een waarde op voor de `duplicateDetectionHistoryTimeWindow` eigenschap om de grootte van het duplicaatdetectievenster in te stellen. In het volgende voorbeeld is deze ingesteld op één dag.  

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
            "requiresDuplicateDetection": true,
            "duplicateDetectionHistoryTimeWindow": "P1D"
          }
        }
      ]
    }
  ]
}

```

Als **u een onderwerp wilt maken met detectie van duplicaten ingeschakeld,** stelt u `requiresDuplicateDetection` in op in de sectie `true` onderwerpeigenschappen. Zie [Microsoft.ServiceBus namespaces/topics template reference (Sjabloonverwijzing voor Microsoft.ServiceBus-naamruimten/onderwerpen) voor meer informatie.](/azure/templates/microsoft.servicebus/namespaces/topics?tabs=json) 

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
            "requiresDuplicateDetection": true,
            "duplicateDetectionHistoryTimeWindow": "P1D"
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