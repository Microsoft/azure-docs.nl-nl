---
title: Scenario's voor het gebruik van een virtueel netwerk
description: Scenario's, bronnen en beperkingen voor het implementeren van container groepen naar een virtueel Azure-netwerk.
ms.topic: article
ms.date: 08/11/2020
ms.openlocfilehash: 20c2b4fe2f19402d6647f398a9696b7e16550d8e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104606885"
---
# <a name="virtual-network-scenarios-and-resources"></a>Scenario's en resources voor het virtuele netwerk

[Azure Virtual Network](../virtual-network/virtual-networks-overview.md) biedt veilige, persoonlijke netwerken voor uw Azure-en on-premises resources. Door container groepen te implementeren in een virtueel Azure-netwerk, kunnen uw containers veilig communiceren met andere resources in het virtuele netwerk. 

Dit artikel bevat achtergrond informatie over scenario's, beperkingen en bronnen van het virtuele netwerk. Zie [container instanties implementeren in een virtueel Azure-netwerk](container-instances-vnet.md)voor implementatie voorbeelden met behulp van de Azure cli.

> [!IMPORTANT]
> Implementatie van container groep naar een virtueel netwerk is algemeen beschikbaar voor Linux-containers, in de meeste regio's waar Azure Container Instances beschikbaar is. Zie [regio's en resources Beschik baarheid](container-instances-region-availability.md)voor meer informatie. 

## <a name="scenarios"></a>Scenario's

Met container groepen die zijn geïmplementeerd in een virtueel Azure-netwerk, kunnen scenario's zoals:

* Directe communicatie tussen container groepen in hetzelfde subnet
* Uitvoer van een werk belasting op basis van een [taak](container-instances-restart-policy.md) naar een data base in het virtuele netwerk verzenden
* Inhoud ophalen voor container exemplaren van een [service-eind punt](../virtual-network/virtual-network-service-endpoints-overview.md) in het virtuele netwerk
* Container communicatie met on-premises bronnen via een [VPN-gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md) of [ExpressRoute](../expressroute/expressroute-introduction.md) inschakelen
* Integreren met [Azure firewall](../firewall/overview.md) om uitgaand verkeer te identificeren dat afkomstig is uit de container 
* Namen omzetten via de interne Azure DNS voor communicatie met Azure-resources in het virtuele netwerk, zoals virtuele machines
* Gebruik NSG-regels om toegang tot de container te beheren tot subnetten of andere netwerk bronnen

## <a name="unsupported-networking-scenarios"></a>Niet-ondersteunde netwerk scenario's 

* **Azure Load Balancer** het plaatsen van een Azure Load Balancer vóór container instanties in een groep met netwerk containers wordt niet ondersteund
* **Globaal virtueel netwerk-peering** -wereld wijd peering (verbinding maken met virtuele netwerken tussen Azure-regio's) wordt niet ondersteund
* **Openbaar IP-of DNS-label** -container groepen die zijn geïmplementeerd in een virtueel netwerk ondersteunen momenteel geen ondersteuning voor het rechtstreeks weer geven van containers op internet met een openbaar IP-adres of een Fully Qualified Domain name
* **Virtual Network NAT** -container groepen die zijn geïmplementeerd in een virtueel netwerk, bieden momenteel geen ondersteuning voor het gebruik van een NAT-gateway resource voor uitgaande internet connectiviteit.

## <a name="other-limitations"></a>Andere beperkingen

* Momenteel worden alleen Linux-containers ondersteund in een container groep die is geïmplementeerd in een virtueel netwerk.
* Als u container groepen wilt implementeren in een subnet, mag het subnet geen andere bron typen bevatten. Verwijder alle bestaande resources uit een bestaand subnet voordat u container groepen implementeert, of maak een nieuw subnet.
* U kunt een [beheerde identiteit](container-instances-managed-identity.md) niet gebruiken in een container groep die is geïmplementeerd in een virtueel netwerk.
* U kunt geen test voor de [duur](container-instances-liveness-probe.md) van de bewaarde of [gereedheids test](container-instances-readiness-probe.md) inschakelen in een container groep die is geïmplementeerd in een virtueel netwerk.
* Vanwege de extra netwerk bronnen die betrokken zijn, zijn implementaties naar een virtueel netwerk doorgaans langzamer dan het implementeren van een standaard container exemplaar.
* Als u uw container groep verbindt met een Azure Storage-account, moet u een [service-eind punt](../virtual-network/virtual-network-service-endpoints-overview.md) toevoegen aan die resource.

[!INCLUDE [container-instances-restart-ip](../../includes/container-instances-restart-ip.md)]

## <a name="required-network-resources"></a>Vereiste netwerk bronnen

Er zijn drie Azure Virtual Network-resources vereist voor het implementeren van container groepen in een virtueel netwerk: het [virtuele netwerk](#virtual-network) zelf, een [overgedragen subnet](#subnet-delegated) binnen het virtuele netwerk en een [netwerk profiel](#network-profile). 

### <a name="virtual-network"></a>Virtueel netwerk

Een virtueel netwerk definieert de adres ruimte waarin u een of meer subnetten maakt. Vervolgens implementeert u Azure-resources (zoals container groepen) in de subnetten in uw virtuele netwerk.

### <a name="subnet-delegated"></a>Subnet (gedelegeerd)

Met subnetten wordt het virtuele netwerk gesegmenteerd tot afzonderlijke adres ruimten die kunnen worden gebruikt door de Azure-resources die u in deze ruimte plaatst. U maakt een of meer subnetten binnen een virtueel netwerk.

Het subnet dat u voor container groepen gebruikt, mag alleen container groepen bevatten. Wanneer u een container groep voor het eerst naar een subnet implementeert, wordt dat subnet door Azure gedelegeerd aan Azure Container Instances. Wanneer het subnet is gedelegeerd, kan het alleen worden gebruikt voor container groepen. Als u probeert andere resources dan container groepen te implementeren op een overgedragen subnet, mislukt de bewerking.

### <a name="network-profile"></a>Netwerkprofiel

Een netwerk profiel is een netwerk configuratie sjabloon voor Azure-resources. Hiermee geeft u bepaalde netwerk eigenschappen voor de resource op, bijvoorbeeld het subnet waarin het moet worden geïmplementeerd. Wanneer u de opdracht [AZ container Create][az-container-create] gebruikt voor het implementeren van een container groep naar een subnet (en dus een virtueel netwerk), maakt Azure een netwerk profiel voor u. U kunt dat netwerk profiel vervolgens gebruiken voor toekomstige implementaties naar het subnet. 

Als u een resource manager-sjabloon, een YAML-bestand of een programmatische methode wilt gebruiken om een container groep naar een subnet te implementeren, moet u de volledige Resource Manager-Resource-ID van een netwerk profiel opgeven. U kunt een profiel gebruiken dat eerder is gemaakt met [AZ container Create][az-container-create]of een profiel maken met een resource manager-sjabloon (Zie [voor beeld van sjabloon](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-vnet) en [Naslag informatie](/azure/templates/microsoft.network/networkprofiles)). Als u de ID van een eerder gemaakt profiel wilt ophalen, gebruikt u de opdracht [AZ Network profile list][az-network-profile-list] . 

In het volgende diagram zijn verschillende container groepen geïmplementeerd naar een subnet dat is overgedragen aan Azure Container Instances. Zodra u één container groep naar een subnet hebt geïmplementeerd, kunt u extra container groepen implementeren door hetzelfde netwerk profiel op te geven.

![Container groepen binnen een virtueel netwerk][aci-vnet-01]

## <a name="next-steps"></a>Volgende stappen

* Zie [container instanties implementeren in een virtueel Azure-netwerk](container-instances-vnet.md)voor implementatie voorbeelden met de Azure cli.
* Zie [een Azure-container groep met VNet maken](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-vnet
)voor het implementeren van een nieuw virtueel netwerk, subnet, netwerk profiel en container groep met behulp van een resource manager-sjabloon.
* Wanneer u de [Azure Portal](container-instances-quickstart-portal.md) gebruikt om een container exemplaar te maken, kunt u ook instellingen voor een nieuw of exsting virtueel netwerk opgeven op het tabblad **netwerk** .


<!-- IMAGES -->
[aci-vnet-01]: ./media/container-instances-virtual-network-concepts/aci-vnet-01.png

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-network-profile-list]: /cli/azure/network/profile#az-network-profile-list
