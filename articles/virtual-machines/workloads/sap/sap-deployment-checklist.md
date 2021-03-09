---
title: Controle lijst voor SAP-werk belasting plannen en implementeren | Microsoft Docs
description: Controle lijst voor het plannen van SAP-werkbelasting implementaties naar Azure en het implementeren van de werk belastingen
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/10/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 51bfd632e854132be27a7b971cf03e9fe5b00138
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102504300"
---
# <a name="sap-workloads-on-azure-planning-and-deployment-checklist"></a>SAP-workloads op Azure: controle lijst voor planning en implementatie

Deze controle lijst is ontworpen voor klanten die SAP NetWeaver-, S/4HANA-en Hybris-toepassingen verplaatsen naar een Azure-infra structuur als een service. Gedurende de loop tijd van het project moet een klant en/of SAP-partner de controle lijst controleren. Het is belang rijk te weten dat veel van de controles aan het begin van het project en tijdens de plannings fase zijn voltooid. Wanneer de implementatie is voltooid, kunnen eenvoudige wijzigingen in de geïmplementeerde Azure-infra structuur of SAP-software versies ingewikkeld worden.

Raadpleeg de controle lijst bij belang rijke mijl palen tijdens uw project. Als u dit doet, kunt u kleine problemen detecteren voordat ze grote problemen worden. U hebt ook voldoende tijd om opnieuw te kunnen samen stellen en alle benodigde wijzigingen te testen. Bekijk deze controle lijst niet. Afhankelijk van uw situatie moet u mogelijk veel meer controles uitvoeren.

De controle lijst bevat geen taken die onafhankelijk zijn van Azure. SAP-toepassings interfaces veranderen bijvoorbeeld tijdens een verplaatsing naar het Azure-platform of een hosting provider.

Deze controle lijst kan ook worden gebruikt voor systemen die al zijn geïmplementeerd. Nieuwe functies, zoals Write Accelerator en Beschikbaarheidszones, en nieuwe VM-typen zijn mogelijk toegevoegd sinds u hebt geïmplementeerd. Het is dus handig om de controle lijst regel matig te bekijken, zodat u op de hoogte bent van de nieuwe functies van het Azure-platform.

## <a name="project-preparation-and-planning-phase"></a>Project voorbereiding en plannings fase
Tijdens deze fase plant u de migratie van uw SAP-werk belasting naar het Azure-platform. Tijdens deze fase moet u ten minste de volgende documenten maken en de volgende elementen van de migratie definiëren en bespreken:

1. Ontwerp document op hoog niveau. Dit document moet het volgende bevatten:
    - De huidige inventaris van SAP-onderdelen en-toepassingen en een inventarisatie van de doel toepassing voor Azure.
    - Een RACI (verantwoordelijkheids toewijzings matrix) die de verantwoordelijkheden en toewijzingen van de betrokken partijen definieert. Begin op een hoog niveau en werk aan meer granulaire niveaus tijdens de planning en de eerste implementaties.
    - Een oplossings architectuur op hoog niveau.
    - Een beslissing over welke Azure-regio's moeten worden geïmplementeerd. Bekijk de [lijst met Azure-regio's](https://azure.microsoft.com/global-infrastructure/regions/). Zie [producten beschikbaar per regio](https://azure.microsoft.com/global-infrastructure/services/)voor meer informatie over welke services beschikbaar zijn in elke regio.
    - Een netwerk architectuur om verbinding te maken tussen on-premises en Azure. Begin met het verkennen [van de blauw druk van Virtual Data Center voor Azure](/azure/architecture/vdc/).
    - Beveiligings principes voor het uitvoeren van belang rijke Bedrijfs gegevens in Azure. Als u meer wilt weten over de beveiliging van gegevens, gaat u naar de [Azure Security-documentatie](../../../security/index.yml).
2.  Technisch ontwerp document. Dit document moet het volgende bevatten:
    - Een blok diagram voor de oplossing.
    - De grootte van compute-, opslag-en netwerk onderdelen in Azure. Zie voor SAP-grootte van virtuele machines van Azure [SAP 
    -  Opmerking #1928533] ( https://launchpad.support.sap.com/#/notes/1928533) .
    - Architectuur voor bedrijfs continuïteit en herstel na nood gevallen.
    - Gedetailleerde informatie over versies van het besturings systeem, DB, kernel en SAP-ondersteunings pakketten. Het is niet nood zakelijk dat elke besturingssysteem versie die wordt ondersteund door SAP NetWeaver of S/4HANA wordt ondersteund op virtuele machines van Azure. Hetzelfde geldt voor de DBMS-releases. Controleer de volgende bronnen om de SAP-releases, DBMS-releases en versies van het besturings systeem bij te werken en zo nodig upgrades voor SAP-en Azure-ondersteuning te garanderen. U moet beschikken over release combinaties die worden ondersteund door SAP en Azure om volledige ondersteuning van SAP en micro soft te krijgen. Indien nodig moet u een upgrade van bepaalde software onderdelen plannen. Meer informatie over ondersteunde SAP-, OS-en DBMS-software wordt hier beschreven:
        - [SAP-ondersteunings opmerking #1928533](https://launchpad.support.sap.com/#/notes/1928533). In deze opmerking worden de minimale OS-Releases gedefinieerd die worden ondersteund op virtuele machines van Azure. Ook worden de mini maal vereiste database releases gedefinieerd voor de meeste niet-HANA-data bases. Ten slotte biedt het de SAP-grootte voor door SAP ondersteunde Azure VM-typen.
        - [SAP-ondersteunings opmerking #2015553](https://launchpad.support.sap.com/#/notes/2015553). Deze opmerking definieert het ondersteunings beleid voor Azure Storage en de ondersteunings relatie die is vereist voor micro soft.
        - [SAP-ondersteunings opmerking #2039619](https://launchpad.support.sap.com/#/notes/2039619). Deze notitie definieert de Oracle-ondersteunings matrix voor Azure. Oracle ondersteunt alleen Windows en Oracle Linux als gast besturingssystemen op Azure voor SAP-workloads. Deze ondersteunings instructie is ook van toepassing op de SAP-toepassingslaag waarop SAP-exemplaren worden uitgevoerd. Oracle biedt echter geen ondersteuning voor hoge Beschik baarheid voor SAP Central-Services in Oracle Linux via pacemaker. Als u maximale Beschik baarheid voor ASCS op Oracle Linux nodig hebt, moet u de SIOS-beveiligings Suite voor Linux gebruiken. Voor gedetailleerde SAP-certificerings gegevens raadpleegt u SAP-ondersteuning opmerking [#1662610: ondersteunings Details voor de SIOS-beveiligings Suite voor Linux](https://launchpad.support.sap.com/#/notes/1662610). Voor Windows wordt de door SAP ondersteunde Windows Server failover clustering-oplossing voor SAP Central Services ondersteund in combi natie met Oracle als de DBMS-laag.
        - [SAP-ondersteunings opmerking #2235581](https://launchpad.support.sap.com/#/notes/2235581). Deze opmerking bevat de ondersteunings matrix voor SAP HANA in verschillende versies van het besturings systeem.
        - Virtuele Azure-machines met SAP HANA-ondersteuning en [Hana grote instanties](./hana-overview-architecture.md) worden weer gegeven op de [SAP-website](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure).
        - [SAP-product beschikbaarheids matrix](https://support.sap.com/en/).
        - [SAP-ondersteuning Opmerking #2555629-SAP HANA 2,0 van dynamische lagen – Hyper Visor-en Cloud ondersteuning](https://launchpad.support.sap.com/#/notes/2555629)
        - [SAP-ondersteuning Opmerking #1662610: ondersteunings Details voor het SIOS-beveiligings pakket voor Linux](https://launchpad.support.sap.com/#/notes/1662610)
        - SAP-notities voor andere SAP-specifieke producten.     
    - Het gebruik van multi-SID cluster configuraties voor SAP Central Services wordt ondersteund op Windows-, SLES-en RHEL-gast besturingssystemen in Azure. Houd er wel van uit dat de krachtige RADIUS de meer ASCS/SCS die u op een multi-SID-cluster plaatst kan verg Roten. Raadpleeg de volgende artikelen voor informatie over het scenario voor het respectieve gast besturingssysteem:
        - [SAP ASCS/SCS instance multi-SID hoge Beschik baarheid met Windows Server Failover Clustering en gedeelde schijf op Azure](./sap-ascs-ha-multi-sid-wsfc-shared-disk.md)
        - [SAP ASCS/SCS instance multi-SID hoge Beschik baarheid met Windows Server Failover Clustering en file share op Azure](./sap-ascs-ha-multi-sid-wsfc-file-share.md)
        - [Hoge Beschik baarheid voor SAP NetWeaver op Azure Vm's op SUSE Linux Enterprise Server voor de multi-SID-hand leiding voor SAP-toepassingen](./high-availability-guide-suse-multi-sid.md)
        - [Hoge Beschik baarheid voor SAP NetWeaver op Azure Vm's op Red Hat Enterprise Linux voor de multi-SID-hand leiding voor SAP-toepassingen](./high-availability-guide-rhel-multi-sid.md)
    - Architectuur met hoge Beschik baarheid en herstel na nood gevallen.
        - Bepaal op basis van RTO en RPO hoe de architectuur voor hoge Beschik baarheid en herstel na nood gevallen moet worden weer geven.
        - Voor maximale Beschik baarheid binnen een zone, controleert u wat de gewenste DBMS in azure moet aanbieden. De meeste DBMS-pakketten bieden synchrone methoden van een synchrone hot stand-by, die we aanraden voor productie systemen. Raadpleeg ook de SAP-gerelateerde documentatie voor verschillende data bases, te beginnen met [overwegingen voor Azure virtual machines DBMS-implementatie voor SAP-werk belastingen](./dbms_guide_general.md) en gerelateerde documenten.
           Het gebruik van Windows Server failover clustering met een gedeelde schijf configuratie voor de DBMS-laag als bijvoorbeeld [beschreven voor SQL Server](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server), wordt niet ondersteund. Gebruik in plaats daarvan oplossingen zoals:
           - [SQL Server Always On](/previous-versions/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-ps-sql-alwayson-availability-groups)
           - [Oracle Data Guard](../oracle/configure-oracle-dataguard.md)
           - [HANA-systeem replicatie](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html)
        - Bekijk voor herstel na nood gevallen tussen Azure-regio's de oplossingen die worden aangeboden door verschillende DBMS-leveranciers. De meeste hiervan ondersteunen asynchrone replicatie of logboek verzending.
        - Bepaal voor de SAP-toepassingslaag of u uw zakelijke regressie test systemen wilt uitvoeren, die in het ideale geval replica's zijn van uw productie-implementaties, in dezelfde Azure-regio of in uw DR-regio. In het tweede geval kunt u het regressie systeem van het bedrijf als het DR-doel voor uw productie-implementaties richten.
        - Als u besluit de niet-productie systemen te plaatsen op de DR-site, kijkt u naar Azure Site Recovery als methode voor het repliceren van de SAP-toepassingslaag naar de Azure DR-regio. Zie voor meer informatie een [herstel na nood geval instellen voor een implementatie van SAP NetWeaver met meerdere lagen](../../../site-recovery/site-recovery-sap.md).
        - Als u besluit een gecombineerde HADR-configuratie te gebruiken met behulp van [Azure-beschikbaarheidszones](../../../availability-zones/az-overview.md), moet u vertrouwd raken met de Azure-regio's waar Beschikbaarheidszones beschikbaar zijn. Houd ook rekening met de beperkingen die kunnen worden geïntroduceerd door extra netwerk latentie tussen twee Beschikbaarheidszones.  
3.  Een inventarisatie van alle SAP-interfaces (SAP en niet-SAP).
4.  Ontwerp van Foundation-Services. Dit ontwerp moet de volgende items bevatten:
    - Active Directory-en DNS-ontwerp.
    - Netwerk topologie binnen Azure en toewijzing van verschillende SAP-systemen.
    - [Azure RBAC-structuur (op rollen gebaseerd toegangs beheer)](../../../role-based-access-control/overview.md) voor teams die infra structuur-en SAP-toepassingen beheren in Azure.
    - Topologie van resource groep.
    - [Strategie voor Tags Toep assen](../../../azure-resource-manager/management/tag-resources.md#tags-and-billing).
    - Naamgevings regels voor Vm's en andere infrastructuur onderdelen en/of logische namen.
5.  Micro soft Professional-of Premier Support-contract. Identificeer uw Technical Account Manager (TAM) van micro soft als u een premier-ondersteunings contract met micro soft hebt. Zie SAP-ondersteunings [opmerking #2015553](https://launchpad.support.sap.com/#/notes/2015553)voor SAP-ondersteunings vereisten.
6.  Het aantal Azure-abonnementen en het kern quotum voor de abonnementen. [Open ondersteunings aanvragen om de quota van Azure-abonnementen naar behoefte te verhogen](../../../azure-portal/supportability/resource-manager-core-quotas-request.md) .
7.  Gegevens reductie en gegevens migratie plan voor het migreren van SAP-gegevens naar Azure. SAP NetWeaver Systems bevat richt lijnen voor het beperken van het volume van grote hoeveel heden gegevens. Zie [deze SAP-hand leiding](https://wiki.scn.sap.com/wiki/download/attachments/247399467/DVM_%20Guide_7.2.pdf?version=1&modificationDate=1549365516000&api=v2) over gegevens beheer in SAP ERP-systemen. Een deel van de inhoud is ook van toepassing op netweave en S/4HANA-systemen in het algemeen.
8.  Een geautomatiseerde implementatie methode. Het doel van de automatisering van infrastructuur implementaties in Azure is om op een deterministische manier te implementeren en de deterministische resultaten te verkrijgen. Veel klanten gebruiken scripts op basis van Power shell of CLI. Er zijn echter verschillende open-source technologieën die u kunt gebruiken om Azure-infra structuur voor SAP te implementeren en zelfs SAP-software te installeren. U kunt voor beelden vinden op GitHub:
    - [Geautomatiseerde SAP-implementaties in azure Cloud](https://github.com/Azure/sap-hana)
    - [Installatie van SAP HANA](https://github.com/AzureCAT-GSI/SAP-HANA-ARM)
9.  Definieer een regel matige ontwerp-en implementatie uitgebracht tussen u als de klant, de systeem integrator, micro soft en andere betrokken partijen.


## <a name="pilot-phase-strongly-recommended"></a>Test fase (sterk aanbevolen)
 
U kunt een pilot uitvoeren vóór of tijdens de planning en voor bereiding van het project. U kunt ook de pilot fase gebruiken om benaderingen en ontwerpen te testen die zijn aangebracht tijdens de plannings-en voorbereidings fase. En u kunt de test fase uitbreiden om het een echt concept te maken.

U wordt aangeraden een volledige HADR-oplossing en beveiligings ontwerp in te stellen en te valideren tijdens een pilot implementatie. Sommige klanten voeren schaal baarheid-tests uit tijdens deze fase. Andere klanten gebruiken implementaties van SAP-sandboxsystemen als een pilot fase. We gaan ervan uit dat u al een systeem hebt geïdentificeerd dat u wilt migreren naar Azure voor de pilot.

1. Optimaliseer de gegevens overdracht naar Azure. De optimale keuze is zeer afhankelijk van het specifieke scenario. Overdracht van on-premises via [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) is snelst als het ExpressRoute-circuit voldoende band breedte heeft. In andere gevallen is het overbrengen via internet sneller.
2. Voor een heterogene SAP-platform migratie waarbij gegevens worden geëxporteerd en geïmporteerd, moet u de export-en import fasen testen en optimaliseren. Voor grote migraties waarin SQL Server het doel platform is, kunt u [aanbevelingen](https://techcommunity.microsoft.com/t5/Running-SAP-Applications-on-the/SAP-OS-DB-Migration-to-SQL-Server-8211-FAQ-v6-2-April-2017/ba-p/368070)vinden. U kunt de migratie monitor/SWPM gebruiken als u geen gecombineerde versie-upgrade nodig hebt. U kunt het [SAP DMO](https://blogs.sap.com/2013/11/29/database-migration-option-dmo-of-sum-introduction/) -proces gebruiken wanneer u de migratie combineert met een SAP-versie-upgrade. Hiervoor moet u voldoen aan bepaalde vereisten voor de combi natie van bron-en doel database platform. Dit proces wordt beschreven in [Data Base Migration Option (DMO) van SUM 2,0 SP03](https://launchpad.support.sap.com/#/notes/2631152).
   1.  Exporteren naar bron, export bestand uploaden naar Azure en prestaties importeren. Maximaliseer overlap ping tussen exporteren en importeren.
   2.  Evalueer het volume van de Data Base op de doel-en doel platformen om de infra structuur te verg Roten.
   3.  Timing valideren en optimaliseren.
1. Technische validatie.
   1. VM-typen.
        - Bekijk de resources in SAP-ondersteunings notities, in de SAP HANA hardware-map en de SAP PAM-module opnieuw. Zorg ervoor dat er geen wijzigingen zijn in ondersteunde Vm's voor Azure, ondersteunde versies van besturings systemen voor deze VM-typen en ondersteunde SAP-en DBMS-releases.
        - Valideer opnieuw de grootte van uw toepassing en de infra structuur die u in azure implementeert. Als u bestaande toepassingen verplaatst, kunt u vaak de benodigde SAP'S afleiden van de infra structuur die u gebruikt en de [SAP-benchmark pagina](https://www.sap.com/dmc/exp/2018-benchmark-directory/#/sd) en deze vergelijken met de sap's-nummers die worden vermeld in de [SAP-ondersteunings Opmerking #1928533](https://launchpad.support.sap.com/#/notes/1928533). Houd [dit artikel ook bij op sap's-classificaties](https://techcommunity.microsoft.com/t5/Running-SAP-Applications-on-the/SAPS-ratings-on-Azure-VMs-8211-where-to-look-and-where-you-can/ba-p/368208) .
        - Evalueer en test de grootte van uw Azure-Vm's met betrekking tot de maximale opslag doorvoer en netwerk doorvoer van de VM-typen die u tijdens de plannings fase hebt gekozen. U kunt de gegevens hier vinden:
           -  [Grootten voor virtuele Windows-machines in azure](../../sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Het is belang rijk dat u rekening houdt met de Maxi maal beschik bare *schijf doorvoer* in de cache voor grootte.
           -  [Grootten voor virtuele Linux-machines in azure](../../sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Het is belang rijk dat u rekening houdt met de Maxi maal beschik bare *schijf doorvoer* in de cache voor grootte.
   2. Opslag.
        - Controleer het document [Azure Storage typen voor SAP-workload](./planning-guide-storage.md)
        - Gebruik ten minste [Azure Standard-SSD Storage](../../disks-types.md#standard-ssd) voor virtuele machines die een SAP-toepassings lagen vertegenwoordigen en voor de implementatie van DBMSs die geen prestatie gevoelig zijn.
        - In het algemeen wordt het gebruik van [Azure Standard-HDD-schijven](../../disks-types.md#standard-hdd)niet aangeraden.
        - Gebruik [Azure Premium Storage](../../disks-types.md#premium-ssd) voor alle DBMS-vm's die op afstand gevoelig zijn.
        - Gebruik [Azure Managed disks](https://azure.microsoft.com/services/managed-disks/).
        - Gebruik Azure Write Accelerator voor DBMS-logboek stations met een M-serie. Houd rekening met Write Accelerator limieten en het gebruik, zoals wordt beschreven in [Write Accelerator](../../how-to-enable-write-accelerator.md).
        - Voor de verschillende DBMS-typen raadpleegt u de [algemene SAP-gerelateerde DBMS-documentatie](./dbms_guide_general.md) en de DBMS-specifieke documentatie waarnaar het algemene document verwijst.
        - Zie [SAP Hana Infrastructure-configuraties en-bewerkingen op Azure](./hana-vm-operations.md)voor meer informatie over SAP Hana.
        - Koppel nooit Azure-gegevens schijven aan een Azure Linux-VM met behulp van de apparaat-ID. Gebruik in plaats daarvan de UUID (Universally Unique Identifier). Wees voorzichtig wanneer u grafische hulpprogram ma's gebruikt om Azure-gegevens schijven te koppelen. Controleer de vermeldingen in bestand/etc/fstab om te controleren of de UUID wordt gebruikt om de schijven te koppelen. In [dit artikel](../../linux/attach-disk-portal.md#connect-to-the-linux-vm-to-mount-the-new-disk)vindt u meer informatie.
   3. Inbel.
        - Test en evalueer uw virtuele netwerk infrastructuur en de distributie van uw SAP-toepassingen over of binnen de verschillende virtuele netwerken van Azure.
        -  Evalueer de architectuur van de hub en het spoke-netwerk of de microsegmentatie aanpak binnen één virtueel Azure-netwerk. Baseer deze evaluatie op:
               1. Kosten van gegevens uitwisseling tussen [gepeerde virtuele netwerken van Azure](../../../virtual-network/virtual-network-peering-overview.md). Zie [Virtual Network prijzen](https://azure.microsoft.com/pricing/details/virtual-network/)voor meer informatie over de kosten.
               2. Voor delen van een snelle ontkoppeling van de peering tussen virtuele Azure-netwerken in plaats van de netwerk beveiligings groep wijzigen om een subnet in een virtueel netwerk te isoleren. Deze evaluatie is voor gevallen waarin toepassingen of Vm's die worden gehost in een subnet van het virtuele netwerk een beveiligings risico vormen.
                3. Centrale logboek registratie en controle van netwerk verkeer tussen on-premises, de buiten wereld en het virtuele Data Center dat u in azure hebt gemaakt.
        - Evalueer en test het gegevenspad tussen de SAP-toepassingslaag en de SAP DBMS-laag.
            -  Plaatsing van [virtuele Azure-netwerk apparaten](https://azure.microsoft.com/solutions/network-appliances/) in het communicatie traject tussen de SAP-toepassing en de DBMS-laag van SAP-systemen op basis van SAP NetWeaver, hybris of S/4HANA wordt niet ondersteund.
            -  Plaatsing van de SAP-toepassingslaag en SAP DBMS in verschillende virtuele netwerken van Azure die niet worden gekoppeld, wordt niet ondersteund.
            -  U kunt [toepassings beveiligings groep en regels voor netwerk beveiligings groepen](../../../virtual-network/network-security-groups-overview.md) gebruiken om routes te definiëren tussen de SAP-toepassingslaag en de SAP-DBMS-laag.
        - Zorg ervoor dat [Azure versneld netwerken](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) is ingeschakeld op de virtuele machines die worden gebruikt in de SAP-toepassingslaag en de SAP-DBMS-laag. Houd er bij dat er verschillende besturingssysteem niveaus nodig zijn voor de ondersteuning van versneld netwerken in Azure:
            - Windows Server 2012 R2 of nieuwer.
            - SUSE Linux 12 SP3 of hoger.
            - RHEL 7,4 of hoger.
            - Oracle Linux 7,5. Als u de RHCKL-kernel gebruikt, is release 3.10.0-862.13.1. EL7 vereist. Als u de Oracle UEK-kernel gebruikt, is release 5 vereist.
        - Test en evalueer de netwerk latentie tussen de virtuele machines van de SAP-toepassingslaag en de DBMS-Vm's op basis van SAP-ondersteunings opmerkingen [#500235](https://launchpad.support.sap.com/#/notes/500235) en [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Controleer de resultaten op basis van de richt lijnen voor netwerk latentie in [SAP-ondersteunings opmerking #1100926](https://launchpad.support.sap.com/#/notes/1100926/E). De netwerk latentie moet in het gemiddelde of het goede bereik liggen. Uitzonde ringen zijn van toepassing op verkeer tussen Vm's en HANA grote instantie-eenheden, zoals beschreven in [dit artikel](./hana-network-architecture.md#networking-architecture-for-hana-large-instance).
        - Zorg ervoor dat ILB-implementaties zijn ingesteld om direct server return te gebruiken. Met deze instelling wordt de latentie verminderd wanneer Azure ILBs wordt gebruikt voor configuraties met hoge Beschik baarheid op de DBMS-laag.
        - Als u Azure Load Balancer samen met Linux-gast besturingssystemen gebruikt, controleert u of de para meter van het Linux-netwerk **net.IPv4.tcp_timestamps** is ingesteld op **0**. Deze aanbeveling is in conflict met aanbevelingen in oudere versies van [SAP note #2382421](https://launchpad.support.sap.com/#/notes/2382421). De SAP-notitie wordt nu bijgewerkt met de status dat deze para meter moet worden ingesteld op **0** om te kunnen werken met Azure load balancers.
        - Overweeg het gebruik van [Azure proximity placement groups](../../co-location.md) om een optimale netwerk latentie te verkrijgen. Zie voor meer informatie [Azure proximity placement groups voor optimale netwerk latentie met SAP-toepassingen](sap-proximity-placement-scenarios.md).
   4. Hoge Beschik baarheid en implementaties voor herstel na nood gevallen.
        - Als u de SAP-toepassingslaag implementeert zonder een specifieke Azure-beschikbaarheids zone te definiëren, moet u ervoor zorgen dat alle virtuele machines waarop SAP-dialoog instanties of middleware-exemplaren van één SAP-systeem worden uitgevoerd, worden geïmplementeerd in een [beschikbaarheidsset](../../availability-set-overview.md).
        - Als u geen hoge Beschik baarheid voor SAP Central-Services en het DBMS nodig hebt, kunt u deze Vm's implementeren in dezelfde beschikbaarheidsset als de SAP-toepassingslaag.
        - Als u SAP Central-Services en de DBMS-laag voor hoge Beschik baarheid beveiligt met behulp van passieve replicatie, plaatst u de twee knoop punten voor SAP Central-Services in één afzonderlijke beschikbaarheidsset en de twee DBMS-knoop punten in een andere beschikbaarheidsset.
        - Als u in Azure-beschikbaarheidszones implementeert, kunt u geen beschikbaarheids sets gebruiken. Maar u moet ervoor zorgen dat u de knoop punten actief en passieve centrale Services implementeert in twee verschillende Beschikbaarheidszones. Gebruik Beschikbaarheidszones die de laagste latentie ertussen hebben.
          Houd er rekening mee dat u [Azure Standard Load Balancer](../../../load-balancer/load-balancer-standard-availability-zones.md) moet gebruiken voor het gebruik van om Windows-of pacemaker-failoverclusters te maken voor de DBMS en SAP Central Services-laag voor alle Beschikbaarheidszones. U kunt geen [basis Load Balancer](../../../load-balancer/load-balancer-overview.md) gebruiken voor zonegebonden-implementaties.
   5. Time-outinstellingen.
        - Controleer de SAP NetWeaver-ontwikkelaars traceringen van de SAP-exemplaren om ervoor te zorgen dat er geen verbindings onderbrekingen tussen de bewerkings-en SAP-processen zijn. U kunt deze verbindings onderbrekingen vermijden door deze twee register parameters in te stellen:
            - HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters\KeepAliveTime = 120000. Zie [KeepAliveTime](/previous-versions/windows/it-pro/windows-2000-server/cc957549(v=technet.10))voor meer informatie.
            - HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters\KeepAliveInterval = 120000. Zie [KeepAliveInterval](/previous-versions/windows/it-pro/windows-2000-server/cc957548(v=technet.10))voor meer informatie.
        - Als u wilt voor komen dat GUI-time-outs tussen on-premises SAP GUI-interfaces en SAP-toepassings lagen in azure zijn geïmplementeerd, controleert u of deze para meters zijn ingesteld in het standaard. PFL-of het exemplaar profiel:
            - rdisp/keepalive_timeout = 3600
            - rdisp/keepalive = 20
        - U moet de para meter **enque/encni/set_so_keepalive** instellen op **True** om te voor komen dat er verbinding wordt gemaakt tussen de SAP-in-en SAP-werk processen. Zie ook [SAP-opmerking #2743751](https://launchpad.support.sap.com/#/notes/2743751).  
        - Als u een configuratie van een Windows-failovercluster gebruikt, moet u ervoor zorgen dat de tijd die moet worden gereageerd op niet-reagerende knoop punten correct is ingesteld voor Azure. In het artikel de [drempel waarden voor het afstemmen van het failover cluster-netwerk](https://techcommunity.microsoft.com/t5/Failover-Clustering/Tuning-Failover-Cluster-Network-Thresholds/ba-p/371834) worden de para meters weer gegeven. Ervan uitgaande dat de cluster knooppunten zich in hetzelfde subnet bevinden, moet u deze para meters wijzigen:
            - SameSubNetDelay = 2000
            - SameSubNetThreshold = 15
            - RoutingHistorylength = 30
    6. Instellingen of patches voor besturings systeem
        - Voor het uitvoeren van HANA op SAP leest u deze opmerkingen en documenties:
            -   [SAP-ondersteunings Opmerking #2814271-SAP HANA back-up mislukt op Azure met controlesom fout](https://launchpad.support.sap.com/#/notes/2814271)
            -   [SAP-ondersteunings Opmerking #2753418: mogelijke prestatie vermindering door timer terugval](https://launchpad.support.sap.com/#/notes/2753418)
            -   [SAP-ondersteunings Opmerking #2791572 prestatie vermindering vanwege ontbrekende VDSO-ondersteuning voor Hyper-V in azure](https://launchpad.support.sap.com/#/notes/2791572)
            -   [SAP-ondersteunings Opmerking #2382421-optimalisatie van de netwerk configuratie op HANA-en OS-niveau](https://launchpad.support.sap.com/#/notes/2382421)
            -   [SAP-ondersteuning: #2694118 Red Hat Enterprise Linux HA Add-On op Azure](https://launchpad.support.sap.com/#/notes/2694118)
            -   [SAP-ondersteuning Opmerking #1984787-SUSE LINUX Enter prise Server 12: installatie notities](https://launchpad.support.sap.com/#/notes/1984787)
            -   [SAP-ondersteunings Opmerking #2002167-Red Hat Enterprise Linux 7. x: installatie en upgrade](https://launchpad.support.sap.com/#/notes/0002002167)
            -   [SAP-ondersteunings Opmerking #2292690-SAP HANA DB: aanbevolen besturingssysteem instellingen voor RHEL 7](https://launchpad.support.sap.com/#/notes/0002292690)
            -   [SAP-ondersteunings Opmerking #2772999-Red Hat Enterprise Linux 8. x: installatie en configuratie](https://launchpad.support.sap.com/#/notes/2772999)
            -   [SAP-ondersteunings Opmerking #2777782-SAP HANA DB: aanbevolen besturingssysteem instellingen voor RHEL 8](https://launchpad.support.sap.com/#/notes/2777782)
            -   [SAP-ondersteunings Opmerking #2578899-SUSE Linux Enterprise Server 15: installatie Opmerking](https://launchpad.support.sap.com/#/notes/2578899)
            -   [SAP-ondersteuning Opmerking #2455582-Linux: SAP-toepassingen uitvoeren die zijn gecompileerd met GCC 6. x](https://launchpad.support.sap.com/#/notes/0002455582)
            -    [SAP-ondersteuning Opmerking #2729475-HWCCT is mislukt met de fout ' Hyper Visor wordt niet ondersteund ' op Azure Vm's gecertificeerd voor SAP HANA](https://launchpad.support.sap.com/#/notes/2729475)
1. Test uw hoge Beschik baarheid en nood herstel procedures.
   1. Failover-situaties simuleren door Vm's (Windows-gast besturingssystemen) af te sluiten of besturings systemen in de modus paniek (Linux-gast besturingssystemen) te zetten. Met deze stap kunt u nagaan of uw failover-configuraties werken zoals is ontworpen.
   1. Meet hoe lang het duurt om een failover uit te voeren. Als de tijden te lang zijn, kunt u het volgende overwegen:
        - Voor SUSE Linux gebruikt u SBD-apparaten in plaats van de Azure Fence-agent om failover te versnellen.
        - Voor SAP HANA, als het opnieuw laden van gegevens te lang duurt, kunt u overwegen om meer opslag bandbreedte in te richten.
   3. Test uw back-up/herstel volgorde en timing en breng zo nodig wijzigingen aan. Zorg ervoor dat de back-uptijden voldoende zijn. U moet ook de herstel-en tijd herstel activiteiten testen. Zorg ervoor dat de herstel tijden binnen uw RTO-Sla's vallen, waar uw RTO afhankelijk is van een Data Base-of VM-herstel proces.
   4. De DR-functionaliteit en-architectuur van meerdere regio's testen.
1. Beveiligings controles.
   1. Test de geldigheid van uw Azure RBAC-architectuur (op rollen gebaseerd toegangs beheer). Het doel is om de toegang en machtigingen van verschillende teams te scheiden en te beperken. SAP-team leden moeten bijvoorbeeld Vm's kunnen implementeren en schijven toewijzen van Azure Storage in een bepaald Azure Virtual Network. Het SAP-basis team mag echter geen eigen virtuele netwerken maken of de instellingen van bestaande virtuele netwerken wijzigen. Leden van het netwerk team mogen geen Vm's implementeren in virtuele netwerken waarin SAP-toepassingen en DBMS-Vm's worden uitgevoerd. Ook kunnen leden van dit team kenmerken van Vm's wijzigen of zelfs Vm's of schijven verwijderen.  
   1.  Controleer of de [netwerk beveiligings groep en de ASC](../../../virtual-network/network-security-groups-overview.md) -regels naar behoren werken en de beveiligde resources afschermen.
   1.  Zorg ervoor dat alle resources die moeten worden versleuteld, zijn versleuteld. Definieer en implementeer processen voor het maken van back-ups van certificaten, opslag en toegang tot die certificaten en herstel de versleutelde entiteiten.
   1.  Gebruik [Azure Disk Encryption](../../../security/fundamentals/azure-disk-encryption-vms-vmss.md) voor besturingssysteem schijven waar mogelijk vanuit een besturings systeem-ondersteunings punt wordt weer gegeven.
   1.  Zorg ervoor dat u niet te veel versleutelings lagen gebruikt. In sommige gevallen is het zinvol om Azure Disk Encryption samen met een van de DBMS-Transparent Data Encryption methoden te gebruiken om verschillende schijven of onderdelen op dezelfde server te beveiligen.  Op een SAP-DBMS-server kan bijvoorbeeld de Azure Disk Encryption (ADE) worden ingeschakeld op de opstart schijf van het besturings systeem (als het besturings systeem ADE ondersteunt) en die gegevens schijven die niet worden gebruikt door de bestanden met DBMS-gegevens persistentie.  Een voor beeld hiervan is het gebruik van ADE op de schijf met de DBMS TDE-versleutelings sleutels.
1. Prestatie testen. In SAP voert u de volgende vergelijkingen uit op basis van SAP-tracering en-metingen:
   - Indien van toepassing, kunt u de 10 beste online rapporten vergelijken met uw huidige implementatie.
   - Indien van toepassing, kunt u de eerste 10 batch-taken vergelijken met uw huidige implementatie.
   - Vergelijkt gegevens overdracht via interfaces naar het SAP-systeem. Focus op interfaces waar u weet dat de overdracht plaatsvindt tussen verschillende locaties, zoals van on-premises naar Azure.


## <a name="non-production-phase"></a>Niet-productie fase 
In deze fase gaan we ervan uit dat u, na een geslaagde pilot of proef van het concept (haalbaarheids test), u begint met het implementeren van niet-productie SAP-systemen naar Azure. Integreer alles wat u hebt geleerd en ervaring hebt tijdens de haalbaarheids test voor deze implementatie. Alle criteria en stappen die worden vermeld voor POCs, zijn ook van toepassing op deze implementatie.

Tijdens deze fase implementeert u doorgaans Ontwikkel systemen, eenheids test systemen en zakelijke regressie test systemen naar Azure. Het is raadzaam dat ten minste één niet-productie systeem in één SAP-toepassings lijn de volledige configuratie van hoge Beschik baarheid heeft die het toekomstige productie systeem zal hebben. Hier volgen enkele extra stappen die u moet uitvoeren tijdens deze fase:  

1.  Voordat u systemen van het oude platform naar Azure verplaatst, verzamelt u gegevens over het verbruik van bronnen, zoals CPU-gebruik, opslag doorvoer en IOPS-gegevens. Dit geldt met name voor het verzamelen van gegevens uit de DBMS-laag eenheden, maar u kunt deze ook verzamelen uit de eenheden van de toepassingslaag. Meet ook netwerk-en opslag latentie.
2.  Registreer de tijd patronen voor het beschikbaarheids gebruik van uw systemen. Het doel is te bepalen of niet-productie systemen elke dag en elke dag beschikbaar moeten zijn, en of er niet-productie systemen zijn die kunnen worden afgesloten tijdens bepaalde fasen van een week of maand.
3.  Test en bepaal of u uw eigen installatie kopieën voor het besturings systeem wilt maken voor uw virtuele machines in azure of dat u een installatie kopie wilt gebruiken uit de galerie met gedeelde installatie kopieën van Azure. Als u een installatie kopie uit de galerie met gedeelde afbeeldingen gebruikt, moet u ervoor zorgen dat u een installatie kopie gebruikt die het ondersteunings contract weerspiegelt met de leverancier van uw besturings systeem. Voor sommige leveranciers van het besturings systeem kunt u met de galerie met gedeelde installatie kopieën uw eigen licentie-installatie kopieën meenemen. Voor andere installatie kopieën van besturings systemen is ondersteuning opgenomen in de prijs die door Azure wordt vermeld. Als u besluit om uw eigen installatie kopieën voor het besturings systeem te maken, kunt u de documentatie vinden in deze artikelen:
    -   [Een gegeneraliseerde installatie kopie maken van een Windows-VM die is geïmplementeerd in azure](../../windows/capture-image-resource.md)
    -   [Een gegeneraliseerde installatie kopie maken van een virtuele Linux-machine die is geïmplementeerd in azure](../../linux/capture-image.md)
3.  Als u SUSE-en Red Hat Linux-installatie kopieën uit de galerie met gedeelde afbeeldingen gebruikt, moet u de installatie kopieën voor SAP van de Linux-leveranciers gebruiken in de galerie met gedeelde installatie kopieën.
4.  Zorg ervoor dat u voldoet aan de SAP-ondersteunings vereisten voor micro soft-ondersteunings overeenkomsten. Zie [SAP-ondersteunings opmerking #2015553](https://launchpad.support.sap.com/#/notes/2015553). Zie [vereisten voor onboarding](./hana-onboarding-requirements.md)voor Hana grote instanties.
4.  Zorg ervoor dat de juiste mensen [gepland onderhouds meldingen](https://azure.microsoft.com/blog/a-new-planned-maintenance-experience-for-your-virtual-machines/) ontvangen, zodat u de beste downtime kunt kiezen.
5.  Controleer regel matig op Azure-presentaties op kanalen als [Channel 9](https://channel9.msdn.com/) voor nieuwe functionaliteit die mogelijk van toepassing is op uw implementaties.
6.  Raadpleeg SAP-notities met betrekking tot Azure, zoals de [ondersteunings opmerking #1928533](https://launchpad.support.sap.com/#/notes/1928533), voor nieuwe VM-sku's en nieuw ondersteund besturings systeem en DBMS-releases. Vergelijk de prijzen van nieuwe VM-typen met de oudere VM-typen, zodat u Vm's kunt implementeren met de beste prijs-prestatie verhouding.
7.  Raadpleeg SAP-ondersteunings opmerkingen, de SAP HANA hardware-map en de SAP PAM opnieuw. Zorg ervoor dat er geen wijzigingen zijn in ondersteunde Vm's voor Azure, ondersteunde besturingssysteem versies op deze Vm's en ondersteunde SAP-en DBMS-releases.
8.  Raadpleeg [de SAP-website](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) voor nieuwe met Hana gecertificeerde Sku's in Azure. Vergelijk de prijs van nieuwe Sku's met de prijzen die u voor gebruik hebt gepland. Breng uiteindelijk de benodigde wijzigingen aan voor het gebruik ervan met de beste prijs-prestatie verhouding.
9.  Uw implementatie scripts aanpassen om nieuwe VM-typen te gebruiken en nieuwe Azure-functies te integreren die u wilt gebruiken.
10. Na de implementatie van de-infra structuur test en evalueert u de netwerk latentie tussen de virtuele machines van de SAP-toepassingslaag en de DBMS-Vm's op basis van SAP-ondersteunings opmerkingen [#500235](https://launchpad.support.sap.com/#/notes/500235) en [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Controleer de resultaten op basis van de richt lijnen voor netwerk latentie in [SAP-ondersteunings opmerking #1100926](https://launchpad.support.sap.com/#/notes/1100926/E). De netwerk latentie moet in het gemiddelde of het goede bereik liggen. Uitzonde ringen zijn van toepassing op verkeer tussen Vm's en HANA grote instantie-eenheden, zoals beschreven in [dit artikel](./hana-network-architecture.md#networking-architecture-for-hana-large-instance). Zorg ervoor dat geen van de beperkingen die worden vermeld in [overwegingen voor azure virtual machines DBMS-implementatie voor SAP-workloads](./dbms_guide_general.md#azure-network-considerations) en [SAP Hana infrastructuur configuraties en-bewerkingen op Azure van](./hana-vm-operations.md) toepassing zijn op uw implementatie.
11. Zorg ervoor dat uw virtuele machines worden geïmplementeerd in de juiste [Azure proximity-plaatsings groep](../../co-location.md), zoals beschreven in [Azure-plaatsings groepen voor optimale netwerk latentie met SAP-toepassingen](sap-proximity-placement-scenarios.md).
11. Voer alle andere controles uit die worden vermeld voor de concept fase testen voordat u de werk belasting toepast.
12. Als de werk belasting van toepassing is, noteert u het Resource verbruik van de systemen in Azure. Vergelijk dit verbruik met records van uw oude platform. Pas de VM-grootte van toekomstige implementaties aan als u ziet dat er grote verschillen zijn. Houd er rekening mee dat bij het krimpen, opslag en netwerk bandbreedte van Vm's ook worden gereduceerd.
    - [Grootten voor Windows Virtual Machines in Azure](../../sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
    - [Sizes for Linux virtual machines in Azure](../../sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 
13. Experimenteer met de functionaliteit en processen van systeem kopieën. Het doel is om het eenvoudig te maken om een ontwikkelings systeem of een test systeem te kopiëren, zodat project teams snel nieuwe systemen kunnen verkrijgen. 
14. Optimaliseer en verwerk de op rollen gebaseerde toegang, machtigingen en processen van uw team om ervoor te zorgen dat u een schei ding van taken hebt. Op hetzelfde moment moet u ervoor zorgen dat alle teams hun taken in de Azure-infra structuur kunnen uitvoeren.
15. Ontwikkel, test en documenteer procedures voor hoge Beschik baarheid en herstel na nood gevallen om uw mede werkers in staat te stellen deze taken uit te voeren. Identificeer tekortkomingen en pas nieuwe Azure-functionaliteit aan die u integreert in uw implementaties.


## <a name="production-preparation-phase"></a>Fase productie voorbereiding 
In deze fase verzamelt u wat u hebt ervaren en geleerd tijdens uw implementaties zonder productie en past u deze toe op toekomstige productie-implementaties. U moet ook het werk van de gegevens overdracht voorbereiden tussen de huidige hosting locatie en Azure.

1.  Voltooi de benodigde SAP-release-upgrades van uw productie systemen voordat u overstapt op Azure.
1.  Ga akkoord met de zakelijke eigen aren van functionele en zakelijke tests die moeten worden uitgevoerd na de migratie van het productie systeem.
1.  Zorg ervoor dat deze tests zijn voltooid met de bron systemen in de huidige hosting locatie. Vermijd het uitvoeren van tests voor de eerste keer nadat het systeem is verplaatst naar Azure.
1.  Test het proces van het migreren van productie systemen naar Azure. Als u gedurende hetzelfde tijds bestek niet alle productie systemen naar Azure verplaatst, bouwt u groepen productie systemen op die zich op dezelfde hosting locatie moeten bevinden. Test gegevens migratie. Hier volgen enkele veelvoorkomende methoden:
    - Gebruik DBMS-methoden zoals back-up maken/herstellen in combi natie met SQL Server always on, HANA System Replication of log shipping to Seed and Synchronize data base content in Azure.
    - Back-up/herstel gebruiken voor kleinere data bases.
    - Gebruik SAP-migratie monitor, die is geïntegreerd in SAP SWPM, om heterogene migraties uit te voeren.
    - Gebruik het [SAP DMO](https://blogs.sap.com/2013/11/29/database-migration-option-dmo-of-sum-introduction/) -proces als u uw migratie wilt combi neren met een SAP-versie-upgrade. Denk eraan dat niet alle combi Naties van bron-DBMS en doel-DBMS worden ondersteund. U vindt meer informatie in de specifieke SAP-ondersteunings opmerkingen voor de verschillende versies van DMO. Bijvoorbeeld, [Data Base Migration Option (DMO) van SUM 2,0 SP04](https://launchpad.support.sap.com/#/notes/2644872).
    - Testen of de door Voer van gegevens overdracht beter is via internet of via ExpressRoute, voor het geval u back-ups of SAP-export bestanden moet verplaatsen. Als u gegevens verplaatst via internet, moet u mogelijk een aantal van de regels voor de beveiligings groep van uw netwerk groep/toepassing wijzigen die u nodig hebt voor toekomstige productie systemen.
1.  Verzamel gegevens over resource verbruik voordat u systemen van uw oude platform naar Azure verplaatst. Nuttige gegevens zijn onder andere CPU-gebruik, opslag doorvoer en IOPS-gegevens. Dit geldt met name voor het verzamelen van gegevens uit de DBMS-laag eenheden, maar u kunt deze ook verzamelen uit de eenheden van de toepassingslaag. Meet ook netwerk-en opslag latentie.
1.  Raadpleeg SAP-ondersteunings opmerkingen en de vereiste instellingen voor het besturings systeem, de SAP HANA hardware-map en de SAP PAM. Zorg ervoor dat er geen wijzigingen zijn in ondersteunde Vm's voor Azure, ondersteunde besturingssysteem versies in deze Vm's en ondersteunde SAP-en DBMS-releases.
1.  Update implementatie scripts om rekening te houden met de meest recente beslissingen die u hebt genomen voor VM-typen en Azure-functionaliteit.
1.  Nadat u de infra structuur en toepassingen hebt geïmplementeerd, controleert u of:
    - De juiste VM-typen zijn geïmplementeerd, met de juiste kenmerken en opslag grootten.
    - De virtuele machines bevinden zich op de juiste en gewenste versies van het besturings systeem en patches en zijn uniform.
    - Vm's worden zo nodig en op een uniforme manier gehard.
    - De juiste toepassings releases en patches zijn geïnstalleerd en geïmplementeerd.
    - De virtuele machines zijn in azure-beschikbaarheids sets geïmplementeerd zoals gepland.
    - Azure Premium Storage wordt gebruikt voor latentie gevoelige schijven of als de [Sla met één virtuele machine van 99,9%](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/) is vereist.
    - Azure Write Accelerator is correct geïmplementeerd.
        - Zorg ervoor dat de virtuele machines, opslag ruimten of stripesets, op de juiste wijze zijn gebouwd op schijven die Write Accelerator hebben.
        - Controleer de [configuratie van de software-RAID op Linux](/previous-versions/azure/virtual-machines/linux/configure-raid).
        - Controleer de [configuratie van LVM op Linux-vm's in azure](/previous-versions/azure/virtual-machines/linux/configure-lvm).
    - [Azure Managed disks](https://azure.microsoft.com/services/managed-disks/) worden uitsluitend gebruikt.
    - Vm's zijn geïmplementeerd in de juiste beschikbaarheids sets en Beschikbaarheidszones.
    - [Versneld netwerken van Azure](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) is ingeschakeld op de virtuele machines die worden gebruikt in de SAP-toepassingslaag en de SAP-DBMS-laag.
    - Er bevinden zich geen [virtuele Azure-netwerk apparaten](https://azure.microsoft.com/solutions/network-appliances/) in het communicatie traject tussen de SAP-toepassing en de DBMS-laag van SAP-systemen op basis van SAP NetWeaver, hybris of S/4HANA.
    - Met de regels voor toepassings beveiligings groepen en netwerk beveiligings groepen kan de communicatie naar wens worden gecommuniceerd en wordt de communicatie, indien nodig, worden geblokkeerd.
    - Time-outinstellingen zijn juist ingesteld, zoals eerder is beschreven.
    - Virtuele machines worden geïmplementeerd in de juiste [Azure proximity-plaatsings groep](../../co-location.md), zoals beschreven in [Azure proximity placement groups voor optimale netwerk latentie met SAP-toepassingen](sap-proximity-placement-scenarios.md).
    - De netwerk latentie tussen de virtuele machines van de SAP-toepassingslaag en de DBMS Vm's wordt getest en gevalideerd zoals beschreven in de SAP-ondersteunings opmerkingen [#500235](https://launchpad.support.sap.com/#/notes/500235) en [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Controleer de resultaten op basis van de richt lijnen voor netwerk latentie in [SAP-ondersteunings opmerking #1100926](https://launchpad.support.sap.com/#/notes/1100926/E). De netwerk latentie moet in het gemiddelde of het goede bereik liggen. Uitzonde ringen zijn van toepassing op verkeer tussen Vm's en HANA grote instantie-eenheden, zoals beschreven in [dit artikel](./hana-network-architecture.md#networking-architecture-for-hana-large-instance).
    - Versleuteling is geïmplementeerd indien nodig en met de juiste versleutelings methode.
    - Interfaces en andere toepassingen kunnen verbinding maken met de zojuist geïmplementeerde infra structuur.
1.  Maak een Playbook voor het reageren op geplande Azure-onderhoud. Bepaal de volg orde waarin systemen en Vm's opnieuw moeten worden opgestart voor gepland onderhoud.
    

## <a name="go-live-phase"></a>Go-Live-fase
Zorg ervoor dat u tijdens de Go-Live-fase de playbooks volgt die u hebt ontwikkeld tijdens eerdere fasen. Voer de stappen uit die u hebt getest en gepraktijkd. Accepteer geen wijzigingen in de laatste minuut in configuraties en processen. Voer ook de volgende stappen uit:

1. Controleer of Azure Portal controle en andere controle hulpprogramma's werken. U kunt het beste Windows prestatie meter (PerfMon) voor Windows en SAR voor Linux.
    - CPU-tellers.
        - Gemiddelde CPU-tijd, totaal (alle Cpu's)
        - Gemiddelde CPU-tijd, elke afzonderlijke processor (128 processors op M128 Vm's)
        - CPU-kerneltijd, elke afzonderlijke processor
        - CPU-gebruikers tijd, elke afzonderlijke processor
    - Geheugen.
        - Vrij geheugen
        - Geheugen pagina in per seconde
        - Geheugen pagina uit per seconde
    - Schijf.
        - Schijf gelezen in KBps, per afzonderlijke schijf
        - Lees-en schrijf bewerkingen per seconde op afzonderlijke schijven
        - Schijf lezen in micro seconden/lezen, per afzonderlijke schijf
        - Schijf schrijven in KBps, per afzonderlijke schijf
        - Schijf schrijf bewerkingen per seconde, op afzonderlijke schijven
        - Schrijf bewerkingen naar de schijf in micro seconden/lezen, per afzonderlijke schijf
    - Netwerk.
        - Netwerk pakketten in/seconde
        - Netwerk pakketten uit per seconde
        - Netwerk KB per seconde
        - Netwerk KB uit/seconde
1.  Voer na de gegevens migratie alle validatie tests uit die u hebt geaccepteerd bij de zakelijke eigen aren. Alleen validatie test resultaten accepteren als er resultaten voor de oorspronkelijke bron systemen zijn.
1.  Controleer of de interfaces functioneren en of andere toepassingen kunnen communiceren met de zojuist geïmplementeerde productie systemen.
1.  Controleer het Trans Port en het correctie systeem via SAP Trans Action STMS.
1.  Database back-ups uitvoeren nadat het systeem is vrijgegeven voor productie.
1.  Voer VM-back-ups uit voor de SAP Application Layer-Vm's nadat het systeem is vrijgegeven voor productie.
1.  Voor SAP-systemen die geen deel uitmaken van de huidige Go-Live-fase, maar die communiceren met de SAP-systemen die u tijdens deze go-live-fase hebt verplaatst naar Azure, moet u de buffer van de hostnaam opnieuw instellen in SM51. Als u dit doet, worden de oude IP-adressen in de cache verwijderd die zijn gekoppeld aan de namen van de exemplaren van de toepassing die u naar Azure hebt verplaatst.  


## <a name="post-production"></a>Na productie
Deze fase is informatie over het bewaken, het beheren van en het beheer van het systeem. Vanuit een SAP-oogpunt zijn de gebruikelijke taken die u nodig had om te volt ooien in uw oude hosting locatie van toepassing. Voer deze specifieke Azure-taken ook uit:

1. Bekijk Azure-facturen voor systemen met hoge beladen.
2. Optimaliseer de prijs-prestatie verhouding van de virtuele machine en de opslag zijde.
3. Optimaliseer de tijden wanneer u systemen kunt uitschakelen.  


## <a name="next-steps"></a>Volgende stappen
Zie deze artikelen:

- [Azure Virtual Machines planning en implementatie voor SAP net-Weaver](./planning-guide.md)
- [Azure Virtual Machines-implementatie voor SAP NetWeaver](./deployment-guide.md)
- [Overwegingen voor de implementatie van Azure Virtual Machines DBMS voor SAP-workloads](./dbms_guide_general.md)