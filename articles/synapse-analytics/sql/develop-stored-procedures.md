---
title: Opgeslagen procedures gebruiken
description: Tips voor het implementeren van opgeslagen procedures met Synapse SQL in azure Synapse Analytics voor het ontwikkelen van oplossingen.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 11/03/2020
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 083077b2b29bc415e83d6f5a76941fa83fa853e3
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101674141"
---
# <a name="stored-procedures-using-synapse-sql-in-azure-synapse-analytics"></a>Opgeslagen procedures met Synapse SQL in azure Synapse Analytics

Met Synapse SQL provisioned en serverloze groepen kunt u complexe gegevens verwerkings logica plaatsen in opgeslagen SQL-procedures. Opgeslagen procedures zijn een uitstekende manier om uw SQL-code te integreren en de gegevens op te slaan in het Data Warehouse. Met opgeslagen procedures kunnen ontwikkel aars hun oplossingen modularize door de code in te kapselen in beheer bare eenheden en meer herbruikbaarheid van code te vergemakkelijken. Elke opgeslagen procedure kan ook para meters accepteren om ze nog flexibeler te maken.
In dit artikel vindt u enkele tips voor het implementeren van opgeslagen procedures in Synapse SQL pool voor het ontwikkelen van oplossingen.

## <a name="what-to-expect"></a>Wat u kunt verwachten

Synapse SQL ondersteunt veel van de T-SQL-functies die worden gebruikt in SQL Server. Belang rijker is dat er specifieke functies zijn die u kunt gebruiken om de prestaties van uw oplossing te optimaliseren. In dit artikel vindt u informatie over de functies die u in opgeslagen procedures kunt plaatsen.

> [!NOTE]
> In de hoofd tekst van de procedure kunt u alleen de functies gebruiken die worden ondersteund in Synapse SQL surface area. Lees [dit artikel](overview-features.md) om objecten te identificeren, een instructie die kan worden gebruikt in opgeslagen procedures. In de voor beelden in deze artikelen worden algemene functies gebruikt die zowel in serverloze als toegewezen surface area beschikbaar zijn. Raadpleeg het einde van dit artikel voor meer [beperkingen in de ingerichte en serverloze Synapse SQL-groepen](#limitations) .

Voor het behoud van de schaal en prestaties van de SQL-groep zijn er ook enkele functies en functionaliteit die gedrags verschillen hebben en andere die niet worden ondersteund.

## <a name="stored-procedures-in-synapse-sql"></a>Opgeslagen procedures in Synapse SQL

In het volgende voor beeld ziet u de procedures waarmee externe objecten worden verwijderd als deze bestaan in de Data Base:

```sql
CREATE PROCEDURE drop_external_table_if_exists @name SYSNAME
AS BEGIN
    IF (0 <> (SELECT COUNT(*) FROM sys.external_tables WHERE name = @name))
    BEGIN
        DECLARE @drop_stmt NVARCHAR(200) = N'DROP EXTERNAL TABLE ' + @name; 
        EXEC sp_executesql @tsql = @drop_stmt;
    END
END
GO
CREATE PROCEDURE drop_external_file_format_if_exists @name SYSNAME
AS BEGIN
    IF (0 <> (SELECT COUNT(*) FROM sys.external_file_formats WHERE name = @name))
    BEGIN
        DECLARE @drop_stmt NVARCHAR(200) = N'DROP EXTERNAL FILE FORMAT ' + @name; 
        EXEC sp_executesql @tsql = @drop_stmt;
    END
END
GO
CREATE PROCEDURE drop_external_data_source_if_exists @name SYSNAME
AS BEGIN
    IF (0 <> (SELECT COUNT(*) FROM sys.external_data_sources WHERE name = @name))
    BEGIN
        DECLARE @drop_stmt NVARCHAR(200) = N'DROP EXTERNAL DATA SOURCE ' + @name; 
        EXEC sp_executesql @tsql = @drop_stmt;
    END
END
```

Deze procedures kunnen worden uitgevoerd met behulp van `EXEC` de instructie waarbij u de procedure naam en de para meters kunt opgeven:

```sql
EXEC drop_external_table_if_exists 'mytest';
EXEC drop_external_file_format_if_exists 'mytest';
EXEC drop_external_data_source_if_exists 'mytest';
```

Synapse SQL biedt een vereenvoudigde en gestroomlijnde implementatie van opgeslagen procedures. Het grootste verschil ten opzichte van SQL Server is dat de opgeslagen procedure geen vooraf gecompileerde code is. In data warehouses is de compilatie tijd klein in vergelijking met de tijd die nodig is om query's uit te voeren op grote gegevens volumes. Het is belang rijker om ervoor te zorgen dat de opgeslagen procedure code correct is geoptimaliseerd voor grote query's. Het doel is om uren, minuten en seconden op te slaan, niet in milliseconden. Het is daarom handiger om opgeslagen procedures te beschouwen als containers voor SQL-logica.

Wanneer Synapse SQL uw opgeslagen procedure uitvoert, worden de SQL-instructies geparseerd, vertaald en geoptimaliseerd tijdens runtime. Tijdens dit proces wordt elke instructie geconverteerd naar gedistribueerde query's. De SQL-code die wordt uitgevoerd op basis van de gegevens, wijkt af van de query die is verzonden.

## <a name="encapsulate-validation-rules"></a>Validatie regels inkapselen

Met opgeslagen procedures kunt u validatie logica vinden in één module die is opgeslagen in SQL database. In het volgende voor beeld kunt u zien hoe u de waarden van para meters valideert en de standaard waarden wijzigt.

```sql
CREATE PROCEDURE count_objects_by_date_created 
                            @start_date DATETIME2,
                            @end_date DATETIME2
AS BEGIN 

    IF( @start_date >= GETUTCDATE() )
    BEGIN
        THROW 51000, 'Invalid argument @start_date. Value should be in past.', 1;  
    END

    IF( @end_date IS NULL )
    BEGIN
        SET @end_date = GETUTCDATE();
    END

    IF( @start_date >= @end_date )
    BEGIN
        THROW 51000, 'Invalid argument @end_date. Value should be greater than @start_date.', 2;  
    END

    SELECT
         year = YEAR(create_date),
         month = MONTH(create_date),
         objects_created = COUNT(*)
    FROM
        sys.objects
    WHERE
        create_date BETWEEN @start_date AND @end_date
    GROUP BY
        YEAR(create_date), MONTH(create_date);
END
```

De logica in de SQL-procedure valideert de invoer parameters wanneer de procedure wordt aangeroepen.

```sql

EXEC count_objects_by_date_created '2020-08-01', '2020-09-01'

EXEC count_objects_by_date_created '2020-08-01', NULL

EXEC count_objects_by_date_created '2020-09-01', '2020-08-01'
-- Error
-- Invalid argument @end_date. Value should be greater than @start_date.

EXEC count_objects_by_date_created '2120-09-01', NULL
-- Error
-- Invalid argument @start_date. Value should be in past.
```

## <a name="nesting-stored-procedures"></a>Opgeslagen procedures nesten

Wanneer opgeslagen procedures andere opgeslagen procedures aanroepen of dynamische SQL uitvoeren, wordt de interne opgeslagen procedure of het aanroepen van de code genest.
Een voor beeld van een geneste procedure wordt weer gegeven in de volgende code:

```sql
CREATE PROCEDURE clean_up @name SYSNAME
AS BEGIN
    EXEC drop_external_table_if_exists @name;
    EXEC drop_external_file_format_if_exists @name;
    EXEC drop_external_data_source_if_exists @name;
END
```

Met deze procedure wordt een para meter geaccepteerd die een naam vertegenwoordigt en vervolgens andere procedures aanroept om de objecten met deze naam neer te zetten.
De SQL-pool Synapse ondersteunt Maxi maal acht geneste niveaus. Deze functie wijkt enigszins af van SQL Server. Het nest niveau in SQL Server is 32.

De op het hoogste niveau opgeslagen procedure aanroep is gelijk aan nest niveau 1.

```sql
EXEC clean_up 'mytest'
```

Als de opgeslagen procedure ook een andere EXEC-aanroep maakt, neemt het nest niveau toe tot twee.

```sql
CREATE PROCEDURE clean_up @name SYSNAME
AS
    EXEC drop_external_table_if_exists @name  -- This call is nest level 2
GO
EXEC clean_up 'mytest'  -- This call is nest level 1
```

Als de tweede procedure vervolgens een aantal dynamische SQL-procedures uitvoert, neemt het nest niveau toe tot drie.

```sql
CREATE PROCEDURE drop_external_table_if_exists @name SYSNAME
AS BEGIN
    /* See full code in the previous example */
    EXEC sp_executesql @tsql = @drop_stmt;  -- This call is nest level 3
END
GO
CREATE PROCEDURE clean_up @name SYSNAME
AS
    EXEC drop_external_table_if_exists @name  -- This call is nest level 2
GO
EXEC clean_up 'mytest'  -- This call is nest level 1
```

> [!NOTE]
> Synapse SQL biedt momenteel geen ondersteuning [voor @NESTLEVEL @](/sql/t-sql/functions/nestlevel-transact-sql?view=azure-sqldw-latest&preserve-view=true). U moet het nest niveau bijhouden. Het is niet onwaarschijnlijk dat u de limiet van acht geneste niveaus overschrijdt, maar als u dat wel doet, moet u uw code opnieuw instellen zodat deze overeenkomt met de geneste niveaus binnen deze limiet.

## <a name="insertexecute"></a>INSERT..EXESCHATTIGE

De ingerichte Synapse-SQL-groep staat niet toe dat u de resultatenset van een opgeslagen procedure gebruikt met een instructie INSERT. Er is een alternatieve benadering die u kunt gebruiken. Zie het artikel over [tijdelijke tabellen](develop-tables-temporary.md) voor de ingerichte Synapse SQL-groep voor een voor beeld.

## <a name="limitations"></a>Beperkingen

Er zijn een aantal aspecten van opgeslagen Transact-SQL-procedures die niet zijn geïmplementeerd in Synapse SQL, zoals:

| Functie/optie | Ingericht | Serverloos |
| --- | --- |
| Tijdelijke opgeslagen procedures | Nee | Ja |
| Genummerde opgeslagen procedures | Nee | Nee |
| Uitgebreide opgeslagen procedures | Nee | Nee |
| Opgeslagen CLR-procedures | Nee | Nee |
| Versleutelings optie | Nee | Ja |
| Replicatie-optie | Nee | Nee |
| Tabelwaardeparameters | Nee | Nee |
| Alleen-lezen-para meters | Nee | Nee |
| Standaard parameters | Nee | Ja |
| Uitvoerings contexten | Nee | Nee |
| Instructie return | Nee | Ja |
| INVOEGEN IN.. EXEC | Nee | Ja |

## <a name="next-steps"></a>Volgende stappen

Zie [ontwikkelings overzicht](develop-overview.md)voor meer tips voor ontwikkel aars.
