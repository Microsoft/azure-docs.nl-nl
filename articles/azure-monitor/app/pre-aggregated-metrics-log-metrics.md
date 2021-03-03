---
title: Op logboek gebaseerde en vooraf geaggregeerde metrische gegevens in Azure-toepassing inzichten | Microsoft Docs
description: Waarom het gebruik van op logboek gebaseerd versus vooraf geaggregeerde metrische gegevens in Azure-toepassing inzichten
ms.topic: conceptual
author: vgorbenko
ms.author: vitalyg
ms.date: 09/18/2018
ms.reviewer: mbullwin
ms.openlocfilehash: acbe535d740eb527d165be1675f31e759851a987
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101717822"
---
# <a name="log-based-and-pre-aggregated-metrics-in-application-insights"></a>Vooraf samengevoegde metrische gegevens op basis van logboeken in Application Insights

In dit artikel wordt het verschil beschreven tussen ' traditionele ' Application Insights metrische gegevens die zijn gebaseerd op Logboeken en vooraf samengestelde metrische gegevens. Beide soorten metrische gegevens zijn beschikbaar voor de gebruikers van Application Insights en elk een unieke waarde voor het controleren van de status van toepassingen, diagnostische gegevens en analyses. De ontwikkel aars die toepassingen maken, kunnen bepalen welk type metrische gegevens het meest geschikt zijn voor een bepaald scenario, afhankelijk van de grootte van de toepassing, het verwachte volume van de telemetrie en zakelijke vereisten voor de precisie van metrische gegevens en waarschuwingen.

## <a name="log-based-metrics"></a>Metrische gegevens op basis van een logboek

In het verleden was het toepassings model voor telemetrie in Application Insights alleen gebaseerd op een klein aantal vooraf gedefinieerde typen gebeurtenissen, zoals aanvragen, uitzonde ringen, afhankelijkheids aanroepen, pagina weergaven, enzovoort. Ontwikkel aars kunnen de SDK gebruiken om deze gebeurtenissen hand matig te verzenden (door code te schrijven die de SDK expliciet aanroept) of ze kunnen vertrouwen op het automatisch verzamelen van gebeurtenissen van automatische instrumentatie. In beide gevallen slaat de back-end van de Application Insights alle verzamelde gebeurtenissen op als logboeken en de Application Insights-blades in de Azure Portal fungeren als een analytisch en diagnostisch hulp programma voor het visualiseren van op gebeurtenissen gebaseerde gegevens uit Logboeken.

Het gebruik van Logboeken voor het bewaren van een complete set gebeurtenissen kan een uitstekende analytische en diagnose waarde bieden. U kunt bijvoorbeeld een exacte telling van aanvragen naar een bepaalde URL verkrijgen met het aantal afzonderlijke gebruikers dat deze aanroepen heeft gedaan. U kunt ook gedetailleerde diagnostische traceringen ophalen, inclusief uitzonde ringen en afhankelijkheids aanroepen voor elke gebruikers sessie. Met dit type informatie kan de zicht baarheid van de toepassings status en het gebruik aanzienlijk worden verbeterd, waardoor de tijd die nodig is om de oorzaak van problemen met een app op te sporen, te verminderen.

Op hetzelfde moment kan het verzamelen van een complete set gebeurtenissen niet praktisch zijn (of zelfs onmogelijk) voor toepassingen die een grote hoeveelheid telemetrie genereren. Voor situaties waarin het volume van gebeurtenissen te hoog is, Application Insights de implementatie van verschillende telemetrie-volume reductie technieken, zoals [steek proeven](./sampling.md) en [filters](./api-filtering-sampling.md) , waarmee het aantal verzamelde en opgeslagen gebeurtenissen wordt verminderd. Het verlagen van het aantal opgeslagen gebeurtenissen vermindert ook de nauw keurigheid van de metrische gegevens die, achter de schermen, query's moeten uitvoeren op de gebeurtenissen die in Logboeken zijn opgeslagen.

> [!NOTE]
> In Application Insights worden de metrische gegevens die zijn gebaseerd op de query-tijd aggregatie van gebeurtenissen en metingen die in Logboeken zijn opgeslagen, metrische gegevens op basis van een logboek genoemd. Deze metrische gegevens hebben doorgaans veel dimensies van de gebeurtenis eigenschappen, waardoor ze voor analyses worden gemaakt, maar de nauw keurigheid van deze metrische gegevens wordt negatief beïnvloed door steek proeven en filters.

## <a name="pre-aggregated-metrics"></a>Vooraf samengestelde metrische gegevens

Naast de metrische gegevens op basis van een logboek, heeft het Application Insights team een open bare preview-versie van metrische gegevens die is opgeslagen in een gespecialiseerde opslag plaats die is geoptimaliseerd voor tijd reeksen, in het eind 2018. De nieuwe metrische gegevens worden niet langer bewaard als afzonderlijke gebeurtenissen met een groot aantal eigenschappen. In plaats daarvan worden ze opgeslagen als vooraf geaggregeerde time series en alleen met belang rijke dimensies. Dit zorgt ervoor dat de nieuwe metrische gegevens zich op het moment van de query kunnen voordoen: het ophaal proces verloopt veel sneller en vereist minder reken kracht. Hierdoor kunnen nieuwe scenario's, zoals [bijna realtime waarschuwingen over dimensies van metrische gegevens](../alerts/alerts-metric-near-real-time.md), snellere [Dash boards](./overview-dashboard.md)en meer, worden ingeschakeld.

> [!IMPORTANT]
> Beide, op logboek gebaseerde en vooraf geaggregeerde metrische gegevens bestaan naast elkaar in Application Insights. Om de twee te onderscheiden, worden in de Application Insights UX de vooraf samengestelde metrische gegevens nu ' standaard meet waarden (preview) ' genoemd, terwijl de traditionele metrische gegevens van de gebeurtenissen zijn gewijzigd in metrische gegevens op basis van een logboek.

De nieuwere Sdk's ([Application Insights 2,7](https://www.nuget.org/packages/Microsoft.ApplicationInsights/2.7.2) SDK of hoger voor .net) vooraf samengestelde metrische gegevens tijdens het verzamelen. Dit geldt voor  [standaard metrische gegevens die standaard worden verzonden](../essentials/metrics-supported.md#microsoftinsightscomponents) , zodat de nauw keurigheid niet wordt beïnvloed door steek proeven of filters. Het is ook van toepassing op aangepaste metrische gegevens die worden verzonden met behulp van [GetMetric](./api-custom-events-metrics.md#getmetric) , wat resulteert in minder gegevensopname en lagere kosten.

Voor de Sdk's die geen vooraf aggregatie implementeren (dat wil zeggen, oudere versies van Application Insights Sdk's of voor browser instrumentatie), vult de Application Insights back-end de nieuwe metrische gegevens in door het samen voegen van de gebeurtenissen die worden ontvangen door het Application Insights eind punt voor gebeurtenis verzameling. Dit betekent dat u niet profiteert van de gereduceerde hoeveelheid gegevens die via de kabel wordt verzonden, maar u kunt nog steeds gebruikmaken van de vooraf geaggregeerde metrieken en betere prestaties en ondersteuning bieden voor de nabije real-time driedimensionale waarschuwing met Sdk's die geen prestatistische metrische gegevens tijdens de verzameling hebben.

Het is belang rijk dat het verzamelings eindpunt gebeurtenissen vóór opname samenvoegt, wat betekent dat de [steek proeven voor opname](./sampling.md) nooit van invloed zijn op de nauw keurigheid van vooraf geaggregeerde metrische gegevens, ongeacht de SDK-versie die u met uw toepassing gebruikt.  

### <a name="sdk-supported-pre-aggregated-metrics-table"></a>Door SDK ondersteunde tabel met vooraf samengestelde metrische gegevens

| Huidige productie-Sdk's | Standaard metrische gegevens (vooraf aggregatie van SDK) | Aangepaste metrische gegevens (zonder vooraf aggregatie van de SDK) | Aangepaste metrische gegevens (met vooraf aggregatie van SDK)|
|------------------------------|-----------------------------------|----------------------------------------------|---------------------------------------|
| .NET core en .NET Framework | Ondersteund (V 2.13.1 +)| Ondersteund via [TrackMetric](api-custom-events-metrics.md#trackmetric)| Ondersteund (V 2.7.2 +) via [GetMetric](get-metric.md) |
| Java                         | Niet ondersteund       | Ondersteund via [TrackMetric](api-custom-events-metrics.md#trackmetric)| Niet ondersteund                           |
| Node.js                      | Niet ondersteund       | Ondersteund via  [TrackMetric](api-custom-events-metrics.md#trackmetric)| Niet ondersteund                           |
| Python                       | Niet ondersteund       | Ondersteund                                 | Gedeeltelijk ondersteund via [Opentellingen. statistieken](opencensus-python.md#metrics) |  

> [!NOTE]
>  De metrische gegevens implementatie voor python met opentellingen. stats wijkt af van GetMetric. Raadpleeg [de python-documentatie over metrische](./opencensus-python.md#metrics)gegevens voor meer informatie.

### <a name="codeless-supported-pre-aggregated-metrics-table"></a>Niet-ondersteunde, vooraf geaggregeerde metrische tabel met code ring

| Huidige productie-Sdk's | Standaard metrische gegevens (vooraf aggregatie van SDK) | Aangepaste metrische gegevens (zonder vooraf aggregatie van de SDK) | Aangepaste metrische gegevens (met vooraf aggregatie van SDK)|
|-------------------------|--------------------------|-------------------------------------------|-----------------------------------------|
| ASP.NET                 | Ondersteund <sup> 1<sup>    | Niet ondersteund                             | Niet ondersteund                           |
| ASP.NET Core            | Ondersteund <sup> 2<sup>    | Niet ondersteund                             | Niet ondersteund                           |
| Java                    | Niet ondersteund            | Niet ondersteund                             | [Ondersteund](java-in-process-agent.md#metrics) |
| Node.js                 | Niet ondersteund            | Niet ondersteund                             | Niet ondersteund                           |

1. ASP.NET-koppeling op App Service worden alleen metrische gegevens in de modus ' Full ' verzonden. ASP.NET is gekoppeld aan App Service, VM/VMSS, en on-premises maakt standaard metrieken zonder dimensies. SDK is vereist voor alle dimensies.
2. ASP.NET Core zonder code aan te sluiten op App Service worden standaard metrische gegevens met maat eenheden verzonden. SDK is vereist voor alle dimensies.

## <a name="using-pre-aggregation-with-application-insights-custom-metrics"></a>Vooraf aggregatie gebruiken met Application Insights aangepaste metrische gegevens

U kunt vooraf aggregatie met aangepaste metrische gegevens gebruiken. De twee belangrijkste voor delen zijn de mogelijkheid om een dimensie van een aangepaste metriek te configureren en te waarschuwen en de hoeveelheid gegevens die vanuit de SDK wordt verzonden naar het eind punt van de Application Insights verzameling te verminderen.

Er zijn verschillende [manieren om aangepaste metrische gegevens van de Application INSIGHTS SDK te verzenden](./api-custom-events-metrics.md). Als uw versie van de SDK de [GetMetric-en TrackValue](./api-custom-events-metrics.md#getmetric) -methoden biedt, is dit de voorkeurs methode voor het verzenden van aangepaste metrische gegevens, omdat in dit geval vooraf aggregatie plaatsvindt binnen de SDK, niet alleen het volume van de in azure opgestelde Data, maar ook het volume van de gegevens die van de sdk naar Application Insights worden verzonden. Als dat niet het geval is, gebruikt u de methode [trackMetric](./api-custom-events-metrics.md#trackmetric)  , waarmee metrische gebeurtenissen vooraf worden geaggregeerd tijdens gegevens opname.

## <a name="custom-metrics-dimensions-and-pre-aggregation"></a>Aangepaste dimensies voor metrische gegevens en vooraf aggregatie

Alle metrische gegevens die u verzendt met behulp van [trackMetric](./api-custom-events-metrics.md#trackmetric) -of [GetMetric-en TrackValue](./api-custom-events-metrics.md#getmetric) -API-aanroepen, worden automatisch opgeslagen in Logboeken en metrische archieven. Terwijl de op Logboeken gebaseerde versie van uw aangepaste metrische gegevens altijd alle dimensies behoudt, wordt de vooraf geaggregeerde versie van de metriek standaard zonder dimensies opgeslagen. U kunt het verzamelen van dimensies met aangepaste metrische gegevens inschakelen op het tabblad [gebruik en geschatte kosten](./pricing.md) door het selectie vakje waarschuwingen op aangepaste metrische dimensies inschakelen in te scha kelen. 

![Gebruik en geraamde kosten](./media/pre-aggregated-metrics-log-metrics/001-cost.png)

## <a name="why-is-collection-of-custom-metrics-dimensions-turned-off-by-default"></a>Waarom is het verzamelen van dimensies met aangepaste metrische gegevens standaard uitgeschakeld?

Het verzamelen van dimensies met aangepaste metrische gegevens is standaard uitgeschakeld omdat in de toekomst aangepaste metrische gegevens worden opgeslagen met dimensies die afzonderlijk van Application Insights worden gefactureerd, terwijl de opslag van de niet-dimensionale aangepaste metrische gegevens gratis blijft (tot een quotum). Op onze officiële [prijs pagina](https://azure.microsoft.com/pricing/details/monitor/)vindt u meer informatie over de aanstaande wijzigingen in het prijs model.

## <a name="creating-charts-and-exploring-log-based-and-standard-pre-aggregated-metrics"></a>Grafieken maken en op logboek gebaseerde en standaard vooraf samengestelde metrische gegevens verkennen

Gebruik [Azure Monitor Metrics Explorer](../essentials/metrics-getting-started.md) voor het uitzetten van grafieken van vooraf geaggregeerde en op Logboeken gebaseerde metrische gegevens en om Dash boards te ontwerpen met grafieken. Nadat u de gewenste Application Insights resource hebt geselecteerd, gebruikt u de naam ruimte kiezer om te scha kelen tussen Standard (preview) en metrische gegevens op basis van een logboek of selecteert u een aangepaste metrische naam ruimte:

![Metrische naam ruimte](./media/pre-aggregated-metrics-log-metrics/002-metric-namespace.png)

## <a name="pricing-models-for-application-insights-metrics"></a>Prijs modellen voor Application Insights metrische gegevens

Het opnemen van gegevens in Application Insights, hetzij op basis van een logboek of vooraf geaggregeerd, genereert kosten op basis van de grootte van de opgenomen gegevens, zoals [hier](./pricing.md#pricing-model)wordt beschreven. Uw aangepaste metrische gegevens, inclusief alle afmetingen, worden altijd opgeslagen in het Application Insights logboek archief; Daarnaast wordt een vooraf geaggregeerde versie van uw aangepaste metrische gegevens (zonder dimensies) standaard doorgestuurd naar de metrische opslag.

Als u de optie [waarschuwingen inschakelen op aangepaste metrische dimensies inschakelt](#custom-metrics-dimensions-and-pre-aggregation) om alle dimensies van de vooraf geaggregeerde metrische gegevens in het metrische archief op te slaan, kunt u **extra** kosten genereren op basis van de [prijzen voor aangepaste metrische gegevens](https://azure.microsoft.com/pricing/details/monitor/).

## <a name="next-steps"></a>Volgende stappen

* [Bijna realtime waarschuwingen](../alerts/alerts-metric-near-real-time.md)
* [GetMetric en TrackValue](./api-custom-events-metrics.md#getmetric)
