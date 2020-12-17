---
title: 'Zelfstudie: Scala Maven-app voor Spark & IntelliJ - Azure HDInsight'
description: 'Zelfstudie: een Spark-toepassing maken die is geschreven in Scala met Apache Maven als het buildsysteem. En een bestaand Maven-archetype voor Scala dat door IntelliJ IDEA wordt verstrekt.'
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: tutorial
ms.custom: contperf-fy21q1
ms.date: 08/21/2020
ms.openlocfilehash: 5be4ce97e6087847f084f9b9ec2a6b8b0df84e14
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/10/2020
ms.locfileid: "97033507"
---
# <a name="tutorial-create-a-scala-maven-application-for-apache-spark-in-hdinsight-using-intellij"></a>Zelfstudie: Een Scala Maven-toepassing maken voor Apache Spark in HDInsight met behulp van IntelliJ

In deze zelfstudie leert u hoe u met behulp van Apache Maven met IntelliJ IDEA een Apache Spark-toepassing maakt die is geschreven in Scala. In het artikel wordt Apache Maven als het buildsysteem gebruikt. En er wordt begonnen met een bestaand Maven-archetype voor Scala dat door IntelliJ IDEA wordt verstrekt.  Het maken van een Scala-toepassing in IntelliJ IDEA omvat de volgende stappen:

* Maven gebruiken als het buildsysteem.
* POM-bestand (Project Object Model) bijwerken om afhankelijkheden van Spark-module om te zetten.
* De toepassing schrijven in Scala.
* Een JAR-bestand genereren dat kan worden verstuurd naar HDInsight Spark-clusters.
* De toepassing uitvoeren in een Spark-cluster met behulp van Livy.

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
> * Scala-invoegtoepassing voor IntelliJ IDEA installeren
> * IntelliJ gebruiken voor het ontwikkelen van een Scala Maven-toepassing
> * Een zelfstandig Scala-project maken

## <a name="prerequisites"></a>Vereisten

* Een Apache Spark-cluster in HDInsight. Zie [Apache Spark-clusters maken in Azure HDInsight](apache-spark-jupyter-spark-sql.md) voor instructies.

* [Oracle Java Development Kit](https://www.azul.com/downloads/azure-only/zulu/).  In deze zelfstudie wordt gebruikgemaakt van Java-versie 8.0.202.

* Een Java-IDE. In dit artikel wordt [IntelliJ IDEA Community versie  2018.3.4](https://www.jetbrains.com/idea/download/).

* Azure-toolkit voor IntelliJ.  Zie [Installing the Azure Toolkit for IntelliJ](/java/azure/intellij/azure-toolkit-for-intellij-create-hello-world-web-app#installation-and-sign-in) (De Azure Toolkit voor IntelliJ installeren).

## <a name="install-scala-plugin-for-intellij-idea"></a>Scala-invoegtoepassing voor IntelliJ IDEA installeren

Voer de volgende stappen uit om de Scala-invoegtoepassing te installeren:

1. Open IntelliJ IDEA.

2. Ga op het welkomstscherm naar **Configure** > **Plugins** om het venster **Plugins** te openen.

    !['IntelliJ IDEA: Scala-invoegtoepassing inschakelen'](./media/apache-spark-create-standalone-application/enable-scala-plugin1.png)

3. Selecteer **Install** voor de Scala-invoegtoepassing die in het nieuwe venster wordt weergegeven.  

    !['IntelliJ IDEA: Scala-invoegtoepassing installeren'](./media/apache-spark-create-standalone-application/install-scala-plugin.png)

4. Als de invoegtoepassing is geïnstalleerd, moet u de IDE opnieuw starten.

## <a name="use-intellij-to-create-application"></a>IntelliJ gebruiken om een toepassing te maken

1. Start IntelliJ IDEA en selecteer **Create New Project** om het venster **New Project** te openen.

2. Selecteer **Apache Spark/HDInsight** in het linkerdeelvenster.

3. Selecteer **Spark Project (Scala)** in het hoofdvenster.

4. Selecteer in de vervolgkeuzelijst **Build-hulpprogramma** een van de volgende waarden:
      * **Maven**, voor de ondersteuning van de wizard Scala-project maken.
      * **SBT**, voor het beheren van de afhankelijkheden en het maken van het Scala-project.

   ![Het dialoogvenster Nieuw project van IntelliJ](./media/apache-spark-create-standalone-application/intellij-project-apache-spark.png)

5. Selecteer **Next**.

6. Geef in het venster **New project** de volgende gegevens op:  

  	|  Eigenschap   | Beschrijving   |  
  	| ----- | ----- |  
  	|Projectnaam| Voer een naam in.|  
  	|Project&nbsp;location| Voer de locatie in om uw project in op te slaan.|
  	|Project SDK| Als u IDEA voor het eerst gebruikt, is dit veld leeg.  Selecteer **New...** en ga naar uw JDK.|
  	|Spark-versie|De wizard voor het maken van het project integreert de juiste versie voor Spark SDK en Scala SDK. Selecteer **Spark 1.x** als de Spark-clusterversie ouder is dan 2.0. Selecteer anders **Spark 2.x**. In dit voorbeeld wordt **Spark 2.3.0 (Scala 2.11.8)** gebruikt.|

    ![IntelliJ IDEA: de Spark SDK selecteren](./media/apache-spark-create-standalone-application/hdi-scala-new-project.png)

7. Selecteer **Finish**.

## <a name="create-a-standalone-scala-project"></a>Een zelfstandig Scala-project maken

1. Start IntelliJ IDEA en selecteer **Create New Project** om het venster **New Project** te openen.

2. Selecteer **Maven** in het linkerdeelvenster.

3. Geef een **Project SDK** op. Selecteer **New...** als het veld leeg is en ga naar de installatiemap van Java.

4. Schakel het selectievakje **Create from archetype** in.  

5. Selecteer **`org.scala-tools.archetypes:scala-archetype-simple`** in de lijst met archetypen. Met dit archetype maakt u de juiste mapstructuur en worden de vereiste standaardafhankelijkheden voor het schrijven van het Scala-programma gedownload.

    ![Schermafbeelding met het geselecteerde Archetype in het venster New Project.](./media/apache-spark-create-standalone-application/intellij-project-create-maven.png)

6. Selecteer **Next**.

7. Vouw **Artefactcoördinaten** uit. Geef relevante waarden op voor **GroupId** en **ArtifactId**. **Naam** en **Locatie** worden automatisch ingevuld. In deze zelfstudie worden de volgende waarden gebruikt:

    - **GroupId:** com.microsoft.spark.example
    - **ArtifactId:** SparkSimpleApp

    ![Schermafbeelding met de optie Artifact Coordinates in het venster New Project.](./media/apache-spark-create-standalone-application/intellij-artifact-coordinates.png)

8. Selecteer **Next**.

9. Controleer de instellingen en selecteer vervolgens **Next**.

10. Controleer de naam en de locatie van het project en selecteer vervolgens **Finish**.  Het duurt enkele minuten voordat het project is geïmporteerd.

11. Als het project is geïmporteerd, gaat u in het linkerdeelvenster naar **SparkSimpleApp** > **src** > **test** > **scala** > **com** > **microsoft** > **spark** > **example**.  Klik met de rechtermuisknop op **MySpec** en selecteer vervolgens **Delete...** . U hebt dit bestand niet nodig voor de toepassing.  Selecteer **OK** in het dialoogvenster.
  
12. In de latere stappen gaat u het bestand **pom.xml** bijwerken om de afhankelijkheden voor de Spark Scala-toepassing te definiëren. Om deze afhankelijkheden automatisch te downloaden en om te zetten, moet u Maven configureren.

13. Selecteer in het menu **File** de optie **Settings** om het venster **Settings** te openen.

14. Ga in het venster **Settings** naar **Build, Execution, Deployment** > **Build Tools** > **Maven** > **Importing**.

15. Schakel het selectievakje **Import Maven projects automatically** in.

16. Selecteer **Apply** en vervolgens **OK**.  U keert dan terug naar het projectvenster.

    ![Maven configureren voor automatische downloads](./media/apache-spark-create-standalone-application/configure-maven-download.png)

17. Ga in het linkerdeelvenster naar **src** > **main** > **scala** > **com.microsoft.spark.example** en dubbelklik op **App** om App.scala te openen.

18. Vervang de bestaande voorbeeldcode door de volgende code en sla de wijzigingen op. Deze code leest de gegevens uit de HVAC.csv (beschikbaar op alle HDInsight Spark-clusters). Hiermee worden de rijen opgehaald die slechts één cijfer in de zesde kolom hebben. En de uitvoer wordt geschreven naar **/HVACOut-** onder de standaardopslagcontainer voor het cluster.

    ```scala
    package com.microsoft.spark.example

    import org.apache.spark.SparkConf
    import org.apache.spark.SparkContext

    /**
      * Test IO to wasb
      */
    object WasbIOTest {
        def main (arg: Array[String]): Unit = {
            val conf = new SparkConf().setAppName("WASBIOTest")
            val sc = new SparkContext(conf)
    
            val rdd = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
    
            //find the rows which have only one digit in the 7th column in the CSV
            val rdd1 = rdd.filter(s => s.split(",")(6).length() == 1)
    
            rdd1.saveAsTextFile("wasb:///HVACout")
        }
    }
    ```

19. Dubbelklik in het linkerdeelvenster op **pom.xml**.  

20. Voeg binnen `<project>\<properties>` de volgende segmenten toe:

    ```xml
    <scala.version>2.11.8</scala.version>
    <scala.compat.version>2.11.8</scala.compat.version>
    <scala.binary.version>2.11</scala.binary.version>
    ```

21. Voeg binnen `<project>\<dependencies>` de volgende segmenten toe:

    ```xml
    <dependency>
        <groupId>org.apache.spark</groupId>
        <artifactId>spark-core_${scala.binary.version}</artifactId>
        <version>2.3.0</version>
    </dependency>
    ```

    Sla de wijzigingen in pom.xml op.

22. Maak het JAR-bestand. IntelliJ IDEA maakt het mogelijk om het JAR-bestand te maken als een artefact van een project. Voer de volgende stappen uit.

    1. Selecteer **Project Structure...** in het menu **File**.

    2. Ga in het venster **Project Structure** naar **Artifacts** > **het plusteken +**  > **JAR** > **From modules with dependencies...** .

        !['IntelliJ IDEA: projectstructuur - jar toevoegen'](./media/apache-spark-create-standalone-application/hdinsight-create-jar1.png)

    3. Selecteer in het venster **Create JAR from Modules** het mappictogram in het tekstvak **Main Class**.

    4. Selecteer in het venster **Select Main Class** de klasse die standaard wordt weergegeven en selecteer vervolgens **OK**.

        !['IntelliJ IDEA: projectstructuur - klasse selecteren'](./media/apache-spark-create-standalone-application/hdinsight-create-jar2.png)

    5. Controleer of in het venster **Create JAR from Modules** de optie **extract to the target JAR** is geselecteerd en selecteer vervolgens **OK**.  Met deze instelling wordt er één JAR gemaakt met alle afhankelijkheden.

        ![IntelliJ IDEA: projectstructuur - vanuit module'](./media/apache-spark-create-standalone-application/hdinsight-create-jar3.png)

    6. Het tabblad **Output Layout** geeft een overzicht van alle JAR-bestanden die zijn opgenomen als onderdeel van het Maven-project. U kunt de bestanden selecteren en verwijderen waarvan de Scala-toepassing niet direct afhankelijk is. Voor de toepassing die u hier maakt, kunt u alle bestanden behalve het laatste bestand (**SparkSimpleApp compile output**) verwijderen. Selecteer de JAR-bestanden die u wilt verwijderen en selecteer vervolgens het minteken **-** .

        !['IntelliJ IDEA: projectstructuur - uitvoer verwijderen'](./media/apache-spark-create-standalone-application/hdi-delete-output-jars.png)

        Zorg ervoor dat het selectievakje **Include in project build** is geselecteerd. Deze optie zorgt ervoor dat het JAR-bestand telkens wanneer het project wordt gebouwd of bijgewerkt, wordt gemaakt. Selecteer **Apply** en vervolgens **OK**.

    7. Ga naar **Build** > **Build Artifacts** > **Build** om het JAR-bestand te maken. Het project wordt in circa dertig seconden gecompileerd.  Het bestand wordt opgeslagen in **\out\artifacts**.

        ![IntelliJ IDEA: projectstructuur - artefactuitvoer](./media/apache-spark-create-standalone-application/hdi-artifact-output-jar.png)

## <a name="run-the-application-on-the-apache-spark-cluster"></a>De toepassing uitvoeren in het Apache Spark-cluster

U kunt de volgende methoden gebruiken om de toepassing uit te voeren in het cluster:

* **Kopieer het JAR-bestand van de toepassing naar de Azure-opslagblob** die aan het cluster is gekoppeld. Dit kan met **AzCopy**, een opdrachtregelprogramma. Er zijn echter een heleboel clients die u kunt gebruiken om gegevens te uploaden. Meer informatie hierover vindt u in [Gegevens voor Apache Hadoop-taken uploaden in HDInsight](../hdinsight-upload-data.md).

* **Gebruik Apache Livy om een toepassingstaak op afstand te versturen** naar het Spark-cluster. Spark-clusters in HDInsight ondersteunen Livy voor het aanbieden van REST-eindpunten waarmee Spark-taken op afstand kunnen worden verzonden. Zie [Apache Livy met Spark-clusters in HDInsight gebruiken om Apache Spark-taken op afstand te verzenden](apache-spark-livy-rest-interface.md) voor meer informatie.

## <a name="clean-up-resources"></a>Resources opschonen

Als u deze toepassing verder niet meer gebruikt, verwijdert u het cluster dat u hebt gemaakt, via de volgende stappen:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).

1. Typ **HDInsight** in het **Zoekvak** bovenaan.

1. Selecteer onder **Services** de optie **HDInsight-clusters**.

1. Selecteer in de lijst met HDInsight-clusters die wordt weergegeven, de **...** naast het cluster dat u voor deze zelfstudie hebt gemaakt.

1. Selecteer **Verwijderen**. Selecteer **Ja**.

!['HDInsight: Azure Portal -cluster verwijderen'](./media/apache-spark-create-standalone-application/hdinsight-azure-portal-delete-cluster.png "HDInsight-cluster verwijderen")

## <a name="next-step"></a>Volgende stap

In dit artikel hebt u geleerd hoe u een Apache Spark Scala-toepassing maakt. Ga naar het volgende artikel om te leren hoe u deze toepassing uitvoert in een HDInsight Spark-cluster met behulp van Livy.

> [!div class="nextstepaction"]
>[Apache Livy gebruiken om taken op afstand uit te voeren in een Apache Spark-cluster](./apache-spark-livy-rest-interface.md)