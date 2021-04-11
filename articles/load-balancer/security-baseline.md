---
title: Azure-beveiligings basislijn voor Azure Load Balancer
description: De Azure Load Balancer Security Baseline voorziet in procedure richtlijnen en resources voor het implementeren van de beveiligings aanbevelingen die zijn opgegeven in de Azure Security-Bench Mark.
author: msmbaldwin
ms.service: load-balancer
ms.topic: conceptual
ms.date: 03/16/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: bffc9eb3e75dda2b04ad4118d1f599f85a0013c2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104590137"
---
# <a name="azure-security-baseline-for-azure-load-balancer"></a>Azure-beveiligings basislijn voor Azure Load Balancer

In deze beveiligings basislijn worden richt lijnen van de [Azure Security Bench Mark-versie 1,0](../security/benchmarks/overview-v1.md) Microsoft Azure Load Balancer. De Azure Security-benchmark biedt aanbevelingen voor hoe u uw cloudoplossingen in Azure kunt beveiligen.
De inhoud wordt gegroepeerd op de **beveiligings controles** die zijn gedefinieerd door de Azure Security-benchmark en de bijbehorende richt lijnen die van toepassing zijn op Azure Load Balancer. **Besturings elementen** die niet van toepassing zijn op Azure Load Balancer, zijn uitgesloten.

 
Als u wilt zien hoe Azure Load Balancer volledig is toegewezen aan de beveiligings benchmark van Azure, raadpleegt u het [volledige Azure Load Balancer beveiligings basislijn toewijzings bestand](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Netwerkbeveiliging

*Zie [Azure Security Benchmark: netwerkbeveiliging](../security/benchmarks/security-control-network-security.md) voor meer informatie.*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Azure-resources in virtuele netwerken beveiligen

**Richt lijnen**: gebruik interne Azure load balancers om alleen verkeer toe te staan van back-end-bronnen vanuit bepaalde virtuele netwerken of gekoppelde virtuele netwerken zonder bloot stelling aan Internet. Een extern Load Balancer implementeren met bron netwerk

Adres omzetting (SNAT) voor het maskeren van de IP-adressen van back-end-bronnen voor beveiliging tegen rechtstreekse Internet belichting.

Azure biedt twee soorten Load Balancer-aanbiedingen, Standard en Basic. Gebruik de Standard Load Balancer voor alle productie werkbelastingen. Implementeer netwerk beveiligings groepen en sta alleen toegang toe tot de vertrouwde poorten en IP-adresbereiken van uw toepassing. Als er geen netwerk beveiligings groep is toegewezen aan het back-end-subnet of de NIC van de virtuele machine van de back-end, is verkeer niet toegestaan voor deze resources van de load balancer. Met standaard load balancers kunt u uitgaande regels opgeven voor het definiëren van uitgaande NAT met een netwerk beveiligings groep. Bekijk deze uitgaande regels om het gedrag van uw uitgaande verbindingen af te stemmen.

Het gebruik van een Standard Load Balancer wordt aanbevolen voor uw productie werkbelastingen en normaal gesp roken wordt de basis Load Balancer alleen gebruikt voor het testen omdat het basis type standaard open is voor verbindingen van het internet en geen netwerk beveiligings groepen voor bewerking vereist.

- [Uitgaande verbindingen in Azure](load-balancer-outbound-connections.md)

- [Open bare Azure-Load Balancer bijwerken](upgrade-basic-standard.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: de [Security Bench Mark van Azure](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) is het standaard beleids initiatief voor Security Center en is de basis voor de [aanbevelingen van Security Center](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md). De Azure Policy definities die aan dit besturings element zijn gerelateerd, worden automatisch door Security Center ingeschakeld. Voor waarschuwingen met betrekking tot dit besturings element is mogelijk een [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) -plan vereist voor de gerelateerde services.

**Ingebouwde definities Azure Policy-micro soft. Network**:

[!INCLUDE [Resource Policy for Microsoft.Network 1.1](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1,2: de configuratie en het verkeer van virtuele netwerken, subnetten en Nic's bewaken en vastleggen

**Hulp**: de Load Balancer is een Pass Through-service, omdat deze afhankelijk is van de regels voor netwerk beveiligings groepen die worden toegepast op back-end-bronnen en de geconfigureerde regels voor uitgaande verbindingen om Internet toegang te beheren.

Controleer de uitgaande regels die zijn geconfigureerd voor uw Standard Load Balancer via de Blade uitgaande regels van uw Load Balancer en de Blade taakverdelings regels waar u mogelijk impliciete uitgaande regels hebt ingeschakeld.

Bewaak het aantal van uw uitgaande verbindingen om bij te houden hoe vaak uw resources met internet zijn verbonden. 

Gebruik Security Center en volg de aanbevelingen voor netwerk beveiliging om uw Azure-netwerk bronnen te beveiligen.

Volg de aanbevelingen voor beveiliging voor uw back-endservers en schakel logboeken stroom voor netwerk beveiligings groepen in en verzend de logboeken naar een Azure Storage account voor controle.

U kunt de stroom logboeken ook naar een Log Analytics-werk ruimte verzenden en vervolgens Traffic Analytics gebruiken om inzicht te krijgen in verkeers patronen in uw Azure-Cloud. Voor delen van Traffic Analytics zijn onder andere de mogelijkheid om netwerk activiteiten te visualiseren, HOTS pots en beveiligings Risico's te identificeren, verkeers patronen te begrijpen en netwerk configuraties te lokaliseren.

- [Stroom logboeken van netwerk beveiligings groepen inschakelen](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Traffic Analytics inschakelen en gebruiken](../network-watcher/traffic-analytics.md)

- [Informatie over de netwerk beveiliging die wordt verschaft door Azure Security Center](../security-center/security-center-network-recommendations.md)

- [De statistieken van mijn uitgaande verbinding Hoe kan ik controleren](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics#how-do-i-check-my-outbound-connection-statistics)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: de [Security Bench Mark van Azure](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) is het standaard beleids initiatief voor Security Center en is de basis voor de [aanbevelingen van Security Center](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md). De Azure Policy definities die aan dit besturings element zijn gerelateerd, worden automatisch door Security Center ingeschakeld. Voor waarschuwingen met betrekking tot dit besturings element is mogelijk een [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) -plan vereist voor de gerelateerde services.

**Ingebouwde definities Azure Policy-micro soft. Network**:

[!INCLUDE [Resource Policy for Microsoft.Network 1.2](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-2.md)]

### <a name="13-protect-critical-web-applications"></a>1,3: essentiële webtoepassingen beveiligen

**Richt lijnen**: Definieer expliciet Internet connectiviteit en geldige bron-ip's via uitgaande regels en netwerk beveiligings groepen met uw Load Balancer om de bedreigings informatie van micro soft te gebruiken voor het beveiligen van uw webtoepassingen.

- [De Azure Firewall integreren](../firewall/integrate-lb.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

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
 

- [Azure DDoS Protection Standard beheren met de Azure Portal](../ddos-protection/manage-ddos-protection.md)

- [Azure Firewall op bedreigingen gebaseerd filteren](../firewall/threat-intel.md)

- [Bescherming tegen bedreiging in Azure Security Center](../security-center/azure-defender.md)

- [Beheerpoorten beveiligen met just-in-time-toegang](../security-center/security-center-just-in-time.md)

- [Adaptieve netwerk beveiliging in Azure Security Center](../security-center/security-center-adaptive-network-hardening.md)

- [Azure Firewall met uw Load Balancer integreren](../firewall/overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: de [Security Bench Mark van Azure](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) is het standaard beleids initiatief voor Security Center en is de basis voor de [aanbevelingen van Security Center](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md). De Azure Policy definities die aan dit besturings element zijn gerelateerd, worden automatisch door Security Center ingeschakeld. Voor waarschuwingen met betrekking tot dit besturings element is mogelijk een [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) -plan vereist voor de gerelateerde services.

**Ingebouwde definities Azure Policy-micro soft. Network**:

[!INCLUDE [Resource Policy for Microsoft.Network 1.4](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-4.md)]

### <a name="15-record-network-packets"></a>1,5: netwerk pakketten opnemen

**Hulp**: Schakel Network Watcher pakket vastleggen in om afwijkende activiteiten te onderzoeken.

- [Een Network Watcher-exemplaar maken](../network-watcher/network-watcher-create.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: de [Security Bench Mark van Azure](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) is het standaard beleids initiatief voor Security Center en is de basis voor de [aanbevelingen van Security Center](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md). De Azure Policy definities die aan dit besturings element zijn gerelateerd, worden automatisch door Security Center ingeschakeld. Voor waarschuwingen met betrekking tot dit besturings element is mogelijk een [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) -plan vereist voor de gerelateerde services.

**Ingebouwde definities Azure Policy-micro soft. Network**:

[!INCLUDE [Resource Policy for Microsoft.Network 1.5](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-5.md)]

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: op netwerk gebaseerde inbreuk detectie/indringings systemen (ID'S/IP-adressen) implementeren

**Richt lijnen**: Implementeer een aanbieding vanuit de Azure Marketplace die ondersteuning biedt voor ID'S/IP-adressen met Payload-inspectie mogelijkheden voor de omgeving van uw Load Balancer. 

Gebruik Azure Firewall bedreigings informatie als Payload inspectie geen vereiste is. Azure Firewall op bedreigingen gebaseerd filteren wordt gebruikt om het verkeer van en naar bekende schadelijke IP-adressen en domeinen te Signa lering en/of te blok keren. De IP-adressen en domeinen zijn afkomstig van de Microsoft Bedreigingsinformatie-feed.

Implementeer de door u gewenste firewall oplossing op elk van de netwerk grenzen van uw organisatie om schadelijk verkeer te detecteren en/of te blok keren.

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

- [Azure Firewall implementeren](../firewall/tutorial-firewall-deploy-portal.md)

- [Waarschuwingen configureren met Azure Firewall](../firewall/threat-intel.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="17-manage-traffic-to-web-applications"></a>1,7: verkeer naar webtoepassingen beheren

**Richt lijnen**: Definieer expliciet Internet connectiviteit en geldige bron-ip's via uitgaande regels en netwerk beveiligings groepen met uw Load Balancer om de Threat Intelligence-functies van micro soft te gebruiken voor het beveiligen van uw webtoepassingen.

- [De Azure Firewall integreren](../firewall/integrate-lb.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: de complexiteit en administratieve overhead van netwerk beveiligings regels minimaliseren

**Hulp**: gebruik service tags in plaats van specifieke IP-adressen bij het maken van beveiligings regels. Geef de servicetag naam op in het bron-of doel veld van een regel om het verkeer voor de bijbehorende service toe te staan of te weigeren. 

Micro soft beheert de adres voorvoegsels die zijn opgenomen in het servicetag van de service en werkt de servicetag automatisch bij met gewijzigde adressen. 

Standaard bevat elke netwerk beveiligings groep de service label AzureLoadBalancer om verkeer met de status test toe te staan. 

Raadpleeg de Azure-documentatie voor alle service tags die beschikbaar zijn voor gebruik in regels voor netwerk beveiligings groepen.

- [Beschik bare service Tags](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#available-service-tags)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: standaard beveiligings configuraties voor netwerk apparaten onderhouden

**Richt lijnen**: standaard beveiligings configuraties voor netwerk bronnen definiëren en implementeren met Azure Policy.

Gebruik Azure-blauw drukken om grootschalige Azure-implementaties te vereenvoudigen door sleutel omgevings artefacten, zoals Azure Resources Manager-sjablonen, Azure RBAC-besturings elementen en-beleid, in één blauw druk te definiëren. 

De blauw druk Toep assen op nieuwe abonnementen en het beheer en het beheer nauw keuriger maken.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Voor beelden Azure Policy voor netwerken](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network)

- [Een Azure Blueprint maken](../governance/blueprints/create-blueprint-portal.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="110-document-traffic-configuration-rules"></a>1,10: configuratie regels voor het document verkeer

**Richt lijnen**: gebruik resource Tags voor netwerk beveiligings groepen en andere bronnen die betrekking hebben op netwerk beveiliging en verkeers stroom. 

Gebruik het veld Beschrijving om de regels te documenteren die verkeer naar of van een netwerk toestaan voor afzonderlijke regels voor netwerk beveiligings groepen.

Implementeer een van de ingebouwde Azure Policy definities met betrekking tot labeling, zoals ' vereist label en de bijbehorende waarde ', waarmee wordt gegarandeerd dat alle resources worden gemaakt met tags en op de hoogte moeten zijn van bestaande niet-gelabelde resources.

Gebruik Azure PowerShell of Azure CLI om op basis van hun labels acties op te zoeken of uit te voeren op resources.

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

- [Een Azure-Virtual Network maken](../virtual-network/quick-create-portal.md)

- [Netwerk verkeer filteren met regels voor netwerk beveiligings groepen](../virtual-network/tutorial-filter-network-traffic.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: gebruik automatische hulpprogram ma's om netwerk bron configuraties te bewaken en wijzigingen te detecteren

**Hulp**: Azure-activiteiten logboek gebruiken om resource configuraties te bewaken en wijzigingen in uw Azure-resources te detecteren. 

Maak waarschuwingen in Azure Monitor om u te waarschuwen wanneer kritieke resources worden gewijzigd.

- [Activiteiten logboek gebeurtenissen van Azure weer geven en ophalen](https://docs.microsoft.com/azure/azure-monitor/essentials/activity-log#view-the-activity-log)

- [Waarschuwingen maken in Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="logging-and-monitoring"></a>Logboekregistratie en bewaking

*Zie [Azure Security Bench Mark: Logging and monitoring](../security/benchmarks/security-control-logging-monitoring.md)(Engelstalig) voor meer informatie.*

### <a name="22-configure-central-security-log-management"></a>2,2: Centraal beveiligings logboek beheer configureren

**Hulp**: Bekijk de wijzigingen in de regels voor uitgaande verbindingen en netwerk beveiligings groepen voor uw load balancers door het activiteiten logboek in uw abonnementen weer te geven. 

Bekijk de interne logboeken van de host om te controleren of uw back-end-bronnen veilig zijn.

Deze logboeken exporteren naar Log Analytics of een ander opslag platform. Gebruik in Azure Monitor Log Analytics-werk ruimten om analyses uit te voeren en te uitvoeren en gebruik Azure Storage accounts voor lange termijn-en archiverings opslag.

Deze gegevens in-of uitschakelen voor Azure Sentinel of een SIEM van derden op basis van uw zakelijke vereisten voor uw organisatie.

- [Azure-Sentinel onboarden](../sentinel/quickstart-onboard.md)

- [Platform logboeken en metrische gegevens verzamelen met Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md)

- [Interne host-logboeken van de virtuele machine van Azure verzamelen met Azure Monitor](../azure-monitor/vm/quick-collect-azurevm.md)

- [Aan de slag met Azure Monitor en integratie van SIEM van derden](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

- [Activiteiten logboeken van platform](../azure-monitor/essentials/activity-log.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

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

- [Lees dit artikel met stapsgewijze instructies voor elke methode die wordt beschreven in de audit bewerkingen met Resource Manager](../azure-resource-manager/management/view-activity-logs.md)

- [Azure Monitor-logboeken voor openbare Basic Azure Load Balancer](load-balancer-monitor-log.md)

- [Activiteiten logboeken weer geven om acties op resources te controleren](../azure-resource-manager/management/view-activity-logs.md)

- [Via Api's multi-dimensionale metrieken ophalen](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics#retrieve-multi-dimensional-metrics-programmatically-via-apis)

- [Aan de slag met Azure Monitor en integratie van SIEM van derden](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="25-configure-security-log-storage-retention"></a>2,5: Bewaar beveiliging van het beveiligings logboek configureren

**Hulp**: het activiteiten logboek is standaard ingeschakeld en blijft 90 dagen geldig in de gebeurtenis logboeken van Azure. Stel de Bewaar periode voor uw Log Analytics-werk ruimte in op basis van de nalevings voorschriften van uw organisatie in Azure Monitor. Gebruik Azure Storage accounts voor lange termijn-en archiverings opslag.

- [Activiteiten logboeken weer geven voor het bewaken van acties op het artikel resources](../azure-resource-manager/management/view-activity-logs.md)

- [De Bewaar periode voor gegevens wijzigen in Log Analytics](https://docs.microsoft.com/azure/azure-monitor/logs/manage-cost-storage#change-the-data-retention-period)

- [Bewaar beleid configureren voor logboeken van Azure Storage-account](https://docs.microsoft.com/azure/storage/common/manage-storage-analytics-logs#configure-logging)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="26-monitor-and-review-logs"></a>2,6: Logboeken bewaken en controleren

**Richt lijnen**: Standard Load Balancer resources controleren, beheren en problemen oplossen met behulp van de Load Balancer pagina in de Azure Portal en de resource Health pagina onder Azure monitor. Beschik bare metrische gegevens voor beveiliging zijn onder meer informatie over de bron netwerk adres omzetting (SNAT)-verbindingen, poorten. Daarnaast zijn er ook metrische gegevens over SYN-pakketten (Synchronize) en pakket tellers beschikbaar. 

Gebruik Azure Monitor om de status van het eind punt status te controleren met multi-dimensionale metrische gegevens voor standaard, externe en interne, load balancers. Verzamel deze metrische gegevens via een programma via Api's en naar een opslag account geschreven via de optie ' alle metrische gegevens '.

Azure Monitor-logboeken zijn niet beschikbaar voor interne load balancers van de Basic. 

Gebruik Azure Monitor om de status van de status test per back-end-pool te bekijken voor de eenvoudige externe Load Balancer, zoals het aantal exemplaren in uw back-end-pool dat geen aanvragen van de Load Balancer ontvangt als gevolg van fouten in de status test. 

Implementeer logboek registratie met Azure Operational Insights om statistieken te geven over load balancer status. 

Gebruik de activiteiten Logboeken om waarschuwingen weer te geven en acties te controleren op resources en hun status in uw Azure-abonnementen. Activiteiten logboeken zijn standaard ingeschakeld en kunnen worden weer gegeven in de Azure Portal. 

Gebruik micro soft Power BI met het inhouds pakket voor Azure audit logs en analyseer uw gegevens met vooraf geconfigureerde Dash boards. Pas de weer gaven aan uw vereisten aan op basis van de bedrijfs behoeften. 

Stream-logboeken naar een Event Hub-of Log Analytics-werk ruimte. Ze kunnen ook worden geëxtraheerd uit Azure Blob-opslag en worden weer gegeven in verschillende hulpprogram ma's, zoals Excel en Power BI. U kunt gegevens in-en inschakelen voor Azure Sentinel of een SIEM van derden.

- [Statustesten voor Load Balancer](load-balancer-custom-probe-overview.md)

- [Azure Monitor REST API](/rest/api/monitor)

- [Metrische gegevens ophalen via REST API](/rest/api/monitor/metrics/list)

- [Diagnostische gegevens Standard Load Balancer met metrische gegevens, waarschuwingen en resource status](load-balancer-standard-diagnostics.md)

- [Azure Monitor-logboeken voor openbare Basic Azure Load Balancer](load-balancer-monitor-log.md)

- [Uw load balancer metrische gegevens weer geven in de Azure Portal](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics#view-your-load-balancer-metrics-in-the-azure-portal)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: waarschuwingen inschakelen voor afwijkende activiteiten

**Hulp**: gebruik Security Center met log Analytics werk ruimte voor het bewaken en waarschuwen over afwijkende activiteiten die betrekking hebben op Load Balancer in beveiligings logboeken en-gebeurtenissen.

Gegevens in-en uitschakelen voor Azure Sentinel of een SIEM-hulp programma van derden.

- [Azure-Sentinel onboarden](../sentinel/quickstart-onboard.md)

- [Waarschuwingen beheren in Azure Security Center](../security-center/security-center-managing-and-responding-alerts.md)

- [Een waarschuwing over logboek gegevens van log Analytics](../azure-monitor/alerts/tutorial-response.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="identity-and-access-control"></a>Identiteits- en toegangsbeheer

*Zie [Azure Security Bench Mark: Identity and Access Control](../security/benchmarks/security-control-identity-access-control.md)voor meer informatie.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: een inventaris van beheerders accounts onderhouden

**Richt lijnen**: met Azure op rollen gebaseerd toegangs beheer (Azure RBAC) kunt u de toegang tot Azure-resources, zoals uw Load Balancer, beheren via roltoewijzingen. Wijs deze rollen toe aan gebruikers, groeperingen van service-principals en beheerde identiteiten.

Vooraf gedefinieerde en ingebouwde rollen inventariseren voor bepaalde resources met hulpprogram ma's als Azure CLI, Azure PowerShell of de Azure Portal.

- [Een directory-rol verkrijgen in Azure Active Directory (Azure AD) met Power shell](/powershell/module/azuread/get-azureaddirectoryrole)

- [Leden van een directory-rol in azure AD ophalen met Power shell](/powershell/module/azuread/get-azureaddirectoryrolemember)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: multi-factor Authentication gebruiken voor alle op Azure Active Directory gebaseerde toegang

**Hulp**: Schakel Azure Active Directory (Azure AD) multi-factor Authentication in en volg de aanbevelingen voor identiteits-en toegangs beheer van Security Center.

- [Multi-factor Authentication inschakelen in azure](../active-directory/authentication/howto-mfa-getstarted.md) 

- [Identiteit en toegang bewaken in Azure Security Center](../security-center/security-center-identity-access.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: gebruik speciale machines (privileged Access workstations) voor alle beheer taken

**Hulp**: gebruik paw (privileged Access workstations) met multi-factor Authentication die is geconfigureerd voor het beheren en openen van Azure-netwerk bronnen. 

- [Meer informatie over privileged Access workstations](/security/compass/privileged-access-devices)

- [Multi-factor Authentication inschakelen in azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="38-manage-azure-resources-only-from-approved-locations"></a>3,8: beheer Azure-resources alleen op goedgekeurde locaties

**Hulp**: gebruik benoemde locaties voor voorwaardelijke toegang om alleen toegang toe te staan vanaf specifieke logische groepen met IP-adresbereiken of landen/regio's.

- [Benoemde locaties configureren in azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="39-use-azure-active-directory"></a>3,9: Azure Active Directory gebruiken

**Hulp**: gebruik Azure Active Directory (Azure AD) als centrale verificatie-en autorisatie systeem voor uw services. Azure AD beveiligt gegevens door gebruik te maken van sterke code ring voor gegevens in rust en onderweg, met zouten, hashes en het veilig opslaan van gebruikers referenties.  

- [Een Azure AD-instantie maken en configureren](../active-directory-domain-services/tutorial-create-instance.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: regel matig gebruikers toegang controleren en afstemmen

**Hulp**: gebruik Azure Active Directory (Azure AD) om logboeken te maken waarmee u verlopen accounts kunt detecteren. 

Azure Identity Access revisies kan worden uitgevoerd om groepslid maatschappen, toegang tot bedrijfs toepassingen en roltoewijzingen op efficiënte wijze te beheren. Gebruikers toegang moet regel matig worden gecontroleerd om ervoor te zorgen dat alleen de actieve gebruikers de toegang blijven hebben.

- [Meer informatie over Azure AD-rapportage](/azure/active-directory/reports-monitoring/)

- [Beoordelingen over Azure Identity Access gebruiken](../active-directory/governance/access-reviews-overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: controle pogingen om toegang te krijgen tot gedeactiveerde referenties

**Hulp**: Integreer Azure Active Directory (Azure AD)-aanmeldings activiteiten, controle en risico gebeurtenis logboek bronnen, met elk Siem of bewakings programma op basis van uw toegang.

Stroom lijn dit proces door Diagnostische instellingen voor Azure AD-gebruikers accounts te maken en de audit logboeken en aanmeldings logboeken te verzenden naar een Log Analytics-werk ruimte. Alle gewenste waarschuwingen kunnen worden geconfigureerd in Log Analytics werk ruimte.

- [Azure-activiteitenlogboeken integreren in Azure Monitor](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: waarschuwing voor de afwijking van het aanmeldings gedrag van accounts

**Hulp**: gebruik Azure Active Directory (Azure AD) de functies risico-en identiteits beveiliging om automatische antwoorden te configureren op gedetecteerde verdachte acties met betrekking tot gebruikers identiteiten. Gegevens opnemen in azure Sentinel voor verdere onderzoeken.

- [Riskante Azure AD-aanmeldingen weergeven](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [Risico beleid voor identiteits beveiliging configureren en inschakelen](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Azure-Sentinel onboarden](../sentinel/quickstart-onboard.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="data-protection"></a>Gegevensbeveiliging

*Zie [Azure Security Benchmark: gegevensbescherming](../security/benchmarks/security-control-data-protection.md) voor meer informatie.*

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: alle gevoelige gegevens in de overdracht versleutelen

**Richt lijnen**: Zorg ervoor dat clients die verbinding maken met uw Azure-resources, TLS 1,2 of hoger kunnen onderhandelen.

Volg Azure Security Center aanbevelingen voor het versleutelen van de rest en de versleuteling in de door Voer, indien van toepassing.

- [Meer informatie over versleuteling in transit met Azure](https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4,6: Azure RBAC gebruiken om de toegang tot resources te beheren

**Hulp**: Azure RBAC gebruiken om de toegang tot uw Load Balancer-resources te beheren.

- [Azure RBAC configureren](../role-based-access-control/role-assignments-portal.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: voor komen dat gegevens verlies op basis van host wordt gebruikt voor het afdwingen van toegangs beheer

**Hulp**: Load Balancer is een Pass Through-service die geen klant gegevens opslaat. Het maakt deel uit van het onderliggende platform dat door micro soft wordt beheerd. 

Micro soft behandelt alle inhoud van de klant als vertrouwelijk en gaat naar uitstekende lengtes om het verlies en de bloot stelling van klant gegevens te beschermen. 

Om ervoor te zorgen dat klant gegevens in azure veilig blijven, heeft micro soft een reeks robuuste besturings elementen en mogelijkheden voor gegevens bescherming geïmplementeerd en onderhouden. 

- [Informatie over beveiliging van klantgegevens in Azure](../security/fundamentals/protection-customer-data.md)

**Verantwoordelijkheid**: Gedeeld

**Azure Security Center bewaking**: geen

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: wijzigingen in essentiële Azure-resources vastleggen en waarschuwen

**Hulp**: gebruik Azure monitor in combi natie met het Azure-activiteiten logboek om waarschuwingen te maken wanneer wijzigingen worden aangebracht in essentiële Azure-resources, zoals load balancers die worden gebruikt voor belang rijke productie werkbelastingen.

- [Waarschuwingen maken voor gebeurtenissen in het Azure-activiteiten logboek](../azure-monitor/alerts/alerts-activity-log.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="inventory-and-asset-management"></a>Inventarisatie en Asset Management

*Zie [Azure Security Bench Mark: Inventory and Asset Management](../security/benchmarks/security-control-inventory-asset-management.md)voor meer informatie.*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: automatische Asset-detectie oplossing gebruiken

**Hulp**: Azure resource Graph gebruiken om alle resources (zoals compute, opslag, netwerk, poorten, protocollen enzovoort) in uw abonnementen op te vragen en te detecteren. Azure Resource Manager wordt aanbevolen om huidige resources te maken en te gebruiken.

Zorg ervoor dat u de juiste (Lees) machtigingen in uw Tenant hebt en alle Azure-abonnementen en-resources in uw abonnementen hebt geïnventariseerd.

- [Query's maken met Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

- [Uw Azure-abonnementen weer geven](/powershell/module/az.accounts/get-azsubscription)

- [Meer informatie over Azure RBAC](../role-based-access-control/overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="62-maintain-asset-metadata"></a>6,2: meta gegevens van activa onderhouden

**Richt lijnen**: Tags Toep assen op Azure-resources met meta gegevens om te organiseren op logische wijze volgens een taxonomie.

- [Tags maken en gebruiken](/azure/azure-resource-manager/resource-group-using-tags)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: niet-geautoriseerde Azure-resources verwijderen

**Richt lijnen**: Gebruik labels, beheer groepen en afzonderlijke abonnementen, indien van toepassing, om assets te organiseren en bij te houden. 

Sluit de inventaris op regel matige basis af en zorg ervoor dat niet-geautoriseerde resources tijdig worden verwijderd uit uw abonnementen.

- [Aanvullende Azure-abonnementen maken](../cost-management-billing/manage/create-subscription.md)

- [Beheer groepen maken](../governance/management-groups/create-management-group-portal.md)

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6,4: een inventaris van goedgekeurde Azure-resources definiëren en onderhouden

**Hulp**: Maak een lijst met goedgekeurde Azure-resources op basis van de behoeften van uw organisatie, die u kunt gebruiken als een allowlist-mechanisme. Hierdoor kan uw organisatie alle pas beschik bare Azure-Services vrijgeven nadat deze formeel werden gecontroleerd en goedgekeurd door de typische beveiligings evaluatie processen van uw organisatie.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: monitor voor niet-goedgekeurde Azure-resources

**Hulp**: gebruik Azure Policy om beperkingen toe te voegen voor het type resources dat kan worden gemaakt in uw abonnementen.

Zoek en ontdek resources met Azure-resource grafiek in abonnementen. 

Zorg ervoor dat alle Azure-resources die in de omgeving aanwezig zijn, zijn goedgekeurd.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Query's maken met Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="69-use-only-approved-azure-services"></a>6,9: alleen goedgekeurde Azure-Services gebruiken

**Hulp: gebruik** Azure Policy om beperkingen toe te voegen voor het type resources dat kan worden gemaakt in klant abonnementen met behulp van de volgende ingebouwde beleids definities:
- Niet toegestane resourcetypen
- Toegestane brontypen

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Een specifiek resource type weigeren met Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

- [Voor beeld van Azure-beleid voor het virtuele netwerk](/azure/virtual-network/policy-samples)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: de mogelijkheid van gebruikers om te communiceren met Azure Resource Manager beperken

**Hulp**: gebruik Azure Active Directory (Azure AD) voorwaardelijke toegang om de mogelijkheid van gebruikers om te communiceren met Azure Resource Manager te beperken door ' toegang blok keren ' te configureren voor de App ' Microsoft Azure beheer '.

- [Voorwaardelijke toegang configureren om de toegang tot Azure-bronnen beheer te blok keren](../role-based-access-control/conditional-access-azure-management.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: toepassingen met een hoog risico fysiek of logisch scheiden

**Richt lijnen**: software die vereist is voor bedrijfs activiteiten, maar die een hoger risico voor de organisatie kan opleveren, moet worden geïsoleerd binnen een eigen virtuele machine en/of virtueel netwerk en voldoende beveiligd zijn met een Azure firewall of een netwerk beveiligings groep.

- [Een virtueel netwerk maken](../virtual-network/quick-create-portal.md)

- [Een netwerk beveiligings groep maken met een beveiligings configuratie](../virtual-network/tutorial-filter-network-traffic.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="secure-configuration"></a>Veilige configuratie

*Zie [Azure Security Bench Mark: Secure Configuration](../security/benchmarks/security-control-secure-configuration.md)(Engelstalig) voor meer informatie.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: veilige configuraties instellen voor alle Azure-resources

**Hulp**: gebruik Azure Policy aliassen om aangepaste beleids regels te maken om de configuratie van uw Azure-resources te controleren of af te dwingen. Gebruik ingebouwde Azure Policy definities.

Azure Resource Manager kunt de sjabloon in JavaScript Object Notation (JSON) exporteren, die moet worden gecontroleerd om ervoor te zorgen dat de configuraties voldoen aan de beveiligings vereisten voor uw organisatie.

Exporteer Azure Resource Manager sjablonen naar JavaScript Object Notation (JSON)-indelingen en controleer ze regel matig om er zeker van te zijn dat de configuraties voldoen aan de beveiligings vereisten van uw organisatie.

Implementeer aanbevelingen van Security Center als een veilige configuratie basislijn voor uw Azure-resources.

- [Beschik bare Azure Policy aliassen weer geven](/powershell/module/az.resources/get-azpolicyalias)

- [Zelfstudie: Beleidsregels voor het afdwingen van naleving maken en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Eén en meerdere resources exporteren naar een sjabloon in Azure Portal](../azure-resource-manager/templates/export-template-portal.md)

- [Aanbevelingen voor beveiliging: een naslaggids](../security-center/recommendations-reference.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: Beveilig Azure-resource configuraties onderhouden

**Hulp**: gebruik Azure Policy [deny] en [implementeren indien niet aanwezig] voor het afdwingen van beveiligde instellingen voor uw Azure-resources.  U kunt ook Azure Resource Manager sjablonen gebruiken om de beveiligings configuratie van uw Azure-resources te onderhouden die uw organisatie nodig heeft. 

- [Azure Policy effecten begrijpen](../governance/policy/concepts/effects.md)

- [Beleidsregels voor het afdwingen van naleving maken en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Overzicht van Azure Resource Manager sjablonen](../azure-resource-manager/templates/overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: de configuratie van Azure-resources veilig opslaan

**Hulp**: Azure DevOps gebruiken om uw code veilig op te slaan en te beheren, zoals aangepaste Azure Policy definities, Azure Resource Manager sjablonen en desired state Configuration-scripts.

Machtigingen verlenen of weigeren voor specifieke gebruikers, ingebouwde beveiligings groepen of groepen die zijn gedefinieerd in Azure Active Directory (Azure AD) als deze is geïntegreerd met Azure DevOps of in azure AD als deze zijn geïntegreerd met TFS.

- [Code opslaan in azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Over machtigingen en groepen in azure DevOps](/azure/devops/organizations/security/about-permissions)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: hulpprogram ma's voor configuratie beheer voor Azure-resources implementeren

**Richt lijnen**: standaard beveiligings configuraties voor Azure-resources definiëren en implementeren met behulp van Azure Policy.  Gebruik Azure Policy aliassen om aangepaste beleids regels te maken om de netwerk configuratie van uw Azure-resources te controleren of af te dwingen. Implementeer ingebouwde beleids definities die betrekking hebben op uw specifieke Azure Load Balancer-resources.  Gebruik Azure Automation ook om configuratie wijzigingen te implementeren. aan uw Azure Load Balancer-resources.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Aliassen gebruiken](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: geautomatiseerde configuratie bewaking voor Azure-resources implementeren

**Hulp**: gebruik Security Center om basislijn scans uit te voeren voor uw Azure-Resources en Azure Policy om waarschuwingen en resource configuraties te controleren.

- [Aanbevelingen herstellen in Azure Security Center](../security-center/security-center-remediate-recommendations.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="incident-response"></a>Reageren op incidenten

*Zie [Azure Security Benchmark: respons op incidenten](../security/benchmarks/security-control-incident-response.md) voor meer informatie.*

### <a name="101-create-an-incident-response-guide"></a>10,1: een hand leiding voor reactie op incidenten maken

**Richtlijnen**: Stel voor uw organisatie een responshandleiding op voor gebruik bij incidenten. Zorg ervoor dat er schriftelijke responsplannen zijn waarin alle rollen van het personeel worden gedefinieerd, evenals alle fasen in het afhandelen/managen van incidenten, vanaf de detectie van het incident tot een evaluatie ervan achteraf. 

- [Werk stroom automatisering configureren in Azure Security Center](../security-center/security-center-planning-and-operations-guide.md) 

- [Richt lijnen voor het bouwen van uw eigen beveiligings incident antwoord proces](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/) 

- [Micro soft Security Response Center anatomie van een incident](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/) 

- [Klant kan ook gebruikmaken van de hand leiding voor de verwerking van het computer beveiligings incident van het NIST om hulp te bieden bij het maken van een eigen incident respons plan](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: een beoordelings procedure voor incidenten en prioriteits procedures maken

**Hulp**: Security Center wijst aan elke waarschuwing een Ernst toe om u te helpen bepalen welke waarschuwingen het eerst moeten worden onderzocht. 

De ernst is gebaseerd op de manier waarop vertrouwen Security Center is in de zoek actie of de analyse die wordt gebruikt om de waarschuwing te geven, evenals het betrouwbaarheids niveau waarvan er sprake is van schadelijke opzet achter de activiteit die tot de waarschuwing heeft geleid.

Markeer abonnementen met tags en maak een naamgevings systeem voor het identificeren en categoriseren van Azure-resources, met name voor de verwerking van gevoelige gegevens.  

Het is uw verantwoordelijkheid om prioriteit te geven aan het oplossen van waarschuwingen op basis van de ernst van de Azure-resources en -omgeving waarin het incident heeft plaatsgevonden.

- [Beveiligingswaarschuwingen in Azure Security Center](../security-center/security-center-alerts-overview.md)

- [Tags gebruiken om Azure-resources te organiseren](../azure-resource-manager/management/tag-resources.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="103-test-security-response-procedures"></a>10,3: procedures voor beveiligings antwoorden testen

**Richt lijnen**: oefent oefeningen uit om de respons mogelijkheden van uw systeem te testen op een reguliere uitgebracht om uw Azure-resources te beschermen. Identificeer zwakke punten en tussen ruimten en wijzig vervolgens uw reactie schema naar wens. 

- [Publicatie van het NIST-hand leiding voor het testen, trainen en uitoefenen van Program Ma's voor IT-plannen en-mogelijkheden](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: contact gegevens van het beveiligings incident opgeven en waarschuwings meldingen configureren voor beveiligings incidenten

**Hulp**: contact gegevens van beveiligings incidenten worden door micro soft gebruikt om contact met u op te nemen als het micro soft Security Response Center (MSRC) detecteert dat uw gegevens zijn geopend door een onrecht matige of niet-gemachtigde partij. Bekijk incidenten na het feit om te controleren of de problemen zijn opgelost. 

- [De Azure Security Center Security-contactpersoon instellen](../security-center/security-center-provide-security-contact-details.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: beveiligings waarschuwingen opnemen in uw reactie systeem van uw incident

**Hulp**: exporteer uw Security Center waarschuwingen en aanbevelingen met behulp van de functie continue export om Risico's voor Azure-resources te identificeren. 

Gebruik de functie continue export in Security Center waarmee u waarschuwingen en aanbevelingen hand matig of op doorlopende wijze kunt exporteren. 

Gebruik de Security Center Data Connector om de waarschuwingen naar Azure Sentinel te streamen.

- [Continue export configureren](../security-center/continuous-export.md)

- [Waarschuwingen streamen naar Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: de reactie op beveiligings waarschuwingen automatiseren

**Hulp**: gebruik de functie werk stroom automatisering in Security Center om automatisch reacties op beveiligings waarschuwingen en aanbevelingen voor het beveiligen van uw Azure-resources te activeren.

- [Werk stroom automatisering configureren in Security ENTER](../security-center/workflow-automation.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="penetration-tests-and-red-team-exercises"></a>Penetratietests en Red Team-oefeningen

*Zie [Azure Security Bench Mark: Indringings tests en rode team oefeningen](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)voor meer informatie.*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: voert regel matig indringings tests van uw Azure-resources uit en zorgt voor herbemiddeling van alle essentiële beveiligings resultaten

**Richt lijnen**: Volg de stappen voor het testen van de Microsoft Cloud indringings regels om ervoor te zorgen dat uw indringings tests niet worden geschonden door het micro soft-beleid. Gebruik de strategie van Microsoft en de uitvoering van Red Teaming-activiteiten, en voer een penetratietest van de live site uit op basis van een infrastructuur, services en toepassingen die door Microsoft worden beheerd. 

- [Regels voor het inzetten van penetratietests](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="next-steps"></a>Volgende stappen

- Zie [Overzicht Azure Security Benchmark V2](/azure/security/benchmarks/overview)
- Meer informatie over [Azure-beveiligingsbasislijnen](/azure/security/benchmarks/security-baselines-overview)
