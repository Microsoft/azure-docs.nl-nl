---
title: Bicep-Opera tors
description: Hierin worden de Bicep-Opera tors beschreven die beschikbaar zijn voor Azure Resource Manager-implementaties.
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: 4bf1005a11b1dcfea9f4b28d6bd3fa7c33e3278f
ms.sourcegitcommit: c3739cb161a6f39a9c3d1666ba5ee946e62a7ac3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2021
ms.locfileid: "107211308"
---
# <a name="bicep-operators"></a>Bicep-Opera tors

In dit artikel worden de Bicep-Opera tors beschreven die beschikbaar zijn wanneer u een Bicep-sjabloon maakt en Azure Resource Manager gebruikt voor het implementeren van resources. Opera tors worden gebruikt om waarden te berekenen, waarden te vergelijken of voor waarden te evalueren. Er zijn drie typen Bicep-Opera tors: [vergelijking](#comparison), [logisch](#logical)en [Numeriek](#numeric).

## <a name="comparison"></a>Vergelijking

De vergelijkings operators vergelijken waarden en retour neren `true` of `false` .

| Operator | Naam | Beschrijving |
| ---- | ---- | ---- |
| `>=` | [Groter dan of gelijk aan](bicep-operators-comparison.md#greater-than-or-equal-) | Evalueert of de eerste waarde groter is dan of gelijk is aan de tweede waarde. |
| `>`  | [Groter dan](bicep-operators-comparison.md#greater-than-) | Evalueert of de eerste waarde groter is dan de tweede waarde. |
| `<=` | [Kleiner dan of gelijk aan](bicep-operators-comparison.md#less-than-or-equal-) | Evalueert of de eerste waarde kleiner is dan of gelijk is aan de tweede waarde. |
| `<`  | [Kleiner dan](bicep-operators-comparison.md#less-than-) | Evalueert of de eerste waarde lager is dan de tweede waarde. |
| `==` | [Gelijk aan](bicep-operators-comparison.md#equals-) | Evalueert of twee waarden gelijk zijn. |
| `!=` | [Is niet gelijk aan](bicep-operators-comparison.md#not-equal-) | Evalueert of twee waarden **niet** gelijk zijn. |
| `=~` | [Gelijk aan niet-hoofdletter gevoelig](bicep-operators-comparison.md#equal-case-insensitive-) | Negeert case om te bepalen of twee waarden gelijk zijn. |
| `!~` | [Niet gelijk aan hoofdletter gevoelig](bicep-operators-comparison.md#not-equal-case-insensitive-) | Negeert case om te bepalen of twee waarden **niet** gelijk zijn. |

## <a name="logical"></a>Logisch

De logische Opera tors evalueren Boole-waarden, retour neren waarden die niet null zijn of evalueren een voorwaardelijke expressie.

| Operator | Naam | Beschrijving |
| ---- | ---- | ---- |
| `&&` | [Maar](bicep-operators-logical.md#and-) | Retourneert `true` of alle waarden waar zijn. |
| `||`| [Of](bicep-operators-logical.md#or-) | Retourneert `true` als een van beide waarden waar is. |
| `!` | [Ten](bicep-operators-logical.md#not-) | Een Booleaanse waarde wordt genegeerd. |
| `??` | [Voeg](bicep-operators-logical.md#coalesce-) | Retourneert de eerste waarde die niet null is. |
| `?` `:` | [Voorwaardelijke expressie](bicep-operators-logical.md#conditional-expression--) | Evalueert een voor waarde voor True of False en retourneert een waarde. |

## <a name="numeric"></a>Numeriek

De numerieke Opera tors gebruiken gehele getallen om berekeningen uit te voeren en waarden van een geheel getal op te halen.

| Operator | Naam | Beschrijving |
| ---- | ---- | ---- |
| `*` | [Maal](bicep-operators-numeric.md#multiply-) | Vermenigvuldigt twee gehele getallen. |
| `/` | [Lijn](bicep-operators-numeric.md#divide-) | Hiermee wordt een geheel getal gedeeld door een geheel getal. |
| `%` | [Modulo](bicep-operators-numeric.md#modulo-) | Hiermee wordt een geheel getal gedeeld door een geheel getal en wordt het restant geretourneerd. |
| `+` | [Add](bicep-operators-numeric.md#add-) | Voegt twee gehele getallen toe. |
| `-` | [Aftrekken](bicep-operators-numeric.md#subtract--) | Trekt een geheel getal af van een geheel getal. |
| `-` | [Verminderd](bicep-operators-numeric.md#minus--) | Vermenigvuldigt een geheel getal op `-1` . |

> [!NOTE]
> Aftrekken en minus gebruiken dezelfde operator. De functionaliteit verschilt omdat aftrekken gebruikmaakt van twee operanden en minus een operand gebruikt.

## <a name="next-steps"></a>Volgende stappen

- Als u een Bicep-bestand wilt maken, raadpleegt u [zelf studie: het eerste Azure Resource Manager Bicep-bestand maken en implementeren](bicep-tutorial-create-first-bicep.md).
- Zie voor meer informatie over het oplossen van Bicep-type fouten [een functie voor Bicep](template-functions-any.md).
- Zie [JSON en Bicep voor sjablonen](compare-template-syntax.md)vergelijken als u de syntaxis voor BICEP en JSON wilt vergelijken.
- Zie [arm-sjabloon functies](template-functions.md)voor voor beelden van BICEP en arm-sjabloon functies.
