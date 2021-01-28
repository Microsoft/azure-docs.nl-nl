---
title: Apache Hive Zeppelin-interpreter fout-Azure HDInsight
description: De Apache Zeppelin Hive JDBC-interpreter verwijst naar de verkeerde URL in azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/30/2019
ms.openlocfilehash: 2d6e660459d9f350cc5c63fb8d312bcbcb300a64
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98930741"
---
# <a name="scenario-apache-hive-zeppelin-interpreter-gives-a-zookeeper-error-in-azure-hdinsight"></a>Scenario: Apache Hive Zeppelin-interpreter bevat een Zookeeper-fout in azure HDInsight

In dit artikel worden probleemoplossings stappen en mogelijke oplossingen voor problemen beschreven bij het gebruik van interactieve query onderdelen in azure HDInsight-clusters.

## <a name="issue"></a>Probleem

Op een Apache Hive LLAP-cluster geeft de Zeppelin-interpreter het volgende fout bericht weer wanneer wordt geprobeerd een query uit te voeren:

```
java.sql.SQLException: org.apache.hive.jdbc.ZooKeeperHiveClientException: Unable to read HiveServer2 configs from ZooKeeper
```

## <a name="cause"></a>Oorzaak

De Zeppelin Hive JDBC-interpreter verwijst naar de verkeerde URL.

## <a name="resolution"></a>Oplossing

1. Navigeer naar het samen vatting van Hive-onderdelen en kopieer de ' hive JDBC-URL ' naar het klem bord.

1. Ga naar `https://clustername.azurehdinsight.net/zeppelin/#/interpreter`

1. De JDBC-instellingen bewerken: werk de Hive. URL-waarde bij naar de component JDBC-URL die u in stap 1 hebt gekopieerd

1. Sla het bestand op en voer de query opnieuw uit

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]