---
title: UI-definitie verzamelings functies maken
description: Hierin worden de functies beschreven die moeten worden gebruikt bij het werken met verzamelingen, zoals matrices en objecten.
author: tfitzmac
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: tomfitz
ms.openlocfilehash: 2a075c5c99f457681cd49e75014487bf9cca263c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87096963"
---
# <a name="createuidefinition-collection-functions"></a>CreateUiDefinition-verzamelings functies

Deze functies kunnen worden gebruikt in combi natie met verzamelingen, zoals JSON-teken reeksen, matrices en objecten.

## <a name="contains"></a>bevat

Retourneert `true` als een teken reeks de opgegeven subtekenreeks bevat, een matrix bevat de opgegeven waarde of een object de opgegeven sleutel bevat.

### <a name="example-string-contains"></a>Voor beeld: teken reeks bevat

In het volgende voor beeld wordt geretourneerd `true` :

```json
"[contains('webapp', 'web')]"
```

### <a name="example-array-contains"></a>Voor beeld: matrix bevat

`element1`Retourneert als resultaat `[1, 2, 3]` . In het volgende voor beeld wordt geretourneerd `false` :

```json
"[contains(steps('demoStep').element1, 4)]"
```

### <a name="example-object-contains"></a>Voor beeld: object bevat

`element1`Retourneert aannemen:

```json
{
  "key1": "Linux",
  "key2": "Windows"
}
```

In het volgende voor beeld wordt geretourneerd `true` :

```json
"[contains(steps('demoStep').element1, 'key1')]"
```

## <a name="empty"></a>leeg

Retourneert `true` of de teken reeks, matrix of object null of leeg is.

### <a name="example-string-empty"></a>Voor beeld: lege teken reeks

In het volgende voor beeld wordt geretourneerd `true` :

```json
"[empty('')]"
```

### <a name="example-array-empty"></a>Voor beeld: matrix leeg

`element1`Retourneert als resultaat `[1, 2, 3]` . In het volgende voor beeld wordt geretourneerd `false` :

```json
"[empty(steps('demoStep').element1)]"
```

### <a name="example-object-empty"></a>Voor beeld: object leeg

`element1`Retourneert aannemen:

```json
{
  "key1": "Linux",
  "key2": "Windows"
}
```

In het volgende voor beeld wordt geretourneerd `false` :

```json
"[empty(steps('demoStep').element1)]"
```

### <a name="example-null-and-undefined"></a>Voor beeld: null en niet-gedefinieerd

Hierbij wordt ervan uitgegaan dat het `element1` niet is `null` gedefinieerd. In het volgende voor beeld wordt geretourneerd `true` :

```json
"[empty(steps('demoStep').element1)]"
```

## <a name="filter"></a>filter

Retourneert een nieuwe matrix na het Toep assen van de filter logica die is gegeven als een Lambda-functie. De eerste para meter is de matrix die moet worden gebruikt voor het filteren. De tweede para meter is de Lambda-functie waarmee de filter logica wordt opgegeven.

In het volgende voor beeld wordt de matrix geretourneerd `[ { "name": "abc" } ]` .

```json
"[filter(parse('[{\"name\":\"abc\"},{\"name\":\"xyz\"}]'), (item) => contains(item.name, 'abc'))]"
```

## <a name="first"></a>instantie

Retourneert het eerste teken van de opgegeven teken reeks. de eerste waarde van de opgegeven matrix. of de eerste sleutel en waarde van het opgegeven object.

### <a name="example-string-first"></a>Voor beeld: teken reeks eerst

In het volgende voor beeld wordt geretourneerd `"c"` :

```json
"[first('contoso')]"
```

### <a name="example-array-first"></a>Voor beeld: matrix eerst

`element1`Retourneert als resultaat `[1, 2, 3]` . In het volgende voor beeld wordt geretourneerd `1` :

```json
"[first(steps('demoStep').element1)]"
```

#### <a name="example-object-first"></a>Voor beeld: object eerst

`element1`Retourneert aannemen:

```json
{
  "key1": "Linux",
  "key2": "Windows"
}
```

In het volgende voor beeld wordt geretourneerd `{"key1": "Linux"}` :

```json
"[first(steps('demoStep').element1)]"
```

## <a name="last"></a>duren

Retourneert het laatste teken van de opgegeven teken reeks, de laatste waarde van de opgegeven matrix of de laatste sleutel en waarde van het opgegeven object.

### <a name="example-string-last"></a>Voor beeld: teken reeks als laatste

In het volgende voor beeld wordt geretourneerd `"o"` :

```json
"[last('contoso')]"
```

### <a name="example-array-last"></a>Voor beeld: matrix laatst

`element1`Retourneert als resultaat `[1, 2, 3]` . In het volgende voor beeld wordt geretourneerd `3` :

```json
"[last(steps('demoStep').element1)]"
```

### <a name="example-object-last"></a>Voor beeld: object laatst

`element1`Retourneert aannemen:

```json
{
  "key1": "Linux",
  "key2": "Windows"
}
```

In het volgende voor beeld wordt geretourneerd `{"key2": "Windows"}` :

```json
"[last(steps('demoStep').element1)]"
```

## <a name="length"></a>lengte

Retourneert het aantal tekens in een teken reeks, het aantal waarden in een matrix of het aantal sleutels in een object.

### <a name="example-string-length"></a>Voor beeld: teken reeks lengte

In het volgende voor beeld wordt geretourneerd `7` :

```json
"[length('Contoso')]"
```

### <a name="example-array-length"></a>Voor beeld: matrix lengte

`element1`Retourneert als resultaat `[1, 2, 3]` . In het volgende voor beeld wordt geretourneerd `3` :

```json
"[length(steps('demoStep').element1)]"
```

### <a name="example-object-length"></a>Voor beeld: object lengte

`element1`Retourneert aannemen:

```json
{
  "key1": "Linux",
  "key2": "Windows"
}
```

In het volgende voor beeld wordt geretourneerd `2` :

```json
"[length(steps('demoStep').element1)]"
```

## <a name="map"></a>map

Retourneert een nieuwe matrix na het aanroepen van een Lambda-functie op een aangelegde matrix. De eerste para meter is de matrix die moet worden gebruikt voor de functie Lambda. De tweede para meter is de functie Lambda.

Het volgende voor beeld retourneert een nieuwe matrix waarbij elke waarde wordt verdubbeld. Het resultaat is `[2, 4, 6]` .

```json
"[map(parse('[1, 2, 3]'), (item) => mul(2, item))]"
```

In het volgende voor beeld wordt een nieuwe matrix geretourneerd `["abc", "xyz"]` .

```json
"[map(parse('[{\"name\":\"abc\"},{\"name\":\"xyz\"}]'), (item) => item.name)]"
```

## <a name="skip"></a>skip

Hiermee wordt een opgegeven aantal elementen in een verzameling omzeild en worden de resterende elementen geretourneerd.

### <a name="example-string-skip"></a>Voor beeld: teken reeks overs Laan

In het volgende voor beeld wordt geretourneerd `"app"` :

```json
"[skip('webapp', 3)]"
```

### <a name="example-array-skip"></a>Voor beeld: matrix overs Laan

`element1`Retourneert als resultaat `[1, 2, 3]` . In het volgende voor beeld wordt geretourneerd `[3]` :

```json
"[skip(steps('demoStep').element1, 2)]"
```

### <a name="example-object-skip"></a>Voor beeld: object overs Laan

`element1`Retourneert aannemen:

```json
{
  "key1": "Linux",
  "key2": "Windows"
}
```
In het volgende voor beeld wordt geretourneerd `{"key2": "Windows"}` :

```json
"[skip(steps('demoStep').element1, 1)]"
```

## <a name="split"></a>split

Retourneert een matrix met teken reeksen die de subtekenreeksen bevatten van de invoer die wordt gescheiden door het scheidings teken.

In het volgende voor beeld wordt de matrix geretourneerd `[ "555", "867", "5309" ]` .

```json
"[split('555-867-5309', '-')]"
```

## <a name="take"></a>take

Retourneert een opgegeven aantal aaneengesloten tekens vanaf het begin van de teken reeks, een opgegeven aantal aaneengesloten waarden vanaf het begin van de matrix of een opgegeven aantal aaneengesloten sleutels en waarden vanaf het begin van het object.

### <a name="example-string-take"></a>Voor beeld: teken reeks nemen

In het volgende voor beeld wordt geretourneerd `"web"` :

```json
"[take('webapp', 3)]"
```

### <a name="example-array-take"></a>Voor beeld: matrix nemen

`element1`Retourneert als resultaat `[1, 2, 3]` . In het volgende voor beeld wordt geretourneerd `[1, 2]` :

```json
"[take(steps('demoStep').element1, 2)]"
```

### <a name="example-object-take"></a>Voor beeld: object nemen

`element1`Retourneert aannemen:

```json
{
  "key1": "Linux",
  "key2": "Windows"
}
```

In het volgende voor beeld wordt geretourneerd `{"key1": "Linux"}` :

```json
"[take(steps('demoStep').element1, 1)]"
```

## <a name="next-steps"></a>Volgende stappen

* Zie [overzicht van Azure Resource Manager](../management/overview.md)voor een inleiding tot Azure Resource Manager.
