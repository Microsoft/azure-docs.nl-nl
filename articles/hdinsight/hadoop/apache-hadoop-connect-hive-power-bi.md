---
title: Apache Hive gegevens visualiseren met Power BI-Azure HDInsight
description: Meer informatie over het gebruik van micro soft Power BI voor het visualiseren van Hive-gegevens die worden verwerkt door Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/24/2020
ms.openlocfilehash: a5732c2dc0a92bd5727eeff39a529630e45683d7
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98946675"
---
# <a name="visualize-apache-hive-data-with-microsoft-power-bi-using-odbc-in-azure-hdinsight"></a>Apache Hive-gegevens visualiseren met Microsoft Power BI met behulp van ODBC in Azure HDInsight

Meer informatie over het verbinden van micro soft Power BI Desktop met Azure HDInsight met behulp van ODBC en Apache Hive gegevens visualiseren.

> [!IMPORTANT]
> U kunt gebruikmaken van het Hive ODBC-stuur programma om te importeren via de algemene ODBC-Connector in Power BI Desktop. Het wordt echter niet aanbevolen voor BI-workloads die niet-interactieve aard hebben van de Hive-query-engine. [Hdinsight Interactive query-connector](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md) en [hdinsight Spark-connector](/power-bi/spark-on-hdinsight-with-direct-connect) zijn betere keuzes voor de prestaties.

In dit artikel laadt u de gegevens uit een `hivesampletable` Hive-tabel naar Power bi. De Hive-tabel bevat een aantal gebruiks gegevens over mobiele telefoons. U kunt de gebruiks gegevens vervolgens op een wereld kaart uitzetten:

![HDInsight-Power BI het kaart rapport](./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-visualization.png)

De informatie is ook van toepassing op het nieuwe type [interactieve query](../interactive-query/apache-interactive-query-get-started.md) cluster. Zie voor informatie over het maken van een verbinding met HDInsight interactieve query met behulp van directe query de [Hive-gegevens van interactieve Query's visualiseren met micro soft power bi met behulp van directe query's in azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).

## <a name="prerequisites"></a>Vereisten

Voordat u dit artikel gaat passeren, hebt u de volgende items nodig:

* HDInsight-cluster. Het cluster kan een HDInsight-cluster met hive of een nieuw, vrijgegeven interactief query cluster zijn. Zie [cluster maken](apache-hadoop-linux-tutorial-get-started.md)voor meer informatie over het maken van clusters.

* [Micro soft power bi Desktop](https://powerbi.microsoft.com/desktop/). U kunt een kopie downloaden van het [micro soft Download centrum](https://www.microsoft.com/download/details.aspx?id=45331).

## <a name="create-hive-odbc-data-source"></a>Hive ODBC-gegevensbron maken

Zie de [component ODBC-gegevens bron maken](apache-hadoop-connect-excel-hive-odbc-driver.md#create-apache-hive-odbc-data-source).

## <a name="load-data-from-hdinsight"></a>Gegevens uit HDInsight laden

De **hivesampletable** -Hive-tabel wordt geleverd met alle HDInsight-clusters.

1. Start Power BI Desktop.

1. Ga in het bovenste menu naar **Start**  >  **gegevens ophalen**  >  **meer...**.

    ![HDInsight Excel Power BI gegevens openen](./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-open-odbc.png)

1. Selecteer in het dialoog venster **gegevens ophalen** **andere** links, selecteer **ODBC** aan de rechter kant en selecteer vervolgens **verbinding maken** aan de onderkant.

1. Selecteer in het dialoog venster **vanuit ODBC** de naam van de gegevens bron die u hebt gemaakt in de laatste sectie in de vervolg keuzelijst. Selecteer vervolgens **OK**.

1. Voor het eerste gebruik wordt het dialoog venster **ODBC-stuur programma** geopend. Selecteer **standaard of aangepast** in het menu links. Selecteer vervolgens **verbinding maken** om de **Navigator** te openen.

1. Vouw in het **Navigatie** venster **ODBC-> Hive > standaard** uit, selecteer **hivesampletable** en selecteer vervolgens **laden**.

## <a name="visualize-data"></a>Gegevens visualiseren

Door gaan met de laatste procedure.

1. Selecteer in het deel venster visualisaties de optie **kaart**, het pictogram een wereldbol.

    ![Power BI van HDInsight-rapport aanpassen](./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-customize.png)

1. Selecteer in het deel venster **velden** de optie **land** en **devicemake**. U kunt de gegevens weer geven die zijn getekend op de kaart.

1. Vouw de kaart uit.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u gegevens kunt visualiseren vanuit HDInsight met behulp van Power BI.  Lees de volgende artikelen voor meer informatie:

* [Koppel Excel aan HDInsight met het micro soft Hive ODBC-stuur programma](./apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Verbinding maken tussen Excel en Apache Hadoop met behulp van Power query](apache-hadoop-connect-excel-power-query.md).
* [Interactieve query Apache Hive gegevens visualiseren met micro soft Power BI met behulp van een directe query](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md)