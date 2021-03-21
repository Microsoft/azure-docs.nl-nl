---
title: "Snelstart: Query's uitvoeren op Apache HBase in Azure HDInsight - HBase Shell"
description: In deze quickstart leert u hoe u Apache HBase Shell gebruikt om Apache HBase-query's uit te voeren.
keywords: hdinsight,hadoop,HBase
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: quickstart
ms.date: 06/12/2019
ms.openlocfilehash: 358e058667cb1750a6fc97dffebcbfb2c6e06f84
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98942927"
---
# <a name="quickstart-query-apache-hbase-in-azure-hdinsight-with-hbase-shell"></a>Snelstart: Query's uitvoeren op Apache HBase in Azure HDInsight met HBase Shell

In deze quickstart leert u hoe u Apache HBase Shell kunt gebruiken om een HBase-tabel te maken, gegevens in te voegen en een query uit te voeren op de tabel.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

* Een Apache HBase-cluster. Raadpleeg [Cluster maken](../hadoop/apache-hadoop-linux-tutorial-get-started.md) voor het maken van een HDInsight-cluster.  Zorg ervoor dat u het clustertype **HBase** kiest.

* Een SSH-client. Zie voor meer informatie [Verbinding maken met HDInsight (Apache Hadoop) via SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="create-a-table-and-manipulate-data"></a>Een tabel maken en gegevens bewerken

Voor de meeste mensen worden de gegevens weergegeven in een tabelindeling:

![Tabelgegevens in HDInsight Apache HBase](./media/query-hbase-with-hbase-shell/hdinsight-hbase-contacts-tabular.png)

In HBase (een implementatie van [Cloud BigTable](https://cloud.google.com/bigtable/)) zien dezelfde gegevens er als volgt uit:

![BigTable-gegevens in HDInsight Apache HBase](./media/query-hbase-with-hbase-shell/hdinsight-hbase-contacts-bigtable.png)

U kunt SSH gebruiken om verbinding te maken met HBase-clusters, en vervolgens Apache HBase Shell gebruiken om HBase-tabellen te maken, gegevens in te voegen en gegevens te doorzoeken.

1. Gebruik de opdracht `ssh` om verbinding te maken met uw HBase-cluster. Bewerk de onderstaande opdracht door `CLUSTERNAME` te vervangen door de naam van uw cluster.Voer vervolgens deze opdracht in:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Gebruik de opdracht `hbase shell` om de interactieve HBase-shell te starten. Voer de volgende opdracht in uw SSH-verbinding in:

    ```bash
    hbase shell
    ```

3. Gebruik de opdracht `create` om een HBase-tabel met twee kolomfamilies te maken. Voer de volgende opdracht in:

    ```hbase
    create 'Contacts', 'Personal', 'Office'
    ```

4. Gebruik de opdracht `list` om alle tabellen in HBase weer te geven. Voer de volgende opdracht in:

    ```hbase
    list
    ```

5. Gebruik de opdracht `put` om in een opgegeven rij in een bepaalde tabel waarden in te voegen voor een opgegeven kolom. Voer de volgende opdracht in:

    ```hbase
    put 'Contacts', '1000', 'Personal:Name', 'John Dole'
    put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
    put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
    put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
    ```

6. Gebruik de opdracht `scan` om de `Contacts`-tabelgegevens te scannen en te retourneren. Voer de volgende opdracht in:

    ```hbase
    scan 'Contacts'
    ```

7. Gebruik de opdracht `get` om de inhoud van een rij op te halen. Voer de volgende opdracht in:

    ```hbase
    get 'Contacts', '1000'
    ```

    U ziet soortgelijke resultaten als bij de opdracht `scan`, omdat er maar één rij is.

8. Gebruik de opdracht `delete` om een celwaarde in een tabel te verwijderen. Voer de volgende opdracht in:

    ```hbase
    delete 'Contacts', '1000', 'Office:Address'
    ```

9. Gebruik de opdracht `disable` om de tabel uit te schakelen. Voer de volgende opdracht in:

    ```hbase
    disable 'Contacts'
    ```

10. Gebruik de opdracht `drop` om een tabel uit HBase neer te zetten. Voer de volgende opdracht in:

    ```hbase
    drop 'Contacts'
    ```

11. Gebruik de opdracht `exit` om de interactieve HBase-shell te stoppen. Voer de volgende opdracht in:

    ```hbase
    exit
    ```

Zie [Inleiding tot het Apache HBase-schemaontwerp](http://0b4af6cdc2f0c5998459-c0245c5c937c5dedcca3f1764ecc9b2f.r43.cf2.rackcdn.com/9353-login1210_khurana.pdf) voor meer informatie over het HBase-tabelschema. Raadpleeg de [Snelzoekgids voor Apache HBase](https://hbase.apache.org/book.html#quickstart) voor meer HBase-opdrachten.

## <a name="clean-up-resources"></a>Resources opschonen

Nadat u de quickstart hebt voltooid, kunt u het cluster verwijderen. Met HDInsight worden uw gegevens opgeslagen in Azure Storage zodat u een cluster veilig kunt verwijderen wanneer deze niet wordt gebruikt. Voor een HDInsight-cluster worden ook kosten in rekening gebracht, zelfs wanneer het niet wordt gebruikt. Aangezien de kosten voor het cluster vaak zoveel hoger zijn dan de kosten voor opslag, is het financieel gezien logischer clusters te verwijderen wanneer ze niet worden gebruikt.

Als u een cluster wilt verwijderen, raadpleegt u [HDInsight-cluster verwijderen met behulp van uw browser, PowerShell of de Azure CLI](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u geleerd hoe u Apache HBase Shell kunt gebruiken om een HBase-tabel te maken, gegevens in te voegen en een query uit te voeren op de tabel. Meer informatie over in HBase opgeslagen gegevens vindt u in het volgende artikel. Hierin wordt uitgelegd hoe u query's kunt uitvoeren met Apache Spark.

> [!div class="nextstepaction"]
> [Apache Spark gebruiken om Apache HBase-gegevens te lezen en schrijven](../hdinsight-using-spark-query-hbase.md)