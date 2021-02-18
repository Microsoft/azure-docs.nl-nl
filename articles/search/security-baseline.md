---
title: Azure-beveiligings basislijn voor Azure Cognitive Search
description: De beveiligings basislijn van Azure Cognitive Search biedt procedure richtlijnen en resources voor het implementeren van de beveiligings aanbevelingen die zijn opgegeven in de Azure Security-Bench Mark.
author: msmbaldwin
ms.service: search
ms.topic: conceptual
ms.date: 09/25/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 8c32d06247516611fc79bd6f77b60fec6b73044d
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100591817"
---
# <a name="azure-security-baseline-for-azure-cognitive-search"></a>Azure-beveiligings basislijn voor Azure Cognitive Search

Deze beveiligings basislijn is van toepassing op richt lijnen van de [Azure Security Bench Mark versie 1,0](../security/benchmarks/overview.md) naar Azure Cognitive Search. De Azure Security-benchmark biedt aanbevelingen voor hoe u uw cloudoplossingen in Azure kunt beveiligen. De inhoud wordt gegroepeerd op basis van de **beveiligings controles** die zijn gedefinieerd door de Azure Security-benchmark en de bijbehorende richt lijnen die van toepassing zijn op Azure Cognitive Search. **Besturings elementen** die niet van toepassing zijn op Azure Cognitive Search, of de klant is uitgesloten.

Zie het [volledige azure Cognitive Search Security Baseline-toewijzings bestand](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)voor meer informatie over de manier waarop Azure Cognitive Search volledig is toegewezen aan de beveiligings benchmark van Azure.

## <a name="network-security"></a>Netwerkbeveiliging

*Zie [Azure Security Bench Mark: Network Security](../security/benchmarks/security-control-network-security.md)(Engelstalig) voor meer informatie.*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Azure-resources in virtuele netwerken beveiligen

**Hulp**: Zorg ervoor dat alle implementaties van Microsoft Azure Virtual Network subnet een netwerk beveiligings groep hebben die is toegepast op regels voor het implementeren van een ' minst privileged ' toegangs schema. Alleen toegang tot de vertrouwde poorten en IP-adresbereiken van uw toepassing toestaan. Implementeer Azure Cognitive Search met een persoonlijk Azure-eind punt, waar mogelijk, om persoonlijke toegang tot uw services in te scha kelen vanuit uw virtuele netwerk.

Cognitive Search biedt ook ondersteuning voor extra netwerk beveiligings functies voor het beheren van netwerk toegangs beheer lijsten. Configureer uw zoek service zo dat alleen communicatie met vertrouwde bronnen wordt toegestaan door de toegang te beperken tot specifieke open bare IP-adresbereiken met de firewall mogelijkheid.

- [Privé-eind punten configureren voor Azure Cognitive Search](./service-create-private-endpoint.md)

- [De Azure Cognitive Search-firewall configureren](./service-configure-firewall.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1,2: de configuratie en het verkeer van virtuele netwerken, subnetten en Nic's bewaken en vastleggen

**Richt lijnen**: Cognitive Search kunnen niet rechtstreeks in een virtueel netwerk worden geïmplementeerd. Als uw client toepassing of gegevens bron zich echter in een virtueel netwerk bevindt, kunt u verkeer bewaken en registreren voor deze in-Network-onderdelen, waaronder aanvragen die worden verzonden naar een zoek service in de Cloud. Standaard aanbevelingen zijn het inschakelen van een stroom logboek van een netwerk beveiligings groep en het verzenden van logboeken naar een Azure Storage of een Log Analytics-werk ruimte. U kunt eventueel Traffic Analytics gebruiken om inzicht te krijgen in verkeers patronen.

- [Stroom logboeken van netwerk beveiligings groepen inschakelen](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Traffic Analytics inschakelen en gebruiken](../network-watcher/traffic-analytics.md)

- [Informatie over de netwerk beveiliging die wordt verschaft door Azure Security Center](../security-center/security-center-network-recommendations.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="13-protect-critical-web-applications"></a>1,3: essentiële webtoepassingen beveiligen

**Richt lijnen**: niet van toepassing op Cognitive Search. Deze aanbeveling is bedoeld voor webtoepassingen die worden uitgevoerd op Azure App Service of reken bronnen.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: communicatie met bekende schadelijke IP-adressen weigeren

**Hulp**: Cognitive Search biedt geen specifieke functie voor het bestrijden van een gedistribueerde Denial-of-service-aanval, maar u kunt DDoS Protection standaard inschakelen op de virtuele netwerken die zijn gekoppeld aan uw Cognitive Search-service voor algemene beveiliging.

- [DDoS-beveiliging configureren](../ddos-protection/manage-ddos-protection.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="15-record-network-packets"></a>1,5: netwerk pakketten opnemen

**Hulp**: Schakel logboeken stroom voor netwerk beveiligings groepen in voor de netwerk beveiligings groep die Azure virtual machines (VM) beveiligen die verbinding maakt met uw Cognitive Search-service. Verzend logboeken naar een Azure Storage account voor verkeers controle. 

Schakel Network Watcher pakket opname in als dit nodig is voor het onderzoeken van afwijkende activiteiten.

- [NSG-stroom logboeken inschakelen](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Network Watcher inschakelen](../network-watcher/network-watcher-create.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: op netwerk gebaseerde inbreuk detectie/indringings systemen (ID'S/IP-adressen) implementeren

**Richt lijnen**: Cognitive Search biedt geen ondersteuning voor het detecteren van inbreuken op het netwerk, maar als indringings beperking kunt u firewall regels configureren om de IP-adressen op te geven die worden geaccepteerd door de Cognitive Search-service. Configureer een persoonlijk eind punt om zoeken naar verkeer van het open bare Internet te blijven.

- [Door de klant beheerde sleutels configureren voor gegevens versleuteling](./search-security-manage-encryption-keys.md)

- [Informatie over door de klant beheerde sleutels ophalen van indexen en synoniemen toewijzingen](./search-security-get-encryption-keys.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="17-manage-traffic-to-web-applications"></a>1,7: verkeer naar webtoepassingen beheren

**Richt lijnen**: niet van toepassing op Cognitive Search. Deze aanbeveling is bedoeld voor webtoepassingen die worden uitgevoerd op Azure App Service of reken bronnen.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: de complexiteit en administratieve overhead van netwerk beveiligings regels minimaliseren

**Richt lijnen**: gebruik service tags als u Indexeer functies en vaardig heden in cognitive Search gebruikt, om een bereik van IP-adressen weer te geven die zijn gemachtigd om verbinding te maken met externe bronnen. 

Verkeer naar resources toestaan of weigeren door de naam van de servicetag (bijvoorbeeld AzureCognitiveSearch) op te geven in het juiste bron-of doel veld van een regel. 

- [Service tags van virtueel netwerk](../virtual-network/service-tags-overview.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: standaard beveiligings configuraties voor netwerk apparaten onderhouden

**Hulp**: het Cognitive Search is niet of is afhankelijk van de netwerk resources. Client-apps en gegevens bronnen die betrekking hebben op uw zoek toepassing, bevinden zich mogelijk in een virtueel netwerk, maar de zoek service is niet zelf geïmplementeerd in het netwerk. 

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="110-document-traffic-configuration-rules"></a>1,10: configuratie regels voor het document verkeer

**Hulp**: u kunt Cognitive Search configureren met een persoonlijk Azure-eind punt om uw zoek service te integreren met een virtueel netwerk.  Gebruik resource Tags voor netwerk beveiligings groepen en andere bronnen die betrekking hebben op netwerk beveiliging en verkeers stroom. Gebruik voor afzonderlijke regels voor de netwerk beveiligings groep het veld Beschrijving om de regels te documenteren die verkeer van of naar een netwerk toestaan. 

Gebruik een van de ingebouwde Azure Policy definities met betrekking tot labeling, zoals ' tag vereisen en de bijbehorende waarde ', om ervoor te zorgen dat alle resources met tags worden gemaakt en u op de hoogte moet zijn van bestaande niet-gelabelde resources. 

U kunt Azure PowerShell of Azure CLI gebruiken om op basis van hun labels acties op resources te zoeken of uit te voeren. 

- [Een persoonlijk eind punt maken voor Cognitive Search](./service-create-private-endpoint.md)

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

- [Een Azure-Virtual Network maken](../virtual-network/quick-create-portal.md)

- [Netwerk verkeer filteren met regels voor netwerk beveiligings groepen](../virtual-network/tutorial-filter-network-traffic.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: gebruik automatische hulpprogram ma's om netwerk bron configuraties te bewaken en wijzigingen te detecteren

**Richt lijnen**: Cognitive Search niet of afhankelijk zijn van netwerk onderdelen, waardoor de configuraties van deze resources niet kunnen worden bewaakt.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

## <a name="logging-and-monitoring"></a>Logboekregistratie en bewaking

*Zie [Azure Security Bench Mark: Logging and monitoring](../security/benchmarks/security-control-logging-monitoring.md)(Engelstalig) voor meer informatie.*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: goedgekeurde tijd synchronisatie bronnen gebruiken

**Hulp**: Cognitive Search biedt geen ondersteuning voor het configureren van uw eigen tijd synchronisatie bronnen. De zoek service is afhankelijk van micro soft-tijd synchronisatie bronnen en kan niet worden blootgesteld aan klanten voor configuratie.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: micro soft

### <a name="22-configure-central-security-log-management"></a>2,2: Centraal beveiligings logboek beheer configureren

**Hulp**: opname logboeken gerelateerd aan Cognitive Search via Azure monitor om beveiligings gegevens te verzamelen die worden gegenereerd door eindpunt apparaten, netwerk bronnen en andere beveiligings systemen. Gebruik in Azure Monitor Log Analytics-werk ruimten om analyses uit te voeren en te uitvoeren en gebruik Azure Storage accounts voor lange termijn-en archiverings opslag.
U kunt deze gegevens ook in-of uitschakelen voor Azure Sentinel of een SIEM van derden.

- [Aan de slag met Azure Monitor en integratie van SIEM van derden](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

- [Platform logboeken en metrische gegevens verzamelen met Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md)

- [Azure-Sentinel onboarden](../sentinel/quickstart-onboard.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: controle logboek registratie inschakelen voor Azure-resources

**Richt lijnen**: diagnose-en operationele logboeken bieden inzicht in de gedetailleerde bewerkingen van Cognitive Search en zijn nuttig voor het bewaken van de service en voor werk belastingen die toegang hebben tot uw service.  Schakel logboek registratie in om diagnostische gegevens vast te leggen door op te geven waar de logboek gegevens worden opgeslagen.

- [Logboek gegevens verzamelen en analyseren voor Azure Cognitive Search](./search-monitor-logs.md)

- [Platform logboeken en metrische gegevens verzamelen met Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md) 

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: beveiligings logboeken verzamelen van besturings systemen

**Richt lijnen**: niet van toepassing op Cognitive Search. Deze aanbeveling is bedoeld voor reken resources.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="25-configure-security-log-storage-retention"></a>2,5: Bewaar beveiliging van het beveiligings logboek configureren

**Richt lijnen**: historische gegevens die in diagnostische meet waarden worden opgenomen, worden standaard 30 dagen bewaard door Cognitive Search. Zorg ervoor dat u voor langere retentie de instelling inschakelt waarmee een opslag optie wordt opgegeven voor het persistent maken van geregistreerde gebeurtenissen en metrische gegevens.

Stel in Azure Monitor de Bewaar periode voor uw Log Analytics werk ruimte in volgens de nalevings voorschriften van uw organisatie. Gebruik Azure Storage accounts voor lange termijn-en archiverings opslag. 

- [De Bewaar periode voor gegevens wijzigen in Log Analytics](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

- [Bewaar beleid configureren voor logboeken van Azure Storage-account](../storage/common/storage-monitor-storage-account.md#configure-logging)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="26-monitor-and-review-logs"></a>2,6: Logboeken bewaken en controleren

**Richt lijnen**: Logboeken analyseren en bewaken vanuit uw Cognitive Search-service voor afwijkend gedrag. Gebruik de Log Analytics van Azure Monitor om logboeken te controleren en query's uit te voeren op logboek gegevens. U kunt ook gegevens in-of uitschakelen voor Azure Sentinel of een SIEM van derden.

- [Logboek gegevens voor Cognitive Search verzamelen en analyseren](./search-monitor-logs.md)

- [Zoek logboek gegevens in Power BI visualiseren](./search-monitor-logs-powerbi.md)

- [Azure-Sentinel onboarden](../sentinel/quickstart-onboard.md)

- [Meer informatie over Log Analytics](../azure-monitor/logs/log-analytics-tutorial.md)

- [Aangepaste query's uitvoeren in Azure Monitor](../azure-monitor/logs/get-started-queries.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: waarschuwingen inschakelen voor afwijkende activiteiten

**Hulp**: gebruik Security Center met log Analytics werk ruimte voor bewaking en waarschuwingen over afwijkende activiteiten die in beveiligings logboeken en gebeurtenissen zijn gevonden. U kunt ook gegevens naar Azure-Sentinel inschakelen en op het bord zetten.

- [Azure-Sentinel onboarden](../sentinel/quickstart-onboard.md)

- [Waarschuwingen beheren in Azure Security Center](../security-center/security-center-managing-and-responding-alerts.md)

- [Een waarschuwing over logboek gegevens van log Analytics](../azure-monitor/alerts/tutorial-response.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="28-centralize-anti-malware-logging"></a>2,8: registratie van anti-malware centraliseren

**Richt lijnen**: niet van toepassing op Cognitive Search. Micro soft beheert de anti-malware-oplossing voor het onderliggende platform.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="29-enable-dns-query-logging"></a>2,9: DNS-query logboek registratie inschakelen

**Richt lijnen**: niet van toepassing op Cognitive Search. Er worden geen DNS-logboeken geproduceerd of gebruikt.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="210-enable-command-line-audit-logging"></a>2,10: controle logboek registratie op opdracht regel inschakelen

**Richt lijnen**: niet van toepassing op Cognitive Search. Het controleren van de opdracht regel is niet beschikbaar voor Cognitive Search.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

## <a name="identity-and-access-control"></a>Identiteits- en toegangsbeheer

*Zie [Azure Security Bench Mark: identiteits-en toegangs beheer](../security/benchmarks/security-control-identity-access-control.md)voor meer informatie.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: een inventaris van beheerders accounts onderhouden

**Richt lijnen**: met Azure op rollen gebaseerd toegangs beheer (Azure RBAC) kunt u de toegang tot Azure-resources beheren via roltoewijzingen. U kunt deze rollen toewijzen aan gebruikers, groeperingen van service-principals en beheerde identiteiten. Er bestaan vooraf gedefinieerde, ingebouwde rollen voor bepaalde resources, en deze rollen kunnen worden geïnventariseerd of opgevraagd via tools zoals Azure CLI, Azure PowerShell en Azure Portal.

Cognitive Search rollen zijn gekoppeld aan machtigingen die beheer taken op service niveau ondersteunen.  Deze rollen verlenen geen toegang tot het service-eind punt. Toegang tot bewerkingen op het eind punt (zoals index beheer, index populatie en query's op zoek gegevens). Gebruik de API-sleutels om de aanvraag te verifiëren.

- [Rollen instellen voor beheerders toegang tot Azure Cognitive Search](./search-security-rbac.md)

- [API-sleutels voor een Azure Cognitive Search-service maken en beheren](./search-security-api-keys.md)

- [Een directory-rol verkrijgen in azure AD met Power shell](/powershell/module/azuread/get-azureaddirectoryrole)
- [Leden van een directory-rol in azure AD ophalen met Power shell](/powershell/module/azuread/get-azureaddirectoryrolemember)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="32-change-default-passwords-where-applicable"></a>3,2: standaard wachtwoorden wijzigen indien van toepassing

**Richt lijnen**: niet van toepassing op Cognitive Search. Het bevat geen concept van standaard wachtwoorden.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: speciale beheerders accounts gebruiken

**Hulp**: Cognitive Search heeft niet het concept van lokale-of Azure Active Directory (Azure AD)-beheerders accounts die kunnen worden gebruikt voor het beheren van indexen en bewerkingen. 

Gebruik de ingebouwde Azure AD-rollen die expliciet moeten worden toegewezen voor beheer bewerkingen. Roep de Azure AD Power shell-module aan om ad-hoc-query's uit te voeren om accounts te detecteren die lid zijn van beheer groepen.

- [Rollen gebruiken voor beheerders toegang in Cognitive Search](./search-security-rbac.md)

- [Een directory-rol verkrijgen in azure AD met Power shell](/powershell/module/azuread/get-azureaddirectoryrole)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: eenmalige aanmelding (SSO) met Azure Active Directory gebruiken

**Hulp**: SSO-verificatie met Azure Active Directory (Azure AD) gebruiken om toegang te krijgen tot zoek service-informatie voor beheer bewerkingen die via Azure Resource Manager worden ondersteund. 

Stel een proces in om het aantal identiteiten en referenties te verminderen door eenmalige aanmelding in te scha kelen voor de service met de bestaande identiteiten van uw organisatie.

- [Informatie over eenmalige aanmelding met Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: multi-factor Authentication gebruiken voor alle op Azure Active Directory gebaseerde toegang

**Hulp** bij het inschakelen van de functie voor het maken van Azure Active Directory (Azure AD) multi-factor Authentication (MFA) en het volgen van de aanbevelingen voor de identiteit en toegang van Security Center.

- [MFA inschakelen in Azure](../active-directory/authentication/howto-mfa-getstarted.md) 

- [Identiteit en toegang bewaken in Azure Security Center](../security-center/security-center-identity-access.md) 

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: gebruik speciale machines (privileged Access workstations) voor alle beheer taken

**Hulp**: gebruik een privileged Access-werk station (Paw) met multi-factor Authentication (MFA) dat is geconfigureerd om u aan te melden bij en toegang te krijgen tot Azure-resources.

- [Meer informatie over veilige, door Azure beheerde werk stations](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)
 

- [Azure AD MFA inschakelen](../active-directory/authentication/howto-mfa-getstarted.md)
 

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: Logboeken en waarschuwingen voor verdachte activiteiten van beheerders accounts

**Hulp**: gebruik Azure Active Directory (Azure AD) beveiligings rapporten en-bewaking om te detecteren wanneer verdachte of onveilige activiteiten in de omgeving worden uitgevoerd. Gebruik Security Center om identiteits-en toegangs activiteiten te bewaken.

- [Azure AD-gebruikers identificeren die zijn gemarkeerd voor riskante activiteiten](../active-directory/identity-protection/overview-identity-protection.md)

- [Identiteits- en toegangsactiviteiten van gebruikers controleren in Azure Security Center](../security-center/security-center-identity-access.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="38-manage-azure-resources-only-from-approved-locations"></a>3,8: beheer Azure-resources alleen op goedgekeurde locaties

**Richt lijnen**: niet van toepassing op Cognitive Search. Het gebruik van een goedgekeurde locatie als voor waarde voor toegang wordt niet ondersteund.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="39-use-azure-active-directory"></a>3,9: Azure Active Directory gebruiken

**Hulp**: gebruik Azure Active Directory (Azure AD) als het centrale verificatie-en autorisatie systeem voor beheer taken op service niveau in azure Cognitive Search. Azure AD-identiteiten verlenen geen toegang tot het eind punt van de zoek service.  Toegang tot bewerkingen, zoals index beheer, index populatie en query's op zoek gegevens, zijn beschikbaar via API-sleutels.

- [Een Azure AD-instantie maken en configureren](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [API-sleutels voor een Azure Cognitive Search-service maken en beheren](./search-security-api-keys.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: regel matig gebruikers toegang controleren en afstemmen

**Hulp**: Azure Active Directory (Azure AD) biedt logboeken waarmee u verlopen accounts kunt detecteren. Gebruik de beoordelingen van Azure AD voor identiteits-en toegangs beheer om groepslid maatschappen en de toegang tot bedrijfs toepassingen en roltoewijzingen op efficiënte wijze te beheren. Gebruikers toegang kan regel matig worden gecontroleerd om ervoor te zorgen dat alleen de juiste gebruikers toegang hebben. 

Bekijk de diagnostische logboeken van Cognitive Search voor activiteit in het eind punt van de zoek service, zoals index beheer, index populatie en query's.

- [Meer informatie over Azure AD-rapportage](../active-directory/reports-monitoring/index.yml)

- [Identiteits- en toegangsbeoordelingen van Azure AD](../active-directory/governance/access-reviews-overview.md)

- [Bewerkingen en activiteiten van Azure Cognitive Search bewaken](./search-monitor-usage.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: controle pogingen om toegang te krijgen tot gedeactiveerde referenties

**Hulp**: u kunt met behulp van toegang tot Azure Active Directory-, audit-en risico logboek bronnen voor het aanmelden van een Azure AD-activiteit, de controle en de Risk

Stroom lijn dit proces door Diagnostische instellingen voor Azure AD-gebruikers accounts te maken en de audit logboeken en aanmeldings logboeken te verzenden naar een Log Analytics-werk ruimte. Gewenste waarschuwingen configureren in Log Analytics werk ruimte.

- [Azure-activiteiten logboeken integreren met Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) 

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: waarschuwing voor de afwijking van het aanmeldings gedrag van accounts

**Hulp**: gebruik Azure Active Directory-functies (Azure AD) voor identiteits beveiliging om automatische antwoorden te configureren op gedetecteerde verdachte acties die betrekking hebben op gebruikers identiteiten. Gegevens opnemen in azure Sentinel voor verder onderzoek, indien nodig.

- [Riskante Azure AD-aanmeldingen weergeven](../active-directory/identity-protection/overview-identity-protection.md) 

- [Risico beleid voor identiteits beveiliging configureren en inschakelen](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md) 

- [Azure-Sentinel onboarden](../sentinel/quickstart-onboard.md) 

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: micro soft biedt toegang tot relevante klant gegevens tijdens ondersteunings scenario's

**Richt lijnen**: niet van toepassing op Cognitive Search. Klanten-lockbox biedt geen ondersteuning voor Cognitive Search.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

## <a name="data-protection"></a>Gegevensbeveiliging

*Zie [Azure Security Bench Mark: Data Protection](../security/benchmarks/security-control-data-protection.md)voor meer informatie.*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: een inventaris van gevoelige informatie onderhouden

**Hulp**: Tags gebruiken om Azure-resources te helpen bij het bijhouden of verwerken van gevoelige informatie.

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: systemen isoleren die gevoelige informatie opslaan of verwerken

**Richt lijnen**: afzonderlijke abonnementen en/of beheer groepen implementeren voor ontwikkeling, testen en productie. Resources moeten worden gescheiden door het virtuele netwerk/subnet, op de juiste wijze worden gelabeld en beveiligd in een netwerk beveiligings groep of Azure Firewall. Resources die gevoelige gegevens opslaan of verwerken, moeten worden geïsoleerd. Gebruik een persoonlijke koppeling om een persoonlijk eind punt te configureren voor Cognitive Search.

- [Aanvullende Azure-abonnementen maken](../cost-management-billing/manage/create-subscription.md) 

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

- [Een persoonlijk eind punt maken voor Cognitive Search](./service-create-private-endpoint.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: niet-geautoriseerde overdracht van gevoelige gegevens controleren en blok keren

**Richt lijnen**: gebruik een oplossing van een derde partij van Azure Marketplace in netwerk verbindingen om te controleren op niet-geautoriseerde overdracht van gevoelige informatie en om dergelijke overdrachten te blok keren terwijl u waarschuwt voor informatie beveiliging.

Micro soft beheert het onderliggende platform en behandelt alle inhoud van de klant als gevoelig en bescherming tegen verlies en bloot stelling van klant gegevens. Om ervoor te zorgen dat klant gegevens binnen Azure veilig blijven, heeft micro soft een reeks robuuste besturings elementen en mogelijkheden voor gegevens bescherming geïmplementeerd en onderhouden.

- [Informatie over beveiliging van klantgegevens in Azure](../security/fundamentals/protection-customer-data.md) 

**Monitoring door Azure Security Center**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: alle gevoelige gegevens in de overdracht versleutelen

**Hulp**: Cognitive Search versleutelt gegevens tijdens de overdracht met Transport Layer Security 1,2 en dwingt te allen tijde VERSLEUTELING (SSL/TLS) af voor alle verbindingen. Dit zorgt ervoor dat alle gegevens worden versleuteld in transit tussen de client en de service.

- [Meer informatie over versleuteling in transit met Azure](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit) 

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: micro soft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: een actief detectie hulpprogramma gebruiken om gevoelige gegevens te identificeren

**Hulp**: de functies gegevens identificatie, classificatie en verlies preventie zijn nog niet beschikbaar voor Cognitive Search. Implementeer, indien nodig, een oplossing van derden voor naleving. 

Micro soft beheert het onderliggende platform en behandelt alle inhoud van de klant als gevoelig en bescherming tegen verlies en bloot stelling van klant gegevens. Om ervoor te zorgen dat klant gegevens binnen Azure veilig blijven, heeft micro soft een reeks robuuste besturings elementen en mogelijkheden voor gegevens bescherming geïmplementeerd en onderhouden.

- [Informatie over beveiliging van klantgegevens in Azure](../security/fundamentals/protection-customer-data.md)

**Monitoring door Azure Security Center**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4,6: Azure RBAC gebruiken om de toegang tot resources te beheren

**Richt lijnen**: voor Service beheer gebruikt u Azure RBAC (op rollen gebaseerd toegangs beheer) voor het beheren van de toegang tot sleutels en configuratie. Voor inhouds bewerkingen, zoals indexering en query's, gebruikt Cognitive Search sleutels in plaats van een model voor toegangs beheer op basis van een identiteit. Gebruik Azure RBAC om de toegang tot sleutels te beheren.
- [RBAC configureren in Azure](../role-based-access-control/role-assignments-portal.md) 

 
- [Rollen gebruiken voor beheerders toegang tot Cognitive Search](./search-security-rbac.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: voor komen dat gegevens verlies op basis van host wordt gebruikt voor het afdwingen van toegangs beheer

**Richt lijnen**: niet van toepassing op Cognitive Search. Deze richt lijn is bedoeld voor reken resources. 

Micro soft beheert de onderliggende infra structuur voor Cognitive Search en heeft strikte controles geïmplementeerd om verlies of bloot stelling van klant gegevens te voor komen.

- [Informatie over beveiliging van klantgegevens in Azure](../security/fundamentals/protection-customer-data.md)

**Monitoring door Azure Security Center**: Niet van toepassing

**Verantwoordelijkheid**: micro soft

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: gevoelige informatie op rest versleutelen

**Hulp**: Cognitive Search automatisch geïndexeerde inhoud versleutelen met door micro soft beheerde sleutels. Als er meer beveiliging nodig is, kunt u standaard versleuteling aanvullen met een tweede versleutelings laag met behulp van sleutels die u in Azure Key Vault maakt en beheert.

- [Door de klant beheerde sleutels voor gegevens versleuteling configureren in azure Cognitive Search](./search-security-manage-encryption-keys.md)

- [Meer informatie over versleuteling van data-at-rest in Azure](../security/fundamentals/encryption-atrest.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Gedeeld

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: wijzigingen in essentiële Azure-resources vastleggen en waarschuwen

**Hulp**: gebruik Azure monitor met het Azure-activiteiten logboek om waarschuwingen te maken wanneer wijzigingen worden aangebracht in productie-exemplaren van Cognitive Search en andere essentiële of gerelateerde resources.

- [Waarschuwingen maken voor gebeurtenissen in het Azure-activiteiten logboek](../azure-monitor/alerts/alerts-activity-log.md)

- [Waarschuwingen voor Cognitive Search activiteiten maken](./search-monitor-logs.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

## <a name="vulnerability-management"></a>Beheer van beveiligingsproblemen

*Zie voor meer informatie de [Azure Security Bench Mark: beveiligingslek beheer](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: automatische hulpprogram ma's voor het scannen van beveiligings problemen uitvoeren

**Hulp**: momenteel niet beschikbaar voor Cognitive Search.  Micro soft is verantwoordelijk voor clusters waarin inhoud van de zoek service wordt opgeslagen.

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: micro soft

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: geautomatiseerde oplossing voor patch beheer voor besturings systemen implementeren

**Richt lijnen**: niet van toepassing op Cognitive Search. Deze aanbeveling is bedoeld voor reken resources.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="53-deploy-an-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: een geautomatiseerde oplossing voor patch beheer implementeren voor titels van software van derden

**Richt lijnen**: niet van toepassing op Cognitive Search. Deze aanbeveling is bedoeld voor reken resources.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: vergelijken van back-to-back-problemen

**Richt lijnen**: niet van toepassing op Cognitive Search. Micro soft voert beveiligings beheer uit op de onderliggende systemen die ondersteuning bieden voor Cognitive Search Services.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: micro soft

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: een risico classificatie proces gebruiken om prioriteit te geven aan het herstel van ontdekte beveiligings problemen

**Richt lijnen**: niet van toepassing op Cognitive Search. Er is geen standaard risico classificatie-of score systeem voor de resultaten van de beveiligings scan.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

## <a name="inventory-and-asset-management"></a>Inventarisatie en asset-management

*Zie [Azure Security Bench Mark: Inventory and Asset Management](../security/benchmarks/security-control-inventory-asset-management.md)voor meer informatie.*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: automatische Asset-detectie oplossing gebruiken

**Hulp**: Azure resource Graph gebruiken om alle resources (zoals compute, opslag, netwerk, poorten, protocollen enzovoort) in uw abonnementen op te vragen en te detecteren.  

Zorg ervoor dat de juiste (Lees-) machtigingen voor uw Tenant zijn en alle Azure-abonnementen en de resources in uw abonnementen inventariseren.  

- [Query's maken met Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md) 

- [Uw Azure-abonnementen weer geven](/powershell/module/az.accounts/get-azsubscription) 

- [Meer informatie over Azure RBAC](../role-based-access-control/overview.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="62-maintain-asset-metadata"></a>6,2: meta gegevens van activa onderhouden

**Richt lijnen**: Tags Toep assen op Azure-resources met meta gegevens om ze logisch in een taxonomie te organiseren.

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: niet-geautoriseerde Azure-resources verwijderen

**Richt lijnen**: Gebruik labels, beheer groepen en afzonderlijke abonnementen, indien van toepassing, om assets te organiseren en bij te houden. Sluit de inventaris regel matig af en zorg ervoor dat niet-geautoriseerde resources tijdig worden verwijderd uit het abonnement.
- [Aanvullende Azure-abonnementen maken](../cost-management-billing/manage/create-subscription.md) 

- [Beheergroepen maken](../governance/management-groups/create-management-group-portal.md) 

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md) 

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6,4: een inventaris van goedgekeurde Azure-resources definiëren en onderhouden

**Richt lijnen**: Definieer een lijst met goedgekeurde Azure-resources met betrekking tot indexering en vaardigheidset-verwerking in cognitive Search.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: monitor voor niet-goedgekeurde Azure-resources

**Richt lijnen**: het wordt aanbevolen een inventaris te definiëren van Azure-resources die zijn goedgekeurd voor gebruik volgens uw organisatie beleid en standaarden, en vervolgens te controleren op niet-goedgekeurde Azure-resources met Azure Policy of Azure resource Graph.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md) 

- [Query's maken met Azure Graph](../governance/resource-graph/first-query-portal.md) 

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: monitor voor niet-goedgekeurde software toepassingen binnen reken resources

**Richt lijnen**: niet van toepassing op Cognitive Search. Deze richt lijnen zijn bedoeld voor reken resources.

Het is raadzaam dat u beschikt over een inventarisatie van software toepassingen die zijn goedgekeurd volgens uw organisatie beleid en beveiligings standaarden, en om te controleren of er niet-goedgekeurde software titels zijn geïnstalleerd op uw Azure Compute-resources.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: niet-goedgekeurde Azure-resources en software toepassingen verwijderen

**Richt lijnen**: niet van toepassing op Cognitive Search. Deze aanbeveling is bedoeld voor reken resources.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="68-use-only-approved-applications"></a>6,8: alleen goedgekeurde toepassingen gebruiken

**Richt lijnen**: niet van toepassing op Cognitive Search. Er worden geen reken bronnen weer gegeven of software toepassingen kunnen worden geïnstalleerd op een van de bronnen.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="69-use-only-approved-azure-services"></a>6,9: alleen goedgekeurde Azure-Services gebruiken

**Hulp: gebruik** Azure Policy om beperkingen te plaatsen voor het type resources dat kan worden gemaakt in klant abonnementen met de volgende ingebouwde beleids definities:

- Niet toegestane resourcetypen
- Toegestane brontypen

Gebruik Azure resource Graph om resources binnen uw abonnement (en) te doorzoeken of te detecteren. Zorg ervoor dat alle Azure-resources die aanwezig zijn in de omgeving, zijn goedgekeurd.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md) 

- [Een specifiek resource type weigeren met Azure Policy](../governance/policy/samples/index.md) 

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: een inventaris van goedgekeurde software titels onderhouden

**Richt lijnen**: niet van toepassing op Cognitive Search. Deze aanbeveling is bedoeld voor toepassingen die worden uitgevoerd op reken resources.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: de mogelijkheid van gebruikers om te communiceren met Azure Resource Manager beperken

**Richt lijnen**: voor Service beheer gebruikt u voorwaardelijke toegang van Azure om de interactie van gebruikers met Azure Resource Manager te beperken door ' blok toegang ' te configureren voor de app Microsoft Azure management. 

Beheer de toegang tot de sleutels die worden gebruikt voor het verifiëren van aanvragen voor alle andere bewerkingen, met name die met betrekking tot inhoud met Cognitive Search.

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="612-limit-users-ability-to-execute-scripts-in-compute-resources"></a>6,12: de mogelijkheid van gebruikers om scripts in reken bronnen uit te voeren, beperken

**Richt lijnen**: niet van toepassing op Cognitive Search. Deze aanbeveling is bedoeld voor reken resources.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: toepassingen met een hoog risico fysiek of logisch scheiden

**Richt lijnen**: niet van toepassing op Cognitive Search. Deze aanbeveling is bedoeld voor webtoepassingen die worden uitgevoerd op Azure App Service of reken bronnen.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

## <a name="secure-configuration"></a>Veilige configuratie

*Zie [Azure Security Bench Mark: Secure Configuration](../security/benchmarks/security-control-secure-configuration.md)(Engelstalig) voor meer informatie.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: veilige configuraties instellen voor alle Azure-resources

**Hulp**: gebruik Azure Policy aliassen in de naam ruimte ' micro soft. Search ' om aangepaste beleids regels te maken om de configuratie van uw Azure Cognitive Search-resources te controleren of af te dwingen. U kunt ook ingebouwde Azure Policy definities voor Cognitive Search Services gebruiken, zoals:

- Auditlogboekregistratie voor Azure-resources inschakelen

Azure Resource Manager kunt de sjabloon in JavaScript Object Notation (JSON) exporteren, die moet worden gecontroleerd om ervoor te zorgen dat de configuraties voldoen aan de beveiligings vereisten voor uw organisatie. 

U kunt ook de aanbevelingen van Azure Security Center gebruiken als een veilige configuratie basislijn voor uw Azure-resources. 

- [Besturingselementen voor Naleving van Azure Policy-regelgeving voor Azure Cognitive Search](./security-controls-policy.md)

- [Beschik bare Azure Policy aliassen weer geven](/powershell/module/az.resources/get-azpolicyalias)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: veilige configuraties van besturings systemen instellen

**Richt lijnen**: niet van toepassing op Cognitive Search. Deze aanbeveling is bedoeld voor reken resources.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: Beveilig Azure-resource configuraties onderhouden

**Hulp**: gebruik Azure Policy [deny] en [indien aanwezig] effecten om beveiligde instellingen af te dwingen voor uw Cognitive Search service bronnen. 

Azure Resource Manager sjablonen kunnen worden gebruikt voor het onderhouden van de beveiligings configuratie van uw Azure-resources die uw organisatie nodig heeft. 

- [Azure Policy effecten begrijpen](../governance/policy/concepts/effects.md)

- [Besturingselementen voor Naleving van Azure Policy-regelgeving voor Azure Cognitive Search](./security-controls-policy.md)

- [Beleidsregels voor het afdwingen van naleving maken en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Overzicht van Azure Resource Manager sjablonen](../azure-resource-manager/templates/overview.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: veilige configuraties van besturings systemen onderhouden

**Richt lijnen**: niet van toepassing op Cognitive Search. Deze aanbeveling is bedoeld voor reken resources.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: de configuratie van Azure-resources veilig opslaan

**Richt lijnen**: als u aangepaste Azure Policy definities gebruikt, kunt u Azure DevOps of Azure opslag plaatsen gebruiken om uw code veilig op te slaan en te beheren.

- [Code opslaan in azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Documentatie voor Azure opslag plaatsen](/azure/devops/repos/index)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: aangepaste installatie kopieën van een besturings systeem veilig opslaan

**Richt lijnen**: niet van toepassing op Cognitive Search. Deze aanbeveling is bedoeld voor reken resources.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: hulpprogram ma's voor configuratie beheer voor Azure-resources implementeren

**Hulp**: Definieer en implementeer standaard beveiligings configuraties voor uw Cognitive Search-service resources met behulp van Azure Policy. 

Gebruik aliassen om aangepast beleid te maken om de netwerk configuraties te controleren of af te dwingen. U kunt ook gebruik maken van ingebouwde beleids definities die betrekking hebben op uw specifieke resources. 

Daarnaast kunt u Azure Automation gebruiken om configuratie wijzigingen te implementeren en beleids uitzonderingen te beheren. 

- [Besturingselementen voor Naleving van Azure Policy-regelgeving voor Azure Cognitive Search](./security-controls-policy.md)

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: hulpprogram ma's voor configuratie beheer voor besturings systemen implementeren

**Richt lijnen**: niet van toepassing op Cognitive Search. Deze aanbeveling is bedoeld voor reken resources.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: geautomatiseerde configuratie bewaking voor Azure-resources implementeren

**Hulp**: gebruik Security Center voor het uitvoeren van basislijn scans van uw Cognitive Search-service resources.  Gebruik Azure Policy bovendien om uw resource configuraties te waarschuwen en te controleren. 

- [Aanbevelingen herstellen in Azure Security Center](../security-center/security-center-remediate-recommendations.md)

- [Besturingselementen voor Naleving van Azure Policy-regelgeving voor Azure Cognitive Search](./security-controls-policy.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: geautomatiseerde configuratie bewaking voor besturings systemen implementeren

**Richt lijnen**: niet van toepassing op Cognitive Search. Deze aanbeveling is bedoeld voor reken resources.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="711-manage-azure-secrets-securely"></a>7,11: Azure-geheimen veilig beheren

**Hulp**: Azure Managed identiteiten gebruiken in combi natie met Azure Key Vault om het geheime beheer voor uw Cloud toepassingen te vereenvoudigen.
- [Beheerde identiteiten gebruiken voor Azure-resources](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md) 
- [Een Key Vault maken](../key-vault/general/quick-create-portal.md) 

- [Key Vault verificatie bieden met een beheerde identiteit](../key-vault/general/assign-access-policy-portal.md) 

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: identiteiten veilig en automatisch beheren

**Hulp**: een door Azure beheerde identiteit gebruiken om Cognitive Search toegang te geven tot andere Azure-Services, zoals Key Vault en Indexeer functie gegevens bronnen met behulp van een automatisch beheerde identiteit in azure Active Directory (Azure AD). Met beheerde identiteiten kunt u zich verifiëren bij elke service die ondersteuning biedt voor Azure AD-verificatie, met inbegrip van Azure Key Vault, zonder dat u referenties in uw code hoeft op te geven. 

- [Een Indexeer functie verbinding met een gegevens bron instellen met behulp van een beheerde identiteit](./search-howto-managed-identities-data-sources.md)

- [Door de klant beheerde sleutels configureren voor gegevens versleuteling met behulp van een beheerde identiteit](./search-security-manage-encryption-keys.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: onbedoelde referentie blootstelling elimineren

**Richt lijnen**: niet van toepassing op Cognitive Search. Er wordt geen code gehost en er zijn geen referenties om te identificeren.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

## <a name="malware-defense"></a>Beveiliging tegen malware

*Zie voor meer informatie de [Azure Security Bench Mark: beveiliging tegen schadelijke software](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-antimalware-software"></a>8,1: centraal beheerde antimalware-software gebruiken

**Richt lijnen**: niet van toepassing op Cognitive Search. Deze aanbeveling is bedoeld voor reken resources.

Micro soft anti-malware is ingeschakeld op de onderliggende host die ondersteuning biedt voor Azure-Services (bijvoorbeeld Azure Cognitive Search), maar wordt niet uitgevoerd op de inhoud van de klant.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: scan bestanden die moeten worden geüpload naar niet-reken resources van Azure

**Richt lijnen**: het vooraf scannen van inhoud die wordt geüpload naar niet-reken resources van Azure, zoals Cognitive Search, Blob Storage, Azure SQL database, enzovoort. 

Het is uw verantwoordelijkheid om vooraf te scannen op inhoud die wordt geüpload naar niet-reken resources van Azure. Micro soft heeft geen toegang tot klant gegevens en kan daarom geen anti-malware scans uitvoeren van klant inhoud namens u.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="83-ensure-antimalware-software-and-signatures-are-updated"></a>8,3: controleren of antimalware-software en hand tekeningen zijn bijgewerkt

**Richt lijnen**: niet van toepassing op Cognitive Search. Het is niet toegestaan om anti-malware-oplossingen te installeren op de resources. Voor het onderliggende platform behandelt micro soft het bijwerken van software en hand tekeningen van malware. 

Voor alle reken resources die eigendom zijn van uw organisatie en worden gebruikt in uw zoek oplossing, volgt u aanbevelingen in Security Center, reken &amp; apps om ervoor te zorgen dat alle eind punten up-to-date zijn met de nieuwste hand tekeningen. Gebruik voor Linux een anti-malware-oplossing van derden.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Gedeeld

## <a name="data-recovery"></a>Gegevensherstel

*Zie [Azure Security Bench Mark: Data Recovery](../security/benchmarks/security-control-data-recovery.md)(Engelstalig) voor meer informatie.*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: controleren op regel matige automatische back-ups

**Hulp**: er kan geen back-up worden gemaakt van inhoud die is opgeslagen in een zoek service via Azure backup of een ander ingebouwd mechanisme, maar u kunt een index opnieuw samen stellen op basis van de bron code van de toepassing en primaire gegevens bronnen, of een aangepast hulp programma bouwen om geïndexeerde inhoud op te halen en op te slaan.

- [GitHub-index: back-up maken-voor beeld herstellen](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/index-backup-restore)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: volledige back-ups van het systeem uitvoeren en een back-up maken van door de klant beheerde sleutels

**Hulp**: Cognitive Search biedt momenteel geen ondersteuning voor automatische back-ups voor gegevens in een zoek service en waarvan u een back-up moet maken via een hand matig proces.  U kunt ook een back-up van door de klant beheerde sleutels in Azure Key Vault. 

- [Back-ups van een Azure Cognitive Search-index maken en deze herstellen](/samples/azure-samples/azure-search-dotnet-samples/azure-search-backup-restore-index/)

- [Back-ups maken van Key Vault sleutels in azure](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: alle back-ups valideren, inclusief door de klant beheerde sleutels

**Hulp**: Cognitive Search biedt momenteel geen ondersteuning voor automatische back-ups voor gegevens in een zoek service. u moet een back-up maken en deze herstellen via een hand matig proces.  Maak regel matig een back-up van de inhoud die u hand matig hebt gemaakt om de end-to-end integriteit van uw back-upproces te controleren.

- [Back-ups van een Azure Cognitive Search-index maken en deze herstellen](/samples/azure-samples/azure-search-dotnet-samples/azure-search-backup-restore-index/)

- [Key Vault sleutels herstellen in azure](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: zorg voor de bescherming van back-ups en door de klant beheerde sleutels

**Hulp**: Cognitive Search biedt momenteel geen ondersteuning voor automatische back-ups voor gegevens in een zoek service en waarvan een back-up moet worden gemaakt via een hand matig proces.  U kunt ook een back-up van door de klant beheerde sleutels in Azure Key Vault. 

Schakel de beveiliging van zacht verwijderen en opschonen in Key Vault in om sleutels te beschermen tegen onbedoelde of schadelijke verwijdering. Als Azure Storage wordt gebruikt om hand matige back-ups op te slaan, kunt u met de optie voor het uitvoeren van de functie voor voorlopig verwijderen uw gegevens opslaan en herstellen wanneer blobs of BLOB-moment opnamen worden verwijderd. 

- [Back-ups van een Azure Cognitive Search-index maken en deze herstellen](/samples/azure-samples/azure-search-dotnet-samples/azure-search-backup-restore-index/)

- [Voorlopig verwijderen en bescherming tegen opschonen in Azure Key Vault inschakelen](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

- [Zacht verwijderen voor Azure Blob Storage](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

## <a name="incident-response"></a>Reageren op incidenten

*Zie [Azure Security Bench Mark: Incident Response](../security/benchmarks/security-control-incident-response.md)(Engelstalig) voor meer informatie.*

### <a name="101-create-an-incident-response-guide"></a>10,1: een hand leiding voor reactie op incidenten maken

**Hulp**: een antwoord gids voor incidenten ontwikkelen voor uw organisatie. Zorg ervoor dat er schriftelijke incidenten abonnementen zijn die alle personeels rollen definiëren, evenals de fasen van het verwerken van incidenten en het beheer van de detectie tot een beoordeling van het incident.

- [Richt lijnen voor het bouwen van uw eigen beveiligings incident antwoord proces](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Micro soft Security Response Center anatomie van een incident](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Klant kan ook gebruikmaken van de hand leiding voor de verwerking van het computer beveiligings incident van het NIST om hulp te bieden bij het maken van een eigen incident respons plan](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: een beoordelings procedure voor incidenten en prioriteits procedures maken

**Hulp**: Security Center wijst aan elke waarschuwing een Ernst toe om u te helpen bepalen welke waarschuwingen het eerst moeten worden onderzocht. De ernst is gebaseerd op de manier waarop vertrouwen Security Center zich in de zoek actie bevindt of de analyse die wordt gebruikt om de waarschuwing te geven, evenals het betrouwbaarheids niveau dat er schadelijke intentie is achter de activiteit die tot de waarschuwing heeft geleid.

Markeer bovendien abonnementen met tags en maak een naamgevings systeem voor het identificeren en categoriseren van Azure-resources, met name voor de verwerking van gevoelige gegevens. Het is uw verantwoordelijkheid om prioriteit te geven aan het herstel van waarschuwingen op basis van de ernst van de Azure-resources en-omgeving waar het incident heeft plaatsgevonden.

- [Tags gebruiken om Azure-resources te organiseren](../azure-resource-manager/management/tag-resources.md)

- [Beveiligingswaarschuwingen in Azure Security Center](../security-center/security-center-alerts-overview.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="103-test-security-response-procedures"></a>10,3: procedures voor beveiligings antwoorden testen

**Richt lijnen**: oefent oefeningen uit om de respons mogelijkheden van uw systeem op een gewone uitgebracht te testen. Stel vast waar zich zwakke plekken en hiaten bevinden, en wijzig zo nodig het plan.

- [Raadpleeg de publicatie van het NIST, "hand leiding voor het testen, trainen en uitoefenen van Program Ma's voor IT-plannen en-mogelijkheden"](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: contact gegevens van het beveiligings incident opgeven en waarschuwings meldingen configureren voor beveiligings incidenten

**Hulp**: contact gegevens van beveiligings incidenten worden door micro soft gebruikt om contact met u op te nemen als het micro soft Security Response Center (MSRC) detecteert dat uw gegevens zijn geopend door een onrecht matige of niet-gemachtigde partij. Bekijk incidenten na het feit om te controleren of de problemen zijn opgelost.

- [De Azure Security Center Security-contact persoon instellen](../security-center/security-center-provide-security-contact-details.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: beveiligings waarschuwingen opnemen in uw reactie systeem van uw incident

**Richt lijnen**: uw Security Center waarschuwingen en aanbevelingen exporteren met de functie continue export. Met doorlopend exporteren kunt u waarschuwingen en aanbevelingen hand matig of op een doorlopende basis exporteren. U kunt de Security Center Data Connector gebruiken om de waarschuwingen naar Azure Sentinel te streamen.

- [Continue export configureren](../security-center/continuous-export.md)

- [Waarschuwingen streamen naar Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: de reactie op beveiligings waarschuwingen automatiseren

**Hulp**: gebruik de functie werk stroom automatisering in azure Security Center om automatisch reacties te activeren via ' Logic apps ' in beveiligings waarschuwingen en aanbevelingen.

- [Werk stroom automatisering en Logic Apps configureren](../security-center/workflow-automation.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

## <a name="penetration-tests-and-red-team-exercises"></a>Penetratietests en Red Team-oefeningen

*Zie [Azure Security Bench Mark: Indringings tests en rode team oefeningen](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)voor meer informatie.*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: voert regel matig indringings tests van uw Azure-resources uit en zorgt voor herbemiddeling van alle essentiële beveiligings resultaten

**Richt lijnen**: Volg de stappen voor het testen van de Microsoft Cloud indringings regels om ervoor te zorgen dat uw indringings tests niet worden geschonden door het micro soft-beleid. Gebruik de strategie van Microsoft en de uitvoering van Red Teaming-activiteiten, en voer een penetratietest van de live site uit op basis van een infrastructuur, services en toepassingen die door Microsoft worden beheerd.
- [Regels voor het inzetten van penetratietests](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)
- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Gedeeld

## <a name="next-steps"></a>Volgende stappen

- Zie de [Azure Security-Bench Mark](../security/benchmarks/overview.md)
- Meer informatie over [Azure-beveiligingsbasislijnen](../security/benchmarks/security-baselines-overview.md)