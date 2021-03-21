---
title: Beschikbaarheids opties voor Azure Virtual Machines
description: Meer informatie over de beschik bare opties voor het uitvoeren van virtuele machines in azure
author: mimckitt
ms.author: mimckitt
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 03/08/2021
ms.reviewer: cynthn
ms.openlocfilehash: 1ea87d40430dbf3edabd557b80ab1456b49f4605
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102507871"
---
# <a name="availability-options-for-azure-virtual-machines"></a>Beschikbaarheids opties voor Azure Virtual Machines
Dit artikel bevat een overzicht van de beschik bare opties voor Azure virtual machines (Vm's).

## <a name="availability-zones"></a>Beschikbaarheidszones

[Beschikbaarheids zones](../availability-zones/az-overview.md?context=/azure/virtual-machines/context/context) breiden het beheer niveau uit dat u nodig hebt om de beschik baarheid van de toepassingen en gegevens op uw vm's te behouden. Een beschikbaarheids zone is een fysiek gescheiden zone binnen een Azure-regio. Er zijn drie Beschikbaarheidszones per ondersteunde Azure-regio. 

Elke beschikbaarheidszone heeft een afzonderlijke voedingsbron en koeling, en een afzonderlijk netwerk. Door uw oplossingen te ontwerpen voor het gebruik van gerepliceerde Vm's in zones, kunt u uw apps en gegevens beveiligen tegen verlies van een Data Center. Als er een zone wordt aangetast, zijn de gerepliceerde apps en gegevens direct beschikbaar in een andere zone. 

## <a name="availability-sets"></a>Beschikbaarheidssets
Een [beschikbaarheidsset](availability-set-overview.md) is een logische groepering van Vm's waarmee Azure kan begrijpen hoe uw toepassing is gebouwd om te voorzien in redundantie en beschik baarheid. We raden u aan twee of meer virtuele machines te maken binnen een beschikbaarheidsset om te voorzien in een Maxi maal beschik bare toepassing en om te voldoen aan de [99,95% Azure Sla](https://azure.microsoft.com/support/legal/sla/virtual-machines/). Er zijn geen kosten voor de Beschikbaarheidsset zelf, u betaalt alleen voor elk VM-exemplaar dat u maakt.


## <a name="virtual-machines-scale-sets"></a>Virtual Machines schaal sets 

Met de [virtuele-machine schaal sets van Azure](../virtual-machine-scale-sets/overview.md?context=/azure/virtual-machines/context/context) kunt u een groep met virtuele machines met taak verdeling maken en beheren. Het aantal VM-exemplaren kan automatisch toe- of afnemen in reactie op de vraag of volgens een ingesteld schema. Schaal sets bieden een hoge Beschik baarheid voor uw toepassingen en u kunt een groot aantal virtuele machines centraal beheren, configureren en bijwerken. We raden u aan twee of meer virtuele machines te maken binnen een schaalset om te voorzien in een Maxi maal beschik bare toepassing en om te voldoen aan de [99,95% Azure Sla](https://azure.microsoft.com/support/legal/sla/virtual-machines/). Er zijn geen kosten verbonden aan de schaalset zelf, u betaalt alleen voor elk VM-exemplaar dat u maakt.

Virtuele machines in een schaalset kunnen ook in één beschikbaarheids zone of regio worden geïmplementeerd. De implementatie opties voor de beschikbaarheids zone kunnen verschillen op basis van de [Orchestration-modus](../virtual-machine-scale-sets/virtual-machine-scale-sets-orchestration-modes.md?context=/azure/virtual-machines/context/context).

## <a name="load-balancer"></a>Load balancer
Combi neer de [Azure Load Balancer](../load-balancer/load-balancer-overview.md) met een beschikbaarheids zone of beschikbaarheidsset om de meeste toepassings tolerantie te verkrijgen. De Azure Load Balancer verdeelt het verkeer tussen meerdere virtuele machines. De Azure Load Balancer is voor virtuele machines uit de prijscategorie Standard bij de prijs inbegrepen. De Azure Load Balancer is niet bij alle prijscategorieën voor virtuele machines inbegrepen. Zie **virtuele machines met taak verdeling** voor [Linux](linux/tutorial-load-balancer.md) of [Windows](windows/tutorial-load-balancer.md)voor meer informatie over taak verdeling voor uw virtuele machines.


## <a name="azure-storage-redundancy"></a>Azure Storage-redundantie
Azure Storage slaat altijd meerdere kopieën van uw gegevens op, zodat deze beschermd zijn tegen geplande en niet-geplande gebeurtenissen, waaronder tijdelijke hardwarestoringen, netwerk-of energie storingen en enorme natuur rampen. Redundantie zorgt ervoor dat uw opslag account voldoet aan de beschik baarheid en duurzaamheids doelen, zelfs in het geval van storingen.

Bij het bepalen van de optie voor redundantie voor uw scenario, kunt u rekening houden met de compromissen met lagere kosten en een hogere Beschik baarheid. De factoren waarmee u de gewenste redundantie optie kunt bepalen, zijn onder andere:
- Hoe uw gegevens worden gerepliceerd in de primaire regio
- Of uw gegevens worden gerepliceerd naar een tweede regio die geografisch onbereikbaar is naar de primaire regio, ter bescherming tegen regionale rampen
- Of voor uw toepassing Lees toegang is vereist voor de gerepliceerde gegevens in de secundaire regio als de primaire regio om een of andere reden niet beschikbaar is

Zie [Azure Storage redundantie](../storage/common/storage-redundancy.md) voor meer informatie

## <a name="azure-site-recovery"></a>Azure Site Recovery
Als organisatie moet u een BCDR-strategie (Business Continunity and Disaster Recovery) gebruiken die ervoor zorgt dat uw gegevens zijn beveiligd, en uw apps en workloads online blijven tijdens geplande en ongeplande onderbrekingen.

[Azure site Recovery](../site-recovery/site-recovery-overview.md) helpt bedrijfs continuïteit te garanderen door zakelijke apps en workloads die worden uitgevoerd tijdens uitval te houden. Met Site Recovery worden workloads die worden uitgevoerd op fysieke en virtuele machines (VM’s), gerepliceerd van een primaire site naar een secundaire locatie. Wanneer een onderbreking optreedt op de primaire site, voert u een failover uit naar de secundaire locatie, waar u vervolgens toegang hebt tot de apps. Als de primaire locatie weer actief is, kunt u een failback naar deze site uitvoeren.

Met Site Recovery kunt u replicatie beheren voor:
- Azure-VM's die worden gerepliceerd tussen Azure-regio's.
- On-premises VM's, Azure Stack-VM's en fysieke servers.

## <a name="next-steps"></a>Volgende stappen
- [Een virtuele machine maken in een beschikbaarheids zone](/linux/create-cli-availability-zone.md)
- [Een virtuele machine maken in een beschikbaarheidsset](/linux/tutorial-availability.md)
- [Een virtuele-machineschaalset maken](../virtual-machine-scale-sets/quick-create-portal.md)