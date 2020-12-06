---
title: Virtuele netwerk integratie van Azure-Services voor netwerk isolatie
titlesuffix: Azure Virtual Network
description: In dit artikel worden verschillende methoden beschreven voor het integreren van een Azure-service in een virtueel netwerk waarmee u veilig toegang kunt krijgen tot de Azure-service.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.workload: infrastructure-services
ms.date: 12/01/2020
ms.author: kumud
ms.openlocfilehash: 4a6cd529511d4a2e71e1a31c1600f8a51f455a37
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/06/2020
ms.locfileid: "96746128"
---
# <a name="integrate-azure-services-with-virtual-networks-for-network-isolation"></a>Azure-Services integreren met virtuele netwerken voor netwerk isolatie

Dankzij de integratie van Virtual Network (VNet) voor een Azure-service kunt u de toegang tot de service alleen vergren delen op de infra structuur van uw virtuele netwerk. De VNet-infra structuur omvat ook gepeerde virtuele netwerken en on-premises netwerken.

VNet-integratie biedt Azure-Services de voor delen van netwerk isolatie en kan worden bereikt met een of meer van de volgende methoden:
- [Implementatie van toegewezen exemplaren van de service in een virtueel netwerk](virtual-network-service-endpoints-overview.md). De services kunnen vervolgens worden geopend in het virtuele netwerk en vanuit on-premises netwerken.
- Het gebruik van een [privé-eind punt](../private-link/private-endpoint-overview.md) waarmee u privé en veilig een service kunt verbinden met een [persoonlijke Azure-koppeling](../private-link/private-link-overview.md). Persoonlijk eind punt gebruikt een privé-IP-adres uit uw VNet, waardoor de service in het virtuele netwerk effectief wordt.
- Toegang tot de service via open bare eind punten door een virtueel netwerk uit te breiden naar de service, via [service-eind punten](virtual-network-service-endpoints-overview.md). Met service-eindpunten kunnen serviceresources worden beveiligd naar het virtuele netwerk.
- [Service Tags](service-tags-overview.md) gebruiken om verkeer naar uw Azure-resources toe te staan of te weigeren van open bare IP-eind punten.

## <a name="deploy-dedicated-azure-services-into-virtual-networks"></a>Speciale Azure-Services implementeren in virtuele netwerken

Wanneer u toegewezen Azure-Services in een virtueel netwerk implementeert, kunt u met de service bronnen privé communiceren via privé-IP-adressen.

![Speciale Azure-Services implementeren in virtuele netwerken](./media/virtual-network-for-azure-services/deploy-service-into-vnet.png)

Het implementeren van een speciale Azure-service in uw virtuele netwerk biedt de volgende mogelijkheden:
- Resources binnen het virtuele netwerk kunnen met elkaar communiceren via privé-IP-adressen. Zo kunt u gegevens rechtstreeks overbrengen tussen HDInsight en SQL Server die worden uitgevoerd op een virtuele machine, in het virtuele netwerk.
- On-premises resources hebben toegang tot bronnen in een virtueel netwerk met behulp van privé-IP-adressen via een site-to-site VPN (VPN Gateway) of ExpressRoute.
- Virtuele netwerken kunnen worden gekoppeld om resources in de virtuele netwerken met elkaar te laten communiceren met behulp van privé-IP-adressen.
- Service-exemplaren in een virtueel netwerk worden doorgaans volledig beheerd door de Azure-service. Dit omvat het controleren van de status van de resources en schalen met laden.
- Service-exemplaren worden geïmplementeerd in een subnet in een virtueel netwerk. Binnenkomende en uitgaande netwerk toegang voor het subnet moet worden geopend via netwerk beveiligings groepen, volgens de richt lijnen van de service.
- Bepaalde services leggen ook beperkingen op het subnet waarin ze worden geïmplementeerd, waardoor de toepassing van beleids regels wordt beperkt, de virtuele machines en service bronnen binnen hetzelfde subnet worden gedistribueerd of gecombineerd. Controleer bij elke service op de specifieke beperkingen, aangezien deze na verloop van tijd kunnen veranderen. Voor beelden van dergelijke services zijn Azure NetApp Files, toegewezen HSM, Azure Container Instances App Service.
- Services kunnen eventueel een gedelegeerd subnet vereisen als een expliciete id die een subnet kan hosten voor een bepaalde service. Door het delegeren van Services krijgen expliciete machtigingen voor het maken van servicespecifieke bronnen in het gedelegeerde subnet.
- Bekijk een voor beeld van een REST API antwoord op een virtueel netwerk met een overgedragen subnet. Een uitgebreide lijst met services die gebruikmaken van het gedelegeerde subnet model kan worden verkregen via de beschik bare delegaties-API.

Zie [speciale Azure-Services implementeren in virtuele netwerken](virtual-network-for-azure-services.md)voor een lijst met services die kunnen worden geïmplementeerd in een virtueel netwerk.

## <a name="private-link-and-private-endpoints"></a>Persoonlijke koppeling en persoonlijke eind punten

U kunt privé-eind punten gebruiken om binnenkomend verkeer rechtstreeks vanuit uw virtuele netwerk naar Azure-resource veilig te maken via een privé-verbinding zonder via het open bare Internet te gaan. Een persoonlijk eind punt is een speciale netwerk interface voor een Azure-service in uw virtuele netwerk. Wanneer u een persoonlijk eind punt voor uw Azure-resource maakt, biedt het een beveiligde verbinding tussen clients in uw virtuele netwerk en uw Azure-resource. Het persoonlijke eind punt krijgt een IP-adres uit het IP-adres bereik van uw virtuele netwerk. De verbinding tussen het persoonlijke eind punt en de Azure-service maakt gebruik van een beveiligde persoonlijke koppeling.

In het volgende voor beeld wordt de persoonlijke toegang weer gegeven van een privé-eind punt van Event Grid bron dat zorgt voor een veilige connectiviteit tussen clients in een virtueel netwerk en Event Grid bron.

![Persoonlijke toegang tot de SQL DB-resource met behulp van een persoonlijk eind punt](./media/network-isolation/architecture-diagram.png)

Zie [Wat is een persoonlijke koppeling?](../private-link/private-link-overview.md) voor meer informatie over persoonlijke koppelingen en een lijst met ondersteunde Azure-Services.

## <a name="service-endpoints"></a>Service-eindpunten
VNet-service-eind punt biedt veilige en directe connectiviteit met Azure-Services via een geoptimaliseerde route via het Azure-backbone-netwerk. Met eindpunten kunt u uw kritieke Azure-serviceresources alleen beveiligen naar uw virtuele netwerken. Met Service-eind punten kunnen privé IP-adressen in het VNet het eind punt van een Azure-service bereiken zonder dat hiervoor een openbaar IP-adres op het VNet nodig is.

![Azure-services aan virtuele netwerken koppelen](./media/virtual-network-service-endpoints-overview/VNet_Service_Endpoints_Overview.png)

Zie [service-eind punten voor virtueel netwerk](virtual-network-service-endpoints-overview.md) voor meer informatie.

## <a name="service-tags"></a>Servicetags

Een servicetag vertegenwoordigt een groep IP-adres voorvoegsels van een bepaalde Azure-service. Met Service tags kunt u netwerk toegangs beheer definiëren voor [netwerk beveiligings groepen](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules) of [Azure firewall](https://docs.microsoft.com/azure/firewall/service-tags). Door de naam van de service label (bijvoorbeeld AzureEventGrid) op te geven in het juiste bron-of doel veld van een regel, kunt u het verkeer voor de bijbehorende service toestaan of weigeren.

![Verkeer toestaan of weigeren met Service Tags](./media/network-isolation/service-tags.png)

U kunt service tags gebruiken om netwerk isolatie te bereiken en uw Azure-resources te beveiligen via het algemene Internet tijdens het openen van Azure-Services met open bare eind punten. Maak regels voor binnenkomende/uitgaande netwerk beveiligings groepen om verkeer naar/van **Internet** te weigeren en verkeer naar/van **Cloud** of andere [beschik bare service Tags](service-tags-overview.md#available-service-tags) van specifieke Azure-Services toe te staan.

Zie [service Tags Overview](service-tags-overview.md) (Engelstalig) voor meer informatie over service tags en Azure-Services die ze ondersteunen.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [integreren van uw app met een Azure-netwerk](../app-service/web-sites-integrate-with-vnet.md).
- Meer informatie over het [beperken van toegang tot resources met behulp van service Tags](tutorial-restrict-network-access-to-resources.md).
- Meer informatie over hoe u [privé verbinding maakt met een Azure Cosmos-account met behulp van een persoonlijke Azure-koppeling](../private-link/create-private-endpoint-cosmosdb-portal.md).