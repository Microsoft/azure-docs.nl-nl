---
title: Ondersteuning voor lang gegevens type in Azure Time Series Insights Gen2 | Microsoft Docs
description: Ondersteuning voor lang gegevens type in Azure Time Series Insights Gen2.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.topic: conceptual
ms.date: 12/07/2020
ms.custom: dpalled
ms.openlocfilehash: 0f7ac9844bfe7eac7094d3b7fdf653e07f236599
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/07/2020
ms.locfileid: "96780821"
---
# <a name="adding-support-for-long-data-type-in-azure-time-series-insights-gen2"></a>Ondersteuning toevoegen voor lang gegevens type in Azure Time Series Insights Gen2

Het toevoegen van ondersteuning voor lang gegevens type is van invloed op de manier waarop we numerieke gegevens in Azure Time Series Insights Gen2-omgevingen opslaan en indexeren. Als u een gen1-omgeving hebt, kunt u deze wijzigingen negeren.

Vanaf 29 juni of 30 juni 2020, afhankelijk van uw regio, worden uw gegevens als **lang** en **dubbel** geïndexeerd.  Als u vragen hebt of problemen hebt met betrekking tot deze wijziging, verzendt u een ondersteunings ticket via de Azure Portal en vermeldt u deze communicatie.

Als u een van de volgende situaties ondervindt, moet u de aanbevolen wijzigingen aanbrengen:

- Voor **Beeld 1**: u gebruikt momenteel time series model-variabelen en verzendt alleen integrale gegevens typen in uw telemetriegegevens.
- Voor **Beeld 2**: u gebruikt momenteel time series model-variabelen en verzendt zowel integrale als niet-integrale gegevens typen in uw telemetriegegevens.
- Voor **Beeld 3**: u kunt Categorische-variabelen gebruiken om gehele waarden toe te wijzen aan categorieën.
- Voor **beeld 4**: u de Java script-SDK gebruikt om een aangepaste front-end-toepassing te maken.
- Voor **beeld 5**: u voldoet aan de 1.000-eigenschaps naam limiet in warme Store en verzendt zowel integrale als niet-integrale gegevens. Het aantal eigenschappen kan worden weer gegeven als een metrische waarde in het [Azure Portal](https://portal.azure.com/).

Als een van de aanvragen op u van toepassing is, brengt u wijzigingen aan in uw model. Werk de time series-expressie (TSX) bij in de definitie van de variabele met de aanbevolen wijzigingen. Update beide:

- Azure Time Series Insights Explorer
- Elke aangepaste client die gebruikmaakt van onze Api's

Afhankelijk van uw IoT-oplossing en beperkingen, hebt u mogelijk niet de zicht baarheid van de gegevens die naar uw Azure Time Series Insights Gen2-omgeving worden verzonden. Als u niet zeker weet of uw gegevens alleen integraal of zowel integraal als niet-integraal zijn, hebt u een aantal opties:

- U kunt wachten tot de functie is vrijgegeven. Bekijk vervolgens uw onbewerkte gebeurtenissen in de Explorer-gebruikers interface om te begrijpen welke eigenschappen in twee afzonderlijke kolommen zijn opgeslagen.
- U kunt preventief de aanbevolen wijzigingen aanbrengen voor alle numerieke Tags.
- U kunt een subset van gebeurtenissen tijdelijk door sturen naar opslag om uw schema beter te begrijpen en te verkennen.

Als u gebeurtenissen wilt opslaan, schakelt u [gebeurtenis vastleggen](../event-hubs/event-hubs-capture-overview.md) in voor Azure Event hubs of stuurt u een [route](../iot-hub/iot-hub-devguide-messages-d2c.md#azure-storage-as-a-routing-endpoint) van uw IOT hub naar Azure Blob-opslag.

Gegevens kunnen ook worden waargenomen via de [Event hub Verkenner](https://marketplace.visualstudio.com/items?itemName=Summer.azure-event-hub-explorer)of door de [Event processor host](../event-hubs/event-hubs-dotnet-standard-getstarted-send.md#receive-events)te gebruiken.

Als u IoT Hub gebruikt, gaat u naar [apparaat-naar-Cloud-berichten lezen van het ingebouwde eind punt](../iot-hub/iot-hub-devguide-messages-read-builtin.md) voor toegang tot het ingebouwde eind punt.

> [!NOTE]
> U kunt onderbrekingen ondervinden als u de aanbevolen wijzigingen niet aanbrengt. De betrokken Time Series Insights variabelen die worden geopend via de query-Api's of Time Series Insights Explorer, retour neren bijvoorbeeld **Null** (dat wil zeggen dat er geen gegevens worden weer gegeven in de Explorer).

## <a name="recommended-changes"></a>Aanbevolen wijzigingen

### <a name="case-1-using-time-series-model-variables-and-sending-only-integral-data-types-in-telemetry-data"></a>Voor beeld 1: Time Series-model variabelen gebruiken en alleen integrale gegevens typen verzenden in telemetriegegevens

De aanbevolen wijzigingen voor Case 1 zijn hetzelfde als voor Case 2. Volg de instructies in de sectie voor Case 2.

### <a name="case-2-using-time-series-model-variables-and-sending-both-integral-and-nonintegral-types-in-telemetry-data"></a>Voor beeld 2: Time Series-model variabelen gebruiken en zowel integrale als niet-integrale typen in telemetriegegevens verzenden

Als u momenteel telemetriegegevens van een geheel getal verzendt, worden uw gegevens onderverdeeld in twee kolommen:

- **propertyValue_double**
- **propertyValue_long**

De gegevens van het gehele getal worden wegge schreven naar **propertyValue_long**. Eerder opgenomen (en toekomstige opgenomen) numerieke gegevens in **propertyValue_double** worden niet gekopieerd.

Als u gegevens over deze twee kolommen wilt opvragen voor de eigenschap **propertyValue** , moet u de functie **Coalesce ()** SCALAIR gebruiken in uw TSX. De functie accepteert argumenten van hetzelfde **gegevens type** en retourneert de eerste niet-null-waarde in de argumenten lijst. Zie [Azure time series Insights Gen2 Data Access-concepten](/rest/api/time-series-insights/reference-time-series-expression-syntax#other-functions)voor meer informatie.

#### <a name="variable-definition-in-tsx---numeric"></a>Variabele definitie in TSX-numeriek

*Vorige variabele definitie:*

[![Scherm afbeelding toont het dialoog venster een nieuwe variabele toevoegen voor de variabele PropertyValue en Numeriek.](media/time-series-insights-long-data-type/var-def-previous.png)](media/time-series-insights-long-data-type/var-def-previous.png#lightbox)

*Nieuwe definitie van variabele:*

[![Scherm afbeelding toont het dialoog venster een nieuwe variabele toevoegen voor de variabele PropertyValue met een aangepaste waarde, numeriek.](media/time-series-insights-long-data-type/var-def.png)](media/time-series-insights-long-data-type/var-def.png#lightbox)

U kunt ook **Coalesce ($Event. PropertyValue. double, toDouble ($Event. PropertyValue. Long))** gebruiken als de aangepaste [Time Series-expressie](/rest/api/time-series-insights/reference-time-series-expression-syntax).

#### <a name="inline-variable-definition-using-tsx-query-apis---numeric"></a>Definitie van inline-variabele met behulp van TSX-query-Api's-numeriek

*Vorige variabele definitie:*

```JSON
"PropertyValueVariable": {

    "kind": "numeric",

    "value": {

        "tsx": "$event.propertyValue.Double"

    },

    "filter": null,

    "aggregation": {

        "tsx": "avg($value)"
    }
}
```

*Nieuwe definitie van variabele:*

```JSON
"PropertyValueVariable ": {

    "kind": "numeric",

    "value": {

        "tsx": "coalesce($event.propertyValue.Long, toLong($event.propertyValue.Double))"

    },

    "filter": null,

    "aggregation": {

        "tsx": "avg($value)"
    }
}
```

U kunt ook **Coalesce ($Event. PropertyValue. double, toDouble ($Event. PropertyValue. Long))** gebruiken als de aangepaste [Time Series-expressie](/rest/api/time-series-insights/reference-time-series-expression-syntax).

> [!NOTE]
> U wordt aangeraden deze variabelen bij te werken op alle locaties die kunnen worden gebruikt. Deze locaties zijn onder andere tijdreeks model, opgeslagen query's en Power BI connector query's.

### <a name="case-3-using-categorical-variables-to-map-integer-values-to-categories"></a>Voor beeld 3: Categorische-variabelen gebruiken om gehele waarden toe te wijzen aan categorieën

Als u momenteel Categorische variabelen gebruikt waarmee gehele waarden worden toegewezen aan categorieën, zult u waarschijnlijk de functie **toLong** gebruiken om gegevens van het type **Double** te converteren naar een **lang** type. Net als case 1 en 2 moet u de kolommen met **dubbele** en **lange** **gegevens typen** samenvoegd.

#### <a name="variable-definition-in-time-series-explorer---categorical"></a>Variabele definitie in time series Explorer-categorische

*Vorige variabele definitie:*

[![Scherm afbeelding toont het dialoog venster een nieuwe variabele toevoegen voor de variabele PropertyValue, categorische.](media/time-series-insights-long-data-type/var-def-cat-previous.png)](media/time-series-insights-long-data-type/var-def-cat-previous.png#lightbox)

*Nieuwe definitie van variabele:*

[![Scherm afbeelding toont het dialoog venster een nieuwe variabele toevoegen voor de variabele PropertyValue met een aangepaste waarde, categorische.](media/time-series-insights-long-data-type/var-def-cat.png)](media/time-series-insights-long-data-type/var-def-cat.png#lightbox)

U kunt ook **Coalesce ($Event. PropertyValue. double, toDouble ($Event. PropertyValue. Long))** gebruiken als de aangepaste [Time Series-expressie](/rest/api/time-series-insights/preview#time-series-expression-and-syntax).

Categorische-variabelen vereisen nog steeds een waarde van het type geheel getal. Het **gegevens type** van alle argumenten in **Coalesce ()** moet van het type **Long** zijn in de aangepaste [Time Series-expressie.](/rest/api/time-series-insights/reference-time-series-expression-syntax)

#### <a name="inline-variable-definition-using-tsx-query-apis---categorical"></a>Definitie van inline-variabele met behulp van TSX-query-Api's-categorische

*Vorige variabele definitie:*

```JSON
"PropertyValueVariable_Long": {

    "kind": "categorical",

    "value": {

        "tsx": "tolong($event.propertyValue.Double)"

    },

    "categories": [

    {
        "label": "Good",

        "values": [0, 1, 2 ]

    },

    {

        "label": "Bad",

        "values": [ 3, 4 ]

    } ],

    "defaultCategory": {

        "label": "Unknown"

    }
}
```

*Nieuwe definitie van variabele:*

```JSON
"PropertyValueVariable_Long": {

    "kind": "categorical",

    "value": {

        "tsx": "coalesce($event.propertyValue.Long, tolong($event.propertyValue.Double))"

    },

    "categories": [

    {
        "label": "Good",

        "values": [0, 1, 2 ]

    },

    {

        "label": "Bad",

        "values": [ 3, 4 ]

    } ],

    "defaultCategory": {

        "label": "Unknown"

    }
}
```

Categorische-variabelen vereisen nog steeds een waarde van het type geheel getal. Het **gegevens type** van alle argumenten in **Coalesce ()** moet van het type **Long** zijn in de aangepaste [Time Series-expressie](/rest/api/time-series-insights/reference-time-series-expression-syntax).

> [!NOTE]
> U wordt aangeraden deze variabelen bij te werken op alle locaties die kunnen worden gebruikt. Deze locaties zijn onder andere tijdreeks model, opgeslagen query's en Power BI connector query's.

### <a name="case-4-using-the-javascript-sdk-to-build-a-custom-front-end-application"></a>Case 4: de Java script-SDK gebruiken om een aangepaste front-end-toepassing te maken

Als u wordt beïnvloed door cases 1 tot en met 3 en aangepaste toepassingen bouwt, moet u uw query's bijwerken om de functie **Coalesce ()** te gebruiken, zoals wordt getoond in de voor gaande voor beelden.

### <a name="case-5-nearing-warm-store-1000-property-limit"></a>Case 5: de eigenschaps limiet van de warme Store 1.000 bijna bereikt

Als u een warme winkel gebruiker bent met een groot aantal eigenschappen en denkt dat deze wijziging uw omgeving zou pushen over de 1.000 warme Store-eigenschap-naam limiet, dient u een ondersteunings ticket in te dienen via de Azure Portal en deze communicatie te vermelden.

## <a name="next-steps"></a>Volgende stappen

- Bekijk de volledige lijst met [ondersteunde gegevens typen](concepts-supported-data-types.md).
