---
title: Een cluster van ML Services op HDInsight-Azure beheren
description: Meer informatie over het beheren van verschillende taken in een cluster met MILLILITER Services in azure HDInsight.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 06/19/2019
ms.openlocfilehash: a5e623b0429194db6d03beb674679bd10e337844
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104869467"
---
# <a name="manage-ml-services-cluster-on-azure-hdinsight"></a>Een cluster van ML Services beheren in azure HDInsight

In dit artikel leert u hoe u een bestaand ML Services-cluster in azure HDInsight kunt beheren om taken uit te voeren, zoals het toevoegen van meerdere gelijktijdige gebruikers, het extern verbinden van een server met een ML-cluster, het wijzigen van de reken context, enzovoort.

## <a name="prerequisites"></a>Vereisten

* Een ML Services-cluster beheren in HDInsight. Zie [Apache Hadoop-clusters maken met behulp van Azure Portal](../hdinsight-hadoop-create-linux-clusters-portal.md) en selecteer **ML Services** voor **Clustertype**.

* Een SSH-client (Secure Shell): er wordt een SSH-client gebruikt om extern verbinding te maken met het HDInsight-cluster en om opdrachten rechtstreeks uit te voeren op het cluster. Zie [SSH gebruiken met HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md)voor meer informatie.

## <a name="enable-multiple-concurrent-users"></a>Meerdere gelijktijdige gebruikers inschakelen

U kunt meerdere gelijktijdige gebruikers inschakelen voor een cluster van ML Services in HDInsight door meer gebruikers toe te voegen voor het Edge-knoop punt waarop de RStudio-Community-versie wordt uitgevoerd. Wanneer u een HDInsight-cluster maakt, moet u twee gebruikers opgeven, te weten een HTTP-gebruiker en een SSH-gebruiker:

:::image type="content" source="./media/r-server-hdinsight-manage/hdi-concurrent-users1.png" alt-text="HDI Azure Portal aanmeldings parameters" border="true":::

- **Gebruikersnaam voor aanmelding cluster**: een HTTP-gebruiker voor verificatie via de HDInsight-gateway die wordt gebruikt voor het beveiligen van de HDInsight-clusters die u hebt gemaakt. Deze HTTP-gebruiker wordt gebruikt voor toegang tot de Apache Ambari-gebruikers interface, Apache Hadoop garen gebruikers interface en andere GEBRUIKERSINTERFACE onderdelen.
- **Secure Shell (SSH)-gebruikersnaam**: een SSH-gebruiker voor toegang tot het cluster via Secure Shell. Deze gebruiker is een gebruiker in het Linux-systeem voor alle hoofdknooppunten, werkknooppunten en Edge-knooppunten. U kunt Secure Shell gebruiken voor toegang tot alle knooppunten in een extern cluster.

De R Studio Server Community-versie die wordt gebruikt in het clusters van de ML Services op HDInsight accepteert alleen Linux-gebruikers naam en-wacht woord als aanmeldings mechanisme. Doorgegeven tokens worden niet ondersteund. Daarom moet u zich twee keer aanmelden wanneer u probeert om R Studio voor de eerste keer te openen op een ML-cluster.

- Meld u eerst aan met de HTTP-gebruikers referenties via de HDInsight-gateway.

- Gebruik vervolgens de aanmeldings gegevens van de SSH-gebruiker om u aan te melden bij RStudio.
  
Op dit moment kan slechts één SSH gebruikersaccount worden gemaakt tijdens het inrichten van een HDInsight-cluster. Om meerdere gebruikers in staat te stellen om toegang te krijgen tot het Services-cluster in HDInsight, moet u extra gebruikers in het Linux-systeem maken.

Omdat RStudio op het Edge-knoop punt van het cluster wordt uitgevoerd, zijn er verschillende stappen:

1. Gebruik de bestaande SSH-gebruiker om u aan te melden bij het Edge-knoop punt
2. Meer Linux-gebruikers toevoegen in Edge-knooppunt
3. RStudio Community-versie gebruiken met de gemaakte gebruiker

### <a name="step-1-use-the-created-ssh-user-to-sign-in-to-the-edge-node"></a>Stap 1: gebruik de gemaakte SSH-gebruiker om u aan te melden bij het Edge-knoop punt

Volg de instructies in [Connect to HDInsight (Apache Hadoop) met behulp van SSH](../hdinsight-hadoop-linux-use-ssh-unix.md) om toegang te krijgen tot het Edge-knoop punt. Het Edge-knooppunt adres voor een cluster van ML Services in HDInsight is `CLUSTERNAME-ed-ssh.azurehdinsight.net` .

### <a name="step-2-add-more-linux-users-in-edge-node"></a>Stap 2: meer Linux-gebruikers toevoegen in Edge-knooppunt

Om een gebruiker toe te voegen aan het Edge-knooppunt, voert u de volgende opdrachten uit:

```bash
# Add a user 
sudo useradd <yournewusername> -m

# Set password for the new user
sudo passwd <yournewusername>
```

In de volgende scherm afbeelding ziet u de uitvoer.

:::image type="content" source="./media/r-server-hdinsight-manage/hdi-concurrent-users2.png" alt-text="scherm opname-uitvoer van gelijktijdige gebruikers" border="true":::

Wanneer u wordt gevraagd naar ' Huidig Kerberos-wacht woord: ', drukt u op **Enter** om het te negeren. De `-m`-optie in de opdracht `useradd` geeft aan dat het systeem een basismap voor de gebruiker maakt die vereist is voor RStudio Community-versie.

### <a name="step-3-use-rstudio-community-version-with-the-user-created"></a>Stap 3: RStudio Community-versie gebruiken met de gemaakte gebruiker

Toegang tot RStudio vanuit `https://CLUSTERNAME.azurehdinsight.net/rstudio/` . Als u zich voor het eerst aanmeldt na het maken van het cluster, voert u de referenties van de Cluster beheerder in, gevolgd door de gebruikers referenties voor SSH die u hebt gemaakt. Als dit niet uw eerste aanmelding is, voert u alleen de referenties in voor de SSH-gebruiker die u hebt gemaakt.

U kunt zich ook vanuit een ander browser venster Aanmelden met de oorspronkelijke referenties (standaard *sshuser*).

U ziet ook dat de zojuist toegevoegde gebruikers geen hoofdmapbevoegdheden in het Linux-systeem hebben, maar wel dezelfde toegang tot alle bestanden in de externe HDFS- en WASB-opslag hebben.

## <a name="connect-remotely-to-microsoft-ml-services"></a>Op afstand verbinding maken met micro soft ML Services

U kunt toegang tot de HDInsight Spark-Compute-context instellen vanaf een extern exemplaar van een ML-client dat op uw bureau blad wordt uitgevoerd. Hiervoor moet u de opties (hdfsShareDir, shareDir, sshUsername, sshHostname, sshSwitches en sshProfileScript) opgeven bij het definiëren van de RxSpark-Compute-context op het bureau blad: bijvoorbeeld:

```r
myNameNode <- "default"
myPort <- 0

mySshHostname  <- '<clustername>-ed-ssh.azurehdinsight.net'  # HDI secure shell hostname
mySshUsername  <- '<sshuser>'# HDI SSH username
mySshSwitches  <- '-i /cygdrive/c/Data/R/davec'   # HDI SSH private key

myhdfsShareDir <- paste("/user/RevoShare", mySshUsername, sep="/")
myShareDir <- paste("/var/RevoShare" , mySshUsername, sep="/")

mySparkCluster <- RxSpark(
    hdfsShareDir = myhdfsShareDir,
    shareDir     = myShareDir,
    sshUsername  = mySshUsername,
    sshHostname  = mySshHostname,
    sshSwitches  = mySshSwitches,
    sshProfileScript = '/etc/profile',
    nameNode     = myNameNode,
    port         = myPort,
    consoleOutput= TRUE
)
```

Zie voor meer informatie de sectie ' using Microsoft Machine Learning Server as a Apache Hadoop client ' in [How to use RevoScaleR in een Apache Spark Compute-context](/machine-learning-server/r/how-to-revoscaler-spark#more-spark-scenarios)

## <a name="use-a-compute-context"></a>Een compute-context gebruiken

Met een compute-context kunt u bepalen of een berekening lokaal op het Edge-knooppunt wordt uitgevoerd of wordt gedistribueerd naar de verschillende knooppunten in het HDInsight-cluster.  Voor een voor beeld van het instellen van een compute-context met RStudio-Server raadpleegt [u een R-script uitvoeren op een ml Services-cluster in azure HDInsight met behulp van RStudio-server](machine-learning-services-quickstart-job-rstudio.md).

## <a name="distribute-r-code-to-multiple-nodes"></a>R-code distribueren naar meerdere knooppunten

Met ML Services op HDInsight kunt u bestaande R-code nemen en deze uitvoeren op meerdere knoop punten in het cluster met behulp van `rxExec` . Deze functie is handig bij het uitvoeren van een parameteropschoning of van simulaties. Hier volgt een voorbeeldcode van het gebruik van `rxExec`:

```r
rxExec( function() {Sys.info()["nodename"]}, timesToRun = 4 )
```

Als u nog steeds de Spark-context gebruikt, retourneert deze opdracht de waarde van de nodenaam voor de worker-knoop punten waarop de code `(Sys.info()["nodename"])` wordt uitgevoerd. Op een cluster met vier knoop punten verwacht u bijvoorbeeld dat er uitvoer wordt weer gegeven die vergelijkbaar is met het volgende code fragment:

```r
$rxElem1
    nodename
"wn3-mymlser"

$rxElem2
    nodename
"wn0-mymlser"

$rxElem3
    nodename
"wn3-mymlser"

$rxElem4
    nodename
"wn3-mymlser"
```

## <a name="access-data-in-apache-hive-and-parquet"></a>Toegang tot gegevens in Apache Hive en Parquet

Met HDInsight ML-Services kunt u rechtstreeks toegang krijgen tot gegevens in Hive en Parquet voor gebruik door Scaler-functies in de Spark-Compute-context. Deze mogelijkheden zijn beschikbaar via nieuwe functies voor ScaleR-gegevensbronnen (genaamd RxHiveData en RxParquetData) die gebruikmaken van Spark SQL om gegevens rechtstreeks in Spark DataFrame te laden voor analyse met ScaleR.

Hieronder ziet u code met voorbeeldcode bij het gebruik van de nieuwe functies:

```r
#Create a Spark compute context:
myHadoopCluster <- rxSparkConnect(reset = TRUE)

#Retrieve some sample data from Hive and run a model:
hiveData <- RxHiveData("select * from hivesampletable",
                       colInfo = list(devicemake = list(type = "factor")))
rxGetInfo(hiveData, getVarInfo = TRUE)

rxLinMod(querydwelltime ~ devicemake, data=hiveData)

#Retrieve some sample data from Parquet and run a model:
rxHadoopMakeDir('/share')
rxHadoopCopyFromLocal(file.path(rxGetOption('sampleDataDir'), 'claimsParquet/'), '/share/')
pqData <- RxParquetData('/share/claimsParquet',
                        colInfo = list(
                            age    = list(type = "factor"),
                            car.age = list(type = "factor"),
                            type = list(type = "factor")
                        ) )
rxGetInfo(pqData, getVarInfo = TRUE)

rxNaiveBayes(type ~ age + cost, data = pqData)

#Check on Spark data objects, cleanup, and close the Spark session:
lsObj <- rxSparkListData() # two data objs are cached
lsObj
rxSparkRemoveData(lsObj)
rxSparkListData() # it should show empty list
rxSparkDisconnect(myHadoopCluster)
```

Voor meer informatie over het gebruik van deze nieuwe functies raadpleegt u de online-Help in ML Services via het gebruik van de `?RxHivedata` `?RxParquetData` opdrachten en.  

## <a name="install-additional-r-packages-on-the-cluster"></a>Extra R-pakketten installeren op het cluster

### <a name="to-install-r-packages-on-the-edge-node"></a>R-pakketten installeren op het Edge-knoop punt

Als u extra R-pakketten wilt installeren op het Edge-knoop punt, kunt u `install.packages()` rechtstreeks vanuit de R-console gebruiken wanneer u via SSH bent verbonden met het Edge-knoop punt. 

### <a name="to-install-r-packages-on-the-worker-node"></a>R-pakketten installeren op het worker-knoop punt

Als u R-pakketten wilt installeren op de worker-knoop punten van het cluster, moet u een script actie gebruiken. Scriptacties zijn Bash-scripts die worden gebruikt om configuratiewijzigingen aan te brengen in het HDInsight-cluster of om extra software te installeren, zoals extra R-pakketten. 

> [!IMPORTANT]  
> U kunt scriptacties alleen gebruiken om extra R-pakketten te installeren nadat het cluster is gemaakt. Gebruik deze procedure niet tijdens het maken van het cluster, omdat het script afhankelijk is van MILLILITERs services die volledig zijn geconfigureerd.

1. Volg de stappen in [clusters aanpassen met behulp van script acties](../hdinsight-hadoop-customize-cluster-linux.md).

3. Geef voor de **actie script verzenden** de volgende informatie op:

   * Selecteer **aangepast** bij **script type**.

   * Geef bij **naam** een naam op voor de script actie.

     * Voer voor **bash-script-URI** in  `https://mrsactionscripts.blob.core.windows.net/rpackages-v01/InstallRPackages.sh` . Dit is het script waarmee extra R-pakketten worden geïnstalleerd op het worker-knoop punt

   * Schakel het selectie vakje voor de **werk nemer** alleen in.

   * **Parameters**: de R-pakketten die moeten worden geïnstalleerd. Bijvoorbeeld: `bitops stringr arules`

   * Schakel het selectie vakje in om **Deze script actie persistent** te maken.  

   > [!NOTE]
   > 1. Standaard worden alle R-pakketten geïnstalleerd vanuit een moment opname van de micro soft MRAN-opslag plaats, consistent met de versie van ML Server die is geïnstalleerd. Als u nieuwere versies van pakketten wilt installeren, is er kans op incompatibiliteit. Dit soort installatie is echter mogelijk door `useCRAN` op te geven als het eerste element van de pakketlijst, bijvoorbeeld `useCRAN bitops, stringr, arules`.  
   > 2. Voor sommige R-pakketten zijn aanvullende Linux-systeembibliotheken vereist. Voor het gemak wordt de HDInsight ML-service vooraf geïnstalleerd met de afhankelijkheden die nodig zijn voor de populairste 100 meest populaire R-pakketten. Als voor de R-pakketten die u wilt installeren, meer bibliotheken zijn vereist, downloadt u het standaardscript dat hier wordt gebruikt, en voegt u stappen toe om de systeembibliotheken te installeren. Vervolgens moet u het gewijzigde script uploaden naar een openbare blobcontainer in Azure-opslag en het gewijzigde script gebruiken om de pakketten te installeren.
   >    Zie [Ontwikkeling van scriptacties](../hdinsight-hadoop-script-actions-linux.md) voor meer informatie over het ontwikkelen van scriptacties.

   :::image type="content" source="./media/r-server-hdinsight-manage/submit-script-action.png" alt-text="Azure Portal script actie verzenden" border="true":::

4. Selecteer **Maken** om het script uit te voeren. Nadat het script is voltooid, zijn de R-pakketten beschikbaar op alle werkknooppunten.

## <a name="next-steps"></a>Volgende stappen

* [ML Services-cluster operationeel maken in HDInsight](r-server-operationalize.md)
* [Opties voor Compute-context voor het service cluster van ML in HDInsight](r-server-compute-contexts.md)
* [Opties voor Azure Storage voor ML Services-cluster in HDInsight](r-server-storage.md)