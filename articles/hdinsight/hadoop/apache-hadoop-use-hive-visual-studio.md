---
title: Apache Hive & Data Lake-hulpprogram ma's voor Visual Studio-Azure HDInsight
description: Meer informatie over het gebruik van de Data Lake-hulpprogram ma's voor Visual Studio voor het uitvoeren van Apache Hive query's met Apache Hadoop in azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 11/27/2019
ms.openlocfilehash: 4512c9d9fdb66713ba24fbf30278e5d5dbb2ae23
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104863748"
---
# <a name="run-apache-hive-queries-using-the-data-lake-tools-for-visual-studio"></a>Apache Hive-query's uitvoeren met de Data Lake-hulpprogramma's voor Visual Studio

Meer informatie over het gebruik van de Data Lake-hulpprogram ma's voor Visual Studio voor het opvragen van Apache Hive. Met de Data Lake-hulpprogram ma's kunt u eenvoudig Hive-query's maken, verzenden en bewaken om te Apache Hadoop op Azure HDInsight.

## <a name="prerequisites"></a>Vereisten

* Een Apache Hadoop cluster in HDInsight. Zie voor meer informatie over het maken van dit item [Apache Hadoop cluster maken in azure HDInsight met behulp van Resource Manager-sjabloon](./apache-hadoop-linux-tutorial-get-started.md).

* [Visual Studio](https://visualstudio.microsoft.com/vs/). In de stappen in dit artikel wordt gebruikgemaakt van Visual Studio 2019.

* HDInsight-hulpprogram ma's voor Visual Studio of Azure Data Lake-hulpprogram ma's voor Visual Studio. Zie [Data Lake-Hulpprogram ma's voor Visual Studio installeren](apache-hadoop-visual-studio-tools-get-started.md#install-data-lake-tools-for-visual-studio)voor meer informatie over het installeren en configureren van de hulpprogram ma's.

## <a name="run-apache-hive-queries-using-the-visual-studio"></a>Apache Hive query's uitvoeren met Visual Studio

U hebt twee opties voor het maken en uitvoeren van Hive-query's:

* Ad-hoc-query's maken.
* Maak een Hive-toepassing.

### <a name="create-an-ad-hoc-hive-query"></a>Een ad hoc Hive-query maken

Ad hoc-query's kunnen worden uitgevoerd in **batch** of **interactieve** modus.

1. Start **Visual Studio** en selecteer **door gaan zonder code**.

2. Klik vanuit **Server Explorer** met de rechter muisknop op **Azure**, selecteer **verbinding maken met Microsoft Azure abonnement...** en voltooi het aanmeldings proces.

3. Vouw **HDInsight** uit, klik met de rechter muisknop op het cluster waar u de query wilt uitvoeren en selecteer vervolgens **een Hive-query schrijven**.

4. Voer de volgende Hive-query in:

    ```hql
    SELECT * FROM hivesampletable;
    ```

5. Selecteer **Uitvoeren**. De uitvoerings modus wordt standaard ingesteld op **interactief**.

    :::image type="content" source="./media/apache-hadoop-use-hive-visual-studio/vs-execute-hive-query.png" alt-text="Interactieve Hive-query uitvoeren, Visual Studio" border="true":::

6. Als u dezelfde query wilt uitvoeren in de **batch** modus, schakelt u in de vervolg keuzelijst van **interactief** naar **batch**. De knop uitvoeren wordt gewijzigd van **uitvoeren** naar **verzenden**.

    :::image type="content" source="./media/apache-hadoop-use-hive-visual-studio/visual-studio-batch-query.png" alt-text="Batch Hive-query, Visual Studio verzenden" border="true":::

    De Hive-editor ondersteunt IntelliSense. Data Lake Tools voor Visual Studio biedt ondersteuning voor het laden van externe metagegevens wanneer u het Hive-script bewerkt. Als u bijvoorbeeld typt `SELECT * FROM` , geeft IntelliSense alle voorgestelde tabel namen weer. Wanneer een tabelnaam wordt opgegeven, geeft IntelliSense de kolomnamen weer. De hulpprogramma's ondersteunen de meeste DML-instructies, subquery's en ingebouwde UDF's van Hive. IntelliSense suggereert alleen de metagegevens van het cluster dat in de HDInsight-werkbalk is geselecteerd.

7. Selecteer in de werk balk query (het gebied onder het tabblad Query en boven de query tekst) de optie **verzenden** of selecteer de vervolg keuze pijl naast **verzenden** en kies **Geavanceerd** in de keuze lijst. Als u de laatste optie selecteert,

8. Als u de optie Geavanceerd verzenden hebt geselecteerd, configureert u **taak naam**, **argumenten**, **aanvullende configuraties** en **status map** in het dialoog venster **script indienen** . Selecteer vervolgens **verzenden**.

    :::image type="content" source="./media/apache-hadoop-use-hive-visual-studio/vs-tools-submit-jobs-advanced.png" alt-text="Het dialoog venster script indienen, HDInsight Hadoop Hive-query" border="true":::

### <a name="create-a-hive-application"></a>Een Hive-toepassing maken

Voer de volgende stappen uit om een Hive-query uit te voeren door een Hive-toepassing te maken:

1. Open **Visual Studio**.

2. Selecteer in het **Startvenster** de optie **Een nieuw project maken**.

3. In het venster **een nieuw project maken** in het vak **zoeken naar sjablonen** voert u *Hive* in. Kies vervolgens **Hive-toepassing** en selecteer **volgende**.

4. Voer in het venster **uw nieuwe project configureren** een **project naam** in, selecteer of maak een **locatie** voor het nieuwe project en selecteer vervolgens **maken**.

5. Open het **script. HQL** -bestand dat is gemaakt met dit project en plak de volgende HiveQL-instructies:

    ```hql
    set hive.execution.engine=tez;
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION '/example/data/';
    SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND  INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;
    ```

    Deze instructies doen de volgende acties:

    * `DROP TABLE`: Hiermee verwijdert u de tabel als deze bestaat.

    * `CREATE EXTERNAL TABLE`: Hiermee wordt een nieuwe externe tabel in Hive gemaakt. Externe tabellen slaan de tabel definitie in Hive alleen op. (De gegevens blijven op de oorspronkelijke locatie.)

        > [!NOTE]  
        > Externe tabellen moeten worden gebruikt wanneer u verwacht dat de onderliggende gegevens worden bijgewerkt door een externe bron, zoals een MapReduce-taak of een Azure-service.
        >
        > Als u een externe tabel verwijdert, worden de gegevens **niet** verwijderd, maar alleen de tabel definitie.

    * `ROW FORMAT`: Hiermee wordt aangegeven hoe de gegevens worden opgemaakt. In dit geval worden de velden in elk logboek gescheiden door een spatie.

    * `STORED AS TEXTFILE LOCATION`: Hiermee wordt aangegeven dat de gegevens worden opgeslagen in de map *voor beeld/data* , en dat deze is opgeslagen als tekst.

    * `SELECT`: Selecteert een telling van alle rijen waarin `t4` de kolom de waarde bevat `[ERROR]` . Deze instructie retourneert een waarde van `3` , omdat drie rijen deze waarde bevatten.

    * `INPUT__FILE__NAME LIKE '%.log'`: Hiermee wordt aangegeven dat de Hive alleen gegevens retourneert uit bestanden die eindigen op. log. Met deze component wordt de zoek opdracht beperkt tot het *voorbeeld logboek* bestand dat de gegevens bevat.

6. Selecteer op de werk balk van het query bestand (die een vergelijk bare vormgeving heeft met de werk balk van de ad-hoc query) het HDInsight-cluster dat u wilt gebruiken voor deze query. Wijzig vervolgens **interactief** naar **batch** (indien nodig) en selecteer **verzenden** om de instructies als een Hive-taak uit te voeren.

   Het **overzicht** van de Hive-taak wordt weer gegeven en geeft informatie weer over de taak die wordt uitgevoerd. Gebruik de koppeling **vernieuwen** om de taak informatie te vernieuwen totdat de **taak status** is gewijzigd in **voltooid**.

   :::image type="content" source="./media/apache-hadoop-use-hive-visual-studio/hdinsight-job-summary.png" alt-text="Samen vatting van Hive-taak, Hive-toepassing, Visual Studio" border="true":::

7. Selecteer **taak uitvoer** om de uitvoer van deze taak weer te geven. Dit wordt weer gegeven `[ERROR] 3` . Dit is de waarde die wordt geretourneerd door deze query.

### <a name="additional-example"></a>Extra voor beeld

In het volgende voor beeld wordt gebruikgemaakt van de `log4jLogs` tabel die u in de vorige procedure hebt gemaakt, [maakt u een Hive-toepassing](#create-a-hive-application).

1. Klik vanuit **Server Explorer** met de rechter muisknop op uw cluster en selecteer **een Hive-query schrijven**.

2. Voer de volgende Hive-query in:

    ```hql
    set hive.execution.engine=tez;
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
    ```

    Deze instructies doen de volgende acties:

    * `CREATE TABLE IF NOT EXISTS`: Hiermee wordt een tabel gemaakt als deze nog niet bestaat. Omdat het `EXTERNAL` sleutel woord niet wordt gebruikt, maakt deze instructie een interne tabel. Interne tabellen worden opgeslagen in het Hive-Data Warehouse en worden beheerd door Hive.

        > [!NOTE]  
        > In tegens telling tot `EXTERNAL` tabellen, verwijdert het verwijderen van een interne tabel ook de onderliggende gegevens.

    * `STORED AS ORC`: De gegevens worden opgeslagen in de Orc-indeling ( *Optimized Row in kolommen* ). ORC is een zeer geoptimaliseerde en efficiënte indeling voor het opslaan van Hive-gegevens.

    * `INSERT OVERWRITE ... SELECT`: Selecteert rijen uit de `log4jLogs` tabel die bevat `[ERROR]` en voegt vervolgens de gegevens in de `errorLogs` tabel in.

3. Wijzig **interactief** naar **batch** , indien nodig, en selecteer vervolgens **verzenden**.

4. Als u wilt controleren of de tabel is gemaakt, gaat u naar **Server Explorer** en vouwt u **Azure**  >  **HDInsight** uit. Breid uw HDInsight-cluster uit en vouw vervolgens de standaard- **Hive-data bases** uit  >  . De tabel **errorLogs** en de tabel **log4jLogs** worden weer gegeven.

## <a name="next-steps"></a>Volgende stappen

Zoals u kunt zien, bieden de HDInsight-hulpprogram ma's voor Visual Studio een eenvoudige manier om te werken met hive-query's op HDInsight.

* Zie [Wat is Apache Hive en HiveQL in azure hdinsight?](hdinsight-use-hive.md) voor algemene informatie over Hive in HDInsight?

* Zie [MapReduce gebruiken in Apache Hadoop op hdinsight](hdinsight-use-mapreduce.md) voor informatie over andere manieren waarop u met Hadoop in hdinsight kunt werken.

* Zie[Data Lake-Hulpprogram ma's voor Visual Studio gebruiken om verbinding te maken met Azure hdinsight en Apache Hive query's uit te voeren](apache-hadoop-visual-studio-tools-get-started.md) voor meer informatie over de HDInsight-hulpprogram Ma's voor Visual Studio.
