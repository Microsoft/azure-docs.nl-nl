---
title: Gebeurtenis filtering voor Azure Event Grid
description: Hierin wordt beschreven hoe u gebeurtenissen filtert bij het maken van een Azure Event Grid-abonnement.
ms.topic: conceptual
ms.date: 03/04/2021
ms.openlocfilehash: fa63296f97bfa888cb0f425d0c03a5e4a7e46525
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103419844"
---
# <a name="understand-event-filtering-for-event-grid-subscriptions"></a>Gebeurtenis filters begrijpen voor Event Grid-abonnementen

In dit artikel worden de verschillende manieren beschreven waarop u kunt filteren welke gebeurtenissen naar uw eind punt worden verzonden. Wanneer u een gebeurtenis abonnement maakt, hebt u drie opties voor het filteren:

* Gebeurtenistypen
* Onderwerp begint met of eindigt met
* Geavanceerde velden en Opera tors

## <a name="event-type-filtering"></a>Filter gebeurtenis type

Standaard worden alle [gebeurtenis typen](event-schema.md) voor de gebeurtenis bron naar het eind punt verzonden. U kunt ervoor kiezen om alleen bepaalde gebeurtenis typen naar uw eind punt te verzenden. U kunt bijvoorbeeld een melding ontvangen over updates voor uw resources, maar niet op de hoogte worden gesteld van andere bewerkingen, zoals verwijderingen. In dat geval filtert u op het `Microsoft.Resources.ResourceWriteSuccess` gebeurtenis type. Geef een matrix met de gebeurtenis typen op, of geef `All` op om alle gebeurtenis typen voor de gebeurtenis bron op te halen.

De JSON-syntaxis voor filteren op gebeurtenis type is:

```json
"filter": {
  "includedEventTypes": [
    "Microsoft.Resources.ResourceWriteFailure",
    "Microsoft.Resources.ResourceWriteSuccess"
  ]
}
```

## <a name="subject-filtering"></a>Filteren op onderwerp

Geef voor eenvoudig filteren op onderwerp een begin-of eind waarde voor het onderwerp op. U kunt bijvoorbeeld opgeven dat het onderwerp eindigt met `.txt` om alleen gebeurtenissen op te halen met betrekking tot het uploaden van een tekst bestand naar een opslag account. U kunt ook filteren of het onderwerp begint met `/blobServices/default/containers/testcontainer` om alle gebeurtenissen voor die container op te halen, maar niet andere containers in het opslag account.

Bij het publiceren van gebeurtenissen naar aangepaste onderwerpen maakt u onderwerpen voor uw evenementen waarmee abonnees eenvoudig kunnen zien of ze geïnteresseerd zijn in de gebeurtenis. Abonnees gebruiken de eigenschap Subject om gebeurtenissen te filteren en te routeren. Overweeg het pad toe te voegen voor waar de gebeurtenis zich voordeed, zodat abonnees kunnen filteren op segmenten van dat pad. Met het pad kunnen abonnees de gebeurtenissen op een smalle of brede manier filteren. Als u een drie segment pad opgeeft `/A/B/C` , zoals in het onderwerp, kunnen abonnees filteren op het eerste segment `/A` om een grote set gebeurtenissen te verkrijgen. Deze abonnees ontvangen gebeurtenissen met onderwerpen als `/A/B/C` of `/A/D/E` . Andere abonnees kunnen filteren op `/A/B` om een smalle set gebeurtenissen te verkrijgen.

De JSON-syntaxis voor filteren op onderwerp is:

```json
"filter": {
  "subjectBeginsWith": "/blobServices/default/containers/mycontainer/log",
  "subjectEndsWith": ".jpg"
}

```

## <a name="advanced-filtering"></a>Geavanceerd filteren

Als u wilt filteren op waarden in de gegevens velden en de vergelijkings operator wilt opgeven, gebruikt u de geavanceerde filter optie. In geavanceerde filtering geeft u het volgende op:

* operator type: het type vergelijking.
* sleutel: het veld in de gebeurtenis gegevens die u gebruikt voor het filteren van. Dit kan een getal, een Booleaanse waarde, een teken reeks of een matrix zijn.
* waarden: de waarde of waarden die moeten worden vergeleken met de sleutel.

## <a name="key"></a>Sleutel
Sleutel is het veld in de gebeurtenis gegevens die u gebruikt voor het filteren van. Dit kan een van de volgende typen zijn:

- Aantal
- Boolean
- Tekenreeks
- Array. U moet de eigenschap instellen `enableAdvancedFilteringOnArrays` op True om deze functie te gebruiken. Op dit moment biedt de Azure Portal geen ondersteuning voor het inschakelen van deze functie. 

    ```json
    "filter":
    {
        "subjectBeginsWith": "/blobServices/default/containers/mycontainer/log",
        "subjectEndsWith": ".jpg",
        "enableAdvancedFilteringOnArrays": true
    }
    ```

Voor gebeurtenissen in het **Event grid schema** gebruikt u de volgende waarden voor de sleutel:,,,,, `ID` `Topic` `Subject` `EventType` `DataVersion` of gebeurtenis gegevens (zoals `data.key1` ).

Voor gebeurtenissen in het **schema voor Cloud gebeurtenissen** gebruikt u de volgende waarden voor de sleutel:,,, `eventid` `source` `eventtype` `eventtypeversion` of gebeurtenis gegevens (zoals `data.key1` ).

Voor het **aangepaste invoer schema** gebruikt u de velden voor gebeurtenis gegevens (zoals `data.key1` ). Als u toegang wilt krijgen tot velden in de sectie gegevens, gebruikt u de `.` notatie (punt). Bijvoorbeeld, `data.sitename` `data.appEventTypeDetail.action` om toegang te krijgen tot `sitename` of `action` voor de volgende voorbeeld gebeurtenis.

```json
    "data": {
        "appEventTypeDetail": {
            "action": "Started"
        },
        "siteName": "<site-name>",
        "clientRequestId": "None",
        "correlationRequestId": "None",
        "requestId": "292f499d-04ee-4066-994d-c2df57b99198",
        "address": "None",
        "verb": "None"
    },
```

## <a name="values"></a>Waarden
De waarden kunnen zijn: Number, String, Boolean of array

## <a name="operators"></a>Operators

De beschik bare Opera tors voor **getallen** zijn:

## <a name="numberin"></a>NumberIn
De operator NumberIn resulteert in waar als de **sleutel** waarde een van de opgegeven **filter** waarden is. In het volgende voor beeld wordt gecontroleerd of de waarde van het `counter` kenmerk in de `data` sectie 5 of 1 is. 

```json
"advancedFilters": [{
    "operatorType": "NumberIn",
    "key": "data.counter",
    "values": [
        5,
        1
    ]
}]
```


Als de sleutel een matrix is, worden alle waarden in de matrix gecontroleerd op basis van de matrix met filter waarden. Dit is de pseudo code met de sleutel: `[v1, v2, v3]` en het filter: `[a, b, c]` . Sleutel waarden met gegevens typen die niet overeenkomen met het gegevens type van het filter, worden genegeerd.

```
FOR_EACH filter IN (a, b, c)
    FOR_EACH key IN (v1, v2, v3)
        IF filter == key
            MATCH
```

## <a name="numbernotin"></a>NumberNotIn
De NumberNotIn resulteert in waar als de **sleutel** waarde **geen** van de opgegeven **filter** waarden is. In het volgende voor beeld wordt gecontroleerd of de waarde van het `counter` kenmerk in de `data` sectie niet 41 en 0 is. 

```json
"advancedFilters": [{
    "operatorType": "NumberNotIn",
    "key": "data.counter",
    "values": [
        41,
        0
    ]
}]
```

Als de sleutel een matrix is, worden alle waarden in de matrix gecontroleerd op basis van de matrix met filter waarden. Dit is de pseudo code met de sleutel: `[v1, v2, v3]` en het filter: `[a, b, c]` . Sleutel waarden met gegevens typen die niet overeenkomen met het gegevens type van het filter, worden genegeerd.

```
FOR_EACH filter IN (a, b, c)
    FOR_EACH key IN (v1, v2, v3)
        IF filter == key
            FAIL_MATCH
```

## <a name="numberlessthan"></a>NumberLessThan
De operator NumberLessThan resulteert in waar als de **sleutel** waarde lager is **dan** de opgegeven **filter** waarde. In het volgende voor beeld wordt gecontroleerd of de waarde van het `counter` kenmerk in de `data` sectie kleiner is dan 100. 

```json
"advancedFilters": [{
    "operatorType": "NumberLessThan",
    "key": "data.counter",
    "value": 100
}]
```

Als de sleutel een matrix is, worden alle waarden in de matrix vergeleken met de filter waarde. Dit is de pseudo code met de sleutel: `[v1, v2, v3]` . Sleutel waarden met gegevens typen die niet overeenkomen met het gegevens type van het filter, worden genegeerd.

```
FOR_EACH key IN (v1, v2, v3)
    IF key < filter
        MATCH
```

## <a name="numbergreaterthan"></a>NumberGreaterThan
De operator NumberGreaterThan resulteert in waar als de **sleutel** waarde groter is **dan** de opgegeven **filter** waarde. In het volgende voor beeld wordt gecontroleerd of de waarde van het `counter` kenmerk in de `data` sectie groter is dan 20. 

```json
"advancedFilters": [{
    "operatorType": "NumberGreaterThan",
    "key": "data.counter",
    "value": 20
}]
```

Als de sleutel een matrix is, worden alle waarden in de matrix vergeleken met de filter waarde. Dit is de pseudo code met de sleutel: `[v1, v2, v3]` . Sleutel waarden met gegevens typen die niet overeenkomen met het gegevens type van het filter, worden genegeerd.

```
FOR_EACH key IN (v1, v2, v3)
    IF key > filter
        MATCH
```

## <a name="numberlessthanorequals"></a>NumberLessThanOrEquals
De operator NumberLessThanOrEquals resulteert in waar als de **sleutel** waarde kleiner is **dan of gelijk** is aan de opgegeven **filter** waarde. In het volgende voor beeld wordt gecontroleerd of de waarde van het `counter` kenmerk in de `data` sectie kleiner is dan of gelijk is aan 100. 

```json
"advancedFilters": [{
    "operatorType": "NumberLessThanOrEquals",
    "key": "data.counter",
    "value": 100
}]
```

Als de sleutel een matrix is, worden alle waarden in de matrix vergeleken met de filter waarde. Dit is de pseudo code met de sleutel: `[v1, v2, v3]` . Sleutel waarden met gegevens typen die niet overeenkomen met het gegevens type van het filter, worden genegeerd.

```
FOR_EACH key IN (v1, v2, v3)
    IF key <= filter
        MATCH
```

## <a name="numbergreaterthanorequals"></a>NumberGreaterThanOrEquals
De operator NumberGreaterThanOrEquals resulteert in waar als de **sleutel** waarde groter is **dan of gelijk** is aan de opgegeven **filter** waarde. In het volgende voor beeld wordt gecontroleerd of de waarde van het `counter` kenmerk in de `data` sectie groter is dan of gelijk is aan 30. 

```json
"advancedFilters": [{
    "operatorType": "NumberGreaterThanOrEquals",
    "key": "data.counter",
    "value": 30
}]
```

Als de sleutel een matrix is, worden alle waarden in de matrix vergeleken met de filter waarde. Dit is de pseudo code met de sleutel: `[v1, v2, v3]` . Sleutel waarden met gegevens typen die niet overeenkomen met het gegevens type van het filter, worden genegeerd.

```
FOR_EACH key IN (v1, v2, v3)
    IF key >= filter
        MATCH
```

## <a name="numberinrange"></a>NumberInRange
De operator NumberInRange resulteert in waar als de **sleutel** waarde zich in een van de opgegeven **filter bereik** bevindt. In het volgende voor beeld wordt gecontroleerd of de waarde van het `key1` kenmerk in de `data` sectie zich in een van de twee bereiken bevindt: 3,14159-999,95, 3000-4000. 

```json
{
    "operatorType": "NumberInRange",
    "key": "data.key1",
    "values": [[3.14159, 999.95], [3000, 4000]]
}
```

De `values` eigenschap is een matrix met bereiken. In het vorige voor beeld is het een matrix van twee bereiken. Hier volgt een voor beeld van een matrix met één te controleren bereik. 

**Matrix met één bereik:** 
```json
{
    "operatorType": "NumberInRange",
    "key": "data.key1",
    "values": [[3000, 4000]]
}
```

Als de sleutel een matrix is, worden alle waarden in de matrix gecontroleerd op basis van de matrix met filter waarden. Dit is de pseudo code met de sleutel: `[v1, v2, v3]` en het filter: een matrix met bereiken. In deze pseudo code `a` en `b` zijn lage en hoge waarden van elk bereik in de matrix. Sleutel waarden met gegevens typen die niet overeenkomen met het gegevens type van het filter, worden genegeerd.

```
FOR_EACH (a,b) IN filter.Values
    FOR_EACH key IN (v1, v2, v3)
       IF key >= a AND key <= b
           MATCH
```


## <a name="numbernotinrange"></a>NumberNotInRange
De operator NumberNotInRange resulteert in waar als de **sleutel** waarde zich **niet** in een van de opgegeven **afdrukbereiken** bevindt. In het volgende voor beeld wordt gecontroleerd of de waarde van het `key1` kenmerk in de `data` sectie zich in een van de twee bereiken bevindt: 3,14159-999,95, 3000-4000. Als dat het geval is, retourneert de operator false. 

```json
{
    "operatorType": "NumberNotInRange",
    "key": "data.key1",
    "values": [[3.14159, 999.95], [3000, 4000]]
}
```
De `values` eigenschap is een matrix met bereiken. In het vorige voor beeld is het een matrix van twee bereiken. Hier volgt een voor beeld van een matrix met één te controleren bereik.

**Matrix met één bereik:** 
```json
{
    "operatorType": "NumberNotInRange",
    "key": "data.key1",
    "values": [[3000, 4000]]
}
```

Als de sleutel een matrix is, worden alle waarden in de matrix gecontroleerd op basis van de matrix met filter waarden. Dit is de pseudo code met de sleutel: `[v1, v2, v3]` en het filter: een matrix met bereiken. In deze pseudo code `a` en `b` zijn lage en hoge waarden van elk bereik in de matrix. Sleutel waarden met gegevens typen die niet overeenkomen met het gegevens type van het filter, worden genegeerd.

```
FOR_EACH (a,b) IN filter.Values
    FOR_EACH key IN (v1, v2, v3)
        IF key >= a AND key <= b
            FAIL_MATCH
```


De beschik bare operator voor **Booleaanse** waarden is: 

## <a name="boolequals"></a>BoolEquals
De operator BoolEquals resulteert in waar als de **sleutel** waarde het opgegeven **filter** voor Booleaanse waarden is. In het volgende voor beeld wordt gecontroleerd of de waarde van het `isEnabled` kenmerk in de `data` sectie is `true` . 

```json
"advancedFilters": [{
    "operatorType": "BoolEquals",
    "key": "data.isEnabled",
    "value": true
}]
```

Als de sleutel een matrix is, worden alle waarden in de matrix vergeleken met de Booleaanse waarde van het filter. Dit is de pseudo code met de sleutel: `[v1, v2, v3]` . Sleutel waarden met gegevens typen die niet overeenkomen met het gegevens type van het filter, worden genegeerd.

```
FOR_EACH key IN (v1, v2, v3)
    IF filter == key
        MATCH
```

De beschik bare Opera tors voor **teken reeksen** zijn:

## <a name="stringcontains"></a>StringContains
De **StringContains** resulteert in waar als de **sleutel** waarde een van de opgegeven **filter** waarden (als subtekenreeksen) **bevat** . In het volgende voor beeld wordt gecontroleerd of de waarde van het `key1` kenmerk in de `data` sectie een van de opgegeven subtekenreeksen bevat: `microsoft` of `azure` . `azure data factory`Deze bevat bijvoorbeeld `azure` . 

```json
"advancedFilters": [{
    "operatorType": "StringContains",
    "key": "data.key1",
    "values": [
        "microsoft", 
        "azure"
    ]
}]
```

Als de sleutel een matrix is, worden alle waarden in de matrix gecontroleerd op basis van de matrix met filter waarden. Dit is de pseudo code met de sleutel: `[v1, v2, v3]` en het filter: `[a,b,c]` . Sleutel waarden met gegevens typen die niet overeenkomen met het gegevens type van het filter, worden genegeerd.

```
FOR_EACH filter IN (a, b, c)
    FOR_EACH key IN (v1, v2, v3)
        IF key CONTAINS filter
            MATCH
```

## <a name="stringnotcontains"></a>StringNotContains
De operator **StringNotContains** evalueert naar waar als de **sleutel** de opgegeven **filter** waarden niet als subtekenreeksen **bevat** . Als de sleutel een van de opgegeven waarden als een subtekenreeks bevat, resulteert de operator in ONWAAR. In het volgende voor beeld retourneert de operator alleen True als de waarde van het `key1` kenmerk in de `data` sectie geen `contoso` `fabrikam` subtekenreeksen bevat. 

```json
"advancedFilters": [{
    "operatorType": "StringNotContains",
    "key": "data.key1",
    "values": [
        "contoso", 
        "fabrikam"
    ]
}]
```

Als de sleutel een matrix is, worden alle waarden in de matrix gecontroleerd op basis van de matrix met filter waarden. Dit is de pseudo code met de sleutel: `[v1, v2, v3]` en het filter: `[a,b,c]` . Sleutel waarden met gegevens typen die niet overeenkomen met het gegevens type van het filter, worden genegeerd.

```
FOR_EACH filter IN (a, b, c)
    FOR_EACH key IN (v1, v2, v3)
        IF key CONTAINS filter
            FAIL_MATCH
```
Zie de sectie [beperkingen](#limitations) voor huidige beperking van deze operator.

## <a name="stringbeginswith"></a>StringBeginsWith
De operator **StringBeginsWith** evalueert naar waar als de **sleutel** waarde **begint met** een van de opgegeven **filter** waarden. In het volgende voor beeld wordt gecontroleerd of de waarde van het `key1` kenmerk in de `data` sectie begint met `event` of `grid` . `event hubs`Begint bijvoorbeeld met `event` .  

```json
"advancedFilters": [{
    "operatorType": "StringBeginsWith",
    "key": "data.key1",
    "values": [
        "event", 
        "message"
    ]
}]
```

Als de sleutel een matrix is, worden alle waarden in de matrix gecontroleerd op basis van de matrix met filter waarden. Dit is de pseudo code met de sleutel: `[v1, v2, v3]` en het filter: `[a,b,c]` . Sleutel waarden met gegevens typen die niet overeenkomen met het gegevens type van het filter, worden genegeerd.

```
FOR_EACH filter IN (a, b, c)
    FOR_EACH key IN (v1, v2, v3)
        IF key BEGINS_WITH filter
            MATCH
```

## <a name="stringnotbeginswith"></a>StringNotBeginsWith
De operator **StringNotBeginsWith** resulteert in waar als de **sleutel** waarde **niet begint met** een van de opgegeven **filter** waarden. In het volgende voor beeld wordt gecontroleerd of de waarde van het `key1` kenmerk in de `data` sectie niet begint met `event` of `message` .

```json
"advancedFilters": [{
    "operatorType": "StringNotBeginsWith",
    "key": "data.key1",
    "values": [
        "event", 
        "message"
    ]
}]
```

Als de sleutel een matrix is, worden alle waarden in de matrix gecontroleerd op basis van de matrix met filter waarden. Dit is de pseudo code met de sleutel: `[v1, v2, v3]` en het filter: `[a,b,c]` . Sleutel waarden met gegevens typen die niet overeenkomen met het gegevens type van het filter, worden genegeerd.

```
FOR_EACH filter IN (a, b, c)
    FOR_EACH key IN (v1, v2, v3)
        IF key BEGINS_WITH filter
            FAIL_MATCH
```

## <a name="stringendswith"></a>StringEndsWith
De operator **StringEndsWith** evalueert naar waar als de **sleutel** waarde **eindigt met** een van de opgegeven **filter** waarden. In het volgende voor beeld wordt gecontroleerd of de waarde van het `key1` kenmerk in de `data` sectie eindigt met `jpg` of `jpeg` of `png` . Bijvoorbeeld eindigt op `eventgrid.png` `png` .


```json
"advancedFilters": [{
    "operatorType": "StringEndsWith",
    "key": "data.key1",
    "values": [
        "jpg", 
        "jpeg", 
        "png"
    ]
}]
```

Als de sleutel een matrix is, worden alle waarden in de matrix gecontroleerd op basis van de matrix met filter waarden. Dit is de pseudo code met de sleutel: `[v1, v2, v3]` en het filter: `[a,b,c]` . Sleutel waarden met gegevens typen die niet overeenkomen met het gegevens type van het filter, worden genegeerd.

```
FOR_EACH filter IN (a, b, c)
    FOR_EACH key IN (v1, v2, v3)
        IF key ENDS_WITH filter
            MATCH
```

## <a name="stringnotendswith"></a>StringNotEndsWith
De operator **StringNotEndsWith** resulteert in waar als de **sleutel** waarde **niet eindigt met** een van de opgegeven **filter** waarden. In het volgende voor beeld wordt gecontroleerd of de waarde van het `key1` kenmerk in de `data` sectie niet eindigt met `jpg` of `jpeg` of `png` . 


```json
"advancedFilters": [{
    "operatorType": "StringNotEndsWith",
    "key": "data.key1",
    "values": [
        "jpg", 
        "jpeg", 
        "png"
    ]
}]
```

Als de sleutel een matrix is, worden alle waarden in de matrix gecontroleerd op basis van de matrix met filter waarden. Dit is de pseudo code met de sleutel: `[v1, v2, v3]` en het filter: `[a,b,c]` . Sleutel waarden met gegevens typen die niet overeenkomen met het gegevens type van het filter, worden genegeerd.

```
FOR_EACH filter IN (a, b, c)
    FOR_EACH key IN (v1, v2, v3)
        IF key ENDS_WITH filter
            FAIL_MATCH
```

## <a name="stringin"></a>StringIn
De operator **StringIn** controleert of de **sleutel** waarde **exact overeenkomt met** een van de opgegeven **filter** waarden. In het volgende voor beeld wordt gecontroleerd of de waarde van het `key1` kenmerk in de `data` sectie is `exact` of `string` of `matches` . 

```json
"advancedFilters": [{
    "operatorType": "StringIn",
    "key": "data.key1",
    "values": [
        "contoso", 
        "fabrikam", 
        "factory"
    ]
}]
```

Als de sleutel een matrix is, worden alle waarden in de matrix gecontroleerd op basis van de matrix met filter waarden. Dit is de pseudo code met de sleutel: `[v1, v2, v3]` en het filter: `[a,b,c]` . Sleutel waarden met gegevens typen die niet overeenkomen met het gegevens type van het filter, worden genegeerd.

```
FOR_EACH filter IN (a, b, c)
    FOR_EACH key IN (v1, v2, v3)
        IF filter == key
            MATCH
```

## <a name="stringnotin"></a>StringNotIn
De operator **StringNotIn** controleert of de **sleutel** waarde **niet overeenkomt met** een van de opgegeven **filter** waarden. In het volgende voor beeld wordt gecontroleerd of de waarde van het `key1` kenmerk in de `data` sectie niet is `aws` en `bridge` . 

```json
"advancedFilters": [{
    "operatorType": "StringNotIn",
    "key": "data.key1",
    "values": [
        "aws", 
        "bridge"
    ]
}]
```

Als de sleutel een matrix is, worden alle waarden in de matrix gecontroleerd op basis van de matrix met filter waarden. Dit is de pseudo code met de sleutel: `[v1, v2, v3]` en het filter: `[a,b,c]` . Sleutel waarden met gegevens typen die niet overeenkomen met het gegevens type van het filter, worden genegeerd.

```
FOR_EACH filter IN (a, b, c)
    FOR_EACH key IN (v1, v2, v3)
        IF filter == key
            FAIL_MATCH
```


Alle teken reeks vergelijkingen zijn niet hoofdletter gevoelig.

> [!NOTE]
> Als de JSON van de gebeurtenis niet de geavanceerde filter sleutel bevat, is filter evaulated **niet overeenkomt met** de volgende Opera tors: NumberGreaterThan, NumberGreaterThanOrEquals, NumberLessThan, NumberLessThanOrEquals, NumberIn, BoolEquals, StringContains, StringNotContains, StringBeginsWith, StringNotBeginsWith, StringEndsWith, StringNotEndsWith, StringIn.
> 
>Het filter is evaulated die **overeenkomt met** de volgende Opera tors: NumberNotIn, StringNotIn.


## <a name="isnullorundefined"></a>IsNullOrUndefined
De operator IsNullOrUndefined resulteert in waar als de waarde van de sleutel NULL of niet gedefinieerd is. 

```json
{
    "operatorType": "IsNullOrUndefined",
    "key": "data.key1"
}
```

In het volgende voor beeld ontbreekt Key1, waardoor de operator resulteert in waar. 

```json
{ 
    "data": 
    { 
        "key2": 5 
    } 
}
```

In het volgende voor beeld is Key1 ingesteld op NULL, zodat de operator resulteert in waar.

```json
{
    "data": 
    { 
        "key1": null
    }
}
```

Als Key1 een andere waarde in deze voor beelden heeft, resulteert de operator in ONWAAR. 

## <a name="isnotnull"></a>IsNotNull
De operator IsNotNull resulteert in waar als de waarde van de sleutel niet NULL of niet gedefinieerd is. 

```json
{
    "operatorType": "IsNotNull",
    "key": "data.key1"
}
```

## <a name="or-and-and"></a>OF en
Als u één filter met meerdere waarden opgeeft, wordt een **of** -bewerking uitgevoerd, zodat de waarde van het sleutel veld een van deze waarden moet zijn. Hier volgt een voorbeeld:

```json
"advancedFilters": [
    {
        "operatorType": "StringContains",
        "key": "Subject",
        "values": [
            "/providers/microsoft.devtestlab/",
            "/providers/Microsoft.Compute/virtualMachines/"
        ]
    }
]
```

Als u meerdere verschillende filters opgeeft, wordt er een **en** -bewerking uitgevoerd, dus er moet aan elke filter voorwaarde worden voldaan. Hier volgt een voorbeeld: 

```json
"advancedFilters": [
    {
        "operatorType": "StringContains",
        "key": "Subject",
        "values": [
            "/providers/microsoft.devtestlab/"
        ]
    },
    {
        "operatorType": "StringContains",
        "key": "Subject",
        "values": [
            "/providers/Microsoft.Compute/virtualMachines/"
        ]
    }
]
```

## <a name="cloudevents"></a>CloudEvents 
Voor gebeurtenissen in het **CloudEvents-schema** gebruikt u de volgende waarden voor de sleutel:,,,, `eventid` `source` `eventtype` `eventtypeversion` of gebeurtenis gegevens (zoals `data.key1` ). 

U kunt ook [extensie context kenmerken gebruiken in CloudEvents 1,0](https://github.com/cloudevents/spec/blob/v1.0.1/spec.md#extension-context-attributes). In het volgende voor beeld `comexampleextension1` en `comexampleothervalue` zijn extensie context kenmerken. 

```json
{
    "specversion" : "1.0",
    "type" : "com.example.someevent",
    "source" : "/mycontext",
    "id" : "C234-1234-1234",
    "time" : "2018-04-05T17:31:00Z",
    "subject": null,
    "comexampleextension1" : "value",
    "comexampleothervalue" : 5,
    "datacontenttype" : "application/json",
    "data" : {
        "appinfoA" : "abc",
        "appinfoB" : 123,
        "appinfoC" : true
    }
}
```

Hier volgt een voor beeld van het gebruik van een uitbreidings context kenmerk in een filter.

```json
"advancedFilters": [{
    "operatorType": "StringBeginsWith",
    "key": "comexampleothervalue",
    "values": [
        "5", 
        "1"
    ]
}]
```


## <a name="limitations"></a>Beperkingen

Geavanceerde filtering heeft de volgende beperkingen:

* 5 geavanceerde filters en 25 filter waarden voor alle filters per Event grid-abonnement
* 512 tekens per teken reeks waarde
* Vijf waarden voor **in** en **niet in** Opera tors
* De `StringNotContains` operator is momenteel niet beschikbaar in de portal.
* Sleutels met een teken **`.` (punt)** . Bijvoorbeeld: `http://schemas.microsoft.com/claims/authnclassreference` of `john.doe@contoso.com` . Er is momenteel geen ondersteuning voor Escape tekens in sleutels. 

Dezelfde sleutel kan in meer dan één filter worden gebruikt.





## <a name="next-steps"></a>Volgende stappen

* Zie [gebeurtenissen filteren voor Event grid voor](how-to-filter-events.md)meer informatie over het filteren van gebeurtenissen met Power shell en Azure cli.
* Zie [aangepaste gebeurtenissen maken en routeren met Azure Event grid](custom-event-quickstart.md)om snel aan de slag te gaan met Event grid.
