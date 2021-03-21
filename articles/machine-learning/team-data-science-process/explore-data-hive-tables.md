---
title: Gegevens in Hive-tabellen verkennen met hive-query's-team data Science process
description: Gebruik voorbeeld scripts voor componenten die worden gebruikt voor het verkennen van gegevens in Hive-tabellen in een HDInsight Hadoop-cluster.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: a7234a8c45c20c64dddb43a52a099aa92f2d297d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93321117"
---
# <a name="explore-data-in-hive-tables-with-hive-queries"></a>Gegevens in Hive-tabellen verkennen met Hive-query's

In dit artikel vindt u voor beelden van Hive-scripts die worden gebruikt voor het verkennen van gegevens in Hive-tabellen in een HDInsight Hadoop-cluster.

Deze taak is een stap in het [team data Science process](overview.md).

## <a name="prerequisites"></a>Vereisten
In dit artikel wordt ervan uitgegaan dat u het volgende hebt:

* Een Azure-opslag account gemaakt. Als u instructies nodig hebt, raadpleegt u [een Azure Storage account maken](../../storage/common/storage-account-create.md)
* Er is een aangepast Hadoop-cluster ingericht met de HDInsight-service. Zie [Azure HDInsight Hadoop clusters aanpassen voor geavanceerde analyse](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md)als u instructies nodig hebt.
* De gegevens zijn geüpload naar Hive-tabellen in Azure HDInsight Hadoop clusters. Als dat niet het geval is, volgt u de instructies in [maken en gegevens laden in Hive-tabellen](move-hive-tables.md) om eerst gegevens te uploaden naar Hive-tabellen.
* Externe toegang tot het cluster is ingeschakeld. Zie [toegang tot het hoofd knooppunt van het Hadoop-cluster](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md)als u instructies nodig hebt.
* Zie [Hive-Query's verzenden](move-hive-tables.md#submit) als u instructies nodig hebt voor het verzenden van Hive-query's.

## <a name="example-hive-query-scripts-for-data-exploration"></a>Voor beeld van Hive-query scripts voor het verkennen van gegevens
1. Het aantal observaties per partitie ophalen  `SELECT <partitionfieldname>, count(*) from <databasename>.<tablename> group by <partitionfieldname>;`
2. Het aantal observaties per dag ophalen  `SELECT to_date(<date_columnname>), count(*) from <databasename>.<tablename> group by to_date(<date_columnname>);`
3. De niveaus in een kolom categorische ophalen  
    `SELECT  distinct <column_name> from <databasename>.<tablename>`
4. Het aantal niveaus in combi natie van twee categorische kolommen ophalen  `SELECT <column_a>, <column_b>, count(*) from <databasename>.<tablename> group by <column_a>, <column_b>`
5. De distributie voor numerieke kolommen ophalen  
    `SELECT <column_name>, count(*) from <databasename>.<tablename> group by <column_name>`
6. Records ophalen uit twee tabellen samen voegen
   
    ```hiveql
    SELECT
        a.<common_columnname1> as <new_name1>,
        a.<common_columnname2> as <new_name2>,
        a.<a_column_name1> as <new_name3>,
        a.<a_column_name2> as <new_name4>,
        b.<b_column_name1> as <new_name5>,
        b.<b_column_name2> as <new_name6>
    FROM
        (
        SELECT <common_columnname1>,
            <common_columnname2>,
            <a_column_name1>,
            <a_column_name2>,
        FROM <databasename>.<tablename1>
        ) a
        join
        (
        SELECT <common_columnname1>,
            <common_columnname2>,
            <b_column_name1>,
            <b_column_name2>,
        FROM <databasename>.<tablename2>
        ) b
        ON a.<common_columnname1>=b.<common_columnname1> and a.<common_columnname2>=b.<common_columnname2>
    ```

## <a name="additional-query-scripts-for-taxi-trip-data-scenarios"></a>Aanvullende query scripts voor de gegevens scenario's voor taxi's-reis
Er worden ook voor beelden gegeven van query's die specifiek zijn voor [NYC-gegevens](https://chriswhong.com/open-data/foil_nyc_taxi/) Scenario's voor taxi's in de [github-opslag plaats](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts). Deze query's hebben al een gegevens schema opgegeven en zijn gereed om te worden verzonden om te worden uitgevoerd.