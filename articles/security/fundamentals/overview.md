---
title: Inleiding tot Azure-beveiliging | Microsoft Docs
description: Kennis nemen met Azure-beveiliging, de verschillende services en hoe deze werkt door dit overzicht te lezen.
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/03/2021
ms.author: TomSh
ms.openlocfilehash: b5f9df4e6f682b5d1e9e3cd35affe6e4191e3d53
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2021
ms.locfileid: "105047776"
---
# <a name="introduction-to-azure-security"></a>Inleiding tot Azure-beveiliging

## <a name="overview"></a>Overzicht

We weten dat de veiligheid taak in de Cloud is en hoe belang rijk het is dat u nauw keurige en tijdige informatie over Azure-beveiliging vindt. Een van de beste redenen om Azure te gebruiken voor uw toepassingen en services is om te profiteren van de uitgebreide reeks beveiligings Programma's en-mogelijkheden. Met deze hulpprogram ma's en mogelijkheden kunt u beveiligde oplossingen maken op het beveiligde Azure-platform. Microsoft Azure biedt vertrouwelijkheid, integriteit en beschik baarheid van klant gegevens, terwijl ook transparante verantwoording wordt ingeschakeld.

In dit artikel vindt u een uitgebreid overzicht van de beveiliging die beschikbaar is met Azure.

### <a name="azure-platform"></a>Azure-platform

Azure is een openbaar Cloud service platform dat ondersteuning biedt voor een groot aantal besturings systemen, programmeer talen, frameworks, hulpprogram ma's, data bases en apparaten. Het kan Linux-containers uitvoeren met docker-integratie; bouw apps met Java script, Python, .NET, PHP, Java en Node.js; Maak back-ends voor iOS-, Android-en Windows-apparaten.

Open bare Cloud Services van Azure bieden ondersteuning voor miljoenen ontwikkel aars en IT-professionals die al gebruikmaken van en vertrouwen. Wanneer u de IT-activa bouwt op of migreert naar, een open bare Cloud serviceprovider die u vertrouwt over de mogelijkheden van die organisatie om uw toepassingen en gegevens te beschermen met de services en de besturings elementen die ze bieden om de beveiliging van uw cloud-gebaseerde assets te beheren.

De infra structuur van Azure is ontworpen met het oog op toepassingen om miljoenen klanten tegelijkertijd te hosten, en biedt een betrouw bare basis waarover bedrijven kunnen voldoen aan de beveiligings vereisten.

Bovendien biedt Azure u een breed scala aan Configureer bare beveiligings opties en de mogelijkheid om deze te beheren, zodat u de beveiliging kunt aanpassen om te voldoen aan de unieke vereisten van de implementaties van uw organisatie. In dit document wordt uitgelegd hoe u de beveiligings mogelijkheden van Azure kunt gebruiken om aan deze vereisten te voldoen.

> [!Note]
> De primaire focus van dit document is op klant gerichte besturings elementen die u kunt gebruiken om de beveiliging van uw toepassingen en services aan te passen en te verbeteren.
>
> Zie [Azure Infrastructure Security](infrastructure.md)(Engelstalig) voor meer informatie over hoe micro soft het Azure-platform zelf beveiligt.

## <a name="summary-of-azure-security-capabilities"></a>Overzicht van Azure-beveiligings mogelijkheden

Afhankelijk van het Cloud service model, is er een variabele verantwoordelijkheid voor wie verantwoordelijk is voor het beheren van de beveiliging van de toepassing of service. Er zijn mogelijkheden beschikbaar in het Azure-platform om u te helpen bij het beantwoorden van deze verantwoordelijkheden via ingebouwde functies en via partner oplossingen die kunnen worden geïmplementeerd in een Azure-abonnement.

De ingebouwde mogelijkheden zijn ingedeeld in zes functionele gebieden: bewerkingen, toepassingen, opslag, netwerken, compute en identiteit. Meer details over de functies en mogelijkheden die beschikbaar zijn in het Azure-platform in deze zes gebieden worden verstrekt via samenvattings informatie.

## <a name="operations"></a>Operations

Deze sectie bevat aanvullende informatie over belang rijke functies in beveiligings bewerkingen en overzichts informatie over deze mogelijkheden.

### <a name="azure-security-center"></a>Azure Security Center

[Security Center](../../security-center/security-center-introduction.md) helpt u bedreigingen te voor komen, te detecteren en erop te reageren met verbeterde zicht baarheid en controle over de beveiliging van uw Azure-resources. Het biedt geïntegreerde beveiligingsbewaking en beleidsbeheer voor uw Azure-abonnementen, helpt bedreigingen te detecteren die anders onopgemerkt zouden blijven, en werkt met een uitgebreid ecosysteem van beveiligingsoplossingen.

Daarnaast helpt Security Center bij het uitvoeren van beveiligings bewerkingen door u een dash board te bieden dat waarschuwingen en aanbevelingen ondervindt die direct kunnen worden verwerkt. Vaak kunt u problemen oplossen met één klik binnen de Security Center-console.

### <a name="azure-resource-manager"></a>Azure Resource Manager

[Azure Resource Manager](../../azure-resource-manager/management/overview.md) kunt u met de resources in uw oplossing als groep gebruiken. U kunt alle resources voor uw oplossing implementeren, bijwerken of verwijderen in een enkele, gecoördineerde bewerking. U gebruikt een [Azure Resource Manager-sjabloon](../../azure-resource-manager/templates/overview.md) voor de implementatie en die sjabloon kan werken voor verschillende omgevingen, zoals testen, faseren en productie. Resource Manager biedt beveiliging, controle en tagfuncties die u na de implementatie helpen bij het beheren van uw resources.

Azure Resource Manager implementaties op basis van een sjabloon helpen bij het verbeteren van de beveiliging van oplossingen die zijn geïmplementeerd in azure, omdat standaard instellingen voor beveiligings beheer en kunnen worden geïntegreerd in gestandaardiseerde implementaties op basis van een sjabloon. Dit vermindert het risico van beveiligings configuratie fouten die kunnen optreden tijdens hand matige implementaties.

### <a name="application-insights"></a>Application Insights

[Application Insights](../../azure-monitor/app/app-insights-overview.md) is een UITBREID bare apm-service (Application Performance Management) voor webontwikkelaars. Met Application Insights kunt u uw Live webtoepassingen bewaken en automatisch prestatie afwijkingen detecteren. Het bevat krachtige analyse hulpprogramma's waarmee u problemen kunt vaststellen en inzicht kunt krijgen wat gebruikers daad werkelijk doen met uw apps. Hiermee wordt uw toepassing gecontroleerd op het moment dat deze wordt uitgevoerd, zowel tijdens het testen als nadat u deze hebt gepubliceerd of geïmplementeerd.

Application Insights maakt grafieken en tabellen waarin u bijvoorbeeld kunt zien op welke tijdstippen u de meeste gebruikers krijgt, hoe reageert de app en hoe goed deze wordt bediend door externe services waarvan deze afhankelijk is.

Als er zich crashes, fouten of prestatie problemen voordoen, kunt u de gegevens in de telemetrie gedetailleerd doorzoeken om de oorzaak vast te stellen. En de service stuurt u e-mail berichten als er wijzigingen zijn in de beschik baarheid en prestaties van uw app. Toepassings inzicht wordt daarom een waardevol beveiligings programma, omdat het helpt bij de beschik baarheid van de beveiligings Triad van de vertrouwelijkheid, integriteit en beschik baarheid.

### <a name="azure-monitor"></a>Azure Monitor

[Azure monitor](/azure/monitoring-and-diagnostics/) biedt visualisatie, query, route ring, waarschuwingen, automatisch schalen en automatisering op gegevens van het Azure-abonnement ([activiteiten logboek](../../azure-monitor/essentials/platform-logs-overview.md)) en elke afzonderlijke Azure-resource ([resource logboeken](../../azure-monitor/essentials/platform-logs-overview.md)). U kunt Azure Monitor gebruiken om u te waarschuwen voor beveiligings gebeurtenissen die worden gegenereerd in azure-Logboeken.

### <a name="azure-monitor-logs"></a>Azure Monitor-logboeken

[Azure monitor-logboeken](../../azure-monitor/logs/log-query-overview.md) : voorziet in een IT-beheer oplossing voor zowel on-premises als Cloud infrastructuur op basis van derden (zoals AWS) naast Azure-resources. Gegevens van Azure Monitor kunnen rechtstreeks naar Azure Monitor-logboeken worden gerouteerd, zodat u meet waarden en logboeken voor uw hele omgeving op één plek kunt zien.

Azure Monitor logboeken kunnen een handig hulp middel zijn in forensische en een andere beveiligings analyse, omdat u met het hulp programma snel grote hoeveel heden aan beveiligings gegevens kunt doorzoeken met een flexibele query benadering. Daarnaast kunnen on-premises [firewall-en proxy Logboeken in Azure worden geëxporteerd en beschikbaar worden gesteld voor analyse met behulp van Azure monitor-Logboeken.](../../azure-monitor/agents/agent-windows.md)

### <a name="azure-advisor"></a>Azure Advisor

[Azure Advisor](../../advisor/advisor-overview.md) is een gepersonaliseerde Cloud consultant waarmee u uw Azure-implementaties kunt optimaliseren. Advisor analyseert de configuratie van uw resources en de gebruiksgerelateerde telemetrie. Vervolgens worden oplossingen aanbevolen om de [prestaties](../../advisor/advisor-performance-recommendations.md), [beveiliging](../../advisor/advisor-security-recommendations.md)en [betrouw baarheid](../../advisor/advisor-high-availability-recommendations.md) van uw resources te verbeteren terwijl u op zoek bent naar mogelijkheden om [uw totale Azure-uitgaven te verminderen](../../advisor/advisor-cost-recommendations.md). Azure Advisor biedt beveiligings aanbevelingen, waarmee u uw algemene beveiligings postuur aanzienlijk kunt verbeteren voor oplossingen die u in azure implementeert. Deze aanbevelingen worden getrokken van de beveiligings analyse die wordt uitgevoerd door [Azure Security Center.](../../security-center/security-center-introduction.md)

## <a name="applications"></a>Toepassingen

De sectie bevat aanvullende informatie over de belangrijkste functies in toepassings beveiliging en samenvattings informatie over deze mogelijkheden.

### <a name="web-application-vulnerability-scanning"></a>Scan voor beveiligings problemen webtoepassing

Een van de eenvoudigste manieren om aan de slag te gaan met het testen op beveiligings problemen in uw [app service-app](../../app-service/overview.md) is het gebruik van de [integratie met Tinfoil-beveiliging](https://azure.microsoft.com/blog/web-vulnerability-scanning-for-azure-app-service-powered-by-tinfoil-security/) om met één klik een beveiligingslek in uw app te scannen. U kunt de test resultaten weer geven in een eenvoudig te begrijpen rapport en meer informatie over het oplossen van elk beveiligings probleem met stapsgewijze instructies.

### <a name="penetration-testing"></a>Penetratietesten

Er worden geen [indringings tests](./pen-testing.md) van uw toepassing voor u uitgevoerd, maar we begrijpen dat u dat wilt en moet testen uitvoeren op uw eigen toepassingen. Het is een goed idee, omdat wanneer u de beveiliging van uw toepassingen verbetert, u het volledige Azure-ecosysteem beter kunt beveiligen. Hoewel het niet meer nodig is dat micro soft op de hoogte wordt gebracht van de test activiteiten van de pen, moeten klanten nog steeds voldoen aan de [Microsoft Cloud Indringings test regels van betrokkenheid](https://www.microsoft.com/msrc/pentest-rules-of-engagement).

### <a name="web-application-firewall"></a>Web Application firewall

De Web Application Firewall (WAF) in [Azure-toepassing gateway](../../application-gateway/features.md#web-application-firewall) helpt bij het beveiligen van webtoepassingen tegen veelvoorkomende aanvallen op internet, zoals SQL-injectie, cross-site scripting aanvallen en het overnemen van sessies. Het is vooraf geconfigureerd met beveiliging tegen bedreigingen die zijn geïdentificeerd door het [Open Web Application Security project (OWASP) als de Top 10 van veelvoorkomende beveiligings problemen](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project).

### <a name="authentication-and-authorization-in-azure-app-service"></a>Verificatie en autorisatie in Azure App Service

[App service-verificatie/-autorisatie](../../app-service/overview-authentication-authorization.md) is een functie waarmee uw toepassing zich kan aanmelden, zodat u geen code hoeft te wijzigen op de back-end van de app. Het biedt een eenvoudige manier om uw toepassing te beveiligen en te werken met gegevens per gebruiker.

### <a name="layered-security-architecture"></a>Gelaagde beveiligings architectuur

Omdat [app service omgevingen](../../app-service/environment/app-service-app-service-environment-intro.md) een geïsoleerde runtime-omgeving bieden die is geïmplementeerd in een [Azure-Virtual Network](../../virtual-network/virtual-networks-overview.md), kunnen ontwikkel aars een gelaagde beveiligings architectuur maken die verschillende niveaus van netwerk toegang biedt voor elke toepassingslaag. Een gemeen schappelijke wens is de API-back-ends van algemene Internet toegang te verbergen en alleen Api's toe te staan door upstream-web-apps. [Netwerk beveiligings groepen (nsg's)](../../virtual-network/virtual-network-vnet-plan-design-arm.md) kunnen worden gebruikt in azure Virtual Network subnetten die app service omgevingen bevatten om de open bare toegang tot API-toepassingen te beperken.

### <a name="web-server-diagnostics-and-application-diagnostics"></a>Diagnostische gegevens van webservers en Application Diagnostics
[App service Web-apps](../../app-service/troubleshoot-diagnostic-logs.md) bieden diagnostische functionaliteit voor het vastleggen van gegevens van zowel de webserver als de webtoepassing. Deze worden logisch gescheiden in webserver diagnostiek en toepassings diagnoses. Webserver bevat twee belang rijke voor uitgang bij het opsporen en oplossen van problemen met sites en toepassingen.

De eerste nieuwe functie is realtime-status informatie over groepen van toepassingen, werk processen, sites, toepassings domeinen en actieve aanvragen. De tweede nieuwe voor delen zijn de gedetailleerde tracerings gebeurtenissen die een aanvraag volgen tijdens het volledige proces van aanvragen en antwoorden.

Om het verzamelen van deze tracerings gebeurtenissen in te scha kelen, kan IIS 7 worden geconfigureerd voor het automatisch vastleggen van volledige traceer Logboeken in XML-indeling, voor een bepaalde aanvraag op basis van de verstreken tijd of de code voor fout reacties.

## <a name="storage"></a>Storage
De sectie bevat aanvullende informatie over de belangrijkste functies in azure Storage-beveiliging en samenvattings informatie over deze mogelijkheden.

### <a name="azure-role-based-access-control-azure-rbac"></a>Azure RBAC (op rollen gebaseerd toegangsbeheer van Azure)
U kunt uw opslag account beveiligen met [Azure op rollen gebaseerd toegangs beheer (Azure RBAC)](../../role-based-access-control/overview.md). Het beperken van de toegang op basis van de beveiligings principes van de [nood zaak om te kennen](https://en.wikipedia.org/wiki/Need_to_know) , is van cruciaal belang voor organisaties [die beveiligings beleid](https://en.wikipedia.org/wiki/Principle_of_least_privilege) voor gegevens toegang willen afdwingen. Deze toegangs rechten worden verleend door de juiste Azure-rol toe te wijzen aan groepen en toepassingen bij een bepaald bereik. U kunt [ingebouwde rollen van Azure](../../role-based-access-control/built-in-roles.md), zoals Inzender voor opslag accounts, gebruiken om machtigingen toe te wijzen aan gebruikers. Toegang tot de opslag sleutels voor een opslag account met behulp van het [Azure Resource Manager](../../storage/blobs/security-recommendations.md#data-protection) model kan worden beheerd via Azure RBAC.

### <a name="shared-access-signature"></a>Shared Access Signature
Een [Shared Access Signature (SAS)](../../storage/common/storage-sas-overview.md) biedt gedelegeerde toegang tot resources in uw opslag account. De SAS betekent dat u een client beperkte machtigingen kunt verlenen voor objecten in uw opslag account voor een opgegeven periode en met een opgegeven set machtigingen. U kunt deze beperkte machtigingen verlenen zonder dat u de toegangs sleutels van uw account hoeft te delen.

### <a name="encryption-in-transit"></a>Versleuteling in transit
Versleuteling in transit is een mechanisme voor het beveiligen van gegevens wanneer deze via netwerken worden verzonden. Met Azure Storage kunt u gegevens beveiligen met behulp van:
- [Versleuteling op transport niveau](../../storage/blobs/security-recommendations.md), zoals https wanneer u gegevens overbrengt naar of van Azure Storage.

- [Wire-versleuteling](../../storage/blobs/security-recommendations.md), zoals [SMB 3,0-versleuteling](../../storage/blobs/security-recommendations.md) voor [Azure-bestands shares](../../storage/files/storage-dotnet-how-to-use-files.md).

- Versleuteling aan de client zijde, voor het versleutelen van de gegevens voordat deze naar de opslag wordt overgebracht en voor het ontsleutelen van de gegevens nadat deze buiten de opslag zijn overgedragen.

### <a name="encryption-at-rest"></a>Versleuteling 'at rest'
Voor veel organisaties is gegevens versleuteling in rust een verplichte stap op het vlak van gegevens privacy, naleving en data soevereiniteit. Er zijn drie Azure Storage-beveiligings functies die gegevens versleuteling bieden die ' op rust ' zijn:

- Met [Storage service Encryption](../../storage/common/storage-service-encryption.md) kunt u aanvragen dat de opslag service automatisch gegevens versleutelt bij het schrijven naar Azure Storage.

- [Versleuteling aan de client zijde](../../storage/common/storage-client-side-encryption.md) biedt ook de mogelijkheid om op rest te versleutelen.

- Met [Azure Disk Encryption](./azure-disk-encryption-vms-vmss.md) kunt u de stations van het besturings systeem en de gegevens schijven die worden gebruikt door een virtuele machine van IaaS versleutelen.

### <a name="storage-analytics"></a>Storage Analytics

[Azure Opslaganalyse](/rest/api/storageservices/fileservices/storage-analytics) voert logboek registratie uit en geeft metrische gegevens voor een opslag account. U kunt deze gegevens gebruiken om aanvragen te traceren, gebruiks trends te analyseren en problemen met uw opslag account op te sporen. Opslaganalyse registreert gedetailleerde informatie over geslaagde en mislukte aanvragen bij een opslagservice. Deze informatie kan worden gebruikt voor het bewaken van afzonderlijke aanvragen en voor het vaststellen van problemen met een opslagservice. Aanvragen worden op de beste basis geregistreerd. De volgende typen geverifieerde aanvragen worden geregistreerd:

- Geslaagde aanvragen.
- Mislukte aanvragen, inclusief time-out, beperking, netwerk, autorisatie en andere fouten.
- Aanvragen met behulp van een Shared Access Signature (SAS), waaronder mislukte en geslaagde aanvragen.
- Aanvragen voor Analytics-gegevens.

### <a name="enabling-browser-based-clients-using-cors"></a>Browser-Based-clients met CORS inschakelen

[Cross-Origin Resource Sharing (CORS)](/rest/api/storageservices/fileservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) is een mechanisme waarmee domeinen elke andere machtiging kunnen geven om toegang te krijgen tot de resources van elkaar. De gebruikers agent verzendt extra headers om ervoor te zorgen dat de Java script-code die is geladen vanuit een bepaald domein, toegang heeft tot bronnen die zich in een ander domein bevinden. Het laatste domein beantwoordt vervolgens met extra headers die de oorspronkelijke toegang tot de bronnen van het domein toestaan of weigeren.

Azure Storage-services ondersteunen nu CORS zodat als u de CORS-regels voor de service hebt ingesteld, een correct geverifieerde aanvraag voor de service vanuit een ander domein wordt geëvalueerd om te bepalen of deze is toegestaan volgens de regels die u hebt opgegeven.

## <a name="networking"></a>Netwerken

De sectie bevat aanvullende informatie over de belangrijkste functies in azure-netwerk beveiliging en samenvattings informatie over deze mogelijkheden.

### <a name="network-layer-controls"></a>Besturings elementen voor netwerklaag

Netwerk toegangs beheer is de handeling van het beperken van de connectiviteit met en van specifieke apparaten of subnetten en vertegenwoordigt de kern van netwerk beveiliging. Het doel van netwerk toegangs beheer is om ervoor te zorgen dat uw virtuele machines en services alleen toegankelijk zijn voor gebruikers en apparaten waartoe u ze toegang wilt geven.

#### <a name="network-security-groups"></a>Netwerkbeveiligingsgroepen

Een [netwerk beveiligings groep (NSG)](../../virtual-network/virtual-network-vnet-plan-design-arm.md#security) is een elementaire stateful pakket filtering firewall waarmee u de toegang kunt beheren op basis van een 5-tuple. Nsg's bieden geen inspectie van toepassings lagen of geverifieerde toegangs beheer. Ze kunnen worden gebruikt om verkeer tussen subnetten binnen een Azure-Virtual Network en verkeer tussen een Azure Virtual Network en Internet te beheren.

#### <a name="route-control-and-forced-tunneling"></a>Route beheer en geforceerde tunneling

De mogelijkheid om routerings gedrag voor uw virtuele Azure-netwerken te beheren, is een essentiële mogelijkheden voor netwerk beveiliging en toegangs beheer. Als u bijvoorbeeld wilt dat al het verkeer naar en van de Azure-Virtual Network het virtuele beveiligings apparaat doorloopt, moet u het gedrag van route ring kunnen bepalen en aanpassen. U kunt dit doen door User-Defined routes te configureren in Azure.

Door de [gebruiker gedefinieerde routes](../../virtual-network/virtual-networks-udr-overview.md#custom-routes) bieden u de mogelijkheid om binnenkomende en uitgaande paden aan te passen voor verkeer dat wordt verplaatst naar en van afzonderlijke virtuele machines of subnetten om de veiligste route te garanderen. [Geforceerde tunneling](../../vpn-gateway/vpn-gateway-forced-tunneling-rm.md) is een mechanisme dat u kunt gebruiken om ervoor te zorgen dat uw services geen verbinding met apparaten op Internet mogen initiëren.

Dit wijkt af van de mogelijkheid om binnenkomende verbindingen te accepteren en vervolgens te reageren. Front-end-webservers moeten reageren op aanvragen van Internet hosts, waardoor het Internet verkeer naar deze webservers wordt toegestaan en de webservers kunnen reageren.

Geforceerde tunneling wordt doorgaans gebruikt om uitgaand verkeer naar Internet af te dwingen via on-premises beveiligings proxy's en firewalls.

#### <a name="virtual-network-security-appliances"></a>Beveiligings apparaten Virtual Network

Hoewel netwerk beveiligings groepen, User-Defined routes en geforceerde tunneling u een beveiligings niveau bieden op het netwerk en de transport lagen van het [OSI-model](https://en.wikipedia.org/wiki/OSI_model), is het mogelijk dat u de beveiliging op een hoger niveau van de stack wilt inschakelen. U kunt deze verbeterde beveiligings functies van het netwerk openen met behulp van een Azure-partner netwerk beveiligings apparaat. U kunt de meest recente oplossingen voor Azure-partner netwerk beveiliging vinden door de [Azure Marketplace](https://azure.microsoft.com/marketplace/) te bezoeken en te zoeken naar ' Beveiliging ' en ' netwerk beveiliging '.

### <a name="azure-virtual-network"></a>Azure Virtual Network

Een virtueel Azure-netwerk (VNET) is een weergave van uw eigen netwerk in de cloud. Het is een logische isolatie van de Azure Network-infra structuur die is toegewezen aan uw abonnement. U kunt de IP-adresblokken, DNS-instellingen, beveiligingsbeleidsregels en routetabellen binnen dit netwerk volledig beheren. U kunt uw VNet in subnetten segmenteren en Azure IaaS virtuele machines (Vm's) en/of [Cloud Services (PaaS-rolinstanties)](../../cloud-services/cloud-services-choose-me.md) plaatsen in azure Virtual Networks.

Hiermee kunt u het virtuele netwerk via een van de beschikbare [verbindingsopties](../../vpn-gateway/index.yml) in Azure verbinden met uw on-premises netwerk. In wezen kunt u uw netwerk uitbreiden naar Azure met behoud van de volledige controle over IP-adresblokken en de schaalvoordelen van Azure voor ondernemingen.

Azure-netwerken biedt ondersteuning voor verschillende scenario's voor beveiligde externe toegang. Dit zijn onder andere:

- [Afzonderlijke werk stations verbinden met een Azure-Virtual Network](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

- [On-premises netwerk verbinden met een Azure-Virtual Network met een VPN](../../vpn-gateway/vpn-gateway-about-vpngateways.md)

- [On-premises netwerk verbinden met een Azure-Virtual Network met een specifieke WAN-koppeling](../../expressroute/expressroute-introduction.md)

- [Virtuele Azure-netwerken met elkaar verbinden](../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

### <a name="azure-private-link"></a>Azure Private Link

Met [Azure private link](https://azure.microsoft.com/services/private-link/) kunt u toegang krijgen tot Azure PaaS-Services (bijvoorbeeld Azure Storage en SQL database) en Azure gehoste klant-eigendom/partner services privé in uw virtuele netwerk via een [persoonlijk eind punt](../../private-link/private-endpoint-overview.md). Het instellen en gebruiken van Azure Private Link is consistent voor Azure PaaS-services, services die eigendom zijn van klanten en gedeelde partnerservices. Verkeer van uw virtuele netwerk naar de Azure-service blijft altijd op het Microsoft Azure backbone-netwerk.

Met [persoonlijke eind punten](../../private-link/private-endpoint-overview.md) kunt u uw kritieke Azure-service resources alleen op uw virtuele netwerken beveiligen. Azure private endpoint maakt gebruik van een privé-IP-adres van uw VNet om u privé en veilig te verbinden met een service die wordt aangestuurd door een persoonlijke Azure-koppeling, waardoor de service in uw VNet effectief wordt. Het beschikbaar maken van uw virtuele netwerk op het open bare Internet is niet langer nodig voor het gebruik van services in Azure. 

U kunt ook uw eigen persoonlijke koppelings service in uw virtuele netwerk maken. [Persoonlijke koppelings service van Azure](../../private-link/private-link-service-overview.md) is de verwijzing naar uw eigen service die wordt aangestuurd door een persoonlijke Azure-koppeling. De service die wordt uitgevoerd achter Azure Standard Load Balancer kan worden ingeschakeld voor toegang tot persoonlijke koppelingen, zodat gebruikers die toegang hebben tot uw service, privé kunnen zijn vanuit hun eigen virtuele netwerken. Uw klanten kunnen een persoonlijk eind punt in hun virtuele netwerk maken en dit aan deze service toewijzen. Het beschikbaar maken van uw service voor het open bare Internet is niet langer nodig voor het weer geven van services in Azure. 

### <a name="vpn-gateway"></a>VPN Gateway

Als u netwerk verkeer wilt verzenden tussen uw Azure Virtual Network en uw on-premises site, moet u een VPN-gateway maken voor uw Azure-Virtual Network. Een [VPN-gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md) is een type virtuele netwerk gateway die versleuteld verkeer verzendt via een open bare verbinding. U kunt VPN-gateways ook gebruiken om verkeer tussen virtuele Azure-netwerken te verzenden via de Azure-netwerk infrastructuur.

### <a name="express-route"></a>ExpressRoute

Microsoft Azure [ExpressRoute](../../expressroute/expressroute-introduction.md) is een specifieke WAN-verbinding waarmee u uw on-premises netwerken in de micro soft-Cloud kunt uitbreiden via een speciale persoonlijke verbinding die wordt vereenvoudigd door een connectiviteits provider.

![ExpressRoute](./media/overview/azure-security-figure-1.png)

Met ExpressRoute kunt u verbindingen tot stand brengen met micro soft-Cloud Services, zoals Microsoft Azure, Microsoft 365 en CRM Online. Via een connectiviteitsprovider in een co-locatiefaciliteit is connectiviteit mogelijk vanuit een any-to-any (IP VPN) netwerk, een point-to-point Ethernet-netwerk of een virtuele overlappende verbinding.

ExpressRoute-verbindingen gaan niet via het open bare Internet en kunnen worden beschouwd als veiliger dan op VPN gebaseerde oplossingen. Daardoor zijn ExpressRoute-verbindingen betrouwbaarder en sneller en hebben ze lagere latenties en betere beveiliging dan gewone verbindingen via internet.

### <a name="application-gateway"></a>Application Gateway

Micro soft [Azure-toepassing gateway](../../application-gateway/overview.md) biedt een [ADC (Application Delivery controller)](https://en.wikipedia.org/wiki/Application_delivery_controller) als een service en biedt verschillende mogelijkheden voor de taak verdeling van laag 7 voor uw toepassing.

![Application Gateway](./media/overview/azure-security-figure-2.png)

Hiermee kunt u de productiviteit van webfarms optimaliseren door de CPU-intensieve TLS-beëindiging te offloaden naar de Application Gateway (ook wel ' TLS-offload ' of ' TLS-bridging ' genoemd). Het biedt ook andere Layer 7-routerings mogelijkheden, waaronder round-robin distributie van binnenkomend verkeer, sessie affiniteit op basis van cookies, op URL gebaseerde route ring en de mogelijkheid om meerdere websites achter één Application Gateway te hosten. Azure Application Gateway is een load balancer in laag 7.

De gateway biedt opties voor failovers en het routeren van HTTP-aanvragen tussen servers (on-premises en in de cloud).

De toepassing biedt veel ADC-functies (Application Delivery controller), waaronder HTTP-taak verdeling, sessie affiniteit op basis van cookies, [TLS-offload](../../web-application-firewall/ag/tutorial-restrict-web-traffic-powershell.md), aangepaste status tests, ondersteuning voor meerdere locaties en vele andere.

### <a name="web-application-firewall"></a>Web Application Firewall

Web Application firewall is een functie van [Azure-toepassing gateway](../../application-gateway/overview.md) die beveiliging biedt voor webtoepassingen die gebruikmaken van Application Gateway voor standaard ADC-functies (Application Delivery Control). Web Application Firewall doet dit door deze te beschermen tegen het grootste deel van de algemene internetbeveiligingsproblemen uit de OWASP top 10.

![Web Application Firewall](./media/overview/azure-security-figure-3.png)

- Beveiliging tegen SQL-injecties

- Beveiliging tegen veelvoorkomende aanvallen via internet, zoals opdrachtinjectie, het smokkelen van HTTP-aanvragen, het uitsplitsen van HTTP-antwoorden en aanvallen waarbij een extern bestand wordt ingesloten

- Beveiliging tegen schendingen van het HTTP-protocol

- Beveiliging tegen afwijkingen van het HTTP-protocol, zoals een gebruikersagent voor de host en Accept-headers die ontbreken

- Beveiliging tegen bots, crawlers en scanners

- Detectie van veelvoorkomende onjuiste configuraties van toepassingen (dat wil zeggen Apache, IIS, enzovoort)

Een gecentraliseerde Web Application Firewall ter bescherming tegen aanvallen via internet maakt het beveiligingsbeheer veel eenvoudiger en biedt de toepassing meer veiligheid tegen de bedreigingen van indringers. Een WAF-oplossing kan ook sneller reageren op een beveiligingsrisico door een patch voor een bekend beveiligingsprobleem toe te passen op een centrale locatie in plaats van elke afzonderlijke webtoepassing te beveiligen. Bestaande toepassingsgateways kunnen eenvoudig worden geconverteerd naar een toepassingsgateway met Web Application Firewall.

### <a name="traffic-manager"></a>Traffic Manager

Met micro soft [Azure Traffic Manager](../../traffic-manager/traffic-manager-overview.md) kunt u de distributie van gebruikers verkeer voor service-eind punten in verschillende data centers beheren. Service-eind punten die worden ondersteund door Traffic Manager zijn onder andere Azure-Vm's, Web Apps en Cloud Services. U kunt Traffic Manager ook gebruiken met externe, niet-Azure-eindpunten. Traffic Manager gebruikt de Domain Name System (DNS) om client aanvragen door te sturen naar het meest geschikte eind punt op basis van een [routerings methode voor verkeer](../../traffic-manager/traffic-manager-routing-methods.md) en de status van de eind punten.

Traffic Manager biedt een reeks methoden voor het routeren van verkeer voor verschillende toepassings behoeften, [controle](../../traffic-manager/traffic-manager-monitoring.md)van de eindpunt status en automatische failover. Traffic Manager is bestand tegen storingen, waaronder het uitvallen van een hele Azure-regio.

### <a name="azure-load-balancer"></a>Azure Load Balancer

[Azure Load Balancer](../../load-balancer/load-balancer-overview.md) zorgt dat uw toepassingen een hoge beschikbaarheid hebben en goede netwerkprestaties leveren. Het is een laag 4 (TCP, UDP) load balancer waarmee binnenkomend verkeer wordt verdeeld over in orde zijnde instanties van services die zijn gedefinieerd in een set met gelijke taak verdeling. Azure Load Balancer kunnen worden geconfigureerd voor het volgende:

- Taak verdeling van binnenkomend Internet verkeer naar virtuele machines. Deze configuratie wordt ook wel [open bare taak verdeling](../../load-balancer/components.md#frontend-ip-configurations)genoemd.

- Taak verdeling van verkeer tussen virtuele machines in een virtueel netwerk, tussen virtuele machines in Cloud Services of tussen lokale computers en virtuele machines in een cross-premises virtueel netwerk. Deze configuratie wordt ook wel [interne taak verdeling](../../load-balancer/components.md#frontend-ip-configurations)genoemd.

- Extern verkeer door sturen naar een specifieke virtuele machine

### <a name="internal-dns"></a>Interne DNS

U kunt de lijst met DNS-servers die worden gebruikt in een VNet, beheren in de Beheerportal of in het netwerk configuratie bestand. De klant kan Maxi maal 12 DNS-servers voor elk VNet toevoegen. Wanneer u DNS-servers opgeeft, is het belang rijk om te controleren of de DNS-servers van de klant in de juiste volg orde voor de omgeving van de klant staan. DNS-Server lijsten werken niet met Round Robin. Ze worden gebruikt in de volg orde waarin ze zijn opgegeven. Als de eerste DNS-server in de lijst kan worden bereikt, gebruikt de client die DNS-server, ongeacht of de DNS-server correct werkt of niet. Als u de volg orde van de DNS-server voor het virtuele netwerk van de klant wilt wijzigen, verwijdert u de DNS-servers uit de lijst en voegt u deze weer toe in de volg orde die de klant wil. DNS ondersteunt het beschik baarheids aspect van de CIA Security Triad.

### <a name="azure-dns"></a>Azure DNS

De Domain Name System, of DNS, is verantwoordelijk voor het vertalen van een website-of service naam naar het IP-adres. [Azure DNS](../../dns/dns-overview.md) is een service voor het hosten van DNS-domeinen die naamomzetting biedt met behulp van de Microsoft Azure-infrastructuur. Door uw domeinen in Azure te hosten, kunt u uw DNS-records met dezelfde referenties, API's, hulpprogramma's en facturering beheren als voor uw andere Azure-services. DNS ondersteunt het beschik baarheids aspect van de CIA Security Triad.

### <a name="azure-monitor-logs-nsgs"></a>Azure Monitor logboeken Nsg's

U kunt de volgende diagnostische logboek categorieën inschakelen voor Nsg's:

- Gebeurtenis: bevat vermeldingen waarvoor NSG-regels worden toegepast op Vm's en instantie rollen op basis van een MAC-adres. De status voor deze regels wordt elke 60 seconden verzameld.

- Regel teller: bevat vermeldingen voor het aantal keren dat elke NSG regel wordt toegepast om verkeer te weigeren of toe te staan.

### <a name="security-center"></a>Beveiligingscentrum

[Azure Security Center](../../security-center/security-center-introduction.md) de beveiligings status van uw Azure-resources voortdurend geanalyseerd op de aanbevolen procedures voor netwerk beveiliging. Wanneer Security Center mogelijke beveiligings problemen identificeert, worden er [aanbevelingen](../../security-center/security-center-recommendations.md) gemaakt die u door het proces van het configureren van de benodigde besturings elementen leiden om uw resources te beschermen en te beveiligen.

## <a name="compute"></a>Compute
De sectie bevat aanvullende informatie over belang rijke functies op dit gebied en samenvattings informatie over deze mogelijkheden.

### <a name="antimalware--antivirus"></a>Antimalware & anti virus
Met Azure IaaS kunt u antimalware-software gebruiken van beveiligings leveranciers zoals micro soft, Symantec, Trend Micro, McAfee en Kaspersky om uw virtuele machines te beschermen tegen schadelijke bestanden, adware en andere bedreigingen. [Micro soft antimalware](antimalware.md) voor Azure Cloud Services en virtual machines is een beschermings functie waarmee u virussen, spyware en andere schadelijke software kunt identificeren en verwijderen. Micro soft antimalware biedt Configureer bare waarschuwingen wanneer bekende schadelijke of ongewenste software probeert zichzelf te installeren of uit te voeren op uw Azure-systemen. Micro soft antimalware kan ook worden geïmplementeerd met Azure Security Center

### <a name="hardware-security-module"></a>Hardware Security module
Versleuteling en verificatie verbeteren de beveiliging alleen als de sleutels zelf zijn beveiligd. U kunt het beheer en de beveiliging van uw essentiële geheimen en sleutels vereenvoudigen door ze op te slaan in [Azure Key Vault](../../key-vault/general/overview.md). Key Vault biedt de mogelijkheid om uw sleutels op te slaan in Hardware Security modules (Hsm's) die zijn gecertificeerd voor FIPS 140-2 level 2-standaarden. Uw SQL Server versleutelings sleutels voor back-up of [transparante gegevens versleuteling](/sql/relational-databases/security/encryption/transparent-data-encryption) kunnen allemaal worden opgeslagen in Key Vault met alle sleutels of geheimen van uw toepassingen. Machtigingen en toegang tot deze beveiligde items worden beheerd via [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/).

### <a name="virtual-machine-backup"></a>Back-up van virtuele machine
[Azure backup](../../backup/backup-overview.md) is een oplossing die uw toepassings gegevens beveiligt met een kapitaal investering van nul en minimale bedrijfs kosten. Toepassings fouten kunnen uw gegevens beschadigen en menselijke fouten kunnen fouten in uw toepassingen introduceren die kunnen leiden tot beveiligings problemen. Met Azure Backup worden uw virtuele machines met Windows en Linux beveiligd.

### <a name="azure-site-recovery"></a>Azure Site Recovery
Een belang rijk onderdeel van de strategie voor [bedrijfs continuïteit/herstel na nood gevallen (BCDR)](../../best-practices-availability-paired-regions.md) van uw organisatie is te bepalen hoe u bedrijfs werkbelastingen en apps actief kunt houden wanneer geplande en ongeplande storingen optreden. [Azure site Recovery](../../site-recovery/site-recovery-overview.md) helpt de replicatie, failover en herstel van werk belastingen en apps te organiseren, zodat deze beschikbaar zijn vanaf een secundaire locatie als uw primaire locatie uitvalt.

### <a name="sql-vm-tde"></a>TDE SQL-VM
Transparent Data Encryption (TDE) en encryption op kolom niveau (CLE) zijn SQL Server-versleutelings functies. Deze vorm van versleuteling vereist dat klanten de cryptografische sleutels die u voor versleuteling gebruikt, beheert en opslaat.

De Azure Key Vault-service (Azure) is ontworpen voor het verbeteren van de beveiliging en het beheer van deze sleutels op een veilige en Maxi maal beschik bare locatie. Met de SQL Server-connector kan SQL Server deze sleutels vanuit Azure Key Vault gebruiken.

Als u SQL Server met on-premises machines uitvoert, zijn er stappen die u kunt volgen om toegang te krijgen tot Azure Key Vault van uw on-premises SQL Server exemplaar. Maar voor SQL Server in azure-Vm's kunt u tijd besparen door gebruik te maken van de functie voor Azure Key Vault integratie. Met enkele Azure PowerShell-cmdlets om deze functie in te scha kelen, kunt u de configuratie automatiseren die nodig is voor een SQL-VM om toegang te krijgen tot uw sleutel kluis.

### <a name="vm-disk-encryption"></a>VM-schijf versleuteling
[Azure Disk Encryption](./azure-disk-encryption-vms-vmss.md) is een nieuwe mogelijkheid waarmee u uw virtuele Windows-en Linux IaaS-machine schijven kunt versleutelen. Hiermee wordt de industrie standaard BitLocker-functie van Windows en de DM-Crypt-functie van Linux toegepast om volume versleuteling voor het besturings systeem en de gegevens schijven te bieden. De oplossing is geïntegreerd met Azure Key Vault om u te helpen de schijf versleutelings sleutels en geheimen in uw Key Vault-abonnement te beheren. De oplossing zorgt er ook voor dat alle gegevens op de schijven van de virtuele machine op rest worden versleuteld in uw Azure-opslag.

### <a name="virtual-networking"></a>Virtueel netwerk
Virtuele machines hebben een netwerk verbinding nodig. Voor de ondersteuning van deze vereiste vereist Azure dat virtuele machines zijn verbonden met een Azure-Virtual Network. Een Azure-Virtual Network is een logische constructie die boven op de fysieke Azure-netwerk infrastructuur is gebouwd. Elke logische [Azure-Virtual Network](../../virtual-network/virtual-networks-overview.md) is geïsoleerd van alle andere virtuele netwerken van Azure. Deze isolatie helpt ervoor te zorgen dat het netwerk verkeer in uw implementaties niet toegankelijk is voor andere Microsoft Azure-klanten.

### <a name="patch-updates"></a>Patch updates
Patch updates bieden de basis voor het zoeken en verhelpen van mogelijke problemen en het vereenvoudigen van het software-update beheer proces, zowel door het aantal software-updates dat u moet implementeren in uw onderneming te verminderen als door de mogelijkheid te verhogen om naleving te bewaken.

### <a name="security-policy-management-and-reporting"></a>Beheer en rapportage van beveiligings beleid
[Security Center](../../security-center/security-center-introduction.md) helpt u bedreigingen te voor komen, te detecteren en erop te reageren, en biedt u meer inzicht in en controle over de beveiliging van uw Azure-resources. Het biedt geïntegreerde beveiligings bewaking en beleids beheer voor uw Azure-abonnementen, helpt bedreigingen te detecteren die anders niet kunnen worden opgemerkt en werkt met een uitgebreid ecosysteem van beveiligings oplossingen.

## <a name="identity-and-access-management"></a>Identiteits- en toegangsbeheer
Het beveiligen van systemen, toepassingen en gegevens begint met toegangs beheer op basis van een identiteit. De functies voor identiteits-en toegangs beheer die zijn ingebouwd in micro soft-producten en-services, helpen uw organisatie-en persoonlijke gegevens te beschermen tegen onbevoegde toegang wanneer deze beschikbaar zijn voor legitieme gebruikers, waar en wanneer ze deze nodig hebben.

### <a name="secure-identity"></a>Beveiligde identiteit
Micro soft maakt gebruik van meerdere beveiligings procedures en-technologieën in de producten en services voor het beheren van identiteit en toegang.

- [Multi-factor Authentication](https://azure.microsoft.com/services/multi-factor-authentication/) moeten gebruikers meerdere methoden gebruiken voor toegang, on-premises en in de Cloud. Het biedt krachtige verificatie met diverse eenvoudige verificatie opties, terwijl gebruikers een eenvoudig aanmeldings proces kunnen uitvoeren.

- [Microsoft Authenticator](https://aka.ms/authenticator) biedt een gebruiks vriendelijke multi-factor Authentication ervaring die werkt met Microsoft Azure Active Directory-en micro soft-accounts, en biedt ondersteuning voor wearables en goed keuringen op basis van vinger afdrukken.

- Met het [afdwingen van het wacht woord](../../active-directory/authentication/concept-sspr-policy.md) wordt de beveiliging van traditionele wacht woorden verhoogd door de vereisten voor lengte en complexiteit, geforceerde periodieke rotatie en account vergrendeling na mislukte verificatie pogingen op te nemen.

- [Met verificatie op basis van tokens](../../active-directory/develop/authentication-vs-authorization.md) kunt u verificatie via Azure Active Directory.

- Met [Azure op rollen gebaseerd toegangs beheer (Azure RBAC)](../../role-based-access-control/built-in-roles.md) kunt u toegang verlenen op basis van de toegewezen rol van de gebruiker, zodat u gebruikers eenvoudig de benodigde toegangs rechten voor hun taak kunt geven. U kunt Azure RBAC aanpassen volgens het bedrijfs model en de risico tolerantie van uw organisatie.

- Met [geïntegreerde identiteits beheer (hybride identiteit)](../../active-directory/hybrid/plan-hybrid-identity-design-considerations-overview.md) kunt u de toegang van gebruikers tot de interne data centers en Cloud platforms beheren en één gebruikers identiteit voor verificatie en autorisatie voor alle resources maken.

### <a name="secure-apps-and-data"></a>Apps en gegevens beveiligen
[Azure Active Directory](https://azure.microsoft.com/services/active-directory/), een uitgebreide Cloud oplossing voor identiteits-en toegangs beheer, helpt bij het beveiligen van de toegang tot gegevens in toepassingen op site en in de Cloud, en vereenvoudigt het beheer van gebruikers en groepen. Het combineert essentiële Directory Services, Geavanceerd identiteits bestuur, beveiliging en toegangs beheer voor toepassingen, en maakt het eenvoudig voor ontwikkel aars om identiteits beheer op basis van beleid in hun apps te bouwen. Om uw Azure Active Directory te verbeteren, kunt u betaalde mogelijkheden toevoegen met de Azure Active Directory Basic-, Premium P1- en Premium P2-edities.

| Gratis/algemene functies     | Basis functies    |Premium P1-functies |Premium P2-functies | Azure Active Directory toevoegen: alleen de verwante functies van Windows 10|
| :------------- | :------------- |:------------- |:------------- |:------------- |
|   [Directory-objecten](../../active-directory/fundamentals/active-directory-whatis.md),    [gebruikers-of groeps beheer (toevoegen/bijwerken/verwijderen)/inrichten op basis van een gebruiker, apparaatregistratie](../../active-directory/fundamentals/active-directory-whatis.md),  [eenmalige Sign-On (SSO)](../../active-directory/fundamentals/active-directory-whatis.md),     [selfservice voor het wijzigen van wacht woorden voor Cloud gebruikers](../../active-directory/fundamentals/active-directory-whatis.md),     [verbinding maken (synchronisatie-engine die on-premises directory's uitbreidt naar Azure Active Directory)](../../active-directory/fundamentals/active-directory-whatis.md),     [beveiligings-en gebruiks rapporten](../../active-directory/fundamentals/active-directory-whatis.md)       |  [Op groepen gebaseerd toegangs beheer/inrichting](../../active-directory/fundamentals/active-directory-whatis.md), [self-service voor wacht woord opnieuw instellen voor Cloud gebruikers](../../active-directory/fundamentals/active-directory-whatis.md),  [huis stijl (aanmeldings pagina's/toegangs venster aanpassen)](../../active-directory/fundamentals/active-directory-whatis.md),    [toepassings proxy](../../active-directory/fundamentals/active-directory-whatis.md),    [Sla 99,9%](../../active-directory/fundamentals/active-directory-whatis.md) |  [Selfservice groep-en app-beheer/self-service toepassings toevoegingen/dynamische groepen](../../active-directory/fundamentals/active-directory-whatis.md),   [selfservice voor wacht woord opnieuw instellen/wijzigen/ontgrendelen met on-premises write-back](../../active-directory/fundamentals/active-directory-whatis.md),    [multi-factor Authentication (Cloud en on-premises (MFA-server))](../../active-directory/fundamentals/active-directory-whatis.md), [MIM CAL + MIM-server](../../active-directory/fundamentals/active-directory-whatis.md),     [Cloud app Discovery](../../active-directory/fundamentals/active-directory-whatis.md),  [Connect Health](../../active-directory/fundamentals/active-directory-whatis.md),   [Automatische wachtwoord overschakeling voor groeps accounts](../../active-directory/fundamentals/active-directory-whatis.md)|   [Identiteits beveiliging](../../active-directory/identity-protection/overview-identity-protection.md),  [privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md)|   [Een apparaat toevoegen aan Azure AD, Desktop-SSO, Microsoft Passport voor Azure AD, BitLocker-herstel door de beheerder](../../active-directory/fundamentals/active-directory-whatis.md),    [automatische MDM-inschrijving, Self-Service BitLocker-herstel, extra lokale beheerders naar Windows 10-apparaten via Azure AD-deelname](../../active-directory/fundamentals/active-directory-whatis.md)|

- [Cloud app Discovery](/cloud-app-security/set-up-cloud-discovery) is een Premium-functie van Azure Active Directory waarmee u Cloud toepassingen kunt identificeren die worden gebruikt door de werk nemers in uw organisatie.

- [Azure Active Directory Identity Protection](../../active-directory/identity-protection/overview-identity-protection.md) is een beveiligings service die gebruikmaakt van Azure Active Directory anomalie detectie mogelijkheden om een geconsolideerde weer gave te bieden in risico detecties en mogelijke beveiligings problemen die van invloed kunnen zijn op de identiteiten van uw organisatie.

- Met [Azure Active Directory Domain Services](https://azure.microsoft.com/services/active-directory-ds/) kunt u virtuele Azure-machines toevoegen aan een domein zonder dat u domein controllers hoeft te implementeren. Gebruikers melden zich aan bij deze Vm's door hun bedrijfs Active Directory referenties te gebruiken en kunnen naadloos toegang krijgen tot resources.

- [Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) is een Maxi maal beschik bare, wereld wijde identiteits beheer service voor consumenten toepassingen die kan worden geschaald naar honderden miljoenen identiteiten en kunnen worden geïntegreerd in mobiele en webplatformen. Uw klanten kunnen zich bij al uw apps aanmelden via aanpas bare ervaringen die gebruikmaken van bestaande accounts voor sociale media of u kunt nieuwe zelfstandige referenties maken.

- [Azure Active Directory B2B-samen werking](../../active-directory/external-identities/what-is-b2b.md) is een beveiligde partner integratie oplossing die ondersteuning biedt voor uw cross-Company-relaties door partners om uw bedrijfs toepassingen en-gegevens selectief te maken met behulp van hun zelf beheerde identiteiten.

- Met [Azure Active Directory join](../../active-directory/devices/overview.md) kunt u Cloud mogelijkheden voor gecentraliseerd beheer uitbreiden naar Windows 10-apparaten. Hierdoor kunnen gebruikers verbinding maken met de cloud van het bedrijf of de organisatie via Azure Active Directory en kan de toegang tot apps en bronnen worden vereenvoudigd.

- [Azure Active Directory-toepassingsproxy](../../active-directory/manage-apps/application-proxy.md) biedt SSO en beveiligde externe toegang voor webtoepassingen die on-premises worden gehost.

## <a name="next-steps"></a>Volgende stappen

- Begrijp uw [gedeelde verantwoordelijkheid in de Cloud](shared-responsibility.md).

- Meer informatie over hoe [Azure Security Center](../../security-center/security-center-introduction.md) u kan helpen bedreigingen te voor komen, te detecteren en erop te reageren met verbeterde zicht baarheid en controle over de beveiliging van uw Azure-resources.