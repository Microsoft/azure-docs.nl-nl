---
title: Spark gebruiken om HBase-gegevens te lezen en te schrijven-Azure HDInsight
description: Gebruik de Spark HBase-connector om gegevens te lezen en te schrijven van een Spark-cluster naar een HBase-cluster.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 08/12/2020
ms.openlocfilehash: 344caf4080380f5d9dfdaf452798ada6d1dc9f1c
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98931219"
---
# <a name="use-apache-spark-to-read-and-write-apache-hbase-data"></a>Apache Spark gebruiken om Apache HBase-gegevens te lezen en schrijven

Apache HBase wordt doorgaans opgevraagd met de API op laag niveau (scans, haalt en plaatst) of met een SQL-syntaxis met Apache Phoenix. Apache biedt ook de Apache Spark HBase-connector. De connector is een handig en efficiënt alternatief voor het uitvoeren van query's en het wijzigen van gegevens die zijn opgeslagen door HBase.

## <a name="prerequisites"></a>Vereisten

* Twee afzonderlijke HDInsight-clusters die zijn geïmplementeerd in hetzelfde [virtuele netwerk](./hdinsight-plan-virtual-network-deployment.md). Eén HBase en een Spark met ten minste Spark 2,1 (HDInsight 3,6) geïnstalleerd. Zie op [Linux gebaseerde clusters in HDInsight maken met behulp van de Azure Portal](hdinsight-hadoop-create-linux-clusters-portal.md)voor meer informatie.

* Het URI-schema voor de primaire opslag voor uw clusters. Dit schema is wasb://voor Azure Blob Storage, `abfs://` voor Azure data Lake Storage Gen2 of ADL://voor Azure data Lake Storage gen1. Als beveiligde overdracht is ingeschakeld voor Blob Storage, zou de URI zijn `wasbs://` .  Zie ook [beveiligde overdracht](../storage/common/storage-require-secure-transfer.md).

## <a name="overall-process"></a>Algemeen proces

Het proces op hoog niveau om uw Spark-cluster in te scha kelen om uw HBase-cluster op te vragen, is als volgt:

1. Bereid enkele voorbeeld gegevens voor in HBase.
2. Haal het hbase-site.xml-bestand op uit uw HBase (/etc/hbase/conf) en plaats een kopie van hbase-site.xml in de map Spark 2-configuratie (/etc/spark2/conf). (Optioneel: script van HDInsight-team gebruiken om dit proces te automatiseren)
4. Voer `spark-shell` een verwijzing naar de Spark HBase-connector uit met de bijbehorende maven-coördinaten in de `packages` optie.
5. Definieer een catalogus die het schema toewijst van Spark in HBase.
6. Interactie met de HBase-gegevens met behulp van de RDD-of data frame-Api's.

## <a name="prepare-sample-data-in-apache-hbase"></a>Voorbeeld gegevens voorbereiden in Apache HBase

In deze stap maakt en vult u een tabel in Apache HBase op die u vervolgens kunt uitvoeren met behulp van Spark.

1. Gebruik de `ssh` opdracht om verbinding te maken met uw HBase-cluster. Bewerk de onderstaande opdracht door `HBASECLUSTER` de naam van uw HBase-cluster te vervangen en voer de opdracht in:

    ```cmd
    ssh sshuser@HBASECLUSTER-ssh.azurehdinsight.net
    ```

2. Gebruik de `hbase shell` opdracht om de HBase-interactieve shell te starten. Voer de volgende opdracht in uw SSH-verbinding in:

    ```bash
    hbase shell
    ```

3. Gebruik de `create` opdracht om een HBase-tabel met twee kolom families te maken. Voer de volgende opdracht in:

    ```hbase
    create 'Contacts', 'Personal', 'Office'
    ```

4. Gebruik de `put` opdracht om waarden in een opgegeven kolom in een opgegeven rij in een bepaalde tabel in te voegen. Voer de volgende opdracht in:

    ```hbase
    put 'Contacts', '1000', 'Personal:Name', 'John Dole'
    put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
    put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
    put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
    put 'Contacts', '8396', 'Personal:Name', 'Calvin Raji'
    put 'Contacts', '8396', 'Personal:Phone', '230-555-0191'
    put 'Contacts', '8396', 'Office:Phone', '230-555-0191'
    put 'Contacts', '8396', 'Office:Address', '5415 San Gabriel Dr.'
    ```

5. Gebruik de `exit` opdracht om de HBase-interactieve shell te stoppen. Voer de volgende opdracht in:

    ```hbase
    exit
    ```
    
## <a name="run-scripts-to-set-up-connection-between-clusters"></a>Scripts uitvoeren om verbinding tussen clusters in te stellen

Als u de communicatie tussen clusters wilt instellen, volgt u de onderstaande stappen om twee scripts op uw clusters uit te voeren. Deze scripts automatiseren het proces van het kopiëren van bestanden die worden beschreven in de onderstaande sectie ' zelf communicatie instellen '. 

* Met het script dat u vanuit het HBase-cluster uitvoert, worden `hbase-site.xml` IP-toewijzings gegevens geüpload en HBase naar de standaard opslag die aan uw Spark-cluster is gekoppeld. 
* Het script dat u vanuit het Spark-cluster uitvoert, stelt twee cron-taken in om periodiek twee hulp scripts uit te voeren:  
    1.  HBase cron-taak: down load nieuwe `hbase-site.xml` bestanden en HBase IP-toewijzing van het standaard opslag account Spark naar het lokale knoop punt
    2.  Taak Spark cron: controleert of een Spark is geschaald en of het cluster is beveiligd. Als dit het geval is, `/etc/hosts` gaat u naar HBase IP-toewijzing toevoegen die lokaal is opgeslagen

__Opmerking__: voordat u doorgaat, moet u ervoor zorgen dat u het opslag account van het Spark-cluster hebt toegevoegd aan uw HBase-cluster als secundair opslag account. Zorg ervoor dat u de scripts in de aangegeven volg orde ziet.


1. Gebruik [script actie](hdinsight-hadoop-customize-cluster-linux.md#script-action-to-a-running-cluster) op uw HBase-cluster om de wijzigingen toe te passen met de volgende overwegingen: 


    |Eigenschap | Waarde |
    |---|---|
    |Bash-script-URI|`https://hdiconfigactions.blob.core.windows.net/hbasesparkconnectorscript/connector-hbase.sh`|
    |Knooppunttype(n)|Regio|
    |Parameters|`-s SECONDARYS_STORAGE_URL`|
    |Persistente|ja|

    * `SECONDARYS_STORAGE_URL` is de URL van de standaard opslag met Spark-zijde. Parameter voorbeeld: `-s wasb://sparkcon-2020-08-03t18-17-37-853z@sparkconhdistorage.blob.core.windows.net`


2.  Gebruik script actie op uw Spark-cluster om de wijzigingen toe te passen met de volgende overwegingen:

    |Eigenschap | Waarde |
    |---|---|
    |Bash-script-URI|`https://hdiconfigactions.blob.core.windows.net/hbasesparkconnectorscript/connector-spark.sh`|
    |Knooppunttype(n)|Head, worker, Zookeeper|
    |Parameters|`-s "SPARK-CRON-SCHEDULE"` (optioneel) `-h "HBASE-CRON-SCHEDULE"` Beschrijving|
    |Persistente|ja|


    * U kunt opgeven hoe vaak dit cluster automatisch moet controleren of er een update is. Standaard:-s "*/1 * * * *"-h 0 (in dit voor beeld wordt de Spark-cron elke minuut uitgevoerd, terwijl de cron van HBase niet wordt uitgevoerd)
    * Omdat HBase cron niet standaard is ingesteld, moet u dit script opnieuw uitvoeren wanneer u het HBase-cluster kunt schalen. Als uw HBase-cluster regel matig wordt geschaald, kunt u ervoor kiezen om HBase cron-taak automatisch in te stellen. Bijvoorbeeld: `-h "*/30 * * * *"` Hiermee configureert u het script om elke 30 minuten controles uit te voeren. Hiermee wordt HBase cron schema regel matig uitgevoerd om het downloaden van nieuwe HBase-informatie over het algemene opslag account naar het lokale knoop punt te automatiseren.
    
    

## <a name="set-up-communication-manually-optional-if-provided-script-in-above-step-fails"></a>Communicatie hand matig instellen (optioneel, indien opgegeven script in de bovenstaande stap mislukt)

__Opmerking:__ Deze stappen moeten worden uitgevoerd telkens wanneer een van de clusters een schaal activiteit ondergaat.

1. Kopieer de hbase-site.xml van de lokale opslag naar de hoofdmap van de standaard opslag van uw Spark-cluster.  Bewerk de onderstaande opdracht om uw configuratie weer te geven.  Voer vervolgens vanuit uw open SSH-sessie naar het HBase-cluster de volgende opdracht in:

    | Syntaxis waarde | Nieuwe waarde|
    |---|---|
    |[URI-schema](hdinsight-hadoop-linux-information.md#URI-and-scheme) | Wijzig om uw opslag ruimte weer te geven.  De onderstaande syntaxis is voor Blob Storage waarvoor beveiligde overdracht is ingeschakeld.|
    |`SPARK_STORAGE_CONTAINER`|Vervang door de naam van de standaard opslag container die wordt gebruikt voor het Spark-cluster.|
    |`SPARK_STORAGE_ACCOUNT`|Vervang door de standaard naam van het opslag account dat wordt gebruikt voor het Spark-cluster.|

    ```bash
    hdfs dfs -copyFromLocal /etc/hbase/conf/hbase-site.xml wasbs://SPARK_STORAGE_CONTAINER@SPARK_STORAGE_ACCOUNT.blob.core.windows.net/
    ```

2. Sluit vervolgens uw SSH-verbinding met uw HBase-cluster af.

    ```bash
    exit
    ```


3. Maak met SSH verbinding met het hoofd knooppunt van uw Spark-cluster. Bewerk de onderstaande opdracht door `SPARKCLUSTER` de naam van uw Spark-cluster te vervangen en voer de volgende opdracht in:

    ```cmd
    ssh sshuser@SPARKCLUSTER-ssh.azurehdinsight.net
    ```

4. Voer de onderstaande opdracht in om de `hbase-site.xml` standaard opslag van uw Spark-cluster te kopiëren naar de map Spark 2-configuratie op de lokale opslag van het cluster:

    ```bash
    sudo hdfs dfs -copyToLocal /hbase-site.xml /etc/spark2/conf
    ```

## <a name="run-spark-shell-referencing-the-spark-hbase-connector"></a>Spark-shell uitvoeren die verwijst naar de Spark HBase-connector

Nadat u de vorige stap hebt voltooid, moet u Spark shell kunnen uitvoeren om te verwijzen naar de juiste versie van de Spark HBase-connector. Zie [SHC core-opslag plaats](https://repo.hortonworks.com/content/groups/public/com/hortonworks/shc/shc-core/)om de meest recente geschikte versie van de HBase-connector te vinden voor uw cluster scenario.

De volgende tabel bevat een voor beeld van twee versies en de bijbehorende opdrachten die het HDInsight-team momenteel gebruikt. U kunt dezelfde versies voor uw clusters gebruiken als de versies van HBase en Spark hetzelfde zijn als in de tabel. 


1. Voer in uw open SSH-sessie naar het Spark-cluster de volgende opdracht in om een Spark-shell te starten:

    |Spark-versie| HDI HBase-versie  | SHC-versie    |  Opdracht  |
    | :-----------:| :----------: | :-----------: |:----------- |
    |      2.1    | HDI 3,6 (HBase 1,1) | 1.1.1-2.1-s_2.11    | `spark-shell --packages com.hortonworks:shc-core:1.1.1-2.1-s_2.11 --repositories https://repo.hortonworks.com/content/groups/public/` |
    |      2.4    | HDI 4,0 (HBase 2,0) | 1.1.0.3.1.2.2-1  | `spark-shell --packages com.hortonworks.shc:shc-core:1.1.0.3.1.2.2-1 --repositories http://repo.hortonworks.com/content/groups/public/` |

2. Bewaar dit Spark shell-exemplaar open en ga door met [het definiëren van een catalogus en query](#define-a-catalog-and-query). Als u de potten die overeenkomen met uw versies in de SHC-kern opslagplaats niet vindt, gaat u verder met lezen. 

U kunt de potten rechtstreeks vanuit de GitHub-vertakking van de [Spark-hbase-connector](https://github.com/hortonworks-spark/shc) bouwen. Als u bijvoorbeeld met Spark 2,3 en HBase 1,1 werkt, voert u de volgende stappen uit:

1. Kloon de opslagplaats:

    ```bash
    git clone https://github.com/hortonworks-spark/shc
    ```
    
2. Ga naar vertakking-2,3:

    ```bash
    git checkout branch-2.3
    ```

3. Bouwen vanuit de vertakking (maakt een jar-bestand):

    ```bash
    mvn clean package -DskipTests
    ```
    
3. Voer de volgende opdracht uit (zorg ervoor dat u de naam van de jar wijzigt die overeenkomt met het jar-bestand dat u hebt gemaakt):

    ```bash
    spark-shell --jars <path to your jar>,/usr/hdp/current/hbase-client/lib/htrace-core-3.1.0-incubating.jar,/usr/hdp/current/hbase-client/lib/hbase-client.jar,/usr/hdp/current/hbase-client/lib/hbase-common.jar,/usr/hdp/current/hbase-client/lib/hbase-server.jar,/usr/hdp/current/hbase-client/lib/hbase-protocol.jar,/usr/hdp/current/hbase-client/lib/htrace-core-3.1.0-incubating.jar
    ```
    
4. Bewaar dit Spark shell-exemplaar open en ga door naar de volgende sectie. 



## <a name="define-a-catalog-and-query"></a>Een catalogus en query definiëren

In deze stap definieert u een catalogus object dat het schema toewijst van Apache Spark aan Apache HBase.  

1. Voer in uw open Spark-shell de volgende `import` instructies in:

    ```scala
    import org.apache.spark.sql.{SQLContext, _}
    import org.apache.spark.sql.execution.datasources.hbase._
    import org.apache.spark.{SparkConf, SparkContext}
    import spark.sqlContext.implicits._
    ```  

1. Voer de onderstaande opdracht in om een catalogus te definiëren voor de tabel contact personen die u hebt gemaakt in HBase:

    ```scala
    def catalog = s"""{
        |"table":{"namespace":"default", "name":"Contacts"},
        |"rowkey":"key",
        |"columns":{
        |"rowkey":{"cf":"rowkey", "col":"key", "type":"string"},
        |"officeAddress":{"cf":"Office", "col":"Address", "type":"string"},
        |"officePhone":{"cf":"Office", "col":"Phone", "type":"string"},
        |"personalName":{"cf":"Personal", "col":"Name", "type":"string"},
        |"personalPhone":{"cf":"Personal", "col":"Phone", "type":"string"}
        |}
    |}""".stripMargin
    ```

    De code:  

    1. Hiermee wordt een catalogus schema gedefinieerd voor de HBase-tabel met de naam `Contacts` .  
    1. Identificeert de rowkey als `key` en wijst de kolom namen die worden gebruikt in Spark toe aan het kolom familie, de kolom naam en het kolom Type zoals gebruikt in HBase.  
    1. Hiermee wordt de rowkey in detail gedefinieerd als een benoemde kolom ( `rowkey` ) met een specifieke kolom familie `cf` van `rowkey` .  

1. Voer de onderstaande opdracht in om een methode te definiëren die een data frame rond uw `Contacts` tabel in HBase biedt:

    ```scala
    def withCatalog(cat: String): DataFrame = {
        spark.sqlContext
        .read
        .options(Map(HBaseTableCatalog.tableCatalog->cat))
        .format("org.apache.spark.sql.execution.datasources.hbase")
        .load()
     }
    ```

1. Maak een instantie van de data frame:

    ```scala
    val df = withCatalog(catalog)
    ```  

1. Query uitvoeren op de data frame:

    ```scala
    df.show()
    ```

    U ziet nu twee rijen met gegevens:

    ```output
    +------+--------------------+--------------+-------------+--------------+
    |rowkey|       officeAddress|   officePhone| personalName| personalPhone|
    +------+--------------------+--------------+-------------+--------------+
    |  1000|1111 San Gabriel Dr.|1-425-000-0002|    John Dole|1-425-000-0001|
    |  8396|5415 San Gabriel Dr.|  230-555-0191|  Calvin Raji|  230-555-0191|
    +------+--------------------+--------------+-------------+--------------+
    ```

1. Registreer een tijdelijke tabel zodat u een query kunt uitvoeren op de HBase-tabel met behulp van Spark SQL:

    ```scala
    df.createTempView("contacts")
    ```

1. Een SQL-query voor de `contacts` tabel uitgeven:

    ```scala
    spark.sqlContext.sql("select personalName, officeAddress from contacts").show
    ```

    De resultaten moeten er als volgt uitzien:

    ```output
    +-------------+--------------------+
    | personalName|       officeAddress|
    +-------------+--------------------+
    |    John Dole|1111 San Gabriel Dr.|
    |  Calvin Raji|5415 San Gabriel Dr.|
    +-------------+--------------------+
    ```

## <a name="insert-new-data"></a>Nieuwe gegevens invoegen

1. Als u een nieuwe contactpersoonrecord wilt invoegen, definieert u een `ContactRecord` klasse:

    ```scala
    case class ContactRecord(
        rowkey: String,
        officeAddress: String,
        officePhone: String,
        personalName: String,
        personalPhone: String
        )
    ```

1. Maak een instantie van `ContactRecord` en plaats deze in een matrix:

    ```scala
    val newContact = ContactRecord("16891", "40 Ellis St.", "674-555-0110", "John Jackson","230-555-0194")

    var newData = new Array[ContactRecord](1)
    newData(0) = newContact
    ```

1. Sla de matrix van nieuwe gegevens op naar HBase:

    ```scala
    sc.parallelize(newData).toDF.write.options(Map(HBaseTableCatalog.tableCatalog -> catalog, HBaseTableCatalog.newTable -> "5")).format("org.apache.spark.sql.execution.datasources.hbase").save()
    ```

1. Bekijk de resultaten:

    ```scala  
    df.show()
    ```

    De uitvoer ziet er als volgt uit:

    ```output
    +------+--------------------+--------------+------------+--------------+
    |rowkey|       officeAddress|   officePhone|personalName| personalPhone|
    +------+--------------------+--------------+------------+--------------+
    |  1000|1111 San Gabriel Dr.|1-425-000-0002|   John Dole|1-425-000-0001|
    | 16891|        40 Ellis St.|  674-555-0110|John Jackson|  230-555-0194|
    |  8396|5415 San Gabriel Dr.|  230-555-0191| Calvin Raji|  230-555-0191|
    +------+--------------------+--------------+------------+--------------+
    ```

1. Sluit de Spark-shell door de volgende opdracht in te voeren:

    ```scala
    :q
    ```

## <a name="next-steps"></a>Volgende stappen

* [Apache Spark HBase-connector](https://github.com/hortonworks-spark/shc)
