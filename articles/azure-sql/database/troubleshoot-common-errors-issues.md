---
title: Algemene verbindingsfouten oplossen Azure SQL-database
description: Biedt stappen voor het oplossen van problemen met Azure SQL Database verbindingen en het oplossen van andere Azure SQL Database of Azure SQL Managed instance-specifieke problemen
services: sql-database
ms.service: sql-db-mi
ms.subservice: development
ms.topic: troubleshooting
ms.custom: seo-lt-2019, OKR 11/2019, sqldbrb=1
author: ramakoni1
ms.author: ramakoni
ms.reviewer: sstein,vanto
ms.date: 01/14/2021
ms.openlocfilehash: ec61f2c67576d6e144d8d4bb7e8ecaaa157db0a9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98233369"
---
# <a name="troubleshooting-connectivity-issues-and-other-errors-with-azure-sql-database-and-azure-sql-managed-instance"></a>Verbindings problemen en andere fouten oplossen met Azure SQL Database en Azure SQL Managed instance
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Er worden fout berichten weer gegeven wanneer de verbinding met Azure SQL Database of een door Azure SQL beheerd exemplaar mislukt. Deze verbindings problemen kunnen worden veroorzaakt door herconfiguratie, Firewall instellingen, een time-out voor de verbinding, onjuiste aanmeldings gegevens of het Toep assen van aanbevolen procedures en ontwerp richtlijnen tijdens het ontwerp proces van de [toepassing](develop-overview.md) . Daarnaast kunt u geen verbinding meer maken als de maximum limiet van sommige bronnen voor Azure SQL Database of SQL Managed instance wordt bereikt.

## <a name="transient-fault-error-messages-40197-40613-and-others"></a>Tijdelijke fout berichten voor fouten (40197, 40613 en andere)

De Azure-infrastructuur biedt de mogelijkheid om servers dynamisch opnieuw te configureren wanneer zware workloads optreden in de SQL Database-service.  Dit dynamische gedrag kan ertoe leiden dat de verbinding van het client programma met de data base of het exemplaar verloren gaat. Dit type fout wordt een *tijdelijke fout* genoemd. Gebeurtenissen voor het opnieuw configureren van de data base vinden plaats vanwege een geplande gebeurtenis (bijvoorbeeld een software-upgrade) of een niet-geplande gebeurtenis (bijvoorbeeld een proces storing of taak verdeling). De meeste opnieuw geconfigureerde gebeurtenissen zijn meestal korte tijd en moeten binnen Maxi maal 60 seconden worden voltooid. Het volt ooien van deze gebeurtenissen kan echter af en toe langer duren, bijvoorbeeld wanneer een grote trans actie een langlopend herstel veroorzaakt. De volgende tabel bevat een lijst met verschillende tijdelijke fouten die toepassingen kunnen ontvangen bij het maken van verbinding met SQL Database

### <a name="list-of-transient-fault-error-codes"></a>Lijst met tijdelijke fout codes

| Foutcode | Ernst | Beschrijving |
| ---:| ---:|:--- |
| 4060 |16 |Kan de data base%. &#x2a;ls niet openen die door de aanmelding is aangevraagd. De aanmelding is mislukt. Zie [fouten 4000 tot 4999](/sql/relational-databases/errors-events/database-engine-events-and-errors#errors-4000-to-4999) voor meer informatie.|
| 40197 |17 |De service heeft een fout aangetroffen bij het verwerken van uw aanvraag. Probeer het opnieuw. Fout code% d.<br/><br/>U ontvangt deze fout melding wanneer de service niet beschikbaar is vanwege software-of hardware-upgrades, hardwarefouten of andere failover-problemen. De fout code (% d) Inge sloten in het bericht van fout 40197 bevat aanvullende informatie over het soort fout of failover dat heeft plaatsgevonden. Enkele voor beelden van de fout codes zijn Inge sloten in het bericht met de fout 40197 zijn 40020, 40143, 40166 en 40540.<br/><br/>Als u opnieuw verbinding maakt, wordt u automatisch verbonden met een gezonde kopie van uw data base. Uw toepassing moet fout 40197 opvangen, de Inge sloten fout code (% d) in het bericht voor het oplossen van problemen registreren en opnieuw proberen verbinding te maken met SQL Database totdat de resources beschikbaar zijn en uw verbinding opnieuw tot stand is gebracht. Zie [Tijdelijke fouten](troubleshoot-common-connectivity-issues.md#transient-errors-transient-faults) voor meer informatie.|
| 40501 |20 |De service is momenteel te druk. Voer de aanvraag na 10 seconden opnieuw uit. Incident-ID:% ls. Code:% d. Zie voor meer informatie: <br/>&bull;&nbsp; [Logische SQL Server-Resource limieten](resource-limits-logical-server.md)<br/>&bull;&nbsp; [Op DTU gebaseerde limieten voor afzonderlijke data bases](service-tiers-dtu.md)<br/>&bull;&nbsp; [Op DTU gebaseerde limieten voor elastische Pools](resource-limits-dtu-elastic-pools.md)<br/>&bull;&nbsp; [limieten op basis van vCore voor afzonderlijke data bases](resource-limits-vcore-single-databases.md)<br/>&bull;&nbsp; [limieten op basis van vCore voor elastische Pools](resource-limits-vcore-elastic-pools.md)<br/>&bull;&nbsp; [Resource limieten voor Azure SQL Managed instance](../managed-instance/resource-limits.md).|
| 40613 |17 |Data Base%. &#x2a;ls op server%. &#x2a;LS is momenteel niet beschikbaar. Probeer de verbinding later opnieuw. Als het probleem zich blijft voordoen, neemt u contact op met de klant ondersteuning en geeft u de sessie tracering-ID%. &#x2a;ls.<br/><br/> Deze fout kan optreden als er al een DAC (dedicated Administrator Connection) is ingesteld op de data base. Zie [Tijdelijke fouten](troubleshoot-common-connectivity-issues.md#transient-errors-transient-faults) voor meer informatie.|
| 49918 |16 |Kan aanvraag niet verwerken. Er zijn onvoldoende bronnen om de aanvraag te verwerken.<br/><br/>De service is momenteel te druk. Voer de aanvraag later opnieuw uit. Zie voor meer informatie: <br/>&bull;&nbsp; [Logische SQL Server-Resource limieten](resource-limits-logical-server.md)<br/>&bull;&nbsp; [Op DTU gebaseerde limieten voor afzonderlijke data bases](service-tiers-dtu.md)<br/>&bull;&nbsp; [Op DTU gebaseerde limieten voor elastische Pools](resource-limits-dtu-elastic-pools.md)<br/>&bull;&nbsp; [limieten op basis van vCore voor afzonderlijke data bases](resource-limits-vcore-single-databases.md)<br/>&bull;&nbsp; [limieten op basis van vCore voor elastische Pools](resource-limits-vcore-elastic-pools.md)<br/>&bull;&nbsp; [Resource limieten voor Azure SQL Managed instance](../managed-instance/resource-limits.md). |
| 49919 |16 |Kan de aanvraag voor maken of bijwerken niet verwerken. Er zijn te veel bewerkingen voor het maken of bijwerken van het abonnement% ld.<br/><br/>De service is bezig met het verwerken van meerdere aanvragen voor maken of bijwerken voor uw abonnement of server. Aanvragen zijn momenteel geblokkeerd voor het optimaliseren van resources. Query [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) voor bewerkingen die in behandeling zijn. Wacht tot er aanvragen voor maken of bijwerken zijn voltooid of verwijder een van de in behandeling zijnde aanvragen en voer de aanvraag later opnieuw uit. Zie voor meer informatie: <br/>&bull;&nbsp; [Logische SQL Server-Resource limieten](resource-limits-logical-server.md)<br/>&bull;&nbsp; [Op DTU gebaseerde limieten voor afzonderlijke data bases](service-tiers-dtu.md)<br/>&bull;&nbsp; [Op DTU gebaseerde limieten voor elastische Pools](resource-limits-dtu-elastic-pools.md)<br/>&bull;&nbsp; [limieten op basis van vCore voor afzonderlijke data bases](resource-limits-vcore-single-databases.md)<br/>&bull;&nbsp; [limieten op basis van vCore voor elastische Pools](resource-limits-vcore-elastic-pools.md)<br/>&bull;&nbsp; [Resource limieten voor Azure SQL Managed instance](../managed-instance/resource-limits.md). |
| 49920 |16 |Kan aanvraag niet verwerken. Er worden te veel bewerkingen uitgevoerd voor het abonnement% ld.<br/><br/>De service is bezig met het verwerken van meerdere aanvragen voor dit abonnement. Aanvragen zijn momenteel geblokkeerd voor het optimaliseren van resources. Query [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) voor bewerkings status. Wacht totdat aanvragen in behandeling zijn voltooid of verwijder een van de in behandeling zijnde aanvragen en voer de aanvraag later opnieuw uit. Zie voor meer informatie: <br/>&bull;&nbsp; [Logische SQL Server-Resource limieten](resource-limits-logical-server.md)<br/>&bull;&nbsp; [Op DTU gebaseerde limieten voor afzonderlijke data bases](service-tiers-dtu.md)<br/>&bull;&nbsp; [Op DTU gebaseerde limieten voor elastische Pools](resource-limits-dtu-elastic-pools.md)<br/>&bull;&nbsp; [limieten op basis van vCore voor afzonderlijke data bases](resource-limits-vcore-single-databases.md)<br/>&bull;&nbsp; [limieten op basis van vCore voor elastische Pools](resource-limits-vcore-elastic-pools.md)<br/>&bull;&nbsp; [Resource limieten voor Azure SQL Managed instance](../managed-instance/resource-limits.md). |
| 4221 |16 |Aanmelden bij lezen-secundair is mislukt omdat er een lange wacht tijd op HADR_DATABASE_WAIT_FOR_TRANSITION_TO_VERSIONING is. De replica is niet beschikbaar voor aanmelding omdat er geen rijkoppen ontbreken voor trans acties die in de vlucht waren toen de replica werd gerecycled. Het probleem kan worden opgelost door de actieve trans acties op de primaire replica te herstellen of door te voeren. Exemplaren van deze voor waarde kunnen worden geminimaliseerd door lange schrijf transacties op de primaire te vermijden. |

### <a name="steps-to-resolve-transient-connectivity-issues"></a>Stappen voor het oplossen van problemen met de tijdelijke verbinding

1. Controleer het [Microsoft Azure-service dashboard](https://azure.microsoft.com/status) voor eventuele bekende storingen die zijn opgetreden tijdens de periode waarin de fouten zijn gerapporteerd door de toepassing.
2. Toepassingen die verbinding maken met een Cloud service, zoals Azure SQL Database, moeten periodieke configuratie gebeurtenissen verwachten en nieuwe pogings logica implementeren voor het afhandelen van deze fouten in plaats van halen toepassings fouten aan gebruikers.
3. Wanneer een Data Base de resource limieten nadert, kan dit een probleem met de tijdelijke verbinding zijn. Zie [resource limieten](resource-limits-logical-server.md#what-happens-when-database-resource-limits-are-reached).
4. Als er verbindings problemen blijven bestaan of als de duur waarvoor uw toepassing de fout ondervindt, groter is dan 60 seconden of als er meerdere exemplaren van de fout in een bepaalde dag worden weer gegeven, kunt u een Azure-ondersteunings aanvraag indienen door **ondersteuning** te selecteren op de [ondersteunings](https://azure.microsoft.com/support/options) site van Azure.

#### <a name="implementing-retry-logic"></a>Logica voor opnieuw proberen implementeren

Het wordt ten zeerste aangeraden dat uw client programma logica voor nieuwe pogingen heeft, zodat er een verbinding tot stand kan worden gebracht nadat de tijdelijke fout tijd is gecorrigeerd.  U wordt aangeraden vijf seconden vóór uw eerste nieuwe poging uit te stellen. Opnieuw proberen na een vertraging die korter is dan 5 seconden, is de Cloud service minder Risico's. Voor elke volgende poging moet de vertraging exponentieel toenemen, tot een maximum van 60 seconden.

Zie voor code voorbeelden van de logica voor opnieuw proberen:

- [Flexibel verbinding maken met SQL via ADO.NET](/sql/connect/ado-net/step-4-connect-resiliently-sql-ado-net)
- [Flexibel verbinding maken met SQL via PHP](/sql/connect/php/step-4-connect-resiliently-to-sql-with-php)

Voor meer informatie over het afhandelen van tijdelijke fouten in uw toepassing raadpleegt [u problemen met tijdelijke verbindings fouten oplossen SQL database](troubleshoot-common-connectivity-issues.md)

Een bespreking van de *blokkerings periode* voor clients die gebruikmaken van ADO.net is beschikbaar in [Connection pooling (ADO.net)](/dotnet/framework/data/adonet/sql-server-connection-pooling).

## <a name="a-network-related-or-instance-specific-error-occurred-while-establishing-a-connection-to-your-server"></a>Er is een netwerkgerelateerde of exemplaar fout opgetreden tijdens het maken van een verbinding met uw server

Het probleem treedt op als de toepassing geen verbinding kan maken met de server.

U kunt dit probleem oplossen door de stappen (in de weer gegeven volg orde) in de [stappen voor het oplossen van veelvoorkomende problemen met de verbinding te](#steps-to-fix-common-connection-issues) verhelpen.

## <a name="the-serverinstance-was-not-found-or-was-not-accessible-errors-26-40-10053"></a>De server/het exemplaar is niet gevonden of niet toegankelijk (fouten 26, 40, 10053)

### <a name="error-26-error-locating-server-specified"></a>Fout 26: fout bij het zoeken van de opgegeven server

``System.Data.SqlClient.SqlException: A network-related or instance-specific error occurred while establishing a connection to SQL Server. The server was not found or was not accessible. Verify that the instance name is correct and that SQL Server is configured to allow remote connections.(provider: SQL Network Interfaces, error: 26 – Error Locating Server/Instance Specified)``

#### <a name="error-40-could-not-open-a-connection-to-the-server"></a>Fout 40: kan geen verbinding met de server openen

``A network-related or instance-specific error occurred while establishing a connection to SQL Server. The server was not found or was not accessible. Verify that the instance name is correct and that SQL Server is configured to allow remote connections. (provider: Named Pipes Provider, error: 40 - Could not open a connection to SQL Server)``

#### <a name="error-10053-a-transport-level-error-has-occurred-when-receiving-results-from-the-server"></a>Fout 10053: er is een fout op transport niveau opgetreden bij het ontvangen van resultaten van de server

``10053: A transport-level error has occurred when receiving results from the server. (Provider: TCP Provider, error: 0 - An established connection was aborted by the software in your host machine)``

Deze problemen treden op als de toepassing geen verbinding kan maken met de server.

Om deze problemen op te lossen, voert u de stappen (in de weer gegeven volg orde) uit in de sectie [stappen voor het oplossen van veelvoorkomende problemen met de verbinding](#steps-to-fix-common-connection-issues) .

## <a name="cannot-connect-to-server-due-to-firewall-issues"></a>Kan geen verbinding maken met de server vanwege problemen met de firewall

### <a name="error-40615-cannot-connect-to--servername-"></a>Fout 40615: er kan geen verbinding worden gemaakt met < servername >

U kunt dit probleem oplossen door [firewall instellingen op SQL database te configureren via de Azure Portal](firewall-configure.md).

### <a name="error-5-cannot-connect-to--servername-"></a>Fout 5: er kan geen verbinding worden gemaakt met < servername >

U kunt dit probleem oplossen door ervoor te zorgen dat poort 1433 is geopend voor uitgaande verbindingen op alle firewalls tussen de client en Internet.

## <a name="unable-to-log-in-to-the-server-errors-18456-40531"></a>Kan niet aanmelden bij de server (fouten 18456, 40531)

### <a name="login-failed-for-user--user-name-"></a>De aanmelding is mislukt voor de gebruiker ' < gebruikers naam > '

``Login failed for user '<User name>'.This session has been assigned a tracing ID of '<Tracing ID>'. Provide this tracing ID to customer support when you need assistance. (Microsoft SQL Server, Error: 18456)``

Neem contact op met de service beheerder om dit probleem op te lossen en geef een geldige gebruikers naam en een geldig wacht woord op.

De service beheerder kan doorgaans de volgende stappen gebruiken om de aanmeldings referenties toe te voegen:

1. Meld u aan bij de server met behulp van SQL Server Management Studio (SSMS).
2. Voer de volgende SQL-query uit in de hoofd database om te controleren of de aanmeldings naam is uitgeschakeld:

   ```sql
   SELECT name, is_disabled FROM sys.sql_logins;
   ```

3. Als de bijbehorende naam is uitgeschakeld, schakelt u deze in met behulp van de volgende instructie:

   ```sql
   ALTER LOGIN <User name> ENABLE;
   ```

4. Als de gebruikers naam voor SQL-aanmelding niet bestaat, bewerkt u de volgende SQL-query en voert u deze uit om een nieuwe SQL-aanmelding te maken:

   ```sql
   CREATE LOGIN <SQL_login_name, sysname, login_name>
   WITH PASSWORD = '<password, sysname, Change_Password>';
   GO
   ```

5. Vouw in SSMS Objectverkenner **data bases** uit.
6. Selecteer de data base waaraan u de gebruiker toestemming wilt verlenen.
7. Klik met de rechter muisknop op **beveiliging** en selecteer vervolgens **Nieuw**, **gebruiker**.
8. Bewerk en voer de volgende SQL-query uit in het gegenereerde script met tijdelijke aanduidingen:

   ```sql
   CREATE USER <user_name, sysname, user_name>
   FOR LOGIN <login_name, sysname, login_name>
   WITH DEFAULT_SCHEMA = <default_schema, sysname, dbo>;
   GO

   -- Add user to the database owner role
   EXEC sp_addrolemember N'db_owner', N'<user_name, sysname, user_name>';
   GO
   ```

   U kunt ook gebruiken `sp_addrolemember` om specifieke gebruikers toe te wijzen aan specifieke database rollen.

   > [!NOTE]
   > In Azure SQL Database moet u rekening houden met de nieuwere syntaxis van [ALTER Role](/sql/t-sql/statements/alter-role-transact-sql) voor het beheren van het lidmaatschap van de databaserol.  

Zie [data bases en aanmeldingen beheren in Azure SQL database](./logins-create-manage.md)voor meer informatie.

## <a name="connection-timeout-expired-errors"></a>Time-out tijdens verbindingstime-out

### <a name="systemdatasqlclientsqlexception-0x80131904-connection-timeout-expired"></a>System. data. SqlClient. SqlException (0x80131904): time-out voor verbinding verlopen

``System.Data.SqlClient.SqlException (0x80131904): Connection Timeout Expired. The timeout period elapsed while attempting to consume the pre-login handshake acknowledgement. This could be because the pre-login handshake failed or the server was unable to respond back in time. The duration spent while attempting to connect to this server was - [Pre-Login] initialization=3; handshake=29995;``

### <a name="systemdatasqlclientsqlexception-0x80131904-timeout-expired"></a>System. data. SqlClient. SqlException (0x80131904): time-out verlopen

``System.Data.SqlClient.SqlException (0x80131904): Timeout expired. The timeout period elapsed prior to completion of the operation or the server is not responding.``

### <a name="systemdataentitycoreentityexception-the-underlying-provider-failed-on-open"></a>System. data. entity. core. EntityException: de onderliggende provider is niet geopend

``System.Data.Entity.Core.EntityException: The underlying provider failed on Open. -> System.Data.SqlClient.SqlException: Timeout expired. The timeout period elapsed prior to completion of the operation or the server is not responding. -> System.ComponentModel.Win32Exception: The wait operation timed out``

### <a name="cannot-connect-to--server-name-"></a>Kan geen verbinding maken met < server naam >

``Cannot connect to <server name>.ADDITIONAL INFORMATION:Connection Timeout Expired. The timeout period elapsed during the post-login phase. The connection could have timed out while waiting for server to complete the login process and respond; Or it could have timed out while attempting to create multiple active connections. The duration spent while attempting to connect to this server was - [Pre-Login] initialization=231; handshake=983; [Login] initialization=0; authentication=0; [Post-Login] complete=13000; (Microsoft SQL Server, Error: -2) For help, click: http://go.microsoft.com/fwlink?ProdName=Microsoft%20SQL%20Server&EvtSrc=MSSQLServer&EvtID=-2&LinkId=20476 The wait operation timed out``

Deze uitzonde ringen kunnen worden veroorzaakt door verbindings-of query problemen. Zie [bevestigen of een fout wordt veroorzaakt door een verbindings probleem](#confirm-whether-an-error-is-caused-by-a-connectivity-issue)om te controleren of deze fout wordt veroorzaakt door verbindings problemen.

Verbindingstime-outs doen zich voor omdat de toepassing geen verbinding kan maken met de server. U kunt dit probleem oplossen door de stappen (in de weer gegeven volg orde) in de [stappen voor het oplossen van veelvoorkomende problemen met de verbinding te](#steps-to-fix-common-connection-issues) verhelpen.

## <a name="resource-governance-errors"></a>Resource governance-fouten

### <a name="error-10928-resource-id-d"></a>Fout 10928: Resource-ID:% d

``10928: Resource ID: %d. The %s limit for the database is %d and has been reached. See http://go.microsoft.com/fwlink/?LinkId=267637 for assistance. The Resource ID value in error message indicates the resource for which limit has been reached. For sessions, Resource ID = 2.``

Ga op een van de volgende manieren te werk om dit probleem op te lossen:

- Controleer of er langlopende query's zijn.

  > [!NOTE]
  > Dit is een minimale aanpak waarmee het probleem mogelijk niet kan worden opgelost. Zie voor meer gedetailleerde informatie over het oplossen van langdurige query's en het [oplossen van problemen](understand-resolve-blocking.md)met het blok keren van Azure SQL database.

1. Voer de volgende SQL-query uit om de weer gave [sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) te controleren om blokkerings aanvragen weer te geven:

   ```sql
   SELECT * FROM sys.dm_exec_requests;
   ```

1. Bepaal de **invoer buffer** voor de hoofd blok kering met behulp van de [sys.dm_exec_input_buffer](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-input-buffer-transact-sql) Dynamic Management function en de session_id van de conflicterende query, bijvoorbeeld:

   ```sql 
   SELECT * FROM sys.dm_exec_input_buffer (100,0);
   ```

1. Stem de hoofd blok-query af.

Als de data base consequent de limiet heeft bereikt ondanks het blok keren van het adresseren en langlopende query's, kunt u overwegen om een upgrade uit te voeren naar een editie met meer bronnen- [edities](https://azure.microsoft.com/pricing/details/sql-database/).

Zie  [SQL database resource limieten voor servers](./resource-limits-logical-server.md)voor meer informatie over database limieten.

### <a name="error-10929-resource-id-1"></a>Fout 10929: Resource-ID: 1

``10929: Resource ID: 1. The %s minimum guarantee is %d, maximum limit is %d and the current usage for the database is %d. However, the server is currently too busy to support requests greater than %d for this database. See http://go.microsoft.com/fwlink/?LinkId=267637 for assistance. Otherwise, please try again later.``

### <a name="error-40501-the-service-is-currently-busy"></a>Fout 40501: de service is momenteel bezet

``40501: The service is currently busy. Retry the request after 10 seconds. Incident ID: %ls. Code: %d.``

Dit is een beperkings fout van de engine, een indicatie dat de resource limieten worden overschreden.

Zie [logische SQL Server-Resource limieten](./resource-limits-logical-server.md)voor meer informatie over resource limieten.

### <a name="error-40544-the-database-has-reached-its-size-quota"></a>Fout 40544: de grootte van de data base is bereikt

``40544: The database has reached its size quota. Partition or delete data, drop indexes, or consult the documentation for possible resolutions. Incident ID: <ID>. Code: <code>.``

Deze fout treedt op wanneer de data base het quotum voor de grootte heeft bereikt.

U kunt de volgende stappen gebruiken om het probleem op te lossen of om extra opties te bieden:

1. Controleer de huidige grootte van de data base met behulp van het dash board in de Azure Portal.

   > [!NOTE]
   > Voer de volgende SQL-query uit om te bepalen welke tabellen de meeste ruimte gebruiken en daarom potentiële kandidaten voor opschoning hebben:

   ```sql
   SELECT o.name,
    SUM(p.row_count) AS 'Row Count',
    SUM(p.reserved_page_count) * 8.0 / 1024 AS 'Table Size (MB)'
   FROM sys.objects o
   JOIN sys.dm_db_partition_stats p on p.object_id = o.object_id
   GROUP BY o.name
   ORDER BY [Table Size (MB)] DESC;
   ```

2. Als de huidige grootte niet groter is dan de maximale grootte die voor uw editie wordt ondersteund, kunt u ALTER Data Base gebruiken om de waarde voor MAXSIZE te verhogen.
3. Als de data base al de Maxi maal ondersteunde grootte voor uw versie overschrijdt, kunt u een of meer van de volgende stappen uitvoeren:

   - Normale activiteiten voor het opschonen van data bases uitvoeren. U kunt bijvoorbeeld de ongewenste gegevens opschonen met behulp van afkap ping/verwijderen of gegevens verplaatsen met behulp van SQL Server Integration Services (SSIS) of het hulp programma voor het bulksgewijs kopiëren van Program ma's (BCP).
   - Partitioneer of verwijder gegevens, verwijder indexen of Raadpleeg de documentatie voor mogelijke oplossingen.
   - Zie voor het schalen van de Data Base [afzonderlijke database resources schalen](./single-database-scale.md) en [bronnen voor elastische Pools schalen](./elastic-pool-scale.md).

### <a name="error-40549-session-is-terminated-because-you-have-a-long-running-transaction"></a>Fout 40549: de sessie is beëindigd omdat u een langlopende trans actie hebt

``40549: Session is terminated because you have a long-running transaction. Try shortening your transaction.``

Als u deze fout herhaaldelijk ondervindt, kunt u proberen om het probleem op te lossen door de volgende stappen uit te voeren:

1. Controleer in de weer gave sys.dm_exec_requests of er geopende sessies met een hoge waarde voor de total_elapsed_time kolom worden weer gegeven. Voer deze controle uit door het volgende SQL-script uit te voeren:

   ```sql
   SELECT * FROM sys.dm_exec_requests;
   ```

2. Bepaal de **invoer buffer** voor de hoofd blok kering met behulp van de [sys.dm_exec_input_buffer](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-input-buffer-transact-sql) Dynamic Management function en de session_id van de conflicterende query, bijvoorbeeld:

   ```sql 
   SELECT * FROM sys.dm_exec_input_buffer (100,0);
   ```

3. De query afstemmen.

    > [!Note]
    > Voor meer informatie over het oplossen van problemen met blok keringen in Azure SQL Database raadpleegt u [Azure SQL database problemen met het blok keren begrijpen en oplossen](understand-resolve-blocking.md).

U kunt ook uw query's batcheren. Zie voor meer informatie over batch verwerking [batch verwerking gebruiken om de prestaties van SQL database-toepassingen te verbeteren](../performance-improve-use-batching.md).

### <a name="error-40551-the-session-has-been-terminated-because-of-excessive-tempdb-usage"></a>Fout 40551: de sessie is beëindigd vanwege het overmatige gebruik van TEMPDB

``40551: The session has been terminated because of excessive TEMPDB usage. Try modifying your query to reduce the temporary table space usage.``

Voer de volgende stappen uit om dit probleem te omzeilen:

1. Wijzig de query's om het gebruik van tijdelijke tabel ruimte te verminderen.
2. Tijdelijke objecten verwijderen nadat ze niet meer nodig zijn.
3. Tabellen afkappen of ongebruikte tabellen verwijderen.

### <a name="error-40552-the-session-has-been-terminated-because-of-excessive-transaction-log-space-usage"></a>Fout 40552: de sessie is beëindigd vanwege het overmatige gebruik van het transactie logboek

``40552: The session has been terminated because of excessive transaction log space usage. Try modifying fewer rows in a single transaction.``

Probeer de volgende methoden om dit op te lossen:

- Het probleem kan worden veroorzaakt door invoeg-, update-of verwijderings bewerkingen.
Probeer het aantal rijen dat onmiddellijk wordt geëxploiteerd, te verminderen door batches te implementeren of te splitsen in meerdere kleinere trans acties.
- Het probleem kan zich voordoen als gevolg van het opnieuw opbouwen van de index. U kunt dit probleem omzeilen door te controleren of het aantal rijen dat in de tabel wordt beïnvloed * (gemiddelde grootte van het veld dat wordt bijgewerkt in bytes + 80) < 2 gigabytes (GB).

  > [!NOTE]
  > Voor een opnieuw opgebouwde index moet de gemiddelde grootte van het veld dat wordt bijgewerkt, worden vervangen door de gemiddelde index grootte.

### <a name="error-40553-the-session-has-been-terminated-because-of-excessive-memory-usage"></a>Fout 40553: de sessie is beëindigd vanwege het overmatige geheugen gebruik

``40553 : The session has been terminated because of excessive memory usage. Try modifying your query to process fewer rows.``

U kunt dit probleem omzeilen door de query te optimaliseren.

Voor een uitgebreide probleemoplossings procedure, Zie [is mijn query wordt in de Cloud verfijnd?](/archive/blogs/sqlblog/is-my-query-running-fine-in-the-cloud).

### <a name="table-of-additional-resource-governance-error-messages"></a>Tabel met aanvullende resource governance-fout berichten

| Foutcode | Ernst | Beschrijving |
| ---:| ---:|:--- |
| 10928 |20 |Resource-ID:% d. De% s-limiet voor de data base is% d en is bereikt. Zie [SQL database resource limieten voor één en gepoolde data bases](resource-limits-logical-server.md)voor meer informatie.<br/><br/>De resource-ID geeft de resource aan die de limiet heeft bereikt. Voor worker-threads, de resource-ID = 1. Voor sessies, de resource-ID = 2.<br/><br/>Zie voor meer informatie over deze fout en hoe u deze kunt oplossen: <br/>&bull;&nbsp; [Logische SQL Server-Resource limieten](resource-limits-logical-server.md)<br/>&bull;&nbsp; [Op DTU gebaseerde limieten voor afzonderlijke data bases](service-tiers-dtu.md)<br/>&bull;&nbsp; [Op DTU gebaseerde limieten voor elastische Pools](resource-limits-dtu-elastic-pools.md)<br/>&bull;&nbsp; [limieten op basis van vCore voor afzonderlijke data bases](resource-limits-vcore-single-databases.md)<br/>&bull;&nbsp; [limieten op basis van vCore voor elastische Pools](resource-limits-vcore-elastic-pools.md)<br/>&bull;&nbsp; [Resource limieten voor Azure SQL Managed instance](../managed-instance/resource-limits.md). |
| 10929 |20 |Resource-ID:% d. De minimale garantie van% s is% d, de maximum limiet is% d en het huidige gebruik van de data base is% d. De server is momenteel echter bezet om aanvragen te ondersteunen die groter zijn dan% d voor deze data base. De resource-ID geeft de resource aan die de limiet heeft bereikt. Voor worker-threads, de resource-ID = 1. Voor sessies, de resource-ID = 2. Zie voor meer informatie: <br/>&bull;&nbsp; [Logische SQL Server-Resource limieten](resource-limits-logical-server.md)<br/>&bull;&nbsp; [Op DTU gebaseerde limieten voor afzonderlijke data bases](service-tiers-dtu.md)<br/>&bull;&nbsp; [Op DTU gebaseerde limieten voor elastische Pools](resource-limits-dtu-elastic-pools.md)<br/>&bull;&nbsp; [limieten op basis van vCore voor afzonderlijke data bases](resource-limits-vcore-single-databases.md)<br/>&bull;&nbsp; [limieten op basis van vCore voor elastische Pools](resource-limits-vcore-elastic-pools.md)<br/>&bull;&nbsp; [Resource limieten voor Azure SQL Managed instance](../managed-instance/resource-limits.md). <br/>Als dat niet het geval is, probeert u het later opnieuw. |
| 40544 |20 |De data base heeft het quotum voor de grootte bereikt. Partitioneer of verwijder gegevens, verwijder indexen of Raadpleeg de documentatie voor mogelijke oplossingen. Zie voor het schalen van de Data Base [afzonderlijke database resources schalen](single-database-scale.md) en [bronnen voor elastische Pools schalen](elastic-pool-scale.md).|
| 40549 |16 |De sessie is beëindigd omdat u een langlopende trans actie hebt. Probeer uw trans actie te verkorten. Zie voor meer informatie over batch verwerking [batch verwerking gebruiken om de prestaties van SQL database-toepassingen te verbeteren](../performance-improve-use-batching.md).|
| 40550 |16 |De sessie is beëindigd omdat er te veel vergren delingen zijn verkregen. Probeer minder rijen in één trans actie te lezen of te wijzigen. Zie voor meer informatie over batch verwerking [batch verwerking gebruiken om de prestaties van SQL database-toepassingen te verbeteren](../performance-improve-use-batching.md).|
| 40551 |16 |De sessie is beëindigd vanwege overmatig `TEMPDB` gebruik. Wijzig uw query om het gebruik van de tijdelijke tabel ruimte te verminderen.<br/><br/>Als u tijdelijke objecten gebruikt, bespaart u ruimte in de `TEMPDB` Data Base door tijdelijke objecten te verwijderen nadat deze niet langer nodig zijn voor de sessie. Zie [tempdb-data base in SQL database](/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database)voor meer informatie over het gebruik van tempdb in SQL database.|
| 40552 |16 |De sessie is beëindigd vanwege het overmatige gebruik van het transactie logboek. Probeer minder rijen in één trans actie te wijzigen. Zie voor meer informatie over batch verwerking [batch verwerking gebruiken om de prestaties van SQL database-toepassingen te verbeteren](../performance-improve-use-batching.md).<br/><br/>Als u bulksgewijs invoegen uitvoert met het `bcp.exe` hulp programma of de `System.Data.SqlClient.SqlBulkCopy` klasse, kunt u de `-b batchsize` Opties of gebruiken `BatchSize` om het aantal rijen te beperken dat in elke trans actie naar de server is gekopieerd. Als u een index opnieuw wilt samen stellen met de `ALTER INDEX` -instructie, gebruikt u de `REBUILD WITH ONLINE = ON` optie. Voor informatie over de grootte van het transactie logboek voor het vCore-aankoop model raadpleegt u: <br/>&bull;&nbsp; [limieten op basis van vCore voor afzonderlijke data bases](resource-limits-vcore-single-databases.md)<br/>&bull;&nbsp; [limieten op basis van vCore voor elastische Pools](resource-limits-vcore-elastic-pools.md)<br/>&bull;&nbsp; [Resource limieten voor Azure SQL Managed instance](../managed-instance/resource-limits.md).|
| 40553 |16 |De sessie is beëindigd vanwege het overmatige geheugen gebruik. Wijzig uw query om minder rijen te verwerken.<br/><br/>Het verminderen van het aantal `ORDER BY` en de `GROUP BY` bewerkingen in uw Transact-SQL-code vermindert de geheugen vereisten van uw query. Zie voor het schalen van de Data Base [afzonderlijke database resources schalen](single-database-scale.md) en [bronnen voor elastische Pools schalen](elastic-pool-scale.md).|

## <a name="elastic-pool-errors"></a>Fouten in elastische Pools

De volgende fouten zijn gerelateerd aan het maken en gebruiken van elastische Pools:

| Foutcode | Ernst | Beschrijving | Corrigerende actie |
|:--- |:--- |:--- |:--- |
| 1132 | 17 |De elastische pool heeft de opslag limiet bereikt. Het opslag gebruik voor de elastische pool mag niet groter zijn dan (% d) MB. Poging tot het schrijven van gegevens naar een Data Base als de opslag limiet van de elastische pool is bereikt. Zie voor informatie over resource limieten: <br/>&bull;&nbsp; [Op DTU gebaseerde limieten voor elastische Pools](resource-limits-dtu-elastic-pools.md)<br/>&bull;&nbsp; [limieten op basis van vCore voor elastische Pools](resource-limits-vcore-elastic-pools.md). <br/> |U kunt overwegen de Dtu's van en/of opslag ruimte toe te voegen aan de elastische pool, indien mogelijk om de opslag limiet te verhogen, de opslag die wordt gebruikt door afzonderlijke data bases in de elastische pool te verminderen of data bases uit de elastische pool te verwijderen. Zie [elastische pool resources schalen](elastic-pool-scale.md)voor elastische Pools schalen. Zie [Bestands ruimte voor data bases beheren in Azure SQL database](file-space-manage.md)voor meer informatie over het verwijderen van ongebruikte ruimte uit data bases.|
| 10929 | 16 |De minimale garantie van% s is% d, de maximum limiet is% d en het huidige gebruik van de data base is% d. De server is momenteel echter bezet om aanvragen te ondersteunen die groter zijn dan% d voor deze data base. Zie voor informatie over resource limieten: <br/>&bull;&nbsp; [Op DTU gebaseerde limieten voor elastische Pools](resource-limits-dtu-elastic-pools.md)<br/>&bull;&nbsp; [limieten op basis van vCore voor elastische Pools](resource-limits-vcore-elastic-pools.md). <br/> Als dat niet het geval is, probeert u het later opnieuw. DTU/vCore min per data base; DTU/vCore Max per data base. Het totale aantal gelijktijdige werk rollen (aanvragen) voor alle data bases in de elastische pool heeft geprobeerd de limiet van de groep te overschrijden. |U kunt eventueel de Dtu's of vCores van de elastische pool verg Roten, zodat de werknemers limiet kan worden verhoogd of data bases uit de elastische pool kunnen worden verwijderd. |
| 40844 | 16 |De data base% ls op server% LS is een% ls Edition-data base in een elastische pool en kan geen doorlopende Kopieer relatie hebben.  |N.v.t. |
| 40857 | 16 |Kan de elastische groep niet vinden voor de server:% ls, naam van elastische groep:% ls. Opgegeven elastische groep bestaat niet in de opgegeven server. | Geef een geldige naam op voor een elastische pool. |
| 40858 | 16 |Elastische pool% ls bestaat al op de server:% ls. Opgegeven elastische groep bestaat al in de opgegeven server. | Geef een nieuwe naam voor de elastische groep op. |
| 40859 | 16 |De elastische pool biedt geen ondersteuning voor de servicelaag% ls. De opgegeven servicelaag wordt niet ondersteund voor het inrichten van elastische Pools. |Geef de juiste editie op of laat de servicelaag leeg om de standaard servicelaag te gebruiken. |
| 40860 | 16 |De combi natie van elastische pool% LS en service doelstelling% LS is ongeldig. Elastische pool en servicelaag kunnen alleen samen worden opgegeven als het resource type is opgegeven als ' ElasticPool '. |Geef een juiste combi natie van elastische pool en servicelaag op. |
| 40861 | 16 |De data base-editie%. *ls kan niet verschillen van de servicelaag van de elastische pool. deze is%.* ls. De data base-editie wijkt af van de servicelaag van de elastische pool. |Geef geen data base-editie op die verschilt van de servicelaag van de elastische pool.  Houd er rekening mee dat de data base-editie niet hoeft te worden opgegeven. |
| 40862 | 16 |De naam van de elastische groep moet worden opgegeven als de service doelstelling van de elastische groep is opgegeven. De service doelstelling van de elastische groep biedt geen unieke identificatie voor een elastische pool. |Geef de naam van de elastische groep op als u gebruikmaakt van de service doelstelling van de elastische groep. |
| 40864 | 16 |De Dtu's voor de elastische pool moet ten minste (% d) Dtu's zijn voor de servicelaag %1!. Er wordt geprobeerd de Dtu's voor de elastische pool in te stellen beneden de minimale limiet. |Stel de Dtu's voor de elastische pool opnieuw in op ten minste de minimale limiet. |
| 40865 | 16 |De Dtu's voor de elastische pool mag niet groter zijn dan (% d) Dtu's voor servicelaag %1!. Er wordt geprobeerd de Dtu's voor de elastische pool in te stellen boven de maximum limiet. |Stel de Dtu's voor de elastische pool in op een waarde die niet groter is dan de maximum limiet. |
| 40867 | 16 |Het maximum aantal DTU per data base moet ten minste (% d) zijn voor de servicelaag %1!. Er wordt geprobeerd het maximale DTU-maximum per data base in te stellen onder de ondersteunde limiet. | Overweeg het gebruik van de servicelaag van de elastische pool die de gewenste instelling ondersteunt. |
| 40868 | 16 |Het maximum aantal DTU per data base mag niet groter zijn dan (% d) voor servicelaag %1!. Er wordt geprobeerd het maximale DTU-maximum per data base in te stellen buiten de ondersteunde limiet. | Overweeg het gebruik van de servicelaag van de elastische pool die de gewenste instelling ondersteunt. |
| 40870 | 16 |Het DTU-minimum per data base mag niet groter zijn dan (% d) voor servicelaag %1!. Poging tot het instellen van de maximale DTU-minimum per data base dan de ondersteunde limiet. | Overweeg het gebruik van de servicelaag van de elastische pool die de gewenste instelling ondersteunt. |
| 40873 | 16 |Het aantal data bases (% d) en de DTU min per data base (% d) mag de Dtu's van de elastische pool (% d) niet overschrijden. Poging tot opgeven van DTU min voor data bases in de elastische pool die groter is dan de Dtu's van de elastische pool. | Overweeg de Dtu's van de elastische pool te verhogen, of verklein de DTU min per data base, of verklein het aantal data bases in de elastische pool. |
| 40877 | 16 |Een elastische pool kan alleen worden verwijderd als deze geen data bases bevat. De elastische pool bevat een of meer data bases en kan daarom niet worden verwijderd. |Verwijder data bases uit de elastische pool om deze te verwijderen. |
| 40881 | 16 |De elastische pool %1! ls heeft de limiet voor het aantal data bases bereikt.  De limiet voor het aantal data bases voor de elastische pool mag niet groter zijn dan (% d) voor een elastische pool met (% d) Dtu's. Poging om een Data Base te maken of toe te voegen aan een elastische pool wanneer de limiet voor het aantal data bases van de elastische pool is bereikt. | Overweeg zo mogelijk de Dtu's van de elastische pool te verhogen om de database limiet te verhogen of om data bases uit de elastische pool te verwijderen. |
| 40889 | 16 |De Dtu's of opslag limiet voor de elastische pool %1!!, kan niet worden verlaagd omdat er onvoldoende opslag ruimte beschikbaar is voor de data bases. Er wordt geprobeerd de opslag limiet van de elastische pool onder het opslag gebruik te verlagen. | Overweeg het opslag gebruik van afzonderlijke data bases in de elastische pool te verminderen of data bases uit de groep te verwijderen om de Dtu's of opslag limiet te verminderen. |
| 40891 | 16 |Het maximum aantal DTU per data base (% d) kan niet groter zijn dan de maximale DTU per data base (% d). Poging om de DTU min per data base in te stellen hoger dan het maximum aantal DTU per data base. |Zorg ervoor dat de minimale DTU per data base het maximale DTU-maximum per data base niet overschrijdt. |
| NOG TE BEPALEN | 16 |De opslag grootte voor een afzonderlijke data base in een elastische pool kan niet groter zijn dan de maximale grootte die is toegestaan door %1! de elastische pool van de service tier. De maximale grootte voor de data base overschrijdt de Maxi maal toegestane grootte van de servicelaag van de elastische groep. |Stel de maximale grootte van de data base in binnen de limieten van de Maxi maal toegestane grootte door de servicelaag van de elastische pool. |

## <a name="cannot-open-database-master-requested-by-the-login-the-login-failed"></a>Kan de data base ' Master ' die door de aanmelding is aangevraagd, niet openen. De aanmelding is mislukt

Dit probleem treedt op omdat het account geen machtiging heeft voor toegang tot de hoofd database. SQL Server Management Studio (SSMS) probeert standaard verbinding te maken met de hoofd database.

Volg deze stappen om dit probleem op te lossen:

1. Selecteer **Opties** in het aanmeldings scherm van SSMS en selecteer vervolgens **verbindings eigenschappen**.
2. Voer in het veld **verbinding maken met data base** de naam van de standaard database van de gebruiker in als de standaard aanmeldings database en selecteer vervolgens **verbinding maken**.

   ![Verbindingseigenschappen](./media/troubleshoot-common-errors-issues/cannot-open-database-master.png)

## <a name="confirm-whether-an-error-is-caused-by-a-connectivity-issue"></a>Controleren of een fout wordt veroorzaakt door een verbindings probleem

Als u wilt controleren of een fout wordt veroorzaakt door een probleem met de verbinding, bekijkt u de stack tracering voor frames die aanroepen weer geven om een verbinding te openen, zoals de volgende: (Let op de verwijzing naar de klasse **SqlConnection** ).

```
System.Data.SqlClient.SqlConnection.TryOpen(TaskCompletionSource`1 retry)
 at System.Data.SqlClient.SqlConnection.Open()
 at AzureConnectionTest.Program.Main(String[] args)
ClientConnectionId:<Client connection ID>
```

Wanneer de uitzonde ring wordt geactiveerd door query problemen, ziet u een aanroep stack die er ongeveer als volgt uitzien (Let op de verwijzing naar de klasse **SqlCommand** ). In dit geval kunt [u uw query's afstemmen](/archive/blogs/sqlblog/is-my-query-running-fine-in-the-cloud).

```
  at System.Data.SqlClient.SqlCommand.ExecuteReader()
  at AzureConnectionTest.Program.Main(String[] args)
  ClientConnectionId:<Client ID>
```

Raadpleeg de volgende bronnen voor meer informatie over de prestaties van fijnafstelling:

- [Azure SQL-indexen en-statistieken onderhouden](https://techcommunity.microsoft.com/t5/Azure-Database-Support-Blog/How-to-maintain-Azure-SQL-Indexes-and-Statistics/ba-p/368787)
- [De prestaties van de query in Azure SQL Database hand matig afstemmen](./performance-guidance.md)
- [Prestaties bewaken Azure SQL Database door dynamische beheer weergaven te gebruiken](./monitoring-with-dmvs.md)
- [Het query archief in Azure SQL Database gebruiken](/sql/relational-databases/performance/best-practice-with-the-query-store#Insight)

## <a name="steps-to-fix-common-connection-issues"></a>Stappen voor het oplossen van algemene verbindingsproblemen

1. Zorg ervoor dat TCP/IP is ingeschakeld als een client protocol op de toepassings server. Zie [client protocollen configureren](/sql/database-engine/configure-windows/configure-client-protocols)voor meer informatie. Op toepassings servers waarop geen SQL-hulpprogram ma's zijn geïnstalleerd, controleert u of TCP/IP is ingeschakeld door **cliconfg.exe** (SQL Server Client Network Utility) uit te voeren.
2. Controleer de connection string van de toepassing om te controleren of deze correct is geconfigureerd. Zorg er bijvoorbeeld voor dat de connection string de juiste poort (1433) en de volledig gekwalificeerde server naam opgeeft.
Zie [verbindings gegevens ophalen](./connect-query-ssms.md#get-server-connection-information).
3. Probeer de time-outwaarde voor de verbinding te verhogen. Het is raadzaam om een time-out voor de verbinding van ten minste 30 seconden te gebruiken.
4. Test de connectiviteit tussen de toepassings server en de Azure SQL Database met behulp van [SQL Server Management Studio (SSMS)](./connect-query-ssms.md), een udl-bestand, ping of Telnet. Zie [verbindings problemen](https://support.microsoft.com/help/4009936/solving-connectivity-errors-to-sql-server) en [diagnoses voor verbindings problemen](./troubleshoot-common-connectivity-issues.md#diagnostics)oplossen voor meer informatie.

   > [!NOTE]
   > Als stap voor het oplossen van problemen kunt u ook de connectiviteit op een andere client computer testen.

5. Zorg ervoor dat de logica voor opnieuw proberen is ingesteld als best practice. Zie [problemen met tijdelijke fouten en verbindings SQL database fouten oplossen](./troubleshoot-common-connectivity-issues.md)voor meer informatie over logica voor opnieuw proberen.

Als u met deze stappen het probleem niet kunt oplossen, kunt u proberen om meer gegevens te verzamelen en vervolgens contact op te nemen met de ondersteuning. Als uw toepassing een Cloud service is, schakelt u logboek registratie in. Deze stap retourneert een UTC-tijds tempel van de fout. Daarnaast retourneert SQL Database de tracerings-ID. [Micro soft Customer Support Services](https://azure.microsoft.com/support/options/) kan deze informatie gebruiken.

Zie [logboek registratie van diagnostische gegevens inschakelen voor apps in azure app service](../../app-service/troubleshoot-diagnostic-logs.md)voor meer informatie over het inschakelen van logboek registratie.

## <a name="next-steps"></a>Volgende stappen

- [Azure SQL Database-connectiviteitsarchitectuur](./connectivity-architecture.md)
- [Azure SQL Database-en Azure Synapse Analytics-netwerk toegangs beheer](./network-access-controls-overview.md)