---
title: Operationeel maken a Data Analytics-pijp lijn-Azure
description: Stel een voorbeeld gegevens pijplijn in en voer deze uit die wordt geactiveerd door nieuwe gegevens en die beknopte resultaten oplevert.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/25/2019
ms.openlocfilehash: c81eb092fa59cb890093e1e9acd0511e39b5047b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104864207"
---
# <a name="operationalize-a-data-analytics-pipeline"></a>Een pijplijn voor gegevensanalyse operationeel maken

*Gegevens pijplijnen* met veel gegevens analyse oplossingen. Zoals in de naam wordt voorgesteld, neemt een gegevens pijplijn in onbewerkte gegevens, reinigt en vormt deze naar wens en voert doorgaans berekeningen of aggregaties uit voordat de verwerkte gegevens worden opgeslagen. De verwerkte gegevens worden gebruikt door clients, rapporten of Api's. Een gegevens pijplijn moet herhaal bare resultaten opleveren, hetzij volgens een planning of wanneer ze worden geactiveerd door nieuwe gegevens.

In dit artikel wordt beschreven hoe u uw gegevens pijplijnen kunt operationeel makenen voor Herhaal baarheid, met behulp van Oozie die worden uitgevoerd op HDInsight Hadoop-clusters. In het voorbeeld scenario wordt u begeleid bij een gegevens pijplijn die de gegevens reeks van de vliegtuig vlucht voorbereidt en verwerkt.

In het volgende scenario zijn de invoer gegevens een plat bestand met een batch vlucht gegevens gedurende één maand. Deze vlucht gegevens omvatten informatie zoals de oorsprong en bestemming lucht haven, de mijlen, de vertrek-en aankomst tijden, enzovoort. Het doel van deze pijp lijn is de dagelijkse vliegtuig prestaties samen te vatten, waarbij elke luchtvaart maatschappij één rij voor elke dag heeft, met de gemiddelde vertrek-en aankomst vertraging in minuten, en de totale hoeveelheid kilo meters die dag.

| YEAR | MONTH | DAY_OF_MONTH | MAATSCHAPPIJ |AVG_DEP_DELAY | AVG_ARR_DELAY |TOTAL_DISTANCE |
| --- | --- | --- | --- | --- | --- | --- |
| 2017 | 1 | 3 | A | 10,142229 | 7,862926 | 2644539 |
| 2017 | 1 | 3 | AS | 9,435449 | 5,482143 | 572289 |
| 2017 | 1 | 3 | DISTRIBUTIE | 6,935409 | -2,1893024 | 1909696 |

De voorbeeld pijplijn wacht totdat er een nieuwe vlucht gegevens van de periode arriveert en slaat vervolgens die gedetailleerde vlucht gegevens op in uw Apache Hive data warehouse voor lange termijn analyses. De pijp lijn maakt ook een veel kleinere gegevensset die alleen de dagelijkse vlucht gegevens samenvat. Deze dagelijkse vlucht samenvattings gegevens worden verzonden naar een SQL Database om rapporten te bieden, zoals voor een website.

In het volgende diagram ziet u de voorbeeld pijplijn.

:::image type="content" source="./media/hdinsight-operationalize-data-pipeline/flight-pipeline-overview.png" alt-text="Overzicht van gegevens pijplijn voor HDI Flight" border="false":::

## <a name="apache-oozie-solution-overview"></a>Overzicht van Apache Oozie-oplossing

Deze pijp lijn gebruikt Apache Oozie dat wordt uitgevoerd op een HDInsight Hadoop-cluster.

Oozie beschrijft de pijp lijnen in termen van *acties*, *werk stromen* en *coördinatoren*. Acties bepalen het werkelijke werk dat moet worden uitgevoerd, zoals het uitvoeren van een Hive-query. Met werk stromen wordt de reeks acties gedefinieerd. Coördinatoren definiëren het schema voor wanneer de werk stroom wordt uitgevoerd. Coördinatoren kunnen ook wachten op de beschik baarheid van nieuwe gegevens voordat een exemplaar van de werk stroom wordt gestart.

Het volgende diagram toont het ontwerp op hoog niveau van dit voor beeld Oozie-pijp lijn.

:::image type="content" source="./media/hdinsight-operationalize-data-pipeline/pipeline-overview-oozie.png" alt-text="Voorbeeld gegevens pijplijn van Oozie Flight" border="false":::

## <a name="provision-azure-resources"></a>Azure-resources inrichten

Voor deze pijp lijn is een Azure SQL Database en een HDInsight Hadoop-cluster op dezelfde locatie vereist. In de Azure SQL Database worden de samenvattings gegevens opgeslagen die zijn geproduceerd door de pijp lijn en de Oozie-meta gegevens opslag.

### <a name="provision-azure-sql-database"></a>Azure SQL Database inrichten

1. Maak een Azure SQL Database. Zie [een Azure SQL database maken in de Azure Portal](../azure-sql/database/single-database-create-quickstart.md).

1. Om ervoor te zorgen dat uw HDInsight-cluster toegang heeft tot de verbonden Azure SQL Database, configureert u Azure SQL Database firewall regels om Azure-Services en-bronnen toegang te geven tot de server. U kunt deze optie inschakelen in de Azure Portal door **Server firewall instellen** **te selecteren en onder andere** Azure- **Services en-bronnen toestaan toegang te krijgen tot deze server** voor Azure SQL database. Raadpleeg [IP-firewallregels maken en beheren](../azure-sql/database/firewall-configure.md#use-the-azure-portal-to-manage-server-level-ip-firewall-rules) voor meer informatie.

1. Gebruik de [query-editor](../azure-sql/database/single-database-create-quickstart.md#query-the-database) om de volgende SQL-instructies uit te voeren om de tabel te maken `dailyflights` waarin de samenvattings gegevens van elke uitvoering van de pijp lijn worden opgeslagen.

    ```sql
    CREATE TABLE dailyflights
    (
        YEAR INT,
        MONTH INT,
        DAY_OF_MONTH INT,
        CARRIER CHAR(2),
        AVG_DEP_DELAY FLOAT,
        AVG_ARR_DELAY FLOAT,
        TOTAL_DISTANCE FLOAT
    )
    GO

    CREATE CLUSTERED INDEX dailyflights_clustered_index on dailyflights(YEAR,MONTH,DAY_OF_MONTH,CARRIER)
    GO
    ```

Uw Azure SQL Database is nu gereed.

### <a name="provision-an-apache-hadoop-cluster"></a>Een Apache Hadoop cluster inrichten

Een Apache Hadoop cluster maken met een aangepaste meta Store. Zorg ervoor dat u tijdens het maken van het cluster vanuit de portal op het tabblad **opslag** uw SQL database selecteert onder instellingen voor de **META Store**. Zie voor meer informatie over het selecteren van een meta Store [een aangepaste meta Store selecteren tijdens het maken](./hdinsight-use-external-metadata-stores.md#select-a-custom-metastore-during-cluster-creation)van het cluster. Zie [aan de slag met HDInsight op Linux](hadoop/apache-hadoop-linux-tutorial-get-started.md)voor meer informatie over het maken van een cluster.

## <a name="verify-ssh-tunneling-set-up"></a>Instellen van SSH-Tunneling controleren

Als u de Oozie-webconsole wilt gebruiken om de status van uw coördinator en werk stroom exemplaren weer te geven, stelt u een SSH-tunnel in op uw HDInsight-cluster. Zie [SSH-tunnel](hdinsight-linux-ambari-ssh-tunnel.md)voor meer informatie.

> [!NOTE]  
> U kunt Chrome ook gebruiken met de [foxy-proxy](https://getfoxyproxy.org/) extensie om te bladeren door de webbronnen van uw cluster in de SSH-tunnel. Stel deze in op proxy voor alle aanvragen via de host `localhost` op de poort 9876 van de tunnel. Deze benadering is compatibel met het Windows-subsysteem voor Linux, ook wel bekend als bash in Windows 10.

1. Voer de volgende opdracht uit om een SSH-tunnel naar uw cluster te openen, waarbij `CLUSTERNAME` de naam van uw cluster is:

    ```cmd
    ssh -C2qTnNf -D 9876 sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Controleer of de tunnel operationeel is door te navigeren naar Ambari op het hoofd knooppunt door te bladeren naar:

    `http://headnodehost:8080`

1. Om toegang te krijgen tot de **Oozie-webconsole** vanuit Ambari, gaat u naar **Oozie**  >  **snelle koppelingen** > [Active Server] > **Oozie Web UI**.

## <a name="configure-hive"></a>Hive configureren

### <a name="upload-data"></a>Gegevens uploaden

1. Een voor beeld van een CSV-bestand met vlucht gegevens voor één maand downloaden. Down load het ZIP-bestand `2017-01-FlightData.zip` uit de [HDInsight github-opslag plaats](https://github.com/hdinsight/hdinsight-dev-guide) en pak het uit naar het CSV-bestand `2017-01-FlightData.csv` .

1. Kopieer dit CSV-bestand naar het Azure Storage-account dat aan uw HDInsight-cluster is gekoppeld en plaats het in de `/example/data/flights` map.

    1. Gebruik SCP om de bestanden van uw lokale computer te kopiëren naar de lokale opslag van het hoofd knooppunt van het HDInsight-cluster.

        ```cmd
        scp ./2017-01-FlightData.csv sshuser@CLUSTERNAME-ssh.azurehdinsight.net:2017-01-FlightData.csv
        ```

    1. Gebruik de [ssh-opdracht](./hdinsight-hadoop-linux-use-ssh-unix.md) om verbinding te maken met uw cluster. Bewerk de onderstaande opdracht door `CLUSTERNAME` te vervangen door de naam van uw cluster.Voer vervolgens deze opdracht in:

        ```cmd
        ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
        ```

    1. Gebruik vanuit SSH-sessie de HDFS-opdracht om het bestand te kopiëren van de lokale opslag van uw hoofd knooppunt naar Azure Storage.

        ```bash
        hadoop fs -mkdir /example/data/flights
        hdfs dfs -put ./2017-01-FlightData.csv /example/data/flights/2017-01-FlightData.csv
        ```

### <a name="create-tables"></a>Tabellen maken

De voorbeeld gegevens zijn nu beschikbaar. Voor de pijp lijn zijn echter twee Hive-tabellen vereist voor de verwerking, één voor de inkomende gegevens ( `rawFlights` ) en één voor de samenvattings gegevens ( `flights` ). Maak deze tabellen in Ambari als volgt.

1. Meld u aan bij Ambari door naar te navigeren `http://headnodehost:8080` .

2. Selecteer **Hive** in de lijst met Services.

    :::image type="content" source="./media/hdinsight-operationalize-data-pipeline/hdi-ambari-services-hive.png" alt-text="Component Apache Ambari Services-lijst selectie":::

3. Selecteer **Ga naar weer gave** naast het label component weergave 2,0.

    :::image type="content" source="./media/hdinsight-operationalize-data-pipeline/hdi-ambari-services-hive-summary.png" alt-text="Overzichts lijst van Ambari-Apache Hive":::

4. Plak in het tekst gebied query de volgende instructies om de tabel te maken `rawFlights` . De `rawFlights` tabel bevat een schema-bij-lezen voor de CSV-bestanden `/example/data/flights` in de map in azure Storage.

    ```sql
    CREATE EXTERNAL TABLE IF NOT EXISTS rawflights (
        YEAR INT,
        MONTH INT,
        DAY_OF_MONTH INT,
        FL_DATE STRING,
        CARRIER STRING,
        FL_NUM STRING,
        ORIGIN STRING,
        DEST STRING,
        DEP_DELAY FLOAT,
        ARR_DELAY FLOAT,
        ACTUAL_ELAPSED_TIME FLOAT,
        DISTANCE FLOAT)
    ROW FORMAT SERDE 'org.apache.hadoop.hive.serde2.OpenCSVSerde'
    WITH SERDEPROPERTIES
    (
        "separatorChar" = ",",
        "quoteChar"     = "\""
    )
    LOCATION '/example/data/flights'
    ```

5. Selecteer **uitvoeren** om de tabel te maken.

    :::image type="content" source="./media/hdinsight-operationalize-data-pipeline/hdi-ambari-services-hive-query.png" alt-text="HDI ambari Services-Hive-query":::

6. Als u de `flights` tabel wilt maken, vervangt u de tekst in het tekst gebied query door de volgende instructies. De `flights` tabel is een door een module beheerde tabel waarin de gegevens worden gepartitioneerd per jaar, maand en dag van de maand. Deze tabel bevat alle historische vlucht gegevens, waarbij de laagste granulatie aanwezig is in de bron gegevens van één rij per vlucht.

    ```sql
    SET hive.exec.dynamic.partition.mode=nonstrict;

    CREATE TABLE flights
    (
        FL_DATE STRING,
        CARRIER STRING,
        FL_NUM STRING,
        ORIGIN STRING,
        DEST STRING,
        DEP_DELAY FLOAT,
        ARR_DELAY FLOAT,
        ACTUAL_ELAPSED_TIME FLOAT,
        DISTANCE FLOAT
    )
    PARTITIONED BY (YEAR INT, MONTH INT, DAY_OF_MONTH INT)
    ROW FORMAT SERDE 'org.apache.hadoop.hive.serde2.OpenCSVSerde'
    WITH SERDEPROPERTIES 
    (
        "separatorChar" = ",",
        "quoteChar"     = "\""
    );
    ```

7. Selecteer **uitvoeren** om de tabel te maken.

## <a name="create-the-oozie-workflow"></a>De Oozie-werk stroom maken

In pijp lijnen worden gegevens doorgaans met een bepaald tijds interval verwerkt in batches. In dit geval wordt de vlucht gegevens dagelijks verwerkt door de pijp lijn. Op deze manier kan de invoer CSV-bestanden dagelijks, wekelijks, maandelijks of jaarlijks worden aangeleverd.

De voorbeeld werk stroom verwerkt de vlucht gegevens per dag, in drie belang rijke stappen:

1. Voer een Hive-query uit om de gegevens voor het datum bereik van die dag te extra heren uit het CSV-bron bestand dat wordt vertegenwoordigd door de `rawFlights` tabel en de gegevens in de tabel in te voegen `flights` .
2. Voer een Hive-query uit om dynamisch een faserings tabel in de Hive te maken, die een kopie bevat van de vlucht gegevens, die worden samengestuurd per dag en transporteur.
3. Gebruik Apache Sqoop om alle gegevens van de dagelijkse faserings tabel in de component te kopiëren naar de doel `dailyflights` tabel in Azure SQL database. Sqoop leest de bron rijen van de gegevens achter de Hive-tabel die zich bevindt in Azure Storage en laadt deze in SQL Database met behulp van een JDBC-verbinding.

Deze drie stappen worden gecoördineerd door een Oozie-werk stroom.

1. Maak een bestand met de naam van uw lokale werk station `job.properties` . Gebruik de onderstaande tekst als de begin inhoud voor het bestand.
Werk vervolgens de waarden voor uw specifieke omgeving bij. De tabel onder de tekst bevat een overzicht van de eigenschappen en geeft aan waar u de waarden voor uw eigen omgeving kunt vinden.

    ```text
    nameNode=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net
    jobTracker=[ACTIVERESOURCEMANAGER]:8050
    queueName=default
    oozie.use.system.libpath=true
    appBase=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net/oozie
    oozie.wf.application.path=${appBase}/load_flights_by_day
    hiveScriptLoadPartition=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net/oozie/load_flights_by_day/hive-load-flights-partition.hql
    hiveScriptCreateDailyTable=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net/oozie/load_flights_by_day/hive-create-daily-summary-table.hql
    hiveDailyTableName=dailyflights${year}${month}${day}
    hiveDataFolder=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net/example/data/flights/day/${year}/${month}/${day}
    sqlDatabaseConnectionString="jdbc:sqlserver://[SERVERNAME].database.windows.net;user=[USERNAME];password=[PASSWORD];database=[DATABASENAME]"
    sqlDatabaseTableName=dailyflights
    year=2017
    month=01
    day=03
    ```

    | Eigenschap | Waarde bron |
    | --- | --- |
    | nameNode | Het volledige pad naar de Azure Storage container die aan uw HDInsight-cluster is gekoppeld. |
    | jobTracker | De interne hostnaam naar het hoofd knooppunt van uw actieve cluster. Selecteer op de start pagina van Ambari de optie GARENs in de lijst met Services en kies vervolgens actieve Resource Manager. De hostnaam-URI wordt boven aan de pagina weer gegeven. Voeg poort 8050 toe. |
    | queueName | De naam van de garen wachtrij die wordt gebruikt bij het plannen van de Hive-acties. Als standaard ingesteld laten. |
    | Pad naar oozie.use.system. | Geef waar. |
    | appBase | Het pad naar de submap in Azure Storage waar u de Oozie-werk stroom en ondersteunende bestanden implementeert. |
    | oozie. WF. Application. Path | De locatie van de Oozie-werk stroom `workflow.xml` die moet worden uitgevoerd. |
    | hiveScriptLoadPartition | Het pad in Azure Storage naar het Hive-query bestand `hive-load-flights-partition.hql` . |
    | hiveScriptCreateDailyTable | Het pad in Azure Storage naar het Hive-query bestand `hive-create-daily-summary-table.hql` . |
    | hiveDailyTableName | De dynamisch gegenereerde naam die voor de faserings tabel moet worden gebruikt. |
    | hiveDataFolder | Het pad in Azure Storage naar de gegevens die zijn opgenomen in de faserings tabel. |
    | sqlDatabaseConnectionString | De JDBC-syntaxis connection string uw Azure SQL Database. |
    | sqlDatabaseTableName | De naam van de tabel in Azure SQL Database waarin de samenvattings rijen worden ingevoegd. Laten staan `dailyflights` . |
    | jaar | Het jaar gedeelte van de dag waarvoor vlucht overzichten worden berekend. Laten. |
    | maand | Het maand gedeelte van de dag waarvoor vlucht overzichten worden berekend. Laten. |
    | day | Het onderdeel dag van de maand van de dag waarvoor vlucht overzichten worden berekend. Laten. |

1. Maak een bestand met de naam van uw lokale werk station `hive-load-flights-partition.hql` . Gebruik de onderstaande code als de inhoud van het bestand.

    ```sql
    SET hive.exec.dynamic.partition.mode=nonstrict;

    INSERT OVERWRITE TABLE flights
    PARTITION (YEAR, MONTH, DAY_OF_MONTH)
    SELECT 
          FL_DATE,
          CARRIER,
          FL_NUM,
          ORIGIN,
          DEST,
          DEP_DELAY,
          ARR_DELAY,
          ACTUAL_ELAPSED_TIME,
          DISTANCE,
        YEAR,
          MONTH,
          DAY_OF_MONTH
    FROM rawflights
    WHERE year = ${year} AND month = ${month} AND day_of_month = ${day};
    ```

    Oozie-variabelen gebruiken de syntaxis `${variableName}` . Deze variabelen worden ingesteld in het `job.properties` bestand. Oozie vervangt de werkelijke waarden tijdens runtime.

1. Maak een bestand met de naam van uw lokale werk station `hive-create-daily-summary-table.hql` . Gebruik de onderstaande code als de inhoud van het bestand.

    ```sql
    DROP TABLE ${hiveTableName};
    CREATE EXTERNAL TABLE ${hiveTableName}
    (
        YEAR INT,
          MONTH INT,
          DAY_OF_MONTH INT,
          CARRIER STRING,
          AVG_DEP_DELAY FLOAT,
          AVG_ARR_DELAY FLOAT,
          TOTAL_DISTANCE FLOAT
    )
    ROW FORMAT DELIMITED
    FIELDS TERMINATED BY '\t' STORED AS TEXTFILE LOCATION '${hiveDataFolder}';
    INSERT OVERWRITE TABLE ${hiveTableName}
    SELECT     year, month, day_of_month, carrier, avg(dep_delay) avg_dep_delay, 
            avg(arr_delay) avg_arr_delay, sum(distance) total_distance 
    FROM flights
    GROUP BY year, month, day_of_month, carrier 
    HAVING year = ${year} AND month = ${month} AND day_of_month = ${day};
    ```

    Met deze query maakt u een faserings tabel die alleen de samenvattings gegevens voor één dag opslaat, noteert u de SELECT-instructie die de gemiddelde vertragingen en het totale aantal overlopen per expediteur per dag berekent. De gegevens die in deze tabel zijn ingevoegd, worden opgeslagen op een bekende locatie (het pad dat wordt aangegeven door de variabele hiveDataFolder), zodat deze kan worden gebruikt als bron voor Sqoop in de volgende stap.

1. Maak een bestand met de naam van uw lokale werk station `workflow.xml` . Gebruik de onderstaande code als de inhoud van het bestand. Deze stappen worden weer gegeven als afzonderlijke acties in het Oozie-werk stroom bestand.

    ```xml
    <workflow-app name="loadflightstable" xmlns="uri:oozie:workflow:0.5">
        <start to = "RunHiveLoadFlightsScript"/>
        <action name="RunHiveLoadFlightsScript">
            <hive xmlns="uri:oozie:hive-action:0.2">
                <job-tracker>${jobTracker}</job-tracker>
                <name-node>${nameNode}</name-node>
                <configuration>
                <property>
                    <name>mapred.job.queue.name</name>
                    <value>${queueName}</value>
                </property>
                </configuration>
                <script>${hiveScriptLoadPartition}</script>
                <param>year=${year}</param>
                <param>month=${month}</param>
                <param>day=${day}</param>
            </hive>
            <ok to="RunHiveCreateDailyFlightTableScript"/>
            <error to="fail"/>
        </action>
    
        <action name="RunHiveCreateDailyFlightTableScript">
            <hive xmlns="uri:oozie:hive-action:0.2">
                <job-tracker>${jobTracker}</job-tracker>
                <name-node>${nameNode}</name-node>
                <configuration>
                <property>
                    <name>mapred.job.queue.name</name>
                    <value>${queueName}</value>
                </property>
                </configuration>
                <script>${hiveScriptCreateDailyTable}</script>
                <param>hiveTableName=${hiveDailyTableName}</param>
                <param>year=${year}</param>
                <param>month=${month}</param>
                <param>day=${day}</param>
                <param>hiveDataFolder=${hiveDataFolder}/${year}/${month}/${day}</param>
            </hive>
            <ok to="RunSqoopExport"/>
            <error to="fail"/>
        </action>
    
        <action name="RunSqoopExport">
            <sqoop xmlns="uri:oozie:sqoop-action:0.2">
                <job-tracker>${jobTracker}</job-tracker>
                <name-node>${nameNode}</name-node>
                <configuration>
                <property>
                    <name>mapred.compress.map.output</name>
                    <value>true</value>
                </property>
                </configuration>
                <arg>export</arg>
                <arg>--connect</arg>
                <arg>${sqlDatabaseConnectionString}</arg>
                <arg>--table</arg>
                <arg>${sqlDatabaseTableName}</arg>
                <arg>--export-dir</arg>
                <arg>${hiveDataFolder}/${year}/${month}/${day}</arg>
                <arg>-m</arg>
                <arg>1</arg>
                <arg>--input-fields-terminated-by</arg>
                <arg>"\t"</arg>
                <archive>mssql-jdbc-7.0.0.jre8.jar</archive>
                </sqoop>
            <ok to="end"/>
            <error to="fail"/>
        </action>
        <kill name="fail">
            <message>Job failed, error message[${wf:errorMessage(wf:lastErrorNode())}] </message>
        </kill>
        <end name="end"/>
    </workflow-app>
    ```

De twee Hive-query's worden geopend door hun pad in Azure Storage en de resterende waarden van de variabele worden door het `job.properties` bestand verschaft. Met dit bestand wordt de werk stroom geconfigureerd die moet worden uitgevoerd voor de datum van 3 januari 2017.

## <a name="deploy-and-run-the-oozie-workflow"></a>De Oozie-werk stroom implementeren en uitvoeren

Gebruik SCP vanuit uw bash-sessie om uw Oozie-werk stroom ( `workflow.xml` ), de Hive-query's ( `hive-load-flights-partition.hql` en `hive-create-daily-summary-table.hql` ) en de taak configuratie () te implementeren `job.properties` .  In Oozie kan alleen het `job.properties` bestand bestaan in de lokale opslag van de hoofd knooppunt. Alle andere bestanden moeten in dit geval Azure Storage worden opgeslagen in HDFS. De Sqoop-actie die door de werk stroom wordt gebruikt, is afhankelijk van een JDBC-stuur programma voor de communicatie met uw SQL Database, dat moet worden gekopieerd van het hoofd knooppunt naar HDFS.

1. Maak de `load_flights_by_day` submap onder het pad van de gebruiker in de lokale opslag van het hoofd knooppunt. Voer vanuit uw open SSH-sessie de volgende opdracht uit:

    ```bash
    mkdir load_flights_by_day
    ```

1. Kopieer alle bestanden in de huidige map ( `workflow.xml` en `job.properties` bestanden) naar de `load_flights_by_day` submap. Voer vanaf uw lokale werk station de volgende opdracht uit:

    ```cmd
    scp ./* sshuser@CLUSTERNAME-ssh.azurehdinsight.net:load_flights_by_day
    ```

1. Kopieer de werk stroom bestanden naar HDFS. Voer de volgende opdrachten uit vanuit uw open SSH-sessie:

    ```bash
    cd load_flights_by_day
    hadoop fs -mkdir -p /oozie/load_flights_by_day
    hdfs dfs -put ./* /oozie/load_flights_by_day
    ```

1. Kopieer `mssql-jdbc-7.0.0.jre8.jar` van het lokale hoofd knooppunt naar de map werk stroom in HDFS. Wijzig indien nodig de opdracht als uw cluster een ander jar-bestand bevat. Wijzig `workflow.xml` indien nodig om een ander jar-bestand weer te geven. Voer vanuit uw open SSH-sessie de volgende opdracht uit:

    ```bash
    hdfs dfs -put /usr/share/java/sqljdbc_7.0/enu/mssql-jdbc*.jar /oozie/load_flights_by_day
    ```

1. Voer de werk stroom uit. Voer vanuit uw open SSH-sessie de volgende opdracht uit:

    ```bash
    oozie job -config job.properties -run
    ```

1. Bekijk de status met behulp van de Oozie-webconsole. In Ambari selecteert u **Oozie**, **snelle koppelingen** en vervolgens **Oozie web-console**. Selecteer op het tabblad **werk stroom taken** de optie **alle taken**.

    :::image type="content" source="./media/hdinsight-operationalize-data-pipeline/hdi-oozie-web-console-workflows.png" alt-text="HDI oozie-webconsole-werk stromen":::

1. Als de status geslaagd is, query's uitvoeren op de tabel SQL Database om de ingevoegde rijen weer te geven. Ga met behulp van de Azure Portal naar het deel venster voor uw SQL Database, selecteer **extra** en open de **query-editor**.

    ```sql
    SELECT * FROM dailyflights
    ```

Nu de werk stroom wordt uitgevoerd voor de enkele test dag, kunt u deze werk stroom verpakken met een coördinator waarmee de werk stroom wordt gepland zodat deze dagelijks wordt uitgevoerd.

## <a name="run-the-workflow-with-a-coordinator"></a>De werk stroom uitvoeren met een coördinator

Als u deze werk stroom wilt plannen zodat deze dagelijks (of alle dagen in een datum bereik) wordt uitgevoerd, kunt u een coördinator gebruiken. Een coördinator wordt gedefinieerd door een XML-bestand, bijvoorbeeld `coordinator.xml` :

```xml
<coordinator-app name="daily_export" start="2017-01-01T00:00Z" end="2017-01-05T00:00Z" frequency="${coord:days(1)}" timezone="UTC" xmlns="uri:oozie:coordinator:0.4">
    <datasets>
        <dataset name="ds_input1" frequency="${coord:days(1)}" initial-instance="2016-12-31T00:00Z" timezone="UTC">
            <uri-template>${sourceDataFolder}${YEAR}-${MONTH}-FlightData.csv</uri-template>
            <done-flag></done-flag>
        </dataset>
    </datasets>
    <input-events>
        <data-in name="event_input1" dataset="ds_input1">
            <instance>${coord:current(0)}</instance>
        </data-in>
    </input-events>
    <action>
        <workflow>
            <app-path>${appBase}/load_flights_by_day</app-path>
            <configuration>
                <property>
                    <name>year</name>
                    <value>${coord:formatTime(coord:nominalTime(), 'yyyy')}</value>
                </property>
                <property>
                    <name>month</name>
                    <value>${coord:formatTime(coord:nominalTime(), 'MM')}</value>
                </property>
                <property>
                    <name>day</name>
                    <value>${coord:formatTime(coord:nominalTime(), 'dd')}</value>
                </property>
                <property>
                    <name>hiveScriptLoadPartition</name>
                    <value>${hiveScriptLoadPartition}</value>
                </property>
                <property>
                    <name>hiveScriptCreateDailyTable</name>
                    <value>${hiveScriptCreateDailyTable}</value>
                </property>
                <property>
                    <name>hiveDailyTableNamePrefix</name>
                    <value>${hiveDailyTableNamePrefix}</value>
                </property>
                <property>
                    <name>hiveDailyTableName</name>
                    <value>${hiveDailyTableNamePrefix}${coord:formatTime(coord:nominalTime(), 'yyyy')}${coord:formatTime(coord:nominalTime(), 'MM')}${coord:formatTime(coord:nominalTime(), 'dd')}</value>
                </property>
                <property>
                    <name>hiveDataFolderPrefix</name>
                    <value>${hiveDataFolderPrefix}</value>
                </property>
                <property>
                    <name>hiveDataFolder</name>
                    <value>${hiveDataFolderPrefix}${coord:formatTime(coord:nominalTime(), 'yyyy')}/${coord:formatTime(coord:nominalTime(), 'MM')}/${coord:formatTime(coord:nominalTime(), 'dd')}</value>
                </property>
                <property>
                    <name>sqlDatabaseConnectionString</name>
                    <value>${sqlDatabaseConnectionString}</value>
                </property>
                <property>
                    <name>sqlDatabaseTableName</name>
                    <value>${sqlDatabaseTableName}</value>
                </property>
            </configuration>
        </workflow>
    </action>
</coordinator-app>
```

Zoals u ziet, geeft de meerderheid van de coördinator alleen configuratie-informatie door aan het werk stroom exemplaar. Er zijn echter enkele belang rijke items die u kunt aanroepen.

* Punt 1: de `start` `end` kenmerken en van het `coordinator-app` element zelf bepalen het tijds interval voor de uitvoering van de coördinator.

    ```
    <coordinator-app ... start="2017-01-01T00:00Z" end="2017-01-05T00:00Z" frequency="${coord:days(1)}" ...>
    ```

    Een coördinator is verantwoordelijk voor het plannen van acties binnen het `start` `end` datum bereik en op basis van het interval dat is opgegeven door het `frequency` kenmerk. Elke geplande actie op zijn beurt voert de werk stroom uit zoals geconfigureerd. In de coördinator definitie hierboven is de coördinator geconfigureerd om acties uit te voeren vanaf 1 januari 2017 tot 5 januari 2017. De frequentie wordt ingesteld op één dag met de expressie voor de taal frequentie van [Oozie](https://oozie.apache.org/docs/4.2.0/CoordinatorFunctionalSpec.html#a4.4._Frequency_and_Time-Period_Representation) `${coord:days(1)}` . Dit leidt ertoe dat de coördinator een actie (en dus de werk stroom) één keer per dag plant. Voor datumbereiken die zich in het verleden bevinden, zoals in dit voor beeld, wordt de actie gepland om zonder vertraging te worden uitgevoerd. Het begin van de datum vanaf welke de uitvoering van een actie wordt gepland, wordt de *nominale tijd* genoemd. Als u bijvoorbeeld de gegevens voor 1 januari 2017 wilt verwerken, zal de coördinator een actie plannen met een nominale tijd van 2017-01-01T00:00:00 GMT.

* Punt 2: binnen het datum bereik van de werk stroom `dataset` geeft het element op waar u wilt zoeken naar HDFS voor de gegevens voor een bepaald datum bereik en configureert u hoe Oozie bepaalt of de gegevens nog beschikbaar zijn voor verwerking.

    ```xml
    <dataset name="ds_input1" frequency="${coord:days(1)}" initial-instance="2016-12-31T00:00Z" timezone="UTC">
        <uri-template>${sourceDataFolder}${YEAR}-${MONTH}-FlightData.csv</uri-template>
        <done-flag></done-flag>
    </dataset>
    ```

    Het pad naar de gegevens in HDFS wordt dynamisch gebouwd op basis van de expressie in het- `uri-template` element. In deze coördinator wordt er ook een frequentie van één dag gebruikt voor de gegevensset. De begin-en eind datum van het element van de coördinator bepalen wanneer de acties worden gepland (en definieert hun nominale tijden), de `initial-instance` en `frequency` op de gegevensset bepalen de berekening van de datum die wordt gebruikt voor het bouwen van de `uri-template` . In dit geval stelt u het oorspronkelijke exemplaar in op één dag vóór het begin van de coördinator om ervoor te zorgen dat het de eerste dag (1/1/2017) aan gegevens ophaalt. De datum berekening van de gegevensset wordt voorwaarts uitgevoerd op basis van de waarde van `initial-instance` (12/31/2016), in stappen van de frequentie (één dag), tot de meest recente datum waarop de nominale tijd die door de coördinator is ingesteld (2017-01-01T00:00:00 GMT voor de eerste actie) wordt gevonden.

    Het lege `done-flag` element geeft aan dat wanneer Oozie controleert op de aanwezigheid van invoer gegevens op de aangewezen tijd, Oozie bepaalt of gegevens beschikbaar zijn door aanwezigheid van een map of bestand. In dit geval is het de aanwezigheid van een CSV-bestand. Als er een CSV-bestand aanwezig is, gaat Oozie ervan uit dat de gegevens gereed zijn en wordt een workflowexemplaar gestart om het bestand te verwerken. Als er geen CSV-bestand aanwezig is, gaat Oozie ervan uit dat de gegevens nog niet gereed zijn en dat de werk stroom wordt uitgevoerd in een wacht status.

* Punt 3: in het `data-in` element wordt de specifieke tijds tempel aangegeven die moet worden gebruikt als de nominale tijd bij het vervangen van de waarden in `uri-template` voor de bijbehorende gegevensset.

    ```xml
    <data-in name="event_input1" dataset="ds_input1">
        <instance>${coord:current(0)}</instance>
    </data-in>
    ```

    In dit geval stelt u het exemplaar in op de expressie `${coord:current(0)}` , waarmee wordt gebruikgemaakt van de nominale tijd van de actie, zoals oorspronkelijk gepland door de coördinator. Met andere woorden, wanneer de coördinator de actie plant om te worden uitgevoerd met een nominale tijd van 01/01/2017, wordt 01/01/2017 wat wordt gebruikt om de variabelen YEAR (2017) en MONTH (01) in de URI-sjabloon te vervangen. Zodra de URI-sjabloon voor dit exemplaar is berekend, controleert Oozie of de verwachte map of het bestand beschikbaar is en wordt de volgende uitvoering van de werk stroom dienovereenkomstig gepland.

De drie voorafgaande punten combi neren om een situatie op te leveren waarbij de coördinator de verwerking van de bron gegevens op een dag per dag plant.

* Punt 1: de coördinator begint met een nominale datum van 2017-01-01.

* Punt 2: Oozie zoekt naar gegevens die beschikbaar zijn in `sourceDataFolder/2017-01-FlightData.csv` .

* Punt 3: wanneer Oozie dat bestand vindt, wordt een exemplaar van de werk stroom gepland dat de gegevens verwerkt voor 2017-01-01. Oozie gaat vervolgens verder met de verwerking van 2017-01-02. Deze evaluatie wordt herhaald tot maar niet inclusief 2017-01-05.

Net als bij werk stromen wordt de configuratie van een coördinator gedefinieerd in een `job.properties` bestand met een superset van de instellingen die door de werk stroom worden gebruikt.

```text
nameNode=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net
jobTracker=[ACTIVERESOURCEMANAGER]:8050
queueName=default
oozie.use.system.libpath=true
appBase=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net/oozie
oozie.coord.application.path=${appBase}
sourceDataFolder=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net/example/data/flights/
hiveScriptLoadPartition=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net/oozie/load_flights_by_day/hive-load-flights-partition.hql
hiveScriptCreateDailyTable=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net/oozie/load_flights_by_day/hive-create-daily-summary-table.hql
hiveDailyTableNamePrefix=dailyflights
hiveDataFolderPrefix=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net/example/data/flights/day/
sqlDatabaseConnectionString="jdbc:sqlserver://[SERVERNAME].database.windows.net;user=[USERNAME];password=[PASSWORD];database=[DATABASENAME]"
sqlDatabaseTableName=dailyflights
```

De enige nieuwe eigenschappen die in dit `job.properties` bestand zijn geïntroduceerd zijn:

| Eigenschap | Waarde bron |
| --- | --- |
| oozie. coord. Application. Path | Hiermee wordt de locatie van het `coordinator.xml` bestand met de Oozie-coördinator aangegeven dat moet worden uitgevoerd. |
| hiveDailyTableNamePrefix | Het voor voegsel dat wordt gebruikt bij het dynamisch maken van de tabel naam van de faserings tabel. |
| hiveDataFolderPrefix | Het voor voegsel van het pad waar alle faserings tabellen worden opgeslagen. |

## <a name="deploy-and-run-the-oozie-coordinator"></a>De Oozie-coördinator implementeren en uitvoeren

Als u de pijp lijn met een coördinator wilt uitvoeren, gaat u op een vergelijk bare manier als voor de werk stroom, met uitzonde ring van een map die één niveau boven de map met uw werk stroom bevat. Deze map scheidt de coördinatoren van de werk stromen op de schijf, zodat u één coördinator kunt koppelen aan verschillende onderliggende werk stromen.

1. Gebruik SCP van uw lokale computer om de coördinator bestanden te kopiëren naar de lokale opslag van het hoofd knooppunt van uw cluster.

    ```bash
    scp ./* sshuser@CLUSTERNAME-ssh.azurehdinsight.net:~
    ```

2. SSH in uw hoofd knooppunt.

    ```bash
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

3. Kopieer de bestanden van de coördinator naar HDFS.

    ```bash
    hdfs dfs -put ./* /oozie/
    ```

4. De coördinator uit te voeren.

    ```bash
    oozie job -config job.properties -run
    ```

5. Controleer de status met behulp van de Oozie-webconsole. deze keer dat u het tabblad **coördinator taken** selecteert en vervolgens  **alle taken**.

    :::image type="content" source="./media/hdinsight-operationalize-data-pipeline/hdi-oozie-web-console-coordinator-jobs.png" alt-text="Taken voor Oozie web console-coördinator":::

6. Selecteer een coördinator instantie om de lijst met geplande acties weer te geven. In dit geval ziet u vier acties met nominale tijden in het bereik van 1/1/2017 tot 1/4/2017.

    :::image type="content" source="./media/hdinsight-operationalize-data-pipeline/hdi-oozie-web-console-coordinator-instance.png" alt-text="Taak Oozie web console-coördinator":::

    Elke actie in deze lijst komt overeen met een exemplaar van de werk stroom die de gegevens van één dag verwerkt, waarbij het begin van die dag wordt aangegeven door de nominale tijd.

## <a name="next-steps"></a>Volgende stappen

[Documentatie voor Apache Oozie](https://oozie.apache.org/docs/4.2.0/index.html)
