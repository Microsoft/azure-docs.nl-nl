---
title: In plaats van ETL ontwerpt u ELT
description: Implementeert flexibele strategieën voor het laden van gegevens voor toegewezen SQL-pools binnen Azure Synapse Analytics.
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
ms.openlocfilehash: 8a8f857dcfdc271a3aaad71f4b9c26d474033383
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107566100"
---
# <a name="data-loading-strategies-for-dedicated-sql-pool-in-azure-synapse-analytics"></a>Strategieën voor het laden van gegevens voor toegewezen SQL-pool in Azure Synapse Analytics

Traditionele SMP toegewezen SQL-pools gebruiken een ETL-proces (Extract, Transform, and Load) voor het laden van gegevens. Synapse SQL maakt binnen Azure Synapse Analytics gebruik van een gedistribueerde queryverwerkingsarchitectuur die gebruikmaakt van de schaalbaarheid en flexibiliteit van reken- en opslagbronnen.

Met behulp van een ELT-proces (Extraheren, laden en transformeren) worden ingebouwde mogelijkheden voor gedistribueerde queryverwerking gebruikt en worden de resources geëlimineerd die nodig zijn voor gegevenstransformatie vóór het laden.

Hoewel toegewezen SQL-pools ondersteuning bieden voor veel laadmethoden, waaronder populaire SQL Server-opties zoals [bcp](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) en de [SqlBulkCopy-API,](/dotnet/api/system.data.sqlclient.sqlbulkcopy?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)is de snelste en meest schaalbare manier om gegevens te laden via externe PolyBase-tabellen en de [COPY-instructie](/sql/t-sql/statements/copy-into-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

Met PolyBase en de COPY-instructie hebt u toegang tot externe gegevens die zijn opgeslagen in Azure Blob Storage of Azure Data Lake Store via de T-SQL-taal. Voor de meeste flexibiliteit bij het laden wordt u aangeraden de copy-instructie te gebruiken.


## <a name="what-is-elt"></a>Wat is ELT?

Extraheren, laden en transformeren (ELT) is een proces waarmee gegevens worden geëxtraheerd uit een bronsysteem, in een toegewezen SQL-pool worden geladen en vervolgens getransformeerd.

De basisstappen voor het implementeren van ELT zijn:

1. Extraheer de brongegevens naar tekstbestanden.
2. De gegevens in Azure Blob Storage of Azure Data Lake Store.
3. Bereid de gegevens voor op het laden.
4. Laad de gegevens in faseringstabellen met PolyBase of de opdracht COPY.
5. De gegevens transformeren.
6. Voeg de gegevens in productietabellen in.

Zie Gegevens laden vanuit Azure Blob Storage voor een zelfstudie [over het laden.](./load-data-from-azure-blob-storage-using-copy.md)

## <a name="1-extract-the-source-data-into-text-files"></a>1. De brongegevens uitpakken in tekstbestanden

Het verwijderen van gegevens uit uw bronsysteem is afhankelijk van de opslaglocatie. Het doel is om de gegevens te verplaatsen naar ondersteunde tekst- of CSV-bestanden met scheidingstekens.

### <a name="supported-file-formats"></a>Ondersteunde bestandsindelingen

Met PolyBase en de COPY-instructie kunt u gegevens laden uit UTF-8- en UTF-16-gecodeerde tekst- of CSV-bestanden. Naast tekst- of CSV-bestanden met scheidingstekens worden deze geladen vanuit de Hadoop-bestandsindelingen zoals ORC en Parquet. PolyBase en de COPY-instructie kunnen ook gegevens laden uit gecomprimeerde Gzip- en Snappy-bestanden.

Uitgebreide ASCII, indeling met vaste breedte en geneste indelingen, zoals WinZip of XML, worden niet ondersteund. Als u exporteert vanuit SQL Server, kunt u het [opdrachtregelprogramma bcp](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) gebruiken om de gegevens te exporteren naar tekstbestanden met scheidingstekens.

## <a name="2-land-the-data-into-azure-blob-storage-or-azure-data-lake-store"></a>2. De gegevens in Azure Blob Storage of Azure Data Lake Store

Als u de gegevens in Azure Storage wilt opslaan, kunt u deze verplaatsen naar [Azure Blob Storage](../../storage/blobs/storage-blobs-introduction.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) of Azure Data Lake Store [Gen2.](../../data-lake-store/data-lake-store-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) Op beide locaties moeten de gegevens worden opgeslagen in tekstbestanden. PolyBase en de COPY-instructie kunnen vanaf beide locaties worden geladen.

Hulpprogramma's en services die u kunt gebruiken om gegevens te verplaatsen naar Azure Storage:

- [Azure ExpressRoute-service](../../expressroute/expressroute-introduction.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) verbetert de netwerkdoorvoer, prestaties en voorspelbaarheid. ExpressRoute is een service die uw gegevens routeert via een toegewezen privéverbinding naar Azure. ExpressRoute-verbindingen leiden geen gegevens door via het openbare internet. De verbindingen bieden meer betrouwbaarheid, hogere snelheden, lagere latentie en hogere beveiliging dan gewone verbindingen via het openbare internet.
- [Het AZCopy-hulpprogramma](../../storage/common/storage-choose-data-transfer-solution.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) verplaatst gegevens naar Azure Storage via het openbare internet. Dit werkt als uw gegevens minder dan 10 TB groot zijn. Als u regelmatig belasting wilt uitvoeren met AZCopy, test u de netwerksnelheid om te zien of dit acceptabel is.
- [Azure Data Factory (ADF) heeft](../../data-factory/introduction.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) een gateway die u op uw lokale server kunt installeren. Vervolgens kunt u een pijplijn maken om gegevens van uw lokale server naar een Azure Storage. Zie Gegevens laden Data Factory toegewezen SQL-pools voor meer informatie over het gebruik van Data Factory [toegewezen SQL-pools.](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)

## <a name="3-prepare-the-data-for-loading"></a>3. De gegevens voorbereiden voor het laden

Mogelijk moet u de gegevens in uw opslagaccount voorbereiden en ops schonen voordat u ze laadt. Gegevensvoorbereiding kan worden uitgevoerd terwijl uw gegevens zich in de bron, tijdens het exporteren van de gegevens naar tekstbestanden of nadat de gegevens zich in de bron Azure Storage.  Het is het gemakkelijkst om zo vroeg mogelijk in het proces met de gegevens te werken.  

### <a name="define-the-tables"></a>De tabellen definiëren

U moet eerst de tabel(en) definiëren waarin u laadt in uw toegewezen SQL-pool wanneer u de INSTRUCTIE COPY gebruikt.

Als u PolyBase gebruikt, moet u externe tabellen in uw toegewezen SQL-pool definiëren voordat u deze laadt. PolyBase maakt gebruik van externe tabellen voor het definiëren en openen van de gegevens in Azure Storage. Een externe tabel is vergelijkbaar met een databaseweergave. De externe tabel bevat het tabelschema en wijst naar gegevens die buiten de toegewezen SQL-pool zijn opgeslagen.

Het definiëren van externe tabellen omvat het opgeven van de gegevensbron, de indeling van de tekstbestanden en de tabeldefinities. Naslagartikelen over de T-SQL-syntaxis die u nodig hebt, zijn:

- [CREATE EXTERNAL DATA SOURCE](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [CREATE EXTERNAL TABLE](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)

Gebruik de volgende toewijzing van SQL-gegevenstype bij het laden van Parquet-bestanden:

|                         Parquet-type                         |   Logisch type van Parquet (annotatie)   |  SQL-gegevenstype   |
| :----------------------------------------------------------: | :-----------------------------------: | :--------------: |
|                           BOOLEAN                            |                                       |       bit        |
|                     BINARY / BYTE_ARRAY                      |                                       |    varbinary     |
|                            DOUBLE                            |                                       |      float       |
|                            FLOAT                             |                                       |       werkelijk       |
|                            INT32                             |                                       |       int        |
|                            INT64                             |                                       |      bigint      |
|                            INT96                             |                                       |    datetime2     |
|                     FIXED_LEN_BYTE_ARRAY                     |                                       |      binair      |
|                            BINARY                            |                 UTF8                  |     nvarchar     |
|                            BINARY                            |                STRING                 |     nvarchar     |
|                            BINARY                            |                 ENUM                  |     nvarchar     |
|                            BINARY                            |                 UUID                  | uniqueidentifier |
|                            BINARY                            |                DECIMAL                |     decimal      |
|                            BINARY                            |                 JSON                  |  nvarchar(MAX)   |
|                            BINARY                            |                 BSON                  |  varbinary(max)  |
|                     FIXED_LEN_BYTE_ARRAY                     |                DECIMAL                |     decimal      |
|                          BYTE_ARRAY                          |               INTERVAL                |  varchar(max),   |
|                            INT32                             |             INT(8, true)              |     smallint     |
|                            INT32                             |            INT(16, true)            |     smallint     |
|                            INT32                             |             INT(32, true)             |       int        |
|                            INT32                             |            INT(8, false)            |     tinyint      |
|                            INT32                             |            INT(16, false)             |       int        |
|                            INT32                             |           INT(32, false)            |      bigint      |
|                            INT32                             |                 DATE                  |       date       |
|                            INT32                             |                DECIMAL                |     decimal      |
|                            INT32                             |            TIME (MILLIS )             |       tijd       |
|                            INT64                             |            INT(64, true)            |      bigint      |
|                            INT64                             |           INT(64, false )            |  decimal(20,0)   |
|                            INT64                             |                DECIMAL                |     decimal      |
|                            INT64                             |         TIME (MILLIS)                 |       tijd       |
|                            INT64                             | TIJDSTEMPEL (UURSTEMPELS)                  |    datetime2     |
| [Complex type](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fgithub.com%2Fapache%2Fparquet-format%2Fblob%2Fmaster%2FLogicalTypes.md%23lists&data=02\|01\|kevin%40microsoft.com\|19f74d93f5ca45a6b73c08d7d7f5f111\|72f988bf86f141af91ab2d7cd011db47\|1\|0\|637215323617803168&sdata=6Luk047sK26ijTzfvKMYc%2FNu%2Fz0AlLCX8lKKTI%2F8B5o%3D&reserved=0) |                 LIST                  |   varchar(max)   |
| [Complex type](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fgithub.com%2Fapache%2Fparquet-format%2Fblob%2Fmaster%2FLogicalTypes.md%23maps&data=02\|01\|kevin%40microsoft.com\|19f74d93f5ca45a6b73c08d7d7f5f111\|72f988bf86f141af91ab2d7cd011db47\|1\|0\|637215323617803168&sdata=FiThqXxjgmZBVRyigHzfh5V7Z%2BPZHjud2IkUUM43I7o%3D&reserved=0) |                  MAP                  |   varchar(max)   |

>[!IMPORTANT] 
>- Toegewezen SQL-pools bieden momenteel geen ondersteuning voor Parquet-gegevenstypen met MICROS- en NANOS-precisie. 
>- De volgende fout kan zich voor doen als de typen niet overeenkomen tussen Parquet en SQL of als u niet-ondersteunde Parquet-gegevenstypen hebt: **"HdfsBridge::recordReaderFillBuffer - Unexpected error encountered filling record reader buffer: ClassCastException: ..."**
>- Het laden van een waarde buiten het bereik van 0-127 in een kleine kolom voor Parquet- en ORC-bestandsindeling wordt niet ondersteund.

Zie Externe tabellen maken voor een voorbeeld van het [maken van externe objecten.](../sql/develop-tables-external-tables.md?tabs=sql-pool)

### <a name="format-text-files"></a>Tekstbestanden opmaken

Als u PolyBase gebruikt, moeten de gedefinieerde externe objecten de rijen van de tekstbestanden uitlijnen met de definitie van de externe tabel en bestandsindeling. De gegevens in elke rij van het tekstbestand moeten worden uitgelijnd met de tabeldefinitie.
De tekstbestanden opmaken:

- Als uw gegevens afkomstig zijn van een niet-relationele bron, moet u deze transformeren in rijen en kolommen. Of de gegevens nu afkomstig zijn van een relationele of niet-relationele bron, de gegevens moeten worden getransformeerd om ze uit te lijnen met de kolomdefinities voor de tabel waarin u van plan bent de gegevens te laden.
- Maak gegevens in het tekstbestand op om deze uit te lijnen met de kolommen en gegevenstypen in de doeltabel. Onjuiste uitlijning tussen gegevenstypen in de externe tekstbestanden en de toegewezen SQL-pooltabel zorgt ervoor dat rijen worden geweigerd tijdens het laden.
- Scheid velden in het tekstbestand met een eind.  Zorg ervoor dat u een teken of tekenreeks gebruikt die niet in uw brongegevens wordt gevonden. Gebruik het eind-eind dat u hebt opgegeven [met CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

## <a name="4-load-the-data-using-polybase-or-the-copy-statement"></a>4. De gegevens laden met PolyBase of de COPY-instructie

Het is best practice gegevens in een faseringstabel te laden. Met faseringstabellen kunt u fouten afhandelen zonder de productietabellen te verstoren. Een faseringstabel biedt u ook de mogelijkheid om de toegewezen architectuur voor parallelle verwerking van SQL-pool te gebruiken voor gegevenstransformaties voordat u de gegevens in productietabellen invoegt.

### <a name="options-for-loading"></a>Opties voor laden

Als u gegevens wilt laden, kunt u een van deze laadopties gebruiken:

- De [COPY-instructie](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true) is het aanbevolen laadprogramma, omdat u hiermee naadloos en flexibel gegevens kunt laden. De instructie heeft veel extra laadmogelijkheden die PolyBase niet biedt. 
- [PolyBase met T-SQL](./load-data-from-azure-blob-storage-using-copy.md) vereist dat u externe gegevensobjecten definieert.
- [PolyBase en copy-instructie met Azure Data Factory (ADF)](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) is een ander hulpprogramma voor orchestration.  Hiermee definieert u een pijplijn en worden taken gepland.
- [PolyBase met SSIS](/sql/integration-services/load-data-to-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) werkt goed wanneer uw brongegevens zich in de SQL Server. SSIS definieert de toewijzingen van de bron-naar-doeltabel en orkestreert ook de belasting. Als u al SSIS-pakketten hebt, kunt u de pakketten wijzigen zodat ze werken met de nieuwe datawarehouse-bestemming.
- [PolyBase met Azure Databricks](/azure/databricks/scenarios/databricks-extract-load-sql-data-warehouse?bc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2ftoc.json) brengt gegevens over van een tabel naar een Databricks-gegevensframe en/of schrijft gegevens van een Databricks-gegevensframe naar een tabel met behulp van PolyBase.

### <a name="other-loading-options"></a>Andere laadopties

Naast PolyBase en de COPY-instructie kunt u [bcp](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) of de [SqlBulkCopy-API gebruiken.](/dotnet/api/system.data.sqlclient.sqlbulkcopy?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) BCP wordt rechtstreeks in de database geladen zonder via Azure Blob Storage te gaan en is alleen bedoeld voor kleine belastingen.

> [!NOTE]
> De laadprestaties van deze opties zijn trager dan PolyBase en de COPY-instructie.

## <a name="5-transform-the-data"></a>5. De gegevens transformeren

Terwijl gegevens zich in de faseringstabel staan, voert u transformaties uit die uw workload vereist. Verplaats de gegevens vervolgens naar een productietabel.

## <a name="6-insert-the-data-into-production-tables"></a>6. De gegevens invoegen in productietabellen

De INSERT INTO ... De select-instructie verplaatst de gegevens van de faseringstabel naar de permanente tabel.

Als u een ETL-proces ontwerpt, kunt u het proces uitvoeren in een klein testvoorbeeld. Probeer 1000 rijen uit de tabel te extraheren naar een bestand, verplaats het naar Azure en laad deze vervolgens in een faseringstabel.

## <a name="partner-loading-solutions"></a>Oplossingen voor het laden van partners

Veel van onze partners hebben laadoplossingen. Zie een lijst met onze oplossingspartners voor [meer informatie.](sql-data-warehouse-partner-business-intelligence.md)

## <a name="next-steps"></a>Volgende stappen

Zie voor hulp bij het laden [Richtlijnen voor het laden van gegevens](guidance-for-loading-data.md).