---
title: IllegalArgumentException-fout voor Apache Spark-Azure HDInsight
description: IllegalArgumentException voor Apache Spark-activiteit in azure HDInsight voor Azure Data Factory
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/29/2019
ms.openlocfilehash: 429659d605cdaf8aad978841e486a17da321cce4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98929392"
---
# <a name="scenario-illegalargumentexception-for-apache-spark-activity-in-azure-hdinsight"></a>Scenario: IllegalArgumentException for Apache Spark-activiteit in azure HDInsight

In dit artikel worden probleemoplossings stappen en mogelijke oplossingen voor problemen beschreven bij het gebruik van Apache Spark-onderdelen in azure HDInsight-clusters.

## <a name="issue"></a>Probleem

U ontvangt de volgende uitzonde ring bij het uitvoeren van een Spark-activiteit in een Azure Data Factory-pijp lijn:

```error
Exception in thread "main" java.lang.IllegalArgumentException:
Wrong FS: wasbs://additional@xxx.blob.core.windows.net/spark-examples_2.11-2.1.0.jar, expected: wasbs://wasbsrepro-2017-11-07t00-59-42-722z@xxx.blob.core.windows.net
```

## <a name="cause"></a>Oorzaak

Een Spark-taak mislukt als het jar-bestand zich niet in de standaard-of primaire opslag ruimte van het Spark-cluster bevindt.

Dit is een bekend probleem met het open-source-framework dat wordt bijgehouden in deze bug: [Spark-taak mislukt als FS. defaultFS en Application jar een andere URL zijn](https://issues.apache.org/jira/browse/SPARK-22587).

Dit probleem is opgelost in Spark 2.3.0.

## <a name="resolution"></a>Oplossing

Zorg ervoor dat de toepassing jar is opgeslagen op de standaard/primaire opslag voor het HDInsight-cluster. In het geval van Azure Data Factory moet u ervoor zorgen dat de gekoppelde ADF-service wordt verwezen naar de standaard-HDInsight-container in plaats van een secundaire container.

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]