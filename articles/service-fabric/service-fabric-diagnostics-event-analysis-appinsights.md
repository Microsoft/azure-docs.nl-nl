---
title: Azure Service Fabric-gebeurtenis analyse met Application Insights
description: Meer informatie over het visualiseren en analyseren van gebeurtenissen met Application Insights voor het bewaken en diagnosticeren van Azure Service Fabric-clusters.
ms.topic: conceptual
ms.date: 11/21/2018
ms.openlocfilehash: 0e84c3c1ab542dc4541ebdcfbc49f45e02458d9c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105626211"
---
# <a name="event-analysis-and-visualization-with-application-insights"></a>Gebeurtenis analyse en visualisatie met Application Insights

Een deel van Azure Monitor is Application Insights een uitbreidbaar platform voor het controleren van toepassingen en diagnostische gegevens. Het bevat een krachtig analyse-en query programma, aanpas bare Dash boards en visualisaties en andere opties, waaronder geautomatiseerde waarschuwingen. De integratie van Application Insights met Service Fabric bevat hulp middelen voor Visual Studio en Azure Portal, evenals Service Fabric specifieke metrische gegevens, waarmee u een allesomvattende out-of-the-box-registratie ervaring kunt bieden. Hoewel er automatisch veel logboeken worden gemaakt en verzameld voor u met Application Insights, raden we u aan om verdere aangepaste logboek registratie toe te voegen aan uw toepassingen om een uitgebreide diagnostische ervaring te creëren.

Dit artikel helpt u bij het oplossen van de volgende veelgestelde vragen:

* Hoe kan ik weet wat er gebeurt in mijn toepassing en services en hoe u telemetrie verzamelt?
* Hoe kan ik problemen met mijn toepassing oplossen, met name services die met elkaar communiceren?
* Hoe kan ik metrische gegevens weer geven over hoe mijn services worden uitgevoerd, bijvoorbeeld pagina laad tijd, HTTP-aanvragen?

In dit artikel wordt uitgelegd hoe u inzicht krijgt en problemen oplost vanuit Application Insights. Als u meer wilt weten over het instellen en configureren van Application Insights met Service Fabric, raadpleegt u deze [zelf studie](service-fabric-tutorial-monitoring-aspnet.md).

## <a name="monitoring-in-application-insights"></a>Bewaking in Application Insights

Application Insights heeft een rijke out van de box-ervaring bij het gebruik van Service Fabric. Op de pagina overzicht bevat Application Insights belang rijke informatie over uw service, zoals de reactie tijd en het aantal verwerkte aanvragen. Als u bovenaan op de knop Zoeken klikt, ziet u een lijst met recente aanvragen in uw toepassing. Daarnaast kunt u hier mislukte aanvragen bekijken en vaststellen welke fouten zijn opgetreden.

![Overzicht van Application Insights](media/service-fabric-diagnostics-event-analysis-appinsights/ai-overview.png)

In het rechterdeel venster van de voor gaande afbeelding bevinden zich twee hoofd typen vermeldingen in de lijst: aanvragen en gebeurtenissen. Aanvragen zijn aanroepen naar de API van de app met behulp van HTTP-aanvragen in dit geval en gebeurtenissen zijn aangepaste gebeurtenissen, die als telemetrie fungeren, die u overal in uw code kunt toevoegen. U kunt het instrumenteren van uw toepassingen verder verkennen in [Application Insights-API voor aangepaste gebeurtenissen en metrische gegevens](../azure-monitor/app/api-custom-events-metrics.md). Als u op een aanvraag klikt, wordt er meer informatie weer gegeven, zoals wordt weer gegeven in de volgende afbeelding, inclusief gegevens die specifiek zijn voor Service Fabric, die worden verzameld in het Application Insights Service Fabric NuGet-pakket. Deze informatie is nuttig voor het oplossen van problemen en het weten wat de status van uw toepassing is, en al deze gegevens kunnen worden doorzocht in Application Insights

![Scherm afbeelding met meer details, inclusief gegevens die specifiek zijn voor Service Fabric, die worden verzameld in het Application Insights Service Fabric NuGet-pakket.](media/service-fabric-diagnostics-event-analysis-appinsights/ai-request-details.png)

Application Insights heeft een specifieke weer gave voor het uitvoeren van query's op alle gegevens in. Selecteer Metrics Explorer boven aan de pagina overzicht om naar de Application Insights portal te navigeren. Hier kunt u query's uitvoeren op basis van aangepaste gebeurtenissen die worden genoemd vóór, aanvragen, uitzonde ringen, prestatie meter items en andere metrische gegevens met behulp van de Kusto-query taal. In het volgende voor beeld worden alle aanvragen in het afgelopen uur weer gegeven.

![Details van Application Insights-aanvraag](media/service-fabric-diagnostics-event-analysis-appinsights/ai-metrics-explorer.png)

Als u meer wilt weten over de mogelijkheden van de Application Insights Portal, gaat u naar de [documentatie over Application Insights Portal](../azure-monitor/app/overview-dashboard.md).

### <a name="configuring-application-insights-with-eventflow"></a>Application Insights configureren met Event flow

Als u Event Flow gebruikt om gebeurtenissen te verzamelen, moet u ervoor zorgen dat u het `Microsoft.Diagnostics.EventFlow.Outputs.ApplicationInsights` NuGet-pakket importeert. De volgende code is vereist in het gedeelte *outputs* van de *eventFlowConfig.jsop*:

```json
"outputs": [
    {
        "type": "ApplicationInsights",
        "instrumentationKey": "***ADD INSTRUMENTATION KEY HERE***"
    }
]
```

Zorg ervoor dat u de vereiste wijzigingen in uw filters aanbrengt, evenals andere invoer (samen met hun respectieve NuGet-pakketten).

## <a name="application-insights-sdk"></a>Application Insights SDK

Het is raadzaam om event flow en WAD te gebruiken als aggregatie oplossingen, omdat ze een meer modulaire benadering van diagnostische gegevens en bewaking toestaan, dat wil zeggen, als u uw uitvoer wilt wijzigen van Event flow, maar u hoeft alleen maar een eenvoudige wijziging aan te brengen in het configuratie bestand. Als u besluit om te investeren in het gebruik van Application Insights en waarschijnlijk niet naar een ander platform overschakelt, moet u Application Insights ' nieuwe SDK gebruiken voor het samen voegen van gebeurtenissen en deze naar Application Insights verzenden. Dit betekent dat u niet langer Event flow hoeft te configureren om uw gegevens naar Application Insights te verzenden, maar u kunt in plaats daarvan het Service Fabric NuGet-pakket van de ApplicationInsight installeren. Meer informatie over het pakket vindt u [hier](https://github.com/Microsoft/ApplicationInsights-ServiceFabric).

[Application Insights ondersteuning voor micro Services en containers](https://azure.microsoft.com/blog/app-insights-microservices/) toont u een aantal van de nieuwe functies waaraan wordt gewerkt (momenteel nog steeds in de bèta versie), zodat u geavanceerde opties voor het controleren van de mogelijkheden met Application Insights kunt hebben. Dit omvat het bijhouden van afhankelijkheden (gebruikt voor het bouwen van een AppMap van al uw services en toepassingen in een cluster en de communicatie ertussen), en een betere correlatie van traceringen die afkomstig zijn van uw services (helpt bij het beter herkennen van een probleem in de werk stroom van een toepassing of service).

Als u in .NET ontwikkelt en waarschijnlijk enkele van de programmeer modellen van Service Fabric gebruikt, en u bereid bent Application Insights te gebruiken als uw platform voor het visualiseren en analyseren van gebeurtenis-en logboek gegevens, raden we u aan om via de Application Insights SDK-route te gaan als uw werk stroom voor bewaking en diagnose. Lees de documentatie van [Application Insights documentatie](../azure-monitor/azure-monitor-app-hub.yml) en [traceer logboeken](../azure-monitor/app/asp-net-trace-logs.md) om aan de slag te gaan met Application Insights om uw logboeken te verzamelen en weer te geven.

## <a name="navigating-the-application-insights-resource-in-azure-portal"></a>Navigeren door de Application Insights resource in Azure Portal

Zodra u Application Insights hebt geconfigureerd als uitvoer voor uw gebeurtenissen en Logboeken, moeten de gegevens in een paar minuten worden weer gegeven in uw Application Insights resource. Ga naar de Application Insights resource waarmee u naar het Application Insights resource dashboard gaat. Selecteer **zoeken** in de Application Insights taak balk om de meest recente traceringen te zien die ze hebben ontvangen en om ze te kunnen filteren.

*Metrics Explorer* is een handig hulp middel voor het maken van aangepaste Dash boards op basis van metrische gegevens die uw toepassingen, services en cluster kunnen rapporteren. Zie [metrische gegevens verkennen in Application Insights](../azure-monitor/essentials/metrics-charts.md) voor het instellen van een paar grafieken die u zelf hebt gemaakt op basis van de door u verzamelde informatie.

Als u op **Analytics** klikt, gaat u naar de Application Insights Analytics-Portal, waar u kunt zoeken naar gebeurtenissen en traceringen met een grotere omvang en optioneel. Meer informatie hierover vindt u [in Application Insights](../azure-monitor/logs/log-query-overview.md).

## <a name="next-steps"></a>Volgende stappen

* [Stel waarschuwingen in AI in](../azure-monitor/alerts/alerts-log.md) om een melding te ontvangen over wijzigingen in prestaties of gebruik
* [Slimme detectie in Application Insights](../azure-monitor/app/proactive-diagnostics.md) voert een proactieve analyse uit van de telemetrie die wordt verzonden naar Application Insights om u te waarschuwen over mogelijke prestatie problemen
