---
title: Gebruik MapReduce en krul met Apache Hadoop in HDInsight-Azure
description: Meer informatie over het extern uitvoeren van MapReduce-taken met Apache Hadoop op HDInsight met behulp van krul.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 01/13/2020
ms.openlocfilehash: e90dc2c7220caf5bd72b7086adc275934652e150
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98939689"
---
# <a name="run-mapreduce-jobs-with-apache-hadoop-on-hdinsight-using-rest"></a>MapReduce-taken uitvoeren met Apache Hadoop op HDInsight met behulp van REST

Meer informatie over het gebruik van de Apache Hive WebHCat REST API om MapReduce-taken uit te voeren op een Apache Hadoop in HDInsight-cluster. Krul wordt gebruikt om te laten zien hoe u kunt communiceren met HDInsight met behulp van onbewerkte HTTP-aanvragen om MapReduce-taken uit te voeren.

> [!NOTE]  
> Als u al bekend bent met het gebruik van op Linux gebaseerde Hadoop-servers, maar u geen ervaring hebt met HDInsight, raadpleegt u de [informatie die u nodig hebt om te weten te komen over op Linux gebaseerde Apache Hadoop in hdinsight-](../hdinsight-hadoop-linux-information.md) document.

## <a name="prerequisites"></a>Vereisten

* Een Apache Hadoop cluster in HDInsight. Zie [Apache Hadoop-clusters maken met behulp van Azure Portal](../hdinsight-hadoop-create-linux-clusters-portal.md).

Ofwel:
  * Windows Power shell of,
  * [Krul](https://curl.haxx.se/) met [JQ](https://stedolan.github.io/jq/)

## <a name="run-a-mapreduce-job"></a>Een MapReduce-taak uitvoeren

> [!NOTE]  
> Wanneer u een krul of andere REST-communicatie met WebHCat gebruikt, moet u de aanvragen verifiëren door de gebruikers naam en het wacht woord van de HDInsight-cluster beheerder op te geven. U moet de cluster naam gebruiken als onderdeel van de URI die wordt gebruikt om de aanvragen naar de server te verzenden.
>
> De REST API wordt beveiligd met behulp van [basis verificatie voor toegang](https://en.wikipedia.org/wiki/Basic_access_authentication). U moet altijd aanvragen indienen via HTTPS om ervoor te zorgen dat uw referenties veilig worden verzonden naar de server.

### <a name="curl"></a>Curl

1. Stel de variabelen hieronder in om gebruiks gemak te gebruiken. Dit voor beeld is gebaseerd op een Windows-omgeving en wordt zo nodig herzien voor uw omgeving.

    ```cmd
    set CLUSTERNAME=
    set PASSWORD=
    ```

1. Gebruik een opdrachtregel met de volgende opdracht om te controleren of u verbinding met uw HDInsight-cluster kunt maken:

    ```bash
    curl -u admin:%PASSWORD% -G https://%CLUSTERNAME%.azurehdinsight.net/templeton/v1/status
    ```

    In deze opdracht worden de volgende parameters gebruikt:

   * **-u**: geeft de gebruikers naam en het wacht woord aan die worden gebruikt om de aanvraag te verifiëren
   * **-G**: geeft aan dat deze bewerking een GET-aanvraag is

   Het begin van de URI, `https://CLUSTERNAME.azurehdinsight.net/templeton/v1` , is hetzelfde voor alle aanvragen.

    U ontvangt een antwoord dat vergelijkbaar is met de volgende JSON:

    ```json
    {"version":"v1","status":"ok"}
    ```

1. Als u een MapReduce-taak wilt verzenden, gebruikt u de volgende opdracht. Wijzig zo nodig het pad naar **JQ** .

    ```cmd
    curl -u admin:%PASSWORD% -d user.name=admin ^
    -d jar=/example/jars/hadoop-mapreduce-examples.jar ^
    -d class=wordcount -d arg=/example/data/gutenberg/davinci.txt -d arg=/example/data/output ^
    https://%CLUSTERNAME%.azurehdinsight.net/templeton/v1/mapreduce/jar | ^
    C:\HDI\jq-win64.exe .id
    ```

    Het einde van de URI (/MapReduce/jar) vertelt WebHCat dat deze aanvraag een MapReduce-taak start vanuit een klasse in een jar-bestand. In deze opdracht worden de volgende parameters gebruikt:

   * **-d**: wordt `-G` niet gebruikt, dus de aanvraag wordt standaard ingesteld op de methode post. `-d` Hiermee geeft u de gegevens waarden op die met de aanvraag worden verzonden.
     * **User.name**: de gebruiker die de opdracht uitvoert
     * **jar**: de locatie van het jar-bestand met de klasse die moet worden uitgevoerd
     * **klasse**: de klasse die de MapReduce Logic bevat
     * **ARG**: de argumenten die moeten worden door gegeven aan de MapReduce-taak. In dit geval het invoer tekst bestand en de map die worden gebruikt voor de uitvoer

    Met deze opdracht moet een taak-ID worden geretourneerd die kan worden gebruikt om de status van de taak te controleren: `job_1415651640909_0026` .

1. Gebruik de volgende opdracht om de status van de taak te controleren. Vervang de waarde voor `JOBID` door de **werkelijke** waarde die in de vorige stap is geretourneerd. Wijzig de locatie van **JQ** naar behoefte.

    ```cmd
    set JOBID=job_1415651640909_0026

    curl -G -u admin:%PASSWORD% -d user.name=admin https://%CLUSTERNAME%.azurehdinsight.net/templeton/v1/jobs/%JOBID% | ^
    C:\HDI\jq-win64.exe .status.state
    ```

### <a name="powershell"></a>PowerShell

1. Stel de variabelen hieronder in om gebruiks gemak te gebruiken. Vervang door `CLUSTERNAME` de werkelijke cluster naam. Voer de opdracht uit en geef het wacht woord voor de cluster aanmelding op wanneer u hierom wordt gevraagd.

    ```powershell
    $clusterName="CLUSTERNAME"
    $creds = Get-Credential -UserName admin -Message "Enter the cluster login password"
    ```

1. Gebruik de volgende opdracht om te controleren of u verbinding kunt maken met uw HDInsight-cluster:

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clustername.azurehdinsight.net/templeton/v1/status" `
        -Credential $creds `
        -UseBasicParsing
    $resp.Content
    ```

    U ontvangt een antwoord dat vergelijkbaar is met de volgende JSON:

    ```json
    {"version":"v1","status":"ok"}
    ```

1. Als u een MapReduce-taak wilt verzenden, gebruikt u de volgende opdracht:

    ```powershell
    $reqParams = @{}
    $reqParams."user.name" = "admin"
    $reqParams.jar = "/example/jars/hadoop-mapreduce-examples.jar"
    $reqParams.class = "wordcount"
    $reqParams.arg = @()
    $reqParams.arg += "/example/data/gutenberg/davinci.txt"
    $reqparams.arg += "/example/data/output"
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/mapreduce/jar" `
       -Credential $creds `
       -Body $reqParams `
       -Method POST `
       -UseBasicParsing
    $jobID = (ConvertFrom-Json $resp.Content).id
    $jobID
    ```

    Het einde van de URI (/MapReduce/jar) vertelt WebHCat dat deze aanvraag een MapReduce-taak start vanuit een klasse in een jar-bestand. In deze opdracht worden de volgende parameters gebruikt:

    * **User.name**: de gebruiker die de opdracht uitvoert
    * **jar**: de locatie van het jar-bestand met de klasse die moet worden uitgevoerd
    * **klasse**: de klasse die de MapReduce Logic bevat
    * **ARG**: de argumenten die moeten worden door gegeven aan de MapReduce-taak. In dit geval het invoer tekst bestand en de map die worden gebruikt voor de uitvoer

   Met deze opdracht moet een taak-ID worden geretourneerd die kan worden gebruikt om de status van de taak te controleren: `job_1415651640909_0026` .

1. Als u de status van de taak wilt controleren, gebruikt u de volgende opdracht:

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

### <a name="both-methods"></a>Beide methoden

1. Als de taak is voltooid, is de geretourneerde status `SUCCEEDED` .

1. Wanneer de status van de taak is gewijzigd in `SUCCEEDED` , kunt u de resultaten van de taak ophalen uit Azure Blob-opslag. De `statusdir` para meter die wordt door gegeven met de query, bevat de locatie van het uitvoer bestand. In dit voor beeld is de locatie `/example/curl` . Dit adres slaat de uitvoer van de taak op in de standaard opslag voor clusters op `/example/curl` .

U kunt deze bestanden weer geven en downloaden met behulp van de [Azure cli](/cli/azure/install-azure-cli). Zie [Quick Start: Create, down loads en List blobs with Azure cli](../../storage/blobs/storage-quickstart-blobs-cli.md)(Engelstalig) voor meer informatie over het gebruik van Azure CLI om te werken met Azure Blob-opslag.

## <a name="next-steps"></a>Volgende stappen

Voor informatie over andere manieren om met Hadoop in HDInsight te werken:

* [MapReduce gebruiken met Apache Hadoop op HDInsight](hdinsight-use-mapreduce.md)
* [Apache Hive gebruiken met Apache Hadoop op HDInsight](hdinsight-use-hive.md)

Zie de [WebHCat-verwijzing](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference)voor meer informatie over de rest-interface die in dit artikel wordt gebruikt.
