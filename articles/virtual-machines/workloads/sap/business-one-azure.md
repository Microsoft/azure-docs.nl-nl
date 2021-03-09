---
title: SAP Business One in azure Virtual Machines | Microsoft Docs
description: SAP Business One in Azure.
author: msjuergent
ms.service: virtual-machines-sap
ms.topic: article
ms.date: 07/15/2018
ms.author: juergent
ms.openlocfilehash: e17739c65c0b80beb1f6fdd09f31897b317d7858
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102506885"
---
# <a name="sap-business-one-on-azure-virtual-machines"></a>SAP Business One in virtuele Azure-machines
Dit document bevat richt lijnen voor het implementeren van SAP Business One in azure Virtual Machines. De documentatie is geen vervanging voor de installatie documentatie van Business One voor SAP. De documentatie moet betrekking hebben op elementaire plannings-en implementatie richtlijnen voor de Azure-infra structuur voor het uitvoeren van één toepassing op.

Business One ondersteunt twee verschillende data bases:
- SQL Server-Zie [SAP-opmerking #928839-release planning voor Microsoft SQL Server](https://launchpad.support.sap.com/#/notes/928839)
- SAP HANA-voor een ondersteunings matrix van de [SAP-product](https://support.sap.com/pam) ondersteuning voor SAP Hana

Met betrekking tot SQL Server zijn de basis overwegingen voor implementatie zoals beschreven in de [Azure virtual machines DBMS-implementatie voor SAP NetWeaver](./dbms_guide_general.md) van toepassing. in dit document worden de overwegingen voor SAP HANA beschreven.

## <a name="prerequisites"></a>Vereisten
Als u deze hand leiding wilt gebruiken, moet u basis kennis hebben van de volgende Azure-onderdelen:

- [Azure virtual machines op Windows](../../windows/tutorial-manage-vm.md)
- [Azure virtual machines op Linux](../../linux/tutorial-manage-vm.md)
- [Beheer van Azure-netwerken en virtuele netwerken met Power shell](../../windows/tutorial-virtual-network.md)
- [Azure-netwerken en virtuele netwerken met CLI](../../linux/tutorial-virtual-network.md)
- [Azure-schijven beheren met de Azure CLI](../../linux/tutorial-manage-disks.md)

Zelfs als u alleen geïnteresseerd bent in bedrijven, kan het document [Azure virtual machines planning en implementatie voor SAP NetWeaver](./planning-guide.md) een goede bron van informatie zijn.

De veronderstelling is dat u als instantie implementeert SAP Business One:

- Vertrouwd zijn met het installeren van SAP HANA op een bepaalde infra structuur, zoals een virtuele machine
- Vertrouwde installatie van de SAP Business One-toepassing op een infra structuur zoals virtuele Azure-machines
- Vertrouwd zijn met bedrijfs-SAP-bedrijven en het gekozen DBMS-systeem
- Vertrouwd zijn met het implementeren van de infra structuur in azure

Al deze gebieden worden niet in dit document behandeld.

Afgezien van de Azure-documentatie moet u zich bewust zijn van de belangrijkste SAP-notities, die betrekking hebben op Business One of de centrale notities van SAP voor bedrijven One:

- [528296-algemeen overzicht opmerking voor SAP Business One releases en gerelateerde producten](https://launchpad.support.sap.com/#/notes/528296)
- [2216195-release-updates opmerking voor SAP Business One 9,2, versie voor SAP HANA](https://launchpad.support.sap.com/#/notes/2216195)
- [2483583-centrale notitie voor SAP Business One 9,3](https://launchpad.support.sap.com/#/notes/2483583)
- [2483615-release-updates opmerking voor SAP Business One 9,3](https://launchpad.support.sap.com/#/notes/2483615)
- [2483595-collectieve notitie voor SAP Business One 9,3 algemene problemen](https://launchpad.support.sap.com/#/notes/2483595)
- [2027458-collectieve Consulting opmerking voor SAP HANA-Related-onderwerpen van SAP Business One, versie voor SAP HANA](https://launchpad.support.sap.com/#/notes/2027458)


## <a name="business-one-architecture"></a>Bedrijf met één architectuur
Business One is een toepassing met twee lagen:

- Een client-laag met een FAT-client
- Een gegevenslaag die het database schema voor een Tenant bevat

Een beter overzicht van de onderdelen die worden uitgevoerd in het client onderdeel en de onderdelen die worden uitgevoerd in het Server onderdeel, worden beschreven in [SAP Business One Administrator's Guide (Engelstalig](https://help.sap.com/doc/601fbd9113be4240b81d74626439cfa9/10.0/en-US/AdministratorGuide_SQL.pdf) ) 

Omdat er een belang rijke interactie met de latentie tussen de client tier en de DBMS-laag is, moeten beide lagen zich in azure bevinden tijdens de implementatie in Azure. het is gebruikelijk dat gebruikers vervolgens RDS in een of meer virtuele machines met een RDS-service voor het bedrijf één client onderdelen.

### <a name="sizing-vms-for-sap-business-one"></a>Vm's aanpassen voor SAP Business One

Met betrekking tot de grootte van de virtuele machine (s) van de client worden de resource vereisten beschreven door SAP in het document [SAP Business One hardware requirements Guide (Engelstalig](https://help.sap.com/doc/bfa9770d12284cce8509956dcd4c5fcb/9.3/en-US/B1_Hardware_Requirements_Guide.pdf)). Voor Azure moet u zich richten op en berekenen met de vereisten die zijn vermeld in hoofd stuk 2,4 van het document.

Als Azure virtual machines voor het hosten van het bedrijf één client onderdelen en de DBMS-host, zijn alleen Vm's toegestaan die worden ondersteund door SAP net-Weaver. Als u de lijst met ondersteunde Azure-Vm's voor SAP NetWeaver wilt vinden, raadpleegt u [SAP Note #1928533](https://launchpad.support.sap.com/#/notes/1928533).

SAP HANA als DBMS-back-end voor Business One worden uitgevoerd, worden alleen Vm's, die in de [lijst Hana Certified IaaS platform](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure%23SAP%20Business%20One) worden vermeld, ondersteund voor Hana. Het bedrijf One-client onderdelen wordt niet beïnvloed door deze sterkere beperking voor de SAP HANA als DBMS-systeem.

### <a name="operating-system-releases-to-use-for-sap-business-one"></a>Versies van het besturings systeem die voor SAP Business One worden gebruikt

In principe is het altijd het beste om de meest recente versies van het besturings systeem te gebruiken. Met name in de Linux-ruimte werd de nieuwe functionaliteit van Azure geïntroduceerd met verschillende nieuwere, kleine releases van SuSE en Red Hat. Aan de kant van Windows wordt het gebruik van Windows Server 2016 ten zeerste aanbevolen.


## <a name="deploying-infrastructure-in-azure-for-sap-business-one"></a>Implementatie van een infra structuur in azure voor SAP Business One
In de volgende hoofd stukken zijn de infrastructuur onderdelen die van belang zijn voor het implementeren van SAP.

### <a name="azure-network-infrastructure"></a>Azure-netwerk infrastructuur
De netwerk infrastructuur die u nodig hebt om te implementeren in azure, is afhankelijk van of u één bedrijf per systeem implementeert. Of u bent een hoster die tien tallen bedrijven een systeem voor klanten host. Het is ook mogelijk dat er kleine wijzigingen zijn in het ontwerp, ongeacht of de manier waarop u verbinding maakt met Azure. Met verschillende mogelijkheden, één ontwerp waarbij u een VPN-verbinding hebt in Azure en waar u uw Active Directory uitbreidt via [VPN](../../../vpn-gateway/vpn-gateway-about-vpngateways.md) of [ExpressRoute](../../../expressroute/expressroute-introduction.md) in Azure.

![Eenvoudige netwerk configuratie met bedrijf 1](./media/business-one-azure/simple-network-with-VPN.PNG)

De weer gegeven vereenvoudigde configuratie introduceert diverse beveiligings instanties waarmee route ring kan worden beheerd en beperkt. Deze begint met 

- De router/firewall voor de on-premises kant van de klant.
- De volgende instantie is de [Azure-netwerk beveiligings groep](../../../virtual-network/network-security-groups-overview.md) die u kunt gebruiken om routerings-en beveiligings regels in te voeren voor Azure VNet dat u uw SAP Business One-configuratie uitvoert in.
- Als u wilt voor komen dat gebruikers van bedrijven één client ook de server kunnen zien waarop het bedrijf één server uitvoert, waarmee de data base wordt uitgevoerd, moet u de virtuele machine die als host fungeert voor het bedrijf met één client en het bedrijf één server in twee verschillende subnetten binnen het VNet, scheiden.
- U gebruikt Azure NSG opnieuw toegewezen aan de twee verschillende subnetten om de toegang tot het bedrijf één server te beperken.

Een meer geavanceerde versie van een Azure-netwerk configuratie is gebaseerd op de door Azure [gedocumenteerde aanbevolen procedures van hub-en spoke-architectuur](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke). Met het architectuur patroon van hub en spoke wordt de eerste vereenvoudigde configuratie gewijzigd in een als volgt:


![Hub-en spoke-configuratie met Business One](./media/business-one-azure/hub-spoke-network-with-VPN.PNG)

Als de gebruikers via Internet verbinding maken zonder particuliere verbinding met Azure, moet het ontwerp van het netwerk in Azure worden afgestemd op de beginselen die zijn beschreven in de Azure-referentie architectuur voor [DMZ tussen Azure en Internet](/azure/architecture/reference-architectures/dmz/secure-vnet-dmz).

### <a name="business-one-database-server"></a>Bedrijf één database server
SQL Server en SAP HANA zijn beschikbaar voor het database type. Onafhankelijk van het DBMS moet u de document [overwegingen voor Azure virtual machines DBMS-implementatie voor SAP-workload](./dbms_guide_general.md) lezen voor een algemeen overzicht van DBMS-implementaties in azure-vm's en de verwante onderwerpen over netwerken en opslag.

Hoewel er al een nadruk is gelegd op de specifieke en algemene database documenten, moet u vertrouwd zijn met:

- [De beschik baarheid van virtuele Windows-machines in azure beheren](../../availability.md) en [de beschik baarheid van virtuele Linux-machines in azure beheren](../../availability.md)
- [SLA voor virtuele machines](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/)

Deze documenten moeten u helpen bij het bepalen van de selectie van opslag typen en de configuratie met een hoge Beschik baarheid.

In principe moet u het volgende doen:

- Gebruik Premium Ssd's via standaard Hdd's. Voor meer informatie over de beschik bare schijf typen raadpleegt u ons artikel [een schijf type selecteren](../../disks-types.md)
- Met Azure beheerde schijven over niet-beheerde schijven gebruiken
- Zorg ervoor dat u voldoende IOPS en I/O-door Voer hebt geconfigureerd met de schijf configuratie
- Combi neer/Hana/data-en/Hana/log-volume om een rendabele opslag configuratie te hebben


#### <a name="sql-server-as-dbms"></a>SQL Server als DBMS
Voor het implementeren van SQL Server als DBMS voor bedrijven One, gaat u naar het document [SQL Server Azure virtual machines DBMS-implementatie voor SAP NetWeaver](./dbms_guide_sqlserver.md). 

Ruwe schattingen voor de DBMS-zijde voor SQL Server zijn:

| Aantal gebruikers | vCPUs | Geheugen | Voor beeld van VM-typen |
| --- | --- | --- | --- |
| Maxi maal 20 | 4 | 16 GB | D4s_v3, E4s_v3 |
| Maxi maal 40 | 8 | 32 GB | D8s_v3, E8s_v3 |
| Maxi maal 80 | 16 | 64 GB | D16s_v3, E16s_v3 |
| Maxi maal 150 | 32 | 128 GB | D32s_v3, E32s_v3 |

Met de hierboven vermelde grootte moet u een idee geven waar u begint met. Het kan zijn dat u minder of meer resources nodig hebt, in welk geval een aanpassing van Azure eenvoudig is. Een wijziging tussen VM-typen is mogelijk met alleen de herstart van de virtuele machine.

#### <a name="sap-hana-as-dbms"></a>SAP HANA als DBMS
Met SAP HANA als DBMS kunt u de volgende secties gebruiken om de overwegingen van het document [SAP Hana op de Azure-bedienings handleiding](./hana-vm-operations.md)te volgen.

Voor een hoge Beschik baarheid en configuraties voor herstel na nood gevallen om SAP HANA als Data Base voor bedrijven in azure, moet u de documentatie lezen [SAP Hana hoge Beschik baarheid voor virtuele Azure-machines](./sap-hana-availability-overview.md) en de documentatie waarnaar in dat document wordt gezocht.

Voor SAP HANA strategieën voor back-up en herstel moet u de document [back-upgids lezen voor SAP Hana op Azure virtual machines](./sap-hana-backup-guide.md) en de documentatie waarnaar wordt gezocht.

 
### <a name="business-one-client-server"></a>Bedrijf met één client server
Overwegingen voor de opslag van deze onderdelen zijn niet het belangrijkste probleem. Niettemin wilt u echter een betrouwbaar platform hebben. Daarom moet u Azure Premium Storage voor deze VM gebruiken, zelfs voor de basis-VHD. Het formaat van de virtuele machine wijzigen, met de gegevens die zijn opgegeven in [SAP Business One hardware requirements Guide (Engelstalig](https://help.sap.com/doc/bfa9770d12284cce8509956dcd4c5fcb/9.3/en-US/B1_Hardware_Requirements_Guide.pdf)). Voor Azure moet u zich richten op en berekenen met de vereisten die zijn vermeld in hoofd stuk 2,4 van het document. Bij het berekenen van de vereisten moet u deze vergelijken met de volgende documenten om de ideale virtuele machine voor u te vinden:

- [Grootten voor Windows Virtual Machines in Azure](../../sizes.md)
- [SAP-notitie #1928533](https://launchpad.support.sap.com/#/notes/1928533)

Vergelijkt het aantal Cpu's en het geheugen dat nodig is voor wat door micro soft wordt gedocumenteerd. Houd bij het kiezen van de virtuele machines ook netwerk doorvoer in acht.
