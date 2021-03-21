---
title: 'Snelstart: Apache HBase en Apache Phoenix - Azure HDInsight'
description: In deze quickstart leert u hoe u Apache Phoenix kunt gebruiken in HDInsight. U leert ook hoe u SQLLine op uw computer installeert en instelt om verbinding te maken met een HBase-cluster in HDInsight.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: quickstart
ms.date: 06/12/2019
ms.openlocfilehash: 6b9196d9ad5cf07cd210726bc1af7121c08094a8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98935986"
---
# <a name="quickstart-query-apache-hbase-in-azure-hdinsight-with-apache-phoenix"></a>Snelstart: Query Apache HBase in Azure HDInsight met Apache Phoenix

In deze quickstart leert u hoe u Apache Phoenix kunt gebruiken om HBase-query's uit te voeren in Azure HDInsight. Apache Phoenix is a SQL-query-engine voor Apache HBase. Deze is toegankelijk als een JDBC-stuurprogramma en maakt het uitvoeren van query's en beheren van HBase-tabellen SQL mogelijk. [SQLLine](http://sqlline.sourceforge.net/) is een opdrachtregelprogramma voor het uitvoeren van SQL.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

* Een Apache HBase-cluster. Raadpleeg [Cluster maken](../hadoop/apache-hadoop-linux-tutorial-get-started.md) voor het maken van een HDInsight-cluster.  Zorg ervoor dat u het clustertype **HBase** kiest.

* Een SSH-client. Zie voor meer informatie [Verbinding maken met HDInsight (Apache Hadoop) via SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="identify-a-zookeeper-node"></a>Een ZooKeeper-knooppunt identificeren

Wanneer u verbinding maakt met een HBase-cluster, moet u verbinding maken met een van de Apache ZooKeeper-knooppunten. Elk HDInsight-cluster heeft drie ZooKeeper-knooppunten. cURL kan worden gebruikt om snel een ZooKeeper-knooppunt te identificeren. Bewerk de onderstaande cURL-opdracht door `PASSWORD` en `CLUSTERNAME` te vervangen door de relevante waarden, en voer vervolgens deze opdracht uit in een opdrachtprompt:

```cmd
curl -u admin:PASSWORD -sS -G https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER
```

Een deel van de uitvoer ziet er ongeveer als volgt uit:

```output
    {
      "href" : "http://hn1-brim.432dc3rlshou3ocf251eycoapa.bx.internal.cloudapp.net:8080/api/v1/clusters/myCluster/hosts/zk0-brim.432dc3rlshou3ocf251eycoapa.bx.internal.cloudapp.net/host_components/ZOOKEEPER_SERVER",
      "HostRoles" : {
        "cluster_name" : "myCluster",
        "component_name" : "ZOOKEEPER_SERVER",
        "host_name" : "zk0-brim.432dc3rlshou3ocf251eycoapa.bx.internal.cloudapp.net"
      }
```

Noteer de waarde van `host_name` voor later gebruik.

## <a name="create-a-table-and-manipulate-data"></a>Een tabel maken en gegevens bewerken

U kunt SSH gebruiken om verbinding te maken met HBase-clusters en vervolgens Apache Phoenix gebruiken om HBase-tabellen te maken, gegevens in te voegen, en gegevens te doorzoeken.

1. Gebruik de opdracht `ssh` om verbinding te maken met uw HBase-cluster. Bewerk de onderstaande opdracht door `CLUSTERNAME` te vervangen door de naam van uw cluster.Voer vervolgens deze opdracht in:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Wijzig de map in de map van de Phoenix-client. Voer de volgende opdracht in:

    ```bash
    cd /usr/hdp/current/phoenix-client/bin
    ```

3. Start [SQLLine](http://sqlline.sourceforge.net/). Bewerk de onderstaande opdracht door `ZOOKEEPER` te vervangen door het eerder geïdentificeerde ZooKeeper-knooppunt. Voer vervolgens deze opdracht in:

    ```bash
    ./sqlline.py ZOOKEEPER:2181:/hbase-unsecure
    ```

4. Maak een HBase-tabel. Voer de volgende opdracht in:

    ```sql
    CREATE TABLE Company (company_id INTEGER PRIMARY KEY, name VARCHAR(225));
    ```

5. Gebruik de SQLLine-opdracht `!tables` om alle tabellen in HBase weer te geven. Voer de volgende opdracht in:

    ```sqlline
    !tables
    ```

6. Voeg waarden in de tabel in. Voer de volgende opdracht in:

    ```sql
    UPSERT INTO Company VALUES(1, 'Microsoft');
    UPSERT INTO Company VALUES(2, 'Apache');
    ```

7. Voer een query op de tabel uit. Voer de volgende opdracht in:

    ```sql
    SELECT * FROM Company;
    ```

8. Verwijder een record. Voer de volgende opdracht in:

    ```sql
    DELETE FROM Company WHERE COMPANY_ID=1;
    ```

9. Verwijder de tabel. Voer de volgende opdracht in:

    ```hbase
    DROP TABLE Company;
    ```

10. Gebruik de SQLLine-opdracht `!quit` om SQLLine af te sluiten. Voer de volgende opdracht in:

    ```sqlline
    !quit
    ```

## <a name="clean-up-resources"></a>Resources opschonen

Nadat u de quickstart hebt voltooid, kunt u het cluster verwijderen. Met HDInsight worden uw gegevens opgeslagen in Azure Storage zodat u een cluster veilig kunt verwijderen wanneer deze niet wordt gebruikt. Voor een HDInsight-cluster worden ook kosten in rekening gebracht, zelfs wanneer het niet wordt gebruikt. Aangezien de kosten voor het cluster vaak zoveel hoger zijn dan de kosten voor opslag, is het financieel gezien logischer clusters te verwijderen wanneer ze niet worden gebruikt.

Als u een cluster wilt verwijderen, raadpleegt u [HDInsight-cluster verwijderen met behulp van uw browser, PowerShell of de Azure CLI](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Volgende stappen

In deze quickstart leert u hoe u Apache Phoenix moet gebruiken om HBase-query's uit te voeren in Azure HDInsight. Als u zich verder wilt verdiepen in Apache Phoenix, raadpleegt u het volgende artikel.

> [!div class="nextstepaction"]
> [Apache Phoenix in HDInsight](../hdinsight-phoenix-in-hdinsight.md)
