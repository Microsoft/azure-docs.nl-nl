---
title: De replicatie van schema wijzigingen in SQL Data Sync automatiseren
description: Meer informatie over het automatiseren van de replicatie van schema wijzigingen in Azure SQL Data Sync.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: data sync
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 11/14/2018
ms.openlocfilehash: f9997ea737b96185b31a7f51996a396fb5fc46c4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92790182"
---
# <a name="automate-the-replication-of-schema-changes-in-azure-sql-data-sync"></a>De replicatie van schema wijzigingen in Azure SQL Data Sync automatiseren
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Met SQL Data Sync kunnen gebruikers gegevens synchroniseren tussen data bases in Azure SQL Database en SQL Server exemplaren in één richting of in beide richtingen. Een van de huidige beperkingen van SQL Data Sync is een gebrek aan ondersteuning voor de replicatie van schema wijzigingen. Telkens wanneer u het tabel schema wijzigt, moet u de wijzigingen hand matig Toep assen op alle eind punten, met inbegrip van de hub en alle leden, en vervolgens het synchronisatie schema bijwerken.

In dit artikel wordt een oplossing geïntroduceerd voor het automatisch repliceren van schema wijzigingen naar alle SQL Data Sync-eind punten.

1. Deze oplossing maakt gebruik van een DDL-trigger om schema wijzigingen bij te houden.
1. Met de trigger worden de opdrachten voor schema wijziging ingevoegd in een tracerings tabel.
1. Deze tracerings tabel wordt met behulp van de Data Sync-Service gesynchroniseerd met alle eind punten.
1. DML-triggers na het invoegen worden gebruikt om de schema wijzigingen toe te passen op de andere eind punten.

In dit artikel wordt ALTER TABLE gebruikt als voor beeld van een schema wijziging, maar deze oplossing werkt ook voor andere typen schema wijzigingen.

> [!IMPORTANT]
> We raden u aan dit artikel zorgvuldig te lezen, met name de secties over het [oplossen van problemen](#troubleshoot) en [andere overwegingen](#other), voordat u begint met het implementeren van automatische schema wijzigings replicatie in uw synchronisatie omgeving. We raden u ook aan om [synchronisatie gegevens te lezen in meerdere Cloud-en on-premises data bases met SQL Data Sync](sql-data-sync-data-sql-server-sql-database.md). Sommige database bewerkingen kunnen de oplossing die in dit artikel wordt beschreven, verstoren. Aanvullende domein kennis van SQL Server en Transact-SQL zijn mogelijk vereist om deze problemen op te lossen.

![De replicatie van schema wijzigingen automatiseren](./media/sql-data-sync-update-sync-schema/automate-schema-changes.png)

## <a name="set-up-automated-schema-change-replication"></a>Automatische schema wijzigings replicatie instellen

### <a name="create-a-table-to-track-schema-changes"></a>Een tabel maken voor het bijhouden van schema wijzigingen

Een tabel maken voor het bijhouden van schema wijzigingen in alle data bases in de synchronisatie groep:

```sql
CREATE TABLE SchemaChanges (
ID bigint IDENTITY(1,1) PRIMARY KEY,
SqlStmt nvarchar(max),
[Description] nvarchar(max)
)
```

Deze tabel bevat een identiteits kolom voor het bijhouden van de volg orde van schema wijzigingen. U kunt meer velden toevoegen om zo nodig meer informatie te registreren.

### <a name="create-a-table-to-track-the-history-of-schema-changes"></a>Een tabel maken om de geschiedenis van schema wijzigingen bij te houden

Maak op alle eind punten een tabel om de ID van de laatst toegepaste schema wijzigings opdracht bij te houden.

```sql
CREATE TABLE SchemaChangeHistory (
LastAppliedId bigint PRIMARY KEY
)
GO

INSERT INTO SchemaChangeHistory VALUES (0)
```

### <a name="create-an-alter-table-ddl-trigger-in-the-database-where-schema-changes-are-made"></a>Een DDL-trigger voor ALTER TABLE maken in de Data Base waarin schema wijzigingen worden aangebracht

Maak een DDL-trigger voor ALTER TABLE-bewerkingen. U hoeft deze trigger alleen in de data base te maken waarin schema wijzigingen worden aangebracht. Om conflicten te voor komen, mag u alleen schema wijzigingen in één data base in een synchronisatie groep toestaan.

```sql
CREATE TRIGGER AlterTableDDLTrigger
ON DATABASE
FOR ALTER_TABLE
AS

-- You can add your own logic to filter ALTER TABLE commands instead of replicating all of them.

IF NOT (EVENTDATA().value('(/EVENT_INSTANCE/SchemaName)[1]', 'nvarchar(512)') like 'DataSync')

INSERT INTO SchemaChanges (SqlStmt, Description)
    VALUES (EVENTDATA().value('(/EVENT_INSTANCE/TSQLCommand/CommandText)[1]', 'nvarchar(max)'), 'From DDL trigger')
```

De trigger voegt een record in de tabel schema wijzigingen bijhouden toe voor elke opdracht ALTER TABLE. In dit voor beeld wordt een filter toegevoegd om te voor komen dat wijzigingen in het schema worden gerepliceerd die zijn gemaakt onder schema **DataSync**, omdat dit het meest waarschijnlijk is gedaan door de Data Sync-Service. Voeg meer filters toe als u alleen bepaalde typen schema wijzigingen wilt repliceren.

U kunt ook meer triggers toevoegen om andere typen schema wijzigingen te repliceren. Maak bijvoorbeeld CREATE_PROCEDURE, ALTER_PROCEDURE en DROP_PROCEDURE triggers om wijzigingen in opgeslagen procedures te repliceren.

### <a name="create-a-trigger-on-other-endpoints-to-apply-schema-changes-during-insertion"></a>Een trigger maken op andere eind punten om schema wijzigingen tijdens het invoegen toe te passen

Deze trigger voert de schema wijzigings opdracht uit wanneer deze wordt gesynchroniseerd met andere eind punten. U moet deze trigger maken op alle eind punten, met uitzonde ring van het schema waarin wijzigingen worden aangebracht (dat wil zeggen, in de Data Base waarin de DDL `AlterTableDDLTrigger` -trigger in de vorige stap is gemaakt).

```sql
CREATE TRIGGER SchemaChangesTrigger
ON SchemaChanges
AFTER INSERT
AS
DECLARE \@lastAppliedId bigint
DECLARE \@id bigint
DECLARE \@sqlStmt nvarchar(max)
SELECT TOP 1 \@lastAppliedId=LastAppliedId FROM SchemaChangeHistory
SELECT TOP 1 \@id = id, \@SqlStmt = SqlStmt FROM SchemaChanges WHERE id \> \@lastAppliedId ORDER BY id
IF (\@id = \@lastAppliedId + 1)
BEGIN
    EXEC sp_executesql \@SqlStmt
        UPDATE SchemaChangeHistory SET LastAppliedId = \@id
    WHILE (1 = 1)
    BEGIN
        SET \@id = \@id + 1
        IF exists (SELECT id FROM SchemaChanges WHERE ID = \@id)
            BEGIN
                SELECT \@sqlStmt = SqlStmt FROM SchemaChanges WHERE ID = \@id
                EXEC sp_executesql \@SqlStmt
                UPDATE SchemaChangeHistory SET LastAppliedId = \@id
            END
        ELSE
            BREAK;
    END
END
```

Deze trigger wordt uitgevoerd na het invoegen en controleert of de huidige opdracht volgende moet worden uitgevoerd. De code logica zorgt ervoor dat er geen schema wijzigings instructie wordt overgeslagen en alle wijzigingen worden toegepast, zelfs als de invoeging niet in orde is.

### <a name="sync-the-schema-change-tracking-table-to-all-endpoints"></a>De tabel voor het bijhouden van schema wijzigingen synchroniseren met alle eind punten

U kunt de tabel voor het bijhouden van schema wijzigingen synchroniseren met alle eind punten met behulp van de bestaande synchronisatie groep of een nieuwe synchronisatie groep. Zorg ervoor dat de wijzigingen in de tracerings tabel kunnen worden gesynchroniseerd met alle eind punten, met name wanneer u een synchronisatie met één richting gebruikt.

Synchroniseer de tabel schema wijzigings geschiedenis niet, omdat die tabel verschillende statussen voor verschillende eind punten bijhoudt.

### <a name="apply-the-schema-changes-in-a-sync-group"></a>De schema wijzigingen Toep assen in een synchronisatie groep

Alleen schema wijzigingen die zijn aangebracht in de Data Base waarin de DDL-trigger wordt gemaakt, worden gerepliceerd. Schema wijzigingen die in andere data bases zijn aangebracht, worden niet gerepliceerd.

Nadat de schema wijzigingen zijn gerepliceerd naar alle eind punten, moet u ook extra stappen uitvoeren om het synchronisatie schema bij te werken om de synchronisatie van de nieuwe kolommen te starten of te stoppen.

#### <a name="add-new-columns"></a>Nieuwe kolommen toevoegen

1.  Maak de schema wijziging.

1.  Vermijd de wijziging van gegevens waarbij de nieuwe kolommen worden betrokken totdat u de stap hebt voltooid waarmee de trigger wordt gemaakt.

1.  Wacht tot de schema wijzigingen zijn toegepast op alle eind punten.

1.  Vernieuw het database schema en voeg de nieuwe kolom toe aan het synchronisatie schema.

1.  De gegevens in de nieuwe kolom worden gesynchroniseerd tijdens de volgende synchronisatie bewerking.

#### <a name="remove-columns"></a>Kolommen verwijderen

1.  Verwijder de kolommen uit het synchronisatie schema. Gegevens synchronisatie stopt met het synchroniseren van gegevens in deze kolommen.

1.  Maak de schema wijziging.

1.  Het database schema vernieuwen.

#### <a name="update-data-types"></a>Gegevens typen bijwerken

1.  Maak de schema wijziging.

1.  Wacht tot de schema wijzigingen zijn toegepast op alle eind punten.

1.  Het database schema vernieuwen.

1.  Als de nieuwe en oude gegevens typen niet volledig compatibel zijn: als u bijvoorbeeld wijzigt van `int` naar `bigint` -Sync, kan dit mislukken voordat de stappen voor het maken van de triggers zijn voltooid. Synchronisatie slaagt na een nieuwe poging.

#### <a name="rename-columns-or-tables"></a>De naam van kolommen of tabellen wijzigen

Het wijzigen van de naam van kolommen of tabellen zorgt ervoor dat de gegevens synchronisatie niet meer werkt. Maak een nieuwe tabel of kolom, backfill de gegevens in en verwijder vervolgens de oude tabel of kolom in plaats van de naam te wijzigen.

#### <a name="other-types-of-schema-changes"></a>Andere typen schema wijzigingen

Voor andere typen schema wijzigingen: het maken van opgeslagen procedures of het weghalen van een index voor het bijwerken van het synchronisatie schema is niet vereist.

## <a name="troubleshoot-automated-schema-change-replication"></a><a name="troubleshoot"></a> Automatische replicatie van schema wijzigingen oplossen

De replicatie logica die in dit artikel wordt beschreven, werkt niet meer in bepaalde situaties, bijvoorbeeld als u een schema wijziging hebt aangebracht in een on-premises data base die niet wordt ondersteund in Azure SQL Database. In dat geval mislukt het synchroniseren van de schema-tabel voor het bijhouden van wijzigingen. U moet dit probleem hand matig oplossen:

1.  Schakel de DDL-trigger uit en Voorkom verdere schema wijzigingen totdat het probleem is opgelost.

1.  In de eindpunt database waar het probleem zich voordoet, schakelt u de trigger na invoegen uit op het eind punt waar de schema wijziging niet kan worden doorgevoerd. Met deze actie kan de schema wijzigings opdracht worden gesynchroniseerd.

1.  Activeer de synchronisatie om de schema wijzigings tabel voor het bijhouden van wijzigingen te synchroniseren.

1.  In de eindpunt database waar het probleem zich voordoet, voert u een query uit in de tabel schema wijzigings geschiedenis om de ID van de laatst toegepaste schema wijzigings opdracht op te halen.

1.  Zoek in de tabel schema wijzigingen bijhouden een lijst met alle opdrachten met een ID die groter is dan de ID-waarde die u in de vorige stap hebt opgehaald.

    a.  Negeer deze opdrachten die niet kunnen worden uitgevoerd in de eindpunt database. U moet de consistentie van het schema afhandelen. Wijzig de oorspronkelijke schema wijzigingen als de inconsistentie van invloed is op uw toepassing.

    b.  Pas de opdrachten die moeten worden toegepast hand matig toe.

1.  Werk de tabel schema wijzigings geschiedenis bij en stel de laatst toegepaste ID in op de juiste waarde.

1.  Controleer of het schema up-to-date is.

1.  Schakel de trigger na invoegen in de tweede stap opnieuw in.

1.  Schakel de DDL-trigger die in de eerste stap is uitgeschakeld opnieuw in.

Als u de records in de tabel schema wijzigingen bijhouden wilt opschonen, gebruikt u verwijderen in plaats van TRUNCATe. Verstrek nooit de kolom Identity in de tabel schema voor het bijhouden van wijzigingen door gebruik te maken van DBCC CHECKIDENT. U kunt nieuwe plannings tabellen voor het bijhouden van wijzigingen maken en de tabel naam in de DDL-trigger bijwerken als opnieuw seeden vereist is.

## <a name="other-considerations"></a><a name="other"></a> Andere overwegingen

-   Database gebruikers die de hub-en leden databases configureren, moeten voldoende machtigingen hebben om de opdrachten voor schema wijziging uit te voeren.

-   U kunt meer filters toevoegen in de DDL-trigger om de schema wijziging alleen in de geselecteerde tabellen of bewerkingen te repliceren.

-   U kunt alleen schema wijzigingen aanbrengen in de Data Base waarin de DDL-trigger wordt gemaakt.

-   Als u een wijziging aanbrengt in een SQL Server-Data Base, moet u ervoor zorgen dat de schema wijziging wordt ondersteund in Azure SQL Database.

-   Als er schema wijzigingen worden aangebracht in andere data bases dan de Data Base waarin de DDL-trigger wordt gemaakt, worden de wijzigingen niet gerepliceerd. Om dit probleem te voor komen, kunt u DDL-triggers maken om wijzigingen op andere eind punten te blok keren.

-   Als u het schema van de tabel voor het bijhouden van wijzigingen wilt wijzigen, moet u de DDL-trigger uitschakelen voordat u de wijziging aanbrengt en vervolgens hand matig de wijziging Toep assen op alle eind punten. Het is niet mogelijk om het schema bij te werken in een trigger na invoegen in dezelfde tabel.

-   Verzaai de identiteits kolom niet opnieuw met behulp van DBCC CHECKIDENT.

-   Gebruik TRUNCATe niet om gegevens op te schonen in de tabel schema voor het bijhouden van wijzigingen.

## <a name="next-steps"></a>Volgende stappen

Zie de volgende onderwerpen voor meer informatie over SQL Data Sync:

-   Overzicht: [Gegevens synchroniseren tussen meerdere cloud- en on-premises databases met SQL Data Sync](sql-data-sync-data-sql-server-sql-database.md)
-   Data Sync instellen
    - In de portal- [zelf studie: SQL Data Sync instellen om gegevens te synchroniseren tussen Azure SQL database en SQL Server](sql-data-sync-sql-server-configure.md)
    - Met PowerShell
        -  [Power shell gebruiken om te synchroniseren tussen meerdere data bases in Azure SQL Database](scripts/sql-data-sync-sync-data-between-sql-databases.md)
        -  [Power shell gebruiken om te synchroniseren tussen een data base in Azure SQL Database en een data base in een SQL Server-exemplaar](scripts/sql-data-sync-sync-data-between-azure-onprem.md)
-   Data Sync-agent: [Data Sync-agent voor Azure SQL Data Sync](sql-data-sync-agent-overview.md)
-   Best practices: [Best practices voor Azure SQL Data Sync](sql-data-sync-best-practices.md)
-   Bewaken: [SQL Data Sync bewaken met Azure Monitor-logboeken](./monitor-tune-overview.md)
-   Problemen oplossen: [Problemen met Azure SQL Data Sync oplossen]()
-   Het synchronisatieschema bijwerken
    -   Met PowerShell: [PowerShell gebruiken voor het bijwerken van het synchronisatieschema in een bestaande synchronisatiegroep](scripts/update-sync-schema-in-sync-group.md)