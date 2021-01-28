---
title: Apache HBase Master kan niet worden gestart in azure HDInsight
description: Apache HBase Master (HMaster) kan niet worden gestart in azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/14/2019
ms.openlocfilehash: c30077d0d8f359e93745b53755f9dae998073d4d
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98936908"
---
# <a name="apache-hbase-master-hmaster-fails-to-start-in-azure-hdinsight"></a>Apache HBase Master (HMaster) kan niet worden gestart in azure HDInsight

In dit artikel worden de stappen beschreven voor het oplossen van problemen en mogelijke oplossingen voor problemen bij het werken met Azure HDInsight-clusters.

## <a name="scenario-atomic-renaming-failure"></a>Scenario: fout bij het wijzigen van de naam Atom

### <a name="issue"></a>Probleem

Onverwachte bestanden geïdentificeerd tijdens het opstart proces.

### <a name="cause"></a>Oorzaak

Tijdens het opstart proces voert HMaster veel initialisatie stappen uit, waaronder het verplaatsen van gegevens uit een nieuwe map (. tmp) naar de map Data. HMaster controleert ook de map write-Ahead logs (WAL) om te zien of er niet-reagerende regio servers zijn.

HMaster voert een elementaire lijst opdracht uit in de WAL-mappen. Als er op elk moment HMaster een onverwacht bestand in een van deze mappen ziet, wordt er een uitzonde ring gegenereerd en start niet.

### <a name="resolution"></a>Oplossing

Controleer de aanroep stack en probeer te bepalen welke map het probleem veroorzaakt (bijvoorbeeld omdat het de map WAL of de map. tmp kan zijn). Probeer vervolgens in Cloud Explorer of met HDFS-opdrachten het probleem bestand te vinden. Normaal gesp roken is dit een `*-renamePending.json` bestand. (Het `*-renamePending.json` bestand is een logboek bestand dat wordt gebruikt voor het implementeren van de Atomic-naam bewerking in het WASB-stuur programma. Als gevolg van fouten in deze implementatie kunnen deze bestanden overblijven nadat het proces is vastgelopen, enzovoort.) Force-Hiermee kunt u dit bestand in Cloud Verkenner of met HDFS-opdrachten verwijderen.

Soms kan er ook een tijdelijk bestand zijn met de naam iets `$$$.$$$` op deze locatie. U moet de HDFS- `ls` opdracht gebruiken om dit bestand te bekijken. u kunt het bestand niet zien in Cloud Explorer. Als u dit bestand wilt verwijderen, gebruikt u de opdracht HDFS `hdfs dfs -rm /\<path>\/\$\$\$.\$\$\$` .

Nadat u deze opdrachten hebt uitgevoerd, moet HMaster onmiddellijk worden gestart.

---

## <a name="scenario-no-server-address-listed"></a>Scenario: er is geen server adres vermeld

### <a name="issue"></a>Probleem

Mogelijk wordt er een bericht weer gegeven dat aangeeft dat de `hbase: meta` tabel niet online is. Als `hbck` u dit rapport uitvoert `hbase: meta table replicaId 0 is not found on any region.` , ziet u mogelijk het volgende bericht in de HMaster-logboeken: `No server address listed in hbase: meta for region hbase: backup <region name>` .  

### <a name="cause"></a>Oorzaak

HMaster kan niet worden geïnitialiseerd na het opnieuw opstarten van HBase.

### <a name="resolution"></a>Oplossing

1. Voer in de HBase-shell de volgende opdrachten in (werkelijke waarden wijzigen indien van toepassing):

    ```hbase
    scan 'hbase:meta'
    delete 'hbase:meta','hbase:backup <region name>','<column name>'
    ```

1. Verwijder de `hbase: namespace` vermelding. Dit item is mogelijk dezelfde fout die wordt gerapporteerd wanneer de `hbase: namespace` tabel wordt gescand.

1. Start de actieve HMaster van de Ambari-gebruikers interface opnieuw op om HBase in de actieve staat te brengen.

1. Voer in de HBase-shell de volgende opdracht uit om alle offline tabellen te openen:

    ```hbase
    hbase hbck -ignorePreCheckPermission -fixAssignments
    ```

---

## <a name="scenario-javaioioexception-timedout"></a>Scenario: Java. io. IOException: out

### <a name="issue"></a>Probleem

HMaster keer een time-out met een onherstelbare uitzonde ring op: `java.io.IOException: Timedout 300000ms waiting for namespace table to be assigned` .

### <a name="cause"></a>Oorzaak

Dit probleem kan optreden als u veel tabellen en regio's hebt die niet zijn leeg gemaakt wanneer u uw HMaster-services opnieuw opstart. De time-out is een bekend defect met HMaster. Algemene opstart taken van het cluster kunnen veel tijd in beslag nemen. HMaster wordt afgesloten als de naam ruimte tabel nog niet is toegewezen. De langdurige opstart taken worden uitgevoerd, waarbij een grote hoeveelheid niet-geflushe gegevens bestaat en een time-out van vijf minuten niet voldoende is.

### <a name="resolution"></a>Oplossing

1. Ga vanuit de Apache Ambari-gebruikers interface naar **HBase**-  >  **configuraties**. Voeg in het aangepaste `hbase-site.xml` bestand de volgende instelling toe:

    ```
    Key: hbase.master.namespace.init.timeout Value: 2400000  
    ```

1. Start de vereiste services (HMaster en mogelijk andere HBase-Services) opnieuw op.

---

## <a name="scenario-frequent-region-server-restarts"></a>Scenario: het opnieuw opstarten van een server met een regel matig gebied

### <a name="issue"></a>Probleem

Knoop punten worden periodiek opnieuw opgestart. In de regio server logboeken ziet u mogelijk vermeldingen die vergelijkbaar zijn met:

```
2017-05-09 17:45:07,683 WARN  [JvmPauseMonitor] util.JvmPauseMonitor: Detected pause in JVM or host machine (eg GC): pause of approximately 31000ms
2017-05-09 17:45:07,683 WARN  [JvmPauseMonitor] util.JvmPauseMonitor: Detected pause in JVM or host machine (eg GC): pause of approximately 31000ms
2017-05-09 17:45:07,683 WARN  [JvmPauseMonitor] util.JvmPauseMonitor: Detected pause in JVM or host machine (eg GC): pause of approximately 31000ms
```

### <a name="cause"></a>Oorzaak

Lange `regionserver` JVM °c pauzeren. De onderbreking `regionserver` reageert niet meer en kan geen harte slag verzenden naar HMaster binnen de time-out van de ZK-sessie 40s. HMaster is van mening dat het inactief `regionserver` is en afbreekt `regionserver` en opnieuw wordt gestart.

### <a name="resolution"></a>Oplossing

Wijzig de time-out van de Zookeeper-sessie, niet alleen `hbase-site` instelling, `zookeeper.session.timeout` maar ook de `zoo.cfg` instelling Zookeeper `maxSessionTimeout` moet worden gewijzigd.

1. Open de Ambari-gebruikers interface, ga naar **HBase-> configuraties-> instellingen** in de sectie time-outs, wijzig de waarde van Zookeeper sessietime time-out.

1. Toegang tot de Ambari-gebruikers interface, gaat u naar **Zookeeper-> configs-> aangepast** `zoo.cfg` , voegt u de volgende instelling toe of wijzigt u deze. Zorg ervoor dat de waarde gelijk is aan HBase `zookeeper.session.timeout` .

    ```
    Key: maxSessionTimeout Value: 120000  
    ```

1. De vereiste services opnieuw opstarten.

---

## <a name="scenario-log-splitting-failure"></a>Scenario: fout bij het splitsen van Logboeken

### <a name="issue"></a>Probleem

HMasters is niet beschikbaar op een HBase-cluster.

### <a name="cause"></a>Oorzaak

Onjuist geconfigureerde HDFS-en HBase-instellingen voor een secundair opslag account.

### <a name="resolution"></a>Oplossing

Stel hbase. rootdir: wasb://@.blob.core.windows.net/hbase en start services opnieuw op Ambari.

---

## <a name="next-steps"></a>Volgende stappen

Als u het probleem niet ziet of als u het probleem niet kunt oplossen, gaat u naar een van de volgende kanalen voor meer ondersteuning:

* Krijg antwoorden van Azure-experts via de [ondersteuning van Azure Community](https://azure.microsoft.com/support/community/).

* Maak verbinding met [@AzureSupport](https://twitter.com/azuresupport) -het officiële Microsoft Azure account voor het verbeteren van de gebruikers ervaring. Verbinding maken met de Azure-community met de juiste resources: antwoorden, ondersteuning en experts.

* Als u meer hulp nodig hebt, kunt u een ondersteunings aanvraag indienen via de [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecteer **ondersteuning** in de menu balk of open de hub **Help en ondersteuning** . Lees [hoe u een ondersteunings aanvraag voor Azure kunt maken](../../azure-portal/supportability/how-to-create-azure-support-request.md)voor meer informatie. De toegang tot abonnementen voor abonnements beheer en facturering is inbegrepen bij uw Microsoft Azure-abonnement en technische ondersteuning wordt geleverd via een van de [ondersteunings abonnementen voor Azure](https://azure.microsoft.com/support/plans/).