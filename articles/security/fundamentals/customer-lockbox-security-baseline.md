---
title: Azure-beveiligings basislijn voor Klanten-lockbox voor Microsoft Azure
description: Azure-beveiligings basislijn voor Klanten-lockbox voor Microsoft Azure
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 06/05/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: c2595d75acfacf3d501b6efb83b383d9a56215a6
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98203283"
---
# <a name="azure-security-baseline-for-customer-lockbox-for-microsoft-azure"></a>Azure-beveiligings basislijn voor Klanten-lockbox voor Microsoft Azure

De Azure-beveiligings basislijn voor Klanten-lockbox voor Microsoft Azure bevat aanbevelingen waarmee u de beveiligings postuur van uw implementatie kunt verbeteren.

De basis lijn voor deze service wordt opgehaald uit de [Azure Security Bench Mark-versie 1,0](../benchmarks/overview.md), die aanbevelingen biedt over hoe u uw cloud oplossingen kunt beveiligen in azure met onze richt lijnen voor best practices.

Zie het [overzicht van Azure Security-basis lijnen](../benchmarks/security-baselines-overview.md)voor meer informatie.

## <a name="network-security"></a>Netwerkbeveiliging

*Zie [beveiligings beheer: netwerk beveiliging](../benchmarks/security-control-network-security.md)voor meer informatie.*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1,1: Beveilig bronnen met behulp van netwerk beveiligings groepen of Azure Firewall op de Virtual Network

**Richt lijnen**: niet van toepassing; u kunt een virtueel netwerk, subnet of netwerk beveiligings groep niet koppelen aan Klanten-lockbox.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1,2: de configuratie en het verkeer van Vnets, subnetten en Nic's bewaken en vastleggen

**Richt lijnen**: niet van toepassing; u kunt een virtueel netwerk, subnet of netwerk beveiligings groep niet koppelen aan Klanten-lockbox.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="13-protect-critical-web-applications"></a>1,3: essentiële webtoepassingen beveiligen

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor webtoepassingen die worden uitgevoerd op Azure App Service of reken bronnen.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: communicatie met bekende schadelijke IP-adressen weigeren

**Richt lijnen**: niet van toepassing; u kunt een virtueel netwerk, subnet of netwerk beveiligings groep niet koppelen aan Klanten-lockbox.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="15-record-network-packets-and-flow-logs"></a>1,5: netwerk pakketten en stroom logboeken vastleggen

**Richt lijnen**: niet van toepassing; u kunt een virtueel netwerk, subnet of netwerk beveiligings groep niet koppelen aan Klanten-lockbox.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: op netwerk gebaseerde inbreuk detectie/indringings systemen (ID'S/IP-adressen) implementeren

**Richt lijnen**: niet van toepassing; u kunt een virtueel netwerk, subnet of netwerk beveiligings groep niet koppelen aan Klanten-lockbox.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="17-manage-traffic-to-web-applications"></a>1,7: verkeer naar webtoepassingen beheren

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor webtoepassingen die worden uitgevoerd op Azure App Service of reken bronnen.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: de complexiteit en administratieve overhead van netwerk beveiligings regels minimaliseren

**Richt lijnen**: niet van toepassing; u kunt een virtueel netwerk, subnet of netwerk beveiligings groep niet koppelen aan Klanten-lockbox.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: standaard beveiligings configuraties voor netwerk apparaten onderhouden

**Richt lijnen**: niet van toepassing; u kunt een virtueel netwerk, subnet of netwerk beveiligings groep niet koppelen aan Azure Klanten-lockbox.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="110-document-traffic-configuration-rules"></a>1,10: configuratie regels voor het document verkeer

**Richt lijnen**: niet van toepassing; u kunt een virtueel netwerk, subnet of netwerk beveiligings groep niet koppelen aan Klanten-lockbox.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: gebruik automatische hulpprogram ma's om netwerk bron configuraties te bewaken en wijzigingen te detecteren

**Richt lijnen**: niet van toepassing; u kunt een virtueel netwerk, subnet of netwerk beveiligings groep niet koppelen aan Klanten-lockbox. Er zijn geen netwerk configuraties die kunnen worden gemaakt of gecontroleerd.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

## <a name="logging-and-monitoring"></a>Logboekregistratie en bewaking

*Zie voor meer informatie [beveiligings beheer: logboek registratie en controle](../benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: goedgekeurde tijd synchronisatie bronnen gebruiken

**Richt lijnen**: niet van toepassing; Micro soft onderhoudt de tijd bronnen die worden gebruikt voor resources zoals Klanten-lockbox.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="22-configure-central-security-log-management"></a>2,2: Centraal beveiligings logboek beheer configureren

**Hulp**: audit logboeken voor klanten-lockbox worden automatisch ingeschakeld en bijgehouden in activiteiten logboeken van Azure. U kunt deze gegevens weer geven door deze vanuit het Azure-activiteiten logboek te streamen naar een log analytic-werk ruimte waar u vervolgens onderzoek en analyses kunt uitvoeren.

De activiteiten logboeken die zijn gegenereerd door Klanten-lockbox naar Azure Sentinel of een andere SIEM voor het inschakelen van centrale logboek aggregatie en-beheer.

* [Controle logboeken voor Klanten-lockbox](./customer-lockbox-overview.md#auditing-logs)

* [Azure-Sentinel onboarden](../../sentinel/quickstart-onboard.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: controle logboek registratie inschakelen voor Azure-resources

**Hulp**: audit logboeken voor klanten-lockbox worden automatisch ingeschakeld en bijgehouden in activiteiten logboeken van Azure. U kunt deze gegevens weer geven door deze vanuit het Azure-activiteiten logboek te streamen naar een log analytic-werk ruimte waar u vervolgens onderzoek en analyses kunt uitvoeren.

* [Controle logboeken voor Klanten-lockbox](./customer-lockbox-overview.md#auditing-logs)

* [Azure-Sentinel onboarden](../../sentinel/quickstart-onboard.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: beveiligings logboeken verzamelen van besturings systemen

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="25-configure-security-log-storage-retention"></a>2,5: Bewaar beveiliging van het beveiligings logboek configureren

**Richt lijnen**: stel in azure monitor de Bewaar periode voor logboek registratie In voor log Analytics werk ruimten die zijn gekoppeld aan uw klanten-lockbox volgens de nalevings voorschriften van uw organisatie.

* [Para meters voor het bewaren van Logboeken instellen](../../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="26-monitor-and-review-logs"></a>2,6: Logboeken bewaken en controleren

**Hulp**: audit logboeken voor klanten-lockbox worden automatisch ingeschakeld en bijgehouden in activiteiten logboeken van Azure. U kunt deze gegevens weer geven door deze vanuit het Azure-activiteiten logboek te streamen naar een log analytic-werk ruimte waar u vervolgens onderzoek en analyses kunt uitvoeren. Analyseer en bewaak logboeken van uw Klanten-lockbox aanvragen voor afwijkend gedrag. Gebruik de sectie ' logs ' in de Azure Sentinel-werk ruimte om query's uit te voeren of waarschuwingen te maken op basis van uw Klanten-lockbox-Logboeken.

* [Audit Logboeken in Klanten-lockbox](./customer-lockbox-overview.md#auditing-logs)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="27-enable-alerts-for-anomalous-activity"></a>2,7: waarschuwingen inschakelen voor afwijkende activiteit

**Hulp**: audit logboeken voor klanten-lockbox worden automatisch ingeschakeld en bijgehouden in activiteiten logboeken van Azure. U kunt deze gegevens weer geven door deze vanuit het Azure-activiteiten logboek te streamen naar een log analytic-werk ruimte waar u vervolgens onderzoek en analyses kunt uitvoeren. Analyseer en bewaak logboeken van uw Klanten-lockbox aanvragen voor afwijkend gedrag. Gebruik de sectie ' logs ' in de Azure Sentinel-werk ruimte om query's uit te voeren of waarschuwingen te maken op basis van uw Klanten-lockbox-Logboeken.

* [Audit Logboeken in Klanten-lockbox](./customer-lockbox-overview.md#auditing-logs)

* [Een waarschuwing over logboek gegevens van log Analytics](../../azure-monitor/learn/tutorial-response.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="28-centralize-anti-malware-logging"></a>2,8: registratie van anti-malware centraliseren

**Richt lijnen**: niet van toepassing; Klanten-lockbox geen aan anti-malware gerelateerde logboeken verwerkt of produceert.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="29-enable-dns-query-logging"></a>2,9: DNS-query logboek registratie inschakelen

**Richt lijnen**: niet van toepassing; Klanten-lockbox worden geen DNS-gerelateerde logboeken verwerkt of geproduceerd.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="210-enable-command-line-audit-logging"></a>2,10: controle logboek registratie op opdracht regel inschakelen

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

## <a name="identity-and-access-control"></a>Identiteits- en toegangsbeheer

*Zie [beveiligings beheer: identiteits-en toegangs beheer](../benchmarks/security-control-identity-access-control.md)voor meer informatie.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: een inventaris van beheerders accounts onderhouden

**Richt lijnen**: een inventaris van de gebruikers accounts met beheerders toegang tot uw klanten-lockbox aanvragen onderhouden. U kunt het deel venster identiteits-en toegangs beheer (IAM) in de Azure Portal voor uw abonnement gebruiken om toegangs beheer op basis van rollen (Azure RBAC) te configureren. De rollen worden toegepast op gebruikers, groepen, service-principals en beheerde identiteiten in Azure Active Directory.

De gebruiker die de rol van de eigenaar van het Azure-abonnement heeft, ontvangt een e-mail van micro soft, om hen op de hoogte te stellen van alle openstaande toegangs aanvragen bij de organisatie van de klant. Voor Klanten-lockbox aanvragen is deze persoon de aangewezen fiatteur.

* [Informatie over aangepaste rollen](../../role-based-access-control/custom-roles.md)

* [Azure RBAC voor werkmappen configureren](../../sentinel/quickstart-get-visibility.md)

* [Machtigingen voor toegangs aanvragen in Klanten-lockbox](./customer-lockbox-overview.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="32-change-default-passwords-where-applicable"></a>3,2: standaard wachtwoorden wijzigen indien van toepassing

**Hulp**: Azure Active Directory heeft niet het concept van standaard wachtwoorden. Andere Azure-resources die een wacht woord vereisen, moeten een wacht woord afdwingen voor het maken van complexiteits vereisten en een minimale wachtwoord lengte, die afhankelijk is van de service. U bent verantwoordelijk voor toepassingen van derden en Marketplace-services die standaard wachtwoorden kunnen gebruiken.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: speciale beheerders accounts gebruiken

**Richt lijnen**: Maak standaard procedures voor het gebruik van specifieke beheerders accounts. Gebruik Azure Security Center identiteits-en toegangs beheer om het aantal beheerders accounts te bewaken.

Daarnaast kunt u aanbevelingen van Azure Security Center of ingebouwde Azure-beleids regels gebruiken om u te helpen bij het bijhouden van specifieke beheerders accounts, zoals:
- Er moet meer dan één eigenaar zijn toegewezen aan uw abonnement
- Afgeschafte accounts met eigenaarsmachtigingen moeten worden verwijderd uit uw abonnement
- Externe accounts met eigenaarsmachtigingen moeten worden verwijderd uit uw abonnement

* [Azure Security Center gebruiken om identiteit en toegang te bewaken (preview)](../../security-center/security-center-identity-access.md)

* [Azure Policy gebruiken](../../governance/policy/tutorials/create-and-manage.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: eenmalige aanmelding (SSO) met Azure Active Directory gebruiken

**Richt lijnen**: niet van toepassing; toegang tot Klanten-lockbox via de Azure Portal en is gereserveerd voor accounts met de Tenant rol van eigenaar. Eenmalige aanmelding wordt niet ondersteund.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: multi-factor Authentication gebruiken voor alle op Azure Active Directory gebaseerde toegang

**Hulp**: schakel Azure Active Directory multi-factor Authentication in en volg de aanbevelingen voor Azure Security Center identiteits-en toegangs beheer.

* [MFA inschakelen in Azure](../../active-directory/authentication/howto-mfa-getstarted.md)

* [Identiteit en toegang bewaken in Azure Security Center](../../security-center/security-center-identity-access.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: gebruik speciale machines (privileged Access workstations) voor alle beheer taken

**Hulp**: gebruik een privileged Access-werk station (Paw) met Azure AD multi-factor Authentication (MFA) ingeschakeld om u aan te melden en uw klanten-lockbox-aanvragen te configureren.

* [Privileged Access Workstations](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

* [Planning van een cloudimplementatie van Azure AD Multi-Factor Authentication](../../active-directory/authentication/howto-mfa-getstarted.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3,7: logboek en waarschuwing voor verdachte activiteiten van beheerders accounts

**Hulp**: gebruik Azure Active Directory PRIVILEGED Identity Management (PIM) voor het genereren van Logboeken en waarschuwingen wanneer verdachte of onveilige activiteiten in de omgeving worden uitgevoerd.

Daarnaast kunt u Azure Active Directory risico detecties gebruiken om waarschuwingen en rapporten weer te geven over Risk ante gebruikers gedrag.

* [Privileged Identity Management implementeren (PIM)](../../active-directory/privileged-identity-management/pim-deployment-plan.md)

* [Meer informatie over Azure AD-risico detectie](../../active-directory/identity-protection/overview-identity-protection.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: Azure-resources alleen beheren vanaf goedgekeurde locaties

**Hulp**: gebruik benoemde locaties voor voorwaardelijke toegang om alleen toegang toe te staan tot de Azure Portal vanuit specifieke logische groepen met IP-adresbereiken of landen/regio's.

* [Benoemde locaties configureren in azure](../../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="39-use-azure-active-directory"></a>3,9: Azure Active Directory gebruiken

**Hulp**: gebruik Azure Active Directory als centraal verificatie-en autorisatie systeem, indien van toepassing. Azure Active Directory beschermt gegevens door gebruik te maken van sterke code ring voor gegevens in rust en onderweg. Azure Active Directory ook zouten, hashes en veilig opslaan van gebruikers referenties.

* [Een Azure Active Directory-exemplaar maken en configureren](../../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: regel matig gebruikers toegang controleren en afstemmen

**Hulp**: Azure Active Directory biedt logboeken waarmee u verouderde accounts kunt detecteren. Daarnaast kunt u Azure Active Directory toegangs beoordelingen gebruiken om groepslid maatschappen, toegang tot bedrijfs toepassingen en roltoewijzingen op efficiënte wijze te beheren. Gebruikers toegang kan regel matig worden gecontroleerd om ervoor te zorgen dat alleen de juiste gebruikers toegang hebben.

* [Azure Active Directory rapportage begrijpen](../../active-directory/reports-monitoring/index.yml)

* [Azure Active Directory toegangs beoordelingen gebruiken](../../active-directory/governance/access-reviews-overview.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3,11: controle pogingen om toegang te krijgen tot gedeactiveerde accounts

**Hulp**: gebruik Azure Active Directory als centraal verificatie-en autorisatie systeem, indien van toepassing. Azure Active Directory beschermt gegevens door gebruik te maken van sterke code ring voor gegevens in rust en onderweg. Azure Active Directory ook zouten, hashes en veilig opslaan van gebruikers referenties.

U hebt toegang tot Azure Active Directory aanmeldings activiteiten, controle-en risico gebeurtenis logboek bronnen, waarmee u kunt integreren met Azure Sentinel of een SIEM van derden.

U kunt dit proces stroom lijnen door Diagnostische instellingen voor Azure Active Directory gebruikers accounts te maken en de audit logboeken en aanmeldings logboeken te verzenden naar een Log Analytics-werk ruimte. U kunt de gewenste logboek waarschuwingen configureren in Log Analytics.

* [Azure-activiteiten logboeken integreren in Azure Monitor](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

* [Azure-Sentinel aan de trein](../../sentinel/quickstart-onboard.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: waarschuwing voor de afwijking van het aanmeldings gedrag van accounts

**Richt lijnen**: voor de afwijking van het aanmeldings gedrag van accounts op het besturings vlak (bijvoorbeeld Azure Portal), gebruikt u Azure Active Directory functies voor identiteits beveiliging en risico detectie om automatische antwoorden te configureren op gedetecteerde verdachte acties die betrekking hebben op gebruikers identiteiten. U kunt ook gegevens opnemen in azure Sentinel voor verder onderzoek.

* [Azure Active Directory Risk ante aanmelding weer geven](../../active-directory/identity-protection/overview-identity-protection.md)

* [Risico beleid voor identiteits beveiliging configureren en inschakelen](../../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

* [Azure-Sentinel onboarden](../../sentinel/quickstart-onboard.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: micro soft biedt toegang tot relevante klant gegevens tijdens ondersteunings scenario's

**Richt lijnen**: deze aanbeveling is niet van toepassing op klanten-lockbox.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

## <a name="data-protection"></a>Gegevensbeveiliging

*Zie [beveiligings beheer: gegevens beveiliging](../benchmarks/security-control-data-protection.md)voor meer informatie.*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: een inventaris van gevoelige informatie onderhouden

**Richt lijnen**: deze aanbeveling is niet van toepassing. Tags worden niet ondersteund voor Klanten-lockbox.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: systemen isoleren die gevoelige informatie opslaan of verwerken

**Richt lijnen**: niet van toepassing; Klanten-lockbox wordt ingericht in hetzelfde abonnement als de resources waartoe u toegang verleent. Er is geen openbaar eind punt om te beveiligen of isoleren. Klanten-lockbox toegang aanvragen wordt verleend aan de gebruiker die de rol van eigenaar op Tenant niveau heeft.

* [Klanten-lockbox werk stroom begrijpen](./customer-lockbox-overview.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: niet-geautoriseerde overdracht van gevoelige gegevens controleren en blok keren

**Hulp**: micro soft beheert de onderliggende infra structuur voor klanten-lockbox en heeft strikte controles geïmplementeerd om verlies of bloot stelling van klant gegevens te voor komen.

* [Informatie over beveiliging van klantgegevens in Azure](./protection-customer-data.md)

**Monitoring door Azure Security Center**: Niet van toepassing

**Verantwoordelijkheid**: micro soft

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: alle gevoelige gegevens in de overdracht versleutelen

**Richt lijnen**: micro soft gebruikt standaard het Transport Layer Security (TLS)-protocol om gegevens te beveiligen wanneer het onderweg is tussen de Cloud Services en klanten. Micro soft-data centers onderhandelen over een TLS-verbinding met client systemen die verbinding maken met Azure-Services. TLS biedt sterke verificatie, privacy van berichten en integriteit (inschakelen van detectie van bericht manipulatie, onderscheping en vervalsing), interoperabiliteit, algoritme flexibiliteit en gemakkelijke implementatie en gebruik.

* [Meer informatie over versleuteling in transit met Azure](./encryption-overview.md#encryption-of-data-in-transit)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: micro soft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: een actief detectie hulpprogramma gebruiken om gevoelige gegevens te identificeren

**Richt lijnen**: niet van toepassing; Klanten-lockbox zichzelf geen klant gegevens bevat.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: Azure RBAC gebruiken om de toegang tot resources te beheren

**Hulp**: klanten-lockbox aanvraag goed keuring wordt verleend aan de gebruiker die de rol van eigenaar op Tenant niveau heeft.

* [Klanten-lockbox werk stroom begrijpen](./customer-lockbox-overview.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: voor komen dat gegevens verlies op basis van host wordt gebruikt voor het afdwingen van toegangs beheer

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources. Micro soft beheert de onderliggende infra structuur voor Klanten-lockbox en heeft strikte controles geïmplementeerd om verlies of bloot stelling van klant gegevens te voor komen.

* [Azure-klant gegevens beveiliging](./protection-customer-data.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: gevoelige informatie op rest versleutelen

**Richt lijnen**: niet van toepassing; Klanten-lockbox zelf niet de klant gegevens bevatten.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: wijzigingen in essentiële Azure-resources vastleggen en waarschuwen

**Hulp**: audit logboeken voor klanten-lockbox worden automatisch ingeschakeld en bijgehouden in activiteiten logboeken van Azure. Gebruik het Azure-activiteiten logboek om wijzigingen in azure Klanten-lockbox-resources te controleren en te detecteren. Maak waarschuwingen in Azure Monitor die worden geactiveerd wanneer er wijzigingen in kritieke resources plaatsvinden.

* [Controle inschakelen in Klanten-lockbox](./customer-lockbox-overview.md)

* [Activiteiten logboek gebeurtenissen van Azure weer geven en ophalen](../../azure-monitor/platform/activity-log.md#view-the-activity-log)

* [Waarschuwingen maken in Azure Monitor](../../azure-monitor/platform/alerts-activity-log.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

## <a name="vulnerability-management"></a>Beheer van beveiligingsproblemen

*Zie [beveiligings beheer: beveiligingslek beheer](../benchmarks/security-control-vulnerability-management.md)voor meer informatie.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: automatische hulpprogram ma's voor het scannen van beveiligings problemen uitvoeren

**Richt lijnen**: niet van toepassing; Micro soft voert beveiligings beheer uit op de onderliggende systemen die ondersteuning bieden voor Klanten-lockbox.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: micro soft

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: geautomatiseerde oplossing voor patch beheer voor besturings systemen implementeren

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5,3: Implementeer een geautomatiseerde oplossing voor software patch beheer van derden

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: vergelijken van back-to-back-problemen

**Richt lijnen**: niet van toepassing; Micro soft voert beveiligings beheer uit op de onderliggende systemen die ondersteuning bieden voor Klanten-lockbox.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: een risico classificatie proces gebruiken om prioriteit te geven aan het herstel van ontdekte beveiligings problemen

**Richt lijnen**: niet van toepassing; Micro soft voert beveiligings beheer uit op de onderliggende systemen die ondersteuning bieden voor Klanten-lockbox.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

## <a name="inventory-and-asset-management"></a>Inventarisatie en asset-management

*Zie voor meer informatie [beveiligings beheer: inventarisatie en activa beheer](../benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-azure-asset-discovery"></a>6,1: Azure Asset Discovery gebruiken

**Hulp**: Azure resource Graph gebruiken voor het opvragen/detecteren van alle resources (zoals compute, opslag, netwerk, poorten en protocollen enz.) binnen uw abonnement (en). Zorg ervoor dat de juiste (Lees-) machtigingen voor uw Tenant en alle Azure-abonnementen en resources in uw abonnementen inventariseren.

Hoewel klassieke Azure-resources kunnen worden gedetecteerd via Azure resource Graph, is het raadzaam om Azure Resource Manager-resources te maken en te gebruiken.

* [Query's maken met Azure resource Graph](../../governance/resource-graph/first-query-portal.md)

* [Uw Azure-abonnementen weer geven](/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

* [Meer informatie over toegangs beheer op basis van rollen in azure (Azure RBAC)](../../role-based-access-control/overview.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="62-maintain-asset-metadata"></a>6,2: meta gegevens van activa onderhouden

**Hulp**: Labels worden niet ondersteund voor klanten-lockbox.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: niet-geautoriseerde Azure-resources verwijderen

**Richt lijnen**: Gebruik labels, beheer groepen en afzonderlijke abonnementen, waar nodig, om Azure-resources te organiseren en bij te houden. Sluit de inventaris regel matig af en zorg ervoor dat niet-geautoriseerde resources tijdig worden verwijderd uit het abonnement.

Gebruik Azure Policy bovendien om beperkingen te leggen voor het type resources dat kan worden gemaakt in klant abonnementen met behulp van de volgende ingebouwde beleids definities:
- Niet toegestane resourcetypen
- Toegestane brontypen

* [Aanvullende Azure-abonnementen maken](../../cost-management-billing/manage/create-subscription.md)

* [Beheergroepen maken](../../governance/management-groups/create-management-group-portal.md)

* [Tags maken en gebruiken](../../azure-resource-manager/management/tag-resources.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6,4: een inventaris van goedgekeurde Azure-resources en software titels onderhouden

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: monitor voor niet-goedgekeurde Azure-resources

**Hulp**: gebruik Azure Policy om beperkingen toe te voegen voor het type resources dat kan worden gemaakt in uw abonnement (en).

Gebruik Azure resource Graph voor het opvragen/detecteren van resources binnen hun abonnement (en). Zorg ervoor dat alle Azure-resources die aanwezig zijn in de omgeving, zijn goedgekeurd.

* [Azure Policy configureren en beheren](../../governance/policy/tutorials/create-and-manage.md)

* [Query's maken met Azure resource Graph](../../governance/resource-graph/first-query-portal.md)

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

**Hulp: gebruik** Azure Policy om beperkingen toe te voegen voor het type resources dat kan worden gemaakt in uw abonnement (en) met de volgende ingebouwde beleids definities:
- Niet toegestane resourcetypen
- Toegestane brontypen

* [Azure Policy configureren en beheren](../../governance/policy/tutorials/create-and-manage.md)

* [Een specifiek resource type weigeren met Azure Policy](../../governance/policy/samples/index.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="610-implement-approved-application-list"></a>6,10: lijst met goedgekeurde toepassingen implementeren

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="611-limit-users-ability-to-interact-with-azureresources-manager-via-scripts"></a>6,11: de mogelijkheid van gebruikers om te communiceren met kunt Manager via scripts beperken

**Hulp** bij het configureren van voorwaardelijke toegang van Azure om gebruikers de mogelijkheid te bieden om te communiceren met Azure Resource Manager door ' blok toegang ' te configureren voor de app Microsoft Azure management.

* [Voorwaardelijke toegang configureren om de toegang tot Azure Resource Manager te blok keren](../../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: de mogelijkheid van gebruikers om scripts uit te voeren binnen reken bronnen beperken

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: toepassingen met een hoog risico fysiek of logisch scheiden

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor webtoepassingen die worden uitgevoerd op Azure App Service of reken bronnen.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

## <a name="secure-configuration"></a>Veilige configuratie

*Zie [beveiligings beheer: beveiligde configuratie](../benchmarks/security-control-secure-configuration.md)voor meer informatie.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: veilige configuraties instellen voor alle Azure-resources

**Richt lijnen**: niet van toepassing, klanten-lockbox geen Configureer bare beveiligings instellingen heeft.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: veilige configuraties van besturings systemen instellen

**Richt lijnen**: niet van toepassing; deze richt lijn is bedoeld voor reken resources.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: Beveilig Azure-resource configuraties onderhouden

**Richt lijnen**: niet van toepassing, klanten-lockbox geen Configureer bare beveiligings instellingen heeft.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: veilige configuraties van besturings systemen onderhouden

**Richt lijnen**: niet van toepassing; deze richt lijn is bedoeld voor reken resources.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: de configuratie van Azure-resources veilig opslaan

**Richt lijnen**: niet van toepassing; Klanten-lockbox heeft geen Configureer bare beveiligings instellingen.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: aangepaste installatie kopieën van een besturings systeem veilig opslaan

**Richt lijnen**: niet van toepassing; deze richt lijn is bedoeld voor reken resources.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="77-deploy-system-configuration-management-tools"></a>7,7: hulpprogram ma's voor het beheer van systeem configuratie implementeren

**Richt lijnen**: niet van toepassing; Klanten-lockbox heeft geen Configureer bare beveiligings instellingen.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7,8: hulpprogram ma's voor het beheer van systeem configuratie implementeren voor besturings systemen

**Richt lijnen**: niet van toepassing; deze richt lijn is bedoeld voor reken resources.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7,9: geautomatiseerde configuratie bewaking voor Azure-Services implementeren

**Richt lijnen**: niet van toepassing; Klanten-lockbox heeft geen Configureer bare beveiligings instellingen.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: geautomatiseerde configuratie bewaking voor besturings systemen implementeren

**Richt lijnen**: niet van toepassing; deze richt lijn is bedoeld voor reken resources.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="711-manage-azure-secrets-securely"></a>7,11: Azure-geheimen veilig beheren

**Richt lijnen**: niet van toepassing; de toegang tot Klanten-lockbox aanvragen is beperkt tot de eigenaar van het Azure-abonnement dat de resource instalt. Er zijn geen wacht woorden, geheimen of sleutels vereist voor toegang tot Klanten-lockbox buiten het logboek als eigenaar van de Tenant.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: identiteiten veilig en automatisch beheren

**Richt lijnen**: niet van toepassing; Klanten-lockbox maakt geen gebruik van beheerde identiteiten.

* [Azure-Services die beheerde identiteiten ondersteunen](../../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: onbedoelde referentie blootstelling elimineren

**Richt lijnen**: referentie scanner implementeren om referenties in code te identificeren. Door het gebruik van Credential Scanner worden gebruikers ook aangemoedigd om gedetecteerde referenties naar veiligere locaties, zoals Azure Key Vault, te verplaatsen.

* [Referentie scanner instellen](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="malware-defense"></a>Beveiliging tegen malware

*Zie [beveiligings beheer: verdediging tegen malware](../benchmarks/security-control-malware-defense.md)voor meer informatie.*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: centraal beheerde anti-malware-software gebruiken

**Richt lijnen**: niet van toepassing; deze richt lijn is bedoeld voor reken resources. Micro soft antimalware is ingeschakeld op de onderliggende host die ondersteuning biedt voor de Klanten-lockbox oplossing.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: scan bestanden die moeten worden geüpload naar niet-reken resources van Azure

**Hulp**: micro soft antimalware is ingeschakeld op de onderliggende host die ondersteuning biedt voor Azure-services zoals klanten-lockbox.

Het is uw verantwoordelijkheid om vooraf te scannen op inhoud die wordt geüpload naar niet-reken resources van Azure. Micro soft heeft geen toegang tot klant gegevens en kan daarom geen anti-malware scans uitvoeren van klant inhoud namens u.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: controleren of anti-malware-software en hand tekeningen zijn bijgewerkt

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources. Micro soft antimalware is ingeschakeld op de onderliggende host die ondersteuning biedt voor Azure-Services, maar wordt niet uitgevoerd op de inhoud van de klant.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

## <a name="data-recovery"></a>Gegevensherstel

*Zie [beveiligings beheer: gegevens herstel](../benchmarks/security-control-data-recovery.md)voor meer informatie.*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: controleren op regel matige automatische back-ups

**Richt lijnen**: niet van toepassing; Klanten-lockbox zichzelf slaat geen klant gegevens op.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: volledige back-ups van het systeem uitvoeren en een back-up maken van een door de klant beheerde sleutels

**Richt lijnen**: niet van toepassing; Klanten-lockbox zichzelf slaat geen klant gegevens op.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: alle back-ups valideren, inclusief door de klant beheerde sleutels

**Richt lijnen**: niet van toepassing; Klanten-lockbox zichzelf slaat geen klant gegevens op.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: zorg voor de bescherming van back-ups en door de klant beheerde sleutels

**Richt lijnen**: niet van toepassing; Klanten-lockbox zichzelf geen klant gegevens opslaat, worden ook geen sleutels of wacht woorden gebruikt voor toegang.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

## <a name="incident-response"></a>Reageren op incidenten

*Zie voor meer informatie [beveiligings beheer: reactie op incidenten](../benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: een hand leiding voor reactie op incidenten maken

**Richtlijnen**: Stel voor uw organisatie een responshandleiding op voor gebruik bij incidenten. Zorg ervoor dat er schriftelijke responsplannen zijn waarin alle rollen van het personeel worden gedefinieerd, evenals alle fasen in het afhandelen/managen van incidenten, vanaf de detectie van het incident tot een evaluatie ervan achteraf.

* [Werk stroom automatisering configureren in Azure Security Center](../../security-center/security-center-planning-and-operations-guide.md)

* [Richt lijnen voor het bouwen van uw eigen beveiligings incident antwoord proces](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Micro soft Security Response Center anatomie van een incident](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Klant kan ook gebruikmaken van de hand leiding voor de verwerking van het computer beveiligings incident van het NIST om hulp te bieden bij het maken van een eigen incident respons plan](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: een beoordelings procedure voor incidenten en prioriteits procedures maken

**Hulp**: Security Center wijst aan elke waarschuwing een Ernst toe om u te helpen bepalen welke waarschuwingen het eerst moeten worden onderzocht. De ernst is gebaseerd op de manier waarop vertrouwen Security Center is in de zoek actie of het analyse programma dat wordt gebruikt om de waarschuwing te geven, evenals het betrouwbaarheids niveau dat er schadelijke bedoelingen zijn achter de activiteit die tot de waarschuwing heeft geleid.

Daarnaast kunt u ook duidelijk abonnementen markeren (voor bijvoorbeeld productie, niet-productie) en maak een naamgevings systeem om Azure-resources duidelijk te identificeren en te categoriseren.

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="103-test-security-response-procedures"></a>10,3: procedures voor beveiligings antwoorden testen

**Richt lijnen**: oefent oefeningen uit om de respons mogelijkheden van uw systeem op een gewone uitgebracht te testen. Stel vast waar zich zwakke plekken en hiaten bevinden, en wijzig zo nodig het plan.

* [Raadpleeg de publicatie van het NIST: hand leiding voor het testen, trainen en uitoefenen van Program Ma's voor IT-plannen en-mogelijkheden](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: contact gegevens van het beveiligings incident opgeven en waarschuwings meldingen configureren voor beveiligings incidenten

**Hulp**: contact gegevens van beveiligings incidenten worden door micro soft gebruikt om contact met u op te nemen als het micro soft Security Response Center (MSRC) detecteert dat de gegevens van de klant zijn geopend door een onrecht matige of niet-gemachtigde partij. Bekijk incidenten na het feit om te controleren of de problemen zijn opgelost.

* [De Azure Security Center Security-contact persoon instellen](../../security-center/security-center-provide-security-contact-details.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: beveiligings waarschuwingen opnemen in uw reactie systeem van uw incident

**Richt lijnen**: uw Azure Security Center waarschuwingen en aanbevelingen exporteren met de functie continue export. Met doorlopend exporteren kunt u waarschuwingen en aanbevelingen hand matig of op een doorlopende manier exporteren. U kunt de Azure Security Center Data Connector gebruiken om de Sentinel van waarschuwingen te streamen.

* [Continue export configureren](../../security-center/continuous-export.md)

* [Waarschuwingen streamen naar Azure Sentinel](../../sentinel/connect-azure-security-center.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: de reactie op beveiligings waarschuwingen automatiseren

**Hulp**: gebruik de functie werk stroom automatisering in azure Security Center om automatisch reacties te activeren via ' Logic apps ' in beveiligings waarschuwingen en aanbevelingen.

* [Werk stroom automatisering en Logic Apps configureren](../../security-center/workflow-automation.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="penetration-tests-and-red-team-exercises"></a>Penetratietests en Red Team-oefeningen

*Zie voor meer informatie [Security Control: Indringings tests en Red team-oefeningen](../benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11,1: voert regel matig indringings tests van uw Azure-resources uit en zorgt voor herstel van alle essentiële beveiligings resultaten binnen 60 dagen

**Hulp**: 

* [Volg de richt lijnen van micro soft om ervoor te zorgen dat de indringings tests niet worden geschonden door het micro soft-beleid](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

* [U vindt hier meer informatie over de strategie van micro soft en de uitvoering van Red Teaming en live site indringings tests met door micro soft beheerde Cloud infrastructuur,-services en-toepassingen.](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Gedeeld

## <a name="next-steps"></a>Volgende stappen

- Zie de [Azure Security-Bench Mark](../benchmarks/overview.md)
- Meer informatie over [Azure-beveiligingsbasislijnen](../benchmarks/security-baselines-overview.md)