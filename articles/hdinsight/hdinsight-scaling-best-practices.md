---
title: Clustergrootten schalen - Azure HDInsight
description: Een Apache Hadoop-cluster elastisch schalen om deze te laten overeenkomen met uw workload in Azure HDInsight
ms.author: ashish
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020
ms.date: 04/29/2020
ms.openlocfilehash: 1c388cb070c66fc3a2322c358bc4113ed2106c77
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107761845"
---
# <a name="scale-azure-hdinsight-clusters"></a>Clusters Azure HDInsight schalen

HDInsight biedt elasticiteit met opties om het aantal werkknooppunten in uw clusters omhoog en omlaag te schalen. Met deze elasticiteit kunt u een cluster na uren of in het weekend verkleinen. En breid deze uit tijdens pieken in de bedrijfsvraag.

Schaal uw cluster omhoog vóór periodieke batchverwerking, zodat het cluster voldoende resources heeft.  Nadat de verwerking is voltooid en het gebruik omlaag gaat, schaalt u het HDInsight-cluster omlaag naar minder werkknooppunten.

U kunt een cluster handmatig schalen met behulp van een van de onderstaande methoden. U kunt ook opties [voor automatisch schalen](hdinsight-autoscale-clusters.md) gebruiken om automatisch omhoog en omlaag te schalen als reactie op bepaalde metrische gegevens.

> [!NOTE]  
> Alleen clusters met HDInsight versie 3.1.3 of hoger worden ondersteund. Als u niet zeker weet welke versie van uw cluster u hebt, kunt u de pagina Eigenschappen controleren.

## <a name="utilities-to-scale-clusters"></a>Hulpprogramma's voor het schalen van clusters

Microsoft biedt de volgende hulpprogramma's voor het schalen van clusters:

|Hulpprogramma | Beschrijving|
|---|---|
|[PowerShell Az](/powershell/azure)|[`Set-AzHDInsightClusterSize`](/powershell/module/az.hdinsight/set-azhdinsightclustersize) `-ClusterName CLUSTERNAME -TargetInstanceCount NEWSIZE`|
|[PowerShell AzureRM](/powershell/azure/azurerm) |[`Set-AzureRmHDInsightClusterSize`](/powershell/module/azurerm.hdinsight/set-azurermhdinsightclustersize) `-ClusterName CLUSTERNAME -TargetInstanceCount NEWSIZE`|
|[Azure-CLI](/cli/azure/) | [`az hdinsight resize`](/cli/azure/hdinsight#az_hdinsight_resize) `--resource-group RESOURCEGROUP --name CLUSTERNAME --workernode-count NEWSIZE`|
|[Klassieke versie van Azure-CLI](hdinsight-administer-use-command-line.md)|`azure hdinsight cluster resize CLUSTERNAME NEWSIZE` |
|[Azure-portal](https://portal.azure.com)|Open het deelvenster van het HDInsight-cluster, selecteer **Clustergrootte** in het menu aan de linkerkant, typ in het deelvenster Clustergrootte het aantal werkknooppunten en selecteer Opslaan.|  

:::image type="content" source="./media/hdinsight-scaling-best-practices/azure-portal-settings-nodes.png" alt-text="Azure Portal clusteroptie schalen":::

Met een van deze methoden kunt u uw HDInsight-cluster binnen enkele minuten omhoog of omlaag schalen.

> [!IMPORTANT]  
> * De klassieke Azure CLI is afgeschaft en mag alleen worden gebruikt met het klassieke implementatiemodel. Gebruik de Azure CLI voor alle [andere implementaties.](/cli/azure/)
> * De PowerShell AzureRM-module is afgeschaft.  Gebruik waar [mogelijk de Az-module.](/powershell/azure/new-azureps-module-az)

## <a name="impact-of-scaling-operations"></a>Impact van schaalbewerkingen

Wanneer u **knooppunten** toevoegt aan uw hdinsight-cluster dat wordt uitgevoerd (omhoog schalen), worden taken niet beïnvloed. Nieuwe taken kunnen veilig worden verzonden terwijl het schaalproces wordt uitgevoerd. Als de schaalbewerking mislukt, heeft de fout de functionele status van uw cluster.

Als u **knooppunten** verwijdert (omlaag schalen), mislukken in behandeling zijnde of lopende taken wanneer de schaalbewerking is voltooid. Deze fout is het gevolg van een aantal services die opnieuw worden opgestart tijdens het schalen. Uw cluster kan vast komen te zitten in de veilige modus tijdens een handmatige schaalbewerking.

De impact van het wijzigen van het aantal gegevensknooppunten varieert voor elk type cluster dat wordt ondersteund door HDInsight:

* Apache Hadoop

    U kunt het aantal werkknooppunten in een actief Hadoop-cluster naadloos verhogen zonder dat dit van invloed is op taken. Nieuwe taken kunnen ook worden verzonden terwijl de bewerking wordt uitgevoerd. Fouten in een schaalbewerking worden zonder problemen afgehandeld. Het cluster blijft altijd functioneel.

    Wanneer een Hadoop-cluster omlaag wordt geschaald met minder gegevensknooppunten, worden sommige services opnieuw gestart. Dit gedrag zorgt ervoor dat alle taken die worden uitgevoerd en in behandeling zijn, mislukken bij het voltooien van de schaalbewerking. U kunt de taken echter opnieuw inzenden zodra de bewerking is voltooid.

* Apache HBase

    U kunt naadloos knooppunten toevoegen aan of verwijderen uit uw HBase-cluster terwijl het wordt uitgevoerd. Regionale servers worden automatisch verdeeld binnen een paar minuten na het voltooien van de schaalbewerking. U kunt de regionale servers echter handmatig in balans brengen. Meld u aan bij het clusterhoofdknooppunt en voer de volgende opdrachten uit:

    ```bash
    pushd %HBASE_HOME%\bin
    hbase shell
    balancer
    ```

    Zie Aan de slag met een [Apache HBase-voorbeeld in HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)voor meer informatie over het gebruik van de HBase-shell.

* Apache Storm

    U kunt naadloos gegevensknooppunten toevoegen of verwijderen terwijl Storm wordt uitgevoerd. Nadat de schaalbewerking is voltooid, moet u de topologie echter opnieuw in balans brengen. Door de topologie opnieuw te hervertalen kunnen [de instellingen voor parallellisme](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html) worden aangepast op basis van het nieuwe aantal knooppunten in het cluster. Als u de lopende topologies opnieuw wilt in balans brengen, gebruikt u een van de volgende opties:

  * Storm-webinterface

    Gebruik de volgende stappen om een topologie opnieuw te in balans brengen met behulp van de Storm-gebruikersinterface.

    1. Open `https://CLUSTERNAME.azurehdinsight.net/stormui` in uw webbrowser, waarbij de naam van `CLUSTERNAME` uw Storm-cluster is. Voer des te meer de naam en het wachtwoord in van de HDInsight-clusterbeheerder (beheerder) die u hebt opgegeven bij het maken van het cluster.

    1. Selecteer de topologie die u opnieuw wilt in balans brengen en selecteer vervolgens **de knop Opnieuw** in balans brengen. Voer de vertraging in voordat de herbalanceerbewerking is uitgevoerd.

        :::image type="content" source="./media/hdinsight-scaling-best-practices/hdinsight-portal-scale-cluster-storm-rebalance.png" alt-text="HdInsight Storm-schaal opnieuw in balans brengen":::

  * Opdrachtregelinterface (CLI)

    Maak verbinding met de server en gebruik de volgende opdracht om een topologie opnieuw in balans te brengen:

    ```bash
     storm rebalance TOPOLOGYNAME
    ```

    U kunt ook parameters opgeven om de hints voor parallellelisme te overschrijven die oorspronkelijk door de topologie zijn opgegeven. Met de onderstaande code wordt de topologie bijvoorbeeld opnieuw geconfigureerd voor 5 werkprocessen, 3 uitvoerders voor het `mytopology` blue-spout-onderdeel en 10 uitvoerders voor het yellow-bolt-onderdeel.

    ```bash
    ## Reconfigure the topology "mytopology" to use 5 worker processes,
    ## the spout "blue-spout" to use 3 executors, and
    ## the bolt "yellow-bolt" to use 10 executors
    $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10
    ```

* Kafka

    U moet partitiereplica's opnieuw verdelen na het schalen van bewerkingen. Zie het document Hoge beschikbaarheid van gegevens met Apache Kafka [hdinsight voor meer](./kafka/apache-kafka-high-availability.md) informatie.

* Apache Hive LLAP

    Na het schalen naar `N` werkknooppunten worden in HDInsight automatisch de volgende configuraties ingesteld en Hive opnieuw gestart.

  * Maximumaantal gelijktijdige query's: `hive.server2.tez.sessions.per.default.queue = min(N, 32)`
  * Aantal knooppunten dat wordt gebruikt door LLAP van Hive: `num_llap_nodes  = N`
  * Aantal knooppunt(en) voor het uitvoeren van Hive LLAP-daemon: `num_llap_nodes_for_llap_daemons = N`

## <a name="how-to-safely-scale-down-a-cluster"></a>Een cluster veilig omlaag schalen

### <a name="scale-down-a-cluster-with-running-jobs"></a>Een cluster omlaag schalen met taken die worden uitgevoerd

Om te voorkomen dat uw taken mislukken tijdens een omlaag schalen, kunt u drie dingen proberen:

1. Wacht tot de taken zijn voltooid voordat u het cluster omlaag schaalt.
1. De taken handmatig beëindigen.
1. Opnieuw de taken nadat de schaalbewerking is afgerond.

Als u een lijst met taken die in behandeling zijn en worden uitgevoerd wilt bekijken, kunt u de YARN-Resource Manager **gebruiken.** Volg deze stappen:

1. Selecteer in [Azure Portal](https://portal.azure.com/)het cluster.  Het cluster wordt geopend op een nieuwe portalpagina.
2. Navigeer in de hoofdweergave naar **Clusterdashboards**  >  **Ambari home.** Voer uw clusterreferenties in.
3. Selecteer in de Ambari-gebruikersinterface **YARN** in de lijst met services in het menu aan de linkerkant.  
4. Selecteer op de pagina YARN snelle **koppelingen en** beweeg de muisaanwijzer over het actieve hoofd-knooppunt en selecteer vervolgens **Resource Manager UI.**

    :::image type="content" source="./media/hdinsight-scaling-best-practices/resource-manager-ui1.png" alt-text="Snelle koppelingen van Apache Ambari Resource Manager ui":::

U hebt rechtstreeks toegang tot Resource Manager gebruikersinterface met `https://<HDInsightClusterName>.azurehdinsight.net/yarnui/hn/cluster` .

U ziet een lijst met taken, samen met hun huidige status. In de schermopname wordt momenteel één taak uitgevoerd:

:::image type="content" source="./media/hdinsight-scaling-best-practices/resourcemanager-ui-applications.png" alt-text="Resource Manager UI-toepassingen":::

Als u de toepassing die wordt uitgevoerd handmatig wilt verwijderen, voert u de volgende opdracht uit vanuit de SSH-shell:

```bash
yarn application -kill <application_id>
```

Bijvoorbeeld:

```bash
yarn application -kill "application_1499348398273_0003"
```

### <a name="getting-stuck-in-safe-mode"></a>Blijven hangen in de veilige modus

Wanneer u een cluster omlaag schaalt, maakt HDInsight gebruik van Apache Ambari-beheerinterfaces om eerst de extra werkknooppunten uit bedrijf te nemen. De knooppunten repliceren hun HDFS-blokken naar andere online werkknooppunten. Daarna schaalt HDInsight het cluster veilig omlaag. HDFS wordt tijdens het schalen in de veilige modus uitgevoerd. HDFS moet worden uitgevoerd zodra het schalen is voltooid. In sommige gevallen loopt HDFS echter vast in de veilige modus tijdens een schaalbewerking vanwege een te grote replicatie van het bestandsblok.

HDFS is standaard geconfigureerd met de instelling 1, waarmee wordt ingesteld hoeveel kopieën van elk `dfs.replication` bestandsblok beschikbaar zijn. Elke kopie van een bestandsblok wordt opgeslagen op een ander knooppunt van het cluster.

Wanneer het verwachte aantal blokkopieen niet beschikbaar is, gaat HDFS in de veilige modus en genereert Ambari waarschuwingen. HDFS kan in de veilige modus worden uitgevoerd voor een schaalbewerking. Het cluster kan vast komen te zitten in de veilige modus als het vereiste aantal knooppunten niet wordt gedetecteerd voor replicatie.

### <a name="example-errors-when-safe-mode-is-turned-on"></a>Voorbeeldfouten wanneer de veilige modus is ingeschakeld

```output
org.apache.hadoop.hdfs.server.namenode.SafeModeException: Cannot create directory /tmp/hive/hive/819c215c-6d87-4311-97c8-4f0b9d2adcf0. Name node is in safe mode.
```

```output
org.apache.http.conn.HttpHostConnectException: Connect to active-headnode-name.servername.internal.cloudapp.net:10001 [active-headnode-name.servername. internal.cloudapp.net/1.1.1.1] failed: Connection refused
```

U kunt de naamknooppuntlogboeken bekijken vanuit de map, in de buurt van het tijdstip waarop het cluster is geschaald, om te zien wanneer `/var/log/hadoop/hdfs/` de veilige modus is ingegaan. De logboekbestanden hebben de naam `Hadoop-hdfs-namenode-<active-headnode-name>.*` .

De hoofdoorzaak is dat Hive tijdens het uitvoeren van query's afhankelijk is van tijdelijke bestanden in HDFS. Wanneer HDFS in de veilige modus komt, kan Hive geen query's uitvoeren omdat deze niet naar HDFS kunnen schrijven. Tijdelijke bestanden in HDFS bevinden zich op het lokale station dat is bevestigd aan de afzonderlijke werkpunt-VM's. De bestanden worden minimaal op drie replica's gerepliceerd tussen andere werkknooppunten.

### <a name="how-to-prevent-hdinsight-from-getting-stuck-in-safe-mode"></a>Voorkomen dat HDInsight vastloopt in de veilige modus

Er zijn verschillende manieren om te voorkomen dat HDInsight in de veilige modus wordt gelaten:

* Stop alle Hive-taken voordat u HDInsight omlaag schaalt. U kunt ook het omlaag schalen plannen om conflicten met het uitvoeren van Hive-taken te voorkomen.
* Schoon de scratchmapbestanden van Hive `tmp` handmatig op in HDFS voordat u omlaag schaalt.
* Alleen HDInsight omlaag schalen naar drie werkknooppunten, minimaal. Vermijd zo laag te gaan als één werkpunt.
* Voer de opdracht uit om de veilige modus te verlaten, indien nodig.

In de volgende secties worden deze opties beschreven.

#### <a name="stop-all-hive-jobs"></a>Alle Hive-taken stoppen

Stop alle Hive-taken voordat u omlaag schaalt naar één werkpunt. Als uw workload is gepland, voert u de omlaag schalen uit nadat het Hive-werk is uitgevoerd.

Door de Hive-taken te stoppen voordat u schaalt, wordt het aantal scratchbestanden in de map tmp (indien van toepassing) geminimaliseerd.

#### <a name="manually-clean-up-hives-scratch-files"></a>De scratchbestanden van Hive handmatig ops schonen

Als Hive tijdelijke bestanden heeft achtergelaten, kunt u deze bestanden handmatig ops schonen voordat u omlaag schaalt om de veilige modus te voorkomen.

1. Controleer welke locatie wordt gebruikt voor tijdelijke Hive-bestanden door te kijken naar de `hive.exec.scratchdir` configuratie-eigenschap. Deze parameter wordt ingesteld in `/etc/hive/conf/hive-site.xml` :

    ```xml
    <property>
        <name>hive.exec.scratchdir</name>
        <value>hdfs://mycluster/tmp/hive</value>
    </property>
    ```

1. Stop Hive-services en zorg ervoor dat alle query's en taken zijn voltooid.

1. Vermeld de inhoud van de scratchmap hierboven om te zien of `hdfs://mycluster/tmp/hive/` deze bestanden bevat:

    ```bash
    hadoop fs -ls -R hdfs://mycluster/tmp/hive/hive
    ```

    Hier is een voorbeelduitvoer wanneer er bestanden bestaan:

    ```output
    sshuser@scalin:~$ hadoop fs -ls -R hdfs://mycluster/tmp/hive/hive
    drwx------   - hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c
    drwx------   - hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/_tmp_space.db
    -rw-r--r--   3 hive hdfs         27 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/inuse.info
    -rw-r--r--   3 hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/inuse.lck
    drwx------   - hive hdfs          0 2017-07-06 20:30 hdfs://mycluster/tmp/hive/hive/c108f1c2-453e-400f-ac3e-e3a9b0d22699
    -rw-r--r--   3 hive hdfs         26 2017-07-06 20:30 hdfs://mycluster/tmp/hive/hive/c108f1c2-453e-400f-ac3e-e3a9b0d22699/inuse.info
    ```

1. Als u weet dat Hive klaar is met deze bestanden, kunt u ze verwijderen. Zorg ervoor dat Hive geen query's heeft die worden uitgevoerd door te kijken op de pagina Yarn Resource Manager ui.

    Voorbeeld van een opdrachtregel voor het verwijderen van bestanden uit HDFS:

    ```bash
    hadoop fs -rm -r -skipTrash hdfs://mycluster/tmp/hive/
    ```

#### <a name="scale-hdinsight-to-three-or-more-worker-nodes"></a>HDInsight schalen naar drie of meer werkknooppunten

Als uw clusters regelmatig vast komen te zitten in de veilige modus wanneer u omlaag schaalt naar minder dan drie werkknooppunten, moet u ten minste drie werkknooppunten behouden.

Het hebben van drie werkknooppunten is duurder dan omlaag schalen naar slechts één werkknooppunt. Met deze actie wordt echter voorkomen dat uw cluster vastloopt in de veilige modus.

### <a name="scale-hdinsight-down-to-one-worker-node"></a>HDInsight omlaag schalen naar één werk knooppunt

Zelfs wanneer het cluster omlaag wordt geschaald naar één knooppunt, blijft werkknooppunt 0 bestaan. Werk knooppunt 0 kan nooit buiten gebruik worden gesteld.

#### <a name="run-the-command-to-leave-safe-mode"></a>Voer de opdracht uit om de veilige modus te verlaten

De laatste optie is om de opdracht 'veilige modus verlaten' uit te voeren. Als HDFS in de veilige modus is gekomen vanwege de replicatie van het Hive-bestand, voert u de volgende opdracht uit om de veilige modus te verlaten:

```bash
hdfs dfsadmin -D 'fs.default.name=hdfs://mycluster/' -safemode leave
```

### <a name="scale-down-an-apache-hbase-cluster"></a>Een Apache HBase-cluster omlaag schalen

Regioservers worden binnen enkele minuten na het voltooien van een schaalbewerking automatisch in balans gebracht. Voltooi de volgende stappen om regioservers handmatig te balanceren:

1. Maak verbinding met het HDInsight-cluster met behulp van SSH. Zie [SSH gebruiken met HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md) voor meer informatie.

2. Start de HBase-shell:

    ```bash
    hbase shell
    ```

3. Gebruik de volgende opdracht om de regioservers handmatig in balans te brengen:

    ```bash
    balancer
    ```

## <a name="next-steps"></a>Volgende stappen

* [Azure HDInsight-clusters automatisch schalen](hdinsight-autoscale-clusters.md)

Zie voor specifieke informatie over het schalen van uw HDInsight-cluster:

* [Apache Hadoop-clusters in HDInsight beheren met behulp van de Azure Portal](hdinsight-administer-use-portal-linux.md#scale-clusters)
* [Apache Hadoop-clusters beheren in HDInsight met behulp van Azure CLI](hdinsight-administer-use-command-line.md#scale-clusters)