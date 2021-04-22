---
title: Modelvariabelen - Azure Time Series Insights Gen2-| Microsoft Docs
description: Modelvariabelen
author: shreyasharmamsft
ms.author: shresha
ms.service: time-series-insights
ms.topic: conceptual
ms.date: 01/22/2021
ms.openlocfilehash: 01184a4eb2aac81bbcabcebf89ef10afeabddbe8
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107872961"
---
# <a name="time-series-model-variables"></a>Tijdreeksmodelvariabelen

In dit artikel worden de Time Series Model-variabelen beschreven die formule- en berekeningsregels voor gebeurtenissen opgeven.

Elke variabele kan een van de volgende drie typen zijn: *numeriek,* *categorisch* en *geaggregeerd.*

* **Numerieke typen** werken met doorlopende numerieke waarden.
* **Categorische** soorten werken met een gedefinieerde set discrete waarden.
* **Aggregatiesoorten** combineren meerdere variabelen van één type (alle numerieke of alle categorische variabelen).

In de volgende tabel ziet u welke eigenschappen relevant zijn voor elk type variabele.

[![Tabel met variabele time series-modellen](media/v2-update-tsm/time-series-model-variable-table.png)](media/v2-update-tsm/time-series-model-variable-table.png#lightbox)

## <a name="numeric-variables"></a>Numerieke variabelen

| Variabele eigenschap | Description |
| --- | ---|
| Variabelefilter | Filters zijn optionele voorwaardelijke clausules om het aantal rijen te beperken dat wordt overwogen voor berekeningen. |
| Variabele waarde | Telemetriewaarden die worden gebruikt voor berekeningen die afkomstig zijn van het apparaat of de sensoren of worden getransformeerd met behulp van Time Series-expressies. Variabelen van het type Numeriek moeten of zijn `Double` om overeen te komen met het `Long` gegevenstype van de binnenkomende gegevens.|
| Variabele interpolatie | Interpolatie geeft aan hoe een signaal moet worden gereconstrueerd met behulp van bestaande gegevens. *De* opties *Stap en* Lineaire interpolatie zijn beschikbaar voor numerieke variabelen. |
| Variabele aggregatie | Voer berekeningen uit via de [ondersteunde aggregatiefuncties voor numerieke variabele soorten](/rest/api/time-series-insights/reference-time-series-expression-syntax#numeric-variable-kind). |

Variabelen voldoen aan het volgende JSON-voorbeeld:

```JSON
"Interpolated Speed": {
  "kind": "numeric",
  "value": {
    "tsx": "$event['Speed-Sensor'].Double"
  },
  "filter": null,
  "interpolation": {
    "kind": "step",
    "boundary": {
      "span": "P1D"
    }
  },
  "aggregation": {
    "tsx": "right($value)"
  }
}
```

## <a name="categorical-variables"></a>Categorische variabelen

| Variabele eigenschap | Description |
| --- | ---|
| Variabelefilter | Filters zijn optionele voorwaardelijke clausules om het aantal rijen te beperken dat wordt overwogen voor berekeningen. |
| Variabele waarde | Telemetriewaarden die worden gebruikt voor berekeningen die afkomstig zijn van het apparaat of de sensoren. Categorische soorten variabelen moeten of zijn `Long` om overeen te komen met het `String` gegevenstype van de binnenkomende gegevens. |
| Variabele interpolatie | Interpolatie geeft aan hoe een signaal moet worden gereconstrueerd met behulp van bestaande gegevens. De *optie Stapinterpolatie* is beschikbaar voor categorische variabelen. |
| Variabelecategorieën | Categorieën maken een toewijzing tussen de waarden die afkomstig zijn van het apparaat of de sensoren aan een label. |
| Standaardcategorie voor variabelen | De standaardcategorie is voor alle waarden die niet worden weergegeven in de eigenschap 'categories'. |

Variabelen voldoen aan het volgende JSON-voorbeeld:

```JSON
"Status": {
  "kind": "categorical",
  "value": {
     "tsx": "$event.Status.Long"
},
  "interpolation": {
    "kind": "step",
    "boundary": {
      "span" : "PT1M"
    }
  },
  "categories": [
    {
      "values": [0, 1, 2, 3],
      "label": "Good"
    },
    {
      "values": [4],
      "label": "Bad"
    }
  ],
  "defaultCategory": {
    "label": "Not Applicable"
  }
}
```

## <a name="aggregate-variables"></a>Variabelen aggregeren

| Variabele eigenschap | Description |
| --- | ---|
| Variabelefilter | Filters zijn optionele voorwaardelijke clausules om het aantal rijen te beperken dat wordt overwogen voor berekeningen. |
| Variabele aggregatie | Voer berekeningen uit via de ondersteunde [aggregatiefuncties voor Aggregatievariabelen.](/rest/api/time-series-insights/reference-time-series-expression-syntax#aggregate-variable-kind) |

Variabelen voldoen aan het volgende JSON-voorbeeld:

```JSON
"Speed Range": {
  "kind": "aggregate",
  "filter": null,
  "aggregation": {
    "tsx": "max($event.Speed.Double) - min($event.Speed.Double)"
  }
}
```

Variabelen worden opgeslagen in de typedefinitie van een tijdreeksmodel en kunnen inline worden opgegeven via API's om de opgeslagen definitie te overschrijven of aan te vullen.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [Time Series Model.](./concepts-model-overview.md)

* Lees meer over het inline definiëren van variabelen met behulp van [Query-API's.](./concepts-query-overview.md)
