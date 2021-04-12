---
title: 'SAP on Azure: welke SAP-software wordt ondersteund op Azure'
description: Legt uit wat SAP-software wordt ondersteund voor implementatie in azure
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: SAP
ms.assetid: d7c59cc1-b2d0-4d90-9126-628f9c7a5538
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 12/12/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: dfd560754d92c97b435ff7643f6da4d3e62e8cfd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101673534"
---
# <a name="what-sap-software-is-supported-for-azure-deployments"></a>Welke SAP-software wordt ondersteund voor Azure-implementaties
In dit artikel wordt beschreven hoe u kunt zien welke SAP-software wordt ondersteund voor Azure-implementaties en wat de nood zakelijke releases van het besturings systeem of DBMS-releases zijn.

Evalueren of uw huidige SAP-software wordt ondersteund en welk besturings systeem en DBMS-releases worden ondersteund met uw SAP-software in azure, hebt u toegang nodig tot:

- SAP-ondersteunings opmerkingen
- SAP-product beschikbaarheids matrix



## <a name="general-restrictions-for-sap-workload"></a>Algemene beperkingen voor SAP-workload
Azure IaaS-services die voor SAP-workloads kunnen worden gebruikt, zijn beperkt tot x86-64-of x64-hardware. Er zijn geen sparc-of Power CPU-aanbiedingen die van toepassing zijn op SAP-workloads. Klanten die werken met hun toepassingen op besturings systemen die eigendom zijn van hardware architecturen zoals IBM mainframe of AS400, of waar de besturings systemen HP-UX, Solaris of AIX worden gebruikt, moeten hun SAP-toepassingen, waaronder DBMS, wijzigen in een van de volgende besturings systemen:

- Windows Server 64-bits voor het x86-64-platform
- SUSE Linux 64-bits voor het x86-64-platform
- Red Hat Linux 64-bits voor het x86-64-platform
- Oracle Linux 64-bits voor het x86-64-platform

In combi natie met SAP-software worden geen andere versies van het besturings systeem of Linux-distributies ondersteund. De exacte details van specifieke versies en cases worden verderop in het document beschreven.


## <a name="you-start-here"></a>U begint hier
Het uitgangs punt voor u is [SAP-ondersteunings notitie #1928533](https://launchpad.support.sap.com/#/notes/1928533). Wanneer u deze SAP-notitie van boven naar beneden door lopen, worden er verschillende gebieden van ondersteunde software en virtuele machines weer gegeven

De eerste sectie bevat de minimale vereisten voor de besturingssysteem versies die worden ondersteund door SAP-software in azure Vm's in het algemeen. Als u niet voldoet aan deze minimale vereisten en oudere versies van deze besturings systemen uitvoert, moet u de versie van het besturings systeem upgraden naar een dergelijke minimale release of nog een aantal recente releases. Het is juist dat Azure in het algemeen oudere releases van sommige van deze besturings systemen ondersteunt. Maar de vermelde beperkingen of minimum releases zijn gebaseerd op testen en kwalificaties die worden uitgevoerd en die niet meer zullen worden uitgebreid. 


> [!NOTE]
>Er zijn enkele specifieke VM-typen, HANA grote instanties of SAP-workloads die meer recente versies van het besturings systeem zullen vereisen. Gevallen zoals die worden vermeld in het hele document. Dergelijke gevallen worden duidelijk beschreven in SAP Notes of andere SAP-publicaties.

In de volgende sectie worden algemene SAP-platforms vermeld die worden ondersteund met de releases die worden ondersteund en belang rijker zijn dan de SAP-kernels die worden ondersteund. Hier worden netweave/ABAP of Java-stacks weer gegeven die worden ondersteund en die minimale kernel-releases nodig hebben. Er worden meer recente ABAP-stacks ondersteund op Azure, maar er zijn geen minimale kernel-releases vereist omdat wijzigingen voor Azure zijn geïmplementeerd vanaf het begin van de ontwikkeling van de meest recente stacks

U moet het volgende controleren:

- Of de SAP-toepassingen die u uitvoert, worden gedekt door de vermelde minimum releases. Als dat niet het geval is, moet u een nieuwe doel versie definiëren, de SAP-product beschikbaarheids matrix controleren, welke builds van besturings systemen en DBMS-combi Naties worden ondersteund met de nieuwe doel release. Dat betekent dat u de juiste versie van het besturings systeem en de versie van de DBMS kunt kiezen
- Of u uw SAP-kernels wilt bijwerken in een overstap naar Azure
- Of u SAP-ondersteunings pakketten moet bijwerken. Met name ondersteuning voor pakketten die vereist zijn voor gevallen waarin u moet overstappen op een recentere versie van de DBMS


In de volgende sectie vindt u meer informatie over andere SAP-producten en DBMS-releases die worden ondersteund door SAP on Azure voor Windows en Linux. 

> [!NOTE]
> De minimale releases van de verschillende DBMS worden zorgvuldig gekozen en worden mogelijk niet altijd weer gegeven in het hele spectrum van DBMS, waarbij de verschillende DBMS-leveranciers in het algemeen worden ondersteund in Azure. Er zijn veel SAP-werk belastingen in rekening gebracht om deze minimale releases te definiëren. Er zijn geen inspanningen voor het testen en kwalificeren van oudere DBMS-releases. 

> [!NOTE]
> De minimale releases die worden vermeld, zijn oudere versies van besturings systemen en database releases. We raden u ten zeerste aan de meest recente versies van het besturings systeem en de database releases te gebruiken. In veel gevallen hebben meer recente versies van besturings systemen en data bases het gebruik in de open bare Cloud uitgevoerd in overweging genomen en aangepaste code om te optimaliseren voor het uitvoeren in een open bare Cloud of specifiekere Azure

## <a name="oracle-dbms-support"></a>Ondersteuning voor Oracle DBMS
Het besturings systeem, Oracle DBMS-releases en Oracle-functionaliteit die wordt ondersteund in azure, worden specifiek vermeld in de [SAP-ondersteunings opmerking #2039619](https://launchpad.support.sap.com/#/notes/2039619). Deze notitie kan worden samenvatten als:

- De minimale Oracle-versie die wordt ondersteund op Azure-Vm's die zijn gecertificeerd voor netweave, is Oracle 11g release 2 Patchset 3 (11.2.0.4)
- Als gast besturingssystemen worden alleen Windows en Oracle Linux in aanmerking komen. De exacte versies van het besturings systeem en de bijbehorende minimale DBMS-releases worden vermeld in de opmerking
- De ondersteuning van Oracle Linux wordt ook uitgebreid naar de Oracle DBMS-client. Dit betekent dat alle SAP-onderdelen, zoals dialoogvenster instanties van de ABAP of Java stack, ook moeten worden uitgevoerd op Oracle Linux. Alleen SAP-onderdelen in een SAP-systeem die geen verbinding met de Oracle-DBMS zouden maken, mogen een ander Linux-besturings systeem uitvoeren
- Oracle RAC wordt niet ondersteund 
- Oracle ASM wordt voor enkele van de gevallen ondersteund. Details worden weer gegeven in de notitie
- Niet-Unicode SAP-systemen worden alleen ondersteund met toepassings servers die worden uitgevoerd met het Windows-gast besturingssysteem. Het gast besturingssysteem van het DBMS kan worden Oracle Linux of Windows. De reden voor deze beperking is duidelijk wanneer de SAP-product beschikbaarheids matrix (PAM) wordt gecontroleerd. SAP heeft voor Oracle Linux nooit niet-Unicode SAP-kernels vrijgegeven

Als u de DBMS-releases wilt weten die worden ondersteund met de beoogde Azure-infra structuur, moet u de SAP-product beschikbaarheids matrix controleren op basis van het feit of de vereiste besturingssysteem versies en DBMS worden ondersteund met uw SAP-product releases die u wilt uitvoeren. 

## <a name="oracle-linux"></a>Oracle Linux
De meest prominente vraag rond Oracle Linux is of SAP de Red Hat-kernel ondersteunt die ook integraal deel uitmaakt van Oracle Linux. Raadpleeg voor meer informatie [SAP-ondersteuning opmerking #1565179](https://launchpad.support.sap.com/#/notes/1565179).

## <a name="other-database-than-sap-hana"></a>Andere data base dan SAP HANA
Ondersteuning van niet-HANA-data bases voor SAP-workload wordt beschreven in [SAP-ondersteunings opmerking #1928533](https://launchpad.support.sap.com/#/notes/1928533).


## <a name="sap-hana-support"></a>Ondersteuning voor SAP HANA
In azure zijn er twee services die kunnen worden gebruikt om de HANA-data base uit te voeren:

- Azure Virtual Machines
- [HANA grote instanties](./hana-overview-architecture.md)

Voor het uitvoeren van SAP HANA moet SAP meer en sterkere voor waarden hebben om te voldoen aan de infra structuur om netweave of andere SAP-toepassingen en DBMS uit te voeren. Als gevolg hiervan is een kleiner aantal Azure-Vm's in aanmerking voor het uitvoeren van de SAP HANA DBMS. De lijst met ondersteunde Azure-infra structuur die wordt ondersteund voor SAP HANA, vindt u in de [map SAP Hana hardware](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). 

> [!NOTE]
> De eenheden die beginnen met de letter ' zijn [Hana grote instanties](./hana-overview-architecture.md) eenheden. 

> [!NOTE]
> SAP heeft geen specifieke certificering die afhankelijk is van de SAP HANA primaire releases. In tegens telling tot een gemeen schappelijk advies, het scenario voor de kolom **certificering** in de [Hana-gecertificeerde IaaS-platforms](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure), biedt de kolom **geen instructie over de Hana-primaire of secundaire release gecertificeerd**. U moet ervan uitgaan dat alle eenheden vermeld die kunnen worden gebruikt voor HANA 1,0 en HANA 2,0, zolang de gecertificeerde besturingssysteem releases voor de specifieke eenheden ook worden ondersteund door HANA 1,0-releases. 

Voor het gebruik van SAP HANA kunnen verschillende minimale OS-releases van toepassing zijn dan voor de algemene NetWeaver-cases. U moet de ondersteunde besturings systemen voor elke afzonderlijke eenheid afzonderlijk bekijken, aangezien deze kunnen variëren. U doet dit door op elke eenheid te klikken. Meer details worden weer gegeven. Een van de vermelde Details is de verschillende besturings systemen die voor deze specifieke eenheid worden ondersteund.

> [!NOTE]
> Azure HANA grote instantie-eenheden zijn beperkter met ondersteunde besturings systemen vergeleken met Azure-Vm's. Op de andere kant kunnen Azure-Vm's meer recente versies afdwingen als minimale releases. Dit geldt met name voor sommige van de grotere VM-eenheden waarvoor wijzigingen aan Linux-kernels zijn vereist

Als u het ondersteunde besturings systeem voor de Azure-infra structuur hebt, moet u de [SAP-ondersteunings Opmerking controleren #2235581](https://launchpad.support.sap.com/#/notes/2235581) voor de exacte SAP Hana releases en patch niveaus die worden ondersteund door de Azure-eenheden waarvoor u bent bestemd. 

> [!IMPORTANT]
> De stap voor het controleren van de exacte SAP HANA releases en patch niveaus die worden ondersteund, is zeer belang rijk. In veel gevallen is ondersteuning van een bepaalde besturingssysteem versie afhankelijk van een specifiek patch niveau van de uitvoer bare bestanden van SAP HANA.

Aangezien u weet welke specifieke HANA-releases u kunt uitvoeren op de beoogde Azure-infra structuur, moet u de SAP-product beschikbaarheids matrix controleren om na te gaan of er beperkingen zijn met de SAP-product releases die ondersteuning bieden voor de HANA-releases die u hebt gefilterd


## <a name="certified-azure-vms-and-hana-large-instance-units-and-business-transaction-throughput"></a>Gecertificeerde Azure-Vm's en HANA grote instantie-eenheden en door Voer van zakelijke trans acties
Naast het evalueren van ondersteunde besturingssysteem releases, DBMS-releases en afhankelijke support SAP-software releases voor Azure-infrastructuur eenheden, moet u deze eenheden kwalificeren met de door Voer van de zakelijke trans actie, die wordt weer gegeven in de eenheid ' SAP ' door SAP. Alle SAP-groottes zijn afhankelijk van de SAP'S-berekeningen. Door bestaande SAP-systemen te evalueren, kunt u meestal met behulp van uw infrastructuur provider de SAP'S van de eenheden berekenen. Voor de DBMS-laag en voor de toepassingslaag. In andere gevallen waarin nieuwe functionaliteit wordt gemaakt, kan een schaal oefening met SAP de vereiste SAP'S-nummers voor de toepassingslaag en de DBMS-laag onthullen. Aangezien de infrastructuur provider micro soft verplicht is de SAP-doorvoer kenmerken van de verschillende eenheden te verstrekken die netweave en/of HANA-gecertificeerd zijn.

Voor Azure-Vm's worden deze SAP'S-doorvoer nummers gedocumenteerd in de [SAP-ondersteunings opmerking #1928533](https://launchpad.support.sap.com/#/notes/1928533). Voor Azure HANA grote instantie-eenheden worden de SAP'S-doorvoer nummers gedocumenteerd in de [SAP-ondersteunings opmerking #2316233](https://launchpad.support.sap.com/#/notes/2316233)

In de [SAP-ondersteunings opmerking #1928533](https://launchpad.support.sap.com/#/notes/1928533), worden de volgende opmerkingen toegepast:

- **Voor Azure-vm's uit de M-serie en Mv2-Series Azure-vm's zijn verschillende minimale OS-releases van toepassing dan voor andere Azure VM-typen**. De vereiste voor recentere versies van het besturings systeem is gebaseerd op wijzigingen in de versies van het besturings systeem van het bedrijf om hun besturings systemen in te scha kelen die worden uitgevoerd op de specifieke VM-typen van Azure, of om de prestaties en door Voer van SAP-workloads op deze typen VM'S te optimaliseren
- Er zijn twee tabellen die verschillende VM-typen opgeven. De tweede tabel specificeert de SAP'S-door Voer voor Azure VM-typen die alleen ondersteuning bieden voor Azure Standard-opslag. De DBMS-implementatie voor de eenheden die in de tweede tabel van de opmerking zijn opgegeven, wordt niet ondersteund


## <a name="other-sap-products-supported-on-azure"></a>Andere SAP-producten die worden ondersteund op Azure
In het algemeen is de veronderstelling dat met de status van grootschalige-Clouds, zoals Azure, de meeste SAP-software zonder functionele problemen in azure wordt uitgevoerd. In het tegenovergestelde van de visualisatie van de privécloud, biedt SAP nog steeds ondersteuning voor de verschillende SAP-producten voor de verschillende hyerpscale-cloud providers. Als gevolg hiervan zijn er verschillende SAP-ondersteunings opmerkingen die ondersteuning bieden voor Azure voor verschillende SAP-producten. 

Voor het Business Objects BI-platform biedt [SAP-ondersteunings opmerking #2145537](https://launchpad.support.sap.com/#/notes/2145537) een lijst met SAP Business Objects-producten die worden ondersteund op Azure. Als er vragen zijn over onderdelen of combi Naties van software releases en versies van het besturings systeem die niet worden vermeld of ondersteund en die recenter zijn dan de minimale releases vermeld, moet u een SAP-ondersteunings aanvraag openen tegen het onderdeel waarvoor u ondersteuning voor hebt.

Voor professionele objecten Data Services [biedt SAP-ondersteunings opmerking #22288344](https://launchpad.support.sap.com/#/notes/2288344) de minimale ondersteuning van SAP-data services in azure wordt uitgelegd. 

> [!NOTE]
> Zoals aangegeven in de SAP-ondersteunings opmerking, moet u de SAP PAM controleren om het juiste ondersteunings pakket niveau te identificeren dat moet worden ondersteund in azure

SAP Datahub/Vora-ondersteuning in azure Kubernetes Services (AKS) wordt beschreven in de [SAP-ondersteunings opmerking #2464722](https://launchpad.support.sap.com/#/notes/2464722)

Ondersteuning voor SAP BPC 10,1 SP08 wordt beschreven in [SAP-ondersteunings opmerking #2451795](https://launchpad.support.sap.com/#/notes/2451795)

Ondersteuning voor SAP Hybris commerce platform op Azure wordt beschreven in de [Hybris-documentatie](https://help.sap.com/viewer/a74589c3a81a4a95bf51d87258c0ab15/1811/en-US/8c71300f866910149b40c88dfc0de431.html). Vanaf een ondersteund DBMS voor het SAP Hybris-commerce platform, worden er lijsten weer gegeven zoals:

- SQL Server en Oracle op het Windows-besturings systeem. Voor SAP NetWeaver gelden dezelfde minimale releases. Zie [SAP-ondersteunings opmerking #1928533](https://launchpad.support.sap.com/#/notes/1928533) voor meer informatie
- SAP HANA op Red Hat en SUSE Linux. SAP HANA gecertificeerde VM-typen zijn vereist zoals eerder in [dit document](#sap-hana-support)is beschreven. SAP (Hybris) commerce platform wordt beschouwd als OLTP-werk belasting
- SQL Azure DB vanaf SAP (Hybris) commerce platform versie 1811




## <a name="next-steps"></a>Volgende stappen
De volgende stappen in de [Azure virtual machines planning en implementatie van de SAP net-Weaver](./planning-guide.md) lezen
