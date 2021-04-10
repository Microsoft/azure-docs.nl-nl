---
title: NoClassDefFoundError-Apache Spark met Apache Kafka gegevens in azure HDInsight
description: Apache Spark streaming-taak waarmee gegevens van een Apache Kafka cluster worden gelezen, mislukt met een NoClassDefFoundError in azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/29/2019
ms.openlocfilehash: 4d00cbcb0151da39feb0cb015660291af544d7f4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98946385"
---
# <a name="apache-spark-streaming-job-that-reads-apache-kafka-data-fails-with-noclassdeffounderror-in-hdinsight"></a>Apache Spark streaming-taak waarmee Apache Kafka gegevens worden gelezen, mislukt met NoClassDefFoundError in HDInsight

In dit artikel worden probleemoplossings stappen en mogelijke oplossingen voor problemen beschreven bij het gebruik van Apache Spark-onderdelen in azure HDInsight-clusters.

## <a name="issue"></a>Probleem

Het Apache Spark cluster voert een Spark-streaming-taak uit waarmee gegevens uit een Apache Kafka cluster worden gelezen. De Spark streaming-taak mislukt als de Kafka-stroom compressie is ingeschakeld. In dit geval is de application_1525986016285_0193 van de Spark streaming-app mislukt vanwege een fout:

```
18/05/17 20:01:33 WARN YarnAllocator: Container marked as failed: container_e25_1525986016285_0193_01_000032 on host: wn87-Scaled.2ajnsmlgqdsutaqydyzfzii3le.cx.internal.cloudapp.net. Exit status: 50. Diagnostics: Exception from container-launch.
Container id: container_e25_1525986016285_0193_01_000032
Exit code: 50
Stack trace: ExitCodeException exitCode=50: 
 at org.apache.hadoop.util.Shell.runCommand(Shell.java:944)
```

## <a name="cause"></a>Oorzaak

Deze fout kan worden veroorzaakt door een versie van het jar-bestand op te geven `spark-streaming-kafka` die afwijkt van de versie van het Kafka-cluster dat u uitvoert.

Als u bijvoorbeeld een Kafka-cluster versie 0.10.1 uitvoert, resulteert de volgende opdracht in een fout:

```
spark-submit \
--packages org.apache.spark:spark-streaming-kafka-0-8_2.11:2.2.0
--conf spark.executor.instances=16 \
...
~/Kafka_Spark_SQL.py <bootstrap server details>
```

## <a name="resolution"></a>Oplossing

Gebruik de opdracht Spark-Submit met de `–packages` optie en zorg ervoor dat de versie van het bestand Spark-streaming-Kafka jar hetzelfde is als de versie van het Kafka-cluster dat u uitvoert.

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]