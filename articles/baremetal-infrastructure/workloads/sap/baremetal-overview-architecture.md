---
title: Overzicht van de preview-versie van BareMetal-infra structuur in azure
description: Overzicht van de BareMetal-infra structuur in Azure.
ms.custom: references_regions
ms.topic: conceptual
ms.date: 1/4/2021
ms.openlocfilehash: eb4dc129719dc410f7101598e3d72e68f17809c1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97860977"
---
#  <a name="what-is-baremetal-infrastructure-preview-on-azure"></a>Wat is een BareMetal-infra structuur in azure?

De Azure BareMetal-infra structuur biedt een veilige oplossing voor het migreren van aangepaste werk belastingen op ondernemings niveau. De BareMetal-instanties zijn niet-gedeelde host/serverhardware die aan u zijn toegewezen. Hiermee wordt de poort van uw on-premises oplossing ontgrendeld met gespecialiseerde werk belastingen waarvoor gecertificeerde hardware-, licentie-en ondersteunings overeenkomsten zijn vereist. Azure verwerkt infrastructuur bewaking en onderhoud voor u, terwijl het besturings systeem van de gast besturing en toepassings bewaking binnen uw eigendom valt.

De BareMetal-infra structuur biedt een pad voor het moderniseren van uw infra structuur en het onderhoud van uw bestaande investeringen en architectuur. Met de BareMetal-infra structuur kunt u speciale workloads naar Azure brengen, waardoor u toegang hebt tot en integratie met Azure-Services met lage latentie.

## <a name="sku-availability-in-azure-regions"></a>SKU-Beschik baarheid in azure-regio's
De BareMetal-infra structuur voor gespecialiseerde en algemene werk belastingen is beschikbaar, beginnend met vier regio's op basis van de revisie 4,2 (Rev 4,2) stem pels:
- Europa -west
- Europa - noord
- VS - oost 2
- VS - zuid-centraal

>[!NOTE]
>**Rev 4,2** is de meest recente BareMetal-infra structuur met een eigenlijke rebranding en maakt gebruik van de bestaande Rev 4-architectuur.  Rev 4 biedt dichter nabijheid van de Azure virtual machine-hosts (VM). Het heeft aanzienlijke verbeteringen in de netwerk latentie tussen Azure Vm's en BareMetal exemplaar eenheden die zijn geïmplementeerd in Rev 4-stem pels of-rijen.  U kunt uw BareMetal-instanties openen en beheren via de Azure Portal. 

## <a name="support"></a>Ondersteuning
BareMetal-infra structuur is ISO 27001, ISO 27017, SOC 1 en SOC 2-compatibel.  Er wordt ook gebruikgemaakt van een BYOL-model (maken-your-own-License): besturings systeem, gespecialiseerde werk belasting en toepassingen van derden.  

Zodra u toegang krijgt tot de hoofdmap en volledig beheer, neemt u de volgende verantwoordelijkheid:
- Back-up-en herstel oplossingen, hoge Beschik baarheid en herstel na nood gevallen ontwerpen en implementeren
- Licenties, beveiliging en ondersteuning voor besturings systeem en software van derden

Micro soft is verantwoordelijk voor het volgende:
- De hardware voorzien van gespecialiseerde workloads 
- Het besturings systeem inrichten

:::image type="content" source="media/baremetal-support-model.png" alt-text="Model voor ondersteuning van BareMetal-infra structuur" border="false":::

## <a name="compute"></a>Compute
De BareMetal-infra structuur biedt meerdere Sku's voor gespecialiseerde workloads. Beschik bare Sku's beschik bare variëren van het kleinere systeem met twee sockets tot het systeem met 24 sockets. Gebruik de workload-specifieke Sku's voor uw gespecialiseerde werk belasting.

De BareMetal-instantie stempel zelf is een combi natie van de volgende onderdelen:

- **Computing:** Servers op basis van een andere generatie Intel Xeon-processors die de benodigde computer capaciteit bieden en zijn gecertificeerd voor de gespecialiseerde werk belasting.

- **Netwerk:** Een uniforme netwerk infrastructuur met hoge snelheid verbindt computer-, opslag-en LAN-onderdelen.

- **Opslag:** Een infra structuur die toegankelijk is via een uniforme netwerk infrastructuur.

Binnen de multi tenant-infra structuur van de BareMetal-stempel worden klanten geïmplementeerd in geïsoleerde tenants. Wanneer u een Tenant implementeert, moet u een Azure-abonnement binnen uw Azure-inschrijving benoemen. Dit Azure-abonnement is de versie die BareMetal exemplaren worden gefactureerd.

>[!NOTE]
>Een klant die in het BareMetal-exemplaar is geïmplementeerd, wordt geïsoleerd in een Tenant. Een Tenant wordt geïsoleerd in het netwerk, de opslag en de compute-laag van andere tenants. Opslag-en reken eenheden die aan de verschillende tenants zijn toegewezen, kunnen niet worden weer geven of communiceren met elkaar op de BareMetal-instanties.

## <a name="os"></a>Besturingssysteem
Tijdens het inrichten van de BareMetal-instantie kunt u het besturings systeem selecteren dat u op de computers wilt installeren. 

>[!NOTE]
>Houd er rekening mee dat BareMetal Infrastructure een BYOL-model is.

De beschik bare versies van het Linux-besturings systeem zijn:
- Red Hat Enterprise Linux (RHEL) 7,6
- SUSE Linux Enterprise Server (SLES)
   - SLES 12 SP2
   - SLES 12 SP3
   - SLES 12 SP4
   - SLES 12 SP5
   - SLES 15 SP1

## <a name="storage"></a>Storage
BareMetal-exemplaren op basis van een specifiek SKU-type worden geleverd met vooraf gedefinieerde NFS-opslag voor het specifieke type werk belasting. Wanneer u BareMetal inricht, kunt u meer opslag ruimte inrichten op basis van uw geschatte groei door een ondersteunings aanvraag in te dienen. Alle opslag wordt geleverd met een all-flash-schijf in Revision 4,2 met ondersteuning voor NFSv3 en NFSv4. De nieuwere versie van de revisies 4,5 NVMe-SSD is beschikbaar. Zie de sectie [BareMetal workload type](../../../virtual-machines/workloads/sap/get-started.md) voor meer informatie over opslag grootte.

>[!NOTE]
>De opslag die wordt gebruikt voor BareMetal voldoet aan de 140-2-vereisten voor de [publicatie van Federal Information Processing Standard (FIPS)](/microsoft-365/compliance/offering-fips-140-2) die standaard versleuteling aanbieden. De gegevens worden veilig opgeslagen op de schijven.

## <a name="networking"></a>Netwerken
De architectuur van Azure Network Services is een belang rijk onderdeel voor een geslaagde implementatie van gespecialiseerde workloads in BareMetal-instanties. Waarschijnlijk zijn niet alle IT-systemen in azure al aanwezig. Azure biedt u netwerk technologie waarmee Azure eruitziet als een virtueel Data Center naar uw on-premises software-implementaties. De Azure-netwerk functionaliteit die is vereist voor BareMetal-exemplaren is:

- Virtuele Azure-netwerken zijn verbonden met het ExpressRoute-circuit dat verbinding maakt met uw on-premises netwerk assets.
- Een ExpressRoute-circuit dat on-premises met Azure verbindt, moet een minimale band breedte van 1 Gbps of hoger hebben.
- Uitgebreide Active Directory en DNS in azure of volledig uitgevoerd in Azure.

Met ExpressRoute kunt u uw on-premises netwerk uitbreiden naar micro soft Cloud via een particuliere verbinding met de hulp van een connectiviteits provider. U kunt **ExpressRoute Premium** inschakelen voor het uitbreiden van de connectiviteit tussen de geopolitieke grenzen of het gebruik van **ExpressRoute lokaal** voor rendabele gegevens overdracht tussen de locatie in de buurt van de Azure-regio die u wilt.

BareMetal-exemplaren worden ingericht binnen het IP-adres bereik van uw Azure VNET-server.

:::image type="content" source="media/baremetal-infrastructure-portal/baremetal-infrastructure-diagram.png" alt-text="Diagram van Azure BareMetal-infra structuur" lightbox="media/baremetal-infrastructure-portal/baremetal-infrastructure-diagram.png" border="false":::

De weer gegeven architectuur is onderverdeeld in drie secties:
- **Links:** toont de on-premises infra structuur van de klant die verschillende toepassingen uitvoert, waarbij verbinding wordt gemaakt via de partner of de lokale edge-router, zoals Equinix. Zie [connectiviteits providers en-locaties: Azure ExpressRoute](../../../expressroute/expressroute-locations.md)voor meer informatie.
- **Center:** toont [ExpressRoute](../../../expressroute/expressroute-introduction.md) ingericht met behulp van uw Azure-abonnement waarmee verbinding wordt gemaakt met het Azure Edge-netwerk.
- **Rechts:** hier worden Azure-IaaS weer gegeven, en in dit geval het gebruik van vm's voor het hosten van uw toepassingen, die zijn ingericht in uw virtuele Azure-netwerk.
- **Onder:** toont uw ExpressRoute-gateway die is ingeschakeld met [ExpressRoute FastPath](../../../expressroute/about-fastpath.md) voor BareMetal-connectiviteit met lage latentie.   
   >[!TIP]
   >Ter ondersteuning hiervan moet uw ExpressRoute-gateway Ultra Performance zijn.  Zie [over ExpressRoute virtuele netwerk gateways](../../../expressroute/expressroute-about-virtual-network-gateways.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

De volgende stap is informatie over het identificeren en gebruiken van BareMetal-exemplaar eenheden via de Azure Portal.

> [!div class="nextstepaction"]
> [BareMetal-instanties beheren via de Azure Portal](baremetal-infrastructure-portal.md)