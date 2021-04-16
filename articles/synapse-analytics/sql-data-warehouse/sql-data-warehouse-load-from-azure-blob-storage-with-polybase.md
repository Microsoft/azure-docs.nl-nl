---
title: Contoso-retailgegevens laden in toegewezen SQL-pools
description: Gebruik PolyBase- en T-SQL-opdrachten om twee tabellen uit de Contoso-retailgegevens in toegewezen SQL-pools te laden.
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/20/2020
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: b1afcdfa74245eb566663d5dec6ce2e2276fbdc8
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107568230"
---
# <a name="load-contoso-retail-data-into-dedicated-sql-pools-in-azure-synapse-analytics"></a>Contoso-retailgegevens laden in toegewezen SQL-pools in Azure Synapse Analytics

In deze zelfstudie leert u hoe u PolyBase- en T-SQL-opdrachten gebruikt om twee tabellen uit de Contoso-retailgegevens te laden in toegewezen SQL-pools.

In deze zelfstudie gaat u:

1. PolyBase configureren om te laden vanuit Azure Blob Storage
2. Openbare gegevens laden in uw database
3. Optimalisaties uitvoeren nadat het laden is voltooid.

## <a name="before-you-begin"></a>Voordat u begint

Als u deze zelfstudie wilt uitvoeren, hebt u een Azure-account nodig dat al een toegewezen SQL-pool heeft. Zie Een datawarehouse maken en firewallregel op serverniveau instellen als u geen datawarehouse [hebt ingericht.](create-data-warehouse-portal.md)

## <a name="configure-the-data-source"></a>De gegevensbron configureren

PolyBase maakt gebruik van externe T-SQL-objecten om de locatie en kenmerken van de externe gegevens te definiëren. De externe objectdefinities worden opgeslagen in toegewezen SQL-pools. De gegevens worden extern opgeslagen.

## <a name="create-a-credential"></a>Een referentie maken

**Sla deze stap** over als u de openbare gegevens van Contoso laadt. U hebt geen beveiligde toegang tot de openbare gegevens nodig omdat deze al toegankelijk zijn voor iedereen.

**Sla deze stap niet over als** u deze zelfstudie gebruikt als sjabloon voor het laden van uw eigen gegevens. Als u toegang wilt krijgen tot gegevens via een referentie, gebruikt u het volgende script om een referentie binnen databasebereik te maken. Gebruik deze vervolgens bij het definiëren van de locatie van de gegevensbron.

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

## <a name="create-the-external-data-source"></a>De externe gegevensbron maken

Gebruik deze [opdracht CREATE EXTERNAL DATA SOURCE om](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) de locatie van de gegevens en het gegevenstype op te slaan.

```sql
CREATE EXTERNAL DATA SOURCE AzureStorage_west_public
WITH
(  
    TYPE = Hadoop
,   LOCATION = 'wasbs://contosoretaildw-tables@contosoretaildw.blob.core.windows.net/'
);
```

> [!IMPORTANT]
> Als u ervoor kiest om uw Azure Blob Storage-containers openbaar te maken, houd er dan rekening mee dat er als gegevenseigenaar kosten in rekening worden gebracht voor het uit de gegevens verwijderen van gegevens wanneer gegevens het datacenter verlaat.

## <a name="configure-the-data-format"></a>De gegevensindeling configureren

De gegevens worden opgeslagen in tekstbestanden in Azure Blob Storage en elk veld wordt gescheiden door een scheidingsteken. Voer in SSMS de volgende opdracht CREATE EXTERNAL FILE FORMAT uit om de indeling van de gegevens in de tekstbestanden op te geven. De Contoso-gegevens zijn gedecomprimeerd en worden door een spipe van scheidingstekens verwijderd.

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

Nu u de gegevensbron en bestandsindeling hebt opgegeven, kunt u het schema voor de externe tabellen maken.

Als u een plaats wilt maken om de Contoso-gegevens in uw database op te slaan, maakt u een schema.

```sql
CREATE SCHEMA [asb]
GO
```

## <a name="create-the-external-tables"></a>De externe tabellen maken

Voer het volgende script uit om de externe tabellen DimProduct en FactOnlineSales te maken. Het enige wat u hier doet, is kolomnamen en gegevenstypen definiëren en deze binden aan de locatie en indeling van de Azure Blob Storage-bestanden. De definitie wordt opgeslagen in het datawarehouse en de gegevens staan nog in de Azure Storage Blob.

De  **parameter LOCATION** is de map onder de hoofdmap in de Azure Storage Blob. Elke tabel staat in een andere map.

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

Er zijn verschillende manieren om toegang te krijgen tot externe gegevens.  U kunt gegevens rechtstreeks vanuit de externe tabellen opvragen, de gegevens in nieuwe tabellen in het datawarehouse laden of externe gegevens toevoegen aan bestaande datawarehouse-tabellen.  

### <a name="create-a-new-schema"></a>Een nieuw schema maken

CTAS maakt een nieuwe tabel die gegevens bevat.  Maak eerst een schema voor de contoso-gegevens.

```sql
CREATE SCHEMA [cso]
GO
```

### <a name="load-the-data-into-new-tables"></a>De gegevens in nieuwe tabellen laden

Als u gegevens uit Azure Blob Storage wilt laden in de datawarehouse-tabel, gebruikt u [de instructie CREATE TABLE AS SELECT (Transact-SQL).](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) Laden met [CTAS maakt](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) gebruik van de sterk getypeerd externe tabellen die u hebt gemaakt. Als u de gegevens in nieuwe tabellen wilt laden, gebruikt u één CTAS-instructie per tabel.

CTAS maakt een nieuwe tabel en vult deze met de resultaten van een select-instructie. CTAS definieert de nieuwe tabel met dezelfde kolommen en gegevenstypen als de resultaten van de select-instructie. Als u alle kolommen uit een externe tabel selecteert, wordt de nieuwe tabel een replica van de kolommen en gegevenstypen in de externe tabel.

In dit voorbeeld maken we zowel de dimensietabel als de feitentabel als gedistribueerde hashtabellen.

```sql
SELECT GETDATE();
GO

CREATE TABLE [cso].[DimProduct]            WITH (DISTRIBUTION = HASH([ProductKey]  ) ) AS SELECT * FROM [asb].[DimProduct]             OPTION (LABEL = 'CTAS : Load [cso].[DimProduct]             ');
CREATE TABLE [cso].[FactOnlineSales]       WITH (DISTRIBUTION = HASH([ProductKey]  ) ) AS SELECT * FROM [asb].[FactOnlineSales]        OPTION (LABEL = 'CTAS : Load [cso].[FactOnlineSales]        ');
```

### <a name="track-the-load-progress"></a>De voortgang van het laden bijhouden

U kunt de voortgang van uw belasting bijhouden met dynamische beheerweergaven (DMV's).

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

## <a name="optimize-columnstore-compression"></a>Columnstore-compressie optimaliseren

Toegewezen SQL-pools slaan de tabel standaard op als een geclusterde columnstore-index. Nadat het laden is voltooid, worden sommige gegevensrijen mogelijk niet gecomprimeerd in de columnstore.  Er zijn verschillende redenen waarom dit kan gebeuren. Zie [Columnstore-indexen](sql-data-warehouse-tables-index.md)beheren voor meer informatie.

Als u de queryprestaties en columnstore-compressie na het laden wilt optimaliseren, moet u de tabel opnieuw opbouwen om de columnstore-index te dwingen alle rijen te comprimeren.

```sql
SELECT GETDATE();
GO

ALTER INDEX ALL ON [cso].[DimProduct]               REBUILD;
ALTER INDEX ALL ON [cso].[FactOnlineSales]          REBUILD;
```

Zie het artikel [columnstore-indexen](sql-data-warehouse-tables-index.md) beheren voor meer informatie over het onderhouden van columnstore-indexen.

## <a name="optimize-statistics"></a>Statistieken optimaliseren

Het is het beste om statistieken met één kolom direct na het laden te maken. Als u weet dat bepaalde kolommen geen querypredicaten zullen hebben, kunt u het maken van statistieken voor deze kolommen overslaan. Als u statistieken met één kolom maakt voor elke kolom, kan het lang duren om alle statistieken opnieuw samen te bouwen.

Als u besluit statistieken met één kolom te maken voor elke kolom van elke tabel, kunt u het opgeslagen codevoorbeeld voor de procedure `prc_sqldw_create_stats` gebruiken in het artikel [statistieken.](sql-data-warehouse-tables-statistics.md)

Het volgende voorbeeld is een goed uitgangspunt voor het maken van statistieken. Er worden statistieken met één kolom gemaakt voor elke kolom in de dimensietabel en voor elke samenbundelende kolom in de feitentabellen. U kunt later altijd statistieken met één of meerdere kolommen toevoegen aan andere feitentabelkolommen.

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

## <a name="achievement-unlocked"></a>Prestatie ontgrendeld!

U hebt openbare gegevens geladen in uw datawarehouse. Helemaal goed!

U kunt nu beginnen met het uitvoeren van query's op de tabellen om uw gegevens te verkennen. Voer de volgende query uit om de totale verkoop per merk te vinden:

```sql
SELECT  SUM(f.[SalesAmount]) AS [sales_by_brand_amount]
,       p.[BrandName]
FROM    [cso].[FactOnlineSales] AS f
JOIN    [cso].[DimProduct]      AS p ON f.[ProductKey] = p.[ProductKey]
GROUP BY p.[BrandName]
```

## <a name="next-steps"></a>Volgende stappen

Als u de volledige gegevensset wilt laden, laadt u in het voorbeeld het volledige [Contoso-datawarehouse](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/contoso-data-warehouse/readme.md) uit de opslagplaats Microsoft SQL Server voorbeelden.
Zie Ontwerpbeslissingen en coderingstechnieken voor [datawarehouses](sql-data-warehouse-overview-develop.md)voor meer tips voor ontwikkeling.
