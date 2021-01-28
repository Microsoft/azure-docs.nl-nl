---
title: Verbindings problemen Apache Phoenix in azure HDInsight
description: Verbindings problemen tussen Apache HBase en Apache Phoenix in azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/14/2019
ms.openlocfilehash: 2cc6556f681ece170bdfe02b985f56274c0faa1e
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98936972"
---
# <a name="scenario-apache-phoenix-connectivity-issues-in-azure-hdinsight"></a>Scenario: verbindings problemen Apache Phoenix in azure HDInsight

In dit artikel worden de stappen beschreven voor het oplossen van problemen en mogelijke oplossingen voor problemen bij het werken met Azure HDInsight-clusters.

## <a name="issue"></a>Probleem

Kan geen verbinding maken met Apache HBase met Apache Phoenix. Redenen kunnen variëren.

## <a name="cause-incorrect-ip"></a>Oorzaak: onjuist IP-adres

Onjuist IP-adres van actief Zookeeper-knoop punt.

### <a name="resolution"></a>Oplossing

Het IP-adres van het actieve Zookeeper-knoop punt kan worden geïdentificeerd via de Ambari-gebruikers interface door de koppelingen naar **HBase**  >  **Quick links**  >  **ZK (Active)**  >  **Zookeeper info** te volgen. Corrigeer het IP-adres als dat nodig is.

---

## <a name="cause-systemcatalog-table-offline"></a>Oorzaak: systeem. CATALOGUS tabel offline

Bij het uitvoeren van opdrachten `!tables` , zoals, wordt een fout bericht van de volgende strekking weer gegeven:

```output
Error while connecting to sqlline.py (Hbase - phoenix) Setting property: [isolation, TRANSACTION_READ_COMMITTED] issuing: !connect jdbc:phoenix:10.2.0.7 none none org.apache.phoenix.jdbc.PhoenixDriver Connecting to jdbc:phoenix:10.2.0.7 SLF4J: Class path contains multiple SLF4J bindings.
```

Bij het uitvoeren van opdrachten `count 'SYSTEM.CATALOG'` , zoals, wordt een fout bericht van de volgende strekking weer gegeven:

```output
ERROR: org.apache.hadoop.hbase.NotServingRegionException: Region SYSTEM.CATALOG,,1485464083256.c0568c94033870c517ed36c45da98129. is not online on 10.2.0.5,16020,1489466172189)
```

### <a name="resolution"></a>Oplossing

In de Apache Ambari-gebruikers interface voert u de volgende stappen uit om de HMaster-service opnieuw op alle ZooKeeper-knoop punten te starten:

1. Ga in de sectie **samen vatting** van HBase naar **HBase**  >  **Active HBase Master**.

1. Start de HBase Master-service opnieuw op in de sectie **onderdelen** .

1. Herhaal deze stappen voor alle resterende **stand-by-HBase Master** Services.

Het kan tot vijf minuten duren voordat de HBase Master service het herstel heeft gestabiliseerd en voltooid. Wanneer de `SYSTEM.CATALOG` tabel weer normaal is, wordt het connectiviteits probleem met Apache Phoenix automatisch opgelost.

## <a name="next-steps"></a>Volgende stappen

Als u het probleem niet ziet of als u het probleem niet kunt oplossen, gaat u naar een van de volgende kanalen voor meer ondersteuning:

* Krijg antwoorden van Azure-experts via de [ondersteuning van Azure Community](https://azure.microsoft.com/support/community/).

* Maak verbinding met [@AzureSupport](https://twitter.com/azuresupport) -het officiële Microsoft Azure account voor het verbeteren van de gebruikers ervaring. Verbinding maken met de Azure-community met de juiste resources: antwoorden, ondersteuning en experts.

* Als u meer hulp nodig hebt, kunt u een ondersteunings aanvraag indienen via de [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecteer **ondersteuning** in de menu balk of open de hub **Help en ondersteuning** . Lees [hoe u een ondersteunings aanvraag voor Azure kunt maken](../../azure-portal/supportability/how-to-create-azure-support-request.md)voor meer informatie. De toegang tot abonnementen voor abonnements beheer en facturering is inbegrepen bij uw Microsoft Azure-abonnement en technische ondersteuning wordt geleverd via een van de [ondersteunings abonnementen voor Azure](https://azure.microsoft.com/support/plans/).