---
title: Syntaxis van Azure Resource Manager sjablonen in JSON en Bicep vergelijken
description: Vergelijkt Azure Resource Manager sjablonen die zijn ontwikkeld met JSON en Bicep en laat zien hoe u kunt converteren tussen de talen.
ms.topic: conceptual
ms.date: 03/12/2021
ms.openlocfilehash: 85f85e66e69eede68bab847e4bc68514e65115eb
ms.sourcegitcommit: df1930c9fa3d8f6592f812c42ec611043e817b3b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2021
ms.locfileid: "103418042"
---
# <a name="comparing-json-and-bicep-for-templates"></a>JSON en Bicep vergelijken voor sjablonen

In dit artikel wordt Bicep-syntaxis vergeleken met de JSON-syntaxis voor Azure Resource Manager sjablonen (ARM-sjablonen). In de meeste gevallen biedt Bicep de syntaxis minder uitgebreid dan het equivalent in JSON.

## <a name="syntax-equivalents"></a>Syntaxis equivalenten

Als u bekend bent met het gebruik van JSON voor het ontwikkelen van ARM-sjablonen, gebruikt u de volgende tabel voor meer informatie over de equivalente syntaxis voor Bicep.

| Scenario | Bicep | JSON |
| -------- | ------------ | ----- |
| Een expressie maken | `func()` | `"[func()]"` |
| Parameter waarde ophalen | `exampleParameter` | `[parameters('exampleParameter'))]` |
| Waarde van variabele ophalen | `exampleVar` | `[variables('exampleVar'))]` |
| Tekenreeksen samenvoegen | `'${namePrefix}-vm'` | `[concat(parameters('namePrefix'), '-vm')]` |
| Eigenschap van de resource instellen | `sku: '2016-Datacenter'` | `"sku": "2016-Datacenter",` |
| Retourneert de logische en | `isMonday && isNovember` | `[and(parameter('isMonday'), parameter('isNovember'))]` |
| Resource-ID van resource ophalen in de sjabloon | `nic1.id` | `[resourceId('Microsoft.Network/networkInterfaces', variables('nic1Name'))]` |
| Eigenschap ophalen uit resource in de sjabloon | `diagsAccount.properties.primaryEndpoints.blob` | `[reference(resourceId('Microsoft.Storage/storageAccounts', variables('diagStorageAccountName'))).primaryEndpoints.blob]` |
| Voorwaardelijk een waarde instellen | `isMonday ? 'valueIfTrue' : 'valueIfFalse'` | `[if(parameters('isMonday'), 'valueIfTrue', 'valueIfFalse')]` |
| Een oplossing scheiden in meerdere bestanden | Modules gebruiken | Gekoppelde sjablonen gebruiken |
| Het doel bereik van de implementatie instellen | `targetScope = 'subscription'` | `"$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#"` |
| Afhankelijkheid instellen | Vertrouw op automatische detectie van afhankelijkheden of stel de afhankelijkheid hand matig in met `dependsOn: [ stg ]` | `"dependsOn": ["[resourceId('Microsoft.Storage/storageAccounts', 'parameters('storageAccountName'))]"]` |
| Resource declaratie | `resource vm 'Microsoft.Compute/virtualMachines@2020-06-01' = {...}` | `"resources": [ { "type": "Microsoft.Compute/virtualMachines", "apiVersion": "2020-06-01", ... } ]` |

## <a name="recommendations"></a>Aanbevelingen

* Vermijd het gebruik van de functies [Reference](template-functions-resource.md#reference) en [ResourceID](template-functions-resource.md#resourceid) in uw Bicep-bestand, indien mogelijk. Wanneer u verwijst naar een resource in dezelfde Bicep-implementatie, gebruikt u in plaats daarvan de resource-id. Als u bijvoorbeeld een resource in uw Bicep-bestand hebt geïmplementeerd met `stg` als resource-id, gebruikt u syntaxis zoals `stg.id` of `stg.properties.primaryEndpoints.blob` om eigenschaps waarden op te halen. Met behulp van de resource-id maakt u een impliciete afhankelijkheid tussen resources. U hoeft de afhankelijkheid niet expliciet in te stellen met de eigenschap dependsOn.
* Gebruik een consistente behuizing voor id's. Als u niet zeker weet welk type behuizing u moet gebruiken, kunt u Camel-behuizing proberen. Bijvoorbeeld `param myCamelCasedParameter string`.
* Voeg alleen een beschrijving toe aan een para meter als de beschrijving essentiële informatie voor gebruikers bevat. U kunt `//` opmerkingen voor bepaalde informatie gebruiken.

## <a name="next-steps"></a>Volgende stappen

* Zie [Bicep-zelf studie](./bicep-tutorial-create-first-bicep.md)voor meer informatie over de Bicep.
* Zie [arm-sjablonen converteren tussen JSON en Bicep](bicep-decompile.md)voor meer informatie over het converteren van sjablonen tussen de talen.
