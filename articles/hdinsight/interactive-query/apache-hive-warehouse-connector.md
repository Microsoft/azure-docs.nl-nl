---
title: Apache Spark & component-Hive Warehouse connector-Azure HDInsight
description: Meer informatie over het integreren van Apache Spark en Apache Hive met de Hive-Warehouse connector op Azure HDInsight.
author: nis-goel
ms.author: nisgoel
ms.service: hdinsight
ms.topic: how-to
ms.date: 05/28/2020
ms.openlocfilehash: 57a3d76f24c33984a883e926a8d4c68736e9f121
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104869885"
---
# <a name="integrate-apache-spark-and-apache-hive-with-hive-warehouse-connector-in-azure-hdinsight"></a>Apache Spark en Apache Hive integreren met hive Warehouse connector in azure HDInsight

De Apache Hive Warehouse connector (HWC) is een bibliotheek waarmee u gemakkelijker kunt werken met Apache Spark en Apache Hive. Het ondersteunt taken zoals het verplaatsen van gegevens tussen Spark-DataFrames en Hive-tabellen. Daarnaast kunt u met Spark streaming-gegevens in Hive-tabellen richten. Hive Warehouse connector werkt als een brug tussen Spark en Hive. Het biedt ook ondersteuning voor scala, Java en python als programmeer talen voor ontwikkeling.

Met de component Warehouse connector kunt u profiteren van de unieke functies van Hive en Spark om krachtige toepassingen voor Big data te bouwen.

Apache Hive biedt ondersteuning voor database transacties die atomisch, consistent, geïsoleerd en duurzaam zijn. Zie [Hive-trans acties](https://cwiki.apache.org/confluence/display/Hive/Hive+Transactions)voor meer informatie over zuren en trans acties in Hive. Hive biedt ook gedetailleerde beveiligings controles via Apache zwerver en LLAP (Low latentie Analytical Processing) die niet beschikbaar zijn in Apache Spark.

Apache Spark heeft een Structured streaming API die streaming-mogelijkheden biedt die niet beschikbaar zijn in Apache Hive. Vanaf HDInsight 4,0 hebben Apache Spark 2.3.1 en Apache Hive 3.1.0 afzonderlijke meta Stores. De afzonderlijke meta Stores kunnen de interoperabiliteit lastig maken. De Hive-Warehouse connector maakt het gemakkelijker om Spark en Hive samen te gebruiken. De HWC-bibliotheek laadt gegevens van LLAP-daemons naar Spark-uitvoerende parallel. Dit proces maakt het efficiënter en aanpasbaarer dan een standaard JDBC-verbinding van Spark naar Hive.

:::image type="content" source="./media/apache-hive-warehouse-connector/hive-warehouse-connector-architecture.png" alt-text="architectuur van de Hive-Warehouse connector" border="true":::

Enkele van de bewerkingen die worden ondersteund door de Hive Warehouse connector zijn:

* Een tabel beschrijven
* Een tabel maken voor gegevens in de ORC-indeling
* Hive-gegevens selecteren en een Data Frame ophalen
* Een data frame naar een Hive schrijven in batch
* Een update-instructie van een Hive uitvoeren
* Tabel gegevens lezen uit Hive, deze transformeren in Spark en schrijven naar een nieuwe Hive-tabel
* Een data frame-of Spark-stroom naar een Hive schrijven met behulp van HiveStreaming

## <a name="hive-warehouse-connector-setup"></a>Setup van Hive Warehouse-connector

> [!IMPORTANT]
> - De HiveServer2 Interactive instance die is geïnstalleerd op Spark 2,4 Enterprise Security Package-clusters wordt niet ondersteund voor gebruik met de Hive Warehouse-connector. In plaats daarvan moet u een afzonderlijk HiveServer2 interactief cluster configureren om uw HiveServer2 Interactive-workloads te hosten. Een component Warehouse-connector configuratie die gebruikmaakt van één Spark 2,4-cluster wordt niet ondersteund.
> - HWC-bibliotheek (component Warehouse connector) wordt niet ondersteund voor gebruik met interactieve query clusters waarop de functie workload Management (WLM) is ingeschakeld. <br>
In een scenario waarin u alleen Spark-workloads hebt en HWC-bibliotheek wilt gebruiken, zorgt u ervoor dat voor het interactieve query cluster niet de functie workload Management is ingeschakeld ( `hive.server2.tez.interactive.queue` configuratie is niet ingesteld in Hive-configuraties). <br>
Voor een scenario waarbij zowel Spark-workloads (HWC) als LLAP systeem eigen werk belastingen bestaan, moet u twee afzonderlijke interactieve query clusters maken met de gedeelde meta base van het gegevens archief. Eén cluster voor systeem eigen LLAP-werk belastingen waarbij WLM functie kan worden ingeschakeld op basis van nood zaak en ander cluster voor HWC alleen werk belasting waarbij WLM-functie niet mag worden geconfigureerd.
Het is belang rijk te weten dat u de WLM-resource plannen van beide clusters kunt bekijken, zelfs als deze is ingeschakeld in slechts één cluster. Breng geen wijzigingen aan in resource plannen in het cluster waarin de functie WLM is uitgeschakeld, omdat dit van invloed kan zijn op de WLM-functionaliteit in een ander cluster.

Hive Warehouse connector heeft afzonderlijke clusters nodig voor Spark-en interactieve query-workloads. Volg deze stappen om deze clusters in te stellen in azure HDInsight.

### <a name="create-clusters"></a>Clusters maken

1. Een HDInsight Spark **4,0** -cluster maken met een opslag account en een aangepast virtueel Azure-netwerk. Zie [HDInsight toevoegen aan een bestaand virtueel netwerk](../../hdinsight/hdinsight-plan-virtual-network-deployment.md#existingvnet)voor meer informatie over het maken van een cluster in een virtueel Azure-netwerk.

1. Maak een HDInsight Interactive query (LLAP) **4,0** -cluster met hetzelfde opslag account en een virtueel Azure-netwerk als het Spark-cluster.

### <a name="configure-hwc-settings"></a>HWC-instellingen configureren

#### <a name="gather-preliminary-information"></a>Voorlopige gegevens verzamelen

1. Navigeer vanuit een webbrowser naar `https://LLAPCLUSTERNAME.azurehdinsight.net/#/main/services/HIVE` waar LLAPCLUSTERNAME de naam is van uw interactieve query cluster.

1. Navigeer naar **Summary**  >  **HiveServer2 Interactive JDBC URL** en noteer de waarde. De waarde kan er ongeveer als volgt uitzien: `jdbc:hive2://zk0-iqgiro.rekufuk2y2ce.bx.internal.cloudapp.net:2181,zk1-iqgiro.rekufuk2y2ce.bx.internal.cloudapp.net:2181,zk4-iqgiro.rekufuk2y2ce.bx.internal.cloudapp.net:2181/;serviceDiscoveryMode=zooKeeper;zooKeeperNamespace=hiveserver2-interactive` .

1. Ga naar **configuratie**  >  **Geavanceerde** geavanceerde  >  **componenten-site**  >  **component. Zookeeper. quorum** en noteer de waarde. De waarde kan er ongeveer als volgt uitzien: `zk0-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:2181,zk1-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:2181,zk4-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:2181` .

1. Navigeer naar **configurations**  >  **Advanced**  >  **General**  >  **component. meta Store. uri's** en noteer de waarde. De waarde kan er ongeveer als volgt uitzien: `thrift://iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:9083,thrift://hn1-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:9083` .

1. Navigeer naar **configuratie** van  >  **Geavanceerde**  >  **Geavanceerde component-Interactive-site**  >  **Hive. llap. daemon. service. hosts** en noteer de waarde. De waarde kan er ongeveer als volgt uitzien: `@llap0` .

#### <a name="configure-spark-cluster-settings"></a>Spark-cluster instellingen configureren

1. Navigeer vanuit een webbrowser naar `https://CLUSTERNAME.azurehdinsight.net/#/main/services/SPARK2/configs` de naam van het Apache Spark cluster.

1. Vouw **aangepaste spark2-defaults**.

    :::image type="content" source="./media/apache-hive-warehouse-connector/hive-warehouse-connector-spark2-ambari.png" alt-text="Configuratie van Apache Ambari Spark2" border="true":::

1. Selecteer **eigenschap toevoegen...** om de volgende configuraties toe te voegen:

    | Configuratie | Waarde |
    |----|----|
    |`spark.datasource.hive.warehouse.load.staging.dir`|`wasbs://STORAGE_CONTAINER_NAME@STORAGE_ACCOUNT_NAME.blob.core.windows.net/tmp`. <br> Stel in op een geschikte, met HDFS compatibele tijdelijke directory. Als u twee verschillende clusters hebt, moet de map voor gefaseerde installatie een map zijn in de map met tijdelijke bestanden van het opslag account van het LLAP-cluster, zodat HiveServer2 er toegang toe heeft.  Vervang door de `STORAGE_ACCOUNT_NAME` naam van het opslag account dat wordt gebruikt door het cluster en `STORAGE_CONTAINER_NAME` met de naam van de opslag container. |
    |`spark.sql.hive.hiveserver2.jdbc.url`| De waarde die u eerder hebt verkregen van de **HiveServer2 Interactive JDBC-URL** |
    |`spark.datasource.hive.warehouse.metastoreUri`| De waarde die u eerder hebt verkregen uit **Hive. meta Store. uri's**. |
    |`spark.security.credentials.hiveserver2.enabled`|`true` voor de cluster modus garen en `false` voor de modus garens van de client. |
    |`spark.hadoop.hive.zookeeper.quorum`| De waarde die u eerder hebt verkregen van **Hive. Zookeeper. quorum**. |
    |`spark.hadoop.hive.llap.daemon.service.hosts`| De waarde die u eerder hebt verkregen vanuit **Hive. llap. daemon. service. hosts**. |

1. Sla de wijzigingen op en start alle betrokken onderdelen opnieuw op.

### <a name="configure-hwc-for-enterprise-security-package-esp-clusters"></a>HWC voor Enterprise Security Package (ESP)-clusters configureren

De Enterprise Security Package (ESP) biedt mogelijkheden op ondernemings niveau zoals op Active Directory gebaseerde verificatie, ondersteuning voor meerdere gebruikers en toegangs beheer op basis van rollen voor Apache Hadoop clusters in azure HDInsight. Zie [Enterprise Security package gebruiken in HDInsight](../domain-joined/apache-domain-joined-architecture.md)voor meer informatie over ESP.

Naast de configuraties die in de vorige sectie worden genoemd, voegt u de volgende configuratie toe om HWC te gebruiken op de ESP-clusters.

1. Ga in Ambari-webgebruikersinterface van Spark-cluster naar **Spark2**  >  **configs**  >  **Custom Spark2-defaults**.

1. Werk de volgende eigenschap bij.

    | Configuratie | Waarde |
    |----|----|
    | `spark.sql.hive.hiveserver2.jdbc.url.principal`    | `hive/<llap-headnode>@<AAD-Domain>` |
    
    * Navigeer vanuit een webbrowser naar `https://CLUSTERNAME.azurehdinsight.net/#/main/services/HIVE/summary` de naam van het interactieve query cluster. Klik op **HiveServer2 Interactive**. U ziet de FQDN-naam (Fully Qualified Domain Name) van het hoofd knooppunt waarop LLAP wordt uitgevoerd, zoals wordt weer gegeven in de scherm opname. Vervang door `<llap-headnode>` deze waarde.

        :::image type="content" source="./media/apache-hive-warehouse-connector/head-node-hive-server-interactive.png" alt-text="Hoofd knooppunt van de component Warehouse-connector" border="true":::

    * Gebruik de [SSH-opdracht](../hdinsight-hadoop-linux-use-ssh-unix.md) om verbinding te maken met uw interactieve query cluster. Zoek naar `default_realm` de para meter in het `/etc/krb5.conf` bestand. Vervang door `<AAD-DOMAIN>` deze waarde als een hoofd letter teken reeks, anders wordt de referentie niet gevonden.

        :::image type="content" source="./media/apache-hive-warehouse-connector/aad-domain.png" alt-text="AAD-domein van Hive Warehouse connector" border="true":::

    * Bijvoorbeeld `hive/hn0-ng36ll.mjry42ikpruuxgs2qy2kpg4q5e.cx.internal.cloudapp.net@PKRSRVUQVMAE6J85.D2.INTERNAL.CLOUDAPP.NET`.
    
1. Sla de wijzigingen op en start de onderdelen indien nodig opnieuw op.

## <a name="hive-warehouse-connector-usage"></a>Gebruik van het Hive-magazijn connector

U kunt kiezen uit een aantal verschillende methoden om verbinding te maken met uw interactieve query cluster en query's uit te voeren met de Hive-Warehouse connector. Ondersteunde methoden zijn onder andere de volgende hulpprogram ma's:

* [Spark-shell/PySpark](../spark/apache-spark-shell.md)
* [Spark-verzenden](#spark-submit)
* [Zeppelin](./apache-hive-warehouse-connector-zeppelin.md)


Hieronder ziet u enkele voor beelden om verbinding te maken met HWC vanuit Spark.

### <a name="spark-shell"></a>Spark-shell

1. Gebruik de [SSH-opdracht](../hdinsight-hadoop-linux-use-ssh-unix.md) om verbinding te maken met uw Apache Spark cluster. Bewerk de onderstaande opdracht door CLUSTERNAME te vervangen door de naam van uw cluster. Voer vervolgens deze opdracht in:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Voer vanuit uw SSH-sessie de volgende opdracht uit om de `hive-warehouse-connector-assembly` versie te noteren:

    ```bash
    ls /usr/hdp/current/hive_warehouse_connector
    ```

1. Bewerk de onderstaande code met de `hive-warehouse-connector-assembly` hierboven aangegeven versie. Voer vervolgens de opdracht uit om de Spark-shell te starten:

    ```bash
    spark-shell --master yarn \
    --jars /usr/hdp/current/hive_warehouse_connector/hive-warehouse-connector-assembly-<VERSION>.jar \
    --conf spark.security.credentials.hiveserver2.enabled=false
    ```

1. Nadat u de Spark-shell hebt gestart, kunt u een component Warehouse connector-exemplaar starten met behulp van de volgende opdrachten:

    ```scala
    import com.hortonworks.hwc.HiveWarehouseSession
    val hive = HiveWarehouseSession.session(spark).build()
    ```

### <a name="spark-submit"></a>Spark-verzenden

Nadat u de scala/Java-code samen met de afhankelijkheden in een assembly jar hebt gemaakt, gebruikt u de onderstaande opdracht om een Spark-toepassing te starten. Vervang `<VERSION>` en `<APP_JAR_PATH>` door de werkelijke waarden.

* Client modus garen
    
    ```scala
    spark-submit \
    --class myHwcApp \
    --master yarn \
    --deploy-mode client \
    --jars /usr/hdp/current/hive_warehouse_connector/hive-warehouse-connector-assembly-<VERSION>.jar \
    --conf spark.security.credentials.hiveserver2.enabled=false
    /<APP_JAR_PATH>/myHwcAppProject.jar
    ```

* De cluster modus garen
    ```scala
    spark-submit \
    --class myHwcApp \
    --master yarn \
    --deploy-mode cluster \
    --jars /usr/hdp/current/hive_warehouse_connector/hive-warehouse-connector-assembly-<VERSION>.jar \
    --conf spark.security.credentials.hiveserver2.enabled=true
    /<APP_JAR_PATH>/myHwcAppProject.jar
    ```

Voor python voegt u ook de volgende configuratie toe. 

```python
--py-files /usr/hdp/current/hive_warehouse_connector/pyspark_hwc-<VERSION>.zip
```
    
## <a name="run-queries-on-enterprise-security-package-esp-clusters"></a>Query's uitvoeren op Enterprise Security Package (ESP)-clusters

Gebruik `kinit` voordat u de Spark-shell of Spark verzendt. Vervang de gebruikers naam door de naam van een domein account met machtigingen voor toegang tot het cluster en voer de volgende opdracht uit:

```bash
kinit USERNAME
```

### <a name="securing-data-on-spark-esp-clusters"></a>Gegevens beveiligen op Spark ESP-clusters

1. `demo`Voer de volgende opdrachten in om een tabel met enkele voorbeeld gegevens te maken:

    ```scala
    create table demo (name string);
    INSERT INTO demo VALUES ('HDinsight');
    INSERT INTO demo VALUES ('Microsoft');
    INSERT INTO demo VALUES ('InteractiveQuery');
    ```

1. Bekijk de inhoud van de tabel met de volgende opdracht. Voordat u het beleid toepast, `demo` wordt in de tabel de volledige kolom weer gegeven.

    ```scala
    hive.executeQuery("SELECT * FROM demo").show()
    ```

    :::image type="content" source="./media/apache-hive-warehouse-connector/hive-warehouse-connector-table-before-ranger-policy.png" alt-text="demo tabel vóór het Toep assen van het beleid voor Zwerver" border="true":::

1. Pas een kolom maskerings beleid toe dat alleen de laatste vier tekens van de kolom bevat.  
    1. Ga naar de gebruikers interface van zwerver op `https://LLAPCLUSTERNAME.azurehdinsight.net/ranger/` .
    1. Klik onder **Hive** op de component service voor uw cluster.
        :::image type="content" source="./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-service-manager.png" alt-text="zwerver Service Manager" border="true":::
    1. Klik op het tabblad **maskeren** en vervolgens op **Nieuw beleid toevoegen**

        :::image type="content" source="./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-hive-policy-list.png" alt-text="Hive-beleids lijst van de component Warehouse connector zwerver" border="true":::

    1. Geef een gewenste beleids naam op. Data base selecteren: **standaard**, Hive-tabel: **demo**, Hive-kolom: **naam**, gebruiker: **rsadmin2**, toegangs typen: **selecteren**, en **gedeeltelijk masker: weer geven laatste 4** van het menu **optie masker selecteren** . Klik op **Add**.
                :::image type="content" source="./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-create-policy.png" alt-text="beleid maken" border="true":::
1. Bekijk de inhoud van de tabel opnieuw. Na het Toep assen van het beleid voor zwerver, kunnen we alleen de laatste vier tekens van de kolom zien.

    :::image type="content" source="./media/apache-hive-warehouse-connector/hive-warehouse-connector-table-after-ranger-policy.png" alt-text="demo tabel na toepassing van het beleid van zwerver" border="true":::

## <a name="next-steps"></a>Volgende stappen

* [HWC- en Apache Spark-bewerkingen](./apache-hive-warehouse-connector-operations.md)
* [Interactieve query gebruiken met HDInsight](./apache-interactive-query-get-started.md)
* [HWC-integratie met Apache Zeppelin](./apache-hive-warehouse-connector-zeppelin.md)
* [Voor beelden van interactie met hive Warehouse connector met Zeppelin, livy, Spark-Submit en pyspark](https://community.hortonworks.com/articles/223626/integrating-apache-hive-with-apache-spark-hive-war.html)
