---
title: 'Snelstart: Apache Hive in Azure HDInsight met Apache Zeppelin'
description: In deze quickstart leert u hoe u Apache Zeppelin gebruikt om Apache Hive-query's uit te voeren.
ms.service: hdinsight
ms.topic: quickstart
ms.custom: hdinsightactive
ms.date: 12/03/2019
ms.openlocfilehash: 1199fee82532eacf1f0ef41b877b970d7d9f2cf5
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98941064"
---
# <a name="quickstart-execute-apache-hive-queries-in-azure-hdinsight-with-apache-zeppelin"></a>Snelstart: Apache Hive-query’s uitvoeren in Azure HDInsight met Apache Zeppelin

In deze quickstart leert u hoe u Apache Zeppelin gebruikt om [Apache Hive](https://hive.apache.org/)-query's uit te voeren in Azure HDInsight. Interactive Query-clusters in HDInsight bevatten [Apache Zeppelin](https://zeppelin.apache.org/)-notebooks waarmee u interactieve Hive-query's kunt uitvoeren.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

Een Interactive Query-cluster in HDInsight. Raadpleeg [Cluster maken](../hadoop/apache-hadoop-linux-tutorial-get-started.md) voor het maken van een HDInsight-cluster.  Zorg ervoor dat u het clustertype **Interactive Query** kiest.

## <a name="create-an-apache-zeppelin-note"></a>Een Apache Zeppelin-notitie maken

1. Vervang `CLUSTERNAME` door de naam van uw cluster in de volgende URL: `https://CLUSTERNAME.azurehdinsight.net/zeppelin`. Voer vervolgens de URL in een webbrowser in.

2. Voer uw gebruikersnaam en wachtwoord in om u aan te melden bij het cluster. Op de Zeppelin-pagina kunt u een nieuwe notitie maken of bestaande notities openen. **HiveSample** bevat enkele voorbeelden van Hive-query's.  

    ![Zeppelin in Interactive Query in HDInsight](./media/hdinsight-connect-hive-zeppelin/hdinsight-hive-zeppelin.png)

3. Selecteer **Create new note** (Nieuwe notitie maken).

4. Typ of selecteer in het dialoogvenster **Create new note** de volgende waarden:

    - Note Name (Naam van notitie): Voer een naam voor de notitie in.
    - Default interpreter (Standaard-interpreter): Selecteer **jdbc** in de vervolgkeuzelijst.

5. Selecteer **Create Note** (Notitie maken).

6. Voer de volgende Hive-query in de codesectie in en druk op **Shift+Enter**:

    ```hive
    %jdbc(hive)
    show tables
    ```

    ![Zeppelin in Interactive Query in HDInsight voert query uit](./media/hdinsight-connect-hive-zeppelin/hdinsight-hive-zeppelin-query.png)

    Met de instructie **%jdbc(hive)** in de eerste regel wordt aangegeven dat de notebook de JDBC-interpreter van Hive moet gebruiken.

    De query retourneert een Hive-tabel met de naam **hivesampletable**.

    Hieronder vindt u twee extra Hive-query's die u kunt uitvoeren op **hivesampletable**:

    ```hive
    %jdbc(hive)
    select * from hivesampletable limit 10

    %jdbc(hive)
    select ${group_name}, count(*) as total_count
    from hivesampletable
    group by ${group_name=market,market|deviceplatform|devicemake}
    limit ${total_count=10}
    ```

    In vergelijking met de traditionele Hive-component, worden de queryresultaten veel sneller weergegeven.

### <a name="additional-examples"></a>Aanvullende voorbeelden

1. Een tabel maken. Voer de volgende code uit in de Zeppelin-notebook:

    ```hql
    %jdbc(hive)
    CREATE EXTERNAL TABLE log4jLogs (
        t1 string,
        t2 string,
        t3 string,
        t4 string,
        t5 string,
        t6 string,
        t7 string)
    ROW FORMAT DELIMITED
    FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE;
    ```

1. Gegevens in de nieuwe tabel laden. Voer de volgende code uit in de Zeppelin-notebook:

    ```hql
    %jdbc(hive)
    LOAD DATA
    INPATH 'wasbs:///example/data/sample.log'
    INTO TABLE log4jLogs;
    ```

1. Eén record invoegen. Voer de volgende code uit in de Zeppelin-notebook:

    ```hql
    %jdbc(hive)
    INSERT INTO TABLE log4jLogs2
    VALUES ('A', 'B', 'C', 'D', 'E', 'F', 'G');
    ```

Raadpleeg de [handleiding voor de Hive-taal](https://cwiki.apache.org/confluence/display/Hive/LanguageManual) voor aanvullende informatie over de syntaxis.

## <a name="clean-up-resources"></a>Resources opschonen

Nadat u de quickstart hebt voltooid, kunt u het cluster verwijderen. Met HDInsight worden uw gegevens opgeslagen in Azure Storage zodat u een cluster veilig kunt verwijderen wanneer deze niet wordt gebruikt. Voor een HDInsight-cluster worden ook kosten in rekening gebracht, zelfs wanneer het niet wordt gebruikt. Aangezien de kosten voor het cluster vaak zoveel hoger zijn dan de kosten voor opslag, is het financieel gezien logischer clusters te verwijderen wanneer ze niet worden gebruikt.

Als u een cluster wilt verwijderen, raadpleegt u [HDInsight-cluster verwijderen met behulp van uw browser, PowerShell of de Azure CLI](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u geleerd hoe u Apache Zeppelin gebruikt om Apache Hive-query's uit te voeren in Azure HDInsight. Meer informatie over Hive-query's vindt u in het volgende artikel. Hierin wordt uitgelegd hoe u query's kunt uitvoeren met Visual Studio.

> [!div class="nextstepaction"]
> [Verbinding maken met Azure HDInsight en Apache Hive-query's uitvoeren met behulp van Data Lake Tools voor Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)
