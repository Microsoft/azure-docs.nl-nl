---
title: Query Store best practices in Azure Database for PostgreSQL - Flex Server
description: In dit artikel worden de best practices beschreven voor Query Store in Azure Database for PostgreSQL Flex Server.
author: ssen-msft
ms.author: ssen
ms.service: postgresql
ms.topic: conceptual
ms.date: 04/01/2021
ms.openlocfilehash: 9ee2dc4b3e8ea6a9f892adbc378e8e13b8bd8160
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107559022"
---
# <a name="best-practices-for-query-store---flexible-server"></a>Best practices voor Query Store - Flexible Server

**Van toepassing op:** Azure Database for PostgreSQL Flex Server versie 11, 12

Dit artikel bevat een overzicht van de best practices voor het gebruik van Query Store in Azure Database for PostgreSQL.

## <a name="set-the-optimal-query-capture-mode"></a>De optimale modus voor het vastleggen van query's instellen
Laat Query Store de gegevens vastleggen die voor u belangrijk zijn. 

|**pg_qs.query_capture_mode** | **Scenario**|
|---|---|
|_Alles_  |Analyseer uw workload grondig in termen van alle query's en de uitvoeringsfrequenties en andere statistieken. Identificeer nieuwe query's in uw workload. Detecteren of ad-hocquery's worden gebruikt om mogelijkheden voor gebruikers- of automatische parameterisering te identificeren. _Alle_ worden geleverd met hogere kosten voor resourceverbruik. |
|_Boven_  |Richt uw aandacht op de belangrijkste query's: query's die zijn uitgegeven door clients.
|_Geen_ |Als deze is ingesteld op Geen, worden er geen nieuwe query's in Query Store vastleggen. U hebt al een queryset en tijdvenster vastgelegd die u wilt onderzoeken en u wilt de afleidingen elimineren die andere query's kunnen introduceren. _Geen_ is geschikt voor test- en bankmarkeringsomgevingen. _Wees_ voorzichtig met het gebruik van geen enkele query, omdat u mogelijk de kans mist om belangrijke nieuwe query's bij te houden en te optimaliseren. |


> [!NOTE] 
> **pg_qs.query_capture_mode** wordt vervangen **door pgms_wait_sampling.query_capture_mode.** Als pg_qs.query_capture_mode geen _is,_ heeft de instelling pgms_wait_sampling.query_capture_mode geen effect. 


## <a name="keep-the-data-you-need"></a>Bewaar de gegevens die u nodig hebt
De **parameter pg_qs.retention_period_in_days** specificeert in dagen de gegevensretentieperiode voor Query Store. Oudere query- en statistiekengegevens worden verwijderd. Query Store is standaard geconfigureerd om de gegevens 7 dagen te bewaren. Vermijd het bewaren van historische gegevens die u niet wilt gebruiken. Verhoog de waarde als u gegevens langer wilt bewaren.


## <a name="next-steps"></a>Volgende stappen
- Meer informatie over het op halen of instellen van parameters met de [Azure Portal](howto-configure-server-parameters-using-portal.md) of [de Azure CLI.](howto-configure-server-parameters-using-cli.md)
