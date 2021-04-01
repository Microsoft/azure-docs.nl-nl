---
title: SQL Server-databasemigratie naar Azure SQL Database
description: Meer informatie over SQL Server database migratie naar Azure SQL Database.
keywords: databasemigratie, sql server-databasemigratie, hulpprogramma's voor databasemigratie, database migreren, sql-database migreren
services: sql-database
ms.service: sql-database
ms.subservice: migration
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 02/11/2019
ms.openlocfilehash: fab52fcea03a2f65c868cfac27f8a8cef115b2be
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94917644"
---
# <a name="sql-server-database-migration-to-azure-sql-database"></a>SQL Server-databasemigratie naar Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

In dit artikel vindt u informatie over de primaire methoden voor het migreren van een SQL Server 2005 of hoger-Data Base naar Azure SQL Database. Zie [een SQL Server-exemplaar migreren naar Azure SQL Managed](../migration-guides/managed-instance/sql-server-to-managed-instance-overview.md)instance (Engelstalig) voor meer informatie over het migreren naar een beheerd exemplaar van Azure SQL. Zie [migreren naar Azure SQL](../migration-guides/index.yml) voor meer informatie over het kiezen van migratie opties en hulpprogram ma's voor het migreren naar Azure SQL


## <a name="migrate-to-a-single-database-or-a-pooled-database"></a>Migreren naar een enkele data base of een gegroepeerde Data Base

Er zijn twee primaire methoden voor het migreren van een Data Base van SQL Server 2005 of hoger naar Azure SQL Database. De eerste methode is eenvoudiger, maar leidt wel tot enige uitvaltijd tijdens de migratie. In bepaalde gevallen kan deze uitvaltijd aanzienlijk zijn. De tweede methode is complexer, maar veroorzaakt tijdens de migratie veel minder uitvaltijd.

In beide gevallen moet u ervoor zorgen dat de bron database compatibel is met Azure SQL Database met behulp van de [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595). SQL Database is bijna de [functie pariteit](features-comparison.md) bereikt met SQL Server, met uitzonde ring van problemen met betrekking tot bewerkingen op server niveau en andere data bases. Bij databases en toepassingen die afhankelijk zijn van [slechts gedeeltelijk of in het geheel niet ondersteunde functies](transact-sql-tsql-differences-sql-server.md), is een zekere mate van [herstructurering vereist om compatibiliteitsproblemen](migrate-to-database-from-sql-server.md#resolving-database-migration-compatibility-issues) op te lossen voordat de SQL Server-database kan worden gemigreerd.

> [!NOTE]
> Voor het migreren van een SQL Server-database, met inbegrip van Microsoft Access, Sybase, MySQL Oracle en DB2, naar Azure SQL Database raadpleegt u [SQL Server Migration Assistant](https://blogs.msdn.microsoft.com/datamigration/2017/09/29/release-sql-server-migration-assistant-ssma-v7-6/) (Migratieassistent voor SQL Server).

## <a name="method-1-migration-with-downtime-during-the-migration"></a>Methode 1: Migratie die gepaard gaat met uitvaltijd

 Gebruik deze methode om naar een enkele of een gegroepeerde Data Base te migreren als u enige downtime kunt bieden of als u een test migratie van een productie database wilt uitvoeren voor latere migratie. Zie [een SQL Server-Data Base migreren](../../dms/tutorial-sql-server-to-azure-sql.md)voor een zelf studie.

De volgende lijst bevat de algemene werk stroom voor een SQL Server database migratie van een enkele of een gegroepeerde Data Base met behulp van deze methode. Zie [migratie naar SQL Managed](../managed-instance/migrate-to-instance-from-sql-server.md)instance voor migratie naar SQL Managed instance.

  ![Diagram van VSSSDT-migratie](./media/migrate-to-database-from-sql-server/azure-sql-migration-sql-db.png)

1. [Evalueer](/sql/dma/dma-assesssqlonprem) de Data Base voor compatibiliteit met behulp van de nieuwste versie van de [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595).
2. Bereid alle benodigde fixes voor als Transact-SQL-scripts.
3. Maak een transactioneel consistente kopie van de bron database die wordt gemigreerd of stopt nieuwe trans acties in de bron database terwijl de migratie plaatsvindt. Voor het uitvoeren van deze laatste optie, waaronder het uitschakelen van client connectiviteit of het maken van een [moment opname van een Data Base](/sql/relational-databases/databases/create-a-database-snapshot-transact-sql). Na de migratie kunt u transactionele replicatie gebruiken om de gemigreerde data bases bij te werken met wijzigingen die zich voordoen na het afsluit punt voor de migratie. Zie [migreren met transactionele migratie](migrate-to-database-from-sql-server.md#method-2-use-transactional-replication).  
4. Implementeer de Transact-SQL-scripts om de fixes toe te passen op de databasekopie.
5. [Migreer](/sql/dma/dma-migrateonpremsql) de database kopie naar een nieuwe data base in Azure SQL database met behulp van de Data Migration Assistant.

> [!NOTE]
> In plaats van DMA te gebruiken, kunt u ook een BACPAC-bestand gebruiken. Zie [een BACPAC-bestand importeren in een nieuwe data base in Azure SQL database](database-import.md).

### <a name="optimizing-data-transfer-performance-during-migration"></a>De prestaties van de gegevensoverdracht tijdens de migratie optimaliseren

Hieronder leest u enkele aanbevelingen voor het behalen van optimale prestaties tijdens het importeren.

- Kies de hoogste servicelaag en reken grootte die uw budget toestaat om de overdrachts prestaties te maximaliseren. Na voltooiing van de migratie kunt u weer omlaag schalen om geld te besparen.
- Minimaliseer de afstand tussen uw BACPAC-bestand en het doel Data Center.
- Autostatistics tijdens migratie uitschakelen
- Partitioneer tabellen en indexen.
- Verwijder geïndexeerde weergaven en maak ze opnieuw nadat de migratie is voltooid.
- Verwijder zelden gequeryte historische gegevens naar een andere data base en migreer deze historische gegevens naar een afzonderlijke data base in Azure SQL Database. U kunt dan [elastische query's](elastic-query-overview.md) uitvoeren om eventueel benodigde historische gegevens op te vragen.

### <a name="optimize-performance-after-the-migration-completes"></a>Prestaties optimaliseren nadat de migratie is voltooid

[Werk uw statistieken bij](/sql/t-sql/statements/update-statistics-transact-sql) met een volledige scan nadat de migratie is voltooid.

## <a name="method-2-use-transactional-replication"></a>Methode 2: Transactionele replicatie gebruiken

Wanneer u de SQL Server-Data Base niet uit de productie omgeving kunt verwijderen terwijl de migratie wordt uitgevoerd, gebruikt u SQL Server transactionele replicatie als uw migratie oplossing. Voor het gebruik van deze methode moet de brondatabase voldoen aan de [vereisten voor transactionele replicatie](./replication-to-sql-database.md) en compatibel zijn met Azure SQL Database. Zie replicatie configureren voor AlwaysOn- [beschikbaarheids groepen (SQL Server)](/sql/database-engine/availability-groups/windows/configure-replication-for-always-on-availability-groups-sql-server)voor meer informatie over SQL-replicatie met Always on.

Als u deze oplossing wilt gebruiken, configureert u uw data base in Azure SQL Database als een abonnee voor het SQL Server exemplaar dat u wilt migreren. De distributeur voor transactionele replicatie synchroniseert dan de gegevens uit de database die moeten worden gesynchroniseerd (de uitgever) terwijl er nieuwe transacties blijven binnenkomen.

Met transactionele replicatie worden alle wijzigingen aan uw gegevens of schema weer gegeven in uw data base in Azure SQL Database. Nadat de synchronisatie is voltooid en u klaar bent om te migreren, wijzigt u de connection string van uw toepassingen zodat deze naar uw data base worden gewijzen. Wanneer door toepassing van transactionele replicatie alle wijzigingen die nog in uw brondatabase aanwezig zijn, zijn overgenomen en al uw toepassingen naar Azure DB verwijzen, kunt u de functie voor transactionele replicatie verwijderen. Uw data base in Azure SQL Database is nu uw productie systeem.

 ![SeedCloudTR-diagram](./media/migrate-to-database-from-sql-server/SeedCloudTR.png)

> [!TIP]
> U kunt transactionele replicatie ook gebruiken voor het migreren van een subset van uw brondatabase. De publicatie die u naar Azure SQL Database repliceert, kan worden beperkt tot een subset van de tabellen in de betreffende database. Voor elke tabel die wordt gerepliceerd, kunt u de gegevens beperken tot een subset van de rijen en/of een subset van de kolommen.

## <a name="migration-to-sql-database-using-transaction-replication-workflow"></a>Migratie naar SQL Database met de transactiereplicatiewerkstroom

> [!IMPORTANT]
> Gebruik de nieuwste versie van SQL Server Management Studio om gesynchroniseerd te blijven met updates voor Azure en SQL Database. In oudere versies van SQL Server Management Studio kunt u SQL Database niet instellen als abonnee. [SQL Server Management Studio bijwerken](/sql/ssms/download-sql-server-management-studio-ssms).

1. Distributie instellen
   - [SQL Server Management Studio (SSMS) gebruiken](/sql/relational-databases/replication/configure-publishing-and-distribution/)
   - [Transact-SQL gebruiken](/sql/relational-databases/replication/configure-publishing-and-distribution/)

2. Publicatie maken
   - [SQL Server Management Studio (SSMS) gebruiken](/sql/relational-databases/replication/configure-publishing-and-distribution/)
   - [Transact-SQL gebruiken](/sql/relational-databases/replication/configure-publishing-and-distribution/)
3. Abonnement maken
   - [SQL Server Management Studio (SSMS) gebruiken](/sql/relational-databases/replication/create-a-push-subscription/)
   - [Transact-SQL gebruiken](/sql/relational-databases/replication/create-a-push-subscription/)

Enkele tips en verschillen met betrekking tot migratie naar SQL Database

- Gebruik een lokale distributeur.
  - Dit veroorzaakt een invloed op de prestaties van de server.
  - Als de prestatie-impact onacceptabel is, kunt u een andere server gebruiken, maar dit verhoogt wel de complexiteit van het beheer.
- Als u een map met momentopnamen selecteert, moet u ervoor zorgen dat de map die u selecteert, groot genoeg is voor een BCP van elke tabel die u wilt repliceren.
- Met het maken van moment opnamen worden de gekoppelde tabellen vergrendeld totdat deze is voltooid, dus moet u de moment opname op de juiste wijze plannen.
- In Azure SQL Database worden alleen push-abonnementen ondersteund. U kunt alleen abonnees toevoegen vanuit de brondatabase.

## <a name="resolving-database-migration-compatibility-issues"></a>Compatibiliteitsproblemen met databasemigratie oplossen

Er zijn een groot aantal compatibiliteits problemen die kunnen optreden, afhankelijk van de versie van SQL Server in de bron database en de complexiteit van de data base die u wilt migreren. Oudere versies van SQL Server hebben vaker te maken met compatibiliteitsproblemen. Gebruik de volgende resources en voer ook op internet een gerichte zoekopdracht uit (met uw zoekmachine):

- [SQL Server-databasefuncties die niet worden ondersteund in Azure SQL Database](transact-sql-tsql-differences-sql-server.md)
- [Database Engine-functionaliteit in SQL Server 2016 stopgezet](/sql/database-engine/discontinued-database-engine-functionality-in-sql-server)
- [Database Engine-functionaliteit in SQL Server 2014 stopgezet](/sql/database-engine/discontinued-database-engine-functionality-in-sql-server?viewFallbackFrom=sql-server-2014)
- [Database Engine-functionaliteit in SQL Server 2012 stopgezet](/previous-versions/sql/sql-server-2012/ms144262(v=sql.110))
- [Database Engine-functionaliteit in SQL Server 2008 R2 stopgezet](/previous-versions/sql/sql-server-2008-r2/ms144262(v=sql.105))
- [Database Engine-functionaliteit in SQL Server 2005 stopgezet](/previous-versions/sql/sql-server-2005/ms144262(v=sql.90))

Naast het zoeken op internet en het gebruik van deze resources, gebruikt u de [pagina micro soft Q&een vraag voor Azure SQL database](/answers/topics/azure-sql-database.html) of [stack overflow](https://stackoverflow.com/).

> [!IMPORTANT]
> Met Azure SQL Managed Instance kunt u een bestaand SQL Server exemplaar en de bijbehorende data bases migreren met minimale problemen met de compatibiliteit. Zie [Wat is een beheerd exemplaar](../managed-instance/sql-managed-instance-paas-overview.md).

## <a name="next-steps"></a>Volgende stappen

- Gebruik het script dat beschikbaar is op de Azure SQL EMEA Engineers-blog [om het tempdb-gebruik tijdens de migratie te bewaken](/archive/blogs/azuresqlemea/lesson-learned-10-monitoring-tempdb-usage).
- Gebruik het script dat beschikbaar is op de Azure SQL EMEA Engineers-blog [om de transactielogboekruimte van de database te bewaken terwijl de migratie bezig is](/archive/blogs/azuresqlemea/lesson-learned-7-monitoring-the-transaction-log-space-of-my-database).
- Raadpleeg dit blogartikel van het SQL Server-klantadviesteam over migratie met behulp van BACPAC-bestanden: [Migrating from SQL Server to Azure SQL Database using BACPAC Files](/archive/blogs/sqlcat/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files) (Migreren van SQL Server naar Azure SQL Database met BACPAC-bestanden).
- Meer informatie over hoe u na de migratie met de UTC-tijd werkt, vindt u in [Modifying the default time zone for your local time zone](/archive/blogs/azuresqlemea/lesson-learned-4-modifying-the-default-time-zone-for-your-local-time-zone) (De standaardtijdzone voor uw lokale tijdzone wijzigen).
- Meer informatie over hoe u na de migratie de standaardtaal van een database wijzigt, vindt u in [How to change the default language of Azure SQL Database](/archive/blogs/azuresqlemea/lesson-learned-16-how-to-change-the-default-language-of-azure-sql-database) (De standaardtaal van Azure SQL Database wijzigen).
