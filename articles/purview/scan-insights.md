---
title: Inzichten op uw gegevens in azure controle sfeer liggen scannen
description: In deze hand leiding wordt beschreven hoe u controle sfeer liggen Insights-scan rapporten kunt weer geven en gebruiken voor uw gegevens.
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/20/2020
ms.openlocfilehash: 00f72e1de230cdc68f86010b7b25d86debaa5eb5
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96575785"
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

### <a name="view-scan-insights"></a>Scan inzichten weer geven

1. Ga naar het scherm van het **Azure controle sfeer liggen** -exemplaar in het Azure Portal en selecteer uw controle sfeer liggen-account.

1. Selecteer op de pagina **overzicht** in de sectie **aan de slag** de tegel **Open controle sfeer liggen Studio** .

   :::image type="content" source="./media/scan-insights/portal-access.png" alt-text="Controle sfeer liggen starten vanuit de Azure Portal":::

1. Op de **Start** pagina van controle sfeer liggen selecteert u de tegel **inzichten weer geven** om toegang te krijgen tot uw **inzichten** - :::image type="icon" source="media/scan-insights/ico-insights.png" border="false"::: gebied.

   :::image type="content" source="./media/scan-insights/view-insights.png" alt-text="Bekijk uw inzichten in de Azure Portal":::

1. In het gebied **inzichten** :::image type="icon" source="media/scan-insights/ico-insights.png" border="false"::: selecteert u **scans** om het rapport controle sfeer liggen **Scan Insights** weer te geven.

### <a name="view-high-level-kpis-to-show-count-of-scans-by-status"></a>Kpi's op hoog niveau weer geven met het aantal scans per status
 
De Kpi's op hoog niveau tonen het totale aantal scans die binnen een periode worden uitgevoerd. De tijds periode wordt standaard ingesteld op 30 dagen. U kunt echter ook de laatste zeven dagen selecteren. Op basis van het tijd filter weerspiegelt de KPI-waarden het aantal scans op de juiste manier.


Op basis van de geselecteerde tijd filter-waarde kunt u de distributie van geslaagde, mislukte en geannuleerde scans per week of per dag in de grafiek bekijken.

   :::image type="content" source="./media/scan-insights/scan-insights.png" alt-text="Scan inzichten weer geven":::

## <a name="next-steps"></a>Volgende stappen

Meer informatie over Azure controle sfeer liggen Insight-rapporten met [Asset Insights](./asset-insights.md)
