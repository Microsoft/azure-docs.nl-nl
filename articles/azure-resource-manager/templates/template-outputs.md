---
title: Uitvoer in sjablonen
description: Hierin wordt beschreven hoe u uitvoer waarden definieert in een Azure Resource Manager sjabloon (ARM-sjabloon) en Bicep-bestand.
ms.topic: conceptual
ms.date: 02/19/2021
ms.openlocfilehash: 2b6a6afa127bf43102103baadae576233843f00d
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102123408"
---
# <a name="outputs-in-arm-templates"></a>Uitvoer in ARM-sjablonen

In dit artikel wordt beschreven hoe u uitvoer waarden definieert in uw Azure Resource Manager sjabloon (ARM-sjabloon) en Bicep-bestand. U gebruikt uitvoer wanneer u waarden van de geïmplementeerde resources moet retour neren.

De indeling van elke uitvoer waarde moet worden omgezet in een van de [gegevens typen](data-types.md).

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="define-output-values"></a>Uitvoer waarden definiëren

In het volgende voor beeld ziet u hoe u een eigenschap van een geïmplementeerde resource retourneert.

# <a name="json"></a>[JSON](#tab/json)

Voor JSON voegt u de `outputs` sectie toe aan de sjabloon. De uitvoer waarde krijgt de Fully Qualified Domain Name voor een openbaar IP-adres.

```json
"outputs": {
  "hostname": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.Network/publicIPAddresses', variables('publicIPAddressName'))).dnsSettings.fqdn]"
    },
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

Gebruik het `output` sleutel woord voor Bicep.

In het volgende voor beeld `publicIP` is de id van een openbaar IP-adres dat is geïmplementeerd in het Bicep-bestand. De uitvoer waarde krijgt de Fully Qualified Domain Name voor het open bare IP-adres.

```bicep
output hostname string = publicIP.properties.dnsSettings.fqdn
```

---

Als u een eigenschap wilt uitvoeren die een koppel teken in de naam heeft, gebruikt u haakjes rond de naam in plaats van punt notatie. Gebruik bijvoorbeeld  `['property-name']` in plaats van `.property-name` .

# <a name="json"></a>[JSON](#tab/json)

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "variables": {
        "user": {
            "user-name": "Test Person"
        }
    },
    "resources": [
    ],
    "outputs": {
        "nameResult": {
            "type": "string",
            "value": "[variables('user')['user-name']]"
        }
    }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
var user = {
  'user-name': 'Test Person'
}

output stringOutput string = user['user-name']
```

---

## <a name="conditional-output"></a>Voorwaardelijke uitvoer

U kunt voorwaardelijk een waarde Retour neren. Normaal gesp roken gebruikt u een voorwaardelijke uitvoer wanneer u een resource [voorwaardelijk hebt geïmplementeerd](conditional-resource-deployment.md) . In het volgende voor beeld ziet u hoe u de resource-ID voor een openbaar IP-adres voorwaardelijk kunt retour neren op basis van het feit of er een nieuw pakket is geïmplementeerd:

# <a name="json"></a>[JSON](#tab/json)

Voeg in JSON het `condition` element toe om te definiëren of de uitvoer wordt geretourneerd.

```json
"outputs": {
  "resourceID": {
    "condition": "[equals(parameters('publicIpNewOrExisting'), 'new')]",
    "type": "string",
    "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

Als u een voorwaardelijke uitvoer wilt opgeven in Bicep, gebruikt u de `?` operator. Het volgende voor beeld retourneert een eind punt-URL of een lege teken reeks, afhankelijk van een voor waarde.

```bicep
param deployStorage bool = true
param storageName string
param location string = resourceGroup().location

resource sa 'Microsoft.Storage/storageAccounts@2019-06-01' = if (deployStorage) {
  name: storageName
  location: location
  kind: 'StorageV2'
  sku:{
    name:'Standard_LRS'
    tier: 'Standard'
  }
  properties: {
    accessTier: 'Hot'
  }
}

output endpoint string = deployStorage ? sa.properties.primaryEndpoints.blob : ''
```

---

Zie [voorwaardelijke uitvoer sjabloon](https://github.com/bmoore-msft/AzureRM-Samples/blob/master/conditional-output/azuredeploy.json)voor een eenvoudig voor beeld van voorwaardelijke uitvoer.

## <a name="dynamic-number-of-outputs"></a>Dynamisch aantal uitvoer bewerkingen

In sommige scenario's weet u niet het aantal exemplaren van een waarde die u moet retour neren bij het maken van de sjabloon. U kunt een variabele aantal waarden retour neren met behulp van iteratieve uitvoer.

# <a name="json"></a>[JSON](#tab/json)

Voeg in JSON het `copy` element toe om een uitvoer te herhalen.

```json
"outputs": {
  "storageEndpoints": {
    "type": "array",
    "copy": {
      "count": "[parameters('storageCount')]",
      "input": "[reference(concat(copyIndex(), variables('baseName'))).primaryEndpoints.blob]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

Herhaalde uitvoer is momenteel niet beschikbaar voor Bicep.

---

Zie [uitvoer iteratie in arm-sjablonen](copy-outputs.md)voor meer informatie.

## <a name="linked-templates"></a>Gekoppelde sjablonen

In JSON-sjablonen kunt u gerelateerde sjablonen implementeren met behulp van [gekoppelde sjablonen](linked-templates.md). Gebruik de functie [Reference](template-functions-resource.md#reference) in de bovenliggende sjabloon om de uitvoer waarde van een gekoppelde sjabloon op te halen. De syntaxis van de bovenliggende sjabloon is:

```json
"[reference('<deploymentName>').outputs.<propertyName>.value]"
```

In het volgende voor beeld ziet u hoe u het IP-adres instelt op een load balancer door een waarde op te halen uit een gekoppelde sjabloon.

```json
"publicIPAddress": {
  "id": "[reference('linkedTemplate').outputs.resourceID.value]"
}
```

Als de naam van de eigenschap een koppel teken heeft, gebruikt u haakjes rond de naam in plaats van punt notatie.

```json
"publicIPAddress": {
  "id": "[reference('linkedTemplate').outputs['resource-ID'].value]"
}
```

U kunt de functie niet gebruiken `reference` in het gedeelte outputs van een [geneste sjabloon](linked-templates.md#nested-template). Als u de waarden voor een geïmplementeerde resource in een geneste sjabloon wilt retour neren, converteert u de geneste sjabloon naar een gekoppelde sjabloon.

Met de [sjabloon openbaar IP-adres](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json) maakt u een openbaar IP-adres en voert u de resource-id uit. De [Load Balancer-sjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json) verwijst naar de vorige sjabloon. De resource-ID wordt in de uitvoer gebruikt bij het maken van de load balancer.

## <a name="modules"></a>Modules

In Bicep-bestanden kunt u gerelateerde sjablonen implementeren met behulp van modules. Als u een uitvoer waarde uit een module wilt ophalen, gebruikt u de volgende syntaxis:

```bicep
<module-name>.outputs.<property-name>
```

In het volgende voor beeld ziet u hoe u het IP-adres instelt op een load balancer door een waarde uit een module op te halen. De naam van de module is `publicIP` .

```bicep
publicIPAddress: {
  id: publicIP.outputs.resourceID
}
```

## <a name="example-template"></a>Voorbeeld sjabloon

Met de volgende sjabloon worden geen resources geïmplementeerd. Het bevat enkele manieren om uitvoer van verschillende typen te retour neren.

# <a name="json"></a>[JSON](#tab/json)

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/outputs.json":::

# <a name="bicep"></a>[Bicep](#tab/bicep)

Bicep biedt momenteel geen ondersteuning voor lussen.

:::code language="bicep" source="~/resourcemanager-templates/azure-resource-manager/outputs.bicep":::

---

## <a name="get-output-values"></a>Uitvoer waarden ophalen

Wanneer de implementatie is geslaagd, worden de uitvoer waarden automatisch geretourneerd in de resultaten van de implementatie.

Als u uitvoer waarden wilt ophalen uit de implementatie geschiedenis, kunt u script gebruiken.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
(Get-AzResourceGroupDeployment `
  -ResourceGroupName <resource-group-name> `
  -Name <deployment-name>).Outputs.resourceID.value
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli-interactive
az deployment group show \
  -g <resource-group-name> \
  -n <deployment-name> \
  --query properties.outputs.resourceID.value
```

---

## <a name="next-steps"></a>Volgende stappen

* Zie [inzicht krijgen in de structuur en syntaxis van arm-sjablonen](template-syntax.md)voor meer informatie over de beschik bare eigenschappen voor uitvoer.
