---
title: Sjabloon functies in implementaties met een bereik
description: Hierin wordt beschreven hoe sjabloon functies worden omgezet in implementaties met een bereik. Het bereik kan een Tenant, beheer groepen, abonnementen en resource groepen zijn.
ms.topic: conceptual
ms.date: 10/22/2020
ms.openlocfilehash: f128448380612bc9b8d9114226e8a3036feeead8
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/03/2021
ms.locfileid: "99492090"
---
# <a name="arm-template-functions-in-deployment-scopes"></a>ARM-sjabloon functies in implementatie bereiken

Met Azure Resource Manager sjablonen (ARM-sjablonen) kunt u implementeren naar resource groepen, abonnementen, beheer groepen of tenants. Over het algemeen werken [arm-sjabloon functies](template-functions.md) hetzelfde voor alle bereiken. In dit artikel worden de verschillen beschreven die van toepassing zijn voor sommige functies, afhankelijk van het bereik.

## <a name="supported-functions"></a>Ondersteunde functies

Wanneer u implementeert in verschillende bereiken, zijn er enkele belang rijke aandachtspunten:

* De functie [resourceGroup ()](template-functions-resource.md#resourcegroup) wordt **ondersteund** voor implementaties van resource groepen.
* De functie [Subscription ()](template-functions-resource.md#subscription) wordt **ondersteund** voor implementaties van resource groepen en abonnementen.
* De functies [Reference ()](template-functions-resource.md#reference) en [List ()](template-functions-resource.md#list) worden **ondersteund** voor alle bereiken.
* Gebruik [resourceId ()](template-functions-resource.md#resourceid) om de id op te halen van een resource die is geïmplementeerd in de groep resources.

  ```json
  "subnet": {
    "id": "[resourceId(parameters('virtualNetworkResourceGroup'), 'Microsoft.Network/virtualNetworks/subnets', parameters('virtualNetworkName'), parameters('subnet1Name'))]"
  }
  ```

* Gebruik de functie [subscriptionResourceId ()](template-functions-resource.md#subscriptionresourceid) om de id op te halen van een resource die bij het abonnement is geïmplementeerd.

  Als u bijvoorbeeld de resource-ID wilt ophalen voor een beleids definitie die is geïmplementeerd op een abonnement, gebruikt u:

  ```json
  "roleDefinitionId": "[subscriptionResourceId('Microsoft.Authorization/roleDefinitions', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]"
  ```

* Gebruik de functie [extensionResourceId ()](template-functions-resource.md#extensionresourceid) voor resources die zijn geïmplementeerd als uitbrei dingen van de beheer groep. Aangepaste beleids definities die worden geïmplementeerd in de beheer groep zijn uitbrei dingen van de beheer groep.

  Als u de resource-ID voor een aangepaste beleids definitie op het niveau van de beheer groep wilt ophalen, gebruikt u:

  ```json
  "policyDefinitionId": "[extensionResourceId(variables('mgScope'), 'Microsoft.Authorization/policyDefinitions', parameters('policyDefinitionID'))]"
  ```

* Gebruik de functie [tenantResourceId ()](template-functions-resource.md#tenantresourceid) om de id op te halen van een resource die is geïmplementeerd op de Tenant. Ingebouwde beleids definities zijn resources op Tenant niveau. Wanneer u een ingebouwd beleid toewijst op het niveau van de beheer groep, gebruikt u de functie tenantResourceId.

  Als u de resource-ID voor een ingebouwde beleids definitie wilt ophalen, gebruikt u:

  ```json
  "policyDefinitionId": "[tenantResourceId('Microsoft.Authorization/policyDefinitions', parameters('policyDefinitionID'))]"
  ```

## <a name="function-resolution-in-scopes"></a>Functie resolutie in bereiken

Wanneer u op meer dan één bereik implementeert, worden de functies [resourceGroup ()](template-functions-resource.md#resourcegroup) en [Subscription ()](template-functions-resource.md#subscription) verschillend omgezet op basis van de manier waarop u de sjabloon opgeeft. Wanneer u een koppeling naar een externe sjabloon maakt, worden de functies altijd omgezet in het bereik voor die sjabloon. Wanneer u een sjabloon in een bovenliggende sjabloon nest, gebruikt u de `expressionEvaluationOptions` eigenschap om op te geven of de functies worden omgezet in de resource groep en het abonnement voor de bovenliggende sjabloon of de geneste sjabloon. Stel de eigenschap in op `inner` om om te zetten in het bereik voor de geneste sjabloon. Stel de eigenschap in op `outer` om om te zetten naar het bereik van de bovenliggende sjabloon.

In de volgende tabel wordt aangegeven of de functies worden omgezet naar de bovenliggende of Inge sloten resource groep en het-abonnement.

| Sjabloon type | Bereik | Oplossing |
| ------------- | ----- | ---------- |
| nest        | Outer (standaard) | Bovenliggende resource groep |
| nest        | wend | Subresourcegroep |
| Btrieve        | N.v.t.   | Subresourcegroep |

In de volgende [voorbeeld sjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/crossresourcegroupproperties.json) ziet u:

* geneste sjabloon met het standaard bereik (buitenste)
* geneste sjabloon met binnenste bereik
* gekoppelde sjabloon

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/crossresourcegroupproperties.json":::

Als u de voor gaande sjabloon wilt testen en de resultaten wilt weer geven, gebruikt u Power shell of Azure CLI.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup -Name parentGroup -Location southcentralus
New-AzResourceGroup -Name inlineGroup -Location southcentralus
New-AzResourceGroup -Name linkedGroup -Location southcentralus

New-AzResourceGroupDeployment `
  -ResourceGroupName parentGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json
```

De uitvoer van het vorige voor beeld is:

```output
 Name             Type                       Value
 ===============  =========================  ==========
 parentRG         String                     Parent resource group is parentGroup
 defaultScopeRG   String                     Default scope resource group is parentGroup
 innerScopeRG     String                     Inner scope resource group is inlineGroup
 linkedRG         String                     Linked resource group is linkedgroup
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli-interactive
az group create --name parentGroup --location southcentralus
az group create --name inlineGroup --location southcentralus
az group create --name linkedGroup --location southcentralus

az deployment group create \
  --name ExampleDeployment \
  --resource-group parentGroup \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json
```

De uitvoer van het vorige voor beeld is:

```output
"outputs": {
  "defaultScopeRG": {
    "type": "String",
    "value": "Default scope resource group is parentGroup"
  },
  "innerScopeRG": {
    "type": "String",
    "value": "Inner scope resource group is inlineGroup"
  },
  "linkedRG": {
    "type": "String",
    "value": "Linked resource group is linkedGroup"
  },
  "parentRG": {
    "type": "String",
    "value": "Parent resource group is parentGroup"
  }
},
```

---

## <a name="next-steps"></a>Volgende stappen

* Zie [inzicht in de structuur en syntaxis van arm-sjablonen](template-syntax.md)voor informatie over het definiëren van para meters in uw sjabloon.
* Zie [problemen met algemene Azure-implementatie fouten oplossen met Azure Resource Manager](common-deployment-errors.md)voor tips over het oplossen van veelvoorkomende implementatie fouten.
* Zie voor meer informatie over het implementeren van een sjabloon waarvoor een SAS-token is vereist een [persoonlijke arm-sjabloon implementeren met SAS-token](secure-template-with-sas-token.md).
