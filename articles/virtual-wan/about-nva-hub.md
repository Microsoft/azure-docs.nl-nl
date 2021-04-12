---
title: 'Virtueel WAN van Azure: over het virtuele netwerk apparaat in de hub'
description: In dit artikel vindt u meer informatie over virtuele netwerk apparaten in de virtuele WAN-hub.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: scottnap
ms.openlocfilehash: e70b899ea0133704e4ecab42dd4c2a09bca41804
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2021
ms.locfileid: "107226308"
---
# <a name="about-network-virtual-appliance-in-an-azure-virtual-wan-hub-preview"></a>Over virtuele netwerk apparaten in een virtuele WAN-hub van Azure (preview)

Azure Virtual WAN heeft met netwerk partners gewerkt om automatisering te bouwen waarmee het eenvoudig is om hun klant locatie-uitrusting (CPE) te verbinden met een Azure VPN-gateway in de virtuele hub. Azure werkt met geselecteerde netwerk partners om klanten in staat te stellen een virtueel netwerk apparaat (NVA) van derden rechtstreeks naar de virtuele hub te implementeren. Hierdoor kunnen klanten die hun vertakking CPE willen verbinden met dezelfde merk NVA in de virtuele hub, zodat ze profiteren van eigen end-to-end SD-WAN-mogelijkheden.

Barracuda Networks en Cisco Systems zijn de eerste partners die de Nva's bieden die rechtstreeks op de virtuele WAN-hub kunnen worden geïmplementeerd.  Zie [Barracuda CLOUDGEN WAN](https://www.barracuda.com/products/cloudgenwan), [Cisco Cloud opstap for multi-Cloud](https://www.cisco.com/c/en/us/td/docs/routers/sdwan/configuration/cloudonramp/ios-xe-17/cloud-onramp-book-xe/cloud-onramp-multi-cloud.html#Cisco_Concept.dita_c61e0e7a-fff8-4080-afee-47b81e8df701) en [VMware SD-WAN](https://kb.vmware.com/s/article/82746) voor hun respectieve product documentatie. Azure werkt met meer partners, zodat u rekening moet houden met andere aanbiedingen.

> [!NOTE]
> Alleen NVA-aanbiedingen die beschikbaar zijn om te worden geïmplementeerd in de virtuele WAN-hub, kunnen in de virtuele WAN-hub worden geïmplementeerd. Ze kunnen niet worden geïmplementeerd in een wille keurig virtueel netwerk in Azure.

## <a name="how-does-it-work"></a><a name="how"></a>Hoe werkt het?

De Nva's die beschikbaar zijn om rechtstreeks te worden geïmplementeerd in de virtuele WAN-hub van Azure, worden speciaal ontworpen om te worden gebruikt in de virtuele hub. De NVA-aanbieding wordt gepubliceerd naar Azure Marketplace als een beheerde toepassing. klanten kunnen de aanbieding rechtstreeks vanuit Azure Marketplace implementeren of ze kunnen de aanbieding via de virtuele hub implementeren via de Azure Portal.

:::image type="content" source="./media/about-nva-hub/high-level-process.png" alt-text="Overzicht van het proces":::

De NVA-aanbieding van elke partner heeft een iets andere ervaring en functionaliteit op basis van hun implementatie vereisten. Er zijn echter enkele dingen die gemeen schappelijk zijn voor alle partner aanbiedingen voor NVA in de virtuele WAN-hub.

* Een beheerde toepassings ervaring die wordt aangeboden via Azure Marketplace.
* NVA capaciteit en facturering op basis van de infra structuur.
* Metrische gegevens over de status, die worden belicht door Azure Monitor.

### <a name="managed-application"></a><a name="managed"></a>Beheerde toepassing

Alle NVA-aanbiedingen die beschikbaar zijn om te worden geïmplementeerd in de virtuele WAN-hub, hebben een **beheerde toepassing** die beschikbaar is in azure Marketplace. Met beheerde toepassingen kunnen partners het volgende doen:

* Bouw een aangepaste implementatie-ervaring voor hun NVA.
* Geef een gespecialiseerde Resource Manager-sjabloon op waarmee de NVA rechtstreeks in de virtuele WAN-hub kan worden gemaakt.
* Factuur software licentie kosten rechtstreeks of via Azure Marketplace.
* Stel aangepaste eigenschappen en resource meters beschikbaar.

NVA-partners kunnen verschillende resources maken, afhankelijk van hun toestel-implementatie, configuratie licenties en beheer behoeften. Wanneer een klant een NVA maakt in de virtuele WAN-hub, zoals alle beheerde toepassingen, worden er twee resource groepen gemaakt in het abonnement.

* **Resource groep van klant** : dit bevat een tijdelijke aanduiding voor de toepassing voor de beheerde toepassing. Partners kunnen deze gebruiken om te zien welke klant eigenschappen ze hier kiezen.
* **Beheerde resource groep** : klanten kunnen resources in deze resource groep niet rechtstreeks configureren of wijzigen, omdat dit wordt bepaald door de uitgever van de beheerde toepassing. Deze resource groep bevat de **NetworkVirtualAppliances** -resource.

:::image type="content" source="./media/about-nva-hub/managed-app.png" alt-text="Resource groepen voor beheerde toepassingen":::

### <a name="nva-infrastructure-units"></a><a name="units"></a>NVA-infrastructuur eenheden

Wanneer u een NVA in de virtuele WAN-hub maakt, moet u het aantal NVA-infrastructuur eenheden kiezen dat u wilt implementeren. Een **NVA-infrastructuur eenheid** is een eenheid voor een cumulatieve bandbreedte capaciteit voor een NVA in de virtuele WAN-hub. Een **NVA-infrastructuur eenheid** is vergelijkbaar met een eenheid voor VPN- [schaal](pricing-concepts.md#scale-unit) afhankelijk van de manier waarop u rekening moet houden met capaciteit en grootte.

* 1 NVA infrastructuur eenheid vertegenwoordigt 500 Mbps geaggregeerde band breedte voor alle filialen die in deze NVA komen, met een prijs van $0,25 per uur.
* Azure ondersteunt van 1-80 NVA-infrastructuur eenheden voor een bepaalde implementatie van een virtuele NVA-hub.
* Elke partner kan verschillende NVA infrastructuur eenheid-bundels zijn die een subset zijn van alle ondersteunde configuraties van de NVA-infrastructuur eenheid.

Net als bij VPN-schaal eenheden kunt u, als u *1 NVA-infrastructuur eenheid = 500 Mbps* kiest, er twee exemplaren voor redundantie worden gemaakt, elk met een maximale door voer van 500 Mbps. Als u bijvoorbeeld vijf vertakkingen hebt, elk met een doorvoer van 10 Mbps, hebt u aan het eind van de vertakkingen een totaal van 50 Mbps nodig. Het plannen van de totale capaciteit van de NVA moet worden uitgevoerd na het beoordelen van de capaciteit die nodig is om het aantal branches naar de hub te ondersteunen.

## <a name="network-virtual-appliance-configuration-process"></a><a name="configuration"></a>Configuratie proces van virtueel netwerk apparaat

Partners hebben gewerkt om een ervaring te bieden waarbij de NVA automatisch worden geconfigureerd als onderdeel van het implementatie proces. Zodra de NVA is ingericht in de virtuele hub, moeten eventuele aanvullende configuraties die nodig zijn voor de NVA, worden uitgevoerd via de portal voor de NVA-partners of de beheer toepassing. Er is geen rechtstreekse toegang tot de NVA beschikbaar.

## <a name="site-and-connection-resources-with-nvas"></a><a name="resources"></a>Site-en verbindings bronnen met Nva's

In tegens telling tot Azure VPN Gateway-configuraties hoeft u geen **site** resources, **site-naar-site-verbindings** resources of **punt-naar-site-verbindings** resources te maken om uw Branch-sites te verbinden met uw NVA in de virtuele WAN-hub. Dit wordt allemaal beheerd via de NVA-partner.

U moet nog steeds hub-naar-VNet-verbindingen maken om uw virtuele WAN-hub te koppelen aan uw Azure Virtual Networks.

## <a name="supported-regions"></a><a name="regions"></a>Ondersteunde regio’s

NVA in de virtuele hub is beschikbaar voor preview in de volgende regio's:

|Geopolitieke regio | Azure-regio's|
|---|---|
| Noord-Amerika| VS-West, Zuid-Centraal VS, VS-Oost 2   |
| Zuid-Amerika | Brazilië - zuid |
| Europa | Europa-west, UK-zuid|
|  Midden-Oosten | VAE - noord |
| Azië | Japan East |
| Australië | Australië - oost |

## <a name="faq"></a>Veelgestelde vragen

### <a name="i-am-a-network-appliance-partner-and-want-to-get-our-nva-in-the-hub--can-i-join-this-partner-program"></a>Ik ben een netwerk apparaat partner en wil onze NVA in de hub ontvangen.  Kan ik lid worden van dit partner programma?

Helaas hebben we op dit moment geen capaciteit voor nieuwe partner aanbiedingen. Kom in november!

### <a name="can-i-deploy-any-nva-from-azure-marketplace-into-the-virtual-wan-hub"></a>Kan ik een wille keurige NVA implementeren vanuit Azure Marketplace in de virtuele WAN-hub?

Op dit moment zijn alleen [Barracuda CLOUDGEN WAN](https://aka.ms/BarracudaMarketPlaceOffer) -en [Cisco Cloud vWAN-toepassing](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/cisco.cisco_cloud_vwan_app?tab=Overview) beschikbaar om te worden GEÏMPLEMENTEERD in de virtuele WAN-hub.

### <a name="what-is-the-cost-of-the-nva"></a>Wat zijn de kosten van de NVA?

U moet een licentie voor de NVA aanschaffen bij de NVA-leverancier.  Voor uw Barracuda CloudGen WAN NVA van Barracuda-licentie raadpleegt u [Barracuda CLOUDGEN WAN-pagina](https://www.barracuda.com/products/cloudgenwan). Cisco biedt momenteel alleen BYOL (neem uw eigen licentie mee) die rechtstreeks van Cisco moeten worden aangeschaft. Daarnaast worden er ook kosten van micro soft in rekening gebracht voor de NVA-infrastructuur eenheden die u verbruikt en alle andere resources die u gebruikt. Zie [prijs begrippen](pricing-concepts.md)voor meer informatie.

### <a name="can-i-deploy-an-nva-to-a-basic-hub"></a>Kan ik een NVA implementeren op een Basic-hub?

Nee. U moet een standaard hub gebruiken als u een NVA wilt implementeren.

### <a name="can-i-deploy-an-nva-into-a-secure-hub"></a>Kan ik een NVA implementeren in een beveiligde hub?

Ja. Barracuda CloudGen WAN kan worden geïmplementeerd in een hub met Azure Firewall.

### <a name="can-i-connect-any-cpe-device-in-my-branch-office-to-barracuda-cloudgen-wan-nva-in-the-hub"></a>Kan ik een CPE-apparaat in mijn filiaal aansluiten op Barracuda CloudGen WAN NVA in de hub?

Nee. Barracuda CloudGen WAN is alleen compatibel met Barracuda CPE-apparaten. Zie [de CLOUDGEN WAN-pagina van Barracuda](https://www.barracuda.com/products/cloudgenwan)voor meer informatie over CloudGen WAN-vereisten. Voor Cisco zijn er verschillende SD-WAN-apparaten die compatibel zijn. Zie [Cisco Cloud opstap for multi-Cloud](https://www.cisco.com/c/en/us/td/docs/routers/sdwan/configuration/cloudonramp/ios-xe-17/cloud-onramp-book-xe/cloud-onramp-multi-cloud.html#Cisco_Concept.dita_c61e0e7a-fff8-4080-afee-47b81e8df701) Documenation voor compatibele CPES.

### <a name="what-routing-scenarios-are-supported-with-nva-in-the-hub"></a>Welke routerings scenario's worden ondersteund met NVA in de hub?

Alle routerings scenario's die worden ondersteund door virtuele WAN worden ondersteund met Nva's in de hub.

## <a name="next-steps"></a>Volgende stappen

Zie het artikel [overzicht van Virtual WAN](virtual-wan-about.md) voor meer informatie over Virtual WAN.
