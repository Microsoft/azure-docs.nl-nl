---
title: Over het gebruik van ExpressRoute met Azure Site Recovery
description: Hierin wordt beschreven hoe u Azure ExpressRoute gebruikt met de Azure Site Recovery-service voor herstel na nood gevallen en migratie.
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/13/2019
ms.author: mayg
ms.openlocfilehash: 99fa8d4cf8f48d0fe72da36baef20c83add438c0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94330254"
---
# <a name="azure-expressroute-with-azure-site-recovery"></a>Azure-ExpressRoute met Azure Site Recovery

Met Microsoft Azure ExpressRoute kunt u uw on-premises netwerken in de Microsoft Cloud uitbreiden via een persoonlijke verbinding die wordt gefaciliteerd door een connectiviteitsprovider. Met ExpressRoute kunt u verbindingen tot stand brengen met Microsoft Cloud-services, zoals Microsoft Azure, Microsoft 365 en Dynamics 365.

In dit artikel wordt beschreven hoe u Azure ExpressRoute kunt gebruiken met Azure Site Recovery voor herstel na nood gevallen en migratie.

## <a name="expressroute-circuits"></a>ExpressRoute-circuits

Een ExpressRoute-circuit vertegenwoordigt een logische verbinding tussen uw on-premises infra structuur en micro soft-Cloud Services via een connectiviteits provider. U kunt meerdere ExpressRoute-circuits best Ellen. Elk circuit kan zich in dezelfde of verschillende regio's bevindt en kan worden aangesloten op uw locatie via verschillende connectiviteits providers. Meer informatie over ExpressRoute-circuits [vindt u hier](../expressroute/expressroute-circuit-peerings.md).

Er zijn meerdere routerings domeinen gekoppeld aan een ExpressRoute-circuit. Meer informatie over en vergelijkt u ExpressRoute-routerings [domeinen.](../expressroute/expressroute-circuit-peerings.md#peeringcompare)

## <a name="on-premises-to-azure-replication-with-expressroute"></a>Replicatie van on-premises naar Azure met ExpressRoute

Azure Site Recovery herstel na nood gevallen en migratie naar Azure mogelijk maken voor on-premises [virtuele Hyper-V-machines](hyper-v-azure-architecture.md), [virtuele VMware-machines](vmware-azure-architecture.md)en [fysieke servers](physical-azure-architecture.md). Voor alle on-premises naar Azure-scenario's worden er replicatie gegevens verzonden naar en opgeslagen in een Azure Storage-account. Tijdens de replicatie betaalt u geen kosten voor de virtuele machine. Wanneer u een failover naar Azure uitvoert, maakt Site Recovery automatisch virtuele Azure IaaS-machines.

Site Recovery repliceert gegevens naar een Azure Storage account of een door de replica beheerde schijf in de Azure-doel regio via een openbaar eind punt. Als u ExpressRoute wilt gebruiken voor Site Recovery replicatie verkeer, kunt u gebruikmaken van [micro soft-peering](../expressroute/expressroute-circuit-peerings.md#microsoftpeering) of een bestaande [open bare peering](../expressroute/about-public-peering.md) (afgeschaft voor nieuwe creatie). Micro soft-peering is het aanbevolen routerings domein voor replicatie. Houd er rekening mee dat replicatie alleen wordt ondersteund via particuliere peering als [privé-eind punten zijn ingeschakeld voor de kluis](hybrid-how-to-enable-replication-private-endpoints.md).

Zorg er ook voor dat aan de [netwerk vereisten](vmware-azure-configuration-server-requirements.md#network-requirements) voor de configuratie server wordt voldaan. De configuratie server voor de indeling van Site Recovery replicatie vereist dat er verbinding wordt met specifieke Url's. ExpressRoute kan niet worden gebruikt voor deze verbinding. 

Als u on-premises proxy gebruikt en ExpressRoute voor replicatie verkeer wilt gebruiken, moet u de lijst met ongebruikte proxy configureren op de configuratie server en de proces servers. Volg de onderstaande stappen:

- Down load het PsExec-hulp programma van [hier](/sysinternals/downloads/psexec) om toegang te krijgen tot systeem gebruikers context.
- Open Internet Explorer in de context van het systeem gebruikers door de volgende opdracht regel PsExec-s-i "%programfiles%\Internet Explorer\iexplore.exe" uit te voeren.
- Proxy-instellingen in Internet Explorer toevoegen
- Voeg in de lijst overs Laan de Azure Storage-URL *. blob.core.windows.net

Dit zorgt ervoor dat alleen replicatie verkeer via ExpressRoute loopt terwijl de communicatie via de proxy kan passeren.

Nadat de failover van virtuele machines of servers naar een virtueel Azure-netwerk is uitgevoerd, kunt u deze openen met behulp van [privé-peering](../expressroute/expressroute-circuit-peerings.md#privatepeering). 

Het gecombineerde scenario wordt in het volgende diagram weer gegeven: ![ on-premises-naar-Azure met ExpressRoute](./media/concepts-expressroute-with-site-recovery/site-recovery-with-expressroute.png)

## <a name="azure-to-azure-replication-with-expressroute"></a>Replicatie van Azure naar Azure met ExpressRoute

Azure Site Recovery kunnen nood herstel van [virtuele machines van Azure](azure-to-azure-architecture.md)worden hersteld. Afhankelijk van of uw virtuele Azure-machines [azure Managed disks](../virtual-machines/managed-disks-overview.md)gebruiken, worden de replicatie gegevens verzonden naar een Azure Storage account of een door de replica beheerde schijf in de Azure-doel regio. Hoewel de replicatie-eind punten openbaar zijn, wordt het replicatie verkeer voor Azure VM-replicatie standaard niet door lopen via internet, ongeacht de Azure-regio waarin het virtuele bron netwerk zich bevindt. U kunt de standaard systeem route van Azure voor het adres voorvoegsel 0.0.0.0/0 vervangen door een aangepaste route en VM-verkeer [omleiden](../virtual-network/virtual-networks-udr-overview.md#custom-routes) naar een on-premises netwerk virtueel apparaat (NVA), maar deze configuratie wordt niet aanbevolen voor replicatie van site Recovery. Als u aangepaste routes gebruikt, moet u [een service-eind punt voor een virtueel netwerk maken](azure-to-azure-about-networking.md#create-network-service-endpoint-for-storage) in het virtuele netwerk voor "opslag" zodat het replicatie verkeer de Azure-grens niet verlaat.

Voor Azure VM-nood herstel is ExpressRoute standaard niet vereist voor replicatie. Nadat de failover van virtuele machines naar de Azure-doel regio is uitgevoerd, kunt u deze openen met behulp van [privé-peering](../expressroute/expressroute-circuit-peerings.md#privatepeering). Houd er rekening mee dat tarieven voor gegevens overdracht van toepassing zijn onafhankelijk van de modus voor gegevens replicatie tussen Azure-regio's.

Als u al gebruikmaakt van ExpressRoute om verbinding te maken vanaf uw on-premises Data Center met de Azure-Vm's in de bron regio, kunt u een planning maken voor het opnieuw instellen van de ExpressRoute-connectiviteit in de doel regio van de failover. U kunt hetzelfde ExpressRoute-circuit gebruiken om via een nieuwe virtuele netwerk verbinding verbinding te maken met de doel regio, of gebruik een afzonderlijk ExpressRoute-circuit en verbinding voor nood herstel. De verschillende mogelijke scenario's worden [hier](azure-vm-disaster-recovery-with-expressroute.md#fail-over-azure-vms-when-using-expressroute)beschreven.

U kunt virtuele Azure-machines repliceren naar elke Azure-regio binnen hetzelfde geografische cluster, zoals [hier](../site-recovery/azure-to-azure-support-matrix.md#region-support)wordt beschreven. Als de gekozen Azure-doel regio zich niet in dezelfde geopolitieke regio als de bron bevindt, moet u mogelijk ExpressRoute Premium inschakelen. Raadpleeg [ExpressRoute-locaties](../expressroute/expressroute-locations.md) en [ExpressRoute-prijzen](https://azure.microsoft.com/pricing/details/expressroute/)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [ExpressRoute-circuits](../expressroute/expressroute-circuit-peerings.md).
- Meer informatie over [ExpressRoute-routerings domeinen](../expressroute/expressroute-circuit-peerings.md#peeringcompare).
- Meer informatie over [ExpressRoute-locaties](../expressroute/expressroute-locations.md).
- Meer informatie over herstel na nood gevallen van [virtuele Azure-machines met ExpressRoute](azure-vm-disaster-recovery-with-expressroute.md).