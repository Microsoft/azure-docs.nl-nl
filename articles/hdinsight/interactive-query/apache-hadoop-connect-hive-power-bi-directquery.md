---
title: Interactieve query-Hive-gegevens bekijken met Power BI in azure HDInsight
description: Micro soft Power BI gebruiken om interactieve query-Hive-gegevens te visualiseren vanuit Azure HDInsight
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 06/17/2019
ms.openlocfilehash: 7f249bb0e81bf3a371b8743a304ef49baffaed7a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98941393"
---
# <a name="visualize-interactive-query-apache-hive-data-with-microsoft-power-bi-using-direct-query-in-hdinsight"></a>Interactieve query Apache Hive gegevens visualiseren met micro soft Power BI met behulp van directe query's in HDInsight

In dit artikel wordt beschreven hoe u micro soft Power BI verbindt met Azure HDInsight Interactive query-clusters en Apache Hive gegevens visualiseren met behulp van directe query's. In het voor beeld worden de gegevens van een `hivesampletable` Hive-tabel geladen in Power bi. De `hivesampletable` Hive-tabel bevat een aantal gebruiks gegevens over mobiele telefoons. U kunt de gebruiks gegevens vervolgens op een wereld kaart uitzetten:

![HDInsight-Power BI het kaart rapport](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-visualization.png)

U kunt gebruikmaken van het [Apache Hive ODBC-stuur programma](../hadoop/apache-hadoop-connect-hive-power-bi.md) om te importeren via de algemene ODBC-connector in Power bi Desktop. Het wordt echter niet aanbevolen voor BI-workloads die niet-interactieve aard hebben van de Hive-query-engine. [Hdinsight Interactive query connector](./apache-hadoop-connect-hive-power-bi-directquery.md) en [hdinsight Apache Spark connector](/power-bi/spark-on-hdinsight-with-direct-connect) zijn de beste keuze voor de prestaties.

## <a name="prerequisites"></a>Vereisten
Voordat u dit artikel gaat passeren, hebt u de volgende items nodig:

* **HDInsight-cluster**. Het cluster kan een HDInsight-cluster met Apache Hive of een nieuw, vrijgegeven interactief query cluster zijn. Zie [cluster maken](../hadoop/apache-hadoop-linux-tutorial-get-started.md)voor meer informatie over het maken van clusters.
* **[Micro soft power bi Desktop](https://powerbi.microsoft.com/desktop/)**. U kunt een kopie downloaden van het [micro soft Download centrum](https://www.microsoft.com/download/details.aspx?id=45331).

## <a name="load-data-from-hdinsight"></a>Gegevens uit HDInsight laden

De `hivesampletable` Hive-tabel wordt geleverd met alle HDInsight-clusters.

1. Start Power BI Desktop.

2. Ga in de menu balk naar **Start**  >  **gegevens ophalen**  >  **meer...**.

    ![HDInsight-Power BI gegevens meer ophalen](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-open-odbc.png)

3. In het venster **gegevens ophalen** voert u **hdinsight** in het zoekvak in.  

4. Selecteer in de zoek resultaten **HDInsight Interactive query** en selecteer vervolgens **verbinding maken**.  Als u **HDInsight Interactive query** niet ziet, moet u uw Power bi Desktop bijwerken naar de nieuwste versie.

5. Selecteer **door gaan** om het dialoog venster **verbinding maken met een service van derden** te sluiten.

6. Voer in het venster **HDInsight Interactive query** de volgende gegevens in en selecteer **OK**:

    |Eigenschap | Waarde |
    |---|---|
    |Server |Voer de naam van het cluster in, bijvoorbeeld *myiqcluster.azurehdinsight.net*.|
    |Database |Voer de **standaard waarde** in voor dit artikel.|
    |Gegevensverbindingsmodus |Selecteer **DirectQuery** voor dit artikel.|

    ![HDInsight Interactive query Power BI DirectQuery Connect](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-interactive-query-power-bi-connect.png)

7. Voer de HTTP-referenties in en selecteer vervolgens **verbinding maken**. De standaard gebruikersnaam is **admin**.

8. Selecteer **hivesampletale** in het **Navigatie** venster in het linkerdeel venster.

9. Selecteer **laden** in het hoofd venster.

    ![HDInsight Interactive query Power BI hivesampletable](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-interactive-query-power-bi-hivesampletable.png)

## <a name="visualize-data-on-a-map"></a>Gegevens op een kaart visualiseren

Door gaan met de laatste procedure.

1. Selecteer in het deel venster visualisaties de optie **kaart**, het pictogram wereldbol. Er wordt een algemene toewijzing weer gegeven in het hoofd venster.

    ![Power BI van HDInsight-rapport aanpassen](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-customize.png)

2. Selecteer in het deel venster velden de optie **land** en **devicemake**. Een wereld kaart met de gegevens punten wordt na enkele ogen blikken weer gegeven in het hoofd venster.

3. Vouw de kaart uit.

## <a name="next-steps"></a>Volgende stappen
In dit artikel hebt u geleerd hoe u gegevens kunt visualiseren vanuit HDInsight met behulp van micro soft Power BI.  Raadpleeg de volgende artikelen voor meer informatie over gegevens visualisatie:

* [Visualiseer Apache Hive gegevens met micro soft-power bi met behulp van ODBC in azure HDInsight](../hadoop/apache-hadoop-connect-hive-power-bi.md). 
* [Gebruik Apache Zeppelin om Apache Hive query's uit te voeren in azure HDInsight](../interactive-query/hdinsight-connect-hive-zeppelin.md).
* [Koppel Excel aan HDInsight met het micro soft Hive ODBC-stuur programma](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Verbinding maken tussen Excel en Apache Hadoop met behulp van Power query](../hadoop/apache-hadoop-connect-excel-power-query.md).
* [Verbinding maken met Azure HDInsight en Apache Hive query's uitvoeren met behulp van data Lake-Hulpprogram ma's voor Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).
* [Gebruik het hulp programma Azure HDInsight voor Visual Studio code](../hdinsight-for-vscode.md).
* [Gegevens uploaden naar HDInsight](./../hdinsight-upload-data.md).