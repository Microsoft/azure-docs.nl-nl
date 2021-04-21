---
title: Scenario's voor het gebruik van een virtueel netwerk
description: Scenario's, resources en beperkingen voor het implementeren van containergroepen in een virtueel Azure-netwerk.
ms.topic: article
ms.date: 08/11/2020
ms.openlocfilehash: 6de99c68c3f05e4734dd46a579d28a6f1a3b824e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107763771"
---
# <a name="virtual-network-scenarios-and-resources"></a>Scenario's en resources voor virtuele netwerken

[Azure Virtual Network](../virtual-network/virtual-networks-overview.md) biedt veilige privénetwerken voor uw Azure- en on-premises resources. Door containergroepen te implementeren in een virtueel Azure-netwerk, kunnen uw containers veilig communiceren met andere resources in het virtuele netwerk. 

In dit artikel vindt u achtergrondinformatie over scenario's, beperkingen en resources voor virtuele netwerken. Zie Container-exemplaren implementeren in een virtueel Azure-netwerk voor implementatievoorbeelden met behulp [van de Azure CLI.](container-instances-vnet.md)

> [!IMPORTANT]
> Implementatie van containergroep in een virtueel netwerk is algemeen beschikbaar voor Linux-containers, in de meeste regio's waar Azure Container Instances beschikbaar is. Zie Regio's en beschikbaarheid [van resources voor meer informatie.](container-instances-region-availability.md) 

## <a name="scenarios"></a>Scenario's

Containergroepen die zijn geïmplementeerd in een virtueel Azure-netwerk maken scenario's mogelijk zoals:

* Directe communicatie tussen containergroepen in hetzelfde subnet
* Taakgebaseerde [workloaduitvoer](container-instances-restart-policy.md) van container-exemplaren verzenden naar een database in het virtuele netwerk
* Inhoud voor container instances ophalen van een [service-eindpunt](../virtual-network/virtual-network-service-endpoints-overview.md) in het virtuele netwerk
* Containercommunicatie met on-premises resources via een [VPN-gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md) of [ExpressRoute inschakelen](../expressroute/expressroute-introduction.md)
* Integreren met [Azure Firewall](../firewall/overview.md) om uitgaand verkeer te identificeren dat afkomstig is van de container 
* Namen oplossen via de interne Azure DNS communicatie met Azure-resources in het virtuele netwerk, zoals virtuele machines
* NSG-regels gebruiken om containertoegang tot subnetten of andere netwerkresources te beheren

## <a name="unsupported-networking-scenarios"></a>Niet-ondersteunde netwerkscenario's 

* **Azure Load Balancer:** het plaatsen Azure Load Balancer containerin een containergroep in een containergroep in een netwerk wordt niet ondersteund
* **Wereldwijde peering voor virtuele netwerken:** wereldwijde peering (het verbinden van virtuele netwerken tussen Azure-regio's) wordt niet ondersteund
* **Openbaar IP- of DNS-label:** containergroepen die zijn geïmplementeerd in een virtueel netwerk bieden momenteel geen ondersteuning voor het rechtstreeks beschikbaar maken van containers op internet met een openbaar IP-adres of een volledig gekwalificeerde domeinnaam
* **Virtual Network NAT:** containergroepen die zijn geïmplementeerd in een virtueel netwerk bieden momenteel geen ondersteuning voor het gebruik van een NAT-gatewayresource voor uitgaande internetverbinding.

## <a name="other-limitations"></a>Andere beperkingen

* Op dit moment worden alleen Linux-containers ondersteund in een containergroep die is geïmplementeerd in een virtueel netwerk.
* Als u containergroepen wilt implementeren in een subnet, kan het subnet geen andere resourcetypen bevatten. Verwijder alle bestaande resources uit een bestaand subnet voordat u er containergroepen in implementeert of maak een nieuw subnet.
* U kunt geen beheerde identiteit gebruiken in [een](container-instances-managed-identity.md) containergroep die is geïmplementeerd in een virtueel netwerk.
* U kunt een [](container-instances-liveness-probe.md) test of gereedheidstest niet [inschakelen](container-instances-readiness-probe.md) in een containergroep die is geïmplementeerd in een virtueel netwerk.
* Vanwege de extra betrokken netwerkresources zijn implementaties naar een virtueel netwerk doorgaans langzamer dan het implementeren van een standaardcontainer-instantie.
* Als u uw containergroep verbindt met een Azure Storage-account, moet u een [service-eindpunt toevoegen](../virtual-network/virtual-network-service-endpoints-overview.md) aan die resource.

[!INCLUDE [container-instances-restart-ip](../../includes/container-instances-restart-ip.md)]

## <a name="required-network-resources"></a>Vereiste netwerkbronnen

Er zijn drie Azure Virtual Network-resources vereist voor het implementeren van containergroepen in een virtueel netwerk: het virtuele netwerk [zelf,](#virtual-network) een gedelegeerd [subnet](#subnet-delegated) binnen het virtuele netwerk en een [netwerkprofiel.](#network-profile) 

### <a name="virtual-network"></a>Virtueel netwerk

Een virtueel netwerk definieert de adresruimte waarin u een of meer subnetten maakt. Vervolgens implementeert u Azure-resources (zoals containergroepen) in de subnetten in uw virtuele netwerk.

### <a name="subnet-delegated"></a>Subnet (gedelegeerd)

Subnetten segmenteren het virtuele netwerk in afzonderlijke adresruimten die kunnen worden gebruikt door de Azure-resources die u in deze subnetten wilt plaatsen. U maakt een of meer subnetten binnen een virtueel netwerk.

Het subnet dat u voor containergroepen gebruikt, mag alleen containergroepen bevatten. Wanneer u voor het eerst een containergroep implementeert in een subnet, delegeert Azure dat subnet aan Azure Container Instances. Zodra het subnet is gedelegeerd, kan het alleen worden gebruikt voor containergroepen. Als u andere resources dan containergroepen naar een gedelegeerd subnet probeert te implementeren, mislukt de bewerking.

### <a name="network-profile"></a>Netwerkprofiel

Een netwerkprofiel is een netwerkconfiguratiesjabloon voor Azure-resources. Hiermee geeft u bepaalde netwerkeigenschappen voor de resource op, bijvoorbeeld het subnet waarin deze moet worden geïmplementeerd. Wanneer u de opdracht [az container create voor][az-container-create] het eerst gebruikt om een containergroep te implementeren in een subnet (en dus een virtueel netwerk), maakt Azure een netwerkprofiel voor u. U kunt dat netwerkprofiel vervolgens gebruiken voor toekomstige implementaties naar het subnet. 

Als u een Resource Manager, YAML-bestand of een programmatische methode wilt gebruiken om een containergroep in een subnet te implementeren, moet u de volledige resource Resource Manager-id van een netwerkprofiel verstrekken. U kunt een profiel gebruiken dat u eerder hebt gemaakt [met az container create][az-container-create]of een profiel maken met behulp van een Resource Manager-sjabloon (zie het [sjabloonvoorbeeld](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-vnet) en de [verwijzing](/azure/templates/microsoft.network/networkprofiles)). Gebruik de opdracht az network profile [list][az-network-profile-list] om de id van een eerder gemaakt profiel op te halen. 

In het volgende diagram zijn verschillende containergroepen geïmplementeerd in een subnet dat is gedelegeerd aan Azure Container Instances. Zodra u één containergroep in een subnet hebt geïmplementeerd, kunt u er extra containergroepen voor implementeren door hetzelfde netwerkprofiel op te geven.

![Containergroepen binnen een virtueel netwerk][aci-vnet-01]

## <a name="next-steps"></a>Volgende stappen

* Zie Container-exemplaren implementeren in een virtueel Azure-netwerk voor implementatievoorbeelden [met de Azure CLI.](container-instances-vnet.md)
* Zie Een [Azure-containergroep](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-vnet
)maken met VNet als u een nieuw virtueel netwerk, subnet, netwerkprofiel en containergroep wilt implementeren met behulp van een Resource Manager-sjabloon.
* Wanneer u de [Azure Portal](container-instances-quickstart-portal.md) om een container-instantie te maken, kunt u ook instellingen opgeven voor een nieuw virtueel netwerk of een virtueel netwerk dat wordt uitgebreid op het **tabblad** Netwerken.


<!-- IMAGES -->
[aci-vnet-01]: ./media/container-instances-virtual-network-concepts/aci-vnet-01.png

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az_container_create
[az-network-profile-list]: /cli/azure/network/profile#az_network_profile_list
