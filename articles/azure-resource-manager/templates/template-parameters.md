---
title: Parameters in sjablonen
description: Hierin wordt beschreven hoe u parameters definieert in een Azure Resource Manager sjabloon.
ms.topic: conceptual
ms.date: 09/05/2019
ms.openlocfilehash: 89c6984c587e8dae59c1825a99d4f8da1c06dafb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "76122420"
---
# <a name="parameters-in-azure-resource-manager-templates"></a>Parameters in Azure Resource Manager templates (Parameters in Azure Resource Manager-sjablonen)

In dit artikel wordt beschreven hoe u parameters definieert en gebruikt in uw Azure Resource Manager sjabloon. Door verschillende waarden voor parameters op te geven, kunt u een sjabloon gebruiken voor verschillende omgevingen.

Resource Manager zet parameter waarden om voordat de implementatie bewerkingen worden gestart. Wanneer de parameter wordt gebruikt in de sjabloon, wordt deze vervangen door de omgezette waarde.

## <a name="define-parameter"></a>parameter definiëren

In het volgende voor beeld ziet u een eenvoudige parameter definitie. Hiermee wordt een parameter gedefinieerd met de naam **storageSKU**. De parameter is een teken reeks waarde en accepteert alleen waarden die geldig zijn voor het beoogde gebruik. De parameter gebruikt een standaard waarde wanneer er geen waarde wordt gegeven tijdens de implementatie.

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

## <a name="use-parameter"></a>parameter gebruiken

In de sjabloon verwijst u naar de waarde voor de parameter met behulp van de [parameters](template-functions-deployment.md#parameters) -functie. In het volgende voor beeld wordt de waarde van de parameter gebruikt om SKU in te stellen voor het opslag account.

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

Wanneer u de standaard waarde voor een parameter opgeeft, kunt u de meeste sjabloon functies gebruiken. U kunt een andere parameter waarde gebruiken om een standaard waarde te maken. De volgende sjabloon toont het gebruik van functies in de standaard waarde. Als er geen naam wordt gegeven voor de site, maakt deze een unieke teken reeks waarde en voegt deze toe aan de **site**. Als er geen naam wordt gegeven voor het host-abonnement, neemt deze de waarde voor de-site en de **-plan**toe.

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
      "description": "The host plan name. To use the default value, do not specify a new value."
    }
  }
}
```

U kunt de functie [Reference](template-functions-resource.md#reference) of een van de [lijst](template-functions-resource.md#list) functies niet gebruiken in de parameters van de sectie. Deze functies halen de runtime status van een resource en kunnen niet worden uitgevoerd voor implementatie wanneer parameters worden omgezet.

## <a name="objects-as-parameters"></a>Objecten als parameters

Het kan gemakkelijker zijn om verwante waarden te organiseren door ze als een object door te geven. Deze benadering vermindert ook het aantal parameters in de sjabloon.

In het volgende voor beeld ziet u een parameter die een-object is. De standaard waarde toont de verwachte eigenschappen van het object.

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

In de volgende voor beelden ziet u scenario's voor het gebruik van parameters.

|Template  |Beschrijving  |
|---------|---------|
|[parameters met functies voor standaard waarden](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterswithfunctions.json) | Demonstreert hoe u sjabloon functies gebruikt bij het definiëren van standaard waarden voor parameters. De sjabloon implementeert geen resources. Er worden parameter waarden gemaakt en deze waarden worden geretourneerd. |
|[parameter object](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterobject.json) | Laat zien hoe u een object gebruikt voor een parameter. De sjabloon implementeert geen resources. Er worden parameter waarden gemaakt en deze waarden worden geretourneerd. |


## <a name="next-steps"></a>Volgende stappen

* Zie [inzicht krijgen in de structuur en de syntaxis van Azure Resource Manager-sjablonen](template-syntax.md)voor meer informatie over de beschik bare eigenschappen voor parameters.
* Zie [Resource Manager-parameter bestand maken](parameter-files.md)voor meer informatie over het door geven van parameter waarden als een bestand.
* Zie [Best practices-parameters (](template-best-practices.md#parameters)Engelstalig) voor aanbevelingen voor het maken van parameters.
