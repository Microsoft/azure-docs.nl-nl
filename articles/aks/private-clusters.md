---
title: Een persoonlijk Azure Kubernetes service-cluster maken
description: Meer informatie over het maken van een AKS-cluster (private Azure Kubernetes service)
services: container-service
ms.topic: article
ms.date: 7/17/2020
ms.openlocfilehash: 696ba785abb317a29de38160440dc06487ff5bca
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/18/2020
ms.locfileid: "97673882"
---
# <a name="create-a-private-azure-kubernetes-service-cluster"></a>Een persoonlijk Azure Kubernetes service-cluster maken

In een particulier cluster heeft het besturings vlak of de API-server interne IP-adressen die zijn gedefinieerd in het [RFC1918 voor particulier Internet](https://tools.ietf.org/html/rfc1918) document. Door gebruik te maken van een persoonlijk cluster, kunt u ervoor zorgen dat het netwerk verkeer tussen uw API-server en de knooppunt groepen alleen in het particuliere netwerk blijven.

Het besturings vlak of de API-server bevindt zich in een door Azure Kubernetes service (AKS) beheerd Azure-abonnement. Het cluster of de knooppunt groep van een klant bevindt zich in het abonnement van de klant. De server en het cluster of de knooppunt groep kunnen met elkaar communiceren via de [Azure Private Link-service][private-link-service] in het virtuele netwerk van de API-server en een persoonlijk eind punt dat wordt weer gegeven in het subnet van het AKS-cluster van de klant.

## <a name="region-availability"></a>Beschikbaarheid in regio’s

Een persoonlijk cluster is beschikbaar in open bare regio's, Azure Government en Azure China 21Vianet-regio's waar [AKS wordt ondersteund](https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service).

> [!NOTE]
> Azure Government-sites worden ondersteund, maar US Gov-Texas wordt momenteel niet ondersteund vanwege ontbrekende persoonlijke koppelings ondersteuning.

## <a name="prerequisites"></a>Vereisten

* De Azure CLI-versie 2.2.0 of hoger
* De service private link wordt alleen ondersteund op standaard Azure Load Balancer. Basis Azure Load Balancer wordt niet ondersteund.  
* Als u een aangepaste DNS-server wilt gebruiken, voegt u de Azure DNS IP-168.63.129.16 toe als de upstream-DNS-server in de aangepaste DNS-server.

## <a name="create-a-private-aks-cluster"></a>Een persoonlijk AKS-cluster maken

### <a name="create-a-resource-group"></a>Een resourcegroep maken

Maak een resource groep of gebruik een bestaande resource groep voor uw AKS-cluster.

```azurecli-interactive
az group create -l westus -n MyResourceGroup
```

### <a name="default-basic-networking"></a>Standaard netwerken 

```azurecli-interactive
az aks create -n <private-cluster-name> -g <private-cluster-resource-group> --load-balancer-sku standard --enable-private-cluster  
```
Waar `--enable-private-cluster` is een verplichte vlag voor een persoonlijk cluster. 

### <a name="advanced-networking"></a>Geavanceerde netwerken  

```azurecli-interactive
az aks create \
    --resource-group <private-cluster-resource-group> \
    --name <private-cluster-name> \
    --load-balancer-sku standard \
    --enable-private-cluster \
    --network-plugin azure \
    --vnet-subnet-id <subnet-id> \
    --docker-bridge-address 172.17.0.1/16 \
    --dns-service-ip 10.2.0.10 \
    --service-cidr 10.2.0.0/24 
```
Waar `--enable-private-cluster` is een verplichte vlag voor een persoonlijk cluster. 

> [!NOTE]
> Als de docker Bridge-adres CIDR (172.17.0.1/16) in conflict is met de CIDR van het subnet, wijzigt u het docker Bridge-adres op de juiste manier.

### <a name="configure-private-dns-zone"></a>Privé-DNS zone configureren

De standaard waarde is ' System ', als het argument--privé-DNS-zone is wegge laten. AKS maakt een Privé-DNS zone in de knooppunt resource groep. Als de para meter ' none ' wordt door gegeven, betekent AKS geen Privé-DNS zone te maken.  Dit is afhankelijk van uw eigen DNS-server en de configuratie van de DNS-omzetting voor de persoonlijke FQDN.  Als u geen DNS-omzetting configureert, kan DNS alleen worden omgezet in de agent knooppunten en kunnen er cluster problemen optreden na de implementatie.

## <a name="no-private-dns-zone-prerequisites"></a>Er zijn geen Privé-DNS zone vereisten
Geen PrivateDNSZone
* De Azure CLI-versie 0.4.67 of hoger
* De API-versie 2020-11-01 of hoger

## <a name="create-a-private-aks-cluster-with-private-dns-zone"></a>Een persoonlijk AKS-cluster maken met Privé-DNS zone

```azurecli-interactive
az aks create -n <private-cluster-name> -g <private-cluster-resource-group> --load-balancer-sku standard --enable-private-cluster --private-dns-zone [none|system]
```
## <a name="options-for-connecting-to-the-private-cluster"></a>Opties voor het maken van verbinding met het privé cluster

Het API-server eindpunt heeft geen openbaar IP-adres. Als u de API-server wilt beheren, moet u een virtuele machine gebruiken die toegang heeft tot de Azure-Virtual Network (VNet) van het AKS-cluster. Er zijn verschillende opties voor het tot stand brengen van een netwerk verbinding met het persoonlijke cluster.

* Maak een virtuele machine in hetzelfde Azure-Virtual Network (VNet) als het AKS-cluster.
* Gebruik een virtuele machine in een afzonderlijk netwerk en stel de [peering van een virtueel netwerk][virtual-network-peering]in.  Zie de sectie hieronder voor meer informatie over deze optie.
* Gebruik een [snelle route of VPN-][express-route-or-VPN] verbinding.

Het maken van een virtuele machine in hetzelfde VNET als het AKS-cluster is de eenvoudigste optie.  Express route en Vpn's voegen kosten toe en vereisen extra netwerk complexiteit.  Voor peering van virtuele netwerken moet u uw netwerkcidr-bereiken plannen om ervoor te zorgen dat er geen overlappende bereiken zijn.

## <a name="virtual-network-peering"></a>Peering op virtueel netwerk

Zoals gezegd, is peering in virtuele netwerken een manier om toegang te krijgen tot uw persoonlijke cluster. Als u virtuele netwerk peering wilt gebruiken, moet u een koppeling instellen tussen het virtuele netwerk en de privé-DNS-zone.
    
1. Ga naar de resource groep knoop punt in de Azure Portal.  
2. Selecteer de privé-DNS-zone.   
3. Selecteer de koppeling **virtueel netwerk** in het linkerdeel venster.  
4. Maak een nieuwe koppeling om het virtuele netwerk van de VM toe te voegen aan de privé-DNS-zone. Het duurt enkele minuten voordat de koppeling van de DNS-zone beschikbaar wordt.  
5. Navigeer in het Azure Portal naar de resource groep die het virtuele netwerk van het cluster bevat.  
6. Selecteer het virtuele netwerk in het rechterdeel venster. De naam van het virtuele netwerk bevindt zich in de vorm *AKS-vnet- \**.  
7. Selecteer **peerings** in het linkerdeel venster.  
8. Selecteer **toevoegen**, voeg het virtuele netwerk van de VM toe en maak de peering.  
9. Ga naar het virtuele netwerk waar u de virtuele machine hebt, selecteer **peerings**, selecteer het virtuele netwerk AKS en maak de peering. Als de adresbereiken in het virtuele netwerk van AKS en het virtuele netwerk van de VM conflicteren, mislukt de peering. Zie  [peering van virtuele netwerken][virtual-network-peering]voor meer informatie.

## <a name="hub-and-spoke-with-custom-dns"></a>Hub en spoke met aangepaste DNS

[Hub-en spoke-architecturen](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) worden vaak gebruikt voor het implementeren van netwerken in Azure. In veel van deze implementaties worden DNS-instellingen in de spoke VNets geconfigureerd om te verwijzen naar een centrale DNS-doorstuur server om te zorgen voor on-premises en op Azure gebaseerde DNS-omzetting. Wanneer u een AKS-cluster in een dergelijke netwerk omgeving implementeert, moet u rekening houden met een aantal speciale overwegingen.

![Private cluster hub en spoke](media/private-clusters/aks-private-hub-spoke.png)

1. Wanneer een persoonlijk cluster is ingericht, wordt standaard een persoonlijk eind punt (1) en een privé-DNS-zone (2) gemaakt in de door het cluster beheerde resource groep. Het cluster maakt gebruik van een A-record in de privé zone om het IP-adres van het privé-eind punt voor communicatie met de API-server op te lossen.

2. De privé-DNS-zone wordt alleen gekoppeld aan het VNet waaraan de cluster knooppunten zijn gekoppeld (3). Dit betekent dat het privé-eind punt alleen kan worden omgezet door hosts in het gekoppelde VNet. In scenario's waarin geen aangepaste DNS is geconfigureerd op het VNet (standaard), werkt dit zonder te verlenen als hosts-punt op 168.63.129.16 voor DNS die records kan omzetten in de privé-DNS-zone vanwege de koppeling.

3. In scenario's waarin het VNet dat uw cluster bevat aangepaste DNS-instellingen (4) heeft, mislukt de implementatie van het cluster, tenzij de privé-DNS-zone is gekoppeld aan het VNet dat de aangepaste DNS-resolvers (5) bevat. Deze koppeling kan hand matig worden gemaakt nadat de privé zone is gemaakt tijdens het inrichten van een cluster of via Automation wanneer de zone wordt gedetecteerd met behulp van implementatie mechanismen op basis van gebeurtenissen (bijvoorbeeld Azure Event Grid en Azure Functions).

> [!NOTE]
> Als u [uw eigen route tabel gebruikt met kubenet](https://docs.microsoft.com/azure/aks/configure-kubenet#bring-your-own-subnet-and-route-table-with-kubenet) en uw eigen DNS-server naar een privé cluster brengt, mislukt het maken van het cluster. U moet de [RouteTable](https://docs.microsoft.com/azure/aks/configure-kubenet#bring-your-own-subnet-and-route-table-with-kubenet) in de knooppunt resource groep koppelen aan het subnet nadat het maken van het cluster is mislukt, zodat het maken is geslaagd.

## <a name="limitations"></a>Beperkingen 
* Toegestane IP-bereiken kunnen niet worden toegepast op het eind punt van de persoonlijke API-server, maar zijn alleen van toepassing op de open bare API-server
* De beperkingen van de [Azure Private Link-service][private-link-service] zijn van toepassing op persoonlijke clusters.
* Geen ondersteuning voor door micro soft gehoste DevOps-agents van Azure met persoonlijke clusters. Overweeg [zelf-hostende agents](https://docs.microsoft.com/azure/devops/pipelines/agents/agents?view=azure-devops&tabs=browser&preserve-view=true)te gebruiken. 
* Voor klanten die Azure Container Registry kunnen gebruiken met persoonlijke AKS, moet het virtuele netwerk Container Registry worden gekoppeld aan het virtuele netwerk van het agent cluster.
* Geen ondersteuning voor het converteren van bestaande AKS-clusters naar particuliere clusters
* Als u het persoonlijke eind punt in het subnet van de klant verwijdert of wijzigt, werkt het cluster niet meer. 
* Azure Monitor voor containers Live-gegevens wordt momenteel niet ondersteund.
* Nadat klanten de A-record op hun eigen DNS-servers hebben bijgewerkt, zullen die peulen nog steeds apiserver FQDN omzetten naar het oudere IP-adres na de migratie tot ze opnieuw zijn opgestart. Klanten moeten hostNetwork peul en standaard-DNSPolicy peul opnieuw opstarten na de migratie van het controle vlak.
* In het geval van onderhoud op het besturings vlak kan uw [AKS-IP](https://docs.microsoft.com/azure/aks/limit-egress-traffic#:~:text=By%20default%2C%20AKS%20clusters%20have%20unrestricted%20outbound%20%28egress%29,be%20accessible%20to%20maintain%20healthy%20cluster%20maintenance%20tasks.) worden gewijzigd. In dit geval moet u de A-record die verwijst naar het privé-IP-adres van de API-server op uw aangepaste DNS-server bijwerken en aangepaste peulen of implementaties opnieuw starten met behulp van hostNetwork.

<!-- LINKS - internal -->
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest#az-provider-register
[az-feature-list]: /cli/azure/feature?view=azure-cli-latest#az-feature-list
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[private-link-service]: ../private-link/private-link-service-overview.md#limitations
[virtual-network-peering]: ../virtual-network/virtual-network-peering-overview.md
[azure-bastion]: ../bastion/tutorial-create-host-portal.md
[express-route-or-vpn]: ../expressroute/expressroute-about-virtual-network-gateways.md
[devops-agents]: /azure/devops/pipelines/agents/agents?view=azure-devops
[availability-zones]: availability-zones.md