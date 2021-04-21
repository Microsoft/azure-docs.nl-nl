---
title: Statisch IP-adres gebruiken voor het verkeer voorgressie
titleSuffix: Azure Kubernetes Service
description: Meer informatie over het maken en gebruiken van een statisch openbaar IP-adres voor Azure Kubernetes Service (AKS)-cluster
services: container-service
ms.topic: article
ms.date: 03/16/2021
ms.openlocfilehash: 7cff3f5d66bb9872a0c949c6237150f8b69c9fa7
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773005"
---
# <a name="use-a-static-public-ip-address-for-egress-traffic-with-a-basic-sku-load-balancer-in-azure-kubernetes-service-aks"></a>Gebruik een statisch openbaar IP-adres voor het verkeer voorgressie met een *Basic* SKU-load balancer in Azure Kubernetes Service (AKS)

Standaard wordt het ip-adres van een Azure Kubernetes Service AKS-cluster willekeurig toegewezen. Deze configuratie is niet ideaal wanneer u bijvoorbeeld een IP-adres moet identificeren voor toegang tot externe services. In plaats daarvan moet u mogelijk een statisch IP-adres toewijzen dat moet worden toegevoegd aan een allowlist voor servicetoegang.

In dit artikel wordt beschreven hoe u een statisch openbaar IP-adres maakt en gebruikt voor gebruik met het verkeer voorgressie in een AKS-cluster.

## <a name="before-you-begin"></a>Voordat u begint

In dit artikel wordt ervan uitgenomen dat u de Azure Basic-Load Balancer.  We raden u aan de [Azure Standard Load Balancer](../load-balancer/load-balancer-overview.md)en u kunt meer geavanceerde functies gebruiken voor het beheren van [het AKS-verkeer voor egressie.](./limit-egress-traffic.md)

In dit artikel wordt ervan uitgenomen dat u een bestaand AKS-cluster hebt. Als u een AKS-cluster nodig hebt, bekijkt u de AKS-quickstart met behulp van [de Azure CLI][aks-quickstart-cli] of met behulp van de [Azure Portal][aks-quickstart-portal].

Ook moet Azure CLI versie 2.0.59 of hoger zijn geïnstalleerd en geconfigureerd. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren][install-azure-cli] als u de CLI wilt installeren of een upgrade wilt uitvoeren.

> [!IMPORTANT]
> In dit artikel wordt de *Basis-SKU* load balancer met één knooppuntgroep. Deze configuratie is niet beschikbaar voor  meerdere knooppuntgroepen omdat de basis-SKU-load balancer niet wordt ondersteund met meerdere knooppuntgroepen. Zie [Use a public Standard Load Balancer in Azure Kubernetes Service (AKS) (Een][slb] openbare SKU gebruiken in Azure Kubernetes Service) voor meer informatie over het gebruik van de *Standard* SKU load balancer.

## <a name="egress-traffic-overview"></a>Overzicht van het egress-verkeer

Uitgaand verkeer van een AKS-cluster volgt [Azure Load Balancer conventies][outbound-connections]. Voordat de eerste Kubernetes-service van het type wordt gemaakt, maken de agentknooppunten in een AKS-cluster geen deel uit `LoadBalancer` van Azure Load Balancer groep. In deze configuratie hebben de knooppunten geen openbaar IP-adres op exemplaarniveau. Azure vertaalt de uitgaande stroom naar een openbaar bron-IP-adres dat niet configureerbaar of deterministisch is.

Zodra een Kubernetes-service van het type `LoadBalancer` is gemaakt, worden agentknooppunten toegevoegd aan een Azure Load Balancer groep. Load Balancer Basic kiest één front-end die moet worden gebruikt voor uitgaande stromen wanneer meerdere (openbare) IP-front-enden in de kandidaten zijn voor uitgaande stromen. Deze selectie kan niet worden geconfigureerd en u moet het selectiealgoritme als willekeurig beschouwen. Dit openbare IP-adres is alleen geldig voor de levensduur van die resource. Als u de Kubernetes LoadBalancer-service verwijdert, worden load balancer en IP-adres ook verwijderd. Als u een specifiek IP-adres wilt toewijzen of een IP-adres wilt behouden voor opnieuw geïmplementeerde Kubernetes-services, kunt u een statisch openbaar IP-adres maken en gebruiken.

## <a name="create-a-static-public-ip"></a>Een statisch openbaar IP-adres maken

Haal de naam van de resourcegroep op met [de opdracht az aks show][az-aks-show] en voeg de `--query nodeResourceGroup` queryparameter toe. In het volgende voorbeeld wordt de knooppuntresourcegroep voor de AKS-clusternaam *myAKSCluster* in de naam van de resourcegroep *myResourceGroup:*

```azurecli-interactive
$ az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_eastus
```

Maak nu een statisch openbaar IP-adres met de [opdracht az network public ip create.][az-network-public-ip-create] Geef de naam van de knooppuntresourcegroep op die u in de vorige opdracht hebt verkregen en geef vervolgens een naam op voor de IP-adresresource, *zoals myAKSPublicIP:*

```azurecli-interactive
az network public-ip create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --name myAKSPublicIP \
    --allocation-method static
```

Het IP-adres wordt weergegeven, zoals wordt weergegeven in de volgende verkorte voorbeelduitvoer:

```json
{
  "publicIp": {
    "dnsSettings": null,
    "etag": "W/\"6b6fb15c-5281-4f64-b332-8f68f46e1358\"",
    "id": "/subscriptions/<SubscriptionID>/resourceGroups/MC_myResourceGroup_myAKSCluster_eastus/providers/Microsoft.Network/publicIPAddresses/myAKSPublicIP",
    "idleTimeoutInMinutes": 4,
    "ipAddress": "40.121.183.52",
    [..]
  }
```

U kunt het openbare IP-adres later op halen met de [opdracht az network public-ip list.][az-network-public-ip-list] Geef de naam van de knooppuntresourcegroep op en zoek vervolgens naar *het ipAddress,* zoals wordt weergegeven in het volgende voorbeeld:

```azurecli-interactive
$ az network public-ip list --resource-group MC_myResourceGroup_myAKSCluster_eastus --query [0].ipAddress --output tsv

40.121.183.52
```

## <a name="create-a-service-with-the-static-ip"></a>Een service maken met het statische IP-adres

Als u een service wilt maken met het statische openbare IP-adres, voegt u de eigenschap en de waarde van het statische openbare IP-adres toe `loadBalancerIP` aan het YAML-manifest. Maak een bestand met `egress-service.yaml` de naam en kopieer het in de volgende YAML. Geef uw eigen openbare IP-adres op dat u in de vorige stap hebt gemaakt.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-egress
spec:
  loadBalancerIP: 40.121.183.52
  type: LoadBalancer
  ports:
  - port: 80
```

Maak de service en implementatie met de `kubectl apply` opdracht .

```console
kubectl apply -f egress-service.yaml
```

Deze service configureert een nieuw front-end-IP-adres op Azure Load Balancer. Als u geen andere IP-adressen hebt geconfigureerd, moet nu al het **verkeer** dat de egress gebruikt dit adres gebruiken. Wanneer meerdere adressen zijn geconfigureerd op de Azure Load Balancer, zijn al deze openbare IP-adressen een kandidaat voor uitgaande stromen en wordt er een willekeurig geselecteerd.

## <a name="verify-egress-address"></a>Het adres van het egress-adres controleren

Als u wilt controleren of het statische openbare IP-adres wordt gebruikt, kunt u de DNS-zoekservice gebruiken, zoals `checkip.dyndns.org` .

Start en koppel aan een eenvoudige *Debian-pod:*

```console
kubectl run -it --rm aks-ip --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11
```

Als u vanuit de container toegang wilt krijgen tot een website, gebruikt `apt-get` u om deze in de container te `curl` installeren.

```console
apt-get update && apt-get install curl -y
```

Gebruik nu curl om toegang te krijgen tot *checkip.dyndns.org* site. Het IP-adres van het egress wordt weergegeven, zoals wordt weergegeven in de volgende voorbeelduitvoer. Dit IP-adres komt overeen met het statische openbare IP-adres dat is gemaakt en gedefinieerd voor de loadBalancer-service:

```console
$ curl -s checkip.dyndns.org

<html><head><title>Current IP Check</title></head><body>Current IP Address: 40.121.183.52</body></html>
```

## <a name="next-steps"></a>Volgende stappen

Als u wilt voorkomen dat er meerdere openbare IP-adressen op de Azure Load Balancer, kunt u in plaats daarvan een controller voor ingress gebruiken. Ingress-controllers bieden extra voordelen, zoals SSL/TLS-beëindiging, ondersteuning voor herschrijven van URI's en upstream SSL/TLS-versleuteling. Zie Create [a basic ingress controller in AKS (Een eenvoudige controller voor ingress maken in AKS) voor meer informatie.][ingress-aks-cluster]

<!-- LINKS - internal -->
[az-network-public-ip-create]: /cli/azure/network/public-ip#az_network_public_ip_create
[az-network-public-ip-list]: /cli/azure/network/public-ip#az_network_public_ip_list
[az-aks-show]: /cli/azure/aks#az_aks_show
[azure-cli-install]: /cli/azure/install-azure-cli
[ingress-aks-cluster]: ./ingress-basic.md
[outbound-connections]: ../load-balancer/load-balancer-outbound-connections.md#scenarios
[public-ip-create]: /cli/azure/network/public-ip#az_network_public_ip_create
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[slb]: load-balancer-standard.md
