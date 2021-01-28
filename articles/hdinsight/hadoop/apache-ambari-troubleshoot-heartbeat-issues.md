---
title: Problemen met de Apache Ambari-heartbeat in Azure HDInsight
description: Bekijk de verschillende redenen voor Apache Ambari heartbeat-problemen in azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/06/2020
ms.openlocfilehash: 5eebde42098d74f533565d274b693c4a06f2f60d
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98946738"
---
# <a name="apache-ambari-heartbeat-issues-in-azure-hdinsight"></a>Problemen met de Apache Ambari-heartbeat in Azure HDInsight

In dit artikel worden de stappen beschreven voor het oplossen van problemen en mogelijke oplossingen voor problemen bij het werken met Azure HDInsight-clusters.

## <a name="scenario-high-cpu-utilization"></a>Scenario: hoog CPU-gebruik

### <a name="issue"></a>Probleem

De Ambari-agent heeft een hoog CPU-gebruik, wat leidt tot waarschuwingen van de Ambari-gebruikers interface die voor bepaalde knoop punten de Ambari-agent heartbeat verloren zijn gegaan. De waarschuwing heartbeat verloren is doorgaans tijdelijk.

### <a name="cause"></a>Oorzaak

Vanwege verschillende ambari-agent fouten kan uw ambari-agent in zeldzame gevallen een hoog CPU-gebruik hebben (dicht tot 100).

### <a name="resolution"></a>Oplossing

1. Proces-ID (PID) van ambari-agent identificeren:

    ```bash
    ps -ef | grep ambari_agent
    ```

1. Voer vervolgens de volgende opdrachten uit om het CPU-gebruik weer te geven:

    ```bash
    top -p <ambari-agent-pid>
    ```

1. Ambari-agent opnieuw starten om het probleem te verhelpen:

    ```bash
    service ambari-agent restart
    ```

1. Als opnieuw opstarten niet werkt, beëindigt u het proces ambari agent en start u het op:

    ```bash
    kill -9 <ambari-agent-pid>
    service ambari-agent start
    ```

---

## <a name="scenario-ambari-agent-not-started"></a>Scenario: Ambari-agent is niet gestart

### <a name="issue"></a>Probleem

De Ambari-agent is nog niet gestart, waardoor er waarschuwingen zijn van de Ambari-gebruikers interface die voor bepaalde knoop punten de heartbeat van de Ambari-agent is verbroken.

### <a name="cause"></a>Oorzaak

De waarschuwingen worden veroorzaakt door de Ambari-agent die niet wordt uitgevoerd.

### <a name="resolution"></a>Oplossing

1. Status van ambari-agent bevestigen:

    ```bash
    service ambari-agent status
    ```

1. Controleren of de failover-controller services worden uitgevoerd:

    ```bash
    ps -ef | grep failover
    ```

    Als failover controller-Services niet worden uitgevoerd, is er waarschijnlijk een probleem met het niet toestaan van hdinsight-agent om failover controller te starten. Controleer het logboek van hdinsight-agent van het `/var/log/hdinsight-agent/hdinsight-agent.out` bestand.

## <a name="scenario-heartbeat-lost-for-ambari"></a>Scenario: heartbeat verloren voor Ambari

### <a name="issue"></a>Probleem

De Ambari heartbeat-agent is verloren gegaan.

### <a name="cause"></a>Oorzaak

OMS-logboeken veroorzaken een hoog CPU-gebruik.

### <a name="resolution"></a>Oplossing

* Schakel Azure Monitor logboek registratie uit met de Power shell [-cmdlet Disable-AzHDInsightMonitoring](/powershell/module/az.hdinsight/disable-azhdinsightmonitoring) .
* Het `mdsd.warn` logboek bestand verwijderen

---

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]