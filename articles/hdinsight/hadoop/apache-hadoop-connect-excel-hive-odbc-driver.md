---
title: Excel & Apache Hadoop met stuur programma voor Open Database Connectivity (ODBC)-Azure HDInsight
description: Meer informatie over het instellen en gebruiken van het micro soft Hive ODBC-stuur programma voor Excel voor het opvragen van gegevens in HDInsight-clusters vanuit micro soft Excel.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017,seoapr2020
ms.date: 04/22/2020
ms.openlocfilehash: c7125e25c9716a9934c97f9f8f85666d264d5f89
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104866944"
---
# <a name="connect-excel-to-apache-hadoop-in-azure-hdinsight-with-the-microsoft-hive-odbc-driver"></a>Excel verbinden met Apache Hadoop in Azure HDIsight met behulp van het Hive ODBC-stuurprogramma van Microsoft

[!INCLUDE [ODBC-JDBC-selector](../../../includes/hdinsight-selector-odbc-jdbc.md)]

De Big Data-oplossing van micro soft integreert micro soft Business Intelligence-onderdelen (BI) met Apache Hadoop clusters die in HDInsight zijn geïmplementeerd. Een voor beeld is de mogelijkheid om Excel te verbinden met het Hive-Data Warehouse van een Hadoop-cluster. Verbinding maken via het micro soft ODBC-stuur programma (component Open Database Connectivity).

U kunt de gegevens die zijn gekoppeld aan een HDInsight-cluster via Excel verbinden met de micro soft Power Query-invoeg toepassing voor Excel. Zie [Excel verbinden met HDInsight met Power query](./apache-hadoop-connect-excel-power-query.md)voor meer informatie.

## <a name="prerequisites"></a>Vereisten

Voordat u met dit artikel begint, moet u beschikken over de volgende items:

* An HDInsight Hadoop-cluster. Zie [Aan de slag met Azure HDInsight](apache-hadoop-linux-tutorial-get-started.md) om er een te maken.
* Een werk station met Office 2010 Professional Plus of hoger of Excel 2010 of hoger.

## <a name="install-microsoft-hive-odbc-driver"></a>Microsoft Hive ODBC-stuurprogramma installeren

Down load en installeer het [micro soft Hive ODBC-stuur programma](https://www.microsoft.com/download/details.aspx?id=40886). Kies de versie die overeenkomt met de versie van de toepassing waarin u het ODBC-stuur programma gaat gebruiken.  Voor dit artikel wordt het stuur programma gebruikt voor Office Excel.

## <a name="create-apache-hive-odbc-data-source"></a>Apache Hive ODBC-gegevensbron maken

De volgende stappen laten zien hoe u een Hive-ODBC-gegevens bron maakt.

1. Ga vanuit Windows naar **Start > Windows-beheer programma's > ODBC-gegevens bronnen (32-bits)/(64-bits)**.  Met deze actie wordt het venster **ODBC-gegevens bron beheer** geopend.

   :::image type="content" source="./media/apache-hadoop-connect-excel-hive-odbc-driver/simbahiveodbc-datasourceadmin1.png" alt-text="ODBC-gegevensbronbeheerder" border="true":::

1. Op het tabblad **gebruikers-DSN** selecteert u **Toevoegen** om het venster **Nieuwe gegevensbron maken** te openen.

1. Selecteer **Microsoft Hive ODBC-stuurprogramma** en selecteer vervolgens **Voltooien** om het venster **DSN-installatie van Microsoft Hive ODBC-stuurprogramma** te openen.

1. Typ of selecteer de volgende waarden:

   | Eigenschap | Beschrijving |
   | --- | --- |
   |  Naam van de gegevensbron |Geef uw gegevensbron een naam |
   |  Host(s) |Voer `HDInsightClusterName.azurehdinsight.net` in. Bijvoorbeeld `myHDICluster.azurehdinsight.net`. Opmerking: `HDInsightClusterName-int.azurehdinsight.net` wordt ondersteund zolang de client-VM is gekoppeld aan hetzelfde virtuele netwerk. |
   |  Poort |Gebruik **443**. (Deze poort is gewijzigd van 563 in 443.) |
   |  Database |Gebruik **standaard**. |
   |  Mechanisme |Selecteer **Windows Azure HDInsight Service** |
   |  Gebruikersnaam |Voer de gebruikersnaam van de HTTP-gebruiker van het HDInsight-cluster in. De standaardgebruikersnaam **admin**. |
   |  Wachtwoord |Voer het gebruikerswachtwoord voor het HDInsight-cluster in. Schakel het selectievakje **Wachtwoord opslaan (versleuteld)** in.|

1. Optioneel: Selecteer **Geavanceerde opties...**  

   | Parameter | Beschrijving |
   | --- | --- |
   |  Systeemeigen query gebruiken |Wanneer het ODBC-stuurprogramma is geselecteerd, probeert het NIET TSQL te converteren naar HiveQL. U dient deze alleen te gebruiken als u 100% zeker weet dat u HiveQL-instructies verzendt. Wanneer u verbinding maakt met SQL Server of Azure SQL Database, moet u het selectievakje uitgeschakeld laten. |
   |  Opgehaalde rijen per blok |Bij het ophalen van een groot aantal records is het afstemmen van deze parameter mogelijk vereist om te zorgen voor optimale prestaties. |
   |  Standaardlengte van een tekenreekskolom, binaire kolomlengte, decimale kolomschaal |De lengten en precisie van het gegevenstype kunnen van invloed zijn op hoe gegevens worden geretourneerd. Ze leiden ertoe dat onjuiste gegevens worden geretourneerd vanwege verlies van nauw keurigheid en, of afkap ping. |

    :::image type="content" source="./media/apache-hadoop-connect-excel-hive-odbc-driver/hiveodbc-datasource-advancedoptions1.png" alt-text="Geavanceerde DSN-configuratieopties" border="true":::

1. Selecteer **Testen** om de gegevensbron te testen. Wanneer de gegevens bron op de juiste wijze is geconfigureerd, toont het test resultaat de uitkomst **.**

1. Selecteer **OK** om het testvenster te sluiten.  

1. Selecteer **OK** om het venster **DSN-installatie van Microsoft Hive ODBC-stuurprogramma** te sluiten.  

1. Selecteer **OK** om het venster **ODBC-gegevensbronbeheerder** te sluiten.  

## <a name="import-data-into-excel-from-hdinsight"></a>Gegevens in Excel importeren vanuit HDInsight

In de volgende stappen wordt beschreven hoe u gegevens uit een Hive-tabel importeert in een Excel-werkmap met behulp van de ODBC-gegevens bron die u hebt gemaakt in de vorige sectie.

1. Open een nieuwe of bestaande werkmap in Excel.

2. Ga op het tabblad **gegevens** naar **gegevens ophalen**  >  **uit andere bronnen**  >  **van ODBC** om het venster **van ODBC** te starten.

   :::image type="content" source="./media/apache-hadoop-connect-excel-hive-odbc-driver/simbahiveodbc-excel-dataconnection1.png" alt-text="Wizard Excel-gegevens verbinding openen" border="true":::

3. Selecteer in de vervolg keuzelijst de naam van de gegevens bron die u in de laatste sectie hebt gemaakt en selecteer vervolgens **OK**.

4. Voor het eerste gebruik wordt het dialoog venster **ODBC-stuur programma** geopend. Selecteer **Windows** in het menu links. Selecteer vervolgens **verbinding maken** om het **Navigator** venster te openen.

5. Navigeer vanuit **Navigator** naar **Hive**  >  **default**  >  **hivesampletable** en selecteer **Load**. Het duurt enkele minuten voordat de gegevens in Excel worden geïmporteerd.

   :::image type="content" source="./media/apache-hadoop-connect-excel-hive-odbc-driver/hdinsight-hive-odbc-navigator.png" alt-text="ODBC-Navigator Excel-Hive van HDInsight" border="true":::

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u het micro soft Hive ODBC-stuur programma gebruikt om gegevens op te halen uit de HDInsight-service in Excel. Op dezelfde manier kunt u gegevens ophalen van de HDInsight-service in SQL Database. Het is ook mogelijk om gegevens te uploaden naar een HDInsight-service. Raadpleeg voor meer informatie:

* [Visualiseer Apache Hive gegevens met micro soft-power bi in azure HDInsight](apache-hadoop-connect-hive-power-bi.md).
* [Visualisatie gegevens van interactieve Query's visualiseren met Power bi in azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).
* [Verbinding maken tussen Excel en Apache Hadoop met behulp van Power query](apache-hadoop-connect-excel-power-query.md).
* [Verbinding maken met Azure HDInsight en Apache Hive query's uitvoeren met behulp van data Lake-Hulpprogram ma's voor Visual Studio](apache-hadoop-visual-studio-tools-get-started.md).