---
title: Voorwaardelijke implementatie met sjablonen
description: Hierin wordt beschreven hoe u een resource voorwaardelijk kunt implementeren in een Azure Resource Manager sjabloon (ARM-sjabloon).
ms.topic: conceptual
ms.date: 03/02/2021
ms.openlocfilehash: 409d258d7dfe3ed186e5cf97cc0dbe6dc149b849
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101741171"
---
# <a name="conditional-deployment-in-arm-templates"></a>Voorwaardelijke implementatie in ARM-sjablonen

Soms moet u optioneel een resource implementeren in een Azure Resource Manager sjabloon (ARM-sjabloon) of Bicep-bestand. Gebruik voor JSON-sjablonen het `condition` element om op te geven of de resource is geïmplementeerd. Gebruik voor Bicep het `if` sleutel woord om op te geven of de resource is geïmplementeerd. De waarde voor de voor waarde wordt omgezet in True of false. Als de waarde True is, wordt de resource gemaakt. Als de waarde False is, wordt de resource niet gemaakt. De waarde kan alleen worden toegepast op de hele resource.

> [!NOTE]
> Voorwaardelijke implementatie kan niet worden gecascaded op [onderliggende resources](child-resource-name-type.md). Als u een resource en de onderliggende resources voorwaardelijk wilt implementeren, moet u dezelfde voor waarde Toep assen op elk resource type.

## <a name="deploy-condition"></a>Voor waarde implementeren

U kunt een parameter waarde door geven die aangeeft of een resource is geïmplementeerd. In het volgende voor beeld wordt een DNS-zone geïmplementeerd.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "deployZone": {
      "type": "bool"
    }
  },
  "functions": [],
  "resources": [
    {
      "condition": "[parameters('deployZone')]",
      "type": "Microsoft.Network/dnsZones",
      "apiVersion": "2018-05-01",
      "name": "myZone",
      "location": "global"
    }
  ]
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param deployZone bool

resource dnsZone 'Microsoft.Network/dnszones@2018-05-01' = if (deployZone) {
  name: 'myZone'
  location: 'global'
}
```

---

Zie [logische Azure SQL-Server](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-logical-server)voor een complexere voor beeld.

## <a name="new-or-existing-resource"></a>Nieuwe of bestaande resource

U kunt voorwaardelijke implementatie gebruiken om een nieuwe resource te maken of een bestaande te gebruiken. In het volgende voor beeld ziet u hoe u een nieuw opslag account implementeert of een bestaand opslag account gebruikt.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountName": {
      "type": "string"
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]"
    },
    "newOrExisting": {
      "type": "string",
      "defaultValue": "new",
      "allowedValues": [
        "new",
        "existing"
      ]
    }
  },
  "functions": [],
  "resources": [
    {
      "condition": "[equals(parameters('newOrExisting'), 'new')]",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-06-01",
      "name": "[parameters('storageAccountName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "Standard_LRS",
        "tier": "Standard"
      },
      "kind": "StorageV2",
      "properties": {
        "accessTier": "Hot"
      }
    }
  ]
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param storageAccountName string
param location string = resourceGroup().location

@allowed([
  'new'
  'existing'
])
param newOrExisting string = 'new'

resource sa 'Microsoft.Storage/storageAccounts@2019-06-01' = if (newOrExisting == 'new') {
  name: storageAccountName
  location: location
  sku: {
    name: 'Standard_LRS'
    tier: 'Standard'
  }
  kind: 'StorageV2'
  properties: {
    accessTier: 'Hot'
  }
}
```

---

Wanneer de para meter `newOrExisting` is ingesteld op **Nieuw**, resulteert de voor waarde in waar. Het opslag account wordt geïmplementeerd. Wanneer echter `newOrExisting` is ingesteld op **bestaand**, wordt de voor waarde geëvalueerd als onwaar en wordt het opslag account niet geïmplementeerd.

`condition`Zie [VM met een nieuwe of bestaande Virtual Network, opslag en openbaar IP-adres](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-new-or-existing-conditions)voor een complete voorbeeld sjabloon die gebruikmaakt van het-element.

## <a name="runtime-functions"></a>Runtime-functies

Als u een [verwijzing](template-functions-resource.md#reference) of [lijst](template-functions-resource.md#list) functie gebruikt met een resource die voorwaardelijk is geïmplementeerd, wordt de functie geëvalueerd, zelfs als de resource niet is geïmplementeerd. Er wordt een fout bericht weer geven als de functie verwijst naar een resource die niet bestaat.

Gebruik de functie [als](template-functions-logical.md#if) om ervoor te zorgen dat de functie alleen wordt geëvalueerd voor omstandigheden wanneer de resource wordt geïmplementeerd. Zie de [functie als](template-functions-logical.md#if) voor een voorbeeld sjabloon die gebruikmaakt van `if` en `reference` met een voorwaardelijk geïmplementeerde resource.

U stelt een [resource in die afhankelijk](define-resource-dependency.md) is van een voorwaardelijke resource, op dezelfde manier als andere resources. Wanneer een voorwaardelijke resource niet is geïmplementeerd, wordt deze automatisch door Azure Resource Manager verwijderd uit de vereiste afhankelijkheden.

## <a name="complete-mode"></a>Volledige modus

Als u een sjabloon implementeert met de [modus volledig](deployment-modes.md) en een resource wordt niet geïmplementeerd omdat `condition` resulteert in ONWAAR, is het resultaat afhankelijk van de rest API versie die u gebruikt om de sjabloon te implementeren. Als u een eerdere versie dan 2019-05-10 gebruikt, wordt de resource **niet verwijderd**. Met 2019-05-10 of hoger wordt de resource **verwijderd**. Met de nieuwste versies van Azure PowerShell en Azure CLI wordt de resource verwijderd wanneer de voor waarde ONWAAR is.

## <a name="next-steps"></a>Volgende stappen

* Zie [complexe Cloud implementaties beheren met behulp van geavanceerde arm-sjabloon functies](/learn/modules/manage-deployments-advanced-arm-template-features/)voor een Microsoft Learn module die de voorwaardelijke implementatie bedekt.
* Zie [Aanbevolen procedures voor arm](template-best-practices.md)-sjablonen voor aanbevelingen voor het maken van een sjabloon.
* Zie [resource herhaling in arm-sjablonen](copy-resources.md)als u meerdere exemplaren van een resource wilt maken.
