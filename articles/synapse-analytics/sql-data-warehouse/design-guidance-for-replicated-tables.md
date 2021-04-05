---
title: Richt lijnen voor het ontwerpen van gerepliceerde tabellen
description: Aanbevelingen voor het ontwerpen van gerepliceerde tabellen in Synapse SQL-pool
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 03/19/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 7dcb884d8eafdfa5218e96d63f62a5d462d20cf8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98679927"
---
# <a name="design-guidance-for-using-replicated-tables-in-synapse-sql-pool"></a>Ontwerp richtlijnen voor het gebruik van gerepliceerde tabellen in de Synapse SQL-pool

In dit artikel worden aanbevelingen gedaan voor het ontwerpen van gerepliceerde tabellen in uw Synapse SQL pool-schema. Gebruik deze aanbevelingen om de query prestaties te verbeteren door de verplaatsing van gegevens en de complexiteit van query's te verminderen.

> [!VIDEO https://www.youtube.com/embed/1VS_F37GI9U]

## <a name="prerequisites"></a>Vereisten

In dit artikel wordt ervan uitgegaan dat u bekend bent met de concepten voor gegevens distributie en gegevens verplaatsing in de SQL-groep.  Zie het artikel over de [architectuur](massively-parallel-processing-mpp-architecture.md) voor meer informatie.

Als onderdeel van het tabel ontwerp begrijpt u zoveel mogelijk informatie over uw gegevens en de manier waarop de gegevens worden opgevraagd.  Denk bijvoorbeeld aan de volgende vragen:

- Hoe groot is de tabel?
- Hoe vaak is de tabel vernieuwd?
- Heb ik feiten-en dimensie tabellen in een SQL-groep?

## <a name="what-is-a-replicated-table"></a>Wat is een gerepliceerde tabel?

Een gerepliceerde tabel heeft een volledige kopie van de tabel die toegankelijk is op elk reken knooppunt. Als een tabel wordt gerepliceerd, hoeven de gegevens niet meer te worden overgedragen tussen rekenknooppunten voordat er sprake is van een samenvoeging of aggregatie. Omdat de tabel meerdere kopieën bevat, werken gerepliceerde tabellen het beste wanneer de tabel kleiner is dan 2 GB gecomprimeerd.  2 GB is geen vaste limiet.  Als de gegevens statisch zijn en niet wordt gewijzigd, kunt u grotere tabellen repliceren.

In het volgende diagram ziet u een gerepliceerde tabel die toegankelijk is op elk reken knooppunt. In de SQL-groep wordt de gerepliceerde tabel volledig gekopieerd naar een distributie database op elk reken knooppunt.

![Gerepliceerde tabel](./media/design-guidance-for-replicated-tables/replicated-table.png "Gerepliceerde tabel")  

Gerepliceerde tabellen werken goed voor dimensie tabellen in een ster schema. Dimensie tabellen worden meestal gekoppeld aan feiten tabellen die anders gedistribueerd zijn dan de dimensie tabel.  Dimensies zijn meestal een grootte waardoor het haalbaar is om meerdere kopieën op te slaan en te onderhouden. In dimensies worden beschrijvende gegevens opgeslagen die langzaam veranderen, zoals de naam en het adres van de klant en product gegevens. De langzaam veranderende aard van de gegevens leidt tot minder onderhoud van de gerepliceerde tabel.

Overweeg het gebruik van een gerepliceerde tabel wanneer:

- De tabel grootte op schijf is minder dan 2 GB, ongeacht het aantal rijen. Als u de grootte van een tabel wilt weten, kunt u de [DBCC PDW_SHOWSPACEUSED](/sql/t-sql/database-console-commands/dbcc-pdw-showspaceused-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) -opdracht gebruiken: `DBCC PDW_SHOWSPACEUSED('ReplTableCandidate')` .
- De tabel wordt gebruikt in samen voegingen waarvoor gegevens verplaatsing anders zou worden vereist. Bij het koppelen van tabellen die niet in dezelfde kolom worden gedistribueerd, zoals een hash-gedistribueerde tabel naar een Round-Robin tabel, is gegevens verplaatsing vereist om de query te volt ooien.  Als een van de tabellen klein is, overweeg dan een gerepliceerde tabel. In de meeste gevallen kunt u het beste gerepliceerde tabellen gebruiken in plaats van Round-Robin tabellen. Gebruik [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)om bewerkingen voor gegevens verplaatsing in query plannen weer te geven.  De BroadcastMoveOperation is de gebruikelijke bewerking voor het verplaatsen van gegevens die kan worden verwijderd met behulp van een gerepliceerde tabel.  

Gerepliceerde tabellen leveren mogelijk niet de beste query prestaties als:

- De tabel bevat regel matig insert-, update-en delete-bewerkingen. Voor de Data Manipulation Language (DML)-bewerkingen moet de gerepliceerde tabel opnieuw worden opgebouwd. Een regel matig gebouw kan leiden tot tragere prestaties.
- De SQL-pool wordt regel matig geschaald. Het schalen van een SQL-groep wijzigt het aantal reken knooppunten, waardoor de gerepliceerde tabel wordt opnieuw samengesteld.
- De tabel heeft een groot aantal kolommen, maar gegevens bewerkingen hebben doorgaans slechts een klein aantal kolommen. In dit scenario, in plaats van de hele tabel te repliceren, is het mogelijk effectiever om de tabel te distribueren en vervolgens een index te maken voor de veelgebruikte kolommen. Wanneer een query gegevens verplaatsing vereist, verplaatst de SQL-groep alleen gegevens voor de aangevraagde kolommen.

## <a name="use-replicated-tables-with-simple-query-predicates"></a>Gerepliceerde tabellen gebruiken met eenvoudige query predikaten

Voordat u ervoor kiest om een tabel te distribueren of te repliceren, moet u nadenken over de typen query's die u wilt uitvoeren op de tabel. Indien mogelijk,

- Gerepliceerde tabellen gebruiken voor query's met eenvoudige query predikaten, zoals gelijkheid of ongelijkheid.
- Gedistribueerde tabellen gebruiken voor query's met complexe query predikaten, zoals zoals of niet.

CPU-intensieve query's worden het beste uitgevoerd wanneer het werk wordt gedistribueerd op alle reken knooppunten. Query's die berekeningen uitvoeren op elke rij van een tabel, kunnen bijvoorbeeld beter worden uitgevoerd op gedistribueerde tabellen dan gerepliceerde tabellen. Aangezien een gerepliceerde tabel volledig op elk reken knooppunt wordt opgeslagen, wordt een CPU-intensieve query voor een gerepliceerde tabel uitgevoerd op basis van de volledige tabel op elk reken knooppunt. De extra berekening kan de prestaties van query's vertragen.

Deze query heeft bijvoorbeeld een complex predicaat.  Het wordt sneller uitgevoerd wanneer de gegevens zich in een gedistribueerde tabel bevinden in plaats van een gerepliceerde tabel. In dit voor beeld kunnen de gegevens Round-Robin worden gedistribueerd.

```sql

SELECT EnglishProductName
FROM DimProduct
WHERE EnglishDescription LIKE '%frame%comfortable%'

```

## <a name="convert-existing-round-robin-tables-to-replicated-tables"></a>Bestaande Round-Robin tabellen converteren naar gerepliceerde tabellen

Als u al Round-Robin tabellen hebt, raden we u aan om deze te converteren naar gerepliceerde tabellen als ze voldoen aan de criteria die in dit artikel worden beschreven. Gerepliceerde tabellen verbeteren de prestaties ten opzichte van Round-Robin tabellen, omdat de gegevens verplaatsing overbodig is.  Een Round-Robin tabel vereist altijd gegevens verplaatsing voor samen voegingen.

In dit voor beeld wordt [CTAS](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) gebruikt om de tabel tabel dimsalesterritory te wijzigen in een gerepliceerde tabel. Dit voor beeld werkt ongeacht of tabel dimsalesterritory hash-Distributed of Round-Robin is.

```sql
CREATE TABLE [dbo].[DimSalesTerritory_REPLICATE]
WITH
  (
    CLUSTERED COLUMNSTORE INDEX,  
    DISTRIBUTION = REPLICATE  
  )  
AS SELECT * FROM [dbo].[DimSalesTerritory]
OPTION  (LABEL  = 'CTAS : DimSalesTerritory_REPLICATE')

-- Switch table names
RENAME OBJECT [dbo].[DimSalesTerritory] to [DimSalesTerritory_old];
RENAME OBJECT [dbo].[DimSalesTerritory_REPLICATE] TO [DimSalesTerritory];

DROP TABLE [dbo].[DimSalesTerritory_old];
```

### <a name="query-performance-example-for-round-robin-versus-replicated"></a>Voor beeld van query prestaties voor Round Robin versus gerepliceerd

Voor een gerepliceerde tabel is geen verplaatsing van gegevens vereist voor samen voegingen omdat de volledige tabel al aanwezig is op elk reken knooppunt. Als de dimensie tabellen Round-Robin gedistribueerd zijn, kopieert een koppeling de dimensie tabel volledig naar elk reken knooppunt. Als u de gegevens wilt verplaatsen, bevat het query plan een bewerking met de naam BroadcastMoveOperation. Dit type verplaatsings bewerking voor gegevens vertraagt de query prestaties en wordt geëlimineerd door het gebruik van gerepliceerde tabellen. Als u de stappen in het query plan wilt weer geven, gebruikt u de weer gave [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) systeem catalogus.  

In de volgende query op basis van het AdventureWorks-schema is de tabel bijvoorbeeld een `FactInternetSales` hash-distributie. De `DimDate` `DimSalesTerritory` tabellen en zijn kleinere dimensie tabellen. Met deze query wordt de totale omzet in Noord-Amerika voor boek jaar 2004 geretourneerd:

```sql
SELECT [TotalSalesAmount] = SUM(SalesAmount)
FROM dbo.FactInternetSales s
INNER JOIN dbo.DimDate d
  ON d.DateKey = s.OrderDateKey
INNER JOIN dbo.DimSalesTerritory t
  ON t.SalesTerritoryKey = s.SalesTerritoryKey
WHERE d.FiscalYear = 2004
  AND t.SalesTerritoryGroup = 'North America'
```

Er worden nieuwe `DimDate` en `DimSalesTerritory` afgeronde Robin tabellen gemaakt. Als gevolg hiervan heeft de query het volgende query plan getoond, dat meerdere bewerkingen voor het verplaatsen van de broadcast heeft:

![Round Robin-query plan](./media/design-guidance-for-replicated-tables/round-robin-tables-query-plan.jpg)

We maken `DimDate` en `DimSalesTerritory` als gerepliceerde tabellen opnieuw en voer de query opnieuw uit. Het resulterende query plan is veel korter en er wordt geen uitzending verplaatst.

![Gerepliceerd query plan](./media/design-guidance-for-replicated-tables/replicated-tables-query-plan.jpg)

## <a name="performance-considerations-for-modifying-replicated-tables"></a>Prestatie overwegingen voor het wijzigen van gerepliceerde tabellen

De SQL-Groep implementeert een gerepliceerde tabel door een hoofd versie van de tabel te onderhouden. Hiermee wordt de hoofd versie gekopieerd naar de eerste distributie database op elk reken knooppunt. Wanneer er een wijziging is, wordt de hoofd versie eerst bijgewerkt, daarna worden de tabellen op elk reken knooppunt opnieuw opgebouwd. Het opnieuw opbouwen van een gerepliceerde tabel omvat het kopiëren van de tabel naar elk reken knooppunt en vervolgens het samen stellen van de indexen.  Bijvoorbeeld, een gerepliceerde tabel op een DW2000c heeft 5 kopieën van de gegevens.  Een hoofd kopie en een volledige kopie op elk reken knooppunt.  Alle gegevens worden opgeslagen in distributie databases. De SQL-groep gebruikt dit model om snellere instructies voor het wijzigen van gegevens en flexibele schaal bewerkingen te ondersteunen.

Asynchrone opnieuw opgebouwden worden door de eerste query geactiveerd op basis van de gerepliceerde tabel na:

- Gegevens worden geladen of gewijzigd
- Het Synapse SQL-exemplaar is geschaald naar een ander niveau
- De tabel definitie is bijgewerkt

Opnieuw opbouwen is niet vereist na:

- Bewerking onderbreken
- Bewerking hervatten

Het opnieuw opbouwen gebeurt niet onmiddellijk nadat de gegevens zijn gewijzigd. In plaats daarvan wordt het opnieuw opbouwen geactiveerd wanneer een query voor het eerst wordt geselecteerd in de tabel.  De query die de opnieuw opgebouwde Lees bewerkingen heeft geactiveerd, wordt direct vanuit de hoofd versie van de tabel uitgevoerd terwijl de gegevens asynchroon naar elk reken knooppunt worden gekopieerd. De volgende query's blijven de hoofd versie van de tabel gebruiken totdat de gegevens kopie is voltooid.  Als er activiteiten worden uitgevoerd op basis van de gerepliceerde tabel die opnieuw moet worden gebouwd, wordt de gegevens kopie ongeldig gemaakt en worden de volgende SELECT-instructies de gegevens geactiveerd die opnieuw moeten worden gekopieerd.

### <a name="use-indexes-conservatively"></a>Indexen conservatief gebruiken

Standaard indexerings procedures zijn van toepassing op gerepliceerde tabellen. De SQL-groep bouwt elke gerepliceerde tabel index opnieuw op als onderdeel van het opnieuw opbouwen. Gebruik alleen indexen wanneer de prestatie verbetering de kosten van het opnieuw samen stellen van de indexen uitweegt.

### <a name="batch-data-load"></a>Batch gegevens laden

Bij het laden van gegevens in gerepliceerde tabellen, probeert u het opnieuw opgebouwden te minimaliseren door batch taken samen te laden. Voer alle belastingen in de batch uit voordat u SELECT-instructies uitvoert.

Met dit laad patroon worden bijvoorbeeld gegevens van vier bronnen geladen en vier opnieuw opgebouwd.

- Laden vanaf bron 1.
- Selecteer instructie triggers opnieuw samen stellen 1.
- Laden vanaf bron 2.
- Selecteer de instructie triggers opnieuw samen stellen 2.
- Laden vanaf bron 3.
- Selecteer de instructie triggers opnieuw samen te stellen 3.
- Laden vanaf bron 4.
- Selecteer de instructie triggers opnieuw bouwen 4.

Met dit laad patroon worden bijvoorbeeld gegevens van vier bronnen geladen, maar wordt slechts één opnieuw opgebouwd.

- Laden vanaf bron 1.
- Laden vanaf bron 2.
- Laden vanaf bron 3.
- Laden vanaf bron 4.
- Selecteer de instructie triggers opnieuw samen te stellen.

### <a name="rebuild-a-replicated-table-after-a-batch-load"></a>Een gerepliceerde tabel opnieuw samen stellen na het laden van een batch

Om ervoor te zorgen dat er consistente query's worden uitgevoerd, kunt u overwegen de build van de gerepliceerde tabellen te forceren na het laden van een batch. Anders zal de eerste query nog steeds gegevens verplaatsing gebruiken om de query te volt ooien.

Deze query gebruikt de [sys.pdw_replicated_table_cache_state](/sql/relational-databases/system-catalog-views/sys-pdw-replicated-table-cache-state-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) dmv om de gerepliceerde tabellen weer te geven die zijn gewijzigd, maar niet opnieuw opgebouwd.

```sql
SELECT [ReplicatedTable] = t.[name]
  FROM sys.tables t  
  JOIN sys.pdw_replicated_table_cache_state c  
    ON c.object_id = t.object_id
  JOIN sys.pdw_table_distribution_properties p
    ON p.object_id = t.object_id
  WHERE c.[state] = 'NotReady'
    AND p.[distribution_policy_desc] = 'REPLICATE'
```

Voer de volgende instructie uit voor elke tabel in de voor gaande uitvoer om een Rebuild te activeren.

```sql
SELECT TOP 1 * FROM [ReplicatedTable]
```

## <a name="next-steps"></a>Volgende stappen

Als u een gerepliceerde tabel wilt maken, gebruikt u een van de volgende instructies:

- [CREATE TABLE (SQL-groep)](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [CREATE TABLE als selecteren (SQL-groep)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)

Zie [gedistribueerde tabellen](sql-data-warehouse-tables-distribute.md)voor een overzicht van gedistribueerde tabellen.
