---
title: De maximale mate van parallellisme configureren (MAXDOP)
titleSuffix: Azure SQL Database
description: Meer informatie over de maximale mate van parallellisme (MAXDOP).
ms.date: 04/12/2021
services: sql-database
dev_langs:
- TSQL
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: tsql
ms.topic: conceptual
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: ''
ms.openlocfilehash: c9b8e916c82a42df7addb3c49b4452c0eb403023
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107536905"
---
# <a name="configure-the-max-degree-of-parallelism-maxdop-in-azure-sql-database"></a>De maximale mate van parallellisme (MAXDOP) in Azure SQL Database configureren
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

  In dit artikel wordt de **maximale mate van configuratie van parallelle uitvoering (MAXDOP)** in Azure SQL Database. 

> [!NOTE]
> **Deze inhoud is gericht op Azure SQL Database.** Azure SQL Database is gebaseerd op de nieuwste stabiele versie van de Microsoft SQL Server-database-engine, dus veel van de inhoud is vergelijkbaar, hoewel de opties voor probleemoplossing en configuratie verschillen. Zie voor meer informatie over MAXDOP in SQL Server configureren van de maximale mate van [parallelle uitvoering serverconfiguratieoptie.](/sql/database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option)

## <a name="overview"></a>Overzicht
  MAXDOP beheert parallelle parallelle query's in de database-engine. Hogere MAXDOP-waarden resulteren in het algemeen in meer parallelle threads per query en snellere query-uitvoering. 

  In Azure SQL Database is de standaard MAXDOP-instelling voor elke nieuwe individuele database en elastische pooldatabase 8. Deze standaardinstelling voorkomt onnodig resourcegebruik, terwijl de database-engine toch sneller query's kan uitvoeren met behulp van parallelle threads. Het is doorgaans niet nodig om MAXDOP verder te configureren in Azure SQL Database workloads, maar dit kan voordelen bieden als een geavanceerde oefening voor het afstemmen van de prestaties.

> [!Note]
>   In september 2020 is op basis van jaren telemetrie in de Azure SQL Database-service MAXDOP 8 de standaardwaarde voor nieuwe [databases](https://techcommunity.microsoft.com/t5/azure-sql/changing-default-maxdop-in-azure-sql-database-and-azure-sql/ba-p/1538528)gemaakt als de optimale waarde voor de meest uiteenlopende workloads van klanten. Deze standaardinstelling heeft bijgedragen aan het voorkomen van prestatieproblemen vanwege overmatige parallelle uitvoering. Daarvoor was MAXDOP 0 de standaardinstelling voor nieuwe databases. MAXDOP is niet automatisch gewijzigd voor bestaande databases die zijn gemaakt vóór september 2020.

  Als de database-engine er over het algemeen voor kiest om een query uit te voeren met behulp van parallelle uitvoering, is de uitvoeringstijd sneller. Overmatige parallelle uitvoering kan echter extra processorbronnen verbruiken zonder de queryprestaties te verbeteren. Op schaal kan overtollige parallelle uitvoering een negatieve invloed hebben op de queryprestaties voor alle query's die worden uitgevoerd op hetzelfde exemplaar van de database-engine. Van oudsher is het instellen van een bovengrens voor parallelle uitvoering een algemene oefening voor het afstemmen van prestaties in SQL Server workloads.

  In de volgende tabel wordt het gedrag van de database-engine beschreven bij het uitvoeren van query's met verschillende MAXDOP-waarden:

| MAXDOP | Gedrag | 
|--|--|
| = 1 | De database-engine gebruikt één seriële thread om query's uit te voeren. Parallelle threads worden niet gebruikt. | 
| > 1 | De database-engine stelt het aantal extra [schedulers](https://docs.microsoft.com/sql/relational-databases/thread-and-task-architecture-guide#sql-server-task-scheduling) dat door parallelle threads moet worden gebruikt in op de MAXDOP-waarde of het totale aantal logische processors, wat kleiner is. |
| = 0 | De database-engine stelt het aantal extra [schedulers](https://docs.microsoft.com/sql/relational-databases/thread-and-task-architecture-guide#sql-server-task-scheduling) dat door parallelle threads moet worden gebruikt in op het totale aantal logische processors of 64, wat kleiner is. | 
| | |

> [!Note]
> Elke query wordt uitgevoerd met ten minste één scheduler en één werkthread op die scheduler.
>
> Een query die wordt uitgevoerd met parallelle uitvoering, maakt gebruik van extra schedulers en aanvullende parallelle threads. Omdat meerdere parallelle threads kunnen worden uitgevoerd op dezelfde scheduler, kan het totale aantal threads dat wordt gebruikt voor het uitvoeren van een query hoger zijn dan de opgegeven MAXDOP-waarde of het totale aantal logische processors. Zie Parallelle taken plannen [voor meer informatie.](/sql/relational-databases/thread-and-task-architecture-guide#scheduling-parallel-tasks)

##  <a name="considerations"></a><a name="Considerations"></a> Overwegingen  

-   In Azure SQL Database kunt u de standaardwaarde voor MAXDOP wijzigen:
    -   Gebruik op queryniveau de **MAXDOP-queryhint** [](/sql/t-sql/queries/hints-transact-sql-query).     
    -   Op databaseniveau met behulp van de configuratie met het bereik van de **MAXDOP-database.** [](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql)

-   Langdurige SQL Server maxdop-overwegingen en -aanbevelingen [zijn](/sql/database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option#Guidelines) van toepassing op Azure SQL Database. 

-   Indexbewerkingen die een index maken of herbouwen, of die een geclusterde index laten vallen, kunnen resource-intensief zijn. U kunt de MAXDOP-waarde van de database voor indexbewerkingen overschrijven door de optie MAXDOP-index op te geven in de `CREATE INDEX` instructie `ALTER INDEX` or. De MAXDOP-waarde wordt toegepast op de instructie tijdens de uitvoering en wordt niet opgeslagen in de metagegevens van de index. Zie Parallelle indexbewerkingen [configureren voor meer informatie.](/sql/relational-databases/indexes/configure-parallel-index-operations)  
  
-   Naast query's en indexbewerkingen beheert de configuratieoptie voor het databasebereik voor MAXDOP ook de parallelle uitvoering van andere instructies die parallelle uitvoering kunnen gebruiken, zoals DBCC CHECKTABLE, DBCC CHECKDB en DBCC CHECKFILEGROUP. 

##  <a name="recommendations"></a><a name="Recommendations"></a> Aanbevelingen  

  Het wijzigen van MAXDOP voor de database kan een grote invloed hebben op de queryprestaties en het resourcegebruik, zowel positief als negatief. Er is echter geen enkele MAXDOP-waarde die optimaal is voor alle workloads. De [aanbevelingen voor het](/sql/database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option#Guidelines) instellen van MAXDOP zijn genuanceerd en zijn afhankelijk van veel factoren. 

  Sommige gelijktijdige piekworkloads werken mogelijk beter met een andere MAXDOP dan andere. Een correct geconfigureerde MAXDOP vermindert het risico op prestatie- en beschikbaarheidsincidenten en kan in sommige gevallen de kosten verlagen door onnodig resourcegebruik te voorkomen en zo omlaag te schalen naar een lagere servicedoelstelling.

### <a name="excessive-parallelism"></a>Overmatig parallellisme

  Een hogere MAXDOP vermindert vaak de duur van CPU-intensieve query's. Overmatige parallelle uitvoering kan echter de prestaties van andere gelijktijdige workloads verslechteren door andere query's van CPU- en werkthread-resources te verminderen. In uitzonderlijke gevallen kan overmatig parallellisme alle database- of elastische poolresources verbruiken, waardoor er time-outs, fouten en toepassingsstoringen optreden. 

> [!Tip]
> We raden klanten aan maxdop niet in te stellen op 0, zelfs als deze momenteel geen problemen lijkt te veroorzaken.

  Overmatig parallellisme wordt het meest problematisch wanneer er meer gelijktijdige aanvragen zijn dan kan worden ondersteund door de CPU- en werkthreadresources die worden geleverd door de servicedoelstelling. Vermijd MAXDOP 0 om het risico op potentiële toekomstige problemen te verminderen als gevolg van overmatig parallellisme als een database wordt opgeschaald, of als toekomstige hardware-generaties in Azure SQL Database meer kernen bieden voor dezelfde databaseservicedoelstelling.

### <a name="modifying-maxdop"></a>MAXDOP wijzigen 

  Als u bepaalt dat een MAXDOP-instelling die verschilt van de standaardinstelling optimaal is voor uw Azure SQL Database-workload, kunt u de `ALTER DATABASE SCOPED CONFIGURATION` T-SQL-instructie gebruiken. Zie de sectie Voorbeelden met [Transact-SQL](#examples) hieronder voor voorbeelden. Als u MAXDOP wilt wijzigen in een niet-standaardwaarde voor elke nieuwe database die u maakt, voegt u deze stap toe aan het implementatieproces van uw database.

  Als een niet-standaard MAXDOP slechts een kleine subset query's in de workload gebruikt, kunt u MAXDOP op queryniveau overschrijven door de HINT OPTION (MAXDOP) toe te voegen. Zie de sectie Voorbeelden met [Transact-SQL](#examples) hieronder voor voorbeelden. 

  Test uw MAXDOP-configuratiewijzigingen grondig met belastingstests met realistische gelijktijdige querybelastingen. 

  MAXDOP voor de primaire en secundaire replica's kan onafhankelijk worden geconfigureerd als verschillende MAXDOP-instellingen optimaal zijn voor uw workloads voor lezen/schrijven en alleen-lezen. Dit geldt voor Azure SQL Database [uitschalen,](read-scale-out.md)geo-replicatie en [secundaire](service-tier-hyperscale.md) Hyperscale-replica's. [](active-geo-replication-overview.md) Standaard nemen alle secundaire replica's de MAXDOP-configuratie van de primaire replica over.

## <a name="security"></a><a name="Security"></a> Veiligheid  
  
###  <a name="permissions"></a><a name="Permissions"></a> Machtigingen  
  De instructie moet worden uitgevoerd als de serverbeheerder, als lid van de databaserol of als een gebruiker `ALTER DATABASE SCOPED CONFIGURATION` die de machtiging heeft `db_owner` `ALTER ANY DATABASE SCOPED CONFIGURATION` gekregen.
 
## <a name="examples"></a>Voorbeelden   

  In deze voorbeelden wordt de meest recente **AdventureWorksLT-voorbeelddatabase** gebruikt wanneer de optie wordt gekozen voor `SAMPLE` een nieuwe individuele database met Azure SQL Database.

### <a name="powershell"></a>PowerShell

#### <a name="maxdop-database-scoped-configuration"></a>Configuratie met MAXDOP-databasebereik   

  In dit voorbeeld ziet u hoe u [de instructie ALTER DATABASE SCOPED CONFIGURATION gebruikt](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) om de configuratie in te stellen op `MAXDOP` `2` . De instelling wordt onmiddellijk van kracht voor nieuwe query's. De PowerShell-cmdlet [Invoke-SqlCmd](/powershell/module/sqlserver/invoke-sqlcmd) voert de T-SQL-query's uit om de configuratie van het MAXDOP-databasebereik in te stellen en te retourneren. 

```powershell
$dbName = "sample" 
$serverName = <server name here>
$serveradminLogin = <login here>
$serveradminPassword = <password here>
$desiredMAXDOP = 8

$params = @{
    'database' = $dbName
    'serverInstance' =  $serverName
    'username' = $serveradminLogin
    'password' = $serveradminPassword
    'outputSqlErrors' = $true
    'query' = 'ALTER DATABASE SCOPED CONFIGURATION SET MAXDOP = ' + $desiredMAXDOP + ';
     SELECT [value] FROM sys.database_scoped_configurations WHERE [name] = ''MAXDOP'';'
  }
  Invoke-SqlCmd @params
```

Dit voorbeeld is voor gebruik met Azure SQL Databases met uitschaalreplica's voor lezen [ingeschakeld,](read-scale-out.md) [geo-replicatie](active-geo-replication-overview.md)en Azure SQL Database [hyperscale secundaire replica's.](service-tier-hyperscale.md) De primaire replica is bijvoorbeeld ingesteld op een andere standaard MAXDOP als de secundaire replica, in de afwachting dat er mogelijk verschillen zijn tussen een lees-/schrijfworkload en een alleen-lezen workload.

```powershell
$dbName = "sample" 
$serverName = <server name here>
$serveradminLogin = <login here>
$serveradminPassword = <password here>
$desiredMAXDOP_primary = 8
$desiredMAXDOP_secondary_readonly = 1
 
$params = @{
    'database' = $dbName
    'serverInstance' =  $serverName
    'username' = $serveradminLogin
    'password' = $serveradminPassword
    'outputSqlErrors' = $true
    'query' = 'ALTER DATABASE SCOPED CONFIGURATION SET MAXDOP = ' + $desiredMAXDOP + ';
    ALTER DATABASE SCOPED CONFIGURATION FOR SECONDARY SET MAXDOP = ' + $desiredMAXDOP_secondary_readonly + ';
    SELECT [value], value_for_secondary FROM sys.database_scoped_configurations WHERE [name] = ''MAXDOP'';'
  }
  Invoke-SqlCmd @params
```

### <a name="transact-sql"></a>Transact-SQL
  
  U kunt de [Azure Portal queryeditor](connect-query-portal.md), [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms)of [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio) gebruiken om T-SQL-query's uit te voeren op uw Azure SQL Database.

1.  Open een nieuw queryvenster.

2.  Maak verbinding met de database waarin u MAXDOP wilt wijzigen. U kunt configuraties met databasebereik niet wijzigen in de hoofddatabase.
  
3.  Kopieer en plak het volgende voorbeeld in het queryvenster en selecteer **Uitvoeren.** 

#### <a name="maxdop-database-scoped-configuration"></a>MAXDOP-databaseconfiguratie

  In dit voorbeeld ziet u hoe u de huidige maxdop-databaseconfiguratie binnen databasebereik kunt bepalen met behulp van [de weergave sys.database_scoped_configurations](/sql/relational-databases/system-catalog-views/sys-database-scoped-configurations-transact-sql) systeemcatalogus.

```sql
SELECT [value] FROM sys.database_scoped_configurations WHERE [name] = 'MAXDOP';
```

  In dit voorbeeld ziet u hoe u de [instructie ALTER DATABASE SCOPED CONFIGURATION gebruikt](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) om de configuratie in te stellen op `MAXDOP` `8` . De instelling wordt onmiddellijk van kracht.  
  
```sql  
ALTER DATABASE SCOPED CONFIGURATION SET MAXDOP = 8;
```  

Dit voorbeeld is voor gebruik met Azure SQL Databases met uitschaalreplica's voor lezen ingeschakeld, [geo-replicatie](active-geo-replication-overview.md)en [secundaire](service-tier-hyperscale.md) Hyperscale-replica's. [](read-scale-out.md) De primaire replica is bijvoorbeeld ingesteld op een andere MAXDOP dan de secundaire replica, in de afwachting dat er mogelijk verschillen zijn tussen de workloads voor lezen/schrijven en alleen-lezen. Alle instructies worden uitgevoerd op de primaire replica. De `value_for_secondary` kolom van de bevat instellingen voor de `sys.database_scoped_configurations` secundaire replica.

```sql
ALTER DATABASE SCOPED CONFIGURATION SET MAXDOP = 8;
ALTER DATABASE SCOPED CONFIGURATION FOR SECONDARY SET MAXDOP = 1;
SELECT [value], value_for_secondary FROM sys.database_scoped_configurations WHERE [name] = 'MAXDOP';
```

#### <a name="maxdop-query-hint"></a>MAXDOP-queryhint

  In dit voorbeeld ziet u hoe u een query uitvoert met behulp van de queryhint om de tot `max degree of parallelism` af te `2` dwingen.  

```sql 
SELECT ProductID, OrderQty, SUM(LineTotal) AS Total  
FROM SalesLT.SalesOrderDetail  
WHERE UnitPrice < 5  
GROUP BY ProductID, OrderQty  
ORDER BY ProductID, OrderQty  
OPTION (MAXDOP 2);    
GO
```
#### <a name="maxdop-index-option"></a>MaxDOP-indexoptie

  In dit voorbeeld ziet u hoe u een index herbouwt met behulp van de indexoptie om de af te `max degree of parallelism` dwingen tot `12` .  

```sql 
ALTER INDEX ALL ON SalesLT.SalesOrderDetail 
REBUILD WITH 
   (     MAXDOP = 12
       , SORT_IN_TEMPDB = ON
       , ONLINE = ON);
```

## <a name="see-also"></a>Zie ook  
* [ALTER DATABASE SCOPED CONFIGURATION &#40;Transact-SQL&#41;](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql)        
* [sys.database_scoped_configurations (Transact-SQL)](/sql/relational-databases/system-catalog-views/sys-database-scoped-configurations-transact-sql)
* [Parallelle indexbewerkingen configureren](/sql/relational-databases/indexes/configure-parallel-index-operations)    
* [Queryhints &#40;Transact-SQL-&#41;](/sql/t-sql/queries/hints-transact-sql-query)     
* [Indexopties instellen](/sql/relational-databases/indexes/set-index-options)     
* [Problemen met blokkerende Azure SQL Database begrijpen en oplossen](understand-resolve-blocking.md)

## <a name="next-steps"></a>Volgende stappen

* [Prestaties bewaken en afstemmen](/sql/relational-databases/performance/monitor-and-tune-for-performance)
