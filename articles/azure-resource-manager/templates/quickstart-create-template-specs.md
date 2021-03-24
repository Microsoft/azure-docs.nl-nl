---
title: Sjabloonspecificatie maken en implementeren
description: Meer informatie over het maken van ene sjabloonspecificatie op basis van een ARM-sjabloon. Implementeer de sjabloonspecificatie vervolgens naar een resourcegroep in uw abonnement.
author: tfitzmac
ms.date: 12/14/2020
ms.topic: quickstart
ms.author: tomfitz
ms.openlocfilehash: 28987486726f5a88d20efe9fe8a766e536062c2c
ms.sourcegitcommit: a67b972d655a5a2d5e909faa2ea0911912f6a828
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104889957"
---
# <a name="quickstart-create-and-deploy-template-spec-preview"></a>Quickstart: Sjabloonspecificatie maken en implementeren (preview)

In deze quickstart wordt uitgelegd hoe u een ARM-sjabloon (Azure Resource Manager) inpakt in een [sjabloonspecificatie](template-specs.md). Vervolgens implementeert u deze sjabloonspecificatie. Uw sjabloonspecificatie bevat een ARM-sjabloon die een opslagaccount implementeert.

## <a name="prerequisites"></a>Vereisten

Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

> [!NOTE]
> Sjabloonspecificaties is momenteel beschikbaar als preview-versie. Voor gebruik met Azure PowerShell moet u [versie 5.0.0 of hoger](/powershell/azure/install-az-ps) installeren. Voor gebruik met Azure CLI hebt u [versie 2.14.2 of hoger](/cli/azure/install-azure-cli) nodig.

## <a name="create-template"></a>Sjabloon maken

U maakt een sjabloonspecificatie met behulp van een lokaal sjabloon. Kopieer de volgende sjabloon en sla deze lokaal op in een bestand met de naam **azuredeploy.json**. In deze quickstart wordt ervan uitgegaan dat u hebt opgeslagen naar het pad **c:\Templates\azuredeploy.json**, maar u kunt elk pad gebruiken.

:::code language="json" source="~/quickstart-templates/101-storage-account-create/azuredeploy.json":::

## <a name="create-template-spec"></a>Sjabloonspecificatie maken

De sjabloonspecificatie is een resourcetype met de naam `Microsoft.Resources/templateSpecs`. Gebruik PowerShell, Azure CLI, de portal of een ARM-sjabloon om een sjabloonspecificatie te maken.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Maak een nieuwe resourcegroep voor de sjabloonspecificatie.

    ```azurepowershell
    New-AzResourceGroup `
      -Name templateSpecRG `
      -Location westus2
    ```

1. Maak de sjabloonspecificatie in deze resourcegroep. Geef de nieuwe sjabloonspecificatie de naam **storageSpec**.

    ```azurepowershell
    New-AzTemplateSpec `
      -Name storageSpec `
      -Version "1.0" `
      -ResourceGroupName templateSpecRG `
      -Location westus2 `
      -TemplateFile "c:\Templates\azuredeploy.json"
    ```

# <a name="cli"></a>[CLI](#tab/azure-cli)

1. Maak een nieuwe resourcegroep voor de sjabloonspecificatie.

    ```azurecli
    az group create \
      --name templateSpecRG \
      --location westus2
    ```

1. Maak de sjabloonspecificatie in deze resourcegroep. Geef de nieuwe sjabloonspecificatie de naam **storageSpec**.

    ```azurecli
    az ts create \
      --name storageSpec \
      --version "1.0" \
      --resource-group templateSpecRG \
      --location "westus2" \
      --template-file "c:\Templates\azuredeploy.json"
    ```

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Zoek naar **sjabloonspecificatie**. Selecteer **Sjabloonspecificatie** in de beschikbare opties.

    :::image type="content" source="./media/quickstart-create-template-specs/search-template-spec.png" alt-text="sjabloonspecificatie zoeken":::

1. Selecteer **Sjabloon importeren**.

    :::image type="content" source="./media/quickstart-create-template-specs/import-template.png" alt-text="sjabloon importeren":::

1. Selecteer het mappictogram.

    :::image type="content" source="./media/quickstart-create-template-specs/open-folder.png" alt-text="map openen":::

1. Ga naar de lokale sjabloon die u hebt opgeslagen, en selecteer deze. Selecteer **Openen**.
1. Selecteer **Importeren**.

    :::image type="content" source="./media/quickstart-create-template-specs/select-import.png" alt-text="optie importeren selecteren":::

1. Geef de volgende waarden op:

    - **Naam**: voer een naam in voor de sjabloonspecificatie.  Bijvoorbeeld **storageSpec**
    - **Abonnement**: selecteer een Azure-abonnement om de sjabloonspecificatie te maken.
    - **Resourcegroep**: selecteer **Nieuwe maken** en voer een naam voor de nieuwe resourcegroep in.  Bijvoorbeeld **templateSpecRG**.
    - **Locatie**: selecteer een locatie voor de resourcegroep. Bijvoorbeeld **West US 2**.
    - **Versie**: voer een versie in voor de sjabloonspecificatie. Gebruik **1.0**.

1. Selecteer **Controleren + maken**.
1. Selecteer **Maken**.

# <a name="arm-template"></a>[Azure VPN-gateway](#tab/azure-resource-manager)

> [!NOTE]
> In plaats van een ARM-sjabloon te gebruiken, raden wij u aan PowerShell of CLI te gebruiken om de sjabloonspecificatie te maken. Met deze hulpprogramma's worden automatisch gekoppelde sjablonen geconverteerd naar artefacten die zijn verbonden met uw hoofdsjabloon. Wanneer u een ARM-sjabloon gebruikt om de sjabloonspecificatie te maken, moet u deze gekoppelde sjablonen handmatig toevoegen als artefacten. Dit kan gecompliceerd zijn.

1. Wanneer u een ARM-sjabloon gebruikt om de sjabloonspecificatie te maken, wordt de sjabloon ingesloten in de resourcedefinitie. U moet enkele wijzigingen aanbrengen in de lokale sjabloon. Kopieer de volgende sjabloon en sla deze lokaal op als **azuredeploy.json**.

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

Als u een sjabloonspecificatie wilt implementeren, gebruikt u dezelfde implementatieopdrachten die u gebruikt voor het implementeren van een sjabloon. Geef de resource-id van de sjabloonspecificatie door om te implementeren.

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
    id=$(az ts show --name storageSpec --resource-group templateSpecRG --version "1.0" --query "id")
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

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Selecteer de sjabloonspecificatie die u hebt gemaakt.

    :::image type="content" source="./media/quickstart-create-template-specs/select-template-spec.png" alt-text="sjabloonspecificatie selecteren":::

1. Selecteer **Implementeren**.

    :::image type="content" source="./media/quickstart-create-template-specs/deploy-template-spec.png" alt-text="sjabloonspecificatie implementeren":::

1. Geef de volgende waarden op:

    - **Abonnement**: selecteer een Azure-abonnement om de resource te maken.
    - **Resourcegroep**: selecteer **Nieuwe maken** en typ **storageRG**.
    - **Type opslagaccount**: selecteer **Standard_GRS**.

1. Selecteer **Controleren en maken**.
1. Selecteer **Maken**.

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
          "apiVersion": "2020-10-01",
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

## <a name="update-template"></a>Sjabloon bijwerken

Stel dat u een wijziging hebt geïdentificeerd die u wilt aanbrengen in de sjabloon in uw sjabloonspecificatie. De volgende sjabloon is vergelijkbaar met de eerdere sjabloon, behalve dat er een voorvoegsel wordt toegevoegd aan de naam van het opslagaccount. Kopieer de volgende sjabloon, en werk het bestand azuredeploy.json bij.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/templatespecs/azuredeploy2.json":::

## <a name="update-template-spec-version"></a>Versie van sjabloonspecificatie bijwerken

In plaats van een nieuwe sjabloonspecificatie te maken voor de gereviseerde sjabloon, voegt u een nieuwe versie, genaamd `2.0`, toe aan de bestaande sjabloonspecificatie. Gebruikers kunnen kiezen welke van de twee versie ze willen implementeren.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Maak een nieuwe versie voor de sjabloonspecificatie.

   ```powershell
   New-AzTemplateSpec `
     -Name storageSpec `
     -Version "2.0" `
     -ResourceGroupName templateSpecRG `
     -Location westus2 `
     -TemplateFile "c:\Templates\azuredeploy.json"
   ```

1. Als u de nieuwe versie wilt implementeren, haalt u de resource-id op voor de `2.0`-versie.

   ```powershell
   $id = (Get-AzTemplateSpec -ResourceGroupName templateSpecRG -Name storageSpec -Version "2.0").Versions.Id
   ```

1. Implementeer deze versie. Geef een voorvoegsel op voor de opslagaccountnaam.

   ```powershell
   New-AzResourceGroupDeployment `
     -TemplateSpecId $id `
     -ResourceGroupName storageRG `
     -namePrefix "demoaccount"
   ```

# <a name="cli"></a>[CLI](#tab/azure-cli)

1. Maak een nieuwe versie voor de sjabloonspecificatie.

   ```azurecli
   az ts create \
     --name storageSpec \
     --version "2.0" \
     --resource-group templateSpecRG \
     --location "westus2" \
     --template-file "c:\Templates\azuredeploy.json"
   ```

1. Als u de nieuwe versie wilt implementeren, haalt u de resource-id op voor de `2.0`-versie.

   ```azurecli
   id=$(az ts show --name storageSpec --resource-group templateSpecRG --version "2.0" --query "id")
   ```

1. Implementeer deze versie. Geef een voorvoegsel op voor de opslagaccountnaam.

   ```azurecli
    az deployment group create \
      --resource-group storageRG \
      --template-spec $id \
      --parameters namePrefix='demoaccount'
    ```

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Selecteer in de sjabloonspecificatie de optie **Nieuwe versie maken**.

   :::image type="content" source="./media/quickstart-create-template-specs/select-versions.png" alt-text="nieuwe versie maken":::

1. Geef de nieuwe versie de naam `2.0`, en voeg eventueel notities toe. Selecteer **Sjabloon bewerken**.

   :::image type="content" source="./media/quickstart-create-template-specs/add-version-name.png" alt-text="nieuwe versie een naam geven":::

1. Vervang de inhoud van de sjabloon door de bijgewerkte sjabloon. Selecteer **Beoordelen en opslaan**.
1. Selecteer **Save changes**.

1. Als u de nieuwe versie wilt implementeren, selecteert u **Versies**

   :::image type="content" source="./media/quickstart-create-template-specs/see-versions.png" alt-text="versies weergeven":::

1. Selecteer voor de versie die u wilt implementeren, de drie punten en **Implementeren**.

   :::image type="content" source="./media/quickstart-create-template-specs/deploy-version.png" alt-text="versie selecteren om te implementeren":::

1. Vul de velden in zoals u hebt gedaan bij het implementeren van de eerdere versie.
1. Selecteer **Controleren en maken**.
1. Selecteer **Maken**.

# <a name="arm-template"></a>[Azure VPN-gateway](#tab/azure-resource-manager)

1. Ook hier moet u enkele wijzigingen aanbrengen in de lokale sjabloon om deze te laten werken met sjabloonspecificaties. Kopieer de volgende sjabloon en sla deze lokaal op als azuredeploy.json.

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
              "name": "2.0",
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
                    },
                    "namePrefix": {
                      "type": "string",
                      "maxLength": 11,
                      "defaultValue": "store",
                      "metadata": {
                        "description": "Prefix for storage account name"
                      }
                    }
                  },
                  "variables": {
                    "storageAccountName": "[[concat(parameters('namePrefix'), uniquestring(resourceGroup().id))]"
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

1. Als u de nieuwe versie wilt toevoegen aan de sjabloonspecificatie, implementeert u de sjabloon met Azure CLI of PowerShell.

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
          "apiVersion": "2020-10-01",
          "name": "demo",
          "properties": {
            "templateLink": {
              "id": "[resourceId('templateSpecRG', 'Microsoft.Resources/templateSpecs/versions', 'storageSpec', '2.0')]"
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

## <a name="clean-up-resources"></a>Resources opschonen

Als u de resource wilt opschonen die u in deze quickstart hebt geïmplementeerd, verwijdert u beide resourcegroepen die u hebt gemaakt.

1. Selecteer Resourcegroep in het linkermenu van Azure Portal.

1. Voer in het veld Filteren op naam de naam van de resourcegroep (templateSpecRG en storageRG) in.

1. Selecteer de naam van de resourcegroep.

1. Selecteer Resourcegroep verwijderen in het bovenste menu.

## <a name="next-steps"></a>Volgende stappen

Zie [Een sjabloonspecificatie van een gekoppelde sjabloon maken](template-specs-create-linked.md) voor meer informatie over het maken van een sjabloonspecificatie die gekoppelde sjablonen bevat.
