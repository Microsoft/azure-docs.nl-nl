---
title: T-SQL-verschillen tussen SQL Server & Azure SQL Managed instance
description: In dit artikel worden de verschillen in Transact-SQL (T-SQL) beschreven tussen een door Azure SQL beheerd exemplaar en SQL Server.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.devlang: ''
ms.topic: reference
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, bonova, danil
ms.date: 3/5/2021
ms.custom: seoapril2019, sqldbrb=1
ms.openlocfilehash: 014140b9b9832bab3de4f71c0b5f164b564b3fe5
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102212719"
---
# <a name="t-sql-differences-between-sql-server--azure-sql-managed-instance"></a>T-SQL-verschillen tussen SQL Server & Azure SQL Managed instance
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

In dit artikel vindt u een overzicht van de verschillen tussen de syntaxis en het gedrag tussen Azure SQL Managed instance en SQL Server. 


SQL Managed instance biedt hoge compatibiliteit met de SQL Server data base-engine en de meeste functies worden ondersteund in een door SQL beheerd exemplaar.

![Eenvoudige migratie van SQL Server](./media/transact-sql-tsql-differences-sql-server/migration.png)

Er zijn enkele PaaS-beperkingen die worden geïntroduceerd in SQL Managed instance en sommige gedrags wijzigingen in vergelijking tot SQL Server. De verschillen zijn onderverdeeld in de volgende categorieën: <a name="Differences"></a>

- [Beschik baarheid](#availability) omvat de verschillen in AlwaysOn- [beschikbaarheids groepen](#always-on-availability-groups) en [back-ups](#backup).
- [Beveiliging](#security) omvat de verschillen in [controle](#auditing), [certificaten](#certificates), [referenties](#credential), [cryptografische providers](#cryptographic-providers), [aanmeldingen en gebruikers](#logins-and-users), en de [Service sleutel en service hoofd sleutel](#service-key-and-service-master-key).
- De [configuratie](#configuration) bevat de verschillen [in de buffergroepuitbreiding](#buffer-pool-extension), [sortering](#collation), [compatibiliteits niveaus](#compatibility-levels), [database spiegeling](#database-mirroring), [database opties](#database-options), [SQL Server Agent](#sql-server-agent)en [tabel opties](#tables).
- [Functies](#functionalities) zijn onder [andere Bulk Insert/OPENROWSET](#bulk-insert--openrowset), [CLR](#clr), [DBCC](#dbcc), [gedistribueerde trans acties](#distributed-transactions), [Extended Events](#extended-events), [externe bibliotheken](#external-libraries), [FileStream en bestands tabel](#filestream-and-filetable), [semantisch zoeken in volledige tekst](#full-text-semantic-search), [gekoppelde servers](#linked-servers), [poly base](#polybase), [replicatie](#replication), [herstel](#restore-statement), [Service Broker](#service-broker), [opgeslagen procedures, functies en triggers](#stored-procedures-functions-and-triggers).
- [Omgevings instellingen](#Environment) , zoals VNets en subnet-configuraties.

De meeste van deze functies zijn architectuur beperkingen en vertegenwoordigen service onderdelen.

Tijdelijke bekende problemen die worden gedetecteerd in het SQL Managed instance en worden in de toekomst opgelost, worden beschreven in de [pagina release opmerkingen](../database/doc-changes-updates-release-notes.md).

## <a name="availability"></a>Beschikbaarheid

### <a name="always-on-availability-groups"></a><a name="always-on-availability-groups"></a>AlwaysOn-beschikbaarheidsgroepen

[Hoge Beschik baarheid](../database/high-availability-sla.md) is ingebouwd in een SQL-beheerd exemplaar en kan niet worden beheerd door gebruikers. De volgende instructies worden niet ondersteund:

- [EIND PUNT MAKEN... VOOR DATABASE_MIRRORING](/sql/t-sql/statements/create-endpoint-transact-sql)
- [BESCHIKBAARHEIDS GROEP MAKEN](/sql/t-sql/statements/create-availability-group-transact-sql)
- [ALTER AVAILABILITY GROUP](/sql/t-sql/statements/alter-availability-group-transact-sql)
- [BESCHIKBAARHEIDS GROEP NEERZETTEN](/sql/t-sql/statements/drop-availability-group-transact-sql)
- De [set HADR](/sql/t-sql/statements/alter-database-transact-sql-set-hadr) -component van de instructie [ALTER data base](/sql/t-sql/statements/alter-database-transact-sql)

### <a name="backup"></a>Backup

SQL Managed instance heeft automatische back-ups, zodat gebruikers volledige database `COPY_ONLY` back-ups kunnen maken. Differentiële back-ups, logboek bestanden en moment opnamen van bestands momentopnamen worden niet ondersteund.

- Met een SQL Managed Instance kunt u een back-up maken van een exemplaar database alleen naar een Azure Blob Storage-account:
  - Alleen `BACKUP TO URL` wordt ondersteund.
  - `FILE`, `TAPE` en back-upapparaten worden niet ondersteund.
- De meeste algemene `WITH` opties worden ondersteund.
  - `COPY_ONLY` is verplicht.
  - `FILE_SNAPSHOT` wordt niet ondersteund.
  - Tape opties: `REWIND` , `NOREWIND` , `UNLOAD` en `NOUNLOAD` worden niet ondersteund.
  - Opties die specifiek zijn voor het logboek: `NORECOVERY` , `STANDBY` en `NO_TRUNCATE` worden niet ondersteund.

Beperkingen: 

- Met een SQL Managed Instance kunt u een back-up maken van een exemplaar database met Maxi maal 32 Stripes, die voldoende is voor data bases tot 4 TB als back-upcompressie wordt gebruikt.
- U kunt niet uitvoeren `BACKUP DATABASE ... WITH COPY_ONLY` op een Data Base die is versleuteld met door service beheerde transparent Data Encryption (TDE). Door service beheerde TDE zorgt ervoor dat back-ups worden versleuteld met een interne TDE-sleutel. De sleutel kan niet worden geëxporteerd, dus u kunt de back-up niet herstellen. Gebruik automatische back-ups en herstel naar een bepaald tijdstip, of gebruik in plaats daarvan door de [klant beheerde (BYOK) TDe](../database/transparent-data-encryption-tde-overview.md#customer-managed-transparent-data-encryption---bring-your-own-key) . U kunt versleuteling ook uitschakelen voor de data base.
- Systeem eigen back-ups die zijn gemaakt op een beheerd exemplaar, kunnen niet worden hersteld naar een SQL Server. Dit komt doordat het beheerde exemplaar een hogere interne database versie heeft dan een versie van SQL Server.
- De maximale grootte van de back-upstripe met behulp van de `BACKUP` opdracht in het SQL Managed instance is 195 GB. Dit is de maximale grootte voor de blob. Verhoog het aantal Stripes in de back-upopdracht om de afzonderlijke Stripe-grootte te verminderen en binnen deze limiet te blijven.

    > [!TIP]
    > Als u een back-up van een Data Base maakt vanuit een van SQL Server in een on-premises omgeving of op een virtuele machine, kunt u het volgende doen om deze beperking te omzeilen:
    >
    > - Maak een back-up in `DISK` plaats van een back-up te maken naar `URL` .
    > - Upload de back-upbestanden naar de Blob-opslag.
    > - Herstellen naar een door SQL beheerd exemplaar.
    >
    > De `Restore` opdracht in een SQL Managed instance ondersteunt grotere BLOB-grootten in de back-upbestanden omdat een ander type BLOB wordt gebruikt voor het opslaan van de geüploade back-upbestanden.

Zie [back-up](/sql/t-sql/statements/backup-transact-sql)voor informatie over back-ups met behulp van T-SQL.

## <a name="security"></a>Beveiliging

### <a name="auditing"></a>Controleren

De belangrijkste verschillen tussen controles in Microsoft Azure SQL en in SQL Server zijn:

- Met SQL Managed instance werkt auditing op server niveau. De `.xel` logboek bestanden worden opgeslagen in Azure Blob-opslag.
- Met Azure SQL Database werkt controle op het niveau van de data base. De `.xel` logboek bestanden worden opgeslagen in Azure Blob-opslag.
- Met SQL Server, on-premises of in virtuele machines, werkt auditing op server niveau. Gebeurtenissen worden opgeslagen in gebeurtenis logboeken van het bestands systeem of Windows.
 
XEvent-controle in een door SQL beheerd exemplaar ondersteunt Azure Blob-opslag doelen. Bestands-en Windows-logboeken worden niet ondersteund.

De belangrijkste verschillen in de `CREATE AUDIT` syntaxis voor de controle van Azure Blob-opslag zijn:

- Er wordt een nieuwe syntaxis `TO URL` opgegeven die u kunt gebruiken om de URL op te geven van de Azure Blob Storage-container waar de `.xel` bestanden worden geplaatst.
- De syntaxis `TO FILE` wordt niet ondersteund omdat een door SQL beheerd exemplaar geen toegang krijgt tot Windows-bestands shares.

Zie voor meer informatie: 

- [SERVER CONTROLE MAKEN](/sql/t-sql/statements/create-server-audit-transact-sql) 
- [ALTER SERVER AUDIT](/sql/t-sql/statements/alter-server-audit-transact-sql)
- [Controle](/sql/relational-databases/security/auditing/sql-server-audit-database-engine)

### <a name="certificates"></a>Certificaten

SQL Managed instance heeft geen toegang tot bestands shares en Windows-mappen, dus de volgende beperkingen zijn van toepassing:

- Het `CREATE FROM` / `BACKUP TO` bestand wordt niet ondersteund voor certificaten.
- Het `CREATE` / `BACKUP` certificaat van `FILE` / `ASSEMBLY` wordt niet ondersteund. Persoonlijke sleutel bestanden kunnen niet worden gebruikt. 

Zie [certificaat](/sql/t-sql/statements/create-certificate-transact-sql) en [back-upcertificaat](/sql/t-sql/statements/backup-certificate-transact-sql)maken. 
 
**Tijdelijke oplossing**: in plaats van het maken van een back-up van het certificaat en het herstellen van de back-up, [de binaire inhoud en de persoonlijke sleutel van het certificaat op te halen, deze op te slaan als. SQL-bestand en te maken](/sql/t-sql/functions/certencoded-transact-sql#b-copying-a-certificate-to-another-database)

```sql
CREATE CERTIFICATE  
   FROM BINARY = asn_encoded_certificate
WITH PRIVATE KEY (<private_key_options>)
```

### <a name="credential"></a>Referentie

Alleen Azure Key Vault en `SHARED ACCESS SIGNATURE` identiteiten worden ondersteund. Windows-gebruikers worden niet ondersteund.

Zie [referentie maken](/sql/t-sql/statements/create-credential-transact-sql) en [referenties wijzigen](/sql/t-sql/statements/alter-credential-transact-sql).

### <a name="cryptographic-providers"></a>Cryptografische providers

SQL Managed instance heeft geen toegang tot bestanden, zodat er geen cryptografische providers kunnen worden gemaakt:

- `CREATE CRYPTOGRAPHIC PROVIDER` wordt niet ondersteund. Zie [Cryptografische provider maken](/sql/t-sql/statements/create-cryptographic-provider-transact-sql).
- `ALTER CRYPTOGRAPHIC PROVIDER` wordt niet ondersteund. Zie [ALTER CRYPTOGRAPHIC provider](/sql/t-sql/statements/alter-cryptographic-provider-transact-sql).

### <a name="logins-and-users"></a>Aanmeldingen en gebruikers

- SQL-aanmeldingen die zijn gemaakt met behulp van `FROM CERTIFICATE` , `FROM ASYMMETRIC KEY` en `FROM SID` worden ondersteund. Zie [login maken](/sql/t-sql/statements/create-login-transact-sql).
- Azure Active Directory (Azure AD) server-principals (aanmeldingen) die zijn gemaakt met de syntaxis voor het maken van een [aanmelding](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) of de syntaxis voor het [maken van een gebruiker op basis van aanmelding [Azure AD login]](/sql/t-sql/statements/create-user-transact-sql?view=azuresqldb-mi-current) worden ondersteund. Deze aanmeldingen worden gemaakt op server niveau.

    SQL Managed instance ondersteunt Azure AD data base-principals met de syntaxis `CREATE USER [AADUser/AAD group] FROM EXTERNAL PROVIDER` . Deze functie is ook bekend als Azure AD, Inge sloten database gebruikers.

- Windows-aanmeldingen die met de `CREATE LOGIN ... FROM WINDOWS` syntaxis worden gemaakt, worden niet ondersteund. Gebruik Azure Active Directory aanmeldingen en gebruikers.
- De Azure AD-gebruiker die het exemplaar heeft gemaakt, heeft [onbeperkte beheerders bevoegdheden](../database/logins-create-manage.md).
- Gebruikers van Azure AD-data bases die geen beheerder zijn, kunnen worden gemaakt met behulp van de `CREATE USER ... FROM EXTERNAL PROVIDER` syntaxis. Zie [gebruiker maken... VAN externe PROVIDER](../database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities).
- Azure AD-server-principals (aanmeldingen) ondersteunen SQL-functies binnen één SQL Managed instance. Functies waarvoor cross-instance interacties zijn vereist, ongeacht of deze zich binnen dezelfde Azure AD-Tenant of andere tenants bevinden, worden niet ondersteund voor Azure AD-gebruikers. Voor beelden van deze functies zijn:

  - SQL transactionele replicatie.
  - Koppelings server.

- Het instellen van een Azure AD-aanmelding die is toegewezen aan een Azure AD-groep, wordt niet ondersteund voor de eigenaar van de data base.
- Imitatie van Azure AD-principals op server niveau met behulp van andere Azure AD-principals wordt ondersteund, zoals de component [Execute as](/sql/t-sql/statements/execute-as-transact-sql) . UITVOEREN als beperkingen zijn:

  - UITVOEREN als gebruiker wordt niet ondersteund voor Azure AD-gebruikers wanneer de naam verschilt van de aanmeldings naam. Een voor beeld hiervan is wanneer de gebruiker wordt gemaakt via de syntaxis CREATE USER [myAadUser] van LOGIN [ john@contoso.com ] en imitatie wordt geprobeerd via exec as User = _myAadUser_. Wanneer u een **gebruiker** maakt op basis van een Azure ad server-principal (aanmelden), geeft u de user_name op als dezelfde login_name van de **aanmelding**.
  - Alleen de principals op het SQL Server niveau (aanmeldingen) die deel uitmaken van de `sysadmin` rol kunnen de volgende bewerkingen uitvoeren die zijn gericht op Azure AD-principals:

    - EXECUTE AS USER
    - EXECUTE AS LOGIN

  - Om een gebruiker met de instructie EXECUTe AS te imiteren, moet de gebruiker rechtstreeks worden toegewezen aan de Azure AD-server principal (login). Gebruikers die lid zijn van Azure AD-groepen die zijn toegewezen aan Azure AD-server-principals, kunnen niet effectief worden geïmiteerd met de instructie EXECUTe AS, ook al is de aanroeper gemachtigd voor het imiteren van de opgegeven gebruikers naam.

- Het exporteren/importeren van een Data Base met Bacpac-bestanden wordt ondersteund voor Azure AD-gebruikers in een SQL Managed instance met behulp van [SSMS v 18.4 of hoger](/sql/ssms/download-sql-server-management-studio-ssms), of [SQLPackage.exe](/sql/tools/sqlpackage-download).
  - De volgende configuraties worden ondersteund met behulp van het Bacpac-bestand van de Data Base: 
    - Een Data Base tussen verschillende beheer exemplaren binnen hetzelfde Azure AD-domein exporteren/importeren.
    - Exporteer een Data Base van een SQL Managed instance en importeer deze in SQL Database binnen hetzelfde Azure AD-domein. 
    - Een Data Base exporteren uit SQL Database en importeren in een SQL Managed instance binnen hetzelfde Azure AD-domein.
    - Een Data Base exporteren uit SQL Managed instance en importeren in SQL Server (versie 2012 of hoger).
      - In deze configuratie worden alle Azure AD-gebruikers gemaakt als SQL Server Data Base-principals (gebruikers) zonder aanmeldingen. Het type gebruikers wordt weer gegeven als `SQL` en is zichtbaar als `SQL_USER` in sys.database_principals). De machtigingen en rollen blijven aanwezig in de meta gegevens van de data base van SQL Server en kunnen worden gebruikt voor imitatie. Ze kunnen echter niet worden gebruikt voor toegang tot en aanmelding bij de SQL Server met behulp van hun referenties.

- Alleen de principal-aanmelding op server niveau, die wordt gemaakt door de SQL Managed instance Provisioning proces, leden van de server functies, zoals `securityadmin` of `sysadmin` , of andere aanmeldingen met wijziging van de machtiging Aanmelden op server niveau, kunnen Azure ad server-principals (aanmeldingen) maken in de hoofd database voor SQL Managed instance.
- Als de aanmelding een SQL-principal is, kunnen alleen aanmeldingen die deel uitmaken van de rol `sysadmin` de opdracht create gebruiken om aanmeldingen te maken voor een Azure AD-account.
- De Azure AD-aanmelding moet lid zijn van een Azure AD in dezelfde map die wordt gebruikt voor Azure SQL Managed instance.
- Azure AD server-principals (aanmeldingen) zijn zichtbaar in Objectverkenner vanaf SQL Server Management Studio 18,0 Preview 5.
- Het is niet toegestaan Azure AD-server-principals (aanmeldingen) te overlappen met een Azure AD-beheerders account. Azure AD server-principals (aanmeldingen) hebben voor rang op de Azure AD-beheerder wanneer u de principal verhelpt en machtigingen toepast op een SQL Managed instance.
- Tijdens de verificatie wordt de volgende reeks toegepast om de verificatie-principal op te lossen:

    1. Als het Azure AD-account is gekoppeld aan de principal van de Azure AD-server (aanmelden), dat aanwezig is in sys.server_principals als type ' E ', toegang verlenen en machtigingen Toep assen van de Azure AD server-principal (aanmelden).
    2. Als het Azure AD-account lid is van een Azure AD-groep die is toegewezen aan de Azure AD-server principal (login), die aanwezig is in sys.server_principals als type X, toegang verlenen en machtigingen Toep assen van de aanmelding van de Azure AD-groep.
    3. Als het Azure AD-account een speciaal door de portal geconfigureerde Azure AD-beheerder voor SQL Managed instance is, dat zich niet in SQL Managed instance System views bevindt, moet u speciale vaste machtigingen Toep assen van de Azure AD-beheerder voor SQL Managed instance (legacy-modus).
    4. Als het Azure AD-account is gekoppeld aan een Azure AD-gebruiker in een Data Base, die aanwezig is in sys.database_principals als type E, toegang verlenen en machtigingen Toep assen van de gebruiker van de Azure AD-data base.
    5. Als het Azure AD-account lid is van een Azure AD-groep die is toegewezen aan een Azure AD-gebruiker in een Data Base, die aanwezig is in sys.database_principals als type X, toegang verlenen en machtigingen Toep assen van de Azure AD-groeps aanmelding.
    6. Als er een Azure AD-aanmelding is toegewezen aan een Azure AD-gebruikers account of een Azure AD-groeps account, die wordt omgezet naar de gebruiker die de verificatie uitvoert, worden alle machtigingen van deze Azure AD-aanmelding toegepast.

### <a name="service-key-and-service-master-key"></a>Service sleutel en service hoofd sleutel

- [Back-up van hoofd sleutel](/sql/t-sql/statements/backup-master-key-transact-sql) wordt niet ondersteund (beheerd door SQL database-service).
- Het terugzetten van de [hoofd sleutel](/sql/t-sql/statements/restore-master-key-transact-sql) wordt niet ondersteund (beheerd door de SQL database-service).
- De [back-up van de service hoofd sleutel](/sql/t-sql/statements/backup-service-master-key-transact-sql) wordt niet ondersteund (beheerd door SQL database-service).
- Het herstellen van de [service hoofd sleutel](/sql/t-sql/statements/restore-service-master-key-transact-sql) wordt niet ondersteund (beheerd door de SQL database-service).

## <a name="configuration"></a>Configuratie

### <a name="buffer-pool-extension"></a>Extensie van buffer groep

- De [buffergroepuitbreiding](/sql/database-engine/configure-windows/buffer-pool-extension) wordt niet ondersteund.
- `ALTER SERVER CONFIGURATION SET BUFFER POOL EXTENSION` wordt niet ondersteund. Zie [ALTER Server Configuration](/sql/t-sql/statements/alter-server-configuration-transact-sql).

### <a name="collation"></a>Sortering

De standaard sortering van exemplaren is `SQL_Latin1_General_CP1_CI_AS` en kan worden opgegeven als een aanmaak parameter. Zie [sorteringen](/sql/t-sql/statements/collations).

### <a name="compatibility-levels"></a>Compatibiliteitsniveaus

- Ondersteunde compatibiliteits niveaus zijn 100, 110, 120, 130, 140 en 150.
- Compatibiliteits niveaus onder 100 worden niet ondersteund.
- Het standaard compatibiliteits niveau voor nieuwe data bases is 140. Voor herstelde data bases blijft het compatibiliteits niveau ongewijzigd als het 100 en hoger.

Zie [ALTER data base Compatibility Level](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level).

### <a name="database-mirroring"></a>Databasespiegeling

Het spie gelen van data bases wordt niet ondersteund.

- `ALTER DATABASE SET PARTNER` en `SET WITNESS` opties worden niet ondersteund.
- `CREATE ENDPOINT … FOR DATABASE_MIRRORING` wordt niet ondersteund.

Zie [ALTER data base set partner en Witness instellen](/sql/t-sql/statements/alter-database-transact-sql-database-mirroring) en ENDPOINT maken voor meer informatie [. VOOR DATABASE_MIRRORING](/sql/t-sql/statements/create-endpoint-transact-sql).

### <a name="database-options"></a>Database opties

- Meerdere logboek bestanden worden niet ondersteund.
- In-Memory-objecten worden niet ondersteund in de servicelaag Algemeen. 
- Er is een limiet van 280 bestanden per Algemeen-exemplaar, wat een maximum van 280 bestanden per data base impliceert. De gegevens en logboek bestanden in de laag Algemeen worden meegeteld bij deze limiet. [De laag bedrijfskritiek ondersteunt 32.767 bestanden per data base](./resource-limits.md#service-tier-characteristics).
- De data base mag geen bestands groepen bevatten die FileStream-gegevens bevatten. Herstellen mislukt als. bak `FILESTREAM` gegevens bevat. 
- Elk bestand wordt in Azure Blob-opslag geplaatst. I/o en door Voer per bestand zijn afhankelijk van de grootte van elk afzonderlijk bestand.

#### <a name="create-database-statement"></a>Instructie CREATE data base

De volgende beperkingen zijn van toepassing op `CREATE DATABASE` :

- Bestanden en bestands groepen kunnen niet worden gedefinieerd. 
- De `CONTAINMENT` optie wordt niet ondersteund. 
- `WITH` opties worden niet ondersteund. 
   > [!TIP]
   > Als tijdelijke oplossing gebruikt u `ALTER DATABASE` na `CREATE DATABASE` om database opties in te stellen voor het toevoegen van bestanden of het instellen van containment. 

- De `FOR ATTACH` optie wordt niet ondersteund.
- De `AS SNAPSHOT OF` optie wordt niet ondersteund.

Zie [Create Data Base](/sql/t-sql/statements/create-database-sql-server-transact-sql)(Engelstalig) voor meer informatie.

#### <a name="alter-database-statement"></a>Instructie ALTER data base

Sommige bestands eigenschappen kunnen niet worden ingesteld of gewijzigd:

- Er kan geen bestandspad worden opgegeven in de `ALTER DATABASE ADD FILE (FILENAME='path')` t-SQL-instructie. Verwijderen `FILENAME` uit het script omdat SQL Managed instance automatisch de bestanden plaatst. 
- Een bestands naam kan niet worden gewijzigd met behulp van de- `ALTER DATABASE` instructie.

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

Sommige `ALTER DATABASE` instructies (bijvoorbeeld [set containment](/sql/relational-databases/databases/migrate-to-a-partially-contained-database#converting-a-database-to-partially-contained-using-transact-sql)) kunnen tijdelijk mislukken, bijvoorbeeld tijdens de automatische database back-up of direct nadat een Data Base is gemaakt. In dit geval `ALTER DATABASE` moet de instructie opnieuw worden uitgevoerd. Zie de [sectie opmerkingen](/sql/t-sql/statements/alter-database-transact-sql?preserve-view=true&tabs=sqlpool&view=azuresqldb-mi-current#remarks-2)voor meer informatie over verwante fout berichten.

Zie [ALTER data base](/sql/t-sql/statements/alter-database-transact-sql-file-and-filegroup-options)(Engelstalig) voor meer informatie.

### <a name="sql-server-agent"></a>SQL Server Agent

- Het in-en uitschakelen van SQL Server Agent wordt momenteel niet ondersteund in een SQL Managed instance. SQL Agent wordt altijd uitgevoerd.
- De trigger voor taak planning op basis van een niet-actieve CPU wordt niet ondersteund.
- SQL Server Agent-instellingen zijn alleen-lezen. De procedure `sp_set_agent_properties` wordt niet ondersteund in een SQL Managed instance. 
- Taken
  - T-SQL-taak stappen worden ondersteund.
  - De volgende replicatie taken worden ondersteund:
    - Transactie logboek lezer
    - Momentopname
    - Verdeler
  - SSIS-taak stappen worden ondersteund.
  - Andere typen taak stappen worden momenteel niet ondersteund:
    - De taak stap voor samenvoeg replicatie wordt niet ondersteund. 
    - Queue Reader wordt niet ondersteund. 
    - De opdracht shell wordt nog niet ondersteund.
  - SQL Managed instance heeft geen toegang tot externe bronnen, bijvoorbeeld netwerk shares via Robocopy. 
  - SQL Server Analysis Services wordt niet ondersteund.
- Meldingen worden gedeeltelijk ondersteund.
- E-mail meldingen worden ondersteund, maar hiervoor moet u een Database Mail profiel configureren. SQL Server Agent kunt slechts één Database Mail profiel gebruiken en het moet worden aangeroepen `AzureManagedInstance_dbmail_profile` . 
  - Paginering wordt niet ondersteund.
  - Netsend wordt niet ondersteund.
  - Waarschuwingen worden nog niet ondersteund.
  - Proxy's worden niet ondersteund.
- EventLog wordt niet ondersteund.
- De gebruiker moet rechtstreeks worden toegewezen aan de Azure AD-server principal (login) om SQL-Agent taken te maken, te wijzigen of uit te voeren. Gebruikers die niet rechtstreeks zijn toegewezen, bijvoorbeeld gebruikers die deel uitmaken van een Azure AD-groep die de rechten heeft om SQL-Agent taken te creëren, te wijzigen of uit te voeren, kunnen deze acties niet uitvoeren. Dit wordt veroorzaakt door Managed instance nabootsing en [Execute as-beperkingen](#logins-and-users).
- De functie voor het beheer van meerdere servers voor hoofd-en doel taken (MSX/TSX) wordt niet ondersteund.

Zie [SQL Server Agent](/sql/ssms/agent/sql-server-agent) voor meer informatie over SQL Server Agent.

### <a name="tables"></a>Tabellen

De volgende tabel typen worden niet ondersteund:

- [-](/sql/relational-databases/blob/filestream-sql-server)
- [BESTANDS tabel](/sql/relational-databases/blob/filetables-sql-server)
- [Externe tabel](/sql/t-sql/statements/create-external-table-transact-sql) (poly base)
- [MEMORY_OPTIMIZED](/sql/relational-databases/in-memory-oltp/introduction-to-memory-optimized-tables) (wordt niet alleen ondersteund in de algemeen-laag)

Zie [Create Table](/sql/t-sql/statements/create-table-transact-sql) en [ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql)voor meer informatie over het maken en wijzigen van tabellen.

## <a name="functionalities"></a>Functionaliteiten

### <a name="bulk-insert--openrowset"></a>Bulksgewijs invoegen/OPENROWSET

SQL Managed instance heeft geen toegang tot bestands shares en Windows-mappen, dus moeten de bestanden vanuit Azure Blob Storage worden geïmporteerd:

- `DATASOURCE` is vereist in de `BULK INSERT` opdracht tijdens het importeren van bestanden vanuit Azure Blob-opslag. Zie [Bulk Insert](/sql/t-sql/statements/bulk-insert-transact-sql).
- `DATASOURCE` is vereist in de `OPENROWSET` functie wanneer u de inhoud van een bestand leest uit Azure Blob-opslag. Zie [OPENrowset](/sql/t-sql/functions/openrowset-transact-sql).
- `OPENROWSET` kan worden gebruikt om gegevens te lezen van Azure SQL Database, Azure SQL Managed instance of SQL Server instances. Andere bronnen, zoals Oracle-data bases of Excel-bestanden, worden niet ondersteund.

### <a name="clr"></a>-

Een door SQL beheerd exemplaar heeft geen toegang tot bestands shares en Windows-mappen, dus de volgende beperkingen zijn van toepassing:

- Alleen `CREATE ASSEMBLY FROM BINARY` wordt ondersteund. Zie [Assembly maken van binair](/sql/t-sql/statements/create-assembly-transact-sql). 
- `CREATE ASSEMBLY FROM FILE` wordt niet ondersteund. Zie [Assembly maken op basis van een bestand](/sql/t-sql/statements/create-assembly-transact-sql).
- `ALTER ASSEMBLY` kan niet verwijzen naar bestanden. Zie [ALTER assembly](/sql/t-sql/statements/alter-assembly-transact-sql).

### <a name="database-mail-db_mail"></a>Database Mail (db_mail)
 - `sp_send_dbmail` kan geen bijlagen verzenden met behulp van een @file_attachments para meter. Lokaal bestands systeem en externe shares of Azure Blob Storage zijn niet toegankelijk vanuit deze procedure.
 - Zie de bekende problemen met betrekking tot `@query` para meter en authenticatie.
 
### <a name="dbcc"></a>DBCC

Niet-gedocumenteerde DBCC-instructies die zijn ingeschakeld in SQL Server, worden niet ondersteund in een SQL-beheerd exemplaar.

- Slechts een beperkt aantal globale tracerings vlaggen wordt ondersteund. Sessie niveau wordt `Trace flags` niet ondersteund. Zie [tracerings vlaggen](/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql).
- [DBCC TRACEOFF](/sql/t-sql/database-console-commands/dbcc-traceoff-transact-sql) en [DBCC TRACEON](/sql/t-sql/database-console-commands/dbcc-traceon-transact-sql) werken met het beperkte aantal globale traceer vlaggen.
- [DBCC CHECKDB](/sql/t-sql/database-console-commands/dbcc-checkdb-transact-sql) met opties REPAIR_ALLOW_DATA_LOSS, REPAIR_FAST en REPAIR_REBUILD kunnen niet worden gebruikt omdat de data base niet kan worden ingesteld in de `SINGLE_USER` modus-Zie [ALTER data base verschillen](#alter-database-statement). Mogelijke beschadiging van de data base wordt verwerkt door het ondersteunings team van Azure. Neem contact op met de ondersteuning van Azure als er sprake is van een beschadiging van de data base.

### <a name="distributed-transactions"></a>Gedistribueerde transacties

Gedeeltelijke ondersteuning voor [gedistribueerde trans acties](../database/elastic-transactions-overview.md) is momenteel beschikbaar als open bare preview. Ondersteunde scenario's zijn:
* Trans acties waarbij deel nemers alleen Azure SQL Managed instances zijn die deel uitmaken van de [vertrouwens groep](./server-trust-group-overview.md)van de server.
* Trans acties die worden geïnitieerd vanuit .NET (TransactionScope-klasse) en Transact-SQL.

Azure SQL Managed instance biedt momenteel geen ondersteuning voor andere scenario's die regel matig worden ondersteund door MSDTC on-premises of in azure Virtual Machines.

### <a name="extended-events"></a>Uitgebreide gebeurtenissen

Sommige Windows-specifieke doelen voor uitgebreide evenementen (XEvents) worden niet ondersteund:

- Het `etw_classic_sync` doel wordt niet ondersteund. `.xel`Bestanden opslaan in Azure Blob-opslag. Zie [etw_classic_sync doel](/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#etw_classic_sync_target-target).
- Het `event_file` doel wordt niet ondersteund. `.xel`Bestanden opslaan in Azure Blob-opslag. Zie [Doel van de event_file](/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#event_file-target).

### <a name="external-libraries"></a>Externe bibliotheken

Externe bibliotheken in-data base R en python worden ondersteund in een beperkte open bare preview-versie. Zie [Machine Learning Services in Azure SQL Managed instance (preview)](machine-learning-services-overview.md).

### <a name="filestream-and-filetable"></a>FileStream en bestands tabel

- FileStream-gegevens worden niet ondersteund.
- De data base mag geen bestands groepen met `FILESTREAM` gegevens bevatten.
- `FILETABLE` wordt niet ondersteund.
- Tabellen kunnen geen `FILESTREAM` typen hebben.
- De volgende functies worden niet ondersteund:
  - `GetPathLocator()`
  - `GET_FILESTREAM_TRANSACTION_CONTEXT()`
  - `PathName()`
  - `GetFileNamespacePat)`
  - `FileTableRootPath()`

Zie [FILESTREAM](/sql/relational-databases/blob/filestream-sql-server) en [FileTables](/sql/relational-databases/blob/filetables-sql-server)voor meer informatie.

### <a name="full-text-semantic-search"></a>Zoek opdracht in volledige tekst

[Semantisch zoeken](/sql/relational-databases/search/semantic-search-sql-server) wordt niet ondersteund.

### <a name="linked-servers"></a>Gekoppelde servers

Gekoppelde servers in SQL Managed instance ondersteunen een beperkt aantal doelen:

- Ondersteunde doelen zijn SQL Managed instance, SQL Database, Azure Synapse SQL [serverloze](https://devblogs.microsoft.com/azure-sql/linked-server-to-synapse-sql-to-implement-polybase-like-scenarios-in-managed-instance/) en dedicated pools en SQL Server exemplaren. 
- Gedistribueerde Beschrijf bare trans acties zijn alleen mogelijk voor beheerde exemplaren. Zie [gedistribueerde trans acties](../database/elastic-transactions-overview.md)voor meer informatie. MS DTC wordt echter niet ondersteund.
- Doelen die niet worden ondersteund zijn bestanden, Analysis Services en andere RDBMS. Gebruik een systeem eigen CSV-Import van Azure Blob Storage met `BULK INSERT` of `OPENROWSET` als alternatief voor het importeren van bestanden of het laden van bestand met een [serverloze SQL-groep in azure Synapse Analytics](https://devblogs.microsoft.com/azure-sql/linked-server-to-synapse-sql-to-implement-polybase-like-scenarios-in-managed-instance/).

Bewerkingen: 

- Trans acties voor [Cross-instance](../database/elastic-transactions-overview.md) write worden alleen ondersteund voor beheerde exemplaren.
- `sp_dropserver` wordt ondersteund voor het verwijderen van een gekoppelde server. Zie [sp_dropserver](/sql/relational-databases/system-stored-procedures/sp-dropserver-transact-sql).
- De `OPENROWSET` functie kan worden gebruikt om alleen query's uit te voeren op SQL Server exemplaren. Ze kunnen worden beheerd, on-premises of in virtuele machines. Zie [OPENrowset](/sql/t-sql/functions/openrowset-transact-sql).
- De `OPENDATASOURCE` functie kan worden gebruikt om alleen query's uit te voeren op SQL Server exemplaren. Ze kunnen worden beheerd, on-premises of in virtuele machines. Alleen de `SQLNCLI` `SQLNCLI11` waarden, en `SQLOLEDB` worden ondersteund als een provider. Een voorbeeld is `SELECT * FROM OPENDATASOURCE('SQLNCLI', '...').AdventureWorks2012.HumanResources.Employee`. Zie [OPENDATA source](/sql/t-sql/functions/opendatasource-transact-sql).
- Gekoppelde servers kunnen niet worden gebruikt voor het lezen van bestanden (Excel, CSV) van de netwerk shares. Probeer [Bulk Insert](/sql/t-sql/statements/bulk-insert-transact-sql#e-importing-data-from-a-csv-file), [OpenRowSet](/sql/t-sql/functions/openrowset-transact-sql#g-accessing-data-from-a-csv-file-with-a-format-file) , te gebruiken voor het lezen van CSV-bestanden van Azure Blob Storage of een [gekoppelde server die verwijst naar een serverloze SQL-groep in Synapse Analytics](https://devblogs.microsoft.com/azure-sql/linked-server-to-synapse-sql-to-implement-polybase-like-scenarios-in-managed-instance/). Deze aanvragen volgen voor het feedback-item van een [SQL Managed instance](https://feedback.azure.com/forums/915676-sql-managed-instance/suggestions/35657887-linked-server-to-non-sql-sources)|

### <a name="polybase"></a>PolyBase

Het enige beschik bare type externe bron is RDBMS (in open bare preview) van Azure SQL database, Azure SQL Managed instance en Azure Synapse pool. U kunt [een externe tabel gebruiken die verwijst naar een serverloze SQL-groep in Synapse Analytics](https://devblogs.microsoft.com/azure-sql/read-azure-storage-files-using-synapse-sql-external-tables/) als tijdelijke oplossing voor poly base externe tabellen die rechtstreeks vanuit de Azure-opslag worden gelezen. In Azure SQL Managed Instance kunt u gekoppelde servers gebruiken voor [een serverloze SQL-groep in Synapse Analytics](https://devblogs.microsoft.com/azure-sql/linked-server-to-synapse-sql-to-implement-polybase-like-scenarios-in-managed-instance/) of SQL Server voor het lezen van Azure Storage-gegevens.
Zie [poly base](/sql/relational-databases/polybase/polybase-guide)voor meer informatie over poly base.

### <a name="replication"></a>Replicatie

- De typen moment opname en bidirectionele replicatie worden ondersteund. Samenvoeg replicatie, peer-to-peer-replicatie en bij te werken abonnementen worden niet ondersteund.
- [Transactionele replicatie](replication-transactional-overview.md) is beschikbaar voor de open bare preview van een SQL Managed instance met enkele beperkingen:
    - Alle typen replicatie deelnemers (uitgever, distributeur, pull-abonnee en push-abonnee) kunnen worden geplaatst op een SQL Managed instance, maar de uitgever en de distributeur moeten zowel in de Cloud als in beide on-premises zijn.
    - SQL Managed instance kan communiceren met de recente versies van SQL Server. Zie de [matrix ondersteunde versies](replication-transactional-overview.md#supportability-matrix) voor meer informatie.
    - Transactionele replicatie heeft enkele [extra netwerk vereisten](replication-transactional-overview.md#requirements).

Zie de volgende zelf studies voor meer informatie over het configureren van transactionele replicatie:
- [Replicatie tussen een SQL MI-Uitgever en een SQL MI-abonnee](replication-between-two-instances-configure-tutorial.md)
- [Replicatie tussen een SQL MI-Publisher, een SQL MI-distributeur en een SQL Server-abonnee](replication-two-instances-and-sql-server-configure-tutorial.md)

### <a name="restore-statement"></a>Instructie Restore 

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
  - `FROM URL` (Azure Blob-opslag) is de enige optie die wordt ondersteund.
  - `FROM DISK`/`TAPE`/Backup-apparaat wordt niet ondersteund.
  - Back-upsets worden niet ondersteund.
- `WITH` opties worden niet ondersteund. Herstel pogingen `WITH` , zoals zoals `DIFFERENTIAL` , `STATS` , `REPLACE` , enzovoort, mislukken.
- `ASYNC RESTORE`: Het herstellen gaat verder, zelfs als de client verbinding is verbroken. Als de verbinding wordt verbroken, kunt u de `sys.dm_operation_status` weer gave voor de status van een herstel bewerking controleren en voor een Data Base maken en verwijderen. Zie [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database). 

De volgende database opties zijn ingesteld of worden overschreven en kunnen later niet meer worden gewijzigd: 

- `NEW_BROKER` Als de Broker niet is ingeschakeld in het bak-bestand. 
- `ENABLE_BROKER` Als de Broker niet is ingeschakeld in het bak-bestand. 
- `AUTO_CLOSE=OFF` Als een data base in het. bak-bestand is `AUTO_CLOSE=ON` . 
- `RECOVERY FULL` Als een data base in het. bak-bestand `SIMPLE` of de `BULK_LOGGED` herstel modus.
- Een bestands groep die is geoptimaliseerd voor geheugen wordt toegevoegd met de naam XTP als deze niet voor komt in het bron. bak-bestand. 
- De naam van een bestaande bestands groep met geoptimaliseerd geheugen is gewijzigd in XTP. 
- `SINGLE_USER` en `RESTRICTED_USER` opties worden geconverteerd naar `MULTI_USER` .

Beperkingen: 

- Back-ups van de beschadigde data bases kunnen worden hersteld, afhankelijk van het type beschadiging, maar automatische back-ups worden pas uitgevoerd als de beschadiging is opgelost. Zorg ervoor dat u uitvoert `DBCC CHECKDB` op het door SQL beheerde bron exemplaar en gebruik back-up `WITH CHECKSUM` om dit probleem te voor komen.
- Herstellen van `.BAK` een bestand met een beperking die in dit document is beschreven (bijvoorbeeld `FILESTREAM` of `FILETABLE` objecten) kan niet worden hersteld voor een SQL-beheerd exemplaar.
- `.BAK` bestanden die meerdere back-upsets bevatten, kunnen niet worden hersteld. 
- `.BAK` bestanden die meerdere logboek bestanden bevatten, kunnen niet worden hersteld.
- Back-ups die data bases bevatten die groter zijn dan 8 TB, actieve in-memory OLTP objecten of het aantal bestanden dat groter zou zijn dan 280 bestanden per exemplaar, kunnen niet worden hersteld op een Algemeen-exemplaar. 
- Back-ups met data bases die groter zijn dan 4 TB of in-memory OLTP objecten met de totale grootte die groter is dan de grootte die is beschreven in [resource limieten](resource-limits.md) , kunnen niet worden hersteld op bedrijfskritiek exemplaar.
Zie [Restore statements (instructies herstellen](/sql/t-sql/statements/restore-statements-transact-sql)) voor meer informatie over Restore-instructies.

 > [!IMPORTANT]
 > Dezelfde beperkingen zijn van toepassing op de ingebouwde herstel bewerking naar een bepaald tijdstip. Zo kan Algemeen data base van meer dan 4 TB niet worden hersteld op Bedrijfskritiek exemplaar. Bedrijfskritiek data base met OLTP-bestanden in het geheugen of meer dan 280 bestanden kunnen niet worden hersteld op Algemeen exemplaar.

### <a name="service-broker"></a>Service Broker

Service Broker met meerdere exemplaren wordt niet ondersteund:

- `sys.routes`: Als u een vereiste hebt, moet u het adres selecteren in sys. routes. Het adres moet lokaal op elke route zijn. Zie [sys. routes](/sql/relational-databases/system-catalog-views/sys-routes-transact-sql).
- `CREATE ROUTE`: U kunt niet gebruiken `CREATE ROUTE` met `ADDRESS` andere dan `LOCAL` . Zie [route maken](/sql/t-sql/statements/create-route-transact-sql).
- `ALTER ROUTE`: U kunt niet gebruiken `ALTER ROUTE` met `ADDRESS` andere dan `LOCAL` . Zie [ALTER route](/sql/t-sql/statements/alter-route-transact-sql). 

Service Broker is standaard ingeschakeld en kan niet worden uitgeschakeld. De volgende opties voor ALTER database worden niet ondersteund:
- `ENABLE_BROKER`
- `DISABLE_BROKER`

### <a name="stored-procedures-functions-and-triggers"></a>Opgeslagen procedures, functies en triggers

- `NATIVE_COMPILATION` wordt niet ondersteund in de laag Algemeen.
- De volgende [sp_configure](/sql/relational-databases/system-stored-procedures/sp-configure-transact-sql) opties worden niet ondersteund: 
  - `allow polybase export`
  - `allow updates`
  - `filestream_access_level`
  - `remote access`
  - `remote data archive`
  - `remote proc trans`
  - `scan for startup procs`
- `sp_execute_external_scripts` wordt niet ondersteund. Zie [sp_execute_external_scripts](/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql#examples).
- `xp_cmdshell` wordt niet ondersteund. Zie [xp_cmdshell](/sql/relational-databases/system-stored-procedures/xp-cmdshell-transact-sql).
- `Extended stored procedures` worden niet ondersteund. Dit omvat `sp_addextendedproc` en `sp_dropextendedproc` . Deze functionaliteit wordt niet ondersteund omdat deze zich op een afschaffen pad voor SQL Server bevinden. Zie [uitgebreide opgeslagen procedures](/sql/relational-databases/extended-stored-procedures-programming/database-engine-extended-stored-procedures-programming)voor meer informatie.
- `sp_attach_db`, `sp_attach_single_file_db` en `sp_detach_db` worden niet ondersteund. Zie [sp_attach_db](/sql/relational-databases/system-stored-procedures/sp-attach-db-transact-sql), [sp_attach_single_file_db](/sql/relational-databases/system-stored-procedures/sp-attach-single-file-db-transact-sql)en [sp_detach_db](/sql/relational-databases/system-stored-procedures/sp-detach-db-transact-sql).

### <a name="system-functions-and-variables"></a>Systeem functies en-variabelen

De volgende variabelen, functies en weer gaven retour neren verschillende resultaten:

- `SERVERPROPERTY('EngineEdition')` retourneert de waarde 8. Met deze eigenschap wordt een unieke id van een SQL beheerd exemplaar aangeduid. Zie [Server Property](/sql/t-sql/functions/serverproperty-transact-sql).
- `SERVERPROPERTY('InstanceName')` retourneert NULL omdat het concept van instance bestaat voor SQL Server niet van toepassing is op een SQL-beheerd exemplaar. Zie [Server Property (' instanceName ')](/sql/t-sql/functions/serverproperty-transact-sql).
- `@@SERVERNAME` retourneert een volledige DNS-naam (' connectable '), bijvoorbeeld my-managed-instance.wcus17662feb9ce98.database.windows.net. Zie [@ @SERVERNAME ](/sql/t-sql/functions/servername-transact-sql). 
- `SYS.SERVERS` retourneert de volledige DNS-naam ' connectable ', zoals `myinstance.domain.database.windows.net` voor de eigenschappen ' name ' en ' data_source '. Zie [sys. SERVERS](/sql/relational-databases/system-catalog-views/sys-servers-transact-sql).
- `@@SERVICENAME` retourneert NULL omdat het concept van de service bestaat voor SQL Server niet van toepassing is op een SQL-beheerd exemplaar. Zie [@ @SERVICENAME ](/sql/t-sql/functions/servicename-transact-sql).
- `SUSER_ID` wordt ondersteund. Er wordt NULL geretourneerd als de Azure AD-aanmelding zich niet in sys.sysaanmeldingen bevindt. Zie [SUSER_ID](/sql/t-sql/functions/suser-id-transact-sql). 
- `SUSER_SID` wordt niet ondersteund. De verkeerde gegevens worden geretourneerd. Dit is een tijdelijk bekend probleem. Zie [SUSER_SID](/sql/t-sql/functions/suser-sid-transact-sql). 

## <a name="environment-constraints"></a><a name="Environment"></a>Omgevings beperkingen

### <a name="subnet"></a>Subnet
-  U kunt geen andere resources (bijvoorbeeld virtuele machines) plaatsen in het subnet waar u uw SQL Managed instance hebt geïmplementeerd. Implementeer deze bronnen met behulp van een ander subnet.
- Het subnet moet voldoende beschik bare [IP-adressen](connectivity-architecture-overview.md#network-requirements)hebben. Minimale moet ten minste 32 IP-adressen hebben in het subnet.
- Het aantal vCores en typen instanties dat u in een regio kunt implementeren, hebben een aantal [beperkingen en](resource-limits.md#regional-resource-limitations)beperkingen.
- Er is een [netwerk configuratie](connectivity-architecture-overview.md#network-requirements) die op het subnet moet worden toegepast.

### <a name="vnet"></a>VNET
- VNet kan worden geïmplementeerd met behulp van resource model-Klassiek model voor VNet wordt niet ondersteund.
- Nadat een door SQL beheerd exemplaar is gemaakt, wordt het door SQL beheerde exemplaar of VNet naar een andere resource groep of een ander abonnement niet ondersteund.
- Voor SQL Managed instances die worden gehost in virtuele clusters die zijn gemaakt voordat 9/22/2020 [Global peering](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) wordt ondersteund, wordt niet toegestaan. U kunt via VNet-gateways verbinding maken met deze resources via ExpressRoute of VNet-naar-VNet.

### <a name="failover-groups"></a>Failovergroepen
Systeem databases worden niet gerepliceerd naar het secundaire exemplaar in een failovergroep. Daarom zijn scenario's die afhankelijk zijn van objecten van de systeem databases niet mogelijk op het secundaire exemplaar, tenzij de objecten hand matig op de secundaire instantie worden gemaakt.

### <a name="tempdb"></a>TEMPDB
- De maximale bestands grootte van `tempdb` mag niet groter zijn dan 24 GB per kern op een algemeen laag. De maximale `tempdb` grootte van een bedrijfskritiek laag wordt beperkt door de opslag grootte van het SQL-beheerde exemplaar. `Tempdb` de grootte van het logboek bestand is beperkt tot 120 GB op Algemeen laag. Sommige query's retour neren mogelijk een fout als deze meer dan 24 GB per kern nodig heeft `tempdb` of als er meer dan 120 GB aan logboek gegevens worden geproduceerd.
- `Tempdb` is altijd onderverdeeld in 12 gegevens bestanden: 1 primair, ook wel Master, Data File en 11 niet-primaire gegevens bestanden genoemd. Kan de bestands structuur niet wijzigen en er kunnen geen nieuwe bestanden aan worden toegevoegd `tempdb` . 
- [ `tempdb` Meta gegevens](/sql/relational-databases/databases/tempdb-database?view=sql-server-ver15#memory-optimized-tempdb-metadata)die zijn geoptimaliseerd voor geheugen, een nieuwe SQL Server 2019 in-Memory database functie, worden niet ondersteund.
- Objecten die in de model database zijn gemaakt, kunnen niet automatisch worden gemaakt `tempdb` na het opnieuw opstarten of een failover, omdat de `tempdb` initiële objecten lijst niet wordt opgehaald uit de model database. U moet objecten `tempdb` hand matig maken na elke herstart of een failover.

### <a name="msdb"></a>MSDB

De volgende MSDB-schema's in het SQL Managed instance moeten het eigendom zijn van hun respectieve vooraf gedefinieerde rollen:

- Algemene rollen
  - TargetServersRole
- [Vaste database rollen](/sql/ssms/agent/sql-server-agent-fixed-database-roles?view=sql-server-ver15)
  - SQLAgentUserRole
  - SQLAgentReaderRole
  - SQLAgentOperatorRole
- [DatabaseMail rollen](/sql/relational-databases/database-mail/database-mail-configuration-objects?view=sql-server-ver15#DBProfile):
  - DatabaseMailUserRole
- [Integratie Services-rollen](/sql/integration-services/security/integration-services-roles-ssis-service?view=sql-server-ver15):
  - db_ssisadmin
  - db_ssisltduser
  - db_ssisoperator
  
> [!IMPORTANT]
> Het wijzigen van de namen van de vooraf gedefinieerde rollen, schema namen en schema-eigen aren door klanten is van invloed op de normale werking van de service. Eventuele wijzigingen die u aanbrengt, worden teruggedraaid naar de vooraf gedefinieerde waarden zodra deze zijn gedetecteerd, of bij de volgende service-update, om een normale service bewerking te garanderen.

### <a name="error-logs"></a>Foutenlogboeken

SQL Managed instance plaatst uitgebreide informatie in fouten Logboeken. Er zijn veel interne systeem gebeurtenissen vastgelegd in het fouten logboek. Gebruik een aangepaste procedure om fout logboeken te lezen die een aantal irrelevante vermeldingen filteren. Zie voor meer informatie [SQL Managed instance-sp_readmierrorlog](/archive/blogs/sqlcat/azure-sql-db-managed-instance-sp_readmierrorlog) of [SQL Managed instance extension (preview)](/sql/azure-data-studio/azure-sql-managed-instance-extension#logs) voor Azure Data Studio.

## <a name="next-steps"></a>Volgende stappen

- Zie [Wat is SQL Managed instance?](sql-managed-instance-paas-overview.md) voor meer informatie over SQL Managed instance?
- Zie [functie vergelijking van Azure SQL Managed instance](../database/features-comparison.md)voor een overzicht van functies en vergelijkingen.
- Zie [release opmerkingen voor SQL Managed instance](../database/doc-changes-updates-release-notes.md) (Engelstalig) voor release-updates en bekende problemen.
- Zie [Create a SQL Managed instance](instance-create-quickstart.md)(Engelstalig) voor een Snelstartgids waarin wordt uitgelegd hoe u een nieuw exemplaar van SQL Managed maakt.
