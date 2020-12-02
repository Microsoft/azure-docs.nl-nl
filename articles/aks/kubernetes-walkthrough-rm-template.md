---
title: 'Snelstart: een AKS-cluster (Azure Kubernetes Service) maken'
description: Ontdek hoe u snel een Kubernetes-cluster kunt maken met behulp van een Azure Resource Manager-sjabloon en een toepassing kunt implementeren in Azure Kubernetes Service (AKS)
services: container-service
ms.topic: quickstart
ms.date: 09/11/2020
ms.custom: mvc,subject-armqs, devx-track-azurecli
ms.openlocfilehash: 271913a731a2bdf5af94885b5fe4027c0334853c
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/19/2020
ms.locfileid: "94887498"
---
# <a name="quickstart-deploy-an-azure-kubernetes-service-aks-cluster-using-an-arm-template"></a>Quickstart: Een AKS-cluster (Azure Kubernetes Service) implementeren met behulp van een ARM-sjabloon

Azure Kubernetes Service (AKS) is een beheerde Kubernetes-service waarmee u snel clusters kunt implementeren en beheren. In deze quickstart implementeert u een AKS-cluster met behulp van een Azure Resource Manager-sjabloon (ARM-sjabloon). In het cluster wordt een toepassing met meerdere containers uitgevoerd die bestaat uit een web-front-end en een Redis-exemplaar.

![Afbeelding van browsen naar Azure Vote](media/container-service-kubernetes-walkthrough/azure-voting-application.png)

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

In deze snelstart wordt ervan uitgegaan dat u een basisbegrip hebt van Kubernetes-concepten. Zie [Kubernetes-kernconcepten voor Azure Kubernetes Service (AKS)][kubernetes-concepts] voor meer informatie.

Als uw omgeving voldoet aan de vereisten en u benkend bent met het gebruik van ARM-sjablonen, selecteert u de knop **Implementeren naar Azure**. De sjabloon wordt in Azure Portal geopend.

[![Implementeren in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-aks%2Fazuredeploy.json)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Voor dit artikel is versie 2.0.61 of hoger van Azure CLI vereist. Als u Azure Cloud Shell gebruikt, is de nieuwste versie al geïnstalleerd.

- Als u een AKS-cluster wilt maken met behulp van een Resource Manager-sjabloon, geeft u een open bare SSH-sleutel en Azure Active Directory-service-principal op. U kunt voor machtigingen ook een [beheerde identiteit](use-managed-identity.md) gebruiken in plaats van een service-principal. Als u een van deze resources nodig hebt, raadpleegt u de volgende sectie. Ga anders naar de sectie [De sjabloon controleren](#review-the-template).

### <a name="create-an-ssh-key-pair"></a>Een SSH-sleutelpaar maken

Als u toegang wilt krijgen tot AKS-knooppunten, maakt u verbinding met een SSH-sleutelpaar. Gebruik de opdracht `ssh-keygen` om openbare en privé-SSH-sleutelbestanden te genereren. Deze bestanden worden standaard gemaakt in de map *~/.ssh*. Als er op de opgegeven locatie een SSH-sleutelpaar met dezelfde naam bestaat, worden deze bestanden overschreven.

Ga naar [https://shell.azure.com](https://shell.azure.com) om Cloud Shell in uw browser te openen.

Met de volgende opdracht wordt een SSH-sleutelpaar gemaakt met behulp van RSA-versleuteling en een bitlengte van 2048:

```console
ssh-keygen -t rsa -b 2048
```

Zie [SSH-sleutels maken en beheren voor verificatie in Azure][ssh-keys] voor meer informatie over het maken van SSH-sleutels.

### <a name="create-a-service-principal"></a>Een service-principal maken

Een AKS-cluster heeft een service-principal van Azure Active Directory nodig om met andere Azure-resources te kunnen communiceren. Maak een service-principal met behulp van de opdracht [az ad sp create-for-rbac][az-ad-sp-create-for-rbac]. De parameter `--skip-assignment` zorgt ervoor dat eventuele extra machtigingen beperkt kunnen worden toegewezen. Standaard is de service-principal geldig voor één jaar. U kunt ook een beheerde identiteit gebruiken in plaats van een service-principal. Zie [Beheerde identiteiten gebruiken](use-managed-identity.md) voor meer informatie.

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment
```

De uitvoer lijkt op die in het volgende voorbeeld:

```json
{
  "appId": "8b1ede42-d407-46c2-a1bc-6b213b04295f",
  "displayName": "azure-cli-2019-04-19-21-42-11",
  "name": "http://azure-cli-2019-04-19-21-42-11",
  "password": "27e5ac58-81b0-46c1-bd87-85b4ef622682",
  "tenant": "73f978cf-87f2-41bf-92ab-2e7ce012db57"
}
```

Noteer de *appId* en *wachtwoord*. Deze waarden worden gebruikt in de volgende stappen.

## <a name="review-the-template"></a>De sjabloon controleren

De sjabloon die in deze quickstart wordt gebruikt, komt uit [Azure Quick Start-sjablonen](https://azure.microsoft.com/resources/templates/101-aks/).

:::code language="json" source="~/quickstart-templates/101-aks/azuredeploy.json":::

Zie de site [AKS-quickstartsjablonen][aks-quickstart-templates] voor meer AKS-voorbeelden.

## <a name="deploy-the-template"></a>De sjabloon implementeren

1. Selecteer de volgende afbeelding om u aan te melden bij Azure en een sjabloon te openen.

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
    * **Client-id service-principal**: kopieer en plak de *appId* van de service-principal uit de opdracht `az ad sp create-for-rbac`.
    * **Clientgeheim service-principal**: kopieer en plak het *wachtwoord* van de service-principal uit de opdracht `az ad sp create-for-rbac`.
    * **Ik ga akkoord met de bovenstaande voorwaarden**: schakel dit selectievakje in om akkoord te gaan.

    ![Resource Manager-sjabloon om een Azure Kubernetes Service-cluster te maken in de portal](./media/kubernetes-walkthrough-rm-template/create-aks-cluster-using-template-portal.png)

3. Selecteer **Aankoop**.

Het duurt een paar minuten om het AKS-cluster te maken. Wacht totdat het cluster met succes is geïmplementeerd voordat u met de volgende stap verdergaat.

## <a name="validate-the-deployment"></a>De implementatie valideren

### <a name="connect-to-the-cluster"></a>Verbinding maken met het cluster

Als u een Kubernetes-cluster wilt beheren, gebruikt u [kubectl][kubectl], de Kubernetes-opdrachtregelclient. Als u Azure Cloud Shell gebruikt, is `kubectl` al geïnstalleerd. Als u `kubectl` lokaal wilt installeren, gebruikt u de opdracht [az aks install-cli][az-aks-install-cli]:

```azurecli
az aks install-cli
```

Gebruik de opdracht [az aks get-credentials][az-aks-get-credentials] om `kubectl` zodanig te configureren dat er verbinding wordt gemaakt met het Kubernetes-cluster. Bij deze opdracht worden referenties gedownload en wordt Kubernetes CLI geconfigureerd voor het gebruik van deze referenties.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Als u de verbinding met uw cluster wilt controleren, gebruikt u de opdracht [kubectl get][kubectl-get] om een lijst met clusterknooppunten te retourneren.

```console
kubectl get nodes
```

In de volgende voorbeelduitvoer ziet u de knooppunten die in de vorige stappen zijn gemaakt. Zorg ervoor dat de status van alle knooppunten *Ready* is:

```output
NAME                       STATUS   ROLES   AGE     VERSION
aks-agentpool-41324942-0   Ready    agent   6m44s   v1.12.6
aks-agentpool-41324942-1   Ready    agent   6m46s   v1.12.6
aks-agentpool-41324942-2   Ready    agent   6m45s   v1.12.6
```

### <a name="run-the-application"></a>De toepassing uitvoeren

In een Kubernetes-manifestbestand wordt een gewenste status voor het cluster gedefinieerd, zoals welke containerinstallatiekopieën moeten worden uitgevoerd. In deze snelstart worden met behulp van een manifest alle objecten gemaakt die nodig zijn om de Azure Vote-toepassing uit te voeren. Dit manifest bevat twee [Kubernetes-implementaties][kubernetes-deployment], een voor de Azure Vote Python-voorbeeldtoepassingen en een voor een instantie van Redis. Er worden bovendien twee [Kubernetes-services][kubernetes-service] gemaakt: een interne service voor de Redis-instantie en een externe service voor toegang tot de Azure Vote-toepassing vanaf internet.

Maak een bestand met de naam `azure-vote.yaml` en kopieer de volgende YAML-definitie naar het bestand. Als u Azure Cloud Shell gebruikt, kan dit bestand worden gemaakt met behulp van `vi` of `nano`, zoals bij een virtueel of fysiek systeem:

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

Implementeer de toepassing met de opdracht [kubectl apply][kubectl-apply] en geef de naam op van uw YAML-manifest:

```console
kubectl apply -f azure-vote.yaml
```

In de volgende voorbeelduitvoer ziet u dat je implementaties en services zijn gemaakt:

```output
deployment "azure-vote-back" created
service "azure-vote-back" created
deployment "azure-vote-front" created
service "azure-vote-front" created
```

### <a name="test-the-application"></a>De toepassing testen

Wanneer de toepassing wordt uitgevoerd, maakt een Kubernetes-service de front-end van de toepassing beschikbaar op internet. Dit proces kan enkele minuten duren.

Gebruik de opdracht [kubectl get service][kubectl-get] met het argument `--watch` om de voortgang te controleren.

```console
kubectl get service azure-vote-front --watch
```

Eerst wordt het *EXTERNAL-IP*-adres voor de service *azure-vote-front* weergegeven als *in behandeling*.

```output
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

Zodra het *EXTERNAL-IP*-adres is gewijzigd van *in behandeling* in een echt openbaar IP-adres, gebruikt u `CTRL-C` om het controleproces van `kubectl` te stoppen. In de volgende voorbeelduitvoer ziet u een geldig openbaar IP-adres dat aan de service is toegewezen:

```output
azure-vote-front   LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

Open een webbrowser naar het externe IP-adres van uw service om de Azure Vote-app te zien.

![Afbeelding van browsen naar Azure Vote](media/container-service-kubernetes-walkthrough/azure-voting-application.png)

## <a name="clean-up-resources"></a>Resources opschonen

Gebruik de opdracht [az group delete][az-group-delete] om de resourcegroep, de containerservice en alle gerelateerde resources te verwijderen wanneer u het cluster niet meer nodig hebt.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

> [!NOTE]
> Wanneer u het cluster verwijdert, wordt de Azure Active Directory-service-principal die door het AKS-cluster wordt gebruikt niet verwijderd. Zie [Overwegingen voor en verwijdering van AKS service-principal][sp-delete] voor stappen voor het verwijderen van de service-principal. Als u een beheerde identiteit hebt gebruikt, wordt de identiteit beheerd door het platform en hoeft deze niet te worden verwijderd.

## <a name="get-the-code"></a>Code ophalen

In deze snelstartgids zijn vooraf gemaakte containerinstallatiekopieën gebruikt om een Kubernetes-implementatie te maken. De gerelateerde toepassingscode, Dockerfile en het Kubernetes-manifestbestand zijn beschikbaar op GitHub.

[https://github.com/Azure-Samples/azure-voting-app-redis][azure-vote-app]

## <a name="next-steps"></a>Volgende stappen

In deze snelstart hebt u een Kubernetes-cluster geïmplementeerd en vervolgens een toepassing met meerdere containers geïmplementeerd. [Open het Kubernetes-webdashboard][kubernetes-dashboard] voor het cluster dat u hebt gemaakt.

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
[aks-monitor]: ../azure-monitor/insights/container-insights-onboard.md
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[az-aks-browse]: /cli/azure/aks?view=azure-cli-latest#az-aks-browse
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-aks-install-cli]: /cli/azure/aks?view=azure-cli-latest#az-aks-install-cli
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[azure-cli-install]: /cli/azure/install-azure-cli
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[azure-portal]: https://portal.azure.com
[kubernetes-deployment]: concepts-clusters-workloads.md#deployments-and-yaml-manifests
[kubernetes-service]: concepts-network.md#services
[kubernetes-dashboard]: kubernetes-dashboard.md
[ssh-keys]: ../virtual-machines/linux/create-ssh-keys-detailed.md
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
