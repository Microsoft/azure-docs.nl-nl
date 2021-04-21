---
title: Azure-beveiligingsbasislijn voor Azure Functions
description: De Azure Functions beveiligingsbasislijn biedt procedurele richtlijnen en resources voor het implementeren van de beveiligingsaanbevelingen die zijn opgegeven in de Azure Security-benchmark.
author: msmbaldwin
ms.service: azure-functions
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark, devx-track-azurepowershell
ms.openlocfilehash: 90ae774a4e243b854758c1c7a6cc10a882cd584f
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107831074"
---
# <a name="azure-security-baseline-for-azure-functions"></a>Azure-beveiligingsbasislijn voor Azure Functions

Met deze beveiligingsbasislijn worden richtlijnen van [Azure Security Benchmark versie 1.0](../security/benchmarks/overview-v1.md) toegepast op Azure Functions. De Azure Security-benchmark biedt aanbevelingen voor hoe u uw cloudoplossingen in Azure kunt beveiligen.
De inhoud wordt gegroepeerd op basis van de **beveiligingscontroles** die zijn gedefinieerd door de Azure Security Benchmark en de gerelateerde richtlijnen die van toepassing zijn op Azure Functions. **Besturingselementen** die niet van Azure Functions zijn uitgesloten.

 
Zie het volledige Azure Functions toewijzingsbestand voor beveiligingsbasislijnen om te zien hoe Azure Functions volledig is toegewezen aan de Azure Security [Azure Functions-benchmark.](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)

## <a name="network-security"></a>Netwerkbeveiliging

*Zie [Azure Security Benchmark: netwerkbeveiliging](../security/benchmarks/security-control-network-security.md) voor meer informatie.*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Azure-resources binnen virtuele netwerken beveiligen

**Richtlijnen:** integreer uw Azure Functions-apps met een virtueel Azure-netwerk. Functie-apps die worden uitgevoerd in het Premium-abonnement hebben dezelfde hostingmogelijkheden als web-apps in Azure App Service, waaronder de functie VNet-integratie.  Met virtuele Netwerken van Azure kunt u veel van uw Azure-resources, zoals Azure Functions, in een routeerbaar netwerk dat niet via internet kan worden gebruikt.

- [Functions integreren met een Azure-Virtual Network](functions-create-vnet.md)

- [Vnet-integratie voor Azure Functions en Azure App Service](../app-service/web-sites-integrate-with-vnet.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** De [Azure Security-benchmark](/azure/governance/policy/samples/azure-security-benchmark) is het standaardbeleidsinitiatief voor Security Center en is de basis voor [Security Center aanbevelingen van Security Center.](/azure/security-center/security-center-recommendations) De Azure Policy met betrekking tot dit besturingselement worden automatisch ingeschakeld door Security Center. Waarschuwingen met betrekking tot dit besturingselement vereisen mogelijk een [Azure Defender](/azure/security-center/azure-defender) voor de gerelateerde services.

**Azure Policy ingebouwde definities - Microsoft.Web:**

[!INCLUDE [Resource Policy for Microsoft.Web 1.1](../../includes/policy/standards/asb/rp-controls/microsoft.web-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2: De configuratie en het verkeer van virtuele netwerken, subnetten en netwerkinterfaces bewaken en logboeken

**Richtlijnen:** gebruik Azure Security Center en volg aanbevelingen voor netwerkbeveiliging om netwerkbronnen en netwerkconfiguraties met betrekking tot uw Azure Functions beveiligen.

Als u netwerkbeveiligingsgroepen gebruikt met uw Azure Functions-implementatie, moet u stroomlogboeken van netwerkbeveiligingsgroepen inschakelen en logboeken verzenden naar een Azure Storage-account voor verkeerscontroles. U kunt ook stroomlogboeken van netwerkbeveiligingsgroepen verzenden naar een Log Analytics-werkruimte en Traffic Analytics om inzicht te krijgen in de verkeersstroom in uw Azure-cloud. Enkele voordelen van Traffic Analytics zijn de mogelijkheid om netwerkactiviteit te visualiseren en hotspots te identificeren, beveiligingsrisico's te identificeren, verkeersstroompatronen te begrijpen en onjuiste netwerkconfiguraties aan te wijzen.

- [Informatie over netwerkbeveiliging die wordt geleverd door Azure Security Center](../security-center/security-center-network-recommendations.md)

- [NSG-stroomlogboeken inschakelen](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Informatie over het inschakelen en gebruiken van Traffic Analytics](../network-watcher/traffic-analytics.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="13-protect-critical-web-applications"></a>1.3: Kritieke webtoepassingen beveiligen

**Richtlijnen:** als u uw eindpunten Azure Functions in productie wilt beveiligen, kunt u overwegen een van de volgende beveiligingsopties op app-niveau te implementeren:

- Verificatie of App Service functie-app inschakelen

- Azure API Management (APIM) gebruiken om aanvragen te verifiëren

- Implementeer uw functie-app in een Azure App Service Environment.

Zorg er bovendien voor dat externe debugging is uitgeschakeld voor uw productie-Azure Functions. Bovendien mag Cross-Origin Resource Sharing (CORS) niet toestaan dat alle domeinen toegang hebben tot uw functie-app in Azure. Sta alleen de vereiste domeinen toe om met uw functie-app te communiceren.

Overweeg de implementatie Azure Web Application Firewall (WAF) als onderdeel van de netwerkconfiguratie voor aanvullende inspectie van binnenkomend verkeer. Schakel diagnostische instelling voor WAF in en opname van logboeken in een opslagaccount, Event Hub of Log Analytics-werkruimte. 

- [Eindpunten Azure Functions in productie beveiligen](./functions-bindings-http-webhook-trigger.md?tabs=csharp#secure-an-http-endpoint-in-production)

- [Azure WAF implementeren](../web-application-firewall/ag/create-waf-policy-ag.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** De [Azure Security-benchmark](/azure/governance/policy/samples/azure-security-benchmark) is het standaardbeleidsinitiatief voor Security Center en is de basis voor [Security Center aanbevelingen van Security Center.](/azure/security-center/security-center-recommendations) De Azure Policy met betrekking tot dit besturingselement worden automatisch ingeschakeld door Security Center. Waarschuwingen met betrekking tot dit besturingselement vereisen mogelijk een [Azure Defender](/azure/security-center/azure-defender) voor de gerelateerde services.

**Azure Policy ingebouwde definities - Microsoft.Web:**

[!INCLUDE [Resource Policy for Microsoft.Web 1.3](../../includes/policy/standards/asb/rp-controls/microsoft.web-1-3.md)]

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Communicatie met bekende schadelijke IP-adressen weigeren

**Richtlijnen:** schakel DDoS Protection Standard in op de virtuele netwerken die zijn gekoppeld aan uw functie-apps om bescherming te bieden tegen DDoS-aanvallen. Gebruik bedreigingsinformatiefuncties in Azure Security Center geïntegreerd om communicatie met bekende schadelijke of ongebruikte openbare IP-adressen te weigeren.

Configureer bovendien een front-endgateway, zoals Azure Web Application Firewall, om alle binnenkomende aanvragen te verifiëren en schadelijk verkeer weg te filteren. Azure Web Application Firewall kunt u uw functie-app beveiligen door het inkomende webverkeer te inspecteren om SQL-injecties, cross-site scripting, uploads van malware en DDoS-aanvallen te blokkeren. De introductie van Web Application Firewall vereist een App Service Environment of het gebruik van privé-eindpunten. U kunt privé-eindpunten gebruiken voor uw functies die worden gehost in de Premium- en App Service-abonnementen.

- [Netwerkopties van Azure Functions](functions-networking-options.md)

- [DDoS-beveiliging configureren](../ddos-protection/manage-ddos-protection.md)

- [Inzicht Azure Security Center geïntegreerde bedreigingsinformatie](../security-center/azure-defender.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="15-record-network-packets"></a>1.5: Netwerkpakketten registreren

**Richtlijnen:** als u een netwerkbeveiligingsgroep gebruikt met uw Azure Functions-implementatie, moet u stroomlogboeken van netwerkbeveiligingsgroep inschakelen en logboeken verzenden naar een opslagaccount voor verkeerscontrole. U kunt ook stroomlogboeken verzenden naar een Log Analytics-werkruimte en Traffic Analytics om inzicht te krijgen in de verkeersstroom in uw Azure-cloud. Enkele voordelen van Traffic Analytics zijn de mogelijkheid om netwerkactiviteit te visualiseren en hotspots te identificeren, beveiligingsrisico's te identificeren, verkeersstroompatronen te begrijpen en onjuiste netwerkconfiguraties aan te wijzen.

- [Stroomlogboeken voor netwerkbeveiligingsgroep inschakelen](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Informatie over het inschakelen en gebruiken van Traffic Analytics](../network-watcher/traffic-analytics.md)

- [Het inschakelen van Network Watcher](../network-watcher/network-watcher-create.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Op het netwerk gebaseerde inbraakdetectie-/inbraakpreventiesystemen (IDS/IPS) implementeren

**Richtlijnen:** Configureer een front-endgateway zoals Azure Web Application Firewall om alle binnenkomende aanvragen te verifiëren en schadelijk verkeer te filteren. Azure Web Application Firewall kunt u uw functie-apps beveiligen door inkomende webverkeer te inspecteren om SQL-injecties, cross-site scripting, uploads van malware en DDoS-aanvallen te blokkeren. Voor de introductie van Web Application Firewall is een App Service Environment of het gebruik van privé-eindpunten vereist. U kunt privé-eindpunten gebruiken voor uw functies die worden gehost in de Premium- en App Service-abonnementen.

Er zijn ook meerdere marketplace-opties, zoals de Barracuda Web Application Firewall voor Azure, beschikbaar op de Azure Marketplace die functies voor inbraakdetectie of -preventie bevatten.

- [Netwerkopties van Azure Functions](functions-networking-options.md)

- [Azure Functions Premium-abonnement](functions-premium-plan.md)

- [Inleiding tot Azure App Service Environments](../app-service/environment/intro.md)

- [Netwerkoverwegingen voor een App Service-omgeving](../app-service/environment/network-info.md) 

- [Meer Azure Web Application Firewall](../web-application-firewall/overview.md)

- [Privé-eindpunten gebruiken voor Azure Functions](../app-service/networking/private-endpoint.md)

- [Barracuda WAF-cloudservice begrijpen](../app-service/environment/app-service-app-service-environment-web-application-firewall.md#configuring-your-barracuda-waf-cloud-service)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Verkeer naar webtoepassingen beheren

**Richtlijnen:** Configureer een front-endgateway voor uw netwerk, Azure Web Application Firewall end-to-end TLS-versleuteling. De introductie van Web Application Firewall vereist een App Service Environment of het gebruik van privé-eindpunten. U kunt privé-eindpunten gebruiken voor uw functies die worden gehost in de Premium- en App Service-abonnementen.

- [Netwerkopties van Azure Functions](functions-networking-options.md)

- [Azure Functions Premium-abonnement](functions-premium-plan.md)

- [Inleiding tot Azure App Service Environments](../app-service/environment/intro.md)

- [Netwerkoverwegingen voor een App Service-omgeving](../app-service/environment/network-info.md) 

- [Meer Azure Web Application Firewall](../web-application-firewall/overview.md)

- [End-to-end-TLS configureren met behulp van Application Gateway met de portal](../application-gateway/end-to-end-ssl-portal.md)

- [Privé-eindpunten gebruiken voor Azure Functions](../app-service/networking/private-endpoint.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Complexiteit en administratieve overhead van netwerkbeveiligingsregels minimaliseren

**Richtlijnen:** gebruik Virtual Network-servicetags voor het definiëren van besturingselementen voor netwerktoegang in een netwerkbeveiligingsgroep of Azure Firewall. U kunt servicetags gebruiken in plaats van specifieke IP-adressen wanneer u beveiligingsregels maakt. Door de naam van de servicetag (bijvoorbeeld AzureAppService) op te geven in het juiste bron- of doelveld van een regel, kunt u het verkeer voor de bijbehorende service toestaan of weigeren. Microsoft beheert de adres-voorvoegsels die door de servicetag worden omvat en werkt de servicetag automatisch bij wanneer adressen worden gewijzigd.

- [Voor meer informatie over het gebruik van servicetags](../virtual-network/service-tags-overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Standaardbeveiligingsconfiguraties voor netwerkapparaten onderhouden

**Richtlijnen:** standaardbeveiligingsconfiguraties definiëren en implementeren voor netwerkinstellingen die betrekking hebben op uw Azure Functions. Gebruik Azure Policy aliassen in de naamruimten Microsoft.Web en Microsoft.Network om aangepaste beleidsregels te maken voor het controleren of afdwingen van de netwerkconfiguratie van uw Azure Functions. U kunt ook gebruikmaken van ingebouwde beleidsdefinities voor Azure Functions, zoals:

- CORS mag niet toestaan dat elke resource toegang heeft tot uw functie-apps

- Functie-app mag alleen toegankelijk zijn via HTTPS

- De nieuwste TLS-versie moet worden gebruikt in uw functie-app

U kunt Azure Blueprints ook gebruiken om grootschalige Azure-implementaties te vereenvoudigen door belangrijke omgevingsartefacten te verpakken, zoals Azure Resource Manager-sjablonen, op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC) en beleidsregels in één blauwdrukdefinitie. U kunt de blauwdruk eenvoudig toepassen op nieuwe abonnementen, omgevingen en beheer afstemmen via versiebeheer.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Een Azure Blueprint maken](../governance/blueprints/create-blueprint-portal.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="110-document-traffic-configuration-rules"></a>1.10: Configuratieregels voor verkeer documenteren

**Richtlijnen:** als u een netwerkbeveiligingsgroep gebruikt Azure Functions implementatie, gebruikt u tags voor de NSG's en andere resources met betrekking tot netwerkbeveiliging en verkeersstroom. Gebruik voor afzonderlijke regels voor netwerkbeveiligingsgroep het veld Beschrijving om bedrijfseen/of duur op te geven, voor regels die verkeer van/naar een netwerk toestaan.

Gebruik een van de ingebouwde Azure-beleidsdefinities met betrekking tot taggen, zoals Tag en waarde vereisen, om ervoor te zorgen dat alle resources worden gemaakt met tags en om u op de hoogte te stellen van bestaande resources zonder tag.

U kunt Azure PowerShell of Azure CLI gebruiken om resources op te zoeken of acties uit te voeren op basis van hun tags.

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Geautomatiseerde hulpprogramma's gebruiken om netwerkresourceconfiguraties te bewaken en wijzigingen te detecteren

**Richtlijnen:** Gebruik azure-activiteitenlogboek om configuraties van netwerkresources te bewaken en wijzigingen te detecteren voor netwerkinstellingen en resources met betrekking tot uw Azure Functions implementaties. Maak waarschuwingen binnen Azure Monitor die worden getriggerd wanneer er wijzigingen in kritieke netwerkinstellingen of resources plaatsvinden. 

- [Azure-activiteitenlogboekgebeurtenissen weergeven en ophalen](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Waarschuwingen maken in Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="logging-and-monitoring"></a>Logboekregistratie en bewaking

*Zie de Azure [Security Benchmark: Logging and Monitoring (Azure Security Benchmark: logboekregistratie en bewaking) voor meer informatie.](../security/benchmarks/security-control-logging-monitoring.md)*

### <a name="22-configure-central-security-log-management"></a>2.2: Centraal beheer van beveiligingslogboek configureren

**Richtlijnen:** schakel diagnostische instellingen voor Azure-activiteitenlogboeken in voor controlelogboeken en verzend de logboeken naar een Log Analytics-werkruimte, Azure Event Hub of Azure-opslagaccount voor archivering. Met behulp van azure-activiteitenlogboekgegevens kunt u bepalen wat, wie en wanneer voor schrijfbewerkingen (PUT, POST, DELETE) die worden uitgevoerd op het niveau van het besturingsvlak voor uw Azure-resources.

Azure Functions biedt ook ingebouwde integratie met Azure-toepassing Insights om functies te bewaken. Application Insights verzamelt logboek-, prestatie- en foutgegevens. Het detecteert automatisch prestatieafwijkingen en bevat krachtige analysehulpprogramma's om u te helpen bij het vaststellen van problemen en om te begrijpen hoe uw functies worden gebruikt.

Als u ingebouwde aangepaste logboekregistratie voor beveiliging/controle in uw functie-app hebt, moet u de diagnostische instelling FunctionAppLogs inschakelen en de logboeken verzenden naar een Log Analytics-werkruimte, Azure Event Hub of Azure-opslagaccount voor archivering. 

U kunt eventueel gegevens inschakelen en in gebruik nemen voor Azure Sentinel of een oplossing voor systeeminformatie en gebeurtenisbeheer van derden. 

- [Diagnostische instellingen inschakelen voor Azure-activiteitenlogboek](../azure-monitor/essentials/activity-log.md)

- [Het instellen van Azure Functions met Azure-toepassing Insights](functions-monitoring.md)

- [Diagnostische instellingen (door gebruikers gegenereerde logboeken) inschakelen voor Azure Functions](functions-monitor-log-analytics.md)

- [Onboarding van Azure Sentinel](../sentinel/quickstart-onboard.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Auditlogregistratie voor Azure-resources inschakelen

**Richtlijnen:** schakel diagnostische instellingen voor azure-activiteitenlogboeken in voor controlelogboeken van besturingsvlak en verzend de logboeken naar een Log Analytics-werkruimte, Azure Event Hub of Azure-opslagaccount voor archivering. Met behulp van azure-activiteitenlogboekgegevens kunt u bepalen wat, wie en wanneer voor schrijfbewerkingen (PUT, POST, DELETE) die worden uitgevoerd op het niveau van het besturingsvlak voor uw Azure-resources.

Als u ingebouwde aangepaste logboekregistratie voor beveiliging/controle in uw functie-app hebt, moet u de diagnostische instelling FunctionAppLogs inschakelen en de logboeken verzenden naar een Log Analytics-werkruimte, Azure Event Hub of Azure-opslagaccount voor archivering. 

- [Diagnostische instellingen inschakelen voor Azure-activiteitenlogboek](../azure-monitor/essentials/activity-log.md)

- [Diagnostische instellingen (door gebruikers gegenereerde logboeken) inschakelen voor Azure Functions](functions-monitor-log-analytics.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** De [Azure Security-benchmark](/azure/governance/policy/samples/azure-security-benchmark) is het standaardbeleidsinitiatief voor Security Center en is de basis voor [Security Center aanbevelingen van Security Center.](/azure/security-center/security-center-recommendations) De Azure Policy met betrekking tot dit besturingselement worden automatisch ingeschakeld door Security Center. Voor waarschuwingen met betrekking tot dit besturingselement is mogelijk een [Azure Defender](/azure/security-center/azure-defender) nodig voor de gerelateerde services.

**Azure Policy ingebouwde definities - Microsoft.Web**:

[!INCLUDE [Resource Policy for Microsoft.Web 2.3](../../includes/policy/standards/asb/rp-controls/microsoft.web-2-3.md)]

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Beveiligingslogboeken van besturingssystemen verzamelen

**Richtlijnen:** Niet van toepassing; deze richtlijn is bedoeld voor IaaS-rekenbronnen.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="25-configure-security-log-storage-retention"></a>2.5: Opslagretentie van beveiligingslogboek configureren

**Richtlijnen:** stel Azure Monitor logboekretentieperiode in voor Log Analytics-werkruimten die zijn gekoppeld aan uw functie-apps overeenkomstig de nalevingsvoorschriften van uw organisatie.

- [Retentieparameters voor logboeken instellen](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="26-monitor-and-review-logs"></a>2.6: Logboeken bewaken en controleren

**Richtlijnen:** Schakel diagnostische instellingen voor Azure-activiteitenlogboeken en de diagnostische instellingen voor uw functie-app in en verzend de logboeken naar een Log Analytics-werkruimte. Voer query's uit in Log Analytics om termen te zoeken, trends te identificeren, patronen te analyseren en veel andere inzichten te bieden op basis van de verzamelde gegevens.

Schakel Application Insights functie-apps in om logboek-, prestatie- en foutgegevens te verzamelen. U kunt de telemetriegegevens bekijken die door de Application Insights in de Azure Portal.

Als u ingebouwde aangepaste logboekregistratie voor beveiliging/controle in uw functie-app hebt, moet u de diagnostische instelling FunctionAppLogs inschakelen en de logboeken verzenden naar een Log Analytics-werkruimte, Azure Event Hub of Azure-opslagaccount voor archivering. 

Optioneel kunt u gegevens inschakelen en in gebruik nemen voor Azure Sentinel of een oplossing voor systeeminformatie en gebeurtenisbeheer van derden.

- [Diagnostische instellingen inschakelen voor Azure-activiteitenlogboek](../azure-monitor/essentials/activity-log.md)

- [Diagnostische instellingen inschakelen voor Azure Functions](functions-monitor-log-analytics.md)

- [Hoe u gegevens in Azure Functions met Azure-toepassing Insights en de telemetriegegevens bekijkt](functions-monitoring.md)

- [Onboarding van Azure Sentinel](../sentinel/quickstart-onboard.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Waarschuwingen inschakelen voor afwijkende activiteiten

**Richtlijnen:** Schakel diagnostische instellingen voor Azure-activiteitenlogboeken in, evenals de diagnostische instellingen voor uw functie-app en verzend de logboeken naar een Log Analytics-werkruimte. Voer query's uit in Log Analytics om termen te zoeken, trends te identificeren, patronen te analyseren en veel andere inzichten te bieden op basis van de verzamelde gegevens. U kunt waarschuwingen maken op basis van uw Log Analytics-werkruimtequery's.

Schakel Application Insights functie-apps in om logboek-, prestatie- en foutgegevens te verzamelen. U kunt de telemetriegegevens bekijken die door de Application Insights en waarschuwingen maken binnen de Azure Portal.

U kunt eventueel gegevens inschakelen en in gebruik nemen voor Azure Sentinel of een oplossing voor systeeminformatie en gebeurtenisbeheer van derden.

- [Diagnostische instellingen inschakelen voor Azure-activiteitenlogboek](../azure-monitor/essentials/activity-log.md)

- [Diagnostische instellingen inschakelen voor Azure Functions](functions-monitor-log-analytics.md)

- [Een Application Insights inschakelen voor Azure Functions](./configure-monitoring.md#enable-application-insights-integration)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="identity-and-access-control"></a>Identiteits- en toegangsbeheer

*Zie de Azure [Security Benchmark: Identity and Access Control (Azure Security-benchmark: identiteit en Access Control).](../security/benchmarks/security-control-identity-access-control.md)*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Een inventaris van beheerdersaccounts onderhouden

**Richtlijnen:** Azure Active Directory (Azure AD) heeft ingebouwde rollen die expliciet moeten worden toegewezen en opvraagbaar zijn. Gebruik de Azure AD PowerShell-module om ad-hocquery's uit te voeren om accounts te ontdekken die lid zijn van beheergroepen.

- [Een directoryrol in Azure AD krijgen met PowerShell](/powershell/module/azuread/get-azureaddirectoryrole?preserve-view=true&view=azureadps-2.0)

- [Leden van een directoryrol in Azure AD krijgen met PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember?preserve-view=true&view=azureadps-2.0)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Standaardwachtwoorden wijzigen, indien van toepassing

**Richtlijnen:** De toegang tot de Azure Functions wordt beheerd via Azure Active Directory (Azure AD). Azure AD heeft niet het concept van standaardwachtwoorden.

Toegang tot de gegevensvlak kan op verschillende manieren worden beheerd, waaronder autorisatiesleutels, netwerkbeperkingen en validatie van een Azure AD-identiteit. Autorisatiesleutels worden gebruikt door de clients die verbinding maken met Azure Functions HTTP-eindpunten en kunnen op elk moment opnieuw worden ge regenereerd. Deze sleutels worden standaard gegenereerd voor nieuwe HTTP-eindpunten.

Er zijn meerdere implementatiemethoden beschikbaar voor functie-apps, waarvan sommige gebruikmaken van een set gegenereerde referenties. Controleer de implementatiemethoden die voor uw toepassing worden gebruikt.

- [Een HTTP-eindpunt beveiligen](./functions-bindings-http-webhook-trigger.md?tabs=csharp#secure-an-http-endpoint-in-production)

- [Autorisatiesleutels verkrijgen en opnieuw maken](./functions-bindings-http-webhook-trigger.md?tabs=csharp#obtaining-keys)

- [Implementatietechnologieën in Azure Functions](functions-deployment-technologies.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Toegewezen beheerdersaccounts gebruiken

**Richtlijnen:** standaardprocedures voor het gebruik van toegewezen beheerdersaccounts maken. Gebruik Azure Security Center identiteits- en toegangsbeheer om het aantal beheerdersaccounts te bewaken.

Daarnaast kunt u aanbevelingen van Azure Security Center of ingebouwde Azure-beleidsregels gebruiken om toegewezen beheerdersaccounts bij te houden, zoals:

- Er moet meer dan één eigenaar zijn toegewezen aan uw abonnement

- Afgeschafte accounts met eigenaarsmachtigingen moeten worden verwijderd uit uw abonnement

- Externe accounts met eigenaarsmachtigingen moeten worden verwijderd uit uw abonnement

Aanvullende informatie is beschikbaar via de koppelingen waarnaar wordt verwezen.

- [Identiteit en toegang Azure Security Center gebruiken ](../security-center/security-center-identity-access.md)

- [Hoe u Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4: Gebruik Azure Active Directory eenmalige aanmelding (SSO)

**Richtlijnen:** gebruik waar mogelijk eenmalige Azure Active Directory (Azure AD) in plaats van afzonderlijke, zelf-eigen referenties te configureren voor gegevenstoegang tot uw functie-app. Gebruik Azure Security Center aanbevelingen voor identiteits- en toegangsbeheer. Implementeert een aanmelding voor uw Functions-apps met behulp van App Service functie Verificatie/autorisatie.

- [Verificatie en autorisatie in Azure Functions](../app-service/overview-authentication-authorization.md#identity-providers)

- [Eenmalige aanmelding met Azure AD begrijpen](../active-directory/manage-apps/what-is-single-sign-on.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Meervoudige verificatie gebruiken voor alle Azure Active Directory op basis van toegang

**Richtlijnen:** meervoudige Azure Active Directory (Azure AD) inschakelen en Azure Security Center aanbevelingen voor identiteits- en toegangsbeheer volgen.

- [Meervoudige verificatie inschakelen in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Identiteit en toegang binnen uw Azure Security Center](../security-center/security-center-identity-access.md)

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

- [PIM (Privileged Identity Management) implementeren](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Inzicht in Azure AD-risicodetecties](../active-directory/identity-protection/overview-identity-protection.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Azure-resources alleen beheren vanaf goedgekeurde locaties

**Richtlijnen:** Gebruik benoemde locaties voor voorwaardelijke toegang om toegang tot de Azure Portal alleen toe te staan vanuit specifieke logische groeperingen van IP-adresbereiken of landen/regio's.

- [Benoemde locaties configureren in Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="39-use-azure-active-directory"></a>3.9: Gebruik Azure Active Directory

**Richtlijnen:** Gebruik Azure Active Directory (Azure AD) als het centrale verificatie- en autorisatiesysteem voor uw functie-apps. Azure AD beschermt gegevens met behulp van sterke versleuteling voor data-at-rest en in transit. Azure AD biedt ook salts, hashes en slaat veilig gebruikersreferenties op.

- [Uw functie-app configureren voor het gebruik van Azure AD-aanmelding](../app-service/configure-authentication-provider-aad.md)

- [Een Azure AD-instantie maken en configureren](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Regelmatig gebruikerstoegang controleren en afstemmen

**Richtlijnen:** Azure Active Directory (Azure AD) biedt logboeken om u te helpen verouderde accounts te ontdekken. Gebruik bovendien Azure Identity Access Reviews om groepslidmaatschap, toegang tot bedrijfstoepassingen en roltoewijzingen efficiënt te beheren. Gebruikerstoegang kan regelmatig worden gecontroleerd om ervoor te zorgen dat alleen de juiste gebruikers nog toegang hebben.

- [Inzicht in Azure AD-rapportage](../active-directory/reports-monitoring/index.yml)

- [Toegangsbeoordelingen voor Azure-identiteiten gebruiken](../active-directory/governance/access-reviews-overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Pogingen tot toegang tot gedeactiveerde referenties controleren

**Richtlijnen:** Gebruik Azure Active Directory (Azure AD) als het centrale verificatie- en autorisatiesysteem voor uw functie-apps. Azure AD beschermt gegevens met behulp van sterke versleuteling voor data-at-rest en in transit. Azure AD biedt ook salts, hashes en slaat veilig gebruikersreferenties op.

U hebt toegang tot bronnen van aanmeldingsactiviteiten, controle- en risicogebeurtenissen van Azure AD, waarmee u kunt integreren met Azure Sentinel of een SIEM van derden.

U kunt dit proces stroomlijnen door diagnostische instellingen te maken voor Azure AD-gebruikersaccounts en de auditlogboeken en aanmeldingslogboeken te verzenden naar een Log Analytics-werkruimte. U kunt gewenste logboekwaarschuwingen configureren in Log Analytics.

- [Uw functie-app configureren voor het gebruik van Azure AD-aanmelding](../app-service/configure-authentication-provider-aad.md)

- [Azure-activiteitenlogboeken integreren in Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Hoe kunt u de Azure Sentinel](../sentinel/quickstart-onboard.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: Afwijking van aanmeldingsgedrag van account

**Richtlijnen:** Gebruik Azure Active Directory (Azure AD) als het centrale verificatie- en autorisatiesysteem voor uw functie-apps. Gebruik voor de afwijking van het aanmeldingsgedrag van het account op het besturingsvlak (de Azure Portal) de functies Azure AD Identity Protection en risicodetectie om geautomatiseerde reacties op gedetecteerde verdachte acties met betrekking tot gebruikersidentiteiten te configureren. U kunt ook gegevens opnemen in Azure Sentinel voor verder onderzoek.

- [Riskante Azure AD-aanmeldingen weergeven](../active-directory/identity-protection/overview-identity-protection.md)

- [Identiteitsbeveiligingsrisicobeleid configureren en inschakelen](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Onboarding van Azure Sentinel](../sentinel/quickstart-onboard.md)

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

**Richtlijnen:** Implementeert afzonderlijke abonnementen en/of beheergroepen voor ontwikkeling, test en productie. Functie-apps moeten worden gescheiden door een virtueel netwerk (VNet)/subnet en op de juiste wijze worden getagd.

U kunt ook privé-eindpunten gebruiken om netwerkisolatie uit te voeren. Een privé-eindpunt van Azure is een netwerkinterface waarmee u privé en veilig verbinding maakt met een service (bijvoorbeeld het HTTPs-eindpunt van de functie-app) powered by Azure Private Link. Private Endpoint maakt gebruik van een privé-IP-adres van uw VNet, waarbij de service effectief in uw VNet wordt geplaatst. U kunt privé-eindpunten gebruiken voor uw functies die worden gehost in de Premium- en App Service-abonnementen.

- [Extra Azure-abonnementen maken](../cost-management-billing/manage/create-subscription.md)

- [Een Beheergroepen](../governance/management-groups/create-management-group-portal.md)

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

- [Netwerkopties van Azure Functions](functions-networking-options.md)

- [Azure Functions Premium-abonnement](functions-premium-plan.md)

- [Inzicht in privé-eindpunt](../private-link/private-endpoint-overview.md)

- [Privé-eindpunten gebruiken voor Azure Functions](../app-service/networking/private-endpoint.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Alle gevoelige gegevens tijdens de overdracht versleutelen

**Richtlijnen:** schakel in de Azure Portal voor uw functie-apps, onder Platformfuncties: Netwerken: SSL, de instelling Alleen HTTPs in en stel de minimale TLS-versie in op 1.2.

- [HTTPS vereisen voor functie-apps](./security-concepts.md#require-https)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** De [Azure Security-benchmark](/azure/governance/policy/samples/azure-security-benchmark) is het standaardbeleidsinitiatief voor Security Center en is de basis voor [Security Center aanbevelingen van Security Center.](/azure/security-center/security-center-recommendations) De Azure Policy met betrekking tot dit besturingselement worden automatisch ingeschakeld door Security Center. Waarschuwingen met betrekking tot dit besturingselement vereisen mogelijk een [Azure Defender](/azure/security-center/azure-defender) voor de gerelateerde services.

**Azure Policy ingebouwde definities - Microsoft.Web:**

[!INCLUDE [Resource Policy for Microsoft.Web 4.4](../../includes/policy/standards/asb/rp-controls/microsoft.web-4-4.md)]

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Een actief detectieprogramma gebruiken om gevoelige gegevens te identificeren

**Richtlijnen:** momenteel niet beschikbaar; functies voor gegevensidentificatie, -classificatie en -preventie zijn momenteel niet beschikbaar voor Azure Functions. Tag functie-apps die gevoelige informatie als zodanig kunnen verwerken en implementeert indien nodig een oplossing van derden voor nalevingsdoeleinden.

Voor het onderliggende platform dat wordt beheerd door Microsoft, behandelt Microsoft alle klantinhoud als gevoelig en gaat het in grote lengte om zich te beschermen tegen gegevensverlies en -blootstelling van klanten. Om ervoor te zorgen dat klantgegevens in Azure veilig blijven, heeft Microsoft een suite met robuuste besturingselementen en mogelijkheden voor gegevensbeveiliging geïmplementeerd en onderhouden.

- [Informatie over beveiliging van klantgegevens in Azure](../security/fundamentals/protection-customer-data.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6: Op rollen gebaseerd toegangsbeheer gebruiken om de toegang tot resources te beheren

**Richtlijnen:** Gebruik op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC) voor het beheren van toegang tot het besturingsvlak van de functie-app (Azure Portal).

- [Azure RBAC configureren](../role-based-access-control/role-assignments-portal.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Preventie van gegevensverlies op basis van een host gebruiken om toegangsbeheer af te dwingen

**Richtlijnen:** Niet van toepassing; deze aanbeveling is bedoeld voor IaaS-rekenbronnen.

Microsoft beheert de onderliggende infrastructuur voor Azure Functions en heeft strenge controles geïmplementeerd om verlies of blootstelling van klantgegevens te voorkomen.

- [Informatie over beveiliging van klantgegevens in Azure](../security/fundamentals/protection-customer-data.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Gevoelige informatie at-rest versleutelen

**Richtlijnen:** wanneer u een functie-app maakt, moet u een Azure Storage account voor algemeen gebruik maken of koppelen dat ondersteuning biedt voor Blob-, Queue- en Table-opslag. Dit komt doordat Functions afhankelijk is van Azure Storage voor bewerkingen zoals het beheren van triggers en het vastleggen van functie-uitvoeringen. Azure Storage versleutelt alle gegevens in een opslagaccount at rest. Gegevens worden standaard versleuteld met door Microsoft beheerde sleutels. Voor extra controle over versleutelingssleutels kunt u door de klant beheerde sleutels voor versleuteling van blob- en bestandsgegevens leveren. Deze sleutels moeten aanwezig zijn in Azure Key Vault de functie-app toegang kan krijgen tot het opslagaccount.

- [Inzicht in opslagoverwegingen voor Azure Functions](storage-considerations.md)

- [Inzicht in Azure Storage-versleuteling voor data-at-rest](../storage/common/storage-service-encryption.md)

**Verantwoordelijkheid**: Gedeeld

**Azure Security Center bewaking:** geen

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Logboeken en waarschuwingen voor wijzigingen in kritieke Azure-resources

**Richtlijnen:** gebruik Azure Monitor met het Azure-activiteitenlogboek om waarschuwingen te maken voor wanneer wijzigingen worden aangebracht in productiefunctie-apps en andere kritieke of gerelateerde resources.

- [Waarschuwingen maken voor gebeurtenissen in het Azure-activiteitenlogboek](../azure-monitor/alerts/alerts-activity-log.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="vulnerability-management"></a>Beheer van beveiligingsproblemen

*Zie de Azure [Security Benchmark: Vulnerability Management](../security/benchmarks/security-control-vulnerability-management.md)voor meer informatie.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Geautomatiseerde hulpprogramma's voor het scannen van beveiligingsleed uitvoeren

**Richtlijnen:** Een DevSecOps-praktijk gebruiken om ervoor te zorgen dat uw functie-apps veilig zijn en zo veilig mogelijk blijven gedurende hun levenscyclus. DevSecOps bevat het beveiligingsteam van uw organisatie en hun mogelijkheden in uw DevOps-procedures, waardoor beveiliging een verantwoordelijkheid is voor iedereen in het team.

Volg daarnaast de aanbevelingen van Azure Security Center om uw functie-apps te beveiligen.

- [Continue beveiligingsvalidatie toevoegen aan uw CI/CD-pijplijn](/azure/devops/migrate/security-validation-cicd-pipeline?preserve-view=true&view=azure-devops)

- [Aanbevelingen voor de evaluatie Azure Security Center beveiligingsprobleem implementeren](../security-center/deploy-vulnerability-assessment-vm.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Gebruik een proces voor risicoclassificatie om prioriteit te geven aan het herstellen van gevonden beveiligingsproblemen

**Richtlijnen:** Microsoft voert vulnerability management uit op de onderliggende systemen die ondersteuning bieden voor Azure Functions, maar u kunt de ernst van de aanbevelingen in Azure Security Center en de beveiligingsscore gebruiken om risico's binnen uw omgeving te meten. Uw beveiligingsscore is gebaseerd op het aantal Security Center dat u hebt beperkt. Als u prioriteit wilt geven aan de aanbevelingen die u als eerste wilt oplossen, moet u rekening houden met de ernst van elke aanbeveling.

- [Referentiehandleiding voor beveiligingsaanbevelingen](../security-center/recommendations-reference.md)

**Verantwoordelijkheid**: Gedeeld

**Azure Security Center bewaking:** geen

## <a name="inventory-and-asset-management"></a>Inventarisatie en Asset Management

*Zie Azure Security [Benchmark: Inventory and Asset Management (Azure Security Benchmark: Inventaris en Asset Management) voor meer informatie.](../security/benchmarks/security-control-inventory-asset-management.md)*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Een geautomatiseerde oplossing voor assetdetectie gebruiken

**Richtlijnen:** gebruik Azure Resource Graph voor het opvragen/ontdekken van alle resources (zoals rekenkracht, opslag, netwerk, poorten en protocollen, enzovoort) binnen uw abonnement(en). Zorg voor de juiste machtigingen (lezen) in uw tenant en semuler alle Azure-abonnementen en resources binnen uw abonnementen.

Hoewel klassieke Azure-resources kunnen worden ontdekt via Resource Graph, wordt het ten zeerste aanbevolen om in de toekomst Azure Resource Manager te maken en te gebruiken.

- [Query's maken met Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

- [Uw Azure-abonnementen weergeven](/powershell/module/az.accounts/get-azsubscription?preserve-view=true&view=azps-4.8.0)

- [Inzicht krijgen in Azure RBAC](../role-based-access-control/overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="62-maintain-asset-metadata"></a>6.2: Metagegevens van activa onderhouden

**Richtlijnen:** Tags toepassen op Azure-resources die metagegevens geven om ze logisch te organiseren in een taxonomie.

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Niet-geautoriseerde Azure-resources verwijderen

**Richtlijnen:** gebruik waar van toepassing tags, beheergroepen en afzonderlijke abonnementen om Azure-resources te organiseren en bij te houden. Inventaris regelmatig afstemmen en ervoor zorgen dat niet-geautoriseerde resources tijdig uit het abonnement worden verwijderd.

Gebruik daarnaast Azure Policy om beperkingen in te stellen voor het type resources dat kan worden gemaakt in een of meer klantabonnementen met behulp van de volgende ingebouwde beleidsdefinities:

- Niet toegestane resourcetypen

- Toegestane brontypen

Aanvullende informatie is beschikbaar via de koppelingen waarnaar wordt verwezen.

- [Aanvullende Azure-abonnementen maken](../cost-management-billing/manage/create-subscription.md)

- [Een Beheergroepen](../governance/management-groups/create-management-group-portal.md)

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4: Inventaris van goedgekeurde Azure-resources definiëren en onderhouden

**Richtlijnen:** Goedgekeurde Azure-resources en goedgekeurde software voor rekenbronnen definiëren.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Controleren op niet-goedgekeurde Azure-resources

**Richtlijnen:** Azure Policy beperkingen in te stellen voor het type resources dat in uw abonnement(en) kan worden gemaakt. 

Gebruik Azure Resource Graph om query's uit te voeren op resources binnen hun abonnement(en).  Zorg ervoor dat alle Azure-resources in de omgeving zijn goedgekeurd. 

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md) 

- [Query's maken met Azure Graph](../governance/resource-graph/first-query-portal.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="69-use-only-approved-azure-services"></a>6.9: Alleen goedgekeurde Azure-services gebruiken

**Richtlijnen:** gebruik Azure Policy om beperkingen in te stellen voor het type resources dat kan worden gemaakt in klantabonnementen met behulp van de volgende ingebouwde beleidsdefinities:

- Niet toegestane resourcetypen

- Toegestane brontypen

Aanvullende informatie is beschikbaar via de koppelingen waarnaar wordt verwezen.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Een specifiek resourcetype weigeren met Azure Policy](../governance/policy/samples/built-in-policies.md#general)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: De mogelijkheid van gebruikers om te communiceren met Azure Resource Manager

**Richtlijnen:** Configureer voorwaardelijke toegang van Azure om de mogelijkheid van gebruikers om te communiceren met Azure Resource Manager te beperken door Toegang blokkeren te configureren voor de app Microsoft Azure Management.

- [Voorwaardelijke toegang configureren om de toegang tot Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="secure-configuration"></a>Veilige configuratie

*Zie de Azure [Security Benchmark: Secure Configuration voor meer informatie.](../security/benchmarks/security-control-secure-configuration.md)*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Veilige configuraties voor alle Azure-resources tot stand brengen

**Richtlijnen:** standaardbeveiligingsconfiguraties definiëren en implementeren voor uw functie-app met Azure Policy. Gebruik Azure Policy aliassen in de naamruimte Microsoft.Web om aangepaste beleidsregels te maken om de configuratie van uw Functions-apps te controleren of af te dwingen. U kunt ook gebruikmaken van ingebouwde beleidsdefinities, zoals:

- Er moet een beheerde identiteit worden gebruikt in uw functie-app

- Externe debugging moet worden uitgeschakeld voor functie-apps

- functie-app mag alleen toegankelijk zijn via HTTPS

Aanvullende informatie is beschikbaar via de koppelingen waarnaar wordt verwezen.

- [Beschikbare aliassen Azure Policy weergeven](/powershell/module/az.resources/get-azpolicyalias?preserve-view=true&view=azps-4.8.0)

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Veilige Azure-resourceconfiguraties onderhouden

**Richtlijnen:** Gebruik Azure-beleid [weigeren] en [implementeren indien niet aanwezig] om veilige instellingen af te dwingen voor uw Azure-resources.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Inzicht in Azure Policy effecten](../governance/policy/concepts/effects.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Configuratie van Azure-resources veilig opslaan

**Richtlijnen:** ARM-sjablonen en aangepaste Azure-beleidsdefinities veilig opslaan en beheren in broncodebeheer.

- [Wat is infrastructuur als code?](/azure/devops/learn/what-is-infrastructure-as-code)

- [Beleid ontwerpen als codewerkstromen](../governance/policy/concepts/policy-as-code.md)

- [Code opslaan in Azure DevOps](/azure/devops/repos/git/gitworkflow?preserve-view=true&view=azure-devops)

- [Documentatie voor Azure-repos](/azure/devops/repos/?preserve-view=true&view=azure-devops)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Hulpprogramma's voor configuratiebeheer implementeren voor Azure-resources

**Richtlijnen:** gebruik ingebouwde Azure Policy-definities en Azure Policy-aliassen in de naamruimte Microsoft.Web om aangepaste beleidsregels te maken voor het waarschuwen, controleren en afdwingen van systeemconfiguraties. Daarnaast ontwikkelt u een proces en pijplijn voor het beheren van beleids uitzonderingen.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Geautomatiseerde configuratiebewaking implementeren voor Azure-resources

**Richtlijnen:** gebruik ingebouwde Azure Policy-definities en Azure Policy-aliassen in de naamruimte Microsoft.Web om aangepaste beleidsregels te maken voor het waarschuwen, controleren en afdwingen van systeemconfiguraties. Gebruik Azure Policy [audit], [deny] en [deploy if not exist] om automatisch configuraties af te dwingen voor uw Azure-resources.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="711-manage-azure-secrets-securely"></a>7.11: Azure-geheimen veilig beheren

**Richtlijnen:** Gebruik beheerde identiteiten in combinatie met Azure Key Vault om het beheer van geheimen voor uw cloudtoepassingen te vereenvoudigen en te beveiligen. Met beheerde identiteiten kan uw functie-app worden geverifieerd bij elke service die ondersteuning biedt voor Azure Active Directory-verificatie (Azure AD), inclusief Key Vault, zonder referenties in uw code.

- [Een Key Vault](../key-vault/secrets/quick-create-portal.md)

- [Beheerde identiteiten gebruiken voor App Service en Azure Functions](../app-service/overview-managed-identity.md)

- [Verificatie bij Key Vault](../key-vault/general/authentication.md)

- [Een toegangsbeleid voor Key Vault toewijzen](../key-vault/general/assign-access-policy-portal.md)

- [Gebruik Key Vault voor App Service en Azure Functions](../app-service/app-service-key-vault-references.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Identiteiten veilig en automatisch beheren

**Richtlijnen:** Beheerde identiteiten gebruiken om uw functie-app te voorzien van een automatisch beheerde identiteit in Azure Active Directory (Azure AD). Met beheerde identiteiten kunt u zich verifiëren bij elke service die ondersteuning biedt voor Azure AD-verificatie, Key Vault, zonder referenties in uw code.

- [Beheerde identiteiten gebruiken voor App Service en Azure Functions](../app-service/overview-managed-identity.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** De [Azure Security-benchmark](/azure/governance/policy/samples/azure-security-benchmark) is het standaardbeleidsinitiatief voor Security Center en is de basis voor [Security Center aanbevelingen van Security Center.](/azure/security-center/security-center-recommendations) De Azure Policy met betrekking tot dit besturingselement worden automatisch ingeschakeld door Security Center. Voor waarschuwingen met betrekking tot dit besturingselement is mogelijk een [Azure Defender](/azure/security-center/azure-defender) nodig voor de gerelateerde services.

**Azure Policy ingebouwde definities - Microsoft.Web**:

[!INCLUDE [Resource Policy for Microsoft.Web 7.12](../../includes/policy/standards/asb/rp-controls/microsoft.web-7-12.md)]

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Onbedoelde referentieblootstelling voorkomen

**Richtlijnen:** Referentiescanner implementeren om referenties in code te identificeren. Door het gebruik van Credential Scanner worden gebruikers ook aangemoedigd om gedetecteerde referenties naar veiligere locaties, zoals Azure Key Vault, te verplaatsen. 

- [Referentiescanner instellen](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="data-recovery"></a>Gegevensherstel

*Zie de Azure [Security Benchmark: Data Recovery voor meer informatie.](../security/benchmarks/security-control-data-recovery.md)*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Regelmatige automatische back-ups garanderen

**Richtlijnen:** gebruik de functie Back-up en herstel om regelmatige back-ups van uw app te plannen. Functie-apps die worden uitgevoerd in het Premium-abonnement hebben dezelfde hostingmogelijkheden als web-apps in Azure App Service, waaronder de functie Back-up en herstel.

Maak ook gebruik van een broncodebeheeroplossing zoals Azure Repos en Azure DevOps om uw code veilig op te slaan en te beheren. Azure DevOps Services maakt gebruik van veel van de functies van Azure Storage om te zorgen voor de beschikbaarheid van gegevens in het geval van hardwarestoringen, serviceonderbrekingen of een regionale noodsituatie. Daarnaast volgt het Azure DevOps-team procedures voor het beveiligen van gegevens tegen onbedoelde verwijdering of verwijdering met kwaadaardige opzet.

- [Back-up maken van uw app in Azure](../app-service/manage-backup.md)

- [Inzicht in gegevensbeschikbaarheid in Azure DevOps](/azure/devops/organizations/security/data-protection?preserve-view=true&view=azure-devops#data-availability)

- [Code opslaan in Azure DevOps](/azure/devops/repos/git/gitworkflow?preserve-view=true&view=azure-devops)

- [Documentatie voor Azure-repos](/azure/devops/repos/?preserve-view=true&view=azure-devops)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Volledige systeemback-ups uitvoeren en back-ups maken van door de klant beheerde sleutels

**Richtlijnen:** gebruik de functie Back-up en herstel om regelmatige back-ups van uw app te plannen. Functie-apps die worden uitgevoerd in het Premium-abonnement hebben dezelfde hostingmogelijkheden als web-apps in Azure App Service, waaronder de functie Back-up en herstel. Back-ups maken van door de klant beheerde sleutels binnen Azure Key Vault.

Maak ook gebruik van een bronbeheeroplossing zoals Azure Repos en Azure DevOps om uw code veilig op te slaan en te beheren. Azure DevOps Services maakt gebruik van veel van de functies van Azure Storage om te zorgen voor de beschikbaarheid van gegevens in het geval van hardwarestoringen, serviceonderbrekingen of een regionale noodsituatie. Daarnaast volgt het Azure DevOps-team procedures voor het beveiligen van gegevens tegen onbedoelde verwijdering of verwijdering met kwaadaardige opzet.

- [Back-up maken van uw app in Azure](../app-service/manage-backup.md)

- [Een back-up maken van sleutelkluissleutels in Azure](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

- [Inzicht in gegevensbeschikbaarheid in Azure DevOps](/azure/devops/organizations/security/data-protection?preserve-view=true&view=azure-devops#data-availability)

- [Code opslaan in Azure DevOps](/azure/devops/repos/git/gitworkflow?preserve-view=true&view=azure-devops)

- [Documentatie voor Azure-repos](/azure/devops/repos/?preserve-view=true&view=azure-devops)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Alle back-ups valideren, inclusief door de klant beheerde sleutels

**Richtlijnen:** zorg ervoor dat u periodiek herstel kunt uitvoeren vanuit de functie Back-up en herstel. Als u een andere offlinelocatie gebruikt om een back-up van uw code te maken, zorg er dan regelmatig voor dat u volledige herstelwerkzaamheden kunt uitvoeren. Test het herstel van door de klant beheerde back-upsleutels.

- [Een app in Azure herstellen vanuit een back-up](../app-service/web-sites-restore.md)

- [Een app in Azure herstellen vanuit een momentopname](../app-service/app-service-web-restore-snapshots.md)

- [Sleutelkluissleutels herstellen in Azure](/powershell/module/az.keyvault/restore-azkeyvaultkey?preserve-view=true&view=azps-4.8.0)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Beveiliging van back-ups en door de klant beheerde sleutels garanderen

**Richtlijnen:** back-ups van de functie Back-up en herstel maken gebruik van Azure Storage account in uw abonnement. Azure Storage versleutelt alle gegevens in een opslagaccount at rest. Standaard worden gegevens versleuteld met door Microsoft beheerde sleutels. Voor extra controle over versleutelingssleutels kunt u door de klant beheerde sleutels leveren voor versleuteling van opslaggegevens.

Als u door de klant beheerde sleutels gebruikt, moet u ervoor zorgen dat Soft-Delete in Key Vault is ingeschakeld om sleutels te beveiligen tegen onbedoelde of schadelijke verwijdering.

- [Versleuteling-at-rest in Azure Storage](../storage/common/storage-service-encryption.md)

- [Een Soft-Delete inschakelen in Key Vault](../storage/blobs/soft-delete-blob-overview.md)

**Verantwoordelijkheid**: Gedeeld

**Azure Security Center bewaking:** geen

## <a name="incident-response"></a>Reageren op incidenten

*Zie [Azure Security Benchmark: respons op incidenten](../security/benchmarks/security-control-incident-response.md) voor meer informatie.*

### <a name="101-create-an-incident-response-guide"></a>10.1: Een handleiding voor het reageren op incidenten maken

**Richtlijnen**: Stel voor uw organisatie een responshandleiding op voor gebruik bij incidenten. Zorg ervoor dat er schriftelijke responsplannen zijn waarin alle rollen van het personeel worden gedefinieerd, evenals alle fasen in het afhandelen/managen van incidenten, vanaf de detectie van het incident tot een evaluatie ervan achteraf.

- [Werkstroomautomatiseringen configureren in Azure Security Center](../security-center/security-center-planning-and-operations-guide.md)

- [Richtlijnen voor het bouwen van uw eigen reactieproces voor beveiligingsincident](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft Security Response Center van een incident](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [NIST's Computer Security Incident Handling Guide](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Een procedure voor het scoren en prioriteren van incidenten maken

**Richtlijnen:** Security Center aan elke waarschuwing een ernst toe om u te helpen bepalen welke waarschuwingen het eerst moeten worden onderzocht. De ernst is gebaseerd op hoe zeker Security Center is in het zoeken of de analyse die wordt gebruikt om de waarschuwing uit te geven, evenals het betrouwbaarheidsniveau dat er een kwaadwillende intentie is achter de activiteit die heeft geleid tot de waarschuwing.

Daarnaast moet u abonnementen duidelijk markeren (bijvoorbeeld productie, niet-prod) en een naamgevingssysteem maken om Azure-resources duidelijk te identificeren en te categoriseren.

**Verantwoordelijkheid**: Gedeeld

**Azure Security Center bewaking:** geen

### <a name="103-test-security-response-procedures"></a>10.3: Procedures voor het reageren op beveiliging testen

**Richtlijnen:** oefeningen uitvoeren om de mogelijkheden voor het reageren op incidenten van uw systemen regelmatig te testen. Stel vast waar zich zwakke plekken en hiaten bevinden, en wijzig zo nodig het plan.

- [Raadpleeg de publicatie van NIST: Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities (Handleiding voor test-, trainings- en oefeningsprogramma's voor IT-plannen en -mogelijkheden)](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Contactgegevens voor beveiligingsincidenten verstrekken en waarschuwingsmeldingen voor beveiligingsincidenten configureren

**Richtlijnen:** Contactgegevens voor beveiligingsincidenten worden door Microsoft gebruikt om contact met u op te nemen als de Microsoft Security Response Center (MSRC) detecteert dat de gegevens van de klant zijn gebruikt door een onrechtmatige of niet-geautoriseerde partij.  Bekijk incidenten na het feit om ervoor te zorgen dat problemen worden opgelost.

- [De beveiligingscontactcontacte Azure Security Center instellen](../security-center/security-center-provide-security-contact-details.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Beveiligingswaarschuwingen opnemen in uw systeem voor het reageren op incidenten

**Richtlijnen:** exporteert Azure Security Center waarschuwingen en aanbevelingen met behulp van de functie Continue export. Met continue export kunt u waarschuwingen en aanbevelingen handmatig of doorlopend exporteren. U kunt de connector Azure Security Center gebruiken om de waarschuwingen te streamen naar Azure Sentinel.

- [Continue export configureren](../security-center/continuous-export.md)

- [Waarschuwingen streamen naar Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: De reactie op beveiligingswaarschuwingen automatiseren

**Richtlijnen:** gebruik de functie Werkstroomautomatisering in Azure Security Center automatisch reacties op beveiligingswaarschuwingen en aanbevelingen te activeren met Logic Apps.

- [Werkstroomautomatisering en -Logic Apps](../security-center/workflow-automation.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="penetration-tests-and-red-team-exercises"></a>Penetratietests en Red Team-oefeningen

*Zie de Azure [Security Benchmark: Penetration Tests en Red Team Exercises (Penetratietests en Red Team-oefeningen) voor meer informatie.](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Regelmatige penetratietests uitvoeren van uw Azure-resources en zorgen voor herstel van alle kritieke beveiligings bevindingen

**Richtlijnen:** volg de Microsoft-regels voor betrokkenheid om ervoor te zorgen dat uw penetratietests niet in strijd zijn met het Microsoft-beleid. Gebruik de strategie en uitvoering van Microsoft voor red teaming en penetratietests van live-site tegen door Microsoft beheerde cloudinfrastructuur, -services en -toepassingen.

- [Regels voor het inzetten van penetratietests](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Verantwoordelijkheid**: Gedeeld

**Azure Security Center bewaking:** geen

## <a name="next-steps"></a>Volgende stappen

- Zie [Overzicht Azure Security Benchmark V2](../security/benchmarks/overview.md)
- Meer informatie over [Azure-beveiligingsbasislijnen](../security/benchmarks/security-baselines-overview.md)
