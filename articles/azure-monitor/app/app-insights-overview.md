---
title: Wat is Azure Application Insights? | Microsoft Docs
description: Dit is een service waarmee u de prestaties van toepassingen kunt beheren en het gebruik van uw livewebtoepassing kunt bijhouden.  Met deze service kunt u problemen detecteren, prioriteren en onderzoeken en inzicht krijgen in de manier waarop mensen uw app gebruiken.
ms.topic: overview
ms.date: 06/03/2019
ms.custom: mvc
ms.openlocfilehash: ac6b6f2b47df0bce9ae164c83df54adfdf00b184
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100575712"
---
# <a name="what-is-application-insights"></a>Wat is Application Insights?
Application Insights, een functie van [Azure Monitor](../overview.md), is een uitbreidbare APM-service (Application Performance Management) voor ontwikkelaars en DevOps-professionals. Hiermee kunt u uw livetoepassingen controleren. De service detecteert automatisch afwijkingen in prestaties en bevat krachtige analysehulpmiddelen om u te helpen bij het vaststellen van problemen en te begrijpen wat gebruikers daadwerkelijk doen met uw app.  De service is bedoeld om u te helpen de prestaties en bruikbaarheid van uw apps continu te verbeteren. Het werkt voor apps op uiteenlopende platforms, waaronder .NET, Node.js, Java en Python, on-premises, of in een hybride of openbare cloud gehost. De service kan ook worden geïntegreerd met uw DevOps-proces en bevat verbindingspunten naar verschillende hulpmiddelen voor ontwikkelaars. Gebruik de service voor het controleren en analyseren van telemetrie van mobiele apps door Application Insights te integreren met Visual Studio App Center.

## <a name="how-does-application-insights-work"></a>Hoe werkt Application Insights?
U installeert een klein instrumentatiepakket (SDK) in uw toepassing of u schakelt Application Insights met behulp van de Application Insights-agent in wanneer dit wordt [ondersteund](./platforms.md). Met de instrumentatie wordt uw app bewaakt en worden de telemetriegegevens doorgestuurd naar een Azure Application Insights-resource met een unieke GUID, waarnaar wordt verwezen als een instrumentatiesleutel.

U kunt niet alleen de webservicetoepassing instrumenteren, maar ook de onderdelen die op de achtergrond worden uitgevoerd en de JavaScript-code van de webpagina's zelf. De toepassing en de bijbehorende onderdelen kunnen overal worden uitgevoerd en hoeven niet te worden gehost in Azure.

![Met het Application Insights-instrumentatiepakket in uw app wordt telemetrie naar uw Application Insights-resource verzonden.](./media/app-insights-overview/diagram.png)

Bovendien kunt u telemetrie ophalen uit de hostomgevingen, zoals gegevens van prestatiemeteritems, diagnostische gegevens van Azure of gegevens uit Docker-logboeken. U kunt ook webtests instellen die periodiek synthetische aanvragen naar uw webservice verzenden.

Al deze telemetriestromen worden geïntegreerd in Azure Monitor. In Azure Portal kunt u krachtige analyse- en zoekhulpmiddelen toepassen op de onbewerkte gegevens.

### <a name="whats-the-overhead"></a>Wat is de impact?
De impact op de prestaties van uw app is klein. De aanroepen voor het bijhouden van het appgebruik blokkeren uw app niet en worden batchgewijs in een afzonderlijke thread verzonden.

## <a name="what-does-application-insights-monitor"></a>Wat wordt er door Application Insights gecontroleerd?

Application Insights is bedoeld voor het ontwikkelingsteam en helpt u om te begrijpen hoe de app presteert en op welke manier de app wordt gebruikt. Met deze service kunt u het volgende controleren:

* **Aantal aanvragen, reactietijden en foutpercentages** - ga na welke pagina's het populairst zijn op welke tijdstippen van de dag en waar uw gebruikers zich bevinden. Ontdek welke pagina's het beste presteren. Als uw reactietijden en foutpercentages omhoog gaan wanneer er meer aanvragen binnenkomen, hebt u mogelijk te weinig resources. 
* **Aantal afhankelijkheidsrelaties, reactietijden en foutpercentages** - controleer of externe services zorgen voor vertraging.
* **Uitzonderingen** - analyseer de cumulatieve statistische gegevens of kies specifieke gegevens en zoom in op de stack-trace en verwante aanvragen. Zowel server- als browseruitzonderingen worden gerapporteerd.
* **Paginaweergaven en de prestaties bij het laden van pagina’s** - deze gegevens worden gerapporteerd door de browsers van uw gebruikers.
* **AJAX-aanroepen** van webpagina's - ga na wat het aantal aanroepen, de reactietijden en de foutpercentages zijn.
* **Aantal gebruikers en sessies**.
* **Prestatiemeteritems** van uw Windows- of Linux-servers, zoals die voor CPU-, geheugen- en netwerkgebruik. 
* **Diagnostische gegevens van hosts** van Docker of Azure. 
* **Diagnostische traceerlogboeken** van uw app - met behulp hiervan kunt u de samenhang vaststellen tussen traceergebeurtenissen en aanvragen.
* **Aangepaste gebeurtenissen en functies voor het verzamelen van metrische gegevens** die u zelf schrijft in de client- of servercode - hiermee kunt u zakelijke gebeurtenissen bijhouden, zoals het aantal verkochte artikelen of gewonnen spellen.

## <a name="where-do-i-see-my-telemetry"></a>Waar kan ik mijn telemetrie bekijken?

Er zijn tal van manieren om uw gegevens te verkennen. Lees de volgende artikelen:

|  |  |
| --- | --- |
| [**Slimme detectie en handmatige waarschuwingen**](./proactive-diagnostics.md)<br/>Stel automatische waarschuwingen in die worden afgestemd op de normale telemetriepatronen van uw app en worden geactiveerd wanneer er zich iets voordoet dat buiten het normale patroon valt. U kunt ook [waarschuwingen instellen](../alerts/alerts-log.md) voor bepaalde niveaus van aangepaste functies of standaardfuncties voor het verzamelen van metrische gegevens. |![Voorbeeld van een waarschuwing](./media/app-insights-overview/alerts-tn.png) |
| [**Overzicht van de toepassing**](./app-map.md)<br/>Verken de onderdelen van uw app met belangrijke metrische gegevens en waarschuwingen. |![Overzicht van de toepassing](./media/app-insights-overview/appmap-tn.png)  |
| [**Profiler**](./profiler.md)<br/>Inspecteer de uitvoeringsprofielen van voorbeeldaanvragen. |![Schermopname toont de uitvoeringsprofielen van voorbeeldaanvragen.](./media/app-insights-overview/profiler.png) |
| [**Gebruiksanalyse**](./usage-overview.md)<br/>Analyseer de segmentatie en retentie van gebruikers.|![Retentie-informatie](./media/app-insights-overview/retention.png) |
| [**Diagnostische zoekactie naar gegevens van bepaalde items**](./diagnostic-search.md)<br/>U kunt zoeken naar gebeurtenissen, zoals aanvragen, uitzonderingen, afhankelijkheidsaanroepen, logboektraceringen en paginaweergaven en deze gegevens ook filteren.  |![Zoeken in telemetrie](./media/app-insights-overview/search-tn.png) |
| [**Metrics Explorer voor cumulatieve gegevens**](../essentials/metrics-charts.md)<br/>Verken, filter en segmenteer cumulatieve gegevens, zoals aantallen aanvragen, fouten en uitzonderingen, reactietijden en paginalaadtijden. |![Metrische gegevens](./media/app-insights-overview/metrics-tn.png) |
| [**Dashboards**](./overview-dashboard.md)<br/>Combineer gegevens van meerdere resources tot een mash-up en deel deze met anderen. Ideaal voor toepassingen met meerdere onderdelen en om continu weer te geven in de teamkamer. |![Voorbeelden van dashboards](./media/app-insights-overview/dashboard-tn.png) |
| [**Live Metrics Stream**](./live-stream.md)<br/>Wanneer u een nieuwe build implementeert, kunt u kijken naar deze 'near-realtime' prestatie-indicatoren om te controleren of dat alles naar verwachting werkt. |![Voorbeeld van metrische livegegevens](./media/app-insights-overview/live-metrics-tn.png) |
| [**Analytics**](../logs/log-query-overview.md)<br/>Beantwoord moeilijke vragen over de prestaties en het gebruik van uw app met behulp van deze krachtige querytaal. |![Voorbeeld van Analytics](./media/app-insights-overview/analytics-tn.png) |
| [**Visual Studio**](./visual-studio.md)<br/>Bekijk prestatiegegevens in de code. Ga naar de code vanuit stack-traces.|![Schermopname toont uitzonderingsdetails in Visual Studio en een voorbeeld van het gebruik van code uit stack-traces.](./media/app-insights-overview/visual-studio-tn.png) |
| [**Snapshot Debugger**](./snapshot-debugger.md)<br/>Spoor fouten op in momentopnamen van live activiteiten, inclusief parameterwaarden.|![Visual Studio](./media/app-insights-overview/snapshot.png) |
| [**Power BI**](./export-power-bi.md)<br/>Integreer metrische gegevens over het gebruik van de toepassing met andere business intelligence.| ![Power BI](./media/app-insights-overview/power-bi.png)|
| [**REST API**](https://dev.applicationinsights.io/)<br/>Schrijf code voor het uitvoeren van query's op uw functies voor het verzamelen van metrische gegevens en op onbewerkte gegevens.| ![REST-API](./media/app-insights-overview/rest-tn.png) |
| [**Continue export**](./export-telemetry.md)<br/>Exporteer onbewerkte gegevens bulksgewijs naar de opslag zodra de gegevens binnenkomen. |![Exporteren](./media/app-insights-overview/export-tn.png) |

## <a name="how-do-i-use-application-insights"></a>Hoe kan ik Application Insights gebruiken?

### <a name="monitor"></a>Controleren
Installeer Application Insights in uw app, stel de [beschikbaarheidswebtests](./monitor-web-app-availability.md) in en ga als volgt te werk:

* Bekijk het standaard [toepassingsdashboard](./overview-dashboard.md) voor uw teamkamer om de laadtijd, reactiesnelheid en prestaties van uw afhankelijkheden, paginalaadacties en AJAX-aanroepen in de gaten te houden.
* Ga na welke aanvragen het langzaamst worden verwerkt en de meeste fouten vertonen.
* Bekijk [Live Stream](./live-stream.md) wanneer u een nieuwe release implementeert, zodat u onmiddellijk op de hoogte bent van een eventuele afname van de prestaties.

### <a name="detect-diagnose"></a>Fouten detecteren en een diagnose stellen
Ga als volgt te werk als u een waarschuwing ontvangt of een probleem detecteert:

* Beoordeel hoeveel gebruikers last hebben van het probleem.
* Ga na of er een verband is tussen fouten en uitzonderingen, afhankelijkheidsaanroepen en traceringen.
* Bekijk de informatie van Profiler, momentopnamen, stackdumps en traceerlogboeken.

### <a name="build-measure-learn"></a>Meten is weten
[Meet de effectiviteit](./usage-overview.md) van elke nieuwe functie die u implementeert.

* Maak plannen om te meten hoe klanten de nieuwe gebruikerservaring of zakelijke functies gebruiken.
* Schrijf aangepaste telemetrie in uw code.
* Baseer de volgende ontwikkelingscyclus op de harde bewijzen die uw telemetrie u bieden.

## <a name="get-started"></a>Aan de slag
Application Insights is een van de vele services die worden gehost binnen Microsoft Azure en telemetrie wordt naar deze service verzonden om te worden geanalyseerd en gepresenteerd. Dus voor u aan de slag kunt gaan, hebt u eerst een abonnement op [Microsoft Azure](https://azure.com) nodig. U kunt zich gratis aanmelden en als u kiest voor het basis[prijsplan](https://azure.microsoft.com/pricing/details/application-insights/) van Application Insights, hebt u geen kosten totdat het gebruik van uw toepassing zodanig is toegenomen dat er sprake is van substantieel gebruik. Als uw organisatie al een abonnement heeft, kan uw Microsoft-account aan dat abonnement worden toegevoegd.

Er zijn verschillende manieren om van start te gaan. Begin op de manier die voor u het beste werkt. U kunt later ook andere manieren gebruiken.

* **Tijdens runtime: instrumenteer uw web-app op de server.** Ideaal voor toepassingen die al zijn geïmplementeerd. Hiermee voorkomt u dat u updates moet uitvoeren op de code.
  * [**ASP.NET- of ASP.NET Core-toepassingen die worden gehost op Azure Web Apps**](./azure-web-apps.md)
  * [**ASP.NET-toepassingen die worden gehost in IIS op een Azure VM of een virtuele-machineschaalset van Azure**](./azure-vm-vmss-apps.md)
  * [**ASP.NET-toepassingen die worden gehost in IIS op on-premises VM**](./monitor-performance-live-website-now.md)
* **Tijdens het ontwikkelen: voeg Application Insights toe aan uw code.** Hiermee kunt u het verzamelen van telemetrie aanpassen en extra telemetrie verzenden.
  * [ASP.NET-toepassingen](./asp-net.md)
  * [ASP.NET Core-toepassingen](./asp-net-core.md)
  * [.NET Console-toepassingen](./console.md)
  * [Java](./java-get-started.md)
  * [Node.js](./nodejs.md)
  * [Python](./opencensus-python.md)
  * [Andere platforms](./platforms.md)
* **[Instrumenteer uw webpagina’s](./javascript.md)** voor paginaweergaven, AJAX-aanroepen en andere telemetrie op de clientzijde.
* **[Analyseer het gebruik van uw mobiele app](../app/mobile-center-quickstart.md)** door de service te integreren met Visual Studio App Center.
* **[Beschikbaarheidstests](./monitor-web-app-availability.md)** - ping uw website regelmatig vanaf onze servers.

## <a name="next-steps"></a>Volgende stappen
Gebruik tijdens runtime:

* [Met IIS gehoste apps in Azure-VM en virtuele-machineschaalset van Azure](./azure-vm-vmss-apps.md)
* [IIS-server](./monitor-performance-live-website-now.md)
* [Azure Web Apps](./azure-web-apps.md)

Gebruik tijdens het ontwikkelen:

* [ASP.NET](./asp-net.md)
* [ASP.NET Core](./asp-net-core.md)
* [Java](./java-get-started.md)
* [Node.js](./nodejs.md)
* [Python](./opencensus-python.md)
* [JavaScript](./javascript.md)


## <a name="support-and-feedback"></a>Ondersteuning en feedback
* Vragen en problemen:
  * [Problemen oplossen][qna]
  * [Microsoft Q&A-vragenpagina](/answers/topics/azure-monitor.html)
  * [StackOverflow](https://stackoverflow.com/questions/tagged/ms-application-insights)
* Uw suggesties:
  * [UserVoice](https://feedback.azure.com/forums/357324-application-insights/filters/top)
* Blog:
  * [Application Insights-blog](https://azure.microsoft.com/blog/tag/application-insights)

<!--Link references-->

[android]: ../app/mobile-center-quickstart.md
[azure]: ../../insights-perf-analytics.md
[client]: ./javascript.md
[desktop]: ./windows-desktop.md
[greenbrown]: ./asp-net.md
[ios]: ../app/mobile-center-quickstart.md
[java]: ./java-get-started.md
[knowUsers]: app-insights-web-track-usage.md
[platforms]: ./platforms.md
[portal]: https://portal.azure.com/
[qna]: ../faq.md
[redfield]: ./monitor-performance-live-website-now.md

