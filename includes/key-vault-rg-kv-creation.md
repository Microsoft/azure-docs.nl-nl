---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin
ms.openlocfilehash: 6d6c4a019e9b0b9bd4ed52990fa8b59e939026f1
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "106073087"
---
In deze quickstart wordt gebruikgemaakt van een vooraf gemaakte Azure-sleutelkluis. U kunt een sleutelkluis maken met behulp van de stappen in de [quickstart voor Azure CLI](../articles/key-vault/general/quick-create-cli.md), de [quickstart voor Azure PowerShell](../articles/key-vault/general/quick-create-powershell.md) of de [quickstart voor de Azure-portal](../articles/key-vault/general/quick-create-portal.md). 

U kunt ook de onderstaande Azure CLI- of Azure PowerShell-opdrachten uitvoeren.

> [!Important]
> Elke sleutelkluis moet een unieke naam hebben. Vervang <your-unique-keyvault-name> door de naam van uw sleutelkluis in de volgende voorbeelden.

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)
```azurecli
az group create --name "myResourceGroup" -l "EastUS"

az keyvault create --name "<your-unique-keyvault-name>" -g "myResourceGroup"
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
New-AzResourceGroup -Name myResourceGroup -Location eastus

New-AzKeyVault -Name <your-unique-keyvault-name> -ResourceGroupName myResourceGroup -Location eastus
```
---