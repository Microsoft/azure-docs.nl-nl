---
title: Beoordelings regels voor het SQL Server van SQL Database migratie
description: Beoordelings regels voor het identificeren van problemen met de bron SQL Server instantie die moet worden geadresseerd voordat de migratie naar Azure SQL Database.
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: MashaMSFT
ms.author: mathoma
ms.reviewer: MashaMSFT
ms.date: 12/15/2020
ms.openlocfilehash: bf825572226bf5d7432fd3ad825f2f3a13355c53
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102054573"
---
# <a name="assessment-rules-for-sql-server-to-sql-database-migration"></a>Beoordelings regels voor het SQL Server van SQL Database migratie
[!INCLUDE[appliesto--sqldb](../../includes/appliesto-sqldb.md)]

Migratie hulpprogramma's valideren uw bron SQL Server-exemplaar door een aantal beoordelings regels uit te voeren om te bepalen welke problemen moeten worden opgelost voordat u uw SQL Server-Data Base naar Azure SQL Database migreert. 

In dit artikel vindt u een lijst met de regels die worden gebruikt om de uitvoer baarheid van het migreren van uw SQL Server-Data Base naar Azure SQL Database te evalueren. 


## <a name="bulk-insert"></a>Bulksgewijs invoeren<a id="BulkInsert"></a>

**Titel: BULK INSERT met niet-Azure Blob-gegevens bron wordt niet ondersteund in Azure SQL Database.**   
**Categorie**: probleem   

**Beschrijvingen**   
Azure SQL Database heeft geen toegang tot bestands shares of Windows-mappen. Zie de sectie ' betrokken objecten ' voor het specifieke gebruik van BULK INSERT-instructies die niet verwijzen naar een Azure-Blob. Objecten met ' BULK INSERT ' waarbij de bron geen Azure Blob-opslag is, werken niet na de migratie naar Azure SQL Database. 


**Advies**   
U moet in plaats daarvan BULK INSERT-instructies converteren die gebruikmaken van lokale bestanden of bestands shares voor het gebruik van bestanden uit Azure Blob-opslag, wanneer u migreert naar Azure SQL Database. U kunt ook naar SQL Server migreren op de virtuele machine van Azure.

## <a name="compute-clause"></a>COMPUTE-component<a id="ComputeClause"></a>

**Titel: COMPUTe-component is uit gebruik genomen en is verwijderd.**   
**Categorie**: waarschuwing   

**Beschrijvingen**   
De COMPUTe-component genereert totalen die worden weer gegeven als aanvullende samenvattings kolommen aan het einde van de resultatenset. Deze component wordt echter niet meer ondersteund in Azure SQL Database. 


**Advies**   
De T-SQL-module moet in plaats daarvan worden herschreven met behulp van de operator ROLLUP. De volgende code laat zien hoe COMPUTe kan worden vervangen met ROLLUP: 

```sql 
USE AdventureWorks 
GO;  

SELECT SalesOrderID, UnitPrice, UnitPriceDiscount 
FROM Sales.SalesOrderDetail 
ORDER BY SalesOrderID COMPUTE SUM(UnitPrice), SUM(UnitPriceDiscount) BY SalesOrderID GO; 

SELECT SalesOrderID, UnitPrice, UnitPriceDiscount,SUM(UnitPrice) as UnitPrice , 
SUM(UnitPriceDiscount) as UnitPriceDiscount 
FROM Sales.SalesOrderDetail 
GROUP BY SalesOrderID, UnitPrice, UnitPriceDiscount WITH ROLLUP; 
```

Meer informatie: niet-uitgevoerde [Data base-engine functionaliteit in SQL Server ](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="change-data-capture-cdc"></a>Change Data Capture (CDC)<a id="CDC"></a>

**Titel: Change Data Capture (CDC) wordt niet ondersteund in Azure SQL Database**   
**Categorie**: probleem   


**Beschrijvingen**   
Change Data Capture (CDC) wordt niet ondersteund in Azure SQL Database. Evalueren of Wijzigingen bijhouden in plaats daarvan kan worden gebruikt.  U kunt ook migreren naar Azure SQL Managed instance of SQL Server op Azure Virtual Machines. 


**Advies**   
Change Data Capture (CDC) wordt niet ondersteund in Azure SQL Database. Evalueer of Wijzigingen bijhouden in plaats daarvan kan worden gebruikt of dat u kunt migreren naar Azure SQL Managed instance.

Meer informatie: [bijhouden van Azure SQL-wijzigingen inschakelen](https://social.technet.microsoft.com/wiki/contents/articles/2976.azure-sql-how-to-enable-change-tracking.aspx)

## <a name="clr-assemblies"></a>CLR-assembly's<a id="ClrAssemblies"></a>

**Titel: SQL CLR-assembly's worden niet ondersteund in Azure SQL Database**   
**Categorie**: probleem   


**Beschrijvingen**   
Azure SQL Database biedt geen ondersteuning voor SQL CLR-assembly's. 


**Advies**   
Op dit moment is er geen manier om dit te doen in Azure SQL Database. De aanbevolen alternatieve oplossingen vereisen dat toepassings code en database wijzigingen alleen assembly's gebruiken die door Azure SQL Database worden ondersteund. U kunt ook migreren naar een beheerd exemplaar van Azure SQL of SQL Server op de virtuele machine van Azure

Meer informatie: [niet-ondersteunde Transact-SQL-verschillen in SQL database](../../database/transact-sql-tsql-differences-sql-server.md#transact-sql-syntax-not-supported-in-azure-sql-database)

## <a name="cryptographic-provider"></a>Cryptografische provider<a id="CryptographicProvider"></a>

**Titel: er is een gebruik van CREATE CRYPTOGRAPHIC PROVIDER of ALTER CRYPTOGRAPHIC PROVIDER gevonden. dit wordt niet ondersteund in Azure SQL Database**   
**Categorie**: probleem   

**Beschrijvingen**   
Azure SQL Database biedt geen ondersteuning voor CRYPTOGRAFISCHe PROVIDER-instructies omdat deze geen toegang heeft tot bestanden. Zie de sectie betrokken objecten voor het specifieke gebruik van CRYPTOGRAFISCHe PROVIDER-instructies. Objecten met `CREATE CRYPTOGRAPHIC PROVIDER` of `ALTER CRYPTOGRAPHIC PROVIDER` werken niet goed na de migratie naar Azure SQL database.  


**Advies**   
Objecten bekijken met `CREATE CRYPTOGRAPHIC PROVIDER` of `ALTER CRYPTOGRAPHIC PROVIDER` . Verwijder in dergelijke objecten die vereist zijn het gebruik van deze functies. U kunt ook naar SQL Server migreren op de virtuele machine van Azure

## <a name="cross-database-references"></a>Verwijzingen naar meerdere data bases<a id="CrossDataseReferences"></a>

**Titel: query's voor meerdere data bases worden niet ondersteund in Azure SQL Database**   
**Categorie**: probleem   

**Beschrijvingen**   
Data bases op deze server gebruiken query's voor meerdere data bases, die niet worden ondersteund in Azure SQL Database. 


**Advies**   
Azure SQL Database biedt geen ondersteuning voor query's tussen data bases. De volgende acties worden aanbevolen: 
- Migreer de afhankelijke data base (s) naar Azure SQL Database en gebruik Elastic Database query (momenteel als preview-versie) om een query uit te kunnen vinden op Azure SQL-data bases. 
- Verplaats de afhankelijke gegevens sets van andere data bases naar de data base die wordt gemigreerd. 
- Migreren naar een beheerd exemplaar van Azure SQL.
- Migreer naar SQL Server op de virtuele machine van Azure. 

Meer informatie: [Azure SQL database Elastic data base-query controleren (preview)](../../database/elastic-query-overview.md)

## <a name="database-compatibility"></a>Database compatibiliteit<a id="DbCompatLevelLowerThan100"></a>

**Titel: Azure SQL Database ondersteunt geen compatibiliteits niveaus onder 100.**   
**Categorie**: waarschuwing   

**Beschrijvingen**   
Het database compatibiliteits niveau is een waardevol hulp middel om de data base-engine te helpen bij het uitvoeren van een upgrade, waarbij SQL Server het bijwerken van de functionaliteit van de toepassings status wordt gehandhaafd door hetzelfde compatibiliteits niveau van de data base vooraf te gebruiken. Azure SQL Database ondersteunt geen compatibiliteits niveaus onder 100. 


**Advies**   
Evalueren of de functionaliteit van de toepassing intact is wanneer het database compatibiliteits niveau is bijgewerkt naar 100 op Azure SQL Managed instance. U kunt ook naar SQL Server migreren op de virtuele machine van Azure

## <a name="database-mail"></a>Data base mail<a id="DatabaseMail"></a>

**Titel: Database Mail wordt niet ondersteund in Azure SQL Database.**   
**Categorie**: waarschuwing   

**Beschrijvingen**   
Deze server maakt gebruik van de functie Database Mail, die niet wordt ondersteund in Azure SQL Database.


**Advies**   
U kunt overwegen om te migreren naar een beheerd exemplaar van Azure SQL dat Database Mail ondersteunt.  U kunt ook Azure functions en Sendgrid gebruiken om de functionaliteit van e-mail op Azure SQL Database te bereiken.

Meer informatie: [E-mail verzenden vanuit Azure SQL database met Azure functions script](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/AF%20SendMail)


## <a name="database-principal-alias"></a>Data base-Principal-alias<a id="DatabasePrincipalAlias"></a>

**Titel: SYS. DATABASE_PRINCIPAL_ALIASES wordt stopgezet en is verwijderd.**   
**Categorie**: probleem   

**Beschrijvingen**   
Laden. DATABASE_PRINCIPAL_ALIASES wordt stopgezet en is verwijderd in Azure SQL Database.  


**Advies**   
Gebruik rollen in plaats van aliassen.

Meer informatie: niet-uitgevoerde [Data base-engine functionaliteit in SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)


## <a name="disable_def_cnst_chk-option"></a>DISABLE_DEF_CNST_CHK optie<a id="DisableDefCNSTCHK"></a>

**Titel: de optie DISABLE_DEF_CNST_CHK instellen is niet meer actief en is verwijderd.**   
**Categorie**: probleem   

**Beschrijvingen**   
SET Option DISABLE_DEF_CNST_CHK wordt stopgezet en is verwijderd in Azure SQL Database.  


Meer informatie: niet-uitgevoerde [Data base-engine functionaliteit in SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="fastfirstrow-hint"></a>Hint FASTFIRSTROW<a id="FastFirstRowHint"></a>

**Titel: de FASTFIRSTROW-query Hint is uit gebruik genomen en is verwijderd.**   
**Categorie**: waarschuwing   

**Beschrijvingen**   
De FASTFIRSTROW-query Hint wordt stopgezet en in Azure SQL Database verwijderd.  


**Advies**   
In plaats van de optie FASTFIRSTROW query Hint gebruiken (FAST n).

Meer informatie: niet-uitgevoerde [Data base-engine functionaliteit in SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="filestream"></a>-<a id="FileStream"></a>

**Titel: FileStream wordt niet ondersteund in Azure SQL Database**   
**Categorie**: probleem   

**Beschrijvingen**   
De FileStream-functie, waarmee u ongestructureerde gegevens kunt opslaan, zoals tekst documenten, afbeeldingen en Video's in het NTFS-bestands systeem, wordt niet ondersteund in Azure SQL Database. 


**Advies**   
Upload de ongestructureerde bestanden naar Azure Blob-opslag en sla meta gegevens op die zijn gerelateerd aan deze bestanden (naam, type, URL-locatie, opslag sleutel enz.) in Azure SQL Database. Mogelijk moet u uw toepassing opnieuw inbouwen om blobs voor streamen naar en van Azure SQL Database in te scha kelen. U kunt ook naar SQL Server migreren op de virtuele machine van Azure.

Meer informatie: [stromen van blobs naar en van Azure SQL-blog](https://azure.microsoft.com/blog/streaming-blobs-to-and-from-sql-azure/)


## <a name="linked-server"></a>Gekoppelde server<a id="LinkedServer"></a>

**Titel: de functionaliteit van de gekoppelde server wordt niet ondersteund in Azure SQL Database**   
**Categorie**: probleem   

**Beschrijvingen**   
Met gekoppelde servers kunnen de SQL Server data base-engine opdrachten uitvoeren op OLE DB gegevens bronnen buiten het exemplaar van SQL Server. 


**Advies**   
Azure SQL Database biedt geen ondersteuning voor de functionaliteit van de gekoppelde server. De volgende acties worden aanbevolen om de nood zaak van gekoppelde servers te elimineren: 
- Identificeer de afhankelijke gegevens sets van externe SQL-servers en overweeg deze te verplaatsen naar de data base die wordt gemigreerd. 
- Migreer de afhankelijke data base (s) naar Azure en gebruik de functionaliteit van Elastic Database query (preview) om een query uit te kunnen zetten in meerdere data bases in Azure SQL Database. 

Meer informatie: [Azure SQL database Elastic query controleren (preview)](../../database/elastic-query-overview.md) 

## <a name="ms-dtc"></a>MS DTC<a id="MSDTCTransactSQL"></a>

**Titel: BEGIN gedistribueerde trans actie wordt niet ondersteund in Azure SQL Database.**   
**Categorie**: probleem   

**Beschrijvingen**   
Gedistribueerde trans actie die is gestart door Transact SQL BEGIN gedistribueerde trans actie en beheerd door micro soft gedistribueerde transactie (MS DTC) wordt niet ondersteund in Azure SQL Database.  


**Advies**   
Bekijk de sectie betrokken objecten in Azure Migrate om alle objecten weer te geven met behulp van de BEGIN verspreide trans actie. U kunt de deel nemers-data bases migreren naar Azure SQL Managed instance waarbij gedistribueerde trans acties voor meerdere exemplaren worden ondersteund (momenteel als preview-versie). U kunt ook naar SQL Server migreren op de virtuele machine van Azure.

Meer informatie: [trans acties op meerdere servers voor Azure SQL Managed instance ](../../database/elastic-transactions-overview.md#transactions-across-multiple-servers-for-azure-sql-managed-instance)


## <a name="openrowset-bulk"></a>OPENROWSET (bulk)<a id="OpenRowsetWithNonBlobDataSourceBulk"></a>

**Titel: OPENROWSET die wordt gebruikt in bulk bewerking met niet-Azure Blob Storage-gegevens bron, wordt niet ondersteund in Azure SQL Database.**   
**Categorie**: probleem   

**Beschrijving** OPENROWSET ondersteunt bulk bewerkingen via een ingebouwde BULK provider waarmee gegevens uit een bestand kunnen worden gelezen en geretourneerd als een rijenset. OPENROWSET met niet-Azure Blob Storage-gegevens bron wordt niet ondersteund in Azure SQL Database.


**Advies**   
Azure SQL Database heeft geen toegang tot bestands shares en Windows-mappen, dus moeten de bestanden vanuit Azure Blob Storage worden geïmporteerd. Daarom wordt alleen een gegevens bron van het type BLOB ondersteund in de functie OPENROWSET. U kunt ook naar SQL Server migreren op de virtuele machine van Azure

Meer informatie: [verschillen in Transact-SQL oplossen tijdens de migratie naar SQL database](../../database/transact-sql-tsql-differences-sql-server.md#transact-sql-syntax-not-supported-in-azure-sql-database)


## <a name="openrowset-provider"></a>OPENROWSET (provider)<a id="OpenRowsetWithSQLAndNonSQLProvider"></a>

**Titel: OPENROWSET met SQL of een niet-SQL-provider wordt niet ondersteund in Azure SQL Database.**   
**Categorie**: probleem   

**Beschrijvingen**   
OPENROWSET met SQL of een niet-SQL-provider is een alternatief voor het openen van tabellen op een gekoppelde server en is een eenmalige, ad hoc methode om verbinding te maken met externe gegevens en deze te openen met behulp van OLE DB. OPENROWSET met SQL of een niet-SQL-provider wordt niet ondersteund in Azure SQL Database.


**Advies**   
Azure SQL Database ondersteunt OPENROWSET alleen om te importeren uit Azure Blob-opslag. U kunt ook naar SQL Server migreren op de virtuele machine van Azure

Meer informatie: [verschillen in Transact-SQL oplossen tijdens de migratie naar SQL database](../../database/transact-sql-tsql-differences-sql-server.md#transact-sql-syntax-not-supported-in-azure-sql-database)


## <a name="non-ansi-left-outer-join"></a>Niet-ANSI left outer join<a id="NonANSILeftOuterJoinSyntax"></a>

**Titel: niet-ANSI-stijl left outer join wordt stopgezet en is verwijderd.**   
**Categorie**: waarschuwing   

**Beschrijvingen**   
Niet-ANSI-stijl left outer join wordt stopgezet en is verwijderd in Azure SQL Database. 


**Advies**   
Gebruik de syntaxis van de ANSI-samen voeging.

Meer informatie: niet-uitgevoerde [Data base-engine functionaliteit in SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)


## <a name="non-ansi-right-outer-join"></a>Niet-ANSI right outer join<a id="NonANSIRightOuterJoinSyntax"></a>

**Titel: niet-ANSI-stijl right outer join wordt stopgezet en is verwijderd.**   
**Categorie**: waarschuwing   

**Beschrijvingen**   
Niet-ANSI-stijl right outer join wordt stopgezet en is verwijderd in Azure SQL Database. 


**Advies**   
Gebruik de syntaxis van de ANSI-samen voeging.

Meer informatie: niet-uitgevoerde [Data base-engine functionaliteit in SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="next-column"></a>Volgende kolom<a id="NextColumn"></a>

**Titel: de volgende tabellen en kolommen krijgen een fout in Azure SQL Database.**   
**Categorie**: probleem   

**Beschrijvingen**   
Er zijn tabellen of kolommen met de naam volgende gedetecteerd. Reeksen, geïntroduceerd in Microsoft SQL Server, gebruiken de ANSI-standaard functie NEXT VALUE FOR. Als de naam van een tabel of kolom wordt opgegeven en de kolom als waarde is opgegeven, en als de ANSI-standaard is wegge laten, kan de resulterende instructie een fout veroorzaken.  


**Advies**   
Instructies voor het opnieuw schrijven om het sleutel woord ANSI Standard op te laten bevatten bij het aliassen van een tabel of kolom. Wanneer bijvoorbeeld een kolom de naam volgende heeft en die kolom als een alias fungeert, resulteert de query `SELECT NEXT VALUE FROM TABLE` in een fout en moet deze worden herschreven als waarde selecteren in de tabel. En wanneer een tabel de naam volgende heeft en die tabel als een alias heeft, resulteert de query `SELECT Col1 FROM NEXT VALUE` in een fout en moet deze worden herschreven als `SELECT Col1 FROM NEXT AS VALUE` .

## <a name="raiserror"></a>RAISERROR<a id="RAISERROR"></a>

**Titel: verouderde Style-aanroepen moeten worden vervangen door moderne equivalenten.**   
**Categorie**: waarschuwing   

**Beschrijvingen**   
Elk van de onderstaande voor beelden, zoals in het onderstaande voor beeld, worden genoemd als verouderd, omdat deze niet de komma's en het haakje bevatten. `RAISERROR 50001 'this is a test'`. Deze methode voor het aanroepen van//////////of wordt verwijderd in Azure SQL Database. 


**Advies**   
Herschrijf de instructie met de huidige opdracht//syntaxis of beoordeel of de moderne aanpak van `BEGIN TRY { }  END TRY BEGIN CATCH {  THROW; } END CATCH` haalbaar is.

Meer informatie: niet-uitgevoerde [Data base-engine functionaliteit in SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="server-audits"></a>Server controles<a id="ServerAudits"></a>

**Titel: gebruik Azure SQL Database controle functies om server controles te vervangen**   
**Categorie**: waarschuwing   

**Beschrijvingen**   
Server audits wordt niet ondersteund in Azure SQL Database.


**Advies**   
Overweeg de controle functies Azure SQL Database om server controles te vervangen.  Azure SQL ondersteunt controle en de functies zijn uitgebreider dan SQL Server. Azure SQL database kan verschillende database acties en-gebeurtenissen controleren, waaronder: toegang tot gegevens, schema wijzigingen (DDL), gegevens wijzigingen (DML), accounts, rollen en machtigingen (DCL, beveiligings uitzonderingen. Met Azure SQL Database controle wordt de mogelijkheid van een organisatie uitgebreid om diep inzicht te krijgen in gebeurtenissen en wijzigingen die zich voordoen in hun data base, inclusief updates en query's voor de gegevens. U kunt ook migreren naar een beheerd exemplaar van Azure SQL of SQL Server op de virtuele machine van Azure.

Meer informatie: [controleren op Azure SQL database ](../../database/auditing-overview.md)

## <a name="server-credentials"></a>Server referenties<a id="ServerCredentials"></a>

**Titel: Server bereik referentie wordt niet ondersteund in Azure SQL Database**   
**Categorie**: waarschuwing   

**Beschrijvingen**   
Een referentie is een record met de verificatie gegevens (referenties) die vereist zijn om verbinding te maken met een resource buiten SQL Server. Azure SQL Database biedt ondersteuning voor database referenties, maar niet voor de data bases die zijn gemaakt in het SQL Server bereik.   


**Advies**   
Azure SQL Database ondersteunt data base-Scope referenties. De referenties van het server bereik converteren naar data base-bereik referenties. U kunt ook migreren naar een beheerd exemplaar van Azure SQL of SQL Server op de virtuele machine van Azure

Meer informatie: [referentie data base-scope maken](/sql/t-sql/statements/create-database-scoped-credential-transact-sql)

## <a name="service-broker"></a>Service Broker<a id="ServiceBroker"></a>

**Titel: Service Broker-functie wordt niet ondersteund in Azure SQL Database**   
**Categorie**: probleem   

**Beschrijvingen**   
SQL Server Service Broker biedt systeem eigen ondersteuning voor Messa ging-en Queuing-toepassingen in de SQL Server data base-engine. Service Broker-functie wordt niet ondersteund in Azure SQL Database.


**Advies**   
Service Broker-functie wordt niet ondersteund in Azure SQL Database. Overweeg om te migreren naar een beheerd exemplaar van Azure SQL dat Service Broker binnen hetzelfde exemplaar ondersteunt. U kunt ook naar SQL Server migreren op de virtuele machine van Azure. 

## <a name="server-scoped-triggers"></a>Triggers op server bereik<a id="ServerScopedTriggers"></a>

**Titel: de trigger voor een server bereik wordt niet ondersteund in Azure SQL Database**   
**Categorie**: waarschuwing   

**Beschrijvingen**   
Een trigger is een speciaal soort opgeslagen procedure die wordt uitgevoerd als reactie op bepaalde acties in een tabel zoals invoegen, verwijderen of bijwerken van gegevens. Triggers met een server bereik worden niet ondersteund in Azure SQL Database. Azure SQL Database biedt geen ondersteuning voor de volgende opties voor triggers: voor aanmelding, VERSLEUTELING, met APPEND, niet voor replicatie, optie voor externe naam (er is geen ondersteuning voor externe methoden), alle SERVER opties (DDL-trigger), trigger voor een AANMELDINGS gebeurtenis (aanmeldings trigger), Azure SQL Database ondersteunt geen CLR-triggers.


**Advies**   
Gebruik in plaats daarvan de trigger data base-niveau. U kunt ook migreren naar een beheerd exemplaar van Azure SQL of SQL Server op de virtuele machine van Azure

Meer informatie: [verschillen in Transact-SQL oplossen tijdens de migratie naar SQL database](../../database/transact-sql-tsql-differences-sql-server.md#transact-sql-syntax-not-supported-in-azure-sql-database)


## <a name="sql-agent-jobs"></a>SQL-Agent taken<a id="AgentJobs"></a>

**Titel: SQL Server Agent-taken zijn niet beschikbaar in Azure SQL Database**   
**Categorie**: waarschuwing   

**Beschrijvingen**   
SQL Server Agent is een micro soft Windows-service die geplande beheer taken uitvoert, die taken worden genoemd in SQL Server. SQL Server Agent-taken zijn niet beschikbaar in Azure SQL Database. 


**Aanbeveling** Gebruik elastische taken (preview), die de vervanging zijn voor SQL Server Agent taken in Azure SQL Database. Met taak voor Elastic Database voor Azure SQL Database kunt u op betrouw bare wijze T-SQL-scripts uitvoeren die meerdere data bases omvatten tijdens het automatisch opnieuw proberen en het leveren van mogelijke voltooiings garanties. U kunt eventueel ook migreren naar Azure SQL Managed instance of SQL Server op Azure Virtual Machines.

Meer informatie: [aan de slag met taak voor Elastic database (preview) ](../../database/elastic-jobs-overview.md)

## <a name="sql-database-size"></a>SQL Database grootte<a id="SQLDBDatabaseSize"></a>

**Titel: Azure SQL Database ondersteunt de omvang van de data base niet groter dan 100 TB.**   
**Categorie**: probleem   

**Beschrijvingen**   
De grootte van de data base is groter dan de Maxi maal ondersteunde grootte van 100 TB. 


**Advies**   
Evalueren of de gegevens kunnen worden gearchiveerd of gecomprimeerd of Shard in meerdere data bases. U kunt ook naar SQL Server migreren op de virtuele machine van Azure. 

Meer informatie: [resource limieten voor vCore](../../database/resource-limits-vcore-single-databases.md) 

## <a name="sql-mail"></a>SQL mail<a id="SqlMail"></a>

**Titel: SQL mail is stopgezet.**   
**Categorie**: waarschuwing   

**Beschrijvingen**   
SQL mail is stopgezet en verwijderd in Azure SQL Database.


**Advies**   
U kunt migreren naar Azure SQL Managed instance of SQL Server op Azure Virtual Machines en Database Mail gebruiken.

Meer informatie: niet-uitgevoerde [Data base-engine functionaliteit in SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="systemprocedures110"></a>SystemProcedures110<a id="SystemProcedures110"></a>

**Title: gedetecteerde instructies die verwijzen naar verwijderde opgeslagen systeem procedures die niet beschikbaar zijn in Azure SQL Database.**   
**Categorie**: waarschuwing   

**Beschrijvingen**   
De volgende niet-ondersteunde systeem-en uitgebreide opgeslagen procedures kunnen niet worden gebruikt in Azure SQL database-,,,,, `sp_dboption` `sp_addserver` `sp_dropalias` `sp_activedirectory_obj` `sp_activedirectory_scp` , `sp_activedirectory_start` .


**Advies**    
Verwijder verwijzingen naar niet-ondersteunde systeem procedures die zijn verwijderd in Azure SQL Database.

Meer informatie: niet-uitgevoerde [Data base-engine functionaliteit in SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="trace-flags"></a>Tracerings vlaggen<a id="TraceFlags"></a>

**Titel: de Azure SQL Database ondersteunt geen tracerings vlaggen**   
**Categorie**: waarschuwing   

**Beschrijvingen**   
Tracerings vlaggen worden gebruikt om tijdelijk specifieke server kenmerken in te stellen of om een bepaald gedrag uit te scha kelen. Tracerings vlaggen worden vaak gebruikt voor het diagnosticeren van prestatie problemen of voor het opsporen van fouten in opgeslagen procedures of complexe computer systemen. Azure SQL Database biedt geen ondersteuning voor traceer vlaggen. 


**Advies**   
Bekijk de sectie betrokken objecten in Azure Migrate om alle tracerings vlaggen weer te geven die niet worden ondersteund in Azure SQL Database en om te evalueren of ze kunnen worden verwijderd. U kunt ook migreren naar een beheerd exemplaar van Azure SQL dat een beperkt aantal globale traceer vlaggen of SQL Server op een virtuele Azure-machine ondersteunt.

Meer informatie: [verschillen in Transact-SQL oplossen tijdens de migratie naar SQL database](../../database/transact-sql-tsql-differences-sql-server.md#transact-sql-syntax-not-supported-in-azure-sql-database)


## <a name="windows-authentication"></a>Windows-verificatie<a id="WindowsAuthentication"></a>

**Titel: database gebruikers die zijn toegewezen met Windows-verificatie (geïntegreerde beveiliging), worden niet ondersteund in Azure SQL Database.**   
**Categorie**: waarschuwing   

**Beschrijvingen**   
Azure SQL Database ondersteunt twee typen verificatie 
- SQL-verificatie: maakt gebruik van een gebruikers naam en wacht woord 
- Azure Active Directory-verificatie: gebruikt identiteiten die worden beheerd door Azure Active Directory en wordt ondersteund voor beheerde en geïntegreerde domeinen. 

Database gebruikers die zijn toegewezen met Windows-verificatie (geïntegreerde beveiliging), worden niet ondersteund in Azure SQL Database. 



**Advies**   
De lokale Active Directory verbrengen met Azure Active Directory. De Windows-identiteit kan vervolgens worden vervangen door de equivalente Azure Active Directory-identiteiten. U kunt ook naar SQL Server migreren op de virtuele machine van Azure.

Meer informatie: [SQL database beveiligings mogelijkheden](../../database/security-overview.md#authentication)

## <a name="xp_cmdshell"></a>XP_cmdshell<a id="XpCmdshell"></a>

**Titel: xp_cmdshell wordt niet ondersteund in Azure SQL Database.**   
**Categorie**: probleem   

**Beschrijvingen**   
xp_cmdshell waarmee een Windows-opdracht shell wordt gestart en wordt door gegeven in een teken reeks voor uitvoering, wordt niet ondersteund in Azure SQL Database. 


**Advies**   
De sectie betrokken objecten bekijken in Azure Migrate om alle objecten te zien met xp_cmdshell en te evalueren of de verwijzing naar xp_cmdshell of het betrokken object kan worden verwijderd. Overweeg ook om Azure Automation te verkennen die in de cloud gebaseerde Automation-en configuratie service levert. U kunt ook naar SQL Server migreren op de virtuele machine van Azure. 

## <a name="next-steps"></a>Volgende stappen

Als u uw SQL Server wilt migreren naar Azure SQL Database, raadpleegt [u de SQL Server voor SQL database migratie handleiding](sql-server-to-sql-database-guide.md).

- Zie [service en hulpprogram ma's voor gegevens migratie](../../../dms/dms-tools-matrix.md)voor een matrix van de services en hulpprogram Ma's van micro soft en van derden die beschikbaar zijn om u te helpen bij verschillende scenario's voor data base-en gegevens migratie, en voor speciale taken.

- Voor meer informatie over SQL Database raadpleegt u:
   - [Overzicht van Azure SQL Database](../../database/sql-database-paas-overview.md)
   - [Reken machine totale eigendoms kosten Azure](https://azure.microsoft.com/pricing/tco/calculator/) 

- Zie voor meer informatie over het Framework en de acceptatie cyclus voor Cloud migraties.
   -  [Cloud Adoption Framework voor Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Aanbevolen procedures voor het migreren en aanpassen van werk belastingen naar Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 


- Zie [Data Access Migration Toolkit (preview) voor informatie](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) over het beoordelen van de toegangs laag van de toepassing.
- Zie [database Experimentation Assistant](/sql/dea/database-experimentation-assistant-overview)voor meer informatie over het uitvoeren van een test voor de toegang tot een Data Access Layer A/B.
