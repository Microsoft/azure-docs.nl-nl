---
title: Retail-gegevens van Contoso laden in toegewezen SQL-groepen
description: Gebruik poly base-en T-SQL-opdrachten om twee tabellen uit de Retail gegevens van Contoso te laden in toegewezen SQL-groepen.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/20/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: bbe61444404b16a09a1e0d2bdead72ac53a60744
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96452887"
---
# <a name="load-contoso-retail-data-into-dedicated-sql-pools-in-azure-synapse-analytics"></a>Retail-gegevens van Contoso in speciale SQL-groepen in azure Synapse Analytics laden

In deze zelf studie leert u hoe u poly base-en T-SQL-opdrachten gebruikt om twee tabellen uit de Retail gegevens van Contoso te laden in toegewezen SQL-groepen.

In deze zelfstudie gaat u:

1. Poly base configureren om te laden vanuit Azure Blob-opslag
2. Open bare gegevens in uw data base laden
3. Voer optimalisaties uit nadat het laden is voltooid.

## <a name="before-you-begin"></a>Voordat u begint

Als u deze zelf studie wilt uitvoeren, hebt u een Azure-account nodig dat al een toegewezen SQL-groep heeft. Als u geen data warehouse hebt ingericht, raadpleegt u [een Data Warehouse maken en firewall regel op server niveau instellen](create-data-warehouse-portal.md).

## <a name="configure-the-data-source"></a>De gegevens bron configureren

Poly base maakt gebruik van externe T-SQL-objecten om de locatie en kenmerken van de externe gegevens te definiëren. De definities voor externe objecten worden opgeslagen in toegewezen SQL-groepen. De gegevens worden extern opgeslagen.

## <a name="create-a-credential"></a>Een referentie maken

**Sla deze stap over** als u de open bare gegevens van Contoso wilt laden. U hebt geen beveiligde toegang tot de open bare gegevens nodig omdat deze al toegankelijk is voor iedereen.

**Sla deze stap niet over** als u deze zelf studie gebruikt als sjabloon voor het laden van uw eigen gegevens. Voor toegang tot gegevens via een referentie gebruikt u het volgende script om een Data Base-scoped referentie te maken. Gebruik deze vervolgens als u de locatie van de gegevens bron definieert.

```sql
-- A: Create a master key.
-- Only necessary if one does not already exist.
-- Required to encrypt the credential secret in the next step.

CREATE MASTER KEY;


-- B: Create a database scoped credential
-- IDENTITY: Provide any string, it is not used for authentication to Azure storage.
-- SECRET: Provide your Azure storage account key.


CREATE DATABASE SCOPED CREDENTIAL AzureStorageCredential
WITH
    IDENTITY = 'user',
    SECRET = '<azure_storage_account_key>'
;


-- C: Create an external data source
-- TYPE: HADOOP - PolyBase uses Hadoop APIs to access data in Azure blob storage.
-- LOCATION: Provide Azure storage account name and blob container name.
-- CREDENTIAL: Provide the credential created in the previous step.

CREATE EXTERNAL DATA SOURCE AzureStorage
WITH (
    TYPE = HADOOP,
    LOCATION = 'wasbs://<blob_container_name>@<azure_storage_account_name>.blob.core.windows.net',
    CREDENTIAL = AzureStorageCredential
);
```

## <a name="create-the-external-data-source"></a>De externe gegevens bron maken

Gebruik deze opdracht [externe gegevens bron maken](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) om de locatie van de gegevens en het gegevens type op te slaan.

```sql
CREATE EXTERNAL DATA SOURCE AzureStorage_west_public
WITH
(  
    TYPE = Hadoop
,   LOCATION = 'wasbs://contosoretaildw-tables@contosoretaildw.blob.core.windows.net/'
);
```

> [!IMPORTANT]
> Als u ervoor kiest om uw Azure Blob-opslag containers openbaar te maken, moet u er rekening mee houden dat als de eigenaar van de gegevens in rekening wordt gebracht voor de kosten voor gegevens uitvoer wanneer gegevens het Data Center verlaten.

## <a name="configure-the-data-format"></a>De gegevens indeling configureren

De gegevens worden opgeslagen in tekst bestanden in Azure Blob-opslag en elk veld wordt gescheiden met een scheidings teken. Voer in SSMS de volgende opdracht voor het maken van een externe BESTANDS indeling uit om de indeling van de gegevens in de tekst bestanden op te geven. De contoso-gegevens worden niet gecomprimeerd en door pipes gescheiden.

```sql
CREATE EXTERNAL FILE FORMAT TextFileFormat
WITH
(   FORMAT_TYPE = DELIMITEDTEXT
,    FORMAT_OPTIONS    (   FIELD_TERMINATOR = '|'
                    ,    STRING_DELIMITER = ''
                    ,    DATE_FORMAT         = 'yyyy-MM-dd HH:mm:ss.fff'
                    ,    USE_TYPE_DEFAULT = FALSE
                    )
);
```

## <a name="create-the-schema-for-the-external-tables"></a>Het schema voor de externe tabellen maken

Nu u de gegevens bron en bestands indeling hebt opgegeven, bent u klaar om het schema voor de externe tabellen te maken.

Maak een schema om een locatie te maken voor het opslaan van de contoso-gegevens in uw data base.

```sql
CREATE SCHEMA [asb]
GO
```

## <a name="create-the-external-tables"></a>De externe tabellen maken

Voer het volgende script uit om de externe tabellen DimProduct en FactOnlineSales te maken. U hoeft hier geen kolom namen en gegevens typen te definiëren en deze te binden aan de locatie en indeling van de Azure Blob-opslag bestanden. De definitie wordt opgeslagen in het Data Warehouse en de gegevens bevinden zich nog in de Azure Storage Blob.

De  **locatie** parameter is de map onder de hoofdmap in de Azure Storage blob. Elke tabel bevindt zich in een andere map.

```sql
--DimProduct
CREATE EXTERNAL TABLE [asb].DimProduct (
    [ProductKey] [int] NOT NULL,
    [ProductLabel] [nvarchar](255) NULL,
    [ProductName] [nvarchar](500) NULL,
    [ProductDescription] [nvarchar](400) NULL,
    [ProductSubcategoryKey] [int] NULL,
    [Manufacturer] [nvarchar](50) NULL,
    [BrandName] [nvarchar](50) NULL,
    [ClassID] [nvarchar](10) NULL,
    [ClassName] [nvarchar](20) NULL,
    [StyleID] [nvarchar](10) NULL,
    [StyleName] [nvarchar](20) NULL,
    [ColorID] [nvarchar](10) NULL,
    [ColorName] [nvarchar](20) NOT NULL,
    [Size] [nvarchar](50) NULL,
    [SizeRange] [nvarchar](50) NULL,
    [SizeUnitMeasureID] [nvarchar](20) NULL,
    [Weight] [float] NULL,
    [WeightUnitMeasureID] [nvarchar](20) NULL,
    [UnitOfMeasureID] [nvarchar](10) NULL,
    [UnitOfMeasureName] [nvarchar](40) NULL,
    [StockTypeID] [nvarchar](10) NULL,
    [StockTypeName] [nvarchar](40) NULL,
    [UnitCost] [money] NULL,
    [UnitPrice] [money] NULL,
    [AvailableForSaleDate] [datetime] NULL,
    [StopSaleDate] [datetime] NULL,
    [Status] [nvarchar](7) NULL,
    [ImageURL] [nvarchar](150) NULL,
    [ProductURL] [nvarchar](150) NULL,
    [ETLLoadID] [int] NULL,
    [LoadDate] [datetime] NULL,
    [UpdateDate] [datetime] NULL
)
WITH
(
    LOCATION='/DimProduct/'
,   DATA_SOURCE = AzureStorage_west_public
,   FILE_FORMAT = TextFileFormat
,   REJECT_TYPE = VALUE
,   REJECT_VALUE = 0
)
;

--FactOnlineSales
CREATE EXTERNAL TABLE [asb].FactOnlineSales
(
    [OnlineSalesKey] [int]  NOT NULL,
    [DateKey] [datetime] NOT NULL,
    [StoreKey] [int] NOT NULL,
    [ProductKey] [int] NOT NULL,
    [PromotionKey] [int] NOT NULL,
    [CurrencyKey] [int] NOT NULL,
    [CustomerKey] [int] NOT NULL,
    [SalesOrderNumber] [nvarchar](20) NOT NULL,
    [SalesOrderLineNumber] [int] NULL,
    [SalesQuantity] [int] NOT NULL,
    [SalesAmount] [money] NOT NULL,
    [ReturnQuantity] [int] NOT NULL,
    [ReturnAmount] [money] NULL,
    [DiscountQuantity] [int] NULL,
    [DiscountAmount] [money] NULL,
    [TotalCost] [money] NOT NULL,
    [UnitCost] [money] NULL,
    [UnitPrice] [money] NULL,
    [ETLLoadID] [int] NULL,
    [LoadDate] [datetime] NULL,
    [UpdateDate] [datetime] NULL
)
WITH
(
    LOCATION='/FactOnlineSales/'
,   DATA_SOURCE = AzureStorage_west_public
,   FILE_FORMAT = TextFileFormat
,   REJECT_TYPE = VALUE
,   REJECT_VALUE = 0
)
;
```

## <a name="load-the-data"></a>De gegevens laden

Er zijn verschillende manieren om toegang te krijgen tot externe gegevens.  U kunt gegevens rechtstreeks vanuit de externe tabellen opvragen, de gegevens in nieuwe tabellen in het Data Warehouse laden of externe gegevens toevoegen aan bestaande Data Warehouse-tabellen.  

### <a name="create-a-new-schema"></a>Een nieuw schema maken

CTAS maakt een nieuwe tabel die gegevens bevat.  Maak eerst een schema voor de contoso-gegevens.

```sql
CREATE SCHEMA [cso]
GO
```

### <a name="load-the-data-into-new-tables"></a>De gegevens in nieuwe tabellen laden

Als u gegevens uit Azure Blob-opslag wilt laden in de Data Warehouse-tabel, gebruikt u de instructie [create table as select (Transact-SQL)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) . Het laden met [CTAS](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) maakt gebruik van de sterk getypeerde externe tabellen die u hebt gemaakt. Als u de gegevens in nieuwe tabellen wilt laden, gebruikt u een CTAS-instructie per tabel.

CTAS maakt een nieuwe tabel en vult deze met de resultaten van een SELECT-instructie. CTAS definieert de nieuwe tabel om dezelfde kolommen en gegevens typen te hebben als de resultaten van de SELECT-instructie. Als u alle kolommen uit een externe tabel selecteert, is de nieuwe tabel een replica van de kolommen en gegevens typen in de externe tabel.

In dit voor beeld maken we zowel de dimensie als de feiten tabel als gedistribueerde hash-tabellen.

```sql
SELECT GETDATE();
GO

CREATE TABLE [cso].[DimProduct]            WITH (DISTRIBUTION = HASH([ProductKey]  ) ) AS SELECT * FROM [asb].[DimProduct]             OPTION (LABEL = 'CTAS : Load [cso].[DimProduct]             ');
CREATE TABLE [cso].[FactOnlineSales]       WITH (DISTRIBUTION = HASH([ProductKey]  ) ) AS SELECT * FROM [asb].[FactOnlineSales]        OPTION (LABEL = 'CTAS : Load [cso].[FactOnlineSales]        ');
```

### <a name="track-the-load-progress"></a>De voortgang van het laden volgen

U kunt de voortgang van het laden volgen met dynamische beheer weergaven (Dmv's).

```sql
-- To see all requests
SELECT * FROM sys.dm_pdw_exec_requests;

-- To see a particular request identified by its label
SELECT * FROM sys.dm_pdw_exec_requests as r
WHERE r.[label] = 'CTAS : Load [cso].[DimProduct]             '
      OR r.[label] = 'CTAS : Load [cso].[FactOnlineSales]        '
;

-- To track bytes and files
SELECT
    r.command,
    s.request_id,
    r.status,
    count(distinct input_name) as nbr_files,
    sum(s.bytes_processed)/1024/1024/1024 as gb_processed
FROM
    sys.dm_pdw_exec_requests r
    inner join sys.dm_pdw_dms_external_work s
        on r.request_id = s.request_id
WHERE
    r.[label] = 'CTAS : Load [cso].[DimProduct]             '
    OR r.[label] = 'CTAS : Load [cso].[FactOnlineSales]        '
GROUP BY
    r.command,
    s.request_id,
    r.status
ORDER BY
    nbr_files desc,
    gb_processed desc;
```

## <a name="optimize-columnstore-compression"></a>Column Store-compressie optimaliseren

Standaard worden in toegewezen SQL-groepen de tabel opgeslagen als een geclusterde column store-index. Nadat het laden is voltooid, zijn sommige gegevens rijen mogelijk niet gecomprimeerd naar de column Store.  Er zijn verschillende redenen waarom dit kan gebeuren. Zie [Column Store-indexen beheren](sql-data-warehouse-tables-index.md)voor meer informatie.

Als u de query prestaties en column Store-compressie wilt optimaliseren na een laad opdracht, bouwt u de tabel opnieuw op om ervoor te zorgen dat de column store-index alle rijen kan comprimeren.

```sql
SELECT GETDATE();
GO

ALTER INDEX ALL ON [cso].[DimProduct]               REBUILD;
ALTER INDEX ALL ON [cso].[FactOnlineSales]          REBUILD;
```

Zie het artikel [Column Store-indexen beheren](sql-data-warehouse-tables-index.md) voor meer informatie over het onderhouden van Column Store-indexen.

## <a name="optimize-statistics"></a>Statistieken optimaliseren

Het is het beste om statistieken voor één kolom direct na een belasting te maken. Als u weet dat bepaalde kolommen niet in query predikaten staan, kunt u het maken van statistieken voor die kolommen overs Laan. Als u een statistieken voor één kolom maakt voor elke kolom, kan het lang duren om alle statistieken opnieuw samen te stellen.

Als u besluit om met één kolom statistieken te maken voor elke kolom van elke tabel, kunt u het voor beeld van de opgeslagen procedure code `prc_sqldw_create_stats` in het [statistiek](sql-data-warehouse-tables-statistics.md) artikel gebruiken.

Het volgende voor beeld is een goed uitgangs punt voor het maken van statistieken. Er worden statistieken voor één kolom gemaakt voor elke kolom in de dimensie tabel en voor elke join-kolom in de feiten tabellen. U kunt later altijd statistieken met één of meerdere kolommen toevoegen aan andere feiten tabel kolommen.

```sql
CREATE STATISTICS [stat_cso_DimProduct_AvailableForSaleDate] ON [cso].[DimProduct]([AvailableForSaleDate]);
CREATE STATISTICS [stat_cso_DimProduct_BrandName] ON [cso].[DimProduct]([BrandName]);
CREATE STATISTICS [stat_cso_DimProduct_ClassID] ON [cso].[DimProduct]([ClassID]);
CREATE STATISTICS [stat_cso_DimProduct_ClassName] ON [cso].[DimProduct]([ClassName]);
CREATE STATISTICS [stat_cso_DimProduct_ColorID] ON [cso].[DimProduct]([ColorID]);
CREATE STATISTICS [stat_cso_DimProduct_ColorName] ON [cso].[DimProduct]([ColorName]);
CREATE STATISTICS [stat_cso_DimProduct_ETLLoadID] ON [cso].[DimProduct]([ETLLoadID]);
CREATE STATISTICS [stat_cso_DimProduct_ImageURL] ON [cso].[DimProduct]([ImageURL]);
CREATE STATISTICS [stat_cso_DimProduct_LoadDate] ON [cso].[DimProduct]([LoadDate]);
CREATE STATISTICS [stat_cso_DimProduct_Manufacturer] ON [cso].[DimProduct]([Manufacturer]);
CREATE STATISTICS [stat_cso_DimProduct_ProductDescription] ON [cso].[DimProduct]([ProductDescription]);
CREATE STATISTICS [stat_cso_DimProduct_ProductKey] ON [cso].[DimProduct]([ProductKey]);
CREATE STATISTICS [stat_cso_DimProduct_ProductLabel] ON [cso].[DimProduct]([ProductLabel]);
CREATE STATISTICS [stat_cso_DimProduct_ProductName] ON [cso].[DimProduct]([ProductName]);
CREATE STATISTICS [stat_cso_DimProduct_ProductSubcategoryKey] ON [cso].[DimProduct]([ProductSubcategoryKey]);
CREATE STATISTICS [stat_cso_DimProduct_ProductURL] ON [cso].[DimProduct]([ProductURL]);
CREATE STATISTICS [stat_cso_DimProduct_Size] ON [cso].[DimProduct]([Size]);
CREATE STATISTICS [stat_cso_DimProduct_SizeRange] ON [cso].[DimProduct]([SizeRange]);
CREATE STATISTICS [stat_cso_DimProduct_SizeUnitMeasureID] ON [cso].[DimProduct]([SizeUnitMeasureID]);
CREATE STATISTICS [stat_cso_DimProduct_Status] ON [cso].[DimProduct]([Status]);
CREATE STATISTICS [stat_cso_DimProduct_StockTypeID] ON [cso].[DimProduct]([StockTypeID]);
CREATE STATISTICS [stat_cso_DimProduct_StockTypeName] ON [cso].[DimProduct]([StockTypeName]);
CREATE STATISTICS [stat_cso_DimProduct_StopSaleDate] ON [cso].[DimProduct]([StopSaleDate]);
CREATE STATISTICS [stat_cso_DimProduct_StyleID] ON [cso].[DimProduct]([StyleID]);
CREATE STATISTICS [stat_cso_DimProduct_StyleName] ON [cso].[DimProduct]([StyleName]);
CREATE STATISTICS [stat_cso_DimProduct_UnitCost] ON [cso].[DimProduct]([UnitCost]);
CREATE STATISTICS [stat_cso_DimProduct_UnitOfMeasureID] ON [cso].[DimProduct]([UnitOfMeasureID]);
CREATE STATISTICS [stat_cso_DimProduct_UnitOfMeasureName] ON [cso].[DimProduct]([UnitOfMeasureName]);
CREATE STATISTICS [stat_cso_DimProduct_UnitPrice] ON [cso].[DimProduct]([UnitPrice]);
CREATE STATISTICS [stat_cso_DimProduct_UpdateDate] ON [cso].[DimProduct]([UpdateDate]);
CREATE STATISTICS [stat_cso_DimProduct_Weight] ON [cso].[DimProduct]([Weight]);
CREATE STATISTICS [stat_cso_DimProduct_WeightUnitMeasureID] ON [cso].[DimProduct]([WeightUnitMeasureID]);
CREATE STATISTICS [stat_cso_FactOnlineSales_CurrencyKey] ON [cso].[FactOnlineSales]([CurrencyKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_CustomerKey] ON [cso].[FactOnlineSales]([CustomerKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_DateKey] ON [cso].[FactOnlineSales]([DateKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_OnlineSalesKey] ON [cso].[FactOnlineSales]([OnlineSalesKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_ProductKey] ON [cso].[FactOnlineSales]([ProductKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_PromotionKey] ON [cso].[FactOnlineSales]([PromotionKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_StoreKey] ON [cso].[FactOnlineSales]([StoreKey]);
```

## <a name="achievement-unlocked"></a>Prestaties vergren delen.

U hebt open bare gegevens in uw data warehouse geladen. Helemaal goed!

U kunt nu een query uitvoeren op de tabellen om uw gegevens te verkennen. Voer de volgende query uit om de totale verkoop per merk te vinden:

```sql
SELECT  SUM(f.[SalesAmount]) AS [sales_by_brand_amount]
,       p.[BrandName]
FROM    [cso].[FactOnlineSales] AS f
JOIN    [cso].[DimProduct]      AS p ON f.[ProductKey] = p.[ProductKey]
GROUP BY p.[BrandName]
```

## <a name="next-steps"></a>Volgende stappen

Als u de volledige gegevensset wilt laden, voert u het voor beeld [laden van het volledige contoso Retail Data Warehouse](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/contoso-data-warehouse/readme.md) vanuit de opslag plaats Microsoft SQL Server samples.
Zie [ontwerp beslissingen en coderings technieken voor data warehouses](sql-data-warehouse-overview-develop.md)voor meer tips voor ontwikkel aars.
