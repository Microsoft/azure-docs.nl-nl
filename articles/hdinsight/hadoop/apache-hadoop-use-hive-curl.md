---
title: Apache Hadoop-Hive gebruiken met krul in HDInsight-Azure
description: Meer informatie over het op afstand verzenden van Apache Pig-taken naar Azure HDInsight met behulp van krul.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 01/06/2020
ms.openlocfilehash: 124661c57f779b9f8a639debcc093ed15e717694
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98946461"
---
# <a name="run-apache-hive-queries-with-apache-hadoop-in-hdinsight-using-rest"></a>Apache Hive query's uitvoeren met Apache Hadoop in HDInsight met behulp van REST

[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Meer informatie over het gebruik van de WebHCat REST API om Apache Hive query's uit te voeren met Apache Hadoop in azure HDInsight-cluster.

## <a name="prerequisites"></a>Vereisten

* Een Apache Hadoop cluster in HDInsight. Zie aan de [slag met HDInsight op Linux](./apache-hadoop-linux-tutorial-get-started.md).

* Een REST-client. In dit document wordt [invoke-WebRequest](/powershell/module/microsoft.powershell.utility/invoke-webrequest) op Windows Power shell gebruikt en wordt [gekruld](https://curl.haxx.se/) op [bash](/windows/wsl/install-win10).

* Als u bash gebruikt, hebt u ook JQ, een opdracht regel-JSON-processor nodig.  Zie [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/).

## <a name="base-uri-for-rest-api"></a>Basis-URI voor rest API

De basis-URI (Uniform Resource Identifier) voor de REST API op HDInsight is `https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME` , waarbij `CLUSTERNAME` de naam van uw cluster is.  Cluster namen in Uri's zijn **hoofdletter gevoelig**.  De naam van het cluster in het Fully Qualified Domain Name (FQDN) van de URI ( `CLUSTERNAME.azurehdinsight.net` ) is hoofdletter gevoelig en andere exemplaren in de URI zijn hoofdletter gevoelig.

## <a name="authentication"></a>Verificatie

Wanneer u een krul of andere REST-communicatie met WebHCat gebruikt, moet u de aanvragen verifiëren door de gebruikers naam en het wacht woord op te geven voor de HDInsight-cluster beheerder. De REST API is beveiligd via [basisverificatie](https://en.wikipedia.org/wiki/Basic_access_authentication). Om ervoor te zorgen dat uw referenties veilig worden verzonden naar de server, moet u altijd aanvragen doen met behulp van beveiligde HTTP (HTTPS).

### <a name="setup-preserve-credentials"></a>Setup (referenties behouden)

Bewaar uw referenties om te voor komen dat ze opnieuw worden ingevoerd voor elk voor beeld.  De naam van het cluster wordt in een afzonderlijke stap bewaard.

**A. bash**  
Bewerk het onderstaande script door `PASSWORD` uw eigen wacht woord te vervangen.  Voer vervolgens de opdracht in.

```bash
export password='PASSWORD'
```  

**B. Power shell** Voer de onderstaande code uit en geef uw referenties op in het pop-upvenster:

```powershell
$creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
```

### <a name="identify-correctly-cased-cluster-name"></a>Cluster naam met een onjuiste naam identificeren

De daadwerkelijke lettergrootte van de clusternaam kan anders zijn dan verwacht, afhankelijk van hoe het cluster is gemaakt.  In de stappen hier wordt het werkelijke hoofdletter gebruik weer gegeven en opgeslagen in een variabele voor alle latere voor beelden.

Bewerk de onderstaande scripts om deze te vervangen `CLUSTERNAME` door de naam van uw cluster. Voer vervolgens de opdracht in. (De cluster naam voor de FQDN is niet hoofdletter gevoelig.)

```bash
export clusterName=$(curl -u admin:$password -sS -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters" | jq -r '.items[].Clusters.cluster_name')
echo $clusterName
```  

```powershell
# Identify properly cased cluster name
$resp = Invoke-WebRequest -Uri "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters" `
    -Credential $creds -UseBasicParsing
$clusterName = (ConvertFrom-Json $resp.Content).items.Clusters.cluster_name;

# Show cluster name
$clusterName
```

## <a name="run-a-hive-query"></a>Een Hive-query uitvoeren

1. Gebruik een van de volgende opdrachten om te controleren of u verbinding kunt maken met uw HDInsight-cluster:

    ```bash
    curl -u admin:$password -G https://$clusterName.azurehdinsight.net/templeton/v1/status
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/status" `
       -Credential $creds `
       -UseBasicParsing
    $resp.Content
    ```

    U ontvangt een antwoord dat vergelijkbaar is met de volgende tekst:

    ```json
    {"status":"ok","version":"v1"}
    ```

    In deze opdracht worden de volgende parameters gebruikt:

    * `-u` -De gebruikers naam en het wacht woord die worden gebruikt om de aanvraag te verifiëren.
    * `-G` -Geeft aan dat deze aanvraag een GET-bewerking is.

1. Het begin van de URL, `https://$CLUSTERNAME.azurehdinsight.net/templeton/v1` is hetzelfde voor alle aanvragen. Het pad, `/status` , geeft aan dat de aanvraag de status WebHCat (ook wel bekend als Templeton) voor de server moet retour neren. U kunt ook de versie van Hive aanvragen met behulp van de volgende opdracht:

    ```bash
    curl -u admin:$password -G https://$clusterName.azurehdinsight.net/templeton/v1/version/hive
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/version/hive" `
       -Credential $creds `
       -UseBasicParsing
    $resp.Content
    ```

    Deze aanvraag retourneert een antwoord dat vergelijkbaar is met de volgende tekst:

    ```json
    {"module":"hive","version":"1.2.1000.2.6.5.3008-11"}
    ```

1. Gebruik het volgende om een tabel met de naam **log4jLogs** te maken:

    ```bash
    jobid=$(curl -s -u admin:$password -d user.name=admin -d execute="DROP+TABLE+log4jLogs;CREATE+EXTERNAL+TABLE+log4jLogs(t1+string,t2+string,t3+string,t4+string,t5+string,t6+string,t7+string)+ROW+FORMAT+DELIMITED+FIELDS+TERMINATED+BY+' '+STORED+AS+TEXTFILE+LOCATION+'/example/data/';SELECT+t4+AS+sev,COUNT(*)+AS+count+FROM+log4jLogs+WHERE+t4+=+'[ERROR]'+AND+INPUT__FILE__NAME+LIKE+'%25.log'+GROUP+BY+t4;" -d statusdir="/example/rest" https://$clusterName.azurehdinsight.net/templeton/v1/hive | jq -r .id)
    echo $jobid
    ```

    ```powershell
    $reqParams = @{"user.name"="admin";"execute"="DROP TABLE log4jLogs;CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED BY ' ' STORED AS TEXTFILE LOCATION '/example/data/;SELECT t4 AS sev,COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;";"statusdir"="/example/rest"}
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/hive" `
       -Credential $creds `
       -Body $reqParams `
       -Method POST `
       -UseBasicParsing
    $jobID = (ConvertFrom-Json $resp.Content).id
    $jobID
    ```

    Deze aanvraag gebruikt de POST-methode, waarmee gegevens worden verzonden als onderdeel van de aanvraag naar de REST API. De volgende gegevens waarden worden met de aanvraag verzonden:

     * `user.name` -De gebruiker die de opdracht uitvoert.
     * `execute` -De HiveQL-instructies die moeten worden uitgevoerd.
     * `statusdir` -De map waarnaar de status voor deze taak wordt geschreven.

   Met deze instructies worden de volgende acties uitgevoerd:

   * `DROP TABLE` -Als de tabel al bestaat, wordt deze verwijderd.
   * `CREATE EXTERNAL TABLE` -Maakt een nieuwe externe tabel in Hive. Externe tabellen slaan alleen de tabel definitie in Hive op. De gegevens blijven op de oorspronkelijke locatie.

     > [!NOTE]  
     > Externe tabellen moeten worden gebruikt wanneer u verwacht dat de onderliggende gegevens worden bijgewerkt door een externe bron. Bijvoorbeeld een geautomatiseerd proces voor het uploaden van gegevens of een andere MapReduce-bewerking.
     >
     > Als u een externe tabel verwijdert, worden de gegevens **niet** verwijderd, maar alleen de tabel definitie.

   * `ROW FORMAT` -Hoe de gegevens worden ingedeeld. De velden in elk logboek worden gescheiden door een spatie.
   * `STORED AS TEXTFILE LOCATION` -Waar de gegevens worden opgeslagen (het voor beeld/de gegevens Directory) en dat deze worden opgeslagen als tekst.
   * `SELECT` -Selecteert een telling van alle rijen waarin de kolom **T4** de waarde **[error]** bevat. Deze instructie retourneert de waarde **3** als er drie rijen met deze waarde zijn.

     > [!NOTE]  
     > U ziet dat de spaties tussen HiveQL-instructies worden vervangen door het `+` teken wanneer het wordt gebruikt met krul. Waarden die een spatie bevatten, zoals het scheidings teken, mogen niet worden vervangen door `+` .

      Met deze opdracht wordt een taak-ID geretourneerd die kan worden gebruikt om de status van de taak te controleren.

1. Als u de status van de taak wilt controleren, gebruikt u de volgende opdracht:

    ```bash
    curl -u admin:$password -d user.name=admin -G https://$clusterName.azurehdinsight.net/templeton/v1/jobs/$jobid | jq .status.state
    ```

    ```powershell
    $reqParams=@{"user.name"="admin"}
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/jobs/$jobID" `
       -Credential $creds `
       -Body $reqParams `
       -UseBasicParsing
    # ConvertFrom-JSON can't handle duplicate names with different case
    # So change one to prevent the error
    $fixDup=$resp.Content.Replace("jobID","job_ID")
    (ConvertFrom-Json $fixDup).status.state
    ```

    Als de taak is voltooid, is de status **geslaagd**.

1. Zodra de status van de taak is gewijzigd in **geslaagd**, kunt u de resultaten van de taak ophalen uit Azure Blob-opslag. De `statusdir` para meter die is door gegeven met de query bevat de locatie van het uitvoer bestand, in dit geval `/example/rest` . Dit adres slaat de uitvoer op in de `example/curl` map in de standaard opslag voor clusters.

    U kunt deze bestanden weer geven en downloaden met behulp van de [Azure cli](/cli/azure/install-azure-cli). Zie voor meer informatie over het gebruik van de Azure CLI met Azure Storage het document [Azure CLI gebruiken met Azure Storage](../../storage/blobs/storage-quickstart-blobs-cli.md) .

## <a name="next-steps"></a>Volgende stappen

Voor informatie over andere manieren om met Hadoop in HDInsight te werken:

* [Apache Hive gebruiken met Apache Hadoop op HDInsight](hdinsight-use-hive.md)
* [MapReduce gebruiken met Apache Hadoop op HDInsight](hdinsight-use-mapreduce.md)

Zie het [WebHCat-referentie](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference) document voor meer informatie over de rest API die in dit document worden gebruikt.