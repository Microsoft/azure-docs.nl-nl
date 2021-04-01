---
title: Interconnect met China met behulp van Azure Virtual WAN en Secure hub
description: Meer informatie over hoe u verbinding met China maakt met behulp van Azure Virtual WAN en een beveiligde hub.
services: virtual-wan
author: skishen525
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 12/01/2020
ms.author: sukishen
ms.openlocfilehash: 169477ffdf6fd593f1b6f01469c7303f8bd3a488
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96531834"
---
# <a name="interconnect-with-china-using-azure-virtual-wan-and-secure-hub"></a>Interconnect met China met behulp van Azure Virtual WAN en Secure hub

Bij het bekijken van veelvoorkomende auto's, productie, logistiek industrieën of andere instituten zoals embassies, is er vaak een vraag over hoe u de onderlinge verbinding met China kunt verbeteren. Deze verbeteringen zijn vooral relevant voor het gebruik van Cloud Services zoals Microsoft 365, Azure Global Services of Interconnect branches in China met een klant-backbone.

In de meeste gevallen zijn klanten lastig met hoge latentie, lage band breedte, Insta Biel verbinding en hoge kosten die verbinding maken met buiten China (bijvoorbeeld Europe of de Verenigde Staten).

Een reden hiervoor is de ' fantastische firewall van China ', waarmee het Chinese deel van het Internet wordt beschermd en verkeer wordt gefilterd op China. Bijna al het verkeer dat vanuit de volks Republiek China naar buiten China loopt, wordt de fantastische Firewall door gegeven, met uitzonde ring van de speciale beheer zones zoals Hong Kong en Macao. Het verkeer dat wordt uitgevoerd via Hong Kong en Macao heeft niet de volledige firewall, en wordt afgehandeld door een subset van de fantastische firewall.

:::image type="content" source="./media/interconnect-china/provider.png" alt-text="Diagram geeft de Interconnect van de provider weer.":::

Met behulp van Virtual WAN kan een klant een meer presteertde en stabiele verbinding tot stand brengen met Microsoft Cloud Services en een verbinding met het bedrijfs netwerk zonder de Chinese cyber beveiliging wet te verbreken.

## <a name="requirements-and-workflow"></a><a name="requirements"></a>Vereisten en werk stroom

Als u wilt blijven voldoen aan het Chinees-Cyber beveiliging recht, moet u aan een aantal bepaalde voor waarden voldoet.

Eerst moet u samen werken met een netwerk en ISP die eigenaar is van een ICP-licentie (Internet-inhouds provider) voor China. In de meeste gevallen kunt u een van de volgende providers beëindigen:

* China Telecom wereld Ltd.
* China Mobile Ltd.
* China Unicom Ltd.
* PCCW Global Ltd.
* Hong Kong Telecom Ltd.

Afhankelijk van de provider en uw behoeften moet u nu een van de volgende services voor netwerk connectiviteit aanschaffen om uw branches in China te verbinden.

* Een MPLS/IPVPN-netwerk 
* Een software-gedefinieerde WAN (SDWAN)
* Speciale internet toegang

Vervolgens moet u akkoord gaan met die provider om een groepen te geven aan het wereld wijde netwerk van micro soft en de rand Netwerk-inkomend Hongkong, niet in Beijing of Shanghai. In dit geval is Hong Kong zeer belang rijk vanwege de fysieke verbinding en de locatie naar China.

De meeste klanten denken dat het gebruik van Singapore voor Interconnect het beste is omdat het bij het zoeken in de kaart lijkt op China, dit is niet waar. Wanneer u netwerk glasvezel kaarten volgt, gaan bijna alle netwerk verbindingen via Beijing, Shanghai en Hong Kong. Dit maakt Hong Kong een betere locatie keuze om verbinding te maken met China.

Afhankelijk van de provider, kunt u verschillende service aanbiedingen ontvangen. In de volgende tabel ziet u een voor beeld van providers en de service die ze aanbieden, op basis van informatie op het moment dat dit artikel is geschreven.

| Service | Voor beelden van providers |
| --- | --- |
| MPLS/IPVPN-netwerk |PCCW, China Telecom Global |
|SDWAN| PCCW, China Telecom Global|
| Speciale internet toegang | PCCW, Hong Kong Telecom, China beschikbaar|

Met uw provider kunt u akkoord gaan met de volgende twee oplossingen die moeten worden gebruikt om de globale backbone van micro soft te bereiken:

* Het ophalen van een Microsoft Azure ExpressRoute die is beëindigd in Hong Kong. Dit is het geval voor het gebruik van MPLS/IPVPN. Op dit moment is alleen de enige ICP-licentie provider met ExpressRoute aan Hong Kong het globale China Telecom. Ze kunnen echter ook communiceren met de andere providers als ze gebruikmaken van Cloud Exchange-providers zoals Mega Port of intercloud. Zie [ExpressRoute Connectivity providers](../expressroute/expressroute-locations-providers.md#partners)(Engelstalig) voor meer informatie.

* Het gebruik van een speciale internet toegang rechtstreeks op een van de volgende Internet-Exchange punten of met een VPN-verbinding met een particulier netwerk.

De volgende lijst bevat de Internet-uitwisselingen die mogelijk zijn in Hongkong:

* AMS-IX Hong Kong
* BBIX Hong Kong
* Equinix Hong Kong
* HKIX

Wanneer u deze verbinding maakt, moet uw volgende BGP-hop voor micro soft-Services micro soft autonoom systeem nummer zijn (als #) 8075. Als u één locatie of SDWAN-oplossing gebruikt, is dat de keuze van de verbinding.

Met de huidige wijzigingen ten aanzien van de onderlinge verbinding tussen China en Hong Kong SAR, bouwen de meeste van deze netwerk providers een MPLS-brug tussen China en Hong Kong SAR.

U kunt zien dat site-naar-site-VPN-verbindingen binnen China zijn toegestaan en meestal stabiel zijn. Dit geldt ook voor de site-naar-site-verbindingen tussen filialen in de rest van de wereld. Providers maken nu een VPN-SDWAN aggregatie aan beide zijden en brug via MPLS ertussen.

:::image type="content" source="./media/interconnect-china/china-mpls-bridge.png" alt-text="Diagram toont de MPLS-brug van China.":::

In beide gevallen is het raadzaam dat u een tweede en een normale Internet-groepen in China hebt. Dit is het verdelen van het verkeer tussen ondernemings verkeer naar Cloud Services zoals Microsoft 365 en Azure, en door de wet gereguleerd Internet verkeer.

Een compatibele netwerk architectuur in China kan er als volgt uitzien:

:::image type="content" source="./media/interconnect-china/multi-branch.png" alt-text="Diagram toont meerdere branches.":::

In dit voor beeld hebt u een verbinding met de micro soft Global Netwerk-inkomend Hongkong, u kunt nu gebruikmaken van de [Azure Virtual WAN Global Transit-architectuur](virtual-wan-global-transit-network-architecture.md) en extra services, zoals Azure Secure Virtual WAN hub, om services en connecties te kunnen gebruiken voor uw filialen en Data Center buiten China.

## <a name="hub-to-hub-communication"></a><a name="hub-to-hub"></a>Hub-naar-hub-communicatie

In deze sectie gebruiken we Virtual WAN hub-to-hub-communicatie met Interconnect. In dit scenario maakt u een nieuwe virtuele WAN-hub-resource om verbinding te maken met een virtuele WAN-hub in Hongkong, andere regio's die u wilt, een regio waar u al Azure-resources hebt of waar u verbinding mee wilt maken.

Een voor beeld van een architectuur kan er als volgt uitzien:

:::image type="content" source="./media/interconnect-china/sample.png" alt-text="Diagram toont een voor beeld van een WAN.":::

In dit voor beeld maken de branches in China verbinding met Azure Cloud China en elkaar met behulp van VPN-of MPLS-verbindingen. Vertakkingen die moeten worden verbonden met globale Services, maken gebruik van MPLS of op internet gebaseerde services die rechtstreeks zijn verbonden met Hong Kong. Als u ExpressRoute wilt gebruiken in Hong Kong en in de andere regio, moet u [ExpressRoute Global Reach](../expressroute/expressroute-global-reach.md) configureren om beide ExpressRoute-circuits te verbinden.

ExpressRoute Global Reach is in sommige regio's niet beschikbaar. Als u met Brazilië of India wilt verbinden, moet u bijvoorbeeld gebruikmaken van [Cloud Exchange-providers](../expressroute/expressroute-locations.md#connectivity-through-exchange-providers) om de routerings services te bieden.

In de afbeelding hieronder ziet u voor beelden van dit scenario.

:::image type="content" source="./media/interconnect-china/global.png" alt-text="Diagram toont Global Reach.":::

## <a name="secure-internet-breakout-for-microsoft-365"></a><a name="secure"></a>Beveiligde Internet-groepen voor Microsoft 365

Een andere overweging is netwerk beveiliging en logboek registratie voor het toegangs punt tussen China en het backbone-onderdeel virtuele WAN-verbinding, en de klant-backbone. In de meeste gevallen is het groepen van het internet in Hong Kong nodig om het micro soft Edge-netwerk rechtstreeks te bereiken en, met de Azure front-deur servers die worden gebruikt voor Microsoft 365 Services.

Voor beide scenario's met virtuele WAN maakt u gebruik van de [Azure Virtual WAN beveiligde hub](../firewall-manager/secured-virtual-hub.md). Met Azure Firewall Manager kunt u een normale virtuele WAN-hub wijzigen in een beveiligde hub en vervolgens een Azure Firewall binnen die hub implementeren en beheren.

In de volgende afbeelding ziet u een voor beeld van dit scenario:

:::image type="content" source="./media/interconnect-china/internet.png" alt-text="Diagram toont Internet-groepen voor het verkeer van de web-en micro soft-Services.":::

## <a name="architecture-and-traffic-flows"></a><a name="traffic"></a>Architectuur en verkeers stromen

Afhankelijk van uw keuze met betrekking tot de verbinding met Hong Kong, kan de algehele architectuur enigszins veranderen. In deze sectie worden drie beschik bare architecturen weer gegeven in een andere combi natie met VPN of SDWAN en/of ExpressRoute.

Al deze opties maken gebruik van Azure Virtual WAN beveiligde hub voor rechtstreekse Microsoft 365 connectiviteit in Hong Kong. Deze architecturen bieden ook ondersteuning voor de nalevings vereisten voor [Microsoft 365 multi-geo](/microsoft-365/enterprise/microsoft-365-multi-geo) en houden dat verkeer in de buurt van de volgende locatie van de Azure front-deur. Als gevolg hiervan is het ook een verbetering van het gebruik van Microsoft 365 uit China.

Wanneer u Azure Virtual WAN gebruikt in combi natie met Internet verbindingen, kan elke verbinding profiteren van extra services zoals [Microsoft Azure peering-Services (Maps)](../peering-service/about.md). KAARTEN is gebouwd voor het optimaliseren van verkeer dat afkomstig is van het wereld wijde netwerk van derden van Internet serviceproviders.

### <a name="option-1-sdwan-or-vpn"></a><a name="option-1"></a>Optie 1: SDWAN of VPN

In deze sectie wordt een ontwerp besproken dat gebruikmaakt van SDWAN of VPN naar Hong Kong en andere branches. Met deze optie worden het gebruik en de verkeers stroom weer gegeven wanneer u een zuivere Internet verbinding gebruikt op beide sites van de virtuele WAN-backbone. In dit geval wordt de verbinding met Hong Kong tot stand gebracht met behulp van speciale internet toegang of een SDWAN-oplossing van ICP-provider. Andere branches gebruiken ook zuivere Internet-of SDWAN-oplossingen.

:::image type="content" source="./media/interconnect-china/china-traffic.png" alt-text="Diagram toont China voor Hong Kong-verkeer.":::

In deze architectuur wordt elke site verbonden met het wereld wijde micro soft-netwerk met behulp van VPN en Azure Virtual WAN. Het verkeer tussen de sites en Hong Kong wordt verzonden via het micro soft-netwerk en maakt in de afgelopen mijl alleen gebruik van normale Internet verbinding.

### <a name="option-2-expressroute-and-sdwan-or-vpn"></a><a name="option-2"></a>Optie 2: ExpressRoute en SDWAN of VPN

In deze sectie wordt een ontwerp besproken dat gebruikmaakt van ExpressRoute in Hong Kong en andere branches met VPN/SDWAN-filialen. Met deze optie wordt het gebruik van en ExpressRoute beëindigd in Hong Kong en andere vertakkingen die zijn verbonden via SDWAN of VPN. ExpressRoute in Hongkong is momenteel beperkt tot een korte lijst met providers, die u kunt vinden in de lijst met [Express route-partners](../expressroute/expressroute-locations-providers.md#global-commercial-azure).

:::image type="content" source="./media/interconnect-china/expressroute.png" alt-text="In het diagram wordt China weer gegeven voor Hong Kong Traffic-ExpressRoute.":::

Er zijn ook opties om ExpressRoute te beëindigen vanuit China, bijvoorbeeld in Zuid-Korea of Japan. Maar gezien de naleving, de regelgeving en de latentie is Hong Kong de beste keuze.

### <a name="option-3-expressroute-only"></a><a name="option-3"></a>Optie 3: alleen ExpressRoute

In deze sectie wordt een ontwerp beschreven waarin ExpressRoute wordt gebruikt voor Hong Kong en andere branches. Met deze optie wordt de interconnect met ExpressRoute aan beide kanten weer gegeven. Hier hebt u een andere verkeers stroom dan de andere. Het Microsoft 365 verkeer loopt over naar de Azure Virtual WAN beveiligde hub en van daaruit naar het micro soft Edge-netwerk en het internet.

Het verkeer dat naar de onderling verbonden branches of van deze naar de locaties in China gaat, volgt een andere benadering binnen die architectuur. Momenteel biedt Virtual WAN geen ondersteuning voor ExpressRoute naar ExpressRoute transit. Het verkeer maakt gebruik van ExpressRoute Global Reach of de Interconnect van derden zonder dat de virtuele WAN-hub wordt door gegeven. Het gaat rechtstreeks van de ene micro soft Enter prise Edge (MSEE) naar de andere.

:::image type="content" source="./media/interconnect-china/expressroute-virtual.png" alt-text="Diagram toont ExpressRoute Global Reach.":::

Momenteel is ExpressRoute Global Reach niet beschikbaar in elk land/elke regio, maar u kunt een oplossing configureren met behulp van Azure Virtual WAN.

U kunt bijvoorbeeld een ExpressRoute configureren met micro soft-peering en een VPN-tunnel verbinden via die peering met Azure Virtual WAN. U hebt nu opnieuw de door Voer tussen VPN-en ExpressRoute ingeschakeld, zonder Global Reach en provider en service van derden, zoals Mega Port Cloud.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor meer informatie:

* [Wereld wijde doorvoer netwerk architectuur met Azure Virtual WAN](virtual-wan-global-transit-network-architecture.md)

* [Een virtuele WAN-hub maken](virtual-wan-site-to-site-portal.md)

* [Een virtuele WAN-beveiligde hub configureren](../firewall-manager/secure-cloud-network.md)

* [Overzicht van de preview-versie van Azure peering service](../peering-service/about.md)