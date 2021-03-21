---
title: Apache Hive verbindingen naar Apache Zookeeper-Azure HDInsight
description: Apache Hive weer gave is niet toegankelijk vanwege Apache Zookeeper-problemen in azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/30/2019
ms.openlocfilehash: 42fb9f5389cbc31e772dc9cf36b6a975c5e18d3c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98939297"
---
# <a name="scenario-apache-hive-fails-to-establish-a-connection-to-apache-zookeeper-in-azure-hdinsight"></a>Scenario: Apache Hive kan geen verbinding maken met Apache Zookeeper in azure HDInsight

In dit artikel worden probleemoplossings stappen en mogelijke oplossingen voor problemen beschreven bij het gebruik van interactieve query onderdelen in azure HDInsight-clusters.

## <a name="issue"></a>Probleem

De Hive-weer gave is niet toegankelijk en in de logboeken wordt `/var/log/hive` een fout weer gegeven die er ongeveer als volgt uitziet:

```
ERROR [Curator-Framework-0]: curator.ConnectionState (ConnectionState.java:checkTimeouts(200)) - Connection timed out for connection string (zk0-cluster.cloud.wbmi.com:2181,zk1-cluster.cloud.wbmi.com:2181,zk2-cluster.cloud.wbmi.com:2181) and timeout (15000) / elapsed (21852)
```

## <a name="cause"></a>Oorzaak

Het is mogelijk dat de Hive geen verbinding kan maken met Zookeeper, waardoor de Hive-weer gave niet kan worden gestart.

## <a name="resolution"></a>Oplossing

1. Controleer of de Zookeeper-service in orde is.

1. Controleer of de Zookeeper-service een ZNode-vermelding heeft voor Hive server2. De waarde ontbreekt of is onjuist.

    ```
    /usr/hdp/2.6.2.25-1/zookeeper/bin/zkCli.sh -server zk1-wbwdhs
    [zk: zk0-cluster(CONNECTED) 0] ls /hiveserver2-hive2
    ```

1. Als u opnieuw verbinding wilt maken, start u de Zookeeper-knoop punten opnieuw op en start u HiveServer2 opnieuw op.

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]