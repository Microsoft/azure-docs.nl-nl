---
title: Taken verzenden vanuit R-Hulpprogram Ma's voor Visual Studio-Azure HDInsight
description: Verzend R-taken van uw lokale Visual Studio-machine naar een HDInsight-cluster.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/19/2019
ms.openlocfilehash: c5a0b2d21f7d42b8ce96f72d58e5d0a8ab0c572c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98943947"
---
# <a name="submit-jobs-from-r-tools-for-visual-studio"></a>Taken verzenden vanuit R-hulpprogramma's voor Visual Studio

[R tools for Visual Studio](https://marketplace.visualstudio.com/items?itemName=MikhailArkhipov007.RTVS2019) (RTVS) is een gratis open-source extensie voor de Community (gratis), Professional en Enter prise-edities van zowel [Visual Studio 2017](https://www.visualstudio.com/downloads/)als [Visual Studio 2015 update 3](https://go.microsoft.com/fwlink/?LinkId=691129) of hoger. RTVS is niet beschikbaar voor [Visual Studio 2019](/visualstudio/porting/port-migrate-and-upgrade-visual-studio-projects?preserve-view=true&view=vs-2019).

RTVS breidt uw R-werk stroom uit door hulpprogram ma's te bieden zoals het [R Interactive Window](/visualstudio/rtvs/interactive-repl) (repl), IntelliSense (code completion), de [visualisatie te tekenen](/visualstudio/rtvs/visualizing-data) via R-bibliotheken zoals Ggplot2 en ggviz, [r-code fout opsporing](/visualstudio/rtvs/debugging)en meer.

## <a name="set-up-your-environment"></a>Uw omgeving instellen

1. Installeer [R-Hulpprogram ma's voor Visual Studio](/visualstudio/rtvs/installing-r-tools-for-visual-studio).

    ![RTVS installeren in Visual Studio 2017](./media/r-server-submit-jobs-r-tools-vs/install-r-tools-for-vs.png)

2. Selecteer de werk belasting *Data Science en analytische toepassingen* en selecteer vervolgens de **ondersteuning voor r-taal**, runtime- **ondersteuning voor r-ontwikkeling** en **micro soft R-client** opties.

3. U moet open bare en persoonlijke sleutels hebben voor SSH-verificatie.
   <!-- {TODO tbd, no such file yet}[use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-windows.md) -->

4. Installeer [ml server](/previous-versions/machine-learning-server/install/r-server-install-windows) op de computer. ML Server biedt de [`RevoScaleR`](/machine-learning-server/r-reference/revoscaler/revoscaler) `RxSpark` functies en.

5. Installeer [putty](https://www.putty.org/) om een compute-context te bieden voor het uitvoeren `RevoScaleR` van functies van uw lokale client naar uw HDInsight-cluster.

6. U hebt de optie om de data Science-instellingen toe te passen op uw Visual Studio-omgeving, die een nieuwe indeling biedt voor uw werk ruimte voor de R-hulpprogram ma's.
   1. Als u de huidige Visual Studio-instellingen wilt opslaan, gebruikt u de opdracht **Hulpprogram ma's > instellingen voor importeren en exporteren** en selecteert u **geselecteerde omgevings instellingen exporteren** en geeft u een bestands naam op. Als u deze instellingen wilt herstellen, gebruikt u dezelfde opdracht en selecteert u **geselecteerde omgevings instellingen importeren**.

   2. Ga naar het menu-item **R tools** en selecteer **Data Science Settings...**.

       ![Instellingen voor Visual Studio data Science](./media/r-server-submit-jobs-r-tools-vs/data-science-settings.png)

      > [!NOTE]  
      > Met de aanpak in stap 1 kunt u ook uw persoonlijke gegevens wetenschapper-indeling opslaan en herstellen, in plaats van de opdracht **Data Science Settings** te herhalen.

## <a name="execute-local-r-methods"></a>Lokale R-methoden uitvoeren

1. Maak uw cluster met HDInsight ML-Services.
2. Installeer de [RTVS-extensie](/visualstudio/rtvs/installation).
3. Down load het [zip-bestand voor beelden](https://github.com/Microsoft/RTVS-docs/archive/master.zip).
4. Open `examples/Examples.sln` om de oplossing in Visual Studio te starten.
5. Open het `1-Getting Started with R.R` bestand in de `A first look at R` map Solution.
6. Vanaf de bovenkant van het bestand, drukt u op CTRL + ENTER om elke regel, één per keer, naar het venster R Interactive te verzenden. Sommige regels kunnen even duren als ze pakketten installeren.
    * U kunt ook alle regels in het R-bestand selecteren (CTRL + A) en vervolgens alle uitvoeren (CTRL + ENTER) of het pictogram voor het uitvoeren van een interactieve taak op de werk balk selecteren.

        ![Interactieve uitvoering van Visual Studio](./media/r-server-submit-jobs-r-tools-vs/execute-interactive1.png)

7. Nadat u alle regels in het script hebt uitgevoerd, ziet u een uitvoer die er ongeveer als volgt uitzien:

    ![Hulpprogram ma's voor Visual Studio-werk ruimte R](./media/r-server-submit-jobs-r-tools-vs/visual-studio-workspace.png)

## <a name="submit-jobs-to-an-hdinsight-ml-services-cluster"></a>Taken verzenden naar een HDInsight ML Services-cluster

Als u een micro soft ML Server/micro soft R-client gebruikt vanaf een Windows-computer met PuTTy, kunt u een compute-context maken waarmee gedistribueerde `RevoScaleR` functies van uw lokale client naar uw HDInsight-cluster worden uitgevoerd. Gebruik `RxSpark` om de compute-context te maken, waarbij u uw gebruikers naam opgeeft, het Edge-knoop punt van het Apache Hadoop cluster, SSH-switches, enzovoort.

1. Het knooppunt adres van de ML van het service-Edge op HDInsight is `CLUSTERNAME-ed-ssh.azurehdinsight.net` `CLUSTERNAME` de naam van uw cluster met ml-Services.

1. Plak de volgende code in het venster R Interactive in Visual Studio en wijzig de waarden van de installatie variabelen zodat deze overeenkomen met uw omgeving.

    ```R
    # Setup variables that connect the compute context to your HDInsight cluster
    mySshHostname <- 'r-cluster-ed-ssh.azurehdinsight.net ' # HDI secure shell hostname
    mySshUsername <- 'sshuser' # HDI SSH username
    mySshClientDir <- "C:\\Program Files (x86)\\PuTTY"
    mySshSwitches <- '-i C:\\Users\\azureuser\\r.ppk' # Path to your private ssh key
    myHdfsShareDir <- paste("/user/RevoShare", mySshUsername, sep = "/")
    myShareDir <- paste("/var/RevoShare", mySshUsername, sep = "/")
    mySshProfileScript <- "/usr/lib64/microsoft-r/3.3/hadoop/RevoHadoopEnvVars.site"

    # Create the Spark Cluster compute context
    mySparkCluster <- RxSpark(
          sshUsername = mySshUsername,
          sshHostname = mySshHostname,
          sshSwitches = mySshSwitches,
          sshProfileScript = mySshProfileScript,
          consoleOutput = TRUE,
          hdfsShareDir = myHdfsShareDir,
          shareDir = myShareDir,
          sshClientDir = mySshClientDir
    )

    # Set the current compute context as the Spark compute context defined above
    rxSetComputeContext(mySparkCluster)
    ```

   ![Apache Spark-context instellen](./media/r-server-submit-jobs-r-tools-vs/apache-spark-context.png)

1. Voer de volgende opdrachten uit in het venster R Interactive:

    ```R
    rxHadoopCommand("version") # should return version information
    rxHadoopMakeDir("/user/RevoShare/newUser") # creates a new folder in your storage account
    rxHadoopCopy("/example/data/people.json", "/user/RevoShare/newUser") # copies file to new folder
    ```

    Als het goed is, wordt ongeveer de volgende uitvoer weergegeven:

    ![Geslaagde RX-opdracht voor het uitvoeren van ](./media/r-server-submit-jobs-r-tools-vs/successful-rx-commands.png) een
1. Controleer of het `rxHadoopCopy` bestand is gekopieerd `people.json` van de map voorbeeld gegevens naar de zojuist gemaakte `/user/RevoShare/newUser` map:

    1. Selecteer in het deel venster cluster van HDInsight ML-Services in azure **opslag accounts** in het menu links.

        ![Azure HDInsight Storage-accounts](./media/r-server-submit-jobs-r-tools-vs/hdinsight-storage-accounts.png)

    2. Selecteer het standaard opslag account voor uw cluster en noteer de naam van de container/map.

    3. Selecteer **containers** in het menu links in het deel venster opslag account.

        ![Azure HDInsight Storage-containers](./media/r-server-submit-jobs-r-tools-vs/hdi-storage-containers.png)

    4. Selecteer de container naam van uw cluster, blader naar de map **gebruiker** (mogelijk moet u op *extra laden* onder aan de lijst) en selecteer vervolgens *RevoShare* en **newuser**. Het `people.json` bestand moet in de map worden weer gegeven `newUser` .

        ![Locatie van gekopieerde bestandsmap van HDInsight](./media/r-server-submit-jobs-r-tools-vs/hdinsight-copied-file.png)

1. Wanneer u klaar bent met het gebruik van de huidige Apache Spark context, moet u deze stoppen. U kunt niet meerdere contexten tegelijk uitvoeren.

    ```R
    rxStopEngine(mySparkCluster)
    ```

## <a name="next-steps"></a>Volgende stappen

* [Opties voor compute-context voor ML Services in HDInsight](r-server-compute-contexts.md)
* De [combi natie van Scaler en sparkr](../hdinsight-hadoop-r-scaler-sparkr.md) biedt een voor beeld van vertraagde voor spellingen voor de vliegtuig vlucht.