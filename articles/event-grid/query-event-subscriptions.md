---
title: Query's uitvoeren op Azure Event Grid abonnementen
description: In dit artikel wordt beschreven hoe u Event Grid abonnementen in uw Azure-abonnement kunt weer geven. U kunt verschillende para meters opgeven op basis van het type abonnement.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 3d700f543bc5e3c7add2a346c10acf975e1c2462
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "86120446"
---
# <a name="query-event-grid-subscriptions"></a>Query's uitvoeren op Event Grid abonnementen 

In dit artikel wordt beschreven hoe u de Event Grid-abonnementen in uw Azure-abonnement kunt weer geven. Wanneer u een query uitvoert op uw bestaande Event Grid-abonnementen, is het belang rijk dat u de verschillende soorten abonnementen begrijpt. U kunt verschillende para meters opgeven op basis van het type abonnement dat u wilt ophalen.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="resource-groups-and-azure-subscriptions"></a>Resource groepen en Azure-abonnementen

Azure-abonnementen en resource groepen zijn geen Azure-resources. Daarom hebben Event grid-abonnementen op resource groepen of Azure-abonnementen niet dezelfde eigenschappen als Event grid-abonnementen op Azure-resources. Event grid-abonnementen op resource groepen of Azure-abonnementen worden beschouwd als globaal.

Als u Event grid-abonnementen wilt ophalen voor een Azure-abonnement en de bijbehorende resource groepen, hoeft u geen para meters op te geven. Zorg ervoor dat u het Azure-abonnement hebt geselecteerd dat u wilt doorzoeken. In de volgende voor beelden worden geen Event grid-abonnementen voor aangepaste onderwerpen of Azure-resources weer gegeven.

Gebruik voor Azure CLI:

```azurecli-interactive
az account set -s "My Azure Subscription"
az eventgrid event-subscription list
```

Gebruik voor PowerShell:

```azurepowershell-interactive
Set-AzContext -Subscription "My Azure Subscription"
Get-AzEventGridSubscription
```

Als u Event grid-abonnementen wilt ophalen voor een Azure-abonnement, geeft u het onderwerp type **micro soft. resources. abonnementen** op.

Gebruik voor Azure CLI:

```azurecli-interactive
az eventgrid event-subscription list --topic-type-name "Microsoft.Resources.Subscriptions" --location global
```

Gebruik voor PowerShell:

```azurepowershell-interactive
Get-AzEventGridSubscription -TopicTypeName "Microsoft.Resources.Subscriptions"
```

Als u Event grid-abonnementen wilt ophalen voor alle resource groepen binnen een Azure-abonnement, geeft u het onderwerp type **micro soft. resources. ResourceGroups** op.

Gebruik voor Azure CLI:

```azurecli-interactive
az eventgrid event-subscription list --topic-type-name "Microsoft.Resources.ResourceGroups" --location global
```

Gebruik voor PowerShell:

```azurepowershell-interactive
Get-AzEventGridSubscription -TopicTypeName "Microsoft.Resources.ResourceGroups"
```

Als u Event grid-abonnementen wilt ophalen voor een opgegeven resource groep, geeft u de naam van de resource groep op als para meter.

Gebruik voor Azure CLI:

```azurecli-interactive
az eventgrid event-subscription list --resource-group myResourceGroup --location global
```

Gebruik voor PowerShell:

```azurepowershell-interactive
Get-AzEventGridSubscription -ResourceGroupName myResourceGroup
```

## <a name="custom-topics-and-azure-resources"></a>Aangepaste onderwerpen en Azure-resources

Aangepaste onderwerpen over Event grid zijn Azure-resources. Daarom zoekt u Event grid-abonnementen voor aangepaste onderwerpen en andere resources, zoals Blob Storage-account, op dezelfde manier. Als u Event grid-abonnementen voor aangepaste onderwerpen wilt ophalen, moet u para meters opgeven die de resource identificeren of de locatie van de resource identificeren. Het is niet mogelijk om op grote schaal een query uit te zoeken op Event grid-abonnementen voor resources in uw Azure-abonnement.

Als u Event grid-abonnementen voor aangepaste onderwerpen en andere resources op een locatie wilt ophalen, geeft u de naam van de locatie op.

Gebruik voor Azure CLI:

```azurecli-interactive
az eventgrid event-subscription list --location westus2
```

Gebruik voor PowerShell:

```azurepowershell-interactive
Get-AzEventGridSubscription -Location westus2
```

Als u abonnementen op aangepaste onderwerpen voor een locatie wilt ophalen, geeft u de locatie en het type onderwerp van **micro soft. EventGrid. topics**.

Gebruik voor Azure CLI:

```azurecli-interactive
az eventgrid event-subscription list --topic-type-name "Microsoft.EventGrid.Topics" --location "westus2"
```

Gebruik voor PowerShell:

```azurepowershell-interactive
Get-AzEventGridSubscription -TopicTypeName "Microsoft.EventGrid.Topics" -Location westus2
```

Als u abonnementen voor opslag accounts voor een locatie wilt ophalen, geeft u de locatie en het type onderwerp van **micro soft. storage. Storage accounts** op.

Gebruik voor Azure CLI:

```azurecli-interactive
az eventgrid event-subscription list --topic-type "Microsoft.Storage.StorageAccounts" --location westus2
```

Gebruik voor PowerShell:

```azurepowershell-interactive
Get-AzEventGridSubscription -TopicTypeName "Microsoft.Storage.StorageAccounts" -Location westus2
```

Als u Event grid-abonnementen voor een aangepast onderwerp wilt ophalen, geeft u de naam van het aangepaste onderwerp en de naam van de bijbehorende resource groep op.

Gebruik voor Azure CLI:

```azurecli-interactive
az eventgrid event-subscription list --topic-name myCustomTopic --resource-group myResourceGroup
```

Gebruik voor PowerShell:

```azurepowershell-interactive
Get-AzEventGridSubscription -TopicName myCustomTopic -ResourceGroupName myResourceGroup
```

Als u Event grid-abonnementen voor een bepaalde resource wilt ophalen, geeft u de resource-ID op.

Gebruik voor Azure CLI:

```azurecli-interactive
resourceid=$(az resource show -n mystorage -g myResourceGroup --resource-type "Microsoft.Storage/storageaccounts" --query id --output tsv)
az eventgrid event-subscription list --resource-id $resourceid
```

Gebruik voor PowerShell:

```azurepowershell-interactive
$resourceid = (Get-AzResource -Name mystorage -ResourceGroupName myResourceGroup).ResourceId
Get-AzEventGridSubscription -ResourceId $resourceid
```

## <a name="next-steps"></a>Volgende stappen

* [Event grid aflevering van berichten en probeer het opnieuw](delivery-and-retry.md).
* Zie [Een inleiding tot Event Grid](overview.md) voor een inleiding tot Event Grid.
* Zie [aangepaste gebeurtenissen maken en routeren met Azure Event grid](custom-event-quickstart.md)om snel aan de slag te gaan met Event grid.
