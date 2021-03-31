---
title: Fouten van bovenliggende resources
description: Hierin wordt beschreven hoe u fouten oplost bij het werken met een bovenliggende resource in een Azure Resource Manager sjabloon.
ms.topic: troubleshooting
ms.date: 08/01/2018
ms.openlocfilehash: 474cb85d16382136e24e5502b87ba8a1a65488ef
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "84673657"
---
# <a name="resolve-errors-for-parent-resources"></a>Fouten voor bovenliggende resources oplossen

In dit artikel worden de fouten beschreven die u kunt krijgen bij het implementeren van een resource die afhankelijk is van een bovenliggende resource.

## <a name="symptom"></a>Symptoom

Wanneer u een resource implementeert die een onderliggend item van een andere resource is, wordt mogelijk de volgende fout weer gegeven:

```
Code=ParentResourceNotFound;
Message=Can not perform requested operation on nested resource. Parent resource 'exampleserver' not found."
```

## <a name="cause"></a>Oorzaak

Wanneer een resource een onderliggend item van een andere resource is, moet de bovenliggende resource bestaan voordat de onderliggende resource wordt gemaakt. Met de naam van de onderliggende resource wordt de verbinding met de bovenliggende resource gedefinieerd. De naam van de onderliggende resource heeft de indeling `<parent-resource-name>/<child-resource-name>` . Een SQL Database kan bijvoorbeeld als volgt worden gedefinieerd:

```json
{
  "type": "Microsoft.Sql/servers/databases",
  "name": "[concat(variables('databaseServerName'), '/', parameters('databaseName'))]",
  ...
```

Als u zowel de server als de data base in dezelfde sjabloon implementeert, maar geen afhankelijkheid op de server opgeeft, wordt de implementatie van de data base mogelijk gestart voordat de server is geïmplementeerd.

Als de bovenliggende resource al bestaat en niet is geïmplementeerd in dezelfde sjabloon, wordt deze fout weer geven wanneer Resource Manager de onderliggende resource niet kan koppelen aan het bovenliggende item. Deze fout kan optreden wanneer de onderliggende resource niet de juiste indeling heeft of de onderliggende resource wordt geïmplementeerd naar een resource groep die verschilt van de resource groep voor de bovenliggende resource.

## <a name="solution"></a>Oplossing

Als u deze fout wilt oplossen wanneer bovenliggende en onderliggende resources in dezelfde sjabloon zijn geïmplementeerd, neemt u een afhankelijkheid op.

```json
"dependsOn": [
  "[variables('databaseServerName')]"
]
```

Als u deze fout wilt oplossen wanneer de bovenliggende resource eerder in een andere sjabloon is geïmplementeerd, stelt u geen afhankelijkheid in. Implementeer het onderliggende item in plaats daarvan naar dezelfde resource groep en geef de naam van de bovenliggende resource op.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "sqlServerName": {
      "type": "string"
    },
    "databaseName": {
      "type": "string"
    }
  },
  "resources": [
    {
      "type": "Microsoft.Sql/servers/databases",
      "apiVersion": "2014-04-01",
      "name": "[concat(parameters('sqlServerName'), '/', parameters('databaseName'))]",
      "location": "[resourceGroup().location]",
      "properties": {
        "collation": "SQL_Latin1_General_CP1_CI_AS",
        "edition": "Basic"
      }
    }
  ],
  "outputs": {}
}
```

Zie [de order voor het implementeren van resources in azure Resource Manager-sjablonen definiëren](define-resource-dependency.md)voor meer informatie.