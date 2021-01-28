---
title: Waarschuwingen Apache Ambari Directory in azure HDInsight
description: Discussie en analyse van mogelijke oorzaken en oplossingen voor Apache Ambari Directory-waarschuwingen in HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/22/2020
ms.openlocfilehash: 47d1f9797a44d7dc918677c21ffc7a124808525d
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98943289"
---
# <a name="scenario-apache-ambari-directory-alerts-in-azure-hdinsight"></a>Scenario: Apache Ambari Directory-waarschuwingen in azure HDInsight

In dit artikel worden de stappen beschreven voor het oplossen van problemen en mogelijke oplossingen voor problemen bij het werken met Azure HDInsight-clusters.

## <a name="issue"></a>Probleem

U ontvangt fouten van Apache Ambari die vergelijkbaar zijn met:

```
1/1 local-dirs have errors: [ /mnt/resource/hadoop/yarn/local : Cannot create directory: /mnt/resource/hadoop/yarn/local ]
1/1 log-dirs have errors: [ /mnt/resource/hadoop/yarn/log : Cannot create directory: /mnt/resource/hadoop/yarn/log ]
```

## <a name="cause"></a>Oorzaak

De vermelde mappen van Ambari-waarschuwing ontbreken op betrokken worker-knoop punten.

## <a name="resolution"></a>Oplossing

Hand matig ontbrekende directory's maken op de betrokken worker-knoop punten.

1. SSH naar het relevante worker-knoop punt.

1. Hoofd gebruiker ophalen: `sudo su` .

1. Recursief benodigde mappen maken.

1. Wijzig de eigenaar en groep voor deze directory's.

    ```bash
    chown -R yarn /mnt/resource/hadoop/yarn/local
    chgrp -R hadoop /mnt/resource/hadoop/yarn/local
    chown -R yarn /mnt/resource/hadoop/yarn/log
    chgrp -R hadoop /mnt/resource/hadoop/yarn/log
    ```

1. Schakel in Apache Ambari UI de gebruikers interface uit en schakel vervolgens waarschuwing in.

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]