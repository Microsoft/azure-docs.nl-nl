---
title: Azure-beveiligingsbasislijn voor Key Vault
description: De Key Vault beveiligingsbasislijn biedt procedurele richtlijnen en resources voor het implementeren van de beveiligingsaanbevelingen die zijn opgegeven in de Azure Security-benchmark.
author: msmbaldwin
ms.service: key-vault
ms.topic: conceptual
ms.date: 03/29/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 1f56de94df4fd5d4dd154ae8485edb9eed88364c
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107753340"
---
# <a name="azure-security-baseline-for-key-vault"></a>Azure-beveiligingsbasislijn voor Key Vault

Met deze beveiligingsbasislijn worden richtlijnen van [azure Security Benchmark versie 1.0](../../security/benchmarks/overview-v1.md) toegepast op Key Vault. De Azure Security-benchmark biedt aanbevelingen voor hoe u uw cloudoplossingen in Azure kunt beveiligen. De inhoud wordt gegroepeerd op basis van **de** beveiligingscontroles die zijn gedefinieerd door de Azure Security Benchmark en de gerelateerde richtlijnen die van toepassing zijn op Key Vault. **Besturingselementen** die niet van toepassing zijn op Key Vault of waarvoor de verantwoordelijkheid van Microsoft is, zijn uitgesloten.

Zie het volledige Key Vault toewijzingsbestand voor beveiligingsbasislijnen om te zien hoe Key Vault volledig is toegewezen aan de Azure Security [Key Vault-benchmark.](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)

## <a name="network-security"></a>Netwerkbeveiliging

*Zie [Azure Security Benchmark: netwerkbeveiliging](../../security/benchmarks/security-control-network-security.md) voor meer informatie.*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Azure-resources binnen virtuele netwerken beveiligen

**Richtlijnen:** integratie Azure Key Vault met Azure Private Link. Azure Private Link Service kunt u toegang krijgen tot Azure-services (bijvoorbeeld Azure Key Vault) en in Azure gehoste klant-/partnerservices via een privé-eindpunt in uw virtuele netwerk.

Een privé-eindpunt in Azure is een netwerkinterface waarmee u privé en veilig verbinding maakt met een service die door Azure Private Link mogelijk wordt gemaakt. Het privé-eindpunt maakt gebruik van een privé-IP-adres van uw VNet, waardoor de service feitelijk in uw VNet wordt geplaatst. Al het verkeer naar de service kan worden gerouteerd via het privé-eindpunt, zodat er geen gateways, NAT-apparaten, ExpressRoute of VPN-verbindingen of openbare IP-adressen nodig zijn. Verkeer tussen uw virtuele netwerk en de services wordt via het backbonenetwerk van Microsoft geleid, waarmee de risico's van het openbare internet worden vermeden. U kunt verbinding maken met een exemplaar van een Azure-resource, zodat u het hoogste granulariteit krijgt in toegangsbeheer.

- [Integratie van Key Vault met Azure Private Link](/azure/key-vault/private-link-service)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** De [Azure Security-benchmark](/azure/governance/policy/samples/azure-security-benchmark) is het standaardbeleidsinitiatief voor Security Center en is de basis voor de aanbevelingen [Security Center van Security Center.](/azure/security-center/security-center-recommendations) De Azure Policy met betrekking tot dit besturingselement worden automatisch ingeschakeld door Security Center. Waarschuwingen met betrekking tot dit besturingselement vereisen mogelijk een [Azure Defender](/azure/security-center/azure-defender) voor de gerelateerde services.

**Azure Policy ingebouwde definities - Microsoft.KeyVault**:

[!INCLUDE [Resource Policy for Microsoft.KeyVault 1.1](../../../includes/policy/standards/asb/rp-controls/microsoft.keyvault-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2: De configuratie en het verkeer van virtuele netwerken, subnetten en netwerkinterfaces bewaken en logboeken

**Richtlijnen:** gebruik Azure Security Center en volg de aanbevelingen voor netwerkbeveiliging om uw met Key Vault geconfigureerde resources in Azure te beveiligen. 

- [Voor meer informatie over de netwerkbeveiliging van Azure Security Center](../../security-center/security-center-network-recommendations.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Communicatie met bekende schadelijke IP-adressen weigeren

**Richtlijnen:** Schakel Azure DDoS Protection Standard in op de virtuele Azure-netwerken die zijn gekoppeld aan uw Key Vault-exemplaren voor beveiliging tegen gedistribueerde Denial of Service-aanvallen. Gebruik Azure Security Center Integrated Threat Intelligence om communicatie met bekende schadelijke of ongebruikte INTERNET-IP-adressen te weigeren.

 
- [Azure DDoS Protection Standard beheren met behulp van de Azure Portal](/azure/virtual-network/manage-ddos-protection)

- [Detectie van bedreigingen voor de Azure-servicelaag in Azure Security Center](/azure/security-center/security-center-alerts-service-layer)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Op het netwerk gebaseerde inbraakdetectie-/inbraakpreventiesystemen (IDS/IPS) implementeren

**Richtlijnen:** aan deze vereiste kan worden voldaan door Advanced Threat Protection (ATP) te configureren voor Azure Key Vault. ATP biedt een extra laag beveiligingsinformatie. Dit hulpprogramma detecteert mogelijk schadelijke pogingen om toegang te krijgen tot of aanvallen uit te Azure Key Vault accounts.

Wanneer Azure Security Center afwijkende activiteit detecteert, worden waarschuwingen weergegeven. Er wordt ook een e-mail verzonden naar de abonnementsbeheerder met details van de verdachte activiteit en aanbevelingen voor het onderzoeken en verhelpen van de geïdentificeerde bedreigingen.

- [Advanced beveiliging tegen bedreigingen instellen voor Azure Key Vault](/azure/security-center/advanced-threat-protection-key-vault)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: De complexiteit en administratieve overhead van netwerkbeveiligingsregels minimaliseren

**Richtlijnen:** voor resources die toegang nodig hebben tot uw Azure Key Vault-exemplaren, gebruikt u Azure-servicetags voor de Azure Key Vault voor het definiëren van besturingselementen voor netwerktoegang in netwerkbeveiligingsgroepen of Azure Firewall. U kunt servicetags gebruiken in plaats van specifieke IP-adressen wanneer u beveiligingsregels maakt. Door de naam van de servicetag (bijvoorbeeld ApiManagement) op te geven in het juiste bron- of doelveld van een regel, kunt u het verkeer voor de bijbehorende service toestaan of weigeren. Microsoft beheert de adres voorvoegsels die worden omvat door de servicetag en werkt de servicetag automatisch bij wanneer adressen veranderen.

- [Overzicht van Azure-servicetags](../../virtual-network/service-tags-overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Standaardbeveiligingsconfiguraties voor netwerkapparaten onderhouden

**Richtlijnen:** standaardbeveiligingsconfiguraties definiëren en implementeren voor netwerkbronnen die zijn gekoppeld aan uw Azure Key Vault-exemplaren met Azure Policy. Gebruik Azure Policy aliassen in de naamruimten Microsoft.KeyVault en Microsoft.Network om aangepaste beleidsregels te maken om de netwerkconfiguratie van uw Azure Key Vault-exemplaren te controleren of af te dwingen. U kunt ook gebruikmaken van ingebouwde beleidsdefinities die betrekking hebben op Azure Key Vault, zoals:
- Key Vault moet gebruikmaken van een virtuele-netwerkservice-eindpunt

Raadpleeg de volgende bronnen voor meer informatie:

- [Beleidsregels voor het afdwingen van naleving maken en beheren](../../governance/policy/tutorials/create-and-manage.md)

- [Voorbeelden van Azure Policy](/azure/governance/policy/samples)

- [Een blauwdruk definiëren en toewijzen in de portal](../../governance/blueprints/create-blueprint-portal.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="110-document-traffic-configuration-rules"></a>1.10: Configuratieregels voor verkeer documenteren

**Richtlijnen:** gebruik tags voor resources die betrekking hebben op netwerkbeveiliging en verkeersstromen voor uw Azure Key Vault om metagegevens en logische organisatie te bieden.

Gebruik de ingebouwde Azure Policy met betrekking tot taggen, zoals Tag en waarde vereisen, om ervoor te zorgen dat alle resources worden gemaakt met tags en om u op de hoogte te stellen van niet-getaggingde resources.

U kunt Azure PowerShell of Azure CLI gebruiken om resources op te zoeken of acties uit te voeren op basis van hun tags.

- [Tags gebruiken om Azure-resources te organiseren](/azure/azure-resource-manager/resource-group-using-tags)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Geautomatiseerde hulpprogramma's gebruiken om netwerkresourceconfiguraties te bewaken en wijzigingen te detecteren

**Richtlijnen:** Gebruik azure-activiteitenlogboek om configuraties van netwerkresources te bewaken en wijzigingen te detecteren voor netwerkresources die betrekking hebben op uw Azure Key Vault exemplaren. Maak waarschuwingen binnen Azure Monitor die worden getriggerd wanneer er wijzigingen in kritieke netwerkbronnen plaatsvinden.

- [Azure-activiteitenlogboekgebeurtenissen weergeven en ophalen](/azure/azure-monitor/platform/activity-log-view)

- [Waarschuwingen voor activiteitenlogboek maken, weergeven en beheren met behulp van Azure Monitor](/azure/azure-monitor/platform/alerts-activity-log)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="logging-and-monitoring"></a>Logboekregistratie en bewaking

*Zie de Azure [Security Benchmark: Logging and Monitoring (Azure Security Benchmark: logboekregistratie en bewaking) voor meer informatie.](../../security/benchmarks/security-control-logging-monitoring.md)*

### <a name="22-configure-central-security-log-management"></a>2.2: Centraal beheer van beveiligingslogboek configureren

**Richtlijnen:** Logboeken opnemen via Azure Monitor voor het samenvoegen van beveiligingsgegevens die zijn gegenereerd door Azure Key Vault. Gebruik Azure Monitor Azure Log Analytics-werkruimte om query's uit te voeren en analyses uit te voeren en gebruik Azure Storage accounts voor langetermijn-/archiveringsopslag. U kunt ook gegevens inschakelen en in gebruik nemen voor Azure Sentinel siem van derden. 

- [Azure Key Vault logboekregistratie](/azure/key-vault/key-vault-logging)

- [Quickstart: Een on-board Azure Sentinel](../../sentinel/quickstart-onboard.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Auditlogregistratie voor Azure-resources inschakelen

**Richtlijnen:** Schakel diagnostische instellingen op uw Azure Key Vault voor toegang tot audit-, beveiligings- en diagnostische logboeken. Activiteitenlogboeken, die automatisch beschikbaar zijn, omvatten gebeurtenisbron, datum, gebruiker, tijdstempel, bronadressen, doeladressen en andere nuttige elementen.

- [Azure Key Vault logboekregistratie](/azure/key-vault/key-vault-logging)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** De [Azure Security-benchmark](/azure/governance/policy/samples/azure-security-benchmark) is het standaardbeleidsinitiatief voor Security Center en is de basis voor [Security Center aanbevelingen van Security Center.](/azure/security-center/security-center-recommendations) De Azure Policy met betrekking tot dit besturingselement worden automatisch ingeschakeld door Security Center. Voor waarschuwingen met betrekking tot dit besturingselement [is mogelijk](/azure/security-center/azure-defender) een Azure Defender nodig voor de gerelateerde services.

**Azure Policy ingebouwde definities - Microsoft.KeyVault**:

[!INCLUDE [Resource Policy for Microsoft.KeyVault 2.3](../../../includes/policy/standards/asb/rp-controls/microsoft.keyvault-2-3.md)]

### <a name="25-configure-security-log-storage-retention"></a>2.5: Opslagretentie van beveiligingslogboek configureren

**Richtlijnen:** Azure Monitor de Log Analytics-werkruimte die wordt gebruikt voor het opslaan van uw Azure Key Vault-logboeken, de retentieperiode in overeenstemming met de nalevingsvoorschriften van uw organisatie. Gebruik Azure Storage accounts voor langetermijnopslag/archivering.

- [De gegevensretentieperiode wijzigen](/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="26-monitor-and-review-logs"></a>2.6: Logboeken bewaken en controleren

**Richtlijnen:** analyseer en controleer logboeken op afwijkende gedrag en controleer regelmatig de resultaten voor Azure Key Vault met beveiligde resources. Gebruik Azure Monitor Log Analytics-werkruimte om logboeken te controleren en query's uit te voeren op logboekgegevens. U kunt ook gegevens inschakelen en in gebruik nemen voor Azure Sentinel of een SIEM van derden. 

- [On-board Azure Sentinel](../../sentinel/quickstart-onboard.md)

- [Aan de slag met Log Analytics in Azure Monitor](/azure/azure-monitor/log-query/get-started-portal)

- [Aan de slag met logboekquery’s in Azure Monitor](/azure/azure-monitor/log-query/get-started-queries)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Waarschuwingen inschakelen voor afwijkende activiteiten

**Richtlijnen:** schakel Azure Security Center Advanced Threat Protection (ATP) in voor Key Vault. Schakel diagnostische instellingen in Azure Key Vault en verzend logboeken naar een Log Analytics-werkruimte. Onboard uw Log Analytics-werkruimte Azure Sentinel deze een SOAR-oplossing (Security Orchestration Automated Response) biedt. Hierdoor kunnen playbooks (geautomatiseerde oplossingen) worden gemaakt en gebruikt om beveiligingsproblemen op te lossen.

- [Quickstart: Azure Sentinel onboarden](../../sentinel/quickstart-onboard.md)

- [Beveiligingswaarschuwingen beheren en erop reageren in Azure Security Center](../../security-center/security-center-managing-and-responding-alerts.md)

- [Reageren op gebeurtenissen met Azure Monitor-waarschuwingen](/azure/azure-monitor/learn/tutorial-response)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="identity-and-access-control"></a>Identiteits- en toegangsbeheer

*Zie de Azure [Security Benchmark: Identity and Access Control (Azure Security-benchmark: identiteit en Access Control).](../../security/benchmarks/security-control-identity-access-control.md)*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Een inventaris van beheerdersaccounts onderhouden

**Richtlijnen:** Een inventarisatie onderhouden van uw Azure Active Directory (Azure AD)-geregistreerde toepassingen, evenals alle gebruikersaccounts die toegang hebben tot uw Azure Key Vault sleutels, geheimen en certificaten. U kunt de Azure Portal of PowerShell gebruiken om query's uit te voeren en de toegang Key Vault afstemmen. Gebruik de volgende opdracht om toegang in PowerShell weer te geven:

(Get-AzResource -ResourceId [KeyVaultResourceID]). Properties.AccessPolicies

- [Een toepassing registreren bij Azure AD](/azure/key-vault/key-vault-manage-with-cli2#registering-an-application-with-azure-active-directory)

- [Veilige toegang tot een sleutelkluis](security-overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Toegewezen beheerdersaccounts gebruiken

**Richtlijnen:** maak standaardprocedures voor het gebruik van toegewezen beheerdersaccounts die toegang hebben tot uw Azure Key Vault-exemplaren. Gebruik Azure Security Center Identity and Access Management (momenteel in preview) om het aantal actieve beheerdersaccounts te bewaken.

- [Identiteit en toegang bewaken (preview)](../../security-center/security-center-identity-access.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4: Eenmalige Azure Active Directory gebruiken

**Richtlijnen:** Gebruik een Azure-service-principal in combinatie met de AppId, TenantID en ClientSecret om uw toepassing naadloos te verifiëren en het token op te halen dat wordt gebruikt voor toegang tot uw Azure Key Vault geheimen.

- [Service-naar-serviceverificatie voor Azure Key Vault met behulp van .NET](/azure/key-vault/service-to-service-authentication)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Meervoudige verificatie gebruiken voor alle Azure Active Directory op basis van toegang

**Richtlijnen:** meervoudige Azure Active Directory (Azure AD) inschakelen en aanbevelingen voor Azure Security Center Identity and Access Management (momenteel in preview) volgen om uw Event Hub-resources te beveiligen.

- [Een Azure AD Multi-Factor Authentication-implementatie plannen](../../active-directory/authentication/howto-mfa-getstarted.md)

- [Identiteit en toegang bewaken (preview)](../../security-center/security-center-identity-access.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3.6: Beveiligde, door Azure beheerde werkstations gebruiken voor beheertaken

**Richtlijnen:** Gebruik een PAW (Privileged Access Workstation) met meervoudige verificatie van Azure AD geconfigureerd om u aan te melden bij en Key Vault resources te configureren.

- [Privileged Access Workstations](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/) 

- [Een cloudgebaseerde implementatie van Azure AD-meervoudige verificatie plannen](../../active-directory/authentication/howto-mfa-getstarted.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Logboeken en waarschuwingen voor verdachte activiteiten van beheerdersaccounts

**Richtlijnen:** gebruik Azure Active Directory (Azure AD) Privileged Identity Management (PIM) voor het genereren van logboeken en waarschuwingen wanneer er verdachte of onveilige activiteiten plaatsvinden in de omgeving. Gebruik Azure AD-risicodetecties om waarschuwingen en rapporten over riskant gebruikersgedrag weer te geven. Voor aanvullende logboekregistratie verzendt u waarschuwingen Azure Security Center risicodetectie naar Azure Monitor configureren van aangepaste waarschuwingen/meldingen met behulp van actiegroepen.

Schakel Advanced Threat Protection (ATP) in voor Azure Key Vault voor het genereren van waarschuwingen voor verdachte activiteiten.

- [Implementatie Azure AD Privileged Identity Management (PIM)](../../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Advanced Threat Protection instellen voor Azure Key Vault (preview)](/azure/security-center/advanced-threat-protection-key-vault)

- [Waarschuwingen voor Azure Key Vault (preview)](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-azurekv)

- [Azure AD-risicodetecties](/azure/active-directory/reports-monitoring/concept-risk-events)

- [Actiegroepen maken en beheren in Azure Portal](/azure/azure-monitor/platform/action-groups)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Azure-resources alleen beheren vanaf goedgekeurde locaties

**Richtlijnen:** Configureer de locatievoorwaarde van een beleid voor voorwaardelijke toegang en beheer uw benoemde locaties. Met benoemde locaties kunt u logische groeperingen van IP-adresbereiken of landen en regio's maken. U kunt de toegang tot gevoelige resources, zoals uw Key Vault, beperken tot de geconfigureerde benoemde locaties.

- [Wat is de locatievoorwaarde in Azure Active Directory (Azure AD) Voorwaardelijke toegang?](../../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="39-use-azure-active-directory"></a>3.9: Gebruik Azure Active Directory

**Richtlijnen:** Gebruik Azure Active Directory (Azure AD) als het centrale verificatie- en autorisatiesysteem voor Azure-resources zoals Key Vault. Hierdoor kan op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC) gevoelige resources beheren.

- [Een nieuwe tenant maken in Azure AD](../../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Regelmatig gebruikerstoegang controleren en afstemmen

**Richtlijnen:** bekijk Azure Active Directory logboeken (Azure AD) om te helpen bij het vinden van verouderde accounts met Azure Key Vault beheerdersrollen. Gebruik bovendien Azure AD-toegangsbeoordelingen voor het efficiënt beheren van groepslidmaatschap, toegang tot bedrijfstoepassingen die kunnen worden gebruikt voor toegang tot Azure Key Vault en roltoewijzingen. Gebruikerstoegang moet regelmatig worden gecontroleerd, bijvoorbeeld elke 90 dagen, om ervoor te zorgen dat alleen de juiste gebruikers toegang blijven hebben.

- [Documentatie voor Azure AD-rapporten en -bewaking](/azure/active-directory/reports-monitoring/)

- [Wat zijn toegangsbeoordelingen in Azure Active Directory?](../../active-directory/governance/access-reviews-overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Pogingen tot toegang tot gedeactiveerde referenties controleren

**Richtlijnen:** Schakel diagnostische instellingen in Azure Key Vault en Azure Active Directory (Azure AD) en alle logboeken naar een Log Analytics-werkruimte te verzenden. Configureer de gewenste waarschuwingen (zoals pogingen om toegang te krijgen tot uitgeschakelde geheimen) in Log Analytics.

- [Azure AD-logboeken integreren met Azure Monitor logboeken](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

- [Migreren van de oude Key Vault oplossing](/azure/azure-monitor/insights/azure-key-vault#migrating-from-the-old-key-vault-solution)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: Afwijking van aanmeldingsgedrag van account

**Richtlijnen:** gebruik de Azure Active Directory(Azure AD)-functies voor identiteitsbeveiliging en risicodetectie om geautomatiseerde reacties te configureren op gedetecteerde verdachte acties met betrekking tot uw Azure Key Vault beveiligde resources. U moet geautomatiseerde reacties via Azure Sentinel om de beveiligingsreacties van uw organisatie te implementeren.

- [Rapport Riskante aanmeldingen in de Azure AD-portal](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [Hoe: risicobeleid configureren en inschakelen](../../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Onboarding van Azure Sentinel](../../sentinel/quickstart-onboard.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="data-protection"></a>Gegevensbeveiliging

*Zie [Azure Security Benchmark: gegevensbescherming](../../security/benchmarks/security-control-data-protection.md) voor meer informatie.*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Een inventaris van gevoelige informatie onderhouden

**Richtlijnen:** Tags gebruiken om Azure-resources bij te houden die gevoelige informatie opslaan of verwerken op Azure Key Vault ingeschakelde resources. 

- [Tags gebruiken om Azure-resources te organiseren](/azure/azure-resource-manager/resource-group-using-tags)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Systemen isoleren die gevoelige informatie opslaan of verwerken

**Richtlijnen:** u kunt de toegang tot Azure Key Vault beveiligen door gebruik te maken van service-eindpunten voor virtuele netwerken die zijn geconfigureerd om de toegang tot specifieke subnetten te beperken.

Nadat de firewallregels van kracht zijn, kunt u alleen Azure Key Vault gegevensvlakbewerkingen uitvoeren wanneer uw aanvraag afkomstig is van toegestane subnetten of IP-adresbereiken. Dit geldt ook voor Azure Key Vault toegang in de Azure Portal. Hoewel u vanuit de Azure Portal naar een sleutelkluis kunt bladeren, kunt u mogelijk geen lijst met sleutels, geheimen of certificaten maken als uw clientmachine niet op de lijst met toegestane certificaten staat. Dit is ook van invloed op de Azure Key Vault Picker en andere Azure-services. Mogelijk kunt u lijsten met sleutelkluizen zien, maar geen lijst met sleutels als firewallregels voorkomen dat uw clientmachine dit doet.

- [Azure Key Vault-firewalls en virtuele netwerken configureren](/azure/key-vault/key-vault-network-security)

- [Service-eindpunten voor virtuele netwerken voor Azure Key Vault](/azure/key-vault/key-vault-overview-vnet-service-endpoints)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Onbevoegde overdracht van gevoelige informatie controleren en blokkeren

**Richtlijnen:** alle gegevens die zijn opgeslagen in Azure Key Vault, worden als gevoelig beschouwd. Gebruik Azure Key Vault toegangsbeheer voor gegevensvlak om de toegang tot uw Azure Key Vault te bepalen. U kunt ook de Key Vault firewall van uw netwerk gebruiken om de toegang op de netwerklaag te beheren. Als u de toegang tot Azure Key Vault wilt bewaken, Key Vault diagnostische instellingen inschakelen en logboeken verzenden naar een Azure Storage-account of Log Analytics-werkruimte.

- [Veilige toegang tot een sleutelkluis](security-overview.md)

- [Azure Key Vault-firewalls en virtuele netwerken configureren](/azure/key-vault/key-vault-network-security)

- [Azure Key Vault logboekregistratie](/azure/key-vault/key-vault-logging)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4.6: Azure RBAC gebruiken om de toegang tot resources te beheren

**Richtlijnen:** Beveilig de toegang tot het beheer- en gegevensvlak van uw Azure Key Vault exemplaren.

- [Veilige toegang tot een sleutelkluis](security-overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Logboeken en waarschuwingen voor wijzigingen in kritieke Azure-resources

**Richtlijnen:** gebruik de Azure Key Vault Analytics-oplossing in Azure Monitor om Azure Key Vault auditgebeurtenislogboeken te controleren.

- [Azure Key Vault Analytics-oplossing in Azure Monitor](/azure/azure-monitor/insights/azure-key-vault)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="vulnerability-management"></a>Beheer van beveiligingsproblemen

*Zie de Azure [Security Benchmark: Vulnerability Management](../../security/benchmarks/security-control-vulnerability-management.md)voor meer informatie.*

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Een risicobeoordelingsproces gebruiken om prioriteit te geven aan het herstellen van gevonden beveiligingsproblemen

**Richtlijnen:** gebruik de standaardrisicobeoordelingen (secure score) die door de Azure Security Center.

- [Uw secure score in Azure Security Center](/azure/security-center/security-center-secure-score)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="inventory-and-asset-management"></a>Inventarisatie en Asset Management

*Zie Azure Security [Benchmark: Inventory and Asset Management (Azure Security Benchmark: Inventaris en Asset Management) voor meer informatie.](../../security/benchmarks/security-control-inventory-asset-management.md)*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Een geautomatiseerde oplossing voor assetdetectie gebruiken

**Richtlijnen:** gebruik Azure Resource Graph om alle resources (inclusief de Azure Key Vault) binnen uw abonnement op te vragen en te ontdekken. Zorg ervoor dat u over de juiste (lees)machtigingen in uw tenant hebt en alle Azure-abonnementen en resources binnen uw abonnementen kunt opsnoemen.

- [Uw eerste query Resource Graph uitvoeren met Azure Resource Graph Explorer](../../governance/resource-graph/first-query-portal.md)

- [Abonnementen krijgen die toegankelijk zijn voor het huidige account](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-4.8.0&amp;preserve-view=true)

- [Toegangsbeheer op basis van rollen in Azure (RBAC)](../../role-based-access-control/overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="62-maintain-asset-metadata"></a>6.2: Metagegevens van activa onderhouden

**Richtlijnen:** Tags toepassen op Azure Key Vault resources die metagegevens geven om ze logisch te ordenen in een taxonomie.

- [Tags maken en gebruiken](/azure/azure-resource-manager/resource-group-using-tags)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Niet-geautoriseerde Azure-resources verwijderen

**Richtlijnen:** gebruik waar van toepassing tags, beheergroepen en afzonderlijke abonnementen om uw Azure Key Vault en gerelateerde resources te organiseren en bij te houden. Inventaris regelmatig afstemmen en ervoor zorgen dat niet-geautoriseerde resources tijdig uit het abonnement worden verwijderd.

- [Een extra Azure-abonnement maken:](/azure/billing/billing-create-subscription)

- [Beheergroepen maken voor resource-organisatie en -beheer](/azure/governance/management-groups/create)

- [Tags gebruiken om Azure-resources te organiseren](/azure/azure-resource-manager/resource-group-using-tags)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Controleren op niet-goedgekeurde Azure-resources

**Richtlijnen:** Gebruik Azure-beleid om beperkingen in te stellen voor het type resources dat kan worden gemaakt in een of meer klantabonnementen met behulp van de volgende ingebouwde beleidsdefinities:

- Niet toegestane resourcetypen

- Toegestane brontypen

Gebruik daarnaast de Azure Resource Graph om resources in de abonnementen op te vragen/te ontdekken.

- [Zelfstudie: Beleidsregels voor het afdwingen van naleving maken en beheren](../../governance/policy/tutorials/create-and-manage.md)

- [Quickstart: Uw eerste Resource Graph-query uitvoeren met Azure Resource Graph Explorer](../../governance/resource-graph/first-query-portal.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="69-use-only-approved-azure-services"></a>6.9: Alleen goedgekeurde Azure-services gebruiken

**Richtlijnen:** Gebruik Azure-beleid om beperkingen in te stellen voor het type resources dat kan worden gemaakt in een of meer klantabonnementen met behulp van de volgende ingebouwde beleidsdefinities:

- Niet toegestane resourcetypen

- Toegestane brontypen

Raadpleeg de volgende bronnen voor meer informatie:

- [Zelfstudie: Beleidsregels voor het afdwingen van naleving maken en beheren](../../governance/policy/tutorials/create-and-manage.md)

- [Voorbeelden van Azure Policy](/azure/governance/policy/samples/built-in-policies#general)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: De mogelijkheid van gebruikers om te communiceren met Azure Resource Manager

**Richtlijnen:** gebruik de voorwaardelijke toegang van Azure om de mogelijkheid van gebruikers om te communiceren met Azure Resource Manager te beperken door Toegang blokkeren te configureren voor de app Microsoft Azure Management. Dit kan het maken en wijzigen van resources binnen een omgeving met een hoge beveiliging voorkomen, zoals resources met Key Vault configuratie.

- [Toegang tot Azure-beheer beheren met voorwaardelijke toegang](../../role-based-access-control/conditional-access-azure-management.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="secure-configuration"></a>Veilige configuratie

*Zie de Azure [Security Benchmark: Secure Configuration voor meer informatie.](../../security/benchmarks/security-control-secure-configuration.md)*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Veilige configuraties voor alle Azure-resources tot stand brengen

**Richtlijnen:** gebruik Azure Policy aliassen in de naamruimte Microsoft.KeyVault om aangepaste beleidsregels te maken om de configuratie van uw Azure Key Vault te controleren of af te dwingen. U kunt ook ingebouwde definities Azure Policy gebruiken voor Azure Key Vault, zoals:

- Key Vault-objecten moeten herstelbaar zijn

- Diagnostische instellingen implementeren voor Key Vault naar Log Analytics-werkruimte

- Diagnostische logboeken in Key Vault moeten zijn ingeschakeld

- Key Vault moet gebruikmaken van een virtuele-netwerkservice-eindpunt

- Diagnostische instellingen voor Key Vault toepassen op Event Hub

- Gebruik aanbevelingen van Azure Security Center als een veilige configuratiebasislijn voor uw Azure Key Vault exemplaren.

Raadpleeg de volgende bronnen voor meer informatie:

- [Beschikbare aliassen Azure Policy weergeven](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&amp;preserve-view=true)

- [Zelfstudie: Beleidsregels voor het afdwingen van naleving maken en beheren](../../governance/policy/tutorials/create-and-manage.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Veilige Azure-resourceconfiguraties onderhouden

**Richtlijnen:** gebruik Azure Policy [weigeren] en [implementeren indien niet aanwezig] om beveiligde instellingen af te dwingen voor uw Azure Key Vault resources. 

- [Beleidsregels voor het afdwingen van naleving maken en beheren](../../governance/policy/tutorials/create-and-manage.md)

  
- [Inzicht Azure Policy effecten](../../governance/policy/concepts/effects.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Configuratie van Azure-resources veilig opslaan

**Richtlijnen:** als u aangepaste Azure Policy gebruikt voor uw Azure Key Vault ingeschakelde resources, gebruikt u Azure Repos om uw code veilig op te slaan en te beheren.

- [Code opslaan in Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops&amp;preserve-view=true)

- [Documentatie voor Azure-repos](https://docs.microsoft.com/azure/devops/repos/?view=azure-devops&amp;preserve-view=true)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Hulpprogramma's voor configuratiebeheer implementeren voor Azure-resources

**Richtlijnen:** gebruik Azure Policy aliassen in de naamruimte Microsoft.KeyVault om aangepaste beleidsregels te maken voor het waarschuwen, controleren en afdwingen van systeemconfiguraties. Daarnaast ontwikkelt u een proces en pijplijn voor het beheren van beleids uitzonderingen.

- [Azure Policy configureren en beheren](../../governance/policy/tutorials/create-and-manage.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Geautomatiseerde configuratiebewaking implementeren voor Azure-resources

**Richtlijnen:** gebruik Azure Security Center om basislijnscans uit te voeren voor uw Azure Key Vault beveiligde resources.

- [Aanbevelingen in een Azure Security Center](../../security-center/security-center-remediate-recommendations.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="711-manage-azure-secrets-securely"></a>7.11: Azure-geheimen veilig beheren

**Richtlijnen:** Gebruik Managed Service Identity in combinatie met Azure Key Vault om geheimenbeheer voor uw cloudtoepassingen te vereenvoudigen en te beveiligen. Zorg ervoor Azure Key Vault de functie voor het verwijderen van gegevens is ingeschakeld.

- [Integreren met Azure Managed Identities](../../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Een sleutelkluis maken](quick-create-portal.md)

- [Verifiëren bij Key Vault](authentication.md)

- [Een Key Vault-toegangsbeleid toewijzen](assign-access-policy-portal.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** De [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) is het standaardbeleidsinitiatief voor Security Center en is de basis voor de aanbevelingen Security Center van [Security Center.](/azure/security-center/security-center-recommendations) De Azure Policy met betrekking tot dit besturingselement worden automatisch ingeschakeld door Security Center. Voor waarschuwingen met betrekking tot dit besturingselement [is mogelijk](/azure/security-center/azure-defender) een Azure Defender nodig voor de gerelateerde services.

**Azure Policy ingebouwde definities - Microsoft.KeyVault**:

[!INCLUDE [Resource Policy for Microsoft.KeyVault 7.11](../../../includes/policy/standards/asb/rp-controls/microsoft.keyvault-7-11.md)]

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Identiteiten veilig en automatisch beheren

**Richtlijnen:** Gebruik Managed Service Identity in combinatie met Azure Key Vault om het beheer van geheimen voor uw cloudtoepassingen te vereenvoudigen en te beveiligen.

  

- [Integreren met beheerde Azure-identiteiten](../../azure-app-configuration/howto-integrate-azure-managed-service-identity.md) 

- [Een Key Vault](quick-create-portal.md)    

- [Verificatie bij Key Vault](authentication.md)

- [Een Key Vault-toegangsbeleid toewijzen](assign-access-policy-portal.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Onbedoelde referentieblootstelling voorkomen

**Richtlijnen:** Referentiescanner implementeren om referenties in code te identificeren. Door het gebruik van Credential Scanner worden gebruikers ook aangemoedigd om gedetecteerde referenties naar veiligere locaties, zoals Azure Key Vault, te verplaatsen.  
  
- [ Referentiescanner instellen](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="malware-defense"></a>Beveiliging tegen malware

*Zie de Azure [Security Benchmark: Malware Defense voor meer informatie.](../../security/benchmarks/security-control-malware-defense.md)*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Bestanden vooraf scannen die moeten worden geüpload naar niet-compute-Azure-resources

**Richtlijnen:** Antimalware van Microsoft is ingeschakeld op de onderliggende host die ondersteuning biedt voor Azure-services (bijvoorbeeld Azure Key Vault), maar wordt niet uitgevoerd op klantinhoud.

Scan vooraf alle inhoud die wordt geüpload of verzonden naar niet-compute Azure-resources, zoals Azure Key Vault. Microsoft heeft in deze gevallen geen toegang tot uw gegevens.

- [Meer Microsoft Antimalware voor Azure Cloud Services en Virtual Machines](../../security/fundamentals/antimalware.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="data-recovery"></a>Gegevensherstel

*Zie de Azure [Security Benchmark: Data Recovery voor meer informatie.](../../security/benchmarks/security-control-data-recovery.md)*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Regelmatige automatische back-ups garanderen

**Richtlijnen:** zorg voor regelmatige automatische back-ups van uw Key Vault certificaten, sleutels, beheerde opslagaccounts en geheimen, met de volgende PowerShell-opdrachten:

- Backup-AzKeyVaultCertificate

- Backup-AzKeyVaultKey

- Backup-AzKeyVaultManagedStorageAccount

- Backup-AzKeyVaultSecret

Desgewenst kunt u uw back-ups Key Vault opslaan binnen Azure Backup.

- [Back-ups maken Key Vault certificaten](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultcertificate?view=azps-5.3.0&amp;preserve-view=true)

- [Een back-up maken Key Vault sleutels](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey?view=azps-5.3.0&amp;preserve-view=true)

- [Back-ups maken Key Vault beheerde opslagaccounts](/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount)

- [Back-ups maken Key Vault Geheimen](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultsecret?view=azps-5.3.0&amp;preserve-view=true)

- [Het inschakelen van Azure Backup](/azure/backup)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Volledige systeemback-ups uitvoeren en back-ups maken van door de klant beheerde sleutels

**Richtlijnen:** back-ups van uw Key Vault, sleutels, beheerde opslagaccounts en geheimen, met de volgende PowerShell-opdrachten:

- Backup-AzKeyVaultCertificate

- Backup-AzKeyVaultKey

- Backup-AzKeyVaultManagedStorageAccount

- Backup-AzKeyVaultSecret

Optioneel kunt u uw back-ups Key Vault opslaan binnen Azure Backup.

- [Back-ups maken Key Vault certificaten](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultcertificate?view=azps-5.3.0&amp;preserve-view=true)

- [Back-ups maken van Key Vault sleutels](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey?view=azps-5.3.0&amp;preserve-view=true)

- [Back-ups maken Key Vault beheerde opslagaccounts](/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount)

- [Back-ups maken van Key Vault Geheimen](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultsecret?view=azps-5.3.0&amp;preserve-view=true)

- [Het inschakelen van Azure Backup](/azure/backup)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Alle back-ups valideren, inclusief door de klant beheerde sleutels

**Richtlijnen:** voer periodiek gegevensherstel uit Key Vault certificaten, sleutels, beheerde opslagaccounts en geheimen, met de volgende PowerShell-opdrachten:

- Restore-AzKeyVaultCertificate

- Restore-AzKeyVaultKey

- Restore-AzKeyVaultManagedStorageAccount

- Restore-AzKeyVaultSecret

Raadpleeg de volgende bronnen voor meer informatie:

- [De certificaten Key Vault herstellen](https://docs.microsoft.com/powershell/module/az.keyvault/restore-azkeyvaultcertificate?view=azps-5.3.0&amp;preserve-view=true)

- [De sleutels Key Vault herstellen](https://docs.microsoft.com/powershell/module/az.keyvault/restore-azkeyvaultkey?view=azps-5.3.0&amp;preserve-view=true)

- [Beheerde Key Vault herstellen](/powershell/module/az.keyvault/backup-azkeyvaultmanagedstorageaccount)

- [Geheimen herstellen Key Vault geheimen](https://docs.microsoft.com/powershell/module/az.keyvault/restore-azkeyvaultsecret?view=azps-5.3.0&amp;preserve-view=true)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Beveiliging van back-ups en door de klant beheerde sleutels garanderen

**Richtlijnen:** zorg ervoor dat de functie voor het verwijderen van Azure Key Vault. Met soft-delete kunt u verwijderde sleutelkluizen en kluisobjecten, zoals sleutels, geheimen en certificaten, herstellen. 

- [De zachte Azure Key Vault van een toepassing gebruiken](/azure/key-vault/key-vault-soft-delete-powershell)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** De [Azure Security-benchmark](/azure/governance/policy/samples/azure-security-benchmark) is het standaardbeleidsinitiatief voor Security Center en is de basis voor [Security Center aanbevelingen van Security Center.](/azure/security-center/security-center-recommendations) De Azure Policy met betrekking tot dit besturingselement worden automatisch ingeschakeld door Security Center. Waarschuwingen met betrekking tot dit besturingselement vereisen mogelijk een [Azure Defender](/azure/security-center/azure-defender) voor de gerelateerde services.

**Azure Policy ingebouwde definities - Microsoft.KeyVault**:

[!INCLUDE [Resource Policy for Microsoft.KeyVault 9.4](../../../includes/policy/standards/asb/rp-controls/microsoft.keyvault-9-4.md)]

## <a name="incident-response"></a>Reageren op incidenten

*Zie [Azure Security Benchmark: respons op incidenten](../../security/benchmarks/security-control-incident-response.md) voor meer informatie.*

### <a name="101-create-an-incident-response-guide"></a>10.1: Een handleiding voor het reageren op incidenten maken

**Richtlijnen**: Stel voor uw organisatie een responshandleiding op voor gebruik bij incidenten. Zorg ervoor dat er schriftelijke responsplannen zijn waarin alle rollen van het personeel worden gedefinieerd, evenals alle fasen in het afhandelen/managen van incidenten, vanaf de detectie van het incident tot een evaluatie ervan achteraf. Deze processen moeten zijn gericht op het beveiligen van gevoelige systemen, zoals systemen die gebruikmaken van Key Vault geheimen.

- [Werkstroomautomatiseringen configureren binnen Azure Security Center](../../security-center/security-center-planning-and-operations-guide.md)   

- [Richtlijnen voor het bouwen van uw eigen proces voor het reageren op beveiligingsincidents](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft Security Response Center van een incident](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process)   

- [De klant kan ook gebruikmaken van de computerbeveiligingsincidentafhandelingshandleiding van NIST om te helpen bij het maken van een eigen plan voor het reageren op incidenten](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Een procedure voor het scoren en prioriteren van incidenten maken

**Richtlijnen:** Security Center aan elke waarschuwing een ernst toe om u te helpen bepalen welke waarschuwingen het eerst moeten worden onderzocht. De ernst is gebaseerd op hoe zeker Security Center is in de bevindingen of de metrische gegevens die worden gebruikt om de waarschuwing uit te geven, evenals het betrouwbaarheidsniveau dat er een schadelijke intentie is achter de activiteit die heeft geleid tot de waarschuwing. Daarnaast moet u abonnementen duidelijk markeren (bijvoorbeeld productie, niet-prod) en een naamgevingssysteem maken om Azure-resources duidelijk te identificeren en te categoriseren, met name de resources die gevoelige gegevens verwerken, zoals Azure Key Vault geheimen.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="103-test-security-response-procedures"></a>10.3: Procedures voor het testen van beveiligingsreacties

**Richtlijnen:** oefeningen uitvoeren om de mogelijkheden voor het reageren op incidenten van uw systemen regelmatig te testen om uw Azure Key Vault en gerelateerde resources te beschermen. Stel vast waar zich zwakke plekken en hiaten bevinden, en wijzig zo nodig het plan.

- [Raadpleeg de publicatie van NIST: Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities (Handleiding voor het testen, trainen en trainen van programma's voor IT-abonnementen en -mogelijkheden)](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Contactgegevens voor beveiligingsincidenten verstrekken en waarschuwingsmeldingen voor beveiligingsincidenten configureren

**Richtlijnen:** Contactgegevens voor beveiligingsincidenten worden door Microsoft gebruikt om contact met u op te nemen als de Microsoft Security Response Center (MSRC) detecteert dat uw gegevens zijn gebruikt door een onrechtmatige of niet-geautoriseerde partij.  Bekijk incidenten na het feit om ervoor te zorgen dat problemen worden opgelost.

- [De beveiligingscontactcontacte Azure Security Center instellen](../../security-center/security-center-provide-security-contact-details.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Beveiligingswaarschuwingen opnemen in uw systeem voor het reageren op incidenten

**Richtlijnen:** exporteert Azure Security Center en aanbevelingen met behulp van de functie Continue export om risico's voor het Azure Key Vault resources te identificeren. Met Continue export kunt u waarschuwingen en aanbevelingen handmatig of op een continue, continue manier exporteren.  U kunt de connector Azure Security Center gegevens gebruiken om de waarschuwingen te streamen naar Azure Sentinel. 

 

- [Continue export configureren](../../security-center/continuous-export.md) 

  

- [Waarschuwingen streamen naar Azure Sentinel](../../sentinel/connect-azure-security-center.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Het antwoord op beveiligingswaarschuwingen automatiseren

**Richtlijnen:** gebruik de functie Werkstroomautomatisering in Azure Security Center om automatisch antwoorden te activeren via 'Logic Apps' voor beveiligingswaarschuwingen en aanbevelingen om uw met Azure Key Vault beveiligde resources te beveiligen. 

 

- [Werkstroomautomatisering en -Logic Apps](../../security-center/workflow-automation.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="penetration-tests-and-red-team-exercises"></a>Penetratietests en Red Team-oefeningen

*Zie de Azure [Security Benchmark: Penetration Tests en Red Team Exercises (Penetratietests en Red Team-oefeningen) voor meer informatie.](../../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Regelmatige penetratietests uitvoeren van uw Azure-resources en zorgen voor herstel van alle kritieke beveiligings bevindingen

**Richtlijnen:** volg de Microsoft Cloud Penetration Testing Rules of Engagement om ervoor te zorgen dat uw penetratietests niet in strijd zijn met het Microsoft-beleid. Gebruik de strategie van Microsoft en de uitvoering van Red Teaming-activiteiten, en voer een penetratietest van de live site uit op basis van een infrastructuur, services en toepassingen die door Microsoft worden beheerd.

- [Regels voor het inzetten van penetratietests](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Verantwoordelijkheid**: Gedeeld

**Azure Security Center bewaking:** geen

## <a name="next-steps"></a>Volgende stappen

- Zie [Overzicht Azure Security Benchmark V2](/azure/security/benchmarks/overview)
- Meer informatie over [Azure-beveiligingsbasislijnen](/azure/security/benchmarks/security-baselines-overview)
