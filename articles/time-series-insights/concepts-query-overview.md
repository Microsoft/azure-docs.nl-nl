---
title: Query's uitvoeren op gegevens-Azure Time Series Insights Gen2 | Microsoft Docs
description: Gegevens query's en REST API overview in Azure Time Series Insights Gen2.
author: shreyasharmamsft
ms.author: shresha
manager: dpalled
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 01/22/2021
ms.custom: seodec18
ms.openlocfilehash: b1b055fa7f083bd8bccda16498e2894d5d67eace
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100374130"
---
# <a name="querying-data-from-azure-time-series-insights-gen2"></a>Gegevens uit Azure Time Series Insights Gen2 opvragen

Azure Time Series Insights Gen2 maakt gegevens query's op gebeurtenissen en meta gegevens die zijn opgeslagen in de omgeving via open bare Surface-Api's. Deze Api's worden ook gebruikt door de [Azure time series INSIGHTS TSI-Explorer](./concepts-ux-panels.md).

Er zijn drie primaire API-categorieën beschikbaar in Azure Time Series Insights Gen2:

* **Omgevings-api's**: met deze api's worden query's op de Azure time series Insights Gen2-omgeving zelf ingeschakeld. Deze kunnen worden gebruikt voor het verzamelen van de lijst met omgevingen waartoe de aanroeper toegang heeft en de meta gegevens van de omgeving.
* **Time series Model-Query-api's (TSM-Q)**: Hiermee kunt u bewerkingen voor maken, lezen, bijwerken en verwijderen van meta gegevens die zijn opgeslagen in het tijdreeks model van de omgeving. Deze kunnen worden gebruikt om de instanties, typen en hiërarchieën te openen en te bewerken.
* **Api's voor time series query (TSQ)**: Hiermee wordt het ophalen van gegevens over telemetrie of gebeurtenissen mogelijk gemaakt wanneer deze worden vastgelegd van de bron provider en kunnen er berekeningen en aggregaties worden uitgevoerd op de gegevens met behulp van geavanceerde scalaire en statistische functies.

Azure Time Series Insights Gen2 maakt gebruik van een op teken reeks gebaseerde expressie taal, [Time Series-expressie (TSX)](/rest/api/time-series-insights/reference-time-series-expression-syntax), voor het uitdrukken van berekeningen in [Time Series-variabelen](./concepts-variables.md).

## <a name="azure-time-series-insights-gen2-apis-overview"></a>Overzicht van Azure Time Series Insights Gen2-Api's

De volgende kern-Api's worden ondersteund.

[![Overzicht van de time series-query](media/v2-update-tsq/tsq.png)](media/v2-update-tsq/tsq.png#lightbox)

## <a name="environment-apis"></a>Omgevings-Api's

* [Omgevings-API ophalen](/rest/api/time-series-insights/management(gen1/gen2)/accesspolicies/listbyenvironment): retourneert de lijst met omgevingen waarvoor de aanroeper gemachtigd is om toegang te krijgen.
* [Beschikbaarheids-API voor omgevingen ophalen](/rest/api/time-series-insights/dataaccessgen2/query/getavailability): retourneert de verdeling van het aantal gebeurtenissen over het tijds tempel van de gebeurtenis `$ts` . Met deze API kunt u bepalen of er gebeurtenissen in de omgeving zijn door het aantal gebeurtenissen te retour neren dat is opgesplitst in tijds intervallen, indien aanwezig.
* [Event schema-API ophalen](/rest/api/time-series-insights/dataaccessgen2/query/geteventschema): retourneert de meta gegevens van het gebeurtenis schema voor een opgegeven zoek reeks. Deze API helpt bij het ophalen van alle meta gegevens en eigenschappen die beschikbaar zijn in het schema voor de opgegeven zoek reeks.

## <a name="time-series-model-query-tsm-q-apis"></a>Time Series Model-Query-Api's (TSM-Q)

De meeste van deze Api's ondersteunen de batch-uitvoerings bewerking om batch-ruwe bewerkingen in te scha kelen op meerdere time series-model entiteiten:

* [API voor model instellingen](/rest/api/time-series-insights/reference-model-apis): maakt *Get* en *patch* mogelijk voor het standaard type en de model naam van de omgeving.
* [Type-API](/rest/api/time-series-insights/reference-model-apis#types-api): Hiermee worden ruwe serie typen en de bijbehorende variabelen ingeschakeld.
* [Hiërarchieën API](/rest/api/time-series-insights/reference-model-apis#hierarchies-api): maakt ruwe op de tijdreeks hiërarchieën en de bijbehorende veld paden.
* [Instances-API](/rest/api/time-series-insights/reference-model-apis#instances-api): maakt ruwe op de tijd reeks instanties en de bijbehorende exemplaar velden. Daarnaast ondersteunt de instances-API de volgende bewerkingen:
  * [Search](/rest/api/time-series-insights/dataaccessgen2/timeseriesinstances/search): haalt een gedeeltelijke lijst met treffers op bij het zoeken naar Time Series-exemplaren op basis van instantie kenmerken.
  * [Suggereren](/rest/api/time-series-insights/dataaccessgen2/timeseriesinstances/suggest): zoekt naar een gedeeltelijke lijst met treffers op basis van instantie kenmerken en stelt suggesties op voor het zoeken naar Time Series-exemplaren.

## <a name="time-series-query-tsq-apis"></a>TSQ-Api's (Time Series query)

Deze Api's zijn beschikbaar in beide winkels (warm en koud) in onze opslag oplossing met meerdere lagen. 

* [API voor ophalen van gebeurtenissen](/rest/api/time-series-insights/dataaccessgen2/query/execute#getevents): Hiermee wordt de query en het ophalen van onbewerkte gebeurtenissen en de bijbehorende gebeurtenis tijds tempels ingeschakeld, zoals deze worden vastgelegd in azure time series Insights Gen2 van de bron provider. Met deze API kunt u onbewerkte gebeurtenissen ophalen voor een bepaalde tijd reeks-ID en zoek reeks. Deze API biedt ondersteuning voor paginering voor het ophalen van de volledige reactie gegevensset voor de geselecteerde invoer.

  > [!IMPORTANT]
  > Als onderdeel van de [aanstaande wijzigingen in JSON-afvlakking en-Escape regels](./ingestion-rules-update.md)worden matrices als **dynamisch** type opgeslagen. Payload-eigenschappen die zijn opgeslagen als dit type zijn **alleen toegankelijk via de API voor het ophalen van gebeurtenissen**.

* [Get Series API](/rest/api/time-series-insights/dataaccessgen2/query/execute#getseries): maakt het mogelijk om query's en het ophalen van berekende waarden en de bijbehorende gebeurtenis tijds tempels in te stellen door berekeningen toe te passen die worden gedefinieerd door variabelen op onbewerkte gebeurtenissen. Deze variabelen kunnen worden gedefinieerd in het time series-model of in inline van de query. Deze API biedt ondersteuning voor paginering voor het ophalen van de volledige reactie gegevensset voor de geselecteerde invoer.

* [Aggregatie](/rest/api/time-series-insights/dataaccessgen2/query/execute#aggregateseries)van de-serie: maakt het mogelijk om query's en het ophalen van geaggregeerde waarden en de bijbehorende interval tijds tempels in te stellen door berekeningen toe te passen die worden gedefinieerd door variabelen op onbewerkte gebeurtenissen. Deze variabelen kunnen worden gedefinieerd in het time series-model of in inline van de query. Deze API biedt ondersteuning voor paginering voor het ophalen van de volledige reactie gegevensset voor de geselecteerde invoer.
  
  Voor een opgegeven zoek reeks en-interval retourneert deze API een geaggregeerde respons per interval per variabele voor een tijd reeks-ID. Het aantal intervallen in de reactie gegevensset wordt berekend door epoche Ticks te tellen (het aantal milliseconden dat is verstreken sinds UNIX-epoche 1 januari, 1970) en de maat streepjes te delen door de grootte van het interval dat is opgegeven in de query.

  De tijds tempels die zijn geretourneerd in de antwoordset, zijn de grenzen van het linker interval, niet van de voor beelden van gebeurtenissen uit het interval.


### <a name="selecting-store-type"></a>Type archief selecteren

De bovenstaande Api's kunnen alleen worden uitgevoerd voor een van de twee opslag typen (koud of warm) in één aanroep. Query-URL-para meters worden gebruikt om het [Store-type](/rest/api/time-series-insights/dataaccessgen2/query/execute#uri-parameters) op te geven waarop de query moet worden uitgevoerd. 

Als er geen para meter is opgegeven, wordt de query standaard uitgevoerd in de koude opslag. Als een query een tijds bereik omvat dat uit zowel koude als warme opslag bestaat, is het raadzaam om de query om te leiden naar koude opslag voor de beste ervaring, omdat een warme archief alleen gedeeltelijke gegevens bevat. 

Met de [Azure time series Insights Explorer](./concepts-ux-panels.md) -en [Power bi-connector](./how-to-connect-power-bi.md) worden de bovenstaande api's aangeroepen en wordt automatisch de juiste storeType-para meter geselecteerd, indien van toepassing. 


## <a name="next-steps"></a>Volgende stappen

* Meer informatie over verschillende variabelen die kunnen worden gedefinieerd in het [Time Series-model](./concepts-model-overview.md).
* Meer informatie over het opvragen van gegevens uit de [Azure time series Insights Explorer](./concepts-ux-panels.md).
