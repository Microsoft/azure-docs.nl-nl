---
title: Service-eindpunten voor een virtueel Azure-netwerk
titlesuffix: Azure Virtual Network
description: Ontdek hoe u directe toegang inschakelt voor Azure-resources via een virtueel netwerk met behulp van service-eindpunten.
services: virtual-network
documentationcenter: na
author: sumeetmittal
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/08/2019
ms.author: sumi
ms.custom: ''
ms.openlocfilehash: 7d937542201792c0d1c0be69df9bd1c2b34edea3
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96004939"
---
# <a name="virtual-network-service-endpoints"></a>Service-eindpunten voor virtueel netwerk

Virtual Network (VNet) service-eind punt biedt veilige en directe connectiviteit met Azure-Services via een geoptimaliseerde route via het Azure-backbone-netwerk. Met eindpunten kunt u uw kritieke Azure-serviceresources alleen beveiligen naar uw virtuele netwerken. Met Service-eind punten kunnen privé IP-adressen in het VNet het eind punt van een Azure-service bereiken zonder dat hiervoor een openbaar IP-adres op het VNet nodig is.

Deze functie is beschikbaar voor de volgende Azure-Services en-regio's. *Micro soft. \** resource bevindt zich tussen haakjes. Schakel deze bron in vanaf de subnetten tijdens het configureren van service-eind punten voor uw service:

**Algemeen beschikbaar**

- **[Azure Storage](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json#grant-access-from-a-virtual-network)** (*micro soft. Storage*): algemeen beschikbaar in alle Azure-regio's.
- **[Azure SQL database](../azure-sql/database/vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*micro soft. SQL*): algemeen beschikbaar in alle Azure-regio's.
- **[Azure Synapse Analytics](../azure-sql/database/vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*micro soft. SQL*): algemeen beschikbaar in alle Azure-regio's.
- **[Azure database for postgresql server](../postgresql/howto-manage-vnet-using-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*micro soft. SQL*): algemeen beschikbaar in azure-regio's waar de database service beschikbaar is.
- **[Azure database for mysql server](../mysql/howto-manage-vnet-using-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*micro soft. SQL*): algemeen beschikbaar in azure-regio's waar de database service beschikbaar is.
- **[Azure database for MariaDB](https://docs.microsoft.com/azure/mariadb/concepts-data-access-security-vnet)** (*micro soft. SQL*): algemeen beschikbaar in azure-regio's waar de database service beschikbaar is.
- **[Azure Cosmos DB](../cosmos-db/vnet-service-endpoint.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*micro soft. AzureCosmosDB*): algemeen beschikbaar in alle Azure-regio's.
- **[Azure Key Vault](../key-vault/general/overview-vnet-service-endpoints.md)** (*micro soft.*-sleutel kluis): algemeen beschikbaar in alle Azure-regio's.
- **[Azure service bus](../service-bus-messaging/service-bus-service-endpoints.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*micro soft. ServiceBus*): algemeen beschikbaar in alle Azure-regio's.
- **[Azure Event hubs](../event-hubs/event-hubs-service-endpoints.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*micro soft. EventHub*): algemeen beschikbaar in alle Azure-regio's.
- **[Azure data Lake Store gen 1](../data-lake-store/data-lake-store-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*micro soft. AzureActiveDirectory*): algemeen beschikbaar in alle Azure-regio's waar ADLS gen1 beschikbaar is.
- **[Azure app service](https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions)** (*micro soft. Web*): algemeen beschikbaar in alle Azure-regio's waar app service beschikbaar is.
- **[Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-virtual-networks?tabs=portal)** (*micro soft. CognitiveServices*): algemeen beschikbaar in alle Azure-regio's waar cognitieve services beschikbaar zijn.

**Open bare preview**

- **[Azure container Registry](../container-registry/container-registry-vnet.md)** (*micro soft. ContainerRegistry*): de preview-versie die beschikbaar is in de beperkte Azure-regio's waar Azure container Registry beschikbaar is.

Controleer de pagina [Azure Virtual Network updates](https://azure.microsoft.com/updates/?product=virtual-network) voor de meest recente meldingen.

## <a name="key-benefits"></a>Belangrijkste voordelen

Service-eindpunten bieden de volgende voordelen:

- **Verbeterde beveiliging voor uw Azure-service resources**: VNet persoonlijke adres ruimten kunnen overlappen. U kunt geen overlappende spaties gebruiken om het verkeer dat afkomstig is van uw VNet, uniek te identificeren. Service-eind punten bieden de mogelijkheid om Azure-service bronnen te beveiligen met uw virtuele netwerk door de VNet-identiteit voor de service uit te breiden. Wanneer u service-eind punten in uw virtuele netwerk hebt ingeschakeld, kunt u een regel voor het virtuele netwerk toevoegen om de Azure-service bronnen te beveiligen in uw virtuele netwerk. De regel toevoeging biedt een betere beveiliging door open bare Internet toegang tot bronnen volledig te verwijderen en alleen verkeer van uw virtuele netwerk toe te staan.
- **Optimale route ring voor Azure service verkeer van uw virtuele netwerk**: vandaag zullen alle routes in uw virtuele netwerk die Internet verkeer naar uw on-premises en/of virtuele apparaten afdwingen, ook Azure service-verkeer afdwingen om dezelfde route te nemen als het Internet verkeer. Service-eindpunten bieden een optimale routering voor Azure-verkeer. 

  Eindpunten zorgen er altijd voor dat serviceverkeer rechtstreeks van uw virtuele netwerk naar de service op het Microsoft Azure-backbone-netwerk gaat. Door het verkeer op het Azure-backbone-netwerk in stand te houden, kunt u het uitgaande internetverkeer vanuit uw virtuele netwerk blijven controleren en bewaken via geforceerd tunnelen, zonder dat dit van invloed is op het serviceverkeer. Zie [virtuele netwerk verkeer routeren](virtual-networks-udr-overview.md)voor meer informatie over door de gebruiker gedefinieerde routes en geforceerde tunneling.
- **Eenvoudig te installeren met minder beheeroverhead**: u hebt geen gereserveerde, openbare IP-adressen meer nodig in uw virtuele netwerken om Azure-resources via een IP-firewall te beveiligen. Er zijn geen NAT-of gateway apparaten vereist voor het instellen van de service-eind punten. U kunt Service-eind punten configureren met een eenvoudige klik op een subnet. Er is geen extra overhead voor het onderhouden van de eind punten.

## <a name="limitations"></a>Beperkingen

- De functie is alleen beschikbaar voor virtuele netwerken die zijn geïmplementeerd met behulp van het Azure Resource Manager-implementatiemodel.
- Eindpunten worden ingeschakeld in subnetten die zijn geconfigureerd in virtuele Azure-netwerken. Eind punten kunnen niet worden gebruikt voor verkeer van uw locatie naar Azure-Services. Zie voor meer informatie [Secure Azure Service Access van on-premises](#secure-azure-services-to-virtual-networks)
- Voor Azure SQL geldt een service-eindpunt alleen voor Azure-serviceverkeer binnen de regio van een virtueel netwerk. Voor Azure Storage worden eind punten ook uitgebreid met gekoppelde regio's waar u het virtuele netwerk implementeert ter ondersteuning van Read-Access-verkeer Geo-Redundant Storage (RA-GRS) en Geo-Redundant Storage (GRS). Zie [Gekoppelde Azure-regio's](../best-practices-availability-paired-regions.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-paired-regions) voor meer informatie.
- Voor Azure Data Lake Storage (ADLS) gen 1 is de integratie mogelijkheid voor VNet alleen beschikbaar voor virtuele netwerken binnen dezelfde regio. Houd er ook rekening mee dat de virtuele netwerk integratie voor ADLS Gen1 de eindpunt beveiliging van het virtuele netwerk tussen uw virtuele netwerk en Azure Active Directory (Azure AD) gebruikt om extra beveiligings claims in het toegangs token te genereren. Deze claims worden vervolgens gebruikt om het virtuele netwerk te verifiëren bij het Data Lake Storage Gen1-account en toegang toe te staan. De tag *micro soft. AzureActiveDirectory* die wordt vermeld onder Services die service-eind punten ondersteunen, wordt alleen gebruikt voor de ondersteuning van service-eind punten voor ADLS gen 1. Azure AD biedt geen ondersteuning voor service-eind punten. Zie [netwerk beveiliging in azure data Lake Storage gen1](../data-lake-store/data-lake-store-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)voor meer informatie over de integratie van Azure data Lake Store gen 1-VNet.

## <a name="secure-azure-services-to-virtual-networks"></a>Azure-Services beveiligen met virtuele netwerken

- Een service-eindpunt voor een virtueel netwerk biedt de identiteit van het virtuele netwerk voor de Azure-service. Wanneer u service-eind punten in uw virtuele netwerk hebt ingeschakeld, kunt u een regel voor het virtuele netwerk toevoegen om de Azure-service bronnen te beveiligen in uw virtuele netwerk.
- Tegenwoordig maakt Azure-serviceverkeer vanaf een virtueel netwerk gebruik van openbare IP-adressen als IP-bronadressen. Bij gebruik van service-eindpunten schakelt serviceverkeer over op het gebruik van privéadressen van virtuele netwerken als bron-IP-adressen bij het toegang krijgen tot de Azure-service vanuit een virtueel netwerk. Hierdoor hebt u toegang tot de services zonder dat u gereserveerde openbare IP-adressen nodig hebt die worden gebruikt in IP-firewalls.

   >[!NOTE]
   > Met service-eindpunten worden de IP-bronadressen van de virtuele machines in het subnet voor serviceverkeer overgeschakeld van het gebruik van openbare IPv4-adressen naar IPv4-privéadressen. Bestaande firewallregels voor Azure-service die gebruikmaken van openbare IP-adressen werken na deze overschakeling niet meer. Zorg ervoor dat deze overschakeling is toegestaan op basis van de firewallregels voor Azure-service vóórdat u de service-eindpunten instelt. Er kan ook een tijdelijke onderbreking in het serviceverkeer van dit subnet optreden tijdens het configureren van de service-eindpunten. 
 
## <a name="secure-azure-service-access-from-on-premises"></a>Toegang tot Azure-service vanaf on-premises beveiligen

  Standaard zijn Azure-service resources die zijn beveiligd met virtuele netwerken, niet bereikbaar vanuit on-premises netwerken. Als u verkeer van on-premises wilt toestaan, moet u ook open bare (doorgaans NAT) IP-adressen van uw on-premises of ExpressRoute toestaan. U kunt deze IP-adressen toevoegen via de IP-firewall configuratie voor Azure-service resources.

  ExpressRoute: als u [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) gebruikt voor open bare peering of micro soft-peering vanuit uw locatie, moet u de NAT IP-adressen identificeren die u gebruikt. Voor open bare peering gebruikt elk ExpressRoute-circuit twee NAT IP-adressen, die standaard worden toegepast op Azure-service verkeer wanneer het verkeer de Microsoft Azure-netwerk-backbone binnenkomt. Voor micro soft-peering zijn de NAT IP-adressen ofwel door de klant of door de service provider verschaft. Voor toegang tot uw serviceresources moet u deze openbare IP-adressen toestaan in de instelling voor IP-firewall voor de resource. Wanneer u op zoek bent naar de IP-adressen van uw ExpressRoute-circuit voor openbare peering, opent u [een ondersteuningsticket met ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) via de Azure-portal. Zie [EXPRESSROUTE NAT-vereisten](../expressroute/expressroute-nat.md?toc=%2fazure%2fvirtual-network%2ftoc.json#nat-requirements-for-azure-public-peering)voor meer informatie over NAT voor ExpressRoute open bare en micro soft-peering.

![Azure-services aan virtuele netwerken koppelen](./media/virtual-network-service-endpoints-overview/VNet_Service_Endpoints_Overview.png)

### <a name="configuration"></a>Configuratie

- Configureer service-eind punten in een subnet in een virtueel netwerk. Eindpunten werken met alle soorten rekenprocessen die worden uitgevoerd in dat subnet.
- U kunt meerdere service-eind punten voor alle ondersteunde Azure-Services (bijvoorbeeld Azure Storage of Azure SQL Database) voor een subnet configureren.
- Voor Azure SQL Database moeten virtuele netwerken zich in dezelfde regio bevinden als de Azure-serviceresource. Als u Azure Storage-accounts voor GRS en RA-GRS gebruikt, moet het hoofdaccount zich in dezelfde regio bevinden als het virtuele netwerk. Voor alle andere services kunt u Azure-service resources in een wille keurige regio beveiligen met virtuele netwerken. 
- Het virtuele netwerk waar het eindpunt is geconfigureerd, kan zich in hetzelfde abonnement bevinden als de Azure-serviceresource, maar ook in een ander abonnement. Zie [Inrichten](#provisioning) voor meer informatie over de benodigde machtigingen voor het instellen van eindpunten en het koppelen van Azure-services.
- Voor ondersteunde services kunt u nieuwe of bestaande resources koppelen aan virtuele netwerken met behulp van service-eindpunten.

### <a name="considerations"></a>Overwegingen

- Nadat een service-eind punt is ingeschakeld, scha kelen de bron-IP-adressen van het gebruik van open bare IPv4-adressen met behulp van hun persoonlijke IPv4-adres bij het communiceren met de service vanuit dat subnet. Bestaande open TCP-verbindingen met de service worden hierbij gesloten. Zorg ervoor dat er geen kritieke taken worden uitgevoerd wanneer u een service-eindpunt voor een service voor een subnet in- of uitschakelt. Zorg er ook voor dat uw toepassingen automatisch verbinding kunnen maken met Azure-services nadat de wisseling van IP-adres heeft plaatsgevonden.

  Het wisselen van IP-adres heeft alleen gevolgen voor het serviceverkeer vanuit uw virtuele netwerk. Er is geen invloed op enig ander verkeer dat is geadresseerd aan of van de open bare IPv4-adressen die zijn toegewezen aan uw virtuele machines. Als u voor Azure-services bestaande firewallregels hebt waarin gebruik wordt gemaakt van openbare IP-adressen voor Azure, werken deze regels niet meer nadat is overgeschakeld op privéadressen van virtuele netwerken.
- Met Service-eind punten blijven DNS-vermeldingen voor Azure-Services vandaag nog aanwezig en gaan we verder met het omzetten van open bare IP-adressen die zijn toegewezen aan de Azure-service.

- Netwerkbeveiligingsgroepen (NSG's) met de service-eindpunten:
  - Nsg's toestaan standaard uitgaand Internet verkeer en verkeer van uw VNet naar Azure-Services toe. Dit verkeer blijft werken met Service-eind punten. 
  - Als u al het uitgaande Internet verkeer wilt weigeren en alleen verkeer naar specifieke Azure-Services wilt toestaan, kunt u dit doen met behulp van [service Tags](security-overview.md#service-tags) in uw nsg's. U kunt ondersteunde Azure-Services als doel opgeven in uw NSG-regels en Azure biedt ook het onderhoud van de IP-adressen die onder elke tag liggen. Zie [Azure-servicelabels voor NSG's](security-overview.md#service-tags) voor meer informatie. 

### <a name="scenarios"></a>Scenario's

- **Virtuele netwerken met peers, verbonden of meerdere virtuele netwerken**: als u Azure-services wilt koppelen aan meerdere subnetten binnen een virtueel netwerk of aan meerdere virtuele netwerken, kunt u in elk van de subnetten service-eindpunten inschakelen en de Azure-serviceresources vervolgens aan al deze subnetten koppelen.
- **Uitgaand verkeer van een virtueel netwerk naar Azure-Services filteren**: als u het verkeer dat vanuit een virtueel netwerk naar een Azure-service wilt verzenden, wilt controleren of filteren, kunt u een virtueel netwerk apparaat in het virtuele netwerk implementeren. U kunt dan service-eindpunten toepassen op het subnet waarop het virtueel-netwerkapparaat is geïmplementeerd en Azure-serviceresources alleen koppelen aan dit subnet. Dit scenario kan nuttig zijn als u virtuele netwerk apparaten wilt filteren om de toegang tot Azure-Services vanuit uw virtuele netwerk alleen te beperken tot specifieke Azure-resources. Zie [Egress with network virtual appliances](/azure/architecture/reference-architectures/dmz/nva-ha) (Uitgaand verkeer met virtueel-netwerkapparaten) voor meer informatie.
- **Azure-resources beveiligen tot services die rechtstreeks in virtuele netwerken zijn geïmplementeerd**: u kunt verschillende Azure-Services rechtstreeks implementeren in specifieke subnetten in een virtueel netwerk. U kunt Azure-serviceresources koppelen aan subnetten voor [beheerde services](virtual-network-for-azure-services.md) door een service-eindpunt in te stellen in het subnet van deze beheerde services.
- **Schijf verkeer van een virtuele Azure-machine: het** schijf verkeer van de virtuele machine voor beheerde en onbeheerde schijven wordt niet beïnvloed door service-eind punten routerings wijzigingen voor Azure Storage. Dit verkeer omvat diskIO, en koppelen en ontkoppelen. U kunt de REST toegang tot pagina-blobs beperken om netwerken te selecteren via service-eind punten en [Azure storage netwerk regels](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 

### <a name="logging-and-troubleshooting"></a>Logboekregistratie en problemen oplossen

Wanneer u service-eind punten hebt geconfigureerd voor een specifieke service, controleert u of de service-eindpunt route van kracht is door: 
 
- Valideer het IP-bronadres van een serviceaanvraag in servicediagnose. In alle nieuwe aanvragen met service-eindpunten wordt het IP-bronadres voor de aanvraag weergegeven als het privéadres van het virtuele netwerk, toegewezen aan de client die de aanvraag vanuit uw virtuele netwerk uitvoert. Zonder het eindpunt is het adres een openbaar IP-adres van Azure.
- Het weergeven van de effectieve routes in een netwerkinterface in een subnet. De route naar de service:
  - Toont een meer specifieke standaardroute naar de adresvoorvoegselbereiken van elke service
  - Heeft een nextHopType van *VirtualNetworkServiceEndpoint*
  - Geeft aan dat een meer rechtstreekse verbinding met de service wordt toegepast in vergelijking met alle routes voor geforceerde tunneling

>[!NOTE]
> Service-eindpuntroute overschrijft BGP- of UDR-routes voor de overeenkomst met het adresvoorvoegsel van een Azure-service. Zie [problemen met efficiënte routes oplossen](diagnose-network-routing-problem.md)voor meer informatie.

## <a name="provisioning"></a>Inrichten

Service-eind punten kunnen worden geconfigureerd op virtuele netwerken, onafhankelijk van een gebruiker met schrijf toegang tot een virtueel netwerk. Voor het beveiligen van Azure-service resources naar een VNet moet de gebruiker gemachtigd zijn voor *micro soft. Network/virtualNetworks/subnets/joinViaServiceEndpoint/Action* voor de toegevoegde subnetten. De ingebouwde rollen van de service beheerder bevatten standaard deze machtiging. U kunt de machtiging wijzigen door aangepaste rollen te maken.

Zie [ingebouwde rollen van Azure](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)voor meer informatie over ingebouwde rollen. Zie [aangepaste rollen voor Azure](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)voor meer informatie over het toewijzen van specifieke machtigingen aan aangepaste rollen.

Virtuele netwerken en Azure-serviceresources kunnen binnen hetzelfde abonnement of in verschillende abonnementen aanwezig zijn. Als de virtuele netwerken en Azure-serviceresources in verschillende abonnementen aanwezig zijn, moeten de resources onder dezelfde Active Directory-tenant (AD) vallen. 

## <a name="pricing-and-limits"></a>Prijzen en beperkingen

Er worden geen extra kosten in rekening gebracht voor het gebruik van service-eind punten. Het huidige prijs model voor Azure-Services (Azure Storage, Azure SQL Database, enz.) is vandaag van toepassing.

Er is geen limiet voor het totale aantal service-eind punten in een virtueel netwerk.

Bepaalde Azure-Services, zoals Azure Storage accounts, kunnen Limieten afdwingen voor het aantal subnetten dat wordt gebruikt voor het beveiligen van de bron. Raadpleeg de documentatie voor verschillende services in het gedeelte [volgende stappen](#next-steps) voor meer informatie.

## <a name="vnet-service-endpoint-policies"></a>Beleid voor VNet-service-eind punten 

Met het beleid voor de VNet-service-eind punten kunt u virtueel netwerk verkeer filteren op Azure-Services. Met dit filter worden alleen specifieke Azure-service resources via service-eind punten toegestaan. Beleid voor service-eindpunten zorgt voor nauwkeurig toegangsbeheer voor verkeer van Virtual Network naar Azure-services. Zie [Virtual Networking service Endpoint policies](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-overview)(Engelstalig) voor meer informatie.

## <a name="faqs"></a>Veelgestelde vragen

Zie [Veelgestelde vragen over Virtual Network Service-eind punten](https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq#virtual-network-service-endpoints)voor veelgestelde vragen.

## <a name="next-steps"></a>Volgende stappen

- [Service-eindpunten voor virtuele netwerken configureren](tutorial-restrict-network-access-to-resources.md)
- [Een Azure Storage-account beveiligen met een virtueel netwerk](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Een Azure SQL Database beveiligen met een virtueel netwerk](../azure-sql/database/vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Een Azure Synapse-analyse beveiligen met een virtueel netwerk](../azure-sql/database/vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fsql-data-warehouse%2ftoc.json)
- [Integratie van Azure-Services in virtuele netwerken](virtual-network-for-azure-services.md)
- [Virtual Network service-eindpunt beleid](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-overview)
- [Azure Resource Manager-sjabloon](https://azure.microsoft.com/resources/templates/201-vnet-2subnets-service-endpoints-storage-integration)
