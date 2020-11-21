---
title: Optimaliseer uw Gen2-cache
description: Meer informatie over het bewaken van uw Gen2-cache met behulp van de Azure Portal.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.subservice: sql-dw
ms.topic: conceptual
ms.date: 11/20/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 041751b5b23dbb3153f1ae638303579a860c0e5b
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95020160"
---
# <a name="how-to-monitor-the-adaptive-cache"></a>De adaptieve cache bewaken

In dit artikel wordt beschreven hoe u langzame query prestaties bewaakt en oplost door te bepalen of uw werk belasting optimaal gebruikmaakt van de adaptieve cache voor toegewezen SQL-groepen.

Met de speciale opslag architectuur van de SQL-groep worden automatisch de regel matig opgevraagde column Store-segmenten gelaagd in een cache op een op NVMe gebaseerd Ssd's. U krijgt betere prestaties wanneer uw query's segmenten ophalen die zich in de cache bevinden.
 
## <a name="troubleshoot-using-the-azure-portal"></a>Problemen oplossen met behulp van de Azure Portal

U kunt Azure Monitor gebruiken om de metrische gegevens van de cache weer te geven om de prestaties van query's op te lossen. Ga eerst naar de Azure Portal en klik op **monitor**, **metrische gegevens** en **+ een bereik selecteren**:

![Scherm afbeelding toont een bereik selecteren dat is geselecteerd in metrische gegevens in de Azure Portal.](./media/sql-data-warehouse-how-to-monitor-cache/cache-0.png)

Gebruik de balken zoeken en vervolg keuzelijst om uw toegewezen SQL-groep te zoeken. Selecteer vervolgens Toep assen.

![Scherm afbeelding toont het deel venster een bereik selecteren waar u uw data warehouse kunt selecteren.](./media/sql-data-warehouse-how-to-monitor-cache/cache-1.png)

De belangrijkste metrische gegevens voor het oplossen van problemen met de cache zijn **cache treffer percentages** en **percentage cache gebruik**. Selecteer **percentage cache treffers** en gebruik vervolgens de knop **metriek toevoegen** om **percentage gebruikt geheugen** toe te voegen. 

![Metrische cache gegevens](./media/sql-data-warehouse-how-to-monitor-cache/cache-2.png)

## <a name="cache-hit-and-used-percentage"></a>Aantal cache treffers en gebruikte percentages

In de volgende matrix worden scenario's beschreven op basis van de waarden van de cache-metrische gegevens:

|                                | **Percentage treffers in hoge cache** | **Percentage treffers voor lage cache** |
| :----------------------------: | :---------------------------: | :--------------------------: |
| **Percentage gebruikt hoog cache geheugen** |          Scenario 1           |          Scenario 2          |
| **Percentage beperkt cache gebruik**  |          Scenario 3           |          Scenario 4          |

**Scenario 1:** U kunt uw cache optimaal gebruiken. [Los](sql-data-warehouse-manage-monitor.md) andere gebieden op die uw query's mogelijk vertragen.

**Scenario 2:** De huidige werkset voor werk gegevens past niet in de cache, waardoor er een percentage van lage cache treffers wordt veroorzaakt door fysieke Lees bewerkingen. U kunt het prestatie niveau omhoog schalen en de werk belasting opnieuw uitvoeren om de cache te vullen.

**Scenario 3:** Waarschijnlijk wordt uw query traag als gevolg van redenen die niet aan de cache zijn gerelateerd. [Los](sql-data-warehouse-manage-monitor.md) andere gebieden op die uw query's mogelijk vertragen. U kunt ook overwegen [uw exemplaar omlaag te schalen](sql-data-warehouse-manage-monitor.md) om uw cache grootte te beperken om kosten te besparen. 

**Scenario 4:** U had een koude cache. Dit kan de reden zijn waarom uw query traag is. Overweeg de query opnieuw uit te voeren, omdat uw werk gegevensset nu in de cache moet worden opgeslagen. 

> [!IMPORTANT]
> Als het percentage van de cache treffer of het cache gebruik niet wordt bijgewerkt na het opnieuw uitvoeren van de workload, kan de werkset al in het geheugen worden geplaatst. Alleen geclusterde column Store-tabellen worden opgeslagen in de cache.

## <a name="next-steps"></a>Volgende stappen
Zie de [uitvoering van query's bewaken](sql-data-warehouse-manage-monitor.md#monitor-query-execution)voor meer informatie over het afstemmen van algemene query prestaties.
