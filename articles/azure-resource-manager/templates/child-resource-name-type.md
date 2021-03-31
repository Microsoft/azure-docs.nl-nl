---
title: Onderliggende resources in sjablonen
description: Hierin wordt beschreven hoe u de naam en het type instelt voor onderliggende resources in een Azure Resource Manager sjabloon (ARM-sjabloon).
ms.topic: conceptual
ms.date: 12/21/2020
ms.openlocfilehash: a950d72751b829c0a2aa3ba5ca27316a0544d9cc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97963909"
---
# <a name="set-name-and-type-for-child-resources"></a>Naam en type voor onderliggende resources instellen

Onderliggende resources zijn bronnen die alleen bestaan in de context van een andere resource. Een extensie van een [virtuele machine](/azure/templates/microsoft.compute/virtualmachines/extensions) kan bijvoorbeeld niet bestaan zonder een [virtuele machine](/azure/templates/microsoft.compute/virtualmachines). De extensie resource is een onderliggend element van de virtuele machine.

Elke bovenliggende resource accepteert alleen bepaalde resource typen als onderliggende resources. Het resource type voor de onderliggende resource bevat het resource type voor de bovenliggende resource. `Microsoft.Web/sites/config`En `Microsoft.Web/sites/extensions` zijn bijvoorbeeld de onderliggende resources van `Microsoft.Web/sites` . De geaccepteerde resource typen worden opgegeven in het [sjabloon schema](https://github.com/Azure/azure-resource-manager-schemas) van de bovenliggende resource.

In een Azure Resource Manager sjabloon (ARM-sjabloon) kunt u de onderliggende bron opgeven binnen de bovenliggende resource of buiten de bovenliggende resource. In het volgende voor beeld ziet u de onderliggende resource die is opgenomen in de eigenschap resources van de bovenliggende resource.

```json
"resources": [
  {
    <parent-resource>
    "resources": [
      <child-resource>
    ]
  }
]
```

Onderliggende resources kunnen alleen vijf niveaus diep worden gedefinieerd.

In het volgende voor beeld wordt de onderliggende resource buiten de bovenliggende resource weer gegeven. U kunt deze aanpak gebruiken als de bovenliggende resource niet is geïmplementeerd in dezelfde sjabloon of als u een [kopie](copy-resources.md) wilt gebruiken om meer dan één onderliggende resource te maken.

```json
"resources": [
  {
    <parent-resource>
  },
  {
    <child-resource>
  }
]
```

De waarden die u opgeeft voor de resource naam en het-type variëren afhankelijk van het feit of de onderliggende resource binnen of buiten de bovenliggende resource is gedefinieerd.

## <a name="within-parent-resource"></a>In bovenliggende resource

Wanneer u de waarde definieert in het bovenliggende resource type, formatteert u het type en de naam waarden als één enkel woord zonder slashes.

```json
"type": "{child-resource-type}",
"name": "{child-resource-name}",
```

In het volgende voor beeld ziet u een virtueel netwerk en een subnet. U ziet dat het subnet is opgenomen in de bronnen matrix voor het virtuele netwerk. De naam is ingesteld op **Subnet1** en het type is ingesteld op **subnetten**. De onderliggende resource is gemarkeerd als afhankelijk van de bovenliggende resource, omdat de bovenliggende resource moet bestaan voordat de onderliggende resource kan worden geïmplementeerd.

```json
"resources": [
  {
    "type": "Microsoft.Network/virtualNetworks",
    "apiVersion": "2018-10-01",
    "name": "VNet1",
    "location": "[parameters('location')]",
    "properties": {
      "addressSpace": {
        "addressPrefixes": [
          "10.0.0.0/16"
        ]
      }
    },
    "resources": [
      {
        "type": "subnets",
        "apiVersion": "2018-10-01",
        "name": "Subnet1",
        "location": "[parameters('location')]",
        "dependsOn": [
          "VNet1"
        ],
        "properties": {
          "addressPrefix": "10.0.0.0/24"
        }
      }
    ]
  }
]
```

Het volledige resource type is nog steeds `Microsoft.Network/virtualNetworks/subnets` . U geeft niet `Microsoft.Network/virtualNetworks/` op omdat deze wordt verondersteld uit het bovenliggende resource type.

De naam van de onderliggende resource is ingesteld op **Subnet1** , maar de volledige naam bevat de naam van het bovenliggende item. U geeft **VNet1** niet op omdat deze wordt aangenomen van de bovenliggende resource.

## <a name="outside-parent-resource"></a>Buiten bovenliggende resource

Wanneer u buiten de bovenliggende resource hebt gedefinieerd, formatteert u het type en met slashes om het bovenliggende type en de naam op te laten bevatten.

```json
"type": "{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}",
"name": "{parent-resource-name}/{child-resource-name}",
```

In het volgende voor beeld ziet u een virtueel netwerk en een subnet dat beide zijn gedefinieerd op het hoofd niveau. U ziet dat het subnet niet is opgenomen in de bronnen matrix voor het virtuele netwerk. De naam is ingesteld op **VNet1/Subnet1** en het type is ingesteld op `Microsoft.Network/virtualNetworks/subnets` . De onderliggende resource is gemarkeerd als afhankelijk van de bovenliggende resource, omdat de bovenliggende resource moet bestaan voordat de onderliggende resource kan worden geïmplementeerd.

```json
"resources": [
  {
    "type": "Microsoft.Network/virtualNetworks",
    "apiVersion": "2018-10-01",
    "name": "VNet1",
    "location": "[parameters('location')]",
    "properties": {
      "addressSpace": {
        "addressPrefixes": [
          "10.0.0.0/16"
        ]
      }
    }
  },
  {
    "type": "Microsoft.Network/virtualNetworks/subnets",
    "apiVersion": "2018-10-01",
    "location": "[parameters('location')]",
    "name": "VNet1/Subnet1",
    "dependsOn": [
      "VNet1"
    ],
    "properties": {
      "addressPrefix": "10.0.0.0/24"
    }
  }
]
```

## <a name="next-steps"></a>Volgende stappen

* Zie [inzicht krijgen in de structuur en syntaxis van arm-sjablonen](template-syntax.md)voor meer informatie over het maken van arm-sjablonen.
* Zie de [functie Reference](template-functions-resource.md#reference)voor meer informatie over de indeling van de resource naam bij het verwijzen naar de resource.
