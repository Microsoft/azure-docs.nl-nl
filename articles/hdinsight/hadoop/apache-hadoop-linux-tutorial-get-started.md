---
title: 'Quickstart: Een Apache Hadoop-cluster maken in Azure HDInsight met Resource Manager-sjabloon'
description: In deze quickstart maakt u een Apache Hadoop-cluster in Azure HDInsight met Resource Manager-sjabloon
ms.service: hdinsight
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 03/13/2020
ms.openlocfilehash: fe1a76406c1a8a55996b4c099e04f0925daaa74c
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98946532"
---
# <a name="quickstart-create-apache-hadoop-cluster-in-azure-hdinsight-using-arm-template"></a>Quickstart: Een Apache Hadoop-cluster maken in Azure HDInsight met een ARM-sjabloon

In deze quickstart gebruikt u een Azure Resource Manager-sjabloon (ARM-sjabloon) om een [Apache Hadoop](./apache-hadoop-introduction.md)-cluster te maken in Azure HDInsight. Hadoop was het originele open-source framework voor gedistribueerde verwerking en analyse van sets met big data in clusters. Het Hadoop-ecosysteem omvat verwante software en hulpprogramma's, waaronder Apache Hive, Apache HBase, Spark en Kafka.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]
  
Op dit moment wordt HDInsight geleverd met [zeven verschillende clustertypen](../hdinsight-overview.md#cluster-types-in-hdinsight). Elk clustertype ondersteunt een andere set onderdelen. Alle clustertypen ondersteunen Hive. Zie voor een lijst van ondersteunde onderdelen in HDInsight [Wat is er nieuw in de Hadoop-clusterversies geleverd door HDInsight?](../hdinsight-component-versioning.md)  

Als uw omgeving voldoet aan de vereisten en u benkend bent met het gebruik van ARM-sjablonen, selecteert u de knop **Implementeren naar Azure**. De sjabloon wordt in Azure Portal geopend.

[![Implementeren in Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-ssh-password%2Fazuredeploy.json)

## <a name="prerequisites"></a>Vereisten

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="review-the-template"></a>De sjabloon controleren

De sjabloon die in deze quickstart wordt gebruikt, komt uit [Azure-quickstart-sjablonen](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-ssh-password/).

:::code language="json" source="~/quickstart-templates/101-hdinsight-linux-ssh-password/azuredeploy.json":::

Er worden twee Azure-resources gedefinieerd in de sjabloon:

* [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts): een Azure Storage-account maken.
* [Microsoft.HDInsight/cluster](/azure/templates/microsoft.hdinsight/clusters): een HDInsight-cluster maken.

## <a name="deploy-the-template"></a>De sjabloon implementeren

1. Selecteer de knop **Implementeren in Azure** onderaan om u aan te melden bij Azure en de ARM-sjabloon te openen.

    [![Implementeren in Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-ssh-password%2Fazuredeploy.json)

1. Typ of selecteer de volgende waarden:

    |Eigenschap  |Beschrijving  |
    |---------|---------|
    |Abonnement|Selecteer in de vervolgkeuzelijst het Azure-abonnement dat wordt gebruikt voor het cluster.|
    |Resourcegroep|Selecteer in de vervolgkeuzelijst de bestaande resourcegroep of selecteer **Nieuwe maken**.|
    |Locatie|De waarde wordt automatisch ingevuld met de locatie die wordt gebruikt voor de resourcegroep.|
    |Clusternaam|Geef een wereldwijd unieke naam op. Gebruik voor deze sjabloon alleen kleine letters en cijfers.|
    |Clustertype | Selecteer **hadoop**. |
    |Gebruikersnaam voor clusteraanmelding|Geef de gebruikersnaam op; de standaard is **beheerder**.|
    |Wachtwoord voor clusteraanmelding|Geef een wachtwoord op. Het wachtwoord moet uit minstens tien tekens bestaan en moet minstens één cijfer, één hoofdletter, één kleine letter en één niet-alfanumeriek teken bevatten (uitgezonderd ' " ` ). |
    |Ssh-gebruikersnaam|Geef de gebruikersnaam op; de standaardwaarde is **sshuser**|
    |Ssh-wachtwoord|Geef het wachtwoord op.|

    Sommige eigenschappen zijn vastgelegd in de sjabloon.  U kunt deze waarden uit de sjabloon configureren. Raadpleeg voor meer uitleg over deze eigenschappen [Apache Hadoop-clusters maken in HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

    > [!NOTE]  
    > De opgegeven waarden moeten uniek zijn en moeten voldoen aan deze richtlijnen voor naamgeving. De sjabloon voert geen validatiecontroles uit. Als de waarden die u opgeeft, al in gebruik zijn of niet aan de richtlijnen voldoen, krijgt u een foutmelding nadat u de sjabloon hebt verzonden.  

    ![Aan de slag met HDInsight op basis van Linux en Resource Manager-sjabloon via de portal](./media/apache-hadoop-linux-tutorial-get-started/hdinsight-linux-get-started-arm-template-on-portal.png "Hadoop-cluster in HDInsight implementeren met behulp van de Azure-portal en een resourcegroepsbeheerdersjabloon")

1. Bekijk de **VOORWAARDEN**. Selecteer vervolgens **Ik ga akkoord met de bovenstaande voorwaarden** en daarna **Kopen**. U ontvangt een melding dat uw implementatie wordt uitgevoerd. Het duurt ongeveer 20 minuten om een cluster te maken.

## <a name="review-deployed-resources"></a>Geïmplementeerde resources bekijken

Zodra het cluster is gemaakt, ontvangt u de melding **Implementatie voltooid** met de koppeling **Naar de resource**. Op de pagina Resourcegroep worden uw nieuwe HDInsight-cluster en de standaardopslag bij het cluster weergegeven. Elk cluster is afhankelijk van een [Azure Blob Storage](../hdinsight-hadoop-use-blob-storage.md)-account, van [Azure Data Lake Storage Gen1](../hdinsight-hadoop-use-data-lake-storage-gen1.md) of van [`Azure Data Lake Storage Gen2`](../hdinsight-hadoop-use-data-lake-storage-gen2.md). Dit wordt het standaardopslagaccount genoemd. Het HDInsight-cluster en het standaardopslagaccount moeten samen in dezelfde Azure-regio worden geplaatst. Het opslagaccount wordt niet verwijderd wanneer er clusters worden verwijderd.

> [!NOTE]  
> Zie [HDInsight-clusters maken](../hdinsight-hadoop-provision-linux-clusters.md) voor andere methoden om clusters te maken en inzicht te krijgen in de eigenschappen die worden gebruikt in deze quickstart.

## <a name="clean-up-resources"></a>Resources opschonen

Nadat u de quickstart hebt voltooid, kunt u het cluster verwijderen. Met HDInsight worden uw gegevens opgeslagen in Azure Storage zodat u een cluster veilig kunt verwijderen wanneer deze niet wordt gebruikt. Voor een HDInsight-cluster worden ook kosten in rekening gebracht, zelfs wanneer het niet wordt gebruikt. Aangezien de kosten voor het cluster vaak zoveel hoger zijn dan de kosten voor opslag, is het financieel gezien logischer clusters te verwijderen wanneer ze niet worden gebruikt.

> [!NOTE]  
> Als u *meteen* verder wilt gaan met de volgende zelfstudie om te leren hoe u ETL-bewerkingen uitvoert met behulp van Hadoop in HDInsight, kunt u het cluster beter behouden. In die zelfstudie hebt u namelijk ook een Hadoop-cluster nodig. Als u echter niet direct verdergaat met de volgende zelfstudie, moet u het cluster nu verwijderen.

Ga in de Azure-portal naar het cluster en selecteer **Verwijderen**.

![HDInsight-cluster verwijderen uit portal](./media/apache-hadoop-linux-tutorial-get-started/hdinsight-delete-cluster.png "HDInsight-cluster verwijderen uit portal")

U kunt ook de naam van de resourcegroep selecteren om de pagina van de resourcegroep te openen en vervolgens **Resourcegroep verwijderen** selecteren. Als u de resourcegroep verwijdert, verwijdert u zowel het HDInsight-cluster als het standaardopslagaccount.

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u geleerd hoe u een Apache Hadoop-cluster in HDInsight maakt met behulp van een ARM-sjabloon. In het volgende artikel leert u hoe u een ETL-bewerking (Extraction, Transformation, Loading) uitvoert met behulp van Hadoop in HDInsight.

> [!div class="nextstepaction"]
> [Gegevens extraheren, transformeren en laden met Interactive Query in HDInsight](../interactive-query/interactive-query-tutorial-analyze-flight-data.md)
