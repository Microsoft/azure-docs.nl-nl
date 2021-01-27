---
title: Variabelen in sjablonen
description: Hierin wordt beschreven hoe u variabelen definieert in een Azure Resource Manager sjabloon (ARM-sjabloon).
ms.topic: conceptual
ms.date: 01/26/2021
ms.openlocfilehash: feecc4b5df77e6a3bf51294cb12aabf44899dde5
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98874431"
---
# <a name="variables-in-arm-template"></a>Variabelen in ARM-sjabloon

In dit artikel wordt beschreven hoe u variabelen definieert en gebruikt in uw Azure Resource Manager sjabloon (ARM-sjabloon). U kunt variabelen gebruiken om uw sjabloon te vereenvoudigen. In plaats van ingewikkelde expressies in uw sjabloon te herhalen, definieert u een variabele die de gecompliceerde expressie bevat. Vervolgens verwijst u naar die variabele die u nodig hebt in uw sjabloon.

Met Resource Manager worden variabelen omgezet voordat de implementatie bewerkingen worden gestart. Wanneer de variabele wordt gebruikt in de sjabloon, wordt deze door de Resource Manager vervangen door de omgezette waarde.

## <a name="define-variable"></a>Variabele definiëren

Geef bij het definiëren van een variabele een waarde of een sjabloon expressie op die wordt omgezet in een [gegevens type](template-syntax.md#data-types). U kunt de waarde van een para meter of een andere variabele gebruiken bij het samen stellen van de variabele.

U kunt [sjabloon functies](template-functions.md) gebruiken in de declaratie van variabelen, maar u kunt de functie [Reference](template-functions-resource.md#reference) of een van de functies van de [lijst](template-functions-resource.md#list) niet gebruiken. Met deze functies wordt de runtime status van een resource opgehaald en kunnen niet worden uitgevoerd voordat de implementatie van variabelen wordt opgelost.

In het volgende voor beeld ziet u een definitie van een variabele. Er wordt een teken reeks waarde voor de naam van een opslag account gemaakt. Er worden verschillende sjabloon functies gebruikt om een parameter waarde op te halen en deze aan een unieke teken reeks toe te voegen.

```json
"variables": {
  "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
},
```

## <a name="use-variable"></a>Variabele gebruiken

In de sjabloon verwijst u naar de waarde voor de para meter met behulp van de functie [Varia bles](template-functions-deployment.md#variables) . In het volgende voor beeld ziet u hoe u de variabele voor een resource-eigenschap gebruikt.

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageName')]",
    ...
  }
]
```

## <a name="example-template"></a>Voorbeeld sjabloon

Met de volgende sjabloon worden geen resources geïmplementeerd. Er wordt alleen een aantal manieren voor het declareren van variabelen weer gegeven.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/variables.json":::

## <a name="configuration-variables"></a>Configuratie variabelen

U kunt variabelen definiëren die gerelateerde waarden bevatten voor het configureren van een omgeving. U definieert de variabele als een object met de waarden. In het volgende voor beeld ziet u een object dat waarden voor twee omgevingen bevat: **test** en **Prod**. U geeft een van deze waarden door tijdens de implementatie.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/variablesconfigurations.json":::

## <a name="next-steps"></a>Volgende stappen

* Zie [inzicht krijgen in de structuur en syntaxis van arm-sjablonen](template-syntax.md)voor meer informatie over de beschik bare eigenschappen voor variabelen.
* Zie [Best practices-Varia bles](template-best-practices.md#variables)(Engelstalig) voor aanbevelingen voor het maken van variabelen.
* Zie [netwerk beveiligings regels](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) en [parameter bestand](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json)voor een voorbeeld sjabloon waarmee beveiligings regels worden toegewezen aan een netwerk beveiligings groep.
