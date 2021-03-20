---
title: Azure-beveiligingsbasislijn voor Key Vault
description: Azure-beveiligingsbasislijn voor Key Vault
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/10/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: ad47ac7d51de6ab497d7061961c14b5324fab931
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98202450"
---
# <a name="azure-security-baseline-for-key-vault"></a>Azure-beveiligingsbasislijn voor Key Vault

De Azure-beveiligings basislijn voor Key Vault bevat aanbevelingen waarmee u de beveiligings postuur van uw implementatie kunt verbeteren.

De basis lijn voor deze service wordt opgehaald uit de [Azure Security Bench Mark-versie 1,0](../../security/benchmarks/overview.md), die aanbevelingen biedt over hoe u uw cloud oplossingen kunt beveiligen in azure met onze richt lijnen voor best practices.

Zie [overzicht van Azure Security-basis lijnen](../../security/benchmarks/security-baselines-overview.md)voor meer informatie.

## <a name="network-security"></a>Netwerkbeveiliging

*Zie [beveiligings beheer: netwerk beveiliging](../../security/benchmarks/security-control-network-security.md)voor meer informatie.*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1,1: Beveilig bronnen met behulp van netwerk beveiligings groepen of Azure Firewall op de Virtual Network

**Hulp**: Integreer Azure Key Vault met een persoonlijke Azure-koppeling. 

Met Azure Private Link service kunt u toegang krijgen tot Azure-Services (bijvoorbeeld Azure Key Vault) en Azure hosted Customer/partner-services via een persoonlijk eind punt in uw virtuele netwerk.

Een privé-eindpunt in Azure is een netwerkinterface waarmee u privé en veilig verbinding maakt met een service die door Azure Private Link mogelijk wordt gemaakt. Het privé-eindpunt maakt gebruik van een privé-IP-adres van uw VNet, waardoor de service feitelijk in uw VNet wordt geplaatst. Al het verkeer naar de service kan worden gerouteerd via het privé-eindpunt, zodat er geen gateways, NAT-apparaten, ExpressRoute of VPN-verbindingen of openbare IP-adressen nodig zijn. Verkeer tussen uw virtuele netwerk en de services wordt via het backbonenetwerk van Microsoft geleid, waarmee de risico's van het openbare internet worden vermeden. U kunt verbinding maken met een exemplaar van een Azure-resource, zodat u het hoogste granulariteit krijgt in toegangsbeheer.

Key Vault integreren met een persoonlijke Azure-koppeling:

https://docs.microsoft.com/azure/key-vault/private-link-service


**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1,2: de configuratie en het verkeer van Vnets, subnetten en Nic's bewaken en vastleggen

**Hulp**: gebruik Azure Security Center en volg aanbevelingen voor netwerk beveiliging om uw door Key Vault geconfigureerde resources in azure te beveiligen. 

Voor meer informatie over de netwerk beveiliging die wordt verstrekt door Azure Security Center: 

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="13-protect-critical-web-applications"></a>1,3: essentiële webtoepassingen beveiligen

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor webtoepassingen die worden uitgevoerd op Azure App Service of reken bronnen.


**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: communicatie met bekende schadelijke IP-adressen weigeren

**Hulp**: Schakel Azure DDoS Protection Standard in op de virtuele Azure-netwerken die zijn gekoppeld aan uw Key Vault-instanties voor beveiliging tegen gedistribueerde Denial-of-service-aanvallen. Gebruik Azure Security Center geïntegreerde bedreigings informatie om communicatie met bekende of ongebruikte Internet-IP-adressen te weigeren.

 
Azure DDoS Protection standaard beheren met de Azure Portal: https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Detectie van bedreigingen voor de service-laag van Azure in Azure Security Center: https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer


**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="15-record-network-packets-and-flow-logs"></a>1,5: netwerk pakketten en stroom logboeken vastleggen

**Hulp**: Azure Key Vault maakt geen gebruik van netwerk beveiligings groepen (NSG) en stroom logboeken voor Azure Key Vault niet worden vastgelegd. Gebruik in plaats daarvan persoonlijke Azure-koppeling om uw Azure Key Vault-instanties te beveiligen en diagnostische instellingen in te stellen voor het vastleggen van metrische gegevens en controle gebeurtenissen.

Key Vault integreren met een persoonlijke Azure-koppeling:

https://docs.microsoft.com/azure/key-vault/private-link-service

Azure Key Vault logboek registratie: https://docs.microsoft.com/azure/key-vault/key-vault-logging



**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: op netwerk gebaseerde inbreuk detectie/indringings systemen (ID'S/IP-adressen) implementeren

**Richt lijnen**: aan deze vereiste kan worden voldaan door het configureren van Advanced Threat Protection (ATP) voor Azure Key Vault. ATP biedt een extra laag beveiligings informatie. Dit hulp programma detecteert mogelijk schadelijke pogingen om Azure Key Vault accounts te openen of misbruik te maken.

Wanneer Azure Security Center afwijkende activiteiten detecteert, worden er waarschuwingen weer gegeven. Er wordt ook een e-mail verzonden naar de abonnements beheerder met details van de verdachte activiteit en aanbevelingen voor het onderzoeken en oplossen van de geïdentificeerde bedreigingen.

Geavanceerde bedreigings beveiliging instellen voor Azure Key Vault:

https://docs.microsoft.com/azure/security-center/advanced-threat-protection-key-vault



**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="17-manage-traffic-to-web-applications"></a>1,7: verkeer naar webtoepassingen beheren

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor webtoepassingen die worden uitgevoerd op Azure App Service of reken bronnen.


**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: de complexiteit en administratieve overhead van netwerk beveiligings regels minimaliseren

**Richt lijnen**: voor resources die toegang nodig hebben tot uw Azure Key Vault-instanties, gebruikt u de labels van Azure-service voor de Azure Key Vault om netwerk toegangs beheer te definiëren voor netwerk beveiligings groepen of Azure firewall. U kunt servicetags gebruiken in plaats van specifieke IP-adressen wanneer u beveiligingsregels maakt. Door de naam van de service label (bijvoorbeeld ApiManagement) op te geven in het juiste bron-of doel veld van een regel, kunt u het verkeer voor de bijbehorende service toestaan of weigeren. Micro soft beheert de adres voorvoegsels die zijn opgenomen in het servicetag van de service en werkt de servicetag automatisch bij met gewijzigde adressen.

Overzicht van Azure-service Tags: https://docs.microsoft.com/azure/virtual-network/service-tags-overview


**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: standaard beveiligings configuraties voor netwerk apparaten onderhouden

**Hulp**: Definieer en implementeer standaard beveiligings configuraties voor netwerk resources die zijn gekoppeld aan uw Azure Key Vault-instanties met Azure Policy. Gebruik Azure Policy aliassen in de naam ruimten ' micro soft. sleutel kluis ' en ' micro soft. Network ' om aangepaste beleids regels te maken om de netwerk configuratie van uw Azure Key Vault instanties te controleren of af te dwingen. U kunt ook gebruik maken van ingebouwde beleids definities die betrekking hebben op Azure Key Vault, zoals:

Key Vault moet gebruikmaken van een virtuele-netwerkservice-eindpunt

Zelf studie: beleid maken en beheren om naleving af te dwingen:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure Policy voor beelden:

https://docs.microsoft.com/azure/governance/policy/samples/#networ

Snelstartgids: een blauw druk definiëren en toewijzen in de portal:

https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal


**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="110-document-traffic-configuration-rules"></a>1,10: configuratie regels voor het document verkeer

**Richt lijnen**: Gebruik labels voor bronnen die betrekking hebben op netwerk beveiliging en verkeers stroom voor uw Azure Key Vault-instanties voor het leveren van meta gegevens en logische organisaties.

Gebruik een van de ingebouwde Azure Policy definities die betrekking hebben op labelen, zoals ' tag vereisen en de waarde ', om ervoor te zorgen dat alle resources met tags worden gemaakt en u op de hoogte moet zijn van bestaande niet-gelabelde resources.

U kunt Azure PowerShell of Azure CLI gebruiken om op basis van hun labels acties op te zoeken of uit te voeren op resources.

Tags gebruiken om uw Azure-resources te organiseren:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags


**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: gebruik automatische hulpprogram ma's om netwerk bron configuraties te bewaken en wijzigingen te detecteren

**Hulp**: Azure-activiteiten logboek gebruiken om netwerk resource configuraties te bewaken en wijzigingen te detecteren voor netwerk bronnen die betrekking hebben op uw Azure Key Vault exemplaren. Maak waarschuwingen in Azure Monitor die worden geactiveerd wanneer er wijzigingen in kritieke netwerk bronnen plaatsvinden.

Activiteiten logboek gebeurtenissen van Azure weer geven en ophalen:

https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

Waarschuwingen voor activiteiten logboek maken, weer geven en beheren met behulp van Azure Monitor:

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log


**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="logging-and-monitoring"></a>Logboekregistratie en bewaking

*Zie voor meer informatie [beveiligings beheer: logboek registratie en controle](../../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: goedgekeurde tijd synchronisatie bronnen gebruiken

**Richt lijnen**: niet van toepassing; Micro soft onderhoudt de tijd bron die wordt gebruikt voor Azure-resources, zoals Azure Key Vault, voor tijds tempels in de logboeken.


**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: micro soft

### <a name="22-configure-central-security-log-management"></a>2,2: Centraal beveiligings logboek beheer configureren

**Hulp**: opname logboeken via Azure monitor voor het verzamelen van beveiligings gegevens die zijn gegenereerd door Azure Key Vault. Gebruik in Azure Monitor Azure Log Analytics-werk ruimte voor het opvragen en uitvoeren van analyses en gebruik Azure Storage accounts voor lange termijn/archiverings opslag. U kunt ook gegevens in-of uitschakelen voor Azure Sentinel of een SIEM van derden. 

Azure Key Vault logboek registratie:

https://docs.microsoft.com/azure/key-vault/key-vault-logging

Snelstartgids: Azure-Sentinel op de trein:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard


**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: controle logboek registratie inschakelen voor Azure-resources

**Hulp**: Schakel Diagnostische instellingen in op uw Azure Key Vault-instanties voor toegang tot controle-, beveiligings-en Diagnostische logboeken. Activiteiten logboeken, die automatisch beschikbaar zijn, omvatten gebeurtenis bron, datum, gebruiker, tijds tempel, bron adressen, doel adressen en andere nuttige elementen.

Azure Key Vault logboek registratie:

https://docs.microsoft.com/azure/key-vault/key-vault-logging


**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: beveiligings logboeken verzamelen van besturings systemen

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources.


**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="25-configure-security-log-storage-retention"></a>2,5: Bewaar beveiliging van het beveiligings logboek configureren

**Richt lijnen**: in azure monitor voor de log Analytics werk ruimte die wordt gebruikt om uw Azure Key Vault logboeken te bewaren, stelt u de retentie periode in volgens de nalevings voorschriften van uw organisatie. Gebruik Azure Storage-accounts voor lange termijn/archiverings opslag.

De Bewaar periode voor gegevens wijzigen: https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period


**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="26-monitor-and-review-logs"></a>2,6: Logboeken bewaken en controleren

**Richt lijnen**: Logboeken analyseren en bewaken voor afwijkend gedrag en regel matig de resultaten controleren voor uw met Azure Key Vault beveiligde resources. Gebruik de Log Analytics werk ruimte van Azure Monitor om logboeken te controleren en query's uit te voeren op logboek gegevens. U kunt ook gegevens in-of uitschakelen voor Azure Sentinel of een SIEM van derden. 

Snelstartgids: Azure-Sentinel op de trein:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Aan de slag met Log Analytics in Azure Monitor:

https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Aan de slag met logboek query's in Azure Monitor:

https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries


**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="27-enable-alerts-for-anomalous-activity"></a>2,7: waarschuwingen inschakelen voor afwijkende activiteit

**Hulp**: schakel in azure Security Center Advanced Threat Protection (ATP) in voor Key Vault. Diagnostische instellingen in Azure Key Vault inschakelen en logboeken naar een Log Analytics werkruimte verzenden. Onboarding van uw Log Analytics-werk ruimte naar Azure-Sentinel, omdat dit een via-oplossing (Security Orchestration Automated Response) biedt. Hiermee kunnen playbooks (geautomatiseerde oplossingen) worden gemaakt en gebruikt om beveiligings problemen op te lossen.

Snelstartgids: Azure-Sentinel op de trein:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Beveiligings waarschuwingen beheren en erop reageren in Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts

Reageren op gebeurtenissen met Azure Monitor waarschuwingen:

https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response


**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="28-centralize-anti-malware-logging"></a>2,8: registratie van anti-malware centraliseren

**Richt lijnen**: niet van toepassing; Azure Key Vault geen aan anti-malware gerelateerde logboeken verwerkt of produceert.


**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="29-enable-dns-query-logging"></a>2,9: DNS-query logboek registratie inschakelen

**Richt lijnen**: niet van toepassing; Azure Key Vault verwerkt of produceert geen aan DNS gerelateerde Logboeken.


**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="210-enable-command-line-audit-logging"></a>2,10: controle logboek registratie op opdracht regel inschakelen

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources.


**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

## <a name="identity-and-access-control"></a>Identiteits- en toegangsbeheer

*Zie [beveiligings beheer: identiteit en Access Control](../../security/benchmarks/security-control-identity-access-control.md)voor meer informatie.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: een inventaris van beheerders accounts onderhouden

**Hulp**: behoud een inventaris van uw Azure Active Directory-geregistreerde toepassingen, evenals alle gebruikers accounts die toegang hebben tot uw Azure Key Vault sleutels, geheimen en certificaten. U kunt de Azure Portal of Power shell gebruiken om Key Vault toegang te zoeken en af te stemmen. Als u de toegang in Power shell wilt weer geven, gebruikt u de volgende opdracht:

(Get-AzResource-ResourceId [KeyVaultResourceID]). Eigenschappen. AccessPolicies

Een toepassing registreren met Azure Active Directory:

https://docs.microsoft.com/azure/key-vault/key-vault-manage-with-cli2#registering-an-application-with-azure-active-directory

Veilige toegang tot een sleutel kluis:

https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault


**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="32-change-default-passwords-where-applicable"></a>3,2: standaard wachtwoorden wijzigen indien van toepassing

**Richt lijnen**: niet van toepassing; Azure Key Vault heeft niet het concept standaard wachtwoord als verificatie wordt gegeven door Active Directory en beveiligd met Azure op rollen gebaseerd toegangs beheer (Azure RBAC).


**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: speciale beheerders accounts gebruiken

**Richt lijnen**: Maak standaard procedures voor het gebruik van speciale beheerders accounts die toegang hebben tot uw Azure Key Vault-exemplaren. Gebruik Azure Security Center identiteits-en toegangs beheer (momenteel in de preview-versie) om het aantal actieve beheerders accounts te controleren.

Identiteit en toegang controleren (preview):

https://docs.microsoft.com/azure/security-center/security-center-identity-access


**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: eenmalige aanmelding (SSO) met Azure Active Directory gebruiken

**Richt lijnen**: gebruik een Azure-Service-Principal in combi natie met de AppId, TenantID en ClientSecret om uw toepassing naadloos te verifiëren en het token op te halen dat wordt gebruikt voor toegang tot uw Azure Key Vault geheimen.

Service-naar-service-verificatie voor het Azure Key Vault met behulp van .NET:

https://docs.microsoft.com/azure/key-vault/service-to-service-authentication



**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: multi-factor Authentication gebruiken voor alle op Azure Active Directory gebaseerde toegang

**Hulp** bij het inschakelen van Azure Active Directory multi-factor Authentication en het volgen van de aanbevelingen voor Azure Security Center identiteits-en toegangs beheer (momenteel in Preview) om u te helpen bij het beveiligen van uw event hub-resources.

Een Azure AD Multi-Factor Authentication-implementatie op basis van de Cloud plannen:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Identiteit en toegang controleren (preview):

https://docs.microsoft.com/azure/security-center/security-center-identity-access


**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: gebruik speciale machines (privileged Access workstations) voor alle beheer taken

**Hulp**: gebruik een privileged Access-werk station (Paw) met Azure AD multi-factor Authentication (MFA) dat is geconfigureerd voor aanmelding bij en configureren van Key Vault ingeschakelde resources. 

Privileged Access workstations: https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/ 

Een Azure AD Multi-Factor Authentication-implementatie op basis van de Cloud plannen: https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted



**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3,7: logboek en waarschuwing voor verdachte activiteiten van beheerders accounts

**Hulp**: gebruik Azure Active Directory (Azure AD) PRIVILEGED Identity Management (PIM) voor het genereren van Logboeken en waarschuwingen wanneer verdachte of onveilige activiteiten in de omgeving worden uitgevoerd. Gebruik Azure AD-risico detecties om waarschuwingen en rapporten weer te geven over Risk ante gebruikers gedrag. Voor aanvullende logboek registratie kunt u waarschuwingen voor Azure Security Center risico detectie verzenden naar Azure Monitor en aangepaste waarschuwingen/meldingen configureren met actie groepen.

Schakel Advanced Threat Protection (ATP) in voor Azure Key Vault om waarschuwingen te genereren voor verdachte activiteiten.

Azure AD Privileged Identity Management implementeren (PIM): https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan

Geavanceerde bedreigings beveiliging instellen voor Azure Key Vault (preview): https://docs.microsoft.com/azure/security-center/advanced-threat-protection-key-vault

Waarschuwingen voor Azure Key Vault (preview): https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-azurekv

Detectie van Azure Active Directory risico: https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events

Actie groepen maken en beheren in de Azure Portal: https://docs.microsoft.com/azure/azure-monitor/platform/action-groups



**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: Azure-resources alleen beheren vanaf goedgekeurde locaties

**Hulp**: de locatie voorwaarde van een beleid voor voorwaardelijke toegang configureren en uw benoemde locaties beheren. Met benoemde locaties kunt u logische groeperingen van IP-adresbereiken of landen en regio's maken. U kunt de toegang tot gevoelige bronnen, zoals uw Key Vault geheimen, beperken tot uw geconfigureerde benoemde locaties.

Wat is de voor waarde van de locatie in Azure Active Directory voorwaardelijke toegang?: https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations



**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="39-use-azure-active-directory"></a>3,9: Azure Active Directory gebruiken

**Hulp**: gebruik Azure Active Directory (Azure AD) als centraal verificatie-en autorisatie systeem voor Azure-resources, zoals Key Vault. Hiermee kunnen op rollen gebaseerd toegangs beheer (Azure RBAC) van Azure worden uitgevoerd om gevoelige bronnen te beheren.

 

Snelstartgids: een nieuwe Tenant maken in Azure Active Directory:

https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant


**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: regel matig gebruikers toegang controleren en afstemmen

**Hulp**: Raadpleeg Azure Active Directory-Logboeken (Azure AD) om verouderde accounts met Azure Key Vault beheerders rollen te detecteren. Daarnaast kunt u Azure AD-toegangs beoordelingen gebruiken om groepslid maatschappen efficiënt te beheren, toegang te krijgen tot bedrijfs toepassingen die kunnen worden gebruikt voor toegang tot Azure Key Vault en roltoewijzingen. Gebruikers toegang moet regel matig worden gecontroleerd, bijvoorbeeld elke 90 dagen, om ervoor te zorgen dat alleen de juiste gebruikers toegang hebben.

Azure Active Directory rapporten en controle-documentatie:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/

Wat zijn Azure AD-toegangs beoordelingen?:

https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview


**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3,11: controle pogingen om toegang te krijgen tot gedeactiveerde accounts

**Hulp**: de diagnostische instellingen voor Azure Key Vault en Azure Active Directory inschakelen, waarbij alle logboeken worden verzonden naar een log Analytics-werk ruimte. Gewenste waarschuwingen configureren (zoals pogingen om toegang te krijgen tot uitgeschakelde geheimen) in Log Analytics.

Azure AD-logboeken integreren met Azure Monitor-logboeken: https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

Migratie van de oude Key Vault-oplossing: https://docs.microsoft.com/azure/azure-monitor/insights/azure-key-vault#migrating-from-the-old-key-vault-solution



**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: waarschuwing voor de afwijking van het aanmeldings gedrag van accounts

**Hulp**: gebruik de functies voor identiteits beveiliging en risico detectie van Azure Active Directory om automatische antwoorden te configureren op gedetecteerde verdachte acties die betrekking hebben op uw Azure Key Vault beveiligde bronnen. Schakel automatische antwoorden via Azure Sentinel in om de beveiligings reacties van uw organisatie te implementeren. 

Rapport Risk ante aanmeldingen in de Azure Active Directory-portal: https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins 

Procedure: risico beleid configureren en inschakelen: https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

Azure-Sentinel onboarden:  https://docs.microsoft.com/azure/sentinel/quickstart-onboard


**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: micro soft biedt toegang tot relevante klant gegevens tijdens ondersteunings scenario's

**Richt lijnen**: niet van toepassing; Klanten-lockbox wordt niet ondersteund voor Azure Key Vault.

Ondersteunde services en scenario's in algemene Beschik baarheid: https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability



**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

## <a name="data-protection"></a>Gegevensbeveiliging

*Zie [beveiligings beheer: gegevens beveiliging](../../security/benchmarks/security-control-data-protection.md)voor meer informatie.*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: een inventaris van gevoelige informatie onderhouden

**Hulp**: Tags gebruiken om Azure-resources te helpen bij het bijhouden of verwerken van gevoelige informatie over Azure Key Vault ingeschakelde resources. 

Tags gebruiken om uw Azure-resources te organiseren: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags



**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: systemen isoleren die gevoelige informatie opslaan of verwerken

**Hulp**: u kunt de toegang tot Azure Key Vault beveiligen door gebruik te maken van de service-eind punten van het virtuele netwerk die zijn geconfigureerd om de toegang tot specifieke subnetten te beperken.

Nadat de firewall regels van kracht zijn, kunt u alleen Azure Key Vault gegevenslaag bewerkingen uitvoeren wanneer uw aanvraag afkomstig is van de toegestane subnetten of IP-adresbereiken. Dit geldt ook voor Azure Key Vault toegang in de Azure Portal. Hoewel u naar een sleutel kluis kunt bladeren vanuit de Azure Portal, kunt u mogelijk geen sleutels, geheimen of certificaten weer geven als uw client computer zich niet in de lijst met toegestane computers bevindt. Dit is ook van invloed op de Azure Key Vault kiezer en andere Azure-Services. U kunt mogelijk lijsten met sleutel kluizen zien, maar geen lijst met sleutels als firewall regels verhinderen dat uw client computer dit doet.

Azure Key Vault firewalls en virtuele netwerken configureren: https://docs.microsoft.com/azure/key-vault/key-vault-network-security

Virtuele netwerk service-eind punten voor Azure Key Vault: https://docs.microsoft.com/azure/key-vault/key-vault-overview-vnet-service-endpoints



**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: niet-geautoriseerde overdracht van gevoelige gegevens controleren en blok keren

**Hulp**: alle gegevens die zijn opgeslagen in azure Key Vault, worden beschouwd als gevoelig. Gebruik Azure Key Vault besturings elementen voor gegevens vlak om toegang tot Azure Key Vault geheimen te beheren. U kunt ook de ingebouwde firewall van Key Vault gebruiken om de toegang op de netwerklaag te beheren. Als u de toegang tot Azure Key Vault wilt controleren, schakelt u Key Vault Diagnostische instellingen in en verzendt u logboeken naar een Azure Storage-account of Log Analytics-werk ruimte.

Veilige toegang tot een sleutel kluis: https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault

Azure Key Vault firewalls en virtuele netwerken configureren: https://docs.microsoft.com/azure/key-vault/key-vault-network-security

Azure Key Vault logboek registratie: https://docs.microsoft.com/azure/key-vault/key-vault-logging



**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: alle gevoelige gegevens in de overdracht versleutelen

**Richt lijnen**: al het verkeer dat moet worden Azure Key Vault voor de toegang tot verificatie, beheer en gegevenslaag, is versleuteld en gaat over https: poort 443. (Er is echter af en toe HTTP [poort 80] verkeer voor CRL.) Azure Key Vault blijven TLS 1,1-en TLS 1,0-gegevens worden opgenomen. Gegevens kunnen worden beperkt tot TLS 1,2 via configuratie aan de client zijde.



Toegang tot Azure Key Vault achter een firewall: https://docs.microsoft.com/azure/key-vault/key-vault-access-behind-firewall



**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: micro soft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: een actief detectie hulpprogramma gebruiken om gevoelige gegevens te identificeren

**Richt lijnen**: niet van toepassing; alle gegevens in Azure Key Vault (geheimen, sleutels en certificaten) worden als gevoelig beschouwd.


**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: Azure RBAC gebruiken om de toegang tot resources te beheren

**Hulp**: veilige toegang tot het beheer en gegevens vlak van uw Azure Key Vault-instanties.

Veilige toegang tot een sleutel kluis:

https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault


**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: voor komen dat gegevens verlies op basis van host wordt gebruikt voor het afdwingen van toegangs beheer

**Hulp**: micro soft beheert de onderliggende infra structuur voor Azure Key Vault en heeft strikte controles geïmplementeerd om verlies of bloot stelling van klant gegevens te voor komen.

Wat is Azure Sleutelkluis?

https://docs.microsoft.com/azure/key-vault/key-vault-overview

Azure-klant gegevens beveiliging:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: micro soft

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: gevoelige informatie op rest versleutelen

**Hulp**: alle beheerde objecten (sleutel, certificaten en geheimen) worden op rest versleuteld in azure Key Vault.

Ondersteunende documentatie:

- [Versleutelings model en sleutel beheer tabel](../../security/fundamentals/encryption-atrest.md)


**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: micro soft

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: wijzigingen in essentiële Azure-resources vastleggen en waarschuwen

**Hulp**: gebruik de Azure Key Vault Analytics-oplossing in Azure Monitor om Azure Key Vault controle gebeurtenis logboeken te controleren.

Azure Key Vault Analytics-oplossing in Azure Monitor:

https://docs.microsoft.com/azure/azure-monitor/insights/azure-key-vault



**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="vulnerability-management"></a>Beheer van beveiligingsproblemen

*Zie [beveiligings beheer: beveiligingslek beheer](../../security/benchmarks/security-control-vulnerability-management.md)voor meer informatie.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: automatische hulpprogram ma's voor het scannen van beveiligings problemen uitvoeren

**Hulp**: micro soft voert beveiligings beheer uit op de onderliggende systemen die ondersteuning bieden voor Azure Key Vault.


**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: micro soft

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: geautomatiseerde oplossing voor patch beheer voor besturings systemen implementeren

**Richt lijnen**: n.v.t.; Micro soft voert patch beheer uit op de onderliggende systemen die ondersteuning bieden voor Key Vault.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5,3: Implementeer een geautomatiseerde oplossing voor software patch beheer van derden

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources.


**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: vergelijken van back-to-back-problemen

**Hulp**: micro soft voert beveiligings beheer uit op de onderliggende systemen die ondersteuning bieden voor Key Vault.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: micro soft

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: een risico classificatie proces gebruiken om prioriteit te geven aan het herstel van ontdekte beveiligings problemen

**Richt lijnen**: gebruik de standaard risico classificaties (beveiligde Score) van Azure Security Center.

Verbeter uw beveiligde Score in Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-secure-score


**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="inventory-and-asset-management"></a>Inventarisatie en Asset Management

*Zie voor meer informatie [beveiligings beheer: inventarisatie en activa beheer](../../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-azure-asset-discovery"></a>6,1: Azure Asset Discovery gebruiken

**Richt lijnen**: gebruik Azure resource Graph om alle resources (inclusief Azure Key Vault instanties) in uw abonnement te doorzoeken en te detecteren. Zorg ervoor dat u de juiste machtigingen (lezen) hebt in uw Tenant en dat u alle Azure-abonnementen kunt inventariseren, evenals de resources in uw abonnementen.

Snelstartgids: uw eerste resource grafiek query uitvoeren met Azure resource Graph Explorer:

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Ontvang abonnementen waartoe het huidige account toegang heeft.:

https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Wat is Azure RBAC (toegangsbeheer op basis van rollen)?

https://docs.microsoft.com/azure/role-based-access-control/overview

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="62-maintain-asset-metadata"></a>6,2: meta gegevens van activa onderhouden

**Hulp**: Labels Toep assen op Azure Key Vault resources die meta gegevens geven om ze logisch in een taxonomie te organiseren.

Tags maken en gebruiken:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: niet-geautoriseerde Azure-resources verwijderen

**Richt lijnen**: Gebruik labels, beheer groepen en afzonderlijke abonnementen, waar nodig, om Azure Key Vault instanties en gerelateerde resources te organiseren en bij te houden. Sluit de inventaris regel matig af en zorg ervoor dat niet-geautoriseerde resources tijdig worden verwijderd uit het abonnement.

Een aanvullend Azure-abonnement maken:

https://docs.microsoft.com/azure/billing/billing-create-subscription

Beheer groepen maken voor resource organisatie en-beheer:

https://docs.microsoft.com/azure/governance/management-groups/create

Tags gebruiken om uw Azure-resources te organiseren: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags


**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6,4: een inventaris van goedgekeurde Azure-resources en software titels onderhouden

**Hulp**: een lijst met goedgekeurde Azure-resources en goedgekeurde software voor uw reken resources definiëren

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: monitor voor niet-goedgekeurde Azure-resources

**Richt lijnen**: gebruik Azure-beleid om beperkingen toe te voegen aan het type resources dat kan worden gemaakt in klant abonnement (en) met de volgende ingebouwde beleids definities:

- Niet toegestane resourcetypen

- Toegestane brontypen

Daarnaast gebruikt u de resource grafiek van Azure voor het opvragen/detecteren van resources binnen een of meer abonnementen.

Zelf studie: beleid maken en beheren om naleving af te dwingen: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Snelstartgids: uw eerste resource grafiek query uitvoeren met Azure resource Graph Explorer: https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal



**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: monitor voor niet-goedgekeurde software toepassingen binnen reken resources

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources.


**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: niet-goedgekeurde Azure-resources en software toepassingen verwijderen

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor Azure als geheel en reken resources.


**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="68-use-only-approved-applications"></a>6,8: alleen goedgekeurde toepassingen gebruiken

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources.


**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="69-use-only-approved-azure-services"></a>6,9: alleen goedgekeurde Azure-Services gebruiken

**Richt lijnen**: gebruik Azure-beleid om beperkingen toe te voegen aan het type resources dat kan worden gemaakt in klant abonnement (en) met de volgende ingebouwde beleids definities:

- Niet toegestane resourcetypen

- Toegestane brontypen

Zelf studie: beleid maken en beheren om naleving af te dwingen: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure Policy voor beelden: https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types



**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="610-implement-approved-application-list"></a>6,10: lijst met goedgekeurde toepassingen implementeren

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources.


**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="611-limit-users-ability-to-interact-with-azureresources-manager-via-scripts"></a>6,11: de mogelijkheid van gebruikers om te communiceren met kunt Manager via scripts beperken

**Richt lijnen**: gebruik de voorwaardelijke toegang van Azure om gebruikers de mogelijkheid te bieden om te communiceren met Azure Resource Manager (arm) door ' blok toegang ' te configureren voor de app Microsoft Azure management. Dit kan ertoe leiden dat het maken en wijzigen van resources binnen een omgeving met hoge beveiliging, zoals die met Key Vault configuratie, wordt voor komen.

Toegang tot beheer van Azure beheren met voorwaardelijke toegang:

https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management


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

*Zie [beveiligings beheer: beveiligde configuratie](../../security/benchmarks/security-control-secure-configuration.md)voor meer informatie.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: veilige configuraties instellen voor alle Azure-resources

**Hulp**: gebruik Azure Policy aliassen in de naam ruimte ' micro soft.-sleutel kluis ' om aangepaste beleids regels te maken om de configuratie van uw Azure Key Vault instanties te controleren of af te dwingen. U kunt ook ingebouwde Azure Policy definities gebruiken voor Azure Key Vault zoals:

Key Vault-objecten moeten herstelbaar zijn

Diagnostische instellingen implementeren voor Key Vault naar Log Analytics-werkruimte

Diagnostische logboeken in Key Vault moeten zijn ingeschakeld

Key Vault moet gebruikmaken van een virtuele-netwerkservice-eindpunt

Diagnostische instellingen voor Key Vault toepassen op Event Hub

Gebruik aanbevelingen van Azure Security Center als een veilige configuratie basislijn voor uw Azure Key Vault exemplaren.

Beschik bare Azure Policy aliassen weer geven:

https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Zelf studie: beleid maken en beheren om naleving af te dwingen:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage


**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: veilige configuraties van besturings systemen instellen

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources.


**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: Beveilig Azure-resource configuraties onderhouden

**Hulp**: gebruik Azure Policy [deny] en [implementeren indien niet aanwezig] voor het afdwingen van beveiligde instellingen voor uw Azure Key Vault-resources. 

Zelf studie: beleid maken en beheren om naleving af te dwingen:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage 

  
Azure Policy effecten begrijpen: 

https://docs.microsoft.com/azure/governance/policy/concepts/effects


**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: veilige configuraties van besturings systemen onderhouden

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources.


**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: de configuratie van Azure-resources veilig opslaan

**Richt lijnen**: als u aangepaste Azure Policy definities gebruikt voor uw Azure Key Vault ingeschakelde resources, gebruikt u Azure opslag plaatsen om uw code veilig op te slaan en te beheren.

Code opslaan in azure DevOps: 

https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops 

Documentatie voor Azure opslag plaatsen: 

https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: aangepaste installatie kopieën van een besturings systeem veilig opslaan

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources.


**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="77-deploy-system-configuration-management-tools"></a>7,7: hulpprogram ma's voor het beheer van systeem configuratie implementeren

**Richt lijnen**: gebruik Azure Policy aliassen in de naam ruimte ' micro soft.-sleutel kluis ' om aangepaste beleids regels te maken om systeem configuraties te Signa lering, te controleren en af te dwingen. Ontwikkel bovendien een proces en pijp lijn voor het beheren van beleids uitzonderingen.

Azure Policy configureren en beheren:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7,8: hulpprogram ma's voor het beheer van systeem configuratie implementeren voor besturings systemen

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources.


**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7,9: geautomatiseerde configuratie bewaking voor Azure-Services implementeren

**Hulp**: gebruik Azure Security Center om basislijn scans uit te voeren voor uw met Azure Key Vault beveiligde resources 

  

Aanbevelingen herstellen in Azure Security Center: 

https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: geautomatiseerde configuratie bewaking voor besturings systemen implementeren

**Richt lijnen**: niet van toepassing; deze bench Mark is bedoeld voor reken resources.


**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="711-manage-azure-secrets-securely"></a>7,11: Azure-geheimen veilig beheren

**Hulp**: gebruik Managed Service Identity in combi natie met Azure Key Vault om het geheim beheer voor uw Cloud toepassingen te vereenvoudigen en te beveiligen. Zorg ervoor dat Azure Key Vault Soft-verwijdering is ingeschakeld.

Integratie met door Azure beheerde identiteiten:

https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Een Key Vault maken:

https://docs.microsoft.com/azure/key-vault/general/quick-create-portal

Verificatie bij Key Vault:

https://docs.microsoft.com/azure/key-vault/general/authentication

Een Key Vault toegangs beleid toewijzen:

https://docs.microsoft.com/azure/key-vault/general/assign-access-policy-portal

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: identiteiten veilig en automatisch beheren

**Hulp**: gebruik Managed Service Identity in combi natie met Azure Key Vault om het geheim beheer voor uw Cloud toepassingen te vereenvoudigen en te beveiligen. 

  

* [Integratie met door Azure beheerde identiteiten](../../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

* [Een Key Vault maken](quick-create-portal.md)

* [Verifiëren bij Key Vault](authentication.md)

* [Toegangs beleid voor Key Vault toewijzen](assign-access-policy-portal.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: onbedoelde referentie blootstelling elimineren

**Richt lijnen**: referentie scanner implementeren om referenties in code te identificeren. Door het gebruik van Credential Scanner worden gebruikers ook aangemoedigd om gedetecteerde referenties naar veiligere locaties, zoals Azure Key Vault, te verplaatsen.  
  
 Referentie scanner instellen: https://secdevtools.azurewebsites.net/helpcredscan.html

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="malware-defense"></a>Beveiliging tegen malware

*Zie [beveiligings beheer: verdediging tegen malware](../../security/benchmarks/security-control-malware-defense.md)voor meer informatie.*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: centraal beheerde anti-malware-software gebruiken

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources. Micro soft verzorgt anti-malware voor het onderliggende platform.


**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: scan bestanden die moeten worden geüpload naar niet-reken resources van Azure

**Hulp**: micro soft anti-malware is ingeschakeld op de onderliggende host die ondersteuning biedt voor Azure-Services (bijvoorbeeld Azure Key Vault), maar wordt niet uitgevoerd op de inhoud van de klant.

Scan vooraf op inhoud die wordt geüpload of verzonden naar niet-reken resources van Azure, zoals Azure Key Vault. Micro soft heeft geen toegang tot uw gegevens in deze instanties.

Meer informatie over micro soft antimalware voor Azure Cloud Services en Virtual Machines: https://docs.microsoft.com/azure/security/fundamentals/antimalware

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: controleren of anti-malware-software en hand tekeningen zijn bijgewerkt

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources. Micro soft verzorgt anti-malware voor het onderliggende platform.


**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

## <a name="data-recovery"></a>Gegevensherstel

*Zie [beveiligings beheer: gegevens herstel](../../security/benchmarks/security-control-data-recovery.md)voor meer informatie.*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: controleren op regel matige automatische back-ups

**Hulp**: zorg voor regel matige automatische back-ups van uw Key Vault-certificaten, sleutels, beheerde opslag accounts en geheimen, met de volgende Power shell-opdrachten:

- Backup-AzKeyVaultCertificate

- Backup-AzKeyVaultKey

- Backup-AzKeyVaultManagedStorageAccount

- Backup-AzKeyVaultSecret

U kunt eventueel uw Key Vault-back-ups opslaan in Azure Backup.

Het maken van back-ups van Key Vault certificaten: https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultcertificate

Back-ups maken van Key Vault sleutels: https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey

Back-ups maken van Key Vault beheerde opslag accounts: https://docs.microsoft.com/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount

Back-ups maken van Key Vault geheimen: https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultsecret

Azure Backup inschakelen: https://docs.microsoft.com/azure/backup



**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: volledige back-ups van het systeem uitvoeren en een back-up maken van een door de klant beheerde sleutels

**Richt lijnen**: Maak back-ups van uw Key Vault-certificaten, sleutels, beheerde opslag accounts en geheimen, met de volgende Power shell-opdrachten:

- Backup-AzKeyVaultCertificate

- Backup-AzKeyVaultKey

- Backup-AzKeyVaultManagedStorageAccount

- Backup-AzKeyVaultSecret

U kunt eventueel uw Key Vault-back-ups opslaan in Azure Backup.

Het maken van back-ups van Key Vault certificaten: https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultcertificate

Back-ups maken van Key Vault sleutels: https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey

Back-ups maken van Key Vault beheerde opslag accounts: https://docs.microsoft.com/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount

Back-ups maken van Key Vault geheimen: https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultsecret

Azure Backup inschakelen: https://docs.microsoft.com/azure/backup



**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: alle back-ups valideren, inclusief door de klant beheerde sleutels

**Richt lijnen**: regel matig gegevens herstel van uw Key Vault-certificaten, sleutels, beheerde opslag accounts en geheimen uitvoeren met de volgende Power shell-opdrachten:

- Restore-AzKeyVaultCertificate

- Restore-AzKeyVaultKey

- Restore-AzKeyVaultManagedStorageAccount

- Restore-AzKeyVaultSecret

Key Vault certificaten herstellen:  https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultcertificate?view=azurermps-6.13.0

Key Vault sleutels herstellen: https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0 

Key Vault beheerde opslag accounts herstellen: https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultmanagedstorageaccount

Key Vault geheimen herstellen: https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultsecret?view=azurermps-6.13.0


**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: zorg voor de bescherming van back-ups en door de klant beheerde sleutels

**Richt lijnen**: Zorg ervoor dat de functie voor het voorlopig verwijderen van Azure Key Vault is ingeschakeld. Met zacht verwijderen kunt u verwijderde sleutel kluizen en kluis objecten zoals sleutels, geheimen en certificaten herstellen. 

De tijdelijke verwijdering van Azure Key Vault gebruiken: 

https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

## <a name="incident-response"></a>Reageren op incidenten

*Zie voor meer informatie [beveiligings beheer: reactie op incidenten](../../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: een hand leiding voor reactie op incidenten maken

**Richtlijnen**: Stel voor uw organisatie een responshandleiding op voor gebruik bij incidenten. Zorg ervoor dat er schriftelijke responsplannen zijn waarin alle rollen van het personeel worden gedefinieerd, evenals alle fasen in het afhandelen/managen van incidenten, vanaf de detectie van het incident tot een evaluatie ervan achteraf. Deze processen moeten zich richten op het beveiligen van gevoelige systemen, zoals die van Key Vault geheimen.

Werk stroom automatisering configureren in Azure Security Center: 

https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide   

Richt lijnen voor het bouwen van uw eigen beveiligings incident antwoord proces:  

https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Micro soft Security Response Center anatomie van een incident:   

https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process   

De klant kan ook gebruikmaken van de hand leiding voor de verwerking van het computer beveiligings incident van het NIST om hulp te bieden bij het maken van een eigen reactie plan voor incidenten: 

https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: een beoordelings procedure voor incidenten en prioriteits procedures maken

**Hulp**: Security Center wijst aan elke waarschuwing een Ernst toe om u te helpen bepalen welke waarschuwingen het eerst moeten worden onderzocht. De ernst is gebaseerd op de manier waarop vertrouwen Security Center is in de zoek actie of het analyse programma dat wordt gebruikt om de waarschuwing te geven, evenals het betrouwbaarheids niveau dat er schadelijke bedoelingen zijn achter de activiteit die tot de waarschuwing heeft geleid. Daarnaast kunt u ook duidelijk abonnementen markeren (voor bijvoorbeeld productie, niet-productie) en maak een naamgevings systeem om Azure-resources duidelijk te identificeren en te categoriseren, met name voor de verwerking van gevoelige gegevens zoals Azure Key Vault geheimen.


**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="103-test-security-response-procedures"></a>10,3: procedures voor beveiligings antwoorden testen

**Richt lijnen**: oefent oefeningen uit om de incident respons mogelijkheden van uw systeem te testen op een reguliere uitgebracht om uw Azure Key Vault-instanties en gerelateerde resources te beschermen. Stel vast waar zich zwakke plekken en hiaten bevinden, en wijzig zo nodig het plan.

Raadpleeg de publicatie van het NIST: hand leiding voor het testen, trainen en uitoefenen van Program Ma's voor IT-plannen en-mogelijkheden: 

https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: contact gegevens van het beveiligings incident opgeven en waarschuwings meldingen configureren voor beveiligings incidenten

**Hulp**: contact gegevens van beveiligings incidenten worden door micro soft gebruikt om contact met u op te nemen als het micro soft Security Response Center (MSRC) detecteert dat uw gegevens zijn geopend door een onrecht matige of niet-gemachtigde partij.  Bekijk incidenten na het feit om te controleren of de problemen zijn opgelost.

De Azure Security Center Security-contact persoon instellen:

https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: beveiligings waarschuwingen opnemen in uw reactie systeem van uw incident

**Hulp**: exporteer uw Azure Security Center waarschuwingen en aanbevelingen met behulp van de functie continue export om risico's voor Azure Key Vault resources te identificeren. Met doorlopend exporteren kunt u waarschuwingen en aanbevelingen hand matig of op een doorlopende manier exporteren.  U kunt de Azure Security Center Data Connector gebruiken om de waarschuwingen naar Azure Sentinel te streamen. 

 

Continue export configureren: 

https://docs.microsoft.com/azure/security-center/continuous-export 

  

Waarschuwingen streamen naar Azure Sentinel: 

https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: de reactie op beveiligings waarschuwingen automatiseren

**Hulp**: gebruik de functie werk stroom automatisering in azure Security Center om automatisch reacties te activeren via ' Logic apps ' in beveiligings waarschuwingen en aanbevelingen om uw door Azure Key Vault beveiligde bronnen te beveiligen. 

 

Werk stroom automatisering en Logic Apps configureren: 

https://docs.microsoft.com/azure/security-center/workflow-automation

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

## <a name="penetration-tests-and-red-team-exercises"></a>Penetratietests en Red Team-oefeningen

*Zie voor meer informatie [Security Control: Indringings tests en Red team-oefeningen](../../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11,1: voert regel matig indringings tests van uw Azure-resources uit en zorgt voor herstel van alle essentiële beveiligings resultaten binnen 60 dagen

**Richt lijnen**: u kunt het gebruik van de pen niet rechtstreeks uitvoeren op de Azure Key Vault-service, maar het wordt aanbevolen om uw Azure-resources te testen die Key Vault gebruiken om de beveiliging van de geheimen te garanderen.

U moet de micro soft-regels voor betrokkenheid volgen om ervoor te zorgen dat de indringings tests niet worden geschonden door het micro soft-beleid:

https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

U vindt hier meer informatie over de strategie van micro soft en de uitvoering van de Red Teaming-en live site-indringings tests op door micro soft beheerde Cloud infrastructuur,-services en-toepassingen: 

https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Gedeeld

## <a name="next-steps"></a>Volgende stappen

- Zie de [Azure Security-Bench Mark](../../security/benchmarks/overview.md)
- Meer informatie over [Azure-beveiligings basislijnen](../../security/benchmarks/security-baselines-overview.md)