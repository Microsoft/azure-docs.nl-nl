---
title: Statisch IP-adres gebruiken met load balancer
titleSuffix: Azure Kubernetes Service
description: Meer informatie over het maken en gebruiken van een statisch IP-adres met de Azure Kubernetes service (AKS) load balancer.
services: container-service
ms.topic: article
ms.date: 11/14/2020
ms.openlocfilehash: 102df48ca22fb996e0f4d9c402b8ce8f0fa80f2c
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102509469"
---
# <a name="use-a-static-public-ip-address-and-dns-label-with-the-azure-kubernetes-service-aks-load-balancer"></a>Gebruik een statisch openbaar IP-adres en een DNS-label met de Azure Kubernetes-service (AKS) load balancer

Het open bare IP-adres dat is toegewezen aan een load balancer bron die is gemaakt met een AKS-cluster, is standaard alleen geldig voor de levens duur van die resource. Als u de Kubernetes-service verwijdert, worden ook de gekoppelde load balancer en het IP-adres verwijderd. Als u een specifiek IP-adres wilt toewijzen of een IP-adres voor opnieuw geïmplementeerde Kubernetes-Services wilt behouden, kunt u een statisch openbaar IP-adres maken en gebruiken.

In dit artikel wordt beschreven hoe u een statisch openbaar IP-adres maakt en dit toewijst aan uw Kubernetes-service.

## <a name="before-you-begin"></a>Voordat u begint

In dit artikel wordt ervan uitgegaan dat u beschikt over een bestaand AKS-cluster. Als u een AKS-cluster nodig hebt, raadpleegt u de AKS Quick Start [met behulp van de Azure cli][aks-quickstart-cli] of [met behulp van de Azure Portal][aks-quickstart-portal].

Ook moet de Azure CLI-versie 2.0.59 of hoger zijn geïnstalleerd en geconfigureerd. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren][install-azure-cli] als u de CLI wilt installeren of een upgrade wilt uitvoeren.

In dit artikel wordt gebruikgemaakt van een *standaard* SKU-IP-adres met een *standaard* SKU-Load Balancer. Zie [IP-adres typen en toewijzings methoden in azure][ip-sku]voor meer informatie.

## <a name="create-a-static-ip-address"></a>Een statisch IP-adres maken

Maak een statisch openbaar IP-adres met de opdracht [AZ Network open bare IP Create][az-network-public-ip-create] . Hieronder maakt u een statische IP-bron met de naam *myAKSPublicIP* in de resource groep *myResourceGroup* :

```azurecli-interactive
az network public-ip create \
    --resource-group myResourceGroup \
    --name myAKSPublicIP \
    --sku Standard \
    --allocation-method static
```

> [!NOTE]
> Als u een *basis* -SKU gebruikt Load Balancer in uw AKS-cluster, gebruikt u *Basic* voor de *SKU* -para meter bij het definiëren van een openbaar IP-adres. Alleen *Basic* SKU-IP-adressen werken met de *basis* -SKU Load Balancer en alleen *standaard* -SKU-ip's werken met *Standard* SKU load balancers. 

Het IP-adres wordt weer gegeven, zoals wordt weer gegeven in de volgende verkorte voorbeeld uitvoer:

```json
{
  "publicIp": {
    ...
    "ipAddress": "40.121.183.52",
    ...
  }
}
```

U kunt later het open bare IP-adres ophalen met de opdracht [AZ Network Public-IP List][az-network-public-ip-list] . Geef de naam op van de knooppunt resource groep en het open bare IP-adres dat u hebt gemaakt, en voer een query uit voor het *IP* -adres, zoals weer gegeven in het volgende voor beeld:

```azurecli-interactive
$ az network public-ip show --resource-group myResourceGroup --name myAKSPublicIP --query ipAddress --output tsv

40.121.183.52
```

## <a name="create-a-service-using-the-static-ip-address"></a>Een service maken met behulp van het statische IP-adres

Voordat u een service maakt, moet u ervoor zorgen dat de cluster-id die wordt gebruikt door het AKS-cluster gedelegeerde machtigingen heeft voor de andere resource groep. Bijvoorbeeld:

```azurecli-interactive
az role assignment create \
    --assignee <Client ID> \
    --role "Network Contributor" \
    --scope /subscriptions/<subscription id>/resourceGroups/<resource group name>
```

> [!IMPORTANT]
> Als u uw uitgaande IP-adres hebt aangepast, moet u ervoor zorgen dat uw cluster identiteit machtigingen heeft voor zowel het uitgaande open bare IP-adres als dit inkomende open bare IP.

Als u een *Load Balancer* -service met het statische open bare IP-adres wilt maken, voegt `loadBalancerIP` u de eigenschap en de waarde van het statische open bare IP-adres toe aan het yaml-manifest. Maak een bestand `load-balancer-service.yaml` met de naam en kopieer de volgende YAML. Geef uw eigen open bare IP-adres op dat u in de vorige stap hebt gemaakt. In het volgende voor beeld wordt de aantekening ook ingesteld op de resource groep met de naam *myResourceGroup*. Geef de naam van uw eigen resource groep op.

```yaml
apiVersion: v1
kind: Service
metadata:
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-resource-group: myResourceGroup
  name: azure-load-balancer
spec:
  loadBalancerIP: 40.121.183.52
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-load-balancer
```

Maak de service en de implementatie met behulp van de `kubectl apply` opdracht.

```console
kubectl apply -f load-balancer-service.yaml
```

## <a name="apply-a-dns-label-to-the-service"></a>Een DNS-label op de service Toep assen

Als uw service gebruikmaakt van een dynamisch of statisch openbaar IP-adres, kunt u de annotatie van de service gebruiken `service.beta.kubernetes.io/azure-dns-label-name` om een openbaar DNS-label in te stellen. Hiermee publiceert u een Fully Qualified Domain Name voor uw service met behulp van de open bare DNS-servers van Azure en het domein op het hoogste niveau. De annotatie waarde moet uniek zijn binnen de Azure-locatie. Daarom is het raadzaam om een voldoende gekwalificeerd label te gebruiken.   

In azure wordt vervolgens automatisch een standaard subnet toegevoegd, zoals `<location>.cloudapp.azure.com` (waarbij locatie de regio is die u hebt geselecteerd), naar de naam die u opgeeft, om de volledig gekwalificeerde DNS-naam te maken. Bijvoorbeeld:

```yaml
apiVersion: v1
kind: Service
metadata:
  annotations:
    service.beta.kubernetes.io/azure-dns-label-name: myserviceuniquelabel
  name: azure-load-balancer
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-load-balancer
```

> [!NOTE] 
> Zie [Azure DNS][azure-dns-zone] en het [externe DNS][external-dns] -project om de service op uw eigen domein te publiceren.

## <a name="troubleshoot"></a>Problemen oplossen

Als het statische IP-adres dat is gedefinieerd in de eigenschap *loadBalancerIP* van het Kubernetes-service manifest niet bestaat of niet is gemaakt in de resource groep node en er geen aanvullende delegaties zijn geconfigureerd, mislukt het maken van de Load Balancer-service. Als u problemen wilt oplossen, controleert u de gebeurtenissen voor het maken van een service met de opdracht [kubectl beschrijven][kubectl-describe] . Geef de naam van de service op zoals opgegeven in het YAML-manifest, zoals wordt weer gegeven in het volgende voor beeld:

```console
kubectl describe service azure-load-balancer
```

Informatie over de Kubernetes-service resource wordt weer gegeven. De *gebeurtenissen* aan het einde van de volgende voorbeeld uitvoer geven aan dat het door de *gebruiker opgegeven IP-adres niet is gevonden*. Controleer in deze scenario's of u het statische open bare IP-adres in de knooppunt resource groep hebt gemaakt en of het IP-adres dat is opgegeven in het Kubernetes-service manifest juist is.

```
Name:                     azure-load-balancer
Namespace:                default
Labels:                   <none>
Annotations:              <none>
Selector:                 app=azure-load-balancer
Type:                     LoadBalancer
IP:                       10.0.18.125
IP:                       40.121.183.52
Port:                     <unset>  80/TCP
TargetPort:               80/TCP
NodePort:                 <unset>  32582/TCP
Endpoints:                <none>
Session Affinity:         None
External Traffic Policy:  Cluster
Events:
  Type     Reason                      Age               From                Message
  ----     ------                      ----              ----                -------
  Normal   CreatingLoadBalancer        7s (x2 over 22s)  service-controller  Creating load balancer
  Warning  CreatingLoadBalancerFailed  6s (x2 over 12s)  service-controller  Error creating load balancer (will retry): Failed to create load balancer for service default/azure-load-balancer: user supplied IP Address 40.121.183.52 was not found
```

## <a name="next-steps"></a>Volgende stappen

Als u meer controle over het netwerk verkeer voor uw toepassingen wilt, kunt u in plaats daarvan [een ingangs controller maken][aks-ingress-basic]. U kunt ook [een ingangs controller met een statisch openbaar IP-adres maken][aks-static-ingress].

<!-- LINKS - External -->
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[azure-dns-zone]: https://azure.microsoft.com/services/dns/
[external-dns]: https://github.com/kubernetes-sigs/external-dns

<!-- LINKS - Internal -->
[aks-faq-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[az-network-public-ip-list]: /cli/azure/network/public-ip#az-network-public-ip-list
[az-aks-show]: /cli/azure/aks#az-aks-show
[aks-ingress-basic]: ingress-basic.md
[aks-static-ingress]: ingress-static-ip.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[ip-sku]: ../virtual-network/public-ip-addresses.md#sku
