---
title: 'Quickstart: Een AKS-cluster implementeren met behulp van de Azure CLI'
description: Ontdek hoe u snel een Kubernetes-cluster kunt maken, een toepassing kunt implementeren en de prestaties in Azure Kubernetes Service (AKS) kunt bewaken met behulp van de Azure CLI.
services: container-service
ms.topic: quickstart
ms.date: 09/11/2020
ms.custom:
- H1Hack27Feb2017
- mvc
- devcenter
- seo-javascript-september2019
- seo-javascript-october2019
- seo-python-october2019
- devx-track-azurecli
- contperfq1
ms.openlocfilehash: c3aa2c77141c599348c4ce939114bff4c81a9773
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/19/2020
ms.locfileid: "94887464"
---
# <a name="quickstart-deploy-an-azure-kubernetes-service-cluster-using-the-azure-cli"></a>Quickstart: Implementeer een Azure Kubernetes Service-cluster met behulp van de Azure CLI.

In deze snelstart implementeert u een AKS-cluster (Azure Kubernetes Service) met behulp van de Azure CLI. Azure is een beheerde Kubernetes-service waarmee u snel clusters kunt implementeren en beheren. In het cluster wordt een toepassing met meerdere containers uitgevoerd die bestaat uit een web-front-end en een Redis-exemplaar. Vervolgens ziet u hoe u de status van het cluster en de pods kunt bewaken die uw toepassing uitvoeren.

Zie [Een AKS-cluster maken dat ondersteuning biedt voor Windows Server-containers][windows-container-cli] voor meer informatie over het maken van een Windows Server-knooppuntgroep.

![Stem-app is geïmplementeerd in Azure Kubernetes Service](./media/container-service-kubernetes-walkthrough/voting-app-deployed-in-azure-kubernetes-service.png)

In deze snelstart wordt ervan uitgegaan dat u een basisbegrip hebt van Kubernetes-concepten. Zie [Kubernetes-kernconcepten voor Azure Kubernetes Service (AKS)][kubernetes-concepts] voor meer informatie.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Voor dit artikel is versie 2.0.64 of hoger van Azure CLI vereist. Als u Azure Cloud Shell gebruikt, is de nieuwste versie al geïnstalleerd.

> [!NOTE]
> Als u de opdrachten in deze quickstart lokaal uitvoert (in plaats van met Azure Cloud Shell), zorg er dan voor dat u de opdrachten uitvoert als beheerder.

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Een Azure-resourcegroep is een logische groep waarin Azure-resources worden geïmplementeerd en beheerd. Wanneer u een resourcegroep maakt, wordt u gevraagd een locatie op te geven. Op deze locatie zijn de metagegevens van de resourcegroep opgeslagen. Dit is ook de locatie waar uw resources worden uitgevoerd in Azure als u tijdens het maken van de resource geen andere regio opgeeft. Maak een resourcegroep met de opdracht [az group create][az-group-create].

In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroup* gemaakt op de locatie *VS - oost*.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

In de volgende voorbeelduitvoer ziet u dat de resourcegroep is gemaakt:

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

## <a name="create-aks-cluster"></a>AKS-cluster maken

Gebruik de opdracht [az aks create][az-aks-create] om een AKS-cluster te maken. In het volgende voorbeeld wordt een cluster met de naam *myAKSCluster* gemaakt met één knooppunt. Dit zal enkele minuten in beslag nemen.

> [!NOTE]
> [Azure Monitor voor containers][azure-monitor-containers] wordt ingeschakeld met behulp van de parameter *--enable-addons monitoring*, waarvoor *Microsoft.OperationsManagement* en *Microsoft.OperationalInsights* moeten zijn geregistreerd in uw abonnement. De registratiestatus controleren:
> 
> ```azurecli
> az provider show -n Microsoft.OperationsManagement -o table
> az provider show -n Microsoft.OperationalInsights -o table
> ```
> 
> Als ze niet zijn geregistreerd, gebruikt u de volgende opdracht om *Microsoft.OperationsManagement* en *Microsoft.OperationalInsights* te registreren:
> 
> ```azurecli
> az provider register --namespace Microsoft.OperationsManagement
> az provider register --namespace Microsoft.OperationalInsights
> ```

```azurecli-interactive
az aks create --resource-group myResourceGroup --name myAKSCluster --node-count 1 --enable-addons monitoring --generate-ssh-keys
```

Na enkele minuten is de opdracht voltooid en retourneert deze informatie over het cluster in JSON-indeling.

> [!NOTE]
> Wanneer een AKS-cluster wordt gemaakt, wordt automatisch een tweede resourcegroep gemaakt om de AKS-resources in op te slaan. Zie voor meer informatie [Waarom worden er twee resourcegroepen gemaakt met AKS?](./faq.md#why-are-two-resource-groups-created-with-aks)

## <a name="connect-to-the-cluster"></a>Verbinding maken met het cluster

Als u een Kubernetes-cluster wilt beheren, gebruikt u [kubectl][kubectl], de Kubernetes-opdrachtregelclient. Als u Azure Cloud Shell gebruikt, is `kubectl` al geïnstalleerd. Als u `kubectl` lokaal wilt installeren, gebruikt u de opdracht [az aks install-cli][az-aks-install-cli]:

```azurecli
az aks install-cli
```

Gebruik de opdracht [az aks get-credentials][az-aks-get-credentials] om `kubectl` zodanig te configureren dat er verbinding wordt gemaakt met het Kubernetes-cluster. Bij deze opdracht worden referenties gedownload en wordt Kubernetes CLI geconfigureerd voor het gebruik van deze referenties.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

> [!NOTE]
> De bovenstaande opdracht maakt gebruik van de standaardlocatie voor het [Kubernetes-configuratiebestand][kubeconfig-file], namelijk `~/.kube/config`. U kunt een andere locatie voor uw Kubernetes-configuratiebestand opgeven door *--file* te gebruiken.

Als u de verbinding met uw cluster wilt controleren, gebruikt u de opdracht [kubectl get][kubectl-get] om een lijst met clusterknooppunten te retourneren.

```azurecli-interactive
kubectl get nodes
```

In de volgende voorbeelduitvoer ziet u het enkele knooppunt dat is gemaakt in de vorige stappen. Zorg ervoor dat de status van het knooppunt *Ready* is:

```output
NAME                       STATUS   ROLES   AGE     VERSION
aks-nodepool1-31718369-0   Ready    agent   6m44s   v1.12.8
```

## <a name="run-the-application"></a>De toepassing uitvoeren

In een [Kubernetes-manifestbestand][kubernetes-deployment] wordt een gewenste status voor het cluster gedefinieerd, zoals welke containerinstallatiekopieën moeten worden uitgevoerd. In deze quickstart worden met behulp van een manifest alle objecten gemaakt die nodig zijn om de [Azure Vote-toepassing][azure-vote-app] uit te voeren. Dit manifest bevat twee [Kubernetes-implementaties][kubernetes-deployment], een voor de Azure Vote Python-voorbeeldtoepassingen en een voor een instantie van Redis. Er worden bovendien twee [Kubernetes-services][kubernetes-service] gemaakt: een interne service voor de Redis-instantie en een externe service voor toegang tot de Azure Vote-toepassing vanaf internet.

Maak een bestand met de naam `azure-vote.yaml` en kopieer de volgende YAML-definitie naar het bestand. Als u Azure Cloud Shell gebruikt, kan dit bestand worden gemaakt met behulp van `code`, `vi` of `nano`, zoals bij een virtueel of fysiek systeem:

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

## <a name="test-the-application"></a>De toepassing testen

Wanneer de toepassing wordt uitgevoerd, maakt een Kubernetes-service de front-end van de toepassing beschikbaar op internet. Dit proces kan enkele minuten duren.

Gebruik de opdracht [kubectl get service][kubectl-get] met het argument `--watch` om de voortgang te controleren.

```azurecli-interactive
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

![Stem-app is geïmplementeerd in Azure Kubernetes Service](./media/container-service-kubernetes-walkthrough/voting-app-deployed-in-azure-kubernetes-service.png)

Toen het AKS-cluster werd gemaakt, is [Azure Monitor voor containers][azure-monitor-containers] ingeschakeld om metrische gegevens over de status van de clusterknooppunten en -pods vast te leggen. Deze metrische gegevens over de status zijn in de Azure-portal beschikbaar.

## <a name="delete-the-cluster"></a>Het cluster verwijderen

Om Azure-kosten te vermijden, moet u overbodige resources opschonen.  Gebruik de opdracht [az group delete][az-group-delete] om de resourcegroep, de containerservice en alle gerelateerde resources te verwijderen wanneer u het cluster niet meer nodig hebt.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

> [!NOTE]
> Wanneer u het cluster verwijdert, wordt de Azure Active Directory-service-principal die door het AKS-cluster wordt gebruikt niet verwijderd. Zie [Overwegingen voor en verwijdering van AKS service-principal][sp-delete] voor stappen voor het verwijderen van de service-principal. Als u een beheerde identiteit hebt gebruikt, wordt de identiteit beheerd door het platform en hoeft deze niet te worden verwijderd.

## <a name="get-the-code"></a>Code ophalen

In deze snelstartgids zijn vooraf gemaakte containerinstallatiekopieën gebruikt om een Kubernetes-implementatie te maken. De gerelateerde toepassingscode, Dockerfile en het Kubernetes-manifestbestand zijn beschikbaar op GitHub.

[https://github.com/Azure-Samples/azure-voting-app-redis][azure-vote-app]

## <a name="next-steps"></a>Volgende stappen

In deze snelstart hebt u een Kubernetes-cluster geïmplementeerd en vervolgens een toepassing met meerdere containers geïmplementeerd. [Open het Kubernetes-webdashboard][kubernetes-dashboard] voor het AKS-cluster.

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
[aks-monitor]: ../azure-monitor/insights/container-insights-onboard.md
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[az-aks-browse]: /cli/azure/aks?view=azure-cli-latest#az-aks-browse
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-aks-install-cli]: /cli/azure/aks?view=azure-cli-latest#az-aks-install-cli
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[azure-cli-install]: /cli/azure/install-azure-cli
[azure-monitor-containers]: ../azure-monitor/insights/container-insights-overview.md
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[azure-portal]: https://portal.azure.com
[kubernetes-deployment]: concepts-clusters-workloads.md#deployments-and-yaml-manifests
[kubernetes-service]: concepts-network.md#services
[kubernetes-dashboard]: kubernetes-dashboard.md
[windows-container-cli]: windows-container-cli.md