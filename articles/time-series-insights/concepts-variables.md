---
title: Model variabelen-Azure Time Series Insights Gen2 | Microsoft Docs
description: Model variabelen
author: shreyasharmamsft
ms.author: shresha
ms.service: time-series-insights
ms.topic: conceptual
ms.date: 01/22/2021
ms.openlocfilehash: fd08dc98fa47617bbc7c8d1fff895377837a7327
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98736731"
---
# <a name="time-series-model-variables"></a>Variabelen voor time series-model

In dit artikel worden de tijdreeks model variabelen beschreven waarmee formule-en reken regels voor gebeurtenissen worden opgegeven.

Elke variabele kan een van de volgende drie typen zijn: *numeric*, *categorische* en *aggregatie*.

* **Numerieke** soorten werken met doorlopende numerieke waarden.
* **Categorische** -soorten werken met een gedefinieerde set discrete waarden.
* Bij **gecombineerde** typen worden meerdere variabelen van één soort gecombineerd (ofwel alle numerieke ofwel alle categorische).

In de volgende tabel ziet u welke eigenschappen relevant zijn voor elk type variabele.

[![Tabel met variabele Time Series-modellen](media/v2-update-tsm/time-series-model-variable-table.png)](media/v2-update-tsm/time-series-model-variable-table.png#lightbox)

## <a name="numeric-variables"></a>Numerieke variabelen

| Eigenschap variable | Beschrijving |
| --- | ---|
| Variabele filter | Filters zijn optionele voorwaardelijke componenten voor het beperken van het aantal rijen dat wordt overwogen voor berekening. |
| Waarde van variabele | Telemetrie-waarden die worden gebruikt voor berekeningen die afkomstig zijn van het apparaat of Sens oren of worden getransformeerd met behulp van Time Series-expressies. De variabelen van een numeriek type moeten van het type *Double* zijn.|
| Variabele interpolatie | Interpolatie Hiermee geeft u op hoe een signaal opnieuw moet worden samengesteld met behulp van bestaande gegevens. Opties voor *stap* -en *lineaire* interpolatie zijn beschikbaar voor numerieke variabelen. |
| Variabele aggregatie | Voer berekeningen uit via de ondersteunde [aggregatie functies voor numerieke typen variabelen](/rest/api/time-series-insights/reference-time-series-expression-syntax#numeric-variable-kind). |

Variabelen voldoen aan het volgende JSON-voor beeld:

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

| Eigenschap variable | Beschrijving |
| --- | ---|
| Variabele filter | Filters zijn optionele voorwaardelijke componenten voor het beperken van het aantal rijen dat wordt overwogen voor berekening. |
| Waarde van variabele | Telemetrie-waarden die worden gebruikt voor de berekening die afkomstig zijn van het apparaat of de Sens oren. Categorische-variabelen moeten een *Long-waarde* of een *teken reeks* zijn. |
| Variabele interpolatie | Interpolatie Hiermee geeft u op hoe een signaal opnieuw moet worden samengesteld met behulp van bestaande gegevens. De optie interpolatie van de *stap* is beschikbaar voor Categorische-variabelen. |
| Variabelen Categorieën | Categorieën maken een toewijzing tussen de waarden die afkomstig zijn van het apparaat of Sens oren aan een label. |
| Variabele standaard categorie | De standaard categorie is voor alle waarden die niet worden toegewezen in de eigenschap ' categories '. |

Variabelen voldoen aan het volgende JSON-voor beeld:

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

## <a name="aggregate-variables"></a>Aggregatie variabelen

| Eigenschap variable | Beschrijving |
| --- | ---|
| Variabele filter | Filters zijn optionele voorwaardelijke componenten voor het beperken van het aantal rijen dat wordt overwogen voor berekening. |
| Variabele aggregatie | Voer berekeningen uit via de ondersteunde [aggregatie functies voor aggregatie typen van variabelen](/rest/api/time-series-insights/reference-time-series-expression-syntax#aggregate-variable-kind). |

Variabelen voldoen aan het volgende JSON-voor beeld:

```JSON
"Speed Range": {
  "kind": "aggregate",
  "filter": null,
  "aggregation": {
    "tsx": "max($event.Speed.Double) - min($event.Speed.Double)"
  }
}
```

Variabelen worden opgeslagen in de type definitie van een time series-model en kunnen inline via Api's worden aangelegd om de opgeslagen definitie te overschrijven of te vullen.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [Time Series-model](./concepts-model-overview.md).

* Meer informatie over het definiëren van variabelen inline met behulp van [query-api's](./concepts-query-overview.md).
