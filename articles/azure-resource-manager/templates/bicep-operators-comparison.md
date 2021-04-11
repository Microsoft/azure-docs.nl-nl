---
title: Bicep vergelijkings operators
description: Hierin worden Bicep vergelijkings operatoren beschreven waarmee waarden worden vergeleken.
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: c382ef355131d271d9f4fa4a978a807b9aac1e4f
ms.sourcegitcommit: c3739cb161a6f39a9c3d1666ba5ee946e62a7ac3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2021
ms.locfileid: "107211303"
---
# <a name="bicep-comparison-operators"></a>Bicep vergelijkings operators

De vergelijkings operators vergelijken waarden en retour neren `true` of `false` . Als u de voor beelden wilt uitvoeren, gebruikt u Azure CLI of Azure PowerShell voor het [implementeren van een Bicep-bestand](bicep-tutorial-create-first-bicep.md#deploy-bicep-file).

| Operator | Name |
| ---- | ---- |
| `>=` | [Groter dan of gelijk aan](#greater-than-or-equal-) |
| `>`  | [Groter dan](#greater-than-) |
| `<=` | [Kleiner dan of gelijk aan](#less-than-or-equal-) |
| `<`  | [Kleiner dan](#less-than-) |
| `==` | [Gelijk aan](#equals-) |
| `!=` | [Is niet gelijk aan](#not-equal-) |
| `=~` | [Gelijk aan niet-hoofdletter gevoelig](#equal-case-insensitive-) |
| `!~` | [Niet gelijk aan hoofdletter gevoelig](#not-equal-case-insensitive-) |

## <a name="greater-than-or-equal-"></a>Groter dan of gelijk aan >=

`operand1 >= operand2`

Evalueert of de eerste waarde groter is dan of gelijk is aan de tweede waarde.

### <a name="operands"></a>Operanden

| Mogelijke | Type | Description |
| ---- | ---- | ---- |
| `operand1` | geheel getal, teken reeks | De eerste waarde in de vergelijking. |
| `operand2` | geheel getal, teken reeks | De tweede waarde in de vergelijking. |

### <a name="return-value"></a>Retourwaarde

Als de eerste waarde groter is dan of gelijk is aan de tweede waarde, `true` wordt geretourneerd. Anders `false` wordt geretourneerd.

### <a name="example"></a>Voorbeeld

Een paar gehele getallen en paar teken reeksen worden vergeleken.

```bicep
param firstInt int = 10
param secondInt int = 5

param firstString string = 'A'
param secondString string = 'A'

output intGtE bool = firstInt >= secondInt
output stringGtE bool = firstString >= secondString
```

Uitvoer van het voor beeld:

| Naam | Type | Waarde |
| ---- | ---- | ---- |
| `intGtE` | booleaans | true |
| `stringGtE` | booleaans | true |

## <a name="greater-than-"></a>Groter dan >

`operand1 > operand2`

Evalueert of de eerste waarde groter is dan de tweede waarde.

### <a name="operands"></a>Operanden

| Mogelijke | Type | Description |
| ---- | ---- | ---- |
| `operand1` | geheel getal, teken reeks | De eerste waarde in de vergelijking. |
| `operand2` | geheel getal, teken reeks | De tweede waarde in de vergelijking. |

### <a name="return-value"></a>Retourwaarde

Als de eerste waarde groter is dan de tweede waarde, `true` wordt geretourneerd. Anders `false` wordt geretourneerd.

### <a name="example"></a>Voorbeeld

Een paar gehele getallen en paar teken reeksen worden vergeleken.

```bicep
param firstInt int = 10
param secondInt int = 5

param firstString string = 'bend'
param secondString string = 'band'

output intGt bool = firstInt > secondInt
output stringGt bool = firstString > secondString
```

Uitvoer van het voor beeld:

De **e** in **bocht** maakt de eerste teken reeks groter.

| Naam | Type | Waarde |
| ---- | ---- | ---- |
| `intGt` | booleaans | true |
| `stringGt` | booleaans | true |

## <a name="less-than-or-equal-"></a>Kleiner dan of gelijk aan <=

`operand1 <= operand2`

Evalueert of de eerste waarde kleiner is dan of gelijk is aan de tweede waarde.

### <a name="operands"></a>Operanden

| Mogelijke | Type | Description |
| ---- | ---- | ---- |
| `operand1` | geheel getal, teken reeks | De eerste waarde in de vergelijking. |
| `operand2` | geheel getal, teken reeks | De tweede waarde in de vergelijking. |

### <a name="return-value"></a>Retourwaarde

Als de eerste waarde kleiner dan of gelijk aan de tweede waarde is, `true` wordt geretourneerd. Anders `false` wordt geretourneerd.

### <a name="example"></a>Voorbeeld

Een paar gehele getallen en paar teken reeksen worden vergeleken.

```bicep
param firstInt int = 5
param secondInt int = 10

param firstString string = 'demo'
param secondString string = 'demo'

output intLtE bool = firstInt <= secondInt
output stringLtE bool = firstString <= secondString
```

Uitvoer van het voor beeld:

| Naam | Type | Waarde |
| ---- | ---- | ---- |
| `intLtE` | booleaans | true |
| `stringLtE` | booleaans | true |

## <a name="less-than-"></a>Kleiner dan <

`operand1 < operand2`

Evalueert of de eerste waarde lager is dan de tweede waarde.

### <a name="operands"></a>Operanden

| Mogelijke | Type | Description |
| ---- | ---- | ---- |
| `operand1` | geheel getal, teken reeks | De eerste waarde in de vergelijking. |
| `operand2` | geheel getal, teken reeks | De tweede waarde in de vergelijking. |

### <a name="return-value"></a>Retourwaarde

Als de eerste waarde lager is dan de tweede waarde, `true` wordt geretourneerd. Anders `false` wordt geretourneerd.

### <a name="example"></a>Voorbeeld

Een paar gehele getallen en paar teken reeksen worden vergeleken.

```bicep
param firstInt int = 5
param secondInt int = 10

param firstString string = 'demo'
param secondString string = 'Demo'

output intLt bool = firstInt < secondInt
output stringLt bool = firstString < secondString
```

Uitvoer van het voor beeld:

De teken reeks is `true` omdat kleine letters korter zijn dan hoofd letters.

| Naam | Type | Waarde |
| ---- | ---- | ---- |
| `intLt` | booleaans | true |
| `stringLt` | booleaans | true |

## <a name="equals-"></a>Is gelijk aan = =

`operand1 == operand2`

Evalueert of de waarden gelijk zijn.

### <a name="operands"></a>Operanden

| Mogelijke | Type | Description |
| ---- | ---- | ---- |
| `operand1` | teken reeks, geheel getal, Booleaanse waarde, matrix, object | De eerste waarde in de vergelijking. |
| `operand2` | teken reeks, geheel getal, Booleaanse waarde, matrix, object | De tweede waarde in de vergelijking. |

### <a name="return-value"></a>Retourwaarde

Als de operanden gelijk zijn, `true` wordt geretourneerd. Als de operanden verschillend zijn, `false` wordt geretourneerd.

### <a name="example"></a>Voorbeeld

Paren van gehele getallen, teken reeksen en Booleaanse waarden worden vergeleken.

```bicep
param firstInt int = 5
param secondInt int = 5

param firstString string = 'demo'
param secondString string = 'demo'

param firstBool bool = true
param secondBool bool = true

output intEqual bool = firstInt == secondInt
output stringEqual bool = firstString == secondString
output boolEqual bool = firstBool == secondBool
```

Uitvoer van het voor beeld:

| Naam | Type | Waarde |
| ---- | ---- | ---- |
| `intEqual` | booleaans | true |
| `stringEqual` | booleaans | true |
| `boolEqual` | booleaans | true |

## <a name="not-equal-"></a>Niet gelijk aan! =

`operand1 != operand2`

Evalueert of twee waarden **niet** gelijk zijn.

### <a name="operands"></a>Operanden

| Mogelijke | Type | Description |
| ---- | ---- | ---- |
| `operand1` | teken reeks, geheel getal, Booleaanse waarde, matrix, object | De eerste waarde in de vergelijking. |
| `operand2` | teken reeks, geheel getal, Booleaanse waarde, matrix, object | De tweede waarde in de vergelijking. |

### <a name="return-value"></a>Retourwaarde

Als de operanden **niet** gelijk zijn, `true` wordt geretourneerd. Als de operanden gelijk zijn, `false` wordt geretourneerd.

### <a name="example"></a>Voorbeeld

Paren van gehele getallen, teken reeksen en Booleaanse waarden worden vergeleken.

```bicep
param firstInt int = 10
param secondInt int = 5

param firstString string = 'demo'
param secondString string = 'test'

param firstBool bool = false
param secondBool bool = true

output intNotEqual bool = firstInt != secondInt
output stringNotEqual bool = firstString != secondString
output boolNotEqual bool = firstBool != secondBool
```

Uitvoer van het voor beeld:

| Naam | Type | Waarde |
| ---- | ---- | ---- |
| `intNotEqual` | booleaans | true |
| `stringNotEqual` | booleaans | true |
| `boolNotEqual` | booleaans | true |

## <a name="equal-case-insensitive-"></a>Gelijk aan niet-hoofdletter gevoelig = ~

`operand1 =~ operand2`

Negeert case om te bepalen of de twee waarden gelijk zijn.

### <a name="operands"></a>Operanden

| Mogelijke | Type | Description |
| ---- | ---- | ---- |
| `operand1`  | tekenreeks | De eerste teken reeks in de vergelijking. |
| `operand2`  | tekenreeks | De tweede teken reeks in de vergelijking. |

### <a name="return-value"></a>Retourwaarde

Als de teken reeksen gelijk zijn, `true` wordt geretourneerd. Anders `false` wordt geretourneerd.

### <a name="example"></a>Voorbeeld

Vergelijkt teken reeksen die gebruikmaken van verschillende letters.

```bicep
param firstString string = 'demo'
param secondString string = 'DEMO'

param thirdString string = 'demo'
param fourthString string = 'TEST'

output strEqual1 bool = firstString =~ secondString
output strEqual2 bool = thirdString =~ fourthString
```

Uitvoer van het voor beeld:

| Naam | Type | Waarde |
| ---- | ---- | ---- |
| `strEqual1` | booleaans | true |
| `strEqual2` | booleaans | onjuist |

## <a name="not-equal-case-insensitive-"></a>Niet gelijk aan-hoofdletter gevoelig! ~

`operand1 !~ operand2`

Negeert case om te bepalen of de twee waarden **niet** gelijk zijn.

### <a name="operands"></a>Operanden

| Mogelijke | Type | Description |
| ---- | ---- | ---- |
| `operand1` | tekenreeks | De eerste teken reeks in de vergelijking. |
| `operand2` | tekenreeks | De tweede teken reeks in de vergelijking. |

### <a name="return-value"></a>Retourwaarde

Als de teken reeksen **niet** gelijk zijn, `true` wordt geretourneerd. Anders `false` wordt geretourneerd.

### <a name="example"></a>Voorbeeld

Vergelijkt teken reeksen die gebruikmaken van verschillende letters.

```bicep
param firstString string = 'demo'
param secondString string = 'TEST'

param thirdString string = 'demo'
param fourthString string = 'DeMo'

output strNotEqual1 bool = firstString !~ secondString
output strEqual2 bool = thirdString !~ fourthString
```

Uitvoer van het voor beeld:

| Naam | Type | Waarde |
| ---- | ---- | ---- |
| `strNotEqual1` | booleaans | true |
| `strNotEqual2` | booleaans | onjuist |

## <a name="next-steps"></a>Volgende stappen

- Als u een Bicep-bestand wilt maken, raadpleegt u [zelf studie: het eerste Azure Resource Manager Bicep-bestand maken en implementeren](bicep-tutorial-create-first-bicep.md).
- Zie voor meer informatie over het oplossen van Bicep-type fouten [een functie voor Bicep](template-functions-any.md).
- Zie [JSON en Bicep voor sjablonen](compare-template-syntax.md)vergelijken als u de syntaxis voor BICEP en JSON wilt vergelijken.
- Zie [arm-sjabloon functies](template-functions.md)voor voor beelden van BICEP en arm-sjabloon functies.
