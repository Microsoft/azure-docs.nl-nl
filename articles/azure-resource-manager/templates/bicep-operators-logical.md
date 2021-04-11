---
title: Bicep logische Opera tors
description: Hierin worden Bicep logische Opera tors beschreven waarmee voor waarden worden geëvalueerd.
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: 1eecbe589ecf08e32246dd97d137c60fa7b5078f
ms.sourcegitcommit: c3739cb161a6f39a9c3d1666ba5ee946e62a7ac3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2021
ms.locfileid: "107211311"
---
# <a name="bicep-logical-operators"></a>Bicep logische Opera tors

De logische Opera tors evalueren Boole-waarden, retour neren waarden die niet null zijn of evalueren een voorwaardelijke expressie. Als u de voor beelden wilt uitvoeren, gebruikt u Azure CLI of Azure PowerShell voor het [implementeren van een Bicep-bestand](bicep-tutorial-create-first-bicep.md#deploy-bicep-file).

| Operator | Name |
| ---- | ---- |
| `&&` | [Maar](#and-) |
| `||` | [Of](#or-) |
| `!` | [Ten](#not-) |
| `??` | [Voeg](#coalesce-) |
| `?` `:` | [Voorwaardelijke expressie](#conditional-expression--) |

## <a name="and-"></a>En &&

`operand1 && operand2`

Bepaalt of beide waarden waar zijn.

### <a name="operands"></a>Operanden

| Mogelijke | Type | Beschrijving |
| ---- | ---- | ---- |
| `operand1` | booleaans | De eerste waarde die moet worden gecontroleerd op waar. |
| `operand2` | booleaans | De tweede waarde die moet worden gecontroleerd op waar. |
| Meer operanden | booleaans | Meer operanden kunnen worden opgenomen. |

### <a name="return-value"></a>Retourwaarde

`True` Als beide waarden waar zijn, `false` wordt anders geretourneerd.

### <a name="example"></a>Voorbeeld

Evalueert een set parameter waarden en een set expressies.

```bicep
param operand1 bool = true
param operand2 bool = true

output andResultParm bool = operand1 && operand2
output andResultExp bool = bool(10 >= 10) && bool(5 > 2)
```

Uitvoer van het voor beeld:

| Naam | Type | Waarde |
| ---- | ---- | ---- |
| `andResultParm` | booleaans | true |
| `andResultExp` | booleaans | true |

## <a name="or-"></a>Of | |

`operand1 || operand2`

Bepaalt of een van beide waarden waar is.

### <a name="operands"></a>Operanden

| Mogelijke | Type | Beschrijving |
| ---- | ---- | ---- |
| `operand1` | booleaans | De eerste waarde die moet worden gecontroleerd op waar. |
| `operand2` | booleaans | De tweede waarde die moet worden gecontroleerd op waar. |
| Meer operanden | booleaans | Meer operanden kunnen worden opgenomen. |

### <a name="return-value"></a>Retourwaarde

`True` Als een van beide waarden waar is, `false` wordt anders geretourneerd.

### <a name="example"></a>Voorbeeld

Evalueert een set parameter waarden en een set expressies.

```bicep
param operand1 bool = true
param operand2 bool = false

output orResultParm bool = operand1 || operand2
output orResultExp bool = bool(10 >= 10) || bool(5 < 2)
```

Uitvoer van het voor beeld:

| Naam | Type | Waarde |
| ---- | ---- | ---- |
| `orResultParm` | booleaans | true |
| `orResultExp` | booleaans | true |

## <a name="not-"></a>Ten!

`!boolValue`

Een Booleaanse waarde wordt genegeerd.

### <a name="operand"></a>Mogelijke

| Mogelijke | Type | Beschrijving |
| ---- | ---- | ---- |
| `boolValue` | booleaans | Booleaanse waarde die wordt genegeerd. |

### <a name="return-value"></a>Retourwaarde

De aanvankelijke waarde wordt genegeerd en er wordt een Boolean geretourneerd. Als de aanvankelijke waarde is `true` , `false` wordt geretourneerd.

### <a name="example"></a>Voorbeeld

De `not` operator wordt een waarde genegeerd. De waarden kunnen worden verpakt met haakjes.

```bicep
param initTrue bool = true
param initFalse bool = false

output startedTrue bool = !(initTrue)
output startedFalse bool = !initFalse
```

Uitvoer van het voor beeld:

| Naam | Type | Waarde |
| ---- | ---- | ---- |
| `startedTrue` | booleaans | onjuist |
| `startedFalse` | booleaans | true |

## <a name="coalesce-"></a>Coalesce?

`operand1 ?? operand2`

Retourneert de eerste waarde die niet null is van operands.

### <a name="operands"></a>Operanden

| Mogelijke | Type | Description |
| ---- | ---- | ---- |
| `operand1` | teken reeks, geheel getal, Booleaanse waarde, object, matrix | Waarde waarvoor moet worden getest `null` . |
| `operand2` | teken reeks, geheel getal, Booleaanse waarde, object, matrix | Waarde waarvoor moet worden getest `null` . |
| Meer operanden | teken reeks, geheel getal, Booleaanse waarde, object, matrix | Waarde waarvoor moet worden getest `null` . |

### <a name="return-value"></a>Retourwaarde

Retourneert de eerste waarde die niet null is. Lege teken reeksen, lege matrices en lege objecten zijn niet `null` en er \<empty> wordt een waarde geretourneerd.

### <a name="example"></a>Voorbeeld

De output-instructies retour neren waarden die niet null zijn. Het uitvoer type moet overeenkomen met het type in de vergelijking, anders wordt er een fout gegenereerd.

```bicep
param myObject object = {
  'isnull1': null
  'isnull2': null
  'string': 'demoString'
  'emptystr': ''
  'integer': 10
  }

output nonNullStr string = myObject.isnull1 ?? myObject.string ?? myObject.isnull2
output nonNullInt int = myObject.isnull1 ?? myObject.integer ?? myObject.isnull2
output nonNullEmpty string = myObject.isnull1 ?? myObject.emptystr ?? myObject.string ?? myObject.isnull2
```

Uitvoer van het voor beeld:

| Naam | Type | Waarde |
| ---- | ---- | ---- |
| `nonNullStr` | tekenreeks | demoString |
| `nonNullInt` | int | 10 |
| `nonNullEmpty` | tekenreeks | \<empty> |

## <a name="conditional-expression--"></a>Voorwaardelijke expressie? :

`condition ? true-value : false-value`

Evalueert een voor waarde en retourneert een waarde of de voor waarde waar of onwaar is.

### <a name="operands"></a>Operanden

| Mogelijke | Type | Beschrijving |
| ---- | ---- | ---- |
| `condition` | booleaans | De voor waarde die moet worden geëvalueerd als waar of onwaar. |
| `true-value` | teken reeks, geheel getal, Booleaanse waarde, object, matrix | Waarde wanneer voor waarde waar is. |
| `false-value` | teken reeks, geheel getal, Booleaanse waarde, object, matrix | Waarde wanneer de voor waarde ONWAAR is. |

### <a name="example"></a>Voorbeeld

In dit voor beeld wordt de eerste para meter geëvalueerd en wordt een waarde geretourneerd, ongeacht of de voor waarde waar of onwaar is.

```bicep
param initValue bool = true

output outValue string = initValue ? 'true value' : 'false value'
```

Uitvoer van het voor beeld:

| Naam | Type | Waarde |
| ---- | ---- | ---- |
| `outValue` | tekenreeks | waarde waar |

## <a name="next-steps"></a>Volgende stappen

- Als u een Bicep-bestand wilt maken, raadpleegt u [zelf studie: het eerste Azure Resource Manager Bicep-bestand maken en implementeren](bicep-tutorial-create-first-bicep.md).
- Zie voor meer informatie over het oplossen van Bicep-type fouten [een functie voor Bicep](template-functions-any.md).
- Zie [JSON en Bicep voor sjablonen](compare-template-syntax.md)vergelijken als u de syntaxis voor BICEP en JSON wilt vergelijken.
- Zie [arm-sjabloon functies](template-functions.md)voor voor beelden van BICEP en arm-sjabloon functies.
