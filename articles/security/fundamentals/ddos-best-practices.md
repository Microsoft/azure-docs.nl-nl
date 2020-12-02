---
title: Flexibele oplossingen ontwerpen met Azure DDoS Protection
description: Meer informatie over hoe u logboek gegevens kunt gebruiken om grondige inzichten over uw toepassing te krijgen.
services: security
author: terrylanfear
manager: RKarlin
editor: TomSh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/18/2018
ms.author: terrylan
ms.openlocfilehash: e298cb0d1a2c510a096f8ead03f8af7e39c206a8
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96498928"
---
# <a name="azure-ddos-protection---designing-resilient-solutions"></a>Azure DDoS Protection-flexibele oplossingen ontwerpen

Dit artikel is voor IT-besluit vormers en beveiligings personeel. Het verwacht dat u bekend bent met Azure, netwerken en beveiliging.
DDoS is een type aanval dat probeert toepassings bronnen te uitgeputten. Het doel is de beschik baarheid van de toepassing en de mogelijkheid om legitieme aanvragen af te handelen te beïnvloeden. Aanvallen worden verfijnd en groter en van invloed. DDoS-aanvallen kunnen worden gericht op elk eindpunt dat openbaar bereikbaar is via internet. Voor het ontwerpen van een DDoS-tolerantie (Distributed Denial of service) is het plannen en ontwerpen vereist voor verschillende fout modi. Azure biedt continue beveiliging tegen DDoS-aanvallen. Deze beveiliging is standaard geïntegreerd in het Azure-platform en zonder extra kosten.

Naast de core DDoS-beveiliging in het platform biedt [Azure DDoS Protection Standard](https://azure.microsoft.com/services/ddos-protection/) geavanceerde mogelijkheden voor DDoS-risico beperking tegen netwerk aanvallen. Het is automatisch afgestemd op het beveiligen van uw specifieke Azure-resources. De beveiliging is eenvoudig in te scha kelen tijdens het maken van nieuwe virtuele netwerken. Dit kan ook worden gedaan na het maken en er hoeven geen toepassings-of resource wijzigingen te worden aangebracht.

![De rol van Azure DDoS Protection bij het beveiligen van klanten en een virtueel netwerk van een aanvaller](./media/ddos-best-practices/image1.png)


## <a name="fundamental-best-practices"></a>Fundamentele best practices

De volgende secties bieden prescriptieve richt lijnen voor het bouwen van DDoS-flexibele services in Azure.

### <a name="design-for-security"></a>Ontwerpen voor beveiliging

Zorg ervoor dat beveiliging een prioriteit heeft gedurende de hele levens cyclus van een toepassing, van ontwerp en implementatie tot implementatie en bewerkingen. Toepassingen kunnen bugs hebben die een relatief lage hoeveelheid aanvragen mogelijk maken voor het gebruik van een onordinaat aantal resources, wat resulteert in een service storing. 

Als u een service die wordt uitgevoerd op Microsoft Azure wilt beveiligen, moet u een goed beeld hebben van de architectuur van uw toepassing en zich richten op de [vijf pijlers van de software kwaliteit](/azure/architecture/guide/pillars).
U moet rekening houden met typische verkeers volumes, het verbindings model tussen de toepassing en andere toepassingen en de service-eind punten die beschikbaar worden gesteld aan het open bare Internet.

Ervoor zorgen dat een toepassing robuust genoeg is voor het afhandelen van een denial of service die is gericht op de toepassing zelf, is het belangrijkst. Beveiliging en privacy zijn ingebouwd in het Azure-platform, te beginnen met de [Security Development Lifecycle (SDL)](https://www.microsoft.com/sdl/default.aspx). De SDL vertrouwt de beveiliging van elke ontwikkelings fase en zorgt ervoor dat Azure voortdurend wordt bijgewerkt om het nog veiliger te maken.

### <a name="design-for-scalability"></a>Ontwerpen voor schaal baarheid

Schaal baarheid is hoe goed een systeem een grotere belasting kan afhandelen. Ontwerp uw toepassingen zodanig dat ze [horizon taal kunnen worden geschaald](/azure/architecture/guide/design-principles/scale-out) om te voldoen aan de vraag naar een versterkte belasting, met name in het geval van een DDoS-aanval. Als uw toepassing afhankelijk is van één exemplaar van een service, wordt er een Single Point of Failure gemaakt. Door meerdere exemplaren in te richten, zorgt u ervoor dat uw systeem robuuster en schaalbaar is.

Selecteer voor [Azure app service](../../app-service/overview.md)een [app service-abonnement](../../app-service/overview-hosting-plans.md) dat meerdere exemplaren biedt. Configureer voor Azure Cloud Services elk van uw rollen om [meerdere exemplaren](../../cloud-services/cloud-services-choose-me.md)te gebruiken. Zorg ervoor dat de architectuur van de virtuele machine (VM) voor [Azure virtual machines](../../virtual-machines/index.yml)meer dan één VM bevat en dat elke VM is opgenomen in een [beschikbaarheidsset](../../virtual-machines/windows/tutorial-availability-sets.md). We raden u aan [virtuele-machine schaal sets](../../virtual-machine-scale-sets/overview.md) te gebruiken voor de mogelijkheden voor automatisch schalen.

### <a name="defense-in-depth"></a>Diepgaande verdediging

Het voor beeld van een diep gaande verdedigings linie is het beheren van Risico's met behulp van verschillende verdedigings strategieën. Als u beveiligings beveiliging in een toepassing laagt, vermindert u de kans op een geslaagde aanval. U wordt aangeraden beveiligde ontwerpen voor uw toepassingen te implementeren met behulp van de ingebouwde mogelijkheden van het Azure-platform.

Het risico van een aanval neemt bijvoorbeeld toe met de grootte (*Surface Area*) van de toepassing. U kunt de surface area verminderen door een goedkeurings lijst te gebruiken om de beschik bare IP-adres ruimte te sluiten en poorten te belui Steren die niet nodig zijn op de load balancers ([Azure Load Balancer](../../load-balancer/quickstart-load-balancer-standard-public-portal.md) en [Azure-toepassing gateway](../../application-gateway/application-gateway-create-probe-portal.md)). [Netwerk beveiligings groepen (nsg's)](../../virtual-network/network-security-groups-overview.md) zijn een andere manier om de kwets baarheid voor aanvallen te verminderen.
U kunt [service Tags](../../virtual-network/network-security-groups-overview.md#service-tags) en [toepassings beveiligings groepen](../../virtual-network/network-security-groups-overview.md#application-security-groups) gebruiken om de complexiteit te minimaliseren voor het maken van beveiligings regels en het configureren van netwerk beveiliging, als een natuurlijke uitbrei ding van de structuur van een toepassing.

Indien mogelijk moet u Azure-Services in een [virtueel netwerk](../../virtual-network/virtual-networks-overview.md) implementeren. Met deze procedure kunnen service resources communiceren via privé-IP-adressen. Azure service-verkeer van een virtueel netwerk maakt standaard gebruik van open bare IP-adressen als bron-IP-adressen. Als u [service-eind punten](../../virtual-network/virtual-network-service-endpoints-overview.md) gebruikt, wordt service verkeer voor het gebruik van privé adressen van het virtuele netwerk geswitcheerd als de bron-IP-adressen wanneer ze toegang krijgen tot de Azure-service vanuit een virtueel netwerk.

Vaak zien we de on-premises resources van klanten die zijn aangevallen samen met hun resources in Azure. Als u een on-premises omgeving verbindt met Azure, raden we u aan om de bloot stelling van on-premises resources naar het open bare Internet te minimaliseren. U kunt de functies Scale en Advanced DDoS Protection van Azure gebruiken door uw bekende open bare entiteiten in azure te implementeren. Omdat deze openbaar toegankelijke entiteiten vaak een doel zijn voor DDoS-aanvallen, wordt de impact op uw on-premises resources gereduceerd door ze in azure te brengen.

## <a name="azure-offerings-for-ddos-protection"></a>Azure-aanbiedingen voor DDoS-beveiliging

Azure heeft twee DDoS-service aanbiedingen die bescherming bieden tegen netwerk aanvallen (Layer 3 en 4): DDoS Protection Basic en DDoS Protection Standard. 

### <a name="ddos-protection-basic"></a>DDoS Protection Basic

Basis beveiliging is standaard zonder extra kosten geïntegreerd in het Azure. De schaal en de capaciteit van het wereld wijd geïmplementeerde Azure-netwerk bieden bescherming tegen veelvoorkomende aanvallen via netwerk lagen via de controle van het verkeer en de real-time-oplossing. Voor DDoS Protection Basic zijn geen gebruikers configuratie of toepassings wijzigingen vereist. DDoS Protection Basic helpt bij het beveiligen van alle Azure-Services, waaronder PaaS-Services, zoals Azure DNS.

![Kaart weergave van het Azure-netwerk, met de tekst "wereld wijde DDoS-oplossings aanwezigheid" en "toonaangevende DDoS-risico capaciteit"](./media/ddos-best-practices/image3.png)

Basic DDoS Protection in azure bestaat uit zowel software-als hardwareonderdelen. Een software beheergebied beslist wanneer, waar en welk type verkeer moet worden gestuurd via hardware-apparaten die het risico verkeer analyseren en verwijderen. Het besturings vlak maakt deze beslissing op basis van een DDoS Protection *beleid* voor de hele infra structuur. Dit beleid is statisch ingesteld en wordt op universele wijze toegepast op alle Azure-klanten.

Het DDoS Protection beleid specificeert bijvoorbeeld op welk verkeers volume de beveiliging moet worden *geactiveerd.* (Dat wil zeggen dat het verkeer van de Tenant moet worden gerouteerd via reinigings apparaten.) Het beleid geeft vervolgens op hoe de reinigings apparaten de aanval moeten *verminderen* .

De Azure DDoS Protection Basic-service is gericht op de beveiliging van de infra structuur en de beveiliging van het Azure-platform. Het vermindert het verkeer wanneer het een aantal dat zo significant is dat dit van invloed kan zijn op meerdere klanten in een multi tenant omgeving. Het biedt geen waarschuwingen of aangepaste beleids regels per klant.

### <a name="ddos-protection-standard"></a>DDoS Protection Standard

Standaard beveiliging biedt verbeterde functies voor DDoS-risico beperking. Het wordt automatisch afgestemd om uw specifieke Azure-resources in een virtueel netwerk te beveiligen. Beveiliging is eenvoudig in te scha kelen op een nieuw of bestaand virtueel netwerk en er zijn geen wijzigingen in de toepassing of resource. Het heeft verschillende voor delen ten opzichte van de Basic-service, inclusief logboek registratie, waarschuwingen en telemetrie. De volgende secties bevatten een overzicht van de belangrijkste functies van de Azure DDoS Protection Standard-Service.

#### <a name="adaptive-real-time-tuning"></a>Adaptieve real time tuning

De Azure DDoS Protection Basic-service helpt klanten te beschermen en te voor komen dat ze van invloed zijn op andere klanten. Als een service bijvoorbeeld is ingericht voor een typisch volume van legitiem binnenkomend verkeer dat kleiner is dan de frequentie van de *Activering* van het beleid voor de hele infrastructuur DDoS Protection, kan een DDoS-aanval op de resources van die klant niet worden opgemerkt. Meer in het algemeen, de complexiteit van recente aanvallen (bijvoorbeeld multi-vector DDoS) en het toepassingsspecifieke gedrag van tenants aanroepen voor een aangepast beveiligings beleid per klant. Deze aanpassing wordt door de service uitgevoerd met behulp van twee inzichten:

- Automatisch leren van verkeers patronen per klant (per IP) voor laag 3 en 4.

- Het minimaliseren van valse positieven, waarbij rekening wordt gehouden met de schaal van Azure, waarmee IT een aanzienlijke hoeveelheid verkeer kan absorberen.

![Diagram van de werking van DDoS Protection Standard, met omcirkeld beleid genereren](./media/ddos-best-practices/image5.png)

#### <a name="ddos-protection-telemetry-monitoring-and-alerting"></a>DDoS Protection telemetrie, bewaking en waarschuwingen

DDoS Protection Standard maakt voor de duur van een DDoS-aanval uitgebreide telemetrie mogelijk via [Azure monitor](../../azure-monitor/overview.md) . U kunt waarschuwingen configureren voor elk van de Azure Monitor metrische gegevens die DDoS Protection gebruikt. U kunt logboek registratie integreren met Splunk (Azure Event Hubs), Azure Monitor-logboeken en Azure Storage voor geavanceerde analyse via de diagnostische-interface voor Azure Monitor.

##### <a name="ddos-mitigation-policies"></a>DDoS-beperkings beleid

Selecteer in de Azure Portal **Monitor**  >  **metrische gegevens** controleren. Selecteer de resource groep in het deel venster **metrische gegevens** , selecteer een resource type voor het **open bare IP-adres** en selecteer uw open bare Azure IP-adres. DDoS-metrische gegevens worden weer gegeven in het deel venster **beschik bare metrische gegevens** .

DDoS Protection Standard geldt voor elk openbaar IP-adres van de beveiligde bron, in het virtuele netwerk waarop DDoS is ingeschakeld, drie opties voor het beperken van de oplossing (TCP SYN, TCP en UDP). U kunt de drempel waarden voor het beleid weer geven door de metrische **inkomende pakketten te selecteren om de DDoS-beperking te activeren**.

![Diagram beschik bare metrische gegevens en metrische gegevens](./media/ddos-best-practices/image7.png)

De beleids drempels worden automatisch geconfigureerd via een machine learning-gebaseerd netwerk verkeer profile ring. DDoS-beperking treedt alleen op voor een IP-adres onder een aanval wanneer de drempel waarde voor het beleid wordt overschreden.

##### <a name="metric-for-an-ip-address-under-ddos-attack"></a>Metric voor een IP-adres onder DDoS-aanval

Als het open bare IP-adres zich onder een aanval bevindt, wordt de waarde voor de metrische gegevens **onder DDoS-aanval of niet** gewijzigd in 1 als DDoS Protection een risico op het risico verkeer uitvoert.

!["Onder DDoS-aanval of niet" metrisch en diagram](./media/ddos-best-practices/image8.png)

U kunt het beste een waarschuwing configureren voor deze metrische gegevens. U krijgt dan een melding wanneer er een actieve DDoS-beperking is uitgevoerd op uw open bare IP-adres.

Zie [Azure DDoS Protection Standard beheren met de Azure Portal](../../ddos-protection/manage-ddos-protection.md)voor meer informatie.

#### <a name="web-application-firewall-for-resource-attacks"></a>Web Application Firewall voor bron aanvallen

Wat specifiek is voor bron aanvallen op de toepassingslaag, moet u een Web Application Firewall (WAF) configureren om webtoepassingen te beveiligen. Een WAF inspecteert inkomend webverkeer om SQL-injecties, cross-site scripting, DDoS en andere Layer 7-aanvallen te blok keren. Azure biedt [WAF als een functie van Application Gateway](../../web-application-firewall/ag/ag-overview.md) voor gecentraliseerde beveiliging van uw webtoepassingen van veelvoorkomende aanvallen en beveiligings problemen. Er zijn andere WAF-aanbiedingen beschikbaar van Azure-partners die mogelijk beter geschikt zijn voor uw behoeften via [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?search=WAF&page=1).

Zelfs firewalls voor webtoepassingen zijn gevoelig voor volumetrische aanvallen en status afputting. We raden u ten zeerste aan om DDoS Protection Standard in te scha kelen op het virtuele WAF-netwerk om te helpen beschermen tegen maat-en protocol aanvallen. Zie de sectie met [DDoS Protection referentie architecturen](#ddos-protection-reference-architectures) voor meer informatie.

### <a name="protection-planning"></a>Beveiliging plannen

Planning en voor bereiding zijn essentieel om te begrijpen hoe een systeem tijdens een DDoS-aanval wordt uitgevoerd. Het ontwerpen van een respons plan voor incident beheer maakt deel uit van deze inspanning.

Als u DDoS Protection Standard hebt, moet u ervoor zorgen dat deze is ingeschakeld op het virtuele netwerk van Internet gerichte eind punten. Het configureren van DDoS-waarschuwingen helpt u voortdurend te kijken naar mogelijke aanvallen op uw infra structuur. 

Bewaak uw toepassingen onafhankelijk. Het normale gedrag van een toepassing begrijpen. Bereid u voor op een besluit te nemen als de toepassing niet naar verwachting functioneert tijdens een DDoS-aanval.

#### <a name="testing-through-simulations"></a>Testen via simulaties

Het is een goed idee om uw hypo Thesen te testen op de manier waarop uw services reageren op aanvallen door periodieke simulaties uit te voeren. Controleer tijdens het testen of uw services of toepassingen op de verwachte manier blijven functioneren en er geen onderbreking is voor de gebruikers ervaring. Identificeer hiaten uit een technologie en proces oogpunt en neem ze op in de DDoS-respons strategie. We raden u aan om dergelijke tests uit te voeren in faserings omgevingen of tijdens niet-piek uren om de impact op de productie omgeving te minimaliseren.

We hebben een partnerschap gemaakt met [BreakingPoint Cloud](https://www.ixiacom.com/products/breakingpoint-cloud) voor het bouwen van een interface waar Azure-klanten verkeer kunnen genereren voor open bare eind punten met DDoS Protection voor simulaties. U kunt de [BreakingPoint-Cloud](https://www.ixiacom.com/products/breakingpoint-cloud) simulatie gebruiken voor het volgende:

- Valideer hoe Azure DDoS Protection uw Azure-resources helpt beschermen tegen DDoS-aanvallen.

- Optimaliseer uw incidenten respons proces tijdens DDoS-aanval.

- Conformiteit van document DDoS.

- Train uw netwerk beveiligings teams.

Cyber beveiliging vereist een constante innovatie in verdediging. De Azure DDoS Standard-beveiliging is een geavanceerde oplossing met een doel toepassing om steeds complexe DDoS-aanvallen te beperken.

## <a name="components-of-a-ddos-response-strategy"></a>Onderdelen van een DDoS-reactiestrategie

Een DDoS-aanval die gericht is op Azure-resources, vereist meestal een minimale tussen komst van een gebruikers oogpunt. Als onderdeel van een strategie voor incident reacties is het niet meer mogelijk om de impact op de bedrijfs continuïteit te beperken.

### <a name="microsoft-threat-intelligence"></a>Micro soft Threat Intelligence

Micro soft heeft een uitgebreid netwerk met bedreigings informatie. Dit netwerk maakt gebruik van de collectieve kennis van een uitgebreide beveiligings community die ondersteuning biedt voor micro soft onlineservices, micro soft-partners en relaties binnen de Internet beveiligings community. 

Als kritieke infrastructuur provider ontvangt micro soft vroegtijdige waarschuwingen over bedreigingen. Micro soft verzamelt bedreigings informatie van de onlineservices en van zijn algemene klanten database. Micro soft brengt al deze bedreigings intelligentie weer in de Azure DDoS Protection-producten.

De micro soft-eenheid voor digitale misdrijven (DCU) voert ook aanstootgevende strategieën uit op botnets. Botnets zijn een gemeen schappelijke bron van opdracht en beheer voor DDoS-aanvallen.

### <a name="risk-evaluation-of-your-azure-resources"></a>Risico-evaluatie van uw Azure-resources

Het is belang rijk om het bereik van uw risico van een DDoS-aanval voortdurend te begrijpen. Stel u regel matig in:

- Welke nieuwe open bare Azure-resources moeten worden beschermd?

- Is er een Single Point of Failure in de service? 

- Hoe kunnen services worden geïsoleerd om de impact van een aanval te beperken terwijl services beschikbaar blijven voor geldige klanten?

- Zijn er virtuele netwerken waar DDoS Protection Standard moet worden ingeschakeld, maar niet? 

- Zijn mijn Services actief/actief met failover over meerdere regio's?

### <a name="customer-ddos-response-team"></a>DDoS Response Team van de klant

Het maken van een DDoS-antwoord team is een belang rijke stap in het snel en effectief reageren op een aanval. Identificeer contact personen in uw organisatie die zowel de planning als de uitvoering nagaan. Dit DDoS-antwoord team moet de Azure DDoS Protection Standard-Service grondig begrijpen. Zorg ervoor dat het team een aanval kan identificeren en beperken door te coördineren met interne en externe klanten, waaronder het micro soft-ondersteunings team.

Voor uw DDoS-antwoord team raden we u aan om simulatie oefeningen te gebruiken als een normaal onderdeel van de planning van de beschik baarheid van de service en de continuïteit. Deze oefeningen moeten schaal tests bevatten.

### <a name="alerts-during-an-attack"></a>Waarschuwingen tijdens een aanval

Azure DDoS Protection Standard identificeert en vermindert DDoS-aanvallen zonder tussen komst van de gebruiker. Als u een melding wilt ontvangen wanneer er een actieve beperking is voor een beveiligd openbaar IP-adres, kunt u [een waarschuwing configureren](../../ddos-protection/manage-ddos-protection.md) voor de metriek **onder DDoS-aanval of niet**. U kunt ervoor kiezen om waarschuwingen te maken voor de andere DDoS-metrische gegevens om inzicht te krijgen in de schaal van de aanval, het verkeer dat wordt verwijderd en andere details.

#### <a name="when-to-contact-microsoft-support"></a>Wanneer u contact opneemt met micro soft ondersteuning

- Tijdens een DDoS-aanval kunt u zien dat de prestaties van de beveiligde resource ernstig worden gedegradeerd, of dat de bron niet beschikbaar is.

- U denkt dat de DDoS Protection-Service niet werkt zoals verwacht. 

  De DDoS Protection-service start alleen de oplossing als het metrische waarde- **beleid om DDoS-beperking te activeren (TCP/TCP SYN/UDP)** lager is dan het verkeer dat is ontvangen op de beveiligde open bare IP-resource.

- U bent bezig met het plannen van een evenement waardoor uw netwerk verkeer aanzienlijk wordt verbeterd.

- Een actor heeft zich dreigen een DDoS-aanval voor uw resources te starten.

- Als u een lijst wilt toestaan van een IP-of IP-bereik uit Azure DDoS Protection standaard. Een veelvoorkomend scenario is het toestaan van lijst-IP als het verkeer wordt gerouteerd vanuit een externe Cloud WAF naar Azure. 

Voor aanvallen met een belang rijke bedrijfs impact, maakt u een [ondersteunings ticket](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)met een ernst.

### <a name="post-attack-steps"></a>Stappen na aanval

Het is altijd een goede strategie om een postmortem na een aanval uit te voeren en de DDoS-respons strategie zo nodig aan te passen. Aandachtspunten:

- Is er een onderbreking van de service of gebruikers ervaring vanwege een gebrek aan schaal bare architectuur?

- Welke toepassingen of services hebben het meest geleden?

- Hoe effectief was de DDoS-respons strategie en hoe kan deze worden verbeterd?

Als u vermoedt dat u een DDoS-aanval ondervindt, kunt u uw normale Azure-ondersteunings kanalen escaleren.

## <a name="ddos-protection-reference-architectures"></a>DDoS Protection referentie architecturen

DDoS Protection Standard is ontworpen [voor services die zijn geïmplementeerd in een virtueel netwerk](../../virtual-network/virtual-network-for-azure-services.md). Voor andere services is de standaard DDoS Protection Basic-service van toepassing. De volgende referentie architecturen zijn gerangschikt op scenario's, waarbij architectuur patronen samen worden gegroepeerd.

### <a name="virtual-machine-windowslinux-workloads"></a>Werk belastingen voor virtuele machines (Windows/Linux)

#### <a name="application-running-on-load-balanced-vms"></a>Toepassing die wordt uitgevoerd op virtuele machines met taak verdeling

Deze referentie architectuur bevat een reeks beproefde procedures voor het uitvoeren van meerdere Windows-Vm's in een schaalset achter een load balancer, om de beschik baarheid en schaal baarheid te verbeteren. Deze architectuur kan worden gebruikt voor een staatloze werk belasting, zoals een webserver.

![Diagram van de referentie architectuur voor een toepassing die wordt uitgevoerd op virtuele machines met taak verdeling](./media/ddos-best-practices/image9.png)

In deze architectuur wordt een werkbelasting verdeeld over meerdere VM-exemplaren. Er is één openbaar IP-adres en Internet verkeer wordt via een load balancer naar de virtuele machines gedistribueerd. DDoS Protection standaard is ingeschakeld in het virtuele netwerk van het Azure (Internet) load balancer waaraan het open bare IP-adres is gekoppeld.

De load balancer distribueert binnenkomende Internet aanvragen naar de VM-exemplaren. Met virtuele-machine schaal sets kan het aantal Vm's hand matig worden geschaald of uitgekozen, of automatisch op basis van vooraf gedefinieerde regels. Dit is belang rijk als de resource zich onder DDoS-aanval bevindt. Zie [dit artikel](/azure/architecture/reference-architectures/virtual-machines-windows/multi-vm)voor meer informatie over deze referentie architectuur.

#### <a name="application-running-on-windows-n-tier"></a>Toepassing die wordt uitgevoerd op Windows N-tier

Er zijn veel manieren om een architectuur met meerdere lagen te implementeren. In het volgende diagram ziet u een typische webtoepassing met drie lagen. Deze architectuur bouwt voort op het artikel [virtuele machines met gelijke taak verdeling uitvoeren voor schaal baarheid en beschik baarheid](/azure/architecture/reference-architectures/virtual-machines-windows/multi-vm). De web- en bedrijfslagen gebruiken VM's met taakverdeling.

![Diagram van de referentie architectuur voor een toepassing die wordt uitgevoerd op Windows N-tier](./media/ddos-best-practices/image10.png)

In deze architectuur is DDoS Protection standaard ingeschakeld op het virtuele netwerk. Alle open bare Ip's in het virtuele netwerk krijgen DDoS-beveiliging voor laag 3 en 4. Voor de beveiliging van laag 7 implementeert u Application Gateway in de WAF-SKU. Zie [dit artikel](/azure/architecture/reference-architectures/virtual-machines-windows/n-tier)voor meer informatie over deze referentie architectuur.

#### <a name="paas-web-application"></a>PaaS-webtoepassing

Deze referentie architectuur laat zien hoe een Azure App Service-toepassing wordt uitgevoerd in één regio. Deze architectuur bevat een reeks beproefde procedures voor een webtoepassing die gebruikmaakt van [Azure app service](https://azure.microsoft.com/documentation/services/app-service/) en [Azure SQL database](https://azure.microsoft.com/documentation/services/sql-database/).
Er is een stand-by-regio ingesteld voor failover-scenario's.

![Diagram van de referentie architectuur voor een PaaS-webtoepassing](./media/ddos-best-practices/image11.png)

Azure Traffic Manager stuurt binnenkomende aanvragen naar Application Gateway in een van de regio's. Tijdens normale bewerkingen worden aanvragen gerouteerd naar Application Gateway in de actieve regio. Als deze regio niet beschikbaar is, wordt Traffic Manager failover naar Application Gateway in de stand-by-regio.

Al het verkeer van Internet dat bestemd is voor de webtoepassing, wordt via Traffic Manager doorgestuurd naar het [Application Gateway open bare IP-adres](../../application-gateway/application-gateway-web-app-overview.md) . In dit scenario is de app service (Web-app) zelf niet rechtstreeks gericht en wordt beveiligd door Application Gateway. 

We raden u aan de Application Gateway WAF-SKU (modus voor komen) te configureren om te helpen beschermen tegen laag 7-aanvallen (HTTP/HTTPS/websockets). Daarnaast worden Web-Apps zodanig geconfigureerd dat [alleen verkeer van het Application Gateway](https://azure.microsoft.com/blog/ip-and-domain-restrictions-for-windows-azure-web-sites/) IP-adres wordt geaccepteerd.

Zie [dit artikel](/azure/architecture/reference-architectures/app-service-web-app/multi-region)voor meer informatie over deze referentie architectuur.

### <a name="mitigation-for-non-web-paas-services"></a>Risico beperking voor niet-PaaS Services

#### <a name="hdinsight-on-azure"></a>HDInsight op Azure

Deze referentie architectuur toont het configureren van DDoS Protection Standard voor een [Azure HDInsight-cluster](../../hdinsight/index.yml). Zorg ervoor dat het HDInsight-cluster is gekoppeld aan een virtueel netwerk en dat DDoS Protection is ingeschakeld op het virtuele netwerk.

![De deel Vensters HDInsight en geavanceerde instellingen met virtuele netwerk instellingen](./media/ddos-best-practices/image12.png)

![Selectie voor het inschakelen van DDoS Protection](./media/ddos-best-practices/image13.png)

In deze architectuur wordt verkeer dat is bestemd voor het HDInsight-cluster via internet doorgestuurd naar het open bare IP-adres dat is gekoppeld aan de HDInsight-gateway load balancer. De gateway load balancer verzendt het verkeer vervolgens rechtstreeks naar de hoofd knooppunten of de worker-knoop punten. Omdat DDoS Protection standaard is ingeschakeld in het virtuele HDInsight-netwerk, krijgen alle open bare IP-adressen in het virtuele netwerk DDoS-beveiliging voor laag 3 en 4. Deze referentie architectuur kan worden gecombineerd met de referentie architecturen N-tier en meerdere regio's.

Meer informatie over deze referentie architectuur kunt u vinden in de documentatie [Azure HDInsight uitbreiden met azure Virtual Network](../../hdinsight/hdinsight-plan-virtual-network-deployment.md?toc=%2fazure%2fvirtual-network%2ftoc.json) .


> [!NOTE]
> Azure App Service Environment voor PowerApps-of API-beheer in een virtueel netwerk met een openbaar IP-adres worden niet systeem eigen ondersteund.

## <a name="next-steps"></a>Volgende stappen

* [Gedeelde verantwoordelijkheid in de cloud](shared-responsibility.md)
* [Azure DDoS Protection product pagina](https://azure.microsoft.com/services/ddos-protection/)
* [Documentatie over Azure DDoS Protection](../../ddos-protection/ddos-protection-overview.md)