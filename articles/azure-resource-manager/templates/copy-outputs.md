---
title: Meerdere exemplaren van een uitvoer waarde definiëren
description: Gebruik een Kopieer bewerking in een Azure Resource Manager sjabloon (ARM-sjabloon) om meerdere keren te herhalen wanneer een waarde uit een implementatie wordt geretourneerd.
ms.topic: conceptual
ms.date: 04/01/2021
ms.openlocfilehash: 49050f4c0a494bbfb470b64704a09d8738a727f7
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/05/2021
ms.locfileid: "106385731"
---
# <a name="output-iteration-in-arm-templates"></a>Uitvoer herhaling in ARM-sjablonen

In dit artikel wordt beschreven hoe u meer dan één waarde voor een uitvoer in uw Azure Resource Manager-sjabloon maakt (ARM-sjabloon). Door een kopieer proces toe te voegen aan de uitvoer sectie van uw sjabloon, kunt u tijdens de implementatie dynamisch een aantal items retour neren.

U kunt ook copy-lus gebruiken met [resources](copy-resources.md), [Eigenschappen in een resource](copy-properties.md)en [variabelen](copy-variables.md).

## <a name="syntax"></a>Syntax

# <a name="json"></a>[JSON](#tab/json)

Voeg het `copy` element toe aan de sectie uitvoer van uw sjabloon om een aantal items te retour neren. Het element Copy heeft de volgende algemene indeling:

```json
"copy": {
  "count": <number-of-iterations>,
  "input": <values-for-the-output>
}
```

De `count` eigenschap geeft het aantal iteraties op dat u voor de uitvoer waarde wilt.

`input`Met de eigenschap geeft u de eigenschappen op die u wilt herhalen. U maakt een matrix van elementen die zijn gemaakt op basis van de waarde in de `input` eigenschap. Dit kan één eigenschap zijn (zoals een teken reeks) of een object met verschillende eigenschappen.

# <a name="bicep"></a>[Bicep](#tab/bicep)

Lussen kunnen worden gebruikt om tijdens de implementatie een aantal items te retour neren:

- Een matrix herhalen:

  ```bicep
  output <output-name> array = [for <item> in <collection>: {
    <properties>
  }]

  ```

- De elementen van een matrix herhalen

  ```bicep
  output <output-name> array = [for <item>, <index> in <collection>: {
    <properties>
  }]
  ```

- Loop-index gebruiken

  ```bicep
  output <output-name> array = [for <index> in range(<start>, <stop>): {
    <properties>
  }]
  ```

---

## <a name="copy-limits"></a>Limieten kopiëren

De telling mag niet groter zijn dan 800.

De telling kan geen negatief getal zijn. Dit kan nul zijn als u de sjabloon implementeert met een recente versie van Azure CLI, Power shell of REST API. U moet het volgende gebruiken:

- Azure PowerShell **2,6** of hoger
- Azure CLI **2.0.74** of hoger
- REST API versie **2019-05-10** of hoger
- [Gekoppelde implementaties](linked-templates.md) moeten API-versie **2019-05-10** of hoger voor het bron type implementatie gebruiken

Eerdere versies van Power shell, CLI en de REST API bieden geen ondersteuning voor aantal nul.

## <a name="outputs-iteration"></a>Herhalingen van uitvoer

In het volgende voor beeld wordt een variabele aantal opslag accounts gemaakt en wordt een eind punt voor elk opslag account geretourneerd:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageCount": {
      "type": "int",
      "defaultValue": 2
    }
  },
  "variables": {
    "baseName": "[concat('storage', uniqueString(resourceGroup().id))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[concat(copyIndex(), variables('baseName'))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": {},
      "copy": {
        "name": "storagecopy",
        "count": "[parameters('storageCount')]"
      }
    }
  ],
  "outputs": {
    "storageEndpoints": {
      "type": "array",
      "copy": {
        "count": "[parameters('storageCount')]",
        "input": "[reference(concat(copyIndex(), variables('baseName'))).primaryEndpoints.blob]"
      }
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param storageCount int = 2

var baseName_var = 'storage${uniqueString(resourceGroup().id)}'

resource baseName 'Microsoft.Storage/storageAccounts@2019-04-01' = [for i in range(0, storageCount): {
  name: '${i}${baseName_var}'
  location: resourceGroup().location
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'Storage'
  properties: {}
}]

output storageEndpoints array = [for i in range(0, storageCount): reference(${i}${baseName_var}).primaryEndpoints.blob]
```

---

De vorige sjabloon retourneert een matrix met de volgende waarden:

```json
[
  "https://0storagecfrbqnnmpeudi.blob.core.windows.net/",
  "https://1storagecfrbqnnmpeudi.blob.core.windows.net/"
]
```

In het volgende voor beeld worden drie eigenschappen van de nieuwe opslag accounts geretourneerd.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageCount": {
      "type": "int",
      "defaultValue": 2
    }
  },
  "variables": {
    "baseName": "[concat('storage', uniqueString(resourceGroup().id))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[concat(copyIndex(), variables('baseName'))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": {},
      "copy": {
        "name": "storagecopy",
        "count": "[parameters('storageCount')]"
      }
    }
  ],
  "outputs": {
    "storageInfo": {
      "type": "array",
      "copy": {
        "count": "[parameters('storageCount')]",
        "input": {
          "id": "[reference(concat(copyIndex(), variables('baseName')), '2019-04-01', 'Full').resourceId]",
          "blobEndpoint": "[reference(concat(copyIndex(), variables('baseName'))).primaryEndpoints.blob]",
          "status": "[reference(concat(copyIndex(), variables('baseName'))).statusOfPrimary]"
        }
      }
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param storageCount int = 2

var baseName_var = 'storage${uniqueString(resourceGroup().id)}'

resource baseName 'Microsoft.Storage/storageAccounts@2019-04-01' = [for i in range(0, storageCount): {
  name: '${i}${baseName_var}'
  location: resourceGroup().location
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'Storage'
  properties: {}
}]

output storageInfo array = [for i in range(0, storageCount): {
  id: reference(concat(i, baseName_var), '2019-04-01', 'Full').resourceId
  blobEndpoint: reference(concat(i, baseName_var)).primaryEndpoints.blob
  status: reference(concat(i, baseName_var)).statusOfPrimary
}]
```

---

In het voor gaande voor beeld wordt een matrix met de volgende waarden geretourneerd:

```json
[
  {
    "id": "Microsoft.Storage/storageAccounts/0storagecfrbqnnmpeudi",
    "blobEndpoint": "https://0storagecfrbqnnmpeudi.blob.core.windows.net/",
    "status": "available"
  },
  {
    "id": "Microsoft.Storage/storageAccounts/1storagecfrbqnnmpeudi",
    "blobEndpoint": "https://1storagecfrbqnnmpeudi.blob.core.windows.net/",
    "status": "available"
  }
]
```

## <a name="next-steps"></a>Volgende stappen

- Zie [zelf studie: meerdere resource-instanties maken met arm-sjablonen](template-tutorial-create-multiple-instances.md)om een zelf studie te door lopen.
- Zie voor andere toepassingen van de Kopieer lus:
  - [Resource iteratie in ARM-sjablonen](copy-resources.md)
  - [Eigenschaps herhaling in ARM-sjablonen](copy-properties.md)
  - [Variabele herhaling in ARM-sjablonen](copy-variables.md)
- Zie [inzicht in de structuur en syntaxis van arm-sjablonen](template-syntax.md)als u meer wilt weten over de secties van een sjabloon.
- Zie [resources implementeren met arm-sjablonen en Azure PowerShell](deploy-powershell.md)voor meer informatie over het implementeren van uw sjabloon.
