---
title: Azure-beveiligings basislijn voor API Management
description: De API Management Security Baseline voorziet in procedure richtlijnen en resources voor het implementeren van de beveiligings aanbevelingen die zijn opgegeven in de Azure Security-Bench Mark.
author: msmbaldwin
ms.service: api-management
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 6f54bd88e58ccfef068900fc3c7b249cde1c233d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105558547"
---
# <a name="azure-security-baseline-for-api-management"></a>Azure-beveiligings basislijn voor API Management

In deze beveiligings basislijn worden richt lijnen van de [Azure Security Bench Mark-versie 1,0](../security/benchmarks/overview-v1.md) ingesteld op API management. De Azure Security-benchmark biedt aanbevelingen voor hoe u uw cloudoplossingen in Azure kunt beveiligen.
De inhoud wordt gegroepeerd op de **beveiligings controles** die zijn gedefinieerd door de Azure Security-benchmark en de bijbehorende richt lijnen die van toepassing zijn op API management. **Besturings elementen** die niet van toepassing zijn op API Management, zijn uitgesloten.

 
Als u wilt zien hoe API Management volledig is toegewezen aan de beveiligings benchmark van Azure, raadpleegt u het [volledige API Management beveiligings basislijn toewijzings bestand](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Netwerkbeveiliging

*Zie [Azure Security Benchmark: netwerkbeveiliging](../security/benchmarks/security-control-network-security.md) voor meer informatie.*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Azure-resources in virtuele netwerken beveiligen

**Hulp**: Azure API Management kan worden geïmplementeerd in een Azure Virtual Network (Vnet), zodat het toegang heeft tot back-end-services in het netwerk. De ontwikkelaars Portal en de API Management Gateway kunnen zo worden geconfigureerd dat ze toegankelijk zijn vanaf internet (extern) of alleen binnen het Vnet (intern).

- Extern: de API Management Gateway en de ontwikkelaars Portal zijn toegankelijk via het open bare Internet via een extern load balancer. De gateway kan toegang krijgen tot bronnen in het virtuele netwerk.

- Intern: de API Management Gateway en de ontwikkelaars Portal zijn alleen toegankelijk vanuit het virtuele netwerk via een interne load balancer. De gateway kan toegang krijgen tot bronnen in het virtuele netwerk.

Binnenkomend en uitgaand verkeer in het subnet waarin API Management wordt geïmplementeerd, kunnen worden beheerd met behulp van de netwerk beveiligings groep.

- [Azure API Management gebruiken met virtuele netwerken](api-management-using-with-vnet.md)

- [Azure API Management-service gebruiken met een intern virtueel netwerk](api-management-using-with-internal-vnet.md)

- [API Management in een intern VNET integreren met Application Gateway](api-management-howto-integrate-internal-vnet-appgateway.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: de configuratie en het verkeer van virtuele netwerken, subnetten en netwerk interfaces bewaken en vastleggen

**Richt lijnen**: binnenkomend en uitgaand verkeer in het subnet waarin API Management wordt geïmplementeerd, kunnen worden beheerd met behulp van netwerk beveiligings groepen (nsg's). Implementeer een NSG op uw API Management-subnet en schakel NSG-stroom Logboeken in en verzend logboeken naar een Azure Storage account voor verkeers controle. U kunt ook NSG-stroom logboeken naar een Log Analytics-werk ruimte verzenden en Traffic Analytics gebruiken om inzicht te krijgen in de verkeers stroom in uw Azure-Cloud. Enkele voor delen van Traffic Analytics zijn de mogelijkheid om netwerk activiteiten te visualiseren en HOTS pots te identificeren, beveiligings dreigingen te identificeren, verkeers patronen te begrijpen en netwerk configuraties te lokaliseren.

Let op: bij het configureren van een NSG op het subnet van API Management, moet er een set poorten zijn die open moeten zijn. Als een van deze poorten niet beschikbaar is, werkt API Management mogelijk niet goed en wordt deze mogelijk niet meer toegankelijk.

- [Meer informatie over NSG-configuraties voor Azure API Management](./api-management-using-with-vnet.md#-common-network-configuration-issues)

- [NSG-stroom logboeken inschakelen](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Traffic Analytics inschakelen en gebruiken](../network-watcher/traffic-analytics.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="13-protect-critical-web-applications"></a>1,3: essentiële webtoepassingen beveiligen

**Richt lijnen**: voor het beveiligen van belang rijke Web/http-api's configureert u API Management in een Virtual Network (Vnet) in de interne modus en configureert u een Azure-toepassing gateway. Application Gateway is een PaaS-service. Het fungeert als een omgekeerde proxy en biedt N7-taak verdeling, route ring, Web Application Firewall (WAF) en andere services.

Als u API Management die is ingericht in een intern Vnet combineert met de Application Gateway front-end, worden de volgende scenario's ingeschakeld:
- Gebruik één API Management resource om alle Api's aan zowel interne consumenten als externe consumenten weer te geven.
- Gebruik één API Management resource om een subset van Api's aan externe gebruikers toe te staan.
- Bieden een manier om toegang tot API Management van het open bare Internet te scha kelen in-en uitschakelen.

Opmerking: deze functie is beschikbaar in de Premium-en Developer-laag van API Management.

- [API Management integreren in een intern VNET met Application Gateway](api-management-howto-integrate-internal-vnet-appgateway.md)

- [Azure-toepassing gateway begrijpen](../application-gateway/index.yml)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: communicatie met bekende schadelijke IP-adressen weigeren

**Hulp**: API Management configureren in een Virtual Network (Vnet) in de interne modus en een Azure-toepassing gateway configureren. Application Gateway is een PaaS-service. Het fungeert als een omgekeerde proxy en biedt N7-taak verdeling, route ring, Web Application Firewall (WAF) en andere services.

Als u API Management die is ingericht in een intern Vnet combineert met de Application Gateway front-end, worden de volgende scenario's ingeschakeld:
- Gebruik één API Management resource om alle Api's aan zowel interne consumenten als externe consumenten weer te geven.
- Gebruik één API Management resource om een subset van Api's aan externe gebruikers toe te staan.
- Bieden een manier om toegang tot API Management van het open bare Internet te scha kelen in-en uitschakelen.

Opmerking: deze functie is beschikbaar in de Premium-en Developer-laag van API Management.

Gebruik Azure Security Center geïntegreerde bedreigings informatie om communicatie met bekende of ongebruikte Internet-IP-adressen te weigeren.

- [API Management integreren in een intern VNET met Application Gateway](api-management-howto-integrate-internal-vnet-appgateway.md)

- [Azure-toepassing gateway begrijpen](../application-gateway/index.yml)

- [Meer informatie over Azure Security Center geïntegreerde bedreigings informatie](../security-center/azure-defender.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="15-record-network-packets"></a>1,5: netwerk pakketten opnemen

**Richt lijnen**: binnenkomend en uitgaand verkeer in het subnet waarin API Management wordt geïmplementeerd, kunnen worden beheerd met behulp van netwerk beveiligings groepen (NSG). Implementeer een NSG op uw API Management-subnet en schakel NSG-stroom Logboeken in en verzend logboeken naar een Azure Storage account voor verkeers controle. U kunt ook NSG-stroom logboeken naar een Log Analytics-werk ruimte verzenden en Traffic Analytics gebruiken om inzicht te krijgen in de verkeers stroom in uw Azure-Cloud. Enkele voor delen van Traffic Analytics zijn de mogelijkheid om netwerk activiteiten te visualiseren en HOTS pots te identificeren, beveiligings dreigingen te identificeren, verkeers patronen te begrijpen en netwerk configuraties te lokaliseren.

Let op: bij het configureren van een NSG op het subnet van API Management, moet er een set poorten zijn die open moeten zijn. Als een van deze poorten niet beschikbaar is, werkt API Management mogelijk niet goed en wordt deze mogelijk niet meer toegankelijk.

- [Meer informatie over NSG-configuraties voor Azure API Management](./api-management-using-with-vnet.md#-common-network-configuration-issues)

- [NSG-stroom logboeken inschakelen](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Traffic Analytics inschakelen en gebruiken](../network-watcher/traffic-analytics.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: op netwerk gebaseerde inbreuk detectie/indringings systemen (ID'S/IP-adressen) implementeren

**Hulp**: API Management configureren in een Virtual Network (Vnet) in de interne modus en een Azure-toepassing gateway configureren. Application Gateway is een PaaS-service. Het fungeert als een omgekeerde proxy en biedt N7-taak verdeling, route ring, Web Application Firewall (WAF) en andere services. Application Gateway WAF biedt beveiliging tegen veelvoorkomende beveiligings aanvallen en beveiligings problemen en kan in de volgende twee modi worden uitgevoerd:
- Detectiemodus: Hiermee worden alle waarschuwingen voor bedreigingen gecontroleerd en vastgelegd. U kunt logboek registratie diagnostiek voor Application Gateway inschakelen in de sectie diagnostische gegevens. U moet ervoor zorgen dat het WAF-logboek is geselecteerd en ingeschakeld. In de detectiemodus worden binnenkomende verzoeken niet geblokkeerd door Web Application Firewall.
- Preventiemodus: Blokkeert indringers en aanvallen die door de regels zijn gedetecteerd. De aanvaller krijgt een uitzondering '403 onbevoegde toegang' en de verbinding wordt verbroken. In de preventiemodus worden dergelijke aanvallen vastgelegd in de WAF-logboeken.

Als u API Management die is ingericht in een intern Vnet combineert met de Application Gateway front-end, worden de volgende scenario's ingeschakeld:
- Gebruik één API Management resource om alle Api's aan zowel interne consumenten als externe consumenten weer te geven.
- Gebruik één API Management resource om een subset van Api's aan externe gebruikers toe te staan.
- Bieden een manier om toegang tot API Management van het open bare Internet te scha kelen in-en uitschakelen.

Opmerking: deze functie is beschikbaar in de Premium-en Developer-laag van API Management.

- [API Management integreren in een intern VNET met Application Gateway](api-management-howto-integrate-internal-vnet-appgateway.md)

- [Azure-toepassing gateway WAF begrijpen](../web-application-firewall/ag/ag-overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="17-manage-traffic-to-web-applications"></a>1,7: verkeer naar webtoepassingen beheren

**Richt lijnen**: voor het beheren van verkeer dat wordt uitgevoerd op Web-en http-api's, implementeert u API Management op een Virtual Network (Vnet) dat is gekoppeld aan app service environment in de externe of interne modus.

Configureer in de interne modus een Azure-toepassing gateway vóór API Management. Application Gateway is een PaaS-service. Het fungeert als een omgekeerde proxy en biedt N7-taak verdeling, route ring, Web Application Firewall (WAF) en andere services. Application Gateway WAF biedt beveiliging tegen veelvoorkomende aanvallen en beveiligings problemen.

Als u API Management die is ingericht in een intern Vnet combineert met de Application Gateway front-end, worden de volgende scenario's ingeschakeld:
- Gebruik één API Management resource om alle Api's aan zowel interne consumenten als externe consumenten weer te geven.
- Gebruik één API Management resource om een subset van Api's aan externe gebruikers toe te staan.
- Bieden een manier om toegang tot API Management van het open bare Internet te scha kelen in-en uitschakelen.

Opmerking: deze functie is beschikbaar in de Premium-en Developer-laag van API Management.

- [Persoonlijke Api's beschikbaar stellen voor externe consumenten](/azure/architecture/example-scenario/apps/publish-internal-apis-externally)

- [API Management binnen een Vnet gebruiken](api-management-using-with-vnet.md)

- [Azure Web Application Firewall voor Azure Application Gateway](../web-application-firewall/ag/ag-overview.md)

- [Azure-toepassing gateway begrijpen](../application-gateway/overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: de complexiteit en administratieve overhead van netwerk beveiligings regels minimaliseren

**Hulp**: gebruik Virtual Network (Vnet)-service tags om netwerk toegangs beheer te definiëren voor netwerk beveiligings groepen (nsg's) die worden gebruikt op uw API Management subnetten. U kunt servicetags gebruiken in plaats van specifieke IP-adressen wanneer u beveiligingsregels maakt. Door de naam van de service label (bijvoorbeeld ApiManagement) op te geven in het juiste bron-of doel veld van een regel, kunt u het verkeer voor de bijbehorende service toestaan of weigeren. Micro soft beheert de adres voorvoegsels die zijn opgenomen in het servicetag van de service en werkt de servicetag automatisch bij met gewijzigde adressen.

Let op: bij het configureren van een NSG op het subnet van API Management, moet er een set poorten zijn die open moeten zijn. Als een van deze poorten niet beschikbaar is, werkt API Management mogelijk niet goed en wordt deze mogelijk niet meer toegankelijk.

- [Service Tags leren en gebruiken](../virtual-network/service-tags-overview.md)

- [Poorten die vereist zijn voor de API Management](./api-management-using-with-vnet.md#-common-network-configuration-issues)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: standaard beveiligings configuraties voor netwerk apparaten onderhouden

**Hulp**: Definieer en implementeer standaard beveiligings configuraties voor netwerk instellingen met betrekking tot uw Azure API Management-implementaties. Gebruik Azure Policy aliassen in de naam ruimten ' micro soft. ApiManagement ' en ' micro soft. Network ' om aangepast beleid te maken om de netwerk configuratie van uw Azure API Management-implementaties en gerelateerde resources te controleren of af te dwingen.

U kunt ook Azure-blauw drukken gebruiken om grootschalige Azure-implementaties te vereenvoudigen door sleutel omgevings artefacten, zoals Azure Resource Manager sjablonen, Toegangs beheer op basis van rollen (Azure RBAC) en beleids regels in één blauw definitie te verpakken. U kunt de blauw druk eenvoudig Toep assen op nieuwe abonnementen, omgevingen en het beheer en de verwerkings mogelijkheden van versies.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Een Azure Blueprint maken](../governance/blueprints/create-blueprint-portal.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="110-document-traffic-configuration-rules"></a>1,10: configuratie regels voor het document verkeer

**Hulp**: Labels gebruiken voor netwerk beveiligings groepen (nsg's) en andere bronnen die betrekking hebben op netwerk beveiliging en verkeers stroom. Voor afzonderlijke NSG-regels kunt u het veld Beschrijving gebruiken om de bedrijfs behoefte en/of-duur op te geven voor alle regels die verkeer van of naar een netwerk toestaan.

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

- [Een Virtual Network maken](../virtual-network/quick-create-portal.md)

- [Een NSG maken met een beveiligings configuratie](../virtual-network/tutorial-filter-network-traffic.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: gebruik automatische hulpprogram ma's om netwerk bron configuraties te bewaken en wijzigingen te detecteren

**Hulp**: Azure-activiteiten logboek gebruiken voor het bewaken van netwerk bron configuraties en het detecteren van wijzigingen aan netwerk bronnen die zijn gekoppeld aan uw Azure API Management-implementaties. Maak waarschuwingen in Azure Monitor die worden geactiveerd wanneer er wijzigingen in kritieke netwerk bronnen plaatsvinden.

- [Activiteiten logboek gebeurtenissen van Azure weer geven en ophalen](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Waarschuwingen maken in Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="logging-and-monitoring"></a>Logboekregistratie en bewaking

*Zie [Azure Security Bench Mark: Logging and monitoring](../security/benchmarks/security-control-logging-monitoring.md)(Engelstalig) voor meer informatie.*

### <a name="22-configure-central-security-log-management"></a>2,2: Centraal beveiligings logboek beheer configureren

**Richt lijnen**: u kunt in de Azure monitor log Analytics werk ruimte (n) gebruiken om een query uit te voeren, logboeken te verzenden naar Azure Storage voor langdurige, archiverings-of offline-analyses of Logboeken te exporteren naar een andere analyse oplossing in Azure en elders met behulp van Azure Event hubs. In azure API Management worden logboeken en metrische gegevens standaard naar Azure Monitor uitgevoerd. De uitgebreidheid van de logboek registratie kan worden geconfigureerd op basis van de gehele service en per API.

Azure API Management kan naast Azure Monitor worden geïntegreerd met een of meerdere Azure-toepassing Insights-Services. Registratie-instellingen voor Application Insights kunnen worden geconfigureerd op basis van een service of per API.

Indien gewenst, inschakelen en on-board gegevens voor Azure Sentinel of een beveiligings incident en gebeurtenis beheer van derden (SIEM).

- [Diagnostische instellingen configureren](../azure-monitor/essentials/diagnostic-settings.md#create-in-azure-portal)

- [Azure-Sentinel onboarden](../sentinel/quickstart-onboard.md)

- [Aan de slag met Azure Monitor en integratie van SIEM van derden](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

- [Aangepaste logboek registratie en analyse pijplijn maken](api-management-howto-log-event-hubs.md)

- [Integreren met Azure-toepassing Insights](api-management-howto-app-insights.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: controle logboek registratie inschakelen voor Azure-resources

**Richt lijnen**: voor het vastleggen van controle op vlak audits, de diagnostische instellingen van Azure-activiteiten logboek inschakelen en activiteiten logboeken verzenden naar een log Analytics-werk ruimte voor rapportage en analyse, tot Azure Storage voor lange termijn veilig bewaren, naar Azure Event hubs voor export in andere analyse oplossingen in Azure en elders. Met Azure-activiteiten logboek gegevens kunt u de ' What, wie en wanneer ' bepalen voor schrijf bewerkingen (PUT, POST, DELETE) die zijn uitgevoerd op het niveau van het besturings element van uw Azure API Management service.

Voor logboek registratie van gegevens vlak controle biedt Diagnostische logboeken uitgebreide informatie over bewerkingen en fouten die belang rijk zijn voor controle en probleem oplossing. Diagnoselogboeken verschillen van activiteitenlogboeken. Activiteitenlogboeken bieden inzicht in de bewerkingen die zijn uitgevoerd op uw Azure-resources. Diagnoselogboeken bieden inzicht in bewerkingen die door de resources zelf zijn uitgevoerd.

- [Diagnostische instellingen voor Azure-activiteiten logboek inschakelen](../azure-monitor/essentials/activity-log.md)

- [Diagnostische instellingen inschakelen voor Azure API Management](./api-management-howto-use-azure-monitor.md#resource-logs)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="25-configure-security-log-storage-retention"></a>2,5: Bewaar beveiliging van het beveiligings logboek configureren

**Hulp**: stel binnen Azure monitor uw Bewaar periode voor log Analytics werk ruimte in volgens de nalevings voorschriften van uw organisatie. Gebruik Azure Storage-accounts voor lange termijn/archiverings opslag.

- [Para meters voor het bewaren van Logboeken instellen voor Log Analytics-werk ruimten](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

- [Logboeken archiveren in een Azure Storage-account](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="26-monitor-and-review-logs"></a>2,6: Logboeken bewaken en controleren

**Hulp**: Azure API Management maakt voortdurend logboeken en metrische gegevens voor Azure monitor, waardoor u een bijna realtime inzicht krijgt in de status en status van uw api's. Met Azure Monitor en Log Analytics werk ruimte (n) kunt u de weer geven, query's uitvoeren, visualiseren, archiveren, waarschuwingen configureren en acties uitvoeren op metrische gegevens en logboeken die afkomstig zijn van API Management en gerelateerde resources. Analyseer en bewaak logboeken voor afwijkend gedrag en Bekijk regel matige resultaten.

U kunt API Management eventueel integreren met Azure-toepassing inzichten en deze gebruiken als primair of secundair bewakings-, tracerings-, rapportage-en waarschuwings programma.

- [Logboeken voor Azure API Management controleren en bekijken](./api-management-howto-use-azure-monitor.md)

- [Aangepaste query's uitvoeren in Azure Monitor](../azure-monitor/logs/get-started-queries.md)

- [Log Analytics-werk ruimte begrijpen](../azure-monitor/logs/log-analytics-tutorial.md)

- [Integreren met Azure-toepassing Insights](api-management-howto-app-insights.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: waarschuwingen inschakelen voor afwijkende activiteiten

**Hulp**: Schakel Diagnostische instellingen voor Azure-activiteiten logboek in, evenals de diagnostische instellingen voor uw Azure API Management-instanties en verzend de logboeken naar een log Analytics-werk ruimte. Query's uitvoeren in Log Analytics om termen te zoeken, trends te identificeren, patronen te analyseren en veel andere inzichten te bieden op basis van de verzamelde gegevens. U kunt waarschuwingen maken op basis van uw Log Analytics werkruimte query's.

Maak metrische waarschuwingen om u te laten weten wanneer er iets onverwachts plaatsvindt. U kunt bijvoorbeeld meldingen ontvangen wanneer uw Azure API Management-exemplaar de verwachte piek capaciteit over een bepaalde periode heeft overschreden of als er een bepaald aantal gateway-aanvragen of-fouten is opgetreden gedurende een vooraf gedefinieerde periode.

U kunt API Management eventueel integreren met Azure-toepassing inzichten en deze gebruiken als primair of secundair bewakings-, tracerings-, rapportage-en waarschuwings programma.

Optioneel kunt u gegevens in-en inschakelen voor Azure Sentinel of een SIEM van derden.

- [Diagnostische instellingen voor Azure-activiteiten logboek inschakelen](../azure-monitor/essentials/activity-log.md)

- [Diagnostische instellingen inschakelen voor Azure API Management](./api-management-howto-use-azure-monitor.md#resource-logs)

- [Een waarschuwings regel configureren voor Azure API Management](./api-management-howto-use-azure-monitor.md#set-up-an-alert-rule)

- [Metrische gegevens over capaciteit van een Azure API Management-exemplaar weer geven](api-management-capacity.md)

- [Integreren met Azure-toepassing Insights](api-management-howto-app-insights.md)

- [Azure-Sentinel onboarden](../sentinel/quickstart-onboard.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="identity-and-access-control"></a>Identiteits- en toegangsbeheer

*Zie [Azure Security Bench Mark: Identity and Access Control](../security/benchmarks/security-control-identity-access-control.md)voor meer informatie.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: een inventaris van beheerders accounts onderhouden

**Richt lijnen**: een inventaris van accounts met beheerders toegang tot de Azure API Management Control-besturings vlak (Azure Portal) onderhouden.

Azure Active Directory (Azure AD) heeft ingebouwde rollen die expliciet moeten worden toegewezen en waarop query's kunnen worden doorzocht. API Management is afhankelijk van deze rollen en Role-Based Access Control om verfijnd toegangs beheer voor API Management Services en entiteiten in te scha kelen.

Daarnaast bevat API Management een ingebouwde groep Administrators in het gebruikers systeem van de API Management. Groepen in API Management beheersen de zicht baarheid van Api's in de ontwikkelaars Portal en de leden van de groep Administrators kunnen alle Api's zien.

Volg de aanbevelingen van Azure Security Center voor het beheer en onderhoud van beheerders accounts.

- [Op rollen gebaseerd toegangsbeheer gebruiken in API Management](api-management-role-based-access-control.md)

- [Een lijst met gebruikers verkrijgen onder een Azure API Management-exemplaar](/powershell/module/az.apimanagement/get-azapimanagementuser)

- [Een lijst weer geven met gebruikers die zijn toegewezen aan een directory-rol in azure AD met Power shell](/powershell/module/az.resources/get-azroleassignment)

- [Een directory-roldefinitie ophalen in azure AD met Power shell](/powershell/module/az.resources/get-azroledefinition)

- [Meer informatie over identiteits-en toegangs aanbevelingen van Azure Security Center](../security-center/recommendations-reference.md#identityandaccess-recommendations)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="32-change-default-passwords-where-applicable"></a>3,2: standaard wachtwoorden wijzigen indien van toepassing

**Hulp**: Azure API Management heeft niet het concept van standaard wachtwoorden/-sleutel.

Azure API Management-abonnementen, wat een manier is om de toegang tot Api's te beveiligen, zijn echter wel een paar gegenereerde abonnements sleutels. Klanten kunnen deze abonnements sleutels op elk gewenst moment opnieuw genereren.

- [Informatie over Azure API Management-abonnementen](api-management-subscriptions.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: speciale beheerders accounts gebruiken

**Richt lijnen**: Maak standaard procedures voor het gebruik van specifieke beheerders accounts. Gebruik Azure Security Center identiteits-en toegangs beheer om het aantal beheerders accounts te bewaken.

Daarnaast kunt u aanbevelingen van Azure Security Center of ingebouwde Azure-beleids regels gebruiken om u te helpen bij het bijhouden van specifieke beheerders accounts, zoals:
- Er moet meer dan één eigenaar zijn toegewezen aan uw abonnement
- Afgeschafte accounts met eigenaarsmachtigingen moeten worden verwijderd uit uw abonnement
- Externe accounts met eigenaarsmachtigingen moeten worden verwijderd uit uw abonnement

- [Azure Security Center gebruiken om identiteit en toegang te bewaken (preview)](../security-center/security-center-identity-access.md)

- [Azure Policy gebruiken](../governance/policy/tutorials/create-and-manage.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: gebruik Azure Active Directory eenmalige aanmelding (SSO)

**Richt lijnen**: Azure API Management kan worden geconfigureerd om gebruik te maken van Azure Active Directory (Azure AD) als een id-provider voor het verifiëren van gebruikers op de ontwikkelaars Portal om te profiteren van de SSO-mogelijkheden van Azure AD. Nadat de gebruikers zijn geconfigureerd, kunnen ze het out-of-the-box-aanmeldings proces volgen door eerst te verifiëren via Azure AD en vervolgens het aanmeldings proces op de portal uit te voeren nadat het is geverifieerd.

- [Ontwikkel ontwikkelaars accounts met behulp van Azure AD in azure API Management](api-management-howto-aad.md)

U kunt ook het aanmeldings-en aanmeldings proces verder aanpassen via delegering. Met delegering kunt u uw bestaande website gebruiken voor het afhandelen van ontwikkel aars die zich aanmelden/registreren en abonneren op producten, in tegens telling tot het gebruik van de ingebouwde functionaliteit in de ontwikkelaars Portal. Hiermee kan uw website eigenaar worden van de gebruikers gegevens en kan de validatie van deze stappen op een aangepaste manier worden uitgevoerd.

- [Gebruikersregistratie en productabonnement delegeren](api-management-howto-setup-delegation.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: multi-factor Authentication gebruiken voor alle op Azure Active Directory gebaseerde toegang

**Hulp**: Schakel Azure Active Directory (Azure AD) multi-factor Authentication in en volg Azure Security Center aanbevelingen voor identiteits-en toegangs beheer.

- [Multi-factor Authentication inschakelen in azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Identiteit en toegang bewaken in Azure Security Center](../security-center/security-center-identity-access.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: gebruik speciale machines (privileged Access workstations) voor alle beheer taken

**Richt lijnen**: gebruik privileged Access workstations (Paw) met multi-factor Authentication die is geconfigureerd om Azure-resources aan te melden en te configureren.

- [Meer informatie over privileged Access workstations](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)
  
- [Multi-factor Authentication inschakelen in azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: Logboeken en waarschuwingen voor verdachte activiteiten van beheerders accounts

**Hulp**: gebruik Azure Active Directory (Azure AD) PRIVILEGED Identity Management (PIM) voor het genereren van Logboeken en waarschuwingen wanneer verdachte of onveilige activiteiten in de omgeving worden uitgevoerd.

Daarnaast kunt u met Azure AD-risico detectie waarschuwingen en rapporten bekijken over Risk ante gebruikers gedrag.

- [Privileged Identity Management implementeren (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Meer informatie over Azure AD-risico detectie](../active-directory/identity-protection/overview-identity-protection.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: Azure-resources alleen beheren vanaf goedgekeurde locaties

**Hulp**: gebruik benoemde locaties voor voorwaardelijke toegang om alleen toegang toe te staan tot de Azure Portal vanuit specifieke logische groepen met IP-adresbereiken of landen/regio's.

- [Benoemde locaties configureren in azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="39-use-azure-active-directory"></a>3,9: Azure Active Directory gebruiken

**Richt lijnen**: als dit mogelijk is, gebruikt u Azure Active Directory (Azure AD) als centrale verificatie-en autorisatie systeem. Azure AD beveiligt gegevens door gebruik te maken van sterke versleuteling voor gegevens in rust en onderweg. Azure AD bevat ook zouten, hashes en veilige gebruikers referenties.

Configureer uw Azure API Management-ontwikkelaars Portal om ontwikkelaars accounts te verifiëren met behulp van Azure AD.

Configureer uw Azure API Management-exemplaar om uw Api's te beveiligen met behulp van het OAuth 2,0-protocol met Azure AD.

- [Ontwikkelaars accounts autoriseren met behulp van Azure AD in azure API Management](api-management-howto-aad.md)

- [Een API beveiligen met behulp van OAuth 2,0 met Azure AD en API Management](api-management-howto-protect-backend-with-aad.md)

- [Een Azure AD-instantie maken en configureren](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: regel matig gebruikers toegang controleren en afstemmen

**Hulp**: Azure Active Directory (Azure AD) biedt logboeken waarmee u verlopen accounts kunt detecteren. Klanten kunnen Azure Identity Access revisies gebruiken om groepslid maatschappen, toegang tot bedrijfs toepassingen en roltoewijzingen op efficiënte wijze te beheren. Gebruikers toegang kan regel matig worden gecontroleerd om ervoor te zorgen dat alleen de juiste gebruikers toegang blijven houden.

Klanten kunnen de inventaris van API Management gebruikers accounts onderhouden en zo nodig de toegang afstemmen. In API Management zijn ontwikkel aars de gebruikers van de Api's die beschikbaar zijn gemaakt met API Management. Nieuw gemaakte ontwikkelaars accounts zijn standaard actief en zijn gekoppeld aan de groep ontwikkel aars. Ontwikkelaars accounts die zich in een actieve status bevinden, kunnen worden gebruikt voor toegang tot alle Api's waarvoor ze abonnementen hebben.

Beheerders kunnen aangepaste groepen maken of gebruikmaken van externe groepen in gekoppelde Azure AD-tenants. Aangepaste en externe groepen kunnen naast systeemgroepen worden gebruikt om ontwikkelaars zichtbaarheid van en toegang tot API-producten te geven.

- [Gebruikersaccounts in Azure API Management beheren](api-management-howto-create-or-invite-developers.md)

- [Een lijst met API Management gebruikers ophalen](/powershell/module/az.apimanagement/get-azapimanagementuser)

- [Groepen maken en gebruiken voor beheer van ontwikkelaarsaccounts in Azure API Management](api-management-howto-create-groups.md)

- [Beoordelingen over Azure Identity Access gebruiken](../active-directory/governance/access-reviews-overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: controle pogingen om toegang te krijgen tot gedeactiveerde referenties

**Hulp**: Configureer uw Azure API Management-exemplaar voor het verifiëren van accounts voor ontwikkel aars met behulp van Azure Active Directory (Azure AD) als een id-provider in azure API management.

Configureer uw Azure API Management-exemplaar om uw Api's te beveiligen met behulp van het OAuth 2,0-protocol met Azure AD.

Configureer het JWT-validatie beleid voor inkomende API-aanvragen om het bestaan en de geldigheid van een geldig token af te dwingen.

Diagnostische instellingen voor Azure AD-gebruikers accounts maken en de audit logboeken en aanmeldings logboeken verzenden naar een Log Analytics-werk ruimte. Gewenste waarschuwingen configureren in Log Analytics. Daarnaast kunt u de Log Analytics-werk ruimte onboarden naar Azure Sentinel of een SIEM van derden.

Configureer geavanceerde bewaking met API Management met behulp van het `log-to-eventhub` beleid, leg eventuele aanvullende context informatie vast die vereist is voor de analyse van de beveiliging en verzend een bericht naar een Siem van Azure Sentinel of van derden.

- [Ontwikkelaars accounts autoriseren met behulp van Azure AD in azure API Management](api-management-howto-aad.md)

- [Een API beveiligen met behulp van OAuth 2,0 met Azure AD en API Management](api-management-howto-protect-backend-with-aad.md)

- [API Management access restriction policies](api-management-access-restriction-policies.md) (Beleid voor toegangsbeperking API Management)

- [Azure AD-logboeken integreren in Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Azure-Sentinel aan de trein](../sentinel/quickstart-onboard.md)

- [Geavanceerde bewaking van Api's](api-management-log-to-eventhub-sample.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: waarschuwing voor de afwijking van het aanmeldings gedrag van het account

**Richt lijnen**: voor de afwijking van het aanmeldings gedrag van accounts op het besturings vlak (het Azure Portal), gebruikt u de functies van Azure Active Directory (Azure AD) identiteits beveiliging en risico detectie om automatische antwoorden te configureren op gedetecteerde verdachte acties die betrekking hebben op gebruikers identiteiten. U kunt ook gegevens opnemen in azure Sentinel voor verder onderzoek.

- [Riskante Azure AD-aanmeldingen weergeven](../active-directory/identity-protection/overview-identity-protection.md)

- [Risico beleid voor identiteits beveiliging configureren en inschakelen](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Azure-Sentinel onboarden](../sentinel/quickstart-onboard.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: micro soft biedt toegang tot relevante klant gegevens tijdens ondersteunings scenario's

**Hulp**: momenteel niet beschikbaar; Klanten-lockbox wordt momenteel niet ondersteund voor Azure API Management.

- [Lijst met door Klanten-lockbox ondersteunde services](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="data-protection"></a>Gegevensbeveiliging

*Zie [Azure Security Benchmark: gegevensbescherming](../security/benchmarks/security-control-data-protection.md) voor meer informatie.*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: een inventaris van gevoelige informatie onderhouden

**Hulp**: Tags gebruiken om Azure-resources te helpen bij het bijhouden of verwerken van gevoelige informatie.

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: systemen isoleren die gevoelige informatie opslaan of verwerken

**Richt lijnen**: afzonderlijke abonnementen en/of beheer groepen implementeren voor ontwikkeling, testen en productie. Azure API Management-exemplaren moeten worden gescheiden door de/subnet van het virtuele netwerk (VNet) en worden gelabeld.

- [Aanvullende Azure-abonnementen maken](../cost-management-billing/manage/create-subscription.md)

- [Beheergroepen maken](../governance/management-groups/create-management-group-portal.md)

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

- [Azure API Management gebruiken met virtuele netwerken](api-management-using-with-vnet.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: niet-geautoriseerde overdracht van gevoelige gegevens controleren en blok keren

**Hulp**: momenteel niet beschikbaar; de functies voor gegevens identificatie, classificatie en verlies preventie zijn momenteel niet beschikbaar voor Azure API Management.

Micro soft beheert de onderliggende infra structuur voor Azure API Management en heeft strikte controles geïmplementeerd om verlies of bloot stelling van klant gegevens te voor komen.

- [Informatie over beveiliging van klantgegevens in Azure](../security/fundamentals/protection-customer-data.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: alle gevoelige gegevens in de overdracht versleutelen

**Hulp**: beheer vlak aanroepen worden gedaan via Azure Resource Manager via TLS. Er is een geldig JSON-webtoken (JWT) vereist. Aanroepen voor gegevens vlak kunnen worden beveiligd met TLS en een van de ondersteunde verificatie mechanismen (bijvoorbeeld client certificaat of JWT).

- [Gegevens beveiliging in azure API Management begrijpen](#data-protection)

- [TLS-instellingen in azure API Management beheren](api-management-howto-manage-protocols-ciphers.md)

- [Api's in azure API Management beveiligen met Azure Active Directory (Azure AD)](api-management-howto-protect-backend-with-aad.md)

- [Api's in azure API Management beveiligen met Azure AD B2C](howto-protect-backend-frontend-azure-ad-b2c.md)

**Verantwoordelijkheid**: Gedeeld

**Azure Security Center bewaking**: geen

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: een actief detectie hulpprogramma gebruiken om gevoelige gegevens te identificeren

**Richt lijnen**: nog niet beschikbaar; de functies voor gegevens identificatie, classificatie en verlies preventie zijn nog niet beschikbaar voor Azure API Management. Codeer Azure API Management Services die mogelijk gevoelige informatie verwerken als zodanig en implementeer oplossingen van derden als dat nodig is voor nalevings doeleinden.

Voor het onderliggende platform dat door micro soft wordt beheerd, behandelt micro soft alle inhoud van de klant als gevoelig en gaat u naar een fantastische lengte om te beschermen tegen verlies en bloot stelling van klant gegevens. Om ervoor te zorgen dat klant gegevens binnen Azure veilig blijven, heeft micro soft een reeks robuuste besturings elementen en mogelijkheden voor gegevens bescherming geïmplementeerd en onderhouden.

- [Informatie over beveiliging van klantgegevens in Azure](../security/fundamentals/protection-customer-data.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: Azure RBAC gebruiken om de toegang tot resources te beheren 

**Richt lijnen**: gebruik Azure op rollen gebaseerd toegangs beheer (Azure RBAC) voor het beheren van de toegang tot Azure API management. Azure API Management is afhankelijk van op rollen gebaseerd toegangs beheer op basis van Azure voor het inschakelen van nauw keurig toegang voor API Management Services en entiteiten (bijvoorbeeld Api's en beleids regels).

- [Toegangs beheer op basis van rollen gebruiken in azure API Management](api-management-role-based-access-control.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: voor komen dat gegevens verlies op basis van host wordt gebruikt voor het afdwingen van toegangs beheer

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources.

Micro soft beheert de onderliggende infra structuur voor Azure API Management en heeft strikte controles geïmplementeerd om verlies of bloot stelling van klant gegevens te voor komen.

- [Informatie over beveiliging van klantgegevens in Azure](../security/fundamentals/protection-customer-data.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: wijzigingen in essentiële Azure-resources vastleggen en waarschuwen

**Hulp**: gebruik Azure monitor met het Azure-activiteiten logboek om waarschuwingen te maken voor wanneer wijzigingen worden aangebracht in productie-Azure functions apps, evenals andere kritieke of gerelateerde resources.

- [Waarschuwingen maken voor gebeurtenissen in het Azure-activiteiten logboek](../azure-monitor/alerts/alerts-activity-log.md)

- [Azure Monitor en Azure-activiteiten logboek gebruiken in azure API Management](api-management-howto-use-azure-monitor.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="vulnerability-management"></a>Beheer van beveiligingsproblemen

*Zie voor meer informatie de [Azure Security Bench Mark: beveiligingslek beheer](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: automatische hulpprogram ma's voor het scannen van beveiligings problemen uitvoeren

**Hulp**: momenteel niet beschikbaar; de evaluatie van beveiligings problemen in Azure Security Center is momenteel niet beschikbaar voor Azure API Management.

Onderliggend platform gescand en bijgewerkt door micro soft. Bekijk beveiligings controles die beschikbaar zijn om beveiligings problemen met betrekking tot de service configuratie te verminderen.

- [Informatie over beveiligings besturings elementen die beschikbaar zijn voor Azure API Management]()

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: geautomatiseerde oplossing voor patch beheer voor besturings systemen implementeren

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: Implementeer een oplossing voor geautomatiseerd patch beheer voor software titels van derden

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: vergelijken van back-to-back-problemen

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: een risico classificatie proces gebruiken om prioriteit te geven aan het herstel van ontdekte beveiligings problemen

**Hulp**: momenteel niet beschikbaar; de evaluatie van beveiligings problemen in Azure Security Center is momenteel niet beschikbaar voor Azure API Management.

Onderliggend platform gescand en bijgewerkt door micro soft. Klant voor het beoordelen van de beschik bare beveiligings controles om beveiligings problemen met betrekking tot de service configuratie te verminderen.


**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="inventory-and-asset-management"></a>Inventarisatie en Asset Management

*Zie [Azure Security Bench Mark: Inventory and Asset Management](../security/benchmarks/security-control-inventory-asset-management.md)voor meer informatie.*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: automatische Asset-detectie oplossing gebruiken

**Hulp**: Azure resource Graph gebruiken voor het opvragen/detecteren van alle resources (zoals compute, opslag, netwerk, poorten en protocollen enz.) binnen uw abonnement (en). Zorg ervoor dat de juiste (Lees-) machtigingen voor uw Tenant en alle Azure-abonnementen en resources in uw abonnementen inventariseren.

Hoewel klassieke Azure-resources kunnen worden gedetecteerd via resource grafiek, is het raadzaam om Azure Resource Manager resources te maken en te gebruiken.

- [Query's maken met Azure resource Graph](../governance/resource-graph/first-query-portal.md)

- [Uw Azure-abonnementen weer geven](/powershell/module/az.accounts/get-azsubscription?preserve-view=true&view=azps-4.8.0)

- [Meer informatie over Azure RBAC](../role-based-access-control/overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="62-maintain-asset-metadata"></a>6,2: meta gegevens van activa onderhouden

**Richt lijnen**: Tags Toep assen op Azure-resources die meta gegevens geven om ze logisch in een taxonomie te organiseren.

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: niet-geautoriseerde Azure-resources verwijderen

**Richt lijnen**: Gebruik labels, beheer groepen en afzonderlijke abonnementen, waar nodig, om Azure-resources te organiseren en bij te houden. Sluit de inventaris regel matig af en zorg ervoor dat niet-geautoriseerde resources tijdig worden verwijderd uit het abonnement.

Daarnaast kunt u met Azure Policy beperkingen opleggen aan het type resources dat kan worden gemaakt in klant abonnement (en) met de volgende ingebouwde beleids definities:
- Niet toegestane resourcetypen
- Toegestane brontypen

- [Aanvullende Azure-abonnementen maken](../cost-management-billing/manage/create-subscription.md)

- [Beheergroepen maken](../governance/management-groups/create-management-group-portal.md)

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: de inventaris van goedgekeurde Azure-resources definiëren en onderhouden

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: monitor voor niet-goedgekeurde Azure-resources

**Hulp: gebruik** Azure Policy om beperkingen toe te voegen voor het type resources dat kan worden gemaakt in uw abonnement (en) met de volgende ingebouwde beleids definities:
- Niet toegestane resourcetypen
- Toegestane brontypen

Gebruik Azure resource Graph voor het opvragen/detecteren van resources binnen hun abonnement (en). Zorg ervoor dat alle Azure-resources die aanwezig zijn in de omgeving, zijn goedgekeurd.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Query's maken met Azure Graph](../governance/resource-graph/first-query-portal.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: monitor voor niet-goedgekeurde software toepassingen binnen reken resources

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: niet-goedgekeurde Azure-resources en software toepassingen verwijderen

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="68-use-only-approved-applications"></a>6,8: alleen goedgekeurde toepassingen gebruiken

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="69-use-only-approved-azure-services"></a>6,9: alleen goedgekeurde Azure-Services gebruiken

**Hulp: gebruik** Azure Policy om beperkingen toe te voegen voor het type resources dat kan worden gemaakt in klant abonnement (en) met de volgende ingebouwde beleids definities:
- Niet toegestane resourcetypen
- Toegestane brontypen

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Een specifiek resource type weigeren met Azure Policy](../governance/policy/samples/built-in-policies.md#general)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: een inventaris van goedgekeurde software titels onderhouden

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: de mogelijkheid van gebruikers om te communiceren met Azure Resource Manager beperken

**Hulp** bij het configureren van voorwaardelijke toegang van Azure om gebruikers de mogelijkheid te bieden om te communiceren met Azure Resource Manager door ' blok toegang ' te configureren voor de app Microsoft Azure management.

- [Voorwaardelijke toegang configureren om de toegang tot Azure Resource Manager te blok keren](../role-based-access-control/conditional-access-azure-management.md)

- [Toegangs beheer op basis van rollen in azure API Management](api-management-role-based-access-control.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: de mogelijkheid van gebruikers om scripts uit te voeren binnen reken bronnen beperken

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: toepassingen met een hoog risico fysiek of logisch scheiden

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor webtoepassingen die worden uitgevoerd op Azure App Service of reken bronnen.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="secure-configuration"></a>Veilige configuratie

*Zie [Azure Security Bench Mark: Secure Configuration](../security/benchmarks/security-control-secure-configuration.md)(Engelstalig) voor meer informatie.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: veilige configuraties instellen voor alle Azure-resources

**Hulp**: Definieer en implementeer standaard beveiligings configuraties voor uw Azure API Management-service met Azure Policy. Gebruik Azure Policy aliassen in de naam ruimte ' micro soft. ApiManagement ' om aangepaste beleids regels te maken om de configuratie van uw Azure API Management-Services te controleren of af te dwingen.

- [Beschik bare Azure Policy aliassen weer geven](/powershell/module/az.resources/get-azpolicyalias?preserve-view=true&view=azps-4.8.0)

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: veilige configuraties van besturings systemen instellen

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: Beveilig Azure-resource configuraties onderhouden

**Hulp**: Definieer en implementeer standaard beveiligings configuraties voor uw Azure API Management-services met Azure Policy. Gebruik Azure Policy aliassen in de naam ruimte ' micro soft. ApiManagement ' om aangepaste beleids regels te maken om de configuratie van Azure API Management-exemplaren te controleren of af te dwingen. Gebruik Azure Policy [deny] en [implementeren indien niet aanwezig] voor het afdwingen van beveiligde instellingen voor uw Azure-resources.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy effecten begrijpen](../governance/policy/concepts/effects.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: veilige configuraties van besturings systemen onderhouden

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources.

**Verantwoordelijkheid**: Gedeeld

**Azure Security Center bewaking**: geen

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: de configuratie van Azure-resources veilig opslaan

**Richt lijnen**: als u aangepaste definities van Azure-beleid gebruikt, kunt u Azure DevOps of Azure opslag plaatsen gebruiken om uw Azure API Management service-configuratie veilig op te slaan en te beheren.

- [Bestanden opslaan in azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Documentatie voor Azure opslag plaatsen](/azure/devops/repos/)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: aangepaste installatie kopieën van een besturings systeem veilig opslaan

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: hulpprogram ma's voor configuratie beheer voor Azure-resources implementeren

**Hulp**: Definieer en implementeer standaard beveiligings configuraties voor uw Azure API Management-services met Azure Policy. Gebruik Azure Policy aliassen in de naam ruimte ' micro soft. ApiManagement ' om aangepaste beleids regels te maken om de configuratie van Azure API Management-exemplaren te controleren of af te dwingen. Gebruik Azure Policy [deny] en [implementeren indien niet aanwezig] voor het afdwingen van beveiligde instellingen voor uw Azure-resources.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy effecten begrijpen](../governance/policy/concepts/effects.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: hulpprogram ma's voor configuratie beheer voor besturings systemen implementeren

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: geautomatiseerde configuratie bewaking voor Azure-resources implementeren

**Hulp**: gebruik de Azure API Management DevOps Resource Kit om configuratie beheer voor Azure API Management uit te voeren.

Daarnaast definieert en implementeert u standaard beveiligings configuraties voor uw Azure API Management-Services met Azure Policy. Gebruik Azure Policy aliassen in de naam ruimte ' micro soft. ApiManagement ' om aangepaste beleids regels te maken om de configuratie van Azure API Management-exemplaren te controleren of af te dwingen. Gebruik Azure Policy [deny] en [implementeren indien niet aanwezig] voor het afdwingen van beveiligde instellingen voor uw Azure-resources.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy effecten begrijpen](../governance/policy/concepts/effects.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: geautomatiseerde configuratie bewaking voor besturings systemen implementeren

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: identiteiten veilig en automatisch beheren

**Hulp**: gebruik Managed Service Identity gegenereerd door Azure Active Directory (Azure AD) om uw API Management-exemplaar eenvoudig en veilig toegang te geven tot andere met Azure AD beveiligde resources, zoals Azure Key Vault.

- [Een beheerde identiteit voor een API Management-exemplaar maken](api-management-howto-use-managed-service-identity.md)

- [Beleid voor verificatie met beheerde identiteit](./api-management-authentication-policies.md#ManagedIdentity)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: onbedoelde referentie blootstelling elimineren

**Richt lijnen**: referentie scanner implementeren om referenties in code te identificeren. Door het gebruik van Credential Scanner worden gebruikers ook aangemoedigd om gedetecteerde referenties naar veiligere locaties, zoals Azure Key Vault, te verplaatsen.

- [Referentie scanner instellen](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="malware-defense"></a>Beveiliging tegen malware

*Zie voor meer informatie de [Azure Security Bench Mark: beveiliging tegen schadelijke software](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: centraal beheerde anti-malware-software gebruiken

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources.

Micro soft anti-malware is ingeschakeld op de onderliggende host die ondersteuning biedt voor Azure-Services (bijvoorbeeld Azure API Management), maar wordt niet uitgevoerd op de inhoud van de klant.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: scan bestanden die moeten worden geüpload naar niet-reken resources van Azure

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor niet-reken resources die zijn ontworpen om gegevens op te slaan.

Micro soft anti-malware is ingeschakeld op de onderliggende host die ondersteuning biedt voor Azure-Services (bijvoorbeeld Azure API Management), maar wordt niet uitgevoerd op de inhoud van de klant.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: controleren of anti-malware-software en hand tekeningen zijn bijgewerkt

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor niet-reken resources die zijn ontworpen om gegevens op te slaan.

Micro soft anti-malware is ingeschakeld op de onderliggende host die ondersteuning biedt voor Azure-Services (bijvoorbeeld Azure API Management), maar wordt niet uitgevoerd op de inhoud van de klant.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="data-recovery"></a>Gegevensherstel

*Zie [Azure Security Bench Mark: Data Recovery](../security/benchmarks/security-control-data-recovery.md)(Engelstalig) voor meer informatie.*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: zorg voor regel matige automatische back-ups

**Richt lijnen**: door uw api's te publiceren en beheren via Azure API Management, profiteert u van de fout tolerantie en infrastructuur mogelijkheden die u op een andere manier hand matig ontwerpt, implementeert en beheert. API Management ondersteunt implementatie met meerdere regio's, waardoor het gegevens vlak ondoordringbaar is voor regionale storingen zonder dat er operationele overhead hoeft te worden toegevoegd.

De functies voor het maken en herstellen van back-ups van API Management bieden de benodigde bouw stenen voor het implementeren van een nood herstel strategie. Back-up-en herstel bewerkingen kunnen hand matig of automatisch worden uitgevoerd.

- [API Management data-vlak implementeren in meerdere regio's](api-management-howto-deploy-multi-region.md)

- [Noodherstel implementeren met back-up en herstellen van services in Azure API Management](./api-management-howto-disaster-recovery-backup-restore.md#calling-the-backup-and-restore-operations)

- [De back-upbewerking van API Management aanroepen](/rest/api/apimanagement/2019-12-01/apimanagementservice/backup)

- [De herstel bewerking van API Management aanroepen](/rest/api/apimanagement/2019-12-01/apimanagementservice/restore)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: volledige back-ups van het systeem uitvoeren en een back-up maken van door de klant beheerde sleutels

**Richt lijnen**: back-up-en herstel bewerkingen van Azure API Management volledige back-up en herstel van het systeem uitvoeren.

Beheerde identiteiten kunnen worden gebruikt voor het verkrijgen van certificaten van Azure Key Vault voor API Management aangepaste domein namen. Back-ups maken van certificaten die worden opgeslagen in Azure Key Vault.

- [Noodherstel implementeren met back-up en herstellen van services in Azure API Management](./api-management-howto-disaster-recovery-backup-restore.md#calling-the-backup-and-restore-operations)

- [Back-up maken van Azure Key Vault certificaten](/powershell/module/az.keyvault/backup-azkeyvaultcertificate?preserve-view=true&view=azps-4.8.0)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: alle back-ups valideren, inclusief door de klant beheerde sleutels

**Hulp** bij het valideren van back-ups door het herstellen van de service en certificaten van back-ups te testen.

- [De herstel bewerking van API Management aanroepen](/rest/api/apimanagement/2019-12-01/apimanagementservice/restore)

- [Azure Key Vault certificaten herstellen](/powershell/module/az.keyvault/restore-azkeyvaultcertificate)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: zorg voor de bescherming van back-ups en door de klant beheerde sleutels

**Hulp**: Azure API Management schrijft back-ups naar Azure Storage accounts van de klant. Volg de aanbevelingen van Azure Storage beveiliging om uw back-up te beveiligen.

- [Noodherstel implementeren met back-up en herstellen van services in Azure API Management](./api-management-howto-disaster-recovery-backup-restore.md#calling-the-backup-and-restore-operations)

- [Beveiligings aanbeveling voor Blob Storage](../storage/blobs/security-recommendations.md)

Schakel Soft-Delete in Key Vault in om sleutels te beschermen tegen onbedoelde of schadelijke verwijdering.

- [Soft-Delete in Key Vault inschakelen](../storage/blobs/soft-delete-blob-overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="incident-response"></a>Reageren op incidenten

*Zie [Azure Security Benchmark: respons op incidenten](../security/benchmarks/security-control-incident-response.md) voor meer informatie.*

### <a name="101-create-an-incident-response-guide"></a>10,1: een hand leiding voor reactie op incidenten maken

**Richtlijnen**: Stel voor uw organisatie een responshandleiding op voor gebruik bij incidenten. Zorg ervoor dat er schriftelijke responsplannen zijn waarin alle rollen van het personeel worden gedefinieerd, evenals alle fasen in het afhandelen/managen van incidenten, vanaf de detectie van het incident tot een evaluatie ervan achteraf.

- [Richt lijnen voor het bouwen van uw eigen beveiligings incident antwoord proces](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Micro soft Security Response Center anatomie van een incident](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [De verwerkings handleiding voor het computer beveiligings incident van het NIST gebruiken om u te helpen bij het maken van uw eigen reactie plan voor incidenten](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: een beoordelings procedure voor incidenten en prioriteits procedures maken

**Hulp**: Security Center wijst aan elke waarschuwing een Ernst toe om u te helpen bepalen welke waarschuwingen het eerst moeten worden onderzocht. De ernst is gebaseerd op de manier waarop vertrouwen Security Center is in de zoek actie of de analyse die wordt gebruikt om de waarschuwing te geven, evenals het betrouwbaarheids niveau waarvan er sprake is van schadelijke opzet achter de activiteit die tot de waarschuwing heeft geleid.

Daarnaast kunt u ook duidelijk abonnementen markeren (voor bijvoorbeeld productie, niet-productie) met behulp van tags en maak een naamgevings systeem om Azure-resources duidelijk te identificeren en te categoriseren, met name voor de verwerking van gevoelige gegevens. Het is uw verantwoordelijkheid om prioriteit te geven aan het oplossen van waarschuwingen op basis van de ernst van de Azure-resources en -omgeving waarin het incident heeft plaatsgevonden.

- [Beveiligingswaarschuwingen in Azure Security Center](../security-center/security-center-alerts-overview.md)

- [Tags gebruiken om Azure-resources te organiseren](../azure-resource-manager/management/tag-resources.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="103-test-security-response-procedures"></a>10,3: procedures voor beveiligings antwoorden testen

**Richt lijnen**: oefent oefeningen uit om de respons mogelijkheden van uw systeem te testen op een reguliere uitgebracht om uw Azure-resources te beschermen. Stel vast waar zich zwakke plekken en hiaten bevinden, en wijzig zo nodig het plan.

- [Publicatie van het NIST-hand leiding voor test-, trainings-en oefen Programma's voor IT-plannen en-mogelijkheden](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: contact gegevens van het beveiligings incident opgeven en waarschuwings meldingen configureren voor beveiligings incidenten

**Hulp**: contact gegevens van beveiligings incidenten worden door micro soft gebruikt om contact met u op te nemen als het micro soft Security Response Center (MSRC) detecteert dat uw gegevens zijn geopend door een onrecht matige of niet-gemachtigde partij. Bekijk incidenten na het feit om te controleren of de problemen zijn opgelost.

- [De Azure Security Center Security-contact persoon instellen](../security-center/security-center-provide-security-contact-details.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: beveiligings waarschuwingen opnemen in uw reactie systeem van uw incident

**Hulp**: exporteer uw Azure Security Center waarschuwingen en aanbevelingen met behulp van de functie continue export om Risico's voor Azure-resources te identificeren. Met doorlopend exporteren kunt u waarschuwingen en aanbevelingen hand matig of op een doorlopende manier exporteren. U kunt de Azure Security Center Data Connector gebruiken om de waarschuwingen naar Azure Sentinel te streamen.

- [Continue export configureren](../security-center/continuous-export.md)

- [Waarschuwingen streamen naar Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: de reactie op beveiligings waarschuwingen automatiseren

**Hulp**: gebruik de automatiserings functie voor werk stromen in azure Security Center om automatisch reacties te activeren via ' Logic apps ' in beveiligings waarschuwingen en aanbevelingen.

- [Werk stroom automatisering en Logic Apps configureren](../security-center/workflow-automation.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="penetration-tests-and-red-team-exercises"></a>Penetratietests en Red Team-oefeningen

*Zie [Azure Security Bench Mark: Indringings tests en rode team oefeningen](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)voor meer informatie.*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: voert regel matig indringings tests van uw Azure-resources uit en zorgt voor herbemiddeling van alle essentiële beveiligings resultaten

**Richt lijnen**: Volg de micro soft-regels om ervoor te zorgen dat de indringings tests niet worden geschonden door het micro soft-beleid: https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1 .

- [U vindt hier meer informatie over de strategie van micro soft en de uitvoering van Red Teaming en live site indringings tests ten opzichte van micro soft Managed Cloud Infrastructure, services en toepassingen.](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Verantwoordelijkheid**: Gedeeld

**Azure Security Center bewaking**: geen

## <a name="next-steps"></a>Volgende stappen

- Zie [Overzicht Azure Security Benchmark V2](../security/benchmarks/overview.md)
- Meer informatie over [Azure-beveiligingsbasislijnen](../security/benchmarks/security-baselines-overview.md)
