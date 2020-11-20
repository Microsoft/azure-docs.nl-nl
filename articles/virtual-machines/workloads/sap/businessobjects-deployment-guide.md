---
title: Implementatie van SAP BusinessObjects BI-platform op Azure | Microsoft Docs
description: SAP BusinessObjects BI-platform in azure plannen, implementeren en configureren
services: virtual-machines-windows,virtual-network,storage,azure-netapp-files,azure-files
documentationcenter: saponazure
author: dennispadia
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/05/2020
ms.author: depadia
ms.openlocfilehash: ee7cc1c81bd35bf2b51c712c3a60a046435ee700
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94951280"
---
# <a name="sap-businessobjects-bi-platform-planning-and-implementation-guide-on-azure"></a>Handleiding voor SAP BusinessObjects BI-platformplanning en implementatie in Azure

## <a name="overview"></a>Overzicht

Het doel van deze hand leiding is het bieden van richt lijnen voor het plannen, implementeren en configureren van SAP BusinessObjects BI-platform, ook wel bekend als SAP BOBI-platform op Azure. Deze hand leiding is bedoeld om algemene Azure-Services en-functies te omvatten die relevant zijn voor het SAP BOBI-platform. Deze hand leiding is geen uitputtende lijst met alle mogelijke configuratie opties. Hierin worden oplossingen besproken die gemeen schappelijk zijn voor typische implementatie scenario's.

Deze hand leiding is niet bedoeld als vervanging van de standaard installatie-en beheer handleidingen voor het SAP BOBI-platform, het besturings systeem of een database documentatie.

## <a name="plan-and-implement-sap-businessobjects-bi-platform-on-azure"></a>Het SAP BusinessObjects BI-platform plannen en implementeren op Azure

Microsoft Azure biedt een breed scala aan services, waaronder compute, opslag, netwerken en vele andere voor bedrijven om hun toepassingen te bouwen zonder langdurige aankoop cycli. Met Azure virtual machines (VM) kunnen bedrijven on-demand en schaal bare computer resources implementeren voor verschillende SAP-toepassingen, zoals SAP netweave-toepassingen, SAP Hybris, SAP BusinessObjects BI platform, op basis van hun bedrijfs behoeften. Azure biedt ook ondersteuning voor cross-premises connectiviteit, waarmee bedrijven virtuele machines van Azure kunnen integreren in hun on-premises domeinen, hun eigen Clouds en hun SAP-systeem.

Dit document bevat richt lijnen voor het plannen en implementeren van het SAP BusinessObjects BI-platform op Azure. Het is een aanvulling op de SAP-installatie documentatie en SAP-notities, die de primaire resources vertegenwoordigen voor installaties en implementaties van SAP BOBI.

### <a name="architecture-overview"></a>Overzicht van de architectuur

SAP BusinessObjects BI platform is een systeem dat zich op zichzelf bevindt en dat kan bestaan op één virtuele machine van Azure of kan worden geschaald in een cluster met veel Azure-Virtual Machines waarop verschillende onderdelen worden uitgevoerd. Het SAP BOBI-platform bestaat uit zes conceptuele lagen: client-tier, weblaag, Management-laag, opslaglaag, verwerkings laag en gegevenslaag. (Voor meer informatie over elke laag raadpleegt u de beheerders handleiding in het [SAP BusinessObjects Business Intelligence platform](https://help.sap.com/viewer/product/SAP_BUSINESSOBJECTS_BUSINESS_INTELLIGENCE_PLATFORM/4.3/en-US) Help-Portal). Hieronder vindt u de details op hoog niveau voor elke laag:

- **Client niveau:** Het bevat alle bureau blad-client toepassingen die communiceren met het BI-platform om verschillende soorten rapportage-, analyse-en beheer mogelijkheden te bieden.
- **Weblaag:** Het bevat webtoepassingen die zijn geïmplementeerd op JAVA Web Application-servers. Webtoepassingen bieden de functionaliteit van het BI-platform voor eind gebruikers via een webbrowser.
- **Beheer niveau:** Het coördineert en beheert alle onderdelen die het BI-platform maken. Het bevat een centrale beheer server (CMS) en de gebeurtenis server en de bijbehorende services
- **Opslaglaag:** Het is verantwoordelijk voor het afhandelen van bestanden, zoals documenten en rapporten. Ook wordt de rapport cache afhandeld om systeem bronnen op te slaan wanneer gebruikers toegang krijgen tot rapporten.
- **Verwerkings niveau:** Het analyseert gegevens en maakt rapporten en andere uitvoer typen. Het is de enige laag die toegang heeft tot de data bases die rapport gegevens bevatten.
- **Gegevenslaag:** Het bestaat uit de database servers die als host dienen voor de CMS-systeem databases en het controleren van het gegevens archief.

Het SAP BI-platform bestaat uit een verzameling van servers die worden uitgevoerd op een of meer hosts. Het is essentieel dat u de juiste implementatie strategie kiest op basis van de grootte, de bedrijfs behoefte en het type omgeving. Voor kleine installaties, zoals ontwikkelen of testen, kunt u één virtuele machine van Azure gebruiken voor Web Application Server, database server en alle BI-platform servers. Als u een DBaaS-aanbieding (data base-as-a-Service) van Azure gebruikt, wordt de database server los van andere onderdelen uitgevoerd. Voor een normale en grote installatie kunt u servers uitvoeren op meerdere virtuele machines van Azure.

In de afbeelding hieronder ziet u de architectuur van grootschalige implementatie van het SAP BOBI-platform op Azure virtual machines, waarbij elk onderdeel wordt gedistribueerd en in beschikbaarheids sets wordt geplaatst dat failover kan ondergaan als er sprake is van een onderbreking van de service.

![SAP BusinessObjects BI-platform architectuur op Azure](./media/businessobjects-deployment-guide/businessobjects-architecture-on-azure.png)

#### <a name="architecture-details"></a>Architectuur Details

- Load balancer

  In de SAP BOBI-implementatie met meerdere instanties worden Web Application servers (of weblaag) uitgevoerd op twee of meer hosts. Als u de gebruikers taken gelijkmatig over webservers wilt verdelen, kunt u een load balancer tussen eind gebruikers en webservers gebruiken. In azure kunt u [Azure Load Balancer](../../../load-balancer/load-balancer-overview.md) of [Azure-toepassing gateway](../../../application-gateway/overview.md) gebruiken voor het beheren van verkeer naar uw webservers.

- Web Application servers

  De webserver fungeert als host voor de webtoepassingen van het SAP BOBI-platform zoals CMC en BI launch pad. Voor maximale Beschik baarheid voor webserver moet u ten minste twee Web Application servers implementeren voor het beheren van redundantie en taak verdeling. In azure kunnen deze Web Application servers worden geplaatst in beschikbaarheids sets of beschikbaarheids zones voor een betere Beschik baarheid.

  Tomcat is de standaard webtoepassing voor SAP BI-platform. Voor een hoge Beschik baarheid voor Tomcat, moet u sessie replicatie inschakelen met behulp van [statische Membership-Interceptor](https://tomcat.apache.org/tomcat-9.0-doc/config/cluster-membership.html#Static_Membership_Attributes) in Azure. Zo zorgt u ervoor dat de gebruiker toegang kan krijgen tot de SAP BI-webtoepassing, zelfs wanneer de Tomcat-service wordt onderbroken.

  > [!Important]
  > Tomcat maakt standaard gebruik van multi cast-IP en poort voor clustering die niet wordt ondersteund in azure (SAP-notitie [2764907](https://launchpad.support.sap.com/#/notes/2764907)).

- BI-platform servers

  BI-platform servers bevatten alle services die deel uitmaken van de SAP BOBI-toepassing (Management-laag, verwerkings laag en opslaglaag). Wanneer een webserver een aanvraag ontvangt, detecteert deze elke BI-platform server (met name alle CMS-servers in een cluster) en worden de aanvragen automatisch gebalanceerd. Als een van de BI-platformen mislukt, verzendt webserver automatisch aanvragen naar een andere host.

  Voor een hoge Beschik baarheid of redundantie voor het BI-platform moet u de toepassing implementeren in ten minste twee virtuele machines van Azure. Op basis van de grootte kunt u uw BI-platform schalen om uit te voeren op meer virtuele Azure-machines.

- File Repository Server (FRS)

  File Repository Server bevat alle rapporten en andere BI-documenten die zijn gemaakt. In implementatie met meerdere instanties worden BI-platform servers uitgevoerd op meerdere virtuele machines en moet elke virtuele machine toegang hebben tot deze rapporten en andere BI-documenten. Een bestands systeem moet dus worden gedeeld op alle BI-platform servers.

  In azure kunt u [Azure Premium-bestanden](../../../storage/files/storage-files-introduction.md) of [Azure NetApp files](../../../azure-netapp-files/azure-netapp-files-introduction.md) voor de bestands opslagplaats server gebruiken. Beide Azure-Services hebben ingebouwde redundantie.

  > [!Important]
  > Het SMB-protocol voor Azure Files is algemeen beschikbaar, maar de NFS-protocol ondersteuning voor Azure Files is momenteel in de preview-versie. Zie voor meer informatie [NFS 4,1-ondersteuning voor Azure files is nu](https://azure.microsoft.com/en-us/blog/nfs-41-support-for-azure-files-is-now-in-preview/) beschikbaar als preview-versie

- CMS-& controle database
  
  Voor het SAP BOBI-platform is een Data Base vereist om de systeem gegevens op te slaan. deze worden aangeduid als CMS-data base. Het wordt gebruikt voor het opslaan van BI-platform gegevens, zoals gebruikers-, Server-, map-, document-, configuratie-en verificatie gegevens.

  Azure biedt een [MySQL-data](https://azure.microsoft.com/en-us/services/mysql/) base en [Azure SQL database](https://azure.microsoft.com/en-us/services/sql-database/) data base-as-a-Service (DBaaS) die kan worden gebruikt voor CMS-data base en-controle database. Als u een PaaS-aanbieding hebt, hoeven klanten zich geen zorgen te maken over de werking, Beschik baarheid en het onderhoud van de data bases. De klant kan ook hun eigen Data Base voor CMS en audit opslagplaats kiezen op basis van hun bedrijfs behoeften.

## <a name="support-matrix"></a>Ondersteuningsmatrix

In deze sectie wordt de ondersteunings functie beschreven van verschillende SAP BOBI-onderdelen, zoals SAP BusinessObjects BI-platform versie, besturings systeem en data bases in Azure.

### <a name="sap-businessobjects-bi-platform"></a>SAP BusinessObjects BI-platform

Met Azure Infrastructure as a Service (IaaS) kunt u SAP BusinessObjects BI-platform implementeren en configureren op Azure compute. De volgende versie van het SAP BOBI-platform wordt ondersteund.

- SAP BusinessObjects BI-platform 4,3
- SAP BusinessObjects BI-platform 4,2 SP04 +
- SAP BusinessObjects BI-platform 4,1 SP05 +

Het SAP BI-platform wordt uitgevoerd op verschillende besturings systemen en data bases. Ondersteuning van het SAP BOBI-platform tussen het besturings systeem en de database versie vindt u in de [product beschikbaarheids matrix](https://apps.support.sap.com/sap/support/pam) voor SAP BOBI.

### <a name="operating-system"></a>Besturingssysteem

Azure biedt ondersteuning voor de volgende besturings systemen voor de implementatie van SAP BusinessObjects BI-platforms.

- Microsoft Windows Server
- SUSE Linux Enterprise Server (SLES)
- Red Hat Enterprise Linux (RHEL)
- Oracle Linux (OL)

De versie van het besturings systeem die wordt vermeld in de [Product Availability matrix (PAM) voor het SAP BUSINESSOBJECTS BI-platform](https://support.sap.com/pam) , wordt ondersteund zolang ze compatibel zijn om te worden uitgevoerd op de Azure-infra structuur.

### <a name="databases"></a>Databases

Het BI-platform vereist Data Base voor CMS en audit gegevens opslag, dat kan worden geïnstalleerd op alle ondersteunde data bases die worden vermeld in de [SAP-product beschikbaarheids matrix](https://support.sap.com/pam) die de volgende bevat:

- Microsoft SQL Server

- [Azure SQL database](https://azure.microsoft.com/en-us/services/sql-database/) (alleen ondersteunde Data Base voor het SAP BOBI-platform in Windows)

  Het is een volledig beheerde SQL Server data base-engine, gebaseerd op de nieuwste stabiele Enter prise-editie van SQL Server. Azure SQL database verwerkt de meeste database beheer functies, zoals upgrades, patches en bewaking zonder tussen komst van de gebruiker. U kunt met Azure SQL Database een maximaal beschikbare gegevensopslaglaag met hoge prestaties maken voor de apps en oplossingen in Azure. Raadpleeg [Azure SQL database](../../../azure-sql/azure-sql-iaas-vs-paas-what-is-overview.md) -documentatie voor meer informatie.

- [Azure database for MySQL](https://azure.microsoft.com/en-us/services/mysql/) (volg dezelfde compatibiliteits richtlijnen als vermeld in MySQL AB in SAP pam)

  Het is een relationele database service die is ingeschakeld door de MySQL Community Edition. Het is een volledig beheerde DBaaS-aanbieding (data base-as-a-Service), die bedrijfs kritieke workloads met voorspel bare prestaties en dynamische schaal baarheid kan verwerken. Het heeft ingebouwde hoge Beschik baarheid, automatische back-ups, software patches, automatische fout detectie en herstel naar een bepaald tijdstip voor Maxi maal 35 dagen, waardoor de bewerkings taken aanzienlijk worden verkleind. Raadpleeg [Azure database for MySQL](../../../mysql/overview.md) -documentatie voor meer informatie.

- SAP HANA

- SAP ASE

- IBM DB2

- Oracle (voor versie en beperking Raadpleeg SAP-opmerking [2039619](https://launchpad.support.sap.com/#/notes/2039619))

- MaxDB

Dit document illustreert de richt lijnen voor het implementeren van het **SAP BOBI-platform in Windows met Azure SQL database** en **SAP BOBI platform op Linux met Azure database for MySQL**. Het is ook de aanbevolen benadering voor het uitvoeren van het SAP BusinessObjects BI-platform op Azure.

## <a name="sizing"></a>Grootte aanpassen

Grootte is een proces voor het bepalen van de vereiste hardware om de toepassing efficiënt uit te voeren. Voor het SAP BOBI-platform moet de grootte worden gewijzigd met behulp van het SAP-formaatset [snelle grootte](https://www.sap.com/about/benchmark/sizing.quick-sizer.html#quick-sizer). Het hulp programma biedt de SAP'S op basis van de invoer, die vervolgens moet worden toegewezen aan de typen gecertificeerde virtuele machines van Azure voor SAP. SAP Note [1928533](https://launchpad.support.sap.com/#/notes/1928533) bevat een lijst met ondersteunde SAP-producten en Azure VM-typen samen met de sap's. Raadpleeg voor meer informatie over het aanpassen van de grootte de [SAP BI-formaat gids](https://wiki.scn.sap.com/wiki/display/BOBJ/Sizing+and+Deploying+SAP+BusinessObjects+BI+4.x+Platform+and+Add-Ons).

Voor opslag behoeften voor het SAP BOBI-platform biedt Azure verschillende soorten [Managed disks](../../managed-disks-overview.md). Voor de SAP BOBI-installatiemap kunt u het beste Premium Managed Disk gebruiken en voor de data base die wordt uitgevoerd op virtuele machines, de richt lijnen volgen die zijn opgenomen in de [DBMS-implementatie voor SAP-workload](dbms_guide_general.md).

Azure ondersteunt twee DBaaS-aanbiedingen voor SAP BOBI platform data tier- [Azure SQL database](https://azure.microsoft.com/en-us/services/sql-database) (BI-toepassing die wordt uitgevoerd op Windows) en [Azure database for MySQL](https://azure.microsoft.com/en-us/services/mysql) (BI-toepassing die wordt uitgevoerd op Linux en Windows). Op basis van het formaat resultaat kunt u het aankoop model kiezen dat het beste bij uw behoeften past.

> [!Tip]
> Overweeg 800 SAP'S = 1 vCPU bij het koppelen van het SAP'S-resultaat van de SAP BOBI platform-database tier naar Azure data base-as-a-Service (Azure SQL Database of Azure Database for MySQL) voor naslag informatie over snelle grootte.

### <a name="sizing-models-for-azure-sql-database"></a>Formaat modellen voor Azure SQL database

Azure SQL Database biedt de volgende drie aankoop modellen:

- op basis van vCore

  Hiermee kunt u het aantal vCores, de hoeveelheid geheugen en de hoeveelheid en snelheid van de opslag kiezen. Met het op vCore gebaseerde aankoopmodel kunt u ook [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) gebruiken voor SQL Server, om kosten te besparen. Dit model is geschikt voor klanten die flexibiliteit, controle en transparantie hebben.

  Er zijn drie [Opties](../../../azure-sql/database/service-tiers-vcore.md#service-tiers) voor de servicelaag beschikbaar in vCore-model met-Algemeen, bedrijfskritiek en grootschalige. De servicelaag definieert de opslag architectuur, ruimte, I/O-limieten en opties voor bedrijfs continuïteit die betrekking hebben op Beschik baarheid en herstel na nood gevallen. Hieronder vindt u details op hoog niveau voor elke optie van de servicelaag-

  1. **Algemeen** servicelaag is het meest geschikt voor zakelijke workloads. Het biedt budget gerichte, evenwichtige en schaal bare reken-en opslag opties. Raadpleeg [resource-opties en-limieten](../../../azure-sql/database/resource-limits-vcore-single-databases.md#general-purpose---provisioned-compute---gen5)voor meer informatie.
  2. **Bedrijfskritiek** service tier biedt zakelijke toepassingen de hoogste flexibiliteit voor storingen met behulp van verschillende geïsoleerde replica's en biedt de hoogste I/O-prestaties per database replica. Raadpleeg [resource-opties en-limieten](../../../azure-sql/database/resource-limits-vcore-single-databases.md#business-critical---provisioned-compute---gen5)voor meer informatie.
  3. **Grootschalige** is het meest geschikt voor zakelijke workloads met zeer schaal bare opslag-en lees-en schaal vereisten. Het biedt hogere flexibiliteit voor storingen door de configuratie van meer dan één geïsoleerde database replica toe te staan. Raadpleeg [resource-opties en-limieten](../../../azure-sql/database/resource-limits-vcore-single-databases.md#hyperscale---provisioned-compute---gen5)voor meer informatie.

- Op basis van DTU

  Het op DTU gebaseerde aankoop model biedt een combi natie van reken-, geheugen-en I/O-resources in drie service lagen, ter ondersteuning van lichte en zware data base-workloads. De verschillen in rekenkracht binnen elke laag bieden een andere combinatie van deze resources, waaraan u extra opslagresources kunt toevoegen. Het is het meest geschikt voor klanten die eenvoudige, vooraf geconfigureerde resource opties nodig hebben.

  [Service lagen](../../../azure-sql/database/service-tiers-dtu.md#compare-the-dtu-based-service-tiers) in het op DTU gebaseerde aankoop model worden gedifferentieerd met een bereik van reken grootten met een vaste hoeveelheid inbegrepen opslag, een vaste Bewaar periode van back-ups en vaste prijzen.

- Serverloos

  Bij het serverloze model wordt rekenkracht automatisch geschaald op basis van de vraag naar workload. Ook worden er kosten in rekening gebracht voor de hoeveelheid rekenkracht die per seconde wordt gebruikt. De compute-laag zonder server onderbreekt automatisch data bases tijdens inactieve Peri Oden wanneer alleen opslag wordt gefactureerd en hervat automatisch data bases wanneer de activiteit wordt geretourneerd. Raadpleeg [resource-opties en-limieten](../../../azure-sql/database/resource-limits-vcore-single-databases.md#general-purpose---serverless-compute---gen5)voor meer informatie.
  
  Het is geschikt voor onregelmatige, onvoorspelbaar gebruik met een laag gemiddeld reken gebruik gedurende een bepaalde periode. Dit model kan dus worden gebruikt voor niet-productie SAP BOBI-implementatie.

> [!Note]
> Voor SAP BOBI is het handig om vCore gebaseerd model te gebruiken en kies Algemeen of Bedrijfskritiek servicelaag op basis van de bedrijfs behoeften.

### <a name="sizing-models-for-azure-database-for-mysql"></a>Formaat modellen voor Azure data base for MySQL

Azure Database for MySQL wordt geleverd met drie verschillende prijs categorieën. Ze worden gedifferentieerd met de hoeveelheid Compute in vCores, het geheugen per vCore en de opslag technologie die wordt gebruikt om de datum op te slaan. Hieronder vindt u de details op hoog niveau voor de opties en raadpleegt u de [prijs categorie](../../../mysql/concepts-pricing-tiers.md) voor Azure database for MySQL voor meer informatie over andere kenmerken.

- Basic

  Het wordt gebruikt voor de doel werkbelastingen waarvoor lichte reken kracht en I/O-prestaties zijn vereist.

- Algemeen gebruik

  Het is geschikt voor de meeste werk belastingen die evenwichtige reken kracht en geheugen met schaal bare I/O-door Voer vereisen.

- Geoptimaliseerd geheugen

  Voor data bases met hoge prestaties die de prestaties in het geheugen vereisen voor een snellere transactie verwerking en hogere gelijktijdigheid.

> [!Note]
> Voor SAP BOBI is het handig om Algemeen of een prijs categorie geoptimaliseerd voor geheugen te gebruiken op basis van de zakelijke werk belasting.

## <a name="azure-resources"></a>Azure-resources

### <a name="choosing-regions"></a>Regio's kiezen

Azure-regio is een verzameling gegevens centra die de infra structuur bevat die moet worden uitgevoerd en die als host fungeert voor verschillende Azure-Services. Deze infra structuur bevat een groot aantal knoop punten die als reken knooppunten of opslag knooppunten functioneren of netwerk functionaliteit uitvoeren. Niet alle regio's bieden dezelfde services.

SAP BI-platform bevat verschillende onderdelen waarvoor specifieke VM-typen, opslag, zoals Azure Files, of Azure NetApp Files of Data Base als een service (DBaaS) voor de gegevenslaag mogelijk niet beschikbaar zijn in bepaalde regio's. U vindt de exacte informatie over VM-typen, Azure Storage typen of andere Azure-Services in [producten die beschikbaar zijn per regio](https://azure.microsoft.com/en-us/global-infrastructure/services/) site. Als uw SAP-systemen al op Azure worden uitgevoerd, hebt u waarschijnlijk uw regio geïdentificeerd. In dat geval moet u eerst onderzoeken of de benodigde services beschikbaar zijn in die regio's om de architectuur van het SAP BI-platform te bepalen.

### <a name="availability-zones"></a>Beschikbaarheidszones

Beschikbaarheidszones zijn fysiek gescheiden locaties binnen een Azure-regio. Elke beschikbaarheids zone bestaat uit een of meer data centers die zijn uitgerust met onafhankelijke voeding, koeling en netwerken.

Voor een hoge Beschik baarheid van elke laag voor het SAP BI-platform kunt u Vm's distribueren over de beschikbaarheids zone door een Framework met hoge Beschik baarheid te implementeren. Dit kan de beste SLA in azure bieden. Voor de SLA van de virtuele machine in azure, controleert u de meest recente versie van de service overeenkomst van de [virtuele machine](https://azure.microsoft.com/support/legal/sla/virtual-machines/).

Voor een gegevenslaag biedt Azure data base as a Service-Service (DBaaS) standaard een Framework met hoge Beschik baarheid. U hoeft alleen de regio en de service inherente hoge Beschik baarheid, redundantie en tolerantie mogelijkheden te selecteren om de uitval tijd van de data base van geplande en ongeplande storingen te beperken, zonder dat u extra onderdelen hoeft te configureren. Voor meer informatie over de SLA voor ondersteunde DBaaS-aanbiedingen op Azure, controleert u de [hoge Beschik baarheid in azure database for MySQL](../../../mysql/concepts-high-availability.md) en [hoge Beschik baarheid voor Azure SQL database](../../../azure-sql/database/high-availability-sla.md).

### <a name="availability-sets"></a>Beschikbaarheidssets

De beschikbaarheidsset is een logische groeperings mogelijkheid voor het isoleren van virtuele machine resources (VM) van elkaar op het moment dat deze wordt geïmplementeerd. Azure zorgt ervoor dat de Vm's die u in een Beschikbaarheidsset plaatst, worden uitgevoerd op meerdere fysieke servers, reken rekken, opslag eenheden en netwerk switches. Als er een hardware-of software fout optreedt, is dit alleen van invloed op een subset van uw virtuele machines. de algehele oplossing blijft operationeel. Wanneer virtuele machines in beschikbaarheids sets worden geplaatst, distribueert Azure Fabric-controller de Vm's over verschillende [fout](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/virtual-machines/workloads/sap/planning-guide.md#fault-domains) -en [upgrade](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/virtual-machines/workloads/sap/planning-guide.md#upgrade-domains) domeinen om te voor komen dat alle vm's ontoegankelijk zijn vanwege onderhoud van de infra structuur of het mislukken binnen één fout domein.

Het SAP BI-platform bevat veel verschillende onderdelen en tijdens het ontwerpen van de architectuur moet u er zeker van zijn dat elk van deze onderdelen robuust is tegen onderbrekingen. Dit kan worden bereikt door Azure virtual machines van elk onderdeel binnen beschikbaarheids sets te plaatsen. Wanneer u virtuele machines van verschillende VM-families in één beschikbaarheidsset samenvoegt, is het mogelijk dat u problemen ondervindt waardoor u een bepaald VM-type niet kunt opnemen in deze beschikbaarheidsset. Voor een webtoepassing heeft de BI-toepassing voor het SAP BI-platform dus als gemarkeerd in architectuur overzicht.

Ook het aantal update-en fout domeinen dat kan worden gebruikt door een Azure-Beschikbaarheidsset binnen een Azure-schaal eenheid is eindig. Dus als u virtuele machines toevoegt aan een enkele beschikbaarheidsset, zullen twee of meer Vm's uiteindelijk eindigen op hetzelfde fout-of update domein. Zie de sectie [Azure Availability sets](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/virtual-machines/workloads/sap/planning-guide.md#azure-availability-sets) van de Azure virtual machines planning and implementation for SAP document voor meer informatie.

Lees [Beschik baarheid beheren](../../manage-availability.md) voor meer informatie over het concept van Azure-beschikbaarheids sets en de manier waarop beschikbaarheids sets zijn gekoppeld aan fout-en upgrade domeinen.

> [!Important]
> De concepten van Azure-beschikbaarheidszones-en Azure-beschikbaarheids sets sluiten elkaar wederzijds uit. Dit betekent dat u een paar of meerdere Vm's kunt implementeren in een specifieke beschikbaarheids zone of een Azure-beschikbaarheidsset. Maar niet beide.

### <a name="virtual-machines"></a>Virtuele machines

Azure virtual machine is een service aanbieding waarmee u aangepaste installatie kopieën naar Azure kunt implementeren als IaaS-exemplaren (Infrastructure-as-a-Service). Het vereenvoudigt het onderhoud en de exploitatie van toepassingen door berekening en opslag op aanvraag te bieden om webtoepassingen en verbonden toepassingen te hosten, te schalen en te beheren.

Azure biedt varianten van virtuele machines voor al uw toepassings behoeften. Voor SAP-workload heeft Azure echter de selectie beperkt tot verschillende VM-families die geschikt zijn voor SAP-workload en SAP HANA workload specifiek. Raadpleeg voor meer inzicht [welke SAP-software wordt ondersteund voor Azure-implementaties](sap-supported-product-on-azure.md).

Op basis van de grootte van het SAP BI-platform moet u uw vereiste toewijzen aan de virtuele machine van Azure, die wordt ondersteund in azure voor SAP-product. SAP Note [1928533](https://launchpad.support.sap.com/#/notes/1928533) is een goed uitgangs punt dat ondersteunde Azure VM-typen voor SAP-producten in Windows en Linux vermeldt. Daarnaast moet u er ook voor zorgen dat deze VM-typen in een bepaalde regio beschikbaar zijn, ook als dat niet het enige type wordt ondersteund. U kunt de beschik baarheid van VM-type controleren op de pagina [beschik bare producten per regio](https://azure.microsoft.com/global-infrastructure/services/) . Voor het kiezen van het prijs model kunt u verwijzen naar [Azure virtual machines voor SAP-workload](planning-guide.md#azure-virtual-machines-for-sap-workload)

### <a name="storage"></a>Storage

Azure Storage is een door Azure beheerde Cloud service die opslag biedt die hoge Beschik baarheid, veilig, duurzaam, schaalbaar en redundant is. Sommige opslag typen hebben een beperkt gebruik voor SAP-scenario's. Maar verschillende Azure Storage typen zijn goed geschikt of geoptimaliseerd voor specifieke SAP-werkbelasting scenario's. Raadpleeg voor meer informatie [Azure Storage typen voor SAP-werkbelasting](planning-guide-storage.md) gids, omdat deze verschillende opslag opties markeert die geschikt zijn voor SAP.

Azure Storage heeft verschillende opslag typen die beschikbaar zijn voor klanten en informatie voor hetzelfde type kan worden gelezen in het artikel [welke schijf typen beschikbaar zijn in azure?](../../disks-types.md). SAP BOBI-platform gebruikt de volgende Azure Storage om de toepassing te bouwen:

- Door Azure beheerde schijven

  Het is een opslag volume op blok niveau dat wordt beheerd door Azure. U kunt de schijven voor SAP BOBI platform-toepassings servers en-data bases gebruiken wanneer deze zijn geïnstalleerd op virtuele machines van Azure. Er zijn verschillende typen [Azure-Managed disks](../../managed-disks-overview.md) beschikbaar, maar het is raadzaam [Premium ssd's](../../disks-types.md#premium-ssd) voor de toepassing en data base van het SAP BOBI-platform te gebruiken.

  In het onderstaande voor beeld worden Premium Ssd's gebruikt voor de BOBI-platform installatie directory. Voor de data base die op de virtuele machine is geïnstalleerd, kunt u met behulp van de richt lijnen beheerde schijven gebruiken voor de gegevens en het logboek volume. CMS-en audit databases zijn doorgaans klein en hebben niet dezelfde vereisten voor opslag prestaties als die van andere SAP OLTP/OLAP-data bases.

- Azure Premium-bestanden of-Azure NetApp Files

  In het SAP BOBI-platform verwijst File Repository Server (FRS) naar de schijf mappen waar inhoud zoals rapporten, universums en verbindingen worden opgeslagen die worden gebruikt door alle toepassings servers van dat systeem. [Azure Premium-bestanden](../../../storage/files/storage-files-introduction.md) of opslag van [Azure NetApp files](../../../azure-netapp-files/azure-netapp-files-introduction.md) kunnen worden gebruikt als een gedeeld BESTANDS systeem voor SAP BOBI-toepassingen FRS. Omdat deze opslag aanbieding niet beschikbaar is in alle regio's, raadpleegt u de site [beschik bare producten per regio](https://azure.microsoft.com/en-us/global-infrastructure/services/) om actuele informatie te vinden.

  Als de service niet beschikbaar is in uw regio, kunt u een NFS-server maken van waaruit u het bestands systeem kunt delen met de SAP BOBI-toepassing. Maar u moet ook rekening houden met de hoge Beschik baarheid.

![Opslag indeling voor SAP BusinessObjects BI-platform op Azure](media/businessobjects-deployment-guide/businessobjects-storage-layout.png)

### <a name="networking"></a>Netwerken

SAP BOBI is een rapportage-en analyse BI-platform dat geen zakelijke gegevens bevat. Het systeem is dus verbonden met andere database servers waar het alle gegevens ophaalt en inzicht biedt in gebruikers. Azure biedt een netwerk infrastructuur waarmee de toewijzing van alle scenario's kan worden gerealiseerd met het SAP BI-platform, zoals het maken van verbinding met een on-premises systeem, systemen in een ander virtueel netwerk en andere. Raadpleeg voor meer informatie [Microsoft Azure netwerken voor SAP-workload](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/virtual-machines/workloads/sap/planning-guide.md#microsoft-azure-networking).

Voor een data base-as-a-service-aanbieding heeft elke nieuw gemaakte data base (Azure SQL Database of Azure Database for MySQL) een firewall waarmee alle externe verbindingen worden geblokkeerd. Als u toegang wilt verlenen tot de DBaaS-service van virtuele machines van het BI-platform, moet u een of meer firewall regels op server niveau opgeven om toegang tot uw DBaaS-server mogelijk te maken. Zie voor meer informatie [firewall regels](../../../mysql/concepts-firewall-rules.md) voor Azure database for MySQL en de sectie [netwerk toegangs beheer](../../../azure-sql/database/network-access-controls-overview.md) voor Azure SQL database.

## <a name="next-steps"></a>Volgende stappen

- [Implementatie van SAP BusinessObjects BI-platform op Linux](businessobjects-deployment-guide-linux.md)
- [Azure Virtual Machines planning en implementatie voor SAP](planning-guide.md)
- [Azure Virtual Machines-implementatie voor SAP](deployment-guide.md)
- [Azure Virtual Machines DBMS-implementatie voor SAP](dbms-guide.md)