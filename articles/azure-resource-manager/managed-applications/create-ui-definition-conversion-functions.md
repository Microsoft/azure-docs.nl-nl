---
title: Conversie functies voor de UI-definitie maken
description: Hierin worden de functies beschreven die moeten worden gebruikt bij het converteren van waarden tussen gegevens typen en code ringen.
author: tfitzmac
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: tomfitz
ms.openlocfilehash: b69cd35b27b343da08727b4c4ee9b4fd025e1df7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87096960"
---
# <a name="createuidefinition-conversion-functions"></a>CreateUiDefinition-conversie functies

Deze functies kunnen worden gebruikt om waarden te converteren tussen JSON-gegevens typen en-code ringen.

## <a name="bool"></a>booleaans

Zet de para meter om in een Boole-waarde. Deze functie biedt ondersteuning voor para meters van het type Number, String en Boolean. Vergelijkbaar met Boole-waarden in Java script, een wille keurige waarde, behalve `0` of `'false'` als resultaat `true` .

In het volgende voor beeld wordt geretourneerd `true` :

```json
"[bool(1)]"
```

In het volgende voor beeld wordt geretourneerd `false` :

```json
"[bool(0)]"
```

In het volgende voor beeld wordt geretourneerd `true` :

```json
"[bool(true)]"
```

In het volgende voor beeld wordt geretourneerd `true` :

```json
"[bool('true')]"
```

## <a name="decodebase64"></a>decodeBase64

Decodeert de para meter uit een gecodeerde base-64-teken reeks. Deze functie ondersteunt alleen para meters van het type String.

In het volgende voor beeld wordt geretourneerd `"Contoso"` :

```json
"[decodeBase64('Q29udG9zbw==')]"
```

## <a name="decodeuricomponent"></a>decodeUriComponent

Decodeert de para meter van een URL-gecodeerde teken reeks. Deze functie ondersteunt alleen para meters van het type String.

In het volgende voor beeld wordt geretourneerd `"https://portal.azure.com/"` :

```json
"[decodeUriComponent('https%3A%2F%2Fportal.azure.com%2F')]"
```

## <a name="encodebase64"></a>encodeBase64

Codeert de para meter naar een gecodeerde base-64-teken reeks. Deze functie ondersteunt alleen para meters van het type String.

In het volgende voor beeld wordt geretourneerd `"Q29udG9zbw=="` :

```json
"[encodeBase64('Contoso')]"
```

## <a name="encodeuricomponent"></a>encodeUriComponent

Codeert de para meter naar een URL-gecodeerde teken reeks. Deze functie ondersteunt alleen para meters van het type String.

In het volgende voor beeld wordt geretourneerd `"https%3A%2F%2Fportal.azure.com%2F"` :

```json
"[encodeUriComponent('https://portal.azure.com/')]"
```

## <a name="float"></a>float

Zet de para meter om in een drijvende komma. Deze functie ondersteunt para meters van het type nummer en de teken reeks.

In het volgende voor beeld wordt geretourneerd `1.0` :

```json
"[float('1.0')]"
```

In het volgende voor beeld wordt geretourneerd `2.9` :

```json
"[float(2.9)]"
```

## <a name="int"></a>int

Zet de para meter om in een geheel getal. Deze functie ondersteunt para meters van het type nummer en de teken reeks.

In het volgende voor beeld wordt geretourneerd `1` :

```json
"[int('1')]"
```

In het volgende voor beeld wordt geretourneerd `2` :

```json
"[int(2.9)]"
```

## <a name="parse"></a>parse

Hiermee converteert u de para meter naar een systeem eigen type. Met andere woorden, deze functie is het omgekeerde van `string()` . Deze functie ondersteunt alleen para meters van het type String.

In het volgende voor beeld wordt geretourneerd `1` :

```json
"[parse('1')]"
```

In het volgende voor beeld wordt geretourneerd `true` :

```json
"[parse('true')]"
```

In het volgende voor beeld wordt geretourneerd `[1,2,3]` :

```json
"[parse('[1,2,3]')]"
```

In het volgende voor beeld wordt geretourneerd `{"type":"webapp"}` :

```json
"[parse('{\"type\":\"webapp\"}')]"
```

## <a name="string"></a>tekenreeks

Zet de para meter om in een teken reeks. Deze functie biedt ondersteuning voor para meters van alle JSON-gegevens typen.

In het volgende voor beeld wordt geretourneerd `"1"` :

```json
"[string(1)]"
```

In het volgende voor beeld wordt geretourneerd `"2.9"` :

```json
"[string(2.9)]"
```

In het volgende voor beeld wordt geretourneerd `"[1,2,3]"` :

```json
"[string([1,2,3])]"
```

In het volgende voor beeld wordt geretourneerd `"{"type":"webapp"}"` :

```json
"[string({\"type\":\"webapp\"})]"
```

## <a name="next-steps"></a>Volgende stappen

* Zie [overzicht van Azure Resource Manager](../management/overview.md)voor een inleiding tot Azure Resource Manager.
