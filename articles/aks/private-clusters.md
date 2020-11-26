---
title: Een persoonlijk Azure Kubernetes service-cluster maken
description: Meer informatie over het maken van een AKS-cluster (private Azure Kubernetes service)
services: container-service
ms.topic: article
ms.date: 7/17/2020
ms.openlocfilehash: 450d68e26c5a3fc1ecfbaf6a3be6b5f698ee65e3
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96183257"
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

## <a name="dependencies"></a>Afhankelijkheden  

* De service private link wordt alleen ondersteund op standaard Azure Load Balancer. Basis Azure Load Balancer wordt niet ondersteund.  
* Als u een aangepaste DNS-server wilt gebruiken, voegt u de Azure DNS IP-168.63.129.16 toe als de upstream-DNS-server in de aangepaste DNS-server.

## <a name="limitations"></a>Beperkingen 
* Toegestane IP-bereiken kunnen niet worden toegepast op het eind punt van de persoonlijke API-server, maar zijn alleen van toepassing op de open bare API-server
* [Beschikbaarheidszones][availability-zones] worden momenteel ondersteund voor bepaalde regio's. 
* De beperkingen van de [Azure Private Link-service][private-link-service] zijn van toepassing op persoonlijke clusters.
* Geen ondersteuning voor door micro soft gehoste DevOps-agents van Azure met persoonlijke clusters. Overweeg [zelf-hostende agents][devops-agents]te gebruiken. 
* Voor klanten die Azure Container Registry kunnen gebruiken met persoonlijke AKS, moet het virtuele netwerk Container Registry worden gekoppeld aan het virtuele netwerk van het agent cluster.
* Geen huidige ondersteuning voor Azure dev Spaces
* Geen ondersteuning voor het converteren van bestaande AKS-clusters naar particuliere clusters
* Als u het persoonlijke eind punt in het subnet van de klant verwijdert of wijzigt, werkt het cluster niet meer. 
* Azure Monitor voor containers Live-gegevens wordt momenteel niet ondersteund.
* SLA voor uptime wordt momenteel niet ondersteund.


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