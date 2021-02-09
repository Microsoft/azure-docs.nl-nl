---
title: 'Quickstart: een gedeelde query maken met Azure PowerShell'
description: In deze quickstart volgt u de stappen voor het maken van een gedeelde Resource Graph-query met behulp van Azure PowerShell.
ms.date: 01/11/2021
ms.topic: quickstart
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 2b771253b1dea4bd1d2913bf7c48062112019a19
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/09/2021
ms.locfileid: "99981541"
---
# <a name="quickstart-create-a-resource-graph-shared-query-using-azure-powershell"></a>Quickstart: Een gedeelde Resource Graph-query maken met behulp van Azure PowerShell

In dit artikel wordt beschreven hoe u een gedeelde Azure Resource Graph-query kunt maken met behulp van de PowerShell-module [Az.ResourceGraph](/powershell/module/az.resourcegraph).

## <a name="prerequisites"></a>Vereisten

- Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

[!INCLUDE [azure-powershell-requirements-no-header.md](../../../includes/azure-powershell-requirements-no-header.md)]

  > [!IMPORTANT]
  > Zolang de PowerShell-module **Az.ResourceGraph** zich in preview bevindt, moet u deze afzonderlijk installeren met behulp van de cmdlet `Install-Module`.

  ```azurepowershell-interactive
  Install-Module -Name Az.ResourceGraph
  ```

- Als u meerdere Azure-abonnementen hebt, kiest u het juiste abonnement waarin de resource moet worden gefactureerd. Selecteer een specifiek abonnement met de cmdlet [set-AzContext](/powershell/module/az.accounts/set-azcontext).

  ```azurepowershell-interactive
  Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
  ```

## <a name="create-a-resource-graph-shared-query"></a>Een gedeelde Resource Graph-query maken

Als de Power shell-module **AZ. ResourceGraph** is toegevoegd aan uw omgeving van Choice, is het tijd om een gedeelde query voor resource Graph te maken. De gedeelde query is een Azure Resource Manager-object waaraan u machtigingen kunt verlenen of dat u kunt uitvoeren in Azure Resource Graph Explorer. De query biedt een overzicht van het aantal resources, gegroepeerd op _locatie_.

1. Maak een resourcegroep met [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) om de gedeelde Azure Resource Graph-query op te slaan. Deze resourcegroep heet `resource-graph-queries` en de locatie is `westus2`.

   ```azurepowershell-interactive
   # Login first with `Connect-AzAccount` if not using Cloud Shell

   # Create the resource group
   New-AzResourceGroup -Name resource-graph-queries -Location westus2
   ```

1. Maak de gedeelde Azure resource Graph-query met de Power shell-module **AZ. ResourceGraph** en de cmdlet [New-AzResourceGraphQuery](/powershell/module/az.resourcegraph/new-azresourcegraphquery) :

   ```azurepowershell-interactive
   # Create the Azure Resource Graph shared query
   $Params = @{
     Name = 'Summarize resources by location'
     ResourceGroupName = 'resource-graph-queries'
     Location = 'westus2'
     Description = 'This shared query summarizes resources by location for a pinnable map graphic.'
     Query = 'Resources | summarize count() by location'
   }
   New-AzResourceGraphQuery @Params
   ```

1. Geef de gedeelde query's op in de nieuwe resourcegroep. De cmdlet [Get-AzResourceGraphQuery](/powershell/module/az.resourcegraph/get-azresourcegraphquery) retourneert een matrix met waarden.

   ```azurepowershell-interactive
   # List all the Azure Resource Graph shared queries in a resource group
   Get-AzResourceGraphQuery -ResourceGroupName resource-graph-queries
   ```

1. Als u slechts één gedeeld queryresultaat wilt ophalen, gebruikt u `Get-AzResourceGraphQuery` met de bijbehorende parameter `Name`.

   ```azurepowershell-interactive
   # Show a specific Azure Resource Graph shared query
   Get-AzResourceGraphQuery -ResourceGroupName resource-graph-queries -Name 'Summarize resources by location'
   ```

## <a name="clean-up-resources"></a>Resources opschonen

Als u de gedeelde Resource Graph-query en resourcegroep uit uw Azure-omgeving wilt verwijderen, gebruikt u de volgende opdracht:

- [Remove-AzResourceGraphQuery](/powershell/module/az.resourcegraph/remove-azresourcegraphquery)
- [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup)

```azurepowershell-interactive
# Delete the Azure Resource Graph shared query
Remove-AzResourceGraphQuery -ResourceGroupName resource-graph-queries -Name 'Summarize resources by location'

# Remove the resource group
# WARNING: This command deletes ALL resources you've added to this resource group
Remove-AzResourceGroup -Name resource-graph-queries
```

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een gedeelde Resource Graph-query gemaakt met behulp van Azure PowerShell. Ga door naar de pagina met details van de querytaal voor meer informatie over de taal van Resource Graph.

> [!div class="nextstepaction"]
> [Meer informatie over de querytaal](./concepts/query-language.md)