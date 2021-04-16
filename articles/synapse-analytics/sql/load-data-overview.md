---
title: Een Strategie voor het laden van PolyBase-gegevens ontwerpen voor toegewezen SQL-pool
description: In plaats van ETL ontwerpt u een ELT-proces (Extract, Load, and Transform) voor het laden van gegevens met toegewezen SQL.
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: igorstan
ms.openlocfilehash: 518843e688da7f940b36e77aee2667b4984ea5a3
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567347"
---
# <a name="design-a-polybase-data-loading-strategy-for-dedicated-sql-pool-in-azure-synapse-analytics"></a>Een Strategie voor het laden van PolyBase-gegevens ontwerpen voor toegewezen SQL-pool in Azure Synapse Analytics

Traditionele SMP-datawarehouses gebruiken een ETL-proces (Extract, Transform, and Load) voor het laden van gegevens. Azure SQL pool is een MPP-architectuur (Massively Parallel Processing) die gebruik maakt van de schaalbaarheid en flexibiliteit van reken- en opslagbronnen. Met behulp van een ELT-proces (Extraheren, laden en transformeren) kunt u profiteren van ingebouwde mogelijkheden voor gedistribueerde queryverwerking en resources elimineren die nodig zijn om de gegevens te transformeren voordat ze worden geladen.

Sql-pool ondersteunt veel laadmethoden, waaronder niet-Polybase-opties zoals BCP en SQL BulkCopy-API, maar de snelste en meest schaalbare manier om datums te laden, is via PolyBase.  PolyBase is een technologie die toegang heeft tot externe gegevens die zijn opgeslagen in Azure Blob Storage of Azure Data Lake Store via de T-SQL-taal.

> [!VIDEO https://www.youtube.com/embed/l9-wP7OdhDk]

## <a name="extract-load-and-transform-elt"></a>Extraheren, laden en transformeren (ELT)

Extraheren, laden en transformeren (ELT) is een proces waarmee gegevens worden geëxtraheerd uit een bronsysteem, in een datawarehouse worden geladen en vervolgens getransformeerd.

De basisstappen voor het implementeren van een PolyBase ELT voor toegewezen SQL-pool zijn:

1. Extraheer de brongegevens naar tekstbestanden.
2. De gegevens in Azure Blob Storage of Azure Data Lake Store.
3. Bereid de gegevens voor op het laden.
4. Laad de gegevens in toegewezen faseringstabellen voor SQL-pool met behulp van PolyBase.
5. De gegevens transformeren.
6. Voeg de gegevens in productietabellen in.

Zie PolyBase gebruiken om gegevens uit Azure Blob Storage te laden naar Azure Synapse Analytics voor een zelfstudie over [Azure Synapse Analytics.](../sql-data-warehouse/load-data-from-azure-blob-storage-using-copy.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json)

Zie loading [patterns blog (Blog over laadpatronen) voor meer informatie.](/archive/blogs/sqlcat/azure-sql-data-warehouse-loading-patterns-and-strategies)

## <a name="1-extract-the-source-data-into-text-files"></a>1. De brongegevens uitpakken in tekstbestanden

Het verwijderen van gegevens uit uw bronsysteem is afhankelijk van de opslaglocatie.  Het doel is om de gegevens naar ondersteunde tekstbestanden met scheidingstekens te verplaatsen naar PolyBase.

### <a name="polybase-external-file-formats"></a>Externe PolyBase-bestandsindelingen

PolyBase laadt gegevens uit met UTF-8 en UTF-16 gecodeerde tekstbestanden met scheidingstekens. Naast de tekstbestanden met scheidingstekens worden deze geladen vanuit de Hadoop-bestandsindelingen RC File, ORC en Parquet. PolyBase kan ook gegevens laden uit gecomprimeerde Gzip- en Snappy-bestanden. PolyBase biedt momenteel geen ondersteuning voor uitgebreide ASCII, indeling met vaste breedte en geneste indelingen zoals WinZip, JSON en XML.

Als u exporteert vanuit SQL Server, kunt u het [opdrachtregelprogramma BCP](/sql/tools/bcp-utility?view=azure-sqldw-latest&preserve-view=true) gebruiken om de gegevens te exporteren naar tekstbestanden met scheidingstekens. Parquet voor Azure Synapse Analytics toewijzing van gegevenstype is als volgt:

| **Parquet-gegevenstype** |                      **SQL-gegevenstype**                       |
| :-------------------: | :----------------------------------------------------------: |
|        tinyint        |                           tinyint                            |
|       smallint        |                           smallint                           |
|          int          |                             int                              |
|        bigint         |                            bigint                            |
|        booleaans        |                             bit                              |
|        double         |                            float                             |
|         float         |                             werkelijk                             |
|        double         |                            money                             |
|        double         |                          smallmoney                          |
|        tekenreeks         |                            nchar                             |
|        tekenreeks         |                           nvarchar                           |
|        tekenreeks         |                             char                             |
|        tekenreeks         |                           varchar                            |
|        binair         |                            binair                            |
|        binair         |                          varbinary                           |
|       tijdstempel       |                             date                             |
|       tijdstempel       |                        smalldatetime                         |
|       tijdstempel       |                          datetime2                           |
|       tijdstempel       |                           datum/tijd                           |
|       tijdstempel       |                             tijd                             |
|       date            |                             date                             |
|        decimal        |                            decimal                           |

## <a name="2-land-the-data-into-azure-blob-storage-or-azure-data-lake-store"></a>2. De gegevens in Azure Blob Storage of Azure Data Lake Store

Als u de gegevens in Azure Storage wilt opslaan, kunt u deze verplaatsen naar [Azure Blob Storage](../../storage/blobs/storage-blobs-introduction.md) of Azure [Data Lake Store](../../data-lake-store/data-lake-store-overview.md). Op beide locaties moeten de gegevens worden opgeslagen in tekstbestanden. PolyBase kan vanaf beide locaties worden geladen.

Hulpprogramma's en services die u kunt gebruiken om gegevens te verplaatsen naar Azure Storage:

- [Azure ExpressRoute-service](../../expressroute/expressroute-introduction.md) verbetert de netwerkdoorvoer, prestaties en voorspelbaarheid. ExpressRoute is een service die uw gegevens routeert via een toegewezen privéverbinding naar Azure. ExpressRoute-verbindingen leiden geen gegevens door via het openbare internet. De verbindingen bieden meer betrouwbaarheid, hogere snelheden, lagere latentie en hogere beveiliging dan gewone verbindingen via het openbare internet.
- [Het AZCopy-hulpprogramma](../../storage/common/storage-use-azcopy-v10.md) verplaatst gegevens naar Azure Storage via het openbare internet. Dit werkt als uw gegevens minder dan 10 TB groot zijn. Als u regelmatig belasting wilt uitvoeren met AZCopy, test u de netwerksnelheid om te zien of deze acceptabel is.
- [Azure Data Factory (ADF) heeft](../../data-factory/introduction.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) een gateway die u op uw lokale server kunt installeren. Vervolgens kunt u een pijplijn maken om gegevens van uw lokale server naar een Azure Storage. Zie Gegevens laden Data Factory toegewezen SQL-pool voor meer informatie over het [gebruik van Data Factory toegewezen SQL-pool.](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

## <a name="3-prepare-the-data-for-loading"></a>3. De gegevens voorbereiden voor het laden

Mogelijk moet u de gegevens in uw opslagaccount voorbereiden en ops schonen voordat u ze in een toegewezen SQL-pool laadt. Gegevensvoorbereiding kan worden uitgevoerd terwijl uw gegevens zich in de bron, tijdens het exporteren van de gegevens naar tekstbestanden of nadat de gegevens zich in de bron Azure Storage.  Het is het gemakkelijkst om zo vroeg mogelijk in het proces met de gegevens te werken.  

### <a name="define-external-tables"></a>Externe tabellen definiëren

Voordat u gegevens kunt laden, moet u externe tabellen in uw datawarehouse definiëren. PolyBase maakt gebruik van externe tabellen voor het definiëren en openen van de gegevens in Azure Storage. Een externe tabel is vergelijkbaar met een databaseweergave. De externe tabel bevat het tabelschema en wijst naar gegevens die buiten het datawarehouse zijn opgeslagen.

Het definiëren van externe tabellen omvat het opgeven van de gegevensbron, de indeling van de tekstbestanden en de tabeldefinities. Hier volgen de onderwerpen over de T-SQL-syntaxis die u nodig hebt:

- [CREATE EXTERNAL DATA SOURCE](/sql/t-sql/statements/create-external-data-source-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [CREATE EXTERNAL TABLE](/sql/t-sql/statements/create-external-table-transact-sql?view=azure-sqldw-latest&preserve-view=true)

### <a name="format-text-files"></a>Tekstbestanden opmaken

Zodra de externe objecten zijn gedefinieerd, moet u de rijen van de tekstbestanden uitlijnen met de definitie van de externe tabel en bestandsindeling. De gegevens in elke rij van het tekstbestand moeten worden uitgelijnd met de tabeldefinitie.
De tekstbestanden opmaken:

- Als uw gegevens afkomstig zijn van een niet-relationele bron, moet u deze transformeren in rijen en kolommen. Of de gegevens nu afkomstig zijn van een relationele of niet-relationele bron, de gegevens moeten worden getransformeerd om ze uit te lijnen met de kolomdefinities voor de tabel waarin u van plan bent de gegevens te laden.
- Maak gegevens in het tekstbestand op om deze uit te lijnen met de kolommen en gegevenstypen in de doeltabel van de SQL-pool. Onjuiste uitlijning tussen gegevenstypen in de externe tekstbestanden en de datawarehousetabel zorgt ervoor dat rijen worden geweigerd tijdens het laden.
- Scheid velden in het tekstbestand met een eind.  Zorg ervoor dat u een teken of tekenreeks gebruikt die niet in uw brongegevens wordt gevonden. Gebruik het eind-eind dat u hebt opgegeven [met CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest&preserve-view=true).

## <a name="4-load-the-data-into-dedicated-sql-pool-staging-tables-using-polybase"></a>4. De gegevens laden in toegewezen faseringstabellen voor SQL-pool met PolyBase

Het is best practice gegevens in een faseringstabel te laden. Met faseringstabellen kunt u fouten afhandelen zonder de productietabellen te verstoren. Een faseringstabel biedt u ook de mogelijkheid om ingebouwde mogelijkheden voor gedistribueerde queryverwerking van SQL-pool te gebruiken voor gegevenstransformaties voordat u de gegevens in productietabellen invoegt.

### <a name="options-for-loading-with-polybase"></a>Opties voor laden met PolyBase

Als u gegevens wilt laden met PolyBase, kunt u een van deze laadopties gebruiken:

- [PolyBase met T-SQL](../sql-data-warehouse/load-data-from-azure-blob-storage-using-copy.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json) werkt goed wanneer uw gegevens zich in Azure Blob Storage of Azure Data Lake Store. Het biedt u de meeste controle over het laadproces, maar vereist ook dat u externe gegevensobjecten definieert. De andere methoden definiëren deze objecten achter de schermen wanneer u brontabellen toeschrijft aan doeltabellen.  Als u T-SQL-belastingen wilt ins orchestraeren, kunt u Azure Data Factory, SSIS of Azure-functies gebruiken.
- [PolyBase met SSIS](/sql/integration-services/load-data-to-sql-data-warehouse?view=azure-sqldw-latest&preserve-view=true) werkt goed wanneer uw brongegevens zich in een SQL Server. SSIS definieert de toewijzingen van de bron-naar-doeltabel en definieert ook de belasting. Als u al SSIS-pakketten hebt, kunt u de pakketten wijzigen zodat ze werken met de nieuwe datawarehouse-bestemming.
- [PolyBase met Azure Data Factory (ADF)](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) is een ander orchestration-hulpprogramma.  Hiermee definieert u een pijplijn en worden taken gepland.
- [PolyBase met Azure Databricks](/azure/databricks/scenarios/databricks-extract-load-sql-data-warehouse?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json) brengt gegevens over van een Azure Synapse Analytics-tabel naar een Databricks-gegevensframe en/of schrijft gegevens van een Databricks-gegevensframe naar een Azure Synapse Analytics-tabel met behulp van PolyBase.

### <a name="non-polybase-loading-options"></a>Laadopties zonder PolyBase

Als uw gegevens niet compatibel zijn met PolyBase, kunt u [BCP](/sql/tools/bcp-utility?view=azure-sqldw-latest&preserve-view=true) of de [SQLBulkCopy-API gebruiken.](/dotnet/api/system.data.sqlclient.sqlbulkcopy) BCP wordt rechtstreeks in een toegewezen SQL-pool geladen zonder via Azure Blob Storage te gaan en is alleen bedoeld voor kleine belastingen. Houd er rekening mee dat de laadprestaties van deze opties aanzienlijk langzamer zijn dan PolyBase.

## <a name="5-transform-the-data"></a>5. De gegevens transformeren

Terwijl gegevens zich in de faseringstabel staan, voert u transformaties uit die uw workload vereist. Verplaats de gegevens vervolgens naar een productietabel.

## <a name="6-insert-the-data-into-production-tables"></a>6. De gegevens invoegen in productietabellen

De INSERT INTO ... De SELECT-instructie verplaatst de gegevens van de faseringstabel naar de permanente tabel.

Als u een ETL-proces ontwerpt, kunt u het proces uitvoeren in een klein testvoorbeeld. Probeer 1000 rijen uit de tabel te extraheren naar een bestand, verplaats deze naar Azure en laad deze vervolgens in een faseringstabel.

## <a name="partner-loading-solutions"></a>Oplossingen voor het laden van partners

Veel van onze partners hebben laadoplossingen. Zie een lijst met onze oplossingspartners voor [meer informatie.](../sql-data-warehouse/sql-data-warehouse-partner-business-intelligence.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

## <a name="next-steps"></a>Volgende stappen

Zie Richtlijnen voor het laden van gegevens voor hulp bij het laden van [gegevens.](data-loading-best-practices.md)