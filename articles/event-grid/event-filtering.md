---
title: Gebeurtenis filtering voor Azure Event Grid
description: Hierin wordt beschreven hoe u gebeurtenissen filtert bij het maken van een Azure Event Grid-abonnement.
ms.topic: conceptual
ms.date: 12/03/2020
ms.openlocfilehash: bc3e84037693fcd909961ba409871d947ef1de7d
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96574903"
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
* sleutel: het veld in de gebeurtenis gegevens die u gebruikt voor het filteren van. Dit kan een getal, een Booleaanse waarde of een teken reeks zijn.
* waarden: de waarde of waarden die moeten worden vergeleken met de sleutel.

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

### <a name="operators"></a>Operators

De beschik bare Opera tors voor **getallen** zijn:

* NumberGreaterThan
* NumberGreaterThanOrEquals
* NumberLessThan
* NumberLessThanOrEquals
* NumberIn
* NumberNotIn

De beschik bare operator voor **Booleaanse** waarden is: 
- BoolEquals

De beschik bare Opera tors voor **teken reeksen** zijn:

* StringContains
* StringBeginsWith
* StringEndsWith
* StringIn
* StringNotIn

Alle teken reeks vergelijkingen zijn **niet** hoofdletter gevoelig.

> [!NOTE]
> Als de JSON van de gebeurtenis niet de geavanceerde filter sleutel bevat, wordt het filter evaulated **niet overeenkomt met** de volgende Opera tors: 
> - NumberGreaterThan
> - NumberGreaterThanOrEquals
> - NumberLessThan
> - NumberLessThanOrEquals
> - NumberIn
> - BoolEquals
> - StringContains
> - StringBeginsWith
> - StringEndsWith
> - StringIn
> 
>Het filter is evaulated die **overeenkomt met** de volgende Opera tors:
> - NumberNotIn
> - StringNotIn

### <a name="key"></a>Sleutel

Voor gebeurtenissen in het Event Grid schema gebruikt u de volgende waarden voor de sleutel:

* Id
* Onderwerp
* Onderwerp
* EventType
* DataVersion
* Gebeurtenis gegevens (zoals data. Key1)

Voor gebeurtenissen in het schema voor Cloud gebeurtenissen gebruikt u de volgende waarden voor de sleutel:

* Gebeurtenis
* Bron
* EventType
* EventTypeVersion
* Gebeurtenis gegevens (zoals data. Key1)

Voor het aangepaste invoer schema gebruikt u de velden voor gebeurtenis gegevens (zoals data. Key1).

### <a name="values"></a>Waarden

De waarden kunnen zijn:

* getal
* tekenreeks
* booleaans
* matrix

### <a name="limitations"></a>Beperkingen

Geavanceerde filtering heeft de volgende beperkingen:

* 5 geavanceerde filters en 25 filter waarden voor alle filters per Event grid-abonnement
* 512 tekens per teken reeks waarde
* Vijf waarden voor **in** en **niet in** Opera tors
* Sleutels met een teken **`.` (punt)** . Bijvoorbeeld: `http://schemas.microsoft.com/claims/authnclassreference` of `john.doe@contoso.com` . Er is momenteel geen ondersteuning voor Escape tekens in sleutels. 

Dezelfde sleutel kan in meer dan één filter worden gebruikt.

### <a name="examples"></a>Voorbeelden

### <a name="stringcontains"></a>StringContains

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

### <a name="stringbeginswith"></a>StringBeginsWith

```json
"advancedFilters": [{
    "operatorType": "StringBeginsWith",
    "key": "data.key1",
    "values": [
        "event", 
        "grid"
    ]
}]
```

### <a name="stringendswith"></a>StringEndsWith

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

### <a name="stringin"></a>StringIn

```json
"advancedFilters": [{
    "operatorType": "StringIn",
    "key": "data.key1",
    "values": [
        "exact", 
        "string", 
        "matches"
    ]
}]
```

### <a name="stringnotin"></a>StringNotIn

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

### <a name="numberin"></a>NumberIn

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

### <a name="numbernotin"></a>NumberNotIn

```json
"advancedFilters": [{
    "operatorType": "NumberNotIn",
    "key": "data.counter",
    "values": [
        41,
        0,
        0
    ]
}]
```

### <a name="numberlessthan"></a>NumberLessThan

```json
"advancedFilters": [{
    "operatorType": "NumberLessThan",
    "key": "data.counter",
    "value": 100
}]
```

### <a name="numbergreaterthan"></a>NumberGreaterThan

```json
"advancedFilters": [{
    "operatorType": "NumberGreaterThan",
    "key": "data.counter",
    "value": 20
}]
```

### <a name="numberlessthanorequals"></a>NumberLessThanOrEquals

```json
"advancedFilters": [{
    "operatorType": "NumberLessThanOrEquals",
    "key": "data.counter",
    "value": 100
}]
```

### <a name="numbergreaterthanorequals"></a>NumberGreaterThanOrEquals

```json
"advancedFilters": [{
    "operatorType": "NumberGreaterThanOrEquals",
    "key": "data.counter",
    "value": 30
}]
```

### <a name="boolequals"></a>BoolEquals

```json
"advancedFilters": [{
    "operatorType": "BoolEquals",
    "key": "data.isEnabled",
    "value": true
}]
```


## <a name="next-steps"></a>Volgende stappen

* Zie [gebeurtenissen filteren voor Event grid voor](how-to-filter-events.md)meer informatie over het filteren van gebeurtenissen met Power shell en Azure cli.
* Zie [aangepaste gebeurtenissen maken en routeren met Azure Event grid](custom-event-quickstart.md)om snel aan de slag te gaan met Event grid.
