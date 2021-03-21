---
title: Inzichten op uw gegevens in azure controle sfeer liggen scannen
description: In deze hand leiding wordt beschreven hoe u controle sfeer liggen Insights-scan rapporten kunt weer geven en gebruiken voor uw gegevens.
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/20/2020
ms.openlocfilehash: 7807659a30127f39bb79ad99bdb733c12eb1d25d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100548667"
---
# <a name="scan-insights-on-your-data-in-azure-purview"></a>Inzichten op uw gegevens in azure controle sfeer liggen scannen

In deze hand leiding wordt beschreven hoe u rapporten van Azure controle sfeer liggen scan Insight kunt openen, bekijken en filteren voor uw gegevens.

In deze hand leiding leert u het volgende:

> [!div class="checklist"]
> * Bekijk inzichten uit uw controle sfeer liggen-account.
> * Bekijk de ogen van uw scans.

## <a name="prerequisites"></a>Vereisten

Voordat u aan de slag gaat met controle sfeer liggen Insights, moet u ervoor zorgen dat u de volgende stappen hebt uitgevoerd:

* Stel uw Azure-resources in en vul het account in met gegevens.
* Een scan voor de gegevens bron instellen en volt ooien.

Zie [gegevens bronnen beheren in azure controle sfeer liggen (preview)](manage-data-sources.md)voor meer informatie.

## <a name="use-purview-scan-insights"></a>Controle sfeer liggen scan Insights gebruiken

In azure controle sfeer liggen kunt u bron typen registreren en scannen. U kunt de scan status gedurende een periode in scan Insights bekijken. De inzichten geven aan hoeveel scans zijn mislukt, geslaagd of geannuleerd binnen een bepaalde tijds periode.

### <a name="view-scan-insights"></a>Scaninzichten weergeven

1. Ga naar het scherm van het **Azure controle sfeer liggen** -exemplaar in het Azure Portal en selecteer uw controle sfeer liggen-account.

1. Selecteer op de pagina **overzicht** in de sectie **aan de slag** de tegel **Open controle sfeer liggen Studio** .

   :::image type="content" source="./media/scan-insights/portal-access.png" alt-text="Controle sfeer liggen starten vanuit de Azure Portal":::

1. Op de **Start** pagina van controle sfeer liggen selecteert u de tegel **inzichten weer geven** om toegang te krijgen tot uw **inzichten** - :::image type="icon" source="media/scan-insights/ico-insights.png" border="false"::: gebied.

   :::image type="content" source="./media/scan-insights/view-insights.png" alt-text="Bekijk uw inzichten in de Azure Portal":::

1. In het gebied **inzichten** :::image type="icon" source="media/scan-insights/ico-insights.png" border="false"::: selecteert u **scans** om het rapport controle sfeer liggen **Scan Insights** weer te geven.

### <a name="view-high-level-kpis-to-show-count-of-scans-by-status-and-deep-dive-into-each-scan"></a>Kpi's op hoog niveau weer geven met het aantal scans per status en diep gaande in elke scan
 
1. De Kpi's op hoog niveau tonen het totale aantal scans die binnen een periode worden uitgevoerd. De tijds periode wordt standaard ingesteld op 30 dagen. U kunt echter ook de laatste zeven dagen selecteren. Op basis van het tijd filter weerspiegelt de KPI-waarden het aantal scans op de juiste manier.


1. Op basis van de geselecteerde tijd filter-waarde kunt u de distributie van geslaagde, mislukte en geannuleerde scans per week of per dag in de grafiek bekijken.

1. Onder aan de grafiek ziet u een koppeling **meer weer geven** waarmee u verder kunt gaan. De koppeling opent de pagina  **Scan status** in de ervaring scan Insights. Hier ziet u een scan naam en het aantal keer dat deze is geslaagd, mislukt of geannuleerd in de afgelopen 30 dagen.

    :::image type="content" source="./media/scan-insights/main-graph.png" alt-text="Scan status na verloop van tijd weer geven":::

4. U kunt een specifieke scan verder verkennen door te klikken op de **naam** van de scan waarmee u wordt verbonden met de scan geschiedenis in de **bronnen** ervaring van Azure controle sfeer liggen. Op de pagina uitvoerings geschiedenis kunt u de uitvoerings-ID ophalen die u kan helpen bij het onderzoeken van fouten.

    :::image type="content" source="./media/scan-insights/scan-status.png" alt-text="Scan details weer geven":::

5. Ten slotte kunt u teruggaan naar de pagina scan Insights- **Scan status** door de brood crumbs in de linkerbovenhoek van de pagina uitvoerings geschiedenis te volgen.

    :::image type="content" source="./media/scan-insights/scan-history.png" alt-text="Bekijk het scanoverzicht"::: 

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over Azure controle sfeer liggen **Insights** met [Data Insights](./concept-insights.md)

* Meer informatie over de **bronnen** van Azure controle sfeer liggen voor het [beheren van gegevens bronnen](./manage-data-sources.md)