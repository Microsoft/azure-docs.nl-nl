---
title: 'Snelstart: Apache Hadoop, Apache Hive en de Azure HDInsight-portal'
description: In deze quickstart gebruikt u Azure Portal voor het maken van een Hadoop-cluster in HDInsight
keywords: aan de slag met hadoop, hadoop linux, hadoop quickstart, aan de slag met hive, hive quickstart
ms.service: hdinsight
ms.topic: quickstart
ms.custom: hdinsightactive,hdiseo17may2017,mvc,seodec18
ms.date: 02/24/2020
ms.openlocfilehash: 92520be7e9d2bb83cb61856fe3df38487445ad9a
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104863884"
---
# <a name="quickstart-create-apache-hadoop-cluster-in-azure-hdinsight-using-azure-portal"></a>Snelstart: Een Apache Hadoop-cluster maken in Azure HDInsight met Azure Portal

In dit artikel leert u hoe u Apache Hadoop-clusters maakt in HDInsight met behulp van de Azure-portal en vervolgens Apache Hive-taken uitvoert in HDInsight. De meeste Hadoop-taken zijn batchtaken. U maakt een cluster, voert enkele taken uit en verwijdert het cluster vervolgens. In dit artikel gaat u al deze drie taken uitvoeren. Zie [Clusters instellen in HDInsight](../hdinsight-hadoop-provision-linux-clusters.md) voor uitgebreide uitleg over de beschikbare configuraties. Zie [Clusters maken in de portal](../hdinsight-hadoop-create-linux-clusters-portal.md) voor meer informatie over het gebruik van de portal om clusters te maken.

In deze snelstartgids gebruikt u Azure Portal voor het maken van een Hadoop-cluster in HDInsight. U kunt ook een cluster maken met behulp van een [Azure Resource Manager-sjabloon](apache-hadoop-linux-tutorial-get-started.md).

Op dit moment wordt HDInsight geleverd met [zeven verschillende clustertypen](../hdinsight-overview.md#cluster-types-in-hdinsight). Elk clustertype ondersteunt een andere set onderdelen. Alle clustertypen ondersteunen Hive. Zie [Wat is er nieuw in de Apache Hadoop-clusterversies geleverd door HDInsight?](../hdinsight-component-versioning.md) voor een lijst met ondersteunde onderdelen in HDInsight.  

Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="create-an-apache-hadoop-cluster"></a>Een Apache Hadoop-cluster maken

In deze sectie maakt u een Hadoop-cluster in HDInsight met behulp van Azure Portal.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com).

1. Selecteer **+ Een resource maken** in het menu aan de bovenkant.

   :::image type="content" source="./media/apache-hadoop-linux-create-cluster-get-started-portal/azure-portal-create-resource.png" alt-text="Een HDInsight-cluster voor resources maken" border="true":::

1. Selecteer **Analytics** > **Azure HDInsight** om naar de pagina **HDInsight-cluster maken** te gaan.

1. Geef op het tabblad **Basis** de volgende gegevens op:

   |Eigenschap  |Beschrijving  |
   |---------|---------|
   |Abonnement    |  Selecteer in de vervolgkeuzelijst het Azure-abonnement dat wordt gebruikt voor het cluster. |
   |Resourcegroep     | Selecteer in de vervolgkeuzelijst de bestaande resourcegroep of selecteer **Nieuwe maken**.|
   |Clusternaam   | Geef een wereldwijd unieke naam op. De naam mag bestaan uit maximaal 59 tekens, inclusief letters, cijfers en afbreekstreepjes. De eerste en laatste tekens van de naam mogen geen streepjes zijn. |
   |Region    | Selecteer in de vervolgkeuzelijst een regio waarin het cluster wordt gemaakt.  Kies een locatie zo dicht mogelijk bij u in de buurt voor betere prestaties. |
   |Clustertype| Selecteer **Clustertype selecteren**. Selecteer vervolgens **Hadoop** als het clustertype.|
   |Versie|Selecteer een **versie** in de vervolgkeuzelijst. Gebruik de standaardversie als u niet weet wat u moet kiezen.|
   |Gebruikersnaam/Wachtwoord voor clusteraanmeldgegevens    | De standaardaanmeldingsnaam is **admin**. Het wachtwoord moet uit minstens tien tekens bestaan en moet minstens één cijfer, één hoofdletter, één kleine letter en één niet-alfanumeriek teken bevatten (uitgezonderd ' " ` \). Zorg ervoor dat u **geen makkelijk te raden** wachtwoorden gebruikt, zoals 'Pass@word1'.|
   |SSH-gebruikersnaam (Secure Shell) | De standaardgebruikersnaam is **sshuser**.  U kunt hier echter een andere naam opgeven als u dat wilt. |
   |Het wachtwoord voor clusteraanmelding gebruiken voor SSH| Schakel dit selectievakje in als u voor de SSH-gebruiker het wachtwoord wilt gebruiken dat u hebt opgegeven voor Wachtwoord voor clusteraanmeldgegevens.|

   :::image type="content" source="./media/apache-hadoop-linux-create-cluster-get-started-portal/azure-portal-cluster-basics.png" alt-text="Aan de slag met HDInsight-cluster op basis van Linux: basiswaarden opgeven" border="true":::

   Selecteer de knop **Volgende: Opslag >>** om door te gaan naar de opslaginstellingen.

1. Geef op het tabblad **Opslag** de volgende waarden op:

   |Eigenschap  |Beschrijving  |
   |---------|---------|
   |Type van primaire opslag|Gebruik de standaardwaarde **Azure Storage**.|
   |Selectiemethode|Gebruik de standaardwaarde **Selecteer in lijst**.|
   |Primair opslagaccount|Gebruik de vervolgkeuzelijst om een bestaand opslagaccount te selecteren of selecteer **Nieuwe maken**. Als u een nieuw account maakt, moet de naam 3 tot 24 tekens lang zijn en mag deze alleen cijfers en kleine letters bevatten|
   |Container|Gebruik de waarde die automatisch is ingevuld.|

   :::image type="content" source="./media/apache-hadoop-linux-create-cluster-get-started-portal/azure-portal-cluster-storage.png" alt-text="HDInsight Linux aan de slag, clusteropslagwaarden opgeven" border="true":::

   Elk cluster heeft een [Azure Storage account](../hdinsight-hadoop-use-blob-storage.md), een [Azure data Lake gen1](../hdinsight-hadoop-use-data-lake-storage-gen1.md)of een [`Azure Data Lake Storage Gen2`](../hdinsight-hadoop-use-data-lake-storage-gen2.md) afhankelijkheid. Dit wordt het standaardopslagaccount genoemd. Het HDInsight-cluster en het standaardopslagaccount moeten samen in dezelfde Azure-regio worden geplaatst. Het opslagaccount wordt niet verwijderd wanneer er clusters worden verwijderd.

   Selecteer het tabblad **Beoordelen en maken**.

1. Controleer op het tabblad **Beoordelen en maken** de waarden die u in de eerdere stappen hebt geselecteerd.

   :::image type="content" source="./media/apache-hadoop-linux-create-cluster-get-started-portal/azure-portal-cluster-review-create-hadoop.png" alt-text="Aan de slag met HDInsight-cluster op basis van Linux: overzicht" border="true":::

1. Selecteer **Maken**. Het duurt ongeveer 20 minuten om een cluster te maken.

   Zodra het cluster is gemaakt, ziet u de overzichtspagina van het cluster in Azure Portal.

   :::image type="content" source="./media/apache-hadoop-linux-create-cluster-get-started-portal/cluster-settings-overview.png" alt-text="Aan de slag met clusterinstellingen in HDInsight op basis van Linux" border="true":::

## <a name="run-apache-hive-queries"></a>Apache Hive-query's uitvoeren

[Apache Hive](hdinsight-use-hive.md) is het meest populaire onderdeel dat in HDInsight wordt gebruikt. Er zijn veel manieren om Hive-taken uit te voeren in HDInsight. In deze quickstart gebruikt u de Ambari Hive-weergave in de portal. Voor andere methoden voor het indienen van Hive-taken raadpleegt u [Hive gebruiken in HDInsight](hdinsight-use-hive.md).

> [!NOTE]
> Apache Hive-weergave is niet beschikbaar in HDInsight 4.0.

1. Als u Ambari wilt openen, selecteert u **Clusterdashboard** in de vorige schermafbeelding.  U kunt ook bladeren naar  `https://ClusterName.azurehdinsight.net`, waarbij `ClusterName` het cluster is dat u in de vorige sectie hebt gemaakt.

   :::image type="content" source="./media/apache-hadoop-linux-create-cluster-get-started-portal/hdinsight-linux-get-started-open-cluster-dashboard.png" alt-text="Aan de slag met HDInsight-cluster op basis van Linux: dashboard" border="true":::

2. Voer de gebruikersnaam en het wachtwoord voor Hadoop in die u hebt opgegeven tijdens het maken van het cluster. De standaardgebruikersnaam **admin**.

3. Open **Hive-weergave** zoals weergegeven in de volgende schermafbeelding:

   :::image type="content" source="./media/apache-hadoop-linux-create-cluster-get-started-portal/hdi-select-hive-view.png" alt-text="Hive-weergave selecteren in Ambari" border="true":::

4. Plak in het tabblad **QUERY** de volgende HiveQL-instructies in het werkblad:

   ```sql
   SHOW TABLES;
   ```

    :::image type="content" source="./media/apache-hadoop-linux-create-cluster-get-started-portal/hdi-apache-hive-view1.png" alt-text="HDInsight Hive-weergave Query Editor" border="true":::

5. Selecteer **Uitvoeren**. Er wordt een tabblad **RESULTATEN** weergegeven onder het tabblad **QUERY** met informatie over de taak.

   Nadat de query is voltooid, worden de resultaten van de bewerking weergegeven op het tabblad **QUERY**. U ziet één tabel met de naam **hivesampletable**. Deze Hive-voorbeeldtabel is bij alle HDInsight-clusters inbegrepen.

   :::image type="content" source="./media/apache-hadoop-linux-create-cluster-get-started-portal/hdinsight-hive-views.png" alt-text="Resultaten weergeven in HDInsight Apache Hive-weergave" border="true":::

6. Herhaal stap 4 en 5 om de volgende query uit te voeren:

   ```sql
   SELECT * FROM hivesampletable;
   ```

7. U kunt de resultaten van de query ook opslaan. Selecteer de menuknop aan de rechterkant en geef aan of u de resultaten wilt downloaden als een CSV-bestand of deze wilt opslaan in het opslagaccount dat aan het cluster is gekoppeld.

   :::image type="content" source="./media/apache-hadoop-linux-create-cluster-get-started-portal/hdinsight-linux-hive-view-save-results.png" alt-text="Resultaat van Apache Hive-query opslaan" border="true":::

Nadat u een Hive-taak hebt voltooid, kunt u [de resultaten exporteren naar een Azure SQL Database- of SQL Server-database](apache-hadoop-use-sqoop-mac-linux.md). U kunt ook [de resultaten weergeven in Excel](apache-hadoop-connect-excel-power-query.md). Zie [Apache Hive en HiveQL gebruiken met Apache Hadoop in HDInsight voor het analyseren van een voorbeeldbestand van de Apache-log4j](hdinsight-use-hive.md) voor meer informatie over het gebruik van Hive in HDInsight.

## <a name="clean-up-resources"></a>Resources opschonen

Nadat u de quickstart hebt voltooid, kunt u het cluster verwijderen. Met HDInsight worden uw gegevens opgeslagen in Azure Storage zodat u een cluster veilig kunt verwijderen wanneer deze niet wordt gebruikt. Voor een HDInsight-cluster worden ook kosten in rekening gebracht, zelfs wanneer het niet wordt gebruikt. Aangezien de kosten voor het cluster vaak zoveel hoger zijn dan de kosten voor opslag, is het financieel gezien logischer clusters te verwijderen wanneer ze niet worden gebruikt.

> [!NOTE]  
> Als u *meteen* verder wilt gaan met het volgende artikel om te leren hoe u ETL-bewerkingen uitvoert met behulp van Hadoop in HDInsight, kunt u het cluster beter behouden. In die zelfstudie hebt u namelijk ook een Hadoop-cluster nodig. Als u echter niet direct verdergaat met het volgende artikel, moet u het cluster nu verwijderen.

### <a name="to-delete-the-cluster-andor-the-default-storage-account"></a>Het cluster en/of het standaardopslagaccount verwijderen

1. Ga terug naar het browsertabblad voor Azure Portal. U komt terecht op de overzichtspagina voor het cluster. Selecteer **Verwijderen** als u alleen het cluster wilt verwijderen maar het standaardopslagaccount wilt behouden.

   :::image type="content" source="./media/apache-hadoop-linux-create-cluster-get-started-portal/hdinsight-delete-cluster.png" alt-text="Azure HDInsight-cluster verwijderen" border="true":::

2. Als u het cluster en het standaardopslagaccount wilt verwijderen, selecteert u de naam van de resourcegroep (gemarkeerd in de vorige schermafbeelding) om de pagina van de resourcegroep te openen.

3. Selecteer **Resourcegroep verwijderen** om de resourcegroep te verwijderen. De groep bevat zowel het cluster als het standaardopslagaccount. Als u de resourcegroep verwijdert, wordt ook het opslagaccount verwijderd. Als u het opslagaccount wilt behouden, verwijdert u alleen het cluster.

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u geleerd hoe u een HDInsight-cluster op basis van Linux maakt met behulp van een Resource Manager-sjabloon, en hoe u eenvoudige Hive-query's uitvoert. In het volgende artikel leert u hoe u een ETL-bewerking (Extraction, Transformation, Loading) uitvoert met behulp van Hadoop in HDInsight.

> [!div class="nextstepaction"]
> [Gegevens extraheren, transformeren en laden met Interactive Query in HDInsight](../interactive-query/interactive-query-tutorial-analyze-flight-data.md)
