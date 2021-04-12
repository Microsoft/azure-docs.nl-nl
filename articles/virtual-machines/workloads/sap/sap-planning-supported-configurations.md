---
title: "SAP on Azure: ondersteunde Scenario's met virtuele Azure-machines"
description: Azure Virtual Machines ondersteunde scenario's met SAP-workload
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
ms.date: 03/11/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 274db058f9f291d720fc350bb23f7bfdde2791e9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101670938"
---
# <a name="sap-workload-on-azure-virtual-machine-supported-scenarios"></a>SAP-workload in scenario's met virtuele Azure-machines
Het ontwerpen van de architectuur van SAP NetWeaver, Business One `Hybris` of S/4HANA Systems in azure opent een groot aantal verschillende mogelijkheden voor verschillende architecturen en hulpprogram ma's die u kunt gebruiken om een schaal bare, efficiënte en Maxi maal beschik bare implementatie te verkrijgen. Afhankelijk van het gebruikte besturings systeem of DBMS, zijn er beperkingen. Daarnaast worden niet alle scenario's die on-premises worden ondersteund, op dezelfde manier ondersteund in Azure. In dit document wordt gebruikgemaakt van de ondersteunde configuraties voor niet-hoge Beschik baarheid en configuraties met hoge Beschik baarheid en architecturen met alleen Azure Vm's. Raadpleeg het artikel [ondersteunde scenario's voor Hana grote instanties](./hana-supported-scenario.md)voor scenario's die worden ondersteund met een [grote hoeveelheid Hana-exemplaren](./hana-overview-architecture.md). 


## <a name="2-tier-configuration"></a>configuratie met twee lagen
Een configuratie van een SAP met twee lagen wordt beschouwd als een gecombineerde laag van de SAP-DBMS en de toepassingslaag die worden uitgevoerd op dezelfde server of VM-eenheid. De tweede laag wordt gezien als de laag van de gebruikers interface. In het geval van een configuratie met twee lagen delen de DBMS en SAP Application Layer de resources van de Azure-VM. Als gevolg hiervan moet u de verschillende onderdelen zo configureren dat ze niet concurreren voor resources. U moet er ook voor zorgen dat de resources van de virtuele machine niet worden overschreven. Een dergelijke configuratie biedt geen hoge Beschik baarheid, behalve de [Azure Service Level Agreements](https://azure.microsoft.com/support/legal/sla/) van de verschillende betrokken onderdelen van Azure.

Een grafische weer gave van een dergelijke configuratie kan er als volgt uitzien:

![Eenvoudige configuratie met twee lagen](./media/sap-planning-supported-configurations/two-tier-simple-configuration.png)

Dergelijke configuraties worden ondersteund door Windows, Red Hat, SUSE en Oracle Linux voor de DBMS-systemen van SQL Server, Oracle, Db2, maxDB en SAP ASE voor productie-en niet-productie-cases. Voor SAP HANA als DBMS wordt dit type configuraties alleen ondersteund voor niet-productie doeleinden. Dit omvat ook het implementatie scenario van [Azure Hana grote instanties](./hana-overview-architecture.md) .
Voor alle combi Naties van besturings systemen/DBMS die worden ondersteund in azure, wordt dit type configuratie ondersteund. Het is echter verplicht om de configuratie van het DBMS en de SAP-onderdelen zodanig in te stellen dat DBMS-en SAP-onderdelen niet kunnen concurreren voor geheugen en CPU-resources, waardoor de fysieke beschik bare bronnen worden overschreden. Dit moet worden gedaan door het geheugen te beperken dat het DBMS mag toewijzen. U moet ook het SAP Extended Memory beperken voor toepassings exemplaren. U moet ook het CPU-verbruik van de virtuele machine controleren om ervoor te zorgen dat de onderdelen niet de CPU-Resources maximaliseren. 

> [!NOTE]
> Voor productie-SAP-systemen raden wij u aan extra hoge Beschik baarheid en mogelijke nood herstel configuraties te configureren, zoals verderop in dit document wordt beschreven


## <a name="3-tier-configuration"></a>configuratie met 3 lagen
In dergelijke configuraties scheidt u de SAP-toepassingsobjectlaag en de DBMS-laag in verschillende Vm's. Normaal gesp roken doet u dat voor grotere systemen en is het flexibeler om de resources van de SAP-toepassingslaag flexibel te maken. In de meest eenvoudige configuratie is er geen hoge Beschik baarheid buiten de [Azure Service Level Agreements](https://azure.microsoft.com/support/legal/sla/) van de verschillende onderdelen van Azure betrokken. 

De grafische weer gave ziet er als volgt uit:

![Diagram waarin een eenvoudige configuratie met drie lagen wordt weer gegeven.](./media/sap-planning-supported-configurations/three-tier-simple-configuration.png)

Dit type configuratie wordt ondersteund op Windows, Red Hat, SUSE en Oracle Linux voor de DBMS-systemen van SQL Server, Oracle, Db2, SAP HANA, maxDB en SAP ASE voor productie-en niet-productie-cases. Dit is de standaard implementatie configuratie voor [grote Azure Hana-exemplaren](./hana-overview-architecture.md). Voor vereenvoudiging hebben we geen onderscheid gemaakt tussen SAP Central-Services en SAP-dialoog exemplaren in de SAP-toepassingslaag. In deze eenvoudige configuratie met drie lagen is er geen hoge Beschik baarheid voor SAP Central-Services.

> [!NOTE]
> Voor productie-SAP-systemen raden wij u aan extra hoge Beschik baarheid en mogelijke nood herstel configuraties te configureren, zoals verderop in dit document wordt beschreven


## <a name="multiple-dbms-instances-per-vm-or-hana-large-instance-unit"></a>Meerdere DBMS-instanties per VM of HANA grote instantie-eenheid
In dit configuratie type host u meerdere DBMS-instanties per Azure VM of HANA grote exemplaar-eenheid. De motivatie kan worden beperkt door minder besturings systemen te onderhouden en met die lagere kosten. Andere motivaties kunnen meer flexibiliteit en efficiënter zijn door resources te delen van een grotere virtuele machine of een grote instantie-eenheid van HANA over meerdere DBMS-instanties. Tot nu toe werden deze configuraties voornamelijk voor niet-productie systemen weer gegeven.

Een configuratie zoals die er als volgt uitziet:

![Meerdere DBMS-instanties in één eenheid](./media/sap-planning-supported-configurations/multiple-database-instances.png)

Dit type DBMS-implementatie wordt ondersteund voor:

- SQL Server voor Windows
- IBM Db2. Meer informatie vindt u in het artikel [meerdere instanties (Linux, UNIX)](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.admin.dbobj.doc/doc/c0004904.html)
- Voor Oracle. Raadpleeg voor meer informatie [SAP-ondersteuning opmerking #1778431](https://launchpad.support.sap.com/#/notes/1778431) en gerelateerde SAP-notities
- Voor SAP HANA, meerdere exemplaren op één virtuele machine, SAP aanroept deze implementatie methode MCOS, wordt ondersteund. Zie het SAP-artikel [Multiple SAP HANA Systems op One host (MCOS)] (Engelstalig) (Engelstalig) voor meer informatie (Engelstalig).https://help.sap.com/viewer/eb3777d5495d46c5b2fa773206bbfb46/2.0.02/
- /b2751fd43bec41a9a14e01913f1edf18.html)

Als u meerdere data base-exemplaren op één host uitvoert, moet u ervoor zorgen dat de verschillende exemplaren niet concurreren voor resources en daardoor de fysieke resource limieten van de virtuele machine overschrijden. Dit geldt met name voor geheugen waarbij u het geheugen wilt dat iedereen kan toewijzen aan de instanties die de virtuele machine delen. Dit kan ook waar zijn voor de CPU-resources die de verschillende data base-exemplaren kunnen gebruiken. Alle vermelde DBMS hebben configuraties die het beperken van geheugen toewijzing en CPU-resources op een exemplaar niveau toestaan.
Om ondersteuning te bieden voor een dergelijke configuratie voor virtuele Azure-machines, wordt verwacht dat de schijven of volumes die worden gebruikt voor de gegevens en logboek bestanden van de data base die worden beheerd door de verschillende instanties gescheiden zijn. Of met andere woorden gegevens of logboek bestanden/opnieuw uitvoeren van de data bases die worden beheerd door een ander DBMS-exemplaar, moeten dezelfde schijven of volumes niet worden gedeeld. 

De schijf configuratie voor HANA grote instanties wordt geleverd en wordt gedetailleerd beschreven in [ondersteunde scenario's voor Hana grote instanties](./hana-supported-scenario.md#single-node-mcos). 

> [!NOTE]
> Voor productie-SAP-systemen raden wij u aan extra hoge Beschik baarheid en mogelijke nood herstel configuraties te configureren, zoals verderop in dit document wordt beschreven. Vm's met meerdere DBMS-instanties worden niet ondersteund met de configuraties voor hoge Beschik baarheid, die verderop in dit document worden beschreven.


## <a name="multiple-sap-dialog-instances-in-one-vm"></a>Meerdere SAP-dialoog exemplaren in één VM
In veel gevallen zijn er meerdere dialoogvenster exemplaren geïmplementeerd op bare-metal servers of zelfs in Vm's die in persoonlijke Clouds worden uitgevoerd. De reden hiervoor is om bepaalde SAP-dialoog instanties aan te passen aan bepaalde werk belasting, bedrijfs functionaliteit of typen werk belastingen. De reden voor het niet isoleren van deze instanties in afzonderlijke Vm's was de inspanningen van onderhoud en bewerkingen van het besturings systeem. Of in talrijke gevallen worden de kosten in het geval dat de hoster of de operator van de virtuele machine een maandelijks tarief vraagt per VM die wordt beheerd en beheerd. In azure wordt een scenario voor het hosten van meerdere SAP-dialoog instanties binnen één virtuele machine die wordt ondersteund voor productie-en niet-productie doeleinden op de besturings systemen Windows, Red Hat, SUSE en Oracle Linux. De para meters van de SAP-kernel PHYS_MEMSIZE, die beschikbaar zijn in Windows en moderne Linux-kernels, moeten worden ingesteld als er meerdere SAP-toepassings server exemplaren worden uitgevoerd op één virtuele machine. Het wordt ook aangeraden om de uitbrei ding van SAP Extended Memory op besturings systemen te beperken, zoals Windows, waarbij automatische groei van het uitgebreide SAP-geheugen wordt geïmplementeerd. U kunt dit doen met de para meter SAP-profiel `em/max_size_MB` .

Bij een configuratie met 3 lagen, waar meerdere SAP-dialoog vensters worden uitgevoerd binnen virtuele machines van Azure, kan er als volgt uitzien:

![Diagram waarin een configuratie met drie lagen wordt weer gegeven, waarbij meerdere SAP-dialoog exemplaren worden uitgevoerd in virtuele machines van Azure.](./media/sap-planning-supported-configurations/multiple-dialog-instances.png)

Voor vereenvoudiging hebben we geen onderscheid gemaakt tussen SAP Central-Services en SAP-dialoog exemplaren in de SAP-toepassingslaag. In deze eenvoudige configuratie met drie lagen is er geen hoge Beschik baarheid voor SAP Central-Services. Voor productie systemen is het niet raadzaam SAP Central-Services onbeveiligd te laten. Zie verderop in dit document voor meer informatie over de zogenaamde multi-SID-configuraties rond SAP centraal-instanties en een hoge Beschik baarheid van dergelijke configuraties met meerdere SID'S.

## <a name="high-availability-protection-for-the-sap-dbms-layer"></a>Beveiliging met hoge Beschik baarheid voor de SAP DBMS-laag
Als u een SAP-productie systeem wilt implementeren, moet u overwegen om het hot standby-type van configuraties met hoge Beschik baarheid te gebruiken. Met name bij SAP HANA, waarbij gegevens in het geheugen moeten worden geladen voordat de volledige prestaties en schaal baarheid kan worden verkregen, is Azure service retouchie geen ideale maat eenheid voor hoge Beschik baarheid. 

Algemene micro soft ondersteunt alleen configuraties met hoge Beschik baarheid en software pakketten die worden beschreven in de sectie SAP-workload in docs.microsoft.com. U kunt dezelfde instructie in SAP Note [#1928533](https://launchpad.support.sap.com/#/notes/1928533)lezen. Micro soft biedt geen ondersteuning voor andere niet-micro soft-software kaders van derden die niet in combi natie met SAP-werk belasting worden gedocumenteerd. In dergelijke gevallen is de leverancier van het hoge beschik baarheids raamwerk de ondersteunende partij voor de configuratie met hoge Beschik baarheid die door u moet worden aangestuurd als klant in het ondersteunings proces. In dit artikel worden uitzonde ringen beschreven. 

In het algemeen ondersteunt micro soft een beperkt aantal configuraties met hoge Beschik baarheid op virtuele machines van Azure of HANA grote instanties. Lees voor de ondersteunde scenario's van HANA grote instanties de document [ondersteunde scenario's voor Hana grote instanties](./hana-supported-scenario.md).

Voor virtuele Azure-machines worden de volgende configuraties voor hoge Beschik baarheid ondersteund op DBMS-niveau:

- SAP HANA systeem replicatie op basis van Linux pacemaker op SUSE en Red Hat. Zie de gedetailleerde artikelen:
    - [Hoge Beschik baarheid van SAP HANA op virtuele machines van Azure op SUSE Linux Enterprise Server](./sap-hana-high-availability.md)
    - [Hoge Beschik baarheid van SAP HANA op virtuele machines van Azure op Red Hat Enterprise Linux](./sap-hana-high-availability-rhel.md)
- SAP HANA scale-out n + m configuraties met behulp van [Azure NetApp files](https://azure.microsoft.com/services/netapp/) op SuSE en Red Hat. Meer informatie vindt u in deze artikelen:
    - [Een SAP HANA scale-out systeem met stand-by-knoop punt op virtuele Azure-machines implementeren met behulp van Azure NetApp Files op SUSE Linux Enterprise Server}](./sap-hana-scale-out-standby-netapp-files-suse.md)
    - [Een SAP HANA scale-out systeem met stand-by-knoop punt op virtuele Azure-machines implementeren met behulp van Azure NetApp Files op Red Hat Enterprise Linux](./sap-hana-scale-out-standby-netapp-files-rhel.md)
- SQL Server failovercluster op basis van Windows Scale-Out bestands Services. Een aanbeveling voor productie systemen is het gebruik van SQL Server altijd in plaats van clusteren. SQL Server always on biedt betere Beschik baarheid met afzonderlijke opslag. Details worden in dit artikel beschreven: 
    - [Een SQL Server-failovercluster configureren op virtuele machines van Azure](../../../azure-sql/virtual-machines/windows/failover-cluster-instance-storage-spaces-direct-manually-configure.md)
- SQL Server always on wordt ondersteund met het Windows-besturings systeem voor SQL Server op Azure. Dit is de standaard aanbeveling voor productie-SQL Server exemplaren in Azure. Details worden beschreven in de volgende artikelen:
    - [Introductie van SQL Server AlwaysOn-beschikbaarheids groepen op virtuele machines van Azure](../../../azure-sql/virtual-machines/windows/availability-group-overview.md).
    - [Een always on-beschikbaarheids groep configureren op virtuele machines van Azure in verschillende regio's](../../../azure-sql/virtual-machines/windows/availability-group-manually-configure-multiple-regions.md).
    - [Een Load Balancer configureren voor een AlwaysOn-beschikbaarheids groep in azure](../../../azure-sql/virtual-machines/windows/availability-group-load-balancer-portal-configure.md).
- Oracle Data Guard voor Windows en Oracle Linux. Meer informatie over Oracle Linux vindt u in dit artikel:
    - [Oracle Data Guard implementeren op een virtuele machine van Azure Linux](../oracle/configure-oracle-dataguard.md)
- IBM Db2 HADR op SUSE en RHEL gedetailleerde documentatie voor SUSE en RHEL met behulp van pacemaker vindt u hier:
    - [Hoge Beschik baarheid van IBM Db2 LUW op Azure Vm's op SUSE Linux Enterprise Server met pacemaker](./dbms-guide-ha-ibm.md)
    - [Hoge beschikbaarheid van IBM Db2 LUW in Azure-VM's in Red Hat Enterprise Linux Server](./high-availability-guide-rhel-ibm-db2-luw.md)
- Configuratie van SAP ASE en SAP maxDB, zoals wordt beschreven in deze documenten:
    - [DBMS-implementatie voor SAP-werkbelasting in virtuele Azure-machines voor SAP ASE](./dbms_guide_sapase.md)
    - [Implementatie van SAP MaxDB, liveCache en Content Server op virtuele machines van Azure](./dbms_guide_maxdb.md)
- De scenario's met hoge Beschik baarheid in HANA-exemplaren worden beschreven in:
    - [Ondersteunde scenario's voor HANA grote instanties-HSR met STONITH voor hoge Beschik baarheid](./hana-supported-scenario.md#hsr-with-stonith-for-high-availability)
    - [Ondersteunde scenario's voor HANA grote instanties-automatische failover van de host (1 + 1)](./hana-supported-scenario.md#host-auto-failover-11)

> [!IMPORTANT]
> Voor geen van de hierboven beschreven scenario's ondersteunen we configuraties van meerdere DBMS-instanties in één VM. In elk van deze gevallen kan slechts één data base-exemplaar worden geïmplementeerd per VM en worden beveiligd met de beschreven methoden voor hoge Beschik baarheid. Het beveiligen van meerdere DBMS-instanties onder hetzelfde Windows-of pacemaker-failovercluster wordt op dit moment **niet** ondersteund. Oracle Data Guard wordt ook alleen ondersteund voor implementatie cases per VM. 

Verschillende database systemen kunnen meerdere data bases hosten in één DBMS-exemplaar. Zoals in het geval van SAP HANA, kunnen meerdere data bases worden gehost in meerdere database containers (MDC). Voor gevallen waarin deze configuraties met meerdere data bases binnen één failoverclusterbron werken, worden deze configuraties ondersteund. Configuraties die niet worden ondersteund, zijn gevallen waarin meerdere cluster bronnen vereist zijn. Net als bij configuraties waarbij u meerdere SQL Server-beschikbaarheids groepen definieert, onder een SQL Server-exemplaar.


![Configuratie van DBMS-HA](./media/sap-planning-supported-configurations/database-high-availability-configuration.png)

Afhankelijk van het DBMS van een/-besturings systeem kunnen onderdelen zoals Azure load balancer mogelijk wel of niet vereist zijn als onderdeel van de oplossings architectuur. 

In het bijzonder voor maxDB moet de opslag configuratie verschillend zijn. In maxDB moeten de gegevens en logboek bestanden zich bevinden in de gedeelde opslag voor configuraties met een hoge Beschik baarheid. In het geval van maxDB wordt gedeelde opslag alleen ondersteund voor hoge Beschik baarheid. Voor alle andere DBMS-afzonderlijke opslag stacks per knoop punt zijn de enige ondersteunde schijf configuraties.

Andere frameworks met hoge Beschik baarheid zijn bekend en zijn bekend om ook op Microsoft Azure te worden uitgevoerd. Micro soft heeft deze frameworks echter niet getest. Als u uw configuratie met hoge Beschik baarheid wilt bouwen met deze frameworks, moet u de provider van die software gebruiken om het volgende te doen:

- Een implementatie architectuur ontwikkelen
- Implementatie van de architectuur
- Ondersteuning van de architectuur

> [!IMPORTANT]
> Microsoft Azure Marketplace biedt een aantal zachte apparaten die opslag oplossingen bieden boven op de systeem eigen Azure-opslag. Deze zachte apparaten kunnen worden gebruikt voor het maken van NFS-shares en die theoretisch kunnen worden gebruikt in de SAP HANA scale-out-implementaties waarbij een stand-by knoop punt is vereist. Vanwege verschillende redenen wordt geen van deze tijdelijke opslag apparaten ondersteund voor een van de DBMS-implementaties van micro soft en SAP on Azure. Implementaties van DBMS op SMB-shares worden op dit moment niet ondersteund. Implementaties van DBMS op NFS-shares is beperkt tot NFS 4,1-shares op [Azure NetApp files](https://azure.microsoft.com/services/netapp/).


## <a name="high-availability-for-sap-central-service"></a>Hoge Beschik baarheid voor SAP Central-service
SAP Central Services is een tweede Single Point of Failure van uw SAP-configuratie. Als gevolg hiervan moet u deze processen voor centrale Services ook beveiligen. De aanbieding die wordt ondersteund en gedocumenteerd voor SAP-werk belasting leest er als volgt uit:

- Windows-failovercluster server met scale-out bestands services van Windows voor sapmnt en globale transport Directory. Details worden beschreven in het artikel:
    - [Een SAP ASCS/SCS-exemplaar op een Windows-failovercluster clusteren met behulp van een bestands share in azure](./sap-high-availability-guide-wsfc-file-share.md)
    - [Azure-infra structuur voor SAP-hoge Beschik baarheid voorbereiden met behulp van een Windows-failovercluster en een bestands share voor SAP ASCS/SCS-instanties](./sap-high-availability-infrastructure-wsfc-file-share.md)
- Windows-failovercluster server met SMB-share op basis van [Azure NetApp files](https://azure.microsoft.com/services/netapp/) voor sapmnt en globale transport Directory. Meer informatie vindt u in het artikel:
    - [Hoge Beschik baarheid voor SAP NetWeaver op Azure Vm's in Windows met Azure NetApp Files (SMB) voor SAP-toepassingen](./high-availability-guide-windows-netapp-files-smb.md)
- Windows failover cluster server op basis van SIOS `Datakeeper` . Hoewel u hebt beschreven door micro soft, hebt u een ondersteunings relatie met SIOS nodig, zodat u kunt samen werken met de ondersteuning voor SIOS wanneer u deze oplossing gebruikt. Details worden beschreven in het artikel:
    - [Een SAP ASCS/SCS-exemplaar op een Windows-failovercluster clusteren met behulp van een gedeelde cluster schijf in azure](./sap-high-availability-guide-wsfc-shared-disk.md)
    - [De Azure-infra structuur voor SAP HA voorbereiden met behulp van een Windows-failovercluster en een gedeelde schijf voor SAP ASCS/SCS](./sap-high-availability-infrastructure-wsfc-shared-disk.md)
- Pacemaker op SUSE-besturings systeem met het maken van een Maxi maal beschik bare NFS-share met twee SUSE-Vm's en `drdb` voor bestands replicatie. Details worden beschreven in het artikel
    - [Hoge Beschik baarheid voor SAP NetWeaver op Azure Vm's op SUSE Linux Enterprise Server voor SAP-toepassingen](./high-availability-guide-suse.md)
    - [Hoge Beschik baarheid voor NFS op Azure Vm's op SUSE Linux Enterprise Server](./high-availability-guide-suse-nfs.md)
- Pacemaker SUSE-besturings systeem met gebruik van NFS-shares die door [Azure NetApp files](https://azure.microsoft.com/services/netapp/)worden verstrekt. Details zijn gedocumenteerd in
    - [Hoge Beschik baarheid voor SAP NetWeaver op Azure Vm's op SUSE Linux Enterprise Server met Azure NetApp Files voor SAP-toepassingen](./high-availability-guide-suse-netapp-files.md)
- Pacemaker op een Red Hat-besturings systeem met NFS-share gehost op een `glusterfs` cluster. Meer informatie vindt u in de artikelen
    - [Azure Virtual Machines hoge Beschik baarheid voor SAP NetWeaver op Red Hat Enterprise Linux](./high-availability-guide-rhel.md)
    - [`GlusterFS` op virtuele machines van Azure op Red Hat Enterprise Linux voor SAP net-Weaver](./high-availability-guide-rhel-glusterfs.md)
- Pacemaker op het Red Hat-besturings systeem met de NFS-share gehost op [Azure NetApp files](https://azure.microsoft.com/services/netapp/). Details worden beschreven in het artikel
    - [Azure Virtual Machines hoge Beschik baarheid voor SAP NetWeaver op Red Hat Enterprise Linux met Azure NetApp Files voor SAP-toepassingen](./high-availability-guide-rhel-netapp-files.md)

Van de vermelde oplossingen hebt u een ondersteunings relatie met SIOS nodig ter ondersteuning van het `Datakeeper` product en om met SIOS rechtstreeks in het geval van problemen te werken. Afhankelijk van de manier waarop u het Windows-, Red Hat-en/of SUSE-besturings systeem hebt gelicentieerd, zou u ook een ondersteunings contract moeten hebben met uw OS-provider voor een volledige ondersteuning van de vermelde configuraties met hoge Beschik baarheid.

De configuratie kan ook als volgt worden weer gegeven:

![Configuratie van DBMS en ASCS HA](./media/sap-planning-supported-configurations/high-available-3-tier-configuration.png)

Aan de rechter kant van de afbeeldingen wordt de Maxi maal beschik bare SAP Central-Services weer gegeven. Naast de SAP-services die worden beveiligd met een failovercluster dat een failover kan uitvoeren in het geval van een probleem, is er een nood zaak voor een Maxi maal beschik bare NFS-of SMB-share of een gedeelde Windows-schijf om ervoor te zorgen dat de sapmnt en de globale transport Directory onafhankelijk zijn van het bestaan van één virtuele machine. Meer van de oplossingen, zoals Windows failover cluster server en pacemaker, vereisen een Azure-load balancer om verkeer naar een goedend knoop punt te sturen of omleiden.

In de weer gegeven lijst staat geen vermelding van het Oracle Linux besturings systeem. Oracle Linux biedt geen ondersteuning voor pacemaker als een cluster raamwerk. Als u uw SAP-systeem op Oracle Linux wilt implementeren en u een Framework met hoge Beschik baarheid voor Oracle Linux nodig hebt, moet u met leveranciers van derden werken. Een van de leveranciers is SIOS met hun beveiligings pakket voor Linux dat door SAP on Azure wordt ondersteund. Lees voor meer informatie SAP [-opmerking #1662610: ondersteunings Details voor het SIOS-beveiligings pakket voor Linux](https://launchpad.support.sap.com/#/notes/1662610) voor meer informatie.



### <a name="supported-storage-with-the-sap-central-services-scenarios-listed-above"></a>Ondersteunde opslag met de scenario's voor SAP Central Services hierboven vermeld
Omdat slechts een subset van Azure Storage-typen Maxi maal beschik bare NFS of SMB-shares levert die de kwaliteit voor het gebruik in de cluster scenario's van de SAP-Centrale Services biedt een lijst met ondersteunde opslag typen

- Windows-failovercluster server met scale-out Bestands server van Windows kan worden geïmplementeerd op alle systeem eigen Azure-opslag typen, met uitzonde ring van Azure NetApp Files. Aanbeveling is echter om gebruik te maken van Premium Storage als gevolg van superieure service overeenkomsten in door Voer en IOPS.
- Windows failover cluster server met SMB op Azure NetApp Files wordt ondersteund op Azure NetApp Files. SMB-shares op Azure File Services worden op dit moment **niet** ondersteund.
- Windows-failovercluster-server met een gedeelde Windows-schijf op basis van SIOS `Datakeeper` kan worden geïmplementeerd op alle systeem eigen Azure-opslag typen, met uitzonde ring van Azure NetApp files. Aanbeveling is echter om gebruik te maken van Premium Storage als gevolg van superieure service overeenkomsten in door Voer en IOPS.
- SUSE of Red Hat pacemaker met NFS-shares op Azure NetApp Files wordt ondersteund op Azure NetApp Files. 
- SUSE pacemaker met behulp `drdb` van een configuratie tussen twee vm's wordt ondersteund met behulp van systeem eigen Azure-opslag typen, met uitzonde ring van Azure NetApp files. Aanbeveling is echter om gebruik te maken van Premium Storage als gevolg van superieure service overeenkomsten in door Voer en IOPS.
- Red Hat pacemaker met `glusterfs` voor het leveren van NFS-share wordt ondersteund met systeem eigen Azure-opslag typen, met uitzonde ring van Azure NetApp files. Aanbeveling is echter om gebruik te maken van Premium Storage als gevolg van superieure service overeenkomsten in door Voer en IOPS.

> [!IMPORTANT]
> Microsoft Azure Marketplace biedt een aantal zachte apparaten die opslag oplossingen bieden boven op de systeem eigen Azure-opslag. Deze zachte apparaten kunnen worden gebruikt voor het maken van NFS-of SMB-shares, maar ook in de door failover geclusterde SAP Centrale Services. Deze oplossingen worden niet rechtstreeks ondersteund voor SAP-workloads door micro soft. Als u besluit om een dergelijke oplossing te gebruiken om uw NFS-of SMB-share te maken, moet ondersteuning voor de configuratie van de SAP Central-service worden geboden door de externe partij die eigenaar is van de software in het tijdelijke apparaat voor opslag.


## <a name="multi-sid-sap-central-services-failover-clusters"></a>Failover-clusters met multi-SID SAP Central Services
Om het aantal Vm's te verminderen dat nodig is voor grote SAP-landschappen, kunt u met SAP SAP-services van Centraal beheer uitvoeren van meerdere verschillende SAP-systemen in de configuratie van een failovercluster. Stel dat u 30 of meer netweave of S/4HANA-productie systemen hebt. Zonder multi-SID clustering moeten voor deze configuraties 60 of meer virtuele machines zijn in 30 of meer Windows-of pacemaker. Naast de benodigde DBMS-failoverclusters. Het implementeren van meerdere SAP-Centrale Services op twee knoop punten in een configuratie van een failovercluster kan het aantal Vm's aanzienlijk verminderen. Het implementeren van meerdere SAP Central Services-instanties in een cluster configuratie met één twee knoop punten heeft echter ook enkele nadelen. Problemen rond één virtuele machine in de cluster configuratie zijn van toepassing op meerdere SAP-systemen. Onderhoud voor het gast besturingssysteem dat wordt uitgevoerd in de cluster configuratie vereist meer coördinatie omdat meerdere productie-SAP-systemen worden beïnvloed. Hulpprogram ma's zoals SAP LaMa bieden geen ondersteuning voor multi-SID-clustering in het proces voor het klonen van het systeem.

In azure wordt een multi-SID-cluster configuratie ondersteund voor het Windows-besturings systeem met ENSA1 en ENSA2. Aanbeveling is de oudere ENSA1-replicatie service architectuur (in de wachtrij plaatsen) niet te combi neren met de nieuwe architectuur (ENSA2) op één Multi-SID-cluster. Informatie over een dergelijke architectuur wordt beschreven in de artikelen

- [SAP ASCS/SCS instance multi-SID hoge Beschik baarheid met Windows Server Failover Clustering en gedeelde schijf op Azure](./sap-ascs-ha-multi-sid-wsfc-shared-disk.md) 
- [SAP ASCS/SCS instance multi-SID hoge Beschik baarheid met Windows Server Failover Clustering en file share op Azure](./sap-ascs-ha-multi-sid-wsfc-file-share.md) 

Voor SUSE wordt een multi-SID-cluster op basis van pacemaker ook ondersteund. Tot nu toe wordt de configuratie ondersteund voor:

- Maxi maal vijf SAP-ASCS/SCS-instanties
- De oude server-ijs voor in wachtrij plaatsen (ENSA1)
- Cluster configuraties met twee knoop punten pacemaker

De configuratie wordt beschreven in [hoge Beschik baarheid voor SAP NetWeaver op Azure vm's op SuSE Linux Enterprise Server voor de multi-sid-hand leiding voor SAP-toepassingen](./high-availability-guide-suse-multi-sid.md)

Een multi-SID-cluster met schematische voors telling van replicatie server ziet er als volgt uit:

![Diagram waarin een multi-SID-cluster wordt weer gegeven met replicatie server in de wachtrij.](./media/sap-planning-supported-configurations/high-available-multi-system-configuration.png)


## <a name="sap-hana-scale-out-scenarios"></a>SAP HANA scale-out-scenario's
SAP HANA scale-out-scenario's worden ondersteund voor een subset van de HANA-gecertificeerde Azure-Vm's, zoals vermeld in de [Hardware-map van SAP Hana](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). Alle Vm's die zijn gemarkeerd met ja in de kolom Clustering kunnen worden gebruikt voor de scale-out van OLAP of S/4HANA. Configuraties zonder stand-by worden ondersteund met de Azure Storage typen: 

- Azure Premium Storage, inclusief Azure write Accelerator voor het/Hana/log-volume
- [Ultraschijven](../../disks-enable-ultra-ssd.md)
- [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) 

SAP HANA scale-out-configuraties voor OLAP-of S/4HANA met stand-by-knoop punt (en) worden exclusief ondersteund door de door NFS gedeelde host op Azure NetApp Files.

Raadpleeg de artikelen voor meer informatie over de exacte opslag configuraties met of zonder het knoop punt stand-by.

- [Configuraties van SAP HANA in virtuele Azure-machineopslag](./hana-vm-operations-storage.md) 
- [Een SAP HANA scale-out systeem met stand-by-knoop punt op virtuele Azure-machines implementeren met behulp van Azure NetApp Files op SUSE Linux Enterprise Server](./sap-hana-scale-out-standby-netapp-files-suse.md)
- [Een SAP HANA scale-out systeem met stand-by-knoop punt op virtuele Azure-machines implementeren met behulp van Azure NetApp Files op Red Hat Enterprise Linux](./sap-hana-scale-out-standby-netapp-files-rhel.md)
- [SAP-ondersteunings Opmerking #2080991](https://launchpad.support.sap.com/#/notes/2080991)

De volgende documentatie is van toepassing op de details van HANA-configuraties die worden ondersteund door de Hana grote instanties:

- [Ondersteunde scenario's voor scale-out van HANA grote instanties met stand-by](./hana-supported-scenario.md#scale-out-with-standby)
- [Ondersteunde scenario's voor scale-out van HANA-grote instanties zonder stand-by](./hana-supported-scenario.md#scale-out-without-standby)


## <a name="disaster-recovery-scenario"></a>Scenario voor herstel na nood gevallen
Er zijn verschillende scenario's voor herstel na nood gevallen die worden ondersteund. We definiëren nood architecturen als architecturen die voor een volledige Azure-regio die het raster gaat compenseren. Dit betekent dat het herstel doel voor nood gevallen een andere Azure-regio als doel moet zijn om uw SAP-landschap uit te voeren. Er zijn verschillende methoden en configuraties in de DBMS-laag en de niet-DBMS-laag. 

### <a name="dbms-layer"></a>DBMS-laag
Voor de DBMS-laag worden configuraties die gebruikmaken van de systeem eigen DBMS-replicatie mechanismen, zoals always on, Oracle Data Guard, Db2 HADR, SAP ASE always on of HANA System Replication ondersteund. Het is verplicht dat de replicatie stroom in dergelijke gevallen asynchroon is, in plaats van synchroon als in typische scenario's voor hoge Beschik baarheid die binnen één Azure-regio worden geïmplementeerd. Een typisch voor beeld van een dergelijke ondersteunde configuratie van een DBMS voor nood herstel wordt beschreven in het artikel [SAP Hana Beschik baarheid in azure-regio's](./sap-hana-availability-across-regions.md#combine-availability-within-one-region-and-across-regions). In de tweede afbeelding in deze sectie wordt een scenario met HANA als voor beeld beschreven. De belangrijkste data bases die worden ondersteund voor SAP-toepassingen, kunnen worden geïmplementeerd in een dergelijk scenario.

Het wordt ondersteund om een kleinere virtuele machine als doel exemplaar te gebruiken in het gebied voor herstel na nood gevallen, omdat deze virtuele machine geen gebruik maakt van het volledige workload-verkeer. Als u dit doet, moet u rekening houden met de volgende overwegingen:

- Bij kleinere VM-typen is het niet mogelijk dat er veel schijven zijn gekoppeld aan kleinere Vm's
- Kleinere Vm's hebben minder netwerk-en opslag doorvoer
- Het opnieuw instellen van de grootte van VM-families kan een probleem zijn wanneer de verschillende virtuele machines worden verzameld in één Azure-Beschikbaarheidsset of wanneer de hergrootte moet plaatsvinden tussen de M-serie en de Mv2-serie Vm's
- CPU-en geheugen gebruik voor het data base-exemplaar waarmee de stroom van wijzigingen met minimale vertraging en voldoende CPU-en geheugen bronnen kan worden ontvangen om deze wijzigingen toe te passen met minimale vertraging van de gegevens  

Meer informatie over de beperkingen van verschillende VM-grootten vindt u [hier](../../sizes.md) 

Een andere ondersteunde methode voor het implementeren van een DR-doel is dat er een tweede DBMS-exemplaar is geïnstalleerd op een virtuele machine waarop een niet-productie-DBMS-exemplaar van een niet-productie-SAP-instantie wordt uitgevoerd. Dit kan een beetje lastiger zijn omdat u moet weten welke geheugen, CPU-bronnen, netwerk bandbreedte en opslag bandbreedte nodig zijn voor de specifieke doel instanties die in het nood geval moeten functioneren als hoofd instantie. Met name in HANA wordt het ten zeerste aanbevolen dat u de instantie die fungeert als een DR-doel op een gedeelde host configureert, zodat de gegevens niet vooraf in het doel exemplaar van de nood herstel server worden geladen.

Controleer de volgende documenten voor de DR-scenario's van HANA grote exemplaren:

- [Eén knoop punt met behulp van opslag replicatie met DR](./hana-supported-scenario.md#single-node-with-dr-using-storage-replication)
- [Eén knoop punt met DR (Multipurpose) met behulp van storage-replicatie](./hana-supported-scenario.md#single-node-with-dr-multipurpose-using-storage-replication)
- [Eén knoop punt met DR (Multipurpose) met behulp van storage-replicatie](./hana-supported-scenario.md#single-node-with-dr-multipurpose-using-storage-replication)
- [Hoge Beschik baarheid met HSR en DR met opslag replicatie](./hana-supported-scenario.md#high-availability-with-hsr-and-dr-with-storage-replication)
- [Uitschalen met behulp van de opslag replicatie met DR](./hana-supported-scenario.md#scale-out-with-dr-using-storage-replication)
- [Eén knoop punt met DR met behulp van HSR](./hana-supported-scenario.md#single-node-with-dr-using-hsr)
- [HSR van één knoop punt naar DR (kosten geoptimaliseerd)](./hana-supported-scenario.md#single-node-hsr-to-dr-cost-optimized)
- [Hoge Beschik baarheid en herstel na nood gevallen met HSR](./hana-supported-scenario.md#high-availability-and-disaster-recovery-with-hsr)
- [Hoge Beschik baarheid en herstel na nood gevallen met HSR (kosten geoptimaliseerd)](./hana-supported-scenario.md#high-availability-and-disaster-recovery-with-hsr-cost-optimized)
- [Uitschalen met DR met behulp van HSR](./hana-supported-scenario.md#scale-out-with-dr-using-hsr)

> [!NOTE]
> Het gebruik van [Azure site Recovery](https://azure.microsoft.com/services/site-recovery/) is niet getest op DBMS-implementaties onder SAP-workload. Als gevolg hiervan wordt het op dit moment niet ondersteund voor de DBMS-laag van SAP-systemen. Andere methoden van replicaties door micro soft en SAP die niet worden vermeld, worden niet ondersteund. Het gebruik van software van derden voor het repliceren van de DBMS-laag van SAP-systemen tussen verschillende Azure-regio's moet worden ondersteund door de leverancier van de software en wordt niet ondersteund door middel van micro soft-en SAP-ondersteunings kanalen. 
 
## <a name="non-dbms-layer"></a>Niet-DBMS-laag
Voor de SAP-toepassingslaag en eventuele shares of opslag locaties die nodig zijn, worden de twee belang rijke scenario's door klanten gebruikt:

- De doel stellingen voor herstel na nood gevallen in de tweede Azure-regio worden niet gebruikt voor productie-of niet-productie doeleinden. In dit scenario worden de virtuele machines die als herstel doel fungeren in het ideale geval niet geïmplementeerd en wordt de installatie kopie en wijzigingen in de installatie kopieën van de productie-SAP-toepassingslaag gerepliceerd naar de regio voor nood herstel. Een functionaliteit waarmee een dergelijke taak kan worden uitgevoerd, is [Azure site Recovery](../../../site-recovery/azure-to-azure-move-overview.md). Azure Site Recovery ondersteunen een Azure-naar-Azure-replicatie scenario als volgt. 
- De doel stellingen voor herstel na nood gevallen zijn Vm's die daad werkelijk worden gebruikt door niet-productie systemen. Het hele SAP-landschap is verdeeld over twee verschillende Azure-regio's met productie systemen meestal in één regio en niet-productie systemen in een andere regio. In een groot aantal klant implementaties heeft de klant een niet-productie systeem dat gelijk is aan een productie systeem. De klant heeft exemplaren van productie toepassingen die vooraf zijn geïnstalleerd op de Application Layer niet-productie systemen. In het geval van een failover worden de niet-productie-instanties afgesloten. de virtuele namen van de productie-Vm's worden verplaatst naar de niet-productie-Vm's (na het toewijzen van nieuwe IP-adressen in DNS) en de vooraf geïnstalleerde productie-exemplaren worden aan de slag

### <a name="sap-central-services-clusters"></a>SAP Central Services-clusters
SAP Central Services-clusters die gebruikmaken van gedeelde schijven (Windows), SMB-shares (Windows) of NFS-shares zijn een lastig te repliceren. Windows Storage-replicatie aan de Windows-zijde is een mogelijke oplossing. In Linux rsync is een levensvat bare oplossing.



## <a name="non-supported-scenario"></a>Niet-ondersteund scenario
Er is een lijst met scenario's die niet worden ondersteund voor SAP-workloads op Azure-architecturen. **Niet ondersteund** betekent dat SAP en micro soft deze configuraties niet kunnen ondersteunen en niet moeten worden uitgesteld naar een waarschijnlijke derde partij die de software heeft verschaft om dergelijke architecturen tot stand te brengen. Twee van de categorieën zijn:

- Tijdelijke apparaten voor opslag: er zijn een aantal tijdelijke opslag apparaten beschikbaar in azure Marketplace. Sommige leveranciers bieden eigen documentatie over het gebruik van deze opslag Soft-apparaten op Azure met betrekking tot SAP-software. Ondersteuning van configuraties of implementaties met dergelijke opslag apparaten moet worden geboden door de leverancier van die tijdelijke apparaten voor opslag. Dit feit wordt ook in SAP- [ondersteunings notitie](https://launchpad.support.sap.com/#/notes/2015553) gemanifesteerd #2015553
- Raam werk voor hoge Beschik baarheid: alleen pacemaker en Windows Server-failovercluster worden ondersteund kaders voor hoge Beschik baarheid voor SAP-workload op Azure. Zoals eerder vermeld, wordt de oplossing van SIOS `Datakeeper` beschreven en gedocumenteerd door micro soft. Niettemin moeten de onderdelen van SIOS `Datakeeper` door SIOS worden ondersteund als de leverancier die onderdelen levert. SAP vermeldt ook andere gecertificeerde hoogwaardige Framework-Beschik baarheid in diverse SAP-notities. Sommige hiervan zijn ook gecertificeerd door de leverancier van derden voor Azure. Ondersteuning voor configuraties die gebruikmaken van deze producten moeten echter worden geboden door de leverancier van het product. Verschillende leveranciers hebben een andere integratie in de SAP-ondersteunings processen. U moet nagaan welk ondersteunings proces het meest geschikt is voor de desbetreffende leverancier voordat u besluit het product te gebruiken in SAP-configuraties die in azure zijn geïmplementeerd.
- Gedeelde schijf clusters waarbij database bestanden zich op de gedeelde schijven bevinden, worden niet ondersteund met uitzonde ring van maxDB. Voor alle andere data bases is het mogelijk om afzonderlijke opslag locaties te gebruiken in plaats van een SMB-of NFS-share of gedeelde schijf om scenario's met hoge Beschik baarheid te configureren

Andere scenario's, die niet worden ondersteund, zijn scenario's zoals:

- Implementatie scenario's die een grotere netwerk latentie introduceren tussen de SAP-toepassingslaag en de SAP-DBMS-laag in de algemene architectuur van SAP, zoals wordt weer gegeven in NetWeaver, S/4HANA en bijvoorbeeld `Hybris` . Dit omvat:
    - Een van de lagen op locatie implementeren terwijl de andere laag wordt geïmplementeerd in azure
    - De SAP-gegevenslaagtoepassing van een systeem implementeren in een andere Azure-regio dan de DBMS-laag
    - Implementatie van één laag in data centers die zich op dezelfde locatie bevinden als Azure en de andere laag in azure, behalve wanneer een architectuur patroon wordt verschaft door een systeem eigen Azure-service
    - Virtuele netwerk apparaten implementeren tussen de SAP-gegevenslaagtoepassing en de DBMS-laag
    - Opslag gebruiken die wordt gehost in data centers die zijn opgeslagen in het Azure-Data Center voor de SAP-DBMS-laag of SAP Global Trans Port-adres lijst
    - De twee lagen worden geïmplementeerd met twee verschillende Cloud leveranciers. De DBMS-laag bijvoorbeeld implementeren in de Oracle-Cloud infrastructuur en de toepassingslaag in azure
- Configuraties van pacemaker HANA-clusters met meerdere instanties
- Windows-cluster configuraties met gedeelde schijven via SOFS of SMB op ANF voor SAP-data bases die worden ondersteund in Windows. In plaats daarvan wordt het gebruik van de native replicatie met hoge Beschik baarheid van de bepaalde data bases aangeraden en afzonderlijke opslag stacks gebruikt
- Implementatie van SAP-data bases die worden ondersteund in Linux met database bestanden die zich in NFS-shares bevinden op ANF, met uitzonde ring van SAP HANA
- Implementatie van Oracle DBMS op elk ander gast besturingssysteem dan Windows en Oracle Linux. Zie ook [SAP-ondersteunings opmerking #2039619](https://launchpad.support.sap.com/#/notes/2039619)

Een of meer scenario ('s) die we niet hebben getest en daarom geen ervaring hebben met lijst als:

- Azure Site Recovery het repliceren van DBMS-laag-Vm's. Daarom is het raadzaam om de data base-systeem eigen asynchrone replicatie functionaliteit te gebruiken voor mogelijke nood herstel configuratie
 

## <a name="next-steps"></a>Volgende stappen
De volgende stappen in de [Azure virtual machines planning en implementatie van de SAP net-Weaver](./planning-guide.md) lezen




  