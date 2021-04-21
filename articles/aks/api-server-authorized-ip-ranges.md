---
title: Geautoriseerde IP-adresbereiken van API-server in Azure Kubernetes Service (AKS)
description: Meer informatie over het beveiligen van uw cluster met behulp van een IP-adresbereik voor toegang tot de API-server in Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 09/21/2020
ms.openlocfilehash: 8fca3fe61e26a031e6ea09692c9ba0781bfca21f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769639"
---
# <a name="secure-access-to-the-api-server-using-authorized-ip-address-ranges-in-azure-kubernetes-service-aks"></a>Beveiligde toegang tot de API-server met behulp van geautoriseerde IP-adresbereiken in Azure Kubernetes Service (AKS)

In Kubernetes ontvangt de API-server aanvragen om acties uit te voeren in het cluster, zoals het maken van resources of het schalen van het aantal knooppunten. De API-server is de centrale manier om een cluster te gebruiken en te beheren. Om de clusterbeveiliging te verbeteren en aanvallen te minimaliseren, mag de API-server alleen toegankelijk zijn vanuit een beperkte set IP-adresbereiken.

In dit artikel wordt beschreven hoe u geautoriseerde IP-adresbereiken van de API-server gebruikt om te beperken welke IP-adressen en CIDR's toegang hebben tot het besturingsvlak.

## <a name="before-you-begin"></a>Voordat u begint

In dit artikel wordt beschreven hoe u een AKS-cluster maakt met behulp van de Azure CLI.

Azure CLI versie 2.0.76 of hoger moet zijn geïnstalleerd en geconfigureerd. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren][install-azure-cli] als u de CLI wilt installeren of een upgrade wilt uitvoeren.

### <a name="limitations"></a>Beperkingen

De functie Geautoriseerde IP-adresbereiken van de API-server heeft de volgende beperkingen:
- Op clusters die zijn gemaakt na geautoriseerde IP-adresbereiken van de API-server die in oktober 2019 uit de preview-versie zijn verplaatst, worden geautoriseerde IP-adresbereiken van de API-server alleen ondersteund op de standaard-SKU-load balancer.  Bestaande clusters met de *Basic* SKU load balancer- en API-server geautoriseerde IP-adresbereiken die zijn geconfigureerd, blijven gewoon werken, maar kunnen niet worden gemigreerd naar een *Standard* SKU-load balancer. Deze bestaande clusters blijven ook werken als hun Kubernetes-versie of besturingsvlak wordt bijgewerkt. Geautoriseerde IP-adresbereiken van de API-server worden niet ondersteund voor privéclusters.
- Deze functie is niet compatibel met clusters die gebruikmaken van [een openbaar IP-adres per knooppunt.](use-multiple-node-pools.md#assign-a-public-ip-per-node-for-your-node-pools)

## <a name="overview-of-api-server-authorized-ip-ranges"></a>Overzicht van geautoriseerde IP-adresbereiken van API-server

De Kubernetes-API-server is de manier waarop de onderliggende Kubernetes-API's beschikbaar worden gemaakt. Dit onderdeel biedt de interactie voor beheerhulpprogramma's, zoals `kubectl` of het Kubernetes-dashboard. AKS biedt een clusterbesturingsvlak met één tenant, met een toegewezen API-server. De API-server krijgt standaard een openbaar IP-adres toegewezen en u moet de toegang beheren met op kubernetes-rollen gebaseerd toegangsbeheer (Kubernetes RBAC) of Azure RBAC.

Als u de toegang tot de anderszins openbaar toegankelijke AKS-besturingsvlak/API-server wilt beveiligen, kunt u geautoriseerde IP-bereiken inschakelen en gebruiken. Met deze geautoriseerde IP-adresbereiken kunnen alleen gedefinieerde IP-adresbereiken communiceren met de API-server. Een aanvraag die wordt ingediend bij de API-server vanaf een IP-adres dat geen deel uitmaakt van deze geautoriseerde IP-adresbereiken, wordt geblokkeerd. Blijf Kubernetes RBAC of Azure RBAC gebruiken om gebruikers en de acties die ze aanvragen te autoreren.

Zie Kubernetes-kernconcepten voor AKS voor meer informatie over de API-server en andere [clusteronderdelen.][concepts-clusters-workloads]

## <a name="create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled"></a>Een AKS-cluster maken met geautoriseerde IP-adresbereiken van de API-server

Maak een cluster met [az aks create][az-aks-create] en geef de *`--api-server-authorized-ip-ranges`* parameter op om een lijst met geautoriseerde IP-adresbereiken op te geven. Deze IP-adresbereiken zijn meestal adresbereiken die worden gebruikt door uw on-premises netwerken of openbare IP-adressen. Wanneer u een CIDR-bereik opgeeft, begint u met het eerste IP-adres in het bereik. *137.117.106.90/29* is bijvoorbeeld een geldig bereik, maar zorg ervoor dat u het eerste IP-adres in het bereik opgeeft, zoals *137.117.106.88/29.*

> [!IMPORTANT]
> Uw cluster maakt standaard gebruik van de [Standard SKU-load balancer][standard-sku-lb] u kunt gebruiken om de uitgaande gateway te configureren. Wanneer u geautoriseerde IP-adresbereiken voor de API-server inschakelen tijdens het maken van het cluster, is het openbare IP-adres voor uw cluster standaard ook toegestaan naast de door u opgegeven bereik. Als u '' *of* geen waarde opgeeft voor , worden geautoriseerde IP-adresbereiken van de *`--api-server-authorized-ip-ranges`* API-server uitgeschakeld. Als u PowerShell gebruikt, gebruikt u *`--api-server-authorized-ip-ranges=""`* (met gelijkteken) om parseringsproblemen te voorkomen.

In het volgende voorbeeld wordt een cluster met één knooppunt met de naam *myAKSCluster* gemaakt in de resourcegroep met de *naam myResourceGroup* met geautoriseerde IP-adresbereiken van de API-server ingeschakeld. De toegestane IP-adresbereiken *zijn 73.140.245.0/24:*

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --vm-set-type VirtualMachineScaleSets \
    --load-balancer-sku standard \
    --api-server-authorized-ip-ranges 73.140.245.0/24 \
    --generate-ssh-keys
```

> [!NOTE]
> U moet deze reeksen toevoegen aan een lijst met toegestane reeksen:
> - Het openbare IP-adres van de firewall
> - Elk bereik dat netwerken vertegenwoordigt waarmee u het cluster gaat beheren
> - Als u Azure Dev Spaces gebruikt in uw AKS-cluster, moet u extra bereik toestaan op [basis van uw regio][dev-spaces-ranges].
>
> De bovengrens voor het aantal IP-adresbereiken dat u kunt opgeven, is 200.
>
> Het kan tot 2 minuten duren voor de regels zijn doorgegeven. Het kan tot die tijd duren wanneer u de verbinding test.

### <a name="specify-the-outbound-ips-for-the-standard-sku-load-balancer"></a>Geef de uitgaande IP's op voor de Standard SKU-load balancer

Als u bij het maken van een AKS-cluster de uitgaande IP-adressen of voorvoegsels voor het cluster opgeeft, zijn deze adressen of voorvoegsels ook toegestaan. Bijvoorbeeld:

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --vm-set-type VirtualMachineScaleSets \
    --load-balancer-sku standard \
    --api-server-authorized-ip-ranges 73.140.245.0/24 \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2> \
    --generate-ssh-keys
```

In het bovenstaande voorbeeld zijn alle INP's die zijn opgegeven in de parameter toegestaan, samen *`--load-balancer-outbound-ip-prefixes`* met de IP's in de *`--api-server-authorized-ip-ranges`* parameter .

In plaats daarvan kunt u de parameter opgeven om uitgaande load balancer *`--load-balancer-outbound-ip-prefixes`* IP-voorvoegsels toe te staan.

### <a name="allow-only-the-outbound-public-ip-of-the-standard-sku-load-balancer"></a>Alleen het uitgaande openbare IP-adres van de Standard SKU-load balancer

Wanneer u geautoriseerde IP-adresbereiken van de API-server inschakelen tijdens het maken van het cluster, wordt het uitgaande openbare IP-adres voor de Standard-SKU-load balancer voor uw cluster standaard ook toegestaan, naast de door u opgegeven bereik. Als u alleen het uitgaande openbare IP-adres van de Standard SKU-load balancer wilt toestaan, gebruikt u *0.0.0.0/32* bij het opgeven van de *`--api-server-authorized-ip-ranges`* parameter.

In het volgende voorbeeld is alleen het uitgaande openbare IP-adres van de Standard SKU load balancer toegestaan en hebt u alleen toegang tot de API-server vanaf de knooppunten in het cluster.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --vm-set-type VirtualMachineScaleSets \
    --load-balancer-sku standard \
    --api-server-authorized-ip-ranges 0.0.0.0/32 \
    --generate-ssh-keys
```

## <a name="update-a-clusters-api-server-authorized-ip-ranges"></a>Geautoriseerde IP-adresbereiken bijwerken voor de API-server van een cluster

Als u de geautoriseerde IP-bereiken van de API-server op een bestaand cluster wilt bijwerken, gebruikt u de opdracht [az aks update][az-aks-update] en gebruikt u de *`--api-server-authorized-ip-ranges`* parameters ,--load-balancer-outbound-ip-prefixes *, of *`--load-balancer-outbound-ips`* --load-balancer-outbound-ip-prefixes.*

In het volgende voorbeeld worden geautoriseerde IP-adresbereiken van de API-server bijgewerkt op het cluster met de naam *myAKSCluster* in de resourcegroep met de *naam myResourceGroup.* Het IP-adresbereik dat moet worden geautoriseerd, is *73.140.245.0/24:*

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges  73.140.245.0/24
```

U kunt ook *0.0.0.0/32* gebruiken bij het opgeven van de parameter zodat alleen het openbare IP-adres van de *`--api-server-authorized-ip-ranges`* Standard SKU-load balancer.

## <a name="disable-authorized-ip-ranges"></a>Geautoriseerde IP-adresbereiken uitschakelen

Als u geautoriseerde IP-adresbereiken wilt uitschakelen, gebruikt u [az aks update][az-aks-update] en geeft u een leeg bereik op om geautoriseerde IP-adresbereiken voor de API-server uit te schakelen. Bijvoorbeeld:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges ""
```

## <a name="find-existing-authorized-ip-ranges"></a>Bestaande geautoriseerde IP-adresbereiken zoeken

Als u IP-adresbereiken wilt zoeken die zijn geautoriseerd, gebruikt u [az aks show][az-aks-show] en geeft u de naam en resourcegroep van het cluster op. Bijvoorbeeld:

```azurecli-interactive
az aks show \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --query apiServerAccessProfile.authorizedIpRanges'
```

## <a name="update-disable-and-find-authorized-ip-ranges-using-azure-portal"></a>Geautoriseerde IP-adresbereiken bijwerken, uitschakelen en vinden met behulp Azure Portal

De bovenstaande bewerkingen voor het toevoegen, bijwerken, zoeken en uitschakelen van geautoriseerde IP-adresbereiken kunnen ook worden uitgevoerd in de Azure Portal. Voor toegang gaat u naar **Netwerken onder** **Instellingen** op de menublade van uw clusterresource.

:::image type="content" source="media/api-server-authorized-ip-ranges/ip-ranges-specified.PNG" alt-text="In een browser worden de netwerkinstellingen van de clusterresource Azure Portal weergegeven. De opties 'opgegeven IP-bereik instellen' en 'Opgegeven IP-adresbereiken' zijn gemarkeerd.":::

## <a name="how-to-find-my-ip-to-include-in---api-server-authorized-ip-ranges"></a>Mijn IP-adres vinden om op te nemen in `--api-server-authorized-ip-ranges` ?

U moet uw ontwikkelmachines, hulpprogramma's of automatiserings-IP-adressen toevoegen aan de AKS-clusterlijst met goedgekeurde IP-bereiken om van hieruit toegang te krijgen tot de API-server. 

Een andere optie is om een jumpbox te configureren met de benodigde hulpprogramma's binnen een afzonderlijk subnet in het virtuele netwerk van de firewall. Hier wordt ervan uit gegaan dat uw omgeving een firewall heeft met het desbetreffende netwerk en u de FIREWALL-IP's hebt toegevoegd aan geautoriseerde bereik. En als u geforceerd tunneling van het AKS-subnet naar het subnet Firewall hebt, dan is het ook prima om de jumpbox in het clustersubnet te hebben.

Voeg nog een IP-adres toe aan de goedgekeurde reeksen met de volgende opdracht.

```bash
# Retrieve your IP address
CURRENT_IP=$(dig @resolver1.opendns.com ANY myip.opendns.com +short)
# Add to AKS approved list
az aks update -g $RG -n $AKSNAME --api-server-authorized-ip-ranges $CURRENT_IP/32
```

>> [!NOTE]
> In het bovenstaande voorbeeld worden geautoriseerde IP-adresbereiken van de API-server op het cluster aan de API-server aangepast. Als u geautoriseerde IP-adresbereiken wilt uitschakelen, gebruikt u az aks update en geeft u een leeg bereik op. 

Een andere optie is om de onderstaande opdracht op Windows-systemen te gebruiken om het openbare IPv4-adres op te halen. U kunt ook de stappen in [Uw IP-adres zoeken gebruiken.](https://support.microsoft.com/en-gb/help/4026518/windows-10-find-your-ip-address)

```azurepowershell-interactive
Invoke-RestMethod http://ipinfo.io/json | Select -exp ip
```

U kunt dit adres ook vinden door in een internetbrowser te zoeken naar 'What is my IP address'.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geautoriseerde IP-adresbereiken ingeschakeld voor de API-server. Deze aanpak is een onderdeel van de manier waarop u een beveiligd AKS-cluster kunt uitvoeren.

Zie Beveiligingsconcepten voor toepassingen en [clusters in AKS][concepts-security] en Best practices voor clusterbeveiliging en [-upgrades in AKS][operator-best-practices-cluster-security]voor meer informatie.

<!-- LINKS - external -->
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[dev-spaces-ranges]: ../dev-spaces/configure-networking.md#aks-cluster-network-requirements
[kubenet]: https://kubernetes.io/docs/concepts/extend-kubernetes/compute-storage-net/network-plugins/#kubenet

<!-- LINKS - internal -->
[az-aks-update]: /cli/azure/ext/aks-preview/aks#ext-aks-preview-az-aks-update
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-show]: /cli/azure/aks#az_aks_show
[az-network-public-ip-list]: /cli/azure/network/public-ip#az_network_public_ip_list
[concepts-clusters-workloads]: concepts-clusters-workloads.md
[concepts-security]: concepts-security.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-cluster-security]: operator-best-practices-cluster-security.md
[route-tables]: ../virtual-network/manage-route-table.md
[standard-sku-lb]: load-balancer-standard.md
