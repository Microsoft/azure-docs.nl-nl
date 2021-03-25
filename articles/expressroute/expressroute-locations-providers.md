---
title: 'Locaties en connectiviteitsproviders: Azure ExpressRoute | Microsoft Docs'
description: Dit artikel bevat een gedetailleerd overzicht van de locaties waar services worden aangeboden en hoe u verbinding maakt met Azure-regio's. Gesorteerd op locatie.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/10/2021
ms.author: duau
ms.openlocfilehash: a43f95ad65e95db2b69b32c3fe8d62db71a98a17
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2021
ms.locfileid: "105025200"
---
# <a name="expressroute-partners-and-peering-locations"></a>Partners en peeringlocaties voor ExpressRoute

> [!div class="op_single_selector"]
> * [Locaties per provider](expressroute-locations.md)
> * [Providers per locatie](expressroute-locations-providers.md)


De tabellen in dit artikel bevatten informatie over ExpressRoute geografische dekking en locaties, ExpressRoute-connectiviteits providers en ExpressRoute-systeem integrators (SIs).

> [!Note]
> Azure-regio's en ExpressRoute-locaties zijn twee verschillende concepten, en het verschil tussen de twee is van cruciaal belang voor het verkennen van een Azure Hybrid Networking-verbinding. 
>
>

## <a name="azure-regions"></a>Azure-regio's
Azure-regio's zijn wereld wijde data centers waar Azure compute-, netwerk-en opslag bronnen zich bevinden. Bij het maken van een Azure-resource moet een klant een resource locatie selecteren. De resource locatie bepaalt in welk Azure-Data Center (of beschikbaarheids zone) de resource wordt gemaakt.

## <a name="expressroute-locations"></a>ExpressRoute-locaties
ExpressRoute-locaties (soms ook wel peering-locaties of Vergader locaties genoemd) zijn co-locatie faciliteiten waar micro soft Enter prise Edge (MSEE)-apparaten zich bevinden. ExpressRoute locaties zijn het ingangs punt naar het netwerk van micro soft, en zijn wereld wijd gedistribueerd, waardoor klanten de mogelijkheid hebben om verbinding te maken met het netwerk van micro soft over de hele wereld. Deze locaties zijn waar ExpressRoute partners en ExpressRoute direct-klanten cross-connections naar het netwerk van micro soft verlenen. Over het algemeen hoeft de ExpressRoute-locatie niet overeen te komen met de Azure-regio. Een klant kan bijvoorbeeld een ExpressRoute-circuit maken met de resource locatie *VS-Oost*, op de vestiging *Seattle* peering.

U hebt toegang tot Azure-services in alle regio's binnen een geopolitieke regio als u bent verbonden met ten minste één ExpressRoute-locatie in die geopolitieke regio. 

## <a name="azure-regions-to-expressroute-locations-within-a-geopolitical-region"></a><a name="locations"></a>Azure-regio's naar ExpressRoute-locaties binnen een geopolitieke regio
In de volgende tabel vindt u een toewijzing van Azure-regio's aan ExpressRoute-locaties binnen een geopolitieke regio.

| **Geopolitieke regio** | **Azure-regio's** | **ExpressRoute-locaties** |
| --- | --- | --- |
| **Australië - overheid** | Australië Centraal, Australië Centraal 2 |Canberra, Canberra2 |
| **Europa** | Frankrijk-centraal, Frankrijk-zuid, Duitsland-noord, Duitsland-west-centraal, Europa-noord, Noor wegen Oost, Noor wegen West, Zwitserland-noord, Zwitserland-west, UK-west, UK-zuid, Europa-west |Amsterdam, Amsterdam2, Berlijn, kopen Hagen, Dublin, Frankfurt, Frankfurt2, Genève, Londen, London2, Madrid, Marseille, Milaan, München, Newport (Wales), Utrecht, Parijs, Stavanger, Stockholm, Zürich |
| **Noord-Amerika** | VS - oost, VS - west, VS - oost 2, VS - west 2, VS - centraal, VS - zuid-centraal, VS - noord-centraal, VS - west-centraal, Canada - centraal, Canada - oost |Atlanta, Chicago, Rotterdam, Denver, Las, Groningen, los Angeles2, Miami, Minneapolis, Montreal, New York, Groningen, Quebec City, Queretaro (Mexico), Quincy, San Antonio, Seattle, Silicon dal, Silicon Valley2, Toronto, Toronto2, Vancouver, Washington DC, Washington DC2 |
| **Azië** | Azië - oost, Azië - zuidoost | Bangkok, Hongkong, Hongkong Kong2, Jakarta, Kuala Lumpur, Singapore, Singapore2, Taiwan |
| **India** | India - west, India - centraal, India - zuid |Chennai, Chennai2, Mumbai, Mumbai2 |
| **Japan** | Japan - west, Japan - oost |Osaka, Tokio, Tokyo2 |
| **Oceanië** | Australië - zuidoost, Australië - oost |Auckland, Melbourne, Perth, Sydney, Sydney2 | 
| **Zuid-Korea** | Korea - centraal, Korea - zuid |Busan, Seoul|
| **VAE** | UAE-centraal, UAE-noord | Dubai, Dubai2 |
| **Zuid-Afrika** | Zuid-Afrika-west, Zuid-Afrika-noord |Kaapstad, Johannesburg |
| **Zuid-Amerika** | Brazilië - zuid |Bogotá, Rio de Janeiro, Sao Paulo |

## <a name="azure-regions-and-geopolitical-boundaries-for-national-clouds"></a>Azure-regio's en geopolitieke grenzen voor nationale Clouds
De volgende tabel bevat informatie over regio's en geopolitieke grenzen voor nationale clouds.

| **Geopolitieke regio** | **Azure-regio's** | **ExpressRoute-locaties** |
| --- | --- | --- |
| **Cloud van de Amerikaanse overheid** |US Gov - AZ, US Gov - Iowa, US Gov - TX, US Gov - Virginia, US DoD Central, US DoD East  |Atlanta, Chicago, Rotterdam, New York, Phoenix, San Antonio, Seattle, silicone dal, Washington DC |
| **China East** |China - oost, China - oost2 |Shanghai, Shanghai2 |
| **China - noord** |China - noord, China - noord2 |Beijing, Beijing2 |
| **Duitsland** |Duitsland Centraal, Duitsland Oost |Berlijn, Frankfurt |

Connectiviteit tussen de geopolitieke regio's wordt niet ondersteund op de standaard ExpressRoute-SKU. U moet de invoegtoepassing ExpressRoute Premium inschakelen voor ondersteuning van globale connectiviteit. Connectiviteit met nationale cloudomgevingen wordt niet ondersteund. U kunt met uw connectiviteitsprovider samenwerken als de noodzaak daartoe zich voordoet.

## <a name="expressroute-connectivity-providers"></a><a name="partners"></a>ExpressRoute-connectiviteitsproviders

De volgende tabel geeft de connectiviteitslocaties en de serviceproviders voor elke locatie weer. Als u serviceproviders en de locaties waarvoor zij deze service kunnen bieden, wilt bekijken, raadpleegt u [Locaties per serviceprovider](expressroute-locations.md).

* **Lokale Azure-regio's** zijn degene die [ExpressRoute lokaal](expressroute-faqs.md) op elke peering-locatie hebben. **n.v.t.** geeft aan dat ExpressRoute lokaal niet beschikbaar is op die peering-locatie.

* **Zone** verwijst naar [prijzen](https://azure.microsoft.com/pricing/details/expressroute/).


### <a name="global-commercial-azure"></a>Wereld wijde commerciële Azure
| **Locatie** | **Adres** | **Zone** | **Lokale Azure-regio's** | **Er direct** | **Service providers** |
| --- | --- | --- | --- | --- | --- |
| **Amsterdam** | [Equinix AM5](https://www.equinix.com/locations/europe-colocation/netherlands-colocation/amsterdam-data-centers/am5/) | 1 | Europa -west | 10G, 100G | Aryaka Networks, AT&T netbond, British Telecom, Colt, Equinix, euNetworks, GÉANT, intercloud, Interxion, KPN, IX REACH, Level 3 Communications, Mega Port, NTT Communications, oranje, Tata Communications, Telefonica, Telenor, Telia Carrier, Verizon, Zayo |
| **Amsterdam2** | [Interxion AMS8](https://www.interxion.com/Locations/amsterdam/schiphol/) | 1 | Europa -west | 10G, 100G | British Telecom, CenturyLink Cloud Connect, Colt, DE CIX, Equinix, euNetworks, GÉANT, Interxion, NOS, NTT Global Data Centers EMEA, oranje, Vodafone |
| **Atlanta** | [Equinix AT2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/atlanta-data-centers/at2/) | 1 | n.v.t. | 10G, 100G | Equinix, Megaport |
| **Auckland** | [Vocus groep NZ Albany](https://www.vocus.co.nz/business/cloud-data-centres) | 2 | n.v.t. | 10G | Devoli, Kordia, Mega Port, REANNZ, Spark NZ, Vocus groep NZ |
| **Bangkok** | [AIS](https://business.ais.co.th/solution/en/azure-expressroute.html) | 2 | n.v.t. | 10G | AIS, UIH |
| **Berlijn** | [NTT GDC](https://www.e-shelter.de/en/location/berlin-1-data-center) | 1 | Duitsland - noord | 10G | Equinix, NTT Global Data Centers EMEA|
| **Bogotá** | [Equinix BG1](https://www.equinix.com/locations/americas-colocation/colombia-colocation/bogota-data-centers/bg1/) | 4 | n.v.t. | 10G | Equinix |
| **Busan** | [LG CNS](https://www.lgcns.com/En/Service/DataCenter) | 2 | Korea - zuid | n.v.t. | LG CNS |
| **Canberra** | [CDC](https://cdcdatacentres.com.au/content/about-cdc) | 1 | Australië - centraal | 10G, 100G | CDC |
| **Canberra2** | [CDC](https://cdcdatacentres.com.au/content/about-cdc) | 1 | Australië - centraal 2| 10G, 100G | CDC, Equinix |
| **Kaapstad** | [Teraco CT1](https://www.teraco.co.za/data-centre-locations/cape-town/) | 3 | Zuid-Afrika - west | 10G | BCX, Internet Solutions-Cloud Connect, Liquid Telecom, Teraco |
| **Chennai** | Tata Communications | 2 | India - zuid | 10G | BSNL, Global CloudXchange (GCX), SIFY, Tata Communications, VodafoneIdea |
| **Chennai2** | Airtel | 2 | India - zuid | 10G | Airtel |
| **Chicago** | [Equinix CH1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/chicago-data-centers/ch1/) | 1 | VS - noord-centraal | 10G, 100G | Aryaka Networks, AT&T netbond, British Telecom, CenturyLink Cloud Connect, Cologix, Colt, Comcast, CoreSite, Equinix, intercloud, Internet2, Level 3 Communications, Mega Port, PacketFabric, PCCW Global Limited, Sprint, Telia Carrier, Verizon, Zayo |
| **Kopen** | [Interxion CPH1](https://www.interxion.com/Locations/copenhagen/) | 1 | n.v.t. | 10G | Interxion |
| **Dallas** | [Equinix DA3](https://www.equinix.com/locations/americas-colocation/united-states-colocation/dallas-data-centers/da3/) | 1 | n.v.t. | 10G, 100G | Aryaka Networks, AT&T netbond, Cologix, Equinix, Internet2, Level 3 Communications, Mega Port, Neutrona Networks, PacketFabric, Telmex uninet, Telia Carrier, Transtelco, Verizon, Zayo|
| **Denver** | [CoreSite DE1](https://www.coresite.com/data-centers/locations/denver/de1) | 1 | VS - west-centraal | n.v.t. | CoreSite, Mega Port, Zayo |
| **Dubai** | [PCCS](https://www.pacificcontrols.net/cloudservices/index.html) | 3 | VAE - noord | n.v.t. | Etisalat VAE |
| **Dubai2** | [du datamena](http://datamena.com/solutions/data-centre) | 3 | VAE - noord | n.v.t. | DE CIX, du datamena, Equinix, Mega Port, oranje, Orixcom |
| **Dublin** | [Equinix DB3](https://www.equinix.com/locations/europe-colocation/ireland-colocation/dublin-data-centers/db3/) | 1 | Europa - noord | 10G, 100G | CenturyLink Cloud Connect, Colt, EIR, Equinix, GEANT, euNetworks, Interxion, Mega Port |
| **Frankfurt** | [Interxion FRA11](https://www.interxion.com/Locations/frankfurt/) | 1 | Duitsland - west-centraal | 10G, 100G | BIJ&T netobligatie, CenturyLink Cloud Connect, Colt, DE CIX, Equinix, euNetworks, GEANT, intercloud, Interxion, Mega Port, oranje, Telia-Carrier, T-Systems |
| **Frankfurt2** | [Equinix FR7](https://www.equinix.com/locations/europe-colocation/germany-colocation/frankfurt-data-centers/fr7/) | 1 | Duitsland - west-centraal | 10G, 100G | Equinix |
| **Voortvloei** | [Equinix GV2](https://www.equinix.com/locations/europe-colocation/switzerland-colocation/geneva-data-centers/gv2/) | 1 | Zwitserland - west | 10G, 100G | Equinix, Mega Port, Swisscom |
| **Hongkong** | [Equinix HK1](https://www.equinix.com/locations/asia-colocation/hong-kong-colocation/hong-kong-data-center/hk1/) | 2 | Azië - oost | 10G | Aryaka Networks, British Telecom, CenturyLink Cloud Connect, Chief Telecom, China Telecom Global, China Unicom, Equinix, intercloud, Mega Port, NTT Communications, oranje, PCCW Global Limited, Tata Communications, Telia Carrier, Verizon |
| **Hongkong Kong2** | [MEGA-i](https://www.iadvantage.net/index.php/locations/mega-i) | 2 | Azië - oost | 10G | China Mobile International, China Telecom Global, iAdvantage, Mega Port, PCCW Global Limited, SingTel |
| **Jakarta** | Telin, Telkom Indonesië | 4 | n.v.t. | 10G | Telin |
| **Johannesburg** | [Teraco JB1](https://www.teraco.co.za/data-centre-locations/johannesburg/#jb1) | 3 | Zuid-Afrika - noord | 10G | BCX, British Telecom, Internet Solutions-Cloud Connect, Liquid Telecom, oranje, Teraco |
| **Kuala Lumpur** | [TIJD dotCom Menara doel stellingen](https://www.time.com.my/enterprise/connectivity/direct-cloud) | 2 | n.v.t. | n.v.t. | TIME dotCom |
| **Las Vegas** | [LV wijzigen](https://www.switch.com/las-vegas) | 1 | n.v.t. | 10G, 100G | CenturyLink Cloud Connect, Mega Port, PacketFabric |
| **Londen** | [Equinix LD5](https://www.equinix.com/locations/europe-colocation/united-kingdom-colocation/london-data-centers/ld5/) | 1 | Verenigd Koninkrijk Zuid | 10G, 100G | BIJ&T netbond, British Telecom, Colt, Equinix, euNetworks, intercloud, Internet Solutions-Cloud Connect, Interxion, jisc, Level 3 Communications, Mega Port, MTN, NTT Communications, oranje, PCCW Global Limited, Tata Communications, telehouse-KDDI, Telenor, Telia Carrier, Verizon, Vodafone, Zayo |
| **London2** | [Twee-House-Noord](https://www.telehouse.net/data-centres/emea/uk-data-centres/london-data-centres/north-two) | 1 | Verenigd Koninkrijk Zuid | 10G, 100G | British Telecom, CenturyLink Cloud Connect, Colt, GTT, IX REACH, Equinix, Mega Port, SES, Sohonet, telehouse-KDDI |
| **Los Angeles** | [CoreSite LA1](https://www.coresite.com/data-centers/locations/los-angeles/one-wilshire) | 1 | n.v.t. | 10G, 100G | CoreSite, Equinix, Megaport, Neutrona Networks, NTT, Zayo |
| **Los-Angeles2** | [Equinix LA1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/los-angeles-data-centers/la1/) | 1 | n.v.t. | 10G, 100G | Equinix |
| **Madrid** | [Interxion MAD1](https://www.interxion.com/es/donde-estamos/europa/madrid) | 1 | Europa -west | 10G, 100G | |
| **Marseille** |[Interxion MRS1](https://www.interxion.com/Locations/marseille/) | 1 | Frankrijk - zuid | n.v.t. | DE CIX, GEANT, Interxion, Jaguar Network, Ooredoo Cloud Connect |
| **Melbourne** | [NextDC M1](https://www.nextdc.com/data-centres/m1-melbourne-data-centre) | 2 | Australië - zuidoost | 10G, 100G | AARNet, Devoli, Equinix, Mega Port, NEXTDC, Optus, Telstra Corporation, TPG Telecom |
| **Miami** | [Equinix MI1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/miami-data-centers/mi1/) | 1 | n.v.t. | 10G, 100G | Claro, C3ntro, Equinix, Mega Port, Neutrona netwerken |
| **Milaan** | [IRIDEOS](https://irideos.it/en/data-centers/) | 1 | n.v.t. | 10G | Colt, Equinix, Fastweb, IRIDEOS, Retelit |
| **Minneapolis** | [Cologix MIN1](https://www.cologix.com/data-centers/minneapolis/min1/) | 1 | n.v.t. | 10G, 100G | Cologix, Mega Port |
| **Montreal** | [Cologix MTL3](https://www.cologix.com/data-centers/montreal/mtl3/) | 1 | n.v.t. | 10G, 100G | Bell Canada, Cologix, Fibrenoire, Mega Port, Telus, Zayo |
| **Mumbai** | Tata Communications | 2 | India - west | 10G | BSNL, DE-CIX, Global CloudXchange (GCX), revertrouwende Jio, Sify, Tata Communications, Verizon |
| **Mumbai2** | Airtel | 2 | India - west | 10G | Airtel, Sify, Vodafone Idea |
| **Rotterdam** | [EdgeConneX](https://www.edgeconnex.com/locations/europe/munich/) | 1 | n.v.t. | 10G | DE CIX |
| **New York** | [Equinix NY9](https://www.equinix.com/locations/americas-colocation/united-states-colocation/new-york-data-centers/ny9/) | 1 | n.v.t. | 10G, 100G | CenturyLink Cloud Connect, Colt, CoreSite, DE-CIX, Equinix, intercloud, Mega Port, Packet, Zayo |
| **Newport (Wales)** | [Next Generation Data](https://www.nextgenerationdata.co.uk) | 1 | Verenigd Koninkrijk West | n.v.t. | British Telecom, Colt, jisc, Level 3 Communications, gegevens van de volgende generatie |
| **Osaka** | [Equinix OS1](https://www.equinix.com/locations/asia-colocation/japan-colocation/osaka-data-centers/os1/) | 2 | Japan - west | 10G, 100G | OP TOKYO, Colt, Equinix, Internet Initiative Japan Inc.-IIJ, Mega Port, NTT Communications, NTT SmartConnect, Softbank, Tokai Communications |
| **Oslo** | [DigiPlex Ulven](https://www.digiplex.com/locations/oslo-datacentre) | 1 | Noorwegen - oost | 10G, 100G | GlobalConnect, Mega Port, Telenor, Telia-Carrier |
| **Parijs** | [Interxion PAR5](https://www.interxion.com/Locations/paris/) | 1 | Frankrijk - centraal | 10G, 100G | British Telecom, CenturyLink Cloud Connect, Colt, Equinix, intercloud, Interxion, Jaguar Network, Mega Port, oranje, Telia Carrier, Zayo |
| **Perth** | [NextDC P1](https://www.nextdc.com/data-centres/p1-perth-data-centre) | 2 | n.v.t. | 10G | Mega Port, NextDC |
| **Phoenix** | [EdgeConneX PHX01](https://www.edgeconnex.com/locations/north-america/phoenix-az/) | 1 | n.v.t. | 10G, 100G | |
| **Quebec** | [Vantage](https://vantage-dc.com/data_centers/quebec-city-data-center-campus/) | 1 | Canada - oost | 10G, 100G | Bell Canada, Mega Port, Telus |
| **Queretaro (Mexico)** | [KIO-netwerken QR01](https://www.kionetworks.com/es-mx/) | 4 | n.v.t. | 10G | Transtelco|
| **Quincy** | [Sabey Data Center-een maken](https://sabeydatacenters.com/data-center-locations/central-washington-data-centers/quincy-data-center) | 1 | VS - west 2 | 10G, 100G | |
| **Rio de Janeiro, Brazilië** | [Equinix-RJ2](https://www.equinix.com/locations/americas-colocation/brazil-colocation/rio-de-janeiro-data-centers/rj2/) | 3 | Brazilië-Zuidoost | 10G | Equinix |
| **San Antonio** | [CyrusOne SA1](https://cyrusone.com/locations/texas/san-antonio-texas/) | 1 | VS - zuid-centraal | 10G, 100G | CenturyLink Cloud Connect, Megaport |
| **Sao Paulo** | [Equinix SP2](https://www.equinix.com/locations/americas-colocation/brazil-colocation/sao-paulo-data-centers/sp2/) | 3 | Brazilië - zuid | 10G, 100G | Aryaka Networks, Ascenty Data Centers, British Telecom, Equinix, Level 3 Communications, Neutrona Networks, Orange, Tata Communications, Telefonica, UOLDIVEO |
| **Seattle** | [Equinix SE2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/seattle-data-centers/se2/) | 1 | VS - west 2 | 10G, 100G | Aryaka Networks, Equinix, Level 3 Communications, Mega Port, Telus, Zayo |
| **Seoul** | [KINX Gasan IDC](https://www.kinx.net/?lang=en) | 2 | Korea - centraal | 10G, 100G | KINX, KT, LG CNS, Equinix, Sejong Telecom |
| **Silicon Valley** | [Equinix SV1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/silicon-valley-data-centers/sv1/) | 1 | VS - west | 10G, 100G | Aryaka Networks, bij&T netbond, British Telecom, CenturyLink Cloud Connect, Colt, Comcast, CoreSite, Equinix, intercloud, Internet2, IX REACH, pakket, PacketFabric, Level 3 Communications, Mega Port, oranje, Sprint, Tata Communications, Telia Carrier, Verizon, Zayo |
| **Silicium Valley2** | [CoreSite SV7](https://www.coresite.com/data-centers/locations/silicon-valley/sv7) | 1 | VS - west | 10G, 100G | Colt, CoreSite | 
| **Singapore** | [Equinix SG1](https://www.equinix.com/locations/asia-colocation/singapore-colocation/singapore-data-center/sg1/) | 2 | Azië - zuidoost | 10G, 100G | Aryaka Networks, AT&T netbond, British Telecom, China Mobile International, Epsilon wereld wijde communicatie, Equinix, intercloud, Level 3 Communications, Mega Port, NTT Communications, oranje, SingTel, Tata Communications, Telstra Corporation, Verizon, Vodafone |
| **Singapore2** | [Algemene switch Tai Seng](https://www.globalswitch.com/locations/singapore-data-centres/) | 2 | Azië - zuidoost | 10G, 100G | China Unicom Global, Colt, Epsilon Global Communications, Mega Port, PCCW Global Limited, SingTel, telehouse-KDDI |
| **Stavanger** | [Groene Mountain DC1](https://greenmountain.no/dc1-stavanger/) | 1 | Noorwegen - west | 10G, 100G |GlobalConnect, Mega Port |
| **Akte** | [Equinix SK1](https://www.equinix.com/locations/europe-colocation/sweden-colocation/stockholm-data-centers/sk1/) | 1 | n.v.t. | 10G | Equinix, Telia-Carrier |
| **Sydney** | [Equinix SY2](https://www.equinix.com/locations/asia-colocation/australia-colocation/sydney-data-centers/sy2/) | 2 | Australië - oost | 10G, 100G | AARNet, op&T netbond, British Telecom, Devoli, Equinix, Kordia, Mega Port, NEXTDC, NTT Communications, Optus, oranje, Spark NZ, Telstra Corporation, TPG Telecom, Verizon, Vocus groep NZ |
| **Sydney2** | [NextDC S1](https://www.nextdc.com/data-centres/s1-sydney-data-centre) | 2 | Australië - oost | 10G, 100G | Mega Port, NextDC |
| **Taipei** | Chief Telecom | 2 | n.v.t. | 10G | Chief telecom municatie, Chunghwa Telecom, FarEasTone |
| **Tokio** | [Equinix TY4](https://www.equinix.com/locations/asia-colocation/japan-colocation/tokyo-data-centers/ty4/) | 2 | Japan - oost | 10G, 100G | Aryaka Networks, AT&T netbond, BBIX, British Telecom, CenturyLink Cloud Connect, Colt, Equinix, Internet Initiative Japan Inc.-IIJ, Mega Port, NTT Communications, NTT Oost, oranje, Softbank, Verizon |
| **Tokyo2** | [OP TOKYO](https://www.attokyo.com/) | 2 | Japan - oost | 10G, 100G | OP TOKYO, Mega Port, Tokai Communications |
| **Toronto** | [Cologix TOR1](https://www.cologix.com/data-centers/toronto/tor1/) | 1 | Canada - midden | 10G, 100G | BIJ&T netbond, Bell Canada, CenturyLink Cloud Connect, Cologix, Equinix, IX Mega Port, Telus, Verizon, Zayo |
| **Toronto2** | [Allied REIT](https://www.alliedreit.com/property/905-king-st-w/) | 1 | Canada - midden | 10G, 100G | |
| **Vancouver** | [Cologix VAN1](https://www.cologix.com/data-centers/vancouver/van1/) | 1 | n.v.t. | 10G | Cologix, Mega Port, Telus |
| **Washington DC** | [Equinix DC2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/washington-dc-data-centers/dc2/) | 1 | VS-Oost, VS-Oost 2 | 10G, 100G | Aryaka Networks, AT&T netbond, British Telecom, CenturyLink Cloud Connect, Cologix, Colt, Comcast, CoreSite, Equinix, Internet2, intercloud, ijzer Mountain, IX REACH, Level 3 Communications, Mega Port, Neutrona Networks, NTT Communications, oranje, PacketFabric, SES, Sprint, Tata Communications, Telia-Carrier, Verizon, Zayo |
| **Washington DC2** | [CoreSite Reston](https://www.coresite.com/data-center-locations/northern-virginia-washington-dc) | 1 | VS-Oost, VS-Oost 2 | 10G, 100G | CenturyLink Cloud Connect, CoreSite, INTELSAT, Mega Port, Viasat, Zayo | 
| **Zurich** | [Interxion ZUR2](https://www.interxion.com/Locations/zurich/) | 1 | Zwitserland - noord | 10G, 100G | Equinix, intercloud, Interxion, Mega Port, Swisscom |

 **+** geeft binnenkort een volgende melding

### <a name="national-cloud-environments"></a>Nationale cloudomgevingen

Azure National Clouds zijn geïsoleerd van elkaar en van wereld wijde commerciële Azure. ExpressRoute voor een Azure-Cloud kan geen verbinding maken met de Azure-regio's in de andere.

### <a name="us-government-cloud"></a>Cloud van de Amerikaanse overheid
| **Locatie** | **Adres** | **Lokale Azure-regio's**| **Er direct** | **Service providers** |
| --- | --- | --- | --- | --- |
| **Atlanta** | [Equinix AT1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/atlanta-data-centers/at1/) | n.v.t. | 10G, 100G | Equinix |
| **Chicago** | [Equinix CH1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/chicago-data-centers/ch1/) | n.v.t. | 10G, 100G | BIJ&T netbond, British Telecom, Equinix, Level 3 Communications, Verizon |
| **Dallas** | [Equinix DA3](https://www.equinix.com/locations/americas-colocation/united-states-colocation/dallas-data-centers/da3/) | n.v.t. | 10G, 100G | Equinix, Megaport, Verizon |
| **New York** | [Equinix NY5](https://www.equinix.com/locations/americas-colocation/united-states-colocation/new-york-data-centers/ny5/) | n.v.t. | 10G, 100G | Equinix, CenturyLink Cloud Connect, Verizon |
| **Phoenix** | [CyrusOne Chandler](https://cyrusone.com/locations/arizona/phoenix-arizona-chandler/) | VS (overheid) - Arizona | 10G, 100G | BIJ&T netbond, CenturyLink Cloud Connect, Mega Port |
| **San Antonio** | [CyrusOne SA2](https://cyrusone.com/locations/texas/san-antonio-texas-ii/) | VS (overheid) - Texas | 10G, 100G | CenturyLink Cloud Connect, Megaport |
| **Silicon Valley** | [Equinix SV4](https://www.equinix.com/locations/americas-colocation/united-states-colocation/silicon-valley-data-centers/sv4/) | n.v.t. | 10G, 100G | OP&T, Equinix, Level 3 Communications, Verizon |
| **Seattle** | [Equinix SE2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/seattle-data-centers/se2/) | n.v.t. | 10G, 100G | Equinix, Megaport |
| **Washington DC** | [Equinix DC2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/washington-dc-data-centers/dc2/) | US DoD-oost, US Gov-Virginia | 10G, 100G | BIJ&T netobligatie, CenturyLink Cloud Connect, Equinix, Level 3 Communications, Mega Port, Verizon |

### <a name="china"></a>China
| **Locatie** | **Service providers** |
| --- | --- |
| **Beijing** |China Telecom |
| **Peking2** | China Telecom, China Unicom, GDS |
| **Shanghai** |China Telecom |
| **Shanghai2** | China Telecom, China Unicom, GDS |

Zie [ExpressRoute in China](http://www.windowsazure.cn/home/features/expressroute/) voor meer informatie.

### <a name="germany"></a>Duitsland
| **Locatie** | **Service providers** |
| --- | --- |
| **Berlijn** |e-shelter, Megaport+, T-Systems |
| **Frankfurt** |Colt, Equinix, Interxion |

## <a name="connectivity-through-exchange-providers"></a><a name="c1partners"></a>Connectiviteit via Exchange-providers
Als uw connectiviteitsprovider niet wordt vermeld in de vorige secties, kunt u alsnog verbinding maken.

* Neem contact op met uw connectiviteitsprovider om na te gaan of ze zijn verbonden met een van de exchange-punten in de bovenstaande tabel. Via de volgende koppelingen vindt u meer informatie over services die door de exchange-providers worden verstrekt. Verschillende connectiviteitsproviders zijn al verbonden met Ethernet-exchange-punten.
  * [Cologix](https://www.cologix.com/)
  * [CoreSite](https://www.coresite.com/)
  * [DE CIX](https://www.de-cix.net/en/de-cix-service-world/cloud-exchange)
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

## <a name="connectivity-through-satellite-operators"></a>Connectiviteit via satelliet operators
Als u extern bent en geen glasvezel connectiviteit hebt of als u andere connectiviteits opties wilt verkennen, kunt u de volgende satelliet-Opera tors controleren. 

* Intelsat
* [SES](https://www.ses.com/networks/signature-solutions/signature-cloud/ses-and-azure-expressroute)
* [Viasat](http://www.directcloud.viasatbusiness.com/)

## <a name="connectivity-through-additional-service-providers"></a><a name="c1partners"></a>Connectiviteit via aanvullende service providers
| **Locatie** | **Exchange** | **Connectiviteitsproviders** |
| --- | --- | --- |
| **Amsterdam** | Equinix, Interxion, Level 3 Communications | BICS, CloudXpress, euro Fiber, Fastweb S. p. A, Golf Bridge International, Kalaam Telecom Bahrein B. S. C, MainOne, Nianet, POST Telecom Luxembourg, Proximus, RETN, TDC erhverv, Telecom Italië vonk, Telekom Deutschland GmbH, Telia |
| **Atlanta** | Equinix| Kroon Castle
| **Kaapstad** | Teraco | MTN |
| **Chicago** | Equinix| Kroon Castle, spectrum Enter prise, windstream |
| **Dallas** | Equinix, Megaport | Axtel, C3ntro Telecom, Cox Business, kroon Castle, data found, spectrum onderneming, Transtelco |
| **Frankfurt** | Interxion | BICS, Cinia, Equinix, Nianet, QSC AG, Telekom Deutschland GmbH |
| **Hamburg** | Equinix | Cinia |
| **Hongkong SAR** | Equinix | Chief, Macroview Telecom |
| **Johannesburg** | Teraco | MTN |
| **Londen** | BICS, Equinix, euNetworks| Bezeq International Ltd., CoreAzure, Epsilon Telecom Limited, exponentieel E, HSO, NexGen netwerken, Proximus, Tamares Telecom, Zain |
| **Los Angeles** | Equinix |Kroon Castle, spectrum Enter prise, Transtelco |
| **Madrid** | Level3 | Zertia |
| **Montreal** | Cologix| Poort technologieën, Inc. Aptum Technologies, Rogers, Zirro |
| **New York** |Equinix, Megaport | Altice Business, kroon Castle, spectrum Enter prise, Webair |
| **Parijs** | Equinix | Proximus |
| **Quebec** | Megaport | Fibrenoire |
| **Sao Paulo** | Equinix | Venha Pra Nuvem |
| **Seattle** |Equinix | Alaska Communications |
| **Silicon Valley** |CoreSite, Equinix | Cox Business, spectrum Enter prise, windstream, X2nsat Inc. |
| **Singapore** |Equinix |1CLOUDSTAR, BICS, CMC Telecom, Epsilon Telecom Limited, LGA Telecom, Verenigde informatie-weg (UIH) |
| **Slough** | Equinix | HSO|
| **Sydney** | Megaport | Macquarie Telecom Group|
| **Tokio** | Equinix | ARTERIA Networks Corporation, BroadBand Tower, Inc. |
| **Toronto** | Equinix, Megaport | Beanfield Metroconnect, Aptum Technologies, IVedha Inc, Oncore Cloud Services Inc., Rogers, Thinktel, Zirro|
| **Washington DC** |Equinix | Altice Business, BICS, Cox Business, kroon Castle, GTt Communications Inc, Epsilon Telecom Limited, Masergy, windstream |

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
* Zie de [Veelgestelde vragen over ExpressRoute](expressroute-faqs.md)voor meer informatie over ExpressRoute.
* Controleer of aan alle vereisten is voldaan. Zie [ExpressRoute prerequisites](expressroute-prerequisites.md) (Vereisten voor ExpressRoute).

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "Locatie kaart"
