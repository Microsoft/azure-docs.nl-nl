---
title: Uw Gen2-cache optimaliseren
description: Meer informatie over het bewaken van uw Gen2-cache met behulp van Azure Portal.
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.subservice: sql-dw
ms.topic: conceptual
ms.date: 11/20/2020
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 9de795c54f55295fa69ed7fcb5dd894e2963385b
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107566627"
---
# <a name="how-to-monitor-the-adaptive-cache"></a>De adaptieve cache bewaken

In dit artikel wordt beschreven hoe u trage queryprestaties kunt bewaken en problemen kunt oplossen door te bepalen of uw workload optimaal gebruik maakt van de adaptieve cache voor toegewezen SQL-pools.

De opslagarchitectuur van de toegewezen SQL-pool maakt automatisch een opslaglaag voor de kolomopslagsegmenten die het vaakst worden opgevraagd in een cache die zich op NVMe-gebaseerde SSD's opgeslagen. U krijgt betere prestaties wanneer uw query's segmenten ophalen die zich in de cache hebben opgeslagen.
 
## <a name="troubleshoot-using-the-azure-portal"></a>Problemen oplossen met behulp van de Azure Portal

U kunt de Azure Monitor cachegegevens weergeven om problemen met queryprestaties op te lossen. Ga eerst naar de Azure Portal klik op **Bewaken,** **Metrische** gegevens en **+ Een bereik selecteren:**

![Schermopname toont Select a scope selected from Metrics in the Azure Portal.](./media/sql-data-warehouse-how-to-monitor-cache/cache-0.png)

Gebruik de zoek- en vervolgkeuzebalken om uw toegewezen SQL-pool te vinden. Selecteer vervolgens Toepassen.

![Schermopname van het deelvenster Een bereik selecteren waarin u uw datawarehouse kunt selecteren.](./media/sql-data-warehouse-how-to-monitor-cache/cache-1.png)

De belangrijkste metrische gegevens voor het oplossen van problemen met de cache zijn **Percentage cache-treffers** en **Percentage gebruikt in cache.** Selecteer **Percentage treffers in cache** en gebruik vervolgens de knop **Metrische** gegevens toevoegen om Percentage **gebruikte cache toe te voegen.** 

![Metrische gegevens van cache](./media/sql-data-warehouse-how-to-monitor-cache/cache-2.png)

## <a name="cache-hit-and-used-percentage"></a>Treffer in cache en percentage gebruikt

In de onderstaande matrix worden scenario's beschreven op basis van de waarden van de metrische gegevens van de cache:

|                                | **Hoog trefferpercentage cache** | **Percentage treffers bij lage cache** |
| :----------------------------: | :---------------------------: | :--------------------------: |
| **Percentage gebruikt voor hoge cache** |          Scenario 1           |          Scenario 2          |
| **Percentage gebruikt in lage cache**  |          Scenario 3           |          Scenario 4          |

**Scenario 1:** U maakt optimaal gebruik van uw cache. [Problemen](sql-data-warehouse-manage-monitor.md) met andere gebieden oplossen die uw query's mogelijk vertragen.

**Scenario 2:** Uw huidige werkgegevensset kan niet in de cache passen, wat leidt tot een laag trefferpercentage van de cache vanwege fysieke leesgegevens. Overweeg om uw prestatieniveau op te schalen en de workload opnieuw uit te laten gaan om de cache te vullen.

**Scenario 3:** Het is waarschijnlijk dat uw query traag wordt uitgevoerd vanwege redenen die geen verband houden met de cache. [Problemen](sql-data-warehouse-manage-monitor.md) met andere gebieden oplossen die uw query's mogelijk vertragen. U kunt ook overwegen om [uw exemplaar omlaag te schalen om](sql-data-warehouse-manage-monitor.md) de cachegrootte te verkleinen om kosten te besparen. 

**Scenario 4:** U had een koude cache. Dit kan de reden zijn waarom uw query traag was. Overweeg uw query opnieuw uit te voeren, aangezien uw werkset nu in de cache moet zijn opgeslagen. 

> [!IMPORTANT]
> Als het gebruikte percentage van de cache niet wordt bijgewerkt na het opnieuw uitvoeren van uw workload, kan uw werkset al in het geheugen worden opgeslagen. Alleen geclusterde columnstore-tabellen worden in de cache opgeslagen.

## <a name="next-steps"></a>Volgende stappen
Zie Query-uitvoering bewaken voor meer informatie over het afstemmen [van algemene queryprestaties.](sql-data-warehouse-manage-monitor.md#monitor-query-execution)
