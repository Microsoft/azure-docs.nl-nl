---
title: 'Azure Toolkit: fouten opsporen Apache Spark apps extern-Azure HDInsight'
description: Meer informatie over het gebruik van HDInsight-Hulpprogram Ma's in Azure-toolkit voor IntelliJ voor het op afstand opsporen van Spark-toepassingen die worden uitgevoerd op HDInsight-clusters via VPN.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 11/28/2017
ms.openlocfilehash: 1daf59c47bf71ad019011036bf42bf6727754345
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98940348"
---
# <a name="use-azure-toolkit-for-intellij-to-debug-apache-spark-applications-remotely-in-hdinsight-through-vpn"></a>Azure-toolkit voor IntelliJ gebruiken om fout opsporing uit te Apache Spark toepassingen op afstand in HDInsight via VPN

U kunt het beste op afstand fouten opsporen [Apache Spark](https://spark.apache.org/) toepassingen via SSH. Zie voor instructies [externe fout opsporing Apache Spark toepassingen op een HDInsight-cluster met Azure-Toolkit voor IntelliJ via SSH](./apache-spark-intellij-tool-debug-remotely-through-ssh.md).

In dit artikel vindt u stapsgewijze richt lijnen voor het gebruik van de HDInsight-Hulpprogram Ma's in Azure-toolkit voor IntelliJ om een Spark-taak te verzenden in een HDInsight Spark-cluster en vervolgens op afstand fouten op te sporen vanaf uw desktop computer. Als u deze taken wilt uitvoeren, moet u de volgende stappen op hoog niveau uitvoeren:

1. Een site-naar-site-of punt-naar-site-virtueel Azure-netwerk maken. Voor de stappen in dit document wordt ervan uitgegaan dat u een site-naar-site-netwerk gebruikt.
1. Maak een Spark-cluster in HDInsight dat deel uitmaakt van het site-naar-site-virtuele netwerk.
1. Controleer de verbinding tussen het hoofd knooppunt van het cluster en het bureau blad.
1. Maak een scala-toepassing in IntelliJ idee en configureer deze voor externe fout opsporing.
1. De toepassing uitvoeren en fouten opsporen.

## <a name="prerequisites"></a>Vereisten

* **Een Azure-abonnement**. Zie [een gratis proef versie van Azure downloaden](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)voor meer informatie.
* **Een Apache Spark cluster in HDInsight**. Zie [Apache Spark-clusters maken in Azure HDInsight](apache-spark-jupyter-spark-sql.md) voor instructies.
* **Oracle Java Development Kit**. U kunt deze installeren vanaf de [Oracle-website](/azure/developer/java/fundamentals/java-jdk-long-term-support).
* **IntelliJ-idee**. In dit artikel wordt versie 2017,1 gebruikt. U kunt deze installeren vanaf de [JetBrains-website](https://www.jetbrains.com/idea/download/).
* **HDInsight-Hulpprogram ma's in azure-Toolkit voor IntelliJ**. HDInsight-hulpprogram ma's voor IntelliJ zijn beschikbaar als onderdeel van Azure-toolkit voor IntelliJ. Zie [install Azure-Toolkit voor IntelliJ](/java/azure/intellij/azure-toolkit-for-intellij-installation)voor instructies over het installeren van Azure Toolkit.
* **Meld u aan bij uw Azure-abonnement vanuit het IntelliJ-idee**. Volg de instructies in het [Azure-Toolkit voor IntelliJ gebruiken om Apache Spark-toepassingen voor een HDInsight-cluster te maken](apache-spark-intellij-tool-plugin.md).
* **Tijdelijke uitzonde ring**. Tijdens het uitvoeren van de Spark scala-toepassing voor externe fout opsporing op een Windows-computer, kan er een uitzonde ring optreden. Deze uitzonde ring wordt uitgelegd in [Spark-2356](https://issues.apache.org/jira/browse/SPARK-2356) en treedt op als gevolg van een ontbrekend WinUtils.exe-bestand in Windows. U kunt deze fout omzeilen door [Winutils.exe](https://github.com/steveloughran/winutils) te downloaden naar een locatie zoals **C:\WinUtils\bin**. Voeg een **HADOOP_HOME** omgevings variabele toe en stel de waarde van de variabele in op **C\WinUtils**.

## <a name="step-1-create-an-azure-virtual-network"></a>Stap 1: een virtueel Azure-netwerk maken

Volg de instructies van de volgende koppelingen voor het maken van een virtueel Azure-netwerk en controleer vervolgens de connectiviteit tussen uw desktop computer en het virtuele netwerk:

* [Maak een VNet met een site-naar-site-VPN-verbinding met behulp van de Azure Portal](../../vpn-gateway/tutorial-site-to-site-portal.md)
* [Een VNet maken met een site-naar-site-VPN-verbinding met behulp van Power shell](../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)
* [Een punt-naar-site-verbinding met een virtueel netwerk configureren met behulp van Power shell](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

## <a name="step-2-create-an-hdinsight-spark-cluster"></a>Stap 2: een HDInsight Spark-cluster maken

Het is raadzaam om ook een Apache Spark cluster in azure HDInsight te maken dat deel uitmaakt van het virtuele Azure-netwerk dat u hebt gemaakt. Gebruik de informatie die beschikbaar is in op [Linux gebaseerde clusters maken in HDInsight](../hdinsight-hadoop-provision-linux-clusters.md). Selecteer als onderdeel van de optionele configuratie het virtuele Azure-netwerk dat u in de vorige stap hebt gemaakt.

## <a name="step-3-verify-the-connectivity-between-the-cluster-head-node-and-your-desktop"></a>Stap 3: de connectiviteit tussen het hoofd knooppunt van het cluster en het bureau blad controleren

1. Haal het IP-adres van het hoofd knooppunt op. Open de Ambari-gebruikers interface voor het cluster. Selecteer op de Blade cluster de optie **dash board**.

    ![Dash board selecteren in Apache Ambari](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/launch-apache-ambari.png)

1. Selecteer **hosts** in de Ambari-gebruikers interface.

    ![Hosts in Apache Ambari selecteren](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/apache-ambari-hosts1.png)

1. U ziet een lijst met hoofd knooppunten, Worker-knoop punten en Zookeeper-knoop punten. De hoofd knooppunten hebben een voor voegsel van **HN** _. Selecteer het eerste hoofd knooppunt.

    ![Het hoofd knooppunt zoeken in Apache Ambari](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/ambari-cluster-headnodes.png)

1. In het deel venster _ *samen vatting** onder aan de pagina die wordt geopend, kopieert u het **IP-adres** van het hoofd knooppunt en de **hostnaam**.

    ![Het IP-adres zoeken in Apache Ambari](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/headnode-ip-address1.png)

1. Voeg het IP-adres en de hostnaam van het hoofd knooppunt toe aan het **hosts** -bestand op de computer waarop u wilt uitvoeren en op afstand fouten opsporen in de Spark-taak. Hierdoor kunt u met het hoofd knooppunt communiceren met behulp van het IP-adres en de hostnaam.

   a. Open een Klad blok-bestand met verhoogde machtigingen. Selecteer in het menu **bestand** de optie **openen** en zoek vervolgens de locatie van het hosts-bestand. Op een Windows-computer is de locatie **C:\Windows\System32\Drivers\etc\hosts**.

   b. Voeg de volgende informatie toe aan het **hosts** -bestand:

    ```
    # For headnode0
    192.xxx.xx.xx nitinp
    192.xxx.xx.xx nitinp.lhwwghjkpqejawpqbwcdyp3.gx.internal.cloudapp.net
    
    # For headnode1
    192.xxx.xx.xx nitinp
    192.xxx.xx.xx nitinp.lhwwghjkpqejawpqbwcdyp3.gx.internal.cloudapp.net
    ```

1. Op de computer die u hebt verbonden met het virtuele Azure-netwerk dat wordt gebruikt door het HDInsight-cluster, controleert u of u de hoofd knooppunten kunt pingen met behulp van het IP-adres en de hostnaam.

1. Gebruik SSH om verbinding te maken met het hoofd knooppunt van het cluster door de instructies te volgen in [verbinding maken met een HDInsight-cluster met behulp van SSH](../hdinsight-hadoop-linux-use-ssh-unix.md). Ping het IP-adres van de desktop computer uit het hoofd knooppunt van het cluster. Controleer de verbinding met de IP-adressen die zijn toegewezen aan de computer:

    - Een voor de netwerk verbinding
    - Een voor het virtuele Azure-netwerk

1. Herhaal de stappen voor het andere hoofd knooppunt.

## <a name="step-4-create-an-apache-spark-scala-application-by-using-hdinsight-tools-in-azure-toolkit-for-intellij-and-configure-it-for-remote-debugging"></a>Stap 4: een Apache Spark scala-toepassing maken met behulp van HDInsight-Hulpprogram Ma's in Azure-toolkit voor IntelliJ en deze configureren voor externe fout opsporing

1. Open IntelliJ-idee en maak een nieuw project. Voer in het dialoogvenster **Nieuw project** de volgende handelingen uit:

    ![Selecteer de nieuwe project sjabloon in IntelliJ-idee](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/create-hdi-scala-app.png)

    a. Selecteer **hdinsight**  >  **Spark in hdinsight (scala)**.

    b. Selecteer **Next**.
1. Voer in het dialoog venster volgende **Nieuw project** de volgende handelingen uit en selecteer vervolgens **volt ooien**:

    - Geef een projectnaam en locatie op.

    - Selecteer in de vervolgkeuzelijst **Project SDK** de optie **Java 1.8** voor het Spark 2.x-cluster of selecteer **Java 1.7** voor het Spark 1.x-cluster.

    - In de vervolg keuzelijst **Spark-versie** integreert de wizard scala project maken de juiste versie voor de Spark SDK en de scala SDK. Selecteer **Spark 1.x** als de Spark-clusterversie ouder is dan 2.0. Selecteer anders **Spark 2.x**. In dit voorbeeld wordt **Spark 2.0.2 (Scala 2.11.8)** gebruikt.
  
   ![Selecteer de Project-SDK en Spark-versie](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/hdi-scala-project-details.png)
  
1. Het Spark-project maakt automatisch een artefact voor u. Ga als volgt te werk om het artefact weer te geven:

    a. Selecteer in het menu **bestand** de optie **project structuur**.

    b. Selecteer **artefacten** in het dialoog venster **project structuur** om het standaard artefact te bekijken dat wordt gemaakt. U kunt ook uw eigen artefact maken door het plus teken () te selecteren **+** .

   ![IntelliJ-ideeën artefacten maken jar](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/create-default-artifact.png)

1. Bibliotheken toevoegen aan uw project. Ga als volgt te werk om een bibliotheek toe te voegen:

    a. Klik met de rechter muisknop op de project naam in de project structuur en selecteer vervolgens **module-instellingen openen**.

    b. In het dialoog venster **project structuur** selecteert u **bibliotheken**, selecteert u het ( **+** )-symbool en selecteert u vervolgens **van Maven**.

    ![Bibliotheek met IntelliJ-ideeën downloaden](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/intellij-add-library.png)

    c. Zoek in het dialoog venster **bibliotheek downloaden van Maven-opslag plaats** naar de volgende bibliotheken en voeg deze toe:

   * `org.scalatest:scalatest_2.10:2.2.1`
   * `org.apache.hadoop:hadoop-azure:2.7.1`

1. Kopieer `yarn-site.xml` en `core-site.xml` van het hoofd knooppunt van het cluster en voeg deze toe aan het project. Gebruik de volgende opdrachten om de bestanden te kopiëren. U kunt [Cygwin](https://cygwin.com/install.html) gebruiken om de volgende `scp` opdrachten uit te voeren om de bestanden van de cluster hoofd knooppunten te kopiëren:

    ```bash
    scp <ssh user name>@<headnode IP address or host name>://etc/hadoop/conf/core-site.xml .
    ```

    Omdat we het IP-adres van het hoofd knooppunt van het cluster en de hostnamen voor het bestand hosts op het bureau blad al hebben toegevoegd, kunnen we de `scp` opdrachten op de volgende manier gebruiken:

    ```bash
    scp sshuser@nitinp:/etc/hadoop/conf/core-site.xml .
    scp sshuser@nitinp:/etc/hadoop/conf/yarn-site.xml .
    ```

    Als u deze bestanden wilt toevoegen aan uw project, kopieert u deze onder de map **/src** in de project structuur, bijvoorbeeld `<your project directory>\src` .

1. Werk het `core-site.xml` bestand bij om de volgende wijzigingen door te voeren:

   a. Vervang de versleutelde sleutel. Het `core-site.xml` bestand bevat de versleutelde sleutel voor het opslag account dat aan het cluster is gekoppeld. Vervang in het `core-site.xml` bestand dat u hebt toegevoegd aan het project de versleutelde sleutel door de werkelijke opslag sleutel die aan het standaard opslag account is gekoppeld. Zie [Toegangssleutels voor een opslagaccount beheren](../../storage/common/storage-account-keys-manage.md) voor meer informatie.

    ```xml
    <property>
            <name>fs.azure.account.key.hdistoragecentral.blob.core.windows.net</name>
            <value>access-key-associated-with-the-account</value>
    </property>
    ```

   b. Verwijder de volgende vermeldingen uit `core-site.xml` :

    ```xml
    <property>
            <name>fs.azure.account.keyprovider.hdistoragecentral.blob.core.windows.net</name>
            <value>org.apache.hadoop.fs.azure.ShellDecryptionKeyProvider</value>
    </property>

    <property>
            <name>fs.azure.shellkeyprovider.script</name>
            <value>/usr/lib/python2.7/dist-packages/hdinsight_common/decrypt.sh</value>
    </property>

    <property>
            <name>net.topology.script.file.name</name>
            <value>/etc/hadoop/conf/topology_script.py</value>
    </property>
    ```

   c. Sla het bestand op.

1. Voeg de hoofd klasse voor uw toepassing toe. Klik in de **project Verkenner** met de rechter muisknop op **src**, wijs **Nieuw** aan en selecteer **scala klasse**.

    ![IntelliJ-idee de hoofd klasse selecteren](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/hdi-spark-scala-code.png)

1. Geef in het dialoog venster **nieuwe scala-klasse maken** een naam op, selecteer **object** in het vak **kind** en selecteer vervolgens **OK**.

    ![IntelliJ-idee nieuwe scala-klasse maken](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/hdi-spark-scala-code-object.png)

1. `MyClusterAppMain.scala`Plak de volgende code in het bestand. Met deze code wordt de Spark-context gemaakt en wordt een `executeJob` methode vanuit het `SparkSample` object geopend.

    ```scala
    import org.apache.spark.{SparkConf, SparkContext}

    object SparkSampleMain {
        def main (arg: Array[String]): Unit = {
        val conf = new SparkConf().setAppName("SparkSample")
                                    .set("spark.hadoop.validateOutputSpecs", "false")
        val sc = new SparkContext(conf)

        SparkSample.executeJob(sc,
                            "wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv",
                            "wasb:///HVACOut")
        }
    }
    ```

1. Herhaal stap 8 en 9 om een nieuw scala-object met de naam toe te voegen `*SparkSample` . Voeg de volgende code toe aan deze klasse. Deze code leest de gegevens van de HVAC.csv (beschikbaar in alle HDInsight Spark-clusters). Hiermee worden de rijen die slechts één cijfer bevatten in de zevende kolom in het CSV-bestand opgehaald en wordt de uitvoer naar **/HVACOut** onder de standaard opslag container voor het cluster geschreven.

    ```scala
    import org.apache.spark.SparkContext

    object SparkSample {
        def executeJob (sc: SparkContext, input: String, output: String): Unit = {
        val rdd = sc.textFile(input)

        //find the rows which have only one digit in the 7th column in the CSV
        val rdd1 =  rdd.filter(s => s.split(",")(6).length() == 1)

        val s = sc.parallelize(rdd.take(5)).cartesian(rdd).count()
        println(s)

        rdd1.saveAsTextFile(output)
        //rdd1.collect().foreach(println)
         }
    }
    ```

1. Herhaal stap 8 en 9 om een nieuwe klasse met de naam toe te voegen `RemoteClusterDebugging` . Deze klasse implementeert het Spark-test raamwerk dat wordt gebruikt voor het opsporen van fouten in de toepassingen. Voeg de volgende code toe aan de klasse `RemoteClusterDebugging`:

    ```scala
        import org.apache.spark.{SparkConf, SparkContext}
        import org.scalatest.FunSuite

        class RemoteClusterDebugging extends FunSuite {

         test("Remote run") {
           val conf = new SparkConf().setAppName("SparkSample")
                                     .setMaster("yarn-client")
                                     .set("spark.yarn.am.extraJavaOptions", "-Dhdp.version=2.4")
                                     .set("spark.yarn.jar", "wasb:///hdp/apps/2.4.2.0-258/spark-assembly-1.6.1.2.4.2.0-258-hadoop2.7.1.2.4.2.0-258.jar")
                                     .setJars(Seq("""C:\workspace\IdeaProjects\MyClusterApp\out\artifacts\MyClusterApp_DefaultArtifact\default_artifact.jar"""))
                                     .set("spark.hadoop.validateOutputSpecs", "false")
           val sc = new SparkContext(conf)

           SparkSample.executeJob(sc,
             "wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv",
             "wasb:///HVACOut")
         }
        }
    ```

     Er zijn enkele belang rijke dingen die u moet weten:

      * `.set("spark.yarn.jar", "wasb:///hdp/apps/2.4.2.0-258/spark-assembly-1.6.1.2.4.2.0-258-hadoop2.7.1.2.4.2.0-258.jar")`Zorg er voor dat de Spark-assembly jar beschikbaar is op de cluster opslag op het opgegeven pad.
      * `setJars`Geef voor de locatie op waar het artefact jar is gemaakt. Normaal gesp roken is het `<Your IntelliJ project directory>\out\<project name>_DefaultArtifact\default_artifact.jar` .

1. Klik in de `*RemoteClusterDebugging` klasse met de rechter muisknop op het `test` tref woord en selecteer vervolgens **RemoteClusterDebugging-configuratie maken**.

    ![IntelliJ-idee een externe configuratie maken](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/create-remote-config.png)

1. Geef in het dialoog venster **RemoteClusterDebugging-configuratie maken** een naam op voor de configuratie en selecteer vervolgens **test soort** als **test naam**. Alle andere waarden behouden als de standaard instellingen. Selecteer **Apply** en vervolgens **OK**.

    ![RemoteClusterDebugging-configuratie maken](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/provide-config-value.png)

1. Nu ziet u een vervolg keuzelijst configuratie van **extern uitvoeren** in de menu balk.

    ![De vervolg keuzelijst externe uitvoering IntelliJ](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/intellij-config-remote-run.png)

## <a name="step-5-run-the-application-in-debug-mode"></a>Stap 5: de toepassing uitvoeren in de foutopsporingsmodus

1. Open in uw IntelliJ-idee project `SparkSample.scala` een onderbrekings punt naast `val rdd1` . Selecteer in het pop-upmenu **onderbrekings punt maken voor** **regel in functie executeJob**.

    ![IntelliJ-idee een onderbrekings punt toevoegen](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/intellij-create-breakpoint.png)

1. Als u de toepassing wilt uitvoeren, selecteert u de knop **debug run** naast de vervolg keuzelijst configuratie van **externe uitvoering** .

    ![IntelliJ-idee de knop voor het uitvoeren van fout opsporing selecteren](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-run-mode-button.png)

1. Wanneer de uitvoering van het programma het onderbrekings punt bereikt, ziet u een tabblad **fout opsporing** in het onderste deel venster.

    ![IntelliJ-idee het tabblad fout opsporing weer geven](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/intellij-debugger-tab.png)

1. Als u een horloge wilt toevoegen, selecteert u het **+** pictogram ().

    ![IntelliJ debug-add-Watch-variabele](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-add-watch-variable.png)

    In dit voor beeld wordt de toepassing gerepareerd voordat de variabele `rdd1` is gemaakt. Met deze controle kunnen we de eerste vijf rijen in de variabele zien `rdd` . Druk op **Enter**.

    ![IntelliJ het programma uitvoeren in de foutopsporingsmodus](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-add-watch-variable-value.png)

    Wat u in de vorige afbeelding ziet, is dat tijdens runtime u kunt een query uitvoeren op terabytes aan gegevens en fouten opsporen in de voortgang van de uitvoering van uw toepassing. In de uitvoer die wordt weer gegeven in de vorige afbeelding ziet u bijvoorbeeld dat de eerste rij van de uitvoer een koptekst is. Op basis van deze uitvoer kunt u de toepassings code zo wijzigen dat de koprij, indien nodig, wordt overgeslagen.

1. U kunt nu het pictogram **programma hervatten** selecteren om verder te gaan met het uitvoeren van de toepassing.

    ![IntelliJ-idee programma hervatten selecteren](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-continue-remote-run.png)

1. Als de toepassing is voltooid, ziet u uitvoer als de volgende:

    ![Uitvoer van IntelliJ-ideeën debugger console](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-complete-window.png)

## <a name="next-steps"></a><a name="seealso"></a>Volgende stappen

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
* [Azure-toolkit voor IntelliJ gebruiken om fouten op te lossen Apache Spark toepassingen op afstand via SSH](apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Gebruik HDInsight-Hulpprogram Ma's in Azure-toolkit voor Eclipse om Apache Spark-toepassingen te maken](./apache-spark-eclipse-tool-plugin.md)
* [Apache Zeppelin-notebooks gebruiken met een Apache Spark-cluster in HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernels die beschikbaar zijn voor Jupyter Notebook in een Apache Spark cluster voor HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Externe pakketten gebruiken met Jupyter-notebooks](apache-spark-jupyter-notebook-use-external-packages.md)
* [Jupyter op uw computer installeren en verbinding maken met een HDInsight Spark-cluster](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Resources beheren

* [Resources beheren voor het Apache Spark-cluster in Azure HDInsight](apache-spark-resource-manager.md)
* [Taken bijhouden en fouten opsporen die worden uitgevoerd op een Apache Spark cluster in HDInsight](apache-spark-job-debugging.md)