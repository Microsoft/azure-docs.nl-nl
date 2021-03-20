---
title: XEvent Ringbuffer-code
description: Biedt een voorbeeld van Transact-SQL-code die eenvoudig en snel kan worden gemaakt, door gebruik te maken van het Ringbuffer-doel in Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: sqldbrb=1
ms.devlang: PowerShell
ms.topic: sample
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: sstein
ms.date: 12/19/2018
ms.openlocfilehash: a646588616b874e40b1ed2a5a0b5e691b075075d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96487300"
---
# <a name="ring-buffer-target-code-for-extended-events-in-azure-sql-database"></a>Ringbuffer-doelcode voor uitgebreide gebeurtenissen in Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

[!INCLUDE [sql-database-xevents-selectors-1-include](../../../includes/sql-database-xevents-selectors-1-include.md)]

U wilt een volledig codevoorbeeld voor de eenvoudigste snelle manier om informatie vast te leggen en te rapporteren voor een uitgebreide gebeurtenis tijdens een test. Het eenvoudigste doel voor uitgebreide gebeurtenisgegevens is het [Ringbuffer-doel](/previous-versions/sql/sql-server-2016/bb630339(v=sql.130)).

Dit onderwerp bevat een voorbeeld van Transact-SQL-code waarmee de volgende acties worden uitgevoerd:

1. Maken van een tabel met gegevens voor een demonstratie.
2. Maken van een sessie voor een bestaande uitgebreide gebeurtenis, namelijk **sqlserver.sql_statement_starting**.

   * De gebeurtenis is beperkt tot SQL-instructies die een bepaalde UPDATE-tekenreeks bevatten: **instructies ZOALS: %UPDATE tabEmployee%** .
   * Kiezen voor het verzenden van de gebeurtenisuitvoer naar een Ringbuffer-doel of -type, namelijk **package0.ring_buffer**.
3. Starten van de gebeurtenissessie.
4. Uitgeven van een aantal eenvoudige SQL UPDATE-instructies.
5. Uitgeven van een SQL SELECT-instructie om gebeurtenisuitvoer op te halen uit de Ringbuffer.

   * **sys.dm_xe_database_session_targets** en andere DMV’s (dynamische beheerweergaven) worden gekoppeld.
6. Stoppen van de gebeurtenissessie.
7. Verwijderen van het Ringbuffer-doel om de bijbehorende resources vrij te geven.
8. Verwijderen van de gebeurtenissessie en de demotabel.

## <a name="prerequisites"></a>Vereisten

* Een Azure-account en -abonnement. U  kunt zich aanmelden voor een [gratis proefversie](https://azure.microsoft.com/pricing/free-trial/). 
* Een willekeurige database waarin u een tabel kunt maken.
  
  * U kunt optioneel in enkele minuten [een **AdventureWorksLT**-demonstratiedatabase maken](single-database-create-quickstart.md).
* SQL Server Management Studio (ssms.exe), liefst de meest recente versie van de maandelijkse update.
  U kunt de meest recente ssms.exe downloaden op:
  
  * Onderwerp met de titel [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms).
  * [Een directe koppeling naar de download.](https://go.microsoft.com/fwlink/?linkid=616025)

## <a name="code-sample"></a>Codevoorbeeld

Het volgende voorbeeld van Ringbuffer-code kan met minimale wijzigingen worden uitgevoerd in Azure SQL Database of Microsoft SQL Server. Het verschil is de aanwezigheid van het knooppunt '_database' in de naam van sommige DMV’s (dynamische beheerweergaven), gebruikt in het FROM-component in Stap 5. Bijvoorbeeld:

* sys.dm_xe<strong>_database</strong>_session_targets
* sys.dm_xe_session_targets

&nbsp;

```sql
GO
----  Transact-SQL.
---- Step set 1.

SET NOCOUNT ON;
GO


IF EXISTS
    (SELECT * FROM sys.objects
        WHERE type = 'U' and name = 'tabEmployee')
BEGIN
    DROP TABLE tabEmployee;
END
GO


CREATE TABLE tabEmployee
(
    EmployeeGuid         uniqueIdentifier   not null  default newid()  primary key,
    EmployeeId           int                not null  identity(1,1),
    EmployeeKudosCount   int                not null  default 0,
    EmployeeDescr        nvarchar(256)          null
);
GO


INSERT INTO tabEmployee ( EmployeeDescr )
    VALUES ( 'Jane Doe' );
GO

---- Step set 2.


IF EXISTS
    (SELECT * from sys.database_event_sessions
        WHERE name = 'eventsession_gm_azuresqldb51')
BEGIN
    DROP EVENT SESSION eventsession_gm_azuresqldb51
        ON DATABASE;
END
GO


CREATE
    EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    ADD EVENT
        sqlserver.sql_statement_starting
            (
            ACTION (sqlserver.sql_text)
            WHERE statement LIKE '%UPDATE tabEmployee%'
            )
    ADD TARGET
        package0.ring_buffer
            (SET
                max_memory = 500   -- Units of KB.
            );
GO

---- Step set 3.


ALTER EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    STATE = START;
GO

---- Step set 4.


SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM tabEmployee;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 102;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 1015;

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM tabEmployee;
GO

---- Step set 5.


SELECT
    se.name                      AS [session-name],
    ev.event_name,
    ac.action_name,
    st.target_name,
    se.session_source,
    st.target_data,
    CAST(st.target_data AS XML)  AS [target_data_XML]
FROM
               sys.dm_xe_database_session_event_actions  AS ac

    INNER JOIN sys.dm_xe_database_session_events         AS ev  ON ev.event_name = ac.event_name
        AND CAST(ev.event_session_address AS BINARY(8)) = CAST(ac.event_session_address AS BINARY(8))

    INNER JOIN sys.dm_xe_database_session_object_columns AS oc
         ON CAST(oc.event_session_address AS BINARY(8)) = CAST(ac.event_session_address AS BINARY(8))

    INNER JOIN sys.dm_xe_database_session_targets        AS st
         ON CAST(st.event_session_address AS BINARY(8)) = CAST(ac.event_session_address AS BINARY(8))

    INNER JOIN sys.dm_xe_database_sessions               AS se
         ON CAST(ac.event_session_address AS BINARY(8)) = CAST(se.address AS BINARY(8))
WHERE
        oc.column_name = 'occurrence_number'
    AND
        se.name        = 'eventsession_gm_azuresqldb51'
    AND
        ac.action_name = 'sql_text'
ORDER BY
    se.name,
    ev.event_name,
    ac.action_name,
    st.target_name,
    se.session_source
;
GO

---- Step set 6.


ALTER EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    STATE = STOP;
GO

---- Step set 7.


ALTER EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    DROP TARGET package0.ring_buffer;
GO

---- Step set 8.


DROP EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE;
GO

DROP TABLE tabEmployee;
GO
```

&nbsp;

## <a name="ring-buffer-contents"></a>Ringbuffer-inhoud

We hebben `ssms.exe` gebruikt om het codevoorbeeld uit te voeren.

Om de resultaten te bekijken hebben we op de cel geklikt onder de kolomkop **target_data_XML**.

Vervolgens hebben we in het resultatendeelvenster op de cel geklikt onder de kolomkop **target_data_XML**. Met deze klik is nog een tabblad Bestand gemaakt in ssms.exe waarop de inhoud van de resultaatcel wordt weergegeven, als XML.

De uitvoer wordt weergegeven in het volgende blok. Het lijkt lang, maar het zijn slechts twee **\<event>** -elementen.

&nbsp;

```
<RingBufferTarget truncated="0" processingTime="0" totalEventsProcessed="2" eventCount="2" droppedCount="0" memoryUsed="1728">
  <event name="sql_statement_starting" package="sqlserver" timestamp="2015-09-22T15:29:31.317Z">
    <data name="state">
      <type name="statement_starting_state" package="sqlserver" />
      <value>0</value>
      <text>Normal</text>
    </data>
    <data name="line_number">
      <type name="int32" package="package0" />
      <value>7</value>
    </data>
    <data name="offset">
      <type name="int32" package="package0" />
      <value>184</value>
    </data>
    <data name="offset_end">
      <type name="int32" package="package0" />
      <value>328</value>
    </data>
    <data name="statement">
      <type name="unicode_string" package="package0" />
      <value>UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 102</value>
    </data>
    <action name="sql_text" package="sqlserver">
      <type name="unicode_string" package="package0" />
      <value>
---- Step set 4.


SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM tabEmployee;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 102;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 1015;

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM tabEmployee;
</value>
    </action>
  </event>
  <event name="sql_statement_starting" package="sqlserver" timestamp="2015-09-22T15:29:31.327Z">
    <data name="state">
      <type name="statement_starting_state" package="sqlserver" />
      <value>0</value>
      <text>Normal</text>
    </data>
    <data name="line_number">
      <type name="int32" package="package0" />
      <value>10</value>
    </data>
    <data name="offset">
      <type name="int32" package="package0" />
      <value>340</value>
    </data>
    <data name="offset_end">
      <type name="int32" package="package0" />
      <value>486</value>
    </data>
    <data name="statement">
      <type name="unicode_string" package="package0" />
      <value>UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 1015</value>
    </data>
    <action name="sql_text" package="sqlserver">
      <type name="unicode_string" package="package0" />
      <value>
---- Step set 4.


SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM tabEmployee;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 102;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 1015;

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM tabEmployee;
</value>
    </action>
  </event>
</RingBufferTarget>
```

### <a name="release-resources-held-by-your-ring-buffer"></a>Resources vrijgeven uit de Ringbuffer

Wanneer u klaar bent met de Ringbuffer, kunt u deze als volgt verwijderen en de bijbehorende resources vrijgeven die een **ALTER** uitgeven:

```sql
ALTER EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    DROP TARGET package0.ring_buffer;
GO
```

De definitie van uw gebeurtenissessie wordt bijgewerkt, maar niet verwijderd. U kunt later nog een exemplaar van de Ringbuffer toevoegen aan uw gebeurtenissessie:

```sql
ALTER EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    ADD TARGET
        package0.ring_buffer
            (SET
                max_memory = 500   -- Units of KB.
            );
```

## <a name="more-information"></a>Meer informatie

Het primaire onderwerp voor uitgebreide gebeurtenissen in Azure SQL Database is:

* [Overwegingen voor uitgebreide gebeurtenissen in Azure SQL Database](xevent-db-diff-from-svr.md), in tegenstelling tot een aantal aspecten van uitgebreide gebeurtenissen die verschillen tussen Azure SQL Database versus Microsoft SQL Server.

Andere onderwerpen over codevoorbeelden zijn beschikbaar via de volgende koppelingen. U moet echter regelmatig een willekeurig voorbeeld controleren om te zien of het gericht is op Microsoft SQL Server versus Azure SQL Database. Vervolgens kunt u bepalen of er kleine wijzigingen nodig zijn om het voorbeeld uit te voeren.

* Codevoorbeeld voor Azure SQL Database: [Doelcode voor gebeurtenisbestand voor uitgebreide gebeurtenissen in Azure SQL Database](xevent-code-event-file.md)

<!--
('lock_acquired' event.)

- Code sample for SQL Server: [Determine Which Queries Are Holding Locks](/sql/relational-databases/extended-events/determine-which-queries-are-holding-locks)
- Code sample for SQL Server: [Find the Objects That Have the Most Locks Taken on Them](/sql/relational-databases/extended-events/find-the-objects-that-have-the-most-locks-taken-on-them)
-->