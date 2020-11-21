---
title: Ondersteunde gegevens typen-Azure Time Series Insights Gen2 | Microsoft Docs
description: Meer informatie over de ondersteunde gegevens typen in Azure Time Series Insights Gen2.
author: lyrana
ms.author: lyhughes
manager: deepakpalled
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 10/01/2020
ms.openlocfilehash: aace24485939dcf22c8d40011b9dc445c68f31c9
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95020807"
---
# <a name="supported-data-types"></a>Ondersteunde gegevenstypen

De volgende tabel bevat de gegevens typen die worden ondersteund door Azure Time Series Insights Gen2

| Gegevenstype | Beschrijving | Voorbeeld | [Syntaxis van de expressie time series](/rest/api/time-series-insights/reference-time-series-expression-syntax) | Naam van eigenschaps kolom in Parquet
|---|---|---|---|---|
| **booleaans** | Een gegevens type met een van de twee statussen: `true` of `false` . | `"isQuestionable" : true` | `$event.isQuestionable.Bool` of `$event['isQuestionable'].Bool` | `isQuestionable_bool`
| **datum/tijd** | Vertegenwoordigt een onmiddellijke tijd, meestal uitgedrukt als een datum en tijd van de dag. Uitgedrukt in [ISO 8601](https://www.iso.org/iso-8601-date-and-time-format.html) -indeling. Datetime-eigenschappen worden altijd opgeslagen in UTC-indeling. Tijd zone-offsets worden toegepast en vervolgens wordt de waarde opgeslagen in UTC. Zie [deze](concepts-streaming-ingestion-event-sources.md#event-source-timestamp) sectie voor meer informatie over de omgeving tijds tempel eigenschap en datum/tijd verschuivingen | `"eventProcessedLocalTime": "2020-03-20T09:03:32.8301668Z"` |  Als ' eventProcessedLocalTime ' de tijds tempel van de gebeurtenis bron is: `$event.$ts` . Als het een andere JSON-eigenschap is: `$event.eventProcessedLocalTime.DateTime` of `$event['eventProcessedLocalTime'].DateTime` | `eventProcessedLocalTime_datetime`
| **double** | Een 64-bits getal met dubbele precisie  | `"value": 31.0482941` | `$event.value.Double` of `$event['value'].Double` |  `value_double`
| **long** | Een ondertekend 64-bits geheel getal  | `"value" : 31` | `$event.value.Long` of `$event['value'].Long` |  `value_long`
| **tekenreeksexpressie** | Tekst waarden moeten bestaan uit geldige UTF-8. Null en lege teken reeksen worden op dezelfde manier behandeld. |  `"site": "DIM_MLGGG"`| `$event.site.String` of `$event['site'].String`| `site_string`
| **dynamisch** | Een complex (niet-primitieve) type dat bestaat uit een matrix of eigenschappen verzameling (Dictionary). Momenteel worden alleen stringified JSON-matrices van primitieven of matrices met objecten die de TS-ID of tijds tempel eigenschap (s) bevatten, opgeslagen als dynamisch. Lees dit [artikel](./concepts-json-flattening-escaping-rules.md) voor meer informatie over hoe objecten worden afgevlakt en matrices kunnen niet worden gerollt. Payload-eigenschappen die zijn opgeslagen als dit type zijn alleen toegankelijk door te selecteren `Explore Events` in de time series Insights Explorer om onbewerkte gebeurtenissen weer te geven of via de [`GetEvents`](/rest/api/time-series-insights/dataaccessgen2/query/execute#getevents) query-API voor het parseren van de client. |  `"values": "[197, 194, 189, 188]"` | Het verwijzen naar dynamische typen in een time series-expressie wordt nog niet ondersteund | `values_dynamic`

> [!NOTE]
> waarden van 64 bits geheel getal worden ondersteund, maar het grootste aantal dat de Azure Time Series Insights Explorer veilig kan uitdrukken, is 9.007.199.254.740.991 (2 ^ 53-1) vanwege beperkingen van Java script. Als u in het bovenstaande gegevens model met getallen werkt, kunt u de grootte verminderen door een variabele voor de [tijd reeks model](./concepts-variables.md#numeric-variables) te maken en de waarde te [converteren](/rest/api/time-series-insights/reference-time-series-expression-syntax#conversion-functions) .

> [!NOTE]
> **Teken reeks** type is niet nullbaar:
>
> * Een [tijdreeks expressie (TSX)](/rest/api/time-series-insights/reference-time-series-expression-syntax) in een [Time Series-query](/rest/api/time-series-insights/reference-query-apis) die de waarde van een lege teken reeks (**' '**) op basis van **Null** vergelijkt, gedraagt zich op dezelfde manier: `$event.siteid.String = NULL` is gelijk aan `$event.siteid.String = ''` .
> * De API kan **Null** -waarden retour neren, zelfs als oorspronkelijke gebeurtenissen lege teken reeksen bevatten.
> * Maak geen afhankelijkheid van **Null** -waarden in **teken reeks** kolommen om vergelijkingen of evaluaties uit te voeren. Behandel ze op dezelfde manier als lege teken reeksen.

## <a name="sending-mixed-data-types"></a>Gemengde gegevens typen verzenden

Uw Azure Time Series Insights Gen2-omgeving is sterk getypeerd. Als apparaten of Tags gegevens van verschillende typen voor een eigenschap apparaat verzenden, worden de waarden opgeslagen in twee gescheiden kolommen en moet de [functie Coalesce ()](/rest/api/time-series-insights/reference-time-series-expression-syntax#other-functions) worden gebruikt bij het definiëren van de variabele expressies van uw tijd reeks model in API-aanroepen.

De Azure Time Series Insights Explorer biedt een manier om de afzonderlijke kolommen van dezelfde eigenschap apparaat automatisch in te delen. In het onderstaande voor beeld verzendt de sensor een `PresentValue` eigenschap die zowel een Long-waarde als een double kan zijn. Als u een query wilt uitvoeren op alle opgeslagen waarden (ongeacht het gegevens type) van de `PresentValue` eigenschap, kiest `PresentValue (Double | Long)` u en de kolommen worden samengevoegd.

[![Automatische Coalesce-Verkenner](media\concepts-supported-data-types/explorer-auto-coalesce-sample.png)](media\concepts-supported-data-types/explorer-auto-coalesce-sample.png#lightbox)

## <a name="objects-and-arrays"></a>Objecten en matrices

U kunt complexe typen zoals objecten en arrays verzenden als onderdeel van de nettolading van de gebeurtenis. Geneste objecten worden afgevlakt en matrices worden opgeslagen als `dynamic` of afgevlakt om meerdere gebeurtenissen te maken, afhankelijk van uw omgevings configuratie en JSON-vorm. Meer informatie over de [JSON afvlakking en Escape-regels](./concepts-json-flattening-escaping-rules.md)

## <a name="next-steps"></a>Volgende stappen

* Lees de [regels voor json-afvlakking en-Escapes](./concepts-json-flattening-escaping-rules.md) om te begrijpen hoe gebeurtenissen worden opgeslagen.

* Inzicht in de [doorvoer beperkingen](./concepts-streaming-ingress-throughput-limits.md) van uw omgeving

* Meer informatie over [gebeurtenis bronnen](concepts-streaming-ingestion-event-sources.md) voor het opnemen van streaming-gegevens.