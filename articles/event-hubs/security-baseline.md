---
title: Azure-beveiligingsbasislijn voor Event Hubs
description: De Event Hubs beveiligingsbasislijn biedt procedurele richtlijnen en resources voor het implementeren van de beveiligingsaanbevelingen die zijn opgegeven in de Azure Security Benchmark.
author: msmbaldwin
ms.service: event-hubs
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 33417a9bda9ad4ce36dd6e14f74a53911f3c3473
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/17/2021
ms.locfileid: "107587150"
---
# <a name="azure-security-baseline-for-event-hubs"></a>Azure-beveiligingsbasislijn voor Event Hubs

Deze beveiligingsbasislijn past richtlijnen van [Azure Security Benchmark versie 1.0](../security/benchmarks/overview-v1.md) toe op Event Hubs. De Azure Security-benchmark biedt aanbevelingen voor hoe u uw cloudoplossingen in Azure kunt beveiligen.
De inhoud wordt gegroepeerd op basis van de **beveiligingscontroles** die zijn gedefinieerd door de Azure Security Benchmark en de gerelateerde richtlijnen die van toepassing zijn op Event Hubs. **Besturingselementen** die niet van Event Hubs zijn uitgesloten.

 
Zie het volledige Event Hubs toewijzingsbestand voor beveiligingsbasislijnen om te zien hoe Event Hubs volledig is toegewezen aan de Azure [Security-Event Hubs](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)benchmark.

## <a name="network-security"></a>Netwerkbeveiliging

*Zie [Azure Security Benchmark: netwerkbeveiliging](../security/benchmarks/security-control-network-security.md) voor meer informatie.*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Azure-resources binnen virtuele netwerken beveiligen

**Richtlijnen:** De integratie van Azure Event Hubs met service-eindpunten voor virtuele netwerken maakt beveiligde toegang tot berichtenmogelijkheden mogelijk vanuit werkbelastingen zoals virtuele machines die zijn gebonden aan virtuele netwerken, met het netwerkverkeerspad aan beide uiteinden beveiligd.

Zodra de naamruimte is gebonden aan ten minste één subnetservice-eindpunt voor een virtueel netwerk, accepteert de Event Hubs-naamruimte geen verkeer meer vanaf elke locatie, maar van geautoriseerde subnetten in virtuele netwerken. Vanuit het perspectief van het virtuele netwerk configureert uw Event Hubs-naamruimte aan een service-eindpunt een geïsoleerde netwerktunnel van het subnet van het virtuele netwerk naar de berichtenservice. 

U kunt ook een privé-eindpunt maken. Dit is een netwerkinterface die u privé en veilig verbindt met Event Hubs-service met behulp van de Azure Private Link service. Het privé-eindpunt maakt gebruik van een privé-IP-adres van uw virtuele netwerk, waardoor de service in uw virtuele netwerk wordt gebruikt. Al het verkeer naar de service kan worden gerouteerd via het privé-eindpunt, zodat er geen gateways, NAT-apparaten, ExpressRoute of VPN-verbindingen of openbare IP-adressen nodig zijn. 

U kunt uw Azure Event Hubs ook beveiligen met behulp van firewalls. Event Hubs biedt ondersteuning voor toegangsbesturingselementen op basis van IP voor binnenkomende firewallondersteuning. U kunt firewallregels instellen met behulp van de Azure Portal, Azure Resource Manager sjablonen of via de Azure CLI of Azure PowerShell.

- [Service-eindpunten voor virtuele netwerken gebruiken met Azure Event Hubs](event-hubs-service-endpoints.md)

- [Integratie Azure Event Hubs met Azure Private Link](private-link-service.md)

- [IP-firewallregels configureren voor Azure Event Hubs-naamruimten](event-hubs-ip-filtering.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** De [Azure Security-benchmark](/azure/governance/policy/samples/azure-security-benchmark) is het standaardbeleidsinitiatief voor Security Center en is de basis voor [Security Center aanbevelingen van Security Center.](/azure/security-center/security-center-recommendations) De Azure Policy met betrekking tot dit besturingselement worden automatisch ingeschakeld door Security Center. Waarschuwingen met betrekking tot dit besturingselement vereisen mogelijk een [Azure Defender](/azure/security-center/azure-defender) voor de gerelateerde services.

**Azure Policy ingebouwde definities - Microsoft.EventHub**:

[!INCLUDE [Resource Policy for Microsoft.EventHub 1.1](../../includes/policy/standards/asb/rp-controls/microsoft.eventhub-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2: De configuratie en het verkeer van virtuele netwerken, subnetten en netwerkinterfaces bewaken en logboeken

**Richtlijnen:** gebruik Azure Security Center en volg de aanbevelingen voor netwerkbeveiliging om uw Event Hubs in Azure te beveiligen. Als u virtuele Azure-machines gebruikt voor toegang tot uw Event Hubs, moet u stroomlogboeken van netwerkbeveiligingsgroep inschakelen en logboeken verzenden naar een opslagaccount voor verkeerscontrole.

- [NSG-stroomlogboeken inschakelen](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Informatie over netwerkbeveiliging die wordt geleverd door Azure Security Center](../security-center/security-center-network-recommendations.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Communicatie met bekende schadelijke IP-adressen weigeren

**Richtlijnen:** schakel DDoS Protection Standard in op de virtuele netwerken die zijn gekoppeld aan uw Event Hubs om u te beschermen tegen gedistribueerde DDoS-aanvallen (Denial of Service). Gebruik Azure Security Center Integrated Threat Intelligence om communicatie met bekende schadelijke of ongebruikte INTERNET-IP-adressen te weigeren.

- [DDoS-beveiliging configureren](../ddos-protection/manage-ddos-protection.md)

- [Voor meer informatie over de Azure Security Center Integrated Threat Intelligence](../security-center/azure-defender.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="15-record-network-packets"></a>1.5: Netwerkpakketten registreren

**Richtlijnen:** als u virtuele Azure-machines gebruikt voor toegang tot uw Event Hubs, moet u stroomlogboeken van netwerkbeveiligingsgroep inschakelen en logboeken verzenden naar een opslagaccount voor verkeerscontrole. U kunt ook stroomlogboeken van netwerkbeveiligingsgroep verzenden naar een Log Analytics-werkruimte en Traffic Analytics om inzicht te geven in de verkeersstroom in uw Azure-cloud. Enkele voordelen van Traffic Analytics zijn de mogelijkheid om netwerkactiviteit te visualiseren en hotspots te identificeren, beveiligingsrisico's te identificeren, verkeersstroompatronen te begrijpen en onjuiste netwerkconfiguraties aan te wijzen.

Indien nodig voor het onderzoeken van afwijkende activiteiten, moet u Network Watcher pakketopname inschakelen.

- [NSG-stroomlogboeken inschakelen](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Informatie over het inschakelen en gebruiken van Traffic Analytics](../network-watcher/traffic-analytics.md)

- [Het inschakelen van Network Watcher](../network-watcher/network-watcher-create.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Op het netwerk gebaseerde inbraakdetectie-/inbraakpreventiesystemen (IDS/IPS) implementeren

**Richtlijnen:** als u virtuele Azure-machines gebruikt voor toegang tot uw Event Hubs, selecteert u een aanbieding in de Azure Marketplace die ondersteuning biedt voor IDS/IPS-functionaliteit met mogelijkheden voor nettoladinginspectie. Als inbraakdetectie en/of -preventie op basis van payloadinspectie niet vereist is voor uw organisatie, kunt u de ingebouwde firewallfunctie van Azure Event Hubs gebruiken. U kunt de toegang tot uw Event Hubs voor een beperkt bereik van IP-adressen of een specifiek IP-adres beperken met behulp van firewallregels.

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

- [Een firewallregel toevoegen aan Event Hubs voor een opgegeven IP-adres](event-hubs-ip-filtering.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Standaardbeveiligingsconfiguraties voor netwerkapparaten onderhouden

**Richtlijnen:** standaardbeveiligingsconfiguraties definiëren en implementeren voor netwerkbronnen die zijn gekoppeld aan uw Azure Event Hubs-naamruimten met Azure Policy. Gebruik Azure Policy aliassen in de naamruimten **Microsoft.EventHub** en **Microsoft.Network** om aangepaste beleidsdefinities te maken om de netwerkconfiguratie van uw Event Hubs te controleren of af te dwingen. U kunt ook gebruikmaken van ingebouwde beleidsdefinities die betrekking hebben op Azure Event Hubs, zoals:

- Event Hub moet gebruikmaken van een service-eindpunt voor een virtueel netwerk.

Aanvullende informatie is beschikbaar via de koppelingen waarnaar wordt verwezen.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Ingebouwd Azure-beleid voor Event Hubs naamruimte](../governance/policy/samples/built-in-policies.md#event-hub)

- [Azure Policy voorbeelden voor netwerken](../governance/policy/samples/built-in-policies.md#network)

- [Een Azure Blueprint maken](../governance/blueprints/create-blueprint-portal.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="110-document-traffic-configuration-rules"></a>1.10: Configuratieregels voor verkeer documenteren

**Richtlijnen:** tags gebruiken voor virtuele netwerken en andere resources die betrekking hebben op netwerkbeveiliging en verkeersstromen die zijn gekoppeld aan uw Event Hubs.

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Geautomatiseerde hulpprogramma's gebruiken om netwerkresourceconfiguraties te bewaken en wijzigingen te detecteren

**Richtlijnen:** Azure-activiteitenlogboek gebruiken om configuraties van netwerkresources te bewaken en wijzigingen te detecteren voor netwerkresources met betrekking tot Azure Event Hubs. Maak waarschuwingen binnen Azure Monitor die worden getriggerd wanneer er wijzigingen in kritieke netwerkbronnen plaatsvinden.

- [Azure-activiteitenlogboekgebeurtenissen weergeven en ophalen](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Waarschuwingen maken in Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="logging-and-monitoring"></a>Logboekregistratie en bewaking

*Zie de Azure [Security Benchmark: Logging and Monitoring (Azure Security Benchmark: logboekregistratie en bewaking) voor meer informatie.](../security/benchmarks/security-control-logging-monitoring.md)*

### <a name="22-configure-central-security-log-management"></a>2.2: Centraal beheer van beveiligingslogboek configureren

**Richtlijnen:** configureer Azure Monitor logboeken met betrekking tot Event Hubs in het activiteitenlogboek en de diagnostische instellingen van Event Hub om logboeken te verzenden naar een Log Analytics-werkruimte voor query's of naar een opslagaccount voor langetermijnarchivering.

- [Diagnostische instellingen configureren voor Azure Event Hubs](event-hubs-diagnostic-logs.md)

- [Informatie over Azure-activiteitenlogboek](../azure-monitor/essentials/platform-logs-overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Auditlogregistratie voor Azure-resources inschakelen

**Richtlijnen:** Schakel diagnostische instellingen voor uw Azure Event Hubs in. Er zijn drie categorieën diagnostische instellingen voor Azure Event Hubs: Logboeken archiveren, operationele logboeken en logboeken automatisch schalen. Schakel operationele logboeken in om informatie vast te leggen over wat er gebeurt tijdens Event Hubs-bewerkingen, met name het bewerkingstype, waaronder het maken van event hubs, gebruikte resources en de status van de bewerking.

Daarnaast kunt u diagnostische instellingen voor Azure-activiteitenlogboek inschakelen en deze verzenden naar een Azure Storage-account, Event Hub of een Log Analytics-werkruimte. Activiteitenlogboeken bieden inzicht in de bewerkingen die zijn uitgevoerd op uw Azure Event Hubs en andere resources. Met behulp van activiteitenlogboeken kunt u het 'wat, wie en wanneer' bepalen voor schrijfbewerkingen (PUT, POST, DELETE) die op uw Azure Event Hubs worden uitgevoerd.

- [Diagnostische instellingen inschakelen voor Azure Event Hubs](event-hubs-diagnostic-logs.md)

- [Diagnostische instellingen inschakelen voor Azure-activiteitenlogboek](../azure-monitor/essentials/activity-log.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** De [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) is het standaardbeleidsinitiatief voor Security Center en is de basis voor de aanbevelingen Security Center van [Security Center.](/azure/security-center/security-center-recommendations) De Azure Policy met betrekking tot dit besturingselement worden automatisch ingeschakeld door Security Center. Voor waarschuwingen met betrekking tot dit besturingselement is mogelijk een [Azure Defender](/azure/security-center/azure-defender) nodig voor de gerelateerde services.

**Azure Policy ingebouwde definities - Microsoft.EventHub**:

[!INCLUDE [Resource Policy for Microsoft.EventHub 2.3](../../includes/policy/standards/asb/rp-controls/microsoft.eventhub-2-3.md)]

### <a name="25-configure-security-log-storage-retention"></a>2.5: Opslagretentie van beveiligingslogboek configureren

**Richtlijnen:** stel Azure Monitor log analytics-werkruimteretentieperiode in volgens de nalevingsvoorschriften van uw organisatie om event hub-gerelateerde incidenten vast te leggen en te controleren.

- [Retentieparameters voor logboeken instellen voor Log Analytics-werkruimten](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="26-monitor-and-review-logs"></a>2.6: Logboeken bewaken en controleren

**Richtlijnen:** analyseer en controleer logboeken op afwijkende gedrag en bekijk regelmatig resultaten met betrekking tot uw Event Hubs. Gebruik Azure Monitor Log Analytics om logboeken te controleren en query's uit te voeren op logboekgegevens. U kunt ook gegevens inschakelen en in gebruik nemen voor Azure Sentinel of een oplossing voor systeemgegevens en gebeurtenisbeheer van derden.

- [Voor meer informatie over de Log Analytics-werkruimte](../azure-monitor/logs/log-analytics-tutorial.md)

- [Aangepaste query's uitvoeren in Azure Monitor](../azure-monitor/logs/get-started-queries.md)

- [Onboarding van Azure Sentinel](../sentinel/quickstart-onboard.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Waarschuwingen inschakelen voor afwijkende activiteiten

**Richtlijnen:** configureer binnen Azure Monitor logboeken met betrekking tot Azure Event Hubs in het activiteitenlogboek en Event Hubs diagnostische instellingen voor het verzenden van logboeken naar een Log Analytics-werkruimte die moeten worden opgevraagd of in een opslagaccount voor langetermijnarchivering. Gebruik de Log Analytics-werkruimte om waarschuwingen te maken voor afwijkende activiteiten in beveiligingslogboeken en gebeurtenissen.

U kunt ook gegevens inschakelen en in gebruik nemen om Azure Sentinel. 

- [Inzicht in het Azure-activiteitenlogboek](../azure-monitor/essentials/platform-logs-overview.md)

- [Diagnostische instellingen configureren voor Azure Event Hubs](event-hubs-diagnostic-logs.md)

- [Waarschuwingen voor logboekgegevens van Log Analytics-werkruimten](../azure-monitor/alerts/tutorial-response.md)

- [Onboarding van Azure Sentinel](../sentinel/quickstart-onboard.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="identity-and-access-control"></a>Identiteits- en toegangsbeheer

*Zie de Azure [Security Benchmark: Identity and Access Control (Azure Security-benchmark: identiteit en Access Control).](../security/benchmarks/security-control-identity-access-control.md)*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Een inventaris van beheerdersaccounts onderhouden

**Richtlijnen:** Azure Active Directory (Azure AD) heeft ingebouwde rollen die expliciet moeten worden toegewezen en opvraagbaar zijn. Gebruik de Azure AD PowerShell-module om ad-hocquery's uit te voeren om accounts te vinden die lid zijn van beheergroepen.

- [Een directoryrol in Azure AD krijgen met PowerShell](/powershell/module/azuread/get-azureaddirectoryrole)

- [Leden van een directoryrol in Azure AD krijgen met PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Standaardwachtwoorden wijzigen, indien van toepassing

**Richtlijnen:** Toegang tot de besturingsvlak Event Hubs wordt beheerd via Azure Active Directory (Azure AD). Azure AD heeft niet het concept van standaardwachtwoorden.

Toegang tot gegevensvlakken Event Hubs wordt beheerd via Azure AD met beheerde identiteiten of App-registraties evenals handtekeningen voor gedeelde toegang. Handtekeningen voor gedeelde toegang worden gebruikt door de clients die verbinding maken met uw Event Hubs en kunnen op elk moment opnieuw worden ge regenereerd.

- [Informatie over handtekeningen voor gedeelde toegang voor Event Hubs](authenticate-shared-access-signature.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Toegewezen beheerdersaccounts gebruiken

**Richtlijnen:** standaardprocedures voor het gebruik van toegewezen beheerdersaccounts maken. Gebruik Azure Security Center identiteits- en toegangsbeheer om het aantal beheerdersaccounts te bewaken.

Daarnaast kunt u aanbevelingen van Azure Security Center of ingebouwde Azure-beleidsregels gebruiken om toegewezen beheerdersaccounts bij te houden, zoals:

- Er moet meer dan één eigenaar zijn toegewezen aan uw abonnement

- Afgeschafte accounts met eigenaarsmachtigingen moeten worden verwijderd uit uw abonnement

- Externe accounts met eigenaarsmachtigingen moeten worden verwijderd uit uw abonnement

Aanvullende informatie is beschikbaar via de koppelingen waarnaar wordt verwezen.

- [Identiteit en toegang Azure Security Center gebruiken (preview)](../security-center/security-center-identity-access.md)

- [Hoe u Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4: Eenmalige Azure Active Directory gebruiken

**Richtlijnen:** Microsoft Azure biedt geïntegreerd toegangsbeheer voor resources en toepassingen op basis van Azure Active Directory (Azure AD). Een belangrijk voordeel van het gebruik van Azure AD Azure Event Hubs is dat u uw referenties niet meer in de code hoeft op te slaan. In plaats daarvan kunt u een OAuth 2.0-toegangsteken aanvragen bij het Microsoft Identity-platform. De resourcenaam voor het aanvragen van een token is https: \/ /eventhubs.azure.net/. Azure AD verifieert de beveiligingsprincipaal (een gebruiker, groep of service-principal) die de toepassing uitvoeren. Als de verificatie slaagt, retourneert Azure AD een toegangs token voor de toepassing en kan de toepassing vervolgens het toegangsken gebruiken om de aanvraag te autorisatie Azure Event Hubs resources.

- [Een toepassing verifiëren met Azure AD voor toegang tot Event Hubs resources](authenticate-application.md)

- [Informatie over eenmalige aanmelding met Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Meervoudige verificatie gebruiken voor alle Azure Active Directory op basis van toegang

**Richtlijnen:** meervoudige Azure Active Directory (Azure AD) inschakelen en de aanbevelingen voor Azure Security Center Identity and Access Management volgen om uw Event Hub-resources te beveiligen.

- [Meervoudige verificatie inschakelen in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Identiteit en toegang binnen uw Azure Security Center](../security-center/security-center-identity-access.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Toegewezen machines (Privileged Access Workstations) gebruiken voor alle beheertaken

**Richtlijnen:** Werkstations met bevoegde toegang (PAW) gebruiken met meervoudige verificatie die is geconfigureerd om u aan te melden bij en te configureren voor Event Hub-resources.

- [Meer informatie over Privileged Access Workstations](/security/compass/privileged-access-devices)

- [Meervoudige verificatie inschakelen in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Logboeken en waarschuwingen voor verdachte activiteiten van beheerdersaccounts

**Richtlijnen:** gebruik Azure Active Directory -Privileged Identity Management (Azure AD) voor het genereren van logboeken en waarschuwingen wanneer er verdachte of onveilige activiteiten plaatsvinden in de omgeving. Gebruik Azure AD-risicodetecties om waarschuwingen en rapporten over riskant gebruikersgedrag weer te geven. Voor aanvullende logboekregistratie verzendt u waarschuwingen Azure Security Center risicodetectie naar Azure Monitor configureren van aangepaste waarschuwingen/meldingen met behulp van actiegroepen.

- [Inzicht in Azure AD-risicodetecties](../active-directory/identity-protection/overview-identity-protection.md)

- [Actiegroepen configureren voor aangepaste waarschuwingen en meldingen](../azure-monitor/alerts/action-groups.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Azure-resources alleen beheren vanaf goedgekeurde locaties

**Richtlijnen:** gebruik benoemde locaties voor voorwaardelijke toegang om alleen toegang toe te staan vanuit specifieke logische groeperingen van IP-adresbereiken of landen/regio's.

- [Benoemde locaties configureren in Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="39-use-azure-active-directory"></a>3.9: Gebruik Azure Active Directory

**Richtlijnen:** Gebruik Azure Active Directory (Azure AD) als het centrale verificatie- en autorisatiesysteem voor Azure-resources zoals Event Hubs. Hierdoor is op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC) tot beheergevoelige resources mogelijk.

- [ Een Azure AD-exemplaar maken en configureren](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [Toegang verlenen tot Event Hubs resources met behulp van Azure AD](authorize-access-azure-active-directory.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Regelmatig gebruikerstoegang controleren en afstemmen

**Richtlijnen:** Azure Active Directory (Azure AD) biedt logboeken om u te helpen bij het vinden van verouderde accounts. Gebruik bovendien Azure Identity Access Reviews om groepslidmaatschap, toegang tot bedrijfstoepassingen en roltoewijzingen efficiënt te beheren. Gebruikerstoegang kan regelmatig worden gecontroleerd om ervoor te zorgen dat alleen de juiste gebruikers nog toegang hebben.

Daarnaast moet u de shared access signatures van Event Hubs uw bedrijf regelmatig roteren.

- [Inzicht in Azure AD-rapportage](../active-directory/reports-monitoring/index.yml)

- [Toegangsbeoordelingen voor Azure Identity gebruiken](../active-directory/governance/access-reviews-overview.md)

- [Informatie over handtekeningen voor gedeelde toegang voor Event Hubs](authenticate-shared-access-signature.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Pogingen tot toegang tot gedeactiveerde referenties controleren

**Richtlijnen:** u hebt toegang tot Azure Active Directory(Azure AD)-aanmeldingsactiviteiten, controle- en risicogebeurtenislogboekbronnen, waarmee u kunt integreren met een oplossing voor systeemgegevens en gebeurtenisbeheer van derden of een bewakingshulpprogramma.

U kunt dit proces stroomlijnen door diagnostische instellingen te maken voor Azure AD-gebruikersaccounts en de auditlogboeken en aanmeldingslogboeken te verzenden naar een Log Analytics-werkruimte. U kunt gewenste logboekwaarschuwingen configureren in Log Analytics.

- [Azure-activiteitenlogboeken integreren in Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Toegang verlenen tot Event Hubs resources met behulp van Azure AD](authorize-access-azure-active-directory.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: Afwijking van aanmeldingsgedrag van account

**Richtlijnen:** Gebruik functies voor identiteitsbeveiliging en risicodetectie in Azure Active Directory (Azure AD) om geautomatiseerde reacties te configureren op gedetecteerde verdachte acties met betrekking tot uw Event Hubs-resources. U moet geautomatiseerde reacties via Azure Sentinel om de beveiligingsreacties van uw organisatie te implementeren.

- [Riskante Azure AD-aanmeldingen weergeven](../active-directory/identity-protection/overview-identity-protection.md)

- [Risicobeleid voor Identiteitsbeveiliging configureren en inschakelen](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Onboarding van Azure Sentinel](../sentinel/quickstart-onboard.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Microsoft toegang geven tot relevante klantgegevens tijdens ondersteuningsscenario's

**Richtlijnen:** Momenteel niet beschikbaar; Klanten-lockbox wordt nog niet ondersteund voor Event Hubs.

- [Lijst met Klanten-lockbox ondersteunde services](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="data-protection"></a>Gegevensbeveiliging

*Zie [Azure Security Benchmark: gegevensbescherming](../security/benchmarks/security-control-data-protection.md) voor meer informatie.*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Een inventaris van gevoelige informatie onderhouden

**Richtlijnen:** Gebruik tags voor resources die betrekking hebben op uw Event Hubs om Azure-resources bij te houden die gevoelige informatie opslaan of verwerken.

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Systemen isoleren die gevoelige informatie opslaan of verwerken

**Richtlijnen:** Implementeert afzonderlijke abonnementen, eventueel met beheergroepen voor ontwikkeling, test en productie. Event Hubs naamruimten moeten worden gescheiden door een virtueel netwerk met service-eindpunten ingeschakeld en op de juiste wijze getagd.

U kunt uw Azure Event Hubs ook beveiligen met behulp van firewalls. Azure Event Hubs biedt ondersteuning voor toegangsbesturingselementen op basis van IP voor binnenkomende firewallondersteuning. U kunt firewallregels instellen met behulp van de Azure Portal, Azure Resource Manager-sjablonen of via de Azure CLI of Azure PowerShell.

- [Aanvullende Azure-abonnementen maken](../cost-management-billing/manage/create-subscription.md)

- [Een Beheergroepen](../governance/management-groups/create-management-group-portal.md)

- [IP-firewallregels configureren voor Azure Event Hubs-naamruimten](event-hubs-ip-filtering.md)

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

- [Een Virtual Network](../virtual-network/quick-create-portal.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Onbevoegde overdracht van gevoelige informatie bewaken en blokkeren

**Richtlijnen:** wanneer u virtuele machines gebruikt voor toegang tot uw Event Hubs, maakt u gebruik van virtuele netwerken, service-eindpunten, Event Hubs-firewall, netwerkbeveiligingsgroepen en servicetags om de mogelijkheid van gegevens exfiltratie te beperken.

Microsoft beheert de onderliggende infrastructuur voor Azure Event Hubs en heeft strenge controles geïmplementeerd om verlies of blootstelling van klantgegevens te voorkomen.

- [IP-firewallregels configureren Azure Event Hubs naamruimten](event-hubs-ip-filtering.md)

- [Inzicht Virtual Network service-eindpunten met Azure Event Hubs](event-hubs-service-endpoints.md)

- [Integratie Azure Event Hubs met Azure Private Link](private-link-service.md)

- [Informatie over netwerkbeveiligingsgroepen en servicetags](../virtual-network/network-security-groups-overview.md)

- [Informatie over beveiliging van klantgegevens in Azure](../security/fundamentals/protection-customer-data.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Een actief detectieprogramma gebruiken om gevoelige gegevens te identificeren

**Richtlijnen:** functies voor gegevensidentificatie, -classificatie en -preventie zijn nog niet beschikbaar voor Azure Event Hubs. Implementeert indien nodig een oplossing van derden voor nalevingsdoeleinden.

Voor het onderliggende platform dat wordt beheerd door Microsoft, behandelt Microsoft alle inhoud van klanten als gevoelig en gaat microsoft alles in het werk om zich te beschermen tegen gegevensverlies en -blootstelling van klanten. Om ervoor te zorgen dat klantgegevens in Azure veilig blijven, heeft Microsoft een suite met robuuste besturingselementen en mogelijkheden voor gegevensbeveiliging geïmplementeerd en onderhouden.

- [Informatie over beveiliging van klantgegevens in Azure](../security/fundamentals/protection-customer-data.md)

**Verantwoordelijkheid**: Gedeeld

**Azure Security Center bewaking:** geen

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6: Op rollen gebaseerd toegangsbeheer gebruiken om de toegang tot resources te beheren

**Richtlijnen:** Azure Event Hubs ondersteuning voor het gebruik Azure Active Directory (Azure AD) om aanvragen voor Event Hubs resources. Met Azure AD kunt u op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC) gebruiken om machtigingen te verlenen aan een beveiligingsprincipaal( dit kan een gebruiker zijn of een toepassingsservice-principal).

- [Inzicht in Azure RBAC en beschikbare rollen voor Azure Event Hubs](authorize-access-azure-active-directory.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Gevoelige informatie at-rest versleutelen

**Richtlijnen:** Azure Event Hubs de optie voor het versleutelen van data-at-rest met door Microsoft beheerde sleutels of door de klant beheerde sleutels. Met deze functie kunt u de toegang maken, draaien, uitschakelen en intrekken tot de door de klant beheerde sleutels die worden gebruikt voor het versleutelen Azure Event Hubs data-at-rest.

- [Door de klant beheerde sleutels configureren voor het versleutelen van Azure Event Hubs](configure-customer-managed-key.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Wijzigingen in kritieke Azure-resources aanmelden en er waarschuwingen voor ontvangen

**Richtlijnen:** gebruik Azure Monitor met het Azure-activiteitenlogboek om waarschuwingen te maken voor wanneer er wijzigingen worden aangebracht in productie-exemplaren van Azure Event Hubs en andere kritieke of gerelateerde resources.

- [Waarschuwingen maken voor gebeurtenissen in het Azure-activiteitenlogboek](../azure-monitor/alerts/alerts-activity-log.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="inventory-and-asset-management"></a>Inventarisatie en Asset Management

*Zie Azure Security [Benchmark: Inventory and Asset Management (Azure Security Benchmark: Inventaris en Asset Management) voor meer informatie.](../security/benchmarks/security-control-inventory-asset-management.md)*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Een geautomatiseerde oplossing voor assetdetectie gebruiken

**Richtlijnen:** gebruik Azure Resource Graph om alle resources (inclusief Azure Event Hubs naamruimten) binnen uw abonnementen op te vragen en te ontdekken. Zorg ervoor dat u over de juiste (lees)machtigingen in uw tenant hebt en alle Azure-abonnementen en resources binnen uw abonnementen kunt opsnoemen.

- [Query's maken met Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

- [Uw Azure-abonnementen weergeven](/powershell/module/az.accounts/get-azsubscription)

- [Inzicht krijgen in Azure RBAC](../role-based-access-control/overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="62-maintain-asset-metadata"></a>6.2: Metagegevens van activa onderhouden

**Richtlijnen:** Tags toepassen op Azure-resources die metagegevens geven om ze logisch te organiseren in een taxonomie.

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Niet-geautoriseerde Azure-resources verwijderen

**Richtlijnen:** gebruik waar van toepassing tags, beheergroepen en afzonderlijke abonnementen om uw naamruimten en gerelateerde resources te Azure Event Hubs en bij te houden. Inventaris regelmatig afstemmen en ervoor zorgen dat niet-geautoriseerde resources tijdig uit het abonnement worden verwijderd.

- [Extra Azure-abonnementen maken](../cost-management-billing/manage/create-subscription.md)

- [Een Beheergroepen](../governance/management-groups/create-management-group-portal.md)

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Controleren op niet-goedgekeurde Azure-resources

**Richtlijnen:** gebruik Azure Policy beperkingen in te stellen voor het type resources dat kan worden gemaakt in klantabonnementen met behulp van de volgende ingebouwde beleidsdefinities:

- Niet toegestane resourcetypen

- Toegestane brontypen

Gebruik bovendien Azure Resource Graph om resources in de abonnementen op te vragen/te ontdekken.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Query's maken met Azure Graph](../governance/resource-graph/first-query-portal.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="69-use-only-approved-azure-services"></a>6.9: Alleen goedgekeurde Azure-services gebruiken

**Richtlijnen:** gebruik Azure Policy beperkingen in te stellen voor het type resources dat kan worden gemaakt in klantabonnementen met behulp van de volgende ingebouwde beleidsdefinities:

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

**Richtlijnen:** standaardbeveiligingsconfiguraties definiëren en implementeren voor uw Azure Event Hubs implementaties. Gebruik Azure Policy aliassen in de **Naamruimte Microsoft.EventHub** om aangepaste beleidsregels te maken om configuraties te controleren of af te dwingen. U kunt ook gebruikmaken van ingebouwde beleidsdefinities voor Azure Event Hubs, zoals:

- Diagnostische logboeken in Event Hub moeten zijn ingeschakeld

- Event Hub moet gebruikmaken van een service-eindpunt voor een virtueel netwerk

Aanvullende informatie is beschikbaar via de koppelingen waarnaar wordt verwezen.

- [Ingebouwd Azure-beleid voor Event Hubs-naamruimte](../governance/policy/samples/built-in-policies.md#event-hub)

- [Beschikbare aliassen Azure Policy weergeven](/powershell/module/az.resources/get-azpolicyalias)

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Veilige Azure-resourceconfiguraties onderhouden

**Richtlijnen:** gebruik Azure Policy gevolgen [weigeren] en [implementeren indien niet aanwezig] om beveiligde instellingen af te dwingen voor uw Event Hubs resources. 

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

 
- [Voor meer informatie over de Azure Policy effecten](../governance/policy/concepts/effects.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Hulpprogramma's voor configuratiebeheer implementeren voor Azure-resources

**Richtlijnen:** gebruik Azure Policy aliassen in de naamruimte Microsoft.EventHub om aangepaste beleidsregels te maken voor het waarschuwen, controleren en afdwingen van systeemconfiguraties. Daarnaast ontwikkelt u een proces en pijplijn voor het beheren van beleids uitzonderingen.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Geautomatiseerde configuratiebewaking implementeren voor Azure-resources

**Richtlijnen:** gebruik Azure Policy aliassen in de naamruimte Microsoft.EventHub om aangepaste beleidsregels te maken voor het waarschuwen, controleren en afdwingen van systeemconfiguraties. Gebruik Azure Policy effecten [audit], [deny] en [deploy if not exist] om automatisch configuraties af te dwingen voor uw Azure Event Hubs implementaties en gerelateerde resources.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="711-manage-azure-secrets-securely"></a>7.11: Azure-geheimen veilig beheren

**Richtlijnen:** voor virtuele Azure-machines of webtoepassingen die worden uitgevoerd op Azure App Service die worden gebruikt voor toegang tot uw Event Hubs, gebruikt u Managed Service Identity in combinatie met Azure Key Vault voor het vereenvoudigen en beveiligen van shared access signature-beheer voor uw Azure Event Hubs-implementaties. Zorg Key Vault is geconfigureerd met ingeschakelde functie voor soft-delete.

- [Een beheerde identiteit verifiëren met Azure Active Directory (Azure AD) voor toegang tot Event Hubs resources](./authenticate-managed-identity.md?tabs=latest)

- [Door de klant beheerde sleutels configureren voor Event Hubs](configure-customer-managed-key.md)

- [Integreren met beheerde Azure-identiteiten](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Een Key Vault](../key-vault/secrets/quick-create-portal.md)

- [Verificatie met Key Vault met een beheerde identiteit](../key-vault/general/assign-access-policy-portal.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Identiteiten veilig en automatisch beheren

**Richtlijnen:** gebruik Managed Service Identity in combinatie met Azure Key Vault voor virtuele Azure-machines of webtoepassingen die worden uitgevoerd op Azure App Service die worden gebruikt voor toegang tot uw Event Hubs, om de identiteit te vereenvoudigen en te Azure Event Hubs. Zorg Key Vault is geconfigureerd met ingeschakelde functie voor soft-delete.

Gebruik Beheerde identiteiten om Azure-services te voorzien van een automatisch beheerde identiteit in Azure Active Directory (Azure AD). Met beheerde identiteiten kunt u zich verifiëren bij elke service die ondersteuning biedt voor Azure AD-verificatie, Azure Key Vault zonder referenties in uw code.

- [Een beheerde identiteit verifiëren met Azure AD voor toegang tot Event Hubs Resources](./authenticate-managed-identity.md?tabs=latest)

- [Door de klant beheerde sleutels configureren voor Event Hubs](configure-customer-managed-key.md)

- [Beheerde identiteiten configureren](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

- [Integreren met beheerde Azure-identiteiten](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Onbedoelde blootstelling van referenties voorkomen

**Richtlijnen:** Referentiescanner implementeren om referenties in code te identificeren. Door het gebruik van Credential Scanner worden gebruikers ook aangemoedigd om gedetecteerde referenties naar veiligere locaties, zoals Azure Key Vault, te verplaatsen.

- [Referentiescanner instellen](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="malware-defense"></a>Beveiliging tegen malware

*Zie de Azure [Security Benchmark: Malware Defense voor meer informatie.](../security/benchmarks/security-control-malware-defense.md)*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Bestanden vooraf scannen die moeten worden geüpload naar niet-compute-Azure-resources

**Richtlijnen:** Scan vooraf alle inhoud die wordt geüpload naar niet-compute Azure-resources, zoals Azure Event Hubs, App Service, Data Lake Storage, Blob Storage, Azure Database for PostgreSQL, enzovoort. Microsoft heeft in deze gevallen geen toegang tot uw gegevens.

Antimalware van Microsoft is ingeschakeld op de onderliggende host die ondersteuning biedt voor Azure-services (bijvoorbeeld Azure Cache voor Redis), maar wordt niet uitgevoerd op klantinhoud.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="data-recovery"></a>Gegevensherstel

*Zie de Azure [Security Benchmark: Data Recovery voor meer informatie.](../security/benchmarks/security-control-data-recovery.md)*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Regelmatige automatische back-ups garanderen

**Richtlijnen:** Geo-noodherstel configureren voor Azure Event Hubs. Wanneer volledige Azure-regio's of -datacenters (als er geen beschikbaarheidszones worden gebruikt) downtime ervaren, is het essentieel dat de gegevensverwerking in een andere regio of in een ander datacenter blijft werken. Geo-noodherstel en geo-replicatie zijn daarom belangrijke functies voor elke onderneming. Azure Event Hubs ondersteunt zowel geo-noodherstel als geo-replicatie op naamruimteniveau. 

- [Meer informatie over herstel na geo-nood Azure Event Hubs](./event-hubs-geo-dr.md#availability-zones)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Volledige systeemback-ups uitvoeren en back-ups maken van door de klant beheerde sleutels

**Richtlijnen:** Azure Event Hubs biedt versleuteling van data-at-rest met Azure Storage Service Encryption (Azure SSE). Event Hubs is afhankelijk van Azure Storage voor het opslaan van de gegevens en standaard worden alle gegevens die zijn opgeslagen met Azure Storage versleuteld met door Microsoft beheerde sleutels. Als u Azure Key Vault voor het opslaan van door de klant beheerde sleutels, moet u ervoor zorgen dat uw sleutels regelmatig automatisch worden back-ups.

Zorg ervoor dat er regelmatig automatische back-ups van uw Key Vault geheimen worden gemaakt met de volgende PowerShell-opdracht: Backup-AzKeyVaultSecret

- [Door de klant beheerde sleutels configureren voor het versleutelen Azure Event Hubs data-at-rest](configure-customer-managed-key.md)

- [Back-ups maken Key Vault Geheimen](/powershell/module/azurerm.keyvault/backup-azurekeyvaultsecret)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Alle back-ups valideren, inclusief door de klant beheerde sleutels

**Richtlijnen:** Herstel van door de klant beheerde sleutels met een back-up testen.

- [Sleutelkluissleutels herstellen in Azure](/powershell/module/az.keyvault/restore-azkeyvaultkey)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Beveiliging van back-ups en door de klant beheerde sleutels garanderen

**Richtlijnen:** Schakel de functie voor Key Vault om sleutels te beveiligen tegen onbedoelde of schadelijke verwijdering. Azure Event Hubs door de klant beheerde sleutels moeten zijn geconfigureerd voor Soft Delete en Do Not Purge.

Configureer de tijdelijke opslag voor het Azure-opslagaccount dat wordt gebruikt voor het vastleggen van Event Hubs gegevens. Houd er rekening mee dat deze functie nog niet wordt ondersteund Azure Data Lake Storage Gen 2.

- [Zacht verwijderen inschakelen in Key Vault](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

- [Een sleutelkluis met sleutels instellen](configure-customer-managed-key.md)

- [Voorlopig verwijderen voor Azure Storage-blobs](/azure/storage/blobs/soft-delete-blob-overview)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="incident-response"></a>Reageren op incidenten

*Zie [Azure Security Benchmark: respons op incidenten](../security/benchmarks/security-control-incident-response.md) voor meer informatie.*

### <a name="101-create-an-incident-response-guide"></a>10.1: Een handleiding voor het reageren op incidenten maken

**Richtlijnen:** Zorg ervoor dat er geschreven plannen voor het reageren op incidenten zijn die rollen van personeel en fasen van incidentafhandeling/-beheer definiëren.

- [Werkstroomautomatiseringen configureren binnen Azure Security Center](../security-center/security-center-planning-and-operations-guide.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Een procedure voor het scoren en prioriteren van incidenten maken

**Richtlijnen:** Security Center wijst een ernst toe aan waarschuwingen om u te helpen bij het prioriteren van de volgorde waarin u voor elke waarschuwing moet zorgen, zodat u er meteen bij kunt komen wanneer een resource is aangetast. De ernst is gebaseerd op hoe zeker Security Center is bij het vinden of de analyse die wordt gebruikt om de waarschuwing uit te geven, evenals het betrouwbaarheidsniveau dat er een schadelijke intentie achter de activiteit is die heeft geleid tot de waarschuwing.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="103-test-security-response-procedures"></a>10.3: Procedures voor het testen van beveiligingsreacties

**Richtlijnen:** oefeningen uitvoeren om de mogelijkheden voor het reageren op incidenten van uw systemen regelmatig te testen. Stel vast waar zich zwakke plekken en hiaten bevinden, en wijzig zo nodig het plan.

- [Raadpleeg de publicatie van NIST: Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities (Handleiding voor het testen, trainen en trainen van programma's voor IT-abonnementen en -mogelijkheden)](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Contactgegevens voor beveiligingsincidenten verstrekken en waarschuwingsmeldingen voor beveiligingsincidenten configureren

**Richtlijnen:** Contactgegevens voor beveiligingsincidenten worden door Microsoft gebruikt om contact met u op te nemen als de Microsoft Security Response Center (MSRC) detecteert dat de gegevens van de klant zijn gebruikt door een onrechtmatige of niet-geautoriseerde partij.  Bekijk incidenten na het feit om ervoor te zorgen dat problemen worden opgelost. 

- [De beveiligingscontactcontacte Azure Security Center instellen](../security-center/security-center-provide-security-contact-details.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Beveiligingswaarschuwingen opnemen in uw systeem voor het reageren op incidenten

**Richtlijnen:** exporteert Azure Security Center en aanbevelingen met behulp van de functie Continue export. Met Continue export kunt u waarschuwingen en aanbevelingen handmatig of op een continue, continue manier exporteren. U kunt de connector Azure Security Center gegevens gebruiken om de waarschuwingen te streamen naar Azure Sentinel.

- [Continue export configureren](../security-center/continuous-export.md)

- [Waarschuwingen streamen naar Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Het antwoord op beveiligingswaarschuwingen automatiseren

**Richtlijnen:** gebruik de functie Werkstroomautomatisering in Azure Security Center automatisch reacties te activeren via 'Logic Apps' voor beveiligingswaarschuwingen en -aanbevelingen.

- [Werkstroomautomatisering en -Logic Apps](../security-center/workflow-automation.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="penetration-tests-and-red-team-exercises"></a>Penetratietests en Red Team-oefeningen

*Zie de Azure [Security Benchmark: Penetration Tests en Red Team Exercises (Penetratietests en Red Team-oefeningen) voor meer informatie.](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Regelmatige penetratietests uitvoeren van uw Azure-resources en zorgen voor herstel van alle kritieke beveiligings bevindingen

**Richtlijnen:** volg de Microsoft Cloud Penetration Testing Rules of Engagement om ervoor te zorgen dat uw penetratietests niet in strijd zijn met het Microsoft-beleid. Gebruik de strategie en uitvoering van Microsoft voor red teaming en penetratietests van live-site tegen door Microsoft beheerde cloudinfrastructuur, -services en -toepassingen. 

- [Regels voor het inzetten van penetratietests](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="next-steps"></a>Volgende stappen

- Zie [Overzicht Azure Security Benchmark V2](../security/benchmarks/overview.md)
- Meer informatie over [Azure-beveiligingsbasislijnen](../security/benchmarks/security-baselines-overview.md)
