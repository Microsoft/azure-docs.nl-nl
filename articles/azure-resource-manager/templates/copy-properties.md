---
title: Meerdere exemplaren van een eigenschap definiëren
description: Kopieer bewerkingen gebruiken in een Azure Resource Manager sjabloon (ARM-sjabloon) om meerdere keren te herhalen bij het maken van een eigenschap in een resource.
ms.topic: conceptual
ms.date: 04/01/2021
ms.openlocfilehash: 94bc153a49f80694ab9b2d5b04fdf57e8a12e8c8
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/05/2021
ms.locfileid: "106385748"
---
# <a name="property-iteration-in-arm-templates"></a>Eigenschaps herhaling in ARM-sjablonen

In dit artikel wordt beschreven hoe u meer dan één exemplaar van een eigenschap in uw Azure Resource Manager sjabloon maakt (ARM-sjabloon). Door een kopieer proces toe te voegen aan de sectie eigenschappen van een resource in uw sjabloon, kunt u het aantal items voor een eigenschap tijdens de implementatie dynamisch instellen. U hoeft ook geen sjabloon syntaxis te herhalen.

U kunt Copy-lus alleen gebruiken met resources op het hoogste niveau, zelfs wanneer u een copy-lus toepast op een eigenschap. Zie [iteratie voor een onderliggende resource](copy-resources.md#iteration-for-a-child-resource)voor meer informatie over het wijzigen van een onderliggende resource in een resource op het hoogste niveau.

U kunt ook een copy-lus gebruiken met [resources](copy-resources.md), [variabelen](copy-variables.md)en [uitvoer](copy-outputs.md).

## <a name="syntax"></a>Syntax

# <a name="json"></a>[JSON](#tab/json)

Voeg het `copy` element toe aan de sectie resources van uw sjabloon om het aantal items voor een eigenschap in te stellen. Het element Copy heeft de volgende algemene indeling:

```json
"copy": [
  {
    "name": "<name-of-property>",
    "count": <number-of-iterations>,
    "input": <values-for-the-property>
  }
]
```

`name`Geef voor de naam op van de resource-eigenschap die u wilt maken.

De `count` eigenschap geeft het aantal iteraties op dat u voor de eigenschap wilt.

`input`Met de eigenschap geeft u de eigenschappen op die u wilt herhalen. U maakt een matrix van elementen die zijn gemaakt op basis van de waarde in de `input` eigenschap.

# <a name="bicep"></a>[Bicep](#tab/bicep)

Lussen kunnen worden gebruikt voor het declareren van meerdere eigenschappen per:

- Een matrix herhalen:

  ```bicep
  <property-name>: [for <item> in <collection>: {
    <properties>
  }
  ```

- De elementen van een matrix herhalen

  ```bicep
  <property-name>: [for (<item>, <index>) in <collection>: {
    <properties>
  }
  ```

- Loop-index gebruiken

  ```bicep
  <property-name>: [for <index> in range(<start>, <stop>): {
    <properties>
  }
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

## <a name="property-iteration"></a>Eigenschaps herhaling

In het volgende voor beeld ziet u hoe u een copy-lus toepast op de `dataDisks` eigenschap op een virtuele machine:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "numberOfDataDisks": {
      "type": "int",
      "minValue": 0,
      "maxValue": 16,
      "defaultValue": 3,
      "metadata": {
        "description": "The number of dataDisks to create."
      }
    },
    ...
  },
  "resources": [
    {
      "type": "Microsoft.Compute/virtualMachines",
      "apiVersion": "2020-06-01",
      ...
      "properties": {
        "storageProfile": {
          ...
          "copy": [
            {
              "name": "dataDisks",
              "count": "[parameters('numberOfDataDisks')]",
              "input": {
                "lun": "[copyIndex('dataDisks')]",
                "createOption": "Empty",
                "diskSizeGB": 1023
              }
            }
          ]
        }
        ...
      }
    }
  ]
}
```

U ziet dat wanneer `copyIndex` u een eigenschaps herhaling gebruikt, de naam van de herhaling moet opgeven. Eigenschaps herhaling ondersteunt ook een argument Offset. De offset moet worden opgegeven na de naam van de herhaling, zoals `copyIndex('dataDisks', 1)` .

De geïmplementeerde sjabloon wordt:

```json
{
  "name": "examplevm",
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2020-06-01",
  "properties": {
    "storageProfile": {
      "dataDisks": [
        {
          "lun": 0,
          "createOption": "Empty",
          "diskSizeGB": 1023
        },
        {
          "lun": 1,
          "createOption": "Empty",
          "diskSizeGB": 1023
        },
        {
          "lun": 2,
          "createOption": "Empty",
          "diskSizeGB": 1023
        }
      ],
      ...
```

De Kopieer bewerking is handig bij het werken met matrices, omdat u elk element in de matrix kunt door lopen. Gebruik de `length` functie op de matrix om het aantal voor herhalingen op te geven en `copyIndex` om de huidige index in de matrix op te halen.

Met de volgende voorbeeld sjabloon wordt een failovergroep gemaakt voor data bases die worden door gegeven als een matrix.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "primaryServerName": {
      "type": "string"
    },
    "secondaryServerName": {
      "type": "string"
    },
    "databaseNames": {
      "type": "array",
      "defaultValue": [
        "mydb1",
        "mydb2",
        "mydb3"
      ]
    }
  },
  "variables": {
    "failoverName": "[concat(parameters('primaryServerName'),'/', parameters('primaryServerName'),'failovergroups')]"
  },
  "resources": [
    {
      "type": "Microsoft.Sql/servers/failoverGroups",
      "apiVersion": "2015-05-01-preview",
      "name": "[variables('failoverName')]",
      "properties": {
        "readWriteEndpoint": {
          "failoverPolicy": "Automatic",
          "failoverWithDataLossGracePeriodMinutes": 60
        },
        "readOnlyEndpoint": {
          "failoverPolicy": "Disabled"
        },
        "partnerServers": [
          {
            "id": "[resourceId('Microsoft.Sql/servers', parameters('secondaryServerName'))]"
          }
        ],
        "copy": [
          {
            "name": "databases",
            "count": "[length(parameters('databaseNames'))]",
            "input": "[resourceId('Microsoft.Sql/servers/databases', parameters('primaryServerName'), parameters('databaseNames')[copyIndex('databases')])]"
          }
        ]
      }
    }
  ],
  "outputs": {
  }
}
```

Het `copy` element is een matrix, zodat u meer dan één eigenschap voor de resource kunt opgeven.

```json
{
  "type": "Microsoft.Network/loadBalancers",
  "apiVersion": "2017-10-01",
  "name": "exampleLB",
  "properties": {
    "copy": [
      {
        "name": "loadBalancingRules",
        "count": "[length(parameters('loadBalancingRules'))]",
        "input": {
          ...
        }
      },
      {
        "name": "probes",
        "count": "[length(parameters('loadBalancingRules'))]",
        "input": {
          ...
        }
      }
    ]
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
@minValue(0)
@maxValue(16)
@description('The number of dataDisks to be returned in the output array.')
param numberOfDataDisks int = 16

resource vmName 'Microsoft.Compute/virtualMachines@2020-06-01' = {
  ...
  properties: {
    storageProfile: {
      ...
      dataDisks: [for i in range(0, numberOfDataDisks): {
        lun: i
        createOption: 'Empty'
        diskSizeGB: 1023
      }]
    }
    ...
  }
}
```

De geïmplementeerde sjabloon wordt:

```json
{
  "name": "examplevm",
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2020-06-01",
  "properties": {
    "storageProfile": {
      "dataDisks": [
        {
          "lun": 0,
          "createOption": "Empty",
          "diskSizeGB": 1023
        },
        {
          "lun": 1,
          "createOption": "Empty",
          "diskSizeGB": 1023
        },
        {
          "lun": 2,
          "createOption": "Empty",
          "diskSizeGB": 1023
        }
      ],
      ...
```

---

U kunt resource en eigenschaps herhaling samen gebruiken. Verwijzing naar eigenschaps herhaling op naam.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "type": "Microsoft.Network/virtualNetworks",
  "apiVersion": "2018-04-01",
  "name": "[concat(parameters('vnetname'), copyIndex())]",
  "copy":{
    "count": 2,
    "name": "vnetloop"
  },
  "location": "[resourceGroup().location]",
  "properties": {
    "addressSpace": {
      "addressPrefixes": [
        "[parameters('addressPrefix')]"
      ]
    },
    "copy": [
      {
        "name": "subnets",
        "count": 2,
        "input": {
          "name": "[concat('subnet-', copyIndex('subnets'))]",
          "properties": {
            "addressPrefix": "[variables('subnetAddressPrefix')[copyIndex('subnets')]]"
          }
        }
      }
    ]
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
resource vnetname_resource 'Microsoft.Network/virtualNetworks@2018-04-01' = [for i in range(0, 2): {
  name: concat(vnetname, i)
  location: resourceGroup().location
  properties: {
    addressSpace: {
      addressPrefixes: [
        addressPrefix
      ]
    }
    subnets: [for j in range(0, 2): {
      name: 'subnet-${j}'
      properties: {
        addressPrefix: subnetAddressPrefix[j]
      }
    }]
  }
}]
```

---

## <a name="example-templates"></a>Voorbeeld sjablonen

In het volgende voor beeld ziet u een veelvoorkomend scenario voor het maken van meer dan één waarde voor een eigenschap.

|Template  |Beschrijving  |
|---------|---------|
|[VM-implementatie met een variabele aantal gegevens schijven](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-windows-copy-datadisks) |Hiermee worden verschillende gegevens schijven met een virtuele machine geïmplementeerd. |

## <a name="next-steps"></a>Volgende stappen

- Zie [zelf studie: meerdere resource-instanties maken met arm-sjablonen](template-tutorial-create-multiple-instances.md)om een zelf studie te door lopen.
- Zie voor andere toepassingen van de Kopieer lus:
  - [Resource iteratie in ARM-sjablonen](copy-resources.md)
  - [Variabele herhaling in ARM-sjablonen](copy-variables.md)
  - [Uitvoer herhaling in ARM-sjablonen](copy-outputs.md)
- Zie [inzicht in de structuur en syntaxis van arm-sjablonen](template-syntax.md)als u meer wilt weten over de secties van een sjabloon.
- Zie [resources implementeren met arm-sjablonen en Azure PowerShell](deploy-powershell.md)voor meer informatie over het implementeren van uw sjabloon.
