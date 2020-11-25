---
title: Getraceerde CPU in Apache HBase-cluster-Azure HDInsight
description: Problemen met vastgelegde CPU op regio server in Apache HBase-cluster in azure HDInsight oplossen
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/01/2019
ms.openlocfilehash: bed73c3ccc7f514ffc9ff8f97534ae4b249834ce
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96017012"
---
# <a name="scenario-pegged-cpu-on-region-server-in-apache-hbase-cluster-in-azure-hdinsight"></a>Scenario: CPU op regio server in Apache HBase-cluster in azure HDInsight getraceerd

In dit artikel worden de stappen beschreven voor het oplossen van problemen en mogelijke oplossingen voor problemen bij het werken met Azure HDInsight-clusters.

## <a name="issue"></a>Probleem

Het Server proces Apache HBase Region begint met het behalen van een CPU van 200%, waardoor waarschuwingen worden gestart op HBase Master proces en het cluster niet werken met volledige capaciteit.

## <a name="cause"></a>Oorzaak

Als u HBase cluster v 3.4 uitvoert, hebt u mogelijk een mogelijke fout veroorzaakt door een upgrade van JDK naar versie 1.7.0 _151. Het symptoom dat wordt weer gegeven, is een regio Server proces dat begint met een CPU van 200% (om de opdracht uit te voeren `top` . als er een proces is dat dicht bij 200% CPU bevindt, wordt de PID bereikt en wordt gecontroleerd of het Server proces regio wordt uitgevoerd `ps -aux | grep` ).

## <a name="resolution"></a>Oplossing

1. Installeer JDK 1,8 op alle knoop punten van het cluster, zoals hieronder wordt beschreven:

    * Voer de script actie uit `https://raw.githubusercontent.com/Azure/hbase-utils/master/scripts/upgradetojdk18allnodes.sh` . Zorg ervoor dat u de optie selecteert die op alle knoop punten moet worden uitgevoerd.

    * U kunt zich ook aanmelden bij elk afzonderlijk knoop punt en de opdracht uitvoeren `sudo add-apt-repository ppa:openjdk-r/ppa -y && sudo apt-get -y update && sudo apt-get install -y openjdk-8-jdk` .

1. Ga naar Ambari-gebruikers interface- `https://<clusterdnsname>.azurehdinsight.net` .

1. Navigeer naar **HBase->-configuraties->Geavanceerd->Geavanceerd** `hbase-env configs` en wijzig de variabele `JAVA_HOME` in `export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64` . Sla de configuratie wijziging op.

1. [Optioneel, maar aanbevolen] [Alle tabellen op het cluster leegmaken](/archive/blogs/azuredatalake/hdinsight-hbase-how-to-improve-hbase-cluster-restart-time-by-flushing-tables).

1. Start alle HBase-services die opnieuw moeten worden opgestart opnieuw vanuit de gebruikers interface van Ambari.

1. Afhankelijk van de gegevens op het cluster kan het enkele minuten duren voordat het cluster een stabiele status heeft bereikt. De manier waarop u bevestigt dat het cluster stabiel is, is door een HMaster-gebruikers interface te controleren (alle regio servers moeten actief zijn) van Ambari (vernieuwen) of van hoofd knooppunt HBase shell uit te voeren en vervolgens de status opdracht uit te voeren.

Als u wilt controleren of de upgrade is voltooid, controleert u of de relevante HBase-processen zijn gestart met behulp van de juiste Java-versie, bijvoorbeeld voor de regio server-controle als:

```
ps -aux | grep regionserver, and verify the version like '''/usr/lib/jvm/java-8-openjdk-amd64/bin/java
```

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]