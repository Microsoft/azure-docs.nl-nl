---
title: Azure HDInsight-Azure Data Lake Storage Gen2 maken-Azure CLI
description: Meer informatie over het gebruik van Azure Data Lake Storage Gen2 met Azure HDInsight-clusters met behulp van Azure CLI.
author: guyhay
ms.author: guyhay
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020, devx-track-azurecli
ms.date: 09/17/2020
ms.openlocfilehash: 4ffe5acce831435d3771a2eb48a90b16f584c92a
ms.sourcegitcommit: e7152996ee917505c7aba707d214b2b520348302
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/20/2020
ms.locfileid: "97704007"
---
# <a name="create-a-cluster-with-data-lake-storage-gen2-using-azure-cli"></a>Een cluster met Data Lake Storage Gen2 maken met behulp van Azure CLI

Voer de volgende stappen uit om een HDInsight-cluster te maken dat gebruikmaakt van Data Lake Storage Gen2 voor opslag.

## <a name="prerequisites"></a>Vereisten

- Als u niet bekend bent met Azure Data Lake Storage Gen2, raadpleegt u de [sectie Overzicht](hdinsight-hadoop-use-data-lake-storage-gen2.md). 
- Als u nog geen Azure-account hebt, [registreer u dan voor een gratis account](https://azure.microsoft.com/free/) voordat u verdergaat.
- Als u de CLI-scriptvoorbeelden wilt uitvoeren, hebt u drie opties:
    - Gebruik [Azure Cloud Shell](../cloud-shell/overview.md) vanaf Azure Portal (zie volgende sectie).
    - Gebruik de ingesloten Azure Cloud Shell via de knop 'Nu proberen' in de rechterbovenhoek van elk codeblok.
    - [Installeer de nieuwste versie van de Azure CLI](/cli/azure/install-azure-cli) (2.0.13 of later) als u liever een lokale CLI-console gebruikt. Meld u aan bij Azure met met `az login` behulp van een account dat is gekoppeld aan het Azure-abonnement waaronder u de door de gebruiker toegewezen beheerde identiteit wilt implementeren. Azure CLI. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

U kunt [een voorbeeld sjabloon bestand downloaden](https://github.com/Azure-Samples/hdinsight-data-lake-storage-gen2-templates/blob/master/hdinsight-adls-gen2-template.json) en [een voor beeld-parameter bestand downloaden](https://github.com/Azure-Samples/hdinsight-data-lake-storage-gen2-templates/blob/master/parameters.json). Voordat u de sjabloon en het Azure CLI-code fragment hieronder gebruikt, vervangt u de volgende tijdelijke aanduidingen door de juiste waarden:

| Tijdelijke aanduiding | Beschrijving |
|---|---|
| `<SUBSCRIPTION_ID>` | De ID van uw Azure-abonnement |
| `<RESOURCEGROUPNAME>` | De resource groep waar u het nieuwe cluster en opslag account wilt maken. |
| `<MANAGEDIDENTITYNAME>` | De naam van de beheerde identiteit die machtigingen krijgt voor uw opslag account met Azure Data Lake Storage Gen2. |
| `<STORAGEACCOUNTNAME>` | Het nieuwe opslag account met Azure Data Lake Storage Gen2 dat wordt gemaakt. |
| `<FILESYSTEMNAME>`  | De naam van het bestands systeem dat dit cluster moet gebruiken in het opslag account. |
| `<CLUSTERNAME>` | De naam van uw HDInsight-cluster. |
| `<PASSWORD>` | Het wacht woord dat u hebt gekozen voor het aanmelden bij het cluster met behulp van SSH en het Ambari-dash board. |

In het onderstaande code fragment worden de volgende eerste stappen uitgevoerd:

1. Meld u aan bij uw Azure-account.
1. Hiermee stelt u het actieve abonnement in waarin de bewerkingen worden gemaakt.
1. Hiermee maakt u een nieuwe resource groep voor de nieuwe implementatie activiteiten.
1. Hiermee maakt u een door de gebruiker toegewezen beheerde identiteit.
1. Hiermee wordt een extensie toegevoegd aan de Azure CLI om functies voor Data Lake Storage Gen2 te gebruiken.
1. Hiermee maakt u een nieuw opslag account met Data Lake Storage Gen2 met behulp van de `--hierarchical-namespace true` vlag.

```azurecli
az login
az account set --subscription <SUBSCRIPTION_ID>

# Create resource group
az group create --name <RESOURCEGROUPNAME> --location eastus

# Create managed identity
az identity create -g <RESOURCEGROUPNAME> -n <MANAGEDIDENTITYNAME>

az extension add --name storage-preview

az storage account create --name <STORAGEACCOUNTNAME> \
    --resource-group <RESOURCEGROUPNAME> \
    --location eastus --sku Standard_LRS \
    --kind StorageV2 --hierarchical-namespace true
```

Meld u vervolgens aan bij de portal. Voeg de nieuwe door de gebruiker toegewezen beheerde identiteit toe aan de rol van **Inzender voor blobgegevens** op het opslag account. Deze stap wordt beschreven in stap 3 onder [het gebruik van de Azure Portal](hdinsight-hadoop-use-data-lake-storage-gen2.md).

 > [!IMPORTANT]
 > Zorg ervoor dat uw opslag account de door de gebruiker toegewezen identiteit met de machtigingen van de rol **Storage BLOB data Inzender** heeft, anders kan het maken van het cluster mislukken.

```azurecli
az deployment group create --name HDInsightADLSGen2Deployment \
    --resource-group <RESOURCEGROUPNAME> \
    --template-file hdinsight-adls-gen2-template.json \
    --parameters parameters.json
```

## <a name="clean-up-resources"></a>Resources opschonen

Nadat u het artikel hebt voltooid, kunt u het cluster verwijderen. Met HDInsight worden uw gegevens opgeslagen in Azure Storage zodat u een cluster veilig kunt verwijderen wanneer deze niet wordt gebruikt. Voor een HDInsight-cluster worden ook kosten in rekening gebracht, zelfs wanneer het niet wordt gebruikt. Aangezien de kosten voor het cluster vaak zoveel hoger zijn dan de kosten voor opslag, is het financieel gezien logischer clusters te verwijderen wanneer ze niet worden gebruikt.

Voer alle of enkele van de volgende opdrachten in om resources te verwijderen:

```azurecli-interactive
# Remove cluster
az hdinsight delete \
    --name $clusterName \
    --resource-group $resourceGroupName

# Remove storage container
az storage container delete \
    --account-name $AZURE_STORAGE_ACCOUNT \
    --name $AZURE_STORAGE_CONTAINER

# Remove storage account
az storage account delete \
    --name $AZURE_STORAGE_ACCOUNT \
    --resource-group $resourceGroupName

# Remove resource group
az group delete \
    --name $resourceGroupName
```

## <a name="troubleshoot"></a>Problemen oplossen

Zie [Vereisten voor toegangsbeheer](./hdinsight-hadoop-customize-cluster-linux.md#access-control) als u problemen ondervindt met het maken van HDInsight-clusters.

## <a name="next-steps"></a>Volgende stappen

U hebt een HDInsight-cluster gemaakt. Meer informatie over het werken met uw cluster.

### <a name="apache-spark-clusters"></a>Apache Spark clusters

* [Op Linux gebaseerde HDInsight-clusters aanpassen met behulp van script acties](hdinsight-hadoop-customize-cluster-linux.md)
* [Een zelfstandige toepassing maken met behulp van Scala](spark/apache-spark-create-standalone-application.md)
* [Apache Livy gebruiken om taken op afstand uit te voeren in een Apache Spark-cluster](spark/apache-spark-livy-rest-interface.md)
* [Apache Spark met BI: interactieve gegevens analyses uitvoeren met behulp van Spark in HDInsight met BI-hulpprogram ma's](spark/apache-spark-use-bi-tools.md)
* [Apache Spark met Machine Learning: Spark in HDInsight gebruiken om voedsel inspectie resultaten te voors pellen](spark/apache-spark-machine-learning-mllib-ipython.md)

### <a name="apache-hadoop-clusters"></a>Apache Hadoop clusters

* [Apache Hive gebruiken met HDInsight](hadoop/hdinsight-use-hive.md)
* [MapReduce gebruiken met HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Apache HBase-clusters

* [Aan de slag met Apache HBase in HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Ontwikkel Java-toepassingen voor Apache HBase in HDInsight](hbase/apache-hbase-build-java-maven-linux.md)
