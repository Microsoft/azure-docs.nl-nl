---
title: Para meters in sjablonen
description: Hierin wordt beschreven hoe u para meters definieert in een Azure Resource Manager sjabloon (ARM-sjabloon) en Bicep-bestand.
ms.topic: conceptual
ms.date: 02/22/2021
ms.openlocfilehash: 3b5da4b14fc338ba81be39d1e3ff6965294f0a0b
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101710631"
---
# <a name="parameters-in-arm-templates"></a>Para meters in ARM-sjablonen

In dit artikel wordt beschreven hoe u para meters definieert en gebruikt in uw Azure Resource Manager sjabloon (ARM-sjabloon) en Bicep-bestand. Door verschillende waarden voor para meters op te geven, kunt u een sjabloon gebruiken voor verschillende omgevingen.

Resource Manager zet parameter waarden om voordat de implementatie bewerkingen worden gestart. Wanneer de para meter wordt gebruikt in de sjabloon, wordt deze vervangen door de omgezette waarde.

Elke para meter moet worden ingesteld op een van de [gegevens typen](template-syntax.md#data-types).

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="minimal-declaration"></a>Minimale declaratie

Elke para meter moet mini maal een naam en type hebben. In Bicep kan een para meter niet dezelfde naam hebben als een variabele, resource, uitvoer of andere para meter in hetzelfde bereik.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "demoString": {
    "type": "string"
  },
  "demoInt": {
    "type": "int"
  },
  "demoBool": {
    "type": "bool"
  },
  "demoObject": {
    "type": "object"
  },
  "demoArray": {
    "type": "array"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param demoString string
param demoInt int
param demoBool bool
param demoObject object
param demoArray array
```

---

## <a name="secure-parameters"></a>Veilige para meters

U kunt teken reeks-of object parameters als beveiligd markeren. De waarde van een beveiligde para meter wordt niet opgeslagen in de implementatie geschiedenis en wordt niet geregistreerd.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "demoPassword": {
    "type": "secureString"
  },
  "demoSecretObject": {
    "type": "secureObject"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
@secure()
param demoPassword string

@secure()
param demoSecretObject object
```

---

## <a name="allowed-values"></a>Toegestane waarden

U kunt toegestane waarden definiëren voor een para meter. U geeft de toegestane waarden op in een matrix. De implementatie mislukt tijdens de validatie als er een waarde wordt door gegeven voor de para meter die niet van toepassing is op een van de toegestane waarden.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "demoEnum": {
    "type": "string",
    "allowedValues": [
      "one",
      "two"
    ]
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
@allowed([
  'one'
  'two'
])
param demoEnum string
```

---

## <a name="default-value"></a>Standaardwaarde

U kunt een standaard waarde voor een para meter opgeven. De standaard waarde wordt gebruikt wanneer er geen waarde wordt gegeven tijdens de implementatie.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "demoParam": {
    "type": "string",
    "defaultValue": "Contoso"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param demoParam string = 'Contoso'
```

---

Gebruik de volgende syntaxis om een standaard waarde samen met andere eigenschappen voor de para meter op te geven.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "demoParam": {
    "type": "string",
    "defaultValue": "Contoso",
    "allowedValues": [
      "Contoso",
      "Fabrikam"
    ]
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
@allowed([
  'Contoso'
  'Fabrikam'
])
param demoParam string = 'Contoso'
```

---

U kunt expressies gebruiken met de standaard waarde. U kunt de functie [Reference](template-functions-resource.md#reference) of een van de [lijst](template-functions-resource.md#list) functies niet gebruiken in de para meters van de sectie. Deze functies halen de runtime status van een resource en kunnen niet worden uitgevoerd voor implementatie wanneer para meters worden omgezet.

Expressies zijn niet toegestaan met andere parameter eigenschappen.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "location": {
    "type": "string",
    "defaultValue": "[resourceGroup().location]"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param location string = resourceGroup().location
```

---

U kunt een andere parameter waarde gebruiken om een standaard waarde te maken. Met de volgende sjabloon wordt een naam van een host-plan gemaakt op basis van de naam van de site.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "siteName": {
    "type": "string",
    "defaultValue": "[concat('site', uniqueString(resourceGroup().id))]"
  },
  "hostingPlanName": {
    "type": "string",
    "defaultValue": "[concat(parameters('siteName'),'-plan')]"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param siteName string = 'site${uniqueString(resourceGroup().id)}'
param hostingPlanName string = '${siteName}-plan'
```

## <a name="length-constraints"></a>Lengte beperkingen

U kunt minimale en maximale lengte opgeven voor teken reeks-en matrix parameters. U kunt een of beide beperkingen instellen. Voor teken reeksen geeft de lengte het aantal tekens aan. Voor matrices geeft de lengte het aantal items in de matrix aan.

In het volgende voor beeld worden twee para meters gedeclareerd. Een para meter is een naam voor een opslag account die 3-24 tekens moet bevatten. De andere para meter is een matrix die van 1-5 items moet zijn.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "storageAccountName": {
    "type": "string",
    "minLength": 3,
    "maxLength": 24
  },
  "appNames": {
    "type": "array",
    "minLength": 1,
    "maxLength": 5
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
@minLength(3)
@maxLength(24)
param storageAccountName string

@minLength(1)
@maxLength(5)
param appNames array
```

---

## <a name="integer-constraints"></a>Geheeltallige beperkingen

U kunt minimum-en maximum waarden instellen voor para meters met een geheel getal. U kunt een of beide beperkingen instellen.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "month": {
    "type": "int",
    "minValue": 1,
    "maxValue": 12
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
@minValue(1)
@maxValue(12)
param month int
```

---

## <a name="description"></a>Beschrijving

U kunt een beschrijving toevoegen aan een para meter om gebruikers van uw sjabloon inzicht te geven in de waarde die moet worden verstrekt. Wanneer u de sjabloon via de portal implementeert, wordt de tekst die u in de beschrijving opgeeft automatisch gebruikt als tip voor die para meter. Voeg alleen een beschrijving toe als de tekst meer informatie bevat dan kan worden afgeleid van de parameter naam.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "virtualMachineSize": {
    "type": "string",
    "metadata": {
      "description": "Must be at least Standard_A3 to support 2 NICs."
    },
    "defaultValue": "Standard_DS1_v2"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
@description('Must be at least Standard_A3 to support 2 NICs.')
param virtualMachineSize string = 'Standard_DS1_v2'
```

---

## <a name="use-parameter"></a>Para meter gebruiken

In een JSON-sjabloon verwijst u naar de waarde voor de para meter met behulp van de [para meters](template-functions-deployment.md#parameters) -functie. In Bicep gebruikt u de parameter naam. In het volgende voor beeld wordt een parameter waarde gebruikt voor een Key Vault naam.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vaultName": {
      "type": "string",
      "defaultValue": "[format('keyVault{0}', uniqueString(resourceGroup().id))]"
    }
  },
  "resources": [
    {
      "type": "Microsoft.KeyVault/vaults",
      "apiVersion": "2019-09-01",
      "name": "[parameters('vaultName')]",
      ...
    }
  ]
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param vaultName string = 'keyVault${uniqueString(resourceGroup().id)}'

resource keyvault 'Microsoft.KeyVault/vaults@2019-09-01' = {
  name: vaultName
  ...
}
```

---

## <a name="objects-as-parameters"></a>Objecten als para meters

Het kan gemakkelijker zijn om verwante waarden te organiseren door ze als een object door te geven. Deze benadering vermindert ook het aantal para meters in de sjabloon.

In het volgende voor beeld ziet u een para meter die een-object is. De standaard waarde toont de verwachte eigenschappen van het object. Deze eigenschappen worden gebruikt bij het definiëren van de resource die moet worden geïmplementeerd.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vNetSettings": {
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
  "resources": [
    {
      "type": "Microsoft.Network/virtualNetworks",
      "apiVersion": "2020-06-01",
      "name": "[parameters('vNetSettings').name]",
      "location": "[parameters('vNetSettings').location]",
      "properties": {
        "addressSpace": {
          "addressPrefixes": [
            "[parameters('vNetSettings').addressPrefixes[0].addressPrefix]"
          ]
        },
        "subnets": [
          {
            "name": "[parameters('vNetSettings').subnets[0].name]",
            "properties": {
              "addressPrefix": "[parameters('vNetSettings').subnets[0].addressPrefix]"
            }
          },
          {
            "name": "[parameters('vNetSettings').subnets[1].name]",
            "properties": {
              "addressPrefix": "[parameters('vNetSettings').subnets[1].addressPrefix]"
            }
          }
        ]
      }
    }
  ]
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param vNetSettings object = {
  name: 'VNet1'
  location: 'eastus'
  addressPrefixes: [
    {
      name: 'firstPrefix'
      addressPrefix: '10.0.0.0/22'
    }
  ]
  subnets: [
    {
      name: 'firstSubnet'
      addressPrefix: '10.0.0.0/24'
    }
    {
      name: 'secondSubnet'
      addressPrefix: '10.0.1.0/24'
    }
  ]
}
resource vnet 'Microsoft.Network/virtualNetworks@2020-06-01' = {
  name: vNetSettings.name
  location: vNetSettings.location
  properties: {
    addressSpace: {
      addressPrefixes: [
        vNetSettings.addressPrefixes[0].addressPrefix
      ]
    }
    subnets: [
      {
        name: vNetSettings.subnets[0].name
        properties: {
          addressPrefix: vNetSettings.subnets[0].addressPrefix
        }
      }
      {
        name: vNetSettings.subnets[1].name
        properties: {
          addressPrefix: vNetSettings.subnets[1].addressPrefix
        }
      }
    ]
  }
}
```

---

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
