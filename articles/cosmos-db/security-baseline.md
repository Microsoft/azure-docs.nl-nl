---
title: Azure-beveiligings basislijn voor Cosmos DB
description: Azure-beveiligings basislijn voor Cosmos DB
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 67eece36444f06fed5fc81cd23b9ea284b43ec7c
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98202348"
---
# <a name="azure-security-baseline-for-cosmos-db"></a>Azure-beveiligings basislijn voor Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

De Azure-beveiligings basislijn voor Cosmos DB bevat aanbevelingen waarmee u de beveiligings postuur van uw implementatie kunt verbeteren.

De basis lijn voor deze service wordt opgehaald uit de [Azure Security Bench Mark-versie 1,0](../security/benchmarks/overview.md), die aanbevelingen biedt over hoe u uw cloud oplossingen kunt beveiligen in azure met onze richt lijnen voor best practices.

Zie [overzicht van Azure Security-basis lijnen](../security/benchmarks/security-baselines-overview.md)voor meer informatie.

## <a name="network-security"></a>Netwerkbeveiliging

*Zie [beveiligings beheer: netwerk beveiliging](../security/benchmarks/security-control-network-security.md)voor meer informatie.*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1,1: Beveilig bronnen met behulp van netwerk beveiligings groepen of Azure Firewall op de Virtual Network

**Richt lijnen**: met behulp van een persoonlijke Azure-koppeling kunt u verbinding maken met een Azure Cosmos-account via een persoonlijk eind punt. Verkeer tussen uw virtuele netwerk en de services wordt via het backbonenetwerk van Microsoft geleid, waarmee de risico's van het openbare internet worden vermeden. U kunt ook het risico van gegevens exfiltration verminderen door een strikte set uitgaande regels te configureren in een netwerk beveiligings groep (NSG) en die NSG te koppelen aan uw subnet.

U kunt ook service-eind punten gebruiken om uw Azure Cosmos-account te beveiligen. Door een service-eind punt in te scha kelen, kunt u Azure Cosmos-accounts configureren om alleen toegang toe te staan vanaf een specifiek subnet van een virtueel Azure-netwerk. Zodra het eind punt van de Azure Cosmos DB service is ingeschakeld, kunt u de toegang tot een Azure Cosmos-account beperken met verbindingen van een subnet in een virtueel netwerk.

U kunt ook de gegevens beveiligen die zijn opgeslagen in uw Azure Cosmos-account door gebruik te maken van IP-firewalls. Azure Cosmos DB ondersteunt toegangs beheer op basis van IP voor binnenkomende firewall ondersteuning. U kunt een IP-firewall instellen voor het Azure Cosmos-account met behulp van de Azure Portal, Azure Resource Manager sjablonen of via de Azure CLI of Azure PowerShell.

Overzicht van persoonlijke Azure-koppelingen: https://docs.microsoft.com/azure/private-link/private-link-overview

Een persoonlijk eind punt configureren voor Azure Cosmos DB:  https://docs.microsoft.com/azure/cosmos-db/how-to-configure-private-endpoints 

Een netwerk beveiligings groep maken met een beveiligings configuratie:  https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

IP-Firewall configureren in Cosmos DB: https://docs.microsoft.com/azure/cosmos-db/how-to-configure-firewall

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1,2: de configuratie en het verkeer van Vnets, subnetten en Nic's bewaken en vastleggen

**Hulp**: gebruik Azure Security Center en volg aanbevelingen voor netwerk beveiliging om netwerk bronnen te beveiligen die betrekking hebben op uw Azure Cosmos-account.

Wanneer virtuele machines worden geïmplementeerd in hetzelfde virtuele netwerk als uw Azure Cosmos-account, kunt u een netwerk beveiligings groep (NSG) gebruiken om het risico van gegevens exfiltration te verminderen. Schakel logboeken voor NSG-stroom in en verzend logboeken naar een Azure Storage account voor verkeers controles. U kunt ook NSG-stroom logboeken naar een Log Analytics-werk ruimte verzenden en Traffic Analytics gebruiken om inzicht te krijgen in de verkeers stroom in uw Azure-Cloud. Enkele voor delen van Traffic Analytics zijn de mogelijkheid om netwerk activiteiten te visualiseren en HOTS pots te identificeren, beveiligings dreigingen te identificeren, verkeers patronen te begrijpen en netwerk configuraties te lokaliseren.

Informatie over netwerk beveiliging van Azure Security Center: https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

NSG-stroom logboeken inschakelen: https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Traffic Analytics inschakelen en gebruiken: https://docs.microsoft.com/azure/network-watcher/traffic-analytics

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="13-protect-critical-web-applications"></a>1,3: essentiële webtoepassingen beveiligen

**Richt lijnen**: gebruik de functie voor cross-Origin resource SHARING (CORS) om een webtoepassing in te scha kelen die onder het ene domein wordt uitgevoerd om toegang te krijgen tot bronnen in een ander domein. Webbrowsers implementeren een beveiligings beperking die bekend staat als hetzelfde-Origin-beleid dat voor komt dat een webpagina Api's in een ander domein aanroept. CORS biedt echter een veilige manier om het domein van de oorsprong te laten aanroepen van Api's in een ander domein. Nadat u de CORS-ondersteuning voor uw Azure Cosmos-account hebt ingeschakeld, worden alleen geverifieerde aanvragen geëvalueerd om te bepalen of ze zijn toegestaan volgens de regels die u hebt opgegeven.

Cross-Origin-resource delen configureren: https://docs.microsoft.com/azure/cosmos-db/how-to-configure-cross-origin-resource-sharing

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: communicatie met bekende schadelijke IP-adressen weigeren

**Hulp**: gebruik Advanced Threat Protection (ATP) voor Azure Cosmos db. ATP voor Azure Cosmos DB biedt een extra beveiligingslaag waarmee ongebruikelijke en mogelijk schadelijke pogingen voor het openen of exploiteren van Azure Cosmos-accounts worden gedetecteerd. Met deze beveiligingslaag kunt u bedreigingen aanpakken en ze integreren met centrale beveiligings bewakings systemen.

Schakel DDoS Protection standaard in op de virtuele netwerken die zijn gekoppeld aan uw Azure Cosmos DB-instanties om te beschermen tegen DDoS-aanvallen. Gebruik Azure Security Center geïntegreerde bedreigings informatie om communicatie met bekende of ongebruikte Internet-IP-adressen te weigeren.

Azure Cosmos DB Advanced Threat Protection configureren: https://docs.microsoft.com/azure/cosmos-db/cosmos-db-advanced-threat-protection

DDoS-beveiliging configureren: https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Meer informatie over Azure Security Center geïntegreerde bedreigings informatie: https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="15-record-network-packets-and-flow-logs"></a>1,5: netwerk pakketten en stroom logboeken vastleggen

**Hulp**: Schakel logboeken voor netwerk beveiligings groepen (NSG) in en verzend logboeken naar een opslag account voor verkeers controle. U kunt NSG-stroom logboeken naar een Log Analytics-werk ruimte verzenden en Traffic Analytics gebruiken om inzicht te krijgen in de verkeers stroom in uw Azure-Cloud. Enkele voor delen van Traffic Analytics zijn de mogelijkheid om netwerk activiteiten te visualiseren en HOTS pots te identificeren, beveiligings dreigingen te identificeren, verkeers patronen te begrijpen en netwerk configuraties te lokaliseren.

NSG-stroom logboeken inschakelen: https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Traffic Analytics inschakelen en gebruiken: https://docs.microsoft.com/azure/network-watcher/traffic-analytics

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: op netwerk gebaseerde inbreuk detectie/indringings systemen (ID'S/IP-adressen) implementeren

**Hulp**: gebruik Advanced Threat Protection (ATP) voor Azure Cosmos db. ATP voor Azure Cosmos DB biedt een extra beveiligingslaag waarmee ongebruikelijke en mogelijk schadelijke pogingen voor het openen of exploiteren van Azure Cosmos-accounts worden gedetecteerd. Met deze beveiligingslaag kunt u bedreigingen aanpakken en ze integreren met centrale beveiligings bewakings systemen. 

Cosmos DB Advanced Threat Protection configureren: https://docs.microsoft.com/azure/cosmos-db/cosmos-db-advanced-threat-protection

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="17-manage-traffic-to-web-applications"></a>1,7: verkeer naar webtoepassingen beheren

**Richt lijnen**: niet van toepassing; aanbeveling is bedoeld voor webtoepassingen die worden uitgevoerd op Azure App Service of reken bronnen.


**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: de complexiteit en administratieve overhead van netwerk beveiligings regels minimaliseren

**Richt lijnen**: voor bronnen die toegang nodig hebben tot uw Azure Cosmos-account, gebruikt u Virtual Network Service Tags om netwerk toegangs beheer te definiëren voor netwerk beveiligings groepen of Azure firewall. U kunt servicetags gebruiken in plaats van specifieke IP-adressen wanneer u beveiligingsregels maakt. Door de naam van de service label (bijvoorbeeld AzureCosmosDB) op te geven in het juiste bron-of doel veld van een regel, kunt u het verkeer voor de bijbehorende service toestaan of weigeren. Micro soft beheert de adres voorvoegsels die zijn opgenomen in het servicetag van de service en werkt de servicetag automatisch bij met gewijzigde adressen.

Meer informatie over het gebruik van service Tags: https://docs.microsoft.com/azure/virtual-network/service-tags-overview

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: standaard beveiligings configuraties voor netwerk apparaten onderhouden

**Richt lijnen**: standaard beveiligings configuraties voor netwerk bronnen definiëren en implementeren met Azure Policy. Gebruik Azure Policy aliassen in de naam ruimten ' Microsoft.DocumentDB ' en ' micro soft. Network ' om aangepaste beleids regels te maken om de netwerk configuratie van uw Azure Cosmos DB instanties te controleren of af te dwingen. U kunt ook gebruik maken van ingebouwde beleids definities voor Azure Cosmos DB, zoals:

- Advanced Threat Protection implementeren voor Cosmos DB-accounts

- Cosmos DB moet gebruikmaken van een service-eindpunt voor een virtueel netwerk

U kunt ook Azure-blauw drukken gebruiken om grootschalige Azure-implementaties te vereenvoudigen door sleutel omgevings artefacten, zoals Azure Resource Manager sjablonen, Toegangs beheer op basis van rollen (Azure RBAC) en beleids regels in één blauw definitie te verpakken. U kunt de blauw druk eenvoudig Toep assen op nieuwe abonnementen, omgevingen en het beheer en de verwerkings mogelijkheden van versies.

Azure Policy configureren en beheren: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Een Azure Blueprint maken: https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="110-document-traffic-configuration-rules"></a>1,10: configuratie regels voor het document verkeer

**Richt lijnen**: Gebruik labels voor netwerk bronnen die zijn gekoppeld aan uw Azure Cosmos DB-implementatie om ze logisch in een taxonomie te organiseren.

Tags maken en gebruiken: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: gebruik automatische hulpprogram ma's om netwerk bron configuraties te bewaken en wijzigingen te detecteren

**Hulp**: Azure-activiteiten logboek gebruiken om netwerk resource configuraties te bewaken en wijzigingen te detecteren voor netwerk bronnen die betrekking hebben op uw Azure Cosmos DB exemplaren. Maak waarschuwingen in Azure Monitor die worden geactiveerd wanneer er wijzigingen in kritieke netwerk bronnen plaatsvinden. 

Activiteiten logboek gebeurtenissen van Azure weer geven en ophalen: https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

Waarschuwingen maken in Azure Monitor: https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="logging-and-monitoring"></a>Logboekregistratie en bewaking

*Zie voor meer informatie [beveiligings beheer: logboek registratie en controle](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: goedgekeurde tijd synchronisatie bronnen gebruiken

**Richt lijnen**: micro soft onderhoudt de tijd bron die wordt gebruikt voor Azure-resources, zoals Azure Cosmos DB voor tijds tempels in de logboeken.


**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: micro soft

### <a name="22-configure-central-security-log-management"></a>2,2: Centraal beveiligings logboek beheer configureren

**Hulp**: opname logboeken via Azure monitor voor het verzamelen van beveiligings gegevens die zijn gegenereerd door Azure Cosmos db. In de Azure Monitor gebruikt u Log Analytics werk ruimte (n) om een query uit te voeren en een analyse te verrichten en opslag accounts te gebruiken voor lange termijn/archiverings opslag. U kunt ook gegevens naar Azure Sentinel of een beveiligings incident en gebeurtenis beheer van derden (SIEM) inschakelen en op de trein zetten. 

Diagnostische logboeken inschakelen voor Azure Cosmos DB: https://docs.microsoft.com/azure/cosmos-db/logging

Azure-Sentinel onboarden: https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: controle logboek registratie inschakelen voor Azure-resources

**Hulp**: Diagnostische instellingen inschakelen voor Azure Cosmos DB en de logboeken verzenden naar een log Analytics-werk ruimte of een opslag account. Diagnostische instellingen in Azure Cosmos DB worden gebruikt om resource logboeken te verzamelen. Deze logboeken worden vastgelegd per aanvraag en ze worden ook wel ' gegevens vlak logboeken ' genoemd. Enkele voor beelden van gegevens vlak bewerkingen zijn verwijderen, invoegen en lezen. U kunt ook diagnostische instellingen van Azure-activiteiten logboek inschakelen en deze naar dezelfde Log Analytics-werk ruimte verzenden.

Diagnostische instellingen inschakelen voor Azure Cosmos DB: https://docs.microsoft.com/azure/cosmos-db/logging

Diagnostische instellingen inschakelen voor Azure-activiteiten logboek: https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: beveiligings logboeken verzamelen van besturings systemen

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="25-configure-security-log-storage-retention"></a>2,5: Bewaar beveiliging van het beveiligings logboek configureren

**Richt lijnen**: stel in azure monitor de Bewaar periode voor het logboek In voor log Analytics werk ruimten die zijn gekoppeld aan uw Azure Cosmos DB-instanties volgens de nalevings voorschriften van uw organisatie.

Para meters voor het bewaren van Logboeken instellen: https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="26-monitor-and-review-logs"></a>2,6: Logboeken bewaken en controleren

**Hulp**: u kunt query's uitvoeren in log Analytics een werk ruimte om voor waarden te zoeken, trends te identificeren, patronen te analyseren en veel andere inzichten te bieden op basis van de Azure Cosmos DB-logboeken die u hebt verzameld.

Query's uitvoeren voor Azure Cosmos DB in Log Analytics-werk ruimten: https://docs.microsoft.com/azure/cosmos-db/monitor-cosmos-db

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="27-enable-alerts-for-anomalous-activity"></a>2,7: waarschuwingen inschakelen voor afwijkende activiteit

**Hulp**: in azure Security Center kunt u geavanceerde bedreigingen beveiliging inschakelen voor Azure Cosmos DB om afwijkende activiteiten in beveiligings logboeken en gebeurtenissen te bewaken. Diagnostische instellingen in Azure Cosmos DB inschakelen en logboeken naar een Log Analytics werkruimte verzenden.

 

U kunt uw Log Analytics-werk ruimte ook vrijgeven aan Azure Sentinel, omdat deze een via-oplossing (Security Orchestration Automated Response) biedt. Hiermee kunnen playbooks (geautomatiseerde oplossingen) worden gemaakt en gebruikt om beveiligings problemen op te lossen. Daarnaast kunt u aangepaste logboek waarschuwingen maken in uw Log Analytics-werk ruimte met behulp van Azure Monitor.

Lijst met beveiligings waarschuwingen voor bedreigingen voor Azure Cosmos DB: https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-azurecosmos

Azure-Sentinel onboarden: https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Logboek waarschuwingen maken, weer geven en beheren met behulp van Azure Monitor: https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="28-centralize-anti-malware-logging"></a>2,8: registratie van anti-malware centraliseren

**Richt lijnen**: niet van toepassing; Azure Cosmos DB geen aan anti-malware gerelateerde logboeken verwerkt of produceert.


**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="29-enable-dns-query-logging"></a>2,9: DNS-query logboek registratie inschakelen

**Richt lijnen**: niet van toepassing; Azure Cosmos DB worden geen DNS-gerelateerde logboeken verwerkt of geproduceerd.


**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="210-enable-command-line-audit-logging"></a>2,10: controle logboek registratie op opdracht regel inschakelen

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

## <a name="identity-and-access-control"></a>Identiteits- en toegangsbeheer

*Zie [beveiligings beheer: identiteit en Access Control](../security/benchmarks/security-control-identity-access-control.md)voor meer informatie.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: een inventaris van beheerders accounts onderhouden

**Richt lijnen**: u kunt het venster identiteits-en toegangs beheer (IAM) in de Azure Portal gebruiken om op rollen gebaseerd toegangs beheer (Azure RBAC) voor Azure te configureren en inventaris op Azure Cosmos DB resources te onderhouden. De rollen worden toegepast op gebruikers, groepen, service-principals en beheerde identiteiten in Active Directory. U kunt ingebouwde rollen of aangepaste rollen gebruiken voor individuen en groepen.

Azure Cosmos DB biedt ingebouwde Azure RBAC voor algemene beheer scenario's in Azure Cosmos DB. Een persoon die een profiel in Azure Active Directory (AD) heeft, kan deze Azure-rollen toewijzen aan gebruikers, groepen, service-principals of beheerde identiteiten voor het verlenen of weigeren van toegang tot resources en bewerkingen op Azure Cosmos DB resources.

U kunt ook de Azure AD Power shell-module gebruiken om ad hoc query's uit te voeren om accounts te detecteren die lid zijn van beheer groepen. 

Daarnaast kunnen sommige acties in Azure Cosmos DB worden beheerd met Azure Active Directory en account-specifieke primaire sleutels.  Gebruik de account instelling ' disableKeyBasedMetadataWriteAccess ' om de toegang tot sleutels te beheren.

Meer informatie over toegangs beheer op basis van rollen in Azure in Azure Cosmos DB: https://docs.microsoft.com/azure/cosmos-db/role-based-access-control

Bouw uw eigen aangepaste rollen met behulp van Azure Cosmos DB acties (Microsoft.DocumentDB naam ruimte): https://docs.microsoft.com/azure/role-based-access-control/resource-provider-operations#microsoftdocumentdb

Een nieuwe rol maken in Azure Active Directory: https://docs.microsoft.com/azure/role-based-access-control/custom-roles

Een directory-rol verkrijgen in Azure Active Directory met Power shell: https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0&preserve-view=true

Leden van een directory-rol ophalen in Azure Active Directory met Power shell: https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0&preserve-view=true

Gebruikers toegang beperken tot alleen gegevens bewerkingen: https://docs.microsoft.com/azure/cosmos-db/how-to-restrict-user-data

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="32-change-default-passwords-where-applicable"></a>3,2: standaard wachtwoorden wijzigen indien van toepassing

**Hulp**: het concept van standaard of leeg wacht woord bestaat niet in relatie tot Azure AD of Azure Cosmos db. In plaats daarvan gebruikt Azure Cosmos DB twee soorten sleutels om gebruikers te verifiëren en toegang te bieden tot de gegevens en bronnen van de gebruiker. primaire sleutels en bron tokens. De sleutels kunnen op elk gewenst moment opnieuw worden gegenereerd.

Beveiligde toegang tot gegevens in Azure Cosmos DB: https://docs.microsoft.com/azure/cosmos-db/secure-access-to-data

Azure Cosmos DB sleutels opnieuw genereren: https://docs.microsoft.com/azure/cosmos-db/manage-with-powershell#regenerate-keys

Programmatisch toegang tot sleutels met Azure Active Directory: https://docs.microsoft.com/azure/cosmos-db/certificate-based-authentication

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Gedeeld

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: speciale beheerders accounts gebruiken

**Richt lijnen**: niet van toepassing; Azure Cosmos DB biedt geen ondersteuning voor beheerders accounts.  Alle toegang is geïntegreerd met Azure Active Directory en op rollen gebaseerd toegangs beheer van Azure (Azure RBAC).



**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: eenmalige aanmelding (SSO) met Azure Active Directory gebruiken

**Hulp**: Azure Cosmos DB gebruikt twee soorten sleutels om gebruikers te autoriseren en biedt geen ondersteuning voor eenmalige Sign-On (SSO) op het niveau van het gegevens vlak. Toegang tot het besturings vlak voor Cosmos DB is beschikbaar via REST API en ondersteunt SSO. Als u zich wilt verifiëren, stelt u de autorisatie-header voor uw aanvragen in op een JSON Web Token dat u hebt verkregen via Azure Active Directory.

Meer informatie over Azure data base for Cosmos DB REST API: https://docs.microsoft.com/rest/api/cosmos-db/

Meer informatie over eenmalige aanmelding met Azure Active Directory: https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: multi-factor Authentication gebruiken voor alle op Azure Active Directory gebaseerde toegang

**Hulp**: schakel Azure Active Directory multi-factor Authentication in en volg de aanbevelingen voor Azure Security Center identiteits-en toegangs beheer.

MFA inschakelen in Azure: https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Identiteit en toegang bewaken in Azure Security Center: https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: gebruik speciale machines (privileged Access workstations) voor alle beheer taken

**Hulp**: gebruik paw (privileged Access workstations) met multi-factor Authentication geconfigureerd om u aan te melden en Azure-resources te configureren.

Meer informatie over privileged Access workstations: https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/

MFA inschakelen in Azure: https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3,7: logboek en waarschuwing voor verdachte activiteiten van beheerders accounts

**Hulp**: gebruik Advanced Threat Protection (ATP) voor Azure Cosmos db. ATP voor Azure Cosmos DB biedt een extra beveiligingslaag waarmee ongebruikelijke en mogelijk schadelijke pogingen voor het openen of exploiteren van Azure Cosmos-accounts worden gedetecteerd. Met deze beveiligingslaag kunt u bedreigingen aanpakken en ze integreren met centrale beveiligings bewakings systemen. 

Daarnaast kunt u Azure Active Directory (AD) Privileged Identity Management (PIM) gebruiken voor het genereren van Logboeken en waarschuwingen wanneer verdachte of onveilige activiteiten in de omgeving worden uitgevoerd.

Gebruik Azure AD-risico detecties om waarschuwingen en rapporten weer te geven over Risk ante gebruikers gedrag.

Privileged Identity Management (PIM) implementeren: https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan

Meer informatie over Azure AD-risico detectie: https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: Azure-resources alleen beheren vanaf goedgekeurde locaties

**Hulp**: de locatie voorwaarde van een beleid voor voorwaardelijke toegang configureren en uw benoemde locaties beheren. Met benoemde locaties kunt u logische groeperingen van IP-adresbereiken of landen en regio's maken. U kunt de toegang tot gevoelige bronnen, zoals uw Azure Cosmos DB-instanties, beperken tot uw geconfigureerde benoemde locaties.

Benoemde locaties configureren in Azure: https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="39-use-azure-active-directory"></a>3,9: Azure Active Directory gebruiken

**Hulp**: gebruik Azure Active Directory (AD) als centrale verificatie-en autorisatie systeem. Azure AD beveiligt gegevens door gebruik te maken van sterke versleuteling voor gegevens in rust en onderweg. Azure AD bevat ook zouten, hashes en veilige gebruikers referenties.

Een Azure Active Directory-exemplaar maken en configureren: https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance

Azure Active Directory-verificatie configureren en beheren met Azure SQL: https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: regel matig gebruikers toegang controleren en afstemmen

**Hulp**: Azure Active Directory biedt logboeken waarmee u verlopen accounts kunt detecteren. Daarnaast kunt u Azure Identity Access revisies gebruiken om groepslid maatschappen en de toegang tot bedrijfs toepassingen en roltoewijzingen op efficiënte wijze te beheren. De toegang van de gebruiker kan regel matig worden gecontroleerd om ervoor te zorgen dat alleen de juiste gebruikers toegang hebben.

Azure Identity Access revisies gebruiken: https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3,11: controle pogingen om toegang te krijgen tot gedeactiveerde accounts

**Richt lijnen**: u kunt Diagnostische instellingen voor Azure Active Directory gebruikers accounts maken, de audit logboeken en aanmeldings logboeken verzenden naar een log Analytics-werk ruimte waar u de gewenste waarschuwingen kunt configureren.

Azure-activiteiten logboeken integreren in Azure Monitor: https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: waarschuwing voor de afwijking van het aanmeldings gedrag van accounts

**Hulp**: gebruik Advanced Threat Protection (ATP) voor Azure Cosmos db. ATP voor Azure Cosmos DB biedt een extra beveiligingslaag waarmee ongebruikelijke en mogelijk schadelijke pogingen voor het openen of exploiteren van Azure Cosmos-accounts worden gedetecteerd. Met deze beveiligingslaag kunt u bedreigingen aanpakken en ze integreren met centrale beveiligings bewakings systemen. 

U kunt ook de functie voor het detecteren van Azure AD Identity Protection en risico's gebruiken om automatische antwoorden te configureren op gedetecteerde verdachte acties met betrekking tot gebruikers identiteiten. Daarnaast kunt u Logboeken opnemen in azure Sentinel voor verder onderzoek.

Azure Active Directory Risk ante aanmeldingen weer geven: https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Risico beleid voor identiteits beveiliging configureren en inschakelen: https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

Azure-Sentinel onboarden: https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: micro soft biedt toegang tot relevante klant gegevens tijdens ondersteunings scenario's

**Hulp**: klanten-lockbox is niet van toepassing op Cosmos db.  Werk nemers van micro soft hebben geen toegang tot gegevens bronnen, zelfs met klant machtigingen.

Lijst met door Klanten-lockbox ondersteunde services: https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

## <a name="data-protection"></a>Gegevensbeveiliging

*Zie [beveiligings beheer: gegevens beveiliging](../security/benchmarks/security-control-data-protection.md)voor meer informatie.*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: een inventaris van gevoelige informatie onderhouden

**Hulp**: Tags gebruiken bij het volgen van Azure Cosmos DB exemplaren die gevoelige informatie opslaan of verwerken.

Tags maken en gebruiken: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: systemen isoleren die gevoelige informatie opslaan of verwerken

**Richt lijnen**: afzonderlijke abonnementen en/of beheer groepen implementeren voor ontwikkeling, testen en productie. Azure Cosmos DB exemplaren worden van elkaar gescheiden door een virtueel netwerk/subnet, worden op de juiste wijze gelabeld en beveiligd in een netwerk beveiligings groep (NSG) of Azure Firewall. Azure Cosmos DB instanties waarin gevoelige gegevens worden opgeslagen, moeten worden geïsoleerd. Met behulp van een persoonlijke Azure-koppeling kunt u verbinding maken met een Azure Cosmos DB exemplaar account via een persoonlijk eind punt. Het persoonlijke eind punt is een reeks privé-IP-adressen in een subnet binnen het virtuele netwerk. Vervolgens kunt u de toegang tot de geselecteerde privé-IP-adressen beperken. 

Aanvullende Azure-abonnementen maken: https://docs.microsoft.com/azure/billing/billing-create-subscription

Beheer groepen maken: https://docs.microsoft.com/azure/governance/management-groups/create

Tags maken en gebruiken: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Een persoonlijk eind punt configureren voor Azure Cosmos DB: https://docs.microsoft.com/azure/cosmos-db/how-to-configure-private-endpoints

Een netwerk beveiligings groep maken met een beveiligings configuratie: https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: niet-geautoriseerde overdracht van gevoelige gegevens controleren en blok keren

**Richt lijnen**: u kunt geavanceerde dreigingen beveiligen voor Azure Cosmos DB, waarmee afwijkende activiteiten worden gedetecteerd die een ongebruikelijke en potentieel schadelijke pogingen tot toegang tot of exploiten van data bases zijn. De volgende waarschuwingen kunnen momenteel worden geactiveerd:

- Toegang vanaf ongebruikelijke locaties

- Ongebruikelijke gegevens extractie

Daarnaast kunt u, wanneer u virtuele machines gebruikt om toegang te krijgen tot uw Azure Cosmos DB-instanties, een persoonlijke koppeling, firewall, netwerk beveiligings groepen en service tags gebruiken om de kans op gegevens exfiltration te beperken. Micro soft beheert de onderliggende infra structuur voor Azure Cosmos DB en heeft strikte controles geïmplementeerd om verlies of bloot stelling van klant gegevens te voor komen.

Cosmos DB Advanced Threat Protection configureren: https://docs.microsoft.com/azure/cosmos-db/cosmos-db-advanced-threat-protection

Informatie over de beveiliging van klant gegevens in Azure: https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Gedeeld

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: alle gevoelige gegevens in de overdracht versleutelen

**Hulp**: alle verbindingen met Azure Cosmos DB ondersteuning voor HTTPS. Azure Cosmos DB ondersteunt ook TLS 1.2. Het is mogelijk om een minimale TLS-versie aan de server zijde af te dwingen. Als u dit wilt doen, neemt u contact op met [azurecosmosdbtls@service.microsoft.com](mailto:azurecosmosdbtls@service.microsoft.com) .

Overzicht van Cosmos DB beveiliging: https://docs.microsoft.com/azure/cosmos-db/database-security

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: micro soft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: een actief detectie hulpprogramma gebruiken om gevoelige gegevens te identificeren

**Hulp**: de functies voor automatische gegevens identificatie, classificatie en verlies preventie zijn nog niet beschikbaar voor Azure Cosmos db. U kunt echter de integratie van Azure Cognitive Search gebruiken voor classificatie en gegevens analyse. U kunt ook een oplossing van derden implementeren als dat nodig is voor nalevings doeleinden.

Voor het onderliggende platform dat door micro soft wordt beheerd, behandelt micro soft alle inhoud van de klant als gevoelig en gaat u naar een fantastische lengte om te beschermen tegen verlies en bloot stelling van klant gegevens. Om ervoor te zorgen dat klant gegevens binnen Azure veilig blijven, heeft micro soft een reeks robuuste besturings elementen en mogelijkheden voor gegevens bescherming geïmplementeerd en onderhouden.

Index Azure Cosmos DB gegevens met Azure Cognitive Search: https://docs.microsoft.com/azure/search/search-howto-index-cosmosdb?toc=/azure/cosmos-db/toc.json&amp ; BC =/azure/cosmos-db/breadcrumb/toc.jsop

Informatie over de beveiliging van klant gegevens in Azure: https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Gedeeld

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: Azure RBAC gebruiken om de toegang tot resources te beheren

**Hulp**: Azure Cosmos DB biedt ingebouwde op rollen gebaseerd toegangs beheer voor Azure (Azure RBAC) voor algemene beheer scenario's in azure Cosmos db. Een persoon die een profiel in Azure Active Directory heeft, kan deze Azure-rollen toewijzen aan gebruikers, groepen, service-principals of beheerde identiteiten voor het verlenen of weigeren van toegang tot resources en bewerkingen op Azure Cosmos DB-resources. Roltoewijzingen zijn alleen gericht op toegangs beheer, waaronder toegang tot Azure Cosmos-accounts, data bases, containers en aanbiedingen (door Voer).

Azure RBAC implementeren in Azure Cosmos DB: https://docs.microsoft.com/azure/cosmos-db/role-based-access-control

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: voor komen dat gegevens verlies op basis van host wordt gebruikt voor het afdwingen van toegangs beheer

**Richt lijnen**: niet van toepassing; deze richt lijn is bedoeld voor reken resources.

Micro soft beheert de onderliggende infra structuur voor Cosmos DB en heeft strikte controles geïmplementeerd om verlies of bloot stelling van klant gegevens te voor komen.

Informatie over de beveiliging van klant gegevens in Azure: https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: gevoelige informatie op rest versleutelen

**Hulp**: alle gebruikers gegevens die zijn opgeslagen in Cosmos DB, zijn standaard versleuteld. Er zijn geen besturings elementen om het uit te scha kelen. Azure Cosmos DB maakt gebruik van AES-256-versleuteling voor alle regio's waar het account wordt uitgevoerd.

Standaard beheert micro soft de sleutels die worden gebruikt voor het versleutelen van de gegevens in uw Azure Cosmos-account. U kunt ervoor kiezen om een tweede laag versleuteling toe te voegen met uw eigen sleutels.

Meer informatie over versleuteling op rest met Azure Cosmos DB: https://docs.microsoft.com/azure/cosmos-db/database-encryption-at-rest

Meer informatie over sleutel beheer voor versleuteling in rust met Azure Cosmos DB: https://docs.microsoft.com/azure/cosmos-db/cosmos-db-security-controls

Door de klant beheerde sleutels voor uw Azure Cosmos DB account configureren: https://docs.microsoft.com/azure/cosmos-db/how-to-setup-cmk

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Gedeeld

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: wijzigingen in essentiële Azure-resources vastleggen en waarschuwen

**Hulp**: gebruik Azure monitor met het Azure-activiteiten logboek om waarschuwingen te maken wanneer wijzigingen worden aangebracht in productie-exemplaren van Azure Cosmos db.

Waarschuwingen voor Azure-activiteiten logboek gebeurtenissen maken: https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

Waarschuwingen voor Azure-activiteiten logboek gebeurtenissen maken: https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="vulnerability-management"></a>Beheer van beveiligingsproblemen

*Zie [beveiligings beheer: beveiligingslek beheer](../security/benchmarks/security-control-vulnerability-management.md)voor meer informatie.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: automatische hulpprogram ma's voor het scannen van beveiligings problemen uitvoeren

**Hulp**: Volg de aanbevelingen van Azure Security Center voor uw Azure Cosmos DB-instanties. 

Micro soft voert systeem patches en beveiligings beheer uit op de onderliggende hosts die uw Azure Cosmos DB-instanties ondersteunen. Om ervoor te zorgen dat klant gegevens binnen Azure veilig blijven, heeft micro soft een reeks robuuste besturings elementen en mogelijkheden voor gegevens bescherming geïmplementeerd en onderhouden.

Ondersteunde functies die beschikbaar zijn in Azure Security Center: https://docs.microsoft.com/azure/security-center/security-center-services?tabs=features-windows

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Gedeeld

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: geautomatiseerde oplossing voor patch beheer voor besturings systemen implementeren

**Richt lijnen**: niet van toepassing; deze richt lijn is bedoeld voor reken resources.


**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5,3: Implementeer een geautomatiseerde oplossing voor software patch beheer van derden

**Richt lijnen**: niet van toepassing; deze richt lijn is bedoeld voor reken resources.


**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: vergelijken van back-to-back-problemen

**Richt lijnen**: niet van toepassing; deze richt lijn is bedoeld voor reken resources.


**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: een risico classificatie proces gebruiken om prioriteit te geven aan het herstel van ontdekte beveiligings problemen

**Richt lijnen**: niet van toepassing; deze richt lijn is bedoeld voor reken resources.


**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

## <a name="inventory-and-asset-management"></a>Inventarisatie en Asset Management

*Zie voor meer informatie [beveiligings beheer: inventarisatie en activa beheer](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-azure-asset-discovery"></a>6,1: Azure Asset Discovery gebruiken

**Hulp**: gebruik de Resource grafiek Azure portal of Azure om alle resources te ontdekken (niet beperkt tot Azure Cosmos DB, maar ook resources zoals compute, andere opslag, netwerk, poorten en protocollen, enzovoort) binnen uw abonnement (en).  Zorg ervoor dat u de juiste machtigingen hebt in uw Tenant en dat u alle Azure-abonnementen kunt inventariseren, evenals de resources in uw abonnementen.

Hoewel klassieke Azure-resources kunnen worden gedetecteerd via resource grafiek, is het raadzaam om Azure Resource Manager resources te maken en te gebruiken.

Query's maken met Azure resource Graph: https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Uw Azure-abonnementen weer geven: https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0&preserve-view=true

Meer informatie over toegangs beheer op basis van rollen in Azure: https://docs.microsoft.com/azure/role-based-access-control/overview

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="62-maintain-asset-metadata"></a>6,2: meta gegevens van activa onderhouden

**Richt lijnen**: Tags Toep assen op uw Azure Cosmos DB instanties en gerelateerde resources met meta gegevens om ze logisch in een taxonomie te organiseren.

Tags maken en gebruiken: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Welke Azure Cosmos DB middelen Tags ondersteunt: https://docs.microsoft.com/azure/azure-resource-manager/management/tag-support#microsoftdocumentdb

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: niet-geautoriseerde Azure-resources verwijderen

**Richt lijnen**: Gebruik labels, beheer groepen en afzonderlijke abonnementen, waar nodig, om assets te organiseren en bij te houden, inclusief, maar niet beperkt tot Azure Cosmos DB resources. Sluit de inventaris regel matig af en zorg ervoor dat niet-geautoriseerde resources tijdig worden verwijderd uit het abonnement.

Aanvullende Azure-abonnementen maken: https://docs.microsoft.com/azure/billing/billing-create-subscription

Beheergroepen maken: https://docs.microsoft.com/azure/governance/management-groups/create

Tags maken en gebruiken: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6,4: een inventaris van goedgekeurde Azure-resources en software titels onderhouden

**Richt lijnen**: niet van toepassing; deze richt lijn is bedoeld voor reken bronnen en Azure als geheel.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: monitor voor niet-goedgekeurde Azure-resources

**Hulp: gebruik** Azure Policy om beperkingen toe te voegen voor het type resources dat kan worden gemaakt in klant abonnement (en) met de volgende ingebouwde beleids definities:

- Niet toegestane resourcetypen

- Toegestane brontypen

Daarnaast gebruikt u de resource grafiek van Azure voor het opvragen/detecteren van resources binnen een of meer abonnementen.

Azure Policy configureren en beheren: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Query's maken met Azure Graph: https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: monitor voor niet-goedgekeurde software toepassingen binnen reken resources

**Richt lijnen**: niet van toepassing; deze basis lijn is bedoeld voor reken resources.


**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: niet-goedgekeurde Azure-resources en software toepassingen verwijderen

**Richt lijnen**: niet van toepassing; deze richt lijn is bedoeld voor reken bronnen en Azure als geheel.


**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="68-use-only-approved-applications"></a>6,8: alleen goedgekeurde toepassingen gebruiken

**Richt lijnen**: niet van toepassing; deze richt lijn is bedoeld voor reken resources.


**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="69-use-only-approved-azure-services"></a>6,9: alleen goedgekeurde Azure-Services gebruiken

**Hulp: gebruik** Azure Policy om beperkingen toe te voegen voor het type resources dat kan worden gemaakt in klant abonnement (en) met de volgende ingebouwde beleids definities:

- Niet toegestane resourcetypen 

- Toegestane brontypen

Azure Policy configureren en beheren: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Een specifiek resource type weigeren met Azure Policy: https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="610-implement-approved-application-list"></a>6,10: lijst met goedgekeurde toepassingen implementeren

**Richt lijnen**: niet van toepassing; deze richt lijn is bedoeld voor reken resources.


**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="611-limit-users-ability-to-interact-with-azureresources-manager-via-scripts"></a>6,11: de mogelijkheid van gebruikers om te communiceren met kunt Manager via scripts beperken

**Richt lijnen**: gebruik de voorwaardelijke toegang van Azure om gebruikers de mogelijkheid te bieden om te communiceren met Azure Resource Manager door ' blok toegang ' te configureren voor de app Microsoft Azure management. Dit kan ertoe leiden dat het maken en wijzigen van bronnen binnen een omgeving met hoge beveiliging wordt voor komen.

Voorwaardelijke toegang configureren om de toegang tot Azure Resource Manager te blok keren: https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: de mogelijkheid van gebruikers om scripts uit te voeren binnen reken bronnen beperken

**Richt lijnen**: niet van toepassing; deze richt lijn is bedoeld voor reken resources.


**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: toepassingen met een hoog risico fysiek of logisch scheiden

**Richt lijnen**: niet van toepassing; deze richt lijn is bedoeld voor webtoepassingen die worden uitgevoerd op Azure App Service of reken bronnen.


**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

## <a name="secure-configuration"></a>Veilige configuratie

*Zie [beveiligings beheer: beveiligde configuratie](../security/benchmarks/security-control-secure-configuration.md)voor meer informatie.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: veilige configuraties instellen voor alle Azure-resources

**Richt lijnen**: standaard beveiligings configuraties voor uw Cosmos DB-instanties definiëren en implementeren met Azure Policy. Gebruik Azure Policy aliassen in de naam ruimte ' Microsoft.DocumentDB ' om aangepaste beleids regels te maken om de configuratie van uw Cosmos DB exemplaren te controleren of af te dwingen. U kunt ook gebruik maken van ingebouwde beleids definities voor Azure Cosmos DB, zoals:

- Advanced Threat Protection implementeren voor Cosmos DB-accounts

- Cosmos DB moet gebruikmaken van een service-eindpunt voor een virtueel netwerk

Beschik bare Azure Policy aliassen weer geven: https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0&preserve-view=true

Azure Policy configureren en beheren: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: veilige configuraties van besturings systemen instellen

**Richt lijnen**: niet van toepassing; deze richt lijn is bedoeld voor reken resources.


**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: Beveilig Azure-resource configuraties onderhouden

**Hulp**: gebruik Azure Policy [deny] en [implementeren indien niet aanwezig] voor het afdwingen van beveiligde instellingen voor uw Azure-resources.

Azure Policy configureren en beheren: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure Policy effecten begrijpen: https://docs.microsoft.com/azure/governance/policy/concepts/effects

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: veilige configuraties van besturings systemen onderhouden

**Richt lijnen**: niet van toepassing; deze richt lijn is bedoeld voor reken resources.


**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: de configuratie van Azure-resources veilig opslaan

**Richt lijnen**: als u aangepaste Azure Policy definities gebruikt voor uw Cosmos DB of gerelateerde resources, gebruikt u Azure opslag plaatsen om uw code veilig op te slaan en te beheren.

Documentatie voor Azure opslag plaatsen: https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops&preserve-view=truehttps://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops&preserve-view=true

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: aangepaste installatie kopieën van een besturings systeem veilig opslaan

**Richt lijnen**: niet van toepassing; deze richt lijn is bedoeld voor reken resources.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="77-deploy-system-configuration-management-tools"></a>7,7: hulpprogram ma's voor het beheer van systeem configuratie implementeren

**Hulp**: gebruik Azure Policy aliassen in de naam ruimte ' Microsoft.DocumentDB ' om aangepaste beleids regels te maken om systeem configuraties te Signa lering, te controleren en af te dwingen. Ontwikkel bovendien een proces en pijp lijn voor het beheren van beleids uitzonderingen.

Azure Policy configureren en beheren: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7,8: hulpprogram ma's voor het beheer van systeem configuratie implementeren voor besturings systemen

**Richt lijnen**: niet van toepassing; deze richt lijn is bedoeld voor reken resources.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7,9: geautomatiseerde configuratie bewaking voor Azure-Services implementeren

**Hulp**: gebruik Azure Policy aliassen in de naam ruimte ' Microsoft.DocumentDB ' om aangepaste beleids regels te maken om systeem configuraties te Signa lering, te controleren en af te dwingen. Gebruik Azure Policy [audit], [deny] en [implementeren indien niet aanwezig] om automatisch configuraties af te dwingen voor uw Azure Cosmos DB instanties en gerelateerde bronnen. 

Azure Policy configureren en beheren: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: geautomatiseerde configuratie bewaking voor besturings systemen implementeren

**Richt lijnen**: niet van toepassing; deze richt lijn is bedoeld voor reken resources.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="711-manage-azure-secrets-securely"></a>7,11: Azure-geheimen veilig beheren

**Richt lijnen**: voor virtuele Azure-machines of webtoepassingen die worden uitgevoerd op Azure app service worden gebruikt voor toegang tot uw Azure Cosmos DB-instanties, gebruikt u Managed Service Identity in combi natie met Azure Key Vault om het beheer van het geheim te vereenvoudigen en te Azure Cosmos DB beveiligen. Zorg ervoor Key Vault zacht verwijderen is ingeschakeld.

Integratie met door Azure beheerde identiteiten: https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Een Key Vault maken: https://docs.microsoft.com/azure/key-vault/general/quick-create-portal

Verificatie bij Key Vault: https://docs.microsoft.com/azure/key-vault/general/authentication

Een Key Vault toegangs beleid toewijzen: https://docs.microsoft.com/azure/key-vault/general/assign-access-policy-portal

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: identiteiten veilig en automatisch beheren

**Richt lijnen**: voor virtuele Azure-machines of webtoepassingen die worden uitgevoerd op Azure app service worden gebruikt voor toegang tot uw Azure Cosmos DB-instanties, gebruikt u Managed Service Identity in combi natie met Azure Key Vault om het beheer van het geheim te vereenvoudigen en te Azure Cosmos DB beveiligen.

Gebruik beheerde identiteiten om Azure-Services te voorzien van een automatisch beheerde identiteit in Azure Active Directory (AD). Met beheerde identiteiten kunt u zich verifiëren bij elke service die ondersteuning biedt voor Azure AD-verificatie, met inbegrip van Key Vault, zonder enige referenties in uw code.

Beheerde identiteiten configureren: https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

Integratie met door Azure beheerde identiteiten: https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: onbedoelde referentie blootstelling elimineren

**Richt lijnen**: referentie scanner implementeren om referenties in code te identificeren. Door het gebruik van Credential Scanner worden gebruikers ook aangemoedigd om gedetecteerde referenties naar veiligere locaties, zoals Azure Key Vault, te verplaatsen.

Referentie scanner instellen: https://secdevtools.azurewebsites.net/helpcredscan.html

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="malware-defense"></a>Beveiliging tegen malware

*Zie [beveiligings beheer: verdediging tegen malware](../security/benchmarks/security-control-malware-defense.md)voor meer informatie.*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: centraal beheerde anti-malware-software gebruiken

**Richt lijnen**: niet van toepassing; deze richt lijn is bedoeld voor reken resources. Micro soft antimalware is ingeschakeld op de onderliggende host die ondersteuning biedt voor Azure-Services (bijvoorbeeld Azure App Service), maar wordt niet uitgevoerd op de inhoud van de klant.


**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: scan bestanden die moeten worden geüpload naar niet-reken resources van Azure

**Hulp**: micro soft antimalware is ingeschakeld op de onderliggende host die ondersteuning biedt voor Azure-Services (bijvoorbeeld Azure app service), maar wordt niet uitgevoerd op de inhoud van de klant.

Het is uw verantwoordelijkheid om vooraf bestanden te scannen die worden geüpload naar niet-reken resources van Azure, met inbegrip van Azure Cosmos DB. Micro soft heeft geen toegang tot klant gegevens en kan daarom geen anti-malware scans uitvoeren van klant inhoud namens u.


**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: controleren of anti-malware-software en hand tekeningen zijn bijgewerkt

**Richt lijnen**: niet van toepassing; Bench Mark is bedoeld voor reken resources. Micro soft antimalware is ingeschakeld op de onderliggende host die ondersteuning biedt voor Azure-Services, maar wordt niet uitgevoerd op de inhoud van de klant.


**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

## <a name="data-recovery"></a>Gegevensherstel

*Zie [beveiligings beheer: gegevens herstel](../security/benchmarks/security-control-data-recovery.md)voor meer informatie.*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: controleren op regel matige automatische back-ups

**Hulp**: Azure Cosmos DB maakt moment opnamen van uw gegevens om de vier uur. Alle back-ups worden afzonderlijk in een opslag service opgeslagen en deze back-ups worden wereld wijd gerepliceerd voor flexibiliteit tegen regionale rampen. Op elk gewenst moment worden alleen de laatste twee moment opnamen bewaard. Als de container of database echter wordt verwijderd, blijven in Azure Cosmos DB de bestaande momentopnamen van een opgegeven container of database gedurende 30 dagen behouden. Neem contact op met de ondersteuning van Azure om een back-up te herstellen.

Meer informatie over Azure Cosmos DB automatische back-ups: https://docs.microsoft.com/azure/cosmos-db/online-backup-and-restore

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: micro soft

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: volledige back-ups van het systeem uitvoeren en een back-up maken van een door de klant beheerde sleutels

**Hulp**: met Azure Cosmos DB maakt u regel matig back-ups van uw gegevens. Als de data base of container is verwijderd, kunt u een ondersteunings ticket indienen of ondersteuning voor Azure aanroepen om de gegevens van automatische online back-ups te herstellen. Ondersteuning voor Azure is alleen beschikbaar voor geselecteerde abonnementen, zoals Standard, Developer en abonnementen hoger. Als u een specifieke moment opname van de back-up wilt herstellen, moet Azure Cosmos DB de gegevens beschikbaar hebben voor de duur van de back-upcyclus voor die moment opname. 

Als Key Vault gebruiken om referenties voor uw Cosmos DB-instanties op te slaan, moet u regel matig automatische back-ups van uw sleutels maken.

Meer informatie over Azure Cosmos DB automatische back-ups: https://docs.microsoft.com/azure/cosmos-db/online-backup-and-restore

Gegevens herstellen in Azure Cosmos DB: https://docs.microsoft.com/azure/cosmos-db/how-to-backup-and-restore

Back-ups maken van Key Vault sleutels: https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Gedeeld

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: alle back-ups valideren, inclusief door de klant beheerde sleutels

**Richt lijnen**: als u de data base of container verwijdert, kunt u een ondersteunings ticket indienen of ondersteuning voor Azure aanroepen om de gegevens van automatische online back-ups te herstellen. Ondersteuning voor Azure is alleen beschikbaar voor geselecteerde abonnementen, zoals Standard, Developer en abonnementen hoger. Als u een specifieke moment opname van de back-up wilt herstellen, moet Azure Cosmos DB de gegevens beschikbaar hebben voor de duur van de back-upcyclus voor die moment opname.

Test het herstellen van uw geheimen die zijn opgeslagen in Azure Key Vault met behulp van Power shell. Met de cmdlet Restore-AzureKeyVaultKey maakt u een sleutel in de opgegeven sleutel kluis. Deze sleutel is een replica van de back-upsleutel in het invoer bestand en heeft dezelfde naam als de oorspronkelijke sleutel.

Meer informatie over Azure Cosmos DB automatische back-ups:

https://docs.microsoft.com/azure/cosmos-db/online-backup-and-restore

Gegevens herstellen in Azure Cosmos DB:

https://docs.microsoft.com/azure/cosmos-db/how-to-backup-and-restore

Azure Key Vault geheimen herstellen:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0&preserve-view=true

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Gedeeld

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: zorg voor de bescherming van back-ups en door de klant beheerde sleutels

**Richt lijnen**: omdat alle gebruikers gegevens die zijn opgeslagen in Cosmos DB, worden versleuteld op rest en in Trans Port, hoeft u geen actie te ondernemen. Een andere manier om dit te doen is dat versleuteling op rest standaard is. Er zijn geen besturings elementen om deze in of uit te scha kelen. Azure Cosmos DB maakt gebruik van AES-256-versleuteling voor alle regio's waar het account wordt uitgevoerd.

Schakel Soft-Delete in Key Vault in om sleutels te beschermen tegen onbedoelde of schadelijke verwijdering.

Informatie over het versleutelen van gegevens in Azure Cosmos DB: https://docs.microsoft.com/azure/cosmos-db/database-encryption-at-rest

Soft-Delete in Key Vault inschakelen: https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Gedeeld

## <a name="incident-response"></a>Reageren op incidenten

*Zie voor meer informatie [beveiligings beheer: reactie op incidenten](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: een hand leiding voor reactie op incidenten maken

**Richtlijnen**: Stel voor uw organisatie een responshandleiding op voor gebruik bij incidenten. Zorg ervoor dat er schriftelijke responsplannen zijn waarin alle rollen van het personeel worden gedefinieerd, evenals alle fasen in het afhandelen/managen van incidenten, vanaf de detectie van het incident tot een evaluatie ervan achteraf.

U kunt ook gebruikmaken van de hand leiding voor de verwerking van het computer beveiligings incident van het NIST om u te helpen bij het maken van uw eigen reactie plan voor incidenten: https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf

Werk stroom automatisering configureren in Azure Security Center: https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

Richt lijnen voor het bouwen van uw eigen beveiligings incident antwoord proces: https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Micro soft Security Response Center anatomie van een incident: https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/


**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: een beoordelings procedure voor incidenten en prioriteits procedures maken

**Hulp**: Security Center wijst aan elke waarschuwing een Ernst toe om u te helpen bepalen welke waarschuwingen het eerst moeten worden onderzocht. De ernst is gebaseerd op de manier waarop de Security Center zich in de zoek actie bevindt of de analyse die wordt gebruikt om de waarschuwing te geven, evenals het betrouwbaarheids niveau waarvan er sprake is van schadelijke opzet achter de activiteit die tot de waarschuwing heeft geleid.

Daarnaast kunt u ook duidelijk abonnementen markeren (voor bijvoorbeeld productie, niet-productie) en maak een naamgevings systeem om Azure-resources duidelijk te identificeren en te categoriseren.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="103-test-security-response-procedures"></a>10,3: procedures voor beveiligings antwoorden testen

**Richt lijnen**: oefent oefeningen uit om de respons mogelijkheden van uw systeem op een gewone uitgebracht te testen. Stel vast waar zich zwakke plekken en hiaten bevinden, en wijzig zo nodig het plan.

Raadpleeg de publicatie van het NIST: hand leiding voor het testen, trainen en uitoefenen van Program Ma's voor IT-plannen en-mogelijkheden: https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: contact gegevens van het beveiligings incident opgeven en waarschuwings meldingen configureren voor beveiligings incidenten

**Hulp**: contact gegevens van beveiligings incidenten worden door micro soft gebruikt om contact met u op te nemen als het micro soft Security Response Center (MSRC) detecteert dat de gegevens van de klant zijn geopend door een onrecht matige of niet-gemachtigde partij.  Bekijk incidenten na het feit om te controleren of de problemen zijn opgelost.

De Azure Security Center Security-contact persoon instellen: https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: beveiligings waarschuwingen opnemen in uw reactie systeem van uw incident

**Richt lijnen**: uw Azure Security Center waarschuwingen en aanbevelingen exporteren met de functie continue export. Met doorlopend exporteren kunt u waarschuwingen en aanbevelingen hand matig of op een doorlopende manier exporteren. U kunt de Azure Security Center Data Connector gebruiken om de Sentinel van waarschuwingen te streamen.

Continue export configureren: https://docs.microsoft.com/azure/security-center/continuous-export

Waarschuwingen streamen naar Azure Sentinel: https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: de reactie op beveiligings waarschuwingen automatiseren

**Hulp**: gebruik de functie werk stroom automatisering in azure Security Center om automatisch reacties te activeren via ' Logic apps ' in beveiligings waarschuwingen en aanbevelingen.

Werk stroom automatisering en Logic Apps configureren: https://docs.microsoft.com/azure/security-center/workflow-automation

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="penetration-tests-and-red-team-exercises"></a>Penetratietests en Red Team-oefeningen

*Zie voor meer informatie [Security Control: Indringings tests en Red team-oefeningen](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11,1: voert regel matig indringings tests van uw Azure-resources uit en zorgt voor herstel van alle essentiële beveiligings resultaten binnen 60 dagen

**Richt lijnen**: Volg de micro soft-regels om ervoor te zorgen dat de indringings tests niet worden geschonden door het micro soft-beleid: https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

U vindt hier meer informatie over de strategie van micro soft en de uitvoering van de Red Teaming-en live site-indringings tests op door micro soft beheerde Cloud infrastructuur,-services en-toepassingen:  https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Gedeeld

## <a name="next-steps"></a>Volgende stappen

- Zie de [Azure Security-Bench Mark](../security/benchmarks/overview.md)
- Meer informatie over [Azure-beveiligings basislijnen](../security/benchmarks/security-baselines-overview.md)
