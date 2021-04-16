---
title: Wat is een BareMetal-infrastructuur in Azure?
description: Biedt een overzicht van de BareMetal-infrastructuur in Azure.
ms.custom: references_regions
ms.topic: conceptual
ms.subservice: workloads
ms.date: 04/14/2021
ms.openlocfilehash: c0fd250a63ce93d3f8b62dfe76fe753c928801ce
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107536891"
---
#  <a name="what-is-baremetal-infrastructure-on-azure"></a>Wat is een BareMetal-infrastructuur in Azure?

Microsoft Azure biedt een cloudinfrastructuur met een breed scala aan geïntegreerde cloudservices om te voldoen aan de behoeften van uw bedrijf. In sommige gevallen moet u echter mogelijk services uitvoeren op bare-metalservers zonder een virtualisatielaag. Mogelijk hebt u hoofdtoegang en controle over het besturingssysteem nodig. Om aan een dergelijke behoefte te voldoen, biedt Azure BareMetal Infrastructure voor verschillende hoogwaardige en bedrijfskritische toepassingen.

De BareMetal-infrastructuur bestaat uit toegewezen BareMetal-exemplaren (rekenincidenten), hoogwaardige en toepassingsspecifieke opslag (NFS, ISCSI en Fiber Channel), evenals een set functiespecifieke virtuele LAN's (VLAN's) in een geïsoleerde omgeving. Opslag kan worden gedeeld tussen BareMetal-exemplaren om functies in te schakelen, zoals uitschaalclusters of voor het maken van paren met hoge beschikbaarheid met STONITH.
 
Deze omgeving heeft ook speciale VLAN's die u kunt openen als u virtuele machines (VM's) op een of meer virtuele Azure-netwerken (VNets) in uw Azure-abonnement gebruikt. De hele omgeving wordt weergegeven als een resourcegroep in uw Azure-abonnement.

BareMetal Infrastructure wordt aangeboden in meer dan 30 SKU's van servers met 2 sockets tot 24 sockets en geheugen van 1,5 TB tot 24 TB. Er is ook een grote set SKU's beschikbaar met Het geheugen van Octne. Azure biedt het grootste bereik aan bare metal-exemplaren in een hyperscale-cloud.

## <a name="why-baremetal-infrastructure"></a>Waarom BareMetal-infrastructuur?  

Sommige centrale workloads in de onderneming zijn uit technologieën die alleen niet zijn ontworpen om te worden uitgevoerd in een typische gevirtualiseerde cloudomgeving. Ze vereisen speciale architectuur, gecertificeerde hardware of uitzonderlijk grote grootten. Hoewel deze technologieën de meest geavanceerde functies voor gegevensbeveiliging en bedrijfscontinuïteit hebben, zijn deze functies niet gebouwd voor de gevirtualiseerde cloud. Ze zijn gevoeliger voor latentie, ruis bij buren en vereisen veel meer controle over wijzigingsbeheer en onderhoudsactiviteiten.

BareMetal Infrastructure is gebouwd, gecertificeerd en getest voor een bepaalde set van dergelijke toepassingen. Azure was de eerste die dergelijke oplossingen aanbiedt en heeft sindsdien de leiding over het grootste portfolio en de meest geavanceerde systemen.

BareMetal Infrastructure biedt de volgende voordelen: 

- Toegewezen exemplaren
- Gecertificeerde hardware voor gespecialiseerde workloads
    - SAP (raadpleeg [SAP Note #1928533](https://launchpad.support.sap.com/#/notes/1928533))
    - Oracle (Raadpleeg [Oracle-document-id #948372.1](https://support.oracle.com/epmos/faces/DocumentDisplay?_afrLoop=52088246571495&id=948372.1&_adf.ctrl-state=kwnkj1hzm_52))
- Bare-metal (geen rekenvirtualisatie)
- Lage latentie tussen door Azure gehoste toepassings-VM's naar BareMetal-exemplaren (0,35 ms)
- Alle Flash SSD en NVMe
    - Maximaal 1 PB/tenant 
    - IOPS tot 1,2 miljoen/tenant 
    - 40/100 GB netwerkbandbreedte
    - Toegankelijk via NFS, ISCSI en FC
- Redundante voedingen, voedingen, NIC's, tor's, poorten, WAN's, opslag en beheer
- Hot spares voor vervanging bij een fout (zonder de noodzaak om opnieuw te configureren)
- Door de klant gecoördineerde onderhoudsvensters
- Toepassingsbewuste momentopnamen, archiveren, spiegelen en klonen


## <a name="baremetal-benefits"></a>BareMetal-voordelen  

BareMetal Infrastructure is bedoeld voor bedrijfskritieke workloads waarvoor certificering is vereist om uw bedrijfstoepassingen uit te voeren. De BareMetal-exemplaren zijn alleen aan u toegewezen en u hebt volledige toegang (hoofdtoegang) tot het besturingssysteem. U beheert de installatie van het besturingssysteem en de toepassing op basis van uw behoeften. Voor de beveiliging worden de exemplaren ingericht binnen uw Azure Virtual Network (VNet) zonder internetverbinding. Alleen services die worden uitgevoerd op uw virtuele machines (VM's) en andere Azure-services in hetzelfde laag 2-netwerk, kunnen communiceren met uw BareMetal-exemplaren.  

BareMetal Infrastructure biedt de volgende voordelen: 

- Gecertificeerde hardware voor gespecialiseerde workloads
- SAP (Raadpleeg [SAP Note #1928533](https://launchpad.support.sap.com/#/notes/1928533))
- Oracle (Raadpleeg [Oracle-document-id #948372.1](https://support.oracle.com/epmos/faces/DocumentDisplay?_afrLoop=52088246571495&id=948372.1&_adf.ctrl-state=kwnkj1hzm_52))
- Niet-hypervised BareMetal-exemplaar, eigendom van één tenant
- Lage latentie tussen door Azure gehoste toepassings-VM's naar BareMetal-exemplaren (0,35 ms)
- Alle Flash SSD- en NVMe-ondersteuning
- Database tot 1 PB/tenant 
- IOPS tot 1,2 miljoen/tenant 
- Netwerkbandbreedte van 50 GB 

## <a name="sku-availability-in-azure-regions"></a>Beschikbaarheid van SKU's in Azure-regio's

BareMetal Infrastructure biedt meerdere SKU's die zijn gecertificeerd voor gespecialiseerde workloads. Gebruik de workloadspecifieke SKU's om aan uw behoeften te voldoen.

- Grote exemplaren: variërend van systemen met twee sockets tot systemen met vier sockets.  
- Zeer grote exemplaren: variërend van systemen met vier sockets tot systemen van twintig sockets. 

De BareMetal-infrastructuur voor gespecialiseerde workloads is beschikbaar in de volgende Azure-regio's:
- Europa -west
- Europa - noord
- Duitsland - west-centraal *zones-ondersteuning
- Ondersteuning voor zones in VS - oost 2 *
- Ondersteuning voor *zones in VS - oost
- Ondersteuning voor *zones in VS - west
- Ondersteuning voor zones in VS - west 2 *
- VS - zuid-centraal

>[!NOTE]
>**Ondersteuning voor** zones verwijst naar beschikbaarheidszones binnen een regio waar BareMetal-exemplaren kunnen worden geïmplementeerd in zones voor hoge tolerantie en beschikbaarheid. Deze mogelijkheid maakt ondersteuning mogelijk voor multi-site actief-actief schalen.

## <a name="managing-baremetal-instances-in-azure"></a>BareMetal-exemplaren beheren in Azure 

Afhankelijk van uw behoeften kunnen de toepassings-topologies van BareMetal Infrastructure complex zijn. U kunt meerdere exemplaren implementeren, op een of meer locaties, met gedeelde of toegewezen opslag en gespecialiseerde LAN- en WAN-verbindingen. Voor de BareMetal-infrastructuur biedt Azure daarom een adviserende opname van die informatie door een CSA/GBB in het veld in een inrichtingsportal. 

Op het moment dat uw BareMetal-infrastructuur is ingericht, zijn het besturingssysteem, netwerken, opslagvolumes, plaatsingen in zones en regio's en WAN-verbindingen tussen locaties al vooraf geconfigureerd. U kunt uw besturingssysteemlicenties (BYOL) registreren, het besturingssysteem configureren en de toepassingslaag installeren.

U kunt alle BareMetal-resources en hun status en kenmerken in de Azure Portal. U kunt de exemplaren ook gebruiken en daar serviceaanvragen en ondersteuningstickets openen. 

## <a name="operational-model"></a>Operationeel model
BareMetal Infrastructure is conform ISO 27001, ISO 27017, SOC 1 en SOC 2. Er wordt ook een BYOL-model (Bring Your Own License) gebruikt: besturingssysteem, gespecialiseerde workload en toepassingen van derden.  

Zodra u toegang tot de hoofdmap en volledig beheer hebt, bent u verantwoordelijk voor:
- Het ontwerpen en implementeren van back-up- en hersteloplossingen, hoge beschikbaarheid en herstel na noodherstel.
- Licentieverlening, beveiliging en ondersteuning voor het besturingssysteem en software van derden.

Microsoft is verantwoordelijk voor:
- De hardware leveren voor gespecialiseerde workloads. 
- Het besturingssysteem inrichten.

:::image type="content" source="media/concepts-baremetal-infrastructure-overview/baremetal-support-model.png" alt-text="Diagram van het ondersteuningsmodel voor BareMetal Infrastructure." border="false":::

## <a name="baremetal-instance-stamp"></a>BareMetal-instantiestempel

De BareMetal-instantiestempel zelf combineert de volgende onderdelen:

- **Computing:** Servers op basis van het genereren van Intel Xeon-processors die de benodigde rekenmogelijkheden bieden en zijn gecertificeerd voor de gespecialiseerde workload.

- **Netwerk:** Een geïntegreerde snelle netwerk-fabric verbindt computer-, opslag- en LAN-onderdelen.

- **Opslag:** Een infrastructuur die toegankelijk is via een geïntegreerde netwerkinfrastructuur.

Binnen de infrastructuur met meerdere tenants van de BareMetal-zegel worden klanten geïmplementeerd in geïsoleerde tenants. Wanneer u een tenant implementeert, noemt u een Azure-abonnement binnen uw Azure-inschrijving. Dit Azure-abonnement wordt gefactureerd voor uw BareMetal-exemplaren.

>[!NOTE]
>Een klant die een BareMetal-exemplaar implementeert, wordt geïsoleerd in een tenant. Een tenant is geïsoleerd in de netwerk-, opslag- en rekenlaag van andere tenants. Opslag- en rekeneenheden die zijn toegewezen aan verschillende tenants kunnen elkaar niet zien of met elkaar communiceren op hun BareMetal-exemplaren.

## <a name="operating-system"></a>Besturingssysteem
Tijdens het inrichten van het BareMetal-exemplaar kunt u het besturingssysteem selecteren dat u op de machines wilt installeren. 

>[!NOTE]
>Vergeet niet dat BareMetal Infrastructure een BYOL-model is.

De beschikbare versies van het Linux-besturingssysteem zijn:
- Red Hat Enterprise Linux (RHEL)
- SUSE Linux Enterprise Server (SLES)

## <a name="storage"></a>Storage
BareMetal Infrastructure biedt uiterst redundante NFS-opslag en Fiber Channel-opslag. De infrastructuur biedt een diepe integratie voor zakelijke workloads zoals SAP, SQL en meer. Het biedt ook toepassings-consistente gegevensbeveiliging en mogelijkheden voor gegevensbeheer. De selfservicebeheerprogramma's bieden ruimte-efficiënte mogelijkheden voor momentopnamen, klonen en gedetailleerde replicatie, samen met één deelvenster bewaking. De infrastructuur maakt geen RPO- en RTO-mogelijkheden mogelijk voor gegevensbeschikbaarheid en bedrijfscontinuïteitsbehoeften. 

De opslaginfrastructuur biedt:
- Maximaal 4 x 100 GB uplinks.
- Maximaal 32 GB Fiber Channel-uplinks.
- Alle flash-SSD- en NVMe-schijven.
- Ultra lage latentie en hoge doorvoer.
- Schaalt tot 4 PB aan onbewerkte opslag. 
- Maximaal 11 miljoen IOPS.

Deze protocollen voor gegevenstoegang worden ondersteund: 
- iSCSI 
- NFS (v3 of v4) 
- Fiber Channel 
- NVMe via FC  

## <a name="networking"></a>Netwerken
De architectuur van Azure-netwerkservices is een belangrijk onderdeel voor een geslaagde implementatie van gespecialiseerde workloads in BareMetal-exemplaren. Waarschijnlijk bevinden niet alle IT-systemen zich al in Azure. Azure biedt netwerktechnologie waarmee Azure eruit ziet als een virtueel datacenter voor uw on-premises software-implementaties. De Azure-netwerkfunctionaliteit die is vereist voor BareMetal-exemplaren omvat:

- Virtuele Azure-netwerken die zijn verbonden met Azure ExpressRoute-circuit dat verbinding maakt met uw on-premises netwerkactiva.
- Het ExpressRoute-circuit dat on-premises verbinding maakt met Azure moet een minimale bandbreedte van 1 Gbps of hoger hebben.
- Uitgebreide Active Directory en DNS in Azure, of volledig uitgevoerd in Azure.

Met ExpressRoute kunt u uw on-premises netwerk uitbreiden naar de Microsoft-cloud via een privéverbinding met de hulp van een connectiviteitsprovider. U kunt **ExpressRoute Local gebruiken voor** kosteneffectieve gegevensoverdracht tussen uw on-premises locatie en de wante Azure-regio. Als u connectiviteit wilt uitbreiden buiten geopolitieke grenzen, kunt u **ExpressRoute Premium inschakelen.** 

BareMetal-exemplaren worden ingericht binnen het IP-adresbereik van uw Azure VNet-server.

:::image type="content" source="media/concepts-baremetal-infrastructure-overview/baremetal-infrastructure-diagram.png" alt-text="Architectuurdiagram van diagram van Azure BareMetal Infrastructure." lightbox="media/concepts-baremetal-infrastructure-overview/baremetal-infrastructure-diagram.png" border="false":::

De weergegeven architectuur is onderverdeeld in drie secties:
- **Links:** Toont de on-premises infrastructuur van de klant met verschillende toepassingen, en maakt verbinding via de partner of lokale edge-router, zoals Equinix. Zie Connectiviteitsproviders en [-locaties: Azure ExpressRoute.](../expressroute/expressroute-locations.md)
- **Center:** Toont [ExpressRoute die](../expressroute/expressroute-introduction.md) is ingericht met behulp van uw Azure-abonnement en connectiviteit biedt met het Azure Edge-netwerk.
- **Rechts:** Toont Azure IaaS en in dit geval het gebruik van VM's voor het hosten van uw toepassingen, die zijn ingericht in uw virtuele Azure-netwerk.
- **Onder:** Toont het gebruik van uw ExpressRoute-gateway die is ingeschakeld [met ExpressRoute FastPath](../expressroute/about-fastpath.md) voor BareMetal-connectiviteit met lage latentie.   
   >[!TIP]
   >Ter ondersteuning hiervoor moet uw ExpressRoute-gateway UltraPerformance zijn. Zie About [ExpressRoute virtual network gateways (Over virtuele ExpressRoute-netwerkgateways) voor meer informatie.](../expressroute/expressroute-about-virtual-network-gateways.md)

## <a name="next-steps"></a>Volgende stappen

De volgende stap is om te leren hoe u BareMetal-exemplaren kunt identificeren en ermee kunt werken via de Azure Portal.

> [!div class="nextstepaction"]
> [BareMetal-exemplaren beheren via de Azure Portal](connect-baremetal-infrastructure.md)
