---
title: Apache Sqoop met Apache Hadoop-Azure HDInsight
description: Meer informatie over het gebruik van Apache Sqoop voor het importeren en exporteren tussen Apache Hadoop op HDInsight en Azure SQL Database.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 11/28/2019
ms.openlocfilehash: 2d0d38dee15817e56c2784981365ea331b6a8459
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98943151"
---
# <a name="use-apache-sqoop-to-import-and-export-data-between-apache-hadoop-on-hdinsight-and-azure-sql-database"></a>Apache Sqoop gebruiken voor het importeren en exporteren van gegevens tussen Apache Hadoop op HDInsight en Azure SQL Database

[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Meer informatie over het gebruik van Apache Sqoop voor het importeren en exporteren van een Apache Hadoop cluster in azure HDInsight en Azure SQL Database of Microsoft SQL Server. De stappen in dit document gebruiken de `sqoop` opdracht rechtstreeks vanuit het hoofd knooppunt van het Hadoop-cluster. U gebruikt SSH om verbinding te maken met het hoofd knooppunt en de opdrachten uit te voeren in dit document. Dit artikel is een voortzetting van het [gebruik van Apache Sqoop met Hadoop in HDInsight](./hdinsight-use-sqoop.md).

## <a name="prerequisites"></a>Vereisten

* Volt ooien van de [instelling test omgeving](./hdinsight-use-sqoop.md#create-cluster-and-sql-database) [voor het gebruik van Apache Sqoop met Hadoop in HDInsight](./hdinsight-use-sqoop.md).

* Een SSH-client. Zie voor meer informatie [Verbinding maken met HDInsight (Apache Hadoop) via SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

* Vertrouwd met Sqoop. Zie [Sqoop User Guide (Engelstalig](https://sqoop.apache.org/docs/1.4.7/SqoopUserGuide.html)) voor meer informatie.

## <a name="set-up"></a>Instellen

1. Gebruik de [ssh-opdracht](../hdinsight-hadoop-linux-use-ssh-unix.md) om verbinding te maken met uw cluster. Bewerk de onderstaande opdracht door CLUSTERNAME te vervangen door de naam van uw cluster. Voer vervolgens deze opdracht in:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Stel variabelen in om gebruiks gemak te gebruiken. Vervang `PASSWORD` , `MYSQLSERVER` , en `MYDATABASE` door de relevante waarden en voer de volgende opdrachten in:

    ```bash
    export password='PASSWORD'
    export sqlserver="MYSQLSERVER"
    export database="MYDATABASE"


    export serverConnect="jdbc:sqlserver://$sqlserver.database.windows.net:1433;user=sqluser;password=$password"
    export serverDbConnect="jdbc:sqlserver://$sqlserver.database.windows.net:1433;user=sqluser;password=$password;database=$database"
    ```

## <a name="sqoop-export"></a>Sqoop exporteren

Van Hive naar SQL.

1. Als u wilt controleren of Sqoop uw data base kan zien, voert u de onderstaande opdracht in voor de open SSH-verbinding. Met deze opdracht wordt een lijst met data bases geretourneerd.

    ```bash
    sqoop list-databases --connect $serverConnect
    ```

1. Voer de volgende opdracht in om een lijst met tabellen voor de opgegeven Data Base weer te geven:

    ```bash
    sqoop list-tables --connect $serverDbConnect
    ```

1. Als u gegevens uit de Hive `hivesampletable` -tabel naar de `mobiledata` tabel in uw Data Base wilt exporteren, voert u de onderstaande opdracht in voor de open SSH-verbinding:

    ```bash
    sqoop export --connect $serverDbConnect \
    -table mobiledata \
    --hcatalog-table hivesampletable
    ```

1. Als u wilt controleren of de gegevens zijn geëxporteerd, gebruikt u de volgende query's van uw SSH-verbinding om de geëxporteerde gegevens weer te geven:

    ```bash
    sqoop eval --connect $serverDbConnect \
    --query "SELECT COUNT(*) from dbo.mobiledata WITH (NOLOCK)"


    sqoop eval --connect $serverDbConnect \
    --query "SELECT TOP(10) * from dbo.mobiledata WITH (NOLOCK)"
    ```

## <a name="sqoop-import"></a>Sqoop importeren

Van SQL naar Azure Storage.

1. Voer de onderstaande opdracht in uw open SSH-verbinding in om gegevens uit de `mobiledata` tabel in SQL te importeren in de `wasbs:///tutorials/usesqoop/importeddata` map op HDInsight. De velden in de gegevens worden gescheiden door een tab en de lijnen worden afgesloten met een nieuw regel teken.

    ```bash
    sqoop import --connect $serverDbConnect \
    --table mobiledata \
    --target-dir 'wasb:///tutorials/usesqoop/importeddata' \
    --fields-terminated-by '\t' \
    --lines-terminated-by '\n' -m 1
    ```

1. U kunt ook een Hive-tabel opgeven:

    ```bash
    sqoop import --connect $serverDbConnect \
    --table mobiledata \
    --target-dir 'wasb:///tutorials/usesqoop/importeddata2' \
    --fields-terminated-by '\t' \
    --lines-terminated-by '\n' \
    --create-hive-table \
    --hive-table mobiledata_imported2 \
    --hive-import -m 1
    ```

1. Zodra het importeren is voltooid, voert u de volgende opdracht in uw open SSH-verbinding in om de gegevens in de nieuwe map weer te geven:

    ```bash
    hadoop fs -tail /tutorials/usesqoop/importeddata/part-m-00000
    ```

1. Gebruik [Beeline](./apache-hadoop-use-hive-beeline.md) om te controleren of de tabel in Hive is gemaakt.

    1. Verbinding maken

        ```bash
        beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
        ```

    1. Voer elke query hieronder een voor een uit en controleer de uitvoer:

        ```hql
        show tables;
        describe mobiledata_imported2;
        SELECT COUNT(*) FROM mobiledata_imported2;
        SELECT * FROM mobiledata_imported2 LIMIT 10;
        ```

    1. Sluit Beeline af met `!exit` .

## <a name="limitations"></a>Beperkingen

* Bulksgewijs exporteren-met HDInsight op basis van Linux biedt de Sqoop-connector die wordt gebruikt om gegevens te exporteren naar SQL geen bulk toevoegingen.

* Batch verwerking: met HDInsight op basis van Linux kunt u met behulp `-batch` van de switch tijdens het invoegen meerdere invoegen gebruiken in plaats van een batch verwerking van de Insert-bewerkingen uit te voeren.

## <a name="important-considerations"></a>Belangrijke overwegingen

* Zowel HDInsight als SQL Server moeten zich op dezelfde Azure Virtual Network.

    Zie voor een voor beeld het [Connect HDInsight to your on-premises netwerk](./../connect-on-premises-network.md) document.

    Zie het document [Hdinsight uitbreiden met azure Virtual Network](../hdinsight-plan-virtual-network-deployment.md) voor meer informatie over het gebruik van hdinsight met een Azure Virtual Network. Zie het document [Virtual Network Overview](../../virtual-network/virtual-networks-overview.md) voor meer informatie over Azure Virtual Network.

* SQL Server moet worden geconfigureerd om SQL-verificatie toe te staan. Zie het document [een verificatie modus kiezen](/sql/relational-databases/security/choose-an-authentication-mode) voor meer informatie.

* Mogelijk moet u SQL Server configureren om externe verbindingen te accepteren. Zie [problemen met het maken van verbinding met het document van de SQL Server Data Base-Engine oplossen](https://social.technet.microsoft.com/wiki/contents/articles/2102.how-to-troubleshoot-connecting-to-the-sql-server-database-engine.aspx) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

U hebt nu geleerd hoe u Sqoop kunt gebruiken. Raadpleeg voor meer informatie:

* [Apache Oozie gebruiken met HDInsight](../hdinsight-use-oozie-linux-mac.md): gebruik de actie Sqoop in een Oozie-werk stroom.
* [Analyseer gegevens van de vlucht vertraging met HDInsight](../interactive-query/interactive-query-tutorial-analyze-flight-data.md): gebruik interactieve query's om vlucht vertragings gegevens te analyseren en gebruik vervolgens Sqoop om gegevens te exporteren naar een data base in Azure.
* [Gegevens uploaden naar hdinsight](../hdinsight-upload-data.md): andere methoden voor het uploaden van gegevens naar Hdinsight/Azure Blob Storage zoeken.