---
title: 'Zelfstudie: R gebruiken in een Spark-compute-context in Azure HDInsight'
description: 'Zelfstudie: aan de slag met R en Spark op een Azure HDInsight Machine Learning Services-cluster.'
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 06/21/2019
ms.openlocfilehash: 07c004fa5f03c53ed0778613a0c16a0ba2aff11f
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104869494"
---
# <a name="tutorial-use-r-in-a-spark-compute-context-in-azure-hdinsight"></a>Zelfstudie: R gebruiken in een Spark-compute-context in Azure HDInsight

Deze zelfstudie biedt een stapsgewijze inleiding op het gebruik van de R-functies in Apache Spark die worden uitgevoerd op een Azure HDInsight Machine Learning Services-cluster.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * De voorbeeldgegevens downloaden naar lokale opslag
> * De gegevens naar de standaardopslag kopiëren
> * Een gegevensset instellen
> * Gegevensbronnen maken
> * Een compute-context voor Spark maken
> * Een lineair model passend maken
> * Samengestelde XDF-bestanden gebruiken
> * XDF naar CSV converteren

## <a name="prerequisites"></a>Vereisten

* Een Azure HDInsight Machine Learning Services-cluster. Ga naar [Apache Hadoop-clusters maken met behulp van Azure Portal](../hdinsight-hadoop-create-linux-clusters-portal.md) en selecteer **ML Services** als **Clustertype**.

## <a name="connect-to-rstudio-server"></a>Verbinding maken met RStudio Server

RStudio Server wordt uitgevoerd op het edge-knooppunt van het cluster. Ga naar de volgende site (waarbij *CLUSTERNAME* in de URL de naam is van het HDInsight Machine Learning Services-cluster dat u hebt gemaakt):

```
https://CLUSTERNAME.azurehdinsight.net/rstudio/
```

De eerste keer dat u zich aanmeldt, moet u tweemaal verifiëren. Bij de eerste verificatieprompt geeft u de gebruikers-id en het wachtwoord voor de clusterbeheerder op (de standaardwaarde is *admin*). Bij de tweede verificatieprompt geeft u de gebruikers-id en het wachtwoord voor SSH op (de standaardwaarde is *sshuser*). Voor volgende aanmeldingen zijn alleen de SSH-referenties vereist.

## <a name="download-the-sample-data-to-local-storage"></a>De voorbeeldgegevens downloaden naar lokale opslag

De *Airline 2012 On-Time Data Set* bestaat uit 12 door komma's gescheiden bestanden die de aankomst- en vertrekgegevens van alle commerciële vluchten binnen de VS voor het jaar 2012 bevatten. Deze gegevensset is groot, met meer dan 6 miljoen waarnemingen.

1. Initialiseer een aantal omgevingsvariabelen. Voer in de RStudio Server-console de volgende code in:

    ```R
    bigDataDirRoot <- "/tutorial/data" # root directory on cluster default storage
    localDir <- "/tmp/AirOnTimeCSV2012" # directory on edge node
    remoteDir <- "https://packages.revolutionanalytics.com/datasets/AirOnTimeCSV2012" # location of data
    ```

1. Selecteer in het rechterdeelvenster het tabblad **Environment** (Omgeving). De variabelen worden weer gegeven onder **Values** (Waarden).

    :::image type="content" source="./media/ml-services-tutorial-spark-compute/hdinsight-rstudio-image.png" alt-text="HDInsight RStudio-webconsole" border="true":::

1. Maak een lokale map en download de voorbeeldgegevens. Voer in RStudio de volgende code in:

    ```R
    # Create local directory
    dir.create(localDir)
    
    # Download data to the tmp folder(local)
    download.file(file.path(remoteDir, "airOT201201.csv"), file.path(localDir, "airOT201201.csv"))
    download.file(file.path(remoteDir, "airOT201202.csv"), file.path(localDir, "airOT201202.csv"))
    download.file(file.path(remoteDir, "airOT201203.csv"), file.path(localDir, "airOT201203.csv"))
    download.file(file.path(remoteDir, "airOT201204.csv"), file.path(localDir, "airOT201204.csv"))
    download.file(file.path(remoteDir, "airOT201205.csv"), file.path(localDir, "airOT201205.csv"))
    download.file(file.path(remoteDir, "airOT201206.csv"), file.path(localDir, "airOT201206.csv"))
    download.file(file.path(remoteDir, "airOT201207.csv"), file.path(localDir, "airOT201207.csv"))
    download.file(file.path(remoteDir, "airOT201208.csv"), file.path(localDir, "airOT201208.csv"))
    download.file(file.path(remoteDir, "airOT201209.csv"), file.path(localDir, "airOT201209.csv"))
    download.file(file.path(remoteDir, "airOT201210.csv"), file.path(localDir, "airOT201210.csv"))
    download.file(file.path(remoteDir, "airOT201211.csv"), file.path(localDir, "airOT201211.csv"))
    download.file(file.path(remoteDir, "airOT201212.csv"), file.path(localDir, "airOT201212.csv"))
    ```

    Het downloaden zou ongeveer 9,5 minuut moeten duren.

## <a name="copy-the-data-to-default-storage"></a>De gegevens naar de standaardopslag kopiëren

De locatie van Hadoop Distributed File System (HDFS) wordt opgegeven met de variabele `airDataDir`. Voer in RStudio de volgende code in:

```R
# Set directory in bigDataDirRoot to load the data into
airDataDir <- file.path(bigDataDirRoot,"AirOnTimeCSV2012")

# Create directory (default storage)
rxHadoopMakeDir(airDataDir)

# Copy data from local storage to default storage
rxHadoopCopyFromLocal(localDir, bigDataDirRoot)
    
# Optional. Verify files
rxHadoopListFiles(airDataDir)
```

Deze stap zou ongeveer 10 seconden moeten duren.

## <a name="set-up-a-dataset"></a>Een gegevensset instellen

1. Maak een bestandssysteemobject dat gebruikmaakt van de standaardwaarden. Voer in RStudio de volgende code in:

    ```R
    # Define the HDFS (WASB) file system
    hdfsFS <- RxHdfsFileSystem()
    ```

1. Omdat de oorspronkelijke CSV-bestanden nogal onhandige variabelenamen hebben, geeft u een *colInfo*-lijst op om ze beter te kunnen beheren. Voer in RStudio de volgende code in:

    ```R
    airlineColInfo <- list(
         MONTH = list(newName = "Month", type = "integer"),
        DAY_OF_WEEK = list(newName = "DayOfWeek", type = "factor",
            levels = as.character(1:7),
            newLevels = c("Mon", "Tues", "Wed", "Thur", "Fri", "Sat",
                          "Sun")),
        UNIQUE_CARRIER = list(newName = "UniqueCarrier", type =
                                "factor"),
        ORIGIN = list(newName = "Origin", type = "factor"),
        DEST = list(newName = "Dest", type = "factor"),
        CRS_DEP_TIME = list(newName = "CRSDepTime", type = "integer"),
        DEP_TIME = list(newName = "DepTime", type = "integer"),
        DEP_DELAY = list(newName = "DepDelay", type = "integer"),
        DEP_DELAY_NEW = list(newName = "DepDelayMinutes", type =
                             "integer"),
        DEP_DEL15 = list(newName = "DepDel15", type = "logical"),
        DEP_DELAY_GROUP = list(newName = "DepDelayGroups", type =
                               "factor",
           levels = as.character(-2:12),
           newLevels = c("< -15", "-15 to -1","0 to 14", "15 to 29",
                         "30 to 44", "45 to 59", "60 to 74",
                         "75 to 89", "90 to 104", "105 to 119",
                         "120 to 134", "135 to 149", "150 to 164",
                         "165 to 179", ">= 180")),
        ARR_DELAY = list(newName = "ArrDelay", type = "integer"),
        ARR_DELAY_NEW = list(newName = "ArrDelayMinutes", type =
                             "integer"),  
        ARR_DEL15 = list(newName = "ArrDel15", type = "logical"),
        AIR_TIME = list(newName = "AirTime", type =  "integer"),
        DISTANCE = list(newName = "Distance", type = "integer"),
        DISTANCE_GROUP = list(newName = "DistanceGroup", type =
                             "factor",
         levels = as.character(1:11),
         newLevels = c("< 250", "250-499", "500-749", "750-999",
             "1000-1249", "1250-1499", "1500-1749", "1750-1999",
             "2000-2249", "2250-2499", ">= 2500")))
    
    varNames <- names(airlineColInfo)
    ```

## <a name="create-data-sources"></a>Gegevensbronnen maken

In een Spark-compute-context kunt u gegevensbronnen maken met behulp van de volgende functies:

|Functie | Beschrijving |
|---------|-------------|
|`RxTextData` | Een door komma's gescheiden tekstgegevensbron. |
|`RxXdfData` | Gegevens in de XDF-gegevensbestandsindeling. In RevoScaleR wordt de XDF-bestandsindeling gewijzigd zodat Hadoop gegevens kan opslaan in een samengestelde set bestanden in plaats van één bestand. |
|`RxHiveData` | Hiermee wordt een Hive-gegevensbronobject gegenereerd.|
|`RxParquetData` | Hiermee wordt een Parquet-gegevensbronobject gegenereerd.|
|`RxOrcData` | Hiermee wordt een Orc-gegevensbronobject gegenereerd.|

Maak een [RxTextData](/machine-learning-server/r-reference/revoscaler/rxtextdata)-object met behulp van de bestanden die u naar HDFS hebt gekopieerd. Voer in RStudio de volgende code in:

```R
airDS <- RxTextData( airDataDir,
                        colInfo = airlineColInfo,
                        varsToKeep = varNames,
                        fileSystem = hdfsFS ) 
```

## <a name="create-a-compute-context-for-spark"></a>Een compute-context voor Spark maken

Stel de compute-context in uw script in op [RxSpark](/machine-learning-server/r-reference/revoscaler/rxspark) om gegevens te laden en analyses uit te voeren op werkknooppunten. In deze context distribueren R-functies automatisch de werkbelasting over alle werkknooppunten, zonder ingebouwde vereiste voor het beheren van taken of de wachtrij. De Spark-compute-context wordt tot stand gebracht met `RxSpark` of `rxSparkConnect()`, en gebruikt `rxSparkDisconnect()` om terug te keren naar een lokale compute-context. Voer in RStudio de volgende code in:

```R
# Define the Spark compute context
mySparkCluster <- RxSpark()

# Set the compute context
rxSetComputeContext(mySparkCluster)
```

## <a name="fit-a-linear-model"></a>Een lineair model passend maken

1. Gebruik de [rxLinMod](/machine-learning-server/r-reference/revoscaler/rxlinmod)-functie om een lineair model passend te maken met behulp van uw `airDS`-gegevensbron. Voer in RStudio de volgende code in:

    ```R
    system.time(
         delayArr <- rxLinMod(ArrDelay ~ DayOfWeek, data = airDS,
              cube = TRUE)
    )
    ```
    
    Deze stap zou twee tot drie minuten moeten duren.

1. Bekijk de resultaten. Voer in RStudio de volgende code in:

    ```R
    summary(delayArr)
    ```

    U ziet de volgende resultaten:

    ```output
    Call:
    rxLinMod(formula = ArrDelay ~ DayOfWeek, data = airDS, cube = TRUE)
    
    Cube Linear Regression Results for: ArrDelay ~ DayOfWeek
    Data: airDataXdf (RxXdfData Data Source)
    File name: /tutorial/data/AirOnTimeCSV2012
    Dependent variable(s): ArrDelay
    Total independent variables: 7 
    Number of valid observations: 6005381
    Number of missing observations: 91381 
     
    Coefficients:
                   Estimate Std. Error t value Pr(>|t|)     | Counts
    DayOfWeek=Mon   3.54210    0.03736   94.80 2.22e-16 *** | 901592
    DayOfWeek=Tues  1.80696    0.03835   47.12 2.22e-16 *** | 855805
    DayOfWeek=Wed   2.19424    0.03807   57.64 2.22e-16 *** | 868505
    DayOfWeek=Thur  4.65502    0.03757  123.90 2.22e-16 *** | 891674
    DayOfWeek=Fri   5.64402    0.03747  150.62 2.22e-16 *** | 896495
    DayOfWeek=Sat   0.91008    0.04144   21.96 2.22e-16 *** | 732944
    DayOfWeek=Sun   2.82780    0.03829   73.84 2.22e-16 *** | 858366
    ---
    Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1
    
    Residual standard error: 35.48 on 6005374 degrees of freedom
    Multiple R-squared: 0.001827 (as if intercept included)
    Adjusted R-squared: 0.001826 
    F-statistic:  1832 on 6 and 6005374 DF,  p-value: < 2.2e-16 
    Condition number: 1 
    ```

    De resultaten geven aan dat u alle gegevens hebt verwerkt, 6 miljoen waarnemingen, met behulp van alle CSV-bestanden in de opgegeven map. Omdat u `cube = TRUE` hebt opgegeven, hebt u een geschatte coëfficiënt voor elke dag van de week (en niet het snijpunt).

## <a name="use-composite-xdf-files"></a>Samengestelde XDF-bestanden gebruiken

Zoals u hebt gezien, kunt u CSV-bestanden rechtstreeks analyseren met R op Hadoop. U kunt de analyse echter sneller uitvoeren als u de gegevens in een efficiëntere indeling opslaat. De R XDF-bestandsindeling is efficiënt, maar is enigszins gewijzigd voor HDFS zodat afzonderlijke bestanden binnen één HDFS-blok blijven. (De blokgrootte van HDFS verschilt per installatie, maar is doorgaans 64 of 128 MB.) 

Wanneer u [rxImport](/machine-learning-server/r-reference/revoscaler/rximport) op Hadoop gebruikt om een set samengestelde XDF-bestanden te maken, geeft u een `RxTextData`-gegevensbron zoals `AirDS` op als inData-argument en een `RxXdfData`-gegevensbron met fileSystem ingesteld op een HDFS-bestandssysteem als outFile-argument. U kunt het `RxXdfData`-object vervolgens gebruiken als het gegevensargument in latere R-analyses.

1. Definieer een `RxXdfData`-object. Voer in RStudio de volgende code in:

    ```R
    airDataXdfDir <- file.path(bigDataDirRoot,"AirOnTimeXDF2012")
    
    airDataXdf <- RxXdfData( airDataXdfDir,
                            fileSystem = hdfsFS )
    ```

1. Stel een blokgrootte van 250000 rijen in en geef op dat alle gegevens moeten worden gelezen. Voer in RStudio de volgende code in:

    ```R
    blockSize <- 250000
    numRowsToRead = -1
    ```

1. Importeer de gegevens met behulp van `rxImport`. Voer in RStudio de volgende code in:

    ```R
    rxImport(inData = airDS,
             outFile = airDataXdf,
             rowsPerRead = blockSize,
             overwrite = TRUE,
             numRows = numRowsToRead )
    ```
    
    Deze stap zou enkele minuten moeten duren.

1. Schat hetzelfde lineaire model opnieuw met de nieuwe, snellere gegevensbron. Voer in RStudio de volgende code in:

    ```R
    system.time(
         delayArr <- rxLinMod(ArrDelay ~ DayOfWeek, data = airDataXdf,
              cube = TRUE)
    )
    ```
    
    Deze stap zou minder dan een minuut moeten kosten.

1. Bekijk de resultaten. De resultaten zouden hetzelfde moeten zijn als die van de CSV-bestanden. Voer in RStudio de volgende code in:

    ```R
    summary(delayArr)
    ```

## <a name="convert-xdf-to-csv"></a>XDF naar CSV converteren

### <a name="in-a-spark-context"></a>In een Spark-context

Als u uw CSV-bestanden hebt geconverteerd naar een XDF-bestandsindeling voor grotere efficiëntie tijdens het uitvoeren van de analyses, maar u de gegevens nu weer naar CSV wilt converteren, kunt u dit doen met behulp van [rxDataStep](/machine-learning-server/r-reference/revoscaler/rxdatastep).

Maak om een map met CSV-bestanden te maken eerst een `RxTextData`-object door een mapnaam te gebruiken als bestandsargument. Dit object vertegenwoordigt de map waarin de CSV-bestanden worden gemaakt. Deze map wordt gemaakt wanneer u de `rxDataStep` uitvoert. Verwijs vervolgens naar dit `RxTextData`-object in het `outFile`-argument van de `rxDataStep`. Elk CSV-bestand dat wordt gemaakt, krijgt een naam op basis van de mapnaam gevolgd door een getal.

Stel dat u een map met CSV-bestanden in HDFS wilt schrijven vanuit uw samengestelde `airDataXdf`-XDF nadat u de logistische regressie en voorspelling hebt uitgevoerd, zodat de nieuwe CSV-bestanden de voorspelde waarden en verschillen bevatten. Voer in RStudio de volgende code in:

```R
airDataCsvDir <- file.path(bigDataDirRoot,"AirDataCSV2012")
airDataCsvDS <- RxTextData(airDataCsvDir,fileSystem=hdfsFS)
rxDataStep(inData=airDataXdf, outFile=airDataCsvDS)
```

Deze stap zou ongeveer 2,5 minuut moeten duren.

De `rxDataStep` heeft een CSV-bestand geschreven voor elk XDFD-bestand in het ingevoerde samengestelde XDF-bestand. Dit is het standaardgedrag voor het schrijven van CSV-bestanden van samengestelde XDF-bestanden naar HDFS wanneer de compute-context is ingesteld op `RxSpark`.

### <a name="in-a-local-context"></a>In een lokale context

Wanneer u klaar bent met het uitvoeren van uw analyses, kunt u ook uw compute-context weer terugzetten op `local` om te profiteren van twee argumenten in `RxTextData` die u iets meer controle geven wanneer u CSV-bestanden naar HDFS schrijft: `createFileSet` en `rowsPerOutFile`. Wanneer u `createFileSet` instelt op `TRUE`, wordt een map met CSV-bestanden geschreven naar de map die u opgeeft. Wanneer u `createFileSet` instelt op `FALSE`, wordt één CSV-bestand geschreven. U kunt het tweede argument, `rowsPerOutFile`, instellen op een geheel getal om aan te geven hoeveel rijen er naar elk CSV-bestand moeten worden geschreven wanneer `createFileSet` de waarde `TRUE` heeft.

Voer in RStudio de volgende code in:

```R
rxSetComputeContext("local")
airDataCsvRowsDir <- file.path(bigDataDirRoot,"AirDataCSVRows2012")
airDataCsvRowsDS <- RxTextData(airDataCsvRowsDir, fileSystem=hdfsFS, createFileSet=TRUE, rowsPerOutFile=1000000)
rxDataStep(inData=airDataXdf, outFile=airDataCsvRowsDS)
```

Deze stap zou ongeveer 10 minuten moeten duren.

Wanneer u een `RxSpark`-compute-context gebruikt, is `createFileSet` standaard `TRUE` en heeft `rowsPerOutFile` geen effect. Als u één CSV wilt maken of het aantal rijen per bestand wilt aanpassen, moet u `rxDataStep` daarom uitvoeren in een `local`-compute-context (de gegevens kunnen zich nog wel in HDFS bevinden).

## <a name="final-steps"></a>Laatste stappen

1. De gegevens opschonen Voer in RStudio de volgende code in:

    ```R
    rxHadoopRemoveDir(airDataDir)
    rxHadoopRemoveDir(airDataXdfDir)
    rxHadoopRemoveDir(airDataCsvDir)
    rxHadoopRemoveDir(airDataCsvRowsDir)
    rxHadoopRemoveDir(bigDataDirRoot)
    ```

1. Stop de externe Spark-toepassing. Voer in RStudio de volgende code in:

    ```R
    rxStopEngine(mySparkCluster)
    ```

1. Sluit de R-sessie af. Voer in RStudio de volgende code in:

    ```R
    quit()
    ```

## <a name="clean-up-resources"></a>Resources opschonen

Nadat u de zelfstudie hebt voltooid, kunt u het cluster verwijderen. Met HDInsight worden uw gegevens opgeslagen in Azure Storage, zodat u een cluster veilig kunt verwijderen wanneer het niet wordt gebruikt. Voor een HDInsight-cluster worden ook kosten in rekening gebracht, zelfs wanneer het niet wordt gebruikt. Omdat de kosten voor het cluster veel hoger zijn dan de kosten voor opslag, is het voordeliger om clusters te verwijderen wanneer ze niet in gebruik zijn.

Als u een cluster wilt verwijderen, raadpleegt u [HDInsight-cluster verwijderen met behulp van uw browser, PowerShell of de Azure CLI](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u R-functies gebruikt in Apache Spark die worden uitgevoerd op een HDInsight Machine Learning Services-cluster. Raadpleeg voor meer informatie de volgende artikelen:

* [Compute-contextopties voor een Azure HDInsight Machine Learning Services-cluster](r-server-compute-contexts.md)
* [R-functies voor Spark op Hadoop](/machine-learning-server/r-reference/revoscaler/revoscaler-hadoop-functions)