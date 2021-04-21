---
title: Statisch IP-adres gebruiken met load balancer
titleSuffix: Azure Kubernetes Service
description: Meer informatie over het maken en gebruiken van een statisch IP-adres met de Azure Kubernetes Service (AKS) load balancer.
services: container-service
ms.topic: article
ms.date: 11/14/2020
ms.openlocfilehash: bb1e5691027a4bd86b57390e12259ac165ca9ed8
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769513"
---
# <a name="use-a-static-public-ip-address-and-dns-label-with-the-azure-kubernetes-service-aks-load-balancer"></a>Gebruik een statisch openbaar IP-adres en DNS-label met het Azure Kubernetes Service (AKS) load balancer

Het openbare IP-adres dat is toegewezen aan een load balancer resource die is gemaakt door een AKS-cluster, is standaard alleen geldig voor de levensduur van die resource. Als u de Kubernetes-service verwijdert, worden load balancer en IP-adres ook verwijderd. Als u een specifiek IP-adres wilt toewijzen of een IP-adres wilt behouden voor opnieuw geïmplementeerde Kubernetes-services, kunt u een statisch openbaar IP-adres maken en gebruiken.

In dit artikel wordt beschreven hoe u een statisch openbaar IP-adres maakt en dit toewijst aan uw Kubernetes-service.

## <a name="before-you-begin"></a>Voordat u begint

In dit artikel wordt ervan uitgenomen dat u een bestaand AKS-cluster hebt. Als u een AKS-cluster nodig hebt, bekijkt u de AKS-quickstart met behulp van [de Azure CLI][aks-quickstart-cli] of met behulp van de [Azure Portal][aks-quickstart-portal].

U moet ook Azure CLI versie 2.0.59 of hoger installeren en configureren. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren][install-azure-cli] als u de CLI wilt installeren of een upgrade wilt uitvoeren.

In dit artikel wordt beschreven *hoe u een Standard* SKU IP gebruikt met een *Standard* SKU load balancer. Zie IP-adrestypen en [toewijzingsmethoden in Azure voor meer informatie.][ip-sku]

## <a name="create-a-static-ip-address"></a>Een statisch IP-adres maken

Maak een statisch openbaar IP-adres met de [opdracht az network public ip create.][az-network-public-ip-create] Hiermee maakt u een statische IP-resource met de *naam myAKSPublicIP* in de resourcegroep *myResourceGroup:*

```azurecli-interactive
az network public-ip create \
    --resource-group myResourceGroup \
    --name myAKSPublicIP \
    --sku Standard \
    --allocation-method static
```

> [!NOTE]
> Als u een  Basic SKU-load balancer in uw AKS-cluster, gebruikt u *Basic* voor de *SKU-parameter* bij het definiëren van een openbaar IP-adres. Alleen *BASIC* SKU-IP's werken met de *Basic* SKU-load balancer en alleen *Standard* SKU-IP's werken met *Standard* SKU load balancers. 

Het IP-adres wordt weergegeven, zoals wordt weergegeven in de volgende verkorte voorbeelduitvoer:

```json
{
  "publicIp": {
    ...
    "ipAddress": "40.121.183.52",
    ...
  }
}
```

U kunt het openbare IP-adres later op halen met de [opdracht az network public-ip list.][az-network-public-ip-list] Geef de naam op van de knooppuntresourcegroep en het openbare IP-adres dat u hebt gemaakt en zoek naar *het ipAddress,* zoals wordt weergegeven in het volgende voorbeeld:

```azurecli-interactive
$ az network public-ip show --resource-group myResourceGroup --name myAKSPublicIP --query ipAddress --output tsv

40.121.183.52
```

## <a name="create-a-service-using-the-static-ip-address"></a>Een service maken met behulp van het statische IP-adres

Voordat u een service maakt, moet u ervoor zorgen dat de clusteridentiteit die wordt gebruikt door het AKS-cluster machtigingen heeft gedelegeerd aan de andere resourcegroep. Bijvoorbeeld:

```azurecli-interactive
az role assignment create \
    --assignee <Client ID> \
    --role "Network Contributor" \
    --scope /subscriptions/<subscription id>/resourceGroups/<resource group name>
```

> [!IMPORTANT]
> Als u uw uitgaande IP hebt aangepast, moet u ervoor zorgen dat uw clusteridentiteit machtigingen heeft voor zowel het uitgaande openbare IP-adres als dit inkomende openbare IP-adres.

Als u een *LoadBalancer-service* wilt maken met het statische openbare IP-adres, voegt u de eigenschap en de waarde van het statische openbare IP-adres toe `loadBalancerIP` aan het YAML-manifest. Maak een bestand met de `load-balancer-service.yaml` naam en kopieer dit in de volgende YAML. Geef uw eigen openbare IP-adres op dat u in de vorige stap hebt gemaakt. In het volgende voorbeeld wordt ook de aantekening voor de resourcegroep met de *naam myResourceGroup.* Geef de naam van uw eigen resourcegroep op.

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

Maak de service en implementatie met de `kubectl apply` opdracht .

```console
kubectl apply -f load-balancer-service.yaml
```

## <a name="apply-a-dns-label-to-the-service"></a>Een DNS-label toepassen op de service

Als uw service een dynamisch of statisch openbaar IP-adres gebruikt, kunt u de serviceaantekening gebruiken om een openbaar `service.beta.kubernetes.io/azure-dns-label-name` DNS-label in te stellen. Hiermee publiceert u een volledig gekwalificeerde domeinnaam voor uw service met behulp van de openbare DNS-servers en het domein op het hoogste niveau van Azure. De aantekeningwaarde moet uniek zijn binnen de Azure-locatie, dus het wordt aanbevolen een voldoende gekwalificeerd label te gebruiken.   

Azure zal vervolgens automatisch een standaardsubnet, zoals (waarbij locatie de regio is die u hebt geselecteerd) aan de naam die u op geeft, aan de door u op te geven, om de volledig gekwalificeerde `<location>.cloudapp.azure.com` DNS-naam te maken. Bijvoorbeeld:

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
> Als u de service in uw eigen domein wilt publiceren, Azure DNS [en][azure-dns-zone] het [project external-dns.][external-dns]

## <a name="troubleshoot"></a>Problemen oplossen

Als het statische IP-adres dat is gedefinieerd in de *eigenschap loadBalancerIP* van het Kubernetes-servicemanifest niet bestaat of niet is gemaakt in de knooppuntresourcegroep en er geen aanvullende delegaties zijn geconfigureerd, mislukt het maken van de load balancer-service. Als u problemen wilt oplossen, bekijkt u de gebeurtenissen voor het maken van de service met de [opdracht kubectl describe.][kubectl-describe] Geef de naam van de service op zoals is opgegeven in het YAML-manifest, zoals wordt weergegeven in het volgende voorbeeld:

```console
kubectl describe service azure-load-balancer
```

Er wordt informatie over de Kubernetes-serviceresource weergegeven. De *gebeurtenissen* aan het einde van de volgende voorbeelduitvoer geven aan dat de *door de gebruiker opgegeven IP-adres niet is gevonden.* In deze scenario's controleert u of u het statische openbare IP-adres in de knooppuntresourcegroep hebt gemaakt en of het IP-adres dat is opgegeven in het Kubernetes-servicemanifest juist is.

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

Voor extra controle over het netwerkverkeer naar uw toepassingen kunt u in plaats daarvan een [controller voor binnenverkeer maken.][aks-ingress-basic] U kunt ook [een controller voor ingress maken met een statisch openbaar IP-adres.][aks-static-ingress]

<!-- LINKS - External -->
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[azure-dns-zone]: https://azure.microsoft.com/services/dns/
[external-dns]: https://github.com/kubernetes-sigs/external-dns

<!-- LINKS - Internal -->
[aks-faq-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks
[az-network-public-ip-create]: /cli/azure/network/public-ip#az_network_public_ip_create
[az-network-public-ip-list]: /cli/azure/network/public-ip#az_network_public_ip_list
[az-aks-show]: /cli/azure/aks#az_aks_show
[aks-ingress-basic]: ingress-basic.md
[aks-static-ingress]: ingress-static-ip.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[ip-sku]: ../virtual-network/public-ip-addresses.md#sku
