---
title: Azure-beveiligings basislijn voor Azure Data Box
description: Azure-beveiligings basislijn voor Azure Data Box
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 06/22/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 6c2a15ac8d0863539ca878a048940b19794e920d
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94842904"
---
# <a name="azure-security-baseline-for-azure-data-box"></a>Azure-beveiligings basislijn voor Azure Data Box

De Azure-beveiligings basislijn voor Azure Data Box bevat aanbevelingen waarmee u de beveiligings postuur van uw implementatie kunt verbeteren.

De basis lijn voor deze service wordt opgehaald uit de [Azure Security Bench Mark-versie 1,0](../security/benchmarks/overview.md), die aanbevelingen biedt over hoe u uw cloud oplossingen kunt beveiligen in azure met onze richt lijnen voor best practices.

Zie het [overzicht van Azure Security-basis lijnen](../security/benchmarks/security-baselines-overview.md)voor meer informatie.

## <a name="network-security"></a>Netwerkbeveiliging

*Zie [beveiligings beheer: netwerk beveiliging](../security/benchmarks/security-control-network-security.md)voor meer informatie.*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Azure-resources in virtuele netwerken beveiligen

**Richt lijnen**: niet van toepassing; uw Azure Data Box kan niet worden gekoppeld aan een virtueel netwerk. U beheert het verkeer van de Data Box naar door Azure gehoste opslag via de Azure Portal. Wanneer u gebruikmaakt van Data Box, worden gegevens overgedragen via de Azure-backbone.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1,2: de configuratie en het verkeer van virtuele netwerken, subnetten en Nic's bewaken en vastleggen

**Richt lijnen**: niet van toepassing; uw Azure Data Box kan niet worden gekoppeld aan een virtueel netwerk. U beheert het verkeer van de Data Box naar door Azure gehoste opslag via de Azure Portal. Wanneer u gebruikmaakt van Data Box, worden gegevens overgedragen via de Azure-backbone.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="13-protect-critical-web-applications"></a>1,3: essentiële webtoepassingen beveiligen

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor webtoepassingen die worden uitgevoerd op Azure App Service of reken bronnen.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: communicatie met bekende schadelijke IP-adressen weigeren

**Richt lijnen**: niet van toepassing; uw Azure Data Box kan niet worden gekoppeld aan een virtueel netwerk. U beheert het verkeer van de Data Box naar door Azure gehoste opslag via de Azure Portal. Wanneer u gebruikmaakt van Data Box, worden gegevens overgedragen via de Azure-backbone.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="15-record-network-packets"></a>1,5: netwerk pakketten opnemen

**Richt lijnen**: niet van toepassing; uw Azure Data Box kan niet worden gekoppeld aan een virtueel netwerk. U beheert het verkeer van de Data Box naar door Azure gehoste opslag via de Azure Portal. Wanneer u gebruikmaakt van Data Box, worden gegevens overgedragen via de Azure-backbone.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: op netwerk gebaseerde inbreuk detectie/indringings systemen (ID'S/IP-adressen) implementeren

**Richt lijnen**: richt lijnen: de eind punten die worden gebruikt door Azure data box worden allemaal beheerd door micro soft. U bent verantwoordelijk voor alle extra besturings elementen die u wilt implementeren op uw on-premises systemen.

* [Inzicht in Azure Data Box beveiliging](./data-box-security.md)

* [Poort gegevens voor Azure Data Box](./data-box-system-requirements.md#port-requirements)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: micro soft

### <a name="17-manage-traffic-to-web-applications"></a>1,7: verkeer naar webtoepassingen beheren

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor webtoepassingen die worden uitgevoerd op Azure App Service of reken bronnen.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: de complexiteit en administratieve overhead van netwerk beveiligings regels minimaliseren

**Richt lijnen**: niet van toepassing; uw Azure Data Box kan niet worden gekoppeld aan een virtueel netwerk.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: standaard beveiligings configuraties voor netwerk apparaten onderhouden

**Richt lijnen**: niet van toepassing; uw Azure Data Box kan niet worden gekoppeld aan een virtueel netwerk.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="110-document-traffic-configuration-rules"></a>1,10: configuratie regels voor het document verkeer

**Richt lijnen**: niet van toepassing; uw Azure Data Box kan niet worden gekoppeld aan een virtueel netwerk.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: gebruik automatische hulpprogram ma's om netwerk bron configuraties te bewaken en wijzigingen te detecteren

**Richt lijnen**: niet van toepassing; uw Azure Data Box kan niet worden gekoppeld aan een virtueel netwerk.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

## <a name="logging-and-monitoring"></a>Logboekregistratie en bewaking

*Zie voor meer informatie [beveiligings beheer: logboek registratie en controle](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: goedgekeurde tijd synchronisatie bronnen gebruiken

**Richt lijnen**: micro soft onderhoudt de tijd bron die wordt gebruikt voor Azure-resources, bijvoorbeeld voor tijds tempels in de logboeken. Azure Data Box tijd kan worden gedrift als deze niet is verbonden met het netwerk dat toegang heeft tot de NTP-server op de site van de klant.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="22-configure-central-security-log-management"></a>2,2: Centraal beveiligings logboek beheer configureren

**Richt lijnen**: voor elke stap in uw data Box order, kunt u meerdere acties ondernemen om de toegang tot de order te beheren, de gebeurtenissen te controleren, de volg orde bij te houden en de verschillende logboeken te interpreteren die worden gegenereerd.

* [Informatie over bijhouden en vastleggen van gebeurtenissen voor uw Azure Data Box](./data-box-logs.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: controle logboek registratie inschakelen voor Azure-resources

**Richt lijnen**: voor elke stap in uw data Box order, kunt u meerdere acties ondernemen om de toegang tot de order te beheren, de gebeurtenissen te controleren, de volg orde bij te houden en de verschillende logboeken te interpreteren die worden gegenereerd.

* [Informatie over bijhouden en vastleggen van gebeurtenissen voor uw Azure Data Box](./data-box-logs.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: beveiligings logboeken verzamelen van besturings systemen

**Richt lijnen**: deze aanbeveling is bedoeld voor reken resources. Data Box heeft controle logboeken en een ondersteunings pakket dat beveiligings Logboeken bevat.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: micro soft

### <a name="25-configure-security-log-storage-retention"></a>2,5: Bewaar beveiliging van het beveiligings logboek configureren

**Richt lijnen**: niet van toepassing

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="26-monitor-and-review-logs"></a>2,6: Logboeken bewaken en controleren

**Richt lijnen**: voor elke stap in uw data Box order, kunt u meerdere acties ondernemen om de toegang tot de order te beheren, de gebeurtenissen te controleren, de volg orde bij te houden en de verschillende logboeken te interpreteren die worden gegenereerd.

* [Informatie over bijhouden en vastleggen van gebeurtenissen voor uw Azure Data Box](./data-box-logs.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: waarschuwingen inschakelen voor afwijkende activiteiten

**Richt lijnen**: voor elke stap in uw data Box order, kunt u meerdere acties ondernemen om de toegang tot de order te beheren, de gebeurtenissen te controleren, de volg orde bij te houden en de verschillende logboeken te interpreteren die worden gegenereerd.

* [Informatie over bijhouden en vastleggen van gebeurtenissen voor uw Azure Data Box](./data-box-logs.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="28-centralize-anti-malware-logging"></a>2,8: registratie van anti-malware centraliseren

**Richt lijnen**: niet van toepassing; Azure Data Box geen aan anti-malware gerelateerde logboeken verwerkt of produceert.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="29-enable-dns-query-logging"></a>2,9: DNS-query logboek registratie inschakelen

**Richt lijnen**: niet van toepassing; Azure Data Box worden geen DNS-gerelateerde logboeken verwerkt of geproduceerd.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="210-enable-command-line-audit-logging"></a>2,10: controle logboek registratie op opdracht regel inschakelen

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

## <a name="identity-and-access-control"></a>Identiteits- en toegangsbeheer

*Zie [beveiligings beheer: identiteits-en toegangs beheer](../security/benchmarks/security-control-identity-access-control.md)voor meer informatie.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: een inventaris van beheerders accounts onderhouden

**Richt lijnen**: Houd een inventarisatie bij van de gebruikers accounts met beheerders toegang tot uw Azure data box. U kunt het deel venster identiteits-en toegangs beheer (IAM) in de Azure Portal voor uw abonnement gebruiken om toegangs beheer op basis van rollen (Azure RBAC) te configureren. De rollen worden toegepast op gebruikers, groepen, service-principals en beheerde identiteiten in Active Directory. u kunt bepalen wie toegang heeft tot uw bestelling wanneer de order voor het eerst wordt gemaakt. Stel Azure-rollen in verschillende bereiken in om de toegang tot de Data Box order te beheren. Een Azure-rol bepaalt het type toegang – lezen/schrijven, alleen-lezen, lezen-schrijven naar een subset van bewerkingen.

* [Informatie over aangepaste rollen](../role-based-access-control/custom-roles.md)

* [Azure RBAC voor werkmappen configureren](../sentinel/quickstart-get-visibility.md)

* [Meer informatie over het instellen van toegangs beheer voor de order](./data-box-logs.md#set-up-access-control-on-the-order)

**Azure Security Center bewaking**: Nee

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

**Richt lijnen**: niet van toepassing; toegang tot uw Data Box order is via de Azure Portal en is gereserveerd voor accounts met de Tenant rol van eigenaar of bijdrager.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: multi-factor Authentication gebruiken voor alle op Azure Active Directory gebaseerde toegang

**Richt lijnen**: niet van toepassing

**Azure Security Center bewaking**: Nee

**Verantwoordelijkheid**: niet van toepassing

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: gebruik speciale machines (privileged Access workstations) voor alle beheer taken

**Hulp**: gebruik een privileged Access-werk station (Paw) met Azure AD multi-factor Authentication (MFA) waarmee u uw Azure data Box orders kunt aanmelden en configureren.

* [Privileged Access Workstations](/windows-server/identity/securing-privileged-access/privileged-access-workstations)

* [Een implementatie van Azure AD Multi-Factor Authentication in de Cloud plannen](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: Logboeken en waarschuwingen voor verdachte activiteiten van beheerders accounts

**Richt lijnen**: niet van toepassing, Azure data box heeft geen eigen beheerders account. U kunt deze openen via de Azure Portal. U kunt uw Azure-abonnement echter configureren voor het gebruik van Azure Active Directory (AD) Privileged Identity Management (PIM) voor het genereren van Logboeken en waarschuwingen wanneer verdachte of onveilige activiteiten in de omgeving worden uitgevoerd.

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

**Hulp**: gebruik Azure Active Directory (AD) als centraal verificatie-en autorisatie systeem, indien van toepassing. Azure AD beveiligt gegevens door gebruik te maken van sterke versleuteling voor gegevens in rust en onderweg. Azure AD bevat ook zouten, hashes en veilige gebruikers referenties.

* [Een Azure AD-exemplaar maken en configureren](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: regel matig gebruikers toegang controleren en afstemmen

**Hulp**: Azure Active Directory (AD) bevat logboeken waarmee u verouderde accounts kunt detecteren. Daarnaast kunt u Azure Identity Access revisies gebruiken om groepslid maatschappen en de toegang tot bedrijfs toepassingen en roltoewijzingen op efficiënte wijze te beheren. Gebruikers toegang kan regel matig worden gecontroleerd om ervoor te zorgen dat alleen de juiste gebruikers toegang hebben.

Voor het Data Box apparaat wordt dit niet in realtime ondersteund. U kunt de logboeken aan het einde van de taak bekijken.

* [Meer informatie over Azure AD-rapportage](../active-directory/reports-monitoring/index.yml)

* [Beoordelingen over Azure Identity Access gebruiken](../active-directory/governance/access-reviews-overview.md)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: controle pogingen om toegang te krijgen tot gedeactiveerde referenties

**Hulp**: gebruik Azure Active Directory (AD) als centraal verificatie-en autorisatie systeem, indien van toepassing. Azure AD beveiligt gegevens door gebruik te maken van sterke versleuteling voor gegevens in rust en onderweg. Azure AD bevat ook zouten, hashes en veilige gebruikers referenties.

U hebt toegang tot de Azure AD-aanmeldings activiteit, de controle-en risico gebeurtenis logboek bronnen, waarmee u kunt integreren met Azure Sentinel of een SIEM van derden.

U kunt dit proces stroom lijnen door Diagnostische instellingen voor Azure AD-gebruikers accounts te maken en de audit logboeken en aanmeldings logboeken te verzenden naar een Log Analytics-werk ruimte. U kunt de gewenste logboek waarschuwingen configureren in Log Analytics.

Azure Data Box Service logboeken worden niet in Log Analytics werk ruimte geschreven.

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

**Hulp**: klanten-lockbox wordt momenteel niet ondersteund voor Azure data box.

* [Lijst met door Klanten-lockbox ondersteunde services](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: klant

## <a name="data-protection"></a>Gegevensbescherming

*Zie [beveiligings beheer: gegevens beveiliging](../security/benchmarks/security-control-data-protection.md)voor meer informatie.*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: een inventaris van gevoelige informatie onderhouden

**Richt lijnen**: niet van toepassing op Azure data box.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: systemen isoleren die gevoelige informatie opslaan of verwerken

**Richt lijnen**: Azure data box worden ingericht in het abonnement waar de resources waartoe u toegang wilt bevinden. Er is geen openbaar eind punt om te beveiligen of isoleren. Data Box toegang is beschikbaar voor gebruikers met een eigenaar of Inzender toegang tot het abonnement.

Tijdens het uploaden van gegevens naar Azure zijn het Data Box apparaat en de service die wordt gebruikt om de gegevens te uploaden, geïsoleerd.

* [Aan de slag met Azure Data Box](./data-box-quickstart-portal.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: micro soft

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: niet-geautoriseerde overdracht van gevoelige gegevens controleren en blok keren

**Hulp**: micro soft beheert de onderliggende infra structuur voor Azure data box en heeft strikte controles geïmplementeerd om verlies of bloot stelling van klant gegevens te voor komen. Wanneer Data Box zich op de site van de klant bevindt, volgt u aanbevolen procedures om ervoor te zorgen dat de overdracht van gevoelige gegevens wordt beveiligd.

* [Informatie over beveiliging van klanten in azure](../security/fundamentals/protection-customer-data.md)

**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: gedeeld

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: alle gevoelige gegevens in de overdracht versleutelen

**Hulp**: micro soft beheert de onderliggende infra structuur voor Azure data box en heeft strikte controles geïmplementeerd om verlies of bloot stelling van klant gegevens te voor komen. Wanneer Data Box zich op de site van de klant bevindt, volgt u aanbevolen procedures om ervoor te zorgen dat de overdracht van gevoelige gegevens wordt beveiligd.

* [Gegevens migratie in Azure Data Box begrijpen](./data-box-faq.md)

* [Overzicht van Data Box beveiliging](./data-box-security.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: gedeeld

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: een actief detectie hulpprogramma gebruiken om gevoelige gegevens te identificeren

**Hulp**: momenteel niet beschikbaar; de functies voor gegevens identificatie, classificatie en verlies preventie zijn nog niet beschikbaar voor Azure Data Box. micro soft beheert de onderliggende infra structuur voor Azure Data Box en heeft strikte controles geïmplementeerd om verlies of bloot stelling van klant gegevens te voor komen.

* [Informatie over beveiliging van klanten in azure](../security/fundamentals/protection-customer-data.md)

**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: niet van toepassing

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: Azure RBAC gebruiken om de toegang tot resources te beheren

**Hulp**: Zorg ervoor dat u eigenaar of Inzender toegang tot het abonnement hebt om een Data Box order te maken. U kunt ook Data Box lezer en Data Box Inzender rollen op het niveau van de resource definiëren.

* [Meer informatie over het aan de slag gaan met Azure Data Box](./data-box-quickstart-portal.md)

* [Meer informatie over het instellen van toegangs beheer](./data-box-logs.md#set-up-access-control-on-the-order)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: voor komen dat gegevens verlies op basis van host wordt gebruikt voor het afdwingen van toegangs beheer

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources. Micro soft beheert de onderliggende infra structuur voor Azure Data Box en heeft strikte controles geïmplementeerd om verlies of bloot stelling van klant gegevens te voor komen.

* [Azure-klant gegevens beveiliging](../security/fundamentals/protection-customer-data.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: gevoelige informatie op rest versleutelen

**Hulp**: Azure data Box implementeert AES 256-bits versleuteling voor Data-at-rest.

Azure Data Box implementeert AES 256-bits versleuteling voor Data-at-rest. Daarnaast beveiligt Azure Data Box de ontgrendelings sleutel van het apparaat (ook wel apparaatwachtwoord genoemd) dat wordt gebruikt om het apparaat te vergren delen via een versleutelings sleutel. De ontgrendelings sleutel voor een apparaat voor een Data Box order wordt standaard versleuteld met een door micro soft beheerde sleutel. Voor extra controle over de ontgrendelings sleutel van het apparaat kunt u ook een door de klant beheerde sleutel opgeven. Door de klant beheerde sleutels moeten worden gemaakt en opgeslagen in een Azure Key Vault.

* [Informatie over Data Box gegevens bescherming](./data-box-security.md)

* [Door de klant beheerde sleutels gebruiken in Azure Key Vault voor Azure Data Box](./data-box-customer-managed-encryption-key-portal.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: gedeeld

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: wijzigingen in essentiële Azure-resources vastleggen en waarschuwen

**Hulp**: gebruik Azure monitor met het Azure-activiteiten logboek om waarschuwingen te maken voor wanneer er wijzigingen worden aangebracht in azure data box en andere kritieke of gerelateerde resources.

* [Waarschuwingen maken voor gebeurtenissen in het Azure-activiteiten logboek](../azure-monitor/platform/alerts-activity-log.md)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

## <a name="vulnerability-management"></a>Beheer van beveiligingsproblemen

*Zie [beveiligings beheer: beveiligingslek beheer](../security/benchmarks/security-control-vulnerability-management.md)voor meer informatie.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: automatische hulpprogram ma's voor het scannen van beveiligings problemen uitvoeren

**Hulp**: micro soft voert beveiligings beheer uit op de onderliggende systemen die ondersteuning bieden voor Azure data box.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: micro soft

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: geautomatiseerde oplossing voor patch beheer voor besturings systemen implementeren

**Richt lijnen**: wanneer data box wordt geleverd, wordt deze geïnstalleerd met de nieuwste updates. Veld updates worden niet uitgevoerd.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: micro soft

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: Implementeer een oplossing voor geautomatiseerd patch beheer voor software titels van derden

**Richt lijnen**: wanneer data box wordt geleverd, wordt deze geïnstalleerd met de nieuwste updates. Veld updates worden niet uitgevoerd.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: micro soft

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: vergelijken van back-to-back-problemen

**Hulp**: micro soft voert beveiligings beheer uit op de onderliggende systemen die ondersteuning bieden voor Azure data box.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: micro soft

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: een risico classificatie proces gebruiken om prioriteit te geven aan het herstel van ontdekte beveiligings problemen

**Hulp**: micro soft voert beveiligings beheer uit op de onderliggende systemen die ondersteuning bieden voor Azure data box.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: micro soft

## <a name="inventory-and-asset-management"></a>Inventarisatie en asset-management

*Zie voor meer informatie [beveiligings beheer: inventarisatie en activa beheer](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: automatische Asset-detectie oplossing gebruiken

**Richt lijnen**: niet van toepassing, er zijn geen data Box activa om te detecteren.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="62-maintain-asset-metadata"></a>6,2: meta gegevens van activa onderhouden

**Richt lijnen**: niet van toepassing, er zijn geen meta gegevens van de asset voor data box.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: niet-geautoriseerde Azure-resources verwijderen

**Richt lijnen**: niet van toepassing, data Box service zorgt ervoor dat er geen niet-geautoriseerde Azure-bronnen worden gebruikt.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: micro soft

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6,4: een inventaris van goedgekeurde Azure-resources definiëren en onderhouden

**Richt lijnen**: niet van toepassing; Er zijn geen Data Box Service niveau.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: monitor voor niet-goedgekeurde Azure-resources

**Richt lijnen**: niet van toepassing, er zijn geen data Box Service niveau.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: monitor voor niet-goedgekeurde software toepassingen binnen reken resources

**Richt lijnen**: niet van toepassing, er zijn geen data Box Service niveau.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: niet-goedgekeurde Azure-resources en software toepassingen verwijderen

**Richt lijnen**: niet van toepassing, er zijn geen data Box Service niveau.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="68-use-only-approved-applications"></a>6,8: alleen goedgekeurde toepassingen gebruiken

**Richt lijnen**: niet van toepassing, er zijn geen data Box Service niveau.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="69-use-only-approved-azure-services"></a>6,9: alleen goedgekeurde Azure-Services gebruiken

**Richt lijnen**: niet van toepassing, data Box Services gebruikt alleen goedgekeurde Azure-Services.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: een inventaris van goedgekeurde software titels onderhouden

**Richt lijnen**: niet van toepassing; Data Box Services maakt alleen gebruik van goedgekeurde software titels.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: de mogelijkheid van gebruikers om te communiceren met Azure Resource Manager beperken

**Hulp** bij het configureren van voorwaardelijke toegang van Azure om gebruikers de mogelijkheid te bieden om te communiceren met Azure Resource Manager door ' blok toegang ' te configureren voor de app Microsoft Azure management.

* [Voorwaardelijke toegang configureren om de toegang tot Azure Resource Manager te blok keren](../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: de mogelijkheid van gebruikers om scripts uit te voeren binnen reken bronnen beperken

**Richt lijnen**: niet van toepassing; Data Box-Service biedt geen ondersteuning voor het uitvoeren van scripts.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: toepassingen met een hoog risico fysiek of logisch scheiden

**Richt lijnen**: niet van toepassing; Data Box-Service heeft geen webtoepassingen die worden uitgevoerd op Azure-app service.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

## <a name="secure-configuration"></a>Veilige configuratie

*Zie [beveiligings beheer: beveiligde configuratie](../security/benchmarks/security-control-secure-configuration.md)voor meer informatie.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: veilige configuraties instellen voor alle Azure-resources

**Hulp**: Azure data box worden geleverd met vooraf geconfigureerde beveiligings instellingen voor best practices.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: micro soft

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: veilige configuraties van besturings systemen instellen

**Hulp**: Azure data box worden geleverd met vooraf geconfigureerde beveiligings instellingen voor best practices.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: micro soft

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: Beveilig Azure-resource configuraties onderhouden

**Hulp**: Azure data box worden geleverd met vooraf geconfigureerde beveiligings instellingen voor best practices voor bronnen en kunnen niet worden gewijzigd.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: micro soft

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: veilige configuraties van besturings systemen onderhouden

**Hulp**: Azure data box worden geleverd met vooraf geconfigureerde beveiligings instellingen voor best practices voor bronnen en kunnen niet worden gewijzigd.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: de configuratie van Azure-resources veilig opslaan

**Hulp**: alle data Box configuraties worden veilig opgeslagen.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: micro soft

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: aangepaste installatie kopieën van een besturings systeem veilig opslaan

**Hulp**: alle data box installatie kopieën van besturings systemen worden veilig opgeslagen.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: micro soft

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: hulpprogram ma's voor configuratie beheer voor Azure-resources implementeren

**Richt lijnen**: niet van toepassing, Azure data Box configuraties kunnen niet worden gewijzigd.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: hulpprogram ma's voor configuratie beheer voor besturings systemen implementeren

**Richt lijnen**: niet van toepassing, Azure data Box configuraties kunnen niet worden gewijzigd.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: geautomatiseerde configuratie bewaking voor Azure-resources implementeren

**Richt lijnen**: niet van toepassing, Azure data Box configuraties kunnen niet worden gewijzigd.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: geautomatiseerde configuratie bewaking voor besturings systemen implementeren

**Richt lijnen**: niet van toepassing, Azure data Box configuraties kunnen niet worden gewijzigd.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="711-manage-azure-secrets-securely"></a>7,11: Azure-geheimen veilig beheren

**Hulp**: door de klant beheerde sleutels moeten worden gemaakt en opgeslagen in een Azure Key Vault.

* [Door de klant beheerde sleutels gebruiken in Azure Key Vault voor Azure Data Box](./data-box-customer-managed-encryption-key-portal.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: gedeeld

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: identiteiten veilig en automatisch beheren

**Richt lijnen**: niet van toepassing; Azure Data Box maakt geen gebruik van beheerde identiteiten.

* [Azure-Services die beheerde identiteiten ondersteunen](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: onbedoelde referentie blootstelling elimineren

**Hulp**: micro soft voert referentie scanner uit op Data Box code. Daarnaast beveiligt micro soft ook veilig referenties. Referentie scanner implementeren om referenties in code te identificeren. Referentie scanner stimuleert ook het verplaatsen van gedetecteerde referenties naar veiliger locaties, zoals Azure Key Vault.

* [Referentie scanner instellen](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure Security Center bewaking**: n.v.t.

**Verantwoordelijkheid**: gedeeld

## <a name="malware-defense"></a>Beveiliging tegen malware

*Zie [beveiligings beheer: verdediging tegen malware](../security/benchmarks/security-control-malware-defense.md)voor meer informatie.*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: centraal beheerde anti-malware-software gebruiken

**Richt lijnen**: niet van toepassing; deze richt lijn is bedoeld voor reken resources. Micro soft anti-malware is ingeschakeld op de onderliggende host die ondersteuning biedt voor Azure-Services (bijvoorbeeld Azure App Service), maar wordt niet uitgevoerd op de inhoud van de klant.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: scan bestanden die moeten worden geüpload naar niet-reken resources van Azure

**Hulp**: micro soft anti-malware is ingeschakeld op de onderliggende host die ondersteuning biedt voor Azure-Services (bijvoorbeeld Azure klanten-lockbox), maar wordt niet uitgevoerd op de inhoud van de klant.

Het is uw verantwoordelijkheid om vooraf te scannen op inhoud die wordt geüpload naar niet-reken resources van Azure. Micro soft heeft geen toegang tot klant gegevens en kan daarom geen anti-malware scans uitvoeren van klant inhoud namens u.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: controleren of anti-malware-software en hand tekeningen zijn bijgewerkt

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources. Micro soft antimalware is ingeschakeld op de onderliggende host die ondersteuning biedt voor Azure-Services, maar wordt niet uitgevoerd op de inhoud van de klant.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

## <a name="data-recovery"></a>Gegevensherstel

*Zie [beveiligings beheer: gegevens herstel](../security/benchmarks/security-control-data-recovery.md)voor meer informatie.*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: controleren op regel matige automatische back-ups

**Richt lijnen**: niet van toepassing, data Box Service vereist geen back-ups.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: volledige back-ups van het systeem uitvoeren en een back-up maken van door de klant beheerde sleutels

**Richt lijnen**: Zorg ervoor dat u een back-up maakt van alle gegevens en de door de klant beheerde sleutel. Data Box maakt geen back-ups.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: alle back-ups valideren, inclusief door de klant beheerde sleutels

**Hulp**: Controleer of al uw gegevens zich in azure Storage account bevindt voordat u deze uit uw locatie verwijdert.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: zorg voor de bescherming van back-ups en door de klant beheerde sleutels

**Richt lijnen**: Zorg ervoor dat alle back-ups of door de klant beheerde sleutels die u hebt beveiligd, voldoen aan de aanbevolen procedures.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

## <a name="incident-response"></a>Reageren op incidenten

*Zie voor meer informatie [beveiligings beheer: reactie op incidenten](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: een hand leiding voor reactie op incidenten maken

**Hulp**: een antwoord gids voor incidenten maken voor uw organisatie. Zorg ervoor dat er schriftelijke incidenten abonnementen zijn die alle werk rollen definiëren, evenals fasen van incident handling/management van detectie tot een beoordeling van het incident.

* [Richt lijnen voor het bouwen van uw eigen beveiligings incident antwoord proces](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Micro soft Security Response Center anatomie van een incident](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

* [De verwerkings handleiding voor het computer beveiligings incident van het NIST gebruiken om u te helpen bij het maken van uw eigen reactie plan voor incidenten](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: een beoordelings procedure voor incidenten en prioriteits procedures maken

**Hulp**: Security Center wijst aan elke waarschuwing een Ernst toe om u te helpen bepalen welke waarschuwingen het eerst moeten worden onderzocht. De ernst is gebaseerd op de manier waarop vertrouwen Security Center is in de zoek actie of het analyse programma dat wordt gebruikt om de waarschuwing te geven, evenals het betrouwbaarheids niveau dat er schadelijke bedoelingen zijn achter de activiteit die tot de waarschuwing heeft geleid.

Daarnaast kunt u ook duidelijk abonnementen markeren (voor bijvoorbeeld productie, niet-productie) met behulp van tags en maak een naamgevings systeem om Azure-resources duidelijk te identificeren en te categoriseren, met name voor de verwerking van gevoelige gegevens. Het is uw verantwoordelijkheid om prioriteit te geven aan het herstel van waarschuwingen op basis van de ernst van de Azure-resources en-omgeving waar het incident heeft plaatsgevonden.

* [Beveiligingswaarschuwingen in Azure Security Center](../security-center/security-center-alerts-overview.md)

* [Tags gebruiken om Azure-resources te organiseren](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="103-test-security-response-procedures"></a>10,3: procedures voor beveiligings antwoorden testen

**Richt lijnen**: oefent oefeningen uit om de respons mogelijkheden van uw systeem te testen op een reguliere uitgebracht om uw Azure-resources te beschermen. Identificeer zwakke punten en tussen ruimten en wijzig zo nodig het schema.

* [Publicatie van het NIST-hand leiding voor test-, trainings-en oefen Programma's voor IT-plannen en-mogelijkheden](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: contact gegevens van het beveiligings incident opgeven en waarschuwings meldingen configureren voor beveiligings incidenten

**Hulp**: contact gegevens van beveiligings incidenten worden door micro soft gebruikt om contact met u op te nemen als het micro soft Security Response Center (MSRC) detecteert dat uw gegevens zijn geopend door een onrecht matige of niet-gemachtigde partij. Bekijk incidenten na het feit om te controleren of de problemen zijn opgelost.

* [De Azure Security Center Security-contact persoon instellen](../security-center/security-center-provide-security-contact-details.md)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: beveiligings waarschuwingen opnemen in uw reactie systeem van uw incident

**Hulp**: exporteer uw Azure Security Center waarschuwingen en aanbevelingen met behulp van de functie continue export om Risico's voor Azure-resources te identificeren. Met doorlopend exporteren kunt u waarschuwingen en aanbevelingen hand matig of op een doorlopende manier exporteren. U kunt de Azure Security Center Data Connector gebruiken om de waarschuwingen naar Azure Sentinel te streamen.

* [Continue export configureren](../security-center/continuous-export.md)

* [Waarschuwingen streamen naar Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: de reactie op beveiligings waarschuwingen automatiseren

**Hulp**: gebruik de functie werk stroom automatisering in azure Security Center om automatisch reacties te activeren via ' Logic apps ' in beveiligings waarschuwingen en aanbevelingen voor het beveiligen van uw Azure-resources.

* [Werk stroom automatisering en Logic Apps configureren](../security-center/workflow-automation.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

## <a name="penetration-tests-and-red-team-exercises"></a>Penetratietests en Red Team-oefeningen

*Zie voor meer informatie [Security Control: Indringings tests en Red team-oefeningen](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: voert regel matig indringings tests van uw Azure-resources uit en zorgt voor herbemiddeling van alle essentiële beveiligings resultaten

**Richt lijnen**: micro soft voert het testen van indringing en het scannen van beveiligings problemen op Data Box-apparaten uit. U kunt uw eigen indringings tests en beveiligings scans uitvoeren. Als u ervoor kiest om dit te doen, volgt u de micro soft-regels om te controleren of uw indringings tests niet worden geschonden door het micro soft-beleid. Gebruik de strategie van micro soft en de uitvoering van de implementatie van de indringing van een live site in de Cloud, services en toepassingen die door micro soft worden beheerd.

* [Regels van betrokkenheid voor het testen van indringing](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

* [Microsoft Cloud rode teams](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: gedeeld

## <a name="next-steps"></a>Volgende stappen

- Zie de [Azure Security-Bench Mark](../security/benchmarks/overview.md)
- Meer informatie over [Azure-beveiligings basislijnen](../security/benchmarks/security-baselines-overview.md)