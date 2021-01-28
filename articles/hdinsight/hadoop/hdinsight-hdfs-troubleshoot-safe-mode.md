---
title: Lokale HDFS vastgelopen in veilige modus in azure HDInsight-cluster
description: Problemen met lokale Apache HDFS in veilige modus in een Apache-cluster in azure HDInsight oplossen
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/14/2019
ms.openlocfilehash: d34bf8d82aee14f5ba835f68a061555d24ee2621
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98944438"
---
# <a name="scenario-local-hdfs-stuck-in-safe-mode-on-azure-hdinsight-cluster"></a>Scenario: lokale HDFS vastgelopen in veilige modus in azure HDInsight-cluster

In dit artikel worden de stappen beschreven voor het oplossen van problemen en mogelijke oplossingen voor problemen bij het werken met Azure HDInsight-clusters.

## <a name="issue"></a>Probleem

De lokale Apache Hadoop Distributed File System (HDFS) is vastgelopen in de veilige modus op het HDInsight-cluster. Er wordt een fout bericht van de volgende strekking weer gegeven:

```output
hdiuser@spark2:~$ hdfs dfs -D "fs.default.name=hdfs://mycluster/" -mkdir /temp
17/04/05 16:20:52 WARN retry.RetryInvocationHandler: Exception while invoking ClientNamenodeProtocolTranslatorPB.mkdirs over spark2.2oyzcdm4sfjuzjmj5dnmvscjpg.dx.internal.cloudapp.net/10.0.0.22:8020. Not retrying because try once and fail.
org.apache.hadoop.ipc.RemoteException(org.apache.hadoop.hdfs.server.namenode.SafeModeException): Cannot create directory /temp. Name node is in safe mode.
It was turned on manually. Use "hdfs dfsadmin -safemode leave" to turn safe mode off.
        at org.apache.hadoop.hdfs.server.namenode.FSNamesystem.checkNameNodeSafeMode(FSNamesystem.java:1359)
...
mkdir: Cannot create directory /temp. Name node is in safe mode.
```

## <a name="cause"></a>Oorzaak

Het HDInsight-cluster is omlaag geschaald naar enkele knoop punten hieronder of het aantal knoop punten bevindt zich dicht bij de HDFS-replicatie factor.

## <a name="resolution"></a>Oplossing

1. Rapporteert de status van HDFS op het HDInsight-cluster met de volgende opdracht:

    ```bash
    hdfs dfsadmin -D "fs.default.name=hdfs://mycluster/" -report
    ```

1. Controleer de integriteit van HDFS op het HDInsight-cluster met de volgende opdracht:

    ```bash
    hdiuser@spark2:~$ hdfs fsck -D "fs.default.name=hdfs://mycluster/" /
    ```

1. Als wordt vastgesteld dat er geen ontbrekend, beschadigd of onder gerepliceerde blokken of die blokken kunnen worden genegeerd, voert u de volgende opdracht uit om het naam-knoop punt uit de veilige modus te halen:

    ```bash
    hdfs dfsadmin -D "fs.default.name=hdfs://mycluster/" -safemode leave
    ```

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]