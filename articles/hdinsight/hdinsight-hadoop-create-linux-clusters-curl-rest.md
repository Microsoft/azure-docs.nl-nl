---
title: Apache Hadoop clusters maken met Azure REST API-Azure
description: Meer informatie over het maken van HDInsight-clusters door Azure Resource Manager sjablonen te verzenden naar de Azure-REST API.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, devx-track-azurecli
ms.date: 12/10/2019
ms.openlocfilehash: fa5675104d9614e1bd917585ea537c92dddd88cc
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98945779"
---
# <a name="create-apache-hadoop-clusters-using-the-azure-rest-api"></a>Apache Hadoop clusters maken met behulp van de Azure-REST API

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Meer informatie over het maken van een HDInsight-cluster met behulp van een Azure Resource Manager sjabloon en de Azure REST API.

Met Azure REST API kunt u beheer bewerkingen uitvoeren op Services die worden gehost op het Azure-platform, inclusief het maken van nieuwe resources zoals HDInsight-clusters.

> [!NOTE]  
> De stappen in dit document gebruiken het [krul ( https://curl.haxx.se/) ](https://curl.haxx.se/) hulp programma om te communiceren met de Azure rest API.

## <a name="create-a-template"></a>Een sjabloon maken

Azure Resource Manager sjablonen zijn JSON-documenten waarmee een **resource groep** en alle resources erin worden beschreven (zoals HDInsight.) Met deze op sjablonen gebaseerde benadering kunt u de resources definiëren die u nodig hebt voor HDInsight in één sjabloon.

Het volgende JSON-document is een fusie van de sjabloon en de parameter bestanden van [https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-ssh-password](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-ssh-password) , die een Linux-cluster maakt met behulp van een wacht woord voor het beveiligen van het SSH-gebruikers account.

   ```json
   {
       "properties": {
           "template": {
               "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
               "contentVersion": "1.0.0.0",
               "parameters": {
                   "clusterType": {
                       "type": "string",
                       "allowedValues": ["hadoop",
                       "hbase",
                       "storm",
                       "spark"],
                       "metadata": {
                           "description": "The type of the HDInsight cluster to create."
                       }
                   },
                   "clusterName": {
                       "type": "string",
                       "metadata": {
                           "description": "The name of the HDInsight cluster to create."
                       }
                   },
                   "clusterLoginUserName": {
                       "type": "string",
                       "metadata": {
                           "description": "These credentials can be used to submit jobs to the cluster and to log into cluster dashboards."
                       }
                   },
                   "clusterLoginPassword": {
                       "type": "securestring",
                       "metadata": {
                           "description": "The password must be at least 10 characters in length and must contain at least one digit, one non-alphanumeric character, and one upper or lower case letter."
                       }
                   },
                   "sshUserName": {
                       "type": "string",
                       "metadata": {
                           "description": "These credentials can be used to remotely access the cluster."
                       }
                   },
                   "sshPassword": {
                       "type": "securestring",
                       "metadata": {
                           "description": "The password must be at least 10 characters in length and must contain at least one digit, one non-alphanumeric character, and one upper or lower case letter."
                       }
                   },
                   "clusterStorageAccountName": {
                       "type": "string",
                       "metadata": {
                           "description": "The name of the storage account to be created and be used as the cluster's storage."
                       }
                   },
                   "clusterWorkerNodeCount": {
                       "type": "int",
                       "defaultValue": 4,
                       "metadata": {
                           "description": "The number of nodes in the HDInsight cluster."
                       }
                   }
               },
               "variables": {
                   "defaultApiVersion": "2015-05-01-preview",
                   "clusterApiVersion": "2015-03-01-preview"
               },
               "resources": [{
                   "name": "[parameters('clusterStorageAccountName')]",
                   "type": "Microsoft.Storage/storageAccounts",
                   "location": "[resourceGroup().location]",
                   "apiVersion": "[variables('defaultApiVersion')]",
                   "dependsOn": [],
                   "tags": {

                   },
                   "properties": {
                       "accountType": "Standard_LRS"
                   }
               },
               {
                   "name": "[parameters('clusterName')]",
                   "type": "Microsoft.HDInsight/clusters",
                   "location": "[resourceGroup().location]",
                   "apiVersion": "[variables('clusterApiVersion')]",
                   "dependsOn": ["[concat('Microsoft.Storage/storageAccounts/',parameters('clusterStorageAccountName'))]"],
                   "tags": {

                   },
                   "properties": {
                       "clusterVersion": "3.6",
                       "osType": "Linux",
                       "clusterDefinition": {
                           "kind": "[parameters('clusterType')]",
                           "configurations": {
                               "gateway": {
                                   "restAuthCredential.isEnabled": true,
                                   "restAuthCredential.username": "[parameters('clusterLoginUserName')]",
                                   "restAuthCredential.password": "[parameters('clusterLoginPassword')]"
                               }
                           }
                       },
                       "storageProfile": {
                           "storageaccounts": [{
                               "name": "[concat(parameters('clusterStorageAccountName'),'.blob.core.windows.net')]",
                               "isDefault": true,
                               "container": "[parameters('clusterName')]",
                               "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('clusterStorageAccountName')), variables('defaultApiVersion')).key1]"
                           }]
                       },
                       "computeProfile": {
                           "roles": [{
                               "name": "headnode",
                               "targetInstanceCount": "2",
                               "hardwareProfile": {
                                   "vmSize": "{}" 
                               },
                               "osProfile": {
                                   "linuxOperatingSystemProfile": {
                                       "username": "[parameters('sshUserName')]",
                                       "password": "[parameters('sshPassword')]"
                                   }
                               }
                           },
                           {
                               "name": "workernode",
                               "targetInstanceCount": "[parameters('clusterWorkerNodeCount')]",
                               "hardwareProfile": {
                                   "vmSize": "{}"
                               },
                               "osProfile": {
                                   "linuxOperatingSystemProfile": {
                                       "username": "[parameters('sshUserName')]",
                                       "password": "[parameters('sshPassword')]"
                                   }
                               }
                           }]
                       }
                   }
               }],
               "outputs": {
                   "cluster": {
                       "type": "object",
                       "value": "[reference(resourceId('Microsoft.HDInsight/clusters',parameters('clusterName')))]"
                   }
               }
           },
           "mode": "incremental",
           "Parameters": {
               "clusterName": {
                   "value": "newclustername"
               },
               "clusterType": {
                   "value": "hadoop"
               },
               "clusterStorageAccountName": {
                   "value": "newstoragename"
               },
               "clusterLoginUserName": {
                   "value": "admin"
               },
               "clusterLoginPassword": {
                   "value": "changeme"
               },
               "sshUserName": {
                   "value": "sshuser"
               },
               "sshPassword": {
                   "value": "changeme"
               }
           }
       }
   }
   ```

Dit voor beeld wordt gebruikt in de stappen in dit document. Vervang de voorbeeld *waarden* in het gedeelte **para meters** door de waarden voor uw cluster.

> [!IMPORTANT]  
> De sjabloon gebruikt het standaard aantal worker-knoop punten (4) voor een HDInsight-cluster. Als u meer dan 32 worker-knoop punten wilt plannen, moet u een hoofd knooppunt grootte selecteren met ten minste 8 kernen en een RAM-geheugen van 14 GB.
>
> Zie [Prijsdetails voor Azure HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/) voor meer informatie over knooppuntgrootten en de bijbehorende kosten.

## <a name="sign-in-to-your-azure-subscription"></a>Aanmelden bij uw Azure-abonnement

Volg de stappen in [aan de slag met Azure cli](/cli/azure/get-started-with-az-cli2) en maak verbinding met uw abonnement met behulp van de `az login` opdracht.

## <a name="create-a-service-principal"></a>Een service-principal maken

> [!NOTE]  
> Deze stappen zijn een verkorte versie van de sectie *Service-Principal maken met wacht woord* van [Azure CLI gebruiken om een service-principal te maken voor toegang tot resources](/cli/azure/create-an-azure-service-principal-azure-cli) -document. Met deze stappen maakt u een service-principal die wordt gebruikt voor verificatie bij de Azure-REST API.

1. Gebruik vanaf een opdracht regel de volgende opdracht om uw Azure-abonnementen weer te geven.

   ```azurecli
   az account list --query '[].{Subscription_ID:id,Tenant_ID:tenantId,Name:name}'  --output table
   ```

    Selecteer in de lijst het abonnement dat u wilt gebruiken en noteer de **Subscription_ID** en __Tenant_ID__ kolommen. Sla deze waarden op.

2. Gebruik de volgende opdracht om een toepassing te maken in Azure Active Directory.

   ```azurecli
   az ad app create --display-name "exampleapp" --homepage "https://www.contoso.org" --identifier-uris "https://www.contoso.org/example" --password <Your password> --query 'appId'
   ```

    Vervang de waarden voor de `--display-name` , `--homepage` en door `--identifier-uris` uw eigen waarden. Geef een wacht woord op voor de nieuwe Active Directory vermelding.

   > [!NOTE]  
   > De `--home-page` `--identifier-uris` waarden en hoeven niet te verwijzen naar een daad werkelijke webpagina die wordt gehost op internet. Ze moeten unieke Uri's zijn.

   De waarde die door deze opdracht wordt geretourneerd, is de __App-ID__ voor de nieuwe toepassing. Sla deze waarde op.

3. Gebruik de volgende opdracht om een service-principal te maken met behulp van de **App-ID**.

   ```azurecli
   az ad sp create --id <App ID> --query 'objectId'
   ```

     De waarde die door deze opdracht wordt geretourneerd, is de __object-id__. Sla deze waarde op.

4. Wijs de rol **eigenaar** toe aan de service-principal met behulp van de **object-id-** waarde. Gebruik de **abonnements-id** die u eerder hebt verkregen.

   ```azurecli
   az role assignment create --assignee <Object ID> --role Owner --scope /subscriptions/<Subscription ID>/
   ```

## <a name="get-an-authentication-token"></a>Een verificatie Token ophalen

Gebruik de volgende opdracht om een verificatie token op te halen:

```bash
curl -X "POST" "https://login.microsoftonline.com/$TENANTID/oauth2/token" \
-H "Cookie: flight-uxoptin=true; stsservicecookie=ests; x-ms-gateway-slice=productionb; stsservicecookie=ests" \
-H "Content-Type: application/x-www-form-urlencoded" \
--data-urlencode "client_id=$APPID" \
--data-urlencode "grant_type=client_credentials" \
--data-urlencode "client_secret=$PASSWORD" \
--data-urlencode "resource=https://management.azure.com/"
```

Stel `$TENANTID` , `$APPID` , en `$PASSWORD` op de waarden die u eerder hebt verkregen of gebruikt.

Als deze aanvraag is voltooid, ontvangt u een respons van 200-series en bevat de antwoord tekst een JSON-document.

Het JSON-document dat door deze aanvraag wordt geretourneerd bevat een element met de naam **access_token**. De waarde van **access_token** wordt gebruikt voor het verifiëren van aanvragen voor de rest API.

```json
{
    "token_type":"Bearer",
    "expires_in":"3599",
    "expires_on":"1463409994",
    "not_before":"1463406094",
    "resource":"https://management.azure.com/","access_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWoNBVGZNNXBPWWlKSE1iYTlnb0VLWSIsImtpZCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSJ9.eyJhdWQiOiJodHRwczovL21hbmFnZW1lbnQuYXp1cmUuY29tLyIsImlzcyI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI2Ny8iLCJpYXQiOjE0NjM0MDYwOTQsIm5iZiI6MTQ2MzQwNjA5NCwiZXhwIjoxNDYzNDA5OTk5LCJhcHBpZCI6IjBlYzcyMzM0LTZkMDMtNDhmYi04OWU1LTU2NTJiODBiZDliYiIsImFwcGlkYWNyIjoiMSIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0Ny8iLCJvaWQiOiJlNjgxZTZiMi1mZThkLTRkZGUtYjZiMS0xNjAyZDQyNWQzOWYiLCJzdWIiOiJlNjgxZTZiMi1mZThkLTRkZGUtYjZiMS0xNjAyZDQyNWQzOWYiLCJ0aWQiOiI3MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDciLCJ2ZXIiOiIxLjAifQ.nJVERbeDHLGHn7ZsbVGBJyHOu2PYhG5dji6F63gu8XN2Cvol3J1HO1uB4H3nCSt9DTu_jMHqAur_NNyobgNM21GojbEZAvd0I9NY0UDumBEvDZfMKneqp7a_cgAU7IYRcTPneSxbD6wo-8gIgfN9KDql98b0uEzixIVIWra2Q1bUUYETYqyaJNdS4RUmlJKNNpENllAyHQLv7hXnap1IuzP-f5CNIbbj9UgXxLiOtW5JhUAwWLZ3-WMhNRpUO2SIB7W7tQ0AbjXw3aUYr7el066J51z5tC1AK9UC-mD_fO_HUP6ZmPzu5gLA6DxkIIYP3grPnRVoUDltHQvwgONDOw"
}
```

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Gebruik het volgende om een resource groep te maken.

* Ingesteld `$SUBSCRIPTIONID` op de abonnements-id die is ontvangen tijdens het maken van de Service-Principal.
* Instellen `$ACCESSTOKEN` op het toegangs token dat u in de vorige stap hebt ontvangen.
* Vervang door `DATACENTERLOCATION` het Data Center waarin u de resource groep en de resources wilt maken. Bijvoorbeeld ' Zuid-Centraal VS '.
* Stel `$RESOURCEGROUPNAME` in op de naam die u wilt gebruiken voor deze groep:

```bash
curl -X "PUT" "https://management.azure.com/subscriptions/$SUBSCRIPTIONID/resourcegroups/$RESOURCEGROUPNAME?api-version=2015-01-01" \
    -H "Authorization: Bearer $ACCESSTOKEN" \
    -H "Content-Type: application/json" \
    -d $'{
"location": "DATACENTERLOCATION"
}'
```

Als deze aanvraag is voltooid, ontvangt u een respons van 200-series en bevat de antwoord tekst een JSON-document met informatie over de groep. Het `"provisioningState"` element bevat een waarde van `"Succeeded"` .

## <a name="create-a-deployment"></a>Een implementatie maken

Gebruik de volgende opdracht om de sjabloon te implementeren in de resource groep.

* Stel `$DEPLOYMENTNAME` in op de naam die u wilt gebruiken voor deze implementatie.

```bash
curl -X "PUT" "https://management.azure.com/subscriptions/$SUBSCRIPTIONID/resourcegroups/$RESOURCEGROUPNAME/providers/microsoft.resources/deployments/$DEPLOYMENTNAME?api-version=2015-01-01" \
-H "Authorization: Bearer $ACCESSTOKEN" \
-H "Content-Type: application/json" \
-d "{set your body string to the template and parameters}"
```

> [!NOTE]  
> Als u de sjabloon hebt opgeslagen in een bestand, kunt u de volgende opdracht gebruiken in plaats van `-d "{ template and parameters}"` :
>
> `--data-binary "@/path/to/file.json"`

Als deze aanvraag is voltooid, ontvangt u een respons van 200-series en bevat de antwoord tekst een JSON-document met informatie over de implementatie bewerking.

> [!IMPORTANT]  
> De implementatie is verzonden, maar is niet voltooid. Het kan enkele minuten duren voordat de implementatie is voltooid.

## <a name="check-the-status-of-a-deployment"></a>De status van een implementatie controleren

Als u de status van de implementatie wilt controleren, gebruikt u de volgende opdracht:

```bash
curl -X "GET" "https://management.azure.com/subscriptions/$SUBSCRIPTIONID/resourcegroups/$RESOURCEGROUPNAME/providers/microsoft.resources/deployments/$DEPLOYMENTNAME?api-version=2015-01-01" \
-H "Authorization: Bearer $ACCESSTOKEN" \
-H "Content-Type: application/json"
```

Met deze opdracht wordt een JSON-document geretourneerd dat informatie bevat over de implementatie bewerking. Het `"provisioningState"` element bevat de status van de implementatie. Als dit element een waarde bevat `"Succeeded"` , is de implementatie voltooid.

## <a name="troubleshoot"></a>Problemen oplossen

Zie [Vereisten voor toegangsbeheer](./hdinsight-hadoop-customize-cluster-linux.md#access-control) als u problemen ondervindt met het maken van HDInsight-clusters.

## <a name="next-steps"></a>Volgende stappen

Nu u een HDInsight-cluster hebt gemaakt, gebruikt u de volgende informatie om te leren werken met uw cluster.

### <a name="apache-hadoop-clusters"></a>Apache Hadoop clusters

* [Apache Hive gebruiken met HDInsight](hadoop/hdinsight-use-hive.md)
* [MapReduce gebruiken met HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Apache HBase-clusters

* [Aan de slag met Apache HBase in HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Ontwikkel Java-toepassingen voor Apache HBase in HDInsight](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="apache-storm-clusters"></a>Apache Storm clusters

* [Java-topologieën ontwikkelen voor Apache Storm op HDInsight](storm/apache-storm-develop-java-topology.md)
* [Python-onderdelen gebruiken in Apache Storm in HDInsight](storm/apache-storm-develop-python-topology.md)
* [Topologieën implementeren en bewaken met Apache Storm op HDInsight](storm/apache-storm-deploy-monitor-topology-linux.md)
