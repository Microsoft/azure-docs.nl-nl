---
title: 'Zelfstudie: Een end-to-end ETL-pijplijn maken om verkoopinzichten in Azure HDInsight af te leiden'
description: Meer informatie over het gebruik van ETL-pijplijnen maken met Azure HDInsight voor het afleiden van inzichten op basis van verkoopgegevens met behulp van on-demand clusters van Spark en Power BI.
ms.service: hdinsight
ms.topic: tutorial
ms.custom: hdinsightactive, devx-track-azurecli
ms.date: 04/15/2020
ms.openlocfilehash: 7d026d93ebe6f6a0bd9afbd277294d58d7a2a69b
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98932040"
---
# <a name="tutorial-create-an-end-to-end-data-pipeline-to-derive-sales-insights-in-azure-hdinsight"></a>Zelfstudie: Een end-to-end gegevenspijplijn maken om verkoopinzichten in Azure HDInsight af te leiden

In deze zelfstudie bouwt u een end-to-end gegevenspijplijn waarmee de ETL-bewerkingen uitpakken, transformeren en laden worden uitgevoerd. De pijplijn maakt gebruik van [Apache Spark](./spark/apache-spark-overview.md) en Apache Hive-clusters die worden uitgevoerd op Azure HDInsight voor het uitvoeren van query's en het bewerken van de gegevens. U kunt ook gebruikmaken van technologieën zoals Azure Data Lake Storage Gen2 voor gegevensopslag en Power BI voor visualisatie.

In deze gegevenspijplijn worden de gegevens uit verschillende winkels gecombineerd, worden alle ongewenste gegevens verwijderd, worden nieuwe gegevens toegevoegd en wordt deze weer in uw opslag geladen om zakelijke inzichten te visualiseren. Lees meer over ETL-pijplijnen in [Uitpakken, transformeren en laden (ETL)](./hadoop/apache-hadoop-etl-at-scale.md).

![ETL-architectuur](./media/hdinsight-sales-insights-etl/architecture.png)

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

* Azure CLI - ten minste versie 2.2.0. Raadpleeg [De Azure CLI installeren](/cli/azure/install-azure-cli).

* jq, een opdrachtregel-JSON-processor.  Zie [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/).

* Een lid van de [Ingebouwde Azure-rol - eigenaar](../role-based-access-control/built-in-roles.md).

* Als u PowerShell gebruikt om de Data Factory-pijplijn te activeren, hebt u de [AZ-module](/powershell/azure/) nodig.

* [Power BI Desktop](https://aka.ms/pbiSingleInstaller) om zakelijke inzichten te visualiseren aan het einde van deze zelfstudie.

## <a name="create-resources"></a>Resources maken

### <a name="clone-the-repository-with-scripts-and-data"></a>De opslagplaats klonen met scripts en gegevens

1. Aanmelden bij uw Azure-abonnement. Als u Azure Cloud Shell wilt gebruiken, selecteert u **Probeer het** in de rechterbovenhoek van het codeblok. Anders voert u de onderstaande opdracht in:

    ```azurecli-interactive
    az login

    # If you have multiple subscriptions, set the one to use
    # az account set --subscription "SUBSCRIPTIONID"
    ```

1. Zorg ervoor dat u lid bent van de Azure-rol [Eigenaar](../role-based-access-control/built-in-roles.md). Vervang `user@contoso.com` door uw account en voer de volgende opdracht in:

    ```azurecli
    az role assignment list \
    --assignee "user@contoso.com" \
    --role "Owner"
    ```

    Als er geen record wordt geretourneerd, bent u geen lid en kunt u deze zelfstudie niet voltooien.

1. Download de gegevens en scripts voor deze zelfstudie vanuit de [ETL-opslagplaats voor verkoopinzichten van HDInsight](https://github.com/Azure-Samples/hdinsight-sales-insights-etl). Voer de volgende opdracht in:

    ```bash
    git clone https://github.com/Azure-Samples/hdinsight-sales-insights-etl.git
    cd hdinsight-sales-insights-etl
    ```

1. Zorg dat `salesdata scripts templates` is gemaakt. Controleer met de volgende opdracht:

   ```bash
   ls
   ```

### <a name="deploy-azure-resources-needed-for-the-pipeline"></a>Implementeer Azure-resources die nodig zijn voor de pijplijn

1. Voer de machtigingen voor uitvoeren voor alle scripts uit door het volgende in te voeren:

    ```bash
    chmod +x scripts/*.sh
    ````

1. Stel de variabele in voor de resourcegroep. Vervang `RESOURCE_GROUP_NAME` door de naam van een bestaande of nieuwe resourcegroep en voer de volgende opdracht in:

    ```bash
    resourceGroup="RESOURCE_GROUP_NAME"
    ```

1. Voer het script uit. Vervang `LOCATION` door een gewenste waarde en voer de volgende opdracht in:

    ```bash
    ./scripts/resources.sh $resourceGroup LOCATION
    ```

    Als u niet zeker weet welke regio u moet opgeven, kunt u een lijst met ondersteunde regio's voor uw abonnement ophalen met de opdracht [az account list-locations](/cli/azure/account#az-account-list-locations).

    Met deze opdracht worden de volgende resources geïmplementeerd:

    * Een Azure Blob Storage-account. Dit account bevat de verkoopgegevens van het bedrijf.
    * Een Azure Data Lake Storage Gen2-account. Dit account wordt gebruikt als het opslagaccount voor beide HDInsight-clusters. Meer informatie over HDInsight en Data Lake Storage Gen2 vindt u in [Azure HDInsight-integratie met Data Lake Storage Gen2](https://azure.microsoft.com/blog/azure-hdinsight-integration-with-data-lake-storage-gen-2-preview-acl-and-security-update/).
    * Een aan een gebruiker toegewezen beheerde identiteit. Dit account geeft de HDInsight-clusters toegang tot het Data Lake Storage Gen2-account.
    * Een Apache Spark-cluster. Dit cluster wordt gebruikt om de onbewerkte gegevens op te schonen en te transformeren.
    * Een [Interactive Query-cluster](./interactive-query/apache-interactive-query-get-started.md) in Apache Hive. Met dit cluster kunt u query's uitvoeren op de verkoopgegevens en deze visualiseren met Power BI.
    * Een virtueel Azure-netwerk dat wordt ondersteund door netwerkbeveiligingsgroep (NSG)-regels. Met dit virtuele netwerk kunnen clusters communiceren en hun communicatie beveiligen.

Het maken van een cluster kan ongeveer 20 minuten duren.

Het standaardwachtwoord voor SSH-toegang tot de clusters is `Thisisapassword1`. Als u het wachtwoord wilt wijzigen, gaat u naar het bestand `./templates/resourcesparameters_remainder.json` en wijzigt u het wachtwoord voor de parameters `sparksshPassword`, `sparkClusterLoginPassword`, `llapClusterLoginPassword` en `llapsshPassword`.

### <a name="verify-deployment-and-collect-resource-information"></a>Implementatie controleren en resourcegegevens verzamelen

1. Als u de status van uw implementatie wilt controleren, gaat u naar de resourcegroep op de Azure-portal. Selecteer onder **Instellingen** **Implementaties** en vervolgens uw implementatie. Hier ziet u de resources die zijn geïmplementeerd en de resources die nog worden uitgevoerd.

1. Voer de volgende opdracht in om de namen van de clusters weer te geven:

    ```bash
    sparkClusterName=$(cat resourcesoutputs_remainder.json | jq -r '.properties.outputs.sparkClusterName.value')
    llapClusterName=$(cat resourcesoutputs_remainder.json | jq -r '.properties.outputs.llapClusterName.value')

    echo "Spark Cluster" $sparkClusterName
    echo "LLAP cluster" $llapClusterName
    ```

1. Als u het Azure-opslagaccount en de toegangssleutel wilt weergeven, voert u de volgende opdracht in:

    ```azurecli
    blobStorageName=$(cat resourcesoutputs_storage.json | jq -r '.properties.outputs.blobStorageName.value')

    blobKey=$(az storage account keys list \
        --account-name $blobStorageName \
        --resource-group $resourceGroup \
        --query [0].value -o tsv)

    echo $blobStorageName
    echo $blobKey
    ```

1. Als u het Data Lake Storage Gen2-account en de toegangssleutel wilt weergeven, voert u de volgende opdracht in:

    ```azurecli
    ADLSGen2StorageName=$(cat resourcesoutputs_storage.json | jq -r '.properties.outputs.adlsGen2StorageName.value')

    adlsKey=$(az storage account keys list \
        --account-name $ADLSGen2StorageName \
        --resource-group $resourceGroup \
        --query [0].value -o tsv)

    echo $ADLSGen2StorageName
    echo $adlsKey
    ```

### <a name="create-a-data-factory"></a>Een gegevensfactory maken

Azure Data Factory is een hulpprogramma waarmee Azure-pijplijnen kunnen worden geautomatiseerd. Het is niet de enige manier om deze taken uit te voeren, maar het is een uitstekende manier om de processen te automatiseren. Raadpleeg de [Documentatie voor Azure Data Factory](https://azure.microsoft.com/services/data-factory/) voor meer informatie over Azure Data Factory.

Deze data factory heeft één pijplijn met twee activiteiten:

* Met de eerste activiteit worden de gegevens van Azure Blob-opslag gekopieerd naar het Data Lake Storage Gen 2-opslagaccount om gegevensopname te simuleren.
* Met de tweede activiteit worden de gegevens in het Spark-cluster getransformeerd. Met het script worden de gegevens getransformeerd door ongewenste kolommen te verwijderen. Er wordt ook een nieuwe kolom toegevoegd die de omzet berekent die door één transactie wordt gegenereerd.

Als u uw Azure Data Factory-pijplijn wilt instellen, voert u de onderstaande opdracht uit.  U moet nog steeds de map `hdinsight-sales-insights-etl` zijn.

```bash
blobStorageName=$(cat resourcesoutputs_storage.json | jq -r '.properties.outputs.blobStorageName.value')
ADLSGen2StorageName=$(cat resourcesoutputs_storage.json | jq -r '.properties.outputs.adlsGen2StorageName.value')

./scripts/adf.sh $resourceGroup $ADLSGen2StorageName $blobStorageName
```

Dit script doet het volgende dingen:

1. Hiermee maakt u een service-principal met machtigingen `Storage Blob Data Contributor` voor het Data Lake Storage Gen2-opslagaccount.
1. Hiermee wordt een verificatietoken opgehaald om POST-aanvragen te autoriseren naar het [Data Lake Storage Gen2-bestandssysteem REST API](https://docs.microsoft.com/rest/api/storageservices/datalakestoragegen2/filesystem/create).
1. Hiermee wordt de werkelijke naam van uw Data Lake Storage Gen2-opslagaccount in de bestanden `sparktransform.py` en `query.hql` ingevuld.
1. Hiermee worden opslagsleutels voor de Data Lake Storage Gen2- en Blob Storage-accounts opgehaald.
1. Hiermee maakt u een nieuwe resource-implementatie om een Azure Data Factory-pijplijn te maken, met gekoppelde services en activiteiten. De opslagsleutels worden doorgegeven als parameters aan het sjabloonbestand, zodat de gekoppelde services op de juiste wijze toegang hebben tot de opslagaccounts.

## <a name="run-the-data-pipeline"></a>De gegevens pijplijnuitvoeren

### <a name="trigger-the-data-factory-activities"></a>De Data Factory-activiteiten activeren

Met de eerste activiteit in de Data Factory-pijplijn die u hebt gemaakt, worden de gegevens uit de Blob-opslag naar Data Lake Storage Gen2 verplaatst. Met de tweede activiteit worden de Spark-transformaties op de gegevens toegepast en worden de getransformeerde CSV-bestanden op een nieuwe locatie opgeslagen. Het uitvoeren van de volledige pijplijn kan enkele minuten duren.

Voer de volgende opdracht in om de Data Factory-naam op te halen:

```azurecli
cat resourcesoutputs_adf.json | jq -r '.properties.outputs.factoryName.value'
```

Als u de pijplijn wilt activeren, kunt u het volgende doen:

* Activeer de Data Factory-pijplijn in PowerShell. Vervang `RESOURCEGROUP` en `DataFactoryName` met de juiste waarden en voer vervolgens de volgende opdrachten uit:

    ```powershell
    # If you have multiple subscriptions, set the one to use
    # Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"

    $resourceGroup="RESOURCEGROUP"
    $dataFactory="DataFactoryName"

    $pipeline =Invoke-AzDataFactoryV2Pipeline `
        -ResourceGroupName $resourceGroup `
        -DataFactory $dataFactory `
        -PipelineName "IngestAndTransform"

    Get-AzDataFactoryV2PipelineRun `
        -ResourceGroupName $resourceGroup  `
        -DataFactoryName $dataFactory `
        -PipelineRunId $pipeline
    ```

    Voer `Get-AzDataFactoryV2PipelineRun` opnieuw uit indien nodig om de voortgang te controleren.

    of

* Open de data factory en selecteer **Auteur & controle**. Activeer de pijplijn `IngestAndTransform` vanuit de portal. Zie [Apache Hadoop-clusters op aanvraag maken in HDInsight met behulp van Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md#trigger-a-pipeline) voor meer informatie over het activeren van pijplijnen via de portal.

Om te controleren of de pijplijn is uitgevoerd, kunt u een van de volgende stappen uitvoeren:

* Ga naar de sectie **Controle** in uw data factory via de portal.
* Ga in Azure Storage Explorer naar uw Data Lake Storage Gen 2-opslagaccount. Ga naar het bestandssysteem `files`, ga naar de map `transformed` en controleer de inhoud om te zien of de pijplijn is geslaagd.

Zie [dit artikel over het gebruik van Jupyter Notebook](/azure/hdinsight/spark/apache-spark-load-data-run-query) voor andere manieren om gegevens te transformeren met behulp van HDInsight.

### <a name="create-a-table-on-the-interactive-query-cluster-to-view-data-on-power-bi"></a>Een tabel op het Interactive Query-cluster maken om gegevens op Power BI weer te geven

1. Kopieer het bestand `query.hql` naar het LLAP-cluster met behulp van SCP. Voer de opdracht in:

    ```bash
    llapClusterName=$(cat resourcesoutputs_remainder.json | jq -r '.properties.outputs.llapClusterName.value')
    scp scripts/query.hql sshuser@$llapClusterName-ssh.azurehdinsight.net:/home/sshuser/
    ```

    Herinnering: Het standaardwachtwoord is `Thisisapassword1`.

1. Gebruik SSH om toegang te krijgen tot het LLAP-cluster. Voer de opdracht in:

    ```bash
    ssh sshuser@$llapClusterName-ssh.azurehdinsight.net
    ```

1. Gebruik de volgende opdracht om het script uit te voeren:

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -f query.hql
    ```

    Met dit script wordt een beheerde tabel gemaakt op het interactieve querycluster waartoe u toegang hebt via Power BI.

### <a name="create-a-power-bi-dashboard-from-sales-data"></a>Een Power BI-dashboard maken op basis van verkoopgegevens

1. Open Power BI Desktop.

1. Ga in het menu naar **Gegevens ophalen** > **Meer...**  > **Azure** > **HDInsight Interactive Query**.

1. Selecteer **Verbinden**.

1. In het dialoogvenster **HDInsight Interactive Query**:
    1. Geef in het tekstvak **Server** de naam van uw LLAP-cluster op in de indeling van `https://LLAPCLUSTERNAME.azurehdinsight.net`.
    1. Voer in het tekstvak **database** `default` in.
    1. Selecteer **OK**.

1. In het dialoogvenster **AzureHive**:
    1. Voer in het tekstvak **Gebruikersnaam** `admin` in.
    1. Voer in het tekstvak **Wachtwoord** `Thisisapassword1`in.
    1. Selecteer **Verbinden**.

1. Selecteer in **Navigator** `sales`en/of `sales_raw` om een voorbeeld van de gegevens weer te geven. Nadat de gegevens zijn geladen, kunt u experimenteren met het dashboard dat u wilt maken. Zie de volgende koppelingen om aan de slag te gaan met Power BI-dashboards:

* [Inleiding tot dashboards voor Power BI-ontwerpers](https://docs.microsoft.com/power-bi/service-dashboards)
* [Zelfstudie: aan de slag met de Power BI-service](https://docs.microsoft.com/power-bi/service-get-started)

## <a name="clean-up-resources"></a>Resources opschonen

Als u deze toepassing niet meer wilt gebruiken, verwijdert u alle resources met behulp van de volgende opdracht, zodat er geen kosten in rekening worden gebracht.

1. Om de resourcegroep te verwijderen, voert u de volgende opdracht uit:

    ```azurecli
    az group delete -n $resourceGroup
    ```

1. Als u de service-principal wilt verwijderen, voert u de volgende opdrachten in:

    ```azurecli
    servicePrincipal=$(cat serviceprincipal.json | jq -r '.name')
    az ad sp delete --id $servicePrincipal
    ```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Uitpakken, transformeren en laden (ETL) op de juiste schaal](./hadoop/apache-hadoop-etl-at-scale.md)
