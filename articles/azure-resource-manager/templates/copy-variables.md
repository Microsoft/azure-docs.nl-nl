---
title: Meerdere exemplaren van een variabele definiëren
description: Kopieer bewerking in een Azure Resource Manager sjabloon (ARM-sjabloon) gebruiken om meerdere keren te herhalen bij het maken van een variabele.
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: e1f6dfeb7b701b09ad1a9505d5dbcfddf2cd6b0b
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/05/2021
ms.locfileid: "106385701"
---
# <a name="variable-iteration-in-arm-templates"></a>Variabele herhaling in ARM-sjablonen

In dit artikel wordt beschreven hoe u meer dan één waarde voor een variabele in uw Azure Resource Manager-sjabloon maakt (ARM-sjabloon). Door het element toe te voegen `copy` aan de sectie variabelen van uw sjabloon, kunt u het aantal items voor een variabele tijdens de implementatie dynamisch instellen. U hoeft ook geen sjabloon syntaxis te herhalen.

U kunt ook kopiëren met [resources](copy-resources.md), [Eigenschappen in een resource](copy-properties.md)en [uitvoer](copy-outputs.md)gebruiken.

## <a name="syntax"></a>Syntax

Het element Copy heeft de volgende algemene indeling:

```json
"copy": [
  {
    "name": "<name-of-loop>",
    "count": <number-of-iterations>,
    "input": <values-for-the-variable>
  }
]
```

De `name` eigenschap is een wille keurige waarde die de lus identificeert. De `count` eigenschap geeft het aantal iteraties op dat u voor de variabele wilt.

`input`Met de eigenschap geeft u de eigenschappen op die u wilt herhalen. U maakt een matrix van elementen die zijn gemaakt op basis van de waarde in de `input` eigenschap. Dit kan één eigenschap zijn (zoals een teken reeks) of een object met verschillende eigenschappen.

## <a name="copy-limits"></a>Limieten kopiëren

De telling mag niet groter zijn dan 800.

De telling kan geen negatief getal zijn. Dit kan nul zijn als u de sjabloon implementeert met een recente versie van Azure CLI, Power shell of REST API. U moet het volgende gebruiken:

- Azure PowerShell **2,6** of hoger
- Azure CLI **2.0.74** of hoger
- REST API versie **2019-05-10** of hoger
- [Gekoppelde implementaties](linked-templates.md) moeten API-versie **2019-05-10** of hoger voor het bron type implementatie gebruiken

Eerdere versies van Power shell, CLI en de REST API bieden geen ondersteuning voor aantal nul.

## <a name="variable-iteration"></a>Variabele herhaling

In het volgende voor beeld ziet u hoe u een matrix van teken reeks waarden maakt:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "itemCount": {
      "type": "int",
      "defaultValue": 5
    }
  },
  "variables": {
    "copy": [
      {
        "name": "stringArray",
        "count": "[parameters('itemCount')]",
        "input": "[concat('item', copyIndex('stringArray', 1))]"
      }
    ]
  },
  "resources": [],
  "outputs": {
    "arrayResult": {
      "type": "array",
      "value": "[variables('stringArray')]"
    }
  }
}
```

De vorige sjabloon retourneert een matrix met de volgende waarden:

```json
[
  "item1",
  "item2",
  "item3",
  "item4",
  "item5"
]
```

In het volgende voor beeld ziet u hoe u een matrix met objecten maakt met drie eigenschappen: `name` , `diskSizeGB` en `diskIndex` .

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "itemCount": {
      "type": "int",
      "defaultValue": 5
    }
  },
  "variables": {
    "copy": [
      {
        "name": "objectArray",
        "count": "[parameters('itemCount')]",
        "input": {
          "name": "[concat('myDataDisk', copyIndex('objectArray', 1))]",
          "diskSizeGB": "1",
          "diskIndex": "[copyIndex('objectArray')]"
        }
      }
    ]
  },
  "resources": [],
  "outputs": {
    "arrayResult": {
      "type": "array",
      "value": "[variables('objectArray')]"
    }
  }
}
```

In het voor gaande voor beeld wordt een matrix met de volgende waarden geretourneerd:

```json
[
  {
    "name": "myDataDisk1",
    "diskSizeGB": "1",
    "diskIndex": 0
  },
  {
    "name": "myDataDisk2",
    "diskSizeGB": "1",
    "diskIndex": 1
  },
  {
    "name": "myDataDisk3",
    "diskSizeGB": "1",
    "diskIndex": 2
  },
  {
    "name": "myDataDisk4",
    "diskSizeGB": "1",
    "diskIndex": 3
  },
  {
    "name": "myDataDisk5",
    "diskSizeGB": "1",
    "diskIndex": 4
  }
]
```

> [!NOTE]
> Variabele herhaling ondersteunt het argument Offset. De offset moet worden opgegeven na de naam van de herhaling, zoals `copyIndex('diskNames', 1)` . Als u geen offset waarde opgeeft, wordt het standaard ingesteld op 0 voor het eerste exemplaar.
>

U kunt ook het `copy` element binnen een variabele gebruiken. In het volgende voor beeld wordt een object gemaakt dat een matrix heeft als een van de waarden.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "itemCount": {
      "type": "int",
      "defaultValue": 5
    }
  },
  "variables": {
    "topLevelObject": {
      "sampleProperty": "sampleValue",
      "copy": [
        {
          "name": "disks",
          "count": "[parameters('itemCount')]",
          "input": {
            "name": "[concat('myDataDisk', copyIndex('disks', 1))]",
            "diskSizeGB": "1",
            "diskIndex": "[copyIndex('disks')]"
          }
        }
      ]
    }
  },
  "resources": [],
  "outputs": {
    "objectResult": {
      "type": "object",
      "value": "[variables('topLevelObject')]"
    }
  }
}
```

In het voor gaande voor beeld wordt een object geretourneerd met de volgende waarden:

```json
{
  "sampleProperty": "sampleValue",
  "disks": [
    {
      "name": "myDataDisk1",
      "diskSizeGB": "1",
      "diskIndex": 0
    },
    {
      "name": "myDataDisk2",
      "diskSizeGB": "1",
      "diskIndex": 1
    },
    {
      "name": "myDataDisk3",
      "diskSizeGB": "1",
      "diskIndex": 2
    },
    {
      "name": "myDataDisk4",
      "diskSizeGB": "1",
      "diskIndex": 3
    },
    {
      "name": "myDataDisk5",
      "diskSizeGB": "1",
      "diskIndex": 4
    }
  ]
}
```

In het volgende voor beeld ziet u de verschillende manieren waarop u `copy` met variabelen kunt werken.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {
    "disk-array-on-object": {
      "copy": [
        {
          "name": "disks",
          "count": 5,
          "input": {
            "name": "[concat('myDataDisk', copyIndex('disks', 1))]",
            "diskSizeGB": "1",
            "diskIndex": "[copyIndex('disks')]"
          }
        },
        {
          "name": "diskNames",
          "count": 5,
          "input": "[concat('myDataDisk', copyIndex('diskNames', 1))]"
        }
      ]
    },
    "copy": [
      {
        "name": "top-level-object-array",
        "count": 5,
        "input": {
          "name": "[concat('myDataDisk', copyIndex('top-level-object-array', 1))]",
          "diskSizeGB": "1",
          "diskIndex": "[copyIndex('top-level-object-array')]"
        }
      },
      {
        "name": "top-level-string-array",
        "count": 5,
        "input": "[concat('myDataDisk', copyIndex('top-level-string-array', 1))]"
      },
      {
        "name": "top-level-integer-array",
        "count": 5,
        "input": "[copyIndex('top-level-integer-array')]"
      }
    ]
  },
  "resources": [],
  "outputs": {
    "exampleObject": {
      "value": "[variables('disk-array-on-object')]",
      "type": "object"
    },
    "exampleArrayOnObject": {
      "value": "[variables('disk-array-on-object').disks]",
      "type" : "array"
    },
    "exampleObjectArray": {
      "value": "[variables('top-level-object-array')]",
      "type" : "array"
    },
    "exampleStringArray": {
      "value": "[variables('top-level-string-array')]",
      "type" : "array"
    },
    "exampleIntegerArray": {
      "value": "[variables('top-level-integer-array')]",
      "type" : "array"
    }
  }
}
```

## <a name="example-templates"></a>Voorbeeld sjablonen

In de volgende voor beelden ziet u algemene scenario's voor het maken van meer dan één waarde voor een variabele.

|Template  |Beschrijving  |
|---------|---------|
|[Variabelen kopiëren](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copyvariables.json) |Demonstreert de verschillende manieren om variabelen te herhalen. |
|[Meerdere beveiligings regels](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) |Implementeert diverse beveiligings regels voor een netwerk beveiligings groep. Hiermee worden de beveiligings regels van een para meter gemaakt. Zie [meerdere NSG-parameter bestanden](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json)voor de para meter. |

## <a name="next-steps"></a>Volgende stappen

- Zie [zelf studie: meerdere resource-instanties maken met arm-sjablonen](template-tutorial-create-multiple-instances.md)om een zelf studie te door lopen.
- Zie voor andere toepassingen van het element copy:
  - [Resource iteratie in ARM-sjablonen](copy-resources.md)
  - [Eigenschaps herhaling in ARM-sjablonen](copy-properties.md)
  - [Uitvoer herhaling in ARM-sjablonen](copy-outputs.md)
- Zie [inzicht in de structuur en syntaxis van arm-sjablonen](template-syntax.md)als u meer wilt weten over de secties van een sjabloon.
- Zie [resources implementeren met arm-sjablonen en Azure PowerShell](deploy-powershell.md)voor meer informatie over het implementeren van uw sjabloon.
