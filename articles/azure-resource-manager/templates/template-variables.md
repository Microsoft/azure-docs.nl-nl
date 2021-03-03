---
title: Variabelen in sjablonen
description: Hierin wordt beschreven hoe u variabelen definieert in een Azure Resource Manager sjabloon (ARM-sjabloon) en Bicep-bestand.
ms.topic: conceptual
ms.date: 02/19/2021
ms.openlocfilehash: e00a9e8e1801725707bac2abdc67512477e2cf07
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101700334"
---
# <a name="variables-in-arm-templates"></a>Variabelen in ARM-sjablonen

In dit artikel wordt beschreven hoe u variabelen definieert en gebruikt in uw Azure Resource Manager sjabloon (ARM-sjabloon) of het Bicep-bestand. U kunt variabelen gebruiken om uw sjabloon te vereenvoudigen. In plaats van ingewikkelde expressies in uw sjabloon te herhalen, definieert u een variabele die de gecompliceerde expressie bevat. Vervolgens gebruikt u die variabele als nodig in de sjabloon.

Met Resource Manager worden variabelen omgezet voordat de implementatie bewerkingen worden gestart. Wanneer de variabele wordt gebruikt in de sjabloon, wordt deze door de Resource Manager vervangen door de omgezette waarde.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="define-variable"></a>Variabele definiëren

Wanneer u een variabele definieert, geeft u geen [gegevens type](template-syntax.md#data-types) op voor de variabele. Geef in plaats daarvan een waarde of sjabloon expressie op. Het type variabele wordt afgeleid van de omgezette waarde. In het volgende voor beeld wordt een variabele ingesteld op een teken reeks.

# <a name="json"></a>[JSON](#tab/json)

```json
"variables": {
  "stringVar": "example value"
},
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
var stringVar = 'example value'
```

---

U kunt de waarde van een para meter of een andere variabele gebruiken bij het samen stellen van de variabele.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "inputValue": {
    "defaultValue": "deployment parameter",
    "type": "string"
  }
},
"variables": {
  "stringVar": "myVariable",
  "concatToVar": "[concat(variables('stringVar'), '-addtovar') ]",
  "concatToParam": "[concat(parameters('inputValue'), '-addtoparam')]"
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param inputValue string = 'deployment parameter'

var stringVar = 'myVariable'
var concatToVar =  '${stringVar}-addtovar'
var concatToParam = '${inputValue}-addtoparam'
```

---

U kunt [sjabloon functies](template-functions.md) gebruiken om de waarde van de variabele te maken.

In het volgende voor beeld wordt een teken reeks waarde gemaakt voor de naam van een opslag account. Er worden verschillende sjabloon functies gebruikt om een parameter waarde op te halen en deze aan een unieke teken reeks toe te voegen.

# <a name="json"></a>[JSON](#tab/json)

```json
"variables": {
  "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
},
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
var storageName = '${toLower(storageNamePrefix)}${uniqueString(resourceGroup().id)}'
```

---

In JSON-sjablonen kunt u de functie [Reference](template-functions-resource.md#reference) of een van de [lijst](template-functions-resource.md#list) functies in de variabele declaratie niet gebruiken. Met deze functies wordt de runtime status van een resource opgehaald en kunnen niet worden uitgevoerd voordat de implementatie van variabelen wordt opgelost.

In Bicep-bestanden zijn de functies Reference en List geldig bij het declareren van een variabele.

## <a name="use-variable"></a>Variabele gebruiken

In het volgende voor beeld ziet u hoe u de variabele voor een resource-eigenschap gebruikt.

# <a name="json"></a>[JSON](#tab/json)

In een JSON-sjabloon verwijst u naar de waarde voor de variabele met behulp van de functie [Varia bles](template-functions-deployment.md#variables) .

```json
"variables": {
  "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
},
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageName')]",
    ...
  }
]
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

In een Bicep-bestand verwijst u naar de waarde voor de variabele door de naam van de variabele op te geven.

```bicep
var storageName = '${toLower(storageNamePrefix)}${uniqueString(resourceGroup().id)}'

resource demoAccount 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: storageName
```

---

## <a name="example-template"></a>Voorbeeld sjabloon

Met de volgende sjabloon worden geen resources geïmplementeerd. Het bevat enkele manieren om variabelen van verschillende typen te declareren.

# <a name="json"></a>[JSON](#tab/json)

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/variables.json":::

# <a name="bicep"></a>[Bicep](#tab/bicep)

Bicep biedt momenteel geen ondersteuning voor lussen.

:::code language="bicep" source="~/resourcemanager-templates/azure-resource-manager/variables.bicep":::

---

## <a name="configuration-variables"></a>Configuratie variabelen

U kunt variabelen definiëren die gerelateerde waarden bevatten voor het configureren van een omgeving. U definieert de variabele als een object met de waarden. In het volgende voor beeld ziet u een object dat waarden voor twee omgevingen bevat: **test** en **Prod**. Geef tijdens de implementatie een van deze waarden door.

# <a name="json"></a>[JSON](#tab/json)

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/variablesconfigurations.json":::

# <a name="bicep"></a>[Bicep](#tab/bicep)

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/variablesconfigurations.bicep":::

---

## <a name="next-steps"></a>Volgende stappen

* Zie [inzicht krijgen in de structuur en syntaxis van arm-sjablonen](template-syntax.md)voor meer informatie over de beschik bare eigenschappen voor variabelen.
* Zie [Best practices-Varia bles](template-best-practices.md#variables)(Engelstalig) voor aanbevelingen voor het maken van variabelen.
* Zie [netwerk beveiligings regels](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) en [parameter bestand](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json)voor een voorbeeld sjabloon waarmee beveiligings regels worden toegewezen aan een netwerk beveiligings groep.
