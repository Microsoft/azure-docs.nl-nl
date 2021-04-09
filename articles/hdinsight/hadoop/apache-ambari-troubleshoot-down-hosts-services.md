---
title: Apache Ambari-gebruikers interface toont hosts en services in azure HDInsight
description: Problemen met een Apache Ambari-UI oplossen wanneer de hosts en services in azure HDInsight worden weer gegeven
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/02/2019
ms.openlocfilehash: 0d3a393dc1cbd51b83edc0b6989bb5e7a5670a29
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98943266"
---
# <a name="scenario-apache-ambari-ui-shows-down-hosts-and-services-in-azure-hdinsight"></a>Scenario: Apache Ambari-gebruikers interface toont hosts en services in azure HDInsight

In dit artikel worden de stappen beschreven voor het oplossen van problemen en mogelijke oplossingen voor problemen bij het werken met Azure HDInsight-clusters.

## <a name="issue"></a>Probleem

Apache Ambari-gebruikers interface is toegankelijk, maar in de gebruikers interface worden bijna alle services weer gegeven, worden alle hosts met heartbeat verloren gegaan.

## <a name="cause"></a>Oorzaak

In de meeste gevallen is dit een probleem met Ambari-server die niet wordt uitgevoerd op de actieve hoofd knooppunt. Controleer welk hoofd knooppunt de actieve hoofd knooppunt is en zorg ervoor dat uw ambari-server op de juiste wordt uitgevoerd. Start ambari-server niet hand matig, laat de failover controller-service verantwoordelijk voor het starten van ambari-server op de juiste hoofd knooppunt. Start de actieve hoofd knooppunt opnieuw op om een failover af te dwingen.

Dit probleem kan ook worden veroorzaakt door netwerk problemen. Ga vanuit elk cluster knooppunt naar of u kunt pingen `headnodehost` . Er is een zeldzame situatie waarbij er geen verbinding kan worden gemaakt met een cluster knooppunt `headnodehost` :

```
$>telnet headnodehost 8440
... No route to host
```

## <a name="resolution"></a>Oplossing

Normaal gesp roken wordt de actieve hoofd knooppunt door het opnieuw opstarten van dit probleem verminderd. Neem contact op met het ondersteunings team van HDInsight

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]