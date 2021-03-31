---
title: Telemetrie-gegevens model Azure-toepassing Insights-uitzonde ring
description: Application Insights gegevens model voor uitzonderings-telemetrie
ms.topic: conceptual
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.openlocfilehash: 6732fb3dde79abd5c69e2b0a91710bdd2356d6aa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87320592"
---
# <a name="exception-telemetry-application-insights-data-model"></a>Telemetrie van uitzonde ring: Application Insights gegevens model

In [Application Insights](./app-insights-overview.md)vertegenwoordigt een exemplaar van uitzonde ring een verwerkte of onverwerkte uitzonde ring die is opgetreden tijdens de uitvoering van de bewaakte toepassing.

## <a name="problem-id"></a>Probleem-id

De id van de plaats waar de uitzonde ring is opgetreden in de code. Wordt gebruikt voor de groepering van uitzonde ringen. Meestal een combi natie van het uitzonderings type en een functie van de aanroep stack.

Maximale lengte: 1024 tekens

## <a name="severity-level"></a>Ernstniveau

Niveau van de ernst van de tracering. Waarde kan,,, `Verbose` `Information` `Warning` `Error` , `Critical` .

## <a name="exception-details"></a>Details van uitzondering

(Uitgebreid)

## <a name="custom-properties"></a>Aangepaste eigenschappen

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Aangepaste metingen

[!INCLUDE [application-insights-data-model-measurements](../../../includes/application-insights-data-model-measurements.md)]

## <a name="next-steps"></a>Volgende stappen

- Zie [gegevens model](data-model.md) voor Application Insights typen en gegevens model.
- Meer informatie over het [opsporen van uitzonde ringen in uw web-apps met Application Insights](./asp-net-exceptions.md).
- Bekijk de [platforms](./platforms.md) die door Application Insights worden ondersteund.

