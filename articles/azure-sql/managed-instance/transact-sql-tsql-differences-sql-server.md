---
title: T-SQL-verschillen tussen SQL Server & Azure SQL Managed Instance
description: In dit artikel worden de Verschillen tussen Transact-SQL (T-SQL) Azure SQL Managed Instance en SQL Server.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.devlang: ''
ms.topic: reference
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, bonova, danil
ms.date: 3/16/2021
ms.custom: seoapril2019, sqldbrb=1
ms.openlocfilehash: f744b718919a6da75b2064efdc163ef4618b5a7c
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107815896"
---
# <a name="t-sql-differences-between-sql-server--azure-sql-managed-instance"></a>T-SQL-verschillen tussen SQL Server & Azure SQL Managed Instance
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

In dit artikel worden de verschillen in syntaxis en gedrag tussen Azure SQL Managed Instance en SQL Server. 


SQL Managed Instance biedt hoge compatibiliteit met de SQL Server-database-engine en de meeste functies worden ondersteund in een SQL Managed Instance.

![Eenvoudige migratie van SQL Server](./media/transact-sql-tsql-differences-sql-server/migration.png)

Er zijn enkele PaaS-beperkingen die zijn geïntroduceerd in SQL Managed Instance en enkele gedragswijzigingen in vergelijking met SQL Server. De verschillen zijn onderverdeeld in de volgende categorieën: <a name="Differences"></a>

- [Beschikbaarheid](#availability) omvat de verschillen in [Always On-beschikbaarheidsgroepen en](#always-on-availability-groups) [back-ups](#backup).
- [Beveiliging](#security) omvat de verschillen in [controle,](#auditing) [certificaten,](#certificates) [referenties,](#credential)cryptografische [providers,](#cryptographic-providers)aanmeldingen en [gebruikers,](#logins-and-users)en de [servicesleutel en de hoofdsleutel van de service.](#service-key-and-service-master-key)
- [Configuratie](#configuration) bevat de verschillen in [buffergroepextensie,](#buffer-pool-extension) [collatie,](#collation)compatibiliteitsniveaus, [](#compatibility-levels) [databasespiegeling,](#database-mirroring) [databaseopties,](#database-options) [SQL Server Agent](#sql-server-agent)en [tabelopties](#tables).
- [Functies](#functionalities) omvatten [BULK INSERT/OPENROWSET,](#bulk-insert--openrowset) [CLR](#clr), [DBCC,](#dbcc)gedistribueerde transacties [,](#distributed-transactions)uitgebreide gebeurtenissen [,](#extended-events)externe bibliotheken [,](#external-libraries)bestandsstroom en bestandstabel, volledige tekst Semantic [Search](#full-text-semantic-search), gekoppelde [servers](#linked-servers), [PolyBase](#polybase), [Replicatie](#replication), [RESTORE](#restore-statement), [Service Broker](#service-broker), opgeslagen [procedures, functies en triggers](#stored-procedures-functions-and-triggers). [](#filestream-and-filetable)
- [Omgevingsinstellingen,](#Environment) zoals VNets en subnetconfiguraties.

De meeste van deze functies zijn architectuurbeperkingen en vertegenwoordigen servicefuncties.

Tijdelijke bekende problemen die worden ontdekt in SQL Managed Instance en in de toekomst worden opgelost, worden beschreven op de [pagina met release-opmerkingen.](../database/doc-changes-updates-release-notes.md)

## <a name="availability"></a>Beschikbaarheid

### <a name="always-on-availability-groups"></a><a name="always-on-availability-groups"></a>AlwaysOn-beschikbaarheidsgroepen

[Hoge beschikbaarheid](../database/high-availability-sla.md) is ingebouwd in SQL Managed Instance en kan niet worden beheerd door gebruikers. De volgende instructies worden niet ondersteund:

- [EINDPUNT MAKEN... FOR DATABASE_MIRRORING](/sql/t-sql/statements/create-endpoint-transact-sql)
- [BESCHIKBAARHEIDSGROEP MAKEN](/sql/t-sql/statements/create-availability-group-transact-sql)
- [BESCHIKBAARHEIDSGROEP WIJZIGEN](/sql/t-sql/statements/alter-availability-group-transact-sql)
- [BESCHIKBAARHEIDSGROEP NEERZETTEN](/sql/t-sql/statements/drop-availability-group-transact-sql)
- De [SET HADR-component](/sql/t-sql/statements/alter-database-transact-sql-set-hadr) van de [ALTER DATABASE-instructie](/sql/t-sql/statements/alter-database-transact-sql)

### <a name="backup"></a>Backup

SQL Managed Instance automatische back-ups, zodat gebruikers volledige databaseback-ups `COPY_ONLY` kunnen maken. Differentiële back-ups, logboek- en bestandsmomentopnamen worden niet ondersteund.

- Met een SQL Managed Instance kunt u alleen een back-up maken van een exemplaardatabase naar een Azure Blob Storage-account:
  - Alleen `BACKUP TO URL` wordt ondersteund.
  - `FILE`, `TAPE` en back-upapparaten worden niet ondersteund.
- De meeste algemene `WITH` opties worden ondersteund.
  - `COPY_ONLY` is verplicht.
  - `FILE_SNAPSHOT` wordt niet ondersteund.
  - Tapeopties: `REWIND` `NOREWIND` , , en worden `UNLOAD` niet `NOUNLOAD` ondersteund.
  - Logboekspecifieke opties: `NORECOVERY` `STANDBY` , en worden niet `NO_TRUNCATE` ondersteund.

Beperkingen: 

- Met een SQL Managed Instance kunt u een back-up van een exemplaardatabase maken naar een back-up met maximaal 32 strepen, wat voldoende is voor databases van maximaal 4 TB als back-upcompressie wordt gebruikt.
- U kunt niet uitvoeren op een database die is versleuteld met `BACKUP DATABASE ... WITH COPY_ONLY` door de service beheerde Transparent Data Encryption (TDE). Door de service beheerde TDE dwingt u af dat back-ups worden versleuteld met een interne TDE-sleutel. De sleutel kan niet worden geëxporteerd, dus u kunt de back-up niet herstellen. Gebruik automatische back-ups en herstel naar een bepaald tijdstip of gebruik in plaats daarvan door de klant [beheerde TDE (BYOK).](../database/transparent-data-encryption-tde-overview.md#customer-managed-transparent-data-encryption---bring-your-own-key) U kunt versleuteling ook uitschakelen voor de database.
- Systeemeigen back-ups die op een beheerd exemplaar worden gemaakt, kunnen niet worden hersteld naar SQL Server. Dit komt doordat Managed Instance een hogere interne databaseversie heeft vergeleken met een versie van SQL Server.
- De maximale grootte van de back-upstree met behulp van de `BACKUP` opdracht in SQL Managed Instance is 195 GB, de maximale blobgrootte. Verhoog het aantal striping in de back-upopdracht om de grootte van de afzonderlijke stripe te verminderen en binnen deze limiet te blijven.

    > [!TIP]
    > Als u een back-up van een database wilt maken vanuit een SQL Server in een on-premises omgeving of op een virtuele machine, kunt u het volgende doen:
    >
    > - Back-up naar `DISK` in plaats van een back-up te maken naar `URL` .
    > - Upload de back-upbestanden naar Blob Storage.
    > - Herstel naar SQL Managed Instance.
    >
    > De opdracht in SQL Managed Instance grotere blobgrootten in de back-upbestanden omdat er een ander blobtype wordt gebruikt voor de opslag van de geüploade `Restore` back-upbestanden.

Zie BACKUP voor meer informatie over [](/sql/t-sql/statements/backup-transact-sql)back-ups met T-SQL.

## <a name="security"></a>Beveiliging

### <a name="auditing"></a>Controleren

De belangrijkste verschillen tussen controle in Microsoft Azure SQL en in SQL Server zijn:

- Met SQL Managed Instance werkt de controle op serverniveau. De `.xel` logboekbestanden worden opgeslagen in Azure Blob Storage.
- Met Azure SQL Database werkt de controle op databaseniveau. De `.xel` logboekbestanden worden opgeslagen in Azure Blob Storage.
- Met SQL Server, on-premises of virtuele machines werkt de controle op serverniveau. Gebeurtenissen worden opgeslagen in bestandssysteem- of Windows-gebeurtenislogboeken.
 
XEvent-controle in SQL Managed Instance ondersteuning voor Azure Blob Storage-doelen. Bestands- en Windows-logboeken worden niet ondersteund.

De belangrijkste verschillen in de `CREATE AUDIT` syntaxis voor controle naar Azure Blob Storage zijn:

- Er wordt een nieuwe syntaxis opgegeven die u kunt gebruiken om de URL op te geven van `TO URL` de Azure Blob Storage-container waarin `.xel` de bestanden worden geplaatst.
- De `TO FILE` syntaxis wordt niet ondersteund omdat SQL Managed Instance geen toegang heeft tot Windows-bestands shares.

Zie voor meer informatie: 

- [SERVERCONTROLE MAKEN](/sql/t-sql/statements/create-server-audit-transact-sql) 
- [ALTER SERVER AUDIT](/sql/t-sql/statements/alter-server-audit-transact-sql)
- [Controle](/sql/relational-databases/security/auditing/sql-server-audit-database-engine)

### <a name="certificates"></a>Certificaten

SQL Managed Instance geen toegang hebt tot bestands shares en Windows-mappen, gelden de volgende beperkingen:

- Het `CREATE FROM` / `BACKUP TO` bestand wordt niet ondersteund voor certificaten.
- Het `CREATE` / `BACKUP` certificaat van `FILE` / `ASSEMBLY` wordt niet ondersteund. Persoonlijke sleutelbestanden kunnen niet worden gebruikt. 

Zie CERTIFICAAT EN [BACK-UPCERTIFICAAT MAKEN.](/sql/t-sql/statements/backup-certificate-transact-sql) [](/sql/t-sql/statements/create-certificate-transact-sql) 
 
**Tijdelijke oplossing:** in plaats van een back-up van het certificaat te maken en de back-up te herstellen, moet u de binaire inhoud van het certificaat en de persoonlijke sleutel op te halen, opslaan als SQL-bestand en maken op basis van [binair](/sql/t-sql/functions/certencoded-transact-sql#b-copying-a-certificate-to-another-database):

```sql
CREATE CERTIFICATE  
   FROM BINARY = asn_encoded_certificate
WITH PRIVATE KEY (<private_key_options>)
```

### <a name="credential"></a>Referentie

Alleen Azure Key Vault en `SHARED ACCESS SIGNATURE` identiteiten worden ondersteund. Windows-gebruikers worden niet ondersteund.

Zie [CREATE CREDENTIAL](/sql/t-sql/statements/create-credential-transact-sql) en ALTER [CREDENTIAL](/sql/t-sql/statements/alter-credential-transact-sql).

### <a name="cryptographic-providers"></a>Cryptografische providers

SQL Managed Instance geen toegang tot bestanden, zodat cryptografische providers niet kunnen worden gemaakt:

- `CREATE CRYPTOGRAPHIC PROVIDER` wordt niet ondersteund. Zie [CREATE CRYPTOGRAPHIC PROVIDER (CRYPTOGRAFISCHE PROVIDER MAKEN).](/sql/t-sql/statements/create-cryptographic-provider-transact-sql)
- `ALTER CRYPTOGRAPHIC PROVIDER` wordt niet ondersteund. Zie [ALTER CRYPTOGRAPHIC PROVIDER (CRYPTOGRAFISCHE PROVIDER WIJZIGEN).](/sql/t-sql/statements/alter-cryptographic-provider-transact-sql)

### <a name="logins-and-users"></a>Aanmeldingen en gebruikers

- SQL-aanmeldingen die zijn gemaakt met `FROM CERTIFICATE` behulp van , en worden `FROM ASYMMETRIC KEY` `FROM SID` ondersteund. Zie [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql).
- Azure Active Directory (Azure AD) server-principals (aanmeldingen) die zijn gemaakt met de syntaxis [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current&preserve-view=true) of de syntaxis CREATE USER FROM [LOGIN [Azure AD Login]](/sql/t-sql/statements/create-user-transact-sql?view=azuresqldb-mi-current&preserve-view=true) worden ondersteund. Deze aanmeldingen worden gemaakt op serverniveau.

    SQL Managed Instance ondersteunt Azure AD-database-principals met de syntaxis `CREATE USER [AADUser/AAD group] FROM EXTERNAL PROVIDER` . Deze functie wordt ook wel azure AD-ingesloten databasegebruikers genoemd.

- Windows-aanmeldingen die zijn gemaakt met `CREATE LOGIN ... FROM WINDOWS` de syntaxis worden niet ondersteund. Gebruik Azure Active Directory aanmeldingen en gebruikers.
- De Azure AD-gebruiker die het exemplaar heeft gemaakt, heeft [onbeperkte beheerdersbevoegdheden.](../database/logins-create-manage.md)
- Gebruikers op Azure AD-databaseniveau die geen beheerder zijn, kunnen worden gemaakt met behulp van de `CREATE USER ... FROM EXTERNAL PROVIDER` syntaxis. Zie [CREATE USER ... VAN EXTERNE PROVIDER](../database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities).
- Azure AD-server-principals (aanmeldingen) ondersteunen SQL-functies binnen één SQL Managed Instance slechts. Functies waarvoor interactie tussen exemplaren is vereist, ongeacht of ze zich binnen dezelfde Azure AD-tenant of verschillende tenants, worden niet ondersteund voor Azure AD-gebruikers. Voorbeelden van dergelijke functies zijn:

  - Transactionele SQL-replicatie.
  - Server koppelen.

- Het instellen van een Azure AD-aanmelding die als database-eigenaar aan een Azure AD-groep is toegevoegd, wordt niet ondersteund.
- Imitatie van Principals op Azure AD-serverniveau met behulp van andere Azure AD-principals wordt ondersteund, zoals de [EXECUTE AS-component.](/sql/t-sql/statements/execute-as-transact-sql) EXECUTE AS-beperkingen zijn:

  - EXECUTE AS USER wordt niet ondersteund voor Azure AD-gebruikers wanneer de naam verschilt van de aanmeldingsnaam. Een voorbeeld hiervan is wanneer de gebruiker wordt gemaakt via de syntaxis CREATE USER [myAadUser] FROM LOGIN [] en imitatie wordt geprobeerd via john@contoso.com EXEC AS USER = _myAadUser_. Wanneer u een GEBRUIKER maakt **op** basis van een Azure AD-server-principal (aanmelding), geeft u de user_name op als dezelfde login_name van **LOGIN**.
  - Alleen de SQL Server (aanmeldingen) die deel uitmaken van de rol, kunnen de volgende bewerkingen uitvoeren die zijn gericht op `sysadmin` Azure AD-principals:

    - EXECUTE AS USER
    - EXECUTE AS LOGIN

  - Als u een gebruiker wilt imiteren met de EXECUTE AS-instructie, moet de gebruiker rechtstreeks worden toegesneden op de Azure AD-server-principal (aanmelding). Gebruikers die lid zijn van Azure AD-groepen die zijn ingesteld in Azure AD-server-principals, kunnen niet effectief worden imiteren met de execute AS-instructie, zelfs niet als de aanroeper over de imitatiemachtigingen beschikt voor de opgegeven gebruikersnaam.

- Database exporteren/importeren met behulp van bacpac-bestanden wordt ondersteund voor Azure AD-gebruikers in SQL Managed Instance met [behulp van SSMS V18.4](/sql/ssms/download-sql-server-management-studio-ssms)of hoger of [SQLPackage.exe](/sql/tools/sqlpackage-download).
  - De volgende configuraties worden ondersteund met behulp van het BACPAC-bestand van de database: 
    - Een database exporteren/importeren tussen verschillende beheerexeports binnen hetzelfde Azure AD-domein.
    - Exporteer een database vanuit SQL Managed Instance importeren naar SQL Database binnen hetzelfde Azure AD-domein. 
    - Exporteer een database vanuit SQL Database importeren naar SQL Managed Instance binnen hetzelfde Azure AD-domein.
    - Exporteer een database SQL Managed Instance importeren naar SQL Server (versie 2012 of hoger).
      - In deze configuratie worden alle Azure AD-gebruikers gemaakt als SQL Server database-principals (gebruikers) zonder aanmeldingen. Het type gebruikers wordt vermeld als `SQL` en is zichtbaar zoals in `SQL_USER` sys.database_principals). Hun machtigingen en rollen blijven in de SQL Server metagegevens van de database en kunnen worden gebruikt voor imitatie. Ze kunnen echter niet worden gebruikt voor toegang tot en aanmelding bij de SQL Server met hun referenties.

- Alleen de principal-aanmelding op serverniveau, die is gemaakt door het inrichtingsproces van SQL Managed Instance, leden van de serverrollen, zoals of , of andere aanmeldingen met de machtiging ALTER ANY LOGIN op serverniveau, kunnen `securityadmin` `sysadmin` Azure AD-server-principals (aanmeldingen) maken in de hoofddatabase voor SQL Managed Instance.
- Als de aanmelding een SQL-principal is, kunnen alleen aanmeldingen die deel uitmaken van de rol `sysadmin` de opdracht create gebruiken om aanmeldingen te maken voor een Azure AD-account.
- De Azure AD-aanmelding moet lid zijn van een Azure AD binnen dezelfde map die wordt gebruikt voor Azure SQL Managed Instance.
- Azure AD-server-principals (aanmeldingen) zijn zichtbaar in Objectverkenner vanaf SQL Server Management Studio 18.0 preview 5.
- Overlappende Azure AD-server-principals (aanmeldingen) met een Azure AD-beheerdersaccount zijn toegestaan. Azure AD-server-principals (aanmeldingen) hebben voorrang op de Azure AD-beheerder wanneer u de principal omkeert en machtigingen op de SQL Managed Instance.
- Tijdens de verificatie wordt de volgende reeks toegepast om de verificatie-principal op te lossen:

    1. Als het Azure AD-account bestaat als rechtstreeks is toegekend aan de Azure AD-server-principal (aanmelding), die aanwezig is in sys.server_principals als het type 'E', verleent u toegang en pas u machtigingen toe van de Azure AD-server-principal (aanmelding).
    2. Als het Azure AD-account lid is van een Azure AD-groep die is toegekend aan de Azure AD-server-principal (aanmelding), die aanwezig is in sys.server_principals als het type 'X', verleent u toegang en pas u machtigingen toe voor de aanmelding van de Azure AD-groep.
    3. Als het Azure AD-account een speciale, door de portal geconfigureerde Azure AD-beheerder voor SQL Managed Instance is, die niet bestaat in SQL Managed Instance-systeemweergaven, past u speciale vaste machtigingen van de Azure AD-beheerder toe voor SQL Managed Instance (verouderde modus).
    4. Als het Azure AD-account bestaat als een rechtstreeks aan een Azure AD-gebruiker in een database, die aanwezig is in sys.database_principals als type 'E', verleent u toegang en geeft u machtigingen van de Azure AD-databasegebruiker toe.
    5. Als het Azure AD-account lid is van een Azure AD-groep die is toegeschreven aan een Azure AD-gebruiker in een database, die in sys.database_principals aanwezig is als het type 'X', verleent u toegang en pas u machtigingen toe voor de aanmelding van de Azure AD-groep.
    6. Als er een Azure AD-aanmelding is die is toegeschreven aan een Azure AD-gebruikersaccount of een Azure AD-groepsaccount, dat wordt opgelost naar de gebruiker die zich authenticeert, worden alle machtigingen van deze Azure AD-aanmelding toegepast.

### <a name="service-key-and-service-master-key"></a>Servicesleutel en servicemastersleutel

- [Back-up van hoofdsleutels](/sql/t-sql/statements/backup-master-key-transact-sql) wordt niet ondersteund (beheerd door SQL Database service).
- [Herstellen van de hoofdsleutel](/sql/t-sql/statements/restore-master-key-transact-sql) wordt niet ondersteund (beheerd door SQL Database service).
- [Back-up van de servicemastersleutel](/sql/t-sql/statements/backup-service-master-key-transact-sql) wordt niet ondersteund (beheerd door SQL Database service).
- [Herstellen van de servicemastersleutel](/sql/t-sql/statements/restore-service-master-key-transact-sql) wordt niet ondersteund (beheerd door SQL Database service).

## <a name="configuration"></a>Configuratie

### <a name="buffer-pool-extension"></a>Buffergroepextensie

- [Buffergroepextensie](/sql/database-engine/configure-windows/buffer-pool-extension) wordt niet ondersteund.
- `ALTER SERVER CONFIGURATION SET BUFFER POOL EXTENSION` wordt niet ondersteund. Zie [ALTER SERVER CONFIGURATION (SERVERCONFIGURATIE WIJZIGEN).](/sql/t-sql/statements/alter-server-configuration-transact-sql)

### <a name="collation"></a>Sortering

De standaardseratie van exemplaren is `SQL_Latin1_General_CP1_CI_AS` en kan worden opgegeven als een parameter voor het maken. Zie [Collations (Collations).](/sql/t-sql/statements/collations)

### <a name="compatibility-levels"></a>Compatibiliteitsniveaus

- Ondersteunde compatibiliteitsniveaus zijn 100, 110, 120, 130, 140 en 150.
- Compatibiliteitsniveaus lager dan 100 worden niet ondersteund.
- Het standaardcompatibiliteitsniveau voor nieuwe databases is 140. Voor herstelde databases blijft het compatibiliteitsniveau ongewijzigd als het 100 en hoger is.

Zie [ALTER DATABASE Compatibility Level (Compatibiliteitsniveau ALTER DATABASE).](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level)

### <a name="database-mirroring"></a>Databasespiegeling

Databasespiegeling wordt niet ondersteund.

- `ALTER DATABASE SET PARTNER` - `SET WITNESS` en -opties worden niet ondersteund.
- `CREATE ENDPOINT … FOR DATABASE_MIRRORING` wordt niet ondersteund.

Zie ALTER [DATABASE SET PARTNER en SET WITNESS](/sql/t-sql/statements/alter-database-transact-sql-database-mirroring) en CREATE ENDPOINT ... voor meer [informatie. VOOR DATABASE_MIRRORING](/sql/t-sql/statements/create-endpoint-transact-sql).

### <a name="database-options"></a>Databaseopties

- Meerdere logboekbestanden worden niet ondersteund.
- Objecten in het geheugen worden niet ondersteund in de Algemeen servicelaag. 
- Er is een limiet van 280 bestanden per Algemeen-instantie, wat een maximum van 280 bestanden per database impliceert. Zowel gegevens als logboekbestanden in de Algemeen-laag worden meegetelde voor deze limiet. [De Bedrijfskritiek ondersteunt 32.767 bestanden per database](./resource-limits.md#service-tier-characteristics).
- De database mag geen bestandsgroepen bevatten die filestream-gegevens bevatten. Herstellen mislukt als .bak gegevens `FILESTREAM` bevat. 
- Elk bestand wordt in Azure Blob Storage geplaatst. I/O en doorvoer per bestand zijn afhankelijk van de grootte van elk afzonderlijk bestand.

#### <a name="create-database-statement"></a>CREATE DATABASE-instructie

De volgende beperkingen gelden voor `CREATE DATABASE` :

- Bestanden en bestandsgroepen kunnen niet worden gedefinieerd. 
- De `CONTAINMENT` optie wordt niet ondersteund. 
- `WITH` -opties worden niet ondersteund. 
   > [!TIP]
   > Als tijdelijke oplossing gebruikt u na om databaseopties in te stellen om bestanden toe te voegen of `ALTER DATABASE` `CREATE DATABASE` om containment in te stellen. 

- De `FOR ATTACH` optie wordt niet ondersteund.
- De `AS SNAPSHOT OF` optie wordt niet ondersteund.

Zie CREATE [DATABASE voor meer informatie.](/sql/t-sql/statements/create-database-sql-server-transact-sql)

#### <a name="alter-database-statement"></a>ALTER DATABASE-instructie

Sommige bestandseigenschappen kunnen niet worden ingesteld of gewijzigd:

- Een bestandspad kan niet worden opgegeven in de `ALTER DATABASE ADD FILE (FILENAME='path')` T-SQL-instructie. Verwijder `FILENAME` uit het script omdat SQL Managed Instance de bestanden automatisch plaatst. 
- Een bestandsnaam kan niet worden gewijzigd met behulp van de `ALTER DATABASE` instructie .

De volgende opties zijn standaard ingesteld en kunnen niet worden gewijzigd:

- `MULTI_USER`
- `ENABLE_BROKER`
- `AUTO_CLOSE OFF`

De volgende opties kunnen niet worden gewijzigd:

- `AUTO_CLOSE`
- `AUTOMATIC_TUNING(CREATE_INDEX=ON|OFF)`
- `AUTOMATIC_TUNING(DROP_INDEX=ON|OFF)`
- `DISABLE_BROKER`
- `EMERGENCY`
- `ENABLE_BROKER`
- `FILESTREAM`
- `HADR`
- `NEW_BROKER`
- `OFFLINE`
- `PAGE_VERIFY`
- `PARTNER`
- `READ_ONLY`
- `RECOVERY BULK_LOGGED`
- `RECOVERY_SIMPLE`
- `REMOTE_DATA_ARCHIVE` 
- `RESTRICTED_USER`
- `SINGLE_USER`
- `WITNESS`

Sommige `ALTER DATABASE` instructies (bijvoorbeeld [SET CONTAINMENT](/sql/relational-databases/databases/migrate-to-a-partially-contained-database#converting-a-database-to-partially-contained-using-transact-sql)) kunnen tijdelijk mislukken, bijvoorbeeld tijdens de automatische back-up van de database of direct nadat een database is gemaakt. In dit geval `ALTER DATABASE` moet de instructie opnieuw worden gedaan. Zie de sectie Opmerkingen voor meer informatie over gerelateerde [foutberichten.](/sql/t-sql/statements/alter-database-transact-sql?preserve-view=true&tabs=sqlpool&view=azuresqldb-mi-current#remarks-2)

Zie ALTER [DATABASE voor meer informatie.](/sql/t-sql/statements/alter-database-transact-sql-file-and-filegroup-options)

### <a name="sql-server-agent"></a>SQL Server Agent

- Het in- en uitschakelen SQL Server Agent wordt momenteel niet ondersteund in SQL Managed Instance. SQL Agent wordt altijd uitgevoerd.
- Taakplanningstrigger op basis van een niet-actieve CPU wordt niet ondersteund.
- SQL Server Agent instellingen zijn alleen-lezen. De procedure `sp_set_agent_properties` wordt niet ondersteund in SQL Managed Instance. 
- Taken
  - T-SQL-taakstappen worden ondersteund.
  - De volgende replicatietaken worden ondersteund:
    - Lezer van transactielogboek
    - Momentopname
    - Distributeur
  - SSIS-taakstappen worden ondersteund.
  - Andere typen taakstappen worden momenteel niet ondersteund:
    - De taakstap replicatie samenvoegen wordt niet ondersteund. 
    - Wachtrijlezer wordt niet ondersteund. 
    - Opdrachtshell wordt nog niet ondersteund.
  - SQL Managed Instance hebt geen toegang tot externe resources, bijvoorbeeld netwerk shares via Robocopy. 
  - SQL Server Analysis Services wordt niet ondersteund.
- Meldingen worden gedeeltelijk ondersteund.
- E-mailmeldingen worden ondersteund, maar hiervoor moet u wel een Database Mail configureren. SQL Server Agent kan slechts één Database Mail gebruiken en moet worden `AzureManagedInstance_dbmail_profile` aangeroepen. 
  - Pager wordt niet ondersteund.
  - NetSend wordt niet ondersteund.
  - Waarschuwingen worden nog niet ondersteund.
  - Proxies worden niet ondersteund.
- EventLog wordt niet ondersteund.
- De gebruiker moet rechtstreeks worden ingesteld op de Azure AD-server-principal (aanmelding) om SQL Agent-taken te maken, te wijzigen of uit te voeren. Gebruikers die niet rechtstreeks zijn verbonden, bijvoorbeeld gebruikers die deel uitmaken van een Azure AD-groep die de rechten heeft om SQL Agent-taken te maken, te wijzigen of uit te voeren, kunnen deze acties niet effectief uitvoeren. Dit komt door de beperkingen van Managed Instance-imitatie en [EXECUTE AS.](#logins-and-users)
- De functie Beheer van meerdere servers voor master-/doeltaken (MSX/TSX) wordt niet ondersteund.

Zie [SQL Server Agent](/sql/ssms/agent/sql-server-agent) voor meer informatie over SQL Server Agent.

### <a name="tables"></a>Tables

De volgende tabeltypen worden niet ondersteund:

- [Filestream](/sql/relational-databases/blob/filestream-sql-server)
- [BESTANDSTABEL](/sql/relational-databases/blob/filetables-sql-server)
- [EXTERNAL TABLE](/sql/t-sql/statements/create-external-table-transact-sql) (Polybase)
- [MEMORY_OPTIMIZED](/sql/relational-databases/in-memory-oltp/introduction-to-memory-optimized-tables) (niet alleen ondersteund in Algemeen-laag)

Zie CREATE TABLE en ALTER TABLE voor meer [informatie over het maken en wijzigen](/sql/t-sql/statements/create-table-transact-sql) van [tabellen.](/sql/t-sql/statements/alter-table-transact-sql)

## <a name="functionalities"></a>Functionaliteiten

### <a name="bulk-insert--openrowset"></a>Bulksgewijs invoegen/OPENROWSET

SQL Managed Instance geen toegang tot bestands shares en Windows-mappen, dus de bestanden moeten worden geïmporteerd uit Azure Blob Storage:

- `DATASOURCE` is vereist in de `BULK INSERT` opdracht tijdens het importeren van bestanden uit Azure Blob Storage. Zie [BULK INSERT](/sql/t-sql/statements/bulk-insert-transact-sql).
- `DATASOURCE` is vereist in de `OPENROWSET` functie wanneer u de inhoud van een bestand uit Azure Blob Storage leest. Zie [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql).
- `OPENROWSET` kan worden gebruikt om gegevens van Azure SQL Database, Azure SQL Managed Instance of SQL Server lezen. Andere bronnen, zoals Oracle-databases of Excel-bestanden, worden niet ondersteund.

### <a name="clr"></a>Clr

Een SQL Managed Instance heeft geen toegang tot bestands shares en Windows-mappen, dus zijn de volgende beperkingen van toepassing:

- Alleen `CREATE ASSEMBLY FROM BINARY` wordt ondersteund. Zie [ASSEMBLY MAKEN VAN BINAIRE .](/sql/t-sql/statements/create-assembly-transact-sql) 
- `CREATE ASSEMBLY FROM FILE` wordt niet ondersteund. Zie [ASSEMBLY MAKEN VAN BESTAND](/sql/t-sql/statements/create-assembly-transact-sql).
- `ALTER ASSEMBLY` kan niet verwijzen naar bestanden. Zie [ASSEMBLY WIJZIGEN.](/sql/t-sql/statements/alter-assembly-transact-sql)

### <a name="database-mail-db_mail"></a>Database Mail (db_mail)
 - `sp_send_dbmail` kan geen bijlagen verzenden met de @file_attachments parameter . Lokale bestandssysteem en externe shares of Azure Blob Storage zijn niet toegankelijk via deze procedure.
 - Bekijk de bekende problemen met betrekking tot `@query` parameters en verificatie.
 
### <a name="dbcc"></a>Dbcc

Niet-gedocumenteerde DBCC-instructies die zijn ingeschakeld in SQL Server worden niet ondersteund in SQL Managed Instance.

- Er wordt slechts een beperkt aantal globale traceervlaggen ondersteund. Sessieniveau `Trace flags` wordt niet ondersteund. Zie [Trace flags (Traceervlaggen).](/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql)
- [DBCC TRACEOFF](/sql/t-sql/database-console-commands/dbcc-traceoff-transact-sql) en [DBCC TRACEON](/sql/t-sql/database-console-commands/dbcc-traceon-transact-sql) werken met het beperkte aantal globale traceervlaggen.
- [DBCC CHECKDB](/sql/t-sql/database-console-commands/dbcc-checkdb-transact-sql) met opties REPAIR_ALLOW_DATA_LOSS, REPAIR_FAST en REPAIR_REBUILD kan niet worden gebruikt omdat de database niet kan worden ingesteld in de modus - zie ALTER DATABASE differences (VERSCHILLEN IN `SINGLE_USER` DATABASE [WIJZIGEN).](#alter-database-statement) Potentiële beschadiging van de database wordt afgehandeld door het ondersteuning voor Azure team. Neem contact ondersteuning voor Azure als er een indicatie is dat de database is beschadiging.

### <a name="distributed-transactions"></a>Gedistribueerde transacties

Gedeeltelijke ondersteuning voor [gedistribueerde transacties](../database/elastic-transactions-overview.md) is momenteel beschikbaar als openbare preview. Gedistribueerde transacties worden ondersteund onder de volgende voorwaarden (aan alle transacties moet worden voldaan):
* alle transactiedeelnemers zijn Azure SQL beheerde exemplaren die deel uitmaken van de [vertrouwensgroep Server.](./server-trust-group-overview.md)
* transacties worden gestart vanuit .NET (klasse TransactionScope) of Transact-SQL.

Azure SQL Managed Instance biedt momenteel geen ondersteuning voor andere scenario's die regelmatig worden ondersteund door MSDTC on-premises of in Azure Virtual Machines.

### <a name="extended-events"></a>Uitgebreide gebeurtenissen

Sommige Windows-specifieke doelen voor uitgebreide evenementen (XEvents) worden niet ondersteund:

- Het `etw_classic_sync` doel wordt niet ondersteund. Sla `.xel` bestanden op in Azure Blob Storage. Zie [etw_classic_sync doel](/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#etw_classic_sync_target-target).
- Het `event_file` doel wordt niet ondersteund. Sla `.xel` bestanden op in Azure Blob Storage. Zie [Doel van de event_file](/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#event_file-target).

### <a name="external-libraries"></a>Externe bibliotheken

Externe bibliotheken in database R en Python worden ondersteund in beperkte openbare preview. Zie [Machine Learning Services in Azure SQL Managed Instance (preview)](machine-learning-services-overview.md).

### <a name="filestream-and-filetable"></a>Filestream en FileTable

- Filestream-gegevens worden niet ondersteund.
- De database mag geen bestandsgroepen met gegevens `FILESTREAM` bevatten.
- `FILETABLE` wordt niet ondersteund.
- Tabellen kunnen geen typen `FILESTREAM` hebben.
- De volgende functies worden niet ondersteund:
  - `GetPathLocator()`
  - `GET_FILESTREAM_TRANSACTION_CONTEXT()`
  - `PathName()`
  - `GetFileNamespacePat)`
  - `FileTableRootPath()`

Zie [FILESTREAM](/sql/relational-databases/blob/filestream-sql-server) en [FileTables voor meer informatie.](/sql/relational-databases/blob/filetables-sql-server)

### <a name="full-text-semantic-search"></a>Semantische zoekopdracht in volledige tekst

[Semantic Search](/sql/relational-databases/search/semantic-search-sql-server) wordt niet ondersteund.

### <a name="linked-servers"></a>Gekoppelde servers

Gekoppelde servers in SQL Managed Instance ondersteunen een beperkt aantal doelen:

- Ondersteunde doelen zijn SQL Managed Instance, SQL Database, Azure Synapse [serverloze](https://devblogs.microsoft.com/azure-sql/linked-server-to-synapse-sql-to-implement-polybase-like-scenarios-in-managed-instance/) en toegewezen SQL-pools en SQL Server-instanties. 
- Gedistribueerde beschrijfbare transacties zijn alleen mogelijk tussen beheerde exemplaren. Zie Gedistribueerde transacties [voor meer informatie.](../database/elastic-transactions-overview.md) De MS DTC wordt echter niet ondersteund.
- Doelen die niet worden ondersteund, zijn bestanden, Analysis Services en andere RDBMS. Probeer een native CSV-import uit Azure Blob Storage te gebruiken met of als alternatief voor het importeren van bestanden, of om bestanden te laden met behulp van een `BULK INSERT` `OPENROWSET` [serverloze SQL-pool in Azure Synapse Analytics](https://devblogs.microsoft.com/azure-sql/linked-server-to-synapse-sql-to-implement-polybase-like-scenarios-in-managed-instance/).

Bewerkingen: 

- [Schrijftransacties tussen exemplaren](../database/elastic-transactions-overview.md) worden alleen ondersteund voor beheerde exemplaren.
- `sp_dropserver` wordt ondersteund voor het droppen van een gekoppelde server. Zie [sp_dropserver](/sql/relational-databases/system-stored-procedures/sp-dropserver-transact-sql).
- De `OPENROWSET` functie kan worden gebruikt om query's alleen uit te voeren op SQL Server instanties. Ze kunnen worden beheerd, on-premises of in virtuele machines. Zie [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql).
- De `OPENDATASOURCE` functie kan worden gebruikt om query's alleen uit te voeren op SQL Server instanties. Ze kunnen worden beheerd, on-premises of in virtuele machines. Alleen de `SQLNCLI` waarden , en worden ondersteund als `SQLNCLI11` `SQLOLEDB` provider. Een voorbeeld is `SELECT * FROM OPENDATASOURCE('SQLNCLI', '...').AdventureWorks2012.HumanResources.Employee`. Zie [OPENDATASOURCE](/sql/t-sql/functions/opendatasource-transact-sql).
- Gekoppelde servers kunnen niet worden gebruikt om bestanden (Excel, CSV) uit de netwerk shares te lezen. Probeer BULK INSERT [,](/sql/t-sql/statements/bulk-insert-transact-sql#e-importing-data-from-a-csv-file) [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql#g-accessing-data-from-a-csv-file-with-a-format-file) te gebruiken die CSV-bestanden leest van Azure Blob Storage of een gekoppelde server die verwijst naar een [serverloze SQL-pool in Synapse Analytics](https://devblogs.microsoft.com/azure-sql/linked-server-to-synapse-sql-to-implement-polybase-like-scenarios-in-managed-instance/). Volg deze aanvragen op [SQL Managed Instance feedbackitem](https://feedback.azure.com/forums/915676-sql-managed-instance/suggestions/35657887-linked-server-to-non-sql-sources)|

### <a name="polybase"></a>PolyBase

Het enige beschikbare type externe bron is RDBMS (in openbare preview) voor Azure SQL database, Azure SQL managed instance en Azure Synapse pool. U kunt een externe tabel gebruiken die verwijst naar een [serverloze SQL-pool in Synapse Analytics](https://devblogs.microsoft.com/azure-sql/read-azure-storage-files-using-synapse-sql-external-tables/) als tijdelijke oplossing voor externe Polybase-tabellen die rechtstreeks vanuit de Azure-opslag worden gelezen. In Azure SQL beheerde exemplaar kunt u gekoppelde servers gebruiken voor een [serverloze SQL-pool in Synapse Analytics](https://devblogs.microsoft.com/azure-sql/linked-server-to-synapse-sql-to-implement-polybase-like-scenarios-in-managed-instance/) of SQL Server om Azure-opslaggegevens te lezen.
Zie PolyBase voor meer informatie [over PolyBase.](/sql/relational-databases/polybase/polybase-guide)

### <a name="replication"></a>Replicatie

- Momentopnamen en replicatietypen in twee richtingen worden ondersteund. Samenvoegreplicatie, peer-to-peerreplicatie en bijwerkbare abonnementen worden niet ondersteund.
- [Transactionele replicatie](replication-transactional-overview.md) is beschikbaar voor openbare preview op SQL Managed Instance met enkele beperkingen:
    - Alle typen replicatiedeelnemers (Publisher, Distributor, Pull Subscriber en Push Subscriber) kunnen worden geplaatst op SQL Managed Instance, maar de uitgever en de distributeur moeten zich zowel in de cloud als on-premises in de cloud of on-premises hebben.
    - SQL Managed Instance kunt communiceren met de recente versies van SQL Server. Zie de [matrix met ondersteunde versies](replication-transactional-overview.md#supportability-matrix) voor meer informatie.
    - Transactionele replicatie heeft enkele [aanvullende netwerkvereisten.](replication-transactional-overview.md#requirements)

Zie de volgende zelfstudies voor meer informatie over het configureren van transactionele replicatie:
- [Replicatie tussen een SQL MI-uitgever en SQL MI-abonnee](replication-between-two-instances-configure-tutorial.md)
- [Replicatie tussen een SQL MI-uitgever, SQL MI-distributeur en SQL Server-abonnee](replication-two-instances-and-sql-server-configure-tutorial.md)

### <a name="restore-statement"></a>RESTORE-instructie 

- Ondersteunde syntaxis:
  - `RESTORE DATABASE`
  - `RESTORE FILELISTONLY ONLY`
  - `RESTORE HEADER ONLY`
  - `RESTORE LABELONLY ONLY`
  - `RESTORE VERIFYONLY ONLY`
- Niet-ondersteunde syntaxis:
  - `RESTORE LOG ONLY`
  - `RESTORE REWINDONLY ONLY`
- Bron: 
  - `FROM URL` (Azure Blob Storage) is de enige ondersteunde optie.
  - `FROM DISK`/`TAPE`/backup-apparaat wordt niet ondersteund.
  - Back-upsets worden niet ondersteund.
- `WITH` -opties worden niet ondersteund. Herstelpogingen, `WITH` zoals , `DIFFERENTIAL` , `STATS` `REPLACE` enzovoort, mislukken.
- `ASYNC RESTORE`: Het herstellen wordt voortgezet, zelfs als de clientverbinding wordt breekt. Als uw verbinding wordt verwijderd, kunt u de weergave controleren op de status van een herstelbewerking en `sys.dm_operation_status` op een CREATE- en DROP-database. Zie [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database). 

De volgende databaseopties worden ingesteld of overschrijven en kunnen later niet meer worden gewijzigd: 

- `NEW_BROKER` als de broker niet is ingeschakeld in het BAK-bestand. 
- `ENABLE_BROKER` als de broker niet is ingeschakeld in het BAK-bestand. 
- `AUTO_CLOSE=OFF` als een database in het BAK-bestand `AUTO_CLOSE=ON` heeft. 
- `RECOVERY FULL` als een database in het BAK-bestand de `SIMPLE` herstelmodus `BULK_LOGGED` of heeft.
- Er wordt een voor geheugen geoptimaliseerde bestandsgroep toegevoegd en XTP aangeroepen als deze zich niet in het bron-BAK-bestand zou kunnen vinden. 
- De naam van een bestaande voor geheugen geoptimaliseerde bestandsgroep wordt gewijzigd in XTP. 
- `SINGLE_USER` de `RESTRICTED_USER` opties en worden geconverteerd naar `MULTI_USER` .

Beperkingen: 

- Back-ups van de beschadigde databases kunnen worden hersteld, afhankelijk van het type beschadiging, maar geautomatiseerde back-ups worden pas gemaakt als de beschadiging is opgelost. Zorg ervoor dat u op de `DBCC CHECKDB` bron-SQL Managed Instance en gebruik `WITH CHECKSUM` back-up om dit probleem te voorkomen.
- Het herstellen van een bestand van een database met een beperking die in dit document wordt beschreven (bijvoorbeeld of objecten) kan niet worden hersteld `.BAK` `FILESTREAM` op `FILETABLE` SQL Managed Instance.
- `.BAK` bestanden die meerdere back-upsets bevatten, kunnen niet worden hersteld. 
- `.BAK` bestanden die meerdere logboekbestanden bevatten, kunnen niet worden hersteld.
- Back-ups die databases bevatten die groter zijn dan 8 TB, actieve in-memory OLTP-objecten of het aantal bestanden dat groter is dan 280 bestanden per exemplaar, kunnen niet worden hersteld op een Algemeen-exemplaar. 
- Back-ups die databases bevatten die groter zijn dan 4 TB of in-memory OLTP-objecten met de totale grootte groter dan de grootte die wordt beschreven in [resourcelimieten,](resource-limits.md) kunnen niet worden hersteld op Bedrijfskritiek exemplaar.
Zie RESTORE-instructies voor meer informatie [over herstel-instructies.](/sql/t-sql/statements/restore-statements-transact-sql)

 > [!IMPORTANT]
 > Dezelfde beperkingen gelden voor ingebouwde herstelbewerkingen naar een bepaald tijdstip. Een voorbeeld: Algemeen database groter dan 4 TB kan niet worden hersteld op Bedrijfskritiek exemplaar. Bedrijfskritiek database met IN-memory OLTP-bestanden of meer dan 280 bestanden kan niet worden hersteld op Algemeen exemplaar.

### <a name="service-broker"></a>Service Broker

Berichtuitwisseling tussen verschillende exemplaren van servicebroker wordt alleen ondersteund Azure SQL beheerde exemplaren:

- `CREATE ROUTE`: U kunt niet gebruiken met `CREATE ROUTE` een andere naam dan of `ADDRESS` `LOCAL` DNS-naam van een andere SQL Managed Instance.
- `ALTER ROUTE`: U kunt niet gebruiken met `ALTER ROUTE` een andere naam dan of `ADDRESS` `LOCAL` DNS-naam van een andere SQL Managed Instance.

Transportbeveiliging wordt ondersteund, dialoogvensterbeveiliging is niet:
- `CREATE REMOTE SERVICE BINDING`worden niet ondersteund.

Service Broker is standaard ingeschakeld en kan niet worden uitgeschakeld. De volgende ALTER DATABSE-opties worden niet ondersteund:
- `ENABLE_BROKER`
- `DISABLE_BROKER`

### <a name="stored-procedures-functions-and-triggers"></a>Opgeslagen procedures, functies en triggers

- `NATIVE_COMPILATION` wordt niet ondersteund in de Algemeen laag.
- De volgende [sp_configure](/sql/relational-databases/system-stored-procedures/sp-configure-transact-sql) worden niet ondersteund: 
  - `allow polybase export`
  - `allow updates`
  - `filestream_access_level`
  - `remote access`
  - `remote data archive`
  - `remote proc trans`
  - `scan for startup procs`
- `sp_execute_external_scripts` wordt niet ondersteund. Zie [sp_execute_external_scripts](/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql#examples).
- `xp_cmdshell` wordt niet ondersteund. Zie [xp_cmdshell](/sql/relational-databases/system-stored-procedures/xp-cmdshell-transact-sql).
- `Extended stored procedures` worden niet ondersteund, en dit omvat `sp_addextendedproc` en `sp_dropextendedproc` . Deze functionaliteit wordt niet ondersteund omdat deze zich op een afschaffingspad voor SQL Server. Zie Uitgebreide opgeslagen [procedures voor meer informatie.](/sql/relational-databases/extended-stored-procedures-programming/database-engine-extended-stored-procedures-programming)
- `sp_attach_db`, `sp_attach_single_file_db` en worden niet `sp_detach_db` ondersteund. Zie [sp_attach_db](/sql/relational-databases/system-stored-procedures/sp-attach-db-transact-sql), [sp_attach_single_file_db](/sql/relational-databases/system-stored-procedures/sp-attach-single-file-db-transact-sql)en [sp_detach_db](/sql/relational-databases/system-stored-procedures/sp-detach-db-transact-sql).

### <a name="system-functions-and-variables"></a>Systeemfuncties en variabelen

De volgende variabelen, functies en weergaven retourneren verschillende resultaten:

- `SERVERPROPERTY('EngineEdition')` retourneert de waarde 8. Deze eigenschap identificeert een unieke SQL Managed Instance. Zie [SERVERPROPERTY](/sql/t-sql/functions/serverproperty-transact-sql).
- `SERVERPROPERTY('InstanceName')` retourneert NULL omdat het concept van exemplaar zoals het bestaat voor SQL Server niet van toepassing is op SQL Managed Instance. Zie [SERVERPROPERTY('InstanceName')](/sql/t-sql/functions/serverproperty-transact-sql).
- `@@SERVERNAME` retourneert een volledige 'verbindingsbare' DNS-naam, bijvoorbeeld my-managed-instance.wcus17662feb9ce98.database.windows.net. Zie [ @SERVERNAME @](/sql/t-sql/functions/servername-transact-sql). 
- `SYS.SERVERS` retourneert een volledige DNS-naam die 'verbindingsbaar' is, zoals voor de eigenschappen `myinstance.domain.database.windows.net` 'naam' en 'data_source'. Zie [SYS. SERVERS](/sql/relational-databases/system-catalog-views/sys-servers-transact-sql).
- `@@SERVICENAME` retourneert NULL omdat het concept van service zoals deze bestaat voor SQL Server niet van toepassing is op SQL Managed Instance. Zie [ @SERVICENAME @](/sql/t-sql/functions/servicename-transact-sql).
- `SUSER_ID` wordt ondersteund. Deze retourneert NULL als de Azure AD-aanmelding zich niet in de sys.sysaanmeldingen. Zie [SUSER_ID](/sql/t-sql/functions/suser-id-transact-sql). 
- `SUSER_SID` wordt niet ondersteund. De verkeerde gegevens worden geretourneerd. Dit is een tijdelijk bekend probleem. Zie [SUSER_SID](/sql/t-sql/functions/suser-sid-transact-sql). 

## <a name="environment-constraints"></a><a name="Environment"></a>Beperkingen van de omgeving

### <a name="subnet"></a>Subnet
-  U kunt geen andere resources (bijvoorbeeld virtuele machines) in het subnet plaatsen waar u uw SQL Managed Instance. Implementeer deze resources met behulp van een ander subnet.
- Subnet moet voldoende beschikbare [IP-adressen hebben.](connectivity-architecture-overview.md#network-requirements) Minimaal moet ten minste 32 IP-adressen in het subnet zijn.
- Het aantal vCores en typen exemplaren dat u in een regio kunt implementeren, kent [enkele beperkingen en beperkingen.](resource-limits.md#regional-resource-limitations)
- Er is een [netwerkconfiguratie](connectivity-architecture-overview.md#network-requirements) die moet worden toegepast op het subnet.

### <a name="vnet"></a>VNET
- VNet kan worden geïmplementeerd met resourcemodel: klassiek model voor VNet wordt niet ondersteund.
- Nadat een SQL Managed Instance is gemaakt, wordt het SQL Managed Instance of VNet naar een andere resourcegroep of een ander abonnement niet ondersteund.
- Voor SQL Managed Instances die worden gehost in virtuele clusters die zijn gemaakt vóór 22-9-2020, wordt globale [peering](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) niet ondersteund. U kunt verbinding maken met deze resources via ExpressRoute of VNet-naar-VNet via VNet-gateways.

### <a name="failover-groups"></a>Failovergroepen
Systeemdatabases worden niet gerepliceerd naar het secundaire exemplaar in een failovergroep. Daarom zijn scenario's die afhankelijk zijn van objecten van de systeemdatabases, onmogelijk op het secundaire exemplaar, tenzij de objecten handmatig op de secundaire instantie worden gemaakt.

### <a name="tempdb"></a>Tempdb
- De maximale bestandsgrootte `tempdb` van mag niet groter zijn dan 24 GB per kern op een Algemeen laag. De maximale `tempdb` grootte voor een Bedrijfskritiek-laag wordt beperkt door de SQL Managed Instance opslaggrootte. `Tempdb` de grootte van het logboekbestand is beperkt tot 120 GB op Algemeen laag. Sommige query's kunnen een fout retourneren als ze meer dan 24 GB per kern nodig hebben in of als ze meer dan `tempdb` 120 GB aan logboekgegevens produceren.
- `Tempdb` wordt altijd gesplitst in 12 gegevensbestanden: 1 primaire, ook wel hoofdbestanden genoemd, gegevensbestand en 11 niet-primaire gegevensbestanden. De bestandsstructuur kan niet worden gewijzigd en nieuwe bestanden kunnen niet worden toegevoegd aan `tempdb` . 
- [Voor geheugen geoptimaliseerde `tempdb` metagegevens,](/sql/relational-databases/databases/tempdb-database?view=sql-server-ver15&preserve-view=true#memory-optimized-tempdb-metadata)een nieuwe SQL Server 2019 in-memory database functie, wordt niet ondersteund.
- Objecten die in de modeldatabase zijn gemaakt, kunnen niet automatisch worden gemaakt in na een herstart of een failover, omdat de initiële objectlijst niet uit de `tempdb` `tempdb` modeldatabase wordt gehaald. U moet handmatig objecten maken `tempdb` na elke herstart of failover.

### <a name="msdb"></a>Msdb

De volgende MSDB-schema's in SQL Managed Instance moeten eigendom zijn van hun respectieve vooraf gedefinieerde rollen:

- Algemene rollen
  - TargetServersRole
- [Databaserollen opgelost](/sql/ssms/agent/sql-server-agent-fixed-database-roles?view=sql-server-ver15&preserve-view=true)
  - SQLAgentUserRole
  - SQLAgentReaderRole
  - SQLAgentOperatorRole
- [DatabaseMail-rollen:](/sql/relational-databases/database-mail/database-mail-configuration-objects?view=sql-server-ver15&preserve-view=true#DBProfile)
  - DatabaseMailUserRole
- [Integratieservicesrollen:](/sql/integration-services/security/integration-services-roles-ssis-service?view=sql-server-ver15&preserve-view=true)
  - db_ssisadmin
  - db_ssisltduser
  - db_ssisoperator
  
> [!IMPORTANT]
> Het wijzigen van de vooraf gedefinieerde rolnamen, schemanamen en schema-eigenaren door klanten heeft invloed op de normale werking van de service. Eventuele wijzigingen in deze wijzigingen worden teruggedraaid naar de vooraf gedefinieerde waarden zodra ze zijn gedetecteerd, of bij de volgende service-update op zijn laatst om de normale werking van de service te garanderen.

### <a name="error-logs"></a>Foutenlogboeken

SQL Managed Instance uitgebreide informatie in foutlogboeken. Er zijn veel interne systeemgebeurtenissen die zijn vastgelegd in het foutenlogboek. Gebruik een aangepaste procedure om foutenlogboeken te lezen die een aantal irrelevante vermeldingen wegfilteren. Zie voor meer informatie [SQL Managed Instance : sp_readmierrorlog](/archive/blogs/sqlcat/azure-sql-db-managed-instance-sp_readmierrorlog) of SQL Managed Instance extension [(preview)](/sql/azure-data-studio/azure-sql-managed-instance-extension#logs) voor Azure Data Studio.

## <a name="next-steps"></a>Volgende stappen

- Zie Wat is SQL Managed Instance? voor [meer informatie over SQL Managed Instance?](sql-managed-instance-paas-overview.md)
- Zie voor een lijst met functies en [vergelijkingen Azure SQL Managed Instance functievergelijking.](../database/features-comparison.md)
- Zie de opmerkingen bij de release voor meer informatie over [release-updates SQL Managed Instance status van bekende problemen](../database/doc-changes-updates-release-notes.md)
- Zie Een nieuwe SQL Managed Instance voor een snelstart die laat zien hoe [SQL Managed Instance.](instance-create-quickstart.md)
