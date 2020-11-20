---
title: StylesObject voor dynamische Azure Maps
description: Naslag Gids voor het JSON-schema en de syntaxis voor de StylesObject die worden gebruikt voor het maken van de dynamische Azure Maps.
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/19/2020
ms.topic: reference
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 4284956138002d209ab0934cdd052748ef8aab78
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94966272"
---
# <a name="stylesobject-schema-reference-guide-for-dynamic-maps"></a>Naslag Gids voor StylesObject-Schema's voor dynamische kaarten

 De `StylesObject` is een `StyleObject` matrix die de statusset-stijlen weergeeft. Gebruik de Azure Maps [functie status](/rest/api/maps/featurestate) van de maker om uw stateset-stijlen toe te passen op functies van de kaart gegevens. Wanneer u de statusset-stijlen hebt gemaakt en deze hebt gekoppeld aan de functie functies van een binnenste kaart, kunt u deze gebruiken om dynamische kaarten voor binnenste toe te voegen. Voor meer informatie over het maken van dynamische kaarten, Zie [dynamische opmaak voor de plattegronden van de maker binnen implementeren](indoor-map-dynamic-styling.md).

## <a name="styleobject"></a>StyleObject

A `StyleObject` is een [`BooleanTypeStyleRule`](#booleantypestylerule) of een [`NumericTypeStyleRule`](#numerictypestylerule) .

In de JSON hieronder ziet u een `BooleanTypeStyleRule` benoemde `occupied` en `NumericTypeStyleRule` benoemde `temperature` .

```json
 "styles": [
     {
        "keyname": "occupied",
        "type": "boolean",
        "rules": [
            {
                "true": "#FF0000",
                "false": "#00FF00"
            }
        ]
    },
    {
        "keyname": "temperature",
        "type": "number",
        "rules": [
             {
                "range": {
                "minimum": 50,
                "exclusiveMaximum": 70
                },
                "color": "#343deb"
            },
            {
                "range": {
                "maximum": 70,
                "exclusiveMinimum": 30
              },
              "color": "#eba834"
            }
        ]
    }
]
```

## <a name="numerictypestylerule"></a>NumericTypeStyleRule

 A `NumericTypeStyleRule` is een  [`StyleObject`](#styleobject) en bestaat uit de volgende eigenschappen:

| Eigenschap | Type | Beschrijving | Vereist |
|-----------|----------|-------------|-------------|
| `keyName` | tekenreeks | De *naam* van de of dynamische eigenschap. Een `keyName` moet uniek zijn binnen de `StyleObject` matrix.| Ja |
| `type` | tekenreeks | De waarde is "numeriek". | Ja |
| `rules` | [`NumberRuleObject`](#numberruleobject)[]| Een matrix met numerieke stijl reeksen met gekoppelde kleuren. Elk bereik definieert een kleur die moet worden gebruikt wanneer de *status* waarde aan het bereik voldoet.| Ja |

### <a name="numberruleobject"></a>NumberRuleObject

Een `NumberRuleObject` bestaat uit een- [`RangeObject`](#rangeobject) en- `color` eigenschap. Als de *status* waarde in het bereik valt, wordt de kleur voor weer gave de kleur die is opgegeven in de `color` eigenschap.

Als u meerdere overlappende bereiken definieert, wordt de gekozen kleur de kleur die is gedefinieerd in het eerste bereik waaraan wordt voldaan.

In het volgende JSON-voor beeld bevatten beide bereiken waar wanneer de waarde van de *status* tussen 50-60 ligt. De kleur die wordt gebruikt, is echter het `#343deb` eerste bereik in de lijst waaraan is voldaan.

```json

    {
        "rules":[
            {
                "range": {
                "minimum": 50,
                "exclusiveMaximum": 70
                },
                "color": "#343deb"
            },
            {
                "range": {
                "minimum": 50,
                "maximum": 60
                },
                "color": "#eba834"
            }
        ]
    }
]
```

| Eigenschap | Type | Beschrijving | Vereist |
|-----------|----------|-------------|-------------|
| `range` | [RangeObject](#rangeobject) | De [RangeObject](#rangeobject) definieert een set voor waarden voor logische rangen, die, indien `true` , de weergave kleur van de *status* wijzigt in de kleur die is opgegeven in de `color` eigenschap. Als `range` niet wordt opgegeven, wordt de kleur die in de eigenschap is gedefinieerd, `color` altijd gebruikt.   | Nee |
| `color` | tekenreeks | De kleur die moet worden gebruikt wanneer de status waarde binnen het bereik valt. De `color` eigenschap is een JSON-teken reeks in een van de volgende indelingen: <ul><li> Hexadecimale waarden in HTML-notatie </li><li> RGB ("#ff0", "#ffff00", "RGB (255, 255, 0)")</li><li> RGBA ("RGBA (255, 255, 0, 1)")</li><li> HSL ("HSL (100, 50%, 50%)")</li><li> HSLA ("HSLA (100, 50%, 50%, 1)")</li><li> Vooraf gedefinieerde namen van HTML-kleuren, zoals geel en blauw.</li></ul> | Ja |

### <a name="rangeobject"></a>RangeObject

De `RangeObject` definieert een numerieke bereik waarde van een [`NumberRuleObject`](#numberruleobject) . Voor de *status* waarde die in het bereik moet vallen, moeten alle gedefinieerde voor waarden waar zijn. 

| Eigenschap | Type | Beschrijving | Vereist |
|-----------|----------|-------------|-------------|
| `minimum` | double | Het getal x dat x ≥ `minimum` .| Nee |
| `maximum` | double | Alle x met x ≤ `maximum` . | Nee |
| `exclusiveMinimum` | double | Alle x > `exclusiveMinimum` .| Nee |
| `exclusiveMaximum` | double | Alle x < `exclusiveMaximum` .| Nee |

### <a name="example-of-numerictypestylerule"></a>Voor beeld van NumericTypeStyleRule

De volgende JSON illustreert een `NumericTypeStyleRule` *status* met de naam `temperature` . In dit voor beeld [`NumberRuleObject`](#numberruleobject) bevat de twee gedefinieerde temperatuur bereiken en de bijbehorende kleur stijlen. Als het temperatuur bereik 50-69 is, moet de weer gave de kleur gebruiken `#343deb` .  Als het temperatuur bereik 31-70 is, moet de weer gave de kleur gebruiken `#eba834` .

```json
{
    "keyname": "temperature",
    "type": "number",
    "rules":[
        {
            "range": {
            "minimum": 50,
            "exclusiveMaximum": 70
            },
            "color": "#343deb"
        },
        {
            "range": {
            "maximum": 70,
            "exclusiveMinimum": 30
            },
            "color": "#eba834"
        }
    ]
}
```

## <a name="booleantypestylerule"></a>BooleanTypeStyleRule

A `BooleanTypeStyleRule` is een [`StyleObject`](#styleobject) en bestaat uit de volgende eigenschappen:

| Eigenschap | Type | Beschrijving | Vereist |
|-----------|----------|-------------|-------------|
| `keyName` | tekenreeks |  De *naam* van de of dynamische eigenschap.  Een `keyName` moet uniek zijn binnen de Style-matrix.| Ja |
| `type` | tekenreeks |De waarde is Booleaans. | Ja |
| `rules` | [`BooleanRuleObject`](#booleanruleobject)i| Een Boole-paar met kleuren voor `true` en `false` *status* waarden.| Ja |

### <a name="booleanruleobject"></a>BooleanRuleObject

Een `BooleanRuleObject` definieert kleuren voor `true` en `false` waarden.

| Eigenschap | Type | Beschrijving | Vereist |
|-----------|----------|-------------|-------------|
| `true` | tekenreeks | De kleur die moet worden gebruikt wanneer de *status* waarde is `true` . De `color` eigenschap is een JSON-teken reeks in een van de volgende indelingen: <ul><li> Hexadecimale waarden in HTML-notatie </li><li> RGB ("#ff0", "#ffff00", "RGB (255, 255, 0)")</li><li> RGBA ("RGBA (255, 255, 0, 1)")</li><li> HSL ("HSL (100, 50%, 50%)")</li><li> HSLA ("HSLA (100, 50%, 50%, 1)")</li><li> Vooraf gedefinieerde namen van HTML-kleuren, zoals geel en blauw.</li></ul>| Ja |
| `false` | tekenreeks | De kleur die moet worden gebruikt wanneer de *status* waarde is `false` . | Ja |

### <a name="example-of-booleantypestylerule"></a>Voor beeld van BooleanTypeStyleRule

De volgende JSON illustreert een `BooleanTypeStyleRule` *status* met de naam `occupied` . [`BooleanRuleObject`](#booleanruleobject)Hiermee definieert u kleuren voor `true` en `false` waarden.

```json
{
    "keyname": "occupied",
    "type": "boolean",
    "rules": [
    {
        "true": "#FF0000",
        "false": "#00FF00"
    }
    ]
}
```