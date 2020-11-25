---
title: Netwerk architectuur van SAP HANA op Azure (grote exemplaren) | Microsoft Docs
description: De netwerk architectuur van het implementeren van SAP HANA op Azure (grote exemplaren).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/15/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: bf9fd361dea43b1cfa6ae45710a3a7bbf4e4de03
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96006282"
---
# <a name="sap-hana-large-instances-network-architecture"></a>Netwerk architectuur van SAP HANA (grote exemplaren)

De architectuur van Azure Network Services is een belang rijk onderdeel van de geslaagde implementatie van SAP-toepassingen op HANA grote instanties. Doorgaans hebben SAP HANA op Azure-implementaties (grote instanties) een grotere SAP-landschap met verschillende SAP-oplossingen, met verschillende grootten van data bases, CPU-resource gebruik en geheugen gebruik. Waarschijnlijk zijn niet alle IT-systemen in azure al aanwezig. Uw SAP-landschap is vaak hybriden van een DBMS-punt en een SAP-toepassings punt met behulp van een combi natie van netweave en S/4HANA en SAP HANA en ander DBMS. Azure biedt verschillende services waarmee u de verschillende DBMS-, netweave-en S/4HANA-systemen in azure kunt uitvoeren. Azure biedt u ook netwerk technologie waarmee Azure eruitziet als een virtueel Data Center naar uw on-premises software-implementaties

Tenzij uw volledige IT-systemen worden gehost in Azure. Azure-netwerk functionaliteit wordt gebruikt om de on-premises wereld met uw Azure-assets te verbinden zodat Azure eruit ziet als een virtueel Data Center van uw bedrijf. De functionaliteit van het Azure-netwerk wordt gebruikt: 

- Virtuele Azure-netwerken zijn verbonden met het [ExpressRoute](https://azure.microsoft.com/services/expressroute/) -circuit dat verbinding maakt met uw on-premises netwerk assets.
- Een ExpressRoute-circuit dat on-premises met Azure verbindt, moet een minimale band breedte van [1 Gbps of hoger](https://azure.microsoft.com/pricing/details/expressroute/)hebben. Deze minimale band breedte biedt voldoende band breedte voor de overdracht van gegevens tussen on-premises systemen en systemen die worden uitgevoerd op Vm's. Het biedt ook voldoende band breedte voor de verbinding met Azure-systemen van on-premises gebruikers.
- Alle SAP-systemen in Azure worden in virtuele netwerken ingesteld om met elkaar te communiceren.
- Active Directory en DNS die on-premises worden gehost, worden uitgebreid naar Azure via ExpressRoute van on-premises of worden volledig uitgevoerd in Azure.

Voor het specifieke geval van het integreren van HANA grote instanties in de Azure Data Center-netwerk infrastructuur, wordt ook de Azure ExpressRoute-technologie gebruikt


> [!NOTE] 
> Slechts één Azure-abonnement kan worden gekoppeld aan slechts één Tenant in een HANA grote instantie stempel in een specifieke Azure-regio. Daarentegen kan één HANA-Tenant met een grote instantie stempel worden gekoppeld aan slechts één Azure-abonnement. Deze vereiste is consistent met andere factureer bare objecten in Azure.

Als SAP HANA op Azure (grote instanties) wordt geïmplementeerd in meerdere verschillende Azure-regio's, wordt een afzonderlijke Tenant geïmplementeerd in de HANA-stempel van de grote instantie. U kunt beide onder hetzelfde Azure-abonnement uitvoeren, zolang deze instanties deel uitmaken van hetzelfde SAP-landschap. 

> [!IMPORTANT] 
> Alleen de implementatie methode Azure Resource Manager wordt ondersteund met SAP HANA op Azure (grote exemplaren).

 

## <a name="additional-virtual-network-information"></a>Aanvullende informatie over het virtuele netwerk

Als u een virtueel netwerk wilt verbinden met ExpressRoute, moet u een Azure ExpressRoute-gateway maken. Zie [informatie over Expressroute-gateways voor Expressroute](../../../expressroute/expressroute-about-virtual-network-gateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)voor meer informatie. 

Een Azure ExpressRoute-gateway wordt gebruikt met ExpressRoute voor een infra structuur buiten Azure of een grote Azure-instantie stempel. U kunt de Azure ExpressRoute-gateway verbinden met Maxi maal vier verschillende ExpressRoute-circuits zolang deze verbindingen afkomstig zijn van verschillende micro soft Enter prise Edge-routers. Zie [SAP Hana (grote instanties) infra structuur en connectiviteit op Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)voor meer informatie. 

> [!NOTE] 
> De maximale door Voer die u kunt gebruiken met een ExpressRoute-gateway is 10 Gbps met behulp van een ExpressRoute-verbinding. Het kopiëren van bestanden tussen een virtuele machine die zich in een virtueel netwerk bevindt en een lokaal systeem (als een enkele Kopieer stroom), biedt geen volledige door Voer van de verschillende gateway-Sku's. Gebruik meerdere streams om de volledige band breedte van de ExpressRoute-gateway te benutten. Of u moet verschillende bestanden kopiëren in parallelle streams van één bestand.


## <a name="networking-architecture-for-hana-large-instance"></a>Netwerk architectuur voor HANA grote instantie
De netwerk architectuur voor HANA grote instanties kan worden onderverdeeld in vier verschillende onderdelen:

- On-premises netwerken en ExpressRoute-verbinding met Azure. Dit deel is het domein van de klant en is verbonden met Azure via ExpressRoute. Dit Expressroute-circuit wordt volledig betaald door u als klant. De band breedte moet groot genoeg zijn voor het verwerken van het netwerk verkeer tussen uw on-premises assets en de Azure-regio waarmee u verbinding maakt. Zie de rechter benedenhoek in de volgende afbeelding.
- Azure Network Services, zoals eerder besproken, met virtuele netwerken, waarbij opnieuw ExpressRoute-gateways nodig zijn. Dit deel is een gebied waar u de geschikte ontwerpen moet vinden voor de vereisten van uw toepassing, beveiliging en naleving. Of u een grote instantie van HANA gebruikt, is een ander punt om rekening te houden met het aantal virtuele netwerken en de Azure gateway-Sku's waaruit u kunt kiezen. Zie de rechter bovenhoek van de afbeelding.
- Connectiviteit van HANA grote instanties via ExpressRoute-technologie in Azure. Dit onderdeel wordt geïmplementeerd en afgehandeld door micro soft. Het enige wat u hoeft te doen, is het opgeven van sommige IP-adresbereiken nadat de implementatie van uw assets in HANA grote instantie het ExpressRoute-circuit verbindt met de virtuele netwerken. Zie [SAP Hana (grote instanties) infra structuur en connectiviteit op Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)voor meer informatie. Er zijn geen extra kosten voor u als klant voor de connectiviteit tussen het Azure Data Center netwerk infrastructuur en HANA grote instantie-eenheden.
- Netwerken binnen de HANA-stempel van grote instanties, wat voor u het meest transparant is.

![Virtueel netwerk verbonden met SAP HANA op Azure (grote instanties) en on-premises](./media/hana-overview-architecture/image1-architecture.png)

De vereiste dat uw on-premises activa moeten worden verbonden via ExpressRoute naar Azure, wordt niet gewijzigd, omdat u een grote versie van HANA gebruikt. De vereiste om een of meer virtuele netwerken te hebben waarop de virtuele machines worden uitgevoerd, die de toepassingslaag hosten die verbinding maken met de HANA-instanties die worden gehost in HANA grote instantie-eenheden, worden ook niet gewijzigd. 

De verschillen met SAP-implementaties in azure zijn:

- De HANA-eenheden voor grote instanties van de Tenant van uw klanten zijn verbonden via een ander ExpressRoute-circuit in uw virtuele netwerken. Om de laad voorwaarden te scheiden, delen de on-premises naar Azure Virtual Network ExpressRoute-circuits en de circuits tussen virtuele netwerken van Azure en HANA grote instanties niet dezelfde routers.
- Het werkbelasting profiel tussen de SAP-toepassingslaag en de HANA grote instantie is van een andere aard, met veel kleine aanvragen en bursts zoals gegevens overdrachten (resultaten sets) van SAP HANA naar de toepassingslaag.
- De SAP-toepassings architectuur is gevoeliger voor netwerk latentie dan typische scenario's waarbij gegevens worden uitgewisseld tussen on-premises en Azure.
- De Azure ExpressRoute-gateway heeft ten minste twee ExpressRoute-verbindingen. Een circuit dat is verbonden vanaf on-premises en een-netwerk dat is verbonden vanuit HANA grote instanties. Hiermee blijft alleen ruimte vrij voor andere twee extra circuits van verschillende Msee's om verbinding te maken met op ExpressRoute-gateway. Deze beperking is onafhankelijk van het gebruik van het ExpressRoute snelle pad. Alle verbonden circuits delen de maximale band breedte voor binnenkomende gegevens van de ExpressRoute-gateway.

Met revisie 3 van HANA grote instantie stempels kan de netwerk latentie tussen virtuele machines en HANA grote instantie-eenheden hoger zijn dan een typische latentie van een snelle retour duur van VM-naar-VM-netwerk. Afhankelijk van de Azure-regio, kunnen de gemeten waarden groter zijn dan de latentie van 0,7 MS die is geclassificeerd als onder gemiddeld in [SAP Note #1100926-Veelgestelde vragen: netwerk prestaties](https://launchpad.support.sap.com/#/notes/1100926/E). Afhankelijk van de Azure-regio en het hulp programma voor het meten van de round-trip latentie tussen een Azure-VM en de HANA-eenheid voor grote instanties, kan de gemeten latentie tot en met 2 milliseconden zijn. Klanten implementeren echter op SAP HANA gebaseerde productie-SAP-toepassingen met succes op SAP HANA grote instantie. Zorg ervoor dat u uw bedrijfs processen grondig test in azure HANA grote exemplaren. Met een nieuwe functionaliteit, het ExpressRoute snelle pad, kan de netwerk latentie tussen HANA grote instanties en virtuele machines in azure aanzienlijk verminderen (zie hieronder). 

Met revisie 4 van HANA grote instantie tempels, is de netwerk latentie tussen virtuele Azure-machines die zijn geïmplementeerd in nabij de HANA-stempel van de grote instantie, in het gestuit om te voldoen aan het gemiddelde of beter dan de gemiddelde classificatie zoals beschreven in [SAP Note #1100926-Veelgestelde vragen: netwerk prestaties als het](https://launchpad.support.sap.com/#/notes/1100926/E) snelle pad van Azure ExpressRoute is geconfigureerd (zie hieronder). Als u virtuele Azure-machines in een nabijheid wilt implementeren in HANA grote instantie-eenheden van Revision 4, moet u gebruikmaken van [Azure proximity placement groups](../../linux/co-location.md). Hoe proximity-plaatsings groepen kunnen worden gebruikt om de SAP-toepassingslaag te vinden in hetzelfde Azure-Data Center als revisie 4 gehoste HANA grote instantie-eenheden, worden beschreven in [Azure proximity placement groups voor optimale netwerk latentie met SAP-toepassingen](sap-proximity-placement-scenarios.md).

Als u een deterministische netwerk latentie tussen Vm's en HANA grote instanties wilt bieden, is de keuze van de SKU van de ExpressRoute-gateway essentieel. In tegens telling tot de verkeers patronen tussen on-premises en virtuele machines, kan het verkeers patroon tussen Vm's en HANA grote instanties kleine, maar hoge bursts van aanvragen en gegevens volumes ontwikkelen. Om dergelijke bursts goed te kunnen afhandelen, wordt het gebruik van de Ultra Performance gateway-SKU sterk aangeraden. Het gebruik van de Ultra Performance gateway-SKU als een ExpressRoute-gateway is verplicht voor de klasse type II van HANA grote instanties.

> [!IMPORTANT] 
> Gezien het algehele netwerk verkeer tussen de SAP-toepassing en de database lagen, worden alleen de high performance-of Ultra Performance gateway-Sku's voor virtuele netwerken ondersteund om verbinding te maken met SAP HANA op Azure (grote exemplaren). Voor de Ultra Performance-SKU van HANA-grote exemplaren van het type II wordt alleen de ExpressRoute-gateway ondersteund als gateway. Uitzonde ringen zijn van toepassing wanneer u ExpressRoute snelle pad gebruikt (zie hieronder)

### <a name="expressroute-fast-path"></a>ExpressRoute snelle pad
Om de latentie te verlagen, kreeg ExpressRoute Fast Path geïntroduceerd en uitgebracht in mei 2019 voor de specifieke connectiviteit van HANA grote instanties naar virtuele Azure-netwerken die de SAP-toepassings-Vm's hosten. Het belangrijkste verschil met de oplossing tot nu toe, is dat de gegevens stromen tussen Vm's en HANA grote instanties niet meer via de ExpressRoute-gateway worden gerouteerd. In plaats daarvan worden de Vm's die zijn toegewezen in de subnet ('s) van het virtuele Azure-netwerk, rechtstreeks communiceren met de exclusieve Enter prise-edge-router. 

> [!IMPORTANT] 
> De functie ExpressRoute Fast Path vereist dat de subnetten waarop de SAP-toepassings-Vm's worden uitgevoerd, zich in hetzelfde virtuele Azure-netwerk bevinden dat is verbonden met de HANA grote instanties. Vm's in azure Virtual Networks die zijn gekoppeld aan het virtuele Azure-netwerk dat rechtstreeks is verbonden met de HANA grote instantie-eenheden, profiteren niet van het snelle pad van ExpressRoute. Als gevolg hiervan worden typische hub-en spoke-netwerk ontwerpen, waarbij de ExpressRoute-circuits verbinding maken met een hub virtueel netwerk en virtuele netwerken met de SAP-toepassingslaag (spokes) worden gepeerd, het snelle pad voor optimalisatie op ExpressRoute niet. In naast biedt ExpressRoute snel pad geen ondersteuning meer voor door de gebruiker gedefinieerde routerings regels (UDR). Zie [ExpressRoute Virtual Network gateway en FastPath](../../../expressroute/expressroute-about-virtual-network-gateways.md)voor meer informatie. 


Lees het document [een virtueel netwerk verbinden met Hana grote instanties](./hana-connect-vnet-express-route.md)voor meer informatie over het configureren van ExpressRoute snelle paden.    

> [!NOTE]
> Voor een Ultra Performance ExpressRoute-gateway moet ExpressRoute snel pad werken


## <a name="single-sap-system"></a>Eén SAP-systeem

De on-premises infra structuur die eerder werd weer gegeven, is verbonden via ExpressRoute in Azure. Het ExpressRoute-circuit maakt verbinding met een micro soft Enter prise edge-router (MSEE). Zie [ExpressRoute Technical Overview](../../../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)(Engelstalig) voor meer informatie. Nadat de route tot stand is gebracht, maakt deze verbinding met de Azure-backbone.

> [!NOTE] 
> Als u SAP landschappen wilt uitvoeren in azure, maakt u verbinding met de Enter prise edge-router die het dichtst bij de Azure-regio in het SAP-landschap is. HANA grote instantie tempels worden verbonden via dedicated Enter prise-router apparaten om netwerk latentie tussen Vm's in azure IaaS en HANA grote instantie stempels te minimaliseren.

De ExpressRoute-gateway voor de virtuele machines die SAP-toepassings exemplaren hosten, zijn verbonden met een ExpressRoute-circuit dat verbinding maakt met on-premises. Hetzelfde virtuele netwerk is verbonden met een afzonderlijke Enter prise-edge-router die is toegewezen om verbinding te maken met een grote instantie stempel. Met behulp van het snelle pad van ExpressRoute wordt de gegevens stroom van HANA grote instanties naar de apps van de SAP-toepassingslaag niet meer gerouteerd via de ExpressRoute-gateway en met die de latentie van de netwerk retour verminderen.

Dit systeem is een eenvoudig voor beeld van één SAP-systeem. De SAP-toepassingslaag wordt gehost in Azure. De SAP HANA-data base wordt uitgevoerd op SAP HANA op Azure (grote exemplaren). De veronderstelling is dat de ExpressRoute-gateway band breedte van 2 Gbps of een 10 Gbps-door Voer geen knel punt weergeeft.

## <a name="multiple-sap-systems-or-large-sap-systems"></a>Meerdere SAP-systemen of grote SAP-systemen

Als er meerdere SAP-systemen of grote SAP-systemen zijn geïmplementeerd om verbinding te maken met SAP HANA op Azure (grote exemplaren), kan de door Voer van de ExpressRoute-gateway een knel punt worden. Of u wilt productie-en niet-productie systemen in verschillende virtuele Azure-netwerken isoleren. In dat geval splitst u de toepassings lagen in meerdere virtuele netwerken. U kunt ook een speciaal virtueel netwerk maken dat verbinding maakt met de HANA grote instantie voor cases zoals:

- Het uitvoeren van back-ups rechtstreeks vanuit de HANA-exemplaren in HANA grote instanties naar een virtuele machine in azure die als host fungeert voor NFS-shares.
- Grote back-ups of andere bestanden kopiëren van HANA grote instantie-eenheden naar schijf ruimte die wordt beheerd in Azure.

Gebruik een afzonderlijk virtueel netwerk om Vm's te hosten die opslag beheren voor het massaal overdragen van gegevens tussen HANA grote instanties en Azure. In deze regeling worden de gevolgen van grote bestands-of gegevens overdracht van HANA grote exemplaren naar Azure op de ExpressRoute-gateway voor de virtuele machines die de SAP-toepassingslaag uitvoeren, voor komen. 

Voor een meer schaal bare netwerk architectuur:

- Gebruik meerdere virtuele netwerken voor een enkele, grotere SAP-toepassingslaag.
- Implementeer één afzonderlijk virtueel netwerk voor elk geïmplementeerd SAP-systeem, vergeleken met het combi neren van deze SAP-systemen in afzonderlijke subnetten onder hetzelfde virtuele netwerk.

  Een meer schaal bare netwerk architectuur voor SAP HANA op Azure (grote exemplaren):

![SAP Application Layer implementeren via meerdere virtuele netwerken](./media/hana-overview-architecture/image4-networking-architecture.png)

Afhankelijk van de regels en beperkingen die u wilt Toep assen tussen de verschillende virtuele netwerken die als host fungeren voor Vm's van verschillende SAP-systemen, moet u deze virtuele netwerken gelijkwaardig maken. Zie [peering op virtueel netwerk](../../../virtual-network/virtual-network-peering-overview.md)voor meer informatie over peering voor virtuele netwerken.


## <a name="routing-in-azure"></a>Route ring in azure

Standaard zijn drie netwerk Routering overwegingen belang rijk voor SAP HANA op Azure (grote exemplaren):

* SAP HANA op Azure (grote instanties) kan alleen worden geopend via virtuele machines van Azure en de exclusieve ExpressRoute-verbinding, niet rechtstreeks vanuit on-premises. Rechtstreeks toegang van on-premises naar de HANA-eenheden voor grote instanties, zoals geleverd door micro soft aan u, is niet onmiddellijk mogelijk. De transitieve routerings beperkingen worden veroorzaakt door de huidige Azure-netwerk architectuur die wordt gebruikt voor SAP HANA grote instantie. Sommige beheerclients en toepassingen die directe toegang nodig hebben, zoals SAP Solution Manager die on-premises wordt uitgevoerd, kunnen geen verbinding maken met de SAP HANA-data base. Voor uitzonde ringen controleert u de sectie directe route ring naar HANA grote instanties.

* Als u HANA grote instantie-eenheden hebt geïmplementeerd in twee verschillende Azure-regio's voor herstel na nood gevallen, worden dezelfde tijdelijke routerings beperkingen in het verleden toegepast. Met andere woorden: IP-adressen van een HANA-eenheid voor grote instanties in één regio (bijvoorbeeld VS West) zijn niet gerouteerd naar een HANA-eenheid voor grote instanties die is geïmplementeerd in een andere regio (bijvoorbeeld VS Oost). Deze beperking is onafhankelijk van het gebruik van Azure-netwerk peering tussen regio's of de verbinding tussen de ExpressRoute-circuits die HANA grote instantie-eenheden verbinden met virtuele netwerken. Zie voor een grafische weer gave de afbeelding in de sectie ' gebruik HANA grote instantie-eenheden in meerdere regio's '. Deze beperking, die wordt geleverd bij de geïmplementeerde architectuur, heeft het onmiddellijk gebruik van HANA-systeem replicatie als nood herstel functionaliteit verboden. Voor recente wijzigingen zoekt u de sectie ' gebruik HANA grote instantie-eenheden in meerdere regio's '. 

* SAP HANA op Azure (grote exemplaren) eenheden hebben een toegewezen IP-adres uit het adres bereik van de server-IP-groep die u hebt verzonden bij het aanvragen van de implementatie van de HANA-grote instantie. Zie [SAP Hana (grote instanties) infra structuur en connectiviteit op Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)voor meer informatie. Dit IP-adres is toegankelijk via de Azure-abonnementen en het circuit waarmee virtuele Azure-netwerken met HANA grote instanties worden verbonden. Het IP-adres dat is toegewezen aan het IP-adres bereik van de server, wordt rechtstreeks toegewezen aan de hardware-eenheid. Het is *niet* meer toegewezen via NAT, zoals in de eerste implementatie van deze oplossing. 

### <a name="direct-routing-to-hana-large-instances"></a>Directe route ring naar HANA grote instanties

De transitieve route ring werkt standaard niet in deze scenario's:

* Tussen HANA grote instantie-eenheden en een on-premises implementatie.

* Tussen HANA-route ring van grote instanties die in twee verschillende regio's zijn geïmplementeerd.

Er zijn drie manieren om transitieve route ring in te scha kelen in deze scenario's:

- Een reverse-proxy om gegevens te routeren naar en van. Bijvoorbeeld: F5 BIG-IP, NGINX met Traffic Manager geïmplementeerd in het virtuele Azure-netwerk dat is verbonden met HANA grote instanties en on-premises als een oplossing voor virtuele firewall/verkeers routering.
- Het gebruik van [iptables-regels](http://www.linuxhomenetworking.com/wiki/index.php/Quick_HOWTO_%3a_Ch14_%3a_Linux_Firewalls_Using_iptables#.Wkv6tI3rtaQ) in een Linux-VM om route ring mogelijk te maken tussen on-premises locaties en Hana grote instantie-eenheden, of tussen Hana grote instantie-eenheden in verschillende regio's. De VM met IPTables moet worden geïmplementeerd in het virtuele Azure-netwerk dat is verbonden met HANA grote instanties en on-premises. De VM moet dienovereenkomstig worden aangepast, zodat de netwerk doorvoer van de virtuele machine voldoende is voor het verwachte netwerk verkeer. Voor meer informatie over VM-netwerk bandbreedte raadpleegt u de artikel [grootten van virtuele Linux-machines in azure](../../sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- [Azure firewall](https://azure.microsoft.com/services/azure-firewall/) zou een andere oplossing zijn om direct verkeer tussen on-premises en Hana grote instantie-eenheden in te scha kelen. 

Al het verkeer van deze oplossingen wordt doorgestuurd via een virtueel Azure-netwerk en het verkeer kan ook worden beperkt door de gebruikte apparaten of door Azure-netwerk beveiligings groepen, zodat bepaalde IP-adressen of IP-adresbereiken van on-premises kunnen worden geblokkeerd of expliciet worden toegestaan toegang te krijgen tot HANA grote instanties. 

> [!NOTE]  
> Houd er rekening mee dat implementatie en ondersteuning voor aangepaste oplossingen met netwerk apparaten van derden of IPTables niet wordt ondersteund door micro soft. Ondersteuning moet worden geboden door de leverancier van het gebruikte onderdeel of de integrator. 

#### <a name="express-route-global-reach"></a>Express route Global Reach
Micro soft introduceert een nieuwe functie met de naam [ExpressRoute Global Reach](../../../expressroute/expressroute-global-reach.md). Global Reach kan in twee scenario's worden gebruikt voor HANA grote instanties:

- Directe toegang vanaf on-premises inschakelen voor uw HANA grote instantie-eenheden die in verschillende regio's zijn geïmplementeerd
- Directe communicatie tussen uw HANA-eenheden voor grote instanties inschakelen die in verschillende regio's zijn geïmplementeerd


##### <a name="direct-access-from-on-premises"></a>Directe toegang vanaf on-premises
In de Azure-regio's waar Global Reach wordt aangeboden, kunt u het inschakelen van de Global Reach-functionaliteit voor uw ExpressRoute-circuit om uw on-premises netwerk te verbinden met het virtuele Azure-netwerk dat ook verbinding maakt met uw HANA-instanties voor de omvang van uw onderneming. De on-premises kant van uw ExpressRoute-circuit bevat enkele kosten implicaties. Controleer de prijzen voor [Global Reach-invoeg toepassing](https://azure.microsoft.com/pricing/details/expressroute/)voor prijzen. Er zijn geen extra kosten verbonden aan het circuit dat de HANA-grote exemplaar eenheden verbindt met Azure. 

> [!IMPORTANT]  
> In het geval van het gebruik van Global Reach voor het inschakelen van rechtstreekse toegang tussen uw HANA grote instantie-eenheden en on-premises assets, worden de netwerk gegevens en de controle stroom **niet gerouteerd via virtuele Azure-netwerken**, maar rechtstreeks tussen de micro soft Enter prise Exchange-routers. Als gevolg hiervan worden elke NSG-of ASG-regel, of een type firewall, NVA of proxy dat u hebt geïmplementeerd in een virtueel Azure-netwerk, niet doorlopend. **Als u gebruikmaakt van ExpressRoute Global Reach om direct toegang van on-premises naar HANA grote instantie-eenheden in te scha kelen en machtigingen voor toegang tot de HANA grote instantie-eenheden, moeten worden gedefinieerd in firewalls op de on-premises kant** 

##### <a name="connecting-hana-large-instances-in-different-azure-regions"></a>Verbinding maken met HANA grote instanties in verschillende Azure-regio's
Net zoals ExpressRoute Global Reach kan worden gebruikt om on-premises verbinding te maken met HANA grote instantie-eenheden, kan deze worden gebruikt voor het verbinden van twee HANA-tenants met grote instanties die voor u in twee verschillende regio's worden geïmplementeerd. De isolatie is de ExpressRoute-circuits die uw HANA grote instantie-tenants gebruiken om in beide regio's verbinding te maken met Azure. Er zijn geen extra kosten verbonden aan het verbinden van twee HANA-tenants met grote instanties die in twee verschillende regio's zijn geïmplementeerd. 

> [!IMPORTANT]  
> De gegevens stroom en controle stroom van het netwerk verkeer tussen de verschillende HANA-tenants met grote instanties worden niet gerouteerd via Azure-netwerken. Als gevolg hiervan kunt u geen gebruikmaken van Azure-functionaliteit of Nva's om communicatie beperkingen tussen uw twee HANA grote instanties tenants af te dwingen. 

Lees het document [een virtueel netwerk verbinden met Hana grote instanties](./hana-connect-vnet-express-route.md)voor meer informatie over het ophalen van ExpressRoute Global Reach ingeschakeld.


## <a name="internet-connectivity-of-hana-large-instance"></a>Internet verbinding van HANA grote instantie
HANA grote instanties hebben *geen* rechtstreekse Internet verbinding. Als voor beeld kan deze beperking ertoe leiden dat u de installatie kopie van het besturings systeem rechtstreeks kunt registreren bij de fabrikant van het besturings systeem. Mogelijk moet u werken met uw lokale SUSE Linux Enterprise Server-beheer programma Server voor abonnementen of Red Hat Enterprise Linux Subscription Manager.

## <a name="data-encryption-between-vms-and-hana-large-instance"></a>Gegevens versleuteling tussen Vm's en HANA grote instanties
Gegevens die worden overgebracht tussen HANA grote instanties en Vm's, worden niet versleuteld. Maar alleen voor de uitwisseling tussen de HANA-en JDBC-en op ODBC-gebaseerde toepassingen, kunt u de versleuteling van verkeer inschakelen. Raadpleeg [deze documentatie](https://help.sap.com/viewer/102d9916bf77407ea3942fef93a47da8/1.0.11/en-US/dbd3d887bb571014bf05ca887f897b99.html)voor meer informatie.

## <a name="use-hana-large-instance-units-in-multiple-regions"></a>Gebruik HANA grote instantie-eenheden in meerdere regio's

Als u nood herstel wilt realiseren, moet u SHANA grote instantie-eenheden hebben in meerdere Azure-regio's. Zelfs bij het gebruik van Azure [Global Vnet-peering], is transitieve route ring standaard niet in staat om te werken met tenants van de HANA-grote instanties in twee verschillende regio's. Global Reach opent echter het communicatie traject tussen de HANA-eenheden voor grote instanties die u in twee verschillende regio's hebt ingericht. Dit gebruiks scenario van ExpressRoute Global Reach maakt het volgende mogelijk:

 - HANA-systeem replicatie zonder extra proxy's of firewalls
 - Back-ups kopiëren tussen HANA grote instantie-eenheden in twee verschillende regio's om systeem kopieën of systeem vernieuwingen uit te voeren


![Een virtueel netwerk dat is verbonden met een Azure-stempel in verschillende Azure-regio's](./media/hana-overview-architecture/image8-multiple-regions.png)

In de afbeelding ziet u hoe de verschillende virtuele netwerken in beide regio's zijn verbonden met twee verschillende ExpressRoute-circuits die worden gebruikt om verbinding te maken met SAP HANA op Azure (grote exemplaren) in beide Azure-regio's (grijze lijnen). De reden hiervoor is dat twee verbindingen worden beveiligd tegen een onderbreking van de Msee's aan beide zijden. De communicatie stroom tussen de twee virtuele netwerken in de twee Azure-regio's moet worden afgehandeld over de [globale peering](/archive/blogs/azureedu/how-to-setup-global-vnet-peering-in-azure) van de twee virtuele netwerken in de twee verschillende regio's (blauwe stippel lijn). De dikke rode lijn beschrijft de ExpressRoute-Global Reach verbinding, waarmee de HANA grote instantie-eenheden van uw tenants in twee verschillende regio's met elkaar kunnen communiceren. 

> [!IMPORTANT] 
> Als u meerdere ExpressRoute-circuits hebt gebruikt, moeten de instellingen voor pad in behandeling en lokale voor keuren worden gebruikt om te zorgen voor de juiste route ring van verkeer.

**Volgende stappen**
- Raadpleeg de [opslag architectuur van SAP Hana (grote exemplaren)](hana-storage-architecture.md)