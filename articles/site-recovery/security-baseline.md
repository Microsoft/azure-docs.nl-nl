---
title: Azure-beveiligings basislijn voor Site Recovery
description: De Site Recovery Security Baseline voorziet in procedure richtlijnen en resources voor het implementeren van de beveiligings aanbevelingen die zijn opgegeven in de Azure Security-Bench Mark.
author: msmbaldwin
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/26/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: bf91c0a6e533acb326fe815c3e3c1088c959a603
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100576729"
---
# <a name="azure-security-baseline-for-site-recovery"></a>Azure-beveiligings basislijn voor Site Recovery

In deze beveiligings basislijn worden richt lijnen van de [Azure Security Bench Mark-versie 1,0](../security/benchmarks/overview-v1.md) ingesteld op site Recovery. De Azure Security-benchmark biedt aanbevelingen voor hoe u uw cloudoplossingen in Azure kunt beveiligen. De inhoud wordt gegroepeerd op de **beveiligings controles** die zijn gedefinieerd door de Azure Security-benchmark en de bijbehorende richt lijnen die van toepassing zijn op site Recovery. **Besturings elementen** die niet van toepassing zijn op site Recovery, zijn uitgesloten. 

Als u wilt zien hoe Site Recovery volledig is toegewezen aan de beveiligings benchmark van Azure, raadpleegt u het [volledige site Recovery beveiligings basislijn toewijzings bestand](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Netwerkbeveiliging

*Zie [Azure Security Benchmark: netwerkbeveiliging](../security/benchmarks/security-control-network-security.md) voor meer informatie.*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Azure-resources in virtuele netwerken beveiligen

**Hulp**: Microsoft Azure site Recovery biedt geen ondersteuning voor implementatie in een Azure-Virtual Network. Configureer Site Recovery-service met een persoonlijk Azure-eind punt voor het afdwingen van beveiligde communicatie via uw netwerk.

- [Ondersteuning voor persoonlijke koppelingen Azure Site Recovery](azure-to-azure-how-to-enable-replication-private-endpoints.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: de complexiteit en administratieve overhead van netwerk beveiligings regels minimaliseren

**Hulp**: de site Recovery-service ondersteunt service tags, waarmee klanten verkeer alleen kunnen openen voor specifieke services en poorten. Klanten moeten de service Tags AzureSiteRecovery in hun firewall of netwerk beveiligings groep toestaan om uitgaande toegang tot Site Recovery-service toe te staan.

- [Uitgaande connectiviteit met Service Tags](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="110-document-traffic-configuration-rules"></a>1,10: configuratie regels voor het document verkeer

**Richt lijnen**: gebruik resource Tags voor netwerk beveiligings groepen en andere bronnen die betrekking hebben op netwerk beveiliging en verkeers stroom. Gebruik voor afzonderlijke regels voor de netwerk beveiligings groep het veld Beschrijving om de regels te documenteren die verkeer van en naar een netwerk toestaan. 

Neem een van de ingebouwde Azure Policy definities die betrekking hebben op labelen, zoals ' vereist label en de bijbehorende waarde ', om ervoor te zorgen dat alle resources met tags worden gemaakt en u op de hoogte moet zijn van bestaande niet-gelabelde resources. 

U kunt Azure PowerShell of Azure CLI gebruiken om op basis van hun labels acties op te zoeken of uit te voeren op resources. 

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md) 

- [Een Azure-Virtual Network maken](../virtual-network/quick-create-portal.md) 

- [Netwerk verkeer filteren met regels voor netwerk beveiligings groepen](../virtual-network/tutorial-filter-network-traffic.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: gebruik automatische hulpprogram ma's om netwerk bron configuraties te bewaken en wijzigingen te detecteren

**Richt lijnen**: Bewaak eventuele wijzigingen in netwerk bron configuraties die zijn gerelateerd aan de site Recovery-service met Azure-activiteiten Logboeken. Maak waarschuwingen in Azure Monitor om u te waarschuwen wanneer kritiek Site Recovery netwerk bronnen worden gewijzigd.

- [Activiteiten logboek gebeurtenissen van Azure bekijken en ophalen](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Waarschuwingen voor activiteiten logboek maken, weer geven en beheren met behulp van Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="logging-and-monitoring"></a>Logboekregistratie en bewaking

*Zie [Azure Security Bench Mark: Logging and monitoring](../security/benchmarks/security-control-logging-monitoring.md)(Engelstalig) voor meer informatie.*

### <a name="22-configure-central-security-log-management"></a>2,2: Centraal beveiligings logboek beheer configureren

**Hulp**: Schakel Diagnostische instellingen voor Azure-activiteiten logboek in voor controle logboek registratie en verzend de logboeken naar een log Analytics-werk ruimte, Azure Storage-account of een Azure Event hub voor archivering.

Gebruik Azure-activiteiten logboek gegevens om te bepalen wat het ' wat, wie en wanneer ' voor schrijf bewerkingen (PUT, POST, DELETE) die zijn uitgevoerd op uw Azure-resources.

Opname Site Recovery-Logboeken in Azure Monitor om gegenereerde beveiligings gegevens samen te voegen. In Azure Monitor kunt u Log Analytics-werk ruimten gebruiken om een query uit te voeren en te analyseren en opslag accounts te gebruiken voor lange termijn-of archiverings opslag. Daarnaast kunt u gegevens in-of uitschakelen voor Azure Sentinel of een SIEM-oplossing (Security Incident and Event Management) van derden.

- [Diagnostische instellingen voor Azure-activiteiten logboek inschakelen](../azure-monitor/essentials/activity-log.md)

- [Site Recovery bewaken met Azure Monitor-logboeken](monitor-log-analytics.md)

- [Azure-Sentinel onboarden](../sentinel/quickstart-onboard.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: controle logboek registratie inschakelen voor Azure-resources

**Hulp**: Schakel Diagnostische instellingen voor Azure-activiteiten logboek in voor controle logboek registratie en verzend de logboeken naar een log Analytics-werk ruimte, Azure Storage-account of naar een Azure Event hub voor archivering. 

Gebruik Azure-activiteiten logboek gegevens om te bepalen wat het ' wat, wie en wanneer ' voor schrijf bewerkingen (PUT, POST, DELETE) die zijn uitgevoerd op uw Azure-resources.

Opname Site Recovery-logboeken met Azure Monitor om gegenereerde beveiligings gegevens samen te voegen. In Azure Monitor kunt u Log Analytics-werk ruimten gebruiken om een query uit te voeren en te analyseren en opslag accounts te gebruiken voor lange termijn/archiverings opslag. Gegevens in-en uitschakelen voor Azure Sentinel of een SIEM-oplossing (Security Incident and Event Management) van derden.

- [Diagnostische instellingen voor Azure-activiteiten logboek inschakelen](../azure-monitor/essentials/activity-log.md)

- [Site Recovery bewaken met Azure Monitor-logboeken](monitor-log-analytics.md)

- [Azure-Sentinel onboarden](../sentinel/quickstart-onboard.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="25-configure-security-log-storage-retention"></a>2,5: Bewaar beveiliging van het beveiligings logboek configureren

**Hulp**: Stel de Bewaar periode voor logboek registratie in voor log Analytics werk ruimten die zijn gekoppeld aan uw Azure Recovery Services-kluizen met behulp van Azure monitor op basis van de nalevings voorschriften van uw organisatie. 

- [Para meters voor het bewaren van Logboeken instellen](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="26-monitor-and-review-logs"></a>2,6: Logboeken bewaken en controleren

**Hulp**: Schakel Diagnostische instellingen voor Azure-activiteiten logboek in en verzend de logboeken naar een log Analytics-werk ruimte. 

Voer query's uit in Log Analytics om zoek termen te zoeken, trends te identificeren, patronen te analyseren en inzicht te krijgen in de gegevens van het activiteiten logboek die zijn verzameld van Recovery Services kluizen.

- [Site Recovery bewaken](site-recovery-monitor-and-troubleshoot.md)

- [Diagnostische instellingen voor Azure-activiteiten logboek inschakelen](../azure-monitor/essentials/activity-log.md)

- [Azure-activiteiten logboeken verzamelen en analyseren in Log Analytics werk ruimte in Azure Monitor](../azure-monitor/essentials/activity-log.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: waarschuwingen inschakelen voor afwijkende activiteiten

**Hulp**: computers die door Azure site Recovery worden gerepliceerd, worden bewaakt met behulp van Azure monitor-logboeken en log Analytics. Gebruik Log Analytics binnen Azure Monitor om logboek query's te schrijven en te testen en om logboek gegevens interactief te analyseren. Azure Monitor verzamelt activiteiten en bron logboeken Samen met andere bewakings gegevens. 

Visualiseren en query's uitvoeren op logboek resultaten en waarschuwingen configureren om acties uit te voeren op basis van bewaakte gegevens. Stel waarschuwingen in voor een Log Analytics werk ruimte naar Azure Sentinel, aangezien het een via-oplossing (Security Orchestration Automated Response) biedt. Hierdoor kunnen geautomatiseerde oplossingen, zoals playbooks, worden gemaakt en gebruikt om beveiligings problemen op te lossen. Aangepaste logboek waarschuwingen maken in uw Log Analytics-werk ruimte met behulp van Azure Monitor. 

- [Site Recovery bewaken](site-recovery-monitor-and-troubleshoot.md)

- [Azure-Sentinel onboarden](../sentinel/quickstart-onboard.md)

- [Logboek waarschuwingen maken, weer geven en beheren met behulp van Azure Monitor](../azure-monitor/alerts/alerts-log.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="identity-and-access-control"></a>Identiteits- en toegangsbeheer

*Zie [Azure Security Bench Mark: Identity and Access Control](../security/benchmarks/security-control-identity-access-control.md)voor meer informatie.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: een inventaris van beheerders accounts onderhouden

**Hulp**: standaard worden er geen rollen toegewezen. Ze moeten expliciet worden toegewezen op basis van de bedrijfs behoeften. U kunt eventuele roltoewijzingen controleren met Power shell CLI of Azure Active Directory (Azure AD) om accounts te detecteren die lid zijn van beheer groepen.

- [Een directory-rol verkrijgen in azure AD met Power shell](/powershell/module/azuread/get-azureaddirectoryrole?preserve-view=true&view=azureadps-2.0)

- [Leden van een directory-rol in azure AD ophalen met Power shell](/powershell/module/azuread/get-azureaddirectoryrolemember?preserve-view=true&view=azureadps-2.0)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: speciale beheerders accounts gebruiken

**Richt lijnen**: Maak standaard procedures voor het gebruik van specifieke beheerders accounts. Gebruik de functies voor identiteits-en toegangs beheer van Security Center om het aantal beheerders accounts te controleren.

Om u te helpen bij het bijhouden van specifieke beheerders accounts, kunt u ook aanbevelingen van Security Center of het ingebouwde Azure-beleid gebruiken, zoals: 

- Er moet meer dan één eigenaar zijn toegewezen aan uw abonnement 
- Afgeschafte accounts met eigenaarsmachtigingen moeten worden verwijderd uit uw abonnement 

- Externe accounts met eigenaarsmachtigingen moeten worden verwijderd uit uw abonnement

Maak een proces om identiteits-en toegangs beheer voor beheerders accounts bij te houden en regel matig te controleren.

- [Azure Security Center gebruiken om identiteit en toegang te bewaken](../security-center/security-center-identity-access.md)

- [Azure Policy gebruiken](../governance/policy/tutorials/create-and-manage.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: eenmalige aanmelding (SSO) met Azure Active Directory gebruiken

**Hulp**: Azure-app-registratie met een Service-Principal gebruiken om een token op te halen dat moet worden gebruikt om te communiceren met uw Recovery Services-KLUIZEN via API-aanroepen.

- [Azure REST-Api's aanroepen](/rest/api/azure/#how-to-call-azure-rest-apis-with-postman)

- [Uw client toepassing (Service-Principal) registreren bij Azure AD](/rest/api/azure/#register-your-client-application-with-azure-ad)

- [Informatie over Azure Recovery Services-API](/rest/api/recoveryservices)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: multi-factor Authentication gebruiken voor alle op Azure Active Directory gebaseerde toegang

**Hulp** bij het inschakelen van Azure AD, multi-factor Authentication en het volgen van de aanbevelingen voor identiteit en toegang van Security Center. 
- [Een Azure AD Multi-Factor Authentication-implementatie plannen](../active-directory/authentication/howto-mfa-getstarted.md)

- [Identiteit en toegang bewaken](../security-center/security-center-identity-access.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: gebruik speciale machines (privileged Access workstations) voor alle beheer taken

**Richt lijnen**: gebruik een beveiligd, door Azure beheerd werk station (ook wel een paw (privileged Access Workstation)) met Azure multi-factor Authentication voor beheer taken en om geprivilegieerde acties uit te voeren op site Recovery resources.

- [Privileged Access Workstations](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Planning van een cloudimplementatie van Azure AD Multi-Factor Authentication](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: Logboeken en waarschuwingen voor verdachte activiteiten van beheerders accounts

**Hulp**: gebruik de functie voor het privileged Identity Management van Azure AD (PIM) voor het genereren van Logboeken en waarschuwingen wanneer verdachte of onveilige activiteiten in de omgeving worden uitgevoerd.
Bekijk waarschuwingen en rapporten over Risk ante gebruikers gedrag met de functie voor risico detectie van Azure AD.

- [Privileged Identity Management implementeren (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Meer informatie over Azure AD-risico detectie](../active-directory/identity-protection/overview-identity-protection.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="38-manage-azure-resources-only-from-approved-locations"></a>3,8: beheer Azure-resources alleen op goedgekeurde locaties

**Hulp**: gebruik benoemde locaties voor voorwaardelijke toegang om alleen toegang toe te staan tot de Azure Portal vanuit specifieke logische groepen met IP-adresbereiken, regio's of landen.
- [Benoemde locaties configureren in azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="39-use-azure-active-directory"></a>3,9: Azure Active Directory gebruiken

**Richt lijnen**: Azure AD gebruiken als het centrale verificatie-en autorisatie systeem voor site Recovery. Azure AD beveiligt gegevens door gebruik te maken van sterke versleuteling voor Data-at-rest, transitieve, ook zouten, hashes en veilige gebruikers referenties. 

- [Een Azure AD-instantie maken en configureren](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: regel matig gebruikers toegang controleren en afstemmen

**Hulp: Azure** AD-Logboeken gebruiken om verouderde accounts te detecteren. 

Beheer groepslid maatschappen en de toegang tot bedrijfs toepassingen en roltoewijzingen met de identiteits-en toegangs beoordelingen van Azure AD. 

Maak een proces om gebruikers toegang regel matig te controleren om ervoor te zorgen dat alleen gebruikers met voltooide toegangs beoordelingen de toegang blijven hebben. 

- [Meer informatie over Azure AD-rapportage](../active-directory/reports-monitoring/index.yml)

- [Beoordelingen over Azure Identity Access gebruiken](../active-directory/governance/access-reviews-overview.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: controle pogingen om toegang te krijgen tot gedeactiveerde referenties

**Hulp**: Azure AD gebruiken als het centrale verificatie-en autorisatie systeem voor site Recovery resources. Azure AD beveiligt gegevens door gebruik te maken van sterke code ring voor gegevens in rust en onderweg, met zouten, hashes en het veilig opslaan van gebruikers referenties.

U hebt toegang tot de logboeken van Azure AD-aanmeldings activiteiten, controle en risico bronnen, waarmee u ze kunt integreren met Azure Sentinel of een SIEM-of bewakings programma dat beschikbaar is op Azure Marketplace.

U kunt dit proces verder stroom lijnen door Diagnostische instellingen voor Azure AD-gebruikers accounts te maken en de controle-en aanmeldings logboeken te verzenden naar een Log Analytics-werk ruimte. U kunt de gewenste waarschuwingen configureren in een Log Analytics-werk ruimte.

- [Azure-activiteitenlogboeken integreren in Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Azure-Sentinel aan de trein](../sentinel/quickstart-onboard.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: waarschuwing voor de afwijking van het aanmeldings gedrag van accounts

**Hulp**: Azure AD gebruiken als het centrale verificatie-en autorisatie systeem voor uw Recovery Services-kluizen. 

Gebruik de functies van de identiteits beveiliging van Azure AD voor het detecteren van account aanmeld gedrag en voor het configureren van automatische antwoorden op gedetecteerde verdachte acties, met betrekking tot gebruikers identiteiten. Neem ook gegevens op in azure Sentinel voor verder onderzoek.

- [Hoe kan ik een Risk ante aanmelding van Azure AD weer geven?](../active-directory/identity-protection/overview-identity-protection.md)

- [Risico beleid voor identiteits beveiliging configureren en inschakelen](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Azure-Sentinel onboarden](../sentinel/quickstart-onboard.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="data-protection"></a>Gegevensbeveiliging

*Zie [Azure Security Benchmark: gegevensbescherming](../security/benchmarks/security-control-data-protection.md) voor meer informatie.*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: een inventaris van gevoelige informatie onderhouden

**Hulp**: Tags gebruiken om Azure-resources te helpen bij het bijhouden of verwerken van gevoelige informatie.

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: systemen isoleren die gevoelige informatie opslaan of verwerken

**Richt lijnen**: afzonderlijke abonnementen of beheer groepen implementeren voor ontwikkelings-, test-en productie Recovery Services kluizen. Scheid resources met een virtueel netwerk of subnet, op de juiste wijze en beveiligd door een netwerk beveiligings groep of Azure Firewall. 

Schakel de virtuele machines uit, waarmee gevoelige gegevens worden opgeslagen of verwerkt, wanneer deze niet worden gebruikt. Implementeer beleid en procedures om dit een terugkerend proces te maken. 

- [Aanvullende Azure-abonnementen maken](../cost-management-billing/manage/create-subscription.md)

- [Beheergroepen maken](../governance/management-groups/create-management-group-portal.md)

- [Overzicht van Site Recovery](site-recovery-overview.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: niet-geautoriseerde overdracht van gevoelige gegevens controleren en blok keren

**Richt lijnen**: gebruik een persoonlijke koppeling of een persoonlijk eind punt, netwerk beveiligings groepen en service tags om eventuele kansen te beperken voor gegevens exfiltration van de site Recovery ingeschakelde virtuele machines.

Micro soft beheert het onderliggende platform dat wordt gebruikt door Site Recovery en behandelt alle inhoud van de klant als gevoelig en beschermd tegen verlies en bloot stelling van klant gegevens. Micro soft heeft een reeks robuuste besturings elementen voor gegevens bescherming geïmplementeerd en onderhouden om ervoor te zorgen dat klant gegevens in azure beveiligd blijven. 

- [Informatie over beveiliging van klantgegevens in Azure](../security/fundamentals/protection-customer-data.md)

- [Virtuele machines repliceren met persoonlijke Azure-eind punten](azure-to-azure-how-to-enable-replication-private-endpoints.md)

- [Virtuele machines repliceren met Azure Site Recovery-service Tags](azure-to-azure-about-networking.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Gedeeld

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: alle gevoelige gegevens in de overdracht versleutelen

**Hulp**: site Recovery maakt gebruik van een beveiligd https-kanaal, versleuteld met behulp van Advanced Encryption Standard (AES 256), van Azure-werkbelasting servers tot site Recovery Services die worden gehost achter een Recovery Services kluis.

De huidige TLS-versies die worden ondersteund voor Site Recovery zijn TLS 1,0, TLS 1,1, TLS 1,2 in regio's, die aan het eind van 2019 Live waren. TLS 1.2 is de enige TLS-versie die wordt ondersteund voor nieuwe regio's.

- [Informatie over versleuteling in transit voor Azure Site Recovery](physical-azure-set-up-source.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Gedeeld

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: een actief detectie hulpprogramma gebruiken om gevoelige gegevens te identificeren

**Hulp**: de functies gegevens identificatie, classificatie en verlies preventie zijn nog niet beschikbaar voor site Recovery. 

Implementeer, indien nodig, een oplossing van derden, voor nalevings doeleinden.

Micro soft beheert het onderliggende platform dat wordt gebruikt door Site Recovery en behandelt alle inhoud van de klant als gevoelig en bescherming tegen verlies en bloot stelling van klant gegevens. Het is geïmplementeerd en onderhoudt een reeks robuuste besturings elementen voor gegevens bescherming en biedt de mogelijkheid om klant gegevens in azure veilig te houden. 

- [Informatie over beveiliging van klantgegevens in Azure](../security/fundamentals/protection-customer-data.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4,6: Azure RBAC gebruiken om de toegang tot resources te beheren

**Richt lijnen**: gebruik Azure RBAC (op rollen gebaseerd toegangs beheer) voor het beheren van de toegang tot gegevens en resources die betrekking hebben op site Recovery resources. 

Afzonderlijke werk belastingen met Azure RBAC en de juiste toegang verlenen. Gebruik de ingebouwde Site Recovery rollen om Site Recovery beheer bewerkingen te beheren.

- [Azure RBAC configureren](../role-based-access-control/role-assignments-portal.md)

- [Role-Based Access Control gebruiken voor het beheren van Azure Site Recovery](site-recovery-role-based-linked-access-control.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: gevoelige informatie op rest versleutelen

**Richt lijnen**: dubbele versleuteling met door het platform en door de klant beheerde sleutels inschakelen. Deze mogelijkheid is beschikbaar in Site Recovery. 

Site Recovery ondersteunt versleuteling op rest voor gegevens. Voor Azure IaaS-workloads worden gegevens versleuteld op rest met behulp van Storage Service Encryption (SSE). 

Alleen de klant heeft toegang tot de versleutelings sleutel tijdens het gebruik van een Recovery Services kluis die is versleuteld met een door de klant beheerde sleutel. Micro soft onderhoudt nooit een kopie, heeft geen toegang tot de sleutel en ontsleutelt de gegevens die zijn overgedragen van de primaire naar een nood herstel locatie op elk gewenst moment. 

- [Ondersteuning voor Azure Site Recovery voor door de klant beheerde sleutels](azure-to-azure-how-to-enable-replication-cmk-disks.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Gedeeld

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: wijzigingen in essentiële Azure-resources vastleggen en waarschuwen

**Hulp**: gebruik Azure monitor met Azure-activiteiten Logboeken om waarschuwingen te maken wanneer wijzigingen worden aangebracht in essentiële resources,. Deze resources kunnen productie-exemplaren van Recovery Services kluizen, resources van Site Recovery service en gerelateerde resources omvatten.
- [Waarschuwingen maken voor gebeurtenissen in het Azure-activiteiten logboek](../azure-monitor/alerts/alerts-activity-log.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="inventory-and-asset-management"></a>Inventarisatie en Asset Management

*Zie [Azure Security Bench Mark: Inventory and Asset Management](../security/benchmarks/security-control-inventory-asset-management.md)voor meer informatie.*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: automatische Asset-detectie oplossing gebruiken

**Richt lijnen**: gebruik Azure resource Graph om alle resources, inclusief Recovery Services kluizen, in uw abonnementen op te vragen of te detecteren. Zorg ervoor dat de juiste lees machtigingen voor uw Tenant en alle Azure-abonnementen en resources in uw abonnementen opsommen.

Hoewel klassieke Azure-resources kunnen worden gedetecteerd via resource grafiek, is het raadzaam om Azure Resource Manager resources te maken en te gebruiken.

- [Query's maken met Azure Graph](../governance/resource-graph/first-query-portal.md)

- [Uw Azure-abonnementen weer geven](/powershell/module/az.accounts/get-azsubscription?preserve-view=true&view=azps-4.8.0)

- [Meer informatie over Azure RBAC](../role-based-access-control/overview.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="62-maintain-asset-metadata"></a>6,2: meta gegevens van activa onderhouden

**Richt lijnen**: Tags Toep assen op Recovery Services kluizen en andere gerelateerde resources, gebruikt door site Recovery met meta gegevens, om ze logisch in een taxonomie te organiseren.
- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: niet-geautoriseerde Azure-resources verwijderen

**Richt lijnen**: Gebruik labels, beheer groepen en afzonderlijke abonnementen, indien van toepassing, om Site Recovery (Recovery Services kluizen) en andere gerelateerde resources te organiseren en bij te houden. 

Gebruik Azure Policy bovendien om beperkingen te leggen voor het type resources dat kan worden gemaakt in klant abonnementen met behulp van de volgende ingebouwde beleids definities: 

- Niet toegestane resourcetypen
- Toegestane brontypen

Sluit de inventaris regel matig af en zorg ervoor dat niet-geautoriseerde resources tijdig worden verwijderd uit het abonnement.

- [Aanvullende Azure-abonnementen maken](../cost-management-billing/manage/create-subscription.md)

- [Beheergroepen maken](../governance/management-groups/create-management-group-portal.md)

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6,4: een inventaris van goedgekeurde Azure-resources definiëren en onderhouden

**Richt lijnen**: Maak een inventaris van goedgekeurde Azure-resources en goedgekeurde software voor reken resources op basis van de organisatie vereisten van de klant.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: monitor voor niet-goedgekeurde Azure-resources

**Hulp: gebruik** Azure Policy om beperkingen toe te voegen voor het type resources dat kan worden gemaakt in klant abonnementen met behulp van de volgende ingebouwde beleids definities: 

- Niet toegestane resourcetypen 
- Toegestane brontypen

Gebruik Azure resource Graph om bronnen in de abonnementen op te vragen en te detecteren.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Query's maken met Azure resource Graph](../governance/resource-graph/first-query-portal.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="69-use-only-approved-azure-services"></a>6,9: alleen goedgekeurde Azure-Services gebruiken

**Hulp: gebruik** Azure Policy om beperkingen toe te voegen voor het type resources dat kan worden gemaakt in klant abonnementen met behulp van de volgende ingebouwde beleids definities:

- Niet toegestane resourcetypen 
- Toegestane brontypen

Als u aan uw bedrijfsnormen en serviceovereenkomsten wilt blijven voldoen, is het belangrijk dat u begrijpt hoe u beleidsregels kunt maken en beheren in Azure.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Een specifiek resource type weigeren met Azure Policy](../governance/policy/samples/index.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: de mogelijkheid van gebruikers om te communiceren met Azure Resource Manager beperken

**Richt lijnen**: gebruik de voorwaardelijke toegang van Azure om gebruikers de mogelijkheid te bieden om te communiceren met Azure Resource Manager door ' blok toegang ' te configureren voor de app Microsoft Azure management. Dit kan ertoe leiden dat het maken en wijzigen van bronnen binnen een omgeving met hoge beveiliging wordt voor komen.

- [Voorwaardelijke toegang configureren om de toegang tot Azure Resource Manager te blok keren](../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="secure-configuration"></a>Veilige configuratie

*Zie [Azure Security Bench Mark: Secure Configuration](../security/benchmarks/security-control-secure-configuration.md)(Engelstalig) voor meer informatie.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: veilige configuraties instellen voor alle Azure-resources

**Hulp**: Definieer en implementeer standaard beveiligings configuraties voor uw Recovery Services kluis met Azure Policy. 

Gebruik Azure Policy aliassen in de naam ruimte ' micro soft. Recovery Services ' om aangepaste beleids regels te maken om te controleren of de configuratie van de Recovery Services-kluis resources van Site Recovery service af te dwingen.
- [Beschik bare Azure Policy aliassen weer geven](/powershell/module/az.resources/get-azpolicyalias?preserve-view=true&view=azps-4.8.0)

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: Beveilig Azure-resource configuraties onderhouden

**Hulp**: gebruik Azure Policy [deny] en [indien niet aanwezig] effecten om beveiligde instellingen af te dwingen voor uw Azure-resources.
- [Azure Policy effecten begrijpen](../governance/policy/concepts/effects.md)

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: de configuratie van Azure-resources veilig opslaan

**Hulp**: Kies Azure opslag plaatsen om uw code veilig op te slaan en te beheren als u aangepaste Azure Policy definities voor uw Recovery Services kluizen en gerelateerde resources gebruikt.

- [Code opslaan in azure DevOps](/azure/devops/repos/git/gitworkflow?preserve-view=true&view=azure-devops)

- [Documentatie voor Azure opslag plaatsen](/azure/devops/repos/?preserve-view=true&view=azure-devops)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: hulpprogram ma's voor configuratie beheer voor Azure-resources implementeren

**Hulp**: gebruik ingebouwde Azure Policy definities en Azure Policy aliassen in de naam ruimte ' micro soft. Recovery Services ' om aangepaste beleids regels te maken om systeem configuraties te Signa lering, te controleren en af te dwingen. 

Ontwikkel bovendien een proces en pijp lijn voor het beheren van beleids uitzonderingen.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: geautomatiseerde configuratie bewaking voor Azure-resources implementeren

**Hulp**: gebruik ingebouwde Azure Policy definities en Azure Policy aliassen in de naam ruimte ' micro soft. Recovery Services ' om aangepaste beleids regels te maken om systeem configuraties te Signa lering, te controleren en af te dwingen. 

Gebruik Azure Policy [audit], [deny] en [implementeren indien niet aanwezig] effecten om automatisch configuraties voor uw Azure-resources af te dwingen.
- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="711-manage-azure-secrets-securely"></a>7,11: Azure-geheimen veilig beheren

**Hulp**: de klant moet site Recovery geheimen beheren die zijn geïntegreerd met Azure Key kluis, terwijl herstel na nood geval wordt ingeschakeld voor virtuele machines met Azure Disk Encryption. 

- [Een sleutel kluis maken](../key-vault/general/quick-create-portal.md)

- [Verificatie bij sleutel kluis](../key-vault/general/authentication.md)

- [Een sleutel kluis toegangs beleid toewijzen](../key-vault/general/assign-access-policy-portal.md)

- [Hoe kan ik DR inschakelen voor virtuele machines met Azure Disk Encryption ingeschakeld met behulp van Site Recovery](azure-to-azure-how-to-enable-replication-ade-vms.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: identiteiten veilig en automatisch beheren

**Hulp**: site Recovery ondersteunt alleen door een systeem beheerde identiteit als een klant door het systeem beheerde identiteit kan inschakelen op Recovery Services kluis. Deze methode is van toepassing op bronnen die worden gebruikt in de aanbieding voor nood herstel om de toegangs grens te definiëren. 

Gebruik beheerde identiteiten om Azure-Services te voorzien van een automatisch beheerde identiteit in azure AD. 

Met beheerde identiteiten kunt u zich verifiëren bij elke service die ondersteuning biedt voor Azure AD-verificatie, met inbegrip van Key Vault, zonder dat u referenties in uw code hoeft op te geven.

- [Integratie met door Azure beheerde identiteiten](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md?tabs=core2x)

- [Door systeem beheerde identiteit inschakelen op Recovery Services kluis](azure-to-azure-how-to-enable-replication-private-endpoints.md#enable-the-managed-identity-for-the-vault)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: onbedoelde referentie blootstelling elimineren

**Richt lijnen**: referentie scanner implementeren om referenties in code te identificeren. Door het gebruik van Credential Scanner worden gebruikers ook aangemoedigd om gedetecteerde referenties naar veiligere locaties, zoals Azure Key Vault, te verplaatsen.

- [Referentie scanner instellen](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="malware-defense"></a>Beveiliging tegen malware

*Zie voor meer informatie de [Azure Security Bench Mark: beveiliging tegen schadelijke software](../security/benchmarks/security-control-malware-defense.md).*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: scan bestanden die moeten worden geüpload naar niet-reken resources van Azure

**Hulp**: micro soft antimalware is ingeschakeld op de onderliggende host die ondersteuning biedt voor Azure-Services (bijvoorbeeld site Recovery), maar wordt niet uitgevoerd voor uw inhoud. Scan vooraf bestanden die worden geüpload naar niet-reken resources van Azure, zoals App Service, Data Lake Storage en Blob Storage.

Gebruik Security Center bedreigings detectie voor gegevens Services om malware te detecteren die is geüpload naar opslag accounts.

- [Micro soft antimalware voor Azure Cloud Services en Virtual Machines begrijpen](../security/fundamentals/antimalware.md)

- [Meer informatie over de detectie van bedreigingen van Azure Security Center voor gegevens Services](../security-center/azure-defender.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="data-recovery"></a>Gegevensherstel

*Zie [Azure Security Bench Mark: Data Recovery](../security/benchmarks/security-control-data-recovery.md)(Engelstalig) voor meer informatie.*

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: volledige back-ups van het systeem uitvoeren en een back-up maken van door de klant beheerde sleutels

**Hulp**: site Recovery intern een Azure Storage-account gebruikt om de status van de oplossing voor herstel na nood gevallen te onderhouden, zoals geconfigureerd door klanten op hun werk belastingen.

Alle opslag resources die worden gebruikt door Site Recovery Services-meta gegevens met de configuratie van type: geografisch redundante opslag met lees toegang (RA-GRS). Opslag accounts van het type hierboven GRS (zoals RAGRS, RAG-ZRS) repliceren uw gegevens naar een secundaire regio (honderden kilo meters van de primaire locatie van de bron gegevens) om het herstel na nood gevallen voor klanten te blijven uitvoeren tijdens storingen.

Dit valt buiten het bereik van de klant en Site Recovery team is het intern. De klant kan een back-up maken van Key Vault sleutels in Azure.

- [Hoe kan ik een back-up maken van sleutel kluis sleutels in azure?](/powershell/module/az.keyvault/backup-azkeyvaultkey?preserve-view=true&view=azps-4.8.0)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: alle back-ups valideren, inclusief door de klant beheerde sleutels

**Richt lijnen**: test het periodiek herstellen van back-ups van door de klant beheerde sleutels.

- [Sleutel kluis sleutels herstellen in azure](/powershell/module/az.keyvault/restore-azkeyvaultkey?preserve-view=true&view=azps-4.8.0)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: zorg voor de bescherming van back-ups en door de klant beheerde sleutels

**Richt lijnen**: gegevens worden versleuteld met behulp van Storage service Encryption (SSE) met de IaaS-virtual machines (Infrastructure as a Service) van Azure. Schakel zacht verwijderen in Key Vault in om sleutels te beschermen tegen onbedoelde of schadelijke verwijdering.

- [Zacht verwijderen inschakelen in Key Vault](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

## <a name="incident-response"></a>Reageren op incidenten

*Zie [Azure Security Benchmark: respons op incidenten](../security/benchmarks/security-control-incident-response.md) voor meer informatie.*

### <a name="101-create-an-incident-response-guide"></a>10,1: een hand leiding voor reactie op incidenten maken

**Richtlijnen**: Stel voor uw organisatie een responshandleiding op voor gebruik bij incidenten. 

Zorg ervoor dat er schriftelijke incidenten abonnementen zijn die alle werk rollen definiëren, evenals de fasen van incident handling of het beheer van de detectie tot een beoordeling van het incident.

- [Werk stroom automatisering configureren in Azure Security Center](../security-center/security-center-planning-and-operations-guide.md)

- [Richt lijnen voor het bouwen van uw eigen beveiligings incident antwoord proces](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process)

- [Micro soft Security Response Center anatomie van een incident](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process)

- [Klant kan ook gebruikmaken van de hand leiding voor de verwerking van het computer beveiligings incident van het NIST om hulp te bieden bij het maken van een eigen incident respons plan](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: een beoordelings procedure voor incidenten en prioriteits procedures maken

**Richt lijnen**: prioriteiten bepalen welke waarschuwingen eerst moeten worden onderzocht op basis van de toegewezen waarschuwing ernst van Security Center. De ernst is gebaseerd op de manier waarop vertrouwen Security Center is in de zoek actie of het analyse programma dat wordt gebruikt om de waarschuwing te geven, evenals het betrouwbaarheids niveau dat er schadelijke bedoelingen zijn achter de activiteit die tot de waarschuwing heeft geleid.

Markeer abonnementen duidelijk (bijvoorbeeld productie, niet-productie) en maak een naamgevings systeem om Azure-resources duidelijk te identificeren en te categoriseren.

- [Beveiligingswaarschuwingen in Azure Security Center](../security-center/security-center-alerts-overview.md) 

- [Tags gebruiken om Azure-resources te organiseren](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="103-test-security-response-procedures"></a>10,3: procedures voor beveiligings antwoorden testen

**Richt lijnen**: oefent oefeningen uit om de respons mogelijkheden van uw systeem op een gewone uitgebracht te testen. Identificeren van zwakke punten en leemten en het plan indien nodig herzien

- [Raadpleeg de publicatie handleiding van het NIST voor het testen, trainen en uitoefenen van Program Ma's voor IT-plannen en-mogelijkheden](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: contact gegevens van het beveiligings incident opgeven en waarschuwings meldingen configureren voor beveiligings incidenten

**Hulp**: contact gegevens van beveiligings incidenten worden door micro soft gebruikt om contact met u op te nemen als het micro soft Security Response Center (MSRC) detecteert dat de gegevens van de klant zijn geopend door een onrecht matige of niet-gemachtigde partij. 

Maak een proces voor het controleren van incidenten, het plaatsen van een exemplaar om ervoor te zorgen dat problemen worden opgelost.

- [De Azure Security Center Security-contact persoon instellen](../security-center/security-center-provide-security-contact-details.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: beveiligings waarschuwingen opnemen in uw reactie systeem van uw incident

**Richt lijnen**: uw Security Center waarschuwingen en aanbevelingen exporteren met de functie continue export. Met doorlopend exporteren kunt u waarschuwingen en aanbevelingen hand matig of op een doorlopende manier exporteren. 

Gebruik de Security Center Data Connector om de waarschuwingen naar behoefte te streamen naar Azure Sentinel.
- [Continue export configureren](../security-center/continuous-export.md)

- [Waarschuwingen streamen naar Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: de reactie op beveiligings waarschuwingen automatiseren

**Hulp**: gebruik de functie werk stroom automatisering in Security Center om automatisch reacties te activeren via ' Logic apps ' in beveiligings waarschuwingen en aanbevelingen.
- [Werk stroom automatisering en Logic Apps configureren](../security-center/workflow-automation.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="penetration-tests-and-red-team-exercises"></a>Penetratietests en Red Team-oefeningen

*Zie [Azure Security Bench Mark: Indringings tests en rode team oefeningen](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)voor meer informatie.*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: voert regel matig indringings tests van uw Azure-resources uit en zorgt voor herbemiddeling van alle essentiële beveiligings resultaten

**Richt lijnen**: Volg de micro soft-regels om ervoor te zorgen dat de indringings tests niet worden geschonden door het micro soft-beleid: https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

- [U vindt hier meer informatie over de strategie van micro soft en de uitvoering van Red Teaming en live site indringings tests met door micro soft beheerde Cloud infrastructuur,-services en-toepassingen.](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Gedeeld

## <a name="next-steps"></a>Volgende stappen

- Zie [Overzicht Azure Security Benchmark V2](../security/benchmarks/overview.md)
- Meer informatie over [Azure-beveiligingsbasislijnen](../security/benchmarks/security-baselines-overview.md)