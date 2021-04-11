---
title: Azure-beveiligings basislijn voor Azure Functions
description: De Azure Functions Security Baseline voorziet in procedure richtlijnen en resources voor het implementeren van de beveiligings aanbevelingen die zijn opgegeven in de Azure Security-Bench Mark.
author: msmbaldwin
ms.service: azure-functions
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 8987c4ac61e81c35f664e19dfc72936bb6754e18
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105566950"
---
# <a name="azure-security-baseline-for-azure-functions"></a>Azure-beveiligings basislijn voor Azure Functions

In deze beveiligings basislijn worden richt lijnen van de [Azure Security Bench Mark-versie 1,0](../security/benchmarks/overview-v1.md) ingesteld op Azure functions. De Azure Security-benchmark biedt aanbevelingen voor hoe u uw cloudoplossingen in Azure kunt beveiligen.
De inhoud wordt gegroepeerd op de **beveiligings controles** die zijn gedefinieerd door de Azure Security-benchmark en de bijbehorende richt lijnen die van toepassing zijn op Azure functions. **Besturings elementen** die niet van toepassing zijn op Azure functions, zijn uitgesloten.

 
Als u wilt zien hoe Azure Functions volledig is toegewezen aan de beveiligings benchmark van Azure, raadpleegt u het [volledige Azure functions beveiligings basislijn toewijzings bestand](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Netwerkbeveiliging

*Zie [Azure Security Benchmark: netwerkbeveiliging](../security/benchmarks/security-control-network-security.md) voor meer informatie.*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Azure-resources in virtuele netwerken beveiligen

**Hulp**: integreer uw Azure functions-apps met een virtueel Azure-netwerk. Functie-apps die worden uitgevoerd in het Premium-abonnement hebben dezelfde hosting mogelijkheden als web-apps in Azure App Service, waaronder de functie VNet-integratie.  Met virtuele Azure-netwerken kunt u veel van uw Azure-resources, zoals Azure Functions, in een niet-Internet routeerbaar netwerk plaatsen.

- [Functies integreren met een Azure-Virtual Network](functions-create-vnet.md)

- [Meer informatie over Vnet-integratie voor Azure Functions en Azure App Service](../app-service/web-sites-integrate-with-vnet.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: de [Security Bench Mark van Azure](/azure/governance/policy/samples/azure-security-benchmark) is het standaard beleids initiatief voor Security Center en is de basis voor de [aanbevelingen van Security Center](/azure/security-center/security-center-recommendations). De Azure Policy definities die aan dit besturings element zijn gerelateerd, worden automatisch door Security Center ingeschakeld. Voor waarschuwingen met betrekking tot dit besturings element is mogelijk een [Azure Defender](/azure/security-center/azure-defender) -plan vereist voor de gerelateerde services.

**Ingebouwde definities Azure Policy-micro soft. Web**:

[!INCLUDE [Resource Policy for Microsoft.Web 1.1](../../includes/policy/standards/asb/rp-controls/microsoft.web-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: de configuratie en het verkeer van virtuele netwerken, subnetten en netwerk interfaces bewaken en vastleggen

**Hulp**: gebruik Azure Security Center en volg aanbevelingen voor netwerk beveiliging om netwerk bronnen en netwerk configuraties te beveiligen die betrekking hebben op uw Azure functions-apps.

Als u netwerk beveiligings groepen met uw Azure Functions-implementatie gebruikt, schakelt u stroom logboeken voor netwerk beveiligings groepen in en verzendt u logboeken naar een Azure Storage account voor verkeers controles. U kunt ook stroom logboeken voor netwerk beveiligings groepen naar een Log Analytics-werk ruimte verzenden en Traffic Analytics gebruiken om inzicht te krijgen in de verkeers stroom in uw Azure-Cloud. Enkele voor delen van Traffic Analytics zijn de mogelijkheid om netwerk activiteiten te visualiseren en HOTS pots te identificeren, beveiligings dreigingen te identificeren, verkeers patronen te begrijpen en netwerk configuraties te lokaliseren.

- [Informatie over de netwerk beveiliging die wordt verschaft door Azure Security Center](../security-center/security-center-network-recommendations.md)

- [NSG-stroom logboeken inschakelen](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Traffic Analytics inschakelen en gebruiken](../network-watcher/traffic-analytics.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="13-protect-critical-web-applications"></a>1,3: essentiële webtoepassingen beveiligen

**Hulp**: als u uw Azure functions-eind punten in productie volledig wilt beveiligen, kunt u overwegen om een van de volgende functie-beveiligings opties op app-niveau te implementeren:

- App Service-verificatie of-autorisatie inschakelen voor uw functie-app

- Azure API Management (APIM) gebruiken om aanvragen te verifiëren

- Implementeer uw functie-app in een Azure App Service Environment.

Zorg er bovendien voor dat externe fout opsporing is uitgeschakeld voor uw productie Azure Functions. Daarnaast mogen door middel van cross-Origin Resource Sharing (CORS) niet alle domeinen toegang hebben tot uw functie-app in Azure. Sta toe dat alleen de vereiste domeinen kunnen communiceren met uw functie-app.

Overweeg om Azure Web Application firewall (WAF) te implementeren als onderdeel van de netwerk configuratie voor extra inspectie van binnenkomend verkeer. Schakel de diagnostische instelling voor WAF-en opname Logboeken in een opslag account, Event hub of Log Analytics werk ruimte in. 

- [Azure Functions-eind punten in de productie omgeving beveiligen](./functions-bindings-http-webhook-trigger.md?tabs=csharp#secure-an-http-endpoint-in-production)

- [Azure WAF implementeren](../web-application-firewall/ag/create-waf-policy-ag.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: de [Security Bench Mark van Azure](/azure/governance/policy/samples/azure-security-benchmark) is het standaard beleids initiatief voor Security Center en is de basis voor de [aanbevelingen van Security Center](/azure/security-center/security-center-recommendations). De Azure Policy definities die aan dit besturings element zijn gerelateerd, worden automatisch door Security Center ingeschakeld. Voor waarschuwingen met betrekking tot dit besturings element is mogelijk een [Azure Defender](/azure/security-center/azure-defender) -plan vereist voor de gerelateerde services.

**Ingebouwde definities Azure Policy-micro soft. Web**:

[!INCLUDE [Resource Policy for Microsoft.Web 1.3](../../includes/policy/standards/asb/rp-controls/microsoft.web-1-3.md)]

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: communicatie met bekende schadelijke IP-adressen weigeren

**Hulp**: Schakel DDoS Protection standaard in op de virtuele netwerken die zijn gekoppeld aan uw functie-apps om te beschermen tegen DDoS-aanvallen. Gebruik functies van bedreigings informatie in Azure Security Center geïntegreerd om communicaties te weigeren met bekende of ongebruikte open bare IP-adressen.

Daarnaast kunt u een front-end-gateway, zoals Azure Web Application firewall, configureren om alle inkomende aanvragen te verifiëren en schadelijk verkeer te filteren. Met de firewall van Azure Web Application kunt u uw functie-app beveiligen door inkomend webverkeer te controleren om SQL-injecties, cross-site scripting, uploads van malware en DDoS-aanvallen te blok keren. De introductie van een firewall voor webtoepassingen vereist een App Service Environment of het gebruik van privé-eind punten. U kunt privé-eind punten gebruiken voor uw functies die worden gehost in de Premium-en App Service-abonnementen.

- [Netwerkopties van Azure Functions](functions-networking-options.md)

- [DDoS-beveiliging configureren](../ddos-protection/manage-ddos-protection.md)

- [Meer informatie over Azure Security Center geïntegreerde bedreigings informatie](../security-center/azure-defender.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="15-record-network-packets"></a>1,5: netwerk pakketten opnemen

**Richt lijnen**: als u een netwerk beveiligings groep met uw Azure functions-implementatie gebruikt, schakelt u stroom logboeken voor netwerk beveiligings groepen in en verzendt u logboeken naar een opslag account voor verkeers controle. U kunt ook stroom logboeken naar een Log Analytics-werk ruimte verzenden en Traffic Analytics gebruiken om inzicht te krijgen in de verkeers stroom in uw Azure-Cloud. Enkele voor delen van Traffic Analytics zijn de mogelijkheid om netwerk activiteiten te visualiseren en HOTS pots te identificeren, beveiligings dreigingen te identificeren, verkeers patronen te begrijpen en netwerk configuraties te lokaliseren.

- [Stroom logboeken van netwerk beveiligings groepen inschakelen](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Traffic Analytics inschakelen en gebruiken](../network-watcher/traffic-analytics.md)

- [Network Watcher inschakelen](../network-watcher/network-watcher-create.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: op netwerk gebaseerde inbreuk detectie/indringings systemen (ID'S/IP-adressen) implementeren

**Richt lijnen**: Configureer een front-end-gateway, zoals Azure Web Application firewall, om alle inkomende aanvragen te verifiëren en schadelijk verkeer te filteren. Met de firewall van Azure Web Application kunt u uw functie-apps beveiligen door inkomend webverkeer te controleren om SQL-injecties, cross-site scripting, uploads van malware en DDoS-aanvallen te blok keren. De introductie van een firewall voor webtoepassingen vereist een App Service Environment of het gebruik van privé-eind punten. U kunt privé-eind punten gebruiken voor uw functies die worden gehost in de Premium-en App Service-abonnementen.

U kunt ook meerdere Marketplace-opties gebruiken, zoals de Barracuda Web Application Firewall voor Azure die beschikbaar zijn op de Azure Marketplace, die indringings detectie of preventie functies omvatten.

- [Netwerkopties van Azure Functions](functions-networking-options.md)

- [Azure Functions Premium-abonnement](functions-premium-plan.md)

- [Inleiding tot Azure App Service Environments](../app-service/environment/intro.md)

- [Netwerkoverwegingen voor een App Service-omgeving](../app-service/environment/network-info.md) 

- [Meer informatie over Azure Web Application firewall](../web-application-firewall/overview.md)

- [Privé-eind punten gebruiken voor Azure Functions](../app-service/networking/private-endpoint.md)

- [Meer informatie over Barracuda WAF Cloud service](../app-service/environment/app-service-app-service-environment-web-application-firewall.md#configuring-your-barracuda-waf-cloud-service)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="17-manage-traffic-to-web-applications"></a>1,7: verkeer naar webtoepassingen beheren

**Richt lijnen**: Configureer een front-end-gateway voor uw netwerk, zoals de firewall van Azure-webtoepassingen met end-to-end TLS-code ring. De introductie van een firewall voor webtoepassingen vereist een App Service Environment of het gebruik van privé-eind punten. U kunt privé-eind punten gebruiken voor uw functies die worden gehost in de Premium-en App Service-abonnementen.

- [Netwerkopties van Azure Functions](functions-networking-options.md)

- [Azure Functions Premium-abonnement](functions-premium-plan.md)

- [Inleiding tot Azure App Service Environments](../app-service/environment/intro.md)

- [Netwerkoverwegingen voor een App Service-omgeving](../app-service/environment/network-info.md) 

- [Meer informatie over Azure Web Application firewall](../web-application-firewall/overview.md)

- [End-to-end TLS configureren met behulp van Application Gateway met de portal](../application-gateway/end-to-end-ssl-portal.md)

- [Privé-eind punten gebruiken voor Azure Functions](../app-service/networking/private-endpoint.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: de complexiteit en administratieve overhead van netwerk beveiligings regels minimaliseren

**Hulp**: gebruik Virtual Network Service Tags om netwerk toegangs beheer in te stellen voor de netwerk beveiligings groep of Azure firewall. U kunt servicetags gebruiken in plaats van specifieke IP-adressen wanneer u beveiligingsregels maakt. Door de naam van de service label (bijvoorbeeld AzureAppService) op te geven in het juiste bron-of doel veld van een regel, kunt u het verkeer voor de bijbehorende service toestaan of weigeren. Micro soft beheert de adres voorvoegsels die zijn opgenomen in het servicetag van de service en werkt de servicetag automatisch bij met gewijzigde adressen.

- [Voor meer informatie over het gebruik van service Tags](../virtual-network/service-tags-overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: standaard beveiligings configuraties voor netwerk apparaten onderhouden

**Hulp**: Definieer en implementeer standaard beveiligings configuraties voor netwerk instellingen met betrekking tot uw Azure functions. Gebruik Azure Policy aliassen in de naam ruimten ' micro soft. Web ' en ' micro soft. Network ' om aangepaste beleids regels te maken om de netwerk configuratie van uw Azure Functions te controleren of af te dwingen. U kunt ook gebruik maken van ingebouwde beleids definities voor Azure Functions, zoals:

- CORS mag niet elke resource toestaan om toegang te krijgen tot uw functie-apps

- De functie-app mag alleen toegankelijk zijn via HTTPS

- De laatste TLS-versie moet worden gebruikt in uw functie-app

U kunt ook Azure-blauw drukken gebruiken om grootschalige Azure-implementaties te vereenvoudigen door sleutel omgevings artefacten, zoals Azure Resource Manager sjablonen, Toegangs beheer op basis van rollen (Azure RBAC) en beleids regels in één blauw definitie te verpakken. U kunt de blauw druk eenvoudig Toep assen op nieuwe abonnementen, omgevingen en het beheer en de verwerkings mogelijkheden van versies.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Een Azure Blueprint maken](../governance/blueprints/create-blueprint-portal.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="110-document-traffic-configuration-rules"></a>1,10: configuratie regels voor het document verkeer

**Richt lijnen**: als u de netwerk beveiligings groep gebruikt met uw Azure functions-implementatie, gebruikt u labels voor de nsg's en andere bronnen die betrekking hebben op netwerk beveiliging en verkeers stroom. Gebruik voor afzonderlijke regels voor de netwerk beveiligings groep het veld Beschrijving om de bedrijfs behoeften en/of de duur op te geven, voor alle regels die verkeer van/naar een netwerk toestaan.

Gebruik een van de ingebouwde Azure-beleids definities die betrekking hebben op labelen, zoals ' vereist label en de waarde ', om ervoor te zorgen dat alle resources met tags worden gemaakt en u op de hoogte moet zijn van bestaande niet-gelabelde resources.

U kunt Azure PowerShell of Azure CLI gebruiken om op basis van hun labels acties op resources te zoeken of uit te voeren.

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: gebruik automatische hulpprogram ma's om netwerk bron configuraties te bewaken en wijzigingen te detecteren

**Hulp**: Azure-activiteiten logboek gebruiken om netwerk resource configuraties te bewaken en wijzigingen te detecteren voor netwerk instellingen en-bronnen die betrekking hebben op uw Azure functions-implementaties. Maak waarschuwingen in Azure Monitor die worden geactiveerd wanneer er wijzigingen in essentiële netwerk instellingen of-resources plaatsvinden. 

- [Activiteiten logboek gebeurtenissen van Azure weer geven en ophalen](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Waarschuwingen maken in Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="logging-and-monitoring"></a>Logboekregistratie en bewaking

*Zie [Azure Security Bench Mark: Logging and monitoring](../security/benchmarks/security-control-logging-monitoring.md)(Engelstalig) voor meer informatie.*

### <a name="22-configure-central-security-log-management"></a>2,2: Centraal beveiligings logboek beheer configureren

**Richt lijnen**: voor het bijhouden van de controle op vliegtuig controles, schakelt u Diagnostische instellingen voor Azure-activiteiten logboek in en verzendt u de logboeken naar een log Analytics-werk ruimte, Azure Event hub of Azure Storage-account voor archivering. Met Azure-activiteiten logboek gegevens kunt u de ' What, wie en wanneer ' bepalen voor schrijf bewerkingen (PUT, POST, DELETE) die zijn uitgevoerd op het niveau van het controle vlak voor uw Azure-resources.

Azure Functions biedt ook ingebouwde integratie met Azure-toepassing inzichten om functies te bewaken. Application Insights verzamelt logboek-, prestatie-en fout gegevens. Het detecteert automatisch prestatie afwijkingen en bevat krachtige analyse hulpprogramma's waarmee u problemen kunt vaststellen en inzicht krijgt in de manier waarop uw functies worden gebruikt.

Als u in uw functie-app ingebouwde beveiligings-en controle logboek registratie hebt, schakelt u de diagnostische instelling ' FunctionAppLogs ' in en verzendt u de logboeken naar een Log Analytics-werk ruimte, een Azure Event Hub of een Azure-opslag account voor archivering. 

Optioneel kunt u gegevens in-en inschakelen voor Azure Sentinel of een systeem informatie en gebeurtenis beheer oplossing van derden. 

- [Diagnostische instellingen voor Azure-activiteiten logboek inschakelen](../azure-monitor/essentials/activity-log.md)

- [Azure Functions met Azure-toepassing inzichten instellen](functions-monitoring.md)

- [Diagnostische instellingen inschakelen (door de gebruiker gegenereerde logboeken) voor Azure Functions](functions-monitor-log-analytics.md)

- [Azure-Sentinel onboarden](../sentinel/quickstart-onboard.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: controle logboek registratie inschakelen voor Azure-resources

**Richt lijnen**: voor het bijhouden van de controle op vliegtuig controles, schakelt u Diagnostische instellingen voor Azure-activiteiten logboek in en verzendt u de logboeken naar een log Analytics-werk ruimte, Azure Event hub of Azure Storage-account voor archivering. Met Azure-activiteiten logboek gegevens kunt u de ' What, wie en wanneer ' bepalen voor schrijf bewerkingen (PUT, POST, DELETE) die zijn uitgevoerd op het niveau van het controle vlak voor uw Azure-resources.

Als u in uw functie-app ingebouwde beveiligings-en controle logboek registratie hebt, schakelt u de diagnostische instelling ' FunctionAppLogs ' in en verzendt u de logboeken naar een Log Analytics-werk ruimte, een Azure Event Hub of een Azure-opslag account voor archivering. 

- [Diagnostische instellingen voor Azure-activiteiten logboek inschakelen](../azure-monitor/essentials/activity-log.md)

- [Diagnostische instellingen inschakelen (door de gebruiker gegenereerde logboeken) voor Azure Functions](functions-monitor-log-analytics.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: de [Security Bench Mark van Azure](/azure/governance/policy/samples/azure-security-benchmark) is het standaard beleids initiatief voor Security Center en is de basis voor de [aanbevelingen van Security Center](/azure/security-center/security-center-recommendations). De Azure Policy definities die aan dit besturings element zijn gerelateerd, worden automatisch door Security Center ingeschakeld. Voor waarschuwingen met betrekking tot dit besturings element is mogelijk een [Azure Defender](/azure/security-center/azure-defender) -plan vereist voor de gerelateerde services.

**Ingebouwde definities Azure Policy-micro soft. Web**:

[!INCLUDE [Resource Policy for Microsoft.Web 2.3](../../includes/policy/standards/asb/rp-controls/microsoft.web-2-3.md)]

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: beveiligings logboeken verzamelen van besturings systemen

**Richt lijnen**: niet van toepassing; deze richt lijn is bedoeld voor IaaS Compute-resources.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="25-configure-security-log-storage-retention"></a>2,5: Bewaar beveiliging van het beveiligings logboek configureren

**Richt lijnen**: in azure monitor stelt u de Bewaar periode voor het logboek In voor log Analytics werk ruimten die zijn gekoppeld aan uw functie-apps volgens de nalevings voorschriften van uw organisatie.

- [Para meters voor het bewaren van Logboeken instellen](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="26-monitor-and-review-logs"></a>2,6: Logboeken bewaken en controleren

**Hulp**: Schakel Diagnostische instellingen voor Azure-activiteiten logboek in en de diagnostische instellingen voor uw functie-app en verzend de logboeken naar een log Analytics-werk ruimte. Query's uitvoeren in Log Analytics om termen te zoeken, trends te identificeren, patronen te analyseren en veel andere inzichten te bieden op basis van de verzamelde gegevens.

Schakel Application Insights in voor uw functie-apps voor het verzamelen van logboek-, prestatie-en fout gegevens. U kunt de telemetriegegevens weer geven die zijn verzameld door Application Insights binnen het Azure Portal.

Als u in uw functie-app ingebouwde beveiligings-en controle logboek registratie hebt, schakelt u de diagnostische instelling ' FunctionAppLogs ' in en verzendt u de logboeken naar een Log Analytics-werk ruimte, een Azure Event Hub of een Azure-opslag account voor archivering. 

Optioneel kunt u gegevens in-en inschakelen voor Azure Sentinel of een systeem informatie en gebeurtenis beheer oplossing van derden.

- [Diagnostische instellingen voor Azure-activiteiten logboek inschakelen](../azure-monitor/essentials/activity-log.md)

- [Diagnostische instellingen inschakelen voor Azure Functions](functions-monitor-log-analytics.md)

- [Azure Functions met Azure-toepassing inzichten instellen en de telemetriegegevens weer geven](functions-monitoring.md)

- [Azure-Sentinel onboarden](../sentinel/quickstart-onboard.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: waarschuwingen inschakelen voor afwijkende activiteiten

**Hulp**: Schakel Diagnostische instellingen voor Azure-activiteiten logboek in en de diagnostische instellingen voor uw functie-app en verzend de logboeken naar een log Analytics-werk ruimte. Query's uitvoeren in Log Analytics om termen te zoeken, trends te identificeren, patronen te analyseren en veel andere inzichten te bieden op basis van de verzamelde gegevens. U kunt waarschuwingen maken op basis van uw Log Analytics werkruimte query's.

Schakel Application Insights in voor uw functie-apps voor het verzamelen van logboek-, prestatie-en fout gegevens. U kunt de telemetriegegevens weer geven die zijn verzameld door Application Insights en waarschuwingen maken binnen de Azure Portal.

Optioneel kunt u gegevens in-en inschakelen voor Azure Sentinel of een systeem informatie en gebeurtenis beheer oplossing van derden.

- [Diagnostische instellingen voor Azure-activiteiten logboek inschakelen](../azure-monitor/essentials/activity-log.md)

- [Diagnostische instellingen inschakelen voor Azure Functions](functions-monitor-log-analytics.md)

- [Application Insights inschakelen voor Azure Functions](./configure-monitoring.md#enable-application-insights-integration)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="identity-and-access-control"></a>Identiteits- en toegangsbeheer

*Zie [Azure Security Bench Mark: Identity and Access Control](../security/benchmarks/security-control-identity-access-control.md)voor meer informatie.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: een inventaris van beheerders accounts onderhouden

**Hulp**: Azure Active Directory (Azure AD) heeft ingebouwde rollen die expliciet moeten worden toegewezen en waarop query's kunnen worden doorzocht. Gebruik de Azure AD Power shell-module om ad hoc-query's uit te voeren om accounts te detecteren die lid zijn van beheer groepen.

- [Een directory-rol verkrijgen in azure AD met Power shell](/powershell/module/azuread/get-azureaddirectoryrole?preserve-view=true&view=azureadps-2.0)

- [Leden van een directory-rol in azure AD ophalen met Power shell](/powershell/module/azuread/get-azureaddirectoryrolemember?preserve-view=true&view=azureadps-2.0)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="32-change-default-passwords-where-applicable"></a>3,2: standaard wachtwoorden wijzigen indien van toepassing

**Hulp**: beheer van toegang tot Azure functions wordt beheerd via Azure Active Directory (Azure AD). Azure AD heeft niet het concept van standaard wachtwoorden.

Toegang tot het gegevens vlak kan op verschillende manieren worden beheerd, waaronder autorisatie sleutels, netwerk beperkingen en het valideren van een Azure AD-identiteit. Autorisatie sleutels worden gebruikt door de clients die verbinding maken met uw Azure Functions HTTP-eind punten en kunnen op elk gewenst moment opnieuw worden gegenereerd. Deze sleutels worden standaard voor nieuwe HTTP-eind punten gegenereerd.

Er zijn meerdere implementatie methoden beschikbaar voor het gebruik van apps, waarvan sommige een set van gegenereerde referenties kunnen gebruiken. Controleer de implementatie methoden die voor uw toepassing worden gebruikt.

- [Een HTTP-eind punt beveiligen](./functions-bindings-http-webhook-trigger.md?tabs=csharp#secure-an-http-endpoint-in-production)

- [Autorisatie sleutels verkrijgen en opnieuw genereren](./functions-bindings-http-webhook-trigger.md?tabs=csharp#obtaining-keys)

- [Implementatie technologieën in Azure Functions](functions-deployment-technologies.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: speciale beheerders accounts gebruiken

**Richt lijnen**: Maak standaard procedures voor het gebruik van specifieke beheerders accounts. Gebruik Azure Security Center identiteits-en toegangs beheer om het aantal beheerders accounts te bewaken.

Daarnaast kunt u aanbevelingen van Azure Security Center of ingebouwde Azure-beleids regels gebruiken om u te helpen bij het bijhouden van specifieke beheerders accounts, zoals:

- Er moet meer dan één eigenaar zijn toegewezen aan uw abonnement

- Afgeschafte accounts met eigenaarsmachtigingen moeten worden verwijderd uit uw abonnement

- Externe accounts met eigenaarsmachtigingen moeten worden verwijderd uit uw abonnement

Meer informatie vindt u op de koppelingen waarnaar wordt verwezen.

- [Azure Security Center gebruiken om identiteit en toegang te bewaken ](../security-center/security-center-identity-access.md)

- [Azure Policy gebruiken](../governance/policy/tutorials/create-and-manage.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: gebruik Azure Active Directory eenmalige aanmelding (SSO)

**Richt lijnen**: gebruik waar mogelijk Azure Active Directory (Azure AD) SSO in plaats van afzonderlijke zelfstandige referenties voor gegevens toegang tot uw functie-app te configureren. Gebruik Azure Security Center aanbevelingen voor identiteits-en toegangs beheer. Implementeer eenmalige aanmelding voor uw functions-apps met behulp van de functie voor App Service verificatie/autorisatie.

- [Meer informatie over verificatie en autorisatie in Azure Functions](../app-service/overview-authentication-authorization.md#identity-providers)

- [Informatie over eenmalige aanmelding met Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

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

**Hulp**: gebruik Azure Active Directory (Azure AD) als centraal verificatie-en autorisatie systeem voor uw functie-apps. Azure AD beveiligt gegevens door gebruik te maken van sterke versleuteling voor gegevens in rust en onderweg. Azure AD bevat ook zouten, hashes en veilige gebruikers referenties.

- [De functie-app configureren voor het gebruik van Azure AD-aanmelding](../app-service/configure-authentication-provider-aad.md)

- [Een Azure AD-instantie maken en configureren](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: regel matig gebruikers toegang controleren en afstemmen

**Hulp**: Azure Active Directory (Azure AD) bevat logboeken waarmee u verouderde accounts kunt detecteren. Daarnaast kunt u Azure Identity Access revisies gebruiken om groepslid maatschappen en de toegang tot bedrijfs toepassingen en roltoewijzingen op efficiënte wijze te beheren. Gebruikers toegang kan regel matig worden gecontroleerd om ervoor te zorgen dat alleen de juiste gebruikers toegang hebben.

- [Meer informatie over Azure AD-rapportage](../active-directory/reports-monitoring/index.yml)

- [Beoordelingen over Azure Identity Access gebruiken](../active-directory/governance/access-reviews-overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: controle pogingen om toegang te krijgen tot gedeactiveerde referenties

**Hulp**: gebruik Azure Active Directory (Azure AD) als centraal verificatie-en autorisatie systeem voor uw functie-apps. Azure AD beveiligt gegevens door gebruik te maken van sterke versleuteling voor gegevens in rust en onderweg. Azure AD bevat ook zouten, hashes en veilige gebruikers referenties.

U hebt toegang tot de Azure AD-aanmeldings activiteit, de controle-en risico gebeurtenis logboek bronnen, waarmee u kunt integreren met Azure Sentinel of een SIEM van derden.

U kunt dit proces stroom lijnen door Diagnostische instellingen voor Azure AD-gebruikers accounts te maken en de audit logboeken en aanmeldings logboeken te verzenden naar een Log Analytics-werk ruimte. U kunt de gewenste logboek waarschuwingen configureren in Log Analytics.

- [De functie-app configureren voor het gebruik van Azure AD-aanmelding](../app-service/configure-authentication-provider-aad.md)

- [Azure-activiteitenlogboeken integreren in Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Azure-Sentinel aan de trein](../sentinel/quickstart-onboard.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: waarschuwing voor de afwijking van het aanmeldings gedrag van het account

**Hulp**: gebruik Azure Active Directory (Azure AD) als centraal verificatie-en autorisatie systeem voor uw functie-apps. Voor de afwijking van het gedrag van de account aanmelding op het besturings vlak (de Azure Portal), gebruikt u de functies Azure AD Identity Protection en risico detectie om automatische antwoorden te configureren op gedetecteerde verdachte acties met betrekking tot gebruikers identiteiten. U kunt ook gegevens opnemen in azure Sentinel voor verder onderzoek.

- [Riskante Azure AD-aanmeldingen weergeven](../active-directory/identity-protection/overview-identity-protection.md)

- [Risico beleid voor identiteits beveiliging configureren en inschakelen](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Azure-Sentinel onboarden](../sentinel/quickstart-onboard.md)

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

**Richt lijnen**: afzonderlijke abonnementen en/of beheer groepen implementeren voor ontwikkeling, testen en productie. Functie-apps moeten worden gescheiden door de/subnet van het virtuele netwerk (VNet) en moeten op de juiste wijze worden gelabeld.

U kunt ook privé-eind punten gebruiken om netwerk isolatie uit te voeren. Een persoonlijk Azure-eind punt is een netwerk interface waarmee u privé en veilig kunt verbinden met een service (bijvoorbeeld: functie-app HTTPs-eind punt) met een persoonlijke Azure-koppeling. Private Endpoint maakt gebruik van een privé-IP-adres van uw VNet, waarbij de service effectief in uw VNet wordt geplaatst. U kunt privé-eind punten gebruiken voor uw functies die worden gehost in de Premium-en App Service-abonnementen.

- [Aanvullende Azure-abonnementen maken](../cost-management-billing/manage/create-subscription.md)

- [Beheergroepen maken](../governance/management-groups/create-management-group-portal.md)

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

- [Netwerkopties van Azure Functions](functions-networking-options.md)

- [Azure Functions Premium-abonnement](functions-premium-plan.md)

- [Persoonlijke eind punt begrijpen](../private-link/private-endpoint-overview.md)

- [Privé-eind punten gebruiken voor Azure Functions](../app-service/networking/private-endpoint.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: alle gevoelige gegevens in de overdracht versleutelen

**Richt lijnen**: Schakel In de Azure portal voor uw functie-apps onder ' platform functies: netwerken: SSL ' de instelling ' alleen https ' in en stel de minimale TLS-versie in op 1,2.

- [HTTPS vereisen voor functie-apps](./security-concepts.md#require-https)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: de [Security Bench Mark van Azure](/azure/governance/policy/samples/azure-security-benchmark) is het standaard beleids initiatief voor Security Center en is de basis voor de [aanbevelingen van Security Center](/azure/security-center/security-center-recommendations). De Azure Policy definities die aan dit besturings element zijn gerelateerd, worden automatisch door Security Center ingeschakeld. Voor waarschuwingen met betrekking tot dit besturings element is mogelijk een [Azure Defender](/azure/security-center/azure-defender) -plan vereist voor de gerelateerde services.

**Ingebouwde definities Azure Policy-micro soft. Web**:

[!INCLUDE [Resource Policy for Microsoft.Web 4.4](../../includes/policy/standards/asb/rp-controls/microsoft.web-4-4.md)]

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: een actief detectie hulpprogramma gebruiken om gevoelige gegevens te identificeren

**Hulp**: momenteel niet beschikbaar; de functies voor gegevens identificatie, classificatie en verlies preventie zijn momenteel niet beschikbaar voor Azure Functions. Code functie-apps die mogelijk gevoelige informatie verwerken als zodanig en het implementeren van oplossingen van derden, indien nodig voor nalevings doeleinden.

Voor het onderliggende platform dat door micro soft wordt beheerd, behandelt micro soft alle inhoud van de klant als gevoelig en gaat u naar een fantastische lengte om te beschermen tegen verlies en bloot stelling van klant gegevens. Om ervoor te zorgen dat klant gegevens binnen Azure veilig blijven, heeft micro soft een reeks robuuste besturings elementen en mogelijkheden voor gegevens bescherming geïmplementeerd en onderhouden.

- [Informatie over beveiliging van klantgegevens in Azure](../security/fundamentals/protection-customer-data.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4,6: op rollen gebaseerd toegangs beheer gebruiken voor het beheren van de toegang tot bronnen

**Richt lijnen**: gebruik Azure RBAC (op rollen gebaseerd toegangs beheer) voor het beheren van de toegang tot het besturings element voor de functie-app (de Azure Portal).

- [Azure RBAC configureren](../role-based-access-control/role-assignments-portal.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: voor komen dat gegevens verlies op basis van host wordt gebruikt voor het afdwingen van toegangs beheer

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor IaaS Compute-resources.

Micro soft beheert de onderliggende infra structuur voor Azure Functions en heeft strikte controles geïmplementeerd om verlies of bloot stelling van klant gegevens te voor komen.

- [Informatie over beveiliging van klantgegevens in Azure](../security/fundamentals/protection-customer-data.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: gevoelige informatie op rest versleutelen

**Richt lijnen**: wanneer u een functie-app maakt, moet u een algemeen Azure Storage-account maken of koppelen dat ondersteuning biedt voor blob-, wachtrij-en tabel opslag. Dit komt omdat functies afhankelijk zijn van Azure Storage voor bewerkingen, zoals het beheren van triggers en de uitvoering van logboek functies. Azure Storage versleutelt alle gegevens in een opslag account in rust. Standaard worden gegevens versleuteld met door micro soft beheerde sleutels. Voor extra controle over versleutelings sleutels kunt u door de klant beheerde sleutels leveren voor versleuteling van BLOB-en bestands gegevens. Deze sleutels moeten aanwezig zijn in Azure Key Vault voor de functie-app om toegang te krijgen tot het opslag account.

- [Informatie over de opslag van Azure Functions](storage-considerations.md)

- [Meer informatie over Azure Storage-versleuteling voor Data-at-rest](../storage/common/storage-service-encryption.md)

**Verantwoordelijkheid**: Gedeeld

**Azure Security Center bewaking**: geen

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: wijzigingen in essentiële Azure-resources vastleggen en waarschuwen

**Hulp**: gebruik Azure monitor met het Azure-activiteiten logboek om waarschuwingen te maken voor wanneer wijzigingen worden aangebracht in productie functie-apps, evenals andere kritieke of verwante resources.

- [Waarschuwingen maken voor gebeurtenissen in het Azure-activiteiten logboek](../azure-monitor/alerts/alerts-activity-log.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="vulnerability-management"></a>Beheer van beveiligingsproblemen

*Zie voor meer informatie de [Azure Security Bench Mark: beveiligingslek beheer](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: automatische hulpprogram ma's voor het scannen van beveiligings problemen uitvoeren

**Richt lijnen**: Stel een DevSecOps-praktijk in om ervoor te zorgen dat uw functie-apps veilig zijn en zo veilig mogelijk blijven gedurende de levens cyclus. DevSecOps maakt gebruik van het beveiligings team van uw organisatie en hun mogelijkheden in uw DevOps-procedures om de verantwoordelijkheid van iedereen op het team te beveiligen.

Volg daarnaast de aanbevelingen van Azure Security Center om uw functie-apps te beveiligen.

- [Continue beveiligings validatie toevoegen aan uw CI/CD-pijp lijn](/azure/devops/migrate/security-validation-cicd-pipeline?preserve-view=true&view=azure-devops)

- [Aanbevelingen voor de evaluatie van Azure Security Center-beveiligings problemen implementeren](../security-center/deploy-vulnerability-assessment-vm.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: een risico classificatie proces gebruiken om prioriteit te geven aan het herstel van ontdekte beveiligings problemen

**Hulp**: micro soft voert beveiligings beheer uit op de onderliggende systemen die ondersteuning bieden voor Azure functions. u kunt echter ook de ernst van de aanbevelingen in azure Security Center gebruiken en de beveiligde Score om het risico binnen uw omgeving te meten. Uw beveiligde Score is gebaseerd op het aantal Security Center aanbevelingen dat u hebt verminderd. Denk na over de ernst van de aanbevelingen om eerst de prioriteit te bepalen.

- [Naslag Gids voor beveiligings aanbevelingen](../security-center/recommendations-reference.md)

**Verantwoordelijkheid**: Gedeeld

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

Meer informatie vindt u op de koppelingen waarnaar wordt verwezen.

- [Aanvullende Azure-abonnementen maken](../cost-management-billing/manage/create-subscription.md)

- [Beheergroepen maken](../governance/management-groups/create-management-group-portal.md)

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: de inventaris van goedgekeurde Azure-resources definiëren en onderhouden

**Hulp**: Definieer goedgekeurde Azure-resources en goedgekeurde software voor reken resources.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: monitor voor niet-goedgekeurde Azure-resources

**Hulp**: gebruik Azure Policy om beperkingen toe te voegen voor het type resources dat kan worden gemaakt in uw abonnement (en). 

Gebruik Azure resource Graph voor het opvragen/detecteren van resources binnen hun abonnement (en).  Zorg ervoor dat alle Azure-resources die aanwezig zijn in de omgeving, zijn goedgekeurd. 

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md) 

- [Query's maken met Azure Graph](../governance/resource-graph/first-query-portal.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="69-use-only-approved-azure-services"></a>6,9: alleen goedgekeurde Azure-Services gebruiken

**Hulp: gebruik** Azure Policy om beperkingen toe te voegen voor het type resources dat kan worden gemaakt in klant abonnement (en) met de volgende ingebouwde beleids definities:

- Niet toegestane resourcetypen

- Toegestane brontypen

Meer informatie vindt u op de koppelingen waarnaar wordt verwezen.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Een specifiek resource type weigeren met Azure Policy](../governance/policy/samples/built-in-policies.md#general)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: de mogelijkheid van gebruikers om te communiceren met Azure Resource Manager beperken

**Hulp** bij het configureren van voorwaardelijke toegang van Azure om gebruikers de mogelijkheid te bieden om te communiceren met Azure Resource Manager door ' blok toegang ' te configureren voor de app Microsoft Azure management.

- [Voorwaardelijke toegang configureren om de toegang tot Azure Resource Manager te blok keren](../role-based-access-control/conditional-access-azure-management.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="secure-configuration"></a>Veilige configuratie

*Zie [Azure Security Bench Mark: Secure Configuration](../security/benchmarks/security-control-secure-configuration.md)(Engelstalig) voor meer informatie.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: veilige configuraties instellen voor alle Azure-resources

**Richt lijnen**: standaard beveiligings configuraties voor uw functie-app definiëren en implementeren met Azure Policy. Gebruik Azure Policy aliassen in de naam ruimte ' micro soft. Web ' om aangepaste beleids regels te maken om de configuratie van uw functions-apps te controleren of af te dwingen. U kunt ook gebruik maken van ingebouwde beleids definities zoals:

- Er moet een beheerde identiteit worden gebruikt in uw functie-app

- Fout opsporing op afstand moet worden uitgeschakeld voor functie-apps

- de functie-app mag alleen toegankelijk zijn via HTTPS

Meer informatie vindt u op de koppelingen waarnaar wordt verwezen.

- [Beschik bare Azure Policy aliassen weer geven](/powershell/module/az.resources/get-azpolicyalias?preserve-view=true&view=azps-4.8.0)

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: Beveilig Azure-resource configuraties onderhouden

**Richt lijnen**: gebruik Azure Policy [deny] en [implementeren indien niet aanwezig] voor het afdwingen van beveiligde instellingen voor uw Azure-resources.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy effecten begrijpen](../governance/policy/concepts/effects.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: de configuratie van Azure-resources veilig opslaan

**Hulp**: arm-sjablonen en aangepaste Azure-beleids definities veilig opslaan en beheren in broncode beheer.

- [Wat is een infra structuur als code](/azure/devops/learn/what-is-infrastructure-as-code)

- [Ontwerp beleid als code werk stromen](../governance/policy/concepts/policy-as-code.md)

- [Code opslaan in azure DevOps](/azure/devops/repos/git/gitworkflow?preserve-view=true&view=azure-devops)

- [Documentatie voor Azure opslag plaatsen](/azure/devops/repos/?preserve-view=true&view=azure-devops)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: hulpprogram ma's voor configuratie beheer voor Azure-resources implementeren

**Hulp**: gebruik ingebouwde Azure Policy definities en Azure Policy aliassen in de naam ruimte ' micro soft. Web ' om aangepaste beleids regels te maken om systeem configuraties te Signa lering, te controleren en af te dwingen. Ontwikkel bovendien een proces en pijp lijn voor het beheren van beleids uitzonderingen.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: geautomatiseerde configuratie bewaking voor Azure-resources implementeren

**Hulp**: gebruik ingebouwde Azure Policy definities en Azure Policy aliassen in de naam ruimte ' micro soft. Web ' om aangepaste beleids regels te maken om systeem configuraties te Signa lering, te controleren en af te dwingen. Gebruik Azure Policy [audit], [deny] en [implementeren indien niet aanwezig] om automatisch configuraties af te dwingen voor uw Azure-resources.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="711-manage-azure-secrets-securely"></a>7,11: Azure-geheimen veilig beheren

**Richt lijnen**: gebruik beheerde identiteiten in combi natie met Azure Key Vault om geheim beheer voor uw Cloud toepassingen te vereenvoudigen en te beveiligen. Met beheerde identiteiten kan uw functie-app worden geverifieerd bij elke service die ondersteuning biedt voor Azure Active Directory-verificatie (Azure AD), met inbegrip van Key Vault, zonder enige referenties in uw code.

- [Een Key Vault maken](../key-vault/secrets/quick-create-portal.md)

- [Beheerde identiteiten gebruiken voor App Service en Azure Functions](../app-service/overview-managed-identity.md)

- [Verifiëren bij Key Vault](../key-vault/general/authentication.md)

- [Toegangs beleid voor Key Vault toewijzen](../key-vault/general/assign-access-policy-portal.md)

- [Gebruik Key Vault verwijzingen voor App Service en Azure Functions](../app-service/app-service-key-vault-references.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: identiteiten veilig en automatisch beheren

**Richt lijnen**: gebruik beheerde identiteiten om uw functie-app te voorzien van een automatisch beheerde identiteit in azure Active Directory (Azure AD). Met beheerde identiteiten kunt u zich verifiëren bij elke service die ondersteuning biedt voor Azure AD-verificatie, met inbegrip van Key Vault, zonder enige referenties in uw code.

- [Beheerde identiteiten gebruiken voor App Service en Azure Functions](../app-service/overview-managed-identity.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: de [Security Bench Mark van Azure](/azure/governance/policy/samples/azure-security-benchmark) is het standaard beleids initiatief voor Security Center en is de basis voor de [aanbevelingen van Security Center](/azure/security-center/security-center-recommendations). De Azure Policy definities die aan dit besturings element zijn gerelateerd, worden automatisch door Security Center ingeschakeld. Voor waarschuwingen met betrekking tot dit besturings element is mogelijk een [Azure Defender](/azure/security-center/azure-defender) -plan vereist voor de gerelateerde services.

**Ingebouwde definities Azure Policy-micro soft. Web**:

[!INCLUDE [Resource Policy for Microsoft.Web 7.12](../../includes/policy/standards/asb/rp-controls/microsoft.web-7-12.md)]

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: onbedoelde referentie blootstelling elimineren

**Richt lijnen**: referentie scanner implementeren om referenties in code te identificeren. Door het gebruik van Credential Scanner worden gebruikers ook aangemoedigd om gedetecteerde referenties naar veiligere locaties, zoals Azure Key Vault, te verplaatsen. 

- [Referentie scanner instellen](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="data-recovery"></a>Gegevensherstel

*Zie [Azure Security Bench Mark: Data Recovery](../security/benchmarks/security-control-data-recovery.md)(Engelstalig) voor meer informatie.*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: zorg voor regel matige automatische back-ups

**Hulp**: gebruik de functie voor het maken en terugzetten van back-ups om regel matige back-ups van uw app te plannen. Functie-apps die worden uitgevoerd in het Premium-abonnement hebben dezelfde hosting mogelijkheden als web-apps in Azure App Service, die de functie back-up en herstellen bevatten.

Maak ook gebruik van een broncode beheer oplossing zoals Azure opslag plaatsen en Azure DevOps om uw code veilig op te slaan en te beheren. Azure DevOps Services maakt gebruik van veel van de functies van Azure Storage om te zorgen voor de beschikbaarheid van gegevens in het geval van hardwarestoringen, serviceonderbrekingen of een regionale noodsituatie. Daarnaast volgt het Azure DevOps-team procedures voor het beveiligen van gegevens tegen onbedoelde verwijdering of verwijdering met kwaadaardige opzet.

- [Back-up maken van uw app in Azure](../app-service/manage-backup.md)

- [Inzicht in de beschik baarheid van gegevens in azure DevOps](/azure/devops/organizations/security/data-protection?preserve-view=true&view=azure-devops#data-availability)

- [Code opslaan in azure DevOps](/azure/devops/repos/git/gitworkflow?preserve-view=true&view=azure-devops)

- [Documentatie voor Azure opslag plaatsen](/azure/devops/repos/?preserve-view=true&view=azure-devops)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: volledige back-ups van het systeem uitvoeren en een back-up maken van door de klant beheerde sleutels

**Hulp**: gebruik de functie voor het maken en terugzetten van back-ups om regel matige back-ups van uw app te plannen. Functie-apps die worden uitgevoerd in het Premium-abonnement hebben dezelfde hosting mogelijkheden als web-apps in Azure App Service, die de functie back-up en herstellen bevatten. Back-ups van door de klant beheerde sleutels binnen Azure Key Vault.

Maak ook gebruik van een broncode beheer oplossing zoals Azure opslag plaatsen en Azure DevOps om uw code veilig op te slaan en te beheren. Azure DevOps Services maakt gebruik van veel van de functies van Azure Storage om te zorgen voor de beschikbaarheid van gegevens in het geval van hardwarestoringen, serviceonderbrekingen of een regionale noodsituatie. Daarnaast volgt het Azure DevOps-team procedures voor het beveiligen van gegevens tegen onbedoelde verwijdering of verwijdering met kwaadaardige opzet.

- [Back-up maken van uw app in Azure](../app-service/manage-backup.md)

- [Hoe kan ik een back-up maken van sleutel kluis sleutels in azure?](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

- [Inzicht in de beschik baarheid van gegevens in azure DevOps](/azure/devops/organizations/security/data-protection?preserve-view=true&view=azure-devops#data-availability)

- [Code opslaan in azure DevOps](/azure/devops/repos/git/gitworkflow?preserve-view=true&view=azure-devops)

- [Documentatie voor Azure opslag plaatsen](/azure/devops/repos/?preserve-view=true&view=azure-devops)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: alle back-ups valideren, inclusief door de klant beheerde sleutels

**Richt lijnen**: Zorg ervoor dat u de functie voor het maken en terugzetten van back-ups periodiek kunt herstellen. Als u een andere offline locatie gebruikt om een back-up te maken van uw code, zorgt u er dan voor dat u de volledige herstel bewerkingen uitvoert. Het herstellen van een back-up van door de klant beheerde sleutels testen.

- [Een app in azure herstellen vanuit een back-up](../app-service/web-sites-restore.md)

- [Een app in azure herstellen vanuit een moment opname](../app-service/app-service-web-restore-snapshots.md)

- [Sleutel kluis sleutels herstellen in azure](/powershell/module/az.keyvault/restore-azkeyvaultkey?preserve-view=true&view=azps-4.8.0)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: zorg voor de bescherming van back-ups en door de klant beheerde sleutels

**Richt lijnen**: back-ups van de functie back-up en herstellen gebruiken een Azure Storage-account in uw abonnement. Azure Storage versleutelt alle gegevens in een opslag account in rust. Standaard worden gegevens versleuteld met door micro soft beheerde sleutels. Voor extra controle over versleutelings sleutels kunt u door de klant beheerde sleutels voor versleuteling van opslag gegevens leveren.

Als u door de klant beheerde sleutels gebruikt, zorgt u ervoor dat Soft-Delete in Key Vault is ingeschakeld voor het beveiligen van sleutels tegen onbedoelde of schadelijke verwijdering.

- [Versleuteling-at-rest in Azure Storage](../storage/common/storage-service-encryption.md)

- [Soft-Delete in Key Vault inschakelen](../storage/blobs/soft-delete-blob-overview.md)

**Verantwoordelijkheid**: Gedeeld

**Azure Security Center bewaking**: geen

## <a name="incident-response"></a>Reageren op incidenten

*Zie [Azure Security Benchmark: respons op incidenten](../security/benchmarks/security-control-incident-response.md) voor meer informatie.*

### <a name="101-create-an-incident-response-guide"></a>10,1: een hand leiding voor reactie op incidenten maken

**Richtlijnen**: Stel voor uw organisatie een responshandleiding op voor gebruik bij incidenten. Zorg ervoor dat er schriftelijke responsplannen zijn waarin alle rollen van het personeel worden gedefinieerd, evenals alle fasen in het afhandelen/managen van incidenten, vanaf de detectie van het incident tot een evaluatie ervan achteraf.

- [Werk stroom automatisering configureren in Azure Security Center](../security-center/security-center-planning-and-operations-guide.md)

- [Richt lijnen voor het bouwen van uw eigen beveiligings incident antwoord proces](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Micro soft Security Response Center anatomie van een incident](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Hand leiding voor het verwerken van beveiligings incidenten van het NIST](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: een beoordelings procedure voor incidenten en prioriteits procedures maken

**Hulp**: Security Center wijst aan elke waarschuwing een Ernst toe om u te helpen bepalen welke waarschuwingen het eerst moeten worden onderzocht. De ernst is gebaseerd op de manier waarop vertrouwen Security Center is in de zoek actie of het analyse programma dat wordt gebruikt om de waarschuwing te geven, evenals het betrouwbaarheids niveau dat er schadelijke bedoelingen zijn achter de activiteit die tot de waarschuwing heeft geleid.

Daarnaast kunt u ook duidelijk abonnementen markeren (voor bijvoorbeeld productie, niet-productie) en maak een naamgevings systeem om Azure-resources duidelijk te identificeren en te categoriseren.

**Verantwoordelijkheid**: Gedeeld

**Azure Security Center bewaking**: geen

### <a name="103-test-security-response-procedures"></a>10,3: procedures voor beveiligings antwoorden testen

**Richt lijnen**: oefent oefeningen uit om de respons mogelijkheden van uw systeem op een gewone uitgebracht te testen. Stel vast waar zich zwakke plekken en hiaten bevinden, en wijzig zo nodig het plan.

- [Raadpleeg de publicatie van het NIST: hand leiding voor het testen, trainen en uitoefenen van Program Ma's voor IT-plannen en-mogelijkheden](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: contact gegevens van het beveiligings incident opgeven en waarschuwings meldingen configureren voor beveiligings incidenten

**Hulp**: contact gegevens van beveiligings incidenten worden door micro soft gebruikt om contact met u op te nemen als het micro soft Security Response Center (MSRC) detecteert dat de gegevens van de klant zijn geopend door een onrecht matige of niet-gemachtigde partij.  Bekijk incidenten na het feit om te controleren of de problemen zijn opgelost.

- [De Azure Security Center Security-contact persoon instellen](../security-center/security-center-provide-security-contact-details.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: beveiligings waarschuwingen opnemen in uw reactie systeem van uw incident

**Richt lijnen**: uw Azure Security Center waarschuwingen en aanbevelingen exporteren met de functie continue export. Met doorlopend exporteren kunt u waarschuwingen en aanbevelingen hand matig of op een doorlopende manier exporteren. U kunt de Azure Security Center Data Connector gebruiken om de waarschuwingen naar Azure Sentinel te streamen.

- [Continue export configureren](../security-center/continuous-export.md)

- [Waarschuwingen streamen naar Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: de reactie op beveiligings waarschuwingen automatiseren

**Hulp**: gebruik de functie werk stroom automatisering in azure Security Center om automatisch reacties te activeren op beveiligings waarschuwingen en aanbevelingen met Logic apps.

- [Werk stroom automatisering en Logic Apps configureren](../security-center/workflow-automation.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="penetration-tests-and-red-team-exercises"></a>Penetratietests en Red Team-oefeningen

*Zie [Azure Security Bench Mark: Indringings tests en rode team oefeningen](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)voor meer informatie.*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: voert regel matig indringings tests van uw Azure-resources uit en zorgt voor herbemiddeling van alle essentiële beveiligings resultaten

**Richt lijnen**: Volg de micro soft-regels om ervoor te zorgen dat de indringings tests niet worden geschonden door het micro soft-beleid. Gebruik de strategie van micro soft en de uitvoering van de implementatie van de indringing van een live site in de Cloud, services en toepassingen die door micro soft worden beheerd.

- [Regels voor het inzetten van penetratietests](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Verantwoordelijkheid**: Gedeeld

**Azure Security Center bewaking**: geen

## <a name="next-steps"></a>Volgende stappen

- Zie [Overzicht Azure Security Benchmark V2](../security/benchmarks/overview.md)
- Meer informatie over [Azure-beveiligingsbasislijnen](../security/benchmarks/security-baselines-overview.md)
