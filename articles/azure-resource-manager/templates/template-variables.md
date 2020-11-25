---
title: Variabelen in sjablonen
description: Hierin wordt beschreven hoe u variabelen definieert in een Azure Resource Manager sjabloon.
ms.topic: conceptual
ms.date: 11/24/2020
ms.openlocfilehash: 7758451747503cb8c42566dc081a91f84bafa375
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95916000"
---
# <a name="variables-in-azure-resource-manager-template"></a>Variabelen in Azure Resource Manager-sjabloon

In dit artikel wordt beschreven hoe u in uw Azure Resource Manager-sjabloon variabelen definieert en gebruikt. U kunt variabelen gebruiken om uw sjabloon te vereenvoudigen. In plaats van ingewikkelde expressies in uw sjabloon te herhalen, definieert u een variabele die de gecompliceerde expressie bevat. Vervolgens verwijst u naar die variabele die u nodig hebt in uw sjabloon.

Met Resource Manager worden variabelen omgezet voordat de implementatie bewerkingen worden gestart. Wanneer de variabele wordt gebruikt in de sjabloon, wordt deze door de Resource Manager vervangen door de omgezette waarde.

De indeling van elke variabele moet overeenkomen met een van de [gegevens typen](template-syntax.md#data-types).

## <a name="define-variable"></a>Variabele definiëren

In het volgende voor beeld ziet u een definitie van een variabele. Er wordt een teken reeks waarde voor de naam van een opslag account gemaakt. Er worden verschillende sjabloon functies gebruikt om een parameter waarde op te halen en deze aan een unieke teken reeks toe te voegen.

```json
"variables": {
  "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
},
```

U kunt de functie [Reference](template-functions-resource.md#reference) of een van [de functies in de sectie](template-functions-resource.md#list) Varia BLES niet gebruiken. Met deze functies wordt de runtime status van een resource opgehaald en kunnen niet worden uitgevoerd voordat de implementatie van variabelen wordt opgelost.

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

## <a name="configuration-variables"></a>Configuratie variabelen

U kunt variabelen definiëren die gerelateerde waarden bevatten voor het configureren van een omgeving. U definieert de variabele als een object met de waarden. In het volgende voor beeld ziet u een object dat waarden voor twee omgevingen bevat: **test** en **Prod**.

```json
"variables": {
  "environmentSettings": {
    "test": {
      "instanceSize": "Small",
      "instanceCount": 1
    },
    "prod": {
      "instanceSize": "Large",
      "instanceCount": 4
    }
  }
},
```

In para meters maakt u een waarde die aangeeft welke configuratie waarden moeten worden gebruikt.

```json
"parameters": {
  "environmentName": {
    "type": "string",
    "allowedValues": [
      "test",
      "prod"
    ]
  }
},
```

Als u instellingen voor de opgegeven omgeving wilt ophalen, gebruikt u de variabele en de para meter samen.

```json
"[variables('environmentSettings')[parameters('environmentName')].instanceSize]"
```

## <a name="example-templates"></a>Voorbeeld sjablonen

In de volgende voor beelden ziet u scenario's voor het gebruik van variabelen.

|Template  |Beschrijving  |
|---------|---------|
| [variabele definities](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/variables.json) | Toont de verschillende typen variabelen. De sjabloon implementeert geen resources. Er worden variabele waarden gemaakt en deze waarden worden geretourneerd. |
| [configuratie variabele](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/variablesconfigurations.json) | Toont het gebruik van een variabele die configuratie waarden definieert. De sjabloon implementeert geen resources. Er worden variabele waarden gemaakt en deze waarden worden geretourneerd. |
| [netwerk beveiligings regels](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) en [parameter bestand](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json) | Hiermee wordt een matrix gemaakt met de juiste indeling voor het toewijzen van beveiligings regels aan een netwerk beveiligings groep. |

## <a name="next-steps"></a>Volgende stappen

* Zie [inzicht krijgen in de structuur en de syntaxis van Azure Resource Manager-sjablonen](template-syntax.md)voor meer informatie over de beschik bare eigenschappen voor variabelen.
* Zie [Best practices-Varia bles](template-best-practices.md#variables)(Engelstalig) voor aanbevelingen voor het maken van variabelen.
