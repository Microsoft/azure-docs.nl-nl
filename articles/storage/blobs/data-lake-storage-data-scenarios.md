---
title: Gegevens scenario's met betrekking tot Azure Data Lake Storage Gen2 | Microsoft Docs
description: Inzicht krijgen in de verschillende scenario's en hulpprogram ma's die gebruikmaken van welke gegevens kunnen worden opgenomen, verwerkt, gedownload en gevisualiseerd in Data Lake Storage Gen2 (voorheen bekend als Azure Data Lake Store)
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 02/14/2020
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: bffa7894f7603f95c4840019be5e5670797881df
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98873243"
---
# <a name="using-azure-data-lake-storage-gen2-for-big-data-requirements"></a>Azure Data Lake Storage Gen2 gebruiken voor big data vereisten

Er zijn vier belang rijke fasen in het big data verwerken:

> [!div class="checklist"]
> * Grote hoeveel heden gegevens opnemen in een gegevens archief, in realtime of in batches
> * De gegevens verwerken
> * De gegevens downloaden
> * De gegevens visualiseren

In dit artikel worden de opties en hulpprogram ma's voor elke verwerkings fase gemarkeerd.

Voor een volledige lijst met Azure-Services die u kunt gebruiken met Azure Data Lake Storage Gen2 raadpleegt u [Azure data Lake Storage integreren met Azure-Services](./data-lake-storage-supported-azure-services.md)

## <a name="ingest-the-data-into-data-lake-storage-gen2"></a>De gegevens opnemen in Data Lake Storage Gen2

In deze sectie worden de verschillende gegevens bronnen en de verschillende manieren beschreven waarop de gegevens kunnen worden opgenomen in een Data Lake Storage Gen2-account.

![Gegevens opnemen in Data Lake Storage Gen2](./media/data-lake-storage-data-scenarios/ingest-data.png "Gegevens opnemen in Data Lake Storage Gen2")

### <a name="ad-hoc-data"></a>Ad hoc gegevens

Dit vertegenwoordigt kleinere gegevens sets die worden gebruikt voor het maken van een prototype van een big data-toepassing. Er zijn verschillende manieren om ad hoc gegevens op te nemen, afhankelijk van de bron van de gegevens. 

Hier volgt een lijst met hulpprogram ma's die u kunt gebruiken om ad hoc gegevens op te nemen.

| Gegevensbron | Het opnemen met |
| --- | --- |
| Lokale computer |[Azure PowerShell](data-lake-storage-directory-file-acl-powershell.md)<br><br>[Azure-CLI](data-lake-storage-directory-file-acl-cli.md)<br><br>[Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)<br><br>[Hulpprogramma AzCopy](../common/storage-use-azcopy-v10.md)|
| Azure Storage Blob |[Azure Data Factory](../../data-factory/connector-azure-data-lake-store.md)<br><br>[Hulpprogramma AzCopy](../common/storage-use-azcopy-v10.md)<br><br>[DistCp uitgevoerd op HDInsight-cluster](data-lake-storage-use-distcp.md)|

### <a name="streamed-data"></a>Gestreamde gegevens

Dit duidt op gegevens die kunnen worden gegenereerd door verschillende bronnen, zoals toepassingen, apparaten, Sens oren, enzovoort. Deze gegevens kunnen worden opgenomen in Data Lake Storage Gen2 door diverse hulpprogram ma's. In deze hulpprogram ma's worden de gegevens doorgaans in realtime vastgelegd en verwerkt, en worden de gebeurtenissen in batches naar Data Lake Storage Gen2 geschreven, zodat ze verder kunnen worden verwerkt.

Hier volgt een lijst met hulpprogram ma's die u kunt gebruiken om gestreamde gegevens op te nemen.

|Hulpprogramma | Hulp |
|---|--|
|Azure Stream Analytics|[Snelstart: Een Stream Analytics-taak maken via Azure Portal](../../stream-analytics/stream-analytics-quick-create-portal.md) <br> [Uitgaand naar Azure Data Lake Gen2](../../stream-analytics/stream-analytics-define-outputs.md)|
|Azure HDInsight Storm | [Schrijven naar Apache Hadoop HDFS van Apache Storm op HDInsight](../../hdinsight/storm/apache-storm-write-data-lake-store.md) |

### <a name="relational-data"></a>Relationele gegevens

U kunt ook gegevens van relationele data bases bron. Over een bepaalde tijd verzamelen relationele data bases enorme hoeveel heden gegevens die belang rijke inzichten kunnen geven als ze worden verwerkt via een big data pijp lijn. U kunt de volgende hulpprogram ma's gebruiken om dergelijke gegevens naar Data Lake Storage Gen2 te verplaatsen.

Hier volgt een lijst met hulpprogram ma's die u kunt gebruiken voor het opnemen van relationele gegevens.

|Hulpprogramma | Hulp |
|---|--|
|Azure Data Factory | [Kopieeractiviteit in Azure Data Factory](../../data-factory/copy-activity-overview.md) |

### <a name="web-server-log-data-upload-using-custom-applications"></a>Webserver logboek gegevens (uploaden met aangepaste toepassingen)

Dit type gegevensset wordt specifiek genoemd, omdat analyse van webserver logboek gegevens een veelvoorkomende use-case is voor big data toepassingen en grote hoeveel heden logboek bestanden moeten worden geüpload naar Data Lake Storage Gen2. U kunt een van de volgende hulpprogram ma's gebruiken om uw eigen scripts of toepassingen te schrijven voor het uploaden van gegevens.

Hier volgt een lijst met hulpprogram ma's die u kunt gebruiken om webserver logboek gegevens op te nemen.

|Hulpprogramma | Hulp |
|---|--|
|Azure Data Factory | [Kopieeractiviteit in Azure Data Factory](../../data-factory/copy-activity-overview.md)  |
|Azure CLI|[Azure-CLI](data-lake-storage-directory-file-acl-cli.md)|
|Azure PowerShell|[Azure PowerShell](data-lake-storage-directory-file-acl-powershell.md)|

Voor het uploaden van webserver-logboek gegevens, en voor het uploaden van andere soorten gegevens (bijvoorbeeld Social gevoel-gegevens), is het een goede benadering om uw eigen aangepaste scripts/toepassingen te schrijven, omdat u hiermee de flexibiliteit hebt om uw gegevens te uploaden die deel uitmaken van uw grotere big data-toepassing. In sommige gevallen kan deze code de vorm hebben van een script of een eenvoudig opdracht regel programma. In andere gevallen kan de code worden gebruikt voor de integratie van big data verwerking in een bedrijfs toepassing of oplossing.

### <a name="data-associated-with-azure-hdinsight-clusters"></a>Gegevens die zijn gekoppeld aan Azure HDInsight-clusters

De meeste HDInsight-cluster typen (Hadoop, HBase, Storm) ondersteunen Data Lake Storage Gen2 als opslag plaats voor gegevens. HDInsight-clusters hebben toegang tot gegevens van Azure Storage-blobs (WASB). Voor betere prestaties kunt u de gegevens van WASB kopiëren naar een Data Lake Storage Gen2-account dat aan het cluster is gekoppeld. U kunt de volgende hulpprogram ma's gebruiken om de gegevens te kopiëren.

Hier volgt een lijst met hulpprogram ma's die u kunt gebruiken om gegevens op te nemen die zijn gekoppeld aan HDInsight-clusters.

|Hulpprogramma | Hulp |
|---|--|
|Apache DistCp | [Gebruik DistCp om gegevens te kopiëren tussen Azure Storage blobs en Azure Data Lake Storage Gen2](./data-lake-storage-use-distcp.md) |
|Hulpprogramma AzCopy | [Gegevens overdragen met de AzCopy](../common/storage-use-azcopy-v10.md) |
|Azure Data Factory | [Gegevens kopiëren van of naar Azure Data Lake Storage Gen2 met behulp van Azure Data Factory](../../data-factory/load-azure-data-lake-storage-gen2.md) |

### <a name="data-stored-in-on-premises-or-iaas-hadoop-clusters"></a>Gegevens die zijn opgeslagen in on-premises of IaaS Hadoop-clusters

Grote hoeveel heden gegevens kunnen worden opgeslagen in bestaande Hadoop-clusters, lokaal op computers met HDFS. De Hadoop-clusters kunnen zich in een on-premises implementatie bevinden of bevinden zich in een IaaS-cluster op Azure. Er kunnen vereisten zijn om dergelijke gegevens te kopiëren naar Azure Data Lake Storage Gen2 voor een eenmalige benadering of op een regel matige wijze. Er zijn verschillende opties die u kunt gebruiken om dit te krijgen. Hieronder vindt u een lijst met alternatieven en de bijbehorende afwegingen.

| Methode | Details | Voordelen | Overwegingen |
| --- | --- | --- | --- |
| Azure Data Factory (ADF) gebruiken om gegevens rechtstreeks vanuit Hadoop-clusters naar Azure Data Lake Storage Gen2 te kopiëren |[ADF ondersteunt HDFS als een gegevens bron](../../data-factory/connector-hdfs.md) |ADF biedt kant-en-klare ondersteuning voor HDFS en de eerste klasse end-to-end-beheer en-bewaking |Vereist dat Data Management Gateway on-premises of in het IaaS-cluster wordt geïmplementeerd |
| Gebruik Distcp om gegevens van Hadoop naar Azure Storage te kopiëren. Kopieer vervolgens gegevens van Azure Storage naar Data Lake Storage Gen2 met behulp van het juiste mechanisme. |U kunt gegevens kopiëren van Azure Storage naar Data Lake Storage Gen2 met behulp van: <ul><li>[Azure Data Factory](../../data-factory/copy-activity-overview.md)</li><li>[Hulpprogramma AzCopy](../common/storage-use-azcopy-v10.md)</li><li>[Apache DistCp uitgevoerd op HDInsight-clusters](data-lake-storage-use-distcp.md)</li></ul> |U kunt open source-hulpprogram ma's gebruiken. |Proces voor meerdere stappen waarbij meerdere technologieën zijn vereist |

### <a name="really-large-datasets"></a>Zeer grote gegevens sets

Voor het uploaden van gegevens sets die zich in verschillende terabytes bebereiken, kunnen de hierboven beschreven methoden langzaam en kostbaar zijn. In dergelijke gevallen kunt u Azure ExpressRoute gebruiken.  

Met Azure ExpressRoute kunt u particuliere verbindingen maken tussen Azure-data centers en infra structuur op uw locatie. Dit biedt een betrouw bare optie voor het overbrengen van grote hoeveel heden gegevens. Zie de [documentatie van Azure ExpressRoute](../../expressroute/expressroute-introduction.md)voor meer informatie.

## <a name="process-the-data"></a>De gegevens verwerken

Zodra de gegevens beschikbaar zijn in Data Lake Storage Gen2 kunt u analyses uitvoeren op die gegevens met behulp van de ondersteunde big data toepassingen. 

![Gegevens in Data Lake Storage Gen2 analyseren](./media/data-lake-storage-data-scenarios/analyze-data.png "Gegevens in Data Lake Storage Gen2 analyseren")

Hier volgt een lijst met hulpprogram ma's die u kunt gebruiken om taken voor gegevens analyse uit te voeren voor gegevens die zijn opgeslagen in Data Lake Storage Gen2.

|Hulpprogramma | Hulp |
|---|--|
|Azure HDInsight | [Azure Data Lake Storage Gen2 gebruiken met Azure HDInsight-clusters](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md) |
|Azure Databricks | [Azure Data Lake Storage Gen2](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html)<br><br>[Snelstartgids: gegevens in Azure Data Lake Storage Gen2 analyseren met behulp van Azure Databricks](./data-lake-storage-quickstart-create-databricks-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)<br><br>[Zelfstudie: Gegevens extraheren, transformeren en laden met behulp van Azure Databricks](/azure/databricks/scenarios/databricks-extract-load-sql-data-warehouse?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|

## <a name="visualize-the-data"></a>De gegevens visualiseren

Gebruik de Power BI-connector om visuele weer gaven te maken van gegevens die zijn opgeslagen in Data Lake Storage Gen2. Zie [gegevens analyseren in azure data Lake Storage Gen2 met behulp van Power bi](/power-query/connectors/datalakestorage).

## <a name="download-the-data"></a>De gegevens downloaden

U kunt ook gegevens downloaden of verplaatsen van Azure Data Lake Storage Gen2 voor scenario's zoals:

* Verplaats gegevens naar andere opslag plaatsen naar de interface met uw bestaande gegevens verwerkings pijplijnen. Het is bijvoorbeeld mogelijk dat u gegevens wilt verplaatsen van Data Lake Storage Gen2 naar Azure SQL Database of een SQL Server exemplaar.

* Down load gegevens naar uw lokale computer voor verwerking in IDE-omgevingen tijdens het maken van prototypen van toepassingen.

![Uitgaande gegevens van Data Lake Storage Gen2](./media/data-lake-storage-data-scenarios/egress-data.png "Uitgaande gegevens van Data Lake Storage Gen2")

Hier volgt een lijst met hulpprogram ma's die u kunt gebruiken om gegevens te downloaden van Data Lake Storage Gen2.

|Hulpprogramma | Hulp |
|---|--|
|Azure Data Factory | [Kopieeractiviteit in Azure Data Factory](../../data-factory/copy-activity-overview.md) |
|Apache DistCp | [Gebruik DistCp om gegevens te kopiëren tussen Azure Storage blobs en Azure Data Lake Storage Gen2](./data-lake-storage-use-distcp.md) |
|Azure Storage Explorer|[Azure Storage Explorer gebruiken voor het beheren van adreslijsten, bestanden en ACL's in Azure Data Lake Storage Gen2](data-lake-storage-explorer.md)|
|Hulpprogramma AzCopy|[Gegevens overdragen met AzCopy en Blob Storage](../common/storage-use-azcopy-v10.md#transfer-data)|
