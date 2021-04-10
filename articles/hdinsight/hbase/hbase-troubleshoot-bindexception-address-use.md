---
title: BindException-adres wordt al gebruikt in azure HDInsight
description: BindException-adres wordt al gebruikt in azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/16/2019
ms.openlocfilehash: c0a7bc7629b5c2a2e458ba94d62e341f578fdd25
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98946388"
---
# <a name="scenario-bindexception---address-already-in-use-in-azure-hdinsight"></a>Scenario: BindException-Address wordt al gebruikt in azure HDInsight

In dit artikel worden de stappen beschreven voor het oplossen van problemen en mogelijke oplossingen voor problemen bij het werken met Azure HDInsight-clusters.

## <a name="issue"></a>Probleem

De bewerking voor opnieuw opstarten op een Apache HBase Region-server kan niet worden voltooid. Vanuit de `region-server.log` in- `/var/log/hbase` map op de worker-knoop punten waar het starten van de regio server mislukt, ziet u mogelijk een fout bericht als volgt:

```
Caused by: java.net.BindException: Problem binding to /10.2.0.4:16020 : Address already in use
...

Caused by: java.net.BindException: Address already in use
...
```

## <a name="cause"></a>Oorzaak

De Apache HBase-regio servers worden opnieuw gestart tijdens een zware activiteit voor de werk belasting. Hieronder ziet u wat er gebeurt achter de schermen wanneer een gebruiker de bewerking voor het opnieuw starten van de HBase-regio server van Apache Ambari UI initieert:

1. De Ambari-agent verzendt een Stop aanvraag naar de regio server.

1. De Ambari-agent wacht 30 seconden totdat de regio server zonder problemen wordt afgesloten

1. Als uw toepassing verbinding blijft maken met de regio server, wordt de server niet onmiddellijk afgesloten. De time-out van 30 seconden verloopt voordat het afsluiten plaatsvindt.

1. Na 30 seconden verzendt de Ambari-agent een opdracht geforceerd afsluiten ( `kill -9` ) naar de regio server.

1. Als gevolg van deze abrupte afsluiting, hoewel het Server proces van de regio is afgebroken, wordt de poort die is gekoppeld aan het proces mogelijk niet vrijgegeven, wat uiteindelijk leidt tot `AddressBindException` .

## <a name="resolution"></a>Oplossing

Verminder de belasting van de HBase-regio servers voordat u de computer opnieuw opstart. Het is ook een goed idee om eerst alle tabellen te wissen. Zie voor een Naslag informatie over het leegmaken van tabellen [HDInsight HBase: How to Verbeter The Apache HBase cluster restart time door Tables te wissen](https://web.archive.org/web/20190112153155/https://blogs.msdn.microsoft.com/azuredatalake/2016/09/19/hdinsight-hbase-how-to-improve-hbase-cluster-restart-time-by-flushing-tables/).

U kunt ook de regio servers hand matig opnieuw starten op de worker-knoop punten met behulp van de volgende opdrachten:

```bash
sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh stop regionserver"
sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh start regionserver"
```

## <a name="next-steps"></a>Volgende stappen

Als u het probleem niet ziet of als u het probleem niet kunt oplossen, gaat u naar een van de volgende kanalen voor meer ondersteuning:

* Krijg antwoorden van Azure-experts via de [ondersteuning van Azure Community](https://azure.microsoft.com/support/community/).

* Maak verbinding met [@AzureSupport](https://twitter.com/azuresupport) -het officiële Microsoft Azure account voor het verbeteren van de gebruikers ervaring. Verbinding maken met de Azure-community met de juiste resources: antwoorden, ondersteuning en experts.

* Als u meer hulp nodig hebt, kunt u een ondersteunings aanvraag indienen via de [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecteer **ondersteuning** in de menu balk of open de hub **Help en ondersteuning** . Lees [hoe u een ondersteunings aanvraag voor Azure kunt maken](../../azure-portal/supportability/how-to-create-azure-support-request.md)voor meer informatie. De toegang tot abonnementen voor abonnements beheer en facturering is inbegrepen bij uw Microsoft Azure-abonnement en technische ondersteuning wordt geleverd via een van de [ondersteunings abonnementen voor Azure](https://azure.microsoft.com/support/plans/).