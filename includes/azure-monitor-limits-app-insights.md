---
title: bestand opnemen
description: bestand opnemen
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 02/07/2019
ms.author: robb
ms.custom: include file
ms.openlocfilehash: 397011cfd862607932f671c1f2cacd25513bdeb2
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96026771"
---
Er gelden enkele beperkingen voor het aantal meetgegevens en gebeurtenissen per toepassing, per instrumentatiesleutel. De limieten zijn afhankelijk van de [prijscategorie](https://azure.microsoft.com/pricing/details/application-insights/) die u kiest.

| Resource | Limiet | Opmerking
| --- | --- | --- |
| Totale hoeveelheid gegevens per dag | 100 GB | U kunt gegevens beperken door een maximum in te stellen. Als u meer gegevens nodig hebt, kunt u de limiet in de portal verhogen tot 1000 GB. Voor capaciteiten groter dan 1000 GB stuurt u een mail naar AIDataCap@microsoft.com.
| Beperking | 32.000 gebeurtenissen per seconde | De limiet wordt gemeten in een minuut.
| Bewaartijd voor gegevens | 90 dagen | Deze resource is voor [Search](../articles/azure-monitor/app/diagnostic-search.md), [Analytics](../articles/azure-monitor/log-query/log-query-overview.md) en [Metrics Explorer](../articles/azure-monitor/platform/metrics-charts.md).
| Bewaartijd van gedetailleerde resultaten van [beschikbaarheidstests met meerdere stappen](../articles/azure-monitor/app/availability-multistep.md) | 90 dagen | Deze resource biedt gedetailleerde resultaten van elke stap.
| Maximale gebeurtenisgrootte | 64.000.000 bytes |
| Naamlengte voor de eigenschappen en meetgegevens | 150 | Raadpleeg [typeschema's](https://github.com/MohanGsk/ApplicationInsights-Home/tree/master/EndpointSpecs/Schemas/Bond/).
| Lengte van de tekenreeks eigenschapswaarde | 8\.192 | Raadpleeg [typeschema's](https://github.com/MohanGsk/ApplicationInsights-Home/tree/master/EndpointSpecs/Schemas/Bond/).
| Lengte van berichten voor tracering en uitzonderingen | 32.768  | Raadpleeg [typeschema's](https://github.com/MohanGsk/ApplicationInsights-Home/tree/master/EndpointSpecs/Schemas/Bond/).
| Aantal [beschikbaarheidstests](../articles/azure-monitor/app/monitor-web-app-availability.md) per app | 100 |
| Gegevensretentie voor [Profiler](../articles/azure-monitor/app/profiler.md) | 5 dagen |
| Verzonden gegevens per dag voor [Profiler](../articles/azure-monitor/app/profiler.md) | 10 GB |

Zie [Over prijzen en quota voor Application Insights](../articles/azure-monitor/app/pricing.md) voor meer informatie.