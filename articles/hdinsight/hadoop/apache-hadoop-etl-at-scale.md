---
title: ETL uitpakken, transformeren en laden op schaal-Azure HDInsight
description: Ontdek hoe extra heren, transformeren en laden worden gebruikt in HDInsight met Apache Hadoop.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/28/2020
ms.openlocfilehash: f11b31ba698c193029834caada2e5c4cf3ef2902
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104866638"
---
# <a name="extract-transform-and-load-etl-at-scale"></a>Uitpakken, transformeren en laden (ETL) op de juiste schaal

Extra heren, transformeren en laden (ETL) is het proces waarmee gegevens uit verschillende bronnen worden verkregen. De gegevens worden verzameld op een standaard locatie, opgeschoond en verwerkt. Uiteindelijk worden de gegevens in een gegevens archief geladen waarin kan worden gezocht. Met verouderde ETL worden gegevens geïmporteerd en op de juiste plaats gereinigd en opgeslagen in een relationele gegevens engine. Met Azure HDInsight ondersteunen een breed scala aan Apache Hadoop omgevings onderdelen ETL op schaal.

Het gebruik van HDInsight in het ETL-proces wordt door deze pijp lijn samenvatten:

:::image type="content" source="./media/apache-hadoop-etl-at-scale/hdinsight-etl-at-scale-overview.png" alt-text="Overzicht van HDInsight ETL op schaal" border="false":::

In de volgende secties worden alle ETL-fasen en de bijbehorende onderdelen besproken.

## <a name="orchestration"></a>Orchestration

Indeling omvat alle fasen van de ETL-pijp lijn. ETL-taken in HDInsight omvatten vaak verschillende producten die met elkaar samen werken. Bijvoorbeeld:

- U kunt Apache Hive gebruiken om een deel van de gegevens op te schonen en Apache varken om een ander gedeelte op te schonen.
- U kunt Azure Data Factory voor het laden van gegevens in Azure SQL Database van Azure Data Lake Store.

Indeling is nodig om de juiste taak op het juiste tijdstip uit te voeren.

### <a name="apache-oozie"></a>Apache Oozie

Apache Oozie is een coördinatiesysteem voor werkstromen waarmee Hadoop-taken worden beheerd. Oozie wordt uitgevoerd binnen een HDInsight-cluster en is geïntegreerd met de Hadoop-stack. Oozie ondersteunt Hadoop-taken voor Apache Hadoop MapReduce, Pig, Hive en Sqoop. U kunt Oozie gebruiken om taken te plannen die specifiek zijn voor een systeem, zoals Java-Program ma's of shell scripts.

Zie [Apache Oozie gebruiken met Apache Hadoop om een werk stroom te definiëren en uit te voeren op HDInsight](../hdinsight-use-oozie-linux-mac.md)voor meer informatie. Zie ook [operationeel maken de gegevens pijplijn](../hdinsight-operationalize-data-pipeline.md).

### <a name="azure-data-factory"></a>Azure Data Factory

Azure Data Factory biedt Orchestration-mogelijkheden in de vorm van platform as a Service (PaaS). Azure Data Factory is een op de cloud gebaseerde gegevensintegratieservice. Hiermee kunt u gegevensgestuurde werk stromen maken voor het organiseren en automatiseren van gegevens verplaatsing en gegevens transformatie.

Gebruik Azure Data Factory voor het volgende:

1. Data gegevensgestuurde werk stromen maken en plannen. Deze pijp lijnen nemen gegevens op uit verschillende gegevens archieven.
1. De gegevens verwerken en transformeren met behulp van reken services zoals HDInsight of Hadoop. U kunt ook Spark, Azure Data Lake Analytics, Azure Batch of Azure Machine Learning gebruiken voor deze stap.
1. Publiceer uitvoer gegevens naar gegevens archieven, zoals Azure Synapse Analytics, voor BI-toepassingen die moeten worden gebruikt.

Raadpleeg de [documentatie](../../data-factory/introduction.md)voor meer informatie over Azure Data Factory.

## <a name="ingest-file-storage-and-result-storage"></a>Bestands opslag en resultaat opslag opnemen

Brongegevens bestanden worden meestal geladen op een locatie op Azure Storage of Azure Data Lake Storage. De bestanden bevinden zich meestal in een platte indeling, zoals CSV. Maar ze kunnen elk een wille keurige indeling hebben.

### <a name="azure-storage"></a>Azure Storage

Azure Storage heeft specifieke adaptieve doelen. Zie [schaalbaarheids-en prestatie doelen voor Blob Storage](../../storage/blobs/scalability-targets.md) voor meer informatie. Voor de meeste analytische knoop punten wordt Azure Storage het beste geschaald wanneer er veel kleinere bestanden worden verwerkt. Als u binnen uw account limieten bent, garanderen Azure Storage dezelfde prestaties, ongeacht de omvang van de bestanden. U kunt terabytes aan gegevens opslaan en toch consistente prestaties krijgen. Deze instructie is waar, of u nu een subset of alle gegevens gebruikt.

Azure Storage heeft verschillende typen blobs. Een *toevoeg-BLOB* is een uitstekende optie om Weblogboeken of sensor gegevens op te slaan.

Er kunnen meerdere blobs worden gedistribueerd over verschillende servers om de toegang tot ze te schalen. Maar één BLOB wordt alleen bediend door één server. Hoewel blobs logisch kunnen worden gegroepeerd in BLOB-containers, zijn er geen gevolgen voor het partitioneren van deze groepering.

Azure Storage heeft een WebHDFS API-laag voor de Blob-opslag. Alle HDInsight-Services hebben toegang tot bestanden in Azure Blob-opslag voor het opschonen van gegevens en gegevens verwerking. Dit is vergelijkbaar met de manier waarop deze services Hadoop Distributed File System (HDFS) gebruiken.

Gegevens worden doorgaans opgenomen in Azure Storage via Power shell, de Azure Storage SDK of AZCopy.

### <a name="azure-data-lake-storage"></a>Azure Data Lake Storage

Azure Data Lake Storage is een beheerde, grootschalige-opslag plaats voor analyse gegevens. Het is compatibel met en maakt gebruik van een ontwerp paradigma dat vergelijkbaar is met HDFS. Data Lake Storage biedt onbeperkte aanpas baarheid voor totale capaciteit en de grootte van afzonderlijke bestanden. Het is een goede keuze bij het werken met grote bestanden, omdat deze kunnen worden opgeslagen op meerdere knoop punten. Het partitioneren van gegevens in Data Lake Storage geschiedt achter de schermen. U profiteert van een enorme door Voer voor het uitvoeren van analytische taken met duizenden gelijktijdige uitvoerders die op efficiënte wijze honderden terabytes aan gegevens lezen en schrijven.

Gegevens worden meestal via Azure Data Factory opgenomen in Data Lake Storage. U kunt ook Data Lake Storage Sdk's, de AdlCopy-service, Apache DistCp of Apache Sqoop gebruiken. De service die u kiest, is afhankelijk van waar de gegevens zich bevindt. Als het zich in een bestaand Hadoop-cluster bevolgt, kunt u Apache DistCp, de AdlCopy-service of de Azure Data Factory gebruiken. Voor gegevens in Azure Blob-opslag kunt u Azure Data Lake Storage .NET SDK, Azure PowerShell of Azure Data Factory gebruiken.

Data Lake Storage is geoptimaliseerd voor gebeurtenis opname via Azure Event Hubs of Apache Storm.

### <a name="considerations-for-both-storage-options"></a>Overwegingen voor beide opslag opties

Voor het uploaden van gegevens sets in het terabyte-bereik kan de netwerk latentie een groot probleem zijn. Dit geldt met name als de gegevens afkomstig zijn van een on-premises locatie. In dergelijke gevallen kunt u de volgende opties gebruiken:

- **Azure-ExpressRoute:** Maak particuliere verbindingen tussen Azure-data centers en uw on-premises infra structuur. Deze verbindingen bieden een betrouw bare optie voor het overbrengen van grote hoeveel heden gegevens. Zie de [documentatie van Azure ExpressRoute](../../expressroute/expressroute-introduction.md)voor meer informatie.

- **Gegevens uploaden van harde schijven:** U kunt de [Azure import/export-service](../../import-export/storage-import-export-service.md) gebruiken om harde schijven met uw gegevens naar een Azure-Data Center te verzenden. Uw gegevens worden eerst geüpload naar Azure Blob-opslag. U kunt vervolgens Azure Data Factory of het hulp programma AdlCopy gebruiken om gegevens uit Azure Blob-opslag te kopiëren naar Data Lake Storage.

### <a name="azure-synapse-analytics"></a>Azure Synapse Analytics

Azure Synapse Analytics is een geschikte keuze voor het opslaan van voor bereide resultaten. U kunt Azure HDInsight gebruiken om deze services uit te voeren voor Azure Synapse Analytics.

Azure Synapse Analytics is een relationeel database archief dat is geoptimaliseerd voor analytische werk belastingen. Het wordt geschaald op basis van gepartitioneerde tabellen. Tabellen kunnen worden gepartitioneerd op meerdere knoop punten. De knoop punten worden geselecteerd op het moment van maken. Ze kunnen worden geschaald na het feit, maar dat is een actief proces waarvoor gegevens verplaatsing nodig is. Zie [Compute in azure Synapse Analytics beheren](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md)voor meer informatie.

### <a name="apache-hbase"></a>Apache HBase

Apache HBase is een sleutel/waarde-archief dat beschikbaar is in azure HDInsight. Het is een open-source NoSQL-data base die is gebouwd op Hadoop en gemodelleerd na Google BigTable. HBase biedt wille keurige toegang en sterke consistentie voor grote hoeveel heden niet-gestructureerde en semi-gestructureerde gegevens.

Omdat HBase een schemaloze data base is, hoeft u geen kolommen en gegevens typen te definiëren voordat u ze gebruikt. Gegevens worden opgeslagen in de rijen van een tabel en worden gegroepeerd op kolom familie.

De open-source code wordt lineair geschaald om petabytes aan gegevens op duizenden knooppunten te verwerken. HBase is afhankelijk van gegevens redundantie, batch verwerking en andere functies die worden geleverd door gedistribueerde toepassingen in de Hadoop-omgeving.

HBase is een goede bestemming voor sensor-en logboek gegevens voor toekomstige analyse.

HBase Adaptive is afhankelijk van het aantal knoop punten in het HDInsight-cluster.

### <a name="azure-sql-databases"></a>Azure SQL-data bases

Azure biedt drie relationele data bases voor PaaS:

* [Azure SQL database](../../azure-sql/database/sql-database-paas-overview.md) is een implementatie van Microsoft SQL Server. Zie [prestaties afstemmen in Azure SQL database](../../azure-sql/database/performance-guidance.md)voor meer informatie over prestaties.
* [Azure database for MySQL](../../mysql/overview.md)  is een implementatie van Oracle MySQL.
* [Azure database for PostgreSQL](../../postgresql/quickstart-create-server-database-portal.md) is een implementatie van postgresql.

Voeg meer CPU en geheugen toe om deze producten te schalen.  U kunt er ook voor kiezen om Premium-schijven met de producten te gebruiken voor betere I/O-prestaties.

## <a name="azure-analysis-services"></a>Azure Analysis Services

Azure Analysis Services is een analytische gegevens engine die wordt gebruikt in de besluit ondersteuning en Business Analytics. Het biedt de analytische gegevens voor zakelijke rapporten en client toepassingen zoals Power BI. De analytische gegevens werken ook met Excel, SQL Server Reporting Services rapporten en andere hulp middelen voor gegevens visualisatie.

Schaal analyse kubussen door de lagen voor elke afzonderlijke kubus te wijzigen. Zie [Azure Analysis Services prijzen](https://azure.microsoft.com/pricing/details/analysis-services/)voor meer informatie.

## <a name="extract-and-load"></a>Uitpakken en laden

Wanneer de gegevens zich in azure bevindt, kunt u veel services gebruiken om deze uit te pakken en in andere producten te laden. HDInsight ondersteunt Sqoop en Flume.

### <a name="apache-sqoop"></a>Apache Sqoop

Apache Sqoop is een hulp programma dat is ontworpen voor het efficiënt overbrengen van gegevens tussen gestructureerde, semi-gestructureerde en ongestructureerde gegevens bronnen.

Sqoop maakt gebruik van MapReduce voor het importeren en exporteren van de gegevens, om parallelle bewerkingen en fout tolerantie mogelijk te maken.

### <a name="apache-flume"></a>Apache Flume

Apache Flume is een gedistribueerde, betrouw bare service waarmee u op efficiënte wijze grote hoeveel heden logboek gegevens kunt verzamelen, samen voegen en verplaatsen. De flexibele architectuur is gebaseerd op streaming-gegevens stromen. Flume is robuust en fout tolerant met instel bare-betrouwbaarheids mechanismen. Er zijn veel methoden voor failover en herstel. Flume maakt gebruik van een eenvoudig uitbreidbaar gegevens model waarmee online en analytische toepassingen kunnen worden gebruikt.

Apache Flume kan niet worden gebruikt met Azure HDInsight. Maar een on-premises Hadoop-installatie kan Flume gebruiken om gegevens te verzenden naar Azure Blob-opslag of Azure Data Lake Storage. Zie [using Apache Flume with HDInsight](https://web.archive.org/web/20190217104751/https://blogs.msdn.microsoft.com/bigdatasupport/2014/03/18/using-apache-flume-with-hdinsight/)(Engelstalig) voor meer informatie.

## <a name="transform"></a>Transformeren

Nadat de gegevens op de gekozen locatie aanwezig zijn, moet u deze opschonen, combi neren of voorbereiden voor een specifiek gebruiks patroon. Hive-, Pig-en Spark SQL zijn allemaal een goede keuze voor dat soort werk. Ze worden allemaal ondersteund op HDInsight.

## <a name="next-steps"></a>Volgende stappen

- [Apache Hive als ETL-hulp programma gebruiken](apache-hadoop-using-apache-hive-as-an-etl-tool.md)
- [Azure Data Lake Storage Gen2 gebruiken met Azure HDInsight-clusters](../hdinsight-hadoop-use-data-lake-storage-gen2.md)
- [Gegevens verplaatsen van Azure SQL Database naar een Apache Hive tabel](./apache-hadoop-use-sqoop-mac-linux.md)
