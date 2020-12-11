---
title: Azure-Services die ondersteuning bieden voor Azure Data Lake Storage Gen2 | Microsoft Docs
description: Meer informatie over de Azure-Services die met Azure Data Lake Storage Gen2 worden geïntegreerd
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 08/05/2020
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 3d3fb983f5e55f8b262be0925c2138b465413fc3
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/10/2020
ms.locfileid: "97095403"
---
# <a name="azure-services-that-support-azure-data-lake-storage-gen2"></a>Azure-Services die ondersteuning bieden voor Azure Data Lake Storage Gen2

U kunt Azure-Services gebruiken om gegevens op te nemen, analyses uit te voeren en visuele weer gaven te maken. In dit artikel vindt u een lijst met ondersteunde Azure-Services, wordt het bijbehorende ondersteunings niveau afgesloten en vindt u koppelingen naar artikelen die u helpen bij het gebruik van deze services met Azure Data Lake Storage Gen2.

## <a name="supported-azure-services"></a>Ondersteunde Azure-services

Deze tabel geeft een lijst van de Azure-Services die u kunt gebruiken met Azure Data Lake Storage Gen2. De items die in deze tabellen worden weer gegeven, worden in de loop van de tijd gewijzigd, omdat de ondersteuning blijft toenemen.

> [!NOTE]
> Ondersteunings niveau verwijst alleen naar de manier waarop de service wordt ondersteund met Data Lake Storage gen 2.

|Azure-service |Ondersteunings niveau |Azure AD |Gedeelde sleutel| Verwante artikelen: |
|---------------|-------------------|---|---|---|
|Azure Data Factory|Algemeen beschikbaar|Ja|Ja|[Gegevens laden in Azure Data Lake Storage Gen2 met Azure Data Factory](../../data-factory/load-azure-data-lake-storage-gen2.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Azure Databricks|Algemeen beschikbaar|Ja|Ja|[Gebruiken met Azure Databricks](https://docs.azuredatabricks.net/data/data-sources/azure/azure-datalake-gen2.html) <br> [Snelstartgids: gegevens in Azure Data Lake Storage Gen2 analyseren met behulp van Azure Databricks](data-lake-storage-quickstart-create-databricks-account.md) <br>[Zelfstudie: Gegevens extraheren, transformeren en laden met behulp van Azure Databricks](/azure/databricks/scenarios/databricks-extract-load-sql-data-warehouse) <br>[Zelf studie: toegang tot Data Lake Storage Gen2 gegevens met Azure Databricks met Spark](data-lake-storage-use-databricks-spark.md)|
|Azure Event Hub|Algemeen beschikbaar|Nee|Ja|[Gebeurtenissen vastleggen via Azure Event Hubs in Azure Blob Storage of Azure Data Lake Storage](../../event-hubs/event-hubs-capture-overview.md)|
|Azure Event Grid|Algemeen beschikbaar|Ja|Ja|[Zelfstudie: Het Data Lake Capture-patroon implementeren om een Databricks Delta-tabel bij te werken](data-lake-storage-events.md)|
|Azure Logic Apps|Algemeen beschikbaar|Nee|Ja|[Overzicht - Wat is Azure Logic Apps?](../../logic-apps/logic-apps-overview.md)|
|Azure Machine Learning|Algemeen beschikbaar|Ja|Ja|[Toegang tot gegevens in azure Storage-services](../../machine-learning/how-to-access-data.md)|
|Azure Stream Analytics|Algemeen beschikbaar|Ja|Ja|[Snelstart: Een Stream Analytics-taak maken via Azure Portal](../../stream-analytics/stream-analytics-quick-create-portal.md) <br> [Uitgaand naar Azure Data Lake Gen2](../../stream-analytics/stream-analytics-define-outputs.md)|
|Data Box|Algemeen beschikbaar|Nee|Ja|[Gebruik Azure Data Box om gegevens van een on-premises HDFS-Store te migreren naar Azure Storage](data-lake-storage-migrate-on-premises-hdfs-cluster.md)|
|HDInsight |Algemeen beschikbaar|Ja|Ja|[Azure Data Lake Storage Gen2 gebruiken met Azure HDInsight-clusters](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)<br>[De HDFS CLI gebruiken met Data Lake Storage Gen2](data-lake-storage-use-hdfs-data-lake-storage.md) <br>[Zelf studie: gegevens extra heren, transformeren en laden met behulp van Apache Hive in azure HDInsight](data-lake-storage-tutorial-extract-transform-load-hive.md)|
|IoT Hub |Algemeen beschikbaar|Ja|Ja|[IoT Hub bericht routering gebruiken om apparaat-naar-Cloud-berichten te verzenden naar verschillende eind punten](../../iot-hub/iot-hub-devguide-messages-d2c.md)|
|Power BI|Algemeen beschikbaar|Ja|Ja|[Gegevens analyseren in Data Lake Storage Gen2 met behulp van Power BI](/power-query/connectors/datalakestorage)|
|Azure Synapse Analytics (voorheen Azure SQL Data Warehouse)|Algemeen beschikbaar|Ja|Ja|[Gebruiken met Azure Synapse Analytics](../../azure-sql/database/vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|SQL Server Integration Services (SSIS)|Algemeen beschikbaar|Ja|Ja|[Verbindings beheer Azure Storage](/sql/integration-services/connection-manager/azure-storage-connection-manager)|
|Azure Data Explorer|Algemeen beschikbaar|Ja|Ja|[Query's uitvoeren op gegevens in Azure Data Lake met behulp van Azure Data Explorer](/azure/data-explorer/data-lake-query-data)|
|Azure Cognitive Search|Preview|Ja|Ja|[Index-en zoek Azure Data Lake Storage Gen2 documenten (preview-versie)](../../search/search-howto-index-azure-data-lake-storage.md)|
|Azure Content Delivery Network|Nog niet ondersteund|Niet van toepassing|Niet van toepassing|[Index-en zoek Azure Data Lake Storage Gen2 documenten (preview-versie)](../../cdn/cdn-overview.md)|
|Azure SQL Database|Nog niet ondersteund|Niet van toepassing|Niet van toepassing|[Wat is Azure SQL Database?](/azure/azure-sql/database/sql-database-paas-overview)|

## <a name="see-also"></a>Zie ook

- [Bekende problemen met Azure Data Lake Storage Gen2](data-lake-storage-known-issues.md)
- [Blob-opslag functies die beschikbaar zijn in Azure Data Lake Storage Gen2](data-lake-storage-supported-blob-storage-features.md)
- [Open-source platforms die ondersteuning bieden voor Azure Data Lake Storage Gen2](data-lake-storage-supported-open-source-platforms.md)
- [Toegang met meerdere protocollen in Azure Data Lake Storage](data-lake-storage-multi-protocol-access.md)