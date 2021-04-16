---
title: Bicep-operators
description: Beschrijft de Bicep-operators die beschikbaar zijn Azure Resource Manager implementaties.
ms.topic: conceptual
ms.date: 04/15/2021
ms.openlocfilehash: 0838ebf6bc03f4237ef76e07f1eb6f25aa996fc0
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107537826"
---
# <a name="bicep-operators"></a>Bicep-operators

In dit artikel worden de Bicep-operators beschreven die beschikbaar zijn wanneer u een Bicep-sjabloon maakt en Azure Resource Manager resources implementeert. Operators worden gebruikt om waarden te berekenen, waarden te vergelijken of voorwaarden te evalueren. Er zijn drie typen Bicep-operators:

- [Vergelijking](#comparison)
- [Logische](#logical)
- [Numerieke](#numeric)

Als u een expressie tussen en omsluit, kunt u de standaardvoorwaarde van de `(` `)` Bicep-operator overschrijven. De expressie x + y/z evalueert bijvoorbeeld eerst de deling en vervolgens de optelling. De expressie (x + y) / z evalueert echter de optelling eerste en tweede deling.

## <a name="comparison"></a>Vergelijking

De vergelijkingsoperators vergelijken waarden en retourneren `true` of `false` .

| Operator | Naam | Beschrijving |
| ---- | ---- | ---- |
| `>=` | [Groter dan of gelijk aan](bicep-operators-comparison.md#greater-than-or-equal-) | Evalueert of de eerste waarde groter is dan of gelijk is aan de tweede waarde. |
| `>`  | [Groter dan](bicep-operators-comparison.md#greater-than-) | Evalueert of de eerste waarde groter is dan de tweede waarde. |
| `<=` | [Kleiner dan of gelijk aan](bicep-operators-comparison.md#less-than-or-equal-) | Evalueert of de eerste waarde kleiner is dan of gelijk is aan de tweede waarde. |
| `<`  | [Kleiner dan](bicep-operators-comparison.md#less-than-) | Evalueert of de eerste waarde kleiner is dan de tweede waarde. |
| `==` | [Gelijk aan](bicep-operators-comparison.md#equals-) | Evalueert of twee waarden gelijk zijn. |
| `!=` | [Is niet gelijk aan](bicep-operators-comparison.md#not-equal-) | Evalueert of twee waarden niet **gelijk** zijn. |
| `=~` | [Gelijk aan niet-casegevoelig](bicep-operators-comparison.md#equal-case-insensitive-) | Negeert de case om te bepalen of twee waarden gelijk zijn. |
| `!~` | [Niet gelijk aan niet-case-niet-gevoelig](bicep-operators-comparison.md#not-equal-case-insensitive-) | Negeert de case om te bepalen of twee waarden **niet gelijk** zijn. |

## <a name="logical"></a>Logisch

De logische operators evalueren Booleaanse waarden, retourneren niet-null-waarden of evalueren een voorwaardelijke expressie.

| Operator | Naam | Beschrijving |
| ---- | ---- | ---- |
| `&&` | [En](bicep-operators-logical.md#and-) | `true`Retourneert als alle waarden waar zijn. |
| `||`| [Of](bicep-operators-logical.md#or-) | `true`Retourneert als een van beide waarden waar is. |
| `!` | [Niet](bicep-operators-logical.md#not-) | Negeert een Booleaanse waarde. |
| `??` | [Sameneen](bicep-operators-logical.md#coalesce-) | Retourneert de eerste niet-null-waarde. |
| `?` `:` | [Voorwaardelijke expressie](bicep-operators-logical.md#conditional-expression--) | Evalueert een voorwaarde op waar of onwaar en retourneert een waarde. |

## <a name="numeric"></a>Numeriek

De numerieke operators gebruiken gehele getallen om berekeningen uit te voeren en waarden van gehele getallen te retourneren.

| Operator | Naam | Beschrijving |
| ---- | ---- | ---- |
| `*` | [Vermenigvuldigen](bicep-operators-numeric.md#multiply-) | Vermenigvuldigt twee gehele getallen. |
| `/` | [Verdelen](bicep-operators-numeric.md#divide-) | Deelt een geheel getal door een geheel getal. |
| `%` | [Modulo](bicep-operators-numeric.md#modulo-) | Deelt een geheel getal door een geheel getal en retourneert de rest. |
| `+` | [Add](bicep-operators-numeric.md#add-) | Voegt twee gehele getallen toe. |
| `-` | [Aftrekken](bicep-operators-numeric.md#subtract--) | Trekt een geheel getal af van een geheel getal. |
| `-` | [Minus](bicep-operators-numeric.md#minus--) | Vermenigvuldigt een geheel getal met `-1` . |

> [!NOTE]
> Gebruik dezelfde operator voor Aftrekken en min. De functionaliteit is anders omdat voor aftrekken twee operanden worden gebruikt en min één operand.

## <a name="next-steps"></a>Volgende stappen

- Zie Tutorial: Create and deploy first Azure Resource Manager Bicep file (Zelfstudie: het eerste bestand maken en implementeren) Azure Resource Manager Bicep-bestand om een [Bicep-bestand te maken.](bicep-tutorial-create-first-bicep.md)
- Zie Any function for Bicep (Elke functie voor Bicep) voor meer informatie over het oplossen van [bicep-typefouten.](template-functions-any.md)
- Zie JSON en Bicep vergelijken voor sjablonen om de syntaxis voor Bicep en [JSON te vergelijken.](compare-template-syntax.md)
- Zie ARM-sjabloonfuncties voor voorbeelden van Bicep- en [ARM-sjabloonfuncties.](template-functions.md)
