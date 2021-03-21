---
title: 'Quickstart: Een AKS-cluster implementeren met behulp van de Azure CLI'
description: Ontdek hoe u snel een Kubernetes-cluster kunt maken, een toepassing kunt implementeren en de prestaties in Azure Kubernetes Service (AKS) kunt bewaken met behulp van de Azure CLI.
services: container-service
ms.topic: quickstart
ms.date: 02/26/2021
ms.custom:
- H1Hack27Feb2017
- mvc
- devcenter
- seo-javascript-september2019
- seo-javascript-october2019
- seo-python-october2019
- devx-track-azurecli
- contperf-fy21q1
ms.openlocfilehash: b3d6c7695f74c048cb03e3f4e7ae822005c81c06
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103492877"
---
# <a name="quickstart-deploy-an-azure-kubernetes-service-cluster-using-the-azure-cli"></a>Quickstart: Implementeer een Azure Kubernetes Service-cluster met behulp van de Azure CLI.

Azure Kubernetes Service (AKS) is een beheerde Kubernetes-service waarmee u snel clusters kunt implementeren en beheren. In deze quickstart gaat u het volgende doen:
* Implementeer een AKS-cluster met behulp van de Azure CLI. 
* Voer een toepassing met meerdere containers uit met een web-front-end en een redis-exemplaar in het cluster. 
* Bewaak de status van het cluster en de peuling waarmee uw toepassing wordt uitgevoerd.

  ![Stem-app is geïmplementeerd in Azure Kubernetes Service](./media/container-service-kubernetes-walkthrough/voting-app-deployed-in-azure-kubernetes-service.png)

In deze snelstart wordt ervan uitgegaan dat u een basisbegrip hebt van Kubernetes-concepten. Zie [Kubernetes-kernconcepten voor Azure Kubernetes Service (AKS)][kubernetes-concepts] voor meer informatie.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Zie [Een AKS-cluster maken dat ondersteuning biedt voor Windows Server-containers][windows-container-cli] voor meer informatie over het maken van een Windows Server-knooppuntgroep.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Voor dit artikel is versie 2.0.64 of hoger van de Azure CLI vereist. Als u Azure Cloud Shell gebruikt, is de nieuwste versie al geïnstalleerd.

> [!NOTE]
> Voer de opdrachten als beheerder uit als u van plan bent om de opdrachten in deze Snelstartgids lokaal uit te voeren in plaats van in Azure Cloud Shell.

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Een [Azure-resourcegroep](../azure-resource-manager/management/overview.md) is een logische groep waarin Azure-resources worden geïmplementeerd en beheerd. Wanneer u een resource groep maakt, wordt u gevraagd een locatie op te geven. Deze locatie is: 
* De opslag locatie van de meta gegevens van de resource groep.
* Waar uw resources worden uitgevoerd in azure als u geen andere regio opgeeft tijdens het maken van resources. 

In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroup* gemaakt op de locatie *VS - oost*.

Maak een resourcegroep met de opdracht [az group create][az-group-create].


```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Uitvoer voor geslaagde resource groep:

```json
{
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup",
  "location": "eastus",
  "managedBy": null,
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## <a name="enable-cluster-monitoring"></a>Cluster bewaking inschakelen

1. Controleer of *micro soft. OperationsManagement* en *micro soft. OperationalInsights* zijn geregistreerd bij uw abonnement. De registratiestatus controleren:

    ```azurecli
    az provider show -n Microsoft.OperationsManagement -o table
    az provider show -n Microsoft.OperationalInsights -o table
    ```
 
    Als ze niet zijn geregistreerd, registreert u *micro soft. OperationsManagement* en *micro soft. OperationalInsights* met:
 
    ```azurecli
    az provider register --namespace Microsoft.OperationsManagement
    az provider register --namespace Microsoft.OperationalInsights
    ```

2. Schakel [Azure monitor in voor containers][azure-monitor-containers] met behulp van de *bewakings parameter--enable-invoeg toepassingen* . 

## <a name="create-aks-cluster"></a>AKS-cluster maken

Maak een AKS-cluster met behulp van de opdracht [AZ AKS Create][az-aks-create] . In het volgende voor beeld wordt een cluster met de naam *myAKSCluster* gemaakt met één knoop punt: 

```azurecli-interactive
az aks create --resource-group myResourceGroup --name myAKSCluster --node-count 1 --enable-addons monitoring --generate-ssh-keys
```

Na enkele minuten is de opdracht voltooid en retourneert deze informatie over het cluster in JSON-indeling.

> [!NOTE]
> Wanneer u een AKS-cluster maakt, wordt automatisch een tweede resource groep gemaakt voor het opslaan van de AKS-resources. Zie voor meer informatie [Waarom worden er twee resourcegroepen gemaakt met AKS?](./faq.md#why-are-two-resource-groups-created-with-aks)

## <a name="connect-to-the-cluster"></a>Verbinding maken met het cluster

Als u een Kubernetes-cluster wilt beheren, gebruikt u de Kubernetes-opdracht regel-client, [kubectl][kubectl]. `kubectl` is al geïnstalleerd als u Azure Cloud Shell gebruikt. 

1. `kubectl`Lokaal installeren met behulp van de opdracht [AZ AKS install-cli][az-aks-install-cli] :

    ```azurecli
    az aks install-cli
    ```

2. Configureer `kubectl` om verbinding te maken met uw Kubernetes-cluster met behulp van de opdracht [AZ AKS Get-credentials][az-aks-get-credentials] . De volgende opdracht:  
      * Hiermee worden referenties gedownload en wordt de Kubernetes CLI geconfigureerd om ze te gebruiken.
      * Gebruikt `~/.kube/config` , de standaard locatie voor het [configuratie bestand Kubernetes][kubeconfig-file]. Geef een andere locatie voor het Kubernetes-configuratie bestand op met behulp van *--File*.


    ```azurecli-interactive
    az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
    ```

3. Controleer de verbinding met uw cluster met behulp van de opdracht [kubectl Get][kubectl-get] . Met deze opdracht wordt een lijst van de cluster knooppunten geretourneerd.

    ```azurecli-interactive
    kubectl get nodes
    ```

    In de uitvoer ziet u het enkele knoop punt dat u in de vorige stappen hebt gemaakt. Zorg ervoor dat de knooppunt status *gereed* is:

    ```output
    NAME                       STATUS   ROLES   AGE     VERSION
    aks-nodepool1-31718369-0   Ready    agent   6m44s   v1.12.8
    ```

## <a name="run-the-application"></a>De toepassing uitvoeren

In een [Kubernetes-manifest bestand][kubernetes-deployment] wordt de gewenste status van een cluster gedefinieerd, zoals welke container installatie kopieën moeten worden uitgevoerd. 

In deze Snelstartgids gebruikt u een manifest om alle objecten te maken die nodig zijn om de [Azure stem-toepassing][azure-vote-app]uit te voeren. Dit manifest bevat twee [Kubernetes-implementaties][kubernetes-deployment]:
* Het voor beeld van Azure stem python-toepassingen.
* Een redis-exemplaar. 

Er worden ook twee [Kubernetes-Services][kubernetes-service] gemaakt:
* Een interne service voor het redis-exemplaar.
* Een externe service voor toegang tot de Azure stem-toepassing via internet.

1. Maak een bestand met de naam `azure-vote.yaml`.
    * Als u de Azure Cloud Shell gebruikt, kan dit bestand worden gemaakt met `code` , `vi` , of `nano` alsof het werkt op een virtueel of fysiek systeem
1. Kopiëren in de volgende YAML definitie:

    ```yaml
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: azure-vote-back
    spec:
      replicas: 1
      selector:
        matchLabels:
          app: azure-vote-back
      template:
        metadata:
          labels:
            app: azure-vote-back
        spec:
          nodeSelector:
            "beta.kubernetes.io/os": linux
          containers:
          - name: azure-vote-back
            image: mcr.microsoft.com/oss/bitnami/redis:6.0.8
            env:
            - name: ALLOW_EMPTY_PASSWORD
              value: "yes"
            resources:
              requests:
                cpu: 100m
                memory: 128Mi
              limits:
                cpu: 250m
                memory: 256Mi
            ports:
            - containerPort: 6379
              name: redis
    ---
    apiVersion: v1
    kind: Service
    metadata:
      name: azure-vote-back
    spec:
      ports:
      - port: 6379
      selector:
        app: azure-vote-back
    ---
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: azure-vote-front
    spec:
      replicas: 1
      selector:
        matchLabels:
          app: azure-vote-front
      template:
        metadata:
          labels:
            app: azure-vote-front
        spec:
          nodeSelector:
            "beta.kubernetes.io/os": linux
          containers:
          - name: azure-vote-front
            image: mcr.microsoft.com/azuredocs/azure-vote-front:v1
            resources:
              requests:
                cpu: 100m
                memory: 128Mi
              limits:
                cpu: 250m
                memory: 256Mi
            ports:
            - containerPort: 80
            env:
            - name: REDIS
              value: "azure-vote-back"
    ---
    apiVersion: v1
    kind: Service
    metadata:
      name: azure-vote-front
    spec:
      type: LoadBalancer
      ports:
      - port: 80
      selector:
        app: azure-vote-front
    ```

1. Implementeer de toepassing met de opdracht [kubectl apply][kubectl-apply] en geef de naam op van uw YAML-manifest:

    ```console
    kubectl apply -f azure-vote.yaml
    ```

    Uitvoer toont de geslaagde implementaties en services:

    ```output
    deployment "azure-vote-back" created
    service "azure-vote-back" created
    deployment "azure-vote-front" created
    service "azure-vote-front" created
    ```

## <a name="test-the-application"></a>De toepassing testen

Wanneer de toepassing wordt uitgevoerd, maakt een Kubernetes-service de front-end van de toepassing beschikbaar op internet. Dit proces kan enkele minuten duren.

Bewaak de voortgang met de opdracht [kubectl Get service][kubectl-get] met het `--watch` argument.

```azurecli-interactive
kubectl get service azure-vote-front --watch
```

De **externe-IP** -uitvoer voor de `azure-vote-front` service wordt in eerste instantie weer gegeven als *in behandeling*.

```output
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

Zodra het **externe IP** -adres is gewijzigd van *in behandeling* in een echt openbaar IP-adres, gebruikt `CTRL-C` u om het `kubectl` controle proces te stoppen. In de volgende voorbeelduitvoer ziet u een geldig openbaar IP-adres dat aan de service is toegewezen:

```output
azure-vote-front   LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

Open een webbrowser naar het externe IP-adres van uw service om de Azure Vote-app te zien.

![Stem-app is geïmplementeerd in Azure Kubernetes Service](./media/container-service-kubernetes-walkthrough/voting-app-deployed-in-azure-kubernetes-service.png)

Bekijk de metrische gegevens voor de status van de cluster knooppunten die zijn vastgelegd door [Azure monitor voor containers][azure-monitor-containers] in de Azure Portal. 

## <a name="delete-the-cluster"></a>Het cluster verwijderen

Opschonen van uw overbodige resources om Azure-kosten te voor komen. Gebruik de opdracht [az group delete][az-group-delete] om de resourcegroep, de containerservice en alle gerelateerde resources te verwijderen.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

> [!NOTE]
> Wanneer u het cluster verwijdert, wordt de Azure Active Directory-service-principal die door het AKS-cluster wordt gebruikt niet verwijderd. Zie [Overwegingen voor en verwijdering van AKS service-principal][sp-delete] voor stappen voor het verwijderen van de service-principal.
> 
> Als u een beheerde identiteit hebt gebruikt, wordt de identiteit beheerd door het platform en hoeft deze niet te worden verwijderd.

## <a name="get-the-code"></a>Code ophalen

Er zijn al bestaande container installatie kopieën gebruikt in deze Quick Start om een Kubernetes-implementatie te maken. Het manifest bestand gerelateerde toepassings code, Dockerfile en Kubernetes is [beschikbaar op github.][azure-vote-app]

## <a name="next-steps"></a>Volgende stappen

In deze Snelstartgids hebt u een Kubernetes-cluster geïmplementeerd en vervolgens een toepassing met meerdere containers geïmplementeerd. [Open het Kubernetes-webdashboard][kubernetes-dashboard] voor uw AKS-cluster.

Voor meer informatie over AKS en een volledig stapsgewijs voorbeeld van code tot implementatie gaat u naar de zelfstudie over Kubernetes-clusters.

> [!div class="nextstepaction"]
> [AKS-zelfstudie][aks-tutorial]

<!-- LINKS - external -->
[azure-vote-app]: https://github.com/Azure-Samples/azure-voting-app-redis.git
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubeconfig-file]: https://kubernetes.io/docs/concepts/configuration/organize-cluster-access-kubeconfig/

<!-- LINKS - internal -->
[kubernetes-concepts]: concepts-clusters-workloads.md
[aks-monitor]: ../azure-monitor/containers/container-insights-onboard.md
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[az-aks-browse]: /cli/azure/aks#az-aks-browse
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-aks-install-cli]: /cli/azure/aks#az-aks-install-cli
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[azure-cli-install]: /cli/azure/install-azure-cli
[azure-monitor-containers]: ../azure-monitor/containers/container-insights-overview.md
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[azure-portal]: https://portal.azure.com
[kubernetes-deployment]: concepts-clusters-workloads.md#deployments-and-yaml-manifests
[kubernetes-service]: concepts-network.md#services
[kubernetes-dashboard]: kubernetes-dashboard.md
[windows-container-cli]: windows-container-cli.md