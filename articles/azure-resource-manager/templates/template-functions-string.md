---
title: 'Sjabloon functies: teken reeks'
description: Hierin worden de functies beschreven die u kunt gebruiken in een Azure Resource Manager sjabloon (ARM-sjabloon) om met teken reeksen te werken.
ms.topic: conceptual
ms.date: 03/02/2021
ms.openlocfilehash: cff1424562b45bc722f87fa3ec896c1c641ee758
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105108839"
---
# <a name="string-functions-for-arm-templates"></a>Teken reeks functies voor ARM-sjablonen

Resource Manager biedt de volgende functies voor het werken met teken reeksen in uw Azure Resource Manager-sjabloon (ARM-sjabloon):

* [base64](#base64)
* [base64ToJson](#base64tojson)
* [base64ToString](#base64tostring)
* [concat](#concat)
* [daarin](#contains)
* [dataUri](#datauri)
* [dataUriToString](#datauritostring)
* [leeg](#empty)
* [endsWith](#endswith)
* [instantie](#first)
* [Formatteer](#format)
* [guid](#guid)
* [indexOf](#indexof)
* [JSON](#json)
* [duren](#last)
* [lastIndexOf](#lastindexof)
* [length](#length)
* [newGuid](#newguid)
* [padLeft](#padleft)
* [vervangen](#replace)
* [verdergaan](#skip)
* [delen](#split)
* [startsWith](#startswith)
* [tekenreeks](#string)
* [subtekenreeks](#substring)
* [Houd](#take)
* [toLower](#tolower)
* [toUpper](#toupper)
* [interne](#trim)
* [uniqueString](#uniquestring)
* [URI](#uri)
* [uriComponent](#uricomponent)
* [uriComponentToString](#uricomponenttostring)

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="base64"></a>base64

`base64(inputString)`

Retourneert de base64-weer gave van de invoer teken reeks.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Description |
|:--- |:--- |:--- |:--- |
| inputString |Yes |tekenreeks |De waarde die als een base64-weer gave moet worden geretourneerd. |

### <a name="return-value"></a>Retourwaarde

Een teken reeks met de base64-weer gave.

### <a name="examples"></a>Voorbeelden

In de volgende [voorbeeld sjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/base64.json) ziet u hoe u de base64-functie gebruikt.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "stringData": {
      "type": "string",
      "defaultValue": "one, two, three"
    },
    "jsonFormattedData": {
      "type": "string",
      "defaultValue": "{'one': 'a', 'two': 'b'}"
    }
  },
  "variables": {
    "base64String": "[base64(parameters('stringData'))]",
    "base64Object": "[base64(parameters('jsonFormattedData'))]"
  },
  "resources": [
  ],
  "outputs": {
    "base64Output": {
      "type": "string",
      "value": "[variables('base64String')]"
    },
    "toStringOutput": {
      "type": "string",
      "value": "[base64ToString(variables('base64String'))]"
    },
    "toJsonOutput": {
      "type": "object",
      "value": "[base64ToJson(variables('base64Object'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param stringData string = 'one, two, three'
param jsonFormattedData string = '{\'one\': \'a\', \'two\': \'b\'}'

var base64String = base64(stringData)
var base64Object = base64(jsonFormattedData)

output base64Output string = base64String
output toStringOutput string = base64ToString(base64String)
output toJsonOutput object = base64ToJson(base64Object)
```

---

De uitvoer van het vorige voor beeld met de standaard waarden is:

| Naam | Type | Waarde |
| ---- | ---- | ----- |
| base64Output | Tekenreeks | b25lLCB0d28sIHRocmVl |
| toStringOutput | Tekenreeks | Een twee drie |
| toJsonOutput | Object | {"een": "a", "twee": "b"} |

## <a name="base64tojson"></a>base64ToJson

`base64tojson`

Converteert een base64-weer gave naar een JSON-object.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Description |
|:--- |:--- |:--- |:--- |
| base64Value |Yes |tekenreeks |De base64-weer gave die moet worden geconverteerd naar een JSON-object. |

### <a name="return-value"></a>Retourwaarde

Een JSON-object.

### <a name="examples"></a>Voorbeelden

De volgende [voorbeeld sjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/base64.json) maakt gebruik van de functie base64ToJson om een Base64-waarde te converteren:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "stringData": {
      "type": "string",
      "defaultValue": "one, two, three"
    },
    "jsonFormattedData": {
      "type": "string",
      "defaultValue": "{'one': 'a', 'two': 'b'}"
    }
  },
  "variables": {
    "base64String": "[base64(parameters('stringData'))]",
    "base64Object": "[base64(parameters('jsonFormattedData'))]"
  },
  "resources": [
  ],
  "outputs": {
    "base64Output": {
      "type": "string",
      "value": "[variables('base64String')]"
    },
    "toStringOutput": {
      "type": "string",
      "value": "[base64ToString(variables('base64String'))]"
    },
    "toJsonOutput": {
      "type": "object",
      "value": "[base64ToJson(variables('base64Object'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param stringData string = 'one, two, three'
param jsonFormattedData string = '{\'one\': \'a\', \'two\': \'b\'}'

var base64String = base64(stringData)
var base64Object = base64(jsonFormattedData)

output base64Output string = base64String
output toStringOutput string = base64ToString(base64String)
output toJsonOutput object = base64ToJson(base64Object)

```

---

De uitvoer van het vorige voor beeld met de standaard waarden is:

| Naam | Type | Waarde |
| ---- | ---- | ----- |
| base64Output | Tekenreeks | b25lLCB0d28sIHRocmVl |
| toStringOutput | Tekenreeks | Een twee drie |
| toJsonOutput | Object | {"een": "a", "twee": "b"} |

## <a name="base64tostring"></a>base64ToString

`base64ToString(base64Value)`

Converteert een base64-weer gave naar een teken reeks.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Description |
|:--- |:--- |:--- |:--- |
| base64Value |Yes |tekenreeks |De base64-weer gave die moet worden geconverteerd naar een teken reeks. |

### <a name="return-value"></a>Retourwaarde

Een teken reeks van de geconverteerde Base64-waarde.

### <a name="examples"></a>Voorbeelden

De volgende [voorbeeld sjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/base64.json) maakt gebruik van de functie base64ToString om een Base64-waarde te converteren:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "stringData": {
      "type": "string",
      "defaultValue": "one, two, three"
    },
    "jsonFormattedData": {
      "type": "string",
      "defaultValue": "{'one': 'a', 'two': 'b'}"
    }
  },
  "variables": {
    "base64String": "[base64(parameters('stringData'))]",
    "base64Object": "[base64(parameters('jsonFormattedData'))]"
  },
  "resources": [
  ],
  "outputs": {
    "base64Output": {
      "type": "string",
      "value": "[variables('base64String')]"
    },
    "toStringOutput": {
      "type": "string",
      "value": "[base64ToString(variables('base64String'))]"
    },
    "toJsonOutput": {
      "type": "object",
      "value": "[base64ToJson(variables('base64Object'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param stringData string = 'one, two, three'
param jsonFormattedData string = '{\'one\': \'a\', \'two\': \'b\'}'

var base64String = base64(stringData)
var base64Object = base64(jsonFormattedData)

output base64Output string = base64String
output toStringOutput string = base64ToString(base64String)
output toJsonOutput object = base64ToJson(base64Object)
```

---

De uitvoer van het vorige voor beeld met de standaard waarden is:

| Naam | Type | Waarde |
| ---- | ---- | ----- |
| base64Output | Tekenreeks | b25lLCB0d28sIHRocmVl |
| toStringOutput | Tekenreeks | Een twee drie |
| toJsonOutput | Object | {"een": "a", "twee": "b"} |

## <a name="concat"></a>concat

`concat (arg1, arg2, arg3, ...)`

Combineert meerdere teken reeks waarden en retourneert de aaneengeschakelde teken reeks, of combineert meerdere matrices en retourneert de aaneengeschakelde matrix.

Bicep ondersteunt de syntaxis van een [teken reeks interpolatie](https://en.wikipedia.org/wiki/String_interpolation#) om teken reeks samenvoeging te vereenvoudigen.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Description |
|:--- |:--- |:--- |:--- |
| Arg1 |Yes |teken reeks of matrix |De eerste teken reeks of matrix voor samen voeging. |
| aanvullende argumenten |No |teken reeks of matrix |Extra teken reeksen of matrices in sequentiële volg orde voor samen voeging. |

Deze functie kan elk wille keurig aantal argumenten hebben en kan teken reeksen of matrices voor de para meters accepteren. U kunt echter geen matrixen en teken reeksen opgeven voor para meters. Teken reeksen worden alleen samengevoegd met andere teken reeksen.

### <a name="return-value"></a>Retourwaarde

Een teken reeks of matrix van aaneengeschakelde waarden.

### <a name="examples"></a>Voorbeelden

In de volgende [voorbeeld sjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/concat-string.json) ziet u hoe u twee teken reeks waarden combineert en een samengevoegde teken reeks retourneert.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "prefix": {
      "type": "string",
      "defaultValue": "prefix"
    }
  },
  "resources": [],
  "outputs": {
    "concatOutput": {
      "type": "string",
      "value": "[concat(parameters('prefix'), '-', uniqueString(resourceGroup().id))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param prefix string = 'prefix'

output concatOutput string = concat(prefix, '-', uniqueString(resourceGroup().id))
```

of

```bicep
param prefix string = 'prefix'

output concatOutput string = '${prefix}-${uniqueString(resourceGroup().id)}'
```

---

De uitvoer van het vorige voor beeld met de standaard waarden is:

| Naam | Type | Waarde |
| ---- | ---- | ----- |
| concatOutput | Tekenreeks | prefix-5yj4yjf5mbg72 |

In de volgende [voorbeeld sjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/concat-array.json) ziet u hoe u twee matrices kunt combi neren.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "firstArray": {
      "type": "array",
      "defaultValue": [
        "1-1",
        "1-2",
        "1-3"
      ]
    },
    "secondArray": {
      "type": "array",
      "defaultValue": [
        "2-1",
        "2-2",
        "2-3"
      ]
    }
  },
  "resources": [
  ],
  "outputs": {
    "return": {
      "type": "array",
      "value": "[concat(parameters('firstArray'), parameters('secondArray'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param firstArray array = [
  '1-1'
  '1-2'
  '1-3'
]
param secondArray array = [
  '2-1'
  '2-2'
  '2-3'
]

output return array = concat(firstArray, secondArray)
```

---

De uitvoer van het vorige voor beeld met de standaard waarden is:

| Naam | Type | Waarde |
| ---- | ---- | ----- |
| terugkeren | Matrix | ["1-1", "1-2", "1-3", "2-1", "2-2", "2-3"] |

## <a name="contains"></a>bevat

`contains (container, itemToFind)`

Controleert of een matrix een waarde bevat, een object bevat een sleutel of een teken reeks bevat een subtekenreeks. De teken reeks vergelijking is hoofdletter gevoelig. Als er echter wordt getest of een object een sleutel bevat, is de vergelijking niet hoofdletter gevoelig.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Description |
|:--- |:--- |:--- |:--- |
| container |Yes |matrix, object of teken reeks |De waarde die de te zoeken waarde bevat. |
| itemToFind |Yes |teken reeks of int |De waarde die moet worden gevonden. |

### <a name="return-value"></a>Retourwaarde

**Waar** als het item is gevonden; anders **False**.

### <a name="examples"></a>Voorbeelden

In de volgende [voorbeeld sjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/contains.json) ziet u hoe u deze kunt gebruiken met verschillende typen:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "stringToTest": {
      "type": "string",
      "defaultValue": "OneTwoThree"
    },
    "objectToTest": {
      "type": "object",
      "defaultValue": {
        "one": "a",
        "two": "b",
        "three": "c"
      }
    },
    "arrayToTest": {
      "type": "array",
      "defaultValue": [ "one", "two", "three" ]
    }
  },
  "resources": [
  ],
  "outputs": {
    "stringTrue": {
      "type": "bool",
      "value": "[contains(parameters('stringToTest'), 'e')]"
    },
    "stringFalse": {
      "type": "bool",
      "value": "[contains(parameters('stringToTest'), 'z')]"
    },
    "objectTrue": {
      "type": "bool",
      "value": "[contains(parameters('objectToTest'), 'one')]"
    },
    "objectFalse": {
      "type": "bool",
      "value": "[contains(parameters('objectToTest'), 'a')]"
    },
    "arrayTrue": {
      "type": "bool",
      "value": "[contains(parameters('arrayToTest'), 'three')]"
    },
    "arrayFalse": {
      "type": "bool",
      "value": "[contains(parameters('arrayToTest'), 'four')]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param stringToTest string = 'OneTwoThree'
param objectToTest object = {
  'one': 'a'
  'two': 'b'
  'three': 'c'
}
param arrayToTest array = [
  'one'
  'two'
  'three'
]

output stringTrue bool = contains(stringToTest, 'e')
output stringFalse bool = contains(stringToTest, 'z')
output objectTrue bool = contains(objectToTest, 'one')
output objectFalse bool = contains(objectToTest, 'a')
output arrayTrue bool = contains(arrayToTest, 'three')
output arrayFalse bool = contains(arrayToTest, 'four')
```

---

De uitvoer van het vorige voor beeld met de standaard waarden is:

| Naam | Type | Waarde |
| ---- | ---- | ----- |
| stringTrue | Booleaanse waarde | True |
| stringFalse | Booleaanse waarde | Niet waar |
| objectTrue | Booleaanse waarde | True |
| objectFalse | Booleaanse waarde | Niet waar |
| arrayTrue | Booleaanse waarde | True |
| arrayFalse | Booleaanse waarde | Niet waar |

## <a name="datauri"></a>dataUri

`dataUri(stringToConvert)`

Converteert een waarde naar een gegevens-URI.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Description |
|:--- |:--- |:--- |:--- |
| stringToConvert |Yes |tekenreeks |De waarde die moet worden geconverteerd naar een gegevens-URI. |

### <a name="return-value"></a>Retourwaarde

Een teken reeks die is opgemaakt als een gegevens-URI.

### <a name="examples"></a>Voorbeelden

Met de volgende [voorbeeld sjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/datauri.json) wordt een waarde geconverteerd naar een gegevens-URI en wordt een gegevens-URI geconverteerd naar een teken reeks:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "stringToTest": {
      "type": "string",
      "defaultValue": "Hello"
    },
    "dataFormattedString": {
      "type": "string",
      "defaultValue": "data:;base64,SGVsbG8sIFdvcmxkIQ=="
    }
  },
  "resources": [],
  "outputs": {
    "dataUriOutput": {
      "value": "[dataUri(parameters('stringToTest'))]",
      "type": "string"
    },
    "toStringOutput": {
      "type": "string",
      "value": "[dataUriToString(parameters('dataFormattedString'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param stringToTest string = 'Hello'
param dataFormattedString string = 'data:;base64,SGVsbG8sIFdvcmxkIQ=='

output dataUriOutput string = dataUri(stringToTest)
output toStringOutput string = dataUriToString(dataFormattedString)
```

---

De uitvoer van het vorige voor beeld met de standaard waarden is:

| Naam | Type | Waarde |
| ---- | ---- | ----- |
| dataUriOutput | Tekenreeks | gegevens: Text/Plain; charset = utf8; base64, SGVsbG8 = |
| toStringOutput | Tekenreeks | Hallo wereld! |

## <a name="datauritostring"></a>dataUriToString

`dataUriToString(dataUriToConvert)`

Converteert een waarde in de indeling van een gegevens-URI naar een teken reeks.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Description |
|:--- |:--- |:--- |:--- |
| dataUriToConvert |Yes |tekenreeks |De gegevens-URI-waarde die moet worden geconverteerd. |

### <a name="return-value"></a>Retourwaarde

Een teken reeks met de geconverteerde waarde.

### <a name="examples"></a>Voorbeelden

Met de volgende [voorbeeld sjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/datauri.json) wordt een waarde geconverteerd naar een gegevens-URI en wordt een gegevens-URI geconverteerd naar een teken reeks:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "stringToTest": {
      "type": "string",
      "defaultValue": "Hello"
    },
    "dataFormattedString": {
      "type": "string",
      "defaultValue": "data:;base64,SGVsbG8sIFdvcmxkIQ=="
    }
  },
  "resources": [],
  "outputs": {
    "dataUriOutput": {
      "value": "[dataUri(parameters('stringToTest'))]",
      "type": "string"
    },
    "toStringOutput": {
      "type": "string",
      "value": "[dataUriToString(parameters('dataFormattedString'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param stringToTest string = 'Hello'
param dataFormattedString string = 'data:;base64,SGVsbG8sIFdvcmxkIQ=='

output dataUriOutput string = dataUri(stringToTest)
output toStringOutput string = dataUriToString(dataFormattedString)
```

---

De uitvoer van het vorige voor beeld met de standaard waarden is:

| Naam | Type | Waarde |
| ---- | ---- | ----- |
| dataUriOutput | Tekenreeks | gegevens: Text/Plain; charset = utf8; base64, SGVsbG8 = |
| toStringOutput | Tekenreeks | Hallo wereld! |

## <a name="empty"></a>leeg

`empty(itemToTest)`

Bepaalt of een matrix, een object of een teken reeks leeg is.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Description |
|:--- |:--- |:--- |:--- |
| itemToTest |Yes |matrix, object of teken reeks |De waarde die moet worden gecontroleerd of deze leeg is. |

### <a name="return-value"></a>Retourwaarde

Retourneert **waar** als de waarde leeg is; anders **False**.

### <a name="examples"></a>Voorbeelden

Met de volgende [voorbeeld sjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/empty.json) wordt gecontroleerd of een matrix, een object en een teken reeks leeg zijn.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "testArray": {
      "type": "array",
      "defaultValue": []
    },
    "testObject": {
      "type": "object",
      "defaultValue": {}
    },
    "testString": {
      "type": "string",
      "defaultValue": ""
    }
  },
  "resources": [
  ],
  "outputs": {
    "arrayEmpty": {
      "type": "bool",
      "value": "[empty(parameters('testArray'))]"
    },
    "objectEmpty": {
      "type": "bool",
      "value": "[empty(parameters('testObject'))]"
    },
    "stringEmpty": {
      "type": "bool",
      "value": "[empty(parameters('testString'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param testArray array = []
param testObject object = {}
param testString string = ''

output arrayEmpty bool = empty(testArray)
output objectEmpty bool = empty(testObject)
output stringEmpty bool = empty(testString)
```

---

De uitvoer van het vorige voor beeld met de standaard waarden is:

| Naam | Type | Waarde |
| ---- | ---- | ----- |
| arrayEmpty | Booleaanse waarde | True |
| objectEmpty | Booleaanse waarde | True |
| stringEmpty | Booleaanse waarde | True |

## <a name="endswith"></a>endsWith

`endsWith(stringToSearch, stringToFind)`

Hiermee wordt bepaald of een teken reeks eindigt met een waarde. De vergelijking is hoofdletter gevoelig.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Description |
|:--- |:--- |:--- |:--- |
| stringToSearch |Yes |tekenreeks |De waarde die het item bevat dat moet worden gevonden. |
| stringToFind |Yes |tekenreeks |De waarde die moet worden gevonden. |

### <a name="return-value"></a>Retourwaarde

**Waar** als het laatste teken of de tekens van de teken reeks overeenkomen met de waarde; anders **False**.

### <a name="examples"></a>Voorbeelden

In de volgende [voorbeeld sjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/startsendswith.json) ziet u hoe u de functies StartsWith en endsWith gebruikt:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "startsTrue": {
      "value": "[startsWith('abcdef', 'ab')]",
      "type": "bool"
    },
    "startsCapTrue": {
      "value": "[startsWith('abcdef', 'A')]",
      "type": "bool"
    },
    "startsFalse": {
      "value": "[startsWith('abcdef', 'e')]",
      "type": "bool"
    },
    "endsTrue": {
      "value": "[endsWith('abcdef', 'ef')]",
      "type": "bool"
    },
    "endsCapTrue": {
      "value": "[endsWith('abcdef', 'F')]",
      "type": "bool"
    },
    "endsFalse": {
      "value": "[endsWith('abcdef', 'e')]",
      "type": "bool"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output startsTrue bool = startsWith('abcdef', 'ab')
output startsCapTrue bool = startsWith('abcdef', 'A')
output startsFalse bool = startsWith('abcdef', 'e')
output endsTrue bool = endsWith('abcdef', 'ef')
output endsCapTrue bool = endsWith('abcdef', 'F')
output endsFalse bool = endsWith('abcdef', 'e')
```

---

De uitvoer van het vorige voor beeld met de standaard waarden is:

| Naam | Type | Waarde |
| ---- | ---- | ----- |
| startsTrue | Booleaanse waarde | True |
| startsCapTrue | Booleaanse waarde | True |
| startsFalse | Booleaanse waarde | Niet waar |
| endsTrue | Booleaanse waarde | True |
| endsCapTrue | Booleaanse waarde | True |
| endsFalse | Booleaanse waarde | Niet waar |

## <a name="first"></a>instantie

`first(arg1)`

Retourneert het eerste teken van de teken reeks of het eerste element van de matrix.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Description |
|:--- |:--- |:--- |:--- |
| Arg1 |Yes |matrix of teken reeks |De waarde voor het ophalen van het eerste element of teken. |

### <a name="return-value"></a>Retourwaarde

Een teken reeks van het eerste teken of het type (teken reeks, int, matrix of object) van het eerste element in een matrix.

### <a name="examples"></a>Voorbeelden

In de volgende [voorbeeld sjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/first.json) ziet u hoe u de eerste functie gebruikt met een matrix en teken reeks.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "arrayToTest": {
      "type": "array",
      "defaultValue": [ "one", "two", "three" ]
    }
  },
  "resources": [
  ],
  "outputs": {
    "arrayOutput": {
      "type": "string",
      "value": "[first(parameters('arrayToTest'))]"
    },
    "stringOutput": {
      "type": "string",
      "value": "[first('One Two Three')]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param arrayToTest array = [
  'one'
  'two'
  'three'
]

output arrayOutput string = first(arrayToTest)
output stringOutput string = first('One Two Three')
```

---

De uitvoer van het vorige voor beeld met de standaard waarden is:

| Naam | Type | Waarde |
| ---- | ---- | ----- |
| arrayOutput | Tekenreeks | een |
| stringOutput | Tekenreeks | O |

## <a name="format"></a>indeling

`format(formatString, arg1, arg2, ...)`

Hiermee maakt u een ingedeelde teken reeks van invoer waarden.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Description |
|:--- |:--- |:--- |:--- |
| formatString | Yes | tekenreeks | De samengestelde indelings teken reeks. |
| Arg1 | Yes | teken reeks, geheel getal of Booleaanse waarde | De waarde die moet worden meegenomen in de opgemaakte teken reeks. |
| aanvullende argumenten | No | teken reeks, geheel getal of Booleaanse waarde | Aanvullende waarden die moeten worden meegenomen in de opgemaakte teken reeks. |

### <a name="remarks"></a>Opmerkingen

Gebruik deze functie om een teken reeks in uw sjabloon op te maken. Er worden dezelfde opmaak opties gebruikt als voor de methode [System. String. Format](/dotnet/api/system.string.format) in .net.

### <a name="examples"></a>Voorbeelden

In de volgende voorbeeld sjabloon ziet u hoe u de functie Format gebruikt.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "greeting": {
      "type": "string",
      "defaultValue": "Hello"
    },
    "name": {
      "type": "string",
      "defaultValue": "User"
    },
    "numberToFormat": {
      "type": "int",
      "defaultValue": 8175133
    }
  },
  "resources": [
  ],
  "outputs": {
    "formatTest": {
      "type": "string",
      "value": "[format('{0}, {1}. Formatted number: {2:N0}', parameters('greeting'), parameters('name'), parameters('numberToFormat'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param greeting string = 'Hello'
param name string = 'User'
param numberToFormat int = 8175133

output formatTest string = format('{0}, {1}. Formatted number: {2:N0}', greeting, name, numberToFormat)
```

---

De uitvoer van het vorige voor beeld met de standaard waarden is:

| Naam | Type | Waarde |
| ---- | ---- | ----- |
| Indelings meest | Tekenreeks | Hallo, gebruiker. Opgemaakt aantal: 8.175.133 |

## <a name="guid"></a>guid

`guid(baseString, ...)`

Hiermee maakt u een waarde in de notatie van een Globally Unique Identifier op basis van de waarden die zijn opgegeven als para meters.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Description |
|:--- |:--- |:--- |:--- |
| baseString |Yes |tekenreeks |De waarde die wordt gebruikt in de hash-functie om de GUID te maken. |
| aanvullende para meters indien nodig |No |tekenreeks |U kunt zoveel teken reeksen toevoegen als u nodig hebt om de waarde te maken waarmee het niveau van uniekheid wordt opgegeven. |

### <a name="remarks"></a>Opmerkingen

Deze functie is handig wanneer u een waarde in de indeling van een Globally Unique Identifier wilt maken. U geeft parameter waarden op die het bereik van de uniekheid voor het resultaat beperken. U kunt opgeven of de naam uniek is voor het abonnement, de resource groep of de implementatie.

De geretourneerde waarde is geen wille keurige teken reeks, maar in plaats daarvan het resultaat van een hash-functie voor de para meters. De geretourneerde waarde is 36 tekens lang. Het is niet wereld wijd uniek. Als u een nieuwe GUID wilt maken die niet is gebaseerd op die hashwaarde van de para meters, gebruikt u de functie [newGuid](#newguid) .

In de volgende voor beelden ziet u hoe u met behulp van GUID een unieke waarde voor veelgebruikte niveaus maakt.

Uniek bereik voor abonnement

# <a name="json"></a>[JSON](#tab/json)

```json
"[guid(subscription().subscriptionId)]"
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
guid(subscription().subscriptionId)
```

---

Uniek bereik van resource groep

# <a name="json"></a>[JSON](#tab/json)

```json
"[guid(resourceGroup().id)]"
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
guid(resourceGroup().id)
```

---

Uniek bereik voor implementatie voor een resource groep

# <a name="json"></a>[JSON](#tab/json)

```json
"[guid(resourceGroup().id, deployment().name)]"
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
guid(resourceGroup().id, deployment().name)
```

---

### <a name="return-value"></a>Retourwaarde

Een teken reeks met 36 tekens in de indeling van een Globally Unique Identifier.

### <a name="examples"></a>Voorbeelden

De volgende [voorbeeld sjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/guid.json) retourneert resultaten van GUID:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {},
  "resources": [],
  "outputs": {
    "guidPerSubscription": {
      "value": "[guid(subscription().subscriptionId)]",
      "type": "string"
    },
    "guidPerResourceGroup": {
      "value": "[guid(resourceGroup().id)]",
      "type": "string"
    },
    "guidPerDeployment": {
      "value": "[guid(resourceGroup().id, deployment().name)]",
      "type": "string"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output guidPerSubscription string = guid(subscription().subscriptionId)
output guidPerResourceGroup string = guid(resourceGroup().id)
output guidPerDeployment string = guid(resourceGroup().id, deployment().name)
```

---

## <a name="indexof"></a>indexOf

`indexOf(stringToSearch, stringToFind)`

Retourneert de eerste positie van een waarde binnen een teken reeks. De vergelijking is hoofdletter gevoelig.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Description |
|:--- |:--- |:--- |:--- |
| stringToSearch |Yes |tekenreeks |De waarde die het item bevat dat moet worden gevonden. |
| stringToFind |Yes |tekenreeks |De waarde die moet worden gevonden. |

### <a name="return-value"></a>Retourwaarde

Een geheel getal dat de positie van het item vertegenwoordigt dat moet worden gezocht. De waarde is gebaseerd op nul. Als het item niet wordt gevonden, wordt-1 geretourneerd.

### <a name="examples"></a>Voorbeelden

In de volgende [voorbeeld sjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/indexof.json) ziet u hoe u de functies IndexOf en lastIndexOf gebruikt:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "firstT": {
      "value": "[indexOf('test', 't')]",
      "type": "int"
    },
    "lastT": {
      "value": "[lastIndexOf('test', 't')]",
      "type": "int"
    },
    "firstString": {
      "value": "[indexOf('abcdef', 'CD')]",
      "type": "int"
    },
    "lastString": {
      "value": "[lastIndexOf('abcdef', 'AB')]",
      "type": "int"
    },
    "notFound": {
      "value": "[indexOf('abcdef', 'z')]",
      "type": "int"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output firstT int = indexOf('test', 't')
output lastT int = lastIndexOf('test', 't')
output firstString int = indexOf('abcdef', 'CD')
output lastString int = lastIndexOf('abcdef', 'AB')
output notFound int = indexOf('abcdef', 'z')
```

---

De uitvoer van het vorige voor beeld met de standaard waarden is:

| Naam | Type | Waarde |
| ---- | ---- | ----- |
| eerst | Int | 0 |
| laatst | Int | 3 |
| firstString | Int | 2 |
| lastString | Int | 0 |
| notFound | Int | -1 |

<a id="json"></a>

## <a name="json"></a>json

`json(arg1)`

Hiermee wordt een geldige JSON-teken reeks geconverteerd naar een JSON-gegevens type. Zie [JSON function](template-functions-object.md#json)(Engelstalig) voor meer informatie.

## <a name="last"></a>duren

`last (arg1)`

Retourneert het laatste teken van de teken reeks of het laatste element van de matrix.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Description |
|:--- |:--- |:--- |:--- |
| Arg1 |Yes |matrix of teken reeks |De waarde voor het ophalen van het laatste element of teken. |

### <a name="return-value"></a>Retourwaarde

Een teken reeks van het laatste teken of het type (teken reeks, int, matrix of object) van het laatste element in een matrix.

### <a name="examples"></a>Voorbeelden

In de volgende [voorbeeld sjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/last.json) ziet u hoe u de laatste functie gebruikt met een matrix en teken reeks.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "arrayToTest": {
      "type": "array",
      "defaultValue": [ "one", "two", "three" ]
    }
  },
  "resources": [
  ],
  "outputs": {
    "arrayOutput": {
      "type": "string",
      "value": "[last(parameters('arrayToTest'))]"
    },
    "stringOutput": {
      "type": "string",
      "value": "[last('One Two Three')]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param arrayToTest array = [
  'one'
  'two'
  'three'
]

output arrayOutput string = last(arrayToTest)
output stringOutput string = last('One Two Three')
```

---

De uitvoer van het vorige voor beeld met de standaard waarden is:

| Naam | Type | Waarde |
| ---- | ---- | ----- |
| arrayOutput | Tekenreeks | drie |
| stringOutput | Tekenreeks | e |

## <a name="lastindexof"></a>lastIndexOf

`lastIndexOf(stringToSearch, stringToFind)`

Retourneert de laatste positie van een waarde binnen een teken reeks. De vergelijking is hoofdletter gevoelig.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Description |
|:--- |:--- |:--- |:--- |
| stringToSearch |Yes |tekenreeks |De waarde die het item bevat dat moet worden gevonden. |
| stringToFind |Yes |tekenreeks |De waarde die moet worden gevonden. |

### <a name="return-value"></a>Retourwaarde

Een geheel getal dat de laatste positie van het item vertegenwoordigt dat moet worden gezocht. De waarde is gebaseerd op nul. Als het item niet wordt gevonden, wordt-1 geretourneerd.

### <a name="examples"></a>Voorbeelden

In de volgende [voorbeeld sjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/indexof.json) ziet u hoe u de functies IndexOf en lastIndexOf gebruikt:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "firstT": {
      "value": "[indexOf('test', 't')]",
      "type": "int"
    },
    "lastT": {
      "value": "[lastIndexOf('test', 't')]",
      "type": "int"
    },
    "firstString": {
      "value": "[indexOf('abcdef', 'CD')]",
      "type": "int"
    },
    "lastString": {
      "value": "[lastIndexOf('abcdef', 'AB')]",
      "type": "int"
    },
    "notFound": {
      "value": "[indexOf('abcdef', 'z')]",
      "type": "int"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output firstT int = indexOf('test', 't')
output lastT int = lastIndexOf('test', 't')
output firstString int = indexOf('abcdef', 'CD')
output lastString int = lastIndexOf('abcdef', 'AB')
output notFound int = indexOf('abcdef', 'z')
```

---

De uitvoer van het vorige voor beeld met de standaard waarden is:

| Naam | Type | Waarde |
| ---- | ---- | ----- |
| eerst | Int | 0 |
| laatst | Int | 3 |
| firstString | Int | 2 |
| lastString | Int | 0 |
| notFound | Int | -1 |

## <a name="length"></a>lengte

`length(string)`

Retourneert het aantal tekens in een teken reeks, elementen in een matrix of hoofd niveau-eigenschappen in een-object.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Description |
|:--- |:--- |:--- |:--- |
| Arg1 |Yes |matrix, teken reeks of object |De matrix die moet worden gebruikt voor het ophalen van het aantal elementen, de teken reeks die moet worden gebruikt voor het ophalen van het aantal tekens of het object dat moet worden gebruikt voor het ophalen van het aantal eigenschappen op hoofd niveau. |

### <a name="return-value"></a>Retourwaarde

Een int.

### <a name="examples"></a>Voorbeelden

In de volgende [voorbeeld sjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/length.json) ziet u hoe lengte met een matrix en teken reeks kan worden gebruikt:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "arrayToTest": {
      "type": "array",
      "defaultValue": [
        "one",
        "two",
        "three"
      ]
    },
    "stringToTest": {
      "type": "string",
      "defaultValue": "One Two Three"
    },
    "objectToTest": {
      "type": "object",
      "defaultValue": {
        "propA": "one",
        "propB": "two",
        "propC": "three",
        "propD": {
          "propD-1": "sub",
          "propD-2": "sub"
        }
      }
    }
  },
  "resources": [],
  "outputs": {
    "arrayLength": {
      "type": "int",
      "value": "[length(parameters('arrayToTest'))]"
    },
    "stringLength": {
      "type": "int",
      "value": "[length(parameters('stringToTest'))]"
    },
    "objectLength": {
      "type": "int",
      "value": "[length(parameters('objectToTest'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param arrayToTest array = [
  'one'
  'two'
  'three'
]
param stringToTest string = 'One Two Three'
param objectToTest object = {
  'propA': 'one'
  'propB': 'two'
  'propC': 'three'
  'propD': {
    'propD-1': 'sub'
    'propD-2': 'sub'
  }
}

output arrayLength int = length(arrayToTest)
output stringLength int = length(stringToTest)
output objectLength int = length(objectToTest)
```

---

De uitvoer van het vorige voor beeld met de standaard waarden is:

| Naam | Type | Waarde |
| ---- | ---- | ----- |
| arrayLength | Int | 3 |
| stringLength | Int | 13 |
| objectLength | Int | 4 |

## <a name="newguid"></a>newGuid

`newGuid()`

Retourneert een waarde in de indeling van een Globally Unique Identifier. **Deze functie kan alleen worden gebruikt in de standaard waarde voor een para meter.**

### <a name="remarks"></a>Opmerkingen

U kunt deze functie alleen gebruiken in een expressie voor de standaard waarde van een para meter. Als u deze functie ergens anders in een sjabloon gebruikt, wordt er een fout geretourneerd. De functie is niet toegestaan in andere onderdelen van de sjabloon omdat deze een andere waarde retourneert telkens wanneer deze wordt aangeroepen. Het implementeren van dezelfde sjabloon met dezelfde para meters zou geen betrouw bare resultaten opleveren.

De functie newGuid verschilt van de functie [GUID](#guid) omdat deze geen para meters heeft. Wanneer u een GUID met dezelfde para meter aanroept, wordt elke keer dezelfde id geretourneerd. Gebruik GUID wanneer u op een betrouw bare manier dezelfde GUID wilt genereren voor een specifieke omgeving. Gebruik newGuid wanneer u elke keer een andere id nodig hebt, zoals het implementeren van resources in een test omgeving.

De functie newGuid maakt gebruik van de [GUID-structuur](/dotnet/api/system.guid) in de .NET Framework om de Globally Unique Identifier te genereren.

Als u de optie gebruikt voor het opnieuw [implementeren van een eerdere geslaagde implementatie](rollback-on-error.md), en de eerdere implementatie bevat een para meter die gebruikmaakt van newGuid, wordt de para meter niet opnieuw geëvalueerd. In plaats daarvan wordt de parameter waarde van de eerdere implementatie automatisch opnieuw gebruikt in de rollback-implementatie.

In een test omgeving moet u mogelijk herhaaldelijk resources implementeren die alleen gedurende een korte periode actief zijn. In plaats van unieke namen te maken, kunt u newGuid gebruiken met [Unique string](#uniquestring) om unieke namen te creëren.

Wees voorzichtig met het opnieuw implementeren van een sjabloon die afhankelijk is van de functie newGuid voor een standaard waarde. Wanneer u opnieuw implementeert en geen waarde opgeeft voor de para meter, wordt de functie opnieuw geëvalueerd. Als u een bestaande resource wilt bijwerken in plaats van een nieuwe te maken, geeft u de parameter waarde van de eerdere implementatie door.

### <a name="return-value"></a>Retourwaarde

Een teken reeks met 36 tekens in de indeling van een Globally Unique Identifier.

### <a name="examples"></a>Voorbeelden

In de volgende voorbeeld sjabloon wordt een para meter met een nieuwe id weer gegeven.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "guidValue": {
      "type": "string",
      "defaultValue": "[newGuid()]"
    }
  },
  "resources": [
  ],
  "outputs": {
    "guidOutput": {
      "type": "string",
      "value": "[parameters('guidValue')]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param guidValue string = newGuid()

output guidOutput string = guidValue
```

---

De uitvoer van het voor gaande voor beeld varieert per implementatie, maar lijkt op:

| Naam | Type | Waarde |
| ---- | ---- | ----- |
| guidOutput | tekenreeks | b76a51fc-bd72-4a77-b9a2-3c29e7d2e551 |

In het volgende voor beeld wordt de functie newGuid gebruikt om een unieke naam voor een opslag account te maken. Deze sjabloon kan worden gebruikt voor test omgevingen waarin het opslag account bestaat voor een korte periode en niet opnieuw wordt geïmplementeerd.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "guidValue": {
      "type": "string",
      "defaultValue": "[newGuid()]"
    }
  },
  "variables": {
    "storageName": "[concat('storage', uniqueString(parameters('guidValue')))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageName')]",
      "location": "West US",
      "apiVersion": "2018-07-01",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "StorageV2",
      "properties": {}
    }
  ],
  "outputs": {
    "nameOutput": {
      "type": "string",
      "value": "[variables('storageName')]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param guidValue string = newGuid()

var storageName = 'storage${uniqueString(guidValue)}'

resource myStorage 'Microsoft.Storage/storageAccounts@2018-07-01' = {
  name: storageName
  location: 'West US'
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'StorageV2'
  properties: {}
}

output nameOutput string = storageName
```

---

De uitvoer van het voor gaande voor beeld varieert per implementatie, maar lijkt op:

| Naam | Type | Waarde |
| ---- | ---- | ----- |
| nameOutput | tekenreeks | storagenziwvyru7uxie |

## <a name="padleft"></a>padLeft

`padLeft(valueToPad, totalLength, paddingCharacter)`

Retourneert een rechts uitgelijnde teken reeks door tekens aan de linkerkant toe te voegen totdat de totale opgegeven lengte wordt bereikt.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Description |
|:--- |:--- |:--- |:--- |
| valueToPad |Yes |teken reeks of int |De waarde die rechts moet worden uitgelijnd. |
| totalLength |Ja |int |Het totale aantal tekens in de geretourneerde teken reeks. |
| paddingCharacter |No |Eén teken |Het teken dat moet worden gebruikt voor de linker opvulling totdat de totale lengte wordt bereikt. De standaard waarde is een spatie. |

Als de oorspronkelijke teken reeks langer is dan het aantal tekens dat u wilt aanvullen, worden er geen tekens toegevoegd.

### <a name="return-value"></a>Retourwaarde

Een teken reeks met ten minste het aantal opgegeven tekens.

### <a name="examples"></a>Voorbeelden

In de volgende [voorbeeld sjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/padleft.json) ziet u hoe u de door de gebruiker gedefinieerde parameter waarde invult door het teken nul toe te voegen tot het totaal aantal tekens wordt bereikt.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "testString": {
      "type": "string",
      "defaultValue": "123"
    }
  },
  "resources": [],
  "outputs": {
    "stringOutput": {
      "type": "string",
      "value": "[padLeft(parameters('testString'),10,'0')]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param testString string = '123'

output stringOutput string = padLeft(testString, 10, '0')
```

---

De uitvoer van het vorige voor beeld met de standaard waarden is:

| Naam | Type | Waarde |
| ---- | ---- | ----- |
| stringOutput | Tekenreeks | 0000000123 |

## <a name="replace"></a>vervangen

`replace(originalString, oldString, newString)`

Retourneert een nieuwe teken reeks waarbij alle exemplaren van een teken reeks zijn vervangen door een andere teken reeks.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Description |
|:--- |:--- |:--- |:--- |
| originalString |Yes |tekenreeks |De waarde met alle exemplaren van één teken reeks die is vervangen door een andere teken reeks. |
| Olds Tring |Yes |tekenreeks |De teken reeks die moet worden verwijderd uit de oorspronkelijke teken reeks. |
| newString |Yes |tekenreeks |De teken reeks die moet worden toegevoegd in plaats van de verwijderde teken reeks. |

### <a name="return-value"></a>Retourwaarde

Een teken reeks met de vervangen tekens.

### <a name="examples"></a>Voorbeelden

In de volgende [voorbeeld sjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/replace.json) ziet u hoe u alle streepjes uit de door de gebruiker gegeven teken reeks verwijdert en hoe u een deel van de teken reeks vervangt door een andere teken reeks.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "testString": {
      "type": "string",
      "defaultValue": "123-123-1234"
    }
  },
  "resources": [],
  "outputs": {
    "firstOutput": {
      "type": "string",
      "value": "[replace(parameters('testString'),'-', '')]"
    },
    "secondOutput": {
      "type": "string",
      "value": "[replace(parameters('testString'),'1234', 'xxxx')]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param testString string = '123-123-1234'

output firstOutput string = replace(testString, '-', '')
output secondOutput string = replace(testString, '1234', 'xxxx')
```

---

De uitvoer van het vorige voor beeld met de standaard waarden is:

| Naam | Type | Waarde |
| ---- | ---- | ----- |
| firstOutput | Tekenreeks | 1231231234 |
| secondOutput | Tekenreeks | 123-123-xxxx |

## <a name="skip"></a>skip

`skip(originalValue, numberToSkip)`

Retourneert een teken reeks met alle tekens na het opgegeven aantal tekens of een matrix met alle elementen na het opgegeven aantal elementen.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Description |
|:--- |:--- |:--- |:--- |
| originalValue |Yes |matrix of teken reeks |De matrix of teken reeks die moet worden gebruikt voor het overs Laan. |
| numberToSkip |Ja |int |Het aantal elementen of tekens dat moet worden overgeslagen. Als deze waarde 0 of kleiner is, worden alle elementen of tekens in de waarde geretourneerd. Als het groter is dan de lengte van de matrix of teken reeks, wordt een lege matrix of teken reeks geretourneerd. |

### <a name="return-value"></a>Retourwaarde

Een matrix of teken reeks.

### <a name="examples"></a>Voorbeelden

De volgende [voorbeeld sjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/skip.json) slaat het opgegeven aantal elementen in de matrix over en het opgegeven aantal tekens in een teken reeks.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "testArray": {
      "type": "array",
      "defaultValue": [
        "one",
        "two",
        "three"
      ]
    },
    "elementsToSkip": {
      "type": "int",
      "defaultValue": 2
    },
    "testString": {
      "type": "string",
      "defaultValue": "one two three"
    },
    "charactersToSkip": {
      "type": "int",
      "defaultValue": 4
    }
  },
  "resources": [],
  "outputs": {
    "arrayOutput": {
      "type": "array",
      "value": "[skip(parameters('testArray'),parameters('elementsToSkip'))]"
    },
    "stringOutput": {
      "type": "string",
      "value": "[skip(parameters('testString'),parameters('charactersToSkip'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param testArray array = [
  'one'
  'two'
  'three'
]
param elementsToSkip int = 2
param testString string = 'one two three'
param charactersToSkip int = 4

output arrayOutput array = skip(testArray, elementsToSkip)
output stringOutput string = skip(testString, charactersToSkip)
```

---

De uitvoer van het vorige voor beeld met de standaard waarden is:

| Naam | Type | Waarde |
| ---- | ---- | ----- |
| arrayOutput | Matrix | ["drie"] |
| stringOutput | Tekenreeks | 2 3 |

## <a name="split"></a>split

`split(inputString, delimiter)`

Retourneert een matrix met teken reeksen die de subtekenreeksen bevat van de invoer teken reeks die wordt gescheiden door de opgegeven scheidings tekens.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Description |
|:--- |:--- |:--- |:--- |
| inputString |Yes |tekenreeks |De teken reeks die moet worden gesplitst. |
| scheidingsteken |Yes |teken reeks of matrix met teken reeksen |Het scheidings teken dat moet worden gebruikt voor het splitsen van de teken reeks. |

### <a name="return-value"></a>Retourwaarde

Een matrix met teken reeksen.

### <a name="examples"></a>Voorbeelden

De volgende [voorbeeld sjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/split.json) splitst de invoer teken reeks met een komma en met een komma of een punt komma.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "firstString": {
      "type": "string",
      "defaultValue": "one,two,three"
    },
    "secondString": {
      "type": "string",
      "defaultValue": "one;two,three"
    }
  },
  "variables": {
    "delimiters": [ ",", ";" ]
  },
  "resources": [],
  "outputs": {
    "firstOutput": {
      "type": "array",
      "value": "[split(parameters('firstString'),',')]"
    },
    "secondOutput": {
      "type": "array",
      "value": "[split(parameters('secondString'),variables('delimiters'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param firstString string = 'one,two,three'
param secondString string = 'one;two,three'

var delimiters = [
  ','
  ';'
]

output firstOutput array = split(firstString, ',')
output secondOutput array = split(secondString, delimiters)
```

---

De uitvoer van het vorige voor beeld met de standaard waarden is:

| Naam | Type | Waarde |
| ---- | ---- | ----- |
| firstOutput | Matrix | [' één ', ' twee ', ' drie '] |
| secondOutput | Matrix | [' één ', ' twee ', ' drie '] |

## <a name="startswith"></a>startsWith

`startsWith(stringToSearch, stringToFind)`

Hiermee wordt bepaald of een teken reeks begint met een waarde. De vergelijking is hoofdletter gevoelig.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Description |
|:--- |:--- |:--- |:--- |
| stringToSearch |Yes |tekenreeks |De waarde die het item bevat dat moet worden gevonden. |
| stringToFind |Yes |tekenreeks |De waarde die moet worden gevonden. |

### <a name="return-value"></a>Retourwaarde

**Waar** als het eerste teken of de tekens van de teken reeks overeenkomen met de waarde; anders **False**.

### <a name="examples"></a>Voorbeelden

In de volgende [voorbeeld sjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/startsendswith.json) ziet u hoe u de functies StartsWith en endsWith gebruikt:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "startsTrue": {
      "value": "[startsWith('abcdef', 'ab')]",
      "type": "bool"
    },
    "startsCapTrue": {
      "value": "[startsWith('abcdef', 'A')]",
      "type": "bool"
    },
    "startsFalse": {
      "value": "[startsWith('abcdef', 'e')]",
      "type": "bool"
    },
    "endsTrue": {
      "value": "[endsWith('abcdef', 'ef')]",
      "type": "bool"
    },
    "endsCapTrue": {
      "value": "[endsWith('abcdef', 'F')]",
      "type": "bool"
    },
    "endsFalse": {
      "value": "[endsWith('abcdef', 'e')]",
      "type": "bool"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output startsTrue bool = startsWith('abcdef', 'ab')
output startsCapTrue bool = startsWith('abcdef', 'A')
output startsFalse bool = startsWith('abcdef', 'e')
output endsTrue bool = endsWith('abcdef', 'ef')
output endsCapTrue bool = endsWith('abcdef', 'F')
output endsFalse bool = endsWith('abcdef', 'e')
```

---

De uitvoer van het vorige voor beeld met de standaard waarden is:

| Naam | Type | Waarde |
| ---- | ---- | ----- |
| startsTrue | Booleaanse waarde | True |
| startsCapTrue | Booleaanse waarde | True |
| startsFalse | Booleaanse waarde | Niet waar |
| endsTrue | Booleaanse waarde | True |
| endsCapTrue | Booleaanse waarde | True |
| endsFalse | Booleaanse waarde | False |

## <a name="string"></a>tekenreeks

`string(valueToConvert)`

Hiermee wordt de opgegeven waarde geconverteerd naar een teken reeks.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Description |
|:--- |:--- |:--- |:--- |
| valueToConvert |Yes | Alle |De waarde die moet worden geconverteerd naar een teken reeks. Elk type waarde kan worden geconverteerd, inclusief objecten en matrices. |

### <a name="return-value"></a>Retourwaarde

Een teken reeks van de geconverteerde waarde.

### <a name="examples"></a>Voorbeelden

In de volgende [voorbeeld sjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/string.json) ziet u hoe u verschillende typen waarden converteert naar teken reeksen:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "testObject": {
      "type": "object",
      "defaultValue": {
        "valueA": 10,
        "valueB": "Example Text"
      }
    },
    "testArray": {
      "type": "array",
      "defaultValue": [
        "a",
        "b",
        "c"
      ]
    },
    "testInt": {
      "type": "int",
      "defaultValue": 5
    }
  },
  "resources": [],
  "outputs": {
    "objectOutput": {
      "type": "string",
      "value": "[string(parameters('testObject'))]"
    },
    "arrayOutput": {
      "type": "string",
      "value": "[string(parameters('testArray'))]"
    },
    "intOutput": {
      "type": "string",
      "value": "[string(parameters('testInt'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param testObject object = {
  'valueA': 10
  'valueB': 'Example Text'
}
param testArray array = [
  'a'
  'b'
  'c'
]
param testInt int = 5

output objectOutput string = string(testObject)
output arrayOutput string = string(testArray)
output intOutput string = string(testInt)
```

---

De uitvoer van het vorige voor beeld met de standaard waarden is:

| Naam | Type | Waarde |
| ---- | ---- | ----- |
| objectOutput | Tekenreeks | {"waardea": 10, "valueB": "voorbeeld tekst"} |
| arrayOutput | Tekenreeks | ["a", "b", "c"] |
| intOutput | Tekenreeks | 5 |

## <a name="substring"></a>subtekenreeks

`substring(stringToParse, startIndex, length)`

Retourneert een subtekenreeks die begint bij de opgegeven teken positie en het opgegeven aantal tekens bevat.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Description |
|:--- |:--- |:--- |:--- |
| stringToParse |Yes |tekenreeks |De oorspronkelijke teken reeks waaruit de subtekenreeks wordt opgehaald. |
| Start index |No |int |De op nul gebaseerde positie van het begin teken voor de subtekenreeks. |
| lengte |No |int |Het aantal tekens voor de subtekenreeks. Moet verwijzen naar een locatie binnen de teken reeks. Moet nul of groter zijn. |

### <a name="return-value"></a>Retourwaarde

De subtekenreeks. Of een lege teken reeks als de lengte nul is.

### <a name="remarks"></a>Opmerkingen

De functie mislukt als de subtekenreeks voorbij het einde van de teken reeks valt of wanneer de lengte kleiner is dan nul. Het volgende voor beeld mislukt met de fout ' de para meters index en lengte moeten naar een locatie binnen de teken reeks verwijzen. De index parameter: 0, de lengte parameter: ' 11 ', de lengte van de teken reeks parameter: ' 10 '. '.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "inputString": {
    "type": "string",
    "value": "1234567890"
  }
}, "variables": {
  "prefix": "[substring(parameters('inputString'), 0, 11)]"
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param inputString string = '1234567890'

var prefix = substring(inputString, 0, 11)
```

---

### <a name="examples"></a>Voorbeelden

De volgende [voorbeeld sjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/substring.json) extraheert een subtekenreeks uit een para meter.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "testString": {
      "type": "string",
      "defaultValue": "one two three"
    }
  },
  "resources": [],
  "outputs": {
    "substringOutput": {
      "value": "[substring(parameters('testString'), 4, 3)]",
      "type": "string"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param testString string = 'one two three'
output substringOutput string = substring(testString, 4, 3)
```

---

De uitvoer van het vorige voor beeld met de standaard waarden is:

| Naam | Type | Waarde |
| ---- | ---- | ----- |
| substringOutput | Tekenreeks | twee |

## <a name="take"></a>take

`take(originalValue, numberToTake)`

Retourneert een teken reeks met het opgegeven aantal tekens vanaf het begin van de teken reeks of een matrix met het opgegeven aantal elementen vanaf het begin van de matrix.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Description |
|:--- |:--- |:--- |:--- |
| originalValue |Yes |matrix of teken reeks |De matrix of teken reeks waaruit de elementen moeten worden afgeleid. |
| numberToTake |Ja |int |Het aantal elementen of tekens dat moet worden uitgevoerd. Als deze waarde 0 of kleiner is, wordt een lege matrix of teken reeks geretourneerd. Als het groter is dan de lengte van de opgegeven matrix of teken reeks, worden alle elementen in de matrix of teken reeks geretourneerd. |

### <a name="return-value"></a>Retourwaarde

Een matrix of teken reeks.

### <a name="examples"></a>Voorbeelden

De volgende [voorbeeld sjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/take.json) haalt het opgegeven aantal elementen uit de matrix en tekens uit een teken reeks.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "testArray": {
      "type": "array",
      "defaultValue": [
        "one",
        "two",
        "three"
      ]
    },
    "elementsToTake": {
      "type": "int",
      "defaultValue": 2
    },
    "testString": {
      "type": "string",
      "defaultValue": "one two three"
    },
    "charactersToTake": {
      "type": "int",
      "defaultValue": 2
    }
  },
  "resources": [],
  "outputs": {
    "arrayOutput": {
      "type": "array",
      "value": "[take(parameters('testArray'),parameters('elementsToTake'))]"
    },
    "stringOutput": {
      "type": "string",
      "value": "[take(parameters('testString'),parameters('charactersToTake'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param testArray array = [
  'one'
  'two'
  'three'
]
param elementsToTake int = 2
param testString string = 'one two three'
param charactersToTake int = 2

output arrayOutput array = take(testArray, elementsToTake)
output stringOutput string = take(testString, charactersToTake)
```

---

De uitvoer van het vorige voor beeld met de standaard waarden is:

| Naam | Type | Waarde |
| ---- | ---- | ----- |
| arrayOutput | Matrix | [' één ', ' twee '] |
| stringOutput | Tekenreeks | op |

## <a name="tolower"></a>toLower

`toLower(stringToChange)`

Hiermee wordt de opgegeven teken reeks geconverteerd naar kleine letters.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Description |
|:--- |:--- |:--- |:--- |
| stringToChange |Yes |tekenreeks |De waarde die moet worden omgezet in kleine letters. |

### <a name="return-value"></a>Retourwaarde

De teken reeks die is geconverteerd naar een kleine letter.

### <a name="examples"></a>Voorbeelden

Met de volgende [voorbeeld sjabloon wordt](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/tolower.json) een parameter waarde geconverteerd naar kleine letters en naar hoofd letters.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "testString": {
      "type": "string",
      "defaultValue": "One Two Three"
    }
  },
  "resources": [],
  "outputs": {
    "toLowerOutput": {
      "value": "[toLower(parameters('testString'))]",
      "type": "string"
    },
    "toUpperOutput": {
      "type": "string",
      "value": "[toUpper(parameters('testString'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param testString string = 'One Two Three'

output toLowerOutput string = toLower(testString)
output toUpperOutput string = toUpper(testString)
```

---

De uitvoer van het vorige voor beeld met de standaard waarden is:

| Naam | Type | Waarde |
| ---- | ---- | ----- |
| toLowerOutput | Tekenreeks | Een twee drie |
| toUpperOutput | Tekenreeks | Een twee drie |

## <a name="toupper"></a>toUpper

`toUpper(stringToChange)`

Hiermee wordt de opgegeven teken reeks geconverteerd naar hoofd letters.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Description |
|:--- |:--- |:--- |:--- |
| stringToChange |Yes |tekenreeks |De waarde die moet worden omgezet in hoofd letters. |

### <a name="return-value"></a>Retourwaarde

De teken reeks die naar hoofd letters is geconverteerd.

### <a name="examples"></a>Voorbeelden

Met de volgende [voorbeeld sjabloon wordt](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/tolower.json) een parameter waarde geconverteerd naar kleine letters en naar hoofd letters.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "testString": {
      "type": "string",
      "defaultValue": "One Two Three"
    }
  },
  "resources": [],
  "outputs": {
    "toLowerOutput": {
      "value": "[toLower(parameters('testString'))]",
      "type": "string"
    },
    "toUpperOutput": {
      "type": "string",
      "value": "[toUpper(parameters('testString'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param testString string = 'One Two Three'

output toLowerOutput string = toLower(testString)
output toUpperOutput string = toUpper(testString)
```

---

De uitvoer van het vorige voor beeld met de standaard waarden is:

| Naam | Type | Waarde |
| ---- | ---- | ----- |
| toLowerOutput | Tekenreeks | Een twee drie |
| toUpperOutput | Tekenreeks | Een twee drie |

## <a name="trim"></a>interne

`trim (stringToTrim)`

Verwijdert alle voor loop-en volg spaties uit de opgegeven teken reeks.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Description |
|:--- |:--- |:--- |:--- |
| stringToTrim |Yes |tekenreeks |De waarde die moet worden bijgesneden. |

### <a name="return-value"></a>Retourwaarde

De teken reeks zonder voor loop-en volg spaties.

### <a name="examples"></a>Voorbeelden

Met de volgende [voorbeeld sjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/trim.json) worden de witruimte tekens uit de para meter verkleind.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "testString": {
      "type": "string",
      "defaultValue": "    one two three   "
    }
  },
  "resources": [],
  "outputs": {
    "return": {
      "type": "string",
      "value": "[trim(parameters('testString'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param testString string = '    one two three   '

output return string = trim(testString)
```

---

De uitvoer van het vorige voor beeld met de standaard waarden is:

| Naam | Type | Waarde |
| ---- | ---- | ----- |
| terugkeren | Tekenreeks | Een twee drie |

## <a name="uniquestring"></a>uniqueString

`uniqueString (baseString, ...)`

Hiermee maakt u een deterministische hash-teken reeks op basis van de waarden die zijn opgegeven als para meters.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Description |
|:--- |:--- |:--- |:--- |
| baseString |Yes |tekenreeks |De waarde die wordt gebruikt in de hash-functie om een unieke teken reeks te maken. |
| aanvullende para meters indien nodig |No |tekenreeks |U kunt zoveel teken reeksen toevoegen als u nodig hebt om de waarde te maken waarmee het niveau van uniekheid wordt opgegeven. |

### <a name="remarks"></a>Opmerkingen

Deze functie is handig als u een unieke naam voor een resource wilt maken. U geeft parameter waarden op die het bereik van de uniekheid voor het resultaat beperken. U kunt opgeven of de naam uniek is voor het abonnement, de resource groep of de implementatie.

De geretourneerde waarde is geen wille keurige teken reeks, maar in plaats daarvan het resultaat van een hash-functie. De geretourneerde waarde is 13 tekens lang. Het is niet wereld wijd uniek. U kunt de waarde met een voor voegsel uit uw naam Conventie combi neren om een naam te maken die zinvol is. In het volgende voor beeld ziet u de indeling van de geretourneerde waarde. De werkelijke waarde is afhankelijk van de opgegeven para meters.

`tcvhiyu5h2o5o`

In de volgende voor beelden ziet u hoe u Unique string kunt gebruiken om een unieke waarde te maken voor veelgebruikte niveaus.

Uniek bereik voor abonnement

# <a name="json"></a>[JSON](#tab/json)

```json
"[uniqueString(subscription().subscriptionId)]"
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
uniqueString(subscription().subscriptionId)
```

---

Uniek bereik van resource groep

# <a name="json"></a>[JSON](#tab/json)

```json
"[uniqueString(resourceGroup().id)]"
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
uniqueString(resourceGroup().id)
```

---

Uniek bereik voor implementatie voor een resource groep

# <a name="json"></a>[JSON](#tab/json)

```json
"[uniqueString(resourceGroup().id, deployment().name)]"
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
uniqueString(resourceGroup().id, deployment().name)
```

---

In het volgende voor beeld ziet u hoe u een unieke naam maakt voor een opslag account op basis van uw resource groep. In de resource groep is de naam niet uniek als deze op dezelfde manier wordt gemaakt.

# <a name="json"></a>[JSON](#tab/json)

```json
"resources": [{
  "name": "[concat('storage', uniqueString(resourceGroup().id))]",
  "type": "Microsoft.Storage/storageAccounts",
  ...
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
resource mystorage 'Microsoft.Storage/storageAccounts@@2018-07-01' = {
  name: 'storage${uniqueString(resourceGroup().id)}'
  ...
}
```

---

Als u elke keer dat u een sjabloon implementeert een nieuwe unieke naam moet maken en u niet van plan bent om de resource bij te werken, kunt u de functie [utcNow](template-functions-date.md#utcnow) gebruiken met Unique string. U kunt deze benadering gebruiken in een test omgeving. Zie [utcNow](template-functions-date.md#utcnow)voor een voor beeld.

### <a name="return-value"></a>Retourwaarde

Een teken reeks met 13 tekens.

### <a name="examples"></a>Voorbeelden

De volgende [voorbeeld sjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uniquestring.json) retourneert resultaten van Unique string:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "uniqueRG": {
      "value": "[uniqueString(resourceGroup().id)]",
      "type": "string"
    },
    "uniqueDeploy": {
      "value": "[uniqueString(resourceGroup().id, deployment().name)]",
      "type": "string"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output uniqueRG string = uniqueString(resourceGroup().id)
output uniqueDeploy string = uniqueString(resourceGroup().id, deployment().name)
```

---

## <a name="uri"></a>URI

`uri (baseUri, relativeUri)`

Hiermee maakt u een absolute URI door het combi neren van de baseUri en de relativeUri-teken reeks.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Description |
|:--- |:--- |:--- |:--- |
| baseUri |Yes |tekenreeks |De basis-URI-teken reeks. Let op het gedrag van het afhandelen van de afsluitende slash ('/'), zoals wordt beschreven in de volgende tabel.  |
| relativeUri |Yes |tekenreeks |De relatieve Uri-teken reeks die moet worden toegevoegd aan de basis-URI-teken reeks. |

* Als **baseUri** eindigt op een afsluitende slash, wordt het resultaat gewoon **BaseUri** gevolgd door **relativeUri**.

* Als **baseUri** niet in een afsluitende schuine streep eindigt, gebeurt er een van de twee dingen.

   * Als **baseUri** helemaal geen schuine strepen heeft (afgezien van de '//'), is het **resultaat eenvoudigweg,** gevolgd door **relativeUri**.

   * Als **baseUri** een paar schuine strepen heeft, maar niet eindigt met een schuine streep, wordt alles vanaf de laatste schuine streep verwijderd uit **baseuri** en wordt het resultaat **baseuri** gevolgd door **relativeUri**.

Hier volgen enkele voorbeelden:

```
uri('http://contoso.org/firstpath', 'myscript.sh') -> http://contoso.org/myscript.sh
uri('http://contoso.org/firstpath/', 'myscript.sh') -> http://contoso.org/firstpath/myscript.sh
uri('http://contoso.org/firstpath/azuredeploy.json', 'myscript.sh') -> http://contoso.org/firstpath/myscript.sh
uri('http://contoso.org/firstpath/azuredeploy.json/', 'myscript.sh') -> http://contoso.org/firstpath/azuredeploy.json/myscript.sh
```
Voor volledige details worden de para meters **baseUri** en **relativeUri** omgezet zoals beschreven in [RFC 3986, sectie 5](https://tools.ietf.org/html/rfc3986#section-5).

### <a name="return-value"></a>Retourwaarde

Een teken reeks die de absolute URI voor de basis-en relatieve waarden vertegenwoordigt.

### <a name="examples"></a>Voorbeelden

In het volgende voor beeld ziet u hoe u een koppeling naar een geneste sjabloon bouwt op basis van de waarde van de bovenliggende sjabloon.

# <a name="json"></a>[JSON](#tab/json)

```json
"templateLink": "[uri(deployment().properties.templateLink.uri, 'nested/azuredeploy.json')]"
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
templateLink: uri(deployment().properties.templateLink.uri, 'nested/azuredeploy.json')
```

---

In de volgende [voorbeeld sjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uri.json) ziet u hoe u URI, UriComponent en uriComponentToString gebruikt:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "variables": {
    "uriFormat": "[uri('http://contoso.com/resources/', 'nested/azuredeploy.json')]",
    "uriEncoded": "[uriComponent(variables('uriFormat'))]"
  },
  "resources": [
  ],
  "outputs": {
    "uriOutput": {
      "type": "string",
      "value": "[variables('uriFormat')]"
    },
    "componentOutput": {
      "type": "string",
      "value": "[variables('uriEncoded')]"
    },
    "toStringOutput": {
      "type": "string",
      "value": "[uriComponentToString(variables('uriEncoded'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
var uriFormat = uri('http://contoso.com/resources/', 'nested/azuredeploy.json')
var uriEncoded = uriComponent(uriFormat)

output uriOutput string = uriFormat
output componentOutput string = uriEncoded
output toStringOutput string = uriComponentToString(uriEncoded)
```

---

De uitvoer van het vorige voor beeld met de standaard waarden is:

| Naam | Type | Waarde |
| ---- | ---- | ----- |
| uriOutput | Tekenreeks | `http://contoso.com/resources/nested/azuredeploy.json` |
| componentOutput | Tekenreeks | `http%3A%2F%2Fcontoso.com%2Fresources%2Fnested%2Fazuredeploy.json` |
| toStringOutput | Tekenreeks | `http://contoso.com/resources/nested/azuredeploy.json` |

## <a name="uricomponent"></a>uriComponent

`uricomponent(stringToEncode)`

Codeert een URI.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Description |
|:--- |:--- |:--- |:--- |
| stringToEncode |Yes |tekenreeks |De waarde die moet worden gecodeerd. |

### <a name="return-value"></a>Retourwaarde

Een teken reeks van de gecodeerde URI-waarde.

### <a name="examples"></a>Voorbeelden

In de volgende [voorbeeld sjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uri.json) ziet u hoe u URI, UriComponent en uriComponentToString gebruikt:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "variables": {
    "uriFormat": "[uri('http://contoso.com/resources/', 'nested/azuredeploy.json')]",
    "uriEncoded": "[uriComponent(variables('uriFormat'))]"
  },
  "resources": [
  ],
  "outputs": {
    "uriOutput": {
      "type": "string",
      "value": "[variables('uriFormat')]"
    },
    "componentOutput": {
      "type": "string",
      "value": "[variables('uriEncoded')]"
    },
    "toStringOutput": {
      "type": "string",
      "value": "[uriComponentToString(variables('uriEncoded'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
var uriFormat = uri('http://contoso.com/resources/', 'nested/azuredeploy.json')
var uriEncoded = uriComponent(uriFormat)

output uriOutput string = uriFormat
output componentOutput string = uriEncoded
output toStringOutput string = uriComponentToString(uriEncoded)
```

---

De uitvoer van het vorige voor beeld met de standaard waarden is:

| Naam | Type | Waarde |
| ---- | ---- | ----- |
| uriOutput | Tekenreeks | `http://contoso.com/resources/nested/azuredeploy.json` |
| componentOutput | Tekenreeks | `http%3A%2F%2Fcontoso.com%2Fresources%2Fnested%2Fazuredeploy.json` |
| toStringOutput | Tekenreeks | `http://contoso.com/resources/nested/azuredeploy.json` |

## <a name="uricomponenttostring"></a>uriComponentToString

`uriComponentToString(uriEncodedString)`

Retourneert een teken reeks van een gecodeerde URI-waarde.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Description |
|:--- |:--- |:--- |:--- |
| uriEncodedString |Yes |tekenreeks |De gecodeerde URI-waarde die moet worden geconverteerd naar een teken reeks. |

### <a name="return-value"></a>Retourwaarde

Een gecodeerde teken reeks met URI-code ring.

### <a name="examples"></a>Voorbeelden

In de volgende [voorbeeld sjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uri.json) ziet u hoe u URI, UriComponent en uriComponentToString gebruikt:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "variables": {
    "uriFormat": "[uri('http://contoso.com/resources/', 'nested/azuredeploy.json')]",
    "uriEncoded": "[uriComponent(variables('uriFormat'))]"
  },
  "resources": [
  ],
  "outputs": {
    "uriOutput": {
      "type": "string",
      "value": "[variables('uriFormat')]"
    },
    "componentOutput": {
      "type": "string",
      "value": "[variables('uriEncoded')]"
    },
    "toStringOutput": {
      "type": "string",
      "value": "[uriComponentToString(variables('uriEncoded'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
var uriFormat = uri('http://contoso.com/resources/', 'nested/azuredeploy.json')
var uriEncoded = uriComponent(uriFormat)

output uriOutput string = uriFormat
output componentOutput string = uriEncoded
output toStringOutput string = uriComponentToString(uriEncoded)
```

---

De uitvoer van het vorige voor beeld met de standaard waarden is:

| Naam | Type | Waarde |
| ---- | ---- | ----- |
| uriOutput | Tekenreeks | `http://contoso.com/resources/nested/azuredeploy.json` |
| componentOutput | Tekenreeks | `http%3A%2F%2Fcontoso.com%2Fresources%2Fnested%2Fazuredeploy.json` |
| toStringOutput | Tekenreeks | `http://contoso.com/resources/nested/azuredeploy.json` |

## <a name="next-steps"></a>Volgende stappen

* Zie [inzicht krijgen in de structuur en syntaxis van arm-sjablonen](template-syntax.md)voor een beschrijving van de secties in een arm-sjabloon.
* Zie voor het samen voegen [van meerdere sjablonen gekoppelde en geneste sjablonen gebruiken bij het implementeren van Azure-resources](linked-templates.md).
* Als u een bepaald aantal keer wilt herhalen bij het maken van een resource type, raadpleegt u [resource-iteratie in arm-sjablonen](copy-resources.md).
* Zie [resources implementeren met arm-sjablonen en Azure PowerShell](deploy-powershell.md)voor meer informatie over het implementeren van de sjabloon die u hebt gemaakt.
