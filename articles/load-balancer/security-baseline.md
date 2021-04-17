---
title: Azure-beveiligingsbasislijn voor Azure Load Balancer
description: De Azure Load Balancer beveiligingsbasislijn biedt procedurele richtlijnen en resources voor het implementeren van de beveiligingsaanbevelingen die zijn opgegeven in de Azure Security-benchmark.
author: msmbaldwin
ms.service: load-balancer
ms.topic: conceptual
ms.date: 03/16/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 64e0a8cfcaf00c55038fbe5d1cdc7423b681b690
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589258"
---
# <a name="azure-security-baseline-for-azure-load-balancer"></a>Azure-beveiligingsbasislijn voor Azure Load Balancer

Deze beveiligingsbasislijn past richtlijnen van [Azure Security Benchmark versie 1.0 toe](../security/benchmarks/overview-v1.md) op Microsoft Azure Load Balancer. De Azure Security-benchmark biedt aanbevelingen voor hoe u uw cloudoplossingen in Azure kunt beveiligen.
De inhoud wordt gegroepeerd op basis van de **beveiligingscontroles** die zijn gedefinieerd door de Azure Security Benchmark en de gerelateerde richtlijnen die van toepassing zijn op Azure Load Balancer. **Besturingselementen** die niet van Azure Load Balancer zijn uitgesloten.

 
Zie het volledige Azure Load Balancer toewijzingsbestand voor beveiligingsbasislijnen om te zien Azure Load Balancer hoe Azure Load Balancer volledig is toegewezen aan de Azure [Security-benchmark.](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)

## <a name="network-security"></a>Netwerkbeveiliging

*Zie [Azure Security Benchmark: netwerkbeveiliging](../security/benchmarks/security-control-network-security.md) voor meer informatie.*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Azure-resources binnen virtuele netwerken beveiligen

**Richtlijnen:** Gebruik interne Azure Load Balancers om alleen verkeer toe te staan naar back-end-resources vanuit bepaalde virtuele netwerken of virtuele netwerken met peering zonder blootstelling aan internet. Een externe Load Balancer met bronnetwerk

Adresvertaling (SNAT) om de IP-adressen van back-end-resources te maskeren voor bescherming tegen directe blootstelling aan internet.

Azure biedt twee soorten Load Balancer producten: Standard en Basic. Gebruik de Standard Load Balancer voor alle productieworkloads. Implementeert netwerkbeveiligingsgroepen en staat alleen toegang toe tot de vertrouwde poorten en IP-adresbereiken van uw toepassing. In gevallen waarin er geen netwerkbeveiligingsgroep is toegewezen aan het back-endsubnet of de NIC van de virtuele back-endmachines, wordt het verkeer naar deze resources niet toegestaan vanaf de load balancer. Geef met Standard Load Balancers uitgaande regels op voor het definiëren van uitgaande NAT met een netwerkbeveiligingsgroep. Bekijk deze uitgaande regels om het gedrag van uw uitgaande verbindingen af te stemmen.

Het gebruik van een Standard Load Balancer wordt aanbevolen voor uw productieworkloads. Normaal gesproken wordt de Basic Load Balancer alleen gebruikt voor testen, omdat het basistype standaard is geopend voor internetverbindingen en er geen netwerkbeveiligingsgroepen nodig zijn voor gebruik.

- [Uitgaande verbindingen in Azure](load-balancer-outbound-connections.md)

- [Openbare Azure-Load Balancer](upgrade-basic-standard.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** De [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) is het standaardbeleidsinitiatief voor Security Center en is de basis voor de aanbevelingen Security Center van [Security Center.](/azure/security-center/security-center-recommendations) De Azure Policy met betrekking tot dit besturingselement worden automatisch ingeschakeld door Security Center. Voor waarschuwingen met betrekking tot dit besturingselement [is mogelijk](/azure/security-center/azure-defender) een Azure Defender nodig voor de gerelateerde services.

**Azure Policy ingebouwde definities - Microsoft.Network**:

[!INCLUDE [Resource Policy for Microsoft.Network 1.1](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2: De configuratie en het verkeer van virtuele netwerken, subnetten en NIC's bewaken en in een logboek plaatsen

**Richtlijnen:** de Load Balancer is een pass through-service omdat deze afhankelijk is van de regels voor netwerkbeveiligingsgroepen die worden toegepast op back-en-back-en-resources en de geconfigureerde uitgaande regels voor het beheren van internettoegang.

Controleer de uitgaande regels die zijn geconfigureerd voor uw Standard Load Balancer via de blade Uitgaande regels van uw Load Balancer en de blade taakverdelingsregels waar impliciete uitgaande regels mogelijk zijn ingeschakeld.

Controleer het aantal uitgaande verbindingen om bij te houden hoe vaak uw resources verbinding maken met internet. 

Gebruik Security Center en volg de aanbevelingen voor netwerkbeveiliging om uw Azure-netwerkbronnen te beveiligen.

Volg de beveiligingsaanbevelingen voor uw back-en-mailbronnen en schakel stroomlogboeken van netwerkbeveiligingsgroep in en verzend de logboeken naar een Azure Storage-account voor controle.

Verzend ook de stroomlogboeken naar een Log Analytics-werkruimte en gebruik vervolgens Traffic Analytics om inzicht te krijgen in verkeerspatronen in uw Azure-cloud. Voordelen van Traffic Analytics zijn de mogelijkheid om netwerkactiviteit te visualiseren, hotspots en beveiligingsrisico's te identificeren, verkeersstroompatronen te begrijpen en onjuiste netwerkconfiguraties aan te wijzen.

- [Stroomlogboeken voor netwerkbeveiligingsgroep inschakelen](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Het inschakelen en gebruiken van Traffic Analytics](../network-watcher/traffic-analytics.md)

- [Informatie over netwerkbeveiliging die wordt geboden door Azure Security Center](../security-center/security-center-network-recommendations.md)

- [Hoe kan ik statistieken voor uitgaande verbindingen controleren](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics#how-do-i-check-my-outbound-connection-statistics)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** De [Azure Security-benchmark](/azure/governance/policy/samples/azure-security-benchmark) is het standaardbeleidsinitiatief voor Security Center en is de basis voor [Security Center aanbevelingen van Security Center.](/azure/security-center/security-center-recommendations) De Azure Policy met betrekking tot dit besturingselement worden automatisch ingeschakeld door Security Center. Waarschuwingen met betrekking tot dit besturingselement vereisen mogelijk een [Azure Defender](/azure/security-center/azure-defender) voor de gerelateerde services.

**Azure Policy ingebouwde definities - Microsoft.Network:**

[!INCLUDE [Resource Policy for Microsoft.Network 1.2](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-2.md)]

### <a name="13-protect-critical-web-applications"></a>1.3: Kritieke webtoepassingen beveiligen

**Richtlijnen:** definieer de internetverbinding en geldige bron-IP's expliciet via uitgaande regels en netwerkbeveiligingsgroepen met uw Load Balancer om de bedreigingsinformatie van Microsoft te gebruiken voor het beveiligen van uw webtoepassingen.

- [De Azure Firewall](../firewall/integrate-lb.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Communicatie met bekende schadelijke IP-adressen weigeren

**Richtlijnen:** Schakel standaardbeveiliging van Azure Distributed Denial of Service (DDoS) op uw Azure-Virtual Network in om u te beschermen tegen DDoS-aanvallen. 

Implementeer Azure Firewall netwerkgrenzen van de organisatie met filteren op basis van bedreigingsinformatie ingeschakeld en geconfigureerd voor 'Waarschuwen en weigeren' voor schadelijk netwerkverkeer.

 

Gebruik Security Center tegen bedreigingen om communicatie met bekende schadelijke IP-adressen te detecteren. 

De Standard Load Balancer is standaard beveiligd en maakt deel uit van een privé- en geïsoleerde Virtual Network. Deze is gesloten voor inkomende stromen, tenzij deze worden geopend door netwerkbeveiligingsgroepen om toegestaan verkeer expliciet toe te staan en om bekende schadelijke IP-adressen niet toe te staan. Tenzij er een netwerkbeveiligingsgroep op een subnet of NIC van uw virtuele-machineresource achter de Load Balancer bestaat, mag verkeer deze resource niet bereiken. 

Implementeer Azure Firewall aan elk van de netwerkgrenzen van de organisatie met filteren op basis van bedreigingsinformatie ingeschakeld en geconfigureerd op 'Waarschuwen en weigeren' voor schadelijk netwerkverkeer om aanvallen van schadelijke IP-adressen te voorkomen. 

Richtlijnen implementeren om Azure Firewall te integreren met uw Load Balancer.

Gebruik Security Center functies voor beveiliging tegen bedreigingen om communicatie met bekende schadelijke IP-adressen te detecteren. 

Security Center (Standard-laag) biedt Just-In-Time-toegang tot virtuele machines en configureert toegestane bron-IP-adressen om alleen toegang vanaf goedgekeurde IP-adressen/-bereiken toe te staan.
 

Gebruik Security Center adaptieve netwerkbeveiligingsfunctie van Security Center om configuraties van netwerkbeveiligingsgroepen aan te bevelen die poorten en bron-IP's beperken op basis van werkelijk verkeer en bedreigingsinformatie.
 

- [Azure DDoS Protection Standard beheren met behulp van de Azure Portal](../ddos-protection/manage-ddos-protection.md)

- [Azure Firewall filteren op basis van bedreigingsinformatie](../firewall/threat-intel.md)

- [Bescherming tegen bedreiging in Azure Security Center](../security-center/azure-defender.md)

- [Beheerpoorten beveiligen met just-in-time-toegang](../security-center/security-center-just-in-time.md)

- [Adaptieve netwerkharding in Azure Security Center](../security-center/security-center-adaptive-network-hardening.md)

- [Uw Azure Firewall integreren met uw Load Balancer](../firewall/overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** De [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) is het standaardbeleidsinitiatief voor Security Center en is de basis voor de aanbevelingen Security Center van [Security Center.](/azure/security-center/security-center-recommendations) De Azure Policy met betrekking tot dit besturingselement worden automatisch ingeschakeld door Security Center. Voor waarschuwingen met betrekking tot dit besturingselement [is mogelijk](/azure/security-center/azure-defender) een Azure Defender nodig voor de gerelateerde services.

**Azure Policy ingebouwde definities - Microsoft.Network**:

[!INCLUDE [Resource Policy for Microsoft.Network 1.4](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-4.md)]

### <a name="15-record-network-packets"></a>1.5: Netwerkpakketten registreren

**Richtlijnen:** schakel Network Watcher vastleggen van pakketten in om afwijkende activiteiten te onderzoeken.

- [Een Network Watcher maken](../network-watcher/network-watcher-create.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** De [Azure Security-benchmark](/azure/governance/policy/samples/azure-security-benchmark) is het standaardbeleidsinitiatief voor Security Center en is de basis voor [Security Center aanbevelingen van Security Center.](/azure/security-center/security-center-recommendations) De Azure Policy met betrekking tot dit besturingselement worden automatisch ingeschakeld door Security Center. Waarschuwingen met betrekking tot dit besturingselement vereisen mogelijk een [Azure Defender](/azure/security-center/azure-defender) voor de gerelateerde services.

**Azure Policy ingebouwde definities - Microsoft.Network**:

[!INCLUDE [Resource Policy for Microsoft.Network 1.5](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-5.md)]

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Op het netwerk gebaseerde inbraakdetectie-/inbraakpreventiesystemen (IDS/IPS) implementeren

**Richtlijnen:** implementeert een aanbieding van de Azure Marketplace ondersteuning biedt voor de FUNCTIONALITEIT van IDS/IPS met mogelijkheden voor nettoladinginspectie voor de omgeving van uw Load Balancer. 

Gebruik Azure Firewall bedreigingsinformatie als payloadinspectie geen vereiste is. Azure Firewall filteren op basis van bedreigingsinformatie wordt gebruikt om verkeer van en naar bekende schadelijke IP-adressen en domeinen te waarschuwen of te blokkeren. De IP-adressen en domeinen zijn afkomstig van de Microsoft Bedreigingsinformatie-feed.

Implementeer de firewalloplossing van uw keuze op elk van de netwerkgrenzen van uw organisatie om schadelijk verkeer te detecteren en/of te blokkeren.

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

- [Een Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md)

- [Waarschuwingen configureren met Azure Firewall](../firewall/threat-intel.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Verkeer naar webtoepassingen beheren

**Richtlijnen:** Definieer de internetverbinding en geldige bron-IP's expliciet via uitgaande regels en netwerkbeveiligingsgroepen met uw Load Balancer om de bedreigingsinformatiefuncties van Microsoft te gebruiken om uw webtoepassingen te beveiligen.

- [De Azure Firewall](../firewall/integrate-lb.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Complexiteit en administratieve overhead van netwerkbeveiligingsregels minimaliseren

**Richtlijnen:** gebruik servicetags in plaats van specifieke IP-adressen bij het maken van beveiligingsregels. Geef de naam van de servicetag op in het bron- of doelveld van een regel om het verkeer voor de bijbehorende service toe te staan of te weigeren. 

Microsoft beheert de adres voorvoegsels die worden omvat door de servicetag en werkt de servicetag automatisch bij wanneer adressen veranderen. 

Standaard bevat elke netwerkbeveiligingsgroep de servicetag AzureLoadBalancer om statustestverkeer toe te staan. 

Raadpleeg de Azure-documentatie voor alle servicetags die beschikbaar zijn voor gebruik in regels voor netwerkbeveiligingsgroep.

- [Beschikbare servicetags](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#available-service-tags)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Standaardbeveiligingsconfiguraties voor netwerkapparaten onderhouden

**Richtlijnen:** standaardbeveiligingsconfiguraties definiëren en implementeren voor netwerkbronnen met Azure Policy.

Gebruik Azure Blueprints grootschalige Azure-implementaties te vereenvoudigen door belangrijke omgevingsartefacten, zoals Azure Resources Manager-sjablonen, Azure RBAC-besturingselementen en beleidsregels, in één blauwdrukdefinitie te verpakken. 

Pas de blauwdruk toe op nieuwe abonnementen en stem het beheer en beheer af via versiebeheer.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy voorbeelden voor netwerken](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network)

- [Een Azure Blueprint maken](../governance/blueprints/create-blueprint-portal.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="110-document-traffic-configuration-rules"></a>1.10: Configuratieregels voor verkeer documenteren

**Richtlijnen:** Resourcetags gebruiken voor netwerkbeveiligingsgroepen en andere resources met betrekking tot netwerkbeveiliging en verkeersstroom. 

Gebruik het veld Beschrijving om de regels te documenteren die verkeer van/naar een netwerk toestaan voor afzonderlijke regels voor netwerkbeveiligingsgroep.

Implementeert een van de ingebouwde Azure Policy-definities met betrekking tot taggen, zoals Tag en waarde vereisen. Dit zorgt ervoor dat alle resources worden gemaakt met tags en dat er een melding wordt gegeven over bestaande resources zonder tag.

Gebruik Azure PowerShell of Azure CLI om resources op te zoeken of acties uit te voeren op basis van hun tags.

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

- [Een Azure-Virtual Network](../virtual-network/quick-create-portal.md)

- [Netwerkverkeer filteren met regels voor netwerkbeveiligingsgroep](../virtual-network/tutorial-filter-network-traffic.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Geautomatiseerde hulpprogramma's gebruiken om netwerkresourceconfiguraties te bewaken en wijzigingen te detecteren

**Richtlijnen:** Gebruik het Azure-activiteitenlogboek om resourceconfiguraties te bewaken en wijzigingen in uw Azure-resources te detecteren. 

Maak waarschuwingen in Azure Monitor u te waarschuwen wanneer kritieke resources worden gewijzigd.

- [Azure-activiteitenlogboekgebeurtenissen weergeven en ophalen](https://docs.microsoft.com/azure/azure-monitor/essentials/activity-log#view-the-activity-log)

- [Waarschuwingen maken in Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="logging-and-monitoring"></a>Logboekregistratie en bewaking

*Zie de Azure [Security Benchmark: Logging and Monitoring (Azure Security Benchmark: logboekregistratie en bewaking) voor meer informatie.](../security/benchmarks/security-control-logging-monitoring.md)*

### <a name="22-configure-central-security-log-management"></a>2.2: Centraal beheer van beveiligingslogboek configureren

**Richtlijnen:** bekijk wijzigingen in uw uitgaande regels en netwerkbeveiligingsgroepen voor uw Load Balancers door het activiteitenlogboek in uw abonnementen te bekijken. 

Bekijk de interne hostlogboeken om ervoor te zorgen dat uw back-end-resources veilig zijn.

Exporteert deze logboeken naar Log Analytics of een ander opslagplatform. Gebruik Azure Monitor Log Analytics-werkruimten om query's uit te voeren en analyses uit te voeren, en gebruik Azure Storage accounts voor langetermijn- en archiveringsopslag.

U kunt deze gegevens inschakelen en in gebruik nemen Azure Sentinel of een SIEM van derden op basis van de zakelijke vereisten van uw organisatie.

- [Onboarding van Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Platformlogboeken en metrische gegevens verzamelen met Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md)

- [Interne hostlogboeken van virtuele Azure-machines verzamelen met Azure Monitor](../azure-monitor/vm/quick-collect-azurevm.md)

- [Aan de slag met Azure Monitor siem-integratie van derden](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

- [Platformactiviteitenlogboeken](../azure-monitor/essentials/activity-log.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Auditlogregistratie inschakelen voor Azure-resources

**Richtlijnen:** bekijk de logboek- en controlegegevens van de besturings- en beheervlak die zijn vastgelegd met activiteitenlogboeken voor de Load Balancer. Deze instellingen voor vastleggen zijn standaard ingeschakeld. 

Gebruik Activiteitenlogboeken om acties voor resources te bewaken om alle activiteiten en hun status weer te geven. 

Bepaal met activiteitenlogboeken welke bewerkingen zijn uitgevoerd op de resources in uw abonnement: 

- wie de bewerking heeft gestart
- wanneer de bewerking heeft plaatsgevonden
- de status van de bewerking

- de waarden van andere eigenschappen die u kunnen helpen bij het onderzoeken van de bewerking

Haal informatie op uit het activiteitenlogboek via Azure PowerShell, de Azure-opdrachtregelinterface (CLI), de Azure REST API of de Azure Portal. 

Multidimensionale diagnostische gegevens implementeren voor de Standard Load Balancer configuratiemogelijkheden met Azure Monitor.  Deze omvatten beschikbare metrische gegevens voor beveiliging, waaronder informatie over SNAT-verbindingen (Source Network Address Translation), poorten. Er zijn ook aanvullende metrische gegevens beschikbaar voor SYN-pakketten (synchroniseren) en pakkettellers. 

Multidimensionale metrische gegevens programmatisch ophalen via API's en deze naar een opslagaccount schrijven via de optie Alle metrische gegevens.

Gebruik het inhoudspakket Auditlogboeken van Azure met Microsoft Power BI om uw gegevens te analyseren met vooraf geconfigureerde dashboards of om de weergaven aan te passen op basis van uw vereisten.

Logboeken streamen naar een Event Hub of een Log Analytics-werkruimte. Ze kunnen ook worden geëxtraheerd uit Azure Blob Storage en worden bekeken in verschillende hulpprogramma's, zoals Excel en Power BI. 

Gegevens inschakelen en in gebruik nemen om Azure Sentinel siem van derden te maken op basis van uw zakelijke vereisten.

- [Lees dit artikel met stapsgewijs instructies voor elke methode die wordt beschreven in De bewerkingen controleren met Resource Manager](../azure-resource-manager/management/view-activity-logs.md)

- [Azure Monitor-logboeken voor openbare Basic Azure Load Balancer](load-balancer-monitor-log.md)

- [Activiteitenlogboeken weergeven om acties voor resources te controleren](../azure-resource-manager/management/view-activity-logs.md)

- [Multidimensionale metrische gegevens programmatisch ophalen via API's](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics#retrieve-multi-dimensional-metrics-programmatically-via-apis)

- [Aan de slag met Azure Monitor siem-integratie van derden](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="25-configure-security-log-storage-retention"></a>2.5: Opslagretentie van beveiligingslogboek configureren

**Richtlijnen:** het activiteitenlogboek is standaard ingeschakeld en blijft 90 dagen bewaard in het Azure-logboekenlogboek. Stel de retentieperiode van uw Log Analytics-werkruimte in op basis van de nalevingsvoorschriften van uw organisatie in Azure Monitor. Gebruik Azure Storage accounts voor langetermijn- en archiveringsopslag.

- [Artikel Activiteitenlogboeken weergeven voor het bewaken van acties op resources](../azure-resource-manager/management/view-activity-logs.md)

- [De gegevensretentieperiode in Log Analytics wijzigen](https://docs.microsoft.com/azure/azure-monitor/logs/manage-cost-storage#change-the-data-retention-period)

- [Retentiebeleid configureren voor Azure Storage accountlogboeken](https://docs.microsoft.com/azure/storage/common/manage-storage-analytics-logs#configure-logging)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="26-monitor-and-review-logs"></a>2.6: Logboeken bewaken en controleren

**Richtlijnen:** bemonitor, beheer en los problemen Standard Load Balancer resources op met behulp van de Load Balancer-pagina op de pagina Azure Portal en de Resource Health onder Azure Monitor. Beschikbare metrische gegevens voor beveiliging bevatten informatie over SNAT-verbindingen (Source Network Address Translation), poorten. Daarnaast zijn er metrische gegevens over SYN-pakketten en pakkettellers (synchroniseren) beschikbaar. 

Gebruik Azure Monitor statusteststatus van eindpunten te controleren met multidimensionale metrische gegevens voor Standard,externe en interne Load Balancers. Verzamel deze metrische gegevens programmatisch via API's en geschreven naar een opslagaccount via de optie Alle metrische gegevens.

Azure Monitor zijn niet beschikbaar voor interne Basic Load Balancers. 

Gebruik Azure Monitor om de status van de statustest per back-endpool samen te vatten voor de externe basic Load Balancer, zoals het aantal instanties in uw back-endpool dat geen aanvragen van de Load Balancer ontvangt vanwege fouten in de statustest. 

Implementeert logboekregistratie met Azure Operational Insights om statistieken over de status van load balancer te geven. 

Gebruik de activiteitenlogboeken om waarschuwingen weer te geven en acties voor resources en hun status in uw Azure-abonnementen te controleren. Activiteitenlogboeken zijn standaard ingeschakeld en kunnen worden weergegeven in de Azure Portal. 

Gebruik Microsoft Power BI met het azure auditlogboeken-inhoudspakket en analyseer uw gegevens met vooraf geconfigureerde dashboards. Weergaven aanpassen aan uw vereisten op basis van bedrijfsbehoeften. 

Logboeken streamen naar een Event Hub of een Log Analytics-werkruimte. Ze kunnen ook worden geëxtraheerd uit Azure Blob Storage en worden bekeken in verschillende hulpprogramma's, zoals Excel en Power BI. U kunt gegevens inschakelen en inschakelen voor Azure Sentinel siem van derden.

- [Statustesten voor Load Balancer](load-balancer-custom-probe-overview.md)

- [Azure Monitor REST API](/rest/api/monitor)

- [Metrische gegevens ophalen via REST API](/rest/api/monitor/metrics/list)

- [Standard Load Balancer met metrische gegevens, waarschuwingen en resource health](load-balancer-standard-diagnostics.md)

- [Azure Monitor-logboeken voor openbare Basic Azure Load Balancer](load-balancer-monitor-log.md)

- [Uw metrische load balancer weergeven in de Azure Portal](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics#view-your-load-balancer-metrics-in-the-azure-portal)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Waarschuwingen inschakelen voor afwijkende activiteiten

**Richtlijnen:** Gebruik Security Center Log Analytics-werkruimte voor het bewaken en waarschuwen van afwijkende activiteiten met betrekking tot Load Balancer in beveiligingslogboeken en gebeurtenissen.

Schakel gegevens in en gebruiksgegevens om Azure Sentinel siem-hulpprogramma van derden te gebruiken.

- [Onboarding van Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Waarschuwingen beheren in Azure Security Center](../security-center/security-center-managing-and-responding-alerts.md)

- [Waarschuwingen voor logboekgegevens van Log Analytics](../azure-monitor/alerts/tutorial-response.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="identity-and-access-control"></a>Identiteits- en toegangsbeheer

*Zie Azure Security [Benchmark: Identity and Access Control (Azure Security Benchmark: identiteit en Access Control).](../security/benchmarks/security-control-identity-access-control.md)*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Een inventaris van beheerdersaccounts onderhouden

**Richtlijnen:** Met op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC) kunt u toegang tot Azure-resources, zoals uw Load Balancer via roltoewijzingen. Wijs deze rollen toe aan gebruikers, groepeert service-principals en beheerde identiteiten.

Inventaris vooraf gedefinieerde en ingebouwde rollen voor bepaalde resources met hulpprogramma's zoals Azure CLI, Azure PowerShell of de Azure Portal.

- [Een directoryrol krijgen in Azure Active Directory (Azure AD) met PowerShell](/powershell/module/azuread/get-azureaddirectoryrole)

- [Leden van een directoryrol in Azure AD krijgen met PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Meervoudige verificatie gebruiken voor alle Azure Active Directory op basis van toegang

**Richtlijnen:** meervoudige Azure Active Directory (Azure AD) inschakelen en de aanbevelingen Security Center identiteits- en toegangsbeheer volgen.

- [Meervoudige verificatie inschakelen in Azure](../active-directory/authentication/howto-mfa-getstarted.md) 

- [Identiteit en toegang binnen uw Azure Security Center](../security-center/security-center-identity-access.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Toegewezen machines (Privileged Access Workstations) gebruiken voor alle beheertaken

**Richtlijnen:** Paw (Privileged Access Workstations) gebruiken met meervoudige verificatie die is geconfigureerd voor het beheren en openen van Azure-netwerkresources. 

- [Meer informatie over Privileged Access Workstations](/security/compass/privileged-access-devices)

- [Meervoudige verificatie inschakelen in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="38-manage-azure-resources-only-from-approved-locations"></a>3.8: Azure-resources alleen beheren vanaf goedgekeurde locaties

**Richtlijnen:** gebruik benoemde locaties voor voorwaardelijke toegang om alleen toegang toe te staan vanuit specifieke logische groeperingen van IP-adresbereiken of landen/regio's.

- [Benoemde locaties configureren in Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="39-use-azure-active-directory"></a>3.9: Gebruik Azure Active Directory

**Richtlijnen:** gebruik Azure Active Directory (Azure AD) als een centraal verificatie- en autorisatiesysteem voor uw services. Azure AD beveiligt gegevens met behulp van sterke versleuteling voor data-at-rest en in transit en ook salts, hashes en veilig opgeslagen gebruikersreferenties.  

- [Een Azure AD-instantie maken en configureren](../active-directory-domain-services/tutorial-create-instance.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Regelmatig gebruikerstoegang controleren en afstemmen

**Richtlijnen:** gebruik Azure Active Directory (Azure AD) om logboeken op te geven voor het vinden van verouderde accounts. 

Azure Identity Access Reviews kan worden uitgevoerd om groepslidmaatschap, toegang tot bedrijfstoepassingen en roltoewijzingen efficiënt te beheren. Gebruikerstoegang moet regelmatig worden gecontroleerd om ervoor te zorgen dat alleen de actieve gebruikers nog toegang hebben.

- [Inzicht in Azure AD-rapportage](/azure/active-directory/reports-monitoring/)

- [Toegangsbeoordelingen voor Azure-identiteiten gebruiken](../active-directory/governance/access-reviews-overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Pogingen tot toegang tot gedeactiveerde referenties controleren

**Richtlijnen:** Integreer Azure Active Directory(Azure AD)-aanmeldingsactiviteit, controle- en risicogebeurtenislogboekbronnen met elk SIEM- of bewakingshulpprogramma op basis van uw toegang.

Stroomlijn dit proces door diagnostische instellingen te maken voor Azure AD-gebruikersaccounts en de auditlogboeken en aanmeldingslogboeken te verzenden naar een Log Analytics-werkruimte. Alle gewenste waarschuwingen kunnen worden geconfigureerd in de Log Analytics-werkruimte.

- [Azure-activiteitenlogboeken integreren in Azure Monitor](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Waarschuwing bij afwijking van aanmeldingsgedrag van account

**Richtlijnen:** gebruik Azure Active Directory (Azure AD)-functies voor risico- en identiteitsbeveiliging om geautomatiseerde reacties te configureren op gedetecteerde verdachte acties met betrekking tot gebruikersidentiteiten. Gegevens opnemen in Azure Sentinel voor verdere onderzoeken.

- [Riskante Azure AD-aanmeldingen weergeven](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [Identiteitsbeveiligingsrisicobeleid configureren en inschakelen](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Onboarding van Azure Sentinel](../sentinel/quickstart-onboard.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="data-protection"></a>Gegevensbeveiliging

*Zie [Azure Security Benchmark: gegevensbescherming](../security/benchmarks/security-control-data-protection.md) voor meer informatie.*

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Alle gevoelige gegevens tijdens de overdracht versleutelen

**Richtlijnen:** zorg ervoor dat clients die verbinding maken met uw Azure-resources, kunnen onderhandelen over TLS 1.2 of hoger.

Volg Azure Security Center aanbevelingen voor versleuteling in rust en versleuteling in transit, indien van toepassing.

- [Inzicht in versleuteling tijdens overdracht met Azure](https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4.6: Azure RBAC gebruiken om de toegang tot resources te beheren

**Richtlijnen:** Gebruik Azure RBAC om de toegang tot uw Load Balancer beheren.

- [Azure RBAC configureren](../role-based-access-control/role-assignments-portal.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Preventie van gegevensverlies op basis van een host gebruiken om toegangsbeheer af te dwingen

**Richtlijnen:** Load Balancer is een pass through-service die geen klantgegevens opgeslagen. Het maakt deel uit van het onderliggende platform dat wordt beheerd door Microsoft. 

Microsoft behandelt alle klantinhoud als gevoelig en doet er alles aan om bescherming te bieden tegen verlies en blootstelling van klantgegevens. 

Om ervoor te zorgen dat klantgegevens in Azure veilig blijven, heeft Microsoft een suite met robuuste besturingselementen en mogelijkheden voor gegevensbeveiliging geïmplementeerd en onderhouden. 

- [Informatie over beveiliging van klantgegevens in Azure](../security/fundamentals/protection-customer-data.md)

**Verantwoordelijkheid**: Gedeeld

**Azure Security Center bewaking:** geen

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Logboeken en waarschuwingen voor wijzigingen in kritieke Azure-resources

**Richtlijnen:** gebruik Azure Monitor met het Azure-activiteitenlogboek om waarschuwingen te maken wanneer er wijzigingen worden aangebracht in kritieke Azure-resources, zoals Load Balancers die worden gebruikt voor belangrijke productieworkloads.

- [Waarschuwingen maken voor gebeurtenissen in het Azure-activiteitenlogboek](../azure-monitor/alerts/alerts-activity-log.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="inventory-and-asset-management"></a>Inventarisatie en Asset Management

*Zie Azure Security [Benchmark: Inventory and Asset Management (Azure Security Benchmark: Inventaris en Asset Management) voor meer informatie.](../security/benchmarks/security-control-inventory-asset-management.md)*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Een geautomatiseerde oplossing voor assetdetectie gebruiken

**Richtlijnen:** gebruik Azure Resource Graph om alle resources (zoals rekenkracht, opslag, netwerk, poorten, protocollen, en dergelijke) in uw abonnementen op te vragen en te ontdekken. Azure Resource Manager wordt aanbevolen om de huidige resources te maken en te gebruiken.

Zorg voor de juiste machtigingen (lezen) in uw tenant en semuler alle Azure-abonnementen en -resources in uw abonnementen.

- [Query's maken met Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

- [Uw Azure-abonnementen weergeven](/powershell/module/az.accounts/get-azsubscription)

- [Inzicht krijgen in Azure RBAC](../role-based-access-control/overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="62-maintain-asset-metadata"></a>6.2: Metagegevens van activa onderhouden

**Richtlijnen:** Tags toepassen op Azure-resources met metagegevens om logisch te organiseren op basis van een taxonomie.

- [Tags maken en gebruiken](/azure/azure-resource-manager/resource-group-using-tags)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Niet-geautoriseerde Azure-resources verwijderen

**Richtlijnen:** Gebruik waar nodig tags, beheergroepen en afzonderlijke abonnementen om assets te organiseren en bij te houden. 

Afstemmen van inventaris op regelmatige basis en ervoor zorgen dat niet-geautoriseerde resources worden verwijderd uit uw abonnementen op tijd.

- [Aanvullende Azure-abonnementen maken](../cost-management-billing/manage/create-subscription.md)

- [Beheergroepen maken](../governance/management-groups/create-management-group-portal.md)

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4: Een inventaris van goedgekeurde Azure-resources definiëren en onderhouden

**Richtlijnen:** maak een lijst met goedgekeurde Azure-resources volgens de behoeften van uw organisatie die u als een allowlist-mechanisme kunt gebruiken. Hierdoor kan uw organisatie alle nieuw beschikbare Azure-services onboarden nadat ze formeel zijn beoordeeld en goedgekeurd door de gebruikelijke beveiligingsevaluatieprocessen van uw organisatie.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Controleren op niet-goedgekeurde Azure-resources

**Richtlijnen:** Azure Policy beperkingen in te stellen voor het type resources dat in uw abonnementen kan worden gemaakt.

Query's uitvoeren op resources en deze Azure Resource Graph binnen abonnementen in eigendom. 

Zorg ervoor dat alle Azure-resources in de omgeving zijn goedgekeurd.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Query's maken met Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="69-use-only-approved-azure-services"></a>6.9: Alleen goedgekeurde Azure-services gebruiken

**Richtlijnen:** gebruik Azure Policy beperkingen in te stellen voor het type resources dat kan worden gemaakt in klantabonnementen met behulp van de volgende ingebouwde beleidsdefinities:
- Niet toegestane resourcetypen
- Toegestane brontypen

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Een specifiek resourcetype weigeren met Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

- [Ingebouwde ingebouwde Azure-beleidsvoorbeelden voor virtueel netwerk](/azure/virtual-network/policy-samples)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: De mogelijkheid van gebruikers om te communiceren met Azure Resource Manager

**Richtlijnen:** gebruik Azure Active Directory (Azure AD) Voorwaardelijke toegang om de mogelijkheid van gebruikers om te communiceren met Azure Resource Manager te beperken door Toegang blokkeren te configureren voor de app Microsoft Azure Management.

- [Voorwaardelijke toegang configureren om toegang tot Azure Resources Manager te blokkeren](../role-based-access-control/conditional-access-azure-management.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Toepassingen met een hoog risico fysiek of logisch scheiden

**Richtlijnen:** Software die vereist is voor zakelijke activiteiten, maar die een hoger risico voor de organisatie kan vormen, moet worden geïsoleerd binnen een eigen virtuele machine en/of virtueel netwerk en voldoende worden beveiligd met een Azure Firewall of een netwerkbeveiligingsgroep.

- [Een virtueel netwerk maken](../virtual-network/quick-create-portal.md)

- [Een netwerkbeveiligingsgroep maken met een beveiligings config](../virtual-network/tutorial-filter-network-traffic.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="secure-configuration"></a>Veilige configuratie

*Zie de Azure [Security Benchmark: Secure Configuration voor meer informatie.](../security/benchmarks/security-control-secure-configuration.md)*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Veilige configuraties voor alle Azure-resources tot stand brengen

**Richtlijnen:** gebruik Azure Policy om aangepaste beleidsregels te maken om de configuratie van uw Azure-resources te controleren of af te dwingen. Gebruik ingebouwde Azure Policy definities.

Azure Resource Manager heeft de mogelijkheid om de sjabloon te exporteren in JavaScript Object Notation (JSON), die moet worden gecontroleerd om ervoor te zorgen dat de configuraties voldoen aan de beveiligingsvereisten voor uw organisatie.

Export Azure Resource Manager-sjablonen naar JavaScript Object Notation (JSON)-indelingen en controleer deze periodiek om ervoor te zorgen dat de configuraties voldoen aan de beveiligingsvereisten van uw organisatie.

Implementeert aanbevelingen van Security Center als een veilige configuratiebasislijn voor uw Azure-resources.

- [Beschikbare aliassen Azure Policy weergeven](/powershell/module/az.resources/get-azpolicyalias)

- [Zelfstudie: Beleidsregels voor het afdwingen van naleving maken en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Exporteren van één resource en meerdere resources naar een sjabloon in Azure Portal](../azure-resource-manager/templates/export-template-portal.md)

- [Aanbevelingen voor beveiliging: een naslaggids](../security-center/recommendations-reference.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Veilige Azure-resourceconfiguraties onderhouden

**Richtlijnen:** gebruik Azure Policy [weigeren] en [implementeren indien niet aanwezig] om beveiligde instellingen af te dwingen voor uw Azure-resources.  U kunt ook de sjablonen Azure Resource Manager de beveiligingsconfiguratie te onderhouden van uw Azure-resources die door uw organisatie worden vereist. 

- [Inzicht Azure Policy effecten](../governance/policy/concepts/effects.md)

- [Beleidsregels voor het afdwingen van naleving maken en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Azure Resource Manager sjablonen](../azure-resource-manager/templates/overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Configuratie van Azure-resources veilig opslaan

**Richtlijnen:** Gebruik Azure DevOps om uw code veilig op te slaan en te beheren, zoals aangepaste Azure Policy-definities, Azure Resource Manager-sjablonen en scripts voor gewenste statusconfiguratie.

Verleen of weiger machtigingen aan specifieke gebruikers, ingebouwde beveiligingsgroepen of groepen die zijn gedefinieerd in Azure Active Directory (Azure AD) als deze is geïntegreerd met Azure DevOps of in Azure AD als deze zijn geïntegreerd met TFS.

- [Code opslaan in Azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Over machtigingen en groepen in Azure DevOps](/azure/devops/organizations/security/about-permissions)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Hulpprogramma's voor configuratiebeheer implementeren voor Azure-resources

**Richtlijnen:** standaardbeveiligingsconfiguraties voor Azure-resources definiëren en implementeren met behulp Azure Policy.  Gebruik Azure Policy om aangepaste beleidsregels te maken om de netwerkconfiguratie van uw Azure-resources te controleren of af te dwingen. Ingebouwde beleidsdefinities implementeren die betrekking hebben op uw specifieke Azure Load Balancer resources.  Gebruik ook Azure Automation om configuratiewijzigingen te implementeren. aan uw Azure Load Balancer resources.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Aliassen gebruiken](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Geautomatiseerde configuratiebewaking implementeren voor Azure-resources

**Richtlijnen:** gebruik Security Center basislijnscans uit te voeren voor uw Azure-resources en Azure Policy om resourceconfiguraties te waarschuwen en te controleren.

- [Aanbevelingen herstellen in Azure Security Center](../security-center/security-center-remediate-recommendations.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="incident-response"></a>Reageren op incidenten

*Zie [Azure Security Benchmark: respons op incidenten](../security/benchmarks/security-control-incident-response.md) voor meer informatie.*

### <a name="101-create-an-incident-response-guide"></a>10.1: Een handleiding voor het reageren op incidenten maken

**Richtlijnen**: Stel voor uw organisatie een responshandleiding op voor gebruik bij incidenten. Zorg ervoor dat er schriftelijke responsplannen zijn waarin alle rollen van het personeel worden gedefinieerd, evenals alle fasen in het afhandelen/managen van incidenten, vanaf de detectie van het incident tot een evaluatie ervan achteraf. 

- [Werkstroomautomatiseringen configureren binnen Azure Security Center](../security-center/security-center-planning-and-operations-guide.md) 

- [Richtlijnen voor het bouwen van uw eigen proces voor het reageren op beveiligingsincidents](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/) 

- [Microsoft Security Response Center van een incident](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/) 

- [De klant kan ook gebruikmaken van de computerbeveiligingsincidentafhandelingshandleiding van NIST om te helpen bij het maken van een eigen plan voor het reageren op incidenten](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Een procedure voor het scoren en prioriteren van incidenten maken

**Richtlijnen:** Security Center aan elke waarschuwing een ernst toe om u te helpen bepalen welke waarschuwingen het eerst moeten worden onderzocht. 

De ernst is gebaseerd op hoe zeker Security Center is bij het vinden of de analyse die wordt gebruikt om de waarschuwing uit te geven, evenals het betrouwbaarheidsniveau dat er een schadelijke intentie achter de activiteit is die heeft geleid tot de waarschuwing.

Markeer abonnementen met behulp van tags en maak een naamgevingssysteem om Azure-resources te identificeren en te categoriseren, met name voor de verwerking van gevoelige gegevens.  

Het is uw verantwoordelijkheid om prioriteit te geven aan het oplossen van waarschuwingen op basis van de ernst van de Azure-resources en -omgeving waarin het incident heeft plaatsgevonden.

- [Beveiligingswaarschuwingen in Azure Security Center](../security-center/security-center-alerts-overview.md)

- [Tags gebruiken om Azure-resources te organiseren](../azure-resource-manager/management/tag-resources.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="103-test-security-response-procedures"></a>10.3: Procedures voor het testen van beveiligingsreacties

**Richtlijnen:** oefeningen uitvoeren om de mogelijkheden voor het reageren op incidenten van uw systemen regelmatig te testen om uw Azure-resources te beveiligen. Identificeer zwakke punten en hiaten en pas uw reactieplan naar behoefte aan. 

- [Publicatie van NIST- Handleiding voor test-, trainings- en oefeningsprogramma's voor IT-plannen en -mogelijkheden](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Contactgegevens voor beveiligingsincidenten verstrekken en waarschuwingsmeldingen voor beveiligingsincidenten configureren

**Richtlijnen:** Contactgegevens voor beveiligingsincidenten worden door Microsoft gebruikt om contact met u op te nemen als de Microsoft Security Response Center (MSRC) detecteert dat uw gegevens zijn gebruikt door een onrechtmatige of niet-geautoriseerde partij. Bekijk incidenten na het feit om ervoor te zorgen dat problemen worden opgelost. 

- [De Azure Security Center Security-contactpersoon instellen](../security-center/security-center-provide-security-contact-details.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Beveiligingswaarschuwingen opnemen in uw systeem voor het reageren op incidenten

**Richtlijnen:** exporteert uw Security Center en aanbevelingen met behulp van de functie voor continue export om risico's voor Azure-resources te identificeren. 

Gebruik de functie Continue export in Security Center waarmee u waarschuwingen en aanbevelingen handmatig of continu kunt exporteren. 

Gebruik de Security Center-gegevensconnector om de waarschuwingen te streamen naar Azure Sentinel.

- [Continue export configureren](../security-center/continuous-export.md)

- [Waarschuwingen streamen naar Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: De reactie op beveiligingswaarschuwingen automatiseren

**Richtlijnen:** gebruik de functie Werkstroomautomatisering in Security Center automatisch reacties op beveiligingswaarschuwingen en aanbevelingen te activeren om uw Azure-resources te beveiligen.

- [Werkstroomautomatisering configureren in Security enter](../security-center/workflow-automation.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="penetration-tests-and-red-team-exercises"></a>Penetratietests en Red Team-oefeningen

*Zie de Azure [Security Benchmark: Penetration Tests en Red Team Exercises (Penetratietests en Red Team-oefeningen) voor meer informatie.](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Regelmatige penetratietests uitvoeren van uw Azure-resources en zorgen voor herstel van alle kritieke beveiligings bevindingen

**Richtlijnen:** volg de Microsoft Cloud Penetration Testing Rules of Engagement om ervoor te zorgen dat uw penetratietests niet in strijd zijn met het Microsoft-beleid. Gebruik de strategie van Microsoft en de uitvoering van Red Teaming-activiteiten, en voer een penetratietest van de live site uit op basis van een infrastructuur, services en toepassingen die door Microsoft worden beheerd. 

- [Regels voor het inzetten van penetratietests](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="next-steps"></a>Volgende stappen

- Zie [Overzicht Azure Security Benchmark V2](/azure/security/benchmarks/overview)
- Meer informatie over [Azure-beveiligingsbasislijnen](/azure/security/benchmarks/security-baselines-overview)
