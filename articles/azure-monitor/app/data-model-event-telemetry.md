---
title: Azure-toepassing Insights-gegevens model telemetrie-gebeurtenis-telemetrie | Microsoft Docs
description: Application Insights gegevens model voor telemetrie van gebeurtenissen
ms.topic: conceptual
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.openlocfilehash: 69685afa14352a22b58bccbea342038e4273696e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87320609"
---
# <a name="event-telemetry-application-insights-data-model"></a>Telemetrie van gebeurtenissen: Application Insights gegevens model

U kunt telemetrie-items (in [Application Insights](./app-insights-overview.md)) voor een gebeurtenis maken die een gebeurtenis vertegenwoordigen die in uw toepassing is opgetreden. Normaal gesp roken is het een gebruikers interactie, zoals het klikken op de knop of het afhandelen van de order. Het kan ook een levens cyclus gebeurtenis van een toepassing zijn, zoals initialisatie of configuratie-update. 

Semantisch, gebeurtenissen kunnen of kunnen niet worden gecorreleerd aan aanvragen. Als er echter goed wordt gebruikt, is de telemetrie van de gebeurtenis belang rijker dan aanvragen of traceringen. Gebeurtenissen vertegenwoordigen bedrijfs-telemetrie en moeten een onderliggend object zijn, waarbij minder agressieve [steek proeven](./api-filtering-sampling.md)worden genomen.

## <a name="name"></a>Name

Gebeurtenis naam. Als u de juiste groepering en bruikbare metrische gegevens wilt toestaan, beperkt u uw toepassing zodat er een klein aantal afzonderlijke gebeurtenis namen wordt gegenereerd. Gebruik bijvoorbeeld geen afzonderlijke naam voor elk gegenereerd exemplaar van een gebeurtenis.

Maximale lengte: 512 tekens

## <a name="custom-properties"></a>Aangepaste eigenschappen

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Aangepaste metingen

[!INCLUDE [application-insights-data-model-measurements](../../../includes/application-insights-data-model-measurements.md)]

## <a name="next-steps"></a>Volgende stappen

- Zie [gegevens model](data-model.md) voor Application Insights typen en gegevens model.
- [Telemetrie van aangepaste gebeurtenissen schrijven](./api-custom-events-metrics.md#trackevent)
- Bekijk de [platforms](./platforms.md) die door Application Insights worden ondersteund.

