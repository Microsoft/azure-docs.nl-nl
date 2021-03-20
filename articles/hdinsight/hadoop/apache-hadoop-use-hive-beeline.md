---
title: Apache Beeline gebruiken met Apache Hive-Azure HDInsight
description: Meer informatie over het gebruik van de Beeline-client om Hive-query's uit te voeren met Hadoop op HDInsight. Beeline is een hulp programma voor het werken met HiveServer2 over JDBC.
ms.service: hdinsight
ms.topic: how-to
ms.date: 10/28/2020
ms.custom: contperf-fy21q1, contperf-fy21q2
ms.openlocfilehash: e8b7478ba64da0f99a9b7a710222ff2953795adf
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98943192"
---
# <a name="use-the-apache-beeline-client-with-apache-hive"></a>De Apache Beeline-client gebruiken met Apache Hive

In dit artikel wordt beschreven hoe u de opdracht regel [Apache Beeline](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline–NewCommandLineShell) -client gebruikt om Apache Hive query's te maken en uit te voeren via een SSH-verbinding.

## <a name="background"></a>Achtergrond

Beeline is een Hive-client die is opgenomen op de hoofd knooppunten van uw HDInsight-cluster. Zie [verbinding maken met of installeer Apache Beeline](connect-install-beeline.md)om verbinding te maken met de Beeline-client die op uw HDInsight-cluster is geïnstalleerd of Beeline lokaal te installeren. Beeline maakt gebruik van JDBC om verbinding te maken met HiveServer2, een service die wordt gehost op uw HDInsight-cluster. U kunt Beeline ook gebruiken voor toegang tot de component op HDInsight op afstand via internet. De volgende voor beelden bieden de meest voorkomende verbindings reeksen die worden gebruikt om verbinding te maken met HDInsight vanuit Beeline.

## <a name="prerequisites-for-examples"></a>Vereisten voor voor beelden

* Een Hadoop-cluster in HDInsight. Zie aan de [slag met HDInsight op Linux](./apache-hadoop-linux-tutorial-get-started.md).

* Let op het URI-schema voor de primaire opslag van uw cluster. Bijvoorbeeld  `wasb://` voor Azure Storage, `abfs://` voor Azure data Lake Storage Gen2 of `adl://` voor Azure data Lake Storage gen1. Als beveiligde overdracht is ingeschakeld voor Azure Storage, is de URI `wasbs://` . Zie [beveiligde overdracht](../../storage/common/storage-require-secure-transfer.md)voor meer informatie.

* Een SSH-client. Zie voor meer informatie [Verbinding maken met HDInsight (Apache Hadoop) via SSH](../hdinsight-hadoop-linux-use-ssh-unix.md). Bij de meeste stappen in dit document wordt ervan uitgegaan dat u Beeline gebruikt vanuit een SSH-sessie naar het cluster. U kunt ook een lokale Beeline-client gebruiken, maar deze stappen zijn niet in dit artikel opgenomen.

## <a name="run-a-hive-query"></a>Een Hive-query uitvoeren

Dit voor beeld is gebaseerd op het gebruik van de Beeline-client van een SSH-verbinding.

1. Open een SSH-verbinding met het cluster met de onderstaande code. Vervang `sshuser` door de SSH-gebruiker voor uw cluster en `CLUSTERNAME` door de naam van het cluster. Wanneer u hierom wordt gevraagd, voert u het wacht woord voor het SSH-gebruikers account in.

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Maak verbinding met HiveServer2 met uw Beeline-client vanuit uw open SSH-sessie door de volgende opdracht in te voeren:

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
    ```

3. Beeline-opdrachten beginnen met een `!` teken, bijvoorbeeld `!help` Help weer gegeven. `!`Voor sommige opdrachten kan echter wel worden wegge laten. `help`Werkt bijvoorbeeld ook.

    `!sql`Dit wordt gebruikt om HiveQL-instructies uit te voeren. HiveQL wordt echter vaak gebruikt, zodat u de voor gaande kunt weglaten `!sql` . De volgende twee instructies zijn gelijk:

    ```hiveql
    !sql show tables;
    show tables;
    ```

    Op een nieuw cluster wordt slechts één tabel weer gegeven: **hivesampletable**.

4. Gebruik de volgende opdracht om het schema voor de hivesampletable weer te geven:

    ```hiveql
    describe hivesampletable;
    ```

    Met deze opdracht wordt de volgende informatie geretourneerd:

    ```output
    +-----------------------+------------+----------+--+
    |       col_name        | data_type  | comment  |
    +-----------------------+------------+----------+--+
    | clientid              | string     |          |
    | querytime             | string     |          |
    | market                | string     |          |
    | deviceplatform        | string     |          |
    | devicemake            | string     |          |
    | devicemodel           | string     |          |
    | state                 | string     |          |
    | country               | string     |          |
    | querydwelltime        | double     |          |
    | sessionid             | bigint     |          |
    | sessionpagevieworder  | bigint     |          |
    +-----------------------+------------+----------+--+
    ```

    In deze informatie worden de kolommen in de tabel beschreven.

5. Voer de volgende instructies in om een tabel met de naam **log4jLogs** te maken met behulp van voorbeeld gegevens die zijn opgegeven met het HDInsight-cluster: (indien nodig herzien op basis van uw URI-schema).

    ```hiveql
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs (
        t1 string,
        t2 string,
        t3 string,
        t4 string,
        t5 string,
        t6 string,
        t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION 'wasbs:///example/data/';
    SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs
        WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log'
        GROUP BY t4;
    ```

    Deze instructies doen de volgende acties:

    |Instructie |Beschrijving |
    |---|---|
    |DROP TABLE|Als de tabel bestaat, wordt deze verwijderd.|
    |CREATE EXTERNAL TABLE|Hiermee maakt u een **externe** tabel in Hive. Externe tabellen slaan de tabel definitie in Hive alleen op. De gegevens blijven op de oorspronkelijke locatie.|
    |RIJ-INDELING|Hoe de gegevens worden ingedeeld. In dit geval worden de velden in elk logboek gescheiden door een spatie.|
    |OPGESLAGEN ALS TEXTFILE-LOCATIE|Waar de gegevens worden opgeslagen en in welke bestands indeling.|
    |SELECT|Hiermee wordt het aantal rijen geselecteerd waarin de kolom **T4** de waarde **[error]** bevat. Deze query retourneert de waarde **3** als er drie rijen met deze waarde zijn.|
    |INPUT__FILE__NAME zoals%. log|Hive probeert het schema toe te passen op alle bestanden in de map. In dit geval bevat de map bestanden die niet overeenkomen met het schema. Om garbage-gegevens in de resultaten te voor komen, geeft deze instructie aan dat de component alleen gegevens moet retour neren van bestanden die eindigen op. log.|

   > [!NOTE]  
   > Externe tabellen moeten worden gebruikt wanneer u verwacht dat de onderliggende gegevens worden bijgewerkt door een externe bron. Bijvoorbeeld een geautomatiseerd proces voor het uploaden van gegevens of een MapReduce-bewerking.
   >
   > Als u een externe tabel verwijdert, worden de gegevens **niet** verwijderd, maar alleen de tabel definitie.

    De uitvoer van deze opdracht is vergelijkbaar met de volgende tekst:

    ```output
    INFO  : Tez session hasn't been created yet. Opening session
    INFO  :

    INFO  : Status: Running (Executing on YARN cluster with App id application_1443698635933_0001)

    INFO  : Map 1: -/-      Reducer 2: 0/1
    INFO  : Map 1: 0/1      Reducer 2: 0/1
    INFO  : Map 1: 0/1      Reducer 2: 0/1
    INFO  : Map 1: 0/1      Reducer 2: 0/1
    INFO  : Map 1: 0/1      Reducer 2: 0/1
    INFO  : Map 1: 0(+1)/1  Reducer 2: 0/1
    INFO  : Map 1: 0(+1)/1  Reducer 2: 0/1
    INFO  : Map 1: 1/1      Reducer 2: 0/1
    INFO  : Map 1: 1/1      Reducer 2: 0(+1)/1
    INFO  : Map 1: 1/1      Reducer 2: 1/1
    +----------+--------+--+
    |   sev    | count  |
    +----------+--------+--+
    | [ERROR]  | 3      |
    +----------+--------+--+
    1 row selected (47.351 seconds)
    ```

6. Beeline afsluiten:

    ```bash
    !exit
    ```

## <a name="run-a-hiveql-file"></a>Een HiveQL-bestand uitvoeren

Dit voor beeld is een voortzetting van het vorige voor beeld. Gebruik de volgende stappen om een bestand te maken en voer het vervolgens uit met Beeline.

1. Gebruik de volgende opdracht om een bestand met de naam **query. HQL** te maken:

    ```bash
    nano query.hql
    ```

1. Gebruik de volgende tekst als de inhoud van het bestand. Met deze query maakt u een nieuwe interne tabel met de naam **errorLogs**:

    ```hiveql
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
    ```

    Deze instructies doen de volgende acties:

    |Instructie |Beschrijving |
    |---|---|
    |CREATE TABLE ALS DEZE NIET BESTAAT|Als de tabel nog niet bestaat, wordt deze gemaakt. Omdat het **externe** tref woord niet wordt gebruikt, maakt deze instructie een interne tabel. Interne tabellen worden opgeslagen in het Hive-Data Warehouse en worden volledig beheerd door Hive.|
    |OPGESLAGEN ALS ORC|Hiermee worden de gegevens opgeslagen in de ORC-indeling (Optimized Row in kolommen). De ORC-indeling is een zeer geoptimaliseerde en efficiënte indeling voor het opslaan van Hive-gegevens.|
    |OVERSCHRIJVEN INVOEGEN... UITGESCHAKELD|Hiermee worden rijen uit de **log4jLogs** -tabel met **[error]** geselecteerd en worden de gegevens vervolgens ingevoegd in de tabel **errorLogs** .|

    > [!NOTE]  
    > In tegens telling tot externe tabellen, verwijdert het verwijderen van een interne tabel ook de onderliggende gegevens.

1. Als u het bestand wilt opslaan, gebruikt u **CTRL** + **X** en voert u vervolgens **Y** en tenslotte **Enter**.

1. Gebruik het volgende om het bestand uit te voeren met beeline:

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http' -i query.hql
    ```

    > [!NOTE]  
    > De `-i` para meter start Beeline en voert de instructies in het `query.hql` bestand uit. Zodra de query is voltooid, ontvangt u de `jdbc:hive2://headnodehost:10001/>` prompt. U kunt ook een bestand uitvoeren met de `-f` para meter, waardoor Beeline wordt afgesloten nadat de query is voltooid.

1. Als u wilt controleren of de **errorLogs** -tabel is gemaakt, gebruikt u de volgende instructie om alle rijen uit **errorLogs** te retour neren:

    ```hiveql
    SELECT * from errorLogs;
    ```

    Er moeten drie rijen met gegevens worden geretourneerd, die allemaal **[error]** bevatten in kolom T4:

    ```output
    +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
    | errorlogs.t1  | errorlogs.t2  | errorlogs.t3  | errorlogs.t4  | errorlogs.t5  | errorlogs.t6  | errorlogs.t7  |
    +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
    | 2012-02-03    | 18:35:34      | SampleClass0  | [ERROR]       | incorrect     | id            |               |
    | 2012-02-03    | 18:55:54      | SampleClass1  | [ERROR]       | incorrect     | id            |               |
    | 2012-02-03    | 19:25:27      | SampleClass4  | [ERROR]       | incorrect     | id            |               |
    +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
    3 rows selected (0.813 seconds)
    ```

## <a name="next-steps"></a>Volgende stappen

* Zie [Apache Hive gebruiken met Apache Hadoop in hdinsight](hdinsight-use-hive.md) voor meer algemene informatie over Hive in hdinsight

* Zie [MapReduce gebruiken met Apache Hadoop op hdinsight](hdinsight-use-mapreduce.md) voor meer informatie over andere manieren waarop u met Hadoop in hdinsight kunt werken.
