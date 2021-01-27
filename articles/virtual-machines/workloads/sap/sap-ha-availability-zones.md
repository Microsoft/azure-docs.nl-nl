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
ms.date: 12/29/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e098256a43add6df026ab136bcd6a6b549c147e7
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98871312"
---
# <a name="sap-workload-configurations-with-azure-availability-zones"></a>SAP-werkbelastingconfiguraties met Azure-beschikbaarheidszones
Naast de implementatie van de verschillende SAP Architecture-lagen in azure-beschikbaarheids sets, kunt u ook de meer recent geïntroduceerde [Azure-beschikbaarheidszones](../../../availability-zones/az-overview.md) gebruiken voor implementaties van SAP-workloads. Een Azure-beschikbaarheids zone wordt gedefinieerd als: "unieke fysieke locaties binnen een regio. Elke zone bestaat uit een of meer data centers die zijn uitgerust met onafhankelijke voeding, koeling en netwerken. Azure-beschikbaarheidszones zijn niet in alle regio's beschikbaar. Voor Azure-regio's die Beschikbaarheidszones bieden, controleert u de [Azure Region-kaart](https://azure.microsoft.com/global-infrastructure/geographies/). In deze kaart wordt weer gegeven welke regio's er Beschikbaarheidszones bieden of aangekondigd zijn. 

Vanaf de typische SAP NetWeaver-of S/4HANA-architectuur moet u drie verschillende lagen beveiligen:

- SAP-toepassingslaag, die een van de dozijn Vm's kan zijn. U wilt de kans op Vm's die worden geïmplementeerd op dezelfde hostserver tot een minimum beperkt. U wilt ook dat deze virtuele machines in een acceptabele nabijheid van de DBMS-laag worden gehouden om netwerk latentie in een aanvaardbaar venster te blijven
- SAP ASCS/SCS-laag die een Single Point of Failure vertegenwoordigt in de architectuur van de SAP NetWeaver en S/4HANA. Normaal gesp roken bekijkt u twee Vm's die u wilt bedekken met een failover-Framework. Daarom moeten deze Vm's worden toegewezen in een andere infrastructuur fout en kunnen er domeinen worden bijgewerkt
- SAP DBMS-laag, die ook een Single Point of Failure vertegenwoordigt. In de gebruikelijke gevallen bestaat het uit twee virtuele machines die worden gedekt door een failover-Framework. Daarom moeten deze Vm's worden toegewezen in een andere infrastructuur fout en kunnen er domeinen worden bijgewerkt. Uitzonde ringen zijn SAP HANA scale-out implementaties waarbij meer dan twee Vm's kunnen worden gebruikt

De belangrijkste verschillen tussen het implementeren van uw essentiële Vm's met behulp van beschikbaarheids sets of Beschikbaarheidszones zijn:

- Implementatie met een beschikbaarheidsset is het uitlijnen van de virtuele machines in de set in één zone of Data Center (wat van toepassing is op de specifieke regio). Als gevolg hiervan is de implementatie via de beschikbaarheidsset niet beveiligd door stroom-, koel-of netwerk problemen die van invloed zijn op de dataceter (en) van de zone als geheel. Aan de plus kant zijn de Vm's uitgelijnd met Update-en fout domeinen in die zone of het Data Center. Met name voor de SAP ASCS-of DBMS-laag waar we twee Vm's per beschikbaarheidsset beveiligen, voor komt de uitlijning met fout-en update domeinen dat beide Vm's eindigen op dezelfde host-hardware 
- Het implementeren van Vm's via een Azure-beschikbaarheidszones en het kiezen van verschillende zones (Maxi maal drie mogelijke tot nu toe), is het implementeren van de virtuele machines op de verschillende fysieke locaties en het toevoegen van extra beveiliging tegen energie, koeling of netwerk problemen die van invloed zijn op de dataceter (en) van de zone als geheel. Wanneer u echter meer dan één virtuele machine van dezelfde VM-familie in dezelfde beschikbaarheids zone implementeert, is er geen bescherming tegen de Vm's die op dezelfde host eindigen. Als gevolg hiervan is de implementatie via Beschikbaarheidszones ideaal voor de SAP-ASCS en de DBMS-laag waar we doorgaans twee Vm's bekijken. Voor de SAP-toepassingslaag die aanzienlijk meer dan twee Vm's kan zijn, moet u mogelijk terugvallen op een ander implementatie model (zie later)

Uw motivatie voor een implementatie over Azure-beschikbaarheidszones moet zijn dat u, naast het uitvallen van een enkele kritieke VM of de mogelijkheid om uitval tijd voor software patches te reduceren binnen een kritiek, u wilt beveiligen tegen grotere infrastructuur problemen die van invloed kunnen zijn op de beschik baarheid van een of meer Azure-data centers. 

## <a name="considerations-for-deploying-across-availability-zones"></a>Overwegingen voor de implementatie over Beschikbaarheidszones


Houd rekening met het volgende wanneer u Beschikbaarheidszones gebruikt:

- Er zijn geen garanties met betrekking tot de afstand tussen de verschillende Beschikbaarheidszones binnen een Azure-regio.
- Beschikbaarheidszones zijn geen ideale oplossing voor herstel na nood gevallen. Natuur rampen kunnen leiden tot grote schade in wereld wijde regio's, inclusief zware schade aan energie-infra structuren. De afstanden tussen de verschillende zones zijn mogelijk niet groot genoeg om een goede nood herstel oplossing te vormen.
- De netwerk latentie tussen Beschikbaarheidszones is niet hetzelfde in alle Azure-regio's. In sommige gevallen kunt u de SAP-toepassingsobjectlaag implementeren en uitvoeren in verschillende zones, omdat de netwerk latentie van de ene zone naar de actieve DBMS-VM aanvaardbaar is. Maar in sommige Azure-regio's is de latentie tussen de actieve DBMS-VM en het SAP-toepassings exemplaar, wanneer geïmplementeerd in verschillende zones, mogelijk niet acceptabel voor SAP-bedrijfs processen. In deze gevallen moet de implementatie architectuur verschillen, met een actieve/actieve architectuur voor de toepassing, of een actieve/passieve architectuur waarbij de netwerk latentie tussen zones te hoog is.
- Als u wilt bepalen waar Beschikbaarheidszones moet worden gebruikt, moet u uw beslissing baseren op de netwerk latentie tussen de zones. Netwerk latentie speelt een belang rijke rol in twee gebieden:
    - Latentie tussen de twee DBMS-instanties die synchrone replicatie moeten hebben. Hoe hoger de netwerk latentie, des te groter is de kans op schaal baarheid van uw workload.
    - Het verschil in netwerk latentie tussen een virtuele machine waarop een SAP-dialoog venster wordt uitgevoerd in de zone met het actieve DBMS-exemplaar en een vergelijk bare virtuele machine in een andere zone. Naarmate dit verschil toeneemt, wordt de invloed op de uitvoerings tijd van bedrijfs processen en batch taken ook verhoogd, afhankelijk van of ze in-zone worden uitgevoerd met het DBMS of in een andere zone.

Wanneer u Azure-Vm's implementeert in Beschikbaarheidszones en failover-oplossingen in dezelfde Azure-regio instelt, gelden enkele beperkingen:

- U moet [Azure Managed disks](https://azure.microsoft.com/services/managed-disks/) gebruiken wanneer u implementeert in azure-beschikbaarheidszones. 
- De toewijzing van zone-inventarisaties aan de fysieke zones wordt vastgesteld op basis van een Azure-abonnement. Als u verschillende abonnementen gebruikt om uw SAP-systemen te implementeren, moet u de ideale zones voor elk abonnement definiëren.
- U kunt geen Azure-beschikbaarheids sets implementeren binnen een Azure-beschikbaarheids zone, tenzij u [Azure proximity placement Group](../../co-location.md)gebruikt. De manier waarop u de SAP-DBMS-laag en de centrale Services in meerdere zones kunt implementeren en tegelijkertijd de SAP-toepassingslaag implementeert met behulp van beschikbaarheids sets en nog steeds dicht nabij de grenzen van de Vm's wordt beschreven in het artikel [Azure proximity placement groups voor optimale netwerk latentie met SAP-toepassingen](sap-proximity-placement-scenarios.md). Als u geen gebruik maakt van Azure proximity placement groups, moet u een of de andere kiezen als een implementatie raamwerk voor virtuele machines.
- U kunt geen [Azure Basic-Load Balancer](../../../load-balancer/load-balancer-overview.md) gebruiken om failover-cluster oplossingen te maken op basis van Windows Server failover clustering of Linux pacemaker. In plaats daarvan moet u de [Azure Standard Load Balancer-SKU](../../../load-balancer/load-balancer-standard-availability-zones.md)gebruiken.



## <a name="the-ideal-availability-zones-combination"></a>De ideale Beschikbaarheidszones combi natie
Als u een SAP NetWeaver of S/4HANA-systeem wilt implementeren in meerdere zones, zijn er twee principe architecturen die u kunt implementeren:

- Actief/actief: het combi neren van Vm's met ASCS/SCS en het aantal VM'S waarop de DBMS-laag wordt uitgevoerd, worden verdeeld over twee zones. Het aantal Vm's waarop de SAP-toepassingslaag wordt uitgevoerd, wordt geïmplementeerd op een even aantal verschillende zones. Als er een failover wordt uitgevoerd voor een DBMS of ASCS/SCS, worden een aantal open en actieve trans acties mogelijk teruggedraaid. Maar gebruikers blijven aangemeld. Het maakt niet uit in welke zones de actieve DBMS-VM en de exemplaren van de toepassing worden uitgevoerd. Deze architectuur is de aanbevolen architectuur voor het implementeren van meerdere zones.
- Actief/passief: het combi neren van Vm's met ASCS/SCS en het aantal VM'S met de DBMS-laag worden verdeeld over twee zones. Het aantal Vm's waarop de SAP-toepassingslaag wordt uitgevoerd, wordt geïmplementeerd in een van de Beschikbaarheidszones. U voert de toepassingslaag uit in dezelfde zone als de actieve ASCS/SCS en het DBMS-exemplaar. U gebruikt deze implementatie architectuur als de netwerk latentie over de verschillende zones te hoog is om de toepassingslaag uit te voeren die over de zones worden gedistribueerd. In plaats daarvan moet de SAP-toepassingslaag worden uitgevoerd in dezelfde zone als de actieve ASCS/SCS en/of het DBMS-exemplaar. Als er een failover wordt uitgevoerd voor een ASCS/SCS-of DBMS-VM naar de secundaire zone, kan dit leiden tot een hogere netwerk latentie en een vermindering van de door voer. En u moet zo snel mogelijk een failback uitvoeren van de eerder failover van de virtuele machine om terug te gaan naar de vorige doorvoer niveaus. Als er een zonegebonden-storing optreedt, moet er een failover naar de secundaire zone worden uitgevoerd voor de toepassingslaag. Een activiteit die gebruikers ervaren als het systeem wordt afgesloten. In sommige Azure-regio's is deze architectuur de enige levensvat bare architectuur wanneer u Beschikbaarheidszones wilt gebruiken. Als u de potentiële impact van een ASCS/SCS-of DBMS-VM'S waarvoor failover wordt uitgevoerd naar de secundaire zone niet kunt accepteren, is het wellicht beter om te blijven werken met implementaties van beschikbaarheids sets


Voordat u besluit om Beschikbaarheidszones te gebruiken, moet u dus het volgende bepalen:

- De netwerk latentie tussen de drie zones van een Azure-regio. Als u de netwerk latentie tussen de zones van een regio weet, kunt u de zones met de minste netwerk latentie in meerdere zone netwerk verkeer kiezen.
- Het verschil tussen VM-naar-VM-latentie binnen een van de zones, van uw gekozen en de netwerk latentie tussen twee zones van uw keuze.
- U kunt bepalen of de VM-typen die u wilt implementeren, beschikbaar zijn in de twee zones die u hebt geselecteerd. Bij sommige Vm's, met name voor virtuele machines uit de M-serie, kunnen er situaties optreden waarin bepaalde Sku's alleen beschikbaar zijn in twee van de drie zones.

## <a name="network-latency-between-and-within-zones"></a>Netwerk latentie tussen en binnen zones
Als u de latentie tussen de verschillende zones wilt bepalen, moet u het volgende doen:

- Implementeer de VM-SKU die u wilt gebruiken voor uw DBMS-exemplaar in alle drie de zones. Zorg ervoor dat [Azure versneld netwerken](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) is ingeschakeld wanneer u deze meting uitvoert.
- Wanneer u de twee zones met de minste netwerk latentie vindt, implementeert u nog een of meer drie Vm's van de VM-SKU die u wilt gebruiken als de VM van de toepassingslaag op de drie Beschikbaarheidszones. Meet de netwerk latentie op basis van de twee DBMS-Vm's in de twee DBMS-zones die u hebt geselecteerd. 
- Gebruik **`niping`** als meet instrument. Dit hulp programma, van SAP, wordt beschreven in SAP-ondersteunings notities [#500235](https://launchpad.support.sap.com/#/notes/500235) en [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Focus op de opdrachten die zijn gedocumenteerd voor latentie metingen. Omdat **ping** niet werkt via de paden van de versnelde netwerk code van Azure, is het niet raadzaam om deze te gebruiken.

U hoeft deze tests niet hand matig uit te voeren. U kunt een [latentie test voor de beschikbaarheids zone](https://github.com/Azure/SAP-on-Azure-Scripts-and-Utilities/tree/master/AvZone-Latency-Test) van Power shell-procedures vinden die de latentie tests automatiseert die worden beschreven. 

Op basis van uw metingen en de beschik baarheid van uw VM-Sku's in de Beschikbaarheidszones, moet u enkele beslissingen nemen:

- Definieer de ideale zones voor de DBMS-laag.
- Bepaal of u uw actieve SAP-toepassingslaag wilt distribueren over één, twee of alle drie zones, op basis van verschillen in de zone netwerk latentie versus tussen zones.
- Bepaal of u een actieve/passieve configuratie of een actieve/actieve configuratie wilt implementeren vanuit een toepassings punt. (Deze configuraties worden verderop in dit artikel beschreven.)

Bij deze beslissingen moet u ook rekening houden met de netwerk latentie aanbevelingen van SAP, zoals beschreven in SAP Note [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E).

> [!IMPORTANT]
> De metingen en beslissingen die u maakt, zijn geldig voor het Azure-abonnement dat u hebt gebruikt toen u de metingen nam. Als u een ander Azure-abonnement gebruikt, moet u de metingen herhalen. De toewijzing van opgesomde zones kan verschillen voor een ander Azure-abonnement.


> [!IMPORTANT]
> Verwacht wordt dat de eerder beschreven metingen verschillende resultaten opleveren in elke Azure-regio die [Beschikbaarheidszones](https://azure.microsoft.com/global-infrastructure/geographies/)ondersteunt. Zelfs als uw vereisten voor netwerk latentie hetzelfde zijn, moet u mogelijk verschillende implementatie strategieën in verschillende Azure-regio's aannemen, omdat de netwerk latentie tussen zones kan verschillen. In sommige Azure-regio's kan de netwerk latentie tussen de drie verschillende zones aanzienlijk verschillen. In andere regio's is de netwerk latentie tussen de drie verschillende zones mogelijk een uniformer. De claim dat er altijd een netwerk latentie tussen 1 en 2 milliseconden is, is onjuist. De netwerk latentie over Beschikbaarheidszones in azure-regio's kan niet worden gegeneraliseerd.

## <a name="activeactive-deployment"></a>Actieve/actieve implementatie
Deze implementatie architectuur heet actief/actief, omdat u uw actieve SAP-toepassings servers in twee of drie zones implementeert. Het SAP Central Services-exemplaar dat gebruik maakt van replicatie, wordt geïmplementeerd tussen twee zones. Hetzelfde geldt voor de DBMS-laag, die wordt geïmplementeerd in dezelfde zones als SAP Central-service. Wanneer u deze configuratie overweegt, moet u de twee Beschikbaarheidszones in uw regio vinden die de netwerk latentie tussen zones bieden die voor uw werk belasting en uw synchrone DBMS-replicatie acceptabel zijn. U wilt er ook voor zorgen dat de Delta tussen netwerk latentie binnen de zones die u hebt geselecteerd, en dat de netwerk latentie op meerdere zones niet te groot is. 

De aard van de SAP-architectuur is dat, tenzij u deze anders configureert, gebruikers en batch taken kunnen worden uitgevoerd in de verschillende exemplaren van de toepassing. De neven werking van dit feit met de actieve/actieve implementatie is dat batch taken kunnen worden uitgevoerd door SAP-toepassings exemplaren die onafhankelijk zijn of ze worden uitgevoerd in dezelfde zone met het actieve DBMS of niet. Als het verschil in netwerk latentie tussen de zones verschilt klein is ten opzichte van netwerk latentie binnen een zone, is het verschil in uitvoerings tijden van batch taken mogelijk niet significant. Hoe groter het verschil van netwerk latentie binnen een zone in vergelijking met het netwerk verkeer van de zone is, de uitvoerings tijd van batch taken kan meer worden beïnvloed als de taak is uitgevoerd in een zone waar het DBMS-exemplaar niet actief is. Het is aan u als klant om te bepalen welke acceptabele verschillen in de uitvoerings tijd zijn. En met de toegestane netwerk latentie voor meerdere zones verkeer.

Azure-regio's waar een dergelijke actieve/actieve implementatie moet mogelijk zijn zonder grote verschillen in uitvoerings tijd en door Voer binnen de toepassingslaag die worden geïmplementeerd in verschillende Beschikbaarheidszones, lijst, zoals:

- West-VS2 (alle drie de zones)
- Oost-VS2 (alle drie de zones)
- VS-centraal (alle drie de zones)
- Europa-noord (alle drie de zones)
- Europa-west (twee van de drie zones)
- VS-Oost (twee van de drie zones)
- VS Zuid-Centraal (twee van de drie zones)
- UK-zuid (twee van de drie zones)

Azure-regio's waarbij deze SAP-implementatie architectuur voor meerdere zones niet wordt aanbevolen:

- Frankrijk - centraal 
- Zuid-Afrika - noord
- Canada - midden
- Japan - oost

Afhankelijk van wat u bereid bent te verdragen voor de uitvoerings tijd, verschillen andere regio's die niet worden vermeld, kunnen ook worden gekwalificeerd.


Een vereenvoudigd schema van een actieve/actieve implementatie in twee zones kan er als volgt uitzien:

![Implementatie van Active/Active zone](./media/sap-ha-availability-zones/active_active_zones_deployment.png)

De volgende overwegingen zijn van toepassing op deze configuratie:

- Als u geen gebruik maakt van [Azure proximity placement Group](../../co-location.md), behandelt u de Azure-beschikbaarheidszones als fout-en update domeinen voor alle virtuele machines, omdat beschikbaarheids sets niet in azure-beschikbaarheidszones kunnen worden geïmplementeerd.
- Als u zonegebonden-implementaties voor de DBMS-laag en de centrale Services wilt combi neren, maar Azure-beschikbaarheids sets voor de toepassingslaag wilt gebruiken, moet u Azure-proximity-groepen gebruiken zoals beschreven in het artikel [Azure proximity placement groups voor optimale netwerk latentie met SAP-toepassingen](sap-proximity-placement-scenarios.md).
- Voor de load balancers van de failover-clusters van SAP Central Services en de DBMS-laag moet u de [standaard-SKU Azure Load Balancer](../../../load-balancer/load-balancer-standard-availability-zones.md)gebruiken. De basis Load Balancer werkt niet in verschillende zones.
- Het virtuele Azure-netwerk dat u hebt geïmplementeerd om het SAP-systeem te hosten, samen met de bijbehorende subnetten, is uitgerekt over zones. U hebt geen afzonderlijke virtuele netwerken nodig voor elke zone.
- Voor alle virtuele machines die u implementeert, moet u [Azure Managed disks](https://azure.microsoft.com/services/managed-disks/)gebruiken. Niet-beheerde schijven worden niet ondersteund voor zonegebonden-implementaties.
- Azure Premium Storage, [Ultra-SSD Storage](../../disks-types.md#ultra-disk)of ANF bieden geen ondersteuning voor elk type opslag replicatie tussen zones. De toepassing (DBMS of SAP Central Services) moet belang rijke gegevens repliceren.
- Hetzelfde geldt voor de gedeelde sapmnt-map, een gedeelde schijf (Windows), een CIFS-share (Windows) of een NFS-share (Linux). U moet een technologie gebruiken waarmee deze gedeelde schijven of shares tussen de zones worden gerepliceerd. Deze technologieën worden ondersteund:
  - Voor Windows is een cluster oplossing die gebruikmaakt van SIOS data keeper, zoals beschreven in [cluster a SAP ASCS/SCS instance op een Windows-failovercluster met behulp van een gedeelde cluster schijf in azure](./sap-high-availability-guide-wsfc-shared-disk.md).
  - Voor SUSE Linux is een NFS-share die is gebouwd zoals beschreven in [hoge Beschik baarheid voor NFS op Azure-vm's op SuSE Linux Enterprise Server](./high-availability-guide-suse-nfs.md).
    
    Op dit moment wordt de oplossing die gebruikmaakt van micro soft Scale-Out-Bestands server, zoals beschreven in de [Azure-infra structuur voorbereiden voor SAP hoge Beschik baarheid met behulp van een Windows-failovercluster en een bestands share voor SAP ASCS/SCS-instanties](./sap-high-availability-infrastructure-wsfc-file-share.md), niet ondersteund in meerdere zones.
- De derde zone wordt gebruikt om het SBD-apparaat te hosten als u een [SuSE Linux pacemaker-cluster](./high-availability-guide-suse-pacemaker.md#create-azure-fence-agent-stonith-device) bouwt en gebruikmaakt van SBD-apparaten in plaats van de Azure omheinings agent. Of voor aanvullende toepassings exemplaren.
- Als u de uitvoerings tijd consistent wilt maken voor kritieke bedrijfs processen, kunt u proberen bepaalde batch taken en gebruikers te omleiden naar toepassings exemplaren die in de zone actief zijn met het actieve DBMS-exemplaar met behulp van SAP batch Server-groepen, SAP-aanmeldings groepen of RFC-groepen. In het geval van een zonegebonden-failover moet u deze groepen echter hand matig verplaatsen naar exemplaren die worden uitgevoerd op Vm's die in de zone actief zijn met de actieve DB-VM.  
- In elk van de zones wilt u mogelijk niet-actieve dialoog instanties implementeren. 

> [!IMPORTANT]
> In dit Active/Active-scenario worden extra kosten in rekening gebracht voor de band breedte van micro soft, van 04/01/2020 op. Controleer de [prijs informatie](https://azure.microsoft.com/pricing/details/bandwidth/)voor de document bandbreedte. De gegevens overdracht tussen de SAP-toepassingslaag en de SAP DBMS-laag is zeer intensief. Het scenario actief/actief kan daarom bijdragen aan een behoorlijke prijs. Blijf dit artikel controleren om precies te profiteren van de kosten 


## <a name="activepassive-deployment"></a>Actieve/passieve implementatie
Als u geen acceptabel verschil kunt vinden tussen de netwerk latentie binnen één zone en de latentie van netwerk verkeer op meerdere zones, kunt u een architectuur implementeren met een actief/passief teken van het SAP Application Layer-punt. U definieert een *actieve* zone. Dit is de zone waar u de volledige toepassingslaag implementeert en waar u het actieve DBMS en het SAP Central Services-exemplaar probeert uit te voeren. Met een dergelijke configuratie moet u ervoor zorgen dat u geen extreme uitvoerings tijd variaties hebt, afhankelijk van of een taak in de zone wordt uitgevoerd met het actieve DBMS-exemplaar of niet, in zakelijke trans acties en batch taken.

Azure-regio's waarbij dit type implementatie architectuur in verschillende zones kan worden voor keur:

- Azië - zuidoost
- Australië - oost
- Brazilië - zuid
- Duitsland - west-centraal
- Zuid-Afrika - noord
- Frankrijk - centraal 
- Canada - midden
- Japan - oost


De basis indeling van de architectuur ziet er als volgt uit:

![Implementatie van Active/passieve zone](./media/sap-ha-availability-zones/active_passive_zones_deployment.png)

De volgende overwegingen zijn van toepassing op deze configuratie:

- Beschikbaarheids sets kunnen niet worden geïmplementeerd in Azure-beschikbaarheidszones. Om dat te compenseren, kunt u Azure proximity placement groups gebruiken zoals beschreven in het artikel [Azure proximity placement groups voor optimale netwerk latentie met SAP-toepassingen](sap-proximity-placement-scenarios.md).
- Wanneer u deze architectuur gebruikt, moet u de status nauw keurig bewaken en proberen om de actieve DBMS-en SAP Central Services-instanties in dezelfde zone als uw geïmplementeerde toepassingslaag te houden. In het geval van een failover van de SAP Central-service of het DBMS-exemplaar, wilt u er zeker van zijn dat u hand matig kunt failback in de zone, waarbij de SAP-toepassingslaag zo snel mogelijk wordt geïmplementeerd.
- Voor de load balancers van de failover-clusters van SAP Central Services en de DBMS-laag moet u de [standaard-SKU Azure Load Balancer](../../../load-balancer/load-balancer-standard-availability-zones.md)gebruiken. De basis Load Balancer werkt niet in verschillende zones.
- Het virtuele Azure-netwerk dat u hebt geïmplementeerd om het SAP-systeem te hosten, samen met de bijbehorende subnetten, is uitgerekt over zones. U hebt geen afzonderlijke virtuele netwerken nodig voor elke zone.
- Voor alle virtuele machines die u implementeert, moet u [Azure Managed disks](https://azure.microsoft.com/services/managed-disks/)gebruiken. Niet-beheerde schijven worden niet ondersteund voor zonegebonden-implementaties.
- Azure Premium Storage, [Ultra-SSD Storage](../../disks-types.md#ultra-disk)of ANF bieden geen ondersteuning voor elk type opslag replicatie tussen zones. De toepassing (DBMS of SAP Central Services) moet belang rijke gegevens repliceren.
- Hetzelfde geldt voor de gedeelde sapmnt-map, een gedeelde schijf (Windows), een CIFS-share (Windows) of een NFS-share (Linux). U moet een technologie gebruiken waarmee deze gedeelde schijven of shares tussen de zones worden gerepliceerd. Deze technologieën worden ondersteund:
    - Voor Windows is een cluster oplossing die gebruikmaakt van SIOS data keeper, zoals beschreven in [cluster a SAP ASCS/SCS instance op een Windows-failovercluster met behulp van een gedeelde cluster schijf in azure](./sap-high-availability-guide-wsfc-shared-disk.md).
    - Voor SUSE Linux is een NFS-share die is gebouwd zoals beschreven in [hoge Beschik baarheid voor NFS op Azure-vm's op SuSE Linux Enterprise Server](./high-availability-guide-suse-nfs.md).
    
  Op dit moment wordt de oplossing die gebruikmaakt van micro soft Scale-Out-Bestands server, zoals beschreven in de [Azure-infra structuur voorbereiden voor SAP hoge Beschik baarheid met behulp van een Windows-failovercluster en een bestands share voor SAP ASCS/SCS-instanties](./sap-high-availability-infrastructure-wsfc-file-share.md), niet ondersteund in meerdere zones.
- De derde zone wordt gebruikt om het SBD-apparaat te hosten als u een [SuSE Linux pacemaker-cluster](./high-availability-guide-suse-pacemaker.md#create-azure-fence-agent-stonith-device) bouwt en gebruikmaakt van SBD-apparaten in plaats van de Azure omheinings agent. Of voor aanvullende toepassings exemplaren.
- U moet actieve Vm's in de passieve zone implementeren (vanuit een DBMS-punt weergave), zodat u toepassings bronnen kunt starten voor het geval van een storing in een zone.
    - [Azure site Recovery](https://azure.microsoft.com/services/site-recovery/) kan momenteel geen actieve vm's repliceren naar actieve vm's tussen zones. 
- U moet investeren in Automation waarmee u de SAP-toepassingslaag automatisch in de tweede zone kunt starten als er een zonegebonden-storing optreedt.

## <a name="combined-high-availability-and-disaster-recovery-configuration"></a>Gecombineerde configuratie voor hoge Beschik baarheid en herstel na nood gevallen
Micro soft deelt geen informatie over geografische afstanden tussen de faciliteiten die als host fungeren voor verschillende Azure-beschikbaarheidszones in een Azure-regio. Toch gebruiken sommige klanten zones voor een gecombineerde HA en een DR-configuratie die een Recovery Point Objective (RPO) van nul belooft. Een RPO van nul betekent dat u geen doorgevoerde database transacties moet verliezen, zelfs in het geval van nood herstel. 

> [!NOTE]
> We raden u aan een configuratie zoals dit alleen in bepaalde omstandigheden te gebruiken. U kunt dit bijvoorbeeld gebruiken wanneer gegevens de Azure-regio niet kunnen verlaten voor beveiligings-of nalevings redenen. 

Hier volgt een voor beeld van hoe een dergelijke configuratie eruit kan zien:

![Gecombineerde DR-Beschik baarheid in zones](./media/sap-ha-availability-zones/combined_ha_dr_in_zones.png)

De volgende overwegingen zijn van toepassing op deze configuratie:

- U gaat ervan uit dat er een grote afstand is tussen de faciliteiten die een beschikbaarheids zone hosten of u bent gedwongen binnen een bepaalde Azure-regio te blijven. Beschikbaarheids sets kunnen niet worden geïmplementeerd in Azure-beschikbaarheidszones. Om dat te compenseren, kunt u Azure proximity placement groups gebruiken zoals beschreven in het artikel [Azure proximity placement groups voor optimale netwerk latentie met SAP-toepassingen](sap-proximity-placement-scenarios.md).
- Wanneer u deze architectuur gebruikt, moet u de status nauw keurig bewaken en proberen om de actieve DBMS-en SAP Central Services-instanties in dezelfde zone als uw geïmplementeerde toepassingslaag te houden. In het geval van een failover van de SAP Central-service of het DBMS-exemplaar, wilt u er zeker van zijn dat u hand matig kunt failback in de zone, waarbij de SAP-toepassingslaag zo snel mogelijk wordt geïmplementeerd.
- U moet productie toepassings exemplaren vooraf geïnstalleerd hebben in de virtuele machines waarop de actieve exemplaren van de QA-toepassing worden uitgevoerd.
- In een zonegebonden-fout Case sluit u de exemplaren van de QA-toepassing af en start u in plaats daarvan de productie-exemplaren. U moet virtuele namen voor de toepassings exemplaren gebruiken om dit werk te doen.
- Voor de load balancers van de failover-clusters van SAP Central Services en de DBMS-laag moet u de [standaard-SKU Azure Load Balancer](../../../load-balancer/load-balancer-standard-availability-zones.md)gebruiken. De basis Load Balancer werkt niet in verschillende zones.
- Het virtuele Azure-netwerk dat u hebt geïmplementeerd om het SAP-systeem te hosten, samen met de bijbehorende subnetten, is uitgerekt over zones. U hebt geen afzonderlijke virtuele netwerken nodig voor elke zone.
- Voor alle virtuele machines die u implementeert, moet u [Azure Managed disks](https://azure.microsoft.com/services/managed-disks/)gebruiken. Niet-beheerde schijven worden niet ondersteund voor zonegebonden-implementaties.
- Azure Premium Storage en [Ultra-SSD opslag](../../disks-types.md#ultra-disk) bieden geen ondersteuning voor elk type opslag replicatie tussen zones. De toepassing (DBMS of SAP Central Services) moet belang rijke gegevens repliceren.
- Hetzelfde geldt voor de gedeelde sapmnt-map, een gedeelde schijf (Windows), een CIFS-share (Windows) of een NFS-share (Linux). U moet een technologie gebruiken waarmee deze gedeelde schijven of shares tussen de zones worden gerepliceerd. Deze technologieën worden ondersteund:
    - Voor Windows is een cluster oplossing die gebruikmaakt van SIOS data keeper, zoals beschreven in [cluster a SAP ASCS/SCS instance op een Windows-failovercluster met behulp van een gedeelde cluster schijf in azure](./sap-high-availability-guide-wsfc-shared-disk.md).
    - Voor SUSE Linux is een NFS-share die is gebouwd zoals beschreven in [hoge Beschik baarheid voor NFS op Azure-vm's op SuSE Linux Enterprise Server](./high-availability-guide-suse-nfs.md).

  Op dit moment wordt de oplossing die gebruikmaakt van micro soft Scale-Out-Bestands server, zoals beschreven in de [Azure-infra structuur voorbereiden voor SAP hoge Beschik baarheid met behulp van een Windows-failovercluster en een bestands share voor SAP ASCS/SCS-instanties](./sap-high-availability-infrastructure-wsfc-file-share.md), niet ondersteund in meerdere zones.
- De derde zone wordt gebruikt om het SBD-apparaat te hosten als u een [SuSE Linux pacemaker-cluster](./high-availability-guide-suse-pacemaker.md#create-azure-fence-agent-stonith-device) bouwt en gebruikmaakt van SBD-apparaten in plaats van de Azure omheinings agent. 





## <a name="next-steps"></a>Volgende stappen
Hier volgen enkele volgende stappen voor het implementeren van verschillende Azure-beschikbaarheidszones:

- [Een SAP ASCS/SCS-exemplaar op een Windows-failovercluster clusteren met behulp van een gedeelde cluster schijf in azure](./sap-high-availability-guide-wsfc-shared-disk.md)
- [Azure-infra structuur voor SAP-hoge Beschik baarheid voorbereiden met behulp van een Windows-failovercluster en een bestands share voor SAP ASCS/SCS-instanties](./sap-high-availability-infrastructure-wsfc-file-share.md)