---
title: Apache ZooKeeper server kan geen quorum vormen in azure HDInsight
description: Apache ZooKeeper server kan geen quorum vormen in azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 05/20/2020
ms.openlocfilehash: 3301d00dce6feb00edcb70ba9edfedcce2e31ec9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98929216"
---
# <a name="apache-zookeeper-server-fails-to-form-a-quorum-in-azure-hdinsight"></a>Apache ZooKeeper server kan geen quorum vormen in azure HDInsight

In dit artikel worden probleemoplossings stappen en mogelijke oplossingen beschreven voor problemen met betrekking tot Zookeepers in azure HDInsight-clusters.

## <a name="symptoms"></a>Symptomen

* Beide resource managers gaan naar stand-by-modus
* Namenodes zijn beide in de modus stand-by
* Spark-, Hive-en garen taken of Hive-query's mislukken vanwege Zookeeper-verbindings fouten
* LLAP-daemons worden niet gestart op beveiligde Spark of veilige, interactieve Hive-clusters

## <a name="sample-log"></a>Voorbeeld logboek

Er wordt mogelijk een fout bericht weer gegeven dat vergelijkbaar is met het volgende in de garens-Logboeken (/var/log/Hadoop-Yarn/yarn/yarn-yarn *. log op de hoofd knooppunten):

```output
2020-05-05 03:17:18.3916720|Lost contact with Zookeeper. Transitioning to standby in 10000 ms if connection is not reestablished.
Message
2020-05-05 03:17:07.7924490|Received RMFatalEvent of type STATE_STORE_FENCED, caused by org.apache.zookeeper.KeeperException$NoAuthException: KeeperErrorCode = NoAuth
...
2020-05-05 03:17:08.3890350|State store operation failed 
2020-05-05 03:17:08.3890350|Transitioning to standby state
```

## <a name="related-issues"></a>Gerelateerde problemen

* Services met hoge Beschik baarheid zoals garens, NameNode en livy kunnen om verschillende redenen dalen.
* Controleer de logboeken die zijn gerelateerd aan Zookeeper-verbindingen
* Zorg ervoor dat het probleem herhaaldelijk optreedt (gebruik deze oplossingen niet voor eenmalige cases)
* Taken kunnen tijdelijk mislukken vanwege Zookeeper-verbindings problemen

## <a name="common-causes-for-zookeeper-failure"></a>Veelvoorkomende oorzaken voor Zookeeper-fout

* Hoog CPU-gebruik op de Zookeeper-servers
  * Als u in de Ambari-gebruikers interface bijna 100% heeft gestaand CPU-gebruik op de Zookeeper-servers ziet, kunnen de Zookeeper-sessies gedurende die tijd verlopen en time-out hebben
* Zookeeper-clients rapporteren veelvuldige time-outs
  * In de logboeken voor Resource Manager, Namenode en anderen ziet u veelvuldige time-outs voor client verbindingen
  * Dit kan leiden tot quorum verlies, veelvuldige failovers en andere problemen

## <a name="check-for-zookeeper-status"></a>Controleren op Zookeeper-status

* De Zookeeper-servers vinden in het bestand/etc/hosts-bestand of in de Ambari-gebruikers interface
* Voer de volgende opdracht uit
  * `echo stat | nc <ZOOKEEPER_HOST_IP> 2181` (of 2182)  
  * Poort 2181 is het Apache Zookeeper-exemplaar
  * Poort 2182 wordt gebruikt door de HDInsight-Zookeeper (om HA te bieden voor services die geen systeem eigen HA zijn)
  * Als de opdracht geen uitvoer bevat, betekent dit dat de Zookeeper-servers niet worden uitgevoerd
  * Als de servers worden uitgevoerd, omvat het resultaat ook statische verbindingen van client verbinding en andere statistieken

```output
Zookeeper version: 3.4.6-8--1, built on 12/05/2019 12:55 GMT
Clients:
 /10.2.0.57:50988[1](queued=0,recved=715,sent=715)
 /10.2.0.57:46632[1](queued=0,recved=138340,sent=138347)
 /10.2.0.34:14688[1](queued=0,recved=264653,sent=353420)
 /10.2.0.52:49680[1](queued=0,recved=134812,sent=134814)
 /10.2.0.57:50614[1](queued=0,recved=19812,sent=19812)
 /10.2.0.56:35034[1](queued=0,recved=2586,sent=2586)
 /10.2.0.52:63982[1](queued=0,recved=72215,sent=72217)
 /10.2.0.57:53024[1](queued=0,recved=19805,sent=19805)
 /10.2.0.57:45126[1](queued=0,recved=19621,sent=19621)
 /10.2.0.56:41270[1](queued=0,recved=1348743,sent=1348788)
 /10.2.0.53:59097[1](queued=0,recved=72215,sent=72217)
 /10.2.0.56:41088[1](queued=0,recved=788,sent=802)
 /10.2.0.34:10246[1](queued=0,recved=19575,sent=19575)
 /10.2.0.56:40944[1](queued=0,recved=717,sent=717)
 /10.2.0.57:45466[1](queued=0,recved=19861,sent=19861)
 /10.2.0.57:59634[0](queued=0,recved=1,sent=0)
 /10.2.0.34:14704[1](queued=0,recved=264622,sent=353355)
 /10.2.0.57:42244[1](queued=0,recved=49245,sent=49248)

Latency min/avg/max: 0/3/14865
Received: 238606078
Sent: 239139381
Connections: 18
Outstanding: 0
Zxid: 0x1004f99be
Mode: follower
Node count: 133212
```

## <a name="cpu-load-peaks-up-every-hour"></a>CPU-belasting pieken elk uur omhoog

* Meld u aan bij de Zookeeper-server en controleer de/etc/crontab
* Als er op dit moment elk uur taken worden uitgevoerd, kunt u de begin tijd op verschillende Zookeeper-servers wille keurig maken.
  
## <a name="purging-old-snapshots"></a>Oude moment opnamen verwijderen

* Zookeepers zijn geconfigureerd voor het automatisch verwijderen van oude moment opnamen
* De laatste 30 moment opnamen blijven standaard behouden
* Het aantal moment opnamen dat wordt bewaard, wordt bepaald door de configuratie sleutel `autopurge.snapRetainCount` . Deze eigenschap kan worden gevonden in de volgende bestanden:
  * `/etc/zookeeper/conf/zoo.cfg` voor Hadoop-Zookeeper
  * `/etc/hdinsight-zookeeper/conf/zoo.cfg` voor HDInsight Zookeeper
* Stel `autopurge.snapRetainCount` in op een waarde van 3 en start de Zookeeper-servers opnieuw op
  * Hadoop Zookeeper config kan worden bijgewerkt en de service kan opnieuw worden gestart via Ambari
  * HDInsight-Zookeeper hand matig stoppen en opnieuw starten
    * `sudo lsof -i :2182` geeft u de proces-ID die u wilt afsluiten
    * `sudo python /opt/startup_scripts/startup_hdinsight_zookeeper.py`
* Moment opnamen niet hand matig verwijderen: het hand matig verwijderen van moment opnamen kan leiden tot verlies van gegevens

## <a name="cancelledkeyexception-in-the-zookeeper-server-log-doesnt-require-snapshot-cleanup"></a>CancelledKeyException in het Zookeeper-server logboek vereist geen opschoning van de moment opname

* Deze uitzonde ring wordt weer gegeven op de Zookeeper-servers (/var/log/Zookeeper/Zookeeper-Zookeeper-* of/var/log/hdinsight-Zookeeper/Zookeeper *-bestanden)
* Deze uitzonde ring betekent meestal dat de client niet langer actief is en dat de server geen bericht kan verzenden
* Met deze uitzonde ring wordt ook aangegeven dat de Zookeeper-client tijdig sessies beëindigt
* Zoek naar de andere symptomen die in dit document worden beschreven

## <a name="next-steps"></a>Volgende stappen

Als u het probleem niet ziet of als u het probleem niet kunt oplossen, gaat u naar een van de volgende kanalen voor meer ondersteuning:

- Krijg antwoorden van Azure-experts via de [ondersteuning van Azure Community](https://azure.microsoft.com/support/community/).
- Maak verbinding met [@AzureSupport](https://twitter.com/azuresupport) -het officiële Microsoft Azure account voor het verbeteren van de gebruikers ervaring. Verbinding maken met de Azure-community met de juiste resources: antwoorden, ondersteuning en experts.
- Als u meer hulp nodig hebt, kunt u een ondersteunings aanvraag indienen via de [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecteer **ondersteuning** in de menu balk of open de hub **Help en ondersteuning** . Lees [hoe u een ondersteunings aanvraag voor Azure kunt maken](../../azure-portal/supportability/how-to-create-azure-support-request.md)voor meer informatie. De toegang tot abonnementen voor abonnements beheer en facturering is inbegrepen bij uw Microsoft Azure-abonnement en technische ondersteuning wordt geleverd via een van de [ondersteunings abonnementen voor Azure](https://azure.microsoft.com/support/plans/).