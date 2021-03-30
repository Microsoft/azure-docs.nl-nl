---
title: UI-definitie datum functies maken
description: Hierin worden de functies beschreven die moeten worden gebruikt bij het werken met datum waarden.
author: tfitzmac
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: tomfitz
ms.openlocfilehash: 80484fd15e51bae7036c43bd3ca8fe8167387ede
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87096957"
---
# <a name="createuidefinition-date-functions"></a>CreateUiDefinition datum functies

De functies die moeten worden gebruikt bij het werken met datum waarden.

## <a name="addhours"></a>addHours

Voegt een integraal aantal uren toe aan de opgegeven tijds tempel.

In het volgende voor beeld wordt geretourneerd `"1991-01-01T00:59:59.000Z"` :

```json
"[addHours('1990-12-31T23:59:59Z', 1)]"
```

## <a name="addminutes"></a>addMinutes

Voegt een integraal aantal minuten toe aan de opgegeven tijds tempel.

In het volgende voor beeld wordt geretourneerd `"1991-01-01T00:00:59.000Z"` :

```json
"[addMinutes('1990-12-31T23:59:59Z', 1)]"
```

## <a name="addseconds"></a>addSeconds
Voegt een integraal aantal seconden toe aan de opgegeven tijds tempel.

In het volgende voor beeld wordt geretourneerd `"1991-01-01T00:00:00.000Z"` :

```json
"[addSeconds('1990-12-31T23:59:60Z', 1)]"
```

## <a name="utcnow"></a>utcNow

Retourneert een teken reeks in de ISO 8601-notatie van de huidige datum en tijd op de lokale computer.

Het volgende voor beeld kan resulteren in `"1990-12-31T23:59:59.000Z"` :

```json
"[utcNow()]"
```

## <a name="next-steps"></a>Volgende stappen

* Zie [overzicht van Azure Resource Manager](../management/overview.md)voor een inleiding tot Azure Resource Manager.
