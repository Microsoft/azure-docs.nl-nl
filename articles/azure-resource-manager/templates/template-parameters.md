---
title: Para meters in sjablonen
description: Hierin wordt beschreven hoe u para meters definieert in een Azure Resource Manager sjabloon (ARM-sjabloon).
ms.topic: conceptual
ms.date: 11/24/2020
ms.openlocfilehash: 4ac1aeb579040b35b2a9b4cb90fb5687f91ebd3b
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96353507"
---
# <a name="parameters-in-arm-templates"></a>Para meters in ARM-sjablonen

In dit artikel wordt beschreven hoe u para meters definieert en gebruikt in uw Azure Resource Manager sjabloon (ARM-sjabloon). Door verschillende waarden voor para meters op te geven, kunt u een sjabloon gebruiken voor verschillende omgevingen.

Resource Manager zet parameter waarden om voordat de implementatie bewerkingen worden gestart. Wanneer de para meter wordt gebruikt in de sjabloon, wordt deze vervangen door de omgezette waarde.

Elke para meter moet worden ingesteld op een van de [gegevens typen](template-syntax.md#data-types).

## <a name="define-parameter"></a>Para meter definiëren

In het volgende voor beeld ziet u een eenvoudige parameter definitie. Hiermee wordt een para meter gedefinieerd met de naam **storageSKU**. De para meter is een teken reeks waarde en accepteert alleen waarden die geldig zijn voor het beoogde gebruik. De para meter gebruikt een standaard waarde wanneer er geen waarde wordt gegeven tijdens de implementatie.

```json
"parameters": {
  "storageSKU": {
    "type": "string",
    "allowedValues": [
      "Standard_LRS",
      "Standard_ZRS",
      "Standard_GRS",
      "Standard_RAGRS",
      "Premium_LRS"
    ],
    "defaultValue": "Standard_LRS",
    "metadata": {
      "description": "The type of replication to use for the storage account."
    }
  }
}
```

## <a name="use-parameter"></a>Para meter gebruiken

In de sjabloon verwijst u naar de waarde voor de para meter met behulp van de [para meters](template-functions-deployment.md#parameters) -functie. In het volgende voor beeld wordt de waarde van de para meter gebruikt om SKU in te stellen voor het opslag account.

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "sku": {
      "name": "[parameters('storageSKU')]"
    },
    ...
  }
]
```

## <a name="template-functions"></a>Sjabloonfuncties

Wanneer u de standaard waarde voor een para meter opgeeft, kunt u de meeste sjabloon functies gebruiken. U kunt een andere parameter waarde gebruiken om een standaard waarde te maken. De volgende sjabloon toont het gebruik van functies in de standaard waarde. Als er geen naam wordt gegeven voor de site, maakt deze een unieke teken reeks waarde en voegt deze toe aan de **site**. Als er geen naam wordt gegeven voor het host-abonnement, neemt deze de waarde voor de-site en de **-plan** toe.

```json
"parameters": {
  "siteName": {
    "type": "string",
    "defaultValue": "[concat('site', uniqueString(resourceGroup().id))]",
    "metadata": {
      "description": "The site name. To use the default value, do not specify a new value."
    }
  },
  "hostingPlanName": {
    "type": "string",
    "defaultValue": "[concat(parameters('siteName'),'-plan')]",
    "metadata": {
      "description": "The host name. To use the default value, do not specify a new value."
    }
  }
}
```

U kunt de functie [Reference](template-functions-resource.md#reference) of een van de [lijst](template-functions-resource.md#list) functies niet gebruiken in de para meters van de sectie. Deze functies halen de runtime status van een resource en kunnen niet worden uitgevoerd voor implementatie wanneer para meters worden omgezet.

## <a name="objects-as-parameters"></a>Objecten als para meters

Het kan gemakkelijker zijn om verwante waarden te organiseren door ze als een object door te geven. Deze benadering vermindert ook het aantal para meters in de sjabloon.

In het volgende voor beeld ziet u een para meter die een-object is. De standaard waarde toont de verwachte eigenschappen van het object.

```json
"parameters": {
  "VNetSettings": {
    "type": "object",
    "defaultValue": {
      "name": "VNet1",
      "location": "eastus",
      "addressPrefixes": [
        {
          "name": "firstPrefix",
          "addressPrefix": "10.0.0.0/22"
        }
      ],
      "subnets": [
        {
          "name": "firstSubnet",
          "addressPrefix": "10.0.0.0/24"
        },
        {
          "name": "secondSubnet",
          "addressPrefix": "10.0.1.0/24"
        }
      ]
    }
  }
},
```

U verwijst naar de eigenschappen van het object met behulp van de punt operator.

```json
"resources": [
  {
    "type": "Microsoft.Network/virtualNetworks",
    "apiVersion": "2015-06-15",
    "name": "[parameters('VNetSettings').name]",
    "location": "[parameters('VNetSettings').location]",
    "properties": {
      "addressSpace":{
        "addressPrefixes": [
          "[parameters('VNetSettings').addressPrefixes[0].addressPrefix]"
        ]
      },
      "subnets":[
        {
          "name":"[parameters('VNetSettings').subnets[0].name]",
          "properties": {
            "addressPrefix": "[parameters('VNetSettings').subnets[0].addressPrefix]"
          }
        },
        {
          "name":"[parameters('VNetSettings').subnets[1].name]",
          "properties": {
            "addressPrefix": "[parameters('VNetSettings').subnets[1].addressPrefix]"
          }
        }
      ]
    }
  }
]
```

## <a name="example-templates"></a>Voorbeeld sjablonen

In de volgende voor beelden ziet u scenario's voor het gebruik van para meters.

|Template  |Beschrijving  |
|---------|---------|
|[para meters met functies voor standaard waarden](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterswithfunctions.json) | Demonstreert hoe u sjabloon functies gebruikt bij het definiëren van standaard waarden voor para meters. De sjabloon implementeert geen resources. Er worden parameter waarden gemaakt en deze waarden worden geretourneerd. |
|[parameter object](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterobject.json) | Laat zien hoe u een object gebruikt voor een para meter. De sjabloon implementeert geen resources. Er worden parameter waarden gemaakt en deze waarden worden geretourneerd. |

## <a name="next-steps"></a>Volgende stappen

* Zie [inzicht krijgen in de structuur en syntaxis van arm-sjablonen](template-syntax.md)voor meer informatie over de beschik bare eigenschappen voor para meters.
* Zie [Resource Manager-parameter bestand maken](parameter-files.md)voor meer informatie over het door geven van parameter waarden als een bestand.
* Zie [Best practices-para meters (](template-best-practices.md#parameters)Engelstalig) voor aanbevelingen voor het maken van para meters.
