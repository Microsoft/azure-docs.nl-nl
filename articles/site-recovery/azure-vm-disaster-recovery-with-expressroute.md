---
title: Azure ExpressRoute Azure VM-nood herstel integreren met Azure Site Recovery
description: Hierin wordt beschreven hoe u herstel na nood gevallen instelt voor Azure-Vm's met behulp van Azure Site Recovery en Azure ExpressRoute
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: mayg
ms.openlocfilehash: 0e1f670f2ba5ad31f29d56b2de40acd6e2bf18a9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88654375"
---
# <a name="integrate-expressroute-with-disaster-recovery-for-azure-vms"></a>ExpressRoute integreren met nood herstel voor Azure-Vm's


In dit artikel wordt beschreven hoe u Azure ExpressRoute integreert met [Azure site Recovery](site-recovery-overview.md), wanneer u herstel na nood geval instelt voor Azure-vm's naar een secundaire Azure-regio.

Met Site Recovery kunnen Azure-Vm's in nood gevallen worden hersteld door Azure VM-gegevens te repliceren naar Azure.

- Als Azure-Vm's [Azure Managed disks](../virtual-machines/managed-disks-overview.md)gebruiken, worden VM-gegevens gerepliceerd naar een gerepliceerde beheerde schijf in de secundaire regio.
- Als Azure-Vm's geen beheerde schijven gebruiken, worden VM-gegevens gerepliceerd naar een Azure-opslag account.
- Replicatie-eind punten zijn openbaar, maar het replicatie verkeer voor Azure-Vm's heeft geen betrekking op internet.

Met ExpressRoute kunt u on-premises netwerken uitbreiden naar de Microsoft Azure Cloud via een particuliere verbinding, waardoor een connectiviteits provider wordt vergemakkelijkt. Als u ExpressRoute hebt geconfigureerd, wordt dit met Site Recovery als volgt geïntegreerd:

- **Tijdens de replicatie tussen Azure-regio's**: replicatie verkeer voor Azure VM-nood herstel is alleen binnen Azure en ExpressRoute is niet nodig of wordt niet gebruikt voor replicatie. Als u echter verbinding maakt vanaf een on-premises site naar de Azure-Vm's in de primaire Azure-site, zijn er een aantal problemen waarvan u rekening moet houden wanneer u herstel na nood gevallen instelt voor die virtuele Azure-machines.
- **Failover tussen Azure-regio's**: wanneer er storingen optreden, kunt u een failover uitvoeren van Azure-vm's van de primaire naar de secundaire Azure-regio. Na een failover naar een secundaire regio, zijn er een aantal stappen die u moet uitvoeren om toegang te krijgen tot de virtuele Azure-machines in de secundaire regio met behulp van ExpressRoute.


## <a name="before-you-begin"></a>Voordat u begint

Voordat u begint, moet u de volgende concepten begrijpen:

- ExpressRoute- [circuits](../expressroute/expressroute-circuit-peerings.md)
- ExpressRoute- [routerings domeinen](../expressroute/expressroute-circuit-peerings.md#routingdomains)
- ExpressRoute [locaties](../expressroute/expressroute-locations.md).
- Azure VM- [replicatie architectuur](azure-to-azure-architecture.md)
- [Replicatie instellen](azure-to-azure-tutorial-enable-replication.md) voor virtuele Azure-machines.
- Over het [uitvoeren](azure-to-azure-tutorial-failover-failback.md) van een failover voor virtuele Azure-machines.


## <a name="general-recommendations"></a>Algemene aanbevelingen

Voor best practice en om te zorgen voor efficiënte herstel tijd doel stellingen (Rto's) voor herstel na nood gevallen, raden we u aan het volgende te doen wanneer u Site Recovery instelt om te integreren met ExpressRoute:

- Netwerk onderdelen inrichten vóór een failover naar een secundaire regio:
    - Wanneer u replicatie voor virtuele Azure-machines inschakelt, kan Site Recovery netwerk bronnen, zoals netwerken, subnetten en gateways, automatisch implementeren in de Azure-doel regio, op basis van de instellingen van het bron netwerk.
    - Site Recovery kan geen netwerk bronnen, zoals VNet-gateways, automatisch instellen.
    - U wordt aangeraden deze extra netwerk bronnen in te richten vóór de failover. Een kleine uitval tijd is gekoppeld aan deze implementatie en kan van invloed zijn op de totale herstel tijd, als u deze niet hebt gezien tijdens de implementatie planning.
- Normale nood herstel oefeningen uitvoeren:
    - Een analyse valideert uw replicatiestrategie zonder gegevensverlies of uitvaltijd, en is niet van invloed op uw productieomgeving. Het helpt bij het voor komen van problemen met de configuratie van het laatste moment dat de RTO nadelig kan beïnvloeden.
    - Wanneer u een testfailover voor de analyse uitvoert, raden we u aan om een afzonderlijk Azure VM-netwerk te gebruiken in plaats van het standaard netwerk dat wordt ingesteld wanneer u replicatie inschakelt.
- Gebruik verschillende IP-adres ruimten als u een enkel ExpressRoute-circuit hebt.
    - U wordt aangeraden een andere IP-adres ruimte te gebruiken voor het virtuele netwerk van het doel. Dit voor komt problemen bij het tot stand brengen van verbindingen tijdens regionale storingen.
    - Als u geen afzonderlijke adres ruimte kunt gebruiken, moet u ervoor zorgen dat u de testfailover voor herstel na nood gevallen uitvoert op een afzonderlijk test netwerk met verschillende IP-adressen. U kunt niet twee VNets koppelen met overlappende IP-adres ruimte aan hetzelfde ExpressRoute-circuit.

## <a name="replicate-azure-vms-when-using-expressroute"></a>Virtuele Azure-machines repliceren wanneer u ExpressRoute gebruikt


Als u replicatie wilt instellen voor virtuele Azure-machines in een primaire site en u vanaf uw on-premises site via ExpressRoute verbinding maakt met deze virtuele machines, kunt u het volgende doen:

1. [Schakel replicatie in](azure-to-azure-tutorial-enable-replication.md) voor elke virtuele machine van Azure.
2. U kunt eventueel Site Recovery netwerk instellen:
    - Wanneer u replicatie configureert en inschakelt, worden in Site Recovery netwerken, subnetten en gateway-subnetten in de Azure-doel regio ingesteld, zodat deze overeenkomen met die in de bron regio. Site Recovery is ook gekoppeld tussen de bron-en doel-virtuele netwerken.
    - Als u Site Recovery dit niet automatisch wilt doen, maakt u de netwerk resources aan de doel zijde voordat u replicatie inschakelt.
3. Andere netwerk elementen maken:
    - Site Recovery maakt geen route tabellen, VNet-gateways, vnet-gateway verbindingen, VNet-peering of andere netwerk bronnen en verbindingen in de secundaire regio.
    - U moet deze extra netwerk elementen in de secundaire regio maken, telkens voordat u een failover uitvoert vanuit de primaire regio.
    - U kunt [herstel plannen](site-recovery-create-recovery-plans.md) en automatiserings scripts gebruiken om deze netwerk bronnen in te stellen en te verbinden.
1. Als u een virtueel netwerk apparaat (NVA) hebt geïmplementeerd om de stroom van het netwerk verkeer te beheren, moet u rekening houden met het volgende:
    - De standaard systeem route van Azure voor Azure VM-replicatie is 0.0.0.0/0.
    - In NVA-implementaties wordt meestal ook een standaard route (0.0.0.0/0) gedefinieerd waarmee uitgaand Internet verkeer wordt afgedwongen door de NVA. De standaard route wordt gebruikt wanneer er geen andere specifieke route configuratie kan worden gevonden.
    - Als dit het geval is, kan de NVA overbelast zijn als alle replicatie verkeer via de NVA wordt door gegeven.
    - Deze beperking geldt ook wanneer standaard routes worden gebruikt voor het routeren van alle Azure VM-verkeer naar on-premises implementaties.
    - In dit scenario wordt u aangeraden [een netwerk service-eind punt](azure-to-azure-about-networking.md#create-network-service-endpoint-for-storage) in uw virtuele netwerk te maken voor de service micro soft. Storage, zodat het replicatie verkeer Azure-grens niet verlaat.

## <a name="replication-example"></a>Voor beeld van replicatie

Bedrijfs implementaties hebben doorgaans werk belastingen gesplitst over meerdere Azure-VNets, met een centrale connectiviteit hub voor externe verbinding met internet en op on-premises sites. Een hub-en-spoke-topologie wordt doorgaans gebruikt in combi natie met ExpressRoute.

![On-premises-naar-Azure met ExpressRoute vóór een failover](./media/azure-vm-disaster-recovery-with-expressroute/site-recovery-with-expressroute-before-failover.png)

- **Regio**. Apps worden geïmplementeerd in de Azure Azië-oost-regio.
- **Spoke vNets**. Apps worden geïmplementeerd in twee spoke-vNets:
    - **Bron vNet1**: 10.1.0.0/24.
    - **Bron vNet2**: 10.2.0.0/24.
    - Elk spoke-virtueel netwerk is verbonden met de **hub vNet**.
- **Hub-vNet**. Er is een hub vNet- **bron-hub vnet**: 10.10.10.0/24.
  - Dit hub vNet fungeert als gate keeper.
  - Alle communicaties tussen subnetten passeren deze hub.
    - **Hub vNet-subnetten**. De hub vNet heeft twee subnetten:
    - **NVA-subnet**: 10.10.10.0/25. Dit subnet bevat een NVA (10.10.10.10).
    - **Gateway-subnet**: 10.10.10.128/25. Dit subnet bevat een ExpressRoute-gateway die is verbonden met een ExpressRoute-verbinding die naar de on-premises site verzendt via een particulier routerings domein.
- Het on-premises Data Center heeft een ExpressRoute-circuit verbinding via een partner Edge in Hong Kong.
- Alle route ring wordt geregeld via Azure route Tables (UDR).
- Al het uitgaande verkeer tussen vNets of het on-premises Data Center wordt doorgestuurd via de NVA.

### <a name="hub-and-spoke-peering-settings"></a>Instellingen voor hub en spoke-peering

#### <a name="spoke-to-hub"></a>Spoke naar hub

**Richting** | **Instelling** | **Status**
--- | --- | ---
Spoke naar hub | Virtueel netwerk adres toestaan | Ingeschakeld
Spoke naar hub | Doorgestuurd verkeer toestaan | Ingeschakeld
Spoke naar hub | Gateway doorvoer toestaan | Uitgeschakeld
Spoke naar hub | Gateways verwijderen gebruiken | Ingeschakeld

 ![Configuratie van spoke to hub-peering](./media/azure-vm-disaster-recovery-with-expressroute/spoke-to-hub-peering-configuration.png)

#### <a name="hub-to-spoke"></a>Hub naar spoke

**Richting** | **Instelling** | **Status**
--- | --- | ---
Hub naar spoke | Virtueel netwerk adres toestaan | Ingeschakeld
Hub naar spoke | Doorgestuurd verkeer toestaan | Ingeschakeld
Hub naar spoke | Gateway doorvoer toestaan | Ingeschakeld
Hub naar spoke | Gateways verwijderen gebruiken | Uitgeschakeld

 ![Configuratie van hub naar spoke-peering](./media/azure-vm-disaster-recovery-with-expressroute/hub-to-spoke-peering-configuration.png)

### <a name="example-steps"></a>Voorbeeld stappen

In ons voor beeld moet het volgende gebeuren bij het inschakelen van replicatie voor virtuele Azure-machines in het bron netwerk:

1. U [schakelt replicatie in](azure-to-azure-tutorial-enable-replication.md) voor een virtuele machine.
2. Site Recovery maakt replica-vNets, subnetten en gateway-subnetten in de doel regio.
3. Site Recovery maakt toewijzingen tussen de bron netwerken en de replica doel netwerken die worden gemaakt.
4. U kunt hand matig virtuele netwerk gateways, virtuele netwerk gateway verbindingen, peering van virtuele netwerken of andere netwerk bronnen of-verbindingen maken.


## <a name="fail-over-azure-vms-when-using-expressroute"></a>Failover van virtuele Azure-machines bij gebruik van ExpressRoute

Nadat u virtuele Azure-machines hebt uitgevoerd in de Azure-doel regio met behulp van Site Recovery, kunt u deze openen met ExpressRoute- [persoonlijke peering](../expressroute/expressroute-circuit-peerings.md#privatepeering).

- U moet verbinding maken met ExpressRoute met de doel-vNet met een nieuwe verbinding. De bestaande ExpressRoute-verbinding wordt niet automatisch overgedragen.
- De manier waarop u de ExpressRoute-verbinding met het doel-vNet instelt, is afhankelijk van uw ExpressRoute-topologie.


### <a name="access-with-two-circuits"></a>Toegang met twee circuits

#### <a name="two-circuits-with-two-peering-locations"></a>Twee circuits met twee peering-locaties

Deze configuratie helpt ExpressRoute-circuits te beschermen tegen regionale nood gevallen. Als uw primaire peering-locatie uitvalt, kunnen verbindingen van de andere locatie worden voortgezet.

- Het circuit dat is verbonden met de productie omgeving, is meestal de primaire. Het secundaire circuit heeft doorgaans een lagere band breedte, wat kan worden verhoogd als er sprake is van een nood geval.
- Na een failover kunt u verbindingen tot stand brengen tussen het secundaire ExpressRoute-circuit en het doel-vNet. U kunt ook in het geval van een ramp verbindingen instellen en gereed maken, om de totale herstel tijd te verminderen.
- Met gelijktijdige verbindingen met zowel de primaire als de doel-vNets, moet u ervoor zorgen dat uw on-premises route ring alleen gebruikmaakt van het secundaire circuit en de verbinding na een failover.
- De bron-en doel-vNets kunnen nieuwe IP-adressen ontvangen of dezelfde gebruiken na een failover. In beide gevallen kunnen de secundaire verbindingen tot stand worden gebracht voordat de failover wordt uitgevoerd.


#### <a name="two-circuits-with-single-peering-location"></a>Twee circuits met één peering-locatie

Deze configuratie helpt bij het opsporen van fouten in het primaire ExpressRoute-circuit, maar niet als de peering-locatie met één ExpressRoute uitvalt, wat van invloed is op beide circuits.

- U kunt gelijktijdige verbindingen van het on-premises Data Center hebben tot het bron-vNEt met het primaire circuit en naar het doel-vNet met het secundaire circuit.
- Zorg ervoor dat de on-premises route ring alleen gebruikmaakt van het secundaire circuit en de verbinding na een failover met gelijktijdige verbindingen met het primaire en het doel.
-   U kunt niet beide circuits verbinden met hetzelfde vNet wanneer circuits op dezelfde peering-locatie worden gemaakt.

### <a name="access-with-a-single-circuit"></a>Toegang met één circuit

In deze configuratie is er slechts één Expressroute-circuit. Hoewel het circuit een redundante verbinding heeft voor het geval één wordt afgesloten, biedt één route-Circuit geen flexibiliteit als uw peering-regio uitvalt. Opmerking:

- U kunt virtuele Azure-machines repliceren naar een Azure-regio op [dezelfde geografische locatie](azure-to-azure-support-matrix.md#region-support). Als de Azure-doel regio zich niet op dezelfde locatie bevindt als de bron, moet u ExpressRoute Premium inschakelen als u één ExpressRoute-circuit gebruikt. Meer informatie over [ExpressRoute-locaties](../expressroute/expressroute-locations.md) en ExpressRoute- [prijzen](https://azure.microsoft.com/pricing/details/expressroute/).
- U kunt geen bron-en doel-vNets tegelijkertijd verbinden met het circuit als dezelfde IP-adres ruimte wordt gebruikt voor de doel regio. In dit scenario geldt het volgende:    
    -  Verbreek de verbinding met de bron zijde en stel vervolgens de verbinding met de doel zijde in. Deze verbindings wijziging kan worden vastgelegd als onderdeel van een Site Recovery herstel plan. Opmerking:
        - Als de primaire regio niet toegankelijk is, kan de verbrekings bewerking mislukken als er een regionale fout optreedt. Dit kan invloed hebben op het maken van een verbinding met de doel regio.
        - Als u de verbinding in de doel regio hebt gemaakt en de primaire regio later wordt hersteld, kunt u het pakket ondervinden als twee gelijktijdige verbindingen proberen verbinding te maken met dezelfde adres ruimte.
        - Om dit te voor komen, moet u de primaire verbinding direct beëindigen.
        - Nadat de VM is gefailback naar de primaire regio, kan de primaire verbinding opnieuw tot stand worden gebracht, nadat u de verbinding met de secundaire verbindingen hebt verbroken.
-   Als er een andere adres ruimte wordt gebruikt op het doel-vNet, kunt u gelijktijdig verbinding maken met de bron-en doel-vNets vanuit hetzelfde ExpressRoute-circuit.


## <a name="failover-example"></a>Voor beeld van failover

In ons voor beeld gebruiken we de volgende topologie:

- Twee verschillende ExpressRoute-circuits op twee verschillende peering-locaties.
- Bewaar privé IP-adressen voor de virtuele Azure-machines na een failover.
- De doel herstel regio is Azure Zuidoost-Azië.
- Een secundaire ExpressRoute-circuit verbinding wordt tot stand gebracht via een partner Edge in Singapore.

[Raadpleeg dit artikel](site-recovery-retain-ip-azure-vm-failover.md#hybrid-resources-full-failover)voor een eenvoudige topologie die gebruikmaakt van één ExpressRoute-circuit, met hetzelfde IP-adres na een failover.

### <a name="example-steps"></a>Voorbeeld stappen
Als u herstel in dit voor beeld wilt automatiseren, kunt u het volgende doen:

1. Volg de stappen voor het instellen van de replicatie.
2. [Failover van de virtuele Azure-machines](azure-to-azure-tutorial-failover-failback.md), met de volgende aanvullende stappen tijdens of na de failover.

    a. Maak de Azure ExpressRoute-gateway in de doel regio-hub VNet. Dit is nodig om de doel-hub vNet te verbinden met het ExpressRoute-circuit.

    b. Maak de verbinding van de doel-hub vNet naar het doel-ExpressRoute-circuit.

    c. Stel de VNet-peering in tussen de hub-en spoke-virtuele netwerken van de doel regio. De peering-eigenschappen voor de doel regio zijn hetzelfde als die in de bron regio.

    d. Stel de Udr's in het hub-VNet in en de twee spoke-VNets.

    - De eigenschappen van de Udr's van de doel zijde zijn hetzelfde als die op de bron zijde wanneer u dezelfde IP-adressen gebruikt.
    - Met verschillende doel-IP-adressen moet de Udr's dienovereenkomstig worden gewijzigd.


De bovenstaande stappen kunnen worden vastgelegd als onderdeel van een [herstel plan](site-recovery-create-recovery-plans.md). Afhankelijk van de vereisten voor de verbindings-en herstel tijd van de toepassing, kunnen de bovenstaande stappen ook worden voltooid voordat de failover wordt gestart.

#### <a name="after-recovery"></a>Na herstel

Na het herstellen van de Vm's en het volt ooien van de connectiviteit, is de herstel omgeving als volgt.

![On-premises-naar-Azure met ExpressRoute na een failover](./media/azure-vm-disaster-recovery-with-expressroute/site-recovery-with-expressroute-after-failover.png)



## <a name="next-steps"></a>Volgende stappen

Meer informatie over het gebruik van [herstel plannen](site-recovery-create-recovery-plans.md) voor het automatiseren van de failover van de app.
