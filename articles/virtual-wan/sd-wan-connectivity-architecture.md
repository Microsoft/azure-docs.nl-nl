---
title: Architecturen voor virtuele WAN-en SD-WAN-verbindingen
titleSuffix: Azure Virtual WAN
description: Meer informatie over het verbinden van een privé-SD-WAN met Azure Virtual WAN
services: virtual-wan
author: skishen525
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 10/07/2020
ms.author: sukishen
ms.openlocfilehash: ea9ddd05fe6fc745a3eefc29ab4f1d6aababc936
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94564698"
---
# <a name="sd-wan-connectivity-architecture-with-azure-virtual-wan"></a>Architectuur van de SD-WAN-verbinding met Azure Virtual WAN

Azure Virtual WAN is een netwerk service die veel Cloud connectiviteits-en beveiligings Services met één operationele interface samen brengt. Deze services omvatten onder andere vertakking (via site-naar-site-VPN), externe gebruiker (punt-naar-site-VPN), persoonlijke (ExpressRoute) verbinding, intra-Cloud transitieve connectiviteit voor Vnets, VPN en ExpressRoute interconnectiviteit, route ring, Azure Firewall en versleuteling voor persoonlijke connectiviteit.

Hoewel Azure Virtual WAN zelf een door software gedefinieerde WAN (SD-WAN) is, is het ook ontworpen om naadloze verbinding mogelijk te maken met de op premises gebaseerde SD-WAN-technologieën en-services. Veel van deze services worden aangeboden door ons [virtuele WAN](virtual-wan-locations-partners.md) -ecosysteem en Azure Network Managed Services partners [(msp's)](../networking/networking-partners-msp.md). Ondernemingen die hun particuliere WAN transformeren naar SD-WAN, hebben opties bij het interkoppelen van hun particuliere SD-WAN met Azure Virtual WAN. Ondernemingen kunnen kiezen uit de volgende opties:

* Model direct Interconnect
* Model direct interconnect met NVA-in-VWAN-hub
* Model voor indirecte Interconnect
* Beheerd hybride WAN-model met behulp van hun favoriete managed service provider [MSP](../networking/networking-partners-msp.md)

In al deze gevallen is de interconnectie van het virtuele WAN met SD-WAN vergelijkbaar met de connectiviteits zijde, maar kan de indeling en de operationele kant variëren.

## <a name="direct-interconnect-model"></a><a name="direct"></a>Model direct Interconnect

:::image type="content" source="./media/sd-wan-connectivity-architecture/direct.png" alt-text="Model direct Interconnect":::

In dit architectuur model is de klant locatie-uitrusting van SD-WAN-Branch (CPE) rechtstreeks verbonden met virtuele WAN-hubs via IPsec-verbindingen. De vertakking CPE kan ook worden verbonden met andere branches via de particuliere SD-WAN of gebruikmaken van Virtual WAN voor vertakkings connectiviteit. Vertakkingen die toegang moeten hebben tot hun werk belastingen in azure, kunnen rechtstreeks en veilig toegang krijgen tot Azure via de IPsec-tunnel (s) die worden beëindigd in de virtuele WAN-hub (s).

Met SD-WAN-CPE-partners kan automatisering worden ingeschakeld om het normaal-en probleem gevoelige IPsec-connectiviteit van hun respectieve CPE-apparaten te automatiseren. Met Automation kan de SD-WAN-controller met Azure communiceren via de virtuele WAN-API om de virtuele WAN-sites te configureren, en de benodigde configuratie van de IPsec-tunnel naar het filiaal CPEs te pushen. Zie de [richt lijnen voor automatisering](virtual-wan-configure-automation-providers.md) voor de beschrijving van de automatisering van virtuele WAN-verbindingen door verschillende SD-WAN-partners.

Het SD-WAN CPE blijft de plek waar het verkeer wordt geoptimaliseerd en het selecteren van het pad wordt geïmplementeerd en afgedwongen. 

In dit model worden bepaalde eigen verkeer optimalisaties op basis van real-time verkeers kenmerken mogelijk niet ondersteund, omdat de verbinding met Virtual WAN via IPsec verloopt en het IPsec VPN wordt beëindigd op de virtuele WAN-VPN-gateway. Dynamische paden kunnen bijvoorbeeld worden geselecteerd bij het vertakkings verschil als gevolg van het vertakkings apparaat waarbij verschillende netwerk pakket gegevens worden uitgewisseld met een ander SD-WAN-knoop punt, waardoor de beste koppeling kan worden gebruikt voor verschillende verkeer dat dynamisch wordt geprioriteerd op het filiaal. Deze functie kan nuttig zijn in gebieden waar de optimalisatie van de laatste mijl (vertakking naar de dichtstbijzijnde micro soft-POP) is vereist.

Met virtueel WAN kunnen gebruikers de selectie van Azure-paden verkrijgen. Dit is een op beleid gebaseerde Padselectie op meerdere ISP-koppelingen van het vertakkings CPE tot virtuele WAN-gateways. Met Virtual WAN kunt u meerdere koppelingen (paden) van dezelfde SD-WAN-vertakking CPE instellen. elke koppeling vertegenwoordigt een dubbele tunnel verbinding van een uniek openbaar IP-adres van het SD-WAN-netwerk tot twee verschillende exemplaren van Azure Virtual WAN VPN gateway. Leveranciers van SD-WAN kunnen het meest optimale pad naar Azure implementeren op basis van het Traffic-beleid dat is ingesteld door hun beleids engine op de CPE-koppelingen. Op het eind punt van Azure worden alle binnenkomende verbindingen op dezelfde manier behandeld.

## <a name="direct-interconnect-model-with-nva-in-vwan-hub"></a><a name="direct"></a>Model direct interconnect met NVA-in-VWAN-hub

:::image type="content" source="./media/sd-wan-connectivity-architecture/direct-nva.png" alt-text="Model direct interconnect met NVA-in-VWAN-hub":::

Dit architectuur model ondersteunt de implementatie van een [virtueel netwerk (NVA) van een derde partij rechtstreeks naar de virtuele hub](./about-nva-hub.md). Hierdoor kunnen klanten die hun vertakkings verbinding willen maken met hetzelfde merk NVA in de virtuele hub, zodat ze profiteren van eigen end-to-end SD-WAN-mogelijkheden bij het maken van verbinding met Azure-workloads. 

Verschillende virtuele WAN-partners hebben gewerkt om een ervaring te bieden waarmee de NVA automatisch worden geconfigureerd als onderdeel van het implementatie proces. Zodra de NVA is ingericht in de virtuele hub, moeten eventuele aanvullende configuraties die nodig zijn voor de NVA, worden uitgevoerd via de portal voor de NVA-partners of de beheer toepassing. Er is geen rechtstreekse toegang tot de NVA beschikbaar. De Nva's die beschikbaar zijn om rechtstreeks te worden geïmplementeerd in de virtuele WAN-hub van Azure, worden speciaal ontworpen om te worden gebruikt in de virtuele hub. Raadpleeg het artikel over [virtuele WAN-partners](virtual-wan-locations-partners.md#partners-with-integrated-virtual-hub-offerings) voor partners die ondersteuning bieden voor NVA in VWAN hub en hun implementatie handleidingen.

Het SD-WAN CPE blijft de plek waar het verkeer wordt geoptimaliseerd en het selecteren van het pad wordt geïmplementeerd en afgedwongen.
In dit model wordt de leverancier van eigen verkeer op basis van real-time verkeers kenmerken ondersteund, omdat de verbinding met Virtual WAN via de SD-WAN-NVA in de hub plaatsvindt.

## <a name="indirect-interconnect-model"></a><a name="indirect"></a>Model voor indirecte Interconnect

:::image type="content" source="./media/sd-wan-connectivity-architecture/indirect.png" alt-text="Model voor indirecte Interconnect":::

In dit architectuur model zijn SD-WAN Branch CPEs indirect verbonden met virtuele WAN-hubs. Zoals in de afbeelding wordt weer gegeven, wordt een virtuele SD-WAN-CPE geïmplementeerd in een VNet voor ondernemingen. Dit virtuele CPE is op zijn beurt verbonden met de virtuele WAN-hub (s) met behulp van IPsec. De virtuele CPE fungeert als een SD-WAN-gateway in Azure. Vertakkingen die toegang moeten hebben tot hun werk belastingen in azure, hebben toegang tot deze via de v-CPE-gateway.

Omdat de connectiviteit met Azure via de v-CPE-gateway (NVA) is, wordt al het verkeer van en naar de Azure-werk belasting VNets naar andere branches van een SD-WAN via de NVA. In dit model is de gebruiker verantwoordelijk voor het beheren en gebruiken van de NVA van de SD-WAN, inclusief hoge Beschik baarheid, schaal baarheid en route ring.
  
## <a name="managed-hybrid-wan-model"></a><a name="hybrid"></a>Beheerd hybride WAN-model

:::image type="content" source="./media/sd-wan-connectivity-architecture/hybrid.png" alt-text="Beheerd hybride WAN-model":::

In dit architectuur model kunnen ondernemingen gebruikmaken van een beheerde SD-WAN-service die wordt aangeboden door een begeleide MSP-partner (service provider). Dit model is vergelijkbaar met de directe of indirecte modellen die hierboven worden beschreven. In dit model worden het ontwerp, de indeling en de bewerkingen van het SD-WAN echter geleverd door de SD-WAN-provider.

[MSP-partners van Azure-netwerken](../networking/networking-partners-msp.md) kunnen [Azure Lighthouse](https://azure.microsoft.com/services/azure-lighthouse/) gebruiken voor het implementeren van de SD-WAN-en Virtual WAN-service in het Azure-abonnement van de Enter prise-klant, evenals voor het uitvoeren van de end-to-end Hybrid WAN namens de klant. Deze Msp's kunnen ook Azure ExpressRoute in het virtuele WAN implementeren en dit als een end-to-end beheerde service uitvoeren.

## <a name="additional-information"></a>Aanvullende informatie

* [Veelgestelde vragen toevoegen](virtual-wan-faq.md)
* [Externe connectiviteit oplossen](work-remotely-support.md)