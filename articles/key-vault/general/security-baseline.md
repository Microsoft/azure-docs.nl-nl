---
title: Azure-beveiligings basislijn voor Key Vault
description: De Key Vault Security Baseline voorziet in procedure richtlijnen en resources voor het implementeren van de beveiligings aanbevelingen die zijn opgegeven in de Azure Security-Bench Mark.
author: msmbaldwin
ms.service: key-vault
ms.topic: conceptual
ms.date: 03/29/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 305fdd3a0b8e0c876924c6e1f090424e67571af0
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105968662"
---
# <a name="azure-security-baseline-for-key-vault"></a>Azure-beveiligings basislijn voor Key Vault

In deze beveiligings basislijn worden richt lijnen van de [Azure Security Bench Mark-versie 1,0](../../security/benchmarks/overview-v1.md) ingesteld op Key Vault. De Azure Security-benchmark biedt aanbevelingen voor hoe u uw cloudoplossingen in Azure kunt beveiligen. De inhoud wordt gegroepeerd op de **beveiligings controles** die zijn gedefinieerd door de Azure Security-benchmark en de bijbehorende richt lijnen die van toepassing zijn op Key Vault. **Besturings elementen** die niet van toepassing zijn op Key Vault of waarvoor de verantwoordelijkheid van micro soft is, zijn uitgesloten.

Als u wilt zien hoe Key Vault volledig is toegewezen aan de beveiligings benchmark van Azure, raadpleegt u het [volledige Key Vault beveiligings basislijn toewijzings bestand](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Netwerkbeveiliging

*Zie [Azure Security Benchmark: netwerkbeveiliging](../../security/benchmarks/security-control-network-security.md) voor meer informatie.*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Azure-resources in virtuele netwerken beveiligen

**Hulp**: Integreer Azure Key Vault met een persoonlijke Azure-koppeling. Met Azure Private Link service kunt u toegang krijgen tot Azure-Services (bijvoorbeeld Azure Key Vault) en Azure hostende klanten/partner services via een persoonlijk eind punt in uw virtuele netwerk.

Een privé-eindpunt in Azure is een netwerkinterface waarmee u privé en veilig verbinding maakt met een service die door Azure Private Link mogelijk wordt gemaakt. Het privé-eindpunt maakt gebruik van een privé-IP-adres van uw VNet, waardoor de service feitelijk in uw VNet wordt geplaatst. Al het verkeer naar de service kan worden gerouteerd via het privé-eindpunt, zodat er geen gateways, NAT-apparaten, ExpressRoute of VPN-verbindingen of openbare IP-adressen nodig zijn. Verkeer tussen uw virtuele netwerk en de services wordt via het backbonenetwerk van Microsoft geleid, waarmee de risico's van het openbare internet worden vermeden. U kunt verbinding maken met een exemplaar van een Azure-resource, zodat u het hoogste granulariteit krijgt in toegangsbeheer.

- [Key Vault integreren met een persoonlijke Azure-koppeling](/azure/key-vault/private-link-service)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: de [Security Bench Mark van Azure](/azure/governance/policy/samples/azure-security-benchmark) is het standaard beleids initiatief voor Security Center en is de basis voor de [aanbevelingen van Security Center](/azure/security-center/security-center-recommendations). De Azure Policy definities die aan dit besturings element zijn gerelateerd, worden automatisch door Security Center ingeschakeld. Voor waarschuwingen met betrekking tot dit besturings element is mogelijk een [Azure Defender](/azure/security-center/azure-defender) -plan vereist voor de gerelateerde services.

**Ingebouwde definities Azure Policy-micro soft.**-sleutel kluis:

[!INCLUDE [Resource Policy for Microsoft.KeyVault 1.1](../../../includes/policy/standards/asb/rp-controls/microsoft.keyvault-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: de configuratie en het verkeer van virtuele netwerken, subnetten en netwerk interfaces bewaken en vastleggen

**Hulp**: gebruik Azure Security Center en volg aanbevelingen voor netwerk beveiliging om uw door Key Vault geconfigureerde resources in azure te beveiligen. 

- [Voor meer informatie over de netwerk beveiliging die wordt verstrekt door Azure Security Center](../../security-center/security-center-network-recommendations.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: communicatie met bekende schadelijke IP-adressen weigeren

**Hulp**: Schakel Azure DDoS Protection Standard in op de virtuele Azure-netwerken die zijn gekoppeld aan uw Key Vault-instanties voor beveiliging tegen gedistribueerde Denial-of-service-aanvallen. Gebruik Azure Security Center geïntegreerde bedreigings informatie om communicatie met bekende of ongebruikte Internet-IP-adressen te weigeren.

 
- [Azure DDoS Protection Standard beheren met de Azure Portal](/azure/virtual-network/manage-ddos-protection)

- [Detectie van dreigingen voor de service laag van Azure in Azure Security Center](/azure/security-center/security-center-alerts-service-layer)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: op netwerk gebaseerde inbreuk detectie/indringings systemen (ID'S/IP-adressen) implementeren

**Richt lijnen**: aan deze vereiste kan worden voldaan door het configureren van Advanced Threat Protection (ATP) voor Azure Key Vault. ATP biedt een extra laag beveiligings informatie. Dit hulp programma detecteert mogelijk schadelijke pogingen om Azure Key Vault accounts te openen of misbruik te maken.

Wanneer Azure Security Center afwijkende activiteiten detecteert, worden er waarschuwingen weer gegeven. Er wordt ook een e-mail verzonden naar de abonnements beheerder met details van de verdachte activiteit en aanbevelingen voor het onderzoeken en oplossen van de geïdentificeerde bedreigingen.

- [Advanced beveiliging tegen bedreigingen instellen voor Azure Key Vault](/azure/security-center/advanced-threat-protection-key-vault)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: de complexiteit en administratieve overhead van netwerk beveiligings regels minimaliseren

**Richt lijnen**: voor resources die toegang nodig hebben tot uw Azure Key Vault-instanties, gebruikt u de labels van Azure-service voor de Azure Key Vault om netwerk toegangs beheer te definiëren voor netwerk beveiligings groepen of Azure firewall. U kunt servicetags gebruiken in plaats van specifieke IP-adressen wanneer u beveiligingsregels maakt. Door de naam van de service label (bijvoorbeeld ApiManagement) op te geven in het juiste bron-of doel veld van een regel, kunt u het verkeer voor de bijbehorende service toestaan of weigeren. Micro soft beheert de adres voorvoegsels die zijn opgenomen in het servicetag van de service en werkt de servicetag automatisch bij met gewijzigde adressen.

- [Overzicht van Azure-service Tags](../../virtual-network/service-tags-overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: standaard beveiligings configuraties voor netwerk apparaten onderhouden

**Hulp**: Definieer en implementeer standaard beveiligings configuraties voor netwerk resources die zijn gekoppeld aan uw Azure Key Vault-instanties met Azure Policy. Gebruik Azure Policy aliassen in de naam ruimten ' micro soft. sleutel kluis ' en ' micro soft. Network ' om aangepaste beleids regels te maken om de netwerk configuratie van uw Azure Key Vault instanties te controleren of af te dwingen. U kunt ook gebruik maken van ingebouwde beleids definities die betrekking hebben op Azure Key Vault, zoals:
- Key Vault moet gebruikmaken van een virtuele-netwerkservice-eindpunt

Raadpleeg de volgende bronnen voor meer informatie:

- [Beleidsregels voor het afdwingen van naleving maken en beheren](../../governance/policy/tutorials/create-and-manage.md)

- [Voorbeelden van Azure Policy](/azure/governance/policy/samples)

- [Een blauw druk in de portal definiëren en toewijzen](../../governance/blueprints/create-blueprint-portal.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="110-document-traffic-configuration-rules"></a>1,10: configuratie regels voor het document verkeer

**Richt lijnen**: Gebruik labels voor bronnen die betrekking hebben op netwerk beveiliging en verkeers stroom voor uw Azure Key Vault-instanties voor het leveren van meta gegevens en logische organisaties.

Gebruik de ingebouwde Azure Policy definities die betrekking hebben op labelen, zoals ' tag vereisen en de waarde ', om ervoor te zorgen dat alle resources worden gemaakt met tags en om u te informeren over niet-gelabelde resources.

U kunt Azure PowerShell of Azure CLI gebruiken om op basis van hun labels acties op te zoeken of uit te voeren op resources.

- [Tags gebruiken om Azure-resources te organiseren](/azure/azure-resource-manager/resource-group-using-tags)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: gebruik automatische hulpprogram ma's om netwerk bron configuraties te bewaken en wijzigingen te detecteren

**Hulp**: Azure-activiteiten logboek gebruiken om netwerk resource configuraties te bewaken en wijzigingen te detecteren voor netwerk bronnen die betrekking hebben op uw Azure Key Vault exemplaren. Maak waarschuwingen in Azure Monitor die worden geactiveerd wanneer er wijzigingen in kritieke netwerk bronnen plaatsvinden.

- [Activiteiten logboek gebeurtenissen van Azure bekijken en ophalen](/azure/azure-monitor/platform/activity-log-view)

- [Waarschuwingen voor activiteiten logboek maken, weer geven en beheren met behulp van Azure Monitor](/azure/azure-monitor/platform/alerts-activity-log)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="logging-and-monitoring"></a>Logboekregistratie en bewaking

*Zie [Azure Security Bench Mark: Logging and monitoring](../../security/benchmarks/security-control-logging-monitoring.md)(Engelstalig) voor meer informatie.*

### <a name="22-configure-central-security-log-management"></a>2,2: Centraal beveiligings logboek beheer configureren

**Hulp**: opname logboeken via Azure monitor voor het verzamelen van beveiligings gegevens die zijn gegenereerd door Azure Key Vault. Gebruik in Azure Monitor Azure Log Analytics-werk ruimte voor het opvragen en uitvoeren van analyses en gebruik Azure Storage accounts voor lange termijn/archiverings opslag. U kunt ook gegevens in-of uitschakelen voor Azure Sentinel of een SIEM van derden. 

- [Logboekregistratie voor Azure Key Vault](/azure/key-vault/key-vault-logging)

- [Snelstartgids: Azure-Sentinel aan de trein](../../sentinel/quickstart-onboard.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: controle logboek registratie inschakelen voor Azure-resources

**Hulp**: Schakel Diagnostische instellingen in op uw Azure Key Vault-instanties voor toegang tot controle-, beveiligings-en Diagnostische logboeken. Activiteiten logboeken, die automatisch beschikbaar zijn, omvatten gebeurtenis bron, datum, gebruiker, tijds tempel, bron adressen, doel adressen en andere nuttige elementen.

- [Logboekregistratie voor Azure Key Vault](/azure/key-vault/key-vault-logging)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: de [Security Bench Mark van Azure](/azure/governance/policy/samples/azure-security-benchmark) is het standaard beleids initiatief voor Security Center en is de basis voor de [aanbevelingen van Security Center](/azure/security-center/security-center-recommendations). De Azure Policy definities die aan dit besturings element zijn gerelateerd, worden automatisch door Security Center ingeschakeld. Voor waarschuwingen met betrekking tot dit besturings element is mogelijk een [Azure Defender](/azure/security-center/azure-defender) -plan vereist voor de gerelateerde services.

**Ingebouwde definities Azure Policy-micro soft.**-sleutel kluis:

[!INCLUDE [Resource Policy for Microsoft.KeyVault 2.3](../../../includes/policy/standards/asb/rp-controls/microsoft.keyvault-2-3.md)]

### <a name="25-configure-security-log-storage-retention"></a>2,5: Bewaar beveiliging van het beveiligings logboek configureren

**Richt lijnen**: in azure monitor voor de log Analytics werk ruimte die wordt gebruikt om uw Azure Key Vault logboeken te bewaren, stelt u de retentie periode in volgens de nalevings voorschriften van uw organisatie. Gebruik Azure Storage-accounts voor lange termijn/archiverings opslag.

- [De gegevensretentieperiode wijzigen](/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="26-monitor-and-review-logs"></a>2,6: Logboeken bewaken en controleren

**Richt lijnen**: Logboeken analyseren en bewaken voor afwijkend gedrag en regel matig de resultaten controleren voor uw met Azure Key Vault beveiligde resources. Gebruik de Log Analytics werk ruimte van Azure Monitor om logboeken te controleren en query's uit te voeren op logboek gegevens. U kunt ook gegevens in-of uitschakelen voor Azure Sentinel of een SIEM van derden. 

- [Azure-Sentinel op de trein](../../sentinel/quickstart-onboard.md)

- [Aan de slag met Log Analytics in Azure Monitor](/azure/azure-monitor/log-query/get-started-portal)

- [Aan de slag met logboekquery’s in Azure Monitor](/azure/azure-monitor/log-query/get-started-queries)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: waarschuwingen inschakelen voor afwijkende activiteiten

**Hulp**: schakel in azure Security Center Advanced Threat Protection (ATP) in voor Key Vault. Diagnostische instellingen in Azure Key Vault inschakelen en logboeken naar een Log Analytics werkruimte verzenden. Onboarding van uw Log Analytics-werk ruimte naar Azure-Sentinel, omdat dit een via-oplossing (Security Orchestration Automated Response) biedt. Hiermee kunnen playbooks (geautomatiseerde oplossingen) worden gemaakt en gebruikt om beveiligings problemen op te lossen.

- [Quickstart: Azure Sentinel onboarden](../../sentinel/quickstart-onboard.md)

- [Beveiligingswaarschuwingen beheren en erop reageren in Azure Security Center](../../security-center/security-center-managing-and-responding-alerts.md)

- [Reageren op gebeurtenissen met Azure Monitor-waarschuwingen](/azure/azure-monitor/learn/tutorial-response)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="identity-and-access-control"></a>Identiteits- en toegangsbeheer

*Zie [Azure Security Bench Mark: Identity and Access Control](../../security/benchmarks/security-control-identity-access-control.md)voor meer informatie.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: een inventaris van beheerders accounts onderhouden

**Hulp**: behoud een inventaris van uw Azure Active Directory (Azure AD)-geregistreerde toepassingen, evenals alle gebruikers accounts die toegang hebben tot uw Azure Key Vault sleutels, geheimen en certificaten. U kunt de Azure Portal of Power shell gebruiken om Key Vault toegang te zoeken en af te stemmen. Als u de toegang in Power shell wilt weer geven, gebruikt u de volgende opdracht:

(Get-AzResource-ResourceId [KeyVaultResourceID]). Eigenschappen. AccessPolicies

- [Een toepassing met Azure AD registreren](/azure/key-vault/key-vault-manage-with-cli2#registering-an-application-with-azure-active-directory)

- [Veilige toegang tot een sleutelkluis](secure-your-key-vault.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: speciale beheerders accounts gebruiken

**Richt lijnen**: Maak standaard procedures voor het gebruik van speciale beheerders accounts die toegang hebben tot uw Azure Key Vault-exemplaren. Gebruik Azure Security Center identiteits-en toegangs beheer (momenteel in de preview-versie) om het aantal actieve beheerders accounts te controleren.

- [Identiteit en toegang bewaken (preview-versie)](../../security-center/security-center-identity-access.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: gebruik Azure Active Directory eenmalige aanmelding (SSO)

**Richt lijnen**: gebruik een Azure-Service-Principal in combi natie met de AppId, TenantID en ClientSecret om uw toepassing naadloos te verifiëren en het token op te halen dat wordt gebruikt voor toegang tot uw Azure Key Vault geheimen.

- [Service-naar-service-verificatie voor het Azure Key Vault met behulp van .NET](/azure/key-vault/service-to-service-authentication)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: multi-factor Authentication gebruiken voor alle op Azure Active Directory gebaseerde toegang

**Hulp** bij het inschakelen van Azure Active Directory (Azure AD) multi-factor Authentication en het volgen van Azure Security Center identiteits-en toegangs beheer (momenteel in Preview) aanbevelingen voor het beveiligen van uw event hub-resources.

- [Een Azure AD Multi-Factor Authentication-implementatie plannen](../../active-directory/authentication/howto-mfa-getstarted.md)

- [Identiteit en toegang bewaken (preview-versie)](../../security-center/security-center-identity-access.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3,6: beveiligde, door Azure beheerde werk stations gebruiken voor beheer taken

**Hulp**: gebruik een privileged Access-werk station (Paw) met Azure AD multi-factor Authentication dat is geconfigureerd voor aanmelding bij en configureer Key Vault ingeschakelde resources.

- [Privileged Access Workstations](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/) 

- [Een Azure AD-implementatie op basis van een Cloud met meerdere factoren plannen](../../active-directory/authentication/howto-mfa-getstarted.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: Logboeken en waarschuwingen voor verdachte activiteiten van beheerders accounts

**Hulp**: gebruik Azure Active Directory (Azure AD) PRIVILEGED Identity Management (PIM) voor het genereren van Logboeken en waarschuwingen wanneer verdachte of onveilige activiteiten in de omgeving worden uitgevoerd. Gebruik Azure AD-risico detecties om waarschuwingen en rapporten weer te geven over Risk ante gebruikers gedrag. Voor aanvullende logboek registratie kunt u waarschuwingen voor Azure Security Center risico detectie verzenden naar Azure Monitor en aangepaste waarschuwingen/meldingen configureren met actie groepen.

Schakel Advanced Threat Protection (ATP) in voor Azure Key Vault om waarschuwingen te genereren voor verdachte activiteiten.

- [Azure AD Privileged Identity Management implementeren (PIM)](../../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Advanced Threat Protection voor Azure Key Vault instellen (preview)](/azure/security-center/advanced-threat-protection-key-vault)

- [Waarschuwingen voor Azure Key Vault (preview-versie)](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-azurekv)

- [Risico detecties van Azure AD](/azure/active-directory/reports-monitoring/concept-risk-events)

- [Actiegroepen maken en beheren in Azure Portal](/azure/azure-monitor/platform/action-groups)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: Azure-resources alleen beheren vanaf goedgekeurde locaties

**Hulp**: de locatie voorwaarde van een beleid voor voorwaardelijke toegang configureren en uw benoemde locaties beheren. Met benoemde locaties kunt u logische groeperingen van IP-adresbereiken of landen en regio's maken. U kunt de toegang tot gevoelige bronnen, zoals uw Key Vault geheimen, beperken tot uw geconfigureerde benoemde locaties.

- [Wat is de voor waarde voor de locatie in Azure Active Directory voorwaardelijke toegang van Azure AD?](../../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="39-use-azure-active-directory"></a>3,9: Azure Active Directory gebruiken

**Hulp**: gebruik Azure Active Directory (Azure AD) als centraal verificatie-en autorisatie systeem voor Azure-resources, zoals Key Vault. Hiermee kunnen op rollen gebaseerd toegangs beheer (Azure RBAC) van Azure worden uitgevoerd om gevoelige bronnen te beheren.

- [Een nieuwe Tenant maken in azure AD](../../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: regel matig gebruikers toegang controleren en afstemmen

**Hulp**: Raadpleeg Azure Active Directory-Logboeken (Azure AD) om verouderde accounts met Azure Key Vault beheerders rollen te detecteren. Daarnaast kunt u Azure AD-toegangs beoordelingen gebruiken om groepslid maatschappen efficiënt te beheren, toegang te krijgen tot bedrijfs toepassingen die kunnen worden gebruikt voor toegang tot Azure Key Vault en roltoewijzingen. Gebruikers toegang moet regel matig worden gecontroleerd, bijvoorbeeld elke 90 dagen, om ervoor te zorgen dat alleen de juiste gebruikers toegang hebben.

- [Documentatie over Azure AD-rapporten en controle](/azure/active-directory/reports-monitoring/)

- [Wat zijn toegangsbeoordelingen in Azure Active Directory?](../../active-directory/governance/access-reviews-overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: controle pogingen om toegang te krijgen tot gedeactiveerde referenties

**Hulp**: Diagnostische instellingen inschakelen voor Azure Key Vault en Azure Active Directory (Azure AD), waarbij alle logboeken worden verzonden naar een log Analytics-werk ruimte. Gewenste waarschuwingen configureren (zoals pogingen om toegang te krijgen tot uitgeschakelde geheimen) in Log Analytics.

- [Azure AD-logboeken integreren met Azure Monitor-logboeken](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

- [Migreren van de oude Key Vault-oplossing](/azure/azure-monitor/insights/azure-key-vault#migrating-from-the-old-key-vault-solution)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: waarschuwing voor de afwijking van het aanmeldings gedrag van het account

**Richt lijnen**: gebruik de functies voor identiteits beveiliging en risico detectie van Azure Active Directory (Azure AD) om automatische antwoorden te configureren op gedetecteerde verdachte acties die betrekking hebben op uw Azure Key Vault beveiligde bronnen. Schakel automatische antwoorden via Azure Sentinel in om de beveiligings reacties van uw organisatie te implementeren.

- [Rapport Risk ante aanmeldingen in de Azure AD-Portal](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [Procedure: risico beleid configureren en inschakelen](../../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Azure-Sentinel onboarden](../../sentinel/quickstart-onboard.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="data-protection"></a>Gegevensbeveiliging

*Zie [Azure Security Benchmark: gegevensbescherming](../../security/benchmarks/security-control-data-protection.md) voor meer informatie.*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: een inventaris van gevoelige informatie onderhouden

**Hulp**: Tags gebruiken om Azure-resources te helpen bij het bijhouden of verwerken van gevoelige informatie over Azure Key Vault ingeschakelde resources. 

- [Tags gebruiken om Azure-resources te organiseren](/azure/azure-resource-manager/resource-group-using-tags)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: systemen isoleren die gevoelige informatie opslaan of verwerken

**Hulp**: u kunt de toegang tot Azure Key Vault beveiligen door gebruik te maken van de service-eind punten van het virtuele netwerk die zijn geconfigureerd om de toegang tot specifieke subnetten te beperken.

Nadat de firewall regels van kracht zijn, kunt u alleen Azure Key Vault gegevenslaag bewerkingen uitvoeren wanneer uw aanvraag afkomstig is van de toegestane subnetten of IP-adresbereiken. Dit geldt ook voor Azure Key Vault toegang in de Azure Portal. Hoewel u naar een sleutel kluis kunt bladeren vanuit de Azure Portal, kunt u mogelijk geen sleutels, geheimen of certificaten weer geven als uw client computer zich niet in de lijst met toegestane computers bevindt. Dit is ook van invloed op de Azure Key Vault kiezer en andere Azure-Services. U kunt mogelijk lijsten met sleutel kluizen zien, maar geen lijst met sleutels als firewall regels verhinderen dat uw client computer dit doet.

- [Azure Key Vault-firewalls en virtuele netwerken configureren](/azure/key-vault/key-vault-network-security)

- [Virtuele netwerk service-eind punten voor Azure Key Vault](/azure/key-vault/key-vault-overview-vnet-service-endpoints)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: niet-geautoriseerde overdracht van gevoelige gegevens controleren en blok keren

**Hulp**: alle gegevens die zijn opgeslagen in azure Key Vault, worden beschouwd als gevoelig. Gebruik Azure Key Vault besturings elementen voor gegevens vlak om toegang tot Azure Key Vault geheimen te beheren. U kunt ook de ingebouwde firewall van Key Vault gebruiken om de toegang op de netwerklaag te beheren. Als u de toegang tot Azure Key Vault wilt controleren, schakelt u Key Vault Diagnostische instellingen in en verzendt u logboeken naar een Azure Storage-account of Log Analytics-werk ruimte.

- [Veilige toegang tot een sleutelkluis](secure-your-key-vault.md)

- [Azure Key Vault-firewalls en virtuele netwerken configureren](/azure/key-vault/key-vault-network-security)

- [Logboekregistratie voor Azure Key Vault](/azure/key-vault/key-vault-logging)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4,6: Azure RBAC gebruiken om de toegang tot resources te beheren

**Hulp**: veilige toegang tot het beheer en gegevens vlak van uw Azure Key Vault-instanties.

- [Veilige toegang tot een sleutelkluis](secure-your-key-vault.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: wijzigingen in essentiële Azure-resources vastleggen en waarschuwen

**Hulp**: gebruik de Azure Key Vault Analytics-oplossing in Azure Monitor om Azure Key Vault controle gebeurtenis logboeken te controleren.

- [Azure Key Vault Analytics-oplossing in Azure Monitor](/azure/azure-monitor/insights/azure-key-vault)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="vulnerability-management"></a>Beheer van beveiligingsproblemen

*Zie voor meer informatie de [Azure Security Bench Mark: beveiligingslek beheer](../../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: een risico classificatie proces gebruiken om prioriteit te geven aan het herstel van ontdekte beveiligings problemen

**Richt lijnen**: gebruik de standaard risico classificaties (beveiligde Score) van Azure Security Center.

- [Verbeter uw beveiligde Score in Azure Security Center](/azure/security-center/security-center-secure-score)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="inventory-and-asset-management"></a>Inventarisatie en Asset Management

*Zie [Azure Security Bench Mark: Inventory and Asset Management](../../security/benchmarks/security-control-inventory-asset-management.md)voor meer informatie.*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: automatische Asset-detectie oplossing gebruiken

**Richt lijnen**: gebruik Azure resource Graph om alle resources (inclusief Azure Key Vault instanties) in uw abonnement te doorzoeken en te detecteren. Zorg ervoor dat u de juiste machtigingen (lezen) hebt in uw Tenant en dat u alle Azure-abonnementen kunt inventariseren, evenals de resources in uw abonnementen.

- [Uw eerste resource grafiek query uitvoeren met Azure resource Graph Explorer](../../governance/resource-graph/first-query-portal.md)

- [Abonnementen ophalen waartoe het huidige account toegang heeft](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-4.8.0&amp;preserve-view=true)

- [Op rollen gebaseerd toegangs beheer (RBAC) van Azure](../../role-based-access-control/overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="62-maintain-asset-metadata"></a>6,2: meta gegevens van activa onderhouden

**Hulp**: Labels Toep assen op Azure Key Vault resources die meta gegevens geven om ze logisch in een taxonomie te organiseren.

- [Tags maken en gebruiken](/azure/azure-resource-manager/resource-group-using-tags)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: niet-geautoriseerde Azure-resources verwijderen

**Richt lijnen**: Gebruik labels, beheer groepen en afzonderlijke abonnementen, waar nodig, om Azure Key Vault instanties en gerelateerde resources te organiseren en bij te houden. Sluit de inventaris regel matig af en zorg ervoor dat niet-geautoriseerde resources tijdig worden verwijderd uit het abonnement.

- [Een extra Azure-abonnement maken:](/azure/billing/billing-create-subscription)

- [Beheergroepen maken voor resource-organisatie en -beheer](/azure/governance/management-groups/create)

- [Tags gebruiken om Azure-resources te organiseren](/azure/azure-resource-manager/resource-group-using-tags)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: monitor voor niet-goedgekeurde Azure-resources

**Richt lijnen**: gebruik Azure-beleid om beperkingen toe te voegen aan het type resources dat kan worden gemaakt in klant abonnement (en) met de volgende ingebouwde beleids definities:

- Niet toegestane resourcetypen

- Toegestane brontypen

Daarnaast gebruikt u de resource grafiek van Azure voor het opvragen/detecteren van resources binnen een of meer abonnementen.

- [Zelfstudie: Beleidsregels voor het afdwingen van naleving maken en beheren](../../governance/policy/tutorials/create-and-manage.md)

- [Quickstart: Uw eerste Resource Graph-query uitvoeren met Azure Resource Graph Explorer](../../governance/resource-graph/first-query-portal.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="69-use-only-approved-azure-services"></a>6,9: alleen goedgekeurde Azure-Services gebruiken

**Richt lijnen**: gebruik Azure-beleid om beperkingen toe te voegen aan het type resources dat kan worden gemaakt in klant abonnement (en) met de volgende ingebouwde beleids definities:

- Niet toegestane resourcetypen

- Toegestane brontypen

Raadpleeg de volgende bronnen voor meer informatie:

- [Zelfstudie: Beleidsregels voor het afdwingen van naleving maken en beheren](../../governance/policy/tutorials/create-and-manage.md)

- [Voorbeelden van Azure Policy](/azure/governance/policy/samples/built-in-policies#general)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: de mogelijkheid van gebruikers om te communiceren met Azure Resource Manager beperken

**Richt lijnen**: gebruik de voorwaardelijke toegang van Azure om gebruikers de mogelijkheid te bieden om te communiceren met Azure Resource Manager door ' blok toegang ' te configureren voor de app Microsoft Azure management. Dit kan ertoe leiden dat het maken en wijzigen van resources binnen een omgeving met hoge beveiliging, zoals die met Key Vault configuratie, wordt voor komen.

- [Toegang tot beheer van Azure beheren met voorwaardelijke toegang](../../role-based-access-control/conditional-access-azure-management.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="secure-configuration"></a>Veilige configuratie

*Zie [Azure Security Bench Mark: Secure Configuration](../../security/benchmarks/security-control-secure-configuration.md)(Engelstalig) voor meer informatie.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: veilige configuraties instellen voor alle Azure-resources

**Hulp**: gebruik Azure Policy aliassen in de naam ruimte ' micro soft.-sleutel kluis ' om aangepaste beleids regels te maken om de configuratie van uw Azure Key Vault instanties te controleren of af te dwingen. U kunt ook ingebouwde Azure Policy definities gebruiken voor Azure Key Vault zoals:

- Key Vault-objecten moeten herstelbaar zijn

- Diagnostische instellingen implementeren voor Key Vault naar Log Analytics-werkruimte

- Diagnostische logboeken in Key Vault moeten zijn ingeschakeld

- Key Vault moet gebruikmaken van een virtuele-netwerkservice-eindpunt

- Diagnostische instellingen voor Key Vault toepassen op Event Hub

- Gebruik aanbevelingen van Azure Security Center als een veilige configuratie basislijn voor uw Azure Key Vault exemplaren.

Raadpleeg de volgende bronnen voor meer informatie:

- [Beschik bare Azure Policy aliassen weer geven](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&amp;preserve-view=true)

- [Zelfstudie: Beleidsregels voor het afdwingen van naleving maken en beheren](../../governance/policy/tutorials/create-and-manage.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: Beveilig Azure-resource configuraties onderhouden

**Hulp**: gebruik Azure Policy [deny] en [implementeren indien niet aanwezig] voor het afdwingen van beveiligde instellingen voor uw Azure Key Vault-resources. 

- [Beleidsregels voor het afdwingen van naleving maken en beheren](../../governance/policy/tutorials/create-and-manage.md)

  
- [Azure Policy effecten begrijpen](../../governance/policy/concepts/effects.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: de configuratie van Azure-resources veilig opslaan

**Richt lijnen**: als u aangepaste Azure Policy definities gebruikt voor uw Azure Key Vault ingeschakelde resources, gebruikt u Azure opslag plaatsen om uw code veilig op te slaan en te beheren.

- [Code opslaan in azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops&amp;preserve-view=true)

- [Documentatie voor Azure opslag plaatsen](https://docs.microsoft.com/azure/devops/repos/?view=azure-devops&amp;preserve-view=true)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: hulpprogram ma's voor configuratie beheer voor Azure-resources implementeren

**Richt lijnen**: gebruik Azure Policy aliassen in de naam ruimte ' micro soft.-sleutel kluis ' om aangepaste beleids regels te maken om systeem configuraties te Signa lering, te controleren en af te dwingen. Ontwikkel bovendien een proces en pijp lijn voor het beheren van beleids uitzonderingen.

- [Azure Policy configureren en beheren](../../governance/policy/tutorials/create-and-manage.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: geautomatiseerde configuratie bewaking voor Azure-resources implementeren

**Hulp**: gebruik Azure Security Center om basislijn scans uit te voeren voor uw met Azure Key Vault beveiligde bronnen.

- [Aanbevelingen herstellen in Azure Security Center](../../security-center/security-center-remediate-recommendations.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="711-manage-azure-secrets-securely"></a>7,11: Azure-geheimen veilig beheren

**Hulp**: gebruik Managed Service Identity in combi natie met Azure Key Vault om het beheer van geheimen voor uw Cloud toepassingen te vereenvoudigen en te beveiligen. Zorg ervoor dat Azure Key Vault Soft-verwijdering is ingeschakeld.

- [Integreren met Azure Managed Identities](../../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Een sleutelkluis maken](quick-create-portal.md)

- [Verifiëren bij Key Vault](authentication.md)

- [Een Key Vault toegangs beleid toewijzen](assign-access-policy-portal.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: de [Security Bench Mark van Azure](/azure/governance/policy/samples/azure-security-benchmark) is het standaard beleids initiatief voor Security Center en is de basis voor de [aanbevelingen van Security Center](/azure/security-center/security-center-recommendations). De Azure Policy definities die aan dit besturings element zijn gerelateerd, worden automatisch door Security Center ingeschakeld. Voor waarschuwingen met betrekking tot dit besturings element is mogelijk een [Azure Defender](/azure/security-center/azure-defender) -plan vereist voor de gerelateerde services.

**Ingebouwde definities Azure Policy-micro soft.**-sleutel kluis:

[!INCLUDE [Resource Policy for Microsoft.KeyVault 7.11](../../../includes/policy/standards/asb/rp-controls/microsoft.keyvault-7-11.md)]

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: identiteiten veilig en automatisch beheren

**Hulp**: gebruik Managed Service Identity in combi natie met Azure Key Vault om het geheim beheer voor uw Cloud toepassingen te vereenvoudigen en te beveiligen.

  

- [Integratie met door Azure beheerde identiteiten](../../azure-app-configuration/howto-integrate-azure-managed-service-identity.md) 

- [Een Key Vault maken](quick-create-portal.md)    

- [Verifiëren bij Key Vault](authentication.md)

- [Een Key Vault toegangs beleid toewijzen](assign-access-policy-portal.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: onbedoelde referentie blootstelling elimineren

**Richt lijnen**: referentie scanner implementeren om referenties in code te identificeren. Door het gebruik van Credential Scanner worden gebruikers ook aangemoedigd om gedetecteerde referenties naar veiligere locaties, zoals Azure Key Vault, te verplaatsen.  
  
- [ Referentie scanner instellen](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="malware-defense"></a>Beveiliging tegen malware

*Zie voor meer informatie de [Azure Security Bench Mark: beveiliging tegen schadelijke software](../../security/benchmarks/security-control-malware-defense.md).*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: scan bestanden die moeten worden geüpload naar niet-reken resources van Azure

**Hulp**: micro soft anti-malware is ingeschakeld op de onderliggende host die ondersteuning biedt voor Azure-Services (bijvoorbeeld Azure Key Vault), maar wordt niet uitgevoerd op de inhoud van de klant.

Scan vooraf op inhoud die wordt geüpload of verzonden naar niet-reken resources van Azure, zoals Azure Key Vault. Micro soft heeft geen toegang tot uw gegevens in deze instanties.

- [Micro soft antimalware voor Azure Cloud Services en Virtual Machines begrijpen](../../security/fundamentals/antimalware.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="data-recovery"></a>Gegevensherstel

*Zie [Azure Security Bench Mark: Data Recovery](../../security/benchmarks/security-control-data-recovery.md)(Engelstalig) voor meer informatie.*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: zorg voor regel matige automatische back-ups

**Hulp**: zorg voor regel matige automatische back-ups van uw Key Vault-certificaten, sleutels, beheerde opslag accounts en geheimen, met de volgende Power shell-opdrachten:

- Backup-AzKeyVaultCertificate

- Backup-AzKeyVaultKey

- Backup-AzKeyVaultManagedStorageAccount

- Backup-AzKeyVaultSecret

U kunt eventueel uw Key Vault-back-ups opslaan in Azure Backup.

- [Back-up maken van Key Vault certificaten](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultcertificate?view=azps-5.3.0&amp;preserve-view=true)

- [Back-ups maken van Key Vault sleutels](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey?view=azps-5.3.0&amp;preserve-view=true)

- [Back-ups maken van Key Vault beheerde opslag accounts](/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount)

- [Back-ups maken van Key Vault geheimen](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultsecret?view=azps-5.3.0&amp;preserve-view=true)

- [Azure Backup inschakelen](/azure/backup)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: volledige back-ups van het systeem uitvoeren en een back-up maken van door de klant beheerde sleutels

**Richt lijnen**: Maak back-ups van uw Key Vault-certificaten, sleutels, beheerde opslag accounts en geheimen, met de volgende Power shell-opdrachten:

- Backup-AzKeyVaultCertificate

- Backup-AzKeyVaultKey

- Backup-AzKeyVaultManagedStorageAccount

- Backup-AzKeyVaultSecret

U kunt eventueel uw Key Vault-back-ups opslaan in Azure Backup.

- [Back-up maken van Key Vault certificaten](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultcertificate?view=azps-5.3.0&amp;preserve-view=true)

- [Back-ups maken van Key Vault sleutels](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey?view=azps-5.3.0&amp;preserve-view=true)

- [Back-ups maken van Key Vault beheerde opslag accounts](/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount)

- [Back-ups maken van Key Vault geheimen](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultsecret?view=azps-5.3.0&amp;preserve-view=true)

- [Azure Backup inschakelen](/azure/backup)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: alle back-ups valideren, inclusief door de klant beheerde sleutels

**Richt lijnen**: regel matig gegevens herstel van uw Key Vault-certificaten, sleutels, beheerde opslag accounts en geheimen uitvoeren met de volgende Power shell-opdrachten:

- Restore-AzKeyVaultCertificate

- Restore-AzKeyVaultKey

- Restore-AzKeyVaultManagedStorageAccount

- Restore-AzKeyVaultSecret

Raadpleeg de volgende bronnen voor meer informatie:

- [Key Vault certificaten herstellen](https://docs.microsoft.com/powershell/module/az.keyvault/restore-azkeyvaultcertificate?view=azps-5.3.0&amp;preserve-view=true)

- [Key Vault sleutels herstellen](https://docs.microsoft.com/powershell/module/az.keyvault/restore-azkeyvaultkey?view=azps-5.3.0&amp;preserve-view=true)

- [Key Vault beheerde opslag accounts herstellen](/powershell/module/az.keyvault/backup-azkeyvaultmanagedstorageaccount)

- [Key Vault geheimen herstellen](https://docs.microsoft.com/powershell/module/az.keyvault/restore-azkeyvaultsecret?view=azps-5.3.0&amp;preserve-view=true)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: zorg voor de bescherming van back-ups en door de klant beheerde sleutels

**Hulp**: Zorg ervoor dat de functie voor voorlopig verwijderen is ingeschakeld voor Azure Key Vault. Met zacht verwijderen kunt u verwijderde sleutel kluizen en kluis objecten zoals sleutels, geheimen en certificaten herstellen. 

- [De tijdelijke verwijdering van Azure Key Vault gebruiken](/azure/key-vault/key-vault-soft-delete-powershell)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: de [Security Bench Mark van Azure](/azure/governance/policy/samples/azure-security-benchmark) is het standaard beleids initiatief voor Security Center en is de basis voor de [aanbevelingen van Security Center](/azure/security-center/security-center-recommendations). De Azure Policy definities die aan dit besturings element zijn gerelateerd, worden automatisch door Security Center ingeschakeld. Voor waarschuwingen met betrekking tot dit besturings element is mogelijk een [Azure Defender](/azure/security-center/azure-defender) -plan vereist voor de gerelateerde services.

**Ingebouwde definities Azure Policy-micro soft.**-sleutel kluis:

[!INCLUDE [Resource Policy for Microsoft.KeyVault 9.4](../../../includes/policy/standards/asb/rp-controls/microsoft.keyvault-9-4.md)]

## <a name="incident-response"></a>Reageren op incidenten

*Zie [Azure Security Benchmark: respons op incidenten](../../security/benchmarks/security-control-incident-response.md) voor meer informatie.*

### <a name="101-create-an-incident-response-guide"></a>10,1: een hand leiding voor reactie op incidenten maken

**Richtlijnen**: Stel voor uw organisatie een responshandleiding op voor gebruik bij incidenten. Zorg ervoor dat er schriftelijke responsplannen zijn waarin alle rollen van het personeel worden gedefinieerd, evenals alle fasen in het afhandelen/managen van incidenten, vanaf de detectie van het incident tot een evaluatie ervan achteraf. Deze processen moeten zich richten op het beveiligen van gevoelige systemen, zoals die van Key Vault geheimen.

- [Werk stroom automatisering configureren in Azure Security Center](../../security-center/security-center-planning-and-operations-guide.md)   

- [Richt lijnen voor het bouwen van uw eigen beveiligings incident antwoord proces](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Micro soft Security Response Center anatomie van een incident](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process)   

- [Klant kan ook gebruikmaken van de hand leiding voor de verwerking van het computer beveiligings incident van het NIST om hulp te bieden bij het maken van een eigen incident respons plan](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: een beoordelings procedure voor incidenten en prioriteits procedures maken

**Hulp**: Security Center wijst aan elke waarschuwing een Ernst toe om u te helpen bepalen welke waarschuwingen het eerst moeten worden onderzocht. De ernst is gebaseerd op de manier waarop vertrouwen Security Center zich in de zoek actie bevindt of de metriek die wordt gebruikt om de waarschuwing te geven, evenals het betrouwbaarheids niveau waarvan er sprake is van schadelijke opzet achter de activiteit die tot de waarschuwing heeft geleid. Daarnaast kunt u ook duidelijk abonnementen markeren (voor bijvoorbeeld productie, niet-productie) en maak een naamgevings systeem om Azure-resources duidelijk te identificeren en te categoriseren, met name voor de verwerking van gevoelige gegevens zoals Azure Key Vault geheimen.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="103-test-security-response-procedures"></a>10,3: procedures voor beveiligings antwoorden testen

**Richt lijnen**: oefent oefeningen uit om de incident respons mogelijkheden van uw systeem te testen op een reguliere uitgebracht om uw Azure Key Vault-instanties en gerelateerde resources te beschermen. Stel vast waar zich zwakke plekken en hiaten bevinden, en wijzig zo nodig het plan.

- [Raadpleeg de publicatie van het NIST: hand leiding voor het testen, trainen en uitoefenen van Program Ma's voor IT-plannen en-mogelijkheden](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: contact gegevens van het beveiligings incident opgeven en waarschuwings meldingen configureren voor beveiligings incidenten

**Hulp**: contact gegevens van beveiligings incidenten worden door micro soft gebruikt om contact met u op te nemen als het micro soft Security Response Center (MSRC) detecteert dat uw gegevens zijn geopend door een onrecht matige of niet-gemachtigde partij.  Bekijk incidenten na het feit om te controleren of de problemen zijn opgelost.

- [De Azure Security Center Security-contact persoon instellen](../../security-center/security-center-provide-security-contact-details.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: beveiligings waarschuwingen opnemen in uw reactie systeem van uw incident

**Hulp**: exporteer uw Azure Security Center waarschuwingen en aanbevelingen met behulp van de functie continue export om risico's voor Azure Key Vault resources te identificeren. Met doorlopend exporteren kunt u waarschuwingen en aanbevelingen hand matig of op een doorlopende manier exporteren.  U kunt de Azure Security Center Data Connector gebruiken om de waarschuwingen naar Azure Sentinel te streamen. 

 

- [Continue export configureren](../../security-center/continuous-export.md) 

  

- [Waarschuwingen streamen naar Azure Sentinel](../../sentinel/connect-azure-security-center.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: de reactie op beveiligings waarschuwingen automatiseren

**Hulp**: gebruik de functie werk stroom automatisering in azure Security Center om automatisch reacties te activeren via ' Logic apps ' in beveiligings waarschuwingen en aanbevelingen om uw door Azure Key Vault beveiligde bronnen te beveiligen. 

 

- [Werk stroom automatisering en Logic Apps configureren](../../security-center/workflow-automation.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="penetration-tests-and-red-team-exercises"></a>Penetratietests en Red Team-oefeningen

*Zie [Azure Security Bench Mark: Indringings tests en rode team oefeningen](../../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)voor meer informatie.*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: voert regel matig indringings tests van uw Azure-resources uit en zorgt voor herbemiddeling van alle essentiële beveiligings resultaten

**Richt lijnen**: Volg de stappen voor het testen van de Microsoft Cloud indringings regels om ervoor te zorgen dat uw indringings tests niet worden geschonden door het micro soft-beleid. Gebruik de strategie van Microsoft en de uitvoering van Red Teaming-activiteiten, en voer een penetratietest van de live site uit op basis van een infrastructuur, services en toepassingen die door Microsoft worden beheerd.

- [Regels voor het inzetten van penetratietests](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Verantwoordelijkheid**: Gedeeld

**Azure Security Center bewaking**: geen

## <a name="next-steps"></a>Volgende stappen

- Zie [Overzicht Azure Security Benchmark V2](/azure/security/benchmarks/overview)
- Meer informatie over [Azure-beveiligingsbasislijnen](/azure/security/benchmarks/security-baselines-overview)
