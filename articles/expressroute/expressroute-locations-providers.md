---
title: 'Locaties en connectiviteitsproviders: Azure ExpressRoute | Microsoft Docs'
description: Dit artikel bevat een gedetailleerd overzicht van de locaties waar services worden aangeboden en hoe u verbinding maakt met Azure-regio's. Gesorteerd op locatie.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/10/2021
ms.author: duau
ms.openlocfilehash: 5972e95425513e789725ed8de142c721d58011f3
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739922"
---
# <a name="expressroute-partners-and-peering-locations"></a>Partners en peeringlocaties voor ExpressRoute

> [!div class="op_single_selector"]
> * [Locaties per provider](expressroute-locations.md)
> * [Providers per locatie](expressroute-locations-providers.md)


De tabellen in dit artikel bevatten informatie over geografische dekking en locaties van ExpressRoute, ExpressRoute-connectiviteitsproviders en ExpressRoute System Integrators (SIs).

> [!Note]
> Azure-regio's en ExpressRoute-locaties zijn twee verschillende concepten. Het verschil tussen de twee is essentieel voor het verkennen van de connectiviteit van hybride Azure-netwerken. 
>
>

## <a name="azure-regions"></a>Azure-regio's
Azure-regio's zijn wereldwijde datacenters waar Azure-reken-, netwerk- en opslagbronnen zich bevinden. Bij het maken van een Azure-resource moet een klant een resourcelocatie selecteren. De resourcelocatie bepaalt in welk Azure-datacenter (of beschikbaarheidszone) de resource wordt gemaakt.

## <a name="expressroute-locations"></a>ExpressRoute-locaties
ExpressRoute-locaties (soms peeringlocaties of meet-me-locaties genoemd) zijn co-locatie-faciliteiten waar MSEE-apparaten (Microsoft Enterprise Edge) zich bevinden. ExpressRoute-locaties zijn het toegangspunt voor het netwerk van Microsoft en zijn wereldwijd gedistribueerd, zodat klanten overal ter wereld verbinding kunnen maken met het netwerk van Microsoft. Deze locaties zijn waar ExpressRoute-partners en ExpressRoute Direct verbinding maken met het netwerk van Microsoft. Over het algemeen hoeft de ExpressRoute-locatie niet overeen te komen met de Azure-regio. Een klant kan bijvoorbeeld een ExpressRoute-circuit maken met de resourcelocatie VS - *oost,* op de peeringlocatie *Seattle.*

U hebt toegang tot Azure-services in alle regio's binnen een geopolitieke regio als u bent verbonden met ten minste één ExpressRoute-locatie in die geopolitieke regio. 

## <a name="azure-regions-to-expressroute-locations-within-a-geopolitical-region"></a><a name="locations"></a>Azure-regio's naar ExpressRoute-locaties binnen een geopolitieke regio
In de volgende tabel vindt u een toewijzing van Azure-regio's aan ExpressRoute-locaties binnen een geopolitieke regio.

| **Geopolitieke regio** | **Azure-regio's** | **ExpressRoute-locaties** |
| --- | --- | --- |
| **Australië - overheid** | Australië Centraal, Australië Centraal 2 |Canberra, Canberra2 |
| **Europa** | Frankrijk - centraal, Frankrijk - zuid, Duitsland - noord, Duitsland - west-centraal, Europa - noord, Noorwegen - oost, Noorwegen - west, Zwitserland - noord, Zwitserland - west, VK - west, VK - zuid, Europa - west |Amsterdam, Amsterdam2, Amsterdam, Parijs, Dublin, Dublin, Wales2, Genève, Londen, Londen2, Laten, Laten, Cf, Nulen, Newport(Wales), Parijs, Stavanger, Keer, Zique |
| **Noord-Amerika** | VS - oost, VS - west, VS - oost 2, VS - west 2, VS - centraal, VS - zuid-centraal, VS - noord-centraal, VS - west-centraal, Canada - centraal, Canada - oost |Atlanta, Chicago, Dallas, Denver, Las Vegas, Los Angeles, Los Angeles2, Miami, Minneapolis, Montreal, New York, Phoenix, Quebec City, Queretaro(Mexico), Quincy, San Antonio, Seattle, Silicon Valley, Silicon Valley2, Toronto, Toronto2, Vancouver, Washington DC, Washington DC2 |
| **Azië** | Azië - oost, Azië - zuidoost | Hongkong, Hongkong, Hongkong2, Laten, Lump Lumpur, Singapore, Singapore2, |
| **India** | India - west, India - centraal, India - zuid |Chennai, Chennai2, Mumbai, Mumbai2 |
| **Japan** | Japan - west, Japan - oost |Osaka, Tokio, Tokio2 |
| **Oceanië** | Australië - zuidoost, Australië - oost |Auckland, Melbourne, Perth, Sydney, Sydney2 | 
| **Zuid-Korea** | Korea - centraal, Korea - zuid |Busan, Seoul|
| **Uae** | VAE - centraal, VAE - noord | Moet, Moet2 |
| **Zuid-Afrika** | Zuid-Afrika - west, Zuid-Afrika - noord |Kaapstad, Johannesburg |
| **Zuid-Amerika** | Brazilië - zuid |Pseudoota, Rio de Jaota, Sao Paulo |

## <a name="azure-regions-and-geopolitical-boundaries-for-national-clouds"></a>Azure-regio's en geopolitieke grenzen voor nationale clouds
De volgende tabel bevat informatie over regio's en geopolitieke grenzen voor nationale clouds.

| **Geopolitieke regio** | **Azure-regio's** | **ExpressRoute-locaties** |
| --- | --- | --- |
| **Cloud van de Amerikaanse overheid** |US Gov - AZ, US Gov - Iowa, US Gov - TX, US Gov - Virginia, US DoD Central, US DoD East  |Atlanta,Chicago, Dallas, New York, Phoenix, San Antonio, Seattle, Silicon Valley, Washington DC |
| **China East** |China - oost, China - oost2 |Shanghai, Shanghai2 |
| **China - noord** |China - noord, China - noord2 |Beijing, Beijing2 |
| **Duitsland** |Duitsland Centraal, Duitsland Oost |Berlijn, Frankfurt |

Connectiviteit tussen de geopolitieke regio's wordt niet ondersteund op de standaard ExpressRoute-SKU. U moet de invoegtoepassing ExpressRoute Premium inschakelen voor ondersteuning van globale connectiviteit. Connectiviteit met nationale cloudomgevingen wordt niet ondersteund. U kunt met uw connectiviteitsprovider samenwerken als de noodzaak daartoe zich voordoet.

## <a name="expressroute-connectivity-providers"></a><a name="partners"></a>ExpressRoute-connectiviteitsproviders

De volgende tabel geeft de connectiviteitslocaties en de serviceproviders voor elke locatie weer. Als u serviceproviders en de locaties waarvoor zij deze service kunnen bieden, wilt bekijken, raadpleegt u [Locaties per serviceprovider](expressroute-locations.md).

* **Lokale Azure-regio's** zijn de regio's die lokaal zijn voor [ExpressRoute](expressroute-faqs.md) op elke peeringlocatie. **n/a** geeft aan dat ExpressRoute Local niet beschikbaar is op die peeringlocatie.

* **Zone** verwijst naar [prijzen](https://azure.microsoft.com/pricing/details/expressroute/).


### <a name="global-commercial-azure"></a>Wereldwijd commercieel Azure
| **Locatie** | **Adres** | **Zone** | **Lokale Azure-regio's** | **ER Direct** | **Dienstverleners** |
| --- | --- | --- | --- | --- | --- |
| **Amsterdam** | [Equinix AM5](https://www.equinix.com/locations/europe-colocation/netherlands-colocation/amsterdam-data-centers/am5/) | 1 | Europa -west | 10G, 100G | Aryaka Networks, AT&T NetBond, British Telecom, Colt, Equinix, euNetworks, GÉANT, InterCloud, Interxion, KPN, IX Reach, Level 3 Communications, Megaport, NTT Communications, Orange, Tata Communications, Telefonica, Telenor, Telia Carrier, Verizon, Zayo |
| **Amsterdam2** | [Interxion AMS8](https://www.interxion.com/Locations/amsterdam/schiphol/) | 1 | Europa -west | 10G, 100G | British Telecom, CenturyLink Cloud Connect, Colt, DE-CIX, Equinix, euNetworks, GÉANT, Interxion, NOS, NTT Global DataCenters EMEA, Orange, Vodafone |
| **Atlanta** | [Equinix AT2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/atlanta-data-centers/at2/) | 1 | n.v.t. | 10G, 100G | Equinix, Megaport |
| **Auckland** | [Vocus Group NZ Albany](https://www.vocus.co.nz/business/cloud-data-centres) | 2 | n.v.t. | 10G | Devoli, Kordia, Megaport, REANNZ, Spark NZ, Vocus Group NZ |
| **Bangkok** | [Ais](https://business.ais.co.th/solution/en/azure-expressroute.html) | 2 | n.v.t. | 10G | AIS, UIH |
| **Berlijn** | [NTT GDC](https://www.e-shelter.de/en/location/berlin-1-data-center) | 1 | Duitsland - noord | 10G | Colt, Equinix, NTT Global DataCenters EMEA|
| **Bogota** | [Equinix BG1](https://www.equinix.com/locations/americas-colocation/colombia-colocation/bogota-data-centers/bg1/) | 4 | n.v.t. | 10G | Equinix |
| **Busan** | [LG CNS](https://www.lgcns.com/En/Service/DataCenter) | 2 | Korea - zuid | n.v.t. | LG CNS |
| **Canberra** | [CDC](https://cdcdatacentres.com.au/about-us/) | 1 | Australië - centraal | 10G, 100G | CDC |
| **Canberra2** | [CDC](https://cdcdatacentres.com.au/about-us/) | 1 | Australië - centraal 2| 10G, 100G | CDC, Equinix |
| **Kaapstad** | [Teraco CT1](https://www.teraco.co.za/data-centre-locations/cape-town/) | 3 | Zuid-Afrika - west | 10G | BCX, Internet Solutions - Cloud Connect, Liquid Telecom, Teraco |
| **Chennai** | Tata Communications | 2 | India - zuid | 10G | BSNL, Global CloudXchange (GCX), SIFY, Tata Communications, VodafoneIdea |
| **Chennai2** | Airtel | 2 | India - zuid | 10G | Airtel |
| **Chicago** | [Equinix CH1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/chicago-data-centers/ch1/) | 1 | VS - noord-centraal | 10G, 100G | Aryaka Networks, AT&T NetBond, British Telecom, CenturyLink Cloud Connect, Cologix, Colt, Comcast, Coresite, Equinix, InterCloud, Internet2, Level 3 Communications, Megaport, PacketFabric, PCCW Global Limited, Sprint, Telia Carrier, Verizon, Zayo |
| **Kopenhagen** | [Interxion CPH1](https://www.interxion.com/Locations/copenhagen/) | 1 | n.v.t. | 10G | Interxion |
| **Dallas** | [Equinix DA3](https://www.equinix.com/locations/americas-colocation/united-states-colocation/dallas-data-centers/da3/) | 1 | n.v.t. | 10G, 100G | Aryaka Networks, AT&T NetBond, Cologix, Equinix, Internet2, Level 3 Communications, Megaport, Neutrona Networks, PacketFabric, Telmex Uninet, Telia Carrier, Transtelco, Verizon, Zayo|
| **Denver** | [CoreSite DE1](https://www.coresite.com/data-centers/locations/denver/de1) | 1 | VS - west-centraal | n.v.t. | CoreSite, Megaport, Zayo |
| **Dubai** | [PCCS](https://www.pacificcontrols.net/cloudservices/index.html) | 3 | VAE - noord | n.v.t. | Etisalat UAE |
| **2** | [du datamena](http://datamena.com/solutions/data-centre) | 3 | VAE - noord | n.v.t. | DE-CIX, du datamena, Equinix, Megaport, Orange, Orixcom |
| **Dublin** | [Equinix DB3](https://www.equinix.com/locations/europe-colocation/ireland-colocation/dublin-data-centers/db3/) | 1 | Europa - noord | 10G, 100G | CenturyLink Cloud Connect, Colt, eir, Equinix, GEANT, euNetworks, Interxion, Megaport |
| **Frankfurt** | [Interxion FRA11](https://www.interxion.com/Locations/frankfurt/) | 1 | Duitsland - west-centraal | 10G, 100G | AT&T NetBond, CenturyLink Cloud Connect, Colt, DE-CIX, Equinix, euNetworks, GEANT, InterCloud, Interxion, Megaport, Orange, Telia Carrier, T-Systems |
| **2** | [Equinix FR7](https://www.equinix.com/locations/europe-colocation/germany-colocation/frankfurt-data-centers/fr7/) | 1 | Duitsland - west-centraal | 10G, 100G | Equinix |
| **Genève** | [Equinix GV2](https://www.equinix.com/locations/europe-colocation/switzerland-colocation/geneva-data-centers/gv2/) | 1 | Zwitserland - west | 10G, 100G | Equinix, Megaport, Equicom |
| **Hongkong** | [Equinix HK1](https://www.equinix.com/locations/asia-colocation/hong-kong-colocation/hong-kong-data-center/hk1/) | 2 | Azië - oost | 10G | Aryaka Networks, British Telecom, CenturyLink Cloud Connect, Chief Telecom, China Telecom Global, China Unicom, Colt, Equinix, InterCloud, Megaport, NTT Communications, Orange, PCCW Global Limited, Tata Communications, Telia Carrier, Verizon |
| **Hongkong2** | [iAdvantage MEGA-i](https://www.iadvantage.net/index.php/locations/mega-i) | 2 | Azië - oost | 10G | China Mobile International, China Telecom Global, iAdvantage, Megaport, PCCW Global Limited, SingTel |
| **Jakarta** | Telin, Telkom Indonesia | 4 | n.v.t. | 10G | Telin |
| **Johannesburg** | [Teraco JB1](https://www.teraco.co.za/data-centre-locations/johannesburg/#jb1) | 3 | Zuid-Afrika - noord | 10G | BCX, British Telecom, Internet Solutions - Cloud Connect, Liquid Telecom, Orange, Teraco |
| **Kuala Lumpur** | [TIME dotCom Menara AIMS](https://www.time.com.my/enterprise/connectivity/direct-cloud) | 2 | n.v.t. | n.v.t. | TIME dotCom |
| **Las Vegas** | [LV wisselen](https://www.switch.com/las-vegas) | 1 | n.v.t. | 10G, 100G | CenturyLink Cloud Connect, Megaport, PacketFabric |
| **Londen** | [Equinix LD5](https://www.equinix.com/locations/europe-colocation/united-kingdom-colocation/london-data-centers/ld5/) | 1 | Verenigd Koninkrijk Zuid | 10G, 100G | AT&T NetBond, British Telecom, Colt, Equinix, euNetworks, InterCloud, Internet Solutions - Cloud Connect, Interxion, Jisc, Level 3 Communications, Megaport, MTN, NTT Communications, Orange, PCCW Global Limited, Tata Communications, Telehouse - KDDI, Telenor, Telia Carrier, Verizon, Vodafone, Zayo |
| **London2** | [Telehouse North Two](https://www.telehouse.net/data-centres/emea/uk-data-centres/london-data-centres/north-two) | 1 | Verenigd Koninkrijk Zuid | 10G, 100G | British Telecom, CenturyLink Cloud Connect, Colt, GTT, IX Reach, Equinix, Megaport, SES, Sohonet, Telehouse - KDDI |
| **Los Angeles** | [CoreSite LA1](https://www.coresite.com/data-centers/locations/los-angeles/one-wilshire) | 1 | n.v.t. | 10G, 100G | CoreSite, Equinix, Megaport, Neutrona Networks, NTT, Zayo |
| **Los Angeles2** | [Equinix LA1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/los-angeles-data-centers/la1/) | 1 | n.v.t. | 10G, 100G | Equinix |
| **Madrid** | [InterxionCHU1](https://www.interxion.com/es/donde-estamos/europa/madrid) | 1 | Europa -west | 10G, 100G | Interxion |
| **Marseille** |[Interxion MRS1](https://www.interxion.com/Locations/marseille/) | 1 | Frankrijk - zuid | n.v.t. | DE-CIX, GEANT, Interxion, Jaguar Network, Ooredoo Cloud Connect |
| **Melbourne** | [NextDC M1](https://www.nextdc.com/data-centres/m1-melbourne-data-centre) | 2 | Australië - zuidoost | 10G, 100G | AARNet, Devoli, Equinix, Megaport, NEXTDC, Optus, Telstra Corporation, TPG Telecom |
| **Miami** | [Equinix MI1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/miami-data-centers/mi1/) | 1 | n.v.t. | 10G, 100G | Claro, C3ntro, Equinix, Megaport, Neutrona Networks |
| **Milaan** | [IRIDEOS](https://irideos.it/en/data-centers/) | 1 | n.v.t. | 10G | Colt, Equinix, Fastweb, IRIDEOS, Retelit |
| **Minneapolis** | [Cologix MIN1](https://www.cologix.com/data-centers/minneapolis/min1/) | 1 | n.v.t. | 10G, 100G | Cologix, Megaport |
| **Montreal** | [Cologix MTL3](https://www.cologix.com/data-centers/montreal/mtl3/) | 1 | n.v.t. | 10G, 100G | Bell Canada, Cologix, Fibrenoire, Megaport, Telus, Zayo |
| **Mumbai** | Tata Communications | 2 | India - west | 10G | BSNL, DE-CIX, Global CloudXchange (GCX), Reliance Jio, Sify, Tata Communications, Verizon |
| **Mumbai2** | Airtel | 2 | India - west | 10G | Airtel, Sify, Vodafone Idea |
| **München** | [EdgeConneX](https://www.edgeconnex.com/locations/europe/munich/) | 1 | n.v.t. | 10G | DE-CIX |
| **New York** | [Equinix NY9](https://www.equinix.com/locations/americas-colocation/united-states-colocation/new-york-data-centers/ny9/) | 1 | n.v.t. | 10G, 100G | CenturyLink Cloud Connect, Colt, Coresite, DE-CIX, Equinix, InterCloud, Megaport, Packet, Zayo |
| **Newport (Wales)** | [Next Generation Data](https://www.nextgenerationdata.co.uk) | 1 | Verenigd Koninkrijk West | n.v.t. | British Telecom, Colt, Jisc, Level 3 Communications, Next Generation Data |
| **Osaka** | [Equinix OS1](https://www.equinix.com/locations/asia-colocation/japan-colocation/osaka-data-centers/os1/) | 2 | Japan - west | 10G, 100G | AT TOKIO, Colt, Equinix, Internet Initiative Japan Inc. - IIJ, Megaport, NTT Communications, NTT SmartConnect, Softbank, Tokai Communications |
| **Oslo** | [DigiPlex Ulven](https://www.digiplex.com/locations/oslo-datacentre) | 1 | Noorwegen - oost | 10G, 100G | GlobalConnect, Megaport, Telenor, Telia Carrier |
| **Parijs** | [Interxion PAR5](https://www.interxion.com/Locations/paris/) | 1 | Frankrijk - centraal | 10G, 100G | British Telecom, CenturyLink Cloud Connect, Colt, Equinix, Intercloud, Interxion, Jaguar Network, Megaport, Orange, Telia Carrier, Zayo |
| **Perth** | [NextDC P1](https://www.nextdc.com/data-centres/p1-perth-data-centre) | 2 | n.v.t. | 10G | Megaport, NextDC |
| **Phoenix** | [EdgeConneX PHX01](https://www.edgeconnex.com/locations/north-america/phoenix-az/) | 1 | n.v.t. | 10G, 100G | |
| **Quebec** | [Vantage](https://vantage-dc.com/data_centers/quebec-city-data-center-campus/) | 1 | Canada - oost | 10G, 100G | Bell Canada, Megaport, Telus |
| **Queretaro (Mexico)** | [KIO Networks QR01](https://www.kionetworks.com/es-mx/) | 4 | n.v.t. | 10G | Transtelco|
| **Quincy** | [Hadoey Datacenter - Gebouw A](https://sabeydatacenters.com/data-center-locations/central-washington-data-centers/quincy-data-center) | 1 | VS - west 2 | 10G, 100G | |
| **Rio de Janeiro, Brazilië** | [Equinix-RJ2](https://www.equinix.com/locations/americas-colocation/brazil-colocation/rio-de-janeiro-data-centers/rj2/) | 3 | Brazilië - zuidoost | 10G | Equinix |
| **San Antonio** | [HadoesOne SA1](https://cyrusone.com/locations/texas/san-antonio-texas/) | 1 | VS - zuid-centraal | 10G, 100G | CenturyLink Cloud Connect, Megaport |
| **Sao Paulo** | [Equinix SP2](https://www.equinix.com/locations/americas-colocation/brazil-colocation/sao-paulo-data-centers/sp2/) | 3 | Brazilië - zuid | 10G, 100G | Aryaka Networks, Ascenty Data Centers, British Telecom, Equinix, Level 3 Communications, Neutrona Networks, Orange, Tata Communications, Telefonica, UOLDIVEO |
| **Seattle** | [Equinix SE2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/seattle-data-centers/se2/) | 1 | VS - west 2 | 10G, 100G | Aryaka Networks, Equinix, Level 3 Communications, Megaport, Telus, Zayo |
| **Seoul** | [KINX Gasan IDC](https://www.kinx.net/?lang=en) | 2 | Korea - centraal | 10G, 100G | KINX, KT, LG CNS, Equinix, Sejong Telecom |
| **Silicon Valley** | [Equinix SV1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/silicon-valley-data-centers/sv1/) | 1 | VS - west | 10G, 100G | Aryaka Networks, AT&T NetBond, British Telecom, CenturyLink Cloud Connect, Colt, Comcast, Coresite, Equinix, InterCloud, Internet2, IX Reach, Packet, PacketFabric, Level 3 Communications, Megaport, Orange, Sprint, Tata Communications, Telia Carrier, Verizon, Zayo |
| **Silicon Valley2** | [Coresite SV7](https://www.coresite.com/data-centers/locations/silicon-valley/sv7) | 1 | VS - west | 10G, 100G | Colt, Coresite | 
| **Singapore** | [Equinix SG1](https://www.equinix.com/locations/asia-colocation/singapore-colocation/singapore-data-center/sg1/) | 2 | Azië - zuidoost | 10G, 100G | Aryaka Networks, AT&T NetBond, British Telecom, China Mobile International, Epsilon Global Communications, Equinix, InterCloud, Level 3 Communications, Megaport, NTT Communications, Orange, SingTel, Tata Communications, Telstra Corporation, Verizon, Vodafone |
| **Singapore2** | [Global Switch Tai Seng](https://www.globalswitch.com/locations/singapore-data-centres/) | 2 | Azië - zuidoost | 10G, 100G | China Unicom Global, Colt, Epsilon Global Communications, Megaport, PCCW Global Limited, SingTel, Telehouse - KDDI |
| **Stavanger** | [Green Mountain DC1](https://greenmountain.no/dc1-stavanger/) | 1 | Noorwegen - west | 10G, 100G |GlobalConnect, Megaport |
| **Stockholm** | [Equinix SK1](https://www.equinix.com/locations/europe-colocation/sweden-colocation/stockholm-data-centers/sk1/) | 1 | n.v.t. | 10G | Equinix, Telia Carrier |
| **Sydney** | [Equinix SY2](https://www.equinix.com/locations/asia-colocation/australia-colocation/sydney-data-centers/sy2/) | 2 | Australië - oost | 10G, 100G | AARNet, AT&T NetBond, British Telecom, Devoli, Equinix, Kordia, Megaport, NEXTDC, NTT Communications, Optus, Orange, Spark NZ, Telstra Corporation, TPG Telecom, Verizon, Vocus Group NZ |
| **Sydney2** | [NextDC S1](https://www.nextdc.com/data-centres/s1-sydney-data-centre) | 2 | Australië - oost | 10G, 100G | Megaport, NextDC |
| **Taipei** | Chief Telecom | 2 | n.v.t. | 10G | Chief Telecom, Chunghwa Telecom, FarEasTone |
| **Tokio** | [Equinix TY4](https://www.equinix.com/locations/asia-colocation/japan-colocation/tokyo-data-centers/ty4/) | 2 | Japan - oost | 10G, 100G | Aryaka Networks, AT&T NetBond, BBIX, British Telecom, CenturyLink Cloud Connect, Colt, Equinix, Internet Initiative Japan Inc. - IIJ, Megaport, NTT Communications, NTT EAST, Orange, Softbank, Verizon |
| **Tokio2** | [AT TOKIO](https://www.attokyo.com/) | 2 | Japan - oost | 10G, 100G | AT TOKIO, Megaport, Tokai Communications |
| **Toronto** | [Cologix TOR1](https://www.cologix.com/data-centers/toronto/tor1/) | 1 | Canada - midden | 10G, 100G | AT&T NetBond, Bell Canada, CenturyLink Cloud Connect, Cologix, Equinix, IX Reach Megaport, Telus, Verizon, Zayo |
| **Toronto2** | [10-12-20](https://www.alliedreit.com/property/905-king-st-w/) | 1 | Canada - midden | 10G, 100G | |
| **Vancouver** | [Cologix VAN1](https://www.cologix.com/data-centers/vancouver/van1/) | 1 | n.v.t. | 10G | Cologix, Megaport, Telus |
| **Washington DC** | [Equinix DC2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/washington-dc-data-centers/dc2/) | 1 | VS - oost, VS - oost 2 | 10G, 100G | Aryaka Networks, AT&T NetBond, British Telecom, CenturyLink Cloud Connect, Cologix, Colt, Comcast, Coresite, Equinix, Internet2, InterCloud, Iron Mountain, IX Reach, Level 3 Communications, Megaport, Neutrona Networks, NTT Communications, Orange, PacketFabric, SES, Sprint, Tata Communications, Telia Carrier, Verizon, Zayo |
| **Washington DC2** | [Coresite Reston](https://www.coresite.com/data-center-locations/northern-virginia-washington-dc) | 1 | VS - oost, VS - oost 2 | 10G, 100G | CenturyLink Cloud Connect, Coresite, Intelsat, Megaport, Viasat, Zayo | 
| **Zürich** | [Interxion ZUR2](https://www.interxion.com/Locations/zurich/) | 1 | Zwitserland - noord | 10G, 100G | Colt, Equinix, Intercloud, Interxion, Megaport, Equicom |

 **+** geeft aan dat binnenkort beschikbaar is

### <a name="national-cloud-environments"></a>Nationale cloudomgevingen

Nationale Azure-clouds zijn geïsoleerd van elkaar en van wereldwijde commerciële Azure. ExpressRoute voor de ene Azure-cloud kan geen verbinding maken met de Azure-regio's in de andere.

### <a name="us-government-cloud"></a>Cloud van de Amerikaanse overheid
| **Locatie** | **Adres** | **Lokale Azure-regio's**| **ER Direct** | **Dienstverleners** |
| --- | --- | --- | --- | --- |
| **Atlanta** | [Equinix AT1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/atlanta-data-centers/at1/) | n.v.t. | 10G, 100G | Equinix |
| **Chicago** | [Equinix CH1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/chicago-data-centers/ch1/) | n.v.t. | 10G, 100G | AT&T NetBond, British Telecom, Equinix, Level 3 Communications, Verizon |
| **Dallas** | [Equinix DA3](https://www.equinix.com/locations/americas-colocation/united-states-colocation/dallas-data-centers/da3/) | n.v.t. | 10G, 100G | Equinix, Megaport, Verizon |
| **New York** | [Equinix NY5](https://www.equinix.com/locations/americas-colocation/united-states-colocation/new-york-data-centers/ny5/) | n.v.t. | 10G, 100G | Equinix, CenturyLink Cloud Connect, Verizon |
| **Phoenix** | [HadoenEneEn](https://cyrusone.com/locations/arizona/phoenix-arizona-chandler/) | VS (overheid) - Arizona | 10G, 100G | AT&T NetBond, CenturyLink Cloud Connect, Megaport |
| **San Antonio** | [HebtoEEN SA2](https://cyrusone.com/locations/texas/san-antonio-texas-ii/) | VS (overheid) - Texas | 10G, 100G | CenturyLink Cloud Connect, Megaport |
| **Silicon Valley** | [Equinix SV4](https://www.equinix.com/locations/americas-colocation/united-states-colocation/silicon-valley-data-centers/sv4/) | n.v.t. | 10G, 100G | AT&T, Equinix, Level 3 Communications, Verizon |
| **Seattle** | [Equinix SE2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/seattle-data-centers/se2/) | n.v.t. | 10G, 100G | Equinix, Megaport |
| **Washington DC** | [Equinix DC2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/washington-dc-data-centers/dc2/) | US DoD East, US Gov Virginia | 10G, 100G | AT&T NetBond, CenturyLink Cloud Connect, Equinix, Level 3 Communications, Megaport, Verizon |

### <a name="china"></a>China
| **Locatie** | **Adres** | **Lokale Azure-regio's** | **ER Direct** | **Dienstverleners** |
| --- | --- | --- | --- | --- |
| **Beijing** | China Telecom | n.v.t. | 10G | China Telecom |
| **Peking2** | GDS | n.v.t. | 10G | China Telecom, China Unicom, GDS |
| **Shanghai** | China Telecom | n.v.t. | 10G | China Telecom |
| **Shanghai2** | GDS | n.v.t. | 10G | China Telecom, China Unicom, GDS |

Zie [ExpressRoute in China](http://www.windowsazure.cn/home/features/expressroute/) voor meer informatie.

### <a name="germany"></a>Duitsland
| **Locatie** | **Dienstverleners** |
| --- | --- |
| **Berlijn** |e-shelter, Megaport+, T-Systems |
| **Frankfurt** |Colt, Equinix, Interxion |

## <a name="connectivity-through-exchange-providers"></a><a name="c1partners"></a>Connectiviteit via Exchange-providers
Als uw connectiviteitsprovider niet wordt vermeld in de vorige secties, kunt u alsnog verbinding maken.

* Neem contact op met uw connectiviteitsprovider om na te gaan of ze zijn verbonden met een van de exchange-punten in de bovenstaande tabel. Via de volgende koppelingen vindt u meer informatie over services die door de exchange-providers worden verstrekt. Verschillende connectiviteitsproviders zijn al verbonden met Ethernet-exchange-punten.
  * [Cologix](https://www.cologix.com/)
  * [CoreSite](https://www.coresite.com/)
  * [DE-CIX](https://www.de-cix.net/en/de-cix-service-world/cloud-exchange)
  * [Equinix Cloud Exchange](https://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
  * [InterXion](https://www.interxion.com/)
  * [NextDC](https://www.nextdc.com/)
  * [Megaport](https://www.megaport.com/services/microsoft-expressroute/)
  * [PacketFabric](https://www.packetfabric.com/cloud-connectivity/microsoft-azure)
  * [Teraco](https://www.teraco.co.za/platform-teraco/africa-cloud-exchange/)
  
* Vraag uw connectiviteitsprovider om uw netwerk uit te breiden tot de gewenste peeringlocatie.
  * Vergewis u ervan dat de connectiviteitsprovider uw connectiviteit uitbreidt op een maximaal beschikbare manier, zodat er geen storingspunten zijn.
* Vraag een ExpressRoute-circuit aan met het exchange-punt wanneer uw connectiviteitsprovider verbinding maakt met Microsoft.
  * Volg de stappen in [Create an ExpressRoute circuit](expressroute-howto-circuit-classic.md) (Een ExpressRoute-circuit maken) om connectiviteit in te stellen.

## <a name="connectivity-through-satellite-operators"></a>Connectiviteit via satellietoperators
Als u extern bent en geen glasvezelconnectiviteit hebt of als u andere connectiviteitsopties wilt verkennen, kunt u de volgende satellietoperators controleren. 

* Intelsat
* [Ses](https://www.ses.com/networks/signature-solutions/signature-cloud/ses-and-azure-expressroute)
* [Viasat](http://www.directcloud.viasatbusiness.com/)

## <a name="connectivity-through-additional-service-providers"></a><a name="c1partners"></a>Connectiviteit via aanvullende serviceproviders
| **Locatie** | **Exchange** | **Connectiviteitsproviders** |
| --- | --- | --- |
| **Amsterdam** | Equinix, Interxion, Level 3 Communications | BICS, CloudXpress, Eurofiber, Fastweb S.p.A, Bridge International, Moetam Telecom Telecom B.S.C, MainOne, Nianet, POST Telecom Telecom Telecom, Gmb, ReTN, TDC Erhverv, Telecom Telecom TelecomTelecommunicatie, Telekom Deutschland GmbH, Telia |
| **Atlanta** | Equinix| HadoenEn
| **Kaapstad** | Teraco | MTN |
| **Chennai** | Tata Communications | Tata Teleservices |
| **Chicago** | Equinix| Hadeens, Spectrum Enterprise, Windstream |
| **Dallas** | Equinix, Megaport | Axtel, C3ntro Telecom, Transcript Business, Welke Zij, Data Foundry, Spectrum Enterprise, Transtelco |
| **Frankfurt** | Interxion | BICS, Cinia, Equinix, Nianet, QSC AG, Telekom Deutschland GmbH |
| **Hamburg** | Equinix | Cinia |
| **Hongkong SAR** | Equinix | Chief, Macroview Telecom |
| **Johannesburg** | Teraco | MTN |
| **Londen** | BICS, Equinix, euNetworks| Bezeq International Ltd., CoreAzure, Epsilon Telecommunications Limited, Exponential E, HSO, NexGen Networks, Curve, Tamares Telecom, Zain |
| **Los Angeles** | Equinix |Hadoe, Spectrum Enterprise, Transtelco |
| **Madrid** | Level3 | Zertia |
| **Montreal** | Cologix| Airgate Technologies, Inc. Aptum Technologies, Rogers, Zirro |
| **Mumbai** | Tata Communications | Tata Teleservices |
| **New York** |Equinix, Megaport | Altice Business, Crown Castle, Spectrum Enterprise, Webair |
| **Parijs** | Equinix | Proximus |
| **Quebec** | Megaport | Fibrenoire |
| **Sao Paulo** | Equinix | Venha Pra Nuvem |
| **Seattle** |Equinix | Alaska Communications |
| **Silicon Valley** |Coresite, Equinix | Cox Business, Spectrum Enterprise, Windstream, X2nsat Inc. |
| **Singapore** |Equinix |1CLOUDSTAR, BICS, CMC Telecom, Epsilon Telecommunications Limited, LGA Telecom, United Information Highway (UIH) |
| **Slough** | Equinix | HSO|
| **Sydney** | Megaport | Macquarie Telecom Group|
| **Tokio** | Equinix | ARTERIA Networks Corporation, BroadBand Tower, Inc. |
| **Toronto** | Equinix, Megaport | Airgate Technologies Inc., Beanfield Metroconnect, Aptum Technologies, IVedha Inc, Oncore Cloud Services Inc., Rogers, Thinktel, Zirro|
| **Washington DC** |Equinix | Altice Business, BICS, Bics, Cox Business, Hadoop, Gtt Communications Inc, Epsilon Telecommunications Limited, Masergy, Windstream |

## <a name="expressroute-system-integrators"></a>ExpressRoute-SI's
Het inschakelen van particuliere connectiviteit conform uw specifieke behoeften kan lastig zijn, al naargelang de schaal van uw netwerk. U kunt alle SI's uit de volgende tabel gebruiken om u te helpen met de voorbereidingen voor ExpressRoute.

| **Continent** | **Systeemintegratie** |
| --- | --- |
| **Azië** |Avanade Inc., OneAs1a |
| **Australië** | Ensyst, IT Consultancy, MOQdigital, Vigilant.IT |
| **Europa** |Avanade Inc., Altogee, Bright Skies GmbH, Inframon, MSG Services, New Signature, Nelite, Orange Networks, sol-tec |
| **Noord-Amerika** |Avanade Inc., Equinix Professional Services, FlexManage, Lightstream, Perficient, Presidio |
| **Zuid-Amerika** |Avanade Inc., Venha Pra Nuvem |
## <a name="next-steps"></a>Volgende stappen
* Zie veelgestelde vragen over ExpressRoute voor [meer informatie over ExpressRoute.](expressroute-faqs.md)
* Controleer of aan alle vereisten is voldaan. Zie [ExpressRoute prerequisites](expressroute-prerequisites.md) (Vereisten voor ExpressRoute).

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "Locatiekaart"
