---
title: XTP in-Memory-opslag controleren
description: XTP in-Memory gebruiken om te schatten en te controleren, capaciteit; capaciteits fout 41823 oplossen
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: how-to
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: sstein
ms.date: 01/25/2019
ms.openlocfilehash: 5717c2479c1d894117bae44826a814c3cfeaa98a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96493386"
---
# <a name="monitor-in-memory-oltp-storage-in-azure-sql-database-and-azure-sql-managed-instance"></a>In-Memory OLTP-opslag bewaken in Azure SQL Database en Azure SQL Managed instance
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

Bij gebruik [van in-Memory OLTP](in-memory-oltp-overview.md)worden gegevens in tabellen die zijn geoptimaliseerd voor geheugen en tabel variabelen bevinden zich in In-Memory OLTP-opslag.

## <a name="determine-whether-data-fits-within-the-in-memory-oltp-storage-cap"></a>Bepalen of gegevens binnen de In-Memory OLTP-opslag limiet passen

Bepaal de opslag limieten van de verschillende service lagen. Elke Premium-en Bedrijfskritiek-servicelaag heeft een maximale In-Memory OLTP-opslag grootte.

- [Op DTU gebaseerde resource limieten-één data base](database/resource-limits-dtu-single-databases.md)
- [Op DTU gebaseerde resource limieten-elastische Pools](database/resource-limits-dtu-elastic-pools.md)
- [resource limieten op basis van vCore-afzonderlijke data bases](database/resource-limits-vcore-single-databases.md)
- [vCore-gebaseerde resource limieten: elastische Pools](database/resource-limits-vcore-elastic-pools.md)
- [op vCore gebaseerde resource limieten-beheerd exemplaar](managed-instance/resource-limits.md)

Het schatten van de geheugen vereisten voor een tabel die is geoptimaliseerd voor geheugen werkt op dezelfde SQL Server manier als in Azure SQL Database en Azure SQL Managed instance. Neem even de tijd om de [schatting van de geheugen vereisten](/sql/relational-databases/in-memory-oltp/estimate-memory-requirements-for-memory-optimized-tables)te bekijken.

Tabel-en tabel variabele rijen, evenals indexen, tellen bij de maximale grootte van de gebruikers gegevens. Daarnaast heeft ALTER TABLE voldoende ruimte nodig om een nieuwe versie van de volledige tabel en de bijbehorende indexen te maken.

Zodra deze limiet is overschreden, kunnen INSERT-en update-bewerkingen mislukken met fout 41823 voor afzonderlijke data bases in Azure SQL Database en data bases in Azure SQL Managed instance en fout 41840 voor elastische Pools in Azure SQL Database. Op dat moment moet u gegevens verwijderen om geheugen vrij te maken of de servicelaag of reken grootte van uw data base upgraden.

## <a name="monitoring-and-alerting"></a>Bewaking en waarschuwingen

U kunt in-Memory opslag gebruiken als percentage van de opslag limiet voor de berekenings grootte in de [Azure Portal](https://portal.azure.com/):

1. Ga op de Blade Data Base naar het vak resource gebruik en klik op bewerken.
2. Selecteer de metrische gegevens `In-Memory OLTP Storage percentage` .
3. Als u een waarschuwing wilt toevoegen, klikt u op het vak resource gebruik om de Blade metrische gegevens te openen en klikt u vervolgens op waarschuwing toevoegen.

U kunt ook de volgende query gebruiken om het gebruik van de opslag in het geheugen weer te geven:

```sql
    SELECT xtp_storage_percent FROM sys.dm_db_resource_stats
```

## <a name="correct-out-of-in-memory-oltp-storage-situations---errors-41823-and-41840"></a>Problemen met OLTP-opslag in het geheugen corrigeren-fouten 41823 en 41840

Zoek de In-Memory OLTP-opslag limiet in uw data base resulteert in INSERT-, UPDATE-, ALTER-en CREATE-bewerkingen die mislukken met het fout bericht 41823 (voor afzonderlijke data bases) of fout 41840 (voor elastische Pools). Beide fouten zorgen ervoor dat de actieve trans actie wordt afgebroken.

Fout berichten 41823 en 41840 geven aan dat de door het geheugen geoptimaliseerde tabellen en tabel variabelen in de data base of pool de maximale In-Memory OLTP-opslag grootte hebben bereikt.

Om deze fout op te lossen, kunt u het volgende doen:

- Verwijder gegevens uit de tabellen die zijn geoptimaliseerd voor geheugen, waardoor het mogelijk is om de gegevens te offloaden naar traditionele, op schijven gebaseerde tabellen. of
- Voer een upgrade uit van de servicelaag met voldoende in-Memory opslag voor de gegevens die u nodig hebt om in geheugen geoptimaliseerde tabellen te bewaren.

> [!NOTE]
> In zeldzame gevallen kunnen fouten 41823 en 41840 tijdelijk zijn, wat betekent dat er voldoende beschik bare In-Memory OLTP-opslag is en dat de bewerking opnieuw wordt uitgevoerd. Daarom wordt u aangeraden de totale beschik bare In-Memory OLTP-opslag te controleren en opnieuw te proberen wanneer de fout 41823 of 41840 voor het eerst optreedt. Zie [conflict detectie en pogings logica met In-Memory OLTP](/sql/relational-databases/In-memory-oltp/transactions-with-memory-optimized-tables#conflict-detection-and-retry-logic)voor meer informatie over de logica voor opnieuw proberen.

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over het bewaken van [dynamische beheer weergaven](database/monitoring-with-dmvs.md).