---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/18/2020
ms.author: glenga
ms.openlocfilehash: 99ae35aca485ac928f7c5ef9f98295eed4bc1245
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/03/2021
ms.locfileid: "99500107"
---
## <a name="create-supporting-azure-resources-for-your-function"></a>Ondersteunende Azure-resources maken voor uw functie

Voordat u uw functiecode kunt implementeren in Azure, moet u drie resources maken:

- Een [resource groep](../articles/azure-resource-manager/management/overview.md), een logische container voor gerelateerde resources.
- Een [opslag account](../articles/storage/common/storage-account-create.md), dat wordt gebruikt om de status en andere informatie over uw functies te onderhouden.
- Een functie-app, die de omgeving biedt voor het uitvoeren van uw functiecode. Een functie-app wordt gekoppeld aan uw lokale functieproject en maakt het mogelijk om functies te groeperen als een logische eenheid, zodat u resources eenvoudiger kunt beheren, implementeren en delen.

Gebruik de volgende opdrachten om deze items te maken. Zowel Azure CLI als PowerShell worden ondersteund.

1. Als u dit nog niet hebt gedaan, meldt u zich aan bij Azure:

    # <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)
    ```azurecli
    az login
    ```

    Met de opdracht [az login](/cli/azure/reference-index#az_login) meldt u zich aan bij uw Azure-account.

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell) 
    ```azurepowershell
    Connect-AzAccount
    ```

    Met de cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) meldt u zich aan bij uw Azure-account.

    ---

1. Maak een resourcegroep met de naam `AzureFunctionsQuickstart-rg` in de regio `westeurope`:

    # <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)
    
    ```azurecli
    az group create --name AzureFunctionsQuickstart-rg --location westeurope
    ```
 
    Met de opdracht [az group create](/cli/azure/group#az_group_create) maakt u een resourcegroep. Over het algemeen maakt u een resourcegroep en resources in een regio bij u in de buurt, waarbij u een beschikbare regio gebruikt die wordt geretourneerd door de opdracht `az account list-locations`.

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

    ```azurepowershell
    New-AzResourceGroup -Name AzureFunctionsQuickstart-rg -Location westeurope
    ```

    Met de opdracht [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) maakt u een resourcegroep. Over het algemeen maakt u een resourcegroep en resources in een regio bij u in de buurt, waarbij u een beschikbare regio gebruikt die wordt geretourneerd door de cmdlet [Get-AzLocation](/powershell/module/az.resources/get-azlocation).

    ---

1. Maak een algemeen opslagaccount in de resourcegroep en regio:

    # <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

    ```azurecli
    az storage account create --name <STORAGE_NAME> --location westeurope --resource-group AzureFunctionsQuickstart-rg --sku Standard_LRS
    ```

    Met de opdracht [az storage account create](/cli/azure/storage/account#az_storage_account_create) maakt u het opslagaccount. 

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

    ```azurepowershell
    New-AzStorageAccount -ResourceGroupName AzureFunctionsQuickstart-rg -Name <STORAGE_NAME> -SkuName Standard_LRS -Location westeurope
    ```

    Met de cmdlet [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) maakt u het opslagaccount.

    ---

    Vervang `<STORAGE_NAME>` in het vorige voorbeeld door een naam die voor u passend is en die uniek is in Azure Storage. Namen mogen drie tot 24 tekens bevatten en u mag alleen kleine letters gebruiken. Met `Standard_LRS` geeft u een account voor algemeen gebruik op dat wordt [ondersteund door Functions](../articles/azure-functions/storage-considerations.md#storage-account-requirements).
