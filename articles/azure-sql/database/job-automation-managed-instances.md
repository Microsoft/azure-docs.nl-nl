---
title: Taak automatisering met SQL-Agent taken in Azure SQL Managed instance
description: Opties voor automatisering om Transact-SQL (T-SQL)-scripts uit te voeren in Azure SQL Managed instance
services: sql-database
ms.service: sql-db-mi
ms.subservice: features
ms.custom: sqldbrb=1
dev_langs:
- TSQL
ms.topic: conceptual
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: ''
ms.date: 02/01/2021
ms.openlocfilehash: beb82f8435aea817a074ce83fddc6a5417b86c26
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100417266"
---
# <a name="automate-management-tasks-using-sql-agent-jobs-in-azure-sql-managed-instance"></a>Beheer taken automatiseren met SQL-Agent taken in Azure SQL Managed instance
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Met [SQL Server Agent](/sql/ssms/agent/sql-server-agent) in SQL Server en [SQL Managed instance](../../azure-sql/managed-instance/sql-managed-instance-paas-overview.md)kunt u taken maken en plannen die regel matig kunnen worden uitgevoerd op een of meer data bases om Transact-SQL (T-SQL)-query's uit te voeren en onderhouds taken uit te voeren. In dit artikel is SQL Agent voor SQL Managed instance geïntroduceerd.

> [!Note]
> SQL-Agent is niet beschikbaar in Azure SQL Database of Azure Synapse Analytics. In plaats daarvan wordt het [automatiseren van taken aangeraden met elastische taken](job-automation-overview.md).

### <a name="sql-agent-job-limitations-in-azure-sql-managed-instance"></a>Beperkingen van SQL-Agent taken in Azure SQL Managed instance

Het is een goed idee om de verschillen tussen de SQL-Agent die beschikbaar is in SQL Server en als onderdeel van een door SQL beheerd exemplaar. Zie voor meer informatie over de ondersteunde functie verschillen tussen SQL Server en SQL Managed instance [Azure SQL Managed instance T-SQL-verschillen van SQL Server](../../azure-sql/managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent). 

Sommige functies van de SQL-Agent die beschikbaar zijn in SQL Server worden niet ondersteund in een SQL Managed instance:

- SQL Agent-instellingen zijn alleen-lezen. 
    - De door het systeem opgeslagen procedure `sp_set_agent_properties` wordt niet ondersteund in een SQL Managed instance.
- Het in-/uitschakelen van SQL-Agent wordt momenteel niet ondersteund in het SQL Managed instance. SQL Agent wordt altijd uitgevoerd.
- Meldingen worden gedeeltelijk ondersteund:
  - Pager wordt niet ondersteund.
  - NetSend wordt niet ondersteund.
  - Waarschuwingen worden niet ondersteund.
- Proxy's worden niet ondersteund.
- Gebeurtenislogboek wordt niet ondersteund.
- De trigger voor taak planning op basis van een niet-actieve CPU wordt niet ondersteund.

## <a name="when-to-use-sql-agent-jobs"></a>SQL-Agent taken gebruiken 

Er zijn verschillende scenario's voor het gebruik van SQL-Agent taken:

- Beheertaken automatiseren en deze vervolgens plannen voor uitvoering op elke weekdag, na kantooruren enzovoort.
  - Implementeer schemawijzigingen, referentiebeheer, het verzamelen van prestatiegegevens of telemetrie van tenant (klant).
  - Referentiegegevens (algemene informatie voor alle databases) bijwerken, gegevens laden uit Azure-blobopslag.
  - Veelvoorkomende onderhouds taken, waaronder DBCC CHECKDB, zorgen voor gegevens integriteit of index onderhoud om de query prestaties te verbeteren. Configureer taken die op terugkerende basis, bijvoorbeeld tijdens daluren, moeten worden uitgevoerd.
  - Verzamel op continue basis queryresultaten uit een reeks databases in een centrale tabel. Prestatiequery's kunnen voortdurend worden uitgevoerd en geconfigureerd om de uitvoering van aanvullende taken te activeren.
- Gegevens verzamelen voor rapportage
  - Verzamel gegevens uit een verzameling databases in één doeltabel.
  - Voer langer durende gegevensverwerkingsquery's uit voor een groot aantal databases, zoals het verzamelen van klanttelemetrie. Resultaten worden in één doeltabel verzameld voor verdere analyse.
- Gegevensverplaatsingen
  - Taken maken waarmee wijzigingen die zijn aangebracht in uw databases, naar andere databases worden gerepliceerd of updates verzamelen die zijn uitgevoerd in externe databases en wijzigingen doorvoeren in de database.
  - Taken maken waarmee gegevens vanuit of naar uw databases worden geladen met behulp van SQL Server Integration Services (SSIS).

## <a name="sql-agent-jobs-in-azure-sql-managed-instance"></a>SQL-Agent taken in Azure SQL Managed instance

SQL-Agent taken worden uitgevoerd door de SQL Agent-service die nog steeds wordt gebruikt voor taak automatisering in SQL Server en SQL Managed instance. 

SQL Agent-taken zijn een reeks T-SQL-scripts die voor uw database zijn opgegeven. Gebruik taken om een administratieve taak te definiëren die een keer of vaker kan worden uitgevoerd en kan worden gecontroleerd op slagen of mislukken. 

Een taak kan op één lokale server of op meerdere externe servers worden uitgevoerd. SQL-Agent taken zijn een interne data base-engine component die wordt uitgevoerd binnen de service SQL Managed instance.

Er zijn enkele belangrijke concepten in SQL Agent-taken:

- **Taakstappen** zijn een of meer stappen die in de taak moet worden uitgevoerd. U kunt voor elke stap een strategie voor opnieuw proberen definiëren en de actie die moet plaatsvinden moet als de taakstap is geslaagd of mislukt.
- **Schema's** definiëren wanneer de taak moet worden uitgevoerd.
- Met **meldingen** kunt u regels definiëren die worden gebruikt om een melding naar operators te sturen via een e-mailbericht wanneer de taak is voltooid.

### <a name="sql-agent-job-steps"></a>Taak stappen voor SQL-Agent

SQL Agent-taakstappen zijn reeksen met acties die door SQL Agent moeten worden uitgevoerd. Elke stap bevat de volgende stap die moet worden uitgevoerd als de stap is geslaagd of mislukt en in het laatste geval het aantal nieuwe pogingen in geval van een fout.

Met SQL-Agent kunt u verschillende typen taak stappen maken, zoals Transact-SQL-taak stappen voor het uitvoeren van één Transact-SQL-batch op basis van de data base, of de besturingssysteem opdracht/Power shell-stappen waarmee een aangepast besturings systeem script kan worden uitgevoerd, [SSIS-taak stappen](/azure/data-factory/how-to-invoke-ssis-package-managed-instance-agent) waarmee u gegevens kunt laden met SSIS runtime of [replicatie](../managed-instance/replication-transactional-overview.md) stappen die wijzigingen van uw Data Base naar andere data base

> [!Note]
> Zie voor meer informatie over het gebruik van de Azure SSIS-Integration Runtime met SSISDB die door Azure SQL Managed instance worden gehost [Azure SQL Managed instance met SQL Server Integration Services (SSIS) in azure Data Factory gebruiken](/../azure/data-factory/how-to-use-sql-managed-instance-with-ir.md).

[Transactionele replicatie](../managed-instance/replication-transactional-overview.md) kan de wijzigingen van uw tabellen repliceren naar andere data bases in Azure SQL Managed Instance, Azure SQL Database of SQL Server. Zie [replicatie configureren in Azure SQL Managed instance](../../azure-sql/managed-instance/replication-between-two-instances-configure-tutorial.md)voor meer informatie. 

Andere typen taak stappen worden momenteel niet ondersteund in een SQL Managed instance, waaronder:

- Het samenvoegen van replicatietaakstappen wordt niet ondersteund.
- De wachtrijlezer wordt niet ondersteund.
- Analysis Services worden niet ondersteund
 
### <a name="sql-agent-job-schedules"></a>SQL Agent-taak schema's

Een schema geeft aan wanneer een taak wordt uitgevoerd. Er kan meer dan één taak worden uitgevoerd in hetzelfde schema en er kan meer dan één schema worden toegepast op dezelfde taak.

Een schema kan de volgende voorwaarden definiëren voor het moment waarop een taak wordt uitgevoerd:

- Wanneer SQL Server Agent wordt gestart. De taak wordt geactiveerd na elke failover.
- Eén keer op een specifieke datum en tijd, wat handig is voor uitgestelde uitvoering van een taak.
- Met een terugkerend schema.

> [!Note]
> Met SQL Managed Instance kunt u op dit moment geen taak starten wanneer de CPU niet actief is.

### <a name="sql-agent-job-notifications"></a>Taak meldingen van SQL-Agent

Met SQL Agent-taken kunt u meldingen ontvangen wanneer de taak is voltooid of mislukt. U kunt meldingen via e-mail ontvangen.

Als dit nog niet is ingeschakeld, moet u eerst [de functie database mail](/sql/relational-databases/database-mail/database-mail) configureren op het beheerde exemplaar van Azure SQL:

```sql
GO
EXEC sp_configure 'show advanced options', 1;
GO
RECONFIGURE;
GO
EXEC sp_configure 'Database Mail XPs', 1;
GO
RECONFIGURE
```

Stel een voor beeld uit van het e-mail account dat wordt gebruikt om de e-mail meldingen te verzenden. Wijs het account toe aan het e-mail profiel met de naam `AzureManagedInstance_dbmail_profile` . Als u e-mail berichten wilt verzenden met SQL-Agent taken in een SQL Managed instance, moet er een profiel worden aangeroepen `AzureManagedInstance_dbmail_profile` . Als dat niet het geval is, kan SQL Managed instance geen e-mail berichten verzenden via SQL-Agent. Zie het volgende voor beeld:

```sql
-- Create a Database Mail account
EXECUTE msdb.dbo.sysmail_add_account_sp
    @account_name = 'SQL Agent Account',
    @description = 'Mail account for Azure SQL Managed Instance SQL Agent system.',
    @email_address = '$(loginEmail)',
    @display_name = 'SQL Agent Account',
    @mailserver_name = '$(mailserver)' ,
    @username = '$(loginEmail)' ,
    @password = '$(password)';

-- Create a Database Mail profile
EXECUTE msdb.dbo.sysmail_add_profile_sp
    @profile_name = 'AzureManagedInstance_dbmail_profile',
    @description = 'E-mail profile used for messages sent by Managed Instance SQL Agent.';

-- Add the account to the profile
EXECUTE msdb.dbo.sysmail_add_profileaccount_sp
    @profile_name = 'AzureManagedInstance_dbmail_profile',
    @account_name = 'SQL Agent Account',
    @sequence_number = 1;
```

De configuratie van de Database Mail via T-SQL testen met behulp van de door [sp_send_db_mail](/sql/relational-databases/system-stored-procedures/sp-send-dbmail-transact-sql) systeem opgeslagen procedure:

```sql
DECLARE @body VARCHAR(4000) = 'The email is sent from ' + @@SERVERNAME;
EXEC msdb.dbo.sp_send_dbmail  
    @profile_name = 'AzureManagedInstance_dbmail_profile',  
    @recipients = 'ADD YOUR EMAIL HERE',  
    @body = 'Add some text',  
    @subject = 'Azure SQL Instance - test email';  
```

U kunt de operator waarschuwen dat er iets is gebeurd met uw SQL Agent-taken. Een operator definieert contactgegevens voor een persoon die verantwoordelijk is voor het onderhoud van een of meer beheerde SQL-exemplaren. Soms zijn operatorverantwoordelijkheden toegewezen aan één persoon.

In systemen met meerdere exemplaren in SQL Managed Instance of SQL Server, kunnen veel personen operatorverantwoordelijkheden delen. Een operator bevat geen informatie over beveiliging, en definieert geen beveiligingsprincipal. In het ideale geval is een operator geen persoon waarvan de verantwoordelijkheden kunnen veranderen, maar een e-mail distributie groep.   

U kunt [Opera tors maken](/sql/relational-databases/system-stored-procedures/sp-add-operator-transact-sql) met behulp van SQL Server Management Studio (SSMS) of het Transact-SQL-script dat in het volgende voor beeld wordt weer gegeven:

```sql
EXEC msdb.dbo.sp_add_operator
    @name=N'AzureSQLTeam',
    @enabled=1,
    @email_address=N'AzureSQLTeamn@contoso.com';
```

Bevestig het slagen of mislukken van het e-mail bericht via het [database mail-logboek](/sql/relational-databases/database-mail/database-mail-log-and-audits) in SSMS.

U kunt vervolgens [elke SQL-Agent taak wijzigen](/sql/relational-databases/system-stored-procedures/sp-update-job-transact-sql) en Opera tors toewijzen die via e-mail worden gewaarschuwd als de taak is voltooid, mislukt of slaagt met behulp van SSMS of het volgende Transact-SQL-script:

```sql
EXEC msdb.dbo.sp_update_job @job_name=N'Load data using SSIS',
    @notify_level_email=3, -- Options are: 1 on succeed, 2 on failure, 3 on complete
    @notify_email_operator_name=N'AzureSQLTeam';
```

### <a name="sql-agent-job-history"></a>Taak geschiedenis van de SQL-Agent

Met Azure SQL Managed Instance kunt u op dit moment geen SQL-Agent eigenschappen wijzigen omdat deze zijn opgeslagen in de onderliggende register waarden. Dit betekent dat opties voor het aanpassen van het Bewaar beleid voor de agent voor taak geschiedenis records worden vastgesteld op basis van 1000 totaal aantal records en Maxi maal 100 geschiedenis records per taak.

### <a name="sql-agent-fixed-database-role-membership"></a>Lidmaatschap van vaste databaserol voor SQL-Agent

Als gebruikers die zijn gekoppeld aan niet-sysadmin-aanmeldingen, worden toegevoegd aan een van de drie vaste database rollen van SQL-Agent in de msdb-systeem database, bestaat er een probleem waarbij expliciete uitvoer machtigingen moeten worden verleend aan de opgeslagen hoofd procedures voor deze aanmeldingen. Als dit probleem wordt aangetroffen, wordt het fout bericht ' de machtiging voor uitvoeren is geweigerd op het object <object_name> (Microsoft SQL Server, fout: 229) ' weer gegeven. 

Nadat u gebruikers aan een vaste databaserol van SQL-Agent (SQLAgentUserRole, SQLAgentReaderRole of SQLAgentOperatorRole) in msdb hebt toegevoegd, voert u voor elk van de aanmeldingen van de gebruiker aan deze rollen het onderstaande T-SQL-script uit om expliciet uitvoer machtigingen te verlenen aan de vermelde opgeslagen systeem procedures. In dit voor beeld wordt ervan uitgegaan dat de gebruikers naam en aanmeldings naam hetzelfde zijn. 

```sql
USE [master]
GO
CREATE USER [login_name] FOR LOGIN [login_name];
GO
GRANT EXECUTE ON master.dbo.xp_sqlagent_enum_jobs TO [login_name];
GRANT EXECUTE ON master.dbo.xp_sqlagent_is_starting TO [login_name];
GRANT EXECUTE ON master.dbo.xp_sqlagent_notify TO [login_name];
```

## <a name="learn-more"></a>Lees meer

- [Wat is Azure SQL Managed Instance?](../managed-instance/sql-managed-instance-paas-overview.md)
- [Wat is er nieuw in Azure SQL Database & SQL Managed instance?](../../azure-sql/database/doc-changes-updates-release-notes.md?tabs=managed-instance)
- [T-SQL-verschillen met Azure SQL Managed instance van SQL Server](../../azure-sql/managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent)
- [Vergelijking van functies: Azure SQL Database en Azure SQL Managed instance](../../azure-sql/database/features-comparison.md)