---
title: 'Quickstart: Azure Time Series Insights Explorer - Azure Time Series Insights | Microsoft Docs'
description: Informatie over hoe u aan de slag kunt gaan met Azure Time Series Insights Explorer. Visualiseer grote hoeveelheden IoT-gegevens en maak een rondleiding door de belangrijkste functies van uw omgeving.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.topic: quickstart
ms.workload: big-data
ms.custom: mvc seodec18
ms.date: 09/30/2020
ms.openlocfilehash: dd5ed52187a7cd23142b59e20c91c6d125946a72
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91613778"
---
# <a name="quickstart-explore-azure-time-series-insights-gen1"></a>Quickstart: Azure Time Series Insights Gen1 verkennen

> [!CAUTION]
> Dit is een Gen1-artikel.

Deze quickstart over Azure Time Series Insights Explorer helpt u om aan de slag te gaan met Azure Time Series Insights in een gratis demo-omgeving. In deze quickstart leert u hoe u in uw webbrowser grote hoeveelheden IoT-gegevens kunt visualiseren en maakt u een rondleiding door de belangrijkste functies die nu algemeen beschikbaar zijn.

Azure Time Series Insights is een volledig beheerde service voor analyse, opslag en visualisatie die het eenvoudig maakt om miljarden IoT-gebeurtenissen tegelijkertijd te verkennen en te analyseren. De service biedt u een globaal overzicht van uw gegevens zodat u uw IoT-oplossing snel kunt valideren en kostbare stilstand van essentiële apparaten kunt vermijden. Azure Time Series Insights helpt u bij het ontdekken van verborgen trends, vinden van afwijkingen en bijna in realtime uitvoeren van oorzaak-gevolganalyses.

Voor extra flexibiliteit kunt u Azure Time Series Insights toevoegen aan een bestaande toepassing met behulp van de krachtige [REST API's](./concepts-query-overview.md) en [client-SDK](https://github.com/microsoft/tsiclient). U kunt de API's gebruiken voor het opslaan, opvragen en gebruiken van tijdreeksgegevens in een clienttoepassing naar keuze. U kunt ook de client-SDK gebruiken om UI-componenten toe te voegen aan uw bestaande toepassing.

Deze quickstart over Azure Time Series Insights Explorer biedt een rondleiding door de functies.

> [!IMPORTANT]
> Maak een [gratis Azure-account](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) als u er nog geen hebt gemaakt.

## <a name="prepare-the-demo-environment"></a>De testomgeving voorbereiden

1. Ga in een browser naar de [Gen1-demo](https://insights.timeseries.azure.com/demo).

1. Meld u indien nodig aan bij Azure Time Series Insights Explorer. Gebruik hiervoor de referenties van uw Azure-account.

1. De pagina van de korte rondleiding door Azure Time Series Insights wordt weergegeven. Selecteer **Next** om de rondleiding te starten.

   [![Welkom bij quickstart: selecteer Next](media/quickstart/quickstart-welcome.png)](media/quickstart/quickstart-welcome.png#lightbox)

## <a name="explore-the-demo-environment"></a>De testomgeving verkennen

1. Het venster **Time selection** wordt weergegeven. Gebruik dit venster om het tijdsbestek te selecteren dat u wilt visualiseren.

   [![Venster Time selection](media/quickstart/quickstart-time-selection-panel.png)](media/quickstart/quickstart-time-selection-panel.png#lightbox)

1. Selecteer een tijdsbestek en sleep dit in de regio. Selecteer vervolgens **Search**.

   [![Een tijdsbestek selecteren](media/quickstart/quickstart-select-time.png)](media/quickstart/quickstart-select-time.png#lightbox)

   U ziet in Azure Time Series Insights een grafiek met een visualisatie voor het opgegeven tijdsbestek. U kunt verschillende acties uitvoeren in het lijndiagram. U kunt bijvoorbeeld filteren, vastmaken, sorteren en stapelen.

   Als u terug wilt naar het venster **Time selection**, selecteert u de pijl-omlaag, zoals wordt aangegeven:

   [![Grafiek](media/quickstart/quickstart-select-down-arrow.png)](media/quickstart/quickstart-select-down-arrow.png#lightbox)

1. Selecteer **Add** in het venster **Terms** om een nieuwe zoekterm toe te voegen.

   [![Venster voor het toevoegen van zoektermen](media/quickstart/quickstart-add-terms.png)](media/quickstart/quickstart-add-terms.png#lightbox)

1. In de grafiek kunt u een regio selecteren, hier met de rechtermuisknop op klikken en vervolgens **Explore Events** selecteren.

   [![Gebeurtenissen verkennen](media/quickstart/quickstart-explore-events.png)](media/quickstart/quickstart-explore-events.png#lightbox)

   U ziet een raster met de onbewerkte gegevens uit de regio die u verkent.

   [![Gebeurtenissen verkennen - rastergegevensweergave](media/quickstart/quickstart-explore-events-grid-data.png)](media/quickstart/quickstart-explore-events-grid-data.png#lightbox)

## <a name="select-and-filter-data"></a>Gegevens selecteren en filteren

1. Bewerk uw termen om de waarden in de grafiek te wijzigen. Voeg nog een term toe om verschillende soorten waarden kruislings te correleren.

   [![Een term toevoegen](media/quickstart/quickstart-add-a-term.png)](media/quickstart/quickstart-add-a-term.png#lightbox)

1. Laat het vak **Filter series** leeg om alle geselecteerde zoektermen weer te geven of geef een filterterm op in het vak **Filter series** om geavanceerd te filteren op reeksen.

   [![Filter series](media/quickstart/quickstart-filter-series.png)](media/quickstart/quickstart-filter-series.png#lightbox)

   Voor deze quickstart voeren we **Station5** in om de temperatuur- en drukwaarden voor dat station kruislings te correleren.

Als u klaar bent met de quickstart, kunt u experimenteren met de set voorbeeldgegevens om verschillende visualisaties te maken.

## <a name="clean-up-resources"></a>Resources opschonen

Nu u de zelfstudie hebt voltooid, kunt u de resources die u hebt gemaakt opschonen:

1. Selecteer in het linkermenu in de [Azure-portal](https://portal.azure.com) de optie **Alle resources** en zoek uw Azure Time Series Insights-resourcegroep.
1. U kunt de hele resourcegroep (en alle resources erin) verwijderen door **Verwijderen** te selecteren of elke resource afzonderlijk verwijderen.

## <a name="next-steps"></a>Volgende stappen

* U kunt nu uw eigen Azure Time Series Insights-omgeving gaan maken. Lees [Uw Azure Time Series Insights-omgeving plannen](time-series-insights-environment-planning.md).
