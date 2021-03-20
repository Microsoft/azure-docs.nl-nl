---
title: Logische functies voor de definitie van de gebruikers interface maken
description: Hierin worden de functies beschreven om logische bewerkingen uit te voeren.
author: tfitzmac
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: tomfitz
ms.openlocfilehash: 00d2f0eeb5d353c8ebd7ad30f6866f890d6cb42e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87096951"
---
# <a name="createuidefinition-logical-functions"></a>Logische CreateUiDefinition-functies

Deze functies kunnen worden gebruikt in voorwaardelijke expressies. Sommige functies bieden mogelijk geen ondersteuning voor alle JSON-gegevens typen.

## <a name="and"></a>en

Retourneert `true` of alle para meters worden geëvalueerd `true` . Deze functie biedt ondersteuning voor twee of meer para meters van het type Boolean.

In het volgende voor beeld wordt geretourneerd `true` :

```json
"[and(equals(0, 0), equals('web', 'web'), less(1, 2))]"
```

In het volgende voor beeld wordt geretourneerd `false` :

```json
"[and(equals(0, 0), greater(1, 2))]"
```

## <a name="coalesce"></a>Voeg

Retourneert de waarde van de eerste para meter die niet null is. Deze functie ondersteunt alle JSON-gegevens typen.

Aannemen dat `element1` en niet `element2` zijn gedefinieerd. In het volgende voor beeld wordt geretourneerd `"Contoso"` :

```json
"[coalesce(steps('demoStep').element1, steps('demoStep').element2, 'Contoso')]"
```

Deze functie is vooral nuttig in de context van een optionele aanroep die plaatsvindt als gevolg van de gebruikers actie nadat de pagina is geladen. Een voor beeld is als de beperkingen voor één veld in de gebruikers interface afhankelijk zijn van de momenteel geselecteerde waarde van een andere, **niet-zicht bare** veld. In dit geval `coalesce()` kan de functie worden gebruikt om de syntaxis geldig te maken op het moment dat de pagina wordt geladen en het gewenste effect heeft wanneer de gebruiker met het veld communiceert.

Bekijk dit `DropDown` , waarmee de gebruiker uit verschillende soorten data bases kan kiezen:

```
{
    "name": "databaseType",
    "type": "Microsoft.Common.DropDown",
    "label": "Choose database type",
    "toolTip": "Choose database type",
    "defaultValue": "Oracle Database",
    "visible": "[bool(steps('section_database').connectToDatabase)]"
    "constraints": {
        "allowedValues": [
            {
                "label": "Azure Database for PostgreSQL",
                "value": "postgresql"
            },
            {
                "label": "Oracle Database",
                "value": "oracle"
            },
            {
                "label": "Azure SQL",
                "value": "sqlserver"
            }
        ],
        "required": true
    },
```

Als voor waarde voor de actie van een ander veld op de huidige gekozen waarde van dit veld, gebruikt u `coalesce()` , zoals hier wordt weer gegeven:

```
"regex": "[concat('^jdbc:', coalesce(steps('section_database').databaseConnectionInfo.databaseType, ''), '.*$')]",
```

Dit is nodig omdat de `databaseType` is in eerste instantie niet zichtbaar is en daarom geen waarde heeft. Dit zorgt ervoor dat de volledige expressie niet correct wordt geëvalueerd.

## <a name="equals"></a>is gelijk aan

Retourneert `true` als beide para meters hetzelfde type en dezelfde waarde hebben. Deze functie ondersteunt alle JSON-gegevens typen.

In het volgende voor beeld wordt geretourneerd `true` :

```json
"[equals(0, 0)]"
```

In het volgende voor beeld wordt geretourneerd `true` :

```json
"[equals('web', 'web')]"
```

In het volgende voor beeld wordt geretourneerd `false` :

```json
"[equals('abc', ['a', 'b', 'c'])]"
```

## <a name="greater"></a>greater

Retourneert `true` als de eerste para meter strikt groter is dan de tweede para meter. Deze functie ondersteunt alleen para meters van het type Number en string.

In het volgende voor beeld wordt geretourneerd `false` :

```json
"[greater(1, 2)]"
```

In het volgende voor beeld wordt geretourneerd `true` :

```json
"[greater('9', '10')]"
```

## <a name="greaterorequals"></a>greaterOrEquals

Retourneert `true` als de eerste para meter groter is dan of gelijk is aan de tweede para meter. Deze functie ondersteunt alleen para meters van het type Number en string.

In het volgende voor beeld wordt geretourneerd `true` :

```json
"[greaterOrEquals(2, 2)]"
```

## <a name="if"></a>if

Retourneert een waarde op basis van het feit of een voor waarde waar of onwaar is. De eerste para meter is de voor waarde die moet worden getest. De tweede para meter is de waarde die moet worden geretourneerd als de voor waarde waar is. De derde para meter is de waarde die moet worden geretourneerd als de voor waarde ONWAAR is.

In het volgende voor beeld wordt geretourneerd `yes` .

```json
"[if(equals(42, mul(6, 7)), 'yes', 'no')]"
```

## <a name="less"></a>less

Retourneert `true` als de eerste para meter strikt kleiner is dan de tweede para meter. Deze functie ondersteunt alleen para meters van het type Number en string.

In het volgende voor beeld wordt geretourneerd `true` :

```json
"[less(1, 2)]"
```

In het volgende voor beeld wordt geretourneerd `false` :

```json
"[less('9', '10')]"
```

## <a name="lessorequals"></a>lessOrEquals

Retourneert `true` als de eerste para meter kleiner is dan of gelijk is aan de tweede para meter. Deze functie ondersteunt alleen para meters van het type Number en string.

In het volgende voor beeld wordt geretourneerd `true` :

```json
"[lessOrEquals(2, 2)]"
```

## <a name="not"></a>not

Retourneert `true` of de para meter resulteert in `false` . Deze functie ondersteunt alleen para meters van het type Boolean.

In het volgende voor beeld wordt geretourneerd `true` :

```json
"[not(false)]"
```

In het volgende voor beeld wordt geretourneerd `false` :

```json
"[not(equals(0, 0))]"
```

## <a name="or"></a>of

Retourneert `true` of ten minste één van de para meters wordt geëvalueerd `true` . Deze functie biedt ondersteuning voor twee of meer para meters van het type Boolean.

In het volgende voor beeld wordt geretourneerd `true` :

```json
"[or(equals(0, 0), equals('web', 'web'), less(1, 2))]"
```

In het volgende voor beeld wordt geretourneerd `true` :

```json
"[or(equals(0, 0), greater(1, 2))]"
```

## <a name="next-steps"></a>Volgende stappen

* Zie [overzicht van Azure Resource Manager](../management/overview.md)voor een inleiding tot Azure Resource Manager.
