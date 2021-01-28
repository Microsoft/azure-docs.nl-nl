---
title: 'Zelfstudie: HDInsight Apache Storm naar opslag - Azure/Data Lake'
description: 'Zelfstudie: informatie over het gebruik van Apache Storm om te schrijven naar de met HDFS compatibele opslag voor Azure HDInsight.'
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 06/24/2019
ms.openlocfilehash: 4e648c57be699620e669ce7db0845dad2b876095
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98932551"
---
# <a name="tutorial-write-to-apache-hadoop-hdfs-from-apache-storm-on-azure-hdinsight"></a>Zelfstudie: Schrijven naar Apache Hadoop HDFS van Apache Storm in Azure HDInsight

Deze zelfstudie laat zien hoe u Apache Storm kunt gebruiken om gegevens te schrijven naar de met HDFS compatibele opslag die wordt gebruikt door Apache Storm in HDInsight. In HDInsight kan zowel Azure Storage als Azure Data Lake Storage worden gebruikt als met HDFS compatibele opslag. Storm bevat een [HdfsBolt](https://storm.apache.org/releases/current/javadocs/org/apache/storm/hdfs/bolt/HdfsBolt.html)-onderdeel dat gegevens naar HDFS schrijft. Dit document bevat informatie over het schrijven naar een van beide typen opslag met HdfsBolt.

De voorbeeldtopologie die in dit document wordt gebruikt, is afhankelijk van onderdelen die zijn opgenomen in Storm in HDInsight. Aanpassing kan nodig zijn als u Azure Data Lake Storage wilt gebruiken in combinatie met andere Apache Storm-clusters.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Het cluster configureren met een scriptactie
> * De topologie bouwen en inpakken
> * De topologie implementeren en uitvoeren
> * Uitvoergegevens weergeven
> * De topologie stoppen

## <a name="prerequisites"></a>Vereisten

* [Java Developer Kit (JDK) versie 8](/azure/developer/java/fundamentals/java-jdk-long-term-support)

* [Apache Maven](https://maven.apache.org/download.cgi) correct [geïnstalleerd](https://maven.apache.org/install.html) volgens Apache.  Maven is een systeem voor het bouwen van Java-projecten.

* Een SSH-client. Zie voor meer informatie [Verbinding maken met HDInsight (Apache Hadoop) via SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

* Het [URI-schema](../hdinsight-hadoop-linux-information.md#URI-and-scheme) voor de primaire opslag voor uw clusters. Dit is `wasb://` voor Azure Storage, `abfs://` voor Azure Data Lake Storage Gen2 of `adl://` voor Azure Data Lake Storage Gen1. Als beveiligde overdracht is ingeschakeld voor Azure Storage, wordt de URI `wasbs://`.  Zie ook [beveiligde overdracht](../../storage/common/storage-require-secure-transfer.md).

### <a name="example-configuration"></a>Voorbeeldconfiguratie

De volgende YAML is een fragment van het bestand `resources/writetohdfs.yaml` uit het voorbeeld. Dit bestand definieert de Storm-topologie met behulp van het [Flux](https://storm.apache.org/releases/current/flux.html)-framework voor Apache Storm.

```yaml
components:
  - id: "syncPolicy"
    className: "org.apache.storm.hdfs.bolt.sync.CountSyncPolicy"
    constructorArgs:
      - 1000

  # Rotate files when they hit 5 MB
  - id: "rotationPolicy"
    className: "org.apache.storm.hdfs.bolt.rotation.FileSizeRotationPolicy"
    constructorArgs:
      - 5
      - "MB"

  - id: "fileNameFormat"
    className: "org.apache.storm.hdfs.bolt.format.DefaultFileNameFormat"
    configMethods:
      - name: "withPath"
        args: ["${hdfs.write.dir}"]
      - name: "withExtension"
        args: [".txt"]

  - id: "recordFormat"
    className: "org.apache.storm.hdfs.bolt.format.DelimitedRecordFormat"
    configMethods:
      - name: "withFieldDelimiter"
        args: ["|"]

# spout definitions
spouts:
  - id: "tick-spout"
    className: "com.microsoft.example.TickSpout"
    parallelism: 1


# bolt definitions
bolts:
  - id: "hdfs-bolt"
    className: "org.apache.storm.hdfs.bolt.HdfsBolt"
    configMethods:
      - name: "withConfigKey"
        args: ["hdfs.config"]
      - name: "withFsUrl"
        args: ["${hdfs.url}"]
      - name: "withFileNameFormat"
        args: [ref: "fileNameFormat"]
      - name: "withRecordFormat"
        args: [ref: "recordFormat"]
      - name: "withRotationPolicy"
        args: [ref: "rotationPolicy"]
      - name: "withSyncPolicy"
        args: [ref: "syncPolicy"]
```

Deze YAML definieert de volgende items:

* `syncPolicy`: Hiermee wordt gedefinieerd wanneer bestanden in het bestandssysteem worden gesynchroniseerd/verwijderd. In dit voorbeeld is dat elke 1000 tuples.
* `fileNameFormat`: Hiermee definieert u het pad- en bestandsnaampatroon dat moet worden gebruikt bij het schrijven van bestanden. In dit voorbeeld wordt het pad tijdens runtime opgegeven met behulp van een filter, en de bestandsextensie is `.txt`.
* `recordFormat`: Hiermee wordt de interne indeling van de geschreven bestanden gedefinieerd. In dit voorbeeld wordt `|` gebruikt als veldscheidingsteken.
* `rotationPolicy`: Hiermee wordt gedefinieerd wanneer bestanden worden gedraaid. In dit voorbeeld wordt er geen draaiing uitgevoerd.
* `hdfs-bolt`: Hiermee worden de vorige onderdelen gebruikt als configuratieparameters voor de klasse `HdfsBolt`.

Zie [https://storm.apache.org/releases/current/flux.html](https://storm.apache.org/releases/current/flux.html) voor meer informatie over het Flux-framework.

## <a name="configure-the-cluster"></a>Het cluster configureren

Storm in HDInsight bevat standaard niet de onderdelen die in `HdfsBolt` worden gebruikt voor communicatie met Azure Storage of Data Lake Storage in het klassepad van Storm. Gebruik de volgende scriptactie om deze onderdelen toe te voegen aan de map `extlib` voor Storm in uw cluster:

| Eigenschap | Waarde |
|---|---|
|Scripttype |- Aangepast|
|Bash-script-URI |`https://hdiconfigactions.blob.core.windows.net/linuxstormextlibv01/stormextlib.sh`|
|Knooppunttype(n) |Nimbus, Supervisor|
|Parameters |Geen|

Zie het document [HDInsight-clusters aanpassen met behulp van scriptacties](./../hdinsight-hadoop-customize-cluster-linux.md) voor informatie over het gebruik van dit script in uw cluster.

## <a name="build-and-package-the-topology"></a>De topologie bouwen en inpakken

1. Download het voorbeeldproject van [https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store](https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store) naar uw ontwikkelomgeving.

2. Navigeer met behulp van een opdrachtprompt, terminal of shell-sessie naar de hoofdmap van het gedownloade project. Gebruik de volgende opdracht om de topologie te compileren en in te pakken:

    ```cmd
    mvn compile package
    ```

    Na het compileren en inpakken hebt u een nieuwe map met de naam `target`, die een bestand bevat met de naam `StormToHdfs-1.0-SNAPSHOT.jar`. Dit bestand bevat de gecompileerde topologie.

## <a name="deploy-and-run-the-topology"></a>De topologie implementeren en uitvoeren

1. Gebruik de volgende opdracht om de topologie naar het HDInsight-cluster te kopiëren. Vervang `CLUSTERNAME` door de naam van het cluster.

    ```cmd
    scp target\StormToHdfs-1.0-SNAPSHOT.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:StormToHdfs-1.0-SNAPSHOT.jar
    ```

1. Nadat het uploaden is voltooid, gebruikt u de volgende om via SSH verbinding te maken met het HDInsight-cluster. Vervang `CLUSTERNAME` door de naam van het cluster.

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Als de verbinding tot stand is gebracht, gebruikt u de volgende opdracht om een bestand met de naam `dev.properties` te maken:

    ```bash
    nano dev.properties
    ```

1. Gebruik de volgende tekst als de inhoud van het bestand `dev.properties`. Wijzig deze indien nodig op basis van uw [URI-schema](../hdinsight-hadoop-linux-information.md#URI-and-scheme).

    ```
    hdfs.write.dir: /stormdata/
    hdfs.url: wasbs:///
    ```

    Als u het bestand wilt opslaan, gebruikt u achtereenvolgens __Ctrl+X__, __J__ en __Enter__. Met de waarden in dit bestand stelt u de opslag-URL en de naam in van de map waarnaar gegevens worden geschreven.

1. Gebruik de volgende opdracht om de topologie te starten:

    ```bash
    storm jar StormToHdfs-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /writetohdfs.yaml --filter dev.properties
    ```

    Met deze opdracht wordt de topologie gestart met behulp van het Flux-framework door deze te verzenden naar het Nimbus-knooppunt van het cluster. De topologie wordt gedefinieerd door het bestand `writetohdfs.yaml`, dat in het jar-bestand is opgenomen. Het bestand `dev.properties` wordt doorgegeven als een filter, en de waarden in het bestand worden door de topologie gelezen.

## <a name="view-output-data"></a>Uitvoergegevens weergeven

Gebruik de volgende opdracht om de gegevens weer te geven:

  ```bash
  hdfs dfs -ls /stormdata/
  ```

Er wordt een lijst weergegeven met de bestanden die door deze topologie zijn gemaakt. De volgende lijst bevat een voorbeeld van de gegevens die door de vorige opdrachten worden geretourneerd:

```output
Found 23 items
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-0-1561407909895.txt
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-1-1561407915577.txt
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-10-1561407943327.txt
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-11-1561407946312.txt
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-12-1561407949320.txt
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-13-1561407952662.txt
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-14-1561407955502.txt
```

## <a name="stop-the-topology"></a>De topologie stoppen

Storm-topologieën worden uitgevoerd totdat ze worden gestopt of het cluster wordt verwijderd. Gebruik de volgende opdracht om de topologie te stoppen:

```bash
storm kill hdfswriter
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u de in deze zelfstudie gemaakte resources wilt opschonen, kunt u de resourcegroep verwijderen. Als u de resourcegroep verwijdert, worden ook het bijbehorende HDInsight-cluster en eventuele andere resources die aan de resourcegroep zijn gekoppeld, verwijderd.

Ga als volgt te werk om de resourcegroep te verwijderen in Azure Portal:

1. Vouw het menu aan de linkerkant in Azure Portal uit om het menu met services te openen en kies __Resourcegroepen__ om de lijst met resourcegroepen weer te geven.
2. Zoek de resourcegroep die u wilt verwijderen en klik met de rechtermuisknop op de knop __Meer__ (... ) aan de rechterkant van de vermelding.
3. Selecteer __Resourcegroep verwijderen__ en bevestig dit.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u Apache Storm gebruikt om gegevens te schrijven naar de met HDFS compatibele opslag die wordt gebruikt door Apache Storm in HDInsight.

> [!div class="nextstepaction"]
> Andere [Apache Storm-voorbeelden voor HDInsight](apache-storm-example-topology.md) ontdekken