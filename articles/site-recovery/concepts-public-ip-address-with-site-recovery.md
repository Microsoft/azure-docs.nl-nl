---
title: Open bare IP-adressen toewijzen na een failover met Azure Site Recovery
description: Hierin wordt beschreven hoe u open bare IP-adressen instelt met Azure Site Recovery en Azure Traffic Manager voor herstel na nood gevallen en migratie
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: mayg
ms.openlocfilehash: 01c2f61dcf024e8c9dbbd5b2ee11a479b3c16305
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "86130276"
---
# <a name="set-up-public-ip-addresses-after-failover"></a>Open bare IP-adressen instellen na failover

Door openbare IP-adressen te gebruiken, zijn internetbronnen in staat inkomende communicatie voor Azure-resources te verwerken. Openbare IP-adressen maken het ook mogelijk dat Azure-resources uitgaande communicatie naar internet en openbare Azure-services kunnen afhandelen via een IP-adres dat is toegewezen aan de resource.
- Inkomende communicatie van het internet naar de resource, zoals Azure Virtual Machines (VM), Azure-toepassing gateways, Azure load balancers, Azure VPN-gateways en anderen. U kunt nog steeds communiceren met sommige resources, zoals Vm's, van het Internet, als aan een virtuele machine geen openbaar IP-adres is toegewezen, mits de virtuele machine deel uitmaakt van een load balancer back-end-pool en aan de load balancer een openbaar IP-adres is toegewezen.
- Uitgaande verbinding met internet met behulp van een voorspelbaar IP-adres. Een virtuele machine kan bijvoorbeeld uitgaand verkeer naar Internet communiceren zonder dat hieraan een openbaar IP-adres is toegewezen, maar het adres is standaard het netwerk adres dat door Azure wordt vertaald naar een onvoorspelbaar openbaar adres. Door een openbaar IP-adres toe te wijzen aan een resource kunt u bepalen welk IP-adres wordt gebruikt voor de uitgaande verbinding. Het adres kan echter wel worden gewijzigd, afhankelijk van de gekozen toewijzings methode. Zie [een openbaar IP-adres maken](../virtual-network/virtual-network-public-ip-address.md#create-a-public-ip-address)voor meer informatie. Zie [begrijpen van uitgaande verbindingen](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json)voor meer informatie over uitgaande verbindingen van Azure-resources.

In Azure Resource Manager is een openbaar IP-adres een bron met eigen eigenschappen. U kunt sommige resources aan een openbare IP-adresresource koppelen, zoals:

* Netwerkinterfaces van virtuele machines
* Internetgerichte load balancers
* VPN-gateways
* Toepassingsgateways

In dit artikel wordt beschreven hoe u open bare IP-adressen kunt gebruiken met Site Recovery.

## <a name="public-ip-address-assignment-using-recovery-plan"></a>Toewijzing van openbaar IP-adres met herstel plan

Het open bare IP-adres van de productie toepassing **kan niet worden bewaard tijdens de failover**. Werk belastingen die worden geladen als onderdeel van het failoverproces, moeten een open bare Azure IP-resource toewijzen die beschikbaar is in de doel regio. Deze stap kan hand matig worden uitgevoerd of wordt geautomatiseerd met herstel plannen. Met een herstel plan worden computers in herstel groepen verzameld. U kunt hiermee een systematisch herstel proces definiëren. U kunt een herstel plan gebruiken om een order in te stellen en de acties te automatiseren die nodig zijn bij elke stap, met Azure Automation runbooks voor failover naar Azure of scripts.

De installatie is als volgt:
- Maak een [herstel plan](../site-recovery/site-recovery-create-recovery-plans.md#create-a-recovery-plan) en groepeer zo nodig uw werk belastingen in het plan.
- Pas het plan aan door een stap toe te voegen om een openbaar IP-adres toe te voegen met [Azure Automation runbooks](../site-recovery/site-recovery-runbook-automation.md#customize-the-recovery-plan) -scripts aan de virtuele machine waarvoor een failover is uitgevoerd.

 
## <a name="public-endpoint-switching-with-dns-level-routing"></a>Open bare eindpunt wisseling met DNS-niveau routering

Azure Traffic Manager maakt route ring op DNS-niveau tussen eind punten mogelijk en kan u helpen bij [het rto's](../site-recovery/concepts-traffic-manager-with-site-recovery.md#recovery-time-objective-rto-considerations) van een Dr-scenario. 

Meer informatie over failover-scenario's met Traffic Manager:
1. [Failover van on-premises naar Azure](../site-recovery/concepts-traffic-manager-with-site-recovery.md#on-premises-to-azure-failover) met Traffic Manager 
2. [Failover van Azure naar Azure](../site-recovery/concepts-traffic-manager-with-site-recovery.md#azure-to-azure-failover) met Traffic Manager 

De installatie is als volgt:
- Maak een [Traffic Manager profiel](../traffic-manager/quickstart-create-traffic-manager-profile.md).
- Met de routerings methode **prioriteit** maakt u twee eind punten – **primair** voor de bron en **failover** voor Azure. **Primaire** is toegewezen aan prioriteit 1 en **failover** krijgt prioriteit 2.
- Het **primaire** eind punt kan [Azure](../traffic-manager/traffic-manager-endpoint-types.md#azure-endpoints) of [extern](../traffic-manager/traffic-manager-endpoint-types.md#external-endpoints) zijn, afhankelijk van het feit of uw bron omgeving zich binnen of buiten Azure bevindt.
- Het **failover** -eind punt wordt gemaakt als een **Azure** -eind punt. Gebruik een **statisch openbaar IP-adres** omdat dit een extern Facing-eind punt is voor Traffic manager in de nood geval.

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [Traffic Manager met Azure site Recovery](../site-recovery/concepts-traffic-manager-with-site-recovery.md)
- Meer informatie over Traffic Manager [routerings methoden](../traffic-manager/traffic-manager-routing-methods.md).
- Meer informatie over [herstel plannen](site-recovery-create-recovery-plans.md) voor het automatiseren van de failover van de toepassing.
