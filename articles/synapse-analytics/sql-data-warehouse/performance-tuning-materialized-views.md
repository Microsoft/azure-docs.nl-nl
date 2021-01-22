---
title: Prestaties afstemmen met gerealiseerde weer gaven
description: Meer informatie over aanbevelingen en overwegingen die u moet kennen bij het gebruik van gerealiseerde weer gaven om de query prestaties te verbeteren.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 09/05/2019
ms.author: xiaoyul
ms.reviewer: nibruno; jrasnick; azure-synapse
ms.openlocfilehash: e137611809e2d2beefecfeaea11b4295bf6ba141
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98678488"
---
# <a name="performance-tune-with-materialized-views"></a>Prestaties afstemmen met gerealiseerde weer gaven

Gerealiseerde weer gaven in azure Synapse SQL pool bieden een lage onderhouds methode voor complexe analytische query's om snelle prestaties te krijgen zonder dat er query's worden gewijzigd. In dit artikel vindt u de algemene richt lijnen voor het gebruik van gerealiseerde weer gaven.

## <a name="materialized-views-vs-standard-views"></a>Gerealiseerde weer gaven versus standaard weergaven

SQL-groep in azure Synapse ondersteunt standaard-en gerealiseerde weer gaven.  Beide zijn virtuele tabellen die zijn gemaakt met SELECT-expressies en worden weer gegeven als logische tabellen.  Met weer gaven wordt de complexiteit van common data computing ingekapseld en wordt een abstractie laag aan berekenings wijzigingen toegevoegd, zodat u geen query's hoeft te schrijven.  

Een standaard weergave berekent de gegevens telkens wanneer de weer gave wordt gebruikt.  Er zijn geen gegevens opgeslagen op schijf. Personen gebruiken meestal standaard weergaven als een hulp middel waarmee u de logische objecten en query's in een SQL-groep kunt ordenen.  Als u een standaard weergave wilt gebruiken, moet er direct naar een query worden verwezen.

Met een gerealiseerde weer gave worden de gegevens in de SQL-groep, net als in een tabel, opgeslagen en bewaard.  Telkens wanneer een gerealiseerde weer gave wordt gebruikt, is er geen herberekening nodig.  Daarom kunnen query's die gebruikmaken van alle of subset van de gegevens in gerealiseerde weer gaven, betere prestaties krijgen.  Daarnaast kunt u met query's gebruikmaken van een gerealiseerde weer gave zonder dat hiervoor direct een verwijzing wordt gemaakt. u hoeft geen toepassings code te wijzigen.  

De meeste vereisten voor een standaard weergave zijn nog steeds van toepassing op een gerealiseerde weer gave. Zie [gerealiseerde weer gave maken als selecteren](/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) voor meer informatie over de syntaxis van gerealiseerde weer gaven en andere vereisten.

| Vergelijking                     | Weergave                                         | Gerealiseerde weergave
|:-------------------------------|:---------------------------------------------|:--------------------------------------------------------------|
|Definitie weergeven                 | Opgeslagen in SQL-groep.              | Opgeslagen in SQL-groep.
|Inhoud weergeven                    | Elke keer dat de weer gave wordt gebruikt, gegenereerd.   | Vooraf verwerkt en opgeslagen in SQL-groep tijdens het maken van de weer gave. Bijgewerkt wanneer gegevens worden toegevoegd aan de onderliggende tabellen.
|Gegevensvernieuwing                    | Altijd bijgewerkt                               | Altijd bijgewerkt
|Snelheid om weergave gegevens op te halen uit complexe query's     | Langzaam                                         | Snel  
|Extra opslag ruimte                   | Nee                                           | Ja
|Syntax                          | CREATE VIEW                                  | GEREALISEERDE WEER GAVE MAKEN ALS SELECTEREN

## <a name="benefits-of-using-materialized-views"></a>Voor delen van het gebruik van gerealiseerde weer gaven

Een goed ontworpen gerealiseerde weer gave biedt de volgende voor delen:

- Verminder de uitvoerings tijd voor complexe query's met samen voegingen en statistische functies. Hoe complexer de query, des te hoger de mogelijkheden voor het besparen van de uitvoering. Het meest voor deel is opgedaan wanneer de kosten van een query hoog zijn en de resulterende gegevensset klein is.  
- De Optimizer in de SQL-groep kan automatisch geïmplementeerde gerealiseerde weer gaven gebruiken om de uitvoerings plannen voor query's te verbeteren.  Dit proces is transparant voor gebruikers die snellere query prestaties bieden en er hoeven geen query's te worden uitgevoerd om direct naar de gerealiseerde weer gaven te verwijzen.
- Weinig onderhoud vereist voor de weer gaven.  Alle incrementele gegevens wijzigingen ten opzichte van de basis tabellen worden op synchrone wijze automatisch toegevoegd aan de gerealiseerde weer gaven.  Met dit ontwerp kunt u query's uitvoeren op gerealiseerde weer gaven om dezelfde gegevens te retour neren als het rechtstreeks uitvoeren van query's op de basis tabellen.
- De gegevens in een gerealiseerde weer gave kunnen anders worden gedistribueerd vanuit de basis tabellen.  
- Gegevens in gerealiseerde weer gaven hebben dezelfde hoge Beschik baarheid en tolerantie voor delen als gegevens in reguliere tabellen.  

De gerealiseerde weer gaven die in de SQL-groep zijn geïmplementeerd, bieden ook de volgende extra voor delen:

Vergelijken met andere data warehouse-providers, bieden de gerealiseerde weer gaven die in azure Synapse Analytics worden geïmplementeerd ook de volgende extra voor delen:

- Automatische en synchrone gegevens vernieuwing met gegevens wijzigingen in basis tabellen. Er is geen gebruikers actie vereist.
- Ondersteuning voor uitgebreide statistische functies. Zie [gerealiseerde weer gave maken als Select (Transact-SQL)](/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).
- De ondersteuning voor query-specifieke gerealiseerde weergave aanbeveling.  Zie [uitleggen (Transact-SQL)](/sql/t-sql/queries/explain-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

## <a name="common-scenarios"></a>Algemene scenario's  

Gerealiseerde weer gaven worden doorgaans gebruikt in de volgende scenario's:

**De prestaties van complexe analytische query's voor grote gegevens in omvang moeten worden verbeterd**

Complexe analytische query's gebruiken meestal meer aggregatie functies en tabel samenvoegingen, waardoor er meer compute-bewerkingen worden uitgevoerd, zoals wille keurige Volg ordes en samen voegingen bij het uitvoeren van query's.  Daarom nemen deze query's langer toe om te worden voltooid, speciaal voor grote tabellen.  

Gebruikers kunnen gerealiseerde weer gaven maken voor de gegevens die worden geretourneerd door de algemene berekeningen van query's. Daarom is er geen herberekening vereist wanneer deze gegevens door query's nodig zijn, waardoor lagere reken kosten en een snellere query respons worden toegestaan.

**Behoefte aan snellere prestaties zonder of minimale query wijzigingen**

Wijzigingen in het schema en de query in SQL-groepen worden doorgaans beperkt tot een minimum voor de ondersteuning van normale ETL-bewerkingen en rapportage.  Personen kunnen gerealiseerde weer gaven gebruiken voor het afstemmen van de query prestaties, als de kosten die worden gemaakt door de weer gaven, kunnen worden gecompenseerd door de toename van de query prestaties.

Ten opzichte van andere afstemmings opties, zoals schalen en statistieken, is het een veel minder belang rijke productie wijziging voor het maken en onderhouden van een gerealiseerde weer gave en de potentiële prestaties van het prestatie niveau ook hoger.

- Het maken of onderhouden van gerealiseerde weer gaven heeft geen invloed op de query's die worden uitgevoerd op de basis tabellen.
- De query optimalisatie kan automatisch gebruikmaken van de geïmplementeerde gerealiseerde weer gaven zonder directe weergave verwijzing in een query. Deze functie vermindert de nood zaak voor het afstemmen van de prestaties van de query.

**U hebt verschillende strategieën voor het distribueren van gegevens nodig voor snellere query prestaties**

Azure Synapse Analytics is een systeem voor gedistribueerde query verwerking.  Gegevens in een SQL-tabel worden verdeeld over 60 knoop punten met behulp van een van de drie [distributie strategieën](sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) (hash, round_robin of gerepliceerd).   

De gegevens distributie is opgegeven bij de aanmaak tijd van de tabel en blijft ongewijzigd totdat de tabel wordt verwijderd. Gerealiseerde weer gave van een virtuele tabel op schijf ondersteunt hash-en round_robin gegevens distributies.  Gebruikers kunnen een gegevens distributie kiezen die afwijkt van de basis tabellen, maar wel optimaal is voor de prestaties van query's die de weer gaven het meest gebruiken.  

## <a name="design-guidance"></a>Ontwerp richtlijnen

Hier volgt de algemene richt lijnen voor het gebruik van gerealiseerde weer gaven om de query prestaties te verbeteren:

**Ontwerpen voor uw workload**

Voordat u begint met het maken van gerealiseerde weer gaven, is het belang rijk dat u een grondige uitleg krijgt van uw werk belasting in termen van query patronen, urgentie, frequentie en de grootte van de resulterende gegevens.  

Gebruikers kunnen uitleg WITH_RECOMMENDATIONS <SQL_statement> uitvoeren voor de gerealiseerde weer gaven die worden aanbevolen door de query Optimizer.  Omdat deze aanbevelingen specifiek zijn voor query's, is het mogelijk dat een gerealiseerde weer gave die één query voordoet, niet optimaal is voor andere query's in dezelfde werk belasting.  

Bekijk deze aanbevelingen met betrekking tot uw workload behoeften.  De ideale gerealiseerde weer gaven zijn die voor de prestaties van de werk belasting.  

**Houd rekening met de verhouding tussen snellere query's en de kosten**

Voor elke gerealiseerde weer gave zijn er kosten voor de gegevens opslag en de kosten voor het onderhouden van de weer gave.  Naarmate gegevens wijzigingen in basis tabellen worden aangebracht, neemt de grootte van de gerealiseerde weer gave toe en wordt de fysieke structuur ook gewijzigd.  Om te voor komen dat de query prestaties verslechteren, wordt elke gerealiseerde weer gave afzonderlijk beheerd door de SQL Analytics-engine.  

De werk belasting van de onderhouds taken is hoger wanneer het aantal gerealiseerde weer gaven en basis tabel wijzigingen toeneemt.   Gebruikers moeten controleren of de kosten die zijn gemaakt voor alle gerealiseerde weer gaven, kunnen worden gecompenseerd door de prestatie verbetering van de query.  

U kunt deze query uitvoeren voor de lijst met gerealiseerde weer gaven in een SQL-groep:

```sql
SELECT V.name as materialized_view, V.object_id
FROM sys.views V
JOIN sys.indexes I ON V.object_id= I.object_id AND I.index_id < 2;
```

Opties om het aantal gerealiseerde weer gaven te verminderen:

- Identificeer veelgebruikte gegevens sets die vaak worden gebruikt door de complexe query's in uw werk belasting.  Maak gerealiseerde weer gaven om deze gegevens sets op te slaan, zodat de Optimizer ze kan gebruiken als bouw stenen bij het maken van uitvoerings plannen.  

- De gerealiseerde weer gaven met weinig gebruik verwijderen of niet meer nodig zijn.  Een uitgeschakelde gerealiseerde weer gave wordt niet behouden, maar de opslag kosten worden nog steeds tegengesteld.  

- Combi neer gerealiseerde weer gaven die zijn gemaakt in dezelfde of vergelijk bare basis tabellen, zelfs als de gegevens elkaar niet overlappen.  Het combi neren van gerealiseerde weer gaven kan ertoe leiden dat een grotere weer gave groter is dan de som van de afzonderlijke weer gaven, maar de kosten voor de onderhouds beurt moeten worden verminderd.  Bijvoorbeeld:

```sql

-- Query 1 would benefit from having a materialized view created with this SELECT statement

SELECT A, SUM(B)
FROM T
GROUP BY A

-- Query 2 would benefit from having a materialized view created with this SELECT statement

SELECT C, SUM(D)
FROM T
GROUP BY C

-- You could create a single mateiralized view of this form

SELECT A, C, SUM(B), SUM(D)
FROM T
GROUP BY A, C

```

**Niet alle prestatie afstemming vereist een query wijziging**

De SQL Analytics Optimizer kan automatisch geïmplementeerde gerealiseerde weer gaven gebruiken om de query prestaties te verbeteren.  Deze ondersteuning wordt op transparante wijze toegepast op query's die niet verwijzen naar de weer gaven en query's die niet worden ondersteund in gerealiseerde weer gaven.  Er is geen query wijziging nodig. U kunt het geschatte uitvoerings plan van een query controleren om te bevestigen of een gerealiseerde weer gave wordt gebruikt.  

**Gerealiseerde weer gaven bewaken**

Een gerealiseerde weer gave wordt net als een tabel met geclusterde column store-index (CCI) opgeslagen in de SQL-groep.  Het lezen van gegevens vanuit een gerealiseerde weer gave omvat het scannen van de CCI-index segmenten en het Toep assen van incrementele wijzigingen van basis tabellen. Wanneer het aantal incrementele wijzigingen te hoog is, kan het omzetten van een query vanuit een gerealiseerde weer gave langer duren dan het rechtstreeks opvragen van de basis tabellen.  

Om te voor komen dat de prestaties van query's teruglopen, is het een goed idee om [DBCC PDW_SHOWMATERIALIZEDVIEWOVERHEAD](/sql/t-sql/database-console-commands/dbcc-pdw-showmaterializedviewoverhead-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) uit te voeren om de overhead_ratio van de weer gave te controleren (total_rows/Max (1, base_view_row)).  Gebruikers moeten de gerealiseerde weer gave opnieuw samen stellen als de overhead_ratio te hoog is.

**Gerealiseerde weer gave en caching van resultaten sets**

Deze twee functies worden op dezelfde tijd geïntroduceerd in SQL Analytics voor het afstemmen van query prestaties.  Cache voor de resultatenset wordt gebruikt voor het ophalen van een hoge gelijktijdigheid en snelle respons van herhaalde query's tegen statische gegevens.  

Om het resultaat in de cache te kunnen gebruiken, moet de vorm van de cache die query vraagt overeenkomen met de query die de cache heeft geproduceerd.  Daarnaast moet het resultaat in de cache op de hele query worden toegepast.  

Met gerealiseerde weer gaven kunnen gegevens wijzigingen in de basis tabellen worden aangebracht.  Gegevens in gerealiseerde weer gaven kunnen worden toegepast op een stukje van een query.  Met deze ondersteuning kunnen dezelfde gerealiseerde weer gaven worden gebruikt door verschillende query's die een bepaalde berekening delen voor betere prestaties.

## <a name="example"></a>Voorbeeld

In dit voor beeld wordt een TPCDS-achtige query gebruikt waarmee klanten die meer geld uitgeven via catalog dan in winkels, de voorkeurs klanten en hun land of regio van herkomst identificeren.   De query moet het selecteren van TOP 100-records uit de samen VOEGing van drie subselect-instructies waarbij SUM () en GROUP BY worden geselecteerd.

```sql
WITH year_total AS (
SELECT c_customer_id customer_id
       ,c_first_name customer_first_name
       ,c_last_name customer_last_name
       ,c_preferred_cust_flag customer_preferred_cust_flag
       ,c_birth_country customer_birth_country
       ,c_login customer_login
       ,c_email_address customer_email_address
       ,d_year dyear
       ,sum(isnull(ss_ext_list_price-ss_ext_wholesale_cost-ss_ext_discount_amt+ss_ext_sales_price, 0)/2) year_total
       ,'s' sale_type
FROM customer
     ,store_sales
     ,date_dim
WHERE c_customer_sk = ss_customer_sk
   AND ss_sold_date_sk = d_date_sk
GROUP BY c_customer_id
         ,c_first_name
         ,c_last_name
         ,c_preferred_cust_flag
         ,c_birth_country
         ,c_login
         ,c_email_address
         ,d_year
UNION ALL
SELECT c_customer_id customer_id
       ,c_first_name customer_first_name
       ,c_last_name customer_last_name
       ,c_preferred_cust_flag customer_preferred_cust_flag
       ,c_birth_country customer_birth_country
       ,c_login customer_login
       ,c_email_address customer_email_address
       ,d_year dyear
       ,sum(isnull(cs_ext_list_price-cs_ext_wholesale_cost-cs_ext_discount_amt+cs_ext_sales_price, 0)/2) year_total
       ,'c' sale_type
FROM customer
     ,catalog_sales
     ,date_dim
WHERE c_customer_sk = cs_bill_customer_sk
   AND cs_sold_date_sk = d_date_sk
GROUP BY c_customer_id
         ,c_first_name
         ,c_last_name
         ,c_preferred_cust_flag
         ,c_birth_country
         ,c_login
         ,c_email_address
         ,d_year
UNION ALL
SELECT c_customer_id customer_id
       ,c_first_name customer_first_name
       ,c_last_name customer_last_name
       ,c_preferred_cust_flag customer_preferred_cust_flag
       ,c_birth_country customer_birth_country
       ,c_login customer_login
       ,c_email_address customer_email_address
       ,d_year dyear
       ,sum(isnull(ws_ext_list_price-ws_ext_wholesale_cost-ws_ext_discount_amt+ws_ext_sales_price, 0)/2) year_total
       ,'w' sale_type
FROM customer
     ,web_sales
     ,date_dim
WHERE c_customer_sk = ws_bill_customer_sk
   AND ws_sold_date_sk = d_date_sk
GROUP BY c_customer_id
         ,c_first_name
         ,c_last_name
         ,c_preferred_cust_flag
         ,c_birth_country
         ,c_login
         ,c_email_address
         ,d_year
         )
  SELECT TOP 100
                  t_s_secyear.customer_id
                 ,t_s_secyear.customer_first_name
                 ,t_s_secyear.customer_last_name
                 ,t_s_secyear.customer_birth_country
FROM year_total t_s_firstyear
     ,year_total t_s_secyear
     ,year_total t_c_firstyear
     ,year_total t_c_secyear
     ,year_total t_w_firstyear
     ,year_total t_w_secyear
WHERE t_s_secyear.customer_id = t_s_firstyear.customer_id
   AND t_s_firstyear.customer_id = t_c_secyear.customer_id
   AND t_s_firstyear.customer_id = t_c_firstyear.customer_id
   AND t_s_firstyear.customer_id = t_w_firstyear.customer_id
   AND t_s_firstyear.customer_id = t_w_secyear.customer_id
   AND t_s_firstyear.sale_type = 's'
   AND t_c_firstyear.sale_type = 'c'
   AND t_w_firstyear.sale_type = 'w'
   AND t_s_secyear.sale_type = 's'
   AND t_c_secyear.sale_type = 'c'
   AND t_w_secyear.sale_type = 'w'
   AND t_s_firstyear.dyear+0 =  1999
   AND t_s_secyear.dyear+0 = 1999+1
   AND t_c_firstyear.dyear+0 =  1999
   AND t_c_secyear.dyear+0 =  1999+1
   AND t_w_firstyear.dyear+0 = 1999
   AND t_w_secyear.dyear+0 = 1999+1
   AND t_s_firstyear.year_total > 0
   AND t_c_firstyear.year_total > 0
   AND t_w_firstyear.year_total > 0
   AND CASE WHEN t_c_firstyear.year_total > 0 THEN t_c_secyear.year_total / t_c_firstyear.year_total ELSE NULL END
           > CASE WHEN t_s_firstyear.year_total > 0 THEN t_s_secyear.year_total / t_s_firstyear.year_total ELSE NULL END
   AND CASE WHEN t_c_firstyear.year_total > 0 THEN t_c_secyear.year_total / t_c_firstyear.year_total ELSE NULL END
           > CASE WHEN t_w_firstyear.year_total > 0 THEN t_w_secyear.year_total / t_w_firstyear.year_total ELSE NULL END
ORDER BY t_s_secyear.customer_id
         ,t_s_secyear.customer_first_name
         ,t_s_secyear.customer_last_name
         ,t_s_secyear.customer_birth_country
OPTION ( LABEL = 'Query04-af359846-253-3');
```

Controleer het geschatte uitvoerings plan van de query.  Er zijn 18 actie-en 17-koppelings bewerkingen, waardoor meer tijd nodig is om uit te voeren. We gaan nu één gerealiseerde weer gave maken voor elk van de drie subselect-instructies.

```sql
CREATE materialized view nbViewSS WITH (DISTRIBUTION=HASH(customer_id)) AS
SELECT c_customer_id customer_id
       ,c_first_name customer_first_name
       ,c_last_name customer_last_name
       ,c_preferred_cust_flag customer_preferred_cust_flag
       ,c_birth_country customer_birth_country
       ,c_login customer_login
       ,c_email_address customer_email_address
       ,d_year dyear
       ,sum(isnull(ss_ext_list_price-ss_ext_wholesale_cost-ss_ext_discount_amt+ss_ext_sales_price, 0)/2) year_total
          , count_big(*) AS cb
FROM dbo.customer
     ,dbo.store_sales
     ,dbo.date_dim
WHERE c_customer_sk = ss_customer_sk
   AND ss_sold_date_sk = d_date_sk
GROUP BY c_customer_id
         ,c_first_name
         ,c_last_name
         ,c_preferred_cust_flag
         ,c_birth_country
         ,c_login
         ,c_email_address
         ,d_year
GO
CREATE materialized view nbViewCS WITH (DISTRIBUTION=HASH(customer_id)) AS
SELECT c_customer_id customer_id
       ,c_first_name customer_first_name
       ,c_last_name customer_last_name
       ,c_preferred_cust_flag customer_preferred_cust_flag
       ,c_birth_country customer_birth_country
       ,c_login customer_login
       ,c_email_address customer_email_address
       ,d_year dyear
       ,sum(isnull(cs_ext_list_price-cs_ext_wholesale_cost-cs_ext_discount_amt+cs_ext_sales_price, 0)/2) year_total
          , count_big(*) as cb
FROM dbo.customer
     ,dbo.catalog_sales
     ,dbo.date_dim
WHERE c_customer_sk = cs_bill_customer_sk
   AND cs_sold_date_sk = d_date_sk
GROUP BY c_customer_id
         ,c_first_name
         ,c_last_name
         ,c_preferred_cust_flag
         ,c_birth_country
         ,c_login
         ,c_email_address
         ,d_year

GO
CREATE materialized view nbViewWS WITH (DISTRIBUTION=HASH(customer_id)) AS
SELECT c_customer_id customer_id
       ,c_first_name customer_first_name
       ,c_last_name customer_last_name
       ,c_preferred_cust_flag customer_preferred_cust_flag
       ,c_birth_country customer_birth_country
       ,c_login customer_login
       ,c_email_address customer_email_address
       ,d_year dyear
       ,sum(isnull(ws_ext_list_price-ws_ext_wholesale_cost-ws_ext_discount_amt+ws_ext_sales_price, 0)/2) year_total
          , count_big(*) AS cb
FROM dbo.customer
     ,dbo.web_sales
     ,dbo.date_dim
WHERE c_customer_sk = ws_bill_customer_sk
   AND ws_sold_date_sk = d_date_sk
GROUP BY c_customer_id
         ,c_first_name
         ,c_last_name
         ,c_preferred_cust_flag
         ,c_birth_country
         ,c_login
         ,c_email_address
         ,d_year

```

Controleer het uitvoerings plan van de oorspronkelijke query opnieuw.  Nu wordt het aantal aansluitingen gewijzigd van 17 in 5 en is er geen wille keurige volg orde.  Klik op het pictogram voor de filter bewerking in het plan. in de uitvoer lijst worden de gegevens uit de gerealiseerde weer gaven gelezen in plaats van met basis tabellen.  

 ![Plan_Output_List_with_Materialized_Views](./media/performance-tuning-materialized-views/output-list.png)

Met gerealiseerde weer gaven wordt dezelfde query veel sneller uitgevoerd zonder dat de code wordt gewijzigd.  

## <a name="next-steps"></a>Volgende stappen

Zie [Synapse SQL pool Development Overview](sql-data-warehouse-overview-develop.md)(Engelstalig) voor meer tips voor ontwikkel aars.
