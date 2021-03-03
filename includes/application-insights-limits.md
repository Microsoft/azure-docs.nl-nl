---
title: bestand opnemen
description: bestand opnemen
services: application-insights
author: lgayhardt
ms.service: application-insights
ms.topic: include
ms.date: 08/06/2019
ms.author: lagayhar
ms.custom: include file
ms.openlocfilehash: 1c4f6b876a4aa80c7e51f2bb3ca88234203d0daa
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101726173"
---
Er gelden enkele beperkingen voor het aantal meetgegevens en gebeurtenissen per toepassing, per instrumentatiesleutel. De limieten zijn afhankelijk van de [prijscategorie](https://azure.microsoft.com/pricing/details/application-insights/) die u kiest.

| Resource | Standaardlimiet | Opmerking
| --- | --- | --- |
| Totale hoeveelheid gegevens per dag | 100 GB | U kunt gegevens beperken door een maximum in te stellen. Als u meer gegevens nodig hebt, kunt u de limiet in de portal verhogen tot 1000 GB. Voor capaciteiten groter dan 1000 GB stuurt u een mail naar AIDataCap@microsoft.com.
| Beperking | 32.000 gebeurtenissen per seconde | De limiet wordt gemeten in een minuut.
| Logboeken voor gegevens retentie | [30 - 730 dagen](../articles/azure-monitor/app/pricing.md#change-the-data-retention-period)  | Deze resource is voor [Logboeken](../articles/azure-monitor/logs/log-query-overview.md).
| Metrieken voor gegevens behoud | 90 dagen| Deze resource is voor [Metrics Explorer](../articles/azure-monitor/essentials/metrics-charts.md).
| Bewaartijd van gedetailleerde resultaten van [beschikbaarheidstests met meerdere stappen](../articles/azure-monitor/app/availability-multistep.md) | 90 dagen | Deze resource biedt gedetailleerde resultaten van elke stap.
| Maximale grootte telemetriegegeven | 64 KB |
| Maximumaantal telemetriegegevens per batch | 64 K |
| Naamlengte voor de eigenschappen en meetgegevens | 150 | Raadpleeg [typeschema's](https://github.com/MohanGsk/ApplicationInsights-Home/tree/master/EndpointSpecs/Schemas/Bond).
| Lengte van de tekenreeks eigenschapswaarde | 8\.192  | Raadpleeg [typeschema's](https://github.com/MohanGsk/ApplicationInsights-Home/tree/master/EndpointSpecs/Schemas/Bond).
| Lengte van berichten voor tracering en uitzonderingen | 32.768  | Raadpleeg [typeschema's](https://github.com/MohanGsk/ApplicationInsights-Home/tree/master/EndpointSpecs/Schemas/Bond).
| Aantal [beschikbaarheidstests](../articles/azure-monitor/app/monitor-web-app-availability.md) per app | 100 |
| Gegevensretentie voor [Profiler](../articles/azure-monitor/app/profiler.md) | 5 dagen |
| Verzonden gegevens per dag voor [Profiler](../articles/azure-monitor/app/profiler.md) | 10 GB |

Zie [Over prijzen en quota voor Application Insights](../articles/azure-monitor/app/pricing.md) voor meer informatie.