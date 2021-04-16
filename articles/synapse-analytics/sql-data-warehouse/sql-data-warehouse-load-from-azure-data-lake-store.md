---
title: 'Zelfstudie: gegevens uit Azure Data Lake Storage'
description: Gebruik de INSTRUCTIE COPY om gegevens te laden uit Azure Data Lake Storage voor toegewezen SQL-pools.
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/20/2020
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 16f95a86169be04eba202b311fc4437b204ec8b3
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107566525"
---
# <a name="load-data-from-azure-data-lake-storage-into-dedicated-sql-pools-in-azure-synapse-analytics"></a>Gegevens uit Azure Data Lake Storage in toegewezen SQL-pools in Azure Synapse Analytics

In deze handleiding wordt beschreven hoe u de [copy-instructie gebruikt](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true) om gegevens uit uw Azure Data Lake Storage. Voor snelle voorbeelden van het gebruik van de COPY-instructie voor alle verificatiemethoden, gaat u naar de volgende documentatie: Gegevens veilig laden [met behulp van toegewezen SQL-pools.](./quickstart-bulk-load-copy-tsql-examples.md)

> [!NOTE]  
> Als u feedback wilt geven of problemen wilt melden met de COPY-instructie, stuurt u een e-mail naar de volgende distributielijst: sqldwcopypreview@service.microsoft.com .
>
> [!div class="checklist"]
>
> * Maak de doeltabel om gegevens uit de Azure Data Lake Storage.
> * Maak de INSTRUCTIE COPY om gegevens in het datawarehouse te laden.

Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="before-you-begin"></a>Voordat u begint

Download en installeer voordat u met deze zelfstudie begint de nieuwste versie van [SSMS](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) (SQL Server Management Studio).

U hebt het volgende nodig om deze zelfstudie te volgen:

* Een toegewezen SQL-pool. Zie [Een toegewezen SQL-pool maken en gegevens opvragen.](create-data-warehouse-portal.md)
* Een Data Lake Storage account. Zie [Aan de slag met Azure Data Lake Storage](../../data-lake-store/data-lake-store-get-started-portal.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). Voor dit opslagaccount moet u een van de volgende referenties configureren of opgeven om te laden: een opslagaccountsleutel, SAS-sleutel (Shared Access Signature), een azure directorytoepassingsgebruiker of een AAD-gebruiker met de juiste Azure-rol voor het opslagaccount.

## <a name="create-the-target-table"></a>De doeltabel maken

Maak verbinding met uw toegewezen SQL-pool en maak de doeltabel waar u naar wilt laden. In dit voorbeeld maken we een productdimenssietabel.

```sql
-- A: Create the target table
-- DimProduct
CREATE TABLE [dbo].[DimProduct]
(
    [ProductKey] [int] NOT NULL,
    [ProductLabel] [nvarchar](255) NULL,
    [ProductName] [nvarchar](500) NULL
)
WITH
(
    DISTRIBUTION = HASH([ProductKey]),
    CLUSTERED COLUMNSTORE INDEX
    --HEAP
);
```


## <a name="create-the-copy-statement"></a>De COPY-instructie maken

Maak verbinding met uw toegewezen SQL-pool en voer de COPY-instructie uit. Ga naar de volgende documentatie voor een volledige lijst met voorbeelden: [Gegevens veilig laden met behulp van toegewezen SQL-pools.](./quickstart-bulk-load-copy-tsql-examples.md)

```sql
-- B: Create and execute the COPY statement

COPY INTO [dbo].[DimProduct] 
--The column list allows you map, omit, or reorder input file columns to target table columns. 
--You can also specify the default value when there is a NULL value in the file.
--When the column list is not specified, columns will be mapped based on source and target ordinality
(
    ProductKey default -1 1,
    ProductLabel default 'myStringDefaultWhenNull' 2,
    ProductName default 'myStringDefaultWhenNull' 3
)
--The storage account location where you data is staged
FROM 'https://storageaccount.blob.core.windows.net/container/directory/'
WITH 
(
   --CREDENTIAL: Specifies the authentication method and credential access your storage account
   CREDENTIAL = (IDENTITY = '', SECRET = ''),
   --FILE_TYPE: Specifies the file type in your storage account location
   FILE_TYPE = 'CSV',
   --FIELD_TERMINATOR: Marks the end of each field (column) in a delimited text (CSV) file
   FIELDTERMINATOR = '|',
   --ROWTERMINATOR: Marks the end of a record in the file
   ROWTERMINATOR = '0x0A',
   --FIELDQUOTE: Specifies the delimiter for data of type string in a delimited text (CSV) file
   FIELDQUOTE = '',
   ENCODING = 'UTF8',
   DATEFORMAT = 'ymd',
   --MAXERRORS: Maximum number of reject rows allowed in the load before the COPY operation is canceled
   MAXERRORS = 10,
   --ERRORFILE: Specifies the directory where the rejected rows and the corresponding error reason should be written
   ERRORFILE = '/errorsfolder',
) OPTION (LABEL = 'COPY: ADLS tutorial');
```

## <a name="optimize-columnstore-compression"></a>Columnstore-compressie optimaliseren

Tabellen worden standaard gedefinieerd als een geclusterde columnstore-index. Nadat het laden is voltooid, worden sommige gegevensrijen mogelijk niet gecomprimeerd in de columnstore.  Er zijn verschillende redenen waarom dit kan gebeuren. Zie [Columnstore-indexen](sql-data-warehouse-tables-index.md)beheren voor meer informatie.

Als u de queryprestaties en columnstore-compressie na het laden wilt optimaliseren, moet u de tabel opnieuw opbouwen om de columnstore-index te dwingen alle rijen te comprimeren.

```sql

ALTER INDEX ALL ON [dbo].[DimProduct] REBUILD;

```

## <a name="optimize-statistics"></a>Statistieken optimaliseren

Het is het beste om statistieken met één kolom direct na het laden te maken. Er zijn enkele opties voor statistieken. Als u bijvoorbeeld statistieken met één kolom maakt voor elke kolom, kan het lang duren om alle statistieken opnieuw samen te stellen. Als u weet dat bepaalde kolommen niet in querypredicaten worden opgenomen, kunt u het maken van statistieken voor deze kolommen overslaan.

Als u besluit statistieken met één kolom te maken voor elke kolom van elke tabel, kunt u het opgeslagen codevoorbeeld voor de procedure `prc_sqldw_create_stats` gebruiken in het artikel [statistieken.](sql-data-warehouse-tables-statistics.md)

Het volgende voorbeeld is een goed uitgangspunt voor het maken van statistieken. Er worden statistieken met één kolom gemaakt voor elke kolom in de dimensietabel en voor elke samenbundelende kolom in de feitentabellen. U kunt later altijd statistieken met één of meerdere kolommen toevoegen aan andere feitentabelkolommen.

## <a name="achievement-unlocked"></a>Prestatie ontgrendeld!

U hebt gegevens in uw datawarehouse geladen. Helemaal goed!

## <a name="next-steps"></a>Volgende stappen
Het laden van gegevens is de eerste stap bij het ontwikkelen van een datawarehouseoplossing met behulp Azure Synapse Analytics. Bekijk onze ontwikkelingsbronnen.

> [!div class="nextstepaction"]
> [Meer informatie over het ontwikkelen van tabellen voor datawarehousing](sql-data-warehouse-tables-overview.md)

Bekijk de volgende documentatie voor meer laadvoorbeelden en -verwijzingen:
- [Referentiedocumentatie voor COPY-instructie](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true#syntax)
- [COPY-voorbeelden voor elke verificatiemethode](./quickstart-bulk-load-copy-tsql-examples.md)
- [Snelstart copy voor één tabel](./quickstart-bulk-load-copy-tsql.md)