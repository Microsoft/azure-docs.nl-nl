---
title: Apache Spark gebruiken om gegevens te lezen en te schrijven naar Azure SQL Database
description: Meer informatie over het instellen van een verbinding tussen HDInsight Spark-cluster en Azure SQL Database. Gegevens lezen, gegevens schrijven en gegevens streamen naar een SQL database
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/20/2020
ms.openlocfilehash: 97c326bd1d2f61ba3fb9d6e381f5a8711bd0ca1d
ms.sourcegitcommit: 28c93f364c51774e8fbde9afb5aa62f1299e649e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/30/2020
ms.locfileid: "97821193"
---
# <a name="use-hdinsight-spark-cluster-to-read-and-write-data-to-azure-sql-database"></a>HDInsight Spark-cluster gebruiken om gegevens te lezen en te schrijven naar Azure SQL Database

Meer informatie over hoe u met Azure SQL Database verbinding maakt met een Apache Spark cluster in azure HDInsight. Lees, schrijf en stream gegevens in de SQL database. De instructies in dit artikel gebruiken een Jupyter Notebook voor het uitvoeren van de code fragmenten scala. U kunt echter een zelfstandige toepassing maken in scala of python en dezelfde taken uitvoeren.

## <a name="prerequisites"></a>Vereisten

* Azure HDInsight Spark cluster.  Volg de instructies in [een Apache Spark-cluster maken in HDInsight](apache-spark-jupyter-spark-sql.md).

* Azure SQL Database. Volg de instructies bij het [maken van een data base in Azure SQL database](../../azure-sql/database/single-database-create-quickstart.md). Zorg ervoor dat u een Data Base maakt met het voor beeld- **AdventureWorksLT** schema en de gegevens. Zorg er ook voor dat u een firewall regel op server niveau maakt om het IP-adres van de client toe te staan om toegang te krijgen tot de SQL database. De instructies voor het toevoegen van de firewall regel zijn beschikbaar in hetzelfde artikel. Wanneer u uw SQL database hebt gemaakt, moet u ervoor zorgen dat u de volgende waarden kunt gebruiken. U hebt deze nodig om verbinding te maken met de data base vanuit een Spark-cluster.

    * Server naam.
    * Database naam.
    * Gebruikers naam/wacht woord voor beheerder Azure SQL Database.

* SQL Server Management Studio (SSMS). Volg de instructies op [SSMS gebruiken om verbinding te maken en gegevens op te vragen](../../azure-sql/database/connect-query-ssms.md).

## <a name="create-a-jupyter-notebook"></a>Een Jupyter Notebook maken

Maak eerst een Jupyter Notebook dat is gekoppeld aan het Spark-cluster. U gebruikt dit notitie blok om de code fragmenten uit te voeren die in dit artikel worden gebruikt.

1. Open uw cluster vanuit het [Azure Portal](https://portal.azure.com/).
1. Selecteer **Jupyter notebook** onder **cluster dashboards** aan de rechter kant.  Als **cluster dashboards** niet worden weer gegeven, selecteert u **overzicht** in het menu links. Voer de beheerdersreferenties voor het cluster in als u daarom wordt gevraagd.

    ![Jupyter Notebook op Apache Spark](./media/apache-spark-connect-to-sql-database/hdinsight-spark-cluster-dashboard-jupyter-notebook.png "Jupyter Notebook op Spark")

   > [!NOTE]  
   > U kunt ook toegang krijgen tot de Jupyter Notebook in Spark-cluster door de volgende URL in uw browser te openen. Vervang **CLUSTERNAME** door de naam van uw cluster.
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

1. Klik in de rechter bovenhoek van de Jupyter Notebook op **Nieuw** en klik vervolgens op **Spark** om een scala-notebook te maken. Jupyter notebooks in HDInsight Spark-cluster bieden ook de **PySpark** kernel voor Python2-toepassingen en de **PySpark3** -kernel voor Python3-toepassingen. Voor dit artikel maken we een scala-notebook.

    ![Kernels voor Jupyter Notebook op Spark](./media/apache-spark-connect-to-sql-database/kernel-jupyter-notebook-on-spark.png "Kernels voor Jupyter Notebook op Spark")

    Zie [Jupyter notebook-kernels met Apache Spark clusters in HDInsight gebruiken](apache-spark-jupyter-notebook-kernels.md)voor meer informatie over de kernels.

   > [!NOTE]  
   > In dit artikel gebruiken we een Spark-kernel (scala), omdat het streamen van gegevens van Spark naar SQL Database alleen wordt ondersteund in scala en Java. Hoewel het lezen van en schrijven naar SQL kan worden uitgevoerd met behulp van python, wordt voor consistentie in dit artikel scala gebruikt voor alle drie de bewerkingen.

1. Er wordt een nieuw notitie blok geopend met een standaard naam zonder **titel**. Klik op de naam van het notitie blok en voer de naam van uw keuze in.

    ![Een naam opgeven voor de notebook](./media/apache-spark-connect-to-sql-database/hdinsight-spark-jupyter-notebook-name.png "Een naam opgeven voor de notebook")

U kunt nu beginnen met het maken van uw toepassing.

## <a name="read-data-from-azure-sql-database"></a>Gegevens lezen uit Azure SQL Database

In deze sectie leest u gegevens uit een tabel (bijvoorbeeld **tabel saleslt. Address**) die zich in de AdventureWorks-Data Base bevindt.

1. Plak in een nieuwe Jupyter Notebook in een code-cel het volgende code fragment en vervang de waarden van de tijdelijke aanduiding door de waarden voor uw data base.

    ```scala
    // Declare the values for your database

    val jdbcUsername = "<SQL DB ADMIN USER>"
    val jdbcPassword = "<SQL DB ADMIN PWD>"
    val jdbcHostname = "<SQL SERVER NAME HOSTING SDL DB>" //typically, this is in the form or servername.database.windows.net
    val jdbcPort = 1433
    val jdbcDatabase ="<AZURE SQL DB NAME>"
    ```

    Druk op **SHIFT+ENTER** om de codecel uit te voeren.  

1. Gebruik het onderstaande fragment om een JDBC-URL te maken die u kunt door geven aan de Spark data frame-Api's. De code maakt een `Properties` object voor de para meters. Plak het fragment in een code-cel en druk op **SHIFT + ENTER** om uit te voeren.

    ```scala
    import java.util.Properties

    val jdbc_url = s"jdbc:sqlserver://${jdbcHostname}:${jdbcPort};database=${jdbcDatabase};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=60;"
    val connectionProperties = new Properties()
    connectionProperties.put("user", s"${jdbcUsername}")
    connectionProperties.put("password", s"${jdbcPassword}")
    ```

1. Gebruik het onderstaande fragment om een data frame te maken met de gegevens uit een tabel in uw data base. In dit code fragment `SalesLT.Address` wordt een tabel gebruikt die beschikbaar is als onderdeel van de **AdventureWorksLT** -data base. Plak het fragment in een code-cel en druk op **SHIFT + ENTER** om uit te voeren.

    ```scala
    val sqlTableDF = spark.read.jdbc(jdbc_url, "SalesLT.Address", connectionProperties)
    ```

1. U kunt nu bewerkingen uitvoeren op de data frame, zoals het ophalen van het gegevens schema:

    ```scala
    sqlTableDF.printSchema
    ```

    U ziet een uitvoer die vergelijkbaar is met de volgende afbeelding:

    ![schema-uitvoer](./media/apache-spark-connect-to-sql-database/read-from-sql-schema-output.png "schema-uitvoer")

1. U kunt ook bewerkingen uitvoeren zoals, de bovenste 10 rijen ophalen.

    ```scala
    sqlTableDF.show(10)
    ```

1. Of haal specifieke kolommen op uit de gegevensset.

    ```scala
    sqlTableDF.select("AddressLine1", "City").show(10)
    ```

## <a name="write-data-into-azure-sql-database"></a>Gegevens schrijven naar Azure SQL Database

In deze sectie gebruiken we een voor beeld van een CSV-bestand dat op het cluster beschikbaar is om een tabel in uw data base te maken en deze te vullen met gegevens. Het CSV-voorbeeld bestand (**HVAC.csv**) is beschikbaar op alle HDInsight-clusters op `HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv` .

1. Plak in een nieuwe Jupyter Notebook in een code-cel het volgende code fragment en vervang de waarden van de tijdelijke aanduiding door de waarden voor uw data base.

    ```scala
    // Declare the values for your database

    val jdbcUsername = "<SQL DB ADMIN USER>"
    val jdbcPassword = "<SQL DB ADMIN PWD>"
    val jdbcHostname = "<SQL SERVER NAME HOSTING SDL DB>" //typically, this is in the form or servername.database.windows.net
    val jdbcPort = 1433
    val jdbcDatabase ="<AZURE SQL DB NAME>"
    ```

    Druk op **SHIFT+ENTER** om de codecel uit te voeren.  

1. Het volgende code fragment bouwt een JDBC-URL op die u kunt door geven aan de Spark data frame-Api's. De code maakt een `Properties` object voor de para meters. Plak het fragment in een code-cel en druk op **SHIFT + ENTER** om uit te voeren.

    ```scala
    import java.util.Properties

    val jdbc_url = s"jdbc:sqlserver://${jdbcHostname}:${jdbcPort};database=${jdbcDatabase};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=60;"
    val connectionProperties = new Properties()
    connectionProperties.put("user", s"${jdbcUsername}")
    connectionProperties.put("password", s"${jdbcPassword}")
    ```

1. Gebruik het volgende code fragment voor het extra heren van het schema van de gegevens in HVAC.csv en gebruik het schema om de gegevens uit het CSV-bestand in een data frame te laden `readDf` . Plak het fragment in een code-cel en druk op **SHIFT + ENTER** om uit te voeren.

    ```scala
    val userSchema = spark.read.option("header", "true").csv("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv").schema
    val readDf = spark.read.format("csv").schema(userSchema).load("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
    ```

1. Gebruik de `readDf` Data frame om een tijdelijke tabel te maken `temphvactable` . Gebruik vervolgens de tijdelijke tabel om een Hive-tabel te maken `hvactable_hive` .

    ```scala
    readDf.createOrReplaceTempView("temphvactable")
    spark.sql("create table hvactable_hive as select * from temphvactable")
    ```

1. Ten slotte gebruikt u de Hive-tabel om een tabel in uw data base te maken. Het volgende code fragment maakt `hvactable` in Azure SQL database.

    ```scala
    spark.table("hvactable_hive").write.jdbc(jdbc_url, "hvactable", connectionProperties)
    ```

1. Maak verbinding met de Azure SQL Database met behulp van SSMS en controleer of er een er wordt weer geven `dbo.hvactable` .

    a. Start SSMS en maak verbinding met de Azure SQL Database door verbindings gegevens op te geven, zoals wordt weer gegeven in de onderstaande scherm afbeelding.

    ![Verbinding maken met SQL Database met behulp van SSMS1](./media/apache-spark-connect-to-sql-database/connect-to-sql-db-ssms.png "Verbinding maken met SQL Database met behulp van SSMS1")

    b. Vouw vanuit **objectverkenner** de data base en het tabel knooppunt uit om te zien hoe **dbo. hvactable** is gemaakt.

    ![Verbinding maken met SQL Database met behulp van SSMS2](./media/apache-spark-connect-to-sql-database/connect-to-sql-db-ssms-locate-table.png "Verbinding maken met SQL Database met behulp van SSMS2")

1. Voer een query uit in SSMS om de kolommen in de tabel weer te geven.

    ```sql
    SELECT * from hvactable
    ```

## <a name="stream-data-into-azure-sql-database"></a>Gegevens streamen naar Azure SQL Database

In deze sectie worden gegevens gestreamd naar de `hvactable` die u in de vorige sectie hebt gemaakt.

1. Als eerste stap zorgt u ervoor dat er geen records in zijn `hvactable` . Gebruik SSMS om de volgende query uit te voeren op de tabel.

    ```sql
    TRUNCATE TABLE [dbo].[hvactable]
    ```

1. Maak een nieuwe Jupyter Notebook op het HDInsight Spark-cluster. Plak het volgende fragment in een code-cel en druk op **SHIFT + ENTER**:

    ```scala
    import org.apache.spark.sql._
    import org.apache.spark.sql.types._
    import org.apache.spark.sql.functions._
    import org.apache.spark.sql.streaming._
    import java.sql.{Connection,DriverManager,ResultSet}
    ```

1. We streamen gegevens van het **HVAC.csv** naar de `hvactable` . HVAC.csv-bestand is beschikbaar op het cluster op `/HdiSamples/HdiSamples/SensorSampleData/HVAC/` . In het volgende code fragment ontvangen we eerst het schema van de gegevens die moeten worden gestreamd. Vervolgens maken we een streaming-data frame met behulp van dat schema. Plak het fragment in een code-cel en druk op **SHIFT + ENTER** om uit te voeren.

    ```scala
    val userSchema = spark.read.option("header", "true").csv("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv").schema
    val readStreamDf = spark.readStream.schema(userSchema).csv("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/") 
    readStreamDf.printSchema
    ```

1. De uitvoer toont het schema van **HVAC.csv**. De `hvactable` heeft ook hetzelfde schema. In de uitvoer worden de kolommen in de tabel weer gegeven.

    ![' hdinsight-Apache Spark schema tabel '](./media/apache-spark-connect-to-sql-database/hdinsight-schema-table.png "Schema van tabel")

1. Gebruik ten slotte het volgende fragment om gegevens van de HVAC.csv te lezen en deze in de `hvactable` Data Base te streamen. Plak het fragment in een code-cel, vervang de waarden van de tijdelijke aanduiding door de waarden voor uw data base en druk op **SHIFT + ENTER** om uit te voeren.

    ```scala
    val WriteToSQLQuery  = readStreamDf.writeStream.foreach(new ForeachWriter[Row] {
        var connection:java.sql.Connection = _
        var statement:java.sql.Statement = _

        val jdbcUsername = "<SQL DB ADMIN USER>"
        val jdbcPassword = "<SQL DB ADMIN PWD>"
        val jdbcHostname = "<SQL SERVER NAME HOSTING SDL DB>" //typically, this is in the form or servername.database.windows.net
        val jdbcPort = 1433
        val jdbcDatabase ="<AZURE SQL DB NAME>"
        val driver = "com.microsoft.sqlserver.jdbc.SQLServerDriver"
        val jdbc_url = s"jdbc:sqlserver://${jdbcHostname}:${jdbcPort};database=${jdbcDatabase};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;"

        def open(partitionId: Long, version: Long):Boolean = {
        Class.forName(driver)
        connection = DriverManager.getConnection(jdbc_url, jdbcUsername, jdbcPassword)
        statement = connection.createStatement
        true
        }

        def process(value: Row): Unit = {
        val Date  = value(0)
        val Time = value(1)
        val TargetTemp = value(2)
        val ActualTemp = value(3)
        val System = value(4)
        val SystemAge = value(5)
        val BuildingID = value(6)  

        val valueStr = "'" + Date + "'," + "'" + Time + "'," + "'" + TargetTemp + "'," + "'" + ActualTemp + "'," + "'" + System + "'," + "'" + SystemAge + "'," + "'" + BuildingID + "'"
        statement.execute("INSERT INTO " + "dbo.hvactable" + " VALUES (" + valueStr + ")")
        }

        def close(errorOrNull: Throwable): Unit = {
        connection.close
        }
        })

    var streamingQuery = WriteToSQLQuery.start()
    ```

1. Controleer of de gegevens worden gestreamd naar de `hvactable` door de volgende query uit te voeren in SQL Server Management Studio (SSMS). Telkens wanneer u de query uitvoert, wordt het aantal rijen in de tabel verhoogd.

    ```sql
    SELECT COUNT(*) FROM hvactable
    ```

## <a name="next-steps"></a>Volgende stappen

* [HDInsight Spark-cluster gebruiken voor het analyseren van gegevens in Data Lake Storage](apache-spark-use-with-data-lake-store.md)
* [Gegevens laden en query's uitvoeren op een Apache Spark cluster in azure HDInsight](apache-spark-load-data-run-query.md)
* [Apache Spark Structured Streaming gebruiken met Apache Kafka op HDInsight](../hdinsight-apache-kafka-spark-structured-streaming.md)
