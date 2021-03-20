---
title: Door API server geautoriseerde IP-bereiken in azure Kubernetes service (AKS)
description: Meer informatie over het beveiligen van uw cluster met behulp van een IP-adres bereik voor toegang tot de API-server in azure Kubernetes service (AKS)
services: container-service
ms.topic: article
ms.date: 09/21/2020
ms.openlocfilehash: ca6e1c06b3ad90ef12c9bf375bae50d46c5f7c37
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98890630"
---
# <a name="secure-access-to-the-api-server-using-authorized-ip-address-ranges-in-azure-kubernetes-service-aks"></a>Veilige toegang tot de API-server met behulp van geautoriseerde IP-adresbereiken in azure Kubernetes service (AKS)

In Kubernetes ontvangt de API-server aanvragen voor het uitvoeren van acties in het cluster, zoals het maken van resources of het schalen van het aantal knoop punten. De API-server is de centrale manier om met een cluster te communiceren en te beheren. Als u de beveiliging van het cluster wilt verbeteren en aanvallen wilt minimaliseren, moet de API-server alleen toegankelijk zijn vanuit een beperkt aantal IP-adresbereiken.

Dit artikel laat u zien hoe u met de API-server geautoriseerde IP-adresbereiken kunt beperken welke IP-adressen en CIDRs het beheer vlak kunnen hebben.

## <a name="before-you-begin"></a>Voordat u begint

In dit artikel wordt beschreven hoe u een AKS-cluster maakt met behulp van de Azure CLI.

U moet de Azure CLI-versie 2.0.76 of hoger hebben geïnstalleerd en geconfigureerd. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren][install-azure-cli] als u de CLI wilt installeren of een upgrade wilt uitvoeren.

### <a name="limitations"></a>Beperkingen

De functie voor door de API-server geautoriseerde IP-bereiken heeft de volgende beperkingen:
- Op clusters die zijn gemaakt na de door de API server geautoriseerde IP-adresbereiken in oktober 2019, worden door API-server geautoriseerde IP-adresbereiken alleen ondersteund voor de *standaard* SKU Load Balancer. Bestaande clusters met de *basis* -SKU Load Balancer en de door de API server geautoriseerde IP-adresbereiken blijven werken, maar kunnen niet worden gemigreerd naar een *standaard* -SKU Load Balancer. Deze bestaande clusters blijven werken als hun Kubernetes-versie of besturings vlak wordt geüpgraded. Geautoriseerde IP-adresbereiken van de API-server worden niet ondersteund voor persoonlijke clusters.
- Deze functie is niet compatibel met clusters die gebruikmaken van de [Preview-functie voor het gebruik van open bare IP per knooppunt groep](use-multiple-node-pools.md#assign-a-public-ip-per-node-for-your-node-pools-preview)knoop punten.

## <a name="overview-of-api-server-authorized-ip-ranges"></a>Overzicht van door de API server geautoriseerde IP-adresbereiken

De Kubernetes API-server is hoe de onderliggende Kubernetes-Api's worden weer gegeven. Dit onderdeel biedt de interactie voor beheer hulpprogramma's, zoals `kubectl` of het Kubernetes-dash board. AKS biedt een cluster beheer vlak met één Tenant, met een speciale API-server. Standaard wordt aan de API-server een openbaar IP-adres toegewezen. u moet de toegang beheren via Kubernetes op basis van rollen (Kubernetes RBAC) of Azure RBAC.

Als u de toegang tot de anderszins toegankelijke AKS Control vlak/API-server wilt beveiligen, kunt u geautoriseerde IP-bereiken inschakelen en gebruiken. Met deze geautoriseerde IP-bereiken zijn alleen gedefinieerde IP-adresbereiken toegestaan om te communiceren met de API-server. Een aanvraag naar de API-server vanaf een IP-adres dat geen deel uitmaakt van deze toegestane IP-bereiken, wordt geblokkeerd. Blijf Kubernetes RBAC of Azure RBAC gebruiken om gebruikers en de acties die ze aanvragen te autoriseren.

Zie [Kubernetes core-concepten voor AKS][concepts-clusters-workloads]voor meer informatie over de API-server en andere cluster onderdelen.

## <a name="create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled"></a>Een AKS-cluster maken waarvoor een API-server geautoriseerde IP-adresbereiken is ingeschakeld

Maak een cluster met behulp van de [AZ AKS Create][az-aks-create] en geef de *`--api-server-authorized-ip-ranges`* para meter op om een lijst met geautoriseerde IP-adresbereiken op te geven. Deze IP-adresbereiken zijn meestal adresbereiken die worden gebruikt door uw on-premises netwerken of open bare Ip's. Wanneer u een CIDR-bereik opgeeft, begint u met het eerste IP-adres in het bereik. *137.117.106.90/29* is bijvoorbeeld een geldig bereik, maar zorg ervoor dat u het eerste IP-adres in het bereik opgeeft, zoals *137.117.106.88/29*.

> [!IMPORTANT]
> Uw cluster maakt standaard gebruik van de [standaard-SKU Load Balancer][standard-sku-lb] die u kunt gebruiken om de uitgaande gateway te configureren. Wanneer u toegestane IP-bereiken van de API-server inschakelt tijdens het maken van het cluster, is het open bare IP-adres voor uw cluster standaard ook toegestaan naast de bereiken die u opgeeft. Als u ' of geen waarde voor *'* opgeeft *`--api-server-authorized-ip-ranges`* , wordt de API-server geautoriseerde IP-adresbereiken uitgeschakeld. Als u Power shell gebruikt, gebruikt u *`--api-server-authorized-ip-ranges=""`* (met een gelijkteken) om problemen met het parseren te voor komen.

In het volgende voor beeld wordt een cluster met één knoop punt met de naam *myAKSCluster* gemaakt in de resource groep met de naam *myResourceGroup* waarvoor geautoriseerde IP-bereiken voor de API-server zijn ingeschakeld. De toegestane IP-adresbereiken zijn *73.140.245.0/24*:

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
> Voeg deze bereiken toe aan een acceptatie lijst:
> - Het open bare IP-adres van de firewall
> - Elk bereik dat netwerken vertegenwoordigt waarvan u het cluster beheert
> - Als u Azure dev Spaces op uw AKS-cluster gebruikt, moet u [extra bereiken op basis van uw regio][dev-spaces-ranges]toestaan.
>
> De bovengrens voor het aantal IP-bereiken dat u kunt opgeven, is 200.
>
> Het kan zijn dat de regels tot 2min kunnen worden door gegeven. Wacht tijd voor het testen van de verbinding.

### <a name="specify-the-outbound-ips-for-the-standard-sku-load-balancer"></a>Geef de uitgaande Ip's op voor de standaard-SKU load balancer

Als u een AKS-cluster maakt en u de uitgaande IP-adressen of voor voegsels voor het cluster opgeeft, worden deze adressen of voor voegsels ook toegestaan. Bijvoorbeeld:

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

In het bovenstaande voor beeld zijn alle IP-adressen die in de para meter zijn opgenomen, *`--load-balancer-outbound-ip-prefixes`* toegestaan in combi natie met de IP-adressen in de *`--api-server-authorized-ip-ranges`* para meter.

In plaats daarvan kunt u de *`--load-balancer-outbound-ip-prefixes`* para meter opgeven om uitgaande Load Balancer IP-voor voegsels toe te staan.

### <a name="allow-only-the-outbound-public-ip-of-the-standard-sku-load-balancer"></a>Alleen het uitgaande open bare IP-adres van de standaard-SKU toestaan load balancer

Wanneer u toegestane IP-bereiken van de API-server inschakelt tijdens het maken van het cluster, is het uitgaande open bare IP-adres voor de standaard SKU-load balancer voor uw cluster ook standaard toegestaan naast de bereiken die u opgeeft. Als u alleen het uitgaande open bare IP-adres van de standaard-SKU load balancer wilt toestaan, gebruikt u *0.0.0.0/32* bij het opgeven van de *`--api-server-authorized-ip-ranges`* para meter.

In het volgende voor beeld is alleen het uitgaande open bare IP-adres van de standaard-SKU-load balancer toegestaan en u hebt alleen toegang tot de API-server vanuit de knoop punten in het cluster.

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

## <a name="update-a-clusters-api-server-authorized-ip-ranges"></a>De door de API-server geautoriseerde IP-adresbereiken van een cluster bijwerken

Als u de door de API-server geautoriseerde IP-adresbereiken wilt bijwerken op een bestaand cluster, gebruikt u [AZ AKS update][az-aks-update] Command en gebruikt u de *`--api-server-authorized-ip-ranges`* ,--Load Balancer-uitgaand-IP-voor voegsels *, *`--load-balancer-outbound-ips`* of--Load Balancer-uitgaand-IP-voor voegsels* para meters.

In het volgende voor beeld worden de geautoriseerde IP-adresbereiken van de API-server bijgewerkt in het cluster met de naam *myAKSCluster* in de resource groep genaamd *myResourceGroup*. Het IP-adres bereik dat moet worden geautoriseerd, is *73.140.245.0/24*:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges  73.140.245.0/24
```

U kunt ook *0.0.0.0/32* gebruiken bij het opgeven *`--api-server-authorized-ip-ranges`* van de para meter om alleen het open bare IP-adres van de standaard-SKU Load Balancer toe te staan.

## <a name="disable-authorized-ip-ranges"></a>Toegestane IP-bereiken uitschakelen

Als u gemachtigde IP-bereiken wilt uitschakelen, gebruikt u [AZ AKS update][az-aks-update] en geeft u een leeg bereik op om IP-adresbereiken met de API-server uit te scha kelen. Bijvoorbeeld:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges ""
```

## <a name="find-existing-authorized-ip-ranges"></a>Bestaande gemachtigde IP-bereiken zoeken

Gebruik [AZ AKS show][az-aks-show] en geef de naam en resource groep van het cluster op om IP-bereiken te vinden die zijn gemachtigd. Bijvoorbeeld:

```azurecli-interactive
az aks show \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --query apiServerAccessProfile.authorizedIpRanges'
```

## <a name="update-disable-and-find-authorized-ip-ranges-using-azure-portal"></a>Gemachtigde IP-bereiken bijwerken, uitschakelen en zoeken met behulp van Azure Portal

De bovenstaande bewerkingen voor het toevoegen, bijwerken, zoeken en uitschakelen van geautoriseerde IP-bereiken kunnen ook worden uitgevoerd in de Azure Portal. Ga naar **netwerken** onder **instellingen** in de menu-Blade van de cluster bron om toegang te krijgen.

:::image type="content" source="media/api-server-authorized-ip-ranges/ip-ranges-specified.PNG" alt-text="In een browser worden de netwerk instellingen van de cluster bron Azure Portal pagina weer gegeven. De opties ' opgegeven IP-bereik instellen ' en ' opgegeven IP-bereiken ' zijn gemarkeerd.":::

## <a name="how-to-find-my-ip-to-include-in---api-server-authorized-ip-ranges"></a>Hoe vind ik mijn IP-adres om in te voegen `--api-server-authorized-ip-ranges` ?

U moet uw ontwikkel computers, hulpprogram ma's of automatiserings-IP-adressen toevoegen aan de AKS-cluster lijst met goedgekeurde IP-bereiken om vanaf daar toegang te krijgen tot de API-server. 

Een andere optie is het configureren van een JumpBox met het benodigde hulp programma binnen een afzonderlijk subnet in het virtuele netwerk van de firewall. Hierbij wordt ervan uitgegaan dat uw omgeving een firewall heeft met het respectieve netwerk en dat u de firewall-Ip's hebt toegevoegd aan de gemachtigde bereiken. En als u tunneling van het subnet AKS naar het subnet van de firewall hebt geforceerd, dan is de JumpBox in het subnet van het cluster ook goed.

Voeg een ander IP-adres toe aan de goedgekeurde bereiken met de volgende opdracht.

```bash
# Retrieve your IP address
CURRENT_IP=$(dig @resolver1.opendns.com ANY myip.opendns.com +short)
# Add to AKS approved list
az aks update -g $RG -n $AKSNAME --api-server-authorized-ip-ranges $CURRENT_IP/32
```

>> [!NOTE]
> In het bovenstaande voor beeld worden de door de API server geautoriseerde IP-adresbereiken toegevoegd aan het cluster. Als u gemachtigde IP-bereiken wilt uitschakelen, gebruikt u AZ AKS update en geeft u een leeg bereik op. 

U kunt ook de onderstaande opdracht op Windows-systemen gebruiken om het open bare IPv4-adres op te halen, of gebruik de stappen in [uw IP-adres zoeken](https://support.microsoft.com/en-gb/help/4026518/windows-10-find-your-ip-address).

```azurepowershell-interactive
Invoke-RestMethod http://ipinfo.io/json | Select -exp ip
```

U kunt dit adres ook vinden door te zoeken in ' wat is mijn IP-adres ' in een Internet browser.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u de API-server geautoriseerde IP-adresbereiken ingeschakeld. Deze benadering is een onderdeel van hoe u een veilig AKS-cluster kunt uitvoeren.

Zie voor meer informatie [beveiligings concepten voor toepassingen en clusters in AKS][concepts-security] en [Aanbevolen procedures voor het beveiligen van het cluster en upgrades in AKS][operator-best-practices-cluster-security].

<!-- LINKS - external -->
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[dev-spaces-ranges]: ../dev-spaces/configure-networking.md#aks-cluster-network-requirements
[kubenet]: https://kubernetes.io/docs/concepts/extend-kubernetes/compute-storage-net/network-plugins/#kubenet

<!-- LINKS - internal -->
[az-aks-update]: /cli/azure/ext/aks-preview/aks#ext-aks-preview-az-aks-update
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-show]: /cli/azure/aks#az_aks_show
[az-network-public-ip-list]: /cli/azure/network/public-ip#az-network-public-ip-list
[concepts-clusters-workloads]: concepts-clusters-workloads.md
[concepts-security]: concepts-security.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-cluster-security]: operator-best-practices-cluster-security.md
[route-tables]: ../virtual-network/manage-route-table.md
[standard-sku-lb]: load-balancer-standard.md
