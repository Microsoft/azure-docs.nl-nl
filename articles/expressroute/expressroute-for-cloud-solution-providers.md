---
title: ExpressRoute voor Cloud Solution Providers - Azure | Microsoft Docs
description: Dit artikel bevat informatie voor Cloud Solution Providers die Azure-services en ExpressRoute in hun aanbod willen opnemen.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: article
ms.date: 10/10/2016
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: 09fee610ccc15874481ecfd4693e4b89379caa7a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92330033"
---
# <a name="expressroute-for-cloud-solution-providers-csp"></a>ExpressRoute voor Cloud Solution Providers (CSP)
Microsoft biedt grootschalige services waarmee traditionele leveranciers en distributeurs (CSP) snel nieuwe services en oplossingen voor uw klanten kunnen inrichten, zonder dat ze hoeven te investeren in de ontwikkeling van deze nieuwe services. Microsoft biedt programma's en API's waarmee de Cloud Solution Provider (CSP) Microsoft Azure-resources kan beheren namens uw klanten. Zo kan de Cloud Solution Provider (CSP) deze nieuwe services rechtstreeks beheren. Een van deze resources is ExpressRoute. Met ExpressRoute kan de CSP verbinding maken tussen de bestaande resources van de klant en de Azure-services. ExpressRoute is een snelle, persoonlijke communicatie koppeling naar Services in Azure. 

ExpressRoute bestaat uit een paar circuits voor hoge Beschik baarheid die zijn gekoppeld aan één of meer abonnementen van een klant en kunnen niet worden gedeeld door meerdere klanten. Elk circuit moet worden beëindigd in een andere router om de hoge beschikbaarheid te houden.

> [!NOTE]
> Er zijn limieten voor de band breedte en het aantal verbindingen dat mogelijk is op elk ExpressRoute-circuit. Als de behoeften van één klant hoger zijn dan deze limieten, hebben ze meerdere ExpressRoute-circuits nodig voor de implementatie van een hybride netwerk.
> 
> 

Microsoft Azure biedt een toenemend aantal services dat u aan uw klanten kunt aanbieden. ExpressRoute helpt u en uw klanten profiteren van deze services door snelle toegang tot de Microsoft Azure omgeving te bieden.

## <a name="microsoft-azure-management"></a>Microsoft Azure-beheer
Micro soft biedt Csp's met Api's voor het beheren van de Azure-klanten abonnementen door middel van programmatische integratie met uw eigen service beheersystemen. U vindt de ondersteunde beheermogelijkheden [hier](/previous-versions/windows/mt844538(v=win.10)).

## <a name="microsoft-azure-resource-management"></a>Beheer van Microsoft Azure-resources
Het contract dat u met uw klant hebt, bepaalt hoe het abonnement wordt beheerd. De CSP kan het maken en het onderhouden van resources rechtstreeks beheren, maar de klant kan ook zelf de controle behouden over het Microsoft Azure-abonnement en de Azure-resources naar behoefte maken. Als uw klant het maken van resources in hun Microsoft Azure-abonnement beheert, zullen ze een van de twee modellen gebruiken: '*connect-through*' model of '*direct-to '-* model. Deze modellen worden gedetailleerd beschreven in de volgende gedeelten.  

### <a name="connect-through-model"></a>Het model 'Doorverbinden'
![Diagram waarin het model ' Connect-through ' wordt weer gegeven.](./media/expressroute-for-cloud-solution-providers/connect-through.png)  

In het model 'Doorverbinden' maakt de CSP een rechtstreekse verbinding tussen uw datacenter en het Azure-abonnement van uw klant. De rechtstreekse verbinding wordt gemaakt met behulp van ExpressRoute en verbindt uw netwerk met Azure. De klant maakt vervolgens verbinding met uw netwerk. Dit scenario vereist dat de klant het CSP-netwerk gebruikt voor toegang tot de Azure-services. 

Als uw klant andere Azure-abonnementen heeft die niet door u worden beheerd, gebruiken ze het open bare Internet of hun eigen particuliere verbinding om verbinding te maken met de services die zijn ingericht onder het niet-CSP-abonnement. 

Voor CSP die Azure-Services beheert, wordt ervan uitgegaan dat de CSP een eerder vastgelegde identiteits opslag voor klanten heeft, die vervolgens moet worden gerepliceerd naar Azure Active Directory voor het beheer van hun CSP-abonnement via het beheren van namens-of (ADMINISTRATE). Belangrijkste Stuur Programma's voor dit scenario zijn onder andere waar een bepaalde partner of service provider een bestaande relatie met de klant heeft, de klant al gebruikmaakt van provider Services of omdat de partner een combi natie van door de provider gehoste en door Azure gehoste oplossingen biedt om flexibiliteit te bieden en klant uitdagingen te verhelpen die niet kunnen worden voldaan door de CSP. Dit model wordt geïllustreerd in de **afbeelding** hieronder.

![Diagram waarin een gedetailleerd scenario voor het model ' Connect-through ' wordt weer gegeven.](./media/expressroute-for-cloud-solution-providers/connect-through-model.png)

### <a name="connect-to-model"></a>Het model 'Verbinden met'
![Diagram waarin het model ' Connect-to ' wordt weer gegeven.](./media/expressroute-for-cloud-solution-providers/connect-to.png)

In het model 'Verbinden met' maakt de serviceprovider een rechtstreekse verbinding tussen het datacenter van de klant en het door de CSP ingerichte Azure-abonnement. Hiervoor wordt ExpressRoute gebruikt via het (klant)netwerk van de klant.

> [!NOTE]
> Voor ExpressRoute moet de klant het ExpressRoute-circuit maken en onderhouden.  
> 
> 

Voor dit verbindings scenario moet de klant rechtstreeks verbinding maken via een klant netwerk om toegang te krijgen tot het door de CSP beheerde Azure-abonnement, met behulp van een rechtstreekse netwerk verbinding die geheel of gedeeltelijk wordt gemaakt, eigendom en beheerd. Voor deze klanten wordt ervan uitgegaan dat er op dit moment geen klant identiteits opslag is ingesteld voor de provider en dat de provider de klant helpt bij het repliceren van hun huidige identificerende Store in Azure Active Directory voor het beheer van hun abonnement via ADMINISTRATE. Situaties waarvoor dit scenario geschikt is, zijn bijvoorbeeld: wanneer een partner of serviceprovider al een bestaande relatie met de klant heeft, de klant al gebruikmaakt van de services van de provider of de partner services wil bieden die alleen zijn gebaseerd op in Azure gehoste oplossingen, zonder dat hiervoor een bestaand datacenter of een bestaande infrastructuur van de provider nodig is.

![Diagram waarin een gedetailleerd scenario voor het model ' Connect-to ' wordt weer gegeven.](./media/expressroute-for-cloud-solution-providers/connect-to-model.png)

De keuze tussen deze twee opties zijn gebaseerd op de behoeften van uw klant en uw huidige behoefte om Azure-Services te leveren. Voor gedetailleerde informatie over deze modellen en het bijbehorende op rollen gebaseerde toegangsbeheer, de netwerken en de ontwerppatronen voor de identiteit volgt u de volgende links:

* **Toegangs beheer op basis van rollen (Azure RBAC)** van Azure: Azure RBAC is gebaseerd op Azure Active Directory.  Zie [hier](../role-based-access-control/role-assignments-portal.md)voor meer informatie over Azure RBAC.
* **Netwerken**: bevat informatie over de verschillende netwerkonderwerpen in Microsoft Azure.
* **Azure Active Directory (Azure AD)** : Azure AD biedt het identiteits beheer voor Microsoft Azure en SaaS-toepassingen van derden. Zie [hier](https://azure.microsoft.com/documentation/services/active-directory/)voor meer informatie over Azure AD.  

## <a name="network-speeds"></a>Netwerksnelheden
ExpressRoute ondersteunt netwerk snelheden van 50 MB/s tot 10 GB/s. Dit geeft klanten de mogelijkheid om de hoeveelheid bandbreedte aan te schaffen die nodig is voor hun specifieke omgeving.

> [!NOTE]
> De bandbreedte van het netwerk kan naar behoefte worden verhoogd zonder dat de communicatie wordt onderbroken. Als u de snelheid van het netwerk wilt verlagen, moet het circuit echter worden verbroken en opnieuw worden gemaakt met de lagere netwerksnelheid.  
> 
> 

ExpressRoute ondersteunt de verbinding van meerdere vNets met een enkel ExpressRoute-circuit voor beter gebruik van de verbindingen met hogere snelheid. Een enkel ExpressRoute-circuit kan worden gedeeld met meerdere Azure-abonnementen die eigendom zijn van dezelfde klant.

## <a name="configuring-expressroute"></a>ExpressRoute configureren
ExpressRoute kan worden geconfigureerd voor ondersteuning van drie typen verkeer ([routeringsdomeinen](#expressroute-routing-domains)) via een enkel ExpressRoute-circuit. Dit verkeer wordt gescheiden in privé-peering, micro soft-peering en open bare peering (afgeschaft). U kunt ervoor kiezen om één of alle soorten verkeer te verzenden via een enkel ExpressRoute-circuit of meerdere ExpressRoute-circuits, afhankelijk van de grootte van het ExpressRoute-circuit en de door uw klant vereiste isolatie. De beveiliging van uw klant mag niet toestaan dat openbaar verkeer en privéverkeer via hetzelfde circuit lopen.

### <a name="connect-through-model"></a>Het model 'Doorverbinden'
In een configuratie via een verbinding bent u verantwoordelijk voor alle netwerk ondersteuning om de datacenter bronnen van uw klant te koppelen aan de abonnementen die worden gehost in Azure. Elk van uw klanten die Azure-mogelijkheden willen gebruiken, hebben hun eigen ExpressRoute-verbinding nodig die door u wordt beheerd. U maakt gebruik van dezelfde methoden die de klant zou gebruiken om het ExpressRoute-circuit aan te schaffen. U voert dezelfde stappen uit die worden beschreven in het artikel [ExpressRoute werk stromen](expressroute-workflows.md) voor circuit inrichting en circuit statussen. Vervolgens configureert u de Border Gateway Protocol (BGP)-routes om het verkeer te beheren tussen het on-premises netwerk en Azure vNet.

### <a name="connect-to-model"></a>Het model 'Verbinden met'
In een configuratie voor verbinden heeft uw klant al een bestaande verbinding met Azure of initieert deze een verbinding met de Internet serviceprovider die ExpressRoute vanuit hun eigen Data Center rechtstreeks naar Azure koppelt in plaats van uw Data Center. Uw klant moet de stappen zoals hiervoor beschreven voor het model 'Doorverbinden' volgen om het inrichtingsproces te starten. Zodra het circuit tot stand is gebracht, moet uw klant de on-premises routers configureren voor toegang tot zowel uw netwerk als Azure vNets.

U kunt helpen bij het instellen van de verbinding en het configureren van de routes om de resources in uw datacenter(s) te laten communiceren met de clientresources in uw datacenter of met de resources die in Azure worden gehost.

## <a name="expressroute-routing-domains"></a>ExpressRoute-routeringsdomeinen
ExpressRoute biedt twee routerings domeinen voor nieuwe circuits: persoonlijke peering en micro soft-peering. Elk van de routerings domeinen is geconfigureerd met identieke routers in een actief-actief configuratie voor hoge Beschik baarheid. Klik [hier](expressroute-circuit-peerings.md) voor meer informatie over de ExpressRoute-routeringsdomeinen.

U kunt aangepaste routefilters definiëren, zodat alleen route(s) die u wilt toestaan of nodig hebt, zijn toegestaan. Voor meer informatie of om te zien hoe u deze wijzigingen aanbrengt, raadpleegt u het artikel: [Routering voor een ExpressRoute-circuit maken en wijzigen met behulp van PowerShell ](expressroute-howto-routing-classic.md) voor meer informatie over routeringsfilters.

> [!NOTE]
> Voor micro soft-peering moet de connectiviteit een openbaar IP-adres zijn dat eigendom is van de klant of CSP en moet voldoen aan alle gedefinieerde regels. Zie voor meer informatie de pagina [Vereisten voor ExpressRoute](expressroute-prerequisites.md).  
> 
> 

## <a name="routing"></a>Routering
ExpressRoute maakt verbinding met de Azure-netwerken via de gateway van Azure Virtual Network. Netwerkgateways bieden routering voor virtuele netwerken in Azure.

Als u virtuele netwerken in Azure maakt, wordt er ook een standaardrouteringstabel voor het vNet gemaakt om verkeer van/naar de subnetten van het vNet te leiden. Als de standaard route tabel onvoldoende is voor de oplossing, kunnen er aangepaste routes worden gemaakt om uitgaand verkeer naar aangepaste toestellen te routeren of om routes naar specifieke subnetten of externe netwerken te blok keren.

### <a name="default-routing"></a>Standaardroutering
De standaardroutetabel bevat de volgende routes:

* Routering binnen een subnet
* Subnet-naar-subnet binnen het virtuele netwerk
* Naar internet
* Virtueel netwerk-naar-virtueel netwerk via VPN-gateway
* Virtueel netwerk-naar-on-premises netwerk via een VPN- of ExpressRoute-gateway

![Diagram waarin de standaard routerings opties worden weer gegeven.](./media/expressroute-for-cloud-solution-providers/default-routing.png)  

### <a name="user-defined-routing-udr"></a>Door de gebruiker gedefinieerde routering
Door de gebruiker gedefinieerde routes geven u controle over het uitgaande verkeer vanuit het toegewezen subnet naar andere subnetten in het virtuele netwerk of via een van de andere vooraf gedefinieerde gateways (ExpressRoute; internet of VPN). De standaardrouteringstabel kan worden vervangen door een door de gebruiker gedefinieerde routeringstabel. Hiermee wordt de standaardrouteringstabel vervangen door aangepaste routes. Met door de gebruiker gedefinieerde routering kunnen klanten specifieke routes maken voor apparaten, zoals firewalls of inbraakdetectieapparaten. Daarnaast kunnen klanten de toegang tot specifieke subnetten blokkeren vanuit het subnet dat de door de gebruiker gedefinieerde route host. [Hier](../virtual-network/virtual-networks-udr-overview.md)vindt u een overzicht van User-Defined routes. 

## <a name="security"></a>Beveiliging
Afhankelijk van het model dat uw klant gebruikt ('Verbinden met' of 'Doorverbinden'), kan hij of zij de beveiligingsregels in het vNet definiëren of deze regels doorgeven aan de CSP, zodat deze de vNets kan definiëren. De volgende beveiligingscriteria kunnen worden gedefinieerd:

1. **Klantisolatie**: het Azure-platform biedt klantisolatie door de klant-ID en vNet-gegevens op te slaan in een beveiligde database. Deze wordt gebruikt om het verkeer van elke klant in te kapselen in een GRE-tunnel.
2. De regels voor de **netwerkbeveiligingsgroep (NSG)** worden gebruikt om te definiëren welk verkeer van en naar de subnetten binnen vNets in Azure mag worden geleid. De NSG bevat standaard blokkerings regels voor het blok keren van verkeer van Internet naar het vNet en toestaan van regels voor verkeer binnen een vNet. Zie [hier](https://azure.microsoft.com/blog/network-security-groups/)voor meer informatie over netwerk beveiligings groepen.
3. **Geforceerde tunneling**: dit is een optie waarmee u verkeer vanuit Azure naar internet kunt omleiden via de ExpressRoute-verbinding met het on-premises datacenter. Klik [hier](expressroute-routing.md#advertising-default-routes) voor meer informatie over geforceerde tunneling.  
4. **Versleuteling**: hoewel de ExpressRoute-circuits zijn toegewezen aan een specifieke klant, is het mogelijk dat de netwerkprovider wordt geschonden, waardoor een indringer het pakketverkeer kan zien. Een klant of CSP kan dit potentiële probleem oplossen door het verkeer te versleutelen dat gebruikmaakt van de verbinding. Dit kan worden gedaan door een beleid voor de IPSec-tunnelmodus te definiëren voor al het verkeer tussen de on-premises resources en de Azure-resources (raadpleeg de optionele tunnelmodus IPSec voor klant 1 in bovenstaande afbeelding 5: ExpressRoute-beveiliging). De tweede optie is het gebruik van een firewallapparaat op elk eindpunt van het ExpressRoute-circuit. Hiervoor moeten extra Vm's/apparaten van derden worden geïnstalleerd op beide einden om het verkeer via het ExpressRoute-circuit te versleutelen.

![alternatieve tekst](./media/expressroute-for-cloud-solution-providers/expressroute-security.png)  

## <a name="next-steps"></a>Volgende stappen
De Cloud Solution Provider-service biedt een manier om uw waarde voor uw klanten te verhogen, zonder dat u hiervoor dure infrastructuur en capaciteit hoeft aan te schaffen. U behoudt daarbij uw positie als de belangrijkste provider voor uitbesteding. Met de CSP-API kunt u zorgen voor naadloze integratie met Microsoft Azure, zodat u het beheer van Microsoft Azure binnen uw bestaande beheerframeworks kunt integreren.  

Voor meer informatie klikt u op de volgende koppelingen:

[Azure in Cloud Solution Provider-programma](/azure/cloud-solution-provider).  
[Bereid u voor om zaken te doen als een Cloud Solution Provider](https://partner.microsoft.com/solutions/cloud-reseller-pre-launch).  
[Microsoft Cloud Solution Provider-resources](https://partner.microsoft.com/solutions/cloud-reseller-resources).