---
title: Time-out van Apache Hive weer geven vanuit query resultaat-Azure HDInsight
description: Er treedt een time-out op Apache Hive weer geven bij het ophalen van een query resultaat in azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/30/2019
ms.openlocfilehash: 474fa5e084acfa508a4391075b3c78d96b01aa46
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98930735"
---
# <a name="scenario-apache-hive-view-times-out-when-fetching-a-query-result-in-azure-hdinsight"></a>Scenario: er wordt een time-out opgeApache Hive van de weer gave bij het ophalen van een query resultaat in azure HDInsight

In dit artikel worden probleemoplossings stappen en mogelijke oplossingen voor problemen beschreven bij het gebruik van interactieve query onderdelen in azure HDInsight-clusters.

## <a name="issue"></a>Probleem

Bij het uitvoeren van bepaalde query's in de weer gave Apache Hive, kan de volgende fout optreden:

```
result fetch timed out
java.util.concurrent.TimeoutException: deadline passed
```

## <a name="cause"></a>Oorzaak

De standaard time-outwaarde van de Hive-weer gave is mogelijk niet geschikt voor de query die u uitvoert. De opgegeven tijds periode is te kort voor de Hive-weer gave om het query resultaat op te halen.

## <a name="resolution"></a>Oplossing

Verhoog de time-outs voor de Apache Ambari-Hive door de volgende eigenschappen in in te stellen `/etc/ambari-server/conf/ambari.properties` .

```
views.ambari.request.read.timeout.millis=300000
views.request.read.timeout.millis=300000
views.ambari.hive.<HIVE_VIEW_INSTANCE_NAME>.result.fetch.timeout=300000
```

De waarde van `HIVE_VIEW_INSTANCE_NAME` is beschikbaar aan het einde van de URL van de Hive-weer gave.

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]