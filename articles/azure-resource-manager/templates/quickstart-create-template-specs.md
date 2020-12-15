---
title: Sjabloonspecificatie maken en implementeren
description: Meer informatie over het maken van ene sjabloonspecificatie op basis van een ARM-sjabloon. Implementeer de sjabloonspecificatie vervolgens naar een resourcegroep in uw abonnement.
author: tfitzmac
ms.date: 12/01/2020
ms.topic: quickstart
ms.author: tomfitz
ms.openlocfilehash: 03cf2013f1cec9722af5d7e72285d9f11d8a6bc1
ms.sourcegitcommit: 84e3db454ad2bccf529dabba518558bd28e2a4e6
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96518954"
---
# <a name="quickstart-create-and-deploy-template-spec-preview"></a>Quickstart: Sjabloonspecificatie maken en implementeren (preview)

In deze quickstart wordt uitgelegd hoe u een Azure Resource Manager-sjabloon (ARM-sjabloon) inpakt in een [sjabloonspecificatie](template-specs.md) en hoe u de sjabloonspecificatie vervolgens implementeert. Uw sjabloonspecificatie bevat een ARM-sjabloon die een opslagaccount implementeert.

## <a name="prerequisites"></a>Vereisten

Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

> [!NOTE]
> Sjabloonspecificaties is momenteel beschikbaar als preview-versie. Voor gebruik met Azure PowerShell moet u [versie 5.0.0 of hoger](/powershell/azure/install-az-ps) installeren. Voor gebruik met Azure CLI hebt u [versie 2.14.2 of hoger](/cli/azure/install-azure-cli) nodig.

## <a name="create-template-spec"></a>Sjabloonspecificatie maken

De sjabloonspecificatie is een resourcetype met de naam **Microsoft.Resources/templateSpecs**. U kunt Azure Portal, Azure PowerShell, Azure CLI of een ARM-sjabloon gebruiken om uw sjabloonspecificatie te maken. Voor alle opties hebt u een ARM-sjabloon nodig die is verpakt in de sjabloonspecificatie.

Voor PowerShell en de CLI wordt de ARM-sjabloon als parameter aan de opdracht doorgegeven. De ARM-sjabloon die in de sjabloonspecificatie wordt opgenomen, wordt ingesloten in de definitie van de sjabloonspecificatie.

Deze opties worden hieronder weergegeven.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Voer boven in het scherm bij **Resources, services en docs zoeken** **sjabloonspecificaties** in en selecteer **Sjabloonspecificaties** in.
1. Selecteer **Sjabloonspecificatie maken**.
1. Typ of selecteer de volgende waarden:

    - **Naam**: voer een naam in voor de sjabloonspecificatie.  Bijvoorbeeld **storageSpec**
    - **Abonnement**: selecteer een Azure-abonnement om de sjabloonspecificatie te maken.
    - **Resourcegroep**: selecteer **Nieuwe maken** en voer een naam voor de nieuwe resourcegroep in.  Bijvoorbeeld **templateSpecRG**.
    - **Locatie**: selecteer een locatie voor de resourcegroep. Bijvoorbeeld **West US 2**.
    - **Versie**: voer een versie in voor de sjabloonspecificatie. Bijvoorbeeld **1.0** of **v1.0**.

1. Selecteer **Volgende: Sjabloon bewerken**.
1. Vervang de sjablooninhoud door de volgende JSON:

    :::code language="json" source="~/quickstart-templates/101-storage-account-create/azuredeploy.json":::

    Dit is de sjabloon die in de sjabloonspecificatie wordt verpakt.
1. Selecteer **Controleren + maken**.
1. Selecteer **Maken**.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Wanneer u een sjabloonspecificatie met PowerShell maakt, kunt u een lokale sjabloon doorgeven. Kopieer de volgende sjabloon en sla deze lokaal op in een bestand met de naam **azuredeploy.json**. In deze quickstart wordt ervan uitgegaan dat u hebt opgeslagen naar het pad **c:\Templates\azuredeploy.json**, maar u kunt elk pad gebruiken.

    :::code language="json" source="~/quickstart-templates/101-storage-account-create/azuredeploy.json":::

1. Maak een nieuwe resourcegroep voor de sjabloonspecificatie.

    ```azurepowershell
    New-AzResourceGroup `
      -Name templateSpecRG `
      -Location westus2
    ```

1. Maak vervolgens de sjabloonspecificatie in die resourcegroep. U geeft de nieuwe sjabloonspecificatie de naam **storageSpec**.

    ```azurepowershell
    New-AzTemplateSpec `
      -Name storageSpec `
      -Version "1.0" `
      -ResourceGroupName templateSpecRG `
      -Location westus2 `
      -TemplateFile "c:\Templates\azuredeploy.json"
    ```

# <a name="cli"></a>[CLI](#tab/azure-cli)

1. Wanneer u een sjabloonspecificatie met CLI maakt, kunt u een lokale sjabloon doorgeven. Kopieer de volgende sjabloon en sla deze lokaal op in een bestand met de naam **azuredeploy.json**. In deze quickstart wordt ervan uitgegaan dat u hebt opgeslagen naar het pad **c:\Templates\azuredeploy.json**, maar u kunt elk pad gebruiken.

    :::code language="json" source="~/quickstart-templates/101-storage-account-create/azuredeploy.json":::

1. Maak een nieuwe resourcegroep voor de sjabloonspecificatie.

    ```azurecli
    az group create \
      --name templateSpecRG \
      --location westus2
    ```

1. Maak vervolgens de sjabloonspecificatie in die resourcegroep. U geeft de nieuwe sjabloonspecificatie de naam **storageSpec**.

    ```azurecli
    az ts create \
      --name storageSpec \
      --version "1.0" \
      --resource-group templateSpecRG \
      --location "westus2" \
      --template-file "c:\Templates\azuredeploy.json"
    ```

# <a name="arm-template"></a>[Azure VPN-gateway](#tab/azure-resource-manager)

1. Wanneer u een ARM-sjabloon gebruikt om de sjabloonspecificatie te maken, wordt de sjabloon ingesloten in de resourcedefinitie. Kopieer de volgende sjabloon en sla deze lokaal op als **azuredeploy.json**. In deze quickstart wordt ervan uitgegaan dat u hebt opgeslagen naar het pad **c:\Templates\azuredeploy.json**, maar u kunt elk pad gebruiken.

    > [!NOTE]
    > In de ingesloten sjabloon moeten alle [sjabloonexpressies](template-expressions.md) worden voorafgegaan (escaped) door een tweede vierkante haak links. Gebruik `"[[` in plaats van `"[`. JSON-matrices gebruiken nog steeds één vierkante haak links.

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {},
      "functions": [],
      "variables": {},
      "resources": [
        {
          "type": "Microsoft.Resources/templateSpecs",
          "apiVersion": "2019-06-01-preview",
          "name": "storageSpec",
          "location": "westus2",
          "properties": {
            "displayName": "Storage template spec"
          },
          "tags": {},
          "resources": [
            {
              "type": "versions",
              "apiVersion": "2019-06-01-preview",
              "name": "1.0",
              "location": "westus2",
              "dependsOn": [ "storageSpec" ],
              "properties": {
                "template": {
                  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
                  "contentVersion": "1.0.0.0",
                  "parameters": {
                    "storageAccountType": {
                      "type": "string",
                      "defaultValue": "Standard_LRS",
                      "allowedValues": [
                        "Standard_LRS",
                        "Standard_GRS",
                        "Standard_ZRS",
                        "Premium_LRS"
                      ],
                      "metadata": {
                        "description": "Storage Account type"
                      }
                    },
                    "location": {
                      "type": "string",
                      "defaultValue": "[[resourceGroup().location]",
                      "metadata": {
                        "description": "Location for all resources."
                      }
                    }
                  },
                  "variables": {
                    "storageAccountName": "[[concat('store', uniquestring(resourceGroup().id))]"
                  },
                  "resources": [
                    {
                      "type": "Microsoft.Storage/storageAccounts",
                      "apiVersion": "2019-04-01",
                      "name": "[[variables('storageAccountName')]",
                      "location": "[[parameters('location')]",
                      "sku": {
                        "name": "[[parameters('storageAccountType')]"
                      },
                      "kind": "StorageV2",
                      "properties": {}
                    }
                  ],
                  "outputs": {
                    "storageAccountName": {
                      "type": "string",
                      "value": "[[variables('storageAccountName')]"
                    }
                  }
                }
              },
              "tags": {}
            }
          ]
        }
      ],
      "outputs": {}
    }
    ```

1. Gebruik Azure CLI of PowerShell om een nieuwe resourcegroep te maken.

    ```azurepowershell
    New-AzResourceGroup `
      -Name templateSpecRG `
      -Location westus2
    ```

    ```azurecli
    az group create \
      --name templateSpecRG \
      --location westus2
    ```

1. Implementeer uw sjabloon met Azure CLI of PowerShell.

    ```azurepowershell
    New-AzResourceGroupDeployment `
      -ResourceGroupName templateSpecRG `
      -TemplateFile "c:\Templates\azuredeploy.json"
    ```

    ```azurecli
    az deployment group create \
      --name templateSpecRG \
      --template-file "c:\Templates\azuredeploy.json"
    ```

---

## <a name="deploy-template-spec"></a>Sjabloonspecificatie implementeren

U kunt nu de sjabloonspecificatie implementeren. De sjabloonspecificatie wordt op dezelfde manier geïmplementeerd als de sjabloon in de sjabloonspecificatie. Het enige verschil is dat u de resource-id van de sjabloonspecificatie in Azure PowerShell of Azure CLI doorgeeft. U gebruikt dezelfde implementatieopdrachten en geeft indien nodig de parameterwaarden voor de sjabloonspecificatie door.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Open vanuit Azure Portal de resourcegroep die u in de laatste procedure hebt gemaakt.  Bijvoorbeeld **templateSpecRG**.
1. Selecteer de sjabloonspecificatie die u hebt gemaakt. Bijvoorbeeld **storageSpec**.
1. Selecteer **Implementeren**.
1. Typ of selecteer de volgende waarden:

    - **Abonnement**: selecteer een Azure-abonnement om de resource te maken.
    - **Resourcegroep**: selecteer **Nieuwe maken** en typ **storageRG**.
    - **Type opslagaccount**: selecteer **Standard_GRS**.

    U maakt een nieuwe resourcegroep en implementeert de sjabloon in de sjabloonspecificatie voor de nieuwe resourcegroep.

1. Selecteer **Controleren + maken**.
1. Selecteer **Maken**.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Eerst maakt u een resourcegroep die het nieuwe opslagaccount bevat.

    ```azurepowershell
    New-AzResourceGroup `
      -Name storageRG `
      -Location westus2
    ```

1. Haal de resource-id op van de sjabloonspecificatie.

    ```azurepowershell
    $id = (Get-AzTemplateSpec -ResourceGroupName templateSpecRG -Name storageSpec -Version "1.0").Versions.Id
    ```

1. Implementeer de sjabloonspecificatie.

    ```azurepowershell
    New-AzResourceGroupDeployment `
      -TemplateSpecId $id `
      -ResourceGroupName storageRG
    ```

1. U moet de parameters opgeven op precies dezelfde manier als voor een ARM-sjabloon. Implementeer de sjabloonspecificatie opnieuw met een parameter voor het type opslagaccount.

    ```azurepowershell
    New-AzResourceGroupDeployment `
      -TemplateSpecId $id `
      -ResourceGroupName storageRG `
      -storageAccountType Standard_GRS
    ```

# <a name="cli"></a>[CLI](#tab/azure-cli)

1. Eerst maakt u een resourcegroep die het nieuwe opslagaccount bevat.

    ```azurecli
    az group create \
      --name storageRG \
      --location westus2
    ```

1. Haal de resource-id op van de sjabloonspecificatie.

    ```azurecli
    id = $(az ts show --name storageSpec --resource-group templateSpecRG --version "1.0" --query "id")
    ```

    > [!NOTE]
    > Er is een bekend probleem met het ophalen van een sjabloonspecificatie-id en het toewijzen ervan aan een variabele in Windows PowerShell.

1. Implementeer de sjabloonspecificatie.

    ```azurecli
    az deployment group create \
      --resource-group storageRG \
      --template-spec $id
    ```

1. U moet de parameters opgeven op precies dezelfde manier als voor een ARM-sjabloon. Implementeer de sjabloonspecificatie opnieuw met een parameter voor het type opslagaccount.

    ```azurecli
    az deployment group create \
      --resource-group storageRG \
      --template-spec $id \
      --parameters storageAccountType='Standard_GRS'
    ```

# <a name="arm-template"></a>[Azure VPN-gateway](#tab/azure-resource-manager)

1. Kopieer de volgende sjabloon en sla deze lokaal op in een bestand met de naam **storage.json**.

    ```json
       {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {},
      "functions": [],
      "variables": {},
      "resources": [
        {
          "type": "Microsoft.Resources/deployments",
          "apiVersion": "2020-06-01",
          "name": "demo",
          "properties": {
            "templateLink": {
              "id": "[resourceId('templateSpecRG', 'Microsoft.Resources/templateSpecs/versions', 'storageSpec', '1.0')]"
            },
            "parameters": {
            },
            "mode": "Incremental"
          }
        }
      ],
      "outputs": {}
    }
    ```

1. Gebruik Azure CLI of PowerShell om een nieuwe resourcegroep te maken voor het opslagaccount.

    ```azurepowershell
    New-AzResourceGroup `
      -Name storageRG `
      -Location westus2
    ```

    ```azurecli
    az group create \
      --name storageRG \
      --location westus2
    ```

1. Implementeer uw sjabloon met Azure CLI of PowerShell.

    ```azurepowershell
    New-AzResourceGroupDeployment `
      -ResourceGroupName storageRG `
      -TemplateFile "c:\Templates\storage.json"
    ```

    ```azurecli
    az deployment group create \
      --name storageRG \
      --template-file "c:\Templates\storage.json"
    ```

---

## <a name="grant-access"></a>Toegang verlenen

Als u wilt dat andere gebruikers in uw organisatie uw sjabloonspecificatie implementeren, moet u hen leestoegang verlenen. U kunt de rol van lezer toewijzen aan een Azure AD-groep voor de resourcegroep die de sjabloonspecificaties bevat die u wilt delen. Zie [Zelfstudie: Verleen een groep toegang tot Azure-resources met behulp van Azure PowerShell](../../role-based-access-control/tutorial-role-assignments-group-powershell.md).

## <a name="clean-up-resources"></a>Resources opschonen

Als u de resource wilt opschonen die u in deze quickstart hebt geïmplementeerd, verwijdert u beide resourcegroepen die u hebt gemaakt.

1. Selecteer Resourcegroep in het linkermenu van Azure Portal.

1. Voer in het veld Filteren op naam de naam van de resourcegroep (templateSpecRG en storageRG) in.

1. Selecteer de naam van de resourcegroep.

1. Selecteer Resourcegroep verwijderen in het bovenste menu.

## <a name="next-steps"></a>Volgende stappen

Zie [Een sjabloonspecificatie van een gekoppelde sjabloon maken](template-specs-create-linked.md) voor meer informatie over het maken van een sjabloonspecificatie die gekoppelde sjablonen bevat.
