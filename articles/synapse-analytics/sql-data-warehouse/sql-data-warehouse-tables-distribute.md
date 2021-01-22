---
title: Richt lijnen voor het ontwerpen van gedistribueerde tabellen
description: Aanbevelingen voor het ontwerpen van op hash gedistribueerde en Round Robin gedistribueerde tabellen met een exclusieve SQL-groep in azure Synapse Analytics.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 5aefe869041d9fff8112b6aa380961ca6568ae0b
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98673566"
---
# <a name="guidance-for-designing-distributed-tables-using-dedicated-sql-pool-in-azure-synapse-analytics"></a>Richt lijnen voor het ontwerpen van gedistribueerde tabellen met een toegewezen SQL-groep in azure Synapse Analytics

Aanbevelingen voor het ontwerpen van op hash gedistribueerde en Round Robin gedistribueerde tabellen in toegewezen SQL-groepen.

In dit artikel wordt ervan uitgegaan dat u bekend bent met de concepten voor gegevens distributie en gegevens verplaatsing in de toegewezen SQL-groep.  Zie [Azure Synapse Analytics-architectuur](massively-parallel-processing-mpp-architecture.md)voor meer informatie.

## <a name="what-is-a-distributed-table"></a>Wat is een gedistribueerde tabel?

Een gedistribueerde tabel wordt weer gegeven als één tabel, maar de rijen worden feitelijk opgeslagen in 60 distributies. De rijen worden gedistribueerd met een hash-of Round Robin-algoritme.  

**Hash-gedistribueerde tabellen** verbeteren de query prestaties in grote feiten tabellen en vormen de focus van dit artikel. **Round-Robin tabellen** zijn handig voor het verbeteren van de laad snelheid. Deze ontwerp opties hebben een aanzienlijke invloed op het verbeteren van query's en het laden van prestaties.

Een andere table-opslag optie is het repliceren van een kleine tabel over alle reken knooppunten. Zie [ontwerp richtlijnen voor gerepliceerde tabellen](design-guidance-for-replicated-tables.md)voor meer informatie. Zie gedistribueerde tabellen in het [overzicht tabellen](sql-data-warehouse-tables-overview.md)om snel te kiezen uit de drie opties.

Als onderdeel van het tabel ontwerp begrijpt u zoveel mogelijk informatie over uw gegevens en de manier waarop de gegevens worden opgevraagd.  Denk bijvoorbeeld aan de volgende vragen:

- Hoe groot is de tabel?
- Hoe vaak is de tabel vernieuwd?
- Heb ik feiten-en dimensie tabellen in een toegewezen SQL-groep?

### <a name="hash-distributed"></a>Hash gedistribueerd

Een hash-gedistribueerde tabel distribueert tabel rijen over de reken knooppunten met behulp van een deterministische hash-functie voor het toewijzen van elke rij aan één [distributie](massively-parallel-processing-mpp-architecture.md#distributions).

![Gedistribueerde tabel](./media/sql-data-warehouse-tables-distribute/hash-distributed-table.png "Gedistribueerde tabel")  

Aangezien identieke waarden altijd een hash naar dezelfde distributie hebben, heeft SQL Analytics ingebouwde kennis van de maplocaties van de rij. In de toegewezen SQL-groep wordt deze kennis gebruikt om de verplaatsing van gegevens tijdens query's te minimaliseren, waardoor de query prestaties worden verbeterd.

Hash-gedistribueerde tabellen werken goed voor grote feiten tabellen in een ster schema. Ze kunnen zeer grote aantallen rijen hebben en toch hoge prestaties bezorgen. Er zijn uiteraard enkele ontwerp overwegingen waarmee u de prestaties van het gedistribueerde systeem kunt bepalen. Het kiezen van een goede distributie kolom is een van deze overwegingen die in dit artikel wordt beschreven.

Overweeg het gebruik van een hash-gedistribueerde tabel wanneer:

- De tabel grootte op schijf is groter dan 2 GB.
- De tabel bevat regel matig insert-, update-en delete-bewerkingen.

### <a name="round-robin-distributed"></a>Round-Robin gedistribueerd

Een met Round Robin gedistribueerde tabel verdeelt tabel rijen gelijkmatig over alle distributies. De toewijzing van rijen aan distributies is wille keurig. In tegens telling tot hash-gedistribueerde tabellen, worden rijen met gelijke waarden niet gegarandeerd toegewezen aan dezelfde distributie.

Als gevolg hiervan moet het systeem soms een bewerking voor gegevens verplaatsing aanroepen om uw gegevens beter te organiseren voordat een query kan worden omgezet.  Deze extra stap kan uw query's vertragen. Als u bijvoorbeeld een Round-Robin tabel samenvoegt, moeten de rijen worden gevolgd. Dit is een prestatie treffer.

Overweeg het gebruik van round-robin distributie voor uw tabel in de volgende scenario's:

- Als u aan de slag gaat als eenvoudig begin punt omdat dit de standaard waarde is
- Als er geen duidelijke samenvoegings sleutel is
- Als er geen geschikte kandidaten kolom is voor de hash die de tabel distribueert
- Als de tabel geen algemene koppelings sleutel met andere tabellen deelt
- Als de samen voeging minder significant is dan andere samen voegingen in de query
- Wanneer de tabel een tijdelijke faserings tabel is

De zelf studie [over taxi's gegevens van New York laden](./load-data-from-azure-blob-storage-using-copy.md#load-the-data-into-your-data-warehouse) biedt een voor beeld van het laden van gegevens in een faserings tabel met Round Robin.

## <a name="choosing-a-distribution-column"></a>Een distributie kolom kiezen

Een hash-gedistribueerde tabel heeft een distributie kolom die de hash-sleutel is. Met de volgende code wordt bijvoorbeeld een met een hash gedistribueerde tabel gemaakt met ProductKey als de kolom distributie.

```sql
CREATE TABLE [dbo].[FactInternetSales]
(   [ProductKey]            int          NOT NULL
,   [OrderDateKey]          int          NOT NULL
,   [CustomerKey]           int          NOT NULL
,   [PromotionKey]          int          NOT NULL
,   [SalesOrderNumber]      nvarchar(20) NOT NULL
,   [OrderQuantity]         smallint     NOT NULL
,   [UnitPrice]             money        NOT NULL
,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,  DISTRIBUTION = HASH([ProductKey])
)
;
```

Gegevens die zijn opgeslagen in de kolom distributie kunnen worden bijgewerkt. Het bijwerken van gegevens in de kolom distributie kan resulteren in een bewerking voor gegevens in wille keurige volg orde.

Het kiezen van een distributie kolom is een belang rijke ontwerp beslissing omdat de waarden in deze kolom bepalen hoe de rijen worden gedistribueerd. De beste keuze is afhankelijk van verschillende factoren en is doorgaans van compromissen. Zodra een distributie kolom is gekozen, kunt u deze niet meer wijzigen.  

Als u de beste kolom de eerste keer niet hebt gekozen, kunt u [Create Table als Select (CTAS)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) gebruiken om de tabel opnieuw te maken met een andere distributie kolom.

### <a name="choose-a-distribution-column-with-data-that-distributes-evenly"></a>Een distributie kolom kiezen met gegevens die gelijkmatig worden gedistribueerd

Voor de beste prestaties moeten alle distributies ongeveer hetzelfde aantal rijen bevatten. Wanneer een of meer distributies een niet-proportioneel aantal rijen hebben, volt ooien sommige distributies het gedeelte van een parallelle query vóór andere. Omdat de query niet kan worden voltooid totdat alle distributies zijn verwerkt, is elke query alleen net zo snel als de langzaamste distributie.

- Gegevens scheefheid betekent dat de gegevens niet gelijkmatig worden verdeeld over de distributies
- Verwerkings scheefheid betekent dat sommige distributies langer duren dan andere, wanneer parallelle query's worden uitgevoerd. Dit kan gebeuren wanneer de gegevens worden schuingetrokken.
  
Als u de parallelle verwerking wilt verdelen, selecteert u een distributie kolom die:

- **Heeft veel unieke waarden.** De kolom kan enkele dubbele waarden bevatten. Alle rijen met dezelfde waarde worden echter toegewezen aan dezelfde verdeling. Omdat er 60 distributies zijn, moet de kolom ten minste 60 unieke waarden bevatten.  Meestal is het aantal unieke waarden veel meer.
- **Heeft geen NULL-waarden of heeft slechts enkele NULL-waarden.** Voor een uitzonderlijk voor beeld geldt dat als alle waarden in de kolom NULL zijn, alle rijen worden toegewezen aan dezelfde verdeling. Als gevolg hiervan wordt de verwerking van query's vervormd tot één distributie en wordt niet geprofiteerd van parallelle verwerking.
- **Is geen datum kolom**. Alle gegevens voor hetzelfde datum land in dezelfde distributie. Als meerdere gebruikers allemaal worden gefilterd op dezelfde datum, wordt de verwerking van slechts 1 van de 60-distributies uitgevoerd.

### <a name="choose-a-distribution-column-that-minimizes-data-movement"></a>Kies een distributie kolom die de verplaatsing van gegevens minimaliseert

Om de juiste query resultaat query's te verkrijgen, kunnen gegevens van het ene reken knooppunt naar het andere worden verplaatst. Gegevens verplaatsing gebeurt meestal wanneer query's samen voegingen en aggregaties hebben op gedistribueerde tabellen. Het kiezen van een distributie kolom die helpt bij het minimaliseren van gegevens verplaatsing is een van de belangrijkste strategieën voor het optimaliseren van de prestaties van uw toegewezen SQL-groep.

Als u de verplaatsing van gegevens wilt minimaliseren, selecteert u een distributie kolom die:

- Wordt gebruikt in `JOIN` , `GROUP BY` , `DISTINCT` , `OVER` en- `HAVING` componenten. Wanneer twee grote feiten tabellen veelvuldig samen voegen, worden de query prestaties verbeterd wanneer u beide tabellen op een van de samenvoegings kolommen distribueert.  Wanneer een tabel niet wordt gebruikt in samen voegingen, kunt u overwegen om de tabel te distribueren in een kolom die regel matig voor komt in de `GROUP BY` component.
- Wordt *niet* gebruikt in- `WHERE` componenten. Hierdoor kan de query worden beperkt zodat deze niet op alle distributies kan worden uitgevoerd.
- Is *geen* datum kolom. WHERE-componenten worden vaak op datum gefilterd.  Als dit gebeurt, kan alle verwerking alleen op enkele distributies worden uitgevoerd.

### <a name="what-to-do-when-none-of-the-columns-are-a-good-distribution-column"></a>Wat te doen wanneer geen van de kolommen een goede distributie kolom is

Als geen van uw kolommen voldoende unieke waarden voor een distributie kolom heeft, kunt u een nieuwe kolom maken als een combi natie van een of meer waarden. Als u gegevens verplaatsing tijdens het uitvoeren van query's wilt voor komen, gebruikt u de kolom samengestelde distributie als een samenvoegings kolom in query's.

Wanneer u een hash-gedistribueerde tabel ontwerpt, is de volgende stap het laden van gegevens in de tabel.  Zie [overzicht van het laden](design-elt-data-loading.md)voor meer informatie.

## <a name="how-to-tell-if-your-distribution-column-is-a-good-choice"></a>Bepalen of uw distributie kolom een goede keuze is

Nadat de gegevens in een hash-gedistribueerde tabel zijn geladen, controleert u of de rijen gelijkmatig zijn verdeeld over de 60-distributies. De rijen per distributie kunnen variëren tot 10% zonder merkbaar effect op de prestaties.

### <a name="determine-if-the-table-has-data-skew"></a>Bepalen of het scheef trekken van gegevens in de tabel is

Een snelle manier om te controleren op gegevens scheefheid is door [DBCC PDW_SHOWSPACEUSED](/sql/t-sql/database-console-commands/dbcc-pdw-showspaceused-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)te gebruiken. De volgende SQL-code retourneert het aantal tabel rijen dat is opgeslagen in elk van de 60-distributies. Voor evenwichtige prestaties moeten de rijen in de gedistribueerde tabel gelijkmatig worden verdeeld over alle distributies.

```sql
-- Find data skew for a distributed table
DBCC PDW_SHOWSPACEUSED('dbo.FactInternetSales');
```

Om te bepalen welke tabellen meer dan 10% gegevens scheef trekken:

1. Maak de weer gave dbo. vTableSizes die wordt weer gegeven in het artikel [overzicht van tabellen](sql-data-warehouse-tables-overview.md#table-size-queries) .  
2. Voer de volgende query uit.

```sql
select *
from dbo.vTableSizes
where two_part_name in
    (
    select two_part_name
    from dbo.vTableSizes
    where row_count > 0
    group by two_part_name
    having (max(row_count * 1.000) - min(row_count * 1.000))/max(row_count * 1.000) >= .10
    )
order by two_part_name, row_count
;
```

### <a name="check-query-plans-for-data-movement"></a>Query plannen controleren op gegevens verplaatsing

Met een goede distributie kolom kunnen samen voegingen en aggregaties minimale gegevens verplaatsing hebben. Dit is van invloed op de manier waarop samen voegingen moeten worden geschreven. Een van de samenvoegings kolommen moet de kolom distributie zijn om minimale gegevens verplaatsing te krijgen voor een koppeling op twee hash-gedistribueerde tabellen.  Wanneer twee hash-gedistribueerde tabellen worden toegevoegd aan een distributie kolom van hetzelfde gegevens type, is voor de koppeling geen gegevens verplaatsing vereist. Samen voegingen kunnen extra kolommen gebruiken zonder dat gegevens worden verplaatst.

Gegevens verplaatsing tijdens een koppeling voor komen:

- De tabellen die deel uitmaken van de koppeling, moeten worden gedistribueerd naar hash op **een** van de kolommen die deel nemen aan de koppeling.
- De gegevens typen van de samenvoegings kolommen moeten tussen beide tabellen overeenkomen.
- De kolommen moeten worden gekoppeld aan een operator equals.
- Het samenvoegings type mag geen zijn `CROSS JOIN` .

Als u wilt zien of er query's zijn die gegevens verplaatsen, kunt u het query plan bekijken.  

## <a name="resolve-a-distribution-column-problem"></a>Een probleem met een distributie kolom oplossen

Het is niet nodig om alle gevallen van het hellen van gegevens op te lossen. Het distribueren van gegevens is een kwestie van het vinden van de juiste balans tussen het minimaliseren van gegevens scheefheid en het verplaatsen van gegevens. Het is niet altijd mogelijk om zowel het hellen als de verplaatsing van gegevens te minimaliseren. Soms is het voor deel dat de minimale verplaatsing van gegevens het gevolg kan hebben van de impact van het Verhellen van gegevens.

Als u wilt beslissen of gegevens scheefheid in een tabel moet worden opgelost, moet u zoveel mogelijk inzicht krijgen in de gegevens volumes en query's in uw werk belasting. U kunt de stappen in het [query controle](sql-data-warehouse-manage-monitor.md) -artikel gebruiken om de gevolgen van het hellen van de query prestaties te bewaken. Zoek in het bijzonder hoe lang het duurt om grote query's uit te voeren op afzonderlijke distributies.

Omdat u de kolom distributie op een bestaande tabel niet kunt wijzigen, kunt u gegevens scheefheid het beste op de gebruikelijke manier oplossen door de tabel opnieuw te maken met een andere distributie kolom.  

### <a name="re-create-the-table-with-a-new-distribution-column"></a>De tabel opnieuw maken met een nieuwe distributie kolom

In dit voor beeld wordt [Create Table als selecteren](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) om een tabel met een andere hash-distributie kolom opnieuw te maken.

```sql
CREATE TABLE [dbo].[FactInternetSales_CustomerKey]
WITH (  CLUSTERED COLUMNSTORE INDEX
     ,  DISTRIBUTION =  HASH([CustomerKey])
     ,  PARTITION       ( [OrderDateKey] RANGE RIGHT FOR VALUES (   20000101, 20010101, 20020101, 20030101
                                                                ,   20040101, 20050101, 20060101, 20070101
                                                                ,   20080101, 20090101, 20100101, 20110101
                                                                ,   20120101, 20130101, 20140101, 20150101
                                                                ,   20160101, 20170101, 20180101, 20190101
                                                                ,   20200101, 20210101, 20220101, 20230101
                                                                ,   20240101, 20250101, 20260101, 20270101
                                                                ,   20280101, 20290101
                                                                )
                        )
    )
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
OPTION  (LABEL  = 'CTAS : FactInternetSales_CustomerKey')
;

--Create statistics on new table
CREATE STATISTICS [ProductKey] ON [FactInternetSales_CustomerKey] ([ProductKey]);
CREATE STATISTICS [OrderDateKey] ON [FactInternetSales_CustomerKey] ([OrderDateKey]);
CREATE STATISTICS [CustomerKey] ON [FactInternetSales_CustomerKey] ([CustomerKey]);
CREATE STATISTICS [PromotionKey] ON [FactInternetSales_CustomerKey] ([PromotionKey]);
CREATE STATISTICS [SalesOrderNumber] ON [FactInternetSales_CustomerKey] ([SalesOrderNumber]);
CREATE STATISTICS [OrderQuantity] ON [FactInternetSales_CustomerKey] ([OrderQuantity]);
CREATE STATISTICS [UnitPrice] ON [FactInternetSales_CustomerKey] ([UnitPrice]);
CREATE STATISTICS [SalesAmount] ON [FactInternetSales_CustomerKey] ([SalesAmount]);

--Rename the tables
RENAME OBJECT [dbo].[FactInternetSales] TO [FactInternetSales_ProductKey];
RENAME OBJECT [dbo].[FactInternetSales_CustomerKey] TO [FactInternetSales];
```

## <a name="next-steps"></a>Volgende stappen

Als u een gedistribueerde tabel wilt maken, gebruikt u een van de volgende instructies:

- [CREATE TABLE (toegewezen SQL-groep)](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [CREATE TABLE als selecteren (toegewezen SQL-groep)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)