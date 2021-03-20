---
title: Wiskundige functies voor UI-definitie maken
description: Hierin worden de functies beschreven die moeten worden gebruikt voor het uitvoeren van reken kundige bewerkingen.
author: tfitzmac
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: tomfitz
ms.openlocfilehash: 022e59d847a4d89b4243223637fc6fd1e73255a9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87096945"
---
# <a name="createuidefinition-math-functions"></a>Wiskundige functies van CreateUiDefinition

Met de functies kunt u wiskundige bewerkingen uitvoeren.

## <a name="add"></a>add

Telt twee getallen op en retourneert het resultaat.

In het volgende voor beeld wordt geretourneerd `3` :

```json
"[add(1, 2)]"
```

## <a name="ceil"></a>ceil

Retourneert het grootste gehele getal dat groter is dan of gelijk is aan het opgegeven getal.

In het volgende voor beeld wordt geretourneerd `4` :

```json
"[ceil(3.14)]"
```

## <a name="div"></a>div

Deelt het eerste getal door het tweede getal en retourneert het resultaat. Het resultaat is altijd een geheel getal.

In het volgende voor beeld wordt geretourneerd `2` :

```json
"[div(6, 3)]"
```

## <a name="floor"></a>Floor

Retourneert de grootste integer die kleiner dan of gelijk aan het opgegeven getal is.

In het volgende voor beeld wordt geretourneerd `3` :

```json
"[floor(3.14)]"
```

## <a name="max"></a>max

Retourneert de grootste van de twee getallen.

In het volgende voor beeld wordt geretourneerd `2` :

```json
"[max(1, 2)]"
```

## <a name="min"></a>min.

Retourneert de kleinste van de twee getallen.

In het volgende voor beeld wordt geretourneerd `1` :

```json
"[min(1, 2)]"
```

## <a name="mod"></a>mod

Deelt het eerste getal door het tweede getal en retourneert de rest.

In het volgende voor beeld wordt geretourneerd `0` :

```json
"[mod(6, 3)]"
```

In het volgende voor beeld wordt geretourneerd `2` :

```json
"[mod(6, 4)]"
```

## <a name="mul"></a>mul

Vermenigvuldigt twee getallen en retourneert het resultaat.

In het volgende voor beeld wordt geretourneerd `6` :

```json
"[mul(2, 3)]"
```

## <a name="rand"></a>ASELECT

Retourneert een wille keurig integraal getal binnen het opgegeven bereik. Met deze functie worden geen cryptografisch beveiligde wille keurige getallen gegenereerd.

Het volgende voor beeld kan resulteren in `42` :

```json
"[rand(-100, 100)]"
```

## <a name="range"></a>bereik

Hiermee wordt een reeks integraal cijfers binnen het opgegeven bereik gegenereerd.

In het volgende voor beeld wordt geretourneerd `[1,2,3]` :

```json
"[range(1, 3)]"
```

## <a name="sub"></a>sub

Trekt het tweede getal af van het eerste getal en retourneert het resultaat.

In het volgende voor beeld wordt geretourneerd `1` :

```json
"[sub(3, 2)]"
```

## <a name="next-steps"></a>Volgende stappen

* Zie [overzicht van Azure Resource Manager](../management/overview.md)voor een inleiding tot Azure Resource Manager.
