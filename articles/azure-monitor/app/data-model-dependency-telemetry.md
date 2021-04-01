---
title: Azure Monitor Application Insights afhankelijkheids gegevens model
description: Application Insights gegevens model voor de telemetrie van de afhankelijkheid
ms.topic: conceptual
ms.date: 04/17/2017
ms.reviewer: sergkanz
ms.openlocfilehash: 0f9fc96479569c3411024068ed614d422035ab17
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87315968"
---
# <a name="dependency-telemetry-application-insights-data-model"></a>Telemetrie van afhankelijkheid: Application Insights gegevens model

Telemetrie van afhankelijkheid (in [Application Insights](./app-insights-overview.md)) vertegenwoordigt een interactie van het bewaakte onderdeel met een extern onderdeel, zoals SQL of een http-eind punt.

## <a name="name"></a>Name

De naam van de opdracht die is gestart met deze afhankelijkheids aanroep. Waarde voor weinig kardinaliteit. Voor beelden zijn opgeslagen procedure naam en URL-pad sjabloon.

## <a name="id"></a>Id

Id van een exemplaar van een afhankelijkheids aanroep. Wordt gebruikt voor correlatie met de aanvraag voor het telemetrie-item dat overeenkomt met deze afhankelijkheids aanroep. Zie [correlatie](./correlation.md) pagina voor meer informatie.

## <a name="data"></a>Gegevens

Opdracht gestart door deze afhankelijkheids aanroep. Voor beelden hiervan zijn de SQL-instructie en de HTTP-URL met alle query parameters.

## <a name="type"></a>Type

Naam van type afhankelijkheid. Lage kardinaliteit waarde voor logische groepering van afhankelijkheden en interpretatie van andere velden, zoals opdrachtnaam en resultCode. Voor beelden zijn SQL, Azure Table en HTTP.

## <a name="target"></a>Doel

Doel site van een afhankelijkheids aanroep. Voor beelden zijn server naam, hostadres. Zie [correlatie](./correlation.md) pagina voor meer informatie.

## <a name="duration"></a>Duur

De duur van aanvragen in de indeling: `DD.HH:MM:SS.MMMMMM` . Moet minder dan `1000` dagen zijn.

## <a name="result-code"></a>Resultaatcode

De resultaat code van een afhankelijkheids aanroep. Voor beelden hiervan zijn de SQL-fout code en de HTTP-status code.

## <a name="success"></a>Geslaagd

Indicatie van een geslaagde of mislukte aanroep.

## <a name="custom-properties"></a>Aangepaste eigenschappen

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Aangepaste metingen

[!INCLUDE [application-insights-data-model-measurements](../../../includes/application-insights-data-model-measurements.md)]


## <a name="next-steps"></a>Volgende stappen

- Het bijhouden van afhankelijkheden instellen voor [.net](./asp-net-dependencies.md).
- Het bijhouden van afhankelijkheden instellen voor [Java](./java-agent.md).
- [Aangepaste telemetrie van afhankelijkheid schrijven](./api-custom-events-metrics.md#trackdependency)
- Zie [gegevens model](data-model.md) voor Application Insights typen en gegevens model.
- Bekijk de [platforms](./platforms.md) die door Application Insights worden ondersteund.

