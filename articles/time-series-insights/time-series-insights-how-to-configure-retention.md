---
title: Bewaar termijn configureren in uw omgeving-Azure Time Series Insights | Microsoft Docs
description: Meer informatie over het configureren van Bewaar termijn in uw Azure Time Series Insights omgeving.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.topic: conceptual
ms.date: 09/29/2020
ms.custom: seodec18
ms.openlocfilehash: 468b4f7ca7b0af4abc32df5d9ef64a74154d3de1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91569415"
---
# <a name="configuring-retention-in-azure-time-series-insights-gen1"></a>Bewaar periode in Azure Time Series Insights gen1 configureren

> [!CAUTION]
> Dit is een Gen1-artikel.

In dit artikel wordt beschreven hoe u de **Bewaar tijd voor gegevens** en de **opslag limiet hebt overschreden** in azure time series Insights.

## <a name="summary"></a>Samenvatting

Elke Azure Time Series Insights omgeving heeft een instelling voor het configureren van **gegevens retentie tijd**. De waarde ligt tussen 1 en 400 dagen. De gegevens worden verwijderd op basis van de opslag capaciteit van de omgeving of de Bewaar periode (1-400), afhankelijk van wat het eerste komt.

Voor elke Azure Time Series Insights omgeving is een extra **opslag limiet** ingesteld. Met deze instelling bepaalt u het gedrag van ingang en leegmaken wanneer de maximale capaciteit van een omgeving wordt bereikt. Er zijn twee manieren om te kiezen:

- **Oude gegevens opschonen** (standaard)
- **Ingangs onderbrekingen**

Lees de informatie [over retentie in azure time series Insights](time-series-insights-concepts-retention.md)voor gedetailleerde gegevens om deze instellingen beter te begrijpen.  

## <a name="configure-data-retention"></a>Gegevensretentie configureren

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

1. Zoek uw bestaande Azure Time Series Insights-omgeving. Selecteer **alle resources** in het menu aan de linkerkant van het Azure Portal. Selecteer uw Azure Time Series Insights omgeving.

1. Selecteer in de kop **instellingen** de optie **opslag configuratie**.

    [![Selecteer onder instellingen opslag configuratie](media/data-retention/configure-data-retention.png)](media/data-retention/configure-data-retention.png#lightbox)

1. Selecteer de **Bewaar tijd voor gegevens (in dagen)** om de retentie te configureren met behulp van de schuif regelaar of typ een getal in het tekstvak.

1. Let op de **capaciteits** instelling, omdat deze configuratie invloed heeft op de maximale hoeveelheid gegevens gebeurtenissen en de totale opslag capaciteit voor het opslaan van gegevens.

1. De instelling voor het gedrag van de **opslag limiet is overschreden** . Selecteer **oude gegevens leegmaken** of **ingangs gedrag onderbreken** .

    [![Ingangs onderbrekingen: accepteren en opslaan.](media/data-retention/pause-ingress-accept-and-save.png)](media/data-retention/pause-ingress-accept-and-save.png#lightbox)

1. Raadpleeg de documentatie om inzicht te krijgen in de mogelijke Risico's van gegevens verlies. Selecteer **Opslaan** om de wijzigingen te configureren.

## <a name="next-steps"></a>Volgende stappen

- Lees voor meer informatie [uitleg over retentie in azure time series Insights](time-series-insights-concepts-retention.md).

- Meer informatie [over het schalen van uw Azure time series Insights-omgeving](time-series-insights-how-to-scale-your-environment.md).

- Meer informatie over [het plannen van uw omgeving](time-series-insights-environment-planning.md).
