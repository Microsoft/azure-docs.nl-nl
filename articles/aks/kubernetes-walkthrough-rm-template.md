---
title: 'Snelstart: een AKS-cluster (Azure Kubernetes Service) maken'
description: Ontdek hoe u snel een Kubernetes-cluster kunt maken met behulp van een Azure Resource Manager-sjabloon en een toepassing kunt implementeren in Azure Kubernetes Service (AKS)
services: container-service
ms.topic: quickstart
ms.date: 03/15/2021
ms.custom: mvc,subject-armqs, devx-track-azurecli
ms.openlocfilehash: 4bcaafdb1f465fb8568078dfb5bfaf988d0cd587
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107764419"
---
# <a name="quickstart-deploy-an-azure-kubernetes-service-aks-cluster-using-an-arm-template"></a>Quickstart: Een AKS-cluster (Azure Kubernetes Service) implementeren met behulp van een ARM-sjabloon

Azure Kubernetes Service (AKS) is een beheerde Kubernetes-service waarmee u snel clusters kunt implementeren en beheren. In deze quickstart gaat u het volgende doen:
* Implementeer een AKS-cluster met behulp van Azure Resource Manager sjabloon. 
* Voer een toepassing met meerdere containers uit met een webfront-end en een Redis-exemplaar in het cluster. 

![Afbeelding van browsen naar Azure Vote](media/container-service-kubernetes-walkthrough/azure-voting-application.png)

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

In deze snelstart wordt ervan uitgegaan dat u een basisbegrip hebt van Kubernetes-concepten. Zie [Kubernetes-kernconcepten voor Azure Kubernetes Service (AKS)][kubernetes-concepts] voor meer informatie.

Als uw omgeving voldoet aan de vereisten en u benkend bent met het gebruik van ARM-sjablonen, selecteert u de knop **Implementeren naar Azure**. De sjabloon wordt in Azure Portal geopend.

[![Implementeren in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-aks%2Fazuredeploy.json)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Voor dit artikel is versie 2.0.61 of hoger van Azure CLI vereist. Als u Azure Cloud Shell gebruikt, is de nieuwste versie al geïnstalleerd.

- Als u een AKS-cluster wilt maken met Resource Manager sjabloon, geeft u een openbare SSH-sleutel op. Als u deze resource nodig hebt, bekijkt u de volgende sectie: ga anders naar de [sectie De sjabloon](#review-the-template) controleren.

### <a name="create-an-ssh-key-pair"></a>Een SSH-sleutelpaar maken

Voor toegang tot AKS-knooppunten maakt u verbinding met behulp van een SSH-sleutelpaar (openbaar en privé), dat u genereert met behulp van de `ssh-keygen` opdracht . Deze bestanden worden standaard gemaakt in de map *~/.ssh*. Als u `ssh-keygen` de opdracht uitwerkt, worden alle SSH-sleutelparen overschreven met dezelfde naam die al op de opgegeven locatie bestaan.

1. Ga naar [https://shell.azure.com](https://shell.azure.com) om Cloud Shell in uw browser te openen.

1. Voer de opdracht `ssh-keygen` uit. In het volgende voorbeeld wordt een SSH-sleutelpaar gemaakt met behulp van RSA-versleuteling en een bitlengte van 2048:

    ```console
    ssh-keygen -t rsa -b 2048
    ```

Zie [SSH-sleutels maken en beheren voor verificatie in Azure][ssh-keys] voor meer informatie over het maken van SSH-sleutels.

## <a name="review-the-template"></a>De sjabloon controleren

De sjabloon die in deze quickstart wordt gebruikt, komt uit [Azure Quick Start-sjablonen](https://azure.microsoft.com/resources/templates/101-aks/).

:::code language="json" source="~/quickstart-templates/101-aks/azuredeploy.json":::

Zie de site [AKS-quickstartsjablonen][aks-quickstart-templates] voor meer AKS-voorbeelden.

## <a name="deploy-the-template"></a>De sjabloon implementeren

1. Selecteer de volgende knop om u aan te melden bij Azure en een sjabloon te openen.

    [![Implementeren in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-aks%2Fazuredeploy.json)

2. Typ of selecteer de volgende waarden.

    Voor deze quickstart behoudt u de standaardwaarden voor *besturingssysteemschijfgrootte (GB)* , *aantal agenten*, *agentgrootte van de virtuele machine*, *type besturingssysteem* en *Kubernetes-versie*. Geef uw eigen waarden op voor de volgende sjabloonparameters:

    * **Abonnement**: Selecteer een Azure-abonnement.
    * **Resourcegroep**: Selecteer **Nieuw maken**. Voer een unieke naam in voor de resourcegroep, zoals *myResourceGroup*, en kies vervolgens **OK**.
    * **Locatie**: Selecteer een locatie, zoals **VS-Oost**.
    * **Clusternaam**: voer een unieke naam in voor het AKS-cluster, zoals *myAKSCluster*.
    * **DNS-voorvoegsel**: voer een uniek DNS-voorvoegsel voor uw cluster in, zoals *myakscluster*.
    * **Linux-gebruikersnaam van beheerder**: voer een gebruikersnaam in om verbinding te maken via SSH, zoals *azureuser*.
    * **Openbare SSH-RSA-sleutel**: kopieer en plak het *openbare* onderdeel van uw SSH-sleutelpaar (standaard de inhoud van *~/.ssh/id_rsa.pub*).

    ![Resource Manager-sjabloon om een Azure Kubernetes Service-cluster te maken in de portal](./media/kubernetes-walkthrough-rm-template/create-aks-cluster-using-template-portal.png)

3. Selecteer **Controleren + maken**.

Het duurt een paar minuten om het AKS-cluster te maken. Wacht totdat het cluster met succes is geïmplementeerd voordat u met de volgende stap verdergaat.

## <a name="validate-the-deployment"></a>De implementatie valideren

### <a name="connect-to-the-cluster"></a>Verbinding maken met het cluster

Als u een Kubernetes-cluster wilt beheren, gebruikt u de Kubernetes-opdrachtregelclient [kubectl][kubectl]. `kubectl` is al geïnstalleerd als u Azure Cloud Shell. 

1. Installeer `kubectl` lokaal met de opdracht az [aks install-cli:][az-aks-install-cli]

    ```azurecli
    az aks install-cli
    ```

2. Configureer `kubectl` om verbinding te maken met uw Kubernetes-cluster met behulp van de opdracht az [aks get-credentials.][az-aks-get-credentials] Bij deze opdracht worden referenties gedownload en wordt Kubernetes CLI geconfigureerd voor het gebruik van deze referenties.

    ```azurecli-interactive
    az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
    ```

3. Controleer de verbinding met uw cluster met behulp van [de opdracht kubectl get.][kubectl-get] Met deze opdracht wordt een lijst met de clusterknooppunten weergegeven.

    ```console
    kubectl get nodes
    ```

    Uitvoer toont de knooppunten die in de vorige stappen zijn gemaakt. Zorg ervoor dat de status van alle knooppunten *Ready* is:

    ```output
    NAME                       STATUS   ROLES   AGE     VERSION
    aks-agentpool-41324942-0   Ready    agent   6m44s   v1.12.6    
    aks-agentpool-41324942-1   Ready    agent   6m46s   v1.12.6
    aks-agentpool-41324942-2   Ready    agent   6m45s   v1.12.6
    ```

### <a name="run-the-application"></a>De toepassing uitvoeren

Een [Kubernetes-manifestbestand][kubernetes-deployment] definieert de gewenste status van een cluster, zoals welke containerafbeeldingen moeten worden uitgevoerd. 

In deze quickstart gebruikt u een manifest om alle objecten te maken die nodig zijn om de [Azure Vote-toepassing uit te voeren.][azure-vote-app] Dit manifest bevat twee [Kubernetes-implementaties:][kubernetes-deployment]
* De Azure Vote Python-voorbeeldtoepassingen.
* Een Redis-exemplaar. 

Er [worden ook twee Kubernetes-services][kubernetes-service] gemaakt:
* Een interne service voor het Redis-exemplaar.
* Een externe service voor toegang tot de Azure Vote-toepassing vanaf internet.

1. Maak een bestand met de naam `azure-vote.yaml`.
    * Als u de Azure Cloud Shell, kan dit bestand worden gemaakt met of alsof het werkt op een `vi` `nano` virtueel of fysiek systeem
1. Kopieer de volgende YAML-definitie:

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

    Uitvoer toont de gemaakte implementaties en services:

    ```output
    deployment "azure-vote-back" created
    service "azure-vote-back" created
    deployment "azure-vote-front" created
    service "azure-vote-front" created
    ```

### <a name="test-the-application"></a>De toepassing testen

Wanneer de toepassing wordt uitgevoerd, maakt een Kubernetes-service de front-end van de toepassing beschikbaar op internet. Dit proces kan enkele minuten duren.

Controleer de voortgang met behulp [van de opdracht kubectl get service][kubectl-get] met het argument `--watch` .

```console
kubectl get service azure-vote-front --watch
```

De **external-IP-uitvoer** voor de `azure-vote-front` service wordt in eerste instantie als in behandeling weer *geven.*

```output
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

Zodra het **EXTERNAL-IP-adres** is gewijzigd van *in behandeling* in een echt openbaar IP-adres, gebruikt u `CTRL-C` om het `kubectl` watch-proces te stoppen. In de volgende voorbeelduitvoer ziet u een geldig openbaar IP-adres dat aan de service is toegewezen:

```output
azure-vote-front   LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

Open een webbrowser naar het externe IP-adres van uw service om de Azure Vote-app te zien.

![Afbeelding van browsen naar Azure Vote](media/container-service-kubernetes-walkthrough/azure-voting-application.png)

## <a name="clean-up-resources"></a>Resources opschonen

Schoon uw overbodige resources op om Azure-kosten te voorkomen. Gebruik de opdracht [az group delete][az-group-delete] om de resourcegroep, de containerservice en alle gerelateerde resources te verwijderen.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

> [!NOTE]
> Wanneer u het cluster verwijdert, wordt de Azure Active Directory-service-principal die door het AKS-cluster wordt gebruikt niet verwijderd. Zie [Overwegingen voor en verwijdering van AKS service-principal][sp-delete] voor stappen voor het verwijderen van de service-principal.
> 
> Als u een beheerde identiteit hebt gebruikt, wordt de identiteit beheerd door het platform en hoeft deze niet te worden verwijderd.

## <a name="get-the-code"></a>Code ophalen

In deze quickstart zijn al bestaande containerafbeeldingen gebruikt om een Kubernetes-implementatie te maken. De gerelateerde toepassingscode, Dockerfile en het Kubernetes-manifestbestand zijn [beschikbaar op GitHub.][azure-vote-app]

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een Kubernetes-cluster geïmplementeerd en vervolgens een toepassing met meerdere containers geïmplementeerd. [Toegang tot het Kubernetes-webdashboard][kubernetes-dashboard] voor uw AKS-cluster.

Voor meer informatie over AKS en een volledig stapsgewijs voorbeeld van code tot implementatie gaat u naar de zelfstudie over Kubernetes-clusters.

> [!div class="nextstepaction"]
> [AKS-zelfstudie][aks-tutorial]

<!-- LINKS - external -->
[azure-vote-app]: https://github.com/Azure-Samples/azure-voting-app-redis.git
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[azure-dev-spaces]: ../dev-spaces/index.yml
[aks-quickstart-templates]: https://azure.microsoft.com/resources/templates/?term=Azure+Kubernetes+Service

<!-- LINKS - internal -->
[kubernetes-concepts]: concepts-clusters-workloads.md
[aks-monitor]: ../azure-monitor/containers/container-insights-onboard.md
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[az-aks-browse]: /cli/azure/aks#az_aks_browse
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[az-aks-install-cli]: /cli/azure/aks#az_aks_install_cli
[az-group-create]: /cli/azure/group#az_group_create
[az-group-delete]: /cli/azure/group#az_group_delete
[azure-cli-install]: /cli/azure/install-azure-cli
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[azure-portal]: https://portal.azure.com
[kubernetes-deployment]: concepts-clusters-workloads.md#deployments-and-yaml-manifests
[kubernetes-service]: concepts-network.md#services
[kubernetes-dashboard]: kubernetes-dashboard.md
[ssh-keys]: ../virtual-machines/linux/create-ssh-keys-detailed.md
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az_ad_sp_create_for_rbac
