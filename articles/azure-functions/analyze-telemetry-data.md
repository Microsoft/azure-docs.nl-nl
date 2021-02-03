---
title: Azure Functions telemetrie in Application Insights analyseren
description: Meer informatie over het weer geven en opvragen van Azure Functions telemetriegegevens die zijn verzameld door en die zijn opgeslagen in Azure-toepassing Insights.
ms.topic: how-to
ms.date: 10/14/2020
ms.custom: contperf-fy21q2
ms.openlocfilehash: 2a991157962b0588e3d49510e8a82a9abcfb9aed
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/03/2021
ms.locfileid: "99493767"
---
# <a name="analyze-azure-functions-telemetry-in-application-insights"></a>Azure Functions telemetrie in Application Insights analyseren 

Azure Functions integreert met Application Insights om uw functie-apps beter te kunnen bewaken. Application Insights verzamelt telemetriegegevens die zijn gegenereerd door uw functie-app, met inbegrip van gegevens die uw app naar Logboeken schrijft. Application Insights integratie is doorgaans ingeschakeld wanneer uw functie-app wordt gemaakt. Als voor uw functie-app geen instrumentatie sleutel is ingesteld, moet u eerst [Application Insights-integratie inschakelen](configure-monitoring.md#enable-application-insights-integration). 

De gegevens die zijn verzameld uit de functie-app worden standaard opgeslagen in Application Insights. In de [Azure Portal](https://portal.azure.com)biedt Application Insights een uitgebreide set visualisaties van uw telemetriegegevens. U kunt inzoomen op fouten logboeken en query's uitvoeren op gebeurtenissen en metrische gegevens. In dit artikel vindt u eenvoudige voor beelden van het weer geven en opvragen van uw verzamelde gegevens. Zie [Wat is Application Insights?](../azure-monitor/app/app-insights-overview.md)voor meer informatie over het verkennen van de gegevens van uw functie-app in Application Insights. 

Zie [gegevens verzameling, retentie en opslag in Application Insights](../azure-monitor/app/data-retention-privacy.md)voor meer informatie over het bewaren van gegevens en mogelijke opslag kosten.   

## <a name="viewing-telemetry-in-monitor-tab"></a>Telemetrie weer geven op het tabblad Monitor

Als [Application Insights integratie is ingeschakeld](configure-monitoring.md#enable-application-insights-integration), kunt u telemetrie-gegevens weer geven op het tabblad **monitor** .

1. Selecteer op de pagina functie-app een functie die ten minste eenmaal is uitgevoerd nadat Application Insights is geconfigureerd. Selecteer vervolgens **monitor** in het linkerdeel venster. Selecteer regel matig **vernieuwen** totdat de lijst met functie aanroepen wordt weer gegeven.

   ![Lijst met aanroepen](media/functions-monitoring/monitor-tab-ai-invocations.png)

    > [!NOTE]
    > Het kan tot vijf minuten duren voordat de lijst wordt weer gegeven terwijl de gegevens van de telemetrie-client worden gebatcheerd voor verzen ding naar de server. De vertraging geldt niet voor de [Live Metrics stream](../azure-monitor/app/live-stream.md). Deze service maakt verbinding met de functions-host wanneer u de pagina laadt. logboeken worden dus rechtstreeks naar de pagina gestreamd.

1. Als u de logboeken voor een bepaalde functie aanroep wilt bekijken, selecteert u de kolom koppeling voor de **datum (UTC)** voor die aanroep. De logboek registratie-uitvoer voor die aanroep wordt weer gegeven op een nieuwe pagina.

   ![Details van aanroep](media/functions-monitoring/invocation-details-ai.png)

1. Kies **uitvoeren in Application Insights** om de bron van de query weer te geven waarmee de Azure monitor logboek gegevens worden opgehaald in het Azure-logboek. Als dit de eerste keer is dat u Azure Log Analytics gebruikt in uw abonnement, wordt u gevraagd om dit in te scha kelen.

1. Nadat u Log Analytics hebt ingeschakeld, wordt de volgende query weer gegeven. U kunt zien dat de query resultaten beperkt zijn tot de laatste 30 dagen ( `where timestamp > ago(30d)` ) en dat de resultaten niet meer dan 20 rijen ( `take 20` ) bevatten. De lijst met aanroep Details voor uw functie is daarentegen voor de afgelopen 30 dagen zonder limiet.

   ![Application Insights analyse aanroep lijst](media/functions-monitoring/ai-analytics-invocation-list.png)

Zie voor meer informatie [Query's uitvoeren op telemetriegegevens](#query-telemetry-data) verderop in dit artikel.

## <a name="view-telemetry-in-application-insights"></a>Telemetrie in Application Insights weer geven

Application Insights openen vanuit een functie-app in de [Azure Portal](https://portal.azure.com):

1. Blader naar de functie-app in de portal.

1. Selecteer **Application Insights** onder **instellingen** op de linkerpagina. 

1. Als dit de eerste keer is dat Application Insights met uw abonnement wordt gebruikt, wordt u gevraagd om dit in te scha kelen. Hiervoor selecteert u **Application Insights inschakelen** en selecteert u vervolgens **Toep assen** op de volgende pagina.

![Open Application Insights op de pagina overzicht van functie-app](media/functions-monitoring/ai-link.png)

Zie de [Application Insights-documentatie](/azure/application-insights/)voor meer informatie over het gebruik van Application Insights. In deze sectie ziet u enkele voor beelden van het weer geven van gegevens in Application Insights. Als u al bekend bent met Application Insights, gaat u rechtstreeks naar [de secties over het configureren en aanpassen van de telemetriegegevens](configure-monitoring.md#configure-log-levels).

![Tabblad Overzicht van Application Insights](media/functions-monitoring/metrics-explorer.png)

De volgende gebieden van Application Insights kunnen nuttig zijn bij het evalueren van het gedrag, de prestaties en de fouten in uw functies:

| Onderzoeken | Beschrijving |
| ---- | ----------- |
| **[Fouten](../azure-monitor/app/asp-net-exceptions.md)** |  Grafieken en waarschuwingen maken op basis van functie fouten en server uitzonderingen. De **naam** van de bewerking is de naam van de functie. Storingen in afhankelijkheden worden niet weer gegeven, tenzij u aangepaste telemetrie implementeert voor afhankelijkheden. |
| **[Prestaties](../azure-monitor/app/performance-counters.md)** | Analyseer prestatie problemen door het resource gebruik en de door Voer per **Cloud-rolinstanties** weer te geven. Deze prestatie gegevens kunnen nuttig zijn voor het opsporen van fouten in scenario's waarbij functies worden bogging van uw onderliggende resources. |
| **[Metrische gegevens](../azure-monitor/platform/metrics-charts.md)** | Grafieken en waarschuwingen maken op basis van metrische gegevens. Metrische gegevens bevatten het aantal functie-aanroepen, uitvoerings tijd en succes percentages. |
| **[Live Metrics](../azure-monitor/app/live-stream.md)** | Metrische gegevens weer geven terwijl deze in bijna realtime worden gemaakt. |

## <a name="query-telemetry-data"></a>Telemetrie-gegevens opvragen

[Application Insights Analytics](../azure-monitor/log-query/log-query-overview.md) geeft u toegang tot alle telemetriegegevens in de vorm van tabellen in een Data Base. Analytics biedt een query taal voor het uitpakken, bewerken en visualiseren van de gegevens. 

Kies **Logboeken** om te verkennen of een query uit te zoeken naar vastgelegde gebeurtenissen.

![Voor beeld van analyse](media/functions-monitoring/analytics-traces.png)

Hier volgt een voor beeld van een query waarin de distributie van aanvragen per werk nemer in de afgelopen 30 minuten wordt weer gegeven.

```kusto
requests
| where timestamp > ago(30m) 
| summarize count() by cloud_RoleInstance, bin(timestamp, 1m)
| render timechart
```

De tabellen die beschikbaar zijn, worden weer gegeven op het tabblad **schema** aan de linkerkant. In de volgende tabellen vindt u gegevens die zijn gegenereerd door functie aanroepen:

| Tabel | Beschrijving |
| ----- | ----------- |
| **traceringen** | Logboeken die zijn gemaakt door de runtime, schaal controller en traceringen van uw functie code. |
| **aanvragen** | Eén aanvraag voor elke functie aanroep. |
| **uitzonderingen** | Eventuele uitzonde ringen die worden veroorzaakt door de runtime. |
| **customMetrics** | Het aantal geslaagde en mislukte aanroepen, succes percentage en duur. |
| **customEvents** | Gebeurtenissen die worden bijgehouden door de runtime, bijvoorbeeld: HTTP-aanvragen die een functie activeren. |
| **Performance Counters** | Informatie over de prestaties van de servers waarop de functies worden uitgevoerd. |

De andere tabellen zijn voor beschikbaarheids testen en voor client-en browser-telemetrie. U kunt aangepaste telemetrie implementeren om er gegevens aan toe te voegen.

In elke tabel bevindt zich een deel van de functions-specifieke gegevens in een `customDimensions` veld.  De volgende query haalt bijvoorbeeld alle traceringen op die logboek niveau hebben `Error` .

```kusto
traces 
| where customDimensions.LogLevel == "Error"
```

De runtime levert de `customDimensions.LogLevel` `customDimensions.Category` velden en. U kunt extra velden opgeven in de logboeken die u in uw functie code schrijft. Zie voor een voor beeld in C# [Structured logging](functions-dotnet-class-library.md#structured-logging) in de .net Class Library-ontwikkelaars handleiding.

## <a name="query-scale-controller-logs"></a>Logboeken voor query Scale-controller

_Deze functie is beschikbaar als preview-versie._

Na het inschakelen van zowel [logboek registratie](configure-monitoring.md#configure-scale-controller-logs) voor de schaal van de controller als [Application Insights-integratie](configure-monitoring.md#enable-application-insights-integration), kunt u de logboeken van de uitgebrachte schaal controller in het Application Insights zoeken naar een query. Schaal controller logboeken worden opgeslagen in de `traces` verzameling onder de categorie **ScaleControllerLogs** .

De volgende query kan worden gebruikt om te zoeken naar alle logboeken van de schaal controller voor de huidige functie-app binnen de opgegeven tijds periode:

```kusto
traces 
| extend CustomDimensions = todynamic(tostring(customDimensions))
| where CustomDimensions.Category == "ScaleControllerLogs"
```

Met de volgende query wordt de vorige query uitgebreid om te laten zien hoe u alleen logboeken kunt ophalen die een wijziging in de schaal aangeven:

```kusto
traces 
| extend CustomDimensions = todynamic(tostring(customDimensions))
| where CustomDimensions.Category == "ScaleControllerLogs"
| where message == "Instance count changed"
| extend Reason = CustomDimensions.Reason
| extend PreviousInstanceCount = CustomDimensions.PreviousInstanceCount
| extend NewInstanceCount = CustomDimensions.CurrentInstanceCount
```

## <a name="consumption-plan-specific-metrics"></a>Verbruiks plan-specifieke metrische gegevens

Bij het uitvoeren van een [verbruiks abonnement](consumption-plan.md)wordt de uitvoerings *kosten* van één functie-uitvoering gemeten in *GB seconden*. De uitvoerings kosten worden berekend door het geheugen gebruik te combi neren met de uitvoerings tijd. Zie [kosten voor verbruiks abonnementen schatten](functions-consumption-costs.md)voor meer informatie.

De volgende telemetrie-query's zijn specifiek voor metrische gegevens die van invloed zijn op de kosten van het uitvoeren van functies in het verbruiks abonnement.

[!INCLUDE [functions-consumption-metrics-queries](../../includes/functions-consumption-metrics-queries.md)]

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het controleren van Azure Functions:

+ [Azure Functions controleren](functions-monitoring.md)
+ [Bewaking voor Azure Functions configureren](configure-monitoring.md)

