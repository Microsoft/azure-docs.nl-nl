---
title: Het gebruik en de kosten voor Azure-toepassing inzichten beheren | Microsoft Docs
description: Telemetrie-volumes beheren en de kosten controleren in Application Insights.
ms.topic: conceptual
ms.custom: devx-track-dotnet
author: DaleKoetke
ms.author: dalek
ms.date: 5/7/2020
ms.reviewer: mbullwin
ms.openlocfilehash: 69ac1e82c267dee521143c4ed5f6c2be4d32e2ea
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96531323"
---
# <a name="manage-usage-and-costs-for-application-insights"></a>Gebruik en kosten van Application Insights beheren

> [!NOTE]
> In dit artikel wordt beschreven hoe u de kosten voor Application Insights begrijpt en beheert.  Een verwant artikel, het [bewaken van het gebruik en de geschatte kosten](../platform/usage-estimated-costs.md) , beschrijft het weer geven van het gebruik en de geschatte kosten in meerdere Azure-bewakings functies voor verschillende prijs modellen.

Application Insights is ontworpen om alles wat u nodig hebt om de beschik baarheid, prestaties en het gebruik van uw webtoepassingen te bewaken, of ze nu worden gehost op Azure of on-premises. Application Insights ondersteunt populaire talen en frameworks, zoals .NET, Java en Node.js, en kan worden geïntegreerd met DevOps-processen en-hulpprogram ma's zoals Azure DevOps, Jira en PagerDuty. Het is belang rijk om te begrijpen wat de kosten bepalen van het controleren van uw toepassingen. In dit artikel worden de kosten van uw toepassing gecontroleerd en wordt uitgelegd hoe u ze proactief kunt controleren en beheren.

Als u vragen hebt over de werking van de prijzen voor Application Insights, kunt u een vraag stellen op [de pagina micro soft Q&een vraag](/answers/topics/azure-monitor.html).

## <a name="pricing-model"></a>Prijsmodel

De prijzen voor [Azure-toepassing Insights][start] is een model voor **betalen naar gebruik** op basis van het gegevens volume dat is opgenomen en optioneel voor langere gegevens retentie. Elke Application Insights resource wordt in rekening gebracht als een afzonderlijke service en draagt bij aan de factuur voor uw Azure-abonnement. Gegevens volume wordt gemeten als de grootte van het niet-gecomprimeerde JSON-gegevens pakket dat door Application Insights van uw toepassing wordt ontvangen. Er worden geen gegevens volumes in rekening gebracht voor het gebruik van de [Live Metrics stream](./live-stream.md).

Voor [webtests met meerdere stappen](./availability-multistep.md) worden extra kosten in rekening gebracht. Webtests met meerdere stappen zijn webtests die een reeks acties uitvoeren. Er worden geen afzonderlijke kosten in rekening gebracht voor *ping-tests* van één pagina. Telemetrie van ping-tests en tests met meerdere stappen wordt in rekening gebracht op hetzelfde als andere telemetrie van uw app.

De Application Insights optie om [waarschuwingen in te scha kelen op aangepaste metrische dimensies](./pre-aggregated-metrics-log-metrics.md#custom-metrics-dimensions-and-pre-aggregation) kan ook extra kosten genereren, omdat dit kan leiden tot het maken van aanvullende preaggregatie gegevens. Meer [informatie](./pre-aggregated-metrics-log-metrics.md) over metrische gegevens op basis van het logboek en vooraf geaggregeerde metrieken in Application Insights en over de [prijzen](https://azure.microsoft.com/pricing/details/monitor/) voor Azure monitor aangepaste metrische gegevens.

### <a name="workspace-based-application-insights"></a>Application Insights op basis van een werk ruimte

Voor Application Insights resources die hun gegevens verzenden naar een Log Analytics-werk ruimte, een [Application Insights resources op basis](create-workspace-resource.md)van een werk ruimte genoemd, wordt de facturering voor gegevens opname en-retentie uitgevoerd door de werk ruimte waarin de Application Insights gegevens zich bevinden. Hierdoor kunnen klanten alle opties van het Log Analytics- [prijs model](../platform/manage-cost-storage.md#pricing-model) gebruiken, inclusief de capaciteits reserveringen naast betalen per gebruik. Log Analytics heeft ook meer opties voor het bewaren van gegevens, inclusief het [bewaren van gegevens](../platform/manage-cost-storage.md#retention-by-data-type). Application Insights gegevens typen in de werk ruimte 90 dagen met retentie ontvangen zonder dat er kosten in rekening worden gebracht. Gebruik van webtests en het inschakelen van waarschuwingen op aangepaste metrische dimensies wordt nog steeds gerapporteerd via Application Insights. Meer informatie over het bijhouden van gegevens opname en de retentie kosten in Log Analytics met behulp van het [gebruik en de geschatte kosten](../platform/manage-cost-storage.md#understand-your-usage-and-estimate-costs), [Azure Cost Management + facturering](../platform/manage-cost-storage.md#viewing-log-analytics-usage-on-your-azure-bill) en [log Analytics query's](#data-volume-for-workspace-based-application-insights-resources). 

## <a name="estimating-the-costs-to-manage-your-application"></a>De kosten voor het beheren van uw toepassing schatten

Als u Application Insights nog niet gebruikt, kunt u de [Azure monitor prijs calculator](https://azure.microsoft.com/pricing/calculator/?service=monitor) gebruiken om de kosten van het gebruik van Application Insights te schatten. Begin met het invoeren van ' Azure Monitor ' in het zoekvak en klik op de resulterende Azure Monitor tegel. Schuif omlaag in de pagina naar Azure Monitor en selecteer Application Insights in de vervolg keuzelijst Type.  Hier kunt u het aantal GB invoeren van de gegevens die u verwacht per maand te verzamelen. Daarom is de vraag hoeveel gegevens Application Insights het verzamelen van de controle van uw toepassing.

Er zijn twee benaderingen om dit op te lossen: gebruik van standaard bewaking en adaptieve steek proeven, die beschikbaar zijn in de ASP.NET-SDK, of schatting van uw waarschijnlijke gegevens opname op basis van wat andere vergelijk bare klanten hebben gezien.

### <a name="data-collection-when-using-sampling"></a>Gegevens verzameling bij gebruik van steek proeven

Met de [adaptieve steek proef](sampling.md#adaptive-sampling)van de ASP.NET SDK wordt het gegevens volume automatisch aangepast om binnen een opgegeven maximum frequentie aan verkeer te blijven voor standaard Application Insights bewaking. Als de toepassing een geringe hoeveelheid telemetrie produceert, zoals bij het opsporen van fouten of wegens een laag gebruik, worden de items niet verwijderd door de sampling processor zolang het volume minder is dan het geconfigureerde aantal gebeurtenissen per seconde niveau. Voor een toepassing met hoge volumes, met de standaard drempelwaarde van vijf gebeurtenissen per seconde, wordt het aantal dagelijkse gebeurtenissen door adaptieve steek proeven beperkt tot 432.000. Met een typische gemiddelde gebeurtenis grootte van 1 KB komt dit overeen met 13,4 GB telemetrie per 31-daagse maand per knoop punt dat als host fungeert voor uw toepassing (aangezien de steek proef op elk knoop punt wordt uitgevoerd). 

Voor Sdk's die geen adaptieve bemonstering ondersteunen, kunt u [opname sampling](./sampling.md#ingestion-sampling)gebruiken, die voor beelden van de gegevens die worden ontvangen door Application Insights op basis van een percentage van de gegevens die moeten worden bewaard, of een [vast aantal steek proeven voor ASP.net, ASP.net core en Java-websites](sampling.md#fixed-rate-sampling) om het verkeer te verminderen dat vanaf uw webserver en webbrowsers wordt verzonden.

### <a name="learn-from-what-similar-customers-collect"></a>Meer informatie over wat vergelijk bare klanten verzamelen

Als u in de Azure monitoring-prijs calculator voor Application Insights de functie schatting gegevens volume gebaseerd op toepassings activiteit inschakelt, kunt u invoeren over uw toepassing (aanvragen per maand en pagina weergaven per maand), voor het geval u telemetrie aan de client zijde verzamelt. vervolgens krijgt u de reken machine de gemiddelde en negen tigste percentiel hoeveelheid gegevens die worden verzameld door vergelijk bare toepassingen. Deze toepassingen omvatten het bereik van Application Insights configuratie (er zijn bijvoorbeeld standaard [steekproef](./sampling.md)waarden, sommige geen steek proeven enz.), zodat u nog steeds over het besturings element beschikt om de hoeveelheid gegevens die u onder het mediaan niveau hebt opgenomen, te verminderen met behulp van steek proeven. Maar dit is een uitgangs punt om te begrijpen wat andere, vergelijk bare klanten te zien krijgen.

## <a name="understand-your-usage-and-estimate-costs"></a>Inzicht in uw gebruik en geschatte kosten

Application Insights maakt het eenvoudig om te begrijpen wat uw kosten waarschijnlijk zijn op basis van recente gebruiks patronen. Ga naar de pagina **gebruik en geschatte kosten** in het Azure Portal om aan de slag te gaan voor de Application Insights resource:

![Prijzen kiezen](./media/pricing/pricing-001.png)

A. Controleer uw gegevens volume voor de maand. Dit omvat alle gegevens die worden ontvangen en bewaard (na [steek proeven](./sampling.md)) van de server en client-apps, en van beschikbaarheids testen.  
B. Er worden afzonderlijke kosten in rekening gebracht voor [webtests met meerdere stappen](./availability-multistep.md). (Dit omvat geen eenvoudige beschikbaarheids testen, die zijn opgenomen in de kosten van het gegevens volume.)  
C. Bekijk trends in gegevens volumes voor de afgelopen maand.  
D. [Steek proeven](./sampling.md)voor gegevens opname inschakelen.
E. Stel de dagelijkse gegevens volume limiet in.  

(Alle prijzen die in de scherm afbeeldingen in dit artikel worden weer gegeven, zijn alleen bedoeld als voor beeld. Zie [Application Insights prijzen][pricing]voor actuele prijzen in uw valuta en regio.)

Als u uw Application Insights-gebruik dieper wilt onderzoeken, opent u de pagina **metrische** gegevens, voegt u de metriek toe met de naam ' data Point volume ' en selecteert u vervolgens de optie *splitsing Toep assen* om de gegevens te splitsen op het type telemetrie-item.

Application Insights kosten worden toegevoegd aan uw Azure-factuur. U kunt de details van uw Azure-factuur bekijken in het gedeelte **Cost Management en facturering** van het Azure portal of in de [Azure-facturerings Portal](https://account.windowsazure.com/Subscriptions).  [Hieronder vindt](#viewing-application-insights-usage-on-your-azure-bill) u meer informatie over het gebruik van deze voor Application Insights. 

![Selecteer in het menu links facturering](./media/pricing/02-billing.png)

### <a name="using-data-volume-metrics"></a>Gegevens volume metrieken gebruiken
<a id="understanding-ingested-data-volume"></a>

Als u meer wilt weten over uw gegevens volumes, selecteert u de **metrieken** voor uw Application Insights resource, voegt u een nieuwe grafiek toe. Voor de grafiek metriek, onder **metrische gegevens op basis van een logboek**, selecteert u het **gegevens punt volume**. Klik op **splitsing Toep assen** en selecteer Groeperen op **`Telemetryitem` type**.

![Metrische gegevens gebruiken om te kijken naar het data volume](./media/pricing/10-billing.png)

### <a name="queries-to-understand-data-volume-details"></a>Query's om details van gegevens volumes te begrijpen

Er zijn twee benaderingen voor het onderzoeken van gegevens volumes voor Application Insights. De eerste gebruikt geaggregeerde informatie in de `systemEvents` tabel en de tweede gebruikt de `_BilledSize` eigenschap, die beschikbaar is voor elke opgenomen gebeurtenis. `systemEvents` heeft geen informatie over de grootte van de gegevens voor [op werk ruimte gebaseerde toepassing-inzichten](#data-volume-for-workspace-based-application-insights-resources).

#### <a name="using-aggregated-data-volume-information"></a>Informatie over geaggregeerd gegevens volume gebruiken

U kunt bijvoorbeeld de `systemEvents` tabel gebruiken om het gegevens volume dat in de afgelopen 24 uur is opgenomen weer te geven met de query:

```kusto
systemEvents
| where timestamp >= ago(24h)
| where type == "Billing"
| extend BillingTelemetryType = tostring(dimensions["BillingTelemetryType"])
| extend BillingTelemetrySizeInBytes = todouble(measurements["BillingTelemetrySize"])
| summarize sum(BillingTelemetrySizeInBytes)
```

Als u een grafiek van het gegevens volume (in bytes) per gegevens type voor de afgelopen 30 dagen wilt weer geven, kunt u het volgende gebruiken:

```kusto
systemEvents
| where timestamp >= startofday(ago(30d))
| where type == "Billing"
| extend BillingTelemetryType = tostring(dimensions["BillingTelemetryType"])
| extend BillingTelemetrySizeInBytes = todouble(measurements["BillingTelemetrySize"])
| summarize sum(BillingTelemetrySizeInBytes) by BillingTelemetryType, bin(timestamp, 1d) | render barchart  
```

Houd er rekening mee dat deze query kan worden gebruikt in een [Azure-logboek waarschuwing](../platform/alerts-unified-log.md) om waarschuwingen op gegevens volumes in te stellen.  

Voor meer informatie over wijzigingen in de telemetriegegevens, kunnen we het aantal gebeurtenissen per type ophalen met behulp van de query:

```kusto
systemEvents
| where timestamp >= startofday(ago(30d))
| where type == "Billing"
| extend BillingTelemetryType = tostring(dimensions["BillingTelemetryType"])
| summarize count() by BillingTelemetryType, bin(timestamp, 1d)
| render barchart  
```

#### <a name="using-data-size-per-event-information"></a>Gegevens grootte per gebeurtenis gegevens gebruiken

Voor meer informatie over de bron van uw gegevens volumes kunt u de `_BilledSize` eigenschap gebruiken die aanwezig is voor elke opgenomen gebeurtenis.

Als u bijvoorbeeld wilt zien welke bewerkingen het meeste gegevens volume in de afgelopen 30 dagen genereren, kunnen we de som `_BilledSize` van alle afhankelijkheids gebeurtenissen opsommen:

```kusto
dependencies
| where timestamp >= startofday(ago(30d))
| summarize sum(_BilledSize) by operation_Name
| render barchart  
```

#### <a name="data-volume-for-workspace-based-application-insights-resources"></a>Gegevens volume voor op werk ruimte gebaseerde Application Insights resources

Ga naar de werk ruimte Log Analytics en voer de volgende query uit om de trends van het gegevens volume te bekijken voor alle [Application Insights resources op basis van werk ruimten](create-workspace-resource.md) in een werk ruimte voor de afgelopen week:

```kusto
union (AppAvailabilityResults),
      (AppBrowserTimings),
      (AppDependencies),
      (AppExceptions),
      (AppEvents),
      (AppMetrics),
      (AppPageViews),
      (AppPerformanceCounters),
      (AppRequests),
      (AppSystemEvents),
      (AppTraces)
| where TimeGenerated >= startofday(ago(7d)) and TimeGenerated < startofday(now())
| summarize sum(_BilledSize) by _ResourceId, bin(TimeGenerated, 1d)
| render areachart
```

Als u een query wilt uitvoeren op de gegevens volume trends op type voor een specifieke Application Insights resource op basis van een werk ruimte, in de Log Analytics werkruimte gebruiken:

```kusto
union (AppAvailabilityResults),
      (AppBrowserTimings),
      (AppDependencies),
      (AppExceptions),
      (AppEvents),
      (AppMetrics),
      (AppPageViews),
      (AppPerformanceCounters),
      (AppRequests),
      (AppSystemEvents),
      (AppTraces)
| where TimeGenerated >= startofday(ago(7d)) and TimeGenerated < startofday(now())
| where _ResourceId contains "<myAppInsightsResourceName>"
| summarize sum(_BilledSize) by Type, bin(TimeGenerated, 1d)
| render areachart
```

## <a name="viewing-application-insights-usage-on-your-azure-bill"></a>Application Insights gebruik op uw Azure-factuur weer geven

Azure biedt een groot aantal handige functies in de hub [Azure Cost Management en facturering](../../cost-management-billing/costs/quick-acm-cost-analysis.md?toc=/azure/billing/TOC.json) . Zo kunt u met de functionaliteit ' cost analysis ' uw uitgaven voor Azure-resources weer geven. Door een filter toe te voegen op resource type (aan micro soft. Insights/onderdelen voor Application Insights), kunt u uw uitgaven bijhouden. Selecteer vervolgens ' groeperen op ' om ' meter categorie ' of ' meter ' te selecteren.  Voor Application Insights resources in de huidige prijs plannen wordt het meeste gebruik weer gegeven als Log Analytics voor de categorie meter, aangezien er één back-end van een logboek is voor alle Azure Monitor-onderdelen. 

Meer informatie over uw gebruik kan worden verkregen door [uw gebruik te downloaden uit de Azure-portal](../../cost-management-billing/manage/download-azure-invoice-daily-usage-date.md#download-usage-in-azure-portal).
In het gedownloade werk blad kunt u het gebruik per Azure-resource per dag bekijken. In dit Excel-werk blad kunt u het gebruik van uw Application Insights-resources vinden door eerst te filteren op de kolom meter categorie om "Application Insights" en "Log Analytics" weer te geven, en vervolgens een filter toe te voegen aan de kolom exemplaar-ID, dat is "bevat micro soft. Insights/Components".  Het meeste Application Insights gebruik wordt gerapporteerd op meters van de meter categorie Log Analytics, omdat er één logboek back-end is voor alle Azure Monitor-onderdelen.  Alleen Application Insights bronnen in verouderde prijs categorieën en webtests met meerdere stappen worden gerapporteerd met een meter categorie van Application Insights.  Het gebruik wordt weer gegeven in de kolom verbruikte hoeveelheid en de eenheid voor elk item wordt weer gegeven in de kolom eenheid.  Er is meer informatie beschikbaar om u te helpen [inzicht te krijgen in uw Microsoft Azure-factuur](../../cost-management-billing/understand/review-individual-bill.md).

## <a name="managing-your-data-volume"></a>Uw gegevens volume beheren

De hoeveelheid gegevens die u verzendt, kan worden beheerd met behulp van de volgende technieken:

* **Steek proef**: u kunt steek proeven gebruiken om de hoeveelheid telemetrie te verminderen die wordt verzonden vanaf uw server-en client-apps, met minimale verstoring van metrische gegevens. Steek proeven zijn het primaire hulp programma dat u kunt gebruiken om de hoeveelheid gegevens die u verzendt af te stemmen. Meer informatie over [sampling-functies](./sampling.md).

* **Ajax-aanroepen beperken**: u kunt [het aantal Ajax-aanroepen beperken dat kan worden gerapporteerd](./javascript.md#configuration) in elke pagina weergave, of de Ajax-rapportage uitschakelen.

* **Overbodige modules uitschakelen**: [Bewerk ApplicationInsights.config](./configuration-with-applicationinsights-config.md) om verzamelings modules uit te scha kelen die u niet nodig hebt. U kunt bijvoorbeeld besluiten dat prestatie meter items of afhankelijkheids gegevens inessentieel zijn.

* **Cumulatieve metrische gegevens**: als u aanroepen naar TrackMetric in uw app opneemt, kunt u het verkeer verminderen door gebruik te maken van de overbelasting die uw berekening van de gemiddelde en standaard afwijking van een batch metingen accepteert. U kunt ook een pakket met [vooraf aggregatie](https://www.myget.org/gallery/applicationinsights-sdk-labs)gebruiken.
 
* **Dagelijkse limiet**: wanneer u een Application Insights resource maakt in de Azure Portal, wordt de daglimiet ingesteld op 100 GB per dag. Wanneer u een Application Insights resource maakt in Visual Studio, is de standaard klein (slechts 32,3 MB/dag). De standaard waarde voor dagelijks Cap is ingesteld om testen te vergemakkelijken. De bedoeling is dat de gebruiker de dagelijkse limiet verhoogt voordat de app in de productie omgeving wordt geïmplementeerd. 

    De maximale limiet is 1.000 GB/dag, tenzij u een hoger maximum voor een toepassing met een hoog verkeer opvraagt.
    
    Waarschuwings-e-mails over de dagelijkse limiet worden verzonden naar het account dat lid is van deze rollen voor uw Application Insights resource: ' ServiceAdmin ', ' AccountAdmin ', ' coadmin ', ' eigenaar '.

    Wees voorzichtig bij het instellen van een daglimiet. Het is uw bedoeling om *nooit het dagelijkse kapje* te bereiken. Als u de daglimiet bereikt, verliest u gegevens voor de rest van de dag en kunt u uw toepassing niet bewaken. Als u de dagelijkse limiet wilt wijzigen, gebruikt u de optie **dagelijks volume limiet** . U kunt deze optie gebruiken in het deel venster **gebruik en geraamde kosten** (dit wordt verderop in het artikel meer gedetailleerder beschreven).
    
    We hebben de beperking verwijderd voor sommige abonnements typen waarvoor een tegoed is dat niet kan worden gebruikt voor Application Insights. Als er eerder een bestedings limiet voor het abonnement is, is het dialoog venster voor de dagelijkse Cap instructies om de bestedings limiet te verwijderen en het dagelijks kapje te activeren dat groter is dan 32,3 MB/dag.
    
* **Beperking**: beperking beperkt de gegevens snelheid tot 32.000 gebeurtenissen per seconde, gemiddeld meer dan 1 minuut per instrumentatie sleutel. Het gegevens volume dat door uw app wordt verzonden, wordt elke minuut beoordeeld. Als het per seconde gemiddeld rente overschrijdt gedurende de minuut, worden sommige aanvragen door de server geweigerd. De SDK buffert de gegevens en probeert deze opnieuw te verzenden. Er wordt een piek van enkele minuten gespreid. Als uw app op consistente wijze gegevens verzendt met meer dan de beperkings snelheid, worden sommige gegevens verwijderd. (De ASP.NET-, Java-en Java script-Sdk's proberen gegevens op deze manier opnieuw te verzenden; andere Sdk's kunnen gewoon vertraagde gegevens verwijderen.) Als er sprake is van beperking, wordt er een melding weer gegeven dat dit is gebeurd.

## <a name="manage-your-maximum-daily-data-volume"></a>Uw maximale dagelijkse gegevens volume beheren

U kunt het dagelijks volume kapje gebruiken om de verzamelde gegevens te beperken. Als de limiet is bereikt, wordt echter een verlies van alle telemetriegegevens verzonden vanuit uw toepassing voor de rest van de dag. Het is *niet raadzaam* om uw toepassing te laten overlopen op het dagelijkse kapje. U kunt de status en prestaties van uw toepassing niet volgen nadat u de dagelijkse limiet hebt bereikt.

In plaats van de dagelijkse volume limiet te gebruiken, gebruikt u [steek proeven](./sampling.md) om het gegevens volume op het gewenste niveau af te stemmen. Gebruik het dagelijks kapje alleen als een ' laatste redmiddel ' voor het geval uw toepassing een grote grotere hoeveelheid telemetrie verstuurt.

### <a name="identify-what-daily-data-limit-to-define"></a>Identificeren welke dagelijkse gegevens limiet moet worden gedefinieerd

Bekijk Application Insights gebruik en de geschatte kosten om inzicht te krijgen in de trend van de gegevens opname en wat het dagelijkse volume Cap is dat moet worden gedefinieerd. U moet er rekening mee houden, omdat u uw resources niet kunt bewaken nadat de limiet is bereikt.

### <a name="set-the-daily-cap"></a>Het dagelijks kapje instellen

Als u het dagelijks kapje wilt wijzigen, selecteert u in de sectie **configureren** van uw Application Insights-resource op de pagina **gebruik en geschatte kosten** de optie  **dagelijks Cap**.

![Het dagelijkse volume limiet voor telemetrie aanpassen](./media/pricing/pricing-003.png)

Als u [het dagelijks kapje wilt wijzigen via Azure Resource Manager](./powershell.md), wijzigt u de eigenschap in `dailyQuota` .  Via Azure Resource Manager kunt u ook de `dailyQuotaResetTime` en de dagelijkse Cap instellen `warningThreshold` .

### <a name="create-alerts-for-the-daily-cap"></a>Waarschuwingen voor het dagelijkse kapje maken

De Application Insights Daily Cap maakt een gebeurtenis in het Azure-activiteiten logboek wanneer de opgenomen gegevens volumes het waarschuwings niveau of het niveau van de dagelijkse Cap bereikt.  U kunt [een waarschuwing maken op basis van deze gebeurtenissen in het activiteiten logboek](../platform/alerts-activity-log.md#create-with-the-azure-portal). De signaal namen voor deze gebeurtenissen zijn:

* Waarschuwings drempelwaarde voor dagelijkse Cap Application Insights-onderdeel bereikt

* Dagelijkse limiet van Application Insights onderdeel is bereikt

## <a name="sampling"></a>Steekproeven
[steek proeven](./sampling.md) zijn een methode om de snelheid waarmee telemetrie wordt verzonden naar uw app te verminderen, terwijl u de mogelijkheid houdt om gerelateerde gebeurtenissen te vinden tijdens diagnostische Zoek opdrachten. U behoudt ook de juiste gebeurtenis aantallen.

Steek proeven zijn een efficiënte manier om kosten te verlagen en binnen uw maandelijkse quotum te blijven. De bemonsterings algoritme houdt gerelateerde items van telemetrie bij, zodat u bijvoorbeeld de aanvraag met betrekking tot een bepaalde uitzonde ring kunt vinden wanneer u zoekt. Het algoritme behoudt ook de juiste aantallen, zodat u de juiste waarden ziet in metrische Explorer voor aanvraag tarieven, uitzonderings snelheden en andere aantallen.

Er zijn verschillende soorten steek proeven.

* [Adaptieve steek proeven](./sampling.md) zijn de standaard waarde voor de ASP.NET-SDK. Adaptieve steek proeven worden automatisch aangepast aan het volume van de telemetrie dat uw app verzendt. Het wordt automatisch uitgevoerd in de SDK in uw web-app, zodat het telemetrie verkeer in het netwerk wordt gereduceerd. 
* Het nemen van *steek proeven* is een alternatief dat werkt op het punt waar telemetrie van uw app de Application Insights-service binnenkomt. De steek proef van opname heeft geen invloed op het volume van de telemetrie die wordt verzonden vanuit uw app, maar vermindert het volume dat door de service wordt bewaard. U kunt opname sampling gebruiken om het quotum te reduceren dat wordt gebruikt door telemetrie van browsers en andere Sdk's.

Ga naar het  **prijs** venster om opname sampling in te stellen:

![Selecteer in het deel venster quotum en prijs de tegel voor beelden en selecteer vervolgens een steek proef Fractie](./media/pricing/pricing-004.png)

> [!WARNING]
> In het deel venster voor **gegevens steek proeven** wordt alleen de waarde van de steek proef van opname bepaald. Dit weerspiegelt niet de sampling frequentie die wordt toegepast door de Application Insights SDK in uw app. Als er al een voor beeld van de inkomende telemetrie in de SDK is, worden de steek proeven voor opname niet toegepast.
>

Gebruik een [Analytics-query](../log-query/log-query-overview.md)om de werkelijke sampling frequentie te ontdekken, ongeacht waar deze is toegepast. De query ziet er als volgt uit:

```kusto
requests | where timestamp > ago(1d)
| summarize 100/avg(itemCount) by bin(timestamp, 1h)
| render areachart
```

In elk bewaard record `itemCount` geeft het aantal oorspronkelijke records aan dat het vertegenwoordigt. De waarde is gelijk aan 1 + het aantal eerder verwijderde records.

## <a name="change-the-data-retention-period"></a>De gegevensretentieperiode wijzigen

De standaard Bewaar periode voor Application Insights resources is 90 dagen. Voor elke Application Insights resource kunnen verschillende Bewaar perioden worden geselecteerd. De volledige set beschik bare Bewaar perioden is 30, 60, 90, 120, 180, 270, 365, 550 of 730 dagen. Meer [informatie](https://azure.microsoft.com/pricing/details/monitor/) over prijzen voor langere retentie van gegevens. 

Als u de retentie wilt wijzigen Application Insights, gaat u naar de pagina **gebruik en geschatte kosten** en selecteert u de optie **gegevens retentie** :

![Scherm afbeelding die laat zien waar u de Bewaar periode voor gegevens wijzigt.](./media/pricing/pricing-005.png)

Als de retentie is verlaagd, is er een respijt periode van een enkele dag voordat de oudste gegevens worden verwijderd.

De retentie kan ook via [Power shell worden ingesteld](powershell.md#set-the-data-retention) met behulp van de `retentionInDays` para meter. Als u de gegevens retentie instelt op 30 dagen, kunt u een onmiddellijke verwijdering van oudere gegevens activeren met behulp van de `immediatePurgeDataOn30Days` para meter, wat nuttig kan zijn voor nalevings scenario's. Deze opschoon functionaliteit wordt alleen weer gegeven via Azure Resource Manager en moet worden gebruikt met extreme zorg. De dagelijkse herstel tijd voor de limiet voor het gegevens volume kan worden geconfigureerd met behulp van Azure Resource Manager om de para meter in te stellen `dailyQuotaResetTime` .

## <a name="data-transfer-charges-using-application-insights"></a>Kosten voor gegevens overdracht met behulp van Application Insights

Bij het verzenden van gegevens naar Application Insights kunnen kosten voor de gegevens bandbreedte worden berekend. Zoals beschreven op de [pagina met prijzen voor Azure-band breedte](https://azure.microsoft.com/pricing/details/bandwidth/), wordt gegevens overdracht tussen Azure-Services in twee regio's in rekening gebracht als uitgaande gegevens overdracht tegen het normale tarief. Inkomende gegevens overdracht is gratis. Deze kosten zijn echter zeer klein (aantal%) vergeleken met de kosten voor de opname van Application Insights logboek gegevens. Als gevolg van het beheer van de kosten voor Log Analytics moet u zich richten op uw opgenomen gegevens volume, en wij hebben richt lijnen om die [hier](#managing-your-data-volume)te helpen begrijpen.

## <a name="limits-summary"></a>Limieten overzicht

[!INCLUDE [application-insights-limits](../../../includes/application-insights-limits.md)]

## <a name="disable-daily-cap-e-mails"></a>Dagelijkse Cap-e-mails uitschakelen

Als u de dagelijkse e-mails voor volume limieten wilt uitschakelen, selecteert u in het deel venster **gebruik en geschatte kosten** de optie **dagelijks Cap** onder het gedeelte **configureren** van uw Application Insights-resource. Er zijn instellingen voor het verzenden van e-mail berichten wanneer de limiet is bereikt, en wanneer een aanpasbaar waarschuwings niveau is bereikt. Als u alle e-mail berichten met betrekking tot het dagelijks volume wilt uitschakelen, schakelt u beide vakjes uit.

## <a name="legacy-enterprise-per-node-pricing-tier"></a>Prijs categorie verouderde onderneming (per knoop punt)

Voor vroege toepassers van Azure-toepassing Insights zijn er nog twee mogelijke prijs Categorieën: Basic en Enter prise. De prijs categorie Basic is hetzelfde als hierboven beschreven en is de standaardlaag. Dit omvat alle functies van de Enter prise-laag, zonder extra kosten. De laag basis is vooral afhankelijk van het volume van de gegevens die worden opgenomen.

> [!NOTE]
> De naam van deze verouderde prijs categorieën is gewijzigd. De Enter prise-prijs categorie wordt nu **per knoop punt** genoemd en de prijs categorie Basic wordt nu **per GB** aangeroepen. Deze nieuwe namen worden hieronder en in de Azure Portal gebruikt.  

De laag per knoop punt (voorheen onderneming) heeft een kosten per knoop punt en elk knoop punt ontvangt een dagelijks gegevens toelage. In de prijs categorie per knoop punt worden er kosten in rekening gebracht voor gegevens die boven de inbegrepen limiet zijn opgenomen. Als u Operations Management Suite gebruikt, kiest u de laag per knoop punt.

Zie [Application Insights prijzen](https://azure.microsoft.com/pricing/details/application-insights/)voor actuele prijzen in uw valuta en regio.

> [!NOTE]
> In april 2018 hebben we een nieuw prijs model [geïntroduceerd](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/) voor Azure-bewaking. In dit model wordt een eenvoudig ' pay-as-u-go-' model voor de volledige Port Folio van bewakings Services aangenomen. Meer informatie over het [nieuwe prijs model](../platform/usage-estimated-costs.md), [het beoordelen van de impact van het verplaatsen naar dit model](../platform/usage-estimated-costs.md#understanding-your-azure-monitor-costs) op basis van uw gebruiks patronen en [hoe u zich kunt aanmelden bij het nieuwe model](../platform/usage-estimated-costs.md#azure-monitor-pricing-model)

### <a name="per-node-tier-and-operations-management-suite-subscription-entitlements"></a>De rechten van het abonnement per knooppunt laag en het Operations Management Suite-pakket

Klanten die Operations Management Suite E1 en E2 kopen, kunnen Application Insights per knoop punt ontvangen als extra onderdeel zonder extra kosten zoals [eerder aangekondigd](/archive/blogs/msoms/azure-application-insights-enterprise-as-part-of-operations-management-suite-subscription). In het bijzonder bevat elke eenheid van Operations Management Suite E1 en E2 een recht op één knoop punt van de Application Insights per knooppunt laag. Elk Application Insights knoop punt bevat Maxi maal 200 MB aan gegevens die per dag (gescheiden van Log Analytics gegevens opname) worden opgenomen, met een gegevens Bewaar periode van 90 dagen zonder extra kosten. De laag wordt verderop in het artikel uitvoeriger beschreven.

Omdat deze laag alleen van toepassing is op klanten met een Operations Management Suite-abonnement, zien klanten die geen Operations Management Suite-abonnement hebben geen optie om deze laag te selecteren.

> [!NOTE]
> Om ervoor te zorgen dat u dit recht krijgt, moeten uw Application Insights-resources zich in de prijs categorie per knoop punt bevinden. Dit recht is alleen van toepassing als knoop punten. Application Insights resources in de laag per GB hebben geen voor deel. Dit recht is niet zichtbaar in de geschatte kosten die worden weer gegeven in het deel venster **gebruik en geschatte kosten** . Als u een abonnement verplaatst naar het nieuwe Azure monitoring-prijs model in april 2018, is de laag per GB de enige laag die beschikbaar is. Het is niet raadzaam een abonnement te verplaatsen naar het nieuwe Azure monitoring-prijs model als u een Operations Management Suite-abonnement hebt.

### <a name="how-the-per-node-tier-works"></a>Hoe werkt de laag per knoop punt?

* U betaalt voor elk knoop punt dat telemetrie verzendt voor apps in de laag per knoop punt.
  * Een *knoop punt* is een fysieke of virtuele server machine of een platform-as-a-Service Role-exemplaar dat als host fungeert voor uw app.
  * Ontwikkel machines, client browsers en mobiele apparaten tellen niet als knoop punten.
  * Als uw app verschillende onderdelen heeft die telemetrie verzenden, zoals een webservice en een back-end-werk nemer, worden de onderdelen afzonderlijk geteld.
  * [Live Metrics stream](./live-stream.md) gegevens worden niet geteld voor prijs doeleinden. In een abonnement zijn uw kosten per knoop punt, niet per app. Als u vijf knoop punten hebt die telemetrie voor 12 apps verzenden, is de kosten voor vijf knoop punten.
* Hoewel kosten per maand worden genoteerd, worden er alleen kosten in rekening gebracht voor elk uur dat een knoop punt telemetrie vanuit een app verzendt. De kosten per uur zijn de maandelijkse kosten per maand, gedeeld door 744 (het aantal uren in een maand van 31 dagen).
* Er wordt een gegevensvolume toewijzing van 200 MB per dag gegeven voor elk gedetecteerd knoop punt (met granulatie per uur). Ongebruikte gegevens toewijzing wordt niet van de ene dag naar de volgende getransporteerd.
  * Als u de prijs categorie per knoop punt kiest, ontvangt elk abonnement een dagelijkse hoeveelheid gegevens op basis van het aantal knoop punten dat telemetrie verzendt naar de Application Insights resources in dat abonnement. Als u dus vijf knoop punten hebt die gegevens dagelijks verzenden, hebt u een gepoolde limiet van 1 GB toegepast op alle Application Insights resources in dat abonnement. Het maakt niet uit of bepaalde knoop punten meer gegevens verzenden dan andere knoop punten, omdat de opgenomen gegevens worden gedeeld op alle knoop punten. Als op een gegeven Application Insights dag meer gegevens worden ontvangen dan is opgenomen in de dagelijkse gegevens toewijzing voor dit abonnement, zijn de kosten voor overschrijding-gegevens per GB van toepassing. 
  * De dagelijkse gegevens limiet wordt berekend als het aantal uren van de dag (met behulp van UTC) dat elk knoop punt telemetriegegevens verzendt gedeeld door 24 vermenigvuldigd met 200 MB. Als u dus vier knoop punten hebt die een telemetrie verzenden gedurende 15 van de 24 uur per dag, worden de inbegrepen gegevens voor die dag ((4 &#215; 15)/24) &#215; 200 MB = 500 MB. Tegen de prijs van 2,30 USD per GB voor de overschrijding van gegevens, zijn de kosten 1,15 USD als de knoop punten 1 GB aan gegevens naar die dag verzenden.
  * De dagelijkse limiet per knoop punt laag wordt niet gedeeld met toepassingen waarvoor u de laag per GB hebt gekozen. Ongebruikte toelage wordt niet van dag tot dag getransporteerd.

### <a name="examples-of-how-to-determine-distinct-node-count"></a>Voor beelden van het bepalen van het aantal afzonderlijke knoop punten

| Scenario                               | Totaal aantal dagelijkse knoop punten |
|:---------------------------------------|:----------------:|
| 1 toepassing met 3 Azure App Service exemplaren en 1 virtuele server | 4 |
| 3 toepassingen die worden uitgevoerd op 2 Vm's; de Application Insights resources voor deze toepassingen bevinden zich in hetzelfde abonnement en in de laag per knoop punt | 2 | 
| 4 toepassingen waarvan de toepassingen Insights-resources zich in hetzelfde abonnement bevinden; elke toepassing die 2 instanties uitvoert gedurende 16-piek uren en 4 instanties gedurende 8 piek uren | 13,33 |
| Cloud Services met één werknemersrol en één webrol, elk met 2 exemplaren | 4 | 
| Een Azure Service Fabric-cluster met 5 knoop punten met 50 micro Services; elke micro service die drie instanties uitvoert | 5|

* De nauw keurigheid van het knoop punt is afhankelijk van welke Application Insights SDK uw toepassing gebruikt. 
  * In SDK versie 2,2 en hoger rapporteert zowel de Application Insights [core SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) als de [Web-SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) elke toepassingshost als een knoop punt. Voor beelden zijn de computer naam voor de fysieke server en VM-hosts of de naam van het exemplaar voor Cloud Services.  De enige uitzonde ring hierop is een toepassing die alleen de [.net core](https://dotnet.github.io/) -en de Application Insights core-SDK gebruikt. In dat geval wordt er slechts één knoop punt voor alle hosts gerapporteerd, omdat de hostnaam niet beschikbaar is.
  * Voor eerdere versies van de SDK gedraagt de [Web-SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) zich als de nieuwere SDK-versies, maar de kern- [SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) rapporteert slechts één knoop punt, ongeacht het aantal hosts van de toepassing.
  * Als uw toepassing de SDK gebruikt om **roleInstance** in te stellen op een aangepaste waarde, wordt de standaard waarde gebruikt voor het bepalen van het aantal knoop punten.
  * Als u een nieuwe SDK-versie gebruikt met een app die wordt uitgevoerd vanaf client computers of mobiele apparaten, zou het aantal knoop punten een groot aantal kunnen retour neren (vanwege het grote aantal client computers of mobiele apparaten).

## <a name="automation"></a>Automation

U kunt een script schrijven om de prijs categorie in te stellen met behulp van Azure-resource beheer. [Meer informatie](powershell.md#price).

## <a name="next-steps"></a>Volgende stappen

* [proef](./sampling.md)

[api]: app-insights-api-custom-events-metrics.md
[apiproperties]: app-insights-api-custom-events-metrics.md#properties
[start]: ./app-insights-overview.md
[pricing]: https://azure.microsoft.com/pricing/details/application-insights/
[pricing]: https://azure.microsoft.com/pricing/details/application-insights/

