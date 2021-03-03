---
title: Prestaties afstemmen met gerealiseerde weergaven
description: Aanbevelingen en overwegingen voor gerealiseerde weer gaven om de query prestaties te verbeteren.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: xiaoyul
ms.reviewer: nibruno; jrasnick
ms.openlocfilehash: e6c3987e2de7f9592a1f7f6086657592e1bf0c16
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101676595"
---
# <a name="performance-tuning-with-materialized-views-using-dedicated-sql-pool-in-azure-synapse-analytics"></a>Prestaties afstemmen met gerealiseerde weer gaven met exclusieve SQL-groep in azure Synapse Analytics

In de toegewezen SQL-groep bieden gerealiseerde weer gaven een lage onderhouds methode voor complexe analytische query's om snelle prestaties te krijgen zonder dat er een query wordt gewijzigd. In dit artikel vindt u de algemene richt lijnen voor het gebruik van gerealiseerde weer gaven.

## <a name="materialized-views-vs-standard-views"></a>Gerealiseerde weer gaven versus standaard weergaven

SQL-pool ondersteunt zowel standaard als gerealiseerde weer gaven.  Beide zijn virtuele tabellen die zijn gemaakt met SELECT-expressies en worden weer gegeven als logische tabellen.  In weer gaven wordt de complexiteit van de algemene gegevens berekening onthuld en wordt een abstractie laag aan de berekenings wijzigingen toegevoegd, zodat u geen query's hoeft te herschrijven.  

Een standaard weergave berekent de gegevens telkens wanneer de weer gave wordt gebruikt.  Er zijn geen gegevens opgeslagen op schijf. Personen gebruiken meestal standaard weergaven als een hulp middel waarmee u de logische objecten en query's in een Data Base kunt ordenen.  Als u een standaard weergave wilt gebruiken, moet er direct naar een query worden verwezen.

Een gerealiseerde weer gave van vooraf berekende, opgeslagen en onderhoudt de gegevens in een exclusieve SQL-groep, net als een tabel.  Herberekening is niet nodig wanneer een gerealiseerde weer gave wordt gebruikt.  Daarom kunnen query's die gebruikmaken van alle of een subset van de gegevens in gerealiseerde weer gaven, betere prestaties krijgen.  Daarnaast kunt u met query's gebruikmaken van een gerealiseerde weer gave zonder dat hiervoor direct een verwijzing wordt gemaakt. u hoeft geen toepassings code te wijzigen.  

De meeste standaard weergave vereisten zijn nog steeds van toepassing op een gerealiseerde weer gave. Zie [gerealiseerde weer gave maken als selecteren](/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?view=azure-sqldw-latest&preserve-view=true)voor meer informatie over de syntaxis van gerealiseerde weer gaven en andere vereisten.

| Vergelijking                     | Weergave                                         | Gerealiseerde weergave
|:-------------------------------|:---------------------------------------------|:--------------------------------------------------------------|
|Definitie weergeven                 | Opgeslagen in azure Data Warehouse.              | Opgeslagen in azure Data Warehouse.
|Inhoud weergeven                    | Elke keer dat de weer gave wordt gebruikt, gegenereerd.   | Vooraf verwerkt en opgeslagen in azure Data Warehouse tijdens het maken van de weer gave. Bijgewerkt wanneer gegevens worden toegevoegd aan de onderliggende tabellen.
|Gegevensvernieuwing                    | Altijd bijgewerkt                               | Altijd bijgewerkt
|Snelheid om weergave gegevens op te halen uit complexe query's     | Langzaam                                         | Snel  
|Extra opslag ruimte                   | Nee                                           | Ja
|Syntax                          | CREATE VIEW                                  | GEREALISEERDE WEER GAVE MAKEN ALS SELECTEREN

## <a name="benefits-of-materialized-views"></a>Voor delen van gerealiseerde weer gaven

Een goed ontworpen gerealiseerde weer gave biedt de volgende voor delen:

- Beperkte uitvoerings tijd voor complexe query's met samen voegingen en statistische functies. Hoe complexer de query, des te hoger de mogelijkheden voor het besparen van de uitvoering. Het meest voor deel is opgedaan wanneer de kosten van een query hoog zijn en de resulterende gegevensset klein is.  

- De query optimalisatie in de toegewezen SQL-groep kan automatisch geïmplementeerde gerealiseerde weer gaven gebruiken om de uitvoerings plannen voor query's te verbeteren.  Dit proces is transparant voor gebruikers die snellere query prestaties bieden en er hoeven geen query's te worden uitgevoerd om direct naar de gerealiseerde weer gaven te verwijzen.

- Vereist weinig onderhoud voor de weer gaven.  In een gerealiseerde weer gave worden gegevens op twee plaatsen opgeslagen, een geclusterde column store-index voor de eerste gegevens op de weer gave en een Delta-Archief voor de incrementele gegevens wijzigingen.  Alle gegevens wijzigingen van de basis tabellen worden op synchrone wijze automatisch toegevoegd aan de Delta opslag.  Een achtergrond proces (tuple-overschakeling) verplaatst de gegevens van het Delta archief periodiek naar de column store-index van de weer gave.  Met dit ontwerp kunt u query's uitvoeren op gerealiseerde weer gaven om dezelfde gegevens te retour neren als het rechtstreeks uitvoeren van query's op de basis tabellen.
- De gegevens in een gerealiseerde weer gave kunnen anders worden gedistribueerd vanuit de basis tabellen.  
- Gegevens in gerealiseerde weer gaven hebben dezelfde hoge Beschik baarheid en tolerantie voor delen als gegevens in reguliere tabellen.  

In vergelijking met andere data warehouse-providers bieden de gerealiseerde weer gaven die in de toegewezen SQL-pool worden geïmplementeerd ook de volgende extra voor delen:

- Automatische en synchrone gegevens vernieuwing met gegevens wijzigingen in basis tabellen. Er is geen gebruikers actie vereist.
- Ondersteuning voor uitgebreide statistische functies. Zie [gerealiseerde weer gave maken als Select (Transact-SQL)](/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?view=azure-sqldw-latest&preserve-view=true).
- De ondersteuning voor query-specifieke gerealiseerde weergave aanbeveling.  Zie [uitleggen (Transact-SQL)](/sql/t-sql/queries/explain-transact-sql?view=azure-sqldw-latest&preserve-view=true).

## <a name="common-scenarios"></a>Algemene scenario's  

Gerealiseerde weer gaven worden doorgaans gebruikt in de volgende scenario's:

**De prestaties van complexe analytische query's voor grote gegevens in omvang moeten worden verbeterd**

Complexe analytische query's gebruiken meestal meer aggregatie functies en tabel samenvoegingen, waardoor er meer compute-bewerkingen worden uitgevoerd, zoals wille keurige Volg ordes en samen voegingen bij het uitvoeren van query's.  Daarom duurt het langer voordat deze query's zijn voltooid, met name voor grote tabellen.  

Gebruikers kunnen gerealiseerde weer gaven maken voor de gegevens die worden geretourneerd door de algemene berekeningen van query's. Daarom is er geen herberekening vereist wanneer deze gegevens door query's nodig zijn, waardoor lagere reken kosten en een snellere query respons worden toegestaan.

**Behoefte aan snellere prestaties zonder of minimale query wijzigingen**

Wijzigingen in schema's en query's in data warehouses worden doorgaans beperkt tot een minimum voor de ondersteuning van normale ETL-bewerkingen en rapportage.  Personen kunnen gerealiseerde weer gaven gebruiken voor het afstemmen van de query prestaties als de kosten die worden gemaakt door de weer gaven, kunnen worden gecompenseerd door de toename van de query prestaties.

In vergelijking met andere afstemmings opties, zoals schalen en statistieken, is het een veel minder belang rijke productie wijziging om een gerealiseerde weer gave te maken en te onderhouden, en is de potentiële prestatie verbetering ook hoger.

- Het maken of onderhouden van gerealiseerde weer gaven heeft geen invloed op de query's die worden uitgevoerd op de basis tabellen.
- De query optimalisatie kan automatisch gebruikmaken van de geïmplementeerde gerealiseerde weer gaven zonder directe weergave verwijzing in een query. Deze functie vermindert de nood zaak voor het afstemmen van de prestaties van de query.

**U hebt verschillende strategieën voor het distribueren van gegevens nodig voor snellere query prestaties**

Azure data warehouse is een gedistribueerd en een systeem met een enorme parallelle verwerking (MPP).  

Synapse SQL is een gedistribueerd querysysteem waarmee ondernemingen datawarehousing en gegevensvirtualisatie-scenario's kunnen implementeren met behulp van standaard T-SQL-ervaringen die bekend zijn bij data engineers. Ook worden de mogelijkheden van SQL uitgebreid om scenario's wat betreft streaming- en machine learning aan te pakken. Gegevens in een Data Warehouse-tabel worden verdeeld over 60-knoop punten met behulp van een van de drie [distributie strategieën](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) (hash, round_robin of gerepliceerd).  

De gegevens distributie wordt opgegeven tijdens het maken van de tabel en blijft ongewijzigd totdat de tabel wordt verwijderd. Gerealiseerde weer gave van een virtuele tabel op schijf ondersteunt hash-en round_robin gegevens distributies.  Gebruikers kunnen een gegevens distributie kiezen die afwijkt van de basis tabellen, maar wel optimaal is voor de prestaties van query's die regel matig gebruikmaken van de weer gaven.  

## <a name="design-guidance"></a>Ontwerp richtlijnen

Hier volgt de algemene richt lijnen voor het gebruik van gerealiseerde weer gaven om de query prestaties te verbeteren:

**Ontwerpen voor uw workload**

Voordat u begint met het maken van gerealiseerde weer gaven, is het belang rijk dat u een grondige uitleg krijgt van uw werk belasting in termen van query patronen, urgentie, frequentie en de grootte van de resulterende gegevens.  

Gebruikers kunnen uitleg WITH_RECOMMENDATIONS <SQL_statement> uitvoeren voor de gerealiseerde weer gaven die worden aanbevolen door de query Optimizer.  Omdat deze aanbevelingen specifiek zijn voor query's, is het mogelijk dat een gerealiseerde weer gave die één query voordoet, niet optimaal is voor andere query's in dezelfde werk belasting.  

Bekijk deze aanbevelingen met betrekking tot uw workload behoeften.  De ideale gerealiseerde weer gaven zijn die voor de prestaties van de werk belasting.  

**Houd rekening met de verhouding tussen snellere query's en de kosten**

Voor elke gerealiseerde weer gave zijn er kosten voor de gegevens opslag en de kosten voor het onderhouden van de weer gave.  Naarmate er gegevens worden gewijzigd in de basis tabellen, neemt de grootte van de gerealiseerde weer gave toe en wordt de fysieke structuur ook gewijzigd.  

Om te voor komen dat de prestaties van query's worden vertraagd, wordt elke gerealiseerde weer gave afzonderlijk beheerd door de Data Warehouse-engine, inclusief het verplaatsen van rijen uit het Delta archief naar de column store-index segmenten en het consolideren van gegevens wijzigingen.  

De werk belasting van het onderhoud stijgt hoger wanneer het aantal gerealiseerde weer gaven en basis tabel wijzigingen toeneemt.   Gebruikers moeten controleren of de kosten die zijn gemaakt voor alle gerealiseerde weer gaven, kunnen worden gecompenseerd door de prestatie verbetering van de query.  

U kunt deze query uitvoeren voor de lijst met gerealiseerde weer gaven in een Data Base:

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

De optimalisatie van het Data Warehouse kan automatisch geïmplementeerde gerealiseerde weer gaven gebruiken om de query prestaties te verbeteren.  Deze ondersteuning wordt op transparante wijze toegepast op query's die niet verwijzen naar de weer gaven en naar query's waarbij aggregaties worden gebruikt die niet worden ondersteund in gerealiseerde weer gaven maken.  Er is geen query wijziging nodig. U kunt het geschatte uitvoerings plan van een query controleren om te bevestigen of een gerealiseerde weer gave wordt gebruikt.  

**Gerealiseerde weer gaven bewaken**

Een gerealiseerde weer gave wordt in het Data Warehouse opgeslagen net als een tabel met geclusterde column store-index (CCI).  Het lezen van gegevens vanuit een gerealiseerde weer gave omvat het scannen van de index en het Toep assen van wijzigingen in het Delta-archief.  Wanneer het aantal rijen in de Delta opslag te hoog is, kan het oplossen van een query vanuit een gerealiseerde weer gave langer duren dan het rechtstreeks opvragen van query's in de basis tabellen.  

Om te voor komen dat de prestaties van query's worden vertraagd, is het een goed idee om [DBCC PDW_SHOWMATERIALIZEDVIEWOVERHEAD](/sql/t-sql/database-console-commands/dbcc-pdw-showmaterializedviewoverhead-transact-sql?view=azure-sqldw-latest&preserve-view=true) uit te voeren om de overhead_ratio van de weer gave te bewaken (total_rows/base_view_row).  Als de overhead_ratio te hoog is, kunt u de gerealiseerde weer gave opnieuw samen stellen, zodat alle rijen in het Delta archief worden verplaatst naar de column store-index.  

**Gerealiseerde weer gave en caching van resultaten sets**

Deze twee functies worden op hetzelfde moment geïntroduceerd in een toegewezen SQL-groep voor het afstemmen van de query prestaties. Het opslaan in de cache voor de resultatenset wordt gebruikt voor het bereiken van hoge gelijktijdigheid en snelle respons tijden van herhaalde query's tegen statische gegevens.  

Om het resultaat in de cache te kunnen gebruiken, moet de vorm van de cache die query vraagt overeenkomen met de query die de cache heeft geproduceerd.  Daarnaast moet het resultaat in de cache op de hele query worden toegepast.  

Met gerealiseerde weer gaven kunnen gegevens wijzigingen in de basis tabellen worden aangebracht.  Gegevens in gerealiseerde weer gaven kunnen worden toegepast op een stukje van een query.  Met deze ondersteuning kunnen dezelfde gerealiseerde weer gaven worden gebruikt door verschillende query's die een bepaalde berekening delen voor betere prestaties.

## <a name="example"></a>Voorbeeld

In dit voor beeld wordt een TPCDS-achtige query gebruikt waarmee klanten die meer geld uitgeven via catalog dan in winkels. Het identificeert ook de voorkeurs klanten en hun land/regio van oorsprong.   De query moet het selecteren van TOP 100-records uit de samen VOEGing van drie subselect-instructies waarbij SUM () en GROUP BY worden geselecteerd.

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

Controleer het uitvoerings plan van de oorspronkelijke query opnieuw.  Nu wordt het aantal aansluitingen gewijzigd van 17 in 5 en is er geen wille keurige volg orde.  Selecteer het pictogram voor de filter bewerking in het plan. In de uitvoer lijst ziet u dat de gegevens worden gelezen uit de gerealiseerde weer gaven in plaats van met basis tabellen.  

 ![Plan_Output_List_with_Materialized_Views](./media/develop-materialized-view-performance-tuning/output-list.png)

Met gerealiseerde weer gaven wordt dezelfde query veel sneller uitgevoerd zonder dat de code wordt gewijzigd.  

## <a name="next-steps"></a>Volgende stappen

Zie [Synapse SQL Development Overview](develop-overview.md)(Engelstalig) voor meer tips voor ontwikkel aars.
 