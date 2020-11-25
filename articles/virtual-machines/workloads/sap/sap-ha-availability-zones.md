---
title: SAP-werkbelasting configuraties met Azure-beschikbaarheidszones | Microsoft Docs
description: Architectuur met hoge Beschik baarheid en scenario's voor SAP NetWeaver met behulp van Azure-beschikbaarheidszones
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 887caaec-02ba-4711-bd4d-204a7d16b32b
ms.service: virtual-machines-windows
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/05/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ad9820cc3227c788582a9c2a452abe1886a4a2d3
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96019324"
---
# <a name="sap-workload-configurations-with-azure-availability-zones"></a>SAP-werkbelastingconfiguraties met Azure-beschikbaarheidszones
[Azure-beschikbaarheidszones](../../../availability-zones/az-overview.md) is een van de functies voor hoge Beschik baarheid die Azure biedt. Met Beschikbaarheidszones verbetert u de algehele Beschik baarheid van SAP-workloads op Azure. Deze functie is al beschikbaar in sommige [Azure-regio's](https://azure.microsoft.com/global-infrastructure/regions/). In de toekomst is het beschikbaar in meer regio's.

In deze afbeelding wordt de basis architectuur van SAP-hoge Beschik baarheid weer gegeven:

![Standaard configuratie voor hoge Beschik baarheid](./media/sap-ha-availability-zones/standard-ha-config.png)

De SAP-toepassingslaag wordt geïmplementeerd in één Azure- [beschikbaarheidsset](../../manage-availability.md). Voor een hoge Beschik baarheid van SAP Central-Services kunt u twee Vm's implementeren in een afzonderlijke beschikbaarheidsset. Gebruik Windows Server failover clustering of pacemaker (Linux) als een Framework met hoge Beschik baarheid met automatische failover in het geval van een infra structuur of software probleem. Zie voor meer informatie over deze implementaties:

- [Een SAP ASCS/SCS-exemplaar op een Windows-failovercluster clusteren met behulp van een gedeelde cluster schijf](./sap-high-availability-guide-wsfc-shared-disk.md)
- [Een SAP ASCS/SCS-exemplaar op een Windows-failovercluster clusteren met behulp van de bestands share](./sap-high-availability-guide-wsfc-file-share.md)
- [Hoge Beschik baarheid voor SAP NetWeaver op Azure Vm's op SUSE Linux Enterprise Server voor SAP-toepassingen](./high-availability-guide-suse.md)
- [Azure Virtual Machines hoge Beschik baarheid voor SAP NetWeaver op Red Hat Enterprise Linux](./high-availability-guide-rhel.md)

Een vergelijk bare architectuur geldt voor de DBMS-laag van SAP NetWeaver, S/4HANA of Hybris-systemen. U implementeert de DBMS-laag in een actieve/passieve modus met een failover cluster-oplossing om te beschermen tegen infra structuur of software storing. De failover-cluster oplossing kan een DBMS-specifiek failovercluster, Windows Server failover clustering of pacemaker zijn.

Als u dezelfde architectuur wilt implementeren met behulp van Azure-beschikbaarheidszones, moet u een aantal wijzigingen aanbrengen in de architectuur die eerder is beschreven. In dit artikel worden deze wijzigingen beschreven.

## <a name="considerations-for-deploying-across-availability-zones"></a>Overwegingen voor de implementatie over Beschikbaarheidszones


Houd rekening met het volgende wanneer u Beschikbaarheidszones gebruikt:

- Er zijn geen garanties met betrekking tot de afstand tussen de verschillende Beschikbaarheidszones binnen een Azure-regio.
- Beschikbaarheidszones zijn geen ideale oplossing voor herstel na nood gevallen. Natuur rampen kunnen leiden tot grote schade in wereld wijde regio's, inclusief zware schade aan energie-infra structuren. De afstanden tussen de verschillende zones zijn mogelijk niet groot genoeg om een goede nood herstel oplossing te vormen.
- De netwerk latentie tussen Beschikbaarheidszones is niet hetzelfde in alle Azure-regio's. In sommige gevallen kunt u de SAP-toepassingsobjectlaag implementeren en uitvoeren in verschillende zones, omdat de netwerk latentie van de ene zone naar de actieve DBMS-VM aanvaardbaar is. Maar in sommige Azure-regio's is de latentie tussen de actieve DBMS-VM en het SAP-toepassings exemplaar, wanneer geïmplementeerd in verschillende zones, mogelijk niet acceptabel voor SAP-bedrijfs processen. In deze gevallen moet de implementatie architectuur verschillen, met een actieve/actieve architectuur voor de toepassing of een actieve/passieve architectuur waarbij de netwerk latentie tussen zones te hoog is.
- Als u wilt bepalen waar Beschikbaarheidszones moet worden gebruikt, moet u uw beslissing baseren op de netwerk latentie tussen de zones. Netwerk latentie speelt een belang rijke rol in twee gebieden:
    - Latentie tussen de twee DBMS-instanties die synchrone replicatie moeten hebben. Hoe hoger de netwerk latentie, des te groter is de kans op schaal baarheid van uw workload.
    - Het verschil in netwerk latentie tussen een virtuele machine waarop een SAP-dialoog venster wordt uitgevoerd in de zone met het actieve DBMS-exemplaar en een vergelijk bare virtuele machine in een andere zone. Naarmate dit verschil toeneemt, wordt de invloed op de uitvoerings tijd van bedrijfs processen en batch taken ook verhoogd, afhankelijk van of ze in-zone worden uitgevoerd met het DBMS of in een andere zone.

Wanneer u Azure-Vm's implementeert in Beschikbaarheidszones en failover-oplossingen in dezelfde Azure-regio instelt, gelden enkele beperkingen:

- U moet [Azure Managed disks](https://azure.microsoft.com/services/managed-disks/) gebruiken wanneer u implementeert in azure-beschikbaarheidszones. 
- De toewijzing van zone-inventarisaties aan de fysieke zones wordt vastgesteld op basis van een Azure-abonnement. Als u verschillende abonnementen gebruikt om uw SAP-systemen te implementeren, moet u de ideale zones voor elk abonnement definiëren.
- U kunt geen Azure-beschikbaarheids sets implementeren binnen een Azure-beschikbaarheids zone, tenzij u [Azure proximity placement Group](../../linux/co-location.md)gebruikt. De manier waarop u de SAP-DBMS-laag en de centrale Services in meerdere zones kunt implementeren en tegelijkertijd de SAP-toepassingslaag implementeert met behulp van beschikbaarheids sets en nog steeds dicht nabij de grenzen van de Vm's wordt beschreven in het artikel [Azure proximity placement groups voor optimale netwerk latentie met SAP-toepassingen](sap-proximity-placement-scenarios.md). Als u geen gebruik maakt van Azure proximity placement groups, moet u een of de andere kiezen als een implementatie raamwerk voor virtuele machines.
- U kunt geen [Azure Basic-Load Balancer](../../../load-balancer/load-balancer-overview.md) gebruiken om failover-cluster oplossingen te maken op basis van Windows Server failover clustering of Linux pacemaker. In plaats daarvan moet u de [Azure Standard Load Balancer-SKU](../../../load-balancer/load-balancer-standard-availability-zones.md)gebruiken.



## <a name="the-ideal-availability-zones-combination"></a>De ideale Beschikbaarheidszones combi natie
Voordat u besluit hoe u Beschikbaarheidszones gebruikt, moet u het volgende bepalen:

- De netwerk latentie tussen de drie zones van een Azure-regio. Hiermee kunt u de zones met de minste netwerk latentie in meerdere zones in het netwerk verkeer kiezen.
- Het verschil tussen VM-naar-VM-latentie binnen een van de zones, van uw gekozen en de netwerk latentie tussen twee zones van uw keuze.
- U kunt bepalen of de VM-typen die u wilt implementeren, beschikbaar zijn in de twee zones die u hebt geselecteerd. Bij sommige Vm's, met name voor virtuele machines uit de M-serie, kunnen er situaties optreden waarin bepaalde Sku's alleen beschikbaar zijn in twee van de drie zones.

## <a name="network-latency-between-and-within-zones"></a>Netwerk latentie tussen en binnen zones
Als u de latentie tussen de verschillende zones wilt bepalen, moet u het volgende doen:

- Implementeer de VM-SKU die u wilt gebruiken voor uw DBMS-exemplaar in alle drie de zones. Zorg ervoor dat [Azure versneld netwerken](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) is ingeschakeld wanneer u deze meting uitvoert.
- Wanneer u de twee zones met de minste netwerk latentie vindt, implementeert u nog een of meer drie Vm's van de VM-SKU die u wilt gebruiken als de VM van de toepassingslaag op de drie Beschikbaarheidszones. Meet de netwerk latentie op basis van de twee DBMS-Vm's in de twee DBMS-zones die u hebt geselecteerd. 
- Gebruik **niping** als meet programma. Dit hulp programma, van SAP, wordt beschreven in SAP-ondersteunings notities [#500235](https://launchpad.support.sap.com/#/notes/500235) en [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Focus op de opdrachten die zijn gedocumenteerd voor latentie metingen. Omdat **ping** niet werkt via de paden van de versnelde netwerk code van Azure, is het niet raadzaam om deze te gebruiken.

U hoeft deze tests niet hand matig uit te voeren. U kunt een [latentie test voor de beschikbaarheids zone](https://github.com/Azure/SAP-on-Azure-Scripts-and-Utilities/tree/master/AvZone-Latency-Test) van Power shell-procedures vinden die de latentie tests automatiseert die worden beschreven. 

Op basis van uw metingen en de beschik baarheid van uw VM-Sku's in de Beschikbaarheidszones, moet u enkele beslissingen nemen:

- Definieer de ideale zones voor de DBMS-laag.
- Bepaal of u uw actieve SAP-toepassingslaag wilt distribueren over één, twee of alle drie zones, op basis van verschillen in de zone netwerk latentie versus tussen zones.
- Bepaal of u een actieve/passieve configuratie of een actieve/actieve configuratie wilt implementeren vanuit een toepassings punt. (Deze configuraties worden verderop in dit artikel beschreven.)

Bij deze beslissingen moet u ook rekening houden met de netwerk latentie aanbevelingen van SAP, zoals beschreven in SAP Note [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E).

> [!IMPORTANT]
> De metingen en beslissingen die u maakt, zijn geldig voor het Azure-abonnement dat u hebt gebruikt toen u de metingen nam. Als u een ander Azure-abonnement gebruikt, moet u de metingen herhalen. De toewijzing van opgesomde zones kan verschillen voor een ander Azure-abonnement.


> [!IMPORTANT]
> Verwacht wordt dat de eerder beschreven metingen verschillende resultaten opleveren in elke Azure-regio die [Beschikbaarheidszones](../../../availability-zones/az-overview.md)ondersteunt. Zelfs als uw vereisten voor netwerk latentie hetzelfde zijn, moet u mogelijk verschillende implementatie strategieën in verschillende Azure-regio's aannemen, omdat de netwerk latentie tussen zones kan verschillen. In sommige Azure-regio's kan de netwerk latentie tussen de drie verschillende zones aanzienlijk verschillen. In andere regio's is de netwerk latentie tussen de drie verschillende zones mogelijk een uniformer. De claim dat er altijd een netwerk latentie tussen 1 en 2 milliseconden is, is onjuist. De netwerk latentie over Beschikbaarheidszones in azure-regio's kan niet worden gegeneraliseerd.

## <a name="activeactive-deployment"></a>Actieve/actieve implementatie
Deze implementatie architectuur heet actief/actief, omdat u uw actieve SAP-toepassings servers in twee of drie zones implementeert. Het SAP Central Services-exemplaar dat gebruik maakt van replicatie, wordt geïmplementeerd tussen twee zones. Hetzelfde geldt voor de DBMS-laag, die wordt geïmplementeerd in dezelfde zones als SAP Central-service.

Wanneer u deze configuratie overweegt, moet u de twee Beschikbaarheidszones in uw regio vinden die de netwerk latentie tussen zones bieden die voor uw werk belasting en uw synchrone DBMS-replicatie acceptabel zijn. U wilt er ook voor zorgen dat de Delta tussen netwerk latentie binnen de zones die u hebt geselecteerd, en dat de netwerk latentie op meerdere zones niet te groot is. Dit komt omdat u geen grote variaties wilt, afhankelijk van of een taak wordt uitgevoerd in de zone met de DBMS-server of in meerdere zones, op het moment dat uw bedrijfs processen of batch taken worden uitgevoerd. Sommige variaties zijn acceptabel, maar geen verschillen.

Een vereenvoudigd schema van een actieve/actieve implementatie in twee zones kan er als volgt uitzien:

![Implementatie van Active/Active zone](./media/sap-ha-availability-zones/active_active_zones_deployment.png)

De volgende overwegingen zijn van toepassing op deze configuratie:

- Als u geen gebruik maakt van [Azure proximity placement Group](../../linux/co-location.md), behandelt u de Azure-beschikbaarheidszones als fout-en update domeinen voor alle virtuele machines, omdat beschikbaarheids sets niet in azure-beschikbaarheidszones kunnen worden geïmplementeerd.
- Als u zonegebonden-implementaties voor de DBMS-laag en de centrale Services wilt combi neren, maar Azure-beschikbaarheids sets voor de toepassingslaag wilt gebruiken, moet u Azure-proximity-groepen gebruiken zoals beschreven in het artikel [Azure proximity placement groups voor optimale netwerk latentie met SAP-toepassingen](sap-proximity-placement-scenarios.md).
- Voor de load balancers van de failover-clusters van SAP Central Services en de DBMS-laag moet u de [standaard-SKU Azure Load Balancer](../../../load-balancer/load-balancer-standard-availability-zones.md)gebruiken. De basis Load Balancer werkt niet in verschillende zones.
- Het virtuele Azure-netwerk dat u hebt geïmplementeerd om het SAP-systeem te hosten, samen met de bijbehorende subnetten, is uitgerekt over zones. U hebt geen afzonderlijke virtuele netwerken nodig voor elke zone.
- Voor alle virtuele machines die u implementeert, moet u [Azure Managed disks](https://azure.microsoft.com/services/managed-disks/)gebruiken. Niet-beheerde schijven worden niet ondersteund voor zonegebonden-implementaties.
- Azure Premium Storage en [Ultra-SSD opslag](../../disks-types.md#ultra-disk) bieden geen ondersteuning voor elk type opslag replicatie tussen zones. De toepassing (DBMS of SAP Central Services) moet belang rijke gegevens repliceren.
- Hetzelfde geldt voor de gedeelde sapmnt-map, een gedeelde schijf (Windows), een CIFS-share (Windows) of een NFS-share (Linux). U moet een technologie gebruiken waarmee deze gedeelde schijven of shares tussen de zones worden gerepliceerd. Deze technologieën worden ondersteund:
  - Voor Windows is een cluster oplossing die gebruikmaakt van SIOS data keeper, zoals beschreven in [cluster a SAP ASCS/SCS instance op een Windows-failovercluster met behulp van een gedeelde cluster schijf in azure](./sap-high-availability-guide-wsfc-shared-disk.md).
  - Voor SUSE Linux is een NFS-share die is gebouwd zoals beschreven in [hoge Beschik baarheid voor NFS op Azure-vm's op SuSE Linux Enterprise Server](./high-availability-guide-suse-nfs.md).
    
    Op dit moment wordt de oplossing die gebruikmaakt van micro soft Scale-Out-Bestands server, zoals beschreven in de [Azure-infra structuur voorbereiden voor SAP hoge Beschik baarheid met behulp van een Windows-failovercluster en een bestands share voor SAP ASCS/SCS-instanties](./sap-high-availability-infrastructure-wsfc-file-share.md), niet ondersteund in meerdere zones.
- De derde zone wordt gebruikt om het SBD-apparaat te hosten voor het geval u een [SuSE Linux pacemaker-cluster](./high-availability-guide-suse-pacemaker.md#create-azure-fence-agent-stonith-device) of extra toepassings exemplaren bouwt.
- Als u de uitvoerings tijd consistent wilt maken voor kritieke bedrijfs processen, kunt u proberen bepaalde batch taken en gebruikers te omleiden naar toepassings exemplaren die in de zone actief zijn met het actieve DBMS-exemplaar met behulp van SAP batch Server-groepen, SAP-aanmeldings groepen of RFC-groepen. In het geval van een zonegebonden-failover moet u deze groepen echter hand matig verplaatsen naar exemplaren die worden uitgevoerd op Vm's die in de zone actief zijn met de actieve DB-VM.  
- In elk van de zones wilt u mogelijk niet-actieve dialoog instanties implementeren. Dit is om direct terug te keren naar de voormalige resource capaciteit als een zone die wordt gebruikt door een deel van uw toepassings exemplaren buiten dienst is.

> [!IMPORTANT]
> In dit Active/Active-scenario worden extra kosten in rekening gebracht voor de band breedte van micro soft, van 04/01/2020 op. Controleer de [prijs informatie](https://azure.microsoft.com/pricing/details/bandwidth/)voor de document bandbreedte. De gegevens overdracht tussen de SAP-toepassingslaag en de SAP DBMS-laag is zeer intensief. Het scenario actief/actief kan daarom bijdragen aan een behoorlijke prijs. Blijf dit artikel controleren om precies te profiteren van de kosten 


## <a name="activepassive-deployment"></a>Actieve/passieve implementatie
Als u geen acceptabel verschil kunt vinden tussen de netwerk latentie binnen één zone en de latentie van netwerk verkeer op meerdere zones, kunt u een architectuur implementeren met een actief/passief teken van het SAP Application Layer-punt. U definieert een *actieve* zone. Dit is de zone waar u de volledige toepassingslaag implementeert en waar u het actieve DBMS en het SAP Central Services-exemplaar probeert uit te voeren. Met een dergelijke configuratie moet u ervoor zorgen dat u geen extreme uitvoerings tijd variaties hebt, afhankelijk van of een taak in de zone wordt uitgevoerd met het actieve DBMS-exemplaar of niet, in zakelijke trans acties en batch taken.

De basis indeling van de architectuur ziet er als volgt uit:

![Implementatie van Active/passieve zone](./media/sap-ha-availability-zones/active_passive_zones_deployment.png)

De volgende overwegingen zijn van toepassing op deze configuratie:

- Beschikbaarheids sets kunnen niet worden geïmplementeerd in Azure-beschikbaarheidszones. Om dat te compenseren, kunt u Azure proximity placement groups gebruiken zoals beschreven in het artikel [Azure proximity placement groups voor optimale netwerk latentie met SAP-toepassingen](sap-proximity-placement-scenarios.md).
- Wanneer u deze architectuur gebruikt, moet u de status nauw keurig bewaken en proberen om de actieve DBMS-en SAP Central Services-instanties in dezelfde zone als uw geïmplementeerde toepassingslaag te houden. In het geval van een failover van de SAP Central-service of het DBMS-exemplaar, wilt u er zeker van zijn dat u hand matig kunt failback in de zone, waarbij de SAP-toepassingslaag zo snel mogelijk wordt geïmplementeerd.
- Voor de load balancers van de failover-clusters van SAP Central Services en de DBMS-laag moet u de [standaard-SKU Azure Load Balancer](../../../load-balancer/load-balancer-standard-availability-zones.md)gebruiken. De basis Load Balancer werkt niet in verschillende zones.
- Het virtuele Azure-netwerk dat u hebt geïmplementeerd om het SAP-systeem te hosten, samen met de bijbehorende subnetten, is uitgerekt over zones. U hebt geen afzonderlijke virtuele netwerken nodig voor elke zone.
- Voor alle virtuele machines die u implementeert, moet u [Azure Managed disks](https://azure.microsoft.com/services/managed-disks/)gebruiken. Niet-beheerde schijven worden niet ondersteund voor zonegebonden-implementaties.
- Azure Premium Storage en [Ultra-SSD opslag](../../disks-types.md#ultra-disk) bieden geen ondersteuning voor elk type opslag replicatie tussen zones. De toepassing (DBMS of SAP Central Services) moet belang rijke gegevens repliceren.
- Hetzelfde geldt voor de gedeelde sapmnt-map, een gedeelde schijf (Windows), een CIFS-share (Windows) of een NFS-share (Linux). U moet een technologie gebruiken waarmee deze gedeelde schijven of shares tussen de zones worden gerepliceerd. Deze technologieën worden ondersteund:
    - Voor Windows is een cluster oplossing die gebruikmaakt van SIOS data keeper, zoals beschreven in [cluster a SAP ASCS/SCS instance op een Windows-failovercluster met behulp van een gedeelde cluster schijf in azure](./sap-high-availability-guide-wsfc-shared-disk.md).
    - Voor SUSE Linux is een NFS-share die is gebouwd zoals beschreven in [hoge Beschik baarheid voor NFS op Azure-vm's op SuSE Linux Enterprise Server](./high-availability-guide-suse-nfs.md).
    
  Op dit moment wordt de oplossing die gebruikmaakt van micro soft Scale-Out-Bestands server, zoals beschreven in de [Azure-infra structuur voorbereiden voor SAP hoge Beschik baarheid met behulp van een Windows-failovercluster en een bestands share voor SAP ASCS/SCS-instanties](./sap-high-availability-infrastructure-wsfc-file-share.md), niet ondersteund in meerdere zones.
- De derde zone wordt gebruikt om het SBD-apparaat te hosten voor het geval u een [SuSE Linux pacemaker-cluster](./high-availability-guide-suse-pacemaker.md#create-azure-fence-agent-stonith-device) of extra toepassings exemplaren bouwt.
- U moet actieve Vm's in de passieve zone implementeren (vanuit een DBMS-punt weergave), zodat u toepassings bronnen kunt starten in het geval van een storing in een zone.
    - [Azure site Recovery](https://azure.microsoft.com/services/site-recovery/) kan momenteel geen actieve vm's repliceren naar actieve vm's tussen zones. 
- U moet investeren in Automation, zodat u, in het geval van een zone storing, automatisch de SAP-toepassingslaag in de tweede zone kunt starten.

## <a name="combined-high-availability-and-disaster-recovery-configuration"></a>Gecombineerde configuratie voor hoge Beschik baarheid en herstel na nood gevallen
Micro soft deelt geen informatie over geografische afstanden tussen de faciliteiten die als host fungeren voor verschillende Azure-beschikbaarheidszones in een Azure-regio. Toch gebruiken sommige klanten zones voor een gecombineerde HA en een DR-configuratie die een Recovery Point Objective (RPO) van nul belooft. Dit betekent dat u geen vastgelegde database transacties moet verliezen, zelfs in het geval van nood herstel. 

> [!NOTE]
> We raden u aan een configuratie zoals dit alleen in bepaalde omstandigheden te gebruiken. U kunt dit bijvoorbeeld gebruiken wanneer gegevens de Azure-regio niet kunnen verlaten voor beveiligings-of nalevings redenen. 

Hier volgt een voor beeld van hoe een dergelijke configuratie eruit kan zien:

![Gecombineerde DR-Beschik baarheid in zones](./media/sap-ha-availability-zones/combined_ha_dr_in_zones.png)

De volgende overwegingen zijn van toepassing op deze configuratie:

- U gaat ervan uit dat er een grote afstand is tussen de faciliteiten die een beschikbaarheids zone hosten of u bent gedwongen binnen een bepaalde Azure-regio te blijven. Beschikbaarheids sets kunnen niet worden geïmplementeerd in Azure-beschikbaarheidszones. Om dat te compenseren, kunt u Azure proximity placement groups gebruiken zoals beschreven in het artikel [Azure proximity placement groups voor optimale netwerk latentie met SAP-toepassingen](sap-proximity-placement-scenarios.md).
- Wanneer u deze architectuur gebruikt, moet u de status nauw keurig bewaken en proberen om de actieve DBMS-en SAP Central Services-instanties in dezelfde zone als uw geïmplementeerde toepassingslaag te houden. In het geval van een failover van de SAP Central-service of het DBMS-exemplaar, wilt u er zeker van zijn dat u hand matig kunt failback in de zone, waarbij de SAP-toepassingslaag zo snel mogelijk wordt geïmplementeerd.
- U moet productie toepassings exemplaren vooraf geïnstalleerd hebben in de virtuele machines waarop de actieve exemplaren van de QA-toepassing worden uitgevoerd.
- Als er een zone storing optreedt, sluit u de exemplaren van de QA-toepassing af en start u in plaats daarvan de productie-exemplaren. Houd er rekening mee dat u virtuele namen moet gebruiken voor de instanties van de toepassing om dit te laten werken.
- Voor de load balancers van de failover-clusters van SAP Central Services en de DBMS-laag moet u de [standaard-SKU Azure Load Balancer](../../../load-balancer/load-balancer-standard-availability-zones.md)gebruiken. De basis Load Balancer werkt niet in verschillende zones.
- Het virtuele Azure-netwerk dat u hebt geïmplementeerd om het SAP-systeem te hosten, samen met de bijbehorende subnetten, is uitgerekt over zones. U hebt geen afzonderlijke virtuele netwerken nodig voor elke zone.
- Voor alle virtuele machines die u implementeert, moet u [Azure Managed disks](https://azure.microsoft.com/services/managed-disks/)gebruiken. Niet-beheerde schijven worden niet ondersteund voor zonegebonden-implementaties.
- Azure Premium Storage en [Ultra-SSD opslag](../../disks-types.md#ultra-disk) bieden geen ondersteuning voor elk type opslag replicatie tussen zones. De toepassing (DBMS of SAP Central Services) moet belang rijke gegevens repliceren.
- Hetzelfde geldt voor de gedeelde sapmnt-map, een gedeelde schijf (Windows), een CIFS-share (Windows) of een NFS-share (Linux). U moet een technologie gebruiken waarmee deze gedeelde schijven of shares tussen de zones worden gerepliceerd. Deze technologieën worden ondersteund:
    - Voor Windows is een cluster oplossing die gebruikmaakt van SIOS data keeper, zoals beschreven in [cluster a SAP ASCS/SCS instance op een Windows-failovercluster met behulp van een gedeelde cluster schijf in azure](./sap-high-availability-guide-wsfc-shared-disk.md).
    - Voor SUSE Linux is een NFS-share die is gebouwd zoals beschreven in [hoge Beschik baarheid voor NFS op Azure-vm's op SuSE Linux Enterprise Server](./high-availability-guide-suse-nfs.md).

  Op dit moment wordt de oplossing die gebruikmaakt van micro soft Scale-Out-Bestands server, zoals beschreven in de [Azure-infra structuur voorbereiden voor SAP hoge Beschik baarheid met behulp van een Windows-failovercluster en een bestands share voor SAP ASCS/SCS-instanties](./sap-high-availability-infrastructure-wsfc-file-share.md), niet ondersteund in meerdere zones.
- De derde zone wordt gebruikt om het SBD-apparaat te hosten voor het geval u een [SuSE Linux pacemaker-cluster](./high-availability-guide-suse-pacemaker.md#create-azure-fence-agent-stonith-device) of extra toepassings exemplaren bouwt.





## <a name="next-steps"></a>Volgende stappen
Hier volgen enkele volgende stappen voor het implementeren van verschillende Azure-beschikbaarheidszones:

- [Een SAP ASCS/SCS-exemplaar op een Windows-failovercluster clusteren met behulp van een gedeelde cluster schijf in azure](./sap-high-availability-guide-wsfc-shared-disk.md)
- [Azure-infra structuur voor SAP-hoge Beschik baarheid voorbereiden met behulp van een Windows-failovercluster en een bestands share voor SAP ASCS/SCS-instanties](./sap-high-availability-infrastructure-wsfc-file-share.md)