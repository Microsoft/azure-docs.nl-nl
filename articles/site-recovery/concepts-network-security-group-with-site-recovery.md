---
title: Netwerk beveiligings groepen met Azure Site Recovery | Microsoft Docs
description: Hierin wordt beschreven hoe u netwerk beveiligings groepen gebruikt met Azure Site Recovery voor nood herstel en migratie
author: Harsha-CS
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: harshacs
ms.openlocfilehash: 367aba09f84da1e227c08721077aa1b2132a62bf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92367970"
---
# <a name="network-security-groups-with-azure-site-recovery"></a>Netwerkbeveiligingsgroepen met Azure Site Recovery

Netwerk beveiligings groepen worden gebruikt om het netwerk verkeer te beperken tot resources in een virtueel netwerk. Een [netwerk beveiligings groep (NSG)](../virtual-network/network-security-groups-overview.md#network-security-groups) bevat een lijst met beveiligings regels voor het toestaan of weigeren van binnenkomend of uitgaand netwerk verkeer op basis van het bron-of doel-IP-adres, de poort en het protocol.

Onder het Resource Manager-implementatie model kan Nsg's worden gekoppeld aan subnetten of afzonderlijke netwerk interfaces. Wanneer een NSG is gekoppeld aan een subnet, zijn de regels van toepassing op alle resources die zijn verbonden met het subnet. Verkeer kan verder worden beperkt door ook een NSG te koppelen aan afzonderlijke netwerk interfaces binnen een subnet dat al een gekoppelde NSG heeft.

In dit artikel wordt beschreven hoe u netwerk beveiligings groepen kunt gebruiken met Azure Site Recovery.

## <a name="using-network-security-groups"></a>Netwerk beveiligings groepen gebruiken

Een afzonderlijk subnet kan een of meer gekoppelde NSG hebben. Een afzonderlijke netwerk interface kan ook nul of één gekoppelde NSG hebben. Daarom kunt u de beperking van het dubbele verkeer voor een virtuele machine effectief doen door eerst een NSG aan een subnet te koppelen en vervolgens een andere NSG aan de netwerk interface van de VM. De toepassing van NSG-regels in dit geval is afhankelijk van de richting van het verkeer en de prioriteit van toegepaste beveiligings regels.

U kunt als volgt een eenvoudig voor beeld met één virtuele machine beschouwen:
-    De virtuele machine wordt in het **Contoso-subnet** geplaatst.
-    Het **Contoso-subnet** is gekoppeld aan het **subnet NSG**.
-    De VM-netwerk interface is ook gekoppeld aan de **VM-NSG**.

![NSG met Site Recovery](./media/concepts-network-security-group-with-site-recovery/site-recovery-with-network-security-group.png)

In dit voor beeld wordt voor inkomend verkeer het subnet NSG eerst geëvalueerd. Elk verkeer dat via het subnet NSG wordt toegestaan, wordt vervolgens geëvalueerd door de VM-NSG. De omgekeerde is van toepassing op uitgaand verkeer, waarbij de VM-NSG eerst wordt geëvalueerd. Elk verkeer dat via de VM-NSG is toegestaan, wordt vervolgens geëvalueerd door het subnet NSG.

Hiermee wordt de toepassing voor de beveiligings regel nauw keuriger. U kunt bijvoorbeeld inkomende Internet toegang toestaan voor een aantal toepassings-Vm's (zoals frontend-Vm's) onder een subnet, maar inkomende Internet toegang beperken tot andere virtuele machines (zoals data bases en andere back-end Vm's). In dit geval kunt u een meer lenient-regel gebruiken op het subnet NSG, het toestaan van Internet verkeer en het beperken van de toegang tot specifieke Vm's door toegang tot VM-NSG te weigeren. Dit kan ook worden toegepast voor uitgaand verkeer.

Bij het instellen van dergelijke NSG-configuraties moet u ervoor zorgen dat de juiste prioriteiten worden toegepast op de [beveiligings regels](../virtual-network/network-security-groups-overview.md#security-rules). Regels worden verwerkt in volgorde van prioriteit, waarbij lagere getallen worden verwerkt vóór hogere getallen omdat lagere getallen een hogere prioriteit hebben. Zodra het verkeer overeenkomt met een regel, wordt de verwerking beëindigd. Daardoor worden regels met een lagere prioriteit (een hoger getal) die dezelfde kenmerken hebben als regels met een hogere prioriteit, niet verwerkt.

U ben er mogelijk niet altijd van op de hoogte wanneer netwerkbeveiligingsgroepen worden toegepast op zowel een netwerkinterface als een subnet. U kunt de geaggregeerde regels controleren die worden toegepast op een netwerk interface door de [juiste beveiligings regels](../virtual-network/virtual-network-network-interface.md#view-effective-security-rules) voor een netwerk interface te bekijken. U kunt ook de functie voor het controleren van de [IP-stroom](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md) in [Azure Network Watcher](../network-watcher/network-watcher-monitoring-overview.md) gebruiken om te bepalen of communicatie is toegestaan van of naar een netwerk interface. Deze functie vertelt u of communicatie is toegestaan en welke netwerkbeveiligingsregel verkeer toestaat of weigert.

## <a name="on-premises-to-azure-replication-with-nsg"></a>Replicatie van on-premises naar Azure met NSG

Azure Site Recovery herstel na nood gevallen en migratie naar Azure mogelijk maken voor on-premises [virtuele Hyper-V-machines](hyper-v-azure-architecture.md), [virtuele VMware-machines](vmware-azure-architecture.md)en [fysieke servers](physical-azure-architecture.md). Voor alle on-premises naar Azure-scenario's worden er replicatie gegevens verzonden naar en opgeslagen in een Azure Storage-account. Tijdens de replicatie betaalt u geen kosten voor de virtuele machine. Wanneer u een failover naar Azure uitvoert, maakt Site Recovery automatisch virtuele Azure IaaS-machines.

Zodra Vm's zijn gemaakt na een failover naar Azure, kan Nsg's worden gebruikt om het netwerk verkeer naar het virtuele netwerk en Vm's te beperken. Site Recovery maakt geen Nsg's als onderdeel van de failover-bewerking. Het is raadzaam om de vereiste Azure Nsg's te maken voordat u de failover initieert. U kunt Nsg's vervolgens automatisch koppelen aan failover van Vm's tijdens het uitvoeren van de virtuele machine, met behulp van automatiserings scripts met de krachtige [herstel plannen](site-recovery-create-recovery-plans.md)van site Recovery.

Als de VM-configuratie na de failover bijvoorbeeld overeenkomt met het [voorbeeld scenario](concepts-network-security-group-with-site-recovery.md#using-network-security-groups) dat hierboven wordt beschreven:
-    U kunt **Contoso VNet** en **Contoso-subnet** maken als onderdeel van de Dr-planning op de Azure-doel regio.
-    U kunt ook zowel de NSG voor het **subnet** maken en configureren als voor de **VM-NSG** als onderdeel van dezelfde Dr-planning.
-    Het **SUBNET NSG** kan vervolgens direct worden gekoppeld aan het **Contoso-subnet**, omdat de NSG en het subnet al beschikbaar zijn.
-    **VM-NSG** kunnen worden gekoppeld aan vm's tijdens een failover met behulp van herstel plannen.

Zodra de Nsg's zijn gemaakt en geconfigureerd, wordt u aangeraden [een testfailover uit](site-recovery-test-failover-to-azure.md) te voeren om de NSG-koppelingen in het script te verifiëren en om na te gaan of de VM-verbinding na de failover.

## <a name="azure-to-azure-replication-with-nsg"></a>Replicatie van Azure naar Azure met NSG

Azure Site Recovery kunnen nood herstel van [virtuele machines van Azure](azure-to-azure-architecture.md)worden hersteld. Wanneer u replicatie voor virtuele Azure-machines inschakelt, kunt Site Recovery de virtuele replica netwerken (inclusief subnetten en gateway subnetten) maken op de doel regio en de vereiste toewijzingen maken tussen de bron-en doel-virtuele netwerken. U kunt ook de doel netwerken en subnetten vooraf maken en dezelfde gebruiken bij het inschakelen van replicatie. Site Recovery maakt geen Vm's in de Azure-doel regio voorafgaand aan de [failover](azure-to-azure-tutorial-failover-failback.md).

Zorg ervoor dat voor Azure VM-replicatie de NSG-regels in de Azure-regio voor [uitgaande verbindingen](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags) voor replicatie verkeer zijn toegestaan. U kunt deze vereiste regels ook testen en controleren met behulp van dit [voor beeld NSG-configuratie](azure-to-azure-about-networking.md#example-nsg-configuration).

Site Recovery maakt of repliceert Nsg's niet als onderdeel van de failoverbewerking. We raden u aan de vereiste Nsg's te maken op de Azure-doel regio voordat u de failover initieert. U kunt Nsg's vervolgens automatisch koppelen aan failover van Vm's tijdens het uitvoeren van de virtuele machine, met behulp van automatiserings scripts met de krachtige [herstel plannen](site-recovery-create-recovery-plans.md)van site Recovery.

Overwegend het [voorbeeld scenario](concepts-network-security-group-with-site-recovery.md#using-network-security-groups) dat eerder is beschreven:
-    Site Recovery kunt replica's maken van **Contoso VNet** en **Contoso-subnet** in de Azure-doel regio wanneer replicatie is ingeschakeld voor de virtuele machine.
-    U kunt de gewenste replica's van **SUBNET NSG** en VM- **NSG** (met de naam, bijvoorbeeld de **doel-subnet NSG** en de **doel-VM NSG**) maken op de Azure-doel regio, waardoor er extra regels voor de doel regio vereist zijn.
-    Het **doel-SUBNET NSG** kan vervolgens direct worden gekoppeld aan het doel regio subnet, omdat zowel de NSG als het subnet al beschikbaar zijn.
-    De **doel-VM-NSG** kunnen worden gekoppeld aan vm's tijdens een failover met behulp van herstel plannen.

Zodra de Nsg's zijn gemaakt en geconfigureerd, wordt u aangeraden [een testfailover uit](azure-to-azure-tutorial-dr-drill.md) te voeren om de NSG-koppelingen in het script te verifiëren en om na te gaan of de VM-verbinding na de failover.

## <a name="next-steps"></a>Volgende stappen
-    Meer informatie over [Netwerkbeveiligingsgroepen](../virtual-network/network-security-groups-overview.md#network-security-groups).
-    Meer informatie over NSG- [beveiligings regels](../virtual-network/network-security-groups-overview.md#security-rules).
-    Meer informatie over de [juiste beveiligings regels](../virtual-network/diagnose-network-traffic-filter-problem.md) voor een NSG.
-    Meer informatie over [herstel plannen](site-recovery-create-recovery-plans.md) voor het automatiseren van de failover van de toepassing.