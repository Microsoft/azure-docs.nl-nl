---
title: Ontwerp ELT in plaats van ETL
description: Implementeer flexibele strategieën voor het laden van gegevens voor exclusieve SQL-groepen in azure Synapse Analytics.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/20/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: b59e256f137bc8e1b13cb92d477e32bc01629ac5
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98679893"
---
# <a name="data-loading-strategies-for-dedicated-sql-pool-in-azure-synapse-analytics"></a>Strategieën voor het laden van gegevens voor een toegewezen SQL-groep in azure Synapse Analytics

Traditionele SMP-exclusieve SQL-Pools gebruiken een proces voor het laden van gegevens en het laden van een taak voor het door halen van een bewerking. Synapse SQL, in azure Synapse Analytics, maakt gebruik van een gedistribueerde architectuur voor het verwerken van query's die de schaal baarheid en flexibiliteit van reken-en opslag bronnen benut.

Het gebruik van een proces voor extractie, laden en transformeren (ELT) maakt gebruik van ingebouwde functies voor gedistribueerde query verwerking en elimineert de resources die nodig zijn voor gegevens transformatie voordat ze worden geladen.

Exclusieve SQL-groepen ondersteunen veel laad methoden, waaronder populaire SQL Server opties zoals [bcp](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) en de SQLBULKCOPY- [API](/dotnet/api/system.data.sqlclient.sqlbulkcopy?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json), de snelste en meest schaal bare manier om gegevens te laden via Poly base externe tabellen en de [instructie Copy](/sql/t-sql/statements/copy-into-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

Met poly base en de instructie COPY kunt u toegang krijgen tot externe gegevens die zijn opgeslagen in Azure Blob Storage of Azure Data Lake Store via de T-SQL-taal. Voor de meeste flexibiliteit bij het laden wordt u aangeraden de instructie COPY te gebruiken.


## <a name="what-is-elt"></a>Wat is ELT?

Extra heren, laden en transformeren (ELT) is een proces waarmee gegevens worden geëxtraheerd uit een bron systeem, worden geladen in een toegewezen SQL-groep en vervolgens worden getransformeerd.

De basis stappen voor het implementeren van ELT zijn:

1. Extraheer de brongegevens naar tekstbestanden.
2. Voer de gegevens in op Azure Blob-opslag of Azure Data Lake Store.
3. De gegevens voorbereiden voor het laden van.
4. Laad de gegevens in faserings tabellen met poly base of de Kopieer opdracht.
5. Transformeer de gegevens.
6. Voeg de gegevens in productietabellen in.

Zie [gegevens laden uit Azure Blob-opslag](./load-data-from-azure-blob-storage-using-copy.md)voor een zelf studie over het laden van.

## <a name="1-extract-the-source-data-into-text-files"></a>1. Extraheer de bron gegevens in tekst bestanden

Het ophalen van gegevens uit uw bron systeem is afhankelijk van de opslag locatie. Het doel is om de gegevens te verplaatsen naar ondersteunde gescheiden tekst of CSV-bestanden.

### <a name="supported-file-formats"></a>Ondersteunde bestandsindelingen

Met poly base en de instructie COPY kunt u gegevens laden van UTF-8-en UTF-16-gecodeerde tekst of CSV-bestanden met scheidings tekens. Behalve tekst-of CSV-bestanden, worden ze geladen vanuit de Hadoop-bestands indelingen, zoals ORC en Parquet. Poly base en de instructie COPY kunnen ook gegevens laden uit gzip-en Snappy gecomprimeerde bestanden.

Uitgebreide ASCII-indeling met een vaste breedte en geneste indelingen, zoals WinZip of XML, worden niet ondersteund. Als u exporteert vanaf SQL Server, kunt u het [opdracht regel programma BCP](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) gebruiken om de gegevens te exporteren naar tekst bestanden met scheidings tekens.

## <a name="2-land-the-data-into-azure-blob-storage-or-azure-data-lake-store"></a>2. Voer de gegevens over naar Azure Blob-opslag of Azure Data Lake Store

Als u de gegevens in azure Storage wilt laten staan, kunt u deze verplaatsen naar [Azure Blob-opslag](../../storage/blobs/storage-blobs-introduction.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) of [Azure data Lake Store Gen2](../../data-lake-store/data-lake-store-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). In beide locaties moeten de gegevens worden opgeslagen in tekst bestanden. Poly base en de instructie COPY kunnen worden geladen vanuit een van beide locaties.

Hulpprogram ma's en services die u kunt gebruiken om gegevens naar Azure Storage te verplaatsen:

- De [Azure ExpressRoute](../../expressroute/expressroute-introduction.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) -service verbetert de netwerk doorvoer, prestaties en voorspel baarheid. ExpressRoute is een service waarmee uw gegevens worden gerouteerd via een speciale particuliere verbinding met Azure. ExpressRoute-verbindingen sturen geen gegevens via het open bare Internet. De verbindingen bieden meer betrouw baarheid, hogere snelheden, lagere wacht tijden en hogere beveiliging dan typische verbindingen via het open bare Internet.
- Met het [hulp programma AZCopy](../../storage/common/storage-choose-data-transfer-solution.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) kunt u gegevens verplaatsen naar Azure Storage via het open bare Internet. Dit werkt als uw gegevens grootten minder dan 10 TB zijn. Als u de belasting regel matig wilt uitvoeren met AZCopy, test u de netwerk snelheid om te controleren of deze acceptabel is.
- [Azure Data Factory (ADF)](../../data-factory/introduction.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) heeft een gateway die u op uw lokale server kunt installeren. Vervolgens kunt u een pijp lijn maken om gegevens van uw lokale server naar Azure Storage te verplaatsen. Zie [gegevens laden voor toegewezen SQL-groepen](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)om Data Factory te gebruiken met toegewezen SQL-groepen.

## <a name="3-prepare-the-data-for-loading"></a>3. de gegevens voorbereiden voor het laden

Mogelijk moet u de gegevens in uw opslag account voorbereiden en opschonen voordat u ze laadt. Gegevens voorbereiding kan worden uitgevoerd terwijl de gegevens zich in de bron bevinden, terwijl u de gegevens naar tekst bestanden exporteert of wanneer de gegevens zich in Azure Storage bevinden.  Het is eenvoudig om zo snel mogelijk in het proces te werken met de gegevens.  

### <a name="define-the-tables"></a>De tabellen definiëren

U moet eerst de tabel (len) die u in uw toegewezen SQL-groep laadt, definiëren wanneer u de instructie COPY gebruikt.

Als u poly base gebruikt, moet u externe tabellen definiëren in uw toegewezen SQL-groep voordat u ze laadt. Poly base gebruikt externe tabellen voor het definiëren en openen van de gegevens in Azure Storage. Een externe tabel is vergelijkbaar met een database weergave. De externe tabel bevat het tabel schema en verwijst naar gegevens die zijn opgeslagen buiten de toegewezen SQL-groep.

Als u externe tabellen definieert, moet u de gegevens bron, de indeling van de tekst bestanden en de tabel definities opgeven. Naslag informatie over T-SQL-syntaxis die u nodig hebt, zijn:

- [CREATE EXTERNAL DATA SOURCE](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [CREATE EXTERNAL TABLE](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)

Gebruik de volgende SQL-gegevens type toewijzing bij het laden van Parquet-bestanden:

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
|                          BYTE_ARRAY                          |               INTERVAL                |  varchar (max),   |
|                            INT32                             |             INT(8, true)              |     smallint     |
|                            INT32                             |            INT (16, True)            |     smallint     |
|                            INT32                             |             INT(32, true)             |       int        |
|                            INT32                             |            INT (8, false)            |     tinyint      |
|                            INT32                             |            INT(16, false)             |       int        |
|                            INT32                             |           INT (32, false)            |      bigint      |
|                            INT32                             |                 DATE                  |       date       |
|                            INT32                             |                DECIMAL                |     decimal      |
|                            INT32                             |            TIME (MILLIS )             |       tijd       |
|                            INT64                             |            INT (64, True)            |      bigint      |
|                            INT64                             |           INT (64, false)            |  decimal(20,0)   |
|                            INT64                             |                DECIMAL                |     decimal      |
|                            INT64                             |         TIME (MILLIS)                 |       tijd       |
|                            INT64                             | TIJDS TEMPEL (MILLIS)                  |    datetime2     |
| [Complex type](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fgithub.com%2Fapache%2Fparquet-format%2Fblob%2Fmaster%2FLogicalTypes.md%23lists&data=02\|01\|kevin%40microsoft.com\|19f74d93f5ca45a6b73c08d7d7f5f111\|72f988bf86f141af91ab2d7cd011db47\|1\|0\|637215323617803168&sdata=6Luk047sK26ijTzfvKMYc%2FNu%2Fz0AlLCX8lKKTI%2F8B5o%3D&reserved=0) |                 LIST                  |   varchar(max)   |
| [Complex type](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fgithub.com%2Fapache%2Fparquet-format%2Fblob%2Fmaster%2FLogicalTypes.md%23maps&data=02\|01\|kevin%40microsoft.com\|19f74d93f5ca45a6b73c08d7d7f5f111\|72f988bf86f141af91ab2d7cd011db47\|1\|0\|637215323617803168&sdata=FiThqXxjgmZBVRyigHzfh5V7Z%2BPZHjud2IkUUM43I7o%3D&reserved=0) |                  MAP                  |   varchar(max)   |

>[!IMPORTANT] 
>- SQL-toegewezen Pools bieden momenteel geen ondersteuning voor Parquet-gegevens typen met MICRO-en NANOS-precisie. 
>- De volgende fout kan optreden als de typen niet overeenkomen tussen Parquet en SQL of als u niet-ondersteunde Parquet-gegevens typen hebt: **"HdfsBridge:: recordReaderFillBuffer-onverwachte fout aangetroffen bij het invullen van de buffer voor record lezer: ClassCastException:...**
>- Het laden van een waarde buiten het bereik van 0-127 in een kolom tinyint voor de Parquet-en ORC-bestands indeling wordt niet ondersteund.

Zie voor een voor beeld van het maken van externe objecten [externe tabellen maken](../sql/develop-tables-external-tables.md?tabs=sql-pool).

### <a name="format-text-files"></a>Tekst bestanden opmaken

Als u poly base gebruikt, moeten de gedefinieerde externe objecten de rijen van de tekst bestanden uitlijnen met de definitie van de externe tabel en de bestands indeling. De gegevens in elke rij van het tekst bestand moeten worden uitgelijnd met de tabel definitie.
De tekst bestanden opmaken:

- Als uw gegevens afkomstig zijn uit een niet-relationele bron, moet u deze omzetten in rijen en kolommen. Of de gegevens afkomstig zijn uit een relationele of niet-relationele bron, dat de gegevens moeten worden getransformeerd om te worden uitgelijnd met de kolom definities voor de tabel waarin u de gegevens wilt laden.
- Gegevens in het tekst bestand opmaken om uit te lijnen met de kolommen en gegevens typen in de doel tabel. Een onjuiste uitlijning tussen gegevens typen in de externe tekst bestanden en de toegewezen SQL-groeps tabel zorgt ervoor dat rijen worden afgewezen tijdens de belasting.
- Scheid velden in het tekst bestand met een terminator.  Zorg ervoor dat u een teken of een teken reeks gebruikt die niet in de bron gegevens voor komt. Gebruik de Terminator die u hebt opgegeven met [externe BESTANDS indeling maken](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

## <a name="4-load-the-data-using-polybase-or-the-copy-statement"></a>4. gegevens laden met poly base of de instructie COPY

Het is best practice om gegevens in een faserings tabel te laden. Met faserings tabellen kunt u fouten afhandelen zonder de productie tabellen te verstoren. Een faserings tabel biedt u ook de mogelijkheid om de exclusieve verwerkings architectuur van de speciale SQL-groep te gebruiken voor gegevens transformaties voordat u de gegevens in productie tabellen invoegt.

### <a name="options-for-loading"></a>Opties voor het laden van

Als u gegevens wilt laden, kunt u een van de volgende laad opties gebruiken:

- De [instructie Copy](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true) is het aanbevolen laad hulpprogramma waarmee u gegevens naadloos en flexibel kunt laden. De instructie bevat veel extra laad mogelijkheden die poly Base niet biedt. 
- [Poly Base met T-SQL](./load-data-from-azure-blob-storage-using-copy.md) vereist dat u externe gegevens objecten definieert.
- [Poly base-en copy-instructie met Azure Data Factory (ADF)](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) is een andere Orchestration-tool.  Hierin worden een pijp lijn en plannings taken gedefinieerd.
- [Poly Base met SSIS](/sql/integration-services/load-data-to-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) werkt goed als uw bron gegevens zich in SQL Server bevinden. SSIS definieert de bron-naar-doel tabel toewijzingen en organiseert ook de belasting. Als u al SSIS-pakketten hebt, kunt u de pakketten wijzigen om met de nieuwe Data Warehouse-bestemming te werken.
- [Poly Base met Azure Databricks](/azure/databricks/scenarios/databricks-extract-load-sql-data-warehouse?bc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2ftoc.json) gegevens overdraagt van een tabel naar een Databricks data frame en/of schrijft gegevens van een Databricks data frame naar een tabel met poly base.

### <a name="other-loading-options"></a>Andere opties voor laden

Naast poly base en de instructie COPY kunt u gebruikmaken van [bcp](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) of de SQLBULKCOPY- [API](/dotnet/api/system.data.sqlclient.sqlbulkcopy?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). BCP wordt rechtstreeks naar de data base geladen zonder Azure Blob-opslag te passeren en is uitsluitend bedoeld voor kleine belastingen.

> [!NOTE]
> De belasting prestaties van deze opties zijn lager dan poly base en de instructie COPY.

## <a name="5-transform-the-data"></a>5. de gegevens transformeren

Terwijl de gegevens zich in de faserings tabel bevindt, voert u trans formaties uit die nodig zijn voor uw werk belasting. Verplaats de gegevens vervolgens naar een productie tabel.

## <a name="6-insert-the-data-into-production-tables"></a>6. Voeg de gegevens in productie tabellen in

INVOEGEN in... Met de instructie SELECT worden de gegevens van de faserings tabel naar de permanente tabel verplaatst.

Probeer tijdens het ontwerpen van een ETL-proces het proces uit te voeren op een kleine steek proef. Probeer 1000 rijen uit de tabel naar een bestand te halen, verplaats het naar Azure en probeer het te laden in een faserings tabel.

## <a name="partner-loading-solutions"></a>Oplossingen voor het laden van partners

Veel van onze partners hebben het laden van oplossingen. Zie een lijst met onze [oplossings partners](sql-data-warehouse-partner-business-intelligence.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Zie voor hulp bij het laden [Richtlijnen voor het laden van gegevens](guidance-for-loading-data.md).