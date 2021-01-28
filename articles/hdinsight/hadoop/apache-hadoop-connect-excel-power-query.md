---
title: Excel verbinden met Apache Hadoop met Power Query-Azure HDInsight
description: Meer informatie over het gebruik van business intelligence-onderdelen en Power Query voor Excel om toegang te krijgen tot gegevens die zijn opgeslagen in Hadoop op HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/17/2019
ms.openlocfilehash: bcca511bcb61ceab8395ee56b377efa99f095586
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98946705"
---
# <a name="connect-excel-to-apache-hadoop-by-using-power-query"></a>Excel verbinden met Apache Hadoop met behulp van Power Query

Een belang rijke functie van de micro soft-oplossing voor Big data is de integratie van micro soft business intelligence-onderdelen (BI) met Apache Hadoop clusters in azure HDInsight. Een primair voor beeld is de mogelijkheid om Excel te verbinden met het Azure Storage-account dat de gegevens bevat die aan uw Hadoop-cluster zijn gekoppeld met behulp van de Microsoft Power Query voor Excel-invoeg toepassing. In dit artikel vindt u instructies voor het instellen en gebruiken van Power Query voor het opvragen van gegevens die zijn gekoppeld aan een Hadoop-cluster dat wordt beheerd met HDInsight.

## <a name="prerequisites"></a>Vereisten

* Een Apache Hadoop cluster in HDInsight. Zie aan de [slag met HDInsight op Linux](./apache-hadoop-linux-tutorial-get-started.md).
* Een werk station met Windows 10, 7, Windows Server 2008 R2 of een later besturings systeem.
* Microsoft 365 apps voor Enter prise, Office 2016, Office 2013 Professional Plus, Excel 2013 standalone of Office 2010 Professional Plus.

## <a name="install-microsoft-power-query"></a>Micro soft Power Query installeren

Power Query kunt gegevens importeren die zijn uitgevoerd of die zijn gegenereerd door een Hadoop-taak die wordt uitgevoerd op een HDInsight-cluster.

In Excel 2016 is Power Query geïntegreerd in het gegevens lint onder het gedeelte & trans formatie ophalen. Voor oudere Excel-versies downloadt u Microsoft Power Query voor Excel van het [micro soft Download centrum](https://go.microsoft.com/fwlink/?LinkID=286689) en installeert u het.

## <a name="import-hdinsight-data-into-excel"></a>HDInsight-gegevens importeren in Excel

Met de invoeg toepassing Power Query voor Excel kunt u eenvoudig gegevens uit uw HDInsight-cluster importeren in Excel, waar BI-hulpprogram ma's zoals Power Pivot en Power Map kunnen worden gebruikt om de gegevens te controleren, analyseren en presen teren.

1. Start Excel.

1. Een nieuwe lege werkmap maken.

1. Voer de volgende stappen uit op basis van de Excel-versie:

   * Excel 2016

     * Selecteer > **gegevens**  >  **gegevens ophalen**  >  **uit Azure**  >  **van Azure HDInsight (HDFS)**.

       ![HDI. PowerQuery. SelectHdiSource. 2016](./media/apache-hadoop-connect-excel-power-query/powerquery-selecthdisource-excel2016.png)

   * Excel 2013/2010

     * Selecteer **Power query** van  >  **Azure**  >  **vanuit Microsoft Azure HDInsight**.

       ![HDI. PowerQuery.SelectHdiSource](./media/apache-hadoop-connect-excel-power-query/powerquery-selecthdisource.png)

       **Opmerking:** Als u het menu **Power query** niet ziet, gaat u naar **Bestands**  >  **Opties**  >  **invoeg toepassingen** en selecteert u **com** -invoeg toepassingen in de vervolg keuzelijst **beheren** onder aan de pagina. Selecteer de knop **go...** en controleer of het selectie vakje voor de Power query voor Excel-invoeg toepassing is ingeschakeld.

       **Opmerking:** Met Power Query kunt u ook gegevens importeren uit HDFS door te selecteren **uit andere bronnen**.

1. Voer in het dialoog venster **Azure HDInsight (HDFS)** in het tekstvak **account naam of URL** de naam in van het Azure Blob Storage-account dat is gekoppeld aan uw cluster. Selecteer vervolgens **OK**. Dit account kan het standaard opslag account of een gekoppeld opslag account zijn.  De indeling is `https://StorageAccountName.blob.core.windows.net/`.

1. Voer voor **account sleutel** de sleutel in voor het Blob Storage-account en selecteer vervolgens **verbinding maken**. (U hoeft alleen de account gegevens op te geven voor de eerste keer dat u toegang tot deze winkel hebt.)

1. Dubbel klik in het **Navigatie** deel venster aan de linkerkant van de query-editor op de naam van de BLOB storage-container die aan uw cluster is gekoppeld. De naam van de container is standaard hetzelfde als de naam van het cluster.

1. Zoek **HiveSampleData.txt** in de kolom **naam** (het mappad is **. /Hive/Warehouse/hivesampletable/**) en selecteer vervolgens **binary** aan de linkerkant van HiveSampleData.txt. HiveSampleData.txt wordt geleverd met alle cluster. U kunt desgewenst uw eigen bestand gebruiken.

    ![HDI Excel Power query-gegevens importeren](./media/apache-hadoop-connect-excel-power-query/powerquery-importdata.png)

1. Als u wilt, kunt u de naam van de kolom namen wijzigen. Wanneer u klaar bent, selecteert u **sluiten & laden**.  De gegevens zijn geladen in uw werkmap:

    ![Geïmporteerde tabel HDI Excel Power query](./media/apache-hadoop-connect-excel-power-query/powerquery-importedtable.png)

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u Power Query kunt gebruiken om gegevens uit HDInsight op te halen in Excel. Op dezelfde manier kunt u gegevens ophalen van HDInsight in Azure SQL Database. Het is ook mogelijk om gegevens te uploaden naar HDInsight. Lees de volgende artikelen voor meer informatie:

* [Visualiseer Apache Hive gegevens met micro soft-power bi in azure HDInsight](apache-hadoop-connect-hive-power-bi.md).
* [Visualisatie gegevens van interactieve Query's visualiseren met Power bi in azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).
* [Gebruik Apache Zeppelin om Apache Hive query's uit te voeren in azure HDInsight](../interactive-query/hdinsight-connect-hive-zeppelin.md).
* [Koppel Excel aan HDInsight met het micro soft Hive ODBC-stuur programma](apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Verbinding maken met Azure HDInsight en Apache Hive query's uitvoeren met behulp van data Lake-Hulpprogram ma's voor Visual Studio](apache-hadoop-visual-studio-tools-get-started.md).
* [Gebruik het hulp programma Azure HDInsight voor Visual Studio code](../hdinsight-for-vscode.md).
* [Gegevens uploaden naar HDInsight](./../hdinsight-upload-data.md).
