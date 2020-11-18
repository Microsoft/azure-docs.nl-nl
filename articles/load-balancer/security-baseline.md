---
title: Azure-beveiligings basislijn voor Azure Load Balancer
description: De Azure Load Balancer Security Baseline voorziet in procedure richtlijnen en resources voor het implementeren van de beveiligings aanbevelingen die zijn opgegeven in de Azure Security-Bench Mark.
author: msmbaldwin
ms.service: load-balancer
ms.topic: conceptual
ms.date: 09/28/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 37db72b2b5dadfba930ca003121140fd99c4736d
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94686069"
---
# <a name="azure-security-baseline-for-azure-load-balancer"></a>Azure-beveiligings basislijn voor Azure Load Balancer

De Azure-beveiligings basislijn voor Microsoft Azure Load Balancer bevat aanbevelingen waarmee u de beveiligings postuur van uw implementatie kunt verbeteren. De basis lijn voor deze service wordt opgehaald uit de [Azure Security Bench Mark-versie 1,0](https://docs.microsoft.com/azure/security/benchmarks/overview), die aanbevelingen biedt over hoe u uw cloud oplossingen kunt beveiligen in azure met onze richt lijnen voor best practices. Zie [overzicht van Azure Security-basis lijnen](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)voor meer informatie.

## <a name="network-security"></a>Netwerkbeveiliging

*Zie [Azure Security Bench Mark: Network Security](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security)(Engelstalig) voor meer informatie.*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Azure-resources in virtuele netwerken beveiligen

**Richt lijnen**: gebruik interne Azure load balancers om alleen verkeer toe te staan van back-end-bronnen vanuit bepaalde virtuele netwerken of gekoppelde virtuele netwerken zonder bloot stelling aan Internet. Implementeer een externe Load Balancer met de bron netwerk adres omzetting (SNAT) om de IP-adressen van back-endservers te maskeren voor beveiliging tegen rechtstreekse bloot stelling via internet.

Azure biedt twee soorten Load Balancer-aanbiedingen, Standard en Basic. Gebruik de Standard Load Balancer voor alle productie werkbelastingen. Implementeer netwerk beveiligings groepen en sta alleen toegang toe tot de vertrouwde poorten en IP-adresbereiken van uw toepassing. Als er geen netwerk beveiligings groep is toegewezen aan het back-end-subnet of de NIC van de virtuele machine van de back-end, is verkeer niet toegestaan voor deze resources van de load balancer. Met standaard load balancers kunt u uitgaande regels opgeven voor het definiëren van uitgaande NAT met een netwerk beveiligings groep. Bekijk deze uitgaande regels om het gedrag van uw uitgaande verbindingen af te stemmen. 

Het gebruik van een Standard Load Balancer wordt aanbevolen voor uw productie werkbelastingen en normaal gesp roken wordt de basis Load Balancer alleen gebruikt voor het testen omdat het basis type standaard open is voor verbindingen van het internet en geen netwerk beveiligings groepen voor bewerking vereist. 

- [Uitgaande verbindingen in Azure](load-balancer-outbound-connections.md)

- [Open bare Azure-Load Balancer bijwerken](https://docs.microsoft.com/azure/load-balancer/upgrade-basic-standard)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1,2: de configuratie en het verkeer van virtuele netwerken, subnetten en Nic's bewaken en vastleggen

**Hulp**: de Load Balancer is een Pass Through-service, omdat deze afhankelijk is van de regels voor netwerk beveiligings groepen die worden toegepast op back-end-bronnen en de geconfigureerde regels voor uitgaande verbindingen om Internet toegang te beheren.

Controleer de uitgaande regels die zijn geconfigureerd voor uw Standard Load Balancer via de Blade uitgaande regels van uw Load Balancer en de Blade taakverdelings regels waar mogelijk impliciete uitgaande regels zijn ingeschakeld.

Bewaak het aantal van uw uitgaande verbindingen om bij te houden hoe vaak uw resources met internet zijn verbonden. 

Gebruik Security Center en volg de aanbevelingen voor netwerk beveiliging om uw Azure-netwerk bronnen te beveiligen.

Volg de aanbevelingen voor beveiliging voor uw back-endservers en schakel logboeken stroom voor netwerk beveiligings groepen in en verzend de logboeken naar een Azure Storage account voor controle.

U kunt de stroom logboeken ook naar een Log Analytics-werk ruimte verzenden en vervolgens Traffic Analytics gebruiken om inzicht te krijgen in verkeers patronen in uw Azure-Cloud. Voor delen van Traffic Analytics zijn onder andere de mogelijkheid om netwerk activiteiten te visualiseren, HOTS pots en beveiligings Risico's te identificeren, verkeers patronen te begrijpen en netwerk configuraties te lokaliseren.

- [Stroom logboeken van netwerk beveiligings groepen inschakelen](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

- [Traffic Analytics inschakelen en gebruiken](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)

- [Informatie over de netwerk beveiliging die wordt verschaft door Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-network-recommendations)

- [De statistieken van mijn uitgaande verbinding Hoe kan ik controleren](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics#how-do-i-check-my-outbound-connection-statistics)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="13-protect-critical-web-applications"></a>1,3: essentiële webtoepassingen beveiligen

**Richt lijnen**: Definieer expliciet Internet connectiviteit en geldige bron-ip's via uitgaande regels en netwerk beveiligings groepen met uw Load Balancer om de bedreigings informatie van micro soft te gebruiken voor het beveiligen van uw webtoepassingen.

- [De Azure Firewall integreren](https://docs.microsoft.com/azure/firewall/integrate-lb)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: communicatie met bekende schadelijke IP-adressen weigeren

**Hulp**: Schakel Azure DDoS-standaard beveiliging (Distributed Denial of service) in op uw Azure-Virtual Network om te beschermen tegen DDoS-aanvallen. 

Implementeer Azure Firewall op alle netwerk grenzen van de organisatie met op bedreigingen gebaseerd filteren en is geconfigureerd voor ' waarschuwen en weigeren ' voor schadelijk netwerk verkeer.

 

Gebruik Security Center beveiliging tegen bedreigingen om communicatie met bekende schadelijke IP-adressen te detecteren. 

De Standard Load Balancer is ontworpen om standaard te worden beveiligd en een deel van een privé-of geïsoleerde Virtual Network. Het wordt gesloten voor binnenkomende stromen, tenzij deze worden geopend door netwerk beveiligings groepen om expliciet toegestaan verkeer toe te staan en om bekende schadelijke IP-adressen te weigeren. Tenzij er een netwerk beveiligings groep op een subnet of NIC van uw virtuele-machine bron zich achter de Load Balancer bevindt, is verkeer niet toegestaan om deze bron te bereiken. 

Implementeer Azure Firewall op elke netwerk grenzen van de organisatie met op bedreigingen gebaseerd filteren en is geconfigureerd om te ' waarschuwen en weigeren ' voor schadelijk netwerk verkeer om aanvallen van schadelijke IP-adressen te voor komen. 

Implementeer richt lijnen voor het integreren van Azure Firewall met uw Load Balancer.

Gebruik Security Center functies voor beveiliging tegen bedreigingen om communicatie met bekende schadelijke IP-adressen te detecteren. 

Security Center (Standard-laag) biedt just-in-time toegang tot virtuele machines en configureert toegestane IP-adressen van de bron om alleen toegang toe te staan vanuit goedgekeurde IP-adressen/bereiken.
 

Gebruik de functie adaptieve netwerk beveiliging van Security Center om configuraties van netwerk beveiligings groepen aan te bevelen die poorten en bron-Ip's beperken op basis van daad werkelijk verkeer en bedreigings informatie.
 

- [Azure DDoS Protection Standard beheren met de Azure Portal](https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection)

- [Azure Firewall op bedreigingen gebaseerd filteren](https://docs.microsoft.com/azure/firewall/threat-intel)

- [Bescherming tegen bedreiging in Azure Security Center](https://docs.microsoft.com/azure/security-center/threat-protection)

- [Beheerpoorten beveiligen met just-in-time-toegang](https://docs.microsoft.com/azure/security-center/security-center-just-in-time)

- [Adaptieve netwerk beveiliging in Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-adaptive-network-hardening)

- [Azure Firewall met uw Load Balancer integreren](https://docs.microsoft.com/azure/firewall/overview)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="15-record-network-packets"></a>1,5: netwerk pakketten opnemen

**Hulp**: Schakel Network Watcher pakket vastleggen in om afwijkende activiteiten te onderzoeken.

- [Een Network Watcher-exemplaar maken](https://docs.microsoft.com/azure/network-watcher/network-watcher-create)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: op netwerk gebaseerde inbreuk detectie/indringings systemen (ID'S/IP-adressen) implementeren

**Richt lijnen**: Implementeer een aanbieding vanuit de Azure Marketplace die ondersteuning biedt voor ID'S/IP-adressen met Payload-inspectie mogelijkheden voor de omgeving van uw Load Balancer. 

Gebruik Azure Firewall bedreigings informatie als Payload inspectie geen vereiste is. Azure Firewall op bedreigingen gebaseerd filteren wordt gebruikt om het verkeer van en naar bekende schadelijke IP-adressen en domeinen te Signa lering en/of te blok keren. De IP-adressen en domeinen zijn afkomstig van de Microsoft Bedreigingsinformatie-feed.

Implementeer de door u gewenste firewall oplossing op elk van de netwerk grenzen van uw organisatie om schadelijk verkeer te detecteren en/of te blok keren.

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

- [Azure Firewall implementeren](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal)

- [Waarschuwingen configureren met Azure Firewall](https://docs.microsoft.com/azure/firewall/threat-intel)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="17-manage-traffic-to-web-applications"></a>1,7: verkeer naar webtoepassingen beheren

**Richt lijnen**: Definieer expliciet Internet connectiviteit en geldige bron-ip's via uitgaande regels en netwerk beveiligings groepen met uw Load Balancer om de Threat Intelligence-functies van micro soft te gebruiken voor het beveiligen van uw webtoepassingen.

- [De Azure Firewall integreren](https://docs.microsoft.com/azure/firewall/integrate-lb)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: de complexiteit en administratieve overhead van netwerk beveiligings regels minimaliseren

**Hulp**: gebruik service tags in plaats van specifieke IP-adressen bij het maken van beveiligings regels. Geef de servicetag naam op in het bron-of doel veld van een regel om het verkeer voor de bijbehorende service toe te staan of te weigeren. 

Micro soft beheert de adres voorvoegsels die zijn opgenomen in het servicetag van de service en werkt de servicetag automatisch bij met gewijzigde adressen. 

Standaard bevat elke netwerk beveiligings groep de service label AzureLoadBalancer om verkeer met de status test toe te staan. 

Raadpleeg de Azure-documentatie voor alle service tags die beschikbaar zijn voor gebruik in regels voor netwerk beveiligings groepen.

- [Beschik bare service Tags](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#available-service-tags)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: standaard beveiligings configuraties voor netwerk apparaten onderhouden

**Richt lijnen**: standaard beveiligings configuraties voor netwerk bronnen definiëren en implementeren met Azure Policy.

Gebruik Azure-blauw drukken om grootschalige Azure-implementaties te vereenvoudigen door sleutel omgevings artefacten, zoals Azure Resources Manager-sjablonen, Azure RBAC-besturings elementen en-beleid, in één blauw druk te definiëren. 

De blauw druk Toep assen op nieuwe abonnementen en het beheer en het beheer nauw keuriger maken.

- [Azure Policy configureren en beheren](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Voor beelden Azure Policy voor netwerken](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network)

- [Een Azure Blueprint maken](https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="110-document-traffic-configuration-rules"></a>1,10: configuratie regels voor het document verkeer

**Richt lijnen**: gebruik resource Tags voor netwerk beveiligings groepen en andere bronnen die betrekking hebben op netwerk beveiliging en verkeers stroom. 

Gebruik het veld Beschrijving om de regels te documenteren die verkeer naar of van een netwerk toestaan voor afzonderlijke regels voor netwerk beveiligings groepen.

Implementeer een van de ingebouwde Azure Policy definities met betrekking tot labeling, zoals ' vereist label en de bijbehorende waarde ', waarmee wordt gegarandeerd dat alle resources worden gemaakt met tags en op de hoogte moeten zijn van bestaande niet-gelabelde resources.

Gebruik Azure PowerShell of Azure CLI om op basis van hun labels acties op te zoeken of uit te voeren op resources.

- [Tags maken en gebruiken](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

- [Een Azure-Virtual Network maken](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

- [Netwerk verkeer filteren met regels voor netwerk beveiligings groepen](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: gebruik automatische hulpprogram ma's om netwerk bron configuraties te bewaken en wijzigingen te detecteren

**Hulp**: Azure-activiteiten logboek gebruiken om resource configuraties te bewaken en wijzigingen in uw Azure-resources te detecteren. 

Maak waarschuwingen in Azure Monitor om u te waarschuwen wanneer kritieke resources worden gewijzigd.

- [Activiteiten logboek gebeurtenissen van Azure weer geven en ophalen](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view)

- [Waarschuwingen maken in Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

## <a name="logging-and-monitoring"></a>Logboekregistratie en bewaking

*Zie [Azure Security Bench Mark: Logging and monitoring](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring)(Engelstalig) voor meer informatie.*

### <a name="22-configure-central-security-log-management"></a>2,2: Centraal beveiligings logboek beheer configureren

**Hulp**: Bekijk de wijzigingen in de regels voor uitgaande verbindingen en netwerk beveiligings groepen voor uw load balancers door het activiteiten logboek in uw abonnementen weer te geven. 

Bekijk de interne logboeken van de host om te controleren of uw back-end-bronnen veilig zijn.

Deze logboeken exporteren naar Log Analytics of een ander opslag platform. Gebruik in Azure Monitor Log Analytics-werk ruimten om analyses uit te voeren en te uitvoeren en gebruik Azure Storage accounts voor lange termijn-en archiverings opslag.

Deze gegevens in-of uitschakelen voor Azure Sentinel of een SIEM van derden op basis van uw zakelijke vereisten voor uw organisatie.

- [Azure-Sentinel onboarden](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

- [Platform logboeken en metrische gegevens verzamelen met Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings)

- [Interne host-logboeken van de virtuele machine van Azure verzamelen met Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/learn/quick-collect-azurevm)

- [Aan de slag met Azure Monitor en integratie van SIEM van derden](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

- [Activiteiten logboeken van platform](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: controle logboek registratie inschakelen voor Azure-resources

**Richt lijnen**: Controleer de logboek registratie van het besturings element en het beheer en de controle gegevens die zijn vastgelegd met activiteiten logboeken voor de basis Load Balancer. Deze vastleg instellingen zijn standaard ingeschakeld. 

Gebruik activiteiten Logboeken om acties op resources te controleren om alle activiteiten en hun status weer te geven. 

Bepalen welke bewerkingen zijn uitgevoerd op de resources in uw abonnement met activiteiten logboeken: 

- wie de bewerking heeft gestart
- Wanneer de bewerking is uitgevoerd
- de status van de bewerking

- de waarden van andere eigenschappen die u kunnen helpen bij het onderzoeken van de bewerking

Gegevens ophalen uit het activiteiten logboek via Azure PowerShell, de Azure-opdracht regel interface (CLI), de Azure-REST API of de Azure Portal. 

Implementeer Multi-Dimension Diagnostic voor de mogelijkheden van de Standard Load Balancer-configuraties met Azure Monitor.  Dit zijn de beschik bare metrische gegevens voor beveiliging, waaronder informatie over SNAT-verbindingen (bron netwerkadres omzetting), poorten. Er zijn ook extra metrische gegevens over SYN-pakketten (Synchronize) en pakket tellers beschikbaar. 

U kunt via Api's multi-dimensionale metrieken ophalen en deze naar een opslag account schrijven met behulp van de optie ' alle metrische gegevens '.

Gebruik het Azure audit logs-inhouds pakket met micro soft Power BI om uw gegevens te analyseren met vooraf geconfigureerde Dash boards of om de weer gaven aan te passen op basis van uw vereisten.

Stream-logboeken naar een Event Hub-of Log Analytics-werk ruimte. Ze kunnen ook worden geëxtraheerd uit Azure Blob-opslag en worden weer gegeven in verschillende hulpprogram ma's, zoals Excel en Power BI. 

Schakel gegevens naar Azure Sentinel of een SIEM van derden in op basis van uw bedrijfs vereisten.

- [Lees dit artikel met stapsgewijze instructies voor elke methode die wordt beschreven in de audit bewerkingen met Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/management/view-activity-logs)

- [Azure Monitor-logboeken voor openbare Basic Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-monitor-log)

- [Activiteiten logboeken weer geven om acties op resources te controleren](https://docs.microsoft.com/azure/azure-resource-manager/management/view-activity-logs)

- [Via Api's multi-dimensionale metrieken ophalen](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics#retrieve-multi-dimensional-metrics-programmatically-via-apis)

- [Aan de slag met Azure Monitor en integratie van SIEM van derden](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="25-configure-security-log-storage-retention"></a>2,5: Bewaar beveiliging van het beveiligings logboek configureren

**Hulp**: het activiteiten logboek is standaard ingeschakeld en blijft 90 dagen geldig in de gebeurtenis logboeken van Azure. Stel de Bewaar periode voor uw Log Analytics-werk ruimte in op basis van de nalevings voorschriften van uw organisatie in Azure Monitor. Gebruik Azure Storage accounts voor lange termijn-en archiverings opslag.

- [Activiteiten logboeken weer geven voor het bewaken van acties op het artikel resources](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit)

- [De Bewaar periode voor gegevens wijzigen in Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

- [Bewaar beleid configureren voor logboeken van Azure Storage-account](https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account#configure-logging)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="26-monitor-and-review-logs"></a>2,6: Logboeken bewaken en controleren

**Richt lijnen**: Standard Load Balancer resources controleren, beheren en problemen oplossen met behulp van de Load Balancer pagina in de Azure Portal en de resource Health pagina onder Azure monitor. Beschik bare metrische gegevens voor beveiliging zijn onder meer informatie over de bron netwerk adres omzetting (SNAT)-verbindingen, poorten. Daarnaast zijn er ook metrische gegevens over SYN-pakketten (Synchronize) en pakket tellers beschikbaar. 

Gebruik Azure Monitor om de status van het eind punt status te controleren met multi-dimensionale metrische gegevens voor standaard, externe en interne, load balancers. Verzamel deze metrische gegevens via een programma via Api's en naar een opslag account geschreven via de optie ' alle metrische gegevens '.

Azure Monitor-logboeken zijn niet beschikbaar voor interne load balancers van de Basic. 

Gebruik Azure Monitor om de status van de status test per back-end-pool te bekijken voor de eenvoudige externe Load Balancer, zoals het aantal exemplaren in uw back-end-pool dat geen aanvragen van de Load Balancer ontvangt als gevolg van fouten in de status test. 

Implementeer logboek registratie met Azure Operational Insights om statistieken te geven over load balancer status. 

Gebruik de activiteiten Logboeken om waarschuwingen weer te geven en acties te controleren op resources en hun status in uw Azure-abonnementen. Activiteiten logboeken zijn standaard ingeschakeld en kunnen worden weer gegeven in de Azure Portal. 

Gebruik micro soft Power BI met het inhouds pakket voor Azure audit logs en analyseer uw gegevens met vooraf geconfigureerde Dash boards. Pas de weer gaven aan uw vereisten aan op basis van de bedrijfs behoeften. 

Stream-logboeken naar een Event Hub-of Log Analytics-werk ruimte. Ze kunnen ook worden geëxtraheerd uit Azure Blob-opslag en worden weer gegeven in verschillende hulpprogram ma's, zoals Excel en Power BI. U kunt gegevens in-en inschakelen voor Azure Sentinel of een SIEM van derden.

- [Statustesten voor Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview)

- [Azure Monitor REST API](https://docs.microsoft.com/rest/api/monitor)

- [Metrische gegevens ophalen via REST API](https://docs.microsoft.com/rest/api/monitor/metrics/list)

- [Diagnostische gegevens Standard Load Balancer met metrische gegevens, waarschuwingen en resource status](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics)

- [Azure Monitor-logboeken voor openbare Basic Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-monitor-log)

- [Uw load balancer metrische gegevens weer geven in de Azure Portal](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics#view-your-load-balancer-metrics-in-the-azure-portal)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: waarschuwingen inschakelen voor afwijkende activiteiten

**Hulp**: gebruik Security Center met log Analytics werk ruimte voor het bewaken en waarschuwen over afwijkende activiteiten die betrekking hebben op Load Balancer in beveiligings logboeken en-gebeurtenissen.

Gegevens in-en uitschakelen voor Azure Sentinel of een SIEM-hulp programma van derden.

- [Azure-Sentinel onboarden](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

- [Waarschuwingen beheren in Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts)

- [Een waarschuwing over logboek gegevens van log Analytics](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="28-centralize-anti-malware-logging"></a>2,8: registratie van anti-malware centraliseren

**Richt lijnen**: niet van toepassing op Azure Load Balancer. Deze aanbeveling is bedoeld voor reken resources.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="29-enable-dns-query-logging"></a>2,9: DNS-query logboek registratie inschakelen

**Richt lijnen**: niet van toepassing als Azure Load Balancer is een basis netwerk service die geen DNS-query's maakt.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="210-enable-command-line-audit-logging"></a>2,10: controle logboek registratie op opdracht regel inschakelen

**Richt lijnen**: niet van toepassing op Azure Load Balancer omdat deze aanbeveling van toepassing is op reken resources.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

## <a name="identity-and-access-control"></a>Identiteits- en toegangsbeheer

*Zie [Azure Security Bench Mark: identiteits-en toegangs beheer](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control)voor meer informatie.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: een inventaris van beheerders accounts onderhouden

**Richt lijnen**: met Azure op rollen gebaseerd toegangs beheer (Azure RBAC) kunt u de toegang tot Azure-resources, zoals uw Load Balancer, beheren via roltoewijzingen. Wijs deze rollen toe aan gebruikers, groeperingen van service-principals en beheerde identiteiten. 

Vooraf gedefinieerde en ingebouwde rollen inventariseren voor bepaalde resources met hulpprogram ma's als Azure CLI, Azure PowerShell of de Azure Portal.

- [Een directory-rol verkrijgen in azure AD met Power shell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [Leden van een directory-rol in azure AD ophalen met Power shell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

## <a name="data-protection"></a>Gegevensbeveiliging

*Zie [Azure Security Bench Mark: Data Protection](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection)voor meer informatie.*

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4,6: Azure RBAC gebruiken om de toegang tot resources te beheren

**Hulp**: Azure RBAC gebruiken om de toegang tot uw Load Balancer-resources te beheren.

- [Azure RBAC configureren](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: voor komen dat gegevens verlies op basis van host wordt gebruikt voor het afdwingen van toegangs beheer

**Hulp**: Load Balancer is een Pass Through-service die geen klant gegevens opslaat. Het maakt deel uit van het onderliggende platform dat door micro soft wordt beheerd. 

Micro soft behandelt alle inhoud van de klant als vertrouwelijk en gaat naar uitstekende lengtes om het verlies en de bloot stelling van klant gegevens te beschermen. 

Om ervoor te zorgen dat klant gegevens in azure veilig blijven, heeft micro soft een reeks robuuste besturings elementen en mogelijkheden voor gegevens bescherming geïmplementeerd en onderhouden. 

- [Informatie over beveiliging van klanten in azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: gedeeld

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: wijzigingen in essentiële Azure-resources vastleggen en waarschuwen

**Hulp**: gebruik Azure monitor in combi natie met het Azure-activiteiten logboek om waarschuwingen te maken wanneer wijzigingen worden aangebracht in essentiële Azure-resources, zoals load balancers die worden gebruikt voor belang rijke productie werkbelastingen.

- [Waarschuwingen maken voor gebeurtenissen in het Azure-activiteiten logboek](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

## <a name="inventory-and-asset-management"></a>Inventarisatie en asset-management

*Zie [Azure Security Bench Mark: Inventory and Asset Management](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management)voor meer informatie.*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: automatische Asset-detectie oplossing gebruiken

**Hulp**: Azure resource Graph gebruiken om alle resources (zoals compute, opslag, netwerk, poorten, protocollen enzovoort) in uw abonnementen op te vragen en te detecteren. Azure Resource Manager wordt aanbevolen om huidige resources te maken en te gebruiken. 

Zorg ervoor dat u de juiste (Lees) machtigingen in uw Tenant hebt en alle Azure-abonnementen en-resources in uw abonnementen hebt geïnventariseerd.

- [Query's maken met Azure Resource Graph Explorer](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

- [Uw Azure-abonnementen weer geven](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

- [Meer informatie over Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="62-maintain-asset-metadata"></a>6,2: meta gegevens van activa onderhouden

**Richt lijnen**: Tags Toep assen op Azure-resources met meta gegevens om te organiseren op logische wijze volgens een taxonomie.

- [Tags maken en gebruiken](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: niet-geautoriseerde Azure-resources verwijderen

**Richt lijnen**: Gebruik labels, beheer groepen en afzonderlijke abonnementen, indien van toepassing, om assets te organiseren en bij te houden. 

Sluit de inventaris op regel matige basis af en zorg ervoor dat niet-geautoriseerde resources tijdig worden verwijderd uit uw abonnementen.

- [Aanvullende Azure-abonnementen maken](https://docs.microsoft.com/azure/billing/billing-create-subscription)

- [Beheer groepen maken](https://docs.microsoft.com/azure/governance/management-groups/create)

- [Tags maken en gebruiken](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6,4: een inventaris van goedgekeurde Azure-resources definiëren en onderhouden

**Hulp**: een lijst met goedgekeurde Azure-resources maken op basis van de behoeften van uw organisatie, die u kunt gebruiken als mechanisme voor het toestaan van een lijst. Hierdoor kan uw organisatie alle pas beschik bare Azure-Services vrijgeven nadat deze formeel werden gecontroleerd en goedgekeurd door de typische beveiligings evaluatie processen van uw organisatie.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: monitor voor niet-goedgekeurde Azure-resources

**Hulp**: gebruik Azure Policy om beperkingen toe te voegen voor het type resources dat kan worden gemaakt in uw abonnementen.

Zoek en ontdek resources met Azure-resource grafiek in abonnementen. 

Zorg ervoor dat alle Azure-resources die in de omgeving aanwezig zijn, zijn goedgekeurd.

- [Azure Policy configureren en beheren](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Query's maken met Azure Resource Graph Explorer](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: de mogelijkheid van gebruikers om te communiceren met Azure Resource Manager beperken

**Hulp**: gebruik de voorwaardelijke toegang van Azure AD om de interactie van gebruikers met Azure Resource Manager te beperken door ' blok toegang ' te configureren voor de app Microsoft Azure management.

- [Voorwaardelijke toegang configureren om de toegang tot Azure-bronnen beheer te blok keren](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: toepassingen met een hoog risico fysiek of logisch scheiden

**Richt lijnen**: software die vereist is voor bedrijfs activiteiten, maar die een hoger risico voor de organisatie kan opleveren, moet worden geïsoleerd binnen een eigen virtuele machine en/of virtueel netwerk en voldoende beveiligd zijn met een Azure firewall of een netwerk beveiligings groep.

- [Een virtueel netwerk maken](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

- [Een netwerk beveiligings groep maken met een beveiligings configuratie](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

## <a name="secure-configuration"></a>Veilige configuratie

*Zie [Azure Security Bench Mark: Secure Configuration](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration)(Engelstalig) voor meer informatie.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: veilige configuraties instellen voor alle Azure-resources

**Hulp**: gebruik Azure Policy aliassen om aangepaste beleids regels te maken om de configuratie van uw Azure-resources te controleren of af te dwingen. Gebruik ingebouwde Azure Policy definities.

Azure Resource Manager kunt de sjabloon in JavaScript Object Notation (JSON) exporteren, die moet worden gecontroleerd om ervoor te zorgen dat de configuraties voldoen aan de beveiligings vereisten voor uw organisatie.

Exporteer Azure Resource Manager sjablonen naar JavaScript Object Notation (JSON)-indelingen en controleer ze regel matig om er zeker van te zijn dat de configuraties voldoen aan de beveiligings vereisten van uw organisatie. 

Implementeer aanbevelingen van Security Center als een veilige configuratie basislijn voor uw Azure-resources. 

- [Beschik bare Azure Policy aliassen weer geven](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [Zelfstudie: Beleidsregels voor het afdwingen van naleving maken en beheren](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Eén en meerdere resources exporteren naar een sjabloon in Azure Portal](https://docs.microsoft.com/azure/azure-resource-manager/templates/export-template-portal)

- [Aanbevelingen voor beveiliging: een naslaggids](https://docs.microsoft.com/azure/security-center/recommendations-reference)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: Beveilig Azure-resource configuraties onderhouden

**Hulp**: gebruik Azure Policy [deny] en [implementeren indien niet aanwezig] voor het afdwingen van beveiligde instellingen voor uw Azure-resources.  U kunt ook Azure Resource Manager sjablonen gebruiken om de beveiligings configuratie van uw Azure-resources te onderhouden die uw organisatie nodig heeft. 

- [Azure Policy effecten begrijpen](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

- [Beleidsregels voor het afdwingen van naleving maken en beheren](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Overzicht van Azure Resource Manager sjablonen](https://docs.microsoft.com/azure/azure-resource-manager/templates/overview)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: de configuratie van Azure-resources veilig opslaan

**Hulp**: Azure DevOps gebruiken om uw code veilig op te slaan en te beheren, zoals aangepaste Azure Policy definities, Azure Resource Manager sjablonen en desired state Configuration-scripts. 

Machtigingen verlenen of weigeren voor specifieke gebruikers, ingebouwde beveiligings groepen of groepen die zijn gedefinieerd in Azure Active Directory (Azure AD) als deze is geïntegreerd met Azure DevOps of in Active Directory als deze zijn geïntegreerd met TFS.

- [Code opslaan in azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Over machtigingen en groepen in azure DevOps](https://docs.microsoft.com/azure/devops/organizations/security/about-permissions)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: hulpprogram ma's voor configuratie beheer voor Azure-resources implementeren

**Richt lijnen**: standaard beveiligings configuraties voor Azure-resources definiëren en implementeren met behulp van Azure Policy.  Gebruik Azure Policy aliassen om aangepaste beleids regels te maken om de netwerk configuratie van uw Azure-resources te controleren of af te dwingen. Implementeer ingebouwde beleids definities die betrekking hebben op uw specifieke Azure Load Balancer-resources.  Gebruik Azure Automation ook om configuratie wijzigingen te implementeren. aan uw Azure Load Balancer-resources.

- [Azure Policy configureren en beheren](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Aliassen gebruiken](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: geautomatiseerde configuratie bewaking voor Azure-resources implementeren

**Hulp**: gebruik Security Center om basislijn scans uit te voeren voor uw Azure-Resources en Azure Policy om waarschuwingen en resource configuraties te controleren.

- [Aanbevelingen herstellen in Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

## <a name="incident-response"></a>Reageren op incidenten

*Zie [Azure Security Bench Mark: Incident Response](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response)(Engelstalig) voor meer informatie.*

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: een beoordelings procedure voor incidenten en prioriteits procedures maken

**Hulp**: Security Center wijst aan elke waarschuwing een Ernst toe om u te helpen bepalen welke waarschuwingen het eerst moeten worden onderzocht. 

De ernst is gebaseerd op de manier waarop vertrouwen Security Center is in de zoek actie of de analyse die wordt gebruikt om de waarschuwing te geven, evenals het betrouwbaarheids niveau waarvan er sprake is van schadelijke opzet achter de activiteit die tot de waarschuwing heeft geleid.

Markeer abonnementen met tags en maak een naamgevings systeem voor het identificeren en categoriseren van Azure-resources, met name voor de verwerking van gevoelige gegevens.  

Het is uw verantwoordelijkheid om prioriteit te geven aan het herstel van waarschuwingen op basis van de ernst van de Azure-resources en-omgeving waar het incident heeft plaatsgevonden.

- [Beveiligingswaarschuwingen in Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-alerts-overview)

- [Tags gebruiken om Azure-resources te organiseren](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: beveiligings waarschuwingen opnemen in uw reactie systeem van uw incident

**Hulp**: exporteer uw Security Center waarschuwingen en aanbevelingen met behulp van de functie continue export om Risico's voor Azure-resources te identificeren. 

Gebruik de functie continue export in Security Center waarmee u waarschuwingen en aanbevelingen hand matig of op doorlopende wijze kunt exporteren. 

Gebruik de Security Center Data Connector om de waarschuwingen naar Azure Sentinel te streamen.

- [Continue export configureren](https://docs.microsoft.com/azure/security-center/continuous-export)

- [Waarschuwingen streamen naar Azure Sentinel](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: de reactie op beveiligings waarschuwingen automatiseren

**Hulp**: gebruik de functie werk stroom automatisering in Security Center om automatisch reacties op beveiligings waarschuwingen en aanbevelingen voor het beveiligen van uw Azure-resources te activeren.

- [Werk stroom automatisering configureren in Security ENTER](https://docs.microsoft.com/azure/security-center/workflow-automation)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

## <a name="penetration-tests-and-red-team-exercises"></a>Penetratietests en Red Team-oefeningen

*Zie [Azure Security Bench Mark: Indringings tests en rode team oefeningen](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises)voor meer informatie.*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: voert regel matig indringings tests van uw Azure-resources uit en zorgt voor herbemiddeling van alle essentiële beveiligings resultaten

**Richt lijnen**: Volg de stappen voor het testen van de Microsoft Cloud indringings regels om ervoor te zorgen dat uw indringings tests niet worden geschonden door het micro soft-beleid. Gebruik de strategie van micro soft en de uitvoering van de implementatie van de indringing van een live site in de Cloud, services en toepassingen die door micro soft worden beheerd. 

- [Regels van betrokkenheid voor het testen van indringing](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud rode teams](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: gedeeld

## <a name="next-steps"></a>Volgende stappen

- Zie de [Azure Security-Bench Mark](/azure/security/benchmarks/overview)
- Meer informatie over [Azure-beveiligings basislijnen](/azure/security/benchmarks/security-baselines-overview)
