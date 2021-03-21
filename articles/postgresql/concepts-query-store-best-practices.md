---
title: Aanbevolen procedures voor query Store in Azure Database for PostgreSQL-één server
description: In dit artikel worden aanbevolen procedures beschreven voor het query archief in Azure Database for PostgreSQL-één-server.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: dd39b7ecd51902f5035b4cd17d59dea964d0c962
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91708829"
---
# <a name="best-practices-for-query-store"></a>Aanbevolen procedures voor query Store

**Van toepassing op:** Azure Database for PostgreSQL-één server versie 9,6, 10, 11

In dit artikel vindt u een overzicht van de aanbevolen procedures voor het gebruik van query Store in Azure Database for PostgreSQL.

## <a name="set-the-optimal-query-capture-mode"></a>De optimale query Capture-modus instellen
Laat query Store de gegevens vastleggen die voor u belang rijk zijn. 

|**pg_qs pg_qs.query_capture_mode** | **Scenario**|
|---|---|
|_Alles_  |Analyseer uw werk belasting uitgebreid met alle query's en hun uitvoerings frequenties en andere statistieken. Nieuwe query's in uw workload identificeren. Detecteren of ad hoc-query's worden gebruikt voor het identificeren van de mogelijkheden voor de gebruiker of de automatische parameterisering. _Alle_ wordt geleverd met een verhoogde resource verbruiks kosten. |
|_Boven_  |Richt u op de belangrijkste query's die door clients worden uitgegeven.
|_Geen_ |U hebt al een queryset en tijd venster vastgelegd die u wilt onderzoeken en u wilt de afleidingen elimineren die andere query's kunnen introduceren. _Geen_ is geschikt voor het testen en in de Bank markeren van omgevingen. _Geen_ van beide moet worden gebruikt om te zorgen dat u belang rijke nieuwe query's kunt bijhouden en optimaliseren. U kunt geen gegevens meer herstellen op dit moment Windows. |

Query Store bevat ook een Store voor wacht statistieken. Er is een aanvullende query voor de opname modus die wacht statistieken regelt: **pgms_wait_sampling. query_capture_mode** kan worden ingesteld op _none_ of _all_. 

> [!NOTE] 
> **pg_qs. query_capture_mode** vervangt **pgms_wait_sampling. query_capture_mode**. Als pg_qs. query_capture_mode _geen_ is, heeft de instelling pgms_wait_sampling. query_capture_mode geen effect. 


## <a name="keep-the-data-you-need"></a>Behoud de gegevens die u nodig hebt
Met de para meter **pg_qs. retention_period_in_days** geeft u in dagen de Bewaar periode voor gegevens voor de query Store op. Oudere query-en statistische gegevens worden verwijderd. Query Store is standaard geconfigureerd om de gegevens 7 dagen te bewaren. Vermijd historische gegevens die u niet wilt gebruiken. Verhoog de waarde als u gegevens langer wilt gebruiken.


## <a name="set-the-frequency-of-wait-stats-sampling"></a>De frequentie van de steek proef van wachtende statistieken instellen 
De para meter **pgms_wait_sampling. history_period** geeft aan hoe vaak (in milliseconden) wacht gebeurtenissen worden bemonsterd. Hoe korter de periode, hoe vaker de steek proef. Er worden meer gegevens opgehaald, maar dit wordt geleverd met de kosten van een groter Resource verbruik. Verhoog deze periode als de server wordt geladen of als u de granulatie niet nodig hebt


## <a name="get-quick-insights-into-query-store"></a>Snelle inzichten verkrijgen in query Store
U kunt [query Performance Insight](concepts-query-performance-insight.md) in de Azure Portal gebruiken om snel inzicht te krijgen in de gegevens in het query archief. De visualisaties zijn het Opper vlak van de langst uitgevoerde query's en de langste wacht gebeurtenissen gedurende een periode.

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over het ophalen of instellen van para meters met behulp van de [Azure Portal](howto-configure-server-parameters-using-portal.md) of de [Azure cli](howto-configure-server-parameters-using-cli.md).