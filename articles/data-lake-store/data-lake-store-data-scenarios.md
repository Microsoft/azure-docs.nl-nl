---
title: Gegevens scenario's met betrekking tot Data Lake Storage Gen1 | Microsoft Docs
description: Inzicht krijgen in de verschillende scenario's en hulpprogram ma's die gebruikmaken van welke gegevens kunnen worden opgenomen, verwerkt, gedownload en gevisualiseerd in Data Lake Storage Gen1 (voorheen bekend als Azure Data Lake Store)
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: twooley
ms.openlocfilehash: 0bd895b0c19293a199b2a9b135915b7dc45b9e5e
ms.sourcegitcommit: 75041f1bce98b1d20cd93945a7b3bd875e6999d0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98702494"
---
# <a name="using-azure-data-lake-storage-gen1-for-big-data-requirements"></a>Azure Data Lake Storage Gen1 gebruiken voor big data vereisten

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

Er zijn vier belang rijke fasen in het big data verwerken:

* Grote hoeveel heden gegevens opnemen in een gegevens archief, in realtime of in batches
* De gegevens verwerken
* De gegevens downloaden
* De gegevens visualiseren

In dit artikel gaan we deze fasen bekijken met betrekking tot Azure Data Lake Storage Gen1 om inzicht te krijgen in de opties en hulpprogram ma's die beschikbaar zijn om te voldoen aan uw big data behoeften.

## <a name="ingest-data-into-data-lake-storage-gen1"></a>Gegevens opnemen in Data Lake Storage Gen1
In deze sectie worden de verschillende gegevens bronnen en de verschillende manieren beschreven waarop de gegevens kunnen worden opgenomen in een Data Lake Storage Gen1-account.

![Gegevens opnemen in Data Lake Storage Gen1](./media/data-lake-store-data-scenarios/ingest-data.png "Gegevens opnemen in Data Lake Storage Gen1")

### <a name="ad-hoc-data"></a>Ad hoc gegevens
Dit vertegenwoordigt kleinere gegevens sets die worden gebruikt voor het maken van een prototype van een big data-toepassing. Er zijn verschillende manieren om ad hoc gegevens op te nemen, afhankelijk van de bron van de gegevens.

| Gegevensbron | Het opnemen met |
| --- | --- |
| Lokale computer |<ul> <li>[Azure-portal](data-lake-store-get-started-portal.md)</li> <li>[Azure PowerShell](data-lake-store-get-started-powershell.md)</li> <li>[Azure-CLI](data-lake-store-get-started-cli-2.0.md)</li> <li>[Data Lake-Hulpprogram Ma's voor Visual Studio gebruiken](../data-lake-analytics/data-lake-analytics-data-lake-tools-get-started.md) </li></ul> |
| Azure Storage Blob |<ul> <li>[Azure Data Factory](../data-factory/connector-azure-data-lake-store.md)</li> <li>[Hulp programma AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)</li><li>[DistCp uitgevoerd op HDInsight-cluster](data-lake-store-copy-data-wasb-distcp.md)</li> </ul> |

### <a name="streamed-data"></a>Gestreamde gegevens
Dit duidt op gegevens die kunnen worden gegenereerd door verschillende bronnen, zoals toepassingen, apparaten, Sens oren, enzovoort. Deze gegevens kunnen worden opgenomen in Data Lake Storage Gen1 door diverse hulpprogram ma's. In deze hulpprogram ma's worden de gegevens doorgaans in realtime vastgelegd en verwerkt, en worden de gebeurtenissen in batches naar Data Lake Storage Gen1 geschreven, zodat ze verder kunnen worden verwerkt.

Hieronder vindt u de hulpprogram ma's die u kunt gebruiken:

* [Azure stream Analytics](../stream-analytics/stream-analytics-define-outputs.md) gebeurtenissen die in Event hubs zijn opgenomen, kunnen naar Azure data Lake Storage gen1 worden geschreven met behulp van een Azure data Lake Storage gen1 uitvoer.
* [Azure HDInsight Storm](../hdinsight/storm/apache-storm-write-data-lake-store.md) : u kunt gegevens rechtstreeks naar Data Lake Storage gen1 schrijven vanuit het Storm-cluster.
* [EventProcessorHost](../event-hubs/event-hubs-dotnet-standard-getstarted-send.md) : u kunt gebeurtenissen ontvangen van Event hubs en deze vervolgens naar Data Lake Storage gen1 schrijven met behulp van de [Data Lake Storage gen1 .NET SDK](data-lake-store-get-started-net-sdk.md).

### <a name="relational-data"></a>Relationele gegevens
U kunt ook gegevens van relationele data bases bron. Over een bepaalde tijd verzamelen relationele data bases enorme hoeveel heden gegevens die belang rijke inzichten kunnen geven als ze worden verwerkt via een big data pijp lijn. U kunt de volgende hulpprogram ma's gebruiken om dergelijke gegevens naar Data Lake Storage Gen1 te verplaatsen.

* [Apache Sqoop](data-lake-store-data-transfer-sql-sqoop.md)
* [Azure Data Factory](../data-factory/copy-activity-overview.md)

### <a name="web-server-log-data-upload-using-custom-applications"></a>Webserver logboek gegevens (uploaden met aangepaste toepassingen)
Dit type gegevensset wordt specifiek genoemd, omdat analyse van webserver logboek gegevens een veelvoorkomende use-case is voor big data toepassingen en grote hoeveel heden logboek bestanden moeten worden geüpload naar Data Lake Storage Gen1. U kunt een van de volgende hulpprogram ma's gebruiken om uw eigen scripts of toepassingen te schrijven voor het uploaden van gegevens.

* [Azure-CLI](data-lake-store-get-started-cli-2.0.md)
* [Azure PowerShell](data-lake-store-get-started-powershell.md)
* [Azure Data Lake Storage Gen1 .NET SDK](data-lake-store-get-started-net-sdk.md)
* [Azure Data Factory](../data-factory/copy-activity-overview.md)

Voor het uploaden van webserver-logboek gegevens, en voor het uploaden van andere soorten gegevens (bijvoorbeeld Social gevoel-gegevens), is het een goede benadering om uw eigen aangepaste scripts/toepassingen te schrijven, omdat u hiermee de flexibiliteit hebt om uw gegevens te uploaden die deel uitmaken van uw grotere big data-toepassing. In sommige gevallen kan deze code de vorm hebben van een script of een eenvoudig opdracht regel programma. In andere gevallen kan de code worden gebruikt voor de integratie van big data verwerking in een bedrijfs toepassing of oplossing.

### <a name="data-associated-with-azure-hdinsight-clusters"></a>Gegevens die zijn gekoppeld aan Azure HDInsight-clusters
De meeste HDInsight-cluster typen (Hadoop, HBase, Storm) ondersteunen Data Lake Storage Gen1 als opslag plaats voor gegevens. HDInsight-clusters hebben toegang tot gegevens van Azure Storage-blobs (WASB). Voor betere prestaties kunt u de gegevens van WASB kopiëren naar een Data Lake Storage Gen1-account dat aan het cluster is gekoppeld. U kunt de volgende hulpprogram ma's gebruiken om de gegevens te kopiëren.

* [Apache DistCp](data-lake-store-copy-data-wasb-distcp.md)
* [AdlCopy-service](data-lake-store-copy-data-azure-storage-blob.md)
* [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md)

### <a name="data-stored-in-on-premises-or-iaas-hadoop-clusters"></a>Gegevens die zijn opgeslagen in on-premises of IaaS Hadoop-clusters
Grote hoeveel heden gegevens kunnen worden opgeslagen in bestaande Hadoop-clusters, lokaal op computers met HDFS. De Hadoop-clusters kunnen zich in een on-premises implementatie bevinden of bevinden zich in een IaaS-cluster op Azure. Er kunnen vereisten zijn om dergelijke gegevens te kopiëren naar Azure Data Lake Storage Gen1 voor een eenmalige benadering of op een regel matige wijze. Er zijn verschillende opties die u kunt gebruiken om dit te krijgen. Hieronder vindt u een lijst met alternatieven en de bijbehorende afwegingen.

| Methode | Details | Voordelen | Overwegingen |
| --- | --- | --- | --- |
| Azure Data Factory (ADF) gebruiken om gegevens rechtstreeks vanuit Hadoop-clusters naar Azure Data Lake Storage Gen1 te kopiëren |[ADF ondersteunt HDFS als een gegevens bron](../data-factory/connector-hdfs.md) |ADF biedt kant-en-klare ondersteuning voor HDFS en de eerste klasse end-to-end-beheer en-bewaking |Vereist dat Data Management Gateway on-premises of in het IaaS-cluster wordt geïmplementeerd |
| Gegevens exporteren uit Hadoop als bestanden. Kopieer de bestanden vervolgens naar Azure Data Lake Storage Gen1 met behulp van het juiste mechanisme. |U kunt bestanden kopiëren naar Azure Data Lake Storage Gen1 met behulp van: <ul><li>[Azure PowerShell voor Windows-besturings systeem](data-lake-store-get-started-powershell.md)</li><li>[Azure-CLI](data-lake-store-get-started-cli-2.0.md)</li><li>Aangepaste app met een Data Lake Storage Gen1 SDK</li></ul> |Snel om aan de slag te gaan. Kan aangepaste uploads uitvoeren |Proces voor meerdere stappen waarbij meerdere technologieën betrokken zijn. Het beheer en de bewaking groeien in de loop van de tijd, op basis van de aangepaste aard van de hulpprogram ma's |
| Gebruik Distcp om gegevens van Hadoop naar Azure Storage te kopiëren. Kopieer vervolgens gegevens van Azure Storage naar Data Lake Storage Gen1 met behulp van het juiste mechanisme. |U kunt gegevens kopiëren van Azure Storage naar Data Lake Storage Gen1 met behulp van: <ul><li>[Azure Data Factory](../data-factory/copy-activity-overview.md)</li><li>[Hulp programma AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)</li><li>[Apache DistCp uitgevoerd op HDInsight-clusters](data-lake-store-copy-data-wasb-distcp.md)</li></ul> |U kunt open source-hulpprogram ma's gebruiken. |Proces voor meerdere stappen waarbij meerdere technologieën zijn vereist |

### <a name="really-large-datasets"></a>Zeer grote gegevens sets
Voor het uploaden van gegevens sets die zich in verschillende terabytes bebereiken, kunnen de hierboven beschreven methoden langzaam en kostbaar zijn. In dergelijke gevallen kunt u de onderstaande opties gebruiken.

* **Met behulp van Azure ExpressRoute**. Met Azure ExpressRoute kunt u particuliere verbindingen maken tussen Azure-data centers en infra structuur op uw locatie. Dit biedt een betrouw bare optie voor het overbrengen van grote hoeveel heden gegevens. Zie de [documentatie van Azure ExpressRoute](../expressroute/expressroute-introduction.md)voor meer informatie.
* **' Offline ' uploaden van gegevens**. Als Azure ExpressRoute niet kan worden gebruikt om welke reden dan ook, kunt u de [Azure import/export-service](../import-export/storage-import-export-service.md) gebruiken om harde schijven met uw gegevens naar een Azure-Data Center te verzenden. Uw gegevens worden eerst geüpload naar Azure Storage blobs. U kunt vervolgens [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md) -of [AdlCopy-hulp programma](data-lake-store-copy-data-azure-storage-blob.md) gebruiken om gegevens van Azure Storage blobs naar Data Lake Storage gen1 te kopiëren.

  > [!NOTE]
  > Bij gebruik van de import/export-service, mag de bestands grootte op de schijven die u naar Azure Data Center verzendt, niet groter zijn dan 195 GB.
  >
  >

## <a name="process-data-stored-in-data-lake-storage-gen1"></a>Gegevens verwerken die zijn opgeslagen in Data Lake Storage Gen1
Zodra de gegevens beschikbaar zijn in Data Lake Storage Gen1 kunt u analyses uitvoeren op die gegevens met behulp van de ondersteunde big data toepassingen. Op dit moment kunt u Azure HDInsight en Azure Data Lake Analytics gebruiken om taken voor gegevens analyse uit te voeren op de gegevens die zijn opgeslagen in Data Lake Storage Gen1.

![Gegevens in Data Lake Storage Gen1 analyseren](./media/data-lake-store-data-scenarios/analyze-data.png "Gegevens in Data Lake Storage Gen1 analyseren")

U kunt de volgende voor beelden bekijken.

* [Een HDInsight-cluster met Data Lake Storage Gen1 als opslag maken](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Azure Data Lake Analytics gebruiken met Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)

## <a name="download-data-from-data-lake-storage-gen1"></a>Gegevens downloaden van Data Lake Storage Gen1
U kunt ook gegevens downloaden of verplaatsen van Azure Data Lake Storage Gen1 voor scenario's zoals:

* Verplaats gegevens naar andere opslag plaatsen naar de interface met uw bestaande gegevens verwerkings pijplijnen. Het is bijvoorbeeld mogelijk dat u gegevens wilt verplaatsen van Data Lake Storage Gen1 naar Azure SQL Database of SQL Server.
* Down load gegevens naar uw lokale computer voor verwerking in IDE-omgevingen tijdens het maken van prototypen van toepassingen.

![Uitgaande gegevens van Data Lake Storage Gen1](./media/data-lake-store-data-scenarios/egress-data.png "Uitgaande gegevens van Data Lake Storage Gen1")

In dergelijke gevallen kunt u een van de volgende opties gebruiken:

* [Apache Sqoop](data-lake-store-data-transfer-sql-sqoop.md)
* [Azure Data Factory](../data-factory/copy-activity-overview.md)
* [Apache DistCp](data-lake-store-copy-data-wasb-distcp.md)

U kunt ook de volgende methoden gebruiken om uw eigen script/toepassing te schrijven om gegevens van Data Lake Storage Gen1 te downloaden.

* [Azure-CLI](data-lake-store-get-started-cli-2.0.md)
* [Azure PowerShell](data-lake-store-get-started-powershell.md)
* [Azure Data Lake Storage Gen1 .NET SDK](data-lake-store-get-started-net-sdk.md)

## <a name="visualize-data-in-data-lake-storage-gen1"></a>Gegevens visualiseren in Data Lake Storage Gen1
U kunt een combi natie van Services gebruiken om visuele weer gaven te maken van gegevens die zijn opgeslagen in Data Lake Storage Gen1.

![Gegevens visualiseren in Data Lake Storage Gen1](./media/data-lake-store-data-scenarios/visualize-data.png "Gegevens visualiseren in Data Lake Storage Gen1")

* U kunt beginnen met [Azure Data Factory om gegevens te verplaatsen van data Lake Storage gen1 naar Azure Synapse Analytics](../data-factory/copy-activity-overview.md)
* Daarna kunt u [Power bi integreren met Azure Synapse Analytics](/power-bi/connect-data/service-azure-sql-data-warehouse-with-direct-connect) om een visuele weer gave van de gegevens te maken.