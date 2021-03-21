---
title: Virtuele knoop punten maken met behulp van Azure CLI
titleSuffix: Azure Kubernetes Service
description: Meer informatie over hoe u de Azure CLI gebruikt om een AKS-cluster (Azure Kubernetes Services) te maken dat virtuele knoop punten gebruikt voor het uitvoeren van een Peul.
services: container-service
ms.topic: conceptual
ms.date: 03/16/2021
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 1c673cae41fcbd3d54aa9b4062dd030ace9f0767
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104577798"
---
# <a name="create-and-configure-an-azure-kubernetes-services-aks-cluster-to-use-virtual-nodes-using-the-azure-cli"></a>Een AKS-cluster (Azure Kubernetes Services) maken en configureren voor het gebruik van virtuele knoop punten met behulp van de Azure CLI

In dit artikel wordt beschreven hoe u de Azure CLI gebruikt om de virtuele netwerk resources en het AKS-cluster te maken en te configureren en vervolgens virtuele knoop punten in te scha kelen.


## <a name="before-you-begin"></a>Voordat u begint

Virtuele knoop punten scha kelen netwerk communicatie in tussen de peulen die worden uitgevoerd in Azure Container Instances (ACI) en het AKS-cluster. Om deze communicatie te bieden, wordt een subnet van een virtueel netwerk gemaakt en worden gedelegeerde machtigingen toegewezen. Virtuele knoop punten werken alleen met AKS-clusters die zijn gemaakt met *Advanced* Network (Azure cni). Standaard worden AKS-clusters gemaakt met *Basic* -netwerken (kubenet). In dit artikel wordt beschreven hoe u een virtueel netwerk en subnetten maakt en hoe u een AKS-cluster implementeert dat gebruikmaakt van geavanceerde netwerken.

> [!IMPORTANT]
> Voordat u virtuele knoop punten met AKS gebruikt, raadpleegt u de [beperkingen van AKS virtuele knoop punten][virtual-nodes-aks] en de [beperkingen voor virtuele netwerken van ACI][virtual-nodes-networking-aci]. Deze beperkingen zijn van invloed op de locatie, de netwerk configuratie en andere configuratie details van uw AKS-cluster en de virtuele knoop punten.

Als u geen ACI eerder hebt gebruikt, registreert u de service provider bij uw abonnement. U kunt de status van de ACI-provider registratie controleren met de opdracht [AZ provider List][az-provider-list] , zoals wordt weer gegeven in het volgende voor beeld:

```azurecli-interactive
az provider list --query "[?contains(namespace,'Microsoft.ContainerInstance')]" -o table
```

De provider van *micro soft. ContainerInstance* meldt zich aan als *geregistreerd*, zoals wordt weer gegeven in de volgende voorbeeld uitvoer:

```output
Namespace                    RegistrationState    RegistrationPolicy
---------------------------  -------------------  --------------------
Microsoft.ContainerInstance  Registered           RegistrationRequired
```

Als de provider wordt weer gegeven als *NotRegistered*, registreert u de provider met behulp van de [AZ provider REGI ster][az-provider-register] , zoals wordt weer gegeven in het volgende voor beeld:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerInstance
```

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell starten

Azure Cloud Shell is een gratis interactieve shell waarmee u de stappen in dit artikel kunt uitvoeren. In deze shell zijn algemene Azure-hulpprogramma's vooraf geïnstalleerd en geconfigureerd voor gebruik met uw account.

Als u Cloud Shell wilt openen, selecteert u **Proberen** in de rechterbovenhoek van een codeblok. U kunt Cloud Shell ook openen in een afzonderlijk browsertabblad door naar [https://shell.azure.com/bash](https://shell.azure.com/bash) te gaan. Klik op **Kopiëren** om de codeblokken te kopiëren, plak deze in Cloud Shell en druk vervolgens op Enter om de code uit te voeren.

Als u liever de CLI lokaal wilt installeren en gebruiken, moet u voor dit artikel gebruikmaken van Azure CLI versie 2.0.49 of hoger. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren]( /cli/azure/install-azure-cli) als u de CLI wilt installeren of een upgrade wilt uitvoeren.

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Een Azure-resourcegroep is een logische groep waarin Azure-resources worden geïmplementeerd en beheerd. Een resourcegroep maken met de opdracht [az group create][az-group-create]. In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroup* gemaakt op de locatie *westus*.

```azurecli-interactive
az group create --name myResourceGroup --location westus
```

## <a name="create-a-virtual-network"></a>Een virtueel netwerk maken

Maak een virtueel netwerk met behulp van de opdracht [AZ Network vnet Create][az-network-vnet-create] . In het volgende voor beeld wordt een virtuele netwerk naam *myVnet* met het adres voorvoegsel *10.0.0.0/8* en een subnet met de naam *myAKSSubnet*. Het adres voorvoegsel van dit subnet is standaard ingesteld op *10.240.0.0/16*:

```azurecli-interactive
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefixes 10.0.0.0/8 \
    --subnet-name myAKSSubnet \
    --subnet-prefix 10.240.0.0/16
```

Maak nu een extra subnet voor virtuele knoop punten met behulp van de opdracht [AZ Network vnet subnet Create][az-network-vnet-subnet-create] . In het volgende voor beeld wordt een subnet met de naam *myVirtualNodeSubnet* gemaakt met het adres voorvoegsel *10.241.0.0/16*.

```azurecli-interactive
az network vnet subnet create \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name myVirtualNodeSubnet \
    --address-prefixes 10.241.0.0/16
```

## <a name="create-a-service-principal-or-use-a-managed-identity"></a>Een service-principal maken of een beheerde identiteit gebruiken

Als u een AKS-cluster wilt toestaan om te communiceren met andere Azure-resources, wordt er een cluster identiteit gebruikt. Deze cluster identiteit kan automatisch worden gemaakt door de Azure CLI of portal, maar u kunt er vooraf een maken en extra machtigingen toewijzen. Deze cluster identiteit is standaard een beheerde identiteit. Zie [Beheerde identiteiten gebruiken](use-managed-identity.md) voor meer informatie. U kunt ook een Service-Principal als uw cluster-id gebruiken. De volgende stappen laten zien hoe u de Service-Principal hand matig kunt maken en toewijzen aan uw cluster.

Maak een service-principal met behulp van de opdracht [az ad sp create-for-rbac][az-ad-sp-create-for-rbac]. De parameter `--skip-assignment` zorgt ervoor dat eventuele extra machtigingen beperkt kunnen worden toegewezen.

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment
```

De uitvoer lijkt op die in het volgende voorbeeld:

```output
{
  "appId": "bef76eb3-d743-4a97-9534-03e9388811fc",
  "displayName": "azure-cli-2018-11-21-18-42-00",
  "name": "http://azure-cli-2018-11-21-18-42-00",
  "password": "1d257915-8714-4ce7-a7fb-0e5a5411df7f",
  "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db48"
}
```

Noteer de *appId* en *wachtwoord*. Deze waarden worden gebruikt in de volgende stappen.

## <a name="assign-permissions-to-the-virtual-network"></a>Machtigingen toewijzen aan het virtuele netwerk

Als u wilt dat uw cluster het virtuele netwerk kan gebruiken en beheren, moet u de AKS-Service-Principal de juiste rechten geven voor het gebruik van de netwerk bronnen.

Haal eerst de bron-ID van het virtuele netwerk op met [AZ Network vnet show][az-network-vnet-show]:

```azurecli-interactive
az network vnet show --resource-group myResourceGroup --name myVnet --query id -o tsv
```

Als u de juiste toegang wilt verlenen voor het AKS-cluster om het virtuele netwerk te gebruiken, maakt u een roltoewijzing met de opdracht [AZ Role Assignment Create][az-role-assignment-create] . Vervang `<appId`> en `<vnetId>` door de waarden die zijn verzameld in de vorige twee stappen.

```azurecli-interactive
az role assignment create --assignee <appId> --scope <vnetId> --role Contributor
```

## <a name="create-an-aks-cluster"></a>Een AKS-cluster maken

U implementeert een AKS-cluster in het AKS-subnet dat u in een vorige stap hebt gemaakt. De ID van dit subnet ophalen met [AZ Network vnet subnet show][az-network-vnet-subnet-show]:

```azurecli-interactive
az network vnet subnet show --resource-group myResourceGroup --vnet-name myVnet --name myAKSSubnet --query id -o tsv
```

Gebruik de opdracht [az aks create][az-aks-create] om een AKS-cluster te maken. In het volgende voorbeeld wordt een cluster met de naam *myAKSCluster* gemaakt met één knooppunt. Vervang door `<subnetId>` de id die u hebt verkregen in de vorige stap en vervolgens `<appId>` `<password>` met de waarden die in de vorige sectie zijn verzameld.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --network-plugin azure \
    --service-cidr 10.0.0.0/16 \
    --dns-service-ip 10.0.0.10 \
    --docker-bridge-address 172.17.0.1/16 \
    --vnet-subnet-id <subnetId> \
    --service-principal <appId> \
    --client-secret <password>
```

Na enkele minuten is de opdracht voltooid en retourneert deze informatie over het cluster in JSON-indeling.

## <a name="enable-virtual-nodes-addon"></a>Invoeg toepassing voor virtuele knoop punten inschakelen

Als u virtuele knoop punten wilt inschakelen, gebruikt u nu de opdracht [AZ AKS Enable-addons][az-aks-enable-addons] . In het volgende voor beeld wordt het subnet gebruikt met de naam *myVirtualNodeSubnet* die in een vorige stap is gemaakt:

```azurecli-interactive
az aks enable-addons \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --addons virtual-node \
    --subnet-name myVirtualNodeSubnet
```

## <a name="connect-to-the-cluster"></a>Verbinding maken met het cluster

Gebruik de opdracht [az aks get-credentials][az-aks-get-credentials] om `kubectl` zodanig te configureren dat er verbinding wordt gemaakt met het Kubernetes-cluster. In deze stap worden referenties gedownload en wordt Kubernetes CLI geconfigureerd voor het gebruik van deze referenties.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Als u de verbinding met uw cluster wilt controleren, gebruikt u de opdracht [kubectl get][kubectl-get] om een lijst met clusterknooppunten te retourneren.

```console
kubectl get nodes
```

In de volgende voorbeeld uitvoer ziet u het knoop punt met één VM dat is gemaakt en vervolgens het virtuele knoop punt voor Linux, *Virtual-node-ACI-Linux*:

```output
NAME                          STATUS    ROLES     AGE       VERSION
virtual-node-aci-linux        Ready     agent     28m       v1.11.2
aks-agentpool-14693408-0      Ready     agent     32m       v1.11.2
```

## <a name="deploy-a-sample-app"></a>Een voor beeld-app implementeren

Maak een bestand `virtual-node.yaml` met de naam en kopieer de volgende YAML. Als u de container op het knoop punt wilt plannen, worden er een [nodeSelector][node-selector] en- [tolerantie][toleration] gedefinieerd.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: aci-helloworld
spec:
  replicas: 1
  selector:
    matchLabels:
      app: aci-helloworld
  template:
    metadata:
      labels:
        app: aci-helloworld
    spec:
      containers:
      - name: aci-helloworld
        image: mcr.microsoft.com/azuredocs/aci-helloworld
        ports:
        - containerPort: 80
      nodeSelector:
        kubernetes.io/role: agent
        beta.kubernetes.io/os: linux
        type: virtual-kubelet
      tolerations:
      - key: virtual-kubelet.io/provider
        operator: Exists
      - key: azure.com/aci
        effect: NoSchedule
```

Voer de toepassing uit met de opdracht [kubectl apply][kubectl-apply] .

```console
kubectl apply -f virtual-node.yaml
```

Gebruik de opdracht [kubectl Get peul][kubectl-get] with het `-o wide` argument voor het uitvoeren van een lijst met een van de peulen en het geplande knoop punt. U ziet dat de `aci-helloworld` Pod is gepland op het `virtual-node-aci-linux` knoop punt.

```console
kubectl get pods -o wide
```

```output
NAME                            READY     STATUS    RESTARTS   AGE       IP           NODE
aci-helloworld-9b55975f-bnmfl   1/1       Running   0          4m        10.241.0.4   virtual-node-aci-linux
```

Aan de Pod wordt een intern IP-adres toegewezen vanuit het subnet van het virtuele netwerk van Azure dat is gedelegeerd voor gebruik met virtuele knoop punten.

> [!NOTE]
> [Configureer en gebruik een Kubernetes-geheim][acr-aks-secrets]als u installatie kopieën gebruikt die zijn opgeslagen in azure container Registry. Een huidige beperking van virtuele knoop punten is dat u geen geïntegreerde Azure AD-Service-Principal-verificatie kunt gebruiken. Als u geen geheim gebruikt, kan er geen peulen gepland op virtuele knoop punten worden gestart en wordt de fout gerapporteerd `HTTP response status code 400 error code "InaccessibleImage"` .

## <a name="test-the-virtual-node-pod"></a>De pod van het virtuele knoop punt testen

Als u de pod die op het virtuele knoop punt wordt uitgevoerd wilt testen, bladert u naar de demo toepassing met een webclient. Als aan de pod een intern IP-adres is toegewezen, kunt u deze verbinding snel testen vanaf een andere pod op het AKS-cluster. Een test pod maken en een terminal sessie hieraan koppelen:

```console
kubectl run -it --rm testvk --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11
```

Installeer `curl` in de Pod met `apt-get` :

```console
apt-get update && apt-get install -y curl
```

U hebt nu toegang tot het adres van uw Pod met `curl` , zoals *http://10.241.0.4* . Geef uw eigen interne IP-adres op dat wordt weer gegeven in de vorige `kubectl get pods` opdracht:

```console
curl -L http://10.241.0.4
```

De voorbeeld toepassing wordt weer gegeven, zoals wordt weer gegeven in de volgende verkorte voorbeeld uitvoer:

```output
<html>
<head>
  <title>Welcome to Azure Container Instances!</title>
</head>
[...]
```

Sluit de terminal sessie met uw test pod met `exit` . Wanneer de sessie is beëindigd, wordt de pod verwijderd.

## <a name="remove-virtual-nodes"></a>Virtuele knoop punten verwijderen

Als u geen virtuele knoop punten meer wilt gebruiken, kunt u deze uitschakelen met de opdracht [AZ AKS Disable-addons][az aks disable-addons] . 

Ga indien nodig naar [https://shell.azure.com](https://shell.azure.com) om Azure Cloud shell in uw browser te openen.

Verwijder eerst de `aci-helloworld` pod die wordt uitgevoerd op het virtuele knoop punt:

```console
kubectl delete -f virtual-node.yaml
```

Met de volgende voorbeeld opdracht worden de virtuele Linux-knoop punten uitgeschakeld:

```azurecli-interactive
az aks disable-addons --resource-group myResourceGroup --name myAKSCluster --addons virtual-node
```

Verwijder nu de virtuele netwerk resources en de resource groep:

```azurecli-interactive
# Change the name of your resource group, cluster and network resources as needed
RES_GROUP=myResourceGroup
AKS_CLUSTER=myAKScluster
AKS_VNET=myVnet
AKS_SUBNET=myVirtualNodeSubnet

# Get AKS node resource group
NODE_RES_GROUP=$(az aks show --resource-group $RES_GROUP --name $AKS_CLUSTER --query nodeResourceGroup --output tsv)

# Get network profile ID
NETWORK_PROFILE_ID=$(az network profile list --resource-group $NODE_RES_GROUP --query [0].id --output tsv)

# Delete the network profile
az network profile delete --id $NETWORK_PROFILE_ID -y

# Delete the subnet delegation to Azure Container Instances
az network vnet subnet update --resource-group $RES_GROUP --vnet-name $AKS_VNET --name $AKS_SUBNET --remove delegations 0
```

## <a name="next-steps"></a>Volgende stappen

In dit artikel is een pod gepland op het virtuele knoop punt en een persoonlijk, intern IP-adres toegewezen. U kunt in plaats daarvan een service-implementatie maken en verkeer naar uw Pod sturen via een load balancer-of ingangs controller. Zie [een Basic ingress-controller maken in AKS][aks-basic-ingress]voor meer informatie.

Virtuele knoop punten zijn vaak één onderdeel van een schaal oplossing in AKS. Raadpleeg de volgende artikelen voor meer informatie over het schalen van oplossingen:

- [De Kubernetes-functie voor horizontale pod automatisch schalen gebruiken][aks-hpa]
- [De automatische schaal functie van het Kubernetes-cluster gebruiken][aks-cluster-autoscaler]
- [Bekijk het voor beeld van automatisch schalen voor virtuele knoop punten][virtual-node-autoscale]
- [Meer informatie over de open source-bibliotheek van de virtuele-Kubelet][virtual-kubelet-repo]

<!-- LINKS - external -->
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[node-selector]:https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[toleration]: https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/
[aks-github]: https://github.com/azure/aks/issues
[virtual-node-autoscale]: https://github.com/Azure-Samples/virtual-node-autoscale
[virtual-kubelet-repo]: https://github.com/virtual-kubelet/virtual-kubelet
[acr-aks-secrets]: https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/

<!-- LINKS - internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-group-create]: /cli/azure/group#az-group-create
[az-network-vnet-create]: /cli/azure/network/vnet#az-network-vnet-create
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet#az-network-vnet-subnet-create
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-network-vnet-show]: /cli/azure/network/vnet#az-network-vnet-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet#az-network-vnet-subnet-show
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-enable-addons]: /cli/azure/aks#az-aks-enable-addons
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az aks disable-addons]: /cli/azure/aks#az-aks-disable-addons
[aks-hpa]: tutorial-kubernetes-scale.md
[aks-cluster-autoscaler]: ./cluster-autoscaler.md
[aks-basic-ingress]: ingress-basic.md
[az-provider-list]: /cli/azure/provider#az-provider-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[virtual-nodes-aks]: virtual-nodes.md
[virtual-nodes-networking-aci]: ../container-instances/container-instances-virtual-network-concepts.md