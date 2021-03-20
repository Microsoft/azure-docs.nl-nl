---
title: Azure-toepassing Insights-gegevens model-telemetrie traceren
description: Application Insights gegevens model voor het traceren van telemetrie
ms.topic: conceptual
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.openlocfilehash: 8fea4bbf590816b2ef168a2ed16f197389ee282e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87320541"
---
# <a name="trace-telemetry-application-insights-data-model"></a>Telemetrie traceren: Application Insights gegevens model

Telemetrie traceren (in [Application Insights](./app-insights-overview.md)) vertegenwoordigt `printf` opmaak overzichten die met tekst worden doorzocht. `Log4Net`, `NLog` en andere vermeldingen op basis van tekst logboek bestanden worden omgezet in exemplaren van dit type. De tracering heeft geen metingen als een uitbreid baarheid.

## <a name="message"></a>Bericht

Traceer bericht.

Maximale lengte: 32768 tekens

## <a name="severity-level"></a>Ernstniveau

Niveau van de ernst van de tracering. Waarde kan,,, `Verbose` `Information` `Warning` `Error` , `Critical` .

## <a name="custom-properties"></a>Aangepaste eigenschappen

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="next-steps"></a>Volgende stappen

- [Verken .net-traceer Logboeken in Application Insights](./asp-net-trace-logs.md).
- [Verken Java-traceer Logboeken in Application Insights](./java-trace-logs.md).
- Zie [gegevens model](data-model.md) voor Application Insights typen en gegevens model.
- [Telemetrie van aangepaste tracering schrijven](./api-custom-events-metrics.md#tracktrace)
- Bekijk de [platforms](./platforms.md) die door Application Insights worden ondersteund.

