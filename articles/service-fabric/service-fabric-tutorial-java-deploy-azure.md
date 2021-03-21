---
title: Een Java-app implementeren in een Service Fabric-cluster in Azure
description: In deze zelfstudie leert u hoe u een Java Service Fabric-toepassing implementeert in een Azure Service Fabric-cluster.
ms.topic: tutorial
ms.date: 02/26/2018
ms.custom: mvc, devx-track-java, devx-track-azurecli
ms.openlocfilehash: 48cb7df49fb918ebd30883edc573cc63b6e35e4a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98789394"
---
# <a name="tutorial-deploy-a-java-application-to-a-service-fabric-cluster-in-azure"></a>Zelfstudie: Een Java-toepassing implementeren in een Service Fabric-cluster in Azure

Deze zelfstudie is deel drie van een reeks en laat zien hoe u een Service Fabric-toepassing implementeert in een cluster in Azure.

In deel drie van de serie leert u het volgende:

> [!div class="checklist"]
> * Een beveiligd Linux-cluster maken in Azure
> * Een toepassing implementeren in het cluster

In deze zelfstudiereeks leert u het volgende:

> [!div class="checklist"]
> * [Een Java Service Fabric Reliable Services-toepassing maken](service-fabric-tutorial-create-java-app.md)
> * [De toepassing implementeren en er foutopsporing op toepassen in een lokaal cluster](service-fabric-tutorial-debug-log-local-cluster.md)
> * De toepassing implementeren in een Azure-cluster
> * [Controle en diagnostische gegevens voor de toepassing instellen](service-fabric-tutorial-java-elk.md)
> * [CI/CD instellen](service-fabric-tutorial-java-jenkins.md)

## <a name="prerequisites"></a>Vereisten

Voor u met deze zelfstudie begint:

* Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Azure CLI installeren](/cli/azure/install-azure-cli)
* Installeer Service Fabric-SDK voor [Mac](service-fabric-get-started-mac.md) of [Linux](service-fabric-get-started-linux.md)
* [Installeer Python 3](https://wiki.python.org/moin/BeginnersGuide/Download)

## <a name="create-a-service-fabric-cluster-in-azure"></a>Een Service Fabric-cluster maken in Azure

In de volgende stappen maakt u de benodigde resources die vereist zijn voor het implementeren van uw toepassing in een Service Fabric-cluster. Daarnaast worden de resources ingesteld die nodig zijn om de status van uw oplossing te controleren met behulp van de ELK-stack (Elasticsearch, Logstash, Kibana). Met name [Event Hubs](https://azure.microsoft.com/services/event-hubs/) wordt gebruikt als een sink voor logboeken van Service Fabric. Dit wordt geconfigureerd voor het verzenden van de logboeken van het Service Fabric-cluster naar uw Logstash-instantie.

1. Open een terminal en download het volgende pakket dat de benodigde helperscripts en de sjablonen bevat voor het maken van de resources in Azure

    ```bash
    git clone https://github.com/Azure-Samples/service-fabric-java-quickstart.git
    ```

2. Aanmelden bij uw Azure-account

    ```azurecli
    az login
    ```

3. Stel het Azure-abonnement in dat u wilt gebruiken om de resources te maken

    ```azurecli
    az account set --subscription [SUBSCRIPTION-ID]
    ```

4. Voer vanuit de map *service-fabric-java-snelstart/AzureCluster* de volgende opdracht uit om een clustercertificaat te maken in de sleutelkluis. Dit certificaat wordt gebruikt om uw Service Fabric-cluster te beveiligen. Geef de regio op (deze moet hetzelfde zijn als uw Service Fabric-cluster), de naam van de resourcegroep van de sleutelkluis, de naam van de sleutelkluis, het wachtwoord van het certificaat en DNS-naam van het cluster.

    ```bash
    ./new-service-fabric-cluster-certificate.sh [REGION] [KEY-VAULT-RESOURCE-GROUP] [KEY-VAULT-NAME] [CERTIFICATE-PASSWORD] [CLUSTER-DNS-NAME-FOR-CERTIFICATE]

    Example: ./new-service-fabric-cluster-certificate.sh 'westus' 'testkeyvaultrg' 'testkeyvault' '<password>' 'testservicefabric.westus.cloudapp.azure.com'
    ```

    Met de voorgaande opdracht wordt de volgende informatie opgehaald die moet worden genoteerd voor later gebruik.

    ```output
    Source Vault Resource Id: /subscriptions/<subscription_id>/resourceGroups/testkeyvaultrg/providers/Microsoft.KeyVault/vaults/<name>
    Certificate URL: https://<name>.vault.azure.net/secrets/<cluster-dns-name-for-certificate>/<guid>
    Certificate Thumbprint: <THUMBPRINT>
    ```

5. Een resourcegroep maken voor het opslagaccount waarin uw logboeken worden opgeslagen

    ```azurecli
    az group create --location [REGION] --name [RESOURCE-GROUP-NAME]

    Example: az group create --location westus --name teststorageaccountrg
    ```

6. Een opslagaccount maken dat wordt gebruikt voor het opslaan van de logboeken die worden geproduceerd

    ```azurecli
    az storage account create -g [RESOURCE-GROUP-NAME] -l [REGION] --name [STORAGE-ACCOUNT-NAME] --kind Storage

    Example: az storage account create -g teststorageaccountrg -l westus --name teststorageaccount --kind Storage
    ```

7. Open [Azure Portal](https://portal.azure.com) en navigeer naar het tabblad **Shared Access Signature** voor uw opslagaccount. Genereer de SAS-token als volgt.

    ![SAS genereren voor opslag](./media/service-fabric-tutorial-java-deploy-azure/storagesas.png)

8. Kopieer de URL van de account-SAS en bewaar deze voor gebruik bij het maken van uw Service Fabric-cluster. Deze lijkt op de volgende URL:

    ```output
    ?sv=2017-04-17&ss=bfqt&srt=sco&sp=rwdlacup&se=2018-01-31T03:24:04Z&st=2018-01-30T19:24:04Z&spr=https,http&sig=IrkO1bVQCHcaKaTiJ5gilLSC5Wxtghu%2FJAeeY5HR%2BPU%3D
    ```

9. Maak een resourcegroep die de Event Hub-resources bevat. Event Hubs wordt gebruikt om berichten te verzenden vanuit Service Fabric voor de server waarop de ELK-resources worden uitgevoerd.

    ```azurecli
    az group create --location [REGION] --name [RESOURCE-GROUP-NAME]

    Example: az group create --location westus --name testeventhubsrg
    ```

10. Maak een Event Hubs-resource met behulp van de volgende opdracht. Volg de aanwijzingen om details in te voeren voor de namespaceName, eventHubName, consumerGroupName, sendAuthorizationRule en receiveAuthorizationRule.

    ```azurecli
    az deployment group create -g [RESOURCE-GROUP-NAME] --template-file eventhubsdeploy.json

    Example:
    az deployment group create -g testeventhubsrg --template-file eventhubsdeploy.json
    Please provide string value for 'namespaceName' (? for help): testeventhubnamespace
    Please provide string value for 'eventHubName' (? for help): testeventhub
    Please provide string value for 'consumerGroupName' (? for help): testeventhubconsumergroup
    Please provide string value for 'sendAuthorizationRuleName' (? for help): sender
    Please provide string value for 'receiveAuthorizationRuleName' (? for help): receiver
    ```

    Kopieer de inhoud van het veld **output** naar de JSON-uitvoer van de voorgaande opdracht. De gegevens van de afzender worden gebruikt wanneer het Service Fabric-cluster wordt gemaakt. De naam van de ontvanger en de sleutel moeten worden opgeslagen voor gebruik in de volgende zelfstudie wanneer de Logstash-service wordt geconfigureerd voor het ontvangen van berichten van Event Hub. De volgende blob is een voorbeeld van de JSON-uitvoer:

    ```json
    "outputs": {
        "receiver Key": {
            "type": "String",
            "value": "[KEY]"
        },
        "receiver Name": {
            "type": "String",
            "value": "receiver"
        },
        "sender Key": {
            "type": "String",
            "value": "[KEY]"
        },
        "sender Name": {
            "type": "String",
            "value": "sender"
        }
    }
    ```

11. Voer het script *eventhubssastoken.py* uit om de SAS-URL te genereren voor de Event Hubs-resource die u hebt gemaakt. Deze SAS-URL wordt door het Service Fabric-cluster gebruikt om logboeken te verzenden naar Event Hubs. Als gevolg hiervan wordt het beleid van de **afzender** gebruikt om de URL te genereren. Het script retourneert de SAS-URL voor de Event Hubs-resource die in de volgende stap wordt gebruikt:

    ```python
    python3 eventhubssastoken.py 'testeventhubs' 'testeventhubs' 'sender' '[PRIMARY-KEY]'
    ```

    Kopieer de waarde van het veld **sr** in de JSON die is geretourneerd. De veldwaarde **sr** is de SAS-token voor EventHubs. De volgende URL is een voorbeeld van het veld **sr**:

    ```output
    https%3A%2F%testeventhub.servicebus.windows.net%testeventhub&sig=7AlFYnbvEm%2Bat8ALi54JqHU4i6imoFxkjKHS0zI8z8I%3D&se=1517354876&skn=sender
    ```

    Uw SAS URL voor de EventHubs volgt de structuur: `https://<namespacename>.servicebus.windows.net/<eventhubsname>?sr=<sastoken>`. Bijvoorbeeld: `https://testeventhubnamespace.servicebus.windows.net/testeventhub?sr=https%3A%2F%testeventhub.servicebus.windows.net%testeventhub&sig=7AlFYnbvEm%2Bat8ALi54JqHU4i6imoFxkjKHS0zI8z8I%3D&se=1517354876&skn=sender`

12. Open het bestand *sfdeploy.parameters.json* en vervang de volgende inhoud uit de voorgaande stappen. [SAS-URL-STORAGE-ACCOUNT] hebt u genoteerd in stap 8. [SAS-URL-EVENT-HUBS] hebt u genoteerd in stap 11.

    ```json
    "applicationDiagnosticsStorageAccountName": {
        "value": "teststorageaccount"
    },
    "applicationDiagnosticsStorageAccountSasToken": {
        "value": "[SAS-URL-STORAGE-ACCOUNT]"
    },
    "loggingEventHubSAS": {
        "value": "[SAS-URL-EVENT-HUBS]"
    }
    ```

13. Opens **sfdeploy.parameters.json**. Wijzig de volgende parameters en sla het bestand op.
    - **clusterName**. Gebruik alleen kleine letters en cijfers.
    - **adminUserName** (in een waarde anders dan leeg)
    - **adminPassword** (in een waarde anders dan leeg)

14. Voer de volgende opdracht uit om uw Service Fabric-cluster te maken

    ```azurecli
    az sf cluster create --location 'westus' --resource-group 'testlinux' --template-file sfdeploy.json --parameter-file sfdeploy.parameters.json --secret-identifier <certificate_url_from_step4>
    ```

## <a name="deploy-your-application-to-the-cluster"></a>De toepassing in het cluster implementeren

1. Voordat u uw toepassing implementeert, moet u het volgende codefragment toevoegen aan het bestand *Voting/VotingApplication/ApplicationManifest.xml*. Het veld **X509FindValue** is de vingerafdruk die is geretourneerd uit stap 4 van de sectie **Een Service Fabric-cluster maken in Azure**. Dit codefragment is genest onder het veld **ApplicationManifest** (het hoofdveld).

    ```xml
    <Certificates>
          <SecretsCertificate X509FindType="FindByThumbprint" X509FindValue="[CERTIFICATE-THUMBPRINT]" />
    </Certificates>
    ```

2. Om uw toepassing in dit cluster te implementeren, moet u SFCTL gebruiken om een verbinding met het cluster tot stand te brengen. SFCTL vereist een PEM-bestand met zowel de openbare als de persoonlijke sleutel om verbinding te maken met het cluster. Voer de volgende opdracht uit om een PEM-bestand met zowel de openbare als de persoonlijke sleutel te maken. 

    ```bash
    openssl pkcs12 -in <clustername>.<region>.cloudapp.azure.com.pfx -out sfctlconnection.pem -nodes -passin pass:<password>
    ```

3. Voer de volgende opdracht uit om verbinding te maken met het cluster.

    ```bash
    sfctl cluster select --endpoint https://<clustername>.<region>.cloudapp.azure.com:19080 --pem sfctlconnection.pem --no-verify
    ```

4. Om uw toepassing te implementeren, navigeert u naar de map *Voting/Scripts* en voert u het script **install.sh** uit.

    ```bash
    ./install.sh
    ```

5. Voor toegang tot Service Fabric Explorer opent u uw favoriete browser en typt u `https://testlinuxcluster.westus.cloudapp.azure.com:19080`. Selecteer het certificaat in het certificaatarchief dat u wilt gebruiken om verbinding te maken met dit eindpunt. Als u een Linux-machine gebruikt, moeten de certificaten die zijn gegenereerd door het script *new-service-fabric-cluster-certificate.sh* worden geïmporteerd in Chrome om Service Fabric Explorer weer te geven. Als u een Mac gebruikt, moet u het PFX-bestand in uw sleutelketen installeren. U ziet dat uw toepassing is geïnstalleerd in het cluster.

    ![SFX Java Azure](./media/service-fabric-tutorial-java-deploy-azure/sfxjavaonazure.png)

6. Typ `https://testlinuxcluster.westus.cloudapp.azure.com:8080` voor toegang tot uw toepassing

    ![Stem-app in Java Azure](./media/service-fabric-tutorial-java-deploy-azure/votingappjavaazure.png)

7. Als u uw toepassing wilt verwijderen uit het cluster, voert u het script *uninstall.sh* uit in de map **Scripts**

    ```bash
    ./uninstall.sh
    ```

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

> [!div class="checklist"]
> * Een beveiligd Linux-cluster maken in Azure
> * Resources maken die nodig zijn voor controle met ELK

Ga door naar de volgende zelfstudie:
> [!div class="nextstepaction"]
> [Controle en diagnose instellen](service-fabric-tutorial-java-elk.md)
