---
title: Meerdere exemplaren van een eigenschap definiëren
description: Gebruik de kopieerbewerking in een Azure Resource Manager sjabloon (ARM-sjabloon) om meerdere keren te itereren bij het maken van een eigenschap op een resource.
ms.topic: conceptual
ms.date: 04/01/2021
ms.openlocfilehash: 16c293f1c3aff64aeb8b6cae4b7f1aa14dcd0a77
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107479998"
---
# <a name="property-iteration-in-arm-templates"></a>Iteratie van eigenschappen in ARM-sjablonen

In dit artikel wordt beschreven hoe u meer dan één exemplaar van een eigenschap maakt in uw Azure Resource Manager -sjabloon (ARM-sjabloon). Door een kopieerlus toe te voegen aan de sectie Eigenschappen van een resource in uw sjabloon, kunt u het aantal items voor een eigenschap dynamisch instellen tijdens de implementatie. U voorkomt ook dat u sjabloonsyntaxis moet herhalen.

U kunt de kopieerlus alleen gebruiken met resources op het hoogste niveau, zelfs bij het toepassen van een kopieerlus op een eigenschap. Zie [Iteratie voor](copy-resources.md#iteration-for-a-child-resource)een onderliggende resource voor meer informatie over het wijzigen van een onderliggende resource in een resource op het hoogste niveau.

U kunt ook een kopieerlus gebruiken [met resources,](copy-resources.md) [variabelen](copy-variables.md)en [uitvoer.](copy-outputs.md)

## <a name="syntax"></a>Syntax

# <a name="json"></a>[JSON](#tab/json)

Voeg het `copy` element toe aan de sectie Resources van uw sjabloon om het aantal items voor een eigenschap in te stellen. Het kopieerelement heeft de volgende algemene indeling:

```json
"copy": [
  {
    "name": "<name-of-property>",
    "count": <number-of-iterations>,
    "input": <values-for-the-property>
  }
]
```

Geef `name` voor de naam op van de resource-eigenschap die u wilt maken.

De `count` eigenschap geeft het aantal iteraties aan dat u voor de eigenschap wilt gebruiken.

De `input` eigenschap geeft de eigenschappen op die u wilt herhalen. U maakt een matrix met elementen die zijn samengesteld op basis van de waarde in de `input` eigenschap .

# <a name="bicep"></a>[Bicep](#tab/bicep)

Lussen kunnen worden gebruikt om meerdere eigenschappen te declaren door:

- Itereren over een matrix:

  ```bicep
  <property-name>: [for <item> in <collection>: {
    <properties>
  }]
  ```

- De elementen van een matrix itereren

  ```bicep
  <property-name>: [for (<item>, <index>) in <collection>: {
    <properties>
  }]
  ```

- Lusindex gebruiken

  ```bicep
  <property-name>: [for <index> in range(<start>, <stop>): {
    <properties>
  }]
  ```

---

## <a name="copy-limits"></a>Limieten voor kopiëren

Het aantal mag niet groter zijn dan 800.

Het aantal mag geen negatief getal zijn. Dit kan nul zijn als u de sjabloon implementeert met een recente versie van Azure CLI, PowerShell of REST API. U moet met name het volgende gebruiken:

- Azure PowerShell **2.6** of hoger
- Azure CLI **2.0.74** of hoger
- REST API **versie 2019-05-10** of hoger
- [Voor gekoppelde implementaties](linked-templates.md) moet API-versie **2019-05-10** of hoger worden gebruikt voor het resourcetype van de implementatie

Eerdere versies van PowerShell, CLI en de REST API bieden geen ondersteuning voor nul voor aantal.

## <a name="property-iteration"></a>Iteratie van eigenschappen

In het volgende voorbeeld ziet u hoe u een kopieerlus kunt toepassen op `dataDisks` de eigenschap op een virtuele machine:

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

U ziet dat wanneer u `copyIndex` binnen een eigenschaps iteratie gebruikt, u de naam van de iteratie moet geven. Eigenschaps iteratie ondersteunt ook een offsetargument. De offset moet achter de naam van de iteratie komen, zoals `copyIndex('dataDisks', 1)` .

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

De kopieerbewerking is handig bij het werken met matrices, omdat u elk element in de matrix kunt itereren. Gebruik de `length` functie in de matrix om het aantal voor iteraties op te geven en om de huidige index in de matrix op te `copyIndex` halen.

Met de volgende voorbeeldsjabloon maakt u een failovergroep voor databases die worden doorgegeven als een matrix.

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

U kunt resource- en eigenschaps iteratie samen gebruiken. Verwijs naar de eigenschaps iteratie op naam.

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

## <a name="example-templates"></a>Voorbeeldsjablonen

In het volgende voorbeeld ziet u een algemeen scenario voor het maken van meer dan één waarde voor een eigenschap.

|Template  |Beschrijving  |
|---------|---------|
|[VM-implementatie met een variabel aantal gegevensschijven](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-windows-copy-datadisks) |Implementeert verschillende gegevensschijven met een virtuele machine. |

## <a name="next-steps"></a>Volgende stappen

- Zie Zelfstudie: Meerdere resource-exemplaren maken met [ARM-sjablonen voor een zelfstudie.](template-tutorial-create-multiple-instances.md)
- Zie voor ander gebruik van de kopieerlus:
  - [Resource-iteratie in ARM-sjablonen](copy-resources.md)
  - [Variabele iteratie in ARM-sjablonen](copy-variables.md)
  - [Uitvoer-iteratie in ARM-sjablonen](copy-outputs.md)
- Zie Inzicht in de structuur en syntaxis van ARM-sjablonen voor meer informatie over de secties [van een sjabloon.](template-syntax.md)
- Zie Resources implementeren met ARM-sjablonen en Azure PowerShell voor meer informatie [over het implementeren van Azure PowerShell.](deploy-powershell.md)
