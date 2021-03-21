---
title: Upgrade uitvoeren naar de nieuwste generatie toegewezen SQL-groep (voorheen SQL DW)
description: Voer een upgrade uit voor Azure Synapse Analytics exclusieve SQL-groep (voorheen SQL DW) naar de nieuwste generatie van de Azure-hardware-en opslag architectuur.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 02/19/2019
ms.author: martinle
ms.reviewer: jrasnick
ms.custom: seo-lt-2019
ms.openlocfilehash: b5a9d1781bd0498ac6ad74439b1572c52e3c345a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96459267"
---
# <a name="optimize-performance-by-upgrading-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>Optimaliseer de prestaties door de exclusieve SQL-groep (voorheen SQL DW) in azure Synapse Analytics te upgraden

Voer een upgrade uit van uw toegewezen SQL-groep (voorheen SQL DW) naar de nieuwste generatie van de Azure-hardware-en opslag architectuur.

## <a name="why-upgrade"></a>Waarom bijwerken?

U kunt nu probleemloos upgraden naar de toegewezen Gen2-laag van de exclusieve SQL-groep (voorheen SQL DW) in de Azure Portal voor [ondersteunde regio's](gen2-migration-schedule.md#automated-schedule-and-region-availability-table). Als uw regio zelf een upgrade niet ondersteunt, kunt u een upgrade uitvoeren naar een ondersteunde regio of wachten tot de Self-upgrade beschikbaar is in uw regio. Voer nu een upgrade uit om te profiteren van de nieuwste generatie van Azure-hardware en een verbeterde opslag architectuur, inclusief snellere prestaties, hogere schaal baarheid en onbeperkte kolom opslag.

> [!VIDEO https://www.youtube.com/embed/9B2F0gLoyss]

> [!IMPORTANT]
> Deze upgrade is van toepassing op met reken kracht geoptimaliseerde SQL-groepen in de gen1-laag (fornmerly SQL DW) in [ondersteunde regio's](gen2-migration-schedule.md#automated-schedule-and-region-availability-table).

## <a name="before-you-begin"></a>Voordat u begint

1. Controleer of uw [regio](gen2-migration-schedule.md#automated-schedule-and-region-availability-table) wordt ondersteund voor de migratie van GEN1 naar GEN2. Let op de automatische migratie datums. Om conflicten met het geautomatiseerde proces te voor komen, moet u uw hand matige migratie plannen vóór de begin datum van het geautomatiseerde proces.
2. Als u zich in een regio bevindt die nog niet wordt ondersteund, gaat u door met het herstellen naar een ondersteunde regio om te controleren of de regio is toegevoegd of [bijgewerkt](#upgrade-from-an-azure-geographical-region-using-restore-through-the-azure-portal) .
3. Als uw regio wordt ondersteund, moet u [een upgrade uitvoeren via de Azure Portal](#upgrade-in-a-supported-region-using-the-azure-portal)
4. **Selecteer het voorgestelde prestatie niveau** voor de toegewezen SQL-groep (voorheen SQL DW) op basis van uw huidige prestatie niveau voor de gen1-laag met behulp van de onderstaande toewijzing:

   | Gen1-laag met geoptimaliseerde reken kracht | Gen2-laag met geoptimaliseerde reken kracht |
   | :-------------------------: | :-------------------------: |
   |            DW100            |           DW100c            |
   |            DW200            |           DW200c            |
   |            DW300            |           DW300c            |
   |            DW400            |           DW400c            |
   |            DW500            |           DW500c            |
   |            DW600            |           DW500c            |
   |           DW1000            |           DW1000c           |
   |           DW1200            |           DW1000c           |
   |           DW1500            |           DW1500c           |
   |           DW2000            |           DW2000c           |
   |           DW3000            |           DW3000c           |
   |           DW6000            |           DW6000c           |

> [!NOTE]
> Voorgestelde prestatie niveaus zijn geen directe conversie. U kunt bijvoorbeeld het beste van DW600 naar DW500c.

## <a name="upgrade-in-a-supported-region-using-the-azure-portal"></a>Een upgrade uitvoeren in een ondersteunde regio met behulp van de Azure Portal

- Migratie van gen1 naar Gen2 via de Azure Portal is permanent. Er is geen proces voor het retour neren van gen1.
- Er moet een toegewezen SQL-groep (voorheen SQL DW) worden uitgevoerd om naar Gen2 te migreren

### <a name="before-you-begin"></a>Voordat u begint

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

- Meld u aan bij [Azure Portal](https://portal.azure.com/).
- Zorg ervoor dat er een toegewezen SQL-groep (voorheen SQL DW) wordt uitgevoerd. deze moet worden gemigreerd naar Gen2

### <a name="powershell-upgrade-commands"></a>Power shell-upgrade opdrachten

1. Als de geGen1eerde toegewezen SQL-groep met de reken fase (voorheen SQL DW) die moet worden bijgewerkt, is onderbroken, hervat u de [toegewezen SQL-groep (voorheen SQL DW)](pause-and-resume-compute-portal.md).

2. Voor bereidingen voor een paar minuten uitval tijd.

3. Identificeer code verwijzingen voor het berekenen van geoptimaliseerde gen1-prestatie niveaus en wijzig ze in hun equivalente, geoptimaliseerde Gen2-prestatie niveau. Hieronder vindt u twee voor beelden van waar u code verwijzingen moet bijwerken voordat u een upgrade uitvoert:

   Oorspronkelijke gen1 Power shell-opdracht:

   ```powershell
   Set-AzSqlDatabase -ResourceGroupName "myResourceGroup" -DatabaseName "mySampleDataWarehouse" -ServerName "mynewserver-20171113" -RequestedServiceObjectiveName "DW300"
   ```

   Gewijzigd in:

   ```powershell
   Set-AzSqlDatabase -ResourceGroupName "myResourceGroup" -DatabaseName "mySampleDataWarehouse" -ServerName "mynewserver-20171113" -RequestedServiceObjectiveName "DW300c"
   ```

   > [!NOTE]
   > -RequestedServiceObjectiveName "DW300" is gewijzigd in-RequestedServiceObjectiveName "DW300 **c**"
   >

   Oorspronkelijke gen1 T-SQL-opdracht:

   ```SQL
   ALTER DATABASE mySampleDataWarehouse MODIFY (SERVICE_OBJECTIVE = 'DW300') ;
   ```

   Gewijzigd in:

   ```sql
   ALTER DATABASE mySampleDataWarehouse MODIFY (SERVICE_OBJECTIVE = 'DW300c') ;
   ```

   > [!NOTE]
   > SERVICE_OBJECTIVE = ' DW300 ' is gewijzigd in SERVICE_OBJECTIVE = ' DW300 **c**'

## <a name="start-the-upgrade"></a>De upgrade starten

1. Ga naar de berekenings geoptimaliseerde toegewezen gen1 SQL-groep (voorheen SQL DW) in de Azure Portal. Als de geGen1eerde toegewezen SQL-groep met de berekenings niveau (voorheen SQL DW) die moet worden bijgewerkt, is onderbroken, hervat u de [toegewezen SQL-groep](pause-and-resume-compute-portal.md).
2. Selecteer **upgrade naar Gen2** -kaart op het tabblad taken: ![ Upgrade_1](./media/upgrade-to-latest-generation/upgrade-to-gen2-1.png)

   > [!NOTE]
   > Als u de kaart **upgrade naar Gen2** niet op het tabblad taken ziet, is uw abonnements type beperkt in de huidige regio.
   > [Dien een ondersteunings ticket](sql-data-warehouse-get-started-create-support-ticket.md) in om uw abonnement goed te keuren.

3. Zorg ervoor dat uw werk belasting is voltooid en stilgelegd voordat u de upgrade uitvoert. U krijgt een paar minuten de tijd om uw toegewezen SQL-groep (voorheen SQL DW) weer online te zetten als een berekenings geoptimaliseerde, toegewezen SQL-pool met Gen2-laag (voorheen SQL DW). **Selecteer een upgrade**:

   ![Upgrade_2](./media/upgrade-to-latest-generation/upgrade-to-gen2-2.png)

4. **Controleer de upgrade** door de status te controleren in de Azure portal:

   ![Upgrade3](./media/upgrade-to-latest-generation/upgrade-to-gen2-3.png)

   De eerste stap van het upgrade proces loopt door de schaal bewerking (' upgrade-offline '), waarbij alle sessies worden afgebroken en verbindingen worden verwijderd.

   De tweede stap van het upgrade proces is gegevens migratie ("upgrade-online"). Gegevens migratie is een online achtergrond proces voor trickle. Dit proces verplaatst gegevens in kolommen met een lokale SSD-cache langzaam van de oude opslag architectuur naar de nieuwe opslag architectuur. Gedurende deze periode is uw toegewezen SQL-groep (voorheen SQL DW) online voor het uitvoeren van query's en laden. Uw gegevens zijn beschikbaar voor query's, ongeacht of deze zijn gemigreerd of niet. De gegevens migratie gebeurt op verschillende tarieven, afhankelijk van de grootte van uw gegevens, het prestatie niveau en het aantal column Store-segmenten.

5. **Optionele aanbeveling:** Zodra de schaal bewerking is voltooid, kunt u het achtergrond proces voor gegevens migratie versnellen. U kunt het verplaatsen van gegevens afdwingen door [ALTER index Rebuild](sql-data-warehouse-tables-index.md) uit te voeren op alle primaire column Store-tabellen die u wilt doorzoeken op een grotere SLO en resource klasse. Deze bewerking is **offline** vergeleken met het trickle-achtergrond proces. Dit kan uren duren, afhankelijk van het aantal en de grootte van uw tabellen. Zodra de gegevens zijn gemigreerd, is het echter veel sneller als gevolg van de nieuwe architectuur voor uitgebreide opslag met Rijg roepen van hoge kwaliteit.

> [!NOTE]
> Alter index Rebuild is een offline bewerking en de tabellen zijn pas beschikbaar als het opnieuw opbouwen is voltooid.

Met de volgende query worden de vereiste opdrachten Alter index Rebuild voor het versnellen van de gegevens migratie gegenereerd:

```sql
SELECT 'ALTER INDEX [' + idx.NAME + '] ON ['
       + Schema_name(tbl.schema_id) + '].['
       + Object_name(idx.object_id) + '] REBUILD ' + ( CASE
                                                         WHEN (
                                                     (SELECT Count(*)
                                                      FROM   sys.partitions
                                                             part2
                                                      WHERE  part2.index_id
                                                             = idx.index_id
                                                             AND
                                                     idx.object_id =
                                                     part2.object_id)
                                                     > 1 ) THEN
              ' PARTITION = '
              + Cast(part.partition_number AS NVARCHAR(256))
              ELSE ''
                                                       END ) + '; SELECT ''[' +
              idx.NAME + '] ON [' + Schema_name(tbl.schema_id) + '].[' +
              Object_name(idx.object_id) + '] ' + (
              CASE
                WHEN ( (SELECT Count(*)
                        FROM   sys.partitions
                               part2
                        WHERE
                     part2.index_id =
                     idx.index_id
                     AND idx.object_id
                         = part2.object_id) > 1 ) THEN
              ' PARTITION = '
              + Cast(part.partition_number AS NVARCHAR(256))
              + ' completed'';'
              ELSE ' completed'';'
                                                    END )
FROM   sys.indexes idx
       INNER JOIN sys.tables tbl
               ON idx.object_id = tbl.object_id
       LEFT OUTER JOIN sys.partitions part
                    ON idx.index_id = part.index_id
                       AND idx.object_id = part.object_id
WHERE  idx.type_desc = 'CLUSTERED COLUMNSTORE';
```

## <a name="upgrade-from-an-azure-geographical-region-using-restore-through-the-azure-portal"></a>Voer een upgrade uit van een geografische regio in azure met behulp van Restore via de Azure Portal

## <a name="create-a-user-defined-restore-point-using-the-azure-portal"></a>Een door de gebruiker gedefinieerd herstel punt maken met behulp van de Azure Portal

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).

2. Navigeer naar de toegewezen SQL-groep (voorheen SQL DW) waarvoor u een herstel punt wilt maken.

3. Klik boven aan de sectie Overzicht en selecteer **+ Nieuw herstel punt**.

    ![Nieuw herstel punt](./media/upgrade-to-latest-generation/creating_restore_point_0.png)

4. Geef een naam op voor het herstel punt.

    ![Naam van herstel punt](./media/upgrade-to-latest-generation/creating_restore_point_1.png)

## <a name="restore-an-active-or-paused-database-using-the-azure-portal"></a>Een actieve of onderbroken data base herstellen met behulp van de Azure Portal

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
2. Navigeer naar de toegewezen SQL-groep (voorheen SQL DW) waarvan u wilt herstellen.
3. Klik boven aan de sectie Overzicht op **herstellen**.

    ![ Overzicht van Herstellen](./media/upgrade-to-latest-generation/restoring_0.png)

4. Selecteer **automatische herstel punten** of door de **gebruiker gedefinieerde herstel punten**. Selecteer voor door de gebruiker gedefinieerde herstel punten **een herstel punt dat door de gebruiker is gedefinieerd** of **Maak een nieuw, door de gebruiker gedefinieerd herstel punt**. Voor de-server selecteert u **nieuwe maken** en kiest u een server in een geografische regio met Gen2-ondersteuning.

    ![Automatische herstelpunten](./media/upgrade-to-latest-generation/restoring_1.png)

## <a name="restore-from-an-azure-geographical-region-using-powershell"></a>Herstellen vanuit een geografische regio in azure met behulp van Power shell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Als u een Data Base wilt herstellen, gebruikt u de cmdlet [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) .

> [!NOTE]
> U kunt een geo-herstel bewerking uitvoeren op Gen2. Als u dit wilt doen, geeft u een Gen2 ServiceObjectiveName op (bijvoorbeeld DW1000 **c**) als een optionele para meter.

1. Open Windows PowerShell.
2. Maak verbinding met uw Azure-account en vermeld alle abonnementen die aan uw account zijn gekoppeld.
3. Selecteer het abonnement dat de Data Base bevat die u wilt herstellen.
4. Haal de Data Base op die u wilt herstellen.
5. Maak de herstel aanvraag voor de data base, waarbij u een Gen2 ServiceObjectiveName opgeeft.
6. Controleer de status van de geo-herstelde data base.

```Powershell
Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName "<Subscription_name>"

# Get the database you want to recover
$GeoBackup = Get-AzSqlDatabaseGeoBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourServerName>" -DatabaseName "<YourDatabaseName>"

# Recover database
$GeoRestoredDatabase = Restore-AzSqlDatabase –FromGeoBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourTargetServer>" -TargetDatabaseName "<NewDatabaseName>" –ResourceId $GeoBackup.ResourceID -ServiceObjectiveName "<YourTargetServiceLevel>" -RequestedServiceObjectiveName "DW300c"

# Verify that the geo-restored database is online
$GeoRestoredDatabase.status
```

> [!NOTE]
> Als u de Data Base wilt configureren nadat de herstel bewerking is voltooid, raadpleegt u [de data base configureren na herstel](../../azure-sql/database/disaster-recovery-guidance.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#configure-your-database-after-recovery).

De herstelde data base wordt TDE ingeschakeld als de bron database TDE is ingeschakeld.

Als u problemen ondervindt met uw toegewezen SQL-groep, maakt u een [ondersteunings aanvraag](sql-data-warehouse-get-started-create-support-ticket.md) en verwijst u naar "Gen2 upgrade" als de mogelijke oorzaak.

## <a name="next-steps"></a>Volgende stappen

Uw bijgewerkte exclusieve SQL-groep (voorheen SQL DW) is online. Zie [resource klassen voor workload Management](resource-classes-for-workload-management.md)om te profiteren van de verbeterde architectuur.
