---
title: Problemen met regio servers in azure HDInsight
description: Problemen met regio servers in azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/16/2019
ms.openlocfilehash: 968a0c6e1717245171bf84821a58cad4e440046e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98936613"
---
# <a name="issues-with-region-servers-in-azure-hdinsight"></a>Problemen met regio servers in azure HDInsight

In dit artikel worden de stappen beschreven voor het oplossen van problemen en mogelijke oplossingen voor problemen bij het werken met Azure HDInsight-clusters.

## <a name="scenario-unassigned-regions"></a>Scenario: niet-toegewezen regio's

### <a name="issue"></a>Probleem

Wanneer `hbase hbck` u de opdracht uitvoert, wordt een fout bericht weer gegeven dat er ongeveer als volgt uitziet:

```
multiple regions being unassigned or holes in the chain of regions
```

Vanuit de Apache-HBase Master gebruikers interface ziet u het aantal regio's dat niet in balans is over alle regio servers. Vervolgens kunt u `hbase hbck` de opdracht uitvoeren om gaten in de regio keten weer te geven.

### <a name="cause"></a>Oorzaak

Openingen kunnen het resultaat zijn van offline regio's.

### <a name="resolution"></a>Oplossing

Los de toewijzingen op. Volg de onderstaande stappen om de niet-toegewezen regio's weer in de normale staat te brengen:

1. Meld u met SSH aan bij het HDInsight HBase-cluster.

1. Voer de `hbase zkcli` opdracht uit om verbinding te maken met de ZooKeeper-shell.

1. Uitvoeren `rmr /hbase/regions-in-transition` of `rmr /hbase-unsecure/regions-in-transition` opdracht.

1. Sluit Zookeeper-shell af met behulp van de `exit` opdracht.

1. Open de Apache Ambari-gebruikers interface en start de Active HBase Master-service opnieuw.

1. Voer `hbase hbck` de opdracht opnieuw uit (zonder verdere opties). Controleer de uitvoer en zorg ervoor dat alle regio's worden toegewezen.

---

## <a name="scenario-dead-region-servers"></a>Scenario: Dead-regio servers

### <a name="issue"></a>Probleem

De regio servers kunnen niet worden gestart.

### <a name="cause"></a>Oorzaak

Meerdere mappen voor het splitsen van WAL.

1. Lijst met huidige WALs ophalen: `hadoop fs -ls -R /hbase/WALs/ > /tmp/wals.out` .

1. Inspecteer het `wals.out` bestand. Als er te veel splitsings mappen zijn (te beginnen met *-splitsen), mislukt de regio server waarschijnlijk vanwege deze directory's.

### <a name="resolution"></a>Oplossing

1. Stop HBase van de Ambari-Portal.

1. Voer `hadoop fs -ls -R /hbase/WALs/ > /tmp/wals.out` uit om een nieuwe lijst met WALs op te halen.

1. Verplaats de *-splitsings mappen naar een tijdelijke map `splitWAL` en verwijder de *-splitsings mappen.

1. Voer de `hbase zkcli` opdracht uit om verbinding te maken met de Zookeeper-shell.

1. Uitvoeren `rmr /hbase-unsecure/splitWAL` .

1. Start de HBase-service opnieuw.

## <a name="next-steps"></a>Volgende stappen

Als u het probleem niet ziet of als u het probleem niet kunt oplossen, gaat u naar een van de volgende kanalen voor meer ondersteuning:

* Krijg antwoorden van Azure-experts via de [ondersteuning van Azure Community](https://azure.microsoft.com/support/community/).

* Maak verbinding met [@AzureSupport](https://twitter.com/azuresupport) -het officiële Microsoft Azure account voor het verbeteren van de gebruikers ervaring. Verbinding maken met de Azure-community met de juiste resources: antwoorden, ondersteuning en experts.

* Als u meer hulp nodig hebt, kunt u een ondersteunings aanvraag indienen via de [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecteer **ondersteuning** in de menu balk of open de hub **Help en ondersteuning** . Lees [hoe u een ondersteunings aanvraag voor Azure kunt maken](../../azure-portal/supportability/how-to-create-azure-support-request.md)voor meer informatie. De toegang tot abonnementen voor abonnements beheer en facturering is inbegrepen bij uw Microsoft Azure-abonnement en technische ondersteuning wordt geleverd via een van de [ondersteunings abonnementen voor Azure](https://azure.microsoft.com/support/plans/).