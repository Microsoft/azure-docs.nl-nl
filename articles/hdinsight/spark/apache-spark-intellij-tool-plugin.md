---
title: 'Azure-toolkit voor IntelliJ: Spark-app-HDInsight'
description: Gebruik de Azure-toolkit voor IntelliJ voor het ontwikkelen van Spark-toepassingen die zijn geschreven in scala en verzend deze naar een HDInsight Spark-cluster.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 04/14/2020
ms.openlocfilehash: 29fb96dc83ada329910844506838dee461321343
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104866332"
---
# <a name="use-azure-toolkit-for-intellij-to-create-apache-spark-applications-for-hdinsight-cluster"></a>Azure-toolkit voor IntelliJ gebruiken om Apache Spark-toepassingen voor een HDInsight-cluster te maken

In dit artikel wordt beschreven hoe u Apache Spark-toepassingen kunt ontwikkelen in azure HDInsight met behulp van de **Azure Toolkit** -invoeg toepassing voor de IntelliJ IDE. [Azure HDInsight](../hdinsight-overview.md) is een beheerde, open-source analyse service in de Cloud. Met de service kunt u open-source frameworks zoals Hadoop, Apache Spark, Apache Hive en Apache Kafka gebruiken.

U kunt de **Azure Toolkit** -invoeg toepassing op een paar manieren gebruiken:

* Ontwikkel en verzend een scala Spark-toepassing naar een HDInsight Spark-cluster.
* Toegang tot uw Azure HDInsight Spark cluster resources.
* Ontwikkel een Scala Spark-toepassing en voer deze lokaal uit.

In dit artikel leert u het volgende:
> [!div class="checklist"]
> * De invoegtoepassing Azure-toolkit voor IntelliJ gebruiken
> * Apache Spark-toepassingen ontwikkelen
> * Een toepassing verzenden naar een Azure HDInsight-cluster

## <a name="prerequisites"></a>Vereisten

* Een Apache Spark-cluster in HDInsight. Zie [Apache Spark-clusters maken in Azure HDInsight](apache-spark-jupyter-spark-sql.md) voor instructies.

* [Oracle Java Development Kit](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).  In dit artikel wordt gebruikgemaakt van Java versie 8.0.202.

* IntelliJ-idee. In dit artikel wordt [IntelliJ IDEA Community versie  2018.3.4](https://www.jetbrains.com/idea/download/).

* Azure-toolkit voor IntelliJ.  Zie [Installing the Azure Toolkit for IntelliJ](/azure/developer/java/toolkit-for-intellij/) (De Azure Toolkit voor IntelliJ installeren).

## <a name="install-scala-plugin-for-intellij-idea"></a>Scala-invoegtoepassing voor IntelliJ IDEA installeren

Stappen voor het installeren van de scala-invoeg toepassing:

1. Open IntelliJ IDEA.

2. Ga op het welkomstscherm naar **Configure** > **Plugins** om het venster **Plugins** te openen.

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin/enable-scala-plugin1.png" alt-text="IntelliJ-idee maakt scala-invoeg toepassing mogelijk" border="true":::

3. Selecteer **Install** voor de Scala-invoegtoepassing die in het nieuwe venster wordt weergegeven.  

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin/install-scala-plugin.png" alt-text="IntelliJ-idee installeert scala-invoeg toepassing" border="true":::

4. Als de invoegtoepassing is geïnstalleerd, moet u de IDE opnieuw starten.

## <a name="create-a-spark-scala-application-for-an-hdinsight-spark-cluster"></a>Een Spark scala-toepassing maken voor een HDInsight Spark-cluster

1. Start IntelliJ IDEA en selecteer **Create New Project** om het venster **New Project** te openen.

2. Selecteer **Azure Spark/HDInsight** in het linkerdeelvenster.

3. Selecteer **Spark Project (Scala)** in het hoofdvenster.

4. Selecteer een van de volgende opties in de vervolg keuzelijst **Build Tool** :
   * **Maven**, voor de ondersteuning van de wizard Scala-project maken.
   * **SBT**, voor het beheren van de afhankelijkheden en het maken van het Scala-project.

     :::image type="content" source="./media/apache-spark-intellij-tool-plugin/create-hdi-scala-app.png" alt-text="IntelliJ IDEA-dialoogvenster Nieuw project" border="true":::

5. Selecteer **Next**.

6. Geef in het venster **New project** de volgende gegevens op:  

    |  Eigenschap   | Beschrijving   |  
    | ----- | ----- |  
    |Projectnaam| Voer een naam in.  In dit artikel wordt gebruikgemaakt van `myApp` .|  
    |Project&nbsp;location| Voer de locatie in om uw project in op te slaan.|
    |Project SDK| Dit veld is mogelijk leeg in het eerste gebruik van het idee.  Selecteer **New...** en ga naar uw JDK.|
    |Spark-versie|De wizard voor het maken van het project integreert de juiste versie voor Spark SDK en Scala SDK. Selecteer **Spark 1.x** als de Spark-clusterversie ouder is dan 2.0. Selecteer anders **Spark 2.x**. In dit voorbeeld wordt **Spark 2.3.0 (Scala 2.11.8)** gebruikt.|

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin/intellij-new-project.png" alt-text="De Apache Spark SDK selecteren" border="true":::

7. Selecteer **Finish**.  Het kan enkele minuten duren voordat het project beschikbaar wordt.

8. Het Spark-project maakt automatisch een artefact voor u. Voer de volgende stappen uit om het artefact weer te geven:

   a. Navigeer in de menubalk naar **Bestand** > **Projectstructuur...** .

   b. Selecteer in het venster **Projectstructuur** de optie **Artefacten**.  

   c. Selecteer **Annuleren**  nadat u het artefact hebt weer gegeven.

      :::image type="content" source="./media/apache-spark-intellij-tool-plugin/default-artifact-dialog.png" alt-text="Artefactgegevens in het dialoogvenster" border="true":::

9. Voeg de bron code van uw toepassing toe door de volgende stappen uit te voeren:

    a. Ga vanuit Project naar **Mijntoep**  >  **src**  >  **Main**  >  **scala**.  

    b. Klik met de rechter muisknop op **scala** en navigeer vervolgens naar de **nieuwe**  >  **scala-klasse**.

   :::image type="content" source="./media/apache-spark-intellij-tool-plugin/hdi-spark-scala-code.png" alt-text="Opdrachten voor het maken van een Scala-klasse uit Project" border="true":::

   c. Geef in het dialoog venster **nieuwe scala-klasse maken** een naam op, selecteer **object** in de vervolg keuzelijst **type** en selecteer vervolgens **OK**.

     :::image type="content" source="./media/apache-spark-intellij-tool-plugin/hdi-spark-scala-code-object.png" alt-text="Het dialoog venster nieuwe scala-klasse maken" border="true":::

   d. Het bestand **MyApp. scala** wordt vervolgens in de hoofd weergave geopend. Vervang de standaard code door de code die hieronder wordt gevonden:  

    ```scala
    import org.apache.spark.SparkConf
    import org.apache.spark.SparkContext

    object myApp{
        def main (arg: Array[String]): Unit = {
        val conf = new SparkConf().setAppName("myApp")
        val sc = new SparkContext(conf)

        val rdd = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

        //find the rows that have only one digit in the seventh column in the CSV file
        val rdd1 =  rdd.filter(s => s.split(",")(6).length() == 1)

        rdd1.saveAsTextFile("wasbs:///HVACOut")
        }

    }
    ```

    De code leest de gegevens van HVAC.csv (beschikbaar op alle HDInsight Spark-clusters), haalt de rijen op die slechts één cijfer bevatten in de zevende kolom in het CSV-bestand en schrijft de uitvoer naar `/HVACOut` onder de standaard opslag container voor het cluster.

## <a name="connect-to-your-hdinsight-cluster"></a>Verbinding maken met uw HDInsight-cluster

De gebruiker kan [zich aanmelden bij het Azure-abonnement](#sign-in-to-your-azure-subscription)of [een HDInsight-cluster koppelen](#link-a-cluster). Gebruik de referenties Ambari gebruikers naam/wacht woord of domein toegevoegd aan om verbinding te maken met uw HDInsight-cluster.

### <a name="sign-in-to-your-azure-subscription"></a>Aanmelden bij uw Azure-abonnement

1. Navigeer in de menubalk naar **Beeld** > **Hulpprogrammavensters** > **Azure Explorer**.

   :::image type="content" source="./media/apache-spark-intellij-tool-plugin/show-azure-explorer1.png" alt-text="IntelliJ-idee toont Azure Explorer" border="true":::

2. Klik in Azure Explorer met de rechtermuisknop op het knooppunt **Azure** en selecteer **Aanmelden**.

   :::image type="content" source="./media/apache-spark-intellij-tool-plugin/explorer-rightclick-azure.png" alt-text="IntelliJ-ideeën Verkenner met de rechter muisknop op Azure" border="true":::

3. Kies in het dialoogvenster **Azure-aanmelding** de optie **Apparaataanmelding** en selecteer **Aanmelden**.

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin/intellij-view-explorer2.png" alt-text="' IntelliJ-idee Azure-aanmeldings apparaat aanmelding" border="true":::

4. Klik in het dialoogvenster **Azure-apparaataanmelding** op **Kopiëren en openen**.

   :::image type="content" source="./media/apache-spark-intellij-tool-plugin/intellij-view-explorer5.png" alt-text="' IntelliJ-idee Azure-apparaat aanmelding" border="true":::

5. Plak de code in de browserinterface en klik op **Volgende**.

   :::image type="content" source="./media/apache-spark-intellij-tool-plugin/intellij-view-explorer6.png" alt-text="' Micro soft code-dialoog venster invoeren voor HDI '" border="true":::

6. Voer uw referenties voor Azure in en sluit de browser.

   :::image type="content" source="./media/apache-spark-intellij-tool-plugin/intellij-view-explorer7.png" alt-text="' Micro soft e-mail dialoog venster voor HDI '" border="true":::

7. Zodra u bent aangemeld, toont het dialoogvenster **Abonnementen selecteren** alle Azure-abonnementen die aan de referenties zijn gekoppeld. Selecteer uw abonnement en selecteer vervolgens de knop **selecteren** .

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin/Select-Subscriptions.png" alt-text="Het dialoogvenster Abonnementen selecteren" border="true":::

8. Vouw in **Azure Verkenner** **hdinsight** uit om de hdinsight Spark-clusters in uw abonnementen weer te geven.

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin/intellij-view-explorer3.png" alt-text="Azure Explorer-hoofdweergave in IntelliJ IDEA" border="true":::

9. Als u de resources wilt weer geven (bijvoorbeeld opslag accounts) die aan het cluster zijn gekoppeld, kunt u een cluster naam knooppunt verder uitbreiden.

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin/intellij-view-explorer4.png" alt-text="Azure Explorer-opslagaccounts" border="true":::

### <a name="link-a-cluster"></a>Een cluster koppelen

U kunt een HDInsight-cluster koppelen met behulp van de Apache Ambari Managed username. Op dezelfde manier kunt u voor een HDInsight-cluster dat is gekoppeld aan een domein een koppeling maken met behulp van het domein en de gebruikers naam, zoals `user1@contoso.com` . U kunt ook een livy-service cluster koppelen.

1. Navigeer in de menubalk naar **Beeld** > **Hulpprogrammavensters** > **Azure Explorer**.

1. Klik in azure Verkenner met de rechter muisknop op het **HDInsight** -knoop punt en selecteer vervolgens **koppelen van een cluster**.

   :::image type="content" source="./media/apache-spark-intellij-tool-plugin/link-a-cluster-context-menu.png" alt-text="Context menu van Azure Explorer-koppelings cluster" border="true":::

1. De beschik bare opties in de **koppeling een cluster** venster variëren, afhankelijk van welke waarde u selecteert in de vervolg keuzelijst **resource type koppelen** .  Voer uw waarden in en selecteer **OK**.

    * **HDInsight-cluster**  
  
        |Eigenschap |Waarde |
        |----|----|
        |Resource type voor koppeling|Selecteer **HDInsight-cluster** in de vervolg keuzelijst.|
        |Cluster naam/URL| Voer de cluster naam in.|
        |Verificatietype| Als **basis verificatie** laten staan|
        |Gebruikersnaam| Voer de gebruikers naam van het cluster in, de standaard instelling is admin.|
        |Wachtwoord| Voer het wacht woord in voor de gebruikers naam.|

        :::image type="content" source="./media/apache-spark-intellij-tool-plugin/link-hdinsight-cluster-dialog.png" alt-text="IntelliJ-idee een cluster dialoogvenster koppelen" border="true":::

    * **Livy-service**  
  
        |Eigenschap |Waarde |
        |----|----|
        |Resource type voor koppeling|Selecteer **livy-service** in de vervolg keuzelijst.|
        |Livy-eind punt| Livy-eind punt invoeren|
        |Clusternaam| Voer de cluster naam in.|
        |Eind punt van garen|Optioneel.|
        |Verificatietype| Als **basis verificatie** laten staan|
        |Gebruikersnaam| Voer de gebruikers naam van het cluster in, de standaard instelling is admin.|
        |Wachtwoord| Voer het wacht woord in voor de gebruikers naam.|

        :::image type="content" source="./media/apache-spark-intellij-tool-plugin/link-livy-cluster-dialog.png" alt-text="Dialoog venster IntelliJ-idee livy-cluster" border="true":::

1. U kunt het gekoppelde cluster bekijken vanuit het **HDInsight** -knoop punt.

   :::image type="content" source="./media/apache-spark-intellij-tool-plugin/hdinsight-linked-cluster.png" alt-text="Gekoppelde cluster1 van Azure Verkenner" border="true":::

1. U kunt ook een cluster ontkoppelen van **Azure Explorer**.

   :::image type="content" source="./media/apache-spark-intellij-tool-plugin/hdi-unlinked-cluster.png" alt-text="Een cluster dat is gekoppeld aan Azure Verkenner" border="true":::

## <a name="run-a-spark-scala-application-on-an-hdinsight-spark-cluster"></a>Een Spark scala-toepassing uitvoeren op een HDInsight Spark-cluster

Nadat u een scala-toepassing hebt gemaakt, kunt u deze verzenden naar het cluster.

1. Navigeer in Project naar **myApp** > **src** > **main** > **scala** > **myApp**.  Klik met de rechter muisknop op **MyApp** en selecteer vervolgens **Spark-toepassing verzenden** (deze staat waarschijnlijk onder aan de lijst).

      :::image type="content" source="./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-1.png" alt-text="De opdracht Spark-toepassing verzenden naar HDInsight" border="true":::

2. Selecteer in het dialoog venster **Spark-toepassing verzenden** de optie **1. Spark in HDInsight**.

3. Geef in het venster **configuratie bewerken** de volgende waarden op en selecteer **OK**:

    |Eigenschap |Waarde |
    |----|----|
    |Spark-clusters (alleen Linux)|Selecteer het HDInsight Spark-cluster waarop u de toepassing wilt uitvoeren.|
    |Selecteer een artefact om te verzenden|Laat de standaardinstelling staan.|
    |Hoofdklassenaam|De standaardwaarde is de hoofdklasse uit het geselecteerde bestand. U kunt de klasse wijzigen door het weglatings teken (**...**) te selecteren.  en kies een andere klasse.|
    |Taakconfiguraties|U kunt de standaard sleutels en-waarden wijzigen. Zie [Apache Livy REST API](https://livy.incubator.apache.org/docs/latest/rest-api.html) voor meer informatie.|
    |Opdrachtregelargumenten|U kunt voor de hoofdklasse argumenten invoeren, gescheiden door een spatie, indien nodig.|
    |JAR’s en bestanden waarnaar wordt verwezen|U kunt de paden invoeren voor de JAR’s en bestanden waarnaar wordt verwezen, indien aanwezig. U kunt ook bladeren door bestanden in het virtuele bestands systeem van Azure, dat momenteel alleen ondersteuning biedt voor ADLS gen 2-cluster. Voor meer informatie: [Apache Spark configuratie](https://spark.apache.org/docs/latest/configuration.html#runtime-environment).  Zie ook [How to upload resources to cluster](../../storage/blobs/storage-quickstart-blobs-storage-explorer.md).|
    |Opslag van taakuploads|Vouw uit om aanvullende opties te onthullen.|
    |Opslagtype|Selecteer **Azure Blob gebruiken om te uploaden** in de vervolgkeuzelijst.|
    |Opslagaccount|Voer uw opslagaccount in.|
    |Opslagsleutel|Voer uw opslagsleutel in.|
    |Opslagcontainer|Selecteer uw opslagcontainer in de vervolgkeuzelijst zodra **Opslagaccount** en **Opslagsleutel** zijn ingevoerd.|

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-02.png" alt-text="Het dialoogvenster voor de verzending van Spark" border="true":::

4. Selecteer **SparkJobRun** om uw project naar het geselecteerde cluster te verzenden. Onderaan het tabblad **Externe Spark-taak in cluster** wordt de voortgang van de taakuitvoering weergegeven. U kunt de toepassing stoppen door op de rode knop te klikken.

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin/hdi-spark-app-result.png" alt-text="Venster voor verzending van Apache Spark" border="true":::

## <a name="debug-apache-spark-applications-locally-or-remotely-on-an-hdinsight-cluster"></a>Fouten opsporen Apache Spark toepassingen lokaal of op afstand op een HDInsight-cluster

We raden ook een andere manier aan om de Spark-toepassing naar het cluster te verzenden. U kunt dit doen door de para meters in te stellen op de IDE voor **uitvoeren/fouten opsporen** . Zie [fouten opsporen Apache Spark toepassingen lokaal of op afstand op een HDInsight-cluster met Azure-Toolkit voor IntelliJ via SSH](apache-spark-intellij-tool-debug-remotely-through-ssh.md).

## <a name="access-and-manage-hdinsight-spark-clusters-by-using-azure-toolkit-for-intellij"></a>HDInsight Spark-clusters openen en beheren met behulp van Azure-toolkit voor IntelliJ

U kunt verschillende bewerkingen uitvoeren met behulp van Azure-toolkit voor IntelliJ.  De meeste bewerkingen worden gestart vanuit **Azure Verkenner**.  Navigeer in de menubalk naar **Beeld** > **Hulpprogrammavensters** > **Azure Explorer**.

### <a name="access-the-job-view"></a>De taak weergave openen

1. Ga in azure Verkenner naar **HDInsight**-  >  \<Your Cluster>  >  **taken**.

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin/intellij-job-view-node.png" alt-text="IntelliJ Azure Explorer-taak weergave knooppunt" border="true":::

2. In het rechterdeel venster worden op het tabblad **Spark-taak weergave** alle toepassingen weer gegeven die op het cluster zijn uitgevoerd. Selecteer de naam van de toepassing waarvoor u meer details wilt weer geven.

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin/intellij-view-job-logs.png" alt-text="Toepassingsdetails weergeven in Spark-taak" border="true":::

3. Beweeg de muis aanwijzer over de taak grafiek om de informatie over elementaire actieve taken weer te geven. Selecteer een knoop punt in de taak grafiek om de fasen diagram en informatie te bekijken die elke taak genereert.

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin/Job-graph-stage-info.png" alt-text="Details taak fase Spark-taak weergave" border="true":::

4. Als u veelgebruikte logboeken wilt weer geven, zoals *stuur programma stderr*, *stuur programma stdout* en *Directory gegevens*, selecteert u het tabblad **logboek** .

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin/intellij-job-log-info.png" alt-text="Logboek Details van Spark-taak weergave" border="true":::

5. U kunt de gebruikers interface van Spark en de gebruikers interface van het (op toepassings niveau) weer geven. Selecteer een koppeling boven aan het venster.

### <a name="access-the-spark-history-server"></a>Toegang tot de Spark-geschiedenis server

1. Vouw in azure Explorer **HDInsight** uit, klik met de rechter muisknop op de naam van uw Spark-cluster en selecteer vervolgens **openen Spark-gebruikers interface**.  
2. Wanneer u hierom wordt gevraagd, voert u de beheerders referenties van het cluster in, die u hebt opgegeven bij het instellen van het cluster.

3. Op het Spark-geschiedenis server dashboard kunt u de naam van de toepassing gebruiken om te zoeken naar de toepassing die u zojuist hebt uitgevoerd. In de voor gaande code stelt u de naam van de toepassing in met behulp van `val conf = new SparkConf().setAppName("myApp")` . De Spark-toepassings naam is **Mijntoep**.

### <a name="start-the-ambari-portal"></a>De Ambari-Portal starten

1. Vouw in azure Explorer **HDInsight** uit, klik met de rechter muisknop op de naam van uw Spark-cluster en selecteer vervolgens **cluster Beheerportal openen (Ambari)**.  

2. Wanneer u hierom wordt gevraagd, voert u de beheerders referenties voor het cluster in. U hebt deze referenties opgegeven tijdens het installatie proces van het cluster.

### <a name="manage-azure-subscriptions"></a>Azure-abonnementen beheren

Azure-toolkit voor IntelliJ worden standaard de Spark-clusters uit al uw Azure-abonnementen weer gegeven. Indien nodig kunt u de abonnementen opgeven die u wilt gebruiken.  

1. Klik in azure Verkenner met de rechter muisknop op het **Azure** -hoofd knooppunt en selecteer vervolgens **abonnementen selecteren**.  

2. Schakel in het venster **abonnementen selecteren** de selectie vakjes uit naast de abonnementen die u niet wilt openen en selecteer vervolgens **sluiten**.

## <a name="spark-console"></a>Spark-console

U kunt de lokale Spark-console (Scala) uitvoeren of de interactieve Spark Livy-sessieconsole (Scala) uitvoeren.

### <a name="spark-local-consolescala"></a>Spark lokale console (scala)

Verzeker dat u aan de WINUTILS.EXE-vereiste voldoet.

1. Navigeer in de menubalk naar **Uitvoeren** > **Configuraties bewerken...** .

2. Ga in het linkerdeel venster van het venster **configuraties voor uitvoeren/fout opsporing** naar **Apache Spark op hdinsight**  >  **[Spark op hdinsight] Mijntoep**.

3. Selecteer in het hoofd venster het **`Locally Run`** tabblad.

4. Geef de volgende waarden op en selecteer **OK**:

    |Eigenschap |Waarde |
    |----|----|
    |Hoofd klasse van taak|De standaardwaarde is de hoofdklasse uit het geselecteerde bestand. U kunt de klasse wijzigen door het weglatings teken (**...**) te selecteren.  en kies een andere klasse.|
    |Omgevingsvariabelen|Verzeker dat de waarde voor HADOOP_HOME correct is.|
    |WINUTILS.exe-locatie|Verzeker dat het pad correct is.|

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin/console-set-configuration.png" alt-text="Configuratie lokale consoleset" border="true":::

5. Navigeer in Project naar **myApp** > **src** > **main** > **scala** > **myApp**.  

6. Ga in de menu balk naar **extra**  >  **Spark console**  >  **uitvoeren Spark Local Console (scala)**.

7. Er kunnen dan twee dialoogvensters worden weergegeven waarin u wordt gevraagd of u afhankelijkheden automatisch wilt oplossen. Als u dat wilt doen, selecteert u **Automatisch oplossen**.

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin/intellij-console-autofix1.png" alt-text="Dialoogvenster1 Automatisch oplossen in Spark in IntelliJ IDEA" border="true":::

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin/intellij-console-autofix2.png" alt-text="Dialoogvenster2 Automatisch oplossen in Spark in IntelliJ IDEA" border="true":::

8. De console moet er ongeveer uitzien zoals in de onderstaande afbeelding. Typ `sc.appName` in het consolevenster en druk op Ctrl+Enter.  Het resultaat wordt weergegeven. U kunt de lokale console beëindigen door op de knop rood te klikken.

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin/local-console-result.png" alt-text="Resultaat lokale console in IntelliJ IDEA" border="true":::

### <a name="spark-livy-interactive-session-consolescala"></a>Livy (scala) voor de interactieve sessie van Spark

1. Navigeer in de menubalk naar **Uitvoeren** > **Configuraties bewerken...** .

2. Ga in het linkerdeel venster van het venster **configuraties voor uitvoeren/fout opsporing** naar **Apache Spark op hdinsight**  >  **[Spark op hdinsight] Mijntoep**.

3. Selecteer in het hoofd venster het **`Remotely Run in Cluster`** tabblad.

4. Geef de volgende waarden op en selecteer **OK**:

    |Eigenschap |Waarde |
    |----|----|
    |Spark-clusters (alleen Linux)|Selecteer het HDInsight Spark-cluster waarop u de toepassing wilt uitvoeren.|
    |Hoofdklassenaam|De standaardwaarde is de hoofdklasse uit het geselecteerde bestand. U kunt de klasse wijzigen door het weglatings teken (**...**) te selecteren.  en kies een andere klasse.|

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin/interactive-console-configuration.png" alt-text="Configuratie interactieve consoleset" border="true":::

5. Navigeer in Project naar **myApp** > **src** > **main** > **scala** > **myApp**.  

6. Ga in de menu balk naar **extra**  >  **Spark-console** en  >  **Voer Spark livy Interactive Session console (scala)** uit.

7. De console moet er ongeveer uitzien zoals in de onderstaande afbeelding. Typ `sc.appName` in het consolevenster en druk op Ctrl+Enter.  Het resultaat wordt weergegeven. U kunt de lokale console beëindigen door op de knop rood te klikken.

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin/interactive-console-result.png" alt-text="Resultaat interactieve console in IntelliJ IDEA" border="true":::

### <a name="send-selection-to-spark-console"></a>Selectie naar Spark-console verzenden

Het is handig voor u om het scriptresultaat te voorzien door wat code naar de lokale console of de interactieve Livy-sessieconsole (Scala) te verzenden. U kunt bepaalde code markeren in het scala-bestand en vervolgens met de rechter muisknop op **selectie verzenden klikken naar Spark-console**. De geselecteerde code wordt naar de-console verzonden. Het resultaat wordt achter de code weergegeven in de console. De console controleert de fouten, indien aanwezig.  

   :::image type="content" source="./media/apache-spark-intellij-tool-plugin/send-selection-to-console.png" alt-text="Selectie naar Spark-console verzenden" border="true":::

## <a name="integrate-with-hdinsight-identity-broker-hib"></a>Integreren met HDInsight Identity Broker (HIB)

### <a name="connect-to-your-hdinsight-esp-cluster-with-id-broker-hib"></a>Verbinding maken met uw HDInsight ESP-cluster met ID Broker (HIB)

U kunt de gebruikelijke stappen volgen om u aan te melden bij het Azure-abonnement om verbinding te maken met uw HDInsight ESP-cluster met ID Broker (HIB). Nadat u zich hebt aangemeld, ziet u de cluster lijst in azure Verkenner. Zie [verbinding maken met uw HDInsight-cluster](#connect-to-your-hdinsight-cluster)voor meer instructies.

### <a name="run-a-spark-scala-application-on-an-hdinsight-esp-cluster-with-id-broker-hib"></a>Een Spark scala-toepassing uitvoeren op een HDInsight ESP-cluster met ID Broker (HIB)

U kunt de normale stappen volgen voor het verzenden van een taak naar het HDInsight ESP-cluster met ID Broker (HIB). Raadpleeg [een Spark scala-toepassing uitvoeren op een HDInsight Spark-cluster](#run-a-spark-scala-application-on-an-hdinsight-spark-cluster) voor meer instructies.

De benodigde bestanden worden geüpload naar een map met de naam met uw aanmeldings account en u ziet het pad naar de upload in het configuratie bestand.

   :::image type="content" source="./media/apache-spark-intellij-tool-plugin/upload-path-in-the-configuration.png" alt-text="pad naar uploaden in de configuratie" border="true":::

### <a name="spark-console-on-an-hdinsight-esp-cluster-with-id-broker-hib"></a>Spark-console in een HDInsight ESP-cluster met ID Broker (HIB)

U kunt Spark Local Console (scala) uitvoeren of Spark livy Interactive Session console (scala) uitvoeren op een HDInsight ESP-cluster met ID Broker (HIB). Raadpleeg de [Spark-console](#spark-console) voor meer instructies.

   > [!NOTE]  
   > Voor het HDInsight ESP-cluster met id Broker (HIB) kunt u [een cluster koppelen](#link-a-cluster) en [fouten opsporen Apache Spark toepassingen op afstand](#debug-apache-spark-applications-locally-or-remotely-on-an-hdinsight-cluster) niet worden ondersteund.

## <a name="reader-only-role"></a>Alleen-lezen rol

Wanneer gebruikers een taak verzenden naar een cluster met de machtiging alleen lezer, zijn Ambari referenties vereist.

### <a name="link-cluster-from-context-menu"></a>Cluster koppelen via het snelmenu

1. Meld u aan met een account met alleen-lezen rollen.

2. Vouw in **Azure Verkenner** **HDInsight** uit om hdinsight-clusters in uw abonnement weer te geven. De gemarkeerde clusters **"Role: Reader"** hebben alleen de rol alleen-lezen.

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin/intellij-view-explorer15.png" alt-text="' IntelliJ Azure Explorer-rol: lezer '" border="true":::

3. Klik met de rechter muisknop op het cluster met de machtiging alleen lezer. Selecteer **dit cluster koppelen in het** context menu om het cluster te koppelen. Voer de Ambari-gebruikers naam en het wacht woord in.

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin/intellij-view-explorer11.png" alt-text="IntelliJ Azure Explorer koppelen aan dit cluster" border="true":::

4. Als het cluster is gekoppeld, wordt HDInsight vernieuwd.
   De fase van het cluster wordt gekoppeld.
  
    :::image type="content" source="./media/apache-spark-intellij-tool-plugin/intellij-view-explorer8.png" alt-text="IntelliJ Azure Verkenner gekoppeld dialoog venster" border="true":::

### <a name="link-cluster-by-expanding-jobs-node"></a>Knoop punt voor koppeling van cluster door taken uitbreiden

1. Klik op het knoop punt **taken** , het venster **toegang tot cluster taken geweigerd** verschijnt.

2. Klik op **dit cluster koppelen om het** cluster te koppelen.

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin/intellij-view-explorer9.png" alt-text="dialoog venster geweigerde toegang tot cluster taak" border="true":::

### <a name="link-cluster-from-rundebug-configurations-window"></a>Het venster cluster koppelen via configuratie van uitvoering/fout opsporing

1. Een HDInsight-configuratie maken. Selecteer vervolgens **extern uitvoeren in het cluster**.

2. Selecteer een cluster met een machtiging voor alleen-lezen rollen voor **Spark-clusters (alleen voor Linux)**. Er wordt een waarschuwings bericht weer gegeven. U kunt klikken op **dit cluster koppelen om het** cluster te koppelen.

   :::image type="content" source="./media/apache-spark-intellij-tool-plugin/create-configuration.png" alt-text="IntelliJ-idee uitvoeren/fouten opsporen-configuratie maken" border="true":::

### <a name="view-storage-accounts"></a>Opslag accounts weer geven

* Voor clusters met de machtiging alleen lezer, klikt u op het knoop punt **opslag accounts** , het venster **opslag toegang geweigerd** verschijnt. U kunt klikken op **openen Azure Storage Explorer** om Storage Explorer te openen.

   :::image type="content" source="./media/apache-spark-intellij-tool-plugin/intellij-view-explorer14.png" alt-text="' IntelliJ-idee opslag toegang geweigerd '" border="true":::

   :::image type="content" source="./media/apache-spark-intellij-tool-plugin/intellij-view-explorer10.png" alt-text="Knop voor IntelliJ-idee opslag toegang geweigerd" border="true":::

* Voor gekoppelde clusters klikt u op het knoop punt **opslag accounts** , het venster **opslag toegang geweigerd** verschijnt. U kunt klikken op **openen Azure Storage** om Storage Explorer te openen.

   :::image type="content" source="./media/apache-spark-intellij-tool-plugin/intellij-view-explorer13.png" alt-text="' IntelliJ-idee opslag toegang Denied2 '" border="true":::

   :::image type="content" source="./media/apache-spark-intellij-tool-plugin/intellij-view-explorer12.png" alt-text="Knop voor IntelliJ-idee opslag toegang Denied2" border="true":::

## <a name="convert-existing-intellij-idea-applications-to-use-azure-toolkit-for-intellij"></a>Bestaande IntelliJ-ideeën converteren voor het gebruik van Azure-toolkit voor IntelliJ

U kunt de bestaande Spark scala-toepassingen die u in IntelliJ-idee hebt gemaakt, converteren om compatibel te zijn met Azure-toolkit voor IntelliJ. U kunt vervolgens de-invoeg toepassing gebruiken om de toepassingen naar een HDInsight Spark-cluster te verzenden.

1. Voor een bestaande Spark scala-toepassing die is gemaakt via IntelliJ idee, opent u het bijbehorende `.iml` bestand.

2. Op hoofd niveau is dit een **module** -element zoals de volgende tekst:

    ```xml
    <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4">
    ```

   Bewerk het element dat u wilt toevoegen `UniqueKey="HDInsightTool"` , zodat het **module** -element eruitziet als de volgende tekst:

    ```xml
    <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4" UniqueKey="HDInsightTool">
    ```

3. Sla de wijzigingen op. Uw toepassing moet nu compatibel zijn met Azure-toolkit voor IntelliJ. U kunt dit testen door met de rechter muisknop op de project naam in project te klikken. In het pop-upmenu wordt nu de optie **Spark-toepassing verzenden naar HDInsight**.

## <a name="clean-up-resources"></a>Resources opschonen

Als u deze toepassing verder niet meer gebruikt, verwijdert u het cluster dat u hebt gemaakt, via de volgende stappen:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).

1. Typ **HDInsight** in het **Zoekvak** bovenaan.

1. Selecteer onder **Services** de optie **HDInsight-clusters**.

1. Selecteer in de lijst met HDInsight-clusters die wordt weer gegeven, de **...** naast het cluster dat u voor dit artikel hebt gemaakt.

1. Selecteer **Verwijderen**. Selecteer **Ja**.

:::image type="content" source="./media/apache-spark-intellij-tool-plugin/hdinsight-azure-portal-delete-cluster.png " alt-text="HDInsight-cluster Azure Portal verwijderd" border="true":::

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u de Azure-toolkit voor IntelliJ-invoeg toepassing gebruikt om Apache Spark toepassingen te ontwikkelen die in [scala](https://www.scala-lang.org/)zijn geschreven. Deze vervolgens rechtstreeks vanuit de IntelliJ-Integrated Development Environment (IDE) naar een HDInsight Spark-cluster verzonden. Ga naar het volgende artikel om te zien hoe de gegevens die u hebt geregistreerd in Apache Spark kunnen worden overgebracht naar een BI-hulpprogramma voor analyse zoals Power BI.

> [!div class="nextstepaction"]
> [Apache Spark gegevens analyseren met behulp van Power BI](apache-spark-use-bi-tools.md)
