---
title: Toegangssleutel voor een Event Grid krijgen
description: In dit artikel wordt beschreven hoe u een toegangssleutel voor een Event Grid onderwerp of domein op kunt halen
ms.topic: how-to
ms.date: 07/07/2020
ms.openlocfilehash: cd60777b2e28b82d72f8f2bf93fe0be301e9e280
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107775219"
---
# <a name="get-access-keys-for-event-grid-resources-topics-or-domains"></a>Toegangssleutels voor Event Grid resources (onderwerpen of domeinen) ophalen
Toegangssleutels worden gebruikt voor het verifiëren van gebeurtenissen voor het publiceren van toepassingen Azure Event Grid resources (onderwerpen en domeinen). We raden u aan uw sleutels regelmatig opnieuw te gebruiken en ze veilig op te slaan. U krijgt twee toegangssleutels zodat u verbindingen met één sleutel kunt onderhouden tijdens het opnieuw maken van de andere sleutel.

In dit artikel wordt beschreven hoe u toegangssleutels voor een Event Grid resource (onderwerp of domein) op kunt halen met Azure Portal, PowerShell of CLI. 

## <a name="azure-portal"></a>Azure Portal
Ga in Azure Portal naar het  tabblad Toegangssleutels van Event Grid **onderwerp** of **Event Grid domein voor** uw onderwerp of domein.  

:::image type="content" source="./media/get-access-keys/azure-portal.png" alt-text="Pagina Toegangssleutels":::

## <a name="azure-powershell"></a>Azure PowerShell
Gebruik de [opdracht Get-AzEventGridTopicKey om](/powershell/module/az.eventgrid/get-azeventgridtopickey) toegangssleutels voor onderwerpen op te halen. 

```azurepowershell-interactive
Get-AzEventGridTopicKey -ResourceGroup <RESOURCE GROUP NAME> -Name <TOPIC NAME>
```

Gebruik [de opdracht Get-AzEventGridDomainKey](/powershell/module/az.eventgrid/get-azeventgriddomainkey) om toegangssleutels voor domeinen op te halen. 

```azurepowershell-interactive
Get-AzEventGridDomainKey -ResourceGroup <RESOURCE GROUP NAME> -Name <DOMAIN NAME>
```

## <a name="azure-cli"></a>Azure CLI
Gebruik de [az eventgrid topic key list om](/cli/azure/eventgrid/topic/key#az_eventgrid_topic_key_list) toegangssleutels voor onderwerpen op te halen. 

```azurecli-interactive
az eventgrid topic key list --resource-group <RESOURCE GROUP NAME> --name <TOPIC NAME>
```

Gebruik [az eventgrid domain key list om](/cli/azure/eventgrid/domain/key#az_eventgrid_domain_key_list) toegangssleutels voor domeinen op te halen. 

```azurecli-interactive
az eventgrid domain key list --resource-group <RESOURCE GROUP NAME> --name <DOMAIN NAME>
```

## <a name="next-steps"></a>Volgende stappen
Zie het volgende artikel: [Publicatie van clients verifiëren.](security-authenticate-publishing-clients.md) 
