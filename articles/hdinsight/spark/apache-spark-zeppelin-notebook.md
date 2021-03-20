---
title: Zeppelin-notebooks & Apache Spark cluster-Azure HDInsight
description: Stapsgewijze instructies voor het gebruik van Zeppelin-notebooks met Apache Spark clusters in azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/23/2020
ms.openlocfilehash: ba58c007dc0bc3776f429366651cb79404b137b3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98928484"
---
# <a name="use-apache-zeppelin-notebooks-with-apache-spark-cluster-on-azure-hdinsight"></a>Apache Zeppelin-notebooks gebruiken met Apache Spark-cluster in Azure HDInsight

HDInsight Spark-clusters zijn [Apache Zeppelin](https://zeppelin.apache.org/) -notebooks. Gebruik de notitie blokken om Apache Spark-taken uit te voeren. In dit artikel leert u hoe u de Zeppelin-notebook kunt gebruiken in een HDInsight-cluster.

## <a name="prerequisites"></a>Vereisten

* Een Apache Spark-cluster in HDInsight. Zie [Apache Spark-clusters maken in Azure HDInsight](apache-spark-jupyter-spark-sql.md) voor instructies.
* Het URI-schema voor de primaire opslag voor uw clusters. Het schema is `wasb://` voor Azure Blob Storage, `abfs://` voor Azure data Lake Storage Gen2 of `adl://` voor Azure data Lake Storage gen1. Als beveiligde overdracht is ingeschakeld voor Blob Storage, zou de URI zijn `wasbs://` .  Zie [veilige overdracht vereisen in azure Storage](../../storage/common/storage-require-secure-transfer.md) voor meer informatie.

## <a name="launch-an-apache-zeppelin-notebook"></a>Een Apache Zeppelin-notebook starten

1. Selecteer in het **overzicht** van het Spark-cluster **Zeppelin notebook** in **cluster dashboards**. Voer de beheerders referenties in voor het cluster.  

   > [!NOTE]  
   > U kunt ook de Zeppelin-notebook voor uw cluster bereiken door de volgende URL in uw browser te openen. Vervang **CLUSTERNAME** door de naam van uw cluster.
   >
   > `https://CLUSTERNAME.azurehdinsight.net/zeppelin`

2. Maak een nieuwe notebook. Ga in het deel venster koptekst naar **notitie blok**  >  **nieuwe notitie maken**.

    ![Een nieuwe Zeppelin-notebook maken](./media/apache-spark-zeppelin-notebook/hdinsight-create-zeppelin-notebook.png "Een nieuwe Zeppelin-notebook maken")

    Voer een naam in voor het notitie blok en selecteer vervolgens **notitie maken**.

3. Zorg ervoor dat de kop van het notitie blok een verbonden status bevat. Deze wordt aangeduid met een groene stip in de rechter bovenhoek.

    ![Status van Zeppelin-notebook](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-connected.png "Status van Zeppelin-notebook")

4. Laad voorbeeldgegevens in een tijdelijke tabel. Wanneer u een Spark-cluster in HDInsight maakt, wordt het voorbeeld gegevensbestand, `hvac.csv` , gekopieerd naar het bijbehorende opslag account onder `\HdiSamples\SensorSampleData\hvac` .

    Plak het volgende fragment in de lege alinea die standaard wordt gemaakt in het nieuwe notitie blok.

    ```scala
    %livy2.spark
    //The above magic instructs Zeppelin to use the Livy Scala interpreter

    // Create an RDD using the default Spark context, sc
    val hvacText = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

    // Define a schema
    case class Hvac(date: String, time: String, targettemp: Integer, actualtemp: Integer, buildingID: String)

    // Map the values in the .csv file to the schema
    val hvac = hvacText.map(s => s.split(",")).filter(s => s(0) != "Date").map(
        s => Hvac(s(0),
                s(1),
                s(2).toInt,
                s(3).toInt,
                s(6)
        )
    ).toDF()

    // Register as a temporary table called "hvac"
    hvac.registerTempTable("hvac")
    ```

    Druk op **SHIFT + ENTER** of selecteer de knop **afspelen** voor de alinea om het fragment uit te voeren. De status in de rechter bovenhoek van de alinea moet worden uitgevoerd vanaf gereed, in behandeling en wordt uitgevoerd op voltooid. De uitvoer wordt weer gegeven aan de onderkant van dezelfde alinea. De scherm opname ziet eruit als in de volgende afbeelding:

    ![Een tijdelijke tabel maken op basis van onbewerkte gegevens](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-load-data.png "Een tijdelijke tabel maken op basis van onbewerkte gegevens")

    U kunt ook een titel voor elke alinea opgeven. Selecteer in de rechter bovenhoek van de alinea het **instellingen** pictogram (Sprocket) en selecteer vervolgens **titel weer geven**.  

    > [!NOTE]  
    > % spark2-interpreter wordt niet ondersteund in Zeppelin-notebooks voor alle HDInsight-versies en% sh-interpreter wordt niet ondersteund vanaf HDInsight 4,0.

5. U kunt nu Spark SQL-instructies uitvoeren op de `hvac` tabel. Plak de volgende query in een nieuwe alinea. De query haalt de gebouw-ID op. Ook het verschil tussen het doel en de werkelijke Tempe ratuur voor elk gebouw op een bepaalde datum. Druk op **Shift+Enter**.

    ```sql
    %sql
    select buildingID, (targettemp - actualtemp) as temp_diff, date from hvac where date = "6/1/13"
    ```  

    De **% SQL** -instructie aan het begin geeft aan dat het notitie blok de livy scala-interpreter moet gebruiken.

6. Selecteer het pictogram van het **staaf diagram** om de weer gave te wijzigen.  de **instellingen** die worden weer gegeven nadat u een **staaf diagram** hebt geselecteerd, kunt u kiezen uit **sleutels** en **waarden**.  In de volgende scherm afbeelding ziet u de uitvoer.

    ![Een Spark SQL-instructie uitvoeren met behulp van de notebook1](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-spark-query-1.png "Een Spark SQL-instructie uitvoeren met behulp van de notebook1")

7. U kunt ook Spark SQL-instructies uitvoeren met behulp van variabelen in de query. Het volgende code fragment laat zien hoe u een variabele definieert, `Temp` in de query met de mogelijke waarden waarmee u een query wilt uitvoeren. Wanneer u de query voor het eerst uitvoert, wordt een vervolg keuzelijst automatisch gevuld met de waarden die u hebt opgegeven voor de variabele.

    ```sql
    %sql  
    select buildingID, date, targettemp, (targettemp - actualtemp) as temp_diff from hvac where targettemp > "${Temp = 65,65|75|85}"
    ```

    Plak dit fragment in een nieuwe alinea en druk op **SHIFT + ENTER**. Selecteer vervolgens **65** in de vervolg keuzelijst **temp** .

8. Selecteer het pictogram van het **staaf diagram** om de weer gave te wijzigen.  Selecteer vervolgens **instellingen** en breng de volgende wijzigingen aan:

   * **Groepen:**  Voeg **targettemp** toe.  
   * **Waarden:** i. Verwijder de **datum**.  2. Voeg **temp_diff** toe.  3.  Wijzig de aggregator van **Sum** in **AVG**.  

     In de volgende scherm afbeelding ziet u de uitvoer.

     ![Een Spark SQL-instructie uitvoeren met behulp van de notebook2](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-spark-query-2.png "Een Spark SQL-instructie uitvoeren met behulp van de notebook2")

## <a name="how-do-i-use-external-packages-with-the-notebook"></a>Met het notitie blok Hoe kan ik externe pakketten gebruiken?

Zeppelin notebook in Apache Spark cluster op HDInsight kan externe, door de Community bijgedragen pakketten gebruiken die niet zijn opgenomen in het cluster. Zoek in de [maven-opslag plaats](https://search.maven.org/) naar de volledige lijst met pakketten die beschikbaar zijn. U kunt ook een lijst met beschik bare pakketten uit andere bronnen ophalen. Een volledige lijst met door de Community bijgedragen pakketten is bijvoorbeeld beschikbaar in [Spark-pakketten](https://spark-packages.org/).

In dit artikel ziet u hoe u het [Spark-CSV-](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) pakket gebruikt met de Jupyter notebook.

1. Open de instellingen voor de interpreter. Selecteer in de rechter bovenhoek de naam van de aangemelde gebruiker en selecteer vervolgens **interpreter**.

    ![Interpreter starten](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Hive-uitvoer")

2. Ga naar **livy2** en selecteer **bewerken**.

    ![Settings1 van interpreter wijzigen](./media/apache-spark-zeppelin-notebook/zeppelin-use-external-package-1.png "Settings1 van interpreter wijzigen")

3. Navigeer naar Key `livy.spark.jars.packages` en stel de waarde in de notatie in `group:id:version` . Als u het [Spark-CSV-](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) pakket wilt gebruiken, moet u dus de waarde van de sleutel instellen op `com.databricks:spark-csv_2.10:1.4.0` .

    ![Settings2 van interpreter wijzigen](./media/apache-spark-zeppelin-notebook/zeppelin-use-external-package-2.png "Settings2 van interpreter wijzigen")

    Selecteer **Opslaan** en klik vervolgens op **OK** om de livy-interpreter opnieuw op te starten.

4. Als u wilt weten hoe u kunt aankomen met de waarde van de hierboven ingevoerde sleutel, kunt u dit als volgt doen.

    a. Zoek het pakket in de Maven-opslag plaats. Voor dit artikel hebben we [Spark-CSV](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar)gebruikt.

    b. Verzamel de waarden voor **GroupId**, **ArtifactId** en **Version** uit de opslag plaats.

    ![Externe pakketten gebruiken met Jupyter Notebook](./media/apache-spark-zeppelin-notebook/use-external-packages-with-jupyter.png "Externe pakketten gebruiken met Jupyter Notebook")

    c. De drie waarden samen voegen, gescheiden door een dubbele punt (**:**).

    ```
    com.databricks:spark-csv_2.10:1.4.0
    ```

## <a name="where-are-the-zeppelin-notebooks-saved"></a>Waar worden de Zeppelin-notebooks opgeslagen?

De Zeppelin-notebooks worden opgeslagen in het cluster hoofd knooppunten. Als u het cluster verwijdert, worden ook de notitie blokken verwijderd. Als u uw notitie blokken voor later gebruik op andere clusters wilt behouden, moet u deze exporteren nadat u de taken hebt uitgevoerd. Als u een notitie blok wilt exporteren, selecteert u het pictogram **exporteren** , zoals wordt weer gegeven in de onderstaande afbeelding.

![Notitie blok downloaden](./media/apache-spark-zeppelin-notebook/zeppelin-download-notebook.png "Het notitie blok downloaden")

Met deze actie wordt het notitie blok opgeslagen als een JSON-bestand in de download locatie.

## <a name="use-shiro-to-configure-access-to-zeppelin-interpreters-in-enterprise-security-package-esp-clusters"></a>Gebruiken `Shiro` voor het configureren van toegang tot Zeppelin-tolken in Enterprise Security Package (ESP)-clusters

Zoals hierboven vermeld, `%sh` wordt de interpreter niet ondersteund vanaf HDInsight 4,0. Bovendien is er, aangezien de `%sh` interpreter mogelijke beveiligings problemen introduceert, zoals het openen van de tabtoets met shell-opdrachten, ook verwijderd uit de ESP-clusters van HDInsight 3,6. Dit betekent dat `%sh` interpreter niet beschikbaar is als u standaard op **nieuwe notitie maken** of in de interpreter-gebruikers interface klikt.

Geprivilegieerde domein gebruikers kunnen het `Shiro.ini` bestand gebruiken om de toegang tot de gebruikers interface van de interpreter te beheren. Alleen deze gebruikers kunnen nieuwe `%sh` interpreters maken en machtigingen instellen voor elke nieuwe `%sh` interpreter. Gebruik de volgende stappen om de toegang te beheren met het `shiro.ini` bestand:

1. Definieer een nieuwe rol met behulp van de naam van een bestaande domein groep. In het volgende voor beeld `adminGroupName` is een groep bevoegde gebruikers in Aad. Gebruik geen speciale tekens of spaties in de groeps naam. De tekens nadat u `=` de machtigingen voor deze rol hebt toegewezen. `*` betekent dat de groep volledige machtigingen heeft.

    ```
    [roles]
    adminGroupName = *
    ```

2. Voeg de nieuwe rol toe voor toegang tot Zeppelin-interpreters. In het volgende voor beeld krijgen alle gebruikers in `adminGroupName` toegang tot Zeppelin-tolken en kunnen nieuwe interpreters worden gemaakt. U kunt meerdere rollen tussen de vier Kante haken plaatsen `roles[]` , gescheiden door komma's. Gebruikers die de benodigde machtigingen hebben, hebben dan toegang tot Zeppelin-interpreters.

    ```
    [urls]
    /api/interpreter/** = authc, roles[adminGroupName]
    ```

## <a name="livy-session-management"></a>Livy-sessie beheer

Met de eerste code in uw Zeppelin-notebook wordt een nieuwe livy-sessie in uw cluster gemaakt. Deze sessie wordt gedeeld in alle Zeppelin-notebooks die u later maakt. Als de livy-sessie om welke reden dan ook wordt beëindigd, worden er geen taken uitgevoerd vanuit de Zeppelin-notebook.

In dat geval moet u de volgende stappen uitvoeren voordat u taken kunt uitvoeren vanuit een Zeppelin-notebook.  

1. Start de livy-interpreter vanuit de Zeppelin-notebook. Hiertoe opent u de instellingen voor de interpreter door de naam van de aangemelde gebruiker te selecteren in de rechter bovenhoek en vervolgens **interpreter** te selecteren.

    ![Interpreter starten](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Hive-uitvoer")

2. Ga naar **livy2** en selecteer **opnieuw starten**.

    ![De livy-interpreter opnieuw opstarten](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-restart-interpreter.png "De Zeppelin-interpreter opnieuw opstarten")

3. Een code-cel uitvoeren vanuit een bestaand Zeppelin-notebook. Met deze code wordt een nieuwe livy-sessie in het HDInsight-cluster gemaakt.

## <a name="general-information"></a>Algemene informatie

### <a name="validate-service"></a>Service valideren

Als u de service van Ambari wilt valideren, gaat u naar `https://CLUSTERNAME.azurehdinsight.net/#/main/services/ZEPPELIN/summary` de naam van het cluster.

Als u de service vanaf een opdracht regel wilt valideren, moet u SSH naar het hoofd knooppunt. Schakel de gebruiker over naar Zeppelin met behulp van de opdracht `sudo su zeppelin` . Status opdrachten:

|Opdracht |Beschrijving |
|---|---|
|`/usr/hdp/current/zeppelin-server/bin/zeppelin-daemon.sh status`|Status van de service.|
|`/usr/hdp/current/zeppelin-server/bin/zeppelin-daemon.sh --version`|Service versie.|
|`ps -aux | grep zeppelin`|PID identificeren.|

### <a name="log-locations"></a>Logboek locaties

|Service |Pad |
|---|---|
|Zeppelin-server|/usr/hdp/current/zeppelin-server/|
|Serverlogboeken|/var/log/zeppelin|
|Configuratie-Interpreter, `Shiro` , site.xml, Log4J|/usr/HDP/Current/Zeppelin-server/conf of/etc/Zeppelin/conf|
|PID-map|/var/run/zeppelin|

### <a name="enable-debug-logging"></a>Logboek registratie voor fout opsporing inschakelen

1. Navigeer naar `https://CLUSTERNAME.azurehdinsight.net/#/main/services/ZEPPELIN/summary` de naam van het cluster.

1. Navigeer naar **configs**  >  **Advanced Zeppelin-log4j-Properties**  >  **log4j_properties_content**.

1. Wijzigen `log4j.appender.dailyfile.Threshold = INFO` in `log4j.appender.dailyfile.Threshold = DEBUG` .

1. Toevoegen `log4j.logger.org.apache.zeppelin.realm=DEBUG` .

1. Sla de wijzigingen op en start de service opnieuw.

## <a name="next-steps"></a>Volgende stappen

* [Overzicht: Apache Spark in Azure HDInsight](apache-spark-overview.md)
* [Kernels die beschikbaar zijn voor Jupyter Notebook in Apache Spark cluster voor HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Jupyter op uw computer installeren en verbinding maken met een HDInsight Spark-cluster](apache-spark-jupyter-notebook-install-locally.md)
