---
title: Azure-beveiligingsbasislijn voor API Management
description: De API Management beveiligingsbasislijn biedt procedurele richtlijnen en resources voor het implementeren van de beveiligingsaanbevelingen die zijn opgegeven in de Azure Security Benchmark.
author: msmbaldwin
ms.service: api-management
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark, devx-track-azurepowershell
ms.openlocfilehash: 488fa4738918849759fbefa86ef1e9730b3b0ed0
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107813484"
---
# <a name="azure-security-baseline-for-api-management"></a>Azure-beveiligingsbasislijn voor API Management

Deze beveiligingsbasislijn past richtlijnen van [Azure Security Benchmark versie 1.0](../security/benchmarks/overview-v1.md) toe op API Management. De Azure Security-benchmark biedt aanbevelingen voor hoe u uw cloudoplossingen in Azure kunt beveiligen.
De inhoud wordt gegroepeerd op basis van de **beveiligingscontroles** die zijn gedefinieerd door de Azure Security Benchmark en de gerelateerde richtlijnen die van toepassing zijn op API Management. **Besturingselementen** die niet van API Management zijn uitgesloten.

 
Zie het volledige API Management toewijzingsbestand voor beveiligingsbasislijnen om te zien hoe API Management volledig is toegewezen aan de Azure [Security-API Management](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)benchmark.

## <a name="network-security"></a>Netwerkbeveiliging

*Zie [Azure Security Benchmark: netwerkbeveiliging](../security/benchmarks/security-control-network-security.md) voor meer informatie.*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Azure-resources binnen virtuele netwerken beveiligen

**Richtlijnen:** Azure API Management kunnen worden geïmplementeerd in een Azure Virtual Network (Vnet), zodat het toegang heeft tot back-endservices in het netwerk. De ontwikkelaarsportal en API Management-gateway kunnen zo worden geconfigureerd dat ze toegankelijk zijn via internet (extern) of alleen binnen het Vnet (intern).

- Extern: de API Management-gateway en de ontwikkelaarsportal zijn toegankelijk vanaf het openbare internet via een externe load balancer. De gateway heeft toegang tot resources in het virtuele netwerk.

- Intern: de API Management-gateway en de ontwikkelaarsportal zijn alleen toegankelijk vanuit het virtuele netwerk via een interne load balancer. De gateway heeft toegang tot resources in het virtuele netwerk.

Het inkomende en uitgaande verkeer naar het subnet waarin API Management is geïmplementeerd, kan worden beheerd met behulp van de netwerkbeveiligingsgroep.

- [Azure API Management gebruiken met virtuele netwerken](api-management-using-with-vnet.md)

- [Azure API Management-service gebruiken met een intern virtueel netwerk](api-management-using-with-internal-vnet.md)

- [API Management in een intern VNET integreren met Application Gateway](api-management-howto-integrate-internal-vnet-appgateway.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2: De configuratie en het verkeer van virtuele netwerken, subnetten en netwerkinterfaces bewaken en in een logboek plaatsen

**Richtlijnen:** Inkomende en uitgaande verkeer naar het subnet waarin API Management is geïmplementeerd, kunnen worden beheerd met behulp van netwerkbeveiligingsgroepen (NSG's). Implementeer een NSG in API Management subnet en schakel NSG-stroomlogboeken in en verzend logboeken naar een Azure Storage voor verkeerscontrole. U kunt ook NSG-stroomlogboeken verzenden naar een Log Analytics-werkruimte en Traffic Analytics om inzicht te krijgen in de verkeersstroom in uw Azure-cloud. Enkele voordelen van Traffic Analytics zijn de mogelijkheid om netwerkactiviteit te visualiseren en hotspots te identificeren, beveiligingsrisico's te identificeren, verkeersstroompatronen te begrijpen en onjuiste netwerkconfiguraties aan te wijzen.

Let op: wanneer u een NSG configureert op het API Management subnet, zijn er een aantal poorten die open moeten zijn. Als een van deze poorten niet beschikbaar is, API Management mogelijk niet goed werken en is deze mogelijk ontoegankelijk.

- [NSG-configuraties voor Azure API Management](./api-management-using-with-vnet.md#-common-network-configuration-issues)

- [NSG-stroomlogboeken inschakelen](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Het inschakelen en gebruiken van Traffic Analytics](../network-watcher/traffic-analytics.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="13-protect-critical-web-applications"></a>1.3: Kritieke webtoepassingen beveiligen

**Richtlijnen:** Om kritieke web-/HTTP-API's te beveiligen, configureert u API Management binnen een Virtual Network (Vnet) in de interne modus en configureert u een Azure Application Gateway. Application Gateway is een PaaS-service. Het fungeert als een omgekeerde proxy en biedt L7-taakverdeling, routering, WAF (Web Application Firewall) en andere services.

Als u API Management in een intern VNet combineert met de front-Application Gateway, zijn de volgende scenario's mogelijk:
- Gebruik één resource API Management voor het blootstellen van alle API's aan zowel interne consumenten als externe consumenten.
- Gebruik één resource API Management voor het blootstellen van een subset api's aan externe consumenten.
- Een manier bieden om de toegang tot API Management van het openbare internet in of uit te schakelen.

Opmerking: deze functie is beschikbaar in de premium- en ontwikkelaarslaag van API Management.

- [Integratie van API Management in een intern VNET met Application Gateway](api-management-howto-integrate-internal-vnet-appgateway.md)

- [Inzicht Azure Application Gateway](../application-gateway/index.yml)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Communicatie met bekende schadelijke IP-adressen weigeren

**Richtlijnen:** Configureer API Management binnen een Virtual Network (Vnet) in de interne modus en configureer een Azure Application Gateway. Application Gateway is een PaaS-service. Het fungeert als een omgekeerde proxy en biedt L7-taakverdeling, routering, Web Application Firewall (WAF) en andere services.

Als u API Management in een intern VNet combineert met de Application Gateway front-end, zijn de volgende scenario's mogelijk:
- Gebruik één resource API Management voor het blootstellen van alle API's aan zowel interne als externe consumenten.
- Gebruik één resource API Management voor het blootstellen van een subset van API's aan externe consumenten.
- Een manier bieden om de toegang tot API Management van het openbare internet in en uit te schakelen.

Opmerking: deze functie is beschikbaar in de premium- en ontwikkelaarslaag van API Management.

Gebruik Azure Security Center Integrated Threat Intelligence om communicatie met bekende schadelijke of ongebruikte INTERNET-IP-adressen te weigeren.

- [Integratie van API Management in een intern VNET met Application Gateway](api-management-howto-integrate-internal-vnet-appgateway.md)

- [Meer Azure Application Gateway](../application-gateway/index.yml)

- [Inzicht Azure Security Center geïntegreerde bedreigingsinformatie](../security-center/azure-defender.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="15-record-network-packets"></a>1.5: Netwerkpakketten registreren

**Richtlijnen:** Inkomende en uitgaande verkeer naar het subnet waarin API Management is geïmplementeerd, kunnen worden beheerd met behulp van netwerkbeveiligingsgroepen (NSG's). Implementeer een NSG in API Management subnet en schakel NSG-stroomlogboeken in en verzend logboeken naar een Azure Storage-account voor verkeerscontrole. U kunt ook NSG-stroomlogboeken verzenden naar een Log Analytics-werkruimte en Traffic Analytics om inzicht te geven in de verkeersstroom in uw Azure-cloud. Enkele voordelen van Traffic Analytics zijn de mogelijkheid om netwerkactiviteit te visualiseren en hotspots te identificeren, beveiligingsrisico's te identificeren, verkeersstroompatronen te begrijpen en onjuiste netwerkconfiguraties aan te wijzen.

Let op: wanneer u een NSG configureert op het API Management subnet, zijn er een aantal poorten die open moeten zijn. Als een van deze poorten niet beschikbaar is, API Management mogelijk niet goed werken en is deze mogelijk ontoegankelijk.

- [NSG-configuraties voor Azure API Management](./api-management-using-with-vnet.md#-common-network-configuration-issues)

- [NSG-stroomlogboeken inschakelen](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Het inschakelen en gebruiken van Traffic Analytics](../network-watcher/traffic-analytics.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Op het netwerk gebaseerde inbraakdetectie-/inbraakpreventiesystemen (IDS/IPS) implementeren

**Richtlijnen:** configureer API Management binnen een Virtual Network (Vnet) in de interne modus en configureer een Azure Application Gateway. Application Gateway is een PaaS-service. Het fungeert als een omgekeerde proxy en biedt L7-taakverdeling, routering, WAF (Web Application Firewall) en andere services. Application Gateway WAF biedt bescherming tegen veelvoorkomende beveiligingsrisico's en beveiligingsproblemen en kan worden uitgevoerd in de volgende twee modi:
- Detectiemodus: Hiermee worden alle waarschuwingen voor bedreigingen gecontroleerd en vastgelegd. U kunt diagnostische logboekregistratie inschakelen voor Application Gateway in de sectie Diagnostische gegevens. U moet ervoor zorgen dat het WAF-logboek is geselecteerd en ingeschakeld. In de detectiemodus worden binnenkomende verzoeken niet geblokkeerd door Web Application Firewall.
- Preventiemodus: Blokkeert indringers en aanvallen die door de regels zijn gedetecteerd. De aanvaller krijgt een uitzondering '403 onbevoegde toegang' en de verbinding wordt verbroken. In de preventiemodus worden dergelijke aanvallen vastgelegd in de WAF-logboeken.

Als u API Management in een intern VNet combineert met de front-Application Gateway, zijn de volgende scenario's mogelijk:
- Gebruik één resource API Management voor het blootstellen van alle API's aan zowel interne consumenten als externe consumenten.
- Gebruik één resource API Management voor het blootstellen van een subset api's aan externe consumenten.
- Een manier bieden om de toegang tot API Management van het openbare internet in of uit te schakelen.

Opmerking: deze functie is beschikbaar in de premium- en ontwikkelaarslaag van API Management.

- [Integratie van API Management in een intern VNET met Application Gateway](api-management-howto-integrate-internal-vnet-appgateway.md)

- [Inzicht Azure Application Gateway WAF](../web-application-firewall/ag/ag-overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Verkeer naar webtoepassingen beheren

**Richtlijnen:** voor het beheren van verkeer dat naar web-/HTTP-API's stroomt, implementeert u API Management naar een Virtual Network (Vnet) dat is gekoppeld aan App Service Environment in de externe of interne modus.

Configureer in de interne modus een Azure Application Gateway voor API Management. Application Gateway is een PaaS-service. Het fungeert als een omgekeerde proxy en biedt L7-taakverdeling, routering, Web Application Firewall (WAF) en andere services. Application Gateway WAF biedt bescherming tegen veelvoorkomende beveiligingsrisico's en beveiligingsproblemen.

Als u API Management in een intern Vnet combineert met de Application Gateway front-end, zijn de volgende scenario's mogelijk:
- Gebruik één resource API Management voor het blootstellen van alle API's aan zowel interne als externe consumenten.
- Gebruik één resource API Management voor het blootstellen van een subset van API's aan externe consumenten.
- Een manier bieden om de toegang tot API Management van het openbare internet in en uit te schakelen.

Opmerking: deze functie is beschikbaar in de premium- en ontwikkelaarslaag van API Management.

- [Privé-API's blootstellen aan externe consumenten](/azure/architecture/example-scenario/apps/publish-internal-apis-externally)

- [Hoe u API Management binnen een Vnet](api-management-using-with-vnet.md)

- [Azure Web Application Firewall voor Azure Application Gateway](../web-application-firewall/ag/ag-overview.md)

- [Meer Azure Application Gateway](../application-gateway/overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: De complexiteit en administratieve overhead van netwerkbeveiligingsregels minimaliseren

**Richtlijnen:** gebruik Virtual Network servicetags (Vnet) om netwerktoegangsbesturingselementen te definiëren voor netwerkbeveiligingsgroepen (NSG's) die worden gebruikt op uw API Management subnetten. U kunt servicetags gebruiken in plaats van specifieke IP-adressen wanneer u beveiligingsregels maakt. Door de naam van de servicetag (bijvoorbeeld ApiManagement) op te geven in het juiste bron- of doelveld van een regel, kunt u het verkeer voor de bijbehorende service toestaan of weigeren. Microsoft beheert de adres voorvoegsels die worden omvat door de servicetag en werkt de servicetag automatisch bij wanneer adressen veranderen.

Let op: wanneer u een NSG configureert op het API Management subnet, zijn er een aantal poorten die open moeten zijn. Als een van deze poorten niet beschikbaar is, API Management mogelijk niet goed werken en is deze mogelijk ontoegankelijk.

- [Servicetags begrijpen en gebruiken](../virtual-network/service-tags-overview.md)

- [Vereiste poorten voor API Management](./api-management-using-with-vnet.md#-common-network-configuration-issues)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Standaardbeveiligingsconfiguraties voor netwerkapparaten onderhouden

**Richtlijnen:** standaardbeveiligingsconfiguraties definiëren en implementeren voor netwerkinstellingen die betrekking hebben op uw Azure API Management implementaties. Gebruik Azure Policy-aliassen in de naamruimten Microsoft.ApiManagement en Microsoft.Network om aangepaste beleidsregels te maken om de netwerkconfiguratie van uw Azure API Management-implementaties en gerelateerde resources te controleren of af te dwingen.

U kunt Azure Blueprints ook gebruiken om grootschalige Azure-implementaties te vereenvoudigen door belangrijke omgevingsartefacten te verpakken, zoals Azure Resource Manager-sjablonen, op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC) en beleidsregels in één blauwdrukdefinitie. U kunt de blauwdruk eenvoudig toepassen op nieuwe abonnementen, omgevingen en beheer afstemmen via versiebeheer.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Een Azure Blueprint maken](../governance/blueprints/create-blueprint-portal.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="110-document-traffic-configuration-rules"></a>1.10: Configuratieregels voor verkeer documenteren

**Richtlijnen:** Tags gebruiken voor netwerkbeveiligingsgroepen (NSG's) en andere resources met betrekking tot netwerkbeveiliging en verkeersstroom. Voor afzonderlijke NSG-regels kunt u het veld Beschrijving gebruiken om zakelijke behoeften en/of duur (enzovoort) op te geven voor regels die verkeer van/naar een netwerk toestaan.

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

- [Een Virtual Network](../virtual-network/quick-create-portal.md)

- [Een NSG maken met een beveiligings config](../virtual-network/tutorial-filter-network-traffic.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Geautomatiseerde hulpprogramma's gebruiken om netwerkresourceconfiguraties te bewaken en wijzigingen te detecteren

**Richtlijnen:** Gebruik azure-activiteitenlogboek om netwerkresourceconfiguraties te bewaken en wijzigingen te detecteren in netwerkresources die zijn gekoppeld aan uw Azure API Management implementaties. Maak waarschuwingen binnen Azure Monitor die worden getriggerd wanneer er wijzigingen in kritieke netwerkbronnen plaatsvinden.

- [Azure-activiteitenlogboekgebeurtenissen weergeven en ophalen](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Waarschuwingen maken in Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="logging-and-monitoring"></a>Logboekregistratie en bewaking

*Zie de Azure [Security Benchmark: Logging and Monitoring (Azure Security Benchmark: logboekregistratie en bewaking) voor meer informatie.](../security/benchmarks/security-control-logging-monitoring.md)*

### <a name="22-configure-central-security-log-management"></a>2.2: Centraal beheer van beveiligingslogboek configureren

**Richtlijnen:** gebruik in de Azure Monitor Log Analytics-werkruimte(s) om query's uit te voeren en analyses uit te voeren, logboeken naar Azure Storage te verzenden voor langetermijn-/archiveringsopslag of offlineanalyse, of exporteert logboeken naar andere analyse-oplossingen in Azure en elders met behulp van Azure Event Hubs. Azure API Management logboeken en metrische gegevens standaard Azure Monitor gegevens. Uitgebreidheid van de logboekregistratie kan worden geconfigureerd voor de hele service en per API.

Naast de Azure Monitor kan Azure API Management worden geïntegreerd met een of meer Azure-toepassing Insights-services. Logboekinstellingen voor Application Insights kunnen worden geconfigureerd per service of per API.

U kunt eventueel gegevens inschakelen en inschakelen voor Azure Sentinel of een SIEM (Security Incident and Event Management) van derden.

- [Diagnostische instellingen configureren](../azure-monitor/essentials/diagnostic-settings.md#create-in-azure-portal)

- [Onboarding van Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Aan de slag met Azure Monitor siem-integratie van derden](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

- [Aangepaste pijplijn voor logboekregistratie en analyse maken](api-management-howto-log-event-hubs.md)

- [Integreren met Azure-toepassing Insights](api-management-howto-app-insights.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Auditlogregistratie voor Azure-resources inschakelen

**Richtlijnen:** schakel diagnostische instellingen voor Azure-activiteitenlogboeken in voor controlelogboeken van besturingsvlak en verzend activiteitenlogboeken naar een Log Analytics-werkruimte voor rapportage en analyse, naar Azure Storage voor langetermijnwaarhoud, naar Azure Event Hubs voor export in andere analyseoplossingen in Azure en elders. Met behulp van azure-activiteitenlogboekgegevens kunt u bepalen wat, wie en wanneer voor schrijfbewerkingen (PUT, POST, DELETE) die worden uitgevoerd op het niveau van het besturingsvlak voor uw Azure API Management-service.

Voor auditlogboeken voor gegevensvlak bieden diagnostische logboeken uitgebreide informatie over bewerkingen en fouten die belangrijk zijn voor controle en het oplossen van problemen. Diagnoselogboeken verschillen van activiteitenlogboeken. Activiteitenlogboeken bieden inzicht in de bewerkingen die zijn uitgevoerd op uw Azure-resources. Diagnoselogboeken bieden inzicht in bewerkingen die door de resources zelf zijn uitgevoerd.

- [Diagnostische instellingen inschakelen voor Azure-activiteitenlogboek](../azure-monitor/essentials/activity-log.md)

- [Diagnostische instellingen inschakelen voor Azure API Management](./api-management-howto-use-azure-monitor.md#resource-logs)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="25-configure-security-log-storage-retention"></a>2.5: Opslagretentie van beveiligingslogboek configureren

**Richtlijnen:** Azure Monitor de retentieperiode van uw Log Analytics-werkruimte in op basis van de nalevingsvoorschriften van uw organisatie. Gebruik Azure Storage voor opslag op lange termijn/archivering.

- [Logboekretentieparameters instellen voor Log Analytics-werkruimten](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

- [Logboeken archiveren in een Azure Storage account](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="26-monitor-and-review-logs"></a>2.6: Logboeken bewaken en controleren

**Richtlijnen:** Azure API Management continu logboeken en metrische gegevens naar Azure Monitor, zodat u vrijwel realtime inzicht hebt in de status en status van uw API's. Met Azure Monitor en Log Analytics-werkruimten kunt u controleren, query's uitvoeren, visualiseren, routeren, archiveren, waarschuwingen configureren en acties uitvoeren op metrische gegevens en logboeken die afkomstig zijn van API Management en gerelateerde resources. Analyseer en controleer logboeken op afwijkende gedragingen en controleer regelmatig de resultaten.

U kunt de API Management integreren met Azure-toepassing Insights en deze gebruiken als primair of secundair hulpprogramma voor bewaking, tracering, rapportage en waarschuwingen.

- [Logboeken voor Azure-API Management](./api-management-howto-use-azure-monitor.md)

- [Aangepaste query's uitvoeren in Azure Monitor](../azure-monitor/logs/get-started-queries.md)

- [Inzicht in Log Analytics-werkruimte](../azure-monitor/logs/log-analytics-tutorial.md)

- [Integreren met Azure-toepassing Insights](api-management-howto-app-insights.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Waarschuwingen inschakelen voor afwijkende activiteiten

**Richtlijnen:** Schakel diagnostische instellingen voor Azure-activiteitenlogboeken in, evenals de diagnostische instellingen voor uw Azure API Management-exemplaren en verzend de logboeken naar een Log Analytics-werkruimte. Voer query's uit in Log Analytics om termen te zoeken, trends te identificeren, patronen te analyseren en veel andere inzichten te bieden op basis van de verzamelde gegevens. U kunt waarschuwingen maken op basis van uw Log Analytics-werkruimtequery's.

Maak metrische waarschuwingen om u te laten weten wanneer er iets onverwachts gebeurt. Ontvang bijvoorbeeld meldingen wanneer uw Azure API Management-exemplaar de verwachte piekcapaciteit gedurende een bepaalde periode heeft overschrijdt of als er gedurende een vooraf gedefinieerde periode een bepaald aantal niet-geautoriseerde gatewayaanvragen of -fouten is geweest.

U kunt de API Management integreren met Azure-toepassing Insights en deze gebruiken als primair of secundair hulpprogramma voor bewaking, tracering, rapportage en waarschuwingen.

Optioneel kunt u gegevens inschakelen en inschakelen voor Azure Sentinel of een SIEM van derden.

- [Diagnostische instellingen inschakelen voor Azure-activiteitenlogboek](../azure-monitor/essentials/activity-log.md)

- [Diagnostische instellingen inschakelen voor Azure API Management](./api-management-howto-use-azure-monitor.md#resource-logs)

- [Een waarschuwingsregel configureren voor Azure API Management](./api-management-howto-use-azure-monitor.md#set-up-an-alert-rule)

- [Metrische capaciteitsgegevens van een Azure API Management-exemplaar weergeven](api-management-capacity.md)

- [Integreren met Azure-toepassing Insights](api-management-howto-app-insights.md)

- [Onboarding van Azure Sentinel](../sentinel/quickstart-onboard.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="identity-and-access-control"></a>Identiteits- en toegangsbeheer

*Zie Azure Security [Benchmark: Identity and Access Control (Azure Security Benchmark: Identiteit en Access Control) voor meer Access Control.](../security/benchmarks/security-control-identity-access-control.md)*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Een inventaris van beheerdersaccounts onderhouden

**Richtlijnen:** Een inventarisatie onderhouden van accounts met beheerderstoegang tot de Azure API Management (Azure Portal).

Azure Active Directory (Azure AD) heeft ingebouwde rollen die expliciet moeten worden toegewezen en die kunnen worden opgevraagd. API Management is afhankelijk van deze rollen en Role-Based Access Control voor het inschakelen van fijnf mogelijk toegangsbeheer voor API Management services en entiteiten.

Daarnaast bevat API Management een ingebouwde groep Administrators in API Management gebruikerssysteem van de API Management van de gebruiker. Groepen in API Management zichtbaarheid van API's in de ontwikkelaarsportal en de leden van de groep Administrators kunnen alle API's zien.

Volg de aanbevelingen van Azure Security Center voor het beheer en onderhoud van beheerdersaccounts.

- [Op rollen gebaseerd toegangsbeheer gebruiken in API Management](api-management-role-based-access-control.md)

- [Een lijst met gebruikers op te halen onder een Azure API Management Instance](/powershell/module/az.apimanagement/get-azapimanagementuser)

- [Een lijst met gebruikers op te halen die zijn toegewezen aan een directoryrol in Azure AD met PowerShell](/powershell/module/az.resources/get-azroleassignment)

- [Een directoryroldefinitie in Azure AD krijgen met PowerShell](/powershell/module/az.resources/get-azroledefinition)

- [Meer informatie over aanbevelingen voor identiteit en toegang van Azure Security Center](../security-center/recommendations-reference.md#identityandaccess-recommendations)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Standaardwachtwoorden wijzigen, indien van toepassing

**Richtlijnen:** Azure API Management niet het concept van standaardwachtwoorden/-sleutels.

Azure API Management-abonnementen, een manier om de toegang tot API's te beveiligen, worden echter wel met twee gegenereerde abonnementssleutels gebruikt. Klanten kunnen deze abonnementssleutels op elk moment opnieuw gebruiken.

- [Inzicht in Azure API Management-abonnementen](api-management-subscriptions.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Toegewezen beheerdersaccounts gebruiken

**Richtlijnen:** Standaardprocedures voor het gebruik van toegewezen beheerdersaccounts maken. Gebruik Azure Security Center identiteits- en toegangsbeheer om het aantal beheerdersaccounts te bewaken.

Daarnaast kunt u aanbevelingen van Azure Security Center of ingebouwde Azure-beleidsregels gebruiken om toegewezen beheerdersaccounts bij te houden, zoals:
- Er moet meer dan één eigenaar zijn toegewezen aan uw abonnement
- Afgeschafte accounts met eigenaarsmachtigingen moeten worden verwijderd uit uw abonnement
- Externe accounts met eigenaarsmachtigingen moeten worden verwijderd uit uw abonnement

- [Identiteit en toegang Azure Security Center gebruiken (preview)](../security-center/security-center-identity-access.md)

- [Hoe u Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4: Eenmalige Azure Active Directory gebruiken

**Richtlijnen:** Azure API Management kan worden geconfigureerd om gebruik te maken van Azure Active Directory (Azure AD) als id-provider voor het verifiëren van gebruikers in de ontwikkelaarsportal om te profiteren van de mogelijkheden voor eenmalige aanmelding van Azure AD. Zodra de configuratie is geconfigureerd, kunnen nieuwe gebruikers van de ontwikkelaarsportal ervoor kiezen om het aanmeldingsproces te volgen door eerst te verifiëren via Azure AD en vervolgens het aanmeldingsproces in de portal te voltooien zodra de verificatie is uitgevoerd.

- [Ontwikkelaarsaccounts machtigen met behulp van Azure AD in Azure API Management](api-management-howto-aad.md)

U kunt het aanmeldings-/aanmeldingsproces ook verder aanpassen via delegatie. Met delegatie kunt u uw bestaande website gebruiken voor het afhandelen van aanmelding bij/aanmelding bij ontwikkelaars en abonnementen op producten, in tegenstelling tot het gebruik van de ingebouwde functionaliteit in de ontwikkelaarsportal. Hiermee kan uw website eigenaar zijn van de gebruikersgegevens en de validatie van deze stappen op een aangepaste manier uitvoeren.

- [Gebruikersregistratie en productabonnement delegeren](api-management-howto-setup-delegation.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Meervoudige verificatie gebruiken voor alle Azure Active Directory op basis van toegang

**Richtlijnen:** meervoudige Azure Active Directory (Azure AD) inschakelen en Azure Security Center aanbevelingen voor identiteits- en toegangsbeheer volgen.

- [Meervoudige verificatie inschakelen in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Identiteit en toegang bewaken binnen Azure Security Center](../security-center/security-center-identity-access.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Toegewezen machines (Privileged Access Workstations) gebruiken voor alle beheertaken

**Richtlijnen:** Werkstations met bevoegde toegang (PAW) gebruiken met meervoudige verificatie die is geconfigureerd voor aanmelden bij en configureren van Azure-resources.

- [Meer informatie over Privileged Access Workstations](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)
  
- [Meervoudige verificatie inschakelen in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Logboeken en waarschuwingen voor verdachte activiteiten van beheerdersaccounts

**Richtlijnen:** gebruik Azure Active Directory (Azure AD) Privileged Identity Management (PIM) voor het genereren van logboeken en waarschuwingen wanneer er verdachte of onveilige activiteiten plaatsvinden in de omgeving.

Gebruik bovendien Azure AD-risicodetecties om waarschuwingen en rapporten over riskant gebruikersgedrag weer te geven.

- [Een Privileged Identity Management (PIM) implementeren](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Inzicht in Azure AD-risicodetecties](../active-directory/identity-protection/overview-identity-protection.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Azure-resources alleen beheren vanaf goedgekeurde locaties

**Richtlijnen:** gebruik benoemde locaties voor voorwaardelijke toegang om toegang tot de Azure Portal alleen toe te staan vanuit specifieke logische groeperingen van IP-adresbereiken of landen/regio's.

- [Benoemde locaties configureren in Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="39-use-azure-active-directory"></a>3.9: Gebruik Azure Active Directory

**Richtlijnen:** gebruik indien mogelijk Azure Active Directory (Azure AD) als het centrale verificatie- en autorisatiesysteem. Azure AD beschermt gegevens met behulp van sterke versleuteling voor data-at-rest en in transit. Azure AD biedt ook salts, hashes en slaat veilig gebruikersreferenties op.

Configureer uw Azure API Management Developer Portal om ontwikkelaarsaccounts te verifiëren met behulp van Azure AD.

Configureer uw Azure API Management om uw API's te beveiligen met behulp van het OAuth 2.0-protocol met Azure AD.

- [Ontwikkelaarsaccounts machtigen met behulp van Azure AD in Azure API Management](api-management-howto-aad.md)

- [Een API beveiligen met OAuth 2.0 met Azure AD en API Management](api-management-howto-protect-backend-with-aad.md)

- [Een Azure AD-instantie maken en configureren](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Regelmatig gebruikerstoegang controleren en afstemmen

**Richtlijnen:** Azure Active Directory (Azure AD) biedt logboeken om verouderde accounts te helpen ontdekken. Klanten kunnen Azure Identity Access Reviews gebruiken om groepslidmaatschap, toegang tot bedrijfstoepassingen en roltoewijzingen efficiënt te beheren. Gebruikerstoegang kan regelmatig worden gecontroleerd om ervoor te zorgen dat alleen de juiste gebruikers de juiste toegang blijven hebben.

Klanten kunnen de inventaris van API Management gebruikersaccounts onderhouden en de toegang zo nodig afstemmen. In API Management zijn ontwikkelaars de gebruikers van de API's die met de API Management. Nieuw gemaakte ontwikkelaarsaccounts zijn standaard Actief en gekoppeld aan de groep Ontwikkelaars. Ontwikkelaarsaccounts met een actieve status kunnen worden gebruikt voor toegang tot alle API's waarvoor ze abonnementen hebben.

Beheerders kunnen aangepaste groepen maken of gebruikmaken van externe groepen in gekoppelde Azure AD-tenants. Aangepaste en externe groepen kunnen naast systeemgroepen worden gebruikt om ontwikkelaars zichtbaarheid van en toegang tot API-producten te geven.

- [Gebruikersaccounts in Azure API Management beheren](api-management-howto-create-or-invite-developers.md)

- [Een lijst met API Management krijgen](/powershell/module/az.apimanagement/get-azapimanagementuser)

- [Groepen maken en gebruiken voor beheer van ontwikkelaarsaccounts in Azure API Management](api-management-howto-create-groups.md)

- [Toegangsbeoordelingen voor Azure-identiteiten gebruiken](../active-directory/governance/access-reviews-overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Pogingen tot toegang tot gedeactiveerde referenties controleren

**Richtlijnen:** Configureer uw Azure API Management-exemplaar om ontwikkelaarsaccounts te verifiëren met behulp van Azure Active Directory (Azure AD) als id-provider in Azure API Management.

Configureer uw Azure API Management om uw API's te beveiligen met behulp van het OAuth 2.0-protocol met Azure AD.

Configureer het JWT-validatiebeleid voor binnenkomende API-aanvragen om het bestaan en de geldigheid van een geldig token af te dwingen.

Maak diagnostische instellingen voor Azure AD-gebruikersaccounts en verzend de auditlogboeken en aanmeldingslogboeken naar een Log Analytics-werkruimte. Configureer de gewenste waarschuwingen in Log Analytics. Daarnaast kunt u de Log Analytics-werkruimte onboarden voor Azure Sentinel of een SIEM van derden.

Configureer geavanceerde bewaking met API Management met behulp van het beleid, leg eventuele aanvullende contextinformatie vast die vereist is voor beveiligingsanalyse en verzend deze naar Azure Sentinel siem van `log-to-eventhub` derden.

- [Ontwikkelaarsaccounts machtigen met behulp van Azure AD in Azure API Management](api-management-howto-aad.md)

- [Een API beveiligen met behulp van OAuth 2.0 met Azure AD en API Management](api-management-howto-protect-backend-with-aad.md)

- [API Management access restriction policies](api-management-access-restriction-policies.md) (Beleid voor toegangsbeperking API Management)

- [Azure AD-logboeken integreren in Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Het in-boarden Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Geavanceerde bewaking van API's](api-management-log-to-eventhub-sample.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: Afwijking van aanmeldingsgedrag van account

**Richtlijnen:** gebruik Azure Active Directory (Azure AD) Identity Protection en risicodetectiefuncties voor accountaanmelding op het besturingsvlak (de Azure Portal) om geautomatiseerde reacties op gedetecteerde verdachte acties met betrekking tot gebruikersidentiteiten te configureren. U kunt ook gegevens opnemen in Azure Sentinel voor verder onderzoek.

- [Riskante Azure AD-aanmeldingen weergeven](../active-directory/identity-protection/overview-identity-protection.md)

- [Risicobeleid voor Identiteitsbeveiliging configureren en inschakelen](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Onboarding van Azure Sentinel](../sentinel/quickstart-onboard.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Microsoft toegang geven tot relevante klantgegevens tijdens ondersteuningsscenario's

**Richtlijnen:** momenteel niet beschikbaar; Klanten-lockbox wordt momenteel niet ondersteund voor Azure API Management.

- [Lijst met Klanten-lockbox ondersteunde services](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="data-protection"></a>Gegevensbeveiliging

*Zie [Azure Security Benchmark: gegevensbescherming](../security/benchmarks/security-control-data-protection.md) voor meer informatie.*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Een inventaris van gevoelige informatie onderhouden

**Richtlijnen:** Tags gebruiken om Azure-resources bij te houden die gevoelige informatie opslaan of verwerken.

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Systemen isoleren die gevoelige informatie opslaan of verwerken

**Richtlijnen:** Implementeert afzonderlijke abonnementen en/of beheergroepen voor ontwikkeling, test en productie. Azure API Management-exemplaren moeten worden gescheiden door een virtueel netwerk (VNet)/subnet en op de juiste wijze worden getagd.

- [Aanvullende Azure-abonnementen maken](../cost-management-billing/manage/create-subscription.md)

- [Een Beheergroepen](../governance/management-groups/create-management-group-portal.md)

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

- [Azure API Management gebruiken met virtuele netwerken](api-management-using-with-vnet.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Onbevoegde overdracht van gevoelige informatie bewaken en blokkeren

**Richtlijnen:** momenteel niet beschikbaar; functies voor gegevensidentificatie, -classificatie en -preventie zijn momenteel niet beschikbaar voor Azure API Management.

Microsoft beheert de onderliggende infrastructuur voor Azure API Management en heeft strenge controles geïmplementeerd om verlies of blootstelling van klantgegevens te voorkomen.

- [Informatie over beveiliging van klantgegevens in Azure](../security/fundamentals/protection-customer-data.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Alle gevoelige gegevens tijdens de overdracht versleutelen

**Richtlijnen:** aanroepen van de beheervlak worden Azure Resource Manager via TLS. Er is een geldig JSON-web token (JWT) vereist. Aanroepen van de gegevensvlak kunnen worden beveiligd met TLS en een van de ondersteunde verificatiemechanismen (bijvoorbeeld clientcertificaat of JWT).

- [Inzicht in gegevensbeveiliging in Azure API Management](#data-protection)

- [TLS-instellingen beheren in Azure API Management](api-management-howto-manage-protocols-ciphers.md)

- [API's in Azure API Management beveiligen met Azure Active Directory (Azure AD)](api-management-howto-protect-backend-with-aad.md)

- [API's in Azure API Management beveiligen met Azure AD B2C](howto-protect-backend-frontend-azure-ad-b2c.md)

**Verantwoordelijkheid**: Gedeeld

**Azure Security Center bewaking:** geen

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Een actief detectieprogramma gebruiken om gevoelige gegevens te identificeren

**Richtlijnen:** Nog niet beschikbaar; functies voor gegevensidentificatie, -classificatie en -preventie zijn nog niet beschikbaar voor Azure API Management. Tag Azure API Management services die gevoelige informatie als zodanig kunnen verwerken en implementeert indien nodig een oplossing van derden voor nalevingsdoeleinden.

Voor het onderliggende platform dat wordt beheerd door Microsoft, behandelt Microsoft alle klantinhoud als gevoelig en gaat het in grote lengte om zich te beschermen tegen gegevensverlies en -blootstelling van klanten. Om ervoor te zorgen dat klantgegevens in Azure veilig blijven, heeft Microsoft een suite met robuuste besturingselementen en mogelijkheden voor gegevensbeveiliging geïmplementeerd en onderhouden.

- [Informatie over beveiliging van klantgegevens in Azure](../security/fundamentals/protection-customer-data.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Azure RBAC gebruiken om de toegang tot resources te beheren 

**Richtlijnen:** Op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC) gebruiken voor het beheren van de toegang tot Azure API Management. Azure API Management is afhankelijk van op rollen gebaseerd toegangsbeheer van Azure voor het inschakelen van fijnf mogelijk toegangsbeheer voor API Management-services en entiteiten (bijvoorbeeld API's en beleidsregels).

- [Op rollen gebaseerd toegangsbeheer van Azure gebruiken in Azure API Management](api-management-role-based-access-control.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Preventie van gegevensverlies op basis van een host gebruiken om toegangsbeheer af te dwingen

**Richtlijnen:** Niet van toepassing; deze aanbeveling is bedoeld voor rekenbronnen.

Microsoft beheert de onderliggende infrastructuur voor Azure API Management en heeft strenge controles geïmplementeerd om verlies of blootstelling van klantgegevens te voorkomen.

- [Informatie over beveiliging van klantgegevens in Azure](../security/fundamentals/protection-customer-data.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Logboeken en waarschuwingen voor wijzigingen in kritieke Azure-resources

**Richtlijnen:** gebruik Azure Monitor met het Azure-activiteitenlogboek om waarschuwingen te maken voor wanneer er wijzigingen worden aangebracht in productie-Azure Functions-apps en andere kritieke of gerelateerde resources.

- [Waarschuwingen maken voor gebeurtenissen in het Azure-activiteitenlogboek](../azure-monitor/alerts/alerts-activity-log.md)

- [Het gebruik van Azure Monitor azure-activiteitenlogboek in Azure API Management](api-management-howto-use-azure-monitor.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="vulnerability-management"></a>Beheer van beveiligingsproblemen

*Zie de Azure [Security Benchmark: Vulnerability Management](../security/benchmarks/security-control-vulnerability-management.md)voor meer informatie.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Geautomatiseerde hulpprogramma's voor het scannen op beveiligingsleed uitvoeren

**Richtlijnen:** momenteel niet beschikbaar; evaluatie van beveiligingsle Azure Security Center is momenteel niet beschikbaar voor Azure API Management.

Onderliggend platform gescand en gepatcht door Microsoft. Bekijk de beschikbare beveiligingscontroles om beveiligingsproblemen met betrekking tot serviceconfiguratie te verminderen.

- [Inzicht in beveiligingsbesturingselementen die beschikbaar zijn voor Azure API Management]()

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Een geautomatiseerde beheeroplossing voor besturingssysteempatchs implementeren

**Richtlijnen:** Niet van toepassing; deze aanbeveling is bedoeld voor rekenbronnen.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5.3: Een automatische oplossing voor patchbeheer implementeren voor softwaretitels van derden

**Richtlijnen:** Niet van toepassing; deze aanbeveling is bedoeld voor rekenbronnen.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Scans van back-to-back-beveiligingsprobleem vergelijken

**Richtlijnen:** Niet van toepassing; deze aanbeveling is bedoeld voor rekenbronnen.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Gebruik een proces voor risicoclassificatie om prioriteit te geven aan het herstellen van gevonden beveiligingsproblemen

**Richtlijnen:** momenteel niet beschikbaar; evaluatie van beveiligingsle Azure Security Center is momenteel niet beschikbaar voor Azure API Management.

Onderliggend platform gescand en gepatcht door Microsoft. De klant moet de beschikbare beveiligingscontroles bekijken om beveiligingsproblemen met betrekking tot de serviceconfiguratie te verminderen.


**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="inventory-and-asset-management"></a>Inventarisatie en Asset Management

*Zie de Azure [Security Benchmark: Inventory and Asset Management (Azure Security-benchmark: inventaris en assetbeheer) voor meer informatie.](../security/benchmarks/security-control-inventory-asset-management.md)*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Geautomatiseerde oplossing voor assetdetectie gebruiken

**Richtlijnen:** gebruik Azure Resource Graph voor het opvragen/ontdekken van alle resources (zoals compute, opslag, netwerk, poorten en protocollen, enzovoort) binnen uw abonnement(en). Zorg voor de juiste machtigingen (lezen) in uw tenant en semuler alle Azure-abonnementen en resources binnen uw abonnementen.

Hoewel klassieke Azure-resources kunnen worden ontdekt via Resource Graph, wordt het ten zeerste aanbevolen om in de toekomst Azure Resource Manager te maken en te gebruiken.

- [Query's maken met Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

- [Uw Azure-abonnementen weergeven](/powershell/module/az.accounts/get-azsubscription?preserve-view=true&view=azps-4.8.0)

- [Inzicht krijgen in Azure RBAC](../role-based-access-control/overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="62-maintain-asset-metadata"></a>6.2: Metagegevens van activa onderhouden

**Richtlijnen:** Tags toepassen op Azure-resources en metagegevens geven om ze logisch in te delen in een taxonomie.

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Niet-geautoriseerde Azure-resources verwijderen

**Richtlijnen:** gebruik waar nodig tags, beheergroepen en afzonderlijke abonnementen om Azure-resources te organiseren en bij te houden. Inventaris regelmatig afstemmen en ervoor zorgen dat niet-geautoriseerde resources tijdig uit het abonnement worden verwijderd.

Gebruik daarnaast Azure Policy om beperkingen in te stellen voor het type resources dat kan worden gemaakt in een of meer klantabonnementen met behulp van de volgende ingebouwde beleidsdefinities:
- Niet toegestane resourcetypen
- Toegestane brontypen

- [Aanvullende Azure-abonnementen maken](../cost-management-billing/manage/create-subscription.md)

- [Een Beheergroepen](../governance/management-groups/create-management-group-portal.md)

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4: Inventaris van goedgekeurde Azure-resources definiëren en onderhouden

**Richtlijnen:** Niet van toepassing; deze aanbeveling is bedoeld voor rekenbronnen.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Controleren op niet-goedgekeurde Azure-resources

**Richtlijnen:** gebruik Azure Policy beperkingen in te stellen voor het type resources dat in uw abonnement(en) kan worden gemaakt met behulp van de volgende ingebouwde beleidsdefinities:
- Niet toegestane resourcetypen
- Toegestane brontypen

Gebruik Azure Resource Graph om query's uit te voeren op resources binnen hun abonnement(en). Zorg ervoor dat alle Azure-resources in de omgeving zijn goedgekeurd.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Query's maken met Azure Graph](../governance/resource-graph/first-query-portal.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Controleren op niet-goedgekeurde softwaretoepassingen binnen rekenbronnen

**Richtlijnen:** Niet van toepassing; deze aanbeveling is bedoeld voor rekenbronnen.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Niet-goedgekeurde Azure-resources en -softwaretoepassingen verwijderen

**Richtlijnen:** Niet van toepassing; deze aanbeveling is bedoeld voor rekenbronnen.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="68-use-only-approved-applications"></a>6.8: Alleen goedgekeurde toepassingen gebruiken

**Richtlijnen:** Niet van toepassing; deze aanbeveling is bedoeld voor rekenbronnen.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="69-use-only-approved-azure-services"></a>6.9: Alleen goedgekeurde Azure-services gebruiken

**Richtlijnen:** gebruik Azure Policy beperkingen in te stellen voor het type resources dat kan worden gemaakt in klantabonnementen met behulp van de volgende ingebouwde beleidsdefinities:
- Niet toegestane resourcetypen
- Toegestane brontypen

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Een specifiek resourcetype weigeren met Azure Policy](../governance/policy/samples/built-in-policies.md#general)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10: Een inventaris van goedgekeurde softwaretitels onderhouden

**Richtlijnen:** Niet van toepassing; deze aanbeveling is bedoeld voor rekenbronnen.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: De mogelijkheid van gebruikers om te communiceren met Azure Resource Manager

**Richtlijnen:** Configureer voorwaardelijke toegang van Azure om de mogelijkheid van gebruikers om te communiceren met Azure Resource Manager te beperken door Toegang blokkeren te configureren voor de app Microsoft Azure Management.

- [Voorwaardelijke toegang configureren om de toegang tot Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

- [Op rollen gebaseerd toegangsbeheer in Azure API Management](api-management-role-based-access-control.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: De mogelijkheid van gebruikers om scripts uit te voeren binnen rekenbronnen beperken

**Richtlijnen:** Niet van toepassing; deze aanbeveling is bedoeld voor rekenbronnen.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Toepassingen met een hoog risico fysiek of logisch scheiden

**Richtlijnen:** Niet van toepassing; Deze aanbeveling is bedoeld voor webtoepassingen die worden uitgevoerd op Azure App Service of rekenbronnen.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="secure-configuration"></a>Veilige configuratie

*Zie de Azure [Security Benchmark: Secure Configuration voor meer informatie.](../security/benchmarks/security-control-secure-configuration.md)*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Veilige configuraties voor alle Azure-resources tot stand brengen

**Richtlijnen:** standaardbeveiligingsconfiguraties definiëren en implementeren voor uw Azure API Management-service met Azure Policy. Gebruik Azure Policy aliassen in de naamruimte Microsoft.ApiManagement om aangepaste beleidsregels te maken om de configuratie van uw Azure API Management-services te controleren of af te dwingen.

- [Beschikbare aliassen Azure Policy weergeven](/powershell/module/az.resources/get-azpolicyalias?preserve-view=true&view=azps-4.8.0)

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Configuraties van beveiligde besturingssystemen tot stand

**Richtlijnen:** Niet van toepassing; deze aanbeveling is bedoeld voor rekenbronnen.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Veilige Azure-resourceconfiguraties onderhouden

**Richtlijnen:** standaardbeveiligingsconfiguraties definiëren en implementeren voor uw Azure API Management-services met Azure Policy. Gebruik Azure Policy aliassen in de naamruimte Microsoft.ApiManagement om aangepaste beleidsregels te maken om de configuratie van Azure API Management-exemplaren te controleren of af te dwingen. Gebruik Azure Policy [deny] en [deploy if not exist] om beveiligde instellingen af te dwingen voor uw Azure-resources.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Inzicht Azure Policy effecten](../governance/policy/concepts/effects.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Veilige besturingssysteemconfiguraties onderhouden

**Richtlijnen:** Niet van toepassing; deze aanbeveling is bedoeld voor rekenbronnen.

**Verantwoordelijkheid**: Gedeeld

**Azure Security Center bewaking:** geen

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Configuratie van Azure-resources veilig opslaan

**Richtlijnen:** als u aangepaste Azure-beleidsdefinities gebruikt, gebruikt u Azure DevOps of Azure Repos om de configuratie van uw Azure API Management-service veilig op te slaan en te beheren.

- [Bestanden opslaan in Azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Documentatie voor Azure-repos](/azure/devops/repos/)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Veilige opslag van aangepaste besturingssysteeminstallatie afbeeldingen

**Richtlijnen:** Niet van toepassing; deze aanbeveling is bedoeld voor rekenbronnen.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Hulpprogramma's voor configuratiebeheer implementeren voor Azure-resources

**Richtlijnen:** standaardbeveiligingsconfiguraties definiëren en implementeren voor uw Azure API Management-services met Azure Policy. Gebruik Azure Policy aliassen in de naamruimte Microsoft.ApiManagement om aangepaste beleidsregels te maken om de configuratie van Azure API Management-exemplaren te controleren of af te dwingen. Gebruik Azure Policy [deny] en [deploy if not exist] om veilige instellingen af te dwingen voor uw Azure-resources.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Inzicht Azure Policy effecten](../governance/policy/concepts/effects.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8: Hulpprogramma's voor configuratiebeheer implementeren voor besturingssystemen

**Richtlijnen:** Niet van toepassing; deze aanbeveling is bedoeld voor rekenbronnen.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Geautomatiseerde configuratiebewaking implementeren voor Azure-resources

**Richtlijnen:** gebruik de Azure API Management DevOps Resource Kit om configuratiebeheer uit te voeren voor Azure API Management.

Daarnaast definieert en implementeert u standaardbeveiligingsconfiguraties voor uw Azure API Management-services met Azure Policy. Gebruik Azure Policy aliassen in de naamruimte Microsoft.ApiManagement om aangepaste beleidsregels te maken om de configuratie van Azure API Management te controleren of af te dwingen. Gebruik Azure Policy [deny] en [deploy if not exist] om veilige instellingen af te dwingen voor uw Azure-resources.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Inzicht in Azure Policy effecten](../governance/policy/concepts/effects.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Geautomatiseerde configuratiebewaking implementeren voor besturingssystemen

**Richtlijnen:** Niet van toepassing; deze aanbeveling is bedoeld voor rekenbronnen.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Identiteiten veilig en automatisch beheren

**Richtlijnen:** Gebruik Managed Service Identity die is gegenereerd door Azure Active Directory (Azure AD) om uw API Management-exemplaar eenvoudig en veilig toegang te geven tot andere met Azure AD beveiligde resources, zoals Azure Key Vault.

- [Een beheerde identiteit maken voor een API Management exemplaar](api-management-howto-use-managed-service-identity.md)

- [Beleid voor verificatie met beheerde identiteit](./api-management-authentication-policies.md#ManagedIdentity)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Onbedoelde blootstelling van referenties voorkomen

**Richtlijnen:** Referentiescanner implementeren om referenties in code te identificeren. Door het gebruik van Credential Scanner worden gebruikers ook aangemoedigd om gedetecteerde referenties naar veiligere locaties, zoals Azure Key Vault, te verplaatsen.

- [Referentiescanner instellen](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="malware-defense"></a>Beveiliging tegen malware

*Zie de Azure [Security Benchmark: Malware Defense voor meer informatie.](../security/benchmarks/security-control-malware-defense.md)*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: Centraal beheerde antimalwaresoftware gebruiken

**Richtlijnen:** Niet van toepassing; deze aanbeveling is bedoeld voor rekenbronnen.

Antimalware van Microsoft is ingeschakeld op de onderliggende host die ondersteuning biedt voor Azure-services (bijvoorbeeld Azure API Management), maar wordt niet uitgevoerd op klantinhoud.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Bestanden vooraf scannen die moeten worden geüpload naar niet-compute-Azure-resources

**Richtlijnen:** Niet van toepassing; Deze aanbeveling is bedoeld voor niet-rekenbronnen die zijn ontworpen om gegevens op te slaan.

Antimalware van Microsoft is ingeschakeld op de onderliggende host die Ondersteuning biedt voor Azure-services (bijvoorbeeld Azure API Management), maar wordt niet uitgevoerd op klantinhoud.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: Ervoor zorgen dat antimalwaresoftware en handtekeningen worden bijgewerkt

**Richtlijnen:** Niet van toepassing; Deze aanbeveling is bedoeld voor niet-rekenbronnen die zijn ontworpen om gegevens op te slaan.

Antimalware van Microsoft is ingeschakeld op de onderliggende host die Ondersteuning biedt voor Azure-services (bijvoorbeeld Azure API Management), maar wordt niet uitgevoerd op klantinhoud.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="data-recovery"></a>Gegevensherstel

*Zie de Azure [Security Benchmark: Data Recovery voor meer informatie.](../security/benchmarks/security-control-data-recovery.md)*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Regelmatige automatische back-ups garanderen

**Richtlijnen:** Door uw API's te publiceren en te beheren via Azure API Management, profiteert u van fouttolerantie en infrastructuurmogelijkheden die u anders handmatig zou ontwerpen, implementeren en beheren. API Management ondersteuning voor implementatie in meerdere regio's, waardoor het gegevensvlak ondoordringbaar wordt voor regionale storingen zonder dat er operationele overhead wordt toegevoegd.

De back-up- en herstelfuncties van API Management de benodigde bouwstenen voor het implementeren van een strategie voor herstel na noodherstel. Back-up- en herstelbewerkingen kunnen handmatig of geautomatiseerd worden uitgevoerd.

- [Een gegevensvlak API Management meerdere regio's implementeren](api-management-howto-deploy-multi-region.md)

- [Noodherstel implementeren met back-up en herstellen van services in Azure API Management](./api-management-howto-disaster-recovery-backup-restore.md#calling-the-backup-and-restore-operations)

- [De back-upbewerking API Management aanroepen](/rest/api/apimanagement/2019-12-01/apimanagementservice/backup)

- [De herstelbewerking API Management aanroepen](/rest/api/apimanagement/2019-12-01/apimanagementservice/restore)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Volledige systeemback-ups uitvoeren en back-ups maken van door de klant beheerde sleutels

**Richtlijnen:** Back-up- en herstelbewerkingen van Azure API Management volledige systeemback-up en -herstel uitvoeren.

Beheerde identiteiten kunnen worden gebruikt voor het verkrijgen van certificaten van Azure Key Vault voor API Management aangepaste domeinnamen. Back-up van alle certificaten die worden opgeslagen in Azure Key Vault.

- [Noodherstel implementeren met back-up en herstellen van services in Azure API Management](./api-management-howto-disaster-recovery-backup-restore.md#calling-the-backup-and-restore-operations)

- [Back-ups maken van Azure Key Vault certificaten](/powershell/module/az.keyvault/backup-azkeyvaultcertificate?preserve-view=true&view=azps-4.8.0)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Alle back-ups valideren, inclusief door de klant beheerde sleutels

**Richtlijnen:** Valideer back-ups door een testherstel van de service en certificaten van back-ups uit te voeren.

- [De herstelbewerking API Management aanroepen](/rest/api/apimanagement/2019-12-01/apimanagementservice/restore)

- [Uw certificaten Azure Key Vault herstellen](/powershell/module/az.keyvault/restore-azkeyvaultcertificate)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Beveiliging van back-ups en door de klant beheerde sleutels garanderen

**Richtlijnen:** Azure API Management back-ups naar accounts van klanten Azure Storage schrijven. Volg Azure Storage om uw back-up te beveiligen.

- [Noodherstel implementeren met back-up en herstellen van services in Azure API Management](./api-management-howto-disaster-recovery-backup-restore.md#calling-the-backup-and-restore-operations)

- [Beveiligingsaanbeveling voor blob-opslag](../storage/blobs/security-recommendations.md)

Schakel Soft-Delete in Key Vault om sleutels te beveiligen tegen onbedoelde of schadelijke verwijdering.

- [Een Soft-Delete inschakelen in Key Vault](../storage/blobs/soft-delete-blob-overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="incident-response"></a>Reageren op incidenten

*Zie [Azure Security Benchmark: respons op incidenten](../security/benchmarks/security-control-incident-response.md) voor meer informatie.*

### <a name="101-create-an-incident-response-guide"></a>10.1: Een handleiding voor het reageren op incidenten maken

**Richtlijnen**: Stel voor uw organisatie een responshandleiding op voor gebruik bij incidenten. Zorg ervoor dat er schriftelijke responsplannen zijn waarin alle rollen van het personeel worden gedefinieerd, evenals alle fasen in het afhandelen/managen van incidenten, vanaf de detectie van het incident tot een evaluatie ervan achteraf.

- [Richtlijnen voor het bouwen van uw eigen reactieproces voor beveiligingsincident](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft Security Response Center van een incident](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [Gebruik de Computer Security Incident Handling Guide van NIST om u te helpen bij het maken van uw eigen plan voor het reageren op incidenten](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Een procedure voor het scoren en prioriteren van incidenten maken

**Richtlijnen:** Security Center aan elke waarschuwing een ernst toe om u te helpen bepalen welke waarschuwingen het eerst moeten worden onderzocht. De ernst is gebaseerd op hoe zeker Security Center is bij het vinden of de analyse die wordt gebruikt om de waarschuwing uit te geven, evenals het betrouwbaarheidsniveau dat er een schadelijke intentie is achter de activiteit die heeft geleid tot de waarschuwing.

Daarnaast moet u abonnementen duidelijk markeren (bijvoorbeeld productie, niet-prod) met behulp van tags en een naamgevingssysteem maken om Azure-resources duidelijk te identificeren en te categoriseren, met name azure-resources die gevoelige gegevens verwerken. Het is uw verantwoordelijkheid om prioriteit te geven aan het oplossen van waarschuwingen op basis van de ernst van de Azure-resources en -omgeving waarin het incident heeft plaatsgevonden.

- [Beveiligingswaarschuwingen in Azure Security Center](../security-center/security-center-alerts-overview.md)

- [Tags gebruiken om Azure-resources te organiseren](../azure-resource-manager/management/tag-resources.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="103-test-security-response-procedures"></a>10.3: Procedures voor het reageren op beveiliging testen

**Richtlijnen:** oefeningen uitvoeren om de mogelijkheden voor het reageren op incidenten van uw systemen regelmatig te testen om uw Azure-resources te beschermen. Stel vast waar zich zwakke plekken en hiaten bevinden, en wijzig zo nodig het plan.

- [Publicatie van NIST - Handleiding voor test-, trainings- en oefeningsprogramma's voor IT-plannen en -mogelijkheden](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Contactgegevens voor beveiligingsincidenten verstrekken en waarschuwingsmeldingen voor beveiligingsincidenten configureren

**Richtlijnen:** Contactgegevens voor beveiligingsincidenten worden door Microsoft gebruikt om contact met u op te nemen als de Microsoft Security Response Center (MSRC) detecteert dat uw gegevens zijn gebruikt door een onrechtmatige of niet-geautoriseerde partij. Bekijk incidenten na het feit om ervoor te zorgen dat problemen worden opgelost.

- [De beveiligingscontactcontacte Azure Security Center instellen](../security-center/security-center-provide-security-contact-details.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Beveiligingswaarschuwingen opnemen in uw systeem voor het reageren op incidenten

**Richtlijnen:** exporteert uw Azure Security Center en aanbevelingen met behulp van de functie Continue export om risico's voor Azure-resources te identificeren. Met Continue export kunt u waarschuwingen en aanbevelingen handmatig of op een continue, continue manier exporteren. U kunt de connector Azure Security Center gegevens gebruiken om de waarschuwingen te streamen naar Azure Sentinel.

- [Continue export configureren](../security-center/continuous-export.md)

- [Waarschuwingen streamen naar Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Het antwoord op beveiligingswaarschuwingen automatiseren

**Richtlijnen:** Gebruik de functie Werkstroomautomatisering in Azure Security Center automatisch reacties te activeren via 'Logic Apps' voor beveiligingswaarschuwingen en aanbevelingen.

- [Werkstroomautomatisering en -Logic Apps](../security-center/workflow-automation.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="penetration-tests-and-red-team-exercises"></a>Penetratietests en Red Team-oefeningen

*Zie de Azure [Security Benchmark: Penetratietests en Red Team-oefeningen voor meer informatie.](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Regelmatige penetratietests uitvoeren van uw Azure-resources en zorgen voor herstel van alle kritieke beveiligingsonderzoeken

**Richtlijnen:** Volg de Microsoft-regels voor betrokkenheid om ervoor te zorgen dat uw penetratietests niet in strijd zijn met het Microsoft-beleid: https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1 .

- [U vindt hier meer informatie over de strategie en uitvoering van Microsoft voor Red Teaming en penetratietests van live-site tegen door Microsoft beheerde cloudinfrastructuur, -services en -toepassingen](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Verantwoordelijkheid**: Gedeeld

**Azure Security Center bewaking:** geen

## <a name="next-steps"></a>Volgende stappen

- Zie [Overzicht Azure Security Benchmark V2](../security/benchmarks/overview.md)
- Meer informatie over [Azure-beveiligingsbasislijnen](../security/benchmarks/security-baselines-overview.md)
