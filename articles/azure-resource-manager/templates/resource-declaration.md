---
title: Resources declareren in sjablonen
description: Hierin wordt beschreven hoe u resources declareert om te implementeren in een Azure Resource Manager sjabloon (ARM-sjabloon).
ms.topic: conceptual
ms.date: 03/02/2021
ms.openlocfilehash: 13f4a08162c40cbb36173627d88a4a8202a4ed26
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101745650"
---
# <a name="resource-declaration-in-arm-templates"></a>Resource declaratie in ARM-sjablonen

Als u een resource wilt implementeren via een Azure Resource Manager sjabloon (ARM-sjabloon), voegt u een resource declaratie toe. Gebruik de `resources` matrix voor json-sjabloon of het `resource` tref woord voor Bicep.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="set-resource-type-and-version"></a>Resource type en-versie instellen

Wanneer u een resource aan uw sjabloon toevoegt, moet u eerst het resource type en de API-versie instellen. Deze waarden bepalen de andere eigenschappen die beschikbaar zijn voor de resource.

In het volgende voor beeld ziet u hoe u het resource type en de API-versie instelt voor een opslag account. In het voor beeld wordt de volledige resource declaratie niet weer gegeven.

# <a name="json"></a>[JSON](#tab/json)

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2019-06-01",
    ...
  }
]
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
resource sa 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  ...
}
```

---

Voor Bicep stelt u een symbolische naam voor de resource in. In het vorige voor beeld is de symbolische naam `sa` . U kunt elke waarde voor de symbolische naam gebruiken, maar deze kan niet hetzelfde zijn als een andere resource, para meter of variabele in de sjabloon. De symbolische naam is niet hetzelfde als de naam van de resource. U gebruikt de symbolische naam om eenvoudig te verwijzen naar de bron in andere onderdelen van uw sjabloon.

## <a name="set-resource-name"></a>Resource naam instellen

Elke resource heeft een naam. Let bij het instellen van de resource naam op de [regels en beperkingen voor resource namen](../management/resource-name-rules.md).

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "storageAccountName": {
    "type": "string",
    "minLength": 3,
    "maxLength": 24
  }
},
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2019-06-01",
    "name": "[parameters('storageAccountName')]",
    ...
  }
]
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
@minLength(3)
@maxLength(24)
param storageAccountName string

resource sa 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: storageAccountName
  ...
}
```

---

## <a name="set-location"></a>Locatie instellen

Voor veel resources is een locatie vereist. U kunt bepalen of de resource een locatie nodig heeft via IntelliSense of een [sjabloon verwijzing](/azure/templates/). In het volgende voor beeld wordt een locatie parameter toegevoegd die wordt gebruikt voor het opslag account.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "storageAccountName": {
    "type": "string",
    "minLength": 3,
    "maxLength": 24
  },
  "location": {
    "type": "string",
    "defaultValue": "[resourceGroup().location]"
  }
},
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2019-06-01",
    "name": "[parameters('storageAccountName')]",
    "location": "[parameters('location')]",
    ...
  }
]
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
@minLength(3)
@maxLength(24)
param storageAccountName string
param location string = resourceGroup().location

resource sa 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: storageAccountName
  location: location
  ...
}
```

---

Zie [resource locatie in arm-sjabloon instellen](resource-location.md)voor meer informatie.

## <a name="set-tags"></a>Tags instellen

Tijdens de implementatie kunt u Tags Toep assen op een resource. Met tags kunt u uw geïmplementeerde resources logisch indelen. Zie [arm-sjabloon Tags](../management/tag-resources.md#arm-templates)voor voor beelden van de verschillende manieren waarop u tags kunt opgeven.

## <a name="set-resource-specific-properties"></a>Resource-specifieke eigenschappen instellen

De voor gaande eigenschappen zijn algemeen voor de meeste resource typen. Nadat u deze waarden hebt ingesteld, moet u de eigenschappen instellen die specifiek zijn voor het bron type dat u wilt implementeren.

Gebruik IntelliSense of de verwijzing naar de [sjabloon](/azure/templates/) om te bepalen welke eigenschappen er beschikbaar zijn en welke zijn vereist. In het volgende voor beeld worden de resterende eigenschappen van een opslag account ingesteld.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountName": {
      "type": "string",
      "minLength": 3,
      "maxLength": 24
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]"
    }
  },
  "functions": [],
  "resources": [
    {
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
@minLength(3)
@maxLength(24)
param storageAccountName string
param location string = resourceGroup().location

resource sa 'Microsoft.Storage/storageAccounts@2019-06-01' = {
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

## <a name="next-steps"></a>Volgende stappen

* Zie [voorwaardelijke implementatie in arm-sjablonen](conditional-resource-deployment.md)voor voorwaardelijk implementeren van een resource.
* Zie [de volg orde voor het implementeren van resources in arm-sjablonen definiëren](define-resource-dependency.md)om bron afhankelijkheden in te stellen.
