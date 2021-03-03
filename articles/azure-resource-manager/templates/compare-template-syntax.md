---
title: Azure Resource Manager sjablonen converteren tussen JSON en Bicep
description: Vergelijkt Azure Resource Manager sjablonen die zijn ontwikkeld met JSON en Bicep.
ms.topic: conceptual
ms.date: 02/19/2021
ms.openlocfilehash: 9388ed50f13d6885d0a0668b61a9141dae375244
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101744586"
---
# <a name="comparing-json-and-bicep-for-templates"></a>JSON en Bicep vergelijken voor sjablonen

In dit artikel wordt Bicep-syntaxis vergeleken met de JSON-syntaxis voor Azure Resource Manager sjablonen (ARM-sjablonen). In de meeste gevallen biedt Bicep de syntaxis minder uitgebreid dan het equivalent in JSON.

## <a name="syntax-equivalents"></a>Syntaxis equivalenten

Als u bekend bent met het gebruik van JSON voor het ontwikkelen van ARM-sjablonen, gebruikt u de volgende tabel voor meer informatie over de equivalente syntaxis voor Bicep.

| Scenario | ARM-sjabloon | Bicep |
| -------- | ------------ | ----- |
| Een expressie maken | `"[func()]"` | `func()` |
| Parameter waarde ophalen | `[parameters('exampleParameter'))]` | `exampleParameter` |
| Waarde van variabele ophalen | `[variables('exampleVar'))]` | `exampleVar` |
| Tekenreeksen samenvoegen | `[concat(parameters('namePrefix'), '-vm')]` | `'${namePrefix}-vm'` |
| Eigenschap van de resource instellen | `"sku": "2016-Datacenter",` | `sku: '2016-Datacenter'` |
| Retourneert de logische en | `[and(parameter('isMonday'), parameter('isNovember'))]` | `isMonday && isNovember` |
| Resource-ID van resource ophalen in de sjabloon | `[resourceId('Microsoft.Network/networkInterfaces', variables('nic1Name'))]` | `nic1.id` |
| Eigenschap ophalen uit resource in de sjabloon | `[reference(resourceId('Microsoft.Storage/storageAccounts', variables('diagStorageAccountName'))).primaryEndpoints.blob]` | `diagsAccount.properties.primaryEndpoints.blob` |
| Voorwaardelijk een waarde instellen | `[if(parameters('isMonday'), 'valueIfTrue', 'valueIfFalse')]` | `isMonday ? 'valueIfTrue' : 'valueIfFalse'` |
| Een oplossing scheiden in meerdere bestanden | Gekoppelde sjablonen gebruiken | Modules gebruiken |
| Het doel bereik van de implementatie instellen | `"$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#"` | `targetScope = 'subscription'` |
| Afhankelijkheid instellen | `"dependsOn": ["[resourceId('Microsoft.Storage/storageAccounts', 'parameters('storageAccountName'))]"]` | Vertrouw op automatische detectie van afhankelijkheden of stel de afhankelijkheid hand matig in met `dependsOn: [ stg ]` |

Als u het type en de versie voor een resource wilt declareren, gebruikt u het volgende in Bicep:

```bicep
resource vm 'Microsoft.Compute/virtualMachines@2020-06-01' = {
  ...
}
```

In plaats van de equivalente syntaxis in JSON:

```json
"resources": [
  {
    "type": "Microsoft.Compute/virtualMachines",
    "apiVersion": "2020-06-01",
    ...
  }
]
```

## <a name="recommendations"></a>Aanbevelingen

* Vermijd het gebruik van de functies [Reference](template-functions-resource.md#reference) en [ResourceID](template-functions-resource.md#resourceid) in uw Bicep-bestand, indien mogelijk. Wanneer u verwijst naar een resource in dezelfde Bicep-implementatie, gebruikt u in plaats daarvan de resource-id. Als u bijvoorbeeld een resource in uw Bicep-bestand hebt geïmplementeerd met `stg` als resource-id, gebruikt u syntaxis zoals `stg.id` of `stg.properties.primaryEndpoints.blob` om eigenschaps waarden op te halen. Met behulp van de resource-id maakt u een impliciete afhankelijkheid tussen resources. U hoeft de afhankelijkheid niet expliciet in te stellen met de eigenschap dependsOn.
* Gebruik een consistente behuizing voor id's. Als u niet zeker weet welk type behuizing u moet gebruiken, kunt u Camel-behuizing proberen. Bijvoorbeeld `param myCamelCasedParameter string`.
* Voeg alleen een beschrijving toe aan een para meter als de beschrijving essentiële informatie voor gebruikers bevat. U kunt `//` opmerkingen voor bepaalde informatie gebruiken.

## <a name="decompile-json-to-bicep"></a>JSON decompileren op Bicep

De Bicep CLI biedt een opdracht voor het decompileren van een bestaande ARM-sjabloon in een Bicep-bestand. Gebruik voor het decompileren van een JSON-bestand: `bicep decompile "path/to/file.json"`

Met deze opdracht wordt een start punt geboden voor Bicep-ontwerp, maar de opdracht werkt niet voor alle sjablonen. De opdracht kan mislukken of u moet mogelijk problemen oplossen na de decompilatie. Op dit moment heeft de opdracht de volgende beperkingen:

* Sjablonen die gebruikmaken van Kopieer lussen kunnen niet worden ontcompilatied.
* Geneste sjablonen kunnen alleen worden ontcompileerd als ze het evaluatie bereik ' binnenste ' gebruiken.

U kunt de sjabloon voor een resource groep exporteren en deze vervolgens rechtstreeks door geven aan de opdracht Bicep decompile. In het volgende voor beeld ziet u hoe u een geëxporteerde sjabloon decompileert.

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli
az group export --name "your_resource_group_name" > main.json
bicep decompile main.json
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Export-AzResourceGroup -ResourceGroupName "your_resource_group_name" -Path ./main.json
bicep decompile main.json
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

[Exporteer de sjabloon](export-template-portal.md) via de portal. Gebruiken `bicep decompile <filename>` op het gedownloade bestand.

---

## <a name="build-json-from-bicep"></a>JSON maken op basis van Bicep

De Bicep CLI biedt ook een opdracht om Bicep te converteren naar JSON. Als u een JSON-bestand wilt maken, gebruikt u: `bicep build "path/to/file.json"`

## <a name="side-by-side-view"></a>Weer gave naast elkaar

Met de [Bicep Playground](https://aka.ms/bicepdemo) kunt u GELIJKWAARDIGe JSON-en Bicep-bestanden naast elkaar weer geven. U kunt een voorbeeld sjabloon selecteren om beide versies weer te geven. Of selecteer `Decompile` om uw eigen JSON-sjabloon te uploaden en het equivalente Bicep-bestand weer te geven.

## <a name="next-steps"></a>Volgende stappen

Zie [project Bicep](https://github.com/Azure/bicep)voor meer informatie over het Bicep-project.
