---
title: In-Memory OLTP verbetert SQL trans actie-prestaties
description: In-Memory OLTP aannemen om de transactionele prestaties in een bestaande data base in Azure SQL Database en Azure SQL Managed instance te verbeteren.
services: sql-database
ms.service: sql-database
ms.custom: sqldbrb=2
ms.subservice: development
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: MightyPen
ms.date: 11/07/2018
ms.openlocfilehash: e17e98e784b7453c87814c5cce5c03568f66b1cb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91619743"
---
# <a name="use-in-memory-oltp-to-improve-your-application-performance-in-azure-sql-database-and-azure-sql-managed-instance"></a>In-Memory OLTP gebruiken om de prestaties van uw toepassing in Azure SQL Database en Azure SQL Managed instance te verbeteren
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

[In-Memory OLTP](in-memory-oltp-overview.md) kan worden gebruikt voor het verbeteren van de prestaties van transactie verwerking, gegevens opname en tijdelijke gegevens Scenario's in [Premium-en bedrijfskritiek-laag](database/service-tiers-vcore.md) databases zonder de prijs categorie te verhogen.

> [!NOTE]
> Meer informatie over hoe [quorum de werk belasting van de Key Data Base verdubbelt tijdens het verlagen van DTU met 70% met Azure SQL database](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database)

Volg deze stappen om In-Memory OLTP in uw bestaande Data Base te nemen.

## <a name="step-1-ensure-you-are-using-a-premium-and-business-critical-tier-database"></a>Stap 1: Zorg ervoor dat u een Premium-en Bedrijfskritiek-laag database gebruikt

In-Memory OLTP wordt alleen ondersteund in data bases uit de Premium-en Bedrijfskritiek-laag. In-Memory wordt ondersteund als het geretourneerde resultaat 1 (niet 0) is:

```sql
SELECT DatabasePropertyEx(Db_Name(), 'IsXTPSupported');
```

*XTP* staat voor *extreme transactie verwerking*

## <a name="step-2-identify-objects-to-migrate-to-in-memory-oltp"></a>Stap 2: bepalen welke objecten moeten worden gemigreerd naar In-Memory OLTP

SSMS bevat een **overzichts rapport voor analyse van transactie prestaties** dat u kunt uitvoeren op een Data Base met een actieve werk belasting. Het rapport bevat tabellen en opgeslagen procedures die kandidaten zijn voor migratie naar In-Memory OLTP.

In SSMS, om het rapport te genereren:

* Klik in de **objectverkenner** met de rechter muisknop op uw database knooppunt.
* Klik op **rapporten**  >  **standaard rapporten**  >  **transactie prestatie analyse overzicht**.

Zie [bepalen of een tabel of opgeslagen procedure moet worden getransporteerd naar In-Memory OLTP](/sql/relational-databases/in-memory-oltp/determining-if-a-table-or-stored-procedure-should-be-ported-to-in-memory-oltp)voor meer informatie.

## <a name="step-3-create-a-comparable-test-database"></a>Stap 3: een vergelijk bare test database maken

Stel dat het rapport aangeeft dat uw data base een tabel heeft die kan worden geconverteerd naar een tabel die is geoptimaliseerd voor geheugen. We raden u aan eerst te testen om de vermelding te bevestigen door te testen.

U hebt een test kopie van uw productie database nodig. De test database moet zich op hetzelfde niveau van de servicelaag bezien als uw productie database.

U kunt als volgt de test database verfijnen:

1. Maak verbinding met de test database met behulp van SSMS.
2. Om te voor komen dat u de optie WITH (snap shot) in query's nodig hebt, stelt u de optie voor de data base in, zoals wordt weer gegeven in de volgende T-SQL-instructie:

   ```sql
   ALTER DATABASE CURRENT
    SET
        MEMORY_OPTIMIZED_ELEVATE_TO_SNAPSHOT = ON;
   ```

## <a name="step-4-migrate-tables"></a>Stap 4: tabellen migreren

U moet een door het geheugen geoptimaliseerde kopie maken en vullen van de tabel die u wilt testen. U kunt deze maken met behulp van:

* De handige wizard voor het optimaliseren van geheugen in SSMS.
* Hand matig T-SQL.

### <a name="memory-optimization-wizard-in-ssms"></a>Wizard geheugen optimalisatie in SSMS

Deze migratie optie gebruiken:

1. Maak verbinding met de test database met SSMS.
2. Klik in de **objectverkenner** met de rechter muisknop op de tabel en klik vervolgens op **geheugen optimalisatie Advisor**.

   De wizard **tabel geheugen optimalisatie Advisor** wordt weer gegeven.
3. Klik in de wizard op **migratie validatie** (of op de knop **volgende** ) om te zien of de tabel niet-ondersteunde functies bevat die niet worden ondersteund in tabellen die zijn geoptimaliseerd voor geheugen. Zie voor meer informatie:

   * De *controle lijst voor geheugen optimalisatie* in [geheugen optimalisatie Advisor](/sql/relational-databases/in-memory-oltp/memory-optimization-advisor).
   * [Transact-SQL-constructs worden niet ondersteund door In-Memory OLTP](/sql/relational-databases/in-memory-oltp/transact-sql-constructs-not-supported-by-in-memory-oltp).
   * [Migreren naar In-Memory OLTP](/sql/relational-databases/in-memory-oltp/plan-your-adoption-of-in-memory-oltp-features-in-sql-server).
4. Als de tabel geen niet-ondersteunde functies heeft, kan de Advisor het werkelijke schema en de gegevens migratie voor u uitvoeren.

### <a name="manual-t-sql"></a>Hand matig T-SQL

Deze migratie optie gebruiken:

1. Maak verbinding met uw test database met behulp van SSMS (of een soortgelijk hulp programma).
2. Verkrijg het volledige T-SQL-script voor uw tabel en de bijbehorende indexen.

   * Klik in SSMS met de rechter muisknop op het knoop punt van de tabel.
   * Klik op **script tabel als**  >  **maken voor**  >  **Nieuw query venster**.
3. Voeg in het Script-venster met (MEMORY_OPTIMIZED = aan) toe aan de CREATE TABLE-instructie.
4. Als er een geclusterde index is, wijzigt u deze in niet-geclusterde.
5. Wijzig de naam van de bestaande tabel met behulp van SP_RENAME.
6. Maak de nieuwe voor het geheugen geoptimaliseerde kopie van de tabel door uw bewerkte CREATE TABLE-script uit te voeren.
7. Kopieer de gegevens naar de tabel die is geoptimaliseerd voor geheugen met behulp van INSERT... SELECTEER * IN:

```sql
INSERT INTO <new_memory_optimized_table>
        SELECT * FROM <old_disk_based_table>;
```

## <a name="step-5-optional-migrate-stored-procedures"></a>Stap 5 (optioneel): opgeslagen procedures migreren

De functie In-Memory kan ook een opgeslagen procedure wijzigen voor betere prestaties.

### <a name="considerations-with-natively-compiled-stored-procedures"></a>Overwegingen met systeem eigen, gecompileerde, opgeslagen procedures

Een opgeslagen procedure met systeem eigen compilatie moet de volgende opties hebben voor de T-SQL WITH-component:

* NATIVE_COMPILATION
* SCHEMA binding: tabellen waarvan de opgeslagen procedure de kolom definities niet kan wijzigen, worden op welke manier dan ook van invloed op de opgeslagen procedure, tenzij u de opgeslagen procedure verwijdert.

Een systeem eigen module moet een Big [atomische blok kering](/sql/relational-databases/in-memory-oltp/atomic-blocks-in-native-procedures) voor transactie beheer gebruiken. Er is geen rol voor een expliciete BEGIN TRANSACTION of voor TERUGDRAAI transacties. Als uw code een schending van een bedrijfs regel detecteert, kan het atomische blok met een instructie [throw](/sql/t-sql/language-elements/throw-transact-sql) worden beëindigd.

### <a name="typical-create-procedure-for-natively-compiled"></a>Typische PROCEDURE voor maken voor systeem eigen compilatie

Normaal gesp roken is de T-SQL voor het maken van een opgeslagen procedure met systeem eigen compilatie vergelijkbaar met de volgende sjabloon:

```sql
CREATE PROCEDURE schemaname.procedurename
    @param1 type1, …
    WITH NATIVE_COMPILATION, SCHEMABINDING
    AS
        BEGIN ATOMIC WITH
            (TRANSACTION ISOLATION LEVEL = SNAPSHOT,
            LANGUAGE = N'your_language__see_sys.languages'
            )
        …
        END;
```

* Voor de TRANSACTION_ISOLATION_LEVEL is moment opname de meest voorkomende waarde voor de systeem eigen, gecompileerde, opgeslagen procedure. Een subset van de andere waarden wordt echter ook ondersteund:
  
  * HERHAAL BARE LEES BEWERKING
  * SERIALIZABLE
* De taal waarde moet aanwezig zijn in de weer gave sys. languages.

### <a name="how-to-migrate-a-stored-procedure"></a>Een opgeslagen procedure migreren

De migratie stappen zijn:

1. Haal het script voor het maken van de PROCEDURE op in de regel matig geïnterpreteerde opgeslagen procedure.
2. Herschrijf de koptekst zodat deze overeenkomt met de vorige sjabloon.
3. Controleer of de T-SQL-code van de opgeslagen procedure gebruikmaakt van functies die niet worden ondersteund voor systeem eigen, gecompileerde, opgeslagen procedures. Implementeer zo nodig tijdelijke oplossingen.

   Zie [migratie problemen voor systeem eigen, gecompileerde, opgeslagen procedures](/sql/relational-databases/in-memory-oltp/a-guide-to-query-processing-for-memory-optimized-tables)voor meer informatie.
4. Wijzig de naam van de oude opgeslagen procedure met behulp van SP_RENAME. U kunt deze gewoon verwijderen.
5. Voer uw bewerkte PROCEDURE T-SQL-script maken uit.

## <a name="step-6-run-your-workload-in-test"></a>Stap 6: Voer de werk belasting uit in de test

Voer een workload in uw test database uit die vergelijkbaar is met de werk belasting die wordt uitgevoerd in uw productie database. Hiermee wordt de prestatie verbetering onthuld die wordt behaald door uw gebruik van de functie In-Memory voor tabellen en opgeslagen procedures.

De belangrijkste kenmerken van de werk belasting zijn:

* Aantal gelijktijdige verbindingen.
* Ratio voor lezen/schrijven.

Als u de werk belasting van de test wilt aanpassen en uitvoeren, kunt u het beste het handige `ostress.exe` hulp programma gebruiken, dat wordt geïllustreerd in dit artikel [in het geheugen](in-memory-oltp-overview.md) .

Als u de netwerk latentie wilt minimaliseren, voert u uw test uit in dezelfde Azure-geografische regio waar de data base zich bevindt.

## <a name="step-7-post-implementation-monitoring"></a>Stap 7: bewaking na de implementatie

Overweeg de prestatie-effecten van uw In-Memory implementaties in productie te controleren:

* [In-Memory opslag bewaken](in-memory-oltp-monitor-space.md).
* [Bewaken met dynamische beheer weergaven](database/monitoring-with-dmvs.md)

## <a name="related-links"></a>Verwante koppelingen

* [In-Memory OLTP (In-Memory-optimalisatie)](/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization)
* [Inleiding tot systeem eigen, gecompileerde, opgeslagen procedures](/sql/relational-databases/in-memory-oltp/a-guide-to-query-processing-for-memory-optimized-tables)
* [Geheugen optimalisatie Advisor](/sql/relational-databases/in-memory-oltp/memory-optimization-advisor)
