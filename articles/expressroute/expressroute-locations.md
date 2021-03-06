---
title: 'Connectiviteitsproviders en -locaties: Azure ExpressRoute | Microsoft Docs'
description: Dit artikel bevat een gedetailleerd overzicht van de locaties waar services worden aangeboden en hoe u verbinding maakt met Azure-regio's. Gesorteerd op connectiviteitsprovider.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/10/2021
ms.author: duau
ms.openlocfilehash: 741fd7e4554ffe07de08e96233c440210eefa2fa
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107498404"
---
# <a name="expressroute-connectivity-partners-and-peering-locations"></a>Partners en peeringlocaties voor ExpressRoute-connectiviteit

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
ExpressRoute-locaties (soms peeringlocaties of meet-me-locaties genoemd) zijn co-locatielocaties waar MSEE-apparaten (Microsoft Enterprise Edge) zich bevinden. ExpressRoute-locaties zijn het toegangspunt voor het netwerk van Microsoft en zijn wereldwijd gedistribueerd, zodat klanten overal ter wereld verbinding kunnen maken met het netwerk van Microsoft. Deze locaties zijn waar ExpressRoute-partners en ExpressRoute Direct verbinding maken met het netwerk van Microsoft. Over het algemeen hoeft de ExpressRoute-locatie niet overeen te komen met de Azure-regio. Een klant kan bijvoorbeeld een ExpressRoute-circuit maken met de resourcelocatie VS - *oost,* op de peeringlocatie *Seattle.*

U hebt toegang tot Azure-services in alle regio's binnen een geopolitieke regio als u bent verbonden met ten minste één ExpressRoute-locatie in die geopolitieke regio.

## <a name="azure-regions-to-expressroute-locations-within-a-geopolitical-region"></a><a name="locations"></a>Azure-regio's naar ExpressRoute-locaties binnen een geopolitieke regio.
In de volgende tabel vindt u een toewijzing van Azure-regio's aan ExpressRoute-locaties binnen een geopolitieke regio.

| **Geopolitieke regio** | **Azure-regio's** | **ExpressRoute-locaties** |
| --- | --- | --- |
| **Australië - overheid** |Australië Centraal, Australië Centraal 2 |Canberra, Canberra2 |
| **Europa** | Frankrijk - centraal, Frankrijk - zuid, Duitsland - noord, Duitsland - west-centraal, Europa - noord, Noorwegen - oost, Noorwegen - west, Zwitserland - noord, Zwitserland - west, VK - west, VK - zuid, Europa - west |Amsterdam, Amsterdam2, Amsterdam, Parijs, Dublin, Dublin, Wales2, Genève, Londen, Londen2, Laten, Laten, Cf, Nulen, Newport(Wales), Parijs, Stavanger, Keer, Zique |
| **Noord-Amerika** |VS - oost, VS - west, VS - oost 2, VS - west 2, VS - centraal, VS - zuid-centraal, VS - noord-centraal, VS - west-centraal, Canada - centraal, Canada - oost |Atlanta, Chicago, Dallas, Denver, Las Vegas, Los Angeles, Los Angeles2, Miami, Minneapolis, Montreal, New York, Phoenix, Quebec City, Queretaro(Mexico), Quincy, San Antonio, Seattle, Silicon Valley, Silicon Valley2, Toronto, Toronto2, Vancouver, Washington DC, Washington DC2 |
| **Azië** | Azië - oost, Azië - zuidoost | Hongkong, Hongkong, Hongkong2, Laten, Laten Lump Lumpur, Singapore, Singapore2, |
| **India** | India - west, India - centraal, India - zuid |Chennai, Chennai2, Mumbai, Mumbai2 |
| **Japan** | Japan - west, Japan - oost |Osaka, Tokio, Tokio2 |
| **Oceanië** | Australië - zuidoost, Australië - oost |Auckland, Melbourne, Perth, Sydney, Sydney2 |
| **Zuid-Korea** | Korea - centraal, Korea - zuid |Busan, Seoul|
| **Uae** | VAE - centraal, VAE - noord | Moet, Moet2 |
| **Zuid-Afrika** | Zuid-Afrika - west, Zuid-Afrika - noord |Kaapstad, Johannesburg |
| **Zuid-Amerika** | Brazilië - zuid |Pseudoota, Rio de Jaota, Sao Paulo |


## <a name="regions-and-geopolitical-boundaries-for-national-clouds"></a>Regio's en geopolitieke grenzen voor nationale clouds
De volgende tabel bevat informatie over regio's en geopolitieke grenzen voor nationale clouds.

| **Geopolitieke regio** | **Azure-regio's** | **ExpressRoute-locaties** |
| --- | --- | --- |
| **Cloud van de Amerikaanse overheid** |US Gov - AZ, US Gov - Iowa, US Gov - TX, US Gov - Virginia, US DoD Central, US DoD East  |Atlanta,Chicago, Dallas, New York, Phoenix, San Antonio, Seattle, Silicon Valley, Washington DC |
| **China East** |China - oost, China - oost2 |Shanghai, Shanghai2 |
| **China - noord** |China - noord, China - noord2 |Beijing, Beijing2 |
| **Duitsland** |Duitsland Centraal, Duitsland Oost |Berlijn, Frankfurt |

Connectiviteit tussen de geopolitieke regio's wordt niet ondersteund op de standaard ExpressRoute-SKU. U moet de invoegtoepassing ExpressRoute Premium inschakelen voor ondersteuning van globale connectiviteit. Connectiviteit met nationale cloudomgevingen wordt niet ondersteund. U kunt met uw connectiviteitsprovider samenwerken als de noodzaak daartoe zich voordoet.

## <a name="expressroute-connectivity-providers"></a><a name="partners"></a>ExpressRoute-connectiviteitsproviders

De volgende tabel toont de locaties per serviceprovider. Als u alle beschikbare providers per locatie wilt weergeven, raadpleegt u [Serviceproviders per locatie](expressroute-locations-providers.md).


### <a name="global-commercial-azure"></a>Wereldwijd commercieel Azure

| **Serviceprovider** | **Microsoft Azure** | **Microsoft 365**  | **Locaties** |
| --- | --- | --- | --- |
| **[AARNet](https://www.aarnet.edu.au/network-and-services/connectivity-services/azure-expressroute)** |Ondersteund |Ondersteund |Melbourne, Sydney |
| **[Airtel](https://www.airtel.in/business/#/)** | Ondersteund | Ondersteund | Chennai2, Mumbai2 |
| **[Ais](https://business.ais.co.th/solution/en/azure-expressroute.html)** | Ondersteund | Ondersteund | Bangkok |
| **[Aryaka Networks](https://www.aryaka.com/)** |Ondersteund |Ondersteund |Amsterdam, Chicago, Dallas, Hongkong SAR, Sao Paulo, Seattle, Silicon Valley, Singapore, Tokio, Washington DC |
| **[Ascenty Data Centers](https://www.ascenty.com/en/cloud/microsoft-express-route)** |Ondersteund |Ondersteund |Sao Paulo |
| **[AT&T NetBond](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** |Ondersteund |Ondersteund |Amsterdam, Chicago, Dallas, Toronto, Londen, Silicon Valley, Singapore, Sydney, Tokio, Toronto, Washington DC |
| **[AT TOKIO](https://www.attokyo.com/connectivity/azure.html)** | Ondersteund | Ondersteund | Osaka, Tokio2 |
| **[BBIX](https://www.bbix.net/en/service/ix/)** | Ondersteund | Ondersteund | Tokio |
| **[BCX](https://www.bcx.co.za/solutions/connectivity/data-networks)** |Ondersteund |Ondersteund |Kaapstad, Johannesburg|
| **[Bell Canada](https://business.bell.ca/shop/enterprise/cloud-connect-access-to-cloud-partner-services)** |Ondersteund |Ondersteund |Montreal, Toronto, Quebec |
| **[British Telecom](https://www.globalservices.bt.com/en/solutions/products/cloud-connect-azure)** |Ondersteund |Ondersteund |Amsterdam, Amsterdam2, Chicago, Hongkong SAR,Label, Londen, Londen2, Newport(Wales), Parijs, Sao Paulo, Silicon Valley, Singapore, Sydney, Tokio, Washington DC |
| **[Bsnl](https://www.bsnl.co.in/opencms/bsnl/BSNL/services/enterprises/cloudway.html)** |Ondersteund |Ondersteund |Chennai, Mumbai |
| **[C3ntro](https://www.c3ntro.com/data1/express-route1.php)** |Ondersteund |Ondersteund |Miami |
| **CDC** | Ondersteund | Ondersteund | Canberra, Canberra2 |
| **[CenturyLink Cloud Connect](https://www.centurylink.com/cloudconnect)** |Ondersteund |Ondersteund |Amsterdam2, Chicago, Dublin, Hongkong, Las Vegas, Londen2, New York, Parijs, San Antonio, Silicon Valley, Tokio, Toronto, Washington DC, Washington DC2 |
| **[Chief Telecom](https://www.chief.com.tw/)** |Ondersteund |Ondersteund |Hongkong, Hongkong |
| **China Mobile International** |Ondersteund |Ondersteund | Hongkong, Hongkong2, Singapore |
| **China Telecom Global** |Ondersteund |Ondersteund |Hongkong, Hongkong2 |
| **China Unicom Global** |Ondersteund |Ondersteund | Hongkong, Singapore2 |
| **[Chunghwa Telecom](https://www.cht.com.tw/en/home/cht/about-cht/products-and-services/International/Cloud-Service)** |Ondersteund |Ondersteund |Taipei |
| **[Claro](https://www.usclaro.com/enterprise-mnc/connectivity/mpls/)** |Ondersteund |Ondersteund |Miami |
| **[Cologix](https://www.cologix.com/hyperscale/microsoft-azure/)** |Ondersteund |Ondersteund |Chicago, Dallas,Label, Montreal, Toronto, Denver, Washington DC |
| **[Colt](https://www.colt.net/direct-connect/azure/)** |Ondersteund |Ondersteund |Amsterdam, Amsterdam2, Parijs, Chicago, Dublin, Dublin, Hongkong, Londen, Londen2, Laten, Newport, New York, Osaka, Parijs, Silicon Valley, Silicon Valley2, Singapore2, Tokio, Washington DC, Z goed |
| **[Comcast](https://business.comcast.com/landingpage/microsoft-azure)** |Ondersteund |Ondersteund |Chicago, Silicon Valley, Washington DC |
| **[CoreSite](https://www.coresite.com/solutions/cloud-services/public-cloud-providers/microsoft-azure-expressroute)** |Ondersteund |Ondersteund |Chicago, Denver, Los Angeles, New York, Silicon Valley, Silicon Valley2, Washington DC, Washington DC2 |
| **[DE-CIX](https://www.de-cix.net/en/de-cix-service-world/cloud-exchange/find-a-cloud-service/detail/microsoft-azure)** | Ondersteund |Ondersteund |Amsterdam2, Hado2, Haddeno, Mumbai, Mumbai, Mün, New York |
| **[Devoli](https://devoli.com/expressroute)** | Ondersteund |Ondersteund | Auckland, Melbourne, Sydney |
| **du datamena** |Ondersteund |Ondersteund | 2. |
| **eir** |Ondersteund |Ondersteund |Dublin|
| **[Epsilon Global Communications](https://www.epsilontel.com/solutions/direct-cloud-connect)** |Ondersteund |Ondersteund |Singapore, Singapore2 |
| **[Equinix](https://www.equinix.com/partners/microsoft-azure/)** |Ondersteund |Ondersteund |Amsterdam, Amsterdam2, Atlanta, Parijs, Osaka, Canberra2, Chicago, Dallas, Atlanta2, Dublin, Osaka, Osaka2, Genève, Hongkong SAR, Londen, Londen2, Los Angeles, Los Angeles2, Melbourne, Miami, New York, Osaka, Parijs, Rio de Jaën, Sao Paulo, Seattle, Seattle, Silicon Valley, Singapore, Sydney, Sydney, Tokio, Toronto, Washington DC, Z dc |
| **Etisalat UAE** |Ondersteund |Ondersteund |Dubai|
| **[euNetworks](https://eunetworks.com/services/solutions/cloud-connect/microsoft-azure-expressroute/)** |Ondersteund |Ondersteund |Amsterdam, Amsterdam2, Dublin, Moet, Londen |
| **[FarEasTone](https://www.fetnet.net/corporate/en/Enterprise.html)** |Ondersteund |Ondersteund |Taipei|
| **[Fastweb](https://www.fastweb.it/grandi-aziende/cloud/scheda-prodotto/fastcloud-interconnect/)** | Ondersteund |Ondersteund |Milaan|
| **[Fibrenoire](https://fibrenoire.ca/en/services/cloudextn-2/)** |Ondersteund |Ondersteund |Montreal|
| **[Géant](https://www.geant.org/Networks)** |Ondersteund |Ondersteund |Amsterdam, Amsterdam2, Dublin, Dublin, Dublin, Dublin |
| **[GlobalConnect]()** | Ondersteund |Ondersteund | Moet, Stavanger | 
| **GTT** |Ondersteund |Ondersteund |London2 |
| **[Global Cloud Xchange (GCX)](https://globalcloudxchange.com/cloud-platform/cloud-x-fusion/)** | Ondersteund| Ondersteund | Chennai, Mumbai |
| **[iAdvantage](https://www.scx.sunevision.com/)** | Ondersteund | Ondersteund | Hongkong2 |
| **Intelsat** | Ondersteund | Ondersteund | Washington DC2 |
| **[InterCloud](https://www.intercloud.com/)** |Ondersteund |Ondersteund |Amsterdam, Chicago, Washington, Hongkong, Londen, New York, Parijs, Silicon Valley, Singapore, Washington DC, Z kong |
| **[Internet2](https://internet2.edu/services/cloud-connect/#service-cloud-connect)** |Ondersteund |Ondersteund |Chicago, Dallas, Silicon Valley, Washington DC |
| **[Internet Initiative Japan Inc. - IIJ](https://www.iij.ad.jp/en/news/pressrelease/2015/1216-2.html)** |Ondersteund |Ondersteund |Osaka, Tokio |
| **[Internet Solutions - Cloud Connect](https://www.is.co.za/solution/cloud-connect/)** |Ondersteund |Ondersteund |Kaapstad, Johannesburg, Londen |
| **[Interxion](https://www.interxion.com/why-interxion/colocate-with-the-clouds/Microsoft-Azure/)** |Ondersteund |Ondersteund |Amsterdam, Amsterdam2, Hadden, Dublin, Dublin, Londen, Laten, Cf, Parijs, Zjoe |
| **[IRIDEOS](https://irideos.it/)** |Ondersteund |Ondersteund |Milaan |
| **Iron Mountain** | Ondersteund |Ondersteund |Washington DC |
| **[IX Reach](https://www.ixreach.com/partners/cloud-partners/microsoft-azure/)**|Ondersteund |Ondersteund | Amsterdam, Londen2, Silicon Valley, Toronto, Washington DC |
| **Jaguar Network** |Ondersteund |Ondersteund |Londen, Parijs |
| **[Jisc](https://www.jisc.ac.uk/microsoft-azure-expressroute)** |Ondersteund |Ondersteund |Londen, Newport (Wales) |
| **[KINX](https://www.kinx.net/service/cloudhub/ms-expressroute/?lang=en)** |Ondersteund |Ondersteund |Seoul |
| **[Kordia](https://www.kordia.co.nz/cloudconnect)** | Ondersteund |Ondersteund |Auckland, Sydney |
| **[KPN](https://www.kpn.com/zakelijk/cloud/connect.htm)** | Ondersteund | Ondersteund | Amsterdam |
| **[Kt](https://cloud.kt.com/)** | Ondersteund | Ondersteund | Seoul |
| **[Level 3 Communications](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** |Ondersteund |Ondersteund |Amsterdam, Chicago, Dallas, Londen, Newport (Wales), Sao Paulo, Seattle, Silicon Valley, Singapore, Washington DC |
| **LG CNS** |Ondersteund |Ondersteund |Busan, Seoul |
| **[Liquid Telecom](https://www.liquidtelecom.com/products-and-services/cloud.html)** |Ondersteund |Ondersteund |Kaapstad, Johannesburg |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |Ondersteund |Ondersteund |Amsterdam, Atlanta, Auckland, Chennai, Chicago, Dallas, Denver, Ticket2, Dublin, Osaka, Genève, Hongkong, Hongkong, Hongkong2, Las Vegas, Londen, Londen2, Los Angeles, Melbourne, Miami, Miami, Montreal, New York, Osaka, Osaka, Parijs, Perth, Quebec City, San Antonio, Seattle, Silicon Valley, Singapore, Singapore2, Stavanger, Sydney, Sydney2, Tokio, Tokio2 Toronto, Washington DC, Washington DC, Washington DC2, Zns |
| **[Mtn](https://www.mtnbusiness.co.za/en/Cloud-Solutions/Pages/microsoft-express-route.aspx)** |Ondersteund |Ondersteund |Londen |
| **[Neutrona Networks](https://www.neutrona.com/index.php/azure-expressroute/)** |Ondersteund |Ondersteund |Dallas, Los Angeles, Miami, Sao Paulo, Washington DC |
| **[Next Generation Data](https://vantage-dc-cardiff.co.uk/)** |Ondersteund |Ondersteund |Newport (Wales) |
| **[NEXTDC](https://www.nextdc.com/services/axon-ethernet/microsoft-expressroute)** |Ondersteund |Ondersteund |Melbourne, Perth, Sydney, Sydney2 |
| **[Nos](https://www.nos.pt/empresas/corporate/cloud/cloud/Pages/nos-cloud-connect.aspx)** |Ondersteund |Ondersteund |Amsterdam2 |
| **[NTT Communications](https://www.ntt.com/en/services/network/virtual-private-network.html)** |Ondersteund |Ondersteund |Amsterdam, Hongkong SAR, Londen, Los Angeles, Osaka, Singapore, Sydney, Tokio, Washington DC |
| **[NTT EAST](https://business.ntt-east.co.jp/service/crossconnect/)** |Ondersteund |Ondersteund |Tokio |
| **[NTT Global DataCenters EMEA](https://hello.global.ntt/)** |Ondersteund |Ondersteund |Amsterdam2, Amsterdam |
| **[NTT SmartConnect](https://cloud.nttsmc.com/cxc/azure.html)** |Ondersteund |Ondersteund |Osaka |
| **[Ooredoo Cloud Connect](https://www.ooredoo.qa/portal/OoredooQatar/cloud-connect-expressroute)** |Ondersteund |Ondersteund |Marseille |
| **[Optus](https://www.optus.com.au/enterprise/)** |Ondersteund |Ondersteund |Melbourne, Sydney |
| **[Oranje](https://www.orange-business.com/en/products/business-vpn-galerie)** |Ondersteund |Ondersteund |Amsterdam, Amsterdam2, Moet2, Cura, Hongkong SAR, Laten, Londen, Parijs, Sao Paulo, Silicon Valley, Singapore, Sydney, Tokio, Washington DC |
| **[Orixcom](https://www.orixcom.com/cloud-solutions/)** | Ondersteund | Ondersteund | 2. |
| **[PacketFabric](https://www.packetfabric.com/cloud-connectivity/microsoft-azure)** |Ondersteund |Ondersteund |Chicago, Dallas, Las Vegas, Silicon Valley, Washington DC |
| **[PCCW Global Limited](https://consoleconnect.com/clouds/#azureRegions)** |Ondersteund |Ondersteund |Chicago, Hongkong, Hongkong2, Londen, Singapore2 |
| **[REANNZ](https://www.reannz.co.nz/products-and-services/cloud-connect/)** | Ondersteund | Ondersteund | Auckland |
| **[Opnieuw vertalitsen](https://www.retelit.it/EN/Home.aspx)** | Ondersteund | Ondersteund | Milaan | 
| **[Sejong Telecom](https://www.sejongtelecom.net/en/pages/service/cloud_ms)** |Ondersteund |Ondersteund |Seoul |
| **[Ses](https://www.ses.com/networks/signature-solutions/signature-cloud/ses-and-azure-expressroute)** | Ondersteund |Ondersteund | Londen2, Washington DC |
| **[SIFY](http://telecom.sify.com/azure-expressroute.html)** |Ondersteund |Ondersteund |Chennai, Mumbai2 |
| **[SingTel](https://www.singtel.com/about-us/news-releases/singtel-provide-secure-private-access-microsoft-azure-public-cloud)** |Ondersteund |Ondersteund |Hongkong2, Singapore, Singapore2 |
| **[Softbank](https://www.softbank.jp/biz/cloud/cloud_access/direct_access_for_az/)** |Ondersteund |Ondersteund |Osaka, Tokio |
| **[Sohonet](https://www.sohonet.com/fastlane/)** |Ondersteund |Ondersteund |London2 |
| **[Spark NZ](https://www.sparkdigital.co.nz/solutions/connectivity/cloud-connect/)** |Ondersteund |Ondersteund |Auckland, Sydney |
| **[Sprint](https://business.sprint.com/solutions/cloud-networking/)** |Ondersteund |Ondersteund |Chicago, Silicon Valley, Washington DC |
| **[Swisscom](https://www.swisscom.ch/en/business/enterprise/offer/cloud-data-center/microsoft-cloud-services/microsoft-azure-von-swisscom.html)** | Ondersteund | Ondersteund | Genève, Zjoe |
| **[Tata Communications](https://www.tatacommunications.com/solutions/network/cloud-ready-networks/)** |Ondersteund |Ondersteund |Amsterdam, Chennai, Hongkong SAR, Londen, Mumbai, Sao Paulo, Silicon Valley, Singapore, Washington DC |
| **[Telefonica](https://www.business-solutions.telefonica.com/es/enterprise/solutions/efficient-infrastructure/managed-voice-data-connectivity/)** |Ondersteund |Ondersteund |Amsterdam, Sao Paulo |
| **[Telehouse - KDDI](https://www.telehouse.net/solutions/cloud-services/cloud-link)** |Ondersteund |Ondersteund |Londen, Londen2, Singapore2 |
| **Telenor** |Ondersteund |Ondersteund |Amsterdam, Londen, Laten |
| **[Telia Carrier](https://www.teliacarrier.com/)** | Ondersteund | Ondersteund |Amsterdam, Chicago, Dallas, Kong, Hongkong, Londen,Label, Parijs, Silicon Valley, Washington DC |
| **[Telin](https://www.telin.net/product/data-connectivity/telin-cloud-exchange)** | Ondersteund | Ondersteund |Jakarta |
| **Telmex Uninet**| Ondersteund | Ondersteund | Dallas |
| **[Telstra Corporation](https://www.telstra.com.au/business-enterprise/network-services/networks/cloud-direct-connect/)** |Ondersteund |Ondersteund |Melbourne, Singapore, Sydney |
| **[Telus](https://www.telus.com)** |Ondersteund |Ondersteund |Montreal, Seattle, Quebec City, Toronto, Seattle |
| **[Teraco](https://www.teraco.co.za/services/africa-cloud-exchange/)** |Ondersteund |Ondersteund |Kaapstad, Johannesburg |
| **[TIME dotCom](https://www.time.com.my/enterprise/connectivity/direct-cloud)** | Ondersteund | Ondersteund | Kuala Lumpur |
| **[Tokai Communications](https://www.tokai-com.co.jp/en/)** | Ondersteund | Ondersteund | Osaka, Tokio2 |
| **[Transtelco](https://transtelco.net/enterprise-services/)** |Ondersteund |Ondersteund |Dallas, Queretaro (Mexico)|
| **T-Systems** |Ondersteund |Ondersteund |Frankfurt|
| **[UOLDIVEO](https://www.uoldiveo.com.br/)** |Ondersteund |Ondersteund |Sao Paulo |
| **[UIH](https://www.uih.co.th/en/network-solutions/global-network/cloud-direct-for-microsoft-azure-expressroute)** | Ondersteund | Ondersteund | Bangkok |
| **[Verizon](https://enterprise.verizon.com/products/network/application-enablement/secure-cloud-interconnect/)** |Ondersteund |Ondersteund |Amsterdam, Chicago, Dallas, Hongkong SAR, Londen, Mumbai, Silicon Valley, Singapore, Sydney, Tokio, Toronto, Washington DC |
| **[Viasat](http://www.directcloud.viasatbusiness.com/)** | Ondersteund | Ondersteund | Washington DC2 |
| **[Vocus Group NZ](https://www.vocus.co.nz/business/cloud-data-centres)** | Ondersteund | Ondersteund | Auckland, Sydney |
| **[Vodafone](https://www.vodafone.com/business/global-enterprise/global-connectivity/vodafone-ip-vpn-cloud-connect)** |Ondersteund |Ondersteund |Amsterdam2, Londen, Singapore |
| **[Vodafone Idea](https://www.vodafone.in/business/enterprise-solutions/connectivity/vpn-extended-connect)** | Ondersteund | Ondersteund | Mumbai2 |
| **[Zayo](https://www.zayo.com/solutions/industries/cloud-connectivity/microsoft-expressroute)** |Ondersteund |Ondersteund |Amsterdam, Chicago, Dallas, Denver, Londen, Los Angeles, Montreal, New York, Parijs, Seattle, Silicon Valley, Toronto, Washington DC, Washington DC2 |

 **+** geeft aan dat binnenkort beschikbaar is

### <a name="national-cloud-environment"></a>Nationale cloudomgeving

Nationale Azure-clouds zijn geïsoleerd van elkaar en van wereldwijde commerciële Azure. ExpressRoute voor de ene Azure-cloud kan geen verbinding maken met de Azure-regio's in de andere. 

### <a name="us-government-cloud"></a>Cloud van de Amerikaanse overheid

| **Serviceprovider** | **Microsoft Azure** | **Office 365** | **Locaties** |
| --- | --- | --- | --- |
| **[AT&T NetBond](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** |Ondersteund |Ondersteund |Chicago, Phoenix, Silicon Valley, Washington DC |
| **[CenturyLink Cloud Connect](https://www.centurylink.com/cloudconnect)** |Ondersteund |Ondersteund |New York, Phoenix, San Antonio, Washington DC |
| **[Equinix](https://www.equinix.com/partners/microsoft-azure/)** |Ondersteund |Ondersteund |Atlanta, Chicago, Dallas, New York, Seattle, Silicon Valley, Washington DC |
| **[Level 3 Communications](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** |Ondersteund |Ondersteund |Chicago, Silicon Valley, Washington DC |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |Ondersteund | Ondersteund | Chicago, Dallas, San Antonio, Seattle, Washington DC |
| **[Verizon](http://news.verizonenterprise.com/2014/04/secure-cloud-interconnect-solutions-enterprise/)** |Ondersteund |Ondersteund |Chicago, Dallas, New York, Silicon Valley, Washington DC |

### <a name="china"></a>China

| **Serviceprovider** | **Microsoft Azure** | **Office 365** | **Locaties** |
| --- | --- | --- | --- |
| **China Telecom** |Ondersteund |Niet ondersteund |China, China2, Shanghai, Shanghai2 |
| **China Unicom** | Ondersteund | Niet ondersteund | Beijing2, Shanghai2 |
| **[GDS](http://www.gds-services.com/en/about_2.html)** |Ondersteund |Niet ondersteund |Beijing2, Shanghai2 |

Zie [ExpressRoute in China voor meer informatie.](http://www.windowsazure.cn/home/features/expressroute/)

### <a name="germany"></a>Duitsland

| **Serviceprovider** | **Microsoft Azure** | **Office 365** | **Locaties** |
| --- | --- | --- | --- |
| **[Colt](https://www.colt.net/direct-connect/azure/)** |Ondersteund |Niet ondersteund |Frankfurt |
| **[Equinix](https://www.equinix.com/partners/microsoft-azure/)** |Ondersteund |Niet ondersteund |Frankfurt |
| **[e-shelter](https://www.e-shelter.de/en/microsoft-expressroute)** |Ondersteund |Niet ondersteund |Berlijn |
| **Interxion** |Ondersteund |Niet ondersteund |Frankfurt |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |Ondersteund  | Niet ondersteund | Berlijn |
| **T-Systems** |Ondersteund |Niet ondersteund |Berlijn |

## <a name="connectivity-through-exchange-providers"></a>Connectiviteit via Exchange-providers

Als uw connectiviteitsprovider niet wordt vermeld in de vorige secties, kunt u alsnog verbinding maken.

* Neem contact op met uw connectiviteitsprovider om na te gaan of ze zijn verbonden met een van de exchange-punten in de bovenstaande tabel. Via de volgende koppelingen vindt u meer informatie over services die door de exchange-providers worden verstrekt. Verschillende connectiviteitsproviders zijn al verbonden met Ethernet-exchange-punten.
  * [Cologix](https://www.cologix.com/)
  * [CoreSite](https://www.coresite.com/)
  * [DE-CIX](https://www.de-cix.net/en/de-cix-service-world/cloud-exchange)
  * [Equinix Cloud Exchange](https://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
  * [Interxion](https://www.interxion.com/products/interconnection/cloud-connect/)
  * [IX Reach](https://www.ixreach.com/partners/cloud-partners/microsoft-azure/)
  * [Megaport](https://www.megaport.com/services/microsoft-expressroute/)
  * [NextDC](https://www.nextdc.com/)
  * [PacketFabric](https://www.packetfabric.com/cloud-connectivity/microsoft-azure) 
  * [Teraco](https://www.teraco.co.za/platform-teraco/africa-cloud-exchange/)

* Vraag uw connectiviteitsprovider om uw netwerk uit te breiden tot de gewenste peeringlocatie.
  * Vergewis u ervan dat de connectiviteitsprovider uw connectiviteit uitbreidt op een maximaal beschikbare manier, zodat er geen storingspunten zijn.
* Vraag een ExpressRoute-circuit aan met het exchange-punt wanneer uw connectiviteitsprovider verbinding maakt met Microsoft.
  * Volg de stappen in [Create an ExpressRoute circuit](expressroute-howto-circuit-classic.md) (Een ExpressRoute-circuit maken) om connectiviteit in te stellen.

## <a name="connectivity-through-satellite-operators"></a>Connectiviteit via satellietoperators
Als u extern bent en geen glasvezelconnectiviteit hebt of als u andere verbindingsopties wilt verkennen, kunt u de volgende satellietoperators controleren. 

* Intelsat
* [Ses](https://www.ses.com/networks/signature-solutions/signature-cloud/ses-and-azure-expressroute)
* [Viasat](http://www.directcloud.viasatbusiness.com/)

## <a name="connectivity-through-additional-service-providers"></a>Connectiviteit via aanvullende serviceproviders

| **Connectiveitsprovider** | **Exchange** | **Locaties** |
| --- | --- | --- |
| **[1CLOUDSTAR](https://www.1cloudstar.com/service/cloudconnect-azure-expressroute/)** | Equinix |Singapore |
| **[Airgate Technologies, Inc.](https://www.airgate.ca/)** | Equinix, Cologix | Toronto, Montreal |
| **[Alaska Communications](https://www.alaskacommunications.com/Business)** |Equinix |Seattle |
| **[Altice Business](https://golightpath.com/transport)** |Equinix |New York, Washington DC |
| **[Arteria Networks Corporation](https://www.arteria-net.com/business/service/cloud/sca/)** |Equinix |Tokio |
| **[Axtel](https://alestra.mx/landing/expressrouteazure/)** |Equinix |Dallas|
| **[Beanfield Metroconnect](https://www.beanfield.com/business/cloud-exchange)** |Megaport |Toronto|
| **[Bezeq International Ltd.](https://www.bezeqint.net/english)** | euNetworks | Londen |
| **[BICS](https://bics.com/bics-solutions-suite/cloud-connect/)** | Equinix | Amsterdam, Frankfurt, Londen, Singapore, Washington DC |
| **[BroadBand Tower, Inc.](https://www.bbtower.co.jp/product-service/data-center/network/dcconnect-for-azure/)** | Equinix | Tokio |
| **[C3ntro Telecom](https://www.c3ntro.com/data/express-route)** | Equinix, Megaport | Dallas |
| **[Chief](https://www.chief.com.tw/)** | Equinix | Hongkong SAR |
| **[Cinia](https://www.cinia.fi/en/services/connectivity-services/direct-public-cloud-connection.html)** | Equinix, Megaport | Frankfurt, Hamburg |
| **[CloudXpress](https://www2.telenet.be/fr/business/produits-services/internet/cloudxpress/)** | Equinix | Amsterdam | 
| **[CMC Telecom](https://cmctelecom.vn/san-pham/value-added-service-and-it/cmc-telecom-cloud-express-en/)** | Equinix | Singapore | 
| **[Aptum Technologies](https://aptum.com/services/cloud/managed-azure/)**| Equinix | Montreal, Toronto |
| **[CoreAzure](https://www.coreazure.com/)**| Equinix | Londen |
| **[Cox Business](https://www.cox.com/business/networking/cloud-connectivity.html)**| Equinix | Dallas, Silicon Valley, Washington DC |
| **[Hadoe](https://fiber.crowncastle.com/solutions/added/cloud-connect)**| Equinix | Atlanta, Chicago, Dallas, Los Angeles, New York, Washington DC |
| **[Data Foundry](https://www.datafoundry.com/services/cloud-connect)** | Megaport | Dallas |
| **[Epsilon Telecommunications Limited](https://www.epsilontel.com/solutions/cloud-connect/)** | Equinix | London, Singapore, Washington DC |
| **[Eurofiber](https://eurofiber.nl/microsoft-azure/)** | Equinix | Amsterdam |
| **[Exponential E](https://www.exponential-e.com/services/connectivity-services/cloud-connect-exchange)** | Equinix | Londen |
| **[Fastweb S.p.A](https://www.fastweb.it/grandi-aziende/connessione-voce-e-wifi/scheda-prodotto/rete-privata-virtuale/)** | Equinix | Amsterdam |
| **[Fibrenoire](https://www.fibrenoire.ca/en/cloudextn)** | Megaport | Quebec |
| **[Gtt Communications Inc](https://www.gtt.net)** |Equinix | Washington DC |
| **[Gulf Bridge International](https://gbiinc.com/)** | Equinix | Amsterdam |
| **[HSO](https://www.hso.co.uk/products/cloud-direct)** |Equinix | Londen, Slough |
| **[IVedha Inc](http://www.ivedha.com/cloud/manage-azure-cloud/express-route-4/)**| Equinix | Toronto |
| **[Telecommunicatieam Telecom Telecom B.S.C](http://www.kalaam-telecom.com/azure/)**| Level 3 Communications |Amsterdam |
| **LGA Telecom** |Equinix |Singapore|
| **[Macroview Telecom](http://www.macroview.com/en/scripts/catitem.php?catid=solution&sectionid=expressroute)** |Equinix |Hongkong SAR 
| **[Macquarie Telecom Group](https://macquariegovernment.com/secure-cloud/secure-cloud-exchange/)** | Megaport | Sydney |
| **[MainOne](https://www.mainone.net/services/connectivity/cloud-connect/)** |Equinix | Amsterdam |
| **[Masergy](https://www.masergy.com/solutions/hybrid-networking/cloud-marketplace/microsoft-azure)** | Equinix | Washington DC |
| **[Mtn](https://www.mtnbusiness.co.za/en/Cloud-Solutions/Pages/microsoft-express-route.aspx)** | Teraco | Kaapstad, Johannesburg |
| **[NexGen Networks](https://www.nexgen-net.com/nexgen-networks-direct-connect-microsoft-azure-expressroute.html)** | Interxion | Londen |
| **[Nianet](https://nianet.dk/produkter/internet/microsoft-expressroute)** |Equinix | Amsterdam, Frankfurt |
| **[Oncore Cloud Service Inc](https://www.oncore.cloud/services/ue-for-expressroute)**| Equinix | Toronto |
| **[POST Telecom Luxembourg](https://www.teralinksolutions.com/cloud-connectivity/cloudbridge-to-azure-expressroute/)**|Equinix | Amsterdam |
| **[Proximus](https://www.proximus.be/en/id_b_cl_proximus_external_cloud_connect/companies-and-public-sector/discover/magazines/expert-blog/proximus-external-cloud-connect.html)**|Equinix | Amsterdam, Dublin, Londen, Parijs |
| **[QSC AG](https://www2.qbeyond.de/en/)** |Interxion | Frankfurt |  
| **[RETN](https://retn.net/services/cloud-connect/)** | Equinix | Amsterdam |
| **[Tata Teleservices](https://www.tatateleservices.com/business-services/data-services/secure-cloud-connect)** | Tata Communications | Chennai, Mumbai |
| **Rogers** | Cologix, Equinix | Montreal, Toronto |
| **[Spectrum Enterprise](https://enterprise.spectrum.com/services/cloud/cloud-connect.html)** | Equinix | Chicago, Dallas, Los Angeles, New York, Silicon Valley | 
| **[Tamares Telecom](http://www.tamarestelecom.com/our-services/#Connectivity)** | Equinix | Londen | 
| **[TDC Erhverv](https://tdc.dk/Produkter/cloudaccessplus)** | Equinix | Amsterdam | 
| **[Telecom Italia Sparkle](https://www.tisparkle.com/our-platform/corporate-platform/sparkle-cloud-connect#catalogue)**| Equinix | Amsterdam |
| **[Telekom Deutschland GmbH](https://cloud.telekom.de/de/infrastruktur/managed-it-services/managed-hybrid-infrastructure-mit-microsoft-azure)** | Interxion | Amsterdam, Frankfurt |
| **[Telia](https://www.telia.se/foretag/losningar/produkter-tjanster/datanet)** | Equinix | Amsterdam |
| **[ThinkTel](https://www.thinktel.ca/services/agile-ix-data/expressroute/)** | Equinix | Toronto | 
| **[United Information Highway (UIH)](https://www.uih.co.th/en/internet-solution/cloud-direct/uih-cloud-direct-for-microsoft-azure-expressroute)**| Equinix | Singapore |
| **[Venha Pra Nuvem](https://venhapranuvem.com.br/)** | Equinix | Sao Paulo |
| **[Webair](https://www.webair.com/microsoft-express-route-partnership/)**| Megaport | New York |
| **[Windstream](https://www.windstreambusiness.com/solutions/cloud-services/cloud-and-managed-hosting-services)**| Equinix | Chicago, Silicon Valley, Washington DC |
| **[X2nsat Inc.](https://www.x2nsat.com/expressroute/)** |Coresite |Silicon Valley, Silicon Valley 2|
| **Zain** |Equinix |Londen|
| **[Zertia](https://www.zertia.es)**| Level 3 | Madrid |
| **[Zirro](https://zirro.com/services/)**| Cologix, Equinix | Montreal, Toronto |

## <a name="connectivity-through-datacenter-providers"></a>Connectiviteit via datacenterproviders

| **Provider** | **Exchange** |
| --- | --- |
| **[CyrusOne](https://cyrusone.com/enterprise-data-center-services/connectivity-and-interconnection/cloud-connectivity-reaching-amazon-microsoft-google-and-more/microsoft-azure-expressroute/?doing_wp_cron=1498512235.6733090877532958984375)** | Megaport, PacketFabric |
| **[Cyxtera](https://www.cyxtera.com/data-center-services/interconnection)** | Megaport, PacketFabric |
| **[Clip](https://www.databank.com/platforms/connectivity/cloud-direct-connect/)** | Megaport |
| **[DataFoundry](https://www.datafoundry.com/services/cloud-connect/)** | Megaport |
| **[Digital Realty](https://www.digitalrealty.com/services/interconnection/service-exchange/)** | IX Reach, Megaport PacketFabric |
| **[EdgeConnex](https://www.edgeconnex.com/services/edge-data-centers-proximity-matters/)** | Megaport, PacketFabric |
| **[Flexential](https://www.flexential.com/connectivity/cloud-connect-microsoft-azure-expressroute)** | IX Reach, Megaport, PacketFabric |
| **[QTS-datacenters](https://www.qtsdatacenters.com/hybrid-solutions/connectivity/azure-cloud )** | Megaport, PacketFabric |
| **[Stream Data Centers]( https://www.streamdatacenters.com/products-services/network-cloud/ )** | Megaport |
| **[RagingWire Data Centers](https://www.ragingwire.com/wholesale/wholesale-data-centers-worldwide-nexcenters)** | IX Reach, Megaport, PacketFabric |
| **[vXchnge](https://www.vxchnge.com/colocation-services/interconnection)** | IX Reach, Megaport |
| **[T5 Datacenters](https://t5datacenters.com/)** | IX Reach |

## <a name="connectivity-through-national-research-and-education-networks-nren"></a>Connectiviteit via National Research and Education Networks (NREN)

| **Provider**|
| --- |
| **AARNET**| 
| **DeIC, through GÉANT**|
| **GARR via GÉANT**|
| **GÉANT**|
| **HEAnet via GÉANT**|
| **Internet2**|
| **JISC**|
| **RedIRIS via GÉANT**|
| **SINET**|
| **Surfnet via GÉANT**|

* Als uw connectiviteitsprovider hier niet wordt vermeld, controleert u of ze zijn verbonden aan een van de hierboven vermelde ExpressRoute Exchange-Partners.

## <a name="expressroute-system-integrators"></a>ExpressRoute-SI's
Het inschakelen van particuliere connectiviteit conform uw specifieke behoeften kan lastig zijn, al naargelang de schaal van uw netwerk. U kunt alle SI's uit de volgende tabel gebruiken om u te helpen met de voorbereidingen voor ExpressRoute.

| **System integrator** | **Continent** |
| --- | --- |
| **[Altogee](https://altogee.be/diensten/express-route/)** | Europa |
| **[Avanade Inc.](https://www.avanade.com/)** | Azië, Europa, Noord-Amerika, Zuid-Amerika |
| **[Bright Skies GmbH](https://bskies.io/expressroute)** | Europa
| **[Ensyst](https://www.ensyst.com.au)** | Azië
| **[Equinix Professional Services](https://www.equinix.com/services/consulting/)** | Noord-Amerika |
| **[FlexManage](https://www.flexmanage.com/cloud)** | Noord-Amerika |
| **[Lightstream](https://www.lightstream.tech/partners/microsoft-azure/)** | Noord-Amerika |
| **[The IT Consultancy Group](https://itconsult.com.au/)** | Australië |
| **[MOQdigital](https://www.moqdigital.com/insights)** | Australië |
| **[MSG Services](https://www.msg-services.de/it-services/managed-services/cloud-outsourcing/)** | Europa (Duitsland) |
| **[Nelite](https://www.exakis-nelite.com/offres/)** | Europa |
| **[New Signature](https://newsignature.com/technologies/express-route/)** | Europa |
| **[OneAs1a](https://www.oneas1a.com/connectivity.html)** | Azië |
| **[Orange Networks](https://orange-networks.com/blog/88-azureexpressroute)** | Europa |
| **[Perficient](https://www.perficient.com/Partners/Microsoft/Cloud/Azure-ExpressRoute)** | Noord-Amerika |
| **[Presidio](https://www.presidio.com/subpage/1107/microsoft-azure)** | Noord-Amerika |
| **[sol-tec](https://www.sol-tec.com/what-we-do/)** | Europa |
| **[Venha Pra Nuvem](https://venhapranuvem.com.br/)** | Zuid-Amerika |
| **[Vigilant.IT](https://vigilant.it/expressroute)** | Australië |

## <a name="next-steps"></a>Volgende stappen
* Zie veelgestelde vragen over ExpressRoute voor [meer informatie over ExpressRoute.](expressroute-faqs.md)
* Controleer of aan alle vereisten is voldaan. Zie [ExpressRoute prerequisites](expressroute-prerequisites.md) (Vereisten voor ExpressRoute).

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "Locatiekaart"
