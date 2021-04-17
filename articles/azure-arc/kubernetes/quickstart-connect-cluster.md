---
title: 'Quickstart: Een bestaand Kubernetes-cluster verbinden met Azure Arc'
description: In deze quickstart leert u hoe u een kubernetes Azure Arc cluster kunt verbinden.
author: mgoedtel
ms.author: magoedte
ms.service: azure-arc
ms.topic: quickstart
ms.date: 03/03/2021
ms.custom: template-quickstart, references_regions, devx-track-azurecli
keywords: Kubernetes, Arc, Azure, cluster
ms.openlocfilehash: 21ec5000ed7ef9df1805fa6ec43e20efc0f82182
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107481239"
---
# <a name="quickstart-connect-an-existing-kubernetes-cluster-to-azure-arc"></a>Quickstart: Een bestaand Kubernetes-cluster verbinden met Azure Arc 

In deze quickstart profiteren we van de voordelen van kubernetes Azure Arc kubernetes en verbinden we een bestaand Kubernetes-cluster met Azure Arc. Voor een conceptueel artikel over het verbinden van clusters met Azure Arc, zie Azure Arc [Kubernetes Agent Architecture (Architectuur van Kubernetes-agent) voor een conceptueel artikel.](./conceptual-agent-architecture.md)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

* Een actief Kubernetes-cluster. Als u nog geen cluster hebt, kunt u een cluster maken met behulp van een van de volgende opties:
    * [Kubernetes in Docker (KIND)](https://kind.sigs.k8s.io/)
    * Een Kubernetes-cluster maken met Docker voor [Mac](https://docs.docker.com/docker-for-mac/#kubernetes) of [Windows](https://docs.docker.com/docker-for-windows/#kubernetes)
    * Zelf-beheerd Kubernetes-cluster met [cluster-API](https://cluster-api.sigs.k8s.io/user/quick-start.html)

    >[!NOTE]
    > Het cluster moet ten minste één knooppunt van het besturingssysteem en architectuurtype `linux/amd64` hebben. Clusters met alleen `linux/arm64` knooppunten worden nog niet ondersteund.
    
* Een `kubeconfig` bestand en context die naar uw cluster wijzen.
* De machtigingen Lezen en Schrijven voor de Azure Arc Kubernetes-resourcetype ingeschakeld ( `Microsoft.Kubernetes/connectedClusters` ).

* Installeer de [nieuwste versie van Helm 3.](https://helm.sh/docs/intro/install)

- [Azure CLI installeren of upgraden](https://docs.microsoft.com/cli/azure/install-azure-cli) naar versie >= 2.16.0
* Installeer de `connectedk8s` Azure CLI-extensie van versie >= 1.0.0:
  
  ```azurecli
  az extension add --name connectedk8s
  ```

>[!TIP]
> Als de `connectedk8s` extensie al is geïnstalleerd, werkt u deze bij naar de nieuwste versie met behulp van de volgende opdracht: `az extension update --name connectedk8s`


>[!NOTE]
>De lijst met regio's Azure Arc Kubernetes wordt ondersteund, vindt u [hier.](https://azure.microsoft.com/global-infrastructure/services/?products=azure-arc)

>[!NOTE]
> Als u aangepaste locaties in het cluster wilt gebruiken, gebruikt u VS - oost of Europa - west-regio's om uw cluster te verbinden als aangepaste locaties is vanaf nu alleen beschikbaar in deze regio's. Alle andere Azure Arc kubernetes-functies zijn beschikbaar in alle hierboven vermelde regio's.

## <a name="meet-network-requirements"></a>Voldoen aan netwerkvereisten

>[!IMPORTANT]
>Azure Arc agents moeten de volgende protocollen/poorten/uitgaande URL's werken:
>* TCP op poort 443: `https://:443`
>* TCP op poort 9418: `git://:9418`
  
| Eindpunt (DNS) | Beschrijving |  
| ----------------- | ------------- |  
| `https://management.azure.com`                                                                                 | Vereist om de agent verbinding te laten maken met Azure en het cluster te registreren.                                                        |  
| `https://<region>.dp.kubernetesconfiguration.azure.com` | Gegevensvlak-eindpunt voor de agent om de status te pushen en configuratiegegevens op te halen.                                      |  
| `https://login.microsoftonline.com`                                                                            | Vereist voor het ophalen en bijwerken Azure Resource Manager tokens.                                                                                    |  
| `https://mcr.microsoft.com`                                                                            | Vereist voor het pullen van containerafbeeldingen Azure Arc agents.                                                                  |  
| `https://eus.his.arc.azure.com`, `https://weu.his.arc.azure.com`, `https://wcus.his.arc.azure.com`, `https://scus.his.arc.azure.com`, `https://sea.his.arc.azure.com`, `https://uks.his.arc.azure.com`, `https://wus2.his.arc.azure.com`, `https://ae.his.arc.azure.com`, `https://eus2.his.arc.azure.com`, `https://ne.his.arc.azure.com` |  Vereist om door het systeem toegewezen MSI-certificaten (Managed Service Identity) op te halen.                                                                  |

## <a name="register-the-two-providers-for-azure-arc-enabled-kubernetes"></a>De twee providers registreren voor Azure Arc Kubernetes

1. Voer de volgende opdrachten in:
    ```azurecli
    az provider register --namespace Microsoft.Kubernetes
    az provider register --namespace Microsoft.KubernetesConfiguration
    az provider register --namespace Microsoft.ExtendedLocation
    ```
2. Controleer het registratieproces. De registratie kan maximaal 10 minuten duren.
    ```azurecli
    az provider show -n Microsoft.Kubernetes -o table
    az provider show -n Microsoft.KubernetesConfiguration -o table
    az provider show -n Microsoft.ExtendedLocation -o table
    ```

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Een resourcegroep maken:  

```console
az group create --name AzureArcTest -l EastUS -o table
```

```output
Location    Name
----------  ------------
eastus      AzureArcTest
```

## <a name="connect-an-existing-kubernetes-cluster"></a>Een bestaand Kubernetes-cluster verbinden

1. Verbind uw Kubernetes-cluster met Azure Arc met behulp van de volgende opdracht:
    ```console
    az connectedk8s connect --name AzureArcTest1 --resource-group AzureArcTest
    ```

    ```output
    Helm release deployment succeeded

    {
      "aadProfile": {
        "clientAppId": "",
        "serverAppId": "",
        "tenantId": ""
      },
      "agentPublicKeyCertificate": "xxxxxxxxxxxxxxxxxxx",
      "agentVersion": null,
      "connectivityStatus": "Connecting",
      "distribution": "gke",
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/AzureArcTest/providers/Microsoft.Kubernetes/connectedClusters/AzureArcTest1",
      "identity": {
        "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "type": "SystemAssigned"
      },
      "infrastructure": "gcp",
      "kubernetesVersion": null,
      "lastConnectivityTime": null,
      "location": "eastus",
      "managedIdentityCertificateExpirationTime": null,
      "name": "AzureArcTest1",
      "offering": null,
      "provisioningState": "Succeeded",
      "resourceGroup": "AzureArcTest",
      "tags": {},
      "totalCoreCount": null,
      "totalNodeCount": null,
      "type": "Microsoft.Kubernetes/connectedClusters"
    }
    ```

> [!TIP]
> Met de bovenstaande opdracht zonder de opgegeven locatieparameter wordt Azure Arc Kubernetes-resource gemaakt op dezelfde locatie als de resourcegroep. Als u de Azure Arc Kubernetes-resource op een andere locatie wilt maken, geeft u of op `--location <region>` bij het uitvoeren van de `-l <region>` `az connectedk8s connect` opdracht.

## <a name="verify-cluster-connection"></a>Clusterverbinding controleren

Bekijk een lijst met uw verbonden clusters met de volgende opdracht:  

```console
az connectedk8s list -g AzureArcTest -o table
```

```output
Name           Location    ResourceGroup
-------------  ----------  ---------------
AzureArcTest1  eastus      AzureArcTest
```

> [!NOTE]
> Na het onboarden van het cluster duurt het ongeveer 5 tot 10 minuten voordat de clustermetagegevens (clusterversie, agentversie, aantal knooppunten, enzovoort) worden weergegeven op de overzichtspagina van de Kubernetes-resource met Azure Arc-functie in Azure Portal.

## <a name="connect-using-an-outbound-proxy-server"></a>Verbinding maken met behulp van een uitgaande proxyserver

Als uw cluster zich achter een uitgaande proxyserver, moeten Azure CLI en de Azure Arc ingeschakelde Kubernetes-agents hun aanvragen doorverzenden via de uitgaande proxyserver. 


1. Stel de omgevingsvariabelen in die azure CLI nodig heeft om de uitgaande proxyserver te gebruiken:

    * Als u bash gebruikt, moet u de volgende opdracht uitvoeren met de juiste waarden:

        ```bash
        export HTTP_PROXY=<proxy-server-ip-address>:<port>
        export HTTPS_PROXY=<proxy-server-ip-address>:<port>
        export NO_PROXY=<cluster-apiserver-ip-address>:<port>
        ```

    * Als u PowerShell gebruikt, voer dan de volgende opdracht uit met de juiste waarden:

        ```powershell
        $Env:HTTP_PROXY = "<proxy-server-ip-address>:<port>"
        $Env:HTTPS_PROXY = "<proxy-server-ip-address>:<port>"
        $Env:NO_PROXY = "<cluster-apiserver-ip-address>:<port>"
        ```

2. Voer de opdracht connect uit met de opgegeven proxyparameters:

    ```console
    az connectedk8s connect -n <cluster-name> -g <resource-group> --proxy-https https://<proxy-server-ip-address>:<port> --proxy-http http://<proxy-server-ip-address>:<port> --proxy-skip-range <excludedIP>,<excludedCIDR> --proxy-cert <path-to-cert-file>
    ```

> [!NOTE]
> * Geef onder op om ervoor te zorgen dat de communicatie binnen `excludedCIDR` het cluster niet wordt `--proxy-skip-range` verbroken voor de agents.
> * `--proxy-http`, `--proxy-https` en worden verwacht voor de meeste uitgaande `--proxy-skip-range` proxyomgevingen. `--proxy-cert` is *alleen* vereist als u vertrouwde certificaten die door de proxy worden verwacht, moet injecteren in het vertrouwde certificaatopslag van agentpods.

## <a name="view-azure-arc-agents-for-kubernetes"></a>De Azure Arc voor Kubernetes weergeven

Azure Arc kubernetes ingeschakeld implementeert een aantal operators in de `azure-arc` naamruimte. 

1. Bekijk deze implementaties en pods met behulp van:

    ```console
    kubectl -n azure-arc get deployments,pods
    ```

1. Controleer of alle pods een status `Running` hebben.

    ```output
    NAME                                        READY      UP-TO-DATE  AVAILABLE  AGE
    deployment.apps/cluster-metadata-operator     1/1             1        1      16h
    deployment.apps/clusteridentityoperator       1/1             1        1      16h
    deployment.apps/config-agent                  1/1             1        1      16h
    deployment.apps/controller-manager            1/1             1        1      16h
    deployment.apps/flux-logs-agent               1/1             1        1      16h
    deployment.apps/metrics-agent                 1/1             1        1      16h
    deployment.apps/resource-sync-agent           1/1             1        1      16h

    NAME                                           READY    STATUS   RESTART AGE
    pod/cluster-metadata-operator-7fb54d9986-g785b  2/2     Running  0       16h
    pod/clusteridentityoperator-6d6678ffd4-tx8hr    3/3     Running  0       16h
    pod/config-agent-544c4669f9-4th92               3/3     Running  0       16h
    pod/controller-manager-fddf5c766-ftd96          3/3     Running  0       16h
    pod/flux-logs-agent-7c489f57f4-mwqqv            2/2     Running  0       16h
    pod/metrics-agent-58b765c8db-n5l7k              2/2     Running  0       16h
    pod/resource-sync-agent-5cf85976c7-522p5        3/3     Running  0       16h
    ```

## <a name="clean-up-resources"></a>Resources opschonen

U kunt de Azure Arc Kubernetes-resource, alle bijbehorende  configuratieresources en eventuele agents die worden uitgevoerd op het cluster verwijderen met behulp van Azure CLI met behulp van de volgende opdracht:

```azurecli
az connectedk8s delete --name AzureArcTest1 --resource-group AzureArcTest
```

>[!NOTE]
>Als u de Azure Arc Kubernetes-resource verwijdert met behulp van Azure Portal, worden  alle bijbehorende configuratieresources verwijderd, maar worden er geen agents verwijderd die op het cluster worden uitgevoerd. U kunt het beste de kubernetes Azure Arc resource verwijderen met behulp van `az connectedk8s delete` in plaats van Azure Portal.

## <a name="next-steps"></a>Volgende stappen

Ga naar het volgende artikel voor meer informatie over het implementeren van configuraties in uw verbonden Kubernetes-cluster met behulp van GitOps.
> [!div class="nextstepaction"]
> [Configuraties implementeren met Gitops](tutorial-use-gitops-connected-cluster.md)
