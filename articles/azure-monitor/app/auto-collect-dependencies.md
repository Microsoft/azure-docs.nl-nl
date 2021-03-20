---
title: Automatische verzameling van Azure-toepassing Insights-afhankelijkheden | Microsoft Docs
description: Afhankelijkheden Application Insights automatisch verzamelen en visualiseren
ms.topic: reference
ms.custom: devx-track-dotnet
ms.date: 05/06/2020
ms.openlocfilehash: 8a4d79e52465e93fb4db2625217cb37a06917218
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91930863"
---
# <a name="dependency-auto-collection"></a>Afhankelijkheden automatisch verzamelen

Hieronder ziet u de momenteel ondersteunde lijst met afhankelijkheids aanroepen die automatisch worden gedetecteerd als afhankelijkheden, zonder dat er extra wijzigingen hoeven te worden aangebracht in de code van uw toepassing. Deze afhankelijkheden worden gevisualiseerd in de weer gaven Application Insights [toepassings kaart](./app-map.md) en [transactie diagnose](./transaction-diagnostics.md) . Als uw afhankelijkheid niet in de onderstaande lijst staat, kunt u deze nog steeds hand matig bijhouden met behulp van een [afhankelijkheids oproep](./api-custom-events-metrics.md#trackdependency)voor een track.

## <a name="net"></a>.NET

| App-Frameworks| Versies |
| ------------------------|----------|
| ASP.NET-webformulieren | 4.5 + |
| ASP.NET MVC | 4 + |
| ASP.NET WebAPI | 4.5 + |
| ASP.NET Core | 1.1 + |
| <b> Communicatie bibliotheken</b> |
| [Httpclient maakt](https://www.microsoft.com/net/) | 4.5 +, .NET Core 1.1 + |
| [SqlClient](https://www.nuget.org/packages/System.Data.SqlClient) | .NET Core 1.0 +, NuGet 4.3.0 |
| [Micro soft. data. SqlClient](https://www.nuget.org/packages/Microsoft.Data.SqlClient/1.1.2)| 1.1.0-laatste stabiele release. (Zie opmerking hieronder.)
| [Event hubs-client-SDK](https://www.nuget.org/packages/Microsoft.Azure.EventHubs) | 1.1.0 |
| [ServiceBus-client-SDK](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus) | 3.0.0 |
| <b>Storage-clients</b>|  |
| ADO.NET | 4.5 + |

> [!NOTE]
> Er is een [bekend probleem](https://github.com/microsoft/ApplicationInsights-dotnet/issues/1347) met oudere versies van micro soft. data. SqlClient. Het is raadzaam om 1.1.0 of hoger te gebruiken om dit probleem te verhelpen. Entity Framework Core kan niet per se worden geleverd met de laatste stabiele versie van micro soft. data. SqlClient, zodat we bevestigen dat u ten minste 1.1.0 bent om dit probleem te voor komen.   


## <a name="java"></a>Java
| App-servers | Versies |
|-------------|----------|
| [Tomcat](https://tomcat.apache.org/) | 7, 8 | 
| [JBoss EAP](https://developers.redhat.com/products/eap/download/) | 6, 7 |
| [Jetty](https://www.eclipse.org/jetty/) | 9 |
| <b>App-Frameworks </b> |  |
| [Spring](https://spring.io/) | 3,0 |
| [Spring Boot](https://spring.io/projects/spring-boot) | 1.5.9 +<sup>*</sup> |
| Java-servlet | 3.1 + |
| <b>Communicatie bibliotheken</b> |  |
| [Apache HTTP-client](https://mvnrepository.com/artifact/org.apache.httpcomponents/httpclient) | 4.3 +<sup>†</sup> |
| <b>Storage-clients</b> | |
| [SQL Server]( https://mvnrepository.com/artifact/com.microsoft.sqlserver/mssql-jdbc) | 1 +<sup>†</sup> |
| [PostgreSQL (bèta-ondersteuning)](https://github.com/Microsoft/ApplicationInsights-Java/blob/master/CHANGELOG.md#version-240-beta) | |
| [Oracle]( https://www.oracle.com/technetwork/database/application-development/jdbc/downloads/index.html) | 1 +<sup>†</sup> |
| [MySql]( https://mvnrepository.com/artifact/mysql/mysql-connector-java) | 1 +<sup>†</sup> |
| <b>Bibliotheken voor logboek registratie</b> | |
| [Logback](https://logback.qos.ch/) | 1 + |
| [Log4j](https://logging.apache.org/log4j/) | 1.2 + |
| <b>Metrische bibliotheken</b> |  |
| JMX | 1.0 + |

> [!NOTE]
> * Behalve ondersteuning voor reactieve Program ma's.
> <br>† Vereist de installatie van de [JVM-agent](./java-agent.md#install-the-application-insights-agent-for-java).

## <a name="nodejs"></a>Node.js

| Communicatie bibliotheken | Versies |
| ------------------------|----------|
| [http](https://nodejs.org/api/http.html), [https](https://nodejs.org/api/https.html) | 0,10 + |
| <b>Storage-clients</b> | |
| [Redis](https://www.npmjs.com/package/redis) | 2.x |
| [MongoDb](https://www.npmjs.com/package/mongodb); [MongoDb-kern](https://www.npmjs.com/package/mongodb-core) | 2. x-3. x |
| [MySQL](https://www.npmjs.com/package/mysql) | 2.0.0-2.16. x |
| [Postgresql](https://www.npmjs.com/package/pg); | 6. x-7. x |
| [PG-pool](https://www.npmjs.com/package/pg-pool) | 1. x-2. x |
| <b>Bibliotheken voor logboek registratie</b> | |
| [module](https://nodejs.org/api/console.html) | 0,10 + |
| [Bunyan](https://www.npmjs.com/package/bunyan) | 1.x |
| [Winston](https://www.npmjs.com/package/winston) | 2. x-3. x |

## <a name="javascript"></a>Javascript

| Communicatie bibliotheken | Versies |
| ------------------------|----------|
| [XMLHttpRequest](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) | Alles |

## <a name="next-steps"></a>Volgende stappen

- Aangepaste afhankelijkheden bijhouden instellen voor [.net](./asp-net-dependencies.md).
- Aangepaste afhankelijkheden bijhouden instellen voor [Java](./java-agent.md).
- Aangepaste afhankelijkheden bijhouden instellen voor [Opentellingen python](./opencensus-python-dependency.md).
- [Aangepaste telemetrie van afhankelijkheid schrijven](./api-custom-events-metrics.md#trackdependency)
- Zie [gegevens model](./data-model.md) voor Application Insights typen en gegevens model.
- Bekijk de [platforms](./platforms.md) die door Application Insights worden ondersteund.

