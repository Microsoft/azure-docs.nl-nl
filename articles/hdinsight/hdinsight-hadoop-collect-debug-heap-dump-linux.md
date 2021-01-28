---
title: Heap-dumps inschakelen voor Apache Hadoop Services op HDInsight-Azure
description: Schakel heap-dumps in voor Apache Hadoop services van HDInsight-clusters op basis van Linux voor fout opsporing en analyse.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/02/2020
ms.openlocfilehash: 824ba2c3316ccb34b59a9e435b9a6e582f137090
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98945916"
---
# <a name="enable-heap-dumps-for-apache-hadoop-services-on-linux-based-hdinsight"></a>Heap-dumps inschakelen voor Apache Hadoop Services op HDInsight op basis van Linux

[!INCLUDE [heapdump-selector](../../includes/hdinsight-selector-heap-dump.md)]

Heap-dumps bevatten een moment opname van het geheugen van de toepassing, met inbegrip van de waarden van variabelen op het moment dat de dump werd gemaakt. Ze zijn dus handig voor het vaststellen van problemen die zich tijdens runtime voordoen.

## <a name="services"></a>Services

U kunt heap-dumps inschakelen voor de volgende services:

* **Apache hcatalog** -Tempelton
* **Apache Hive** -hiveserver2, meta Store, derbyserver
* **MapReduce** -jobhistoryserver
* **Apache garens** -Resource Manager, nodemanager, timelineserver
* **Apache hdfs** -DataNode, secondarynamenode, namenode

U kunt ook heap-dumps inschakelen voor de kaart en processen verminderen die door HDInsight worden uitgevoerd.

## <a name="understanding-heap-dump-configuration"></a>Informatie over heap-dump configuratie

Heap-dumps worden ingeschakeld door de opties voor het door geven (ook wel "kiest of para meters" genoemd) aan de JVM toe te staan wanneer een service wordt gestart. Voor de meeste [Apache Hadoop](https://hadoop.apache.org/) Services kunt u het shell script dat wordt gebruikt om de service te starten, zodanig wijzigen dat deze opties worden door gegeven.

In elk script is er een export voor **\* \_ kiest**, die de opties bevat die zijn door gegeven aan de JVM. In het script **Hadoop-env.sh** bevat bijvoorbeeld de regel die begint met `export HADOOP_NAMENODE_OPTS=` de opties voor de NameNode-service.

Het toewijzen en verminderen van processen is iets anders, omdat deze bewerkingen een onderliggend proces van de MapReduce-service zijn. Elke kaart of vermindert proces wordt uitgevoerd in een onderliggende container en er zijn twee vermeldingen die de JVM-opties bevatten. Beide bevinden zich in **mapred-site.xml**:

* **MapReduce. admin. map. Child. java. kiest**
* **MapReduce. admin. reduce. Child. java. kiest**

> [!NOTE]  
> We raden u aan [Apache Ambari](https://ambari.apache.org/) te gebruiken voor het wijzigen van de instellingen voor scripts en mapred-site.xml, omdat Ambari de replicatie van wijzigingen tussen knoop punten in het cluster wijzigt. Zie de sectie [Apache Ambari gebruiken](#using-apache-ambari) voor specifieke stappen.

### <a name="enable-heap-dumps"></a>Heapdumps inschakelen

Met de volgende optie worden heap-dumps ingeschakeld wanneer er een OutOfMemoryError optreedt:

`-XX:+HeapDumpOnOutOfMemoryError`

De **+** geeft aan dat deze optie is ingeschakeld. Uitgeschakeld is de standaardinstelling.

> [!WARNING]  
> Heap-services worden niet standaard ingeschakeld op HDInsight wanneer de dump bestanden groot kunnen zijn. Als u deze optie inschakelt voor het oplossen van problemen, vergeet dan niet om deze uit te scha kelen wanneer u het probleem hebt verveelvoudigd en de dump bestanden hebt verzameld.

### <a name="dump-location"></a>Dump locatie

De standaard locatie voor het dump bestand is de huidige werkmap. U kunt bepalen waar het bestand wordt opgeslagen met behulp van de volgende optie:

`-XX:HeapDumpPath=/path`

Als u bijvoorbeeld gebruikt, `-XX:HeapDumpPath=/tmp` worden de dumps opgeslagen in de map/tmp-map.

### <a name="scripts"></a>Scripts

U kunt ook een script activeren als er een **OutOfMemoryError** optreedt. U kunt bijvoorbeeld een melding activeren, zodat u weet dat de fout is opgetreden. Gebruik de volgende optie om een script op een __OutOfMemoryError__ te starten:

`-XX:OnOutOfMemoryError=/path/to/script`

> [!NOTE]  
> Omdat Apache Hadoop een gedistribueerd systeem is, moet elk gebruikt script op alle knoop punten in het cluster worden geplaatst waarop de-service wordt uitgevoerd.
> 
> Het script moet zich ook bevindt op een locatie die toegankelijk is voor het account waarop de service wordt uitgevoerd, en moet machtigingen voor uitvoeren opgeven. U kunt bijvoorbeeld scripts opslaan in `/usr/local/bin` en gebruiken `chmod go+rx /usr/local/bin/filename.sh` om machtigingen voor lezen en uitvoeren te verlenen.

## <a name="using-apache-ambari"></a>Apache Ambari gebruiken

Als u de configuratie voor een service wilt wijzigen, gebruikt u de volgende stappen:

1. Navigeer in een webbrowser naar `https://CLUSTERNAME.azurehdinsight.net`, waarbij `CLUSTERNAME` de naam van uw cluster is.

2. Selecteer in de lijst met aan de linkerkant het service gebied dat u wilt wijzigen. Bijvoorbeeld **HDFS**. Selecteer in het middelste gebied het tabblad **configuratie** .

    ![Afbeelding van het tabblad Ambari web met HDFS-configuraties geselecteerd](./media/hdinsight-hadoop-collect-debug-heap-dump-linux/hdi-service-config-tab.png)

3. Voer in het veld **filter...** de waarde **kiest**. Alleen items met deze tekst worden weer gegeven.

    ![Gefilterde lijst Apache Ambari config](./media/hdinsight-hadoop-collect-debug-heap-dump-linux/hdinsight-filter-list.png)

4. Zoek het **item omlaag voor \* \_ de service** waarvoor u de opslag van heap-dumps wilt inschakelen en voeg de opties toe die u wilt inschakelen. In de volgende afbeelding hebt `-XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=/tmp/` u de vermelding **HADOOP \_ NAMENODE \_ kiest** :

    ![Apache Ambari Hadoop-namenode-kiest](./media/hdinsight-hadoop-collect-debug-heap-dump-linux/hadoop-namenode-opts.png)

   > [!NOTE]  
   > Wanneer u heap-dumps inschakelt voor de kaart of een onderliggend proces verminderen, zoekt u naar de velden met de naam **MapReduce. admin. map. Child. java. kiest** en **MapReduce. admin. reduce. Child. java. kiest**.

    Gebruik de knop **Opslaan** om de wijzigingen op te slaan. U kunt een korte notitie invoeren waarin de wijzigingen worden beschreven.

5. Zodra de wijzigingen zijn toegepast, wordt het pictogram **opnieuw opstarten vereist** weer gegeven naast een of meer services.

    ![pictogram opnieuw opstarten vereist en opnieuw starten](./media/hdinsight-hadoop-collect-debug-heap-dump-linux/restart-required-icon.png)

6. Selecteer elke service die opnieuw moet worden opgestart en gebruik de knop **service acties** om de **onderhouds modus in te scha kelen**. Onderhouds modus voor komt dat waarschuwingen worden gegenereerd van de service wanneer u deze opnieuw opstart.

    ![Menu onderhouds modus HDI inschakelen](./media/hdinsight-hadoop-collect-debug-heap-dump-linux/hdi-maintenance-mode.png)

7. Wanneer u de onderhouds modus hebt ingeschakeld, gebruikt u de knop **opnieuw opstarten** zodat de service **alle gevolgen opnieuw start**

    ![Apache Ambari alle betrokken vermeldingen opnieuw opstarten](./media/hdinsight-hadoop-collect-debug-heap-dump-linux/hdi-restart-all-button.png)

   > [!NOTE]  
   > De vermeldingen voor de knop **opnieuw opstarten** kunnen verschillen voor andere services.

8. Zodra de services opnieuw zijn opgestart, gebruikt u de knop **service acties** om de **onderhouds modus uit te scha kelen**. Deze Ambari om de bewaking van waarschuwingen voor de service te hervatten.