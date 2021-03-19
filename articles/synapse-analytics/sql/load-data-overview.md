---
title: Een polybase data-strategie voor het laden van gegevens ontwerpen voor een toegewezen SQL-groep
description: In plaats van ETL kunt u een uitpak-, laad-en transformatie proces (ELT) voor het laden van gegevens met exclusieve SQL ontwerpen.
services: synapse-analytics
author: gaursa
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: gaursa
ms.reviewer: igorstan
ms.openlocfilehash: 850c03d5cbda8fb11bbc804f2cd2c848a5a2411f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104589496"
---
# <a name="design-a-polybase-data-loading-strategy-for-dedicated-sql-pool-in-azure-synapse-analytics"></a>Een polybase data-strategie voor het laden van gegevens ontwerpen voor een toegewezen SQL-groep in azure Synapse Analytics

Traditionele SMP-data warehouses maken gebruik van een proces voor het laden van de gegevens extract, Transform en load (ETL). De Azure SQL-groep is een enorm parallelle verwerkings architectuur (MPP) die gebruikmaakt van de schaal baarheid en flexibiliteit van reken-en opslag resources. Het gebruik van een proces voor het uitpakken, laden en transformeren (ELT) kan profiteren van de ingebouwde mogelijkheden voor gedistribueerde query verwerking en resources elimineren die nodig zijn om de gegevens te transformeren voordat ze worden geladen.

Hoewel de SQL-groep veel methoden voor het laden ondersteunt, inclusief niet-polybase-opties zoals BCP en SQL BulkCopy-API, is de snelste en meest schaal bare manier om de gegevens te laden via Poly base.  Poly Base is een technologie die toegang heeft tot externe gegevens die zijn opgeslagen in Azure Blob Storage of Azure Data Lake Store via de T-SQL-taal.

> [!VIDEO https://www.youtube.com/embed/l9-wP7OdhDk]

## <a name="extract-load-and-transform-elt"></a>Extra heren, laden en transformeren (ELT)

Extra heren, laden en transformeren (ELT) is een proces waarmee gegevens worden geëxtraheerd uit een bron systeem, in een Data Warehouse worden geladen en vervolgens worden getransformeerd.

De basis stappen voor het implementeren van een poly base-ELT voor een toegewezen SQL-groep zijn:

1. Extraheer de brongegevens naar tekstbestanden.
2. Voer de gegevens in op Azure Blob-opslag of Azure Data Lake Store.
3. De gegevens voorbereiden voor het laden van.
4. Laad de gegevens in speciale faserings tabellen van SQL-groep met poly base.
5. Transformeer de gegevens.
6. Voeg de gegevens in productietabellen in.

Zie [poly Base gebruiken voor het laden van gegevens uit Azure Blob-opslag naar Azure Synapse Analytics](../sql-data-warehouse/load-data-from-azure-blob-storage-using-copy.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json)voor een zelf studie.

Zie [blog voor het laden van patronen](/archive/blogs/sqlcat/azure-sql-data-warehouse-loading-patterns-and-strategies)voor meer informatie.

## <a name="1-extract-the-source-data-into-text-files"></a>1. Extraheer de bron gegevens in tekst bestanden

Het ophalen van gegevens uit uw bron systeem is afhankelijk van de opslag locatie.  Het doel is om de gegevens te verplaatsen naar poly base-ondersteunde tekst bestanden met scheidings tekens.

### <a name="polybase-external-file-formats"></a>Poly base externe bestands indelingen

Poly base laadt gegevens van UTF-8-en UTF-16-gecodeerde tekst bestanden met scheidings tekens. Naast de tekst bestanden met scheidings tekens, wordt deze geladen vanuit de Hadoop-bestands indelingen RC-bestand, ORC en Parquet. Poly Base kan ook gegevens laden uit gzip-en Snappy gecomprimeerde bestanden. Poly Base ondersteunt momenteel geen uitgebreide ASCII-indeling met een vaste breedte en geneste indelingen zoals WinZip, JSON en XML.

Als u exporteert vanaf SQL Server, kunt u het [opdracht regel programma BCP](/sql/tools/bcp-utility?view=azure-sqldw-latest&preserve-view=true) gebruiken om de gegevens te exporteren naar tekst bestanden met scheidings tekens. De Parquet voor Azure Synapse Analytics-gegevens type toewijzing is als volgt:

| **Parquet-gegevens type** |                      **SQL-gegevens type**                       |
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

## <a name="2-land-the-data-into-azure-blob-storage-or-azure-data-lake-store"></a>2. Voer de gegevens over naar Azure Blob-opslag of Azure Data Lake Store

Als u de gegevens in azure Storage wilt laten staan, kunt u deze verplaatsen naar [Azure Blob-opslag](../../storage/blobs/storage-blobs-introduction.md) of [Azure data Lake Store](../../data-lake-store/data-lake-store-overview.md). In beide locaties moeten de gegevens worden opgeslagen in tekst bestanden. Poly Base kan worden geladen vanuit een van beide locaties.

Hulpprogram ma's en services die u kunt gebruiken om gegevens naar Azure Storage te verplaatsen:

- De [Azure ExpressRoute](../../expressroute/expressroute-introduction.md) -service verbetert de netwerk doorvoer, prestaties en voorspel baarheid. ExpressRoute is een service waarmee uw gegevens worden gerouteerd via een speciale particuliere verbinding met Azure. ExpressRoute-verbindingen sturen geen gegevens via het open bare Internet. De verbindingen bieden meer betrouw baarheid, hogere snelheden, lagere wacht tijden en hogere beveiliging dan typische verbindingen via het open bare Internet.
- Met het [hulp programma AZCopy](../../storage/common/storage-use-azcopy-v10.md) kunt u gegevens verplaatsen naar Azure Storage via het open bare Internet. Dit werkt als uw gegevens grootten minder dan 10 TB zijn. Als u de belasting regel matig wilt uitvoeren met AZCopy, test u de netwerk snelheid om te controleren of deze acceptabel is.
- [Azure Data Factory (ADF)](../../data-factory/introduction.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) heeft een gateway die u op uw lokale server kunt installeren. Vervolgens kunt u een pijp lijn maken om gegevens van uw lokale server naar Azure Storage te verplaatsen. Zie [gegevens laden in toegewezen SQL-groep](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)om Data Factory te gebruiken met exclusieve SQL-groep.

## <a name="3-prepare-the-data-for-loading"></a>3. de gegevens voorbereiden voor het laden

Mogelijk moet u de gegevens in uw opslag account voorbereiden en opschonen voordat u deze in een toegewezen SQL-groep laadt. Gegevens voorbereiding kan worden uitgevoerd terwijl de gegevens zich in de bron bevinden, terwijl u de gegevens naar tekst bestanden exporteert of wanneer de gegevens zich in Azure Storage bevinden.  Het is eenvoudig om zo snel mogelijk in het proces te werken met de gegevens.  

### <a name="define-external-tables"></a>Externe tabellen definiëren

Voordat u gegevens kunt laden, moet u externe tabellen definiëren in uw data warehouse. Poly base gebruikt externe tabellen voor het definiëren en openen van de gegevens in Azure Storage. Een externe tabel is vergelijkbaar met een database weergave. De externe tabel bevat het tabel schema en verwijst naar gegevens die buiten het Data Warehouse zijn opgeslagen.

Als u externe tabellen definieert, moet u de gegevens bron, de indeling van de tekst bestanden en de tabel definities opgeven. Hieronder vindt u de functies van de T-SQL-syntaxis die u nodig hebt:

- [CREATE EXTERNAL DATA SOURCE](/sql/t-sql/statements/create-external-data-source-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [CREATE EXTERNAL TABLE](/sql/t-sql/statements/create-external-table-transact-sql?view=azure-sqldw-latest&preserve-view=true)

### <a name="format-text-files"></a>Tekst bestanden opmaken

Zodra de externe objecten zijn gedefinieerd, moet u de rijen van de tekst bestanden uitlijnen met de definitie van de externe tabel en de bestands indeling. De gegevens in elke rij van het tekst bestand moeten worden uitgelijnd met de tabel definitie.
De tekst bestanden opmaken:

- Als uw gegevens afkomstig zijn uit een niet-relationele bron, moet u deze omzetten in rijen en kolommen. Of de gegevens afkomstig zijn uit een relationele of niet-relationele bron, dat de gegevens moeten worden getransformeerd om te worden uitgelijnd met de kolom definities voor de tabel waarin u de gegevens wilt laden.
- Gegevens in het tekst bestand opmaken om uit te lijnen met de kolommen en gegevens typen in de doel tabel van de SQL-pool. Een onjuiste uitlijning tussen gegevens typen in de externe tekst bestanden en de Data Warehouse tabel zorgt ervoor dat rijen worden afgewezen tijdens het laden.
- Scheid velden in het tekst bestand met een terminator.  Zorg ervoor dat u een teken of een teken reeks gebruikt die niet in uw bron gegevens is gevonden. Gebruik de Terminator die u hebt opgegeven met [externe BESTANDS indeling maken](/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest&preserve-view=true).

## <a name="4-load-the-data-into-dedicated-sql-pool-staging-tables-using-polybase"></a>4. Laad de gegevens in speciale faserings tabellen van de SQL-groep met poly base

Het is best practice om gegevens in een faserings tabel te laden. Met faserings tabellen kunt u fouten afhandelen zonder de productie tabellen te verstoren. Een faserings tabel biedt u ook de mogelijkheid om de ingebouwde mogelijkheden van de gedistribueerde query verwerking van SQL-groepen te gebruiken voor gegevens transformaties voordat u de gegevens in productie tabellen invoegt.

### <a name="options-for-loading-with-polybase"></a>Opties voor het laden met poly base

Als u gegevens wilt laden met poly Base, kunt u een van de volgende laad opties gebruiken:

- [Poly Base met T-SQL](../sql-data-warehouse/load-data-from-azure-blob-storage-using-copy.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json) werkt goed als uw gegevens zich in Azure Blob storage of Azure data Lake Store bevinden. Het biedt u de meeste controle over het laad proces, maar u moet ook externe gegevens objecten definiëren. Met de andere methoden worden deze objecten achter de schermen gedefinieerd wanneer u bron tabellen aan doel tabellen toewijst.  U kunt met behulp van Azure Data Factory-, SSIS-of Azure-functies voor het organiseren van T-SQL-belasting.
- [Poly Base met SSIS](/sql/integration-services/load-data-to-sql-data-warehouse?view=azure-sqldw-latest&preserve-view=true) werkt goed als uw bron gegevens zich in SQL Server bevinden. SSIS definieert de bron-naar-doel tabel toewijzingen en organiseert ook de belasting. Als u al SSIS-pakketten hebt, kunt u de pakketten wijzigen om met de nieuwe Data Warehouse-bestemming te werken.
- [Poly Base met Azure Data Factory (ADF)](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) is een andere Orchestration-tool.  Hierin worden een pijp lijn en plannings taken gedefinieerd.
- [Poly Base met Azure Databricks](/azure/databricks/scenarios/databricks-extract-load-sql-data-warehouse?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json) gegevens overdraagt van een Azure Synapse Analytics-tabel naar een Databricks data frame en/of schrijft gegevens van een Databricks data frame naar een Azure Synapse Analytics-tabel met poly base.

### <a name="non-polybase-loading-options"></a>Opties voor het laden van niet-poly base

Als uw gegevens niet compatibel zijn met poly Base, kunt u [bcp](/sql/tools/bcp-utility?view=azure-sqldw-latest&preserve-view=true) of de [SQLBulkCopy-API](/dotnet/api/system.data.sqlclient.sqlbulkcopy)gebruiken. BCP wordt rechtstreeks naar een toegewezen SQL-groep geladen zonder Azure Blob-opslag te passeren en is uitsluitend bedoeld voor kleine belastingen. Houd er rekening mee dat de belasting prestaties van deze opties aanzienlijk langzamer zijn dan poly base.

## <a name="5-transform-the-data"></a>5. de gegevens transformeren

Terwijl de gegevens zich in de faserings tabel bevindt, voert u trans formaties uit die nodig zijn voor uw werk belasting. Verplaats de gegevens vervolgens naar een productie tabel.

## <a name="6-insert-the-data-into-production-tables"></a>6. Voeg de gegevens in productie tabellen in

INVOEGEN in... Met de instructie SELECT worden de gegevens van de faserings tabel naar de permanente tabel verplaatst.

Probeer tijdens het ontwerpen van een ETL-proces het proces uit te voeren op een kleine steek proef. Probeer 1000 rijen uit de tabel naar een bestand te halen, verplaats het naar Azure en probeer het te laden in een faserings tabel.

## <a name="partner-loading-solutions"></a>Oplossingen voor het laden van partners

Veel van onze partners hebben het laden van oplossingen. Zie een lijst met onze [oplossings partners](../sql-data-warehouse/sql-data-warehouse-partner-business-intelligence.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de [richt lijnen voor het laden van gegevens](data-loading-best-practices.md)voor meer informatie.