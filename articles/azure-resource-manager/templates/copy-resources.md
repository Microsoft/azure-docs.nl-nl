---
title: Meerdere exemplaren van resources implementeren
description: Kopieerbewerkingen en matrices in een Azure Resource Manager sjabloon (ARM-sjabloon) om het resourcetype vaak te implementeren.
ms.topic: conceptual
ms.date: 04/01/2021
ms.openlocfilehash: 5ddb0cabf0acae1ffe9b9e77e6defa70f9cbd61b
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107479964"
---
# <a name="resource-iteration-in-arm-templates"></a>Resource-iteratie in ARM-sjablonen

In dit artikel wordt beschreven hoe u meer dan één exemplaar van een resource maakt in uw Azure Resource Manager (ARM-sjabloon). Door een kopieerlus toe te voegen aan de sectie resources van uw sjabloon, kunt u dynamisch instellen hoeveel resources u wilt implementeren. U voorkomt ook dat u sjabloonsyntaxis moet herhalen.

U kunt ook een kopieerlus gebruiken [met eigenschappen](copy-properties.md), [variabelen](copy-variables.md)en [uitvoer.](copy-outputs.md)

Zie voorwaardeelement als u wilt opgeven of een resource al dan niet [wordt geïmplementeerd.](conditional-resource-deployment.md)

## <a name="syntax"></a>Syntax

# <a name="json"></a>[JSON](#tab/json)

Voeg het `copy` element toe aan de sectie resources van uw sjabloon om meerdere exemplaren van de resource te implementeren. Het `copy` element heeft de volgende algemene indeling:

```json
"copy": {
  "name": "<name-of-loop>",
  "count": <number-of-iterations>,
  "mode": "serial" <or> "parallel",
  "batchSize": <number-to-deploy-serially>
}
```

De `name` eigenschap is een waarde die de lus identificeert. De `count` eigenschap geeft het aantal iteraties aan dat u wilt gebruiken voor het resourcetype.

Gebruik de eigenschappen en om op te geven of de resources parallel of op volgorde `mode` `batchSize` worden geïmplementeerd. Deze eigenschappen worden beschreven in [Serieel of Parallel.](#serial-or-parallel)

# <a name="bicep"></a>[Bicep](#tab/bicep)

Lussen kunnen worden gebruikt om meerdere resources te declaren door:

- Itereren over een matrix:

  ```bicep
  @batchSize(<number>)
  resource <resource-symbolic-name> '<resource-type>@<api-version>' = [for <item> in <collection>: {
    <resource-properties>
  }]
  ```

- De elementen van een matrix itereren

  ```bicep
  @batchSize(<number>)
  resource <resource-symbolic-name> '<resource-type>@<api-version>' = [for (<item>, <index>) in <collection>: {
    <resource-properties>
  }]
  ```

- Lusindex gebruiken

  ```bicep
  @batchSize(<number>)
  resource <resource-symbolic-name> '<resource-type>@<api-version>' = [for <index> in range(<start>, <stop>): {
    <resource-properties>
  }]
  ```

---

## <a name="copy-limits"></a>Limieten voor kopiëren

Het aantal mag niet groter zijn dan 800.

Het aantal mag geen negatief getal zijn. Dit kan nul zijn als u de sjabloon implementeert met een recente versie van Azure CLI, PowerShell of REST API. U moet met name het volgende gebruiken:

- Azure PowerShell **2.6** of hoger
- Azure CLI **2.0.74** of hoger
- REST API **versie 2019-05-10** of hoger
- [Voor gekoppelde implementaties](linked-templates.md) moet API-versie **2019-05-10** of hoger worden gebruikt voor het resourcetype van de implementatie

Eerdere versies van PowerShell, CLI en de REST API bieden geen ondersteuning voor nul voor aantal.

Wees voorzichtig met het [gebruik van de implementatie in de volledige](deployment-modes.md) modus met de kopieerlus. Als u de volledige modus opnieuw in een resourcegroep wilt toepassen, worden alle resources verwijderd die niet zijn opgegeven in de sjabloon nadat de kopieerlus is opgelost.

## <a name="resource-iteration"></a>Resource-iteratie

In het volgende voorbeeld wordt het aantal opslagaccounts gemaakt dat is opgegeven in de `storageCount` parameter .

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageCount": {
      "type": "int",
      "defaultValue": 2
    }
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": {},
      "copy": {
        "name": "storagecopy",
        "count": "[parameters('storageCount')]"
      }
    }
  ]
}
```

U ziet dat de naam van elke resource de functie bevat, die `copyIndex()` de huidige iteratie in de lus retourneert. `copyIndex()` is gebaseerd op nul. Het volgende voorbeeld:

```json
"name": "[concat('storage', copyIndex())]",
```

Hiermee maakt u de volgende namen:

- storage0
- storage1
- storage2.

Als u de indexwaarde wilt verschuiven, kunt u een waarde doorgeven in de functie `copyIndex()`. Het aantal iteraties wordt nog steeds opgegeven in het kopieerelement, maar de waarde van `copyIndex` wordt verschoven door de opgegeven waarde. Het volgende voorbeeld:

```json
"name": "[concat('storage', copyIndex(1))]",
```

Hiermee maakt u de volgende namen:

- storage1
- storage2
- storage3

De kopieerbewerking is handig bij het werken met matrices, omdat u elk element in de matrix kunt itereren. Gebruik de `length` functie in de matrix om het aantal voor iteraties op te geven en om de huidige index in de matrix op te `copyIndex` halen.

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param storageCount int = 2

resource storage_id 'Microsoft.Storage/storageAccounts@2019-04-01' = [for i in range(0, storageCount): {
  name: '${i}storage${uniqueString(resourceGroup().id)}'
  location: resourceGroup().location
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'Storage'
  properties: {}
}]
```

U ziet dat de index `i` wordt gebruikt bij het maken van de resourcenaam van het opslagaccount.

---

In het volgende voorbeeld wordt één opslagaccount gemaakt voor elke naam die is opgegeven in de parameter .

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "storageNames": {
          "type": "array",
          "defaultValue": [
            "contoso",
            "fabrikam",
            "coho"
          ]
      }
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[concat(parameters('storageNames')[copyIndex()], uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": {},
      "copy": {
        "name": "storagecopy",
        "count": "[length(parameters('storageNames'))]"
      }
    }
  ],
  "outputs": {}
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param storageNames array = [
  'contoso'
  'fabrikam'
  'coho'
]

resource storageNames_id 'Microsoft.Storage/storageAccounts@2019-04-01' = [for name in storageNames: {
  name: concat(name, uniqueString(resourceGroup().id))
  location: resourceGroup().location
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'Storage'
  properties: {}
}]
```

---

Als u waarden van de geïmplementeerde resources wilt retourneren, kunt u [kopiëren gebruiken in de sectie uitvoer.](copy-outputs.md)

## <a name="serial-or-parallel"></a>Serieel of parallel

Standaard worden Resource Manager resources parallel gemaakt. Er wordt geen limiet toegepast op het aantal resources dat parallel is geïmplementeerd, anders dan de totale limiet van 800 resources in de sjabloon. De volgorde waarin ze worden gemaakt, wordt niet gegarandeerd.

Mogelijk wilt u echter opgeven dat de resources op volgorde worden geïmplementeerd. Wanneer u bijvoorbeeld een productieomgeving bij werkt, wilt u de updates mogelijk spreiden, zodat slechts een bepaald aantal tegelijk wordt bijgewerkt.

Als u bijvoorbeeld opslagaccounts twee tegelijk wilt implementeren, gebruikt u:

# <a name="json"></a>[JSON](#tab/json)

Als u meer dan één exemplaar van een resource serieel wilt implementeren, stelt u in op serieel en op het aantal exemplaren dat `mode` tegelijk moet worden  `batchSize` geïmplementeerd. Met de seriële modus maakt Resource Manager een afhankelijkheid van eerdere exemplaren in de lus, zodat deze niet één batch start totdat de vorige batch is voltooid.

De waarde voor `batchSize` mag niet groter zijn dan de waarde voor in het `count` kopieerelement.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "copy": {
        "name": "storagecopy",
        "count": 4,
        "mode": "serial",
        "batchSize": 2
      },
      "properties": {}
    }
  ],
  "outputs": {}
}
```

De `mode` eigenschap accepteert ook **parallelle**, wat de standaardwaarde is.

# <a name="bicep"></a>[Bicep](#tab/bicep)

Als u meer dan één exemplaar van een resource serieel wilt implementeren, stelt u deator in op het aantal exemplaren dat `batchSize` [](./bicep-file.md#resource-and-module-decorators) tegelijk moet worden geïmplementeerd. In de seriële modus maakt Resource Manager afhankelijkheid van eerdere exemplaren in de lus, zodat er niet één batch wordt starten totdat de vorige batch is voltooid.

```bicep
@batchSize(2)
resource storage_id 'Microsoft.Storage/storageAccounts@2019-04-01' = [for i in range(0, 4): {
  name: '${i}storage${uniqueString(resourceGroup().id)}'
  location: resourceGroup().location
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'Storage'
  properties: {}
}]
```

---

## <a name="iteration-for-a-child-resource"></a>Iteratie voor een onderliggende resource

U kunt geen kopieerlus gebruiken voor een onderliggende resource. Als u meer dan één exemplaar van een resource wilt maken dat u doorgaans als genest binnen een andere resource definieert, moet u in plaats daarvan die resource maken als een resource op het hoogste niveau. U definieert de relatie met de bovenliggende resource via het type en de naameigenschappen.

Stel bijvoorbeeld dat u een gegevensset doorgaans definieert als een onderliggende resource binnen een data factory.

```json
"resources": [
{
  "type": "Microsoft.DataFactory/factories",
  "name": "exampleDataFactory",
  ...
  "resources": [
    {
      "type": "datasets",
      "name": "exampleDataSet",
      "dependsOn": [
        "exampleDataFactory"
      ],
      ...
    }
  ]
```

Als u meer dan één gegevensset wilt maken, verplaatst u deze buiten de data factory. De gegevensset moet zich op hetzelfde niveau als de data factory, maar het is nog steeds een onderliggende resource van de data factory. U behoudt de relatie tussen de gegevensset en data factory via het type en de naameigenschappen. Aangezien type niet langer kan worden afgeleid van de positie in de sjabloon, moet u het volledig gekwalificeerde type in de indeling geven: `{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}` .

Als u een bovenliggende/onderliggende relatie met een exemplaar van de data factory, geeft u een naam op voor de gegevensset die de naam van de bovenliggende resource bevat. Gebruik de notatie `{parent-resource-name}/{child-resource-name}`.

In het volgende voorbeeld ziet u de implementatie:

# <a name="json"></a>[JSON](#tab/json)

```json
"resources": [
{
  "type": "Microsoft.DataFactory/factories",
  "name": "exampleDataFactory",
  ...
},
{
  "type": "Microsoft.DataFactory/factories/datasets",
  "name": "[concat('exampleDataFactory', '/', 'exampleDataSet', copyIndex())]",
  "dependsOn": [
    "exampleDataFactory"
  ],
  "copy": {
    "name": "datasetcopy",
    "count": "3"
  },
  ...
}]
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
resource dataFactoryName_resource 'Microsoft.DataFactory/factories@2018-06-01' = {
  name: "exampleDataFactory"
  ...
}

resource dataFactoryName_ArmtemplateTestDatasetIn 'Microsoft.DataFactory/factories/datasets@2018-06-01' = [for i in range(0, 3): {
  name: 'exampleDataFactory/exampleDataset${i}'
  ...
}
```

---

## <a name="example-templates"></a>Voorbeeldsjablonen

De volgende voorbeelden tonen algemene scenario's voor het maken van meer dan één exemplaar van een resource of eigenschap.

|Template  |Beschrijving  |
|---------|---------|
|[Opslag kopiëren](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copystorage.json) |Implementeert meer dan één opslagaccount met een indexnummer in de naam. |
|[Seriële kopieopslag](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/serialcopystorage.json) |Implementeert meerdere opslagaccounts één voor één. De naam bevat het indexnummer. |
|[Opslag kopiëren met matrix](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copystoragewitharray.json) |Implementeert verschillende opslagaccounts. De naam bevat een waarde uit een matrix. |

## <a name="next-steps"></a>Volgende stappen

- Zie Define the order for deploying resources in ARM templates (De volgorde voor het implementeren van resources in ARM-sjablonen definiëren) om afhankelijkheden in te stellen van resources die zijn gemaakt in een [kopieerlus.](define-resource-dependency.md)
- Zie Zelfstudie: Meerdere resource-exemplaren maken met [ARM-sjablonen voor een zelfstudie.](template-tutorial-create-multiple-instances.md)
- Raadpleeg [Complexe cloudimplementaties beheren met behulp van geavanceerde functies voor ARM-sjablonen](/learn/modules/manage-deployments-advanced-arm-template-features/) voor een Microsoft Learn-module over het kopiëren van resources.
- Zie voor ander gebruik van de kopieerlus:
  - [Iteratie van eigenschappen in ARM-sjablonen](copy-properties.md)
  - [Variabele iteratie in ARM-sjablonen](copy-variables.md)
  - [Uitvoer-iteratie in ARM-sjablonen](copy-outputs.md)
- Zie Kopiëren gebruiken voor meer informatie over het gebruik [van kopiëren met geneste sjablonen.](linked-templates.md#using-copy)
