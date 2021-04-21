---
title: Virtuele knooppunten maken met Behulp van Azure CLI
titleSuffix: Azure Kubernetes Service
description: Leer hoe u de Azure CLI gebruikt om een AKS-cluster (Azure Kubernetes Services) te maken dat gebruikmaakt van virtuele knooppunten om pods uit te voeren.
services: container-service
ms.topic: conceptual
ms.date: 03/16/2021
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 86f1d8923eea961471883c44168c4fa848ac12d1
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769279"
---
# <a name="create-and-configure-an-azure-kubernetes-services-aks-cluster-to-use-virtual-nodes-using-the-azure-cli"></a>Een AKS-cluster (Azure Kubernetes Services) maken en configureren om virtuele knooppunten te gebruiken met behulp van de Azure CLI

In dit artikel wordt beschreven hoe u de Azure CLI gebruikt om de resources van het virtuele netwerk en het AKS-cluster te maken en configureren en vervolgens virtuele knooppunten in teschakelen.


## <a name="before-you-begin"></a>Voordat u begint

Virtuele knooppunten maken netwerkcommunicatie mogelijk tussen pods die worden uitgevoerd in Azure Container Instances (ACI) en het AKS-cluster. Om deze communicatie mogelijk te maken, wordt er een subnet van een virtueel netwerk gemaakt en worden gedelegeerde machtigingen toegewezen. Virtuele knooppunten werken alleen met AKS-clusters die zijn gemaakt *met behulp van* geavanceerde netwerken (Azure CNI). Standaard worden AKS-clusters gemaakt met *basisnetwerken* (kubenet). In dit artikel wordt beschreven hoe u een virtueel netwerk en subnetten maakt en vervolgens een AKS-cluster implementeert dat gebruikmaakt van geavanceerde netwerken.

> [!IMPORTANT]
> Voordat u virtuele knooppunten met AKS gebruikt, controleert u de beperkingen van virtuele [AKS-knooppunten][virtual-nodes-aks] en de beperkingen van virtuele netwerken [van ACI.][virtual-nodes-networking-aci] Deze beperkingen zijn van invloed op de locatie, netwerkconfiguratie en andere configuratiedetails van zowel uw AKS-cluster als de virtuele knooppunten.

Als u ACI nog niet eerder hebt gebruikt, registreert u de serviceprovider bij uw abonnement. U kunt de status van de registratie van de ACI-provider controleren met de [opdracht az provider list,][az-provider-list] zoals wordt weergegeven in het volgende voorbeeld:

```azurecli-interactive
az provider list --query "[?contains(namespace,'Microsoft.ContainerInstance')]" -o table
```

De *provider Microsoft.ContainerInstance* moet rapporteren als *Geregistreerd,* zoals wordt weergegeven in de volgende voorbeelduitvoer:

```output
Namespace                    RegistrationState    RegistrationPolicy
---------------------------  -------------------  --------------------
Microsoft.ContainerInstance  Registered           RegistrationRequired
```

Als de provider wordt weergegeven als *NotRegistered,* registreert u de provider met behulp van [az provider register,][az-provider-register] zoals wordt weergegeven in het volgende voorbeeld:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerInstance
```

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell starten

Azure Cloud Shell is een gratis interactieve shell waarmee u de stappen in dit artikel kunt uitvoeren. In deze shell zijn algemene Azure-hulpprogramma's vooraf geïnstalleerd en geconfigureerd voor gebruik met uw account.

Als u Cloud Shell wilt openen, selecteert u **Proberen** in de rechterbovenhoek van een codeblok. U kunt Cloud Shell ook openen in een afzonderlijk browsertabblad door naar [https://shell.azure.com/bash](https://shell.azure.com/bash) te gaan. Klik op **Kopiëren** om de codeblokken te kopiëren, plak deze in Cloud Shell en druk vervolgens op Enter om de code uit te voeren.

Als u liever de CLI lokaal installeert en gebruikt, is voor dit artikel Versie 2.0.49 of hoger van Azure CLI vereist. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren]( /cli/azure/install-azure-cli) als u de CLI wilt installeren of een upgrade wilt uitvoeren.

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Een Azure-resourcegroep is een logische groep waarin Azure-resources worden geïmplementeerd en beheerd. Een resourcegroep maken met de opdracht [az group create][az-group-create]. In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroup* gemaakt op de locatie *westus*.

```azurecli-interactive
az group create --name myResourceGroup --location westus
```

## <a name="create-a-virtual-network"></a>Een virtueel netwerk maken

Maak een virtueel netwerk met de [opdracht az network vnet create.][az-network-vnet-create] In het volgende voorbeeld wordt de naam van een virtueel netwerk *myVnet* gemaakt met het adres voorvoegsel *10.0.0.0/8* en een subnet met de naam *myAKSSubnet.* Het adresvoorvoegsel van dit subnet wordt standaard ingesteld op *10.240.0.0/16:*

```azurecli-interactive
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefixes 10.0.0.0/8 \
    --subnet-name myAKSSubnet \
    --subnet-prefix 10.240.0.0/16
```

Maak nu een extra subnet voor virtuele knooppunten met behulp van [de opdracht az network vnet subnet create.][az-network-vnet-subnet-create] In het volgende voorbeeld wordt een subnet met de naam *myVirtualNodeSubnet* gemaakt met het adres voorvoegsel *10.241.0.0/16.*

```azurecli-interactive
az network vnet subnet create \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name myVirtualNodeSubnet \
    --address-prefixes 10.241.0.0/16
```

## <a name="create-a-service-principal-or-use-a-managed-identity"></a>Een service-principal maken of een beheerde identiteit gebruiken

Om een AKS-cluster te laten communiceren met andere Azure-resources, wordt een clusteridentiteit gebruikt. Deze clusteridentiteit kan automatisch worden gemaakt door de Azure CLI of portal, of u kunt er vooraf een maken en extra machtigingen toewijzen. Deze clusteridentiteit is standaard een beheerde identiteit. Zie [Beheerde identiteiten gebruiken](use-managed-identity.md) voor meer informatie. U kunt ook een service-principal gebruiken als uw clusteridentiteit. De volgende stappen laten zien hoe u handmatig de service-principal maakt en toewijst aan uw cluster.

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

Als u wilt toestaan dat uw cluster het virtuele netwerk gebruikt en beheert, moet u de AKS-service-principal de juiste rechten verlenen om de netwerkbronnen te gebruiken.

Haal eerst de resource-id van het virtuele netwerk op [met az network vnet show][az-network-vnet-show]:

```azurecli-interactive
az network vnet show --resource-group myResourceGroup --name myVnet --query id -o tsv
```

Als u het AKS-cluster de juiste toegang wilt verlenen om het virtuele netwerk te gebruiken, maakt u een roltoewijzing met behulp van [de opdracht az role assignment create.][az-role-assignment-create] Vervang `<appId`> en `<vnetId>` door de waarden die zijn verzameld in de vorige twee stappen.

```azurecli-interactive
az role assignment create --assignee <appId> --scope <vnetId> --role Contributor
```

## <a name="create-an-aks-cluster"></a>Een AKS-cluster maken

U implementeert een AKS-cluster in het AKS-subnet dat u in een vorige stap hebt gemaakt. Haal de id van dit subnet op met [az network vnet subnet show][az-network-vnet-subnet-show]:

```azurecli-interactive
az network vnet subnet show --resource-group myResourceGroup --vnet-name myVnet --name myAKSSubnet --query id -o tsv
```

Gebruik de opdracht [az aks create][az-aks-create] om een AKS-cluster te maken. In het volgende voorbeeld wordt een cluster met de naam *myAKSCluster* gemaakt met één knooppunt. Vervang door de id die u in de vorige stap hebt verkregen en vervang vervolgens door de waarden die `<subnetId>` in de vorige sectie zijn `<appId>` `<password>` verzameld.

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

## <a name="enable-virtual-nodes-addon"></a>Invoegisatie van virtuele knooppunten inschakelen

Als u virtuele knooppunten wilt inschakelen, gebruikt [u nu de opdracht az aks enable-addons.][az-aks-enable-addons] In het volgende voorbeeld wordt het subnet *myVirtualNodeSubnet* gebruikt dat in een vorige stap is gemaakt:

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

In de volgende voorbeelduitvoer ziet u het enkele VM-knooppunt dat is gemaakt en vervolgens het virtuele knooppunt voor Linux, *virtual-node-aci-linux:*

```output
NAME                          STATUS    ROLES     AGE       VERSION
virtual-node-aci-linux        Ready     agent     28m       v1.11.2
aks-agentpool-14693408-0      Ready     agent     32m       v1.11.2
```

## <a name="deploy-a-sample-app"></a>Een voorbeeld-app implementeren

Maak een bestand met `virtual-node.yaml` de naam en kopieer het in de volgende YAML. Als u de container op het knooppunt wilt plannen, worden [een knooppuntselector][node-selector] en [-toleratie][toleration] gedefinieerd.

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

Voer de toepassing uit met de [opdracht kubectl apply.][kubectl-apply]

```console
kubectl apply -f virtual-node.yaml
```

Gebruik de [opdracht kubectl get pods][kubectl-get] met het argument om een lijst met pods en het geplande knooppunt uit `-o wide` te voeren. U ziet dat `aci-helloworld` de pod is gepland op het `virtual-node-aci-linux` knooppunt.

```console
kubectl get pods -o wide
```

```output
NAME                            READY     STATUS    RESTARTS   AGE       IP           NODE
aci-helloworld-9b55975f-bnmfl   1/1       Running   0          4m        10.241.0.4   virtual-node-aci-linux
```

Aan de pod wordt een intern IP-adres toegewezen uit het subnet van het virtuele Azure-netwerk dat is gedelegeerd voor gebruik met virtuele knooppunten.

> [!NOTE]
> Als u afbeeldingen gebruikt die zijn opgeslagen in Azure Container Registry, [configureert en gebruikt u een Kubernetes-geheim][acr-aks-secrets]. Een huidige beperking van virtuele knooppunten is dat u geen geïntegreerde verificatie van de Azure AD-service-principal kunt gebruiken. Als u geen geheim gebruikt, kunnen pods die zijn gepland op virtuele knooppunten niet worden start en wordt de fout `HTTP response status code 400 error code "InaccessibleImage"` weergegeven.

## <a name="test-the-virtual-node-pod"></a>De pod van het virtuele knooppunt testen

Als u de pod wilt testen die wordt uitgevoerd op het virtuele knooppunt, bladert u naar de demotoepassing met een webclient. Omdat aan de pod een intern IP-adres is toegewezen, kunt u deze connectiviteit snel testen vanuit een andere pod in het AKS-cluster. Maak een testpod en koppel er een terminalsessie aan:

```console
kubectl run -it --rm testvk --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11
```

Installeer `curl` in de pod met behulp van `apt-get` :

```console
apt-get update && apt-get install -y curl
```

U hebt nu toegang tot het adres van uw pod met `curl` behulp van , zoals *http://10.241.0.4* . Geef uw eigen interne IP-adres op dat wordt weergegeven in de vorige `kubectl get pods` opdracht:

```console
curl -L http://10.241.0.4
```

De demotoepassing wordt weergegeven, zoals wordt weergegeven in de volgende verkorte voorbeelduitvoer:

```output
<html>
<head>
  <title>Welcome to Azure Container Instances!</title>
</head>
[...]
```

Sluit de terminalsessie met uw `exit` testpod. Wanneer de sessie is beëindigd, wordt de pod verwijderd.

## <a name="remove-virtual-nodes"></a>Virtuele knooppunten verwijderen

Als u geen virtuele knooppunten meer wilt gebruiken, kunt u ze uitschakelen met de [opdracht az aks disable-addons.][az aks disable-addons] 

Ga zo nodig naar om [https://shell.azure.com](https://shell.azure.com) de Azure Cloud Shell openen in uw browser.

Verwijder eerst de `aci-helloworld` pod die wordt uitgevoerd op het virtuele knooppunt:

```console
kubectl delete -f virtual-node.yaml
```

Met de volgende voorbeeldopdracht worden de virtuele Linux-knooppunten uitgeschakeld:

```azurecli-interactive
az aks disable-addons --resource-group myResourceGroup --name myAKSCluster --addons virtual-node
```

Verwijder nu de resources en resourcegroep van het virtuele netwerk:

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

In dit artikel is een pod gepland op het virtuele knooppunt en is een privé, intern IP-adres toegewezen. U kunt in plaats daarvan een service-implementatie maken en verkeer naar uw pod doorverzenden via een load balancer controller of controller voor verkeer naar binnenverkeer. Zie Create [a basic ingress controller in AKS (Een eenvoudige controller voor ingress maken in AKS) voor meer informatie.][aks-basic-ingress]

Virtuele knooppunten zijn vaak een onderdeel van een schaaloplossing in AKS. Zie de volgende artikelen voor meer informatie over oplossingen voor schalen:

- [De kubernetes horizontal autoscaler voor pods gebruiken][aks-hpa]
- [Automatisch schalen van Kubernetes-clusters gebruiken][aks-cluster-autoscaler]
- [Bekijk het voorbeeld van automatisch schalen voor virtuele knooppunten][virtual-node-autoscale]
- [Meer informatie over de virtuele Kubelet-open source bibliotheek][virtual-kubelet-repo]

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
[az-group-create]: /cli/azure/group#az_group_create
[az-network-vnet-create]: /cli/azure/network/vnet#az_network_vnet_create
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet#az_network_vnet_subnet_create
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az_ad_sp_create_for_rbac
[az-network-vnet-show]: /cli/azure/network/vnet#az_network_vnet_show
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet#az_network_vnet_subnet_show
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-enable-addons]: /cli/azure/aks#az_aks_enable_addons
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[az aks disable-addons]: /cli/azure/aks#az_aks_disable_addons
[aks-hpa]: tutorial-kubernetes-scale.md
[aks-cluster-autoscaler]: ./cluster-autoscaler.md
[aks-basic-ingress]: ingress-basic.md
[az-provider-list]: /cli/azure/provider#az_provider_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[virtual-nodes-aks]: virtual-nodes.md
[virtual-nodes-networking-aci]: ../container-instances/container-instances-virtual-network-concepts.md