---
title: Azure-beveiligings basislijn voor Azure Cosmos DB
description: De Azure Cosmos DB Security Baseline voorziet in procedure richtlijnen en resources voor het implementeren van de beveiligings aanbevelingen die zijn opgegeven in de Azure Security-Bench Mark.
author: msmbaldwin
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: dead43f2e9f2e8913bcebde43d543b8df8d33ced
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105565670"
---
# <a name="azure-security-baseline-for-azure-cosmos-db"></a>Azure-beveiligings basislijn voor Azure Cosmos DB

In deze beveiligings basislijn worden richt lijnen van de [Azure Security Bench Mark-versie 1,0](../security/benchmarks/overview-v1.md) ingesteld op Azure Cosmos db. De Azure Security-benchmark biedt aanbevelingen voor hoe u uw cloudoplossingen in Azure kunt beveiligen.
De inhoud wordt gegroepeerd op de **beveiligings controles** die zijn gedefinieerd door de Azure Security-benchmark en de bijbehorende richt lijnen die van toepassing zijn op Azure Cosmos db. **Besturings elementen** die niet van toepassing zijn op Azure Cosmos DB, zijn uitgesloten.

 
Als u wilt zien hoe Azure Cosmos DB volledig is toegewezen aan de beveiligings benchmark van Azure, raadpleegt u het [volledige Azure Cosmos DB beveiligings basislijn toewijzings bestand](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Netwerkbeveiliging

*Zie [Azure Security Benchmark: netwerkbeveiliging](../security/benchmarks/security-control-network-security.md) voor meer informatie.*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Azure-resources in virtuele netwerken beveiligen

**Richt lijnen**: met behulp van een persoonlijke Azure-koppeling kunt u verbinding maken met een Azure Cosmos-account via een persoonlijk eind punt. Verkeer tussen uw virtuele netwerk en de services wordt via het backbonenetwerk van Microsoft geleid, waarmee de risico's van het openbare internet worden vermeden. U kunt ook het risico van gegevens exfiltration verminderen door een strikte set uitgaande regels te configureren in een netwerk beveiligings groep (NSG) en die NSG te koppelen aan uw subnet.

U kunt ook service-eind punten gebruiken om uw Azure Cosmos-account te beveiligen. Door een service-eind punt in te scha kelen, kunt u Azure Cosmos-accounts configureren om alleen toegang toe te staan vanaf een specifiek subnet van een virtueel Azure-netwerk. Zodra het eind punt van de Azure Cosmos DB service is ingeschakeld, kunt u de toegang tot een Azure Cosmos-account beperken met verbindingen van een subnet in een virtueel netwerk.

U kunt ook de gegevens beveiligen die zijn opgeslagen in uw Azure Cosmos-account door gebruik te maken van IP-firewalls. Azure Cosmos DB ondersteunt toegangs beheer op basis van IP voor binnenkomende firewall ondersteuning. U kunt een IP-firewall instellen voor het Azure Cosmos-account met behulp van de Azure Portal, Azure Resource Manager sjablonen of via de Azure CLI of Azure PowerShell.

- [Overzicht van Azure Private Link](../private-link/private-link-overview.md)

- [Een persoonlijk eind punt configureren voor Azure Cosmos DB](how-to-configure-private-endpoints.md) 

- [Een netwerk beveiligings groep maken met een beveiligings configuratie](../virtual-network/tutorial-filter-network-traffic.md)

- [IP-Firewall configureren in Cosmos DB](how-to-configure-firewall.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: de [Security Bench Mark van Azure](/azure/governance/policy/samples/azure-security-benchmark) is het standaard beleids initiatief voor Security Center en is de basis voor de [aanbevelingen van Security Center](/azure/security-center/security-center-recommendations). De Azure Policy definities die aan dit besturings element zijn gerelateerd, worden automatisch door Security Center ingeschakeld. Voor waarschuwingen met betrekking tot dit besturings element is mogelijk een [Azure Defender](/azure/security-center/azure-defender) -plan vereist voor de gerelateerde services.

**Azure Policy ingebouwde definities-Microsoft.DocumentDB**:

[!INCLUDE [Resource Policy for Microsoft.DocumentDB 1.1](../../includes/policy/standards/asb/rp-controls/microsoft.documentdb-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: de configuratie en het verkeer van virtuele netwerken, subnetten en netwerk interfaces bewaken en vastleggen

**Hulp**: gebruik Azure Security Center en volg aanbevelingen voor netwerk beveiliging om netwerk bronnen te beveiligen die betrekking hebben op uw Azure Cosmos-account.

Wanneer virtuele machines worden geïmplementeerd in hetzelfde virtuele netwerk als uw Azure Cosmos-account, kunt u een netwerk beveiligings groep (NSG) gebruiken om het risico van gegevens exfiltration te verminderen. Schakel logboeken voor NSG-stroom in en verzend logboeken naar een Azure Storage account voor verkeers controles. U kunt ook NSG-stroom logboeken naar een Log Analytics-werk ruimte verzenden en Traffic Analytics gebruiken om inzicht te krijgen in de verkeers stroom in uw Azure-Cloud. Enkele voor delen van Traffic Analytics zijn de mogelijkheid om netwerk activiteiten te visualiseren en HOTS pots te identificeren, beveiligings dreigingen te identificeren, verkeers patronen te begrijpen en netwerk configuraties te lokaliseren.

- [Informatie over de netwerk beveiliging die wordt verschaft door Azure Security Center](../security-center/security-center-network-recommendations.md)

- [NSG-stroom logboeken inschakelen](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Traffic Analytics inschakelen en gebruiken](../network-watcher/traffic-analytics.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="13-protect-critical-web-applications"></a>1,3: essentiële webtoepassingen beveiligen

**Richt lijnen**: gebruik de functie voor cross-Origin resource SHARING (CORS) om een webtoepassing in te scha kelen die onder het ene domein wordt uitgevoerd om toegang te krijgen tot bronnen in een ander domein. Webbrowsers implementeren een beveiligings beperking die bekend staat als hetzelfde-Origin-beleid dat voor komt dat een webpagina Api's in een ander domein aanroept. CORS biedt echter een veilige manier om het domein van de oorsprong te laten aanroepen van Api's in een ander domein. Nadat u de CORS-ondersteuning voor uw Azure Cosmos-account hebt ingeschakeld, worden alleen geverifieerde aanvragen geëvalueerd om te bepalen of ze zijn toegestaan volgens de regels die u hebt opgegeven.

- [Cross-Origin-resource delen configureren](how-to-configure-cross-origin-resource-sharing.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: communicatie met bekende schadelijke IP-adressen weigeren

**Hulp**: gebruik Advanced Threat Protection (ATP) voor Azure Cosmos db. ATP voor Azure Cosmos DB biedt een extra beveiligingslaag waarmee ongebruikelijke en mogelijk schadelijke pogingen voor het openen of exploiteren van Azure Cosmos-accounts worden gedetecteerd. Met deze beveiligingslaag kunt u bedreigingen aanpakken en ze integreren met centrale beveiligings bewakings systemen.

Schakel DDoS Protection standaard in op de virtuele netwerken die zijn gekoppeld aan uw Azure Cosmos DB-instanties om te beschermen tegen DDoS-aanvallen. Gebruik Azure Security Center geïntegreerde bedreigings informatie om communicatie met bekende of ongebruikte Internet-IP-adressen te weigeren.

- [Azure Cosmos DB Advanced Threat Protection configureren](cosmos-db-advanced-threat-protection.md)

- [DDoS-beveiliging configureren](../ddos-protection/manage-ddos-protection.md)

- [Meer informatie over Azure Security Center geïntegreerde bedreigings informatie](../security-center/azure-defender.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="15-record-network-packets"></a>1,5: netwerk pakketten opnemen

**Hulp**: Schakel logboeken voor netwerk beveiligings groepen (NSG) in en verzend logboeken naar een opslag account voor verkeers controle. U kunt NSG-stroom logboeken naar een Log Analytics-werk ruimte verzenden en Traffic Analytics gebruiken om inzicht te krijgen in de verkeers stroom in uw Azure-Cloud. Enkele voor delen van Traffic Analytics zijn de mogelijkheid om netwerk activiteiten te visualiseren en HOTS pots te identificeren, beveiligings dreigingen te identificeren, verkeers patronen te begrijpen en netwerk configuraties te lokaliseren.

- [NSG-stroom logboeken inschakelen](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Traffic Analytics inschakelen en gebruiken](../network-watcher/traffic-analytics.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: op netwerk gebaseerde inbreuk detectie/indringings systemen (ID'S/IP-adressen) implementeren

**Hulp**: gebruik Advanced Threat Protection (ATP) voor Azure Cosmos db. ATP voor Azure Cosmos DB biedt een extra beveiligingslaag waarmee ongebruikelijke en mogelijk schadelijke pogingen voor het openen of exploiteren van Azure Cosmos-accounts worden gedetecteerd. Met deze beveiligingslaag kunt u bedreigingen aanpakken en ze integreren met centrale beveiligings bewakings systemen. 

- [Cosmos DB Advanced Threat Protection configureren](cosmos-db-advanced-threat-protection.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: de complexiteit en administratieve overhead van netwerk beveiligings regels minimaliseren

**Richt lijnen**: voor bronnen die toegang nodig hebben tot uw Azure Cosmos-account, gebruikt u Virtual Network Service Tags om netwerk toegangs beheer te definiëren voor netwerk beveiligings groepen of Azure firewall. U kunt servicetags gebruiken in plaats van specifieke IP-adressen wanneer u beveiligingsregels maakt. Door de naam van de service label (bijvoorbeeld AzureCosmosDB) op te geven in het juiste bron-of doel veld van een regel, kunt u het verkeer voor de bijbehorende service toestaan of weigeren. Micro soft beheert de adres voorvoegsels die zijn opgenomen in het servicetag van de service en werkt de servicetag automatisch bij met gewijzigde adressen.

- [Voor meer informatie over het gebruik van service Tags](../virtual-network/service-tags-overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: standaard beveiligings configuraties voor netwerk apparaten onderhouden

**Richt lijnen**: standaard beveiligings configuraties voor netwerk bronnen definiëren en implementeren met Azure Policy. Gebruik Azure Policy aliassen in de naam ruimten ' Microsoft.DocumentDB ' en ' micro soft. Network ' om aangepaste beleids regels te maken om de netwerk configuratie van uw Azure Cosmos DB instanties te controleren of af te dwingen. U kunt ook gebruik maken van ingebouwde beleids definities voor Azure Cosmos DB, zoals:

- Advanced Threat Protection implementeren voor Cosmos DB-accounts

- Cosmos DB moet gebruikmaken van een service-eindpunt voor een virtueel netwerk

U kunt ook Azure-blauw drukken gebruiken om grootschalige Azure-implementaties te vereenvoudigen door sleutel omgevings artefacten, zoals Azure Resource Manager sjablonen, Toegangs beheer op basis van rollen (Azure RBAC) en beleids regels in één blauw definitie te verpakken. U kunt de blauw druk eenvoudig Toep assen op nieuwe abonnementen, omgevingen en het beheer en de verwerkings mogelijkheden van versies.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Een Azure Blueprint maken](../governance/blueprints/create-blueprint-portal.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="110-document-traffic-configuration-rules"></a>1,10: configuratie regels voor het document verkeer

**Richt lijnen**: Gebruik labels voor netwerk bronnen die zijn gekoppeld aan uw Azure Cosmos DB-implementatie om ze logisch in een taxonomie te organiseren.

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: gebruik automatische hulpprogram ma's om netwerk bron configuraties te bewaken en wijzigingen te detecteren

**Hulp**: Azure-activiteiten logboek gebruiken om netwerk resource configuraties te bewaken en wijzigingen te detecteren voor netwerk bronnen die betrekking hebben op uw Azure Cosmos DB exemplaren. Maak waarschuwingen in Azure Monitor die worden geactiveerd wanneer er wijzigingen in kritieke netwerk bronnen plaatsvinden. 

- [Activiteiten logboek gebeurtenissen van Azure weer geven en ophalen](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Waarschuwingen maken in Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="logging-and-monitoring"></a>Logboekregistratie en bewaking

*Zie [Azure Security Bench Mark: Logging and monitoring](../security/benchmarks/security-control-logging-monitoring.md)(Engelstalig) voor meer informatie.*

### <a name="22-configure-central-security-log-management"></a>2,2: Centraal beveiligings logboek beheer configureren

**Hulp**: opname logboeken via Azure monitor voor het verzamelen van beveiligings gegevens die zijn gegenereerd door Azure Cosmos db. Gebruik in Azure Monitor Log Analytics-werk ruimten om analyses uit te voeren en te uitvoeren en gebruik opslag accounts voor lange termijn/archiverings opslag. U kunt ook on-board gegevens toevoegen aan Azure Sentinel of een beveiligings incident en gebeurtenis beheer van derden (SIEM). 

- [Diagnostische logboeken inschakelen voor Azure Cosmos DB](./monitor-cosmos-db.md)

- [Azure-Sentinel onboarden](../sentinel/quickstart-onboard.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: controle logboek registratie inschakelen voor Azure-resources

**Hulp**: Diagnostische instellingen inschakelen voor Azure Cosmos DB en de logboeken verzenden naar een log Analytics-werk ruimte of een Azure-opslag account. Diagnostische instellingen in Azure Cosmos DB worden gebruikt om resource logboeken te verzamelen. Deze logboeken worden vastgelegd per aanvraag en ze worden ook wel **gegevens vlak logboeken** genoemd. Enkele voor beelden van gegevens vlak bewerkingen zijn verwijderen, invoegen en lezen. 

U kunt ook diagnostische instellingen van Azure-activiteiten logboek inschakelen en deze logboeken verzenden naar dezelfde Log Analytics werk ruimte die u voor Azure Cosmos DB-logboeken gebruikt.

- [Diagnostische instellingen inschakelen voor Azure Cosmos DB](./monitor-cosmos-db.md)

- [Diagnostische instellingen voor Azure-activiteiten logboek inschakelen](../azure-monitor/essentials/activity-log.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="25-configure-security-log-storage-retention"></a>2,5: Bewaar beveiliging van het beveiligings logboek configureren

**Richt lijnen**: stel in azure monitor de Bewaar periode voor het logboek In voor log Analytics werk ruimten die zijn gekoppeld aan uw Azure Cosmos DB-instanties volgens de nalevings voorschriften van uw organisatie.

- [Para meters voor het bewaren van Logboeken instellen](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="26-monitor-and-review-logs"></a>2,6: Logboeken bewaken en controleren

**Hulp**: u kunt query's uitvoeren in log Analytics een werk ruimte om voor waarden te zoeken, trends te identificeren, patronen te analyseren en veel andere inzichten te bieden op basis van de Azure Cosmos DB-logboeken die u hebt verzameld.

- [Query's uitvoeren voor Azure Cosmos DB in Log Analytics-werk ruimten](monitor-cosmos-db.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: waarschuwingen inschakelen voor afwijkende activiteiten

**Hulp**: in azure Security Center kunt u geavanceerde bedreigingen beveiliging inschakelen voor Azure Cosmos DB om afwijkende activiteiten in beveiligings logboeken en gebeurtenissen te bewaken. Diagnostische instellingen in Azure Cosmos DB inschakelen en logboeken naar een Log Analytics werkruimte verzenden.

 

U kunt uw Log Analytics-werk ruimte ook vrijgeven aan Azure Sentinel, omdat deze een via-oplossing (Security Orchestration Automated Response) biedt. Hiermee kunnen playbooks (geautomatiseerde oplossingen) worden gemaakt en gebruikt om beveiligings problemen op te lossen. Daarnaast kunt u aangepaste logboek waarschuwingen maken in uw Log Analytics-werk ruimte met behulp van Azure Monitor.

- [Lijst met beveiligings waarschuwingen voor bedreigingen voor Azure Cosmos DB](../security-center/alerts-reference.md#alerts-azurecosmos)

- [Azure-Sentinel onboarden](../sentinel/quickstart-onboard.md)

- [Logboek waarschuwingen maken, weer geven en beheren met behulp van Azure Monitor](../azure-monitor/alerts/alerts-log.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="identity-and-access-control"></a>Identiteits- en toegangsbeheer

*Zie [Azure Security Bench Mark: Identity and Access Control](../security/benchmarks/security-control-identity-access-control.md)voor meer informatie.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: een inventaris van beheerders accounts onderhouden

**Richt lijnen**: u kunt het deel venster identiteits-en toegangs beheer (IAM) in de Azure Portal gebruiken om op rollen gebaseerd toegangs beheer (RBAC) te configureren en inventaris op Azure Cosmos DB resources te onderhouden. De rollen worden toegepast op gebruikers, groepen, service-principals en beheerde identiteiten in Azure Active Directory (Azure AD). U kunt ingebouwde rollen of aangepaste rollen gebruiken voor individuen en groepen.

Azure Cosmos DB biedt ingebouwde Azure RBAC voor algemene beheer scenario's in Azure Cosmos DB. Een persoon die een profiel in azure AD heeft, kan deze Azure-rollen toewijzen aan gebruikers, groepen, service-principals of beheerde identiteiten voor het verlenen of weigeren van toegang tot resources en bewerkingen op Azure Cosmos DB resources.

U kunt ook de Azure AD Power shell-module gebruiken om ad hoc query's uit te voeren om accounts te detecteren die lid zijn van beheer groepen.

Daarnaast kunnen sommige acties in Azure Cosmos DB worden beheerd met Azure AD en account-specifieke hoofd sleutels. Gebruik de account instelling ' disableKeyBasedMetadataWriteAccess ' om de toegang tot sleutels te beheren.

- [Informatie over op rollen gebaseerd toegangs beheer in Azure Cosmos DB](role-based-access-control.md)

- [Bouw uw eigen aangepaste rollen met behulp van Azure Cosmos DB acties (Microsoft.DocumentDB-naam ruimte)](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)

- [Een nieuwe rol maken in azure AD](../role-based-access-control/custom-roles.md)

- [Een directory-rol verkrijgen in azure AD met Power shell](/powershell/module/azuread/get-azureaddirectoryrole?preserve-view=true&view=azureadps-2.0)

- [Leden van een directory-rol in azure AD ophalen met Power shell](/powershell/module/azuread/get-azureaddirectoryrolemember?preserve-view=true&view=azureadps-2.0)

- [Gebruikerstoegang beperken tot alleen gegevensbewerkingen](how-to-restrict-user-data.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="32-change-default-passwords-where-applicable"></a>3,2: standaard wachtwoorden wijzigen indien van toepassing

**Hulp**: het concept van standaard of leeg wacht woord bestaat niet in relatie tot Azure Active Directory (Azure AD) of Azure Cosmos db. In plaats daarvan gebruikt Azure Cosmos DB twee soorten sleutels om gebruikers te verifiëren en toegang te bieden tot de gegevens en bronnen van de gebruiker. hoofd sleutels en bron tokens. De sleutels kunnen op elk gewenst moment opnieuw worden gegenereerd.

- [Beveiligde toegang tot gegevens in Azure Cosmos DB](secure-access-to-data.md)

- [Azure Cosmos DB sleutels opnieuw genereren](./manage-with-powershell.md#regenerate-keys)

- [Programmatisch toegang krijgen tot sleutels met Azure AD](certificate-based-authentication.md)

**Verantwoordelijkheid**: Gedeeld

**Azure Security Center bewaking**: geen

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: speciale beheerders accounts gebruiken

**Richt lijnen**: niet van toepassing; Azure Cosmos DB biedt geen ondersteuning voor beheerders accounts. Alle toegang is geïntegreerd met Azure Active Directory (Azure AD) en toegangs beheer op basis van rollen (Azure RBAC).

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: gebruik Azure Active Directory eenmalige aanmelding (SSO)

**Hulp**: Azure Cosmos DB gebruikt twee soorten sleutels om gebruikers te autoriseren en biedt geen ondersteuning voor eenmalige Sign-On (SSO) op het niveau van het gegevens vlak. Toegang tot het besturings vlak voor Cosmos DB is beschikbaar via REST API en ondersteunt SSO. Als u zich wilt verifiëren, stelt u de autorisatie-header voor uw aanvragen in op een JSON Web Token die u hebt verkregen van Azure Active Directory (Azure AD).

- [Meer informatie over Azure data base for Cosmos DB REST API](/rest/api/cosmos-db/)

- [Informatie over eenmalige aanmelding met Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: multi-factor Authentication gebruiken voor alle op Azure Active Directory gebaseerde toegang

**Hulp**: Schakel Azure Active Directory (Azure AD) multi-factor Authentication in en volg Azure Security Center aanbevelingen voor identiteits-en toegangs beheer.

- [Multi-factor Authentication inschakelen in azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Identiteit en toegang bewaken in Azure Security Center](../security-center/security-center-identity-access.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: gebruik speciale machines (privileged Access workstations) voor alle beheer taken

**Richt lijnen**: gebruik privileged Access workstations (Paw) met multi-factor Authentication die is geconfigureerd om Azure-resources aan te melden en te configureren. 

- [Meer informatie over privileged Access workstations](/security/compass/privileged-access-devices)
 
- [Multi-factor Authentication inschakelen in azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: Logboeken en waarschuwingen voor verdachte activiteiten van beheerders accounts

**Hulp**: gebruik Advanced Threat Protection (ATP) voor Azure Cosmos db. ATP voor Azure Cosmos DB biedt een extra beveiligingslaag waarmee ongebruikelijke en mogelijk schadelijke pogingen voor het openen of exploiteren van Azure Cosmos-accounts worden gedetecteerd. Met deze beveiligingslaag kunt u bedreigingen aanpakken en ze integreren met centrale beveiligings bewakings systemen.

Daarnaast kunt u Azure Active Directory (Azure AD) Privileged Identity Management (PIM) gebruiken voor het genereren van Logboeken en waarschuwingen wanneer verdachte of onveilige activiteiten in de omgeving worden uitgevoerd.

Gebruik Azure AD-risico detecties om waarschuwingen en rapporten weer te geven over Risk ante gebruikers gedrag.

- [Privileged Identity Management implementeren (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Meer informatie over Azure AD-risico detectie](../active-directory/identity-protection/overview-identity-protection.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: Azure-resources alleen beheren vanaf goedgekeurde locaties

**Hulp**: de locatie voorwaarde van een beleid voor voorwaardelijke toegang configureren en uw benoemde locaties beheren. Met benoemde locaties kunt u logische groeperingen van IP-adresbereiken of landen en regio's maken. U kunt de toegang tot gevoelige bronnen, zoals uw Azure Cosmos DB-instanties, beperken tot uw geconfigureerde benoemde locaties.

- [Benoemde locaties configureren in azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="39-use-azure-active-directory"></a>3,9: Azure Active Directory gebruiken

**Hulp**: gebruik Azure Active Directory (Azure AD) als centrale verificatie-en autorisatie systeem. Azure AD beveiligt gegevens door gebruik te maken van sterke versleuteling voor gegevens in rust en onderweg. Azure AD bevat ook zouten, hashes en veilige gebruikers referenties.

- [Een Azure AD-instantie maken en configureren](../active-directory-domain-services/tutorial-create-instance.md)

- [Azure AD-verificatie configureren en beheren met Azure SQL](../azure-sql/database/authentication-aad-configure.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: regel matig gebruikers toegang controleren en afstemmen

**Hulp**: Azure Active Directory (Azure AD) biedt logboeken waarmee u verlopen accounts kunt detecteren. Daarnaast kunt u Azure Identity Access revisies gebruiken om groepslid maatschappen en de toegang tot bedrijfs toepassingen en roltoewijzingen op efficiënte wijze te beheren. De toegang van de gebruiker kan regel matig worden gecontroleerd om ervoor te zorgen dat alleen de juiste gebruikers toegang hebben.

- [Beoordelingen over Azure Identity Access gebruiken](../active-directory/governance/access-reviews-overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: controle pogingen om toegang te krijgen tot gedeactiveerde referenties

**Richt lijnen**: u kunt Diagnostische instellingen maken voor gebruikers accounts voor Azure Active Directory (Azure AD), de audit logboeken en aanmeldings logboeken verzenden naar een log Analytics-werk ruimte waar u de gewenste waarschuwingen kunt configureren.

- [Azure-activiteitenlogboeken integreren in Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: waarschuwing voor de afwijking van het aanmeldings gedrag van het account

**Hulp**: gebruik Advanced Threat Protection (ATP) voor Azure Cosmos db. ATP voor Azure Cosmos DB biedt een extra beveiligingslaag waarmee ongebruikelijke en mogelijk schadelijke pogingen voor het openen of exploiteren van Azure Cosmos-accounts worden gedetecteerd. Met deze beveiligingslaag kunt u bedreigingen aanpakken en ze integreren met centrale beveiligings bewakings systemen.

U kunt ook de functie voor identiteits beveiliging en risico detectie van Azure Active Directory (Azure AD) gebruiken voor het configureren van automatische antwoorden op gedetecteerde verdachte acties die betrekking hebben op gebruikers identiteiten. Daarnaast kunt u Logboeken opnemen in azure Sentinel voor verder onderzoek.

- [Riskante Azure AD-aanmeldingen weergeven](../active-directory/identity-protection/overview-identity-protection.md)

- [Risico beleid voor identiteits beveiliging configureren en inschakelen](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Azure-Sentinel onboarden](../sentinel/quickstart-onboard.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="data-protection"></a>Gegevensbeveiliging

*Zie [Azure Security Benchmark: gegevensbescherming](../security/benchmarks/security-control-data-protection.md) voor meer informatie.*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: een inventaris van gevoelige informatie onderhouden

**Hulp**: Tags gebruiken bij het volgen van Azure Cosmos DB exemplaren die gevoelige informatie opslaan of verwerken.

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: systemen isoleren die gevoelige informatie opslaan of verwerken

**Richt lijnen**: afzonderlijke abonnementen en/of beheer groepen implementeren voor ontwikkeling, testen en productie. Azure Cosmos DB exemplaren worden van elkaar gescheiden door een virtueel netwerk/subnet, worden op de juiste wijze gelabeld en beveiligd in een netwerk beveiligings groep (NSG) of Azure Firewall. Azure Cosmos DB instanties waarin gevoelige gegevens worden opgeslagen, moeten worden geïsoleerd. Met behulp van een persoonlijke Azure-koppeling kunt u verbinding maken met een Azure Cosmos DB exemplaar account via een persoonlijk eind punt. Het persoonlijke eind punt is een reeks privé-IP-adressen in een subnet binnen het virtuele netwerk. Vervolgens kunt u de toegang tot de geselecteerde privé-IP-adressen beperken. 

- [Aanvullende Azure-abonnementen maken](../cost-management-billing/manage/create-subscription.md)

- [Beheer groepen maken](../governance/management-groups/create-management-group-portal.md)

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

- [Een persoonlijk eind punt configureren voor Azure Cosmos DB](how-to-configure-private-endpoints.md)

- [Een netwerk beveiligings groep maken met een beveiligings configuratie](../virtual-network/tutorial-filter-network-traffic.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: niet-geautoriseerde overdracht van gevoelige gegevens controleren en blok keren

**Richt lijnen**: u kunt geavanceerde dreigingen beveiligen voor Azure Cosmos DB, waarmee afwijkende activiteiten worden gedetecteerd die een ongebruikelijke en potentieel schadelijke pogingen tot toegang tot of exploiten van data bases zijn. De volgende waarschuwingen kunnen momenteel worden geactiveerd:

- Toegang vanaf ongebruikelijke locaties

- Ongebruikelijke gegevens extractie

Daarnaast kunt u, wanneer u virtuele machines gebruikt om toegang te krijgen tot uw Azure Cosmos DB-instanties, een persoonlijke koppeling, firewall, netwerk beveiligings groepen en service tags gebruiken om de kans op gegevens exfiltration te beperken. Micro soft beheert de onderliggende infra structuur voor Azure Cosmos DB en heeft strikte controles geïmplementeerd om verlies of bloot stelling van klant gegevens te voor komen.

- [Cosmos DB Advanced Threat Protection configureren](cosmos-db-advanced-threat-protection.md)

- [Informatie over beveiliging van klantgegevens in Azure](../security/fundamentals/protection-customer-data.md)

**Verantwoordelijkheid**: Gedeeld

**Azure Security Center bewaking**: geen

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: een actief detectie hulpprogramma gebruiken om gevoelige gegevens te identificeren

**Hulp**: de functies voor automatische gegevens identificatie, classificatie en verlies preventie zijn nog niet beschikbaar voor Azure Cosmos db. U kunt echter de integratie van Azure Cognitive Search gebruiken voor classificatie en gegevens analyse. U kunt ook een oplossing van derden implementeren als dat nodig is voor nalevings doeleinden.

Voor het onderliggende platform dat door micro soft wordt beheerd, behandelt micro soft alle inhoud van de klant als gevoelig en gaat u naar een fantastische lengte om te beschermen tegen verlies en bloot stelling van klant gegevens. Om ervoor te zorgen dat klant gegevens binnen Azure veilig blijven, heeft micro soft een reeks robuuste besturings elementen en mogelijkheden voor gegevens bescherming geïmplementeerd en onderhouden.

- [Azure Cosmos DB gegevens indexeren met Azure Cognitive Search](../search/search-howto-index-cosmosdb.md?bc=%2fazure%2fcosmos-db%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fcosmos-db%2ftoc.json)

- [Informatie over beveiliging van klantgegevens in Azure](../security/fundamentals/protection-customer-data.md)

**Verantwoordelijkheid**: Gedeeld

**Azure Security Center bewaking**: geen

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4,6: op rollen gebaseerd toegangs beheer gebruiken voor het beheren van de toegang tot bronnen

**Hulp**: Azure Cosmos DB biedt ingebouwde op rollen gebaseerd toegangs beheer voor Azure (Azure RBAC) voor algemene beheer scenario's in azure Cosmos db. Een persoon die een profiel in Azure Active Directory (Azure AD) heeft, kan deze Azure-rollen toewijzen aan gebruikers, groepen, service-principals of beheerde identiteiten voor het verlenen of weigeren van toegang tot resources en bewerkingen op Azure Cosmos DB-resources. Roltoewijzingen zijn alleen gericht op toegangs beheer, waaronder toegang tot Azure Cosmos-accounts, data bases, containers en aanbiedingen (door Voer).

- [Azure RBAC implementeren in Azure Cosmos DB](role-based-access-control.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: gevoelige informatie op rest versleutelen

**Hulp**: alle gebruikers gegevens die zijn opgeslagen in Cosmos DB, zijn standaard versleuteld. Er zijn geen besturings elementen om het uit te scha kelen. Azure Cosmos DB maakt gebruik van AES-256-versleuteling voor alle regio's waar het account wordt uitgevoerd.

Standaard beheert micro soft de sleutels die worden gebruikt voor het versleutelen van de gegevens in uw Azure Cosmos-account. U kunt ervoor kiezen om een tweede laag versleuteling toe te voegen met uw eigen sleutels.

- [Meer informatie over versleuteling in rust met Azure Cosmos DB](database-encryption-at-rest.md)

- [Meer informatie over sleutel beheer voor versleuteling in rust met Azure Cosmos DB]()

- [Door de klant beheerde sleutels voor uw Azure Cosmos DB-account configureren](how-to-setup-cmk.md)

**Verantwoordelijkheid**: Gedeeld

**Azure Security Center bewaking**: geen

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: wijzigingen in essentiële Azure-resources vastleggen en waarschuwen

**Hulp**: gebruik Azure monitor met het Azure-activiteiten logboek om waarschuwingen te maken wanneer wijzigingen worden aangebracht in productie-exemplaren van Azure Cosmos db.

- [Waarschuwingen maken voor gebeurtenissen in het Azure-activiteiten logboek](../azure-monitor/alerts/alerts-activity-log.md)

- [Waarschuwingen maken voor gebeurtenissen in het Azure-activiteiten logboek](../azure-monitor/alerts/alerts-activity-log.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="vulnerability-management"></a>Beheer van beveiligingsproblemen

*Zie voor meer informatie de [Azure Security Bench Mark: beveiligingslek beheer](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: automatische hulpprogram ma's voor het scannen van beveiligings problemen uitvoeren

**Hulp**: Volg de aanbevelingen van Azure Security Center voor uw Azure Cosmos DB-instanties. 

Micro soft voert systeem patches en beveiligings beheer uit op de onderliggende hosts die uw Azure Cosmos DB-instanties ondersteunen. Om ervoor te zorgen dat klant gegevens binnen Azure veilig blijven, heeft micro soft een reeks robuuste besturings elementen en mogelijkheden voor gegevens bescherming geïmplementeerd en onderhouden.

- [Ondersteunde functies zijn beschikbaar in Azure Security Center](../security-center/security-center-services.md?tabs=features-windows)

**Verantwoordelijkheid**: Gedeeld

**Azure Security Center bewaking**: geen

## <a name="inventory-and-asset-management"></a>Inventarisatie en Asset Management

*Zie [Azure Security Bench Mark: Inventory and Asset Management](../security/benchmarks/security-control-inventory-asset-management.md)voor meer informatie.*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: automatische Asset-detectie oplossing gebruiken

**Hulp**: gebruik de Resource grafiek Azure portal of Azure om alle resources te ontdekken (niet beperkt tot Azure Cosmos DB, maar ook resources zoals compute, andere opslag, netwerk, poorten en protocollen, enzovoort) binnen uw abonnement (en). Zorg ervoor dat u de juiste machtigingen hebt in uw Tenant en dat u alle Azure-abonnementen kunt inventariseren, evenals de resources in uw abonnementen.

Hoewel klassieke Azure-resources kunnen worden gedetecteerd via resource grafiek, is het raadzaam om Azure Resource Manager resources te maken en te gebruiken.

- [Query's maken met Azure resource Graph](../governance/resource-graph/first-query-portal.md)

- [Uw Azure-abonnementen weer geven](/powershell/module/az.accounts/get-azsubscription?preserve-view=true&view=azps-4.8.0)

- [Meer informatie over toegangs beheer op basis van rollen in azure](../role-based-access-control/overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="62-maintain-asset-metadata"></a>6,2: meta gegevens van activa onderhouden

**Richt lijnen**: Tags Toep assen op uw Azure Cosmos DB instanties en gerelateerde resources met meta gegevens om ze logisch in een taxonomie te organiseren.

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

- [Welke Azure Cosmos DB-bronnen Tags ondersteunen](../azure-resource-manager/management/tag-support.md#microsoftdocumentdb)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: niet-geautoriseerde Azure-resources verwijderen

**Richt lijnen**: Gebruik labels, beheer groepen en afzonderlijke abonnementen, waar nodig, om assets te organiseren en bij te houden, inclusief, maar niet beperkt tot Azure Cosmos DB resources. Sluit de inventaris regel matig af en zorg ervoor dat niet-geautoriseerde resources tijdig worden verwijderd uit het abonnement.

- [Aanvullende Azure-abonnementen maken](../cost-management-billing/manage/create-subscription.md)

- [Beheergroepen maken](../governance/management-groups/create-management-group-portal.md)

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: monitor voor niet-goedgekeurde Azure-resources

**Hulp: gebruik** Azure Policy om beperkingen toe te voegen voor het type resources dat kan worden gemaakt in klant abonnementen met behulp van de volgende ingebouwde beleids definities:

- Niet toegestane resourcetypen

- Toegestane brontypen

Daarnaast gebruikt u de resource grafiek van Azure om resources in de abonnementen op te vragen en te detecteren.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Query's maken met Azure Graph](../governance/resource-graph/first-query-portal.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="69-use-only-approved-azure-services"></a>6,9: alleen goedgekeurde Azure-Services gebruiken

**Hulp: gebruik** Azure Policy om beperkingen toe te voegen voor het type resources dat kan worden gemaakt in klant abonnement (en) met de volgende ingebouwde beleids definities:
- Niet toegestane resourcetypen
- Toegestane brontypen

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Een specifiek resource type weigeren met Azure Policy](../governance/policy/samples/built-in-policies.md#general)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: de mogelijkheid van gebruikers om te communiceren met Azure Resource Manager beperken

**Richt lijnen**: gebruik de voorwaardelijke toegang van Azure om gebruikers de mogelijkheid te bieden om te communiceren met Azure Resource Manager door ' blok toegang ' te configureren voor de app Microsoft Azure management. Dit kan ertoe leiden dat het maken en wijzigen van bronnen binnen een omgeving met hoge beveiliging wordt voor komen.

- [Voorwaardelijke toegang configureren om de toegang tot Azure Resource Manager te blok keren](../role-based-access-control/conditional-access-azure-management.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="secure-configuration"></a>Veilige configuratie

*Zie [Azure Security Bench Mark: Secure Configuration](../security/benchmarks/security-control-secure-configuration.md)(Engelstalig) voor meer informatie.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: veilige configuraties instellen voor alle Azure-resources

**Richt lijnen**: standaard beveiligings configuraties voor uw Cosmos DB-instanties definiëren en implementeren met Azure Policy. Gebruik Azure Policy aliassen in de **Microsoft.DocumentDB** naam ruimte om aangepaste beleids regels te maken om de configuratie van uw Cosmos DB exemplaren te controleren of af te dwingen. U kunt ook gebruik maken van ingebouwde beleids definities voor Azure Cosmos DB, zoals:
- Advanced Threat Protection implementeren voor Cosmos DB-accounts
- Cosmos DB moet gebruikmaken van een service-eindpunt voor een virtueel netwerk

- [Beschik bare Azure Policy aliassen weer geven](/powershell/module/az.resources/get-azpolicyalias?preserve-view=true&view=azps-4.8.0)

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: Beveilig Azure-resource configuraties onderhouden

**Hulp**: gebruik Azure Policy [deny] en [implementeren indien niet aanwezig] voor het afdwingen van beveiligde instellingen voor uw Azure-resources.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy effecten begrijpen](../governance/policy/concepts/effects.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: de configuratie van Azure-resources veilig opslaan

**Richt lijnen**: als u aangepaste Azure Policy definities gebruikt voor uw Cosmos DB of gerelateerde resources, gebruikt u Azure opslag plaatsen om uw aangepaste beleids definities als code op te slaan en te beheren.

- [Beleid ontwerpen als codewerkstromen](../governance/policy/concepts/policy-as-code.md)

- [Documentatie voor Azure opslag plaatsen](/azure/devops/repos/git/gitworkflow)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: hulpprogram ma's voor configuratie beheer voor Azure-resources implementeren

**Hulp**: gebruik Azure Policy aliassen in de naam ruimte ' Microsoft.DocumentDB ' om aangepaste beleids regels te maken om systeem configuraties te Signa lering, te controleren en af te dwingen. Ontwikkel bovendien een proces en pijp lijn voor het beheren van beleids uitzonderingen.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: geautomatiseerde configuratie bewaking voor Azure-resources implementeren

**Hulp**: gebruik Azure Policy aliassen in de naam ruimte ' Microsoft.DocumentDB ' om aangepaste beleids regels te maken om systeem configuraties te Signa lering, te controleren en af te dwingen. Gebruik Azure Policy [audit], [deny] en [implementeren indien niet aanwezig] om automatisch configuraties af te dwingen voor uw Azure Cosmos DB instanties en gerelateerde bronnen. 

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="711-manage-azure-secrets-securely"></a>7,11: Azure-geheimen veilig beheren

**Richt lijnen**: voor virtuele Azure-machines of webtoepassingen die worden uitgevoerd op Azure app service worden gebruikt voor toegang tot uw Azure Cosmos DB-instanties, gebruikt u Managed Service Identity in combi natie met Azure Key Vault om het beheer van het geheim te vereenvoudigen en te Azure Cosmos DB beveiligen. Zorg ervoor Key Vault zacht verwijderen is ingeschakeld.

- [Integratie met door Azure beheerde identiteiten](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Een Key Vault maken](../key-vault/secrets/quick-create-portal.md)

- [Verifiëren bij Key Vault](../key-vault/general/authentication.md)

- [Toegangs beleid voor Key Vault toewijzen](../key-vault/general/assign-access-policy-portal.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: identiteiten veilig en automatisch beheren

**Richt lijnen**: voor virtuele Azure-machines of webtoepassingen die worden uitgevoerd op Azure app service worden gebruikt voor toegang tot uw Azure Cosmos DB-instanties, gebruikt u Managed Service Identity in combi natie met Azure Key Vault om het beheer van het geheim te vereenvoudigen en te Azure Cosmos DB beveiligen.

Gebruik beheerde identiteiten om Azure-Services te voorzien van een automatisch beheerde identiteit in Azure Active Directory (Azure AD). Met beheerde identiteiten kunt u zich verifiëren bij elke service die ondersteuning biedt voor Azure AD-verificatie, met inbegrip van Key Vault, zonder enige referenties in uw code.

- [Beheerde identiteiten configureren](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

- [Integratie met door Azure beheerde identiteiten](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: onbedoelde referentie blootstelling elimineren

**Richt lijnen**: referentie scanner implementeren om referenties in code te identificeren. Door het gebruik van Credential Scanner worden gebruikers ook aangemoedigd om gedetecteerde referenties naar veiligere locaties, zoals Azure Key Vault, te verplaatsen.

- [Referentie scanner instellen](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="malware-defense"></a>Beveiliging tegen malware

*Zie voor meer informatie de [Azure Security Bench Mark: beveiliging tegen schadelijke software](../security/benchmarks/security-control-malware-defense.md).*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: scan bestanden die moeten worden geüpload naar niet-reken resources van Azure

**Hulp**: micro soft antimalware is ingeschakeld op de onderliggende host die ondersteuning biedt voor Azure-Services (bijvoorbeeld Azure CosmosDB), maar wordt niet uitgevoerd op de inhoud van de klant.

Het is uw verantwoordelijkheid om vooraf bestanden te scannen die worden geüpload naar niet-reken resources van Azure, met inbegrip van Azure Cosmos DB. Micro soft heeft geen toegang tot klant gegevens en kan daarom geen anti-malware scans uitvoeren van klant inhoud namens u.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="data-recovery"></a>Gegevensherstel

*Zie [Azure Security Bench Mark: Data Recovery](../security/benchmarks/security-control-data-recovery.md)(Engelstalig) voor meer informatie.*

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: volledige back-ups van het systeem uitvoeren en een back-up maken van door de klant beheerde sleutels

**Hulp**: met Azure Cosmos DB maakt u regel matig back-ups van uw gegevens. Als de data base of container is verwijderd, kunt u een ondersteunings ticket indienen of ondersteuning voor Azure aanroepen om de gegevens van automatische online back-ups te herstellen. Ondersteuning voor Azure is alleen beschikbaar voor geselecteerde abonnementen, zoals Standard, Developer en abonnementen hoger. Als u een specifieke moment opname van de back-up wilt herstellen, moet Azure Cosmos DB de gegevens beschikbaar hebben voor de duur van de back-upcyclus voor die moment opname. 

Als Key Vault gebruiken om referenties voor uw Cosmos DB-instanties op te slaan, moet u regel matig automatische back-ups van uw sleutels maken.

- [Meer informatie over Azure Cosmos DB automatische back-ups](online-backup-and-restore.md)

- [Gegevens herstellen in Azure Cosmos DB](./online-backup-and-restore.md)

- [Back-ups maken van Key Vault sleutels](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

**Verantwoordelijkheid**: Gedeeld

**Azure Security Center bewaking**: geen

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: alle back-ups valideren, inclusief door de klant beheerde sleutels

**Richt lijnen**: als u de data base of container verwijdert, kunt u een ondersteunings ticket indienen of ondersteuning voor Azure aanroepen om de gegevens van automatische online back-ups te herstellen. Ondersteuning voor Azure is alleen beschikbaar voor geselecteerde abonnementen, zoals Standard, Developer en abonnementen hoger. Als u een specifieke moment opname van de back-up wilt herstellen, moet Azure Cosmos DB de gegevens beschikbaar hebben voor de duur van de back-upcyclus voor die moment opname.

Test het herstellen van uw geheimen die zijn opgeslagen in Azure Key Vault met behulp van Power shell. Met de cmdlet Restore-AzureKeyVaultKey maakt u een sleutel in de opgegeven sleutel kluis. Deze sleutel is een replica van de back-upsleutel in het invoer bestand en heeft dezelfde naam als de oorspronkelijke sleutel.

- [Meer informatie over Azure Cosmos DB automatische back-ups](online-backup-and-restore.md)

- [Gegevens herstellen in Azure Cosmos DB](./online-backup-and-restore.md)

- [Azure Key Vault geheimen herstellen](/powershell/module/az.keyvault/restore-azkeyvaultkey?preserve-view=true&view=azps-4.8.0)

**Verantwoordelijkheid**: Gedeeld

**Azure Security Center bewaking**: geen

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: zorg voor de bescherming van back-ups en door de klant beheerde sleutels

**Richt lijnen**: omdat alle gebruikers gegevens die zijn opgeslagen in Cosmos DB, worden versleuteld op rest en in Trans Port, hoeft u geen actie te ondernemen. Een andere manier om dit te doen is dat versleuteling op rest standaard is. Er zijn geen besturings elementen om deze in of uit te scha kelen. Azure Cosmos DB maakt gebruik van AES-256-versleuteling voor alle regio's waar het account wordt uitgevoerd.

Schakel Soft-Delete in Key Vault in om sleutels te beschermen tegen onbedoelde of schadelijke verwijdering.

- [Gegevens versleuteling in Azure Cosmos DB begrijpen](database-encryption-at-rest.md)

- [Soft-Delete in Key Vault inschakelen](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Verantwoordelijkheid**: Gedeeld

**Azure Security Center bewaking**: geen

## <a name="incident-response"></a>Reageren op incidenten

*Zie [Azure Security Benchmark: respons op incidenten](../security/benchmarks/security-control-incident-response.md) voor meer informatie.*

### <a name="101-create-an-incident-response-guide"></a>10,1: een hand leiding voor reactie op incidenten maken

**Richtlijnen**: Stel voor uw organisatie een responshandleiding op voor gebruik bij incidenten. Zorg ervoor dat er schriftelijke responsplannen zijn waarin alle rollen van het personeel worden gedefinieerd, evenals alle fasen in het afhandelen/managen van incidenten, vanaf de detectie van het incident tot een evaluatie ervan achteraf.

- [U kunt ook gebruikmaken van de hand leiding voor de verwerking van het computer beveiligings incident van het NIST om u te helpen bij het maken van uw eigen reactie plan voor incidenten](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

- [Werk stroom automatisering configureren in Azure Security Center](../security-center/security-center-planning-and-operations-guide.md)

- [Richt lijnen voor het bouwen van uw eigen beveiligings incident antwoord proces](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Micro soft Security Response Center anatomie van een incident](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: een beoordelings procedure voor incidenten en prioriteits procedures maken

**Hulp**: Security Center wijst aan elke waarschuwing een Ernst toe om u te helpen bepalen welke waarschuwingen het eerst moeten worden onderzocht. De ernst is gebaseerd op de manier waarop de Security Center zich in de zoek actie bevindt of de analyse die wordt gebruikt om de waarschuwing te geven, evenals het betrouwbaarheids niveau waarvan er sprake is van schadelijke opzet achter de activiteit die tot de waarschuwing heeft geleid.

Daarnaast kunt u ook duidelijk abonnementen markeren (voor bijvoorbeeld productie, niet-productie) en maak een naamgevings systeem om Azure-resources duidelijk te identificeren en te categoriseren.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="103-test-security-response-procedures"></a>10,3: procedures voor beveiligings antwoorden testen

**Richt lijnen**: oefent oefeningen uit om de respons mogelijkheden van uw systeem op een gewone uitgebracht te testen. Stel vast waar zich zwakke plekken en hiaten bevinden, en wijzig zo nodig het plan.

- [Raadpleeg de publicatie van het NIST: hand leiding voor het testen, trainen en uitoefenen van Program Ma's voor IT-plannen en-mogelijkheden](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: contact gegevens van het beveiligings incident opgeven en waarschuwings meldingen configureren voor beveiligings incidenten

**Hulp**: contact gegevens van beveiligings incidenten worden door micro soft gebruikt om contact met u op te nemen als het micro soft Security Response Center (MSRC) detecteert dat de gegevens van de klant zijn geopend door een onrecht matige of niet-gemachtigde partij.  Bekijk incidenten na het feit om te controleren of de problemen zijn opgelost.

- [De Azure Security Center Security-contact persoon instellen](../security-center/security-center-provide-security-contact-details.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: beveiligings waarschuwingen opnemen in uw reactie systeem van uw incident

**Richt lijnen**: uw Azure Security Center waarschuwingen en aanbevelingen exporteren met de functie continue export. Met doorlopend exporteren kunt u waarschuwingen en aanbevelingen hand matig of op een doorlopende manier exporteren. U kunt de Azure Security Center Data Connector gebruiken om de waarschuwingen naar Azure Sentinel te streamen.

- [Continue export configureren](../security-center/continuous-export.md)

- [Waarschuwingen streamen naar Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: de reactie op beveiligings waarschuwingen automatiseren

**Hulp**: gebruik de functie werk stroom automatisering in azure Security Center om automatisch reacties te activeren via ' Logic apps ' in beveiligings waarschuwingen en aanbevelingen.

- [Werk stroom automatisering en Logic Apps configureren](../security-center/workflow-automation.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="penetration-tests-and-red-team-exercises"></a>Penetratietests en Red Team-oefeningen

*Zie [Azure Security Bench Mark: Indringings tests en rode team oefeningen](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)voor meer informatie.*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: voert regel matig indringings tests van uw Azure-resources uit en zorgt voor herbemiddeling van alle essentiële beveiligings resultaten

**Richt lijnen**: Volg de stappen voor het testen van de Microsoft Cloud indringings regels om ervoor te zorgen dat uw indringings tests niet worden geschonden door het micro soft-beleid. Gebruik de strategie van Microsoft en de uitvoering van Red Teaming-activiteiten, en voer een penetratietest van de live site uit op basis van een infrastructuur, services en toepassingen die door Microsoft worden beheerd. 

- [Regels voor het inzetten van penetratietests](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Verantwoordelijkheid**: Gedeeld

**Azure Security Center bewaking**: geen

## <a name="next-steps"></a>Volgende stappen

- Zie [Overzicht Azure Security Benchmark V2](../security/benchmarks/overview.md)
- Meer informatie over [Azure-beveiligingsbasislijnen](../security/benchmarks/security-baselines-overview.md)
