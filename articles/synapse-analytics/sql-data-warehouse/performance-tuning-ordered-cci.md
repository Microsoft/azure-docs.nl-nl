---
title: Prestaties afstemmen met geordende en geclusterde columnstore-index
description: Aanbevelingen en overwegingen die u moet kennen wanneer u een geordende geclusterde columnstore-index gebruikt om uw queryprestaties in toegewezen SQL-pools te verbeteren.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/13/2021
ms.author: xiaoyul
ms.reviewer: nibruno; jrasnick
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: ab94a83a64ca9770f0c216ddf42145b262629c6d
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/18/2021
ms.locfileid: "107598989"
---
# <a name="performance-tuning-with-ordered-clustered-columnstore-index"></a>Prestaties afstemmen met geordende en geclusterde columnstore-index  

Wanneer gebruikers een query uitvoeren op een columnstore-tabel in een toegewezen SQL-pool, controleert de optimizer de minimum- en maximumwaarden die in elk segment zijn opgeslagen.  Segmenten die buiten de grenzen van het querypredicaat liggen, worden niet van schijf naar geheugen gelezen.  Een query kan snellere prestaties krijgen als het aantal segmenten dat moet worden gelezen en de totale grootte klein is.   

## <a name="ordered-vs-non-ordered-clustered-columnstore-index"></a>Geordende versus niet-geordende geclusterde columnstore-index

Standaard maakt een intern onderdeel (opbouwer van indexen) voor elke tabel die is gemaakt zonder indexoptie een niet-geordende geclusterde columnstore-index (CCI).  Gegevens in elke kolom worden gecomprimeerd in een afzonderlijk CCI-rijgroepsegment.  Er zijn metagegevens in het waardebereik van elk segment, zodat segmenten die buiten de grenzen van het querypredicaat liggen, niet van de schijf worden gelezen tijdens het uitvoeren van de query.  CCI biedt het hoogste niveau van gegevenscompressie en vermindert de grootte van segmenten die moeten worden gelezen, zodat query's sneller kunnen worden uitgevoerd. Omdat de opbouwer van de index echter geen gegevens sorteert voordat deze in segmenten worden gecomprimeerd, kunnen segmenten met overlappende waardebereiken optreden, waardoor query's meer segmenten van schijf kunnen lezen en het langer duurt om te voltooien.  

Bij het maken van een geordende CCI sorteert de toegewezen SQL-poolen engine de bestaande gegevens in het geheugen op volgordesleutels voordat de indexbouwer deze in indexsegmenten comprimeert.  Met gesorteerde gegevens wordt segment overlap verminderd, waardoor query's efficiënter segmenten kunnen verwijderen en dus snellere prestaties kunnen leveren omdat het aantal segmenten dat van de schijf moet worden gelezen, kleiner is.  Als alle gegevens in één keer in het geheugen kunnen worden gesorteerd, kan segment overlapping worden vermeden.  Vanwege grote tabellen in datawarehouses gebeurt dit scenario niet vaak.  

Voer de volgende opdracht uit met uw tabelnaam en kolomnaam om de segmentbereiken voor een kolom te controleren:

```sql
SELECT o.name, pnp.index_id, 
cls.row_count, pnp.data_compression_desc, 
pnp.pdw_node_id, pnp.distribution_id, cls.segment_id, 
cls.column_id, 
cls.min_data_id, cls.max_data_id, 
cls.max_data_id-cls.min_data_id as difference
FROM sys.pdw_nodes_partitions AS pnp
   JOIN sys.pdw_nodes_tables AS Ntables ON pnp.object_id = NTables.object_id AND pnp.pdw_node_id = NTables.pdw_node_id
   JOIN sys.pdw_table_mappings AS Tmap  ON NTables.name = TMap.physical_name AND substring(TMap.physical_name,40, 10) = pnp.distribution_id
   JOIN sys.objects AS o ON TMap.object_id = o.object_id
   JOIN sys.pdw_nodes_column_store_segments AS cls ON pnp.partition_id = cls.partition_id AND pnp.distribution_id  = cls.distribution_id
JOIN sys.columns as cols ON o.object_id = cols.object_id AND cls.column_id = cols.column_id
WHERE o.name = '<Table Name>' and cols.name = '<Column Name>'  and TMap.physical_name  not like '%HdTable%'
ORDER BY o.name, pnp.distribution_id, cls.min_data_id;


```

> [!NOTE] 
> In een geordende CCI-tabel worden de nieuwe gegevens die het resultaat zijn van dezelfde batch met DML- of laadbewerkingen binnen die batch gesorteerd. Er is geen globale sortering voor alle gegevens in de tabel.  Gebruikers kunnen de geordende CCI opnieuw opbouwen om alle gegevens in de tabel te sorteren.  In toegewezen SQL-pool is de columnstore-index REBUILD een offlinebewerking.  Voor een gepartitiesteerde tabel wordt REBUILD met één partitie tegelijk uitgevoerd.  Gegevens in de partitie die opnieuw wordt opgebouwd, zijn offline en niet beschikbaar totdat de REBUILD voor die partitie is voltooid. 

## <a name="query-performance"></a>Queryprestaties

De prestatieverbetering van een query met een geordende CCI is afhankelijk van de querypatronen, de grootte van de gegevens, hoe goed de gegevens worden gesorteerd, de fysieke structuur van segmenten en de DWU en resourceklasse die zijn gekozen voor de uitvoering van de query.  Gebruikers moeten al deze factoren controleren voordat ze de volgordekolommen kiezen bij het ontwerpen van een geordende CCI-tabel.

Query's met al deze patronen worden doorgaans sneller uitgevoerd met geordende CCI.  
1. De query's hebben gelijkheids-, ongelijkheids- of bereikpredicaten
1. De predicaatkolommen en de geordende CCI-kolommen zijn hetzelfde.  
1. De predicaatkolommen worden in dezelfde volgorde gebruikt als de kolom ordinale van geordende CCI-kolommen.  
 
In dit voorbeeld heeft tabel T1 een geclusterde columnstore-index, geordend in de volgorde van Col_C, Col_B en Col_A.

```sql

CREATE CLUSTERED COLUMNSTORE INDEX MyOrderedCCI ON  T1
ORDER (Col_C, Col_B, Col_A);

```

De prestaties van query 1 kunnen beter profiteren van geordende CCI dan de andere drie query's. 

```sql
-- Query #1: 

SELECT * FROM T1 WHERE Col_C = 'c' AND Col_B = 'b' AND Col_A = 'a';

-- Query #2

SELECT * FROM T1 WHERE Col_B = 'b' AND Col_C = 'c' AND Col_A = 'a';

-- Query #3
SELECT * FROM T1 WHERE Col_B = 'b' AND Col_A = 'a';

-- Query #4
SELECT * FROM T1 WHERE Col_A = 'a' AND Col_C = 'c';

```

## <a name="data-loading-performance"></a>Prestaties bij het laden van gegevens

De prestaties van het laden van gegevens in een geordende CCI-tabel zijn vergelijkbaar met een gepartitiesteerde tabel.  Het laden van gegevens in een geordende CCI-tabel kan langer duren dan een niet-geordende CCI-tabel vanwege de gegevenssorteerbewerking, maar query's kunnen later sneller worden uitgevoerd met geordende CCI.  

Hier is een voorbeeld van een prestatievergelijking van het laden van gegevens in tabellen met verschillende schema's.

![Staafdiagram met de prestatievergelijking van het laden van gegevens in tabellen met verschillende schema's.](./media/performance-tuning-ordered-cci/cci-data-loading-performance.png)


Hier volgen een voorbeeld van een vergelijking van queryprestaties tussen CCI en geordende CCI.

![Performance_comparison_data_loading](./media/performance-tuning-ordered-cci/occi_query_performance.png)

 
## <a name="reduce-segment-overlapping"></a>Segment overlapping verminderen

Het aantal overlappende segmenten is afhankelijk van de grootte van de te sorteren gegevens, het beschikbare geheugen en de maximale mate van parallelle ontwikkeling (MAXDOP) tijdens het maken van de geordende CCI. Hieronder vindt u opties voor het verminderen van segment overlapping bij het maken van geordende CCI.

- Gebruik de resourceklasse xlargerc op een hogere DWU om meer geheugen toe te staan voor het sorteren van gegevens voordat de indexbouwer de gegevens in segmenten comprimeert.  Eenmaal in een indexsegment kan de fysieke locatie van de gegevens niet worden gewijzigd.  Er is geen gegevensssorteering binnen een segment of tussen segmenten.  

- Maak een geordende CCI met MAXDOP = 1.  Elke thread die wordt gebruikt voor het maken van een geordende CCI, werkt op een subset van gegevens en sorteert deze lokaal.  Er is geen globale sortering voor gegevens gesorteerd op verschillende threads.  Het gebruik van parallelle threads kan de tijd voor het maken van een geordende CCI verminderen, maar genereert meer overlappende segmenten dan het gebruik van één thread.  Momenteel wordt de optie MAXDOP alleen ondersteund bij het maken van een geordende CCI-tabel met behulp CREATE TABLE AS SELECT-opdracht.  Het maken van een geordende CCI via CREATE INDEX of CREATE TABLE-opdrachten biedt geen ondersteuning voor de MAXDOP-optie. Bijvoorbeeld:

```sql
CREATE TABLE Table1 WITH (DISTRIBUTION = HASH(c1), CLUSTERED COLUMNSTORE INDEX ORDER(c1) )
AS SELECT * FROM ExampleTable
OPTION (MAXDOP 1);
```

- Sorteer de gegevens vooraf op de sorteersleutel(s) voordat u ze in tabellen laadt.

Hier volgt een voorbeeld van een geordende CCI-tabeldistributie met nul segmenten die overlappen volgens de bovenstaande aanbevelingen. De geordende CCI-tabel wordt gemaakt in een DWU1000c-database via CTAS vanuit een heaptabel van 20 GB met MAXDOP 1 en xlargerc.  De CCI wordt zonder dubbele waarden geordend op een BIGINT-kolom.  

![Segment_No_Overlapping](./media/performance-tuning-ordered-cci/perfect-sorting-example.png)

## <a name="create-ordered-cci-on-large-tables"></a>Geordende CCI maken voor grote tabellen

Het maken van een geordende CCI is een offlinebewerking.  Voor tabellen zonder partities zijn de gegevens pas toegankelijk voor gebruikers als het geordende CCI-aanmaakproces is voltooid.   Omdat de engine voor gepartities de geordende CCI-partitie per partitie maakt, hebben gebruikers nog steeds toegang tot de gegevens in partities waar het geordend maken van CCI niet wordt verwerkt.   U kunt deze optie gebruiken om de downtime tijdens het maken van geordende CCI voor grote tabellen te minimaliseren: 

1.    Maak partities op de grote doeltabel (met de naam Table_A).
2.    Maak een lege geordende CCI-tabel (met de naam Table_B) met hetzelfde tabel- en partitieschema als tabel A.
3.    Schakel één partitie van Tabel A naar Tabel B.
4.    Voer ALTER INDEX <Ordered_CCI_Index> ON <Table_B> REBUILD PARTITION = <Partition_ID> on Table B uit om de omgeschakelde partitie opnieuw te bouwen.  
5.    Herhaal stap 3 en 4 voor elke partitie in Table_A.
6.    Zodra alle partities zijn overgeschakeld van Table_A naar Table_B en opnieuw zijn opgebouwd, Table_A en wijzigt u de naam Table_B in Table_A. 

>[!TIP]
> Voor een toegewezen SQL-pooltabel met een geordende CCI sorteert ALTER INDEX REBUILD de gegevens opnieuw met tempdb. Tempdb bewaken tijdens herbouwbewerkingen. Als u meer tempdb-ruimte nodig hebt, schaalt u de pool omhoog. Schaal weer omlaag zodra de index opnieuw is opgebouwd.
>
> Voor een toegewezen SQL-pooltabel met een geordende CCI sorteert ALTER INDEX REORGANIZE de gegevens niet opnieuw. Als u gebruik wilt maken van gegevens, gebruikt u ALTER INDEX REBUILD.
>
> Zie Geclusterde [columnstore-indexen](sql-data-warehouse-tables-index.md#optimizing-clustered-columnstore-indexes)optimaliseren voor meer informatie over geordend CCI-onderhoud.

## <a name="examples"></a>Voorbeelden

**A. Controleren op geordende kolommen en rangorde:**

```sql
SELECT object_name(c.object_id) table_name, c.name column_name, i.column_store_order_ordinal 
FROM sys.index_columns i 
JOIN sys.columns c ON i.object_id = c.object_id AND c.column_id = i.column_id
WHERE column_store_order_ordinal <>0;
```

**B. Als u de kolom ordinale wilt wijzigen, voegt u kolommen toe aan of verwijdert u deze uit de orderlijst of wijzigt u van CCI in geordende CCI:**

```sql
CREATE CLUSTERED COLUMNSTORE INDEX InternetSales ON dbo.InternetSales
ORDER (ProductKey, SalesAmount)
WITH (DROP_EXISTING = ON);
```

## <a name="next-steps"></a>Volgende stappen

Zie overzicht van ontwikkeling voor meer [tips voor ontwikkeling.](sql-data-warehouse-overview-develop.md)
