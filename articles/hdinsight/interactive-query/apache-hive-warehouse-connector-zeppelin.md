---
title: Hive Warehouse connector-Apache Zeppelin met livy-Azure HDInsight
description: Meer informatie over het integreren van Hive-Warehouse connector met Apache Zeppelin op Azure HDInsight.
author: nis-goel
ms.author: nisgoel
ms.service: hdinsight
ms.topic: how-to
ms.date: 05/28/2020
ms.openlocfilehash: 7957041c9d445f4239b8f0100128a531d982b451
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104871551"
---
# <a name="integrate-apache-zeppelin-with-hive-warehouse-connector-in-azure-hdinsight"></a>Apache Zeppelin integreren met hive Warehouse connector in azure HDInsight

HDInsight Spark-clusters zijn Apache Zeppelin-notebooks met verschillende interpreters. In dit artikel focussen we alleen op de livy-interpreter om toegang te krijgen tot Hive-tabellen uit Spark met behulp van Hive Warehouse connector.

> [!NOTE]
> Dit artikel bevat verwijzingen naar de term *whitelist*, een term die Microsoft niet meer gebruikt. Zodra de term uit de software wordt verwijderd, verwijderen we deze uit dit artikel.

## <a name="prerequisite"></a>Vereiste

Voltooi de installatie stappen van het [Hive Warehouse-connector](apache-hive-warehouse-connector.md#hive-warehouse-connector-setup) .

## <a name="getting-started"></a>Aan de slag

1. Gebruik de [SSH-opdracht](../hdinsight-hadoop-linux-use-ssh-unix.md) om verbinding te maken met uw Apache Spark cluster. Bewerk de onderstaande opdracht door CLUSTERNAME te vervangen door de naam van uw cluster. Voer vervolgens deze opdracht in:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Voer vanuit uw SSH-sessie de volgende opdracht uit om de versies voor `hive-warehouse-connector-assembly` te noteren en `pyspark_hwc` :

    ```bash
    ls /usr/hdp/current/hive_warehouse_connector
    ```

    Sla de uitvoer op voor later gebruik bij het configureren van Apache Zeppelin.

## <a name="configure-livy"></a>Livy configureren

De volgende configuraties zijn vereist voor toegang tot Hive-tabellen van Zeppelin met de livy-interpreter.

### <a name="interactive-query-cluster"></a>Interactief query cluster

1. Navigeer vanuit een webbrowser naar `https://LLAPCLUSTERNAME.azurehdinsight.net/#/main/services/HDFS/configs` waar LLAPCLUSTERNAME de naam is van uw interactieve query cluster.

1. Ga naar **Geavanceerde**  >  **aangepaste kern site**. Selecteer **eigenschap toevoegen...** om de volgende configuraties toe te voegen:

    | Configuratie                 | Waarde |
    | ----------------------------- |-------|
    | Hadoop. proxyuser. livy. groups  | *     |
    | Hadoop. proxyuser. livy. hosts   | *     |

1. Sla de wijzigingen op en start alle betrokken onderdelen opnieuw op.

### <a name="spark-cluster"></a>Spark-cluster

1. Navigeer vanuit een webbrowser naar `https://CLUSTERNAME.azurehdinsight.net/#/main/services/SPARK2/configs` de naam van het Apache Spark cluster.

1. Vouw **aangepaste livy2-conf** uit. Selecteer **eigenschap toevoegen...** om de volgende configuratie toe te voegen:

    | Configuratie                 | Waarde                                      |
    | ----------------------------- |------------------------------------------  |
    | livy. file. local-dir-white list | /usr/HDP/Current/hive_warehouse_connector/ |

1. Sla de wijzigingen op en start alle betrokken onderdelen opnieuw op.

### <a name="configure-livy-interpreter-in-zeppelin-ui-spark-cluster"></a>Livy-interpreter configureren in Zeppelin-gebruikers interface (Spark-cluster)

1. Ga in een webbrowser naar `https://CLUSTERNAME.azurehdinsight.net/zeppelin/#/interpreter` , waarbij `CLUSTERNAME` de naam is van uw Apache Spark cluster.

1. Navigeer naar **livy2**.

1. Voeg de volgende configuraties toe:

    | Configuratie                 | Waarde                                      |
    | ----------------------------- |:------------------------------------------:|
    | livy. Spark. Hadoop. component. llap. daemon. service. hosts | @llap0 |
    | livy. Spark. Security. credentials. hiveserver2. enabled | true |
    | livy. Spark. SQL. component. llap | true |
    | livy. Spark. garens. Security. credentials. hiveserver2. enabled | true |
    | livy. supergebruikers | livy, Zeppelin |
    | livy. Spark. potten | `file:///usr/hdp/current/hive_warehouse_connector/hive-warehouse-connector-assembly-VERSION.jar`.<br>Vervang de versie door de waarde die u hebt verkregen om eerder [aan](#getting-started)de slag te gaan. |
    | livy. Spark. Submit. pyFiles | `file:///usr/hdp/current/hive_warehouse_connector/pyspark_hwc-VERSION.zip`.<br>Vervang de versie door de waarde die u hebt verkregen om eerder [aan](#getting-started)de slag te gaan. |
    | livy. Spark. SQL. component. hiveserver2. JDBC. URL | Stel deze in op de HiveServer2 Interactive JDBC-URL van het interactieve query cluster. |
    | Spark. Security. credentials. hiveserver2. enabled | true |

1. Voor alleen ESP-clusters voegt u de volgende configuratie toe:

    | Configuratie| Waarde|
    |---|---|
    | livy. Spark. SQL. component. hiveserver2. JDBC. URL. Principal | `hive/<llap-headnode>@<AAD-Domain>` |

    * Navigeer vanuit een webbrowser naar `https://CLUSTERNAME.azurehdinsight.net/#/main/services/HIVE/summary` de naam van het interactieve query cluster. Klik op **HiveServer2 Interactive**. U ziet de FQDN-naam (Fully Qualified Domain Name) van het hoofd knooppunt waarop LLAP wordt uitgevoerd, zoals wordt weer gegeven in de scherm opname. Vervang door `<llap-headnode>` deze waarde.

        :::image type="content" source="./media/apache-hive-warehouse-connector/head-node-hive-server-interactive.png" alt-text="Hoofd knooppunt van de component Warehouse-connector" border="true":::

    * Gebruik de [SSH-opdracht](../hdinsight-hadoop-linux-use-ssh-unix.md) om verbinding te maken met uw interactieve query cluster. Zoek naar `default_realm` de para meter in het `/etc/krb5.conf` bestand. Vervang door `<AAD-DOMAIN>` deze waarde als een hoofd letter teken reeks, anders wordt de referentie niet gevonden.

        :::image type="content" source="./media/apache-hive-warehouse-connector/aad-domain.png" alt-text="AAD-domein van Hive Warehouse connector" border="true":::

    * Bijvoorbeeld `hive/hn0-ng36ll.mjry42ikpruuxgs2qy2kpg4q5e.cx.internal.cloudapp.net@PKRSRVUQVMAE6J85.D2.INTERNAL.CLOUDAPP.NET`.

1. Sla de wijzigingen op en start de livy-interpreter opnieuw.

Als livy-interpreter niet toegankelijk is, wijzigt u het `shiro.ini` bestand in Zeppelin onderdeel in Ambari. Zie [Configure Apache Zeppelin Security](https://docs.cloudera.com/HDPDocuments/HDP3/HDP-3.0.1/configuring-zeppelin-security/content/enabling_access_control_for_interpreter__configuration__and_credential_settings.html)(Engelstalig) voor meer informatie.  


## <a name="running-queries-in-zeppelin"></a>Query's uitvoeren in Zeppelin 

Start een Zeppelin-notebook met livy-interpreter en voer het volgende uit:

```python
%livy2

import com.hortonworks.hwc.HiveWarehouseSession
import com.hortonworks.hwc.HiveWarehouseSession._
import org.apache.spark.sql.SaveMode

# Initialize the hive context
val hive = HiveWarehouseSession.session(spark).build()

# Create a database
hive.createDatabase("hwc_db",true)
hive.setDatabase("hwc_db")

# Create a Hive table
hive.createTable("testers").ifNotExists().column("id", "bigint").column("name", "string").create()

val dataDF = Seq( (1, "foo"), (2, "bar"), (8, "john")).toDF("id", "name")

# Validate writes to the table
dataDF.write.format("com.hortonworks.spark.sql.hive.llap.HiveWarehouseConnector").mode("append").option("table", "hwc_db.testers").save()

# Validate reads
hive.executeQuery("select * from testers").show()

```

## <a name="next-steps"></a>Volgende stappen

* [HWC- en Apache Spark-bewerkingen](./apache-hive-warehouse-connector-operations.md)
* [HWC-integratie met Apache Spark en Apache Hive](./apache-hive-warehouse-connector.md)
* [Interactieve query gebruiken met HDInsight](./apache-interactive-query-get-started.md)
