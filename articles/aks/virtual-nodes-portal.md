---
title: Virtuele knooppunten maken met behulp van de portal in Azure Kubernetes Services (AKS)
description: Leer hoe u de Azure Portal om een AKS-cluster (Azure Kubernetes Services) te maken dat gebruikmaakt van virtuele knooppunten om pods uit te voeren.
services: container-service
ms.topic: conceptual
ms.date: 03/15/2021
ms.custom: references_regions
ms.openlocfilehash: fad021dc92753013234a3b0831e76e87fa25db10
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769297"
---
# <a name="create-and-configure-an-azure-kubernetes-services-aks-cluster-to-use-virtual-nodes-in-the-azure-portal"></a>Een AKS-cluster (Azure Kubernetes Services) maken en configureren voor het gebruik van virtuele knooppunten in de Azure Portal

In dit artikel wordt beschreven hoe u de Azure Portal voor het maken en configureren van de resources van het virtuele netwerk en een AKS-cluster met virtuele knooppunten ingeschakeld.

> [!NOTE]
> [In dit artikel](virtual-nodes.md) vindt u een overzicht van de beschikbaarheid en beperkingen van regio's met behulp van virtuele knooppunten.

## <a name="before-you-begin"></a>Voordat u begint

Virtuele knooppunten maken netwerkcommunicatie mogelijk tussen pods die worden uitgevoerd in Azure Container Instances (ACI) en het AKS-cluster. Voor deze communicatie wordt een subnet van een virtueel netwerk gemaakt en worden gedelegeerde machtigingen toegewezen. Virtuele knooppunten werken alleen met AKS-clusters die zijn gemaakt *met behulp van* geavanceerde netwerken (Azure CNI). Standaard worden AKS-clusters gemaakt met *basisnetwerken* (kubenet). In dit artikel wordt beschreven hoe u een virtueel netwerk en subnetten maakt en vervolgens een AKS-cluster implementeert dat gebruikmaakt van geavanceerde netwerken.

Als u ACI nog niet eerder hebt gebruikt, registreert u de serviceprovider bij uw abonnement. U kunt de status van de registratie van de ACI-provider controleren met de [opdracht az provider list,][az-provider-list] zoals wordt weergegeven in het volgende voorbeeld:

```azurecli-interactive
az provider list --query "[?contains(namespace,'Microsoft.ContainerInstance')]" -o table
```

De *Microsoft.ContainerInstance-provider* moet rapporteren als *Geregistreerd,* zoals wordt weergegeven in de volgende voorbeelduitvoer:

```output
Namespace                    RegistrationState    RegistrationPolicy
---------------------------  -------------------  --------------------
Microsoft.ContainerInstance  Registered           RegistrationRequired
```

Als de provider wordt weergegeven als *NotRegistered,* registreert u de provider met behulp van [az provider register,][az-provider-register] zoals wordt weergegeven in het volgende voorbeeld:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerInstance
```

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij Azure Portal op https://portal.azure.com.

## <a name="create-an-aks-cluster"></a>Een AKS-cluster maken

Selecteer in de linkerbovenhoek van de Azure Portal   >  **Kubernetes Service** een resource maken.

Configureer op de pagina **Basisprincipes** de volgende opties:

- *PROJECTDETAILS:* selecteer een Azure-abonnement en selecteer of maak vervolgens een Azure-resourcegroep, zoals *myResourceGroup.* Voer een **Kubernetes-clusternaam** in, zoals *myAKSCluster*.
- *CLUSTERDETAILS:* selecteer een regio en Kubernetes-versie voor het AKS-cluster.
- *PRIMAIRE KNOOPPUNTGROEP:* selecteer een VM-grootte voor de AKS-knooppunten. De VM-grootte kan **niet** meer worden gewijzigd als een AKS-cluster eenmaal is geïmplementeerd.
     - Selecteer het aantal knooppunten dat u in het cluster wilt implementeren. Voor dit artikel stelt u **Aantal knooppunt in** op *1*. Het aantal knooppunten kan nog **wel** worden gewijzigd als het cluster is geïmplementeerd.

Klik **op Volgende: Knooppuntgroepen.**

Selecteer op **de pagina Knooppuntgroepen** de optie *Virtuele knooppunten inschakelen.*

:::image type="content" source="media/virtual-nodes-portal/enable-virtual-nodes.png" alt-text="In een browser ziet u hoe u een cluster maakt met virtuele knooppunten ingeschakeld op de Azure Portal. De optie Virtuele knooppunten inschakelen is gemarkeerd.":::

Standaard wordt een clusteridentiteit gemaakt. Deze clusteridentiteit wordt gebruikt voor clustercommunicatie en integratie met andere Azure-services. Deze clusteridentiteit is standaard een beheerde identiteit. Zie [Beheerde identiteiten gebruiken](use-managed-identity.md) voor meer informatie. U kunt ook een service-principal gebruiken als uw clusteridentiteit.

Het cluster is ook geconfigureerd voor geavanceerde netwerken. De virtuele knooppunten zijn geconfigureerd voor het gebruik van hun eigen subnet van een virtueel Azure-netwerk. Dit subnet heeft gedelegeerde machtigingen om Azure-resources te verbinden tussen het AKS-cluster. Als u nog geen gedelegeerd subnet hebt, maakt en configureert de Azure Portal het virtuele Azure-netwerk en -subnet voor gebruik met de virtuele knooppunten.

Selecteer **Controleren + maken**. Nadat de validatie is voltooid, selecteert u **Maken.**

Het duurt enkele minuten om het AKS-cluster te maken en voor te bereiden voor gebruik.

## <a name="connect-to-the-cluster"></a>Verbinding maken met het cluster

Azure Cloud Shell is een gratis interactieve shell waarmee u de stappen in dit artikel kunt uitvoeren. In deze shell zijn algemene Azure-hulpprogramma's vooraf geïnstalleerd en geconfigureerd voor gebruik met uw account. Als u een Kubernetes-cluster wilt beheren, gebruikt u [kubectl][kubectl], de Kubernetes-opdrachtregelclient. De client `kubectl` is vooraf geïnstalleerd in Azure Cloud Shell.

Als u Cloud Shell wilt openen, selecteert u **Proberen** in de rechterbovenhoek van een codeblok. U kunt Cloud Shell ook openen in een afzonderlijk browsertabblad door naar [https://shell.azure.com/bash](https://shell.azure.com/bash) te gaan. Klik op **Kopiëren** om de codeblokken te kopiëren, plak deze in Cloud Shell en druk vervolgens op Enter om de code uit te voeren.

Gebruik de opdracht [az aks get-credentials][az-aks-get-credentials] om `kubectl` te configureren om verbinding te maken met het Kubernetes-cluster. In het volgende voorbeeld worden de referenties opgehaald voor de clusternaam *myAKSCluster* in de resourcegroep met de naam *myResourceGroup*:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Als u de verbinding met uw cluster wilt controleren, gebruikt u de opdracht [kubectl get][kubectl-get] om een lijst met clusterknooppunten te retourneren.

```console
kubectl get nodes
```

In de volgende voorbeelduitvoer ziet u het gemaakte VM-knooppunt en vervolgens het virtuele knooppunt voor Linux, *virtual-node-aci-linux:*

```output
NAME                           STATUS    ROLES     AGE       VERSION
virtual-node-aci-linux         Ready     agent     28m       v1.11.2
aks-agentpool-14693408-0       Ready     agent     32m       v1.11.2
```

## <a name="deploy-a-sample-app"></a>Een voorbeeld-app implementeren

Maak in Azure Cloud Shell een bestand met de naam `virtual-node.yaml` en kopieer dit in de volgende YAML. Om de container op het knooppunt te plannen, worden [een nodeSelector][node-selector] en [een toleratie][toleration] gedefinieerd. Met deze instellingen kan de pod worden gepland op het virtuele knooppunt en wordt bevestigd dat de functie is ingeschakeld.

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
```

Voer de toepassing uit met de [opdracht kubectl apply.][kubectl-apply]

```azurecli-interactive
kubectl apply -f virtual-node.yaml
```

Gebruik de [opdracht kubectl get pods][kubectl-get] met het argument om een lijst met pods en het geplande knooppunt `-o wide` uit te voeren. U ziet dat `virtual-node-helloworld` de pod is gepland op het `virtual-node-linux` knooppunt.

```console
kubectl get pods -o wide
```

```output
NAME                                     READY     STATUS    RESTARTS   AGE       IP           NODE
virtual-node-helloworld-9b55975f-bnmfl   1/1       Running   0          4m        10.241.0.4   virtual-node-aci-linux
```

Aan de pod wordt een intern IP-adres toegewezen uit het subnet van het virtuele Azure-netwerk dat is gedelegeerd voor gebruik met virtuele knooppunten.

> [!NOTE]
> Als u afbeeldingen gebruikt die zijn opgeslagen in Azure Container Registry, [configureert en gebruikt u een Kubernetes-geheim][acr-aks-secrets]. Een huidige beperking van virtuele knooppunten is dat u geen geïntegreerde verificatie van de Azure AD-service-principal kunt gebruiken. Als u geen geheim gebruikt, kunnen pods die zijn gepland op virtuele knooppunten niet worden start en wordt de fout `HTTP response status code 400 error code "InaccessibleImage"` weergegeven.

## <a name="test-the-virtual-node-pod"></a>De pod van het virtuele knooppunt testen

Als u de pod wilt testen die wordt uitgevoerd op het virtuele knooppunt, bladert u naar de demotoepassing met een webclient. Omdat aan de pod een intern IP-adres is toegewezen, kunt u deze connectiviteit snel testen vanuit een andere pod in het AKS-cluster. Maak een testpod en koppel er een terminalsessie aan:

```console
kubectl run -it --rm virtual-node-test --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11
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

Sluit de terminalsessie met uw `exit` testpod. Wanneer uw sessie is beëindigd, wordt de pod verwijderd.

## <a name="next-steps"></a>Volgende stappen

In dit artikel is een pod gepland op het virtuele knooppunt en is een privé, intern IP-adres toegewezen. U kunt in plaats daarvan een service-implementatie maken en verkeer naar uw pod doorverzenden via load balancer controller voor verkeer of ingress. Zie Create [a basic ingress controller in AKS (Een eenvoudige controller voor ingressen maken in AKS) voor meer informatie.][aks-basic-ingress]

Virtuele knooppunten vormen een onderdeel van een schaaloplossing in AKS. Zie de volgende artikelen voor meer informatie over het schalen van oplossingen:

- [De kubernetes horizontal autoscaler voor pods gebruiken][aks-hpa]
- [Automatisch schalen van Kubernetes-clusters gebruiken][aks-cluster-autoscaler]
- [Bekijk het voorbeeld van automatisch schalen voor virtuele knooppunten][virtual-node-autoscale]
- [Meer informatie over de virtuele Kubelet-open source bibliotheek][virtual-kubelet-repo]

<!-- LINKS - external -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[node-selector]:https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[toleration]: https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/
[azure-cni]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[aks-github]: https://github.com/azure/aks/issues]
[virtual-node-autoscale]: https://github.com/Azure-Samples/virtual-node-autoscale
[virtual-kubelet-repo]: https://github.com/virtual-kubelet/virtual-kubelet
[acr-aks-secrets]: https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/

<!-- LINKS - internal -->
[aks-network]: ./configure-azure-cni.md
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[aks-hpa]: tutorial-kubernetes-scale.md
[aks-cluster-autoscaler]: cluster-autoscaler.md
[aks-basic-ingress]: ingress-basic.md
[az-provider-list]: /cli/azure/provider#az_provider_list
[az-provider-register]: /cli/azure/provider#az_provider_register
