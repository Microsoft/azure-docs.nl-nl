---
title: Een sjabloon specificatie met gekoppelde sjablonen maken
description: Meer informatie over het maken van een sjabloon spec met gekoppelde sjablonen.
ms.topic: conceptual
ms.date: 11/17/2020
ms.openlocfilehash: d86b46a88dde7ebffea81cdeb8dc184dd275c0da
ms.sourcegitcommit: c2dd51aeaec24cd18f2e4e77d268de5bcc89e4a7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94747431"
---
# <a name="tutorial-create-a-template-spec-with-linked-templates-preview"></a>Zelf studie: een sjabloon specificatie met gekoppelde sjablonen maken (preview)

Meer informatie over het maken van een [sjabloon specificatie](template-specs.md) met een hoofd sjabloon en een [gekoppelde sjabloon](linked-templates.md#linked-template). U kunt sjabloon specificaties gebruiken om ARM-sjablonen te delen met andere gebruikers in uw organisatie. In dit artikel wordt beschreven hoe u een sjabloon specificatie maakt voor het inpakken van een hoofd sjabloon en gekoppelde sjablonen met behulp `relativePath` van de eigenschap van de [implementatie bron](/azure/templates/microsoft.resources/deployments).

## <a name="prerequisites"></a>Vereisten

Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

> [!NOTE]
> Sjabloonspecificaties is momenteel beschikbaar als preview-versie. Als u deze wilt gebruiken, moet u de nieuwste versie van Power shell of Azure CLI installeren. Gebruik voor Azure PowerShell [versie 5.0.0 of hoger](/powershell/azure/install-az-ps). Gebruik voor Azure CLI [versie 2.14.2 of hoger](/cli/azure/install-azure-cli).

## <a name="create-linked-templates"></a>Gekoppelde sjablonen maken

Maak de hoofd sjabloon en de gekoppelde sjabloon.

Als u een sjabloon wilt koppelen, moet u een implementatie van een [resource](/azure/templates/microsoft.resources/deployments) toevoegen aan uw hoofd sjabloon. Geef in de `templateLink` eigenschap het relatieve pad van de gekoppelde sjabloon op in overeenstemming met het pad van de bovenliggende sjabloon.

De gekoppelde sjabloon heet **linkedTemplate.jsop** en wordt opgeslagen in een submap met de naam **artefacten** in het pad waar de hoofd sjabloon is opgeslagen.  U kunt een van de volgende waarden gebruiken voor de relativePath:

- `./artifacts/linkedTemplate.json`
- `/artifacts/linkedTemplate.json`
- `artifacts/linkedTemplate.json`

De `relativePath` eigenschap is altijd relatief ten opzichte van het sjabloon bestand waar `relativePath` is gedeclareerd, dus als er een andere linkedTemplate2.jsis aangeroepen vanuit linkedTemplate.jsop en linkedTemplate2.jsop wordt opgeslagen in dezelfde submap voor artefacten, is de relativePath die is opgegeven in linkedTemplate.js, alleen `linkedTemplate2.json` .

1. Maak de hoofd sjabloon met de volgende JSON. Sla de hoofd sjabloon **op alsazuredeploy.jsop** de lokale computer. In deze zelf studie wordt ervan uitgegaan dat u hebt opgeslagen in een pad **c:\Templates\linkedTS\azuredeploy.js** , maar u kunt een wille keurig pad gebruiken.

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "location": {
          "type": "string",
          "defaultValue": "westus2",
          "metadata":{
            "description": "Specify the location for the resources."
          }
        },
        "storageAccountType": {
          "type": "string",
          "defaultValue": "Standard_LRS",
          "metadata":{
            "description": "Specify the storage account type."
          }
        }
      },
      "variables": {
        "appServicePlanName": "[concat('plan', uniquestring(resourceGroup().id))]"
      },
      "resources": [
        {
          "type": "Microsoft.Web/serverfarms",
          "apiVersion": "2016-09-01",
          "name": "[variables('appServicePlanName')]",
          "location": "[parameters('location')]",
          "sku": {
            "name": "B1",
            "tier": "Basic",
            "size": "B1",
            "family": "B",
            "capacity": 1
          },
          "kind": "linux",
          "properties": {
            "perSiteScaling": false,
            "reserved": true,
            "targetWorkerCount": 0,
            "targetWorkerSizeId": 0
          }
        },
        {
          "type": "Microsoft.Resources/deployments",
          "apiVersion": "2020-06-01",
          "name": "createStorage",
          "properties": {
            "mode": "Incremental",
            "templateLink": {
              "relativePath": "artifacts/linkedTemplate.json"
            },
            "parameters": {
              "storageAccountType": {
                "value": "[parameters('storageAccountType')]"
              }
            }
          }
        }
      ]
    }
    ```

    > [!NOTE]
    > De apiVersion van `Microsoft.Resources/deployments` moet 2020-06-01 of hoger zijn.

1. Maak een map met de naam **artefacten** in de map waarin de hoofd sjabloon is opgeslagen.
1. Maak de gekoppelde sjabloon met de volgende JSON:

    ```json
    {
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
          "defaultValue": "[resourceGroup().location]",
          "metadata": {
            "description": "Location for all resources."
          }
        }
      },
      "variables": {
        "storageAccountName": "[concat('store', uniquestring(resourceGroup().id))]"
      },
      "resources": [
        {
          "type": "Microsoft.Storage/storageAccounts",
          "apiVersion": "2019-04-01",
          "name": "[variables('storageAccountName')]",
          "location": "[parameters('location')]",
          "sku": {
            "name": "[parameters('storageAccountType')]"
          },
          "kind": "StorageV2",
          "properties": {}
        }
      ],
      "outputs": {
        "storageAccountName": {
          "type": "string",
          "value": "[variables('storageAccountName')]"
        }
      }
    }
    ```

1. Sla de sjabloon **op alslinkedTemplate.js** in de map **artefacten** .

## <a name="create-template-spec"></a>Sjabloonspecificatie maken

Sjabloon specificaties worden opgeslagen in resource groepen.  Maak een resource groep en maak vervolgens een sjabloon specificatie met het volgende script. De naam van de sjabloon specificatie is **webspec**.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroup `
  -Name templateSpecRG `
  -Location westus2

New-AzTemplateSpec `
  -Name webSpec `
  -Version "1.0.0.0" `
  -ResourceGroupName templateSpecRG `
  -Location westus2 `
  -TemplateFile "c:\Templates\linkedTS\azuredeploy.json"
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
az group create \
  --name templateSpecRG \
  --location westus2

az ts create \
  --name webSpec \
  --version "1.0.0.0" \
  --resource-group templateSpecRG \
  --location "westus2" \
  --template-file "c:\Templates\linkedTS\azuredeploy.json"
```

---

Wanneer u klaar bent, kunt u de sjabloon specificatie bekijken vanuit de Azure Portal of met behulp van de volgende cmdlet:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Get-AzTemplateSpec -ResourceGroupName templatespecRG -Name webSpec
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
az ts show --name webSpec --resource-group templateSpecRG --version "1.0.0.0"
```

---

## <a name="deploy-template-spec"></a>Sjabloonspecificatie implementeren

U kunt nu de sjabloonspecificatie implementeren. De sjabloonspecificatie wordt op dezelfde manier geïmplementeerd als de sjabloon in de sjabloonspecificatie. Het enige verschil is dat u de resource-id van de sjabloonspecificatie doorgeeft. U gebruikt dezelfde implementatieopdrachten en geeft indien nodig de parameterwaarden voor de sjabloonspecificatie door.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroup `
  -Name webRG `
  -Location westus2

$id = (Get-AzTemplateSpec -ResourceGroupName templateSpecRG -Name webSpec -Version "1.0.0.0").Versions.Id

New-AzResourceGroupDeployment `
  -TemplateSpecId $id `
  -ResourceGroupName webRG
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
az group create \
  --name webRG \
  --location westus2

id = $(az template-specs show --name webSpec --resource-group templateSpecRG --version "1.0.0.0" --query "id")

az deployment group create \
  --resource-group webRG \
  --template-spec $id
```

> [!NOTE]
> Er is een bekend probleem met het ophalen van een sjabloonspecificatie-id en het toewijzen ervan aan een variabele in Windows PowerShell.

---

## <a name="next-steps"></a>Volgende stappen

Zie [zelf studie: een sjabloon specificatie implementeren als gekoppelde sjabloon](template-specs-deploy-linked-template.md)voor meer informatie over het implementeren van een sjabloon specificatie als een gekoppelde sjabloon.
