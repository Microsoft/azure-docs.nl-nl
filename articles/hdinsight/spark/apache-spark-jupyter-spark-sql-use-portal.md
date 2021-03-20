---
title: 'Quickstart: Een Spark-cluster in HDInsight maken met behulp van Azure Portal'
description: Deze quickstart laat zien hoe u met Azure Portal een Apache Spark-cluster maakt in Azure HDInsight en hoe u een Spark SQL-query uitvoert.
ms.service: hdinsight
ms.topic: quickstart
ms.custom: mvc
ms.date: 02/25/2020
ms.openlocfilehash: 18225703b2a390f64b4bebd58b0167dd77c454d0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98930370"
---
# <a name="quickstart-create-apache-spark-cluster-in-azure-hdinsight-using-azure-portal"></a>Quickstart: Een Apache Spark-cluster maken in Azure HDInsight met Azure Portal

In deze quickstart gebruikt u Azure Portal om een Apache Spark-cluster te maken in Azure HDInsight. Vervolgens maakt u een Jupyter-notebook en gebruikt u dit om Spark SQL-query's uit te voeren op Apache Hive-tabellen. Azure HDInsight is een beheerde, zeer uitgebreide open-source analyseservice voor bedrijven. Het Apache Spark-raamwerk voor HDInsight maakt het mogelijk om snelle gegevensanalyses en clusterberekeningen uit te voeren met behulp van verwerking in het geheugen. Via Jupyter Notebook kunt u met uw gegevens werken, code combineren met markdown-tekst en eenvoudige visualisaties uitvoeren.

Zie [Clusters instellen in HDInsight](../hdinsight-hadoop-provision-linux-clusters.md) voor uitgebreide uitleg over de beschikbare configuraties. Zie [Clusters maken in de portal](../hdinsight-hadoop-create-linux-clusters-portal.md) voor meer informatie over het gebruik van de portal om clusters te maken.

Als u meerdere clusters tegelijk gebruikt, wilt u een virtueel netwerk maken. Als u een Spark-cluster gebruikt, wilt u ook de Hive Warehouse Connector gebruiken. Zie [Plan a virtual network voor Azure HDInsight](../hdinsight-plan-virtual-network-deployment.md) en [Integrate Apache Spark and Apache Hive with the Hive Warehouse Connector](../interactive-query/apache-hive-warehouse-connector.md) voor meer informatie.

> [!IMPORTANT]  
> HDInsight-clusters worden pro rato per minuut gefactureerd, ongeacht of u er wel of niet gebruik van maakt. Verwijder uw cluster daarom als u er klaar mee bent. Zie voor meer informatie de sectie [Resources opschonen](#clean-up-resources) van dit artikel.

## <a name="prerequisites"></a>Vereisten

Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

## <a name="create-an-apache-spark-cluster-in-hdinsight"></a>Een Apache Spark-cluster maken in Azure HDInsight

U kunt de Azure Portal gebruiken om een HDInsight-cluster te maken dat gebruikmaakt van Azure Storage Blobs als de clusteropslag. Zie [Snelstart: Clusters instellen in HDInsight](../hdinsight-hadoop-provision-linux-clusters.md) voor meer informatie over het gebruik van Data Lake Storage Gen2.

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).

1. Selecteer **+ Een resource maken** in het menu aan de bovenkant.

    ![Een resource maken in Azure Portal](./media/apache-spark-jupyter-spark-sql-use-portal/azure-portal-create-resource.png "Een resource maken in Azure Portal")

1. Selecteer **Analytics** > **Azure HDInsight** om naar de pagina **HDInsight-cluster maken** te gaan.

1. Geef op het tabblad **Basis** de volgende gegevens op:

    |Eigenschap  |Beschrijving  |
    |---------|---------|
    |Abonnement  | Selecteer in de vervolgkeuzelijst het Azure-abonnement dat wordt gebruikt voor het cluster. |
    |Resourcegroep | Selecteer in de vervolgkeuzelijst de bestaande resourcegroep of selecteer **Nieuwe maken**.|
    |Clusternaam | Geef een wereldwijd unieke naam op.|
    |Region   | Selecteer in de vervolgkeuzelijst een regio waarin het cluster wordt gemaakt. |
    |Clustertype| Selecteer Clustertype selecteren om een lijst te openen. Selecteer **Spark** in de lijst.|
    |Clusterversie|Dit veld wordt automatisch ingevuld met de standaardversie zodra het clustertype is geselecteerd.|
    |Gebruikersnaam voor clusteraanmeldgegevens| Voer de gebruikersnaam voor aanmelding bij het cluster in.  De standaardnaam is **admin**. U gebruikt dit account om u later in de quickstart aan te melden bij het Jupyter-notebook. |
    |Wachtwoord voor clusteraanmeldgegevens| Voer het wachtwoord voor aanmelding bij het cluster in. |
    |SSH-gebruikersnaam (Secure Shell)| Voer de SSH-gebruikersnaam in. De SSH-gebruikersnaam voor deze snelstartgids is **sshuser**. De standaardinstelling is dat voor dit account hetzelfde wachtwoord wordt gebruikt als voor *Gebruikersnaam voor clusteraanmeldgegevens*. |

    ![Schermopname toont 'HDInsight-cluster maken' waarbij het tabblad 'Basis' is geselecteerd.](./media/apache-spark-jupyter-spark-sql-use-portal/azure-portal-cluster-basics-spark.png "Een Spark-cluster maken in HDInsight met de basisconfiguraties")

    Selecteer **Volgende: Opslag >>** om naar de pagina **Opslag** te gaan.

1. Geef onder **Opslag** de volgende waarden op:

    |Eigenschap  |Beschrijving  |
    |---------|---------|
    |Type van primaire opslag|Gebruik de standaardwaarde **Azure Storage**.|
    |Selectiemethode|Gebruik de standaardwaarde **Selecteer in lijst**.|
    |Primair opslagaccount|Gebruik de waarde die automatisch is ingevuld.|
    |Container|Gebruik de waarde die automatisch is ingevuld.|

    ![Schermopname toont 'HDInsight-cluster maken' waarbij het tabblad 'Opslag' is geselecteerd.](./media/apache-spark-jupyter-spark-sql-use-portal/azure-portal-cluster-storage.png "Een Spark-cluster maken in HDInsight met de basisconfiguraties")

    Selecteer **Beoordelen en maken** om verder te gaan.

1. Selecteer binnen **Beoordelen en maken** de optie **Maken**. Het duurt ongeveer 20 minuten om het cluster te maken. Het cluster moet zijn gemaakt voordat u verder kunt gaan met de volgende sessie.

Als u een probleem ondervindt met het maken van HDInsight-clusters, beschikt u mogelijk niet over de juiste machtigingen om dit te doen. Zie [Vereisten voor toegangsbeheer](../hdinsight-hadoop-customize-cluster-linux.md#access-control) voor meer informatie.

## <a name="create-a-jupyter-notebook"></a>Een Jupyter Notebook maken

Jupyter Notebook is een interactieve notitieblokomgeving die ondersteuning biedt voor verschillende programmeertalen. Via het notitieblok kunt u interactie hebben met uw gegevens, code combineren met markdown-tekst en eenvoudige visualisaties uitvoeren.

1. Navigeer in een webbrowser naar `https://CLUSTERNAME.azurehdinsight.net/jupyter`, waarbij `CLUSTERNAME` de naam van uw cluster is. Voer de aanmeldingsreferenties voor het cluster in als u daarom wordt gevraagd.

1. Selecteer **Nieuw** > **PySpark** om een notebook te maken.

   ![Een Jupyter Notebook maken om de interactieve Spark SQL-query uit te voeren](./media/apache-spark-jupyter-spark-sql-use-portal/hdinsight-spark-create-jupyter-interactive-spark-sql-query.png "Een Jupyter Notebook maken om de interactieve Spark SQL-query uit te voeren")

   Er wordt een nieuwe notebook gemaakt en geopend met de naam Untitled (Untitled.pynb).

## <a name="run-apache-spark-sql-statements"></a>Apache Spark SQL-instructies uitvoeren

SQL (Structured Query Language) is de meest voorkomende en gebruikte taal voor het uitvoeren van query's en het definiëren van gegevens. Spark SQL fungeert als een uitbreiding van Apache Spark voor het verwerken van gestructureerde gegevens, met behulp van de bekende SQL-syntaxis.

1. Controleer of de kernel gereed is. Wanneer u een lege cirkel naast de naam van de kernel in de notebook ziet, is de kernel gereed. Gevulde cirkel geeft aan dat de kernel bezet is.

    ![Schermopname toont een Jupyter-venster met een PySpark-indicator.](./media/apache-spark-jupyter-spark-sql/jupyter-spark-kernel-status.png "Hive-query in HDInsight")

    Wanneer u de notebook voor het eerst start, voert de kernel enkele taken in de achtergrond uit. Wacht tot de kernel gereed is.

1. Plak de volgende code in een lege cel en druk op **Shift+Enter** om de code uit te voeren. Met de opdracht worden de Hive-tabellen in het cluster weergegeven:

    ```PySpark
    %%sql
    SHOW TABLES
    ```

    Als u een Jupyter Notebook gebruikt met uw HDInsight-cluster, krijgt u een vooraf ingestelde `sqlContext` waarmee u Hive-query's kunt uitvoeren met behulp van Spark SQL. `%%sql` instrueert Jupyter Notebook gebruik te maken van de vooraf ingestelde `sqlContext` om de Hive-query uit te voeren. De query haalt de bovenste tien rijen op uit een Hive-tabel (**hivesampletable**) die standaard worden meegeleverd met alle HDInsight-clusters. Het duurt ongeveer 30 seconden om de resultaten op te halen. De uitvoer ziet er als volgt uit:

    ![Schermopname toont een Jupyter-venster voor de Noteboook die u in deze quickstart hebt gemaakt.](./media/apache-spark-jupyter-spark-sql-use-portal/hdinsight-spark-get-started-hive-query.png "Hive-query in HDInsight")

    Telkens wanneer u in Jupyter een query uitvoert, toont de venstertitel van uw webbrowser de status **(Bezet)** en de notebooktitel. Ook ziet u een gevulde cirkel naast de **PySpark**-tekst in de rechterbovenhoek.

1. Voer een andere query uit om de gegevens in `hivesampletable` te zien.

    ```PySpark
    %%sql
    SELECT * FROM hivesampletable LIMIT 10
    ```

    Het scherm wordt vernieuwd om de query-uitvoer weer te geven.

    ![Uitvoer van Hive-query in HDInsight](./media/apache-spark-jupyter-spark-sql-use-portal/hdinsight-spark-get-started-hive-query-output.png "Uitvoer van Hive-query in HDInsight")

1. Klik in het menu **File** van het notebook op **Close and Halt**. Als de notebook wordt afgesloten, komen de clusterbronnen vrij.

## <a name="clean-up-resources"></a>Resources opschonen

Met HDInsight worden uw gegevens opgeslagen in Azure Storage of Azure Data Lake Storage, zodat u een cluster veilig kunt verwijderen wanneer dit niet wordt gebruikt. Voor een HDInsight-cluster worden ook kosten in rekening gebracht, zelfs wanneer het niet wordt gebruikt. Aangezien de kosten voor het cluster vaak zoveel hoger zijn dan de kosten voor opslag, is het financieel gezien logischer clusters te verwijderen wanneer ze niet worden gebruikt. Als u direct verder wilt met de zelfstudie die wordt vermeld bij [Volgende stappen](#next-steps), is het beter om het cluster te behouden.

Ga terug naar Azure Portal en selecteer **Verwijderen**.

![De Azure-portal verwijdert een HDInsight-cluster](./media/apache-spark-jupyter-spark-sql-use-portal/hdinsight-azure-portal-delete-cluster.png "HDInsight-cluster verwijderen")

U kunt ook de naam van de resourcegroep selecteren om de pagina van de resourcegroep te openen en vervolgens **Resourcegroep verwijderen** selecteren. Als u de resourcegroep verwijdert, verwijdert u zowel het HDInsight-cluster als het standaardopslagaccount.

## <a name="next-steps"></a>Volgende stappen

In deze snelstart hebt u geleerd hoe u een Apache Spark-cluster in HDInsight maakt en een eenvoudige Spark SQL-query uitvoert. Ga naar de volgende zelfstudie voor informatie over het gebruik van een HDInsight-cluster om interactieve query's uit te voeren op voorbeeldgegevens.

> [!div class="nextstepaction"]
> [Interactieve query's uitvoeren in Apache Spark](./apache-spark-load-data-run-query.md)