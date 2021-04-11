---
title: De maximale mate van parallellisme configureren (MAXDOP)
titleSuffix: Azure SQL Database
description: Meer informatie over de maximale mate van parallellisme (MAXDOP).
ms.date: 03/29/2021
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
ms.openlocfilehash: 31ddf15975abdce70ea02b5de64ea5611e7e72b3
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2021
ms.locfileid: "106111642"
---
# <a name="configure-the-max-degree-of-parallelism-maxdop-in-azure-sql-database"></a>De maximale mate van parallellisme (MAXDOP) in Azure SQL Database configureren
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

  In dit artikel wordt de **maximale mate van parallelle uitvoering (MAXDOP)** in Azure SQL database beschreven en hoe deze kan worden geconfigureerd. 

> [!NOTE]
> **Deze inhoud is gericht op Azure SQL Database.** Azure SQL Database is gebaseerd op de nieuwste stabiele versie van de Microsoft SQL Server-data base-engine, waardoor een groot deel van de inhoud vergelijkbaar is met het oplossen van problemen en configuratie opties verschillen. Zie [de configuratie optie maximale graad van parallellisme configureren](/sql/database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option)voor meer informatie over MAXDOP in SQL Server.

## <a name="overview"></a>Overzicht
  In Azure SQL Database is de standaard instelling MAXDOP voor elke nieuwe data base en pool voor Elastic data base 8. Dit betekent dat de data base-engine query's kan uitvoeren met meerdere threads. In tegens telling tot SQL Server, waarbij de standaard MAXDOP voor de hele server 0 (onbeperkt) is, worden standaard nieuwe data bases in Azure SQL Database ingesteld op MAXDOP 8. Deze standaard voor komt onnodig resource gebruik en zorgt voor een consistente klant ervaring. Het is doorgaans niet nodig om de MAXDOP in Azure SQL Database workloads te configureren, maar het kan ook voor delen bieden als een geavanceerde prestatie afstemmings oefening.

> [!Note]
>   In september 2020 was gebaseerd op jaren van telemetrie in de Azure SQL Database service [MAXDOP 8 werd gekozen](https://techcommunity.microsoft.com/t5/azure-sql/changing-default-maxdop-in-azure-sql-database-and-azure-sql/ba-p/1538528) als de standaard waarde voor nieuwe data bases als een optimale prijs voor het breedste scala aan klant werkbelastingen. Deze standaard heeft bijgedragen tot het voor komen van prestatie problemen vanwege buitensporige parallellisatie. Vóór dat is de standaard instelling voor nieuwe data bases MAXDOP 0. De configuratie optie voor het bereik van de MAXDOP-data base is niet gewijzigd voor bestaande data bases die zijn gemaakt vóór 2020 september.

  Als de data base-engine in het algemeen een query uitvoert met behulp van parallellisme, is uitvoerings tijd sneller. De overmaat parallelie kan echter overtollige processor bronnen verbruiken zonder de query prestaties te verbeteren. Op schaal kan een overtollige parallellisme een negatieve invloed hebben op de query prestaties van alle query's die worden uitgevoerd op hetzelfde exemplaar van de data base-engine. het instellen van een bovengrens voor parallelisme is een gemeen schappelijke prestatie afstemmings oefening in SQL Server werk belastingen.

  De volgende tabel beschrijft de werking van het data base-engine bij het uitvoeren van query's met verschillende MAXDOP-waarden:

| MAXDOP | Gedrag | 
|--|--|
| = 1 | De data base-engine voert geen query's uit met meerdere gelijktijdige threads. | 
| > 1 | De data base-engine stelt een bovengrens voor het aantal parallelle threads in. De data base-engine kiest het aantal extra worker-threads dat moet worden gebruikt. Het totale aantal worker-threads dat is gebruikt om een query uit te voeren, kan hoger zijn dan de opgegeven MAXDOP-waarde. |
| = 0 | De data base-engine kan gebruikmaken van een aantal parallelle threads met een bovenste grens die afhankelijk is van het totale aantal logische processors. De data base-engine kiest het aantal parallelle threads dat moet worden gebruikt.| 
| | |
  
##  <a name="considerations"></a><a name="Considerations"></a> Tot  

-   In Azure SQL Database kunt u de standaard waarde voor MAXDOP wijzigen:
    -   Op query niveau met behulp van de **MAXDOP** - [query Hint](/sql/t-sql/queries/hints-transact-sql-query).     
    -   Op database niveau met behulp van de **MAXDOP** - [Data Base-scoped configuratie](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql).

-   Lange permanente SQL Server overwegingen voor MAXDOP en [aanbevelingen](/sql/database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option#Guidelines) zijn van toepassing op Azure SQL database. 

-   MAXDOP wordt afgedwongen per [taak](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-tasks-transact-sql). Het wordt niet per [aanvraag](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) of per query afgedwongen. Dit betekent dat een enkele aanvraag tijdens een parallelle query uitvoering meerdere taken kan uitvoeren met een bovengrens die wordt bepaald door de MAXDOP. Zie de sectie *parallelle taken plannen* in de [hand leiding voor thread-en taak architectuur](/sql/relational-databases/thread-and-task-architecture-guide)voor meer informatie. 
  
-   Index bewerkingen waarmee een index wordt gemaakt of opnieuw worden samengesteld of die een geclusterde index verwijderen, kunnen veel resources zijn. U kunt de maximale graad van parallellisme voor index bewerkingen door de data base overschrijven door de optie MAXDOP index op te geven in de `CREATE INDEX` or- `ALTER INDEX` instructie. De MAXDOP-waarde wordt tijdens de uitvoerings tijd toegepast op de instructie en wordt niet opgeslagen in de meta gegevens van de index. Zie [Configure parallel index Operations](/sql/relational-databases/indexes/configure-parallel-index-operations)(Engelstalig) voor meer informatie.  
  
-   Naast query's en index bewerkingen beheert de configuratie optie voor het bereik van de Data Base voor MAXDOP ook de parallelle uitvoering van DBCC CHECKTABLE, DBCC CHECKDB en DBCC CHECKFILEGROUP. 

##  <a name="recommendations"></a><a name="Security"></a> Vereisten  

  Het wijzigen van de MAXDOP voor de data base kan een grote invloed hebben op de query prestaties en het resource gebruik, zowel positieve als negatieve. Er is echter geen enkele MAXDOP-waarde die optimaal is voor alle werk belastingen. De aanbevelingen voor het instellen van MAXDOP worden beaspectd en zijn afhankelijk van veel factoren. 

  Sommige maximum aantal gelijktijdige workloads kan beter zijn met een andere MAXDOP dan andere. Een correct geconfigureerde MAXDOP moet het risico op prestatie-en beschikbaarheids incidenten verminderen, en in sommige gevallen vermindert u de kosten door onnodig resource gebruik te voor komen en kunt u de schaal verlagen naar een lagere service doelstelling.

### <a name="excessive-parallelism"></a>Buitensporige parallellisme

  Een hogere MAXDOP vermindert vaak de duur van CPU-intensieve query's. Buitensporige parallellisme kan echter andere gelijktijdige prestaties van de werk belasting verscherpen door Starving van andere query's van de resources van de CPU-en worker-thread. In uitzonderlijke gevallen kan een buitensporige parallellisme alle data bases of elastische pool bronnen verbruiken, waardoor de time-out van query's, fouten en toepassings storingen worden veroorzaakt. 

  We raden klanten aan MAXDOP 0 te vermijden, zelfs als deze geen problemen lijkt te veroorzaken. Buitensporige parallellisme wordt het meest problematisch wanneer de CPU-en worker-threads meer gelijktijdige aanvragen ontvangen dan door de service doelstelling kunnen worden ondersteund. Vermijd MAXDOP 0 om het risico op mogelijke problemen met de toekomst te verminderen als een Data Base omhoog wordt geschaald of als toekomstige hardware gegenereerde Azure SQL Database meer kernen bieden voor dezelfde database service doelstelling.

### <a name="modifying-maxdop"></a>MAXDOP wijzigen 

  Als u vaststelt dat een andere MAXDOP-instelling optimaal is voor uw Azure SQL Database workload, kunt u de `ALTER DATABASE SCOPED CONFIGURATION` T-SQL-instructie gebruiken. Zie de sectie voor [beelden met behulp van Transact-SQL](#examples) hieronder voor voor beelden. Voeg deze stap toe aan het implementatie proces om MAXDOP te wijzigen nadat de data base is gemaakt.

  Als niet-standaard MAXDOP voor delen alleen een subset van query's in de werk belasting, kunt u MAXDOP op het query niveau onderdrukken door de OPTION (MAXDOP)-Hint toe te voegen. Zie de sectie voor [beelden met behulp van Transact-SQL](#examples) hieronder voor voor beelden. 

  Test uw MAXDOP-configuratie wijzigingen grondig met de belasting tests waarbij u realistische gelijktijdige query belasting hebt. 

  De MAXDOP voor de primaire en secundaire replica's kunnen onafhankelijk worden geconfigureerd om te profiteren van verschillende optimale MAXDOP-instellingen voor alleen-lezen en alleen-lezen workloads. Dit is van toepassing op Azure SQL Database [scale-out](read-scale-out.md), [geo-replicatie](active-geo-replication-overview.md)en [Azure SQL database grootschalige secundaire replica's](service-tier-hyperscale.md). Alle secundaire replica's nemen standaard de configuratie van de MAXDOP over van de primaire replica.

## <a name="security"></a><a name="Security"></a> Beveiligingsprincipal  
  
###  <a name="permissions"></a><a name="Permissions"></a> Bevoegdheden  
  De `ALTER DATABASE SCOPED CONFIGURATION` instructie moet worden uitgevoerd als de server beheerder, als lid van de databaserol `db_owner` of een gebruiker aan wie de machtiging is verleend `ALTER ANY DATABASE SCOPED CONFIGURATION` .
 
## <a name="examples"></a>Voorbeelden   

  In deze voor beelden wordt de meest recente **AdventureWorksLT** -voorbeeld database gebruikt wanneer de `SAMPLE` optie wordt gekozen voor een nieuwe enkele data base van Azure SQL database.

### <a name="powershell"></a>PowerShell

#### <a name="maxdop-database-scoped-configuration"></a>MAXDOP-data base-scoped configuratie   

  In dit voor beeld ziet u hoe u met [ALTER data base scoped Configuration](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) -instructie de `max degree of parallelism` optie configureert `2` . De instelling wordt onmiddellijk van kracht. De Power shell [-cmdlet invoke-SqlCmd](/powershell/module/sqlserver/invoke-sqlcmd) voert de T-SQL-query's uit om in te stellen en de MAXDOP-data base-Scope configuratie retour neren. 

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

Dit voor beeld is bedoeld voor gebruik met Azure SQL-data bases met [Lees scale-out replica's ingeschakeld](read-scale-out.md), [geo-replicatie](active-geo-replication-overview.md)en [Azure SQL database grootschalige secundaire replica's](service-tier-hyperscale.md). Als voor beeld is de primaire replica ingesteld op een andere standaard MAXDOP als de secundaire replica, waardoor er rekening kan worden gehouden met verschillen tussen een alleen-lezen werk belasting en een alleen-lezen workload.

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
  
  U kunt de [Azure Portal query-editor](connect-query-portal.md), [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms)of [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio) gebruiken om T-SQL-query's uit te voeren op uw Azure SQL database.

1.  Maak verbinding met de Azure SQL Database. U kunt de configuraties voor database bereik in de hoofd database niet wijzigen.
  
2.  Selecteer in de standaard balk de optie **nieuwe query**.   
  
3.  Kopieer en plak het volgende voor beeld in het query venster en selecteer **uitvoeren**. 


#### <a name="maxdop-database-scoped-configuration"></a>MAXDOP-data base-scoped configuratie

  In dit voor beeld ziet u hoe u de huidige configuratie van het data base-MAXDOP database bereik kunt bepalen met behulp van de weer gave [sys.database_scoped_configurations](/sql/relational-databases/system-catalog-views/sys-database-scoped-configurations-transact-sql) systeem catalogus.

```sql
SELECT [value] FROM sys.database_scoped_configurations WHERE [name] = 'MAXDOP';
```

  In dit voor beeld ziet u hoe u met [ALTER data base scoped Configuration](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) -instructie de `max degree of parallelism` optie configureert `8` . De instelling wordt onmiddellijk van kracht.  
  
```sql  
ALTER DATABASE SCOPED CONFIGURATION SET MAXDOP = 8;
```  

Dit voor beeld is bedoeld voor gebruik met Azure SQL-data bases met [Lees scale-out replica's ingeschakeld](read-scale-out.md), [geo-replicatie](active-geo-replication-overview.md)en [Azure SQL database grootschalige secundaire replica's](service-tier-hyperscale.md). Als voor beeld is de primaire replica ingesteld op een andere standaard MAXDOP als de secundaire replica, waardoor er rekening kan worden gehouden met verschillen tussen een alleen-lezen werk belasting en een alleen-lezen workload. De `value_for_secondary` kolom van de `sys.database_scoped_configurations` bevat instellingen voor de secundaire replica.

```sql
ALTER DATABASE SCOPED CONFIGURATION SET MAXDOP = 8;
ALTER DATABASE SCOPED CONFIGURATION FOR SECONDARY SET MAXDOP = 1;
SELECT [value], value_for_secondary FROM sys.database_scoped_configurations WHERE [name] = 'MAXDOP';
```

#### <a name="maxdop-query-hint"></a>MAXDOP-query Hint

  In dit voor beeld ziet u hoe u een query uitvoert met behulp van de query hint om de `max degree of parallelism` to af te dwingen `2` .  

```sql 
SELECT ProductID, OrderQty, SUM(LineTotal) AS Total  
FROM SalesLT.SalesOrderDetail  
WHERE UnitPrice < 5  
GROUP BY ProductID, OrderQty  
ORDER BY ProductID, OrderQty  
OPTION (MAXDOP 2);    
GO
```
#### <a name="maxdop-index-option"></a>De optie MAXDOP index

  In dit voor beeld ziet u hoe u een index opnieuw bouwt met de optie index om de `max degree of parallelism` to af te dwingen `12` .  

```sql 
ALTER INDEX ALL ON SalesLT.SalesOrderDetail 
REBUILD WITH 
   (     MAXDOP = 12
       , SORT_IN_TEMPDB = ON
       , ONLINE = ON);
```

## <a name="see-also"></a>Zie ook  
* [ALTER data base SCOPEd CONFIGURATION &#40;Transact-SQL&#41;](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql)        
* [sys.database_scoped_configurations (Transact-SQL)](/sql/relational-databases/system-catalog-views/sys-database-scoped-configurations-transact-sql)
* [Bewerkingen voor parallelle indexen configureren](/sql/relational-databases/indexes/configure-parallel-index-operations)    
* [Query hints &#40;Transact-SQL&#41;](/sql/t-sql/queries/hints-transact-sql-query)     
* [Index opties instellen](/sql/relational-databases/indexes/set-index-options)     
* [Problemen met het Azure SQL Database blok keren begrijpen en oplossen](understand-resolve-blocking.md)

## <a name="next-steps"></a>Volgende stappen

* [Prestaties bewaken en afstemmen](/sql/relational-databases/performance/monitor-and-tune-for-performance)
