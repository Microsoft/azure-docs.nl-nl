---
title: Langdurige opgeslagen procedures in de SQL-connector verwerken
description: Opgeslagen procedures verwerken die een time-out hebben bij het gebruik van de SQL-connector in Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: camerost, logicappspm
ms.topic: conceptual
ms.date: 10/27/2020
ms.openlocfilehash: f5b04c563dc81497f591788dc4890d379c0f898f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93102968"
---
# <a name="handle-stored-procedure-timeouts-in-the-sql-connector-for-azure-logic-apps"></a>Time-outs voor opgeslagen procedures in de SQL-connector voor Azure Logic Apps verwerken

Wanneer uw logische app werkt met resultaten sets zodat de SQL- [connector](../connectors/connectors-create-api-sqlazure.md) niet alle resultaten tegelijkertijd retourneert, of als u meer controle wilt hebben over de grootte en de structuur voor uw resultaten sets, kunt u een [opgeslagen procedure](/sql/relational-databases/stored-procedures/stored-procedures-database-engine) maken waarmee de resultaten op de gewenste manier worden georganiseerd. De SQL-connector biedt veel back-end-functies die u kunt gebruiken met [Azure Logic apps](../logic-apps/logic-apps-overview.md) , zodat u gemakkelijker zakelijke taken kunt automatiseren die samen werken met SQL database tabellen.

Als er bijvoorbeeld meerdere rijen worden opgehaald of ingevoegd, kan de logische app deze rijen door lopen met behulp van een [ **until** -lus](../logic-apps/logic-apps-control-flow-loops.md#until-loop) binnen deze [grenzen](../logic-apps/logic-apps-limits-and-config.md). Als uw logische app echter moet werken met duizenden of miljoenen rijen, wilt u de kosten die het gevolg zijn van aanroepen naar de data base, tot een minimum beperkt. Zie [bulk gegevens verwerken met behulp van de SQL-connector](../connectors/connectors-create-api-sqlazure.md#handle-bulk-data)voor meer informatie.

<a name="timeout-limit"></a>

## <a name="timeout-limit-on-stored-procedure-execution"></a>Time-outlimiet voor het uitvoeren van opgeslagen procedures

De SQL-connector heeft een time-outlimiet van een opgeslagen procedure die [korter is dan 2 minuten](/connectors/sql/#known-issues-and-limitations). Een aantal opgeslagen procedures kan langer duren dan deze limiet is bereikt, waardoor `504 Timeout` er een fout optreedt. Soms worden deze langlopende processen specifiek gecodeerd als opgeslagen procedures. Als gevolg van de time-outlimiet, kan het aanroepen van deze procedures vanuit Azure Logic Apps problemen veroorzaken. Hoewel de SQL-connector geen systeem eigen ondersteuning biedt voor een asynchrone modus, kunt u dit probleem omzeilen en deze modus simuleren met behulp van een SQL-voltooiings trigger, een native SQL Pass Through-query, een status tabel en taken aan de server zijde. Voor deze taak kunt u de [Azure elastische taak agent](../azure-sql/database/elastic-jobs-overview.md) gebruiken voor [Azure SQL database](../azure-sql/database/sql-database-paas-overview.md). Voor [SQL Server on-premises](/sql/sql-server/sql-server-technical-documentation) en [Azure SQL Managed instance](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md)kunt u de [SQL Server Agent](/sql/ssms/agent/sql-server-agent)gebruiken.

Stel dat u de volgende langlopende opgeslagen procedure hebt, wat langer duurt dan de time-outlimiet voor het uitvoeren van de uitvoering. Als u deze opgeslagen procedure uitvoert vanuit een logische app met behulp van de SQL-connector, treedt er een `HTTP 504 Gateway Timeout` fout op.

```sql
CREATE PROCEDURE [dbo].[WaitForIt]
   @delay char(8) = '00:03:00'
AS
BEGIN
   SET NOCOUNT ON;
   WAITFOR DELAY @delay
END
```

In plaats van de opgeslagen procedure rechtstreeks aan te roepen, kunt u de procedure op de achtergrond asynchroon uitvoeren met behulp van een *taak agent*. U kunt de invoer en uitvoer opslaan in een status tabel die u vervolgens kunt gebruiken via uw logische app. Als u de invoer en uitvoer niet nodig hebt, of als u de resultaten al naar een tabel in de opgeslagen procedure schrijft, kunt u deze aanpak vereenvoudigen.

> [!IMPORTANT]
> Zorg ervoor dat de opgeslagen procedure en alle taken *idempotent* zijn, wat betekent dat ze meerdere keren kunnen worden uitgevoerd zonder dat dit van invloed is op de resultaten. Als de asynchrone verwerking mislukt of als er een time-out optreedt, kan de taak agent de stap opnieuw uitvoeren, en dus ook uw opgeslagen procedure, meerdere keren. Lees deze [Aanbevolen procedures en benaderingen](../azure-sql/database/elastic-jobs-overview.md#idempotent-scripts)om te voor komen dat uitvoer wordt gedupliceerd voordat u objecten maakt.

In de volgende sectie wordt beschreven hoe u de Azure elastische taak agent kunt gebruiken voor Azure SQL Database. Voor SQL Server en Azure SQL Managed Instance kunt u de SQL Server Agent gebruiken. Sommige beheer details verschillen, maar de belangrijkste stappen blijven hetzelfde als het instellen van een taak agent voor Azure SQL Database.

<a name="azure-sql-database"></a>

## <a name="job-agent-for-azure-sql-database"></a>Taak agent voor Azure SQL Database

Als u een taak wilt maken waarmee de opgeslagen procedure voor [Azure SQL database](../azure-sql/database/sql-database-paas-overview.md)kan worden uitgevoerd, gebruikt u de [Azure elastische taak agent](../azure-sql/database/elastic-jobs-overview.md). Maak uw taak agent in het Azure Portal. Met deze methode worden verschillende opgeslagen procedures toegevoegd aan de data base die wordt gebruikt door de agent, ook wel bekend als de *agent database*. U kunt vervolgens een taak maken waarmee de opgeslagen procedure in de doel database wordt uitgevoerd en de uitvoer wordt vastgelegd wanneer deze is voltooid.

Voordat u de taak kunt maken, moet u machtigingen, groepen en doelen instellen, zoals wordt beschreven in de [volledige documentatie voor de Azure elastische taak agent](../azure-sql/database/elastic-jobs-overview.md). U moet ook een ondersteunende tabel maken in de doel database, zoals wordt beschreven in de volgende secties.

<a name="create-state-table"></a>

### <a name="create-state-table-for-registering-parameters-and-storing-inputs"></a>Status tabel maken voor het registreren van para meters en het opslaan van invoer

SQL-Agent taken accepteren geen invoer parameters. Maak in plaats daarvan een status tabel in de doel database waarin u de para meters registreert en sla de invoer op die moet worden gebruikt voor het aanroepen van uw opgeslagen procedures. Alle stappen van de agent taak worden uitgevoerd op basis van de doel database, maar de opgeslagen procedures van de taak worden uitgevoerd op de agent database. 

Als u de status tabel wilt maken, gebruikt u dit schema:

```sql
CREATE TABLE [dbo].[LongRunningState](
   [jobid] [uniqueidentifier] NOT NULL,
   [rowversion] [timestamp] NULL,
   [parameters] [nvarchar](max) NULL,
   [start] [datetimeoffset](7) NULL,
   [complete] [datetimeoffset](7) NULL,
   [code] [int] NULL,
   [result] [nvarchar](max) NULL,
   CONSTRAINT [PK_LongRunningState] PRIMARY KEY CLUSTERED
      (   [jobid] ASC
      )WITH (STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF) ON [PRIMARY]
      ) ON [PRIMARY] TEXTIMAGE_ON [PRIMARY]
```

De resulterende tabel ziet er als volgt uit [SQL Server Management Studio (smss)](/sql/ssms/download-sql-server-management-studio-ssms):

![Scherm opname waarin de gemaakte status tabel wordt weer gegeven waarin invoer voor opgeslagen procedure wordt opgeslagen.](media/handle-long-running-stored-procedures-sql-connector/state-table-input-parameters.png)

Om te zorgen voor goede prestaties en ervoor te zorgen dat de agent taak de bijbehorende record kan vinden, gebruikt de tabel de taak uitvoering-ID ( `jobid` ) als primaire sleutel. Als u wilt, kunt u ook afzonderlijke kolommen toevoegen voor de invoer parameters. Het eerder beschreven schema kan meer in het algemeen meerdere para meters verwerken, maar is beperkt tot de grootte die wordt berekend door `NVARCHAR(MAX)` .

<a name="create-top-level-job"></a>

### <a name="create-a-top-level-job-to-run-the-stored-procedure"></a>Een taak op het hoogste niveau maken om de opgeslagen procedure uit te voeren

Als u de langlopende opgeslagen procedure wilt uitvoeren, maakt u deze taak agent op het hoogste niveau in de agent database:

```sql
EXEC jobs.sp_add_job 
   @job_name='LongRunningJob',
   @description='Execute Long-Running Stored Proc',
   @enabled = 1
```

Voeg nu stappen toe aan de taak die para meters, voer uit en voltooi de opgeslagen procedure. Een taak stap is standaard een time-out na 12 uur. Als uw opgeslagen procedure meer tijd nodig heeft of als u wilt dat de procedure eerder een time-out heeft, kunt u de `step_timeout_seconds` para meter wijzigen in een andere waarde die in seconden is opgegeven. Standaard heeft een stap 10 ingebouwde nieuwe pogingen met een uitstel-out tussen elke nieuwe poging, die u kunt gebruiken voor uw voor deel.

Hier volgen de stappen die u kunt toevoegen:

1. Wacht totdat de para meters in de tabel worden weer gegeven `LongRunningState` .

   Bij deze eerste stap wordt gewacht tot de para meters worden toegevoegd in de `LongRunningState` tabel. dit gebeurt binnenkort nadat de taak is gestart. Als de taak uitvoering-ID ( `jobid` ) niet wordt toegevoegd aan de `LongRunningState` tabel, wordt de stap alleen mislukt en wordt de standaard time-out voor opnieuw proberen of uitstel de wacht tijd:

   ```sql
   EXEC jobs.sp_add_jobstep
      @job_name='LongRunningJob',
      @step_name= 'Parameterize WaitForIt',
      @step_timeout_seconds = 30,
      @command= N'
         IF NOT EXISTS(SELECT [jobid] FROM [dbo].[LongRunningState]
            WHERE jobid = $(job_execution_id))
            THROW 50400, ''Failed to locate call parameters (Step1)'', 1',
      @credential_name='JobRun',
      @target_group_name='DatabaseGroupLongRunning'
   ```

1. Query's uitvoeren op de para meters van de status tabel en deze door geven aan de opgeslagen procedure. Met deze stap wordt ook de procedure op de achtergrond uitgevoerd. 

   Als uw opgeslagen procedure geen para meters nodig heeft, roept u de opgeslagen procedure gewoon rechtstreeks aan. Als u de para meter wilt door geven, `@timespan` gebruikt `@callparams` u de, die u ook kunt uitbreiden om aanvullende para meters door te geven.

   ```sql
   EXEC jobs.sp_add_jobstep
      @job_name='LongRunningJob',
      @step_name='Execute WaitForIt',
      @command=N'
         DECLARE @timespan char(8)
         DECLARE @callparams NVARCHAR(MAX)
         SELECT @callparams = [parameters] FROM [dbo].[LongRunningState]
            WHERE jobid = $(job_execution_id))
         SET @timespan = @callparams
         EXECUTE [dbo].[WaitForIt] @delay = @timespan', 
      @credential_name='JobRun',
      @target_group_name='DatabaseGroupLongRunning'
   ```

1. Voltooi de taak en noteer de resultaten.

   ```sql
   EXEC jobs.sp_add_jobstep
      @job_name='LongRunningJob',
      @step_name='Complete WaitForIt',
      @command=N'
         UPDATE [dbo].[LongRunningState]
            SET [complete] = GETUTCDATE(),
               [code] = 200,
               [result] = ''Success''
            WHERE jobid = $(job_execution_id)',
      @credential_name='JobRun',
      @target_group_name='DatabaseGroupLongRunning'
   ```

<a name="start-job-pass-parameters"></a>

### <a name="start-the-job-and-pass-the-parameters"></a>De taak starten en de para meters door geven

Als u de taak wilt starten, gebruikt u een passthrough native query met de [actie **een SQL-query uitvoeren en voert**](/connectors/sql/#execute-a-sql-query-(v2)) u de para meters van de taak onmiddellijk in de status tabel in. Als u invoer wilt opgeven voor het `jobid` kenmerk in de doel tabel, voegt Logic apps een **voor elke** lus toe die de tabel uitvoer van de voor gaande actie doorloopt. Voer voor elke taak uitvoering- **id een actie invoegrij uit** die gebruikmaakt van de dynamische gegevens uitvoer, `ResultSets JobExecutionId` , om de para meters voor de taak toe te voegen en door te geven aan de opgeslagen doel procedure.

![Scherm opname van de acties die moeten worden gebruikt voor het starten van de taak en het door geven van para meters aan de opgeslagen procedure.](media/handle-long-running-stored-procedures-sql-connector/start-job-actions.png)

Wanneer de taak is voltooid, werkt de taak de `LongRunningState` tabel bij, zodat u gemakkelijk kunt activeren op het resultaat door gebruik te maken van de [trigger **Wanneer een item wordt gewijzigd**](/connectors/sql/#when-an-item-is-modified-(v2)). Als u de uitvoer niet nodig hebt, of als u al een trigger hebt waarmee een uitvoer tabel wordt gecontroleerd, kunt u dit gedeelte overs Laan.

![Scherm opname waarin de SQL-trigger wordt weer gegeven wanneer een item wordt gewijzigd.](media/handle-long-running-stored-procedures-sql-connector/trigger-on-results-after-job-completes.png)

<a name="sql-on-premises-or-managed-instance"></a>

## <a name="job-agent-for-sql-server-or-azure-sql-managed-instance"></a>Taak agent voor SQL Server of door Azure SQL beheerd exemplaar

Voor hetzelfde scenario kunt u de [SQL Server Agent](/sql/ssms/agent/sql-server-agent) gebruiken voor [SQL Server on-premises](/sql/sql-server/sql-server-technical-documentation) en [Azure SQL Managed instance](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md). Hoewel sommige beheer details verschillen, blijven de basis stappen hetzelfde als het instellen van een taak agent voor Azure SQL Database.

## <a name="next-steps"></a>Volgende stappen

[Verbinding maken met SQL Server, Azure SQL Database of Azure SQL Managed instance](../connectors/connectors-create-api-sqlazure.md)

