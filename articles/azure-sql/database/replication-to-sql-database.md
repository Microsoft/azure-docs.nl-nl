---
title: Replicatie van Azure SQL Server naar Azure SQL Database
description: U kunt een data base in Azure SQL Database configureren als de push-abonnee in een transactionele of momentopname replicatie topologie.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: mathoma
ms.date: 04/28/2020
ms.openlocfilehash: 6ff1d485ab4c0662ae8a9d754ce67b1446b76fcc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92780951"
---
# <a name="replication-to-azure-sql-database"></a>Replicatie naar Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

U kunt een Azure SQL Database als push-abonnee configureren in een transactionele trans actie-of momentopname replicatie topologie.

> [!NOTE]
> In dit artikel wordt het gebruik van [transactionele replicatie](/sql/relational-databases/replication/transactional/transactional-replication) in Azure SQL database beschreven. Het heeft geen betrekking op [actieve geo-replicatie](./active-geo-replication-overview.md), een Azure SQL database functie waarmee u volledig Lees bare replica's van afzonderlijke data bases kunt maken.

## <a name="supported-configurations"></a>Ondersteunde configuraties
  
- Azure SQL Database kan alleen de push-abonnee zijn van een SQL Server Uitgever en distributie server.  
- Het SQL Server-exemplaar dat fungeert als uitgever en/of distributeur kan een exemplaar zijn van [SQL Server die on-premises](https://www.microsoft.com/sql-server/sql-server-downloads), een door [Azure SQL beheerd exemplaar](../managed-instance/instance-create-quickstart.md)of een exemplaar van [SQL Server worden uitgevoerd op een virtuele machine van Azure in de Cloud](../virtual-machines/windows/sql-vm-create-portal-quickstart.md). 
- De distributie database en de replicatie agenten kunnen niet worden geplaatst in een data base in Azure SQL Database.  
- [Snap shot](/sql/relational-databases/replication/snapshot-replication) en [eenrichtings transactionele](/sql/relational-databases/replication/transactional/transactional-replication) replicatie worden ondersteund. Peer-to-peer transactionele replicatie en samenvoeg replicatie worden niet ondersteund.

### <a name="versions"></a>Versies  

Als u wilt repliceren naar een data base in Azure SQL Database, moeten SQL Server-uitgevers en-distributeurs (ten minste) een van de volgende versies gebruiken:

Publiceren naar een Azure SQL Database van een SQL Server-Data Base wordt ondersteund door de volgende versies van SQL Server:

- SQL Server 2016 en hoger
- SQL Server 2014 [RTM CU10 (12.0.4427.24)](https://support.microsoft.com/help/3094220/cumulative-update-10-for-sql-server-2014) of [SP1 CU3 (12.0.2556.4)](https://support.microsoft.com/help/3094221/cumulative-update-3-for-sql-server-2014-service-pack-1)
- SQL Server 2012 [SP2 CU8 (11.0.5634.1)](https://support.microsoft.com/help/3082561/cumulative-update-8-for-sql-server-2012-sp2) of [SP3 (11.0.6020.0)](https://www.microsoft.com/download/details.aspx?id=49996)

> [!NOTE]
> Poging tot het configureren van een replicatie met een niet-ondersteunde versie kan leiden tot een fout nummer MSSQL_REPL20084 (het proces kan geen verbinding maken met de abonnee.) en MSSQL_REPL40532 (kan de server \<name> die door de aanmelding is aangevraagd, niet openen. De aanmelding is mislukt.)  

Als u alle functies van Azure SQL Database wilt gebruiken, moet u de nieuwste versies van [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) en [SQL Server Data Tools](/sql/ssdt/download-sql-server-data-tools-ssdt)gebruiken.  

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

  
## <a name="remarks"></a>Opmerkingen

- Alleen push-abonnementen naar Azure SQL Database worden ondersteund.  
- Replicatie kan worden geconfigureerd met behulp van [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) of door Transact-SQL-instructies uit te voeren op de Publisher. U kunt geen replicatie configureren met behulp van de Azure Portal.  
- Replicatie kan alleen SQL Server verificatie aanmeldingen gebruiken om verbinding te maken met Azure SQL Database.
- Gerepliceerde tabellen moeten een primaire sleutel hebben.  
- U moet een bestaand Azure-abonnement hebben.  
- De Azure SQL Database-abonnee kan zich in elke regio bevinden.  
- Eén publicatie op SQL Server kan zowel Azure SQL Database als SQL Server ondersteunen (on-premises en SQL Server in een Azure virtual machine)-abonnees.  
- Replicatie beheer, bewaking en probleem oplossing moeten worden uitgevoerd vanaf SQL Server in plaats van Azure SQL Database.  
- `@subscriber_type = 0`Wordt alleen ondersteund in **sp_addsubscription** voor SQL database.  
- Azure SQL Database biedt geen ondersteuning voor bi-directionele, onmiddellijke, bijwerk bare of peer-to-peer-replicatie.

## <a name="replication-architecture"></a>Replicatie architectuur  

![Diagram toont de replicatie architectuur met Azure SQL Database, die verschillende abonnee clusters bevat in verschillende regio's, en on-premises virtuele machines van Azure, die een uitgever, uitvoer bare logRead en de uitvoer bare bestanden van de distributie server bevatten die verbinding maken met externe clusters.](./media/replication-to-sql-database/replication-to-sql-database.png)  

## <a name="scenarios"></a>Scenario's  

### <a name="typical-replication-scenario"></a>Typisch replicatie scenario  

1. Een transactionele replicatie publicatie maken op een SQL Server Data Base.  
2. Gebruik op SQL Server de **wizard Nieuw abonnement** of de Transact-SQL-instructies om een push-abonnement te maken voor Azure SQL database.  
3. Met één en gegroepeerde Data bases in Azure SQL Database is de initiële gegevensset een moment opname die wordt gemaakt door de momentopname agent en wordt gedistribueerd en toegepast door de distributie agent. Met een SQL Managed instance-Uitgever kunt u ook een back-up van een Data Base gebruiken om de Azure SQL Database-abonnee te seeden.

### <a name="data-migration-scenario"></a>Scenario voor gegevens migratie  

1. Transactionele replicatie gebruiken om gegevens van een SQL Server Data Base naar Azure SQL Database te repliceren.  
2. Leid de client-of middle-tier-toepassingen om het kopiëren van de data base bij te werken.  
3. Stop het bijwerken van de SQL Server versie van de tabel en verwijder de publicatie.  

## <a name="limitations"></a>Beperkingen

De volgende opties worden niet ondersteund voor Azure SQL Database-abonnementen:

- Koppeling bestands groepen kopiëren  
- Schema's voor het partitioneren van tabellen kopiëren  
- Schema's voor het partitioneren van indexen kopiëren  
- Door de gebruiker gedefinieerde statistieken kopiëren  
- Standaard bindingen kopiëren  
- Regel bindingen kopiëren  
- Volledige-tekst indexen kopiëren  
- XSD van kopiëren XML  
- XML-indexen kopiëren  
- Machtigingen kopiëren  
- Ruimtelijke indexen kopiëren  
- Gefilterde indexen kopiëren  
- Gegevens compressie kenmerk kopiëren  
- Sparse kolom kenmerk kopiëren  
- FileStream converteren naar maximum aantal gegevens typen  
- Hierarchyid naar MAX-gegevens typen converteren  
- Ruimtelijke naar maximum aantal gegevens typen converteren  
- Uitgebreide eigenschappen kopiëren  

### <a name="limitations-to-be-determined"></a>Te bepalen beperkingen

- Sortering kopiëren  
- Uitvoering in een geserialiseerde trans actie van de SP  

## <a name="examples"></a>Voorbeelden

Maak een publicatie en een push-abonnement. Zie voor meer informatie:
  
- [Een publicatie maken](/sql/relational-databases/replication/publish/create-a-publication)
- [Maak een push-abonnement](/sql/relational-databases/replication/create-a-push-subscription/) met behulp van de server naam als de abonnee (bijvoorbeeld **N'azuresqldbdns. data base. Windows. net**) en de naam van de Azure SQL database als doel database (bijvoorbeeld **AdventureWorks**).  

## <a name="see-also"></a>Zie ook  

- [Transactionele replicatie](../managed-instance/replication-transactional-overview.md)
- [Een publicatie maken](/sql/relational-databases/replication/publish/create-a-publication)
- [Een push-abonnement maken](/sql/relational-databases/replication/create-a-push-subscription/)
- [Typen replicatie](/sql/relational-databases/replication/types-of-replication)
- [Bewaking (replicatie)](/sql/relational-databases/replication/monitor/monitoring-replication)
- [Een abonnement initialiseren](/sql/relational-databases/replication/initialize-a-subscription)