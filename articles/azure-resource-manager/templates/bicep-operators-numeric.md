---
title: Bicep numerieke Opera tors
description: Hierin worden Bicep numerieke Opera tors beschreven waarmee waarden worden berekend.
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: f2c7f722a3f13648c94b69039d31e5095a3256f7
ms.sourcegitcommit: c3739cb161a6f39a9c3d1666ba5ee946e62a7ac3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2021
ms.locfileid: "107211300"
---
# <a name="bicep-numeric-operators"></a>Bicep numerieke Opera tors

De numerieke Opera tors gebruiken gehele getallen om berekeningen uit te voeren en waarden van een geheel getal op te halen. Als u de voor beelden wilt uitvoeren, gebruikt u Azure CLI of Azure PowerShell voor het [implementeren van een Bicep-bestand](bicep-tutorial-create-first-bicep.md#deploy-bicep-file).

| Operator | Name |
| ---- | ---- |
| `*` | [Maal](#multiply-) |
| `/` | [Lijn](#divide-) |
| `%` | [Modulo](#modulo-) |
| `+` | [Add](#add-) |
| `-` | [Aftrekken](#subtract--) |
| `-` | [Verminderd](#minus--) |

> [!NOTE]
> Aftrekken en minus gebruiken dezelfde operator. De functionaliteit verschilt omdat aftrekken gebruikmaakt van twee operanden en minus een operand gebruikt.

## <a name="multiply-"></a>Maal

`operand1 * operand2`

Vermenigvuldigt twee gehele getallen.

### <a name="operands"></a>Operanden

| Mogelijke | Type | Description |
| ---- | ---- | ---- |
| `operand1`  | geheel getal | Getal om te vermenigvuldigen. |
| `operand2`  | geheel getal  | De vermenigvuldigings factor van het getal. |

### <a name="return-value"></a>Retourwaarde

De vermenigvuldiging retourneert het product als een geheel getal.

### <a name="example"></a>Voorbeeld

Twee gehele getallen worden vermenigvuldigd en het product geretourneerd.

```bicep
param firstInt int = 5
param secondInt int = 2

output product int = firstInt * secondInt
```

Uitvoer van het voor beeld:

| Naam | Type | Waarde |
| ---- | ---- | ---- |
| `product` | geheel getal | 10 |

## <a name="divide-"></a>Lijn

`operand1 / operand2`

Hiermee wordt een geheel getal gedeeld door een geheel getal.

### <a name="operands"></a>Operanden

| Mogelijke | Type | Description |
| ---- | ---- | ---- |
| `operand1` | geheel getal | Geheel getal dat is verdeeld. |
| `operand2` | geheel getal | Geheel getal dat wordt gebruikt voor de deling. Kan niet nul zijn. |

### <a name="return-value"></a>Retourwaarde

De deling retourneert het quotiënt als een geheel getal.

### <a name="example"></a>Voorbeeld

Twee gehele getallen worden gedeeld en retour neren het quotiënt.

```bicep
param firstInt int = 10
param secondInt int = 2

output quotient int = firstInt / secondInt
```

Uitvoer van het voor beeld:

| Naam | Type | Waarde |
| ---- | ---- | ---- |
| `quotient` | geheel getal | 5 |

## <a name="modulo-"></a>Modulo

`operand1 % operand2`

Hiermee wordt een geheel getal gedeeld door een geheel getal en wordt het restant geretourneerd.

### <a name="operands"></a>Operanden

| Mogelijke | Type | Description |
| ---- | ---- | ---- |
| `operand1`  | geheel getal  | Het gehele getal dat is verdeeld. |
| `operand2`  | geheel getal  | Het gehele getal dat wordt gebruikt voor de deling. Mag niet 0 zijn. |

### <a name="return-value"></a>Retourwaarde

De rest wordt geretourneerd als een geheel getal. Als de divisie geen rest produceert, wordt 0 geretourneerd.

### <a name="example"></a>Voorbeeld

Twee paren van gehele getallen zijn verdeeld en retour neren de resten.

```bicep
param firstInt int = 10
param secondInt int = 3

param thirdInt int = 8
param fourthInt int = 4

output remainder int = firstInt % secondInt
output zeroRemainder int = thirdInt % fourthInt
```

Uitvoer van het voor beeld:

| Naam | Type | Waarde |
| ---- | ---- | ---- |
| `remainder` | integer | 1 |
| `zeroRemainder` | integer | 0 |

## <a name="add-"></a>+ Toevoegen

`operand1 + operand2`

Voegt twee gehele getallen toe.

### <a name="operands"></a>Operanden

| Mogelijke | Type | Description |
| ---- | ---- | ---- |
| `operand1` | geheel getal | Nummer dat moet worden toegevoegd. |
| `operand2` | geheel getal | Nummer dat wordt toegevoegd aan een getal. |

### <a name="return-value"></a>Retourwaarde

De toevoeging retourneert de som als een geheel getal.

### <a name="example"></a>Voorbeeld

Er worden twee gehele getallen toegevoegd en de som geretourneerd.

```bicep
param firstInt int = 10
param secondInt int = 2

output sum int = firstInt + secondInt
```

Uitvoer van het voor beeld:

| Naam | Type | Waarde |
| ---- | ---- | ---- |
| `sum` | geheel getal | 12 |

## <a name="subtract--"></a>Aftrekken

`operand1 - operand2`

Trekt een geheel getal af van een geheel getal.

### <a name="operands"></a>Operanden

| Mogelijke | Type | Description |
| ---- | ---- | ---- |
| `operand1` | geheel getal | Een groter getal dat wordt afgetrokken van. |
| `operand2` | geheel getal | Getal dat wordt afgetrokken van het grotere getal. |

### <a name="return-value"></a>Retourwaarde

De aftrekking retourneert het verschil als een geheel getal.

### <a name="example"></a>Voorbeeld

Een geheel getal wordt afgetrokken en retourneert het verschil.

```bicep
param firstInt int = 10
param secondInt int = 4

output difference int = firstInt - secondInt
```

Uitvoer van het voor beeld:

| Naam | Type | Waarde |
| ---- | ---- | ---- |
| `difference` | geheel getal | 6 |

## <a name="minus--"></a>Verminderd

`-integerValue`

Vermenigvuldigt een geheel getal op `-1` .

### <a name="operand"></a>Mogelijke

| Mogelijke | Type | Description |
| ---- | ---- | ---- |
| `integerValue` | geheel getal | Geheel getal vermenigvuldigd met `-1` . |

### <a name="return-value"></a>Retourwaarde

Er wordt een geheel getal vermenigvuldigd door `-1` . Een positief geheel getal retourneert een negatief geheel getal en een negatief geheel getal retourneert een positief geheel getal. De waarden kunnen worden verpakt met haakjes.

### <a name="example"></a>Voorbeeld

```bicep
param posInt int = 10
param negInt int = -20

output startedPositive int = -posInt
output startedNegative int = -(negInt)
```

Uitvoer van het voor beeld:

| Naam | Type | Waarde |
| ---- | ---- | ---- |
| `startedPositive` | geheel getal | -10 |
| `startedNegative` | geheel getal | 20 |

## <a name="next-steps"></a>Volgende stappen

- Als u een Bicep-bestand wilt maken, raadpleegt u [zelf studie: het eerste Azure Resource Manager Bicep-bestand maken en implementeren](bicep-tutorial-create-first-bicep.md).
- Zie voor meer informatie over het oplossen van Bicep-type fouten [een functie voor Bicep](template-functions-any.md).
- Zie [JSON en Bicep voor sjablonen](compare-template-syntax.md)vergelijken als u de syntaxis voor BICEP en JSON wilt vergelijken.
- Zie [arm-sjabloon functies](template-functions.md)voor voor beelden van BICEP en arm-sjabloon functies.
