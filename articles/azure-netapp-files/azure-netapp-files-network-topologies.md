---
title: Richt lijnen voor het plannen van Azure NetApp Files netwerken | Microsoft Docs
description: Hierin worden richt lijnen beschreven die u kunnen helpen bij het ontwerpen van een efficiënte netwerk architectuur met behulp van Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: ram-kakani
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: ramakk
ms.openlocfilehash: 96d8ba058a33d408ec2ee2a1adfba9011f393da9
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96184481"
---
# <a name="guidelines-for-azure-netapp-files-network-planning"></a>Richtlijnen voor Azure NetApp Files-netwerkplanning

Planning van de netwerk architectuur is een belang rijk onderdeel van het ontwerpen van elke toepassings infrastructuur. Dit artikel helpt u bij het ontwerpen van een efficiënte netwerk architectuur voor uw workloads om te profiteren van de uitgebreide mogelijkheden van Azure NetApp Files.

Azure NetApp Files volumes zijn ontworpen om te worden opgenomen in een speciaal subnet met de naam een [gedelegeerd subnet](../virtual-network/virtual-network-manage-subnet.md) binnen uw Azure Virtual Network. Daarom kunt u de volumes rechtstreeks vanuit uw VNet benaderen, van peered VNets in dezelfde regio, of van on-premises via een Virtual Network gateway (ExpressRoute of VPN Gateway), indien nodig. Het subnet is toegewezen aan Azure NetApp Files en er is geen verbinding met andere Azure-Services of Internet.

## <a name="considerations"></a>Overwegingen  

U moet rekening houden met enkele overwegingen bij het plannen van Azure NetApp Files netwerk.

### <a name="constraints"></a>Beperkingen

De onderstaande functies worden momenteel niet ondersteund voor Azure NetApp Files: 

* Netwerk beveiligings groepen (Nsg's) die zijn toegepast op het overgedragen subnet
* Door de gebruiker gedefinieerde routes (Udr's) die worden toegepast op het overgedragen subnet
* Azure-beleid (bijvoorbeeld aangepaste naamgevings beleid) op de Azure NetApp Files interface
* Load balancers voor Azure NetApp Files verkeer
* Azure Virtual WAN 
* Zone redundante Virtual Network gateways (gateway-Sku's met AZ) 
* Actieve/actieve Virtual Network GWs 
* VNet (IPv4 en IPv6) met dubbele stack

De volgende netwerk beperkingen zijn van toepassing op Azure NetApp Files:

* Het aantal Ip's dat in een VNet met Azure NetApp Files (inclusief peered VNets) wordt gebruikt, mag niet hoger zijn dan 1000. We zijn bezig om deze limiet te verhogen om tegemoet te komen aan de schaal vereisten van klanten. 
* In elk virtueel Azure-netwerk (VNet) kan er slechts één subnet aan Azure NetApp Files worden gedelegeerd.


### <a name="supported-network-topologies"></a>Ondersteunde netwerk topologieën

De volgende tabel beschrijft de netwerktopologieën die door Azure NetApp Files worden ondersteund.  Hierin worden ook de tijdelijke oplossingen voor de niet-ondersteunde topologieën beschreven. 

|    Topologieën    |    Wordt ondersteund    |     Tijdelijke oplossing    |
|-------------------------------------------------------------------------------------------------------------------------------|--------------------|-----------------------------------------------------------------------------|
|    Connectiviteit met volume in een lokaal VNet    |    Yes    |         |
|    Connectiviteit met volume in een gepeerd VNet (dezelfde regio)    |    Yes    |         |
|    Connectiviteit met volume in een gepeerd VNet (meerdere regio's of globale peering)    |    No    |    Geen    |
|    Connectiviteit met een volume via de ExpressRoute-gateway    |    Yes    |         |
|    Connectiviteit van on-premises naar een volume in een spoke-VNet via ExpressRoute gateway en VNet-peering met gateway-door Voer    |    Yes    |        |
|    Connectiviteit van on-premises naar een volume in een spoke-VNet via een VPN-gateway    |    Yes    |         |
|    Connectiviteit van on-premises naar een volume in een spoke-VNet via VPN-gateway en VNet-peering met gateway-door Voer    |    Yes    |         |


## <a name="virtual-network-for-azure-netapp-files-volumes"></a>Virtueel netwerk voor Azure NetApp Files volumes

In deze sectie worden de concepten uitgelegd die u helpen bij het plannen van virtuele netwerken.

### <a name="azure-virtual-networks"></a>Virtuele netwerken van Azure.

Voordat u een Azure NetApp Files-volume inricht, moet u een virtueel Azure-netwerk (VNet) maken of er een gebruiken dat al aanwezig is in uw abonnement. Het VNet definieert de netwerk grens van het volume.  Zie de [documentatie van Azure Virtual Network](../virtual-network/virtual-networks-overview.md)voor meer informatie over het maken van virtuele netwerken.

### <a name="subnets"></a>Subnetten

Subnetten segmenteert het virtuele netwerk in afzonderlijke adres ruimten die kunnen worden gebruikt door de Azure-resources.  Azure NetApp Files volumes bevinden zich in een subnet met speciale doel einden, een zogenaamde [gedelegeerd subnet](../virtual-network/virtual-network-manage-subnet.md). 

Subnet delegering biedt expliciete machtigingen voor de Azure NetApp Files-service om servicespecifieke bronnen in het subnet te maken.  Er wordt gebruikgemaakt van een unieke id bij het implementeren van de service. In dit geval wordt een netwerk interface gemaakt om verbinding met Azure NetApp Files mogelijk te maken.

Als u een nieuw VNet gebruikt, kunt u een subnet maken en het subnet delegeren naar Azure NetApp Files door de instructies in [een subnet naar Azure NetApp files te delegeren](azure-netapp-files-delegate-subnet.md). U kunt ook een bestaand leeg subnet delegeren dat nog niet is gedelegeerd aan andere services.

Als het VNet is gekoppeld aan een ander VNet, kunt u de VNet-adres ruimte niet uitbreiden. Daarom moet het nieuwe gedelegeerde subnet worden gemaakt binnen de VNet-adres ruimte. Als u de adres ruimte wilt uitbreiden, moet u de VNet-peering verwijderen voordat u de adres ruimte uitbreidt.

### <a name="udrs-and-nsgs"></a>Udr's en Nsg's

Door de gebruiker gedefinieerde routes (Udr's) en netwerk beveiligings groepen (Nsg's) worden niet ondersteund op gedelegeerde subnetten voor Azure NetApp Files. U kunt echter Udr's en Nsg's Toep assen op andere subnetten, zelfs binnen hetzelfde VNet als het subnet dat is overgedragen aan Azure NetApp Files.

* Udr's definieert vervolgens de verkeers stromen van de andere subnetten naar het Azure NetApp Files overgedragen subnet. Dit helpt ervoor te zorgen dat dit wordt uitgelijnd op de verkeers stroom van Azure NetApp Files naar de andere subnetten met behulp van de systeem routes.  
* Nsg's vervolgens het verkeer van en naar het overgedragen subnet Azure NetApp Files toestaan of weigeren. 

## <a name="azure-native-environments"></a>Systeem eigen Azure-omgevingen

Het volgende diagram illustreert een systeem eigen Azure-omgeving:

![Azure-systeem eigen netwerk omgeving](../media/azure-netapp-files/azure-netapp-files-network-azure-native-environment.png)

### <a name="local-vnet"></a>Lokaal VNet

Een basis scenario is het maken of verbinden van een Azure NetApp Files-volume van een virtuele machine (VM) in hetzelfde VNet. Voor VNet 2 in het bovenstaande diagram wordt volume 1 in een gedelegeerd subnet gemaakt en kan het worden gekoppeld op VM 1 in het standaard subnet.

### <a name="vnet-peering"></a>VNet-peering

Als er extra VNets in dezelfde regio zijn die toegang moeten hebben tot de resources van elkaar, kan de VNets worden verbonden met behulp van [VNet-peering](../virtual-network/virtual-network-peering-overview.md) om veilige connectiviteit mogelijk te maken via de Azure-infra structuur. 

Overweeg VNet 2 en VNet 3 in het bovenstaande diagram. Als VM 1 verbinding moet maken met VM 2 of volume 2, of als VM 2 verbinding moet maken met VM 1 of volume 1, moet u VNet-peering inschakelen tussen VNet 2 en VNet 3. 

Bedenk daarnaast een scenario waarin VNet 1 is gekoppeld aan VNet 2 en VNet 2 is gekoppeld aan VNet 3 in dezelfde regio. De resources van VNet 1 kunnen verbinding maken met resources in VNet 2, maar kunnen geen verbinding maken met resources in VNet 3, tenzij VNet 1 en VNet 3 gelijkwaardig zijn. 

In het bovenstaande diagram, hoewel VM 3 verbinding kan maken met volume 1, kan VM 4 geen verbinding maken met volume 2.  De reden hiervoor is dat de spoke-VNets niet worden gepeerd en dat _Transit routering niet wordt ondersteund via VNet-peering_.

## <a name="hybrid-environments"></a>Hybride omgevingen

Het volgende diagram illustreert een hybride omgeving: 

![Hybride netwerk omgeving](../media/azure-netapp-files/azure-netapp-files-network-hybrid-environment.png)

In het hybride scenario moeten toepassingen van on-premises data centers toegang hebben tot de resources in Azure.  Dit is het geval: u wilt uw Data Center uitbreiden naar Azure of u wilt Azure native-Services gebruiken of voor herstel na nood gevallen. Zie [VPN gateway plannings opties](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#planningtable) voor informatie over hoe u meerdere on-premises resources verbindt met resources in azure via een site-naar-site-VPN of een ExpressRoute.

In een hybride hub-spoke-topologie fungeert de hub VNet in azure als een centraal punt van connectiviteit met uw on-premises netwerk. De spokes zijn VNets gekoppeld aan de hub en ze kunnen worden gebruikt om workloads te isoleren.

Afhankelijk van de configuratie kunt u on-premises resources verbinden met resources in de hub en de spokes.

In de bovenstaande topologie is het on-premises netwerk verbonden met een hub VNet in Azure en zijn er twee spoke-VNets in dezelfde regio die zijn gekoppeld aan de hub VNet.  In dit scenario zijn de connectiviteits opties die worden ondersteund voor Azure NetApp Files volumes als volgt:

* On-premises resources VM 1 en VM 2 kunnen verbinding maken met volume 1 in de hub via een site-to-site VPN-of ExpressRoute-circuit. 
* On-premises resources VM 1 en VM 2 kunnen verbinding maken met volume 2 of volume 3 via een site-naar-site-VPN en regionale Vnet-peering.
* VM 3 in de hub-VNet kan verbinding maken met volume 2 in spoke VNet 1 en volume 3 in spoke VNet 2.
* VM 4 van spoke VNet 1 en VM 5 van spoke VNet 2 kan verbinding maken met volume 1 in de hub VNet.
* VM 4 in spoke VNet 1 kan geen verbinding maken met volume 3 in spoke VNet 2. Daarnaast kan VM 5 in spoke VNet2 geen verbinding maken met volume 2 in spoke VNet 1. Dit is het geval omdat de spoke-VNets niet worden gepeerd en _Transit routering niet wordt ondersteund via VNet-peering_.
* Als er in de bovenstaande architectuur ook een gateway in de spoke-VNET bestaat, gaat de connectiviteit met het ANF-volume van on-premises verbinding via de gateway in de hub verloren. Op basis van het ontwerp wordt de voor keur gegeven aan de gateway in de spoke-VNet, zodat alleen computers die verbinding maken via die gateway verbinding kunnen maken met het ANF-volume.

## <a name="next-steps"></a>Volgende stappen

[Een subnet delegeren aan Azure NetApp Files](azure-netapp-files-delegate-subnet.md)