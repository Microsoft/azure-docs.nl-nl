---
title: 'Zelfstudie: Een cluster met de Apache Kafka REST-proxy ingeschakeld maken in HDInsight met behulp van Azure CLI'
description: Informatie over het uitvoeren van Apache Kafka-bewerkingen met behulp van een Kafka REST-proxy op Azure HDInsight.
ms.service: hdinsight
ms.topic: tutorial
ms.date: 02/27/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: ff11b8461b483f5a66df19bb1b108a1fe1168fb9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98944028"
---
# <a name="tutorial-create-an-apache-kafka-rest-proxy-enabled-cluster-in-hdinsight-using-azure-cli"></a>Zelfstudie: Een cluster met de Apache Kafka REST-proxy ingeschakeld maken in HDInsight met behulp van Azure CLI

In deze zelfstudie leert u hoe u met behulp van de Azure-opdrachtregelinterface (CLI) een cluster maakt waarvoor de Apache Kafka [REST-proxy](./rest-proxy.md) is ingeschakeld in Azure HDInsight. Azure HDInsight is een beheerde, zeer uitgebreide open-source analyseservice voor bedrijven. Apache Kafka is een open-source, gedistribueerd streamingplatform. Het wordt vaak gebruikt als een berichtenbroker, omdat het een functionaliteit biedt die vergelijkbaar is met een publicatie-/abonnementswachtrij voor berichten. Met de Kafka REST-proxy kunt u met uw Kafka-cluster communiceren via een [REST API](/rest/api/hdinsight-kafka-rest-proxy/) over HTTP. De Azure CLI is de platformoverschrijdende opdrachtregelervaring van Microsoft voor het beheren van Azure-resources.

De Apache Kafka-API is alleen toegankelijk voor resources binnen hetzelfde virtuele netwerk. U kunt het cluster rechtstreeks openen via SSH. Als u andere services, netwerken of virtuele machines wilt verbinden met Apache Kafka, moet u eerst een virtueel netwerk maken en vervolgens de resources maken in het netwerk. Zie [Verbinding maken met Apache Kafka via een virtueel netwerk](./apache-kafka-connect-vpn-gateway.md) voor meer informatie.

In deze zelfstudie komen deze onderwerpen aan bod:

> [!div class="checklist"]
> * Vereisten voor de Kafka REST-proxy
> * Een Apache Kafka-cluster maken met Azure CLI

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

* Een toepassing die is geregistreerd bij Azure AD. De clienttoepassingen die u schrijft om te communiceren met de Kafka REST-proxy gebruiken de id en het geheim van deze toepassing voor verificatie bij Azure. Zie [Een toepassing registreren bij het Microsoft-identiteitsplatform](../../active-directory/develop/quickstart-register-app.md) voor meer informatie.

* Een Azure AD-beveiligingsgroep waarvan uw geregistreerde toepassing lid is. Deze beveiligingsgroep wordt gebruikt om te bepalen welke toepassingen mogen communiceren met de REST-proxy. Zie [Een basisgroep maken en leden toevoegen met Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md) voor meer informatie over het maken van Azure AD-groepen.

* Azure CLI. Zorg ervoor dat u ten minste versie 2.0.79 hebt. Raadpleeg [De Azure CLI installeren](/cli/azure/install-azure-cli).

## <a name="create-an-apache-kafka-cluster"></a>Apache Kafka-cluster maken

1. Meld u aan bij uw Azure-abonnement.

    ```azurecli
    az login
    
    # If you have multiple subscriptions, set the one to use
    # az account set --subscription "SUBSCRIPTIONID"
    ```

1. Stel omgevingsvariabelen in. Het gebruik van variabelen in deze zelfstudie is gebaseerd op Bash. Er zijn kleine variaties nodig voor andere omgevingen.

    |Variabele | Beschrijving |
    |---|---|
    |resourceGroupName|Vervang RESOURCEGROUPNAME door de naam van uw nieuwe resourcegroep.|
    |location|Vervang LOCATION door de regio waar het cluster wordt gemaakt. Gebruik de opdracht `az account list-locations` voor een lijst met geldige locaties|
    |clusterName|Vervang CLUSTERNAME door een globaal unieke naam voor uw nieuwe cluster.|
    |storageAccount|Vervang STORAGEACCOUNTNAME door een naam voor uw nieuwe opslagaccount.|
    |httpPassword|Vervang PASSWORD door een wachtwoord voor de clusteraanmelding, **admin**.|
    |sshPassword|Vervang PASSWORD door een wachtwoord voor de gebruikersnaam van de beveiligde shell, **sshuser**.|
    |securityGroupName|Vervang SECURITYGROUPNAME door de naam van de AAD-clientbeveiligingsgroep voor de Kafka REST-proxy. De variabele wordt doorgegeven aan de parameter `--kafka-client-group-name` voor `az-hdinsight-create`.|
    |securityGroupID|Vervang SECURITYGROUPID door de id van de AAD-clientbeveiligingsgroep voor de Kafka REST-proxy. De variabele wordt doorgegeven aan de parameter `--kafka-client-group-id` voor `az-hdinsight-create`.|
    |storageContainer|Opslagcontainer die door het cluster wordt gebruikt. Laat deze staan voor deze zelfstudie. Deze variabele wordt ingesteld met de naam van het cluster.|
    |workernodeCount|Het aantal werkknooppunten in het cluster. Laat deze staan voor deze zelfstudie. Voor Kafka zijn minimaal drie werkknooppunten vereist om hoge beschikbaarheid te garanderen|
    |clusterType|Type HDInsight-cluster. Laat dit staan voor deze zelfstudie.|
    |clusterVersion|Versie van het HDInsight-cluster. Laat deze staan voor deze zelfstudie. Voor de Kafka REST-proxy is minimaal clusterversie 4.0 vereist.|
    |componentVersion|Versie van Kafka. Laat deze staan voor deze zelfstudie. Voor de Kafka REST-proxy is minimaal componentversie 2.1 vereist.|

    Werk de variabelen bij met de gewenste waarden. Voer vervolgens de CLI-opdrachten in om de omgevingsvariabelen in te stellen.

    ```azurecli
    export resourceGroupName=RESOURCEGROUPNAME
    export location=LOCATION
    export clusterName=CLUSTERNAME
    export storageAccount=STORAGEACCOUNTNAME
    export httpPassword='PASSWORD'
    export sshPassword='PASSWORD'
    export securityGroupName=SECURITYGROUPNAME
    export securityGroupID=SECURITYGROUPID

    export storageContainer=$(echo $clusterName | tr "[:upper:]" "[:lower:]")
    export workernodeCount=3
    export clusterType=kafka
    export clusterVersion=4.0
    export componentVersion=kafka=2.1
    ```

1. [Maak de resourcegroep](/cli/azure/group#az-group-create) door de onderstaande opdracht in te voeren:

    ```azurecli
     az group create \
        --location $location \
        --name $resourceGroupName
    ```

1. [Maak een Azure Storage-account](/cli/azure/storage/account#az-storage-account-create) door de onderstaande opdracht in te voeren:

    ```azurecli
    # Note: kind BlobStorage is not available as the default storage account.
    az storage account create \
        --name $storageAccount \
        --resource-group $resourceGroupName \
        --https-only true \
        --kind StorageV2 \
        --location $location \
        --sku Standard_LRS
    ```

1. [Extraheer de primaire sleutel](/cli/azure/storage/account/keys#az-storage-account-keys-list) uit het Azure Storage-account en sla deze op in een variabele door de onderstaande opdracht in te voeren:

    ```azurecli
    export storageAccountKey=$(az storage account keys list \
        --account-name $storageAccount \
        --resource-group $resourceGroupName \
        --query [0].value -o tsv)
    ```

1. [Maak een Azure Storage-container](/cli/azure/storage/container#az-storage-container-create) door de onderstaande opdracht in te voeren:

    ```azurecli
    az storage container create \
        --name $storageContainer \
        --account-key $storageAccountKey \
        --account-name $storageAccount
    ```

1. [Maak het HDInsight-cluster](/cli/azure/hdinsight#az-hdinsight-create). Noteer de volgende parameters voordat u de opdracht invoert:

    1. Vereiste parameters voor Kafka-clusters:

        |Parameter | Beschrijving|
        |---|---|
        |--type|De waarde moet **Kafka** zijn.|
        |--workernode-data-disks-per-node|Het aantal gegevensschijven dat per werkknooppunt moet worden gebruikt. HDInsight Kafka wordt alleen ondersteund met gegevensschijven. In deze zelfstudie wordt een waarde van **2** gebruikt.|

    1. Vereiste parameters voor de Kafka REST-proxy:

        |Parameter | Beschrijving|
        |---|---|
        |--kafka-management-node-size|De grootte van het knooppunt. In deze zelfstudie wordt de waarde **Standard_D4_v2** gebruikt.|
        |--kafka-client-group-id|De id van de AAD-clientbeveiligingsgroep voor de Kafka REST-proxy. De waarde wordt doorgegeven vanuit de variabele **$securityGroupID**.|
        |--kafka-client-group-name|De naam van de AAD-clientbeveiligingsgroep voor de Kafka REST-proxy. De waarde wordt doorgegeven vanuit de variabele **$securityGroupName**.|
        |--version|De versie van het HDInsight-cluster moet ten minste 4.0 zijn. De waarde wordt doorgegeven vanuit de variabele **$clusterVersion**.|
        |--component-version|De Kafka-versie moet ten minste 2.1 zijn. De waarde wordt doorgegeven vanuit de variabele **$componentVersion**.|
    
        Als u het cluster wilt maken zonder REST-proxy, verwijdert u `--kafka-management-node-size`, `--kafka-client-group-id` en `--kafka-client-group-name` uit de opdracht `az hdinsight create`.

    1. Als u een bestaand virtueel netwerk hebt, voegt u de parameters `--vnet-name` en `--subnet` plus de bijbehorende waarden toe.

    Voer de volgende opdracht in om het cluster te maken:

    ```azurecli
    az hdinsight create \
        --name $clusterName \
        --resource-group $resourceGroupName \
        --type $clusterType \
        --component-version $componentVersion \
        --http-password $httpPassword \
        --http-user admin \
        --location $location \
        --ssh-password $sshPassword \
        --ssh-user sshuser \
        --storage-account $storageAccount \
        --storage-account-key $storageAccountKey \
        --storage-container $storageContainer \
        --version $clusterVersion \
        --workernode-count $workernodeCount \
        --workernode-data-disks-per-node 2 \
        --kafka-management-node-size "Standard_D4_v2" \
        --kafka-client-group-id $securityGroupID \
        --kafka-client-group-name "$securityGroupName"
    ```

    Het kan enkele minuten duren voordat het cluster is gemaakt. Meestal ongeveer 15.

## <a name="clean-up-resources"></a>Resources opschonen

Nadat u het artikel hebt voltooid, kunt u het cluster verwijderen. Met HDInsight worden uw gegevens opgeslagen in Azure Storage zodat u een cluster veilig kunt verwijderen wanneer deze niet wordt gebruikt. Voor een HDInsight-cluster worden ook kosten in rekening gebracht, zelfs wanneer het niet wordt gebruikt. Aangezien de kosten voor het cluster vaak zoveel hoger zijn dan de kosten voor opslag, is het financieel gezien logischer clusters te verwijderen wanneer ze niet worden gebruikt.

Voer alle of enkele van de volgende opdrachten in om resources te verwijderen:

```azurecli
# Remove cluster
az hdinsight delete \
    --name $clusterName \
    --resource-group $resourceGroupName

# Remove storage container
az storage container delete \
    --account-name $storageAccount  \
    --name $storageContainer

# Remove storage account
az storage account delete \
    --name $storageAccount  \
    --resource-group $resourceGroupName

# Remove resource group
az group delete \
    --name $resourceGroupName
```

## <a name="next-steps"></a>Volgende stappen

Nu u een cluster met de Apache Kafka REST-proxy hebt gemaakt in Azure HDInsight met behulp van Azure CLI, kunt u Python-code gebruiken om met de REST-proxy te communiceren:

> [!div class="nextstepaction"]
> [Voorbeeldtoepassing maken](./rest-proxy.md#client-application-sample)