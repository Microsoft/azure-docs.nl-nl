---
title: Een Data Base herstellen vanuit een back-up
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Meer informatie over herstel naar een bepaald tijdstip, waarmee u een data base in Azure SQL Database of een exemplaar in Azure SQL Managed Instance kunt terugdraaien tot 35 dagen.
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
ms.openlocfilehash: 138cd03989008aa4fbd7d6ca3e06aff94fa76e33
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94989442"
---
# <a name="recover-using-automated-database-backups---azure-sql-database--sql-managed-instance"></a>Herstellen met behulp van automatische database back-ups-Azure SQL Database & door SQL beheerd exemplaar
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

De volgende opties zijn beschikbaar voor database herstel met behulp van [Automatische database back-ups](automated-backups-overview.md). U kunt:

- Maak een nieuwe Data Base op dezelfde server die binnen de Bewaar periode is hersteld naar een bepaald tijdstip.
- Maak een Data Base op dezelfde server die is hersteld naar de verwijderings tijd voor een verwijderde data base.
- Maak een nieuwe Data Base op een wille keurige server in dezelfde regio, hersteld tot het punt van de meest recente back-ups.
- Maak een nieuwe Data Base op een wille keurige server in een andere regio, hersteld tot het punt van de meest recente gerepliceerde back-ups.

Als u [back-up lange termijn retentie](long-term-retention-overview.md)hebt geconfigureerd, kunt u ook een nieuwe data base maken op basis van een back-up voor lange termijn retentie op elke server.

> [!IMPORTANT]
> U kunt een bestaande data base niet overschrijven tijdens het terugzetten.

Wanneer u de service laag Standard of Premium gebruikt, kan het zijn dat bij het herstellen van de Data Base een extra opslag kosten in rekening worden gebracht. De extra kosten worden in rekening gebracht wanneer de maximale grootte van de herstelde data base groter is dan de hoeveelheid opslag die is opgenomen in de servicelaag en het prestatie niveau van de doel database. Zie de [pagina met prijzen voor SQL database](https://azure.microsoft.com/pricing/details/sql-database/)voor meer informatie over de prijs van extra opslag. Als de daad werkelijke hoeveelheid gebruikte ruimte kleiner is dan de hoeveelheid inbegrepen opslag, kunt u deze extra kosten voor komen door de maximale database grootte in te stellen op de opgenomen hoeveelheid.

## <a name="recovery-time"></a>Herstel tijd

De herstel tijd voor het herstellen van een Data Base met behulp van automatische database back-ups wordt beïnvloed door diverse factoren:

- De grootte van de data base.
- De reken grootte van de data base.
- Het aantal transactie logboeken dat betrokken is.
- De hoeveelheid activiteit die opnieuw moet worden afgespeeld om het herstel punt te herstellen.
- De netwerk bandbreedte als het terugzetten naar een andere regio gaat.
- Het aantal gelijktijdige herstel aanvragen dat wordt verwerkt in de doel regio.

Voor een grote of zeer actieve Data Base kan het herstellen enkele uren duren. Als er een langdurige storing in een regio optreedt, is het mogelijk dat er een groot aantal aanvragen voor geo-herstel worden gestart voor herstel na nood gevallen. Wanneer er veel aanvragen zijn, kan de herstel tijd voor afzonderlijke data bases toenemen. De meeste data bases worden hersteld in minder dan 12 uur.

Voor één abonnement gelden beperkingen voor het aantal gelijktijdige herstel aanvragen. Deze beperkingen gelden voor elke combi natie van tijdgebonden herstel bewerkingen, geo-restores en herstel bewerkingen van back-ups op lange termijn.

| **Implementatie optie** | **Maximum aantal gelijktijdige aanvragen dat wordt verwerkt** | **Maximum aantal gelijktijdige aanvragen dat wordt verzonden** |
| :--- | --: | --: |
|**Eén data base (per abonnement)**|10|60|
|**Elastische pool (per groep)**|4|200|


Er is geen ingebouwde methode om de volledige server te herstellen. Zie [Azure SQL database: volledige server herstel](https://gallery.technet.microsoft.com/Azure-SQL-Database-Full-82941666)voor een voor beeld van hoe u deze taak uitvoert.

> [!IMPORTANT]
> Als u wilt herstellen met behulp van geautomatiseerde back-ups, moet u lid zijn van de gebruikersrol SQL Server Inzender of de rol van SQL Managed instance (afhankelijk van de herstel bestemming) in het abonnement, of moet u de eigenaar van het abonnement zijn. Zie [Azure RBAC: ingebouwde rollen](../../role-based-access-control/built-in-roles.md)voor meer informatie. U kunt herstellen met behulp van de Azure Portal, Power shell of de REST API. U kunt Transact-SQL niet gebruiken.

## <a name="point-in-time-restore"></a>Terugzetten naar eerder tijdstip

U kunt een zelfstandige, gepoolde of exemplaar database herstellen naar een eerder tijdstip met behulp van de Azure Portal, [Power shell](/powershell/module/az.sql/restore-azsqldatabase)of de [rest API](/rest/api/sql/databases/createorupdate#creates-a-database-from-pointintimerestore.). De aanvraag kan een servicelaag of reken grootte voor de herstelde data base opgeven. Zorg ervoor dat u voldoende resources hebt op de server waarop u de Data Base wilt herstellen. 

Wanneer dit is voltooid, wordt door de herstel bewerking een nieuwe data base gemaakt op dezelfde server als de oorspronkelijke data base. De herstelde data base wordt in rekening gebracht tegen normale tarieven, op basis van de servicelaag en de reken grootte. Er worden geen kosten in rekening gebracht totdat het herstellen van de data base is voltooid.

Over het algemeen herstelt u een Data Base naar een eerder tijdstip voor herstel doeleinden. U kunt de herstelde data base behandelen als een vervanging voor de oorspronkelijke data base of als gegevens bron gebruiken om de oorspronkelijke Data Base bij te werken.

- **Database vervanging**

  Als u van plan bent de herstelde data base te vervangen door een vervanging voor de oorspronkelijke Data Base, moet u de reken grootte en de servicelaag van de oorspronkelijke data base opgeven. U kunt de naam van de oorspronkelijke data base wijzigen en de herstelde data base de oorspronkelijke naam geven met behulp van de opdracht [ALTER data base](/sql/t-sql/statements/alter-database-azure-sql-database) in T-SQL.

- **Gegevensherstel**

  Als u van plan bent om gegevens op te halen uit de herstelde data base om te herstellen van een gebruiker of toepassings fout, moet u een gegevens herstel script schrijven en uitvoeren waarmee gegevens uit de herstelde data base worden geëxtraheerd en toegepast op de oorspronkelijke data base. Hoewel het herstellen van de herstel bewerking veel tijd kan duren, is de data base herstellen tijdens het herstel proces zichtbaar in de lijst met data bases. Als u de data base verwijdert tijdens het herstellen, wordt de herstel bewerking geannuleerd en worden er geen kosten in rekening gebracht voor de data base die het herstellen niet heeft voltooid.
  
### <a name="point-in-time-restore-by-using-azure-portal"></a>Herstel naar een bepaald tijdstip met behulp van Azure Portal

U kunt een Data Base met één of een exemplaar herstellen naar een bepaald tijdstip vanuit de Blade overzicht van de data base die u wilt herstellen in de Azure Portal.

#### <a name="sql-database"></a>SQL Database

Als u een Data Base wilt herstellen naar een bepaald tijdstip met behulp van de Azure Portal, opent u de pagina overzicht van de data base en selecteert u **herstellen** op de werk balk. Kies de back-upbron en selecteer het punt-in-time back-uppunt van waaruit een nieuwe Data Base wordt gemaakt.

  ![Scherm opname van de opties voor het herstellen van data bases voor SQL Database.](./media/recovery-using-backups/pitr-backup-sql-database-annotated.png)

#### <a name="sql-managed-instance"></a>SQL Managed Instance

Als u een Data Base van een beheerd exemplaar wilt herstellen naar een bepaald tijdstip met behulp van de Azure Portal, opent u de pagina overzicht van de data base en selecteert u **herstellen** op de werk balk. Kies het punt-in-tijd back-uppunt van waaruit een nieuwe Data Base wordt gemaakt.

  ![Scherm opname van de opties voor het herstellen van data bases voor een SQL Managed instance.](./media/recovery-using-backups/pitr-backup-managed-instance-annotated.png)

> [!TIP]
> Zie [programmatisch herstel met behulp van geautomatiseerde back-ups uitvoeren](recovery-using-backups.md)om een Data Base programmatisch te herstellen met een back-up.

## <a name="deleted-database-restore"></a>Het terugzetten van de data base is verwijderd

U kunt een verwijderde data base herstellen naar de verwijderings tijd of een eerder tijdstip op dezelfde server of hetzelfde beheerde exemplaar. U kunt dit doen via de Azure Portal, [Power shell](/powershell/module/az.sql/restore-azsqldatabase)of de [rest (CreateMode = Restore)](/rest/api/sql/databases/createorupdate). U kunt een verwijderde data base herstellen door een nieuwe Data Base te maken op basis van de back-up.

> [!IMPORTANT]
> Als u een server of een beheerd exemplaar verwijdert, worden alle bijbehorende data bases ook verwijderd en kunnen ze niet worden hersteld. U kunt een verwijderde server of een beheerd exemplaar niet herstellen.

### <a name="deleted-database-restore-by-using-the-azure-portal"></a>Het terugzetten van de data base is verwijderd met behulp van de Azure Portal

U kunt verwijderde data bases herstellen van de Azure Portal van de bron van de server of het beheerde exemplaar.

> [!TIP]
> Het kan enkele minuten duren voordat recent verwijderde data bases worden weer gegeven op de pagina **Verwijderde data bases** in azure Portal, of wanneer verwijderde data bases [programmatisch](#programmatic-recovery-using-automated-backups)worden weer gegeven.

#### <a name="sql-database"></a>SQL Database

Als u een verwijderde data base wilt herstellen naar de verwijderings tijd met behulp van de Azure Portal, opent u de pagina overzicht van de server en selecteert u **Verwijderde data bases**. Selecteer een verwijderde data base die u wilt herstellen en typ de naam voor de nieuwe Data Base die wordt gemaakt met de gegevens die worden teruggezet vanuit de back-up.

  ![Scherm opname van verwijderde data base herstellen](./media/recovery-using-backups/restore-deleted-sql-database-annotated.png)

#### <a name="sql-managed-instance"></a>SQL Managed Instance

Als u een beheerde Data Base wilt herstellen met behulp van de Azure Portal, opent u de overzichts pagina van het beheerde exemplaar en selecteert u **Verwijderde data bases**. Selecteer een verwijderde data base die u wilt herstellen en typ de naam voor de nieuwe Data Base die wordt gemaakt met de gegevens die worden teruggezet vanuit de back-up.

  ![Scherm opname van verwijderde Azure SQL Managed instance data base](./media/recovery-using-backups/restore-deleted-sql-managed-instance-annotated.png)

### <a name="deleted-database-restore-by-using-powershell"></a>Verwijderde data base terugzetten met behulp van Power shell

Gebruik de volgende voorbeeld scripts om een verwijderde data base te herstellen voor een SQL Database of een door SQL beheerd exemplaar met behulp van Power shell.

#### <a name="sql-database"></a>SQL Database

Zie [een Data Base herstellen met Power shell](scripts/restore-database-powershell.md)voor een voor beeld van een Power shell-script waarin wordt getoond hoe u een verwijderde data base in Azure SQL database kunt herstellen.

#### <a name="sql-managed-instance"></a>SQL Managed Instance

Zie [verwijderde exemplaar database herstellen met Power shell](../managed-instance/point-in-time-restore.md#restore-a-deleted-database) voor een voor beeld van een Power shell-script waarin wordt getoond hoe een verwijderde exemplaar database moet worden hersteld

> [!TIP]
> Zie [programmatisch herstel uitvoeren met automatische back-ups](recovery-using-backups.md)om een verwijderde data base programmatisch te herstellen.

## <a name="geo-restore"></a>Geo-herstel

> [!IMPORTANT]
> Geo-Restore is alleen beschikbaar voor SQL-data bases of beheerde exemplaren die zijn geconfigureerd met geografisch redundante [back-upopslag](automated-backups-overview.md#backup-storage-redundancy).

U kunt een Data Base op een wille keurige SQL Database Server of een exemplaar database op elk beheerd exemplaar in een Azure-regio herstellen vanuit de meest recente geo-gerepliceerde back-ups. Geo-Restore maakt gebruik van een geo-gerepliceerde back-up als bron. U kunt geografisch herstel aanvragen, zelfs als de data base of het Data Center niet toegankelijk is vanwege een storing.

Geo-Restore is de standaard herstel optie wanneer uw data base niet beschikbaar is vanwege een incident in de hosting regio. U kunt de database herstellen naar een server in elke andere regio. Er is een vertraging tussen het moment waarop een back-up wordt gemaakt en wanneer deze geo-gerepliceerd wordt naar een Azure-Blob in een andere regio. Als gevolg hiervan kan de herstelde data base Maxi maal één uur achter de oorspronkelijke Data Base zijn. In de volgende afbeelding ziet u een Data Base terugzetten van de laatste beschik bare back-up in een andere regio.

![Afbeelding van geo-Restore](./media/recovery-using-backups/geo-restore-2.png)

### <a name="geo-restore-by-using-the-azure-portal"></a>Geo-herstel met behulp van de Azure Portal

Vanuit de Azure Portal maakt u een nieuwe single-of Managed instance-data base en selecteert u een beschik bare geo-herstel back-up. De zojuist gemaakte Data Base bevat de back-upgegevens met geo-herstel bewerking.

#### <a name="sql-database"></a>SQL Database

Ga als volgt te werk om een enkele data base te herstellen uit de Azure Portal in de gewenste regio en server:

1. Selecteer in het **dash board** de optie **Add**  >  **Create SQL database** toevoegen. Voer de vereiste gegevens in op het tabblad **basis beginselen** .
2. Selecteer **aanvullende instellingen**.
3. Selecteer **back-up** voor het **gebruiken van bestaande gegevens**.
4. Selecteer voor **back-up** een back-up in de lijst met beschik bare back-ups van geo-herstel.

    ![Scherm opname van SQL Database opties maken](./media/recovery-using-backups/geo-restore-azure-sql-database-list-annotated.png)

Voltooi het proces van het maken van een nieuwe Data Base uit de back-up. Wanneer u een data base in Azure SQL Database maakt, bevat deze de herstelde back-up van geo-Restore.

#### <a name="sql-managed-instance"></a>SQL Managed Instance

Als u geografisch een beheerde exemplaar database van de Azure Portal naar een bestaand beheerd exemplaar in een door u gekozen gebied wilt herstellen, selecteert u een beheerd exemplaar waarop u een Data Base wilt herstellen. Volg deze stappen:

1. Selecteer **nieuwe data base**.
2. Typ een naam voor de gewenste data base.
3. Selecteer onder **bestaande gegevens gebruiken** de optie **back-up**.
4. Selecteer een back-up in de lijst met beschik bare back-ups van geo-herstel.

    ![Scherm opname van nieuwe database opties](./media/recovery-using-backups/geo-restore-sql-managed-instance-list-annotated.png)

Voltooi het proces van het maken van een nieuwe data base. Wanneer u de exemplaar database maakt, bevat deze de herstelde back-up van geo-Restore.

### <a name="geo-restore-by-using-powershell"></a>Geo-herstel met behulp van Power shell

#### <a name="sql-database"></a>SQL Database

Voor een Power shell-script dat laat zien hoe u geo-herstel kunt uitvoeren voor één data base, raadpleegt u [Power shell gebruiken om een enkele data base te herstellen naar een eerder tijdstip](scripts/restore-database-powershell.md).

#### <a name="sql-managed-instance"></a>SQL Managed Instance

Voor een Power shell-script dat laat zien hoe u geo-herstel kunt uitvoeren voor een beheerde exemplaar database, raadpleegt u [Power shell gebruiken om een Data Base van een beheerd exemplaar te herstellen naar een andere geografische regio](../managed-instance/scripts/restore-geo-backup.md).

### <a name="geo-restore-considerations"></a>Overwegingen voor geo-herstel

U kunt geen herstel naar een bepaald tijdstip uitvoeren op een geo-secundaire data base. U kunt dit alleen doen op een primaire data base. Zie [herstellen vanaf een storing](../../key-vault/general/disaster-recovery-guidance.md)voor gedetailleerde informatie over het gebruik van geo-Restore om te herstellen na een storing.

> [!IMPORTANT]
> Geo-Restore is de meest eenvoudige oplossing voor herstel na nood gevallen die beschikbaar is in SQL Database en SQL Managed instance. Hierbij wordt gebruikgemaakt van automatisch gemaakte geo-gerepliceerde back-ups met een Recovery Point Objective (RPO) tot Maxi maal 1 uur en een geschatte herstel tijd van Maxi maal 12 uur. Het biedt geen garantie dat de doel regio de capaciteit heeft om uw data bases na een regionale onderbreking te herstellen, omdat een sterke toename van de vraag waarschijnlijk is. Als uw toepassing gebruikmaakt van relatief kleine data bases en niet essentieel is voor het bedrijf, is geo-herstel een geschikte oplossing voor herstel na nood gevallen. 
>
> Voor bedrijfskritische toepassingen waarvoor grote data bases zijn vereist en die bedrijfs continuïteit moeten gebruiken, kunt u gebruikmaken van [groepen voor automatische failover](auto-failover-group-overview.md). Het biedt een veel lagere RPO-en herstel tijd en de capaciteit is altijd gegarandeerd. 
>
> Zie [overzicht van bedrijfs continuïteit](business-continuity-high-availability-disaster-recover-hadr-overview.md)voor meer informatie over de mogelijkheden van bedrijfs continuïteit.

## <a name="programmatic-recovery-using-automated-backups"></a>Programmatisch herstel met behulp van geautomatiseerde back-ups

U kunt ook Azure PowerShell of de REST API voor herstel gebruiken. De volgende tabellen bevatten een beschrijving van de beschik bare opdrachten.

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> De Power shell-Azure Resource Manager module wordt nog steeds ondersteund door SQL Database en SQL Managed instance, maar alle toekomstige ontwikkeling is voor de module AZ. SQL. Zie [AzureRM.Sql](/powershell/module/AzureRM.Sql/) voor deze cmdlets. Argumenten voor de opdrachten in de module AZ en in Azure Resource Manager modules zijn in grote mate identiek.

#### <a name="sql-database"></a>SQL Database

Als u een zelfstandige of gegroepeerde Data Base wilt herstellen, raadpleegt u [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase).

  | Cmdlet | Beschrijving |
  | --- | --- |
  | [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) |Hiermee haalt u een of meer databases op. |
  | [Get-AzSqlDeletedDatabaseBackup](/powershell/module/az.sql/get-azsqldeleteddatabasebackup) | Hiermee haalt u een verwijderde database die u kunt herstellen op. |
  | [Get-AzSqlDatabaseGeoBackup](/powershell/module/az.sql/get-azsqldatabasegeobackup) |Hiermee haalt u een geografisch redundante back-up van een database op. |
  | [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase) |Hiermee herstelt u een database. |

  > [!TIP]
  > Zie [een Data Base herstellen met behulp van Power shell](scripts/restore-database-powershell.md)voor een voor beeld van een Power shell-script dat laat zien hoe u een herstel bewerking voor een Data Base uitvoert.

#### <a name="sql-managed-instance"></a>SQL Managed Instance

Zie [Restore-AzSqlInstanceDatabase](/powershell/module/az.sql/restore-azsqlinstancedatabase)voor informatie over het herstellen van een beheerde exemplaar database.

  | Cmdlet | Beschrijving |
  | --- | --- |
  | [Get-AzSqlInstance](/powershell/module/az.sql/get-azsqlinstance) |Hiermee worden een of meer beheerde exemplaren opgehaald. |
  | [Get-AzSqlInstanceDatabase](/powershell/module/az.sql/get-azsqlinstancedatabase) | Hiermee haalt u een exemplaar database op. |
  | [Restore-AzSqlInstanceDatabase](/powershell/module/az.sql/restore-azsqlinstancedatabase) |Hiermee wordt een exemplaar database hersteld. |

### <a name="rest-api"></a>REST-API

Een Data Base herstellen met behulp van de REST API:

| API | Beschrijving |
| --- | --- |
| [REST (createMode = herstel)](/rest/api/sql/databases) |Hiermee herstelt u een database. |
| [Database status van maken of bijwerken ophalen](/rest/api/sql/operations) |Retourneert de status tijdens een herstel bewerking. |

### <a name="azure-cli"></a>Azure CLI

#### <a name="sql-database"></a>SQL Database

Zie [AZ SQL DB Restore](/cli/azure/sql/db#az-sql-db-restore)voor informatie over het herstellen van een Data Base met behulp van de Azure cli.

#### <a name="sql-managed-instance"></a>SQL Managed Instance

Zie [AZ SQL DEELB Restore](/cli/azure/sql/midb#az-sql-midb-restore)voor meer informatie over het herstellen van een beheerde exemplaar database met behulp van de Azure cli.

## <a name="summary"></a>Samenvatting

Met automatische back-ups worden uw data bases beschermd tegen gebruikers-en toepassings fouten, onbedoeld verwijderen van een Data Base en langdurige uitval. Deze ingebouwde mogelijkheid is beschikbaar voor alle service lagen en reken grootten.

## <a name="next-steps"></a>Volgende stappen

- [Overzicht voor bedrijfscontinuïteit](business-continuity-high-availability-disaster-recover-hadr-overview.md)
- [Automatische back-ups SQL Database](automated-backups-overview.md)
- [Lange termijn retentie](long-term-retention-overview.md)
- Zie [actieve geo-replicatie](active-geo-replication-overview.md) of [groepen voor automatische failover](auto-failover-group-overview.md)voor meer informatie over snellere herstel opties.