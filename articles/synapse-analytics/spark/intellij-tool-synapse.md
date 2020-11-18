---
title: Zelfstudie - Azure-toolkit voor IntelliJ (Spark-toepassing)
description: 'Zelfstudie: met behulp van de Azure-toolkit voor IntelliJ om Spark-toepassingen, die in Scala worden geschreven, ontwikkelen en naar een serverloze Apache Spark-pool verzenden (preview).'
services: synapse-analytics
author: hrasheed-msft
ms.author: jejiang
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: spark
ms.date: 04/15/2020
ms.openlocfilehash: 5a9fcf3e13c2f86a11e9626be486a585ec659044
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93325228"
---
# <a name="tutorial-create-an-apache-spark-application-with-intellij-using-a-synapse-workspace"></a>Zelfstudie: een Apache Spark-toepassing met IntelliJ maken met behulp van een Synapse-werkruimte

In deze zelfstudie ziet u hoe u de invoegtoepassing Azure-toolkit voor IntelliJ gebruikt om Apache Spark-toepassingen, die in [Scala](https://www.scala-lang.org/) worden geschreven, te ontwikkelen en naar een serverloze Apache Spark-pool te verzenden (preview), rechtstreeks vanuit de IntelliJ-IDE (Integrated Development Environment). U kunt de invoegtoepassing op een paar manieren gebruiken:

- Ontwikkel een Scala Spark-toepassing en verzend deze naar een Spark-pool.
- Verkrijg toegang tot de resources van uw Spark-pools.
- Ontwikkel een Scala Spark-toepassing en voer deze lokaal uit.

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
>
> - De invoegtoepassing Azure-toolkit voor IntelliJ gebruiken
> - Apache Spark-toepassingen ontwikkelen
> - Toepassingen naar Spark-pools verzenden

## <a name="prerequisites"></a>Vereisten

- [IntelliJ IDEA-communityversie](https://www.jetbrains.com/idea/download/download-thanks.html?platform=windows&code=IIC).
- Invoegtoepassing Azure-toolkit 3.27.0-2019.2 – Installeren vanuit [Opslagplaats voor IntelliJ-invoegtoepassingen](/java/azure/intellij/azure-toolkit-for-intellij-installation?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [JDK (versie 1.8)](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).
- Scala-invoegtoepassing – Installeren vanuit [Opslagplaats voor IntelliJ-invoegtoepassingen](/azure/hdinsight/spark/apache-spark-intellij-tool-plugin#install-scala-plugin-for-intellij-idea).
- De volgende vereiste geldt alleen voor Windows-gebruikers:

  Terwijl u de lokale Spark Scala-toepassing uitvoert op een Windows-computer, kan er een uitzondering optreden, zoals uitgelegd in [SPARK-2356](https://issues.apache.org/jira/browse/SPARK-2356). De uitzondering treedt op omdat WinUtils.exe ontbreekt op Windows.
  Om deze fout op te lossen, downloadt u het [uitvoerbare bestand WinUtils](http://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe) naar een locatie zoals **C:\WinUtils\bin**. Vervolgens voegt u de omgevingsvariabele **HADOOP_HOME** toe en stelt u de waarde van de variabele in op **C:\WinUtils**.

## <a name="create-a-spark-scala-application-for-a-spark-pool"></a>Een Spark Scala-toepassing maken voor een Spark-pool

1. Start IntelliJ IDEA en selecteer **Create New Project** om het venster **New Project** te openen.
2. Selecteer **Apache Spark/HDInsight** in het linkerdeelvenster.
3. Selecteer **Spark Project met voorbeelden (Scala)** in het hoofdvenster.
4. Selecteer in de vervolgkeuzelijst **Build-hulpprogramma** een van de volgende typen:

   - **Maven**, voor de ondersteuning van de wizard Scala-project maken.
   - **SBT**, voor het beheren van de afhankelijkheden en het maken van het Scala-project.

    ![IntelliJ IDEA-dialoogvenster Nieuw project](./media/intellij-tool-synapse/create-synapse-application01.png)

5. Selecteer **Next**.
6. Geef in het venster **New project** de volgende gegevens op:

    | Eigenschap | Beschrijving |
    | ----- | ----- |
    |Projectnaam| Voer een naam in. In deze zelfstudie wordt `myApp` gebruikt.|
    |Project&nbsp;location| Voer de gewenste locatie in om uw project in op te slaan.|
    |Project SDK| Als u IDEA voor het eerst gebruikt, is dit veld wellicht leeg. Selecteer **New...** en ga naar uw JDK.|
    |Spark-versie|De wizard voor het maken van het project integreert de juiste versie voor Spark SDK en Scala SDK. Synapse ondersteunt alleen **Spark 2.4.0**.|
    |||

    ![De Apache Spark SDK selecteren](./media/intellij-tool-synapse/create-synapse-application02.png)

7. Selecteer **Finish**. Het kan enkele minuten duren voordat het project beschikbaar wordt.
8. Het Spark-project maakt automatisch een artefact voor u. Doe het volgende om het artefact te bekijken:

   a. Navigeer in de menubalk naar **Bestand** > **Projectstructuur...** .

   b. Selecteer in het venster **Projectstructuur** de optie **Artefacten**.

   c. Selecteer **Annuleren** nadat u het artefact hebt bekeken.

    ![Artefactgegevens in het dialoogvenster](./media/intellij-tool-synapse/default-artifact-dialog.png)

9. Zoek **LogQuery** in **myApp** > **src** > **main** > **scala**> **sample**> **LogQuery**. Deze zelfstudie maakt gebruik van **LogQuery** om te worden uitgevoerd.

   ![Opdrachten voor het maken van een Scala-klasse uit Project](./media/intellij-tool-synapse/select-run-file.png)

## <a name="connect-to-your-spark-pools"></a>Verbinding maken met uw Spark-pools

Meld u aan bij uw Azure-abonnement om verbinding te maken met uw Spark-pools.

### <a name="sign-in-to-your-azure-subscription"></a>Aanmelden bij uw Azure-abonnement

1. Navigeer in de menubalk naar **Beeld** > **Hulpprogrammavensters** > **Azure Explorer**.

   ![IntelliJ IDEA toont Azure Explorer](./media/intellij-tool-synapse/show-azure-explorer1.png)

2. Klik in Azure Explorer met de rechtermuisknop op het knooppunt **Azure** en selecteer **Aanmelden**.

   ![Met rechtermuisknop op Azure klikken in IntelliJ IDEA-verkenner](./media/intellij-tool-synapse/explorer-rightclick-azure.png)

3. Kies in het dialoogvenster **Azure-aanmelding** de optie **Apparaataanmelding** en selecteer **Aanmelden**.

    ![Azure-aanmelding in IntelliJ IDEA](./media/intellij-tool-synapse/intellij-view-explorer2.png)

4. Selecteer in het dialoogvenster **Azure-apparaataanmelding** de optie **Kopiëren en openen**.

   ![Azure-apparaataanmelding in IntelliJ IDEA](./media/intellij-tool-synapse/intellij-view-explorer5.png)

5. Plak de code in de browserinterface en selecteer **Volgende**.

   ![Microsoft betreedt codedialoogvenster voor HDI](./media/intellij-tool-synapse/intellij-view-explorer6.png)

6. Voer uw referenties voor Azure in en sluit de browser.

   ![Microsoft betreedt e-maildialoogvenster voor HDI](./media/intellij-tool-synapse/intellij-view-explorer7.png)

7. Zodra u bent aangemeld, toont het dialoogvenster **Abonnementen selecteren** alle Azure-abonnementen die aan de referenties zijn gekoppeld. Selecteer uw abonnement en selecteer **Selecteren**.

    ![Het dialoogvenster Abonnementen selecteren](./media/intellij-tool-synapse/Select-Subscriptions.png)

8. Vouw in **Azure Explorer** de optie **Apache Spark in Synapse** uit om de werkruimten te bekijken die in uw abonnementen staan.

    ![Azure Explorer-hoofdweergave in IntelliJ IDEA](./media/intellij-tool-synapse/azure-explorer-workspace.png)

9. Als u de Spark-pools wilt bekijken, kunt u een werkruimte verder uitvouwen.

    ![Azure Explorer-opslagaccounts](./media/intellij-tool-synapse/azure-explorer-pool.png)

## <a name="remote-run-a-spark-scala-application-on-a-spark-pool"></a>Een Spark Scala-toepassing extern uitvoeren in een Spark-pool

Nadat u een Scala-toepassing hebt gemaakt, kunt u deze extern uitvoeren.

1. Open het venster **Uitvoeren/fouten opsporen van configuraties** door het pictogram te selecteren.

    ![De opdracht Spark-toepassing verzenden naar HDInsight 1](./media/intellij-tool-synapse/open-configuration-window.png)

2. Selecteer in het dialoogvenster **Uitvoeren/fouten opsporen van configuraties** de optie **+** en selecteer **Apache Spark in Synapse**.

    ![De opdracht Spark-toepassing verzenden naar HDInsight 2](./media/intellij-tool-synapse/create-synapse-configuration02.png)

3. Geef in het venster **Uitvoeren/fouten opsporen van configuraties** de volgende waarden op en selecteer **OK**:

    |Eigenschap |Waarde |
    |----|----|
    |Spark-pools|Selecteer de Spark-pools waarop u uw toepassing wilt uitvoeren.|
    |Selecteer een artefact om te verzenden|Laat de standaardinstelling staan.|
    |Hoofdklassenaam|De standaardwaarde is de hoofdklasse uit het geselecteerde bestand. U kunt de klasse wijzigen door het beletselteken ( **...** ) te selecteren en een andere klasse te kiezen.|
    |Taakconfiguraties|U kunt de standaardsleutel en -waarden wijzigen. Zie [Apache Livy REST API](http://livy.incubator.apache.org./docs/latest/rest-api.html) voor meer informatie.|
    |Opdrachtregelargumenten|U kunt voor de hoofdklasse argumenten invoeren, gescheiden door een spatie, indien nodig.|
    |JAR’s en bestanden waarnaar wordt verwezen|U kunt de paden invoeren voor de JAR’s en bestanden waarnaar wordt verwezen, indien aanwezig. U kunt ook bladeren door bestanden in het virtuele Azure-bestandssysteem, dat momenteel alleen ADLS Gen2-cluster ondersteunt. Voor meer informatie: [Apache Spark-configuratie]https://spark.apache.org/docs/2.4.5/configuration.html#runtime-environment) en [Resources uploaden naar cluster](../../storage/blobs/storage-quickstart-blobs-storage-explorer.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).|
    |Opslag van taakuploads|Vouw uit om aanvullende opties te onthullen.|
    |Opslagtype|Selecteer **Azure Blob gebruiken voor uploaden** of **Standaardopslagaccount van cluster gebruiken voor uploaden** in de vervolgkeuzelijst.|
    |Opslagaccount|Voer uw opslagaccount in.|
    |Opslagsleutel|Voer uw opslagsleutel in.|
    |Opslagcontainer|Selecteer uw opslagcontainer in de vervolgkeuzelijst zodra **Opslagaccount** en **Opslagsleutel** zijn ingevoerd.|

    ![Het dialoogvenster voor de verzending van Spark 1](./media/intellij-tool-synapse/create-synapse-configuration03.png)

4. Selecteer het pictogram **SparkJobRun** om uw project te verzenden naar de geselecteerde Spark-pool. Onderaan het tabblad **Externe Spark-taak in cluster** wordt de voortgang van de taakuitvoering weergegeven. U kunt de toepassing stoppen door de rode knop te selecteren.

    ![Venster voor verzending van Apache Spark](./media/intellij-tool-synapse/remotely-run-synapse.png)

    ![Het dialoogvenster voor de verzending van Spark 2](./media/intellij-tool-synapse/remotely-run-result.png)

## <a name="local-rundebug-apache-spark-applications"></a>Lokaal uitvoeren/fouten opsporen van Apache Spark-toepassingen

U kunt de onderstaande instructies volgen om uw lokale uitvoering en lokale foutopsporing voor uw Apache Spark-taak in te stellen.

### <a name="scenario-1-do-local-run"></a>Scenario 1: Lokaal uitvoeren

1. Open het dialoogvenster **Uitvoeren/fouten opsporen van configuraties** en selecteer het plusteken ( **+** ). Selecteer vervolgens de optie **Apache Spark in Synapse**. Voer informatie in voor **Naam**, **Hoofdklassenaam** om op te slaan.

    ![Lokaal uitvoeren in Uitvoeren/fouten opsporen van configuraties in IntelliJ 1](./media/intellij-tool-synapse/local-run-synapse.png)

    - Omgevingsvariabelen en WinUtils.exe-locatie zijn alleen voor Windows-gebruikers.
    - Omgevingsvariabelen: De systeemomgevingsvariabele kan automatisch worden gedetecteerd als u deze eerder hebt ingesteld, zodat u deze niet handmatig hoeft toe te voegen.
    - [WinUtils.exe-locatie](http://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe): U kunt de WinUtils-locatie opgeven door aan de rechterkant het mappictogram te selecteren.

2. Selecteer vervolgens de knop Lokaal afspelen.

    ![Lokaal uitvoeren in Uitvoeren/fouten opsporen van configuraties in IntelliJ 2](./media/intellij-tool-synapse/local-run-synapse01.png)

3. Zodra de lokale uitvoering is voltooid, kunt u (als het script uitvoer bevat) het uitvoerbestand controleren via **gegevens** > **__standaard__**.

    ![Resultaat lokale uitvoering IntelliJ-project 1](./media/intellij-tool-synapse/spark-local-run-result.png)

### <a name="scenario-2-do-local-debugging"></a>Scenario 2: Lokaal fouten opsporen

1. Open het **LogQuery**-script en stel onderbrekingspunten in.
2. Selecteer het pictogram **Lokale foutopsporing** om lokaal fouten op te sporen.

    ![Resultaat lokale uitvoering IntelliJ-project 2](./media/intellij-tool-synapse/local-debug-synapse.png)

## <a name="access-and-manage-synapse-workspace"></a>Synapse-werkruimte openen en beheren

U kunt verschillende bewerkingen in Azure Explorer uitvoeren in Azure-toolkit voor IntelliJ. Navigeer in de menubalk naar **Beeld** > **Hulpprogrammavensters** > **Azure Explorer**.

### <a name="launch-workspace"></a>Werkruimte starten

1. Navigeer in Azure Explorer naar **Apache Spark in Synapse** en vouw het uit.

    ![Azure Explorer-hoofdweergave in IntelliJ IDEA](./media/intellij-tool-synapse/azure-explorer-workspace.png)

2. Klik met de rechtermuisknop op een werkruimte en selecteer **Werkruimte starten**, waarna een website wordt geopend.

    ![Toepassingsdetails weergeven in Spark-taak 1](./media/intellij-tool-synapse/launch-workspace-synapse.png)

    ![Toepassingsdetails weergeven in Spark-taak 2](./media/intellij-tool-synapse/launch-workspace-result.png)

## <a name="spark-console"></a>Spark-console

U kunt de lokale Spark-console (Scala) uitvoeren of de interactieve Spark Livy-sessieconsole (Scala) uitvoeren.

### <a name="spark-local-console-scala"></a>Lokale Spark-console (Scala)

Verzeker dat u aan de WINUTILS.EXE-vereiste voldoet.

1. Navigeer in de menubalk naar **Uitvoeren** > **Configuraties bewerken...** .
2. Navigeer in het linkerdeelvenster van het venster **Uitvoeren/fouten opsporen van configuraties** naar **Apache Spark in Synapse** >  **[Spark in Synapse] myApp**.
3. Selecteer in het hoofdvenster het tabblad **Lokaal uitvoeren**.
4. Geef de volgende waarden op en selecteer **OK**:

    |Eigenschap |Waarde |
    |----|----|
    |Omgevingsvariabelen|Verzeker dat de waarde voor HADOOP_HOME correct is.|
    |WINUTILS.exe-locatie|Verzeker dat het pad correct is.|

    ![Configuratie lokale consoleset](./media/intellij-tool-synapse/local-console-synapse01.png)

5. Navigeer in Project naar **myApp** > **src** > **main** > **scala** > **myApp**.
6. Navigeer in de menubalk naar **Hulpprogramma’s** > **Spark-console** > **Lokale Spark-console (Scala) uitvoeren**.
7. Er kunnen dan twee dialoogvensters worden weergegeven waarin u wordt gevraagd of u afhankelijkheden automatisch wilt oplossen. Als u dat wilt doen, selecteert u **Automatisch oplossen**.

    ![Dialoogvenster1 Automatisch oplossen in Spark in IntelliJ IDEA](./media/intellij-tool-synapse/intellij-console-autofix1.png)

    ![Dialoogvenster2 Automatisch oplossen in Spark in IntelliJ IDEA](./media/intellij-tool-synapse/intellij-console-autofix2.png)

8. De console moet er ongeveer uitzien zoals in de onderstaande afbeelding. Typ `sc.appName` in het consolevenster en druk op Ctrl+Enter. Het resultaat wordt weergegeven. U kunt de lokale console stoppen door de rode knop te selecteren.

    ![Resultaat lokale console in IntelliJ IDEA](./media/intellij-tool-synapse/local-console-result.png)

### <a name="spark-livy-interactive-session-console-scala"></a>Interactieve Spark Livy-sessieconsole (Scala)

Deze wordt alleen ondersteund in IntelliJ 2018.2 en 2018.3.

1. Navigeer in de menubalk naar **Uitvoeren** > **Configuraties bewerken...** .

2. Navigeer in het linkerdeelvenster van het venster **Uitvoeren/fouten opsporen van configuraties** naar **Apache Spark in Synapse** >  **[Spark in Synapse] myApp**.

3. Selecteer in het hoofdvenster het tabblad **Extern uitvoeren in cluster**.

4. Geef de volgende waarden op en selecteer **OK**:

    |Eigenschap |Waarde |
    |----|----|
    |Hoofdklassenaam| Selecteer de hoofdklassenaam.| 
    |Spark-pools|Selecteer de Spark-pools waarop u uw toepassing wilt uitvoeren.|
    ||

    ![Configuratie interactieve consoleset](./media/intellij-tool-synapse/interactive-console-configuration.png)

5. Navigeer in Project naar **myApp** > **src** > **main** > **scala** > **myApp**.

6. Navigeer in de menubalk naar **Hulpprogramma’s** > **Spark-console** > **Interactieve Spark Livy-sessieconsole (Scala) uitvoeren**.
7. De console moet er ongeveer uitzien zoals in de onderstaande afbeelding. Typ `sc.appName` in het consolevenster en druk op Ctrl+Enter. Het resultaat wordt weergegeven. U kunt de lokale console stoppen door de rode knop te selecteren.

    ![Resultaat interactieve console in IntelliJ IDEA](./media/intellij-tool-synapse/interactive-console-result.png)

### <a name="send-selection-to-spark-console"></a>Selectie verzenden naar Spark-console

U kunt het scriptresultaat te voorzien door wat code naar de lokale console of de interactieve Livy-sessieconsole (Scala) te verzenden. Daarvoor kunt u wat code in het Scala-bestand markeren en vervolgens met de rechtermuisknop op **Selectie verzenden naar Spark-console** klikken. De geselecteerde code wordt dan naar de console verzonden en uitgevoerd. Het resultaat wordt achter de code weergegeven in de console. De console controleert de aanwezige fouten.

   ![Selectie verzenden naar Spark-console](./media/intellij-tool-synapse/send-selection-to-console.png)

## <a name="next-steps"></a>Volgende stappen

- [Azure Synapse Analytics](../overview-what-is.md)
- [Een nieuwe Apache Spark-pool maken voor een Azure Synapse Analytics-werkruimte](../../synapse-analytics/quickstart-create-apache-spark-pool-studio.md)
