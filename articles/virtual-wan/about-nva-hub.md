---
title: 'Azure Virtual WAN: Over virtueel netwerkapparaat in de hub'
description: In dit artikel vindt u meer informatie over virtuele netwerkapparaten in de Virtual WAN hub.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: scottnap
ms.openlocfilehash: 7c3ae14cd409e7bfc9be77c1a593964b73a12ddc
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107791045"
---
# <a name="about-network-virtual-appliance-in-an-azure-virtual-wan-hub-preview"></a>Over virtueel netwerkapparaat in een Azure Virtual WAN hub (preview)

Azure Virtual WAN heeft samengewerkt met netwerkpartners om automatisering te bouwen die het eenvoudig maakt om hun Customer Premise Equipment (CPE) te verbinden met een Azure VPN-gateway in de virtuele hub. Azure werkt samen met geselecteerde netwerkpartners om klanten in staat te stellen een virtueel netwerkapparaat (NVA) van derden rechtstreeks in de virtuele hub te implementeren. Hierdoor kunnen klanten die hun vertakking CPE willen verbinden met hetzelfde merk NVA in de virtuele hub, profiteren van eigen end-to-end SD-WAN-mogelijkheden.

Barracuda Networks en Cisco Systems zijn de eerste partners die de NNA's leveren die rechtstreeks kunnen worden geïmplementeerd in Virtual WAN hub.  Zie [Barracuda CloudGen WAN,](https://www.barracuda.com/products/cloudgenwan) [Cisco Cloud OnRamp for Multi-Cloud](https://www.cisco.com/c/en/us/td/docs/routers/sdwan/configuration/cloudonramp/ios-xe-17/cloud-onramp-book-xe/cloud-onramp-multi-cloud.html#Cisco_Concept.dita_c61e0e7a-fff8-4080-afee-47b81e8df701) en [VMware SD-WAN](https://kb.vmware.com/s/article/82746) voor hun respectieve productdocumentatie. Azure werkt met meer partners, dus u kunt verwachten dat er andere aanbiedingen volgen.

> [!NOTE]
> Alleen NVA-aanbiedingen die beschikbaar zijn om te worden geïmplementeerd in de Virtual WAN hub kunnen worden geïmplementeerd in Virtual WAN hub. Ze kunnen niet worden geïmplementeerd in een willekeurig virtueel netwerk in Azure.

## <a name="how-does-it-work"></a><a name="how"></a>Hoe werkt het?

De NNA's die rechtstreeks in de Azure Virtual WAN-hub kunnen worden geïmplementeerd, zijn speciaal ontworpen voor gebruik in de virtuele hub. De NVA-aanbieding wordt naar Azure Marketplace gepubliceerd als een beheerde toepassing en klanten kunnen de aanbieding rechtstreeks vanuit Azure Marketplace implementeren of ze kunnen de aanbieding implementeren vanuit de virtuele hub via de Azure Portal.

:::image type="content" source="./media/about-nva-hub/high-level-process.png" alt-text="Procesoverzicht":::

De NVA-aanbieding van elke partner heeft een iets andere ervaring en functionaliteit op basis van hun implementatievereisten. Er zijn echter enkele dingen die gemeenschappelijk zijn voor alle partneraanbiedingen voor NVA in de Virtual WAN hub.

* Een beheerde toepassingservaring die wordt aangeboden via Azure Marketplace.
* Capaciteit en facturering op basis van NVA-infrastructuur.
* Metrische gegevens over status die via Azure Monitor.

### <a name="managed-application"></a><a name="managed"></a>Beheerde toepassing

Alle NVA-aanbiedingen die beschikbaar zijn om te worden geïmplementeerd in  de Virtual WAN-hub, hebben een beheerde toepassing die beschikbaar is in Azure Marketplace. Met beheerde toepassingen kunnen partners het volgende doen:

* Bouw een aangepaste implementatie-ervaring voor hun NVA.
* Geef een speciale Resource Manager sjabloon waarmee ze de NVA rechtstreeks in de Virtual WAN maken.
* Factureren van softwarelicentiekosten rechtstreeks of via Azure Marketplace.
* Aangepaste eigenschappen en resourcemeters weergeven.

NVA-partners kunnen verschillende resources maken, afhankelijk van hun apparaatimplementatie, configuratielicenties en beheerbehoeften. Wanneer een klant een NVA maakt in de Virtual WAN hub, zoals alle beheerde toepassingen, worden er twee resourcegroepen gemaakt in hun abonnement.

* **Klantresourcegroep:** deze bevat een tijdelijke aanduiding voor de toepassing voor de beheerde toepassing. Partners kunnen dit gebruiken om de klanteigenschappen die ze hier kiezen, weer te geven.
* **Beheerde resourcegroep:** klanten kunnen resources in deze resourcegroep niet rechtstreeks configureren of wijzigen, omdat dit wordt beheerd door de uitgever van de beheerde toepassing. Deze resourcegroep bevat de **resource NetworkVirtualAppliances.**

:::image type="content" source="./media/about-nva-hub/managed-app.png" alt-text="Resourcegroepen voor beheerde toepassingen":::

### <a name="nva-infrastructure-units"></a><a name="units"></a>NVA-infrastructuureenheden

Wanneer u een NVA maakt in de Virtual WAN hub, moet u het aantal NVA-infrastructuureenheden kiezen dat u wilt implementeren. Een **NVA-infrastructuureenheid** is een eenheid van cumulatieve bandbreedtecapaciteit voor een NVA in de Virtual WAN hub. Een **NVA-infrastructuureenheid** is vergelijkbaar met een [VPN-schaaleenheid](pricing-concepts.md#scale-unit) wat betreft de manier waarop u over capaciteit en de omvang denkt.

* 1 NVA-infrastructuureenheid vertegenwoordigt 500 Mbps geaggregeerde bandbreedte voor alle siteverbindingen van filialen die in deze NVA komen, en kost $ 0,25/uur.
* Azure ondersteunt 1-80 NVA-infrastructuureenheden voor een bepaalde implementatie van een virtuele NVA-hub.
* Elke partner kan verschillende NVA Infrastructure Unit-bundels aanbieden die een subset zijn van alle ondersteunde NVA Infrastructure Unit-configuraties.

Net als bij VPN-schaaleenheden, als u *1 NVA-infrastructuureenheid = 500 Mbps* kiest, betekent dit dat er twee instanties voor redundantie worden gemaakt, elk met een maximale doorvoer van 500 Mbps. Als u bijvoorbeeld vijf vertakkingen hebt, elk met een doorvoer van 10 Mbps, hebt u aan het eind van de vertakkingen een totaal van 50 Mbps nodig. Planning voor geaggregeerde capaciteit van de NVA moet worden uitgevoerd na het evalueren van de capaciteit die nodig is om het aantal vertakkingen naar de hub te ondersteunen.

## <a name="network-virtual-appliance-configuration-process"></a><a name="configuration"></a>Configuratieproces voor virtueel netwerkapparaat

Partners hebben ervoor gewerkt om een ervaring te bieden die de NVA automatisch configureert als onderdeel van het implementatieproces. Zodra de NVA is ingericht in de virtuele hub, moet elke aanvullende configuratie die mogelijk vereist is voor de NVA, worden uitgevoerd via de NVA-partnerportal of beheertoepassing. Directe toegang tot de NVA is niet beschikbaar.

## <a name="site-and-connection-resources-with-nvas"></a><a name="resources"></a>Site- en verbindingsbronnen met N NVA's

In tegenstelling tot Azure VPN Gateway-configuraties hoeft  u geen site-resources, **site-naar-site-verbindingsbronnen** of resources voor **punt-naar-site-verbindingen** te maken om uw vertakkingssites te verbinden met uw NVA in de Virtual WAN hub. Dit wordt allemaal beheerd via de NVA-partner.

U moet nog steeds hub-naar-VNet-verbindingen maken om uw Virtual WAN-hub te verbinden met uw virtuele Azure-netwerken.

## <a name="supported-regions"></a><a name="regions"></a>Ondersteunde regio’s

NVA in de virtuele hub is beschikbaar voor preview in de volgende regio's:

|Geopolitieke regio | Azure-regio's|
|---|---|
| Noord-Amerika| Canada - centraal, Canada - oost, VS - centraal, VS - oost, VS - oost 2, VS - noord-centraal, VS - west-centraal, VS - west, VS - west 2 |
| Zuid-Amerika | Brazilië - zuid, Brazilië - zuidoost |
| Europa | Frankrijk - centraal, Frankrijk - zuid, Duitsland - noord, Duitsland - west-centraal, Europa - noord, Noorwegen - oost, Noorwegen - west, Zwitserland - noord, Zwitserland - west, VK - zuid, VK - west, Europa - west|
|  Midden-Oosten | VAE - noord |
| Azië |  Azië - oost, Japan - oost, Japan - west, Korea - centraal, Korea - zuid, Azië - zuidoost | 
| Australië | Australië - zuidoost, Australië - oost, Australië - centraal, Australië - centraal 2|
| Afrika | Zuid-Afrika - noord |
| India | India - zuid, India - west, India - centraal | 
## <a name="faq"></a>Veelgestelde vragen

### <a name="i-am-a-network-appliance-partner-and-want-to-get-our-nva-in-the-hub--can-i-join-this-partner-program"></a>Ik ben een netwerkapparaatpartner en wil onze NVA in de hub krijgen.  Kan ik deelnemen aan dit partnerprogramma?

Helaas hebben we op dit moment geen capaciteit om nieuwe partneraanbiedingen in te maken. Neem in november nog even contact met ons op.

### <a name="can-i-deploy-any-nva-from-azure-marketplace-into-the-virtual-wan-hub"></a>Kan ik een NVA implementeren vanuit Azure Marketplace in Virtual WAN hub?

Op dit moment zijn alleen [Barracuda CloudGen WAN](https://aka.ms/BarracudaMarketPlaceOffer)  [Cisco Cloud vWAN Application](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/cisco.cisco_cloud_vwan_app?tab=Overview) en [VMware Sd-WAN](https://aka.ms/vmwareMarketplaceLink) beschikbaar om te worden geïmplementeerd in de Virtual WAN hub.

### <a name="what-is-the-cost-of-the-nva"></a>Wat zijn de kosten van de NVA?

U moet een licentie voor de NVA aanschaffen bij de NVA-leverancier.  Zie de CloudGen WAN-pagina van Barracuda voor uw Barracuda CloudGen WAN NVA van [barracuda-licentie.](https://www.barracuda.com/products/cloudgenwan) Cisco biedt momenteel alleen byOL-licentiemodellen (Bring Your Own License) die rechtstreeks bij Cisco moeten worden aangeschaft. Daarnaast worden er door Microsoft kosten in rekening gebracht voor de NVA-infrastructuureenheden die u gebruikt en andere resources die u gebruikt. Zie Prijsconcepten [voor meer informatie.](pricing-concepts.md)

### <a name="can-i-deploy-an-nva-to-a-basic-hub"></a>Kan ik een NVA implementeren op een Basic-hub?

Nee. U moet een Standard-hub gebruiken als u een NVA wilt implementeren.

### <a name="can-i-deploy-an-nva-into-a-secure-hub"></a>Kan ik een NVA implementeren in een beveiligde hub?

Ja. NVA's van partners kunnen worden geïmplementeerd in een hub met Azure Firewall.

### <a name="can-i-connect-any-cpe-device-in-my-branch-office-to-barracuda-cloudgen-wan-nva-in-the-hub"></a>Kan ik elk CPE-apparaat in mijn filiaal verbinden met Barracuda CloudGen WAN NVA in de hub?

Nee. Barracuda CloudGen WAN is alleen compatibel met Barracuda CPE-apparaten. Zie de Pagina CloudGen WAN van Barracuda voor meer informatie over [CloudGen WAN-vereisten.](https://www.barracuda.com/products/cloudgenwan) Voor Cisco zijn er verschillende SD-WAN CPE-apparaten die kunnen worden gecompatreerd. Zie [Cisco Cloud OnRamp for Multi-Cloud-documenation](https://www.cisco.com/c/en/us/td/docs/routers/sdwan/configuration/cloudonramp/ios-xe-17/cloud-onramp-book-xe/cloud-onramp-multi-cloud.html#Cisco_Concept.dita_c61e0e7a-fff8-4080-afee-47b81e8df701) voor compatable CPE's.

### <a name="what-routing-scenarios-are-supported-with-nva-in-the-hub"></a>Welke routeringsscenario's worden ondersteund met NVA in de hub?

Alle routeringsscenario's die Virtual WAN worden ondersteund met NNA's in de hub.

## <a name="next-steps"></a>Volgende stappen

Zie het artikel Virtual WAN overzicht voor [meer Virtual WAN informatie.](virtual-wan-about.md)
