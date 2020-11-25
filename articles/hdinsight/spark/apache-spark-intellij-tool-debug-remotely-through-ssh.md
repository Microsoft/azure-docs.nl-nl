---
title: 'Azure-toolkit voor IntelliJ: fout opsporing van Spark-apps met SSH-HDInsight'
description: Stapsgewijze richt lijnen voor het gebruik van HDInsight-Hulpprogram Ma's in Azure-toolkit voor IntelliJ voor het opsporen van fouten op externe toepassingen op HDInsight-clusters via SSH
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 12/23/2019
ms.openlocfilehash: 421993ac4aaba551b6fcbd002783d44559ce377d
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95995332"
---
# <a name="debug-apache-spark-applications-on-an-hdinsight-cluster-with-azure-toolkit-for-intellij-through-ssh"></a>Fouten opsporen Apache Spark toepassingen op een HDInsight-cluster met Azure-toolkit voor IntelliJ via SSH

In dit artikel vindt u stapsgewijze richt lijnen voor het gebruik van HDInsight-Hulpprogram Ma's in [Azure-Toolkit voor IntelliJ](/azure/developer/java/toolkit-for-intellij) voor het op afstand opsporen van toepassingen op een HDInsight-cluster. Als u fouten wilt opsporen in uw project, kunt u ook de [fout opsporing HDInsight Spark-toepassingen met Azure-Toolkit voor IntelliJ](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ) video bekijken.

## <a name="prerequisites"></a>Vereisten

* Een Apache Spark-cluster in HDInsight. Zie [Een Apache Spark-cluster maken](../spark/apache-spark-jupyter-spark-sql-use-portal.md).

* Voor Windows-gebruikers: wanneer u de lokale Spark scala-toepassing uitvoert op een Windows-computer, kunt u een uitzonde ring krijgen, zoals wordt uitgelegd in [Spark-2356](https://issues.apache.org/jira/browse/SPARK-2356). De uitzondering treedt op omdat WinUtils.exe ontbreekt op Windows.

    U kunt deze fout oplossen door [Winutils.exe](https://github.com/steveloughran/winutils) te downloaden naar een locatie zoals **C:\WinUtils\bin**. Vervolgens voegt u de omgevingsvariabele **HADOOP_HOME** toe en stelt u de waarde van de variabele in op **C:\WinUtils**.

* [IntelliJ-idee](https://www.jetbrains.com/idea/download/#section=windows) (de Community-editie is gratis).

* [Azure-Toolkit voor IntelliJ](/azure/developer/java/toolkit-for-intellij/installation).

* [Scala-invoeg toepassing voor IntelliJ](../spark/apache-spark-intellij-tool-plugin.md#install-scala-plugin-for-intellij-idea).

* Een SSH-client. Zie voor meer informatie [Verbinding maken met HDInsight (Apache Hadoop) via SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="create-a-spark-scala-application"></a>Een Spark scala-toepassing maken

1. Start IntelliJ IDEA en selecteer **Create New Project** om het venster **New Project** te openen.

1. Selecteer **Apache Spark/HDInsight** in het linkerdeelvenster.

1. Selecteer **Spark-project met voor beelden (scala)** in het hoofd venster.

1. Selecteer in de vervolgkeuzelijst **Build tool** een van de volgende opties:

    * **Maven**, voor de ondersteuning van de wizard Scala-project maken.
    * **SBT**, voor het beheren van de afhankelijkheden en het maken van het Scala-project.

     ![IntelliJ nieuwe project Spark maken](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-create-projectfor-debug-remotely.png)

1. Selecteer **Next**.

1. Geef in het volgende **nieuwe project** venster de volgende informatie op:

    |Eigenschap |Beschrijving |
    |---|---|
    |Projectnaam|Voer een naam in. Dit maakt gebruik van deze instructies `myApp` .|
    |Project locatie|Voer de gewenste locatie in om uw project in op te slaan.|
    |Project SDK|Als deze leeg is, selecteert u **Nieuw...** en navigeert u naar uw JDK.|
    |Spark-versie|De wizard voor het maken van het project integreert de juiste versie voor Spark SDK en Scala SDK. Selecteer **Spark 1.x** als de Spark-clusterversie ouder is dan 2.0. Selecteer anders **Spark 2. x.**. In dit voorbeeld wordt **Spark 2.3.0 (Scala 2.11.8)** gebruikt.|

   ![IntelliJ nieuw project Spark-versie selecteren](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-new-project.png)

1. Selecteer **Finish**. Het kan enkele minuten duren voordat het project beschikbaar wordt. Bekijk de rechter benedenhoek voor de voortgang.

1. Vouw het project uit en navigeer naar **src**  >  **Main**  >  **scala**  >  **sample**. Dubbel klik op **SparkCore_WasbIOTest**.

## <a name="perform-local-run"></a>Lokale uitvoering uitvoeren

1. Klik vanuit het **SparkCore_WasbIOTest** script met de rechter muisknop op de script editor en selecteer vervolgens de optie **' SparkCore_WasbIOTest ' uitvoeren** om lokale uitvoering uit te voeren.

1. Zodra de lokale uitvoering is voltooid, kunt u het uitvoer bestand opslaan in de standaard **gegevens** van de project Verkenner  >  **__default__**.

    ![Resultaat lokale uitvoering IntelliJ-project](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/spark-local-run-result.png)

1. Onze tools hebben de standaard configuratie voor lokale uitvoering automatisch ingesteld wanneer u de lokale uitvoering en de lokale fout opsporing uitvoert. Open de configuratie **[Spark op hdinsight] xxx** . in de rechter bovenhoek ziet u de **[Spark op hdinsight] xxx** die al is gemaakt onder **Apache Spark op hdinsight**. Schakel over naar het tabblad **lokaal uitvoeren** .

    ![Lokaal uitvoeren in Uitvoeren/fouten opsporen van configuraties in IntelliJ](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/local-run-configuration.png)

    - [Omgevings variabelen](#prerequisites): als u de omgevings variabele van de systeem al hebt ingesteld **HADOOP_HOME** op **C:\WinUtils**, kan deze automatisch detecteren dat deze niet hand matig hoeft toe te voegen.
    - [WinUtils.exe locatie](#prerequisites): als u de omgevings variabele systeem niet hebt ingesteld, kunt u de locatie vinden door op de knop te klikken.
    - Kies een van de twee opties en ze zijn niet nodig voor MacOS en Linux.

1. U kunt de configuratie ook hand matig instellen voordat u Local run en Local debug uitvoert. Selecteer in de vorige scherm afbeelding het plus teken ( **+** ). Selecteer vervolgens de optie **Apache Spark op HDInsight** . Voer informatie in voor de **naam**, de naam van de **hoofd klasse** die u wilt opslaan en klik vervolgens op de knop lokale uitvoering.

## <a name="perform-local-debugging"></a>Lokale fout opsporing uitvoeren

1. Open het **SparkCore_wasbloTest** script en stel onderbrekings punten in.

1. Klik met de rechter muisknop op de script editor en selecteer vervolgens de optie **fout opsporing [Spark op HDInsight] xxx** om lokale fout opsporing uit te voeren.

## <a name="perform-remote-run"></a>Extern uitvoeren uitvoeren

1. Navigeren om **Run**  >  **bewerkings configuraties uit te voeren...**. In dit menu kunt u de configuraties voor fout opsporing op afstand maken of bewerken.

1. Selecteer in het dialoog venster **configuraties voor uitvoeren/fout opsporing** het plus teken ( **+** ). Selecteer vervolgens de optie **Apache Spark op HDInsight** .

   ![Nieuwe configuratie IntelliJ toevoegen](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-add-new-Configuration.png)

1. Overschakelen naar **extern uitvoeren op** het tabblad cluster. Voer informatie in voor de **naam**, het **Spark-cluster** en de naam van de **hoofd klasse**. Klik vervolgens op **Geavanceerde configuratie (fout opsporing op afstand)**. Onze hulp middelen ondersteunen debug met **uitvoerende** software. De **numExectors**, de standaard waarde is 5. U kunt beter niet meer dan 3 instellen.

   ![IntelliJ voor fout opsporing uitvoeren](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-run-debug-configurations.png)

1. Selecteer in het gedeelte **Geavanceerde configuratie (externe fout opsporing)** de optie **Spark externe fout opsporing inschakelen**. Voer de SSH-gebruikers naam in en voer een wacht woord in of gebruik een bestand met een persoonlijke sleutel. Als u fout opsporing op afstand wilt uitvoeren, moet u het instellen. U hoeft deze niet in te stellen als u alleen extern uitvoeren wilt gebruiken.

   ![Geavanceerde configuratie van IntelliJ inschakelen Spark externe fout opsporing](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-enable-spark-remote-debug.png)

1. De configuratie wordt nu opgeslagen met de naam die u hebt ingevoerd. Als u de configuratie gegevens wilt weer geven, selecteert u de naam van de configuratie. Als u wijzigingen wilt aanbrengen, selecteert u **configuraties bewerken**.

1. Nadat u de configuratie-instellingen hebt voltooid, kunt u het project uitvoeren op het externe cluster of externe fout opsporing uitvoeren.

   ![Knop voor externe uitvoering van IntelliJ fout opsporing voor externe Spark-taak](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/perform-remote-run-button.png)

1. Klik op de knop **verbinding verbreken** als de inzendings logboeken niet worden weer gegeven in het linkerdeel venster. Het is echter nog steeds actief op de back-end.

   ![Resultaat van externe uitvoering van externe Spark-taak IntelliJ fout opsporing](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/spark-remote-run-result.png)

## <a name="perform-remote-debugging"></a>Fout opsporing op afstand uitvoeren

1. Stel breuk punten in en klik vervolgens op het pictogram **fout opsporing op afstand** . Het verschil met externe verzen ding is dat de SSH-gebruikers naam en het wacht woord moeten worden geconfigureerd.

   ![Pictogram fout opsporing externe Spark-taak IntelliJ](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debug-icon.png)

1. Wanneer de uitvoering van het programma het breuk punt bereikt, ziet u een tabblad van het **stuur programma** **en twee tabbladen** voor uitvoeringen in het deel venster **fout opsporing** . Selecteer het pictogram **programma hervatten** om door te gaan met het uitvoeren van de code, waarna het volgende onderbrekings punt wordt bereikt. U moet overschakelen naar **het tabblad voor** de juiste uitvoerder om de doel-uitvoerder te vinden voor fout opsporing. U kunt de uitvoerings logboeken bekijken op het bijbehorende **console** tabblad.

   ![IntelliJ fout opsporing externe Spark-taak fout opsporing](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debugger-tab.png)

### <a name="perform-remote-debugging-and-bug-fixing"></a>Fout opsporing op afstand en probleem oplossing uitvoeren

1. Stel twee afbreek punten in en selecteer vervolgens het pictogram **fout opsporing** om het proces voor externe fout opsporing te starten.

1. De code stopt bij het eerste punt, en de para meter-en variabele gegevens worden weer gegeven in het deel venster **variabelen** .

1. Selecteer het pictogram **programma hervatten** om door te gaan. De code wordt op het tweede punt gestopt. De uitzonde ring wordt naar verwachting gedetecteerd.

   ![Fout bij genereren van externe Spark-taak voor IntelliJ fouten](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-throw-error.png)

1. Selecteer opnieuw het pictogram **programma hervatten** . In het venster **HDInsight Spark-verzen ding** wordt de fout taak uitvoering is mislukt weer gegeven.

   ![Fout bij het verzenden van externe Spark-taak fouten in IntelliJ](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-error-submission.png)

1. Als u de waarde van de variabele dynamisch wilt bijwerken met behulp van de IntelliJ-functie voor fout opsporing, selecteert u **fout opsporing** opnieuw. Het deel venster **variabelen** wordt weer gegeven.

1. Klik met de rechter muisknop op het doel op het tabblad **fout opsporing** en selecteer vervolgens **waarde instellen**. Voer vervolgens een nieuwe waarde in voor de variabele. Selecteer vervolgens **Enter** om de waarde op te slaan.

   ![Waarde voor fout bij instellen van externe Spark-taak IntelliJ](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-set-value1.png)

1. Selecteer het pictogram **programma hervatten** om door te gaan met het programma uitvoeren. Deze keer wordt er geen uitzonde ring gedetecteerd. U kunt zien dat het project zonder uitzonde ringen correct wordt uitgevoerd.

   ![IntelliJ externe Spark-taak fouten opsporen zonder uitzonde ring](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debug-without-exception.png)

## <a name="next-steps"></a>Volgende stappen

* [Overzicht: Apache Spark in Azure HDInsight](apache-spark-overview.md)

### <a name="demo"></a>Demo

* Scala-project maken (video): [Apache Spark scala-toepassingen maken](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)
* Externe fout opsporing (video): [Azure-Toolkit voor IntelliJ gebruiken om fouten op te sporen in Apache Spark toepassingen op afstand in een HDInsight-cluster](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)

### <a name="scenarios"></a>Scenario's

* [Apache Spark met BI: interactieve gegevens analyses uitvoeren met behulp van Spark in HDInsight met BI-hulpprogram ma's](apache-spark-use-bi-tools.md)
* [Apache Spark met Machine Learning: Spark in HDInsight gebruiken voor het analyseren van de gebouw temperatuur met behulp van HVAC-gegevens](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark met Machine Learning: Spark in HDInsight gebruiken om voedsel inspectie resultaten te voors pellen](apache-spark-machine-learning-mllib-ipython.md)
* [Analyse van website logboeken met Apache Spark in HDInsight](./apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Toepassingen maken en uitvoeren

* [Een zelfstandige toepassing maken met behulp van Scala](./apache-spark-create-standalone-application.md)
* [Apache Livy gebruiken om taken op afstand uit te voeren in een Apache Spark-cluster](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Tools en uitbreidingen

* [Azure-toolkit voor IntelliJ gebruiken om Apache Spark-toepassingen voor een HDInsight-cluster te maken](apache-spark-intellij-tool-plugin.md)
* [Azure-toolkit voor IntelliJ gebruiken om fouten op te lossen Apache Spark toepassingen op afstand via VPN](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Gebruik HDInsight-Hulpprogram Ma's in Azure-toolkit voor Eclipse om Apache Spark-toepassingen te maken](./apache-spark-eclipse-tool-plugin.md)
* [Apache Zeppelin-notebooks gebruiken met een Apache Spark-cluster in HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernels die beschikbaar zijn voor Jupyter-notebook in het Apache Spark-cluster voor HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Externe pakketten gebruiken met Jupyter-notebooks](apache-spark-jupyter-notebook-use-external-packages.md)
* [Jupyter op uw computer installeren en verbinding maken met een HDInsight Spark-cluster](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Resources beheren

* [Resources beheren voor het Apache Spark-cluster in Azure HDInsight](apache-spark-resource-manager.md)
* [Taken die worden uitgevoerd in een Apache Spark-cluster in HDInsight, traceren en er fouten in oplossen](apache-spark-job-debugging.md)