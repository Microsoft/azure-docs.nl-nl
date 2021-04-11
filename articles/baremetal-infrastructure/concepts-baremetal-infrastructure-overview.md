---
title: Overzicht van de BareMetal-infra structuur op Azure
description: Biedt een overzicht van de BareMetal-infra structuur op Azure.
ms.custom: references_regions
ms.topic: conceptual
ms.subservice: workloads
ms.date: 04/08/2021
ms.openlocfilehash: 7a4998a096a5c5d9e793c34d5046dce59262a2ae
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2021
ms.locfileid: "107257565"
---
#  <a name="what-is-baremetal-infrastructure-on-azure"></a>Wat is een BareMetal-infra structuur op Azure?

Microsoft Azure biedt een Cloud infrastructuur met een breed scala aan geïntegreerde Cloud Services om te voldoen aan de behoeften van uw bedrijf. In sommige gevallen moet u echter mogelijk services uitvoeren op bare-metal servers zonder een virtualisatieserver. U hebt mogelijk toegang tot het hoofd en controle over het besturings systeem (OS) nodig. Azure biedt een BareMetal-infra structuur voor verschillende hoogwaardige en bedrijfsspecifieke toepassingen om aan een dergelijke behoefte te voldoen.

De BareMetal-infra structuur bestaat uit specifieke BareMetal-instanties (reken instanties), hoge prestaties en toepassings geschikte opslag (NFS, ISCSI en Fiber Channel), evenals een set functie-specifieke virtuele Lan's (VLAN'S) in een geïsoleerde omgeving. Opslag kan worden gedeeld tussen BareMetal-instanties, zodat functies zoals scale-out clusters of voor het maken van paren met hoge Beschik baarheid met STONITH worden ingeschakeld.
 
Deze omgeving heeft ook speciale VLAN'S die u kunt gebruiken als u virtuele machines (Vm's) uitvoert op een of meer virtuele Azure-netwerken (VNets) in uw Azure-abonnement. De volledige omgeving wordt weer gegeven als een resource groep in uw Azure-abonnement.

De BareMetal-infra structuur wordt aangeboden in meer dan 30 Sku's van 2 tot 24 socket servers en geheugen variërend van 1,5 TB tot 24 TBs. Er is ook een grote set Sku's beschikbaar met Octane-geheugen. Azure biedt het grootste aantal bare metal-instanties in een grootschalige-Cloud.

## <a name="why-baremetal-infrastructure"></a>Waarom BareMetal-infra structuur?  

Sommige centrale werk belastingen in de onderneming bestaan uit technologieën die alleen niet zijn ontworpen om te worden uitgevoerd in een typische gevirtualiseerde Cloud instelling. Hiervoor is een speciale architectuur, gecertificeerde hardware of buiten gewoon grote grootten vereist. Hoewel deze technologieën de meest geavanceerde functies voor gegevens bescherming en bedrijfs continuïteit hebben, zijn deze functies niet gebouwd voor de gevirtualiseerde Cloud. Ze zijn gevoeliger voor latentie, ruis op de neighbors en vereisen veel meer controle over het beheer van wijzigingen en onderhouds activiteiten.

De BareMetal-infra structuur is gebouwd, gecertificeerd en getest voor een select set van dergelijke toepassingen. Azure was het eerst om dergelijke oplossingen aan te bieden en is sinds de grootste portefeuille en de meest geavanceerde systemen.

De BareMetal-infra structuur biedt de volgende voor delen: 

- Toegewezen instanties
- Gecertificeerde hardware voor gespecialiseerde workloads
    - SAP (Raadpleeg [SAP Note #1928533](https://launchpad.support.sap.com/#/notes/1928533))
    - Oracle (Raadpleeg de [Oracle-document-ID #948372.1](https://support.oracle.com/epmos/faces/DocumentDisplay?_afrLoop=52088246571495&id=948372.1&_adf.ctrl-state=kwnkj1hzm_52))
- Bare-metal (geen Compute-virtualisatie)
- Lage latentie tussen door Azure gehoste toepassings-Vm's naar BareMetal-instanties (0,35 MS)
- Alle Flash SSD en NVMe
    - Maxi maal 1 PB/Tenant 
    - IOPS tot 1,2 miljoen per Tenant 
    - 40/100 GB netwerk bandbreedte
    - Toegankelijk via NFS, ISCSI en FC
- Redundante voeding, voeding, Nic's, TORs, poorten, Wan's, opslag en beheer
- Hot spares voor vervanging bij een storing (zonder dat de configuratie opnieuw hoeft te worden geconfigureerd)
- Windows-gecoördineerde onderhouds Vensters
- Toepassings bewuste moment opnamen, archiveren, spie gelen en klonen


## <a name="sku-availability-in-azure-regions"></a>SKU-Beschik baarheid in azure-regio's

De BareMetal-infra structuur biedt meerdere Sku's die zijn gecertificeerd voor gespecialiseerde workloads. Gebruik de workload-specifieke Sku's om te voldoen aan uw behoeften.

- Grote instanties: variërend van twee sockets tot vier socket systemen.  
- Zeer grote instanties: variërend van vier sockets tot 20 socket systemen. 

De BareMetal-infra structuur voor gespecialiseerde workloads is beschikbaar in de volgende Azure-regio's:
- Europa -west
- Europa - noord
- Duitsland-west-centraal * zones ondersteunen
- Ondersteuning voor zones VS-Oost 2 *
- Ondersteuning voor zones VS Oost *
- Ondersteuning voor regio's vs-West *
- VS-West 2 * zones ondersteunen
- VS - zuid-centraal

>[!NOTE]
>**Zones ondersteunen** verwijzingen naar beschikbaarheids zones binnen een regio waar BareMetal-instanties kunnen worden geïmplementeerd in zones voor hoge tolerantie en beschik baarheid. Met deze mogelijkheid wordt ondersteuning geboden voor het schalen op meerdere locaties actief-actief.

## <a name="managing-baremetal-instances-in-azure"></a>BareMetal-instanties in azure beheren 

Afhankelijk van uw behoeften kunnen de Application-topologieën van de BareMetal-infra structuur complex zijn. U kunt meerdere exemplaren implementeren op een of meer locaties, met gedeelde of speciale opslag en gespecialiseerde LAN-en WAN-verbindingen. Voor een BareMetal-infra structuur biedt Azure echter een consultatieve vastleg ging van die informatie door een CSA/GBB in het veld in een inrichtings Portal. 

Op het moment dat uw BareMetal-infra structuur is ingericht, zijn het besturings systeem, netwerken, opslag volumes, plaatsingen in zones en regio's en WAN-verbindingen tussen locaties al vooraf geconfigureerd. U bent ingesteld om uw OS-licenties (BYOL) te registreren, het besturings systeem te configureren en de toepassingslaag te installeren.

U kunt alle bronnen van de BareMetal-infra structuur en hun status en kenmerken weer geven in de Azure Portal. U kunt ook de instanties en open service aanvragen en ondersteunings tickets van daaruit uitvoeren. 

## <a name="operational-model"></a>Operationeel model
BareMetal-infra structuur is ISO 27001, ISO 27017, SOC 1 en SOC 2-compatibel. Er wordt ook gebruikgemaakt van een BYOL-model (maken-your-own-License): besturings systeem, gespecialiseerde werk belasting en toepassingen van derden.  

Zodra u toegang krijgt tot de hoofdmap en volledig beheer, neemt u de volgende verantwoordelijkheid:
- Het ontwerpen en implementeren van back-up-en herstel oplossingen, hoge Beschik baarheid en herstel na nood gevallen.
- Licenties, beveiliging en ondersteuning voor het besturings systeem en de software van derden.

Micro soft is verantwoordelijk voor het volgende:
- Het leveren van de hardware voor gespecialiseerde workloads. 
- Het besturings systeem inrichten.

:::image type="content" source="media/concepts-baremetal-infrastructure-overview/baremetal-support-model.png" alt-text="Diagram van het BareMetal-model voor infrastructuur ondersteuning." border="false":::

## <a name="baremetal-instance-stamp"></a>BareMetal-instantie stempel

De BareMetal-instantie stempel zelf is een combi natie van de volgende onderdelen:

- **Computing:** Servers op basis van de generatie Intel Xeon-processors die de benodigde computer capaciteit bieden en zijn gecertificeerd voor de gespecialiseerde werk belasting.

- **Netwerk:** Een uniforme netwerk infrastructuur met hoge snelheid verbindt computer-, opslag-en LAN-onderdelen.

- **Opslag:** Een infra structuur die toegankelijk is via een uniforme netwerk infrastructuur.

Binnen de multi tenant-infra structuur van de BareMetal-stempel worden klanten geïmplementeerd in geïsoleerde tenants. Wanneer u een Tenant implementeert, moet u een Azure-abonnement binnen uw Azure-inschrijving benoemen. Dit Azure-abonnement is de versie die wordt gefactureerd voor uw BareMetal-instanties.

>[!NOTE]
>Een klant die een BareMetal-exemplaar implementeert, is geïsoleerd in een Tenant. Een Tenant wordt geïsoleerd in het netwerk, de opslag en de compute-laag van andere tenants. Opslag-en reken eenheden die aan verschillende tenants zijn toegewezen, kunnen elkaar niet zien of met elkaar communiceren in hun BareMetal-instanties.

## <a name="operating-system"></a>Besturingssysteem
Tijdens het inrichten van de BareMetal-instantie kunt u het besturings systeem selecteren dat u op de computers wilt installeren. 

>[!NOTE]
>Houd er rekening mee dat BareMetal Infrastructure een BYOL-model is.

De beschik bare versies van het Linux-besturings systeem zijn:
- Red Hat Enterprise Linux (RHEL)
- SUSE Linux Enterprise Server (SLES)

## <a name="storage"></a>Storage
De BareMetal-infra structuur biedt zeer redundante NFS-opslag en Fibre Channel-opslag. De infra structuur biedt een diep gaande integratie voor werk belastingen in de onderneming, zoals SAP, SQL en meer. Het biedt ook toepassings consistente gegevens bescherming en mogelijkheden voor gegevens beheer. De Self-Service beheer hulpprogramma's bieden ruimte-efficiënte moment opnamen, klonen en gedetailleerde replicatie mogelijkheden, samen met één deel venster van glas bewaking. De infra structuur maakt het mogelijk dat er geen RPO-en RTO-mogelijkheden zijn voor de beschik baarheid van gegevens en bedrijfs continuïteit. 

De opslag infrastructuur biedt:
- Maxi maal 4 x 100 GB uplinks.
- Maxi maal 32 GB Fibre Channel-uplinks.
- Alle Flash SSD en NVMe-schijf.
- Zeer lage latentie en hoge door voer.
- Schaalt Maxi maal 4 PB onbewerkte opslag. 
- Maxi maal 11.000.000 IOPS.

Deze protocollen voor gegevens toegang worden ondersteund: 
- iSCSI 
- NFS (v3 of v4) 
- Fibre Channel 
- NVMe via FC  

## <a name="networking"></a>Netwerken
De architectuur van Azure Network Services is een belang rijk onderdeel voor een geslaagde implementatie van gespecialiseerde workloads in BareMetal-instanties. Waarschijnlijk zijn niet alle IT-systemen in azure al aanwezig. Azure biedt u netwerk technologie waarmee Azure eruitziet als een virtueel Data Center naar uw on-premises software-implementaties. De Azure-netwerk functionaliteit die is vereist voor BareMetal-instanties omvat:

- Virtuele Azure-netwerken die zijn verbonden met het Azure ExpressRoute-circuit dat verbinding maakt met uw on-premises netwerk assets.
- Het ExpressRoute-circuit dat on-premises met Azure verbindt, moet een minimale band breedte van 1 Gbps of hoger hebben.
- Uitgebreide Active Directory en DNS in azure, of volledig uitgevoerd in Azure.

Met ExpressRoute kunt u uw on-premises netwerk uitbreiden naar de micro soft-Cloud via een particuliere verbinding met de hulp van een connectiviteits provider. U kunt **ExpressRoute lokaal** gebruiken voor rendabele gegevens overdracht tussen uw on-premises locatie en de Azure-regio die u wilt. Als u de connectiviteit tussen de geopolitieke grenzen wilt uitbreiden, kunt u **ExpressRoute Premium** inschakelen. 

BareMetal-exemplaren worden ingericht binnen het IP-adres bereik van uw Azure VNet-server.

:::image type="content" source="media/concepts-baremetal-infrastructure-overview/baremetal-infrastructure-diagram.png" alt-text="Architectuur diagram van het Azure BareMetal-infrastructuur diagram." lightbox="media/concepts-baremetal-infrastructure-overview/baremetal-infrastructure-diagram.png" border="false":::

De weer gegeven architectuur is onderverdeeld in drie secties:
- **Links:** Toont de on-premises-infra structuur van de klant die verschillende toepassingen uitvoert, verbinding maken via de partner of de lokale edge-router, zoals Equinix. Zie [connectiviteits providers en-locaties: Azure ExpressRoute](../expressroute/expressroute-locations.md)voor meer informatie.
- **Center:** Toont [ExpressRoute](../expressroute/expressroute-introduction.md) ingericht met behulp van uw Azure-abonnement waarmee verbinding wordt gemaakt met het Azure Edge-netwerk.
- **Rechts:** Toont Azure IaaS, en in dit geval gebruikt u Vm's voor het hosten van uw toepassingen, die zijn ingericht in uw virtuele Azure-netwerk.
- **Onder:** Geeft aan dat uw ExpressRoute-gateway is ingeschakeld met [ExpressRoute FastPath](../expressroute/about-fastpath.md) voor BareMetal-connectiviteit met lage latentie.   
   >[!TIP]
   >Ter ondersteuning hiervan moet uw ExpressRoute-gateway Ultra Performance zijn. Zie [over ExpressRoute virtuele netwerk gateways](../expressroute/expressroute-about-virtual-network-gateways.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

De volgende stap is informatie over het identificeren en gebruiken van BareMetal-instanties via de Azure Portal.

> [!div class="nextstepaction"]
> [BareMetal-exemplaren beheren via de Azure Portal](connect-baremetal-infrastructure.md)
