---
title: Een verbinding instellen voor een Kubernetes-cluster met Azure Arc (preview)
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: Een Azure Arc-Kubernetes-cluster verbinden met Azure Arc
keywords: Kubernetes, Arc, azure, K8s, containers
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: b4ab84153eaaf81c668d8589fec7516853aca5f9
ms.sourcegitcommit: 49ea056bbb5957b5443f035d28c1d8f84f5a407b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/09/2021
ms.locfileid: "100008108"
---
# <a name="connect-an-azure-arc-enabled-kubernetes-cluster-preview"></a>Een verbinding instellen voor een Kubernetes-cluster met Azure Arc (preview)

In dit artikel wordt het proces beschreven voor het verbinden van een CNCF-Kubernetes-cluster (native computing Foundation) van de Cloud, zoals AKS-engine op Azure, AKS-engine op Azure Stack hub, GKE, EKS en VMware vSphere cluster naar Azure Arc.

## <a name="before-you-begin"></a>Voordat u begint

Controleer of u de volgende vereisten hebt voor bereid:

* Een up-en-actief Kubernetes-cluster. Als u geen bestaand Kubernetes-cluster hebt, kunt u een van de volgende hand leidingen gebruiken om een test cluster te maken:
  * Maak een Kubernetes-cluster met behulp [van Kubernetes in docker (kind)](https://kind.sigs.k8s.io/).
  * Maak een Kubernetes-cluster met behulp van docker voor [Mac](https://docs.docker.com/docker-for-mac/#kubernetes) of [Windows](https://docs.docker.com/docker-for-windows/#kubernetes).
* Een kubeconfig-bestand voor toegang tot de rol cluster en Cluster beheerder op het cluster voor de implementatie van Kubernetes-agents met Arc-functionaliteit.
* De gebruiker of service-principal die wordt gebruikt met `az login` en `az connectedk8s connect` opdrachten moeten de machtigingen lezen en schrijven hebben voor het resource type micro soft. Kubernetes/connectedclusters. De rol ' Kubernetes cluster-Azure Arc-onboarding ' heeft deze machtigingen en kan worden gebruikt voor roltoewijzingen op de gebruiker of Service-Principal.
* Helm 3 voor de onboarding van het cluster met behulp van een connectedk8s-extensie. [Installeer de nieuwste versie van helm 3](https://helm.sh/docs/intro/install) om te voldoen aan deze vereiste.
* Azure CLI-versie 2.15 + voor het installeren van de Kubernetes CLI-uitbrei dingen voor Azure Arc ingeschakeld. [Installeer Azure cli](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) of werk bij naar de nieuwste versie.
* Installeer de met Arc ingeschakelde Kubernetes CLI-extensies:
  
  * Installeer de `connectedk8s` extensie, waarmee u Kubernetes-clusters kunt verbinden met Azure:
  
  ```azurecli
  az extension add --name connectedk8s
  ```
  
   * Installeer de `k8sconfiguration` extensie:
  
  ```azurecli
  az extension add --name k8sconfiguration
  ```

  * Als u deze uitbrei dingen later wilt bijwerken, voert u de volgende opdrachten uit:
  
  ```azurecli
  az extension update --name connectedk8s
  az extension update --name k8sconfiguration
  ```

## <a name="supported-regions"></a>Ondersteunde regio’s

* VS - oost
* Europa -west

## <a name="network-requirements"></a>Netwerkvereisten

Voor Azure Arc-agents zijn de volgende protocollen/poorten/uitgaande Url's vereist voor de functie:

* TCP op poort 443: `https://:443`
* TCP op poort 9418: `git://:9418`

| Eind punt (DNS)                                                                                               | Description                                                                                                                 |
| ------------------------------------------------------------------------------------------------------------ | --------------------------------------------------------------------------------------------------------------------------- |
| `https://management.azure.com`                                                                                 | Vereist voor de agent om verbinding te maken met Azure en het cluster te registreren.                                                        |
| `https://eastus.dp.kubernetesconfiguration.azure.com`, `https://westeurope.dp.kubernetesconfiguration.azure.com` | Gegevens vlak eindpunt voor de agent om de status te pushen en configuratie gegevens op te halen.                                      |
| `https://login.microsoftonline.com`                                                                            | Vereist om Azure Resource Manager-tokens op te halen en bij te werken.                                                                                    |
| `https://mcr.microsoft.com`                                                                            | Vereist voor het ophalen van container installatie kopieën voor Azure Arc-agents.                                                                  |
| `https://eus.his.arc.azure.com`, `https://weu.his.arc.azure.com`                                                                            |  Vereist voor het ophalen van door het systeem toegewezen beheerde identiteits certificaten.                                                                  |

## <a name="register-the-two-providers-for-azure-arc-enabled-kubernetes"></a>Registreer de twee providers voor Azure Arc-enabled Kubernetes:

```console
az provider register --namespace Microsoft.Kubernetes

az provider register --namespace Microsoft.KubernetesConfiguration
```

Registratie is een asynchroon proces en kan ongeveer 10 minuten duren. U kunt het registratie proces bewaken met de volgende opdrachten:

```console
az provider show -n Microsoft.Kubernetes -o table
```

```console
az provider show -n Microsoft.KubernetesConfiguration -o table
```

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Gebruik een resource groep om meta gegevens voor uw cluster op te slaan.

Maak eerst een resource groep om de verbonden cluster bron te bewaren.

```console
az group create --name AzureArcTest -l EastUS -o table
```

**Uitvoer**

```console
Location    Name
----------  ------------
eastus      AzureArcTest
```

## <a name="connect-a-cluster"></a>Verbinding maken met een cluster

We gaan ons Kubernetes-cluster nu koppelen aan Azure met `az connectedk8s connect` :

1. Controleer de verbinding met uw Kubernetes-cluster via een van de volgende opties:
   1. `KUBECONFIG`
   1. `~/.kube/config`
   1. `--kube-config`
1. Implementeer Azure Arc-agents voor Kubernetes met behulp van helm 3 in de `azure-arc` naam ruimte:

```console
az connectedk8s connect --name AzureArcTest1 --resource-group AzureArcTest
```

**Uitvoer**

```console
Command group 'connectedk8s' is in preview. It may be changed/removed in a future release.
Helm release deployment succeeded

{
  "aadProfile": {
    "clientAppId": "",
    "serverAppId": "",
    "tenantId": ""
  },
  "agentPublicKeyCertificate": "...",
  "agentVersion": "0.1.0",
  "id": "/subscriptions/57ac26cf-a9f0-4908-b300-9a4e9a0fb205/resourceGroups/AzureArcTest/providers/Microsoft.Kubernetes/connectedClusters/AzureArcTest1",
  "identity": {
    "principalId": null,
    "tenantId": null,
    "type": "None"
  },
  "kubernetesVersion": "v1.15.0",
  "location": "eastus",
  "name": "AzureArcTest1",
  "resourceGroup": "AzureArcTest",
  "tags": {},
  "totalNodeCount": 1,
  "type": "Microsoft.Kubernetes/connectedClusters"
}
```

## <a name="verify-connected-cluster"></a>Verbonden cluster verifiëren

Gebruik de volgende opdracht om uw verbonden clusters weer te geven:

```console
az connectedk8s list -g AzureArcTest -o table
```

**Uitvoer**

```console
Command group 'connectedk8s' is in preview. It may be changed/removed in a future release.
Name           Location    ResourceGroup
-------------  ----------  ---------------
AzureArcTest1  eastus      AzureArcTest
```

U kunt deze resource ook bekijken op de [Azure Portal](https://portal.azure.com/). Open de portal in uw browser en navigeer naar de resource groep en de Azure Arc-Kubernetes-resource, op basis van de resource naam en de naam invoer van de resource groep die eerder in de opdracht zijn gebruikt `az connectedk8s connect` .

> [!NOTE]
> Na de onboarding van het cluster duurt het ongeveer vijf tot tien minuten voor de meta gegevens van het cluster (cluster versie, agent versie, aantal knoop punten enz.) naar het Opper vlak op de pagina overzicht van de Azure Arc-Kubernetes-resource in Azure Portal.

## <a name="connect-using-an-outbound-proxy-server"></a>Verbinding maken via een uitgaande proxy server

Als uw cluster zich achter een uitgaande proxy server bevindt, moeten Azure CLI en de Kubernetes-agents van Arc-enabled hun aanvragen via de uitgaande proxy server routeren:

1. Controleer de versie van de `connectedk8s` uitbrei ding die op uw computer is geïnstalleerd:

    ```console
    az -v
    ```

    U hebt `connectedk8s` extensie versie 0.2.5 + nodig om agents met een uitgaande proxy in te stellen. Als u versie 0.2.3 of ouder op uw computer hebt, volgt u de stappen voor het [bijwerken](#before-you-begin) om de meest recente versie van de uitbrei ding op uw computer op te halen.

2. Stel de omgevings variabelen in die nodig zijn voor Azure CLI voor het gebruik van de uitgaande proxy server:

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

3. Voer de opdracht Connect uit met de opgegeven proxy parameters:

    ```console
    az connectedk8s connect -n <cluster-name> -g <resource-group> --proxy-https https://<proxy-server-ip-address>:<port> --proxy-http http://<proxy-server-ip-address>:<port> --proxy-skip-range <excludedIP>,<excludedCIDR> --proxy-cert <path-to-cert-file>
    ```

> [!NOTE]
> 1. Opgeven `excludedCIDR` onder `--proxy-skip-range` is belang rijk om ervoor te zorgen dat de communicatie in het cluster niet wordt verbroken voor de agents.
> 2. Hoewel `--proxy-http` , `--proxy-https` , en `--proxy-skip-range` worden verwacht voor de meeste uitgaande proxy omgevingen, `--proxy-cert` is alleen vereist als vertrouwde certificaten van de proxy moeten worden toegevoegd aan het vertrouwde certificaat archief van de gehele agent.
> 3. De bovenstaande proxy specificatie wordt momenteel alleen toegepast voor Arc-agents en niet voor de stroom die wordt gebruikt in sourceControlConfiguration. Het Kubernetes-team met Arc-functionaliteit werkt actief op deze functie en is binnenkort beschikbaar.

## <a name="azure-arc-agents-for-kubernetes"></a>Azure Arc-agents voor Kubernetes

Azure Arc-enabled Kubernetes implementeert enkele opera tors in de `azure-arc` naam ruimte. U kunt deze implementaties en peulen weer geven met behulp van:

```console
kubectl -n azure-arc get deployments,pods
```

**Uitvoer**

```console
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

Azure Arc-Kubernetes bestaat uit een aantal agents (opera tors) die in uw cluster worden uitgevoerd en die zijn geïmplementeerd in de `azure-arc` naam ruimte.

| Agents (opera tors)                                                                                               | Description                                                                                                                 |
| ------------------------------------------------------------------------------------------------------------ | --------------------------------------------------------------------------------------------------------------------------- |
| `deployment.apps/config-agent`                                                                                 | Hiermee wordt het verbonden cluster gewatcheerd voor configuratie bronnen voor broncode beheer die zijn toegepast op het cluster en de nalevings status van updates.                                                        |
| `deployment.apps/controller-manager` | Een operator van Opera tors die de interacties tussen onderdelen van Azure Arc indeelt.                                      |
| `deployment.apps/metrics-agent`                                                                            | Verzamelt prestatie gegevens over andere Arc-agents.                                                                                    |
| `deployment.apps/cluster-metadata-operator`                                                                            | Verzamelt de meta gegevens van het cluster, zoals de Cluster versie, het aantal knoop punten en de versie van de Azure Arc-agent.                                                                  |
| `deployment.apps/resource-sync-agent`                                                                            |  Synchroniseert de hierboven vermelde meta gegevens van het cluster naar Azure.                                                                  |
| `deployment.apps/clusteridentityoperator`                                                                            |  Azure Arc ingeschakeld Kubernetes ondersteunt momenteel door het systeem toegewezen identiteit. `clusteridentityoperator` onderhoudt het MSI-certificaat (Managed Service Identity) dat door andere agents wordt gebruikt voor communicatie met Azure.                                                                  |
| `deployment.apps/flux-logs-agent`                                                                            |  Hiermee worden logboeken van de stroom-Opera tors verzameld die zijn geïmplementeerd als onderdeel van de configuratie van broncode beheer.                                                                  |

## <a name="delete-a-connected-cluster"></a>Een verbonden cluster verwijderen

U kunt een `Microsoft.Kubernetes/connectedcluster` resource verwijderen met behulp van Azure CLI of Azure Portal.


* **Verwijderen met Azure cli**: gebruik de volgende Azure cli-opdracht om het verwijderen van de Kubernetes-resource met Azure-Arc-functionaliteit te initiëren.
  ```console
  az connectedk8s delete --name AzureArcTest1 --resource-group AzureArcTest
  ```
  Met deze opdracht verwijdert u de `Microsoft.Kubernetes/connectedCluster` resource en eventuele gekoppelde `sourcecontrolconfiguration` resources in Azure. De Azure CLI wordt gebruikt `helm uninstall` voor het verwijderen van de agents die op het cluster worden uitgevoerd.

* **Verwijderen op Azure Portal**: als u de Kubernetes-resource van Azure met de Arc hebt verwijderd op Azure Portal, worden de `Microsoft.Kubernetes/connectedcluster` resource en alle bijbehorende `sourcecontrolconfiguration` resources in azure verwijderd, maar worden de agents die op het cluster worden uitgevoerd, *niet* verwijderd. 

  Als u de agents die op het cluster worden uitgevoerd wilt verwijderen, voert u de volgende opdracht uit:

  ```console
  az connectedk8s delete --name AzureArcTest1 --resource-group AzureArcTest
  ```

## <a name="next-steps"></a>Volgende stappen

* [GitOps gebruiken in een verbonden cluster](./use-gitops-connected-cluster.md)
* [Azure Policy gebruiken om de cluster configuratie te bepalen](./use-azure-policy.md)
