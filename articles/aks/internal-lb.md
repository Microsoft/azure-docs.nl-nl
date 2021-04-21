---
title: Een interne load balancer maken
titleSuffix: Azure Kubernetes Service
description: Informatie over het maken en gebruiken van een intern load balancer om uw services weer te geven met Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 03/04/2019
ms.openlocfilehash: cbb898d05ecc1f0796f3609adb1368c3d77de2c5
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107779737"
---
# <a name="use-an-internal-load-balancer-with-azure-kubernetes-service-aks"></a>Een interne load balancer met Azure Kubernetes Service (AKS)

Als u de toegang tot uw toepassingen in Azure Kubernetes Service (AKS) wilt beperken, kunt u een interne load balancer. Een interne load balancer maakt een Kubernetes-service alleen toegankelijk voor toepassingen die worden uitgevoerd in hetzelfde virtuele netwerk als het Kubernetes-cluster. In dit artikel wordt beschreven hoe u een intern load balancer met Azure Kubernetes Service (AKS) maakt en gebruikt.

> [!NOTE]
> Azure Load Balancer is beschikbaar in twee SKU's: *Basic* en *Standard.* Standaard wordt de Standard-SKU gebruikt wanneer u een AKS-cluster maakt.  Wanneer u een service maakt met het type LoadBalancer, krijgt u hetzelfde LB-type als wanneer u het cluster inrichten. Zie Vergelijking van [Azure load balancer-SKU voor meer informatie.][azure-lb-comparison]

## <a name="before-you-begin"></a>Voordat u begint

In dit artikel wordt ervan uitgenomen dat u een bestaand AKS-cluster hebt. Als u een AKS-cluster nodig hebt, bekijkt u de AKS-quickstart met behulp van [de Azure CLI][aks-quickstart-cli] of met behulp van de [Azure Portal][aks-quickstart-portal].

Ook moet azure CLI versie 2.0.59 of hoger zijn geïnstalleerd en geconfigureerd. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren][install-azure-cli] als u de CLI wilt installeren of een upgrade wilt uitvoeren.

De clusteridentiteit van het AKS-cluster heeft toestemming nodig om netwerkresources te beheren als u een bestaand subnet of bestaande resourcegroep gebruikt. Zie Kubenet-netwerken gebruiken met uw eigen [IP-adresbereiken in Azure Kubernetes Service (AKS) of][use-kubenet] Azure CNI-netwerken [configureren in Azure Kubernetes Service (AKS)][advanced-networking]voor meer informatie. Als u uw load balancer voor het gebruik van een IP-adres in een ander [subnet,][different-subnet]zorgt u ervoor dat de AKS-clusteridentiteit ook leestoegang heeft tot dat subnet.

Zie AKS-toegang tot andere [Azure-resources delegeren voor meer informatie over machtigingen.][aks-sp]

## <a name="create-an-internal-load-balancer"></a>Een interne load balancer maken

Als u een interne load balancer wilt maken, maakt u een servicemanifest met de naam met het servicetype LoadBalancer en de interne aantekening `internal-lb.yaml` *azure-load-balancer,* zoals wordt weergegeven in het volgende voorbeeld: 

```yaml
apiVersion: v1
kind: Service
metadata:
  name: internal-app
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: internal-app
```

Implementeer de interne load balancer met behulp van [kubectl apply][kubectl-apply] en geef de naam van uw YAML-manifest op:

```console
kubectl apply -f internal-lb.yaml
```

Er wordt load balancer Azure-cluster gemaakt in de knooppuntresourcegroep en verbonden met hetzelfde virtuele netwerk als het AKS-cluster.

Wanneer u de servicedetails bekijkt, wordt het IP-adres van de interne load balancer weergegeven in de *kolom EXTERNAL-IP.* In deze context is *Extern* in relatie tot de externe interface van de load balancer, niet dat het een openbaar, extern IP-adres ontvangt. Het kan een paar minuten duren om het IP-adres te wijzigen van in een daadwerkelijk intern *\<pending\>* IP-adres, zoals wordt weergegeven in het volgende voorbeeld:

```
$ kubectl get service internal-app

NAME           TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
internal-app   LoadBalancer   10.0.248.59   10.240.0.7    80:30555/TCP   2m
```

## <a name="specify-an-ip-address"></a>Een IP-adres opgeven

Als u een specifiek IP-adres wilt gebruiken met de interne load balancer, voegt u de eigenschap *loadBalancerIP* toe aan load balancer YAML-manifest. In dit scenario moet het opgegeven IP-adres zich in hetzelfde subnet bevinden als het AKS-cluster en mag het niet al zijn toegewezen aan een resource. Gebruik bijvoorbeeld geen IP-adres in het bereik dat is aangewezen voor het Kubernetes-subnet.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: internal-app
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
spec:
  type: LoadBalancer
  loadBalancerIP: 10.240.0.25
  ports:
  - port: 80
  selector:
    app: internal-app
```

Wanneer het IP-adres is geïmplementeerd en u de servicedetails bekijkt, geeft het IP-adres in de kolom EXTERNAL-IP het opgegeven *IP-adres* weer:

```
$ kubectl get service internal-app

NAME           TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
internal-app   LoadBalancer   10.0.184.168   10.240.0.25   80:30225/TCP   4m
```

Zie Specify a different subnet (Een ander subnet opgeven) voor meer informatie over het configureren van uw load balancer in [een ander subnet][different-subnet]

## <a name="use-private-networks"></a>Privénetwerken gebruiken

Wanneer u uw AKS-cluster maakt, kunt u geavanceerde netwerkinstellingen opgeven. Met deze methode kunt u het cluster implementeren in een bestaand virtueel Azure-netwerk en -subnetten. Een scenario is het implementeren van uw AKS-cluster in een particulier netwerk dat is verbonden met uw on-premises omgeving en services uit te voeren die alleen intern toegankelijk zijn. Zie Configure your own virtual network subnets with [Kubenet][use-kubenet] or Azure CNI (Uw eigen virtuele netwerksubnetten configureren met Kubenet of [Azure CNI) voor meer Azure CNI.][advanced-networking]

Er zijn geen wijzigingen in de vorige stappen nodig om een interne load balancer implementeren in een AKS-cluster dat gebruikmaakt van een particulier netwerk. De load balancer wordt gemaakt in dezelfde resourcegroep als uw AKS-cluster, maar is verbonden met uw persoonlijke virtuele netwerk en subnet, zoals wordt weergegeven in het volgende voorbeeld:

```
$ kubectl get service internal-app

NAME           TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
internal-app   LoadBalancer   10.1.15.188   10.0.0.35     80:31669/TCP   1m
```

> [!NOTE]
> Mogelijk moet u de clusteridentiteit voor  uw AKS-cluster de rol Netwerkbijdrager verlenen aan de resourcegroep waarin uw virtuele Azure-netwerkresources worden geïmplementeerd. Bekijk de clusteridentiteit [met az aks show,][az-aks-show]zoals `az aks show --resource-group myResourceGroup --name myAKSCluster --query "identity"` . Gebruik de opdracht az [role assignment create][az-role-assignment-create] om een roltoewijzing te maken.

## <a name="specify-a-different-subnet"></a>Een ander subnet opgeven

Als u een subnet voor uw load balancer, voegt u de *aantekening azure-load-balancer-internal-subnet* toe aan uw service. Het opgegeven subnet moet zich in hetzelfde virtuele netwerk als uw AKS-cluster. Bij de geïmplementeerde load balancer *EXTERNAL-IP-adres* maakt deel uit van het opgegeven subnet.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: internal-app
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
    service.beta.kubernetes.io/azure-load-balancer-internal-subnet: "apps-subnet"
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: internal-app
```

## <a name="delete-the-load-balancer"></a>De load balancer

Wanneer alle services die gebruikmaken van de interne load balancer verwijderd, worden de load balancer zelf ook verwijderd.

U kunt een service ook rechtstreeks verwijderen, net als bij elke Kubernetes-resource, zoals , waarmee ook de onderliggende `kubectl delete service internal-app` Azure-load balancer.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over Kubernetes-services vindt u in de [Kubernetes Services-documentatie.][kubernetes-services]

<!-- LINKS - External -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubernetes-services]: https://kubernetes.io/docs/concepts/services-networking/service/
[aks-engine]: https://github.com/Azure/aks-engine

<!-- LINKS - Internal -->
[advanced-networking]: configure-azure-cni.md
[az-aks-show]: /cli/azure/aks#az_aks_show
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
[azure-lb-comparison]: ../load-balancer/skus.md
[use-kubenet]: configure-kubenet.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[aks-sp]: kubernetes-service-principal.md#delegate-access-to-other-azure-resources
[different-subnet]: #specify-a-different-subnet