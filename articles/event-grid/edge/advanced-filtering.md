---
title: Geavanceerde filtering-Azure Event Grid IoT Edge | Microsoft Docs
description: Geavanceerd filteren in Event Grid op IoT Edge.
author: HiteshMadan
manager: rajarv
ms.author: himad
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: 64b8956c47cbdbf31bb8253dac0c1e1f12833bf7
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96001045"
---
# <a name="advanced-filtering"></a>Geavanceerd filteren
Met Event Grid kunt u filters voor elke eigenschap in de JSON-nettolading opgeven. Deze filters zijn gemodelleerd als set `AND` voor waarden, waarbij elke Outer voor waarde optionele interne `OR` voor waarden heeft. Voor elke `AND` voor waarde geeft u de volgende waarden op:

* `OperatorType` -Het type vergelijking.
* `Key` -Het JSON-pad naar de eigenschap waarop het filter moet worden toegepast.
* `Value` -De referentie waarde waarmee het filter wordt uitgevoerd (of) `Values` : de set referentie waarden waartegen het filter wordt uitgevoerd.

## <a name="json-syntax"></a>JSON-syntaxis

De JSON-syntaxis voor een geavanceerd filter is als volgt:

```json
{
    "filter": {
        "advancedFilters": [{
                "operatorType": "NumberGreaterThanOrEquals",
                "key": "Data.Key1",
                "value": 5
            }, {
                "operatorType": "StringContains",
                "key": "Subject",
                "values": ["container1", "container2"]
            }
        ]
    }
}
```

## <a name="filtering-on-array-values"></a>Filteren op matrix waarden

Event Grid biedt geen ondersteuning voor filteren op een matrix met waarden vandaag. Als een binnenkomende gebeurtenis een matrix waarde heeft voor de sleutel van het geavanceerde filter, mislukt de overeenkomende bewerking. De binnenkomende gebeurtenis eindigt niet op overeenkomst met het gebeurtenis abonnement.

## <a name="and-or-not-semantics"></a>EN-of-geen semantiek

U ziet dat in het JSON-voor beeld dat eerder is gegeven, `AdvancedFilters` een matrix is. Denk aan elk `AdvancedFilter` matrix element als een `AND` voor waarde.

Voor de Opera tors die meerdere waarden ondersteunen (zoals `NumberIn` , `NumberNotIn` , `StringIn` , enzovoort), wordt elke waarde behandeld als een `OR` voor waarde. Een komt dus `StringBeginsWith("a", "b", "c")` overeen met een teken reeks waarde die begint met `a` ofwel `b` of `c` .

> [!CAUTION]
> De Opera tors en `NumberNotIn` `StringNotIn` gedragen zich als en voor waarden voor elke waarde die in het `Values` veld wordt gegeven.
>
> Als u dit niet doet, wordt er een Accept-All filter gemaakt en wordt het filter verslaan.

## <a name="floating-point-rounding-behavior"></a>Drijvende komma Afrondings gedrag

Event Grid gebruikt het `decimal` .net-type voor het afhandelen van alle numerieke waarden. De numerieke waarden die zijn opgegeven in de JSON van het gebeurtenis abonnement zijn niet onderhevig aan drijvende-komma afronding.

## <a name="case-sensitivity-of-string-filters"></a>Hoofdletter gevoeligheid van teken reeks filters

Alle teken reeks vergelijkingen zijn hoofdletter gevoelig. Het is niet mogelijk om dit gedrag vandaag nog te wijzigen.

## <a name="allowed-advanced-filter-keys"></a>Toegestane geavanceerde filter sleutels

De `Key` eigenschap kan een bekende eigenschap op het hoogste niveau zijn of een JSON-pad met meerdere punten zijn, waarbij elke punt wordt stapsgewijs in een genest JSON-object.

Event Grid heeft geen speciale betekenis voor het `$` teken in de sleutel, in tegens telling tot de JSONPath-specificatie.

### <a name="event-grid-schema"></a>Event grid-schema

Voor gebeurtenissen in het Event Grid schema:

* Id
* Onderwerp
* Onderwerp
* EventType
* DataVersion
* Data. Prop1
* Data. prop * Prop2. Prop3. Prop4. Prop5

### <a name="custom-event-schema"></a>Aangepast gebeurtenis schema

Er is geen beperking voor het `Key` aangepaste gebeurtenis schema, omdat Event grid geen envelop schema op de payload afdwingt.

## <a name="numeric-single-value-filter-examples"></a>Numerieke voor beelden van filter voor één waarde

* NumberGreaterThan
* NumberGreaterThanOrEquals
* NumberLessThan
* NumberLessThanOrEquals

```json
{
    "filter": {
        "advancedFilters": [
            {
                "operatorType": "NumberGreaterThan",
                "key": "Data.Key1",
                "value": 5
            },
            {
                "operatorType": "NumberGreaterThanOrEquals",
                "key": "Data.Key2",
                "value": *456
            },
            {
                "operatorType": "NumberLessThan",
                "key": "Data.P*P2.P3",
                "value": 1000
            },
            {
                "operatorType": "NumberLessThanOrEquals",
                "key": "Data.P*P2",
                "value": 999
            }
        ]
    }
}
```

## <a name="numeric-range-value-filter-examples"></a>Voor beelden van numerieke filter-waarden

* NumberIn
* NumberNotIn

```json
{
    "filter": {
        "advancedFilters": [
            {
                "operatorType": "NumberIn",
                "key": "Data.Key1",
                "values": [1, 10, 100]
            },
            {
                "operatorType": "NumberNotIn",
                "key": "Data.Key2",
                "values": [2, 3, 4.56]
            }
        ]
    }
}
```

## <a name="string-range-value-filter-examples"></a>Voor beelden van teken reeks-Waardefilter

* StringContains
* StringBeginsWith
* StringEndsWith
* StringIn
* StringNotIn

```json
{
    "filter": {
        "advancedFilters": [
            {
                "operatorType": "StringContains",
                "key": "Data.Key1",
                "values": ["microsoft", "azure"]
            },
            {
                "operatorType": "StringBeginsWith",
                "key": "Data.Key2",
                "values": ["event", "grid"]
            },
            {
                "operatorType": "StringEndsWith",
                "key": "Data.P3.P4",
                "values": ["jpg", "jpeg", "png"]
            },
            {
                "operatorType": "StringIn",
                "key": "RootKey",
                "values": ["exact", "string", "matches"]
            },
            {
                "operatorType": "StringNotIn",
                "key": "RootKey",
                "values": ["aws", "bridge"]
            }
        ]
    }
}
```

## <a name="boolean-single-value-filter-examples"></a>Voor beelden van Booleaanse filtering voor één waarde

* BoolEquals

```json
{
    "filter": {
        "advancedFilters": [
            {
                "operatorType": "BoolEquals",
                "key": "BoolKey1",
                "value": true
            },
            {
                "operatorType": "BoolEquals",
                "key": "BoolKey2",
                "value": false
            }
        ]
    }
}
```
