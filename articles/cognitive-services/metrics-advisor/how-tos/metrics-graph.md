---
title: Grafiek met metrische gegevens voor Advisor
titleSuffix: Azure Cognitive Services
description: Hoe u uw metrische gegevens kunt configureren en gerelateerde afwijkingen kunt visualiseren.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: mbullwin
ms.openlocfilehash: bcedef4a1339dacaff3fe841e97f985c42320819
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "92043150"
---
# <a name="how-to-build-a-metrics-graph-to-analyze-related-metrics"></a>Instructies: een grafiek met metrische gegevens maken om gerelateerde metrische gegevens te analyseren

Elke metriek in Metrics Advisor wordt afzonderlijk bewaakt door een model dat informatie uit historische gegevens leert om toekomstige trends te voors pellen. Elke metriek heeft een afzonderlijk model dat hierop wordt toegepast. In sommige gevallen kunnen diverse metrische gegevens echter aan elkaar gerelateerd zijn en moeten afwijkingen over meerdere metrische gegevens worden geanalyseerd. De **metrische gegevens grafiek** helpt bij dit. 

Als u bijvoorbeeld verschillende streams van telemetrie in afzonderlijke metrische gegevens hebt, worden deze door de metrische Advisor afzonderlijk gecontroleerd. Als afwijkingen in één metriek leiden tot afwijkingen in anderen, kan het vinden van deze relaties en de hoofd oorzaak in uw gegevens handig zijn bij het adresseren van incidenten. Met de grafiek metrische gegevens kunt u een Visual topologie grafiek van gevonden afwijkingen maken. 

## <a name="select-a-metric-to-put-the-first-node-to-the-graph"></a>Een metriek selecteren om het eerste knoop punt op de grafiek te plaatsen

Klik op het tabblad **grafiek met metrische gegevens** in de navigatie balk. De eerste stap bij het bouwen van een metrische grafiek is het plaatsen van een knoop punt op de grafiek. Selecteer de gegevensfeed en de metrische gegevens boven aan de pagina. Er wordt een knoop punt weer gegeven in het onderste deel venster. 

:::image type="content" source="../media/graph/metrics-graph-select.png" alt-text="Metrische waarde selecteren":::

## <a name="add-a-noderelation-on-existing-node"></a>Een knoop punt/relatie toevoegen aan een bestaand knoop punt

Vervolgens moet u een ander knoop punt toevoegen en een relatie met een bestaand knoop punt (en) opgeven. Selecteer een bestaand knoop punt en klik er met de rechter muisknop op. Er wordt een context menu weer gegeven met verschillende opties. 

Klik op **relatie toevoegen** en u kunt een andere metriek kiezen en het relatie type tussen de twee knoop punten opgeven. U kunt ook specifieke dimensie filters toep assen. 

:::image type="content" source="../media/graph/metrics-graph-node-action.png" alt-text="Een knoop punt en relatie toevoegen":::

Nadat u de bovenstaande stappen hebt herhaald, hebt u een grafiek met metrische gegevens waarin de relaties tussen alle gerelateerde metrische gegevens worden beschreven.
**Hint op knooppunt kleuren**
> [!TIP]
> - Wanneer u een meet waarde en dimensie filter selecteert, worden alle knoop punten met dezelfde metrische waarde en hetzelfde dimensie filter in de grafiek **<font color=blue>blauw</font>** gekleurd weer gegeven.
> - Niet-geselecteerde knoop punten die een metrische waarde in de grafiek vertegenwoordigen, worden **<font color=green>groen</font>** gekleurd.
> - Als er sprake is van een afwijkend in de huidige metriek, wordt het knoop punt **<font color=red>rood</font>** gekleurd.

## <a name="view-related-metrics-anomaly-status-in-incident-hub"></a>Afwijkings status van gerelateerde metrische gegevens in incident hub weer geven

Wanneer de grafiek metrische gegevens wordt gemaakt en er een afwijkende waarde wordt gedetecteerd voor metrische gegevens in de grafiek, kunt u gerelateerde afwijkings statussen bekijken en een weer gave op hoog niveau van het incident verkrijgen. 

Klik in het diagram op een incident en schuif omlaag naar de **analyse van metrische** gegevens, onder de diagnostische gegevens.

:::image type="content" source="../media/graph/metrics-graph-cross-metrics-analysis.png" alt-text="Gerelateerde metrische gegevens en afwijkingen weer geven":::

## <a name="next-steps"></a>Volgende stappen

- [Anomaliedetectie aanpassen met feedback](anomaly-feedback.md)
- [Een incident diagnosticeren](diagnose-incident.md).
- [Metrische gegevens configureren en detectieconfiguratie afstemmen](configure-metrics.md)
