---
title: Azure-beveiligings basislijn voor Azure-toepassing gateway
description: Azure-beveiligings basislijn voor Azure-toepassing gateway
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 06/05/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 4f28665998dcac9f641d4142a0dea60707fb02e9
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/07/2021
ms.locfileid: "99805357"
---
# <a name="azure-security-baseline-for-azure-application-gateway"></a>Azure-beveiligings basislijn voor Azure-toepassing gateway

De Azure-beveiligings basislijn voor Azure-toepassing-gateway bevat aanbevelingen waarmee u de beveiligings postuur van uw implementatie kunt verbeteren.

De basis lijn voor deze service wordt opgehaald uit de [Azure Security Bench Mark-versie 1,0](../security/benchmarks/overview.md), die aanbevelingen biedt over hoe u uw cloud oplossingen kunt beveiligen in azure met onze richt lijnen voor best practices.

Zie het [overzicht van Azure Security-basis lijnen](../security/benchmarks/security-baselines-overview.md)voor meer informatie.

## <a name="network-security"></a>Netwerkbeveiliging

*Zie [beveiligings beheer: netwerk beveiliging](../security/benchmarks/security-control-network-security.md)voor meer informatie.*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Azure-resources in virtuele netwerken beveiligen

**Richt lijnen**: Zorg ervoor dat voor alle Virtual Network Azure-toepassing gateway-subnet-implementaties een netwerk beveiligings groep (NSG) is toegepast die specifiek is voor de vertrouwde poorten en bronnen van uw toepassing. Hoewel netwerk beveiligings groepen worden ondersteund op Azure-toepassing gateway, zijn er enkele beperkingen en vereisten waaraan moet worden voldaan om ervoor te zorgen dat uw NSG en Azure-toepassing gateway naar verwachting werken.

* [Meer informatie over de beperkingen en vereisten voor het gebruik van Nsg's met Azure-toepassing gateway](./configuration-overview.md)

* [Een NSG maken met een beveiligings configuratie](../virtual-network/tutorial-filter-network-traffic.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1,2: de configuratie en het verkeer van virtuele netwerken, subnetten en Nic's bewaken en vastleggen

**Richt lijnen**: voor de netwerk beveiligings groepen (nsg's) die zijn gekoppeld aan uw Azure-toepassing gateway-subnetten, schakelt u NSG-stroom Logboeken in en verzendt u logboeken naar een opslag account voor verkeers controle. U kunt ook NSG-stroom logboeken naar een Log Analytics-werk ruimte verzenden en Traffic Analytics gebruiken om inzicht te krijgen in de verkeers stroom in uw Azure-Cloud. Enkele voor delen van Traffic Analytics zijn de mogelijkheid om netwerk activiteiten te visualiseren en HOTS pots te identificeren, beveiligings dreigingen te identificeren, verkeers patronen te begrijpen en netwerk configuraties te lokaliseren.

Opmerking: er zijn enkele gevallen waarin NSG-stroom logboeken die zijn gekoppeld aan uw Azure-toepassing gateway-subnetten geen verkeer zien dat is toegestaan. Als uw configuratie overeenkomt met het volgende scenario, ziet u geen toegestaan verkeer in uw NSG-stroom logboeken:
- U hebt Application Gateway v2 geïmplementeerd
- U hebt een NSG op het toepassings gateway-subnet
- U hebt NSG-stroom logboeken ingeschakeld op die NSG

* [NSG-stroom logboeken inschakelen](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

* [Traffic Analytics inschakelen en gebruiken](../network-watcher/traffic-analytics.md)

* [Informatie over de netwerk beveiliging die wordt verschaft door Azure Security Center](../security-center/security-center-network-recommendations.md)

* [Veelgestelde vragen over diagnostische gegevens en logboek registratie voor Azure-toepassing gateway](./application-gateway-faq.yml#what-types-of-logs-does-application-gateway-provide)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="13-protect-critical-web-applications"></a>1,3: essentiële webtoepassingen beveiligen

**Richt lijnen**: implementeer Azure Web Application firewall (WAF) voor essentiële webtoepassingen voor extra inspectie van inkomend verkeer. Web Application firewall (WAF) is een service (functie van Azure-toepassing gateway) waarmee uw webtoepassingen gecentraliseerd worden beschermd tegen veelvoorkomende aanvallen en beveiligings problemen. Met Azure WAF kunt u uw Azure App Service web-apps beveiligen door inkomend webverkeer te controleren om aanvallen zoals SQL-injecties, cross-site scripting, uploads van malware en DDoS-aanvallen te blok keren. WAF is gebaseerd op regels van de OWASP (open Web Application Security project) kern regel sets 3,1 (alleen WAF_v2), 3,0 en 2.2.9.

* [Azure-toepassing gateway functies begrijpen](./features.md)

* [Meer informatie over Azure WAF](../web-application-firewall/ag/ag-overview.md)

* [Azure WAF implementeren](../web-application-firewall/ag/create-waf-policy-ag.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: communicatie met bekende schadelijke IP-adressen weigeren

**Richt lijnen**: Schakel DDoS Standard-beveiliging in op uw virtuele Azure-netwerken die zijn gekoppeld aan uw productie-exemplaren van Azure-toepassing gateway om te beschermen tegen DDoS-aanvallen. Gebruik Azure Security Center geïntegreerde bedreigings informatie om communicatie met bekende schadelijke IP-adressen te weigeren.

* [DDoS-beveiliging configureren](../ddos-protection/manage-ddos-protection.md)

* [Meer informatie over Azure Security Center geïntegreerde bedreigings informatie](../security-center/azure-defender.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="15-record-network-packets"></a>1,5: netwerk pakketten opnemen

**Richt lijnen**: voor de netwerk beveiligings groepen (nsg's) die zijn gekoppeld aan uw Azure-toepassing gateway-subnetten, schakelt u NSG-stroom Logboeken in en verzendt u logboeken naar een opslag account voor verkeers controle. U kunt ook NSG-stroom logboeken naar een Log Analytics-werk ruimte verzenden en Traffic Analytics gebruiken om inzicht te krijgen in de verkeers stroom in uw Azure-Cloud. Enkele voor delen van Traffic Analytics zijn de mogelijkheid om netwerk activiteiten te visualiseren en HOTS pots te identificeren, beveiligings dreigingen te identificeren, verkeers patronen te begrijpen en netwerk configuraties te lokaliseren.

Opmerking: er zijn enkele gevallen waarin NSG-stroom logboeken die zijn gekoppeld aan uw Azure-toepassing gateway-subnetten geen verkeer zien dat is toegestaan. Als uw configuratie overeenkomt met het volgende scenario, ziet u geen toegestaan verkeer in uw NSG-stroom logboeken:
- U hebt Application Gateway v2 geïmplementeerd
- U hebt een NSG op het toepassings gateway-subnet
- U hebt NSG-stroom logboeken ingeschakeld op die NSG

* [NSG-stroom logboeken inschakelen](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

* [Traffic Analytics inschakelen en gebruiken](../network-watcher/traffic-analytics.md)

* [Informatie over de netwerk beveiliging die wordt verschaft door Azure Security Center](../security-center/security-center-network-recommendations.md)

* [Veelgestelde vragen over diagnostische gegevens en logboek registratie voor Azure-toepassing gateway](./application-gateway-faq.yml#what-types-of-logs-does-application-gateway-provide)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: op netwerk gebaseerde inbreuk detectie/indringings systemen (ID'S/IP-adressen) implementeren

**Richt lijnen**: implementeer Azure Web Application firewall (WAF) voor essentiële webtoepassingen voor extra inspectie van inkomend verkeer. Web Application firewall (WAF) is een service (functie van Azure-toepassing gateway) waarmee uw webtoepassingen gecentraliseerd worden beschermd tegen veelvoorkomende aanvallen en beveiligings problemen. Met Azure WAF kunt u uw Azure App Service web-apps beveiligen door inkomend webverkeer te controleren om aanvallen zoals SQL-injecties, cross-site scripting, uploads van malware en DDoS-aanvallen te blok keren. WAF is gebaseerd op regels van de OWASP (open Web Application Security project) kern regel sets 3,1 (alleen WAF_v2), 3,0 en 2.2.9.

Het is ook mogelijk dat er meerdere Marketplace-opties zijn, zoals de Barracuda WAF voor Azure die beschikbaar zijn op de Azure Marketplace, die ID'S/IPS-functies bevatten.

* [Azure-toepassing gateway functies begrijpen](./features.md)

* [Meer informatie over Azure WAF](../web-application-firewall/ag/ag-overview.md)

* [Azure WAF implementeren](../web-application-firewall/ag/create-waf-policy-ag.md)

* [Meer informatie over Barracuda WAF Cloud service](../app-service/environment/app-service-app-service-environment-web-application-firewall.md#configuring-your-barracuda-waf-cloud-service)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="17-manage-traffic-to-web-applications"></a>1,7: verkeer naar webtoepassingen beheren

**Richt lijnen**: Azure-toepassing Gateway implementeren voor webtoepassingen waarvoor https/SSL is ingeschakeld voor vertrouwde certificaten.

* [Application Gateway implementeren](./quick-create-portal.md)

* [Application Gateway configureren voor het gebruik van HTTPS](./create-ssl-portal.md)

* [De taak verdeling van laag 7 met Azure Web Application-gateways begrijpen](./overview.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: de complexiteit en administratieve overhead van netwerk beveiligings regels minimaliseren

**Hulp**: gebruik Virtual Network Service Tags om netwerk toegangs beheer te definiëren voor netwerk beveiligings groepen of Azure firewall. U kunt servicetags gebruiken in plaats van specifieke IP-adressen wanneer u beveiligingsregels maakt. Door de naam van de service label (bijvoorbeeld GatewayManager) op te geven in het juiste bron-of doel veld van een regel, kunt u het verkeer voor de bijbehorende service toestaan of weigeren. Micro soft beheert de adres voorvoegsels die zijn opgenomen in het servicetag van de service en werkt de servicetag automatisch bij met gewijzigde adressen.

Voor de netwerk beveiligings groepen (Nsg's) die zijn gekoppeld aan uw Azure-toepassing gateway-subnetten, moet u binnenkomend Internet verkeer toestaan op TCP-poorten 65503-65534 voor de Application Gateway v1-SKU en TCP-poorten 65200-65535 voor de v2-SKU met het doel-subnet als enige en bron als GatewayManager servicetag. Dit poort bereik is vereist voor de communicatie van Azure-infra structuur. Deze poorten worden beveiligd (vergrendeld) door Azure-certificaten. Externe entiteiten, met inbegrip van de klanten van deze gateways, kunnen niet communiceren op deze eind punten.

* [Service Tags begrijpen en gebruiken](../virtual-network/service-tags-overview.md)

* [Overzicht van Azure-toepassing gateway configuratie](./configuration-overview.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: standaard beveiligings configuraties voor netwerk apparaten onderhouden

**Richt lijnen**: standaard beveiligings configuraties definiëren en implementeren voor netwerk instellingen die betrekking hebben op uw Azure-toepassing gateway-implementaties. Gebruik Azure Policy aliassen in de naam ruimte ' micro soft. netwerk ' om aangepaste beleids regels te maken voor het controleren of afdwingen van de netwerk configuratie van uw Azure-toepassing gateways, virtuele Azure-netwerken en netwerk beveiligings groepen. U kunt ook gebruikmaken van de ingebouwde beleids definitie.

U kunt ook Azure-blauw drukken gebruiken om grootschalige Azure-implementaties te vereenvoudigen door sleutel omgevings artefacten, zoals Azure Resource Manager sjablonen, Toegangs beheer op basis van rollen (Azure RBAC) en beleids regels in één blauw definitie te verpakken. U kunt de blauw druk eenvoudig Toep assen op nieuwe abonnementen, omgevingen en het beheer en de verwerkings mogelijkheden van versies.

* [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

* [Een Azure Blueprint maken](../governance/blueprints/create-blueprint-portal.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="110-document-traffic-configuration-rules"></a>1,10: configuratie regels voor het document verkeer

**Hulp**: Labels gebruiken voor netwerk beveiligings groepen (nsg's) die zijn gekoppeld aan uw Azure-toepassing gateway-subnet, evenals alle andere bronnen die betrekking hebben op netwerk beveiliging en verkeers stroom. Voor afzonderlijke NSG-regels gebruikt u het veld Beschrijving voor het opgeven van de bedrijfs behoefte en/of-duur (etc.) voor alle regels die verkeer van of naar een netwerk toestaan.

Gebruik een van de ingebouwde Azure-beleids definities die betrekking hebben op labelen, zoals ' vereist label en de waarde ', om ervoor te zorgen dat alle resources met tags worden gemaakt en u op de hoogte moet zijn van bestaande niet-gelabelde resources.

U kunt Azure PowerShell of Azure CLI gebruiken om op basis van hun labels acties op resources te zoeken of uit te voeren.

* [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

* [Een Virtual Network maken](../virtual-network/quick-create-portal.md)

* [Een NSG maken met een beveiligings configuratie](../virtual-network/tutorial-filter-network-traffic.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: gebruik automatische hulpprogram ma's om netwerk bron configuraties te bewaken en wijzigingen te detecteren

**Hulp**: Azure-activiteiten logboek gebruiken om netwerk bron configuraties te bewaken en wijzigingen te detecteren voor netwerk instellingen en-bronnen die betrekking hebben op uw Azure-toepassing gateway-implementaties. Maak waarschuwingen in Azure Monitor die worden geactiveerd wanneer er wijzigingen in essentiële netwerk instellingen of-resources plaatsvinden.

* [Activiteiten logboek gebeurtenissen van Azure weer geven en ophalen](../azure-monitor/platform/activity-log.md#view-the-activity-log)

* [Waarschuwingen maken in Azure Monitor](../azure-monitor/platform/alerts-activity-log.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

## <a name="logging-and-monitoring"></a>Logboekregistratie en bewaking

*Zie voor meer informatie [beveiligings beheer: logboek registratie en controle](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: goedgekeurde tijd synchronisatie bronnen gebruiken

**Richt lijnen**: micro soft houdt de tijd bron bij die wordt gebruikt voor Azure-resources, zoals Azure app service.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: micro soft

### <a name="22-configure-central-security-log-management"></a>2,2: Centraal beveiligings logboek beheer configureren

**Richt lijnen**: voor het bijhouden van de controle van de bewakings functie van het Azure-activiteiten logboek kunt u de logboeken voor diagnostische gegevens over het besturings systeem inschakelen en de logbestanden verzenden naar een log Analytics-werk ruimte, Azure Event hub Met Azure-activiteiten logboek gegevens kunt u de ' What, wie en wanneer ' bepalen voor schrijf bewerkingen (PUT, POST, DELETE) die worden uitgevoerd op het niveau van het controle vlak voor uw Azure-toepassing gateway en gerelateerde resources, zoals netwerk beveiligings groepen (Nsg's), die worden gebruikt voor het beveiligen van het Azure-toepassing gateway-subnet.

Naast activiteiten Logboeken kunt u Diagnostische instellingen configureren voor uw Azure-toepassing gateway-implementaties. Diagnostische instellingen worden gebruikt voor het configureren van het exporteren van streaming van platform logboeken en metrische gegevens voor een resource naar het doel van uw keuze (opslag accounts, Event Hubs en Log Analytics).

Azure-toepassing gateway biedt ook ingebouwde integratie met Azure-toepassing Insights. Application Insights verzamelt logboek-, prestatie-en fout gegevens. Application Insights detecteert automatisch prestatie afwijkingen en bevat krachtige analyse hulpprogramma's waarmee u problemen kunt vaststellen en inzicht krijgt in de manier waarop uw web-apps worden gebruikt. U kunt continue export inschakelen voor het exporteren van telemetrie van Application Insights naar een centrale locatie om de gegevens langer dan de standaard retentie periode te bewaren.

* [Diagnostische instellingen voor Azure-activiteiten logboek inschakelen](../azure-monitor/platform/activity-log.md)

* [Diagnostische instellingen inschakelen voor Azure-toepassing gateway](./application-gateway-diagnostics.md)

* [Application Insights inschakelen](../azure-monitor/app/app-insights-overview.md)

* [Continue export configureren](../azure-monitor/app/export-telemetry.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: controle logboek registratie inschakelen voor Azure-resources

**Richt lijnen**: voor het bijhouden van de controle van de bewakings functie van het Azure-activiteiten logboek kunt u de logboeken voor diagnostische gegevens over het besturings systeem inschakelen en de logbestanden verzenden naar een log Analytics-werk ruimte, Azure Event hub Met Azure-activiteiten logboek gegevens kunt u de ' What, wie en wanneer ' bepalen voor schrijf bewerkingen (PUT, POST, DELETE) die worden uitgevoerd op het niveau van het controle vlak voor uw Azure-toepassing gateway en gerelateerde resources, zoals netwerk beveiligings groepen (Nsg's), die worden gebruikt voor het beveiligen van het Azure-toepassing gateway-subnet.

Naast activiteiten Logboeken kunt u Diagnostische instellingen configureren voor uw Azure-toepassing gateway-implementaties. Diagnostische instellingen worden gebruikt voor het configureren van het exporteren van streaming van platform logboeken en metrische gegevens voor een resource naar het doel van uw keuze (opslag accounts, Event Hubs en Log Analytics).

Azure-toepassing gateway biedt ook ingebouwde integratie met Azure-toepassing Insights. Application Insights verzamelt logboek-, prestatie-en fout gegevens. Application Insights detecteert automatisch prestatie afwijkingen en bevat krachtige analyse hulpprogramma's waarmee u problemen kunt vaststellen en inzicht krijgt in de manier waarop uw web-apps worden gebruikt. U kunt continue export inschakelen voor het exporteren van telemetrie van Application Insights naar een centrale locatie om de gegevens langer dan de standaard retentie periode te bewaren.

* [Diagnostische instellingen voor Azure-activiteiten logboek inschakelen](../azure-monitor/platform/activity-log.md)

* [Diagnostische instellingen inschakelen voor Azure-toepassing gateway](./application-gateway-diagnostics.md)

* [Application Insights inschakelen](../azure-monitor/app/app-insights-overview.md)

* [Continue export configureren](../azure-monitor/app/export-telemetry.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: beveiligings logboeken verzamelen van besturings systemen

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor IaaS Compute-resources.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="25-configure-security-log-storage-retention"></a>2,5: Bewaar beveiliging van het beveiligings logboek configureren

**Hulp**: stel binnen Azure monitor uw Bewaar periode voor log Analytics werk ruimte in volgens de nalevings voorschriften van uw organisatie. Gebruik Azure Storage-accounts voor lange termijn/archiverings opslag.

* [Para meters voor het bewaren van Logboeken instellen voor Log Analytics-werk ruimten](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="26-monitor-and-review-logs"></a>2,6: Logboeken bewaken en controleren

**Hulp**: Schakel Diagnostische instellingen voor Azure-activiteiten logboek in, evenals de diagnostische instellingen voor uw Azure-toepassing gateway en verzend de logboeken naar een log Analytics-werk ruimte. Query's uitvoeren in Log Analytics om termen te zoeken, trends te identificeren, patronen te analyseren en veel andere inzichten te bieden op basis van de verzamelde gegevens.

Gebruik Azure Monitor voor netwerken voor een uitgebreid overzicht van de status en metrische gegevens voor alle geïmplementeerde netwerk bronnen, met inbegrip van uw Azure-toepassing gateways.

Optioneel kunt u gegevens in-en inschakelen voor Azure Sentinel of een SIEM van derden.

* [Diagnostische instellingen voor Azure-activiteiten logboek inschakelen](../azure-monitor/platform/activity-log.md)

* [Diagnostische instellingen inschakelen voor Azure-toepassing gateway](./application-gateway-diagnostics.md)

* [Azure Monitor voor netwerken gebruiken](../azure-monitor/insights/network-insights-overview.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: waarschuwingen inschakelen voor afwijkende activiteiten

**Richt lijnen**: implementeer de Azure Web Application firewall (WAF) v2 SKU voor essentiële webtoepassingen voor extra inspectie van inkomend verkeer. Web Application firewall (WAF) is een service (functie van Azure-toepassing gateway) waarmee uw webtoepassingen gecentraliseerd worden beschermd tegen veelvoorkomende aanvallen en beveiligings problemen. Met Azure WAF kunt u uw Azure App Service web-apps beveiligen door inkomend webverkeer te controleren om aanvallen zoals SQL-injecties, cross-site scripting, uploads van malware en DDoS-aanvallen te blok keren. WAF is gebaseerd op regels van de OWASP (open Web Application Security project) kern regel sets 3,1 (alleen WAF_v2), 3,0 en 2.2.9.

Schakel Diagnostische instellingen voor Azure-activiteiten logboek in, evenals de diagnostische instellingen voor uw Azure-WAF en verzend de logboeken naar een Log Analytics-werk ruimte. Query's uitvoeren in Log Analytics om termen te zoeken, trends te identificeren, patronen te analyseren en veel andere inzichten te bieden op basis van de verzamelde gegevens. U kunt waarschuwingen maken op basis van uw Log Analytics werkruimte query's.

Gebruik Azure Monitor voor netwerken voor een uitgebreid overzicht van de status en metrische gegevens voor alle geïmplementeerde netwerk bronnen, met inbegrip van uw Azure-toepassing gateways. In de console Azure Monitor voor netwerken kunt u waarschuwingen voor Azure-toepassing gateway weer geven en maken.

* [Azure WAF implementeren](../web-application-firewall/ag/create-waf-policy-ag.md)

* [Diagnostische instellingen voor Azure-activiteiten logboek inschakelen](../azure-monitor/platform/activity-log.md)

* [Diagnostische instellingen inschakelen voor Azure-toepassing gateway](./application-gateway-diagnostics.md)

* [Azure Monitor voor netwerken gebruiken](../azure-monitor/insights/network-insights-overview.md)

* [Waarschuwingen maken in azure](../azure-monitor/learn/tutorial-response.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="28-centralize-anti-malware-logging"></a>2,8: registratie van anti-malware centraliseren

**Richt lijnen**: implementeer Azure Web Application firewall (WAF) v2 voor essentiële webtoepassingen voor extra inspectie van inkomend verkeer. Web Application firewall (WAF) is een service (functie van Azure-toepassing gateway) waarmee uw webtoepassingen gecentraliseerd worden beschermd tegen veelvoorkomende aanvallen en beveiligings problemen. Met Azure WAF kunt u uw Azure App Service web-apps beveiligen door inkomend webverkeer te controleren om aanvallen zoals SQL-injecties, cross-site scripting, uploads van malware en DDoS-aanvallen te blok keren.

Diagnostische instellingen configureren voor uw Azure-toepassing gateway-implementaties. Diagnostische instellingen worden gebruikt voor het configureren van het exporteren van streaming van platform logboeken en metrische gegevens voor een resource naar het doel van uw keuze (opslag accounts, Event Hubs en Log Analytics).

* [Azure WAF implementeren](../web-application-firewall/ag/create-waf-policy-ag.md)

* [Diagnostische instellingen voor Azure WAF configureren](./application-gateway-diagnostics.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="29-enable-dns-query-logging"></a>2,9: DNS-query logboek registratie inschakelen

**Richt lijnen**: niet van toepassing; Azure-toepassing-gateway verwerkt of produceert geen door de gebruiker toegankelijke logboeken die betrekking hebben op DNS.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="210-enable-command-line-audit-logging"></a>2,10: controle logboek registratie op opdracht regel inschakelen

**Richt lijnen**: niet van toepassing; deze richt lijn is bedoeld voor IaaS Compute-resources.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

## <a name="identity-and-access-control"></a>Identiteits- en toegangsbeheer

*Zie [beveiligings beheer: identiteits-en toegangs beheer](../security/benchmarks/security-control-identity-access-control.md)voor meer informatie.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: een inventaris van beheerders accounts onderhouden

**Hulp**: Azure Active Directory (AD) heeft ingebouwde rollen die expliciet moeten worden toegewezen en waarop query's kunnen worden doorzocht. Gebruik de Azure AD Power shell-module om ad hoc-query's uit te voeren om accounts te detecteren die lid zijn van beheer groepen.

* [Een directory-rol verkrijgen in azure AD met Power shell](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

* [Leden van een directory-rol in azure AD ophalen met Power shell](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="32-change-default-passwords-where-applicable"></a>3,2: standaard wachtwoorden wijzigen indien van toepassing

**Hulp**: het beheer van toegang tot Azure-toepassing gateway wordt geregeld via Azure Active Directory (AD). Azure AD heeft niet het concept van standaard wachtwoorden.

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: speciale beheerders accounts gebruiken

**Richt lijnen**: Maak standaard procedures voor het gebruik van specifieke beheerders accounts. Gebruik Azure Security Center identiteits-en toegangs beheer om het aantal beheerders accounts te bewaken.

Daarnaast kunt u aanbevelingen van Azure Security Center of ingebouwde Azure-beleids regels gebruiken om u te helpen bij het bijhouden van specifieke beheerders accounts, zoals:
- Er moet meer dan één eigenaar zijn toegewezen aan uw abonnement
- Afgeschafte accounts met eigenaarsmachtigingen moeten worden verwijderd uit uw abonnement
- Externe accounts met eigenaarsmachtigingen moeten worden verwijderd uit uw abonnement

* [Azure Security Center gebruiken om identiteit en toegang te bewaken (preview)](../security-center/security-center-identity-access.md)

* [Azure Policy gebruiken](../governance/policy/tutorials/create-and-manage.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: eenmalige aanmelding (SSO) met Azure Active Directory gebruiken

**Richt lijnen**: gebruik een Azure-app-registratie (Service-Principal) om een token op te halen dat kan worden gebruikt om te communiceren met uw Azure-toepassing gateways via API-aanroepen.

* [Azure REST-Api's aanroepen](/rest/api/azure/#how-to-call-azure-rest-apis-with-postman)

* [Uw client toepassing (Service-Principal) registreren bij Azure AD](/rest/api/azure/#register-your-client-application-with-azure-ad)

* [Informatie over Azure Recovery Services-API](/rest/api/recoveryservices/)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: multi-factor Authentication gebruiken voor alle op Azure Active Directory gebaseerde toegang

**Richt lijnen**: Schakel Azure AD MFA in en volg Azure Security Center aanbevelingen voor identiteits-en toegangs beheer.

* [MFA inschakelen in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

* [Identiteit en toegang bewaken in Azure Security Center](../security-center/security-center-identity-access.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: gebruik speciale machines (privileged Access workstations) voor alle beheer taken

**Richt lijnen**: gebruik paw's (privileged Access workstations) met MFA dat is geconfigureerd om Azure-resources aan te melden en te configureren.

* [Meer informatie over privileged Access workstations](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

* [MFA inschakelen in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: Logboeken en waarschuwingen voor verdachte activiteiten van beheerders accounts

**Hulp**: gebruik Azure Active Directory beveiligings rapporten voor het genereren van Logboeken en waarschuwingen wanneer verdachte of onveilige activiteiten in de omgeving worden uitgevoerd. Gebruik Azure Security Center om identiteits-en toegangs activiteiten te bewaken.

* [Azure AD-gebruikers identificeren die zijn gemarkeerd voor riskante activiteiten](../active-directory/identity-protection/overview-identity-protection.md)

* [Identiteits- en toegangsactiviteiten van gebruikers controleren in Azure Security Center](../security-center/security-center-identity-access.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: Azure-resources alleen beheren vanaf goedgekeurde locaties

**Hulp**: gebruik benoemde locaties voor voorwaardelijke toegang om alleen toegang toe te staan vanaf specifieke logische groepen met IP-adresbereiken of landen/regio's.

* [Benoemde locaties configureren in azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="39-use-azure-active-directory"></a>3,9: Azure Active Directory gebruiken

**Hulp**: gebruik Azure Active Directory (Aad) als centrale verificatie-en autorisatie systeem. AAD beveiligt gegevens door gebruik te maken van sterke versleuteling voor gegevens in rust en tijdens de overdracht. AAD bevat ook zouten, hashes en veilige gebruikers referenties.

* [Een AAD-exemplaar maken en configureren](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: regel matig gebruikers toegang controleren en afstemmen

**Richt lijnen**: Azure AD biedt logboeken waarmee u verlopen accounts kunt detecteren. Daarnaast kunt u Azure Identity Access revisies gebruiken om groepslid maatschappen en de toegang tot bedrijfs toepassingen en roltoewijzingen op efficiënte wijze te beheren. Gebruikers toegang kan regel matig worden gecontroleerd om ervoor te zorgen dat alleen de juiste gebruikers toegang hebben.

* [Meer informatie over Azure AD-rapportage](../active-directory/reports-monitoring/index.yml)

* [Beoordelingen over Azure Identity Access gebruiken](../active-directory/governance/access-reviews-overview.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: controle pogingen om toegang te krijgen tot gedeactiveerde referenties

**Richt lijnen**: u hebt toegang tot de Azure AD-aanmeldings activiteit, controle en risico gebeurtenis logboek bronnen, waarmee u kunt integreren met elk Siem/bewakings programma.

U kunt dit proces stroom lijnen door Diagnostische instellingen voor Azure Active Directory gebruikers accounts te maken en de audit logboeken en aanmeldings logboeken te verzenden naar een Log Analytics-werk ruimte. U kunt de gewenste waarschuwingen configureren in Log Analytics werk ruimte.

* [Azure-activiteitenlogboeken integreren in Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: waarschuwing voor de afwijking van het aanmeldings gedrag van accounts

**Hulp**: gebruik functies van Azure AD Identity Protection en risico detectie om automatische antwoorden te configureren op gedetecteerde verdachte acties die betrekking hebben op gebruikers identiteiten. U kunt ook gegevens opnemen in azure Sentinel voor verder onderzoek.

* [Riskante Azure AD-aanmeldingen weergeven](../active-directory/identity-protection/overview-identity-protection.md)

* [Risico beleid voor identiteits beveiliging configureren en inschakelen](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

* [Azure-Sentinel onboarden](../sentinel/quickstart-onboard.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: micro soft biedt toegang tot relevante klant gegevens tijdens ondersteunings scenario's

**Richt lijnen**: niet van toepassing; Klanten-lockbox is niet van toepassing op Azure-toepassing gateway.

* [Lijst met door Klanten-lockbox ondersteunde services](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: niet van toepassing

## <a name="data-protection"></a>Gegevensbeveiliging

*Zie [beveiligings beheer: gegevens beveiliging](../security/benchmarks/security-control-data-protection.md)voor meer informatie.*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: een inventaris van gevoelige informatie onderhouden

**Hulp**: Tags gebruiken om Azure-resources te helpen bij het bijhouden of verwerken van gevoelige informatie.

* [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: systemen isoleren die gevoelige informatie opslaan of verwerken

**Richt lijnen**: afzonderlijke abonnementen en/of beheer groepen implementeren voor ontwikkeling, testen en productie. Zorg ervoor dat voor alle Virtual Network Azure-toepassing gateway-subnet-implementaties een netwerk beveiligings groep (NSG) is toegepast die specifiek is voor de vertrouwde poorten en bronnen van uw toepassing. Hoewel netwerk beveiligings groepen worden ondersteund op Azure-toepassing gateway, zijn er enkele beperkingen en vereisten waaraan moet worden voldaan om ervoor te zorgen dat uw NSG en Azure-toepassing gateway naar verwachting werken.

* [Aanvullende Azure-abonnementen maken](../cost-management-billing/manage/create-subscription.md)

* [Beheergroepen maken](../governance/management-groups/create-management-group-portal.md)

* [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

* [Meer informatie over de beperkingen en vereisten voor het gebruik van Nsg's met Azure-toepassing gateway](./configuration-overview.md)

* [Een NSG maken met een beveiligings configuratie](../virtual-network/tutorial-filter-network-traffic.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: niet-geautoriseerde overdracht van gevoelige gegevens controleren en blok keren

**Richt lijnen**: Zorg ervoor dat voor alle Virtual Network Azure-toepassing gateway-subnet-implementaties een netwerk beveiligings groep (NSG) is toegepast die specifiek is voor de vertrouwde poorten en bronnen van uw toepassing. Beperk uitgaand verkeer alleen voor vertrouwde locaties om de dreiging van gegevens exfiltration te helpen beperken. Hoewel netwerk beveiligings groepen worden ondersteund op Azure-toepassing gateway, zijn er enkele beperkingen en vereisten waaraan moet worden voldaan om ervoor te zorgen dat uw NSG en Azure-toepassing gateway naar verwachting werken.

* [Meer informatie over de beperkingen en vereisten voor het gebruik van Nsg's met Azure-toepassing gateway](./configuration-overview.md)

* [Een NSG maken met een beveiligings configuratie](../virtual-network/tutorial-filter-network-traffic.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Gedeeld

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: alle gevoelige gegevens in de overdracht versleutelen

**Richt lijnen**: end-to-end-versleuteling configureren met TLS voor uw Azure-toepassing gateways.

* [End-to-end TLS configureren met behulp van Azure-toepassing gateway](./end-to-end-ssl-portal.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Gedeeld

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: een actief detectie hulpprogramma gebruiken om gevoelige gegevens te identificeren

**Richt lijnen**: niet van toepassing, de Azure-toepassing-gateway slaat geen klant gegevens op rest op.

Micro soft beheert de onderliggende infra structuur voor Azure-toepassing gateway en heeft strikte controles geïmplementeerd om verlies of bloot stelling van klant gegevens te voor komen.

* [Informatie over beveiliging van klantgegevens in Azure](../security/fundamentals/protection-customer-data.md)

**Monitoring door Azure Security Center**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: Azure RBAC gebruiken om de toegang tot resources te beheren

**Richt lijnen**: gebruik Azure RBAC (op rollen gebaseerd toegangs beheer) om de toegang tot het besturings element voor de Azure-toepassing gateway (de Azure Portal) te beheren.

* [Azure RBAC configureren](../role-based-access-control/role-assignments-portal.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: voor komen dat gegevens verlies op basis van host wordt gebruikt voor het afdwingen van toegangs beheer

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor IaaS Compute-resources.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: gevoelige informatie op rest versleutelen

**Richt lijnen**: niet van toepassing; De Azure-toepassing-gateway slaat geen klant gegevens op.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: wijzigingen in essentiële Azure-resources vastleggen en waarschuwen

**Hulp**: gebruik Azure monitor met het Azure-activiteiten logboek om waarschuwingen te maken voor wanneer wijzigingen worden aangebracht in productie-Azure-toepassing gateway-exemplaren, evenals andere kritieke of verwante resources.

* [Waarschuwingen maken voor gebeurtenissen in het Azure-activiteiten logboek](../azure-monitor/platform/alerts-activity-log.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

## <a name="vulnerability-management"></a>Beheer van beveiligingsproblemen

*Zie [beveiligings beheer: beveiligingslek beheer](../security/benchmarks/security-control-vulnerability-management.md)voor meer informatie.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: automatische hulpprogram ma's voor het scannen van beveiligings problemen uitvoeren

**Hulp**: momenteel niet beschikbaar; de evaluatie van beveiligings problemen in Azure Security Center is nog niet beschikbaar voor Azure-toepassing gateway.

Onderliggend platform gescand en bijgewerkt door micro soft. Bekijk beveiligings controles die beschikbaar zijn voor Azure-toepassing gateway om beveiligings problemen met betrekking tot de service configuratie te verminderen.

* [Functie dekking (met inbegrip van evaluatie van beveiligings problemen) voor Azure PaaS Services](../security-center/features-paas.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: geautomatiseerde oplossing voor patch beheer voor besturings systemen implementeren

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor IaaS Compute-resources.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: Implementeer een oplossing voor geautomatiseerd patch beheer voor software titels van derden

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor IaaS Compute-resources.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: vergelijken van back-to-back-problemen

**Richt lijnen**: nog niet beschikbaar; de evaluatie van beveiligings problemen in Azure Security Center is nog niet beschikbaar voor Azure-toepassing gateway.

Onderliggend platform gescand en bijgewerkt door micro soft. Bekijk beveiligings controles die beschikbaar zijn voor Azure-toepassing gateway om beveiligings problemen met betrekking tot de service configuratie te verminderen.

* [Functie dekking (met inbegrip van evaluatie van beveiligings problemen) voor Azure PaaS Services](../security-center/features-paas.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: een risico classificatie proces gebruiken om prioriteit te geven aan het herstel van ontdekte beveiligings problemen

**Richt lijnen**: nog niet beschikbaar; de evaluatie van beveiligings problemen in Azure Security Center is nog niet beschikbaar voor Azure-toepassing gateway.

Onderliggend platform gescand en bijgewerkt door micro soft. Bekijk beveiligings controles die beschikbaar zijn voor Azure-toepassing gateway om beveiligings problemen met betrekking tot de service configuratie te verminderen.

* [Functie dekking (met inbegrip van evaluatie van beveiligings problemen) voor Azure PaaS Services](../security-center/features-paas.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

## <a name="inventory-and-asset-management"></a>Inventarisatie en asset-management

*Zie voor meer informatie [beveiligings beheer: inventarisatie en activa beheer](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: automatische Asset-detectie oplossing gebruiken

**Hulp**: Azure resource Graph gebruiken voor het opvragen/detecteren van alle resources (zoals compute, opslag, netwerk, poorten en protocollen enz.) binnen uw abonnement (en). Zorg ervoor dat de juiste (Lees-) machtigingen voor uw Tenant en alle Azure-abonnementen en resources in uw abonnementen inventariseren.

Hoewel klassieke Azure-resources kunnen worden gedetecteerd via resource grafiek, is het raadzaam om Azure Resource Manager resources te maken en te gebruiken.

* [Query's maken met Azure resource Graph](../governance/resource-graph/first-query-portal.md)

* [Uw Azure-abonnementen weer geven](/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

* [Meer informatie over Azure RBAC](../role-based-access-control/overview.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="62-maintain-asset-metadata"></a>6,2: meta gegevens van activa onderhouden

**Richt lijnen**: Tags Toep assen op Azure-resources die meta gegevens geven om ze logisch in een taxonomie te organiseren.

* [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: niet-geautoriseerde Azure-resources verwijderen

**Richt lijnen**: Gebruik labels, beheer groepen en afzonderlijke abonnementen, waar nodig, om Azure-resources te organiseren en bij te houden. Sluit de inventaris regel matig af en zorg ervoor dat niet-geautoriseerde resources tijdig worden verwijderd uit het abonnement.

Daarnaast kunt u met Azure Policy beperkingen opleggen aan het type resources dat kan worden gemaakt in klant abonnement (en) met de volgende ingebouwde beleids definities:
- Niet toegestane resourcetypen
- Toegestane brontypen

* [Aanvullende Azure-abonnementen maken](../cost-management-billing/manage/create-subscription.md)

* [Beheergroepen maken](../governance/management-groups/create-management-group-portal.md)

* [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6,4: een inventaris van goedgekeurde Azure-resources definiëren en onderhouden

**Richt lijnen**: goedgekeurde Azure-resources definiëren.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: monitor voor niet-goedgekeurde Azure-resources

**Hulp**: gebruik Azure Policy om beperkingen toe te voegen voor het type resources dat kan worden gemaakt in uw abonnement (en).

Gebruik Azure resource Graph voor het opvragen/detecteren van resources binnen hun abonnement (en). Zorg ervoor dat alle Azure-resources die aanwezig zijn in de omgeving, zijn goedgekeurd.

* [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

* [Query's maken met Azure Graph](../governance/resource-graph/first-query-portal.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: monitor voor niet-goedgekeurde software toepassingen binnen reken resources

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor IaaS Compute-resources.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: niet-goedgekeurde Azure-resources en software toepassingen verwijderen

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor IaaS Compute-resources.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="68-use-only-approved-applications"></a>6,8: alleen goedgekeurde toepassingen gebruiken

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor IaaS Compute-resources.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="69-use-only-approved-azure-services"></a>6,9: alleen goedgekeurde Azure-Services gebruiken

**Hulp: gebruik** Azure Policy om beperkingen toe te voegen voor het type resources dat kan worden gemaakt in klant abonnement (en) met de volgende ingebouwde beleids definities:
- Niet toegestane resourcetypen
- Toegestane brontypen

* [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

* [Een specifiek resource type weigeren met Azure Policy](../governance/policy/samples/index.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: een inventaris van goedgekeurde software titels onderhouden

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor IaaS Compute-resources.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: de mogelijkheid van gebruikers om te communiceren met Azure Resource Manager beperken

**Hulp** bij het configureren van voorwaardelijke toegang van Azure om gebruikers de mogelijkheid te bieden om te communiceren met Azure Resource Manager door ' blok toegang ' te configureren voor de app Microsoft Azure management.

* [Voorwaardelijke toegang configureren om de toegang tot Azure Resource Manager te blok keren](../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: de mogelijkheid van gebruikers om scripts uit te voeren binnen reken bronnen beperken

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor IaaS Compute-resources.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: toepassingen met een hoog risico fysiek of logisch scheiden

**Richt lijnen**: afzonderlijke abonnementen en/of beheer groepen implementeren voor ontwikkeling, testen en productie. Zorg ervoor dat voor alle Virtual Network Azure-toepassing gateway-subnet-implementaties een netwerk beveiligings groep (NSG) is toegepast die specifiek is voor de vertrouwde poorten en bronnen van uw toepassing. Hoewel netwerk beveiligings groepen worden ondersteund op Azure-toepassing gateway, zijn er enkele beperkingen en vereisten waaraan moet worden voldaan om ervoor te zorgen dat uw NSG en Azure-toepassing gateway naar verwachting werken.

* [Aanvullende Azure-abonnementen maken](../cost-management-billing/manage/create-subscription.md)

* [Beheergroepen maken](../governance/management-groups/create-management-group-portal.md)

* [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

* [Meer informatie over de beperkingen en vereisten voor het gebruik van Nsg's met Azure-toepassing gateway](./configuration-overview.md)

* [Een NSG maken met een beveiligings configuratie](../virtual-network/tutorial-filter-network-traffic.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="secure-configuration"></a>Veilige configuratie

*Zie [beveiligings beheer: beveiligde configuratie](../security/benchmarks/security-control-secure-configuration.md)voor meer informatie.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: veilige configuraties instellen voor alle Azure-resources

**Richt lijnen**: standaard beveiligings configuraties definiëren en implementeren voor netwerk instellingen die betrekking hebben op uw Azure-toepassing gateway-implementaties. Gebruik Azure Policy aliassen in de naam ruimte ' micro soft. netwerk ' om aangepaste beleids regels te maken voor het controleren of afdwingen van de netwerk configuratie van uw Azure-toepassing gateways, virtuele Azure-netwerken en netwerk beveiligings groepen. U kunt ook gebruikmaken van de ingebouwde beleids definitie.

* [Beschik bare Azure Policy aliassen weer geven](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: veilige configuraties van besturings systemen instellen

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor IaaS Compute-resources.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: Beveilig Azure-resource configuraties onderhouden

**Richt lijnen**: gebruik Azure Policy [deny] en [implementeren indien niet aanwezig] voor het afdwingen van beveiligde instellingen voor uw Azure-resources.

* [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

* [Azure Policy effecten begrijpen](../governance/policy/concepts/effects.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: veilige configuraties van besturings systemen onderhouden

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor IaaS Compute-resources.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: de configuratie van Azure-resources veilig opslaan

**Richt lijnen**: als u aangepaste definities van Azure-beleid gebruikt, kunt u Azure DevOps of Azure opslag plaatsen gebruiken om uw code veilig op te slaan en te beheren.

* [Code opslaan in azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [Documentatie voor Azure opslag plaatsen](/azure/devops/repos/index?view=azure-devops)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: aangepaste installatie kopieën van een besturings systeem veilig opslaan

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor IaaS Compute-resources.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: hulpprogram ma's voor configuratie beheer voor Azure-resources implementeren

**Hulp**: gebruik ingebouwde Azure Policy definities en Azure Policy aliassen in de naam ruimte ' micro soft. Network ' om aangepaste beleids regels te maken om systeem configuraties te Signa lering, te controleren en af te dwingen. Ontwikkel bovendien een proces en pijp lijn voor het beheren van beleids uitzonderingen.

* [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: hulpprogram ma's voor configuratie beheer voor besturings systemen implementeren

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor IaaS Compute-resources.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: geautomatiseerde configuratie bewaking voor Azure-resources implementeren

**Hulp**: gebruik ingebouwde Azure Policy definities en Azure Policy aliassen in de naam ruimte ' micro soft. Network ' om aangepaste beleids regels te maken om systeem configuraties te Signa lering, te controleren en af te dwingen. Gebruik Azure Policy [audit], [deny] en [implementeren indien niet aanwezig] om automatisch configuraties af te dwingen voor uw Azure-resources.

* [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: geautomatiseerde configuratie bewaking voor besturings systemen implementeren

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor IaaS Compute-resources.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="711-manage-azure-secrets-securely"></a>7,11: Azure-geheimen veilig beheren

**Richt lijnen**: gebruik beheerde identiteiten om uw Azure-toepassing gateway te voorzien van een automatisch beheerde identiteit in azure Active Directory (AD). Met beheerde identiteiten kunt u zich verifiëren bij elke service die ondersteuning biedt voor Azure AD-verificatie, met inbegrip van Key Vault, zonder enige referenties in uw code.

Gebruik Azure Key Vault om certificaten veilig op te slaan. Azure Key Vault is een door een platform beheerd geheim archief dat u kunt gebruiken voor het beveiligen van geheimen, sleutels en SSL-certificaten. Azure-toepassing gateway ondersteunt de integratie met Key Vault voor server certificaten die zijn gekoppeld aan listeners met HTTPS-functionaliteit. Deze ondersteuning is beperkt tot de SKU van Application Gateway v2.

* [SSL-beëindiging met Key Vault certificaten configureren met behulp van Azure PowerShell](./configure-keyvault-ps.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: identiteiten veilig en automatisch beheren

**Richt lijnen**: gebruik beheerde identiteiten om uw Azure-toepassing gateway te voorzien van een automatisch beheerde identiteit in azure Active Directory (AD). Met beheerde identiteiten kunt u zich verifiëren bij elke service die ondersteuning biedt voor Azure AD-verificatie, met inbegrip van Key Vault, zonder enige referenties in uw code.

Gebruik Azure Key Vault om certificaten veilig op te slaan. Azure Key Vault is een door een platform beheerd geheim archief dat u kunt gebruiken voor het beveiligen van geheimen, sleutels en SSL-certificaten. Azure-toepassing gateway ondersteunt de integratie met Key Vault voor server certificaten die zijn gekoppeld aan listeners met HTTPS-functionaliteit. Deze ondersteuning is beperkt tot de SKU van Application Gateway v2.

* [SSL-beëindiging met Key Vault certificaten configureren met behulp van Azure PowerShell](./configure-keyvault-ps.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: onbedoelde referentie blootstelling elimineren

**Richt lijnen**: referentie scanner implementeren om referenties in code te identificeren. Door het gebruik van Credential Scanner worden gebruikers ook aangemoedigd om gedetecteerde referenties naar veiligere locaties, zoals Azure Key Vault, te verplaatsen.

* [Referentie scanner instellen](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="malware-defense"></a>Beveiliging tegen malware

*Zie [beveiligings beheer: verdediging tegen malware](../security/benchmarks/security-control-malware-defense.md)voor meer informatie.*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: centraal beheerde anti-malware-software gebruiken

**Richt lijnen**: implementeer Azure Web Application firewall (WAF) v2 voor essentiële webtoepassingen voor extra inspectie van inkomend verkeer. Web Application firewall (WAF) is een service (functie van Azure-toepassing gateway) waarmee uw webtoepassingen gecentraliseerd worden beschermd tegen veelvoorkomende aanvallen en beveiligings problemen. Met Azure WAF kunt u uw Azure App Service web-apps beveiligen door inkomend webverkeer te controleren om aanvallen zoals SQL-injecties, cross-site scripting, uploads van malware en DDoS-aanvallen te blok keren.

Diagnostische instellingen configureren voor uw Azure-toepassing gateway-implementaties. Diagnostische instellingen worden gebruikt voor het configureren van het exporteren van streaming van platform logboeken en metrische gegevens voor een resource naar het doel van uw keuze (opslag accounts, Event Hubs en Log Analytics).

* [Azure WAF implementeren](../web-application-firewall/ag/create-waf-policy-ag.md)

* [Diagnostische instellingen voor Azure WAF configureren](./application-gateway-diagnostics.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: scan bestanden die moeten worden geüpload naar niet-reken resources van Azure

**Richt lijnen**: niet van toepassing; De Azure-toepassing-gateway slaat geen klant gegevens op.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: controleren of anti-malware-software en hand tekeningen zijn bijgewerkt

**Richt lijnen**: wanneer u Azure Web Application firewall (WAF) gebruikt, kunt u WAF-beleid configureren. Een WAF-beleid bestaat uit twee typen beveiligings regels: aangepaste regels die door de klant zijn gemaakt en beheerde regel sets die een verzameling van door Azure beheerde vooraf geconfigureerde regels zijn. Door Azure beheerde regelsets bieden een eenvoudige manier om beveiliging te implementeren op basis van een gemeenschappelijke set beveiligingsbedreigingen. Aangezien dergelijke regelsets worden beheerd door Azure, worden de regels zo nodig bijgewerkt om ook bescherming te bieden tegen nieuwe aanvalshandtekeningen.

* [Meer informatie over door Azure beheerde WAF-regel sets](../web-application-firewall/ag/ag-overview.md#waf-policy-and-rules)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Gedeeld

## <a name="data-recovery"></a>Gegevensherstel

*Zie [beveiligings beheer: gegevens herstel](../security/benchmarks/security-control-data-recovery.md)voor meer informatie.*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: controleren op regel matige automatische back-ups

**Hulp**: de Azure-toepassing-gateway slaat geen klant gegevens op. Als u echter aangepaste definities van Azure-beleid gebruikt, kunt u Azure DevOps of Azure opslag plaatsen gebruiken om uw code veilig op te slaan en te beheren.

Azure DevOps Services maakt gebruik van veel van de functies van Azure Storage om te zorgen voor de beschikbaarheid van gegevens in het geval van hardwarestoringen, serviceonderbrekingen of een regionale noodsituatie. Daarnaast volgt het Azure DevOps-team procedures voor het beveiligen van gegevens tegen onbedoelde verwijdering of verwijdering met kwaadaardige opzet.

* [Inzicht in de beschik baarheid van gegevens in azure DevOps](/azure/devops/organizations/security/data-protection?view=azure-devops#data-availability)

* [Code opslaan in azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [Documentatie voor Azure opslag plaatsen](/azure/devops/repos/index?view=azure-devops)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: volledige back-ups van het systeem uitvoeren en een back-up maken van een door de klant beheerde sleutels

**Hulp**: back-up van door klant beheerde certificaten binnen Azure Key Vault.

* [Back-ups van sleutel kluis certificaten in azure](/powershell/module/azurerm.keyvault/backup-azurekeyvaultcertificate)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: alle back-ups valideren, inclusief door de klant beheerde sleutels

**Richt lijnen**: het herstellen van een back-up van door de klant beheerde certificaten testen.

* [Sleutel kluis certificaten herstellen](/powershell/module/azurerm.keyvault/restore-azurekeyvaultcertificate)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: zorg voor de bescherming van back-ups en door de klant beheerde sleutels

**Hulp**: Zorg ervoor dat de functie voor voorlopig verwijderen is ingeschakeld voor Azure Key Vault. Met zacht verwijderen kunt u verwijderde sleutel kluizen en kluis objecten zoals sleutels, geheimen en certificaten herstellen.

* [De tijdelijke verwijdering van Azure Key Vault gebruiken](../key-vault/general/key-vault-recovery.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

## <a name="incident-response"></a>Reageren op incidenten

*Zie voor meer informatie [beveiligings beheer: reactie op incidenten](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: een hand leiding voor reactie op incidenten maken

**Richtlijnen**: Stel voor uw organisatie een responshandleiding op voor gebruik bij incidenten. Zorg ervoor dat er schriftelijke responsplannen zijn waarin alle rollen van het personeel worden gedefinieerd, evenals alle fasen in het afhandelen/managen van incidenten, vanaf de detectie van het incident tot een evaluatie ervan achteraf.

* [Werk stroom automatisering configureren in Azure Security Center](../security-center/security-center-planning-and-operations-guide.md)

* [Richt lijnen voor het bouwen van uw eigen beveiligings incident antwoord proces](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Micro soft Security Response Center anatomie van een incident](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Klant kan ook gebruikmaken van de hand leiding voor de verwerking van het computer beveiligings incident van het NIST om hulp te bieden bij het maken van een eigen incident respons plan](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: een beoordelings procedure voor incidenten en prioriteits procedures maken

**Hulp**: Security Center wijst aan elke waarschuwing een Ernst toe om u te helpen bepalen welke waarschuwingen het eerst moeten worden onderzocht. De ernst is gebaseerd op de manier waarop vertrouwen Security Center is in de zoek actie of het analyse programma dat wordt gebruikt om de waarschuwing te geven, evenals het betrouwbaarheids niveau dat er schadelijke bedoelingen zijn achter de activiteit die tot de waarschuwing heeft geleid.

Daarnaast kunt u ook duidelijk abonnementen markeren (voor bijvoorbeeld productie, niet-productie) en maak een naamgevings systeem om Azure-resources duidelijk te identificeren en te categoriseren.

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="103-test-security-response-procedures"></a>10,3: procedures voor beveiligings antwoorden testen

**Richt lijnen**: oefent oefeningen uit om de respons mogelijkheden van uw systeem op een gewone uitgebracht te testen. Stel vast waar zich zwakke plekken en hiaten bevinden, en wijzig zo nodig het plan.

* [Raadpleeg de publicatie van het NIST: hand leiding voor het testen, trainen en uitoefenen van Program Ma's voor IT-plannen en-mogelijkheden](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: contact gegevens van het beveiligings incident opgeven en waarschuwings meldingen configureren voor beveiligings incidenten

**Hulp**: contact gegevens van beveiligings incidenten worden door micro soft gebruikt om contact met u op te nemen als het micro soft Security Response Center (MSRC) detecteert dat de gegevens van de klant zijn geopend door een onrecht matige of niet-gemachtigde partij. Bekijk incidenten na het feit om te controleren of de problemen zijn opgelost.

* [De Azure Security Center Security-contact persoon instellen](../security-center/security-center-provide-security-contact-details.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: beveiligings waarschuwingen opnemen in uw reactie systeem van uw incident

**Richt lijnen**: uw Azure Security Center waarschuwingen en aanbevelingen exporteren met de functie continue export. Met doorlopend exporteren kunt u waarschuwingen en aanbevelingen hand matig of op een doorlopende manier exporteren. U kunt de Azure Security Center Data Connector gebruiken om de waarschuwingen naar Azure Sentinel te streamen.

* [Continue export configureren](../security-center/continuous-export.md)

* [Waarschuwingen streamen naar Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: de reactie op beveiligings waarschuwingen automatiseren

**Hulp**: gebruik de functie werk stroom automatisering in azure Security Center om automatisch reacties te activeren via ' Logic apps ' in beveiligings waarschuwingen en aanbevelingen.

* [Werk stroom automatisering en Logic Apps configureren](../security-center/workflow-automation.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="penetration-tests-and-red-team-exercises"></a>Penetratietests en Red Team-oefeningen

*Zie voor meer informatie [Security Control: Indringings tests en Red team-oefeningen](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: voert regel matig indringings tests van uw Azure-resources uit en zorgt voor herbemiddeling van alle essentiële beveiligings resultaten

**Hulp**: 

* [Volg de richt lijnen van micro soft om ervoor te zorgen dat de indringings tests niet worden geschonden door het micro soft-beleid](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

* [U vindt hier meer informatie over de strategie van micro soft en de uitvoering van Red Teaming en live site indringings tests met door micro soft beheerde Cloud infrastructuur,-services en-toepassingen.](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Gedeeld

## <a name="next-steps"></a>Volgende stappen

- Zie de [Azure Security-Bench Mark](../security/benchmarks/overview.md)
- Meer informatie over [Azure-beveiligingsbasislijnen](../security/benchmarks/security-baselines-overview.md)