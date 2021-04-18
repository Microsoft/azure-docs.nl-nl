---
title: Wat is nieuw?
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Meer informatie over de nieuwe functies en documentatieverbeteringen voor Azure SQL Database & SQL Managed Instance.
services: sql-database
author: stevestein
ms.service: sql-db-mi
ms.subservice: service
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
ms.date: 04/17/2021
ms.author: sstein
ms.openlocfilehash: 81c306ac2a8a5c00c5d06877974db7e04964c76b
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600910"
---
# <a name="whats-new-in-azure-sql-database--sql-managed-instance"></a>Wat is er nieuw in Azure SQL Database & SQL Managed Instance?
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Dit artikel bevat Azure SQL Database en Azure SQL Managed Instance functies die zich momenteel in de openbare preview-versie. Zie SQL Database service SQL Managed Instance updates voor meer informatie [SQL Database & SQL Managed Instance en verbeteringen.](https://azure.microsoft.com/updates/?product=sql-database) Zie Service-updates voor updates en verbeteringen in [andere Azure-services.](https://azure.microsoft.com/updates)

## <a name="whats-new"></a>Wat is er nieuw?

Documentatie voor Azure SQL Database en Azure SQL Managed Instance is opgesplitst in afzonderlijke secties. We hebben ook bijgewerkt hoe we naar een beheerd exemplaar verwijzen van Azure SQL Database *beheerde exemplaar* naar *Azure SQL Managed Instance*.

We hebben dit gedaan omdat sommige functies en functionaliteiten sterk variëren tussen één database en een beheerd exemplaar. Het wordt steeds lastiger om complexe nuances tussen Azure SQL Database en Azure SQL Managed Instance in afzonderlijke gedeelde artikelen uit te leggen.

Deze verduidelijking tussen de verschillende Azure SQL-producten moet het proces van het werken met de SQL Server-database-engine in Azure vereenvoudigen en stroomlijnen, of het nu gaat om één beheerde database in Azure SQL Database, een volledig volwassen beheerd exemplaar dat meerdere databases in Azure SQL Managed Instance host of het vertrouwde on-premises SQL Server-product dat wordt gehost op een virtuele machine in Azure.

Houd er rekening mee dat dit een werk is dat wordt uitgevoerd en dat nog niet elk artikel is bijgewerkt. Documentatie voor Transact-SQL-instructies (T-SQL), opgeslagen procedures en veel functies die worden gedeeld tussen Azure SQL Database en Azure SQL Managed Instance zijn bijvoorbeeld nog niet voltooid. Daarom zijn we blij met uw geduld als we doorgaan met het verduidelijken van de inhoud. 

Deze tabel bevat een snelle vergelijking van de wijziging in terminologie: 


|**Nieuwe term**  | **Vorige term**  |**Uitleg** |
|---------|---------|---------|
|**Azure SQL Managed Instance** | Azure SQL Database beheerd *exemplaar*| Azure SQL Managed Instance is een eigen product binnen de Azure SQL-familie, in plaats van alleen een implementatieoptie binnen Azure SQL Database. | 
|**Azure SQL Database**|Azure SQL Database individuele *database*| Tenzij anders expliciet opgegeven, bevat de productnaam Azure SQL Database zowel individuele databases als databases die zijn geïmplementeerd in een elastische pool. |
|**Azure SQL Database**|Azure SQL Database *elastische pool*| Tenzij anders expliciet opgegeven, bevat de productnaam Azure SQL Database zowel individuele databases als databases die zijn geïmplementeerd in een elastische pool.  |
|**Azure SQL Database** |Azure SQL Database | Hoewel de term hetzelfde blijft, geldt deze nu alleen voor implementaties met één database en elastische pool, en omvat deze niet het beheerde exemplaar. |
| **Azure SQL**| N.v.t. | Dit verwijst naar de familie van SQL Server-database-engineproducten die beschikbaar zijn in Azure: Azure SQL Database, Azure SQL Managed Instance en SQL Server op Azure-VM's. | 

## <a name="features-in-public-preview"></a>Functies in openbare preview

### <a name="azure-sql-database"></a>[Azure SQL Database](#tab/single-database)

| Functie | Details |
| ---| --- |
| Taken voor elastische databases (preview) | Zie Elastische taken [maken, configureren en beheren voor meer informatie.](elastic-jobs-overview.md) |
| Elastische query’s | Zie Overzicht van [elastische query's voor meer informatie.](elastic-query-overview.md) |
| Elastische transacties | [Gedistribueerde transacties over clouddatabases.](elastic-transactions-overview.md) |
| Queryeditor in de Azure Portal |Zie De [SQL Azure Portal query-editor](connect-query-portal.md)van de Azure Portal gebruiken om verbinding te maken en query's uit te voeren op gegevens voor meer informatie.|
|SQL Analytics|Zie voor meer [informatie Azure SQL-analyse](../../azure-monitor/insights/azure-sql.md).|
| &nbsp; |

### <a name="azure-sql-managed-instance"></a>[Azure SQL Managed Instance](#tab/managed-instance)

| Functie | Details |
| ---| --- |
| [Gedistribueerde transacties](/azure/azure-sql/database/elastic-transactions-overview) | Gedistribueerde transacties over beheerde exemplaren. |
| [Exemplaargroepen](/azure/sql-database/sql-database-instance-pools) | Een handige en kostenefficiënte manier om kleinere SQL-exemplaren naar de cloud te migreren. |
| [Azure AD-server-principals op exemplaarniveau (aanmeldingen)](/sql/t-sql/statements/create-login-transact-sql) | Maak aanmeldingen op exemplaarniveau met behulp van [de instructie CREATE LOGIN FROM EXTERNAL PROVIDER.](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current&preserve-view=true) |
| [Transactionele replicatie](../managed-instance/replication-transactional-overview.md) | Repliceer de wijzigingen van uw tabellen in andere databases in SQL Managed Instance, SQL Database of SQL Server. Of werk uw tabellen bij wanneer sommige rijen worden gewijzigd in andere exemplaren van SQL Managed Instance of SQL Server. Zie Replicatie configureren [in Azure SQL Managed Instance.](../managed-instance/replication-between-two-instances-configure-tutorial.md) |
| Detectie van bedreigingen |Zie Detectie van bedreigingen [configureren in Azure SQL Managed Instance.](../managed-instance/threat-detection-configure.md)|
| Langetermijnretentie van back-ups | Zie Langetermijnretentie van [back-up configureren in Azure SQL Managed Instance](../managed-instance/long-term-backup-retention-configure.md), die momenteel in beperkte openbare preview is. |

---

## <a name="new-features"></a>Nieuwe functies

### <a name="sql-managed-instance-h2-2019-updates"></a>SQL Managed Instance H2 2019-updates

- [Service-aided subnetconfiguratie](https://azure.microsoft.com/updates/service-aided-subnet-configuration-for-managed-instance-in-azure-sql-database-available/) is een veilige en handige manier om de subnetconfiguratie te beheren waarbij u gegevensverkeer beheert terwijl SQL Managed Instance zorgt voor de ononderbroken stroom van beheerverkeer.
- [Transparent Data Encryption (TDE) met Bring Your Own Key (BYOK)](https://azure.microsoft.com/updates/general-avilability-transparent-data-encryption-with-customer-managed-keys-for-azure-sql-database-managed-instance/) maakt een BYOK-scenario (Bring Your Own Key) mogelijk voor data-at-rest-beveiliging en stelt organisaties in staat om beheertaken voor sleutels en gegevens te scheiden.
- [Met groepen voor automatische failover](https://azure.microsoft.com/updates/azure-sql-database-auto-failover-groups-feature-now-available-in-all-regions/) kunt u alle databases van het primaire exemplaar repliceren naar een secundair exemplaar in een andere regio.
- [Met globale traceervlaggen](https://azure.microsoft.com/updates/global-trace-flags-are-now-available-in-azure-sql-database-managed-instance/) kunt u SQL Managed Instance configureren.

### <a name="sql-managed-instance-h1-2019-updates"></a>SQL Managed Instance H1 2019-updates

De volgende functies zijn ingeschakeld in het SQL Managed Instance-implementatiemodel in H1 2019:
  - Ondersteuning voor abonnementen met maandelijks <a href="/azure/azure-sql/managed-instance/resource-limits">Azure-tegoed</a> voor Visual Studio en verhoogde [regionale limieten.](../managed-instance/resource-limits.md#regional-resource-limitations)
  - Ondersteuning voor <a href="/sharepoint/administration/deploy-azure-sql-managed-instance-with-sharepoint-servers-2016-2019"> SharePoint 2016 en SharePoint 2019 </a> en <a href="/business-applications-release-notes/october18/dynamics365-business-central/support-for-azure-sql-database-managed-instance"> Dynamics 365 Business Central. </a>
  - Maak een beheerd exemplaar met <a href="/azure/azure-sql/managed-instance/scripts/create-powershell-azure-resource-manager-template">een collatie op exemplaarniveau</a> en een <a href="https://azure.microsoft.com/updates/managed-instance-time-zone-ga/">tijdzone</a> naar keuze.
  - Beheerde exemplaren zijn nu beveiligd met [de ingebouwde firewall](../managed-instance/management-endpoint-verify-built-in-firewall.md).
  - Configureer SQL Managed Instance om openbare eindpunten [te](../managed-instance/public-endpoint-configure.md)gebruiken, de [proxyverbinding](connectivity-architecture.md#connection-policy) te overschrijven voor betere netwerkprestaties, <a href="https://aka.ms/four-cores-sql-mi-update"> 4 vCores op Gen5-hardwaregeneratie</a> of het configureren van back-upretentie tot <a href="/azure/azure-sql/database/automated-backups-overview">35</a> dagen voor herstel naar een bepaald tijdstip. [Langetermijnretentie van back-ups](long-term-retention-overview.md) (maximaal 10 jaar) is momenteel beschikbaar als openbare preview.  
  - Met nieuwe functies kunt u uw <a href="https://medium.com/@jocapc/geo-restore-your-databases-on-azure-sql-instances-1451480e90fa">database geo-herstellen</a>naar een ander datacenter met behulp van PowerShell, de naam [van de database wijzigen](https://azure.microsoft.com/updates/azure-sql-database-managed-instance-database-rename-is-supported/)en het virtuele cluster [verwijderen.](../managed-instance/virtual-cluster-delete.md)
  - De nieuwe ingebouwde rol [Instantiebijdrager](../../role-based-access-control/built-in-roles.md#sql-managed-instance-contributor) maakt scheiding van rechten (SoD) met beveiligingsprincipes en naleving van bedrijfsstandaarden mogelijk.
  - SQL Managed Instance is beschikbaar in de volgende regio Azure Government s voor ga (US Gov - Texas, US Gov - Arizona) en in China - noord 2 en China - oost 2. Het is ook beschikbaar in de volgende openbare regio's: Australië - centraal, Australië - centraal 2, Brazilië - zuid, Frankrijk - zuid, VAE - centraal, VAE - noord, Zuid-Afrika - noord, Zuid-Afrika - west.

## <a name="known-issues"></a>Bekende problemen

|Probleem  |Datum ontdekt  |Status  |Datum opgelost  |
|---------|---------|---------|---------|
|[Het wijzigen van het verbindingstype heeft geen invloed op verbindingen via het eindpunt van de failovergroep](#changing-the-connection-type-does-not-affect-connections-through-the-failover-group-endpoint)|Jan 2021|Heeft een tijdelijke oplossing||
|[Procedure sp_send_dbmail tijdelijk mislukken wanneer @query de parameter wordt gebruikt](#procedure-sp_send_dbmail-may-transiently-fail-when--parameter-is-used)|Jan 2021|Heeft een tijdelijke oplossing||
|[Gedistribueerde transacties kunnen worden uitgevoerd nadat het beheerde exemplaar is verwijderd uit de vertrouwensgroep van de server](#distributed-transactions-can-be-executed-after-removing-managed-instance-from-server-trust-group)|Okt 2020|Heeft een tijdelijke oplossing||
|[Gedistribueerde transacties kunnen niet worden uitgevoerd na een bewerking voor het schalen van een beheerd exemplaar](#distributed-transactions-cannot-be-executed-after-managed-instance-scaling-operation)|Okt 2020|Heeft een tijdelijke oplossing||
|[BULK INSERT](/sql/t-sql/statements/bulk-insert-transact-sql) / [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql) in Azure SQL en instructie in Managed Instance kan `BACKUP` / `RESTORE` azure AD-identiteit beheren niet gebruiken om te verifiëren bij Azure Storage|Sep 2020|Heeft een tijdelijke oplossing||
|[Service-principal heeft geen toegang tot Azure AD en AKV](#service-principal-cannot-access-azure-ad-and-akv)|Aug 2020|Heeft een tijdelijke oplossing||
|[Herstellen van handmatige back-up zonder CHECKSUM kan mislukken](#restoring-manual-backup-without-checksum-might-fail)|Mei 2020|Opgelost|Juni 2020|
|[Agent reageert niet meer bij het wijzigen, uitschakelen of inschakelen van bestaande taken](#agent-becomes-unresponsive-upon-modifying-disabling-or-enabling-existing-jobs)|Mei 2020|Opgelost|Juni 2020|
|[Machtigingen voor de resourcegroep worden niet toegepast op SQL Managed Instance](#permissions-on-resource-group-not-applied-to-sql-managed-instance)|Februari 2020|Opgelost|November 2020|
|[Beperking van handmatige failover via de portal voor failovergroepen](#limitation-of-manual-failover-via-portal-for-failover-groups)|Jan 2020|Heeft een tijdelijke oplossing||
|[SQL Agent-rollen hebben expliciete EXECUTE-machtigingen nodig voor niet-sysadmin-aanmeldingen](#in-memory-oltp-memory-limits-are-not-applied)|Dec 2019|Heeft een tijdelijke oplossing||
|[SQL Agent-taken kunnen worden onderbroken door het opnieuw opstarten van het agentproces](#sql-agent-jobs-can-be-interrupted-by-agent-process-restart)|Dec 2019|Opgelost|Maart 2020|
|[Azure AD-aanmeldingen en -gebruikers worden niet ondersteund in SSDT](#azure-ad-logins-and-users-are-not-supported-in-ssdt)|November 2019|Geen tijdelijke oplossing||
|[In-memory OLTP-geheugenlimieten worden niet toegepast](#in-memory-oltp-memory-limits-are-not-applied)|Okt 2019|Heeft een tijdelijke oplossing||
|[Fout geretourneerd tijdens het verwijderen van een bestand dat niet leeg is](#wrong-error-returned-while-trying-to-remove-a-file-that-is-not-empty)|Okt 2019|Heeft een tijdelijke oplossing||
|[Servicelaag wijzigen en exemplaarbewerkingen maken worden geblokkeerd door doorlopend databaseherstel](#change-service-tier-and-create-instance-operations-are-blocked-by-ongoing-database-restore)|Sep 2019|Heeft een tijdelijke oplossing||
|[Resource Governor op Bedrijfskritiek servicelaag moeten mogelijk opnieuw worden geconfigureerd na een failover](#resource-governor-on-business-critical-service-tier-might-need-to-be-reconfigured-after-failover)|Sep 2019|Heeft een tijdelijke oplossing||
|[Dialoogvensters voor Service Broker database moeten opnieuw worden geherinialiseerd na de upgrade van de servicelaag](#cross-database-service-broker-dialogs-must-be-reinitialized-after-service-tier-upgrade)|Aug 2019|Heeft een tijdelijke oplossing||
|[Imitatie van Azure AD-aanmeldingstypen wordt niet ondersteund](#impersonation-of-azure-ad-login-types-is-not-supported)|Jul 2019|Geen tijdelijke oplossing||
|[@query parameter wordt niet ondersteund in sp_send_db_mail](#-parameter-not-supported-in-sp_send_db_mail)|Apr 2019|Opgelost|Jan 2021|
|[Transactionele replicatie moet opnieuw worden geconfigureerd na geo-failover](#transactional-replication-must-be-reconfigured-after-geo-failover)|Maart 2019|Geen tijdelijke oplossing||
|[Tijdelijke database wordt gebruikt tijdens herstelbewerking](#temporary-database-is-used-during-restore-operation)||Heeft een tijdelijke oplossing||
|[TEMPDB-structuur en -inhoud worden opnieuw gemaakt](#tempdb-structure-and-content-is-re-created)||Geen tijdelijke oplossing||
|[Opslagruimte met kleine databasebestanden overschrijden](#exceeding-storage-space-with-small-database-files)||Heeft een tijdelijke oplossing||
|[GUID-waarden die worden weergegeven in plaats van databasenamen](#guid-values-shown-instead-of-database-names)||Heeft een tijdelijke oplossing||
|[Foutlogboeken worden niet persistent gemaakt](#error-logs-arent-persisted)||Geen tijdelijke oplossing||
|[Transactiebereik voor twee databases binnen hetzelfde exemplaar wordt niet ondersteund](#transaction-scope-on-two-databases-within-the-same-instance-isnt-supported)||Heeft een tijdelijke oplossing|Maart 2020|
|[CLR-modules en gekoppelde servers kunnen soms niet verwijzen naar een lokaal IP-adres](#clr-modules-and-linked-servers-sometimes-cant-reference-a-local-ip-address)||Heeft een tijdelijke oplossing||
|Databaseconsistentie wordt niet geverifieerd met DBCC CHECKDB na het herstellen van de database Azure Blob Storage.||Opgelost|November 2019|
|Herstel naar een bepaald tijdstip van de database van Bedrijfskritiek naar Algemeen-laag slaagt niet als de brondatabase in-memory OLTP bevat.||Opgelost|Okt 2019|
|Database mailfunctie met externe (niet-Azure) e-mailservers met behulp van een beveiligde verbinding||Opgelost|Okt 2019|
|Ingesloten databases worden niet ondersteund in SQL Managed Instance||Opgelost|Aug 2019|

### <a name="changing-the-connection-type-does-not-affect-connections-through-the-failover-group-endpoint"></a>Het wijzigen van het verbindingstype heeft geen invloed op verbindingen via het eindpunt van de failovergroep

Als een exemplaar deelneemt aan een groep voor automatische [](https://docs.microsoft.com/azure/azure-sql/managed-instance/connection-types-overview) failover, wordt het wijzigen van het verbindingstype van het exemplaar niet van kracht voor de verbindingen die tot stand zijn gebracht via het [listener-eindpunt](https://docs.microsoft.com/azure/azure-sql/database/auto-failover-group-overview)van de failovergroep.

**Tijdelijke oplossing:** u kunt de groep voor automatische failovers neerzetten en opnieuw maken door het verbindingstype te wijzigen.

### <a name="procedure-sp_send_dbmail-may-transiently-fail-when-query-parameter-is-used"></a>Procedure sp_send_dbmail tijdelijk mislukken wanneer @query de parameter wordt gebruikt

Procedure sp_send_dbmail tijdelijk mislukken wanneer `@query` de parameter wordt gebruikt. Wanneer dit probleem optreedt, mislukt elke tweede uitvoering van de procedure sp_send_dbmail fout en `Msg 22050, Level 16, State 1` bericht `Failed to initialize sqlcmd library with error number -2147467259` . Als u deze fout goed wilt zien, moet de procedure worden aangeroepen met de standaardwaarde 0 voor de parameter , anders wordt de fout `@exclude_query_output` niet doorgegeven.
Dit probleem wordt veroorzaakt door een bekende fout met betrekking tot hoe sp_send_dbmail imitatie en verbindingsgroepering gebruikt.
U kunt dit probleem oplossen door code te verpakken voor het verzenden van e-mail naar een logica voor opnieuw proberen die afhankelijk is van de uitvoerparameter `@mailitem_id` . Als de uitvoering mislukt, is de parameterwaarde NULL, wat aangeeft sp_send_dbmail nog één keer moet worden aangeroepen om een e-mailbericht te verzenden. Hier is een voorbeeld van deze logica voor opnieuw proberen.
```sql
CREATE PROCEDURE send_dbmail_with_retry AS
BEGIN
    DECLARE @miid INT
    EXEC msdb.dbo.sp_send_dbmail
        @recipients = 'name@mail.com', @subject = 'Subject', @query = 'select * from dbo.test_table',
        @profile_name ='AzureManagedInstance_dbmail_profile', @execute_query_database = 'testdb',
        @mailitem_id = @miid OUTPUT

    -- If sp_send_dbmail returned NULL @mailidem_id then retry sending email.
    --
    IF (@miid is NULL)
    EXEC msdb.dbo.sp_send_dbmail
        @recipients = 'name@mail.com', @subject = 'Subject', @query = 'select * from dbo.test_table',
        @profile_name ='AzureManagedInstance_dbmail_profile', @execute_query_database = 'testdb',
END
```

### <a name="distributed-transactions-can-be-executed-after-removing-managed-instance-from-server-trust-group"></a>Gedistribueerde transacties kunnen worden uitgevoerd nadat het beheerde exemplaar is verwijderd uit de vertrouwensgroep van de server

[Vertrouwensgroepen van](../managed-instance/server-trust-group-overview.md) servers worden gebruikt om een vertrouwensrelatie tot stand te laten komen tussen beheerde exemplaren die vereist zijn voor het uitvoeren van [gedistribueerde transacties.](./elastic-transactions-overview.md) Nadat u Managed Instance hebt verwijderd uit de vertrouwensgroep van de server of de groep hebt verwijderd, kunt u mogelijk nog steeds gedistribueerde transacties uitvoeren. Er is een tijdelijke oplossing die u kunt toepassen om ervoor te zorgen dat gedistribueerde transacties zijn uitgeschakeld en dat een door de gebruiker geïnitieerde handmatige [failover](../managed-instance/user-initiated-failover.md) in managed instance is.

### <a name="distributed-transactions-cannot-be-executed-after-managed-instance-scaling-operation"></a>Gedistribueerde transacties kunnen niet worden uitgevoerd na een bewerking voor het schalen van een beheerd exemplaar

Bij schaalbewerkingen van beheerde exemplaren, waaronder het wijzigen van de servicelaag of het aantal vCores, worden de instellingen van de serververtrouwensgroep op de back-end opnieuw ingesteld en worden de lopende gedistribueerde [transacties uitgeschakeld.](./elastic-transactions-overview.md) Als tijdelijke oplossing verwijdert en maakt u een nieuwe [serververtrouwensgroep](../managed-instance/server-trust-group-overview.md) op Azure Portal.

### <a name="bulk-insert-and-backuprestore-statements-cannot-use-managed-identity-to-access-azure-storage"></a>BULK INSERT- en BACKUP/RESTORE-instructies kunnen beheerde identiteit niet gebruiken voor toegang tot Azure Storage

Bulkinvoegings-, BACKUP- en RESTORE-instructies en de OPENROWSET-functie kunnen niet worden gebruikt met beheerde identiteit om te `DATABASE SCOPED CREDENTIAL` verifiëren bij Azure Storage. Schakel als tijdelijke oplossing over naar SHARED ACCESS SIGNATURE-verificatie. Het volgende voorbeeld werkt niet op Azure SQL (database en beheerd exemplaar):

```sql
CREATE DATABASE SCOPED CREDENTIAL msi_cred WITH IDENTITY = 'Managed Identity';
GO
CREATE EXTERNAL DATA SOURCE MyAzureBlobStorage
  WITH ( TYPE = BLOB_STORAGE, LOCATION = 'https://****************.blob.core.windows.net/curriculum', CREDENTIAL= msi_cred );
GO
BULK INSERT Sales.Invoices FROM 'inv-2017-12-08.csv' WITH (DATA_SOURCE = 'MyAzureBlobStorage');
```

**Tijdelijke oplossing: gebruik** Shared Access Signature [om te verifiëren bij opslag.](/sql/t-sql/statements/bulk-insert-transact-sql#f-importing-data-from-a-file-in-azure-blob-storage)

### <a name="service-principal-cannot-access-azure-ad-and-akv"></a>Service-principal heeft geen toegang tot Azure AD en AKV

In sommige gevallen kan er een probleem zijn met de service-principal die wordt gebruikt voor toegang tot Azure AD- en Azure Key Vault(AKV)-services. Als gevolg hiervan heeft dit probleem invloed op het gebruik van Azure AD-verificatie en Transparent Database Encryption (TDE) met SQL Managed Instance. Dit kan worden ervaren als een onregelmatige verbindingsprobleem of kan geen instructies uitvoeren, zoals CREATE LOGIN/USER FROM EXTERNAL PROVIDER of EXECUTE AS LOGIN/USER. Het instellen van TDE met een door de klant beheerde sleutel op een Azure SQL Managed Instance werkt in sommige omstandigheden mogelijk ook niet.

**Tijdelijke oplossing:** als u wilt voorkomen dat dit probleem optreedt op uw SQL Managed Instance voordat u updateopdrachten gaat uitvoeren, of als u dit probleem al hebt ondervonden na updateopdrachten, gaat u naar Azure Portal, gaat u naar SQL Managed Instance [Active Directory-beheerdersblade](./authentication-aad-configure.md?tabs=azure-powershell#azure-portal). Controleer of u het foutbericht 'Managed Instance needs a Service Principal to access Azure Active Directory. Klik hier om een service-principal te maken. Als dit foutbericht wordt weergegeven, klikt u erop en volgt u de stapsgewijs opgegeven instructies totdat deze fout is opgelost.

### <a name="restoring-manual-backup-without-checksum-might-fail"></a>Het herstellen van handmatige back-ups zonder CHECKSUM kan mislukken

In bepaalde omstandigheden kan een handmatige back-up van databases die zijn gemaakt op een beheerd exemplaar zonder CHECKSUM, mogelijk niet worden hersteld. In dergelijke gevallen kunt u proberen de back-up te herstellen totdat u slaagt.

**Tijdelijke oplossing:** maak handmatige back-ups van databases op beheerde exemplaren met CHECKSUM ingeschakeld.

### <a name="agent-becomes-unresponsive-upon-modifying-disabling-or-enabling-existing-jobs"></a>Agent reageert niet meer bij het wijzigen, uitschakelen of inschakelen van bestaande taken

In bepaalde omstandigheden kan het wijzigen, uitschakelen of inschakelen van een bestaande taak ertoe leiden dat de agent niet meer reageert. Het probleem wordt automatisch opgelost bij detectie, wat resulteert in een herstart van het agentproces.

### <a name="permissions-on-resource-group-not-applied-to-sql-managed-instance"></a>Machtigingen voor de resourcegroep worden niet toegepast op SQL Managed Instance

Wanneer de azure SQL Managed Instance rol Inzender wordt toegepast op een resourcegroep (RG), wordt deze niet toegepast op SQL Managed Instance en heeft dit geen effect.

**Tijdelijke oplossing:** stel een rol in SQL Managed Instance inzender voor gebruikers op abonnementsniveau.

### <a name="limitation-of-manual-failover-via-portal-for-failover-groups"></a>Beperking van handmatige failover via de portal voor failovergroepen

Als een failovergroep meerdere exemplaren in verschillende Azure-abonnementen of resourcegroepen omvat, kan handmatige failover niet worden gestart vanuit het primaire exemplaar in de failovergroep.

**Tijdelijke oplossing:** start failover via de portal vanuit het geo-secundaire exemplaar.

### <a name="sql-agent-roles-need-explicit-execute-permissions-for-non-sysadmin-logins"></a>SQL Agent-rollen hebben expliciete EXECUTE-machtigingen nodig voor niet-sysadmin-aanmeldingen

Als er niet-sysadmin-aanmeldingen worden toegevoegd aan vaste [SQL Agent-databaserollen,](/sql/ssms/agent/sql-server-agent-fixed-database-roles)bestaat er een probleem waarbij expliciete EXECUTE-machtigingen moeten worden verleend aan de master opgeslagen procedures om deze aanmeldingen te laten werken. Als dit probleem is opgetreden, wordt het foutbericht 'The EXECUTE permission was denied on the object <object_name> (Microsoft SQL Server, Error: 229)' weergegeven.

**Tijdelijke oplossing:** wanneer u aanmeldingen toevoegt aan een vaste databaserol van SQL Agent (SQLAgentUserRole, SQLAgentReaderRole of SQLAgentOperatorRole), voert u voor elk van de aanmeldingen die aan deze rollen zijn toegevoegd, het onderstaande T-SQL-script uit om expliciet EXECUTE-machtigingen te verlenen aan de vermelde opgeslagen procedures.

```tsql
USE [master]
GO
CREATE USER [login_name] FOR LOGIN [login_name]
GO
GRANT EXECUTE ON master.dbo.xp_sqlagent_enum_jobs TO [login_name]
GRANT EXECUTE ON master.dbo.xp_sqlagent_is_starting TO [login_name]
GRANT EXECUTE ON master.dbo.xp_sqlagent_notify TO [login_name]
```

### <a name="sql-agent-jobs-can-be-interrupted-by-agent-process-restart"></a>SQL Agent-taken kunnen worden onderbroken door het opnieuw opstarten van het agentproces

**(Opgelost in maart 2020)** SQL Agent maakt een nieuwe sessie telkens als een taak wordt gestart, waardoor het geheugenverbruik geleidelijk toeneemt. Om te voorkomen dat de interne geheugenlimiet wordt bereikt, waardoor de uitvoering van geplande taken wordt geblokkeerd, wordt het agentproces opnieuw gestart zodra het geheugenverbruik de drempelwaarde bereikt. Dit kan ertoe leiden dat de uitvoering van taken die worden uitgevoerd op het moment van opnieuw opstarten wordt onderbroken.

### <a name="in-memory-oltp-memory-limits-are-not-applied"></a>OlTP-geheugenlimieten in het geheugen worden niet toegepast

In Bedrijfskritiek servicelaag worden in sommige gevallen niet de maximale geheugenlimieten toegepast voor objecten die zijn geoptimaliseerd [voor](../managed-instance/resource-limits.md#in-memory-oltp-available-space) geheugen. SQL Managed Instance werkbelasting mogelijk meer geheugen gebruiken voor in-memory OLTP bewerkingen, wat van invloed kan zijn op de beschikbaarheid en stabiliteit van het exemplaar. In-memory OLTP-query's die de limieten bereiken, mislukken mogelijk niet onmiddellijk. Dit probleem wordt binnenkort opgelost. De query's die meer geheugen in-memory OLTP, mislukken eerder als ze de [limieten bereiken.](../managed-instance/resource-limits.md#in-memory-oltp-available-space)

**Tijdelijke oplossing: controleer** [in-memory OLTP opslaggebruik met](../in-memory-oltp-monitor-space.md) behulp van [SQL Server Management Studio](/sql/relational-databases/in-memory-oltp/monitor-and-troubleshoot-memory-usage#bkmk_Monitoring) om ervoor te zorgen dat de werkbelasting niet meer dan het beschikbare geheugen gebruikt. Verhoog de geheugenlimieten die afhankelijk zijn van het aantal vCores of optimaliseer uw werkbelasting om minder geheugen te gebruiken.
 
### <a name="wrong-error-returned-while-trying-to-remove-a-file-that-is-not-empty"></a>Fout geretourneerd tijdens het verwijderen van een bestand dat niet leeg is

SQL Server en SQL Managed Instance gebruiker niet toestaan een bestand te verwijderen [dat niet leeg is.](/sql/relational-databases/databases/delete-data-or-log-files-from-a-database#Prerequisites) Als u probeert een bestand met een instructie te verwijderen, wordt de fout `ALTER DATABASE REMOVE FILE` `Msg 5042 – The file '<file_name>' cannot be removed because it is not empty` niet onmiddellijk geretourneerd. SQL Managed Instance probeert het bestand te verwijderen en de bewerking mislukt na 30 minuten met `Internal server error` .

**Tijdelijke oplossing:** verwijder de inhoud van het bestand met behulp van de `DBCC SHRINKFILE (N'<file_name>', EMPTYFILE)` opdracht . Als dit het enige bestand in de bestandsgroep is, moet u gegevens verwijderen uit de tabel of partitie die aan deze bestandsgroep is gekoppeld voordat u het bestand verkleint en deze gegevens eventueel in een andere tabel/partitie laadt.

### <a name="change-service-tier-and-create-instance-operations-are-blocked-by-ongoing-database-restore"></a>Servicelaag wijzigen en exemplaarbewerkingen maken worden geblokkeerd door doorlopend databaseherstel

Doorlopende instructie, migratieproces van Data Migration Service en ingebouwd herstel naar een bepaald tijdstip blokkeren het bijwerken van een servicelaag of het aanpassen van het bestaande exemplaar en het maken van nieuwe exemplaren totdat het herstelproces is `RESTORE` afgerond. 

Het herstelproces blokkeert deze bewerkingen op de beheerde exemplaren en exemplaarpools in hetzelfde subnet waarin het herstelproces wordt uitgevoerd. De exemplaren in exemplaarpools worden niet beïnvloed. Bewerkingen voor het maken of wijzigen van de servicelaag mislukken niet of er t mislukt een time-out. Ze worden uitgevoerd zodra het herstelproces is voltooid of geannuleerd.

**Tijdelijke oplossing: wacht** totdat het herstelproces is uitgevoerd of annuleer het herstelproces als het maken of bijwerken van de servicelaag een hogere prioriteit heeft.

### <a name="resource-governor-on-business-critical-service-tier-might-need-to-be-reconfigured-after-failover"></a>Resource Governor op Bedrijfskritiek servicelaag moeten mogelijk opnieuw worden geconfigureerd na een failover

De [Resource Governor-functie](/sql/relational-databases/resource-governor/resource-governor) waarmee u de resources kunt beperken die zijn toegewezen aan de werkbelasting van de gebruiker, classificeert mogelijk een bepaalde gebruikersworkload onjuist na een failover of een door de gebruiker geïnitieerde wijziging van de servicelaag (bijvoorbeeld de wijziging van de maximale vCore- of maximale opslaggrootte van het exemplaar).

**Tijdelijke oplossing: voer** periodiek uit of als onderdeel van een SQL Agent-taak die de SQL-taak uitvoert wanneer het exemplaar wordt gestart als u Resource Governor `ALTER RESOURCE GOVERNOR RECONFIGURE` [](/sql/relational-databases/resource-governor/resource-governor).

### <a name="cross-database-service-broker-dialogs-must-be-reinitialized-after-service-tier-upgrade"></a>Dialoogvensters voor Service Broker database-overschrijdende database moeten opnieuw worden geherinialiseerd na de upgrade van de servicelaag

Dialoogvensters voor Service Broker databaseoverschrijdende databases leveren de berichten niet meer aan de services in andere databases na de bewerking voor het wijzigen van de servicelaag. De berichten gaan *niet verloren en* zijn te vinden in de wachtrij van de afzender. Elke wijziging van de vCores- of exemplaaropslaggrootte in SQL Managed Instance zorgt ervoor dat een waarde in de weergave `service_broke_guid` [sys.databases](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql) wordt gewijzigd voor alle databases. Alle `DIALOG` die zijn gemaakt met behulp van een BEGIN [DIALOG-instructie](/sql/t-sql/statements/begin-dialog-conversation-transact-sql) die verwijst naar Service Brokers in een andere database, leveren geen berichten meer aan de doelservice.

**Tijdelijke oplossing: stop** alle activiteiten die gebruik maken van databaseoverschrijdende Service Broker dialoogvensters voordat u een servicelaag bijwerkt, en herinitialiseer deze later opnieuw. Als er resterende berichten zijn die niet bezorgd zijn na een wijziging in de servicelaag, leest u de berichten uit de bronwachtrij en verzenden ze opnieuw naar de doelwachtrij.

### <a name="impersonation-of-azure-ad-login-types-is-not-supported"></a>Imitatie van Azure AD-aanmeldingstypen wordt niet ondersteund

Imitatie met `EXECUTE AS USER` `EXECUTE AS LOGIN` of van de volgende Azure Active Directory (Azure AD)-principals wordt niet ondersteund:
-   Azure AD-gebruikers met een alias. In dit geval wordt de volgende fout geretourneerd: `15517` .
- Azure AD-aanmeldingen en -gebruikers op basis van Azure AD-toepassingen of service-principals. In dit geval worden de volgende fouten geretourneerd: `15517` en `15406` .

### <a name="query-parameter-not-supported-in-sp_send_db_mail"></a>@query parameter wordt niet ondersteund in sp_send_db_mail

De `@query` parameter in de [sp_send_db_mail](/sql/relational-databases/system-stored-procedures/sp-send-dbmail-transact-sql) werkt niet.

### <a name="transactional-replication-must-be-reconfigured-after-geo-failover"></a>Transactionele replicatie moet opnieuw worden geconfigureerd na geo-failover

Als transactionele replicatie is ingeschakeld voor een database in een groep voor automatische failover, moet de SQL Managed Instance-beheerder alle publicaties op de oude primaire database opsleen en deze opnieuw configureren op de nieuwe primaire database nadat een failover naar een andere regio heeft plaatsge gevonden. Zie Replicatie voor [meer informatie.](../managed-instance/transact-sql-tsql-differences-sql-server.md#replication)

### <a name="azure-ad-logins-and-users-are-not-supported-in-ssdt"></a>Azure AD-aanmeldingen en -gebruikers worden niet ondersteund in SSDT

SQL Server Data Tools bieden geen volledige ondersteuning voor Azure AD-aanmeldingen en -gebruikers.

### <a name="temporary-database-is-used-during-restore-operation"></a>Tijdelijke database wordt gebruikt tijdens herstelbewerking

Wanneer een database wordt hersteld in SQL Managed Instance, maakt de herstelservice eerst een lege database met de gewenste naam om de naam aan het exemplaar toe te wijzen. Na enige tijd wordt deze database verwijderd en wordt het herstellen van de werkelijke database gestart. 

De database met de status *Herstellen* heeft tijdelijk een willekeurige GUID-waarde in plaats van naam. De tijdelijke naam wordt gewijzigd in de gewenste naam die is opgegeven in de instructie `RESTORE` zodra het herstelproces is afgelopen. 

In de eerste fase kan een gebruiker toegang krijgen tot de lege database en zelfs tabellen maken of gegevens in deze database laden. Deze tijdelijke database wordt verwijderd wanneer de herstelservice de tweede fase start.

**Tijdelijke oplossing: u** hebt geen toegang tot de database die u wilt herstellen totdat u ziet dat het herstellen is voltooid.

### <a name="tempdb-structure-and-content-is-re-created"></a>TEMPDB-structuur en -inhoud worden opnieuw gemaakt

De `tempdb` database wordt altijd gesplitst in 12 gegevensbestanden en de bestandsstructuur kan niet worden gewijzigd. De maximale grootte per bestand kan niet worden gewijzigd en er kunnen geen nieuwe bestanden worden toegevoegd aan `tempdb` . `Tempdb` wordt altijd opnieuw gemaakt als een lege database wanneer het exemplaar wordt gestart of als er een wisseling van de fout wordt ondergaat, en eventuele wijzigingen in `tempdb` blijven niet behouden.

### <a name="exceeding-storage-space-with-small-database-files"></a>Overschrijding van opslagruimte met kleine databasebestanden

`CREATE DATABASE`- `ALTER DATABASE ADD FILE` en `RESTORE DATABASE` -instructies kunnen mislukken omdat het exemplaar de limiet voor Azure Storage bereiken.

Voor Algemeen instantie van SQL Managed Instance is maximaal 35 TB aan opslagruimte gereserveerd voor Azure Premium Disk-ruimte. Elk databasebestand wordt op een afzonderlijke fysieke schijf geplaatst. Schijfgrootten kunnen 128 GB, 256 GB, 512 GB, 1 TB of 4 TB zijn. Ongebruikte ruimte op de schijf wordt niet in rekening gebracht, maar de totale som van de Azure Premium Disk-grootten mag niet groter zijn dan 35 TB. In sommige gevallen kan een beheerd exemplaar dat in totaal geen 8 TB nodig heeft, de Azure-limiet van 35 TB voor opslaggrootte overschrijden vanwege interne fragmentatie.

Zo kan een Algemeen van SQL Managed Instance een groot bestand van 1,2 TB op een schijf van 4 TB hebben. Het kan ook 248 bestanden bevatten die elk 1 GB zijn en die op afzonderlijke schijven van 128 GB zijn geplaatst. In dit voorbeeld:

- De totale toegewezen schijfopslaggrootte is 1 x 4 TB + 248 x 128 GB = 35 TB.
- De totale gereserveerde ruimte voor databases op de instantie is 1 x 1,2 TB + 248 x 1 GB = 1,4 TB.

In dit voorbeeld ziet u dat een exemplaar van SQL Managed Instance onder bepaalde omstandigheden, vanwege een specifieke distributie van bestanden, mogelijk de limiet van 35 TB bereikt die is gereserveerd voor een gekoppelde Azure Premium-schijf wanneer u dit niet verwacht.

In dit voorbeeld blijven bestaande databases werken en kunnen ze probleemloos groeien zolang er geen nieuwe bestanden worden toegevoegd. Nieuwe databases kunnen niet worden gemaakt of hersteld omdat er onvoldoende ruimte is voor nieuwe schijfstations, zelfs niet als de totale grootte van alle databases de instantiegroottelimiet niet bereikt. De fout die in dat geval wordt geretourneerd, is niet duidelijk.

U kunt [het aantal resterende bestanden identificeren met behulp](https://medium.com/azure-sqldb-managed-instance/how-many-files-you-can-create-in-general-purpose-azure-sql-managed-instance-e1c7c32886c1) van systeemweergaven. Als u deze limiet bereikt, probeert u enkele van de kleinere bestanden leeg te maken en te verwijderen met behulp van de [instructie DBCC SHRINKFILE of](/sql/t-sql/database-console-commands/dbcc-shrinkfile-transact-sql#d-emptying-a-file) schakelt u over naar de [Bedrijfskritiek-laag,](../managed-instance/resource-limits.md#service-tier-characteristics)die deze limiet niet heeft.

### <a name="guid-values-shown-instead-of-database-names"></a>GUID-waarden die worden weergegeven in plaats van databasenamen

Verschillende systeemweergaven, prestatiemeters, foutberichten, XEvents en vermeldingen in foutenlogboek geven GUID-database-id's weer in plaats van de werkelijke databasenamen. Vertrouw niet op deze GUID-id's omdat ze in de toekomst worden vervangen door daadwerkelijke databasenamen.

**Tijdelijke oplossing:** gebruik de weergave sys.databases om de werkelijke databasenaam op te lossen vanuit de naam van de fysieke database, opgegeven in de vorm van GUID-database-id's:

```tsql
SELECT name as ActualDatabaseName, physical_database_name as GUIDDatabaseIdentifier 
FROM sys.databases
WHERE database_id > 4
```

### <a name="error-logs-arent-persisted"></a>Foutenlogboeken worden niet persistent gemaakt

Foutlogboeken die beschikbaar zijn in SQL Managed Instance worden niet persistent gemaakt en hun grootte is niet opgenomen in de maximale opslaglimiet. Foutenlogboeken worden mogelijk automatisch gewist als er een failover plaatsvindt. Mogelijk zijn er hiaten in de geschiedenis van het foutenlogboek omdat SQL Managed Instance meerdere keren is verplaatst op verschillende virtuele machines.

### <a name="transaction-scope-on-two-databases-within-the-same-instance-isnt-supported"></a>Transactiebereik voor twee databases binnen hetzelfde exemplaar wordt niet ondersteund

**(Opgelost in maart 2020)** De klasse in .NET werkt niet als twee query's worden verzonden naar twee databases binnen hetzelfde exemplaar `TransactionScope` onder hetzelfde transactiebereik:

```csharp
using (var scope = new TransactionScope())
{
    using (var conn1 = new SqlConnection("Server=quickstartbmi.neu15011648751ff.database.windows.net;Database=b;User ID=myuser;Password=mypassword;Encrypt=true"))
    {
        conn1.Open();
        SqlCommand cmd1 = conn1.CreateCommand();
        cmd1.CommandText = string.Format("insert into T1 values(1)");
        cmd1.ExecuteNonQuery();
    }

    using (var conn2 = new SqlConnection("Server=quickstartbmi.neu15011648751ff.database.windows.net;Database=b;User ID=myuser;Password=mypassword;Encrypt=true"))
    {
        conn2.Open();
        var cmd2 = conn2.CreateCommand();
        cmd2.CommandText = string.Format("insert into b.dbo.T2 values(2)");        cmd2.ExecuteNonQuery();
    }

    scope.Complete();
}

```

Tijdelijke oplossing (niet meer nodig sinds maart **2020)**: gebruik [SqlConnection.ChangeDatabase(String)](/dotnet/api/system.data.sqlclient.sqlconnection.changedatabase) om een andere database in een verbindingscontext te gebruiken in plaats van twee verbindingen.

### <a name="clr-modules-and-linked-servers-sometimes-cant-reference-a-local-ip-address"></a>CLR-modules en gekoppelde servers kunnen soms niet verwijzen naar een lokaal IP-adres

CLR-modules in SQL Managed Instance gekoppelde servers of gedistribueerde query's die verwijzen naar een huidige instantie, kunnen soms het IP-adres van een lokaal exemplaar niet oplossen. Deze fout is een tijdelijk probleem.

**Tijdelijke oplossing:** gebruik indien mogelijk contextverbindingen in een CLR-module.

## <a name="updates"></a>Updates

Zie service-updates SQL Database een lijst [met SQL Database-updates en -verbeteringen.](https://azure.microsoft.com/updates/?product=sql-database)

Zie Service-updates voor updates en verbeteringen in [alle Azure-services.](https://azure.microsoft.com/updates)

## <a name="contribute-to-content"></a>Bijdragen aan inhoud

Zie de Azure SQL om bij te dragen [aan de gids voor Docs-inzenders.](/contribute/)
