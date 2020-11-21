---
title: JSON afvlakking en Escapes-regels-Azure Time Series Insights Gen2 | Microsoft Docs
description: Meer informatie over de verwerking van JSON, afvlakking en het afhandelen van matrices in Azure Time Series Insights Gen2.
author: lyrana
ms.author: lyhughes
manager: deepakpalled
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 09/28/2020
ms.openlocfilehash: 0839d2c734418824952f37cb177490e56e1133c5
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95023306"
---
# <a name="json-flattening-escaping-and-array-handling"></a>JSON-platmaken, escapen en matrixafhandeling

Uw Azure Time Series Insights Gen2-omgeving maakt dynamische kolommen van uw warme en koude Stores en volgt een bepaalde set naam conventies. Wanneer een gebeurtenis wordt opgenomen, wordt een set regels toegepast op de JSON-nettolading en eigenschaps namen. Dit zijn onder andere het escapepen van bepaalde speciale tekens en het afvlakken van geneste JSON-objecten. Het is belang rijk dat u deze regels weet, zodat u begrijpt hoe de vorm van uw JSON invloed heeft op hoe uw gebeurtenissen worden opgeslagen en opgevraagd. Zie de onderstaande tabel voor een volledige lijst met regels. Voor beelden van een & B laten zien hoe u in een matrix efficiënt meerdere tijd reeksen kunt batchren.

> [!IMPORTANT]
>
> * Controleer de onderstaande regels voordat u een [eigenschap voor de tijd reeks-id](./how-to-select-tsid.md) en/of de juiste tijds tempel van de gebeurtenis bron selecteert [(s)](concepts-streaming-ingestion-event-sources.md#event-source-timestamp). Als uw TS-ID of-tijds tempel zich in een genest object bevindt of een of meer van de volgende speciale tekens bevat, is het belang rijk om ervoor te zorgen dat de naam van de eigenschap die u opgeeft, overeenkomt met de naam van de kolom *nadat* de opname regels zijn toegepast. Zie voor beeld [B](concepts-json-flattening-escaping-rules.md#example-b) hieronder.

| Regel | Voor beeld JSON | [Syntaxis van de expressie time series](/rest/api/time-series-insights/reference-time-series-expression-syntax) | Naam van eigenschaps kolom in Parquet
|---|---|---|---|
| Het gegevens type Azure Time Series Insights Gen2 wordt toegevoegd aan het einde van de kolom naam als "_ \<dataType\> " | ```"type": "Accumulated Heat"``` | `$event.type.String` |`type_string` |
| De [eigenschap tijds tempel](concepts-streaming-ingestion-event-sources.md#event-source-timestamp) van de gebeurtenis bron wordt opgeslagen in azure time series Insights Gen2 als tijds tempel in de opslag en de waarde die is opgeslagen in UTC. U kunt de tijds tempel eigenschap van uw gebeurtenis aanpassen zodat deze voldoet aan de behoeften van uw oplossing, maar de naam van de kolom in warme en koude opslag is ' tijds tempel '. Andere JSON-eigenschappen (datetime) die niet de bron tijds tempel van de gebeurtenis zijn, worden opgeslagen met _datetime in de kolom naam, zoals vermeld in de bovenstaande regel.  | ```"ts": "2020-03-19 14:40:38.318"``` |  `$event.$ts` | `timestamp` |
| De namen van JSON-eigenschappen die de speciale tekens bevatten. [\ en ' worden voorafgegaan door [' en ']  |  ```"id.wasp": "6A3090FD337DE6B"``` |  `$event['id.wasp'].String` | `['id.wasp']_string` |
| Binnen [' en '] is er nog meer Escapes van enkele aanhalings tekens en backslashes. Een enkele aanhalings tekens worden geschreven als \ ' en een back slash wordt geschreven als \\\ | ```"Foo's Law Value": "17.139999389648"``` | `$event['Foo\'s Law Value'].Double` | `['Foo\'s Law Value']_double` |
| Geneste JSON-objecten worden afgevlakt met een punt als scheidings teken. Het nesten van Maxi maal 10 niveaus wordt ondersteund. |  ```"series": {"value" : 316 }``` | `$event.series.value.Long`, `$event['series']['value'].Long` of `$event.series['value'].Long` |  `series.value_long` |
| Matrices van primitieve typen worden opgeslagen als het dynamische type |  ```"values": [154, 149, 147]``` | Dynamische typen kunnen alleen worden opgehaald via de [GetEvents](/rest/api/time-series-insights/dataaccessgen2/query/execute#getevents) -API | `values_dynamic` |
| Matrices die objecten bevatten, zijn afhankelijk van de object inhoud: als de TS-ID (s) of de eigenschap time stamp zich binnen de objecten in een matrix bevindt, wordt de matrix opgerold, zodat de eerste JSON-nettolading meerdere gebeurtenissen genereert. Hierdoor kunt u meerdere gebeurtenissen batchgewijs in één JSON-structuur. Alle eigenschappen op het hoogste niveau die peers zijn voor de matrix, worden met elk niet-gerollt object opgeslagen. Als uw TS-ID en tijds tempel zich *niet* in de matrix bevinden, wordt deze als het dynamische type opgeslagen. | Zie de voor beelden [A](concepts-json-flattening-escaping-rules.md#example-a), [B](concepts-json-flattening-escaping-rules.md#example-b)en [C](concepts-json-flattening-escaping-rules.md#example-c) hieronder
| Matrices met gemengde elementen worden niet afgevlakt. |  ```"values": ["foo", {"bar" : 149}, 147]``` | Dynamische typen kunnen alleen worden opgehaald via de [GetEvents](/rest/api/time-series-insights/dataaccessgen2/query/execute#getevents) -API | `values_dynamic` |
| 512 tekens is de naam limiet van de JSON-eigenschap. Als de naam langer is dan 512 tekens, wordt deze afgekapt tot 512 en ' _< ' hashCode ' > ' wordt toegevoegd. **Houd er rekening mee** dat dit ook geldt voor eigenschapnamen die zijn samengevoegd van een object, waarbij een genest objectpad wordt aangeduid. |``"data.items.datapoints.values.telemetry<...continuing to over 512 chars>" : 12.3440495`` |`"$event.data.items.datapoints.values.telemetry<...continuing to include all chars>.Double"` | `data.items.datapoints.values.telemetry<...continuing to 512 chars>_912ec803b2ce49e4a541068d495ab570_double` |

## <a name="understanding-the-dual-behavior-for-arrays"></a>Meer informatie over het dubbele gedrag voor matrices

Matrices van objecten worden geheel opgeslagen of in meerdere gebeurtenissen gesplitst, afhankelijk van de manier waarop u uw gegevens hebt gemodelleerd. Hierdoor kunt u een matrix gebruiken voor batch gebeurtenissen en vermijdt u herhalende telemetrie-eigenschappen die worden gedefinieerd op het niveau van het hoofd object. Het kan voor komen dat er minder Event Hubs of IoT Hub berichten worden verzonden.

In sommige gevallen zijn matrices met objecten echter alleen zinvol in de context van andere waarden. Als u meerdere gebeurtenissen maakt, worden de gegevens niet zo weer gegeven. Volg de onderstaande richt lijnen voor gegevens modellering om ervoor te zorgen dat een matrix van objecten als een dynamisch type wordt opgeslagen en Bekijk [voor beeld C](concepts-json-flattening-escaping-rules.md#example-c)

### <a name="how-to-know-if-my-array-of-objects-will-produce-multiple-events"></a>Hoe weet ik of mijn matrix van objecten meerdere gebeurtenissen zal genereren?

Als een of meer van uw tijd reeks-ID-verwerkte (s) zijn genest binnen objecten in een matrix *of* als de tijds tempel eigenschap van de gebeurtenis is genest, wordt deze door de opname engine gesplitst om meerdere gebeurtenissen te maken. De namen van eigenschappen die u hebt ingevoerd voor uw TS-ID ('s) en/of Time Stamp moeten voldoen aan de bovenstaande afvlakkings regels en geven daarom de vorm van uw JSON aan. Zie de voor beelden hieronder en lees de hand leiding voor het [selecteren van een time series-ID-eigenschap.](./how-to-select-tsid.md)

### <a name="example-a"></a>Voor beeld A

De time series-ID bij de hoofdmap van het object en het tijds tempel is genest \
**Tijd reeks-id van de omgeving:**`"id"`\
**Tijds tempel gebeurtenis Bron:**`"values.time"`\
**JSON-nettolading:**

```JSON
[
    {
        "id": "caaae533-1d6c-4f58-9b75-da102bcc2c8c",
        "values": [
            {
                "time": "2020-05-01T00:59:59.000Z",
                "value": 25.6073
            },
            {
                "time": "2020-05-01T01:00:29.000Z",
                "value": 43.9077
            }
        ]
    },
    {
        "id": "1ac87b74-0865-4a07-b512-56602a3a576f",
        "values": [
            {
                "time": "2020-05-01T00:59:59.000Z",
                "value": 0.337288
            },
            {
                "time": "2020-05-01T01:00:29.000Z",
                "value": 4.76562
            }
        ]
    }
]
```

**Resultaat in Parquet-bestand:**\
Met de bovenstaande configuratie en payload worden drie kolommen en vier gebeurtenissen gegenereerd

| tijdstempel  | id_string | values.value_double
| ---- | ---- | ---- |
| `2020-05-01T00:59:59.000Z` | `caaae533-1d6c-4f58-9b75-da102bcc2c8c`| ``25.6073`` |
| `2020-05-01T01:00:29.000Z` |`caaae533-1d6c-4f58-9b75-da102bcc2c8c` | ``43.9077`` |
| `2020-05-01T00:59:59.000Z` | `1ac87b74-0865-4a07-b512-56602a3a576f` | ``0.337288`` |
| `2020-05-01T01:00:29.000Z` | `1ac87b74-0865-4a07-b512-56602a3a576f` | ``4.76562`` |

### <a name="example-b"></a>Voor beeld B

Samengestelde time series-ID met één geneste eigenschap \
**Tijd reeks-id van de omgeving:** `"plantId"` maar `"telemetry.tagId"`\
**Tijds tempel gebeurtenis Bron:**`"timestamp"`\
**JSON-nettolading:**

```JSON
[
    {
        "plantId": "9336971",
        "timestamp": "2020-01-22T16:38:09Z",
        "telemetry": [
            {
                "tagId": "100231-A-A6",
                "tagValue": -31.149018
            },
            {
                "tagId": "100231-A-A1",
                "tagValue": 20.560796
            },
            {
                "tagId": "100231-A-A9",
                "tagValue": 177
            },
            {
                "tagId": "100231-A-A8",
                "tagValue": 420
            },
        ]
    },
    {
        "plantId": "9336971",
        "timestamp": "2020-01-22T16:42:14Z",
        "telemetry": [
            {
                "tagId": "103585-A-A7",
                "value": -30.9918
            },
            {
                "tagId": "103585-A-A4",
                "value": 19.960796
            }
        ]
    }
]
```

**Resultaat in Parquet-bestand:**\
Met de bovenstaande configuratie en payload worden vier kolommen en zes gebeurtenissen gegenereerd

| tijdstempel  | plantId_string | telemetry.tagId_string | telemetry.value_double
| ---- | ---- | ---- | ---- |
| `2020-01-22T16:38:09Z` | `9336971`| ``100231-A-A6`` |  -31,149018 |
| `2020-01-22T16:38:09Z` |`9336971` | ``100231-A-A1`` | 20,560796 |
| `2020-01-22T16:38:09Z` | `9336971` | ``100231-A-A9`` | 177 |
| `2020-01-22T16:38:09Z` | `9336971` | ``100231-A-A8`` | 420 |
| `2020-01-22T16:42:14Z` | `9336971` | ``100231-A-A7`` | -30,9918 |  
| `2020-01-22T16:42:14Z` | `9336971` | ``100231-A-A4`` | 19,960796 |

### <a name="example-c"></a>Voor beeld C

De time series-ID en de tijds tempel bevinden zich op de hoofdmap van het object \
**Tijd reeks-id van de omgeving:**`"id"`\
**Tijds tempel gebeurtenis Bron:**`"timestamp"`\
**JSON-nettolading:**

```JSON
{
    "id": "800500054755",
    "timestamp": "2020-11-01T10:00:00.000Z",
    "datapoints": [{
            "value": 120
        },
        {
            "value": 124
        }
    ]
}
```

**Resultaat in Parquet-bestand:**\
Met de bovenstaande configuratie en payload worden drie kolommen en één gebeurtenis gegenereerd

| tijdstempel  | id_string | datapoints_dynamic  
| ---- | ---- | ---- |
| `2020-11-01T10:00:00.000Z` | `800500054755`| ``[{"value": 120},{"value":124}]`` |

## <a name="next-steps"></a>Volgende stappen

* Inzicht in de [doorvoer beperkingen](./concepts-streaming-ingress-throughput-limits.md) van uw omgeving