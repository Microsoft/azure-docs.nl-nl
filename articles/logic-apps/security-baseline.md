---
title: Azure-beveiligings basislijn voor Logic Apps
description: De Logic Apps Security Baseline voorziet in procedure richtlijnen en resources voor het implementeren van de beveiligings aanbevelingen die zijn opgegeven in de Azure Security-Bench Mark.
author: msmbaldwin
ms.service: logic-apps
ms.topic: conceptual
ms.date: 09/01/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: cf4e02f925e0c8c4024137589c0c9c4074c0aa11
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100596757"
---
# <a name="azure-security-baseline-for-logic-apps"></a>Azure-beveiligings basislijn voor Logic Apps

De Azure-beveiligings basislijn voor Logic Apps bevat aanbevelingen waarmee u de beveiligings postuur van uw implementatie kunt verbeteren.

De basis lijn voor deze service wordt opgehaald uit de [Azure Security Bench Mark-versie 1,0](../security/benchmarks/overview.md), die aanbevelingen biedt over hoe u uw cloud oplossingen kunt beveiligen in azure met onze richt lijnen voor best practices.

Zie [overzicht van Azure Security-basis lijnen](../security/benchmarks/security-baselines-overview.md)voor meer informatie.

## <a name="network-security"></a>Netwerkbeveiliging

*Zie [Azure Security Bench Mark: Network Security](../security/benchmarks/security-control-network-security.md)(Engelstalig) voor meer informatie.*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Azure-resources in virtuele netwerken beveiligen

**Hulp**: Connect oren die worden uitgevoerd in de ' wereld wijde ' multi tenant-Logic apps-service, worden geïmplementeerd en beheerd door micro soft. Deze connectors bieden triggers en acties voor toegang tot Cloud Services, on-premises systemen of beide, waaronder Office 365, Azure Blob Storage, SQL Server, Dynamics, Sales Force, share point en meer. U kunt het AzureConnectors-service label gebruiken om regels op te geven voor uw netwerk beveiligings groep of Azure Firewall om toegang tot gerelateerde resources toe te staan.

Voor Logic apps die directe toegang tot resources in een virtueel Azure-netwerk nodig hebben, kunt u een ISE (Integration service Environment) maken waarin u uw Logic apps op toegewezen bronnen kunt bouwen, implementeren en uitvoeren. Sommige virtuele netwerken van Azure gebruiken privé-eind punten (persoonlijke Azure-koppeling) om toegang te bieden tot Azure PaaS-Services, zoals Azure Storage, Azure Cosmos DB, Azure SQL Database, partner services of klanten services die worden gehost op Azure. Als uw Logic apps toegang nodig hebben tot virtuele netwerken die gebruikmaken van privé-eind punten, moet u deze Logic apps maken, implementeren en uitvoeren in een ISE.

Wanneer u uw ISE maakt, kunt u kiezen of u interne of Externe toegangs punten wilt gebruiken. Uw selectie bepaalt of aanvragen of webhooks worden geactiveerd op Logic apps in uw ISE kan aanroepen ontvangen van buiten uw virtuele netwerk. Interne en externe toegangs punten beïnvloeden ook of u de uitvoerings geschiedenis van de logische app kunt bekijken, inclusief de invoer en uitvoer voor een uitvoering, vanuit of buiten uw virtuele netwerk.

Zorg ervoor dat alle subnetten implementaties van virtuele netwerken die betrekking hebben op uw ISE, een netwerk beveiligings groep hebben die is toegepast op de vertrouwde poorten en bronnen van uw toepassing. Wanneer u uw Logic apps in een ISE implementeert, kunt u een persoonlijke koppeling gebruiken. Met persoonlijke Azure-koppeling kunt u toegang krijgen tot Azure PaaS Services en Azure-Services die zijn gehoste klanten service via een persoonlijk eind punt in uw virtuele netwerk. Als u een specifieke use-case hebt, kunt u ook aan deze vereiste voldoen door Azure Firewall te implementeren. Gebruik service tags die groepen met IP-adres voorvoegsels vertegenwoordigen voor een specifieke Azure-service om de complexiteit te verminderen bij het instellen van beveiligings regels.

- [Connectors voor Logic Apps](../connectors/apis-list.md)

- [Informatie over service tags in azure](../virtual-network/service-tags-overview.md)

- [Toegang tot Azure Virtual Network-Resources van Azure Logic Apps begrijpen met behulp van integratie service omgevingen (ISEs)](connect-virtual-network-vnet-isolated-environment-overview.md)

- [Informatie over Virtual Network Service-eind punten](../virtual-network/virtual-network-service-endpoints-overview.md)

- [Persoonlijke Azure-koppeling](../private-link/private-link-overview.md)

- [Meer informatie over ISE endpoint Access](connect-virtual-network-vnet-isolated-environment-overview.md#ise-endpoint-access)

- [Een Virtual Network maken](../virtual-network/quick-create-portal.md)

- [Een NSG maken met een beveiligings configuratie](../virtual-network/tutorial-filter-network-traffic.md)

- [Azure Firewall implementeren en configureren](../firewall/tutorial-firewall-deploy-portal.md)

- [Toegang voor ISE inschakelen](connect-virtual-network-vnet-isolated-environment.md#enable-access-for-ise)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Gedeeld

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: de configuratie en het verkeer van virtuele netwerken, subnetten en netwerk interfaces bewaken en vastleggen

**Richt lijnen**: als u Logic apps uitvoert in een Integration service Environment (ISE) die gebruikmaakt van een extern toegangs punt, kunt u een netwerk beveiligings groep (NSG) gebruiken om het risico van gegevens exfiltration te verminderen. Schakel logboeken voor NSG-stroom in en verzend logboeken naar een Azure Storage account voor verkeers controle. U kunt ook NSG-stroom logboeken naar een Log Analytics-werk ruimte verzenden en Traffic Analytics gebruiken om inzicht te krijgen in de verkeers stroom in uw Azure-Cloud. Enkele voor delen van Traffic Analytics zijn de mogelijkheid om netwerk activiteiten te visualiseren en HOTS pots te identificeren, beveiligings dreigingen te identificeren, verkeers patronen te begrijpen en netwerk configuraties te lokaliseren.

- [Meer informatie over ISE endpoint Access](connect-virtual-network-vnet-isolated-environment-overview.md#ise-endpoint-access)

- [NSG-stroom logboeken inschakelen](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Traffic Analytics inschakelen en gebruiken](../network-watcher/traffic-analytics.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="13-protect-critical-web-applications"></a>1,3: essentiële webtoepassingen beveiligen

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor webtoepassingen die worden uitgevoerd op Azure App Service of reken bronnen.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: communicatie met bekende schadelijke IP-adressen weigeren

**Richt lijnen**: als uw logische app gebruikmaakt van een trigger op basis van een aanvraag, die binnenkomende oproepen of aanvragen ontvangt, zoals de trigger van de aanvraag of de webhook, kunt u de toegang beperken zodat alleen geautoriseerde clients uw logische app kunnen aanroepen.

Als u Logic apps uitvoert in een Integration service Environment (ISE), schakelt u DDoS Protection standaard in het virtuele netwerk dat is gekoppeld aan uw ISE om te beschermen tegen DDoS-aanvallen. Gebruik Azure Security Center geïntegreerde bedreigings informatie om communicatie met bekende of ongebruikte Internet-IP-adressen te weigeren.

Implementeer Azure Firewall op elke netwerk grenzen van de organisatie met behulp van bedreigings informatie en geconfigureerd voor ' waarschuwen en weigeren ' voor schadelijk netwerk verkeer.

Gebruik Azure Security Center just-in-time-netwerk toegang om Nsg's te configureren om de bloot stelling van eind punten te beperken tot goedgekeurde IP-adressen gedurende een beperkte periode.

Gebruik Azure Security Center adaptieve netwerk beveiliging om NSG-configuraties aan te bevelen die poorten en bron-Ip's beperken op basis van daad werkelijk verkeer en bedreigings informatie.

- [Binnenkomende oproepen naar Logic Apps beveiligen](logic-apps-securing-a-logic-app.md#secure-inbound-requests)

- [Hoe kan ik de inkomende IP-adressen beperken?](logic-apps-securing-a-logic-app.md#restrict-inbound-ip-addresses)

- [DDoS-beveiliging configureren](../ddos-protection/manage-ddos-protection.md)

- [Azure Firewall implementeren](../firewall/tutorial-firewall-deploy-portal.md)

- [Meer informatie over Azure Security Center geïntegreerde bedreigings informatie](../security-center/azure-defender.md)

- [Meer informatie over Azure Security Center adaptieve netwerk beveiliging](../security-center/security-center-adaptive-network-hardening.md)

- [Meer informatie over Azure Security Center just-in-time-netwerk Access Control](../security-center/security-center-just-in-time.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="15-record-network-packets"></a>1,5: netwerk pakketten opnemen

**Richt lijnen**: als u Logic apps uitvoert in een Integration service Environment (ISE) die gebruikmaakt van een extern toegangs punt, kunt u een netwerk beveiligings groep (NSG) gebruiken om het risico van gegevens exfiltration te verminderen. Schakel logboeken voor NSG-stroom in en verzend logboeken naar een Azure Storage account voor verkeers controle. U kunt ook NSG-stroom logboeken naar een Log Analytics-werk ruimte verzenden en Traffic Analytics gebruiken om inzicht te krijgen in de verkeers stroom in uw Azure-Cloud. Enkele voor delen van Traffic Analytics zijn de mogelijkheid om netwerk activiteiten te visualiseren en HOTS pots te identificeren, beveiligings dreigingen te identificeren, verkeers patronen te begrijpen en netwerk configuraties te lokaliseren.

Als u meer beveiliging en informatie over het netwerk verkeer wilt bieden, kunt u verwijzen naar Access logs. deze worden alleen gegenereerd als u deze hebt ingeschakeld op elk Application Gateway exemplaar. U kunt dit logboek gebruiken om Application Gateway toegangs patronen weer te geven en belang rijke informatie te analyseren. Dit geldt ook voor de IP van de beller, de aangevraagde URL, reactie latentie, retour code en bytes in en uit.

Als dat niet het geval is, kunt u gebruikmaken van een oplossing van derden van de Marketplace om aan deze vereiste te voldoen.

- [Meer informatie over ISE endpoint Access](connect-virtual-network-vnet-isolated-environment-overview.md#ise-endpoint-access)

- [NSG-stroom logboeken inschakelen](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Traffic Analytics inschakelen en gebruiken](../network-watcher/traffic-analytics.md)

- [API Management integreren in een intern VNET met Application Gateway](../api-management/api-management-howto-integrate-internal-vnet-appgateway.md)

- [Meer informatie over WAF Access logs](../web-application-firewall/ag/web-application-firewall-logs.md#access-log)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: op netwerk gebaseerde inbreuk detectie/indringings systemen (ID'S/IP-adressen) implementeren

**Richt lijnen**: Selecteer een aanbieding van Azure Marketplace die ondersteuning biedt voor ID'S/IP-adressen met Payload-inspectie mogelijkheden.  Als inbraak detectie en/of preventie op basis van Payload-inspectie geen vereiste is, kan Azure Firewall met bedreigings informatie worden gebruikt. Azure Firewall op bedreigingen gebaseerd filteren kan verkeer van en naar bekende schadelijke IP-adressen en domeinen Signa lering en weigeren. De IP-adressen en domeinen zijn afkomstig van de Microsoft Bedreigingsinformatie-feed.

Implementeer de door u gewenste firewall oplossing op elk van de netwerk grenzen van uw organisatie om schadelijk verkeer te detecteren en/of te weigeren.

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

- [Azure Firewall implementeren](../firewall/tutorial-firewall-deploy-portal.md)

- [Waarschuwingen configureren met Azure Firewall](../firewall/threat-intel.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="17-manage-traffic-to-web-applications"></a>1,7: verkeer naar webtoepassingen beheren

**Richt lijnen**: als u Logic apps uitvoert in een Integration service Environment (ISE), implementeert u Azure-toepassing gateway.

- [API Management integreren in een intern VNET met Application Gateway](../api-management/api-management-howto-integrate-internal-vnet-appgateway.md)

- [Application Gateway configureren voor het gebruik van HTTPS](../application-gateway/create-ssl-portal.md) 

- [De taak verdeling van laag 7 met Azure Web Application-gateways begrijpen](../application-gateway/overview.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: de complexiteit en administratieve overhead van netwerk beveiligings regels minimaliseren

**Richt lijnen**: voor bronnen die toegang nodig hebben tot uw Azure Logic apps-instanties, gebruikt u de tags voor het virtuele netwerk om netwerk toegangs beheer te definiëren voor netwerk beveiligings groepen of Azure firewall. U kunt servicetags gebruiken in plaats van specifieke IP-adressen wanneer u beveiligingsregels maakt. Door de naam van de service label (bijvoorbeeld LogicApps, LogicAppsManagement) op te geven in het juiste bron-of doel veld van een regel, kunt u het verkeer voor de bijbehorende service toestaan of weigeren. Micro soft beheert de adres voorvoegsels die zijn opgenomen in het servicetag van de service en werkt de servicetag automatisch bij met gewijzigde adressen.

- [Voor meer informatie over het gebruik van service Tags](../virtual-network/service-tags-overview.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: standaard beveiligings configuraties voor netwerk apparaten onderhouden

**Hulp**: Definieer en implementeer standaard beveiligings configuraties voor netwerk bronnen die betrekking hebben op uw Azure Logic apps-instanties met Azure Policy. Gebruik Azure Policy aliassen in de naam ruimten ' micro soft. Logic ' en ' micro soft. Network ' om aangepaste beleids regels te maken om de netwerk configuratie van uw Azure Logic apps-instanties te controleren of af te dwingen. U kunt ook gebruik maken van ingebouwde beleids definities zoals:

- Diagnostische logboeken in Logic Apps moeten zijn ingeschakeld

- De DDoS Protection-standaard moet zijn ingeschakeld

U kunt ook Azure-blauw drukken gebruiken om grootschalige Azure-implementaties te vereenvoudigen door sleutel omgevings artefacten, zoals Azure Resource Manager sjablonen, Toegangs beheer op basis van rollen (Azure RBAC) en beleids regels, in één blauw definitie te verpakken. Pas de blauw druk toe op nieuwe abonnementen en omgevingen en Verfijn de controle en het beheer via versies.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Een Azure Blueprint maken](../governance/blueprints/create-blueprint-portal.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="110-document-traffic-configuration-rules"></a>1,10: configuratie regels voor het document verkeer

**Richt lijnen**: voor afzonderlijke NSG-regels gebruikt u het veld Beschrijving voor het opgeven van de bedrijfs behoefte en/of-duur (etc.) voor regels die verkeer naar/van een netwerk toestaan.

Gebruik een van de ingebouwde Azure Policy definities die betrekking hebben op labelen, zoals ' tag vereisen en de waarde ', om ervoor te zorgen dat alle resources met tags worden gemaakt en u op de hoogte moet zijn van bestaande niet-gelabelde resources.

U kunt Azure PowerShell of Azure CLI gebruiken om op basis van hun labels acties op resources te zoeken of uit te voeren.

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

- [Een Virtual Network maken](../virtual-network/quick-create-portal.md)

- [Een NSG maken met een beveiligings configuratie](../virtual-network/tutorial-filter-network-traffic.md)

- [Lijst met Azure Policy definities voor Logic Apps](./policy-reference.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: gebruik automatische hulpprogram ma's om netwerk bron configuraties te bewaken en wijzigingen te detecteren

**Hulp**: gebruik het Azure-activiteiten logboek om netwerk resource configuraties te bewaken en wijzigingen te detecteren voor netwerk bronnen die betrekking hebben op uw Azure Logic apps exemplaren. Maak waarschuwingen in Azure Monitor die worden geactiveerd wanneer er wijzigingen in kritieke netwerk bronnen plaatsvinden.

- [Activiteiten logboek gebeurtenissen van Azure weer geven en ophalen](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Waarschuwingen maken in Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="logging-and-monitoring"></a>Logboekregistratie en bewaking

*Zie [Azure Security Bench Mark: Logging and monitoring](../security/benchmarks/security-control-logging-monitoring.md)(Engelstalig) voor meer informatie.*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: goedgekeurde tijd synchronisatie bronnen gebruiken

**Richt lijnen**: micro soft onderhoudt de tijd bron die wordt gebruikt voor Azure-resources, zoals Azure Logic apps voor tijds tempels in de logboeken.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: micro soft

### <a name="22-configure-central-security-log-management"></a>2,2: Centraal beveiligings logboek beheer configureren

**Richt lijnen**: voor meer informatie over het opsporen van fouten in uw Logic apps tijdens runtime kunt u Azure monitor logboeken instellen en gebruiken voor het vastleggen en opslaan van informatie over runtime gegevens en gebeurtenissen, zoals trigger gebeurtenissen, uitvoer gebeurtenissen en actie gebeurtenissen in een log Analytics-werk ruimte. Azure Monitor helpt u bij het bewaken van uw cloud- en on-premises omgevingen, zodat u hun beschikbaarheid en prestaties gemakkelijker kunt onderhouden. Met Azure Monitor-Logboeken kunt u logboek query's maken die u helpen bij het verzamelen en controleren van deze gegevens. U kunt deze diagnostische gegevens ook gebruiken met andere Azure-Services, zoals Azure Storage en Azure Event Hubs.

U kunt ook gegevens in-of uitschakelen voor Azure Sentinel of een SIEM van derden. 

- [Diagnostische instellingen voor Azure-activiteiten logboek inschakelen](../azure-monitor/essentials/activity-log.md)

- [Azure Monitor logboeken instellen en diagnostische gegevens verzamelen voor Azure Logic Apps](monitor-logic-apps-log-analytics.md)

- [Azure-Sentinel onboarden](../sentinel/quickstart-onboard.md) 

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: controle logboek registratie inschakelen voor Azure-resources

**Richt lijnen**: voor meer informatie over het opsporen van fouten in uw Logic apps tijdens runtime kunt u Azure monitor logboeken instellen en gebruiken voor het vastleggen en opslaan van informatie over runtime gegevens en gebeurtenissen, zoals trigger gebeurtenissen, uitvoer gebeurtenissen en actie gebeurtenissen in een log Analytics-werk ruimte. Azure Monitor helpt u bij het bewaken van uw cloud- en on-premises omgevingen, zodat u hun beschikbaarheid en prestaties gemakkelijker kunt onderhouden. Met Azure Monitor-Logboeken kunt u logboek query's maken die u helpen bij het verzamelen en controleren van deze gegevens. U kunt deze diagnostische gegevens ook gebruiken met andere Azure-Services, zoals Azure Storage en Azure Event Hubs.

U kunt ook gegevens in-of uitschakelen voor Azure Sentinel of een SIEM van derden. 

- [Diagnostische instellingen voor Azure-activiteiten logboek inschakelen](../azure-monitor/essentials/activity-log.md)

- [Azure Monitor logboeken instellen en diagnostische gegevens verzamelen voor Azure Logic Apps](monitor-logic-apps-log-analytics.md)

- [Azure-Sentinel onboarden](../sentinel/quickstart-onboard.md) 

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: beveiligings logboeken verzamelen van besturings systemen

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="25-configure-security-log-storage-retention"></a>2,5: Bewaar beveiliging van het beveiligings logboek configureren

**Richt lijnen**: nadat u een logische app hebt gemaakt en uitgevoerd, kunt u de uitvoerings status van de logische app, de geschiedenis, de trigger geschiedenis en de prestaties controleren. Voor real-time gebeurtenis bewaking en uitgebreidere fout opsporing kunt u diagnostische logboek registratie voor uw logische app instellen met behulp van Azure Monitor-Logboeken. Deze Azure-service helpt u bij het bewaken van uw Cloud-en on-premises omgevingen, zodat u hun Beschik baarheid en prestaties gemakkelijker kunt onderhouden. U kunt vervolgens gebeurtenissen zoeken en weer geven, zoals trigger gebeurtenissen, uitvoerings gebeurtenissen en actie gebeurtenissen. Door deze informatie op te slaan in Azure Monitor logboeken, kunt u logboek query's maken waarmee u deze informatie vindt en analyseert. U kunt deze diagnostische gegevens ook gebruiken met andere Azure-Services, zoals Azure Storage en Azure Event Hubs.

Stel in Azure Monitor de Bewaar periode voor logboek registratie in voor de logboeken die zijn gekoppeld aan uw Azure Logic Apps-instanties volgens de nalevings voorschriften van uw organisatie.

- [De uitvoerings status controleren, de trigger geschiedenis controleren en waarschuwingen instellen voor Azure Logic Apps](monitor-logic-apps.md)

- [Para meters voor het bewaren van Logboeken instellen](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="26-monitor-and-review-logs"></a>2,6: Logboeken bewaken en controleren

**Richt lijnen**: als u logboek registratie wilt instellen voor uw logische app, kunt u log Analytics inschakelen wanneer u uw logische app maakt, of u kunt de Logic apps beheer oplossing in uw log Analytics-werk ruimte installeren voor bestaande logische apps. Deze oplossing biedt geaggregeerde gegevens voor uw logische app-uitvoeringen en bevat specifieke details zoals status, uitvoerings tijd, status van opnieuw verzenden en correlatie-Id's. Stel vervolgens Azure Monitor-Logboeken in om logboek registratie in te scha kelen en query's te maken voor deze informatie.

U kunt ook diagnostische instellingen van Azure-activiteiten logboek inschakelen en de logboeken verzenden naar een Log Analytics-werk ruimte. Query's uitvoeren in Log Analytics om zoek termen te zoeken, trends te identificeren, patronen te analyseren en veel andere inzichten te bieden op basis van de activiteiten logboek gegevens die mogelijk zijn verzameld voor Azure Logic Apps.

U kunt ook gegevens in-of uitschakelen voor Azure Sentinel of een SIEM van derden. 

- [Azure Monitor logboeken instellen en diagnostische gegevens verzamelen voor Azure Logic Apps](monitor-logic-apps-log-analytics.md)

- [Diagnostische instellingen voor Azure-activiteiten logboek inschakelen](../azure-monitor/essentials/activity-log.md)

- [Azure-activiteiten Logboeken in Log Analytics in Azure Monitor verzamelen en analyseren](../azure-monitor/essentials/activity-log.md)

- [Azure-Sentinel onboarden](../sentinel/quickstart-onboard.md) 

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: waarschuwingen inschakelen voor afwijkende activiteiten

**Hulp**: gebruik Azure Security Center met log Analytics voor bewaking en waarschuwingen voor afwijkende activiteiten die in beveiligings logboeken en gebeurtenissen zijn gevonden.

U kunt ook gegevens naar Azure-Sentinel inschakelen en op het bord zetten.

- [Azure-Sentinel onboarden](../sentinel/quickstart-onboard.md)

- [Waarschuwingen beheren in Azure Security Center](../security-center/security-center-managing-and-responding-alerts.md)

- [Een waarschuwing over logboek gegevens van log Analytics](../azure-monitor/alerts/tutorial-response.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="28-centralize-anti-malware-logging"></a>2,8: registratie van anti-malware centraliseren

**Richt lijnen**: niet van toepassing; Azure Logic Apps geen aan anti-malware gerelateerde logboeken verwerkt of produceert.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="29-enable-dns-query-logging"></a>2,9: DNS-query logboek registratie inschakelen

**Richt lijnen**: niet van toepassing; Azure Logic Apps verwerkt of produceert geen aan DNS gerelateerde Logboeken.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="210-enable-command-line-audit-logging"></a>2,10: controle logboek registratie op opdracht regel inschakelen

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

## <a name="identity-and-access-control"></a>Identiteits- en toegangsbeheer

*Zie [Azure Security Bench Mark: identiteits-en toegangs beheer](../security/benchmarks/security-control-identity-access-control.md)voor meer informatie.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: een inventaris van beheerders accounts onderhouden

**Hulp**: Azure Active Directory (AD) heeft ingebouwde rollen die expliciet moeten worden toegewezen en waarop query's kunnen worden doorzocht. Gebruik de Azure AD Power shell-module om ad hoc-query's uit te voeren om accounts te detecteren die lid zijn van beheer groepen.

Om toegang te krijgen tot andere bronnen die worden beveiligd door Azure Active Directory (Azure AD) en uw identiteit te verifiëren zonder zich aan te melden, kan uw logische app gebruikmaken van een beheerde identiteit (voorheen Managed Service Identity of MSI), in plaats van referenties of geheimen. Azure beheert deze identiteit voor u en helpt u bij het beveiligen van uw referenties omdat u geen geheimen hoeft op te geven of te wisselen.

Elk aanvraag eindpunt op een logische app heeft een Shared Access Signature (SAS) in de URL van het eind punt. Als u de eind punt-URL voor een op aanvragen gebaseerde trigger met andere partijen deelt, kunt u Url's voor terugbellen genereren die specifieke sleutels gebruiken en verloop datums hebben. Op die manier kunt u sleutels naadloos draaien of de toegang beperken tot het activeren van uw logische app op basis van een specifieke tijds duur.

- [Een directory-rol verkrijgen in azure AD met Power shell](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [Toegang tot Azure-bronnen verifiëren door beheerde identiteiten te gebruiken in Azure Logic Apps](create-managed-service-identity.md)

- [Leden van een directory-rol in azure AD ophalen met Power shell](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

- [Toegang en gegevens beveiligen in Azure Logic Apps met behulp van SAS](logic-apps-securing-a-logic-app.md#sas)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="32-change-default-passwords-where-applicable"></a>3,2: standaard wachtwoorden wijzigen indien van toepassing

**Hulp**: Azure Active Directory en Azure Logic apps hebben geen standaard wachtwoorden.

Als basis verificatie wordt gebruikt, moet u een gebruikers naam en wacht woord opgeven. Wanneer u deze referenties maakt, moet u ervoor zorgen dat u een sterk wacht woord configureert voor authenticatie.

Als u gebruikmaakt van een infra structuur als code, kunt u wacht woorden beter niet opslaan in code en in plaats daarvan Azure Key Vault gebruiken om referenties op te slaan en op te halen.

- [Gegevens beveiligen en openen in Logic Apps](logic-apps-securing-a-logic-app.md)

- [Een geheim instellen en ophalen uit Azure Key Vault](../key-vault/general/quick-create-portal.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: speciale beheerders accounts gebruiken

**Richt lijnen**: Maak standaard procedures voor het gebruik van specifieke beheerders accounts. Gebruik Azure Security Center identiteits-en toegangs beheer om het aantal beheerders accounts te bewaken.

Daarnaast kunt u aanbevelingen van Azure Security Center of ingebouwde Azure-beleids regels gebruiken om u te helpen bij het bijhouden van specifieke beheerders accounts, zoals:

- Er moet meer dan één eigenaar zijn toegewezen aan uw abonnement
- Afgeschafte accounts met eigenaarsmachtigingen moeten worden verwijderd uit uw abonnement
- Externe accounts met eigenaarsmachtigingen moeten worden verwijderd uit uw abonnement

- [Azure Security Center gebruiken om identiteit en toegang te bewaken (preview)](../security-center/security-center-identity-access.md)

- [Azure Policy gebruiken](../governance/policy/tutorials/create-and-manage.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: gebruik Azure Active Directory eenmalige aanmelding (SSO)

**Richt lijnen**: gebruik een Azure-app-registratie (Service-Principal) om een token op te halen dat kan worden gebruikt om te communiceren met uw Recovery Services KLUIZEN via API-aanroepen.

Voor veel connectors moet u eerst een verbinding met de doel service of het systeem maken en verificatie referenties of andere configuratie gegevens opgeven voordat u een trigger of actie in uw logische app kunt gebruiken. U moet bijvoorbeeld een verbinding met een Twitter-account voor toegang tot gegevens toestaan of namens u posten.

Voor connectors die gebruikmaken van Azure Active Directory (Azure AD) OAuth, wordt een verbinding gemaakt bij het aanmelden bij de service, zoals Office 365, Sales Force of GitHub, waarbij uw toegangs token is versleuteld en veilig wordt opgeslagen in een Azure-geheim archief. Andere connectors, zoals FTP en SQL, vereisen een verbinding met configuratie details, zoals het server adres, de gebruikers naam en het wacht woord. Deze gegevens over de configuratie van de verbinding zijn ook versleuteld en veilig opgeslagen.

- [Azure REST-Api's aanroepen](/rest/api/azure/#how-to-call-azure-rest-apis-with-postman)

- [Uw client toepassing registreren bij Azure AD](/rest/api/azure/#register-your-client-application-with-azure-ad)

- [API-informatie voor werk stroom triggers](/rest/api/logic/workflowtriggers)

- [Connector configuratie begrijpen](../connectors/apis-list.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: multi-factor Authentication gebruiken voor alle op Azure Active Directory gebaseerde toegang

**Hulp**: Schakel Azure Active Directory (AD) multi-factor Authentication (MFA) in en volg Azure Security Center aanbevelingen voor identiteits-en toegangs beheer.

- [MFA inschakelen in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Identiteit en toegang bewaken in Azure Security Center](../security-center/security-center-identity-access.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3,6: beveiligde, door Azure beheerde werk stations gebruiken voor beheer taken

**Hulp**: gebruik paw (privileged Access workstations) met multi-factor Authentication (MFA) die zijn geconfigureerd voor aanmelding bij en configureren van Azure-resources.

- [Meer informatie over privileged Access workstations](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [MFA inschakelen in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: Logboeken en waarschuwingen voor verdachte activiteiten van beheerders accounts

**Hulp**: gebruik Azure Active Directory (AD) PRIVILEGED Identity Management (PIM) voor het genereren van Logboeken en waarschuwingen wanneer verdachte of onveilige activiteiten in de omgeving worden uitgevoerd.

Daarnaast kunt u met Azure AD-risico detectie waarschuwingen en rapporten bekijken over Risk ante gebruikers gedrag.

- [Privileged Identity Management implementeren (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Meer informatie over Azure AD-risico detectie](../active-directory/identity-protection/overview-identity-protection.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: Azure-resources alleen beheren vanaf goedgekeurde locaties

**Hulp**: gebruik benoemde locaties voor voorwaardelijke toegang om alleen toegang toe te staan tot de Azure Portal vanuit specifieke logische groepen met IP-adresbereiken of landen/regio's.

Daarnaast heeft elk aanvraag eindpunt op een logische app een Shared Access Signature (SAS) in de URL van het eind punt. U kunt uw logische app beperken tot het accepteren van aanvragen van bepaalde IP-adressen.

- [Benoemde locaties configureren in azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

- [Meer informatie over het beperken van inkomende IP-adressen in Logic Apps](logic-apps-securing-a-logic-app.md#restrict-inbound-ip-addresses)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="39-use-azure-active-directory"></a>3,9: Azure Active Directory gebruiken

**Hulp**: gebruik Azure Active Directory (AD) als centrale verificatie-en autorisatie systeem voor uw Azure Logic apps-instanties. Azure AD beveiligt gegevens door gebruik te maken van sterke versleuteling voor gegevens in rust en onderweg. Azure AD bevat ook zouten, hashes en veilige gebruikers referenties.

Als er ondersteuning is voor Logic Apps, gebruikt u een beheerde identiteit om eenvoudig toegang te krijgen tot andere bronnen die worden beveiligd door Azure Active Directory (Azure AD) en uw identiteit te verifiëren zonder dat u zich hoeft aan te melden, in plaats van referenties of geheimen. Azure beheert deze identiteit voor u en helpt u bij het beveiligen van uw referenties omdat u geen geheimen hoeft op te geven of te wisselen.

Azure Logic Apps ondersteunt zowel door het systeem toegewezen als door de gebruiker toegewezen beheerde identiteiten. Uw logische app kan gebruikmaken van de door het systeem toegewezen identiteit of één door de gebruiker toegewezen identiteit, die u kunt delen via een groep logische apps, maar niet van beide. Momenteel ondersteunen alleen specifieke ingebouwde triggers en acties beheerde identiteiten, niet beheerde connectors of verbindingen, bijvoorbeeld:

-  HTTP

-  Azure Functions

-  Azure API Management

-  Azure App Services 

- [Een Azure AD-instantie maken en configureren](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [Toegang tot Azure-bronnen verifiëren door beheerde identiteiten te gebruiken in Azure Logic Apps](create-managed-service-identity.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: regel matig gebruikers toegang controleren en afstemmen

**Hulp**: Azure Active Directory (AD) bevat logboeken waarmee u verouderde accounts kunt detecteren. Daarnaast kunt u Azure Identity Access revisies gebruiken om groepslid maatschappen en de toegang tot bedrijfs toepassingen en roltoewijzingen op efficiënte wijze te beheren. Gebruikers toegang kan regel matig worden gecontroleerd om ervoor te zorgen dat alleen de juiste gebruikers toegang hebben. 

- [Meer informatie over Azure AD-rapportage](../active-directory/reports-monitoring/index.yml)

- [Beoordelingen over Azure Identity Access gebruiken](../active-directory/governance/access-reviews-overview.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: controle pogingen om toegang te krijgen tot gedeactiveerde referenties

**Hulp**: gebruik Azure Active Directory (AD) als centrale verificatie-en autorisatie systeem voor uw Azure Logic apps-instanties. Azure AD beveiligt gegevens door gebruik te maken van sterke versleuteling voor gegevens in rust en onderweg. Azure AD bevat ook zouten, hashes en veilige gebruikers referenties.

U hebt toegang tot de Azure AD-aanmeldings activiteit, de controle-en risico gebeurtenis logboek bronnen, waarmee u kunt integreren met Azure Sentinel of een SIEM van derden.

U kunt dit proces stroom lijnen door Diagnostische instellingen voor Azure AD-gebruikers accounts te maken en de audit logboeken en aanmeldings logboeken te verzenden naar een Log Analytics-werk ruimte. U kunt de gewenste logboek waarschuwingen configureren in Log Analytics.

- [Azure-activiteitenlogboeken integreren in Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Azure-Sentinel aan de trein](../sentinel/quickstart-onboard.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: waarschuwing voor de afwijking van het aanmeldings gedrag van het account

**Richt lijnen**: Azure AD-functies voor risico-en identiteits beveiliging gebruiken om automatische antwoorden te configureren op gedetecteerde verdachte acties die betrekking hebben op gebruikers identiteiten. U kunt ook gegevens opnemen in azure Sentinel voor verder onderzoek. 

- [Riskante Azure AD-aanmeldingen weergeven](../active-directory/identity-protection/overview-identity-protection.md) 

- [Risico beleid voor identiteits beveiliging configureren en inschakelen](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md) 

- [Azure-Sentinel onboarden](../sentinel/quickstart-onboard.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: micro soft biedt toegang tot relevante klant gegevens tijdens ondersteunings scenario's

**Hulp**: momenteel niet beschikbaar; Klanten-lockbox wordt nog niet ondersteund voor Azure Logic Apps.

- [Lijst met door Klanten-lockbox ondersteunde services](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

## <a name="data-protection"></a>Gegevensbeveiliging

*Zie [Azure Security Bench Mark: Data Protection](../security/benchmarks/security-control-data-protection.md)voor meer informatie.*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: een inventaris van gevoelige informatie onderhouden

**Hulp**: Tags gebruiken om Azure-resources te helpen bij het bijhouden of verwerken van gevoelige informatie.

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: systemen isoleren die gevoelige informatie opslaan of verwerken

**Hulp**: Connect oren die worden uitgevoerd in de ' wereld wijde ' multi tenant-Logic apps-service, worden geïmplementeerd en beheerd door micro soft. Deze connectors bieden triggers en acties voor toegang tot Cloud Services, on-premises systemen of beide, waaronder Office 365, Azure Blob Storage, SQL Server, Dynamics, Sales Force, share point en meer.

Voor Logic apps die directe toegang tot resources in een virtueel Azure-netwerk nodig hebben, kunt u een ISE (Integration service Environment) maken waarin u uw Logic apps op toegewezen bronnen kunt bouwen, implementeren en uitvoeren. Sommige virtuele netwerken van Azure gebruiken privé-eind punten (persoonlijke Azure-koppeling) om toegang te bieden tot Azure PaaS-Services, zoals Azure Storage, Azure Cosmos DB of Azure SQL Database, partner services of klanten services die worden gehost op Azure. Als uw Logic apps toegang nodig hebben tot virtuele netwerken die gebruikmaken van privé-eind punten, moet u deze Logic apps maken, implementeren en uitvoeren in een ISE.

Wanneer u uw ISE maakt, kunt u kiezen of u interne of Externe toegangs punten wilt gebruiken. Uw selectie bepaalt of aanvragen of webhooks worden geactiveerd op Logic apps in uw ISE kan aanroepen ontvangen van buiten uw virtuele netwerk.

Daarnaast kunt u isolatie implementeren met afzonderlijke abonnementen en beheer groepen voor afzonderlijke beveiligings domeinen, zoals omgevings type en gegevens gevoeligheids niveau. U kunt het toegangs niveau voor uw Azure-resources beperken die worden vereist door uw toepassingen en bedrijfs omgevingen. U kunt de toegang tot Azure-resources beheren via Azure op rollen gebaseerd toegangs beheer (Azure RBAC).

- [Connectors voor Logic Apps](../connectors/apis-list.md)

- [Toegang tot Azure Virtual Network resources vanuit Azure Logic Apps met behulp van integratie service omgevingen (ISEs)](connect-virtual-network-vnet-isolated-environment-overview.md)

- [Aanvullende Azure-abonnementen maken](../cost-management-billing/manage/create-subscription.md) 

- [Beheergroepen maken](../governance/management-groups/create-management-group-portal.md) 

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: niet-geautoriseerde overdracht van gevoelige gegevens controleren en blok keren

**Hulp**: momenteel niet beschikbaar; de functies voor gegevens identificatie, classificatie en verlies preventie zijn nog niet beschikbaar voor Azure Logic Apps.

Maak gebruik van een oplossing van een derde partij in azure Marketplace op netwerk verbindingen die monitors voor niet-geautoriseerde overdracht van gevoelige informatie en blok keren van dergelijke overdrachten tijdens het waarschuwen van informatie beveiliging. 

Micro soft beheert de onderliggende infra structuur voor Azure Logic Apps en heeft strikte controles geïmplementeerd om verlies of bloot stelling van klant gegevens te voor komen.

- [Informatie over beveiliging van klantgegevens in Azure](../security/fundamentals/protection-customer-data.md)

**Monitoring door Azure Security Center**: Niet van toepassing

**Verantwoordelijkheid**: Gedeeld

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: alle gevoelige gegevens in de overdracht versleutelen

**Hulp**: alle gevoelige gegevens in de overdracht versleutelen. In Azure Logic Apps worden alle gegevens tijdens de uitvoering van een logische app versleuteld tijdens de overdracht met behulp van Transport Layer Security (TLS) en in rust. Wanneer u de uitvoerings geschiedenis van de logische app bekijkt, wordt uw toegang door Logic Apps geverifieerd en vindt u koppelingen naar de invoer en uitvoer voor de aanvragen en antwoorden voor elke uitvoering. Voor acties waarbij wacht woorden, geheimen, sleutels of andere gevoelige informatie worden verwerkt, wilt u echter voor komen dat anderen deze gegevens kunnen weer geven en gebruiken. Als uw logische app bijvoorbeeld een geheim krijgt van Azure Key Vault om te gebruiken bij het verifiëren van een HTTP-actie, wilt u dat geheim verbergen in de weer gave.

De trigger voor aanvragen ondersteunt alleen Transport Layer Security (TLS) 1,2 voor inkomende aanvragen. Zorg ervoor dat alle clients die verbinding maken met uw Azure-resources, TLS 1,2 of hoger kunnen onderhandelen. Uitgaande oproepen die gebruikmaken van de HTTP-connector ondersteuning Transport Layer Security (TLS) 1,0, 1,1 en 1,2. 

Volg Azure Security Center aanbevelingen voor het versleutelen van de rest en de versleuteling in de door Voer, indien van toepassing.

- [Beveiligde toegang en gegevens in Azure Logic Apps-inkomende oproepen naar activeringen op basis van een aanvraag](logic-apps-securing-a-logic-app.md#secure-inbound-requests)

- [Veilige toegang en gegevens in Azure Logic Apps-uitgaande oproepen naar andere services en systemen](logic-apps-securing-a-logic-app.md#secure-outbound-requests)

- [Meer informatie over versleuteling in transit met Azure](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)

- [Meer informatie over gegevens versleuteling-at-rest met Azure](../security/fundamentals/encryption-atrest.md)

- [Door de klant beheerde sleutels instellen om gegevens in rust te versleutelen voor integratie service omgevingen (ISEs) in Azure Logic Apps](customer-managed-keys-integration-service-environment.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Gedeeld

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: een actief detectie hulpprogramma gebruiken om gevoelige gegevens te identificeren

**Richt lijnen**: in azure Logic apps hebben veel triggers en acties instellingen die u kunt inschakelen voor het beveiligen van invoer, uitvoer of beide door te voor komen dat deze gegevens van de uitvoerings geschiedenis van een logische app worden verborgen.

Micro soft beheert de onderliggende infra structuur voor Azure Logic Apps en heeft strikte controles geïmplementeerd om verlies of bloot stelling van klant gegevens te voor komen.

- [Beveiligde toegang tot uitvoerings geschiedenis gegevens](logic-apps-securing-a-logic-app.md#access-to-run-history-data)

- [Informatie over beveiliging van klantgegevens in Azure](../security/fundamentals/protection-customer-data.md)

**Monitoring door Azure Security Center**: Niet van toepassing

**Verantwoordelijkheid**: Gedeeld

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: Azure RBAC gebruiken om de toegang tot resources te beheren

**Hulp**: u kunt alleen specifieke gebruikers of groepen toestaan specifieke taken uit te voeren, zoals het beheren, bewerken en weer geven van logische apps. Als u de machtigingen wilt beheren, gebruikt u Azure RBAC (op rollen gebaseerd toegangs beheer), zodat u aangepaste of ingebouwde rollen kunt toewijzen aan de leden van uw Azure-abonnement:

- Inzender van Logic apps: Hiermee kunt u logische apps beheren, maar u kunt de toegang niet wijzigen.
- Logische app-operator: Hiermee kunt u logische apps lezen, inschakelen en uitschakelen, maar u kunt ze niet bewerken of bijwerken.

U kunt Azure resource Lock gebruiken om te voor komen dat anderen uw logische app wijzigen of verwijderen. Deze mogelijkheid voor komt dat anderen productie resources wijzigen of verwijderen.

- [Veilige toegang tot Azure Logic Apps bewerkingen](logic-apps-securing-a-logic-app.md#access-to-logic-app-operations)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: voor komen dat gegevens verlies op basis van host wordt gebruikt voor het afdwingen van toegangs beheer

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources. Micro soft beheert de onderliggende infra structuur voor Azure Logic Apps en heeft strikte controles geïmplementeerd om verlies of bloot stelling van klant gegevens te voor komen.

- [Azure-klant gegevens beveiliging](../security/fundamentals/protection-customer-data.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: micro soft

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: gevoelige informatie op rest versleutelen

**Hulp**: Azure Logic apps is afhankelijk van Azure Storage om gegevens in rust op te slaan en automatisch te versleutelen. Deze versleuteling beveiligt uw gegevens en helpt u te voldoen aan de verplichtingen voor beveiliging en naleving van uw organisatie. Azure Storage maakt standaard gebruik van door micro soft beheerde sleutels om uw gegevens te versleutelen.

Wanneer u een Integration service Environment (ISE) maakt voor het hosten van uw logische apps en u meer controle wilt hebben over de versleutelings sleutels die worden gebruikt door Azure Storage, kunt u uw eigen sleutel instellen, gebruiken en beheren met behulp van Azure Key Vault. Deze mogelijkheid is ook bekend als ' Bring Your Own Key ' (BYOK) en uw sleutel wordt een ' door de klant beheerde sleutel ' genoemd.

- [Gegevens in rust versleutelen voor integratie service omgevingen in Azure Logic Apps](customer-managed-keys-integration-service-environment.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: wijzigingen in essentiële Azure-resources vastleggen en waarschuwen

**Hulp**: gebruik Azure monitor met het Azure-activiteiten logboek om waarschuwingen te maken voor wanneer wijzigingen worden aangebracht in azure Logic apps, evenals andere essentiële of gerelateerde resources.

- [Waarschuwingen maken voor gebeurtenissen in het Azure-activiteiten logboek](../azure-monitor/alerts/alerts-activity-log.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

## <a name="vulnerability-management"></a>Beheer van beveiligingsproblemen

*Zie voor meer informatie de [Azure Security Bench Mark: beveiligingslek beheer](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: automatische hulpprogram ma's voor het scannen van beveiligings problemen uitvoeren

**Richt lijnen**: niet van toepassing; Micro soft voert beveiligings beheer uit op de onderliggende systemen die ondersteuning bieden voor Azure Logic Apps.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: geautomatiseerde oplossing voor patch beheer voor besturings systemen implementeren

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: Implementeer een oplossing voor geautomatiseerd patch beheer voor software titels van derden

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: vergelijken van back-to-back-problemen

**Richt lijnen**: niet van toepassing; Micro soft voert beveiligings beheer uit op de onderliggende systemen die ondersteuning bieden voor Azure Logic Apps.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: een risico classificatie proces gebruiken om prioriteit te geven aan het herstel van ontdekte beveiligings problemen

**Richt lijnen**: niet van toepassing; Micro soft voert beveiligings beheer uit op de onderliggende systemen die ondersteuning bieden voor Azure Logic Apps.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

## <a name="inventory-and-asset-management"></a>Inventarisatie en asset-management

*Zie [Azure Security Bench Mark: Inventory and Asset Management](../security/benchmarks/security-control-inventory-asset-management.md)voor meer informatie.*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: automatische Asset-detectie oplossing gebruiken

**Hulp**: Azure resource Graph gebruiken voor het opvragen/detecteren van alle resources (zoals compute, opslag, netwerk, poorten en protocollen enz.) binnen uw abonnement (en).  Zorg ervoor dat de juiste (Lees-) machtigingen voor uw Tenant en alle Azure-abonnementen en resources in uw abonnementen inventariseren.

Hoewel klassieke Azure-resources kunnen worden gedetecteerd via resource grafiek, is het raadzaam om Azure Resource Manager resources te maken en te gebruiken.

- [Query's maken met Azure resource Graph](../governance/resource-graph/first-query-portal.md)

- [Uw Azure-abonnementen weer geven](/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

- [Meer informatie over Azure RBAC](../role-based-access-control/overview.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="62-maintain-asset-metadata"></a>6,2: meta gegevens van activa onderhouden

**Richt lijnen**: Tags Toep assen op Azure-resources die meta gegevens geven om ze logisch in een taxonomie te organiseren.

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: niet-geautoriseerde Azure-resources verwijderen

**Richt lijnen**: Gebruik labels, beheer groepen en afzonderlijke abonnementen, waar nodig, om Azure-resources te organiseren en bij te houden. Sluit de inventaris regel matig af en zorg ervoor dat niet-geautoriseerde resources tijdig worden verwijderd uit het abonnement.

Gebruik Azure Policy bovendien om beperkingen te leggen voor het type resources dat kan worden gemaakt in klant abonnementen met behulp van de volgende ingebouwde beleids definities:

- Niet toegestane resourcetypen
- Toegestane brontypen

- [Aanvullende Azure-abonnementen maken](../cost-management-billing/manage/create-subscription.md)

- [Beheergroepen maken](../governance/management-groups/create-management-group-portal.md)

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: de inventaris van goedgekeurde Azure-resources definiëren en onderhouden

**Hulp**: Maak een inventaris van goedgekeurde Azure-resources (zoals connectors) en goedgekeurde software voor reken bronnen conform uw organisatie behoeften.

Opmerking: als gevolg van het gegevens-en privacybeleid van Google kunt u de Gmail-connector alleen gebruiken met door Google goedgekeurde Services. Deze situatie is in ontwikkeling en kan van invloed zijn op andere Google-connectors in de toekomst.

- [Lijst met alle Logic Apps-connectors](/connectors/connector-reference/connector-reference-logicapps-connectors)

- [Problemen en beperkingen voor Gmail-connectors begrijpen](/connectors/gmail/#known-issues-and-limitations)

- [Meer informatie over het privacybeleid van Google](../connectors/connectors-google-data-security-privacy-policy.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: monitor voor niet-goedgekeurde Azure-resources

**Hulp**: gebruik Azure Policy om beperkingen toe te voegen voor het type resources dat kan worden gemaakt in uw abonnement (en). 

Gebruik Azure resource Graph voor het opvragen/detecteren van resources binnen hun abonnement (en).  Zorg ervoor dat alle Azure-resources die aanwezig zijn in de omgeving, zijn goedgekeurd.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Query's maken met Azure resource Graph](../governance/resource-graph/first-query-portal.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: monitor voor niet-goedgekeurde software toepassingen binnen reken resources

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: niet-goedgekeurde Azure-resources en software toepassingen verwijderen

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="68-use-only-approved-applications"></a>6,8: alleen goedgekeurde toepassingen gebruiken

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="69-use-only-approved-azure-services"></a>6,9: alleen goedgekeurde Azure-Services gebruiken

**Hulp: gebruik** Azure Policy om beperkingen toe te voegen voor het type resources dat kan worden gemaakt in klant abonnement (en) met de volgende ingebouwde beleids definities:

- Niet toegestane resourcetypen
- Toegestane brontypen

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Een specifiek resource type weigeren met Azure Policy](../governance/policy/samples/index.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: een inventaris van goedgekeurde software titels onderhouden

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: de mogelijkheid van gebruikers om te communiceren met Azure Resource Manager beperken

**Hulp** bij het configureren van voorwaardelijke toegang van Azure om gebruikers de mogelijkheid te bieden om te communiceren met Azure Resource Manager door ' blok toegang ' te configureren voor de app Microsoft Azure management.

- [Voorwaardelijke toegang configureren om de toegang tot Azure Resource Manager te blok keren](../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: de mogelijkheid van gebruikers om scripts uit te voeren binnen reken bronnen beperken

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: toepassingen met een hoog risico fysiek of logisch scheiden

**Hulp**: resources die betrekking hebben op uw Logic apps die vereist zijn voor bedrijfs activiteiten, maar die een hoger risico voor de organisatie kunnen opleveren, moeten worden geïsoleerd in een eigen virtuele machine en/of virtueel netwerk en voldoende zijn beveiligd met een Azure firewall of netwerk beveiligings groep.

Logic Apps die vereist zijn voor bedrijfs activiteiten, maar een groter risico kunnen opleveren voor de organisatie, moeten waar mogelijk worden geïsoleerd via afzonderlijke resource groepen met specifieke machtigingen en Azure RBAC-grenzen.

- [Een virtueel netwerk maken](../virtual-network/quick-create-portal.md) 

- [Een NSG maken met een beveiligings configuratie](../virtual-network/tutorial-filter-network-traffic.md)

- [Beheergroepen maken](../governance/management-groups/create-management-group-portal.md) 

- [Toegang tot Logic Apps beveiligen via Azure RBAC](logic-apps-securing-a-logic-app.md#access-to-logic-app-operations)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="secure-configuration"></a>Veilige configuratie

*Zie [Azure Security Bench Mark: Secure Configuration](../security/benchmarks/security-control-secure-configuration.md)(Engelstalig) voor meer informatie.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: veilige configuraties instellen voor alle Azure-resources

**Richt lijnen**: standaard beveiligings configuraties voor uw Azure Logic apps-instanties definiëren en implementeren met Azure Policy. Gebruik Azure Policy aliassen in de naam ruimte ' micro soft. Logic ' om aangepaste beleids regels te maken om de configuratie van uw Logic Apps instanties te controleren of af te dwingen. U kunt bijvoorbeeld voor komen dat anderen verbindingen met resources maken of gebruiken waarvoor u de toegang wilt beperken.

Daarnaast heeft Azure Resource Manager de mogelijkheid om de sjabloon in JavaScript Object Notation (JSON) te exporteren, die moet worden gecontroleerd om ervoor te zorgen dat de configuraties voldoen aan de beveiligings vereisten voor uw organisatie.

Gebruik ook beveiligde para meters voor het beveiligen van gevoelige gegevens en geheimen.

- [Beschik bare Azure Policy aliassen weer geven](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Verbindingen die zijn gemaakt door connectors in Azure Logic Apps blok keren](block-connections-connectors.md)

- [Eén en meerdere resources exporteren naar een sjabloon in Azure Portal](../azure-resource-manager/templates/export-template-portal.md)

- [Azure Resource Manager sjablonen implementeren voor Azure Logic Apps](logic-apps-deploy-azure-resource-manager-templates.md)

- [Informatie over veilige actie parameters](logic-apps-securing-a-logic-app.md#secure-action-parameters)

- [Beveiligings aanbevelingen voor para meters](../azure-resource-manager/templates/template-best-practices.md#security-recommendations-for-parameters)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: veilige configuraties van besturings systemen instellen

**Richt lijnen**: niet van toepassing; deze richt lijn is bedoeld voor reken resources.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: Beveilig Azure-resource configuraties onderhouden

**Hulp**: gebruik Azure Policy [deny] en [implementeren indien niet aanwezig] voor het afdwingen van beveiligde instellingen voor uw Azure-resources.

Definieer en implementeer standaard beveiligings configuraties voor uw Azure Logic Apps-instanties met Azure Policy. Gebruik Azure Policy aliassen in de naam ruimte ' micro soft. Logic ' om aangepaste beleids regels te maken om de configuratie van uw Logic Apps instanties te controleren of af te dwingen. U kunt bijvoorbeeld voor komen dat anderen verbindingen met resources maken of gebruiken waarvoor u de toegang wilt beperken.

Daarnaast heeft Azure Resource Manager de mogelijkheid om de sjabloon in JavaScript Object Notation (JSON) te exporteren, die moet worden gecontroleerd om ervoor te zorgen dat de configuraties voldoen aan de beveiligings vereisten voor uw organisatie.

Zorg er ook voor dat u gegevens in de uitvoerings geschiedenis beveiligt met behulp van een afkorting.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy effecten begrijpen](../governance/policy/concepts/effects.md)

- [Verbindingen die zijn gemaakt door connectors in Azure Logic Apps blok keren](block-connections-connectors.md)

- [Eén en meerdere resources exporteren naar een sjabloon in Azure Portal](../azure-resource-manager/templates/export-template-portal.md)

- [Azure Resource Manager sjablonen implementeren voor Azure Logic Apps](logic-apps-deploy-azure-resource-manager-templates.md)

- [Veilige toegang voor invoer en uitvoer van de uitvoering van de geschiedenis](logic-apps-securing-a-logic-app.md#obfuscate)

- [Veilige toegang tot parameter invoer](logic-apps-securing-a-logic-app.md#secure-action-parameters)

- [Beveiligings aanbevelingen voor para meters](../azure-resource-manager/templates/template-best-practices.md#security-recommendations-for-parameters)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: veilige configuraties van besturings systemen onderhouden

**Richt lijnen**: niet van toepassing; deze richt lijn is bedoeld voor reken resources.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Gedeeld

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: de configuratie van Azure-resources veilig opslaan

**Richt lijnen**: als u aangepaste Azure Policy definities gebruikt, kunt u Azure DevOps of Azure opslag plaatsen gebruiken om uw code veilig op te slaan en te beheren.

Daarnaast heeft Azure Resource Manager de mogelijkheid om de sjabloon in JavaScript Object Notation (JSON) te exporteren, die moet worden gecontroleerd om ervoor te zorgen dat de configuraties voldoen aan de beveiligings vereisten voor uw organisatie.

- [Code opslaan in azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Documentatie voor Azure opslag plaatsen](/azure/devops/repos/index?view=azure-devops)

- [Eén en meerdere resources exporteren naar een sjabloon in Azure Portal](../azure-resource-manager/templates/export-template-portal.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: aangepaste installatie kopieën van een besturings systeem veilig opslaan

**Richt lijnen**: niet van toepassing; deze richt lijn is bedoeld voor reken resources.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: hulpprogram ma's voor configuratie beheer voor Azure-resources implementeren

**Hulp**: gebruik ingebouwde Azure Policy definities en Azure Policy aliassen in de naam ruimte ' micro soft. Logic ' om aangepaste beleids regels te maken om systeem configuraties te Signa lering, te controleren en af te dwingen. Gebruik Azure Policy aliassen om aangepaste beleids regels te maken om de netwerk configuratie van uw Azure-resources te controleren of af te dwingen. Ontwikkel bovendien een proces en pijp lijn voor het beheren van beleids uitzonderingen.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: hulpprogram ma's voor configuratie beheer voor besturings systemen implementeren

**Richt lijnen**: niet van toepassing; deze richt lijn is bedoeld voor reken resources.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: geautomatiseerde configuratie bewaking voor Azure-resources implementeren

**Hulp**: gebruik ingebouwde Azure Policy definities en Azure Policy aliassen in de naam ruimte ' micro soft. Logic ' om aangepaste beleids regels te maken om systeem configuraties te Signa lering, te controleren en af te dwingen. Gebruik Azure Policy [audit], [deny] en [implementeren indien niet aanwezig] om automatisch configuraties af te dwingen voor uw Azure-resources.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: geautomatiseerde configuratie bewaking voor besturings systemen implementeren

**Richt lijnen**: niet van toepassing; deze richt lijn is bedoeld voor reken resources.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="711-manage-azure-secrets-securely"></a>7,11: Azure-geheimen veilig beheren

**Hulp**: Beveilig invoer en uitvoer in de geschiedenis van de logische app met behulp van een afleiding. Als u in verschillende omgevingen implementeert, kunt u de waarden in de werk stroom definitie van de logische app parameterizingen die variëren op basis van deze omgevingen. Op die manier kunt u hardcoded gegevens voor komen door een Azure Resource Manager sjabloon te gebruiken om uw logische app te implementeren, gevoelige gegevens te beschermen door beveiligde para meters te definiëren en deze gegevens als afzonderlijke invoer door te geven via de para meters van de sjabloon met behulp van een parameter bestand. U kunt Key Vault gebruiken om gevoelige gegevens op te slaan en beveiligde sjabloon parameters te gebruiken waarmee deze waarden worden opgehaald uit Key Vault tijdens de implementatie. Vervolgens kunt u verwijzen naar de sleutel kluis en geheimen in uw parameter bestand. 

Wanneer u een Integration service Environment (ISE) maakt voor het hosten van uw logische apps en u meer controle wilt hebben over de versleutelings sleutels die worden gebruikt door Azure Storage, kunt u uw eigen sleutel instellen, gebruiken en beheren met behulp van Azure Key Vault. Deze mogelijkheid is ook bekend als ' Bring Your Own Key ' (BYOK) en uw sleutel wordt een ' door de klant beheerde sleutel ' genoemd.

- [Invoer en uitvoer in de uitvoerings geschiedenis in Azure Logic Apps beveiligen](logic-apps-securing-a-logic-app.md#obfuscate)

- [Beveiligings aanbevelingen voor para meters](../azure-resource-manager/templates/template-best-practices.md#security-recommendations-for-parameters)

- [Veilige toegang tot para meter invoer in Azure Logic Apps](logic-apps-securing-a-logic-app.md#access-to-parameter-inputs)

- [Veilige parameter waarden door geven tijdens de implementatie met behulp van Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md)

- [Door de klant beheerde sleutels instellen om gegevens in rust te versleutelen voor integratie service omgevingen (ISEs) in Azure Logic Apps](customer-managed-keys-integration-service-environment.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: identiteiten veilig en automatisch beheren

**Richt lijnen**: als u eenvoudig toegang wilt krijgen tot andere bronnen die worden beveiligd door Azure Active Directory (Azure AD) en uw identiteit wilt verifiëren zonder u aan te melden, kan uw logische app gebruikmaken van een beheerde identiteit (voorheen Managed Service Identity of MSI), in plaats van referenties of geheimen. Azure beheert deze identiteit voor u en helpt u bij het beveiligen van uw referenties omdat u geen geheimen hoeft op te geven of te wisselen.

Momenteel ondersteunen alleen specifieke ingebouwde triggers en acties beheerde identiteiten, niet beheerde connectors of verbindingen, bijvoorbeeld:

- HTTP
- Azure Functions
- Azure API Management
- Azure App Services

- [Toegang tot Azure-bronnen verifiëren door beheerde identiteiten te gebruiken in Azure Logic Apps](create-managed-service-identity.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: onbedoelde referentie blootstelling elimineren

**Hulp**: Beveilig invoer en uitvoer in de geschiedenis van de logische app met behulp van een afleiding. Als u in verschillende omgevingen implementeert, kunt u de waarden in de werk stroom definitie van de logische app parameterizingen die variëren op basis van deze omgevingen. Op die manier kunt u hardcoded gegevens voor komen door een Azure Resource Manager sjabloon te gebruiken om uw logische app te implementeren, gevoelige gegevens te beschermen door beveiligde para meters te definiëren en deze gegevens als afzonderlijke invoer door te geven via de para meters van de sjabloon met behulp van een parameter bestand. U kunt Key Vault gebruiken om gevoelige gegevens op te slaan en beveiligde sjabloon parameters te gebruiken waarmee deze waarden worden opgehaald uit Key Vault tijdens de implementatie. Vervolgens kunt u verwijzen naar de sleutel kluis en geheimen in uw parameter bestand. 

U kunt referentie scanner ook implementeren om referenties in code te identificeren. Door het gebruik van Credential Scanner worden gebruikers ook aangemoedigd om gedetecteerde referenties naar veiligere locaties, zoals Azure Key Vault, te verplaatsen. 

- [Invoer en uitvoer in de uitvoerings geschiedenis in Azure Logic Apps beveiligen](logic-apps-securing-a-logic-app.md#obfuscate)

- [Beveiligings aanbevelingen voor para meters](../azure-resource-manager/templates/template-best-practices.md#security-recommendations-for-parameters)

- [Veilige toegang tot para meter invoer in Azure Logic Apps](logic-apps-securing-a-logic-app.md#access-to-parameter-inputs)

- [Veilige parameter waarden door geven tijdens de implementatie met behulp van Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md)

- [Referentie scanner instellen](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="malware-defense"></a>Beveiliging tegen malware

*Zie voor meer informatie de [Azure Security Bench Mark: beveiliging tegen schadelijke software](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: centraal beheerde anti-malware-software gebruiken

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources. Micro soft anti-malware is ingeschakeld op de onderliggende host die ondersteuning biedt voor Azure-Services (bijvoorbeeld Azure Logic Apps), maar wordt niet uitgevoerd op de inhoud van de klant.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: scan bestanden die moeten worden geüpload naar niet-reken resources van Azure

**Hulp**: micro soft anti-malware is ingeschakeld op de onderliggende host die ondersteuning biedt voor Azure-Services (bijvoorbeeld Azure backup), maar wordt niet uitgevoerd voor uw inhoud. 

Scan vooraf bestanden die worden geüpload naar niet-reken resources van Azure, zoals App Service, Data Lake Storage, Blob Storage, enzovoort. 

Gebruik Azure Security Center bedreigings detectie voor gegevens Services om malware te detecteren die is geüpload naar opslag accounts. 

- [Meer informatie over micro soft anti-malware voor Azure Cloud Services en Virtual Machines](../security/fundamentals/antimalware.md)

- [Meer informatie over de detectie van bedreigingen van Azure Security Center voor gegevens Services](../security-center/azure-defender.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: controleren of anti-malware-software en hand tekeningen zijn bijgewerkt

**Richt lijnen**: niet van toepassing; deze richt lijn is bedoeld voor reken resources.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="data-recovery"></a>Gegevensherstel

*Zie [Azure Security Bench Mark: Data Recovery](../security/benchmarks/security-control-data-recovery.md)(Engelstalig) voor meer informatie.*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: zorg voor regel matige automatische back-ups

**Hulp** bij het implementeren van een oplossing voor herstel na nood geval, zodat u gegevens kunt beveiligen, snel de resources moet herstellen die essentiële bedrijfs functies ondersteunen en bewerkingen blijven uitvoeren om bedrijfs CONTINUÏTEIT (BC) te onderhouden.

Deze strategie voor herstel na nood gevallen is gericht op het instellen van uw primaire logische app voor failover naar een stand-by-of back-uplogische app op een alternatieve locatie waar Azure Logic Apps ook beschikbaar is. Op die manier kunt u, als de primaire verliezen, onderbrekingen of storingen lijden, de secundaire op het werk uitvoeren. Deze strategie vereist dat uw secundaire logische app en afhankelijke bronnen al zijn geïmplementeerd en gereed zijn op de alternatieve locatie.

Bovendien moet u de onderliggende werk stroom definitie van uw logische app uitbreiden naar een Azure Resource Manager sjabloon. Met deze sjabloon worden de infra structuur, bronnen, para meters en andere gegevens gedefinieerd voor het inrichten en implementeren van uw logische app.

- [Meer informatie over bedrijfs continuïteit en herstel na nood gevallen voor Azure Logic Apps](business-continuity-disaster-recovery-guidance.md)

- [Implementatie voor Azure Logic Apps automatiseren met behulp van Azure Resource Manager sjablonen](logic-apps-azure-resource-manager-templates-overview.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: volledige back-ups van het systeem uitvoeren en een back-up maken van door de klant beheerde sleutels

**Hulp** bij het implementeren van een oplossing voor herstel na nood geval, zodat u gegevens kunt beveiligen, snel de resources moet herstellen die essentiële bedrijfs functies ondersteunen en bewerkingen blijven uitvoeren om bedrijfs CONTINUÏTEIT (BC) te onderhouden.

Deze strategie voor herstel na nood gevallen is gericht op het instellen van uw primaire logische app voor failover naar een stand-by-of back-uplogische app op een alternatieve locatie waar Azure Logic Apps ook beschikbaar is. Op die manier kunt u, als de primaire verliezen, onderbrekingen of storingen lijden, de secundaire op het werk uitvoeren. Deze strategie vereist dat uw secundaire logische app en afhankelijke bronnen al zijn geïmplementeerd en gereed zijn op de alternatieve locatie.

Bovendien moet u de onderliggende werk stroom definitie van uw logische app uitbreiden naar een Azure Resource Manager sjabloon. Met deze sjabloon worden de infra structuur, bronnen, para meters en andere gegevens gedefinieerd voor het inrichten en implementeren van uw logische app.

Elk aanvraag eindpunt op een logische app heeft een Shared Access Signature (SAS) in de URL van het eind punt. Als u Azure Key Vault gebruikt om uw geheimen op te slaan, moet u regel matig automatische back-ups maken van uw sleutels en Url's.

- [Meer informatie over bedrijfs continuïteit en herstel na nood gevallen voor Azure Logic Apps](business-continuity-disaster-recovery-guidance.md)

- [Implementatie voor Azure Logic Apps automatiseren door gebruik te maken van Azure Resource Manager sjablonen](logic-apps-azure-resource-manager-templates-overview.md)

- [Toegang en gegevens beveiligen in Azure Logic Apps met behulp van SAS](logic-apps-securing-a-logic-app.md#sas)

- [Back-ups maken van Key Vault sleutels](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: alle back-ups valideren, inclusief door de klant beheerde sleutels

**Richt lijnen**: uw strategie voor herstel na nood gevallen moet zich richten op het instellen van uw primaire logische app voor failover naar een stand-by-of back-uplogische app op een andere locatie waar Azure Logic apps ook beschikbaar is. Op die manier kunt u, als de primaire verliezen, onderbrekingen of storingen lijden, de secundaire op het werk uitvoeren. Deze strategie vereist dat uw secundaire logische app en afhankelijke bronnen al zijn geïmplementeerd en gereed zijn op de alternatieve locatie.

Het herstellen van een back-up van door de klant beheerde sleutels testen. Houd er rekening mee dat dit alleen van toepassing is op Logic Apps die worden uitgevoerd op ISE (Integration service environments).

- [Meer informatie over bedrijfs continuïteit en herstel na nood gevallen voor Azure Logic Apps](business-continuity-disaster-recovery-guidance.md)

- [Door de klant beheerde sleutels instellen om gegevens in rust te versleutelen voor integratie service omgevingen (ISEs) in Azure Logic Apps](customer-managed-keys-integration-service-environment.md)

- [Sleutel kluis sleutels herstellen in azure](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: zorg voor de bescherming van back-ups en door de klant beheerde sleutels

**Richt lijnen**: uw strategie voor herstel na nood gevallen moet zich richten op het instellen van uw primaire logische app voor failover naar een stand-by-of back-uplogische app op een andere locatie waar Azure Logic apps ook beschikbaar is. Op die manier kunt u, als de primaire verliezen, onderbrekingen of storingen lijden, de secundaire op het werk uitvoeren. Deze strategie vereist dat uw secundaire logische app en afhankelijke bronnen al zijn geïmplementeerd en gereed zijn op de alternatieve locatie. 

Beveilig back-ups van door de klant beheerde sleutels. Houd er rekening mee dat dit alleen van toepassing is op Logic Apps die worden uitgevoerd op ISE (Integration service environments).

Schakel Soft-Delete in en verwijder de beveiliging in Key Vault om sleutels te beschermen tegen onbedoelde of schadelijke verwijdering.

- [Meer informatie over bedrijfs continuïteit en herstel na nood gevallen voor Azure Logic Apps](business-continuity-disaster-recovery-guidance.md)

- [Door de klant beheerde sleutels instellen om gegevens in rust te versleutelen voor integratie service omgevingen (ISEs) in Azure Logic Apps](customer-managed-keys-integration-service-environment.md)

- [Soft-Delete inschakelen en beveiliging opschonen in Key Vault](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

## <a name="incident-response"></a>Reageren op incidenten

*Zie [Azure Security Bench Mark: Incident Response](../security/benchmarks/security-control-incident-response.md)(Engelstalig) voor meer informatie.*

### <a name="101-create-an-incident-response-guide"></a>10,1: een hand leiding voor reactie op incidenten maken

**Richtlijnen**: Stel voor uw organisatie een responshandleiding op voor gebruik bij incidenten. Zorg ervoor dat er schriftelijke responsplannen zijn waarin alle rollen van het personeel worden gedefinieerd, evenals alle fasen in het afhandelen/managen van incidenten, vanaf de detectie van het incident tot een evaluatie ervan achteraf. 

- [Richt lijnen voor het bouwen van uw eigen beveiligings incident antwoord proces](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Micro soft Security Response Center anatomie van een incident](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [De verwerkings handleiding voor het computer beveiligings incident van het NIST gebruiken om u te helpen bij het maken van uw eigen reactie plan voor incidenten](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: een beoordelings procedure voor incidenten en prioriteits procedures maken

**Hulp**: Security Center wijst aan elke waarschuwing een Ernst toe om u te helpen bepalen welke waarschuwingen het eerst moeten worden onderzocht. De ernst is gebaseerd op de manier waarop vertrouwen Security Center is in de zoek actie of het analyse programma dat wordt gebruikt om de waarschuwing te geven, evenals het betrouwbaarheids niveau dat er schadelijke bedoelingen zijn achter de activiteit die tot de waarschuwing heeft geleid. 

Daarnaast kunt u ook duidelijk abonnementen markeren (voor bijvoorbeeld productie, niet-productie) met behulp van tags en maak een naamgevings systeem om Azure-resources duidelijk te identificeren en te categoriseren, met name voor de verwerking van gevoelige gegevens.  Het is uw verantwoordelijkheid om prioriteit te geven aan het oplossen van waarschuwingen op basis van de ernst van de Azure-resources en -omgeving waarin het incident heeft plaatsgevonden.

- [Beveiligingswaarschuwingen in Azure Security Center](../security-center/security-center-alerts-overview.md)

- [Tags gebruiken om Azure-resources te organiseren](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="103-test-security-response-procedures"></a>10,3: procedures voor beveiligings antwoorden testen

**Richt lijnen**: oefent oefeningen uit om de respons mogelijkheden van uw systeem te testen op een reguliere uitgebracht om uw Azure-resources te beschermen. Stel vast waar zich zwakke plekken en hiaten bevinden, en wijzig zo nodig het plan.

- [Publicatie van het NIST-hand leiding voor test-, trainings-en oefen Programma's voor IT-plannen en-mogelijkheden](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: contact gegevens van het beveiligings incident opgeven en waarschuwings meldingen configureren voor beveiligings incidenten

**Hulp**: contact gegevens van beveiligings incidenten worden door micro soft gebruikt om contact met u op te nemen als het micro soft Security Response Center (MSRC) detecteert dat uw gegevens zijn geopend door een onrecht matige of niet-gemachtigde partij. Bekijk incidenten na het feit om te controleren of de problemen zijn opgelost.

- [De Azure Security Center Security-contact persoon instellen](../security-center/security-center-provide-security-contact-details.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: beveiligings waarschuwingen opnemen in uw reactie systeem van uw incident

**Hulp**: exporteer uw Azure Security Center waarschuwingen en aanbevelingen met behulp van de functie continue export om Risico's voor Azure-resources te identificeren. Met doorlopend exporteren kunt u waarschuwingen en aanbevelingen hand matig of op een doorlopende manier exporteren. U kunt de Azure Security Center Data Connector gebruiken om de waarschuwingen naar Azure Sentinel te streamen.

- [Continue export configureren](../security-center/continuous-export.md)

- [Waarschuwingen streamen naar Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: de reactie op beveiligings waarschuwingen automatiseren

**Hulp**: gebruik de functie werk stroom automatisering in azure Security Center om automatisch reacties te activeren via ' Logic apps ' in beveiligings waarschuwingen en aanbevelingen voor het beveiligen van uw Azure-resources.

- [Werk stroom automatisering en Logic Apps configureren](../security-center/workflow-automation.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="penetration-tests-and-red-team-exercises"></a>Penetratietests en Red Team-oefeningen

*Zie [Azure Security Bench Mark: Indringings tests en rode team oefeningen](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)voor meer informatie.*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: voert regel matig indringings tests van uw Azure-resources uit en zorgt voor herbemiddeling van alle essentiële beveiligings resultaten

**Richt lijnen**: Volg de micro soft-regels om ervoor te zorgen dat de indringings tests niet worden geschonden door het micro soft-beleid. Gebruik de strategie van micro soft en de uitvoering van de implementatie van de indringing van een live site in de Cloud, services en toepassingen die door micro soft worden beheerd.

- [Regels voor het inzetten van penetratietests](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Gedeeld

## <a name="next-steps"></a>Volgende stappen

- Zie de [Azure Security-Bench Mark](../security/benchmarks/overview.md)
- Meer informatie over [Azure-beveiligingsbasislijnen](../security/benchmarks/security-baselines-overview.md)