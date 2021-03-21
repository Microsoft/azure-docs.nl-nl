---
title: Sjabloon functies-logisch
description: Hierin worden de functies beschreven die u kunt gebruiken in een Azure Resource Manager sjabloon (ARM-sjabloon) om logische waarden te bepalen.
ms.topic: conceptual
ms.date: 11/18/2020
ms.openlocfilehash: 27d94f10374daf0b9a351469579a5eb659cf5445
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96920482"
---
# <a name="logical-functions-for-arm-templates"></a>Logische functies voor ARM-sjablonen

Resource Manager biedt verschillende functies voor het maken van vergelijkingen in uw Azure Resource Manager sjabloon (ARM-sjabloon):

* [and](#and)
* [booleaans](#bool)
* [terecht](#false)
* [If](#if)
* [ten](#not)
* [or](#or)
* [echte](#true)

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="and"></a>en

`and(arg1, arg2, ...)`

Controleert of alle parameter waarden waar zijn. De `and` functie wordt niet ondersteund in Bicep. Gebruik `&&` in plaats daarvan de operator.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| Arg1 |Ja |booleaans |De eerste waarde om te controleren of deze waar is. |
| Arg2 |Ja |booleaans |De tweede waarde om te controleren of waar is. |
| aanvullende argumenten |Nee |booleaans |Aanvullende argumenten om te controleren of deze waar zijn. |

### <a name="return-value"></a>Retourwaarde

Retourneert **waar** als alle waarden waar zijn. anders **False**.

### <a name="examples"></a>Voorbeelden

In de volgende [voorbeeld sjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/andornot.json) ziet u hoe u logische functies gebruikt.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "andExampleOutput": {
      "type": "bool",
      "value": "[and(bool('true'), bool('false'))]"
    },
    "orExampleOutput": {
      "type": "bool",
      "value": "[or(bool('true'), bool('false'))]"
    },
    "notExampleOutput": {
      "type": "bool",
      "value": "[not(bool('true'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output andExampleOutput bool = bool('true') && bool('false')
output orExampleOutput bool = bool('true') || bool('false')
output notExampleOutput bool = !(bool('true'))
```

---

De uitvoer van het vorige voor beeld is:

| Naam | Type | Waarde |
| ---- | ---- | ----- |
| andExampleOutput | Booleaanse waarde | Niet waar |
| orExampleOutput | Booleaanse waarde | True |
| notExampleOutput | Booleaanse waarde | Niet waar |

## <a name="bool"></a>booleaans

`bool(arg1)`

Zet de para meter om in een Boole-waarde.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| Arg1 |Ja |teken reeks of int |De waarde die moet worden geconverteerd naar een Boole. |

### <a name="return-value"></a>Retourwaarde

Een Boolean van de geconverteerde waarde.

### <a name="remarks"></a>Opmerkingen

U kunt ook [True ()](#true) en [False ()](#false) gebruiken om Booleaanse waarden op te halen.

### <a name="examples"></a>Voorbeelden

In de volgende [voorbeeld sjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/bool.json) ziet u hoe u BOOL gebruikt met een teken reeks of een geheel getal.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "trueString": {
      "type": "bool",
      "value": "[bool('true')]",
    },
    "falseString": {
      "type": "bool",
      "value": "[bool('false')]"
    },
    "trueInt": {
      "type": "bool",
      "value": "[bool(1)]"
    },
    "falseInt": {
      "type": "bool",
      "value": "[bool(0)]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output trueString bool = bool('true')
output falseString bool = bool('false')
output trueInt bool = bool(1)
output falseInt bool = bool(0)
```

---
De uitvoer van het vorige voor beeld met de standaard waarden is:

| Naam | Type | Waarde |
| ---- | ---- | ----- |
| trueString | Booleaanse waarde | True |
| falseString | Booleaanse waarde | Niet waar |
| trueInt | Booleaanse waarde | True |
| falseInt | Booleaanse waarde | Niet waar |

## <a name="false"></a>onjuist

`false()`

Retourneert onwaar. De `false` functie is niet beschikbaar in Bicep.  Gebruik `false` in plaats daarvan het sleutel woord.

### <a name="parameters"></a>Parameters

De functie false accepteert geen para meters.

### <a name="return-value"></a>Retourwaarde

Een Booleaanse waarde die altijd onwaar is.

### <a name="example"></a>Voorbeeld

In het volgende voor beeld wordt een onjuiste uitvoer waarde geretourneerd.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "falseOutput": {
      "type": "bool",
      "value": "[false()]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output falseOutput bool = false
```

---

De uitvoer van het vorige voor beeld is:

| Naam | Type | Waarde |
| ---- | ---- | ----- |
| falseOutput | Booleaanse waarde | Niet waar |

## <a name="if"></a>if

`if(condition, trueValue, falseValue)`

Retourneert een waarde op basis van het feit of een voor waarde waar of onwaar is. De `if` functie wordt niet ondersteund in Bicep. Gebruik `?:` in plaats daarvan de operator.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| regeling |Ja |booleaans |De waarde om te controleren of deze True of False is. |
| trueValue |Ja | teken reeks, int, object of matrix |De waarde die moet worden geretourneerd als de voor waarde waar is. |
| falseValue |Ja | teken reeks, int, object of matrix |De waarde die moet worden geretourneerd als de voor waarde ONWAAR is. |

### <a name="return-value"></a>Retourwaarde

Retourneert een tweede para meter wanneer de eerste para meter **True** is; anders retourneert de derde para meter.

### <a name="remarks"></a>Opmerkingen

Als de voor waarde **waar** is, wordt alleen de waarde True geëvalueerd. Als de voor waarde **Onwaar** is, wordt alleen de waarde ONWAAR geëvalueerd. Met de `if` functie kunt u expressies toevoegen die alleen voorwaardelijk geldig zijn. U kunt bijvoorbeeld verwijzen naar een resource die zich onder één voor waarde bevindt, maar niet onder de andere voor waarde. In de volgende sectie ziet u een voor beeld van een conditioneel evalueren van expressies.

### <a name="examples"></a>Voorbeelden

In de volgende [voorbeeld sjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/if.json) ziet u hoe u de `if` functie gebruikt.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
  ],
  "outputs": {
    "yesOutput": {
      "type": "string",
      "value": "[if(equals('a', 'a'), 'yes', 'no')]"
    },
    "noOutput": {
      "type": "string",
      "value": "[if(equals('a', 'b'), 'yes', 'no')]"
    },
    "objectOutput": {
      "type": "object",
      "value": "[if(equals('a', 'a'), json('{\"test\": \"value1\"}'), json('null'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output yesOutput string = 'a' == 'a' ? 'yes' : 'no'
output noOutput string = 'a' == 'b' ? 'yes' : 'no'
output objectOutput object = 'a' == 'a' ? json('{"test": "value1"}') : json('null')
```

---

De uitvoer van het vorige voor beeld is:

| Naam | Type | Waarde |
| ---- | ---- | ----- |
| yesOutput | Tekenreeks | ja |
| geen uitvoer | Tekenreeks | nee |
| objectOutput | Object | {"test": "waarde1"} |

In de volgende [voorbeeld sjabloon](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/conditionWithReference.json) ziet u hoe u deze functie kunt gebruiken met expressies die alleen voorwaarde geldig zijn.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string"
    },
    "location": {
      "type": "string"
    },
    "logAnalytics": {
      "type": "string",
      "defaultValue": ""
    }
  },
  "resources": [
    {
      "condition": "[not(empty(parameters('logAnalytics')))]",
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "apiVersion": "2017-03-30",
      "name": "[concat(parameters('vmName'),'/omsOnboarding')]",
      "location": "[parameters('location')]",
      "properties": {
        "publisher": "Microsoft.EnterpriseCloud.Monitoring",
        "type": "MicrosoftMonitoringAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "workspaceId": "[if(not(empty(parameters('logAnalytics'))), reference(parameters('logAnalytics'), '2015-11-01-preview').customerId, json('null'))]"
        },
        "protectedSettings": {
          "workspaceKey": "[if(not(empty(parameters('logAnalytics'))), listKeys(parameters('logAnalytics'), '2015-11-01-preview').primarySharedKey, json('null'))]"
        }
      }
    }
  ],
  "outputs": {
    "mgmtStatus": {
      "type": "string",
      "value": "[if(not(empty(parameters('logAnalytics'))), 'Enabled monitoring for VM!', 'Nothing to enable')]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

> [!NOTE]
> `Conditions` zijn nog niet geïmplementeerd in Bicep. Zie [voor waarden](https://github.com/Azure/bicep/issues/186).

---

## <a name="not"></a>not

`not(arg1)`

Zet Boole-waarde om in tegenovergestelde waarde. De `not` functie wordt niet ondersteund in Bicep. Gebruik `!` in plaats daarvan de operator.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| Arg1 |Ja |booleaans |De waarde die moet worden geconverteerd. |

### <a name="return-value"></a>Retourwaarde

Retourneert **waar** als de para meter **False** is. Retourneert **Onwaar** wanneer de para meter **True** is.

### <a name="examples"></a>Voorbeelden

In de volgende [voorbeeld sjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/andornot.json) ziet u hoe u logische functies gebruikt.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "andExampleOutput": {
      "type": "bool",
      "value": "[and(bool('true'), bool('false'))]",
    },
    "orExampleOutput": {
      "type": "bool",
      "value": "[or(bool('true'), bool('false'))]"
    },
    "notExampleOutput": {
      "type": "bool",
      "value": "[not(bool('true'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output andExampleOutput bool = bool('true') && bool('false')
output orExampleOutput bool = bool('true') || bool('false')
output notExampleOutput bool = !(bool('true'))
```

---

De uitvoer van het vorige voor beeld is:

| Naam | Type | Waarde |
| ---- | ---- | ----- |
| andExampleOutput | Booleaanse waarde | Niet waar |
| orExampleOutput | Booleaanse waarde | True |
| notExampleOutput | Booleaanse waarde | Niet waar |

De volgende [voorbeeld sjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/not-equals.json) gebruikt **niet** met [gelijk aan](template-functions-comparison.md#equals).

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
  ],
  "outputs": {
    "checkNotEquals": {
      "type": "bool",
      "value": "[not(equals(1, 2))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output checkNotEquals bool = !(1 == 2)
```

---

De uitvoer van het vorige voor beeld is:

| Naam | Type | Waarde |
| ---- | ---- | ----- |
| checkNotEquals | Booleaanse waarde | True |

## <a name="or"></a>of

`or(arg1, arg2, ...)`

Controleert of een parameter waarde waar is. De `or` functie wordt niet ondersteund in Bicep. Gebruik `||` in plaats daarvan de operator.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| Arg1 |Ja |booleaans |De eerste waarde om te controleren of deze waar is. |
| Arg2 |Ja |booleaans |De tweede waarde om te controleren of waar is. |
| aanvullende argumenten |Nee |booleaans |Aanvullende argumenten om te controleren of deze waar zijn. |

### <a name="return-value"></a>Retourwaarde

Retourneert **waar** als een wille keurige waarde waar is; anders **False**.

### <a name="examples"></a>Voorbeelden

In de volgende [voorbeeld sjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/andornot.json) ziet u hoe u logische functies gebruikt.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "andExampleOutput": {
      "value": "[and(bool('true'), bool('false'))]",
      "type": "bool"
    },
    "orExampleOutput": {
      "value": "[or(bool('true'), bool('false'))]",
      "type": "bool"
    },
    "notExampleOutput": {
      "value": "[not(bool('true'))]",
      "type": "bool"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output andExampleOutput bool = bool('true') && bool('false')
output orExampleOutput bool = bool('true') || bool('false')
output notExampleOutput bool = !(bool('true'))
```

---

De uitvoer van het vorige voor beeld is:

| Naam | Type | Waarde |
| ---- | ---- | ----- |
| andExampleOutput | Booleaanse waarde | Niet waar |
| orExampleOutput | Booleaanse waarde | True |
| notExampleOutput | Booleaanse waarde | Niet waar |

## <a name="true"></a>true

`true()`

Retourneert True. De `true` functie is niet beschikbaar in Bicep.  Gebruik `true` in plaats daarvan het sleutel woord.

### <a name="parameters"></a>Parameters

De functie True accepteert geen para meters. De `true` functie is niet beschikbaar in Bicep.  Gebruik `true` in plaats daarvan het sleutel woord.

### <a name="return-value"></a>Retourwaarde

Een Booleaanse waarde die altijd waar is.

### <a name="example"></a>Voorbeeld

In het volgende voor beeld wordt een werkelijke uitvoer waarde geretourneerd.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "trueOutput": {
      "type": "bool",
      "value": "[true()]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output trueOutput bool = true
```

---

De uitvoer van het vorige voor beeld is:

| Naam | Type | Waarde |
| ---- | ---- | ----- |
| trueOutput | Booleaanse waarde | True |

## <a name="next-steps"></a>Volgende stappen

* Zie [inzicht krijgen in de structuur en syntaxis van arm-sjablonen](template-syntax.md)voor een beschrijving van de secties in een arm-sjabloon.
