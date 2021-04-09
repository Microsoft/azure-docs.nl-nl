---
title: Toegangs sleutel voor een Event Grid bron ophalen
description: In dit artikel wordt beschreven hoe u toegang krijgt tot een sleutel voor een Event Grid onderwerp of domein
ms.topic: how-to
ms.date: 07/07/2020
ms.openlocfilehash: a642affbac79766684dc75a37dae0373450d20e8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98632526"
---
# <a name="get-access-keys-for-event-grid-resources-topics-or-domains"></a>Toegangs sleutels voor Event Grid resources (onderwerpen of domeinen) ophalen
Toegangs sleutels worden gebruikt voor het verifiëren van het publiceren van een toepassing op Azure Event Grid resources (onderwerpen en domeinen). Het is raadzaam om uw sleutels regel matig opnieuw te genereren en ze veilig op te slaan. U krijgt twee toegangs sleutels zodat u verbindingen met één sleutel kunt onderhouden tijdens het opnieuw genereren van de andere.

In dit artikel wordt beschreven hoe u toegangs sleutels voor een Event Grid bron (onderwerp of domein) kunt ophalen met behulp van Azure Portal, Power shell of CLI. 

## <a name="azure-portal"></a>Azure Portal
Ga in het Azure Portal naar het tabblad **toegangs sleutels** van het **onderwerp Event grid** of **Event grid domein** pagina voor uw onderwerp of domein.  

:::image type="content" source="./media/get-access-keys/azure-portal.png" alt-text="Pagina toegangs sleutels":::

## <a name="azure-powershell"></a>Azure PowerShell
Gebruik de opdracht [Get-AzEventGridTopicKey](/powershell/module/az.eventgrid/get-azeventgridtopickey) voor het ophalen van toegangs sleutels voor onderwerpen. 

```azurepowershell-interactive
Get-AzEventGridTopicKey -ResourceGroup <RESOURCE GROUP NAME> -Name <TOPIC NAME>
```

Gebruik de opdracht [Get-AzEventGridDomainKey](/powershell/module/az.eventgrid/get-azeventgriddomainkey) om toegangs sleutels voor domeinen op te halen. 

```azurepowershell-interactive
Get-AzEventGridDomainKey -ResourceGroup <RESOURCE GROUP NAME> -Name <DOMAIN NAME>
```

## <a name="azure-cli"></a>Azure CLI
Gebruik de [sleutel lijst AZ eventgrid topic](/cli/azure/eventgrid/topic/key#az-eventgrid-topic-key-list) om toegangs sleutels voor onderwerpen op te halen. 

```azurecli-interactive
az eventgrid topic key list --resource-group <RESOURCE GROUP NAME> --name <TOPIC NAME>
```

Gebruik [AZ eventgrid Domain Key List](/cli/azure/eventgrid/domain/key#az-eventgrid-domain-key-list) om toegangs sleutels voor domeinen op te halen. 

```azurecli-interactive
az eventgrid domain key list --resource-group <RESOURCE GROUP NAME> --name <DOMAIN NAME>
```

## <a name="next-steps"></a>Volgende stappen
Zie het volgende artikel: [Publishing-clients verifiëren](security-authenticate-publishing-clients.md). 
