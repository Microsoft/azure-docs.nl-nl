---
title: Statisch IP-adres gebruiken voor uitgaand verkeer
titleSuffix: Azure Kubernetes Service
description: Meer informatie over het maken en gebruiken van een statisch openbaar IP-adres voor uitgaand verkeer in een Azure Kubernetes service (AKS)-cluster
services: container-service
ms.topic: article
ms.date: 03/16/2021
ms.openlocfilehash: e1f81bf4c4d35108557449a8bebd126bdf744191
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104592367"
---
# <a name="use-a-static-public-ip-address-for-egress-traffic-with-a-basic-sku-load-balancer-in-azure-kubernetes-service-aks"></a>Een statisch openbaar IP-adres gebruiken voor uitgaand verkeer met een *basis* -SKU Load Balancer in azure Kubernetes service (AKS)

Het uitgaande IP-adres van een Azure Kubernetes service-cluster (AKS) wordt standaard wille keurig toegewezen. Deze configuratie is niet ideaal wanneer u bijvoorbeeld een IP-adres voor toegang tot externe services moet identificeren. In plaats daarvan moet u mogelijk een statisch IP-adres toewijzen dat moet worden toegevoegd aan een allowlist voor toegang tot de service.

In dit artikel wordt beschreven hoe u een statisch openbaar IP-adres maakt en gebruikt voor gebruik met uitgaand verkeer in een AKS-cluster.

## <a name="before-you-begin"></a>Voordat u begint

In dit artikel wordt ervan uitgegaan dat u de Azure Basic-Load Balancer gebruikt.  We raden u aan [Azure Standard Load Balancer](../load-balancer/load-balancer-overview.md)te gebruiken en u kunt meer geavanceerde functies gebruiken voor het [beheren van AKS](./limit-egress-traffic.md)uitgaand verkeer.

In dit artikel wordt ervan uitgegaan dat u beschikt over een bestaand AKS-cluster. Als u een AKS-cluster nodig hebt, raadpleegt u de AKS Quick Start [met behulp van de Azure cli][aks-quickstart-cli] of [met behulp van de Azure Portal][aks-quickstart-portal].

Ook moet de Azure CLI-versie 2.0.59 of hoger zijn geïnstalleerd en geconfigureerd. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren][install-azure-cli] als u de CLI wilt installeren of een upgrade wilt uitvoeren.

> [!IMPORTANT]
> In dit artikel wordt gebruikgemaakt van de *basis* -SKU Load Balancer met één knooppunt groep. Deze configuratie is niet beschikbaar voor meerdere knooppunt groepen, aangezien de *basis* -SKU Load Balancer niet wordt ondersteund met meerdere knooppunt groepen. Zie [een open bare Standard Load Balancer gebruiken in azure Kubernetes service (AKS)][slb] voor meer informatie over het gebruik van de *standaard* -SKU Load Balancer.

## <a name="egress-traffic-overview"></a>Overzicht van uitgaand verkeer

Uitgaand verkeer van een AKS-cluster volgt [Azure Load Balancer conventies][outbound-connections]. Voordat de eerste Kubernetes-service van het type `LoadBalancer` wordt gemaakt, maken de agent knooppunten in een AKS-cluster geen deel uit van een Azure Load Balancer groep. In deze configuratie hebben de knoop punten geen openbaar IP-adres op exemplaar niveau. Azure zet de uitgaande stroom om naar een IP-adres van een open bare bron dat niet configureerbaar of deterministisch is.

Zodra een Kubernetes-service van het type `LoadBalancer` is gemaakt, worden er agent knooppunten toegevoegd aan een Azure Load Balancer groep. Load Balancer Basic kiest voor een eenmalige front-end voor uitgaande stromen wanneer meerdere (open bare) IP-frontends kandidaten voor uitgaande stromen zijn. Deze selectie kan niet worden geconfigureerd. u kunt het selectie algoritme het beste wille keurig beschouwen. Dit open bare IP-adres is alleen geldig voor de levens duur van die resource. Als u de Kubernetes Load Balancer-service verwijdert, worden de gekoppelde load balancer en het IP-adres ook verwijderd. Als u een specifiek IP-adres wilt toewijzen of een IP-adres voor opnieuw geïmplementeerde Kubernetes-Services wilt behouden, kunt u een statisch openbaar IP-adres maken en gebruiken.

## <a name="create-a-static-public-ip"></a>Een statisch openbaar IP-adres maken

Haal de naam van de resource groep op met de opdracht [AZ AKS show][az-aks-show] en voeg de `--query nodeResourceGroup` query parameter toe. In het volgende voor beeld wordt de resource groep node opgehaald voor de AKS-cluster naam *myAKSCluster* in de naam van de resource groep *myResourceGroup*:

```azurecli-interactive
$ az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_eastus
```

Maak nu een statisch openbaar IP-adres met de opdracht [AZ Network Public IP Create][az-network-public-ip-create] . Geef de naam van de resource groep voor het knoop punt op die u hebt verkregen in de vorige opdracht en klik vervolgens op een naam voor de IP-adres bron, zoals *myAKSPublicIP*:

```azurecli-interactive
az network public-ip create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --name myAKSPublicIP \
    --allocation-method static
```

Het IP-adres wordt weer gegeven, zoals wordt weer gegeven in de volgende verkorte voorbeeld uitvoer:

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

U kunt later het open bare IP-adres ophalen met de opdracht [AZ Network Public-IP List][az-network-public-ip-list] . Geef de naam van de knooppunt resource groep op en voer vervolgens een query uit voor het *IP-adres* , zoals wordt weer gegeven in het volgende voor beeld:

```azurecli-interactive
$ az network public-ip list --resource-group MC_myResourceGroup_myAKSCluster_eastus --query [0].ipAddress --output tsv

40.121.183.52
```

## <a name="create-a-service-with-the-static-ip"></a>Een service maken met het statische IP-adres

Als u een service met het statische open bare IP-adres wilt maken, voegt `loadBalancerIP` u de eigenschap en de waarde van het statische open bare IP-adres toe aan het yaml-manifest. Maak een bestand `egress-service.yaml` met de naam en kopieer de volgende YAML. Geef uw eigen open bare IP-adres op dat u in de vorige stap hebt gemaakt.

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

Maak de service en de implementatie met behulp van de `kubectl apply` opdracht.

```console
kubectl apply -f egress-service.yaml
```

Met deze service wordt een nieuw frontend-IP-adres geconfigureerd op de Azure Load Balancer. Als er geen andere IP-adressen zijn geconfigureerd, moet dit adres nu worden gebruikt voor **alle** uitgangs verkeer. Wanneer er meerdere adressen zijn geconfigureerd op de Azure Load Balancer, zijn een van deze open bare IP-adressen een kandidaat voor uitgaande stromen en wordt er een wille keurige selectie geselecteerd.

## <a name="verify-egress-address"></a>Uitgangs adres controleren

Als u wilt controleren of het statische open bare IP-adres wordt gebruikt, kunt u de DNS-zoek service gebruiken zoals `checkip.dyndns.org` .

Starten en koppelen aan een Basic *Debian* Pod:

```console
kubectl run -it --rm aks-ip --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11
```

Om toegang te krijgen tot een website vanuit de container, gebruikt `apt-get` u om `curl` in de container te installeren.

```console
apt-get update && apt-get install curl -y
```

Gebruik nu krul om toegang te krijgen tot de *checkip.dyndns.org* -site. Het uitgaande IP-adres wordt weer gegeven, zoals wordt weer gegeven in de volgende voorbeeld uitvoer. Dit IP-adres komt overeen met het statische open bare IP-adres dat is gemaakt en gedefinieerd voor de loadBalancer-service:

```console
$ curl -s checkip.dyndns.org

<html><head><title>Current IP Check</title></head><body>Current IP Address: 40.121.183.52</body></html>
```

## <a name="next-steps"></a>Volgende stappen

U kunt in plaats daarvan een ingangs controller gebruiken om te voor komen dat meerdere open bare IP-adressen op de Azure Load Balancer worden bewaard. Ingangs controllers bieden extra voor delen, zoals SSL/TLS-beëindiging, ondersteuning voor het opnieuw schrijven van URI'S en de upstream van SSL/TLS-versleuteling. Zie [een Basic ingress-controller maken in AKS][ingress-aks-cluster]voor meer informatie.

<!-- LINKS - internal -->
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[az-network-public-ip-list]: /cli/azure/network/public-ip#az-network-public-ip-list
[az-aks-show]: /cli/azure/aks#az-aks-show
[azure-cli-install]: /cli/azure/install-azure-cli
[ingress-aks-cluster]: ./ingress-basic.md
[outbound-connections]: ../load-balancer/load-balancer-outbound-connections.md#scenarios
[public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[slb]: load-balancer-standard.md
