---
title: Sjabloon functies-matrices
description: Hierin worden de functies beschreven die u kunt gebruiken in een Azure Resource Manager sjabloon (ARM-sjabloon) voor het werken met matrices.
ms.topic: conceptual
ms.date: 11/18/2020
ms.openlocfilehash: 40a6815bb10ce9725405d68498b9a554706f3af8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96920547"
---
# <a name="array-functions-for-arm-templates"></a>Matrix functies voor ARM-sjablonen

Resource Manager biedt verschillende functies voor het werken met matrices in uw Azure Resource Manager sjabloon (ARM-sjabloon):

* [matrix](#array)
* [concat](#concat)
* [daarin](#contains)
* [createArray](#createarray)
* [leeg](#empty)
* [instantie](#first)
* [Snij punt](#intersection)
* [duren](#last)
* [length](#length)
* [aantal](#max)
* [min.](#min)
* [bereik](#range)
* [verdergaan](#skip)
* [Houd](#take)
* [Réunion](#union)

Als u een matrix van teken reeks waarden wilt ophalen die door een waarde worden gescheiden, raadpleegt u [splitsen](template-functions-string.md#split).

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="array"></a>matrix

`array(convertToArray)`

Zet de waarde om in een matrix.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| convertToArray |Ja |int, String, array of object |De waarde die moet worden geconverteerd naar een matrix. |

### <a name="return-value"></a>Retourwaarde

Een matrix.

### <a name="example"></a>Voorbeeld

In het volgende voor beeld ziet u hoe u de matrix functie kunt gebruiken met verschillende typen.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "intToConvert": {
      "type": "int",
      "defaultValue": 1
    },
    "stringToConvert": {
      "type": "string",
      "defaultValue": "efgh"
    },
    "objectToConvert": {
      "type": "object",
      "defaultValue": {
        "a": "b",
        "c": "d"
      }
    }
  },
  "resources": [
  ],
  "outputs": {
    "intOutput": {
      "type": "array",
      "value": "[array(parameters('intToConvert'))]"
    },
    "stringOutput": {
      "type": "array",
      "value": "[array(parameters('stringToConvert'))]"
    },
    "objectOutput": {
      "type": "array",
      "value": "[array(parameters('objectToConvert'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param intToConvert int = 1
param stringToConvert string = 'efgh'
param objectToConvert object = {
  'a': 'b'
  'c': 'd'
}

output intOutput array = array(intToConvert)
output stringOutput array = array(stringToConvert)
output objectOutput array = array(objectToConvert)
```

---

De uitvoer van het vorige voor beeld met de standaard waarden is:

| Naam | Type | Waarde |
| ---- | ---- | ----- |
| intOutput | Matrix |  [1] |
| stringOutput | Matrix | ["efgh"] |
| objectOutput | Matrix | [{"a": "b", "c": "d"}] |

## <a name="concat"></a>concat

`concat(arg1, arg2, arg3, ...)`

Combineert meerdere matrices en retourneert de samengevoegde matrix, of combineert meerdere teken reeks waarden en retourneert de aaneengeschakelde teken reeks.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| Arg1 |Ja |matrix of teken reeks |De eerste matrix of teken reeks voor samen voeging. |
| aanvullende argumenten |Nee |matrix of teken reeks |Extra matrices of teken reeksen in sequentiële volg orde voor samen voeging. |

Deze functie kan elk wille keurig aantal argumenten hebben en kan teken reeksen of matrices voor de para meters accepteren. U kunt echter geen matrixen en teken reeksen opgeven voor para meters. Matrices worden alleen samengevoegd met andere matrices.

### <a name="return-value"></a>Retourwaarde

Een teken reeks of matrix van aaneengeschakelde waarden.

### <a name="example"></a>Voorbeeld

In het volgende voor beeld ziet u hoe u twee matrices combineert.

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

---

De uitvoer van het vorige voor beeld met de standaard waarden is:

| Naam | Type | Waarde |
| ---- | ---- | ----- |
| concatOutput | Tekenreeks | prefix-5yj4yjf5mbg72 |

## <a name="contains"></a>bevat

`contains(container, itemToFind)`

Controleert of een matrix een waarde bevat, een object bevat een sleutel of een teken reeks bevat een subtekenreeks. De teken reeks vergelijking is hoofdletter gevoelig. Als er echter wordt getest of een object een sleutel bevat, is de vergelijking niet hoofdletter gevoelig.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| container |Ja |matrix, object of teken reeks |De waarde die de te zoeken waarde bevat. |
| itemToFind |Ja |teken reeks of int |De waarde die moet worden gevonden. |

### <a name="return-value"></a>Retourwaarde

**Waar** als het item is gevonden; anders **False**.

### <a name="example"></a>Voorbeeld

In het volgende voor beeld ziet u hoe u deze gebruikt met verschillende typen:

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

## <a name="createarray"></a>createArray

`createArray (arg1, arg2, arg3, ...)`

Hiermee wordt een matrix gemaakt op basis van de para meters. De `createArray` functie wordt niet ondersteund door Bicep.  Een letterlijke matrix waarde maken met behulp van `[]` .

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| argumenten |Nee |Teken reeks, geheel getal, matrix of object |De waarden in de matrix. |

### <a name="return-value"></a>Retourwaarde

Een matrix. Als er geen para meters zijn opgegeven, wordt een lege matrix geretourneerd.

### <a name="example"></a>Voorbeeld

In het volgende voor beeld ziet u hoe u createArray gebruikt met verschillende typen:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
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
    "stringArray": {
      "type": "array",
      "value": "[createArray('a', 'b', 'c')]"
    },
    "intArray": {
      "type": "array",
      "value": "[createArray(1, 2, 3)]"
    },
    "objectArray": {
      "type": "array",
      "value": "[createArray(parameters('objectToTest'))]"
    },
    "arrayArray": {
      "type": "array",
      "value": "[createArray(parameters('arrayToTest'))]"
    },
    "emptyArray": {
      "type": "array",
      "value": "[createArray()]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

> [!NOTE]
> `createArray()` wordt niet ondersteund door Bicep.  Een letterlijke matrix waarde maken met behulp van `[]` .

---

De uitvoer van het vorige voor beeld met de standaard waarden is:

| Naam | Type | Waarde |
| ---- | ---- | ----- |
| stringArray | Matrix | ["a", "b", "c"] |
| intArray | Matrix | [1, 2, 3] |
| objectArray | Matrix | [{"een": "a", "twee": "b", "drie": "c"}] |
| arrayArray | Matrix | [[' een ', ' twee ', ' drie ']] |
| emptyArray | Matrix | [] |

## <a name="empty"></a>leeg

`empty(itemToTest)`

Bepaalt of een matrix, een object of een teken reeks leeg is.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| itemToTest |Ja |matrix, object of teken reeks |De waarde die moet worden gecontroleerd als deze leeg is. |

### <a name="return-value"></a>Retourwaarde

Retourneert **waar** als de waarde leeg is; anders **False**.

### <a name="example"></a>Voorbeeld

In het volgende voor beeld wordt gecontroleerd of een matrix, een object en een teken reeks leeg zijn.

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

## <a name="first"></a>instantie

`first(arg1)`

Retourneert het eerste element van de matrix, of het eerste teken van de teken reeks.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| Arg1 |Ja |matrix of teken reeks |De waarde voor het ophalen van het eerste element of teken. |

### <a name="return-value"></a>Retourwaarde

Het type (teken reeks, int, matrix of object) van het eerste element in een matrix of het eerste teken van een teken reeks.

### <a name="example"></a>Voorbeeld

In het volgende voor beeld ziet u hoe u de eerste functie gebruikt met een matrix en teken reeks.

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

## <a name="intersection"></a>Snij punt

`intersection(arg1, arg2, arg3, ...)`

Retourneert een enkele matrix of een object met de algemene elementen van de para meters.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| Arg1 |Ja |matrix of object |De eerste waarde die moet worden gebruikt voor het zoeken van algemene elementen. |
| Arg2 |Ja |matrix of object |De tweede waarde die moet worden gebruikt voor het zoeken van algemene elementen. |
| aanvullende argumenten |Nee |matrix of object |Aanvullende waarden die moeten worden gebruikt voor het zoeken van algemene elementen. |

### <a name="return-value"></a>Retourwaarde

Een matrix of object met de algemene elementen.

### <a name="example"></a>Voorbeeld

In het volgende voor beeld ziet u hoe u het snij punt gebruikt met matrices en objecten:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "firstObject": {
      "type": "object",
      "defaultValue": {
        "one": "a",
        "two": "b",
        "three": "c"
      }
    },
    "secondObject": {
      "type": "object",
      "defaultValue": {
        "one": "a",
        "two": "z",
        "three": "c"
      }
    },
    "firstArray": {
      "type": "array",
      "defaultValue": [ "one", "two", "three" ]
    },
    "secondArray": {
      "type": "array",
      "defaultValue": [ "two", "three" ]
    }
  },
  "resources": [
  ],
  "outputs": {
    "objectOutput": {
      "type": "object",
      "value": "[intersection(parameters('firstObject'), parameters('secondObject'))]"
    },
    "arrayOutput": {
      "type": "array",
      "value": "[intersection(parameters('firstArray'), parameters('secondArray'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param firstObject object = {
  'one': 'a'
  'two': 'b'
  'three': 'c'
}

param secondObject object = {
  'one': 'a'
  'two': 'z'
  'three': 'c'
}

param firstArray array = [
  'one'
  'two'
  'three'
]

param secondArray array = [
  'two'
  'three'
]

output objectOutput object = intersection(firstObject, secondObject)
output arrayOutput array = intersection(firstArray, secondArray)
```

---

De uitvoer van het vorige voor beeld met de standaard waarden is:

| Naam | Type | Waarde |
| ---- | ---- | ----- |
| objectOutput | Object | {"een": "a", "drie": "c"} |
| arrayOutput | Matrix | ["twee", "drie"] |

## <a name="last"></a>duren

`last (arg1)`

Retourneert het laatste element van de matrix, of het laatste teken van de teken reeks.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| Arg1 |Ja |matrix of teken reeks |De waarde voor het ophalen van het laatste element of teken. |

### <a name="return-value"></a>Retourwaarde

Het type (teken reeks, int, matrix of object) van het laatste element in een matrix of het laatste teken van een teken reeks.

### <a name="example"></a>Voorbeeld

In het volgende voor beeld ziet u hoe u de laatste functie gebruikt met een matrix en teken reeks.

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
output stringOutput string = last('One Two three')
```

---

De uitvoer van het vorige voor beeld met de standaard waarden is:

| Naam | Type | Waarde |
| ---- | ---- | ----- |
| arrayOutput | Tekenreeks | drie |
| stringOutput | Tekenreeks | e |

## <a name="length"></a>lengte

`length(arg1)`

Retourneert het aantal elementen in een matrix, tekens in een teken reeks of hoofd niveau-eigenschappen in een-object.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| Arg1 |Ja |matrix, teken reeks of object |De matrix die moet worden gebruikt voor het ophalen van het aantal elementen, de teken reeks die moet worden gebruikt voor het ophalen van het aantal tekens of het object dat moet worden gebruikt voor het ophalen van het aantal eigenschappen op hoofd niveau. |

### <a name="return-value"></a>Retourwaarde

Een int.

### <a name="example"></a>Voorbeeld

In het volgende voor beeld ziet u hoe length met een matrix en teken reeks kan worden gebruikt:

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

U kunt deze functie gebruiken met een matrix om het aantal iteraties op te geven bij het maken van resources. In het volgende voor beeld verwijst de para meter **siteNames** naar een matrix met namen die moeten worden gebruikt bij het maken van de websites.

# <a name="json"></a>[JSON](#tab/json)

```json
"copy": {
  "name": "websitescopy",
  "count": "[length(parameters('siteNames'))]"
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

> [!NOTE]
> Lussen zijn nog niet geïmplementeerd in Bicep.  Zie [lussen](https://github.com/Azure/bicep/blob/main/docs/spec/loops.md).

---

Zie [resource-iteratie in arm-sjablonen](copy-resources.md)voor meer informatie over het gebruik van deze functie met een matrix.

## <a name="max"></a>max

`max(arg1)`

Retourneert de maximum waarde van een matrix met gehele getallen of een door komma's gescheiden lijst met gehele getallen.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| Arg1 |Ja |matrix van gehele getallen of door komma's gescheiden lijst met gehele getallen |De verzameling om de maximum waarde op te halen. |

### <a name="return-value"></a>Retourwaarde

Een geheel getal dat de maximum waarde vertegenwoordigt.

### <a name="example"></a>Voorbeeld

In het volgende voor beeld ziet u hoe u Max kunt gebruiken met een matrix en een lijst met gehele getallen:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "arrayToTest": {
      "type": "array",
      "defaultValue": [ 0, 3, 2, 5, 4 ]
    }
  },
  "resources": [],
  "outputs": {
    "arrayOutput": {
      "type": "int",
      "value": "[max(parameters('arrayToTest'))]"
    },
    "intOutput": {
      "type": "int",
      "value": "[max(0,3,2,5,4)]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param arrayToTest array = [
  0
  3
  2
  5
  4
]

output arrayOutput int = max(arrayToTest)
output intOutput int = max(0,3,2,5,4)
```

---

De uitvoer van het vorige voor beeld met de standaard waarden is:

| Naam | Type | Waarde |
| ---- | ---- | ----- |
| arrayOutput | Int | 5 |
| intOutput | Int | 5 |

## <a name="min"></a>min.

`min(arg1)`

Retourneert de minimum waarde van een matrix met gehele getallen of een door komma's gescheiden lijst met gehele getallen.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| Arg1 |Ja |matrix van gehele getallen of door komma's gescheiden lijst met gehele getallen |De verzameling om de minimum waarde op te halen. |

### <a name="return-value"></a>Retourwaarde

Een geheel getal dat de minimum waarde vertegenwoordigt.

### <a name="example"></a>Voorbeeld

In het volgende voor beeld ziet u hoe min met een matrix en een lijst met gehele getallen worden gebruikt:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "arrayToTest": {
      "type": "array",
      "defaultValue": [ 0, 3, 2, 5, 4 ]
    }
  },
  "resources": [],
  "outputs": {
    "arrayOutput": {
      "type": "int",
      "value": "[min(parameters('arrayToTest'))]"
    },
    "intOutput": {
      "type": "int",
      "value": "[min(0,3,2,5,4)]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param arrayToTest array = [
  0
  3
  2
  5
  4
]

output arrayOutput int = min(arrayToTest)
output intOutput int = min(0,3,2,5,4)
```

---

De uitvoer van het vorige voor beeld met de standaard waarden is:

| Naam | Type | Waarde |
| ---- | ---- | ----- |
| arrayOutput | Int | 0 |
| intOutput | Int | 0 |

## <a name="range"></a>bereik

`range(startIndex, count)`

Hiermee maakt u een matrix met gehele getallen van een begin-geheel getal dat een aantal items bevat.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| Start index |Ja |int |Het eerste geheel getal in de matrix. De som van start index en aantal mag niet groter zijn dan 2147483647. |
| count |Ja |int |Het aantal gehele getallen in de matrix. Moet een niet-negatief geheel getal zijn van Maxi maal 10000. |

### <a name="return-value"></a>Retourwaarde

Een matrix met gehele getallen.

### <a name="example"></a>Voorbeeld

In het volgende voor beeld ziet u hoe u de functie Range gebruikt:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "startingInt": {
      "type": "int",
      "defaultValue": 5
    },
    "numberOfElements": {
      "type": "int",
      "defaultValue": 3
    }
  },
  "resources": [],
  "outputs": {
    "rangeOutput": {
      "type": "array",
      "value": "[range(parameters('startingInt'),parameters('numberOfElements'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param startingInt int = 5
param numberOfElements int = 3

output rangeOutput array = range(startingInt, numberOfElements)
```

---

De uitvoer van het vorige voor beeld met de standaard waarden is:

| Naam | Type | Waarde |
| ---- | ---- | ----- |
| rangeOutput | Matrix | [5, 6, 7] |

## <a name="skip"></a>skip

`skip(originalValue, numberToSkip)`

Retourneert een matrix met alle elementen na het opgegeven getal in de matrix, of retourneert een teken reeks met alle tekens na het opgegeven getal in de teken reeks.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| originalValue |Ja |matrix of teken reeks |De matrix of teken reeks die moet worden gebruikt voor het overs Laan. |
| numberToSkip |Ja |int |Het aantal elementen of tekens dat moet worden overgeslagen. Als deze waarde 0 of kleiner is, worden alle elementen of tekens in de waarde geretourneerd. Als deze groter is dan de lengte van de matrix of teken reeks, wordt een lege matrix of teken reeks geretourneerd. |

### <a name="return-value"></a>Retourwaarde

Een matrix of teken reeks.

### <a name="example"></a>Voorbeeld

In het volgende voor beeld wordt het opgegeven aantal elementen in de matrix en het opgegeven aantal tekens in een teken reeks overs Laan.

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

## <a name="take"></a>take

`take(originalValue, numberToTake)`

Retourneert een matrix met het opgegeven aantal elementen vanaf het begin van de matrix, of een teken reeks met het opgegeven aantal tekens vanaf het begin van de teken reeks.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| originalValue |Ja |matrix of teken reeks |De matrix of teken reeks waaruit de elementen moeten worden afgeleid. |
| numberToTake |Ja |int |Het aantal elementen of tekens dat moet worden uitgevoerd. Als deze waarde 0 of kleiner is, wordt een lege matrix of teken reeks geretourneerd. Als deze groter is dan de lengte van de opgegeven matrix of teken reeks, worden alle elementen in de matrix of teken reeks geretourneerd. |

### <a name="return-value"></a>Retourwaarde

Een matrix of teken reeks.

### <a name="example"></a>Voorbeeld

In het volgende voor beeld wordt het opgegeven aantal elementen uit de matrix en tekens uit een teken reeks gebruikt.

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

## <a name="union"></a>Réunion

`union(arg1, arg2, arg3, ...)`

Retourneert een enkele matrix of een object met alle elementen van de para meters. Dubbele waarden of sleutels zijn slechts eenmaal opgenomen.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| Arg1 |Ja |matrix of object |De eerste waarde die moet worden gebruikt voor het toevoegen van elementen. |
| Arg2 |Ja |matrix of object |De tweede waarde die moet worden gebruikt voor het toevoegen van elementen. |
| aanvullende argumenten |Nee |matrix of object |Aanvullende waarden die moeten worden gebruikt voor het toevoegen van elementen. |

### <a name="return-value"></a>Retourwaarde

Een matrix of object.

### <a name="example"></a>Voorbeeld

In het volgende voor beeld ziet u hoe u Union gebruikt met matrices en objecten:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "firstObject": {
      "type": "object",
      "defaultValue": {
        "one": "a",
        "two": "b",
        "three": "c1"
      }
    },
    "secondObject": {
      "type": "object",
      "defaultValue": {
        "three": "c2",
        "four": "d",
        "five": "e"
      }
    },
    "firstArray": {
      "type": "array",
      "defaultValue": [ "one", "two", "three" ]
    },
    "secondArray": {
      "type": "array",
      "defaultValue": [ "three", "four" ]
    }
  },
  "resources": [
  ],
  "outputs": {
    "objectOutput": {
      "type": "object",
      "value": "[union(parameters('firstObject'), parameters('secondObject'))]"
    },
    "arrayOutput": {
      "type": "array",
      "value": "[union(parameters('firstArray'), parameters('secondArray'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param firstObject object = {
  'one': 'a'
  'two': 'b'
  'three': 'c1'
}

param secondObject object = {
  'three': 'c2'
  'four': 'd'
  'five': 'e'
}

param firstArray array = [
  'one'
  'two'
  'three'
]

param secondArray array = [
  'three'
  'four'
]

output objectOutput object = union(firstObject, secondObject)
output arrayOutput array = union(firstArray, secondArray)
```

---

De uitvoer van het vorige voor beeld met de standaard waarden is:

| Naam | Type | Waarde |
| ---- | ---- | ----- |
| objectOutput | Object | {"One": "a", "twee": "b", "drie": "C2", "vier": "d", "vijf": "e"} |
| arrayOutput | Matrix | [' één ', ' twee ', ' drie ', ' vier '] |

## <a name="next-steps"></a>Volgende stappen

* Zie [inzicht krijgen in de structuur en syntaxis van arm-sjablonen](template-syntax.md)voor een beschrijving van de secties in een arm-sjabloon.
