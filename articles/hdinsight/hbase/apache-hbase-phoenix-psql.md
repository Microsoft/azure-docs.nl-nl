---
title: Bulksgewijs laden in Apache Phoenix met behulp van psql-Azure HDInsight
description: Gebruik het psql-hulp programma voor het laden van gegevens voor bulksgewijs laden in Apache Phoenix tabellen in azure HDInsight
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/17/2019
ms.openlocfilehash: d1ddf69acda442212500200eb6dc326dcbcb3c1b
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104867794"
---
# <a name="bulk-load-data-into-apache-phoenix-using-psql"></a>Bulksgewijs gegevens laden in Apache Phoenix met psql

[Apache Phoenix](https://phoenix.apache.org/) is een open-source, enorm parallelle relationele data base gebouwd op [Apache HBase](../hbase/apache-hbase-overview.md). Phoenix biedt SQL-achtige query's via HBase. Phoenix maakt gebruik van JDBC-Stuur Programma's om gebruikers in staat te stellen SQL-tabellen, indexen, weer gaven en reeksen en upsert te maken, verwijderen en wijzigen. Phoenix gebruikt noSQL systeem eigen compilatie in plaats van MapReduce te gebruiken voor het compileren van query's om toepassingen met lage latentie op HBase te maken. Phoenix voegt coprocessoren toe ter ondersteuning van het uitvoeren van de door de client geleverde code in de adres ruimte van de server, waarbij de code wordt uitgevoerd die zich in de gegevens bevindt. Hiermee wordt de client/server-gegevens overdracht geminimaliseerd.  Als u wilt werken met gegevens met behulp van Phoenix in HDInsight, maakt u eerst tabellen en laadt u de gegevens hierin.

## <a name="bulk-loading-with-apache-phoenix"></a>Bulksgewijs laden met Apache Phoenix

Er zijn meerdere manieren om gegevens op te halen in HBase, met inbegrip van client-Api's, een MapReduce-taak met TableOutputFormat of het hand matig invoeren van de gegevens via de HBase-shell. Phoenix voorziet in twee methoden voor het laden van CSV-gegevens in Phoenix-tabellen: een client laad programma met de naam `psql` en een MapReduce-hulp programma voor bulksgewijs laden.

Het `psql` hulp programma heeft één thread en is het meest geschikt voor het laden van mega bytes of gigabytes aan gegevens. Alle CSV-bestanden die moeten worden geladen, moeten de bestands extensie. csv hebben.  U kunt ook SQL-script bestanden opgeven op de `psql` opdracht regel met de bestands extensie '. SQL '.

Bulksgewijs laden met MapReduce wordt gebruikt voor veel grotere gegevens volumes, doorgaans in productie scenario's, omdat MapReduce meerdere threads gebruikt.

Controleer voordat u begint met het laden van gegevens of Phoenix is ingeschakeld en of de instellingen van de querytime-out naar verwachting zijn.  Open uw HDInsight cluster [Apache Ambari](https://ambari.apache.org/) -dash board, selecteer HBase en klik vervolgens op het tabblad Configuratie.  Schuif omlaag om te controleren of Apache Phoenix is ingesteld op `enabled` zoals weer gegeven:

:::image type="content" source="./media/apache-hbase-phoenix-psql/apache-ambari-phoenix.png" alt-text="Instellingen van HDInsight-cluster Apache Phoenix" border="true":::

### <a name="use-psql-to-bulk-load-tables"></a>Gebruiken `psql` om tabellen bulksgewijs te laden

1. Maak een bestand `createCustomersTable.sql` met de naam en kopieer de onderstaande code naar het bestand. Sla het bestand vervolgens op en sluit het.

    ```sql
    CREATE TABLE Customers (
        ID varchar NOT NULL PRIMARY KEY,
        Name varchar,
        Income decimal,
        Age INTEGER,
        Country varchar);
    ```

1. Maak een bestand `listCustomers.sql` met de naam en kopieer de onderstaande code naar het bestand. Sla het bestand vervolgens op en sluit het.

    ```sql
    SELECT * from Customers;
    ```

1. Maak een bestand `customers.csv` met de naam en kopieer de onderstaande code naar het bestand. Sla het bestand vervolgens op en sluit het.

    ```txt
    1,Samantha,260000.0,18,US
    2,Sam,10000.5,56,US
    3,Anton,550150.0,42,Norway
    ```

1. Maak een bestand `customers2.csv` met de naam en kopieer de onderstaande code naar het bestand. Sla het bestand vervolgens op en sluit het.

    ```txt
    4,Nicolle,180000.0,22,US
    5,Kate,210000.5,24,Canada
    6,Ben,45000.0,32,Poland
    ```

1. Open een opdracht prompt en wijzig de map naar de locatie van de zojuist gemaakte bestanden. Vervang CLUSTERNAME hieronder door de werkelijke naam van uw HBase-cluster. Voer vervolgens de code uit om de bestanden te uploaden naar de hoofd knooppunt van uw cluster:

    ```cmd
    scp customers.csv customers2.csv createCustomersTable.sql listCustomers.sql sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/tmp
    ```

1. Gebruik de [ssh-opdracht](../hdinsight-hadoop-linux-use-ssh-unix.md) om verbinding te maken met uw cluster. Bewerk de onderstaande opdracht door CLUSTERNAME te vervangen door de naam van uw cluster. Voer vervolgens deze opdracht in:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Ga vanuit uw SSH-sessie naar de locatie van het hulp programma **psql** . Voer de onderstaande opdracht uit:

    ```bash
    cd /usr/hdp/current/phoenix-client/bin
    ```

1. Bulksgewijs laden van de gegevens. De onderstaande code maakt beide de tabel **klanten** en uploadt de gegevens.

    ```bash
    python psql.py /tmp/createCustomersTable.sql /tmp/customers.csv
    ```

    Nadat de `psql` bewerking is voltooid, ziet u een bericht als volgt:

    ```output
    csv columns from database.
    CSV Upsert complete. 3 rows upserted
    Time: 0.081 sec(s)
    ```

1. U kunt blijven gebruiken `psql` om de inhoud van de tabel klanten weer te geven. Voer de volgende code uit:

    ```bash
    python psql.py /tmp/listCustomers.sql
    ```

    U kunt ook [HBase-shell](./query-hbase-with-hbase-shell.md)of [Apache Zeppelin](./apache-hbase-phoenix-zeppelin.md) gebruiken om de gegevens op te vragen.

1. Upload aanvullende gegevens. Nu de tabel al bestaat, geeft de opdracht de tabel op. Voer de onderstaande opdracht uit:

    ```bash
    python psql.py -t CUSTOMERS /tmp/customers2.csv
    ```

## <a name="use-mapreduce-to-bulk-load-tables"></a>MapReduce gebruiken voor het bulksgewijs laden van tabellen

Gebruik het hulp programma MapReduce Load voor meer doorvoer belasting over het cluster. Met deze loader worden eerst alle gegevens naar HFiles geconverteerd en wordt vervolgens de gemaakte HFiles naar HBase.

1. Deze sectie gaat verder met de SSH-sessie en de objecten die u eerder hebt gemaakt. Maak de tabel **klanten** en **customers.csv** bestand naar wens aan de hand van de bovenstaande stappen. Indien nodig moet u de SSH-verbinding opnieuw tot stand brengen.

1. De inhoud van de tabel **klanten** afkappen. Voer de onderstaande opdrachten uit vanuit uw open SSH-sessie:

    ```bash
    hbase shell
    truncate 'CUSTOMERS'
    exit
    ```

1. Kopieer het `customers.csv` bestand van uw hoofd knooppunt naar Azure Storage.

    ```bash
    hdfs dfs -put /tmp/customers.csv wasbs:///tmp/customers.csv
    ```

1. Ga naar de map voor de uitvoering van de opdracht voor het bulksgewijs laden van MapReduce:

    ```bash
    cd /usr/hdp/current/phoenix-client
    ```

1. De CSV MapReduce loader starten met behulp van de `hadoop` opdracht met de Breda-client jar:

    ```bash
    HADOOP_CLASSPATH=/usr/hdp/current/hbase-client/lib/hbase-protocol.jar:/etc/hbase/conf hadoop jar phoenix-client.jar org.apache.phoenix.mapreduce.CsvBulkLoadTool --table Customers --input /tmp/customers.csv
    ```

    Zodra het uploaden is voltooid, ziet u een bericht als volgt:

    ```output
    19/12/18 18:30:57 INFO client.ConnectionManager$HConnectionImplementation: Closing master protocol: MasterService
    19/12/18 18:30:57 INFO client.ConnectionManager$HConnectionImplementation: Closing zookeeper sessionid=0x26f15dcceff02c3
    19/12/18 18:30:57 INFO zookeeper.ZooKeeper: Session: 0x26f15dcceff02c3 closed
    19/12/18 18:30:57 INFO zookeeper.ClientCnxn: EventThread shut down
    19/12/18 18:30:57 INFO mapreduce.AbstractBulkLoadTool: Incremental load complete for table=CUSTOMERS
    19/12/18 18:30:57 INFO mapreduce.AbstractBulkLoadTool: Removing output directory /tmp/50254426-aba6-400e-88eb-8086d3dddb6
    ```

1. Als u MapReduce met Azure Data Lake Storage wilt gebruiken, zoekt u de Data Lake Storage hoofdmap. Dit is de `hbase.rootdir` waarde in `hbase-site.xml` . In de volgende opdracht is de hoofdmap van Data Lake Storage `adl://hdinsightconf1.azuredatalakestore.net:443/hbase1` . In deze opdracht geeft u de Data Lake Storage invoer-en uitvoer mappen op als para meters:

    ```bash
    cd /usr/hdp/current/phoenix-client

    $HADOOP_CLASSPATH=$(hbase mapredcp):/etc/hbase/conf hadoop jar /usr/hdp/2.4.2.0-258/phoenix/phoenix-4.4.0.2.4.2.0-258-client.jar

    org.apache.phoenix.mapreduce.CsvBulkLoadTool --table Customers --input adl://hdinsightconf1.azuredatalakestore.net:443/hbase1/data/hbase/temp/input/customers.csv –zookeeper ZookeeperQuorum:2181:/hbase-unsecure --output  adl://hdinsightconf1.azuredatalakestore.net:443/hbase1/data/hbase/output1
    ```

1. Als u de gegevens wilt opvragen en weer geven, kunt u **psql** gebruiken zoals eerder is beschreven. U kunt ook [HBase-shell](./query-hbase-with-hbase-shell.md)of [Apache Zeppelin](./apache-hbase-phoenix-zeppelin.md)gebruiken.

## <a name="recommendations"></a>Aanbevelingen

* Gebruik hetzelfde opslag medium voor zowel de invoer-als uitvoer mappen, Azure Storage (WASB) of Azure Data Lake Storage (ADL). Als u gegevens wilt overdragen van Azure Storage naar Data Lake Storage, kunt u de `distcp` opdracht gebruiken:

    ```bash
    hadoop distcp wasb://@.blob.core.windows.net/example/data/gutenberg adl://.azuredatalakestore.net:443/myfolder
    ```

* Gebruik werk knooppunten met een grotere grootte. De kaart processen van de MapReduce massa kopie produceren grote hoeveel heden tijdelijke uitvoer waarmee de beschik bare niet-DFS-ruimte wordt gevuld. Gebruik meer en grotere werk knooppunten voor een grote hoeveelheid belasting voor bulksgewijs laden. Het aantal worker-knoop punten dat u toewijst aan uw cluster is rechtstreeks van invloed op de verwerkings snelheid.

* Splits de invoer bestanden in segmenten van ongeveer 10 GB. Bulksgewijs laden is een tijdrovende bewerking, waardoor het splitsen van uw invoer bestanden in meerdere segmenten resulteert in betere prestaties.

* Vermijd de gebieden van de regio server. Als uw gestaag toeneemt, kan HBase sequentiële Schrijf bewerkingen de regio server hotspotting veroorzaken. Door het *zouten* van de rij-sleutel worden opeenvolgende schrijf bewerkingen verminderd. Phoenix biedt een manier om de rij-sleutel transparant te zouten met een zouten byte voor een bepaalde tabel, zoals hieronder wordt verwezen.

## <a name="next-steps"></a>Volgende stappen

* [Bulksgewijs laden van gegevens met Apache Phoenix](https://phoenix.apache.org/bulk_dataload.html)
* [Apache Phoenix gebruiken met Apache HBase-clusters op basis van Linux in HDInsight](../hbase/apache-hbase-query-with-phoenix.md)
* [Gezouteerde tabellen](https://phoenix.apache.org/salted.html)
* [Apache Phoenix grammatica](https://phoenix.apache.org/language/index.html)
