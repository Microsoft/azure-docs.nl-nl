---
title: Beoordelings regels voor het SQL Server van de migratie van SQL-beheerde exemplaren
description: Beoordelings regels voor het identificeren van problemen met het bron SQL Server exemplaar dat moet worden geadresseerd voordat de migratie naar Azure SQL Managed instance kan worden gemigreerd.
ms.service: sql-managed-instance
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: MashaMSFT
ms.author: mathoma
ms.reviewer: MashaMSFT
ms.date: 12/15/2020
ms.openlocfilehash: 760a6496ff297ae6328810589f780b430d55b18a
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102054570"
---
# <a name="assessment-rules-for-sql-server-to-sql-managed-instance-migration"></a>Beoordelings regels voor het SQL Server van de migratie van SQL-beheerde exemplaren
[!INCLUDE[appliesto--sqldb](../../includes/appliesto-sqldb.md)]

Migratie hulpprogramma's valideren uw bron SQL Server-exemplaar door een aantal beoordelings regels uit te voeren voor het identificeren van problemen die moeten worden opgelost voordat u uw SQL Server-Data Base naar Azure SQL Managed Instance kunt migreren. 

In dit artikel vindt u een lijst met de regels die worden gebruikt om de uitvoer baarheid van het migreren van uw SQL Server-Data Base naar Azure SQL Managed instance te evalueren. 

## <a name="analysiscommand-job"></a>AnalysisCommand-taak<a id="AnalysisCommandJob"></a>

**Titel: taak stap AnalysisCommand wordt niet ondersteund in Azure SQL Managed instance.**   
**Categorie**: waarschuwing   


**Beschrijvingen**   
Het is een taak stap die een Analysis Services opdracht uitvoert. De taak stap AnalysisCommand wordt niet ondersteund in Azure SQL Managed instance.

**Advies**     
Bekijk de sectie betrokken objecten in Azure Migrate om alle taken weer te geven met behulp van de taak stap van de Analysis service-opdracht en te evalueren of de taak stap of het betreffende object kan worden verwijderd. U kunt ook naar SQL Server migreren op de virtuele machine van Azure.

Meer informatie: [SQL Server Agent verschillen in Azure SQL Managed instance ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent)

## <a name="analysisquery-job"></a>AnalysisQuery-taak<a id="AnalysisQueryJob"></a>

**Titel: taak stap AnalysisQuery wordt niet ondersteund in Azure SQL Managed instance.**   
**Categorie**: waarschuwing   

**Beschrijvingen**   
Het is een taak stap waarmee een Analysis Services query wordt uitgevoerd. De taak stap AnalysisQuery wordt niet ondersteund in Azure SQL Managed instance.


**Advies**   
Bekijk de sectie betrokken objecten in Azure Migrate om alle taken weer te geven met behulp van Analysis Service-query taak stap en te evalueren of de taak stap of het betrokken object kan worden verwijderd. U kunt ook naar SQL Server migreren op de virtuele machine van Azure.

Meer informatie: [SQL Server Agent verschillen in Azure SQL Managed instance ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent)


## <a name="assembly-from-file"></a>Assembly van bestand<a id="AssemblyFromFile"></a>

**Titel: ' CREATE ASSEMBLy ' en ' ALTER ASSEMBLy ' met een bestands parameter worden niet ondersteund in een beheerd exemplaar van Azure SQL.**   
**Categorie**: waarschuwing   

**Beschrijvingen**   
Azure SQL Managed instance heeft geen toegang tot bestands shares of Windows-mappen. Zie de sectie ' betrokken objecten ' voor het specifieke gebruik van BULK INSERT-instructies die niet verwijzen naar een Azure-Blob. Objecten met ' BULK INSERT ' waarbij de bron geen Azure Blob-opslag is, werken niet na de migratie naar Azure SQL Managed instance.


**Advies**   
U moet in plaats daarvan BULK INSERT-instructies converteren die gebruikmaken van lokale bestanden of bestands shares voor het gebruik van bestanden uit de Azure Blob-opslag, wanneer u migreert naar een beheerd exemplaar van Azure SQL. U kunt ook naar SQL Server migreren op de virtuele machine van Azure. 

Meer informatie: [CLR-verschillen in Azure SQL Managed instance ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#clr)

## <a name="bulk-insert"></a>Bulksgewijs invoeren<a id="BulkInsert"></a>

**Titel: BULK INSERT met niet-Azure Blob-gegevens bron wordt niet ondersteund in Azure SQL Managed instance.**   
**Categorie**: probleem   

**Beschrijvingen**   
Azure SQL Managed instance heeft geen toegang tot bestands shares of Windows-mappen. Zie de sectie ' betrokken objecten ' voor het specifieke gebruik van BULK INSERT-instructies die niet verwijzen naar een Azure-Blob. Objecten met ' BULK INSERT ' waarbij de bron geen Azure Blob-opslag is, werken niet na de migratie naar Azure SQL Managed instance.


**Advies**   
U moet in plaats daarvan BULK INSERT-instructies converteren die gebruikmaken van lokale bestanden of bestands shares voor het gebruik van bestanden uit de Azure Blob-opslag, wanneer u migreert naar een beheerd exemplaar van Azure SQL.

Meer informatie: [verschillen in bulksgewijze INSERT en OPENrowset in Azure SQL Managed instance ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#bulk-insert--openrowset)


## <a name="clr-security"></a>CLR-beveiliging<a id="ClrStrictSecurity"></a>

**Titel: CLR-assembly's die zijn gemarkeerd als veilig of EXTERNAL_ACCESS worden beschouwd als onveilig**   
**Categorie**: probleem   

**Beschrijvingen**   
De CLR-beveiligings modus wordt afgedwongen in een door Azure SQL beheerd exemplaar. Deze modus is standaard ingeschakeld en introduceert wijzigingen in data bases die door de gebruiker gedefinieerde CLR-assembly's met een veilige of EXTERNAL_ACCESS zijn gemarkeerd.


**Advies**   
CLR maakt gebruik van code toegangs beveiliging (CAS) in de .NET Framework, die niet meer wordt ondersteund als een beveiligings grens. Vanaf SQL Server 2017 (14. x) data base-engine `sp_configure` is een optie met de naam CLR strict Security geïntroduceerd om de beveiliging van CLR-assembly's te verbeteren. CLR strict-beveiliging is standaard ingeschakeld en behandelt veilige en EXTERNAL_ACCESS CLR-assembly's alsof ze zijn gemarkeerd als onveilig. Als CLR-strikte beveiliging is uitgeschakeld, kan een CLR-assembly die is gemaakt met PERMISSION_SET = veilig toegang krijgen tot externe systeem bronnen, niet-beheerde code aanroepen en sysadmin-bevoegdheden verkrijgen. Na het inschakelen van strikte beveiliging kunnen assembly's die niet zijn ondertekend, niet worden geladen. Als een Data Base ook veilige of EXTERNAL_ACCESS assembly's bevat, kunnen Restore-of ATTACH-data base-instructies worden voltooid, maar de assembly's kunnen niet worden geladen. Als u de assembly's wilt laden, moet u elke assembly wijzigen of verwijderen en opnieuw maken, zodat deze is ondertekend met een certificaat of asymmetrische sleutel met een bijbehorende aanmelding met de machtiging onveilige ASSEMBLy op de server.

Meer informatie: [CLR strict Security](/sql/database-engine/configure-windows/clr-strict-security)

## <a name="compute-clause"></a>COMPUTE-component<a id="ComputeClause"></a>

**Titel: COMPUTe-component is uit gebruik genomen en is verwijderd.**   
**Categorie**: waarschuwing   

**Beschrijvingen**   
De COMPUTe-component genereert totalen die worden weer gegeven als aanvullende samenvattings kolommen aan het einde van de resultatenset. Deze component wordt echter niet meer ondersteund in Azure SQL Managed instance.



**Advies**   
De T-SQL-module moet in plaats daarvan worden herschreven met behulp van de operator ROLLUP. De volgende code laat zien hoe COMPUTe kan worden vervangen met ROLLUP: 

```sql
USE AdventureWorks GO;  

SELECT SalesOrderID, UnitPrice, UnitPriceDiscount 
FROM Sales.SalesOrderDetail 
ORDER BY SalesOrderID COMPUTE SUM(UnitPrice), SUM(UnitPriceDiscount) 
BY SalesOrderID GO; 

SELECT SalesOrderID, UnitPrice, UnitPriceDiscount,SUM(UnitPrice) as UnitPrice , 
SUM(UnitPriceDiscount) as UnitPriceDiscount 
FROM Sales.SalesOrderDetail 
GROUP BY SalesOrderID, UnitPrice, UnitPriceDiscount WITH ROLLUP;
```

Meer informatie: niet-uitgevoerde [Data base-engine functionaliteit in SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="cryptographic-provider"></a>Cryptografische provider<a id="CryptographicProvider"></a>

**Titel: er is een gebruik van CREATE CRYPTOGRAPHIC PROVIDER of ALTER CRYPTOGRAPHIC PROVIDER gevonden. dit wordt niet ondersteund in Azure SQL Managed instance.**   
**Categorie**: probleem   

**Beschrijvingen**   
Azure SQL Managed instance biedt geen ondersteuning voor CRYPTOGRAFISCHe PROVIDER-instructies omdat het geen toegang heeft tot bestanden. Zie de sectie betrokken objecten voor het specifieke gebruik van CRYPTOGRAFISCHe PROVIDER-instructies. Objecten met ' cryptografie PROVIDER maken ' of ' ALTER CRYPTOGRAPHIC PROVIDER ' werken niet goed na de migratie naar Azure SQL Managed instance.


**Advies**   
Bekijk objecten met ' cryptografie PROVIDER maken ' of ' ALTER CRYPTOGRAPHIC PROVIDER '. Verwijder in dergelijke objecten die vereist zijn het gebruik van deze functies. U kunt ook naar SQL Server migreren op de virtuele machine van Azure. 

Meer informatie: [verschillen van cryptografische providers in Azure SQL Managed instance ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#cryptographic-providers)

## <a name="database-compatibility"></a>Database compatibiliteit<a id="DbCompatLevelLowerThan100"></a>

**Titel: het database compatibiliteits niveau onder 100 wordt niet ondersteund**   
**Categorie**: waarschuwing   

**Beschrijvingen**   
Het database compatibiliteits niveau is een waardevol hulp middel om de data base-engine te helpen bij het uitvoeren van een upgrade, waarbij SQL Server het bijwerken van de functionaliteit van de toepassings status wordt gehandhaafd door hetzelfde compatibiliteits niveau van de data base vooraf te gebruiken. Een door Azure SQL beheerd exemplaar ondersteunt geen compatibiliteits niveaus onder 100. Wanneer de data base met een compatibiliteits niveau van minder dan 100 wordt hersteld op een Azure SQL Managed instance, wordt het compatibiliteits niveau geüpgraded naar 100. 


**Aanbeveling**... Evalueren of de functionaliteit van de toepassing intact is wanneer het database compatibiliteits niveau is bijgewerkt naar 100 op Azure SQL Managed instance. U kunt ook naar SQL Server migreren op de virtuele machine van Azure.

Meer informatie: [ondersteunde compatibiliteits niveaus in Azure SQL Managed instance ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#compatibility-levels)

## <a name="database-principal-alias"></a>Data base-Principal-alias<a id="DatabasePrincipalAlias"></a>

**Titel: SYS. DATABASE_PRINCIPAL_ALIASES wordt stopgezet en is verwijderd.**   
**Categorie**: probleem   

**Beschrijvingen**   
Laden. DATABASE_PRINCIPAL_ALIASES wordt niet meer uitgevoerd en is verwijderd in een door Azure SQL beheerd exemplaar.


**Advies**   
Gebruik rollen in plaats van aliassen.

Meer informatie: niet-uitgevoerde [Data base-engine functionaliteit in SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="disable_def_cnst_chk-option"></a>DISABLE_DEF_CNST_CHK optie<a id="DisableDefCNSTCHK"></a>

**Titel: de optie DISABLE_DEF_CNST_CHK instellen is niet meer actief en is verwijderd.**   
**Categorie**: probleem   

**Beschrijvingen**   
SET Option DISABLE_DEF_CNST_CHK wordt stopgezet en is verwijderd in Azure SQL Managed instance.


Meer informatie: niet-uitgevoerde [Data base-engine functionaliteit in SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="fastfirstrow-hint"></a>Hint FASTFIRSTROW<a id="FastFirstRowHint"></a>

**Titel: de FASTFIRSTROW-query Hint is uit gebruik genomen en is verwijderd.**   
**Categorie**: waarschuwing   

**Beschrijvingen**   
De FASTFIRSTROW-query Hint is niet meer actief en is verwijderd in een door Azure SQL beheerd exemplaar.


**Advies**   
In plaats van de optie FASTFIRSTROW query Hint gebruiken (FAST n).

Meer informatie: niet-uitgevoerde [Data base-engine functionaliteit in SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="filestream"></a>-<a id="FileStream"></a>

**Titel: FileStream en bestands tabel worden niet ondersteund in Azure SQL Managed instance.**   
**Categorie**: probleem   

**Beschrijvingen**   
De FileStream-functie, waarmee u ongestructureerde gegevens kunt opslaan, zoals tekst documenten, afbeeldingen en Video's in het NTFS-bestands systeem, wordt niet ondersteund in een door Azure SQL beheerd exemplaar. **Deze data base kan niet worden gemigreerd omdat de back-up met FileStream-bestands groepen niet kan worden hersteld op een door Azure SQL beheerd exemplaar.**


**Advies**   
Upload de ongestructureerde bestanden naar Azure Blob-opslag en sla meta gegevens met betrekking tot deze bestanden (naam, type, URL-locatie, opslag sleutel enz.) op in Azure SQL Managed instance. Mogelijk moet u uw toepassing opnieuw inbouwen om streaming-blobs van en naar Azure SQL Managed instance in te scha kelen. U kunt ook naar SQL Server migreren op de virtuele machine van Azure.

Meer informatie: [streamen van blobs naar en van SQL Azure blog](https://azure.microsoft.com/en-in/blog/streaming-blobs-to-and-from-sql-azure/)

## <a name="heterogeneous-ms-dtc"></a>Heterogene MS DTC<a id="MIHeterogeneousMSDTCTransactSQL"></a>

**Titel: BEGIN gedistribueerde trans actie met niet-SQL Server externe server wordt niet ondersteund in Azure SQL Managed instance.**   
**Categorie**: probleem   

**Beschrijvingen**   
Gedistribueerde trans actie die is gestart door Transact SQL BEGIN gedistribueerde trans actie en beheerd door micro soft gedistribueerde transactie (MS DTC) wordt niet ondersteund in Azure SQL Managed instance als de externe server niet SQL Server is. 


**Advies**   
Bekijk de sectie betrokken objecten in Azure Migrate om alle objecten weer te geven met behulp van de BEGIN verspreide trans actie. U kunt de deel nemers-data bases migreren naar Azure SQL Managed instance waarbij gedistribueerde trans acties voor meerdere exemplaren worden ondersteund (momenteel als preview-versie). U kunt ook naar SQL Server migreren op de virtuele machine van Azure.

Meer informatie: [trans acties op meerdere servers voor Azure SQL Managed instance ](../../database/elastic-transactions-overview.md#transactions-across-multiple-servers-for-azure-sql-managed-instance)

## <a name="homogenous-ms-dtc"></a>Homogene MS DTC<a id="MIHomogeneousMSDTCTransactSQL"></a>

**Titel: BEGIN gedistribueerde trans actie wordt ondersteund op meerdere servers voor Azure SQL Managed instance.**   
**Categorie**: probleem   

**Beschrijvingen**   
Gedistribueerde trans actie die is gestart door Transact SQL BEGIN gedistribueerde trans actie en beheerd door micro soft gedistribueerde transactie (MS DTC) wordt ondersteund op meerdere servers voor Azure SQL Managed instance.


**Advies**   
Bekijk de sectie betrokken objecten in Azure Migrate om alle objecten weer te geven met behulp van de BEGIN verspreide trans actie. U kunt de deel nemers-data bases migreren naar Azure SQL Managed instance waarbij gedistribueerde trans acties voor meerdere exemplaren worden ondersteund (momenteel als preview-versie). U kunt ook naar SQL Server migreren op de virtuele machine van Azure. 

Meer informatie: [trans acties op meerdere servers voor Azure SQL Managed instance](../../database/elastic-transactions-overview.md#transactions-across-multiple-servers-for-azure-sql-managed-instance)


## <a name="linked-server-non-sql-provider"></a>Gekoppelde server (niet-SQL-provider)<a id="LinkedServerWithNonSQLProvider"></a>

**Titel: gekoppelde server met niet-SQL Server provider wordt niet ondersteund in Azure SQL Managed instance.**   
**Categorie**: probleem   

**Beschrijvingen**   
Met gekoppelde servers kunnen de SQL Server data base-engine opdrachten uitvoeren op OLE DB gegevens bronnen buiten het exemplaar van SQL Server. Een gekoppelde server met een niet-SQL Server provider wordt niet ondersteund in het beheerde exemplaar van Azure SQL. 


**Advies**   
Azure SQL Managed instance biedt geen ondersteuning voor de functionaliteit van de gekoppelde server als de externe server provider niet SQL Server is, zoals Oracle, Sybase, enzovoort. 

De volgende acties worden aanbevolen om de nood zaak van gekoppelde servers te elimineren: 
- Identificeer de afhankelijke data base (s) van externe niet-SQL-servers en overweeg deze te verplaatsen naar de data base die wordt gemigreerd. 
- Migreer de afhankelijke data base (s) naar ondersteunde doelen zoals SQL Managed instance, SQL Database, Azure Synapse SQL en SQL Server instances. 
- Overweeg om een gekoppelde server te maken tussen Azure SQL Managed instance en SQL Server op de virtuele machine van Azure (SQL-VM).  Vervolgens maakt u vanuit SQL-VM een gekoppelde server naar Oracle, Sybase enz. Deze aanpak heeft twee hops, maar kan worden gebruikt als tijdelijke oplossing.  
- U kunt ook naar SQL Server migreren op de virtuele machine van Azure.

Meer informatie: [verschillen in gekoppelde servers in Azure SQL Managed instance](../../managed-instance/transact-sql-tsql-differences-sql-server.md#linked-servers)

## <a name="merge-job"></a>Samenvoeg taak<a id="MergeJob"></a>

**Titel: de stap voor het samen voegen van een taak wordt niet ondersteund in een door Azure SQL beheerd exemplaar.**   
**Categorie**: waarschuwing   

**Beschrijvingen**   
Het is een taak stap die de replicatie samenvoegings agent activeert. De replicatie merge agent is een uitvoerbaar hulp programma dat de eerste moment opname in de database tabellen toepast op de abonnees. Daarnaast worden incrementele gegevens wijzigingen die zich op de Publisher hebben voorgedaan nadat de eerste moment opname is gemaakt, samengevoegd en worden conflicten afgestemd op basis van de regels die u configureert of gebruikmaakt van een aangepaste resolver die u hebt gemaakt. De stap voor het samen voegen van een taak wordt niet ondersteund in een door Azure SQL beheerd exemplaar.


**Advies**   
De sectie betrokken objecten bekijken in Azure Migrate om alle taken weer te geven met de stap samenvoeg taak en te evalueren of de taak stap of het betreffende object kan worden verwijderd. U kunt ook naar SQL Server migreren op de virtuele machine van Azure

Meer informatie: [SQL Server Agent verschillen in Azure SQL Managed instance ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent)


## <a name="mi-database-size"></a>MI-database grootte<a id="MIDatabaseSize<"></a>

**Titel: Azure SQL Managed instance biedt geen ondersteuning voor een Data Base van meer dan 8 TB.**   
**Categorie**: probleem   

**Beschrijvingen**   
De grootte van de data base is groter dan de maximum gereserveerde exemplaar opslag. **Deze data base kan niet worden geselecteerd voor migratie omdat de grootte de toegestane limiet overschrijdt.**


**Advies**   
Evalueren of de gegevens gecomprimeerd of Shard kunnen worden gearchiveerd in meerdere data bases. U kunt ook naar SQL Server migreren op de virtuele machine van Azure.

Meer informatie: [kenmerken van de hardware-generatie van Azure SQL Managed instance ](../../managed-instance/resource-limits.md#hardware-generation-characteristics)



## <a name="mi-instance-size"></a>MI-exemplaar grootte<a id="MIInstanceSize<"></a>

**Titel: de maximale opslag grootte voor exemplaren in een Azure SQL Managed instance mag niet groter zijn dan 8 TB.**   
**Categorie**: waarschuwing   

**Beschrijvingen**   
De grootte van alle data bases is groter dan de maximale gereserveerde instantie opslag.  


**Advies**   
U kunt de data bases migreren naar verschillende Azure SQL Managed instances of naar SQL Server op de virtuele machine van Azure als alle data bases op hetzelfde exemplaar moeten bestaan. 

Meer informatie: [kenmerken van de hardware-generatie van Azure SQL Managed instance ](../../managed-instance/resource-limits.md#hardware-generation-characteristics)


## <a name="multiple-log-files"></a>Meerdere logboek bestanden<a id="MultipleLogFiles<"></a>

**Titel: Azure SQL Managed instance biedt geen ondersteuning voor meerdere logboek bestanden.**   
**Categorie**: probleem   

**Beschrijvingen**   
Met SQL Server kan een Data Base worden aangemeld bij meerdere bestanden. Deze data base heeft meerdere logboek bestanden die niet worden ondersteund in Azure SQL Managed instance. * * Deze data base kan niet worden gemigreerd omdat de back-up niet kan worden hersteld op een door Azure SQL beheerd exemplaar. 
**

**Advies**   
Azure SQL Managed instance ondersteunt slechts één logboek per data base. U moet alle logboek bestanden verwijderen voordat u deze data base naar Azure kunt migreren: 

```sql
ALTER DATABASE [database_name] REMOVE FILE [log_file_name]
```

Meer informatie: [niet-ondersteunde database opties in Azure SQL Managed instance  ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#database-options)



## <a name="next-column"></a>Volgende kolom<a id="NextColumn"></a>

**Titel: de volgende tabellen en kolommen krijgen een fout in het beheerde exemplaar van Azure SQL.**   
**Categorie**: probleem   

**Beschrijvingen**   
Er zijn tabellen of kolommen met de naam volgende gedetecteerd. Reeksen, geïntroduceerd in Microsoft SQL Server, gebruiken de ANSI-standaard functie NEXT VALUE FOR. Als de naam van een tabel of kolom wordt opgegeven en de kolom als waarde is opgegeven, en als de ANSI-standaard is wegge laten, kan de resulterende instructie een fout veroorzaken.


**Advies**   
Instructies voor het opnieuw schrijven om het sleutel woord ANSI Standard op te laten bevatten bij het aliassen van een tabel of kolom. Als bijvoorbeeld een kolom de naam volgende heeft en die kolom als waarde is opgegeven, resulteert de query volgende waarde uit tabel in een fout en moet deze worden herschreven als waarde selecteren in de tabel. Op dezelfde manier, wanneer een tabel de naam volgende heeft en die tabel een alias heeft als waarde, wordt in de query Kol1 van de volgende waarde een fout veroorzaakt en moet deze worden herschreven als de optie Kol1 van volgende als waarde selecteren.



## <a name="non-ansi-style-left-outer-join"></a>Niet-ANSI-stijl left outer join<a id="NonANSILeftOuterJoinSyntax"></a>

**Titel: niet-ANSI-stijl left outer join wordt stopgezet en is verwijderd.**   
**Categorie**: waarschuwing   

**Beschrijvingen**   
Niet-ANSI-stijl left outer join wordt stopgezet en is verwijderd in een door Azure SQL beheerd exemplaar. 


**Advies**   
Gebruik de syntaxis van de ANSI-samen voeging.

Meer informatie: niet-uitgevoerde [Data base-engine functionaliteit in SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="non-ansi-style-right-outer-join"></a>Niet-ANSI-stijl right outer join<a id="NonANSIRightOuterJoinSyntax"></a>

**Titel: niet-ANSI-stijl right outer join wordt stopgezet en is verwijderd.**   
**Categorie**: waarschuwing   

**Beschrijvingen**   
Niet-ANSI-stijl right outer join wordt stopgezet en is verwijderd in een door Azure SQL beheerd exemplaar. 



Meer informatie: niet-uitgevoerde [Data base-engine functionaliteit in SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

**Advies**   
Gebruik de syntaxis van de ANSI-samen voeging.

## <a name="databases-exceed-100"></a>Data bases van meer dan 100 <a id="NumDbExceeds100"></a>

**Titel: Azure SQL Managed instance ondersteunt Maxi maal 100 data bases per exemplaar.**   
**Categorie**: waarschuwing   

**Beschrijvingen**   
Het maximum aantal data bases dat wordt ondersteund in Azure SQL Managed instance is 100, tenzij de opslag limiet voor het exemplaar is bereikt.



**Advies**   
U kunt de data bases migreren naar verschillende Azure SQL Managed instances of naar SQL Server op de virtuele machine van Azure als alle data bases op hetzelfde exemplaar moeten bestaan. 

Meer informatie: [resource limieten voor Azure SQL Managed instance ](../../managed-instance/resource-limits.md#service-tier-characteristics)

## <a name="openrowset-non-blob-data-source"></a>OPENROWSET (niet-BLOB-gegevens bron)<a id="OpenRowsetWithNonBlobDataSourceBulk"></a>

**Titel: OPENROWSET die wordt gebruikt in bulk bewerking met niet-Azure Blob Storage-gegevens bron, wordt niet ondersteund in Azure SQL Managed instance.**   
**Categorie**: probleem   

**Beschrijvingen**   
OPENROWSET ondersteunt bulk bewerkingen via een ingebouwde BULK provider waarmee gegevens uit een bestand kunnen worden gelezen en geretourneerd als een rijenset. OPENROWSET met niet-Azure Blob Storage-gegevens bron wordt niet ondersteund in Azure SQL Managed instance. 



**Advies**   
De functie OPENROWSET kan worden gebruikt om alleen query's uit te voeren op SQL Server instanties (beheerd, on-premises of in Virtual Machines). Alleen waarden voor SQLNCLI, SQLNCLI11 en SQLOLEDB worden ondersteund als provider. De aanbevolen actie is daarom het identificeren van de afhankelijke data base (s) van externe niet-SQL-servers en overweeg deze te verplaatsen naar de data base die wordt gemigreerd. U kunt ook naar SQL Server migreren op de virtuele machine van Azure

Meer informatie: [verschillen in bulksgewijze INSERT en OPENrowset in Azure SQL Managed instance ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#bulk-insert--openrowset)

## <a name="openrowset-non-sql-provider"></a>OPENROWSET (niet-SQL-provider)<a id="OpenRowsetWithNonSQLProvider"></a>

**Titel: OPENROWSET met niet-SQL-provider wordt niet ondersteund in Azure SQL Managed instance.**   
**Categorie**: probleem   

**Beschrijvingen**   
Deze methode is een alternatief voor het openen van tabellen op een gekoppelde server en is een eenmalige ad-hoc methode om verbinding te maken met externe gegevens en deze te openen met behulp van OLE DB. OPENROWSET met een niet-SQL-provider wordt niet ondersteund in een beheerd exemplaar van Azure SQL. 



**Advies**   
De functie OPENROWSET kan worden gebruikt om alleen query's uit te voeren op SQL Server instanties (beheerd, on-premises of in Virtual Machines). Alleen waarden voor SQLNCLI, SQLNCLI11 en SQLOLEDB worden ondersteund als provider. De aanbevolen actie is daarom het identificeren van de afhankelijke data base (s) van externe niet-SQL-servers en overweeg deze te verplaatsen naar de data base die wordt gemigreerd.

Meer informatie: [verschillen in bulksgewijze INSERT en OPENrowset in Azure SQL Managed instance ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#bulk-insert--openrowset)


## <a name="powershell-job"></a>Power shell-taak<a id="PowerShellJob"></a>

**Titel: Power shell-taak stap wordt niet ondersteund in Azure SQL Managed instance.**   
**Categorie**: waarschuwing   

**Beschrijvingen**   
Het is een taak stap die een Power shell-script uitvoert. Power shell-taak stap wordt niet ondersteund in Azure SQL Managed instance. 



**Advies**   
Bekijk de sectie betrokken objecten in Azure Migrate om alle taken weer te geven met behulp van Power shell-taak stap en te evalueren of de taak stap of het betreffende object kan worden verwijderd.  Evalueren of Azure Automation kunnen worden gebruikt. U kunt ook naar SQL Server migreren op de virtuele machine van Azure

Meer informatie: [SQL Server Agent verschillen in Azure SQL Managed instance ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent)

## <a name="queue-reader-job"></a>Taak voor wachtrij lezer<a id="QueueReaderJob"></a>

**Titel: de stap wachtrij lezer-taak wordt niet ondersteund in Azure SQL Managed instance.**   
**Categorie**: waarschuwing   

**Beschrijvingen**   
Het is een taak stap die de replicatie Queue Reader Agent activeert. De replicatie Queue Reader Agent is een uitvoerbaar bestand dat berichten leest die zijn opgeslagen in een Microsoft SQL Server wachtrij of een micro soft Message-wachtrij. vervolgens worden deze berichten toegepast op de Publisher. Queue Reader Agent wordt gebruikt met momentopname-en transactionele publicaties die het bijwerken in de wachtrij toestaan. De stap Queue Reader-taak wordt niet ondersteund in een door Azure SQL beheerd exemplaar.


**Advies**   
De sectie betrokken objecten bekijken in Azure Migrate om alle taken weer te geven met behulp van de stap wachtrij lezer-taak en te evalueren of de taak stap of het betrokken object kan worden verwijderd. U kunt ook naar SQL Server migreren op de virtuele machine van Azure.

Meer informatie: [SQL Server Agent verschillen in Azure SQL Managed instance ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent)


## <a name="raiserror"></a>RAISERROR<a id="RAISERROR"></a>

**Titel: verouderde Style-aanroepen moeten worden vervangen door moderne equivalenten.**   
**Categorie**: waarschuwing   

**Beschrijvingen**   
Elk van de onderstaande voor beelden, zoals in het onderstaande voor beeld, worden genoemd als verouderd, omdat deze niet de komma's en het haakje bevatten. Dit is een test. 50001 Deze methode voor het aanroepen van de naam van het proces wordt niet meer uitgevoerd en wordt verwijderd in een door Azure SQL beheerd exemplaar.



**Advies**   
Herschrijf de instructie met de huidige opdracht//syntaxis of beoordeel of de moderne aanpak van `BEGIN TRY { }  END TRY BEGIN CATCH {  THROW; } END CATCH` haalbaar is.

Meer informatie: niet-uitgevoerde [Data base-engine functionaliteit in SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="service-broker"></a>Service Broker<a id="ServiceBrokerWithNonLocalAddress"></a>

**Titel: Service Broker functie wordt gedeeltelijk ondersteund in Azure SQL Managed instance.**   
**Categorie**: probleem   

**Beschrijvingen**   
SQL Server Service Broker biedt systeem eigen ondersteuning voor Messa ging-en Queuing-toepassingen in de SQL Server data base-engine. Voor deze data base is Service Broker met meerdere exemplaren ingeschakeld. dit wordt niet ondersteund in een door Azure SQL beheerd exemplaar. 


**Advies**   
Azure SQL Managed instance biedt geen ondersteuning voor de Service Broker van meerdere exemplaren, d.w.z. Wanneer het adres niet lokaal is. U moet Service Broker uitschakelen met behulp van de volgende opdracht voordat u deze data base naar Azure migreert: `ALTER DATABASE [database_name] SET DISABLE_BROKER` ; Daarnaast moet u mogelijk ook het Service Broker-eind punt verwijderen of stoppen om te voor komen dat berichten in het SQL-exemplaar arriveren. Zodra de Data Base naar Azure is gemigreerd, kunt u in de Azure Service Bus-functionaliteit kijken om een Gene riek, op de cloud gebaseerd berichten systeem te implementeren in plaats van Service Broker. U kunt ook naar SQL Server migreren op de virtuele machine van Azure. 

Meer informatie: [Service Broker verschillen in Azure SQL Managed instance ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#service-broker)

## <a name="sql-mail"></a>SQL mail<a id="SqlMail"></a>

**Titel: SQL mail is stopgezet.**   
**Categorie**: waarschuwing   


**Beschrijvingen**   
SQL mail is stopgezet en verwijderd in een door Azure SQL beheerd exemplaar.



**Advies**   
Gebruik Database Mail.

Meer informatie: niet-uitgevoerde [Data base-engine functionaliteit in SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)


## <a name="systemprocedures110"></a>SystemProcedures110<a id="SystemProcedures110"></a>


**Title: gedetecteerde instructies die verwijzen naar verwijderde opgeslagen systeem procedures die niet beschikbaar zijn in Azure SQL Managed instance.**   
**Categorie**: waarschuwing   

**Beschrijvingen**   
De volgende niet-ondersteunde systeem-en uitgebreide opgeslagen procedures kunnen niet worden gebruikt in Azure SQL Managed instance-,,,, en `sp_dboption` `sp_addserver` `sp_dropalias` `sp_activedirectory_obj` `sp_activedirectory_scp` `sp_activedirectory_start` . 




**Advies**   
Verwijder verwijzingen naar niet-ondersteunde systeem procedures die zijn verwijderd in Azure SQL Managed instance.

Meer informatie: niet-uitgevoerde [Data base-engine functionaliteit in SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="transact-sql-job"></a>Transact-SQL-taak<a id="TransactSqlJob"></a>

**Titel: TSQL-taak stap bevat niet-ondersteunde opdrachten in Azure SQL Managed instance**   
**Categorie**: waarschuwing   


**Beschrijvingen**   
Het is een taak stap waarmee TSQL-scripts op het geplande tijdstip worden uitgevoerd. TSQL-taak stap bevat niet-ondersteunde opdrachten die niet worden ondersteund in Azure SQL Managed instance.



**Advies**   
Bekijk de sectie betrokken objecten in Azure Migrate om alle taken weer te geven die niet-ondersteunde opdrachten bevatten in Azure SQL Managed instance en evalueren of de taak stap of het betrokken object kan worden verwijderd. U kunt ook naar SQL Server migreren op de virtuele machine van Azure.

Meer informatie: [SQL Server Agent verschillen in Azure SQL Managed instance ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent)


## <a name="trace-flags"></a>Tracerings vlaggen<a id="TraceFlags"></a>

**Titel: tracerings vlaggen worden niet ondersteund in Azure SQL Managed instance gevonden**   
**Categorie**: waarschuwing   


**Beschrijvingen**   
Azure SQL Managed instance ondersteunt slechts een beperkt aantal globale tracerings vlaggen. Tracerings vlaggen voor sessies worden niet ondersteund.



**Advies**   
De sectie betrokken objecten bekijken in Azure Migrate om alle tracerings vlaggen weer te geven die niet worden ondersteund in Azure SQL Managed instance en evalueren of ze kunnen worden verwijderd. U kunt ook naar SQL Server migreren op de virtuele machine van Azure.

Meer informatie: [traceer vlaggen](/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql#trace-flags)


## <a name="windows-authentication"></a>Windows-verificatie<a id="WindowsAuthentication"></a>

**Titel: database gebruikers die zijn toegewezen met Windows-verificatie (geïntegreerde beveiliging) worden niet ondersteund in Azure SQL Managed instance**   
**Categorie**: waarschuwing   


**Beschrijvingen**   
Azure SQL Managed instance ondersteunt twee typen verificatie: 
- SQL-verificatie, waarbij een gebruikersnaam en wachtwoord worden gebruikt
- Azure Active Directory-verificatie, waarbij identiteiten worden gebruikt die worden beheerd door Azure Active Directory en wordt ondersteund voor beheerde en geïntegreerde domeinen. 

Database gebruikers die zijn toegewezen met Windows-verificatie (geïntegreerde beveiliging), worden niet ondersteund in Azure SQL Managed instance. 


**Advies**   
De lokale Active Directory verbrengen met Azure Active Directory. De Windows-identiteit kan vervolgens worden vervangen door de equivalente Azure Active Directory-identiteiten. U kunt ook naar SQL Server migreren op de virtuele machine van Azure.

Meer informatie: [beveiligings mogelijkheden van SQL Managed instance](../../database/security-overview.md#authentication)


## <a name="xp_cmdshell"></a>XP_cmdshell<a id="XpCmdshell"></a>

**Titel: xp_cmdshell wordt niet ondersteund in Azure SQL Managed instance.**   
**Categorie**: probleem   


**Beschrijvingen**   
Xp_cmdshell waarmee een Windows-opdracht shell wordt gestart en wordt door gegeven in een teken reeks voor uitvoering, wordt niet ondersteund in Azure SQL Managed instance. 



**Advies**   
De sectie betrokken objecten bekijken in Azure Migrate om alle objecten te zien met xp_cmdshell en te evalueren of de verwijzing naar xp_cmdshell of het betrokken object kan worden verwijderd. Overweeg het verkennen van Azure Automation die cloud-gebaseerde Automation-en configuratie service levert. U kunt ook naar SQL Server migreren op de virtuele machine van Azure.

Meer informatie: [verschillen in opgeslagen procedures in Azure SQL Managed instance](../../managed-instance/transact-sql-tsql-differences-sql-server.md#stored-procedures-functions-and-triggers)

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de [migratie handleiding voor het SQL Server van SQL Managed instance](sql-server-to-managed-instance-guide.md)om te beginnen met het migreren van uw SQL Server naar Azure SQL Managed instance.

- Zie [service en hulpprogram ma's voor gegevens migratie](../../../dms/dms-tools-matrix.md)voor een matrix van de services en hulpprogram Ma's van micro soft en van derden die beschikbaar zijn om u te helpen bij verschillende scenario's voor data base-en gegevens migratie, en voor speciale taken.

- Zie voor meer informatie over Azure SQL Managed instance:
   - [Service lagen in Azure SQL Managed instance](../../managed-instance/sql-managed-instance-paas-overview.md#service-tiers)
   - [Verschillen tussen SQL Server en Azure SQL Managed instance](../../managed-instance/transact-sql-tsql-differences-sql-server.md)
   - [Reken machine totale eigendoms kosten Azure](https://azure.microsoft.com/pricing/tco/calculator/) 


- Zie voor meer informatie over het Framework en de acceptatie cyclus voor Cloud migraties.
   -  [Cloud Adoption Framework voor Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Aanbevolen procedures voor het migreren en aanpassen van werk belastingen naar Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- Zie [Data Access Migration Toolkit (preview) voor informatie](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) over het beoordelen van de toegangs laag van de toepassing.
- Zie [database Experimentation Assistant](/sql/dea/database-experimentation-assistant-overview)voor meer informatie over het uitvoeren van een test voor de toegang tot een Data Access Layer A/B.