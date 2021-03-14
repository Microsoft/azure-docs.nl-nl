---
title: Syntaxis van Azure Resource Manager sjablonen in JSON en Bicep vergelijken
description: Vergelijkt Azure Resource Manager sjablonen die zijn ontwikkeld met JSON en Bicep en laat zien hoe u kunt converteren tussen de talen.
ms.topic: conceptual
ms.date: 03/12/2021
ms.openlocfilehash: 225e52e9534a77a01502b762f043a4f34df19caa
ms.sourcegitcommit: afb9e9d0b0c7e37166b9d1de6b71cd0e2fb9abf5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/14/2021
ms.locfileid: "103461791"
---
# <a name="comparing-json-and-bicep-for-templates"></a>JSON en Bicep vergelijken voor sjablonen

In dit artikel wordt Bicep-syntaxis vergeleken met de JSON-syntaxis voor Azure Resource Manager sjablonen (ARM-sjablonen). In de meeste gevallen biedt Bicep de syntaxis minder uitgebreid dan het equivalent in JSON.

Als u bekend bent met het gebruik van JSON voor het ontwikkelen van ARM-sjablonen, kunt u de volgende voor beelden gebruiken om meer te weten te komen over de equivalente syntaxis voor Bicep.

## <a name="expressions"></a>Expressies

Een expressie schrijven:

```bicep
func()
```

```json
"[func()]"
```

## <a name="parameters"></a>Parameters

Een para meter declareren met een standaard waarde:

```bicep
param demoParam string = 'Contoso'
```

```json
"parameters": {
  "demoParam": {
    "type": "string",
    "defaultValue": "Contoso"
  }
}
```

Een parameter waarde ophalen:

```bicep
demoParam
```

```json
[parameters('demoParam'))]
```

## <a name="variables"></a>Variabelen

Een variabele declareren:

```bicep
var demoVar = 'example value'
```

```json
"variables": {
  "demoVar": "example value"
},
```

De waarde van een variabele ophalen:

```bicep
demoVar
```

```json
[variables('demoVar'))]
```

## <a name="strings"></a>Tekenreeksen

Teken reeksen samen voegen:

```bicep
'${namePrefix}-vm'
```

```json
[concat(parameters('namePrefix'), '-vm')]
```

## <a name="logical-operators"></a>Logische operators

U kunt als volgt de logische **en**:

```bicep
isMonday && isNovember
```

```json
[and(parameter('isMonday'), parameter('isNovember'))]
```

Als u een waarde voorwaardelijk wilt instellen:

```bicep
isMonday ? 'valueIfTrue' : 'valueIfFalse'
```

```json
[if(parameters('isMonday'), 'valueIfTrue', 'valueIfFalse')]
```

## <a name="deployment-scope"></a>Implementatie bereik

Het doel bereik van de implementatie instellen:

```bicep
targetScope = 'subscription'
```

```json
"$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#"
```

## <a name="resources"></a>Resources

Een resource declareren:

```bicep
resource vm 'Microsoft.Compute/virtualMachines@2020-06-01' = {
  ...
}
```

```json
"resources": [ 
  { 
    "type": "Microsoft.Compute/virtualMachines", 
    "apiVersion": "2020-06-01", 
    ... 
  } 
]
```

Een resource voorwaardelijk implementeren:

```bicep
resource vm 'Microsoft.Compute/virtualMachines@2020-06-01' = if(deployVM) {
  ...
}
```

```json
"resources": [ 
  {
    "condition": "[parameters('deployVM')]",
    "type": "Microsoft.Compute/virtualMachines", 
    "apiVersion": "2020-06-01", 
    ... 
  } 
]
```

Eigenschap van de resource instellen:

```bicep
sku: '2016-Datacenter'
```

```json
"sku": "2016-Datacenter",
```

Resource-ID van de resource ophalen in de sjabloon:

```bicep
nic1.id
```

```json
[resourceId('Microsoft.Network/networkInterfaces', variables('nic1Name'))]
```

## <a name="loops"></a>Lussen

Items in een matrix of aantal herhalen:

```bicep
[for storageName in storageAccounts: {
  ...
}]
```

```json
"copy": {
  "name": "storagecopy",
  "count": "[length(parameters('storageAccounts'))]"
},
...
```

## <a name="resource-dependencies"></a>Bronafhankelijkheden

De afhankelijkheid tussen resources instellen:

Voor Bicep is afhankelijk van de automatische detectie van afhankelijkheden of het hand matig instellen van afhankelijkheden.

```bicep
dependsOn: [ stg ]
```

```json
"dependsOn": ["[resourceId('Microsoft.Storage/storageAccounts', 'parameters('storageAccountName'))]"]
```

## <a name="reference-resources"></a>Naslag bronnen

Een eigenschap ophalen van een resource in de sjabloon:

```bicep
diagsAccount.properties.primaryEndpoints.blob
```

```json
[reference(resourceId('Microsoft.Storage/storageAccounts', variables('diagStorageAccountName'))).primaryEndpoints.blob]
```

Een eigenschap ophalen uit een bestaande resource die niet in de sjabloon is geïmplementeerd:

```bicep
resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' existing = {
  name: storageAccountName
}

// use later in template as often as needed
stg.properties.primaryEndpoints.blob
```

```json
// required every time the property is needed
"[reference(resourceId('Microsoft.Storage/storageAccounts/', parameters('storageAccountName')), '2019-06-01').primaryEndpoints.blob]"
```

## <a name="outputs"></a>Uitvoerwaarden

Een eigenschap uitvoeren vanuit een resource in de sjabloon:

```bicep
output hostname string = publicIP.properties.dnsSettings.fqdn
```

```json
"outputs": {
  "hostname": {
    "type": "string",
    "value": "[reference(resourceId('Microsoft.Network/publicIPAddresses', variables('publicIPAddressName'))).dnsSettings.fqdn]"
  },
}
```

## <a name="code-reuse"></a>Code opnieuw gebruiken

Een oplossing scheiden in meerdere bestanden:

* Gebruik [modules](bicep-tutorial-add-modules.md)voor Bicep.
* Gebruik [gekoppelde sjablonen](linked-templates.md)voor JSON.

## <a name="recommendations"></a>Aanbevelingen

* Vermijd het gebruik van de functies [Reference](template-functions-resource.md#reference) en [ResourceID](template-functions-resource.md#resourceid) in uw Bicep-bestand, indien mogelijk. Wanneer u verwijst naar een resource in dezelfde Bicep-implementatie, gebruikt u in plaats daarvan de resource-id. Als u bijvoorbeeld een resource in uw Bicep-bestand hebt geïmplementeerd met `stg` als resource-id, gebruikt u syntaxis zoals `stg.id` of `stg.properties.primaryEndpoints.blob` om eigenschaps waarden op te halen. Met behulp van de resource-id maakt u een impliciete afhankelijkheid tussen resources. U hoeft de afhankelijkheid niet expliciet in te stellen met de eigenschap dependsOn.
* Als de resource niet is geïmplementeerd in het Bicep-bestand, kunt u nog steeds een symbolische verwijzing naar de resource krijgen met behulp van het **bestaande** sleutel woord.
* Gebruik een consistente behuizing voor id's. Als u niet zeker weet welk type behuizing u moet gebruiken, kunt u Camel-behuizing proberen. Bijvoorbeeld `param myCamelCasedParameter string`.
* Voeg alleen een beschrijving toe aan een para meter als de beschrijving essentiële informatie voor gebruikers bevat. U kunt `//` opmerkingen voor bepaalde informatie gebruiken.

## <a name="next-steps"></a>Volgende stappen

* Zie [Bicep-zelf studie](./bicep-tutorial-create-first-bicep.md)voor meer informatie over de Bicep.
* Zie [arm-sjablonen converteren tussen JSON en Bicep](bicep-decompile.md)voor meer informatie over het converteren van sjablonen tussen de talen.
