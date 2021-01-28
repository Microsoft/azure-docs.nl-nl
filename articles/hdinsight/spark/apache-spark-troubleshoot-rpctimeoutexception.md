---
title: RpcTimeoutException voor Apache Spark Thrift-Azure HDInsight
description: Er worden 502-fouten weer gegeven bij het verwerken van grote gegevens sets met behulp van Apache Spark Thrift-server
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/29/2019
ms.openlocfilehash: c2975599272474fed9d61702fc1dbceb946c1190
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98932712"
---
# <a name="scenario-rpctimeoutexception-for-apache-spark-thrift-server-in-azure-hdinsight"></a>Scenario: RpcTimeoutException voor Apache Spark Thrift-server in azure HDInsight

In dit artikel worden probleemoplossings stappen en mogelijke oplossingen voor problemen beschreven bij het gebruik van Apache Spark-onderdelen in azure HDInsight-clusters.

## <a name="issue"></a>Probleem

Spark-toepassing mislukt met een `org.apache.spark.rpc.RpcTimeoutException` uitzonde ring en een bericht: `Futures timed out` , zoals in het volgende voor beeld:

```
org.apache.spark.rpc.RpcTimeoutException: Futures timed out after [120 seconds]. This timeout is controlled by spark.rpc.askTimeout
 at org.apache.spark.rpc.RpcTimeout.org$apache$spark$rpc$RpcTimeout$$createRpcTimeoutException(RpcTimeout.scala:48)
```

`OutOfMemoryError` en `overhead limit exceeded` fouten kunnen ook worden weer gegeven in de `sparkthriftdriver.log` as in het volgende voor beeld:

```
WARN  [rpc-server-3-4] server.TransportChannelHandler: Exception in connection from /10.0.0.17:53218
java.lang.OutOfMemoryError: GC overhead limit exceeded
```

## <a name="cause"></a>Oorzaak

Deze fouten worden veroorzaakt door een gebrek aan geheugen bronnen tijdens het verwerken van gegevens. Als het proces voor het uitvoeren van Java-garbagecollection wordt gestart, kan dit ertoe leiden dat de Spark-toepassing niet meer reageert. Query's gaan naar een time-out en stoppen met de verwerking. De `Futures timed out` fout wijst op een cluster onder ernstige stress.

## <a name="resolution"></a>Oplossing

Verg root de cluster grootte door meer werk knooppunten toe te voegen of de geheugen capaciteit van de bestaande cluster knooppunten te verhogen. U kunt ook de gegevens pijplijn aanpassen om de hoeveelheid gegevens die tegelijk worden verwerkt, te verminderen.

`spark.network.timeout`Hiermee bepaalt u de time-out voor alle netwerk verbindingen. Als u de time-out van het netwerk verhoogt, kan het langer duren voordat bepaalde kritieke bewerkingen zijn voltooid, maar wordt het probleem niet volledig opgelost.

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]