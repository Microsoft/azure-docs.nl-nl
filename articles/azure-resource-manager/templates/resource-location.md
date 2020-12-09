---
title: Resource locatie van sjabloon
description: Hierin wordt beschreven hoe u een resource locatie instelt in een Azure Resource Manager sjabloon (ARM-sjabloon).
ms.topic: conceptual
ms.date: 09/04/2019
ms.custom: ''
ms.openlocfilehash: 84a818109e6681b8d0e18de4d2d7969310582818
ms.sourcegitcommit: fec60094b829270387c104cc6c21257826fccc54
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/09/2020
ms.locfileid: "96922399"
---
# <a name="set-resource-location-in-arm-template"></a>De resource locatie in een ARM-sjabloon instellen

Bij het implementeren van een Azure Resource Manager sjabloon (ARM-sjabloon) moet u voor elke resource een locatie opgeven. De locatie hoeft niet dezelfde locatie te zijn als de locatie van de resource groep.

## <a name="get-available-locations"></a>Beschik bare locaties ophalen

Verschillende resource typen worden ondersteund op verschillende locaties. Gebruik Azure PowerShell of Azure CLI om de ondersteunde locaties voor een resource type op te halen.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
((Get-AzResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes `
  | Where-Object ResourceTypeName -eq batchAccounts).Locations
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli-interactive
az provider show \
  --namespace Microsoft.Batch \
  --query "resourceTypes[?resourceType=='batchAccounts'].locations | [0]" \
  --out table
```

---

## <a name="use-location-parameter"></a>Locatie parameter gebruiken

Gebruik een para meter om de locatie voor resources op te geven om flexibiliteit te bieden bij het implementeren van uw sjabloon. Stel de standaard waarde van de para meter in op `resourceGroup().location` .

In het volgende voor beeld ziet u een opslag account dat is geïmplementeerd op een locatie die is opgegeven als een para meter:

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
    "storageAccountName": "[concat('storage', uniquestring(resourceGroup().id))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2018-07-01",
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

## <a name="next-steps"></a>Volgende stappen

* Zie [arm-sjabloon functies](template-functions.md)voor de volledige lijst met sjabloon functies.
* Zie [inzicht krijgen in de structuur en syntaxis van arm-sjablonen](template-syntax.md)voor meer informatie over sjabloon bestanden.
