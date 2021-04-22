---
title: Azure-toepassing Insights - Automatische verzameling van afhankelijkheden | Microsoft Docs
description: Application Insights automatisch afhankelijkheden verzamelen en visualiseren
ms.topic: reference
ms.custom: devx-track-dotnet
ms.date: 05/06/2020
ms.openlocfilehash: aa4d39ca8964e95ca787d236223e2b475a9597c1
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107873825"
---
# <a name="dependency-auto-collection"></a>Afhankelijkheden automatisch verzamelen

Hieronder vindt u de momenteel ondersteunde lijst met afhankelijkheidsoproepen die automatisch worden gedetecteerd als afhankelijkheden zonder dat er aanvullende wijzigingen in de code van uw toepassing nodig zijn. Deze afhankelijkheden worden gevisualiseerd in Application Insights [weergaven Toepassingskaart](./app-map.md) en [Transactiediagnose.](./transaction-diagnostics.md) Als uw afhankelijkheid niet in de onderstaande lijst staat, kunt u deze nog steeds handmatig bijhouden met een [afhankelijkheidsaanroep bijhouden.](./api-custom-events-metrics.md#trackdependency)

## <a name="net"></a>.NET

| App-frameworks| Versies |
| ------------------------|----------|
| ASP.NET Webforms | 4.5+ |
| ASP.NET MVC | 4+ |
| ASP.NET WebAPI | 4.5+ |
| ASP.NET Core | 1.1+ |
| <b> Communicatiebibliotheken</b> |
| [HttpClient](https://dotnet.microsoft.com) | 4.5+, .NET Core 1.1+ |
| [SqlClient](https://www.nuget.org/packages/System.Data.SqlClient) | .NET Core 1.0+, NuGet 4.3.0 |
| [Microsoft.Data.SqlClient](https://www.nuget.org/packages/Microsoft.Data.SqlClient/1.1.2)| 1.1.0 : nieuwste stabiele release. (Zie Opmerking hieronder.)
| [EventHubs Client SDK](https://www.nuget.org/packages/Microsoft.Azure.EventHubs) | 1.1.0 |
| [ServiceBus Client SDK](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus) | 3.0.0 |
| <b>Opslag-clients</b>|  |
| ADO.NET | 4.5+ |

> [!NOTE]
> Er is een [bekend probleem](https://github.com/microsoft/ApplicationInsights-dotnet/issues/1347) met oudere versies van Microsoft.Data.SqlClient. We raden u aan 1.1.0 of hoger te gebruiken om dit probleem te verhelpen. Entity Framework Core wordt niet noodzakelijkerwijs met de nieuwste stabiele versie van Microsoft.Data.SqlClient meeverworden. Daarom wordt u geadviseerd om te bevestigen dat u ten minste 1.1.0 gebruikt om dit probleem te voorkomen.   


## <a name="java"></a>Java
| App-servers | Versies |
|-------------|----------|
| [Tomcat](https://tomcat.apache.org/) | 7, 8 | 
| [JBoss EAP](https://developers.redhat.com/products/eap/download/) | 6, 7 |
| [Jetty](https://www.eclipse.org/jetty/) | 9 |
| <b>App-frameworks </b> |  |
| [Spring](https://spring.io/) | 3,0 |
| [Spring Boot](https://spring.io/projects/spring-boot) | 1.5.9+<sup>*</sup> |
| Java Servlet | 3.1+ |
| <b>Communicatiebibliotheken</b> |  |
| [Apache HTTP-client](https://mvnrepository.com/artifact/org.apache.httpcomponents/httpclient) | 4.3+<sup>†</sup> |
| <b>Opslag-clients</b> | |
| [SQL Server]( https://mvnrepository.com/artifact/com.microsoft.sqlserver/mssql-jdbc) | 1+<sup>†</sup> |
| [PostgreSQL (bèta-ondersteuning)](https://github.com/Microsoft/ApplicationInsights-Java/blob/master/CHANGELOG.md#version-240-beta) | |
| [Oracle]( https://www.oracle.com/technetwork/database/application-development/jdbc/downloads/index.html) | 1+<sup>†</sup> |
| [Mysql]( https://mvnrepository.com/artifact/mysql/mysql-connector-java) | 1+<sup>†</sup> |
| <b>Bibliotheken voor logboekregistratie</b> | |
| [Logback](https://logback.qos.ch/) | 1+ |
| [Log4j](https://logging.apache.org/log4j/) | 1.2+ |
| <b>Bibliotheken voor metrische gegevens</b> |  |
| Jmx | 1.0+ |

> [!NOTE]
> *Behalve ondersteuning voor reactieve programma's.
> <br>†Een installatie van [JVM-agent is nodig.](./java-agent.md#install-the-application-insights-agent-for-java)

## <a name="nodejs"></a>Node.js

| Communicatiebibliotheken | Versies |
| ------------------------|----------|
| [HTTP,](https://nodejs.org/api/http.html) [HTTPS](https://nodejs.org/api/https.html) | 0.10+ |
| <b>Opslag-clients</b> | |
| [Redis](https://www.npmjs.com/package/redis) | 2.x |
| [MongoDb](https://www.npmjs.com/package/mongodb); [MongoDb Core](https://www.npmjs.com/package/mongodb-core) | 2.x - 3.x |
| [MySQL](https://www.npmjs.com/package/mysql) | 2.0.0 - 2.16.x |
| [PostgreSql](https://www.npmjs.com/package/pg); | 6.x - 7.x |
| [pg-pool](https://www.npmjs.com/package/pg-pool) | 1.x - 2.x |
| <b>Bibliotheken voor logboekregistratie</b> | |
| [Console](https://nodejs.org/api/console.html) | 0.10+ |
| [Bunyan](https://www.npmjs.com/package/bunyan) | 1.x |
| [Winston](https://www.npmjs.com/package/winston) | 2.x - 3.x |

## <a name="javascript"></a>Javascript

| Communicatiebibliotheken | Versies |
| ------------------------|----------|
| [XMLHttpRequest](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) | Alles |

## <a name="next-steps"></a>Volgende stappen

- Aangepaste afhankelijkheidstracking instellen voor [.NET](./asp-net-dependencies.md).
- Aangepaste afhankelijkheidstracking instellen voor [Java](./java-agent.md).
- Aangepaste afhankelijkheidstracking instellen voor [OpenCensus Python](./opencensus-python-dependency.md).
- [Aangepaste afhankelijkheids-telemetrie schrijven](./api-custom-events-metrics.md#trackdependency)
- Zie [gegevensmodel voor](./data-model.md) Application Insights en gegevensmodel.
- Bekijk de [platforms die](./platforms.md) worden ondersteund door Application Insights.

