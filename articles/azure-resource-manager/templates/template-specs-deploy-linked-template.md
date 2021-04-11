---
title: Een sjabloon specificatie als gekoppelde sjabloon implementeren
description: Meer informatie over het implementeren van een bestaande sjabloon specificatie in een gekoppelde implementatie.
ms.topic: conceptual
ms.date: 11/17/2020
ms.openlocfilehash: 8d4ccd77c8b37a696fab7494a8d3f8052fc89b35
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104889260"
---
# <a name="tutorial-deploy-a-template-spec-as-a-linked-template-preview"></a>Zelf studie: een sjabloon specificatie implementeren als gekoppelde sjabloon (preview)

Meer informatie over het implementeren van een bestaande [sjabloon specificatie](template-specs.md) met behulp van een [gekoppelde implementatie](linked-templates.md#linked-template). U kunt sjabloon specificaties gebruiken om ARM-sjablonen te delen met andere gebruikers in uw organisatie. Nadat u een sjabloon specificatie hebt gemaakt, kunt u de sjabloon specificatie implementeren met behulp van Azure PowerShell of Azure CLI. U kunt ook de sjabloon specificatie als onderdeel van uw oplossing implementeren met behulp van een gekoppelde sjabloon.

## <a name="prerequisites"></a>Vereisten

Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

> [!NOTE]
> Sjabloonspecificaties is momenteel beschikbaar als preview-versie. Voor gebruik met Azure PowerShell moet u [versie 5.0.0 of hoger](/powershell/azure/install-az-ps) installeren. Voor gebruik met Azure CLI hebt u [versie 2.14.2 of hoger](/cli/azure/install-azure-cli) nodig.

## <a name="create-a-template-spec"></a>Een sjabloon specificatie maken

Volg de [Snelstartgids: sjabloon specificatie maken en implementeren](quickstart-create-template-specs.md) voor het maken van een sjabloon specificatie voor het implementeren van een opslag account. U hebt de naam van de resource groep van de sjabloon specificatie, naam sjabloon specificatie en sjabloon specificatie nodig in de volgende sectie.

## <a name="create-the-main-template"></a>De hoofd sjabloon maken

Als u een sjabloon specificatie in een ARM-sjabloon wilt implementeren, moet u een [resource voor implementaties](/azure/templates/microsoft.resources/deployments) toevoegen aan uw hoofd sjabloon. Geef in de `templateLink` eigenschap de resource-id van een sjabloon specificatie op. Maak een sjabloon met de volgende JSON met de naam **azuredeploy.jsop**. In deze zelf studie wordt ervan uitgegaan dat u hebt opgeslagen in een pad **c:\Templates\deployTS\azuredeploy.js** , maar u kunt een wille keurig pad gebruiken.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]"
    },
    "tsResourceGroup":{
      "type": "string",
      "metadata": {
        "Description": "Specifies the resource group name of the template spec."
      }
    },
    "tsName": {
      "type": "string",
      "metadata": {
        "Description": "Specifies the name of the template spec."
      }
    },
    "tsVersion": {
      "type": "string",
      "defaultValue": "1.0.0.0",
      "metadata": {
        "Description": "Specifies the version the template spec."
      }
    },
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "metadata": {
        "Description": "Specifies the storage account type required by the template spec."
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
      "apiVersion": "2020-10-01",
      "name": "createStorage",
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "id": "[resourceId(parameters('tsResourceGroup'), 'Microsoft.Resources/templateSpecs/versions', parameters('tsName'), parameters('tsVersion'))]"
        },
        "parameters": {
          "storageAccountType": {
            "value": "[parameters('storageAccountType')]"
          }
        }
      }
    }
  ],
  "outputs": {
    "templateSpecId": {
      "type": "string",
      "value": "[resourceId(parameters('tsResourceGroup'), 'Microsoft.Resources/templateSpecs/versions', parameters('tsName'), parameters('tsVersion'))]"
    }
  }
}
```

De sjabloon specificatie-ID wordt gegenereerd met behulp van de [`resourceID()`](template-functions-resource.md#resourceid) functie. Het argument voor de resource groep in de functie resourceID () is optioneel als de templateSpec zich in dezelfde resource groep bevindt als de huidige implementatie.  U kunt de resource-ID ook rechtstreeks door geven als een para meter. Als u de ID wilt ophalen, gebruikt u:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$id = (Get-AzTemplateSpec -ResourceGroupName $resourceGroupName -Name $templateSpecName -Version $templateSpecVersion).Versions.Id
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli-interactive
id = $(az ts show --name $templateSpecName --resource-group $resourceGroupName --version $templateSpecVersion --query "id")
```

> [!NOTE]
> Er is een bekend probleem met het ophalen van een sjabloonspecificatie-id en het toewijzen ervan aan een variabele in Windows PowerShell.

---

De syntaxis voor het door geven van para meters voor de sjabloon specificatie is:

```json
"parameters": {
  "storageAccountType": {
    "value": "[parameters('storageAccountType')]"
  }
}
```

> [!NOTE]
> De apiVersion van `Microsoft.Resources/deployments` moet 2020-06-01 of hoger zijn.

## <a name="deploy-the-template"></a>De sjabloon implementeren

Wanneer u de gekoppelde sjabloon implementeert, worden zowel de webtoepassing als het opslag account geïmplementeerd. De implementatie is hetzelfde als het implementeren van andere ARM-sjablonen.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroup `
  -Name webRG `
  -Location westus2

New-AzResourceGroupDeployment `
  -ResourceGroupName webRG `
  -TemplateFile "c:\Templates\deployTS\azuredeploy.json" `
  -tsResourceGroup templateSpecRg `
  -tsName storageSpec `
  -tsVersion 1.0
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
az group create \
  --name webRG \
  --location westus2

az deployment group create \
  --resource-group webRG \
  --template-file "c:\Templates\deployTS\azuredeploy.json" \
  --parameters tsResourceGroup=templateSpecRG tsName=storageSpec tsVersion=1.0
```

---

## <a name="next-steps"></a>Volgende stappen

Zie [Een sjabloonspecificatie van een gekoppelde sjabloon maken](template-specs-create-linked.md) voor meer informatie over het maken van een sjabloonspecificatie die gekoppelde sjablonen bevat.
