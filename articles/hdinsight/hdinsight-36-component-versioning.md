---
title: Apache Hadoop onderdelen en versies-Azure HDInsight 3,6
description: Meer informatie over de Apache Hadoop onderdelen en versies in azure HDInsight 3,6.
ms.service: hdinsight
ms.topic: conceptual
author: deshriva
ms.author: deshriva
ms.date: 02/08/2021
ms.openlocfilehash: 32b287c3d7b1974db5a079d1ee84aaafad3faed7
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105727685"
---
# <a name="hdinsight-36-component-versions"></a>Versies van HDInsight 3,6-onderdelen

In dit artikel vindt u informatie over de onderdelen en versies van de Apache Hadoop-omgeving in azure HDInsight 3,6.

## <a name="support-for-hdinsight-36"></a>Ondersteuning voor HDInsight 3,6

Vanaf 1 juli 2021 micro soft biedt Basic Support voor bepaalde cluster typen van HDI 3,6.
De volgende tabel bevat de ondersteunings periode voor HDInsight 3,6-cluster typen.

| Clustertype                    | Framework-versie | Verval datum standaard ondersteuning       | Verval datum van Basic Support | Buitengebruikstellings datum |
|---------------------------------|-------------------|-----------------------------------|------------------------------|-----------------|
| HDInsight 3,6 Hadoop            | 2.7.3             | 30 juni 2021                     | 3 april 2022                | 4 april 2022 |
| HDInsight 3,6 Spark             | 2.3               | 30 juni 2021                     | 3 april 2022                | 4 april 2022 |
| HDInsight 3,6 Kafka             | 1.1               | 30 juni 2021                     | 3 april 2022                | 4 april 2022 |
| HDInsight 3,6 HBase             | 1.1               | 30 juni 2021                     | 3 april 2022                | 4 april 2022 |
| HDInsight 3,6 Interactive-query | 2.1               | 30 juni 2021                     | 3 april 2022                | 4 april 2022 |
| HDInsight 3,6 Storm             | 1.1               | 30 juni 2021                     | 3 april 2022                | 4 april 2022 |
| HDInsight 3,6 ML-Services      | 9.3               | -                                 | -                            | 31 december 2020 |
| HDInsight 3,6 Spark             | 2.2               | -                                 | -                            | 30 juni 2020 |
| HDInsight 3,6 Spark             | 2.1               | -                                 | -                            | 30 juni 2020 |
| HDInsight 3,6 Kafka             | 1.0               | -                                 | -                            | 30 juni 2020 |

## <a name="apache-components-available-with-hdinsight-version-36"></a>Apache-onderdelen die beschikbaar zijn met HDInsight-versie 3,6

De versies van de OSS-onderdelen die zijn gekoppeld aan HDInsight 3,6, worden weer gegeven in de volgende tabel.

| Onderdeel              | HDInsight 3,6 (standaard)     |
|------------------------|-----------------------------|
| Apache Hadoop en garen | 2.7.3                       |
| Apache TEZ             | 0.7.0                       |
| Apache-Pig             | 0.16.0                      |
| Apache Hive            | (2.1.0 op ESP-interactieve query) |
| Apache TEZ Hive2       | 0.8.4                       |
| Apache zwerver          | 0.7.0                       |
| Apache HBase           | 1.1.2                       |
| Apache Sqoop           | 1.4.6                       |
| Apache Oozie           | 4.2.0                       |
| Apache Zookeeper       | 3.4.6                       |
| Apache Storm           | 1.1.0                       |
| Apache mahout          | 0.9.0 +                      |
| Apache Phoenix         | 4.7.0                       |
| Apache Spark           | verschijnsel.                      |
| Apache Livy            | 0,4.                        |
| Apache Kafka           | 1.1                         |
| Apache Ambari          | 2.6.0                       |
| Apache Zeppelin        | 0.7.3                       |
| Mono                   | 4.2.1                       |

## <a name="next-steps"></a>Volgende stappen

- [Setup van het cluster voor Apache Hadoop, Spark en meer op HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
- [Enterprise Security Package](./enterprise-security-package.md)
- [Werken in Apache Hadoop op HDInsight vanaf een Windows-computer](hdinsight-hadoop-windows-tools.md)
