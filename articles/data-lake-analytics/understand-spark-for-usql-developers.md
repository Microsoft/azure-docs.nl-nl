---
title: Meer informatie over Apache Spark voor Azure Data Lake Analytics U-SQL-ontwikkel aars.
description: In dit artikel worden Apache Spark concepten beschreven die u helpen bij het oplossen van verschillen tussen U-SQL-ontwikkel aars.
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: how-to
ms.custom: understand-apache-spark-for-usql-developers
ms.date: 10/15/2019
ms.openlocfilehash: a66a82a6d2a5bb1f534ed211091793b2ab4d2a28
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92221091"
---
# <a name="understand-apache-spark-for-u-sql-developers"></a>Inzicht in Apache Spark voor U-SQL-ontwikkelaars

Micro soft ondersteunt diverse analyse Services, zoals [Azure Databricks](/azure/databricks/scenarios/what-is-azure-databricks) en [Azure HDInsight](../hdinsight/hdinsight-overview.md) , evenals Azure data Lake Analytics. We horen van ontwikkel aars dat ze een duidelijke voor keur hebben voor open-source-oplossingen als ze analytische pijp lijnen bouwen. Om U-SQL-ontwikkel aars te helpen begrijpen Apache Spark en hoe u uw U-SQL-scripts kunt transformeren naar Apache Spark, hebben we deze richt lijnen gemaakt.

Het bevat een aantal stappen die u kunt uitvoeren en verschillende alternatieven.

## <a name="steps-to-transform-u-sql-to-apache-spark"></a>Stappen om U-SQL te transformeren naar Apache Spark

1. Transformeer pijp lijnen van uw taak.

   Als u [Azure Data Factory](../data-factory/introduction.md) gebruikt om uw Azure data Lake Analytics-scripts te organiseren, moet u deze aanpassen om de nieuwe Spark-Program ma's te organiseren.
2. Meer informatie over de verschillen tussen hoe U-SQL en Spark gegevens beheert

   Als u uw gegevens van [Azure data Lake Storage gen1](../data-lake-store/data-lake-store-overview.md) naar [Azure data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)wilt verplaatsen, moet u zowel de bestands gegevens als de gegevens van de catalogus die worden bijgehouden kopiëren. Houd er rekening mee dat Azure Data Lake Analytics alleen Azure Data Lake Storage Gen1 ondersteunt. Zie [informatie over Spark-gegevens indelingen](understand-spark-data-formats.md)
3. Uw U-SQL-scripts transformeren naar Spark

   Voordat u uw U-SQL-scripts transformeert, moet u een analyse service kiezen. Enkele van de beschik bare berekenings Services zijn:
      - [Azure Data Factory gegevensstroom](../data-factory/concepts-data-flow-overview.md) Het toewijzen van gegevens stromen zijn visueel ontworpen gegevens transformaties waarmee data Engineers een grafische logica voor gegevens transformatie kunnen ontwikkelen zonder code te hoeven schrijven. Hoewel ze niet geschikt zijn voor het uitvoeren van complexe gebruikers code, kunnen ze eenvoudig traditionele trans formaties van een SQL-achtige gegevensstroom weer geven
      - [Azure HDInsight-Hive](../hdinsight/hadoop/apache-hadoop-using-apache-hive-as-an-etl-tool.md) Apache Hive op HDInsight is geschikt voor het extra heren, transformeren en laden (ETL)-bewerkingen. Dit betekent dat u uw U-SQL-scripts gaat vertalen naar Apache Hive.
      - Apache Spark engines als [Azure HDInsight Spark](../hdinsight/spark/apache-spark-overview.md) of [Azure Databricks](/azure/databricks/scenarios/what-is-azure-databricks) dit betekent dat u uw U-SQL-scripts gaat vertalen naar Spark. Zie informatie over Spark- [gegevens indelingen begrijpen](understand-spark-data-formats.md) voor meer informatie.

> [!CAUTION]
> Zowel [Azure Databricks](/azure/databricks/scenarios/what-is-azure-databricks) als [Azure HDInsight Spark](../hdinsight/spark/apache-spark-overview.md) zijn Cluster Services en geen serverloze taken zoals Azure data Lake Analytics. U moet rekening houden met het inrichten van de clusters om de juiste kosten/prestatie verhouding te verkrijgen en hun levens duur te beheren om uw kosten te beperken. Deze services hebben verschillende prestatie kenmerken met gebruikers code die is geschreven in .NET. Daarom moet u wrappers schrijven of uw code herschrijven in een ondersteunde taal. Zie informatie over Spark- [gegevens indelingen](understand-spark-data-formats.md)begrijpen [Apache Spark code concepten voor U-SQL-ontwikkel aars](understand-spark-code-concepts.md), [.net voor Apache Spark](https://dotnet.microsoft.com/apps/data/spark)

## <a name="next-steps"></a>Volgende stappen

- [Informatie over Spark-gegevens indelingen voor U-SQL-ontwikkel aars](understand-spark-data-formats.md)
- [Informatie over Spark-code concepten voor U-SQL-ontwikkel aars](understand-spark-code-concepts.md)
- [Upgrade uw big data Analytics-oplossingen van Azure Data Lake Storage Gen1 naar Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-migrate-gen1-to-gen2.md)
- [.NET voor Apache Spark](/dotnet/spark/what-is-apache-spark-dotnet)
- [Gegevens transformeren met behulp van Hadoop Hive-activiteit in Azure Data Factory](../data-factory/transform-data-using-hadoop-hive.md)
- [Gegevens transformeren met behulp van Spark-activiteit in Azure Data Factory](../data-factory/transform-data-using-spark.md)
- [Wat is Apache Spark in Azure HDInsight?](../hdinsight/spark/apache-spark-overview.md)