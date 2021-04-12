---
title: 'Application Insights: talen, platforms en integraties | Microsoft Docs'
description: Talen, platforms en integraties die beschikbaar zijn voor Application Insights
ms.topic: conceptual
ms.date: 07/18/2019
ms.reviewer: olegan
ms.openlocfilehash: 399e57377a779622aa3073dfd3313cee1db345f8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100583851"
---
# <a name="supported-languages"></a>Ondersteunde talen

* [C#|VB (.NET)](./asp-net.md)
* [Java](./java-get-started.md)
* [JavaScript](./javascript.md)
* [Node.JS](./nodejs.md)
* [Python](./opencensus-python.md)

## <a name="supported-platforms-and-frameworks"></a>Ondersteunde platforms en frameworks

### <a name="instrumentation-for-already-deployed-applications-codeless-agent-based"></a>Instrumentatie voor toepassingen die al zijn geïmplementeerd (zonder code, op basis van een agent)
* [Azure VM-en Azure virtual machine-schaal sets](./azure-vm-vmss-apps.md)
* [Azure App Service](./azure-web-apps.md)
* [ASP.NET - voor apps die al live zijn](./monitor-performance-live-website-now.md)
* [Azure Cloud Services](./cloudservices.md), inclusief de web-en werk rollen
* [Azure Functions](../../azure-functions/functions-monitoring.md)
### <a name="instrumentation-through-code-sdks"></a>Instrumentatie via code (Sdk's)
* [ASP.NET](./asp-net.md)
* [ASP.NET Core](./asp-net-core.md)
* [Android](../app/mobile-center-quickstart.md) (App Center)
* [iOS](../app/mobile-center-quickstart.md) (App Center)
* [Java EE](./java-get-started.md)
* [Node.JS](https://www.npmjs.com/package/applicationinsights)
* [Python](./opencensus-python.md)
* [Universele Windows-app](../app/mobile-center-quickstart.md) (App Center)
* [Windows-bureaubladtoepassingen, -services en -werkrollen](./windows-desktop.md)
* [React](./javascript-react-plugin.md)
* [React Native](./javascript-react-native-plugin.md)

## <a name="logging-frameworks"></a>Frameworks voor logboekregistratie
* [ILogger](./ilogger.md)
* [Log4Net, NLog of System.Diagnostics.Trace](./asp-net-trace-logs.md)
* [Java, Log4J of Logback](./java-trace-logs.md)
* [LogStash-invoegtoepassing](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-output-applicationinsights)
* [Azure Monitor](/archive/blogs/msoms/application-insights-connector-in-oms)

## <a name="export-and-data-analysis"></a>Exporteren en gegevens analyse
* [Power BI](https://powerbi.microsoft.com/blog/explore-your-application-insights-data-with-power-bi/)
* [Stream Analytics](./export-power-bi.md)

## <a name="unsupported-sdks"></a>Niet-ondersteunde SDK's
Er is een aantal andere door de Community ondersteunde Sdk's aanwezig. Azure Monitor biedt echter alleen ondersteuning bij het gebruik van de ondersteunde Sdk's die op deze pagina worden weer gegeven. We evalueren voortdurend de mogelijkheden om onze ondersteuning voor andere talen uit te breiden. Volg daarom onze pagina met [github-meldingen](https://github.com/microsoft/ApplicationInsights-Announcements/issues) om het nieuwste SDK-nieuws te ontvangen. 

