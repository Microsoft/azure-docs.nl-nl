---
title: Teken reeks functies voor UI-definitie maken
description: Beschrijft de teken reeks functies die moeten worden gebruikt bij het maken van UI-definities voor Azure Managed Applications
author: tfitzmac
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: tomfitz
ms.openlocfilehash: c662948542c36cd93f889ca045ee245c15c7bb11
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87096939"
---
# <a name="createuidefinition-string-functions"></a>CreateUiDefinition teken reeks functies

Deze functies voor gebruik met JSON-teken reeksen.

## <a name="concat"></a>concat

Voegt een of meer teken reeksen samen.

Als bijvoorbeeld de uitvoer waarde van `element1` if `"Contoso"` is, retourneert dit voor beeld de teken reeks `"Demo Contoso app"` :

```json
"[concat('Demo ', steps('step1').element1, ' app')]"
```

## <a name="endswith"></a>endsWith

Hiermee wordt bepaald of een teken reeks eindigt met een waarde.

Het volgende voor beeld retourneert ' True '.

```json
"[endsWith('tuvwxyz', 'xyz')]"
```

## <a name="guid"></a>guid

Hiermee wordt een globaal unieke teken reeks (GUID) gegenereerd.

In het volgende voor beeld wordt een waarde als resultaat gegeven `"c7bc8bdc-7252-4a82-ba53-7c468679a511"` :

```json
"[guid()]"
```

## <a name="indexof"></a>indexOf

Retourneert de eerste positie van een waarde binnen een teken reeks of-1 als deze niet is gevonden.

In het volgende voor beeld wordt 2 geretourneerd.

```json
"[indexOf('abcdef', 'cd')]"
```

## <a name="lastindexof"></a>lastIndexOf

Retourneert de laatste positie van een waarde in een teken reeks of-1 als deze niet is gevonden.

In het volgende voor beeld wordt 3 geretourneerd.

```json
"[lastIndexOf('test', 't')]"
```

## <a name="replace"></a>vervangen

Retourneert een teken reeks waarin alle instanties van de opgegeven teken reeks in de huidige teken reeks worden vervangen door een andere teken reeks.

In het volgende voor beeld wordt geretourneerd `"Contoso.com web app"` :

```json
"[replace('Contoso.net web app', '.net', '.com')]"
```

## <a name="startswith"></a>startsWith

Hiermee wordt bepaald of een teken reeks begint met een waarde.

Het volgende voor beeld retourneert ' True '.

```json
"[startsWith('abcdefg', 'ab')]"
```

## <a name="substring"></a>subtekenreeks

Retourneert de subtekenreeks van de opgegeven teken reeks. De subtekenreeks begint bij de opgegeven index en heeft de opgegeven lengte.

In het volgende voor beeld wordt geretourneerd `"web"` :

```json
"[substring('Contoso.com web app', 12, 3)]"
```

## <a name="tolower"></a>toLower

Retourneert een teken reeks die is geconverteerd naar kleine letters.

In het volgende voor beeld wordt geretourneerd `"contoso"` :

```json
"[toLower('CONTOSO')]"
```

## <a name="toupper"></a>toUpper

Retourneert een teken reeks die is geconverteerd naar hoofd letters.

In het volgende voor beeld wordt geretourneerd `"CONTOSO"` :

```json
"[toUpper('contoso')]"
```

## <a name="next-steps"></a>Volgende stappen

* Zie [overzicht van Azure Resource Manager](../management/overview.md)voor een inleiding tot Azure Resource Manager.

