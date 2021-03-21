---
title: 'Quick Start: een bestaand Kubernetes-cluster verbinden met Azure Arc'
description: In deze Quick Start leert u hoe u verbinding maakt met een Azure Arc enabled Kubernetes-cluster.
author: mgoedtel
ms.author: magoedte
ms.service: azure-arc
ms.topic: quickstart
ms.date: 03/03/2021
ms.custom: template-quickstart
keywords: Kubernetes, Arc, azure, cluster
ms.openlocfilehash: 3fc522c4bdda9eb1047d5258bcc431d0268990b9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102121640"
---
# <a name="quickstart-connect-an-existing-kubernetes-cluster-to-azure-arc"></a>Quick Start: een bestaand Kubernetes-cluster verbinden met Azure Arc 

In deze Quick start gaan we profiteren van de voor delen van Azure Arc enabled Kubernetes en verbinding maken met een bestaand Kubernetes-cluster met Azure Arc. Voor een conceptueel overzicht van het verbinden van clusters met Azure Arc raadpleegt u het [artikel Azure Arc enabled Kubernetes agent Architecture](./conceptual-agent-architecture.md).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

* Controleer of u het volgende hebt:
    * Een up-en-actief Kubernetes-cluster.
    * Een `kubeconfig` bestand dat verwijst naar het cluster dat u wilt verbinden met Azure Arc.
    * Lees-en schrijf machtigingen voor de gebruiker of Service-Principal waarmee verbinding wordt gemaakt met het resource type Azure Arc enabled Kubernetes ( `Microsoft.Kubernetes/connectedClusters` ).
* Installeer de [nieuwste versie van helm 3](https://helm.sh/docs/intro/install).
* Installeer de volgende Kubernetes CLI-uitbrei dingen van Azure Arc enabled van versie >= 1.0.0:
  
  ```azurecli
  az extension add --name connectedk8s
  az extension add --name k8s-configuration
  ```
  * Als u deze uitbrei dingen wilt bijwerken naar de nieuwste versie, voert u de volgende opdrachten uit:
  
  ```azurecli
  az extension update --name connectedk8s
  az extension update --name k8s-configuration
  ```

>[!NOTE]
>**Ondersteunde regio's:**
>* VS - oost
>* Europa -west
>* VS - west-centraal
>* VS - zuid-centraal
>* Azië - zuidoost
>* Verenigd Koninkrijk Zuid
>* VS - west 2
>* Australië - oost
>* VS - oost 2
>* Europa - noord

## <a name="meet-network-requirements"></a>Voldoen aan de netwerk vereisten

>[!IMPORTANT]
>Voor Azure Arc-agents zijn de volgende protocollen/poorten/uitgaande Url's vereist voor de functie:
>* TCP op poort 443: `https://:443`
>* TCP op poort 9418: `git://:9418`
  
| Eind punt (DNS) | Beschrijving |  
| ----------------- | ------------- |  
| `https://management.azure.com`                                                                                 | Vereist voor de agent om verbinding te maken met Azure en het cluster te registreren.                                                        |  
| `https://eastus.dp.kubernetesconfiguration.azure.com`, `https://westeurope.dp.kubernetesconfiguration.azure.com`, `https://westcentralus.dp.kubernetesconfiguration.azure.com`, `https://southcentralus.dp.kubernetesconfiguration.azure.com`, `https://southeastasia.dp.kubernetesconfiguration.azure.com`, `https://uksouth.dp.kubernetesconfiguration.azure.com`, `https://westus2.dp.kubernetesconfiguration.azure.com`, `https://australiaeast.dp.kubernetesconfiguration.azure.com`, `https://eastus2.dp.kubernetesconfiguration.azure.com`, `https://northeurope.dp.kubernetesconfiguration.azure.com` | Gegevens vlak eindpunt voor de agent om de status te pushen en configuratie gegevens op te halen.                                      |  
| `https://login.microsoftonline.com`                                                                            | Vereist om Azure Resource Manager-tokens op te halen en bij te werken.                                                                                    |  
| `https://mcr.microsoft.com`                                                                            | Vereist voor het ophalen van container installatie kopieën voor Azure Arc-agents.                                                                  |  
| `https://eus.his.arc.azure.com`, `https://weu.his.arc.azure.com`, `https://wcus.his.arc.azure.com`, `https://scus.his.arc.azure.com`, `https://sea.his.arc.azure.com`, `https://uks.his.arc.azure.com`, `https://wus2.his.arc.azure.com`, `https://ae.his.arc.azure.com`, `https://eus2.his.arc.azure.com`, `https://ne.his.arc.azure.com` |  Vereist voor het ophalen van door het systeem toegewezen Managed Service Identity-certificaten (MSI).                                                                  |

## <a name="register-the-two-providers-for-azure-arc-enabled-kubernetes"></a>Registreer de twee providers voor Azure Arc enabled Kubernetes

1. Voer de volgende opdrachten in:
    ```azurecli
    az provider register --namespace Microsoft.Kubernetes
    az provider register --namespace Microsoft.KubernetesConfiguration
    ```
2. Het registratie proces bewaken. Het kan tot tien minuten duren voordat de registratie is uitgevoerd.
    ```azurecli
    az provider show -n Microsoft.Kubernetes -o table
    az provider show -n Microsoft.KubernetesConfiguration -o table    
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

## <a name="connect-an-existing-kubernetes-cluster"></a>Verbinding maken met een bestaand Kubernetes-cluster

1. Verbind uw Kubernetes-cluster met Azure Arc met de volgende opdracht:
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
> De bovenstaande opdracht zonder de opgegeven locatie parameter maakt de Azure-Kubernetes-resource op dezelfde locatie als de resource groep. Als u de Azure-Kubernetes-resource wilt maken op een andere locatie, geeft u `--location <region>` of op `-l <region>` Wanneer u de `az connectedk8s connect` opdracht uitvoert.

## <a name="verify-cluster-connection"></a>Cluster verbinding controleren

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
> Na de onboarding van het cluster duurt het ongeveer vijf tot tien minuten voor de meta gegevens van het cluster (cluster versie, agent versie, aantal knoop punten, enzovoort) tot het Opper vlak op de overzichts pagina van de Azure Arc enabled Kubernetes-resource in Azure Portal.

## <a name="connect-using-an-outbound-proxy-server"></a>Verbinding maken via een uitgaande proxy server

Als uw cluster zich achter een uitgaande proxy server bevindt, moeten Azure CLI en de Azure Arc enabled Kubernetes-agents hun aanvragen via de uitgaande proxy server routeren. 


1. Stel de omgevings variabelen in die nodig zijn voor Azure CLI voor het gebruik van de uitgaande proxy server:

    * Als u bash gebruikt, voert u de volgende opdracht uit met de juiste waarden:

        ```bash
        export HTTP_PROXY=<proxy-server-ip-address>:<port>
        export HTTPS_PROXY=<proxy-server-ip-address>:<port>
        export NO_PROXY=<cluster-apiserver-ip-address>:<port>
        ```

    * Als u Power shell gebruikt, voert u de volgende opdracht uit met de juiste waarden:

        ```powershell
        $Env:HTTP_PROXY = "<proxy-server-ip-address>:<port>"
        $Env:HTTPS_PROXY = "<proxy-server-ip-address>:<port>"
        $Env:NO_PROXY = "<cluster-apiserver-ip-address>:<port>"
        ```

2. Voer de opdracht Connect uit met de opgegeven proxy parameters:

    ```console
    az connectedk8s connect -n <cluster-name> -g <resource-group> --proxy-https https://<proxy-server-ip-address>:<port> --proxy-http http://<proxy-server-ip-address>:<port> --proxy-skip-range <excludedIP>,<excludedCIDR> --proxy-cert <path-to-cert-file>
    ```

> [!NOTE]
> * Geef `excludedCIDR` onder `--proxy-skip-range` op om te zorgen dat de communicatie in het cluster niet wordt verbroken voor de agents.
> * `--proxy-http`, `--proxy-https` , en `--proxy-skip-range` worden verwacht voor de meeste uitgaande proxy omgevingen. `--proxy-cert` is *alleen* vereist als u in het vertrouwde certificaat archief van de gehele agent vertrouwde certificaten moet invoeren die door de proxy worden verwacht.

## <a name="view-azure-arc-agents-for-kubernetes"></a>Azure Arc-agents weer geven voor Kubernetes

Azure Arc enabled Kubernetes implementeert enkele opera tors in de `azure-arc` naam ruimte. 

1. Bekijk deze implementaties en peulen met:

    ```console
    kubectl -n azure-arc get deployments,pods
    ```

1. Controleer of alle peulen een `Running` status hebben.

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

Met de volgende opdracht kunt u de Azure-Kubernetes-resource, eventuele gekoppelde configuratie resources *en* alle agents die op het cluster worden uitgevoerd, verwijderen met behulp van Azure cli:

```azurecli
az connectedk8s delete --name AzureArcTest1 --resource-group AzureArcTest
```

>[!NOTE]
>Als u de Azure-Kubernetes-resource met behulp van Azure Portal verwijdert, worden alle bijbehorende configuratie resources verwijderd, maar worden de agents die op het cluster worden uitgevoerd, *niet* verwijderd. U kunt het beste de Azure Arc-Kubernetes-resource verwijderen met `az connectedk8s delete` in plaats van Azure Portal.

## <a name="next-steps"></a>Volgende stappen

Ga naar het volgende artikel voor meer informatie over het implementeren van configuraties in uw verbonden Kubernetes-cluster met behulp van GitOps.
> [!div class="nextstepaction"]
> [Configuraties implementeren met behulp van Gitops](tutorial-use-gitops-connected-cluster.md)
