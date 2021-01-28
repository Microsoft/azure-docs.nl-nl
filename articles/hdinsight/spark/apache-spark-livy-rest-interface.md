---
title: Livy Spark gebruiken voor het verzenden van taken naar Spark-cluster in azure HDInsight
description: Meer informatie over het gebruik van Apache Spark REST API om Spark-taken extern naar een Azure HDInsight-cluster te verzenden.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 02/28/2020
ms.openlocfilehash: ff63f4fbadd7cb9e7584e2aa045583a35e0363fd
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98930129"
---
# <a name="use-apache-spark-rest-api-to-submit-remote-jobs-to-an-hdinsight-spark-cluster"></a>Apache Spark REST API gebruiken voor het verzenden van externe taken naar een HDInsight Spark-cluster

Meer informatie over het gebruik van [Apache livy](https://livy.incubator.apache.org/), het Apache Spark rest API, dat wordt gebruikt om externe taken naar een Azure HDInsight Spark cluster te verzenden. Zie voor gedetailleerde documentatie [Apache livy](https://livy.incubator.apache.org/docs/latest/rest-api.html).

U kunt livy gebruiken om interactieve Spark-shells uit te voeren of om batch taken te verzenden die moeten worden uitgevoerd op Spark. In dit artikel vindt u informatie over het gebruik van livy voor het indienen van batch taken. De fragmenten in dit artikel gebruiken krul om REST API aanroepen naar het livy Spark-eind punt te maken.

## <a name="prerequisites"></a>Vereisten

Een Apache Spark-cluster in HDInsight. Zie [Apache Spark-clusters maken in Azure HDInsight](apache-spark-jupyter-spark-sql.md) voor instructies.

## <a name="submit-an-apache-livy-spark-batch-job"></a>Een Apache livy Spark-batch taak verzenden

Voordat u een batch-taak indient, moet u het toepassings jar uploaden naar de cluster opslag die aan het cluster is gekoppeld. Dit kan met [AzCopy](../../storage/common/storage-use-azcopy-v10.md), een opdrachtregelprogramma. Er zijn verschillende andere clients die u kunt gebruiken om gegevens te uploaden. Meer informatie hierover vindt u in [Gegevens voor Apache Hadoop-taken uploaden in HDInsight](../hdinsight-upload-data.md).

```cmd
curl -k --user "admin:password" -v -H "Content-Type: application/json" -X POST -d '{ "file":"<path to application jar>", "className":"<classname in jar>" }' 'https://<spark_cluster_name>.azurehdinsight.net/livy/batches' -H "X-Requested-By: admin"
```

### <a name="examples"></a>Voorbeelden

* Als het jar-bestand zich op de cluster opslag bevindt (WASBS)

    ```cmd  
    curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST -d '{ "file":"wasbs://mycontainer@mystorageaccount.blob.core.windows.net/data/SparkSimpleTest.jar", "className":"com.microsoft.spark.test.SimpleFile" }' "https://mysparkcluster.azurehdinsight.net/livy/batches" -H "X-Requested-By: admin"
    ```

* Als u de jar-bestands naam en de className wilt door geven als onderdeel van een invoer bestand (in dit voor beeld input.txt)

    ```cmd
    curl -k  --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\input.txt "https://mysparkcluster.azurehdinsight.net/livy/batches" -H "X-Requested-By: admin"
    ```

## <a name="get-information-on-livy-spark-batches-running-on-the-cluster"></a>Informatie ophalen over livy Spark-batches die worden uitgevoerd op het cluster

Syntaxis:

```cmd
curl -k --user "admin:password" -v -X GET "https://<spark_cluster_name>.azurehdinsight.net/livy/batches"
```

### <a name="examples"></a>Voorbeelden

* Als u alle livy Spark-batches wilt ophalen die worden uitgevoerd op het cluster:

    ```cmd
    curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches"
    ```

* Als u een specifieke batch met een opgegeven batch-ID wilt ophalen

    ```cmd
    curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches/{batchId}"
    ```

## <a name="delete-a-livy-spark-batch-job"></a>Een livy Spark-batch taak verwijderen

```cmd
curl -k --user "admin:mypassword1!" -v -X DELETE "https://<spark_cluster_name>.azurehdinsight.net/livy/batches/{batchId}"
```

### <a name="example"></a>Voorbeeld

Een batch-taak met batch-ID verwijderen `5` .

```cmd
curl -k --user "admin:mypassword1!" -v -X DELETE "https://mysparkcluster.azurehdinsight.net/livy/batches/5"
```

## <a name="livy-spark-and-high-availability"></a>Livy Spark en hoge Beschik baarheid

Livy biedt hoge Beschik baarheid voor Spark-taken die worden uitgevoerd op het cluster. Hier volgen enkele voor beelden.

* Als de livy-service uitvalt nadat u een taak op afstand hebt verzonden naar een Spark-cluster, blijft de taak actief op de achtergrond. Wanneer livy een back-up maakt, wordt de status van de taak hersteld en wordt deze weer gemeld.
* Jupyter-notebooks voor HDInsight worden aangedreven door livy in de back-end. Als een notebook een Spark-taak uitvoert en de livy-service opnieuw wordt gestart, blijft het notitie blok de code cellen uitvoeren.

## <a name="show-me-an-example"></a>Een voor beeld weer geven

In deze sectie kijken we naar voor beelden voor het gebruik van livy Spark om een batch-taak te verzenden, de voortgang van de taak te controleren en deze vervolgens te verwijderen. De toepassing die in dit voor beeld wordt gebruikt, is het in het artikel ontwikkelde [een zelfstandige scala-toepassing en voor het uitvoeren van een HDInsight Spark-cluster](apache-spark-create-standalone-application.md). Bij de volgende stappen wordt ervan uitgegaan:

* U hebt al over het toepassings jar gekopieerd naar het opslag account dat is gekoppeld aan het cluster.
* U hebt een krul geïnstalleerd op de computer waarop u deze stappen uitvoert.

Voer de volgende stappen uit:

1. Stel omgevings variabelen in om gebruiks gemak te gebruiken. Dit voor beeld is gebaseerd op een Windows-omgeving en wijzigt indien nodig de variabelen voor uw omgeving. Vervang `CLUSTERNAME` en `PASSWORD` door de juiste waarden.

    ```cmd
    set clustername=CLUSTERNAME
    set password=PASSWORD
    ```

1. Controleer of livy Spark op het cluster wordt uitgevoerd. We kunnen dit doen door een lijst met actieve batches op te halen. Als u een taak uitvoert met behulp van livy voor de eerste keer, moet de uitvoer nul retour neren.

    ```cmd
    curl -k --user "admin:%password%" -v -X GET "https://%clustername%.azurehdinsight.net/livy/batches"
    ```

    U moet een uitvoer zien die vergelijkbaar is met het volgende code fragment:

    ```output
    < HTTP/1.1 200 OK
    < Content-Type: application/json; charset=UTF-8
    < Server: Microsoft-IIS/8.5
    < X-Powered-By: ARR/2.5
    < X-Powered-By: ASP.NET
    < Date: Fri, 20 Nov 2015 23:47:53 GMT
    < Content-Length: 34
    <
    {"from":0,"total":0,"sessions":[]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact
    ```

    U ziet dat in de laatste regel van de uitvoer het volgende wordt vermeld **: 0**, waarmee wordt aangegeven dat er geen actieve batches worden uitgevoerd.

1. Laat ons nu een batch-taak indienen. In het volgende code fragment wordt een invoer bestand (input.txt) gebruikt voor het door geven van de JAR-naam en de naam van de klasse als para meters. Als u deze stappen uitvoert vanaf een Windows-computer, is het gebruik van een invoer bestand de aanbevolen methode.

    ```cmd
    curl -k --user "admin:%password%" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\input.txt "https://%clustername%.azurehdinsight.net/livy/batches" -H "X-Requested-By: admin"
    ```

    De para meters in het bestand **input.txt** worden als volgt gedefinieerd:

    ```text
    { "file":"wasbs:///example/jars/SparkSimpleApp.jar", "className":"com.microsoft.spark.example.WasbIOTest" }
    ```

    Als het goed is, wordt ongeveer het volgende codefragment weergegeven:

    ```output
    < HTTP/1.1 201 Created
    < Content-Type: application/json; charset=UTF-8
    < Location: /0
    < Server: Microsoft-IIS/8.5
    < X-Powered-By: ARR/2.5
    < X-Powered-By: ASP.NET
    < Date: Fri, 20 Nov 2015 23:51:30 GMT
    < Content-Length: 36
    <
    {"id":0,"state":"starting","log":[]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact
    ```

    U ziet dat de laatste regel van de uitvoer **staat voor de status: wordt gestart**. Het bericht bevat ook **id: 0**. Hier is **0** de batch-id.

1. U kunt nu de status van deze specifieke batch ophalen met behulp van de batch-ID.

    ```cmd
    curl -k --user "admin:%password%" -v -X GET "https://%clustername%.azurehdinsight.net/livy/batches/0"
    ```

    Als het goed is, wordt ongeveer het volgende codefragment weergegeven:

    ```output
    < HTTP/1.1 200 OK
    < Content-Type: application/json; charset=UTF-8
    < Server: Microsoft-IIS/8.5
    < X-Powered-By: ARR/2.5
    < X-Powered-By: ASP.NET
    < Date: Fri, 20 Nov 2015 23:54:42 GMT
    < Content-Length: 509
    <
    {"id":0,"state":"success","log":["\t diagnostics: N/A","\t ApplicationMaster host: 10.0.0.4","\t ApplicationMaster RPC port: 0","\t queue: default","\t start time: 1448063505350","\t final status: SUCCEEDED","\t tracking URL: http://myspar.lpel.jx.internal.cloudapp.net:8088/proxy/application_1447984474852_0002/","\t user: root","15/11/20 23:52:47 INFO Utils: Shutdown hook called","15/11/20 23:52:47 INFO Utils: Deleting directory /tmp/spark-b72cd2bf-280b-4c57-8ceb-9e3e69ac7d0c"]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact
    ```

    In de uitvoer wordt nu status weer gegeven **: geslaagd**, wat aangeeft dat de taak is voltooid.

1. Als u wilt, kunt u nu de batch verwijderen.

    ```cmd
    curl -k --user "admin:%password%" -v -X DELETE "https://%clustername%.azurehdinsight.net/livy/batches/0"
    ```

    Als het goed is, wordt ongeveer het volgende codefragment weergegeven:

    ```output
    < HTTP/1.1 200 OK
    < Content-Type: application/json; charset=UTF-8
    < Server: Microsoft-IIS/8.5
    < X-Powered-By: ARR/2.5
    < X-Powered-By: ASP.NET
    < Date: Sat, 21 Nov 2015 18:51:54 GMT
    < Content-Length: 17
    <
    {"msg":"deleted"}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact
    ```

    In de laatste regel van de uitvoer ziet u dat de batch is verwijderd. Als u een taak verwijdert terwijl deze wordt uitgevoerd, wordt de taak ook afbreken. Als u een taak verwijdert die is voltooid, of anderszins, wordt de taak informatie volledig verwijderd.

## <a name="updates-to-livy-configuration-starting-with-hdinsight-35-version"></a>Updates voor livy-configuratie vanaf HDInsight 3,5-versie

HDInsight 3,5-clusters en hoger: Schakel het gebruik van lokale bestands paden standaard uit voor toegang tot bestanden of potten met voorbeeld gegevens. We raden u aan om `wasbs://` in plaats daarvan het pad te gebruiken voor het openen van potten of voorbeeld gegevens bestanden van het cluster.

## <a name="submitting-livy-jobs-for-a-cluster-within-an-azure-virtual-network"></a>Livy-taken verzenden voor een cluster binnen een virtueel Azure-netwerk

Als u vanuit een Azure Virtual Network verbinding maakt met een HDInsight Spark-cluster, kunt u rechtstreeks verbinding maken met livy op het cluster. In een dergelijk geval is de URL voor livy-eind punt `http://<IP address of the headnode>:8998/batches` . Hier is **8998** de poort waarop livy wordt uitgevoerd op het cluster hoofd knooppunt. Zie [poorten die worden gebruikt door Apache Hadoop Services op HDInsight](../hdinsight-hadoop-port-settings-for-services.md)voor meer informatie over het openen van services op niet-open bare poorten.

## <a name="next-steps"></a>Volgende stappen

* [Documentatie voor Apache livy REST API](https://livy.incubator.apache.org/docs/latest/rest-api.html)
* [Resources beheren voor het Apache Spark-cluster in Azure HDInsight](apache-spark-resource-manager.md)
* [Taken die worden uitgevoerd in een Apache Spark-cluster in HDInsight, traceren en er fouten in oplossen](apache-spark-job-debugging.md)