---
title: Een database herstellen vanuit een back-up
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Meer informatie over herstel naar een bepaald tijdstip, waarmee u een database in Azure SQL Database of een exemplaar in Azure SQL Managed Instance maximaal 35 dagen kunt terugdraaien.
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, sstein, danil
ms.date: 11/13/2020
ms.openlocfilehash: 670176d7478ddab3d17e15526df512dfa7e99fd4
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762061"
---
# <a name="recover-using-automated-database-backups---azure-sql-database--sql-managed-instance"></a>Herstellen met behulp van automatische databaseback-ups - Azure SQL Database & SQL Managed Instance
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

De volgende opties zijn beschikbaar voor databaseherstel met behulp van automatische [databaseback-ups.](automated-backups-overview.md) U kunt:

- Maak een nieuwe database op dezelfde server, hersteld naar een opgegeven tijdstip binnen de bewaarperiode.
- Maak een database op dezelfde server, hersteld naar de verwijderingstijd voor een verwijderde database.
- Maak een nieuwe database op een server in dezelfde regio, hersteld naar het punt van de meest recente back-ups.
- Maak een nieuwe database op een server in een andere regio, hersteld naar het punt van de meest recente gerepliceerde back-ups.

Als u langetermijnretentie van [back-ups hebt](long-term-retention-overview.md)geconfigureerd, kunt u ook een nieuwe database maken op basis van een back-up met langetermijnretentie op elke server.

> [!IMPORTANT]
> U kunt een bestaande database niet overschrijven tijdens het herstellen.

Wanneer u de Standard- of Premium-servicelaag gebruikt, worden er mogelijk extra opslagkosten in de database hersteld. De extra kosten worden gemaakt wanneer de maximale grootte van de herstelde database groter is dan de hoeveelheid opslag die is opgenomen in de servicelaag en het prestatieniveau van de doeldatabase. Zie de pagina met prijzen voor SQL Database prijsinformatie voor [meer informatie over extra opslag.](https://azure.microsoft.com/pricing/details/sql-database/) Als de werkelijke hoeveelheid gebruikte ruimte kleiner is dan de hoeveelheid opslagruimte die is inbegrepen, kunt u deze extra kosten voorkomen door de maximale databasegrootte in te stellen op de inbegrepen hoeveelheid.

## <a name="recovery-time"></a>Hersteltijd

De hersteltijd voor het herstellen van een database met behulp van automatische databaseback-ups wordt beïnvloed door verschillende factoren:

- De grootte van de database.
- De rekenkracht van de database.
- Het aantal betrokken transactielogboeken.
- De hoeveelheid activiteit die opnieuw moet worden afgespeeld om te herstellen naar het herstelpunt.
- De netwerkbandbreedte als de herstel naar een andere regio is.
- Het aantal gelijktijdige herstelaanvragen dat wordt verwerkt in de doelregio.

Voor een grote of zeer actieve database kan het herstellen enkele uren duren. Als er een langdurige storing in een regio optreedt, kan het gebeuren dat een groot aantal aanvragen voor geo-herstel wordt geïnitieerd voor herstel na een noodgeval. Wanneer er veel aanvragen zijn, kan de hersteltijd voor individuele databases toenemen. De meeste databaseherstelingen zijn in minder dan 12 uur klaar.

Voor één abonnement gelden beperkingen voor het aantal gelijktijdige herstelaanvragen. Deze beperkingen zijn van toepassing op elke combinatie van herstelbewerkingen naar een bepaald tijdstip, geo-herstelbewerkingen, en herstelbewerkingen vanaf back-ups met een langetermijnbewaarperiode.

| **Implementatieoptie** | **Maximumaantal gelijktijdige aanvragen dat wordt verwerkt** | **Maximumaantal gelijktijdige aanvragen dat wordt verzonden** |
| :--- | --: | --: |
|**Individuele database (per abonnement)**|30|100|
|**Elastische pool (per groep)**|4|2000|


Er is geen ingebouwde methode om de hele server te herstellen. Zie voor een voorbeeld van hoe u deze taak kunt [uitvoeren Azure SQL Database: Volledig serverherstel.](https://gallery.technet.microsoft.com/Azure-SQL-Database-Full-82941666)

> [!IMPORTANT]
> Als u wilt herstellen met behulp van automatische back-ups, moet u lid zijn van de rol SQL Server Inzender of de rol SQL Managed Instance Inzender (afhankelijk van de herstelbestemming) in het abonnement, of moet u de eigenaar van het abonnement zijn. Zie Azure [RBAC: Ingebouwde rollen voor meer informatie.](../../role-based-access-control/built-in-roles.md) U kunt herstellen met behulp van de Azure Portal, PowerShell of de REST API. U kunt Transact-SQL niet gebruiken.

## <a name="point-in-time-restore"></a>Terugzetten naar eerder tijdstip

U kunt een zelfstandige, pool- of exemplaardatabase herstellen naar een eerder tijdstip met behulp van de Azure Portal, [PowerShell](/powershell/module/az.sql/restore-azsqldatabase)of [de REST API](/rest/api/sql/databases/createorupdate#creates-a-database-from-pointintimerestore.). De aanvraag kan elke servicelaag of rekenkracht voor de herstelde database opgeven. Zorg ervoor dat u voldoende resources hebt op de server waarop u de database wilt herstellen. 

Wanneer het herstellen is voltooid, wordt er een nieuwe database gemaakt op dezelfde server als de oorspronkelijke database. De herstelde database wordt tegen normale tarieven in rekening gebracht op basis van de servicelaag en rekenkracht. Er worden geen kosten in rekening gebracht totdat het herstellen van de database is voltooid.

Over het algemeen herstelt u een database naar een eerder punt voor hersteldoeleinden. U kunt de herstelde database behandelen als vervanging voor de oorspronkelijke database of deze gebruiken als een gegevensbron om de oorspronkelijke database bij te werken.

- **Databasevervanging**

  Als u van plan bent dat de herstelde database een vervanging is voor de oorspronkelijke database, moet u de rekenkracht en servicelaag van de oorspronkelijke database opgeven. U kunt vervolgens de naam van de oorspronkelijke database wijzigen en de herstelde database de oorspronkelijke naam geven met behulp van de [opdracht ALTER DATABASE](/sql/t-sql/statements/alter-database-azure-sql-database) in T-SQL.

- **Gegevensherstel**

  Als u van plan bent om gegevens op te halen uit de herstelde database om te herstellen van een gebruikers- of toepassingsfout, moet u een script voor gegevensherstel schrijven en uitvoeren dat gegevens uit de herstelde database extraheert en van toepassing is op de oorspronkelijke database. Hoewel het lang kan duren voordat de herstelbewerking is voltooid, is de hersteldatabase zichtbaar in de databaselijst tijdens het herstelproces. Als u de database verwijdert tijdens het herstellen, wordt de herstelbewerking geannuleerd en worden er geen kosten in rekening gebracht voor de database die de herstelbewerking niet heeft voltooid.
  
### <a name="point-in-time-restore-by-using-azure-portal"></a>Herstel naar een bepaald tijdstip met behulp van Azure Portal

U kunt één database of exemplaardatabase herstellen naar een bepaald tijdstip vanaf de overzichtsblade van de database die u wilt herstellen in de Azure Portal.

#### <a name="sql-database"></a>SQL Database

Als u een database naar een bepaald tijdstip wilt herstellen met behulp van de Azure Portal, opent u de overzichtspagina van de database en selecteert u **Herstellen** op de werkbalk. Kies de back-upbron en selecteer het tijdstip waarop een back-uppunt wordt gemaakt.

  ![Schermopname van opties voor databaseherstel voor SQL Database.](./media/recovery-using-backups/pitr-backup-sql-database-annotated.png)

#### <a name="sql-managed-instance"></a>SQL Managed Instance

Als u een database van een beheerd exemplaar naar een bepaald tijdstip wilt herstellen met behulp van de Azure Portal, opent u de overzichtspagina van de database en selecteert u **Herstellen op** de werkbalk. Kies het back-uppunt van waaruit een nieuwe database wordt gemaakt.

  ![Schermopname van opties voor databaseherstel voor sql managed instance.](./media/recovery-using-backups/pitr-backup-managed-instance-annotated.png)

> [!TIP]
> Zie Programmatisch herstel met behulp van automatische back-ups als u een database programmatisch wilt herstellen vanuit een [back-up.](recovery-using-backups.md)

## <a name="deleted-database-restore"></a>Databaseherstel verwijderd

U kunt een verwijderde database herstellen naar de verwijderingstijd of een eerder tijdstip op dezelfde server of hetzelfde beheerde exemplaar. U kunt dit doen via Azure Portal, [PowerShell](/powershell/module/az.sql/restore-azsqldatabase)of de [REST (createMode=Restore).](/rest/api/sql/databases/createorupdate) U herstelt een verwijderde database door een nieuwe database te maken vanuit de back-up.

> [!IMPORTANT]
> Als u een server of beheerd exemplaar verwijdert, worden alle databases ook verwijderd en kunnen ze niet worden hersteld. U kunt een verwijderde server of een beheerd exemplaar niet herstellen.

### <a name="deleted-database-restore-by-using-the-azure-portal"></a>Databaseherstel verwijderd met behulp van de Azure Portal

U herstelt verwijderde databases uit de Azure Portal van de server of de resource van het beheerde exemplaar.

> [!TIP]
> Het kan enkele minuten duren voordat onlangs verwijderde databases worden weergegeven op de pagina Verwijderde **databases** in Azure Portal of wanneer verwijderde databases [programmatisch worden weergegeven.](#programmatic-recovery-using-automated-backups)

#### <a name="sql-database"></a>SQL Database

Als u een verwijderde database wilt herstellen naar de verwijderingstijd met behulp van de Azure Portal, opent u de overzichtspagina van de server en **selecteert u Verwijderde databases.** Selecteer een verwijderde database die u wilt herstellen en typ de naam voor de nieuwe database die wordt gemaakt met gegevens die zijn hersteld vanuit de back-up.

  ![Schermopname van het herstellen van een verwijderde database](./media/recovery-using-backups/restore-deleted-sql-database-annotated.png)

#### <a name="sql-managed-instance"></a>SQL Managed Instance

Als u een beheerde database wilt herstellen met behulp van Azure Portal, opent u de overzichtspagina van het beheerde exemplaar en **selecteert u Verwijderde databases.** Selecteer een verwijderde database die u wilt herstellen en typ de naam voor de nieuwe database die wordt gemaakt met gegevens die zijn hersteld vanuit de back-up.

  ![Schermopname van het herstellen van een verwijderde Azure SQL Managed Instance database](./media/recovery-using-backups/restore-deleted-sql-managed-instance-annotated.png)

### <a name="deleted-database-restore-by-using-powershell"></a>Databaseherstel verwijderd met behulp van PowerShell

Gebruik de volgende voorbeeldscripts om een verwijderde database te herstellen voor SQL Database of SQL Managed Instance met behulp van PowerShell.

#### <a name="sql-database"></a>SQL Database

Zie Restore a database using PowerShell (Een database herstellen met PowerShell) voor een powershell-voorbeeldscript dat laat zien hoe u een verwijderde database in Azure SQL Database [herstelt.](scripts/restore-database-powershell.md)

#### <a name="sql-managed-instance"></a>SQL Managed Instance

Zie Restore [deleted instance database using PowerShell](../managed-instance/point-in-time-restore.md#restore-a-deleted-database) (Database van verwijderd exemplaar herstellen met PowerShell) voor een Voorbeeld van een PowerShell-script dat laat zien hoe u een database met een verwijderd exemplaar kunt herstellen

> [!TIP]
> Zie Programmatisch herstel uitvoeren met behulp van automatische back-ups als u een verwijderde database programmatisch [wilt herstellen.](recovery-using-backups.md)

## <a name="geo-restore"></a>Geo-herstel

> [!IMPORTANT]
> Geo-herstel is alleen beschikbaar voor SQL-databases of beheerde exemplaren die zijn geconfigureerd met geografisch redundante [back-upopslag.](automated-backups-overview.md#backup-storage-redundancy)

U kunt een database herstellen op een SQL Database-server of een exemplaardatabase op elk beheerd exemplaar in elke Azure-regio vanuit de meest recente geo-gerepliceerde back-ups. Geo-herstel maakt gebruik van een geo-gerepliceerde back-up als bron. U kunt geo-herstel aanvragen, zelfs als de database of het datacenter niet toegankelijk is vanwege een storing.

Geo-herstel is de standaardhersteloptie wanneer uw database niet beschikbaar is vanwege een incident in de hostingregio. U kunt de database herstellen naar een server in elke andere regio. Er is een vertraging tussen het maken van een back-up en het geo-replicatie naar een Azure-blob in een andere regio. Als gevolg hiervan kan de herstelde database maximaal één uur achterblijven op de oorspronkelijke database. In de volgende afbeelding ziet u een databaseherstel vanaf de laatst beschikbare back-up in een andere regio.

![Afbeelding van geo-herstel](./media/recovery-using-backups/geo-restore-2.png)

### <a name="geo-restore-by-using-the-azure-portal"></a>Geo-herstel met behulp van de Azure Portal

Vanuit de Azure Portal maakt u een nieuwe database met één of beheerd exemplaar en selecteert u een beschikbare back-up voor geo-herstel. De zojuist gemaakte database bevat de geo-herstelde back-upgegevens.

#### <a name="sql-database"></a>SQL Database

Als u een individuele database geo-herstelt vanuit de Azure Portal in de regio en server van uw keuze, volgt u deze stappen:

1. Selecteer **in Dashboard** de optie **Maken**  >  **SQL Database.** Voer op **het** tabblad Basisinformatie de vereiste gegevens in.
2. Selecteer **Aanvullende instellingen.**
3. Bij **Bestaande gegevens gebruiken selecteert** u **Back-up**.
4. Selecteer **voor Back-up** een back-up in de lijst met beschikbare back-ups voor geo-herstel.

    ![Schermopname van opties SQL Database maken](./media/recovery-using-backups/geo-restore-azure-sql-database-list-annotated.png)

Voltooi het proces voor het maken van een nieuwe database vanuit de back-up. Wanneer u een database in Azure SQL Database, bevat deze de herstelde back-up voor geo-herstel.

#### <a name="sql-managed-instance"></a>SQL Managed Instance

Als u een database van een beheerd exemplaar wilt herstellen van de Azure Portal naar een bestaand beheerd exemplaar in een regio naar keuze, selecteert u een beheerd exemplaar waarop u een database wilt herstellen. Volg deze stappen:

1. Selecteer **Nieuwe database.**
2. Typ een gewenste databasenaam.
3. Selecteer **back-up onder Bestaande gegevens** **gebruiken.**
4. Selecteer een back-up in de lijst met beschikbare back-ups voor geo-herstel.

    ![Schermopname van nieuwe databaseopties](./media/recovery-using-backups/geo-restore-sql-managed-instance-list-annotated.png)

Voltooi het proces voor het maken van een nieuwe database. Wanneer u de exemplaardatabase maakt, bevat deze de herstelde geo-herstelback-up.

### <a name="geo-restore-by-using-powershell"></a>Geo-herstel met behulp van PowerShell

#### <a name="sql-database"></a>SQL Database

Zie PowerShell gebruiken om een individuele database te herstellen naar een eerder tijdstip voor een [PowerShell-script](scripts/restore-database-powershell.md)dat laat zien hoe u geo-herstel voor één database kunt uitvoeren.

#### <a name="sql-managed-instance"></a>SQL Managed Instance

Zie PowerShell gebruiken om een database van een beheerd exemplaar te herstellen naar een andere geo-regio voor een PowerShell-script dat laat zien hoe u geo-herstel kunt uitvoeren voor een [beheerde exemplaardatabase.](../managed-instance/scripts/restore-geo-backup.md)

### <a name="geo-restore-considerations"></a>Overwegingen voor geo-herstel

U kunt geen herstel naar een bepaald tijdstip uitvoeren op een geo-secundaire database. U kunt dit alleen doen op een primaire database. Zie Herstellen na een storing voor gedetailleerde informatie over het gebruik van geo-herstel om te herstellen na [een storing.](../../key-vault/general/disaster-recovery-guidance.md)

> [!IMPORTANT]
> Geo-herstel is de meest eenvoudige oplossing voor herstel na noodherstel die beschikbaar is in SQL Database en SQL Managed Instance. Het is afhankelijk van automatisch gemaakte geo-gerepliceerde back-ups met een RPO (Recovery Point Objective) van maximaal 1 uur en een geschatte hersteltijd van maximaal 12 uur. Het biedt geen garantie dat de doelregio de capaciteit heeft om uw databases te herstellen na een regionale storing, omdat de vraag waarschijnlijk sterk toeneemt. Als uw toepassing relatief kleine databases gebruikt en niet essentieel is voor het bedrijf, is geo-herstel een geschikte oplossing voor herstel na noodherstel. 
>
> Gebruik automatische [failovergroepen](auto-failover-group-overview.md)voor bedrijfskritieke toepassingen waarvoor grote databases zijn vereist en bedrijfscontinuïteit moet garanderen. Het biedt een veel lagere RPO en hersteltijddoelstelling en de capaciteit wordt altijd gegarandeerd. 
>
> Zie Overzicht van bedrijfscontinuïteit voor meer informatie over keuzes [voor bedrijfscontinuïteit.](business-continuity-high-availability-disaster-recover-hadr-overview.md)

## <a name="programmatic-recovery-using-automated-backups"></a>Programmatisch herstel met behulp van automatische back-ups

U kunt ook Azure PowerShell of de REST API voor herstel. In de volgende tabellen wordt de set beschikbare opdrachten beschreven.

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> De PowerShell Azure Resource Manager-module wordt nog steeds ondersteund door SQL Database en SQL Managed Instance, maar alle toekomstige ontwikkeling is voor de Az.Sql-module. Zie [AzureRM.Sql](/powershell/module/AzureRM.Sql/) voor deze cmdlets. Argumenten voor de opdrachten in de Az-module en in Azure Resource Manager modules zijn grotendeels identiek.

#### <a name="sql-database"></a>SQL Database

Zie [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase)als u een zelfstandige of pooldatabase wilt herstellen.

  | Cmdlet | Beschrijving |
  | --- | --- |
  | [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) |Hiermee haalt u een of meer databases op. |
  | [Get-AzSqlDeletedDatabaseBackup](/powershell/module/az.sql/get-azsqldeleteddatabasebackup) | Hiermee haalt u een verwijderde database die u kunt herstellen op. |
  | [Get-AzSqlDatabaseGeoBackup](/powershell/module/az.sql/get-azsqldatabasegeobackup) |Hiermee haalt u een geografisch redundante back-up van een database op. |
  | [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase) |Hiermee herstelt u een database. |

  > [!TIP]
  > Zie Restore a database by using PowerShell (Een database herstellen met behulp van [PowerShell)](scripts/restore-database-powershell.md)voor een PowerShell-voorbeeldscript dat laat zien hoe u een herstel naar een bepaald tijdstip van een database kunt uitvoeren.

#### <a name="sql-managed-instance"></a>SQL Managed Instance

Zie [Restore-AzSqlInstanceDatabase](/powershell/module/az.sql/restore-azsqlinstancedatabase)om een beheerde exemplaardatabase te herstellen.

  | Cmdlet | Beschrijving |
  | --- | --- |
  | [Get-AzSqlInstance](/powershell/module/az.sql/get-azsqlinstance) |Haalt een of meer beheerde exemplaren op. |
  | [Get-AzSqlInstanceDatabase](/powershell/module/az.sql/get-azsqlinstancedatabase) | Haalt een exemplaardatabase op. |
  | [Restore-AzSqlInstanceDatabase](/powershell/module/az.sql/restore-azsqlinstancedatabase) |Herstelt een exemplaardatabase. |

### <a name="rest-api"></a>REST-API

Een database herstellen met behulp van de REST API:

| API | Beschrijving |
| --- | --- |
| [REST (createMode=Recovery)](/rest/api/sql/databases) |Hiermee herstelt u een database. |
| [Databasestatus maken of bijwerken](/rest/api/sql/operations) |Retourneert de status tijdens een herstelbewerking. |

### <a name="azure-cli"></a>Azure CLI

#### <a name="sql-database"></a>SQL Database

Zie [az sql db restore](/cli/azure/sql/db#az_sql_db_restore)als u een database wilt herstellen met behulp van de Azure CLI.

#### <a name="sql-managed-instance"></a>SQL Managed Instance

Zie [az sql midb restore](/cli/azure/sql/midb#az_sql_midb_restore)als u een database van een beheerd exemplaar wilt herstellen met behulp van de Azure CLI.

## <a name="summary"></a>Samenvatting

Automatische back-ups beschermen uw databases tegen gebruikers- en toepassingsfouten, onbedoeld verwijderen van databases en langdurige storingen. Deze ingebouwde mogelijkheid is beschikbaar voor alle servicelagen en rekenkracht.

## <a name="next-steps"></a>Volgende stappen

- [Overzicht voor bedrijfscontinuïteit](business-continuity-high-availability-disaster-recover-hadr-overview.md)
- [SQL Database back-ups maken](automated-backups-overview.md)
- [Langetermijnretentie](long-term-retention-overview.md)
- Zie Actieve [geo-replicatie](active-geo-replication-overview.md) of groepen voor automatische failover voor meer informatie over [snellere herstelopties.](auto-failover-group-overview.md)
