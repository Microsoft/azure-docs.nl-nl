---
title: Overzicht van opname-Azure Time Series Insights Gen2 | Microsoft Docs
description: Meer informatie over gegevens opname in Azure Time Series Insights Gen2.
author: lyrana
ms.author: lyhughes
manager: deepakpalled
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/02/2020
ms.custom: seodec18
ms.openlocfilehash: 2b76490ab23cb14755f6f90a27ec8b176bb30a11
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96780022"
---
# <a name="azure-time-series-insights-gen2-data-ingestion-overview"></a>Overzicht van Azure Time Series Insights Gen2-gegevensopname

Uw Azure Time Series Insights Gen2-omgeving bevat een *opname-engine* voor het verzamelen, verwerken en opslaan van streaming tijd reeks gegevens. Wanneer gegevens binnenkomen in uw gebeurtenis bron (s), wordt Azure Time Series Insights Gen2 uw gegevens bijna in realtime gebruikt en opgeslagen.

[![Overzicht van gegevensopname](media/concepts-ingress-overview/ingress-overview.png)](media/concepts-ingress-overview/ingress-overview.png#lightbox)

## <a name="ingestion-topics"></a>Onderwerpen over opname

De volgende artikelen omvatten uitgebreide gegevens verwerking, waaronder aanbevolen procedures voor het volgen van:

* Meer informatie over [gebeurtenis bronnen](./concepts-streaming-ingestion-event-sources.md) en richt lijnen voor het selecteren van een tijds tempel van een gebeurtenis bron.

* De ondersteunde [gegevens typen](./concepts-supported-data-types.md) controleren

* Meer informatie over hoe de opname-engine een reeks [regels](./concepts-json-flattening-escaping-rules.md) toepast op uw JSON-eigenschappen om uw opslag account kolommen te maken.

* Controleer de [doorvoer beperkingen](./concepts-streaming-ingress-throughput-limits.md) van uw omgeving om te plannen voor uw schaal behoeften.

## <a name="next-steps"></a>Volgende stappen

* Ga door om meer te weten te komen over [gebeurtenis bronnen](./concepts-streaming-ingestion-event-sources.md) voor uw Azure time series Insights Gen2-omgeving.
