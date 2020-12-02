---
title: Data Lake Storage Gen1 integreren met andere Azure-Services
description: Begrijp hoe u Azure Data Lake Storage Gen1 kunt integreren met andere Azure-Services.
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 929853f4dbedca7034c8e2a51e6231651a2dd08f
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96461657"
---
# <a name="integrating-azure-data-lake-storage-gen1-with-other-azure-services"></a>Azure Data Lake Storage Gen1 integreren met andere Azure-Services
Azure Data Lake Storage Gen1 kunnen worden gebruikt in combi natie met andere Azure-Services om een breed scala aan scenario's mogelijk te maken. In het volgende artikel worden de services vermeld die Data Lake Storage Gen1 kunnen worden geïntegreerd met.

## <a name="use-data-lake-storage-gen1-with-azure-hdinsight"></a>Data Lake Storage Gen1 gebruiken met Azure HDInsight
U kunt een [Azure HDInsight](https://azure.microsoft.com/documentation/learning-paths/hdinsight-self-guided-hadoop-training/) -cluster inrichten dat gebruikmaakt van data Lake Storage gen1 als de opslag die compatibel is met HDFS. Voor deze release kunt u voor Hadoop-en Storm-clusters in Windows en Linux alleen Data Lake Storage Gen1 gebruiken als extra opslag. Dergelijke clusters gebruiken nog steeds Azure Storage (WASB) als de standaard opslag. Voor HBase-clusters in Windows en Linux kunt u echter Data Lake Storage Gen1 gebruiken als de standaard opslag of extra opslag, of beide.

Voor instructies over het inrichten van een HDInsight-cluster met Data Lake Storage Gen1 raadpleegt u:

* [Een HDInsight-cluster inrichten met Data Lake Storage Gen1 met behulp van Azure Portal](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Een HDInsight-cluster met Data Lake Storage Gen1 als standaard opslag inrichten met Azure PowerShell](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
* [Een HDInsight-cluster inrichten met Data Lake Storage Gen1 als extra opslag met behulp van Azure PowerShell](data-lake-store-hdinsight-hadoop-use-powershell.md)

## <a name="use-data-lake-storage-gen1-with-azure-data-lake-analytics"></a>Data Lake Storage Gen1 gebruiken met Azure Data Lake Analytics
Met [Azure data Lake Analytics](../data-lake-analytics/data-lake-analytics-overview.md) kunt u werken met Big data op Cloud schaal. Er worden dynamisch resources ingericht en u kunt analyses uitvoeren op terabytes of zelfs met een hoeveelheid gegevens die in een aantal ondersteunde gegevens bronnen kunnen worden opgeslagen. een van deze wordt Data Lake Storage Gen1. Data Lake Analytics is speciaal geoptimaliseerd voor gebruik met Data Lake Storage Gen1-het hoogste niveau van prestaties, door Voer en parallel Lise ring voor u big data workloads.

Zie [aan de slag met data Lake Analytics met data Lake Storage gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)voor instructies over het gebruik van Data Lake Analytics met data Lake Storage gen1.

## <a name="use-data-lake-storage-gen1-with-azure-data-factory"></a>Data Lake Storage Gen1 gebruiken met Azure Data Factory
U kunt [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) gebruiken om gegevens op te nemen uit Azure-tabellen, Azure SQL database, Azure SQL data warehouse, Azure Storage-blobs en on-premises databases. De eerste klasse burger in het Azure-ecosysteem kan Azure Data Factory worden gebruikt om de opname van gegevens van deze bron te organiseren op Data Lake Storage Gen1.

Zie [gegevens verplaatsen van en naar Data Lake Storage gen1 met Data Factory](../data-factory/connector-azure-data-lake-store.md)voor instructies over het gebruik van Azure Data Factory met data Lake Storage gen1.

## <a name="copy-data-from-azure-storage-blobs-into-data-lake-storage-gen1"></a>Gegevens kopiëren van Azure Storage-blobs naar Data Lake Storage Gen1
Azure Data Lake Storage Gen1 biedt een opdracht regel programma, AdlCopy, waarmee u gegevens van Azure Blob Storage kunt kopiëren naar een Data Lake Storage Gen1-account. Zie [gegevens kopiëren van Azure Storage-blobs naar Data Lake Storage gen1](data-lake-store-copy-data-azure-storage-blob.md)voor meer informatie.

## <a name="copy-data-between-azure-sql-database-and-data-lake-storage-gen1"></a>Gegevens kopiëren tussen Azure SQL Database en Data Lake Storage Gen1
U kunt Apache Sqoop gebruiken voor het importeren en exporteren van gegevens tussen Azure SQL Database en Data Lake Storage Gen1. Zie [gegevens kopiëren tussen data Lake Storage gen1 en Azure SQL database met behulp van Sqoop](data-lake-store-data-transfer-sql-sqoop.md)voor meer informatie.

## <a name="use-data-lake-storage-gen1-with-stream-analytics"></a>Data Lake Storage Gen1 gebruiken met Stream Analytics
U kunt Data Lake Storage Gen1 als een van de uitvoer gebruiken om gegevens op te slaan die worden gestreamd met behulp van Azure Stream Analytics. Zie [gegevens streamen van Azure Storage BLOB naar Data Lake Storage gen1 met behulp van Azure stream Analytics](data-lake-store-stream-analytics.md)voor meer informatie.

## <a name="use-data-lake-storage-gen1-with-power-bi"></a>Data Lake Storage Gen1 gebruiken met Power BI
U kunt Power BI gebruiken om gegevens te importeren uit een Data Lake Storage Gen1-account om de gegevens te analyseren en te visualiseren. Zie [gegevens analyseren in data Lake Storage gen1 met behulp van Power bi](data-lake-store-power-bi.md)voor meer informatie.

## <a name="use-data-lake-storage-gen1-with-data-catalog"></a>Data Lake Storage Gen1 gebruiken met Data Catalog
U kunt gegevens van Data Lake Storage Gen1 in het Azure Data Catalog registreren, zodat de gegevens binnen de hele organisatie kunnen worden gedetecteerd. Zie [gegevens van data Lake Storage gen1 in azure Data Catalog registreren](data-lake-store-with-data-catalog.md)voor meer informatie.

## <a name="use-data-lake-storage-gen1-with-sql-server-integration-services-ssis"></a>Data Lake Storage Gen1 gebruiken met SQL Server Integration Services (SSIS)
U kunt de Data Lake Storage Gen1 verbindings beheer in SSIS gebruiken om een SSIS-pakket te verbinden met Data Lake Storage Gen1. Zie [Data Lake Storage gen1 gebruiken met SSIS](/sql/integration-services/connection-manager/azure-data-lake-store-connection-manager)voor meer informatie.

## <a name="use-data-lake-storage-gen1-with-azure-synapse-analytics"></a>Data Lake Storage Gen1 gebruiken met Azure Synapse Analytics
U kunt poly Base gebruiken om gegevens van Data Lake Storage Gen1 in azure Synapse Analytics te laden. Zie [Data Lake Storage gen1 gebruiken met Azure Synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-load-from-azure-data-lake-store.md)voor meer informatie.

## <a name="use-data-lake-storage-gen1-with-azure-event-hubs"></a>Data Lake Storage Gen1 gebruiken met Azure Event Hubs
U kunt Azure Data Lake Storage Gen1 gebruiken om gegevens die zijn ontvangen door Azure Event Hubs te archiveren en vast te leggen. Zie [Data Lake Storage gen1 gebruiken met Azure Event hubs](data-lake-store-archive-eventhub-capture.md)voor meer informatie.

## <a name="see-also"></a>Zie ook
* [Overzicht van Azure Data Lake Storage Gen1](data-lake-store-overview.md)
* [Aan de slag met Data Lake Storage Gen1 met behulp van portal](data-lake-store-get-started-portal.md)
* [Aan de slag met Data Lake Storage Gen1 met behulp van Power shell](data-lake-store-get-started-powershell.md)