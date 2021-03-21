---
title: Fout opsporing voor Spark-taken met IntelliJ Azure Toolkit (preview)-HDInsight
description: Richt lijnen voor het gebruik van HDInsight-Hulpprogram Ma's in Azure-toolkit voor IntelliJ voor fout opsporing in toepassingen
keywords: fout opsporing op afstand IntelliJ, externe fout opsporing IntelliJ, SSH, IntelliJ, hdinsight, debug IntelliJ, fout opsporing
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 07/12/2019
ms.openlocfilehash: 5abbb26e7582d8ddabb73f3a178cf6d87e24db21
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98942534"
---
# <a name="failure-spark-job-debugging-with-azure-toolkit-for-intellij-preview"></a>Fout bij het opsporen van Spark-taken met Azure-toolkit voor IntelliJ (preview-versie)

In dit artikel vindt u stapsgewijze richt lijnen voor het gebruik van HDInsight-Hulpprogram Ma's in [Azure-Toolkit voor IntelliJ](/azure/developer/java/toolkit-for-intellij) voor het uitvoeren van fout **opsporing in Spark** -toepassingen.

## <a name="prerequisites"></a>Vereisten

* [Oracle Java Development Kit](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html). In deze zelfstudie wordt gebruikgemaakt van Java-versie 8.0.202.
  
* IntelliJ-idee. In dit artikel wordt gebruikgemaakt van [IntelliJ-idee Community ver. 2019.1.3](https://www.jetbrains.com/idea/download/#section=windows).
  
* Azure-toolkit voor IntelliJ. Zie [Installing the Azure Toolkit for IntelliJ](/azure/developer/java/toolkit-for-intellij/installation) (De Azure Toolkit voor IntelliJ installeren).

* Verbinding maken met uw HDInsight-cluster. Zie [verbinding maken met uw HDInsight-cluster](apache-spark-intellij-tool-plugin.md).

* Microsoft Azure Storage Explorer. Zie [Microsoft Azure Storage Explorer downloaden](https://azure.microsoft.com/features/storage-explorer/).

## <a name="create-a-project-with-debugging-template"></a>Een project maken met een sjabloon voor fout opsporing

Een Spark 2.3.2-project maken om de fout opsporing voort te zetten, fout in het voorbeeld bestand voor fouten in dit document oplossen.

1. Open IntelliJ IDEA. Open het venster **Nieuw project** .

   a. Selecteer **Azure Spark/HDInsight** in het linkerdeelvenster.

   b. Selecteer **Spark-project met fout fout Opsporingsgegevens voor beeld (preview) (scala)** in het hoofd venster.

     ![IntelliJ een debug-project maken](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-projectfor-failure-debug.png)

   c. Selecteer **Volgende**.

2. Voer in het venster **New Project** de volgende stappen uit:

   ![IntelliJ nieuw project Spark-versie selecteren](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-new-project.png)

   a. Voer een project naam en Project locatie in.

   b. Selecteer in de vervolg keuzelijst **Project SDK** **Java 1,8** voor **Spark 2.3.2** -cluster.

   c. Selecteer in de vervolg keuzelijst **Spark-versie** **Spark 2.3.2 (scala 2.11.8)**.

   d. Selecteer **Finish**.

3. Selecteer **src**  >  **Main**  >  **scala** om de code in het project te openen. In dit voor beeld wordt het script **AgeMean_Div ()** gebruikt.

## <a name="run-a-spark-scalajava-application-on-an-hdinsight-cluster"></a>Een Spark scala/Java-toepassing uitvoeren op een HDInsight-cluster

Maak een Spark scala/Java-toepassing en voer de toepassing uit op een Spark-cluster door de volgende stappen uit te voeren:

1. Klik op **Configuratie toevoegen** om het venster **uitvoeren/configuraties voor fout opsporing** te openen.

   ![HDI IntelliJ configuratie toevoegen](./media/apache-spark-intellij-tool-failure-debug/hdinsight-add-new-configuration.png)

2. Selecteer in het dialoog venster **configuraties voor uitvoeren/fout opsporing** het plus teken ( **+** ). Selecteer vervolgens de optie **Apache Spark op HDInsight** .

   ![Nieuwe configuratie IntelliJ toevoegen](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-new-configuraion-01.png)

3. Overschakelen naar **extern uitvoeren op** het tabblad cluster. Voer informatie in voor de **naam**, het **Spark-cluster** en de naam van de **hoofd klasse**. Onze hulp middelen ondersteunen debug met **uitvoerende** software. De **numExectors**, de standaard waarde is 5 en u kunt beter niet hoger dan 3 instellen. U kunt de uitvoerings tijd verminderen door **Spark. garens. maxAppAttempts** toe te voegen aan de **taak configuraties** en de waarde in te stellen op 1. Klik op de knop **OK** om de configuratie op te slaan.

   ![IntelliJ voor fout opsporing uitvoeren](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-new-configuraion-002.png)

4. De configuratie wordt nu opgeslagen met de naam die u hebt ingevoerd. Als u de configuratie gegevens wilt weer geven, selecteert u de naam van de configuratie. Als u wijzigingen wilt aanbrengen, selecteert u **configuraties bewerken**.

5. Nadat u de configuratie-instellingen hebt voltooid, kunt u het project uitvoeren op het externe cluster.

   ![Knop voor externe uitvoering van IntelliJ fout opsporing voor externe Spark-taak](./media/apache-spark-intellij-tool-failure-debug/hdinsight-local-run-configuration.png)

6. U kunt de toepassings-ID controleren vanuit het venster uitvoer.

   ![Resultaat van externe uitvoering van externe Spark-taak IntelliJ fout opsporing](./media/apache-spark-intellij-tool-failure-debug/hdinsight-remotely-run-result.png)

## <a name="download-failed-job-profile"></a>Het taak profiel kan niet worden gedownload

Als het verzenden van de taak mislukt, kunt u het mislukte taak profiel downloaden naar de lokale computer voor verdere fout opsporing.

1. Open **Microsoft Azure Storage Explorer**, zoek het HDInsight-account van het cluster voor de mislukte taak, down load de mislukte taak resources van de overeenkomstige locatie: **\hdp\spark2-events \\ . Spark \\ \<application ID> -failures** naar een lokale map. In het venster **activiteiten** wordt de voortgang van het downloaden weer gegeven.

   ![Fout bij het downloaden van Azure Storage Explorer](./media/apache-spark-intellij-tool-failure-debug/hdinsight-find-spark-file-001.png)

   ![Azure Storage Explorer downloaden is voltooid](./media/apache-spark-intellij-tool-failure-debug/spark-on-cosmos-doenload-file-2.png)

## <a name="configure-local-debugging-environment-and-debug-on-failure"></a>Lokale debugging omgeving configureren en fout opsporing bij fout

1. Open het oorspronkelijke project of maak een nieuw project en koppel dit aan de oorspronkelijke bron code. Er wordt alleen een versie van Spark 2.3.2 ondersteund voor fout opsporing.

1. In IntelliJ-idee maakt u een configuratie bestand voor fout **opsporing van Spark-fouten** , selecteert u het bestand FTD van de eerder gedownloade mislukte taak resources voor het veld Locatie van Spark- **taak fout context** .

   ![configuratie van Crete-fout](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-failure-configuration-01.png)

1. Klik op de knop lokaal uitvoeren op de werk balk, de fout wordt weer gegeven in het venster uitvoeren.

   ![Run-failure-configuration1](./media/apache-spark-intellij-tool-failure-debug/local-run-failure-configuraion-01.png)

   ![Run-failure-configuration2](./media/apache-spark-intellij-tool-failure-debug/local-run-failure-configuration.png)

1. Stel het afbreek punt in als het logboek aangeeft en klik vervolgens op lokale debug-knop om lokale fout opsporing uit te voeren, net als uw normale scala/Java-projecten in IntelliJ.

1. Als het project is voltooid, kunt u de mislukte taak opnieuw verzenden naar uw Spark in HDInsight-cluster.

## <a name="next-steps"></a><a name="seealso"></a>Volgende stappen

* [Overzicht: fouten opsporen Apache Spark toepassingen](apache-spark-intellij-tool-debug-remotely-through-ssh.md)

### <a name="demo"></a>Demo

* Scala-project maken (video): [Apache Spark scala-toepassingen maken](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)
* Externe fout opsporing (video): [Azure-Toolkit voor IntelliJ gebruiken om fouten op te sporen in Apache Spark toepassingen op afstand in een HDInsight-cluster](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)

### <a name="scenarios"></a>Scenario's

* [Apache Spark met BI: interactieve gegevens analyse met behulp van Spark in HDInsight met BI-hulpprogram ma's](apache-spark-use-bi-tools.md)
* [Apache Spark met Machine Learning: Spark in HDInsight gebruiken voor het analyseren van de gebouw temperatuur met behulp van HVAC-gegevens](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark met Machine Learning: Spark in HDInsight gebruiken om voedsel inspectie resultaten te voors pellen](apache-spark-machine-learning-mllib-ipython.md)
* [Analyse van website logboeken met Apache Spark in HDInsight](./apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Toepassingen maken en uitvoeren

* [Een zelfstandige toepassing maken met behulp van Scala](./apache-spark-create-standalone-application.md)
* [Apache Livy gebruiken om taken op afstand uit te voeren in een Apache Spark-cluster](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Tools en uitbreidingen

* [Azure-toolkit voor IntelliJ gebruiken om Apache Spark-toepassingen voor een HDInsight-cluster te maken](apache-spark-intellij-tool-plugin.md)
* [Azure-toolkit voor IntelliJ gebruiken om fouten op te lossen Apache Spark toepassingen op afstand via VPN](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [HDInsight-Hulpprogram Ma's gebruiken voor IntelliJ met Hortonworks sandbox](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)
* [Gebruik HDInsight-Hulpprogram Ma's in Azure-toolkit voor Eclipse om Apache Spark-toepassingen te maken](./apache-spark-eclipse-tool-plugin.md)
* [Apache Zeppelin-notebooks gebruiken met een Apache Spark-cluster in HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernels die beschikbaar zijn voor Jupyter Notebook in het Apache Spark cluster voor HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Externe pakketten gebruiken met Jupyter-notebooks](apache-spark-jupyter-notebook-use-external-packages.md)
* [Jupyter op uw computer installeren en verbinding maken met een HDInsight Spark-cluster](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Resources beheren

* [Resources beheren voor het Apache Spark-cluster in Azure HDInsight](apache-spark-resource-manager.md)
* [Taken die worden uitgevoerd in een Apache Spark-cluster in HDInsight, traceren en er fouten in oplossen](apache-spark-job-debugging.md)