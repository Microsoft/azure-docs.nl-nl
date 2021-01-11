---
author: msftradford
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 11/20/2020
ms.author: parkerra
ms.openlocfilehash: 596b73f8fb205b6a5681fecf3d00fd2a67c1f59f
ms.sourcegitcommit: 86acfdc2020e44d121d498f0b1013c4c3903d3f3
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/17/2020
ms.locfileid: "97628675"
---
## <a name="create-a-spatial-anchors-resource"></a>Een Spatial Anchors-resource maken

### <a name="portal"></a>[Portal](#tab/azure-portal)

Ga naar de <a href="https://portal.azure.com" target="_blank">Azure Portal</a>.

Selecteer **Een resource maken** in het linkerdeelvenster.

Gebruik het zoekvak om te zoeken naar **Spatial Anchors**.

![Schermopname met de resultaten van een zoekopdracht naar Spatial Anchors.](./media/spatial-anchors-get-started-create-resource/portal-search.png)

Selecteer **Spatial Anchors** en vervolgens **Maken**.

Doe in het deelvenster **Spatial Anchors-account** het volgende:

* Voer een unieke resourcenaam in met gewone alfanumerieke tekens.
* Selecteer het abonnement waaraan u de resource wilt koppelen.
* Maak een resourcegroep door **Nieuwe maken** te selecteren. Noem deze **myResourceGroup** en selecteer **OK**.

  [!INCLUDE [resource group intro text](resource-group.md)]

* Selecteer de locatie (regio) waarin u de resource wilt plaatsen.
* Selecteer **Nieuw** om te beginnen met het maken van de resource.

![Schermopname van het deelvenster Spatial Anchors voor het maken van een resource.](./media/spatial-anchors-get-started-create-resource/create-resource-form.png)

Nadat de resource is gemaakt, ziet u in de Azure-portal dat uw implementatie is voltooid.

![Schermopname die laat zien dat de resource-implementatie is voltooid.](./media/spatial-anchors-get-started-create-resource/deployment-complete.png)

Selecteer **Naar resource**. Nu kunt u de resource-eigenschappen bekijken.

Kopieer de waarde bij **Account-id** van de resource naar een teksteditor om later te gebruiken.

![Schermopname van het deelvenster met resource-eigenschappen.](./media/spatial-anchors-get-started-create-resource/view-resource-properties.png)

Kopieer ook de waarde bij **Accountdomein** van de resource naar een teksteditor om later te gebruiken.

![Schermopname van de accountdomeinwaarde van de resource.](./media/spatial-anchors-get-started-create-resource/view-resource-domain.png)

Selecteer onder **Instellingen** de optie **Sleutel**. Kopieer de waarde bij **Primaire sleutel**, **Accountsleutel**, naar een teksteditor om later te gebruiken.

![Schermopname van het deelvenster Sleutels voor het account.](./media/spatial-anchors-get-started-create-resource/view-account-key.png)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Begin door de omgeving voor te bereiden op de Azure CLI:

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](azure-cli-prepare-your-environment-no-header.md)]

1. Nadat u zich hebt aangemeld, gebruikt u de opdracht [az account set](/cli/azure/account#az_account_set) om het abonnement te selecteren waarin u het account voor ruimtelijke ankers wilt instellen:

   ```azurecli
   az account set --subscription 00000000-0000-0000-0000-000000000000
   ```

1. Voer de opdracht [az group create](/cli/azure/group#az_group_create) uit om een resourcegroep te maken, of gebruik een bestaande resourcegroep:

   ```azurecli
   az group create --name myResourceGroup --location eastus2
   ```

   [!INCLUDE [resource group intro text](resource-group.md)]

   U kunt uw huidige accounts voor ruimtelijke ankers voor een resourcegroep weergeven met behulp van de opdracht [az spatial-anchors-account list](/cli/azure/ext/mixed-reality/spatial-anchors-account#ext_mixed_reality_az_spatial_anchors_account_list):

   ```azurecli
   az spatial-anchors-account list --resource-group myResourceGroup
   ```

   U kunt ook de accounts voor ruimtelijke ankers voor uw abonnement bekijken:

   ```azurecli
   az spatial-anchors-account list
   ```

1. Voer de opdracht [az spatial-anchors-account create](/cli/azure/ext/mixed-reality/spatial-anchors-account#ext_mixed_reality_az_spatial_anchors_account_create) uit om een account voor ruimtelijke ankers te maken:

   ```azurecli
   az spatial-anchors-account create --resource-group myResourceGroup --name MySpatialAnchorsQuickStart --location eastus2
   ```

1. Geef de resource-eigenschappen weer met behulp van de opdracht [az spatial-anchors-account show](/cli/azure/ext/mixed-reality/spatial-anchors-account#ext_mixed_reality_az_spatial_anchors_account_show):

   ```azurecli
   az spatial-anchors-account show --resource-group myResourceGroup --name MySpatialAnchorsQuickStart
   ```

   Kopieer de waarde voor **Account-id** van de resource en de waarde voor **Accountdomein** van de resource naar een teksteditor voor later gebruik.

1. Voer de opdracht [az spatial-anchors-account key show](/cli/azure/ext/mixed-reality/spatial-anchors-account/key#ext_mixed_reality_az_spatial_anchors_account_key_show) uit om uw primaire en secundaire sleutels op te halen:

   ```azurecli
   az spatial-anchors-account key show --resource-group myResourceGroup --name MySpatialAnchorsQuickStart
   ```

   Kopieer de sleutelwaarden naar een teksteditor voor later gebruik.

   Als u sleutels opnieuw wilt genereren, gebruikt u de opdracht [az spatial-anchors-account key renew](/cli/azure/ext/mixed-reality/spatial-anchors-account/key#ext_mixed_reality_az_spatial_anchors_account_key_renew):

   ```azurecli
   az spatial-anchors-account key renew --resource-group myResourceGroup --name example --key primary
   az spatial-anchors-account key renew --resource-group myResourceGroup --name example --key secondary
   ```

U kunt een account verwijderen met de opdracht [az spatial-anchors-account delete](/cli/azure/ext/mixed-reality/spatial-anchors-account#ext_mixed_reality_az_spatial_anchors_account_delete):

```azurecli
az spatial-anchors-account delete --resource-group myResourceGroup --name MySpatialAnchorsQuickStart
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Begin door de omgeving voor te bereiden op Azure PowerShell:

[!INCLUDE [azure-powershell-requirements-no-header.md](azure-powershell-requirements-no-header.md)]

> [!IMPORTANT]
> Zolang de PowerShell-module **Az.MixedReality** in de preview-fase is, moet u deze afzonderlijk installeren met behulp van de cmdlet `Install-Module`. Nadat de PowerShell-module algemeen beschikbaar is geworden, wordt deze onderdeel van toekomstige releases van de Az PowerShell-module en is deze standaard beschikbaar vanuit Azure Cloud Shell.

```azurepowershell-interactive
Install-Module -Name Az.MixedReality
```

1. Nadat u zich hebt aangemeld, gebruikt u de cmdlet [Set-AzContext](/powershell/module/az.accounts/set-azcontext) om het abonnement te selecteren waarin u het ruimtelijke-ankersaccount wilt instellen:

   ```azurepowershell-interactive
   Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
   ```

1. Voer de cmdlet [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) uit om een resourcegroep te maken, of gebruik een bestaande resourcegroep:

   ```azurepowershell-interactive
   New-AzResourceGroup -Name myResourceGroup -Location eastus2
   ```

   [!INCLUDE [resource group intro text](resource-group.md)]

   U kunt uw huidige ruimtelijke-ankersaccounts voor een resourcegroep weergeven met behulp van de cmdlet [Get-AzSpatialAnchorsAccount](/powershell/module/az.mixedreality/get-azspatialanchorsaccount):

   ```azurepowershell-interactive
   Get-AzSpatialAnchorsAccount -ResourceGroup myResourceGroup
   ```

   U kunt ook de accounts voor ruimtelijke ankers voor uw abonnement bekijken:

   ```azurepowershell-interactive
   Get-AzSpatialAnchorsAccount
   ```

1. Voer de cmdlet [New-AzSpatialAnchorsAccount](/powershell/module/az.mixedreality/new-azspatialanchorsaccount) uit om uw ruimtelijke-ankersaccount te maken:

   ```azurepowershell-interactive
   New-AzSpatialAnchorsAccount -ResourceGroup myResourceGroup -Name MySpatialAnchorsQuickStart -Location eastus2
   ```

1. Bekijk de resource-eigenschappen met behulp van de cmdlet [Get-AzSpatialAnchorsAccount](/powershell/module/az.mixedreality/get-azspatialanchorsaccount):

   ```azurepowershell-interactive
   Get-AzSpatialAnchorsAccount -ResourceGroup myResourceGroup -Name MySpatialAnchorsQuickStart
   ```

   Kopieer de waarden voor de eigenschappen **accountId** en **accountDomain** in een teksteditor, voor later gebruik.

1. Voer de cmdlet [Get-AzSpatialAnchorsAccountKey](/powershell/module/az.mixedreality/get-azspatialanchorsaccountkey) uit om uw primaire en secundaire sleutels uit te voeren:

   ```azurepowershell-interactive
   Get-AzSpatialAnchorsAccountKey -ResourceGroup myResourceGroup -Name MySpatialAnchorsQuickStart
   ```

   Kopieer de sleutelwaarden naar een teksteditor voor later gebruik.

   Als u sleutels opnieuw wilt genereren, gebruikt u de cmdlet [New-AzSpatialAnchorsAccountKey](/powershell/module/az.mixedreality/new-azspatialanchorsaccountkey):

   ```azurepowershell-interactive
   New-AzSpatialAnchorsAccountKey -ResourceGroupName myResourceGroup -Name MySpatialAnchorsQuickStart -Primary
   New-AzSpatialAnchorsAccountKey -ResourceGroupName myResourceGroup -Name MySpatialAnchorsQuickStart -Secondary
   ```

U kunt een account verwijderen met behulp van cmdlet [Remove-AzSpatialAnchorsAccount](/powershell/module/az.mixedreality/remove-azspatialanchorsaccount):

```azurepowershell-interactive
Remove-AzSpatialAnchorsAccount -ResourceGroup myResourceGroup -Name MySpatialAnchorsQuickStart
```

---
