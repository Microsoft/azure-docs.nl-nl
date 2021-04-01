---
title: Transactionele replicatie
titleSuffix: Azure SQL Managed Instance
description: Meer informatie over het gebruik van SQL Server transactionele replicatie met Azure SQL Managed instance (preview).
services: sql-database
ms.service: sql-managed-instance
ms.subservice: data-movement
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: sstein
ms.date: 04/20/2020
ms.openlocfilehash: 3e4b4fc3d4a6c9529c7c0ac0daef8a28173e0bf3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99225340"
---
# <a name="transactional-replication-with-azure-sql-managed-instance-preview"></a>Transactionele replicatie met Azure SQL Managed instance (preview-versie)
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Transactionele replicatie is een functie van Azure SQL Managed instance en SQL Server waarmee u gegevens kunt repliceren uit een tabel in Azure SQL Managed instance of een SQL Server-exemplaar naar tabellen die zijn geplaatst op externe data bases. Met deze functie kunt u meerdere tabellen in verschillende data bases synchroniseren. 

Transactionele replicatie is momenteel beschikbaar als openbare preview-versie voor SQL Managed Instance. 

## <a name="overview"></a>Overzicht

U kunt transactionele replicatie gebruiken om wijzigingen die zijn aangebracht in een Azure SQL Managed instance, te pushen naar:

- Een SQL Server-Data Base, on-premises of op een Azure VM
- Een data base in Azure SQL Database
- Een exemplaar database in Azure SQL Managed instance

  > [!NOTE]
  > Als u alle functies van Azure SQL Managed instance wilt gebruiken, moet u de nieuwste versies van [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) en [SQL Server Data tools (SSDT)](/sql/ssdt/download-sql-server-data-tools-ssdt)gebruiken.

### <a name="components"></a>Onderdelen

De belangrijkste onderdelen in transactionele replicatie zijn de **Uitgever**, **distributeur** en **abonnee**, zoals wordt weer gegeven in de volgende afbeelding:  

![replicatie met SQL Database](./media/replication-transactional-overview/replication-to-sql-database.png)

| Rol | Azure SQL Database | Azure SQL Managed Instance |
| :----| :------------- | :--------------- |
| **Publisher** | Nee | Ja |
| **Verdeler** | Nee | Ja|
| **Pull-abonnee** | Nee | Ja|
| **Push-abonnee**| Ja | Ja|
| &nbsp; | &nbsp; | &nbsp; |

De **Uitgever** publiceert wijzigingen die zijn aangebracht in sommige tabellen (artikelen) door de updates naar de Distributor te verzenden. De uitgever kan een door Azure SQL beheerd exemplaar of een SQL Server-exemplaar zijn.

De **Distributor** verzamelt wijzigingen in de artikelen van een uitgever en distribueert deze naar de abonnees. De Distributor kan een door Azure SQL beheerd exemplaar of een SQL Server-exemplaar zijn (een wille keurige versie die gelijk is aan of hoger is dan de versie van de uitgever).

De **abonnee** ontvangt wijzigingen die zijn aangebracht op de Publisher. Een SQL Server exemplaar en Azure SQL Managed instance kunnen zowel push-als pull-abonnees zijn, maar een pull-abonnement wordt niet ondersteund wanneer de Distributor een door Azure SQL beheerd exemplaar is en de abonnee niet. Een data base in Azure SQL Database kan alleen een push-abonnee zijn.

Azure SQL Managed instance kan ondersteuning bieden voor een abonnee uit de volgende versies van SQL Server:

- SQL Server 2016 en hoger
- SQL Server 2014 [RTM CU10 (12.0.4427.24)](https://support.microsoft.com/help/3094220/cumulative-update-10-for-sql-server-2014) of [SP1 CU3 (12.0.2556.4)](https://support.microsoft.com/help/3094221/cumulative-update-3-for-sql-server-2014-service-pack-1)
- SQL Server 2012 [SP2 CU8 (11.0.5634.1)](https://support.microsoft.com/help/3082561/cumulative-update-8-for-sql-server-2012-sp2) of [SP3 (11.0.6020.0)](https://www.microsoft.com/download/details.aspx?id=49996)

   > [!NOTE]
   >
   > - Voor andere versies van SQL Server die het publiceren naar objecten in azure niet ondersteunen, is het mogelijk de methode voor het [opnieuw publiceren van gegevens](/sql/relational-databases/replication/republish-data) te gebruiken om gegevens te verplaatsen naar nieuwere versies van SQL Server.
   > - Poging tot het configureren van een replicatie met een oudere versie kan leiden tot een fout nummer MSSQL_REPL20084 (het proces kan geen verbinding maken met de abonnee.) en MSSQ_REPL40532 (kan de server \<name> die door de aanmelding is aangevraagd, niet openen. De aanmelding is mislukt.)

### <a name="types-of-replication"></a>Typen replicatie

Er zijn verschillende [typen replicatie](/sql/relational-databases/replication/types-of-replication):

| Replicatie | Azure SQL Database | Azure SQL Managed Instance |
| :----| :------------- | :--------------- |
| [**Standaard transactionele**](/sql/relational-databases/replication/transactional/transactional-replication) | Ja (alleen als abonnee) | Yes |
| [**Snapshot**](/sql/relational-databases/replication/snapshot-replication) | Ja (alleen als abonnee) | Yes|
| [**Samenvoeg replicatie**](/sql/relational-databases/replication/merge/merge-replication) | Nee | Nee|
| [**Peer-to-peer**](/sql/relational-databases/replication/transactional/peer-to-peer-transactional-replication) | Nee | Nee|
| [**Bidirectioneel**](/sql/relational-databases/replication/transactional/bidirectional-transactional-replication) | Nee | Ja|
| [**Bij te werken abonnementen**](/sql/relational-databases/replication/transactional/updatable-subscriptions-for-transactional-replication) | Nee | Nee|
| &nbsp; | &nbsp; | &nbsp; |

### <a name="supportability-matrix"></a>Ondersteunings matrix

  De matrix voor de ondersteuning van transactionele replicatie voor een Azure SQL Managed instance is hetzelfde als die voor SQL Server.
  
| **Publisher**   | **Verdeler** | **Gehaald** |
| :------------   | :-------------- | :------------- |
| SQL Server 2019 | SQL Server 2019 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/>  |
| SQL Server 2017 | SQL Server 2019 <br/>SQL Server 2017 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 |
| SQL Server 2016 | SQL Server 2019 <br/>SQL Server 2017 <br/> SQL Server 2016 | SQL Server 2019 <br/> SQL Server 2017 <br/>SQL Server 2016 <br/> SQL Server 2014 <br/> SQL Server 2012 |
| SQL Server 2014 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/>| SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/> SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 |
| SQL Server 2012 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/>SQL Server 2012 <br/> | SQL Server 2016 <br/> SQL Server 2014 <br/> SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 |
| SQL Server 2008 R2 <br/> SQL Server 2008 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/>SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 |  SQL Server 2014 <br/> SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 <br/>  |
| &nbsp; | &nbsp; | &nbsp; |

## <a name="when-to-use"></a>Wanneer gebruikt u dit?

Transactionele replicatie is handig in de volgende scenario's:

- Publiceer wijzigingen die zijn aangebracht in een of meer tabellen in een Data Base en distribueer ze naar een of meer data bases in een SQL Server-exemplaar of Azure SQL Database die zijn geabonneerd op de wijzigingen.
- Bewaar verschillende gedistribueerde data bases met de status gesynchroniseerd.
- Migreer data bases van een SQL Server exemplaar of een door Azure SQL beheerd exemplaar naar een andere data base door de wijzigingen continu te publiceren.

### <a name="compare-data-sync-with-transactional-replication"></a>Gegevens synchronisatie met transactionele replicatie vergelijken

| Categorie | Gegevens synchroniseren | Transactionele replicatie |
|---|---|---|
| Voordelen | -Actief-actief ondersteuning<br/>-Bi-richting tussen on-premises en Azure SQL Database | -Laagste latentie<br/>-Transactionele consistentie<br/>-Bestaande topologie na migratie opnieuw gebruiken |
| Nadelen | -Geen transactionele consistentie<br/>-Hogere gevolgen voor de prestaties | -Kan niet publiceren vanaf Azure SQL Database <br/>-Hoge onderhouds kosten |

## <a name="common-configurations"></a>Algemene configuraties

Over het algemeen moeten de uitgever en de Distributor zich in de Cloud of on-premises bestaan. De volgende configuraties worden ondersteund:

### <a name="publisher-with-local-distributor-on-sql-managed-instance"></a>Publisher met lokale Distributor op door SQL beheerd exemplaar

![Eén exemplaar als uitgever en distributeur](./media/replication-transactional-overview/01-single-instance-asdbmi-pubdist.png)

Publisher en Distributor worden geconfigureerd binnen één SQL Managed instance en distribueren wijzigingen naar een ander exemplaar van SQL Managed instance, SQL Database of SQL Server.

### <a name="publisher-with-remote-distributor-on-sql-managed-instance"></a>Publisher met externe Distributor op SQL Managed instance

In deze configuratie publiceert één beheerd exemplaar wijzigingen in een Distributor die zijn geplaatst op een andere SQL Managed instance die een groot aantal exemplaren van SQL-bron beheer kan leveren en wijzigingen kan distribueren naar een of meer doelen op Azure SQL Database, Azure SQL Managed instance of SQL Server.

![Afzonderlijke instanties voor Publisher en Distributor](./media/replication-transactional-overview/02-separate-instances-asdbmi-pubdist.png)

Publisher en Distributor worden geconfigureerd op twee beheerde exemplaren. Er zijn enkele beperkingen met deze configuratie:

- Beide beheerde instanties bevinden zich in hetzelfde vNet.
- Beide beheerde instanties bevinden zich op dezelfde locatie.

### <a name="on-premises-publisherdistributor-with-remote-subscriber"></a>On-premises Uitgever/distributeur met externe abonnee

![Azure SQL Database als abonnee](./media/replication-transactional-overview/03-azure-sql-db-subscriber.png)

In deze configuratie is een-data base in Azure SQL Database of een door Azure SQL beheerd exemplaar een abonnee. Deze configuratie biedt ondersteuning voor migratie van on-premises naar Azure. Als een abonnee een data base in Azure SQL Database is, moet deze zich in de push-modus bevindt.  

## <a name="requirements"></a>Vereisten

- Gebruik SQL-verificatie voor de connectiviteit tussen replicatie deelnemers.
- Gebruik een Azure Storage-account share voor de werkmap die wordt gebruikt voor replicatie.
- Open TCP-uitgaande poort 445 in de beveiligings regels voor het subnet om toegang te krijgen tot de Azure-bestands share.
- Open TCP-uitgaande poort 1433 wanneer het beheerde exemplaar van SQL de uitgever/distributeur is en de abonnee niet. Mogelijk moet u ook de uitgaande beveiligings regel voor het SQL Managed instance NSG voor `allow_linkedserver_outbound` de **doel service label** van de poort 1433 wijzigen van `virtualnetwork` naar `internet` .
- Plaats zowel de uitgever als de Distributor in de Cloud, hetzij on-premises.
- Configureer VPN-peering tussen de virtuele netwerken van replicatie deelnemers als de virtuele netwerken verschillend zijn.

> [!NOTE]
> Fout 53 kan optreden bij het verbinden met een Azure Storage-bestand als de uitgaande netwerk beveiligings groep (NSG) poort 445 wordt geblokkeerd wanneer de Distributor een Azure SQL Managed instance-data base is en de abonnee on-premises is. [Werk de vNet-NSG](../../storage/files/storage-troubleshoot-windows-file-connection-problems.md) bij om dit probleem op te lossen.

## <a name="with-failover-groups"></a>Met failover-groepen

[Actieve geo-replicatie](../database/active-geo-replication-overview.md) wordt niet ondersteund met een door SQL beheerd exemplaar met transactionele replicatie. Gebruik in plaats van actieve geo-replicatie [groepen voor automatische failover](../database/auto-failover-group-overview.md), maar houd er rekening mee dat de publicatie [hand matig](transact-sql-tsql-differences-sql-server.md#replication) moet worden verwijderd uit het primaire beheerde exemplaar en opnieuw wordt gemaakt op het secundaire SQL-beheerde exemplaar na een failover.

Als geo-replicatie is ingeschakeld op een **Uitgever** of een door de **leverancier** beheerde SQL-instantie in een [failovergroep](../database/auto-failover-group-overview.md), moet de beheerder van de SQL Managed instance alle publicaties opschonen op de oude primaire en opnieuw configureren op de nieuwe primaire locatie nadat een failover is uitgevoerd. In dit scenario zijn de volgende activiteiten nodig:

1. Stop alle replicatie taken die worden uitgevoerd op de data base, indien aanwezig.
1. Voer het volgende script uit op de Publisher-data base om de meta gegevens van uw abonnement uit Publisher te verwijderen:

   ```sql
   EXEC sp_dropsubscription @publication='<name of publication>', @article='all',@subscriber='<name of subscriber>'
   ```

1. De meta gegevens van het abonnement op de abonnee weghalen. Voer het volgende script uit op de abonnements database op een door SQL beheerd exemplaar van de abonnee:

   ```sql
   EXEC sp_subscription_cleanup
      @publisher = N'<full DNS of publisher, e.g. example.ac2d23028af5.database.windows.net>',
      @publisher_db = N'<publisher database>',
      @publication = N'<name of publication>';
   ```

1. Haal alle replicatie objecten geforceerd van Publisher door het volgende script uit te voeren in de gepubliceerde Data Base:

   ```sql
   EXEC sp_removedbreplication
   ```

1. De oude Distributor geforceerd verwijderen van het oorspronkelijke primaire SQL-beheerde exemplaar (als er een failover wordt uitgevoerd naar een oude primaire, waarmee een Distributor wordt gebruikt). Voer het volgende script uit op de hoofd database in het oude distributeur SQL Managed instance:

   ```sql
   EXEC sp_dropdistributor 1,1
   ```

Als geo-replicatie is ingeschakeld voor een exemplaar van een **abonnee** in een failovergroep, moet de publicatie worden geconfigureerd om verbinding te maken met het listener-eind punt van de failover-groep voor het beheerde exemplaar van de abonnee. In het geval van een failover is de volgende actie door de beheerder van de beheerde instantie afhankelijk van het type failover dat heeft plaatsgevonden:

- Voor een failover zonder gegevens verlies blijft replicatie na een failover werken.
- Voor een failover met gegevens verlies zal de replicatie ook worden uitgevoerd. De verloren wijzigingen worden opnieuw gerepliceerd.
- Voor een failover met gegevens verlies, maar het gegevens verlies valt buiten de Bewaar periode voor de distributie database, moet de beheerder van de SQL Managed instance de abonnee database opnieuw initialiseren.

## <a name="next-steps"></a>Volgende stappen

Zie de volgende zelf studies voor meer informatie over het configureren van transactionele replicatie:

- [Replicatie configureren tussen een SQL Managed instance-Uitgever en een abonnee](../managed-instance/replication-between-two-instances-configure-tutorial.md)
- [Replicatie configureren tussen een SQL Managed instance, distributeur van SQL Managed instance en SQL Server Subscriber](../managed-instance/replication-two-instances-and-sql-server-configure-tutorial.md)
- [Maak een publicatie](/sql/relational-databases/replication/publish/create-a-publication).
- [Maak een push-abonnement](/sql/relational-databases/replication/create-a-push-subscription) met behulp van de server naam als abonnee (bijvoorbeeld `N'azuresqldbdns.database.windows.net` en de data base in Azure SQL database naam als de doel database (bijvoorbeeld **AdventureWorks**). )

## <a name="see-also"></a>Zie ook  

- [Replicatie met een door SQL beheerd exemplaar en een failovergroep](transact-sql-tsql-differences-sql-server.md#replication)
- [Replicatie naar SQL Database](../database/replication-to-sql-database.md)
- [Replicatie naar een beheerd exemplaar](../managed-instance/replication-between-two-instances-configure-tutorial.md)
- [Een publicatie maken](/sql/relational-databases/replication/publish/create-a-publication)
- [Een push-abonnement maken](/sql/relational-databases/replication/create-a-push-subscription/)
- [Typen replicatie](/sql/relational-databases/replication/types-of-replication)
- [Bewaking (replicatie)](/sql/relational-databases/replication/monitor/monitoring-replication)
- [Een abonnement initialiseren](/sql/relational-databases/replication/initialize-a-subscription)
