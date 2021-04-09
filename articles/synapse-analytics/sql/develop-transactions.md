---
title: Trans acties gebruiken
description: Tips voor het implementeren van trans acties met een toegewezen SQL-groep in azure Synapse Analytics voor het ontwikkelen van oplossingen.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 8af6802f785718ca6064a34c98d9f6dafc046a2c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104594764"
---
# <a name="use-transactions-with-dedicated-sql-pool-in-azure-synapse-analytics"></a>Trans acties met een toegewezen SQL-groep gebruiken in azure Synapse Analytics

Tips voor het implementeren van trans acties met een toegewezen SQL-groep in azure Synapse Analytics voor het ontwikkelen van oplossingen.

## <a name="what-to-expect"></a>Wat u kunt verwachten

Zoals u verwacht, ondersteunt de toegewezen SQL-pool trans acties als onderdeel van de werk belasting van het Data Warehouse. Om ervoor te zorgen dat de prestaties van de toegewezen SQL-groep worden gehandhaafd op schaal, zijn sommige functies beperkt in vergelijking tot SQL Server. In dit artikel worden de verschillen gemarkeerd en worden de andere weer gegeven.

## <a name="transaction-isolation-levels"></a>Trans actie-isolatie niveaus

De toegewezen SQL-Groep implementeert zuur transacties. Het isolatie niveau van de transactionele ondersteuning is standaard om niet-doorgevoerd te lezen.  U kunt deze wijzigen om doorgevoerde MOMENTOPNAME isolatie te lezen door de optie READ_COMMITTED_SNAPSHOT data base in te scha kelen voor een gebruikers database wanneer deze is verbonden met de hoofd database.  

Wanneer deze optie is ingeschakeld, worden alle trans acties in deze data base uitgevoerd onder Lees-VASTGELEGDe snap shot-isolatie en wordt het lezen van niet-toegewezen op sessie niveau niet in rekening gehouden. Raadpleeg [ALTER data base set Options (Transact-SQL)](/sql/t-sql/statements/alter-database-transact-sql-set-options?preserve-view=true&view=azure-sqldw-latest) voor meer informatie.

## <a name="transaction-size"></a>Transactie grootte
Een trans actie met één gegevens wijziging is beperkt. De limiet wordt per distributie toegepast. Als zodanig kan de totale toewijzing worden berekend door de limiet te vermenigvuldigen met het aantal distributies. 

Om het maximum aantal rijen in de trans actie te benaderen, deelt u de verdelings limiet door de totale grootte van elke rij. Overweeg voor kolommen met variabele lengte een gemiddelde kolom lengte in plaats van de maximum grootte te gebruiken.

In de onderstaande tabel zijn de volgende veronderstellingen aangebracht:

* Er is een gelijkmatige verdeling van gegevens opgetreden
* De gemiddelde rijlengte is 250 bytes

## <a name="gen2"></a>Gen2

| [DWU](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | Cap per distributie (GB) | Aantal distributies | MAXIMALE transactie grootte (GB) | Aantal rijen per distributie | Maximum aantal rijen per trans actie |
| --- | --- | --- | --- | --- | --- |
| DW100c |1 |60 |60 |4.000.000 |240.000.000 |
| DW200c |1.5 |60 |90 |6,000,000 |360.000.000 |
| DW300c |2.25 |60 |135 |9.000.000 |540.000.000 |
| DW400c |3 |60 |180 |12.000.000 |720.000.000 |
| DW500c |3,75 |60 |225 |15.000.000 |900.000.000 |
| DW1000c |7,5 |60 |450 |30.000.000 |1.800.000.000 |
| DW1500c |11,25 |60 |675 |45.000.000 |2.700.000.000 |
| DW2000c |15 |60 |900 |60.000.000 |3.600.000.000 |
| DW2500c |18,75 |60 |1125 |75.000.000 |4.500.000.000 |
| DW3000c |22.5 |60 |1.350 |90.000.000 |5.400.000.000 |
| DW5000c |37.5 |60 |2.250 |150.000.000 |9.000.000.000 |
| DW6000c |45 |60 |2.700 |180.000.000 |10.800.000.000 |
| DW7500c |56,25 |60 |3.375 |225.000.000 |13.500.000.000 |
| DW10000c |75 |60 |4.500 |300,000,000 |18.000.000.000 |
| DW15000c |112.5 |60 |6750 |450.000.000 |27.000.000.000 |
| DW30000c |225 |60 |13.500 |900.000.000 |54.000.000.000 |

## <a name="gen1"></a>Gen1

| [DWU](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | Cap per distributie (GB) | Aantal distributies | MAXIMALE transactie grootte (GB) | Aantal rijen per distributie | Maximum aantal rijen per trans actie |
| --- | --- | --- | --- | --- | --- |
| DW100 |1 |60 |60 |4.000.000 |240.000.000 |
| DW200 |1.5 |60 |90 |6,000,000 |360.000.000 |
| DW300 |2.25 |60 |135 |9.000.000 |540.000.000 |
| DW400 |3 |60 |180 |12.000.000 |720.000.000 |
| DW500 |3,75 |60 |225 |15.000.000 |900.000.000 |
| DW600 |4.5 |60 |270 |18.000.000 |1.080.000.000 |
| DW1000 |7,5 |60 |450 |30.000.000 |1.800.000.000 |
| DW1200 |9 |60 |540 |36.000.000 |2.160.000.000 |
| DW1500 |11,25 |60 |675 |45.000.000 |2.700.000.000 |
| DW2000 |15 |60 |900 |60.000.000 |3.600.000.000 |
| DW3000 |22.5 |60 |1.350 |90.000.000 |5.400.000.000 |
| DW6000 |45 |60 |2.700 |180.000.000 |10.800.000.000 |

De limiet voor de transactie grootte wordt toegepast per trans actie of bewerking. Het wordt niet toegepast op alle gelijktijdige trans acties. Elke trans actie is daarom toegestaan om deze hoeveelheid gegevens naar het logboek te schrijven.

Als u de hoeveelheid gegevens die naar het logboek moet worden geschreven, wilt optimaliseren en minimaliseren, raadpleegt u het artikel [trans acties best practices](../sql-data-warehouse/sql-data-warehouse-develop-best-practices-transactions.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) .

> [!WARNING]
> De maximale transactie grootte kan alleen worden bereikt voor HASH-of ROUND_ROBIN gedistribueerde tabellen waarin de sprei ding van de gegevens zich ook bevindt. Als de trans actie gegevens op een gescheefe manier naar de distributies schrijft, wordt de limiet waarschijnlijk bereikt vóór de maximale transactie grootte.
> <!--REPLICATED_TABLE-->

## <a name="transaction-state"></a>Transactie status

Voor de exclusieve SQL-groep wordt de functie XACT_STATE () gebruikt om een mislukte trans actie te rapporteren met de waarde-2. Deze waarde betekent dat de trans actie is mislukt en alleen is gemarkeerd voor terugdraaien.

> [!NOTE]
> Het gebruik van-2 door de functie XACT_STATE om een mislukte trans actie aan te duiden, vertegenwoordigt een ander gedrag voor SQL Server. SQL Server gebruikt de waarde-1 om een niet-doorvoer bare trans actie weer te geven. SQL Server kunt een aantal fouten binnen een trans actie verdragen zonder dat het als niet-doorvoerbaar moet worden gemarkeerd. Er `SELECT 1/0` kan bijvoorbeeld een fout optreden, maar geen trans actie geforceerd worden uitgevoerd. Met SQL Server wordt ook lees bewerkingen in de niet-doorvoer bare trans actie toegestaan. Met exclusieve SQL-groep kunt u dit echter niet doen. Als er een fout optreedt in een toegewezen SQL-groeps transactie, wordt automatisch de status-2 ingevoerd en kunt u geen verdere SELECT-instructies meer maken totdat de instructie terug is teruggedraaid. Het is daarom belang rijk om te controleren of de toepassings code gebruikmaakt van XACT_STATE (), omdat u mogelijk code wijzigingen moet aanbrengen.

In SQL Server ziet u bijvoorbeeld een trans actie die er ongeveer als volgt uitziet:

```sql
SET NOCOUNT ON;
DECLARE @xact_state smallint = 0;

BEGIN TRAN
    BEGIN TRY
        DECLARE @i INT;
        SET     @i = CONVERT(INT,'ABC');
    END TRY
    BEGIN CATCH
        SET @xact_state = XACT_STATE();

        SELECT  ERROR_NUMBER()    AS ErrNumber
        ,       ERROR_SEVERITY()  AS ErrSeverity
        ,       ERROR_STATE()     AS ErrState
        ,       ERROR_PROCEDURE() AS ErrProcedure
        ,       ERROR_MESSAGE()   AS ErrMessage
        ;

        IF @@TRANCOUNT > 0
        BEGIN
            ROLLBACK TRAN;
            PRINT 'ROLLBACK';
        END

    END CATCH;

IF @@TRANCOUNT >0
BEGIN
    PRINT 'COMMIT';
    COMMIT TRAN;
END

SELECT @xact_state AS TransactionState;
```

De voor gaande code bevat het volgende fout bericht:

Msg 111233, niveau 16, status 1, regel 1 111233; De huidige trans actie is afgebroken en alle openstaande wijzigingen zijn teruggedraaid. Oorzaak: een trans actie in een alleen-herstel status is niet expliciet teruggezet vóór een DDL-, DML-of SELECT-instructie.

U krijgt geen uitvoer van de ERROR_ *-functies.

In de toegewezen SQL-groep moet de code enigszins worden gewijzigd:

```sql
SET NOCOUNT ON;
DECLARE @xact_state smallint = 0;

BEGIN TRAN
    BEGIN TRY
        DECLARE @i INT;
        SET     @i = CONVERT(INT,'ABC');
    END TRY
    BEGIN CATCH
        SET @xact_state = XACT_STATE();

        IF @@TRANCOUNT > 0
        BEGIN
            ROLLBACK TRAN;
            PRINT 'ROLLBACK';
        END

        SELECT  ERROR_NUMBER()    AS ErrNumber
        ,       ERROR_SEVERITY()  AS ErrSeverity
        ,       ERROR_STATE()     AS ErrState
        ,       ERROR_PROCEDURE() AS ErrProcedure
        ,       ERROR_MESSAGE()   AS ErrMessage
        ;
    END CATCH;

IF @@TRANCOUNT >0
BEGIN
    PRINT 'COMMIT';
    COMMIT TRAN;
END

SELECT @xact_state AS TransactionState;
```

Het verwachte gedrag wordt nu in acht genomen. De fout in de trans actie wordt beheerd en de functies van de ERROR_ * bieden waarden zoals verwacht.

Alle wijzigingen die zijn gewijzigd, zijn dat het terugdraaien van de trans actie moet plaatsvinden voordat de fout gegevens in het blok CATCH worden gelezen.

## <a name="error_line-function"></a>Functie Error_Line ()

Het is ook een goed idee dat de functie ERROR_LINE () niet wordt geïmplementeerd of ondersteund door de toegewezen SQL-groep. Als u deze functie in uw code hebt, moet u deze verwijderen om te voldoen aan de toegewezen SQL-groep. Gebruik in plaats daarvan query labels in uw code om gelijkwaardige functionaliteit te implementeren. Zie het artikel [Label](develop-label.md) voor meer informatie.

## <a name="use-of-throw-and-raiserror"></a>Gebruik van THROW en///////

THROW is de meer moderne implementatie voor het verhogen van uitzonde ringen in een toegewezen SQL-pool, maar dit wordt ook wel ondersteund. Er zijn enkele verschillen die u moet betalen.

* Door de gebruiker gedefinieerde fout berichten getallen kunnen zich niet in het 100.000-150.000-bereik bevallen
* Fout berichten met de volgende strekking worden opgelost om 50.000
* Het gebruik van sys. messages wordt niet ondersteund

## <a name="limitations"></a>Beperkingen

De toegewezen SQL-groep heeft enkele andere beperkingen die betrekking hebben op trans acties. De verschillen zijn als volgt:

* Geen gedistribueerde trans acties
* Geen geneste trans acties toegestaan
* Geen opslag punten toegestaan
* Geen benoemde trans acties
* Geen gemarkeerde trans acties
* Geen ondersteuning voor DDL, zoals CREATE TABLE binnen een door de gebruiker gedefinieerde trans actie

## <a name="next-steps"></a>Volgende stappen

Zie [Aanbevolen procedures voor trans acties](../sql-data-warehouse/sql-data-warehouse-develop-best-practices-transactions.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)voor meer informatie over het optimaliseren van trans acties. Er zijn ook aanvullende best practices-hand leidingen beschikbaar voor [exclusieve SQL-groep](best-practices-dedicated-sql-pool.md) en [serverloze SQL-groep](best-practices-serverless-sql-pool.md).