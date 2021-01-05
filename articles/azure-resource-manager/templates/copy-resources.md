---
title: Meerdere exemplaren van bronnen implementeren
description: Gebruik kopieer bewerkingen en matrices in een Azure Resource Manager sjabloon (ARM-sjabloon) om het resource type meerdere keren te implementeren.
ms.topic: conceptual
ms.date: 12/21/2020
ms.openlocfilehash: c9bcb22ec53129520fd9574d0eb58b1e5777531e
ms.sourcegitcommit: a4533b9d3d4cd6bb6faf92dd91c2c3e1f98ab86a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/22/2020
ms.locfileid: "97724490"
---
# <a name="resource-iteration-in-arm-templates"></a>Resource iteratie in ARM-sjablonen

In dit artikel wordt beschreven hoe u meer dan één exemplaar van een resource in uw Azure Resource Manager-sjabloon maakt (ARM-sjabloon). Door het element toe te voegen `copy` aan de sectie resources van uw sjabloon, kunt u het aantal resources dat moet worden geïmplementeerd, dynamisch instellen. U hoeft ook geen sjabloon syntaxis te herhalen.

U kunt ook gebruiken `copy` met [Eigenschappen](copy-properties.md), [variabelen](copy-variables.md)en [uitvoer](copy-outputs.md).

Zie [voor waarde-element](conditional-resource-deployment.md)als u wilt opgeven of een resource helemaal moet worden geïmplementeerd.

## <a name="syntax"></a>Syntax

Het `copy` element heeft de volgende algemene indeling:

```json
"copy": {
  "name": "<name-of-loop>",
  "count": <number-of-iterations>,
  "mode": "serial" <or> "parallel",
  "batchSize": <number-to-deploy-serially>
}
```

De `name` eigenschap is een wille keurige waarde die de lus identificeert. De `count` eigenschap geeft het aantal iteraties op dat u voor het resource type wilt.

Gebruik de `mode` `batchSize` Eigenschappen en om op te geven of de resources parallel of sequentieel worden geïmplementeerd. Deze eigenschappen worden beschreven in [serieel of parallel](#serial-or-parallel).

## <a name="copy-limits"></a>Limieten kopiëren

De telling mag niet groter zijn dan 800.

De telling kan geen negatief getal zijn. Dit kan nul zijn als u de sjabloon implementeert met een recente versie van Azure CLI, Power shell of REST API. U moet het volgende gebruiken:

* Azure PowerShell **2,6** of hoger
* Azure CLI **2.0.74** of hoger
* REST API versie **2019-05-10** of hoger
* [Gekoppelde implementaties](linked-templates.md) moeten API-versie **2019-05-10** of hoger voor het bron type implementatie gebruiken

Eerdere versies van Power shell, CLI en de REST API bieden geen ondersteuning voor aantal nul.

Wees voorzichtig met het gebruik van de implementatie van de [volledige modus](deployment-modes.md) met Copy. Als u de volledige modus opnieuw implementeert naar een resource groep, worden alle resources verwijderd die niet zijn opgegeven in de sjabloon na het omzetten van de Kopieer bewerking.

## <a name="resource-iteration"></a>Resource herhaling

In het volgende voor beeld wordt het aantal opslag accounts gemaakt dat is opgegeven in de `storageCount` para meter.

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
    ],
    "outputs": {}
}
```

U ziet dat de naam van elke resource de `copyIndex()` functie bevat, die de huidige iteratie in de lus retourneert. `copyIndex()` is gebaseerd op nul. Het volgende voor beeld:

```json
"name": "[concat('storage', copyIndex())]",
```

Hiermee maakt u deze namen:

* storage0
* storage1
* storage2.

Als u de index waarde wilt verschuiven, kunt u een waarde in de functie door geven `copyIndex()` . Het aantal iteraties is nog steeds opgegeven in het exemplaar element, maar de waarde van `copyIndex` wordt verrekend met de opgegeven waarde. Het volgende voor beeld:

```json
"name": "[concat('storage', copyIndex(1))]",
```

Hiermee maakt u deze namen:

* storage1
* storage2
* storage3

De Kopieer bewerking is handig bij het werken met matrices, omdat u elk element in de matrix kunt door lopen. Gebruik de `length` functie op de matrix om het aantal voor herhalingen op te geven en `copyIndex` om de huidige index in de matrix op te halen.

In het volgende voor beeld wordt één opslag account gemaakt voor elke naam die in de para meter wordt gegeven.

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

Als u waarden van de geïmplementeerde resources wilt retour neren, kunt u [in de sectie outputs de Kopieer versie](copy-outputs.md)gebruiken.

## <a name="serial-or-parallel"></a>Serieel of parallel

Resource Manager maakt standaard de resources parallel. Er geldt geen limiet voor het aantal resources dat parallel is geïmplementeerd, met uitzonde ring van de totale limiet van 800 resources in de sjabloon. De volg orde waarin ze worden gemaakt, is niet gegarandeerd.

Het is echter mogelijk dat u wilt opgeven dat de resources in de juiste volg orde worden geïmplementeerd. Wanneer u bijvoorbeeld een productie omgeving bijwerkt, wilt u mogelijk de updates spreiden zodat alleen een bepaald aantal tegelijk wordt bijgewerkt. Als u meer dan één exemplaar van een bron wilt implementeren, stelt `mode` u in op **serie** en `batchSize` op het aantal exemplaren dat tegelijk moet worden geïmplementeerd. Met de seriële modus maakt Resource Manager een afhankelijkheid van eerdere instanties in de lus, zodat deze geen batch Start totdat de vorige batch is voltooid.

De waarde voor `batchSize` kan niet groter zijn dan de waarde voor `count` in het element copy.

Als u opslag accounts bijvoorbeeld twee keer tegelijk wilt implementeren, gebruikt u:

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

De `mode` eigenschap accepteert ook **parallel**, wat de standaard waarde is.

## <a name="iteration-for-a-child-resource"></a>Herhaling voor een onderliggende resource

U kunt geen kopieer proces voor een onderliggende Resource gebruiken. Als u meer dan één exemplaar van een resource wilt maken die doorgaans wordt gedefinieerd als genest in een andere resource, moet u die resource in plaats daarvan maken als resource op het hoogste niveau. U definieert de relatie met de bovenliggende resource via de eigenschappen type en naam.

Stel bijvoorbeeld dat u een gegevensset definieert als een onderliggende bron in een data factory.

```json
"resources": [
{
  "type": "Microsoft.DataFactory/datafactories",
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

Als u meer dan één gegevensset wilt maken, verplaatst u deze buiten het data factory. De gegevensset moet op hetzelfde niveau zijn als de data factory, maar het is nog steeds een onderliggende resource van de data factory. U behoudt de relatie tussen de gegevensset en data factory via de eigenschappen type en naam. Omdat het type niet meer kan worden afgeleid van de positie in de sjabloon, moet u het volledig gekwalificeerde type opgeven in de notatie: `{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}` .

Als u een bovenliggende/onderliggende relatie met een exemplaar van de data factory wilt instellen, geeft u een naam op voor de gegevensset die de naam van de bovenliggende resource bevat. Gebruik de notatie `{parent-resource-name}/{child-resource-name}`.

In het volgende voor beeld ziet u de implementatie:

```json
"resources": [
{
  "type": "Microsoft.DataFactory/datafactories",
  "name": "exampleDataFactory",
  ...
},
{
  "type": "Microsoft.DataFactory/datafactories/datasets",
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

## <a name="example-templates"></a>Voorbeeld sjablonen

In de volgende voor beelden ziet u algemene scenario's voor het maken van meer dan één exemplaar van een resource of eigenschap.

|Template  |Beschrijving  |
|---------|---------|
|[Opslag kopiëren](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copystorage.json) |Hiermee worden meer dan één opslag account met een index nummer in de naam geïmplementeerd. |
|[Opslag van seriële kopieën](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/serialcopystorage.json) |Hiermee worden verschillende opslag accounts tegelijkertijd geïmplementeerd. De naam bevat het index nummer. |
|[Opslag kopiëren met een matrix](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copystoragewitharray.json) |Hiermee worden verschillende opslag accounts geïmplementeerd. De naam bevat een waarde uit een matrix. |

## <a name="next-steps"></a>Volgende stappen

* Zie [de volg orde voor het implementeren van resources in arm-sjablonen definiëren voor het](define-resource-dependency.md)instellen van afhankelijkheden voor resources die zijn gemaakt in een copy-lus.
* Zie [zelf studie: meerdere resource-instanties maken met arm-sjablonen](template-tutorial-create-multiple-instances.md)om een zelf studie te door lopen.
* Zie [complexe Cloud implementaties beheren met behulp van geavanceerde arm-sjabloon functies](/learn/modules/manage-deployments-advanced-arm-template-features/)voor een Microsoft Learn module die betrekking heeft op het kopiëren van de resource.
* Zie voor andere toepassingen van het element copy:
  * [Eigenschaps herhaling in ARM-sjablonen](copy-properties.md)
  * [Variabele herhaling in ARM-sjablonen](copy-variables.md)
  * [Uitvoer herhaling in ARM-sjablonen](copy-outputs.md)
* Zie [using Copy](linked-templates.md#using-copy)voor informatie over het gebruik van kopiëren met geneste sjablonen.
