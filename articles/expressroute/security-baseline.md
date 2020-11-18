---
title: Beveiligings basislijn voor Azure ExpressRoute
description: Azure-beveiligings basislijn voor ExpressRoute
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 06/22/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 99594ad5fe07f18f639b4870f4e0faa59a697afc
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94843176"
---
# <a name="azure-security-baseline-for-expressroute"></a>Azure-beveiligings basislijn voor ExpressRoute

De Azure Security Baseline voor ExpressRoute bevat aanbevelingen waarmee u de beveiligings-postuur van uw implementatie kunt verbeteren.

De basis lijn voor deze service wordt opgehaald uit de [Azure Security Bench Mark-versie 1,0](../security/benchmarks/overview.md), die aanbevelingen biedt over hoe u uw cloud oplossingen kunt beveiligen in azure met onze richt lijnen voor best practices.

Zie het [overzicht van Azure Security-basis lijnen](../security/benchmarks/security-baselines-overview.md)voor meer informatie.

## <a name="network-security"></a>Netwerkbeveiliging

*Zie [beveiligings beheer: netwerk beveiliging](../security/benchmarks/security-control-network-security.md)voor meer informatie.*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Azure-resources in virtuele netwerken beveiligen

**Richt lijnen**: niet van toepassing; ExpressRoute fungeert als een redundant circuit paar om hoge Beschik baarheid te garanderen. ExpressRoute-verbindingen gaan niet via het openbare internet.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1,2: de configuratie en het verkeer van virtuele netwerken, subnetten en Nic's bewaken en vastleggen

**Richt lijnen**: niet van toepassing; Wanneer u werkt met Gateway-subnetten, moet u geen netwerk beveiligings groep (NSG) koppelen aan het subnet van de gateway. Het koppelen van een netwerk beveiligings groep aan dit subnet kan ertoe leiden dat uw Virtual Network gateway (VPN, ExpressRoute-gateway) niet meer werkt zoals verwacht.

* [Meer informatie over Azure ExpressRoute gateway-vereisten](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#requirements)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="13-protect-critical-web-applications"></a>1,3: essentiële webtoepassingen beveiligen

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor webtoepassingen die worden uitgevoerd op Azure App Service of reken bronnen.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: communicatie met bekende schadelijke IP-adressen weigeren

**Richt lijnen**: niet van toepassing; de Expressroutes waaraan van elke klant zijn opgenomen in hun eigen routerings domeinen en worden in hun eigen virtuele netwerk getunneld. Hoewel Expressroutes waaraan zijn geïsoleerd, voor extra beveiliging van andere resources die hetzelfde virtuele netwerk delen, kunt u DDoS Protection Standard inschakelen voor beveiliging tegen DDoS-aanvallen.

* [Meer informatie over beveiligings controles voor Azure ExpressRoute](./expressroute-security-controls.md)

* [DDoS-beveiliging configureren](../virtual-network/manage-ddos-protection.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="15-record-network-packets"></a>1,5: netwerk pakketten opnemen

**Richt lijnen**: niet van toepassing; pakket opname wordt alleen ondersteund voor IaaS Compute-resources (virtuele Azure-machines).

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: op netwerk gebaseerde inbreuk detectie/indringings systemen (ID'S/IP-adressen) implementeren

**Richt lijnen**: niet van toepassing; de Expressroutes waaraan van elke klant zijn opgenomen in hun eigen routerings domeinen en worden in hun eigen virtuele netwerk getunneld.

* [Meer informatie over beveiligings controles voor Azure ExpressRoute](./expressroute-security-controls.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="17-manage-traffic-to-web-applications"></a>1,7: verkeer naar webtoepassingen beheren

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor webtoepassingen die worden uitgevoerd op Azure App Service of reken bronnen.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: de complexiteit en administratieve overhead van netwerk beveiligings regels minimaliseren

**Richt lijnen**: niet van toepassing; Azure ExpressRoute zelf is geen eind punt waar u verkeer met Service Tags of netwerk beveiligings groepen kunt filteren of toestaan.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: standaard beveiligings configuraties voor netwerk apparaten onderhouden

**Richt lijnen**: standaard beveiligings configuraties voor Azure ExpressRoute definiëren en implementeren met Azure Policy. Gebruik Azure Policy aliassen in de naam ruimte ' micro soft. netwerk ' om aangepaste beleids regels te maken om de netwerk configuratie van uw ExpressRoute te controleren of af te dwingen.

* [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="110-document-traffic-configuration-rules"></a>1,10: configuratie regels voor het document verkeer

**Hulp**: Labels gebruiken voor uw Azure ExpressRoute-instanties om meta gegevens en logische organisaties te bieden.

Gebruik een van de ingebouwde Azure Policy definities die betrekking hebben op labelen, zoals ' tag vereisen en de waarde ', om ervoor te zorgen dat alle resources met tags worden gemaakt en u op de hoogte moet zijn van bestaande niet-gelabelde resources.

U kunt Azure PowerShell of Azure CLI gebruiken om op basis van hun labels acties op te zoeken of uit te voeren op resources.

* [Tags gebruiken om Azure-resources te organiseren](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: gebruik automatische hulpprogram ma's om netwerk bron configuraties te bewaken en wijzigingen te detecteren

**Hulp**: Azure-activiteiten logboek gebruiken om netwerk resource configuraties te bewaken en wijzigingen te detecteren voor netwerk bronnen die betrekking hebben op uw ExpressRoute-verbindingen. Maak waarschuwingen in Azure Monitor die worden geactiveerd wanneer er wijzigingen in kritieke resources plaatsvinden.

* [Controle inschakelen in azure Sentinel](../sentinel/resources.md)

* [Activiteiten logboek gebeurtenissen van Azure weer geven en ophalen](../azure-monitor/platform/activity-log.md#view-the-activity-log)

* [Waarschuwingen maken in Azure Monitor](../azure-monitor/platform/alerts-activity-log.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

## <a name="logging-and-monitoring"></a>Logboekregistratie en bewaking

*Zie voor meer informatie [beveiligings beheer: logboek registratie en controle](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: goedgekeurde tijd synchronisatie bronnen gebruiken

**Richt lijnen**: niet van toepassing; Micro soft onderhoudt de tijd bron die wordt gebruikt voor Azure-resources, zoals Azure Sentinel, voor tijds tempels in de logboeken.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="22-configure-central-security-log-management"></a>2,2: Centraal beveiligings logboek beheer configureren

**Hulp**: Schakel Diagnostische instellingen voor Azure-activiteiten logboek in en verzend de logboeken naar een log Analytics-werk ruimte, een Azure Event hub of een Azure-opslag account voor archivering. Activiteiten logboeken bieden inzicht in de bewerkingen die zijn uitgevoerd op uw Azure ExpressRoute-resources op het niveau van het besturings vlak. Met Azure-activiteiten logboek gegevens kunt u de ' What, wie en wanneer ' bepalen voor schrijf bewerkingen (PUT, POST, DELETE) die zijn uitgevoerd op het niveau van het besturings element van uw ExpressRoute-resources.

* [Diagnostische instellingen voor Azure-activiteiten logboek inschakelen](../azure-monitor/platform/activity-log.md)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: controle logboek registratie inschakelen voor Azure-resources

**Hulp**: Schakel Diagnostische instellingen voor Azure-activiteiten logboek in en verzend de logboeken naar een log Analytics-werk ruimte, een Azure Event hub of een Azure-opslag account voor archivering. Activiteiten logboeken bieden inzicht in de bewerkingen die zijn uitgevoerd op uw Azure ExpressRoute-resources op het niveau van het besturings vlak. Met Azure-activiteiten logboek gegevens kunt u de ' What, wie en wanneer ' bepalen voor schrijf bewerkingen (PUT, POST, DELETE) die zijn uitgevoerd op het niveau van het besturings element van uw ExpressRoute-resources.

* [Diagnostische instellingen voor Azure-activiteiten logboek inschakelen](../azure-monitor/platform/activity-log.md)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: beveiligings logboeken verzamelen van besturings systemen

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="25-configure-security-log-storage-retention"></a>2,5: Bewaar beveiliging van het beveiligings logboek configureren

**Richt lijnen**: stel in azure monitor de Bewaar periode voor logboek registratie In voor log Analytics werk ruimten die zijn gekoppeld aan uw Azure ExpressRoute-resources op basis van de nalevings voorschriften van uw organisatie.

* [Para meters voor het bewaren van Logboeken instellen](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="26-monitor-and-review-logs"></a>2,6: Logboeken bewaken en controleren

**Hulp**: Schakel Diagnostische instellingen voor Azure-activiteiten logboek in en verzend de logboeken naar een log Analytics-werk ruimte. Voer query's uit in Log Analytics om zoek termen te zoeken, trends te identificeren, patronen te analyseren en veel andere inzichten te bieden op basis van de activiteiten logboek gegevens die mogelijk zijn verzameld voor Azure ExpressRoute.

* [Diagnostische instellingen voor Azure-activiteiten logboek inschakelen](../azure-monitor/platform/activity-log.md)

* [Azure-activiteiten logboeken verzamelen en analyseren in Log Analytics werk ruimte in Azure Monitor](../azure-monitor/platform/activity-log.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: waarschuwingen inschakelen voor afwijkende activiteiten

**Richt lijnen**: u kunt configureren voor het ontvangen van waarschuwingen op basis van metrische gegevens en activiteiten logboeken die betrekking hebben op uw Azure ExpressRoute-resources. Met Azure Monitor kunt u een waarschuwing configureren voor het verzenden van een e-mail melding, het aanroepen van een webhook of het aanroepen van een Azure Logic-app.

* [Bewaking en waarschuwingen in ExpressRoute begrijpen](./expressroute-monitoring-metrics-alerts.md)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="28-centralize-anti-malware-logging"></a>2,8: registratie van anti-malware centraliseren

**Richt lijnen**: niet van toepassing; Azure ExpressRoute verwerkt of produceert geen anti-malware-gerelateerde Logboeken.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="29-enable-dns-query-logging"></a>2,9: DNS-query logboek registratie inschakelen

**Richt lijnen**: niet van toepassing; Azure ExpressRoute verwerkt of produceert geen aan DNS gerelateerde Logboeken.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="210-enable-command-line-audit-logging"></a>2,10: controle logboek registratie op opdracht regel inschakelen

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

## <a name="identity-and-access-control"></a>Identiteits- en toegangsbeheer

*Zie [beveiligings beheer: identiteits-en toegangs beheer](../security/benchmarks/security-control-identity-access-control.md)voor meer informatie.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: een inventaris van beheerders accounts onderhouden

**Richt lijnen**: behoud een inventaris van de gebruikers accounts met beheerders toegang tot het besturings vlak (bijvoorbeeld Azure Portal) van uw Azure ExpressRoute-resources.

U kunt het deel venster identiteits-en toegangs beheer (IAM) in de Azure Portal voor uw abonnement gebruiken om toegangs beheer op basis van rollen (Azure RBAC) te configureren. De rollen worden toegepast op gebruikers, groepen, service-principals en beheerde identiteiten in Active Directory.

Daarnaast kunnen partners die gebruikmaken van de ExpressRoute partner Resource Manager-API op rollen gebaseerd toegangs beheer Toep assen op de expressRouteCrossConnection-resource. Met deze besturings elementen kunt u machtigingen definiëren waarvoor gebruikers accounts de expressRouteCrossConnection-resource kunnen wijzigen en peering-configuraties toevoegen/bijwerken/verwijderen.

* [Meer informatie over Azure RBAC](../role-based-access-control/overview.md)

* [Gebruik Azure RBAC in de ExpressRoute-partner Resource Manager-API](./cross-connections-api-development.md)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="32-change-default-passwords-where-applicable"></a>3,2: standaard wachtwoorden wijzigen indien van toepassing

**Hulp**: Azure AD heeft niet het concept van standaard wachtwoorden. Andere Azure-resources die een wacht woord vereisen, moeten een wacht woord afdwingen voor het maken van complexiteits vereisten en een minimale wachtwoord lengte, die afhankelijk is van de service. U bent verantwoordelijk voor toepassingen van derden en Marketplace-services die standaard wachtwoorden kunnen gebruiken.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: speciale beheerders accounts gebruiken

**Richt lijnen**: Maak standaard procedures voor het gebruik van specifieke beheerders accounts. Gebruik Azure Security Center identiteits-en toegangs beheer om het aantal beheerders accounts te bewaken.

Daarnaast kunt u aanbevelingen van Azure Security Center of ingebouwde Azure-beleids regels gebruiken om u te helpen bij het bijhouden van specifieke beheerders accounts, zoals:
- Er moet meer dan één eigenaar zijn toegewezen aan uw abonnement
- Afgeschafte accounts met eigenaarsmachtigingen moeten worden verwijderd uit uw abonnement
- Externe accounts met eigenaarsmachtigingen moeten worden verwijderd uit uw abonnement

* [Azure Security Center gebruiken om identiteit en toegang te bewaken (preview)](../security-center/security-center-identity-access.md)

* [Azure Policy gebruiken](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: eenmalige aanmelding (SSO) met Azure Active Directory gebruiken

**Richt lijnen**: niet van toepassing; eenmalige aanmelding (SSO) voegt beveiligings-en gebruiks gemak toe wanneer gebruikers zich aanmelden bij aangepaste toepassingen in Azure Active Directory (AD). Toegang tot het ExpressRoute-besturings vlak van Azure (bijvoorbeeld Azure Portal) is al geïntegreerd met Azure Active Directory en is toegankelijk via de Azure Portal en de Azure Resource Manager REST API.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: multi-factor Authentication gebruiken voor alle op Azure Active Directory gebaseerde toegang

**Hulp**: schakel Azure Active Directory multi-factor Authentication in en volg de aanbevelingen voor Azure Security Center identiteits-en toegangs beheer.

* [MFA inschakelen in azure](../active-directory/authentication/howto-mfa-getstarted.md)

* [Identiteit en toegang bewaken in Azure Security Center](../security-center/security-center-identity-access.md)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: gebruik speciale machines (privileged Access workstations) voor alle beheer taken

**Hulp**: gebruik een privileged Access-werk station (Paw) met azure AD multi-factor Authentication (MFA) ingeschakeld om u aan te melden en uw Azure-Sentinel-gerelateerde resources te configureren.

* [Privileged Access Workstations](/windows-server/identity/securing-privileged-access/privileged-access-workstations)

* [Een implementatie van Azure AD Multi-Factor Authentication in de Cloud plannen](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: Logboeken en waarschuwingen voor verdachte activiteiten van beheerders accounts

**Hulp**: gebruik Azure Active Directory (AD) PRIVILEGED Identity Management (PIM) voor het genereren van Logboeken en waarschuwingen wanneer verdachte of onveilige activiteiten in de omgeving worden uitgevoerd.

Daarnaast kunt u met Azure AD-risico detectie waarschuwingen en rapporten bekijken over Risk ante gebruikers gedrag.

* [Privileged Identity Management implementeren (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

* [Meer informatie over Azure AD-risico detectie](../active-directory/identity-protection/overview-identity-protection.md)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: Azure-resources alleen beheren vanaf goedgekeurde locaties

**Hulp**: gebruik benoemde locaties voor voorwaardelijke toegang om alleen toegang toe te staan tot de Azure Portal vanuit specifieke logische groepen met IP-adresbereiken of landen/regio's.

* [Benoemde locaties configureren in azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="39-use-azure-active-directory"></a>3,9: Azure Active Directory gebruiken

**Hulp**: gebruik Azure Active Directory (AD) als centraal verificatie-en autorisatie systeem voor uw onderverklikker instanties van Azure. Azure AD beveiligt gegevens door gebruik te maken van sterke versleuteling voor gegevens in rust en onderweg. Azure AD bevat ook zouten, hashes en veilige gebruikers referenties.

* [Een Azure AD-exemplaar maken en configureren](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: regel matig gebruikers toegang controleren en afstemmen

**Hulp**: Azure Active Directory (AD) bevat logboeken waarmee u verouderde accounts kunt detecteren. Daarnaast kunt u Azure Identity Access revisies gebruiken om groepslid maatschappen en de toegang tot bedrijfs toepassingen en roltoewijzingen op efficiënte wijze te beheren. Gebruikers toegang kan regel matig worden gecontroleerd om ervoor te zorgen dat alleen de juiste gebruikers toegang hebben.

* [Meer informatie over Azure AD-rapportage](../active-directory/reports-monitoring/index.yml)

* [Beoordelingen over Azure Identity Access gebruiken](../active-directory/governance/access-reviews-overview.md)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: controle pogingen om toegang te krijgen tot gedeactiveerde referenties

**Hulp**: gebruik Azure Active Directory (AD) als centraal verificatie-en autorisatie systeem voor uw Azure ExpressRoute-resources. Azure AD beveiligt gegevens door gebruik te maken van sterke versleuteling voor gegevens in rust en onderweg. Azure AD bevat ook zouten, hashes en veilige gebruikers referenties.

U hebt toegang tot de Azure AD-aanmeldings activiteit, de controle-en risico gebeurtenis logboek bronnen, waarmee u kunt integreren met Azure Sentinel of een SIEM van derden.

U kunt dit proces stroom lijnen door Diagnostische instellingen voor Azure AD-gebruikers accounts te maken en de audit logboeken en aanmeldings logboeken te verzenden naar een Log Analytics-werk ruimte. U kunt de gewenste logboek waarschuwingen configureren in Log Analytics.

* [Azure-activiteiten logboeken integreren in Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

* [Azure-Sentinel aan de trein](../sentinel/quickstart-onboard.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: waarschuwing voor de afwijking van het aanmeldings gedrag van accounts

**Richt lijnen**: voor de afwijking van het aanmeldings gedrag van accounts op het besturings vlak (bijvoorbeeld Azure Portal), gebruikt u de functies Azure AD Identity Protection en risico detectie om automatische antwoorden te configureren op gedetecteerde verdachte acties die betrekking hebben op gebruikers identiteiten. U kunt ook gegevens opnemen in azure Sentinel voor verder onderzoek.

* [Hoe kan ik een Risk ante aanmelding van Azure AD weer geven?](../active-directory/identity-protection/overview-identity-protection.md)

* [Risico beleid voor identiteits beveiliging configureren en inschakelen](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

* [Azure-Sentinel onboarden](../sentinel/quickstart-onboard.md)

**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: klant

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: micro soft biedt toegang tot relevante klant gegevens tijdens ondersteunings scenario's

**Richt lijnen**: niet van toepassing; Klanten-lockbox wordt niet ondersteund voor Azure ExpressRoute.

* [Lijst met door Klanten-lockbox ondersteunde services](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

## <a name="data-protection"></a>Gegevensbescherming

*Zie [beveiligings beheer: gegevens beveiliging](../security/benchmarks/security-control-data-protection.md)voor meer informatie.*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: een inventaris van gevoelige informatie onderhouden

**Hulp**: Tags gebruiken om Azure-resources te helpen bij het bijhouden of verwerken van gevoelige informatie.

* [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: systemen isoleren die gevoelige informatie opslaan of verwerken

**Richt lijnen**: afzonderlijke abonnementen en/of beheer groepen implementeren voor ontwikkeling, testen en productie.

* [Aanvullende Azure-abonnementen maken](../cost-management-billing/manage/create-subscription.md)

* [Beheergroepen maken](../governance/management-groups/create-management-group-portal.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: niet-geautoriseerde overdracht van gevoelige gegevens controleren en blok keren

**Hulp**: micro soft beheert de onderliggende infra structuur voor Azure ExpressRoute-circuits en gerelateerde resources en heeft strikte controles geïmplementeerd om verlies of bloot stelling van klant gegevens te voor komen.

* [Informatie over beveiliging van klanten in azure](../security/fundamentals/protection-customer-data.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: micro soft

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: alle gevoelige gegevens in de overdracht versleutelen

**Richt lijnen**: IPSec is een IETF Standard. De gegevens worden versleuteld op het niveau van de Internet Protocol (IP) of netwerklaag 3. U kunt IPsec gebruiken voor het versleutelen van een end-to-end-verbinding tussen uw on-premises netwerk en uw virtuele netwerk (VNET) in Azure.

* [Site-naar-site-IPSEC configureren via ExpressRoute](./site-to-site-vpn-over-microsoft-peering.md)

Site-naar-site-IPSEC configureren via ExpressRoute: https://docs.microsoft.com/azure/expressroute/site-to-site-vpn-over-microsoft-peering

**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: gedeeld

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: een actief detectie hulpprogramma gebruiken om gevoelige gegevens te identificeren

**Richt lijnen**: niet van toepassing; De klant gegevens worden niet opgeslagen met Azure ExpressRoute.

* [Meer informatie over beveiligings controles voor Azure ExpressRoute](./expressroute-security-controls.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: Azure RBAC gebruiken om de toegang tot resources te beheren

**Richt lijnen**: u kunt het deel venster identiteits-en toegangs beheer (IAM) in de Azure portal voor uw abonnement gebruiken om op rollen gebaseerd toegangs beheer op basis van Azure (Azure RBAC) te configureren. De rollen worden toegepast op gebruikers, groepen, service-principals en beheerde identiteiten in Active Directory. U kunt ingebouwde rollen of aangepaste rollen gebruiken voor individuen en groepen.

Azure ExpressRoute heeft ook de eigenaar van circuits en circuit gebruikers rollen. Circuit gebruikers zijn eigen aren van virtuele netwerk gateways die zich niet in hetzelfde abonnement bevinden als het ExpressRoute-circuit. De circuiteigenaar heeft de bevoegdheid om autorisaties op elk gewenst moment te wijzigen en in te trekken. Het intrekken van een autorisatie heeft tot gevolg dat alle koppelingsverbindingen uit het abonnement waarvan de toegangsrechten zijn ingetrokken, worden verwijderd. Circuitgebruikers kunnen autorisaties inwisselen (één autorisatie per virtueel netwerk).

Daarnaast kunnen partners die gebruikmaken van de ExpressRoute partner Resource Manager-API op rollen gebaseerd toegangs beheer Toep assen op de expressRouteCrossConnection-resource. Met deze besturings elementen kunt u machtigingen definiëren waarvoor gebruikers accounts de expressRouteCrossConnection-resource kunnen wijzigen en peering-configuraties toevoegen/bijwerken/verwijderen.

* [Meer informatie over Azure RBAC](../role-based-access-control/overview.md)

* [Gebruik Azure RBAC in de ExpressRoute-partner Resource Manager-API](./cross-connections-api-development.md)

* [Beheerders rollen in ExpressRoute begrijpen](./expressroute-howto-linkvnet-portal-resource-manager.md#connect-a-vnet-to-a-circuit---different-subscription)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: voor komen dat gegevens verlies op basis van host wordt gebruikt voor het afdwingen van toegangs beheer

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources. Micro soft beheert de onderliggende infra structuur voor Azure Sentinel en heeft strikte controles geïmplementeerd om verlies of bloot stelling van klant gegevens te voor komen.

* [Azure-klant gegevens beveiliging](../security/fundamentals/protection-customer-data.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: gevoelige informatie op rest versleutelen

**Richt lijnen**: MACsec is een IEEE-standaard. De gegevens worden versleuteld op het MAC-niveau (Media Access Control) of netwerklaag 2. U kunt MACsec gebruiken om de fysieke koppelingen tussen uw netwerk apparaten en de netwerk apparaten van micro soft te versleutelen wanneer u via ExpressRoute direct verbinding maakt met micro soft. MACsec is standaard uitgeschakeld op ExpressRoute direct-poorten. U brengt uw eigen MACsec-sleutel voor versleuteling en slaat deze op in Azure Key Vault. U bepaalt wanneer u de sleutel wilt draaien.

* [Informatie over Point-to-Point-versleuteling in azure ExpressRoute](./expressroute-about-encryption.md)

* [MACsec configureren op ExpressRoute direct-poorten](./expressroute-howto-macsec.md)

* [Azure-beveiligings basislijn voor Key Vault](../key-vault/general/security-baseline.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: wijzigingen in essentiële Azure-resources vastleggen en waarschuwen

**Hulp**: gebruik Azure monitor met het Azure-activiteiten logboek om waarschuwingen te maken wanneer wijzigingen worden aangebracht in productie-exemplaren van Azure ExpressRoute en andere essentiële of gerelateerde resources.

* [Waarschuwingen maken voor gebeurtenissen in het Azure-activiteiten logboek](../azure-monitor/platform/alerts-activity-log.md)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

## <a name="vulnerability-management"></a>Beheer van beveiligingsproblemen

*Zie [beveiligings beheer: beveiligingslek beheer](../security/benchmarks/security-control-vulnerability-management.md)voor meer informatie.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: automatische hulpprogram ma's voor het scannen van beveiligings problemen uitvoeren

**Richt lijnen**: niet van toepassing; Micro soft voert beveiligings beheer uit op de onderliggende systemen die Azure ExpressRoute ondersteunen.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: micro soft

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: geautomatiseerde oplossing voor patch beheer voor besturings systemen implementeren

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: Implementeer een oplossing voor geautomatiseerd patch beheer voor software titels van derden

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: vergelijken van back-to-back-problemen

**Richt lijnen**: niet van toepassing; Micro soft voert beveiligings beheer uit op de onderliggende systemen die Azure ExpressRoute ondersteunen.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: een risico classificatie proces gebruiken om prioriteit te geven aan het herstel van ontdekte beveiligings problemen

**Richt lijnen**: niet van toepassing; Micro soft voert beveiligings beheer uit op de onderliggende systemen die Azure ExpressRoute ondersteunen.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

## <a name="inventory-and-asset-management"></a>Inventarisatie en asset-management

*Zie voor meer informatie [beveiligings beheer: inventarisatie en activa beheer](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: automatische Asset-detectie oplossing gebruiken

**Hulp**: Azure resource Graph gebruiken voor het opvragen/detecteren van alle resources (zoals compute, opslag, netwerk, poorten en protocollen enz.) binnen uw abonnement (en). Zorg ervoor dat de juiste (Lees-) machtigingen voor uw Tenant en alle Azure-abonnementen en resources in uw abonnementen inventariseren.

Hoewel klassieke Azure-resources kunnen worden gedetecteerd via resource grafiek, is het raadzaam om Azure Resource Manager resources te maken en te gebruiken.

* [Query's maken met Azure resource Graph](../governance/resource-graph/first-query-portal.md)

* [Uw Azure-abonnementen weer geven](/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

* [Meer informatie over Azure RBAC](../role-based-access-control/overview.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="62-maintain-asset-metadata"></a>6,2: meta gegevens van activa onderhouden

**Richt lijnen**: Tags Toep assen op Azure-resources die meta gegevens geven om ze logisch in een taxonomie te organiseren.

* [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: niet-geautoriseerde Azure-resources verwijderen

**Richt lijnen**: Gebruik labels, beheer groepen en afzonderlijke abonnementen, waar nodig, om Azure-resources te organiseren en bij te houden. Sluit de inventaris regel matig af en zorg ervoor dat niet-geautoriseerde resources tijdig worden verwijderd uit het abonnement.

Gebruik Azure Policy bovendien om beperkingen te leggen voor het type resources dat kan worden gemaakt in klant abonnementen met behulp van de volgende ingebouwde beleids definities:
- Niet toegestane resourcetypen
- Toegestane brontypen

* [Aanvullende Azure-abonnementen maken](../cost-management-billing/manage/create-subscription.md)

* [Beheergroepen maken](../governance/management-groups/create-management-group-portal.md)

* [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6,4: een inventaris van goedgekeurde Azure-resources definiëren en onderhouden

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: monitor voor niet-goedgekeurde Azure-resources

**Hulp**: gebruik Azure Policy om beperkingen toe te voegen voor het type resources dat kan worden gemaakt in uw abonnement (en).

Gebruik Azure resource Graph voor het opvragen/detecteren van resources binnen hun abonnement (en). Zorg ervoor dat alle Azure-resources die aanwezig zijn in de omgeving, zijn goedgekeurd.

* [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

* [Query's maken met Azure resource Graph](../governance/resource-graph/first-query-portal.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: monitor voor niet-goedgekeurde software toepassingen binnen reken resources

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: niet-goedgekeurde Azure-resources en software toepassingen verwijderen

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="68-use-only-approved-applications"></a>6,8: alleen goedgekeurde toepassingen gebruiken

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="69-use-only-approved-azure-services"></a>6,9: alleen goedgekeurde Azure-Services gebruiken

**Hulp: gebruik** Azure Policy om beperkingen toe te voegen voor het type resources dat kan worden gemaakt in klant abonnement (en) met de volgende ingebouwde beleids definities:
- Niet toegestane resourcetypen
- Toegestane brontypen

* [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

* [Een specifiek resource type weigeren met Azure Policy](../governance/policy/samples/index.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: een inventaris van goedgekeurde software titels onderhouden

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: de mogelijkheid van gebruikers om te communiceren met Azure Resource Manager beperken

**Hulp** bij het configureren van voorwaardelijke toegang van Azure om gebruikers de mogelijkheid te bieden om te communiceren met Azure Resource Manager door ' blok toegang ' te configureren voor de app Microsoft Azure management.

* [Voorwaardelijke toegang configureren om de toegang tot Azure Resource Manager te blok keren](../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: de mogelijkheid van gebruikers om scripts uit te voeren binnen reken bronnen beperken

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: toepassingen met een hoog risico fysiek of logisch scheiden

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor webtoepassingen die worden uitgevoerd op Azure App Service of reken bronnen.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

## <a name="secure-configuration"></a>Veilige configuratie

*Zie [beveiligings beheer: beveiligde configuratie](../security/benchmarks/security-control-secure-configuration.md)voor meer informatie.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: veilige configuraties instellen voor alle Azure-resources

**Richt lijnen**: standaard beveiligings configuraties voor Azure ExpressRoute definiëren en implementeren met Azure Policy. Gebruik Azure Policy aliassen in de naam ruimte ' micro soft. netwerk ' om aangepaste beleids regels te maken om de netwerk configuratie van uw ExpressRoute te controleren of af te dwingen.

* [Beschik bare Azure Policy aliassen weer geven](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: veilige configuraties van besturings systemen instellen

**Richt lijnen**: niet van toepassing; deze richt lijn is bedoeld voor reken resources.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: Beveilig Azure-resource configuraties onderhouden

**Hulp**: gebruik Azure Policy [deny] en [implementeren indien niet aanwezig] voor het afdwingen van beveiligde instellingen voor uw Azure-resources.

* [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

* [Azure Policy effecten begrijpen](../governance/policy/concepts/effects.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: veilige configuraties van besturings systemen onderhouden

**Richt lijnen**: niet van toepassing; deze richt lijn is bedoeld voor reken resources.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: de configuratie van Azure-resources veilig opslaan

**Richt lijnen**: als u aangepaste Azure Policy definities gebruikt, kunt u Azure DevOps of Azure opslag plaatsen gebruiken om uw code veilig op te slaan en te beheren.

* [Code opslaan in azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [Documentatie voor Azure opslag plaatsen](/azure/devops/repos/index?view=azure-devops)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: aangepaste installatie kopieën van een besturings systeem veilig opslaan

**Richt lijnen**: niet van toepassing; deze richt lijn is bedoeld voor reken resources.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: hulpprogram ma's voor configuratie beheer voor Azure-resources implementeren

**Richt lijnen**: standaard beveiligings configuraties voor Azure ExpressRoute definiëren en implementeren met Azure Policy. Gebruik Azure Policy aliassen in de naam ruimte ' micro soft. netwerk ' om aangepaste beleids regels te maken om de netwerk configuratie van uw ExpressRoute te controleren of af te dwingen.

* [Beschik bare Azure Policy aliassen weer geven](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: hulpprogram ma's voor configuratie beheer voor besturings systemen implementeren

**Richt lijnen**: niet van toepassing; deze richt lijn is bedoeld voor reken resources.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: geautomatiseerde configuratie bewaking voor Azure-resources implementeren

**Hulp**: gebruik ingebouwde Azure Policy definities en Azure Policy aliassen in de naam ruimte ' micro soft. Network ' om aangepaste beleids regels te maken om systeem configuraties te Signa lering, te controleren en af te dwingen. Gebruik Azure Policy [audit], [deny] en [implementeren indien niet aanwezig] om automatisch configuraties af te dwingen voor uw Azure-resources.

* [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: geautomatiseerde configuratie bewaking voor besturings systemen implementeren

**Richt lijnen**: niet van toepassing; deze richt lijn is bedoeld voor reken resources.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="711-manage-azure-secrets-securely"></a>7,11: Azure-geheimen veilig beheren

**Richt lijnen**: u kunt MACsec gebruiken om de fysieke koppelingen tussen uw netwerk apparaten en de netwerk apparaten van micro soft te versleutelen wanneer u via ExpressRoute direct verbinding maakt met micro soft. MACsec is standaard uitgeschakeld op ExpressRoute direct-poorten. U brengt uw eigen MACsec-sleutel voor versleuteling en slaat deze op in Azure Key Vault. U bepaalt wanneer u de sleutel wilt draaien.

* [Een Key Vault-exemplaar maken om MACsec geheimen op te slaan in een nieuwe resource groep](./expressroute-howto-macsec.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: identiteiten veilig en automatisch beheren

**Richt lijnen**: niet van toepassing; Azure ExpressRoute-verbindingen en-bronnen maken geen gebruik van beheerde identiteiten.

* [Azure-Services die beheerde identiteiten ondersteunen](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: onbedoelde referentie blootstelling elimineren

**Richt lijnen**: referentie scanner implementeren om referenties in code te identificeren. Referentie scanner stimuleert ook het verplaatsen van gedetecteerde referenties naar veiliger locaties, zoals Azure Key Vault.

* [Referentie scanner instellen](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

## <a name="malware-defense"></a>Beveiliging tegen malware

*Zie [beveiligings beheer: verdediging tegen malware](../security/benchmarks/security-control-malware-defense.md)voor meer informatie.*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: centraal beheerde anti-malware-software gebruiken

**Richt lijnen**: niet van toepassing; deze richt lijn is bedoeld voor reken resources. Micro soft anti-malware is ingeschakeld op de onderliggende host die ondersteuning biedt voor Azure-Services (bijvoorbeeld Azure App Service), maar wordt niet uitgevoerd op de inhoud van de klant.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: scan bestanden die moeten worden geüpload naar niet-reken resources van Azure

**Hulp**: micro soft anti-malware is ingeschakeld op de onderliggende host die ondersteuning biedt voor Azure-Services (bijvoorbeeld Azure ExpressRoute), maar wordt niet uitgevoerd op de inhoud van de klant.

Het is uw verantwoordelijkheid om vooraf te scannen op inhoud die wordt geüpload naar niet-reken resources van Azure. Micro soft heeft geen toegang tot klant gegevens en kan daarom geen anti-malware scans uitvoeren van klant inhoud namens u.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: controleren of anti-malware-software en hand tekeningen zijn bijgewerkt

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources. Micro soft anti-malware is ingeschakeld op de onderliggende host die ondersteuning biedt voor Azure-Services, maar wordt niet uitgevoerd op de inhoud van de klant.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

## <a name="data-recovery"></a>Gegevensherstel

*Zie [beveiligings beheer: gegevens herstel](../security/benchmarks/security-control-data-recovery.md)voor meer informatie.*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: controleren op regel matige automatische back-ups

**Richt lijnen**: niet van toepassing; De klant gegevens worden niet opgeslagen met Azure ExpressRoute.

* [Meer informatie over beveiligings controles voor Azure ExpressRoute](./expressroute-security-controls.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: volledige back-ups van het systeem uitvoeren en een back-up maken van door de klant beheerde sleutels

**Richt lijnen**: als u Azure Key Vault gebruikt om uw MACsec-geheimen op te slaan, moet u regel matig automatische back-ups van uw sleutels maken.

* [Back-ups maken van Key Vault sleutels](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: alle back-ups valideren, inclusief door de klant beheerde sleutels

**Richt lijnen**: het herstellen van een back-up van door de klant beheerde sleutels testen.

* [Sleutel kluis sleutels herstellen in azure](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

* [De connectiviteit van Azure ExpressRoute-circuits testen na het configureren van MACsec](./expressroute-howto-macsec.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: zorg voor de bescherming van back-ups en door de klant beheerde sleutels

**Hulp**: Schakel Soft-Delete in Key Vault in om sleutels te beschermen tegen onbedoelde of schadelijke verwijdering.

* [Soft-Delete in Key Vault inschakelen](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

## <a name="incident-response"></a>Reageren op incidenten

*Zie voor meer informatie [beveiligings beheer: reactie op incidenten](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: een hand leiding voor reactie op incidenten maken

**Hulp**: een antwoord gids voor incidenten maken voor uw organisatie. Zorg ervoor dat er schriftelijke incidenten abonnementen zijn die alle werk rollen definiëren, evenals fasen van incident handling/management van detectie tot een beoordeling van het incident.

* [Werk stroom automatisering configureren in Azure Security Center](../security-center/security-center-planning-and-operations-guide.md)

* [Richt lijnen voor het bouwen van uw eigen beveiligings incident antwoord proces](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Micro soft Security Response Center anatomie van een incident](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Klant kan ook gebruikmaken van de hand leiding voor de verwerking van het computer beveiligings incident van het NIST om hulp te bieden bij het maken van een eigen incident respons plan](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: een beoordelings procedure voor incidenten en prioriteits procedures maken

**Hulp**: Security Center wijst aan elke waarschuwing een Ernst toe om u te helpen bepalen welke waarschuwingen het eerst moeten worden onderzocht. De ernst is gebaseerd op de manier waarop vertrouwen Security Center is in de zoek actie of het analyse programma dat wordt gebruikt om de waarschuwing te geven, evenals het betrouwbaarheids niveau dat er schadelijke bedoelingen zijn achter de activiteit die tot de waarschuwing heeft geleid.

Daarnaast kunt u ook duidelijk abonnementen markeren (voor bijvoorbeeld productie, niet-productie) en maak een naamgevings systeem om Azure-resources duidelijk te identificeren en te categoriseren.

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="103-test-security-response-procedures"></a>10,3: procedures voor beveiligings antwoorden testen

**Richt lijnen**: oefent oefeningen uit om de respons mogelijkheden van uw systeem op een gewone uitgebracht te testen. Identificeer zwakke punten en tussen ruimten en wijzig zo nodig het schema.

* [Raadpleeg de publicatie van het NIST: hand leiding voor het testen, trainen en uitoefenen van Program Ma's voor IT-plannen en-mogelijkheden](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: contact gegevens van het beveiligings incident opgeven en waarschuwings meldingen configureren voor beveiligings incidenten

**Hulp**: contact gegevens van beveiligings incidenten worden door micro soft gebruikt om contact met u op te nemen als het micro soft Security Response Center (MSRC) detecteert dat de gegevens van de klant zijn geopend door een onrecht matige of niet-gemachtigde partij. Bekijk incidenten na het feit om te controleren of de problemen zijn opgelost.

* [De Azure Security Center Security-contact persoon instellen](../security-center/security-center-provide-security-contact-details.md)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: beveiligings waarschuwingen opnemen in uw reactie systeem van uw incident

**Richt lijnen**: uw Azure Security Center waarschuwingen en aanbevelingen exporteren met de functie continue export. Met doorlopend exporteren kunt u waarschuwingen en aanbevelingen hand matig of op een doorlopende manier exporteren. U kunt de Azure Security Center Data Connector gebruiken om de Sentinel van waarschuwingen te streamen.

* [Continue export configureren](../security-center/continuous-export.md)

* [Waarschuwingen streamen naar Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: de reactie op beveiligings waarschuwingen automatiseren

**Hulp**: gebruik de functie werk stroom automatisering in azure Security Center om automatisch reacties te activeren via ' Logic apps ' in beveiligings waarschuwingen en aanbevelingen.

* [Werk stroom automatisering en Logic Apps configureren](../security-center/workflow-automation.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

## <a name="penetration-tests-and-red-team-exercises"></a>Penetratietests en Red Team-oefeningen

*Zie voor meer informatie [Security Control: Indringings tests en Red team-oefeningen](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: voert regel matig indringings tests van uw Azure-resources uit en zorgt voor herbemiddeling van alle essentiële beveiligings resultaten

**Richt lijnen**: * [Volg de regels van micro soft om ervoor te zorgen dat de indringings tests niet worden geschonden door het micro soft-beleid](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

* [U vindt hier meer informatie over de strategie van micro soft en de uitvoering van Red Teaming en live site indringings tests met door micro soft beheerde Cloud infrastructuur,-services en-toepassingen.](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: gedeeld

## <a name="next-steps"></a>Volgende stappen

- Zie de [Azure Security-Bench Mark](../security/benchmarks/overview.md)
- Meer informatie over [Azure-beveiligings basislijnen](../security/benchmarks/security-baselines-overview.md)